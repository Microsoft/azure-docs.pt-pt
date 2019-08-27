---
title: Conectar-se ao SQL Server ou ao banco de dados SQL do Azure-aplicativo lógico do Azure | Microsoft Docs
description: Como acessar e gerenciar bancos de dados SQL localmente ou na nuvem automatizando fluxos de trabalho com aplicativos lógicos do Azure
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
ms.openlocfilehash: 804a913d17c3151d07a1ecf229e2db148dc45558
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050763"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Conectar-se ao SQL Server ou ao banco de dados SQL do Azure de aplicativos lógicos do Azure

Este artigo mostra como você pode acessar dados em seu banco de dado SQL de dentro de um aplicativo lógico com o conector de SQL Server. Dessa forma, você pode automatizar tarefas, processos e fluxos de trabalho que gerenciam seus dados e recursos SQL criando aplicativos lógicos. O conector funciona para ambos os [SQL Server locais](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) e para [o banco de dados SQL do Azure na nuvem](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Você pode criar aplicativos lógicos que são executados quando disparados por eventos em seu banco de dados SQL ou em outros sistemas, como o Dynamics CRM Online. Seus aplicativos lógicos também podem obter, inserir e excluir dados juntamente com a execução de consultas SQL e procedimentos armazenados. Por exemplo, você pode criar um aplicativo lógico que verifica automaticamente se há novos registros no Dynamics CRM Online, adiciona itens ao banco de dados SQL para quaisquer novos registros e, em seguida, envia alertas por email.

Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). Se você for novo em aplicativos lógicos, examine [o que é o](../logic-apps/logic-apps-overview.md) início [rápido e aplicativos lógicos do Azure: Crie seu primeiro aplicativo](../logic-apps/quickstart-create-first-logic-app-workflow.md)lógico. Para obter informações técnicas específicas do conector, consulte a [referência do conector do SQL Server](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Pré-requisitos

* O aplicativo lógico em que você precisa acessar o banco de dados SQL. Para iniciar seu aplicativo lógico com um gatilho SQL, você precisa de um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Um [banco de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) ou um [banco de dados SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  Suas tabelas devem ter dados para que seu aplicativo lógico possa retornar resultados ao chamar operações. Se você criar um banco de dados SQL do Azure, poderá usar bancos de dados de exemplo que estão incluídos. 

* O nome do SQL Server, o nome do banco de dados, seu nome de usuário e sua senha. Você precisa dessas credenciais para que possa autorizar sua lógica para acessar o SQL Server. 

  * Para o banco de dados SQL do Azure, você pode encontrar esses detalhes na cadeia de conexão ou na portal do Azure nas propriedades do banco de dados SQL:

    "Server = TCP: <*nomedoservidor*>. Database. Windows. net, 1433; Initial Catalog = <*nomedoseubancodedados*>; Informações de persistência de segurança = falso; ID de usuário = <*seunomedeusuário*>; Password = <*yourPassword*>; MultipleActiveResultSets = false; Encrypt = true; TrustServerCertificate = false; Tempo limite da conexão = 30; "

  * Por SQL Server, você pode encontrar esses detalhes na cadeia de conexão: 

    "Server = <*yourServerAddress*>;D anco = <*nomedoseubancodedados*>; ID de usuário = <*seunomedeusuário*>; Password = <*yourPassword*>; "

* Antes de poder conectar aplicativos lógicos a sistemas locais, como SQL Server, você deve [configurar um gateway de dados local](../logic-apps/logic-apps-gateway-install.md). Dessa forma, você pode selecionar o gateway ao criar a conexão SQL para seu aplicativo lógico.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Adicionar gatilho SQL

Em aplicativos lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de aplicativos lógicos cria uma instância de aplicativo lógico e começa a executar o fluxo de trabalho do aplicativo.

1. No portal do Azure ou no Visual Studio, crie um aplicativo lógico em branco, que abre o designer de aplicativos lógicos. Este exemplo usa o portal do Azure.

2. Na caixa de pesquisa, digite "SQL Server" como filtro. Na lista de gatilhos, selecione o gatilho SQL desejado. 

   Para este exemplo, selecione este gatilho: **SQL Server-quando um item é criado**

   ![Selecione o gatilho "SQL Server-quando um item é criado"](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Se você for solicitado a fornecer detalhes de conexão, [Crie sua conexão SQL agora](#create-connection). 
   Ou, se sua conexão já existir, selecione o **nome da tabela** que você deseja na lista.

   ![Selecionar a tabela](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Defina as propriedades **Interval** e **Frequency** , que especificam com que frequência seu aplicativo lógico verifica a tabela.

   Este exemplo verifica apenas a tabela selecionada, nada mais. 
   Para fazer algo mais interessante, adicione ações que executem as tarefas desejadas. 
   
   Por exemplo, para exibir o novo item na tabela, você pode adicionar outras ações, como criar um arquivo com campos da tabela e enviar alertas por email. 
   Para saber mais sobre outras ações para esse conector ou outros conectores, consulte conectores de [aplicativos lógicos](../connectors/apis-list.md).

5. Quando terminar, na barra de ferramentas do designer, selecione **salvar**. 

   Esta etapa habilita e publica automaticamente seu aplicativo lógico em tempo real no Azure. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Adicionar ação SQL

Em aplicativos lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa no fluxo de trabalho que segue um gatilho ou outra ação. Neste exemplo, o aplicativo lógico começa com o [gatilho](../connectors/connectors-native-recurrence.md)de recorrência e chama uma ação que obtém uma linha de um banco de dados SQL.

1. No portal do Azure ou no Visual Studio, abra seu aplicativo lógico no designer de aplicativos lógicos. Este exemplo usa o portal do Azure.

2. No designer do aplicativo lógico, no gatilho ou na ação, selecione **nova etapa** > **Adicionar uma ação**.

   ![Selecione "nova etapa", "adicionar uma ação"](./media/connectors-create-api-sqlazure/add-action.png)
   
   Para adicionar uma ação entre as etapas existentes, mova o mouse sobre a seta de conexão. 
   Selecione o sinal de adição **+** () que aparece e, em seguida, selecione **Adicionar uma ação**.

2. Na caixa de pesquisa, digite "SQL Server" como filtro. Na lista ações, selecione qualquer ação SQL desejada. 

   Para este exemplo, selecione esta ação, que obtém um único registro: **SQL Server-obter linha**

   ![Insira "SQL Server", selecione "SQL Server-obter linha"](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Se você for solicitado a fornecer detalhes de conexão, [Crie sua conexão SQL agora](#create-connection). 
   Ou, se sua conexão existir, selecione um **nome de tabela**e insira a **ID de linha** para o registro desejado.

   ![Insira o nome da tabela e a ID da linha](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   Este exemplo retorna apenas uma linha da tabela selecionada, nada mais. 
   Para exibir os dados nessa linha, você pode adicionar outras ações que criam um arquivo com campos da linha para revisão posterior e armazenam esse arquivo em uma conta de armazenamento em nuvem. Para saber mais sobre outras ações neste conector ou outros conectores, consulte conectores de [aplicativos lógicos](../connectors/apis-list.md).

4. Quando terminar, na barra de ferramentas do designer, selecione **salvar**. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Ligar à base de dados

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Lidar com dados em massa

Às vezes, talvez você precise trabalhar com conjuntos de resultados tão grandes que o conector não retorne todos os resultados ao mesmo tempo, ou você queira um melhor controle sobre o tamanho e a estrutura de seus conjuntos de resultados. Aqui estão algumas maneiras que você pode lidar com esses grandes conjuntos de resultados:

* Para ajudá-lo a gerenciar os resultados como conjuntos menores, ative a paginação. Para obter mais informações, consulte [obter dados em massa, registros e itens usando a](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)paginação.

* Crie um procedimento armazenado que organize os resultados da maneira desejada.

  Ao obter ou inserir várias linhas, seu aplicativo lógico pode iterar por meio dessas linhas usando um [*loop Until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) dentro desses [limites](../logic-apps/logic-apps-limits-and-config.md). 
  No entanto, quando seu aplicativo lógico tem que trabalhar com conjuntos de registros tão grandes, por exemplo, milhares ou milhões de linhas, você deseja minimizar os custos resultantes de chamadas para o banco de dados.

  Para organizar os resultados da maneira desejada, você pode criar um [*procedimento armazenado*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) que é executado em sua instância do SQL e usa a instrução **Select-order by** . 
  Essa solução oferece mais controle sobre o tamanho e a estrutura dos resultados. 
  Seu aplicativo lógico chama o procedimento armazenado usando a ação **Executar procedimento armazenado** do conector de SQL Server.

  Para obter detalhes da solução, consulte estes artigos:

  * [Paginação SQL para transferência de dados em massa com aplicativos lógicos](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [Cláusula SELECT – ORDENAr por](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter informações técnicas sobre os gatilhos, as ações e os limites desse conector, consulte os [detalhes de referência do conector](/connectors/sql/). 

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outros conectores de [aplicativos lógicos](../connectors/apis-list.md)

