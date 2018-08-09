---
title: Identify vehicle registration plates with the RegexExtractor
duration:
---

--sep--
---
title: Introduction
duration: 3
---

# Introduction

Botfuel natively supports 31 built-in entities such as `forename`, `location`, `duration` or `url`. You can also create your own dictionary of custom entities using the <a href="https://docs.botfuel.io/dialog/reference/entities/custom-entities" target="_blank">CorpusExtractor</a> class.
However, you may want to use an extractor based on string scheme.

In this tutorial, you will learn how to create an extractor for French licence plates using a RegexExtractor

## What you will need
* Have completed the <a href="/#/codelab/getting-started" target="_blank">Getting Started tutorial</a>
* A very basic understanding of Regular expression (but don't panic, we said very basic!)

--sep--
---
title: The French vehicle registration system
duration: 4
---

# The French vehicle registration system

Shorted SIV for <i>système d'immatriculation des véhicules</i>, the French vehicle registration system is used by all cars registered since 2009.

<center>
<img src="https://github.com/Botfuel/tutorials/raw/master/regexp-imatriculation/images/licence-plate.png" alt="Regex explanation" title="Regex explanation"/>
</center>

<aside class="infos">
<b>Note:</b> Under the SIV system, registration plates contain seven alphanumeric characters: two letters, a dash, three numbers, a dash and two letters, such as AA-229-AA. The system is nationwide and chronological. The first car registered in France under the SIV received a AA-001-AA registration plate, the second one AA-002-AA, the third AA-003-AA. The system will be exhausted when ZZ-999-ZZ is reached, which is scheduled to occur after 80 years of use.

You can find more information on <a href="https://en.wikipedia.org/wiki/Vehicle_registration_plates_of_France#SIV" target="_blank">wikipedia</a>
</aside>

This SIV system makes is very easy to identify wherease a string is a licence plate. However, as mentioned in wikipedia `The SIV format provides ((23 x 23) - 2) x 999 x ((23 x 23) - 1), or 277,977,744 different combinations`. Therefore, we don't recommand you build a corpus of entities with every possible combination...

--sep--
---
title: The RegexExtractor
duration: 8
---

# The RegexExtractor

Botfuel provides a RegexExtractor that you can use to easily extract paterns in a sentense. In our case, we want to extract any string that look like `AA-999-AA`.

## Build the regex

We mentioned in the introduction that you would neede a basic understanding of regular expressions to do this tutorial. But don't worry, the regex we are going to build here is very simple.

<center>
<img src="https://github.com/Botfuel/tutorials/raw/master/regexp-imatriculation/images/regex.png" alt="Regex explanation" title="Regex explanation"/>
</center>

Our licence plate is separated in 5 sections :
1. `AA`: Here, we use `[A-Za-z]{2}` to match any combinaison of two letters between ranges `A` and `Z` or `a` an d `z` as we don't want our extractor to be case sensitive. We want to extract the licence plate even if the user sends us one in lowercase.
2. `-`: A literal dash character.
3. `999`: We use `[0-9]{2,3}` as we want to match any combination of 2 or 3 digits. (Scooters use 2 letters).
4. `-`: Another literal dash character.
5. `AA`: The last section is the same as the first one.

Once we have our regular expression to extract a licence plate, we can use is in our extractor

## Use it in the RegexExtractor

Create a new file called `immatriculation-extractor.js` in the `extractors` folder containing the code below:

```javascript
const { RegexExtractor } = require('botfuel-dialog')

class ImmatriculationExtractor extends RegexExtractor {
  constructor() {
    super({
      dimension: 'immatriculation',
      regex: /[A-Za-z]{1,2}-[0-9]{2,3}-[A-Za-z]{1,2}/
    })
  }
}

module.exports = ImmatriculationExtractor
```

As you can see, creating a new RegexExtractor is very easy. You just need to specify a `dimention` that is going to be used in your dialog and the regex you want to match to extract your entities.

--sep--
---
title: Use the extractor
duration: 5
---

# Use the extractor

## Trainer

Got to <a href="https://app.botfuel.io" target="_blank">https://app.botfuel.io</a>, open your project and create a new intent called `licenceplate`

<center>
<img src="https://github.com/Botfuel/tutorials/raw/master/regexp-imatriculation/images/trainer.png" alt="intent in trainer" title="Intent in trainer"/>
</center>

Add a few training phrases the user may enter to give you his licence plate.

## Create the Dialog

Go in you dialogs folder and create a new dialog called `licenceplate-dialog.js` with the following code:

```javascript
const { PromptDialog } = require('botfuel-dialog');

class LicencePlateDialog extends PromptDialog {}

LicencePlateDialog.params = {
  namespace: 'immatriculation',
  entities: {
    licenceplate: {
      dim: 'immatriculation',
    },
  },
};

module.exports = LicencePlateDialog;
```

Here, you just specified that the `LicencePlateDialog` will have an entity of dimention `immatriculation`. The key `licenceplate` is used in your dialog and view to retrieve the entity.

## Create the View

You can use your entity in your view the same way you use any other entity in Botfuel

Goo in you views folder and create a new dialog called `licenceplate-view.js` with the following code:

```javascript
const { PromptView, BotTextMessage } = require('botfuel-dialog');

class LicencePlateView extends PromptView {
  render(userMessage, { matchedEntities }) {
    const licencePlate = matchedEntities.licenceplate && matchedEntities.immatriculation.values[0].value;

    if (licencePlate) {
      return [
        new BotTextMessage(`Thanks, you licence plate is ${licencePlate.toUpperCase()}.`),
      ];
    }

    return [new BotTextMessage(`Sorry, I did not understand your licence plate.`)];
  }
}

module.exports = LicencePlateView;
```

## Test

Start the bot using your app’s credentials:

```bash
BOTFUEL_APP_TOKEN=<the BOTFUEL_APP_TOKEN> BOTFUEL_APP_ID=<the BOTFUEL_APP_ID> BOTFUEL_APP_KEY=<the BOTFUEL_APP_KEY> npm start
```

You can then try your new intent in your bot.
<center>
<img src="https://github.com/Botfuel/tutorials/raw/master/regexp-imatriculation/images/terminal.png" alt="Bot test in terminal" title="Bot test in terminal"/>
</center>

--sep--
---
title: Congratulations
duration: 1
---

# Congratulation

You have reached the end of this tutorial. The `RegexExtractor` opens endless possibilities to create custom extractors.

## <i class="fas fa-heart"></i> Support us

Did you have a good time using Botfuel to build Chatbots? You can support our developers by staring our open source SDK on <a href="https://github.com/Botfuel/botfuel-dialog" target="_blank">Github <i class="fab fa-github"></i></a>

## Learn more

* The SDK <a href="https://docs.botfuel.io/" target="_blank">documentation</a>
* Deploy your chatbot on Heroku <a href="https://tutorials.botfuel.io/#/codelab/deploy-heroku?step=1" target="_blank">this tutorial</a>
* Deploy your chatbot on Facebook Messenger with <a href="https://tutorials.botfuel.io/#/codelab/connect-messenger?step=1" target="_blank">this tutorial</a>