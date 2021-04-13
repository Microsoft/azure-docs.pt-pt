---
title: Gerir tags VM no dispositivo GPU Azure Stack Edge Pro via Azure PowerShell
description: Descreve como criar e gerir máquinas virtuais para máquinas virtuais que executam em Azure Stack Edge utilizando o Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: be4348359e6b53c3e7454e9ab7c1af8ce8a7020a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305565"
---
# <a name="manage-vm-tags-on-azure-stack-edge-via-azure-powershell"></a>Gerir tags VM em Azure Stack Edge via Azure PowerShell

Este artigo descreve como marcar máquinas virtuais (VMs) em execução nos seus dispositivos GPU Azure Stack Edge Pro utilizando a Azure PowerShell.

## <a name="about-tags"></a>Sobre tags

As etiquetas são pares de valor-chave definidos pelo utilizador que podem ser atribuídos a um recurso ou a um grupo de recursos. Pode aplicar etiquetas em VMs em execução no seu dispositivo para organizar logicamente uma taxonomia. Pode colocar etiquetas num recurso no momento da criação ou adicioná-la a um recurso existente. Por exemplo, pode aplicar o nome `Organization` e o valor a todos os `Engineering` VMs que são utilizados pelo departamento de Engenharia da sua organização.

Para obter mais informações sobre etiquetas, consulte como [gerir as tags através da AzureRM PowerShell](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true).

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder colocar um VM no seu dispositivo através do PowerShell, certifique-se de que:

- Tem acesso a um cliente que vai usar para se ligar ao seu dispositivo.
    - O seu cliente gere um [SISTEMA Suportado.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)
    - O seu cliente está configurado para se ligar ao Gestor de Recursos Azure local do seu dispositivo de acordo com as instruções em [Connect to Azure Resource Manager para o seu dispositivo](azure-stack-edge-gpu-connect-resource-manager.md).


## <a name="verify-connection-to-local-azure-resource-manager"></a>Verifique a ligação ao Gestor de Recursos Azure local

[!INCLUDE [azure-stack-edge-gateway-verify-azure-resource-manager-connection](../../includes/azure-stack-edge-gateway-verify-azure-resource-manager-connection.md)]


## <a name="add-a-tag-to-a-vm"></a>Adicione uma etiqueta a um VM

1. Estabeleça alguns parâmetros.

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
    $TagValue = <Tag Value>   
    ```

    Aqui está uma saída de exemplo:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```

2. Pegue o objeto VM e as suas etiquetas.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $tags = $VirtualMachine.Tags
   ```

3. Adicione a etiqueta e atualize o VM. A atualização do VM pode demorar alguns minutos.

    Pode utilizar a bandeira opcional **-Force** para executar o comando sem confirmação do utilizador.

    ```powershell
    $tags.Add($TagName, $TagValue)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```

    Aqui está uma saída de exemplo:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Add($TagName, $TagValue)
    PS C:\WINDOWS\system32> Set-AzureRmResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    
    Name              : myasetestvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
                        lMachines/myasetestvm1
    ResourceName      : myasetestvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg2
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {Organization}
    Properties        : @{vmId=958c0baa-e143-4d8a-82bd-9c6b1ba45e86; hardwareProfile=; storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    
    PS C:\WINDOWS\system32>
    ```

Para mais informações, consulte [Add-AzureRMTag](/powershell/module/azurerm.tags/remove-azurermtag?view=azurermps-6.13.0&preserve-view=true).

## <a name="view-tags-of-a-vm"></a>Ver tags de um VM

Pode ver as etiquetas aplicadas a uma máquina virtual específica em funcionamento no seu dispositivo. 

1. Defina os parâmetros associados ao VM cujas etiquetas pretende ver.

   ```powershell
   $VMName = <VM Name>
   $VMRG = <VM Resource Group>
   ```
    Aqui está uma saída de exemplo:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```
1. Pegue o objeto VM e veja as suas etiquetas.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $VirtualMachine.Tags
   ```
    Aqui está uma saída de exemplo:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $VirtualMachine

    ResourceGroupName : myaserg2
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
    lMachines/myasetestvm1
    VmId              : 958c0baa-e143-4d8a-82bd-9c6b1ba45e86
    Name              : myasetestvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Sales"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
    
    PS C:\WINDOWS\system32>
    ```
## <a name="view-tags-for-all-resources"></a>Ver tags para todos os recursos

Para ver a lista atual de tags para todos os recursos na subscrição local do Azure Resource Manager (diferente da subscrição Azure) do seu dispositivo, utilize o `Get-AzureRMTag` comando.


Aqui está uma saída de exemplo quando vários VMs estão a ser em execução no seu dispositivo e pretende ver todas as etiquetas em todos os VMs.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag

Name         Count
----         -----
Organization 3

PS C:\WINDOWS\system32>
```

A saída anterior indica que existem três `Organization` tags nos VMs em execução no seu dispositivo.

Para ver mais detalhes, use o `-Detailed` parâmetro.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag -Detailed |fl

Name        : Organization
ValuesTable :
              Name         Count
              ===========  =====
              Engineering  2
              Sales        1

Count       : 3
Values      : {Engineering, Sales}

PS C:\WINDOWS\system32>
```

A saída anterior indica que, das três etiquetas, 2 VMs são marcados como `Engineering` e um é marcado como pertencente a `Sales` .

## <a name="remove-a-tag-from-a-vm"></a>Remova uma etiqueta de um VM

1. Estabeleça alguns parâmetros. 

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
   ``` 
    Aqui está uma saída de exemplo:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myaselinuxvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg1"
    PS C:\WINDOWS\system32> $TagName = "Organization" 
    ```
2. Pegue o objeto VM.

    ```powershell
    $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
    $VirtualMachine   
    ```   

    Aqui está uma saída de exemplo:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    ResourceGroupName : myaserg1
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselinuxvm1
    VmId              : 290b3fdd-0c99-4905-9ea1-cf93cd6f25ee
    Name              : myaselinuxvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Engineering"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
                                                                              PS C:\WINDOWS\system32> 
    ```
3. Retire a etiqueta e atualize o VM. Utilize a bandeira opcional `-Force` para executar o comando sem confirmação do utilizador.

    ```powershell
    $tags = $VirtualMachine.Tags
    $tags.Remove($TagName)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```
    Aqui está uma saída de exemplo:

    ```powershell
    PS C:\WINDOWS\system32> $tags = $Virtualmachine.Tags
    PS C:\WINDOWS\system32> $tags
    Key          Value
    ---          -----
    Organization Engineering
    PS C:\WINDOWS\system32> $tags.Remove($TagName)
    True
    PS C:\WINDOWS\system32> Set-AzureRMResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    Name              : myaselinuxvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGrou
                        ps/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselin
                        uxvm1
    ResourceName      : myaselinuxvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg1
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {}
    Properties        : @{vmId=290b3fdd-0c99-4905-9ea1-cf93cd6f25ee; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    PS C:\WINDOWS\system32>
    ```


## <a name="next-steps"></a>Passos seguintes

Saiba como [gerir as tags através do AzureRM PowerShell](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true).
