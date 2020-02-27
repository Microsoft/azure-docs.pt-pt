---
title: Acesso e gestão de recursos IBM DB2
description: Leia, edite, atualize e gere os recursos do IBM DB2 através da construção de fluxos de trabalho automatizados utilizando aplicações lógicas azure
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 32b482607827ee4420e39b1936586d64f9ea3139
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651387"
---
# <a name="access-and-manage-ibm-db2-resources-by-using-azure-logic-apps"></a>Acesso e gestão de recursos IBM DB2 utilizando aplicações lógicas azure

Com [as Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e o [conector IBM DB2,](/connectors/db2/)pode criar tarefas e fluxos de trabalho automatizados com base nos recursos armazenados na sua base de dados DB2. Os seus fluxos de trabalho podem ligar-se aos recursos da sua base de dados, ler e listar as tabelas da sua base de dados, adicionar linhas, alterar linhas, eliminar linhas e muito mais. Pode incluir ações nas suas aplicações lógicas que obtêm respostas da sua base de dados e disponibilizam a saída para outras ações.

Este artigo mostra como pode criar uma aplicação lógica que executa várias operações de base de dados. Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

## <a name="supported-platforms-and-versions"></a>Plataformas e versões suportadas

O conector DB2 inclui um cliente da Microsoft que comunica com servidores DB2 remotos através de uma rede TCP/IP. Pode utilizar este conector para aceder a bases de dados em nuvem, como o IBM DB2 para windows em execução na virtualização do Azure. Também pode aceder às bases de dados DB2 no local depois de [instalar e configurar o portal de dados no local.](../logic-apps/logic-apps-gateway-connection.md)

O conector IBM DB2 suporta estas plataformas e versões IBM DB2 juntamente com produtos compatíveis com IBM DB2 que suportam as versões 10 e 11 do Gestor de Acesso Relacional Distribuído (DRDA) SQL:

| Plataforma | Versão | 
|----------|---------|
| IBM DB2 para z/OS | 11.1, 10.1 |
| IBM DB2 para i | 7.3, 7.2, 7.1 |
| IBM DB2 para LUW | 11, 10.5 |
|||

## <a name="supported-database-operations"></a>Operações de base de dados suportadas

O conector IBM DB2 suporta estas operações de base de dados, que mapeiam as ações correspondentes no conector:

| Operação de base de dados | Ação do conector |
|--------------------|------------------|
| Lista de tabelas de bases de dados | Obter mesas |
| Leia uma linha usando SELECT | Obter fila |
| Leia todas as linhas usando SELECT | Obter filas |
| Adicione uma linha usando o INSERT | Insira a linha |
| Editar uma linha usando update | Linha de atualização |
| Remova uma linha utilizando o DELETE | Excluir linha |
|||

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma base de dados IBM DB2, baseada em nuvem ou no local

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua base de dados DB2. Este conector fornece apenas ações, de modo a iniciar a sua aplicação lógica, selecione um gatilho separado, por exemplo, o gatilho **Recurrence.**
Os exemplos deste artigo usam o gatilho **da Recorrência.**

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Adicionar ação DB2 - Obter tabelas

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Sob o gatilho, escolha **novo passo**.

1. Na caixa de pesquisa, introduza "db2" como filtro. Para este exemplo, na lista de ações, selecione esta ação: **Obtenha tabelas (Pré-visualização)**

   ![Selecione ação](./media/connectors-create-api-db2/select-db2-action.png)

   Foi-lhe pedido que forneça detalhes de ligação para a sua base de dados DB2.

1. Siga os passos para criar ligações para bases de [dados na nuvem](#cloud-connection) ou bases de dados no [local](#on-premises-connection).

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Ligar à nuvem DB2

Para configurar a sua ligação, forneça estes detalhes de ligação quando solicitado, escolha **Criar**, e, em seguida, guarde a sua aplicação lógica:

| Propriedade | Necessário | Descrição |
|----------|----------|-------------|
| **Ligar através do gateway no local** | Não | Aplica-se apenas para ligações no local. |
| **Nome da Ligação** | Sim | O nome da sua ligação, por exemplo, "MyLogicApp-DB2-connection" |
| **Servidor** | Sim | O endereço ou pseudónimo número de porta do cólon para o seu servidor DB2, por exemplo, "myDB2server.cloudapp.net:50000" <p><p>**Nota:** Este valor é uma cadeia que representa um endereço TCP/IP ou pseudónimo, quer no formato IPv4 quer iPv6, seguido de um cólon e um número de porta TCP/IP. |
| **Base de Dados** | Sim | O nome da sua base de dados <p><p>**Nota:** Este valor é uma cadeia que representa uma nomede base de dados relacional DRDA (RDBNAM): <p>- DB2 para z/OS aceita uma cadeia de 16 bytes onde a base de dados é conhecida como uma localização "IBM DB2 para z/OS". <br>- DB2 para i aceita uma cadeia de 18 bytes onde a base de dados é conhecida como uma base de dados relacional "IBM DB2 for i". <br>- DB2 para LUW aceita uma corda de 8 bytes. |
| **Nome de Utilizador** | Sim | O seu nome de utilizador para a base de dados <p><p>**Nota:** Este valor é uma cadeia cujo comprimento se baseia na base de dados específica: <p><p>- DB2 para z/OS aceita uma corda de 8 bytes. <br>- DB2 pois aceito uma corda de 10 bytes. <br>- DB2 para Linux ou UNIX aceita uma corda de 8 bytes. <br>- DB2 para Windows aceita uma corda de 30 bytes. |
| **Palavra-passe** | Sim | A sua senha para a base de dados |
||||

Por exemplo:

![Detalhes de ligação para bases de dados baseadas em nuvem](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Ligar-se ao DB2 no local

Antes de criar a sua ligação, já deve ter o seu portal de dados instalado no local. Caso contrário, não pode terminar de estabelecer a sua ligação. Se tiver a instalação do gateway, continue a fornecer estes detalhes de ligação e, em seguida, escolha **Criar**.

| Propriedade | Necessário | Descrição |
|----------|----------|-------------|
| **Ligar através do gateway no local** | Sim | Aplica-se quando deseja uma ligação no local e mostra as propriedades de ligação no local. |
| **Nome da Ligação** | Sim | O nome da sua ligação, por exemplo, "MyLogicApp-DB2-connection" | 
| **Servidor** | Sim | O endereço ou pseudónimo número da porta do cólon para o seu servidor DB2, por exemplo, "myDB2server:50000" <p><p>**Nota:** Este valor é uma cadeia que representa um endereço TCP/IP ou pseudónimo, quer no formato IPv4 quer iPv6, seguido de um cólon e um número de porta TCP/IP. |
| **Base de Dados** | Sim | O nome da sua base de dados <p><p>**Nota:** Este valor é uma cadeia que representa uma nomede base de dados relacional DRDA (RDBNAM): <p>- DB2 para z/OS aceita uma cadeia de 16 bytes onde a base de dados é conhecida como uma localização "IBM DB2 para z/OS". <br>- DB2 para i aceita uma cadeia de 18 bytes onde a base de dados é conhecida como uma base de dados relacional "IBM DB2 for i". <br>- DB2 para LUW aceita uma corda de 8 bytes. |
| **Autenticação** | Sim | O tipo de autenticação para a sua ligação, por exemplo, "Básico" <p><p>**Nota:** Selecione este valor da lista, que inclui Basic ou Windows (Kerberos). |
| **Nome de Utilizador** | Sim | O seu nome de utilizador para a base de dados <p><p>**Nota:** Este valor é uma cadeia cujo comprimento se baseia na base de dados específica: <p><p>- DB2 para z/OS aceita uma corda de 8 bytes. <br>- DB2 pois aceito uma corda de 10 bytes. <br>- DB2 para Linux ou UNIX aceita uma corda de 8 bytes. <br>- DB2 para Windows aceita uma corda de 30 bytes. |
| **Palavra-passe** | Sim | A sua senha para a base de dados |
| **Gateway** | Sim | O nome para o seu gateway de dados instalado no local <p><p>**Nota**: Selecione este valor da lista, que inclui todos os gateways de dados instalados dentro do seu grupo de subscrição e recursos Azure. |
||||

Por exemplo:

![Detalhes de ligação para bases de dados no local](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Ver tabelas de saída

Para executar a sua aplicação lógica manualmente, na barra de ferramentas de design, escolha **Run**. Depois de a sua aplicação lógica terminar a funcionar, pode ver a saída a partir da corrida.

1. No menu de aplicativos lógicos, selecione **Visão Geral**.

1. Em **resumo,** na secção **de história runs,** selecione a mais recente execução, que é o primeiro item da lista.

   ![Ver o histórico de execuções](./media/connectors-create-api-db2/run-history.png)

1. No âmbito **da aplicação Logic,** pode agora rever o estado, as inputs e as saídas para cada passo da sua aplicação lógica.
Expanda a ação das **tabelas Get.**

   ![Expandir a ação](./media/connectors-create-api-db2/expand-action-step.png)

1. Para visualizar as inputs, escolha **Mostrar inputs crus**.

1. Para ver as saídas, escolha **Mostrar saídas cruas**.

   As saídas incluem uma lista de tabelas.

   ![Ver tabelas de saída](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Obter fila

Para obter um disco numa tabela de bases de dados DB2, use a ação da **linha Get** na sua aplicação lógica. Esta ação executa uma declaração de `SELECT WHERE` DB2, por exemplo, `SELECT FROM AREA WHERE AREAID = '99999'`.

1. Se nunca usou ações de DB2 na sua aplicação lógica, reveja os passos na [ação Add DB2 - Obtenha tabelas,](#add-db2-action) mas adicione a ação da **linha Get** e, em seguida, volte aqui para continuar.

   Depois de adicionar a ação da **linha Get,** eis como aparece a sua aplicação lógica de exemplo:

   ![Obter ação de linha](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Especifique valores para todas as propriedades necessárias (*). Depois de selecionar uma tabela, a ação mostra as propriedades relevantes que são específicas para registos nessa tabela.

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da mesa** | Sim | A tabela que tem o registo que deseja, como "AREA" neste exemplo |
   | **ID da área** | Sim | A identificação para o registo que deseja, como "99999" neste exemplo |
   ||||

   ![Tabela selecionada](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Quando terminar, na barra de ferramentas de design, escolha **Save**.

### <a name="view-output-row"></a>Ver linha de saída

Para executar a sua aplicação lógica manualmente, na barra de ferramentas de design, escolha **Run**. Depois de a sua aplicação lógica terminar a funcionar, pode ver a saída a partir da corrida.

1. No menu de aplicativos lógicos, selecione **Visão Geral**.

1. Em **resumo,** na secção **de história runs,** selecione a mais recente execução, que é o primeiro item da lista.

1. No âmbito **da aplicação Logic,** pode agora rever o estado, as inputs e as saídas para cada passo da sua aplicação lógica.
Expanda a ação da **linha Get.**

1. Para visualizar as inputs, escolha **Mostrar inputs crus**.

1. Para ver as saídas, escolha **Mostrar saídas cruas**.

   As saídas incluem a sua linha especificada.

   ![Ver linha de saída](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Obter filas

Para obter todos os registos numa tabela de bases de dados DB2, use a ação **get rows** na sua aplicação lógica. Esta ação executa uma declaração de `SELECT` DB2, por exemplo, `SELECT * FROM AREA`.

1. Se nunca usou ações de DB2 na sua aplicação lógica, reveja os passos na [ação Add DB2 - Obtenha](#add-db2-action) **tabelas,** mas adicione a ação das linhas Get e, em seguida, volte aqui para continuar.

   Depois de adicionar a ação **get rows,** eis como aparece a sua aplicação lógica de exemplo:

   ![Obter ação de linhas](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Abra a lista de **nomes** da tabela e, em seguida, selecione a tabela que deseja, que é "AREA" neste exemplo:

   ![Tabela selecionada](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Para especificar um filtro ou consulta de resultados, escolha **Mostrar opções avançadas**.

1. Quando terminar, na barra de ferramentas de design, escolha **Save**.

### <a name="view-output-rows"></a>Ver linhas de saída

Para executar a sua aplicação lógica manualmente, na barra de ferramentas de design, escolha **Run**. Depois de a sua aplicação lógica terminar a funcionar, pode ver a saída a partir da corrida.

1. No menu de aplicativos lógicos, selecione **Visão Geral**.

1. Em **resumo,** na secção **de história runs,** selecione a mais recente execução, que é o primeiro item da lista.

1. No âmbito **da aplicação Logic,** pode agora rever o estado, as inputs e as saídas para cada passo da sua aplicação lógica.
Expanda a ação **get rows.**

1. Para visualizar as inputs, escolha **Mostrar inputs crus**.

1. Para ver as saídas, escolha **Mostrar saídas cruas**.

   As saídas incluem todos os registos na sua tabela especificada.

   ![Ver linhas de saída](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Insira a linha

Para adicionar um único disco a uma tabela de bases de dados DB2, utilize a ação da **linha Insert** na sua aplicação lógica. Esta ação executa uma declaração de `INSERT` DB2, por exemplo, `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

1. Se nunca usou ações de DB2 na sua aplicação lógica, reveja os passos na [ação Add DB2 - Obtenha tabelas,](#add-db2-action) mas adicione a ação da **linha Insert** e, em seguida, volte aqui para continuar.

   Depois de adicionar a ação da **linha Insert,** eis como aparece a sua aplicação lógica de exemplo:

   ![Inserir ação de linha](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Especifique valores para todas as propriedades necessárias (*). Depois de selecionar uma tabela, a ação mostra as propriedades relevantes que são específicas para registos nessa tabela.

   Para este exemplo, aqui estão as propriedades:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da mesa** | Sim | A tabela onde adicionar o recorde, como "AREA" |
   | **ID da área** | Sim | O ID para a área a adicionar, como "99999" |
   | **Descrição da área** | Sim | A descrição para a área a adicionar, como "Área 99999" |
   | **ID da região** | Sim | O ID para a região adicionar, como "102" |
   |||| 

   Por exemplo:

   ![Tabela selecionada](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Quando terminar, na barra de ferramentas de design, escolha **Save**.

### <a name="view-insert-row-outputs"></a>Ver inserir saídas de linha

Para executar a sua aplicação lógica manualmente, na barra de ferramentas de design, escolha **Run**. Depois de a sua aplicação lógica terminar a funcionar, pode ver a saída a partir da corrida.

1. No menu de aplicativos lógicos, selecione **Visão Geral**.

1. Em **resumo,** na secção **de história runs,** selecione a mais recente execução, que é o primeiro item da lista.

1. No âmbito **da aplicação Logic,** pode agora rever o estado, as inputs e as saídas para cada passo da sua aplicação lógica.
Expanda a ação da **linha Insert.**

1. Para visualizar as inputs, escolha **Mostrar inputs crus**.

1. Para ver as saídas, escolha **Mostrar saídas cruas**.

   As saídas incluem o registo que adicionou à sua tabela especificada.

   ![Ver saída com linha inserida](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Linha de atualização

Para atualizar um único disco numa tabela de bases de dados DB2, utilize a ação da **linha Update** na sua aplicação lógica. Esta ação executa uma declaração de `UPDATE` DB2, por exemplo, `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`.

1. Se nunca usou ações de DB2 na sua aplicação lógica, reveja os passos na [ação Add DB2 - Obtenha tabelas,](#add-db2-action) mas adicione a ação da **linha Update** e, em seguida, volte aqui para continuar.

   Depois de adicionar a ação da **linha Update,** eis como aparece a sua aplicação lógica de exemplo:

   ![Atualizar a ação da linha](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Especifique valores para todas as propriedades necessárias (*). Depois de selecionar uma tabela, a ação mostra as propriedades relevantes que são específicas para registos nessa tabela.

   Para este exemplo, aqui estão as propriedades:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da mesa** | Sim | A tabela onde atualizar o registo, como "AREA" |
   | **ID da linha** | Sim | O ID para que o registo atualização, como "99999" |
   | **ID da área** | Sim | A nova área ID, como "99999" |
   | **Descrição da área** | Sim | A nova descrição da área, como "Atualizado 99999" |
   | **ID da região** | Sim | A nova id da região, como "102" |
   ||||

   Por exemplo:

   ![Tabela selecionada](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Quando terminar, na barra de ferramentas de design, escolha **Save**.

### <a name="view-update-row-outputs"></a>Ver saídas de linha de atualização

Para executar a sua aplicação lógica manualmente, na barra de ferramentas de design, escolha **Run**. Depois de a sua aplicação lógica terminar a funcionar, pode ver a saída a partir da corrida.

1. No menu de aplicativos lógicos, selecione **Visão Geral**.

1. Em **resumo,** na secção **de história runs,** selecione a mais recente execução, que é o primeiro item da lista.

1. No âmbito **da aplicação Logic,** pode agora rever o estado, as inputs e as saídas para cada passo da sua aplicação lógica.
Expanda a ação da **linha Update.**

1. Para visualizar as inputs, escolha **Mostrar inputs crus**.

1. Para ver as saídas, escolha **Mostrar saídas cruas**.

   As saídas incluem o registo que atualizou na tabela especificada.

   ![Ver saída com linha atualizada](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Excluir linha

Para eliminar um único registo de uma tabela de bases de dados DB2, utilize a ação da **linha Delete** na sua aplicação lógica. Esta ação executa uma declaração de `DELETE` DB2, por exemplo, `DELETE FROM AREA WHERE AREAID = '99999'`.

1. Se nunca usou ações de DB2 na sua aplicação lógica, reveja os passos na [ação Add DB2 - Obtenha tabelas,](#add-db2-action) mas adicione a ação da **linha Delete** e, em seguida, volte aqui para continuar.

   Depois de adicionar a ação da **linha Delete,** eis como aparece a sua aplicação lógica de exemplo:

   ![Eliminar a ação da linha](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Especifique valores para todas as propriedades necessárias (*). Depois de selecionar uma tabela, a ação mostra as propriedades relevantes que são específicas para registos nessa tabela.

   Para este exemplo, aqui estão as propriedades:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da mesa** | Sim | A tabela onde apagar o registo, como "AREA" |
   | **ID da linha** | Sim | O ID para que o registo apague, como "99999" |
   ||||

   Por exemplo:

   ![Tabela selecionada](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Quando terminar, na barra de ferramentas de design, escolha **Save**.

### <a name="view-delete-row-outputs"></a>Ver eliminar saídas de linhas

Para executar a sua aplicação lógica manualmente, na barra de ferramentas de design, escolha **Run**. Depois de a sua aplicação lógica terminar a funcionar, pode ver a saída a partir da corrida.

1. No menu de aplicativos lógicos, selecione **Visão Geral**.

1. Em **resumo,** na secção **de história runs,** selecione a mais recente execução, que é o primeiro item da lista.

1. No âmbito **da aplicação Logic,** pode agora rever o estado, as inputs e as saídas para cada passo da sua aplicação lógica.
Expanda a ação da **linha Delete.**

1. Para visualizar as inputs, escolha **Mostrar inputs crus**.

1. Para ver as saídas, escolha **Mostrar saídas cruas**.

   As saídas já não incluem o registo que apagou da tabela especificada.

   ![Ver saída sem linha eliminada](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como gatilhos, ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/db2/).

> [!NOTE]
> Para aplicações lógicas num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão do conector com o rótulo ISE utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
