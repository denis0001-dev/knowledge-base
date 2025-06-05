#shell #fix #code #video

This method significantly reduces the size of a 2K video by lowering the resolution to just 1K and compressing it with `ffmpeg`.

```bash
ffmpeg -i <input> -vf "pad=ceil(iw/2)*2:ceil(ih/2)*2" <output>
```