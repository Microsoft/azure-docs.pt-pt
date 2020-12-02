---
title: Implemente VMs no seu dispositivo GPU Azure Stack Edge Pro via Azure PowerShell
description: Descreve como criar e gerir máquinas virtuais (VMs) num dispositivo GPU Azure Stack Edge Pro utilizando a Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: ed6b77f77c9df0bb69edeb7451022605f1633aa3
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454316"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell"></a>Implemente VMs no seu dispositivo GPU Azure Stack Edge Pro via Azure PowerShell

<!--[!INCLUDE [azure-stack-edge-gateway-deploy-vm-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]-->

Este tutorial descreve como criar e gerir um VM no seu dispositivo Azure Stack Edge Pro utilizando a Azure PowerShell.

## <a name="vm-deployment-workflow"></a>Fluxo de trabalho de implantação VM

O fluxo de trabalho de implantação é ilustrado no diagrama seguinte.

![Fluxo de trabalho de implantação VM](media/azure-stack-edge-j-series-deploy-virtual-machine-powershell/vm-workflow_r.svg)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]



## <a name="query-for-built-in-subscription-on-the-device"></a>Consulta para incorporado na subscrição do dispositivo

Para o Azure Resource Manager, apenas uma única subscrição fixa visível pelo utilizador é suportada. Esta subscrição é única por dispositivo e este nome de subscrição ou ID de subscrição não pode ser alterado.

Esta subscrição contém todos os recursos que são criados necessários para a criação de VM. 

> [!IMPORTANT]
> Esta subscrição não está conectada ou relacionada com a sua subscrição Azure e vive localmente no seu dispositivo.

Esta subscrição será usada para implantar os VMs.

1.  Para listar esta subscrição, escreva:

    ```powershell
    Get-AzureRmSubscription
    ```
    
    É apresentada abaixo uma saída de exemplo.

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
3.  Obtenha a lista dos fornecedores de recursos registados em funcionamento no dispositivo. Esta lista normalmente inclui Computação, Rede e Armazenamento.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > Os fornecedores de recursos estão pré-registados e não podem ser modificados ou alterados.
    
    Abaixo é mostrada uma saída de amostra:

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um recipiente lógico no qual os recursos Azure, tais como conta de armazenamento, disco, disco gerido são implantados e geridos.

> [!IMPORTANT]
> Todos os recursos são criados no mesmo local que o do dispositivo e a localização está definida para **DBELocal.**

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

É apresentada abaixo uma saída de exemplo.

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Criar uma nova conta de armazenamento utilizando o grupo de recursos criado no passo anterior. Esta é uma **conta de armazenamento local** que será usada para carregar a imagem virtual do disco para o VM.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Apenas as contas de armazenamento locais, como o armazenamento localmente redundante (Standard_LRS ou Premium_LRS) podem ser criadas através do Azure Resource Manager. Para criar contas de armazenamento hierarquizadas, consulte os passos em [Add, conecte-se às contas de armazenamento no seu Azure Stack Edge Pro](azure-stack-edge-j-series-deploy-add-storage-accounts.md).

É apresentada abaixo uma saída de exemplo.

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

Para obter a chave da conta de armazenamento, executar o `Get-AzureRmStorageAccountKey` comando. Uma amostra deste comando é mostrada aqui.

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-blob-uri-to-hosts-file"></a>Adicione blob URI ao arquivo de anfitriões

Já adicionou o ficheiro blob URI no ficheiro hostis para o cliente que está a usar para ligar ao armazenamento Blob na secção Modificar o [ficheiro do anfitrião para resolução de nome de ponto final](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution). Esta foi a entrada para o URI blob:

\<Azure consistent network services VIP \>\<storage name\>.blob. \<appliance name\> .\<dnsdomain\>


## <a name="install-certificates"></a>Instalar certificados

Se estiver a utilizar *https,* terá de instalar certificados apropriados no seu dispositivo. Neste caso, instale o certificado de ponta final blob. Para mais informações, consulte como criar e carregar certificados em [Obter certificados.](azure-stack-edge-j-series-manage-certificates.md)

## <a name="upload-a-vhd"></a>Carregar um VHD

Copie quaisquer imagens de disco a serem usadas em bolhas de página na conta de armazenamento local que criou nos passos anteriores. Pode utilizar uma ferramenta como [a AzCopy](../storage/common/storage-use-azcopy-v10.md) para fazer o upload do VHD para a conta de armazenamento que criou em etapas anteriores. 

Antes de utilizar o AzCopy, certifique-se de que o [AzCopy está configurado corretamente](#configure-azcopy) para utilização com a versão API de armazenamento de bolhas que está a utilizar com o seu dispositivo Azure Stack Edge Pro.

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> Definir `BlobType` para página para criar um disco gerido a partir de VHD. `BlobType`Desatado para bloquear ao escrever para contas de armazenamento hierárquicas utilizando a AzCopy.

Pode descarregar as imagens do disco do mercado. Para passos detalhados, vá para [obter a imagem virtual do disco do mercado Azure](azure-stack-edge-j-series-create-virtual-machine-image.md).

Uma saída de amostra utilizando a AzCopy 7.3 é mostrada abaixo. Para obter mais informações sobre este comando, aceda ao [ficheiro Upload VHD para a conta de armazenamento utilizando a AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```


## <a name="create-managed-disks-from-the-vhd"></a>Criar discos geridos a partir do VHD

Crie um disco gerido a partir do VHD carregado.

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
Abaixo é mostrada uma saída de amostra: 
<code>
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://</code><code>sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd</code> 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

É apresentada abaixo uma saída de exemplo. Para obter mais informações sobre este cmdlet, vá ao [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0).

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>Criar uma imagem VM a partir do disco gerido pela imagem

Utilize o seguinte comando para criar uma imagem VM a partir do disco gerido. Substitua os valores no interior \< \> pelos nomes que escolher.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

É apresentada abaixo uma saída de exemplo. Para obter mais informações sobre este cmdlet, aceda à [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0).

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-vm-with-previously-created-resources"></a>Criar VM com recursos previamente criados

Tem de criar uma rede virtual e associar uma interface de rede virtual antes de criar e implementar o VM.

> [!IMPORTANT]
> Ao criar rede virtual e interface de rede virtual, aplicam-se as seguintes regras:
> - Apenas um Vnet pode ser criado (mesmo entre grupos de recursos) e deve corresponder exatamente à rede lógica em termos do espaço de endereço.
> -   Só uma sub-rede será permitida no Vnet. A sub-rede deve ser exatamente o mesmo espaço de endereço que o Vnet.
> -   Apenas o método de atribuição estática será permitido durante a criação do Vnic e o utilizador precisa de fornecer um endereço IP privado.

 
**Criar um Vnet**

```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```

**Criar um Vnic utilizando o ID da sub-rede Vnet**

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

A parte da amostra destes comandos é mostrada abaixo:

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

Opcionalmente, ao criar um Vnic para um VM, pode passar o IP público. Neste caso, o IP público devolverá o IP privado. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```


**Criar uma VM**

Agora pode utilizar a imagem VM para criar um VM e anexá-la à rede virtual que criou anteriormente.

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)

You will use this username, password to login to the VM, once it is created and powered up.

$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-a-vm"></a>Ligar a uma VM

Dependendo se criou um Windows ou um Linux VM, os passos para ligar podem ser diferentes.

### <a name="connect-to-linux-vm"></a>Ligue-se ao Linux VM

Siga estes passos para ligar a um Linux VM.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Ligue-se ao Windows VM

Siga estes passos para ligar a um VM do Windows.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


<!--Connect to the VM using the private IP that you passed during the VM creation.

Open an SSH session to connect with the IP address.

`ssh -l <username> <ip address>`

When prompted, provide the password that you used when creating the VM.

If you need to provide the SSH key, use this command.

ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236

If you used a public IP address during VM creation, you can use that IP to connect to the VM. To get the public IP: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
The public IP in this case will be the same as the private IP that you passed during virtual network interface creation.-->


## <a name="manage-vm"></a>Gerir vM

A secção seguinte descreve algumas das operações comuns em torno do VM que irá criar no seu dispositivo Azure Stack Edge Pro.

### <a name="list-vms-running-on-the-device"></a>VMs de lista em execução no dispositivo

Para retornar uma lista de todos os VMs em execução no seu dispositivo Azure Stack Edge Pro, execute o seguinte comando.


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>Ligue o VM

Executar o seguinte cmdlet para ligar uma máquina virtual que funciona no seu dispositivo:


`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`


Para obter mais informações sobre este cmdlet, aceda ao [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0).

### <a name="suspend-or-shut-down-the-vm"></a>Suspender ou desligar o VM

Execute o cmdlet seguinte para parar ou encerrar uma máquina virtual em execução no dispositivo:


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```


Para obter mais informações sobre este cmdlet, aceda à [cmdlet Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0).

### <a name="add-a-data-disk"></a>Adicionar um disco de dados

Se os requisitos de carga de trabalho no seu VM aumentarem, então poderá ter de adicionar um disco de dados.

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>Elimine a VM

Execute o seguinte cmdlet para remover uma máquina virtual do seu dispositivo:

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

Para obter mais informações sobre este cmdlet, aceda à [cmdlet Remove-AzureRmVm](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0).


## <a name="supported-vm-sizes"></a>Tamanhos de VM suportados

O tamanho VM determina a quantidade de recursos computacional como CPU, GPU e memória que são disponibilizados ao VM. As máquinas virtuais devem ser criadas utilizando um tamanho VM adequado para a carga de trabalho. Apesar de todas as máquinas estarem a funcionar no mesmo hardware, os tamanhos das máquinas têm limites diferentes para o acesso ao disco, o que pode ajudá-lo a gerir o acesso geral ao disco através dos seus VMs. Se a carga de trabalho aumentar, uma máquina virtual existente também pode ser redimensionada.

Os VMs da série Standard Dv2 são suportados para criação no dispositivo Azure Stack Edge Pro.

### <a name="dv2-series"></a>Série Dv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Produção de disco Max OS (IOPS) | Produção de armazenamento temporário max (IOPS) | Discos de dados /produção máxima (IOPS) | NICs máximos |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3.5 |50   |500 |3.000  |4 / 4x500   |2 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8x500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16 / 16x500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24000 |32 / 32x500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64 / 64x500 |8 |

### <a name="dsv2-series"></a>Série DSv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Produção de disco Max OS (IOPS) | Produção de armazenamento temporário max (IOPS) | Discos de dados /produção máxima (IOPS) | NICs máximos |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3.5 |7   |1000 |4000  |4 / 4x2300   |2 |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8 / 8x2300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1000 |16000 |16 / 16x2300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32 / 32x2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64 / 64x2300 |8 |

### <a name="dv2-series"></a>Série Dv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Produção de disco Max OS (IOPS) | Produção de armazenamento temporário max (IOPS) | Discos de dados /produção máxima (IOPS) | NICs máximos |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8x500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16 / 16x500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24000  |32 / 32x500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64 / 64x500  |8 |


### <a name="dsv2-series"></a>Série DSv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Produção de disco Max OS (IOPS) | Produção de armazenamento temporário max (IOPS) | Discos de dados /produção máxima (IOPS) | NICs máximos |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |4 / 4x2300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |8 / 8x2300    |4 |
|**Standard_DS13_v2** |8   |56  |112  |1000 |32000  |16 / 16x2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |32 / 32x2300  |8 |

Para obter mais informações, aceda à [série Dv2 em tamanhos VM de finalidade geral](../virtual-machines/dv2-dsv2-series.md#dv2-series).

## <a name="unsupported-vm-operations-and-cmdlets"></a>Operações e cmdlets não suportados

Extensão, conjuntos de escala, conjuntos de disponibilidade, instantâneos não são suportados.

## <a name="configure-azcopy"></a>Configure AzCopy

Quando instalar a versão mais recente do AzCopy, terá de configurar a AzCopy para garantir que corresponde à versão API de armazenamento blob do seu dispositivo Azure Stack Edge Pro.

No cliente utilizado para aceder ao seu dispositivo Azure Stack Edge Pro, crie uma variável global para combinar com a versão API de armazenamento de blob.

### <a name="on-windows-client"></a>No cliente Windows 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### <a name="on-linux-client"></a>No cliente Linux

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

Para verificar se a variável ambiental para a AzCopy foi corretamente definida, tome as seguintes medidas:

1. Executar "azcopy env"
2. Encontre `AZCOPY_DEFAULT_SERVICE_API_VERSION` o parâmetro. Isto deve ter o valor que definiu nos passos anteriores.


## <a name="next-steps"></a>Passos seguintes

[Cmdlets do Gestor de Recursos Azure](/powershell/module/azurerm.resources/?view=azurermps-6.13.0)