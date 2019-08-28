---
title: Criar e gerenciar uma máquina virtual do Azure C# usando | Microsoft Docs
description: Use C# e Azure Resource Manager para implantar uma máquina virtual e todos os seus recursos de suporte.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: c6d092889deec934f1db1f1c93c06aa0dc217df5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079600"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Criar e gerenciar VMs do Windows no Azure usandoC# #

Uma VM ( [máquina virtual) do Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) precisa de vários recursos do Azure de suporte. Este artigo aborda a criação, o gerenciamento e a exclusão de C#recursos da VM usando o. Saiba como:

> [!div class="checklist"]
> * Criar um projeto do Visual Studio
> * Instalar o pacote
> * Criar credenciais
> * Criar recursos
> * Executar tarefas de gerenciamento
> * Eliminar recursos
> * Executar a aplicação

Leva cerca de 20 minutos para executar essas etapas.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. Se você ainda não fez isso, instale o [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Selecione **.net desktop Development** na página cargas de trabalho e clique em **instalar**. No resumo, você pode ver que **.NET Framework ferramentas de desenvolvimento 4-4,6** são selecionadas automaticamente para você. Se você já tiver instalado o Visual Studio, poderá adicionar a carga de trabalho do .NET usando o iniciador do Visual Studio.
2. No Visual Studio, clique em **Ficheiro** > **Novo** > **Projeto**.
3. No**Visual C#**  **modelos** > , selecione **aplicativo de console (.NET Framework)** , digite *myDotnetProject* para o nome do projeto, selecione o local do projeto e clique em **OK**.

## <a name="install-the-package"></a>Instalar o pacote

Os pacotes NuGet são a maneira mais fácil de instalar as bibliotecas necessárias para concluir essas etapas. Para obter as bibliotecas de que você precisa no Visual Studio, siga estas etapas:

1. Clique em **ferramentas** > **Gerenciador de pacotes NuGet**e em **console do Gerenciador de pacotes**.
2. Digite este comando no console:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Criar credenciais

Antes de iniciar esta etapa, verifique se você tem acesso a uma [entidade de serviço Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). Você também deve registrar a ID do aplicativo, a chave de autenticação e a ID do locatário necessários em uma etapa posterior.

### <a name="create-the-authorization-file"></a>Criar o arquivo de autorização

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
4. Defina uma variável de ambiente no Windows chamada AZURE_AUTH_LOCATION com o caminho completo para o arquivo de autorização que você criou. Por exemplo, o seguinte comando do PowerShell pode ser usado:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Criar o cliente de gerenciamento

1. Abra o arquivo Program.cs para o projeto que você criou. Em seguida, adicione essas instruções using às instruções existentes na parte superior do arquivo:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
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

## <a name="create-resources"></a>Criar recursos

### <a name="create-the-resource-group"></a>Criar o grupo de recursos

Todos os recursos devem estar contidos em um [grupo de recursos](../../azure-resource-manager/resource-group-overview.md).

Para especificar valores para o aplicativo e criar o grupo de recursos, adicione este código ao método Main:

```csharp
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Criar o conjunto de disponibilidade

Os [conjuntos de disponibilidade](tutorial-availability-sets.md) tornam mais fácil para você manter as máquinas virtuais usadas pelo seu aplicativo.

Para criar o conjunto de disponibilidade, adicione este código ao método Main:

```csharp
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Criar o endereço IP público

Um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) é necessário para se comunicar com a máquina virtual.

Para criar o endereço IP público para a máquina virtual, adicione este código ao método Main:
   
```csharp
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Uma máquina virtual deve estar em uma sub-rede de uma [rede virtual](../../virtual-network/virtual-networks-overview.md).

Para criar uma sub-rede e uma rede virtual, adicione este código ao método Main:

```csharp
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>Criar a interface de rede

Uma máquina virtual precisa de uma interface de rede para se comunicar na rede virtual.

Para criar uma interface de rede, adicione este código ao método Main:

```csharp
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual

Agora que você criou todos os recursos de suporte, você pode criar uma máquina virtual.

Para criar a máquina virtual, adicione este código ao método Main:

```csharp
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> Este tutorial cria uma máquina virtual que executa uma versão do sistema operacional Windows Server. Para saber mais sobre como selecionar outras imagens, consulte [navegar e selecionar imagens de máquina virtual do Azure com o Windows PowerShell e o CLI do Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Se você quiser usar um disco existente em vez de uma imagem do Marketplace, use este código:

```csharp
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>Executar tarefas de gerenciamento

Durante o ciclo de vida de uma máquina virtual, poderá querer executar tarefas de gestão, como iniciar, parar ou eliminar uma máquina virtual. Além disso, talvez você queira criar código para automatizar tarefas repetitivas ou complexas.

Quando precisar fazer alguma coisa com a VM, você precisará obter uma instância dela:

```csharp
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Obter informações sobre a VM

Para obter informações sobre a máquina virtual, adicione este código ao método Main:

```csharp
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>Parar a VM

Você pode interromper uma máquina virtual e manter todas as suas configurações, mas continuar a ser cobrado por ela, ou pode parar uma máquina virtual e desalocá-la. Quando uma máquina virtual é desalocada, todos os recursos associados a ela também são desalocados e a cobrança termina.

Para parar a máquina virtual sem desalocá-la, adicione este código ao método Main:

```csharp
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Se você quiser desalocar a máquina virtual, altere a chamada estado desligado para este código:

```csharp
vm.Deallocate();
```

### <a name="start-the-vm"></a>Iniciar a VM

Para iniciar a máquina virtual, adicione este código ao método Main:

```csharp
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Redimensionar a VM

Muitos aspectos da implantação devem ser considerados ao decidir sobre um tamanho para sua máquina virtual. Para obter mais informações, consulte [tamanhos de VM](sizes.md).  

Para alterar o tamanho da máquina virtual, adicione este código ao método Main:

```csharp
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Adicionar um disco de dados à VM

Para adicionar um disco de dados à máquina virtual, adicione este código ao método Main. Este exemplo adiciona um disco de dados que tem 2 GB de tamanho, Han a LUN 0 e um tipo de cache de ReadWrite:

```csharp
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Eliminar recursos

Como você é cobrado pelos recursos usados no Azure, é sempre uma boa prática excluir os recursos que não são mais necessários. Se você quiser excluir as máquinas virtuais e todos os recursos de suporte, tudo o que você precisa fazer é excluir o grupo de recursos.

Para excluir o grupo de recursos, adicione este código ao método Main:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Executar a aplicação

Deve levar cerca de cinco minutos para que esse aplicativo de console seja executado completamente do início ao fim. 

1. Para executar o aplicativo de console, clique em **Iniciar**.

2. Antes de pressionar **Enter** para iniciar a exclusão de recursos, você pode levar alguns minutos para verificar a criação dos recursos no portal do Azure. Clique no status de implantação para ver informações sobre a implantação.

## <a name="next-steps"></a>Passos Seguintes
* Aproveite o uso de um modelo para criar uma máquina virtual usando as informações em [implantar uma máquina virtual do Azure usando C# o e um modelo do Resource Manager](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Saiba mais sobre como usar as [bibliotecas do Azure para .net](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).
