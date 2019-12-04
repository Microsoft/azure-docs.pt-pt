---
title: Conectar-se ao banco de dados do IBM Informix
description: Gerenciar recursos com APIs REST do IBM Informix e aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/26/2016
tags: connectors
ms.openlocfilehash: d6f768bc76d19c0aa21a245c008a4b05588f8f43
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789727"
---
# <a name="get-started-with-the-informix-connector"></a>Introdução ao conector do Informix
O Microsoft Connector para Informix conecta aplicativos lógicos a recursos armazenados em um banco de dados IBM Informix. O conector do Informix inclui um cliente da Microsoft para se comunicar com computadores remotos do servidor Informix em uma rede TCP/IP. Isso inclui bancos de dados na nuvem, como o IBM Informix para Windows em execução na virtualização do Azure e bancos de dados locais usando o gateway de data local. Consulte a lista de plataformas e versões do IBM Informix [com suporte](connectors-create-api-informix.md#supported-informix-platforms-and-versions) (neste tópico).

O conector dá suporte às seguintes operações de banco de dados:

* Listar tabelas de banco de dados
* Ler uma linha usando SELECT
* Ler todas as linhas usando SELECT
* Adicionar uma linha usando INSERT
* Alterar uma linha usando UPDATE
* Remover uma linha usando DELETE

Este tópico mostra como usar o conector em um aplicativo lógico para processar operações de banco de dados.

Para saber mais sobre os aplicativos lógicos, consulte [criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Ações disponíveis
Este conector dá suporte às seguintes ações de aplicativo lógico:

* Getaptos
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Listar tabelas
A criação de um aplicativo lógico para qualquer operação é composta por várias etapas executadas por meio do portal do Microsoft Azure.

No aplicativo lógico, você pode adicionar uma ação para listar tabelas em um banco de dados Informix. Essa ação instrui o conector a processar uma instrução de esquema Informix, como `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **quadro inicial do Azure**, selecione **+** (sinal de adição), **Web + celular**e, em seguida, **aplicativo lógico**.
2. Insira o **nome**, como `InformixgetTables`, **assinatura**, **grupo de recursos**, **local**e plano do serviço de **aplicativo**. Selecione **fixar no painel**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de aplicativos lógicos**, selecione **LogicApp em branco** na lista de **modelos** .
2. Na lista de **gatilhos** , selecione **recorrência**. 
3. No gatilho de **recorrência** , selecione **Editar**, selecione a lista suspensa **frequência** para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**.  
4. Selecione a caixa **+ nova etapa** e, em seguida, selecione **Adicionar uma ação**.
5. Na lista **ações** , digite **Informix** na caixa **de edição Pesquisar mais ações** e, em seguida, selecione **Informix-obter tabelas (versão prévia)** .
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. No painel de configuração **Informix-obter tabelas** , marque a **caixa de seleção** para habilitar a **conexão por meio do gateway de dados local**. Observe que as configurações mudam de nuvem para local.
   
   * Digite o valor do **servidor**, na forma de endereço ou número da porta de dois-pontos do alias. Por exemplo, digite `ibmserver01:9089`.
   * Digite o valor do **banco de dados**. Por exemplo, digite `nwind`.
   * Selecione valor para **autenticação**. Por exemplo, selecione **básico**.
   * Digite o valor para **username**. Por exemplo, digite `informix`.
   * Digite o valor para **senha**. Por exemplo, digite `Password1`.
   * Selecione valor para **Gateway**. Por exemplo, selecione **datagateway01**.
7. Selecione **criar**e, em seguida, selecione **salvar**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. Na folha **InformixgetTables** , na lista **todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
9. Na folha **execução do aplicativo lógico** , selecione **detalhes da execução**. Na lista **ação** , selecione **Get_tables**. Consulte o valor de **status**, que deve ser **bem-sucedido**. Selecione o **link entradas** para exibir as entradas. Selecione o **link saídas**e exiba as saídas; que deve incluir uma lista de tabelas.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Criar as ligações
Esse conector dá suporte a conexões com o banco de dados local e na nuvem usando as propriedades de conexão a seguir. 

| Propriedade | Descrição |
| --- | --- |
| servidor |Necessário. Aceita um valor de cadeia de caracteres representando um endereço TCP/IP ou alias, no formato IPv4 ou IPv6, seguido (delimitado por dois-pontos) por um número de porta TCP/IP. |
| base de dados |Necessário. Aceita um valor de cadeia de caracteres representando um nome de banco de dados relacional DRDA (RDBNAM). A Informix aceita uma cadeia de caracteres de 128 bytes (o banco de dados é conhecido como um nome de banco de dados IBM Informix (dbname)). |
| autenticação |Opcional. Aceita um valor de item de lista, básico ou Windows (Kerberos). |
| o nome de utilizador |Necessário. Aceita um valor de cadeia de caracteres. |
| palavra-passe |Necessário. Aceita um valor de cadeia de caracteres. |
| Gateway |Necessário. Aceita um valor de item de lista, representando o gateway de dados local definido para aplicativos lógicos dentro do grupo de armazenamento. |

## <a name="create-the-on-premises-gateway-connection"></a>Criar a conexão de gateway local
Esse conector pode acessar um banco de dados Informix local usando o gateway de data local. Consulte os tópicos do gateway para obter mais informações. 

1. No painel configuração de **gateways** , marque a **caixa de seleção** para habilitar a **conexão via gateway**. Consulte as configurações alterar de nuvem para local.
2. Digite o valor do **servidor**, na forma de endereço ou número da porta de dois-pontos do alias. Por exemplo, digite `ibmserver01:9089`.
3. Digite o valor do **banco de dados**. Por exemplo, digite `nwind`.
4. Selecione valor para **autenticação**. Por exemplo, selecione **básico**.
5. Digite o valor para **username**. Por exemplo, digite `informix`.
6. Digite o valor para **senha**. Por exemplo, digite `Password1`.
7. Selecione valor para **Gateway**. Por exemplo, selecione **datagateway01**.
8. Selecione **criar** para continuar. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Criar a conexão de nuvem
Esse conector pode acessar um banco de dados Informix de nuvem. 

1. No painel de configuração **gateways** , deixe a **caixa de seleção** desabilitada (não clicada) **conectar via gateway**. 
2. Digite o valor para o **nome da conexão**. Por exemplo, digite `hisdemo2`.
3. Digite o valor do **nome do servidor Informix**, na forma de endereço ou número da porta de dois-pontos do alias. Por exemplo, digite `hisdemo2.cloudapp.net:9089`.
4. Digite o valor do **nome do banco de dados Informix**. Por exemplo, digite `nwind`.
5. Digite o valor para **username**. Por exemplo, digite `informix`.
6. Digite o valor para **senha**. Por exemplo, digite `Password1`.
7. Selecione **criar** para continuar. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Buscar todas as linhas usando SELECT
Você pode criar uma ação de aplicativo lógico para buscar todas as linhas na tabela Informix. Essa ação instrui o conector a processar uma instrução SELECT do Informix, como `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **quadro inicial do Azure**, selecione **+** (sinal de adição), **Web + celular**e, em seguida, **aplicativo lógico**.
2. Insira o **nome** (por exemplo, "**InformixgetRows**"), a **assinatura**, o **grupo de recursos**, o **local**e o plano do **serviço de aplicativo**. Selecione **fixar no painel**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de aplicativos lógicos**, selecione **LogicApp em branco** na lista de **modelos** .
2. Na lista de **gatilhos** , selecione **recorrência**. 
3. No gatilho de **recorrência** , selecione **Editar**, selecione a lista suspensa **frequência** para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**. 
4. Selecione a caixa **+ nova etapa** e, em seguida, selecione **Adicionar uma ação**.
5. Na lista **ações** , digite **Informix** na caixa **de edição Pesquisar mais ações** e, em seguida, selecione **Informix-obter linhas (versão prévia)** .
6. Na ação **obter linhas (versão prévia)** , selecione **alterar conexão**.
7. No painel de configuração **conexões** , selecione **criar novo**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. No painel de configuração **gateways** , deixe a **caixa de seleção** desabilitada (não clicada) **conectar via gateway**.
   
   * Digite o valor para o **nome da conexão**. Por exemplo, digite `HISDEMO2`.
   * Digite o valor do **nome do servidor Informix**, na forma de endereço ou número da porta de dois-pontos do alias. Por exemplo, digite `HISDEMO2.cloudapp.net:9089`.
   * Digite o valor do **nome do banco de dados Informix**. Por exemplo, digite `NWIND`.
   * Digite o valor para **username**. Por exemplo, digite `informix`.
   * Digite o valor para **senha**. Por exemplo, digite `Password1`.
9. Selecione **criar** para continuar.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. Na lista **nome da tabela** , selecione a **seta para baixo**e selecione **área**.
11. Opcionalmente, selecione **Mostrar opções avançadas** para especificar opções de consulta.
12. Selecione **Guardar**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. Na folha **InformixgetRows** , na lista **todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
14. Na folha **execução do aplicativo lógico** , selecione **detalhes da execução**. Na lista **ação** , selecione **Get_rows**. Consulte o valor de **status**, que deve ser **bem-sucedido**. Selecione o **link entradas** para exibir as entradas. Selecione o **link saídas**e exiba as saídas; que deve incluir uma lista de linhas.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Adicionar uma linha usando INSERT
Você pode criar uma ação de aplicativo lógico para adicionar uma linha em uma tabela do Informix. Essa ação instrui o conector a processar uma instrução de inserção da Informix, como `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **quadro inicial do Azure**, selecione **+** (sinal de adição), **Web + celular**e, em seguida, **aplicativo lógico**.
2. Insira o **nome**, como `InformixinsertRow`, **assinatura**, **grupo de recursos**, **local**e plano do serviço de **aplicativo**. Selecione **fixar no painel**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de aplicativos lógicos**, selecione **LogicApp em branco** na lista de **modelos** .
2. Na lista de **gatilhos** , selecione **recorrência**. 
3. No gatilho de **recorrência** , selecione **Editar**, selecione a lista suspensa **frequência** para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**. 
4. Selecione a caixa **+ nova etapa** e, em seguida, selecione **Adicionar uma ação**.
5. Na lista **ações** , digite **Informix** na caixa **de edição Pesquisar mais ações** e, em seguida, selecione **Informix – Inserir linha (visualização)** .
6. Na ação **obter linhas (versão prévia)** , selecione **alterar conexão**. 
7. No painel de configuração **conexões** , selecione para selecionar uma conexão. Por exemplo, selecione **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Na lista **nome da tabela** , selecione a **seta para baixo**e selecione **área**.
9. Insira valores para todas as colunas necessárias (consulte o asterisco vermelho). Por exemplo, digite `99999` para **areaid**, digite `Area 99999`e digite `102` para **RegionID**. 
10. Selecione **Guardar**.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. Na folha **InformixinsertRow** , na lista **todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
12. Na folha **execução do aplicativo lógico** , selecione **detalhes da execução**. Na lista **ação** , selecione **Get_rows**. Consulte o valor de **status**, que deve ser **bem-sucedido**. Selecione o **link entradas** para exibir as entradas. Selecione o **link saídas**e exiba as saídas; que deve incluir a nova linha.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Buscar uma linha usando SELECT
Você pode criar uma ação de aplicativo lógico para buscar uma linha em uma tabela do Informix. Essa ação instrui o conector a processar uma instrução SELECT WHERE do Informix, como `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **quadro inicial do Azure**, selecione **+** (sinal de adição), **Web + celular**e, em seguida, **aplicativo lógico**.
2. Insira o **nome**, como `InformixgetRow`, **assinatura**, **grupo de recursos**, **local**e plano do serviço de **aplicativo**. Selecione **fixar no painel**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de aplicativos lógicos**, selecione **LogicApp em branco** na lista de **modelos** .
2. Na lista de **gatilhos** , selecione **recorrência**. 
3. No gatilho de **recorrência** , selecione **Editar**, selecione a lista suspensa **frequência** para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**. 
4. Selecione a caixa **+ nova etapa** e, em seguida, selecione **Adicionar uma ação**.
5. Na lista **ações** , digite **Informix** na caixa **de edição Pesquisar mais ações** e, em seguida, selecione **Informix-obter linhas (versão prévia)** .
6. Na ação **obter linhas (versão prévia)** , selecione **alterar conexão**. 
7. No painel configurações de **conexões** , selecione para selecionar uma conexão existente. Por exemplo, selecione **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Na lista **nome da tabela** , selecione a **seta para baixo**e selecione **área**.
9. Insira valores para todas as colunas necessárias (consulte o asterisco vermelho). Por exemplo, digite `99999` para **dddid**. 
10. Opcionalmente, selecione **Mostrar opções avançadas** para especificar opções de consulta.
11. Selecione **Guardar**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. Na folha **InformixgetRow** , na lista **todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
13. Na folha **execução do aplicativo lógico** , selecione **detalhes da execução**. Na lista **ação** , selecione **Get_rows**. Consulte o valor de **status**, que deve ser **bem-sucedido**. Selecione o **link entradas** para exibir as entradas. Selecione o **link saídas**e exiba as saídas; que deve incluir linha.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Alterar uma linha usando UPDATE
Você pode criar uma ação de aplicativo lógico para alterar uma linha em uma tabela do Informix. Essa ação instrui o conector a processar uma instrução de atualização da Informix, como `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **quadro inicial do Azure**, selecione **+** (sinal de adição), **Web + celular**e, em seguida, **aplicativo lógico**.
2. Insira o **nome**, como `InformixupdateRow`, **assinatura**, **grupo de recursos**, **local**e plano do serviço de **aplicativo**. Selecione **fixar no painel**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de aplicativos lógicos**, selecione **LogicApp em branco** na lista de **modelos** .
2. Na lista de **gatilhos** , selecione **recorrência**. 
3. No gatilho de **recorrência** , selecione **Editar**, selecione a lista suspensa **frequência** para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**. 
4. Selecione a caixa **+ nova etapa** e, em seguida, selecione **Adicionar uma ação**.
5. Na lista **ações** , digite **Informix** na caixa **de edição Pesquisar mais ações** e selecione **Informix-atualizar linha (versão prévia)** .
6. Na ação **obter linhas (versão prévia)** , selecione **alterar conexão**. 
7. No painel configurações de **conexões** , selecione para selecionar uma conexão existente. Por exemplo, selecione **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Na lista **nome da tabela** , selecione a **seta para baixo**e selecione **área**.
9. Insira valores para todas as colunas necessárias (consulte o asterisco vermelho). Por exemplo, digite `99999` para **areaid**, digite `Updated 99999`e digite `102` para **RegionID**. 
10. Selecione **Guardar**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. Na folha **InformixupdateRow** , na lista **todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
12. Na folha **execução do aplicativo lógico** , selecione **detalhes da execução**. Na lista **ação** , selecione **Get_rows**. Consulte o valor de **status**, que deve ser **bem-sucedido**. Selecione o **link entradas** para exibir as entradas. Selecione o **link saídas**e exiba as saídas; que deve incluir a nova linha.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Remover uma linha usando DELETE
Você pode criar uma ação de aplicativo lógico para remover uma linha em uma tabela do Informix. Essa ação instrui o conector a processar uma instrução DELETE do Informix, como `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **quadro inicial do Azure**, selecione **+** (sinal de adição), **Web + celular**e, em seguida, **aplicativo lógico**.
2. Insira o **nome**, como `InformixdeleteRow`, **assinatura**, **grupo de recursos**, **local**e plano do serviço de **aplicativo**. Selecione **fixar no painel**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de aplicativos lógicos**, selecione **LogicApp em branco** na lista de **modelos** .
2. Na lista de **gatilhos** , selecione **recorrência**. 
3. No gatilho de **recorrência** , selecione **Editar**, selecione a lista suspensa **frequência** para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**. 
4. Selecione a caixa **+ nova etapa** e, em seguida, selecione **Adicionar uma ação**.
5. Na lista **ações** , digite **Informix** na caixa **de edição Pesquisar mais ações** e selecione **Informix-excluir linha (visualização)** .
6. Na ação **obter linhas (versão prévia)** , selecione **alterar conexão**. 
7. No painel configurações de **conexões** , selecione uma conexão existente. Por exemplo, selecione **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Na lista **nome da tabela** , selecione a **seta para baixo**e selecione **área**.
9. Insira valores para todas as colunas necessárias (consulte o asterisco vermelho). Por exemplo, digite `99999` para **dddid**. 
10. Selecione **Guardar**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. Na folha **InformixdeleteRow** , na lista **todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
12. Na folha **execução do aplicativo lógico** , selecione **detalhes da execução**. Na lista **ação** , selecione **Get_rows**. Consulte o valor de **status**, que deve ser **bem-sucedido**. Selecione o **link entradas** para exibir as entradas. Selecione o **link saídas**e exiba as saídas; que deve incluir a linha excluída.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Plataformas e versões Informix com suporte
Este conector dá suporte às seguintes versões do IBM Informix, quando configurado para dar suporte a conexões de cliente DRDA (arquitetura de banco de dados relacional distribuído).

* IBM Informix 12,1
* IBM Informix 11,7

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba todos os gatilhos e ações definidos no Swagger e também veja os limites nos [detalhes do conector](/connectors/informix/). 

## <a name="next-steps"></a>Passos seguintes
[Crie um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). Explore os outros conectores disponíveis nos aplicativos lógicos em nossa [lista de APIs](apis-list.md).

