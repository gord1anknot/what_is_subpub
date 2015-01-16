
# What Is SubPub?

## Brad Rowe

- - -

- Publish-Subscribe, Topic Filtered Messaging
- Asynchronous
- Over HTTP

Note:
Take these topics in order in the next few slides

- - -

## Publish-Subscribe

- -

- messages connect software
    - web browser to web server
    - email client to email client
- unicast (point to pint)

- - -

## Over HTTP

- -

Messages into SubPub use standard HTML form encoding, the same
as is specified by the [w3c](http://www.w3.org/TR/html401/interact/forms.html),
used to submit forms all over the web.

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
