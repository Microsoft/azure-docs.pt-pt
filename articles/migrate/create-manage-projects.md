---
title: Criar e gerir projetos de migrações do Azure Migrate
description: Localizar, criar, gerir e apagar projetos em Azure Migrate.
ms.topic: how-to
ms.date: 02/17/2020
ms.openlocfilehash: a49595f0580e71048239d5c5d8f4d1a66e24fe6d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269643"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Criar e gerir projetos de migrações do Azure Migrate

Este artigo descreve como criar, gerir e eliminar projetos [de migração Azure.](migrate-services-overview.md)


## <a name="create-a-project-for-the-first-time"></a>Criar um projeto pela primeira vez

A primeira vez que configura o Azure Migrate, cria um projeto e adiciona uma ferramenta de avaliação ou migração. [Siga estas instruções](how-to-add-tool-first-time.md) para configurar pela primeira vez.

## <a name="create-additional-projects"></a>Criar projetos adicionais

Se já tem um projeto Azure Migrate e pretende criar um projeto adicional, faça o seguinte:  

1. No [portal Azure,](https://portal.azure.com)procure **azure migrate.**
2. No painel de instrumentos Emigração Azure > **Servers,** selecione **a alteração** no canto superior direito.

   ![Projeto Change Azure Migrate](./media/create-manage-projects/switch-project.png)

3. Para criar um novo projeto, selecione **clique aqui**.

   ![Criar um segundo projeto Azure Migrate](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Encontre um projeto

Encontre um projeto da seguinte forma:

1. No [portal Azure,](https://portal.azure.com)procure **azure migrate.**
2. No painel de instrumentos Emigração Azure > **Servers,** selecione **a alteração** no canto superior direito.

    ![Mude para um projeto existente do Azure Migrate](./media/create-manage-projects/switch-project.png)

3. Selecione a subscrição apropriada e o projeto Azure Migrate.


Se criou o projeto na [versão anterior](migrate-services-overview.md#azure-migrate-versions) do Azure Migrate, encontre-o da seguinte forma:

1. No [portal Azure,](https://portal.azure.com)procure **azure migrate.**
2. No painel de instrumentos Do Imigração Azure, se criou um projeto na versão anterior, aparece um banner que referencia projetos mais antigos. Selecione o banner.

    ![Acesso a projetos existentes](./media/create-manage-projects/access-existing-projects.png)

3. Reveja a lista de projetos antigos.


## <a name="delete-a-project"></a>Apagar um projeto

Eliminar da seguinte forma:

1. Abra o grupo de recursos Azure no qual o projeto foi criado.
2. Na página do grupo de recursos, selecione **Tipos ocultos .**
3. Selecione o projeto de migração que pretende eliminar e os seus recursos associados.
    - O tipo de recurso é **Microsoft.Migrate/migrateprojects**.
    - Se o grupo de recursos for exclusivamente utilizado pelo projeto Azure Migrate, pode eliminar todo o grupo de recursos.


Tenha em atenção que:

- Quando apaga, tanto o projeto como os metadados sobre máquinas descobertas são eliminados.
- Se estiver a usar a versão mais antiga do Azure Migrate, abra o grupo de recursos Azure no qual o projeto foi criado. Selecione o projeto de migração que pretende eliminar (o tipo de recurso é **projeto migração).**
- Se estiver a utilizar análises de dependência com um espaço de trabalho Azure Log Analytics:
    - Se tiver anexado um espaço de trabalho de Log Analytics à ferramenta de avaliação do servidor, o espaço de trabalho não é automaticamente eliminado. O mesmo espaço de trabalho log Analytics pode ser usado para vários cenários.
    - Se pretender eliminar o espaço de trabalho do Log Analytics, faça-o manualmente.

### <a name="delete-a-workspace-manually"></a>Eliminar manualmente um espaço de trabalho

1. Navegue no espaço de trabalho do Log Analytics ligado ao projeto.

    - Se ainda não apagou o projeto Azure Migrate, pode encontrar a ligação ao espaço de trabalho na **Essentials** > **Server Assessment**.
       ![](./media/create-manage-projects/loganalytics-workspace.png)do espaço de trabalho de LA.
       
    - Se já apagou o projeto Azure Migrate, selecione **Grupos de Recursos** no painel esquerdo do portal Azure e encontre o espaço de trabalho.
       
2. [Siga as instruções](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) para eliminar o espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

Adicione ferramentas de [avaliação](how-to-assess.md) ou [migração](how-to-migrate.md) aos projetos da Azure Migrate.
