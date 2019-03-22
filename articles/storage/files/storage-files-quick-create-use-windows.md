---
title: Início rápido do Azure - criar e utilizar uma partilha de ficheiros do Azure em VMs do Windows | Documentos da Microsoft
description: Neste início rápido, configurar uma partilha de ficheiros do Azure no portal do Azure e ligá-la a uma máquina virtual do Windows. Ligar à partilha de ficheiros, carregar um ficheiro para a partilha de ficheiros. Em seguida, tirar um instantâneo de partilha de ficheiros, modificar o ficheiro na partilha de ficheiros e, restaurar um instantâneo anterior da partilha de ficheiros.
services: storage
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 5109f4e801c1e34b2026cff8f8dd83558618e153
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58165799"
---
# <a name="quickstart-create-and-manage-azure-files-share-with-windows-virtual-machines"></a>Início rápido: Criar e gerir a partilha de ficheiros do Azure com as máquinas virtuais do Windows

O artigo demonstra a partilham os passos básicos para criar e utilizar um serviço ficheiros do Azure. Neste início rápido, a ênfase está em configurar rapidamente uma partilha de ficheiros do Azure para que pode experimentar o funcionamento do serviço. Se precisar de instruções mais detalhadas para criar e utilizar o Azure partilhas de ficheiros no seu próprio ambiente, consulte [utilizar uma partilha de ficheiros do Azure com o Windows](storage-how-to-use-files-windows.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Preparar o ambiente

Neste início rápido, pode configurar os seguintes itens:

- Uma conta de armazenamento do Azure e uma partilha de ficheiros do Azure
- Um VM do Windows Server 2016 Datacenter

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Antes de pode trabalhar com uma partilha de ficheiros do Azure, tem de criar uma conta de armazenamento do Azure. Uma conta de armazenamento para fins gerais v2 proporciona acesso a todos os serviços de armazenamento do Azure: blobs, ficheiros, filas e tabelas. O início rápido cria uma conta de armazenamento para fins gerais v2, mas os passos para criar qualquer tipo de conta de armazenamento são semelhantes. Uma conta de armazenamento pode conter um número ilimitado de partilhas. Uma partilha pode armazenar um número ilimitado de ficheiros, até aos limites de capacidade da conta de armazenamento.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

Depois, crie uma partilha de ficheiros.

1. Quando a implementação da conta de armazenamento do Azure estiver concluída, selecione **Ir para recurso**.
1. Selecione **ficheiros** partir do painel de conta de armazenamento.

    ![Selecionar ficheiros](./media/storage-files-quick-create-use-windows/click-files.png)

1. Selecione **+ partilha de ficheiros**.

    ![Selecione o botão Adicionar uma partilha de ficheiros](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Nomeie a nova partilha de ficheiros *qsfileshare* > introduza "1" para o **Quota** > selecione **criar**. A quota pode ter um máximo de 5 TiB, mas precisa apenas 1 GiB para este início rápido.
1. Crie um novo ficheiro txt chamado *qsTestFile* no seu computador local.
1. Selecione a nova partilha de ficheiros, em seguida, na localização de partilha de ficheiros, selecione **carregar**.

    ![Carregar um ficheiro](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Navegue até à localização onde criou o ficheiro. txt > selecione *qsTestFile.txt* > selecione **carregar**.

Até agora, criou uma conta de armazenamento do Azure e uma partilha de ficheiros com um arquivo na mesma no Azure. Em seguida irá criar a VM do Azure com o Windows Server 2016 Datacenter para representar o servidor no local neste início rápido.

### <a name="deploy-a-vm"></a>Implementar uma VM

1. Em seguida, expanda o menu no lado esquerdo do portal e escolha **Criar um recurso**, no canto superior esquerdo do portal do Azure.
1. Na caixa de pesquisa acima da lista de recursos do **Azure Marketplace**, procure e selecione **Windows Server 2016 Datacenter** e escolha **Criar**.
1. Na **Noções básicas** separador, em **detalhes do projeto**, selecione o grupo de recursos que criou para este início rápido.

   ![Introduza as informações básicas sobre a VM no painel do portal](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. Sob **detalhes de instância**, dê o nome da VM *qsVM*.
1. Mantenha as predefinições para **Região**, **Opções de disponibilidade**, **Imagem** e **Tamanho**.
1. Sob **conta de administrador**, adicione *VMadmin* como o **nome de utilizador** e introduza um **palavra-passe** para a VM.
1. Em **Regras da porta de entrada**, selecione **Permitir portas selecionadas** e, em seguida, selecione **RDP (3389)** e **HTTP** na lista pendente.
1. Selecione **Rever + criar**.
1. Selecione **Criar**. A criação da VM nova irá demorar alguns minutos a concluir.

1. Uma vez concluída a implementação de VM, selecione **Ir para recurso**.

Nesta fase, já criou uma máquina virtual nova e anexou um disco de dados. Agora, é preciso ligar à VM.

### <a name="connect-to-your-vm"></a>Ligar à VM

1. Selecione **Connect** na página de propriedades da máquina virtual.

   ![Ligar a uma VM do Azure a partir do portal](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. Na **ligar à máquina virtual** página, mantenha as opções predefinidas para ligar ao **endereço IP** sobre **número da porta** *3389* e selecione **Ficheiro RDP transferir**.
1. Abra o ficheiro RDP transferido e selecione **Connect** quando lhe for pedido.
1. Na janela **Segurança do Windows**, selecione **Mais escolhas** e **Utilizar uma conta diferente**. Escreva o nome de utilizador como *localhost\username*, onde &lt;username&gt; é o nome de utilizador de administrador do VM que criou para a máquina virtual. Introduza a palavra-passe que criou para a máquina virtual e, em seguida, selecione **OK**.

   ![Mais opções](./media/storage-files-quick-create-use-windows/local-host2.png)

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Selecione **Sim** ou **continuar** para criar a ligação.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Mapear a partilha de ficheiros do Azure para uma unidade do Windows

1. No portal do Azure, navegue para o *qsfileshare* partilha de ficheiros e selecione **Connect**.
1. Copie o conteúdo da caixa de segundo e cole-a no **bloco de notas**.

   ![O caminho UNC a partir do painel Ligar dos Ficheiros do Azure](./media/storage-files-quick-create-use-windows/portal_netuse_connect2.png)

1. Na VM, abra **Explorador de ficheiros** e selecione **este PC** na janela. Esta seleção irá alterar os menus disponíveis no Friso. Sobre o **computador** menu, selecione **mapear unidade de rede**.
1. Selecione a letra de unidade e introduza o caminho UNC. Se seguiu as sugestões de nomenclatura neste início rápido, copie  *\\qsstorageacct.file.core.windows.net\qsfileshare* partir **bloco de notas**.

   Certifique-se de que ambas as caixas de verificação são verificadas.

   ![Instantâneo da caixa de diálogo “Mapear Unidade de Rede”](./media/storage-files-quick-create-use-windows/mountonwindows10.png)

1. Selecione **Concluir**.
1. Na **segurança do Windows** caixa de diálogo:

   - Do bloco de notas, copie o nome da conta de armazenamento anexado ao AZURE\ e cole-na **segurança do Windows** caixa de diálogo como o nome de utilizador. Se seguiu as sugestões de nomenclatura neste início rápido, copie *AZURE\qsstorageacct*.
   - Do bloco de notas, copie a chave de conta de armazenamento e cole-na **segurança do Windows** caixa de diálogo como a palavra-passe.

      ![O caminho UNC a partir do painel Ligar dos Ficheiros do Azure](./media/storage-files-quick-create-use-windows/portal_netuse_connect3.png)

## <a name="create-a-share-snapshot"></a>Criar um instantâneo de partilha

Agora que tenha mapeado a unidade, pode criar um instantâneo.

1. No portal, navegue para a partilha de ficheiros e selecione **criar instantâneo**.

   ![Criar um instantâneo](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. Na VM, abra a *qstestfile.txt* e escreva "este ficheiro foi modificado" > guarde e feche o ficheiro.
1. Crie instantâneo de outro.

## <a name="browse-a-share-snapshot"></a>Procurar um instantâneo de partilha

1. Na sua partilha de ficheiros, selecione **ver instantâneos**.
1. Sobre o **instantâneos de partilha de ficheiros** painel, selecione o primeiro instantâneo na lista.

   ![Instantâneo selecionado na lista de carimbos de data / hora](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. No painel de nesse instantâneo, selecione *qsTestFile.txt*.

## <a name="restore-from-a-snapshot"></a>Restaurar a partir de um instantâneo

1. No painel de instantâneo de partilha de ficheiros, clique com botão direito a *qsTestFile*e selecione o **restaurar** botão.
1. Selecione **substituir ficheiro original**.

   ![Download e botões de restauro](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. Na VM, abra o ficheiro. A versão não modificada foi restaurada.

## <a name="delete-a-share-snapshot"></a>Eliminar um instantâneo de partilha

1. Na sua partilha de ficheiros, selecione **ver instantâneos**.
1. Sobre o **instantâneos de partilha de ficheiros** painel, selecione o último instantâneo na lista e clique em **eliminar**.

   ![Botão de eliminar](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Utilizar um instantâneo de partilha no Windows

Assim como com instantâneos VSS no local, pode ver os instantâneos de partilha de ficheiros do Azure montada utilizando o separador de versões anteriores.

1. No Explorador de ficheiros, localize a partilha montada.

   ![Partilha montada no Explorador de ficheiros](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Selecione *qsTestFile.txt* e > com o botão direito e selecione **propriedades** no menu.

   ![Clique com o botão direito do rato no menu para um diretório selecionado](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Selecione **Versões Anteriores** para ver a lista de instantâneos de partilha para este diretório.

1. Selecione **abra** para abrir o instantâneo.

   ![Separador Versões Anteriores](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Restaurar de uma versão anterior

1. Selecione **restaurar**. Esta ação copia o conteúdo do recursivamente diretório inteiro para a localização original no momento que do instantâneo de partilha foi criado.

   ![Restaurar o botão na mensagem de aviso](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Utilizar uma partilha de ficheiros do Azure com Windows](storage-how-to-use-files-windows.md)
