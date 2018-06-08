---
title: "Building an intelligent Twitter bot - part 1"
date: 2018-06-08
tags: ["python", "deeplearning", "twitter", "tensorflow"]
---

## Step 1. Defining the problem

Hi everybody, I've decided to write some ongoing series about a project I'm currently working on. At university, I'm currently attending a class on
deep learning and we are supposed to come up with an exciting project using deep learning techniques. Since I spent to much time on Twitter anyway,
I decided to incorporate this into my coursework and analyze some tweets.

As it turns out, this is definitely not a new idea (go figure). Probably due to the fact that Twitter offers a huge stream of new data everyday, it
seems to be a very popular target for research in the NLP community. One interesting thing I found is that there is ongoing research on the topic of
depression and related psychological diseases. Since I wanted the project to actually be useful (or at least I wanted to pretend to be building 
something useful), I decided to build a self-care bot for Twitter which monitors your timeline and sends helpful or encouraging comments should recent
tweets show more sign of depression or general unwell being.

A quick disclaimer: I know that health-care is something where you have to be pretty careful, since well meaning, but misinformed activism can quickly do
more harm than good. I will definitely not deploy a production ready version of the project for public use unless I've spoken with professional counselors
or other people with more experience then me. For now, it is more about the research question whether it is actually possible to assess the general
well-being of a person via their timeline. My guess is that it is somewhat possible, depending on how much they tweet and how open they are with their emotions.
A second note which I want to add is that I will of course not bother random people because their timeline might show signs of depression. Everything that
I run will be strictly consentual.

## Step 2. Getting the data

<figure align="left" style="padding: 1em; float:left; width: 300px">
	<img alt="A smiling, friendly face"
		src="/img/twitter_app_overview.jpg">
	<figcaption><i>Managing Twitter apps</i></figcaption>
</figure>

Every machine learning task is only as good as it's data, and as I found out last year, once you work with data that goes beyond preprocessed Kaggle tasks,
you quickly run into a whole lot of problems. For our taks, since it is a deep learning approach, the first problem is actually getting useful, tagged data.
Luckily, some quick searches on Google scholar gave me a lead that there are tagged datasets specifically tailored towards depression. These have been compiled
by different researchers in the last few years. I attached the papers below for people who are interested.

I wrote one of the lead researchers, Professor Inkpen from Ottawa, and she was kind enough to provide me with her data after I signed an agreement forbidding
me to find out and disclose the names of the participants from whom the original data was gathered. I believe that an ethical approach to research, especially
in a field which deals with complex questions like mental health is really important, so, in this case, I am happy to comply.

Professor Inkpen supplied me with a .csv file which contained tags and tweet id's. Since Twitter has a pretty usable API, I thought that it would not
be a big problem to get the data directly. I installed a Python library [Tweepy](http://www.tweepy.org/) to access the API and was ready to begin.

The first thing you need to do if you want to talk to the Twitter API is to register an application. You can do this at https://apps.twitter.com/.

Now, if you are a CS noob like me, getting a script to connect to Twitter using this API is not completely trivial, since you need to understand what the
different keys you will get mean. Twitter uses a concept called [OAuth](https://oauth.net/). This is a way to allow third-party applications to interact 
with content which requires a log-in by a user. Basically, every Twitter app needs to be verified twice. The app gets a set of tokens, called consumer key
and secrete which allows a programm, for example, to send commands to the application. But it also uses a second pair of keys, called access token and
secrete, to allow the app to use a users Twitter account. I authorized my account with the app, so it could now post and like Tweets, follow or unfollow
other useres, everything that I can also do myself. If I want my programm to access Twitter now, I can authorize my programm with the app and the use my
access token and key to directly manipulate Twitter using my account.

Since we are only interested in mining Tweets right now, it is not really important which account we verify with the app. Once the project is at a stage
where we want to post to Twitter, we will need to register a new account for the bot and generate access tokens for this account.

Mining Tweets with a specific ID is now a pretty easy task. Tweepy supplies well documented functions to query the API, so we can use those:

```python
def get_api_token():
    '''
    Reads the secrets file for api tokens
    '''
    with open('data/secrets.txt', 'r') as f:
        # [:-1] strips newline from end
        consumer_key = f.readline()[:-1]
        consumer_secret = f.readline()[:-1]
        access_token = f.readline()[:-1]
        access_secret = f.readline()[:-1]
    return consumer_key, consumer_secret, access_token, access_secret


def authenticate():
    '''
    Loads auth tokens and performs the api authorization for twitter
    '''
    key, secret, access_token, access_secret = get_api_token()
    auth = OAuthHandler(key, secret)
    auth.set_access_token(access_token, access_secret)
    rate_limit_wait = False
    api = tweepy.API(auth, wait_on_rate_limit=rate_limit_wait, wait_on_rate_limit_notify=rate_limit_wait)
    return api

```
This is the code to authenticate our programm. It returns an tweepy.API object which provides us with a couple of wrapper functions for the API.
When I naively started querying the API, I noticed two things. Several Tweets from my original dataset where not available anymore, probably
because they were deleted since the dataset was created. But more worryingly, after some time, all querys returned nothing. It turns out that 
Twitter has a rate limiting. The code below waits for the rate limit to drop before continuing. Gathering 20,000 Tweets took my server a couple of
hours, so I ran it overnight.

```python
def download_data(tweet_list, output_location):
    '''
    The function loads the original research data and crawls twitter for the associated
    tweetts and users. The enhanced dataset is stored as "tweet_level_data.json" in the
    datasets folder and available for preprocessing and modelling. The tweet data is
    joined with the annotation label, 0 denoting no anotator found signs of depression,
    1 denoting 1 annotator who found signs of depression and 2 denoting a clearly
    depressive tweet.

    The data is saved in "dataset/full_tweets.pickle"
    '''
    # gather tweet id's from dataset
    api = authenticate()
    
    full_tweets = []
    len_list = len(tweet_list)
    start_time = time.time()
    # try to download the actual tweets from twitter
    i = 0
    while i < len_list:
        tweet = tweet_list[i]
        if i > 0 and i%50 ==0:
            print('\rDownloading {} %, Time passed: {} s, Tweets loaded: {}'.format(
                round(i*100/len_list, 3), 
                round(time.time() - start_time), 
                i), end="", flush=True)
        try:
            # append tweet to list of all tweets
            full_tweets.append({'object': api.get_status(id=tweet['id']), 'annotation': tweet})
            i += 1
        except RateLimitError as e:
            reset = int(e.response.headers['x-rate-limit-reset'])
            wait = int(reset-time.time())
            print('\rDownload at {}% | Rate limit hit, waiting {} s'.format(round(i*100/len_list,3), wait), end="", flush=True)
            pickle.dump(full_tweets, open(output_location + '_{}.pickle'.format(i), 'wb'))
            time.sleep(1)
            continue
        except TweepError as e:
            i += 1
    
    print('Finished')
    
    # saves the data to the pickled file
    pickle.dump(full_tweets, open(output_location, 'wb'))

```
In the next issue, I will write about preprocessing this dataset for actual machine learning.

## Papers and interesting links

* [Identifying Depression on Twitter](https://arxiv.org/abs/1607.07384)
* [Monitoring Tweets for Depression to Detect At-risk Users](http://aclweb.org/anthology/W/W17/W17-3104.pdf)
* [Tweepy - Twitter API in Python](http://www.tweepy.org/)
* [OAuth](https://oauth.net/)
* [Twitter Apps](https://apps.twitter.com/)
