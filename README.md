
# Prismatic Interest Graph API

Table of Contents
=================

  * [Prismatic Interest Graph API](#prismatic-interest-graph-api)
    * [What does this do?](#what-does-this-do)
    * [Is the service still in ALPHA?](#is-the-service-still-in-alpha)
    * [How do I use the service?](#how-do-i-use-the-service)
      * [Step 1: Acquire an access token](#step-1-acquire-an-access-token)
      * [Step 2: Make a query](#step-2-make-a-query)
      * [Step 3: Interpret the response](#step-3-interpret-the-response)
    * [What are the API endpoints?](#what-are-the-api-endpoints)
      * [Tag URL with Interests](#tag-url-with-interests)
        * [Input (request JSON body)](#input-request-json-body)
        * [Response](#response)
      * [Tag Text with Interests](#tag-text-with-interests)
        * [Input (request JSON body)](#input-request-json-body-1)
        * [Response](#response-1)
      * [Search for an Interest](#search-for-an-interest)
        * [Parameters](#parameters)
        * [Response](#response-2)
    * [I think the system made a mistake, where can I report it?](#i-think-the-system-made-a-mistake-where-can-i-report-it)
    * [Do you have the topic I care about?](#do-you-have-the-topic-i-care-about)
    * [You don’t currently model my interest. Where can I submit a request for you to model a new interest?](#you-dont-currently-model-my-interest-where-can-i-submit-a-request-for-you-to-model-a-new-interest)



##What does this do?

This service automatically analyzes the content of a document or piece of text
and reports the interests present in the article. An interest is a
non-hierarchical, single-phrase summary of the thematic content of a piece of
text; examples include *Functional Programming*, *Celebrity Gossip*, or
*Flowers*. At Prismatic, we’ve been using interests to automatically analyze
the content of text in order to help connect people with the content they find
interesting. Our interest graph can automatically analyze a piece of text and
determine which interests it is about.

##Is the service still in ALPHA?

Yes. This service is still in ALPHA, access and interface is subject to change at any time.

##How do I use the service?

### Step 1: Acquire an access token

Head over to [http://interest-graph.getprismatic.com](http://interest-graph.getprismatic.com),
enter your email address, and some additional info about how you plan to use
the service, and we will email you an API access token.

### Step 2: Make a query

Once you have your access token, you can try tagging a URL or piece of text via
our web interface. The same
[http://interest-graph.getprismatic.com](http://interest-graph.getprismatic.com) has input fields
where you can enter the query URL or text. 

You can also make requests programmatically. For example, if we want to run the
tagging service on the [Wikipedia article about Machine
Learning](http://en.wikipedia.org/wiki/Machine_learning), we can curl the
service:



```  
curl -H "X-API-TOKEN: <API-TOKEN>" 'http://interest-graph.getprismatic.com/url/topic' --data 'url=http%3A%2F%2Fen.wikipedia.org%2Fwiki%2FMachine_learning'
```

where the `<API-TOKEN>` is a stand-in for the access token string.

### Step 3: Interpret the response

The response comes in the form of a JSON map, with a key `topics` that has a
list of topic tags. Each topic tag has a numeric `id` of the topic in the
system, a human-readable topic name `topic`, and a `score`. The score is a real
value between 0 and 1, and represents the degree to which a significant part of
article is about the corresponding topic.

As a [Schema](https://github.com/Prismatic/schema): 

```clojure
{:topics [{:id long
           :topic String
           :score Num}]}
```

##What are the API endpoints?

We have a number of endpoints that you can access programmatically with an API
access token.  Passing the token is done in the `X-API-TOKEN` header. If for
some reason you have trouble passing headers, you can alternatively pass it in
a query parameter `?api-token=<API-TOKEN>`. Omitting the token from both the
query parameter and header will result in a `401` status code from the server.

While we are still in ALPHA, we rate limit requests to at most 20 calls per minute.

### Tag URL with Interests

    POST /url/topic

#### Input (request JSON body)

Name | Type | Description
-----|------|--------------
`url`|`string` | URL to tag with interests.

#### Response

A JSON map with a `topics` key that has a list of topics, each with an `id`, `topic` name, and `score`.

As a [Schema](https://github.com/Prismatic/schema): 
```clojure
{:topics [{:id long
           :topic String
           :score Num}]}
```


While in most cases we provide automatic tagging of interests based only on the
URL, we respect individual publisher preferences to avoid being crawled by
automated systems -- and therefore we may fail to extract text from a URL.
When this happens, the server will return a `400` status code, with the message
"Bad response from remote server," or a `500` status saying "Unexpected error
fetching document." In such situations, you can extract the text from the page
yourself using the handler below.

Sometimes we can fetch the content on a page, but there is not enough text to
determine what interests the page is about.  When this happens, the server will
return a `500` status code, with a message like "Not enough extracted text to
classify topics."


### Tag Text with Interests

    POST /text/topic

#### Input (request JSON body)

Name | Type | Description
-----|------|--------------
`title`|`string` | The title of the piece of text to tag. Providing a relevant title will often result in higher quality tags.
`body`|`string` | The body of the text to tag. Must be at least 140 characters.

#### Response

A JSON map with a `topics` key that has a list of topics, each with an `id`, `topic` name, and `score`.

As a [Schema](https://github.com/Prismatic/schema): 
```clojure
{:topics [{:id long
           :topic String
           :score Num}]}
```

If fewer than 140 characters are passed in the body, then the server will
return a `400` status code with a message describing the failure.

### Search for an Interest

    GET /topics/search?search-query=Q

#### Parameters

Name | Type | Description
-----|------|--------------
`search-query`|`string` | The search keywords.


#### Response

A JSON map with a `results` key that has a list of topics, each with an `id` and `topic` name.

As a [Schema](https://github.com/Prismatic/schema): 
```clojure
{:results [{:id long
           :topic String}]}
```



##I think the system made a mistake, where can I report it?

Our approach to topic modeling is inherently data-driven, and as with all
data-driven models, it is subject to some noise. It is impossible to have 100%
precision and recall on all queries. There are some articles that might be
mis-tagged with incorrect interests, and some articles whose content reflects a
particular topic that our models fail to detect. On the whole, these models do
a good job, but errors are inevitable. We will record all reported errors in
order to feed them back into our training pipeline to ensure it improves over
time. To report an error, visit our [Topic Classification Error Reporting
Page](http://goo.gl/forms/CU0n34fQ7c).

##Do you have the topic I care about?

We have over 5k modeled interests, and while we try to model the most popular
interests that are applicable over a wide range of applications, we do not
currently model everything. To check whether your topic is currently modeled,
visit our [Topic Search
Page](http://interest-graph.getprismatic.com/topic/search/human).

##You don’t currently model my interest. Where can I submit a request for you to model a new interest?

Currently, the set of interests is fixed. Given our resources, we are limited in
how many interests we can reliably model. While we do plan to expand the set of
modeled interests, we will prioritize which interests we add based on aggregate
demand. If you would like to submit a request to model new topic, please visit
our [Interest Submission Page](http://goo.gl/forms/8ryfTk8I6Z).

