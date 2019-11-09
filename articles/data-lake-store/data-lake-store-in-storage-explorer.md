---
title: Gerenciar recursos de Data Lake Storage Gen1-Gerenciador de Armazenamento do Azure
description: Saiba como acessar e gerenciar seus dados e recursos de Azure Data Lake Storage Gen1 no Gerenciador de Armazenamento do Azure
author: jejiang
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: a5117f72f933e1e48dc471a75624a8d3921f55af
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832262"
---
# <a name="manage-azure-data-lake-storage-gen1-resources-by-using-storage-explorer"></a>Gerenciar Azure Data Lake Storage Gen1 recursos usando Gerenciador de Armazenamento

[Azure data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários. Pode obter acesso aos dados a partir de qualquer lugar através de HTTP ou HTTPS. Data Lake Storage Gen1 no Gerenciador de Armazenamento do Azure permite que você acesse e gerencie Data Lake Storage Gen1 dados e recursos, juntamente com outras entidades do Azure, como BLOBs e filas. Agora, pode utilizar a mesma ferramenta para gerir as diferentes entidades do Azure num único local.

Outra vantagem é que você não precisa ter permissão de assinatura para gerenciar Data Lake Storage Gen1 dados. No Gerenciador de Armazenamento, você pode anexar o caminho de Data Lake Storage Gen1 ao nó **local e anexado** , desde que alguém conceda a permissão.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo, os seguintes requisitos são necessários:

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial).
* Uma conta de Data Lake Storage Gen1. Para obter instruções sobre como criar uma, consulte Introdução [ao Azure data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="install-storage-explorer"></a>Instalar o Explorador de Armazenamento

Instale os bits mais recentes do Explorador de Armazenamento do Azure a partir da [página Web de produtos](https://azure.microsoft.com/features/storage-explorer/). A instalação suporta versões do Windows, Linux e Mac.

## <a name="connect-to-an-azure-subscription"></a>Ligar a uma subscrição do Azure

1. No Explorador de Armazenamento, selecione o ícone de plug-in à esquerda.

   ![Ícone de plug-in](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)

1. Selecione **Adicionar uma Conta do Azure** e selecione **Iniciar Sessão**.

   ![Caixa de diálogo “Ligar ao Armazenamento do Azure”](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

1. Na caixa de diálogo **Iniciar sessão na sua conta**, introduza as suas credenciais do Azure.

    ![Caixa de Diálogo do início de Sessão do Azure](./media/data-lake-store-in-storage-explorer/sign-in.png)

1. Selecione a sua subscrição na lista e selecione **Aplicar**.

    ![Informações de subscrição e o botão "Aplicar"](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    O painel **EXPLORADOR** é atualizado e apresenta as contas na subscrição selecionada.

    ![Lista de contas](./media/data-lake-store-in-storage-explorer/account-list.png)

Você se conectou com êxito Data Lake Storage Gen1 à sua assinatura do Azure.

## <a name="connect-to-data-lake-storage-gen1"></a>Conectar-se ao Data Lake Storage Gen1

Pode aceder a recursos que não existem na sua subscrição, se alguém lhe fornecer o URI dos recursos. Em seguida, você pode se conectar a Data Lake Storage Gen1 usando o URI depois de entrar.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda **Local e Ligado**.
3. Clique com botão direito do rato em **Data Lake Store** e selecione **Ligar ao Data Lake Store**.

      !["Ligar ao Data Lake Store" no menu de atalho](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Introduza o URI. A ferramenta procura a localização do URL que acabou de introduzir.

      ![Caixa de diálogo "Ligar ao Data Lake Store", com a caixa de texto para introduzir o URI](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Resultado da conexão com Data Lake Storage Gen1](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-a-data-lake-storage-gen1-accounts-contents"></a>Exibir o conteúdo de uma conta de Data Lake Storage Gen1

Os recursos de uma conta Data Lake Storage Gen1 contêm pastas e arquivos.

As etapas a seguir ilustram como exibir o conteúdo de uma conta de Data Lake Storage Gen1 no Gerenciador de Armazenamento:

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a assinatura que contém a conta de Data Lake Storage Gen1 que você deseja exibir.
3. Expanda **Data Lake Store**.
4. Clique com o botão direito do mouse no nó da conta Data Lake Storage Gen1 que você deseja exibir e, em seguida, selecione **abrir**. Você também pode clicar duas vezes na conta Data Lake Storage Gen1 para abri-la.

   O painel principal exibe o conteúdo da conta Data Lake Storage Gen1.

   ![Painel principal com uma lista de pastas](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png)

## <a name="manage-resources-in-data-lake-storage-gen1"></a>Gerenciar recursos no Data Lake Storage Gen1

Você pode gerenciar Data Lake Storage Gen1 recursos executando as seguintes operações:

* Navegue pelos recursos de Data Lake Storage Gen1 em várias contas de Data Lake Storage Gen1.  
* Use uma cadeia de conexão para se conectar e gerenciar Data Lake Storage Gen1 diretamente.
* Exiba Data Lake Storage Gen1 recursos compartilhados por outras pessoas por meio de uma ACL em **local e anexado**.
* Execute as operações CRUD de pasta/ficheiro: suporta pastas recursivas e a seleção de vários ficheiros.
* Arraste, largue e adicione uma pasta para aceder rapidamente a localizações recentes. Esta operação reflete a experiência de ambiente de trabalho do Explorador de Ficheiros.
* Copie e abra um Data Lake Storage Gen1 hiperlink no Gerenciador de Armazenamento com um clique.
* Apresente o Registo de Atividades no painel na parte inferior direita para ver o estado das atividades.
* Apresente as estatísticas de pastas e propriedades de ficheiros.

## <a name="manage-resources-in-azure-storage-explorer"></a>Gerir recursos do Explorador de Armazenamento do Azure

Depois de criar uma conta de Data Lake Storage Gen1, você pode:

* Carregar pastas e ficheiros, transferir ficheiros e pastas e abrir recursos no seu computador local.
* Afixar ao **Acesso Rápido**, criar uma nova pasta, copiar um URL e selecionar tudo.
* Copiar e colar, mudar o nome, eliminar, obter estatísticas de pastas e atualizar.

Os itens a seguir ilustram como gerenciar recursos em uma conta de Data Lake Storage Gen1. Siga os passos para a tarefa que pretende efetuar.

### <a name="upload-files"></a>Carregar ficheiros

1. Na barra de ferramentas do painel principal, selecione **Carregar** e, em seguida, selecione **Carregar Ficheiros**, no menu pendente.

   ![Item de menu “Carregar Ficheiros”](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png)

2. Na caixa de diálogo **Selecionar ficheiros para carregar**, selecione os ficheiros que pretende carregar.

   ![Caixa de diálogo para carregar ficheiros](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

3. Selecione **Abrir** para iniciar o carregamento.

### <a name="upload-a-folder"></a>Carregar uma pasta

1. Na barra de ferramentas do painel principal, selecione **Carregar** e, em seguida, selecione **Carregar Pasta**, no menu pendente.

   ![Item de menu “Carregar Pasta”](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png)

2. Na caixa de diálogo **Selecionar pasta para carregar**, selecione a pasta que pretende carregar. Em seguida, clique em **Selecionar Pasta**.

   ![Caixa de diálogo para carregar pastas](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)

   O carregamento começa.

   ![Caixa de diálogo com o carregamento em curso](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png)

> [!NOTE]
> Pode arrastar diretamente as pastas e os ficheiros do computador local para iniciar o carregamento.

### <a name="download-folders-or-files-to-your-local-computer"></a>Transferir pastas ou ficheiros para o computador local

1. Selecione as pastas ou os ficheiros que pretende transferir.
2. Na barra de ferramentas do painel principal, selecione **Download (Transferir)** .
3. Na caixa de diálogo **Selecionar uma pasta na qual guardar os ficheiros transferidos**, especifique a localização e o nome.
4. Selecione **Guardar**.

### <a name="open-a-folder-or-file-from-your-local-computer"></a>Abrir o ficheiro ou pasta do computador local

1. Selecione a pasta ou o ficheiro que pretende abrir.
2. Na barra de ferramentas do painel principal, selecione **Open (Abrir)** . Ou clique com botão direito do rato na pasta ou no ficheiro e, em seguida, selecione **Abrir** no menu de atalho.

O ficheiro é transferido e aberto com a aplicação associada ao respetivo tipo de ficheiro subjacente. Ou a pasta é aberta no painel principal.

![Ficheiro aberto](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png)

### <a name="copy-folders-or-files-to-the-clipboard"></a>Copiar pastas ou ficheiros para a área de transferência

1. Selecione as pastas ou os ficheiros que pretende copiar.
2. Na barra de ferramentas do painel principal, selecione **Copy (Copiar)** . Ou clique com botão direito do rato na pasta ou no ficheiro e, em seguida, selecione **Copiar** no menu de atalho.
3. No painel esquerdo, navegue para outra conta de Data Lake Storage Gen1 e clique duas vezes nela para exibi-la no painel principal.
4. Na barra de ferramentas do painel principal, selecione **Colar** para criar uma cópia do ficheiro. Ou selecione **Colar** no menu de atalho de destino.

![Seleções para copiar uma pasta](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

> [!NOTE]
> As operações de copiar/colar em tipos de armazenamento não são suportadas. Você pode copiar Data Lake Storage Gen1 pastas ou arquivos e colá-los em outra conta do Data Lake Storage Gen1. Mas você *não pode* copiar data Lake Storage Gen1 pastas ou arquivos e colá-los no armazenamento de BLOBs do Azure ou o contrário.
>
> A operação de copiar/colar funciona ao transferir as pastas ou os ficheiros para o computador local e, em seguida, carregá-los para o destino. A ferramenta *não* executa a ação no back-end. A operação de copiar/colar ficheiros grandes é lenta. A otimização de copiar/mover ficheiros de elevado desempenho está em curso.

### <a name="delete-folders-or-files"></a>Eliminar pastas ou ficheiros

1. Selecione as pastas ou os ficheiros que pretende eliminar.
2. Na barra de ferramentas do painel principal, selecione **Delete (Eliminar)** . Ou clique com botão direito do rato na pasta ou no ficheiro e, em seguida, selecione **Eliminar** no menu de atalho.
3. Selecione **Sim** na caixa de diálogo de confirmação.

![Seleções para eliminar uma pasta](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

### <a name="pin-to-quick-access"></a>Afixar para Acesso Rápido

1. Selecione a pasta que pretende afixar.
2. Na barra de ferramentas do painel principal, selecione **Afixar para Acesso rápido**.

   No painel esquerdo, a pasta selecionada foi adicionada ao nó **Acesso Rápido**.

   ![Seleções para afixar uma pasta para "Acesso Rápido"](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

Depois de afixar uma pasta ao nó **Acesso Rápido**, pode facilmente aceder aos recursos.

### <a name="use-deep-links"></a>Utilizar ligações avançadas

Se tiver um URL, pode introduzir o URL no caminho de endereço do Explorador de Ficheiros ou num browser. O Storage Explorer.exe é executado automaticamente para ir para a localização do URL.

![Ligação avançada no Explorador de Ficheiros](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)

## <a name="next-steps"></a>Passos seguintes

* Vejas os [mais recentes vídeos e notas de versão do Explorador de Armazenamento](https://www.storageexplorer.com).
* Saiba como [gerir o Azure Cosmos DB no Explorador de Armazenamento do Azure](https://docs.microsoft.com/azure/cosmos-db/storage-explorer).
* [Introdução ao Explorador de Armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).
* [Introdução ao Azure data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).
* Veja um [vídeo do YouTube sobre como utilizar o Azure Cosmos DB no Explorador de Armazenamento do Azure](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
