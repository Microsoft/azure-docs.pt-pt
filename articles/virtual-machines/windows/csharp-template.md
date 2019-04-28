---
title: Implementar uma VM com c# e um modelo do Resource Manager | Documentos da Microsoft
description: Saiba como utilizar c# e um modelo do Resource Manager para implementar uma VM do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: cynthn
ms.openlocfilehash: 50d0d78e9dc0c7f51fcd82dd16eab5a180eae073
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61402188"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Implementar a Máquina Virtual do Azure com c# e um modelo do Resource Manager

Este artigo mostra-lhe como implementar um modelo do Azure Resource Manager com c#. O modelo criado por si implementa uma única máquina virtual com o Windows Server numa rede virtual nova com uma única sub-rede.

Para obter uma descrição detalhada do recurso de máquina virtual, consulte [máquinas virtuais num modelo Azure Resource Manager](template-description.md). Para obter mais informações sobre todos os recursos num modelo, consulte [instruções do modelo do Azure Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Demora cerca de 10 minutos para realizar estes passos.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Neste passo, certifique-se de que o Visual Studio esteja instalado e criar uma aplicação de consola utilizada para implementar o modelo.

1. Se ainda não o fez, instale [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Selecione **desenvolvimento no ambiente de trabalho .NET** na página de cargas de trabalho e, em seguida, clique **instalar**. Em resumo, pode ver que **ferramentas de desenvolvimento do .NET Framework 4 4.6** é selecionado automaticamente para. Se já tiver instalado o Visual Studio, pode adicionar a carga de trabalho do .NET com o Iniciador do Visual Studio.
2. No Visual Studio, clique em **Ficheiro** > **Novo** > **Projeto**.
3. Na **modelos** > **em Visual C#**, selecione **aplicação de consola (.NET Framework)**, introduza *myDotnetProject* para o nome dos projeto, selecione a localização do projeto e, em seguida, clique em **OK**.

## <a name="install-the-packages"></a>Instalar os pacotes

Os pacotes de NuGet são a forma mais fácil de instalar as bibliotecas de que precisa para concluir estes passos. Para obter as bibliotecas que necessita no Visual Studio, execute estes passos:

1. Clique em **ferramentas** > **Gestor de pacotes Nuget**e, em seguida, clique em **Package Manager Console**.
2. Escreva os comandos seguintes na consola do:

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Criar os ficheiros

Neste passo, vai criar um ficheiro de modelo que implementa os recursos e um ficheiro de parâmetros que forneça os valores de parâmetro para o modelo. Também é criar um arquivo de autorização que é usado para executar operações do Azure Resource Manager.

### <a name="create-the-template-file"></a>Criar o ficheiro de modelo

1. No Solution Explorer, clique com botão direito *myDotnetProject* > **Add** > **Novo Item**e, em seguida, selecione **o arquivo de texto** no *Visual C# itens*. Nomeie o arquivo *CreateVMTemplate.json*e, em seguida, clique em **Add**.
2. Adicione este código JSON para o ficheiro que criou:

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

3. Guarde o ficheiro de CreateVMTemplate.json.

### <a name="create-the-parameters-file"></a>Criar o ficheiro de parâmetros

Para especificar valores para os parâmetros de recursos que estão definidos no modelo, crie um ficheiro de parâmetros que contém os valores.

1. No Solution Explorer, clique com botão direito *myDotnetProject* > **Add** > **Novo Item**e, em seguida, selecione **o arquivo de texto** no *Visual C# itens*. Nomeie o arquivo *Parameters. JSON*e, em seguida, clique em **Add**.
2. Adicione este código JSON para o ficheiro que criou:

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

4. Guarde o ficheiro Parameters. JSON.

### <a name="create-the-authorization-file"></a>Criar o ficheiro de autorização

Antes de poder implementar um modelo, certifique-se de que tem acesso a uma [principal de serviço do Active Directory](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). Do principal de serviço, adquirir um token para autenticar pedidos para o Azure Resource Manager. Também deve gravar o ID da aplicação, a chave de autenticação e o ID de inquilino que necessita no ficheiro de autorização.

1. No Solution Explorer, clique com botão direito *myDotnetProject* > **Add** > **Novo Item**e, em seguida, selecione **o arquivo de texto** no *Visual C# itens*. Nomeie o arquivo *azureauth.properties*e, em seguida, clique em **Add**.
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

    Substitua **&lt;id da subscrição&gt;** com o identificador de subscrição **&lt;id da aplicação&gt;** com o aplicativo do Active Directory Identificador, **&lt;chave de autenticação&gt;** com a chave da aplicação, e **&lt;id do inquilino&gt;** com o identificador do inquilino.

3. Guarde o ficheiro de azureauth.properties.
4. Defina uma variável de ambiente no Windows com o nome AZURE_AUTH_LOCATION com o caminho completo para o ficheiro de autorização que criou, por exemplo o PowerShell seguinte pode ser utilizado o comando:

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>Criar o cliente de gestão

1. Abra o ficheiro Program.cs para o projeto que criou e, em seguida, a adicioná-las com instruções para as instruções existentes na parte superior do ficheiro:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Para criar o cliente de gestão, adicione este código ao método Main:

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

Para especificar valores para a aplicação, adicione código ao método Main:

```csharp
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

O modelo e parâmetros são implementados a partir de uma conta de armazenamento no Azure. Neste passo, cria a conta e carregar os ficheiros. 

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

Implemente o modelo e parâmetros da conta de armazenamento que foi criado. 

Para implementar o modelo, adicione este código ao método Main:

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

Uma vez que lhe é cobrados os recursos utilizados no Azure, é sempre boa prática para eliminar os recursos que já não são necessários. Não precisa de eliminar cada recurso separadamente a partir de um grupo de recursos. Elimine o grupo de recursos e todos os recursos serão eliminados automaticamente. 

Para eliminar o grupo de recursos, adicione este código ao método Main:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Executar a aplicação

Deve demorar cerca de cinco minutos para esta aplicação de consola executar totalmente do início ao fim. 

1. Para executar a aplicação de consola, clique em **iniciar**.

2. Antes de premir **Enter** para iniciar a eliminação de recursos, pode demorar alguns minutos para verificar a criação de recursos no portal do Azure. Clique no estado de implementação para ver informações sobre a implementação.

## <a name="next-steps"></a>Passos Seguintes

* Se ocorreram problemas com a implementação, o passo seguinte seria examinar [resolver erros comuns de implementação do Azure com o Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
* Saiba como implementar uma máquina virtual e os respetivos recursos de suporte ao rever [implementar uma Azure Virtual Machine Using c#](csharp.md).