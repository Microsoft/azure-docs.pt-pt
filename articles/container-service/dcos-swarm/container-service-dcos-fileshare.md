---
title: (DEPRECIADO) Partilha de ficheiros para cluster Azure DC/OS
description: Criar e montar uma partilha de ficheiros num cluster DC/OS no Azure Container Service
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: e6651fc5988a1e1830807219cda02ab057db9a4f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60480389"
---
# <a name="deprecated-create-and-mount-a-file-share-to-a-dcos-cluster"></a>(DEPRECIADO) Criar e montar uma partilha de ficheiros para um cluster DC/OS

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Este tutorial apresenta detalhes sobre como criar uma partilha de ficheiros no Azure e como montá-la em cada agente e mestre do cluster DC/OS. Configurar uma partilha de ficheiros facilita a partilha de ficheiros pelo cluster, como a configuração, o acesso, os registos e muito mais. Neste tutorial, vai concluir as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma conta de armazenamento do Azure
> * Criar uma partilha de ficheiros
> * Montar a partilha no cluster DC/OS

Precisa de um cluster DC/OS do ACS para concluir os passos neste tutorial. Se for necessário, [este script de exemplo](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) pode criar um para si.

Este tutorial requer a versão do módulo 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Criar uma partilha de ficheiros no Microsoft Azure

Para utilizar a partilha de ficheiros do Azure com um cluster DC/OS do ACS, tem de criar a conta de armazenamento e a partilha de ficheiros. Execute o seguinte script para criar o armazenamento e a partilha de ficheiros. Atualize os parâmetros com os do seu ambiente.

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Montar a partilha no cluster

Em seguida, a partilha de ficheiros tem de ser montada em todas as máquinas virtuais no cluster. Esta tarefa é concluída com o ferramenta/protocolo cifs. A operação de montagem pode ser concluída manualmente em cada nó do cluster ou através da execução de um script para cada nó do cluster.

Neste exemplo, são executados dois scripts, um para montar a partilha de ficheiros do Azure e um segundo para executar este script em cada nó do cluster DC/OS.

Em primeiro lugar, é necessário o nome da conta de armazenamento do Azure e a chave de acesso. Execute os seguintes comandos para obter essas informações. Anote cada um destes valores, pois serão utilizados num passo posterior.

Nome da conta de armazenamento:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Chave de acesso da conta de armazenamento:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

Em seguida, obtenha o FQDN do mestre do DC/OS e armazene-o numa variável.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Copie a chave privada para o nó principal. Esta chave é necessária para criar uma ligação ssh com todos os nós do cluster. Atualize o nome de utilizador se tiver sido utilizado um valor não predefinido ao criar o cluster. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Crie uma ligação SSH com o mestre (ou o primeiro mestre) do cluster baseado em DC/SO. Atualize o nome de utilizador se tiver sido utilizado um valor não predefinido ao criar o cluster.

```azurecli-interactive
ssh azureuser@$FQDN
```

Crie um ficheiro com o nome **cifsMount.sh** e copie o seguinte conteúdo para o mesmo. 

Este script serve para montar a partilha de ficheiros do Azure. Atualize as variáveis `STORAGE_ACCT_NAME` e `ACCESS_KEY` com as informações recolhidas anteriormente.

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
Crie um segundo ficheiro denominado **getNodesRunScript.sh** e copie o seguinte conteúdo para o ficheiro. 

Este script deteta todos os nós de cluster e, em seguida, executa o script **cifsMount.sh** para montar a partilha de ficheiros em cada um.

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

Execute o script para montar a partilha de ficheiros do Azure em todos os nós do cluster.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

A partilha de ficheiros está agora acessível em `/mnt/share/dcosshare` em cada nó do cluster.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, a partilha de ficheiros do Azure foi disponibilizada para um cluster DC/OS com os seguintes passos:

> [!div class="checklist"]
> * Criar uma conta de armazenamento do Azure
> * Criar uma partilha de ficheiros
> * Montar a partilha no cluster DC/OS

Avance para o próximo tutorial para saber como integrar o Azure Container Registry no DC/OS, no Azure.  

> [!div class="nextstepaction"]
> [Fazer o balanceamento de carga de aplicações](container-service-dcos-acr.md)
