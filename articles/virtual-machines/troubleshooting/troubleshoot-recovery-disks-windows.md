---
title: Use um VM de resolução de problemas do Windows com Azure PowerShell Microsoft Docs
description: Saiba como resolver problemas com o Windows VM em Azure ligando o disco OS a um VM de recuperação utilizando o Azure PowerShell
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
ms.openlocfilehash: fbdccc2efb72626d1974f4cd81be9d2ef4c522d4
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201591"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Resolução de problemas de um VM do Windows, fixando o disco OS a um VM de recuperação utilizando a Azure PowerShell
Se a sua máquina virtual Do Windows (VM) em Azure encontrar um erro de arranque ou disco, poderá ter de executar etapas de resolução de problemas no próprio disco. Um exemplo comum seria uma atualização de aplicação falhada que impede o VM de ser capaz de arrancar com sucesso. Este artigo detalha como utilizar o Azure PowerShell para ligar o disco a outro VM do Windows para corrigir eventuais erros e, em seguida, reparar o seu VM original. 

> [!Important]
> Os scripts deste artigo aplicam-se apenas aos VMs que utilizam [o Disco Gerido.](../managed-disks-overview.md) 

 

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
Agora podemos usar a Azure PowerShell para alterar o disco DE para um VM. Já não precisamos de apagar e recriar o VM.

O processo de resolução de problemas é o seguinte:

1. Pare o VM afetado.
2. Crie uma imagem instantânea a partir do disco oss do VM.
3. Crie um disco a partir do instantâneo do disco OS.
4. Anexar o disco como disco de dados a um VM de recuperação.
5. Ligue-se ao VM de recuperação. Editar ficheiros ou executar quaisquer ferramentas para corrigir problemas no disco de SISTEMA copiado.
6. Desmonte e retire o disco da recuperação VM.
7. Altere o disco DE para o VM afetado.

Pode utilizar os comandos de reparação VM para automatizar os passos 1, 2, 3, 4, 6 e 7. Para obter mais documentação e instruções, consulte [reparar um VM do Windows utilizando os comandos de reparação da Máquina Virtual Azure](repair-windows-vm-using-azure-virtual-machine-repair-commands.md).

Certifique-se de que tem [o mais recente Azure PowerShell](/powershell/azure/) instalado e iniciado na sua subscrição:

```powershell
Connect-AzAccount
```

Nos exemplos seguintes, substitua os nomes dos parâmetros pelos seus próprios valores. 

## <a name="determine-boot-issues"></a>Determinar problemas de arranque
Pode ver uma imagem do seu VM em Azure para ajudar a resolver problemas com a bota. Esta imagem pode ajudar a identificar por que um VM não é iniciado. O exemplo a seguir obtém a imagem do Windows VM nomeado `myVM` no grupo de recursos `myResourceGroup` denominado:

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Reveja a imagem para determinar por que o VM está falhando no arranque. Note quaisquer mensagens de erro específicas ou códigos de erro fornecidos.

## <a name="stop-the-vm"></a>Parar a VM

O exemplo a seguir impede o VM nomeado `myVM` do grupo de recursos `myResourceGroup` denominado:

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Aguarde até que o VM termine de apagar antes de passar ao passo seguinte.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Criar uma imagem instantânea a partir do disco oss do VM

O exemplo a seguir cria um instantâneo com o nome `mySnapshot` do disco de SO do VM denominado 'myVM'. 

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

Uma foto é uma cópia completa e só de leitura de um VHD. Não pode ser ligado a um VM. No próximo passo, criaremos um disco a partir deste instantâneo.

## <a name="create-a-disk-from-the-snapshot"></a>Criar um disco a partir do instantâneo

Este script cria um disco gerido com o nome `newOSDisk` a partir do instantâneo denominado `mysnapshot` .  

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
Agora tem uma cópia do disco original do SISTEMA. Pode montar este disco noutro VM do Windows para efeitos de resolução de problemas.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>Fixe o disco a outro VM do Windows para resolução de problemas

Agora anexamos a cópia do disco de oss original a um VM como um disco de dados. Este processo permite-lhe corrigir erros de configuração ou rever ficheiros adicionais de aplicação ou registo do sistema no disco. O exemplo a seguir anexa o disco nomeado `newOSDisk` ao VM denominado `RecoveryVM` .

> [!NOTE]
> Para fixar o disco, a cópia do disco original de SO e o VM de recuperação devem estar no mesmo local.

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

1. RDP para a sua recuperação VM usando as credenciais apropriadas. O exemplo seguinte descarrega o ficheiro de conexão RDP para o VM nomeado `RecoveryVM` no grupo de recursos denominado , e `myResourceGroup` transfere-o para `C:\Users\ops\Documents` "

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. O disco de dados deve ser automaticamente detetado e anexado. Consulte a lista de volumes anexos para determinar a letra de unidade da seguinte forma:

    ```powershell
    Get-Disk
    ```

    A saída de exemplo a seguir mostra que o disco ligou um disco **2**. (Também pode usar `Get-Volume` para ver a letra de unidade):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Depois de montada a cópia do disco sol original, pode efetuar quaisquer passos de manutenção e resolução de problemas conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.

## <a name="unmount-and-detach-original-os-disk"></a>Desmonte e desprete o disco de OS original
Uma vez resolvidos os seus erros, desmonta e desmonta o disco existente do seu VM de recuperação. Não é possível utilizar o seu disco com qualquer outro VM até que o contrato de arrendamento que liga o disco ao VM de recuperação seja lançado.

1. A partir da sua sessão de PDR, desmonte o disco de dados no seu VM de recuperação. Precisa do número do disco do `Get-Disk` cmdlet anterior. Em seguida, use `Set-Disk` para definir o disco como offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Confirme que o disco está agora definido como offline `Get-Disk` novamente. A saída de exemplo a seguir mostra que o disco está agora definido como offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Saia da sessão do PDR. Da sua sessão Azure PowerShell, remova o disco nomeado `newOSDisk` do VM denominado 'RecoveryVM'.

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Altere o disco DE para o VM afetado

Pode utilizar o Azure PowerShell para trocar os discos OS. Não é preciso apagar e recriar o VM.

Este exemplo para o nome VM `myVM` e atribui o disco nomeado como o novo disco `newOSDisk` OS. 

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

## <a name="verify-and-enable-boot-diagnostics"></a>Verifique e ative os diagnósticos de arranque

O exemplo a seguir permite a extensão de diagnóstico do VM nomeado `myVMDeployed` no grupo de recursos `myResourceGroup` denominado:

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Passos seguintes
Se tiver problemas de ligação ao seu VM, consulte [as ligações RDP de resolução de problemas a um Azure VM](troubleshoot-rdp-connection.md). Para problemas com o acesso a aplicações em execução no seu VM, consulte [problemas de conectividade da aplicação Troubleshoot num VM do Windows](troubleshoot-app-connection.md).

Para obter mais informações sobre a utilização do Gestor de Recursos, consulte [a visão geral do Gestor de Recursos do Azure](../../azure-resource-manager/management/overview.md).
