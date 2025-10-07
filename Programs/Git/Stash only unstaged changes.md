#git #code #shell #programs #files 

`git stash` or `git stash push` has an option [`--keep-index`](https://git-scm.com/docs/git-stash#Documentation/git-stash.txt---keep-index) that does exactly what you need, so run:

```
git stash --keep-index
```