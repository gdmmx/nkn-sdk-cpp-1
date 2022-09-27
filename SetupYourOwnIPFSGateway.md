# Setup Your Own IPFS Gateway

## Create VPS on cloud
1. Create Instance on DO/AWS
2. Configure a security group with the following options:
```
Custom TCP: 8080
Custom TCP: 4001–4002
```
3. SSH into your EC2 instance

## Install “go-ipfs”
4. Select a Linux binary from https://dist.ipfs.io/go-ipfs/
e.g. https://dist.ipfs.io/go-ipfs/v0.14.0/go-ipfs_v0.14.0_linux-amd64.tar.gz
5. Unzip: `tar -xvzf go-ipfs_v0.14.0_linux-amd64.tar.gz`
6. Navigate to the correct directory: `cd go-ipfs`
7. Run the install script: `sudo bash install.sh`
8. Check that IPFS was installed correctly by running: `ipfs --version`

## Initialize the IPFS repository
9. Create a directory: `mkdir /home/ubuntu/data/ipfs`
10. Add the path to your Bash profile:
```
echo 'export IPFS_PATH=/home/ubuntu/data/ipfs >> ~/.profile'
```
11. Reload your bash profile: `source ~/.profile`
12. Initialize the IPFS repository: `ipfs init --profile server`
13. Open the Gateway on port 8080: `ipfs config Addresses.Gateway /ip4/0.0.0.0/tcp/8080`

## Set up an IPFS daemon
14. Create a systemd service: `sudo vim /lib/systemd/system/ipfs.service`
15. Add the following contents to the file:
```
[Unit]
Description=ipfs daemon
[Service]
ExecStart=/usr/local/bin/ipfs daemon --enable-gc
Restart=always
User=ubuntu
Group=ubuntu
Environment=”IPFS_PATH=/home/ubuntu/data/ipfs”
[Install]
WantedBy=multi-user.target
```
16. Save the file
17. Restart the daemon: `sudo systemctl daemon-reload`
18. Enable the service: `sudo systemctl enable ipfs.service`
19. Start the service: `sudo systemctl start ipfs`
20. Check the status:  `sudo systemctl status ipfs`

## Make your IPFS Writable
21. Edit IPFS configuration: `sudo vim /home/ubuntu/data/ipfs/config`
22. Set json key .Gateway.Writable = true
```
{
  ......
  "Gateway": {
    "APICommands": [],
    "HTTPHeaders": {
      "Access-Control-Allow-Headers": [
        "X-Requested-With",
        "Range",
        "User-Agent"
      ],
      "Access-Control-Allow-Methods": [
        "GET",
        "POST",
        "PUT"
      ],
      "Access-Control-Allow-Origin": [
        "*"
      ]
    },
    "NoDNSLink": false,
    "NoFetch": false,
    "PathPrefixes": [],
    "PublicGateways": null,
    "RootRedirect": "",
    "Writable": true
  },
  ......
}
```
23. Restart ipfs: `sudo systemctl restart ipfs`

## FAQ
