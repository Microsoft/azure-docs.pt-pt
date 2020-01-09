---
title: Acessar e gerenciar recursos do IBM DB2
description: Leia, edite, atualize e gerencie recursos do IBM DB2 criando fluxos de trabalho automatizados usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 0f6e32056783a816d847db191de4fcdae2616ab7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446186"
---
# <a name="access-and-manage-ibm-db2-resources-by-using-azure-logic-apps"></a>Acessar e gerenciar recursos do IBM DB2 usando aplicativos lógicos do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o [conector IBM DB2](/connectors/db2/), você pode criar tarefas e fluxos de trabalho automatizados com base nos recursos armazenados em seu banco de dados DB2. Seus fluxos de trabalho podem se conectar aos recursos no banco de dados, ler e listar suas tabelas de banco de dados, adicionar linhas, alterar linhas, excluir linhas e muito mais. Você pode incluir ações em seus aplicativos lógicos que obtêm respostas do seu banco de dados e disponibilizar a saída para outras ações.

Este artigo mostra como você pode criar um aplicativo lógico que executa várias operações de banco de dados. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="supported-platforms-and-versions"></a>Plataformas e versões com suporte

O conector do DB2 inclui um cliente da Microsoft que se comunica com servidores DB2 remotos em uma rede TCP/IP. Você pode usar esse conector para acessar bancos de dados de nuvem, como IBM DB2 para Windows, em execução na virtualização do Azure. Você também pode acessar bancos de dados do DB2 locais depois [de instalar e configurar o data gateway local](../logic-apps/logic-apps-gateway-connection.md).

O conector IBM DB2 dá suporte a essas plataformas e versões do IBM DB2, juntamente com os produtos compatíveis com IBM DB2 que dão suporte à DRDA (Distributed Relacionative Database Architecture) versões 10 e 11:

| Plataforma | Versão | 
|----------|---------|
| IBM DB2 para z/OS | 11.1, 10.1 |
| IBM DB2 para i | 7.3, 7.2, 7.1 |
| IBM DB2 para LUW | 11, 10,5 |
|||

## <a name="supported-database-operations"></a>Operações de banco de dados com suporte

O conector IBM DB2 dá suporte a essas operações de banco de dados, que são mapeadas para as ações correspondentes no conector:

| Operação de banco de dados | Ação do conector |
|--------------------|------------------|
| Listar tabelas de banco de dados | Obter tabelas |
| Ler uma linha usando SELECT | Obter linha |
| Ler todas as linhas usando SELECT | Obter linhas |
| Adicionar uma linha usando INSERT | Insert row |
| Editar uma linha usando atualizar | Atualizar linha |
| Remover uma linha usando DELETE | Excluir linha |
|||

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um banco de dados IBM DB2, baseado em nuvem ou local

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar o banco de dados DB2. Esse conector fornece apenas ações, portanto, para iniciar seu aplicativo lógico, selecione um gatilho separado, por exemplo, o gatilho de **recorrência** .
Os exemplos neste artigo usam o gatilho de **recorrência** .

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Adicionar ação DB2-obter tabelas

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer do aplicativo lógico, se ainda não estiver aberto.

1. No gatilho, escolha **nova etapa**.

1. Na caixa de pesquisa, digite "DB2" como filtro. Para este exemplo, na lista ações, selecione esta ação: **obter tabelas (versão prévia)**

   ![Selecionar ação](./media/connectors-create-api-db2/select-db2-action.png)

   Agora, você será solicitado a fornecer detalhes de conexão para seu banco de dados DB2.

1. Siga as etapas para criar conexões para [bancos](#cloud-connection) de dados na nuvem ou [bancos de dados locais](#on-premises-connection).

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Conectar-se ao Cloud DB2

Para configurar sua conexão, forneça esses detalhes de conexão quando solicitado, escolha **criar**e, em seguida, salve seu aplicativo lógico:

| Propriedade | Obrigatório | Descrição |
|----------|----------|-------------|
| **Conectar por meio do gateway local** | Não | Aplica-se somente a conexões locais. |
| **Nome da Ligação** | Sim | O nome da conexão, por exemplo, "MyLogicApp-DB2-Connection" |
| **Servidor** | Sim | O número da porta de dois-pontos de endereço ou alias para o servidor DB2, por exemplo, "myDB2server.cloudapp.net:50000" <p><p>**Observação**: esse valor é uma cadeia de caracteres que representa um endereço TCP/IP ou alias, no formato IPv4 ou IPv6, seguido por dois-pontos e um número de porta TCP/IP. |
| **Base de Dados** | Sim | O nome do seu banco de dados <p><p>**Observação**: esse valor é uma cadeia de caracteres que representa um nome de banco de dados relacional DRDA (RDBNAM): <p>-O DB2 para z/OS aceita uma cadeia de caracteres de 16 bytes em que o banco de dados é conhecido como um "IBM DB2 para z/OS". <br>-DB2 para eu aceito uma cadeia de caracteres de 18 bytes em que o banco de dados é conhecido como um banco de dados relacional "IBM DB2 para i". <br>– O DB2 para LUW aceita uma cadeia de caracteres de 8 bytes. |
| **Nome de Utilizador** | Sim | Seu nome de usuário para o banco de dados <p><p>**Observação**: esse valor é uma cadeia de caracteres cujo comprimento é baseado no banco de dados específico: <p><p>-O DB2 para z/OS aceita uma cadeia de caracteres de 8 bytes. <br>-DB2 para eu aceito uma cadeia de caracteres de 10 bytes. <br>-O DB2 para Linux ou UNIX aceita uma cadeia de caracteres de 8 bytes. <br>– O DB2 para Windows aceita uma cadeia de caracteres de 30 bytes. |
| **Palavra-passe** | Sim | Sua senha para o banco de dados |
||||

Por exemplo:

![Detalhes da conexão para bancos de dados baseados em nuvem](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Conectar-se ao DB2 local

Antes de criar sua conexão, você já deve ter seu gateway de dados local instalado. Caso contrário, não será possível concluir a configuração da conexão. Se você tiver a instalação do gateway, continue fornecendo esses detalhes de conexão e, em seguida, escolha **criar**.

| Propriedade | Obrigatório | Descrição |
|----------|----------|-------------|
| **Conectar por meio do gateway local** | Sim | Aplica-se quando você deseja uma conexão local e mostra as propriedades de conexão local. |
| **Nome da Ligação** | Sim | O nome da conexão, por exemplo, "MyLogicApp-DB2-Connection" | 
| **Servidor** | Sim | O número da porta de dois-pontos de endereço ou alias para o servidor DB2, por exemplo, "myDB2server: 50000" <p><p>**Observação**: esse valor é uma cadeia de caracteres que representa um endereço TCP/IP ou alias, no formato IPv4 ou IPv6, seguido por dois-pontos e um número de porta TCP/IP. |
| **Base de Dados** | Sim | O nome do seu banco de dados <p><p>**Observação**: esse valor é uma cadeia de caracteres que representa um nome de banco de dados relacional DRDA (RDBNAM): <p>-O DB2 para z/OS aceita uma cadeia de caracteres de 16 bytes em que o banco de dados é conhecido como um "IBM DB2 para z/OS". <br>-DB2 para eu aceito uma cadeia de caracteres de 18 bytes em que o banco de dados é conhecido como um banco de dados relacional "IBM DB2 para i". <br>– O DB2 para LUW aceita uma cadeia de caracteres de 8 bytes. |
| **Autenticação** | Sim | O tipo de autenticação para sua conexão, por exemplo, "básico" <p><p>**Observação**: selecione esse valor na lista, que inclui Basic ou Windows (Kerberos). |
| **Nome de Utilizador** | Sim | Seu nome de usuário para o banco de dados <p><p>**Observação**: esse valor é uma cadeia de caracteres cujo comprimento é baseado no banco de dados específico: <p><p>-O DB2 para z/OS aceita uma cadeia de caracteres de 8 bytes. <br>-DB2 para eu aceito uma cadeia de caracteres de 10 bytes. <br>-O DB2 para Linux ou UNIX aceita uma cadeia de caracteres de 8 bytes. <br>– O DB2 para Windows aceita uma cadeia de caracteres de 30 bytes. |
| **Palavra-passe** | Sim | Sua senha para o banco de dados |
| **Gateway** | Sim | O nome do gateway de dados local instalado <p><p>**Observação**: selecione esse valor na lista, que inclui todos os gateways de dados instalados em sua assinatura e grupo de recursos do Azure. |
||||

Por exemplo:

![Detalhes da conexão para bancos de dados locais](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Exibir tabelas de saída

Para executar o aplicativo lógico manualmente, na barra de ferramentas do designer, escolha **executar**. Depois de concluir a execução do aplicativo lógico, você poderá exibir a saída da execução.

1. No menu do aplicativo lógico, selecione **visão geral**.

1. Em **Resumo**, na seção **histórico de execuções** , selecione a execução mais recente, que é o primeiro item na lista.

   ![Ver o histórico de execuções](./media/connectors-create-api-db2/run-history.png)

1. Em **execução do aplicativo lógico**, agora você pode examinar o status, as entradas e as saídas de cada etapa em seu aplicativo lógico.
Expanda a ação **obter tabelas** .

   ![Expandir ação](./media/connectors-create-api-db2/expand-action-step.png)

1. Para exibir as entradas, escolha **Mostrar entradas brutas**.

1. Para exibir as saídas, escolha **Mostrar saídas brutas**.

   As saídas incluem uma lista de tabelas.

   ![Exibir tabelas de saída](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Obter linha

Para buscar um registro em uma tabela de banco de dados DB2, use a ação **obter linha** em seu aplicativo lógico. Essa ação executa uma instrução de `SELECT WHERE` do DB2, por exemplo, `SELECT FROM AREA WHERE AREAID = '99999'`.

1. Se você nunca usou as ações do DB2 antes em seu aplicativo lógico, examine as etapas na seção [Adicionar a ação do DB2 – obter tabelas](#add-db2-action) , mas adicione a ação **obter linha** e, em seguida, retorne aqui para continuar.

   Depois de adicionar a ação **obter linha** , veja como seu exemplo de aplicativo lógico é exibido:

   ![Obter ação de linha](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Especifique valores para todas as propriedades necessárias (*). Depois de selecionar uma tabela, a ação mostra as propriedades relevantes que são específicas para os registros nessa tabela.

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome da tabela** | Sim | A tabela que tem o registro desejado, como "área" neste exemplo |
   | **ID da área** | Sim | A ID do registro desejado, como "99999" neste exemplo |
   ||||

   ![Selecionar tabela](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Quando terminar, na barra de ferramentas do designer, escolha **salvar**.

### <a name="view-output-row"></a>Exibir linha de saída

Para executar o aplicativo lógico manualmente, na barra de ferramentas do designer, escolha **executar**. Depois de concluir a execução do aplicativo lógico, você poderá exibir a saída da execução.

1. No menu do aplicativo lógico, selecione **visão geral**.

1. Em **Resumo**, na seção **histórico de execuções** , selecione a execução mais recente, que é o primeiro item na lista.

1. Em **execução do aplicativo lógico**, agora você pode examinar o status, as entradas e as saídas de cada etapa em seu aplicativo lógico.
Expanda a ação **obter linha** .

1. Para exibir as entradas, escolha **Mostrar entradas brutas**.

1. Para exibir as saídas, escolha **Mostrar saídas brutas**.

   As saídas incluem a linha especificada.

   ![Exibir linha de saída](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Obter linhas

Para buscar todos os registros em uma tabela de banco de dados DB2, use a ação **obter linhas** em seu aplicativo lógico. Essa ação executa uma instrução de `SELECT` do DB2, por exemplo, `SELECT * FROM AREA`.

1. Se você nunca usou as ações do DB2 antes em seu aplicativo lógico, examine as etapas na seção [Adicionar a ação do DB2 – obter tabelas](#add-db2-action) , mas adicione a ação **obter linhas** em vez disso e, em seguida, retorne aqui para continuar.

   Depois de adicionar a ação **obter linhas** , veja como seu exemplo de aplicativo lógico é exibido:

   ![Ação de obter linhas](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Abra a lista **nome da tabela** e, em seguida, selecione a tabela desejada, que é "área" neste exemplo:

   ![Selecionar tabela](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Para especificar um filtro ou uma consulta de resultados, escolha **Mostrar opções avançadas**.

1. Quando terminar, na barra de ferramentas do designer, escolha **salvar**.

### <a name="view-output-rows"></a>Exibir linhas de saída

Para executar o aplicativo lógico manualmente, na barra de ferramentas do designer, escolha **executar**. Depois de concluir a execução do aplicativo lógico, você poderá exibir a saída da execução.

1. No menu do aplicativo lógico, selecione **visão geral**.

1. Em **Resumo**, na seção **histórico de execuções** , selecione a execução mais recente, que é o primeiro item na lista.

1. Em **execução do aplicativo lógico**, agora você pode examinar o status, as entradas e as saídas de cada etapa em seu aplicativo lógico.
Expanda a ação **obter linhas** .

1. Para exibir as entradas, escolha **Mostrar entradas brutas**.

1. Para exibir as saídas, escolha **Mostrar saídas brutas**.

   As saídas incluem todos os registros na tabela especificada.

   ![Exibir linhas de saída](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Insert row

Para adicionar um único registro a uma tabela de banco de dados DB2, use a ação **Inserir linha** em seu aplicativo lógico. Essa ação executa uma instrução de `INSERT` do DB2, por exemplo, `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

1. Se você nunca usou as ações do DB2 antes em seu aplicativo lógico, examine as etapas na seção [Adicionar a ação do DB2 – obter tabelas](#add-db2-action) , mas adicione a ação **Inserir linha** e, em seguida, retorne aqui para continuar.

   Depois de adicionar a ação **Inserir linha** , veja como seu exemplo de aplicativo lógico é exibido:

   ![Inserir ação de linha](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Especifique valores para todas as propriedades necessárias (*). Depois de selecionar uma tabela, a ação mostra as propriedades relevantes que são específicas para os registros nessa tabela.

   Para este exemplo, aqui estão as propriedades:

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome da tabela** | Sim | A tabela onde adicionar o registro, como "área" |
   | **ID da área** | Sim | A ID da área a ser adicionada, como "99999" |
   | **Descrição da área** | Sim | A descrição da área a ser adicionada, como "área 99999" |
   | **ID da região** | Sim | A ID da região a ser adicionada, como "102" |
   |||| 

   Por exemplo:

   ![Selecionar tabela](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Quando terminar, na barra de ferramentas do designer, escolha **salvar**.

### <a name="view-insert-row-outputs"></a>Exibir saídas de linha de inserção

Para executar o aplicativo lógico manualmente, na barra de ferramentas do designer, escolha **executar**. Depois de concluir a execução do aplicativo lógico, você poderá exibir a saída da execução.

1. No menu do aplicativo lógico, selecione **visão geral**.

1. Em **Resumo**, na seção **histórico de execuções** , selecione a execução mais recente, que é o primeiro item na lista.

1. Em **execução do aplicativo lógico**, agora você pode examinar o status, as entradas e as saídas de cada etapa em seu aplicativo lógico.
Expanda a ação **Inserir linha** .

1. Para exibir as entradas, escolha **Mostrar entradas brutas**.

1. Para exibir as saídas, escolha **Mostrar saídas brutas**.

   As saídas incluem o registro que você adicionou à tabela especificada.

   ![Exibir saída com linha inserida](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Atualizar linha

Para atualizar um único registro em uma tabela de banco de dados DB2, use a ação **Atualizar linha** em seu aplicativo lógico. Essa ação executa uma instrução de `UPDATE` do DB2, por exemplo, `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`.

1. Se você nunca usou as ações do DB2 antes em seu aplicativo lógico, examine as etapas na seção [Adicionar a ação do DB2 – obter tabelas](#add-db2-action) , mas adicione a ação **Atualizar linha** e, em seguida, retorne aqui para continuar.

   Depois de adicionar a ação **Atualizar linha** , veja como seu exemplo de aplicativo lógico é exibido:

   ![Atualizar ação de linha](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Especifique valores para todas as propriedades necessárias (*). Depois de selecionar uma tabela, a ação mostra as propriedades relevantes que são específicas para os registros nessa tabela.

   Para este exemplo, aqui estão as propriedades:

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome da tabela** | Sim | A tabela onde atualizar o registro, como "área" |
   | **ID da linha** | Sim | A ID do registro a ser atualizado, como "99999" |
   | **ID da área** | Sim | A nova ID de área, como "99999" |
   | **Descrição da área** | Sim | A nova descrição da área, como "atualizado 99999" |
   | **ID da região** | Sim | A nova ID de região, como "102" |
   ||||

   Por exemplo:

   ![Selecionar tabela](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Quando terminar, na barra de ferramentas do designer, escolha **salvar**.

### <a name="view-update-row-outputs"></a>Exibir saídas de linha de atualização

Para executar o aplicativo lógico manualmente, na barra de ferramentas do designer, escolha **executar**. Depois de concluir a execução do aplicativo lógico, você poderá exibir a saída da execução.

1. No menu do aplicativo lógico, selecione **visão geral**.

1. Em **Resumo**, na seção **histórico de execuções** , selecione a execução mais recente, que é o primeiro item na lista.

1. Em **execução do aplicativo lógico**, agora você pode examinar o status, as entradas e as saídas de cada etapa em seu aplicativo lógico.
Expanda a ação **Atualizar linha** .

1. Para exibir as entradas, escolha **Mostrar entradas brutas**.

1. Para exibir as saídas, escolha **Mostrar saídas brutas**.

   As saídas incluem o registro que você atualizou na tabela especificada.

   ![Exibir saída com a linha atualizada](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Excluir linha

Para excluir um único registro de uma tabela de banco de dados DB2, use a ação **Excluir linha** em seu aplicativo lógico. Essa ação executa uma instrução de `DELETE` do DB2, por exemplo, `DELETE FROM AREA WHERE AREAID = '99999'`.

1. Se você nunca usou as ações do DB2 antes em seu aplicativo lógico, examine as etapas na seção [Adicionar a ação do DB2 – obter tabelas](#add-db2-action) , mas adicione a ação **Excluir linha** e, em seguida, retorne aqui para continuar.

   Depois de adicionar a ação **Excluir linha** , veja como seu exemplo de aplicativo lógico é exibido:

   ![Excluir ação de linha](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Especifique valores para todas as propriedades necessárias (*). Depois de selecionar uma tabela, a ação mostra as propriedades relevantes que são específicas para os registros nessa tabela.

   Para este exemplo, aqui estão as propriedades:

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome da tabela** | Sim | A tabela onde excluir o registro, como "área" |
   | **ID da linha** | Sim | A ID do registro a ser excluído, como "99999" |
   ||||

   Por exemplo:

   ![Selecionar tabela](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Quando terminar, na barra de ferramentas do designer, escolha **salvar**.

### <a name="view-delete-row-outputs"></a>Exibir saídas de linha de exclusão

Para executar o aplicativo lógico manualmente, na barra de ferramentas do designer, escolha **executar**. Depois de concluir a execução do aplicativo lógico, você poderá exibir a saída da execução.

1. No menu do aplicativo lógico, selecione **visão geral**.

1. Em **Resumo**, na seção **histórico de execuções** , selecione a execução mais recente, que é o primeiro item na lista.

1. Em **execução do aplicativo lógico**, agora você pode examinar o status, as entradas e as saídas de cada etapa em seu aplicativo lógico.
Expanda a ação **Excluir linha** .

1. Para exibir as entradas, escolha **Mostrar entradas brutas**.

1. Para exibir as saídas, escolha **Mostrar saídas brutas**.

   As saídas não incluem mais o registro que você excluiu da tabela especificada.

   ![Exibir saída sem linha excluída](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo OpenAPI (anteriormente Swagger) do conector, consulte a [página de referência do conector](/connectors/db2/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
