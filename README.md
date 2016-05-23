# redsift-bundler

A bundler for ES2015 Javascript and [Stylus](http://stylus-lang.org) CSS. It's intention is to have a central bundle setup that is configured by a config file. The application developer should not have to care about setting tasks for transpiling, concatenating, pre-processing, etc. of source files. Provided with a config file the `redsift-bundler` takes care of that.

The bundler is based on [Rollup](http://rollupjs.org/) and [gulp-stylus](https://github.com/stevelacy/gulp-stylus).

# Installation

In your project folder do a local installation of the NPM package with

```bash
npm install --save-dev @redsift/redsift-bundler
```

or install it globally with

```bash
npm install -g @redsift/redsift-bundler
```

which both will provide you with the `redsift-bundler` executable.

For convenience add the bundler to your `package.json` file within the `script` section:

```javascript
"scripts": {
    "build": "redsift-bundler -c ./bundle.config.js",
    ...
  }
```

This allows you to call

```bash
npm run build
```

in the project folder to create the bundle(s).

# Usage

```bash
Usage: redsift-bundler -c [config-file]

Options:
  -c  Bundle configuration file [required]
```

The bundler needs a configuration file which is oriented very close to the configuration properties of [Rollup](http://rollupjs.org/). Have a look at Rollup's documentation to understand the used options in detail.

## Simple config file

This simple configuration file takes the ES2015 Javascript file `./src/js/index.js` and the Stylus file `./src/styles/index.styl` and outputs it as a bundle into `./dist/mybundle`. The folder will container a `js` and a `css` folder with the bundled files and outputs a non-minified and a minified version of the bundle together with sourcemaps.

```javascript
var paths = {
  dest: './dist'
}

// We create a single bundle:
var myBundleConfig = {
  // nsmr of the bundle (a folder with this name will be created in the 'outputFolder'
  name: 'mybundle',
  // the Javascript index file. Output Javascript will be written to a file with the given 'name' within 'outputFolder/js'
  mainJS: {
    name: 'mybundle',
    indexFile: './src/js/index.js'
  },  
  // the CSS/Stylus index file. Output CSS will be written to a file with the given 'name' within 'outputFolder/css'
  styles: [{
    name: 'mybundle',
    indexFile: './src/styles/index.styl'
  }],
  // list of formats which are going to be created. Valid formats are described in the Rollup documentation
  formats: ['es6', 'umd'],
  // the output folder of the bundle
  outputFolder: paths.dest,
  // the module name for formats in the global namespace, e.g. 'umd'
  moduleNameJS: 'Redsift',
  // The destination of the sourcemaps relative to 'outputFolder'
  mapsDest: '.',
  // external mappings allow you to exclude the bundling of the named libraries. See the 'real world' example and the 
  // Rollup documentation regarding this option. For the simple example we are including everything into the bundle, so
  // there is no need for that here.
  externalMappings: {
    // 'd3': 'd3'
  }
};

// export the config as array. Muliple bundles can be defined (see real-world example below)
module.exports = [ myBundleConfig ];
```

## Real-world example

The example configuration file is taken from the [RedsiftUI](https://github.com/Redsift/redsift-ui/) repository. Have a look at the project to see the file structure which corresponds to this config file.

The configuration creates the bundles `core`, `sift`, `full` and `crx` and stores them in the `./dist` folder.

```javascript
var paths = {
  dest: './dist'
}

var defaultConfig = {
  formats: ['es6', 'umd'],
  outputFolder: paths.dest,
  moduleNameJS: 'Redsift',
  mapsDest: '.',
  externalMappings: {
    'd3-selection': 'd3',
    'd3-scale': 'd3',
    'd3-axis': 'd3',
    'd3-time-format': 'd3',
    'd3-time': 'd3'
  }
};

var coreConfig = {
  name: 'core',
  mainJS: {
    name: 'redsift',
    indexFile: './bundles/core/index.js'
  },
  styles: [{
    name: 'redsift-light',
    indexFile: './bundles/core/redsift-light.styl'
  }, {
    name: 'redsift-dark',
    indexFile: './bundles/core/redsift-dark.styl'
  }, {
    name: 'redsift-xtra',
    indexFile: './bundles/core/redsift-xtra.styl'
  }]
};

var siftConfig = {
  name: 'sift',
  mainJS: {
    name: 'redsift',
    indexFile: './bundles/sift/index.js'
  },
  styles: [{
    name: 'redsift-light',
    indexFile: './bundles/sift/redsift-light.styl'
  }, {
    name: 'redsift-dark',
    indexFile: './bundles/sift/redsift-dark.styl'
  }, {
    name: 'redsift-xtra',
    indexFile: './bundles/sift/redsift-xtra.styl'
  }]
};

var fullConfig = {
  name: 'full',
  mainJS: {
    name: 'redsift',
    indexFile: './bundles/full/index.js'
  },
  styles: [{
    name: 'redsift-light',
    indexFile: './bundles/full/redsift-light.styl'
  }, {
    name: 'redsift-dark',
    indexFile: './bundles/full/redsift-dark.styl'
  }, {
    name: 'redsift-xtra',
    indexFile: './bundles/full/redsift-xtra.styl'
  }]
};

var crxConfig = {
  name: 'crx',
  mainJS: {
    name: 'redsift',
    indexFile: './bundles/crx/index.js'
  },
  externalMappings: []
};

var bundles = [
  merge(defaultConfig, coreConfig),
  merge(defaultConfig, siftConfig),
  merge(defaultConfig, fullConfig),
  merge(defaultConfig, crxConfig)
];

module.exports = bundles;

function merge(obj1, obj2) {
  var newObj = JSON.parse(JSON.stringify(obj1));
  Object.keys(obj1).forEach(function(key) { newObj[key] = obj1[key]; });
  Object.keys(obj2).forEach(function(key) { newObj[key] = obj2[key]; });
  return newObj;
}
```
