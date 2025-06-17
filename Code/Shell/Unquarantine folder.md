#shell #code #apple #fix #files 

Run this inside the directory you want to unquarantine

```bash
find . -type f -print0 | xargs -0 xattr -d com.apple.quarantine
```