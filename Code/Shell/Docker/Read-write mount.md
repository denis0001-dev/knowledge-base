#docker #shell #files #code

Add the following to the start of your `docker run` command:
```bash
--mount type=bind,src=<host-path>,dst=<container-path>
```
