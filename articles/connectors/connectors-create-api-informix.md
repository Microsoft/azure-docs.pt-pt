---
title: Ligar à base de dados IBM Informix - Azure Logic Apps | Documentos da Microsoft
description: Gerir os recursos com as APIs de REST do IBM Informix e o Azure Logic Apps
author: gplarsen
manager: jeconnoc
ms.author: plarsen
ms.date: 09/26/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 6004c02f190bbfcf374b3b5d2a5c478f0e52c961
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58165905"
---
# <a name="get-started-with-the-informix-connector"></a>Começar com o conector do Informix
Conector do Microsoft para Informix liga-se aplicações lógicas para recursos armazenados numa base de dados IBM Informix. O conector do Informix inclui um cliente da Microsoft para comunicar com computadores remotos de servidor Informix através de uma rede TCP/IP. Isso inclui bases de dados de cloud, como o IBM Informix para o Windows em execução na virtualização do Azure e no local bases de dados com o gateway de dados no local. Consulte a [suportado lista](connectors-create-api-informix.md#supported-informix-platforms-and-versions) de plataformas de IBM Informix e versões (deste tópico).

O conector suporta as seguintes operações de base de dados:

* Tabelas de base de dados de lista
* Leia uma linha, o uso de SELECT
* Ler todas as linhas, o uso de SELECT
* Adicionar uma linha usando INSERT
* Alterar uma linha com a ATUALIZAÇÃO
* Remover uma linha para utilizar a eliminação

Este tópico mostra-lhe como utilizar o conector numa aplicação lógica para operações de banco de dados do processo.

Para saber mais sobre o Logic Apps, veja [criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Ações disponíveis
Este conector suporta as seguintes ações de aplicação lógica:

* Getables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Listar as tabelas
Criar uma aplicação lógica para qualquer operação é composto por várias etapas realizadas através do portal do Microsoft Azure.

Na sua aplicação lógica, pode adicionar uma ação para tabelas de lista numa base de dados Informix. Esta ação instrui o conector para processar uma instrução de esquema do Informix, tais como `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. Na **quadro de início do Azure**, selecione **+** (sinal), **Web + móvel**e, em seguida **aplicação lógica**.
2. Introduza o **Name**, tal como `InformixgetTables`, **subscrição**, **grupo de recursos**, **localização**, e **serviço de aplicações Planear**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. Na **estruturador de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista.
2. Na **acionadores** lista, selecione **periodicidade**. 
3. Na **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione  **Intervalo** digitar **7**.  
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. Na **ações** lista, escreva **informix** no **procurar mais ações** caixa de edição e, em seguida, selecione **Informix - Get tabelas (pré-visualização)**.
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. Na **Informix - tabelas de Get** painel de configuração, selecione **caixa de verificação** para ativar **ligar através do gateway de dados no local**. Tenha em atenção que as definições de alterar de cloud para o local.
   
   * Escreva o valor para **servidor**, na forma de endereço ou alias de número de porta de dois pontos. Por exemplo, escreva `ibmserver01:9089`.
   * Escreva o valor para **base de dados**. Por exemplo, escreva `nwind`.
   * Selecione o valor para **autenticação**. Por exemplo, seleccione **básica**.
   * Escreva o valor para **nome de utilizador**. Por exemplo, escreva `informix`.
   * Escreva o valor para **palavra-passe**. Por exemplo, escreva `Password1`.
   * Selecione o valor para **Gateway**. Por exemplo, seleccione **datagateway01**.
7. Selecione **Create**e, em seguida, selecione **guardar**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. Na **InformixgetTables** painel, dentro do **todas as execuções** lista sob **resumo**, selecione o item listado primeiro (execução mais recente).
9. Na **execução da aplicação lógica** painel, selecione **detalhes da execução**. Dentro de **ação** lista, selecione **Get_tables**. Ver o valor para **Status**, que deve ser **Succeeded**. Selecione o **ligação de entradas** para ver as entradas. Selecione o **ligação de saídas**e ver os resultados; que deve incluir uma lista de tabelas.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Criar as ligações
Este conector suporta ligações de base de dados no local e na cloud utilizando as seguintes propriedades de ligação. 

| Propriedade | Descrição |
| --- | --- |
| servidor |Necessário. Aceita um valor de cadeia de caracteres que representa um endereço de TCP/IP ou o alias, no formato IPv4 ou IPv6, seguido (vírgula delimitada por) por um número de porta de TCP/IP. |
| base de dados |Necessário. Aceita um valor de cadeia de caracteres que representa um nome DRDA da base de dados relacional (RDBNAM). Informix aceita uma cadeia de caracteres de 128 bytes (a base de dados é conhecido como um nome de base de dados IBM Informix (dbname)). |
| autenticação |Opcional. Aceita um valor de item de lista, Basic ou o Windows (kerberos). |
| o nome de utilizador |Necessário. Aceita um valor de cadeia de caracteres. |
| palavra-passe |Necessário. Aceita um valor de cadeia de caracteres. |
| gateway |Necessário. Aceita um valor de item de lista, que representa o gateway de dados no local definido para aplicações lógicas dentro do grupo de armazenamento. |

## <a name="create-the-on-premises-gateway-connection"></a>Criar no local de ligação de gateway
Este conector pode aceder a uma base de dados de Informix no local com o gateway de dados no local. Consulte os tópicos de gateway para obter mais informações. 

1. Na **Gateways** painel de configuração, selecione **caixa de verificação** para ativar **ligar através do gateway**. Ver as definições que alterar de cloud para o local.
2. Escreva o valor para **servidor**, na forma de endereço ou alias de número de porta de dois pontos. Por exemplo, escreva `ibmserver01:9089`.
3. Escreva o valor para **base de dados**. Por exemplo, escreva `nwind`.
4. Selecione o valor para **autenticação**. Por exemplo, seleccione **básica**.
5. Escreva o valor para **nome de utilizador**. Por exemplo, escreva `informix`.
6. Escreva o valor para **palavra-passe**. Por exemplo, escreva `Password1`.
7. Selecione o valor para **Gateway**. Por exemplo, seleccione **datagateway01**.
8. Selecione **criar** para continuar. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Criar a ligação de cloud
Este conector pode aceder a uma nuvem de base de dados Informix. 

1. Na **Gateways** painel de configuração, deixe a **caixa de verificação** desativado (unclicked) **ligar através do gateway**. 
2. Escreva o valor para **nome da ligação**. Por exemplo, escreva `hisdemo2`.
3. Escreva o valor para **nome do servidor Informix**, na forma de endereço ou alias de número de porta de dois pontos. Por exemplo, escreva `hisdemo2.cloudapp.net:9089`.
4. Escreva o valor para **nome de base de dados Informix**. Por exemplo, escreva `nwind`.
5. Escreva o valor para **nome de utilizador**. Por exemplo, escreva `informix`.
6. Escreva o valor para **palavra-passe**. Por exemplo, escreva `Password1`.
7. Selecione **criar** para continuar. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Obter todas as linhas, o uso de SELECT
Pode criar uma ação da aplicação lógica para obter todas as linhas na tabela Informix. Esta ação instrui o conector para processar uma instrução SELECIONE Informix, tais como `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. Na **quadro de início do Azure**, selecione **+** (sinal), **Web + móvel**e, em seguida **aplicação lógica**.
2. Introduza o **nome** (por exemplo, "**InformixgetRows**"), **subscrição**, **grupo de recursos**, **localização**, e **plano de serviço de aplicações**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. Na **estruturador de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista.
2. Na **acionadores** lista, selecione **periodicidade**. 
3. Na **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione  **Intervalo** digitar **7**. 
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. Na **ações** lista, escreva **informix** no **procurar mais ações** caixa de edição e, em seguida, selecione **Informix - obter linhas (pré-visualização)**.
6. Na **obter linhas (pré-visualização)** ação, selecione **Alterar ligação**.
7. Na **conexões** painel de configuração, selecione **criar nova**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. Na **Gateways** painel de configuração, deixe a **caixa de verificação** desativado (unclicked) **ligar através do gateway**.
   
   * Escreva o valor para **nome da ligação**. Por exemplo, escreva `HISDEMO2`.
   * Escreva o valor para **nome do servidor Informix**, na forma de endereço ou alias de número de porta de dois pontos. Por exemplo, escreva `HISDEMO2.cloudapp.net:9089`.
   * Escreva o valor para **nome de base de dados Informix**. Por exemplo, escreva `NWIND`.
   * Escreva o valor para **nome de utilizador**. Por exemplo, escreva `informix`.
   * Escreva o valor para **palavra-passe**. Por exemplo, escreva `Password1`.
9. Selecione **criar** para continuar.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. Na **nome da tabela** lista, selecione a **seta para baixo**e, em seguida, selecione **área**.
11. Opcionalmente, selecione **Mostrar opções avançadas** para especificar opções de consulta.
12. Selecione **Guardar**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. Na **InformixgetRows** painel, dentro do **todas as execuções** lista sob **resumo**, selecione o item listado primeiro (execução mais recente).
14. Na **execução da aplicação lógica** painel, selecione **detalhes da execução**. Dentro de **ação** lista, selecione **Get_rows**. Ver o valor para **Status**, que deve ser **Succeeded**. Selecione o **ligação de entradas** para ver as entradas. Selecione o **ligação de saídas**e ver os resultados; que deve incluir uma lista de linhas.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Adicionar uma linha usando INSERT
Pode criar uma ação da aplicação lógica para adicionar uma linha numa tabela do Informix. Esta ação instrui o conector para processar uma instrução INSERT do Informix, tais como `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. Na **quadro de início do Azure**, selecione **+** (sinal), **Web + móvel**e, em seguida **aplicação lógica**.
2. Introduza o **Name**, tal como `InformixinsertRow`, **subscrição**, **grupo de recursos**, **localização**, e **serviço de aplicações Planear**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. Na **estruturador de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista.
2. Na **acionadores** lista, selecione **periodicidade**. 
3. Na **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione  **Intervalo** digitar **7**. 
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. Na **ações** lista, escreva **informix** no **procurar mais ações** caixa de edição e, em seguida, selecione **Informix - Inserir linha (pré-visualização)**.
6. Na **obter linhas (pré-visualização)** ação, selecione **Alterar ligação**. 
7. Na **ligações** painel de configuração, selecione para selecionar uma ligação. Por exemplo, seleccione **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Na **nome da tabela** lista, selecione a **seta para baixo**e, em seguida, selecione **área**.
9. Introduza valores para colunas de todas as necessárias (consulte o asterisco vermelho). Por exemplo, digite `99999` para **AREAID**, tipo `Area 99999`e o tipo `102` para **REGIONID**. 
10. Selecione **Guardar**.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. Na **InformixinsertRow** painel, dentro do **todas as execuções** lista sob **resumo**, selecione o item listado primeiro (execução mais recente).
12. Na **execução da aplicação lógica** painel, selecione **detalhes da execução**. Dentro de **ação** lista, selecione **Get_rows**. Ver o valor para **Status**, que deve ser **Succeeded**. Selecione o **ligação de entradas** para ver as entradas. Selecione o **ligação de saídas**e ver os resultados; que deve incluir a nova linha.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Obter uma linha, o uso de SELECT
Pode criar uma ação da aplicação lógica para obter uma linha numa tabela do Informix. Esta ação instrui o conector para processar uma instrução Informix SELECIONE em que, por exemplo, `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. Na **quadro de início do Azure**, selecione **+** (sinal), **Web + móvel**e, em seguida **aplicação lógica**.
2. Introduza o **Name**, tal como `InformixgetRow`, **subscrição**, **grupo de recursos**, **localização**, e **serviço de aplicações Planear**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. Na **estruturador de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista.
2. Na **acionadores** lista, selecione **periodicidade**. 
3. Na **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione  **Intervalo** digitar **7**. 
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. Na **ações** lista, escreva **informix** no **procurar mais ações** caixa de edição e, em seguida, selecione **Informix - obter linhas (pré-visualização)**.
6. Na **obter linhas (pré-visualização)** ação, selecione **Alterar ligação**. 
7. Na **ligações** painel de configurações, selecione para selecionar uma ligação existente. Por exemplo, seleccione **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Na **nome da tabela** lista, selecione a **seta para baixo**e, em seguida, selecione **área**.
9. Introduza valores para colunas de todas as necessárias (consulte o asterisco vermelho). Por exemplo, digite `99999` para **AREAID**. 
10. Opcionalmente, selecione **Mostrar opções avançadas** para especificar opções de consulta.
11. Selecione **Guardar**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. Na **InformixgetRow** painel, dentro do **todas as execuções** lista sob **resumo**, selecione o item listado primeiro (execução mais recente).
13. Na **execução da aplicação lógica** painel, selecione **detalhes da execução**. Dentro de **ação** lista, selecione **Get_rows**. Ver o valor para **Status**, que deve ser **Succeeded**. Selecione o **ligação de entradas** para ver as entradas. Selecione o **ligação de saídas**e ver os resultados; que deve incluir a linha.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Alterar uma linha com a ATUALIZAÇÃO
Pode criar uma ação da aplicação lógica para alterar uma linha numa tabela do Informix. Esta ação instrui o conector para processar uma instrução de ATUALIZAÇÃO do Informix, tais como `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. Na **quadro de início do Azure**, selecione **+** (sinal), **Web + móvel**e, em seguida **aplicação lógica**.
2. Introduza o **Name**, tal como `InformixupdateRow`, **subscrição**, **grupo de recursos**, **localização**, e **serviço de aplicações Planear**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. Na **estruturador de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista.
2. Na **acionadores** lista, selecione **periodicidade**. 
3. Na **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione  **Intervalo** digitar **7**. 
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. Na **ações** lista, escreva **informix** no **procurar mais ações** caixa de edição e, em seguida, selecione **Informix - atualizar linha (pré-visualização)**.
6. Na **obter linhas (pré-visualização)** ação, selecione **Alterar ligação**. 
7. Na **ligações** painel de configurações, selecione para selecionar uma ligação existente. Por exemplo, seleccione **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Na **nome da tabela** lista, selecione a **seta para baixo**e, em seguida, selecione **área**.
9. Introduza valores para colunas de todas as necessárias (consulte o asterisco vermelho). Por exemplo, digite `99999` para **AREAID**, tipo `Updated 99999`e o tipo `102` para **REGIONID**. 
10. Selecione **Guardar**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. Na **InformixupdateRow** painel, dentro do **todas as execuções** lista sob **resumo**, selecione o item listado primeiro (execução mais recente).
12. Na **execução da aplicação lógica** painel, selecione **detalhes da execução**. Dentro de **ação** lista, selecione **Get_rows**. Ver o valor para **Status**, que deve ser **Succeeded**. Selecione o **ligação de entradas** para ver as entradas. Selecione o **ligação de saídas**e ver os resultados; que deve incluir a nova linha.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Remover uma linha para utilizar a eliminação
Pode criar uma ação da aplicação lógica para remover uma linha numa tabela do Informix. Esta ação instrui o conector para processar uma instrução eliminar Informix, tais como `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. Na **quadro de início do Azure**, selecione **+** (sinal), **Web + móvel**e, em seguida **aplicação lógica**.
2. Introduza o **Name**, tal como `InformixdeleteRow`, **subscrição**, **grupo de recursos**, **localização**, e **serviço de aplicações Planear**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. Na **estruturador de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista.
2. Na **acionadores** lista, selecione **periodicidade**. 
3. Na **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione  **Intervalo** digitar **7**. 
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. Na **ações** lista, escreva **informix** no **procurar mais ações** caixa de edição e, em seguida, selecione **Informix - Eliminar linha (pré-visualização)**.
6. Na **obter linhas (pré-visualização)** ação, selecione **Alterar ligação**. 
7. Na **ligações** painel de configurações, selecione uma ligação existente. Por exemplo, seleccione **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Na **nome da tabela** lista, selecione a **seta para baixo**e, em seguida, selecione **área**.
9. Introduza valores para colunas de todas as necessárias (consulte o asterisco vermelho). Por exemplo, digite `99999` para **AREAID**. 
10. Selecione **Guardar**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. Na **InformixdeleteRow** painel, dentro do **todas as execuções** lista sob **resumo**, selecione o item listado primeiro (execução mais recente).
12. Na **execução da aplicação lógica** painel, selecione **detalhes da execução**. Dentro de **ação** lista, selecione **Get_rows**. Ver o valor para **Status**, que deve ser **Succeeded**. Selecione o **ligação de entradas** para ver as entradas. Selecione o **ligação de saídas**e ver os resultados; que deve incluir a linha excluída.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Plataformas de Informix e versões suportados
Este conector suporta as seguintes versões do IBM Informix, quando configurado para suportar ligações de cliente distribuídos relacional da base de dados arquitetura (DRDA).

* IBM Informix 12.1
* IBM Informix 11.7

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver os acionadores e as ações definidas no swagger e também ver quaisquer limites na [detalhes do conector](/connectors/informix/). 

## <a name="next-steps"></a>Passos Seguintes
[Criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Explore os outros conectores disponíveis no Logic Apps em nosso [lista APIs](apis-list.md).

