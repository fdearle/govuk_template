# GovukTemplate

This provides a template containing the GOV.UK header and footer, and associated assets.

This is versioned following [Semantic Versioning](http://semver.org).

## Requirements

The Ruby language (1.9.3+), the build tool [Rake](http://rake.rubyforge.org/) & the dependancy management tool [Bundler](http://bundler.io/)

## Packaging

At present this generates 4 output formats:

1. a gem containing a Rails engine
2. a tarball containing Play Framework templates
3. a folder containing Mustache templates
3. a tarball containing Mustache Inheritance templates
4. a tarball

### Gem version

This is available on rubygems.org.  To use it, add this line to your application's Gemfile:

    gem 'govuk_template'

And then execute:

    $ bundle

You can then use the `govuk_template` layout in your app.  If you need to extend the layout you can use [nested layouts](http://guides.rubyonrails.org/layouts_and_rendering.html#using-nested-layouts).

### Play version

To generate the tarball of Play Framework templates run `bundle exec rake build:play`. This will produce a tarball in the `pkg` directory.

### Mustache version

To generate the folder of Mustache templates run `bundle exec rake build:mustache`. This will produce a folder in the `pkg` directory.

### Mustache Inheritance version

There is a [proposal for Mustache to support template inheritance](https://github.com/mustache/spec/issues/38) this is supported in both the `mustache.java` and the `hogan.js` implementations of Mustache.

To generate the tarball of the Mustache Inheritance templates run the `build:mustache_inheritance` rake task. This will produce a tarball in the `pkg` directory.

### Tarball version

To generate the tarball, run the `bundle exec rake build:tar`. This will produce a tarball in the `pkg` directory.

## Publishing

Accepted contributions (pull requests merged into master) will run builds for the Gem, Play and Mustache versions. These will then update the following:

* RubyGems.org
* [alphagov/govuk_template_play](https://github.com/alphagov/govuk_template_play)
* [alphagov/govuk_template_mustache](https://github.com/alphagov/govuk_template_mustache) which updates the [npm package](https://npmjs.org/package/govuk_template_mustache)

## Development

The source files are in the `/source` directory.  The `compile` rake task builds the `/app` contents from these sources.  This process involves the following:

* compiling all stylesheets referenced in `/manifests.yml` to plain CSS (actually css.erb, so the Rails asset pipeline can work in the gem).
* combining all JavaScript files referenced in `/manifests.yml` (using Sprockets)
* copying the images across (including any needed images from the toolkit)

This resulting app directory is included in the gem and hooked in as a Rails engine

### Extra details for the tarball build

The tarball build process takes the compiled template and assets from the `/app` directory, and performs some extra processing:

* it compiles the `*.css.erb` files to plain CSS, replacing all calls to `asset_path` with the relative path to the asset.
  For this reason, all assets referenced in the stylesheets must be stored relative to the stylesheet.
* it compiles the erb layout to plain html.
    * All `asset_path` calls are replaced by the the path to the assets, assuming the assets folder is served from /assets
    * Any `content_for?` calls are assumed to return false
    * yields in the template are removed except for the main layout one which is replaced with an HTML comment.

See the `TemplateProcessor` class for details of this implementation.

## Usage

### Propositional title and navigation

You can get a propositional title and navigation by setting the content for `header_class` to `with-proposition` and `proposition_header` in the form:

    <div class="header-proposition">
      <div class="content">
        <a href="#proposition-links" class="js-header-toggle menu">Menu</a>
        <nav id="proposition-menu">
          <a href="/" id="proposition-name">Service Name</a>
          <ul id="proposition-links">
            <li><a href="url-to-page-1" class="active">Navigation item #1</a></li>
            <li><a href="url-to-page-2">Navigation item #2</a></li>
          </ul>
        </nav>
      </div>
    </div>

This will then create a navigation block which is shown on desktop sized devices but collapsed down on smaller screens.

## Contributing

Please follow the [contribution guidelines](https://github.com/alphagov/govuk_template/CONTRIBUTING.md).
