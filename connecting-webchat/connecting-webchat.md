---
title: Connecting to Webchat
duration:
---

--sep--
---
title: Introduction
duration: 5
---

# Introduction

In this tutorial, you will learn how to connect your Botfuel chatbot with Botfuel Webchat and how to integrate the chat in your website.

If you don't already have a Botfuel App, please follow the installation part of our <a href="/#/codelab/getting-started" target="_blank">Getting Started tutorial</a> and make sure you have located your app’s credentials `App Id`, `App Key` and `App Token`. We will use them in this tutorial.

## You will learn
* Test your bot in the Webchat
* Add the Webchat to your website

## You will need
* Have a bot developped using botfuel dialog
* A recent web browser
* NodeJS installed on your computer
* ngrok installed on your computer

--sep--
---
title: Set up your bot
duration: 4
---

# Set up yout bot

To tell the chatbot we want to use the Botfuel Webchat adapter, let’s add a `config.js` file at the root level of your bot with the following content:

```javascript
module.exports = {
  adapter: {
    name: 'botfuel',
  },
};
```

Start the bot with the `BOTFUEL_APP_ID`, `BOTFUEL_APP_KEY` and `BOTFUEL_APP_TOKEN` environment variables and the `config.js` file you just created:

```shell
BOTFUEL_APP_TOKEN=<the BOTFUEL_APP_TOKEN> BOTFUEL_APP_ID=<the BOTFUEL_APP_ID> BOTFUEL_APP_KEY=<the BOTFUEL_APP_KEY> npm start config.js
```

If you use Windows Powershell, use this command instead:

```shell
$Env:BOTFUEL_APP_TOKEN = <the BOTFUEL_APP_TOKEN>; $Env:BOTFUEL_APP_ID=<the BOTFUEL_APP_ID> ; $Env:BOTFUEL_APP_KEY = <the BOTFUEL_APP_KEY> ; npm start config.js
 ```

If you set your app credentials and the config file correctly, you should see:

```shell
2018-02-16T15:37:06.255Z - info: [Environment] BOTFUEL_APP_TOKEN=<the BOTFUEL_APP_TOKEN>
2018-02-16T15:37:06.258Z - info: [Environment] BOTFUEL_APP_ID=<the BOTFUEL_APP_ID>
2018-02-16T15:37:06.258Z - info: [Environment] BOTFUEL_APP_KEY=<the BOTFUEL_APP_KEY>
2018-02-16T15:37:06.395Z - info: [WebAdapter] run: listening on port 5000
```

It means your bot is listening to new user messages on `http://localhost:5000`, so let’s connect Botfuel Webchat to it!

--sep--
---
title: Configure the Webchat
duration: 7
---

# Configure the Webchat

Go to the Webchat configuration page on the <a href="https://app.botfuel.io/" target="_blank">Botfuel Developer Portal</a>:

<center>
<img src="https://github.com/Botfuel/tutorials/raw/master/connecting-webchat/images/webchat-config.png" alt="Webchat configuration tab" />
</center>

You have two fields to configure on the right panel:
* Bot endpoint
* Allowed origins

The `Bot endpoint` field should have the URL to which the webchat will be sending the user messages. In our case, our bot is running on `http://localhost:5000`. This is problematic, since `localhost` cannot be accessed by the Webchat. So we’ll expose the endpoint to the outside by using <a href="https://ngrok.com/download">ngrok</a>.
If you’re interested in more details, see <a href="https://medium.com/botfuel/how-to-expose-a-local-development-server-to-the-internet-c31532d741cc" target="_blank">this article on the subject</a>
On production, you would host your bot on your own server or use services like Heroku (<a href="https://tutorials.botfuel.io#/codelab/deploy-heroku?step=1" target="_blank">see how here</a>).

Follow the <a href="https://ngrok.com/download">instructions</a> for your operating system, then run the following command:

```shell
ngrok http 5000
```

This will give you a screen like this one:

<center>
<img src="https://github.com/Botfuel/tutorials/raw/master/connecting-webchat/images/ngrok.png" />
</center>

Locate the forwarding link (in our case `https://3efeb72c.ngrok.io`). All requests to `https://3efeb72c.ngrok.io` will be forwarded to your bot.

Set the Bot endpoint field to `<NGROK_URL>/webhook`:
<center>
<img src="https://github.com/Botfuel/tutorials/raw/master/connecting-webchat/images/webchat-how-endpoint.png" alt="Webchat endpoint">
</center>

Make sure that you added `/webhook` at the end of the URL.

The `Allowed origins` field is a list of websites that can display the Botfuel Webchat using your chatbot. Set this field to `*` for quick prototyping.

<center>
<img src="https://github.com/Botfuel/tutorials/raw/master/connecting-webchat/images/webchat-how-origins.png" alt="Webchat allowed origins">
</center>

Then click on `Save`.

## Test in the Webchat

You can check that your bot is properly connected to the Webchat by going to the Webchat Playground and sending some messages:

<center>
<img src="https://github.com/Botfuel/tutorials/raw/master/connecting-webchat/images/webchat-starter-conversation.png" alt="Webchat starter conversation">
</center>

--sep--
---
title: Add the Webchat to your webpage
duration: 5
---

# Add the Webchat to your webpage

On the Webchat page, click on the `Code`tag at the top of the page to get the code snippet to include in youyr webpage

<center>
<img src="https://github.com/Botfuel/tutorials/raw/master/connecting-webchat/images/webchat-snippet.png" alt="Webchat snippet">
</center>

Copy this code by clicking on the icon in the upper right corner and paste it in the HTML page of your website where you want to display the Webchat.

For example, for a basic page, the resulting code would be:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">
  </head>
  <body>
    Webchat
    <script src="https://cdn.jsdelivr.net/npm/botfuel-webchat-client@3.9.1"></script>
    <script>
      BotfuelWebChat.init({
      appToken: 'THE_BOTFUEL_APP_TOKEN',
      size: {
          width: 500,
          height: 600
      },
      startOpen: false,
      startFullScreen: false,
      theme: {
          colors: {
            background: '#ffffff',
            main: '#244891',
            primary: '#0084f4'
          },
          layout: {
            compact: false,
            rounded: false,
            shadowed: false,
            noHeader: false,
            noBorder: false,
            noHelpMessage: false
          }
      }
    });
    </script>
  </body>
</html>
```

If you visit your website in a browser, the Webchat will appear in the bottom right corner (as a bubble iconor already open, depending on the `startOpen` option).

<center>
<img src="https://github.com/Botfuel/tutorials/raw/master/connecting-webchat/images/webchat-website.png" alt="Webchat website integration">
</center>

--sep--
---
title: Congratulations
duration: 1
---

# Congratulations!

You have reached the end of this tutorial and have set up the Botfuel Webchat on your website and connected it with your chatbot!

Learn More:
* Deploy your bot on Facebook Messenger with <a href="/#/codelab/connect-messenger" target="_blank">this tutorial</a>