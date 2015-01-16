
# What Is SubPub?

## Brad Rowe

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

Take these topics in order in the next few slides

- - -

## Publish-Subscribe, Topic Filtered Messaging

- -

### messages connect software 

- web browser to web server
- email client to email client
- RESTful client to RESTful web service
- pipes between processes in a [POSIX](https://en.wikipedia.org/wiki/Unix_shell) compliant shell

- -

### Message Addressing Topologies

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

the two message distribution patterns in common use:

- queues
    - unicast, and buffered
- publish / subscribe
    - multicast, and unbuffered

Note:
most message oriented middleware provides different modes of operation for both,
or a combination of the two

- - -

## Asynchronous

- -

synchronicity: an API concept

*synchronous*

*asynchronous*

- send a letter using the post office

Note:
synchronicity is an API concept, which is characterized by responses to
requests. in the eyes of a client of synchronous api, one response always
comes after one request.

clients of a synchronous API are able to assume that a response will arrive soon.

in an asynchronous api, there are no requests and responses, only transmissions

- -

| synchronous     | asynchronous |
|---              |---           |
| telephone calls | email   |
| TCP | UDP |
| http |  |
|   	|   	|

- - -

## Over HTTP

- -

Messages into SubPub use standard HTML form encoding the same as is specified by the [w3c](http://www.w3.org/TR/html401/interact/forms.html), used to submit forms all over the web.

Note:
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
