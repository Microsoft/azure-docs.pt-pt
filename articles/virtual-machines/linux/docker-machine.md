---
title: Usar o computador do Docker para criar hosts Linux no Azure
description: Descreve como usar a máquina do Docker para criar hosts do Docker no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 6b7f26d4554cc5ab1450ac3a8cf927b2338f8ea2
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036256"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Como usar a máquina do Docker para criar hosts no Azure
Este artigo fornece detalhes sobre como usar a [máquina do Docker](https://docs.docker.com/machine/) para criar hosts no Azure. O comando `docker-machine` cria uma VM (máquina virtual) do Linux no Azure e instala o Docker. Em seguida, você pode gerenciar os hosts do Docker no Azure usando as mesmas ferramentas e fluxos de trabalho locais. Para usar a máquina Docker no Windows 10, você deve usar o bash do Linux.

## <a name="create-vms-with-docker-machine"></a>Criar VMs com o computador Docker
Primeiro, obtenha sua ID de assinatura do Azure com [AZ Account show](/cli/azure/account) da seguinte maneira:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Você cria VMs de host do Docker no Azure com `docker-machine create` especificando o *Azure* como o driver. Para obter mais informações, consulte a [documentação do driver do Docker Azure](https://docs.docker.com/machine/drivers/azure/)

O exemplo a seguir cria uma VM chamada *myVM*, com base no plano "Standard D2 v2", cria uma conta de usuário chamada *azureuser*e abre a porta *80* na VM do host. Siga os prompts para fazer logon em sua conta do Azure e conceder permissões de máquina do Docker para criar e gerenciar recursos.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_DS2_v2" \
    myvm
```

A saída é semelhante ao exemplo a seguir:

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

## <a name="configure-your-docker-shell"></a>Configurar o Shell do Docker
Para se conectar ao host do Docker no Azure, defina as configurações de conexão apropriadas. Conforme observado no final da saída, exiba as informações de conexão para o host do Docker da seguinte maneira: 

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

Para definir as configurações de conexão, você pode executar o comando de configuração sugerido (`eval $(docker-machine env myvm)`) ou pode definir as variáveis de ambiente manualmente. 

## <a name="run-a-container"></a>Executar um contêiner
Para ver um contêiner em ação, vamos executar um servidor webNGINX básico. Crie um contêiner com `docker run` e expor a porta 80 para o tráfego da Web da seguinte maneira:

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

Exibir contêineres em execução com `docker ps`. A saída de exemplo a seguir mostra o contêiner NGINX em execução com a porta 80 exposta:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Testar o contêiner
Obtenha o endereço IP público do host do Docker da seguinte maneira:


```bash
docker-machine ip myvm
```

Para ver o contêiner em ação, abra um navegador da Web e insira o endereço IP público anotado na saída do comando anterior:

![Executando o contêiner Ngnix](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Passos seguintes
Para obter exemplos de como usar Docker Compose, consulte Introdução [ao Docker e ao Compose no Azure](docker-compose-quickstart.md).
