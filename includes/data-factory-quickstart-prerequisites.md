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
ms.openlocfilehash: 4d77cb8128105a40143a40e48ebe450115f7cf1e
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164172"
---
## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-subscription"></a>Subscrição do Azure
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser membro das funções *contribuidor* ou *proprietário* ou *administrador* da subscrição do Azure. Para ver as permissões que tem na subscrição, vá ao [portal Azure,](https://portal.azure.com)selecione o seu nome de utilizador no canto superior direito, selecione **" ícone**para mais opções e, em seguida, selecione **Minhas permissões**. Se tiver acesso a várias subscrições, selecione a subscrição apropriada.

Para criar e gerir recursos subordinados do Data Factory - incluindo conjuntos de dados, serviços ligados, pipelines, acionadores e runtimes de integração - os requisitos seguintes são aplicáveis:

- Para criar e gerir recursos subordinados no portal do Azure, tem de pertencer à função **Contribuidor do Data Factory** ao nível do grupo de recursos ou superior.
- Para criar e gerir recursos subordinados com o PowerShell ou o SDK, a função **contribuidor** ao nível do grupo de recursos ou superior é suficiente.

Para obter instruções de exemplo sobre como adicionar um utilizador a uma função, veja o artigo [Adicionar funções](../articles/cost-management-billing/manage/add-change-subscription-administrator.md).

Para obter mais informações, veja os artigos seguintes:

- [Função de Contribuidor do Data Factory](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Funções e permissões do Azure Data Factory](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Conta de armazenamento do Azure
Você usa uma conta de armazenamento azure de uso geral (especificamente armazenamento Blob) como lojas de dados de *origem* e *destino* neste arranque rápido. Se não tiver uma conta de Armazenamento Azure de uso geral, consulte [Criar uma conta](../articles/storage/common/storage-account-create.md) de armazenamento para criar uma. 

#### <a name="get-the-storage-account-name"></a>Obtenha o nome da conta de armazenamento
Você precisará do nome da sua conta de Armazenamento Azure para este arranque rápido. O seguinte procedimento fornece medidas para obter o nome da sua conta de armazenamento: 

1. Num navegador web, aceda ao [portal Azure](https://portal.azure.com) e inscreva-se usando o seu nome de utilizador E senha Azure.
2. A partir do menu do portal Azure, selecione **Todos os serviços,** em seguida, selecione as contas **de armazenamento** > **armazenamento.** Também pode pesquisar e selecionar *contas de Armazenamento* a partir de qualquer página.
3. Na página de **contas de Armazenamento,** filtre para a sua conta de armazenamento (se necessário) e, em seguida, selecione a sua conta de armazenamento. 

Também pode pesquisar e selecionar *contas de Armazenamento* a partir de qualquer página.

#### <a name="create-a-blob-container"></a>Criar um contentor de blobs
Nesta secção, vai criar um contentor de blobs com o nome **adftutorial** no armazenamento de Blobs do Azure.

1. Na página da conta de armazenamento, selecione **Recipientes**de > de **visão geral** .
2. Na\<Nome da *Conta>*  - barra de ferramentas da página **contentores,** selecione **Recipiente**.
3. Na caixa de diálogo **Novo contentor**, introduza **adftutorial** para o nome e selecione **OK**. A página *\<Name>*  - **Containers** é atualizada para incluir o **adftutorial** na lista de contentores.

   ![Lista de contentores](media/data-factory-quickstart-prerequisites/list-of-containers.png)

#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Adicione uma pasta de entrada e arquivo para o recipiente blob
Nesta secção, cria-se uma pasta chamada **entrada** no recipiente que acaba de criar e, em seguida, envia um ficheiro de amostra para a pasta de entrada. Antes de começar, abra um editor de texto como **o Notepad**e crie um ficheiro chamado **emp.txt** com o seguinte conteúdo:

```emp.txt
John, Doe
Jane, Doe
```

Guarde o ficheiro na pasta **C:\ADFv2QuickStartPSH.** (Se a pasta já não existir, crie-a.) Em seguida, volte ao portal Azure e siga estes passos:

1. Na página *\<Name>*  - **Containers** onde deixou de estar, selecione **adftutorial** da lista atualizada de contentores.

   1. Se fechar a janela ou for para outra página, inscreva-se no [portal Azure](https://portal.azure.com) novamente.
   1. A partir do menu do portal Azure, selecione **Todos os serviços,** em seguida, selecione as contas **de armazenamento** > **armazenamento.** Também pode pesquisar e selecionar *contas de Armazenamento* a partir de qualquer página.
   1. Selecione a sua conta de armazenamento e, em seguida, selecione **Recipientes** > **adftutorial**.

2. Na barra de ferramentas da página do recipiente **adftutorial,** **selecione Upload**.
3. Na página **'Upload',** selecione a caixa **De Ficheiros** e, em seguida, navegue e selecione o ficheiro **emp.txt.**
4. Expanda o título **avançado.** A página agora mostra como mostrado:

   ![Selecionar a ligação Avançadas](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
5. Na caixa **de upload para pasta,** introduza **a entrada**.
6. Selecione o botão **Carregar**. Deverá ver o ficheiro **emp.txt** e o estado do carregamento na lista.
7. Selecione o ícone **Fechar** (um **X)** para fechar a página **de 'Upload'.**

Mantenha a página do recipiente **adftutorial** aberta. Vai utilizá-la para verificar a saída no final deste início rápido.