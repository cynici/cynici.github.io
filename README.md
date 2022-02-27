# Note to self

The landing page for <https://cynici.github.io> is [index.md](index.md)

My first stab at git-based blogging based on https://www.fast.ai/2020/01/16/fast_template/

## Dependabot alert - update Gem version

Over time issues may be reported by GitHub Dependabot on version-pinned
Gem packages which requires updating [Gemfile](Gemfile) and indirectly,
[Gemfile.lock](Gemfile.lock)

- Clone this repo
- cd repo directory
- chmod 777 .
- rm Gemfile.lock
- `docker run -it --rm -v "$PWD":/srv/jekyll -p "4000:4000" jekyll/builder:stable bash`
- bundle install
- apk add build-base # only necessary if bundle install bombs out
- If it bombs out, figure out other dependent Gems which need to be
  added to Gemfile, then re-run

Finally, check if the site gets served correctly. Inside the container,

```
PAGES_REPO_NWO=cynici/cynici.github.io jekyll serve -d ./_site --watch --force_polling -H 0.0.0.0 -P 4000
```

Browse to see if the site gets served correctly,
<http://localhost:4000/>

