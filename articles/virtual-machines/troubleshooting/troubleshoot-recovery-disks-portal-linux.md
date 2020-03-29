---
title: Use um VM de resolução de problemas linux no portal Azure [ Microsoft Docs
description: Aprenda a resolver problemas com a máquina virtual do Linux ligando o disco OS a um VM de recuperação utilizando o portal Azure
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: e45de5c12f0d93645a0b1253acf8300527cafdbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374646"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Troubleshoot a Linux VM ligando o disco OS a um VM de recuperação usando o portal Azure
Se a sua máquina virtual Linux (VM) encontrar um erro de arranque ou disco, poderá ter de executar passos de resolução de problemas no disco rígido virtual em si. Um exemplo comum seria uma `/etc/fstab` entrada inválida que impede que o VM seja capaz de arrancar com sucesso. Este artigo detalha como usar o portal Azure para ligar o seu disco rígido virtual a outro VM Linux para corrigir quaisquer erros e, em seguida, recriar o seu VM original.

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Pare o VM afetado.
1. Tire uma foto para o disco osso do VM.
1. Crie um disco rígido virtual a partir do instantâneo.
1. Fixe e monte o disco rígido virtual noutro VM do Windows para fins de resolução de problemas.
1. Ligue à VM da resolução de problemas. Editar ficheiros ou executar quaisquer ferramentas para corrigir problemas no disco rígido virtual original.
1. Desmonte e desanexe o disco rígido virtual da VM de resolução de problemas.
1. Troque o disco OS pelo VM.

> [!NOTE]
> Este artigo não se aplica ao VM com disco não gerido.

## <a name="determine-boot-issues"></a>Determinar problemas de arranque
Examine os diagnósticos de arranque e a imagem vM para determinar por que razão o seu VM não é capaz de arrancar corretamente. Um exemplo comum seria uma `/etc/fstab`entrada inválida ou um disco rígido virtual subjacente a ser apagado ou movido.

Selecione o seu VM no portal e, em seguida, desloque-se para a secção **Suporte + Resolução de Problemas.** Clique em **diagnósticos boot** para visualizar as mensagens de consola transmitidas a partir do seu VM. Reveja os registos da consola para ver se consegue determinar porque é que o VM está a encontrar um problema. O exemplo que se segue mostra um VM preso no modo de manutenção que requer interação manual:

![Visualização de registos de consola de diagnóstico de arranque vM](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

Também pode clicar em **Screenshot** através do registo de diagnósticos de boot para descarregar uma captura da imagem VM.

## <a name="take-a-snapshot-of-the-os-disk"></a>Tire uma foto do Disco OS
Um instantâneo é uma cópia completa e apenas de leitura de um disco rígido virtual (VHD). Recomendamos que desligue o VM de forma limpa antes de tirar uma foto, para limpar quaisquer processos que estejam em curso. Para tirar uma foto de um disco de OS, siga estes passos:

1. Vá ao [portal Azure.](https://portal.azure.com) Selecione **as máquinas Virtuais** a partir da barra lateral e, em seguida, selecione o VM que tem problemas.
1. No painel esquerdo, selecione **Discos**e, em seguida, selecione o nome do disco OS.
    ![Imagem sobre o nome do disco OS](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Na página **de visão geral** do disco OS e, em seguida, selecione Criar **instantâneo**.
1. Crie uma imagem no mesmo local que o disco DES.

## <a name="create-a-disk-from-the-snapshot"></a>Criar um disco a partir do instantâneo
Para criar um disco a partir do instantâneo, siga estes passos:

1. Selecione **Cloud Shell** do portal Azure.

    ![Imagem sobre a Concha da Nuvem Aberta](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Executar os seguintes comandos PowerShell para criar um disco gerido a partir do instantâneo. Deve substituir estes nomes de amostra suster os nomes apropriados.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Se os comandos funcionarem com sucesso, verá o novo disco no grupo de recursos que forneceu.

## <a name="attach-disk-to-another-vm"></a>Anexar disco a outro VM
Para os próximos passos, você usa outro VM para fins de resolução de problemas. Depois de ligar o disco ao VM de resolução de problemas, pode navegar e editar o conteúdo do disco. Este processo permite-lhe corrigir quaisquer erros de configuração ou rever ficheiros adicionais de registo de aplicações ou sistemas. Para fixar o disco a outro VM, siga estes passos:

1. Selecione o seu grupo de recursos a partir do portal e, em seguida, selecione o seu VM de resolução de problemas. Selecione **Discos,** **selecione Editar**e, em seguida, clique em **Adicionar disco de dados:**

    ![Anexar o disco existente no portal](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Na lista de **discos Data,** selecione o disco OS do VM que identificou. Se não vir o disco OS, certifique-se de que o VM e o disco OS estão na mesma região (localização). 
3. Selecione **Guardar** para aplicar as alterações.

## <a name="mount-the-attached-data-disk"></a>Monte o disco de dados anexado

> [!NOTE]
> Os seguintes exemplos detalham os passos necessários num Ubuntu VM. Se estiver a utilizar um distro Linux diferente, como o Red Hat Enterprise `mount` Linux ou o SUSE, os locais e comandos de ficheiros de registo podem ser um pouco diferentes. Consulte a documentação para o seu distro específico para as alterações adequadas nos comandos.

1. SSH para a sua vm de resolução de problemas usando as credenciais apropriadas. Se este disco for o primeiro disco de dados ligado ao `/dev/sdc`seu VM de resolução de problemas, é provável que esteja ligado a . Utilizar `dmseg` para listar discos anexados:

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


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Corrigir problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, pode agora realizar quaisquer passos de manutenção e resolução de problemas conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmontar e desmontar disco rígido virtual original
Assim que os seus erros forem resolvidos, retire o disco rígido virtual existente do seu VM de resolução de problemas. Não é possível utilizar o disco rígido virtual com qualquer outro VM até que seja lançado o disco rígido virtual ao VM de resolução de problemas.

1. Da sessão SSH ao seu VM de resolução de problemas, desmonte o disco rígido virtual existente. Mude-se do diretório-mãe primeiro para o seu ponto de montagem:

    ```bash
    cd /
    ```

    Agora desmonte o disco rígido virtual existente. O seguinte exemplo desmonta o dispositivo em: `/dev/sdc1`

    ```bash
    sudo umount /dev/sdc1
    ```

2. Agora retire o disco rígido virtual do VM. Selecione o seu VM no portal e clique em **Discos**. Selecione o disco rígido virtual existente e, em seguida, clique em **Desmontar:**

    ![Desmontar o disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Aguarde até que o VM tenha conseguido desprender o disco de dados com sucesso antes de continuar.

## <a name="swap-the-os-disk-for-the-vm"></a>Troque o disco OS pelo VM

O portal Azure suporta agora a alteração do disco OS do VM. Para tal, siga estes passos:

1. Vá ao [portal Azure.](https://portal.azure.com) Selecione **as máquinas Virtuais** a partir da barra lateral e, em seguida, selecione o VM que tem problemas.
1. No painel esquerdo, selecione **Discos,** e, em seguida, **selecione o disco Swap OS**.
        ![A imagem sobre o disco Swap OS no portal Azure](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Escolha o novo disco que reparou e, em seguida, digite o nome do VM para confirmar a alteração. Se não vir o disco na lista, aguarde 10 ~ 15 minutos depois de retirar o disco do VM de resolução de problemas. Certifique-se também de que o disco está no mesmo local que o VM.
1. Selecione OK.

## <a name="next-steps"></a>Passos seguintes
Se tiver problemas de ligação ao seu VM, consulte [as ligações SSH de Troubleshoot a um VM Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Para problemas com o acesso a aplicações em execução no seu VM, consulte problemas de conectividade de [aplicação Troubleshoot num VM Linux](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para mais informações sobre a utilização do Gestor de Recursos, consulte a [visão geral do Gestor de Recursos do Azure](../../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
