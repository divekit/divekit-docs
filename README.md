# The Divekit Documentation

This is the place for documentation regarding the Divekit.
All documentation should be done here as of now.

The documentation is based [Hugo](https://gohugo.io/) with the [Docsy theme](https://www.docsy.dev/).

## Running locally

**Prerequisites:**
* [Node.js](https://nodejs.org/en/)
* [Hugo](https://gohugo.io/getting-started/quick-start/)
or
* [Docker](https://www.docker.com/) and [Docker Compose](https://docs.docker.com/compose/)

Clone the repository (Note that Docsy is provided over a git submodule that needs to be initialized)
```sh
# HTTP
git clone --recurse-submodules https://github.com/divekit/divekit-docs.git
# SSH
git clone --recurse-submodules git@github.com:divekit/divekit-docs.git

cd ./docs
```

### Using Hugo CLI

After the repository has been cloned, you would need to install dependencies from node:
```sh
npm ci
```
Now you can start the hugo server by using
```sh
hugo serve
```

The site is now available at [http://localhost:1313/](http://localhost:1313/) 

### Using Docker
 
Simply run
```sh
# Docker Desktop
docker compose up
# docker-compose
docker-compose up
```

The site is now available at [http://localhost:1313/](http://localhost:1313/) 

### Troubleshooting

As you run the website locally, you may run into the following error:

```
➜ hugo server

INFO 2021/01/21 21:07:55 Using config file: 
Building sites … INFO 2021/01/21 21:07:55 syncing static files to /
Built in 288 ms
Error: Error building site: TOCSS: failed to transform "scss/main.scss" (text/x-scss): resource "scss/scss/main.scss_9fadf33d895a46083cdd64396b57ef68" not found in file cache
```

This error occurs if you have not installed the extended version of Hugo.
See this [section](https://www.docsy.dev/docs/get-started/docsy-as-module/installation-prerequisites/#install-hugo) of the user guide for instructions on how to install Hugo.

Or you may encounter the following error:

```
➜ hugo server

Error: failed to download modules: binary with name "go" not found
```

This error occurs if you have not installed the `go` programming language on your system.
See this [section](https://www.docsy.dev/docs/get-started/docsy-as-module/installation-prerequisites/#install-go-language) of the user guide for instructions on how to install `go`.


## Adding Content to the documentation

This guide is just a quick overview, refer to the [Docsy documentation](https://www.docsy.dev/docs/) for more details.

The whole content is located under the `content/en` location. Refer to the structure there to get an overview.
You can simply add markdown files with a simple [Frontmatter](https://www.docsy.dev/docs/adding-content/content/).

Refer to the [Linking tips](https://www.docsy.dev/docs/best-practices/site-guidance/#linking) on how to properly link content if you're experiencing troubles with it. In the pipeline runs a HTML Tester to test on valid links and more things to guide you there.
