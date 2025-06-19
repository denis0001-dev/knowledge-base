#code #shell #guide #server #net 
## Setup on the server
```bash
sudo apt update
sudo apt install openssh-server
sudo systemctl enable --now ssh
```
## Key pair for seamless authentication 
To create the keys run the following commands:
```bash
ssh-keygen -t rsa
ssh-copy-id user@host
```

Replace `user` with the actual user on the host and replace `host` with the actual host IP address.
SSH may ask you to enter the user's password for the last time.
## Proxy
#proxy #ports 
To connect to the local network of the remote machine, run SSH with the `-D 9000` flag and connect to `127.0.0.1:9000` as a SOCKS proxy.