# Instalação do ScyllaDB
Author: Prof. Barbosa<br>
Contact: infobarbosa@gmail.com<br>
Github: [infobarbosa](https://github.com/infobarbosa)


Upgrade de pacotes
```
sudo apt upgrade -y

```

Vamos incluir a chave e link para o repositório para que o gerenciador de pacotes do Linux saiba onde buscar os binários.  

Configurando o repositório
```
SCYLLA_PUBKEY=D0A112E067426AB2

sudo mkdir -p /etc/apt/keyrings
sudo gpg --homedir /tmp --no-default-keyring --keyring /etc/apt/keyrings/scylladb.gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys ${SCYLLA_PUBKEY}

```

```
sudo curl -L --output /etc/apt/sources.list.d/scylla.list http://downloads.scylladb.com/deb/ubuntu/scylla-5.1.list

```

Atualizando a lista de pacotes:
```
sudo apt-get update -y

```

Atenção! Caso ocorra o erro a seguir:
```
The following signatures couldn't be verified because the public key is not available: NO_PUBKEY D0A112E067426AB2
```
Execute o seguinte comando como alternativa:
```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys ${SCYLLA_PUBKEY}

sudo apt update -y

```

Instalando o Scylla:
```
sudo apt install -y scylla 

```

```
sudo apt install -y openjdk-8-jre-headless
sudo update-java-alternatives --jre-headless -s java-1.8.0-openjdk-amd64

```

Setup
```
sudo scylla_dev_mode_setup --developer-mode 1

```

Iniciando o serviço (caso ainda não esteja sendo executado)
```
sudo systemctl start scylla-server

```

Se tudo correu bem então será possível verificar o status do node:
```
nodetool status

```

O comando acima deve retornar algo assim:
```
ubuntu $ nodetool status
Datacenter: datacenter1
 =======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address    Load       Tokens       Owns    Host ID                               Rack
UN  127.0.0.1  204 KB     256          ?       526419b8-fdb4-46ad-9101-1feffefdfaf9  rack1
```

**Atenção!** Se retornar o erro erro abaixo, é provável que o serviço ainda esteja em processo de inicialização.
```
ubuntu $ nodetool status
nodetool: Scylla API server HTTP GET to URL '/storage_service/ownership/' failed: runtime_exception (runtime error: No nodes present in the cluster. Has this node finished starting up?)
See 'nodetool help' or 'nodetool help <command>'.
```
Espere alguns segundos até que a inicialização tenha terminado e execute o comando novamente.
