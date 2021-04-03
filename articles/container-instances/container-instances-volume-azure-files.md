---
title: Monte Azure Files volume para grupo de contentores
description: Saiba como montar um volume de Ficheiros Azure para persistir com instâncias de contentores Azure
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d52ad8ad02735c98b29a83d8ca69cdea8c6af7d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97954979"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montar uma partilha de ficheiros do Azure no Azure Container Instances

Por predefinição, o Azure Container Instances não tem monitorização de estado. Se o contentor for reiniciado, se despenhar ou parar, todo o seu estado está perdido. Para manter as informações de estado para além da duração do contentor, tem de montar um volume a partir de um arquivo externo. Como mostrado neste artigo, a Azure Container Instances pode montar uma partilha de ficheiros Azure criada com [ficheiros Azure](../storage/files/storage-files-introduction.md). O Azure Files oferece ações de ficheiros totalmente geridas, hospedadas no Azure Storage, acessíveis através do protocolo padrão do Bloco de Mensagens do Servidor (SMB) da indústria. A utilização de uma partilha de ficheiros Azure com a Azure Container Instances fornece funcionalidades de partilha de ficheiros semelhantes à utilização de uma partilha de ficheiros Azure com máquinas virtuais Azure.

> [!NOTE]
> A montagem de uma partilha de Ficheiros Azure está atualmente restrita aos contentores Linux. Encontre as diferenças da plataforma atuais na [visão geral.](container-instances-overview.md#linux-and-windows-containers)
>
> A montagem de uma partilha de Ficheiros Azure para uma instância de contentor é semelhante a um [suporte de encaixe](https://docs.docker.com/storage/bind-mounts/)docker . Tenha em atenção que se montar uma partilha num diretório de contentores em que existam ficheiros ou diretórios, estes ficheiros ou diretórios são obscurecidos pelo suporte e não estão acessíveis enquanto o contentor funciona.
>

> [!IMPORTANT]
> Se estiver a colocar grupos de contentores numa Rede Virtual Azure, tem de adicionar um [ponto final de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) à sua Conta de Armazenamento Azure.

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

Antes de utilizar uma partilha de ficheiros do Azure com o Azure Container Instances, tem de criá-la. Execute o seguinte script para criar uma conta de armazenamento para hospedar a partilha de ficheiros, e a própria partilha. O nome da conta de armazenamento deve ser globalmente exclusivo, pelo que o script adiciona um valor aleatório à cadeia de base.

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
az storage share create \
  --name $ACI_PERS_SHARE_NAME \
  --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Obter as credenciais de armazenamento

Para montar uma partilha de ficheiros do Azure como um volume no Azure Container Instances, precisa de três valores: o nome da conta de armazenamento, o nome da partilha e a chave de acesso ao armazenamento.

* **Nome da conta de armazenamento** - Se usou o script anterior, o nome da conta de armazenamento foi armazenado na `$ACI_PERS_STORAGE_ACCOUNT_NAME` variável. Para ver o nome da conta, escreva:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Nome da** partilha - Este valor já é conhecido (definido como `acishare` no script anterior)

* **Chave da conta de armazenamento** - Este valor pode ser encontrado usando o seguinte comando:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Implantar contentor e volume de montagem - CLI

Para montar uma partilha de ficheiroS Azure como volume num recipiente utilizando o CLI Azure, especifique o ponto de montagem de partilha e volume quando criar o recipiente com [recipiente az criar][az-container-create]. Se seguiu os passos anteriores, pode montar a partilha que criou anteriormente, utilizando o seguinte comando para criar um recipiente:

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

O `--dns-name-label` valor deve ser único na região de Azure onde se cria a instância do contentor. Atualize o valor no comando anterior se receber uma mensagem de erro **do rótulo DNS** quando executar o comando.

## <a name="manage-files-in-mounted-volume"></a>Gerir ficheiros em volume montado

Assim que o recipiente começar, pode utilizar a simples aplicação web implementada através da imagem [aci-hellofiles][aci-hellofiles] da Microsoft para criar pequenos ficheiros de texto na partilha de ficheiros Azure no caminho de montagem especificado. Obtenha o nome de domínio totalmente qualificado da aplicação web (FQDN) com o comando [de show de contentores az:][az-container-show]

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

Depois de guardar texto utilizando a aplicação, pode utilizar o [portal Azure][portal] ou uma ferramenta como o [Microsoft Azure Storage Explorer][storage-explorer] para recuperar e inspecionar o ficheiro ou ficheiros escritos na partilha de ficheiros.

## <a name="deploy-container-and-mount-volume---yaml"></a>Implantar contentor e volume de montagem - YAML

Também pode implantar um grupo de contentores e montar um volume num recipiente com o CLI Azure e um [modelo YAML](container-instances-multi-container-yaml.md). A implantação pelo modelo YAML é um método preferido ao implantar grupos de contentores constituídos por vários contentores.

O modelo YAML seguinte define um grupo de contentores com um recipiente criado com a `aci-hellofiles` imagem. O recipiente monta o *acishare de acishare* de ficheiroS Azure criado anteriormente como um volume. Quando indicado, introduza o nome e a chave de armazenamento para a conta de armazenamento que acolhe a parte do ficheiro. 

Tal como no exemplo do CLI, o `dnsNameLabel` valor deve ser único na região de Azure onde se cria a instância do contentor. Atualizar o valor no ficheiro YAML, se necessário.

```yaml
apiVersion: '2019-12-01'
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

Para implementar com o modelo YAML, guarde o YAML anterior para um ficheiro `deploy-aci.yaml` nomeado, em seguida, execute o [contentor az criar][az-container-create] comando com o `--file` parâmetro:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Implantar contentor e volume de montagem - Gestor de Recursos

Além da implantação de CLI e YAML, pode implantar um grupo de contentores e montar um volume num recipiente utilizando um [modelo de Gestor de Recursos](/azure/templates/microsoft.containerinstance/containergroups)Azure .

Primeiro, povoe a `volumes` matriz na secção do grupo de contentores do `properties` gabarito. 

Em seguida, para cada recipiente em que pretende montar o volume, povoe a `volumeMounts` matriz na secção da `properties` definição do recipiente.

O modelo seguinte do Gestor de Recursos define um grupo de contentores com um recipiente criado com a `aci-hellofiles` imagem. O recipiente monta o *acishare de acishare* de ficheiroS Azure criado anteriormente como um volume. Quando indicado, introduza o nome e a chave de armazenamento para a conta de armazenamento que acolhe a parte do ficheiro. 

Tal como nos exemplos anteriores, o `dnsNameLabel` valor deve ser único na região de Azure onde se cria a instância do contentor. Atualizar o valor no modelo, se necessário.

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
      "apiVersion": "2019-12-01",
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

Para implementar com o modelo de Gestor de Recursos, guarde o JSON anterior para um ficheiro `deploy-aci.json` nomeado, em seguida, execute o [grupo de implementação az criar][az-deployment-group-create] comando com o `--template-file` parâmetro:

```azurecli
# Deploy with Resource Manager template
az deployment group create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Monte vários volumes

Para montar vários volumes numa instância de um recipiente, tem de ser implantado utilizando um [modelo de Gestor de Recursos Azure,](/azure/templates/microsoft.containerinstance/containergroups)um ficheiro YAML ou outro método programático. Para utilizar um modelo ou ficheiro YAML, forneça os detalhes da partilha e defina os volumes povoando a `volumes` matriz na secção do `properties` ficheiro. 

Por exemplo, se criou duas ações da Azure Files *nomeadas share1* e *share2* na conta de armazenamento *myStorageAccount,* o `volumes` conjunto num modelo de Gestor de Recursos apareceria semelhante ao seguinte:

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

Em seguida, para cada recipiente no grupo de contentores em que pretende montar os volumes, povoe a `volumeMounts` matriz na secção da `properties` definição do recipiente. Por exemplo, isto monta os dois volumes, *o myvolume1* e *o myvolume2,* previamente definidos:

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

## <a name="next-steps"></a>Passos seguintes

Saiba como montar outros tipos de volume em Instâncias de Contentores Azure:

* [Monte um volume deDir vazio em instâncias de contentores Azure](container-instances-volume-emptydir.md)
* [Monte um volume gitRepo em Instâncias de Contentores Azure](container-instances-volume-gitrepo.md)
* [Monte um volume secreto em Instâncias de Contentores Azure](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
