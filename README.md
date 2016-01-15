Jira Notifications for Slack
============================
This is a small nodejs+restify server that receives Jira webhooks and dispatches them to Slack. It supports multiple projects/rooms and is easy to setup on Heroku.

The events dispatched to slack are these:

* New issue created
* Issue transitioned to another state
* Comment added or modified

## Installing on Heroku
Simply clone the repository and push it to heroku, we have included a Procfile for a two command setup:

    cd <repository folder>
    heroku create
    git push heroku master

## Creating a new Outgoing Hook on Jira
Webhooks on Jira works globaly. You most likely will want to setup this on a per-project basis if your Jira activity is too high, otherwise you can add a single webhook for all your projects.

 1. Go to Administration > Advanced > Webhooks
 2. Click on "Create a Webhook" over the top-left corner of the screen
 3. Select the "Issue Update" and "Issue Created" events
 4. Insert your application URL. The receiving endpoint is at `/api/jira`, so if your application is being served from `my-domain.com` it would look like `http://my-domain.com/api/jira`.
 5. (Optional) Add a JQL for your project like `project = "My Project"`

## Creating a new Incoming Hook on Slack
On Slack, you must create a new incoming webhook for each project you want to receive these notifications.

  1. Go over to https://my.slack.com/services/new/incoming-webhook
  2. Select the room you would like to receive events and press "Add Incoming Webhook"
  3. Copy your Unique Webhook URL and add it to the configuration (next sections explains how)
  4. (Optional) Change the name for the sender in the "Integration Settings" section. Otherwise it will show up as "incoming-webhook"
  5. (Optional) Update the icon for the sender also in the "Integration Settings" section. For your convenience, you can download a Jira icon from here: http://i.imgur.com/1MtFW8I.png

## Configuring using environment variables
You can set up each new project by using two enviroment variables:

    <Jira project key>:JIRA_DOMAIN=<mycompany>.atlassian.net
    <Jira project key>:SLACK_URL=https://<mycompany>.slack.com/services/hooks/incoming-webhook?token=<your webhook token>

For settings this up on heroku for a given "MP" project key, you would do:

    heroku config:set MP:JIRA_DOMAIN=mycompany.atlassian.net
    heroku config:set MP:SLACK_URL=https://mycompany.slack.com/services/hooks/incoming-webhook?token=zAHRtMv6BN2EDFx2nx4ZhMYr

For new projects just repeat the steps above. Note the JIRA_DOMAIN can point to custom Jira instalations too, not just OnDemand ones.

## Configuring using a JSON config file
You can also use a `secret.config.json` on the project if you don't like the enviroment variable approach. If you are deploy this over a git hook (like on Heroku) you must add it in a commit before pushing.

It uses the same format as the enviroment variable:

    {
      "MP:SLACK_URL": "https://mycompany.slack.com/services/hooks/incoming-webhook?token=zAHRtMv6BN2EDFx2nx4ZhMYr",
      "MP:JIRA_DOMAIN": "mycompany.atlassian.net"
    }
