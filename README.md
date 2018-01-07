# George's Blog

Just about what I learn what I think and what I do.

##### 1.Init

```
git clone https://github.com/georgezouq/geonoter.git
cd geonoter
npm install
```

##### 2.Modify
Modify `_config.yml` file with your own info.
Especially the section:

```
deploy:
  type: git
  repo: https://github.com/georgezouq/georgezouq.github.io.git
  branch: master
```

Replace with your own repo!

##### 3. Writting / Serve / Deploy

```
hexo new post IMAPOST
hexo serve // run hexo in local environment
hexo clean && hexo deploy // hexo will push the static files automatically into the specifig branch(gh-pages) of your repo!
```

##### 4.Enjoy!

Please [**Star**](https://github.com/georgezouq/geonoter)
