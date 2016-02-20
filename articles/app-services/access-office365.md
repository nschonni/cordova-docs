#Getting Started with Office 365 (Cordova Projects)

##What Happened

###References Added
The Active Directory Authentication Library (ADAL) Cordova plugin was included.

###Connection string values for Office 365
Under services\Office365\settings, a new JavaScript (.js) file with the name 
settings.js was created that contains the Office 365 client ID and tenant ID. 
The file contents would look similar to the following code.

```javascript
var O365 = {
        clientId: '<your client id>',
        authUri: 'https://login.microsoftonline.com/common/',
        redirectUri: 'http://localhost:4400/services/office365/redirectTarget.html',
        domain: 'microsoft.com',
        tenantId: '<your tenant id>'
};
```

## Authentication
The Azure AD Authentication Library (ADAL) for Cordova, a plugin located at:
https://github.com/AzureAD/azure-activedirectory-library-for-cordova, enables client 
application developers to easily authenticate users to cloud or on-premises Active 
Directory (AD), and then obtain access tokens for securing API calls. ADAL for Cordova 
has many features that make authentication easier for developers, such as asynchronous 
support, a configurable token cache that stores access tokens and refresh tokens, automatic 
token refresh when an access token expires and a refresh token is available, and more. 
By handling most of the complexity, ADAL can help a developer focus on business logic 
in their application and easily secure resources without being an expert on security.

### Login

```javascript
var AuthenticationContext = Microsoft.ADAL.AuthenticationContext;
AuthenticationContext.createAsync(O365.authUri)
    .then(function(authContext) {
        authContext.acquireTokenAsync(
            "resource-uri",     // Resource URI
            O365.clientId,      // Client ID
            O365.redirectUri    // Redirect URI
        ).then(function(authResult) {
            // Access token is available in authResult.accessToken);
        }, function(err) {
            // Error
        });
    }, function(err) {
        // Error
    });
```

### Logout

```javascript
authContext.tokenCache.clear().then(function() {
    // Cache cleaned up successfully
}, function(err) {
    // Error
});
```
## Outlook: Getting Inbox

```javascript
// Get Users
$.ajax({
    type: "GET",
    url: "https://graph.windows.net/myorganization/users?api-version=1.5",
    dataType: "json",
    headers: {
        'Authorization': 'Bearer ' + app.accessToken
    },
}).done(function(result) {
    result.value.forEach(function(user) {
        app.log(user.displayName);
    });
}).fail(function(jqXHR, textStatus, errorThrown) {
    // Error
});
```

## Graph: Getting Users
```javascript
// Get Users
$.ajax({
    type: "GET",
    url: "https://graph.windows.net/myorganization/users?api-version=1.5",
    dataType: "json",
    headers: {
        'Authorization': 'Bearer ' + app.accessToken
    },
}).done(function(result) {
    result.value.forEach(function(user) {
        app.log(user.displayName);
    });
}).fail(function(jqXHR, textStatus, errorThrown) {
    // Error
});
```

Learn more about Office 365 APIs