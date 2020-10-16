---
title: Use a Máquina Docker para criar anfitriões Linux
description: Descreve como usar a Máquina Docker para criar anfitriões Docker em Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: how-to
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 83799940f8c98952a435f582a9160585e9dbdfd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289799"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Como usar a Máquina Docker para criar anfitriões em Azure
Este artigo detalha como usar a [Máquina Docker](https://docs.docker.com/machine/) para criar anfitriões em Azure. O `docker-machine` comando cria uma máquina virtual Linux (VM) em Azure e depois instala Docker. Em seguida, pode gerir os seus anfitriões Docker em Azure usando as mesmas ferramentas locais e fluxos de trabalho. Para utilizar a máquina de estivar no Windows 10, tem de utilizar a bash Linux.

## <a name="create-vms-with-docker-machine"></a>Criar VMs com máquina Docker
Em primeiro lugar, obtenha o seu ID de subscrição Azure com [a az show](/cli/azure/account) da seguinte forma:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Cria vMs de anfitrião Docker em Azure com `docker-machine create` especificando *azul* como motorista. Para mais informações, consulte a documentação do [Condutor Docker Azure](https://docs.docker.com/machine/drivers/azure/)

O exemplo a seguir cria um VM chamado *myVM,* baseado no plano "Standard D2 v2", cria uma conta de utilizador chamada *azureuser,* e abre a porta *80* no VM anfitrião. Siga quaisquer solicitações para iniciar sessão na sua conta Azure e conceder permissões à Docker Machine para criar e gerir recursos.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_DS2_v2" \
    myvm
```

A saída é semelhante ao seguinte exemplo:

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvm) Completed machine pre-create checks.
Creating machine...
(myvm) Querying existing resource group.  name="docker-machine"
(myvm) Creating resource group.  name="docker-machine" location="westus"
(myvm) Configuring availability set.  name="docker-machine"
(myvm) Configuring network security group.  name="myvm-firewall" location="westus"
(myvm) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvm) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvm) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvm) Creating public IP address.  name="myvm-ip" static=false
(myvm) Creating network interface.  name="myvm-nic"
(myvm) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvm) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvm
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvm
```

## <a name="configure-your-docker-shell"></a>Configure a sua concha Docker
Para ligar ao seu anfitrião Docker em Azure, defina as definições de ligação apropriadas. Como notado no final da saída, veja as informações de ligação para o seu anfitrião Docker da seguinte forma: 

```bash
docker-machine env myvm
```

O resultado é semelhante ao seguinte exemplo:

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvm)
```

Para definir as definições de ligação, pode executar o comando de configuração sugerido `eval $(docker-machine env myvm)` (), ou pode definir as variáveis ambientais manualmente. 

## <a name="run-a-container"></a>Executar um recipiente
Para ver um recipiente em ação, vamos executar um webserver NGINX básico. Crie um recipiente com `docker run` e exponha a porta 80 para tráfego web da seguinte forma:

```bash
docker run -d -p 80:80 --restart=always nginx
```

O resultado é semelhante ao seguinte exemplo:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
ff3d52d8f55f: Pull complete
226f4ec56ba3: Pull complete
53d7dd52b97d: Pull complete
Digest: sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268
Status: Downloaded newer image for nginx:latest
675e6056cb81167fe38ab98bf397164b01b998346d24e567f9eb7a7e94fba14a
```

Ver recipientes de funcionamento com `docker ps` . A saída de exemplo a seguir mostra o contentor NGINX a funcionar com a porta 80 exposta:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Teste o recipiente
Obtenha o endereço IP público do anfitrião Docker da seguinte forma:


```bash
docker-machine ip myvm
```

Para ver o contentor em ação, abra um navegador web e introduza o endereço IP público indicado na saída do comando anterior:

![Recipiente de ngnix de execução](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Passos seguintes
Por exemplo, sobre a utilização de Docker Compose, consulte [Começar com Docker e Compose em Azure](docker-compose-quickstart.md).
