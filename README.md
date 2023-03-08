# BTC-Pay Server

Per installare il BTC Pay Server è necessario avere un nodo bitcoin a disposizione

## Installazione delle dipendenze

Il BTCPayServer per essere installato utiluzza .NET Core SDK, NBXplorer e PostgreSQL.

## Installa .NET Core SDK

```bash
cd ~/Downloads
wget https://packages.microsoft.com/config/ubuntu/22.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
rm packages-microsoft-prod.deb
```

Aggiorna e installa .NET 6.0:

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https
sudo apt-get update
sudo apt-get install -y dotnet6
```

## Installa PostgreSQL

I wasn't able to set up BTCPayServer with MySQL/MariaDB so I was forced to use PostgreSQL.

Install it first and open a shell prompt as a newly created system user:

```bash
sudo apt install postgresql postgresql-contrib
sudo -i -u postgres
```

Crea un nuovo utente:

```bash
createuser --pwprompt --interactive
```

Crea due nuovi database:

```bash
createdb -O satoshi btcpayserver
createdb -O satoshi nbxplorer
```

Se non ci sono errori digita exit

```bash
exit
```

## Installa NBXplorer

NBXplorer è fondamentale per tner traccia delle transazioni in entrata

Clona la repository:

```bash
git clone https://github.com/dgarage/NBXplorer
cd NBXplorer
./build.sh
```

Crea una nuova cartella

```bash
mkdir -p ~/.nbxplorer/Main
cd ~/.nbxplorer/Main
```

Crea il file di configurazioni

```bash
nano settings.config
```

Copia e incolla il seguente file di configurazione che trovi anche nella cartella config

```bash
btc.rpc.auth=<bitcoind rpc user>:<bitcoind rpc password>
port=24445
mainnet=1
postgres=User ID=<your db user>;Password=<your db password>;Host=localhost;Port=5432;Database=nbxplorer;
```

```bash
cd ~/NBXplorer
./run.sh
```

Se funziona ferma tutto premento ctrl+c

```bash
cd /etc/systemd/system
sudo wget https://gist.githubusercontent.com/mariodian/de873b969e70eca4d0a7673efd697d0a/raw/acfc70c5694cd53d8a3df7ff54a35ff2caba7532/nbxplorer.service
```

Attiva il servizio e controlla lo stato

```bash
sudo systemctl enable nbxplorer.service
sudo service nbxplorer start
sudo service nbxplorer status
```

## Installa BTCPayServer

```bash
git clone https://github.com/btcpayserver/btcpayserver.git
cd btcpayserver
./build.sh
```

```bash
mkdir -p ~/.btcpayserver/Main
cd ~/.btcpayserver/Main
```

Crea il file di configurazioni

```bash
nano settings.config
```
Copia e incolla il seguente file di configurazione che trovi anche nella cartella config

```bash
network=mainnet
port=23001
bind=0.0.0.0
chains=btc
BTC.explorer.url=http://127.0.0.1:24445
```

```bash
cd ~/btcpayserver
./run.sh
```

Se funziona ferma tutto premento ctrl+c

```bash
cd /etc/systemd/system
sudo wget https://gist.githubusercontent.com/mariodian/07bb13da314e2a321784b380f543651a/raw/6cef554d9e8311e683a017d5e63a07822dee7642/btcpayserver.service
```

Attiva il servizio e verifca che tutto sia funzionante

```bash
sudo systemctl enable btcpayserver.service
sudo service btcpayserver start
sudo service btcpayserver status
```
