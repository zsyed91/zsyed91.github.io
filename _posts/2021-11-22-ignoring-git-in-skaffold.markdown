---
layout: post
title: Ignoring .git directory when using skaffold dev 
summary: How to ignore .git directory in skaffold 
toc: true
---

I was using `skaffold dev` to do my development work locally. Skaffold allows you to run your kubernetes cluster locally
without requiring building and pushing your container image anywhere. Instead it builds it locally 
and [deploys patched changes](https://skaffold.dev/docs/pipeline-stages/filesync/#manual-sync-mode)
as you edit the files. I was running into an issue where I set up my sync directive to listen to my ruby and javascript 
files (rails stack) and map them over. However, for all other file extensions, skaffold by default will attempt to rebuild 
the image and restart it instead of hot patching the files only.

I noticed, whenever interacting with git, it would cycle through that entire process and generally slow things down
for a minute or so. This was both annoying and frequent enough for me to investigate if its possible to ignore
certain files and directories. The good thing is that not only is it supported but its not even required in skaffold.
Skaffold honors anything within the `.dockerignore` file. So to fix the issue, I simply created one and added the .git folder there.

My `.dockerignore` file looks like this:

```
# Ignore the .git folder entirely
.git
log/
*.tmp
*.temp
```
