---
title: Usar uma VM de solução de problemas do Linux com o CLI do Azure | Microsoft Docs
description: Saiba como solucionar problemas de VM do Linux conectando o disco do sistema operacional a uma VM de recuperação usando o CLI do Azure
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 49ee83e451e9d555a7fe5fca57bc58d6616334da
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641058"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Solucionar problemas de uma VM Linux anexando o disco do sistema operacional a uma VM de recuperação com o CLI do Azure
Se a VM (máquina virtual) do Linux encontrar um erro de disco ou de inicialização, talvez seja necessário executar as etapas de solução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma entrada inválida no `/etc/fstab` que impede que a VM seja capaz de inicializar com êxito. Este artigo fornece detalhes sobre como usar o CLI do Azure para conectar seu disco rígido virtual a outra VM Linux para corrigir erros e, em seguida, recriar a VM original. 

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Pare a VM afetada.
1. Tire um instantâneo do disco do sistema operacional da VM.
1. Crie um disco do instantâneo do disco do sistema operacional.
1. Anexe e monte o novo disco do sistema operacional em outra VM do Linux para fins de solução de problemas.
1. Ligue à VM da resolução de problemas. Edite arquivos ou execute qualquer ferramenta para corrigir problemas no novo disco do sistema operacional.
1. Desmonte e desanexe o novo disco do sistema operacional da VM de solução de problemas.
1. Altere o disco do sistema operacional para a VM afetada.

Para executar essas etapas de solução de problemas, você precisará do [CLI do Azure](/cli/azure/install-az-cli2) mais recente instalado e conectado a uma conta do Azure usando [AZ login](/cli/azure/reference-index).

> [!Important]
> Os scripts neste artigo se aplicam somente às VMs que usam o [disco gerenciado](../linux/managed-disks-overview.md). 

Nos exemplos a seguir, substitua os nomes de parâmetro pelos seus próprios valores. Os nomes de parâmetro `myResourceGroup` de `myVM`exemplo incluem e.

## <a name="determine-boot-issues"></a>Determinar problemas de inicialização
Examine a saída serial para determinar por que sua VM não pode ser inicializada corretamente. Um exemplo comum é uma entrada inválida `/etc/fstab`no, ou o disco rígido virtual subjacente que está sendo excluído ou movido.

Obtenha os logs de inicialização com [AZ VM boot-Diagnostics Get-boot-log](/cli/azure/vm/boot-diagnostics). O exemplo a seguir obtém a saída serial da VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Examine a saída serial para determinar por que a VM está falhando na inicialização. Se a saída serial não estiver fornecendo nenhuma indicação, talvez seja necessário examinar os arquivos de `/var/log` log quando você tiver o disco rígido virtual conectado a uma VM de solução de problemas.

## <a name="stop-the-vm"></a>Parar a VM

O exemplo a seguir interrompe a VM `myVM` denominada do grupo de `myResourceGroup`recursos chamado:

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Aguarde até que a VM tenha concluído a exclusão antes de processar para a próxima etapa.

## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Criar um instantâneo do disco do sistema operacional da VM

Um instantâneo é uma cópia completa e somente leitura de um VHD. Ele não pode ser anexado a uma VM. Na próxima etapa, criaremos um disco a partir desse instantâneo. O exemplo a seguir cria um instantâneo com `mySnapshot` o nome do disco do sistema operacional da VM chamada ' myVM '. 

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
## <a name="create-a-disk-from-the-snapshot"></a>Criar um disco a partir do instantâneo

Esse script cria um disco gerenciado com o `newOSDisk` nome do instantâneo chamado `mysnapshot`.  

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
Agora você tem uma cópia do disco do sistema operacional original. Você pode montar esse novo disco em outra VM do Windows para fins de solução de problemas.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Anexar o novo disco rígido virtual a outra VM
Para as próximas etapas, você usa outra VM para fins de solução de problemas. Você anexa o disco a essa VM de solução de problemas para procurar e editar o conteúdo do disco. Esse processo permite que você corrija quaisquer erros de configuração ou examine arquivos adicionais de log do sistema ou do aplicativo, por exemplo. Escolha ou crie outra VM a ser usada para fins de solução de problemas.

Anexe o disco rígido virtual existente com [AZ VM Unmanaged-Disk Attach](/cli/azure/vm/unmanaged-disk). Ao anexar o disco rígido virtual existente, especifique o URI para o disco obtido no comando anterior `az vm show` . O exemplo a seguir anexa um disco rígido virtual existente à VM de solução de problemas `myVMRecovery` chamada no grupo de recursos `myResourceGroup`chamado:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```
## <a name="mount-the-attached-data-disk"></a>Montar o disco de dados anexado

> [!NOTE]
> Os exemplos a seguir detalham as etapas necessárias em uma VM do Ubuntu. Se você estiver usando um distribuição Linux diferente, como Red Hat Enterprise Linux ou Suse, os locais e `mount` os comandos do arquivo de log poderão ser um pouco diferentes. Consulte a documentação de seu distribuição específico para obter as alterações apropriadas nos comandos.

1. SSH para sua VM de solução de problemas usando as credenciais apropriadas. Se esse disco for o primeiro disco de dados anexado à sua VM de solução de problemas, provavelmente o disco `/dev/sdc`está conectado. Use `dmseg` para exibir os discos anexados:

    ```bash
    dmesg | grep SCSI
    ```

    O resultado é semelhante ao seguinte exemplo:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    No exemplo anterior, o disco do sistema operacional está `/dev/sda` em e o disco temporário fornecido para cada VM está `/dev/sdb`em. Se você tivesse vários discos de dados, eles devem estar `/dev/sdd`em `/dev/sde`, e assim por diante.

2. Crie um diretório para montar o disco rígido virtual existente. O exemplo a seguir cria um diretório `troubleshootingdisk`chamado:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Se você tiver várias partições em seu disco rígido virtual existente, monte a partição necessária. O exemplo a seguir monta a primeira partição primária em `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > A prática recomendada é montar discos de dados em VMs no Azure usando o UUID (identificador universal exclusivo) do disco rígido virtual. Para este pequeno cenário de solução de problemas, não é necessário montar o disco rígido virtual usando o UUID. No entanto, em uso normal `/etc/fstab` , a edição para montar discos rígidos virtuais usando o nome do dispositivo em vez de UUID pode fazer com que a VM falhe na inicialização.


## <a name="fix-issues-on-the-new-os-disk"></a>Corrigir problemas no novo disco do sistema operacional
Com o disco rígido virtual existente montado, agora você pode executar todas as etapas de manutenção e solução de problemas, conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.


## <a name="unmount-and-detach-the-new-os-disk"></a>Desmontar e desanexar o novo disco do sistema operacional
Depois que os erros forem resolvidos, desmonte e desanexe o disco rígido virtual existente da VM de solução de problemas. Você não pode usar seu disco rígido virtual com qualquer outra VM até que a concessão que está anexando o disco rígido virtual à VM de solução de problemas seja liberada.

1. Na sessão do SSH para sua VM de solução de problemas, desmonte o disco rígido virtual existente. Altere o diretório pai para o ponto de montagem primeiro:

    ```bash
    cd /
    ```

    Agora desmonte o disco rígido virtual existente. O exemplo a seguir desmonta o dispositivo em `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Agora desanexe o disco rígido virtual da VM. Saia da sessão SSH para a VM de solução de problemas. Liste os discos de dados anexados à sua VM de solução de problemas com [AZ VM Unmanaged-Disk List](/cli/azure/vm/unmanaged-disk). O exemplo a seguir lista os discos de dados anexados à `myVMRecovery` VM chamada no grupo de `myResourceGroup`recursos denominado:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Anote o nome do disco rígido virtual existente. Por exemplo, o nome de um disco com o URI de **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** é **myVHD**. 

    Desanexe o disco de dados de sua VM [AZ VM Unmanaged-Disk Detach](/cli/azure/vm/unmanaged-disk). O exemplo a seguir desanexa o disco nomeado `myVHD` da VM chamada `myVMRecovery` no `myResourceGroup` grupo de recursos:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Alterar o disco do sistema operacional para a VM afetada

Você pode usar Azure PowerShell para trocar os discos do sistema operacional. Você não precisa excluir e recriar a VM.

Este exemplo interrompe a VM denominada `myVM` e atribui o disco chamado `newOSDisk` como o novo disco do sistema operacional. 

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

## <a name="next-steps"></a>Passos Seguintes
Se você estiver tendo problemas para se conectar à sua VM, consulte [solucionar problemas de conexões SSH para uma VM do Azure](troubleshoot-ssh-connection.md). Para problemas com o acesso a aplicativos em execução na sua VM, consulte [solucionar problemas de conectividade do aplicativo em uma VM do Linux](troubleshoot-app-connection.md).

