---
title: Ligue ao SQL Server ou à Base de Dados Azure SQL
description: Automatizar tarefas para bases de dados SQL nas instalações ou na nuvem utilizando aplicações lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/12/2020
tags: connectors
ms.openlocfilehash: f63553ced8484b3ce328fb9537d5831ae1e27fe8
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84191478"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Automatizar fluxos de trabalho para SQL Server ou Azure SQL Database utilizando apps Azure Logic

Este artigo mostra como pode aceder aos dados na sua base de dados SQL a partir de uma aplicação lógica com o conector SQL Server. Desta forma, pode automatizar tarefas, processos ou fluxos de trabalho que gerem os seus dados e recursos SQL criando aplicações lógicas. O conector SQL Server funciona para [o SQL Server,](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) bem como [para a Base de Dados SQL Azure](../azure-sql/database/sql-database-paas-overview.md) e [para a Azure SQL Gerenciada .](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)

Pode criar aplicações lógicas que são executadas quando desencadeadas por eventos na sua base de dados SQL ou em outros sistemas, como o Dynamics CRM Online. As suas aplicações lógicas também podem obter, inserir e eliminar dados juntamente com consultas SQL executando e procedimentos armazenados. Por exemplo, pode construir uma aplicação lógica que verifica automaticamente novos registos no Dynamics CRM Online, adiciona itens à sua base de dados SQL para quaisquer novos registos e, em seguida, envia alertas de e-mail sobre os itens adicionados.

Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para obter informações técnicas específicas do conector, limitações e problemas conhecidos, consulte a página de referência do [conector SQL Server](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [base de dados sql server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) ou base de [dados Azure SQL](../azure-sql/database/single-database-create-quickstart.md)

  As suas tabelas devem ter dados para que a sua aplicação lógica possa devolver resultados ao ligar para as operações. Se criar uma Base de Dados Azure SQL, pode utilizar bases de dados de amostras, que estão incluídas.

* O nome do servidor SQL, o nome da base de dados, o nome de utilizador e a sua palavra-passe. Precisa destas credenciais para que possa autorizar a sua lógica de acesso ao seu servidor SQL.

  * Para o SQL Server, pode encontrar estes detalhes na cadeia de ligação:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Para a Base de Dados Azure SQL, pode encontrar estes detalhes na cadeia de ligação ou no portal Azure sob as propriedades da Base de Dados SQL:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* O [portal de dados no local](../logic-apps/logic-apps-gateway-install.md) instalado num computador local e um recurso de gateway de [dados Azure criado no portal Azure](../logic-apps/logic-apps-gateway-connection.md) para estes cenários:

  * As suas aplicações lógicas não funcionam num [ambiente de serviço de integração (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

  * As suas *aplicações* lógicas funcionam num ambiente de serviço de integração, mas tem de utilizar a autenticação do Windows para a sua ligação ao SQL Server. Para este cenário, utilize a versão não ISE do conector SQL Server juntamente com o gateway de dados porque a versão ISE não suporta a autenticação do Windows.

* A aplicação lógica onde precisa de acesso à sua base de dados SQL. Para iniciar a sua aplicação lógica com um gatilho SQL, precisa de uma [aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Adicione um gatilho SQL

Nas Azure Logic Apps, todas as aplicações lógicas devem começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que dispara quando um evento específico acontece ou quando uma condição específica é cumprida. Cada vez que o gatilho dispara, o motor Logic Apps cria uma instância de aplicação lógica e começa a executar o fluxo de trabalho da sua aplicação lógica.

1. No portal Azure ou Visual Studio, crie uma aplicação lógica em branco, que abre o Logic Apps Designer. Este exemplo utiliza o portal Azure.

1. No designer, na caixa de pesquisa, introduza o "sql server" como filtro. A partir da lista de gatilhos, selecione o gatilho SQL que deseja.

   Este exemplo utiliza o gatilho **Quando um item é criado.**

   ![Selecione o gatilho "Quando um item é criado"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Se for solicitado a criar uma ligação, [crie já a sua ligação SQL](#create-connection). Se a sua ligação existir, selecione um **nome de tabela**.

   ![Selecione a tabela que deseja](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Desaprova as propriedades **de Intervalo** e **Frequência,** que especificam com que frequência a sua aplicação lógica verifica a tabela.

   Este gatilho retorna apenas uma linha da mesa selecionada, nada mais. Para executar outras tarefas, adicione outras ações que executem as tarefas que deseja. Por exemplo, para ver os dados nesta linha, pode adicionar outras ações que criam um ficheiro que inclui os campos da linha retornado e, em seguida, enviar alertas de e-mail. Para saber mais sobre outras ações disponíveis para este conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sql/).

1. Quando terminar, na barra de ferramentas do designer, **selecione Save**.

   Este passo ativa e publica automaticamente a sua aplicação lógica ao vivo no Azure.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Adicione uma ação SQL

Nas Azure Logic Apps, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no seu fluxo de trabalho que segue um gatilho ou outra ação. Neste exemplo, a aplicação lógica começa com o [gatilho de Recorrência](../connectors/connectors-native-recurrence.md), e chama uma ação que recebe uma linha a partir de uma base de dados SQL.

1. No portal Azure ou Visual Studio, abra a sua aplicação lógica no Logic Apps Designer. Este exemplo utiliza o portal Azure.

1. Sob o gatilho ou ação onde pretende adicionar a ação SQL, selecione **Novo passo**.

   ![Adicione um novo passo na sua aplicação lógica](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Para adicionar uma ação entre os passos existentes, mova o rato sobre a seta de ligação. Selecione o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Em **Escolha uma ação,** na caixa de pesquisa, introduza o "sql server" como filtro. Na lista de ações, selecione a ação SQL que pretende.

   Este exemplo utiliza a ação **Get row,** que obtém um único registo.

   ![Encontre e selecione ação "Get row" do SQL](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Esta ação retorna apenas uma linha da mesa selecionada, nada mais. Para visualizar os dados nesta linha, poderá adicionar outras ações que criem um ficheiro que inclua os campos da linha de retorno e guarde esse ficheiro numa conta de armazenamento em nuvem. Para saber mais sobre outras ações disponíveis para este conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sql/).

1. Se for solicitado a criar uma ligação, [crie já a sua ligação SQL](#create-connection). Se a sua ligação existir, selecione um **nome de tabela**e introduza o **ID da linha** para o registo que deseja.

   ![Insira o nome da mesa e o ID da linha](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. Quando terminar, na barra de ferramentas do designer, **selecione Save**.

   Este passo ativa e publica automaticamente a sua aplicação lógica ao vivo no Azure.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Ligar à base de dados

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Lidar com dados a granel

Por vezes, temos de trabalhar com conjuntos de resultados tão grandes que o conector não devolve todos os resultados ao mesmo tempo, ou quer um melhor controlo sobre o tamanho e estrutura para os conjuntos de resultados. Aqui estão algumas maneiras de lidar com conjuntos de resultados tão grandes:

* Para ajudá-lo a gerir os resultados como conjuntos mais pequenos, ligue *a paginação*. Para obter mais informações, consulte [obter dados, registos e itens em massa utilizando a paginação.](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)

* Crie um procedimento armazenado que organize os resultados da forma que quiser.

  Ao obter ou inserir várias linhas, a sua aplicação lógica pode iterar através destas linhas utilizando um [*loop até*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) ao limite dentro [destes limites](../logic-apps/logic-apps-limits-and-config.md). No entanto, quando a sua aplicação lógica tem de funcionar com conjuntos de discos tão grandes, por exemplo, milhares ou milhões de linhas, que pretende minimizar os custos resultantes das chamadas para a base de dados.

  Para organizar os resultados da forma que pretende, pode criar um [*procedimento armazenado*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) que funciona na sua instância SQL e utiliza o SELECT - **ORDER BY** statement. Esta solução dá-lhe mais controlo sobre o tamanho e estrutura dos seus resultados. A sua aplicação lógica chama o procedimento armazenado utilizando a ação de **procedimento armazenada** do conector SQL Server.

  Para mais detalhes sobre a solução, consulte estes artigos:

  * [Paginação SQL para transferência de dados em massa com Apps Lógicas](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECIONE - ENCOMENDA POR CLÁUSULA](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

### <a name="handle-dynamic-bulk-data"></a>Lidar com dados dinâmicos a granel

Por vezes, quando se faz uma chamada para um procedimento armazenado no conector SQL Server, a saída devolvida é dinâmica. Neste cenário, siga estes passos:

1. **Open Logic Apps Designer**.
1. Realize uma execução de teste da sua aplicação lógica para ver o formato de saída. Copie a sua saída de amostra.
1. No designer, no âmbito da ação em que se chama o procedimento armazenado, selecione **Novo passo**.
1. Em **Escolha uma ação,** procure e selecione a ação [**Parse JSON.**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action)
1. Na ação **Parse JSON,** **selecione Utilize a carga útil da amostra para gerar esquema**.
1. Na **janela de carga JSON de amostra ou cole uma amostra,** cole a sua saída de amostra e, em seguida, selecione **Fazer**.
1. Se tiver um erro que as Aplicações Lógicas não conseguem gerar um esquema, verifique se a sintaxe da saída da amostra está corretamente formatada. Se ainda não conseguir gerar o esquema, introduza manualmente um na caixa **de Esquema.**
1. Na barra de ferramentas do designer, **selecione Save**.
1. Para aceder às propriedades de conteúdo JSON, utilize os tokens de dados que aparecem na lista de conteúdos dinâmicos ao abrigo da [ação **Parse JSON** ](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action).

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter informações técnicas sobre os gatilhos, ações e limites deste conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sql/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores para Apps Azure Logic](../connectors/apis-list.md)
