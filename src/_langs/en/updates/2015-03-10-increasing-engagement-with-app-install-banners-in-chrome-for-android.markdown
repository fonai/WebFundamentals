---

layout: update
published: true

collection: updates
category: chrome
product: chrome
type: news
date: 2015-03-12

title: "Increasing engagement with Web App install banners"
description: "Web App Manifest ✔︎, Service Worker✔.Get ready for Web App Install banners "
article:
  written_on: 2015-03-12
  updated_on: 2015-03-12
authors:
  - paulkinlan
tags:
  - addtohomescreen
  - webapp
  - install
permalink: /updates/2015/03/increasing-engagement-with-app-install-banners-in-chrome-for-android.html
---
We recently enhanced the Add to Homescreen function in Chrome which allows users 
to add your Web App to their home screen with the addition of the 
standards-based "[web app manifest](http://updates.html5rocks.com/2014/11/Support-for-installable-web-apps-with-webapp-manifest-in-chrome-38-for-Android)". 
The manifest gives you extra control over the Add to Homescreen experience, 
allowing you to tell the browser what to launch, how to launch your app 
(fullscreen or in a browser window) and how it should appear to users on the 
home screen.

This improved things for users, but the ability to Add to Homescreen is still 
hidden behind a menu, meaning that your apps still aren't as discoverable as 
they should be.  To increase the chance of a user adding their app to the home 
screen a developer would have to try and guess if the site was already running 
as an Added to Homescreen app and if not, then tactically decide to give them an 
overlay that asked them to work around our poor UX.  This isn't great for users, 
and it is not good for developers.

In Chrome 42 (M42 - that is now in Beta) we are introducing "App Install 
Banners".  App Install Banners give you the ability to have your users quickly 
and seamlessly install your Web App as per the images below.

<p style="text-align: center;">
  <img style="max-width: 100%; height: auto;" src="{{site.baseurl}}/updates/images/2015-03-03/add-to-home-screen.gif" alt="IO Site with install banner" />
</p>

<span id="criteria">"This looks great, I want it on my site"</span> I hear you shout. "Please tell me how 
to add it!".  The good news is if you currently meet the following criteria Chrome will 
manage the prompting of users:

* You have a [web app manifest 
  file](http://updates.html5rocks.com/2014/11/Support-for-installable-web-apps-with-webapp-manifest-in-chrome-38-for-Android) 
  - The manifest defines how your app appears on the user's system and how it 
  should be launched - and you are required to have a \`short\_name\` and a 
  \`144x144\` png icon
  - Your icon declartion's should include a mime type of `image/png`
* You have a [service 
  worker](http://www.html5rocks.com/en/tutorials/service-worker/introduction/) 
  registered on your site. We recommend a [simple custom offline page](https://github.com/GoogleChrome/samples/blob/gh-pages/service-worker/custom-offline-page/service-worker.js) 
  service worker
* Your site is served over 
  [HTTPS](https://docs.google.com/document/d/1oRXJUIttqQxuxmjj2tgYjj096IKw4Zcw6eAoIKWZ2oQ/edit) 
  (you need a service worker after all)
* The user has visited your site twice over two separate days during the course 
  of two weeks.

**Note:** The criteria will change over time.  For more information read the [FAQ](#criteria-faq).

A sample manifest is [provided in our samples](https://github.com/GoogleChrome/samples/tree/gh-pages/app-install-banner) and one here for quick reference:

{% highlight javascript %}
{
  "short_name": "Kinlan's Amaze App",
  "name": "Kinlan's Amazing Application ++",
  "icons": [
    {
      "src": "launcher-icon-3x.png",
      "sizes": "144x144",
      "type": "image/png"
    }
  ],
  "start_url": "index.html",
  "display": "standalone"
}
{% endhighlight %}

If you are interested in the implementation, check out [crbug 
452825](https://code.google.com/p/chromium/issues/detail?id=452825).  If you are 
interested in tracking what other things we are working on in this space, follow 
the 
[Cr-UI-Browser-AppShortcuts](https://code.google.com/p/chromium/issues/list?q=label:Cr-UI-Browser-AppShortcuts) 
label. 


## <span id="cancel">Cancelling the prompt</span>

Chrome manages when to trigger the prompt and for some sites this might not be ideal.

As of Chrome 43 (Beta as of May 2015), you can now [cancel the prompt](http://googlechrome.github.io/samples/app-install-banner/cancelable-banner/index.html) by intercepting the `onbeforinstallprompt` event and preventing default on the event.

{% highlight javascript %}
 window.addEventListener('beforeinstallprompt', function(e) {
  console.log('beforeinstallprompt Event fired');
  e.preventDefault();
  return false;
});
{% endhighlight %}

I would say though, I am not sure why you would do this... But you can.

A more interesting future update will the ability to defer the prompt until later in the page lifecycle,
i.e, just after a user has performed an action, or hit the bottom of the page (something to indicate
that they are engaging with your site).

## <span id="action">Did a user install our web app</span>

A recetent addtion in Chrome 43 (Beta as of May 2015) is the ability to discern if the user clicked "Yes" or "No" to the App install banner.

The `beforeinstallprompt` event will return a promise called `userChoice` that will resolve when the user
actions the prompt.  The promise will return an object with a value of `dismissed` on the `outcome`
attribute or `accepted` if the user added the web page to the homescreen.

{% highlight javascript %}
window.addEventListener('beforeinstallprompt', function(e) {
  // beforeinstallprompt Event fired
  
  // e.userChoice will return a Promise. 
  // For more details read: http://www.html5rocks.com/en/tutorials/es6/promises/
  e.userChoice.then(function(choiceResult) {
    
    console.log(choiceResult.outcome);
    
    if(choiceResult.outcome == 'dismissed') {
      console.log('User cancelled homescreen install');
    }
    else {
      console.log('User added to homescreen');
    }
  });
});
{% endhighlight %}

This is a good tool for understanding how your users interact with your app install prompt.

## <span id="native">Native app install banner</span>

A new powerful feature for native app developers also landed in Chrome 44 Beta.  Native App install banners are similar to Web app install banners, but instead of adding to the Homescreen will let the user inline install your native app.

<p style="text-align: center;">
  <img style="max-width: 100%; height: auto;" src="{{site.baseurl}}/updates/images/2015-03-03/inlineinstall.gif" alt="Native app install banner" />
</p>

The criteria is similar to the Web App install banner except for the need of a Serivce Worker:

* You have a [web app manifest 
  file](http://updates.html5rocks.com/2014/11/Support-for-installable-web-apps-with-webapp-manifest-in-chrome-38-for-Android) 
  - The manifest defines how your app appears on the user's system and how it 
  should be launched - and you are required to have a \`short\_name\` and a 
  \`144x144\` png icon
  - Your icon declarations should include a mime type of `image/png`
* Your site is served over 
  [HTTPS](https://docs.google.com/document/d/1oRXJUIttqQxuxmjj2tgYjj096IKw4Zcw6eAoIKWZ2oQ/edit)
* The user has visited your site twice over two separate days during the course 
  of two weeks.

It is simple to integrate into any manifest.  Just add a `related_applications` array with the platforms of `play` (for Google Play) and the App Id, and then add `"prefer_related_applications": true` to always show the native app banner before the Web App Install banner.

{% highlight javascript %}
"prefer_related_applications": true,
"related_applications": [
  {
    "platform": "play",
    "id": "com.google.samples.apps.iosched"
  }
],
{% endhighlight %}

## <span id="criteria-faq">Frequently Asked Questions</span>
**My app meets all the criteria, but I don't want the banner to display. Can I control this?**  
Yes, you can prevent the banner from displaying.  See "[Cancelling the prompt](#cancel)".

**Can I detect if a user tapped "Add" at the prompt and added to home screen?**  
Yes you can, see "[Did a user install our web app](#action)".

**If a user dismisses the banner, will it appear again?**  
No.  Not unless the user clears their history.  We want to make sure users have 
a good experience.  We will likely be changing all the heuristics over time.

**Can I decide when to prompt the user****?**  
No, we are not letting developers actively prompt the user to Add to Homescreen.

**You said that I will only get the banner if I visit the site on two different 
days.  How on earth do I test it?**  
Yeah, we had the same problem when we were building this.  Enable 
chrome://flags/\#bypass-app-banner-engagement-checks and you will see it as long 
as you have a manifest (configured correctly) and are on HTTPS and have a 
service worker.

**Why do I need a service worker?**  
We believe that when you add to the user's home screen you should be providing 
an app-like experience.  A service worker, especially one that supports push 
messaging or offline, is a strong indicator that you are developing a first 
class device experience.  We recommend the following [simple Service Worker as a 
starting point](https://github.com/GoogleChrome/samples/blob/gh-pages/app-install-banner/basic-banner/service-worker.js). 

**Will the criteria for App Banner activation change over time?**
Yes.

**How will the criteria for App Banner activation change over time?**
We can't say specifics right now but as we better understand what makes an experience the user will want to install we will want to make sure the criteria are updated and also well understood for developers. 

**What could one of the critria for App Banner activation be?**
Again, it is hard to say.  One example could be: supporting offline scenarios in your app could indicate that your app is more resiliant to connectivity issues and therefore would offer a better launchable experience.

**Why do I need SSL?**  
Because you need a service worker, and we believe it is the best option for the future of the platform.

**Are there any good examples of this in action?**  
Yes, Glad you asked: 

* [Google I/O 2015 web app](https://events.google.com/io2015/)
* [Chrome Dev Summit](https://developers.chrome.com/devsummit/)
* [SVG OMG](https://jakearchibald.github.io/svgomg/) 
  [[Code](https://github.com/jakearchibald/svgomg)]
* [QR Snapper](https://qrsnapper.appspot.com) 
  [[Code](https://github.com/PaulKinlan/qrcode)]

**Examples are great, but do you have anything I can just copy and paste?**  
Yes. We have made a [minimal app install banner example](https://github.com/GoogleChrome/samples/tree/gh-pages/app-install-banner/) that you can copy, paste and change.
