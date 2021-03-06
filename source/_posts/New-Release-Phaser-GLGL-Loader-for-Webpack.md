title: "New Release: Phaser GLGL Loader for Webpack"
date: 2015-03-28 23:11:53
tags: [phaser, game development, webpack, node, javascript]
---

You know what sucks? The way phaser loads GLSL files. In that, it doesnt by itself. What also sucks is including code for a totally differnt language as a list of strings in your codebase. Whenever you come across this sort of thing, its usually best to try and store the "nonindigenous code" somewhere sles, like a seperate file. This is especially true if you find it can be templated.

THis is exactly the problem I encountered in Phaser.js, so I made a quick solution.

<!-- more -->

##Phaser GLSL Loader

[![Build Status](https://travis-ci.org/the-simian/phaser-glsl-loader.svg)](https://travis-ci.org/the-simian/phaser-glsl-loader)

[![Coverage Status](https://coveralls.io/repos/the-simian/phaser-glsl-loader/badge.svg?branch=master)](https://coveralls.io/r/the-simian/phaser-glsl-loader?branch=master)

[![Code Climate](https://codeclimate.com/github/the-simian/phaser-glsl-loader/badges/gpa.svg)](https://codeclimate.com/github/the-simian/phaser-glsl-loader)

[![David-Dm](https://david-dm.org/the-simian/phaser-glsl-loader.svg)](https://david-dm.org/the-simian/phaser-glsl-loader)


This is a simple GLSL loader that is meant to work with Phaser and Webpack.


##The Problem:

Lets say you're working in phaser, and you see a really cool looking fragment shader you want to use.

![Some cool shader](http://i.imgur.com/1xys0Iy.png)


So you go to the phaser examples, and they show you to integrate your code just like this:

```js
var game = new Phaser.Game(800, 600, Phaser.AUTO, 'phaser-example', { create: create, update: update });

var filter;
var sprite;

function create() {

    //  From http://glslsandbox.com/e#20450.0

    var fragmentSrc = [
        "precision mediump float;",
        "uniform vec2      resolution;",
        "uniform float     time;",

        "float size = 0.002;",

        "void main( void ) {",
            "vec2 view = ( gl_FragCoord.xy - resolution / 2.0 ) / ( resolution.y / 2.0);",
            "float time = time + length(view)*8.;",
            "vec4 color = vec4(0);",
            "vec2 center = vec2(0);",
            "float rotationVelocity = 2.0;",
            "for( int j = 0; j < 20; j++ ) {",
                "for( int i = 0; i < 20; i++ ) {",
                    "float amplitude = ( cos( time / 10.0 ) + sin(  time /5.0 ) ) / 2.0;",
                    "float angle =   sin( float(j) * time) * rotationVelocity + 2.0 * 3.14 * float(i) / 20.0;",
                    "center.x = cos( 7.0 * float(j) / 20.0 * 2.0 * 3.14 ) + sin( time / 4.0);",
                    "center.y = sin( 3.0 * float(j) / 20.0 * 2.0 *  3.14 )+ cos( time / 8.0);",
                    "vec2 light = center + amplitude * vec2( cos( angle ), sin( angle ));",
                    "//size = sin( time ) * 0.005;",
                    "float l = size / length( view - light );",
                    "vec4 c = vec4( l / 20.0, l, l, 1.0 ) / 5.0;",
                    "color += c;",
                "}",
            "}",
            "gl_FragColor = color;",
        "}"
    ];

    filter = new Phaser.Filter(game, null, fragmentSrc);
    filter.setResolution(800, 600);

    sprite = game.add.sprite();
    sprite.width = 800;
    sprite.height = 600;

    sprite.filters = [ filter ];

}

function update() {
    filter.update();
}
```

Man, what mess! Wouldnt it be awesome if you could just put the shader into a seperate file and load it that way?

```js
var fragmentSrc = require('../shaders/cubething.frag'),
```

You could cut all that code out into a simple require statement! That is what this loader is for.

## The Solution

```sh
npm install --save-dev phaser-glsl-loader
```

First, you need to add the loader to your list of loaders, most likely in your `webpack.config.js` file.

Something like this:

```js

var glFragmentLoader = require('phaser-glsl-loader')

module.exports = {
  module: {
    loaders: [
      {
        test: /\.frag$/i,
        loader: 'gl-fragment-loader'
      }
    ]
  },
  resolveLoader: {
    alias: {
      'gl-fragment-loader': glFragmentLoader,
    }
  }
};
```
Then you'll be able to reference external files, instead of basically including a large array of strings!




