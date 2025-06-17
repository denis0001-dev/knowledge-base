#code #shell #guide #server
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
echo "Verifying key..."
ls -l ~/.ssh/id_*.pub
echo "Copying key..."
ssh-copy-id user@host
```

> [!IMPORTANT]
> If you don't see anything between **Verifying key...** and **Copying key...**, something went wrong.

Replace `user` with the actual user on the host and replace `host` with the actual host IP address.
SSH may ask you to enter the user's password for the last time.
## Proxy
To connect to the local network of the remote machine, run SSH with the `-D` flag and connect to `127.0.0.1:80` as a SOCKS proxy.