---
title: (DEPRECIADO) Utilização de ACR com um cluster Azure DC/OS
description: Utilizar o Azure Container Registry com um cluster DC/OS no Azure Container Service
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 9e69b66c7cee5a6e012ad7ed2477556fa840bfb5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78402065"
---
# <a name="deprecated-use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>(DEPRECIADO) Use ACR com um cluster DC/OS para implementar a sua aplicação

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, vamos explorar como utilizar o Azure Container Registry com um cluster DC/OS. A utilização do ACR permite-lhe armazenar e gerir imagens de contentor em privado. Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Implementar o Azure Container Registry (se for necessário)
> * Configurar a autenticação do ACR num cluster DC/OS
> * Carregar uma imagem para o Azure Container Registry
> * Executar uma imagem de contentor para o Azure Container Registry

Precisa de um cluster DC/OS do ACS para concluir os passos neste tutorial. Se for necessário, [este script de exemplo](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) pode criar um para si.

Este tutorial requer a versão do módulo 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Implementar o Azure Container Registry

Se for necessário, crie um Azure Container Registry com o comando [az acr create](/cli/azure/acr#az-acr-create). 

O exemplo seguinte cria um registo com um nome gerado aleatoriamente. O registo também é configurado com uma conta de administrador com o argumento `--admin-enabled`.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic
```

Após a criação do registo, a CLI do Azure produz dados semelhantes aos seguintes. Tome nota do `name` e do `loginServer`, uma vez que serão utilizados em passos posteriores.

```output
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Obtenha as credenciais de registo de contentor com o comando [az acr credential show](/cli/azure/acr/credential). Substitua `--name` pelo nome indicado no último passo. Tome nota de uma palavra-passe, uma vez que será necessária num passo posterior.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Para obter mais informações sobre o Azure Container Registry, veja [Introdução aos registos privados de contentores Docker](../../container-registry/container-registry-intro.md). 

## <a name="manage-acr-authentication"></a>Gerir a autenticação do ACR

A forma convencional de enviar e receber uma imagem de um registo privado é autenticar primeiro no registo. Para tal, utilize o comando `docker login` em qualquer cliente que tenha de aceder ao registo privado. Uma vez que um cluster DC/OS pode conter vários nós, os quais têm de ser autenticados com o ACR, é útil automatizar este processo em cada nó. 

### <a name="create-shared-storage"></a>Criar armazenamento partilhado

Este processo utiliza uma partilha de ficheiros do Azure montada em cada nó no cluster. Se ainda não tiver configurado o armazenamento partilhado, veja [Configurar uma partilha de ficheiros num cluster DC/SO](container-service-dcos-fileshare.md).

### <a name="configure-acr-authentication"></a>Configurar a autenticação do ACR

Primeiro, obtenha o FQDN do DC/OS mestre e armazene-o numa variável.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

Crie uma ligação SSH com o mestre (ou o primeiro mestre) do cluster baseado em DC/SO. Atualize o nome de utilizador se tiver sido utilizado um valor não predefinido ao criar o cluster.

```console
ssh azureuser@$FQDN
```

Execute o comando seguinte para iniciar sessão no Azure Container Registry. Substitua `--username` pelo nome do registo de contentor e `--password` por uma das palavras-passe fornecidas. Substitua o último argumento *mycontainerregistry.azurecr.io* no exemplo pelo nome loginServer do registo de contentor. 

Este comando armazena os valores de autenticação localmente no caminho `~/.docker`.

```console
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

Crie um ficheiro comprimido com os valores de autenticação de registo de contentor.

```console
tar czf docker.tar.gz .docker
```

Copie este ficheiro para o armazenamento partilhado do cluster. Este passo disponibiliza o ficheiro em todos os nós do cluster DC/OS.

```console
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>Carregar imagem para o ACR

Agora, a partir de uma máquina de desenvolvimento ou de qualquer outro sistema com o Docker instalado, pode criar uma imagem e carregá-la para o Azure Container Registry.

Crie um contentor a partir da imagem do Ubuntu.

```console
docker run ubuntu --name base-image
```

Capture agora o contentor para uma nova imagem. O nome da imagem `loginServer` deve incluir o nome do `loginServer/imageName`registo do contentor com um formato de .

```console
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
```

Inicie sessão no Azure Container Registry. Substitua o nome pelo nome loginServer, --username pelo nome do registo de contentor e --password por uma das palavras-passe fornecidas.

```console
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Por fim, carregue a imagem para o registo do ACR. Este exemplo carrega uma imagem com o nome dcos-demo.

```console
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>Executar uma imagem a partir do ACR

Para utilizar uma imagem a partir do registo do ACR, crie um ficheiro com o nome *acrDemo.json* e copie o seguinte texto para o mesmo. Substitua o nome da imagem pelo nome loginServer do registo de contentor e o nome da imagem, por exemplo `loginServer/imageName`. Tome nota da propriedade `uris`. Esta propriedade contém a localização do ficheiro de autenticação de registo de contentor que,neste caso, é a partilha de ficheiros do Azure montada em cada nó no cluster DC/OS.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

Implemente a aplicação com a CLI do DC/OC.

```console
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou o DC/OS para utilizar o Azure Container Registry, incluindo as seguintes tarefas:

> [!div class="checklist"]
> * Implementar o Azure Container Registry (se for necessário)
> * Configurar a autenticação do ACR num cluster DC/OS
> * Carregar uma imagem para o Azure Container Registry
> * Executar uma imagem de contentor para o Azure Container Registry
