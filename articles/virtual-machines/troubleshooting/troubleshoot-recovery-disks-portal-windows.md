---
title: Usar uma VM de solução de problemas do Windows no portal do Azure | Microsoft Docs
description: Saiba como solucionar problemas de máquina virtual do Windows no Azure conectando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: e76fc2da8da2325a8bb0cda47c4405c9eb03c8f4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75374561"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Solucionar problemas de uma VM do Windows anexando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure
Se sua VM (máquina virtual) do Windows no Azure encontrar um erro de disco ou de inicialização, talvez seja necessário executar as etapas de solução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma atualização de aplicativo com falha que impede que a VM seja capaz de inicializar com êxito. Este artigo fornece detalhes sobre como usar o portal do Azure para conectar seu disco rígido virtual a outra VM do Windows para corrigir erros e, em seguida, recriar a VM original. 

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Pare a VM afetada.
1. Crie um instantâneo para o disco do sistema operacional da VM.
1. Crie um disco rígido virtual a partir do instantâneo.
1. Anexe e monte o disco rígido virtual em outra VM do Windows para fins de solução de problemas.
1. Ligue à VM da resolução de problemas. Edite arquivos ou execute qualquer ferramenta para corrigir problemas no disco rígido virtual original.
1. Desmonte e desanexe o disco rígido virtual da VM de resolução de problemas.
1. Troque o disco do sistema operacional da VM.

> [!NOTE]
> Este artigo não se aplica à VM com disco não gerenciado.

## <a name="take-a-snapshot-of-the-os-disk"></a>Tirar um instantâneo do disco do sistema operacional
Um instantâneo é uma cópia completa e somente leitura de um disco rígido virtual (VHD). Recomendamos que você desligue corretamente a VM antes de tirar um instantâneo, para limpar todos os processos em andamento. Para tirar um instantâneo de um disco do sistema operacional, siga estas etapas:

1. Vá para [portal do Azure](https://portal.azure.com). Selecione **máquinas virtuais** na barra lateral e, em seguida, selecione a VM que tem o problema.
1. No painel esquerdo, selecione **discos**e, em seguida, selecione o nome do disco do sistema operacional.
    ![imagem sobre o nome do disco do sistema operacional](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
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
    
    #Provide the storage type for Managed Disk.  Premium_LRS or Standard_LRS.
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

## <a name="attach-the-disk-to-another-vm"></a>Anexar o disco a outra VM
Para as próximas etapas, você usa outra VM para fins de solução de problemas. Depois de anexar o disco à VM de solução de problemas, você pode procurar e editar o conteúdo do disco. Esse processo permite que você corrija quaisquer erros de configuração ou examine arquivos adicionais de log do sistema ou do aplicativo. Para anexar o disco a outra VM, siga estas etapas:

1. Selecione o grupo de recursos no portal e, em seguida, selecione a VM de solução de problemas. Selecione **discos**, selecione **Editar**e, em seguida, clique em **adicionar disco de dados**:

    ![Anexar disco existente no portal](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Na lista **discos de dados** , selecione o disco do sistema operacional da VM que você identificou. Se você não vir o disco do sistema operacional, verifique se a solução de problemas da VM e do disco do sistema operacional estão na mesma região (local). 
3. Selecione **salvar** para aplicar as alterações.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Montar o disco de dados anexado para a VM

1. Abra uma conexão de Área de Trabalho Remota para a VM de solução de problemas. 
2. Na VM de solução de problemas, abra **Gerenciador do servidor**e, em seguida, selecione **serviços de arquivo e armazenamento**. 

    ![Selecione os serviços de arquivo e armazenamento no Gerenciador do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. O disco de dados é detectado e anexado automaticamente. Para ver uma lista dos discos conectados, selecione **discos**. Você pode selecionar o disco de dados para exibir informações de volume, incluindo a letra da unidade. O exemplo a seguir mostra o disco de dados anexado e usando **F:** :

    ![Informações de volume e anexadas em disco no Gerenciador do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Corrigir problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, agora você pode executar todas as etapas de manutenção e solução de problemas, conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmontar e desanexar o disco rígido virtual original
Depois que os erros forem resolvidos, desanexe o disco rígido virtual existente da VM de solução de problemas. Você não pode usar seu disco rígido virtual com qualquer outra VM até que a concessão que está anexando o disco rígido virtual à VM de solução de problemas seja liberada.

1. Na sessão RDP para sua VM, abra **Gerenciador do servidor**e, em seguida, selecione **serviços de arquivo e armazenamento**:

    ![Selecione serviços de arquivo e armazenamento em Gerenciador do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Selecione **discos** e, em seguida, selecione o disco de dados. Clique com o botão direito do mouse no disco de dados e selecione **colocar offline**:

    ![Definir o disco de dados como offline no Gerenciador do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Agora desanexe o disco rígido virtual da VM. Selecione sua VM no portal do Azure e clique em **discos**. 
4. Selecione **Editar**, selecione o disco do sistema operacional que você anexou e clique em **desanexar**:

    ![Desanexar disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Aguarde até que a VM tenha desanexado com êxito o disco de dados antes de continuar.

## <a name="swap-the-os-disk-for-the-vm"></a>Trocar o disco do sistema operacional da VM

Portal do Azure agora dá suporte à alteração do disco do sistema operacional da VM. Para tal, siga estes passos:

1. Vá para [portal do Azure](https://portal.azure.com). Selecione **máquinas virtuais** na barra lateral e, em seguida, selecione a VM que tem o problema.
1. No painel esquerdo, selecione **discos**e, em seguida, selecione **trocar disco do sistema operacional**.
        ![a imagem sobre o disco do sistema operacional de permuta no portal do Azure](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Escolha o novo disco que você reparou e digite o nome da VM para confirmar a alteração. Se você não vir o disco na lista, aguarde 10 ~ 15 minutos depois de desanexar o disco da VM de solução de problemas. Verifique também se o disco está no mesmo local que a VM.
1. Selecione OK.

## <a name="next-steps"></a>Passos seguintes
Se você estiver tendo problemas para se conectar à sua VM, consulte [solucionar problemas de conexões RDP para uma VM do Azure](troubleshoot-rdp-connection.md). Para problemas com o acesso a aplicativos em execução na sua VM, consulte [solucionar problemas de conectividade do aplicativo em uma VM do Windows](troubleshoot-app-connection.md).

Para obter mais informações sobre como usar o Gerenciador de recursos, consulte [Azure Resource Manager visão geral](../../azure-resource-manager/management/overview.md).


