---
title: Criar e gerenciar projetos de migrações para Azure
description: Localize, crie, gerencie e exclua projetos em migrações para Azure.
ms.topic: how-to
ms.date: 01/01/2020
ms.openlocfilehash: 548e51cbd215dd9b5e69c68b9bd8fc81625240a2
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725887"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Criar e gerenciar projetos de migrações para Azure

Este artigo descreve como criar, gerenciar e excluir projetos de [migrações para Azure](migrate-services-overview.md)


## <a name="create-a-project-for-the-first-time"></a>Criar um projeto pela primeira vez

Na primeira vez que você configurar as migrações para Azure, crie um projeto e adicione uma ferramenta de avaliação ou migração. [Siga estas instruções](how-to-add-tool-first-time.md) para configurar o pela primeira vez.

## <a name="create-additional-projects"></a>Criar projetos adicionais

Se você já tiver um projeto de migrações para Azure e desejar criar um projeto adicional, faça o seguinte:  

1. Na [portal do Azure](https://portal.azure.com), pesquise **migrações para Azure**.
2. No painel migrações para Azure > **servidores**, selecione **alterar** no canto superior direito.

   ![Alterar projeto de migrações para Azure](./media/create-manage-projects/switch-project.png)

3. Para criar um novo projeto, selecione **clique aqui**.

   ![Criar um segundo projeto de migrações para Azure](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Localizar um projeto

Localize um projeto da seguinte maneira:

1. Na [portal do Azure](https://portal.azure.com), pesquise **migrações para Azure**.
2. No painel migrações para Azure > **servidores**, selecione **alterar** no canto superior direito.

    ![Alternar para um projeto atual de migrações para Azure](./media/create-manage-projects/switch-project.png)

3. Selecione a assinatura apropriada e o projeto de migrações para Azure.


Se você criou o projeto na [versão anterior](migrate-services-overview.md#azure-migrate-versions) do migrações para Azure, encontre-o da seguinte maneira:

1. Na [portal do Azure](https://portal.azure.com), pesquise **migrações para Azure**.
2. No painel migrações para Azure, se você tiver criado um projeto na versão anterior, um banner referenciando projetos mais antigos será exibido. Selecione a faixa.

    ![Acessar projetos existentes](./media/create-manage-projects/access-existing-projects.png)

3. Examine a lista de projetos antigos.


## <a name="delete-a-project"></a>Excluir um projeto

Exclua da seguinte maneira:

1. Abra o grupo de recursos do Azure no qual o projeto foi criado.
2. Na página grupo de recursos, selecione **Mostrar tipos ocultos**.
3. Selecione o projeto de migração que você deseja excluir e seus recursos associados.
    - O tipo de recurso é **Microsoft. Migrate/migrateprojects**.
    - Se o grupo de recursos for usado exclusivamente pelo projeto de migrações para Azure, você poderá excluir o grupo de recursos inteiro.


Tenha em atenção que:

- Quando você exclui, o projeto e os metadados sobre computadores descobertos são excluídos.
- Se você estiver usando a versão mais antiga do migrações para Azure, abra o grupo de recursos do Azure no qual o projeto foi criado. Selecione o projeto de migração que você deseja excluir (o tipo de recurso é **projeto de migração**).
- Se você estiver usando a análise de dependência com um espaço de trabalho do Azure Log Analytics:
    - Se você anexou um espaço de trabalho Log Analytics à ferramenta de avaliação do servidor, o espaço de trabalho não será excluído automaticamente. O mesmo espaço de trabalho Log Analytics pode ser usado para vários cenários.
    - Se você quiser excluir o espaço de trabalho Log Analytics, faça isso manualmente.

### <a name="delete-a-workspace-manually"></a>Excluir um espaço de trabalho manualmente

1. Navegue até o espaço de trabalho Log Analytics anexado ao projeto.

    - Se você não tiver excluído o projeto de migrações para Azure, poderá encontrar o link para o espaço de trabalho na avaliação do **Essentials** > **Server**.
       Espaço de trabalho ![LA](./media/create-manage-projects/loganalytics-workspace.png).
       
    - Se você já tiver excluído o projeto de migrações para Azure, selecione **grupos de recursos** no painel esquerdo da portal do Azure e localize o espaço de trabalho.
       
2. [Siga as instruções](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) para excluir o espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

Adicione ferramentas de [avaliação](how-to-assess.md) ou [migração](how-to-migrate.md) para projetos de migrações para Azure.
