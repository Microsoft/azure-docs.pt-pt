---
title: Usar uma VM de solução de problemas do Linux com o CLI do Azure | Microsoft Docs
description: Saiba como solucionar problemas de VM do Linux conectando o disco do sistema operacional a uma VM de recuperação usando o CLI do Azure
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 1b91a39e1297d8952da67a4f8d3b8568cefe04ce
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620566"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Solucionar problemas de uma VM Linux anexando o disco do sistema operacional a uma VM de recuperação com o CLI do Azure
Se a VM (máquina virtual) do Linux encontrar um erro de disco ou de inicialização, talvez seja necessário executar as etapas de solução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma entrada inválida em `/etc/fstab` que impede que a VM seja capaz de inicializar com êxito. Este artigo fornece detalhes sobre como usar o CLI do Azure para conectar seu disco rígido virtual a outra VM Linux para corrigir erros e, em seguida, recriar a VM original. 

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

Nos exemplos a seguir, substitua os nomes de parâmetro pelos seus próprios valores, como `myResourceGroup` e `myVM`.

## <a name="determine-boot-issues"></a>Determinar problemas de inicialização
Examine a saída serial para determinar por que sua VM não pode ser inicializada corretamente. Um exemplo comum é uma entrada inválida no `/etc/fstab`ou o disco rígido virtual subjacente que está sendo excluído ou movido.

Obtenha os logs de inicialização com [AZ VM boot-Diagnostics Get-boot-log](/cli/azure/vm/boot-diagnostics). O exemplo a seguir obtém a saída serial da VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Examine a saída serial para determinar por que a VM está falhando na inicialização. Se a saída serial não estiver fornecendo nenhuma indicação, talvez seja necessário examinar os arquivos de log em `/var/log` assim que você tiver o disco rígido virtual conectado a uma VM de solução de problemas.

## <a name="stop-the-vm"></a>Parar a VM

O exemplo a seguir interrompe a VM chamada `myVM` do grupo de recursos chamado `myResourceGroup`:

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>Tirar um instantâneo do disco do sistema operacional da VM afetada

Um instantâneo é uma cópia completa e somente leitura de um VHD. Ele não pode ser anexado a uma VM. Na próxima etapa, criaremos um disco a partir desse instantâneo. O exemplo a seguir cria um instantâneo com o nome `mySnapshot` do disco do sistema operacional da VM chamada ' myVM '. 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>Criar um disco a partir do instantâneo

Esse script cria um disco gerenciado com o nome `myOSDisk` do instantâneo chamado `mySnapshot`.  

```azurecli
#Provide the name of your resource group
$resourceGroup=myResourceGroup

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot=mySnapshot

#Provide the name of the Managed Disk
$osDisk=myNewOSDisk

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType=Premium_LRS

#Provide the OS type
$osType=linux

#Provide the name of the virtual machine
$virtualMachine=myVM

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query [id] -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

Se o grupo de recursos e o instantâneo de origem não estiverem na mesma região, você receberá o erro "recurso não encontrado" ao executar `az disk create`. Nesse caso, você deve especificar `--location <region>` para criar o disco na mesma região que o instantâneo de origem.

Agora você tem uma cópia do disco do sistema operacional original. Você pode montar esse novo disco em outra VM do Windows para fins de solução de problemas.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Anexar o novo disco rígido virtual a outra VM
Para as próximas etapas, você usa outra VM para fins de solução de problemas. Você anexa o disco a essa VM de solução de problemas para procurar e editar o conteúdo do disco. Esse processo permite que você corrija quaisquer erros de configuração ou examine arquivos adicionais de log do sistema ou do aplicativo.

Esse script anexa o disco `myNewOSDisk` ao `MyTroubleshootVM`da VM:

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $diskId --resource-group MyResourceGroup --size-gb 128 --sku Standard_LRS --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>Montar o disco de dados anexado

> [!NOTE]
> Os exemplos a seguir detalham as etapas necessárias em uma VM do Ubuntu. Se você estiver usando um distribuição Linux diferente, como Red Hat Enterprise Linux ou SUSE, os locais do arquivo de log e os comandos `mount` poderão ser um pouco diferentes. Consulte a documentação de seu distribuição específico para obter as alterações apropriadas nos comandos.

1. SSH para sua VM de solução de problemas usando as credenciais apropriadas. Se esse disco for o primeiro disco de dados anexado à sua VM de solução de problemas, provavelmente o disco está conectado a `/dev/sdc`. Use `dmesg` para exibir os discos anexados:

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

    No exemplo anterior, o disco do sistema operacional está em `/dev/sda` e o disco temporário fornecido para cada VM está em `/dev/sdb`. Se você tivesse vários discos de dados, eles devem estar em `/dev/sdd`, `/dev/sde`e assim por diante.

2. Crie um diretório para montar o disco rígido virtual existente. O exemplo a seguir cria um diretório chamado `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Se você tiver várias partições em seu disco rígido virtual existente, monte a partição necessária. O exemplo a seguir monta a primeira partição primária em `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > A prática recomendada é montar discos de dados em VMs no Azure usando o UUID (identificador universal exclusivo) do disco rígido virtual. Para este pequeno cenário de solução de problemas, não é necessário montar o disco rígido virtual usando o UUID. No entanto, em uso normal, editar `/etc/fstab` para montar discos rígidos virtuais usando o nome do dispositivo em vez de UUID pode fazer com que a VM falhe na inicialização.


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

2. Agora desanexe o disco rígido virtual da VM. Saia da sessão SSH para a VM de solução de problemas:

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Alterar o disco do sistema operacional para a VM afetada

Você pode usar CLI do Azure para trocar os discos do sistema operacional. Você não precisa excluir e recriar a VM.

Este exemplo interrompe a VM chamada `myVM` e atribui o disco chamado `myNewOSDisk` como o novo disco do sistema operacional.

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes
Se você estiver tendo problemas para se conectar à sua VM, consulte [solucionar problemas de conexões SSH para uma VM do Azure](troubleshoot-ssh-connection.md). Para problemas com o acesso a aplicativos em execução na sua VM, consulte [solucionar problemas de conectividade do aplicativo em uma VM do Linux](troubleshoot-app-connection.md).

