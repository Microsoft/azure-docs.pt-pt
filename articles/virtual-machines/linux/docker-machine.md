---
title: Utilizar o Docker Machine para criar anfitriões do Linux no Azure | Documentos da Microsoft
description: Descreve como utilizar o Docker Machine para criar anfitriões do Docker no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: da0e393dc2ae0d93ecc49745a42ffac4669ed74b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386945"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Como utilizar o Docker Machine para criar anfitriões no Azure
Este artigo fornece detalhes sobre como usar [Docker Machine](https://docs.docker.com/machine/) para criar anfitriões no Azure. O `docker-machine` comando cria uma máquina virtual (VM) do Linux no Azure, em seguida, instala o Docker. Em seguida, pode gerir os anfitriões do Docker no Azure com as mesmas ferramentas locais e fluxos de trabalho. Para utilizar uma máquina docker no Windows 10, tem de utilizar bash do Linux.

## <a name="create-vms-with-docker-machine"></a>Criar VMs com o Docker Machine
Em primeiro lugar, obtenha o seu ID de subscrição do Azure com [show de conta de az](/cli/azure/account) da seguinte forma:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Criar VMs de anfitrião do Docker no Azure com `docker-machine create` especificando *azure* como o driver. Para obter mais informações, consulte o [documentação de controlador do Docker Azure](https://docs.docker.com/machine/drivers/azure/)

O exemplo seguinte cria uma VM com o nome *myVM*, com base no plano de "Standard D2 v2", que cria uma conta de utilizador com o nome *azureuser*e abra a porta *80* no anfitrião de VM. Siga todos os pedidos para iniciar sessão na sua conta do Azure e conceder permissões de Docker Machine para criar e gerir os recursos.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_DS2_v2" \
    myvm
```

O resultado será semelhante ao seguinte exemplo:

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

## <a name="configure-your-docker-shell"></a>Configurar a shell de Docker
Para ligar ao seu anfitrião do Docker no Azure, defina as definições de ligação adequado. Conforme observado no final da saída, ver as informações de ligação para o anfitrião do Docker da seguinte forma: 

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

Para definir as definições de ligação, pode optar por executar o comando de configuração sugerido (`eval $(docker-machine env myvm)`), ou pode definir manualmente as variáveis de ambiente. 

## <a name="run-a-container"></a>Executar um contentor
Para ver um contentor em ação, permite executar um servidor de Web NGINX básico. Criar um contentor com `docker run` e expor a porta 80 para tráfego web da seguinte forma:

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

Vista de execução de contentores com `docker ps`. O resultado de exemplo seguinte mostra o contentor de NGINX em execução com a porta 80 expostos:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Testar o contentor
Obtenha o endereço IP público de anfitrião do Docker da seguinte forma:


```bash
docker-machine ip myvm
```

Para ver o contentor em ação, abra um browser e introduza o endereço IP público que anotou na saída do comando anterior:

![Contentor de ngnix em execução](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Passos Seguintes
Para obter exemplos sobre como utilizar o Docker Compose, consulte [introdução ao Docker e ao Compose no Azure](docker-compose-quickstart.md).
