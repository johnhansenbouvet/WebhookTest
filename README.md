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
