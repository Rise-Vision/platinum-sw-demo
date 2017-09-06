# Platinum Service Worker Elements 

A service worker is an event-driven background task that acts as a proxy server between web applications, the browser, and the network. There are many uses for service workers and this demo will focus on service workers as a strategy for offline experience. For an introduction on [Service Worker API](https://developer.mozilla.org/en/docs/Web/API/Service_Worker_API) visit [Service Workers: an Introduction](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers)

The [Platinum Service Worker elements](https://github.com/PolymerElements/platinum-sw) are a set of Polymer web components that enable web designers with the ability to simplify service worker registration and caching, powered by the
[`sw-toolbox` library](https://github.com/googlechrome/sw-toolbox). 

The `<platinum-sw-cache>` element is used to precache specific resources, perform runtime caching, and serve your cached resources when a network is unavailable. It is a simple and effective strategy to have all of the resources your site uses cached at runtime, both local and cross-origin. For full documentation visit [`platinum-sw`](https://polymerelements.github.io/platinum-sw/components/platinum-sw/)

## Required

### Serve via `HTTPS`
For security reasons, service workers only run on pages served over HTTPS. In local development service workers will register and run through `localhost`, but to deploy it on a site you'll need to have HTTPS setup on your server.

### sw-import.js
The `<platinum-sw-register>` element needs to register a JavaScript file called `sw-import.js` located at the top-level of your site's web page root. The file contents should be:

```
importScripts('bower_components/platinum-sw/service-worker.js');
```
You can adjust the path to `service-worker.js` if your project has its Polymer elements installed somewhere other than `bower_components/`. 

See [here](https://github.com/PolymerElements/platinum-sw#considerations) for additional considerations when using Platinum SW components.

## Steps


### Step 1: Install platinum-sw package

To use platinum-sw elements you first need to install the platinum-sw package. You can install it through [Bower](https://bower.io/) using the following command:

```
bower install platinum-sw --save
```

After running this command you should see your `bower_components` folder contain all of the files you will need to use in your page.

### Step 2: Modify HTML `<head>` 

In your web page you should include the `webcomponents-lite.min.js` polyfill to provide backwards compatible browser support of Web Components.  Then load the `platinum-sw-elements` web component using HTML imports:

```
 <head>
	...
	<script src="bower_components/webcomponentsjs/webcomponents-lite.min.js"></script>
  	<link rel="import" href="bower_components/platinum-sw/platinum-sw-elements.html">
	...
</head>
```

### Step 3: Modify HTML `<body>`

As this demo is focused on caching, you're going to use the `platinum-sw-cache` element in your page to configure how service workers should manage caching your resources. 

First, `<platinum-sw-register>` is required to be the parent element which handles registering the service worker to control the page, based on the configuration provided via its `<platinum-sw-*>` children.

You can use the following attributes to configure the registration behavior:

- `skip-waiting` and `clients-claim` work together to ensure that the service worker you register controls your pages as soon as possible.
- `auto-register` means that the registration will be performed automatically by virtue of including the element in your page.
- `reload-on-install` will cause your web page to reload itself the very first time your service worker is installed. This will normally not be noticeable to your users, and ensures that the runtime caching the service worker performs will get a chance to cache all of the page's resources, even those that were already loaded before the service worker was installed.

Next you use `platinum-sw-cache` as a child element to configure the caching behavior. One particularly useful attribute is `precache` which you can use to provide a list of URLs that are always precached as soon as the service worker is installed. This is useful for URLs that that wouldn't necessarily be picked up by runtime caching. 


Below is the configuration used in the accompanying demo in `index.html`.

```
<body>
	...
	<platinum-sw-register
  		auto-register
  		clients-claim
  		skip-waiting
  		reload-on-install>
  			<platinum-sw-cache
    			default-cache-strategy="networkFirst"
    			precache='["images/image-four.jpg"]'></platinum-sw-cache>
	</platinum-sw-register>
	...
</body>
```

See [here](https://polymerelements.github.io/platinum-sw/components/platinum-sw/) for full documentation on attributes to configure. 


### Step 4: Upload Files

Once the basic HTML is in place you can upload your files to your web server. To run the demo provided in this repo, you can upload the following files to a directory on your server:

- index.html
- sw-import.js
- bower_components/
- css/
- fonts/
- images/

**Reminder** - Make sure your server has HTTPS setup  

### Step 5: Inspect via Dev Tools


For testing purposes open a new Incognito Window in Chrome. When you open in Incognito, no previous service workers persist and no cache storage persists either. This is a good way to test out a first time install of your service worker. 

Open Developer Tools and choose the Application tab. In the left panel choose Service Workers. You should see your service worker installed, activated, and running, similar to the below:

![Screenshot 1](https://content.screencast.com/users/stulees/folders/Snagit/media/475ab536-8309-4ad7-9a61-893bc45b9d90/2017-04-06_17-55-40.png)

Now in the left panel choose the Cache Storage with your service worker identifier. This is where you can inspect all your files that have been successfully cached. For example, when running the demo it should look like the below:

![Screenshot 2](https://content.screencast.com/users/stulees/folders/Snagit/media/f41863ce-b7b3-453d-be95-f576af727533/2017-04-06_18-04-20.png)

For more detailed information on debugging service workers visit [Debugging Service Workers](https://developers.google.com/web/fundamentals/getting-started/codelabs/debugging-service-workers/)

### Step 6: Go Offline


Now try turning off your network and reloading the page. You should see your page running offline the same as it did with network. If you configured precaching any resources, they should be served via your service worker. 

### Step 7: Rise Vision Presentations & Scheduled Items

When you've confirmed your service workers are registering and activating correctly in your web page, the page is ready for use in a Rise Vision presentation. Whether you target your page in a scheduled playlist item (URL) or via Web Page Widget in a presentation, the service workers will register and activate as expected. However, please note the following when testing:

- When testing in Preview from the Apps Editor, service workers will not register and activate. This is because the Preview page is not served over https and must be served over http. You should test your web page directly in the browser as mentioned in Steps 5 & 6 to confirm your service worker setup is working correctly.
- When testing in Rise Player, service workers will register and activate as expected. 


