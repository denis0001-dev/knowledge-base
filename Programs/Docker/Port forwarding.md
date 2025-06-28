#docker #cli-option #ports #net #shell #code #containers #programs 
## Syntax
```bash
-p <host port>:<container port>
```
## Examples

```bash
docker run -p 8000:80 -it ubuntu # Maps port 80 in the container to 8000 on the host
```

```bash
docker run -p 127.0.0.1:5000:80 -it ubuntu # Maps port 80 in the container to 5000 on the host (unavailable for other hosts)
```