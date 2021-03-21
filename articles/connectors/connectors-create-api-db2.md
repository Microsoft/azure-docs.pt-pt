---
title: Acesso e gestão dos recursos IBM DB2
description: Leia, edite, atualize e gere os recursos ibm DB2 construindo fluxos de trabalho automatizados usando Apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 11/19/2020
tags: connectors
ms.openlocfilehash: 765bb66b572f0c046222cfb617fe4caa80925256
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967407"
---
# <a name="access-and-manage-ibm-db2-resources-by-using-azure-logic-apps"></a>Aceda e gere os recursos ibm DB2 utilizando apps Azure Logic

Com [as Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o [conector IBM DB2,](/connectors/db2/)pode criar tarefas e fluxos de trabalho automatizados com base nos recursos armazenados na sua base de dados DB2. Os seus fluxos de trabalho podem ligar-se aos recursos da sua base de dados, ler e listar as tabelas da base de dados, adicionar linhas, alterar linhas, eliminar linhas e muito mais. Pode incluir ações nas suas aplicações lógicas que obtenham respostas a partir da sua base de dados e disponibilize a saída para outras ações.

Este artigo mostra como pode criar uma aplicação lógica que executa várias operações de base de dados. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="supported-platforms-and-versions"></a>Plataformas e versões apoiadas

O conector DB2 inclui um cliente da Microsoft que comunica com servidores DB2 remotos através de uma rede TCP/IP. Pode utilizar este conector para aceder a bases de dados em nuvem, como o IBM DB2, para windows em execução na virtualização do Azure. Também pode aceder às bases de dados DB2 no local depois de [instalar e configurar o portal de dados no local.](../logic-apps/logic-apps-gateway-connection.md)

O conector IBM DB2 suporta estas plataformas e versões IBM DB2 juntamente com produtos compatíveis com IBM DB2 que suportam as versões 10 e 11 do Gestor de Acesso SqLL (DRDA) do Gestor de Acesso Da DLAM Distribuídos( DRDA)

| Plataforma | Versão | 
|----------|---------|
| IBM DB2 para z/OS | 12, 11.1, 10.1 |
| IBM DB2 para i | 7.3, 7.2, 7.1 |
| IBM DB2 para LUW | 11, 10.5 |
|||

## <a name="supported-database-operations"></a>Operações de base de dados suportadas

O conector IBM DB2 suporta estas operações de base de dados, que mapeiam as ações correspondentes no conector:

| Operação de base de dados | Ação do conector |
|--------------------|------------------|
| Tabelas de bases de dados de listas | Obter mesas |
| Leia uma linha usando SELECT | Obter fila |
| Leia todas as linhas usando SELECT | Obter filas |
| Adicione uma linha usando INSERT | Inserir linha |
| Editar uma linha usando UPDATE | Linha de atualização |
| Remover uma linha usando DELETE | Eliminar linha |
|||

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma base de dados IBM DB2, baseada em nuvem ou no local

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua base de dados DB2. Este conector fornece apenas ações, por isso, para iniciar a sua aplicação lógica, selecione um gatilho separado, por exemplo, o gatilho **de Recorrência.**
Os exemplos deste artigo utilizam o gatilho **de Recorrência.**

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Adicionar ação DB2 - Obter tabelas

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer, se não já estiver aberta.

1. Sob o gatilho, escolha **novo passo.**

1. Na caixa de pesquisa, introduza "db2" como filtro. Para este exemplo, na lista de ações, selecione esta ação: **Obter tabelas (Pré-visualização)**

   ![Selecione ação](./media/connectors-create-api-db2/select-db2-action.png)

   Foi-lhe agora solicitado que forneça detalhes de ligação para a sua base de dados DB2.

1. Siga os passos para a criação de ligações para [bases de dados](#cloud-connection) de nuvem ou [bases de dados no local.](#on-premises-connection)

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Ligue-se à nuvem DB2

Para configurar a sua ligação, forneça estes detalhes de conexão quando solicitado, escolha **Criar** e, em seguida, guarde a sua aplicação lógica:

| Propriedade | Necessário | Descrição |
|----------|----------|-------------|
| **Ligar através do portal no local** | No | Aplica-se apenas a ligações no local. |
| **Nome de conexão** | Yes | O nome da sua ligação, por exemplo, "MyLogicApp-DB2-connection" |
| **Servidor** | Yes | O endereço ou número da porta do cólon para o seu servidor DB2, por exemplo, "myDB2server.cloudapp.net:50000" <p><p>**Nota:** Este valor é uma cadeia que representa um endereço TCP/IP ou pseudónimo, quer no formato IPv4 quer IPv6, seguido de um cólon e um número de porta TCP/IP. |
| **Base de dados** | Yes | O nome da sua base de dados <p><p>**Nota:** Este valor é uma cadeia que representa um nome de base de dados relacional DRDA (RDBNAM): <p>- DB2 para z/OS aceita uma cadeia de 16 bytes onde a base de dados é conhecida como uma localização "IBM DB2 para z/OS". <br>- DB2 para i aceita uma cadeia de 18 bytes onde a base de dados é conhecida como uma base de dados relacional "IBM DB2 para i". <br>- DB2 para LUW aceita uma corda de 8 bytes. |
| **Nome de Utilizador** | Yes | O seu nome de utilizador para a base de dados <p><p>**Nota:** Este valor é uma cadeia cujo comprimento se baseia na base de dados específica: <p><p>- DB2 para z/OS aceita uma corda de 8 bytes. <br>- DB2 para eu aceitar uma corda de 10 bytes. <br>- DB2 para Linux ou UNIX aceita uma corda de 8 bytes. <br>- DB2 para Windows aceita uma cadeia de 30 bytes. |
| **Palavra-passe** | Yes | A sua senha para a base de dados |
||||

Por exemplo:

![Detalhes de conexão para bases de dados baseadas em nuvem](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Ligar ao DB2 no local

Antes de criar a sua ligação, já deve ter o seu portal de dados no local instalado. Caso contrário, não pode terminar de configurar a sua ligação. Se tiver a sua instalação gateway, continue a fornecer estes detalhes de conexão e, em seguida, escolha **Criar**.

| Propriedade | Necessário | Descrição |
|----------|----------|-------------|
| **Ligar através do portal no local** | Yes | Aplica-se quando pretende uma ligação no local e mostra as propriedades de ligação no local. |
| **Nome de conexão** | Yes | O nome da sua ligação, por exemplo, "MyLogicApp-DB2-connection" | 
| **Servidor** | Yes | O endereço ou número da porta do cólon para o seu servidor DB2, por exemplo, "myDB2server:50000" <p><p>**Nota:** Este valor é uma cadeia que representa um endereço TCP/IP ou pseudónimo, quer no formato IPv4 quer IPv6, seguido de um cólon e um número de porta TCP/IP. |
| **Base de dados** | Yes | O nome da sua base de dados <p><p>**Nota:** Este valor é uma cadeia que representa um nome de base de dados relacional DRDA (RDBNAM): <p>- DB2 para z/OS aceita uma cadeia de 16 bytes onde a base de dados é conhecida como uma localização "IBM DB2 para z/OS". <br>- DB2 para i aceita uma cadeia de 18 bytes onde a base de dados é conhecida como uma base de dados relacional "IBM DB2 para i". <br>- DB2 para LUW aceita uma corda de 8 bytes. |
| **Autenticação** | Yes | O tipo de autenticação para a sua ligação, por exemplo, "Básico" <p><p>**Nota:** Selecione este valor da lista, que inclui Básico ou Windows (Kerberos). |
| **Nome de Utilizador** | Yes | O seu nome de utilizador para a base de dados <p><p>**Nota:** Este valor é uma cadeia cujo comprimento se baseia na base de dados específica: <p><p>- DB2 para z/OS aceita uma corda de 8 bytes. <br>- DB2 para eu aceitar uma corda de 10 bytes. <br>- DB2 para Linux ou UNIX aceita uma corda de 8 bytes. <br>- DB2 para Windows aceita uma cadeia de 30 bytes. |
| **Palavra-passe** | Yes | A sua senha para a base de dados |
| **Gateway** | Yes | O nome do seu portal de dados instalado no local <p><p>**Nota:** Selecione este valor da lista, que inclui todos os gateways de dados instalados dentro do seu grupo de subscrição e recursos Azure. |
||||

Por exemplo:

![Detalhes de ligação para bases de dados no local](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Ver tabelas de saída

Para executar a sua aplicação lógica manualmente, na barra de ferramentas do designer, escolha **Executar**. Depois de a sua aplicação lógica terminar de funcionar, pode ver a saída a partir da execução.

1. No menu de aplicativos logico, selecione **Overview**.

1. Em **Resumo**, na secção **história de Runs,** selecione a mais recente execução, que é o primeiro item da lista.

   ![Ver o histórico de execuções](./media/connectors-create-api-db2/run-history.png)

1. No **âmbito da aplicação Logic,** pode agora rever o estado, entradas e saídas para cada passo na sua aplicação lógica.
Expandir a ação **de tabelas Get.**

   ![Expandir a ação](./media/connectors-create-api-db2/expand-action-step.png)

1. Para ver as entradas, escolha **mostrar entradas cruas**.

1. Para visualizar as saídas, escolha **Mostrar saídas cruas**.

   As saídas incluem uma lista de tabelas.

   ![Ver tabelas de saída](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Obter fila

Para obter um registo numa tabela de bases de dados DB2, utilize a ação **de linha Get** na sua aplicação lógica. Esta ação executa uma declaração DB2, `SELECT WHERE` por exemplo, `SELECT FROM AREA WHERE AREAID = '99999'` .

1. Se nunca usou ações DB2 antes na sua aplicação lógica, reveja os passos na [ação Add DB2 - Obtenha](#add-db2-action) a secção de tabelas, mas adicione a ação **da linha Get** em vez disso, e depois volte aqui para continuar.

   Depois de adicionar a ação **Get row,** eis como aparece a sua aplicação lógica de exemplo:

   ![Obtenha ação de linha](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Especifique os valores de todas as propriedades necessárias (*). Depois de selecionar uma tabela, a ação mostra as propriedades relevantes que são específicas dos registos nessa tabela.

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da mesa** | Yes | A tabela que tem o registo que deseja, como "AREA" neste exemplo |
   | **ID de área** | Yes | O ID para o registo que deseja, como "99999" neste exemplo |
   ||||

   ![Screenshot que mostra a ação "Get row (Preview)" com a lista de "Nome de mesa" aberta e o valor "AREA" selecionado.](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Quando terminar, na barra de ferramentas do designer, escolha **Save**.

### <a name="view-output-row"></a>Ver linha de saída

Para executar a sua aplicação lógica manualmente, na barra de ferramentas do designer, escolha **Executar**. Depois de a sua aplicação lógica terminar de funcionar, pode ver a saída a partir da execução.

1. No menu de aplicativos logico, selecione **Overview**.

1. Em **Resumo**, na secção **história de Runs,** selecione a mais recente execução, que é o primeiro item da lista.

1. No **âmbito da aplicação Logic,** pode agora rever o estado, entradas e saídas para cada passo na sua aplicação lógica.
Expandir a ação **get row.**

1. Para ver as entradas, escolha **mostrar entradas cruas**.

1. Para visualizar as saídas, escolha **Mostrar saídas cruas**.

   As saídas incluem a sua linha especificada.

   ![Ver linha de saída](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Obter filas

Para obter todos os registos numa tabela de bases de dados DB2, utilize a ação **Get rows** na sua aplicação lógica. Esta ação executa uma declaração DB2, `SELECT` por exemplo, `SELECT * FROM AREA` .

1. Se nunca usou ações DB2 antes na sua aplicação lógica, reveja os passos na [ação Add DB2 - Obtenha](#add-db2-action) a secção de **tabelas,** mas adicione a ação get rows e, em seguida, volte aqui para continuar.

   Depois de adicionar a ação **Get rows,** eis como aparece a sua aplicação lógica de exemplo:

   ![Obter ação de linhas](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Abra a lista **de nomes** de tabela e, em seguida, selecione a tabela desejada, que é "AREA" neste exemplo:

   ![Screenshot que mostra a ação "Obter linha (Pré-visualização)" com o valor "AREA" selecionado na lista "Table name".](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Para especificar um filtro ou consulta para obter resultados, escolha **escolha opções avançadas do Show**.

1. Quando terminar, na barra de ferramentas do designer, escolha **Save**.

### <a name="view-output-rows"></a>Ver linhas de saída

Para executar a sua aplicação lógica manualmente, na barra de ferramentas do designer, escolha **Executar**. Depois de a sua aplicação lógica terminar de funcionar, pode ver a saída a partir da execução.

1. No menu de aplicativos logico, selecione **Overview**.

1. Em **Resumo**, na secção **história de Runs,** selecione a mais recente execução, que é o primeiro item da lista.

1. No **âmbito da aplicação Logic,** pode agora rever o estado, entradas e saídas para cada passo na sua aplicação lógica.
Expandir a ação **Get rows.**

1. Para ver as entradas, escolha **mostrar entradas cruas**.

1. Para visualizar as saídas, escolha **Mostrar saídas cruas**.

   As saídas incluem todos os registos na sua tabela especificada.

   ![Ver linhas de saída](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Inserir linha

Para adicionar um único registo a uma tabela de base de dados DB2, utilize a ação **da linha Insert** na sua aplicação lógica. Esta ação executa uma declaração DB2, `INSERT` por exemplo, `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)` .

1. Se nunca usou ações DB2 antes na sua aplicação lógica, reveja os passos na [ação Add DB2 - Obtenha](#add-db2-action) a secção de tabelas, mas adicione a ação da **linha Insert,** e volte aqui para continuar.

   Depois de adicionar a ação **da linha Insira,** eis como aparece a sua aplicação lógica de exemplo:

   ![Inserir ação de linha](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Especifique os valores de todas as propriedades necessárias (*). Depois de selecionar uma tabela, a ação mostra as propriedades relevantes que são específicas dos registos nessa tabela.

   Para este exemplo, aqui estão as propriedades:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da mesa** | Yes | A tabela onde adicionar o recorde, como "AREA" |
   | **ID de área** | Yes | O ID para a área a adicionar, como "99999" |
   | **Descrição da área** | Yes | A descrição para a área a adicionar, como "Área 99999" |
   | **ID da região** | Yes | O ID para a região a adicionar, como "102" |
   |||| 

   Por exemplo:

   ![Screenshot que mostra o Designer de Aplicações Lógicas com a ação "Inserir linha (Pré-visualização)" e exemplo valores de propriedade.](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Quando terminar, na barra de ferramentas do designer, escolha **Save**.

### <a name="view-insert-row-outputs"></a>Ver inserir saídas de linha

Para executar a sua aplicação lógica manualmente, na barra de ferramentas do designer, escolha **Executar**. Depois de a sua aplicação lógica terminar de funcionar, pode ver a saída a partir da execução.

1. No menu de aplicativos logico, selecione **Overview**.

1. Em **Resumo**, na secção **história de Runs,** selecione a mais recente execução, que é o primeiro item da lista.

1. No **âmbito da aplicação Logic,** pode agora rever o estado, entradas e saídas para cada passo na sua aplicação lógica.
Expandir a ação **da linha De Inserção.**

1. Para ver as entradas, escolha **mostrar entradas cruas**.

1. Para visualizar as saídas, escolha **Mostrar saídas cruas**.

   As saídas incluem o registo que adicionou à sua tabela especificada.

   ![Ver saída com linha inserida](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Linha de atualização

Para atualizar um único registo numa tabela de base de dados DB2, utilize a ação **da linha Update** na sua aplicação lógica. Esta ação executa uma declaração DB2, `UPDATE` por exemplo, `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)` .

1. Se nunca usou ações DB2 antes na sua aplicação lógica, reveja os passos na [ação Add DB2 - Obtenha](#add-db2-action) a secção de tabelas, mas adicione a ação da linha **Update** e, em seguida, volte aqui para continuar.

   Depois de adicionar a ação **da linha Update,** eis como aparece a sua aplicação lógica de exemplo:

   ![Atualizar ação da linha](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Especifique os valores de todas as propriedades necessárias (*). Depois de selecionar uma tabela, a ação mostra as propriedades relevantes que são específicas dos registos nessa tabela.

   Para este exemplo, aqui estão as propriedades:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da mesa** | Yes | A tabela onde atualizar o registo, como "AREA" |
   | **ID de linha** | Yes | O ID para o registo a atualizar, como "99999" |
   | **ID de área** | Yes | O novo ID da área, como "99999" |
   | **Descrição da área** | Yes | A nova descrição da área, como "Atualizado 99999" |
   | **ID da região** | Yes | O novo ID da região, como o "102" |
   ||||

   Por exemplo:

   ![Screenshot que mostra o Designer de Aplicações Lógicas com a ação "Update row (Preview)" onde seleciona uma tabela.](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Quando terminar, na barra de ferramentas do designer, escolha **Save**.

### <a name="view-update-row-outputs"></a>Ver saídas de linha de atualização

Para executar a sua aplicação lógica manualmente, na barra de ferramentas do designer, escolha **Executar**. Depois de a sua aplicação lógica terminar de funcionar, pode ver a saída a partir da execução.

1. No menu de aplicativos logico, selecione **Overview**.

1. Em **Resumo**, na secção **história de Runs,** selecione a mais recente execução, que é o primeiro item da lista.

1. No **âmbito da aplicação Logic,** pode agora rever o estado, entradas e saídas para cada passo na sua aplicação lógica.
Expandir a ação **da linha Update.**

1. Para ver as entradas, escolha **mostrar entradas cruas**.

1. Para visualizar as saídas, escolha **Mostrar saídas cruas**.

   As saídas incluem o registo atualizado na sua tabela especificada.

   ![Ver saída com linha atualizada](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Eliminar linha

Para eliminar um único registo de uma tabela de bases de dados DB2, utilize a ação **eliminar a linha** na sua aplicação lógica. Esta ação executa uma declaração DB2, `DELETE` por exemplo, `DELETE FROM AREA WHERE AREAID = '99999'` .

1. Se nunca usou ações DB2 antes na sua aplicação lógica, reveja os passos na [ação Add DB2 - Obtenha](#add-db2-action) a secção de tabelas, mas adicione a ação da linha **Delete** e, em seguida, volte aqui para continuar.

   Depois de adicionar a ação **da linha Delete,** eis como aparece a sua aplicação lógica de exemplo:

   ![Apagar ação de linha](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Especifique os valores de todas as propriedades necessárias (*). Depois de selecionar uma tabela, a ação mostra as propriedades relevantes que são específicas dos registos nessa tabela.

   Para este exemplo, aqui estão as propriedades:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da mesa** | Yes | A tabela onde apagar o registo, como "AREA" |
   | **ID de linha** | Yes | O ID para que o registo apague, como "99999" |
   ||||

   Por exemplo:

   ![Screenshot que mostra o Designer de Aplicações Lógicas com a ação "Delete row (Preview)" onde seleciona uma tabela para eliminar.](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Quando terminar, na barra de ferramentas do designer, escolha **Save**.

### <a name="view-delete-row-outputs"></a>Ver eliminar saídas de linha

Para executar a sua aplicação lógica manualmente, na barra de ferramentas do designer, escolha **Executar**. Depois de a sua aplicação lógica terminar de funcionar, pode ver a saída a partir da execução.

1. No menu de aplicativos logico, selecione **Overview**.

1. Em **Resumo**, na secção **história de Runs,** selecione a mais recente execução, que é o primeiro item da lista.

1. No **âmbito da aplicação Logic,** pode agora rever o estado, entradas e saídas para cada passo na sua aplicação lógica.
Expandir a ação **da linha Delete.**

1. Para ver as entradas, escolha **mostrar entradas cruas**.

1. Para visualizar as saídas, escolha **Mostrar saídas cruas**.

   As saídas já não incluem o registo que apagou da sua tabela especificada.

   ![Ver saída sem linha eliminada](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como gatilhos, ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/db2/).

> [!NOTE]
> Para aplicações lógicas num ambiente de [serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão com rótulo ISE deste conector utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)

