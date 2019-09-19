---
title: Usar uma VM de solução de problemas do Linux no portal do Azure | Microsoft Docs
description: Saiba como solucionar problemas de máquinas virtuais do Linux conectando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure
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
ms.openlocfilehash: 87b4d761eb7bd1c4a16998e44e8160cda24a05b4
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088252"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Solucionar problemas de uma VM Linux anexando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure
Se a VM (máquina virtual) do Linux encontrar um erro de disco ou de inicialização, talvez seja necessário executar as etapas de solução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma entrada inválida no `/etc/fstab` que impede que a VM seja capaz de inicializar com êxito. Este artigo fornece detalhes sobre como usar o portal do Azure para conectar seu disco rígido virtual a outra VM Linux para corrigir erros e, em seguida, recriar a VM original.

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Pare a VM afetada.
1. Tire um instantâneo do disco do sistema operacional da VM.
1. Crie um disco rígido virtual a partir do instantâneo.
1. Anexe e monte o disco rígido virtual em outra VM do Windows para fins de solução de problemas.
1. Ligue à VM da resolução de problemas. Edite arquivos ou execute qualquer ferramenta para corrigir problemas no disco rígido virtual original.
1. Desmonte e desanexe o disco rígido virtual da VM de resolução de problemas.
1. Troque o disco do sistema operacional da VM.

> [!NOTE]
> Este artigo não se aplica à VM com disco não gerenciado.

## <a name="determine-boot-issues"></a>Determinar problemas de inicialização
Examine o diagnóstico de inicialização e a captura de tela da VM para determinar por que sua VM não pode ser inicializada corretamente. Um exemplo comum seria uma entrada inválida no `/etc/fstab`ou um disco rígido virtual subjacente sendo excluído ou movido.

Selecione sua VM no portal e role para baixo até a seção **suporte + solução de problemas** . Clique em **diagnóstico de inicialização** para exibir as mensagens do console transmitidas de sua VM. Examine os logs do console para ver se é possível determinar por que a VM está encontrando um problema. O exemplo a seguir mostra uma VM paralisada no modo de manutenção que requer interação manual:

![Exibindo logs do console do diagnóstico de inicialização da VM](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

Você também pode clicar em **captura de tela** na parte superior do log de diagnóstico de inicialização para baixar uma captura da captura de tela da VM.

## <a name="take-a-snapshot-of-the-os-disk"></a>Tirar um instantâneo do disco do sistema operacional
Um instantâneo é uma cópia completa e somente leitura de um disco rígido virtual (VHD). Recomendamos que você desligue corretamente a VM antes de tirar um instantâneo, para limpar todos os processos em andamento. Para tirar um instantâneo de um disco do sistema operacional, siga estas etapas:

1. Vá para [portal do Azure](https://portal.azure.com). Selecione **máquinas virtuais** na barra lateral e, em seguida, selecione a VM que tem o problema.
1. No painel esquerdo, selecione **discos**e, em seguida, selecione o nome do disco do sistema operacional.
    ![Imagem sobre o nome do disco do sistema operacional](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Na página **visão geral** do disco do sistema operacional, selecione **criar instantâneo**.
1. Crie um instantâneo no mesmo local que o disco do sistema operacional.

## <a name="create-a-disk-from-the-snapshot"></a>Criar um disco a partir do instantâneo
Para criar um disco a partir do instantâneo, siga estas etapas:

1. Selecione **Cloud Shell** na portal do Azure.

    ![Imagem sobre abrir Cloud Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Execute os comandos do PowerShell a seguir para criar um disco gerenciado a partir do instantâneo. Você deve substituir esses nomes de exemplo pelos nomes apropriados.

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
3. Se os comandos forem executados com êxito, você verá o novo disco no grupo de recursos que você forneceu.

## <a name="attach-disk-to-another-vm"></a>Anexar disco a outra VM
Para as próximas etapas, você usa outra VM para fins de solução de problemas. Depois de anexar o disco à VM de solução de problemas, você pode procurar e editar o conteúdo do disco. Esse processo permite que você corrija quaisquer erros de configuração ou examine arquivos adicionais de log do sistema ou do aplicativo. Para anexar o disco a outra VM, siga estas etapas:

1. Selecione o grupo de recursos no portal e, em seguida, selecione a VM de solução de problemas. Selecione **discos**, selecione **Editar**e, em seguida, clique em **adicionar disco de dados**:

    ![Anexar disco existente no portal](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Na lista **discos de dados** , selecione o disco do sistema operacional da VM que você identificou. Se você não vir o disco do sistema operacional, verifique se a solução de problemas da VM e do disco do sistema operacional estão na mesma região (local). 
3. Selecione **salvar** para aplicar as alterações.

## <a name="mount-the-attached-data-disk"></a>Montar o disco de dados anexado

> [!NOTE]
> Os exemplos a seguir detalham as etapas necessárias em uma VM do Ubuntu. Se você estiver usando um distribuição Linux diferente, como Red Hat Enterprise Linux ou Suse, os locais e `mount` os comandos do arquivo de log poderão ser um pouco diferentes. Consulte a documentação de seu distribuição específico para obter as alterações apropriadas nos comandos.

1. SSH para sua VM de solução de problemas usando as credenciais apropriadas. Se esse disco for o primeiro disco de dados anexado à sua VM de solução de problemas, provavelmente ele `/dev/sdc`estará conectado a. Use `dmseg` para listar os discos anexados:

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


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Corrigir problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, agora você pode executar todas as etapas de manutenção e solução de problemas, conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmontar e desanexar o disco rígido virtual original
Depois que os erros forem resolvidos, desanexe o disco rígido virtual existente da VM de solução de problemas. Você não pode usar seu disco rígido virtual com qualquer outra VM até que a concessão que está anexando o disco rígido virtual à VM de solução de problemas seja liberada.

1. Na sessão do SSH para sua VM de solução de problemas, desmonte o disco rígido virtual existente. Altere o diretório pai para o ponto de montagem primeiro:

    ```bash
    cd /
    ```

    Agora desmonte o disco rígido virtual existente. O exemplo a seguir desmonta o dispositivo em `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Agora desanexe o disco rígido virtual da VM. Selecione sua VM no portal e clique em **discos**. Selecione o disco rígido virtual existente e clique em **desanexar**:

    ![Desanexar disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Aguarde até que a VM tenha desanexado com êxito o disco de dados antes de continuar.

## <a name="swap-the-os-disk-for-the-vm"></a>Trocar o disco do sistema operacional da VM

Portal do Azure agora dá suporte à alteração do disco do sistema operacional da VM. Para tal, siga estes passos:

1. Vá para [portal do Azure](https://portal.azure.com). Selecione **máquinas virtuais** na barra lateral e, em seguida, selecione a VM que tem o problema.
1. No painel esquerdo, selecione **discos**e, em seguida, selecione **trocar disco do sistema operacional**.
        ![A imagem sobre o disco do sistema operacional de permuta no portal do Azure](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Escolha o novo disco que você reparou e digite o nome da VM para confirmar a alteração. Se você não vir o disco na lista, aguarde 10 ~ 15 minutos depois de desanexar o disco da VM de solução de problemas. Verifique também se o disco está no mesmo local que a VM.
1. Selecione OK.

## <a name="next-steps"></a>Passos Seguintes
Se você estiver tendo problemas para se conectar à sua VM, consulte [solucionar problemas de conexões SSH para uma VM do Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Para problemas com o acesso a aplicativos em execução na sua VM, consulte [solucionar problemas de conectividade do aplicativo em uma VM do Linux](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para obter mais informações sobre como usar o Gerenciador de recursos, consulte [Azure Resource Manager visão geral](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
