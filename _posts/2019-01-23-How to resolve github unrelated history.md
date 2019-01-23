---
layout:     post
title:      "How to resolve github unrelated history?"
subtitle:    怎样解决github历史冲突的问题
date:       2019-01-23
author:     Lyon Ling
header-img: img/post-bg-githubhistory.png
catalog: true
tags:
    - Github
---



I have met a problem several times before. When we build a new repository on GitHub, the recommended  steps are 

1. Build a new empty repo on GitHub
2. Init local git directory
3. Make the first commit
4. Bind the remote repository URL
5. Push your commits

While some time, the actual is we would init github repo with `Readme.md` and `.gitignore`, and forget to make the pull first and make the first commit. So here comes the problem, it will raise `fatal: refusing to merge unrelated histories`. 

At first, I always delete that dir and re-init the git dir.

After searching on the Internet, I found a solution on `Stackoverflow`.

One `git pull` option helps.

```
git pull origin branchname --allow-unrelated-histories
```

Through `--allow-unrelated-histories`, we can force pull and merge unrelated changes later, if all changes were committed.

