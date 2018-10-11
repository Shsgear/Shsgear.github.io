# My Blog

- ## Info

### Branch Usage

1. default branch:  `blog-src`, for hexo blog resource and source code.

2. github pages: github pages must be built from the `master` branch. `master` branch is for built posts files, CNAME file ...etc

- ## Travis Ci

``` yaml
language: node_js
node_js: stable

install:
  - npm install


script:
  - hexo g

after_script:
  - cd public
  - git init
  - git config user.name "shsgear"
  - git config user.email "shsgear@qq.com"
  - git add .
  - git commit -m "update blog"
  - git push --force --quiet "https://${githubBlog}@${GH_REF}" master:master

#safe list
branches:
  only:
    - blog-src

env:
 global:
   - GH_REF: github.com/Shsgear/Shsgear.github.io.git
```

[Travis-CI-Contol-Board](https://travis-ci.com/Shsgear/Shsgear.github.io)

### what travis do:

### 1. prepare node environment

``` yaml
language: node_js
node_js: stable
```

use the stable version of nodejs.

### 2. install dependencies

### 3. execute script (here is `hexo g`)

generate built post files

### 4. push built files in  `/public` to remote repository

> git push --force --quiet "https://${githubBlog}@${GH_REF}" master:master

`githubBlog` , `GH_REF` are environment variables defined by travis control board or your self.

`githubBlog` is defined in [Travis-CI-Contol-Board](https://travis-ci.com/Shsgear/Shsgear.github.io) by ourself. it's value was is a github AccessToken. See `Github > User > Settings > Developer settings > Personal access tokens`  

`GH_REF` is defined in `.travis.yaml`. The value of this variable is a web URL of this remote repository

### Additions

`branch` in `.travis.yml`  Specify which branches to build using a safelist, or blocklist branches that you do not want to be built

- ## Link

### [http://shsgear.top](http://shsgear.top)

### [http://shsgear.github.io/](https://shsgear.github.io/)