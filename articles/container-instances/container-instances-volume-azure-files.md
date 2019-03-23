---
title: Monte um volume de ficheiros do Azure no Azure Container Instances
description: Saiba como montar um volume de ficheiros do Azure para persistir o estado com o Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/05/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 365264d40554f45533e2ddf0aeb9d85f3e8f8d2d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370623"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montar uma partilha de ficheiros do Azure no Azure Container Instances

Por predefinição, o Azure Container Instances são sem monitoração de estado. Se o contentor de falha ou para, todo seu estado é perdido. Para persistir o estado, além da duração do contentor, tem de montar um volume de um arquivo externo. Este artigo mostra como montar uma partilha de ficheiros do Azure criada com [ficheiros do Azure](../storage/files/storage-files-introduction.md) para utilização com o Azure Container Instances. Os Ficheiros do Azure oferecem partilhas de ficheiros completamente geridas na cloud que são acessíveis através do protocolo standard da indústria Server Message Block (SMB). Utilizar uma partilha de ficheiros do Azure com o Azure Container Instances fornece funcionalidades de partilha de ficheiros semelhantes à utilização uma partilha de ficheiros do Azure com máquinas virtuais do Azure.

> [!NOTE]
> Montar uma partilha de ficheiros do Azure está atualmente restrita para contentores do Linux. Enquanto estamos a trabalhar para colocar todas as funcionalidades de contentores do Windows, pode encontrar as diferenças da plataforma atual em [Quotas e disponibilidade das regiões do Azure Container Instances](container-instances-quotas.md).

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

Antes de utilizar uma partilha de ficheiros do Azure com o Azure Container Instances, deverá criá-la. Execute o seguinte script para criar uma conta de armazenamento para alojar a partilha de ficheiros e a partilha em si. O nome da conta de armazenamento tem de ser globalmente exclusivo, para que o script adiciona um valor aleatório na cadeia de caracteres de base.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Obter credenciais de armazenamento

Para montar uma partilha de ficheiros do Azure como um volume no Azure Container Instances, terá de três valores: o nome da conta de armazenamento, o nome da partilha e a chave de acesso de armazenamento.

Se utilizou o script acima, o nome da conta de armazenamento foi armazenado na variável $ACI_PERS_STORAGE_ACCOUNT_NAME. Para ver o nome da conta, escreva:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

O nome da partilha já é conhecido (definido como *acishare* no script acima), portanto, todos os que resta é a chave de conta de armazenamento, que pode ser encontrada usando o seguinte comando:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>Implementar o contentor e montagem de volume

Para montar uma partilha de ficheiros do Azure como um volume num contentor, especifique a partilha e o volume de ponto de montagem quando criar o contentor com [criar contentor de az][az-container-create]. Se seguiu os passos anteriores, pode montar a partilha que criou anteriormente ao utilizar o seguinte comando para criar um contentor:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

O valor `--dns-name-label` tem de ser exclusivo na região do Azure em que cria a instância de contentor. Atualize o valor no comando anterior se receber um **etiqueta de nome DNS** mensagem de erro ao executar o comando.

## <a name="manage-files-in-mounted-volume"></a>Gerir ficheiros no volume montado

Depois do contentor é iniciado, pode utilizar a aplicação web simples implementada por meio da Microsoft [aci-hellofiles] [ aci-hellofiles] imagem para criar pequenos ficheiros de texto na partilha de ficheiros do Azure no caminho de montagem que especificou. Obter o nome de domínio completamente qualificado da aplicação web (FQDN) com o [show de contentor az] [ az-container-show] comando:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn
```

Pode utilizar o [portal do Azure] [ portal] ou uma ferramenta, como o [Explorador de armazenamento do Microsoft Azure] [ storage-explorer] para recuperar e Inspecione o ficheiro de escrita a partilha de ficheiros.

## <a name="mount-multiple-volumes"></a>Vários volumes de montagem

Para montar vários volumes numa instância de contentor, tem de implementar com um [modelo Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) ou um ficheiro YAML.

Para utilizar um modelo, indique os detalhes de partilha e definir os volumes ao preencher a `volumes` obsahuje pole o `properties` secção do modelo. Por exemplo, se criou duas partilhas de ficheiros do Azure com o nome *share1* e *share2* na conta de armazenamento *myStorageAccount*, o `volumes` apareceria matriz semelhante ao seguinte:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Em seguida, para cada contentor no grupo de contentores em que gostaria de montar os volumes, preencher a `volumeMounts` obsahuje pole o `properties` secção da definição de contentor. Por exemplo, o dois volumes, isso monta *myvolume1* e *myvolume2*, definida anteriormente:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

Para ver um exemplo de implementação de instância de contentor com um modelo Azure Resource Manager, consulte [implementar um grupo de contentores](container-instances-multi-container-group.md). Para obter um exemplo com um ficheiro YAML, consulte [implementar um grupo de vários contentor com YAML](container-instances-multi-container-yaml.md)

## <a name="next-steps"></a>Passos Seguintes

Saiba como montar outros tipos de volume no Azure Container Instances:

* [Montar um volume emptyDir em instâncias de contentor do Azure](container-instances-volume-emptydir.md)
* [Monte um volume de gitRepo no Azure Container Instances](container-instances-volume-gitrepo.md)
* [Montar um volume secreto em instâncias de contentor do Azure](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show