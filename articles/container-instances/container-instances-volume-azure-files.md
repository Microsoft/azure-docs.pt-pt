---
title: Monte um volume de ficheiros do Azure no Azure Container Instances
description: Saiba como montar um volume de ficheiros do Azure para persistir o estado com o Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: bc09aa500743d608c0a3a7a379fe9584c9c55e9b
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657620"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montar uma partilha de ficheiros do Azure no Azure Container Instances

Por predefinição, o Azure Container Instances são sem monitoração de estado. Se o contentor de falha ou para, todo seu estado é perdido. Para persistir o estado, além da duração do contentor, tem de montar um volume de um arquivo externo. Este artigo mostra como montar uma partilha de ficheiros do Azure criada com [ficheiros do Azure](../storage/files/storage-files-introduction.md) para utilização com o Azure Container Instances. Os Ficheiros do Azure oferecem partilhas de ficheiros completamente geridas na cloud que são acessíveis através do protocolo standard da indústria Server Message Block (SMB). Utilizar uma partilha de ficheiros do Azure com o Azure Container Instances fornece funcionalidades de partilha de ficheiros semelhantes à utilização uma partilha de ficheiros do Azure com máquinas virtuais do Azure.

> [!NOTE]
> Montar uma partilha de ficheiros do Azure está atualmente restrita para contentores do Linux. Enquanto estamos a trabalhar para colocar todas as funcionalidades para os contentores do Windows, pode encontrar as diferenças de plataforma atual no [descrição geral](container-instances-overview.md#linux-and-windows-containers).

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

## <a name="deploy-container-and-mount-volume---cli"></a>Implementar o contentor e montar o volume - CLI

Para montar uma partilha de ficheiros do Azure como um volume num contentor com a CLI do Azure, especifique a partilha e o volume de ponto de montagem quando criar o contentor com [criar contentor de az][az-container-create]. Se seguiu os passos anteriores, pode montar a partilha que criou anteriormente ao utilizar o seguinte comando para criar um contentor:

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

O `--dns-name-label` valor tem de ser exclusivo dentro da região do Azure, onde cria a instância de contentor. Atualize o valor no comando anterior se receber um **etiqueta de nome DNS** mensagem de erro ao executar o comando.

## <a name="manage-files-in-mounted-volume"></a>Gerir ficheiros no volume montado

Depois do contentor é iniciado, pode utilizar a aplicação web simples implementada por meio da Microsoft [aci-hellofiles][aci-hellofiles] image to create small text files in the Azure file share at the mount path you specified. Obtain the web app's fully qualified domain name (FQDN) with the [az container show][az-container-show] comando:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

Depois de guardar o texto a utilizar a aplicação, pode utilizar o [portal do Azure][portal] or a tool like the [Microsoft Azure Storage Explorer][storage-explorer] para recuperar e Inspecione o ficheiro de escrita à partilha de ficheiros.

## <a name="deploy-container-and-mount-volume---yaml"></a>Implementar o contentor e montar o volume - YAML

Também pode implementar um grupo de contentores e Monte um volume num contentor com a CLI do Azure e um [modelo YAML](container-instances-multi-container-yaml.md). Implantando com o modelo YAML é o método preferencial quando implementar grupos de contentores que consiste de vários contentores.

O modelo YAML seguinte define um grupo de contentores com um contentor criado com o `aci-hellofiles` imagem. O contentor monta a partilha de ficheiros do Azure *acishare* criado anteriormente como um volume. Sempre que for indicado, introduza a chave de armazenamento e o nome para a conta de armazenamento que aloja a partilha de ficheiros. 

Como no exemplo da CLI, o `dnsNameLabel` valor tem de ser exclusivo dentro da região do Azure, onde cria a instância de contentor. Se for necessário, atualize o valor no ficheiro YAML.

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Para implementar com o modelo YAML, guardar o YAML anterior num ficheiro denominado `deploy-aci.yaml`, em seguida, execute o [criar contentor de az][az-container-create] comando com o `--file` parâmetro:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Implementar o contentor e montagem de volume - Resource Manager

Além de uma implementação da CLI e YAML, pode implementar um grupo de contentores e Monte um volume num contentor com o Azure [modelo do Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Em primeiro lugar, preencher a `volumes` matriz no grupo de contentores `properties` secção do modelo. 

Em seguida, para cada contentor em que pretende montar o volume, preencher a `volumeMounts` obsahuje pole o `properties` secção da definição de contentor.

O modelo do Resource Manager seguinte define um grupo de contentores com um contentor criado com o `aci-hellofiles` imagem. O contentor monta a partilha de ficheiros do Azure *acishare* criado anteriormente como um volume. Sempre que for indicado, introduza a chave de armazenamento e o nome para a conta de armazenamento que aloja a partilha de ficheiros. 

Como nos exemplos anteriores, o `dnsNameLabel` valor tem de ser exclusivo dentro da região do Azure, onde cria a instância de contentor. Se for necessário, atualize o valor no modelo.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

Para implementar com o modelo do Resource Manager, guardar o JSON anterior num ficheiro denominado `deploy-aci.json`, em seguida, execute o [criar a implementação do grupo az][az-group-deployment-create] comando com o `--template-file` parâmetro:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Vários volumes de montagem

Para montar vários volumes numa instância de contentor, tem de implementar com um [modelo Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) ou um ficheiro YAML. Para utilizar um modelo ou do ficheiro YAML, indique os detalhes de partilha e definir os volumes ao preencher a `volumes` obsahuje pole o `properties` secção do modelo. 

Por exemplo, se criou duas partilhas de ficheiros do Azure com o nome *share1* e *share2* na conta de armazenamento *myStorageAccount*, o `volumes` matriz num Gerenciador de recursos modelo seria ter um aspeto semelhante ao seguinte:

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
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create