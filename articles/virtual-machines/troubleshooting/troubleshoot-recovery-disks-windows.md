---
title: Utilizar um Windows resolver problemas de VMS com o Azure PowerShell | Documentos da Microsoft
description: Saiba como resolver problemas de VM do Windows no Azure ao ligar o disco do SO a uma VM com o Azure PowerShell de recuperação
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 45e595e932c4e7070836372a4fd48791f68c5e08
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761570"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Resolver problemas de uma VM do Windows ao anexar o disco do SO a uma VM com o Azure PowerShell de recuperação
Se a sua máquina virtual (VM) do Windows no Azure encontra um erro de arranque ou disco, terá de efetuar os passos de resolução de problemas no próprio disco. Um exemplo comum seria uma atualização de aplicação com falhas que impede que a VM que está a ser capazes de arrancar com êxito. Este artigo fornece detalhes sobre como utilizar o Azure PowerShell para ligar o disco a outra VM do Windows para corrigir quaisquer erros, em seguida, repare a VM original. 

> [!Important]
> Os scripts neste artigo aplicam-se apenas às VMs que utilizam [disco gerido](../windows/managed-disks-overview.md). 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
Agora, pode utilizar o Azure PowerShell para alterar o disco do SO para uma VM. Já não precisamos de eliminar e recriar a VM.

O processo de resolução de problemas é o seguinte:

1. Pare a VM afetada.
2. Crie um instantâneo de disco do SO da VM.
3. Crie um disco a partir do instantâneo do disco de SO.
4. Anexe o disco como um disco de dados a uma VM de recuperação.
5. Ligue-se para a VM de recuperação. Editar ficheiros ou execute quaisquer ferramentas para corrigir problemas no disco do SO copiado.
6. Desmonte e desanexe o disco da VM de recuperação.
7. Altere o disco do SO para a VM afetada.

Pode usar os scripts de recuperação VM para automatizar passos 1, 2, 3, 4, 6 e 7. Para obter mais documentação e instruções, consulte [Scripts de recuperação de VM para VM do Resource Manager](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager).

Certifique-se de que tenha [o Azure PowerShell mais recente](/powershell/azure/overview) instalado e iniciado sessão sua subscrição:

```powershell
Connect-AzAccount
```

Nos exemplos a seguir, substitua os nomes de parâmetros com seus próprios valores. 

## <a name="determine-boot-issues"></a>Determinar os problemas de arranque
Pode ver uma captura de ecrã da sua VM no Azure para ajudar a resolver problemas de arranque. Nesta captura de ecrã pode ajudar a identificar o motivo da falha de uma VM efetuar o arranque. O exemplo seguinte obtém a captura de ecrã da VM Windows com o nome `myVM` no grupo de recursos com o nome `myResourceGroup`:

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Reveja a captura de ecrã para determinar por que a VM está a falhar efetuar o arranque. Tenha em atenção que quaisquer mensagens de erro específicas ou códigos de erro fornecidos.

## <a name="stop-the-vm"></a>Parar a VM

O seguinte exemplo para a VM com o nome `myVM` do grupo de recursos com o nome `myResourceGroup`:

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Aguarde até que a VM tiver concluído a eliminação do antes de processar para o passo seguinte.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Criar um instantâneo de disco do SO da VM

O exemplo seguinte cria um instantâneo com o nome `mySnapshot` do sistema operacional disco da VM com o nome 'myVM'. 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

Um instantâneo é uma cópia completa, só de leitura de um VHD. Não pode ser ligado a uma VM. O próximo passo, vamos criar um disco deste instantâneo.

## <a name="create-a-disk-from-the-snapshot"></a>Criar um disco a partir do instantâneo

Este script cria um disco gerido com o nome `newOSDisk` partir do instantâneo com o nome `mysnapshot`.  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. PremiumLRS or StandardLRS.
$storageType = 'StandardLRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
Agora tem uma cópia do disco do SO original. Pode montar este disco a outra VM do Windows para fins de resolução de problemas.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>Anexar o disco a outra VM do Windows para resolução de problemas

Agora vamos anexar a cópia do disco do SO original para uma VM como um disco de dados. Este processo permite-lhe corrigir os erros de configuração ou reveja adicionais da aplicação ou ficheiros de registo do sistema no disco. O exemplo seguinte anexa o disco com o nome `newOSDisk` para a VM com o nome `RecoveryVM`.

> [!NOTE]
> Para anexar o disco, a cópia de disco do SO original e a VM de recuperação tem de ser na mesma localização.

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Ligue-se para a VM de recuperação e corrigir problemas no disco anexado

1. RDP para a sua VM de recuperação com as credenciais apropriadas. O exemplo seguinte transfere o ficheiro de ligação de RDP para a VM com o nome `RecoveryVM` no grupo de recursos com o nome `myResourceGroup`e transfere-o para `C:\Users\ops\Documents`"

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. O disco de dados deve ser automaticamente detectado e anexado. Ver a lista de volumes anexados para determinar a letra de unidade da seguinte forma:

    ```powershell
    Get-Disk
    ```

    O resultado de exemplo seguinte mostra o disco ligado um disco **2**. (Também pode usar `Get-Volume` para ver a letra de unidade):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Depois da cópia do disco do SO original está montada, pode executar qualquer manutenção e passos de resolução de problemas, conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.

## <a name="unmount-and-detach-original-os-disk"></a>Desmonte e desanexe o disco de sistema operacional original
Assim que os erros são resolvidos, desmonte e desanexe o disco existente da sua VM de recuperação. Não é possível utilizar o disco com qualquer outra VM até que a atribuição do disco para a VM de recuperação de concessão será libertada.

1. Da sua sessão do RDP, desmonte o disco de dados na sua VM de recuperação. Terá do número do disco do anterior `Get-Disk` cmdlet. Em seguida, utilize `Set-Disk` para configurar o disco como offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Confirmar o disco está agora definido como offline usando `Get-Disk` novamente. O resultado de exemplo seguinte mostra que o disco está agora definido como offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. A sua sessão do RDP de saída. Da sua sessão do Azure PowerShell, remova o disco com o nome `newOSDisk` da VM com o nome 'RecoveryVM'.

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Alterar o disco do SO para a VM afetada

Pode utilizar o Azure PowerShell para trocar os discos de SO. Não tem de eliminar e recriar a VM.

Este exemplo para a VM com o nome `myVM` e atribui o disco com o nome `newOSDisk` como o novo disco de SO. 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>Certifique-se e ativar diagnósticos de arranque

O exemplo seguinte ativa a extensão de diagnóstico na VM com o nome `myVMDeployed` no grupo de recursos com o nome `myResourceGroup`:

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Passos Seguintes
Se estiver a ter problemas de ligação à sua VM, veja [ligações de resolução de problemas de RDP para uma VM do Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para problemas com o acesso aos aplicativos em execução na sua VM, consulte [resolver problemas de conectividade de aplicações numa VM do Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para obter mais informações sobre como utilizar o Resource Manager, consulte [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
