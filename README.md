To publish the website online, just committing is enough as a CI job (see ../.build.yml ) will take care of it.

For testing the site locally :

```sh
zola serve
```

To generate the website and deploy it 

```sh
zola build
tar -C public -cvz . > site.tar.gz
hut pages publish -d scut.srht.site site.tar.gz
```

Note : slides have been generated before hand and stored in static/slides with slidev. There can be re-generated with 

```sh
cd ../slides/fosdem2024
npm run build -- --base /slides/fosdem2024 --out ../../site/static/slides/fosdem2024
```
