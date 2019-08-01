---
title: Início rápido do Azure-criar e usar um compartilhamento de arquivos do Azure em VMs do Windows | Microsoft Docs
description: Neste guia de início rápido, você configura um compartilhamento de arquivos do Azure no portal do Azure e o conecta a uma máquina virtual do Windows. Você se conecta ao compartilhamento de arquivos, carrega um arquivo no compartilhamento de arquivos. Em seguida, você tira um instantâneo do compartilhamento de arquivos, modifica o arquivo no compartilhamento de arquivos e restaura um instantâneo anterior do compartilhamento de arquivos.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 626a6a5e0449d267b67653c2f63303226dd0c99d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699741"
---
# <a name="quickstart-create-and-manage-azure-files-share-with-windows-virtual-machines"></a>Início rápido: Criar e gerenciar o compartilhamento de arquivos do Azure com máquinas virtuais do Windows

O artigo demonstra as etapas básicas para criar e usar um compartilhamento de arquivos do Azure. Neste guia de início rápido, a ênfase é configurar rapidamente um compartilhamento de arquivos do Azure para que você possa experimentar como o serviço funciona. Se você precisar de instruções mais detalhadas para criar e usar compartilhamentos de arquivos do Azure em seu próprio ambiente, consulte [usar um compartilhamento de arquivos do Azure com o Windows](storage-how-to-use-files-windows.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Preparar o ambiente

Neste guia de início rápido, você configura os seguintes itens:

- Uma conta de armazenamento do Azure e um compartilhamento de arquivos do Azure
- Uma VM do Windows Server 2016 datacenter

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para poder trabalhar com um compartilhamento de arquivos do Azure, você precisa criar uma conta de armazenamento do Azure. Uma conta de armazenamento de uso geral v2 fornece acesso a todos os serviços de armazenamento do Azure: BLOBs, arquivos, filas e tabelas. O início rápido cria uma conta de armazenamento v2 de uso geral, mas as etapas para criar qualquer tipo de conta de armazenamento são semelhantes. Uma conta de armazenamento pode conter um número ilimitado de partilhas. Uma partilha pode armazenar um número ilimitado de ficheiros, até aos limites de capacidade da conta de armazenamento.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

Depois, crie uma partilha de ficheiros.

1. Quando a implantação da conta de armazenamento do Azure for concluída, selecione **ir para o recurso**.
1. Selecione **arquivos** no painel da conta de armazenamento.

    ![Selecionar arquivos](./media/storage-files-quick-create-use-windows/click-files.png)

1. Selecione **+ compartilhamento de arquivos**.

    ![Selecione o botão Adicionar compartilhamento de arquivos](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Nomeie o novo compartilhamento de arquivos *qsfileshare* > Insira "1" para a **cota** > selecione **criar**. A cota pode ter no máximo 5 TiB, mas você só precisa de 1 GiB para este guia de início rápido.
1. Crie um novo arquivo txt chamado *qsTestFile* no computador local.
1. Selecione o novo compartilhamento de arquivos e, em seguida, no local do compartilhamento de arquivos, selecione **carregar**.

    ![Carregar um ficheiro](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Navegue até o local em que você criou o arquivo. txt > selecione *qsTestFile. txt* > selecione **carregar**.

Até agora, você criou uma conta de armazenamento do Azure e um compartilhamento de arquivos com um arquivo nele no Azure. Em seguida, você criará a VM do Azure com o Windows Server 2016 datacenter para representar o servidor local neste guia de início rápido.

### <a name="deploy-a-vm"></a>Implementar uma VM

1. Em seguida, expanda o menu no lado esquerdo do portal e escolha **Criar um recurso**, no canto superior esquerdo do portal do Azure.
1. Na caixa de pesquisa acima da lista de recursos do **Azure Marketplace**, procure e selecione **Windows Server 2016 Datacenter** e escolha **Criar**.
1. Na guia **noções básicas** , em **detalhes do projeto**, selecione o grupo de recursos que você criou para este guia de início rápido.

   ![Introduza as informações básicas sobre a VM no painel do portal](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. Em **detalhes da instância**, nomeie a VM *qsVM*.
1. Mantenha as predefinições para **Região**, **Opções de disponibilidade**, **Imagem** e **Tamanho**.
1. Em **conta de administrador**, adicione *VMadmin* como o **nome de usuário** e insira uma **senha** para a VM.
1. Em **Regras da porta de entrada**, selecione **Permitir portas selecionadas** e, em seguida, selecione **RDP (3389)** e **HTTP** na lista pendente.
1. Selecione **Rever + criar**.
1. Selecione **Criar**. A criação da VM nova irá demorar alguns minutos a concluir.

1. Quando a implantação da VM for concluída, selecione **ir para o recurso**.

Nesta fase, já criou uma máquina virtual nova e anexou um disco de dados. Agora, é preciso ligar à VM.

### <a name="connect-to-your-vm"></a>Ligar à VM

1. Selecione **conectar** na página de propriedades da máquina virtual.

   ![Ligar a uma VM do Azure a partir do portal](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. Na página **conectar à máquina virtual** , mantenha as opções padrão para se conectar por **endereço IP** no **número da porta** *3389* e selecione **baixar arquivo RDP**.
1. Abra o arquivo RDP baixado e selecione **conectar** quando solicitado.
1. Na janela **Segurança do Windows**, selecione **Mais escolhas** e **Utilizar uma conta diferente**. Digite o nome deusuário como localhost\username &lt;,&gt; em que username é o nome de usuário do administrador da VM que você criou para a máquina virtual. Insira a senha que você criou para a máquina virtual e, em seguida, selecione **OK**.

   ![Mais opções](./media/storage-files-quick-create-use-windows/local-host2.png)

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Selecione **Sim** ou **continuar** para criar a conexão.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Mapear o compartilhamento de arquivos do Azure para uma unidade do Windows

1. Na portal do Azure, navegue até o FileShare *qsfileshare* e selecione **conectar**.
1. Copie o conteúdo da segunda caixa e cole-o no **bloco de notas**.

   ![O caminho UNC a partir do painel Ligar dos Ficheiros do Azure](./media/storage-files-quick-create-use-windows/portal_netuse_connect2.png)

1. Na VM, abra o **Explorador de arquivos** e selecione **este PC** na janela. Essa seleção irá alterar os menus disponíveis na faixa de opção. No menu **computador** , selecione **Mapear unidade de rede**.
1. Selecione a letra de unidade e introduza o caminho UNC. Se você seguiu as sugestões de nomenclatura neste guia de início rápido, copie  *\\qsstorageacct. File. Core. Windows. net\qsfileshare* do **bloco de notas**.

   Certifique-se de que ambas as caixas de seleção estejam marcadas.

   ![Instantâneo da caixa de diálogo “Mapear Unidade de Rede”](./media/storage-files-quick-create-use-windows/mountonwindows10.png)

1. Selecione **Concluir**.
1. Na caixa de diálogo **segurança do Windows** :

   - No bloco de notas, copie o nome da conta de armazenamento que você precedeu com o AZURE \ e cole-o na caixa de diálogo **segurança do Windows** como o nome de usuário. Se você seguiu as sugestões de nomenclatura neste guia de início rápido, copie *AZURE\qsstorageacct*.
   - No bloco de notas, copie a chave da conta de armazenamento e cole-a na caixa de diálogo **segurança do Windows** como a senha.

      ![O caminho UNC a partir do painel Ligar dos Ficheiros do Azure](./media/storage-files-quick-create-use-windows/portal_netuse_connect3.png)

## <a name="create-a-share-snapshot"></a>Criar um instantâneo de compartilhamento

Agora que você mapeou a unidade, você pode criar um instantâneo.

1. No portal, navegue até o compartilhamento de arquivos e selecione **criar instantâneo**.

   ![Criar um instantâneo](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. Na VM, abra o *qstestfile. txt* e digite "este arquivo foi modificado" > salvar e fechar o arquivo.
1. Crie outro instantâneo.

## <a name="browse-a-share-snapshot"></a>Procurar um instantâneo de compartilhamento

1. Em seu compartilhamento de arquivos, selecione **Exibir instantâneos**.
1. No painel **instantâneos de compartilhamento de arquivos** , selecione o primeiro instantâneo na lista.

   ![Instantâneo selecionado na lista de carimbos de data/hora](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. No painel desse instantâneo, selecione *qsTestFile. txt*.

## <a name="restore-from-a-snapshot"></a>Restaurar de um instantâneo

1. Na folha instantâneo de compartilhamento de arquivo, clique com o botão direito do mouse em *qsTestFile*e selecione o botão **restaurar** .
1. Selecione **Substituir arquivo original**.

   ![Botões baixar e restaurar](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. Na VM, abra o arquivo. A versão não modificada foi restaurada.

## <a name="delete-a-share-snapshot"></a>Eliminar um instantâneo de partilha

1. Em seu compartilhamento de arquivos, selecione **Exibir instantâneos**.
1. No painel **instantâneos de compartilhamento de arquivos** , selecione o último instantâneo na lista e clique em **excluir**.

   ![Botão de eliminar](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Usar um instantâneo de compartilhamento no Windows

Assim como ocorre com instantâneos do VSS locais, você pode exibir os instantâneos do seu compartilhamento de arquivos do Azure montado usando a guia versões anteriores.

1. No explorador de arquivos, localize o compartilhamento montado.

   ![Compartilhamento montado no explorador de arquivos](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Selecione *qsTestFile. txt* e > clique com o botão direito do mouse e selecione **Propriedades** no menu.

   ![Clique com o botão direito do rato no menu para um diretório selecionado](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Selecione **Versões Anteriores** para ver a lista de instantâneos de partilha para este diretório.

1. Selecione **abrir** para abrir o instantâneo.

   ![Separador Versões Anteriores](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Restaurar de uma versão anterior

1. Selecione **restaurar**. Essa ação copia o conteúdo do diretório inteiro recursivamente para o local original no momento em que o instantâneo de compartilhamento foi criado.

   ![Botão restaurar na mensagem de aviso](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Utilizar uma partilha de ficheiros do Azure com Windows](storage-how-to-use-files-windows.md)
