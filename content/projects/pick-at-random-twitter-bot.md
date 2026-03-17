---
title: "@PickAtRandom Twitter Bot"
description: A Twitter bot that automates random selection of tweeps for giveaways
date: 2022-04-02T03:13:42+01:00
author: "Orim Dominic Adah"
draft: false
showToc: true
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags: ["twitter bot", "project"]
cover:
  image: "../projects/images/par-twitter-header-image.png"
  alt: "PickAtRandom Twitter bot"
  caption: "Pick random Twitter users with ease"
  relative: false
---

A Twitter user may decide to run a giveaway for a random selection of Twitter users who interacted with a tweet of theirs. [@mykeels](https://twitter.com/mykeels) saw a possibility to automate this and created a [Random-Twitter-Bot specification](https://gist.github.com/mykeels/2c1a04301c7ef9cb54696529565c3b10). Twitter users should not have to go through the stress of perusing their tweet interactors to select winners of a random giveaway. He suggested the idea to me and I built it.

In this article, I share my experience building [@PickAtRandom](https://twitter.com/PickAtRandom) for anyone desiring to build something similar.

## Getting a Twitter Developer Account

I figured that I would need access to Twitter APIs. I applied for access after reading and following the guidelines.

My application was rejected at first and I thought that it was final. I told a friend of mine who had gotten his application approved earlier. He advised me to edit my application mail and respond. I did this over and over again, making sure to refer to parts of the guidelines in my response mail. I also stated that I was going to abide by it. It was approved later.

I feel that responding to the rejection mails as fast as possible played a part in the approval.

## How to build it

After getting the approval, the new challenge became _how to build it_. I had no idea. I had never built a bot before.

I studied the Twitter API documentation for endpoints that would be useful. After developing an algorithm for how I thought that it would work, I asked myself _what Twitter bot does something similar?_. A few days later, I found [@remindme_ofthis](https://twitter.com/remindme_ofthis). Appreciation to [@theshalvah](https://twitter.com/theshalvah) for making the code open source.

I studied the codebase and found tools and packages that I could use. @theshalvah's algorithm was like mine, but his was more experienced.

## Building it

It was a jolly ride. My major challenges were understanding Twitter's API documentation, terms and terminologies around using the developer account, finding a free Redis database that could manage what @PickAtRandom was offering (and most importantly), getting @PickAtRandom's first user 😄. At various points, I had to contribute to open-source Twitter libraries so that I could have a feature in it work well enough for me to use.

After watching a talk by Scott Moss on his experience in building his first apps, I now realise that it was a waste of time trying to make test coverage close to 100% when the bot wasn't fully functional yet. The better thing to have done was to structure the code in such a way that it would be easier to test later.

## How it works

A serverless function with an API endpoint listens to events on the @PickAtRandom Twitter account. It filters in data that is necessary for @PickAtRandom to function.

Twitter users mention @PickAtRandom under their tweet, requesting that the bot makes a random selection for them at a particular time. The bot saves the tweet id and other information in a Redis database. I chose Redis because of speed and the type of data to be stored.

Another serverless function queries the database every minute to retrieve stored tweets to be responded to for that minute. If any is found, it pulls information required for the selection using Twitter APIs and makes selections from the result.

The bot compiles a response text from the result and responds to the requester \0/.

## Possible Improvements

After @PickAtRandom became functional, I started to see more complex use cases. People wanted to make sure that whoever was selected was a follower and had also interacted in more than one way; for example liked and also retweeted. Some wanted to select people whose retweets or comments had the highest or lowest likes.

While these are valid, @PickAtRandom wasn't built to solve these types of problems. The bot runs on free services and tries to manage resources. These scenarios are awesome ideas for Twitter bots.

Lastly, Twitter responds with only about 99 user handles for requests to get a list of users that liked a tweet or retweeted it. This is because @PickAtRandom is on a free plan. In the light of this, it is better to use @PickAtRandom for selections that would expect less than 99 tweet interactors for an interaction category. This can be achieved using short time frames for new tweets. I hope to upgrade the plan when I have the funds for it.

## Conclusion

I have to thank @mykeels here. He was supportive, watching out for tweets from @PickAtRandom and being accessible for questions and feeback. [@bolaji\_](https://twitter.com/bolaji_), the creator of [@anniemusicapp](https://mobile.twitter.com/anniemusicapp) was also cheering me on. They got @PickAtRandom its first set of users. Awesome folks!

 <!-- Things to add: - Other options (follow, like, retweet); 99, highest/lowest number of like tweets/retweets or comments; prevent non owner of tweet from using @pickatrandom, use the other product -->
