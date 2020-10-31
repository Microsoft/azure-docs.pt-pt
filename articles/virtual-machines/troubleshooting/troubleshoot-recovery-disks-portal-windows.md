---
title: Utilize um VM de resolução de problemas do Windows no portal Azure Microsoft Docs
description: Saiba como resolver problemas com a máquina virtual do Windows em Azure ligando o disco OS a um VM de recuperação utilizando o portal Azure
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
ms.openlocfilehash: 5b7fc4a120f5a4b513e1852fc6e2cf5ab68e9631
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101260"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Resolução de problemas de um VM do Windows, fixando o disco DE Aa a um VM de recuperação utilizando o portal Azure
Se a sua máquina virtual Do Windows (VM) em Azure encontrar um erro de arranque ou disco, poderá ter de executar etapas de resolução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma atualização de aplicação falhada que impede o VM de ser capaz de arrancar com sucesso. Este artigo detalha como utilizar o portal Azure para ligar o seu disco rígido virtual a outro VM do Windows para corrigir eventuais erros e, em seguida, recriar o seu VM original. 

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Pare o VM afetado.
1. Crie uma imagem instantânea para o disco de so do VM.
1. Crie um disco rígido virtual a partir do instantâneo.
1. Fixe e monte o disco rígido virtual a outro VM do Windows para efeitos de resolução de problemas.
1. Ligue à VM da resolução de problemas. Editar ficheiros ou executar quaisquer ferramentas para corrigir problemas no disco rígido virtual original.
1. Desmonte e desanexe o disco rígido virtual da VM de resolução de problemas.
1. Troque o disco DE pelo VM.

> [!NOTE]
> Este artigo não se aplica ao VM com disco não gerido.

## <a name="take-a-snapshot-of-the-os-disk"></a>Tire uma foto do disco de oss
Um instantâneo é uma cópia completa e só de leitura de um disco rígido virtual (VHD). Recomendamos que desligue o VM de forma limpa antes de tirar uma foto, para limpar quaisquer processos em curso. Para tirar uma foto de um disco de so, siga estes passos:

1. Ir para o [portal Azure.](https://portal.azure.com) Selecione **máquinas virtuais** da barra lateral e, em seguida, selecione o VM que tem problemas.
1. No painel esquerdo, selecione **Discos** e, em seguida, selecione o nome do disco OS.
    ![Imagem sobre o nome do disco oss](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Na página **geral** do disco SO e, em seguida, selecione **Criar instantâneo** .
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
Para os próximos passos, utilize outro VM para efeitos de resolução de problemas. Depois de fixar o disco ao VM de resolução de problemas, pode navegar e editar o conteúdo do disco. Este processo permite-lhe corrigir quaisquer erros de configuração ou rever ficheiros adicionais de aplicação ou registo de sistema. Para fixar o disco a outro VM, siga estes passos:

1. Selecione o seu grupo de recursos a partir do portal e, em seguida, selecione o seu VM de resolução de problemas. Selecione **Discos,** **selecione Editar** e, em seguida, clique em **Adicionar disco de dados** :

    ![Anexar o disco existente no portal](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Na lista **de discos de dados,** selecione o disco de so do VM que identificou. Se não vir o disco de so, certifique-se de que a resolução de problemas de VM e o disco de sos está na mesma região (localização). 
3. **Selecione Guardar** para aplicar as alterações.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Monte o disco de dados anexado ao VM

1. Abra uma ligação de ambiente de trabalho remoto ao VM de resolução de problemas. 
2. No VM de resolução de problemas, Open **Server Manager,** selecione **Os Serviços de Ficheiros e Armazenamento** . 

    ![Selecione serviços de arquivo e armazenamento dentro do Gestor do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. O disco de dados é automaticamente detetado e anexado. Para ver uma lista dos discos ligados, selecione **Discos** . Pode selecionar o seu disco de dados para visualizar informações de volume, incluindo a letra de unidade. O exemplo a seguir mostra o disco de dados anexado e utilizando **F:**

    ![Informações de disco anexadas e de volume no Gestor do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Corrija problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, pode agora executar quaisquer passos de manutenção e resolução de problemas conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmonte e desprete o disco rígido virtual original
Assim que os seus erros forem resolvidos, retire o disco rígido virtual existente do seu VM de resolução de problemas. Não é possível utilizar o seu disco rígido virtual com qualquer outro VM até que o contrato de arrendamento que liga o disco rígido virtual ao VM de resolução de problemas seja lançado.

1. Da sessão RDP ao seu VM, abra o **Server Manager,** em seguida, selecione **Serviços de Arquivo e Armazenamento** :

    ![Selecione serviços de arquivo e armazenamento no gestor do servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Selecione **Discos** e, em seguida, selecione o seu disco de dados. Clique com o botão direito no seu disco de dados e selecione **'Tirar Offline'**

    ![Desa couuser o disco de dados como offline no Gestor do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Agora desprenda o disco rígido virtual do VM. Selecione o seu VM no portal Azure e clique em **Discos** . 
4. Selecione **Editar,** selecione o disco DE que anexou e clique em **Apagar** :

    ![Desprender o disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Aguarde até que o VM tenha sido eliminado com sucesso no VM ie., desprender o disco de dados antes de continuar.

## <a name="swap-the-os-disk-for-the-vm"></a>Troque o disco DE pelo VM

O portal Azure suporta agora a alteração do disco DE do VM. Para tal, siga estes passos:

1. Ir para o [portal Azure.](https://portal.azure.com) Selecione **máquinas virtuais** da barra lateral e, em seguida, selecione o VM que tem problemas.
1. No painel esquerdo, selecione **Discos** e, em seguida, selecione **o disco Swap OS** .
        ![A imagem sobre o disco Swap OS no portal Azure](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Escolha o novo disco que reparou e, em seguida, digite o nome do VM para confirmar a alteração. Se não vir o disco na lista, aguarde 10 ~ 15 minutos depois de retirar o disco do VM de resolução de problemas. Certifique-se também de que o disco está no mesmo local que o VM.
1. Selecione OK.

## <a name="next-steps"></a>Passos seguintes
Se tiver problemas de ligação ao seu VM, consulte [as ligações RDP de resolução de problemas a um Azure VM](troubleshoot-rdp-connection.md). Para problemas com o acesso a aplicações em execução no seu VM, consulte [problemas de conectividade da aplicação Troubleshoot num VM do Windows](troubleshoot-app-connection.md).

Para obter mais informações sobre a utilização do Gestor de Recursos, consulte [a visão geral do Gestor de Recursos do Azure](../../azure-resource-manager/management/overview.md).


