# GSF Documentation Portal

The GSF Documentation Portal provides documentation and diagrams about D&D AU systems.

This site aims to provide:

- high level architectural guidance on system composition
- a starting point for developers on how to use, extend and maintain the systems
- maintainable diagrams, such as sequence diagrams, class diagrams and flow diagrams. This site integrates tools such as Mermaid and PlantUML so that diagrams can be described and stored as code. 

Ths portal is based on [Docsy][]. Docsy is a [Hugo theme][] for technical documentation sites, providing easy
site navigation, structure, and more.

In this project, the Docsy theme is included as a Git submodule:

```bash
$ git submodule
...<hash>... themes/docsy (remotes/origin/HEAD)
```

You can find detailed theme instructions in the [Docsy user guide][].

## Development

Clone this repository, including submodules, to your local machine:

```bash
git clone --recurse-submodules --depth 1 ssh://git@bitbucket.globalx.com.au:7999/ar/gsf-docsy.git

cd themes/docsy
npm install
```
Until this site is upgraded to use Go modules, `npm install` is required to add bootstrap & font awesome. See https://github.com/google/docsy/discussions/950

Note: If you forgot to clone with submodules, run this to pull the submodules into an already cloned repo:

```bash
git submodule update --init --recursive
```

To pull latest submodule changes:

```bash
git submodule update --recursive --remote
```

**Editing Content**

The documentation content starts from the [content/en/docs](./content/en/docs) directory

**Editing Diagrams**

Simple diagrams can be included inline using PlantUML or Mermaid syntax.

More complex diagrams can be authored using [diagrams.net](https://diagrams.net). 

To create a new diagrams.net diagrams:

* Go to https://app.diagrams.net/
* Choose New Diagram
* Save as SVG (Editable Vector Diagram) into the correct location in this repo on your local. The app will sync changes to that file as you edit

To edit an existing diagram:

* On the hosted docsy portal, click the Edit button
* Make the changes, and Save
* Copy the downloaded .svg into this repo

See [Docsy diagrams documentation][https://www.docsy.dev/docs/adding-content/diagrams-and-formulae/] for more details.

### Running in a container locally

You can run this site inside a [Docker](https://docs.docker.com/)
container, the container runs with a volume bound to the `gsf-docsy`
folder. This approach doesn't require you to install any dependencies other
than [Docker Desktop](https://www.docker.com/products/docker-desktop) on
Windows and Mac, and [Docker Compose](https://docs.docker.com/compose/install/)
on Linux.

1. Build the docker image

   ```bash
   docker-compose build
   ```

1. Run the built image

   ```bash
   docker-compose up
   ```

   > NOTE: You can run both commands at once with `docker-compose up --build`.

1. Verify that the service is working.

   Open your web browser and type `http://localhost:1313` in your navigation bar,
   This opens a local instance of the docsy-example homepage. You can now make
   changes to the docsy example and those changes will immediately show up in your
   browser after you save.

### Cleanup

To stop Docker Compose, on your terminal window, press **Ctrl + C**.

To remove the produced images run:

```console
docker-compose rm
```

For more information see the [Docker Compose
documentation](https://docs.docker.com/compose/gettingstarted/).

[docsy user guide]: https://docsy.dev/docs
[docsy]: https://github.com/google/docsy
[hugo theme]: https://www.mikedane.com/static-site-generators/hugo/installing-using-themes/

## Deployment

This site is deployed to kubernetes via [flux](https://bitbucket.globalx.com.au/projects/CLD/repos/flux-gsf/browse). It runs behind [Kong routes](https://bitbucket.globalx.com.au/projects/KONG/repos/kong-configuration-deck/browse) available at `/docsy/` e.g. https://dev-online.globalx.com.au/docsy.

The docker image is built by [Team City](https://teamcity.globalx.com.au).

To reproduce the production setup locally:

1. Build and run the docker container. The site will be listening on http://localhost:1314/. Due to the production build requiring /docsy as the base url, it won't work directly on this URL.

```
docker build -f deploy.Dockerfile -t gsf-docsy .
docker run -p 1314:80 gsf-docsy
```

2. Modify Kong rules.

* Change the Kong service host to your local IP, port to 1314 and protocol to http.
* Change route Strip Path to True

The site will now be accessible on https://local-online.globalx.com.au/docsy.

3. Any changes made to the config or content will require the container to be rebuilt (repeat step 1).

## Comparing Local Development and Production workflows

The base config is in [./config/\_default/config.toml](). When running via [./docker-compose](), it sets the HUGO_ENV to development.

The production config is in [./config/production/config.toml](). When building the [deploy.Dockerfile](), it sets the HUGO_ENV to production which forces the Production config values to override the default config values.

References:

- Hugo configuration: https://gohugo.io/getting-started/configuration/#configuration-directory
- Details on the ONBUILD base container used for Production: https://github.com/klakegg/docker-hugo#using-onbuild-image
- Details on the standard base container used for development: https://github.com/klakegg/docker-hugo#configuration
