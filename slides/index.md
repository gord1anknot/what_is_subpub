
# What Is SubPub?

## Brad Rowe

To see presentation notes, press the `s` key on
your keyboard.

- - -

## SubPub Is...

- Publish-Subscribe, Topic Filtered Messaging
- Asynchronous
- Over HTTP

Note:
Publish-Subscribe, Topic Filtered Messaging is an enterprise integration design
pattern

A synchronicity is an API paradigm

HTTP is an application level protocol in computer networks

We'll take these topics in order in the next few slides

- - -

## Publish-Subscribe, Topic Filtered Messaging

- -

### messages connect software 

- web browser to web server
- email client to email client
- RESTful client to RESTful web service
- pipes between processes in a [POSIX](https://en.wikipedia.org/wiki/Unix_shell) compliant shell

- -

### Message Addressing

- unicast
    - from one address to only one other; point to point
- broadcast
    - from one to everyone, everywhere
- multicast
    - from one to a group, but not everyone

Note:
"unicast", "broadcast", and "multicast" are networking-specific terms,
but these concepts are applicable to other computing domains

unicast:

- send an email from `alice@pearson.com` to `bob@pearson.com`

broadcast:

- the 9:00 news on network TV

multicast:

- send an email to a mailing list (using a [LISTSERV](https://en.wikipedia.org/wiki/Electronic_mailing_list))
- publish a SubPub message

- -

### Message Oriented Middleware

two message middleware patterns are commonly used:

- queues
    - unicast, and buffered
- publish / subscribe
    - multicast, and unbuffered

Note:

most message oriented middleware provides different modes of operation for both,
or a combination of the two

the unbuffered nature of publish / subscribe is necessary for scale

- -

### Topic Filtered

messages have a topic

subscribers will only get messages on the topic(s) they have subscribed to

subpub's topic is called the `MESSAGE-TYPE`

Note:
Content filtered - this message oriented middleware filtering is aware of
the format and schema of the messages themselves.

Topic filtered - this filtering mechanic uses only message metadata to make
decisions on where to send messages. the messages themselves are binary-opaque

- - -

## Asynchronous

- -

synchronicity: an API concept

*synchronous: requests have responses*

*asynchronous: send messages; then, call me maybe*

Note:
synchronicity is an API concept, which is characterized by expectations around
responses to requests. in the eyes of a client of synchronous api, one
response always comes after one request.

clients of a synchronous API are asked to assume that a response will arrive soon.

in an asynchronous api, there are no requests and responses, only transmissions
and receptions, which may occur unprompted

This is different from concepts around the semantics of trasport like
full-duplex and half-duplex; and this is also different from the concepts
of concurrency or parallelism

- -

| synchronous     | asynchronous |
|---              |---           |
| telephone calls | email |
| TCP | UDP |

- - -

## Over HTTP

- -

Messages into SubPub use standard HTML form encoding the same as is specified by the [w3c](http://www.w3.org/TR/html401/interact/forms.html), used to submit forms all over the web.

Note:
message oriented middleware has been around for a long, long time,
but the best solutions are traditionally closed source, or use a proprietary
protocol

on the other extreme, basically every piece of software that connects to a
network has a http client library, that can send a HTML 4 form post

SubPub marries the AMQP messaging protocol with the HTML 4 form post
specification

this spec doesn't have a concept of encoding (UTF-8, ISO, etc.)
lower ASCII, printable characters work as is, but how to send other text
isn't specified.

- -

Assuming that either JavaScript or the user is able to fill in a value for
`AUTHORIZATION`, is the following HTML snippet is enough to send a message to
SubPub from any web browser:

```html
<form action="http://prospero.ecollege.com/v1/message" method="post">
    <p>
    Message Type: <input type="text" name="MESSAGE-TYPE"><br>
    System: <input type="text" name="SYSTEM"><br>
    SubSystem: <input type="text" name="SUB-SYSTEM"><br>
    Client: <input type="text" name="CLIENT"><br>
    ClientString: <input type="text" name="CLIENT-STRING"><br>
    Realm: <input type="text" name="REALM"><br>
    Payload: <input type="text" name="PAYLOAD"><br>
    Payload Content Type: <input type="text"
                                 name="PAYLOAD-CONTENT-TYPE"><br>
    <input type="hidden" name="AUTHORIZATION"
           value="EXAMPLE|2015-01-16T03:49:38+0000|44a620f04b02619fef1f2552993688cc">
    <br>
    <input type="submit" value="Send">
    <input type="reset">
    </p>
</form>
```

- -

```
POST /v1/message HTTP/1.1
Content-Length: 257
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Host: prospero.ecollege.com
Connection: Keep-Alive
User-Agent: Apache-HttpClient/4.3.3 (java 1.5)
Accept-Encoding: gzip,deflate

CLIENT=dc.test
&CLIENT-STRING=dc.test
&MESSAGE-TYPE=dc.test
&SYSTEM=dc.test
&SUB-SYSTEM=dc.test
&REALM=*
&PAYLOAD=Hello%2C+World%21
&PAYLOAD-CONTENT-TYPE=application%2Fjson&TAGS
&AUTHORIZATION=ONE%7C2015-01-16T03%3A49%3A38%2B0000%7C44a620f04b02619fef1f2552993688cc
```

Note:
SubPub will see a message like this
*Line breaks included for clarity*

- -

Creation of subscriptions works the same...

```
POST /v1/subscription HTTP/1.1
Content-Length: 303
Content-Type: application/x-www-form-urlencoded; charset=US-ASCII
Host: prospero.ecollege.com
Connection: Keep-Alive
User-Agent: Apache-HttpClient/4.3.3 (java 1.5)
Accept-Encoding: gzip,deflate

CALLBACK-URL=http%3A%2F%2Fmyservice.example.net%3A5050%2Fcallbacks%3Fadorable_cat_pictures%3Dtrue%26sub%3D1
&WSDL-URI=
&TAGS=
&CLIENT=dc.test
&CLIENT-STRING=dc.test
&MESSAGE-TYPE=dc.test
&SYSTEM=dc.test
&SUB-SYSTEM=dc.test
&AUTHORIZATION=ONE%7C2015-01-16T04%3A05%3A31%2B0000%7C6a221781ba5ecc66db83a11c0dd42d2e
```

- -

as well as message deliveries from SubPub.

```
POST /callback HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Host: myservice.example.net:5151
Content-Length: 327

DELIVERY-ATTEMPT-ID=00000cf5-67fc-5fd5-2875-37d1b13142e1
&MESSAGE-TYPE=dc.test
&MESSAGE-ID=00000cf5-67fc-5fd5-2875-37d1b13142e1
&AUTHORIZATION=ONE%7C2015-01-16T04%3A11%3A50Z%7C0169ac2ec9ffc60ceff2f003b72e8a33
&AUTHORIZATION-DELIMITER=%7C
&PAYLOAD=%7B%27message%27%3A%27Hello%2C+World%21%27%7D
&PAYLOAD-CONTENT-TYPE=application%2Fjson
```

Note:
*Line breaks added for clarity*

- - -

## Functionally Testing a SubPub Integration

Note:

This advice should apply for ANY message oriented middleware integration

- -

*publishers:*

- know what happens when your app can't contact SubPub
- know how you will change any environment specific connection parameters for publication
- know how many message you intend to publish (rate, and volume in bytes / second)
- know if those messages are sent in batches, or spikes

Note:

for the first point, "what happens" may include behavior not just of your app,
but other apps that will receive your messages. Of course, your responsibilities
when testing always depend on what you've promised in your API's contract. The
apps that receive your messages are ultimately responsible for understanding their
own behavior if they don't get messages.

- -

*subscribers:*

- know what happens if your app receives more than one copy of the exact same message
- know how you will change any environment specific connection parameters for publication
- know how and when your app creates a subscription, and what happens if it is unable to
- know what happens if your app receives messages out of order

Note:

Receiving more than one copy of the same message is always possible in a cloud
application. It is mathematically impossible to guarantee once and only once
without assuming that everything always works perfectly. Read more about why
that is
[here](http://stackoverflow.com/questions/416551/why-is-exactly-once-semantics-infeasible).

many subscribing applications make a subscription creation request at startup.
this is the simplest and most reliable technique, however, as your app is
starting up, the subscription request may fail, or generate an error.

It's important to understand how your app behaves if that were to happen.

- -

*publishers AND subscribers:*

- don't make message publication and receipt the only way to access your app's data

Note:

Just like when you use the post office to mail an important document, know
what your options are if the recipient doesn't get it. Can you call them
directly? Or was it really important that they receive it in the first place?
