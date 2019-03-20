---
title: Usar um volume de ficheiros do Azure com base num aplicativo de malha do Service Fabric | Documentos da Microsoft
description: Saiba como armazenar o estado num aplicativo de malha do Azure Service Fabric ao montar um volume de ficheiros do Azure com base no interior de um serviço com a CLI do Azure.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 4cb7d04c01ae7173e63778f2768b2f9561dff11d
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200962"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Monte um volume de ficheiros do Azure com base num aplicativo de malha do Service Fabric 

Este artigo descreve como montar um volume de ficheiros do Azure com base num serviço de um aplicativo de malha do Service Fabric.  O driver de volume de ficheiros do Azure é um driver de volume do Docker usado para montar uma partilha de ficheiros do Azure para um contentor, o que utilizar para persistir o estado do serviço. Volumes dão-lhe o armazenamento de ficheiros para fins gerais e permitem-lhe ler/escrever ficheiros através de APIs de ficheiros de e/s de disco normal.  Para obter mais informações sobre volumes e as opções para armazenar os dados da aplicação, leia [armazenando o estado](service-fabric-mesh-storing-state.md).

Para montar um volume num serviço, criar um recurso de volume na sua aplicação Mesh de recursos de infraestrutura do serviço e, em seguida, fazer referência a esse volume no seu serviço.  Declarando o recurso de volume e fazer referência a ele no recurso de serviço podem ser feitos qualquer um do [arquivos de recursos com base em YAML](#declare-a-volume-resource-and-update-the-service-resource-yaml) ou o [modelo de implementação baseados em JSON](#declare-a-volume-resource-and-update-the-service-resource-json). Antes de montar o volume, primeiro crie uma conta de armazenamento do Azure e um [partilha de ficheiros nos ficheiros do Azure](/azure/storage/files/storage-how-to-create-file-share).

## <a name="prerequisites"></a>Pré-requisitos

Pode utilizar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir este artigo. 

Para utilizar a CLI do Azure localmente neste artigo, certifique-se de que `az --version` retorna, pelo menos, `azure-cli (2.0.43)`.  Instalar (ou atualizar) o módulo de extensão de CLI de malha do Azure Service Fabric através destas [instruções](service-fabric-mesh-howto-setup-cli.md).

Para iniciar sessão no Azure e definir a subscrição:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Criar uma partilha de ficheiro e da conta de armazenamento (opcional)
Montar um volume de ficheiros do Azure requer uma partilha de ficheiro e da conta de armazenamento.  Pode utilizar uma partilha de ficheiro e da conta de armazenamento do Azure existente ou criar recursos:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Obter o nome da conta de armazenamento e a chave e o nome da partilha de ficheiros
O nome da conta de armazenamento, conta de armazenamento e o nome da partilha de ficheiros que são referenciadas como `<storageAccountName>`, `<storageAccountKey>`, e `<fileShareName>` nas seções a seguir. 

Listar as contas de armazenamento e obter o nome da conta de armazenamento com a partilha de ficheiros que pretende utilizar:
```azurecli-interactive
az storage account list
```

Obter o nome da partilha de ficheiros:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Obter a chave de conta de armazenamento ("key1"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Também pode encontrar estes valores no [portal do Azure](https://portal.azure.com):
* `<storageAccountName>` – Em **contas de armazenamento**, o nome da conta de armazenamento utilizado para criar a partilha de ficheiros.
* `<storageAccountKey>` -Selecione a sua conta de armazenamento sob **contas de armazenamento** e, em seguida, selecione **chaves de acesso** e utilizar o valor sob **chave1**.
* `<fileShareName>` -Selecione a sua conta de armazenamento sob **contas de armazenamento** e, em seguida, selecione **ficheiros**. O nome a utilizar é o nome da partilha de ficheiros que criou.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Declare um recurso de volume e atualizar o recurso de serviço (JSON)

Adicionar parâmetros para o `<fileShareName>`, `<storageAccountName>`, e `<storageAccountKey>` valores encontrados num passo anterior. 

Crie um recurso de Volume como um par do recurso de aplicação. Especifique um nome e o fornecedor ("SFAzureFile" para utilizar o volume de ficheiros do Azure com base). Na `azureFileParameters`, especifique os parâmetros para o `<fileShareName>`, `<storageAccountName>`, e `<storageAccountKey>` valores encontrados num passo anterior.

Para montar o volume no seu serviço, adicione uma `volumeRefs` para o `codePackages` elemento do serviço.  `name` é o ID de recurso para o volume (ou um parâmetro de modelo de implementação para o recurso de volume) e o nome do volume declarado no arquivo de recursos de volume.yaml.  `destinationPath` é o diretório de local que o volume será montado para.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Declare um recurso de volume e atualizar o recurso de serviço (YAML)

Adicionar um novo *volume.yaml* de ficheiros a *recursos de aplicação* diretório para a sua aplicação.  Especifique um nome e o fornecedor ("SFAzureFile" para utilizar o volume de ficheiros do Azure com base). `<fileShareName>`, `<storageAccountName>`, e `<storageAccountKey>` são os valores encontrados num passo anterior.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

Atualização do *service.yaml* do ficheiro no *recursos de serviço* diretório para montar o volume no seu serviço.  Adicionar a `volumeRefs` elemento para a `codePackages` elemento.  `name` é o ID de recurso para o volume (ou um parâmetro de modelo de implementação para o recurso de volume) e o nome do volume declarado no arquivo de recursos de volume.yaml.  `destinationPath` é o diretório de local que o volume será montado para.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>Passos Seguintes

- Ver a aplicação de exemplo do volume de ficheiros do Azure no [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Para saber mais sobre o Modelo de Recursos do Service Fabric, consulte [Modelo de Recursos do Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).
