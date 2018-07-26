# RFC 0003 - Embedded 3rd Party Interfaces

## Summary

Extension from [RFC0001](rfc-0001-third-party-development-requirements.md). Zesty.io needs a way for a 3rd Party UI to be loaded inside of the instance manager application.

## Motivation

To allow for extensibility of our SaaS API, we need a way for developers to extend our interface to work both with our APIs and their custom build 3rd party solutions. The ability to do this drives towards an open 3rd party marketplace that will customers and developers ways to solve problems outside of our core offering.

# Overview

Zesty.io platform will be extended to provide a mechanism through which developers can take part in a 3rd party web applications ecosystem.  This will be achieved by providing developers the ability to submit JavaScript applications to run as part of the Zesty manager interface.  These applications will be given access to information and API tokens of the logged in user, and may call third party APIs as needed.

### Registering an Application (getting a token)

User registers an application through the accounts app, submitting this information:

* ID
* Name
* Thumbnail image
* Business name
* Support URL
* Contact email

We are considering using a YAML descriptor file to contain this information.

In return they receive:

* Private app token
* Refresh token (we will not initially implement token refresh but developers should be prepared to store and use these in future)
* App ZUID (zesty unique identifier)
* Client app id (name)

### Application Requirements

A third party application must:

* Be written in JavaScript
* Be supplied as a "built" application - the Zesty.io platform will not provide an environment for transpiling or building JavaScript applications
* Make all API calls to the Zesty platform and any external APIs that it uses on an SSL encrypted connection
* Assume that it is running in the context of the Zesty.io platform manager app for a Zesty instance (3rd party applications will not run at the account level where they might concurrently have access to all of the logged in user's Zesty instances)
* Provide a `thirdPartyApplication.main` function which will be called by Zesty.  This function should expect to receive the ID of the element in the Zesty manager app that the application's user interface should build itself within

### Loading an Application

Applications must have their source code in a single JavaScript file, and the link to that file must be submitted to Zesty for review (could be GitHub or any CDN).

Our working assumption is that this link would be provided in the YAML descriptor file for the application.

* Only a single JavaScript file
* CSS and HTML should be generated in the JavaScript
* If the app is approved, a pull request can be made against our public repository

Need to consider security here, should an MD5 hash of the JavaScript be submitted when the application is setup, and we should check that?  Load the JavaScript directly from GitHub using the provided URL, or have Zesty proxy it to obfuscate the original URL and perform security checks?

#### Other Notes

* Once the app is running from Zesty.io manager, it can leverage the logged in user's cookie (if we still use that method for auth) and other objects on the window
* All endpoints consumed must be TLS
* Third party APIs must have CORS enabled to allow access from Zesty.io domains

### Submitting Application to App Store

User submits for application approval into the 3rd party app store.

This calls for Zesty.io team review. Approval means all Zesty.io instances would have access to "install" the app to their respective instance and have access to the 3rd party app through the content manager UI.

# Drawbacks

* Potentially creates code review / approval process overhead for Zesty staff

# Alternatives

* An alternative could be to not do this, and have developers only build apps that sit alongside Zesty using Zesty.io platform APIs.  However, this would result in a fragmented user interface experience for the end user.This approach allows both Zesty.io and third party provided app functionality to share a common user interface

# Unresolved Questions

* Should Zesty provide a CDN hosted JavaScript helper library for pulling values from the user's session / dealing with the Zesty API?
* Process for developing a 3rd Party interface prior to submission
