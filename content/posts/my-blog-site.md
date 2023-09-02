---
title: "My Blog Site"
date: 2022-01-21T21:32:14+05:30
tags: ["Hugo", "Github", "Pipeline", "CI/CD", "Github Actions", "Github Pages"]
author: ["Me"]
showToc: true
TocOpen: false
hidemeta: false
comments: false
summary: "Setup my profile & blog site using Hugo and github"
description: "Setup my profile & blog site using Hugo and github"
disableHLJS: true
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---


### Installing Hugo 
[Hugo](https://gohugo.io/) is a static site generator written in go. We can install hugo easily using any of the package manager available for the different platforms. Since I am using macOS the below command worked for me.
We can get the detailed installation steps from the hugo [quick-start](https://gohugo.io/getting-started/quick-start/) guide.

```bash
brew install hugo
```


### Creating a new Hugo Site
Created a new repository which will act as the source repository for the blog site. And use the **`new`** hugo command to generate a new site.

```bash
hugo new site <SITE NAME>
```

Cloning the new repository, and creating a new hugo site in the cloned repository, **`--force`** option is used to force the site creation when the directory is not empty. **`-f yml`** option is used to create the hugo repository using the **config.yml** instead of **config.toml**.

```bash
hugo new site . --force -f yml
```


### Updating the default archetypes
Archetypes contain the default template which will be used while creating new posts/blogs etc using the hugo command. Hence, updating the below config file to the **`default.md`**. Read more on [archetypes](https://gohugo.io/content-management/archetypes/).

```yml
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
tags: []
author: ["Me"]
showToc: false
TocOpen: false
hidemeta: true
comments: false
summary: ""
description: ""
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
draft: true
---

```


### Add a theme for the Hugo site
We will be using the **[hugo-PaperMod](https://github.com/adityatelange/hugo-PaperMod.git)** theme for our blog site. The themes can be imported by adding the themes as sub-modules to the existing repository. Read more [quick-start](https://gohugo.io/getting-started/quick-start/).
```git
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod --depth=1
```


### Update the config file
We need to update the config file with the needed configuration for the theme.

```yml
baseURL: "your base URL"
languageCode: en-us
title: "title"
theme: "PaperMod"
paginate: 5

params:
  env: production # to enable google analytics, opengraph, twitter-cards and schema.
  title: title
  description: ""
  keywords: []
  author: [Me]
  DateFormat: "January 2, 2006"
  defaultTheme: auto # dark, light, auto
  disableThemeToggle: false

  ShowReadingTime: true
  ShowShareButtons: true
  ShowPostNavLinks: true
  ShowBreadCrumbs: true
  ShowCodeCopyButtons: true
  disableSpecial1stPost: true
  disableScrollToTop: false
  comments: false
  hidemeta: false
  hideSummary: false
  showtoc: false
  tocopen: false

  # home-info mode
  homeInfoParams:
    Title: "Title"
    Content: >
      - Lorem Ipsum is simply dummy text of the printing and typesetting
        industry. Lorem Ipsum has been the industry's standard dummy text
        ever since the 1500s, when an unknown printer took a galley of type
        and scrambled it to make a type specimen book. It has survived not
        only five centuries, but also the leap into electronic typesetting,
        remaining essentially unchanged. 

      - It was popularised in the 1960s with
        the release of Letraset sheets containing Lorem Ipsum passages, and
        more recently with desktop publishing software like Aldus PageMaker
        including versions of Lorem Ipsum.

  socialIcons:
    - name: email
      url: "mailto://abc@gmail.com"
    - name: twitter
      url: "https://twitter.com/"
    - name: stackoverflow
      url: "https://stackoverflow.com"
    - name: github
      url: "https://github.com/"
    - name: gitlab
      url: "https://gitlab.com/"
    - name: linkedin
      url: "https://linkedin.com/"

  cover:
    hidden: true # hide everywhere but not in structured data
    hiddenInList: true # hide on list pages and home
    hiddenInSingle: true # hide on single page
    responsiveImages: false # For responsive images

menu:
  main:
    - identifier: Posts
      name: Posts
      url: /posts/
      weight: 20
    - identifier: tags
      name: Tags
      url: /tags/
      weight: 20
```

### Local testing using hugo server
For local testing of the static site we can use the local server which comes with the Hugo for testing.
```bash
hugo server

or 

hugo server -D
```

### Adding a new post to the blog/posts site
We can use the `new` command to create a new post. It will create a new post named `my-first-post` in the `content/posts` directory and the new file which is created will have the base template as of `default.md` archetypes created and updated earlier.

```bash
hugo new posts/my-first-post.md
```


### Generate the static files for deployment
We can use the `hugo` command for generating/building the static site which can later be used for deployment.
```bash
hugo
```
Please refer the [hugo documentation](https://gohugo.io/commands/hugo/) for all the flags and options or refer the man pages for hugo.


### Implementing the CI/CD pipeline for automatic build & deployment
We will be using the `Github actions` to build and deploy the static site in `Github pages`.

#### Generate Github tokens
We need to generate a new personal access token from [Github Tokens](https://github.com/settings/tokens) and use the same token in the repository which will be pushing the static build site for deployment to the external repository. Please refer this [site](https://ruddra.com/hugo-deploy-static-page-using-github-actions/#step-4-create-github-token).

#### Create the workflow
We need to enable the github actions for automatically deploying the site. We will be deploying the site to an external repository. Please refer [actions-gh-pages](https://github.com/peaceiris/actions-gh-pages#%EF%B8%8F-deploy-to-external-repository-external_repository). 

```yml
name: project-build-deployment

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: repository-checkout
        uses: actions/checkout@v2      
      
      - name: update-submodule-theme-repo
        run: git submodule update --init --recursive
      
      - name: install-hugo
        uses: peaceiris/actions-hugo@v2

      - name: build-project
        run: hugo -D --minify

      - name: deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          personal_token: ${{ secrets.TOKEN }}
          external_repository: XXXXXX/YYYYYYYY
          publish_dir: ./public
          user_name: XXXXXX
          user_email: XXXXXX@XXXXXX.com
          publish_branch: main  

```

#### Enable the github pages for the external repository
Enable the github pages in the <PROJECT>/Settings/Pages.


### Conclusion
We just need to update the markdown files for the new posts and push it. And the CI/CD pipeline will automatically build and deploy the static site.
