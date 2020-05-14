---
title: Ligue-se ao SQL Server ou à Base de Dados Azure SQL
description: Automatizar tarefas para bases de dados SQL nas instalações ou na nuvem utilizando aplicações lógicas azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/12/2020
tags: connectors
ms.openlocfilehash: c32e17aaf83c233ad77bbbf607c30cc526253352
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402586"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Automatizar fluxos de trabalho para o SQL Server ou para a Base de Dados Azure SQL utilizando aplicações da Lógica Azure

Este artigo mostra como pode aceder a dados na sua base de dados SQL a partir de dentro de uma aplicação lógica com o conector SQL Server. Dessa forma, pode automatizar tarefas, processos ou fluxos de trabalho que gerem os seus dados e recursos SQL através da criação de aplicações lógicas. O conector SQL Server funciona tanto para [o Servidor SQL no local](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) como para a base de dados [Azure SQL baseada na nuvem](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).

Pode criar aplicações lógicas que funcionam quando desencadeadas por eventos na sua base de dados SQL ou noutros sistemas, como o Dynamics CRM Online. As suas aplicações lógicas também podem obter, inserir e eliminar dados juntamente com consultas SQL e procedimentos armazenados. Por exemplo, pode construir uma aplicação lógica que verifica automaticamente novos registos no Dynamics CRM Online, adiciona itens à sua base de dados SQL para quaisquer novos registos e, em seguida, envia alertas de e-mail sobre os itens adicionados.

Se você é novo em aplicações lógicas, reveja [o que são As Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para informações técnicas específicas do conector, limitações e problemas conhecidos, consulte a página de referência do [conector SQL Server](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [base de dados do Servidor SQL](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) ou base de dados [Azure SQL](../sql-database/sql-database-get-started-portal.md)

  As suas tabelas devem ter dados para que a sua aplicação lógica possa devolver resultados ao ligar para as operações. Se criar uma Base de Dados SQL Azure, pode utilizar bases de dados de amostras, que estão incluídas.

* O nome do servidor SQL, o nome da base de dados, o nome do utilizador e a sua palavra-passe. Precisa destas credenciais para que possa autorizar a sua lógica de acesso ao seu servidor SQL.

  * Para o Servidor SQL, pode encontrar estes detalhes na cadeia de ligação:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Para a Base de Dados Azure SQL, pode encontrar estes detalhes na cadeia de ligação, ou no portal Azure sob as propriedades da Base de Dados SQL:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* A [porta de dados instalada no local](../logic-apps/logic-apps-gateway-install.md) num computador local e um recurso de gateway de dados [Azure criado no portal Azure](../logic-apps/logic-apps-gateway-connection.md) para estes cenários:

  * As suas aplicações lógicas não funcionam num ambiente de serviço de [integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

  * As suas aplicações lógicas *funcionam* num ambiente de serviço de integração, mas tem de utilizar a autenticação do Windows para a sua ligação ao SQL Server. Para este cenário, utilize a versão não ISE do conector SQL Server juntamente com o portal de dados porque a versão ISE não suporta a autenticação do Windows.

* A aplicação lógica onde precisa de acesso à sua base de dados SQL. Para iniciar a sua aplicação lógica com um gatilho SQL, precisa de uma [aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Adicione um gatilho SQL

Nas Aplicações Lógicas Do Azure, todas as aplicações lógicas devem começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que dispara quando um evento específico acontece ou quando uma condição específica é satisfeita. Cada vez que o gatilho dispara, o motor Logic Apps cria uma instância de aplicação lógica e começa a executar o fluxo de trabalho da sua aplicação lógica.

1. No portal Azure ou Visual Studio, crie uma aplicação lógica em branco, que abre o Logic Apps Designer. Este exemplo utiliza o portal Azure.

1. No designer, na caixa de pesquisa, introduza o "servidor sql" como filtro. A partir da lista de gatilhos, selecione o gatilho SQL que deseja.

   Este exemplo utiliza o **gatilho Quando um item é criado.**

   ![Selecione o gatilho "Quando um item é criado"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Se for solicitado a criar uma ligação, crie agora a [sua ligação SQL](#create-connection). Se a sua ligação existir, selecione um nome de **tabela**.

   ![Selecione a tabela que deseja](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Detete as propriedades **de Intervalo** e **Frequência,** que especificam com que frequência a sua aplicação lógica verifica a tabela.

   Este gatilho retorna apenas uma linha da mesa selecionada, nada mais. Para realizar outras tarefas, adicione outras ações que executam as tarefas que deseja. Por exemplo, para ver os dados nesta linha, pode adicionar outras ações que criam um ficheiro que inclui os campos da linha devolvida e, em seguida, enviar alertas de e-mail. Para conhecer outras ações disponíveis para este conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sql/).

1. Quando terminar, na barra de ferramentas de design, selecione **Save**.

   Este passo permite e publica automaticamente a sua aplicação lógica ao vivo no Azure.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Adicione uma ação SQL

Nas Aplicações Lógicas Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no seu fluxo de trabalho que segue um gatilho ou outra ação. Neste exemplo, a aplicação lógica começa com o [gatilho de Recorrência](../connectors/connectors-native-recurrence.md), e chama uma ação que obtém uma linha de uma base de dados SQL.

1. No portal Azure ou No Estúdio Visual, abra a sua aplicação lógica no Logic Apps Designer. Este exemplo utiliza o portal Azure.

1. Sob o gatilho ou ação onde pretende adicionar a ação SQL, selecione **Novo passo**.

   ![Adicione um novo passo à sua aplicação lógica](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Para adicionar uma ação entre os passos existentes, mova o rato sobre a seta de ligação. Selecione o sinal de mais ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Em **'Escolha uma ação**' na caixa de pesquisa' introduza o "servidor sql" como filtro. Na lista de ações, selecione a ação SQL que deseja.

   Este exemplo usa a ação get **row,** que obtém um único disco.

   ![Localizar e selecionar ação SQL "Get row"](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Esta ação retorna apenas a uma linha da mesa selecionada, nada mais. Para ver os dados nesta linha, poderá adicionar outras ações que criem um ficheiro que inclua os campos da linha devolvida e guarde esse ficheiro numa conta de armazenamento na nuvem. Para conhecer outras ações disponíveis para este conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sql/).

1. Se for solicitado a criar uma ligação, crie agora a [sua ligação SQL](#create-connection). Se a sua ligação existir, selecione um nome de **mesa**e introduza o ID da **linha** para o registo que desejar.

   ![Introduza o nome da mesa e o ID da linha](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. Quando terminar, na barra de ferramentas de design, selecione **Save**.

   Este passo permite e publica automaticamente a sua aplicação lógica ao vivo no Azure.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Ligar à base de dados

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Lidar com dados a granel

Às vezes, temos de trabalhar com conjuntos de resultados tão grandes que o conector não devolve todos os resultados ao mesmo tempo, ou quer um melhor controlo sobre o tamanho e estrutura para os seus conjuntos de resultados. Aqui estão algumas maneiras de lidar com conjuntos de resultados tão grandes:

* Para ajudá-lo a gerir os resultados como conjuntos mais pequenos, ligue a *paginação.* Para mais informações, consulte [Obter dados em massa, registos e itens utilizando paginação](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Crie um procedimento armazenado que organize os resultados da forma que quiser.

  Ao obter ou inserir várias linhas, a sua aplicação lógica pode iterar através destas linhas usando um [*loop até*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) dentro [destes limites](../logic-apps/logic-apps-limits-and-config.md). No entanto, quando a sua aplicação lógica tem de trabalhar com conjuntos de discos tão grandes, por exemplo, milhares ou milhões de filas, que pretende minimizar os custos resultantes de chamadas para a base de dados.

  Para organizar os resultados da forma que pretende, pode criar um [*procedimento armazenado*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) que funciona na sua instância SQL e utiliza a declaração SELECT - **ORDER BY.** Esta solução dá-lhe mais controlo sobre o tamanho e estrutura dos seus resultados. A sua aplicação lógica chama o procedimento armazenado utilizando a ação de **procedimento executada do** conector SQL Server.

  Para mais detalhes sobre a solução, consulte estes artigos:

  * [SQL Paginação para transferência de dados a granel com Aplicações Lógicas](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [Selecione - CLÁUSULA DE ENCOMENDA POR](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

### <a name="handle-dynamic-bulk-data"></a>Lidar com dados dinâmicos a granel

Por vezes, quando se faz uma chamada para um procedimento armazenado no conector SQL Server, a saída devolvida é dinâmica. Neste cenário, siga estes passos:

1. Open **Logic Apps Designer**.
1. Execute um teste da sua aplicação lógica para ver o formato de saída. Copie a sua saída de amostra.
1. No designer, no âmbito da ação onde chama o procedimento armazenado, selecione **Novo passo**.
1. Em **'Escolha uma ação',** procure e selecione a ação [**Parse JSON.**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action)
1. Na ação **Parse JSON,** selecione Utilize a carga útil da **amostra para gerar esquema**.
1. No Enter ou pasta uma janela de **carga útil JSON,** cola a saída da amostra e, em seguida, selecione **Done**.
1. Se tiver um erro que as Aplicações Lógicas não possam gerar um esquema, verifique se a sintaxe da sua amostra está corretamente formatada. Se ainda não conseguir gerar o esquema, introduza manualmente um na caixa **Deschema.**
1. Na barra de ferramentas de design, selecione **Guardar**.
1. Para aceder às propriedades de conteúdo jSON, utilize as fichas de dados que aparecem na lista de conteúdos dinâmicos sob a ação [ **Parse JSON** ](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action).

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter informações técnicas sobre os gatilhos, ações e limites deste conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sql/).

## <a name="next-steps"></a>Passos seguintes

* Conheça outros [conectores para aplicações lógicas azure](../connectors/apis-list.md)
