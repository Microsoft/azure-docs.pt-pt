---
title: Use um VM de resolução de problemas linux com o Azure CLI [ Microsoft Docs
description: Aprenda a resolver problemas com a VM do Linux ligando o disco OS a um VM de recuperação utilizando o Azure CLI
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73620566"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Troubleshoot a Linux VM ligando o disco OS a um VM de recuperação com o ClI Azure
Se a sua máquina virtual Linux (VM) encontrar um erro de arranque ou disco, poderá ter de executar passos de resolução de problemas no disco rígido virtual em si. Um exemplo comum seria uma `/etc/fstab` entrada inválida que impede que o VM seja capaz de arrancar com sucesso. Este artigo detalha como usar o Azure CLI para ligar o seu disco rígido virtual a outro VM Linux para corrigir quaisquer erros e, em seguida, recriar o seu VM original. 

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Pare o VM afetado.
1. Tire uma foto do disco operativo do VM.
1. Crie um disco a partir do instantâneo do disco OS.
1. Fixe e monte o novo disco OS a outro VM Linux para fins de resolução de problemas.
1. Ligue à VM da resolução de problemas. Editar ficheiros ou executar quaisquer ferramentas para corrigir problemas no novo disco DES.
1. Desmonte e retire o novo disco de SO do VM de resolução de problemas.
1. Altere o disco OS para o VM afetado.

Para realizar estes passos de resolução de problemas, precisa do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e registado numa conta Azure utilizando [login az](/cli/azure/reference-index).

> [!Important]
> Os scripts deste artigo aplicam-se apenas aos VMs que utilizam [o Disco Gerido](../linux/managed-disks-overview.md). 

Nos seguintes exemplos, substitua os nomes `myResourceGroup` dos `myVM`parâmetros pelos seus próprios valores, tais como e .

## <a name="determine-boot-issues"></a>Determinar problemas de arranque
Examine a saída em série para determinar por que razão o seu VM não é capaz de arrancar corretamente. Um exemplo comum é `/etc/fstab`uma entrada inválida ou o disco rígido virtual subjacente a ser apagado ou movido.

Obtenha os registos de boot com [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics). O exemplo seguinte obtém a saída `myVM` em série `myResourceGroup`do VM nomeado no grupo de recursos chamado:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Reveja a saída em série para determinar por que razão o VM está a falhar no arranque. Se a saída em série não fornecer qualquer indicação, `/var/log` poderá ter de rever ficheiros de registo assim que tiver o disco rígido virtual ligado a um VM de resolução de problemas.

## <a name="stop-the-vm"></a>Parar a VM

O exemplo que se segue `myVM` para o `myResourceGroup`VM nomeado pelo grupo de recursos denominado:

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>Tire uma foto do Disco OS do VM afetado

Um instantâneo é uma cópia completa e só de leitura de um VHD. Não pode ser ligado a um VM. No próximo passo, criaremos um disco a partir deste instantâneo. O exemplo seguinte cria `mySnapshot` um instantâneo com o nome do disco OS do VM chamado 'myVM'. 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>Criar um disco a partir do instantâneo

Este script cria um disco `myOSDisk` gerido com `mySnapshot`o nome do instantâneo nomeado .  

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

Se o grupo de recursos e o instantâneo de origem não estiverem na mesma `az disk create`região, receberá o erro "Recurso não é encontrado" quando executar . Neste caso, deve `--location <region>` especificar para criar o disco na mesma região que o instantâneo de origem.

Agora tem uma cópia do disco original do OS. Pode montar este novo disco noutro VM do Windows para fins de resolução de problemas.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Fixe o novo disco rígido virtual a outro VM
Para os próximos passos, você usa outro VM para fins de resolução de problemas. Liga o disco a este VM de resolução de problemas para navegar e editar o conteúdo do disco. Este processo permite-lhe corrigir quaisquer erros de configuração ou rever ficheiros adicionais de registo de aplicações ou sistemas.

Este script fixa `myNewOSDisk` o disco `MyTroubleshootVM`ao VM:

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $diskId --resource-group MyResourceGroup --size-gb 128 --sku Standard_LRS --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>Monte o disco de dados anexado

> [!NOTE]
> Os seguintes exemplos detalham os passos necessários num Ubuntu VM. Se estiver a utilizar um distro Linux diferente, como o Red Hat Enterprise `mount` Linux ou o SUSE, os locais e comandos de ficheiros de registo podem ser um pouco diferentes. Consulte a documentação para o seu distro específico para as alterações adequadas nos comandos.

1. SSH para a sua vm de resolução de problemas usando as credenciais apropriadas. Se este disco for o primeiro disco de dados ligado ao `/dev/sdc`seu VM de resolução de problemas, o disco está provavelmente ligado a . Utilizar `dmesg` para visualizar discos anexados:

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

    No exemplo anterior, o disco `/dev/sda` OS está no e o disco `/dev/sdb`temporário fornecido para cada VM está em . Se tivesse vários discos de dados, `/dev/sde`eles deviam estar em, `/dev/sdd`e assim por diante.

2. Crie um diretório para montar o disco rígido virtual existente. O exemplo seguinte cria `troubleshootingdisk`um diretório chamado:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Se tiver várias divisórias no disco rígido virtual existente, monte a divisória necessária. O exemplo seguinte monta a `/dev/sdc1`primeira partição primária em:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > A melhor prática é montar discos de dados em VMs em Azure utilizando o identificador universalmente único (UUID) do disco rígido virtual. Para este pequeno cenário de resolução de problemas, não é necessário montar o disco rígido virtual utilizando o UUID. No entanto, em `/etc/fstab` uso normal, a edição para montar discos rígidos virtuais utilizando o nome do dispositivo em vez de UUID pode fazer com que o VM falhe no arranque.


## <a name="fix-issues-on-the-new-os-disk"></a>Corrigir problemas no novo disco de OS
Com o disco rígido virtual existente montado, pode agora realizar quaisquer passos de manutenção e resolução de problemas conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.


## <a name="unmount-and-detach-the-new-os-disk"></a>Desmontar e separar o novo disco de OS
Uma vez resolvidos os seus erros, desmonte e desmonta o disco rígido virtual existente do seu VM de resolução de problemas. Não é possível utilizar o disco rígido virtual com qualquer outro VM até que seja lançado o disco rígido virtual ao VM de resolução de problemas.

1. Da sessão SSH ao seu VM de resolução de problemas, desmonte o disco rígido virtual existente. Mude-se do diretório-mãe primeiro para o seu ponto de montagem:

    ```bash
    cd /
    ```

    Agora desmonte o disco rígido virtual existente. O seguinte exemplo desmonta o dispositivo em: `/dev/sdc1`

    ```bash
    sudo umount /dev/sdc1
    ```

2. Agora retire o disco rígido virtual do VM. Saia da sessão SSH para o seu VM de resolução de problemas:

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Alterar o disco OS para o VM afetado

Pode utilizar o Azure CLI para trocar os discos OS. Não é preciso apagar e recriar o VM.

Este exemplo para o `myVM` VM nomeado e `myNewOSDisk` atribui o disco nomeado como o novo disco OS.

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
Se tiver problemas de ligação ao seu VM, consulte [as ligações SSH de Troubleshoot a um VM Azure](troubleshoot-ssh-connection.md). Para problemas com o acesso a aplicações em execução no seu VM, consulte problemas de conectividade de [aplicação Troubleshoot num VM Linux](troubleshoot-app-connection.md).

