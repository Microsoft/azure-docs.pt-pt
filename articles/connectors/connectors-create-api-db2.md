---
title: Ligue ao IBM DB2 - Azure Logic Apps
description: Gerir os recursos com as APIs de REST do IBM DB2 e o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: plarsen, LADocs
ms.topic: article
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: c46fd1791751ebf912670875f9c8e18e8b0c5c40
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311310"
---
# <a name="manage-ibm-db2-resources-with-azure-logic-apps"></a>Gerir recursos do IBM DB2 no Azure Logic Apps

Com o Azure Logic Apps e o conector de DB2 da IBM, pode criar tarefas automatizadas e fluxos de trabalho com base nos recursos armazenados na base de dados DB2. Os fluxos de trabalho podem ligar aos recursos na sua base de dados, ler e listar as tabelas de base de dados, adicionar linhas, altere as linhas, eliminar linhas e muito mais. Pode incluir ações nas suas aplicações lógicas que obtém respostas da sua base de dados e disponibilizar a saída para outras ações.

Este artigo mostra como pode criar uma aplicação lógica que executa várias operações de base de dados. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="supported-platforms-and-versions"></a>Plataformas suportadas e versões

O conector de DB2 inclui um cliente da Microsoft que comunica com servidores remotos de DB2 através de uma rede TCP/IP. Pode utilizar este conector para acessar bancos de dados na cloud, como o IBM Bluemix dashDB ou IBM DB2 para Windows em execução na virtualização do Azure. Também pode aceder a bases de dados de DB2 no local depois de [instalar e configurar o gateway de dados no local](../logic-apps/logic-apps-gateway-connection.md).

O conector de DB2 da IBM oferece suporte a essas plataformas de IBM DB2 e versões, juntamente com o IBM DB2 compatíveis produtos, como o IBM Bluemix dashDB, que oferecem suporte a versões de Gestor de acesso de SQL de distribuídas relacional da base de dados arquitetura (DRDA) (SQLAM), 10 e 11:

| Plataforma | Versão | 
|----------|---------|
| IBM DB2 para z/OS | 11.1, 10.1 |
| IBM DB2 para eu | 7.3, 7.2, 7.1 |
| IBM DB2 para LUW | 11, 10.5 |
|||

## <a name="supported-database-operations"></a>Operações de base de dados suportados

O conector de DB2 da IBM oferece suporte a essas operações de base de dados, que mapeiam para as ações correspondentes no conector:

| Operação de base de dados | Ação de conector |
|--------------------|------------------|
| Tabelas de base de dados de lista | Obter tabelas |
| Leia uma linha, o uso de SELECT | Obter linha |
| Ler todas as linhas, o uso de SELECT | Obter linhas |
| Adicionar uma linha usando INSERT | Inserir linha |
| Editar uma linha com a ATUALIZAÇÃO | Atualizar linha |
| Remover uma linha para utilizar a eliminação | Eliminar linha |
|||

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>.

* IBM DB2 da base de dados, seja com base na cloud ou no local

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica em que deseja acessar seu banco de dados DB2. Este conector proporciona apenas ações como, por isso, para iniciar a sua aplicação lógica, selecione um acionador separado, por exemplo, o **periodicidade** acionador.
Os exemplos neste artigo utilizam o **periodicidade** acionador.

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Adicionar ação de DB2 - Get tabelas

1. Na [portal do Azure](https://portal.azure.com), abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. No acionador, escolha **novo passo**.

1. Na caixa de pesquisa, introduza "db2" como o filtro. Neste exemplo, sob a lista de ações, selecione a ação: **Obter tabelas (pré-visualização)**

   ![Ação de seleção](./media/connectors-create-api-db2/select-db2-action.png)

   Agora lhe for pedido para fornecer os detalhes de ligação da base de dados DB2.

1. Siga os passos para criar ligações para [bases de dados da cloud](#cloud-connection) ou [bases de dados locais](#on-premises-connection).

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Ligar à cloud DB2

Para configurar a sua ligação, forneça estes detalhes de ligação quando lhe for pedido, escolha **criar**e, em seguida, guarde a aplicação lógica:

| Propriedade | Necessário | Descrição |
|----------|----------|-------------|
| **Ligar através do gateway no local** | Não | Aplica-se apenas para ligações no local. |
| **Nome da Ligação** | Sim | O nome para a sua ligação, por exemplo, "MyLogicApp-DB2-ligação" |
| **Servidor** | Sim | O número de porta de dois pontos de alias ou endereço do servidor de DB2, por exemplo, "myDB2server.cloudapp.net:50000" <p><p>**Nota**: Este valor é uma cadeia de caracteres que representa um endereço de TCP/IP ou alias, no formato IPv4 ou IPv6, seguido por uma vírgula e um número de porta de TCP/IP. |
| **Base de Dados** | Sim | O nome da base de dados <p><p>**Nota**: Este valor é uma cadeia de caracteres que representa um nome DRDA da base de dados relacional (RDBNAM): <p>-DB2 para z/OS aceita uma cadeia de caracteres de 16 bytes em que a base de dados é conhecido como uma localização de "IBM DB2 para z/OS". <br>-DB2 para i aceita uma cadeia de caracteres de byte de 18 onde a base de dados é conhecido como um "IBM DB2 para eu" base de dados relacional. <br>-DB2 para LUW aceita uma cadeia de caracteres de 8 bytes. |
| **Nome de Utilizador** | Sim | O nome de utilizador para a base de dados <p><p>**Nota**: Este valor é uma cadeia cujo comprimento baseia-se a base de dados específico: <p><p>-DB2 para z/OS aceita uma cadeia de caracteres de 8 bytes. <br>-DB2 para i aceita uma cadeia de caracteres de byte de 10. <br>-DB2 para Linux ou UNIX aceita uma cadeia de caracteres de 8 bytes. <br>-DB2 para Windows aceita uma cadeia de caracteres de byte de 30. |
| **Palavra-passe** | Sim | A palavra-passe para a base de dados |
||||

Por exemplo:

![Detalhes de ligação para bases de dados com base na cloud](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Ligue ao DB2 no local

Antes de criar a ligação, já tem de ter o seu gateway de dados no local instalado. Caso contrário, não é possível concluir a configuração da sua conexão. Se tiver sua instalação do gateway, continuar com a prestação estes detalhes de ligação e, em seguida, escolha **criar**.

| Propriedade | Necessário | Descrição |
|----------|----------|-------------|
| **Ligar através do gateway no local** | Sim | Aplica-se quando pretender uma ligação no local e mostra os locais propriedades de ligação. |
| **Nome da Ligação** | Sim | O nome para a sua ligação, por exemplo, "MyLogicApp-DB2-ligação" | 
| **Servidor** | Sim | O número de porta de dois pontos de alias ou endereço do servidor de DB2, por exemplo, "myDB2server:50000" <p><p>**Nota**: Este valor é uma cadeia de caracteres que representa um endereço de TCP/IP ou alias, no formato IPv4 ou IPv6, seguido por uma vírgula e um número de porta de TCP/IP. |
| **Base de Dados** | Sim | O nome da base de dados <p><p>**Nota**: Este valor é uma cadeia de caracteres que representa um nome DRDA da base de dados relacional (RDBNAM): <p>-DB2 para z/OS aceita uma cadeia de caracteres de 16 bytes em que a base de dados é conhecido como uma localização de "IBM DB2 para z/OS". <br>-DB2 para i aceita uma cadeia de caracteres de byte de 18 onde a base de dados é conhecido como um "IBM DB2 para eu" base de dados relacional. <br>-DB2 para LUW aceita uma cadeia de caracteres de 8 bytes. |
| **Autenticação** | Sim | O tipo de autenticação para a sua ligação, por exemplo, "Basic" <p><p>**Nota**: Selecione este valor na lista, que inclui Basic ou o Windows (Kerberos). |
| **Nome de Utilizador** | Sim | O nome de utilizador para a base de dados <p><p>**Nota**: Este valor é uma cadeia cujo comprimento baseia-se a base de dados específico: <p><p>-DB2 para z/OS aceita uma cadeia de caracteres de 8 bytes. <br>-DB2 para i aceita uma cadeia de caracteres de byte de 10. <br>-DB2 para Linux ou UNIX aceita uma cadeia de caracteres de 8 bytes. <br>-DB2 para Windows aceita uma cadeia de caracteres de byte de 30. |
| **Palavra-passe** | Sim | A palavra-passe para a base de dados |
| **Gateway** | Sim | O nome para o seu gateway de dados instalado no local <p><p>**Nota**: Selecione este valor na lista, que inclui todos os gateways de dados instalado na sua subscrição do Azure e o grupo de recursos. |
||||

Por exemplo:

![Detalhes de ligação para bases de dados no local](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Tabelas de saída do modo de exibição

Para executar a aplicação lógica manualmente, na barra de ferramentas da estruturador, escolha **executar**. Após a conclusão da sua aplicação lógica em execução, pode ver o resultado da execução.

1. No menu da aplicação lógica, selecione **descrição geral**.

1. Sob **resumo**, na **histórico de execuções** secção, selecione a execução mais recente, o que é o primeiro item na lista.

   ![Ver o histórico de execuções](./media/connectors-create-api-db2/run-history.png)

1. Sob **execução da aplicação lógica**, agora pode rever o estado, entradas, e saídas para cada passo na sua aplicação lógica.
Expanda a **obter tabelas** ação.

   ![Expanda a ação](./media/connectors-create-api-db2/expand-action-step.png)

1. Para ver as entradas, escolha **Mostrar entradas em bruto**.

1. Para ver os resultados, escolha **Mostrar saídas em bruto**.

   Os resultados incluem uma lista de tabelas.

   ![Tabelas de saída do modo de exibição](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Obter linha

Para obter um registo na tabela de base de dados DB2, utilize o **obter linha** ação na sua aplicação lógica. Esta ação é executada uma DB2 `SELECT WHERE` instrução, por exemplo, `SELECT FROM AREA WHERE AREAID = '99999'`.

1. Se nunca tiver utilizado o DB2 ações antes na sua aplicação lógica, reveja os passos a [DB2 adicionar ação - tabelas de Get](#add-db2-action) secção, mas adicionar o **obter linha** ação em vez disso e, em seguida, volte aqui para continuar.

   Depois de adicionar o **obter linha** ação, eis como aparece a aplicação de lógica de exemplo:

   ![Obter ação de linha](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Especifica valores para todas as propriedades necessárias (*). Depois de selecionar uma tabela, a ação mostra as propriedades relevantes que são específicas para registos na tabela.

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da tabela** | Sim | A tabela que tenha o registo que pretende, como "Área", neste exemplo |
   | **ID de área** | Sim | O ID do registo que pretende, como "99999", neste exemplo |
   ||||

   ![Selecionar a tabela](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**.

### <a name="view-output-row"></a>Linha de saída do modo de exibição

Para executar a aplicação lógica manualmente, na barra de ferramentas da estruturador, escolha **executar**. Após a conclusão da sua aplicação lógica em execução, pode ver o resultado da execução.

1. No menu da aplicação lógica, selecione **descrição geral**.

1. Sob **resumo**, na **histórico de execuções** secção, selecione a execução mais recente, o que é o primeiro item na lista.

1. Sob **execução da aplicação lógica**, agora pode rever o estado, entradas, e saídas para cada passo na sua aplicação lógica.
Expanda a **obter linha** ação.

1. Para ver as entradas, escolha **Mostrar entradas em bruto**.

1. Para ver os resultados, escolha **Mostrar saídas em bruto**.

   Os resultados incluem a linha especificada.

   ![Linha de saída do modo de exibição](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Obter linhas

Para obter todos os registos de uma tabela de base de dados DB2, utilize o **obter linhas** ação na sua aplicação lógica. Esta ação é executada uma DB2 `SELECT` instrução, por exemplo, `SELECT * FROM AREA`.

1. Se nunca tiver utilizado o DB2 ações antes na sua aplicação lógica, reveja os passos a [DB2 adicionar ação - tabelas de Get](#add-db2-action) secção, mas adicionar o **obter linhas** ação em vez disso e, em seguida, volte aqui para continuar.

   Depois de adicionar o **obter linhas** ação, eis como aparece a aplicação de lógica de exemplo:

   ![Obter ação de linhas](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Abra o **nome da tabela** lista e, em seguida, selecione a tabela que pretende, que é a "Área" neste exemplo:

   ![Selecionar a tabela](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Para especificar um filtro ou a consulta para obter os resultados, escolha **Mostrar opções avançadas**.

1. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**.

### <a name="view-output-rows"></a>Linhas de saída do modo de exibição

Para executar a aplicação lógica manualmente, na barra de ferramentas da estruturador, escolha **executar**. Após a conclusão da sua aplicação lógica em execução, pode ver o resultado da execução.

1. No menu da aplicação lógica, selecione **descrição geral**.

1. Sob **resumo**, na **histórico de execuções** secção, selecione a execução mais recente, o que é o primeiro item na lista.

1. Sob **execução da aplicação lógica**, agora pode rever o estado, entradas, e saídas para cada passo na sua aplicação lógica.
Expanda a **obter linhas** ação.

1. Para ver as entradas, escolha **Mostrar entradas em bruto**.

1. Para ver os resultados, escolha **Mostrar saídas em bruto**.

   As saídas incluem todos os registros na tabela especificada.

   ![Linhas de saída do modo de exibição](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Inserir linha

Para adicionar um único registo numa tabela de base de dados DB2, utilize o **Inserir linha** ação na sua aplicação lógica. Esta ação é executada uma DB2 `INSERT` instrução, por exemplo, `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

1. Se nunca tiver utilizado o DB2 ações antes na sua aplicação lógica, reveja os passos a [DB2 adicionar ação - tabelas de Get](#add-db2-action) secção, mas adicionar o **Inserir linha** ação em vez disso e, em seguida, volte aqui para continuar.

   Depois de adicionar o **Inserir linha** ação, eis como aparece a aplicação de lógica de exemplo:

   ![Inserir linha ação](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Especifica valores para todas as propriedades necessárias (*). Depois de selecionar uma tabela, a ação mostra as propriedades relevantes que são específicas para registos na tabela.

   Neste exemplo, aqui estão as propriedades:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da tabela** | Sim | A tabela onde adicionar o registo, por exemplo, "Área" |
   | **ID de área** | Sim | O ID da área para adicionar, por exemplo, "99999" |
   | **Descrição de área** | Sim | A descrição para a área adicionar, por exemplo, "Área 99999" |
   | **ID de região** | Sim | O ID para a região para adicionar, por exemplo, "102" |
   |||| 

   Por exemplo:

   ![Selecionar a tabela](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**.

### <a name="view-insert-row-outputs"></a>Vista de inserir linha saídas

Para executar a aplicação lógica manualmente, na barra de ferramentas da estruturador, escolha **executar**. Após a conclusão da sua aplicação lógica em execução, pode ver o resultado da execução.

1. No menu da aplicação lógica, selecione **descrição geral**.

1. Sob **resumo**, na **histórico de execuções** secção, selecione a execução mais recente, o que é o primeiro item na lista.

1. Sob **execução da aplicação lógica**, agora pode rever o estado, entradas, e saídas para cada passo na sua aplicação lógica.
Expanda a **Inserir linha** ação.

1. Para ver as entradas, escolha **Mostrar entradas em bruto**.

1. Para ver os resultados, escolha **Mostrar saídas em bruto**.

   Os resultados incluem o registo que adicionou à sua tabela especificada.

   ![Saída de exibição com a linha inserida](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Atualizar linha

Para atualizar um único registo numa tabela de base de dados DB2, utilize o **atualizar linha** ação na sua aplicação lógica. Esta ação é executada uma DB2 `UPDATE` instrução, por exemplo, `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`.

1. Se nunca tiver utilizado o DB2 ações antes na sua aplicação lógica, reveja os passos a [DB2 adicionar ação - tabelas de Get](#add-db2-action) secção, mas adicionar o **atualizar linha** ação em vez disso e, em seguida, volte aqui para continuar.

   Depois de adicionar o **atualizar linha** ação, eis como aparece a aplicação de lógica de exemplo:

   ![Ação de atualização de linha](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Especifica valores para todas as propriedades necessárias (*). Depois de selecionar uma tabela, a ação mostra as propriedades relevantes que são específicas para registos na tabela.

   Neste exemplo, aqui estão as propriedades:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da tabela** | Sim | A tabela onde deve atualizar o registo, por exemplo, "Área" |
   | **ID de linha** | Sim | O ID do registo para atualizar, por exemplo, "99999" |
   | **ID de área** | Sim | O novo ID de área, como "99999" |
   | **Descrição de área** | Sim | A descrição da área novo, por exemplo, "Updated 99999" |
   | **ID de região** | Sim | O novo ID de região, por exemplo, "102" |
   ||||

   Por exemplo:

   ![Selecionar a tabela](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**.

### <a name="view-update-row-outputs"></a>Saídas de linha de atualização da vista

Para executar a aplicação lógica manualmente, na barra de ferramentas da estruturador, escolha **executar**. Após a conclusão da sua aplicação lógica em execução, pode ver o resultado da execução.

1. No menu da aplicação lógica, selecione **descrição geral**.

1. Sob **resumo**, na **histórico de execuções** secção, selecione a execução mais recente, o que é o primeiro item na lista.

1. Sob **execução da aplicação lógica**, agora pode rever o estado, entradas, e saídas para cada passo na sua aplicação lógica.
Expanda a **atualizar linha** ação.

1. Para ver as entradas, escolha **Mostrar entradas em bruto**.

1. Para ver os resultados, escolha **Mostrar saídas em bruto**.

   Os resultados incluem o registo atualizado na tabela especificada.

   ![Saída de exibição com a linha atualizada](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Eliminar linha

Para eliminar um único registo de uma tabela de base de dados DB2, utilize o **Eliminar linha** ação na sua aplicação lógica. Esta ação é executada uma DB2 `DELETE` instrução, por exemplo, `DELETE FROM AREA WHERE AREAID = '99999'`.

1. Se nunca tiver utilizado o DB2 ações antes na sua aplicação lógica, reveja os passos a [DB2 adicionar ação - tabelas de Get](#add-db2-action) secção, mas adicionar o **Eliminar linha** ação em vez disso e, em seguida, volte aqui para continuar.

   Depois de adicionar o **Eliminar linha** ação, eis como aparece a aplicação de lógica de exemplo:

   ![Eliminar ação da linha](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Especifica valores para todas as propriedades necessárias (*). Depois de selecionar uma tabela, a ação mostra as propriedades relevantes que são específicas para registos na tabela.

   Neste exemplo, aqui estão as propriedades:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da tabela** | Sim | A tabela onde pretende eliminar o registo, por exemplo, "Área" |
   | **ID de linha** | Sim | O ID do registo para eliminar, como "99999" |
   ||||

   Por exemplo:

   ![Selecionar a tabela](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**.

### <a name="view-delete-row-outputs"></a>Vista de eliminar linha saídas

Para executar a aplicação lógica manualmente, na barra de ferramentas da estruturador, escolha **executar**. Após a conclusão da sua aplicação lógica em execução, pode ver o resultado da execução.

1. No menu da aplicação lógica, selecione **descrição geral**.

1. Sob **resumo**, na **histórico de execuções** secção, selecione a execução mais recente, o que é o primeiro item na lista.

1. Sob **execução da aplicação lógica**, agora pode rever o estado, entradas, e saídas para cada passo na sua aplicação lógica.
Expanda a **Eliminar linha** ação.

1. Para ver as entradas, escolha **Mostrar entradas em bruto**.

1. Para ver os resultados, escolha **Mostrar saídas em bruto**.

   As saídas já não incluem o registo que é eliminado da sua tabela especificada.

   ![Saída do modo de exibição sem linha excluída](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como disparadores, ações e limites, conforme descrito pelo OpenAPI do conector (anteriormente Swagger) de ficheiros, consulte a [página de referência do conector](/connectors/db2/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
