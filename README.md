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
