---
title: Implantar uma VM usando C# o e um modelo do Resource Manager | Microsoft Docs
description: Saiba como usar C# o e um modelo do Resource Manager para implantar uma VM do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/14/2017
ms.author: cynthn
ms.openlocfilehash: 65ce7711786e15a5455d91ce829a3bc0bdf4317d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103235"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Implantar uma máquina virtual do Azure C# usando o e um modelo do Resource Manager

Este artigo mostra como implantar um modelo de Azure Resource Manager usando C#o. O modelo que você cria implanta uma única máquina virtual executando o Windows Server em uma nova rede virtual com uma única sub-rede.

Para obter uma descrição detalhada do recurso de máquina virtual, consulte [máquinas virtuais em um modelo de Azure Resource Manager](template-description.md). Para obter mais informações sobre todos os recursos em um modelo, consulte [instruções passo a Azure Resource Manager do modelo](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Leva cerca de 10 minutos para executar essas etapas.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Nesta etapa, você verifica se o Visual Studio está instalado e cria um aplicativo de console usado para implantar o modelo.

1. Se você ainda não fez isso, instale o [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Selecione **.net desktop Development** na página cargas de trabalho e clique em **instalar**. No resumo, você pode ver que **.NET Framework ferramentas de desenvolvimento 4-4,6** são selecionadas automaticamente para você. Se você já tiver instalado o Visual Studio, poderá adicionar a carga de trabalho do .NET usando o iniciador do Visual Studio.
2. No Visual Studio, clique em **Ficheiro** > **Novo** > **Projeto**.
3. No**Visual C#**  **modelos** > , selecione **aplicativo de console (.NET Framework)** , digite *myDotnetProject* para o nome do projeto, selecione o local do projeto e clique em **OK**.

## <a name="install-the-packages"></a>Instalar os pacotes

Os pacotes NuGet são a maneira mais fácil de instalar as bibliotecas necessárias para concluir essas etapas. Para obter as bibliotecas de que você precisa no Visual Studio, siga estas etapas:

1. Clique em **ferramentas** > **Gerenciador de pacotes NuGet**e em **console do Gerenciador de pacotes**.
2. Digite estes comandos no console:

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Criar os arquivos

Nesta etapa, você cria um arquivo de modelo que implanta os recursos e um arquivo de parâmetros que fornece valores de parâmetro para o modelo. Você também cria um arquivo de autorização que é usado para executar operações de Azure Resource Manager.

### <a name="create-the-template-file"></a>Criar o arquivo de modelo

1. Em Gerenciador de soluções, clique com o botão direito do mouse em *myDotnetProject* > **Adicionar** > **novo item**e selecione **arquivo de texto** em *itens visuais C#* . Nomeie o arquivo *CreateVMTemplate. JSON*e clique em **Adicionar**.
2. Adicione este código JSON ao arquivo que você criou:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

3. Salve o arquivo CreateVMTemplate. JSON.

### <a name="create-the-parameters-file"></a>Criar o arquivo de parâmetros

Para especificar valores para os parâmetros de recurso no modelo, você cria um arquivo de parâmetros que contém os valores.

1. Em Gerenciador de soluções, clique com o botão direito do mouse em *myDotnetProject* > **Adicionar** > **novo item**e selecione **arquivo de texto** em *itens visuais C#* . Nomeie o arquivo *Parameters. JSON*e clique em **Adicionar**.
2. Adicione este código JSON ao arquivo que você criou:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

4. Salve o arquivo Parameters. JSON.

### <a name="create-the-authorization-file"></a>Criar o arquivo de autorização

Antes de implantar um modelo, verifique se você tem acesso a uma entidade de [serviço Active Directory](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). Da entidade de serviço, você adquire um token para autenticar solicitações para Azure Resource Manager. Você também deve registrar a ID do aplicativo, a chave de autenticação e a ID do locatário necessários no arquivo de autorização.

1. Em Gerenciador de soluções, clique com o botão direito do mouse em *myDotnetProject* > **Adicionar** > **novo item**e selecione **arquivo de texto** em *itens visuais C#* . Nomeie o arquivo *azureauth. Properties*e clique em **Adicionar**.
2. Adicione estas propriedades de autorização:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    **&lt;&gt;** Substitua **&lt;Subscription-ID&gt;** pelo seu identificador de assinatura, ID do aplicativo pelo identificador do aplicativo Active Directory, **&lt;chave de autenticação com&gt;** a chave do aplicativo e  **&lt;a ID&gt; do locatário** com o identificador do locatário.

3. Salve o arquivo azureauth. Properties.
4. Defina uma variável de ambiente no Windows chamada AZURE_AUTH_LOCATION com o caminho completo para o arquivo de autorização que você criou, por exemplo, você pode usar o seguinte comando do PowerShell:

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>Criar o cliente de gerenciamento

1. Abra o arquivo Program.cs para o projeto que você criou. Em seguida, adicione essas instruções using às instruções existentes na parte superior do arquivo:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Para criar o cliente de gerenciamento, adicione este código ao método principal:

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para especificar valores para o aplicativo, adicione o código ao método Main:

```csharp
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

O modelo e os parâmetros são implantados de uma conta de armazenamento no Azure. Nesta etapa, você cria a conta e carrega os arquivos. 

Para criar a conta, adicione este código ao método Main:

```csharp
string storageAccountName = SdkContext.RandomResourceName("st", 10);

Console.WriteLine("Creating storage account...");
var storage = azure.StorageAccounts.Define(storageAccountName)
    .WithRegion(Region.USWest)
    .WithExistingResourceGroup(resourceGroup)
    .Create();

var storageKeys = storage.GetKeys();
string storageConnectionString = "DefaultEndpointsProtocol=https;"
    + "AccountName=" + storage.Name
    + ";AccountKey=" + storageKeys[0].Value
    + ";EndpointSuffix=core.windows.net";

var account = CloudStorageAccount.Parse(storageConnectionString);
var serviceClient = account.CreateCloudBlobClient();

Console.WriteLine("Creating container...");
var container = serviceClient.GetContainerReference("templates");
container.CreateIfNotExistsAsync().Wait();
var containerPermissions = new BlobContainerPermissions()
    { PublicAccess = BlobContainerPublicAccessType.Container };
container.SetPermissionsAsync(containerPermissions).Wait();

Console.WriteLine("Uploading template file...");
var templateblob = container.GetBlockBlobReference("CreateVMTemplate.json");
templateblob.UploadFromFileAsync("..\\..\\CreateVMTemplate.json").Result();

Console.WriteLine("Uploading parameters file...");
var paramblob = container.GetBlockBlobReference("Parameters.json");
paramblob.UploadFromFileAsync("..\\..\\Parameters.json").Result();
```

## <a name="deploy-the-template"></a>Implementar o modelo

Implante o modelo e os parâmetros da conta de armazenamento que foi criada. 

Para implantar o modelo, adicione este código ao método Main:

```csharp
var templatePath = "https://" + storageAccountName + ".blob.core.windows.net/templates/CreateVMTemplate.json";
var paramPath = "https://" + storageAccountName + ".blob.core.windows.net/templates/Parameters.json";
var deployment = azure.Deployments.Define("myDeployment")
    .WithExistingResourceGroup(groupName)
    .WithTemplateLink(templatePath, "1.0.0.0")
    .WithParametersLink(paramPath, "1.0.0.0")
    .WithMode(Microsoft.Azure.Management.ResourceManager.Fluent.Models.DeploymentMode.Incremental)
    .Create();
Console.WriteLine("Press enter to delete the resource group...");
Console.ReadLine();
```

## <a name="delete-the-resources"></a>Eliminar os recursos

Como você é cobrado pelos recursos usados no Azure, é sempre uma boa prática excluir os recursos que não são mais necessários. Você não precisa excluir cada recurso separadamente de um grupo de recursos. Exclua o grupo de recursos e todos os seus recursos serão excluídos automaticamente. 

Para excluir o grupo de recursos, adicione este código ao método Main:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Executar a aplicação

Deve levar cerca de cinco minutos para que esse aplicativo de console seja executado completamente do início ao fim. 

1. Para executar o aplicativo de console, clique em **Iniciar**.

2. Antes de pressionar **Enter** para iniciar a exclusão de recursos, você pode levar alguns minutos para verificar a criação dos recursos no portal do Azure. Clique no status de implantação para ver informações sobre a implantação.

## <a name="next-steps"></a>Passos Seguintes

* Se houver problemas com a implantação, uma próxima etapa será examinar a [solução de erros comuns de implantação do Azure com o Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
* Saiba como implantar uma máquina virtual e seus recursos de suporte examinando [implantar uma máquina virtual do Azure C#usando ](csharp.md)o.