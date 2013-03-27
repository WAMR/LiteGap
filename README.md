LiteGap - PhoneGap and Couchbase Lite for iOS
========

Container for building HTML5 mobile apps with PhoneGap and Couchbase Lite, which can sync via [Couchbase Mobile](https://github.com/couchbaselabs/mobile)

Step one is to build and install the CouchbaseLite.framework and CouchbaseLiteListener.framework into the Frameworks/ folder that is part of this project.

(TouchDB for Android should be fairly easy to use with PhoneGap. If anyone creates a similar container for Android, send a pull request linking to it from here.)

## How to develop

You edit `www/`,  and save JSON documents to Couchbase Lite via local Ajax calls. which sync via the cloud to other mobile devices and users. Data on the local device means a snappy user experience. Sync via the cloud adds opportunties for multiuser interaction.

Here's what saving a form entry to the database could look like in your app:

```javascript
$(function(){
	$("form").submit(function() {
		$.ajax("http://lite.couchbase./mydb", {
			dataType : "json",
			type : "POST",
			data : $(this).serializeArray(),
			success : function(meta) {
				console.log("saved your document "+ meta.id);
			}
		});
		return false;
	});
});
```

## Learn More

One place to start is [Chris' recent blog posts](http://blog.couchbase.com/j) on the Couchbase blog. Or look at [CouchChat](https://github.com/couchbaselabs/CouchChat-iOS) if you just want a clean example.

NOTE: We are updating the examples to the latest Sync Gateway capabilities, so for now we've left master branch clean as something that works but doesn't do much. You can use Ajax libraries like jQuery to save data by posting JSON to `http://lite.couchbase./mydb/` (you need to add this to your whitelist.)

## Try the examples

If you want to see an up to date example app, look at [CouchChat](https://github.com/couchbaselabs/CouchChat-iOS). We are updating the HTML5 examples now, contact @jchris on Twitter to help...

There is an example [HTML5 version of the Chat app in it's own repository](https://github.com/couchbaselabs/CouchChat-PhoneGap). To try it out you can bring the code in by pulling in submodules (this project only uses them for example code, so they are optional if you are writing your own app.)

	git submodule init
	git submodule update

Once you have done that, you need to symlink the code into place and setup an additional build step to manage packaging the JavaScript. Simply follow these steps:

* Remove the existing directory from `$PROJECT_ROOT/www`
* Make a symlink so that the `$PROJECT_ROOT/examples/CouchChat-PhoneGap/www` directory shows up at `$PROJECT_ROOT/www/`. You can do this by running `ln -s examples/CouchChat-PhoneGap/www www`
* Note that the app code needs to be bundled by [Browserify](http://browserify.org/) before you can build. A pre-bundled version is included as `output.js`, but if you touch any of the app JavaScript, your changes won't show up until you run `examples/CouchChat-PhoneGap/bundle.js`. For this reason, you are encouraged to add a "run Script" Build Phase to your app target, and have it run something like this: `/usr/local/bin/node ${PROJECT_DIR}/examples/CouchChat-PhoneGap/bundle.js` (use `which node` to find where node lives.) Xcode doesn't get access to your shell ENV, so you need to use the absolute path to node. *The Xcode project already contains a script to bundle CouchChat-PhoneGap, if you are writing your own app using browserify you should modify it to point to your code, otherwise you can ignore it.*
* Now you are ready to build the app and check it out on your simulator.

Follow the rest of these steps to enable sync between multiple devices and the cloud.

* Install [Couchbase Server 2.0 or newer](http://www.couchbase.com/couchbase-server/overview)
* Install the [Couchbase Sync Gateway](https://github.com/couchbaselabs/sync_gateway) somewhere your mobile devices will be able to access it. This can be on your workstation, if you are only testing on the simulator or with devices connected via local wi-fi. For deployments, this Sync Gateway need to be reachable from the public internet.
* Follow the steps to [configure the Sync Gateway for the CouchChat-iOS app](https://github.com/couchbaselabs/CouchChat-iOS). The whole point of CouchChat-PhoneGap is to show how two different clients can interact on the same data set. So we have the config file you'll need in that repo.

Once you have the components installed, launch them in this order:

* Couchbase Server
* the Sync Gateway

You will need to edit the file at `$PROJECT_ROOT/www/js/app/config.js` to replace "animal.local" with the hostname at which your iOS device or Simulator should look to find the Sync Gateway. You can look at `config.js` to see that we expect these services to run on the default ports. Feel free to edit stuff there to fit your deployment.

Now that you have edited the example app to look in the right place on the network, build and run it from Xcode.

You can test to see that stuff is indeed synced to Couchbase Server, by quitting the app in the simulator, deleting it, and then re-launching it from Xcode. If you sign in with the same [Mozilla Persona](https://login.persona.org/) as you did the first time around, then a few seconds later you should see any chat rooms you created the first time around, reappear on the screen.

Or you could run it on multiple devices, with the same credentials. If you do that, you should see changes flow between the devices as they are made.

## The Tech

The project uses Apache Cordova as a base layer, with the goal to abstract the Couchbase Lite integration to a Cordova Plugin. For now, someone will have to update Couchbase Lite and Cordova manually in this project. Once the plugin integration is done, these example apps should only need to include a `www/` folder, and work with boilerplate Cordova and TouchDB.


## Contribute

Right now we need big help in packaging the Objective-C bits as a plugin, so that it can be used on PhoneGap Build. [Join our mailing list](https://groups.google.com/forum/#!forum/mobile-couchbase) to help out.



