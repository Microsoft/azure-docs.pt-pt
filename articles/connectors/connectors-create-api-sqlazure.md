---
title: Conectar-se ao SQL Server ou banco de dados SQL do Azure-aplicativo lógico do Azure
description: Automatizar tarefas para bancos de dados SQL locais ou na nuvem usando aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam; LADocs
manager: carmonm
ms.topic: conceptual
tags: connectors
ms.date: 11/08/2019
ms.openlocfilehash: a6367e5897e9bd548550b099c0bd2e6186845d6d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826310"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Automatizar fluxos de trabalho para SQL Server ou banco de dados SQL do Azure usando aplicativos lógicos do Azure

Este artigo mostra como você pode acessar dados em seu banco de dado SQL de dentro de um aplicativo lógico com o conector de SQL Server. Dessa forma, você pode automatizar tarefas, processos ou fluxos de trabalho que gerenciam seus dados e recursos SQL criando aplicativos lógicos. O conector de SQL Server funciona para o [SQL Server local](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) e para o [banco de dados SQL do Azure baseado em nuvem](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).

Você pode criar aplicativos lógicos que são executados quando disparados por eventos em seu banco de dados SQL ou em outros sistemas, como o Dynamics CRM Online. Seus aplicativos lógicos também podem obter, inserir e excluir dados juntamente com consultas SQL e procedimentos armazenados em execução. Por exemplo, você pode criar um aplicativo lógico que verifica automaticamente se há novos registros no Dynamics CRM Online, adiciona itens ao banco de dados SQL para quaisquer novos registros e, em seguida, envia alertas por email sobre os itens adicionados.

Se você for novo em aplicativos lógicos, examine [o que é o início rápido e aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) [: Crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para obter informações técnicas, limitações e problemas conhecidos específicos do conector, consulte a [página de referência do conector do SQL Server](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um [banco de dados SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) ou [banco de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md)

  Suas tabelas devem ter dados para que seu aplicativo lógico possa retornar resultados ao chamar operações. Se você criar um banco de dados SQL do Azure, poderá usar bancos de dados de exemplo que estão incluídos.

* O nome do SQL Server, o nome do banco de dados, seu nome de usuário e sua senha. Você precisa dessas credenciais para que possa autorizar sua lógica para acessar o SQL Server.

  * Por SQL Server, você pode encontrar esses detalhes na cadeia de conexão:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Para o banco de dados SQL do Azure, você pode encontrar esses detalhes na cadeia de conexão ou na portal do Azure nas propriedades do banco de dados SQL:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* O [Gateway de dados](../logic-apps/logic-apps-gateway-install.md) local instalado em um computador local e um [recurso do gateway de dados do Azure criado no portal do Azure](../logic-apps/logic-apps-gateway-connection.md) para estes cenários:

  * Seus aplicativos lógicos não são executados em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

  * Seus aplicativos lógicos *são* executados em um ambiente de serviço de integração, mas você precisa usar a autenticação do Windows para sua conexão de SQL Server. Para esse cenário, use a versão não ISE do conector de SQL Server junto com o gateway de dados porque a versão do ISE não dá suporte à autenticação do Windows.

* O aplicativo lógico em que você precisa acessar o banco de dados SQL. Para iniciar seu aplicativo lógico com um gatilho SQL, você precisa de um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Adicionar um gatilho SQL

Em aplicativos lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de aplicativos lógicos cria uma instância de aplicativo lógico e começa a executar o fluxo de trabalho do aplicativo lógico.

1. No portal do Azure ou no Visual Studio, crie um aplicativo lógico em branco, que abre o designer de aplicativos lógicos. Este exemplo usa o portal do Azure.

1. No designer, na caixa de pesquisa, digite "SQL Server" como filtro. Na lista de gatilhos, selecione o gatilho SQL desejado.

   Este exemplo usa o gatilho **quando um item é criado** .

   ![Selecione o gatilho "quando um item é criado"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Se você for solicitado a criar uma conexão, [Crie sua conexão SQL agora](#create-connection). Se sua conexão existir, selecione um **nome de tabela**.

   ![Selecione a tabela que você deseja](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Defina as propriedades **Interval** e **Frequency** , que especificam com que frequência seu aplicativo lógico verifica a tabela.

   Esse gatilho retorna apenas uma linha da tabela selecionada, nada mais. Para executar outras tarefas, adicione outras ações que executam as tarefas desejadas. Por exemplo, para exibir os dados nessa linha, você pode adicionar outras ações que criam um arquivo que inclui os campos da linha retornada e, em seguida, enviar alertas por email. Para saber mais sobre outras ações disponíveis para esse conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sql/).

1. Quando terminar, na barra de ferramentas do designer, selecione **salvar**.

   Esta etapa habilita e publica automaticamente seu aplicativo lógico em tempo real no Azure.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Adicionar uma ação SQL

Em aplicativos lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa no fluxo de trabalho que segue um gatilho ou outra ação. Neste exemplo, o aplicativo lógico começa com o [gatilho de recorrência](../connectors/connectors-native-recurrence.md)e chama uma ação que obtém uma linha de um banco de dados SQL.

1. No portal do Azure ou no Visual Studio, abra seu aplicativo lógico no designer de aplicativos lógicos. Este exemplo usa o portal do Azure.

1. No gatilho ou na ação em que você deseja adicionar a ação SQL, selecione **nova etapa**.

   ![Adicionar nova etapa ao seu aplicativo lógico](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Para adicionar uma ação entre as etapas existentes, mova o mouse sobre a seta de conexão. Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Em **escolher uma ação**, na caixa de pesquisa, digite "SQL Server" como filtro. Na lista ações, selecione a ação SQL desejada.

   Este exemplo usa a ação **obter linha** , que obtém um único registro.

   ![Localizar e selecionar a ação SQL "obter linha"](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Essa ação retorna apenas uma linha da tabela selecionada, nada mais. Para exibir os dados nessa linha, você pode adicionar outras ações que criam um arquivo que inclui os campos da linha retornada e armazena esse arquivo em uma conta de armazenamento em nuvem. Para saber mais sobre outras ações disponíveis para esse conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sql/).

1. Se você for solicitado a criar uma conexão, [Crie sua conexão SQL agora](#create-connection). Se sua conexão existir, selecione um **nome de tabela**e insira a **ID de linha** para o registro desejado.

   ![Insira o nome da tabela e a ID da linha](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. Quando terminar, na barra de ferramentas do designer, selecione **salvar**.

   Esta etapa habilita e publica automaticamente seu aplicativo lógico em tempo real no Azure.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Ligar à base de dados

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Lidar com dados em massa

Às vezes, você precisa trabalhar com conjuntos de resultados tão grandes que o conector não retorna todos os resultados ao mesmo tempo ou você deseja ter um melhor controle sobre o tamanho e a estrutura de seus conjuntos de resultados. Aqui estão algumas maneiras que você pode lidar com esses grandes conjuntos de resultados:

* Para ajudá-lo a gerenciar os resultados como conjuntos menores, ative a *paginação*. Para obter mais informações, consulte [obter dados em massa, registros e itens usando a paginação](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Crie um procedimento armazenado que organize os resultados da maneira desejada.

  Ao obter ou inserir várias linhas, seu aplicativo lógico pode iterar por meio dessas linhas usando um [*loop Until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) dentro desses [limites](../logic-apps/logic-apps-limits-and-config.md). No entanto, quando seu aplicativo lógico tem que trabalhar com conjuntos de registros tão grandes, por exemplo, milhares ou milhões de linhas, você deseja minimizar os custos resultantes de chamadas para o banco de dados.

  Para organizar os resultados da maneira desejada, você pode criar um [*procedimento armazenado*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) que é executado em sua instância do SQL e usa a instrução **Select-order by** . Essa solução oferece mais controle sobre o tamanho e a estrutura dos resultados. Seu aplicativo lógico chama o procedimento armazenado usando a ação **Executar procedimento armazenado** do conector de SQL Server.

  Para obter mais detalhes da solução, consulte estes artigos:

  * [Paginação SQL para transferência de dados em massa com aplicativos lógicos](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [Cláusula SELECT – ORDENAr por](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter informações técnicas sobre os gatilhos, as ações e os limites desse conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sql/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores para aplicativos lógicos do Azure](../connectors/apis-list.md)
