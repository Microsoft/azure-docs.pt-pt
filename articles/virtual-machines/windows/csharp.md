---
title: Criar e gerir uma máquina virtual azure usandoC#
description: Use C# e Azure Resource Manager para implantar uma máquina virtual e todos os seus recursos de apoio.
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
ms.openlocfilehash: 3930e51f63615abd21a7b04199a0f4767925792a
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944502"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Criar e gerir VMs windows em Azure usandoC# #

Uma [Máquina Virtual Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) precisa de vários recursos azure de apoio. Este artigo abrange a criação, gestão C#e aeliminar recursos VM utilizando . Saiba como:

> [!div class="checklist"]
> * Criar um projeto do Visual Studio
> * Instale o pacote
> * Criar credenciais
> * Criar recursos
> * Executar tarefas de gestão
> * Eliminar recursos
> * Executar a aplicação

Leva cerca de 20 minutos para fazer estes passos.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. Se ainda não o fez, instale [o Estúdio Visual.](https://docs.microsoft.com/visualstudio/install/install-visual-studio) Selecione o desenvolvimento do ambiente de **trabalho .NET** na página Workloads e, em seguida, clique em **Instalar**. No resumo, pode ver que as ferramentas de **desenvolvimento .NET Framework 4 - 4.6** são automaticamente selecionadas para si. Se já instalou o Visual Studio, pode adicionar a carga de trabalho .NET utilizando o Visual Studio Launcher.
2. No Visual Studio, clique em **Ficheiro** > **Novo** > **Projeto**.
3. Em **Modelos** > **C#Visual**, selecione **App consola (.NET Framework)** , introduza *myDotnetProject* para o nome do projeto, selecione a localização do projeto e, em seguida, clique EM **OK**.

## <a name="install-the-package"></a>Instale o pacote

Os pacotes NuGet são a forma mais fácil de instalar as bibliotecas que precisa para terminar estes passos. Para obter as bibliotecas que precisa no Estúdio Visual, faça estes passos:

1. Clique em **Ferramentas** > **Nuget Package Manager**e, em seguida, clique na consola do Gestor de **Pacotes**.
2. Digite este comando na consola:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Criar credenciais

Antes de iniciar este passo, certifique-se de que tem acesso a um diretor de serviço de [Diretório Ativo.](../../active-directory/develop/howto-create-service-principal-portal.md) Você também deve registar o ID da aplicação, a chave de autenticação, e o ID do inquilino que você precisa em um passo posterior.

### <a name="create-the-authorization-file"></a>Criar o ficheiro de autorização

1. No Solution Explorer, clique no *myDotnetProject* > **Adicionar** > **Novo Item**, e, em seguida, selecione **Ficheiro de Texto** em Itens *Visuais C#* . Nomeie o ficheiro *azureauth.properties,* e depois clique em **Adicionar**.
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

    Substitua **&lt;&gt;de identificação de assinatura** seleção pelo seu identificador de assinatura,&lt;&gt;de identificação de **aplicação** com o identificador de aplicação Ative Directory, **&lt;&gt;chave de autenticação** com a chave de aplicação e **&lt;&gt;de identificação de inquilino** com o identificador de inquilino.

3. Guarde o ficheiro azureauth.properties. 
4. Detete uma variável ambiental no Windows com o nome AZURE_AUTH_LOCATION com o caminho completo para o ficheiro de autorização que criou. Por exemplo, pode ser utilizado o seguinte comando PowerShell:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Criar o cliente de gestão

1. Abra o ficheiro Program.cs para o projeto que criou. Em seguida, adicione-as usando declarações às declarações existentes no topo do ficheiro:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
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

## <a name="create-resources"></a>Criar recursos

### <a name="create-the-resource-group"></a>Criar o grupo de recursos

Todos os recursos devem ser contidos num [grupo de recursos.](../../azure-resource-manager/management/overview.md)

Para especificar valores para a aplicação e criar o grupo de recursos, adicione este código ao método Principal:

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

[Os conjuntos](tutorial-availability-sets.md) de disponibilidade facilitam a manutenção das máquinas virtuais utilizadas pela sua aplicação.

Para criar o conjunto de disponibilidade, adicione este código ao método Principal:

```csharp
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Criar o endereço IP público

É necessário um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) para comunicar com a máquina virtual.

Para criar o endereço IP público para a máquina virtual, adicione este código ao método Principal:
   
```csharp
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Uma máquina virtual deve estar numa sub-rede de uma [rede Virtual.](../../virtual-network/virtual-networks-overview.md)

Para criar uma subnete e uma rede virtual, adicione este código ao método Principal:

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

Uma máquina virtual precisa de uma interface de rede para comunicar na rede virtual.

Para criar uma interface de rede, adicione este código ao método Principal:

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

Agora que criou todos os recursos de apoio, pode criar uma máquina virtual.

Para criar a máquina virtual, adicione este código ao método Principal:

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
> Este tutorial cria uma máquina virtual que executa uma versão do sistema operativo Windows Server. Para saber mais sobre a seleção de outras imagens, consulte [Navegar e selecione imagens de máquinas virtuais Azure com o Windows PowerShell e o Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Se quiser utilizar um disco existente em vez de uma imagem de mercado, utilize este código:

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

## <a name="perform-management-tasks"></a>Executar tarefas de gestão

Durante o ciclo de vida de uma máquina virtual, poderá querer executar tarefas de gestão, como iniciar, parar ou eliminar uma máquina virtual. Além disso, pode querer criar código para automatizar tarefas repetitivas ou complexas.

Quando precisas de fazer alguma coisa com o VM, precisas de ter uma instância:

```csharp
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Obtenha informações sobre o VM

Para obter informações sobre a máquina virtual, adicione este código ao método Principal:

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

Pode parar uma máquina virtual e manter todas as suas definições, mas continuar a ser carregada por ela, ou pode parar uma máquina virtual e desalocar-a. Quando uma máquina virtual é negociada, todos os recursos associados a ela também são deallocalizados e a faturação termina para ela.

Para parar a máquina virtual sem a deslocar, adicione este código ao método Principal:

```csharp
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Se pretender desalojar a máquina virtual, altere a chamada PowerOff para este código:

```csharp
vm.Deallocate();
```

### <a name="start-the-vm"></a>Iniciar a VM

Para ligar a máquina virtual, adicione este código ao método Principal:

```csharp
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Redimensionar o VM

Muitos aspetos de implantação devem ser considerados ao decidir sobre um tamanho para a sua máquina virtual. Para mais informações, consulte os tamanhos de [VM](sizes.md).  

Para alterar o tamanho da máquina virtual, adicione este código ao método Principal:

```csharp
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Adicione um disco de dados ao VM

Para adicionar um disco de dados à máquina virtual, adicione este código ao método Principal. Este exemplo adiciona um disco de dados de 2 GB de tamanho, han a LUN de 0 e um tipo de cache de ReadWrite:

```csharp
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Eliminar recursos

Uma vez que é cobrado pelos recursos utilizados no Azure, é sempre uma boa prática eliminar recursos que já não são necessários. Se quiser eliminar as máquinas virtuais e todos os recursos de apoio, tudo o que tem de fazer é eliminar o grupo de recursos.

Para eliminar o grupo de recursos, adicione este código ao método Principal:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Executar a aplicação

Deve levar cerca de cinco minutos para que esta aplicação da consola decorra completamente do início ao fim. 

1. Para executar a aplicação da consola, clique em **Iniciar**.

2. Antes de pressionar **Enter** para começar a apagar recursos, poderá demorar alguns minutos a verificar a criação dos recursos no portal Azure. Clique no estado de implementação para ver informações sobre a implementação.

## <a name="next-steps"></a>Passos Seguintes
* Aproveite a utilização de um modelo para criar uma máquina virtual utilizando a informação em [Implementar uma Máquina Virtual Azure utilizando C# e um modelo](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)de Gestor de Recursos .
* Saiba mais sobre a utilização das [bibliotecas Azure para .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).
