---
layout: post
title: Git Your Code Right
---

There was once a time when you looked into a folder of source code and thought “My goodness, what’s going on here?” In your astonishment, all you could wonder about was what the codebase did. Now, with the advent of source control, we can ask “How did this codebase come about?” alongside other question which lead to more interesting insights. This curiosity is what has led me to look into [git aliases][1]

Git Aliases are configurable shortcuts.

Let’s say you want to check the status of your git repo. There is a command for this: “git status”. But… 😩 who wants to type all those characters when you can get away with typing just five: “git s”. To set this up, add this to your global [git-config][2].

{% gist 39e48546073767fb183b350a11ad515b %}



  [1]: https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases
  [2]: https://git-scm.com/docs/git-config
