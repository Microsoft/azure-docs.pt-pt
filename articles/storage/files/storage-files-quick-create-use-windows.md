---
title: Criar e utilizar uma partilha de Ficheiros Azure em VMs do Windows
description: Neste arranque rápido, configura uma partilha de Ficheiros Azure no portal Azure e liga-o a uma máquina virtual do Windows. Liga-se à partilha de Ficheiros, faz o upload de um ficheiro para a partilha de Ficheiros. Em seguida, tira uma fotografia da partilha de Ficheiros, modifica o ficheiro na partilha de Ficheiros e restaura uma fotografia anterior da partilha de Ficheiros.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bbab0ee2eefe6e86c150d5bddab4f8e91a7c92d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75463907"
---
# <a name="quickstart-create-and-manage-azure-files-share-with-windows-virtual-machines"></a>Quickstart: Criar e gerir a partilha de Ficheiros Azure com máquinas virtuais windows

O artigo demonstra os passos básicos para criar e utilizar uma partilha de Ficheiros Azure. Neste arranque rápido, a ênfase está na configuração rápida de uma partilha de Ficheiros Azure para que possa experimentar como o serviço funciona. Se precisar de instruções mais detalhadas para criar e utilizar partilhas de ficheiros Azure no seu próprio ambiente, consulte Utilize uma partilha de [ficheiros Azure com o Windows](storage-how-to-use-files-windows.md).

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Preparar o ambiente

Neste arranque rápido, configura os seguintes itens:

- Uma conta de armazenamento Azure e uma partilha de ficheiros Azure
- Um Windows Server 2016 Datacenter VM

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Antes de poder trabalhar com uma partilha de ficheiros Azure, tem de criar uma conta de armazenamento Azure. Uma conta de armazenamento v2 de uso geral fornece acesso a todos os serviços de Armazenamento Azure: blobs, ficheiros, filas e mesas. O quickstart cria uma conta de armazenamento v2 de propósito geral, mas os passos para criar qualquer tipo de conta de armazenamento são semelhantes. Uma conta de armazenamento pode conter um número ilimitado de partilhas. Uma partilha pode armazenar um número ilimitado de ficheiros, até aos limites de capacidade da conta de armazenamento.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

Depois, crie uma partilha de ficheiros.

1. Quando a implantação da conta de armazenamento Azure estiver concluída, selecione **Ir para o recurso**.
1. Selecione **Ficheiros** do painel da conta de armazenamento.

    ![Selecione Ficheiros](./media/storage-files-quick-create-use-windows/click-files.png)

1. Selecione **Partilha de Ficheiros**.

    ![Selecione o botão de partilha de ficheiros adicionais](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Nomeie a nova partilha de *ficheiros qsfileshare* > insira "1" para o **> de quota** selecione **Criar**. A quota pode ser um máximo de 5 TiB, mas você só precisa de 1 GiB para este arranque rápido.
1. Crie um novo ficheiro txt chamado *qsTestFile* na sua máquina local.
1. Selecione a nova partilha de ficheiros e, em seguida, na localização da partilha de ficheiros, **selecione Upload**.

    ![Carregar um ficheiro](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Navegue no local onde criou o seu ficheiro .txt > selecione *qsTestFile.txt* > selecione **Upload**.

Até agora, criou uma conta de armazenamento Azure e uma partilha de ficheiros com um ficheiro no Azure. Em seguida, irá criar o Azure VM com o Windows Server 2016 Datacenter para representar o servidor no local neste arranque rápido.

### <a name="deploy-a-vm"></a>Implementar uma VM

1. Em seguida, expanda o menu no lado esquerdo do portal e escolha **Criar um recurso**, no canto superior esquerdo do portal do Azure.
1. Na caixa de pesquisa acima da lista de recursos do **Azure Marketplace,** procure e selecione **o Datacenter do Windows Server 2016,** em seguida, escolha **Criar**.
1. No separador **Basics,** sob os detalhes do **Projeto,** selecione o grupo de recursos que criou para este arranque rápido.

   ![Introduza as informações básicas sobre a VM no painel do portal](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. Em **detalhes de instância**, nomeie o VM *qsVM*.
1. Mantenha as predefinições para **Região**, **Opções de disponibilidade**, **Imagem** e **Tamanho**.
1. Na **conta 'Administrador',** adicione *vMadmin* como nome de **utilizador** e introduza uma **palavra-passe** para o VM.
1. Em **Regras da porta de entrada**, selecione **Permitir portas selecionadas** e, em seguida, selecione **RDP (3389)** e **HTTP** na lista pendente.
1. Selecione **Rever + criar**.
1. Selecione **Criar**. A criação da VM nova irá demorar alguns minutos a concluir.

1. Assim que a sua implementação VM estiver completa, selecione **Ir para o recurso**.

Nesta fase, já criou uma máquina virtual nova e anexou um disco de dados. Agora, é preciso ligar à VM.

### <a name="connect-to-your-vm"></a>Ligar à VM

1. Selecione **Connect** na página de propriedades da máquina virtual.

   ![Ligar a uma VM do Azure a partir do portal](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. Na página **Connect to virtual machine,** mantenha as opções predefinidas para ligar por **endereço IP** através do número de **porta** *3389* e selecione **download ficheiro RDP**.
1. Abra o ficheiro RDP descarregado e selecione **Connect** quando solicitado.
1. Na janela **Segurança do Windows**, selecione **Mais escolhas** e **Utilizar uma conta diferente**. Digite o nome de utilizador como &lt; *localhost\username*, onde o nome&gt; de utilizador é o nome de utilizador vM que criou para a máquina virtual. Introduza a palavra-passe que criou para a máquina virtual e, em seguida, selecione **OK**.

   ![Mais opções](./media/storage-files-quick-create-use-windows/local-host2.png)

1. Poderá receber um aviso de certificado durante o processo de início de sessão. selecione **Sim** ou **Continuar** a criar a ligação.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Mapeie a partilha de ficheiros Azure para uma unidade windows

1. No portal Azure, navegue para o *fileshare qsfileshare* e selecione **Connect**.
1. Copie o conteúdo da segunda caixa e cole-a no Bloco de **Notas**.

   ![O caminho UNC a partir do painel Ligar dos Ficheiros do Azure](./media/storage-files-quick-create-use-windows/portal_netuse_connect2.png)

1. No VM, abra o **File Explorer** e selecione **Este PC** na janela. Esta seleção irá alterar os menus disponíveis na fita. No menu **computador,** selecione a **unidade de rede Map**.
1. Selecione a letra de unidade e introduza o caminho UNC. Se seguiu as sugestões de nomeação neste quickstart, * \\copie qsstorageacct.file.core.windows.net\qsfileshare* do **Notepad**.

   Certifique-se de que ambas as caixas de verificação estão verificadas.

   ![Instantâneo da caixa de diálogo “Mapear Unidade de Rede”](./media/storage-files-quick-create-use-windows/mountonwindows10.png)

1. Selecione **Concluir**.
1. Na caixa de diálogo **De Segurança do Windows:**

   - A partir do Bloco de Notas, copie o nome da conta de armazenamento preparado com O AZURE\ e cole-o na caixa de diálogo **Windows Security** como nome de utilizador. Se seguiu as sugestões de nomeação neste arranque rápido, copie *o AZURE\qsstorageacct*.
   - A partir do Bloco de Notas, copie a chave da conta de armazenamento e cole-a na caixa de diálogo **do Windows Security** como palavra-passe.

      ![O caminho UNC a partir do painel Ligar dos Ficheiros do Azure](./media/storage-files-quick-create-use-windows/portal_netuse_connect3.png)

## <a name="create-a-share-snapshot"></a>Criar um instantâneo de partilha

Agora que mapeou a unidade, pode criar uma foto.

1. No portal, navegue para a sua partilha de ficheiros e selecione **Criar instantâneo**.

   ![Criar um instantâneo](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. No VM, abra o *qstestfile.txt* e escreva "este ficheiro foi modificado" > Guardar e fechar o ficheiro.
1. Crie outro instantâneo.

## <a name="browse-a-share-snapshot"></a>Navegue por um instantâneo de partilha

1. Na sua partilha de ficheiros, selecione **Ver instantâneos**.
1. No painel de imagens de **partilha de ficheiros,** selecione o primeiro instantâneo da lista.

   ![Instantâneo selecionado na lista de selos temporais](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. No painel para esse instantâneo, selecione *qsTestFile.txt*.

## <a name="restore-from-a-snapshot"></a>Restaurar a partir de um instantâneo

1. A partir da lâmina de instantâneo de partilha de ficheiros, clique à direita no *qsTestFile*, e selecione o botão **Restaurar.**
1. **Selecione Sobreescrever o ficheiro original**.

   ![Descarregar e Restaurar botões](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. No VM, abra o ficheiro. A versão não modificada foi restaurada.

## <a name="delete-a-share-snapshot"></a>Eliminar um instantâneo de partilha

1. Na sua partilha de ficheiros, selecione **Ver instantâneos**.
1. No painel de **imagens de partilha de ficheiros,** selecione o último instantâneo da lista e clique em **Eliminar**.

   ![Eliminar botão](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Use uma imagem de partilha no Windows

Tal como acontece com as imagens VSS no local, pode ver as imagens da partilha de ficheiros Azure montadas utilizando o separador Versões Anteriores.

1. No File Explorer, localize a parte montada.

   ![Participação montada no Explorador de Ficheiros](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Selecione *qsTestFile.txt* e > clique direito e selecione **Propriedades** do menu.

   ![Clique com o botão direito do rato no menu para um diretório selecionado](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Selecione **Versões Anteriores** para ver a lista de instantâneos de partilha para este diretório.

1. **Selecione Abrir** para abrir o instantâneo.

   ![Separador Versões Anteriores](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Restaurar de uma versão anterior

1. Selecione **Restaurar**. Esta ação copia o conteúdo de todo o diretório recursivamente para a localização original no momento em que o instantâneo de partilha foi criado.

   ![Restaurar o botão na mensagem](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png) de aviso Nota: Se o seu ficheiro não tiver mudado, não verá uma versão anterior para esse ficheiro, uma vez que esse ficheiro é a mesma versão que o instantâneo. Isto é consistente com a forma como isto funciona num servidor de ficheiros windows.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Utilizar uma partilha de ficheiros do Azure com Windows](storage-how-to-use-files-windows.md)
