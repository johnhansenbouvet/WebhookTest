# WebhookTest

[Exercise - Set up a webhook for a GitHub repository](https://docs.microsoft.com/nb-no/learn/modules/monitor-github-events-with-a-function-triggered-by-a-webhook/5-exercise-setup-webhook-for-github-repo)

## Add a webhook for the Gollum Event
The Gollum event is the name of the event in GitHub that is fired whenever a page in the repo's wiki is created or updated.
- Go back to main page for your repository.
- Select the Settings tab.
- Select Webhooks in the navigation panel to the left of the page.
- Select the Add webhook button on the top-right.
- Set the payload to the URL for your function app's function from the previous exercise. Remember that it looks similar to this:


## Test the webhook
In the GitHub portal for your repository, click the Wiki tab.
Select the page that you created earlier.
Select Edit.
In the text area of the page, enter the following text:
text

Testing Webhook

Select Save Page to save your update.
Select the Settings tab.
Click Webhooks in the navigation panel on the left.
 Obs!
The webhook will indicate that the message was not processed correctly; it will generate an HTTP 400 error. The webhook is providing a payload that your function app wasn't expecting, and doesn't include a name parameter. You will learn how to parse the payload for a Gollum event in the next unit.
Select Edit next to your webhook.
Scroll down to the Recent Deliveries section.
Select the latest delivery entry by clicking on the ellipsis (...).

rigger your Azure Function with a Gollum event
Return to your GitHub account.
Select the repository you are using for this module.
Select the Settings tab.
Select Webhooks in the navigation panel.
Select the Edit button next to your webhook.
Scroll down to the Recent Deliveries section.
Select the latest delivery entry by clicking the ellipsis button (...).
Select Redeliver.
In the message box that appears, select Yes, redeliver this payload. This action simulates you changing your Wiki page again.
Select the Response tab. You'll see how the webhook, has triggered your function, which then parsed the information and sent back a response similar to the following text:
text

Kopier

Page is Home, Action is edited, Event Type is gollum

Neste leksjon: Secure Webhook payloads with a secret

## Secure Webhook payloads with a secret

Once your function is configured to receive payloads, it will listen for any payload sent to the endpoint you configured. For security reasons, you might want to limit requests to those coming from GitHub. There are a few ways to go about this. For example, you could opt to approve requests from GitHub's IP address. An easier method is to set up a secret token and validate the request using this token.
In the example scenario, your IT Department's management are happy with the webhook triggered function that you've created in an Azure Functions app. All of the information regarding updates to the company wiki are being parsed by that function and sent to the business, each time the Gollum event is triggered. The management has asked how secure the information passed from GitHub is. They've asked you to find a way to secure the information, and verify it's GitHub that is sending updates.
In this unit, you'll learn how to secure your webhook payload with a secret and validate payloads from GitHub.

## Webhook secrets
Setting a webhook secret allows you to ensure that POST requests sent to the payload URL are from GitHub. When you set a secret, you'll receive the x-hub-signature header in the webhook POST request.
In GitHub, you can set the secret field by going to the repository where you have setup your webhook, and then editing the webhook. We'll show you how to do that for our example in the next exercise.

## Validating payloads from GitHub
When your secret token is set, GitHub uses it to create a hash signature for each payload. This hash signature is passed along with each request in the headers as x-hub-signature.
When your function receives a request, you need to compute the hash using your secret, and ensure that it matches the hash in the request header. GitHub uses an HMAC SHA1 hex digest to compute the hash, so you must calculate your hash in this same way, using the key of your secret and your payload body. The hash signature starts with the text sha1=.



Exercise - Secure webhook payloads with a secret
10 minutes
Sandkasse aktivert! Tid som gjenstår: 
1 t 35 min 
Du har brukt 3 av 10 sandbokser for i dag. Flere sandkasser vil være tilgjengelig i morgen.
In this exercise, you'll protect your webhook payload with a secret, and learn how to validate payloads from GitHub inside an Azure Function.
Get Key for your Azure Function
In the Azure portal, return to your function app that you created from the first exercise in the module.
Expand Functions.
Select the function that you created.
In your function's index.js JavaScript file, add a reference to the crypto-js library at the start of the file, above the module.exports statement.
JavaScript

Kopier

var Crypto = require('crypto');
In the side menu, select Manage.
In the Function Keys section, select Click to show next to the default key.
Under Actions, select Copy and save this key for use in the next step.
Back in the body of your function, after the context.log statement, add the following code. Replace *<default key> with the default key that you copied to the clipboard earlier:
JavaScript

Kopier

var hmac = Crypto.createHmac("sha1", "<default key>");
var signature = hmac.update(JSON.stringify(req.body)).digest('hex');
This code computes the hash of the key, using the same mechanism as GitHub.
Add sha1= to the start of the key, so that it matches the format of x-hub-signature in the request header. Add the following code to your function.
JavaScript

Kopier

var shaSignature =  `sha1=${signature}`;
Add the following code to retrieve the GitHub signature from the request header:
JavaScript

Kopier

var gitHubSignature = req.headers['x-hub-signature'];
Compare the two strings. If they match, process the request, as follows:
JavaScript

Kopier

if (!shaSignature.localeCompare(gitHubSignature)) {
    // Existing code
    if (req.body.pages[0].title) {
        ...
    }
    else {
        ...
    }
}
If the strings don't match, return an HTTP 401 (Unauthorized) response, with a message telling the sender that the signatures don't match.
JavaScript

Kopier

if (!shaSignature.localeCompare(gitHubSignature))
{
    ...
}
else {
    context.res = {
        status: 401,
        body: "Signatures don't match"
    };
}

Select Save.
The completed function should look like this:
JavaScript

Kopier

var Crypto = require('crypto');

module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var hmac = Crypto.createHmac("sha1", "<default key>");
    var signature = hmac.update(JSON.stringify(req.body)).digest('hex');
    var shaSignature =  `sha1=${signature}`;
    var gitHubSignature = req.headers['x-hub-signature'];

    if (!shaSignature.localeCompare(gitHubSignature)) {
        if (req.body.pages[0].title) {
            context.res = {
                body: "Page is " + req.body.pages[0].title + ", Action is " + req.body.pages[0].action + ", Event Type is " + req.headers['x-github-event']
            };
        }
        else {
            context.res = {
                status: 400,
                body: ("Invalid payload for Wiki event")
            }
        }
    }
    else {
        context.res = {
            status: 401,
            body: "Signatures don't match"
        }; 
    }
};
Update the webhook secret
Switch to your GitHub account in the GitHub portal.
Select your repository.
Select the Settings tab.
Select Webhooks in the navigation panel.
Select the Edit button next to your webhook.
In the Secret text box, enter the default key from your function that you saved earlier in this exercise.
At the bottom of the page, select Update webhook.
Test the webhook and the Azure Function
On the webhooks page, scroll down to the Recent Deliveries section.
Select the latest delivery entry by clicking the ellipsis (...) button.
Click Redeliver, and then select Yes, redeliver this payload.
This action simulates you changing your Wiki page again.
In the header, you'll see the x-hub-signature. You'll also see that the response code is 200, indicating that the request was processed successfully.
text

Kopier

Request URL: https://testwh123456.azurewebsites.net/api/HttpTrigger1?code=aUjXIpqdJ0ZHPQuB0SzFegxGJu0nAXmsQBnmkCpJ6RYxleRaoxJ8cQ%3D%3D
Request method: POST
content-type: application/json
Expect: 
User-Agent: GitHub-Hookshot/16496cb
X-GitHub-Delivery: ce122460-6aae-11e9-99d4-de6a298a424a
X-GitHub-Event: gollum
X-Hub-Signature: sha1=<hash of default key>
Test an invalid signature
In the GitHub portal, on the webhooks page, scroll up to the Secret test box and then select Edit.
Enter a random string, scroll down, and then select Update webhook.
The key used by the webhook should no longer match that expected by the Azure function.
Scroll down to the Recent Deliveries section.
Select the latest delivery entry by selecting the ellipsis (...) button.
Select Redeliver, and then Yes, redeliver this payload.
This time, you'll see that the response code is 401, indicating that the request was not authorized.
Select the Response tab, and verify that the message "Signatures don't match" appears as the body of the response.
