INSTALLATION
============
Fork this repo in GitHub, then:

    $ mkdir myblog
    $ cd myblog
    $ git clone https://github.com/<my_username>/my-summer-of-code.git .

Choose the right Ruby:

    $ rvm use 2.1.1

Install bundler (if not installed already):

    $ gem install bundler

Install al dependencies:

    $ bundle install

Build and run:

    $ jekyll build && jekyll serve


GRUNT
=====
Grunt is a tool to compile less files, minimize css and optimize images.
You can use it also to generate templates for new posts or pages.

Grunt requires Node.js.
Download Node.js from http://nodejs.org/ and install it, then:

    $ npm install -g grunt-cli


CONFIGURATION AND DEPLOY TO GITHUB PAGES
========================================
You want to deploy your blog w/ so-simple-theme to GitHub.
You first need to create a branch named gh-pages in your repository.
If you repository is named myrepo, the blog will be available at:
myusername.github.io/myrepo

You also need to use some specific settings in Jekyll:
http://jekyllrb.com/docs/github-pages/

In _config.yml:

- comment out:  
    `url:`
- add:  
    `baseurl:          /myrepo`
- replace in any file: site.url  
    `with: site.baseurl`
- rebuild:  
    `$ grunt recess`  
    `$ jekyll buil`
- run:  
    `$ jekyll serve --baseurl ''`

Push the repo to githu and it will be build automatically.


EDITING THEME
=============
You want to edit the .less files in assets/less.  
Then rebuild css and less with:

    $ grunt recess

Or rebuild everything with:

    $ grunt


ADDING POSTS
============
You can copy/paste posts.  
Or you can use Grunt.

    $ rake new_post
    $ rake new_page


INTERACTIVE BUILD
=================
In one shell:

    $ grunt watch

In another shell:

    $ jekyll serve --watch --drafts --baseurl ''


TABLE OF CONTENTS
=================
Requirements:

    $ npm install -g doctoc

Run:

    $ doctoc _posts/2014-05-04-deployment-sketch.md