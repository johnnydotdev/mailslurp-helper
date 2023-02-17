# MailSlurp helper for CodeceptJS

MailSlurp helper for testing emails with [MailSlurp service](https://mailslurp.com).
MailSlurp creates disposable mailbox for each test and removes it after a test.

## Setup

Register and obtain API key from [MailSlurp](https://mailslurp.com).

    npm i @codeceptjs/mailslurp-helper --save

Enable helper in `codecept.conf.js`.

```js
helpers: {
  MailSlurp: {
    apiKey: '<insert api key here>',
    require: '@codeceptjs/mailslurp-helper'    
  },
}
```

## Use Cases

Use this helper in your tests to check email interactions. The most popular one

-   Account activation
-   Restore forgotten password
-   Action verification

## Use Case: Restore Password

```js
const mailbox = await I.haveNewMailbox();
// register user on a website
I.registerUser(mailbox.emailAddress);
// wait 10 seconds for an email
const email = await I.waitForLatestEmail(10);
I.seeInEmailSubject('Restore Password');
I.seeInEmailBody('Click link to restore password');
const restoreUrl = email.body.match(/http(s):\/\/(.*?)\s/)[0];
I.amOnPage(restoreUrl);
```

## Switching Between Mailboxes

```js
const mailbox1 = await I.haveNewMailbox();
const mailbox2 = await I.haveNewMailbox();
I.openMailbox(mailbox1);
const email = I.waitForEmailMatching({ subject: 'Register' });
```

## Using Custom Assertions In Tests

```js
const assert = require('assert');
const mailbox = await I.haveNewMailbox();
const email = await I.waitForEmailMatching({ subject: 'Thanks' });
assert.eql(email.subject, 'Thanks for registering')
```

## API

<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

#### Table of Contents

-   [MailSlurp](#mailslurp)
-   [Configuration](#configuration)
    -   [Parameters](#parameters)
    -   [haveNewMailbox](#havenewmailbox)
    -   [haveExistingMailbox](#haveexistingmailbox)
    -   [openMailbox](#openmailbox)
        -   [Parameters](#parameters-1)
    -   [sendEmail](#sendemail)
        -   [Parameters](#parameters-2)
    -   [waitForLatestEmail](#waitforlatestemail)
        -   [Parameters](#parameters-3)
    -   [waitForEmailMatching](#waitforemailmatching)
        -   [Parameters](#parameters-4)
    -   [waitForNthEmail](#waitfornthemail)
        -   [Parameters](#parameters-5)
    -   [grabEmailsMatching](#grabemailsmatching)
        -   [Parameters](#parameters-6)
    -   [grabAllEmailsFromMailbox](#graballemailsfrommailbox)
    -   [seeInEmailSubject](#seeinemailsubject)
        -   [Parameters](#parameters-7)
    -   [dontSeeInEmailSubject](#dontseeinemailsubject)
        -   [Parameters](#parameters-8)
    -   [seeInEmailBody](#seeinemailbody)
        -   [Parameters](#parameters-9)
    -   [dontSeeInEmailBody](#dontseeinemailbody)
        -   [Parameters](#parameters-10)
    -   [seeEmailIsFrom](#seeemailisfrom)
        -   [Parameters](#parameters-11)
    -   [seeEmailSubjectEquals](#seeemailsubjectequals)
        -   [Parameters](#parameters-12)
    -   [dontSeeEmailSubjectEquals](#dontseeemailsubjectequals)
        -   [Parameters](#parameters-13)
    -   [seeNumberOfEmailAttachments](#seenumberofemailattachments)
        -   [Parameters](#parameters-14)
    -   [seeEmailAttachment](#seeemailattachment)
        -   [Parameters](#parameters-15)

### MailSlurp

Allows to use real emails in end 2 end tests via [MailSlurp service](https://mailslurp.com).
Sign up for account at MailSlurp to start.

A helper requires apiKey from MailSlurp to start

```js
helpers: {
  MailSlurp: {
    apiKey: '<insert api key here>',
    require: '@codeceptjs/mailslurp-helper'    
  },
}
```

> Use .env file and environment variables to store sensitive data like API keys

### Configuration

-   `apiKey` (required) -  api key from MailSlurp
-   `timeout` (default: 10000) - time to wait for emails in milliseconds.

#### Parameters

-   `config`  

#### haveNewMailbox

Creates a new mailbox. A mailbox will be deleted after a test.
Switches to last created mailbox.

```js
const mailbox = await I.haveNewMailbox();
```

#### haveExistingMailbox

Use existing mailbox.

```js
const mailbox = await I.haveExistingMailbox('94cxxxf4-7231-46ce-9f40-xxxcae39xxxx');
```

#### openMailbox

Change a current mailbox to a provided one:

```js
const mailbox1 = await I.haveMailbox();
const mailbox2 = await I.haveMailbox();
// mailbox2 is now default mailbox
// switch back to mailbox1
I.openMailbox(mailbox)
```

##### Parameters

-   `mailbox`  

#### sendEmail

Sends an email from current mailbox, created by `I.haveNewMailbox()`.

```js
I.sendEmail({
  to: ['user@site.com'],
  subject: 'Hello',
  body: 'World'       
});
```

##### Parameters

-   `data`  

#### waitForLatestEmail

Waits for the first email in mailbox.
If mailbox is not empty - opens the last email.

```js
I.waitForLatestEmail()
// wait for 30 seconds for an email
I.waitForLatestEmail(30);
```

##### Parameters

-   `sec` **num?** Number of seconds to wait.

Returns **[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)&lt;Email>** an email received.

#### waitForEmailMatching

Wait for an exact email matched by query. You can match emails by `from`, `to`, `subject`, `cc`, `bcc` fields.
My default, non-strcit matching enabled, so it searches for inclusion of a string. For a strict matching (equality) 
prepend a value with `=` prefix.

```js
 // wait for email with 'password' in subject
const email = await I.waitForEmailMatching({
 subject: 'password',
});

// wait 30 seconds for email with exact subject
const email = await I.waitForEmailMatching({
 subject: '=Forgot password',
}, 30);

// 
const email = await I.waitForEmailMatching({
 from: '@mysite.com', // find anything from mysite
 subject: 'Restore password', // with Restore password in subject
});
```

##### Parameters

-   `query` **[object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)** to locate an email
-   `sec` **num?** Number of seconds to wait.

Returns **[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)&lt;Email>** an email received.

#### waitForNthEmail

Wait for exact number of emails in mailbox. Returns the last email in the list.

```js
// wait for 2 emails
I.waitForNthEmail(2);
// wait for 5 emails for 60 seconds
I.waitForNthEmail(5, 60);
// wait for 2 emails and return the last one
const email = await I.waitForNthEmail(2); 
```

##### Parameters

-   `number`  
-   `sec`  

#### grabEmailsMatching

Returns a bunch of emails matched by query. 
Similar to `waitForEmailMatching` but returns an array of emails.

```js
// return 2 emails from 'user@user.com'
const emails = await I.grabEmailsMatching({ from: 'user@user.com'}, 2);
```

##### Parameters

-   `query` **[object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)** to locate an email
-   `num` **num?** Number of emails to return.

Returns **[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)&lt;\[Email]>** emails matching criteria.

#### grabAllEmailsFromMailbox

Returns all emails from a mailbox.

```js
const emails = await I.grabAllEmailsFromMailbox();
```

Returns **[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)&lt;\[Email]>** emails.

#### seeInEmailSubject

Checks that current email subject contains a text.

```js
I.seeInEmailSubject('Restore password');
```

Requires an opened email. Use either `waitForEmail*` methods to open. Or open manually with `I.openEmail()` method.

##### Parameters

-   `text`  

#### dontSeeInEmailSubject

Checks that current email subject does not contain a text.

```js
I.seeInEmailSubject('Restore password');
```

Requires an opened email. Use either `waitForEmail*` methods to open. Or open manually with `I.openEmail()` method.

##### Parameters

-   `text`  

#### seeInEmailBody

Checks that current email body contains a text.

```js
I.seeInEmailBody('Click link');
```

Requires an opened email. Use either `waitForEmail*` methods to open. Or open manually with `I.openEmail()` method.

##### Parameters

-   `text`  

#### dontSeeInEmailBody

Checks that current email body does not contain a text.

```js
I.dontSeeInEmailBody('Click link');
```

Requires an opened email. Use either `waitForEmail*` methods to open. Or open manually with `I.openEmail()` method.

##### Parameters

-   `text`  

#### seeEmailIsFrom

Checks that email is from a specified address.

```js
I.seeEmailIsFrom('user@user.com');
```

Requires an opened email. Use either `waitForEmail*` methods to open. Or open manually with `I.openEmail()` method.

##### Parameters

-   `text`  

#### seeEmailSubjectEquals

Checks that current email subject equals to text.

```js
I.seeEmailSubjectEquals('Restore password');
```

Requires an opened email. Use either `waitForEmail*` methods to open. Or open manually with `I.openEmail()` method.

##### Parameters

-   `text`  

#### dontSeeEmailSubjectEquals

Checks that current email subject doesn't equal to text.

```js
I.dontSeeEmailSubjectEquals('Restore password');
```

Requires an opened email. Use either `waitForEmail*` methods to open. Or open manually with `I.openEmail()` method.

##### Parameters

-   `text`  

#### seeNumberOfEmailAttachments

Checks that current email has expected number of attachments.

```js
I.seeNumberOfEmailAttachments(2);
```

Requires an opened email. Use either `waitForEmail*` methods to open. Or open manually with `I.openEmail()` method.

##### Parameters

-   `number`  

#### seeEmailAttachment

Checks that current email has an attachment with specified name.

```js
I.seeEmailAttachment('ExampleAttachment.pdf');
```

Be aware that Mailslurp SDK removes special characters in name of attachment,
e.g. "Example-Attachment.pdf" will have name "ExampleAttachment.pdf".

Requires an opened email. Use either `waitForEmail*` methods to open. Or open manually with `I.openEmail()` method.

##### Parameters

-   `nameRegExp`  
