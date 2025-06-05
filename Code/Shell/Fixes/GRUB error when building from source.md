#shell #fix #code 

Avoid this error due to `extra_deps.lst` missing from source tarball:

```
make[3]: *** No rule to make target 'grub-core/extra_deps.lst', needed by 'syminfo.lst'.  Stop.
```

Run this command in the GRUB source directory:
```bash
echo "depends bli part_gpt" > grub-core/extra_deps.lst
```

_source: https://old.calculate-linux.org/packages/sys-boot/grub/grub-2.12-r4.ebuild_