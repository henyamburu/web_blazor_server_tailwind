# Project Title

## Table of Contents

- [Integrating Tailwind CSS with Blazor](#integrating_tailwind)
- [Customising Tailwind](#customising_tailwind)
- [Optimising Your CSS](#optimising_css)
- [Contributing](../CONTRIBUTING.md)

## Integrating Tailwind CSS with Blazor <a name = "integrating_tailwind"></a>

This a summary of Chris Sainty articles on Integrating Tailwind CSS with Blazor covered in [part 1](https://chrissainty.com/integrating-tailwind-css-with-blazor-using-gulp-part-1/) and [part 1](https://chrissainty.com/integrating-tailwind-css-with-blazor-using-gulp-part-2/)


### Adding Gulp and Tailwind via NPM

Install the Gulp CLI globally `npm install gulp-cli -g`

Add a new file called `package.json` to the root of the project

```
{
  "devDependencies": {
    "gulp": "^4.0.2",
    "gulp-postcss": "^8.0.0",
    "precss": "^4.0.0",
    "tailwindcss": "^1.2.0",
    "autoprefixer": "^9.7.4"
  }
}
```

Add CSS file as per Tailwinds docs [root]/assets/styles/site.css with following content:

```
@tailwind base;
@tailwind components;
@tailwind utilities;

#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

Add another file to the root of the project called `gulpfile.js`. This is where we are going to configure Gulp to build Tailwind

)pen a terminal and navigate to the root of the Blazor app, the same folder where the `package.json` and `gulpfile.js` reside. Once there, run the following commands.

```
npm install
gulp css
```

## Customising Tailwind <a name = "customising_tailwind"></a>

To custom Tailwind to your specific needs add `tailwind.config.js` in the root of the app. Paste the code below. The file is split into three different sections, theme, variants and plugins. The theme section allows us to customise things like fonts, colour palettes, border widths, essentially anything related to the visual style of your site. 

```
module.exports = {
  theme: {
    extend: {
      colors: {
        blazoredorange: '#ff6600'
      }
    }
  }
};

```

`extend` is the key here to tell Tailwind to ADD to the existing colours.  Else, written as below would replace the existing colour palettes

```
module.exports = {
  theme: {
    colors: {
      blazoredorange: '#ff6600'
    }
  }
};

```

This file is automatically picked up by Tailwind during the build process so once you've finished adding your custom configuration, just rerun the gulp css task to generate your new custom CSS. With the above configuration change we now get access to the following extra classes.

..* text-blazoredorange
..* border-blazoredorange
..* bg-blazoredorange

## Optimising Your CSS <a name = "optimising_css"></a>

One downside of Tailwind is that there are a lot of classes generated by default. There is a plugin that resolves or addresses this problem call [PurgeCSS](https://github.com/FullHuman/purgecss). 

Install PurgeCSS using npm and the following command: `npm install --save-dev gulp-purgecss`

This plugin scans through files and tries to determine what CSS classes are used and remover the ones not in use.  Update the Gulp file to look like this

```
const gulp = require('gulp');

gulp.task('css', () => {
  const postcss = require('gulp-postcss');
	const purgecss = require('gulp-purgecss');

  return gulp.src('./Styles/site.css')
    .pipe(postcss([
      require('precss'),
      require('tailwindcss'),
      require('autoprefixer')
    ]))
    .pipe(purgecss({ content: ['**/*.html', '**/*.razor'] }))
    .pipe(gulp.dest('./wwwroot/css/'));
});

```

### Minimising CSS

Install [CleanCSS](https://github.com/scniro/gulp-clean-css) which is going to minisize css: `npm install gulp-clean-css --save-dev`

Install [Sourcemaps](https://github.com/gulp-sourcemaps/gulp-sourcemaps) which will generate sourcemap files so you can debug your CSS once it's been minified: `npm install gulp-sourcemaps --save-dev`

Update the `gulpfile` to add more configuration

```
const gulp = require('gulp');

gulp.task('css', () => {
  const postcss = require('gulp-postcss');
  const purgecss = require('gulp-purgecss');
  const sourcemaps = require('gulp-sourcemaps');
  const cleanCSS = require('gulp-clean-css');

  return gulp.src('./Styles/site.css')
    .pipe(sourcemaps.init())
    .pipe(postcss([
      require('precss'),
      require('tailwindcss'),
      require('autoprefixer')
    ]))
    .pipe(purgecss({ content: ['**/*.html', '**/*.razor'] }))
    .pipe(cleanCSS({ level: 2 }))
    .pipe(sourcemaps.write('.'))
    .pipe(gulp.dest('./wwwroot/css/'));
});

```

The current configuration has only one task which produces our CSS and a version that does not include all the utility classes for future development.  Up the `gulpfile` with two gulp tasks, on to use during development and one to use when we want to ship to production,

```
const gulp = require('gulp');
const postcss = require('gulp-postcss');
const sourcemaps = require('gulp-sourcemaps');
const cleanCSS = require('gulp-clean-css');
const purgecss = require('gulp-purgecss');

gulp.task('css:dev', () => {
  return gulp.src('./Styles/site.css')
    .pipe(sourcemaps.init())
    .pipe(postcss([
      require('precss'),
      require('tailwindcss'),
      require('autoprefixer')
    ]))
    .pipe(gulp.dest('./wwwroot/css/'));
});

gulp.task('css:prod', () => {
  return gulp.src('./Styles/site.css')
    .pipe(sourcemaps.init())
    .pipe(postcss([
      require('precss'),
      require('tailwindcss'),
      require('autoprefixer')
    ]))
    .pipe(purgecss({ content: ['**/*.html', '**/*.razor'] }))
    .pipe(cleanCSS({ level: 2 }))
    .pipe(sourcemaps.write('.'))
    .pipe(gulp.dest('./wwwroot/css/'));
});

```















## Getting Started <a name = "getting_started"></a>

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See [deployment](#deployment) for notes on how to deploy the project on a live system.

### Prerequisites

What things you need to install the software and how to install them.

```
Give examples
```

### Installing

A step by step series of examples that tell you how to get a development env running.

Say what the step will be

```
Give the example
```

And repeat

```
until finished
```

End with an example of getting some data out of the system or using it for a little demo.

## Usage <a name = "usage"></a>

Add notes about how to use the system.