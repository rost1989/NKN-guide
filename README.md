# NKN node guide

[Harware requirements](#harware-requirements)

[Guide](#guide)

[Updating node](#updating-node)

Special thanks to following resources: 
-[wiki.my-nkn.cloud](https://wiki.my-nkn.cloud/index.php/Mining_NKN_with_Linux#Disk_space);
-[Christian Busch Medium Guide](https://medium.com/nknetwork/setting-up-a-nkn-miner-in-5-minutes-and-run-it-free-for-2-months-with-digitalocean-76bafcd82ae8)

# Harware requirements

Coins to launch miner: 10.1 NKN coins
Operating system: Ubuntu is recommended (works perfectly with Debian)
Memory: 1GB
CPU: 1 CPU
Disk space: ~20 GB
Internet connection speed: 10Mbps up and down is a minimum
IP address: 1 IP version 4 (IPv4)
Ports: TCP/UDP ports 30001-30021

# Guide

1) Update & install newest versions of the packages for OS. And install packages make; curl; git :

```
apt-get update
apt-get upgrade -y
apt-get install make curl git -y
```


2) Add NKN user:

```
adduser nkn
```


3) Give nkn user super user rights adding it to the sudo-group:

```
usermod -aG sudo nkn
```


4) Install latest Go version (installing `go1.17.3`):

```
wget https://golang.org/dl/go1.17.3.linux-amd64.tar.gz

sudo tar -C /usr/local -xvf go1.17.3.linux-amd64.tar.gz
```


5) Update env variables to include `go`:

```
cat >> ~/.bashrc << 'EOF'
export GOPATH=$HOME/go
export PATH=/usr/local/go/bin:$PATH:$GOPATH/bin
EOF

source .bashrc
```


6) Check 'go' version

```
go version
```


7) Create 'nknorg' folder and change directory to that folder: 

```
mkdir -p ~/go/src/github.com/nknorg && cd ~/go/src/github.com/nknorg
```


8) Copy NKN repository to current folder ('nknorg')

```
git clone https://github.com/nknorg/nkn.git
```


9) Open 'nkn' folder

```
cd nkn
```


10) Build the source code with 'make' command

```
make
```
 

11) Copy existing 'config.mainnet.json' file to 'config.json'

```
cp config.mainnet.json config.json
```


12) Create separate wallet for rewards using https://wallet.nkn.org/.
NOTE: Save `secret seed` and related account details (`public key, password`). You will need to use `secret seed & password` to recover your wallet.


13) Create local wallet using command below.
NOTE: Save file `wallet.json` and related account details (`public key, password`). You will need to use `wallet.json & password` to open your wallet using website https://wallet.nkn.org/ and to run NKN node.

```
./nknc wallet -c
```


14) Send 10.1 mainnet NKN coins to a local wallet (created in above paragraph with command `./nknc wallet -c`)


15) Go to home folder and create service file to run NKN node in the background:

```
cd ~

sudo tee /etc/systemd/system/nkn.service > /dev/null <<EOF
[Unit]
Description=nkn

[Service]
User=nkn
WorkingDirectory=/home/nkn/go/src/github.com/nknorg/nkn
ExecStart=/home/nkn/go/src/github.com/nknorg/nkn/nknd -p <YOUR_WALLET_PASSWORD>
Restart=always
RestartSec=3
LimitNOFILE=500000

[Install]
WantedBy=default.target
EOF
```


16) Load service and start

```
sudo systemctl daemon-reload
sudo systemctl enable nkn
sudo systemctl start nkn
```


17) Check status of service

```
sudo systemctl status nkn
```


18) Check logs:

```
journalctl -f -u nkn
```


# Updating node


1) Stop nkn service:

```
sudo systemctl stop nkn
```


2) Go to nkn directory:

```
cd /home/nkn/go/src/github.com/nknorg/nkn
```


3) Update current folder with up to date github repository:

```
git pull
```


4) Build the source code with 'make' command:

```
make
```


5) Start nkn service:

```
sudo systemctl start nkn
```


6) Check logs that everything works fine:

```
journalctl -f -u nkn
```
