---
title: incluir ficheiro
description: incluir ficheiro
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 06/27/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: f808158c959894e757adaa73a81e3bf57f475da0
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020945"
---
## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-subscription"></a>Subscrição do Azure
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser membro das funções *contribuidor* ou *proprietário* ou *administrador* da subscrição do Azure. Para exibir as permissões que você tem na assinatura, vá para o [portal do Azure](https://portal.azure.com), selecione seu nome de usuário no canto superior direito, selecione **mais opções** (...) e, em seguida, selecione **minhas permissões**. Se tiver acesso a várias subscrições, selecione a subscrição apropriada.

Para criar e gerir recursos subordinados do Data Factory - incluindo conjuntos de dados, serviços ligados, pipelines, acionadores e runtimes de integração - os requisitos seguintes são aplicáveis:

- Para criar e gerir recursos subordinados no portal do Azure, tem de pertencer à função **Contribuidor do Data Factory** ao nível do grupo de recursos ou superior.
- Para criar e gerir recursos subordinados com o PowerShell ou o SDK, a função **contribuidor** ao nível do grupo de recursos ou superior é suficiente.

Para obter instruções de exemplo sobre como adicionar um utilizador a uma função, veja o artigo [Adicionar funções](../articles/cost-management-billing/manage/add-change-subscription-administrator.md).

Para obter mais informações, veja os artigos seguintes:

- [Função de Contribuidor do Data Factory](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Funções e permissões do Azure Data Factory](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Conta de armazenamento do Azure
Neste início rápido, vai utilizar uma conta de armazenamento do Azure para fins gerais (especificamente o armazenamento de Blobs) como arquivo de dados de *origem* e de *destino*. Se não tiver uma conta de armazenamento do Azure para fins gerais, veja [Criar uma conta de armazenamento](../articles/storage/common/storage-account-create.md) para criar uma. 

#### <a name="get-the-storage-account-name"></a>Obter o nome da conta de armazenamento
Você precisará do nome da sua conta de armazenamento do Azure para este guia de início rápido. O procedimento a seguir fornece as etapas para obter o nome da sua conta de armazenamento: 

1. Em um navegador da Web, vá para a [portal do Azure](https://portal.azure.com) e entre usando seu nome de usuário e senha do Azure.
2. No menu portal do Azure, selecione **todos os serviços**e, em seguida, selecione **armazenamento** > **contas de armazenamento**. Você também pode pesquisar e selecionar *contas de armazenamento* em qualquer página.
3. Na página **contas de armazenamento** , filtre sua conta de armazenamento (se necessário) e, em seguida, selecione sua conta de armazenamento. 

Você também pode pesquisar e selecionar *contas de armazenamento* em qualquer página.

#### <a name="create-a-blob-container"></a>Criar um contentor de blobs
Nesta secção, vai criar um contentor de blobs com o nome **adftutorial** no armazenamento de Blobs do Azure.

1. Na página conta de armazenamento, selecione **visão geral** > **BLOBs**.
2. Na barra de ferramentas da página *\<nome da conta >*  - **BLOBs** , selecione **contêiner**.
3. Na caixa de diálogo **Novo contentor**, introduza **adftutorial** para o nome e selecione **OK**. O *nome da conta de\<* página - **BLOBs** é atualizado para incluir **adftutorial** na lista de contêineres.

   ![Lista de contêineres](media/data-factory-quickstart-prerequisites/list-of-containers.png)

#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Adicionar uma pasta de entrada e um arquivo para o contêiner de BLOB
Nesta seção, você cria uma pasta chamada **entrada** no contêiner que você acabou de criar e, em seguida, carrega um arquivo de exemplo na pasta de entrada. Antes de começar, abra um editor de texto como o **bloco de notas**e crie um arquivo chamado **EMP. txt** com o seguinte conteúdo:

```emp.txt
John, Doe
Jane, Doe
```

Salve o arquivo na pasta **C:\ADFv2QuickStartPSH** (Se a pasta ainda não existir, crie-a.) Em seguida, retorne ao portal do Azure e siga estas etapas:

1. Na página *\<nome da conta >*  - **BLOBs** em que você parou, selecione **adftutorial** na lista atualizada de contêineres.

   1. Se você fechou a janela ou entrou em outra página, entre na [portal do Azure](https://portal.azure.com) novamente.
   1. No menu portal do Azure, selecione **todos os serviços**e, em seguida, selecione **armazenamento** > **contas de armazenamento**. Você também pode pesquisar e selecionar *contas de armazenamento* em qualquer página.
   1. Selecione sua conta de armazenamento e, em seguida, selecione **blobs** > **adftutorial**.

2. Na barra de ferramentas da página de contêiner do **adftutorial** , selecione **carregar**.
3. Na página **carregar blob** , selecione a caixa **arquivos** e, em seguida, navegue até e selecione o arquivo **EMP. txt** .
4. Expanda o título **avançado** . A página agora é exibida como mostrado:

   ![Selecionar a ligação Avançadas](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
5. Na caixa **carregar na pasta** , insira **entrada**.
6. Selecione o botão **Carregar**. Deverá ver o ficheiro **emp.txt** e o estado do carregamento na lista.
7. Selecione o ícone **fechar** (um **X**) para fechar a página **carregar blob** .

Mantenha a página contêiner **adftutorial** aberta. Vai utilizá-la para verificar a saída no final deste início rápido.