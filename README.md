**What does this do?**

This service automatically analyzes the content of a document or piece of text
and reports the interests present in the article. A topic is a non-hierarchical,
single-phrase summary of the thematic content of a piece of text; examples
include *Functional Programming*, *Celebrity Gossip*, or *Flowers*. At
Prismatic, we’ve been using interests to automatically analyze the content of text
in order to help connect people with the content they find interesting. Our
topic-tagging infrastructure can automatically analyze a piece of text and
determine which interests it is about.

**How do I use the service?**

Step 1: Acquire an access token

Head over to [http://topic.getprismatic.com](http://topic.getprismatic.com),
enter your email address, and some additional info about how you plan to use
the service, and we will email you an API access token.

Step 2: Make a query

Once you have your access token, you can try tagging a URL or piece of text via
our web interface. The same
[http://topic.getprismatic.com](http://topic.getprismatic.com) has input fields
where you can enter the query URL or text. 

You can also make requests programmatically. For example, if we want to run the
tagging service on the [Wikipedia article about Machine
Learning](http://en.wikipedia.org/wiki/Machine_learning), we can curl the
service:

```  
curl -H "X-API-TOKEN: <API-TOKEN>" http://topic.getprismatic.com/doc/topic?url=http%3A%2F%2Fen.wikipedia.org%2Fwiki%2FMachine_learning
```

where the `<API-TOKEN>` is a stand-in for the access token string.

Step 3: Interpret the response

The response comes in the form of a JSON map, with a key `topics` that has a
list of topic tags. Each topic tag has a numeric `id` of the topic in the
system, a human-readable topic name `topic`, and a `score`. The score is a real
value between 0 and 1, and represents the degree to which a significant part of
article is about the corresponding topic.

**I think the system made a mistake, where can I report it?**

Our approach to topic modeling is inherently data-driven, and as with all
data-driven models, it is subject to some noise. It is impossible to have 100%
precision and recall on all queries. There are some articles that might be
mis-tagged with incorrect interests, and some articles whose content reflects a
particular topic that our models fail to detect. On the whole, these models do
a good job, but errors are inevitable. We will record all reported errors in
order to feed them back into our training pipeline to ensure it improves over
time. To report an error, visit our [Topic Classification Error Reporting
Page](http://goo.gl/forms/CU0n34fQ7c).

**Do you have the topic I care about?**

We have over 5k modeled interests, and while we try to model the most popular
interests that are applicable over a wide range of applications, we do not
currently model everything. To check whether your topic is currently modeled,
visit our [Topic Search
Page](http://topic.getprismatic.com/topics/search/human).

**You don’t currently model my interest. Where can I submit a request for you to model a new interest?**

Currently, the set of interests is fixed. Given our resources, we are limited in
how many interests we can reliably model. While we do plan to expand the set of
modeled interests, we will prioritize which interests we add based on aggregate
demand. If you would like to submit a request to model new topic, please visit
our [Interest Submission Page](http://goo.gl/forms/8ryfTk8I6Z).

