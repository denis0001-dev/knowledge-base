#docker  #cli-option #shell #files #code #containers #programs 
## Syntax
```bash
--mount type=bind,src=<host-path>,dst=<container-path>
```
## Examples
```bash
docker run --mount type=bind,src=.,dst=/root/dir -it ubuntu
```