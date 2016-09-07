<a name="Chapter3"></a>
# Chapter 3 - Customize the Viewer Behavior

- [Step 1 – Creating a basic extension](#Step1)
- [Step 2 – Reference the extension script](#Step2)
- [Step 3 – Load the extension in the viewer](#Step3)
- [Step 4 – Testing the extension](#Step4)
- [Step 5 – Adding a selection handler](#Step5)
- [Step 6 – Displaying a panel](#Step6)
- [Step 7 (Bonus step) – Moving the camera](#Step7)
- [Even more bonus steps](#More)


Now you've got a basic 2D/3D model displayed on your web page, let's customize the viewer behavior.
The simplest way to customize behavior is through the Extension mechanism.
Extensions allow you to encapsulate your customized behavior in a separate JavaScript file that you can 'load' into the viewer when it's running (you can unload it whenever you like too).


<a name="Step1"></a>
## Step 1 – Creating a basic extension

Create a file named "Viewing.Extension.Workshop.js" (for example), and save it in the www subfolder of the project folder you cloned from GitHub (workflow-node.js-view.and.data.api).
Then copy the following basic extension skeleton code into the file and save it:

```js
  ///////////////////////////////////////////////////////////////////////////////
  // Demo Workshop Viewer Extension
  // by Philippe Leefsma, April 2015
  //
  ///////////////////////////////////////////////////////////////////////////////

  AutodeskNamespace("Viewing.Extension");

  Viewing.Extension.Workshop = function (viewer, options) {

    /////////////////////////////////////////////////////////////////
    //  base class constructor
    //
    /////////////////////////////////////////////////////////////////

    Autodesk.Viewing.Extension.call(this, viewer, options);

    var _self = this;
    var _viewer = viewer;

    /////////////////////////////////////////////////////////////////
    // load callback: invoked when viewer.loadExtension is called
    //
    /////////////////////////////////////////////////////////////////

    _self.load = function () {

    alert('Viewing.Extension.Workshop loaded');
    console.log('Viewing.Extension.Workshop loaded');

    return true;

    };

    /////////////////////////////////////////////////////////////////
    // unload callback: invoked when viewer.unloadExtension is called
    //
    /////////////////////////////////////////////////////////////////

    _self.unload = function () {

    console.log('Viewing.Extension.Workshop unloaded');

    return true;

    };

  };

  /////////////////////////////////////////////////////////////////
  // sets up inheritance for extension and register
  //
  /////////////////////////////////////////////////////////////////

  Viewing.Extension.Workshop.prototype =
    Object.create(Autodesk.Viewing.Extension.prototype);

  Viewing.Extension.Workshop.prototype.constructor =
    Viewing.Extension.Workshop;

  Autodesk.Viewing.theExtensionManager.registerExtension(
    'Viewing.Extension.Workshop',
    Viewing.Extension.Workshop);
```

<a name="Step2"></a>
## Step 2 – Reference the extension script

Reference the extension file in your index.html by adding the following script element to the header (change the path if you installed the file anywhere other than the www subfolder):

```js
<script src="/Viewing.Extension.Workshop.js"></script>
```

<a name="Step3"></a>
## Step 3 – Load the extension in the viewer

All that remains for index.js is to add some code to load the extension into the viewer once it is initialized. If the extension relies on geometry in the model, you should set up an
event to wait for the GEOMETRY_LOADED event, as some features may not be usable if the geometry in not fully loaded.

Open index.js and locate the place where you load the viewable in your viewer code:

```js
      viewer.load(pathInfoCollection.path3d[0].path);
    },
    onError);
  });

  function onError(error) {
    console.log('Error: ' + error);
  };
```

Add the event handler immediately before the call to viewer.load:

```js
      viewer.addEventListener(
        Autodesk.Viewing.GEOMETRY_LOADED_EVENT,
        function(event) {
          loadExtensions(viewer);
      });

      viewer.load(pathInfoCollection.path3d[0].path);
    },
    onError);

  });
```

Add a method that loads the extension:

```js
  function loadExtensions(viewer) {
    viewer.loadExtension('Viewing.Extension.Workshop');
  }

  function onError(error) {
    console.log('Error: ' + error);
  };
```

<a name="Step4"></a>
## Step 4 – Testing the extension

Your barebones extension should be ready to run now.
All it does is display an alert when it's loaded.
Test that the extension is loaded properly by running your sample.
(Remember that you setup your node.js project to serve the client page to [http://localhost:3000/](http://localhost:3000/), so open your WebGL-enabled browser and type
that address in the address bar).
Since all our changes are on client side, you can just refresh your browser to test your changes.


<a name="Step5"></a>
## Step 5 – Adding a selection handler

Now we will add some more interesting functionality to the basic extension:

Start by adding a handler for the SELECTION_CHANGED event to the Extension (i.e. editing the file 'Viewing.Extension.Workshop.js'). This event is triggered when user
selects a component in the model. Register your handler callback in the _self.load function, and then add the function definition below.

```js
  _self.load = function () {

    _viewer.addEventListener(
      Autodesk.Viewing.SELECTION_CHANGED_EVENT,
      _self.onSelectionChanged);

    console.log('Viewing.Extension.Workshop loaded');

    return true;
  };

  /////////////////////////////////////////////////////////////////
  // selection changed callback
  //
  /////////////////////////////////////////////////////////////////
  _self.onSelectionChanged = function (event) {

    // event is triggered also when component is unselected

    // in that case event.dbIdArray is an empty array
    if(event.dbIdArray.length) {

      var dbId = event.dbIdArray[0];

      // do stuff with selected component
    }
    else {
      // all components unselected
    }
  }
```

Every element in the displayed model has a unique ID called a dbId. The code you've just written simply stores the dbId of the first element in the list of elements that the
user selected. (Usually, the user will only select a single element, but more complete code would handle multiple selected elements).

You can test your code now, if you like. Put a breakpoint in the event handler to check its being called when you select an element. You can use Developer
Tool of Chrome or similar tools in other modern browsers to do debugging like setting breaking point, watch variable values, etc.  (Hint: You select a model
element by clicking it with you mouse; elements are highlighted in blue when selected).


<a name="Step6"></a>
## Step 6 – Displaying a panel

Now we'll get properties of selected component and display them in a custom viewer panel. Using the viewer UI to create your extensions will help migrating code from one
project to another. It helps making your extension independent of the client. However, you can manipulate any other component of your web application from the extension –
you could read or write information stored in a separate database, or update a table somewhere else on the webpage, etc.

Add some code to initialize an empty panel in the body of your extension:

```js
  /////////////////////////////////////////////////////////////////
  //  base class constructor
  //
  /////////////////////////////////////////////////////////////////

  Autodesk.Viewing.Extension.call(this, viewer, options);

  var _self = this;

  var _viewer = viewer;

  /////////////////////////////////////////////////////////////////
  // create panel and set up inheritance
  //
  /////////////////////////////////////////////////////////////////

  Viewing.Extension.Workshop.WorkshopPanel = function(
    parentContainer,
    id,
    title,
    options)
  {
    Autodesk.Viewing.UI.PropertyPanel.call(
      this,
      parentContainer,
      id, title);
  };

  Viewing.Extension.Workshop.WorkshopPanel.prototype = Object.create(
    Autodesk.Viewing.UI.PropertyPanel.prototype);

  Viewing.Extension.Workshop.WorkshopPanel.prototype.constructor =
    Viewing.Extension.Workshop.WorkshopPanel;

  /////////////////////////////////////////////////////////////////
  // load callback: invoked when viewer.loadExtension is called
  //
  /////////////////////////////////////////////////////////////////

  _self.load = function () {
```

Instantiate the panel in your load method and uninitialize it in unload.
Edit _self.load and _self.unload as follows:

```js
  /////////////////////////////////////////////////////////////////
  // load callback: invoked when viewer.loadExtension is called
  //
  /////////////////////////////////////////////////////////////////
  _self.load = function () {

    _viewer.addEventListener(
      Autodesk.Viewing.SELECTION_CHANGED_EVENT,
      _self.onSelectionChanged);

    _self.panel = new Viewing.Extension.Workshop.WorkshopPanel (
      _viewer.container,
      'WorkshopPanelId',
      'Workshop Panel');

    console.log('Viewing.Extension.Workshop loaded');

    return true;
  };

  /////////////////////////////////////////////////////////////////
  // unload callback: invoked when viewer.unloadExtension is called
  //
  /////////////////////////////////////////////////////////////////
  _self.unload = function () {

    _self.panel.setVisible(false);

    _self.panel.uninitialize();

    console.log('Viewing.Extension.Workshop unloaded');

    return true;
  };
```

Replace the implementation of the selection handler with the following code, so the panel is populated with the properties of the selected element and displayed when an item is selected.
Just for fun, we also isolate the component that is clicked:

```js
  /////////////////////////////////////////////////////////////////
  // selection changed callback
  //
  /////////////////////////////////////////////////////////////////
  _self.onSelectionChanged = function (event) {

    function propertiesHandler(result) {

      if (result.properties) {
        _self.panel.setProperties(
        result.properties);
        _self.panel.setVisible(true);
      }
    }

    if(event.dbIdArray.length) {
      var dbId = event.dbIdArray[0];

      _viewer.getProperties(
        dbId,
        propertiesHandler);

      _viewer.fitToView(dbId);
      _viewer.isolateById(dbId);
    }
    else {
      _viewer.isolateById([]);
      _viewer.fitToView();
      _self.panel.setVisible(false);
    }
  }
```

You've now finished writing your extension to respond to a user selecting a model element by displaying its properties in a panel and isolating it in the view.
Launch the client page and select a model element by clicking on it.
The model and camera view reset if you clear your selection or click in space.


<a name="Step7"></a>
## Step 7 (Bonus step) – Moving the camera

Finally, we'll add some camera animation orbiting the camera around the model.
We will use a simple approach with setInterval.
For a more robust approach, take a look at the blog post article describing
[how to create animations in the viewer](http://adndevblog.typepad.com/cloud_and_mobile/2015/04/how-to-create-animations-in-the-viewer.html).

Add a property the extension to hold the interval Id, so we can cancel it.

```js
  _self.load = function () {

    _viewer.addEventListener(
      Autodesk.Viewing.SELECTION_CHANGED_EVENT,
      _self.onSelectionChanged);

    _self.panel = new Viewing.Extension.Workshop.WorkshopPanel (
      _viewer.container,
      'WorkshopPanelId',
      'Workshop Panel');

    _self.interval = 0;

    console.log('Viewing.Extension.Workshop loaded');

    return true;
  };
```

Add following methods to handle the animation immediately below the end of the _self.onSelectionChanged function implementation.

```js
    /////////////////////////////////////////////////////////////////
    // rotate camera around axis with center origin
    //
    /////////////////////////////////////////////////////////////////
    _self.rotateCamera = function(angle, axis) {
      var pos = _viewer.navigation.getPosition();

      var position = new THREE.Vector3( // Point?
        pos.x, pos.y, pos.z);

      var rAxis = new THREE.Vector3(
        axis.x, axis.y, axis.z);

      var matrix = new THREE.Matrix4().makeRotationAxis(
        rAxis,
        angle);

      position.applyMatrix4(matrix);

      _viewer.navigation.setPosition(position);
    };

    /////////////////////////////////////////////////////////////////
    // start rotation effect
    //
    /////////////////////////////////////////////////////////////////

    _self.startRotation = function() {
      clearInterval(_self.interval);

      // sets small delay before starting rotation

      setTimeout(function() {
        _self.interval = setInterval(function () {
        _self.rotateCamera(0.05, {x:0, y:1, z:0});
        }, 100)}, 500);
    };
```

Finally modify the selection handler to trigger the animation when a component is selected:

```js
  /////////////////////////////////////////////////////////////////
  // selection changed callback
  //
  /////////////////////////////////////////////////////////////////
  _self.onSelectionChanged = function (event) {

    function propertiesHandler(result) {

      if (result.properties) {

        _self.panel.setProperties(
        result.properties);

        _self.panel.setVisible(true);
      }
    }

    if(event.dbIdArray.length) {

      var dbId = event.dbIdArray[0];

      _viewer.getProperties(
        dbId,
        propertiesHandler);

      _viewer.fitToView(dbId);
      _viewer.isolateById(dbId);

      _self.startRotation();
    }
    else {
      clearInterval(_self.interval); // where is this function defined?

      _viewer.isolateById([]);
      _viewer.fitToView();
      _self.panel.setVisible(false);
    }
  }
```

Test your extension again.
This time, in addition to displaying the panel, the camera (your view of the model) starts rotating when you select a model element.


<a name="More"></a>
## Even more bonus steps

If you've still got some time, go to [http://gallery.autodesk.io](http://gallery.autodesk.io) and play with some of the models and sample extensions available there.
The [Car](http://viewer.autodesk.io/node/gallery/#/viewer?id=551d0768be86fc2c1138b4d4) model is reasonably detailed.
To test one of the sample Extensions, click on the Extensions menu, then click Manage and click on an Extension's name to enable or disable it. We recommend you only
enable one Extension at a time (i.e. disable the last Extension you used before enabling another), because not all the Extensions have been written to play nicely with
other Extensions.

To see the source code for those Extensions, go to Extensions and select Source.  Then click on the name of the Extension you're interested in.

Here are two YouTube videos explaining how to use the gallery sample and a couple of the extensions:

[Viewer Gallery Sample](https://www.youtube.com/watch?v=SQJSuqRqiCg)

[Viewer Physics Engine](https://www.youtube.com/watch?v=tK2ndbvchIM)

Here is a tutorial on how to add a Toolbar button [Toolbar Extension](https://developer.autodesk.com/en/docs/viewer/v2/tutorials/toolbar-button/)

<a name="End"></a>
## Solution

You can get the final source code as zip from [here](https://github.com/Developer-Autodesk/workflow-node.js-view.and.data.api/archive/v1.0-workshop-extension.zip), or using git:
```
git checkout v1.0-workshop-extension
```

=========================
[Home](../README.md)
