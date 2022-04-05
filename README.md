# GSF Documentation Portal

The GSF Documentation Portal provides documentation and diagrams about D&D AU systems.

This site aims to provide:

- high level architectural guidance on system composition
- a starting point for developers on how to use, extend and maintain the systems
- maintainable diagrams, such as sequence diagrams, class diagrams and flow diagrams. This site integrates tools such as Mermaid and PlantUML so that diagrams can be described and stored as code. See [Docsy diagrams documentation][https://www.docsy.dev/docs/adding-content/diagrams-and-formulae/] for more details.

Ths portal is based on [Docsy][]. Docsy is a [Hugo theme][] for technical documentation sites, providing easy
site navigation, structure, and more.

In this project, the Docsy theme is included as a Git submodule:

```bash
$ git submodule
...<hash>... themes/docsy (remotes/origin/HEAD)
```

You can find detailed theme instructions in the [Docsy user guide][].

## Deployment

TODO, once this is deployed into a higher environment

## Development

Clone this repository, including submodules, to your local machine:

```bash
git clone --recurse-submodules --depth 1 ssh://git@bitbucket.globalx.com.au:7999/ar/gsf-docsy.git
```

You can now edit your own versions of the site’s source files.

If you want to do SCSS edits and want to publish these, you need to install `PostCSS`

```bash
npm install
```

## Running in a container locally

You can run this site inside a [Docker](https://docs.docker.com/)
container, the container runs with a volume bound to the `docsy-example`
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
