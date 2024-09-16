To publish the website online, just committing is enough as a CI job (see ../.build.yml ) will take care of it.

For testing the site locally :
```sh
zola serve
```

To generate the website and deploy it, use a Github action to generate HTML on a dedicated branch according to [https://www.getzola.org/documentation/deployment/github-pages/](the official instructions). Then setup Github page to deploy it from this branch (I use gh-pages).

Note : slides have been generated before hand and stored in static/slides with slidev. There can be re-generated with 

```sh
cd ../slides/fosdem2024
npm run build -- --base /slides/fosdem2024 --out ../../site/static/slides/fosdem2024
```
