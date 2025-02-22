# Docs
This repo builds the documentation for developer.holochain.org generated by 11ty (https://www.11ty.dev/).

## A note about browsers
Other than a warning to the user (see `unsupported-brower-warning.njk` if you care) ___no___ effort has been made to accomadate non-Evergreen browsers, which is mostly to say Internet Explorer of any version. There isn't a lot of js in the rendered site, but what there is won't work, and newer CSS features are used as well.

## What the heck is 11ty (an extremely quick intro)
11ty, or Eleventy, is a NodeJS based static website generator. It supports a bunch of template types, 
including markdown and nunjucks (https://mozilla.github.io/nunjucks/) which we use here. By default any 
template file in the input directory will generate an html file in the generated site. It also supports 
includes and data generated content. It is extremely configuarable and as such the docs (https://www.11ty.dev/docs/) 
can be a handful, but they are quite good and are worth a look. 

## How are we using 11ty
The 11ty config (.eleventy.js) is the final source of truth, but the basic setup is:
- The output goes to _site.
- Template files in `/src/pages` are transformed into html pages in the resulting site.
- Page templates can have embedded data called Front Matter in YAML format.
- `/src/pages/_includes` contains overall page layouts and helpers that can be called from the page template files
- `/src/pages/_data` contains files that provide data that can be consumed by the templates. See [Datafiles](./Datafiles.md) for details Uses:
    - Header Navigation
    - Contributors display
    - Community quotes display
    - Roadmap data
- `assets` and `client-side` are static assets that get copied to _site, via calls to 11ty's .addPassthroughCopy.
- `copy-to-root` (Not in place yet): sitemap.xml, robots.txt and _redirects (a Netlify config file with HTTP redirect _commands_). 
Copied to _site by 11ty's addPassthroughCopy.
- Also see `Using Markdown Here` below.

## Other artifacts
- SCSS is compiled by sass via package.json script
- `/src/client-side` is Typescript code that is compiled by via package.json script. When building for production it is also bundled into a single script for broader browser support.

## Using Markdown Here
We are using a mixture of markdown and nunjucks as the main page templates. For documentation pages (as opposed to more blingy pages like the home page) md is prefered.

By default 11ty uses the [markdown-it](https://github.com/markdown-it/markdown-it) parser and we stuck with that. 
Additionally the following `markdown-it` plugins have been added:
- [markdown-it-attrs](https://github.com/arve0/markdown-it-attrs) allows, in particular, attributes and classes to be added. For example:
  ```
  [Holochain Launcher](https://github.com/holochain/launcher){#my-link target=_blank .a-super-cool-link}
  ```
  renders as 
  ```
  <a href="https://github.com/holochain/launcher" id="my-link" target="_blank" class="a-super-cool-link">Holochain Launcher</a>
  ```
  There are of course more details. See the docs at [markdown-it-attrs](https://github.com/arve0/markdown-it-attrs) for more.
- [markdown-it-container](https://github.com/markdown-it/markdown-it-container) Plugin for creating block-level custom containers. For example:
  ```
  ::: coreconcepts-intro
  Blah blah
  :::
  ```

  renders as 
  
  ```
  <div class="coreconcepts-intro">
    <p>Blah blah</p>
  </div>
  ```
  Please note: The closing `:::` is required. Also if you need to nest containers then the outer container gets an additional `:`.
  Each container type needs to be configured in `markdown-it-config.js` for examples and to add more. This one provides a lot of flexibility.


## Setup for Dev
- `npm install`
- `npm run dev`
- open browser to http://localhost:8080/

## NPM Scripts of note
- `dev`: Runs 11ty & SCSS compile with browser reload.
- `build`: The Production build. Mostly used by CI/CD.
    - Cleans `_site` dir
    - Builds, [autoprefixes](https://github.com/postcss/autoprefixer) and minifies the SCSS
    - Bundles the JS modules   
    - Builds 11ty with links to the bundled JS and minified CSS
- `clean`: Cleans out the `_site` dir
- `update-browserlist`: Updates the browserlist that autoprefixer uses detirmine what vendor prefixes are needed for your specified browser set. Run once in a while. 


