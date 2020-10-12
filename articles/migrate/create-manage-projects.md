---
title: Criar e gerir projetos de migrações do Azure Migrate
description: Localizar, criar, gerir e apagar projetos em Azure Migrate.
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: d60868f9d0d4c60291cfd92a9e8d11fd3f9a42b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87071800"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Criar e gerir projetos de migrações do Azure Migrate

Este artigo descreve como criar, gerir e eliminar projetos [da Azure Migrate.](migrate-services-overview.md)


## <a name="create-a-project-for-the-first-time"></a>Criar um projeto pela primeira vez

A primeira vez que configura o Azure Migrate, cria um projeto e adiciona uma ferramenta de avaliação ou migração. [Siga estas instruções](how-to-add-tool-first-time.md) para configurar pela primeira vez.

## <a name="create-additional-projects"></a>Criar projetos adicionais

Se já tem um projeto Azure Migrate e pretende criar um projeto adicional, faça o seguinte:  

1. No [portal público Azure](https://portal.azure.com) ou [no Governo Azure,](https://portal.azure.us)procure **Azure Migrate**.
2. No painel Azure Migrate > **Servers**, selecione **a alteração** no canto superior direito.

   ![Mudar projeto Azure Migrate](./media/create-manage-projects/switch-project.png)

3. Para criar um novo projeto, **selecione clique aqui.**

   ![Criar um segundo projeto Azure Migrate](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Encontre um projeto

Encontre o projeto da seguinte forma:

1. No [portal Azure,](https://portal.azure.com)procure **Azure Migrate.**
2. No painel de instrumentos Azure Migrate > **Servers**, selecione **a alteração** no canto superior direito.

    ![Mude para um projeto Azure Migrate existente](./media/create-manage-projects/switch-project.png)

3. Selecione a subscrição adequada e o projeto Azure Migrate.


Se criou o projeto na [versão anterior](migrate-services-overview.md#azure-migrate-versions) do Azure Migrate, encontre-o da seguinte forma:

1. No [portal Azure,](https://portal.azure.com)procure **Azure Migrate.**
2. No painel Azure Migrate, se criou um projeto na versão anterior, aparece um banner que refere projetos mais antigos. Selecione o banner.

    ![Acesso aos projetos existentes](./media/create-manage-projects/access-existing-projects.png)

3. Reveja a lista de projetos antigos.


## <a name="delete-a-project"></a>Excluir um projeto

Eliminar da seguinte forma:

1. Abra o grupo de recursos Azure no qual o projeto foi criado.
2. Na página do grupo de recursos, selecione **Mostrar os tipos ocultos**.
3. Selecione o projeto migratório que pretende eliminar e os seus recursos associados.
    - O tipo de recurso é **Microsoft.Migrate/migrateprojects**.
    - Se o grupo de recursos for utilizado exclusivamente pelo projeto Azure Migrate, pode eliminar todo o grupo de recursos.

Tenha em atenção que:

- Quando elimina, tanto o projeto como os metadados sobre máquinas descobertas são eliminados.
- Se estiver a utilizar a versão mais antiga do Azure Migrate, abra o grupo de recursos Azure no qual o projeto foi criado. Selecione o projeto migratório que pretende eliminar (o tipo de recurso é **projeto migração).**
- Se estiver a utilizar a análise de dependência com um espaço de trabalho Azure Log Analytics:
    - Se tiver anexado um espaço de trabalho do Log Analytics à ferramenta de Avaliação do Servidor, o espaço de trabalho não é automaticamente eliminado. O mesmo espaço de trabalho log Analytics pode ser usado para vários cenários.
    - Se pretender eliminar o espaço de trabalho do Log Analytics, faça-o manualmente.
- A supressão do projeto é irreversível. Os objetos apagados não podem ser recuperados.

### <a name="delete-a-workspace-manually"></a>Excluir um espaço de trabalho manualmente

1. Navegue pelo espaço de trabalho Log Analytics ligado ao projeto.

    - Se não eliminou o projeto Azure Migrate, pode encontrar a **Essentials**ligação ao espaço de trabalho na  >  **Avaliação do Servidor**Essencial.
       ![Espaço de trabalho de LA ](./media/create-manage-projects/loganalytics-workspace.png) .
       
    - Se já eliminou o projeto Azure Migrate, selecione **Grupos** de Recursos no painel esquerdo do portal Azure e encontre o espaço de trabalho.
       
2. [Siga as instruções](../azure-monitor/platform/delete-workspace.md) para eliminar o espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

Adicione ferramentas de [avaliação](how-to-assess.md) ou [migração](how-to-migrate.md) a projetos da Azure Migrate.
