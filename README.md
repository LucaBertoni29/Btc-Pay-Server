# BTC-Pay Server

## Installazione delle dipendenze

To run the BTCPayServer you will need to install .NET Core SDK, NBXplorer and PostgreSQL.

Install .NET Core SDK
Go to your Downloads folder or any other folder that you use to store temporary files:

```bash
cd ~/Downloads
```
and download .NET 6.0 SDK:

```bash
wget https://packages.microsoft.com/config/ubuntu/22.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
rm packages-microsoft-prod.deb
```

Update the package cache and install .NET 6.0:

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https
sudo apt-get update
sudo apt-get install -y dotnet6
```

## Install PostgreSQL

I wasn't able to set up BTCPayServer with MySQL/MariaDB so I was forced to use PostgreSQL.

Install it first and open a shell prompt as a newly created system user:

```bash
sudo apt install postgresql postgresql-contrib
sudo -i -u postgres
```

Create a new database user:

```bash
createuser --pwprompt --interactive
```

Type in the following (you can change satoshi, to any username you like):

Create a new database user and set roles
Create 2 new databases:

```bash
createdb -O satoshi btcpayserver
createdb -O satoshi nbxplorer
```

If you see no errors exit the shell:

```bash
exit
```

## Install NBXplorer

We'll need to install NBXplorer In order to track incoming on-chain transactions.

Clone the repository and build the code:

```bash
git clone https://github.com/dgarage/NBXplorer
cd NBXplorer
./build.sh
```

Create the data folder:

```bash
mkdir -p ~/.nbxplorer/Main
cd ~/.nbxplorer/Main
```

and a new config file:

```bash
nano settings.config
```

Open the file with the editor of your choice and add the following lines:

```bash
btc.rpc.auth=<bitcoind rpc user>:<bitcoind rpc password>
port=24445
mainnet=1
postgres=User ID=<your db user>;Password=<your db password>;Host=localhost;Port=5432;Database=nbxplorer;
```

Make sure to use your bitcoind's rpc login credentials. You can find them with the command:

```bash
cd ~/NBXplorer
./run.sh
```

If so, hit the ctrl+c to kill the process and download the Systemd service:

```bash
cd /etc/systemd/system
sudo wget https://gist.githubusercontent.com/mariodian/de873b969e70eca4d0a7673efd697d0a/raw/acfc70c5694cd53d8a3df7ff54a35ff2caba7532/nbxplorer.service
```

Edit the file according to your environment.

Enable the service, start it and check the status:

```bash
sudo systemctl enable nbxplorer.service
sudo service nbxplorer start
sudo service nbxplorer status
```

If you see no errors go to the next step.

## Install BTCPayServer

Clone the repository and build the code:

```bash
git clone https://github.com/btcpayserver/btcpayserver.git
cd btcpayserver
./build.sh
```

Create the data folder:

```bash
mkdir -p ~/.btcpayserver/Main
cd ~/.btcpayserver/Main
```

and a new config file:

```bash
nano settings.config
```

```bash
network=mainnet
port=23001
bind=0.0.0.0
chains=btc
BTC.explorer.url=http://127.0.0.1:24445
```

Change the highlighted "variables" according to your settings.

Check if everything is working correctly.

```bash
cd ~/btcpayserver
./run.sh
```

If so, hit ctrl+c to kill the process and download the Systemd service:

```bash
cd /etc/systemd/system
sudo wget https://gist.githubusercontent.com/mariodian/07bb13da314e2a321784b380f543651a/raw/6cef554d9e8311e683a017d5e63a07822dee7642/btcpayserver.service
```

Edit the file based on your environment.

Attiva il servizio e verifca che tutto sia funzionante

```bash
sudo systemctl enable btcpayserver.service
sudo service btcpayserver start
sudo service btcpayserver status
```
