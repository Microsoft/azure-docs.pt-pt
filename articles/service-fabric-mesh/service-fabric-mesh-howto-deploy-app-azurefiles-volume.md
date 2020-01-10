---
title: Usar um volume baseado em arquivos do Azure em um aplicativo de malha Service Fabric
description: Saiba como armazenar o estado em um aplicativo de malha de Service Fabric do Azure montando um volume baseado em arquivos do Azure dentro de um serviço usando o CLI do Azure.
author: dkkapur
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: e2172c1808ddf72c09bc08efe680ed497f960b75
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497992"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Montar um volume baseado em arquivos do Azure em um aplicativo de malha Service Fabric 

Este artigo descreve como montar um volume baseado em arquivos do Azure em um serviço de um aplicativo de malha Service Fabric.  O driver de volume do Azure Files é um driver de volume do Docker usado para montar um compartilhamento de arquivos do Azure para um contêiner, que você usa para manter o estado do serviço. Os volumes oferecem armazenamento de arquivos de uso geral e permitem que você leia/grave arquivos usando APIs normais de arquivo de e/s de disco.  Para saber mais sobre volumes e opções para armazenar dados de aplicativos, leia o [estado de armazenamento](service-fabric-mesh-storing-state.md).

Para montar um volume em um serviço, crie um recurso de volume em seu aplicativo de Service Fabric malha e, em seguida, faça referência a esse volume em seu serviço.  Declarar o recurso de volume e fazer referência a ele no recurso de serviço pode ser feito nos [arquivos de recurso baseados em YAML](#declare-a-volume-resource-and-update-the-service-resource-yaml) ou no modelo de [implantação baseado em JSON](#declare-a-volume-resource-and-update-the-service-resource-json). Antes de montar o volume, primeiro crie uma conta de armazenamento do Azure e um [compartilhamento de arquivos nos arquivos do Azure](/azure/storage/files/storage-how-to-create-file-share).

## <a name="prerequisites"></a>Pré-requisitos
> [!NOTE]
> **Problema conhecido com a implantação no computador de desenvolvimento RS5 do Windows:** Há um bug aberto com o cmdlet do PowerShell New-SmbGlobalMapping em computadores com Windows RS5 que impede a montagem de volumes do Azurefile. Abaixo está um erro de exemplo que é encontrado quando o volume baseado no Azure está sendo montado no computador de desenvolvimento local.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
A solução alternativa para o problema é 1) executar o comando a seguir como administrador do PowerShell e 2) reinicializar o computador.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

Você pode usar o Azure Cloud Shell ou uma instalação local do CLI do Azure para concluir este artigo. 

Para usar o CLI do Azure localmente com este artigo, verifique se `az --version` retorna pelo menos `azure-cli (2.0.43)`.  Instale (ou atualize) o módulo de extensão da CLI da malha de Service Fabric do Azure seguindo estas [instruções](service-fabric-mesh-howto-setup-cli.md).

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
O nome da conta de armazenamento, a chave da conta de armazenamento e o nome do compartilhamento de arquivos são referenciados como `<storageAccountName>`, `<storageAccountKey>`e `<fileShareName>` nas seções a seguir. 

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

Você também pode encontrar esses valores no [portal do Azure](https://portal.azure.com):
* `<storageAccountName>`-em **contas de armazenamento**, o nome da conta de armazenamento usada para criar o compartilhamento de arquivos.
* `<storageAccountKey>`-selecione sua conta de armazenamento em **contas de armazenamento** e, em seguida, selecione chaves de **acesso** e use o valor em **key1**.
* `<fileShareName>`-selecione sua conta de armazenamento em **contas de armazenamento** e, em seguida, selecione **arquivos**. O nome a ser usado é o nome do compartilhamento de arquivos que você criou.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Declarar um recurso de volume e atualizar o recurso de serviço (JSON)

Adicione parâmetros para os valores `<fileShareName>`, `<storageAccountName>`e `<storageAccountKey>` encontrados em uma etapa anterior. 

Crie um recurso de volume como um par do recurso de aplicativo. Especifique um nome e o provedor ("SFAzureFile" para usar o volume baseado em arquivos do Azure). Em `azureFileParameters`, especifique os parâmetros para os valores de `<fileShareName>`, `<storageAccountName>`e `<storageAccountKey>` encontrados em uma etapa anterior.

Para montar o volume em seu serviço, adicione um `volumeRefs` ao elemento `codePackages` do serviço.  `name` é a ID de recurso para o volume (ou um parâmetro de modelo de implantação para o recurso de volume) e o nome do volume declarado no arquivo de recurso volume. YAML.  `destinationPath` é o diretório local no qual o volume será montado.

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

Adicione um novo arquivo *volume. YAML* no diretório de *recursos de aplicativo* para seu aplicativo.  Especifique um nome e o provedor ("SFAzureFile" para usar o volume baseado em arquivos do Azure). `<fileShareName>`, `<storageAccountName>`e `<storageAccountKey>` são os valores encontrados em uma etapa anterior.

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

Atualize o arquivo *Service. YAML* no diretório de *recursos de serviço* para montar o volume em seu serviço.  Adicione o elemento `volumeRefs` ao elemento `codePackages`.  `name` é a ID de recurso para o volume (ou um parâmetro de modelo de implantação para o recurso de volume) e o nome do volume declarado no arquivo de recurso volume. YAML.  `destinationPath` é o diretório local no qual o volume será montado.

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

## <a name="next-steps"></a>Passos seguintes

- Exiba o aplicativo de exemplo de volume de arquivos do Azure no [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Para saber mais sobre o Modelo de Recursos do Service Fabric, consulte [Modelo de Recursos do Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).
