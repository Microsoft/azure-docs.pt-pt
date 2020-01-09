---
title: Usar uma VM de solução de problemas do Windows com o Azure PowerShell | Microsoft Docs
description: Saiba como solucionar problemas de VM do Windows no Azure conectando o disco do sistema operacional a uma VM de recuperação usando Azure PowerShell
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 66cda98f272e7353b620059a731972714db585ae
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75374137"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Solucionar problemas de uma VM do Windows anexando o disco do sistema operacional a uma VM de recuperação usando Azure PowerShell
Se sua VM (máquina virtual) do Windows no Azure encontrar um erro de disco ou de inicialização, talvez seja necessário executar as etapas de solução de problemas no próprio disco. Um exemplo comum seria uma atualização de aplicativo com falha que impede que a VM seja capaz de inicializar com êxito. Este artigo fornece detalhes sobre como usar Azure PowerShell para conectar o disco a outra VM do Windows para corrigir erros e, em seguida, reparar a VM original. 

> [!Important]
> Os scripts neste artigo se aplicam somente às VMs que usam o [disco gerenciado](../windows/managed-disks-overview.md). 

 

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
Agora podemos usar Azure PowerShell para alterar o disco do sistema operacional de uma VM. Não precisamos mais excluir e recriar a VM.

O processo de resolução de problemas é o seguinte:

1. Pare a VM afetada.
2. Crie um instantâneo do disco do sistema operacional da VM.
3. Crie um disco do instantâneo do disco do sistema operacional.
4. Anexe o disco como um disco de dados a uma VM de recuperação.
5. Conecte-se à VM de recuperação. Edite arquivos ou execute qualquer ferramenta para corrigir problemas no disco do sistema operacional copiado.
6. Desmonte e desanexe o disco da VM de recuperação.
7. Altere o disco do sistema operacional para a VM afetada.

Você pode usar os comandos de reparo da VM para automatizar as etapas 1, 2, 3, 4, 6 e 7. Para obter mais documentação e instruções, consulte [reparar uma VM do Windows usando os comandos de reparo da máquina virtual do Azure](repair-windows-vm-using-azure-virtual-machine-repair-commands.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Verifique se você tem [as Azure PowerShell mais recentes](/powershell/azure/overview) instaladas e conectadas à sua assinatura:

```powershell
Connect-AzAccount
```

Nos exemplos a seguir, substitua os nomes de parâmetro pelos seus próprios valores. 

## <a name="determine-boot-issues"></a>Determinar problemas de inicialização
Você pode exibir uma captura de tela de sua VM no Azure para ajudar a solucionar problemas de inicialização. Essa captura de tela pode ajudar a identificar por que uma VM não é inicializada. O exemplo a seguir obtém a captura de tela da VM do Windows chamada `myVM` no grupo de recursos chamado `myResourceGroup`:

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Examine a captura de tela para determinar por que a VM está falhando na inicialização. Observe quaisquer mensagens de erro ou códigos de erro específicos fornecidos.

## <a name="stop-the-vm"></a>Parar a VM

O exemplo a seguir interrompe a VM chamada `myVM` do grupo de recursos chamado `myResourceGroup`:

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Aguarde até que a VM tenha concluído a exclusão antes de processar para a próxima etapa.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Criar um instantâneo do disco do sistema operacional da VM

O exemplo a seguir cria um instantâneo com o nome `mySnapshot` do disco do sistema operacional da VM chamada ' myVM '. 

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

Um instantâneo é uma cópia completa e somente leitura de um VHD. Ele não pode ser anexado a uma VM. Na próxima etapa, criaremos um disco a partir desse instantâneo.

## <a name="create-a-disk-from-the-snapshot"></a>Criar um disco a partir do instantâneo

Esse script cria um disco gerenciado com o nome `newOSDisk` do instantâneo chamado `mysnapshot`.  

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

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType = 'Standard_LRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
Agora você tem uma cópia do disco do sistema operacional original. Você pode montar esse disco em outra VM do Windows para fins de solução de problemas.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>Anexar o disco a outra VM do Windows para solução de problemas

Agora anexamos a cópia do disco do sistema operacional original a uma VM como um disco de dados. Esse processo permite que você corrija erros de configuração ou examine arquivos de log do sistema ou do aplicativo adicionais no disco. O exemplo a seguir anexa o disco chamado `newOSDisk` à VM chamada `RecoveryVM`.

> [!NOTE]
> Para anexar o disco, a cópia do disco do sistema operacional original e a VM de recuperação devem estar no mesmo local.

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

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Conectar-se à VM de recuperação e corrigir problemas no disco anexado

1. RDP para sua VM de recuperação usando as credenciais apropriadas. O exemplo a seguir baixa o arquivo de conexão RDP para a VM chamada `RecoveryVM` no grupo de recursos denominado `myResourceGroup`e o baixa para `C:\Users\ops\Documents`"

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. O disco de dados deve ser detectado e anexado automaticamente. Exiba a lista de volumes anexados para determinar a letra da unidade da seguinte maneira:

    ```powershell
    Get-Disk
    ```

    A saída de exemplo a seguir mostra o disco conectado a um disco **2**. (Você também pode usar `Get-Volume` para exibir a letra da unidade):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Depois que a cópia do disco do sistema operacional original for montada, você poderá executar as etapas de manutenção e solução de problemas, conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.

## <a name="unmount-and-detach-original-os-disk"></a>Desmontar e desanexar o disco do sistema operacional original
Depois que os erros forem resolvidos, desmonte e desanexe o disco existente da VM de recuperação. Você não pode usar o disco com nenhuma outra VM até que a concessão que está anexando o disco à VM de recuperação seja liberada.

1. De dentro de sua sessão RDP, desmonte o disco de dados em sua VM de recuperação. Você precisa do número de disco do cmdlet de `Get-Disk` anterior. Em seguida, use `Set-Disk` para definir o disco como offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Confirme se o disco agora está definido como offline usando `Get-Disk` novamente. A saída de exemplo a seguir mostra que o disco agora está definido como offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Saia da sessão RDP. Em sua sessão de Azure PowerShell, remova o disco nomeado `newOSDisk` da VM chamada ' RecoveryVM '.

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Alterar o disco do sistema operacional para a VM afetada

Você pode usar Azure PowerShell para trocar os discos do sistema operacional. Você não precisa excluir e recriar a VM.

Este exemplo interrompe a VM chamada `myVM` e atribui o disco chamado `newOSDisk` como o novo disco do sistema operacional. 

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

## <a name="verify-and-enable-boot-diagnostics"></a>Verificar e habilitar o diagnóstico de inicialização

O exemplo a seguir habilita a extensão de diagnóstico na VM chamada `myVMDeployed` no grupo de recursos chamado `myResourceGroup`:

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Passos seguintes
Se você estiver tendo problemas para se conectar à sua VM, consulte [solucionar problemas de conexões RDP para uma VM do Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para problemas com o acesso a aplicativos em execução na sua VM, consulte [solucionar problemas de conectividade do aplicativo em uma VM do Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para obter mais informações sobre como usar o Gerenciador de recursos, consulte [Azure Resource Manager visão geral](../../azure-resource-manager/management/overview.md).
