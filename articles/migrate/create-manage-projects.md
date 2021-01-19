---
title: Criar e gerir projetos de migrações do Azure Migrate
description: Localizar, criar, gerir e apagar projetos em Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: de0c48bb775b96052fe16d60aa58049bfd58ca4d
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567776"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Criar e gerir projetos de migrações do Azure Migrate

Este artigo descreve como criar, gerir e eliminar projetos [da Azure Migrate.](migrate-services-overview.md) Se estiver a utilizar projetos Classic Azure Migrate, por favor, apague esses projetos e siga os passos para criar um novo projeto Azure Migrate. Não é possível atualizar projetos ou componentes da Classic Azure Migrate para o Azure Migrate.

Um projeto Azure Migrate é usado para armazenar metadados de descoberta, avaliação e migração recolhidos do ambiente que está a avaliar ou migrar. Num projeto é possível rastrear ativos descobertos, criar avaliações e orquestrar migrações para Azure.  

## <a name="verify-permissions"></a>Verificar permissões

Verifique se tem as permissões corretas para criar um projeto Azure Migrate:

1. No portal Azure, abra a subscrição relevante e selecione **Access control (IAM)**.
2. No **Acesso ao Cheque,** encontre a conta relevante e selecione-a ver permissões. Deve ter permissões *de Colaborador* ou *Proprietário.* 


## <a name="create-a-project-for-the-first-time"></a>Criar um projeto pela primeira vez

Crie um novo projeto Azure Migrate numa subscrição da Azure.

1. No portal Azure, procure *Azure Migrate.*
2. Em **Serviços**, **selecione Azure Migrate**.
3. Em **Descrição geral**, selecione **Avaliar e migrar servidores**.

    ![Opção em Visão Geral para avaliar e migrar servidores](./media/create-manage-projects/assess-migrate-servers.png)

4. Em **Servidores,** **selecione Criar projeto**.

    ![Botão para começar a criar projeto](./media/create-manage-projects/create-project.png)

5. No **projeto Create,** selecione o grupo de subscrição e recursos Azure. Crie um grupo de recursos se não tiver um.
6. Em **Detalhes do Projeto,** especifique o nome do projeto e a geografia em que pretende criar o projeto.
    - A geografia é usada apenas para armazenar os metadados recolhidos a partir de máquinas no local. Pode selecionar qualquer região-alvo para a migração. 
    - Reveja geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

8. Selecione **Criar**.

   ![Página para inserir definições de projeto](./media/create-manage-projects/project-details.png)


Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado.

## <a name="create-a-project-in-a-specific-region"></a>Criar um projeto numa região específica

No portal, pode selecionar a geografia em que pretende criar o projeto. Se pretender criar o projeto dentro de uma região específica de Azure, utilize o seguinte comando da API para criar o projeto.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Criar projetos adicionais

Se já tem um projeto Azure Migrate e pretende criar um projeto adicional, faça o seguinte:  

1. No [portal público Azure](https://portal.azure.com) ou [no Governo Azure,](https://portal.azure.us)procure **Azure Migrate**.
2. No painel Azure Migrate > **Servers**, selecione **a alteração** no canto superior direito.

   ![Mudar projeto Azure Migrate](./media/create-manage-projects/switch-project.png)

3. Para criar um novo projeto, **selecione clique aqui.**


## <a name="find-a-project"></a>Encontre um projeto

Encontre o projeto da seguinte forma:

1. No [portal Azure,](https://portal.azure.com)procure *Azure Migrate.*
2. No painel de instrumentos Azure Migrate > **Servers**, selecione **a alteração** no canto superior direito.

    ![Mude para um projeto Azure Migrate existente](./media/create-manage-projects/switch-project.png)

3. Selecione a subscrição adequada e o projeto Azure Migrate.


### <a name="find-a-legacy-project"></a>Encontre um projeto antigo

Se criou o projeto na [versão anterior](migrate-services-overview.md#azure-migrate-versions) do Azure Migrate, encontre-o da seguinte forma:

1. No [portal Azure,](https://portal.azure.com)procure *Azure Migrate.*
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

    - Se não eliminou o projeto Azure Migrate, pode encontrar a ligação ao espaço de trabalho na  >  **Avaliação do Servidor** Essencial.
       ![Espaço de trabalho de LA ](./media/create-manage-projects/loganalytics-workspace.png) .
       
    - Se já eliminou o projeto Azure Migrate, selecione **Grupos** de Recursos no painel esquerdo do portal Azure e encontre o espaço de trabalho.
       
2. [Siga as instruções](../azure-monitor/platform/delete-workspace.md) para eliminar o espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

Adicione ferramentas de [avaliação](how-to-assess.md) ou [migração](how-to-migrate.md) a projetos da Azure Migrate.
