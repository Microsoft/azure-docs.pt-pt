---
title: Volume de Ficheiros Do Monte Azure para grupo de contentores
description: Saiba montar um volume de Ficheiros Azure para persistir em estado com instâncias de contentores Azure
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: f66890c503de8de9160f11fb28795012ae57daeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561342"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montar uma partilha de ficheiros do Azure no Azure Container Instances

Por predefinição, o Azure Container Instances não tem monitorização de estado. Se o contentor falhar ou parar, todo o seu estado será perdido. Para manter as informações de estado para além da duração do contentor, tem de montar um volume a partir de um arquivo externo. Como mostra este artigo, as Instâncias de Contentores Azure podem montar uma partilha de ficheiros Azure criada com [ficheiros Azure](../storage/files/storage-files-introduction.md). O Azure Files oferece partilhas de ficheiros totalmente geridas hospedadas no Armazenamento Azure que são acessíveis através do protocolo padrão do Bloco de Mensagens do Servidor (SMB) da indústria. A utilização de uma partilha de ficheiros Azure com as Instâncias de Contentores Azure fornece funcionalidades de partilha de ficheiros semelhantes à utilização de uma partilha de ficheiros Azure com máquinas virtuais Azure.

> [!NOTE]
> A montagem de uma quota de Ficheiros Azure está atualmente restrita aos contentores Linux. Encontre as diferenças atuais da plataforma na [visão geral](container-instances-overview.md#linux-and-windows-containers).
>
> A montagem de uma partilha de Ficheiros Azure numa instância de contentores é semelhante a um suporte de [encaixe](https://docs.docker.com/storage/bind-mounts/)do Docker . Esteja ciente de que se montar uma parte num diretório de contentores em que existem ficheiros ou diretórios, estes ficheiros ou diretórios são obscurecidos pelo suporte e não são acessíveis enquanto o contentor funciona.
>

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

Antes de utilizar uma partilha de ficheiros do Azure com o Azure Container Instances, tem de criá-la. Execute o seguinte script para criar uma conta de armazenamento para hospedar a parte do ficheiro, e a própria parte. O nome da conta de armazenamento deve ser globalmente exclusivo, pelo que o script adiciona um valor aleatório à cadeia de base.

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

* **Nome** da conta de armazenamento - Se usou o script anterior, o nome da conta de armazenamento foi armazenado na `$ACI_PERS_STORAGE_ACCOUNT_NAME` variável. Para ver o nome da conta, escreva:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Nome da partilha** - Este valor `acishare` já é conhecido (definido como no script anterior)

* **Chave da conta** de armazenamento - Este valor pode ser encontrado utilizando o seguinte comando:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Contentor de implantação e volume de montagem - CLI

Para montar uma partilha de ficheiros Azure como volume num recipiente utilizando o Azure CLI, especifique o ponto de partilha e de montagem de volume quando criar o recipiente com [o recipiente az criar][az-container-create]. Se seguir os passos anteriores, pode montar a parte que criou anteriormente utilizando o seguinte comando para criar um recipiente:

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

O `--dns-name-label` valor deve ser único dentro da região de Azure, onde se cria a instância do contentor. Atualize o valor no comando anterior se receber uma mensagem de erro de **etiqueta de nome DNS** quando executar o comando.

## <a name="manage-files-in-mounted-volume"></a>Gerir ficheiros em volume montado

Assim que o recipiente estiver em alta, pode utilizar a simples aplicação web implementada através da imagem [aci-hellofiles][aci-hellofiles] da Microsoft para criar pequenos ficheiros de texto na partilha de ficheiros Azure no caminho de montagem que especificou. Obtenha o nome de domínio totalmente qualificado da aplicação web (FQDN) com o comando de mostra de [contentores az:][az-container-show]

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

Depois de guardar texto utilizando a aplicação, pode utilizar o [portal Azure][portal] ou uma ferramenta como o [Microsoft Azure Storage Explorer][storage-explorer] para recuperar e inspecionar o ficheiro ou ficheiros escritos na partilha de ficheiros.

## <a name="deploy-container-and-mount-volume---yaml"></a>Desdobre o recipiente e o volume de montagem - YAML

Também pode implantar um grupo de contentores e montar um volume num recipiente com o Azure CLI e um [modelo YAML](container-instances-multi-container-yaml.md). A implantação por modelo YAML é um método preferido para a implantação de grupos de contentores compostos por vários contentores.

O seguinte modelo YAML define um grupo de `aci-hellofiles` contentores com um recipiente criado com a imagem. O recipiente monta o *acishare* de partilha de ficheiros Azure criado anteriormente como um volume. Sempre que indicado, introduza o nome e a chave de armazenamento para a conta de armazenamento que acolhe a parte do ficheiro. 

Tal como no exemplo `dnsNameLabel` do CLI, o valor deve ser único dentro da região de Azure, onde se cria a instância do contentor. Atualize o valor no ficheiro YAML, se necessário.

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

Para implantar com o modelo YAML, guarde o `deploy-aci.yaml`YAML anterior para um ficheiro `--file` chamado e, em seguida, execute o [recipiente az criar][az-container-create] comando com o parâmetro:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Desdobre o recipiente e o volume de montagem - Gestor de Recursos

Além da implantação cli e YAML, pode implantar um grupo de contentores e montar um volume num recipiente utilizando um modelo de Gestor de [Recursos](/azure/templates/microsoft.containerinstance/containergroups)Azure .

Primeiro, povoe a `volumes` matriz na secção do grupo `properties` de contentores do modelo. 

Em seguida, para cada recipiente em que deseja `volumeMounts` montar o `properties` volume, povoe a matriz na secção da definição do recipiente.

O seguinte modelo de Gestor de Recursos define `aci-hellofiles` um grupo de contentores com um recipiente criado com a imagem. O recipiente monta o *acishare* de partilha de ficheiros Azure criado anteriormente como um volume. Sempre que indicado, introduza o nome e a chave de armazenamento para a conta de armazenamento que acolhe a parte do ficheiro. 

Tal como nos exemplos `dnsNameLabel` anteriores, o valor deve ser único dentro da região de Azure, onde se cria a instância do contentor. Atualize o valor no modelo, se necessário.

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

Para implantar com o modelo de Gestor de Recursos, `deploy-aci.json`guarde o JSON anterior para `--template-file` um ficheiro nomeado, em seguida, execute a implementação do [grupo Az criar][az-group-deployment-create] comando com o parâmetro:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Monte vários volumes

Para montar vários volumes numa instância de contentores, deve ser implantado utilizando um modelo de Gestor de [Recursos Azure,](/azure/templates/microsoft.containerinstance/containergroups)um ficheiro YAML ou outro método programático. Para utilizar um modelo ou ficheiro YAML, forneça os detalhes `volumes` da partilha `properties` e defina os volumes povoando a matriz na secção do ficheiro. 

Por exemplo, se criasse duas ações do Azure Files *nomeadas share1* e *partilhou2* na conta de armazenamento *myStorageAccount,* a `volumes` matriz num modelo de Gestor de Recursos pareceria semelhante à seguinte:

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

Em seguida, para cada recipiente do grupo de contentores em que `volumeMounts` gostaria `properties` de montar os volumes, povoe a matriz na secção da definição do recipiente. Por exemplo, isto monta os dois volumes, *myvolume1* e *myvolume2*, previamente definidos:

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

Saiba montar outros tipos de volume em instâncias de contentores azure:

* [Monte um volume dir vazio em instâncias de contentores azure](container-instances-volume-emptydir.md)
* [Monte um volume gitRepo em instâncias de contentores azure](container-instances-volume-gitrepo.md)
* [Monte um volume secreto em instâncias de contentores azure](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create