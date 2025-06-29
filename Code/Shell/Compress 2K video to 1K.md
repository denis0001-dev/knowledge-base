#shell #fix #code #video

```bash
ffmpeg -i <input> -vf "pad=ceil(iw/2)*2:ceil(ih/2)*2" <output>
```