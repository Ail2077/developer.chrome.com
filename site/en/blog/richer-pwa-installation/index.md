---
layout: 'layouts/blog-post.njk'
title: Richer PWA installation
description: Mobile devices and app stores have change how users discover, evaluate, and install software. PWAs need to keep up.
date: 2022-09-01 // Aspirational date
authors:
  - joemedley
tags:
  - capabilities
  - progressive-web-apps
hero: "image/xizoeLGxYNf3VLUHc5BsIoiE1Af1/H5CevJUnOWSewM02r4on.jpg"
alt: "New install surface for progressive web apps"
---

{% Aside 'caution' %}
This is an experimental UI, and could change in the future depending on developer, user, and partner feedback. We are planning to expand the UI to more clearly manage users' expectations around what installation means, and to provide users with additional signals for making a well informed decision regarding installing a particular application.
{% endAside %}

## Introduction {: #introduction }

The app stores provide by mobile vendors have changed how users discover, evaluate, and install software. Users are now so familliar with app stores and the information they provide&mdash;app context, social feedback, ratings, and etc.&mdash;that app store idioms are emerging on other platforms including ChromeOS, Mac, and Windows. In 2021, Chrome brought these idioms to Progressive Web Apps by adding new members to the [web app manifest](https://web.dev/add-manifest/#screenshots). Starting in Chrome 106, developers can provide different experiences for mobile and desktop. 
 
## Challenge with the previous install experience {: #today }

The older style of install prompt provided little information and context and is over too quickly. This didn't match users' expectations of what installation means and could leave them confused about what happened. Many declined the install request entirely, which was also bad for the businesses that built them. 

{% Columns %}
{% Column %}
<figure>
  {% Img src="image/xizoeLGxYNf3VLUHc5BsIoiE1Af1/VwB1V3K61vQMs1htKJpY.png", alt="Older PWA installation on mobile", width="360", height="720" %}
  <figcaption>Older PWA installation on mobile.</figcaption>
</figure>
{% endColumn %}
{% Column %}
<figure>
  {% Img src="", 
alt="Older PWA installation on desktop.", width="342", height="722" %}
  <figcaption>Older PWA installation on desktop.</figcaption>
</figure>
{% endColumn %}
{% endColumns %}

Richer installs let you create experiences more like those on operating systems. To do this, add a description and one or more screenshots to your web app manifest. As you can see below, this creates a more inviting and informative installation, 

{% Columns %}
{% Column %}
<figure>
  {% Img src="image/xizoeLGxYNf3VLUHc5BsIoiE1Af1/SpStAtUk8Zp5iwi9yqKP.jpg", 
alt="Richer Android install UI Expanded", width="342", height="722" %}
  <figcaption>Richer Android install UI expanded.</figcaption>
</figure>
{% endColumn %}
{% Column %}
<figure>
  {% Img src="image/xizoeLGxYNf3VLUHc5BsIoiE1Af1/k7r4yKqrh6iOm2XyZHfw.jpg", 
alt="Richer Android install UI Collapsed", width="342", height="722" %}
  <figcaption>Richer Android install UI collapsed.</figcaption>
</figure>
{% endColumn %}
{% endColumns %}

Although based on the same information, a desktop installation provides more space for the images. Desktop can take advantage of that space by provideing larger images. 

<figure>
  {% Img src="image/.jpg", 
alt="Richer desktop UI", width="342", height="722" %}
  <figcaption>Richer desktop UI.</figcaption>
</figure>

The rest of this article describes how to implement richer installations and where they're supported.

## Implementing richer installs {: #implementation }

to implement a richer installation add the `description` and `screenshots` properties to the web app manifest. 

### Description {: #description }

The `description` member describes the application in the installation prompt. Only seven lines of text are allowed in the description, which works out to about 324 characters. Longer descriptions are truncated and an ellipsis is appended ([for example](https://glitch.com/edit/#!/richerinstall-longer-description)). 

 ```json
"description": "Compress and compare images with different codecs 
right in your browser."
 ```

The description appears at the top of the installation prompt.

{% Columns %}
{% Column %}
<figure>
  {% Img src="image/xizoeLGxYNf3VLUHc5BsIoiE1Af1/oOj7Ls7cQ8E274faxfOz.jpg", 
alt="Description added", width="342", height="684" %}
  <figcaption>Description added.</figcaption>
</figure>
{% endColumn %}
{% Column %}
<figure>
  {% Img src="image/xizoeLGxYNf3VLUHc5BsIoiE1Af1/Dpzs03K6QmBkZaefX2nU.jpg", 
alt="A longer description that has been truncated.", width="342", height="684" %}
  <figcaption>Longer descriptions are truncated.</figcaption>
</figure>
{% endColumn %}
{% endColumns %}

{% Aside 'caution' %}
You may have noticed from the screenshots that installation dialogs al;so list the app's Origin. Origins that are too long to fit the UI are truncated, this is also known as eliding and is used
as a [security measure to protect users](https://chromium.googlesource.com/chromium/src/+/master/docs/security/url_display_guidelines/url_display_guidelines.md#eliding-urls). 
{% endAside %}

### Screenshots {: #screenshots }

Although a richer installation needs a description, you can't really call it 'richer' without the [`screenshots` member](https://web.dev/add-manifest/#screenshots) to the web app manifest. It takes an array that requires at least one member and can have up to eight. An example is shown below. 

```json
 "screenshots": [
    {
     "src": "source/image1.gif",
      "sizes": "320x640",
      "type": "image/gif",
      "platform": "wide",
      "label": "Wonder Widgets"
    }
]
```

In practice that produces something like this:

<figure>
  {% Img src="image/xizoeLGxYNf3VLUHc5BsIoiE1Af1/7jsL23QoMfniU7WTHDK8.jpg", 
alt="A single screenshot added.", width="342", height="684" %}
  <figcaption>A single screenshot added.</figcaption>
</figure>

Screenshots must follow these criteria: 

* Width and height must be at least 320px and at most 3840px.
* The maximum dimension can't be more than 2.3 times as long as the minimum dimension.
* All screenshots must have the same aspect ratio.
* Only JPEG and PNG image formats are supported.

Currently animated gifs are not supported. Also, you need to include the size and type of the image so it 
is rendered correctly. [See this demo](https://glitch.com/edit/#!/richerinstall-screenshot?path=manifest.json%3A14%3A24).

Most of these properties have been supported since Chrome 91; however, the `platform` property is new as of Chrome 106/however the `platform` member is not yet supported. We hope to land it in Chrome xxx. The first implementation of `platform` has two possible values: `wide` for desktop installations; `narrow` for mobile installations. We hope to expand that number in the future to cover specific platforms and distribution channels.

The `platform` is optional because you should only use it when the layout is varies by screen size. The same will apply to platform and channel specific values in the future. 

### Wrapping it all up

If you add the new members, your web app manifest might look the one below. This is just to give you a sense of context. The actual manifest for the [Squoosh App](https://squoosh.app/) is larger. Before you run off to install it, you'll want to turn on the flags that enable the new installation experince. Otherwise you won't see it.

```json
{
  "name": "Squoosh App",
  "icons": [{
  "src": "image/icon.png",
        "sizes": "512x512",
        "type": "image/png"
      }],
  "start_url": "/?start_url",
  "scope": "/",
  "display": "standalone",
  "background_color": "#fff",
  "theme_color": "#fff",
  "description": "Compress and compare images with different codecs 
right in your browser.",
  "screenshots": [
    {
      "src": "source/image1.gif",
      "sizes": "320x640",
      "type": "image/gif",
      "platform": "wide",
      "label": "Wonder Widgets"
    }
  ]
}
 ```

## Current status {: #status }

The existing prompts will be shown for web apps that do not update the web app manifest. This may change in the future depending on devloper uptake and users' reactions.

<div>

| Step                                     | Status                   |
| ---------------------------------------- | ------------------------ |
| 1. Create explainer                      | [Complete](https://github.com/w3c/manifest-app-info/blob/main/explainer.md)    |
| 2. Create initial draft of specification | [Complete](https://www.w3.org/TR/manifest-app-info/)              |
| 3. Gather feedback & iterate on design   | [In progress](#feedback) |
| 4. Launch                                | Not started              |

</div>

## Previewing richer installs {: #previewing }

To enabled the new web app manifest members, go to `chrome://flags` and set the following flags to 'Enabled'.

* Set `#mobile-pwa-install-use-bottom-sheet` to enable the `description` and `screenshots` properties. This works in Chrome 91 or later. 

* To `[TBD]` to enabled the `platform` properties. This works in Chrome xxx or later. 

This UI works in Chrome 91 or later on Android, with the flag `#mobile-pwa-install-use-bottom-sheet` enabled in `chrome://flags`.

## Looking forward {: #looking-forward}

In the future we will consider adding other data such as categories and app rating, but this will 
be based on feedback from developers and users. 
[See the demo](https://glitch.com/edit/#!/richerinstall-description?path=manifest.json%3A13%3A29).

## Feedback  
In the coming months we would love to see how developers explore this new UI pattern and we 
would like to get feedback from you. Reach out to us on 
[Twitter](https://twitter.com/ChromiumDev). 
