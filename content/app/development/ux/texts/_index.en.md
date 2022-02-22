---
title: Texts
description: How to edit and format texts, use variables and add help texts.
toc: true
weight: 40
---

Texts are stored in resource files in the directory `App/config/texts`. Texts can be from common libraries, the data model or manually added by developers.

The text resources are available whne you edit UI components in the schema via Altinn Studio, and they are shown to the end user when the form is loaded in a browser.

Texts are stored in JSON-format and one file per language. The filename format for texts should be `resource.[language].json` f.ex: _resource.nb.json_.

## Formatting of texts

All texts can be formatted with markdown. Below you can see the most commonly used syntax.

A more thorough explanation of the markdown syntax can be found here:
[Markdown Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet).

### Bold and italic texts

Its very easy to make words or sentences bold or italic with markdown.

```markdown
This is in  _italic_ using underscores.
This is also in *italic* using asterisk.
```

```markdown
This is in __bold__ using double underscores.
This is also in **bold**, using double asterisk.
```

### Links

```markdown
Click [here](https://altinn.no) to go to Altinn.
```

### Headings

```markdown
# This is a big heading (H1)
## This is a smaller heading (H2)
### An even smaller heading (H3)
#### A tiny heading (H4)
```

## Add and change texts in an application

You have two options when you want to modify texts in an application, either via Altinn Studio, changing it directly in the repository.

### Using Altinn Studio

In the top navigation bar in Altinn Studio, selec _Languages_ to be able to edit texts. An overview of the texts that already exists in the application are listed.

On this page you can edit the existing texts and add new ones. New texts can be added by clikcing the _New text_ button, and fill in the fields. The key needs to be unique.

Save the changes in the texts by clicking _Save texts_.

![Altinn Studio Designer](edit-texts-in-designer.png "Change texts in Altinn Studio Designer")

### Changing texts directly in the repository

This is the recommended way to update texts if there are many changes you want to make. The texts can be changed directly in the repository, or by cloning the repository and using your preferred text editor.

The texts are stored in  `App/config/texts`

![Altinn Studio Repos](edit-texts-in-repos.png "Change texts in Altinn Studio Repos")

## Variables in texts

Variables in texts can be included by following the syntax below. It is important that the order of the variables in the variables array is the same order as the parameters in the text.

```json
{
  "id": "good.text.id",
  "value": "Hello, {0}! Here is a second variable {1}.",
  "variables": [
    {
      "key": "<datamodelField>",
      "dataSource": "dataModel.<dataModelName>" 
    },
    {
      "key": "<settings key>",
      "dataSource": "applicationSettings"
    },
    {
      "key": "<instance value key>",
      "dataSource": "instanceContext"
    }
  ]
}
```
### Data sources

It is currently possible to fetch values from 3 different data sources.

1. Datamodell
   By defining `dataModel.<dataModelNavn>` as the data source you can fetch values from the fields in the form that the user is filling out. Data can be fetched from fields regardless if they are visible or not. If the user changes data in a field referenced in a variable, the text will be updated when the user blurs the field.
2. Application Settings   
   By defining `applicationSettings` as the data source you can fetch values from a specific section in `appsettings.{environment}.json` files with the key `FrontEndSettings`. This is a dynamic list you can extend without making changes to the code. This makes it possible to have different values in different environments. Be aware of the difference on first letter casing in the keys `FrontEndSettings` and `applicationSettings`.
   ```json
   "FrontEndSettings": {
     "HomeBaseUrl": "https://www.testdirektoratet.no"
   },
   ```
3. Instance
   This datasource is based on the instance and will contain some values from the active instance. We cannot access the entire instance object. The list of available properties is currently:
   1. `instanceOwnerPartyId` inneholder avgiver sin party id.
   2. `instanceId` inneholder id'en til den aktive instansen. 
   3. `appId` inneholder id'en til appen instansen er knyttet til.

### Complete example:

```json

{
  "id": "common.submitinfo",
  "value": "You are submitting for: {0} with organisation number: {1}. The organisations party id is {2}. [Link to our page]({3}).",
  "variables": [
    {
      "key": "skattepliktig.organisasjonsnavn",
      "dataSource": "dataModel.default"
    },
    {
      "key": "skattepliktig.organisasjonsnummer",
        "dataSource": "dataModel.default"
    },
    {
      "key": "instanceOwnerPartyId",
        "dataSource": "instanceContext"
    },
    {
      "key": "homeBaseUrl",
        "dataSource": "applicationSettings"
    }
  ]
}
```

### Variables in text - repeating groups
To make variables in text work with repeating groups, you need to make a slight modification to the example above.

This is achieved by adding `[{0}]` _after_ the repeating groups when you specify a field in the data model in the `key`.parameter
Example:
```json {hl_lines=[6,10]}
{
  "id": "common.submitinfo",
  "value": "You are submitting for: {0} with organisation number: {1}.",
  "variables": [
      {
        "key": "skattepliktig[{0}].organisasjonsnavn",
        "dataSource": "dataModel.default"
      },
      {
        "key": "skattepliktig[{0}].organisasjonsnummer",
        "dataSource": "dataModel.default"
      }
  ]
}
```

It is possible to combine variables from fields in repeating groups with variables from fields that are _not_ in repeating groups. It is not recommended to combine variables from fields in _different_ repeating groups, unless you are sure that the order of the items in the groups will be identical.

## Adding help texts

Help texts are short texts that gives a concrete description of what the end user is expected to fill in the field which the help text is connected to.

The keys that define the help texts are defined in `FormLayout.json`.
In the repository this file is located in `App/ui/`.

Below is an example of a _FormLayout.json_ without help texts.

```json
{
  "data": {
    "layout": [
      {
        "id": "616071dc-90b1-4ce5-8d18-492844828a41",
        "type": "Header",
        "componentType": 0,
        "textResourceBindings": {
          "title": "appName"
        },
        "dataModelBindings": {}
      },
      {
        "id": "08d707a9-2475-4d23-bf76-f209fb434ec2",
        "type": "TextArea",
        "componentType": 7,
        "textResourceBindings": {
          "title": "tilleggsopplysninger.label",
          "description": "tilleggsopplysninger.desc",
        },
        "dataModelBindings": {
          "simpleBinding": "omsetningsoppgaverTilleggsopplysninger.value"
        }
      },
      {
        "id": "bd6589b6-e2ab-49ba-b39a-dd3f8b63e5de",
        "type": "Button",
        "componentType": 9,
        "textResourceBindings": {
          "title": "Send inn"
        },
        "dataModelBindings": {},
        "textResourceId": "Standard.Button.Button",
        "customType": "Standard"
      }
    ]
  }
}
```

If you want to add help texts to any of these components you have to

1. Add the help text in the resource file [as described here](#legge-til-og-endre-tekster-i-en-app).
2. Open the `FormLayout.json`-file.
3. Add the binding to the new help text with the key `"help"` and value equal the newly added text resource.

This is how the file looks after adding help texts:

```json {linenos=false,hl_lines=[20]}
{
  "data": {
    "layout": [
      {
        "id": "616071dc-90b1-4ce5-8d18-492844828a41",
        "type": "Header",
        "componentType": 0,
        "textResourceBindings": {
          "title": "appName"
        },
        "dataModelBindings": {}
      },
      {
        "id": "08d707a9-2475-4d23-bf76-f209fb434ec2",
        "type": "TextArea",
        "componentType": 7,
        "textResourceBindings": {
          "title": "tilleggsopplysninger.label",
          "description": "tilleggsopplysninger.desc",
          "help": "tilleggsopplysninger.help"
        },
        "dataModelBindings": {
          "simpleBinding": "omsetningsoppgaverTilleggsopplysninger.value"
        }
      },
      {
        "id": "bd6589b6-e2ab-49ba-b39a-dd3f8b63e5de",
        "type": "Button",
        "componentType": 9,
        "textResourceBindings": {
          "title": "Send inn"
        },
        "dataModelBindings": {},
        "textResourceId": "Standard.Button.Button",
        "customType": "Standard"
      }
    ]
  }
}
```

## Change application title

When you create an application you have a textresource witht he label `appName`.
This it the title of the application that will be displayed in several places in our solution, f.ex when an end user is filling out a form and when elements are displayed in the messagebox in altinn.no.

The title of the application is located in two places in the application repository:
 1. In the textresource with the key `appName`. 
 2. In `applicationmetadata.json` in the `title` property. This file is located at `App/config/`.

 App owners are encouraged to add titles in bokmål, nynorsk and english. If a title is missing in the textresources the repository name will be used as a fallback, and may be shown to the end user.

If you change `appName` from an IDE (f.ex Visual Studio Code), it is important to also update the title in `applicationmetadata.json`. If the title is changed from Altinn Studio on the "about" page or "languages" page, the `applicationmetadata.json` file is updated automatically.

### Application title configuration example

In `App/config/applicationmetadata.json`:

```json
"title": {
    "nb": "Automatisk deploy applikasjonen",
    "nn": "Automatisk deploy applikasjonen",
    "en": "Auto deploy application"
  },
```

In `App/config/texts/resource.nb.json`:

```json
{
  "language": "nb",
  "resources": [
    {
      "id": "appName",
      "value": "Automatisk deploy applikasjonen"
    },
    .
    .
    .
  ]
}
```

In `App/config/texts/resource.nn.json`:

```json
{
  "language": "nn",
  "resources": [
    {
      "id": "appName",
      "value": "Automatisk deploy applikasjonen"
    },
    .
    .
    .
    ]
}
```

I `App/config/texts/resource.en.json`:

```json
{
  "language": "en",
  "resources": [
    {
      "id": "appName",
      "value": "Auto deploy application"
    },
    .
    .
    .
  ]
}
```

## Change app owner text

The application name and app owner is displayed at the top in the application.

![Tekster i appen](app-name-app-owner.png "Appnavn og appeier tekster")

The application owner is fetched by default from the texts defined in [altinn-orgs.json](https://github.com/Altinn/altinn-cdn/blob/master/orgs/altinn-orgs.json).
If you want to change this name, you can add the key `appOwner` in the text resources. This wil override the value coming from CDN.

Eksempel:
```json
{
  "language": "en",
  "resources": [
    {
      "id": "appOwner",
      "value": "Test Ministry"
    },
    .
    .
    .
  ]
}
```

## Change texts on the archive receipt page

The texts on the receipt page can be overridden by the application by specifying the texts in the applications `config/texts/resource.xx.json` file.

{{%notice info%}}
Overriding any text in the receipt will have an effect on any receipt for the given application. This means that forms that are already submitted will also get the updated texts on the receipt page. The generated PDF will not be affected by this.
{{% /notice%}}

You can also use markdown and variables in the receipt. Currently you can only fetch variables from `Instance` (See [Data Sources](#data-sources) for details)

These are the text id's that can be used to override the default platform texts:

```
receipt_platform.attachments
receipt_platform.date_sent
receipt_platform.helper_text
receipt_platform.is_sent
receipt_platform.receipt 
receipt_platform.receiver
receipt_platform.reference_number
receipt_platform.sender
receipt_platform.sent_content

```

For example if you want to change the help text, you can add this to the `config/texts/resource.en.json` file in your application:

```json
{
  "language": "en",
  "resources": [
    {
      "id": "receipt_platform.helper_text",
      "value": "My custom help text"
    }
  ]
}
```

The image below shows which key controls which text in the UI

![Texts and text keys](archive-receipt-texts.png "Texts and text keys")