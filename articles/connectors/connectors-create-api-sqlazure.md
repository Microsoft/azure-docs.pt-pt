---
title: Ligar ao SQL Server ou base de dados SQL do Azure - Azure Logic Apps | Documentos da Microsoft
description: Como aceder e gerir bases de dados do SQL no local ou na cloud através da automatização de fluxos de trabalho com o Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/15/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 29d53c7fbd26d3c8e2356ce82ff25c7e1b165728
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60541158"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Ligar ao SQL Server ou base de dados SQL do Azure a partir do Azure Logic Apps

Este artigo mostra como pode acessar dados na base de dados SQL de dentro de uma aplicação lógica com o conector do SQL Server. Dessa forma, pode automatizar tarefas, processos e fluxos de trabalho que gerir os seus dados do SQL e a recursos através da criação de aplicações lógicas. O conector funciona para os dois [SQL Server no local](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) e, para [base de dados do Azure SQL na cloud](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Pode criar aplicações lógicas que são executados quando acionado por eventos na base de dados SQL ou em outros sistemas, como o Dynamics CRM Online. As logic apps também podem obter, inserir e eliminar dados, juntamente com a execução de consultas SQL e procedimentos armazenados. Por exemplo, pode criar uma aplicação lógica que verifica automaticamente a existência de novos registos no Dynamics CRM Online, adiciona itens à sua base de dados SQL para quaisquer novos registos e, em seguida, envia alertas por e-mail.

Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para obter informações técnicas específicas do conector, consulte a <a href="https://docs.microsoft.com/connectors/sql/" target="blank">referência do conector do SQL Server</a>.

## <a name="prerequisites"></a>Pré-requisitos

* A aplicação lógica em que precisa de acesso à base de dados SQL. Para começar a sua aplicação lógica com um acionador SQL, tem um [aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Uma [base de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) ou um [base de dados do SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  As suas tabelas tem de ter dados para que a aplicação lógica pode retornar resultados ao chamar operações. Se criar uma base de dados do SQL do Azure, pode utilizar bases de dados de exemplo, que estão incluídos. 

* O nome do servidor SQL, o nome de base de dados, o nome de utilizador e a palavra-passe. Precisa estas credenciais para que pode autorizar a lógica de aceder ao servidor SQL. 

  * Para a base de dados SQL do Azure, pode encontrar estes detalhes na cadeia de ligação ou no portal do Azure em Propriedades de base de dados SQL:

    "Servidor = tcp: <*yourServerName*>. database.windows.net,1433;Initial catálogo = <*yourDatabaseName*>; Manter informações de segurança = False; ID de utilizador = <*yourUserName*>; Palavra-passe = <*yourPassword*>; MultipleActiveResultSets = False; Encriptar = True; TrustServerCertificate = False; Tempo limite da ligação = 30; "

  * Para o SQL Server, pode encontrar estes detalhes na cadeia de ligação: 

    "Server=<*yourServerAddress*>;Database=<*yourDatabaseName*>;User Id=<*yourUserName*>;Password=<*yourPassword*>;"

* Antes de poder ligar aplicações lógicas a sistemas no local, como o SQL Server, deve [configurar um gateway de dados no local](../logic-apps/logic-apps-gateway-install.md). Dessa forma, pode selecionar o gateway ao criar a ligação de SQL para a aplicação lógica.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Adicionar acionador SQL

No Azure Logic Apps, todas as aplicações lógicas têm de começar com uma [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico acontece ou quando for cumprida uma condição específica. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica e começa a ser executado o fluxo de trabalho da sua aplicação.

1. O portal do Azure ou o Visual Studio, crie uma aplicação lógica em branco, que abre o estruturador de aplicações lógicas. Este exemplo utiliza o portal do Azure.

2. Na caixa de pesquisa, introduza "sql server" como o filtro. Na lista de disparadores, selecione o acionador SQL que pretende. 

   Neste exemplo, selecione este acionador: **SQL Server - quando um item é criado**

   ![Selecione "SQL Server - quando é criado um item" acionador](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Se lhe for pedido para obter detalhes de ligação, [criar a ligação de SQL agora](#create-connection). 
   Ou, se a ligação já existir, selecione o **nome da tabela** pretendida na lista.

   ![Selecionar a tabela](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Definir o **intervalo** e **frequência** propriedades, que especifique a frequência com que a aplicação lógica verifica a tabela.

   Este exemplo verifica somente as tabelas selecionadas, nada mais. 
   Para fazer algo mais interessante, adicione ações que executar as tarefas que pretende. 
   
   Por exemplo, para ver o novo item na tabela, pode adicionar outras ações, tais como criar um ficheiro que tem os campos da tabela e, em seguida, enviar alertas por e-mail. 
   Para saber mais sobre outras ações para este conector ou outros conectores, consulte [conectores do Logic Apps](../connectors/apis-list.md).

5. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**. 

   Este passo ativa e publica a aplicação lógica em direto no Azure automaticamente. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Adicionar ação de SQL

No Azure Logic Apps, um [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no fluxo de trabalho que se segue um acionador ou outra ação. Neste exemplo, a aplicação lógica começa com o [acionador de periodicidade](../connectors/connectors-native-recurrence.md)e chama uma ação que obtém uma linha de base de dados SQL.

1. No portal do Azure ou Visual Studio, abra a aplicação lógica no estruturador de aplicações lógicas. Este exemplo utiliza o portal do Azure.

2. No Estruturador da aplicação lógica, sob o acionador ou ação, escolha **novo passo** > **adicionar uma ação**.

   ![Selecione "Novo passo", "Adicionar uma ação"](./media/connectors-create-api-sqlazure/add-action.png)
   
   Para adicionar uma ação entre os passos existentes, mova o rato por cima da seta de ligação. 
   Selecione o sinal de adição (**+**) que é apresentada e, em seguida, escolha **adicionar uma ação**.

2. Na caixa de pesquisa, introduza "sql server" como o filtro. Na lista de ações, selecione qualquer ação de SQL que desejar. 

   Neste exemplo, selecione a ação, o que obtém um único registo: **SQL Server - obter linha**

   ![Introduza "sql server", selecione "SQL Server - obter linha"](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Se lhe for pedido para obter detalhes de ligação, [criar a ligação de SQL agora](#create-connection). 
   Ou, se existir a sua ligação, selecione um **nome da tabela**e introduza o **ID de linha** para o registo que pretende.

   ![Introduza o nome da tabela e ID de linha](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   Neste exemplo devolve apenas uma linha da tabela selecionada, nada mais. 
   Para ver os dados nessa linha, pode adicionar outras ações que crie um ficheiro com campos de linha para análise posterior e armazenar esse ficheiro numa conta de armazenamento na cloud. Para saber mais sobre as restantes ações este conector ou outros conectores, consulte [conectores do Logic Apps](../connectors/apis-list.md).

4. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Ligar à base de dados

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="process-data-in-bulk"></a>Processar dados em massa

Ao trabalhar com conjuntos de resultados tão grandes que o conector não retorna todos os resultados ao mesmo tempo, ou mesmo melhor controle sobre o tamanho e a estrutura para os seus conjuntos de resultados, pode usar *paginação*, que ajuda a gerenciar essas resultados como conjuntos mais pequenos. 

[!INCLUDE [Set up pagination for results exceeding default page size](../../includes/connectors-pagination-bulk-data-transfer.md)]

### <a name="create-a-stored-procedure"></a>Criar um procedimento armazenado

Quando a obter ou inserir várias linhas, a aplicação lógica pode a iteração estes itens utilizando um [ *até que o loop* ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) dentro estes [limites](../logic-apps/logic-apps-limits-and-config.md). Mas, às vezes, a sua aplicação lógica tem de trabalhar com conjuntos de registos tão grandes, tal como milhares ou milhões de linhas, o que deseja minimizar os custos de chamadas para a base de dados. 

Em vez disso, pode criar uma <a href="https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine" target="blank"> *procedimento armazenado* </a> que é executado na sua instância SQL e utiliza o **SELECIONAR - ORDER BY** instrução para organizar os resultados da maneira como desejar. Esta solução dá-lhe mais controlo sobre o tamanho e a estrutura dos seus resultados. A aplicação lógica chama o procedimento armazenado utilizando o conector SQL Server **executar procedimento armazenado** ação. 

Para obter detalhes de solução, veja estes artigos:

* <a href="https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx" target="_blank">Paginação de SQL para transferência de dados em massa com o Logic Apps</a>

* <a href="https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql" target="_blank">SELECIONE - a ordenar por cláusula</a>

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter informações técnicas sobre os acionadores este conector, ações e limites, consulte a [detalhes de referência do conector](/connectors/sql/). 

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)

