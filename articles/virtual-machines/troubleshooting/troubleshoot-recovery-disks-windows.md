---
title: Utilize um VM de resolução de problemas do Windows com a Azure PowerShell [ Microsoft Docs
description: Saiba como resolver problemas com problemas com os problemas do Windows VM em Azure, ligando o disco OS a um VM de recuperação utilizando o Azure PowerShell
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75374137"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Resolução de problemas de um VM windows, ligando o disco OS a um VM de recuperação utilizando o Azure PowerShell
Se a sua máquina virtual Windows (VM) em Azure encontrar um erro de arranque ou disco, poderá ter de executar passos de resolução de problemas no próprio disco. Um exemplo comum seria uma atualização de aplicação falhada que impede que o VM seja capaz de arrancar com sucesso. Este artigo detalha como utilizar o Azure PowerShell para ligar o disco a outro Windows VM para corrigir quaisquer erros e, em seguida, reparar o seu VM original. 

> [!Important]
> Os scripts deste artigo aplicam-se apenas aos VMs que utilizam [o Disco Gerido](../windows/managed-disks-overview.md). 

 

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
Agora podemos usar o Azure PowerShell para alterar o disco OS para um VM. Já não precisamos de apagar e recriar o VM.

O processo de resolução de problemas é o seguinte:

1. Pare o VM afetado.
2. Crie uma imagem do Disco OS do VM.
3. Crie um disco a partir do instantâneo do disco OS.
4. Fixe o disco como um disco de dados a um VM de recuperação.
5. Ligue-se ao VM de recuperação. Editar ficheiros ou executar quaisquer ferramentas para corrigir problemas no disco OS copiado.
6. Desmontar e separar o disco da VM de recuperação.
7. Altere o disco OS para o VM afetado.

Pode utilizar os comandos de reparação vm para automatizar os passos 1, 2, 3, 4, 6 e 7. Para obter mais documentação e instruções, consulte [Reparar um VM do Windows utilizando os comandos de reparação](repair-windows-vm-using-azure-virtual-machine-repair-commands.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)da Máquina Virtual Azure .

Certifique-se de que tem [o mais recente Azure PowerShell](/powershell/azure/overview) instalado e iniciado sessão na sua subscrição:

```powershell
Connect-AzAccount
```

Nos seguintes exemplos, substitua os nomes dos parâmetros pelos seus próprios valores. 

## <a name="determine-boot-issues"></a>Determinar problemas de arranque
Pode ver uma imagem do seu VM em Azure para ajudar a resolver problemas com a bota. Esta imagem pode ajudar a identificar por que um VM falha no arranque. O exemplo seguinte obtém a imagem `myVM` do Windows `myResourceGroup`VM nomeado no grupo de recursos chamado:

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Reveja a imagem para determinar por que razão o VM está a falhar no arranque. Note quaisquer mensagens de erro específicas ou códigos de erro fornecidos.

## <a name="stop-the-vm"></a>Parar a VM

O exemplo que se segue `myVM` para o `myResourceGroup`VM nomeado pelo grupo de recursos denominado:

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Espere até que o VM termine de apagar antes de passar para o próximo passo.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Crie um instantâneo do Disco OS do VM

O exemplo seguinte cria `mySnapshot` um instantâneo com o nome do disco OS do VM chamado 'myVM'. 

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

Um instantâneo é uma cópia completa e só de leitura de um VHD. Não pode ser ligado a um VM. No próximo passo, criaremos um disco a partir deste instantâneo.

## <a name="create-a-disk-from-the-snapshot"></a>Criar um disco a partir do instantâneo

Este script cria um disco `newOSDisk` gerido com `mysnapshot`o nome do instantâneo nomeado .  

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
Agora tem uma cópia do disco original do OS. Pode montar este disco noutro VM do Windows para fins de resolução de problemas.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>Fixe o disco a outro VM do Windows para resolução de problemas

Agora anexamos a cópia do disco osso original a um VM como um disco de dados. Este processo permite-lhe corrigir erros de configuração ou rever ficheiros de registo de aplicações ou sistemas adicionais no disco. O exemplo seguinte prende `newOSDisk` o disco nomeado `RecoveryVM`ao VM nomeado .

> [!NOTE]
> Para fixar o disco, a cópia do disco OS original e o VM de recuperação devem estar no mesmo local.

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

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Ligue-se ao VM de recuperação e corrija problemas no disco anexo

1. RDP para a sua VM de recuperação usando as credenciais apropriadas. O exemplo seguinte descarrega o ficheiro de `RecoveryVM` ligação RDP `myResourceGroup`para o VM nomeado no grupo de recursos chamado , e descarrega-o para `C:\Users\ops\Documents`"

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. O disco de dados deve ser automaticamente detetado e fixado. Consulte a lista de volumes anexados para determinar a letra de unidade da seguinte forma:

    ```powershell
    Get-Disk
    ```

    A saída de exemplo seguinte mostra que o disco ligou um disco **2**. (Também pode `Get-Volume` usar para ver a letra de unidade):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Após a montagem da cópia do disco osso original, pode executar quaisquer passos de manutenção e resolução de problemas conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.

## <a name="unmount-and-detach-original-os-disk"></a>Desmontar e desprender o disco original do OS
Uma vez resolvidos os seus erros, desmonte e desmonta o disco existente do vM de recuperação. Não pode utilizar o disco com qualquer outro VM até que seja liberado o disco de aluguer do disco à VM de recuperação.

1. A partir da sua sessão de RDP, desmonte o disco de dados no seu VM de recuperação. Precisa do número do `Get-Disk` disco do cmdlet anterior. Em seguida, utilize `Set-Disk` para definir o disco como offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Confirme que o disco `Get-Disk` está agora definido como offline usando novamente. A saída de exemplo a seguir mostra que o disco está agora definido como offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Saia da sua sessão de RDP. A partir da sua sessão Azure PowerShell, retire o disco nomeado `newOSDisk` do VM denominado 'RecoveryVM'.

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Alterar o disco OS para o VM afetado

Pode utilizar o Azure PowerShell para trocar os discos OS. Não é preciso apagar e recriar o VM.

Este exemplo para o `myVM` VM nomeado e `newOSDisk` atribui o disco nomeado como o novo disco OS. 

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

## <a name="verify-and-enable-boot-diagnostics"></a>Verificar e ativar diagnósticos de arranque

O exemplo seguinte permite a extensão `myVMDeployed` de diagnóstico no `myResourceGroup`VM nomeado no grupo de recursos denominado:

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Passos seguintes
Se tiver problemas de ligação ao seu VM, consulte [ligações RDP de Troubleshoot a um VM Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para problemas com o acesso a aplicações em execução no seu VM, consulte problemas de conectividade da [aplicação Troubleshoot num VM windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para mais informações sobre a utilização do Gestor de Recursos, consulte a [visão geral do Gestor de Recursos do Azure](../../azure-resource-manager/management/overview.md).
