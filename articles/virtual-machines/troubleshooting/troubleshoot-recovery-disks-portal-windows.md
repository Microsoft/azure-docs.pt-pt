---
title: Usar uma VM de solução de problemas do Windows no portal do Azure | Microsoft Docs
description: Saiba como solucionar problemas de máquina virtual do Windows no Azure conectando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/13/2018
ms.author: genli
ms.openlocfilehash: 8ab6fc75475cd99e3d803450476880175f12d2b6
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881153"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Solucionar problemas de uma VM do Windows anexando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure
Se sua VM (máquina virtual) do Windows no Azure encontrar um erro de disco ou de inicialização, talvez seja necessário executar as etapas de solução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma atualização de aplicativo com falha que impede que a VM seja capaz de inicializar com êxito. Este artigo fornece detalhes sobre como usar o portal do Azure para conectar seu disco rígido virtual a outra VM do Windows para corrigir erros e, em seguida, recriar a VM original. 

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Exclua a VM que está encontrando problemas, mantendo os discos rígidos virtuais.
2. Anexe e monte o disco rígido virtual em outra VM do Windows para fins de solução de problemas.
3. Ligue à VM da resolução de problemas. Edite arquivos ou execute qualquer ferramenta para corrigir problemas no disco rígido virtual original.
4. Desmonte e desanexe o disco rígido virtual da VM de resolução de problemas.
5. Crie uma VM usando o disco rígido virtual original.

Para a VM que usa o disco gerenciado, agora podemos usar Azure PowerShell para alterar o disco do sistema operacional de uma VM. Não precisamos mais excluir e recriar a VM. Para obter mais informações, consulte [solucionar problemas de uma VM do Windows anexando o disco do sistema operacional a uma VM de recuperação usando Azure PowerShell](troubleshoot-recovery-disks-windows.md).

> [!NOTE]
> Este artigo não se aplica à VM com disco não gerenciado.

## <a name="determine-boot-issues"></a>Determinar problemas de inicialização
Para determinar por que a VM não pode ser inicializada corretamente, examine a captura de tela da VM de diagnóstico de inicialização. Um exemplo comum seria uma atualização de aplicativo com falha ou um disco rígido virtual subjacente que está sendo excluído ou movido.

Selecione sua VM no portal e role para baixo até a seção **suporte + solução de problemas** . Clique em **diagnóstico de inicialização** para exibir a captura de tela. Anote quaisquer mensagens de erro ou códigos de erro específicos para ajudar a determinar por que a VM está encontrando um problema.

![Exibindo logs do console do diagnóstico de inicialização da VM](./media/troubleshoot-recovery-disks-portal-windows/screenshot-error.png)

Você também pode clicar em **baixar captura de tela** para baixar uma captura da captura de tela da VM.

## <a name="view-existing-virtual-hard-disk-details"></a>Exibir detalhes do disco rígido virtual existente
Antes de poder anexar o disco rígido virtual a outra VM, você precisa identificar o nome do disco rígido virtual (VHD).

Selecione a VM que tem o problema e, em seguida, selecione **discos**. Registre o nome do disco do sistema operacional como no exemplo a seguir:

![Selecionar blobs de armazenamento](./media/troubleshoot-recovery-disks-portal-windows/view-disk.png)

## <a name="delete-existing-vm"></a>Excluir VM existente
Os discos rígidos virtuais e as VMs são dois recursos diferentes do Azure. Um disco rígido virtual é o local em que o próprio sistema operacional, os aplicativos e as configurações são armazenados. A própria VM é apenas metadados que definem o tamanho ou o local e faz referência a recursos como um disco rígido virtual ou NIC (placa de interface de rede virtual). Cada disco rígido virtual tem uma concessão atribuída quando anexado a uma VM. Embora os discos de dados possam ser anexados e desanexados mesmo se a VM estiver a ser executada, o disco do SO só pode ser desanexado se o recurso da VM for eliminado. A concessão continua a associar o disco do sistema operacional a uma VM, mesmo quando essa VM está em um estado parado e desalocado.

A primeira etapa para recuperar sua VM é excluir o recurso da VM em si. Com a eliminação, os discos rígidos virtuais permanecem na sua conta de armazenamento. Depois que a VM for excluída, você anexará o disco rígido virtual a outra VM para solucionar e resolver os erros.

Selecione sua VM no portal e clique em **excluir**:

![Captura de tela do diagnóstico de inicialização da VM mostrando erro de inicialização](./media/troubleshoot-recovery-disks-portal-windows/stop-delete-vm.png)

Aguarde até que a VM tenha concluído a exclusão antes de anexar o disco rígido virtual a outra VM. A concessão no disco rígido virtual que o associa à VM precisa ser lançada antes que você possa anexar o disco rígido virtual a outra VM.

## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Anexar um disco rígido virtual existente a outra VM
Para as próximas etapas, você usa outra VM para fins de solução de problemas. Você anexa o disco rígido virtual existente a essa VM de solução de problemas para poder procurar e editar o conteúdo do disco. Esse processo permite que você corrija quaisquer erros de configuração ou examine arquivos adicionais de log do sistema ou do aplicativo, por exemplo. Escolha ou crie outra VM a ser usada para fins de solução de problemas.

1. Selecione o grupo de recursos no portal e, em seguida, selecione a VM de solução de problemas. Selecione **discos**, selecione **Editar**e, em seguida, clique em **adicionar disco de dados**:

    ![Anexar disco existente no portal](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Na lista **discos de dados** , selecione o disco do sistema operacional da VM que você identificou. Se você não vir o disco do sistema operacional, verifique se a solução de problemas da VM e do disco do sistema operacional estão na mesma região (local).
3. Selecione **salvar** para aplicar as alterações.

## <a name="mount-the-attached-data-disk"></a>Montar o disco de dados anexado

1. Abra uma conexão Área de Trabalho Remota à sua VM. Selecione sua VM no portal e clique em **conectar**. Baixe e abra o arquivo de conexão RDP. Insira suas credenciais para entrar em sua VM da seguinte maneira:

    ![Entre em sua VM usando Área de Trabalho Remota](./media/troubleshoot-recovery-disks-portal-windows/open-remote-desktop.png)

2. Abra **Gerenciador do servidor**e, em seguida, selecione **serviços de arquivo e armazenamento**. 

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
4. Selecione **Editar**, selecione o disco do sistema operacional que você anexou e clique em desanexar:

    ![Desanexar disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Aguarde até que a VM tenha desanexado com êxito o disco de dados antes de continuar.

## <a name="create-vm-from-original-hard-disk"></a>Criar VM do disco rígido original

### <a name="method-1-use-azure-resource-manager-template"></a>Método 1 Use Azure Resource Manager modelo
Para criar uma VM de seu disco rígido virtual original, use [este modelo de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet). O modelo implanta uma VM em uma rede virtual nova ou existente, usando a URL do VHD do comando anterior. Clique no botão **implantar no Azure** da seguinte maneira:

![Implantar a VM do modelo do GitHub](./media/troubleshoot-recovery-disks-portal-windows/deploy-template-from-github.png)

O modelo é carregado no portal do Azure para implantação. Insira os nomes para sua nova VM e recursos existentes do Azure e cole a URL no disco rígido virtual existente. Para iniciar a implantação, clique em **comprar**:

![Implantar VM do modelo](./media/troubleshoot-recovery-disks-portal-windows/deploy-from-image.png)

### <a name="method-2-create-a-vm-from-the-disk"></a>Método 2 criar uma VM a partir do disco

1. No portal do Azure, selecione o grupo de recursos no portal e localize o disco do sistema operacional. Você também pode pesquisar o disco usando o nome do disco:

    ![Pesquisar disco de portal do Azure](./media/troubleshoot-recovery-disks-portal-windows/search-disk.png)
1. Selecione **visão geral**e, em seguida, selecione **criar VM**.
    ![Criar VM de um disco a partir de portal do Azure](./media/troubleshoot-recovery-disks-portal-windows/create-vm-from-disk.png)
1. Siga o assistente para criar a VM.

## <a name="re-enable-boot-diagnostics"></a>Reabilitar o diagnóstico de inicialização
Quando você cria sua VM a partir do disco rígido virtual existente, o diagnóstico de inicialização pode não ser habilitado automaticamente. Para verificar o status do diagnóstico de inicialização e ativá-lo, se necessário, selecione sua VM no Portal. Em **monitoramento**, clique em **configurações de diagnóstico**. Verifique se o status está **ativado**e se a marca de seleção ao lado de **diagnóstico de inicialização** está selecionada. Se você fizer alterações, clique em **salvar**:

![Atualizar configurações de diagnóstico de inicialização](./media/troubleshoot-recovery-disks-portal-windows/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Passos Seguintes
Se você estiver tendo problemas para se conectar à sua VM, consulte [solucionar problemas de conexões RDP para uma VM do Azure](troubleshoot-rdp-connection.md). Para problemas com o acesso a aplicativos em execução na sua VM, consulte [solucionar problemas de conectividade do aplicativo em uma VM do Windows](troubleshoot-app-connection.md).

Para obter mais informações sobre como usar o Gerenciador de recursos, consulte [Azure Resource Manager visão geral](../../azure-resource-manager/resource-group-overview.md).

