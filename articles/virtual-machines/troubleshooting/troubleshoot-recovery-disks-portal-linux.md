---
title: Use um VM de resolução de problemas do Linux no portal Azure Microsoft Docs
description: Saiba como resolver problemas com a máquina virtual Linux ligando o disco OS a um VM de recuperação utilizando o portal Azure
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
ms.openlocfilehash: affdee6871649102ef7881fb0f540eba6ab450ca
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200962"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Resolução de problemas de um Linux VM, fixando o disco DE Aa a um VM de recuperação utilizando o portal Azure
Se a sua máquina virtual Linux (VM) encontrar um erro de arranque ou disco, poderá ter de executar etapas de resolução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma entrada inválida `/etc/fstab` que impede o VM de ser capaz de arrancar com sucesso. Este artigo detalha como utilizar o portal Azure para ligar o seu disco rígido virtual a outro Linux VM para corrigir eventuais erros e, em seguida, recriar o seu VM original.

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Pare o VM afetado.
1. Tire uma foto para o disco de so do VM.
1. Crie um disco rígido virtual a partir do instantâneo.
1. Fixe e monte o disco rígido virtual a outro Linux VM para efeitos de resolução de problemas.
1. Ligue à VM da resolução de problemas. Editar ficheiros ou executar quaisquer ferramentas para corrigir problemas no disco rígido virtual original.
1. Desmonte e desanexe o disco rígido virtual da VM de resolução de problemas.
1. Troque o disco DE pelo VM.

> [!NOTE]
> Este artigo não se aplica ao VM com disco não gerido.

## <a name="determine-boot-issues"></a>Determinar problemas de arranque
Examine os diagnósticos de arranque e a imagem em VM para determinar por que razão o seu VM não é capaz de arrancar corretamente. Um exemplo comum seria uma entrada inválida em `/etc/fstab` , ou um disco rígido virtual subjacente a ser apagado ou movido.

Selecione o seu VM no portal e, em seguida, desloque-se para baixo para a secção **De Resolução de Problemas De suporte +.** Clique em **diagnósticos boot** para ver as mensagens de consola transmitidas a partir do seu VM. Reveja os registos da consola para ver se consegue determinar por que razão o VM está a encontrar um problema. O exemplo a seguir mostra um VM preso no modo de manutenção que requer interação manual:

![Visualização de registos de consola de diagnóstico de botas VM](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

Também pode clicar em **Screenshot** através do topo do registo de diagnóstico de arranque para descarregar uma captura da imagem VM.

## <a name="take-a-snapshot-of-the-os-disk"></a>Tire uma foto do disco de oss
Um instantâneo é uma cópia completa e só de leitura de um disco rígido virtual (VHD). Recomendamos que desligue o VM de forma limpa antes de tirar uma foto, para limpar quaisquer processos em curso. Para tirar uma foto de um disco de so, siga estes passos:

1. Ir para o [portal Azure.](https://portal.azure.com) Selecione **máquinas virtuais** da barra lateral e, em seguida, selecione o VM que tem problemas.
1. No painel esquerdo, selecione **Discos** e, em seguida, selecione o nome do disco OS.
    ![Imagem sobre o nome do disco oss](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Na página **geral** do disco SO e, em seguida, selecione **Criar instantâneo**.
1. Crie uma imagem instantânea no mesmo local que o disco de so.

## <a name="create-a-disk-from-the-snapshot"></a>Criar um disco a partir do instantâneo
Para criar um disco a partir do instantâneo, siga estes passos:

1. Selecione **Cloud Shell** a partir do portal Azure.

    ![Imagem sobre Open Cloud Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Executar os seguintes comandos PowerShell para criar um disco gerido a partir do instantâneo. Deve substituir estes nomes de amostra pelos nomes apropriados.

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

## <a name="attach-disk-to-another-vm"></a>Fixe o disco a outro VM
Para os próximos passos, utilize outro VM para efeitos de resolução de problemas. Depois de fixar o disco ao VM de resolução de problemas, pode navegar e editar o conteúdo do disco. Este processo permite-lhe corrigir quaisquer erros de configuração ou rever ficheiros adicionais de aplicação ou registo de sistema. Para fixar o disco a outro VM, siga estes passos:

1. Selecione o seu grupo de recursos a partir do portal e, em seguida, selecione o seu VM de resolução de problemas. Selecione **Discos,** **selecione Editar** e, em seguida, clique em **Adicionar disco de dados**:

    ![Anexar o disco existente no portal](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Na lista **de discos de dados,** selecione o disco de so do VM que identificou. Se não vir o disco de so, certifique-se de que a resolução de problemas de VM e o disco de sos está na mesma região (localização). 
3. **Selecione Guardar** para aplicar as alterações.

## <a name="mount-the-attached-data-disk"></a>Monte o disco de dados anexado

> [!NOTE]
> Os exemplos a seguir detalham os passos necessários num Ubuntu VM. Se estiver a utilizar um distro Linux diferente, como o Red Hat Enterprise Linux ou o SUSE, as localizações e comandos do ficheiro de registo `mount` podem ser um pouco diferentes. Consulte a documentação do seu distro específico para as alterações adequadas nos comandos.

1. SSH para a sua resolução de problemas VM usando as credenciais apropriadas. Se este disco for o primeiro disco de dados ligado ao seu VM de resolução de problemas, é provável que esteja ligado a `/dev/sdc` . Utilizar `dmseg` para listar discos anexos:

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


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Corrija problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, pode agora executar quaisquer passos de manutenção e resolução de problemas conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmonte e desprete o disco rígido virtual original
Assim que os seus erros forem resolvidos, retire o disco rígido virtual existente do seu VM de resolução de problemas. Não é possível utilizar o seu disco rígido virtual com qualquer outro VM até que o contrato de arrendamento que liga o disco rígido virtual ao VM de resolução de problemas seja lançado.

1. Desde a sessão SSH até ao seu VM de resolução de problemas, desmonte o disco rígido virtual existente. Mude primeiro o diretório dos pais para o seu ponto de montagem:

    ```bash
    cd /
    ```

    Agora desmonte o disco rígido virtual existente. O exemplo a seguir desmonta o dispositivo `/dev/sdc1` em:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Agora desprenda o disco rígido virtual do VM. Selecione o seu VM no portal e clique em **Discos**. Selecione o seu disco rígido virtual existente e, em seguida, clique em **Descoser**:

    ![Desprender o disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Aguarde até que o VM tenha desligado com sucesso o disco de dados antes de continuar.

## <a name="swap-the-os-disk-for-the-vm"></a>Troque o disco DE pelo VM

O portal Azure suporta agora alterar o disco de SO do VM. Para tal, siga estes passos:

1. Ir para o [portal Azure.](https://portal.azure.com) Selecione **máquinas virtuais** da barra lateral e, em seguida, selecione o VM que tem problemas.
1. No painel esquerdo, selecione **Discos** e, em seguida, selecione **o disco Swap OS**.
        ![A imagem sobre o disco Swap OS no portal Azure](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Escolha o novo disco que reparou e, em seguida, digite o nome do VM para confirmar a alteração. Se não vir o disco na lista, aguarde 10 ~ 15 minutos depois de retirar o disco do VM de resolução de problemas. Certifique-se também de que o disco está no mesmo local que o VM.
1. Selecione OK.

## <a name="next-steps"></a>Passos seguintes
Se tiver problemas de ligação ao seu VM, consulte [as ligações SSH de resolução de problemas a um VM Azure](troubleshoot-ssh-connection.md). Para problemas com o acesso a aplicações em execução no seu VM, consulte [problemas de conectividade da aplicação Troubleshoot num Linux VM](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json).

Para obter mais informações sobre a utilização do Gestor de Recursos, consulte [a visão geral do Gestor de Recursos do Azure](../../azure-resource-manager/management/overview.md).
