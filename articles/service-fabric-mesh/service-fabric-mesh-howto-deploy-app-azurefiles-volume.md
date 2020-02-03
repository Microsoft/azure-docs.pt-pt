---
title: Usar um volume baseado em arquivos do Azure em um aplicativo de malha Service Fabric
description: Saiba como armazenar o estado em um aplicativo de malha de Service Fabric do Azure montando um volume baseado em arquivos do Azure dentro de um serviço usando o CLI do Azure.
author: dkkapur
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 5bb7ab6c861d958f6811ca852363c59cfced3940
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718825"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Montar um volume baseado em arquivos do Azure em um aplicativo de malha Service Fabric 

Este artigo descreve como montar um volume baseado em arquivos do Azure em um serviço de um aplicativo de malha Service Fabric.  O driver de volume do Azure Files é um driver de volume do Docker usado para montar um compartilhamento de arquivos do Azure para um contêiner, que você usa para manter o estado do serviço. Os volumes oferecem armazenamento de arquivos de uso geral e permitem que você leia/grave arquivos usando APIs normais de arquivo de e/s de disco.  Para saber mais sobre volumes e opções para armazenar dados de aplicações, leia [o estado de armazenamento.](service-fabric-mesh-storing-state.md)

Para montar um volume em um serviço, crie um recurso de volume em seu aplicativo de Service Fabric malha e, em seguida, faça referência a esse volume em seu serviço.  Declarar o recurso de volume e referenciar no recurso de serviço pode ser feito quer nos [ficheiros de recursos baseados na YAML](#declare-a-volume-resource-and-update-the-service-resource-yaml) quer no [modelo de implementação baseado em JSON](#declare-a-volume-resource-and-update-the-service-resource-json). Antes de aumentar o volume, crie primeiro uma conta de armazenamento Azure e uma partilha de [ficheiros em Ficheiros Azure](/azure/storage/files/storage-how-to-create-file-share).

## <a name="prerequisites"></a>Pré-requisitos
> [!NOTE]
> **Problema conhecido com implementação na máquina de desenvolvimento Do Windows RS5:** Existe um bug aberto com powershell cmdlet New-SmbGlobalMapping em máquinas RS5 Windows que impedem a montagem de Volumes De FicheiroS Azurefile. Abaixo está um erro de exemplo que é encontrado quando o volume baseado no Azure está sendo montado no computador de desenvolvimento local.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
A suver para o problema é 1)Run abaixo do comando como administrador powershell e 2)Reiniciar a máquina.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

Você pode usar o Azure Cloud Shell ou uma instalação local do CLI do Azure para concluir este artigo. 

Para utilizar o Azure CLI localmente com este artigo, certifique-se de que `az --version` devoluções pelo menos `azure-cli (2.0.43)`.  Instale (ou atualize) o módulo de extensão CLI de malha de tecido de tecido de serviço Azure seguindo estas [instruções](service-fabric-mesh-howto-setup-cli.md).

Para entrar no Azure e definir sua assinatura:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Criar uma conta de armazenamento e um compartilhamento de arquivos (opcional)
A montagem de um volume de arquivos do Azure requer uma conta de armazenamento e um compartilhamento de arquivos.  Você pode usar uma conta de armazenamento do Azure e um compartilhamento de arquivos existentes ou criar recursos:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Obter o nome da conta de armazenamento e a chave e o nome do compartilhamento de arquivos
O nome da conta de armazenamento, a chave da conta de armazenamento e o nome da partilha do ficheiro são referenciados como `<storageAccountName>`, `<storageAccountKey>`e `<fileShareName>` nas seguintes secções. 

Liste suas contas de armazenamento e obtenha o nome da conta de armazenamento com o compartilhamento de arquivos que você deseja usar:
```azurecli-interactive
az storage account list
```

Obtenha o nome do compartilhamento de arquivos:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Obtenha a chave da conta de armazenamento ("key1"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Também pode encontrar estes valores no [portal Azure:](https://portal.azure.com)
* `<storageAccountName>` - Em Contas de **Armazenamento,** o nome da conta de armazenamento utilizada para criar a parte do ficheiro.
* `<storageAccountKey>` - Selecione a sua conta de armazenamento em Contas de Armazenamento e, em seguida, selecione as **teclas** **de acesso** e utilize o valor em baixo **da tecla1**.
* `<fileShareName>` - Selecione a sua conta de armazenamento em **Contas de Armazenamento** e, em seguida, selecione **Ficheiros**. O nome a ser usado é o nome do compartilhamento de arquivos que você criou.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Declarar um recurso de volume e atualizar o recurso de serviço (JSON)

Adicione parâmetros para os valores `<fileShareName>`, `<storageAccountName>`e `<storageAccountKey>` que encontrou num passo anterior. 

Crie um recurso de volume como um par do recurso de aplicativo. Especifique um nome e o provedor ("SFAzureFile" para usar o volume baseado em arquivos do Azure). Em `azureFileParameters`, especifique os parâmetros para os valores `<fileShareName>`, `<storageAccountName>`e `<storageAccountKey>` que encontrou num passo anterior.

Para montar o volume no seu serviço, adicione uma `volumeRefs` ao elemento `codePackages` do serviço.  `name` é o ID de recurso para o volume (ou um parâmetro de modelo de implantação para o recurso de volume) e o nome do volume declarado no ficheiro de recursos volume.yaml.  `destinationPath` é o diretório local a que o volume será montado.

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

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Declarar um recurso de volume e atualizar o recurso de serviço (YAML)

Adicione um novo ficheiro *volume.yaml* no diretório de recursos da *App* para a sua aplicação.  Especifique um nome e o provedor ("SFAzureFile" para usar o volume baseado em arquivos do Azure). `<fileShareName>`, `<storageAccountName>`e `<storageAccountKey>` são os valores que encontrou num passo anterior.

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

Atualize o ficheiro *service.yaml* no diretório de *Recursos de Serviço* para aumentar o volume no seu serviço.  Adicione o elemento `volumeRefs` ao elemento `codePackages`.  `name` é o ID de recurso para o volume (ou um parâmetro de modelo de implantação para o recurso de volume) e o nome do volume declarado no ficheiro de recursos volume.yaml.  `destinationPath` é o diretório local a que o volume será montado.

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

- Ver a aplicação da amostra de volume dos Ficheiros Azure no [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Para saber mais sobre o Modelo de Recursos do Service Fabric, consulte [Modelo de Recursos do Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).
