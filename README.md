This project contains the documentation for API documentation accompanying the source code for SAIFE's endpoint library.

The documentation is built using the Slate library.

##Preparing
You will need Ruby and Bundler.io. From the checkout directory, install all dependncies with:

    bundle install

##Editing
After all dependencies are installed, you can use:

    bundle exec middleman server

to start a local web server displaying the documentation as you update it.

The actual documentation Markdown files are in the source directory, particularly:

    source/index.md
    source/includes

##Exporting for Display
When you are satisfied and want a clean HTML copy for use, use:

    bundle exec middleman build --clean

That will create a directory build that contains the clean, isolated copy.
