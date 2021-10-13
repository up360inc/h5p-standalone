# H5P Standalone Player 3.x [![CircleCI](https://circleci.com/gh/tunapanda/h5p-standalone.svg?style=svg)](https://circleci.com/gh/tunapanda/h5p-standalone)
Display H5P content without the need for an H5P server

## Installation

**Source**|**Info**
-----|-----
yarn | `yarn add h5p-standalone`
Release | [Donwload latest version here](https://github.com/tunapanda/h5p-standalone/releases/latest)

## Basic Usage
Ensure you have an extracted H5P zip file in your workspace folder first. A simple guide on how to extract an H5P zip file is provided towards the [end section of this guide ](https://github.com/tunapanda/h5p-standalone#extracting-h5p)


The player can be set up either by directly calling the already built scripts and styles in your `HTML` page or using `ES6` syntax. For the standalone player to work correctly on a webpage, both the assets, settings, and H5P specific files need to be set properly first.

### Direct use
1.  Download the project latest release zipped source code from [here](https://github.com/tunapanda/h5p-standalone/releases/latest)
2. Extract the downloaded zipped code in step 1 above
3. Copy the contents of the `dist` folder into your workspace static `assets` folder ( _The folder name does not matter. Remember the location for the next step_ )
4. Add a  `div` element in your HTML page where you want to display the H5P content. The `div` element should have a unique `id` attribute as compared to all other elements on the same page.
    ```html
    <div id='h5p-container'></div>
    ```

5. Include the H5P standalone main script in your HTML page (_modify the path location if the files are not in the assets folder_)
    ```html
    <script type="text/javascript" src="assets/main.bundle.js"></script>
    ```
6. Call the H5P player by providing arguments on where to find a `div` element and the location of the H5P content.
    ```javascript

    const el = document.getElementById('h5p-container');
    const options = {
      h5pJsonPath:  '/h5p-folder',
      frameJs: '/assets/frame.bundle.js',
      frameCss: '/assets/styles/h5p.css',
    }
   new H5PStandalone.H5P(el, options);

    ```
    A detailed description of the H5P player arguments are provided  under the [advance section](https://github.com/tunapanda/h5p-standalone#advanced-usage)
    Simple instruction on how to extract H5P zipped file provided [here](https://github.com/tunapanda/h5p-standalone#extracting-h5p)

### Using ES6
Install the player using yarn

```
yarn add h5p-standalone
```

Add an element to attach the player
```html
<div id='h5p-container'></div>
```
initialize the H5P
```javascript
import { H5P } from 'h5p-standalone'; // ES6
// const { H5P } = require('h5p-standalone'); AMD
// const { H5P } = 'H5PStandalone'; // object destructuring

const el = document.getElementById('h5p-container');
const options = {
    h5pJsonPath: '/h5p-folder',
    frameJs: '/assets/frame.bundle.js',
    frameCss: '/assets/styles/h5p.css',
};

new H5P(el, h5pLocation);
```
   A detailed description of the H5P player arguments are provided under the [advance section](https://github.com/tunapanda/h5p-standalone#advanced-usage)
## Advanced Usage
The standalone H5P player constructor accepts two arguments.
1. A HTML element where the H5P iframe will be embedded as the first argument.
2. JSON object with the following options :
### H5P Options
**Option name**|**Required**|**Description**
-----|-----|----
`h5pJsonPath`   | Yes | Path to the H5P content folder
`frameCss`  | Yes | URL to the standalone player `h5p.css`
`frameJs`   |Yes | URL to the standalone player `frame.bundle.js`
`id`    | No | Player unique identifier. Randomly generated by default
`librariesPath` | No| Path where the player should find the H5P content libraries. Defaults to same as `h5pJsonPath`
`contentJsonPath`|No | Path where the player should find the H5P `content.json` file. Defaults to  `{h5pJsonPath}/content/`,
`frame` |No| A boolean on whether to show frame and buttons below H5P
`copyright` |No| A boolean on whether display copyright button
`export` |No|  A boolean on whether display a download button.
`icon`  |No|   A boolean on whether display H5P icon
`downloadUrl` |No| A path or a url that returns zipped h5p for download. The link is used by H5P `export` button
`fullScreen` |No| A boolean on whether to enable fullscreen button if browser supports the feature. Default is `false`|
`xAPIObjectIRI`|No| An identifier for a single unique Activity ~ utilized when generating xAPI [object](https://github.com/adlnet/xAPI-Spec/blob/master/xAPI-Data.md#acturi) field. Default is page host+pathname
`embed` |No| A boolean on whether display embed button. Default is `false`.  N.B. Setting this option to `true` will require an `embedCode` below.
`embedCode` |unless `embed` is true| Embed/Iframe code that user can insert on their site to view same content. Check some caveats to consider [below](#caveats-while-adding-embed-code)
`customCss` | No | Path(s) to custom stylesheet file(s)
`customJs` | No | Path(s) to custom script file(s)

**Note:**
- One can use absolute URL for `frameCss`, `frameJs`, and for other path options(`h5pJsonPath`,`librariesPath`, & `librariesPath`)
- Any path that starts with a forward slash `/` is treated as relative to the site root.
- Any path starting with a dot is treated to be in respect to the current page directory.
----
#### Example with advance options
```javascript
import { H5P } from 'h5p-standalone';

const el = document.getElementById('h5p-container');
const h5pLocation = './workspace';

const options = {
    id: 'exercise-one',
    frameJs: './frame.bundle.js',
    frameCss: './styles/h5p.css',
    h5pJsonPath: '/path/to/h5p-folder',
    librariesPath: '/path/to/h5p-folder', //content is on same folder level as h5p.json
    librariesPath: '/path/to/shared/libaries', //shared libraries path
    frame: true, //required to display copyright,  embed, & export buttons
    copyright: true,
    export: false,
    icon: true,
    downloadUrl: '/path/to/exercise-one.h5p',
    fullScreen: true, //enable fullscreen button
    embed: true,
    embedCode:'<iframe width=":w" height=":h" src="https://replacethiswithyoururl.io" frameBorder="0" scrolling="no" styles="width:100%"></iframe>',
    customCss: ['/path/to/some-css.css', '/path/to/some-other-css.css'], // custom stylesheets
    customJs: '/path/to/custom-script.js' // custom script
  };


new H5P(el,options)
.then(() => {
  // do stuff
});

// Or using async-await syntax (async wrapper function removed for readability) :

 await new H5P(el, options);

```

### Multiple H5P players on the same page
To render multiple H5Ps, your code **must** be async aware.

```javascript
import { H5P } from 'h5p-standalone';
const player1Options = {
    h5pJsonPath: '/h5p/exercise-one',
    frameJs: '/assets/frame.bundle.js',
    frameCss: '/assets/styles/h5p.css',
};

const player2Options = {
    h5pJsonPath: '/h5p/exercise-two',
    frameJs: '/assets/frame.bundle.js',
    frameCss: '/assets/styles/h5p.css',
};

const player1 = new H5P(document.getElementById('h5p-container-1'), player1Options);

player1.then(() => {
  return new H5P(document.getElementById('h5p-container-2'), player2Options);
}).then(( => {
  // do stuff
}));


// OR (async wrapper function removed for readability)
await new H5P(document.getElementById('h5p-container-1'), player1Options);
await new H5P(document.getElementById('h5p-container-2'), player2Options);


```

## Listening to xAPI events
To listen for [xAPI events](https://h5p.org/documentation/api/H5P.XAPIEvent.html) emmitted by the player, you must wait for the player to finish loading and initializing the required content libraries. You can find more info about xAPI events here https://h5p.org/documentation/x-api
1) Using `then()` method
```js

const el = document.getElementById("h5p-container");
const options = {
  h5pJsonPath: "/h5p-folder",
  frameJs: "/assets/frame.bundle.js",
  frameCss: "/assets/styles/h5p.css",
};

new H5PStandalone.H5P(el, options).then(function () {
  H5P.externalDispatcher.on("xAPI", (event) => {
    //do something useful with the event
    console.log("xAPI event: ", event);
  });
});

```
2) Using `async` function
```js
import { H5P as H5PStandalone } from 'h5p-standalone'; //you need you an alias due to conflict

async function myAwesomePlayer() {
  const el = document.getElementById("h5p-container");
  const options = {
    h5pJsonPath: "/h5p-folder",
    frameJs: "/assets/frame.bundle.js",
    frameCss: "/assets/styles/h5p.css",
  };

  await new H5PStandalone(el, options);

  H5P.externalDispatcher.on("xAPI", (event) => {
    //do something useful with the event
    console.log("xAPI event: ", event);
  });
}

//don't forget to call the function
myAwesomePlayer();

```
### Caveats while adding embed code
- This library includes an H5P resizer by default in `main.bundle.js` at the moment. But, to allow the iframe width to resize promptly, add CSS style setting the width to 100% i.e. `style="width:100%;"`
- If you want to allow users to resize the iframe width and height, set them using placeholders provided by H5P i.e., `width=":w"` and `height=":h"`

Example that combines above points:

```js
<iframe width=":w" height=":h"
src="https://app.wikonnect.org/embed/JJuzs-OAACU"  //replace this with your URL
frameBorder="0" scrolling="no" styles="width:100%"></iframe
```


### Extracting H5P
1. Rename the H5P file extension from `.h5p` file to `.zip`
2. Extract the renamed file contents into your workspace `h5p-folder` folder

## Testing during development
 After modifying the project, build the files:
 `` yarn build``
 to run available [Cypress](https://www.cypress.io/) tests:
`` yarn test``
