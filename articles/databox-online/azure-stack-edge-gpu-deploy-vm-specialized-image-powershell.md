---
title: Crie imagens VM a partir de imagem especializada do Windows VHD para o seu dispositivo GPU Azure Stack Edge Pro
description: Descreve como criar imagens VM a partir de imagens especializadas a partir de um VHD do Windows ou de um VHDX. Utilize esta imagem especializada para criar imagens VM para utilizar com VMs implantados no seu dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: d03aeb9759fb321b580fa65e06dc09ccde4a44a0
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556194"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-device-via-azure-powershell"></a>Implemente um VM a partir de uma imagem especializada no seu dispositivo Azure Stack Edge Pro via Azure PowerShell 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve os passos necessários para implantar uma máquina virtual (VM) no seu dispositivo Azure Stack Edge Pro a partir de uma imagem especializada. 

## <a name="about-specialized-images"></a>Sobre imagens especializadas

Um VHD ou VHDX do Windows pode ser usado para criar uma imagem *especializada* ou uma imagem *generalizada.* A tabela seguinte resume as principais diferenças entre as imagens *especializadas* e as *generalizadas.*


|Tipo de imagem  |Generalizada  |Especializada  |
|---------|---------|---------|
|Destino     |Implantado em qualquer sistema         | Direcionado para um sistema específico        |
|Configuração após arranque     | Configuração necessária no primeiro arranque do VM.          | A armação não é necessária. <br> A plataforma liga o VM.        |
|Configuração     |Nome de anfitrião, utilizador de administração e outras definições específicas de VM necessárias.         |Pré-configurado.         |
|Costumava     |Crie vários VMs novos a partir da mesma imagem.         |Migrar uma máquina específica ou restaurar um VM de cópias de segurança anteriores.         |


Este artigo abrange os passos necessários para implantar a partir de uma imagem especializada. Para implementar a partir de uma imagem generalizada, consulte [utilizar o VHD do Windows generalizado](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) para o seu dispositivo.


## <a name="vm-image-workflow"></a>Fluxo de trabalho de imagem VM

O fluxo de trabalho de alto nível para implantar um VM a partir de uma imagem especializada é:

1. Copie o VHD para uma conta de armazenamento local no seu dispositivo GPU Azure Stack Edge Pro.
1. Crie um novo disco gerido a partir do VHD.
1. Crie uma nova máquina virtual a partir do disco gerido e prenda o disco gerido.


## <a name="prerequisites"></a>Pré-requisitos

Antes de poder colocar um VM no seu dispositivo através do PowerShell, certifique-se de que:

- Tem acesso a um cliente que vai usar para se ligar ao seu dispositivo.
    - O seu cliente gere um [SISTEMA Suportado.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)
    - O seu cliente está configurado para se ligar ao Gestor de Recursos Azure local do seu dispositivo de acordo com as instruções em [Connect to Azure Resource Manager para o seu dispositivo](azure-stack-edge-gpu-connect-resource-manager.md).

## <a name="verify-the-local-azure-resource-manager-connection"></a>Verifique a ligação local do Gestor de Recursos Azure

Verifique se o seu cliente pode ligar-se ao Gestor de Recursos Azure local. 

1. Ligue para apis de dispositivo local para autenticar:

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. Forneça o nome de utilizador `EdgeArmUser` e a palavra-passe para ligar através do Azure Resource Manager. Se não se lembrar da palavra-passe, [reinicie a palavra-passe para O Gestor de Recursos Azure](azure-stack-edge-gpu-set-azure-resource-manager-password.md) e utilize esta palavra-passe para iniciar sação.
 

## <a name="deploy-vm-from-specialized-image"></a>Implementar VM a partir de imagem especializada

As seguintes secções contêm instruções passo a passo para implantar um VM a partir de uma imagem especializada.

## <a name="copy-vhd-to-local-storage-account-on-device"></a>Copiar VHD para conta de armazenamento local no dispositivo

Siga estes passos para copiar o VHD para a conta de armazenamento local:

1. Copie a fonte VHD para uma conta de armazenamento de bolhas locais no seu Azure Stack Edge. 

1. Tome nota do URI resultante. Vais usar este URI num passo posterior.
    
    Para criar e aceder a uma conta de armazenamento local, consulte as secções [Criar uma conta de armazenamento](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account) através do Upload a [VHD](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) no artigo: [Implementar VMs no seu dispositivo Azure Stack Edge via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). 

## <a name="create-a-managed-disk-from-vhd"></a>Criar um disco gerido a partir de VHD

Siga estes passos para criar um disco gerido a partir de um VHD que fez o upload para a conta de armazenamento anteriormente:

1. Estabeleça alguns parâmetros.

    ```powershell
    $VhdURI = <URI of VHD in local storage account>
    $DiskRG = <managed disk resource group>
    $DiskName = <managed disk name>    
    ```
    Aqui está uma saída de exemplo.

    ```powershell
    PS C:\WINDOWS\system32> $VHDURI = "https://myasevmsa.blob.myasegpudev.wdshcsso.com/vhds/WindowsServer2016Datacenter.vhd"
    PS C:\WINDOWS\system32> $DiskRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $DiskName = "myasemd1"
    ```
1. Criar um novo disco gerido.

    ```powershell
    $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VhdURI
    $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    ```

    Aqui está uma saída de exemplo. A localização aqui está definida para a localização da conta de armazenamento local e é sempre `DBELocal` para todas as contas de armazenamento locais no seu dispositivo GPU Azure Stack Edge Pro. 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>Criar um VM a partir de disco gerido

Siga estes passos para criar um VM a partir de um disco gerido:

1. Estabeleça alguns parâmetros.

    ```powershell
    $NicRG = <NIC resource group>
    $NicName = <NIC name>
    $IPConfigName = <IP config name>
    $PrivateIP = <IP address> #Optional
    
    $VMRG = <VM resource group>
    $VMName = <VM name>
    $VMSize = <VM size> 
    ```

    >[!NOTE]
    > O `PrivateIP` parâmetro é opcional. Utilize este parâmetro para atribuir um IP estático mais o padrão é um IP dinâmico usando DHCP.

    Aqui está uma saída de exemplo. Neste exemplo, o mesmo grupo de recursos é especificado para todos os recursos VM, embora possa criar e especificar grupos de recursos separados para os recursos, se necessário.

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. Obtenha a informação de rede virtual e crie uma nova interface de rede.

    Esta amostra pressupõe que está a criar uma única interface de rede na rede virtual padrão `ASEVNET` que está associada ao grupo de recursos predefinidos `ASERG` . Se necessário, pode especificar uma rede virtual alternativa ou criar várias interfaces de rede. Para obter mais informações, consulte [adicionar uma interface de rede a um VM através do portal Azure](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md).

    ```powershell
    $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id [-PrivateIpAddress $PrivateIP]
    $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    ```

    Aqui está uma saída de exemplo.

    ```powershell
    PS C:\WINDOWS\system32> $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    PS C:\WINDOWS\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    WARNING: The output object type of this cmdlet will be modified in a future release.
    PS C:\WINDOWS\system32>    
    ```
1. Crie um novo objeto de configuração VM.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    ```

    
1. Adicione a interface de rede ao VM.

    ```powershell
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

1. Desajuste as propriedades do disco DE no VM.

    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach –[Windows/Linux]
    ```
    A última bandeira deste comando será `-Windows` ou `-Linux` dependendo do sistema operativo que estiver a utilizar para o seu VM.

1. Criar o VM.

    ```powershell
    New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm 
    ```

    Aqui está uma saída de exemplo. 

    ```powershell
    PS C:\WINDOWS\system32> $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    PS C:\WINDOWS\system32> $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    PS C:\WINDOWS\system32> $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach -Windows
    PS C:\WINDOWS\system32> New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm
    WARNING: Since the VM is created using premium storage or managed disk, existing standard storage account, myasevmsa, is used for
    boot diagnostics.    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK        
    PS C:\WINDOWS\system32>
    ```

## <a name="delete-vm-and-resources"></a>Eliminar VM e recursos

Este artigo utilizou apenas um grupo de recursos para criar todo o recurso VM. A eliminação desse grupo de recursos eliminará o VM e todos os recursos associados. 

1. Primeiro ver todos os recursos criados no âmbito do grupo de recursos.

    ```powershell
    Get-AzureRmResource -ResourceGroupName <Resource group name>
    ```
    Aqui está uma saída de exemplo.
    
    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResource -ResourceGroupName myasevm1rg
    
    
    Name              : myasemd1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/disks
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/disk
                        s/myasemd1
    
    Name              : myasetestvm1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/virt
                        ualMachines/myasetestvm1
    
    Name              : myasevmnic1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Network/networkInterfaces
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Network/netw
                        orkInterfaces/myasevmnic1
    
    Name              : myasevmsa
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Storage/stor
                        ageaccounts/myasevmsa
    
    PS C:\WINDOWS\system32>
    ```

1. Elimine o grupo de recursos e todos os recursos associados.

    ```powershell
    Remove-AzureRmResourceGroup -ResourceGroupName <Resource group name>
    ```
    Aqui está uma saída de exemplo.
    
    ```powershell
    PS C:\WINDOWS\system32> Remove-AzureRmResourceGroup -ResourceGroupName myasevm1rg
    
    Confirm
    Are you sure you want to remove resource group 'myasevm1rg'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    True
    PS C:\WINDOWS\system32>
    ```

1. Verifique se o grupo de recursos foi eliminado. Obtenha todos os grupos de recursos que existem no dispositivo. 

    ```powershell
    Get-AzureRmResourceGroup
    ```
    Aqui está uma saída de exemplo.

    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResourceGroup

    ResourceGroupName : ase-image-resourcegroup
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ase-image-resourcegroup
    
    ResourceGroupName : ASERG
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ASERG
    
    ResourceGroupName : myaserg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg
        
    PS C:\WINDOWS\system32>
    ```

## <a name="next-steps"></a>Passos seguintes

Dependendo da natureza da implantação, pode escolher um dos seguintes procedimentos.

- [Implementar um VM a partir de uma imagem generalizada via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
- [Implementar um VM via Portal Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
