---
title: Utilize um VM de resolução de problemas do Windows no portal Azure  Microsoft Docs
description: Saiba como resolver problemas com problemas com a máquina virtual do Windows em Azure, ligando o disco OS a um VM de recuperação utilizando o portal Azure
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250000"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Problemas de resolução de um VM windows anexando o disco OS a um VM de recuperação utilizando o portal Azure
Se a sua máquina virtual Windows (VM) em Azure encontrar um erro de arranque ou disco, poderá ser necessário efetuar passos de resolução de problemas no disco rígido virtual em si. Um exemplo comum seria uma atualização de aplicação falhada que impede que o VM seja capaz de arrancar com sucesso. Este artigo detalha como utilizar o portal Azure para ligar o seu disco rígido virtual a outro VM do Windows para corrigir quaisquer erros e, em seguida, recriar o seu VM original. 

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Pare o VM afetado.
1. Crie uma imagem para o disco OS do VM.
1. Crie um disco rígido virtual a partir do instantâneo.
1. Fixe e monte o disco rígido virtual noutro VM do Windows para fins de resolução de problemas.
1. Ligue à VM da resolução de problemas. Editar ficheiros ou executar quaisquer ferramentas para corrigir problemas no disco rígido virtual original.
1. Desmonte e desanexe o disco rígido virtual da VM de resolução de problemas.
1. Troque o disco OS pelo VM.

> [!NOTE]
> Este artigo não se aplica ao VM com disco não gerido.

## <a name="take-a-snapshot-of-the-os-disk"></a>Tire uma foto do Disco OS
Um instantâneo é uma cópia completa e apenas de leitura de um disco rígido virtual (VHD). Recomendamos que desligue o VM de forma limpa antes de tirar uma foto, para limpar quaisquer processos que estejam em curso. Para tirar uma foto de um disco de OS, siga estes passos:

1. Vá ao [portal Azure.](https://portal.azure.com) Selecione **as máquinas Virtuais** a partir da barra lateral e, em seguida, selecione o VM que tem problemas.
1. No painel esquerdo, selecione **Discos**e, em seguida, selecione o nome do disco OS.
    ![Imagem sobre o nome do](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png) do disco osso
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
3. Se os comandos funcionarem com sucesso, verá o novo disco no grupo de recursos que forneceu.

## <a name="attach-the-disk-to-another-vm"></a>Fixe o disco a outro VM
Para os próximos passos, você usa outro VM para fins de resolução de problemas. Depois de ligar o disco ao VM de resolução de problemas, pode navegar e editar o conteúdo do disco. Este processo permite-lhe corrigir quaisquer erros de configuração ou rever ficheiros adicionais de registo de aplicações ou sistemas. Para fixar o disco a outro VM, siga estes passos:

1. Selecione o seu grupo de recursos a partir do portal e, em seguida, selecione o seu VM de resolução de problemas. Selecione **Discos,** **selecione Editar**e, em seguida, clique em **Adicionar disco de dados:**

    ![Anexar o disco existente no portal](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Na lista de **discos Data,** selecione o disco OS do VM que identificou. Se não vir o disco OS, certifique-se de que o VM e o disco OS estão na mesma região (localização). 
3. Selecione **Guardar** para aplicar as alterações.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Monte o disco de dados anexado ao VM

1. Abra uma ligação remote Desktop ao VM de resolução de problemas. 
2. No problema VM, Open **Server Manager,** então selecione **Serviços**de Arquivo e Armazenamento . 

    ![Selecione Serviços de Arquivo e Armazenamento dentro do Gestor do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. O disco de dados é automaticamente detetado e anexado. Para ver uma lista dos discos ligados, selecione **Discos**. Pode selecionar o seu disco de dados para visualizar informações sobre o volume, incluindo a letra de unidade. O exemplo seguinte mostra o disco de dados ligado e utilizando **F:**

    ![Informações anexadas e de volume em Server Manager](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Corrigir problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, pode agora realizar quaisquer passos de manutenção e resolução de problemas conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmontar e desmontar disco rígido virtual original
Assim que os seus erros forem resolvidos, retire o disco rígido virtual existente do seu VM de resolução de problemas. Não é possível utilizar o disco rígido virtual com qualquer outro VM até que seja lançado o disco rígido virtual ao VM de resolução de problemas.

1. Da sessão rdp ao seu VM, open **Server Manager,** em seguida, selecione Serviços de **Arquivo e Armazenamento:**

    ![Selecione Serviços de Arquivo e Armazenamento no Gestor do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Selecione **Discos** e, em seguida, selecione o disco de dados. Clique à direita no seu disco de dados e selecione **Take Offline:**

    ![Delineie o disco de dados como offline no Gestor do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Agora retire o disco rígido virtual do VM. Selecione o seu VM no portal Azure e clique em **Discos**. 
4. **Selecione Editar,** selecione o disco OS que anexou e, em seguida, clique em **Desprender:**

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
Se tiver problemas de ligação ao seu VM, consulte [ligações RDP de Troubleshoot a um VM Azure](troubleshoot-rdp-connection.md). Para problemas com o acesso a aplicações em execução no seu VM, consulte problemas de conectividade da [aplicação Troubleshoot num VM windows](troubleshoot-app-connection.md).

Para mais informações sobre a utilização do Gestor de Recursos, consulte a [visão geral do Gestor de Recursos do Azure](../../azure-resource-manager/management/overview.md).


