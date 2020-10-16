---
title: Implemente um VM usando C# e um modelo de gestor de recursos
description: Aprenda a usar C# e um modelo de Gestor de Recursos para implementar um VM Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: how-to
ms.date: 07/14/2017
ms.author: cynthn
ms.custom: devx-track-csharp
ms.openlocfilehash: 779a09532790ea272d8c95ac28f8c152216efc5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89002968"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Implemente uma máquina virtual Azure usando C# e um modelo de gestor de recursos

Este artigo mostra-lhe como implementar um modelo de Gestor de Recursos Azure usando C#. O modelo que cria implementa uma única máquina virtual que executa o Windows Server numa nova rede virtual com uma única sub-rede.

Para obter uma descrição detalhada do recurso de máquina virtual, consulte [máquinas virtuais num modelo de Gestor de Recursos Azure](template-description.md). Para obter mais informações sobre todos os recursos de um modelo, consulte [o modelo do Azure Resource Manager através do walkthrough](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

Leva cerca de 10 minutos para fazer estes passos.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Neste passo, certifique-se de que o Visual Studio está instalado e cria uma aplicação de consola usada para implementar o modelo.

1. Se ainda não o fez, instale [o Visual Studio](/visualstudio/install/install-visual-studio). Selecione **o desenvolvimento do ambiente de trabalho .NET** na página Workloads e, em seguida, clique em **Instalar**. No resumo, pode ver que **as ferramentas de desenvolvimento .NET Framework 4 - 4.6** são automaticamente selecionadas para si. Se já instalou o Visual Studio, pode adicionar a carga de trabalho .NET utilizando o Lançador de Estúdio Visual.
2. No Estúdio Visual, clique em **File**  >  **New**  >  **Project**.
3. Em **Modelos**  >  **Visual C#**, selecione Console App **(.NET Framework)**, insira *o myDotnetProject* para o nome do projeto, selecione a localização do projeto e, em seguida, clique em **OK**.

## <a name="install-the-packages"></a>Instalar as embalagens

Os pacotes NuGet são a forma mais fácil de instalar as bibliotecas que precisa para terminar estes passos. Para obter as bibliotecas que você precisa no Estúdio Visual, faça estes passos:

1. Clique em **Tools**  >  **Nuget Package Manager**e, em seguida, clique na Consola de Gestor de **Pacotes**.
2. Digite estes comandos na consola:

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Criar os ficheiros

Neste passo, você cria um ficheiro de modelo que implementa os recursos e um ficheiro de parâmetros que fornece valores de parâmetros para o modelo. Também cria um ficheiro de autorização que é utilizado para executar operações do Azure Resource Manager.

### <a name="create-the-template-file"></a>Crie o ficheiro de modelo

1. No Solution Explorer, clique com o botão direito *myDotnetProject*  >  **Add**  >  **New Item**e, em seguida, selecione Ficheiro de **Texto** em *Itens Visuais C#*. Nomeie o ficheiro *CreateVMTemplate.jsligado*e, em seguida, clique em **Adicionar**.
2. Adicione este código JSON ao ficheiro que criou:

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

3. Guarde o CreateVMTemplate.jsarquivado.

### <a name="create-the-parameters-file"></a>Criar o ficheiro de parâmetros

Para especificar valores para os parâmetros de recurso no modelo, cria um ficheiro de parâmetros que contém os valores.

1. No Solution Explorer, clique com o botão direito *myDotnetProject*  >  **Add**  >  **New Item**e, em seguida, selecione Ficheiro de **Texto** em *Itens Visuais C#*. Nomeie o ficheiro *Parameters.jsligado*e, em seguida, clique em **Adicionar**.
2. Adicione este código JSON ao ficheiro que criou:

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

4. Guarde o Parameters.jsarquivado.

### <a name="create-the-authorization-file"></a>Criar o ficheiro de autorização

Antes de poder implementar um modelo, certifique-se de que tem acesso a um [diretor de serviço de Diretório Ativo.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) A partir do diretor de serviço, você adquire um símbolo para autenticação de pedidos para Azure Resource Manager. Deve também registar o ID do pedido, a chave de autenticação e a identificação do inquilino que precisa no ficheiro de autorização.

1. No Solution Explorer, clique com o botão direito *myDotnetProject*  >  **Add**  >  **New Item**e, em seguida, selecione Ficheiro de **Texto** em *Itens Visuais C#*. Nomeie o ficheiro *azureauth.properties*e, em seguida, clique em **Adicionar**.
2. Adicione estas propriedades de autorização:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Substitua o ** &lt; &gt; id de subscrição** pelo seu identificador de subscrição, ** &lt; id &gt; de aplicação** pelo identificador de aplicação ative, ** &lt; chave &gt; de autenticação** com a chave de aplicação e ** &lt; id &gt; do inquilino** pelo identificador do inquilino.

3. Guarde o ficheiro azureauth.properties.
4. Desaprove uma variável ambiental no Windows denominada AZURE_AUTH_LOCATION com o caminho completo para o ficheiro de autorização que criou, por exemplo, pode utilizar o seguinte comando PowerShell:

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>Criar o cliente de gestão

1. Abra o arquivo Program.cs para o projeto que criou. Em seguida, adicione estas declarações usando as declarações existentes no topo do ficheiro:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Para criar o cliente de gestão, adicione este código ao método Principal:

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

Para especificar os valores para a aplicação, adicione código ao método principal:

```csharp
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

O modelo e os parâmetros são implantados a partir de uma conta de armazenamento em Azure. Neste passo, cria-se a conta e faz o upload dos ficheiros. 

Para criar a conta, adicione este código ao método principal:

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

Implemente o modelo e os parâmetros a partir da conta de armazenamento que foi criada. 

Para implementar o modelo, adicione este código ao método principal:

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

Por ser cobrado pelos recursos utilizados no Azure, é sempre uma boa prática apagar recursos que já não são necessários. Não é necessário eliminar cada recurso separadamente de um grupo de recursos. Eliminar o grupo de recursos e todos os seus recursos são automaticamente eliminados. 

Para eliminar o grupo de recursos, adicione este código ao método Principal:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Executar a aplicação

Deve levar cerca de cinco minutos para que esta aplicação da consola corra completamente do início ao fim. 

1. Para executar a aplicação da consola, clique em **Iniciar**.

2. Antes de pressionar **Enter** para começar a apagar recursos, pode demorar alguns minutos a verificar a criação dos recursos no portal Azure. Clique no estado de implementação para ver informações sobre a implementação.

## <a name="next-steps"></a>Passos seguintes

* Se houvesse problemas com a implantação, um passo seguinte seria analisar [os erros comuns de implementação do Azure com o Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md).
* Saiba como implementar uma máquina virtual e os seus recursos de apoio através da revisão [de implementar uma máquina virtual Azure usando C#](../../azure-resource-manager/templates/deploy-rest.md).