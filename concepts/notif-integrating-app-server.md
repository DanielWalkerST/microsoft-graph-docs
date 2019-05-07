---
title: "Setup your Application Service to send Microsoft Graph notifications "
description: "Setup your application service to send user-centric notifications to various clients via the Microsoft Graph. "
localization_priority: Priority
ms.prod: "Microsoft Graph notifications"
---

# Service Side Integration

[!INCLUDE [beta-disclaimer](../api-reference/includes/beta-disclaimer.md)]

## Create and send a notification from your app service

You can now create and send a notification that is targeted to a user using Microsoft Graph. The notification will be stored in the activity feed store and sent to all app clients on all device endpoints that the target user is signed-in to. See below to learn how to authenticate, required permission scopes, request header/body and expected response.

## Authentication

Microsoft Graph notifications requires that your application service uses the On-Behalf-Of (OBO) flow to post a notification to a user. The following is the authentication flow:

1.  The user signs in to your application with their Microsoft or their work or school account. When they sign in, the identity service gives you an access token.

2.  You send the access token to your app service.

3.  You app service authenticates against the identity service and requests an OBO token for Microsoft Graph notifications.

4.  The identity service returns an OBO-based token and a refresh token. Your app service can use this access token to post notifications to this user.

To learn more about OAuth 2.0 OBO flow, see [Service-to-service calls that use delegated user identity in the On-Behalf-Of flow](https://docs.microsoft.com/en-us/azure/active-directory/develop/v1-oauth2-on-behalf-of-flow). For details about how this flow works with Microsoft Graph notifications, see the [App Service sample](https://aka.ms/gnsample-appservice).

> [!NOTE]
> Microsoft Graph notifications currently uses OBO authentication flow with future plans to simplify this authentication further and eliminate the need to maintain access tokens and refresh tokens.

## Permissions
One of the following permissions is required to call this API. To learn more, including how to choose permissions, see [Permissions](/graph/permissions-reference).

|Permission type      | Permissions (from least to most privileged)              |
|:--------------------|:---------------------------------------------------------|
|Delegated (work or school account) | Notifications.ReadWrite.CreatedByApp    |
|Delegated (personal Microsoft account) | Notifications.ReadWrite.CreatedByApp    |

## HTTP request

<!-- { "blockType": "ignored" } -->

```http
POST /me/notifications/
```
## Request headers
|Name | Type | Description|
|:----|:-----|:-----------|
|Authorization | string |The authorization header is used to pass the credentials of the calling party. Bearer {token}. Required. |
## Request body
In the request body, supply a JSON representation of a [notification](https://docs.microsoft.com/en-us/graph/api/resources/projectrome-notification?view=graph-rest-beta) object.

## Response
If successful, this method returns the `201 Created` response code that indicates that the notification was successfully created and stored. 
## Example
#### Request
The following is an example of the request.

```http
POST https://graph.microsoft.com/beta/me/notifications/
Content-type: application/json

{
    "targetHostName": "graphnotifications.sample.windows.com",
    "appNotificationId": "testDirectToastNotification",
    "expirationDateTime": "2018-08-29T23:51:33.000Z",
    "payload": {
        "visualContent": {
            "title": "Hello World!",
            "body": "Notifications are Great!"
        }
    },
    "targetPolicy": {
        "platformTypes": [
        "windows",
        "android"
        ]
    },
    "priority": "High",
    "groupName": "TestGroup",
    "displayTimeToLive": "23"
}
```

#### Response
The following is an example of the response.

```http
HTTP/1.1 201
Content-Type: application/json
location: https://graph.microsoft.com/beta/me/notifications/518c4fb1-c565-4d67-95c4-bcc3eb8eda1b

{
    "@odata.context": "https://graph.microsoft.com/test872018/$metadata#users('graphNotificationsUser%40contoso.com')/notifications/$entity",
    "appNotificationId": "testDirectToastNotification",
    "displayTimeToLive": 23,
    "expirationDateTime": "2018-08-24T12:31:53.858Z",
    "groupName": "TestGroup",
    "id": "cd5c5e6a-99ce-470a-9982-c47635e73620",
    "priority": "1",
    "payload": {
        "visualContent": {
            "title": "Hello World!",
            "body": "Notifications are Great!"
        }
    }
}
```