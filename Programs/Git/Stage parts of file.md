#programs #git #code #files #shell 

You can use:

```sh
git add --patch <filename>
```

or for short:

```sh
git add -p <filename>
```

Git will break down your file into what it thinks are sensible "hunks" (portions of the file). It will then prompt you with this question:

    Stage this hunk [y,n,q,a,d,/,j,J,g,s,e,?]?

Here is a description of each option:

  - <kbd>y</kbd> stage this hunk for the next commit
  - <kbd>n</kbd> do not stage this hunk for the next commit
  - <kbd>q</kbd> quit; do not stage this hunk or any of the remaining hunks
  - <kbd>a</kbd> stage this hunk and all later hunks in the file
  - <kbd>d</kbd> do not stage this hunk or any of the later hunks in the file
  - <kbd>g</kbd> select a hunk to go to
  - <kbd>/</kbd> search for a hunk matching the given regex
  - <kbd>j</kbd> leave this hunk undecided, see next undecided hunk
  - <kbd>J</kbd> leave this hunk undecided, see next hunk
  - <kbd>k</kbd> leave this hunk undecided, see previous undecided hunk
  - <kbd>K</kbd> leave this hunk undecided, see previous hunk
  - <kbd>s</kbd> split the current hunk into smaller hunks
  - <kbd>e</kbd> manually edit the current hunk
    - You can then edit the hunk manually by replacing `+`/`-` by `#` (thanks [veksen](https://stackoverflow.com/users/1732521/veksen))
  - <kbd>?</kbd> print hunk help

If the file is not in the repository yet, you can first do `git add -N <filename>`. Afterwards you can go on with `git add -p <filename>`.

Afterwards, you can use:

  - `git diff --staged` to check that you staged the correct changes
  - `git reset -p` to unstage mistakenly added hunks
  - `git commit -v` to view your commit while you edit the commit message.

Note this is far different than the `git format-patch` command, whose purpose is to parse commit data into a `.patch` files.

Reference for future: [Git Tools - Interactive Staging][1]


  [1]: https://git-scm.com/book/en/v2/Git-Tools-Interactive-Staging
