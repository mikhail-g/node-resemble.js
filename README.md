# @mikhail-g/node-resemble-js

Analyse and compare images with Javascript. This project does not need canvas or any other binary dependencies.
It is a modification of [Resemble.js](https://github.com/Huddle/Resemble.js)

## Installation

`npm install @mikhail-g/node-resemble-js`

## Example

### Retrieve basic analysis on image

```javascript
const resemble = require('@mikhail-g/node-resemble-js');

resemble(fileData)
  .onComplete(function(data){
    console.log(data);
  });
```

output for a blank white image:

```javascript
 {
   red: 255,
   green: 255,
   blue: 255,
   brightness: 255
 }
```

### Use resemble to compare two images

```javascript
var diff = resemble(file)
  .compareTo(file2)
  .ignoreColors()
  .onComplete(function(data){
    console.log(data);
});
```

output:

```javascript
 {
   misMatchPercentage : 100, // %, when all pixels are different in 2 images
   isSameDimensions: false, // is width and height of 2 images are the same
   dimensionDifference: { width: 0, height: -1 }, // defined if dimensions are not the same
   getImageDataUrl: function(){}
 }
```

### Configuration

You can change the comparison method after the first analysis or before `onComplete()`.

```javascript
diff.ignoreNothing(); // any change in pixel color or brightness will increase misMatchPercentage
diff.ignoreColors(); // compares only brightness of pixels ignoring colors
diff.ignoreAntialiasing(); 
```

And change the output display style.

```javascript
resemble.outputSettings({
  errorColor: {
    red: 255,
    green: 0,
    blue: 255
  },
  errorType: 'movement',
  transparency: 0.3
});
// resembleControl.repaint();
```

--------------------------------------

### Usuage in cucumber step definition

``` javascript
     this.Then(/^Screenshot should match image "(.*)"$/, function (image, callback) {
        browser.takeScreenshot().then(function(pngString) {
          var screenshot = new Buffer(pngString, 'base64');
     
          resemble(image)
            .compareTo(screenshot)
            .onComplete(function(data){
     
              if (Number(data.misMatchPercentage) <= 0.01) {
                callback();
              } else {
                data.getDiffImage().pack().pipe(fs.createWriteStream(image + 'diff.png'));
                callback.fail(new Error("Screenshot '" + image+  "' differ " + data.misMatchPercentage + "%"));
              }
            });
        });
      })
```

--------------------------------------

## Credits

* Created by [James Cryer](https://github.com/jamescryer) and the Huddle development team.
* [Lukas Svoboda](https://github.com/lksv) - modification for node.js
* [Mirza Zeyrek](https://github.com/mirzazeyrek) - jpeg support
* [LarryG](https://github.com/larryg01) - dependency updates
* [Mykhailo Hariachyi](https://github.com/mikhail-g) - fix for ignoreNothing() function
