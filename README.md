# Asgardio JavaScript OIDC SDK

## Table of Contents

- [Introduction](#introduction)
- [Getting Started](##getting-started)
    - [Install](###install)
    - [Initialize](###initialize)
    - [Sign In](###sign-in)
    - [Sign Out](###sign-out)
    - [Use a Custom Grant](###use-a-custom-grant)
    - [Callback Hooks](###custom-hooks)
- [Try Out the Sample Apps](##try-out-the-sample-apps)
- [APIs](##apis)
- [Develop](##develop)
    - [Prerequisites](###prerequisites)
    - [Installing Dependencies](###installing-dependencies)
- [Contribute](##contribute)
- [License](##license)

## Introduction
Asgardio's OIDC SDK for JavaScript allows Single Page Applications to use OIDC or OAuth2 authentication in a simple and secure way. By using Asgardio and the JavaScript OIDC SDK, developers will be able to add identity management to their Single Page Applications in a jiffy.

## Getting Started

### Install
Install the JavaScript library from the npm directory.
```
npm install --save @asgardio/authentication
```
Or simply load the SDK by importing the script into the header of your HTML file.
```html
<script src=""></script>
```
### Initialize
The SDK provides a client that can be used to carry out the authentication.
```javascript
import { IdentityClient } from "@asgardio/authentication"
```
This client is a singleton and can be instantiated as follows.
```javascript
const auth = IdentityClient.getInstance();
```
Once instantiated, the  client can be initialized by passing the relevant parameters such as the server origin, redirect URL, client ID, etc.
```javascript
auth.initialize({
        callbackURL: "http://localhost:9443/my-account/login",
        clientHost: "http://localhost:9443/my-account/",
        clientID: "client ID",
        enablePKCE: true,
        responseMode: "query",
        scope: [ "email" ],
        serverOrigin: "http://localhost:9443/",
        storage: "sessionStorage"
    });
```
[Learn more](###initialize).

### Sign In
To sign in, simple call the `signIn()` method.
```javascript
auth.signIn();
```
[Learn more](###signin).


### Sign Out
The `signOut()` method can be used to sign a user out. The `sign-out` hook is used to fire a callback function after signing out is successful. Check the [on()](###on()) section for more information.
```javascript
auth.signOut()
```

[Learn more](###signout).

### Use a Custom Grant
You can use custom grants with our SDK using the `customGrant()` method. The `sign-in` hook is used to fire a callback function after signing in is successful. Check the [on()](###on()) section for more information.
```javascript
oAuth.customGrant({
    attachToken: false,
    data: {
        client_id: "{{clientId}}",
        grant_type: "account_switch",
        scope: "{{scope}}",
        token: "{{token}}",
    },
    id: "account-switch",
    returnResponse: true,
    returnsSession: true,
    signInRequired: true
})
```

[Learn more](###customgrant).

### Callback Hooks
Callback functions can be attached to authentication methods such as sign in and sign out using hooks. Hooks can be attached to the client using the `on()` method.
```javascript
 auth.on("sign-in", (response) => {
     alert("You have successfully signed in!");
 }
```
[Learn more](###on).

## Try Out the Sample Apps

## APIs
### getInstance
This returns an instance of the `IdentityClient`. Since the `IdentityClient` is a singleton, this method returns the same instance no matter how many time sit is called.

This allows the developers the flexibility of using multiple files to implement the authentication logic. That is, you can have the sign in logic implemented on one page and the sign out logic on another.

### initialize

The `initialize` method is used to the initialize the client. This *MUST* be called soon after instantiating the `IdentityClient` and before calling another methods.

This method takes a `config` object as the only argument. The attributes of the `config` object is as follows.

|Attribute| Type | Default Value| Description|
|:-----|:----|:----|:----|
|`callbackURL`|`string`||The URL to redirect to after the user authorizes the client app. eg: `https://conotoso.com/login` |
|`clientHost`|`string`||The hostname of the client app.  eg: `https://contoso.com`|
|`clientID`| `string` | |The client ID of the OIDC application hosted in the Asgardio.
|`clientSecret` (optional)||`string`|The client secret of the OIDC application|
|`enablePKCE` (optional)|`boolean`|`true`|Specifies if a PKCE should be sent with the request for the authorization code. |
|`prompt` (optional)|`string`||Specifies the prompt type of an OIDC request|
|`responseMode` (optional)|`string`|`query`| Specifies the response mode. The value can either be `query` or `form_post`|
|`scope` (optional)|`string[]`|`[openid]`|Specifies the requested scopes|
|`serverOrigin`|`string`||The origin of the Identity Provider. eg: `https://www.asgardio.io`|
|`storage` (optional)| `sessionStorage`| `webWorker`|`localStorage`|`sessionStorage`| The storage medium where the session information such as the access token should be stored.|
|`baseUrls` (required if the `storage` is set to `webWorker`|`string[]`||The URLs of the API endpoints.|
|`endpoints` (optional)|(`ServiceResourceTypes`)[#serviceresourcetypes]|| The OIDC endpoint URLs. The SDK will try to obtain the endpoint URLS using the `.well-known` endpoint. If this fails, the SDK will use these endpoint URLs. If this attribute is not set, then the default endpoint URLs will be used.|

The `initialize` hook is used to fire a callback function after initializing is successful. Check the [on()](###on) section for more information.

#### ServiceResourceTypes
|Attribute|Type|Default Value|Description|
|:--|:--|:--|:--|
|`authorize`|`string`|`/oauth2/authorize`| The endpoint to send the authorization request to.|
|`jwks`|`string`|`/oauth2/jwks`| The endpoint from which the JSON Web Keyset can be obtained`|
|`logout`|`string`| `/oidc/logout`|The endpoint to send the logout request to.
|`oidcSessionIFrame`|`string`| `/oidc/checksession`| The URL of the OIDC session iframe.
|`revoke`|`string`| `/oauth2/revoke`| The endpoint to send the revoke-access-token request to.
|`token`|`string`| `/oauth2/token`| The endpoint to send the token request to.|
|`wellKnown`|`string`| `/oauth2/oidcdiscovery/.well-known/openid-configuration`| The endpoint to receive the OIDC endpoints from|

```javascript
auth.initialize(config)
```

### getUserInfo
This method returns the information about the authenticated user as an object. The object has the following attributes.

|Attribute| Type | Description|
|:--|:--|:--|
|`email`|`string`|The email address of the user|
|`username`|`string`| The username of the user|
|`displayName`| `string`| The display name of the user|
`allowedScopes`|`string`| The scopes the user has authorized teh client to access|
```javascript
auth.getUserInfo().then(response=>{
    console.log(response);
})
```

### signIn

This method initiates the authentication flow.

The `sign-in` hook is used to fire a callback function after signing in is successful. Check the [on()](###on) section for more information.


```javascript
auth.signIn();
```

### signOut
This method ends the user session at the Identity Server and logs the user out.

The `sign-out` hook is used to fire a callback function after signing out is successful. Check the [on()](###on) section for more information.

### httpRequest
This method  is used to send http requests to the Identity Server. The developer doesn't need to manually attach the access token since this method does it automatically.

If the `storage` type is set to `sessionStorage` or `localStorage`, the developer may choose to implement their own ways of sending http requests by obtaining the access token from the relevant storage medium and attaching it to the header. However, if the `storage` is set to `webWorker`, this is the *ONLY* way http requests can be sent.

This method accepts a config object which is of type `AxiosRequestConfig`. If you have used `axios` before, you can use the `httpRequest` in the exact same way.

```javascript
auth.httpRequest(config).then(response=>{
    console.log(response);
}).catch(error=>{
    console.error(error);
})
```

### httpRequestAll
This method is used to send multiple http requests at the same time. This works similar to `axios.all()`. An array of config objects need to be passed as the argument and an array of responses will be returned in a `Promise` in teh order in which the configs were passed.

```javascript
auth.httpRequestAll(configs).then(responses => {
    response.forEach(response=>{
        console.log(response);
    })
}).catch(error=>{
    console.error(error);
})
```

### customGrant
This method allows developers to use custom grants provided by their Identity Servers. This method accepts an object that has the following attributes as the argument.
|Attribute|Type|Description|
|:--|:--|:--|
`id`| `string`|A unique id for the custom grant. This allows developers to use multiple custom grants.|
`data`| `any`|The data to be attached to teh body of the request to the Identity Server|
`signInRequired`| `boolean`|Specifies if the custom grant requires an active user session.|
`attachToken`| `boolean`|Specifies if the access token should be attached to the header of teh request.|
`returnsSession`| `boolean`|Specifies if the response to teh custom grant request would return session information. If set to yes, then the current session will be updated with the returned session.|
`returnResponse`| `boolean`|Specifies if the response returned by the custom grant should be returned back. If the `returnsSession` attribute is set to `true` then only the user information is returned.|

The `custom-grant` hook is used to fire a callback function after a custom grant request is successful. Check the [on()](###on) section for more information.

#### The data attribute
Often, you may have to send session information in the body of a custom grant request. Since when using a web worker to store the session information, you won't have access to the session information, Asgardio provides template tags to attach the necessary session information. When a template tag is used, the SDK automatically replaces the tag with the relevant session information before sending the request. For example, if the access token should be send in teh body of the request, you can use the `{{token}}` template tag. The SDK will replace this tag with the access token before dispatching the request.

The following template tags are at your disposal.
|Template Tags| Session Information Attached|
|:--|:--|
`"{{token}}"`|The access token|
`"{{username}}"`|The username|
`"{{scope}}"`| The allowed scopes |
`"{{clientId}}"`| The client ID|
`"{{clientSecret}}"`|The client secret|

```javascript
return oAuth.customGrant({
    attachToken: false,
    data: {
        client_id: "{{clientId}}",
        grant_type: "account_switch",
        scope: "{{scope}}",
        token: "{{token}}",
    },
    id: "account-switch",
    returnResponse: true,
    returnsSession: true,
    signInRequired: true
})
```

### endUserSession
This method revokes the access token and clears the session information from the storage.

The `end-user-session` hook is used to fire a callback function after end user session is successful. Check the [on()](###on) section for more information.

### getServiceEndpoints
This method returns an object containing the OIDC endpoints obtained from the `.well-known` endpoint.

### on
The `on` method is used to hook callback functions to authentication methods. The method accepts a hook name and a callback function as the only arguments except when the hook name is "custom-grant", in which case the id of the custom grant should be passed as the third argument. The following hooks are available.

|Hook|Method to which the callback function is attached| Returned Response|
|:--|:--|:--|
|`"sign-in"`|`signIn()`| The user information|
|`"sign-out"`|`signOut()`|
|`"initialize"`|`initialize()`|A boolean value indicating if the initialization was successful or not.|
|`"http-request-start"`| `httpRequest()` (Called before an http request is sent)|
|`"http-request-finish"`|`httpRequest()` (Called after an http request is sent and response is received.)|
|`"http-request-error"`| `httpRequest()` (Called when an http request returns an error)|
|`"http-request-success"`| `httpRequest()` (Called when an http requests returns a response successfully)|
|`"end-user-session"`| `endUserSession()`| A boolean value indicating if the process was successful or not
|`"custom-grant"`| `customGrant()`|

## Develop
### Prerequisites
- `Node.js` (version 10 or above).
- `npm` package manager.
### Installing Dependencies
```
npm install
```

## Contribute

Please read [Contributing to the Code Base](http://wso2.github.io/) for details on our code of conduct, and the process for submitting pull requests to us.

### Reporting issues
We encourage you to report issues, improvements, and feature requests creating [git Issues](https://github.com/wso2-extensions/identity-samples-dotnet/issues).

Important: And please be advised that security issues must be reported to security@wso2com, not as GitHub issues, in order to reach the proper audience. We strongly advise following the WSO2 Security Vulnerability Reporting Guidelines when reporting the security issues.

## License
This project is licensed under the Apache License 2.0. See the [LICENSE](LICENSE) file for details.
