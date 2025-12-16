# helm-charts
The custom helm charts I use in my HomeLab

# Operating

To add a Chart/update a chart:
```
helm package cloudreve # Your chart
helm repo index . --url https://waelmio.github.io/helm-charts
git add .
git commit -m "Updating Helm Charts"
git push
git checkout gh-pages
git merge master
git push
git checkout master
```

[Source](https://nsalexamy.github.io/service-foundry/pages/documents/blog/github-helm-repo/)