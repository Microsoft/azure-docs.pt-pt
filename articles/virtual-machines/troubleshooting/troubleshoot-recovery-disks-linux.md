---
title: Use um VM de resolução de problemas linux com o Azure CLI | Microsoft Docs
description: Saiba como resolver problemas com os problemas do Linux VM ligando o disco DE A uma VM de recuperação utilizando o CLI Azure
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
ms.openlocfilehash: 70855f7956e42d815e0b8135ccfe6e374853d8aa
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197528"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Resolução de problemas de um Linux VM, fixando o disco DE A uma VM de recuperação com o CLI Azure
Se a sua máquina virtual Linux (VM) encontrar um erro de arranque ou disco, poderá ter de executar etapas de resolução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma entrada inválida `/etc/fstab` que impede o VM de ser capaz de arrancar com sucesso. Este artigo detalha como utilizar o CLI Azure para ligar o seu disco rígido virtual a outro Linux VM para corrigir eventuais erros e, em seguida, recriar o seu VM original. 

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Pare o VM afetado.
1. Tire uma foto do disco de so do VM.
1. Crie um disco a partir do instantâneo do disco OS.
1. Fixe e monte o novo disco DE AA a outro Linux VM para efeitos de resolução de problemas.
1. Ligue à VM da resolução de problemas. Editar ficheiros ou executar quaisquer ferramentas para corrigir problemas no novo disco DE.
1. Desmonte e retire o novo disco de OS do VM de resolução de problemas.
1. Altere o disco DE para o VM afetado.

Para executar estes passos de resolução de problemas, precisa do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e iniciado numa conta Azure utilizando [o login az](/cli/azure/reference-index).

Pode utilizar os comandos de reparação VM para automatizar os passos 1, 2, 3, 4, 6 e 7. Para obter mais documentação e instruções, consulte [reparar um VM Linux utilizando os comandos de reparação da Máquina Virtual Azure](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

> [!Important]
> Os scripts deste artigo aplicam-se apenas aos VMs que utilizam [o Disco Gerido.](../managed-disks-overview.md) 

Nos exemplos seguintes, substitua os nomes dos parâmetros pelos seus próprios valores, tais como `myResourceGroup` `myVM` .

## <a name="determine-boot-issues"></a>Determinar problemas de arranque
Examine a saída em série para determinar por que razão o seu VM não é capaz de arrancar corretamente. Um exemplo comum é uma entrada inválida em `/etc/fstab` , ou o disco rígido virtual subjacente a ser apagado ou movido.

Obtenha os registos de arranque com [az vm boot-diagnósticos get-boot-log](/cli/azure/vm/boot-diagnostics). O exemplo a seguir obtém a saída em série do VM nomeado `myVM` no grupo de recursos `myResourceGroup` denominado:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Reveja a saída em série para determinar por que o VM não está a arrancar. Se a saída em série não estiver a fornecer qualquer indicação, poderá ter de rever os ficheiros de registo `/var/log` assim que tiver o disco rígido virtual ligado a um VM de resolução de problemas.

## <a name="stop-the-vm"></a>Parar a VM

O exemplo a seguir impede o VM nomeado `myVM` do grupo de recursos `myResourceGroup` denominado:

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>Tire uma foto do disco de oss do VM afetado

Uma foto é uma cópia completa e só de leitura de um VHD. Não pode ser ligado a um VM. No próximo passo, criaremos um disco a partir deste instantâneo. O exemplo a seguir cria um instantâneo com o nome `mySnapshot` do disco de SO do VM denominado 'myVM'. 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>Criar um disco a partir do instantâneo

Este script cria um disco gerido com o nome `myOSDisk` a partir do instantâneo denominado `mySnapshot` .  

```azurecli
#Provide the name of your resource group
$resourceGroup="myResourceGroup"

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot="mySnapshot"

#Provide the name of the Managed Disk
$osDisk="myNewOSDisk"

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType="Premium_LRS"

#Provide the OS type
$osType="linux"

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query id -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

Se o grupo de recursos e o instantâneo de origem não estiverem na mesma região, receberá o erro de "Recurso não é encontrado" quando executar `az disk create` . Neste caso, deve especificar `--location <region>` para criar o disco na mesma região que o instantâneo de origem.

Agora tem uma cópia do disco original do SISTEMA. Pode montar este novo disco noutro Windows VM para efeitos de resolução de problemas.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Ligue o novo disco rígido virtual a outro VM
Para os próximos passos, utilize outro VM para efeitos de resolução de problemas. Ligue o disco a este VM de resolução de problemas para navegar e editar o conteúdo do disco. Este processo permite-lhe corrigir quaisquer erros de configuração ou rever ficheiros adicionais de aplicação ou registo de sistema.

Este script anexa o disco `myNewOSDisk` ao `MyTroubleshootVM` VM:

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az disk show -g $resourceGroup -n $osDisk --query id -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $myNewOSDiskid --resource-group $resourceGroup --size-gb $diskSize --sku $storageType --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>Monte o disco de dados anexado

> [!NOTE]
> Os exemplos a seguir detalham os passos necessários num Ubuntu VM. Se estiver a utilizar um distro Linux diferente, como o Red Hat Enterprise Linux ou o SUSE, as localizações e comandos do ficheiro de registo `mount` podem ser um pouco diferentes. Consulte a documentação do seu distro específico para as alterações adequadas nos comandos.

1. SSH para a sua resolução de problemas VM usando as credenciais apropriadas. Se este disco for o primeiro disco de dados ligado ao seu VM de resolução de problemas, o disco está provavelmente ligado a `/dev/sdc` . Utilizar `dmesg` para visualizar discos anexos:

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

    No exemplo anterior, o disco de so está em `/dev/sda` e o disco temporário fornecido para cada VM está em `/dev/sdb` . Se tinha vários discos de dados, eles deviam estar em `/dev/sdd` , e assim por `/dev/sde` diante.

2. Crie um diretório para montar o seu disco rígido virtual existente. O exemplo a seguir cria um diretório chamado `troubleshootingdisk` :

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Se tiver várias divisórias no disco rígido virtual existente, monte a partição necessária. O exemplo a seguir monta a primeira divisória primária `/dev/sdc1` em:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > A melhor prática é montar discos de dados em VMs em Azure usando o identificador universalmente único (UUID) do disco rígido virtual. Para este pequeno cenário de resolução de problemas, não é necessário montar o disco rígido virtual utilizando o UUID. No entanto, em uso normal, a edição `/etc/fstab` para montar discos rígidos virtuais usando o nome do dispositivo em vez de UUID pode fazer com que o VM não arranque.


## <a name="fix-issues-on-the-new-os-disk"></a>Corrija problemas no novo disco de so
Com o disco rígido virtual existente montado, pode agora executar quaisquer passos de manutenção e resolução de problemas conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.


## <a name="unmount-and-detach-the-new-os-disk"></a>Desmonte e desprete o novo disco de OS
Uma vez resolvidos os seus erros, desmonta e desmonta o disco rígido virtual existente do seu VM de resolução de problemas. Não é possível utilizar o seu disco rígido virtual com qualquer outro VM até que o contrato de arrendamento que liga o disco rígido virtual ao VM de resolução de problemas seja lançado.

1. Desde a sessão SSH até ao seu VM de resolução de problemas, desmonte o disco rígido virtual existente. Mude primeiro o diretório dos pais para o seu ponto de montagem:

    ```bash
    cd /
    ```

    Agora desmonte o disco rígido virtual existente. O exemplo a seguir desmonta o dispositivo `/dev/sdc1` em:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Agora desprenda o disco rígido virtual do VM. Saia da sessão SSH para o seu VM de resolução de problemas:

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Altere o disco DE para o VM afetado

Pode utilizar o Azure CLI para trocar os discos OS. Não é preciso apagar e recriar o VM.

Este exemplo para o nome VM `myVM` e atribui o disco nomeado como o novo disco `myNewOSDisk` OS.

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g $resourceGroup -n $osDisk --query id -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes
Se tiver problemas de ligação ao seu VM, consulte [as ligações SSH de resolução de problemas a um VM Azure](troubleshoot-ssh-connection.md). Para problemas com o acesso a aplicações em execução no seu VM, consulte [problemas de conectividade da aplicação Troubleshoot num Linux VM](troubleshoot-app-connection.md).

