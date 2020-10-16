---
title: Ligue ao SQL Server, Azure SQL Database ou Azure SQL Managed Instance
description: Automatizar tarefas para bases de dados SQL nas instalações ou na nuvem utilizando aplicações lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 06/06/2020
tags: connectors
ms.openlocfilehash: a50a171536d7f81de42da415960398d31ec64827
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326784"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>Automatizar fluxos de trabalho para uma base de dados SQL utilizando apps Azure Logic

Este artigo mostra como pode aceder aos dados na sua base de dados SQL a partir de uma aplicação lógica com o conector SQL Server. Desta forma, pode automatizar tarefas, processos ou fluxos de trabalho que gerem os seus dados e recursos SQL criando aplicações lógicas. O conector SQL Server funciona para [o SQL Server,](/sql/sql-server/sql-server-technical-documentation) bem como [para a Base de Dados SQL Azure](../azure-sql/database/sql-database-paas-overview.md) e [para a Azure SQL Gerenciada .](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)

Pode criar aplicações lógicas que são executadas quando desencadeadas por eventos na sua base de dados SQL ou em outros sistemas, como o Dynamics CRM Online. As suas aplicações lógicas também podem obter, inserir e eliminar dados juntamente com consultas SQL executando e procedimentos armazenados. Por exemplo, pode construir uma aplicação lógica que verifica automaticamente novos registos no Dynamics CRM Online, adiciona itens à sua base de dados SQL para quaisquer novos registos e, em seguida, envia alertas de e-mail sobre os itens adicionados.

Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para obter informações técnicas específicas do conector, limitações e problemas conhecidos, consulte a página de referência do [conector SQL Server](/connectors/sql/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [base de dados sql server](/sql/relational-databases/databases/create-a-database), [Azure SQL Database,](../azure-sql/database/single-database-create-quickstart.md)ou [Azure SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md).

  As suas tabelas devem ter dados para que a sua aplicação lógica possa devolver resultados ao ligar para as operações. Se utilizar a Base de Dados Azure SQL, pode utilizar bases de dados de amostras, que estão incluídas.

* O nome do servidor SQL, o nome da base de dados, o nome de utilizador e a sua palavra-passe. Precisa destas credenciais para que possa autorizar a sua lógica de acesso ao seu servidor SQL.

  * Para o SQL Server no local, pode encontrar estes detalhes na cadeia de ligação:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Para a Base de Dados Azure SQL, pode encontrar estes detalhes na cadeia de ligação.
  
    Por exemplo, para encontrar esta cadeia no portal Azure, abra a sua base de dados. No menu da base de dados, selecione **as cadeias de ligação** ou **propriedades**:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* Com base no facto de as suas aplicações lógicas serem executadas no Azure global, multi-inquilino ou num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)aqui estão outros requisitos para a ligação ao SQL Server no local:

  * Para aplicações lógicas em Azure global e multi-inquilino que se conectam ao SQL Server no local, você precisa ter o [portal de dados no local](../logic-apps/logic-apps-gateway-install.md) instalado em um computador local e um recurso de gateway de [dados que já foi criado em Azure](../logic-apps/logic-apps-gateway-connection.md).

  * Para aplicações lógicas num ISE que se conectam ao SQL Server e utilizam a autenticação do Windows, o conector SQL Server, com versão ISE, não suporta a autenticação do Windows. Por isso, ainda precisa de utilizar o gateway de dados e o conector não-ISE SQL Server. Para outros tipos de autenticação, não precisa de utilizar o gateway de dados e pode utilizar o conector versão ISE.

* A aplicação lógica onde precisa de acesso à sua base de dados SQL. Para iniciar a sua aplicação lógica com um gatilho SQL, precisa de uma [aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Ligar à sua base de dados

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Agora, continue com estes passos:

* [Conecte-se à base de dados Azure SQL baseada na nuvem ou à instância gerida](#connect-azure-sql-db)
* [Ligue-se ao SqL Server no local](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>Ligue à Base de Dados SQL do Azure ou à Instância Gerida

A primeira vez que adiciona um [gatilho SQL](#add-sql-trigger) ou [uma ação SQL,](#add-sql-action)e não criou previamente uma ligação à sua base de dados, é solicitado que complete estes passos:

1. Para **Tipo de Autenticação**, selecione a autenticação necessária e ativada na sua base de dados na Base de Dados Azure SQL ou na Instância Gerida Azure SQL:

   | Autenticação | Descrição |
   |----------------|-------------|
   | [**Azure AD Integrado**](../azure-sql/database/authentication-aad-overview.md) | - Suporta tanto o conector não-ISE como o conector ISE SQL Server. <p><p>- Requer uma identidade válida no Azure Ative Directory (Azure AD) que tenha acesso à sua base de dados. <p>Para obter mais informações, veja estes tópicos: <p>- [Visão geral de segurança do Azure SQL - Autenticação](../azure-sql/database/security-overview.md#authentication) <br>- [Autorizar o acesso à base de dados a Azure SQL - Autenticação e autorização](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL - Autenticação Integrada AD AD](../azure-sql/database/authentication-aad-overview.md) |
   | [**Autenticação do servidor SQL**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | - Suporta tanto o conector não-ISE como o conector ISE SQL Server. <p><p>- Requer um nome de utilizador válido e uma senha forte que são criadas e armazenadas na sua base de dados. <p>Para obter mais informações, veja estes tópicos: <p>- [Visão geral de segurança do Azure SQL - Autenticação](../azure-sql/database/security-overview.md#authentication) <br>- [Autorizar o acesso à base de dados a Azure SQL - Autenticação e autorização](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   Este exemplo continua com **a Azure AD Integrada:**

   ![Screenshot que mostra a janela de ligação "SQL Server" com a lista "Tipo de Autenticação" aberta e "Azure AD Integrado" selecionada.](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. Depois de selecionar **Azure AD Integrado,** **selecione Iniciar S-No**. Com base na utilização da Base de Dados Azure SQL ou da Azure SQL Managed Instance, selecione as credenciais do utilizador para autenticação.

1. Selecione estes valores para a sua base de dados:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome do servidor** | Yes | O endereço do seu servidor SQL, por exemplo, `Fabrikam-Azure-SQL.database.windows.net` |
   | **Nome da base de dados** | Sim | O nome da sua base de dados SQL, por exemplo, `Fabrikam-Azure-SQL-DB` |
   | **Nome da tabela** | Sim | A mesa que quer usar, por exemplo, `SalesLT.Customer` |
   ||||

   > [!TIP]
   > Pode encontrar esta informação na cadeia de ligação da sua base de dados. Por exemplo, no portal Azure, encontre e abra a sua base de dados. No menu da base de dados, selecione **as cadeias de ligação** ou **propriedades** onde pode encontrar esta cadeia:
   >
   > `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

   Este exemplo mostra como estes valores podem parecer:

   ![Criar ligação à base de dados SQL](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Agora, continue com os passos que ainda não completou em [adicionar um gatilho SQL](#add-sql-trigger) ou [adicionar uma ação SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>Ligue-se ao SqL Server no local

A primeira vez que adiciona um [gatilho SQL](#add-sql-trigger) ou [uma ação SQL,](#add-sql-action)e não criou previamente uma ligação à sua base de dados, é solicitado que complete estes passos:

1. Para ligações ao servidor SQL no local que necessitem do gateway de dados no local, certifique-se de que [completou estes pré-requisitos](#multi-tenant-or-ise).

   Caso contrário, o seu recurso de gateway de dados não aparecerá na lista **de Gateway de Ligação** quando criar a sua ligação.

1. Para **tipo de autenticação,** selecione a autenticação necessária e ativada no seu Servidor SQL:

   | Autenticação | Descrição |
   |----------------|-------------|
   | [**Autenticação do Windows**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | - Suporta apenas o conector não-ISE SQL Server, que requer um recurso de gateway de dados que foi previamente criado em Azure para a sua ligação, independentemente de utilizar o Azure multi-inquilino ou um ISE. <p><p>- Requer um nome de utilizador e senha válidos do Windows para confirmar a sua identidade através da sua conta Windows. <p>Para mais informações, consulte [a autenticação do Windows](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) |
   | [**Autenticação do servidor SQL**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | - Suporta tanto o conector não-ISE como o conector ISE SQL Server. <p><p>- Requer um nome de utilizador válido e uma palavra-passe forte que são criadas e armazenadas no seu SQL Server. <p>Para obter mais informações, consulte [a autenticação do servidor SQL](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication). |
   |||

   Este exemplo continua com **a Autenticação do Windows:**

   ![Selecione tipo de autenticação para usar](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. Selecione ou forneça os seguintes valores para a sua base de dados SQL:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome do servidor SQL** | Sim | O endereço do seu servidor SQL, por exemplo, `Fabrikam-Azure-SQL.database.windows.net` |
   | **Nome da base de dados SQL** | Sim | O nome da sua base de dados SQL Server, por exemplo, `Fabrikam-Azure-SQL-DB` |
   | **Nome de Utilizador** | Sim | O seu nome de utilizador para o servidor e base de dados SQL |
   | **Palavra-passe** | Sim | A sua palavra-passe para o servidor e base de dados SQL |
   | **Subscrição** |  Sim, para a autenticação do Windows | A subscrição Azure para o recurso de gateway de dados que criou anteriormente no Azure |
   | **Gateway de conexão** | Sim, para a autenticação do Windows | O nome do recurso data gateway que criou anteriormente em Azure <p><p>**Sugestão**: Se o seu portal não aparecer na lista, verifique se configura corretamente [o seu portal](../logic-apps/logic-apps-gateway-connection.md). |
   |||

   > [!TIP]
   > Pode encontrar esta informação na cadeia de ligação da sua base de dados:
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   Este exemplo mostra como estes valores podem parecer:

   ![Criar ligação SQL Server concluída](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Quando estiver pronto, selecione **Criar**.

1. Agora, continue com os passos que ainda não completou em [adicionar um gatilho SQL](#add-sql-trigger) ou [adicionar uma ação SQL](#add-sql-action).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Adicione um gatilho SQL

1. No [portal Azure](https://portal.azure.com) ou no Visual Studio, crie uma aplicação lógica em branco, que abre o Logic App Designer. Este exemplo continua com o portal Azure.

1. No designer, na caixa de pesquisa, `sql server` entre. A partir da lista de gatilhos, selecione o gatilho SQL que deseja. Este exemplo utiliza o gatilho **Quando um item é criado.**

   ![Selecione o gatilho "Quando um item é criado"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Se estiver a ligar-se à sua base de dados SQL pela primeira vez, é-lhe solicitado que crie agora a [sua ligação à base de dados SQL](#create-connection). Depois de criar esta ligação, pode continuar com o próximo passo.

1. No gatilho, especifique o intervalo e a frequência da frequência com que o gatilho verifica a tabela.

1. Para adicionar outras propriedades disponíveis para este gatilho, abra a nova lista **de parâmetros Adicionar.**

   Este gatilho retorna apenas uma linha da mesa selecionada, e nada mais. Para executar outras tarefas, continue adicionando uma [ação de conector SQL](#add-sql-action) ou [outra ação](../connectors/apis-list.md) que execute a próxima tarefa que deseja no fluxo de trabalho da aplicação lógica.
   
   Por exemplo, para ver os dados nesta linha, pode adicionar outras ações que criam um ficheiro que inclui os campos da linha retornado e, em seguida, enviar alertas de e-mail. Para saber mais sobre outras ações disponíveis para este conector, consulte a [página de referência do conector](/connectors/sql/).

1. Na barra de ferramentas do designer, **selecione Save**.

   Embora este passo permita e publique automaticamente a sua aplicação lógica ao vivo no Azure, a única ação que a sua aplicação lógica atualmente toma é verificar a sua base de dados com base no intervalo e frequência especificados.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Adicione uma ação SQL

Neste exemplo, a aplicação lógica começa com o [gatilho de Recorrência](../connectors/connectors-native-recurrence.md), e chama uma ação que recebe uma linha a partir de uma base de dados SQL.

1. No [portal Azure](https://portal.azure.com) ou no Visual Studio, abra a sua aplicação lógica no Logic App Designer. Este exemplo continua o portal Azure.

1. Sob o gatilho ou ação onde pretende adicionar a ação SQL, selecione **Novo passo**.

   ![Adicione uma ação à sua app lógica](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Ou, para adicionar uma ação entre os passos existentes, mova o rato sobre a seta de ligação. Selecione o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Em **Escolha uma ação,** na caixa de pesquisa, insira `sql server` . Na lista de ações, selecione a ação SQL que pretende. Este exemplo utiliza a ação **Get row,** que obtém um único registo.

   ![Selecione ação "Get row" do SQL](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. Se estiver a ligar-se à sua base de dados SQL pela primeira vez, é-lhe solicitado que crie agora a [sua ligação à base de dados SQL](#create-connection). Depois de criar esta ligação, pode continuar com o próximo passo.

1. Selecione o **nome de tabela**, que está neste `SalesLT.Customer` exemplo. Introduza o **ID da linha** para o registo que quiser.

   ![Selecione o nome da tabela e especifique o ID da linha](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   Esta ação retorna apenas uma linha da mesa selecionada, nada mais. Assim, para ver os dados nesta linha, pode adicionar outras ações que criam um ficheiro que inclui os campos da linha devolvida, e armazenar esse ficheiro numa conta de armazenamento em nuvem. Para saber mais sobre outras ações disponíveis para este conector, consulte a [página de referência do conector](/connectors/sql/).

1. Quando terminar, na barra de ferramentas do designer, **selecione Save**.

   Este passo ativa e publica automaticamente a sua aplicação lógica ao vivo no Azure.

## <a name="handle-bulk-data"></a>Lidar com dados a granel

Por vezes, temos de trabalhar com conjuntos de resultados tão grandes que o conector não devolve todos os resultados ao mesmo tempo, ou quer um melhor controlo sobre o tamanho e estrutura para os conjuntos de resultados. Aqui estão algumas maneiras de lidar com conjuntos de resultados tão grandes:

* Para ajudá-lo a gerir os resultados como conjuntos mais pequenos, ligue *a paginação*. Para obter mais informações, consulte [obter dados, registos e itens em massa utilizando a paginação.](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)

* Crie um procedimento armazenado que organize os resultados da forma que quiser.

  Ao obter ou inserir várias linhas, a sua aplicação lógica pode iterar através destas linhas utilizando um [*loop até*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) ao limite dentro [destes limites](../logic-apps/logic-apps-limits-and-config.md). No entanto, quando a sua aplicação lógica tem de funcionar com conjuntos de discos tão grandes, por exemplo, milhares ou milhões de linhas, que pretende minimizar os custos resultantes das chamadas para a base de dados.

  Para organizar os resultados da forma que pretende, pode criar um [*procedimento armazenado*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) que funciona na sua instância SQL e utiliza o SELECT - **ORDER BY** statement. Esta solução dá-lhe mais controlo sobre o tamanho e estrutura dos seus resultados. A sua aplicação lógica chama o procedimento armazenado utilizando a ação de **procedimento armazenada** do conector SQL Server.

  Para mais detalhes sobre a solução, consulte estes artigos:

  * [Paginação SQL para transferência de dados em massa com Apps Lógicas](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECIONE - ENCOMENDA POR CLÁUSULA](/sql/t-sql/queries/select-order-by-clause-transact-sql)

### <a name="handle-dynamic-bulk-data"></a>Lidar com dados dinâmicos a granel

Quando se chama um procedimento armazenado utilizando o conector SQL Server, a saída devolvida é por vezes dinâmica. Neste cenário, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Ver o formato de saída realizando um ensaio. Copie e guarde a sua saída de amostra.

1. No designer, no âmbito da ação em que se chama o procedimento armazenado, selecione **Novo passo**.

1. Em **Escolha uma ação,** encontre e selecione a ação [**Parse JSON.**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action)

1. Na ação **Parse JSON,** **selecione Utilize a carga útil da amostra para gerar esquema**.

1. Na **caixa de carga JSON, cole a** sua amostra e selecione **'Fazer'.**

   > [!NOTE]
   > Se tiver um erro que as Aplicações Lógicas não conseguem gerar um esquema, verifique se a sintaxe da saída da amostra está corretamente formatada. Se ainda não conseguir gerar o esquema, na caixa **de Schema,** introduza manualmente o esquema.

1. Na barra de ferramentas do designer, **selecione Save**.

1. Para fazer referência às propriedades do conteúdo JSON, clique no interior das caixas de edição onde pretende referenciar essas propriedades para que a lista de conteúdos dinâmicos apareça. Na lista, no título [**Parse JSON,**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) selecione os tokens de dados para as propriedades de conteúdo JSON que deseja.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter informações técnicas sobre os gatilhos, ações e limites deste conector, consulte a [página de referência do conector,](/connectors/sql/)que é gerada a partir da descrição do Swagger.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores para Apps Azure Logic](../connectors/apis-list.md)

