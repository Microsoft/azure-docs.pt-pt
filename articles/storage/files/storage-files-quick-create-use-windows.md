---
title: Criar e utilizar uma partilha de Ficheiros Azure em VMs do Windows
description: Crie e utilize uma partilha de Ficheiros Azure no portal Azure. Conecte-o a um VM do Windows, conecte-se à partilha de Ficheiros e faça o upload de um ficheiro para a partilha de Ficheiros.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 5a3c664f6c6c0532ef915357cfbcbc8228202502
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718303"
---
# <a name="quickstart-create-and-manage-azure-files-share-with-windows-virtual-machines"></a>Quickstart: Criar e gerir ficheiros Azure partilhar com máquinas virtuais do Windows

O artigo demonstra os passos básicos para criar e utilizar uma partilha de Ficheiros Azure. Neste arranque rápido, a ênfase está em criar rapidamente uma partilha de Ficheiros Azure para que possa experimentar como o serviço funciona. Se necessitar de instruções mais detalhadas para criar e utilizar ações de ficheiros Azure no seu próprio ambiente, consulte [utilizar uma partilha de ficheiros Azure com o Windows](storage-how-to-use-files-windows.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Preparar o ambiente

Neste arranque rápido, configura os seguintes itens:

- Uma conta de armazenamento Azure e uma partilha de ficheiros Azure
- Um VM do Datacenter 2016 do Windows Server 2016

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Antes de poder trabalhar com uma partilha de ficheiros Azure, tem de criar uma conta de armazenamento Azure. Uma conta de armazenamento v2 de uso geral fornece acesso a todos os serviços de Armazenamento Azure: bolhas, ficheiros, filas e tabelas. O quickstart cria uma conta de armazenamento v2 para fins gerais mas os passos para criar qualquer tipo de conta de armazenamento são semelhantes. Uma conta de armazenamento pode conter um número ilimitado de partilhas. Uma partilha pode armazenar um número ilimitado de ficheiros, até aos limites de capacidade da conta de armazenamento.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

Depois, crie uma partilha de ficheiros.

1. Quando a implementação da conta de armazenamento Azure estiver concluída, selecione **Ir para o recurso**.
1. Selecione **as ações** do Ficheiro a partir do painel de conta de armazenamento.

    ![Selecione Partilhas de ficheiros.](./media/storage-files-quick-create-use-windows/click-files.png)

1. Selecione **+ Partilha de Ficheiros**.

    ![Selecione + partilha de ficheiros para criar uma nova partilha de ficheiros.](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Nomeie o novo *ficheiro qsfileshare,* introduza "1" para a **Quota,** deixe **a Transação otimizada** selecionada e selecione **Criar**. A quota pode ser no máximo de 5 TiB (100 TiB, com grandes ações de ficheiros ativadas), mas só precisa de 1 GiB para este arranque rápido.
1. Crie um novo ficheiro txt chamado *qsTestFile* na sua máquina local.
1. Selecione a nova partilha de ficheiros e, em seguida, na localização da partilha de ficheiros, selecione **Upload**.

    ![Faça o upload de um ficheiro.](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Navegue no local onde criou o seu ficheiro .txt > selecione *qsTestFile.txt* > selecione **Upload**.

Até agora, criou uma conta de armazenamento Azure e uma partilha de ficheiros com um ficheiro no Azure. Em seguida, irá criar o Azure VM com o Windows Server 2016 Datacenter para representar o servidor no local neste arranque rápido.

### <a name="deploy-a-vm"></a>Implementar uma VM

1. Em seguida, expanda o menu no lado esquerdo do portal e escolha **Criar um recurso**, no canto superior esquerdo do portal do Azure.
1. Na caixa de pesquisa acima da lista de recursos do **Azure Marketplace,** procure e selecione **o Datacenter do Windows Server 2016**.
1. No **separador Basics,** em **detalhes do Projeto,** selecione o grupo de recursos que criou para este arranque rápido.

   ![Introduza informações básicas sobre o seu VM na lâmina do portal.](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. Em **exemplo, os detalhes**, nomeie o *QM qsVM* V .
1. Mantenha as predefinições para **Região**, **Opções de disponibilidade**, **Imagem** e **Tamanho**.
1. Na **conta administrador**, adicione um nome de **utilizador** e introduza uma **palavra-passe** para o VM.
1. Em **Regras da porta de entrada**, selecione **Permitir portas selecionadas** e, em seguida, selecione **RDP (3389)** e **HTTP** na lista pendente.
1. Selecione **Rever + criar**.
1. Selecione **Criar**. A criação da VM nova irá demorar alguns minutos a concluir.

1. Uma vez concluída a sua implementação em VM, selecione **Ir para o recurso**.

Nesta fase, já criou uma máquina virtual nova e anexou um disco de dados. Agora, é preciso ligar à VM.

### <a name="connect-to-your-vm"></a>Ligar à VM

1. Selecione **Connect** na página de propriedades da máquina virtual.

   ![Ligar a uma VM do Azure a partir do portal](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. Na página **'Ligar à máquina virtual',** mantenha as opções predefinidos para ligar por **endereço IP** sobre o número **de porta** *3389* e selecione **download do ficheiro RDP**.
1. Abra o ficheiro RDP descarregado e selecione **Connect** quando solicitado.
1. Na janela **Segurança do Windows**, selecione **Mais escolhas** e **Utilizar uma conta diferente**. Digite o nome de utilizador como *nome local,/username,* onde &lt; o nome de utilizador é o nome de utilizador &gt; vm que criou para a máquina virtual. Introduza a palavra-passe que criou para a máquina virtual e, em seguida, selecione **OK**.

   ![Mais opções](./media/storage-files-quick-create-use-windows/local-host2.png)

1. Poderá receber um aviso de certificado durante o processo de início de sessão. **selecione Sim** ou **Continue** a criar a ligação.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Mapear a partilha de ficheiros Azure para uma unidade do Windows

1. No portal Azure, navegue para o ficheiro *qsfileshare* e selecione **Connect**.
1. Selecione uma letra de unidade e, em seguida, copie o conteúdo da segunda caixa e cole-a no **Bloco de Notas**.

   :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Screenshot que mostra o conteúdo da caixa que deve copiar e colar no Bloco de Notas." lightbox="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png":::

1. No VM, abra **o PowerShell** e cole o conteúdo do Bloco de **Notas** e, em seguida, prima para entrar para executar o comando. Deve mapear a unidade.

## <a name="create-a-share-snapshot"></a>Criar um instantâneo de partilha

Agora que mapeou a unidade, pode criar uma foto.

1. No portal, navegue para a sua partilha de **ficheiros,** selecione Snapshots e, em seguida, **selecione + Adicionar instantâneo**.

   ![Selecione instantâneos sob a secção de operações e, em seguida, selecione adicionar instantâneo.](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. No VM, abra a *qstestfile.txt* e escreva "este ficheiro foi modificado" > Guardar e fechar o ficheiro.
1. Criar outra imagem.

## <a name="browse-a-share-snapshot"></a>Navegue num instantâneo de partilha

1. Na sua partilha de **ficheiros,** selecione Snapshots .
1. Na lâmina **Snapshots,** selecione a primeira imagem da lista.

   ![Instantâneo selecionado na lista de selos temporais](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. Abra a foto e selecione *qsTestFile.txt*.

## <a name="restore-from-a-snapshot"></a>Restaurar a partir de um instantâneo

1. A partir da lâmina de instantâneo de partilha de ficheiro, clique com o botão *qsTestFile* e selecione o botão **Restaurar.**

    :::image type="content" source="media/storage-files-quick-create-use-windows/restore-share-snapshot.png" alt-text="Screenshot da lâmina instantânea, qstestfile é selecionado, restauro é realçado.":::

1. Selecione **Overwrite original file**.

   ![Screenshot de restaurar pop-up, o ficheiro original de substituição é selecionado.](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. No VM, abra o ficheiro. A versão não modificada foi restaurada.

## <a name="delete-a-share-snapshot"></a>Eliminar um instantâneo de partilha

1. Na sua partilha de **ficheiros,** selecione Snapshots .
1. Na lâmina **Snapshots,** selecione a última imagem da lista e selecione **Delete**.

   ![Screenshot da lâmina de instantâneo, última imagem selecionada, apagar botão realçado.](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Use uma imagem de partilha no Windows

Tal como acontece com as imagens VSS no local, pode ver as imagens da sua partilha de ficheiros Azure montada utilizando o separador Versões Anteriores.

1. No File Explorer, localize a parte montada.

   ![Partilha montada no Explorador de Ficheiros](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Selecione *qsTestFile.txt* e > clique à direita e selecione **Propriedades** no menu.

   ![Clique com o botão direito do rato no menu para um diretório selecionado](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Selecione **Versões Anteriores** para ver a lista de instantâneos de partilha para este diretório.

1. Selecione **Abrir** para abrir a imagem.

   ![Separador Versões Anteriores](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Restaurar de uma versão anterior

1. Selecione **Restaurar**. Esta ação copia o conteúdo de todo o diretório recursivamente para a localização original no momento em que o instantâneo de partilha foi criado.

   ![Botão de restauro na mensagem de aviso](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png)
    
    > [!NOTE]
    > Se o seu ficheiro não tiver sido alterado, não verá uma versão anterior para esse ficheiro porque esse ficheiro é a mesma versão que o instantâneo. Isto é consistente com o funcionamento de um servidor de ficheiros Windows.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Use an Azure file share with Windows](storage-how-to-use-files-windows.md) (Utilizar uma partilha de ficheiros do Azure com o Windows)
