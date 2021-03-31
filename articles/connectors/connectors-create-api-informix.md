---
title: Ligue à base de dados IBM Informix
description: Automatizar tarefas e fluxos de trabalho que gerem os recursos armazenados na IBM Informix utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: daberry
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: 4995a91783c2302f3bda5cc9409f017248ca29fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88761649"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>Gerir os recursos de base de dados da IBM Informix utilizando apps Azure Logic

Com [a Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o [conector Informix,](/connectors/informix/)pode criar tarefas automatizadas e fluxos de trabalho que gerem recursos numa base de dados IBM Informix. Este conector inclui um cliente da Microsoft que comunica com computadores de servidor informix remotos através de uma rede TCP/IP, incluindo bases de dados baseadas em nuvem, como o IBM Informix para Windows em execução em virtualização Azure e bases de dados no local quando utiliza o [portal de dados no local.](../logic-apps/logic-apps-gateway-connection.md) Pode ligar-se a estas plataformas e versões Informix se estiverem configuradas para suportar ligações de clientes distribuídas da Arquitetura de Base de Dados Relacionais Distribuídas (DRDA):

* IBM Informix 12.1
* IBM Informix 11.7

Este tópico mostra-lhe como usar o conector numa aplicação lógica para processar operações de base de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Para bases de dados no local, [faça o download e instale o portal de dados no local](../logic-apps/logic-apps-gateway-install.md) num computador local e, em seguida, crie um recurso de gateway de [dados Azure no portal Azure](../logic-apps/logic-apps-gateway-connection.md).

* A aplicação lógica onde precisa de acesso à sua base de dados Informix. Este conector fornece apenas ações, pelo que a sua aplicação lógica já deve começar com um gatilho, por exemplo, o [gatilho recorrência](../connectors/connectors-native-recurrence.md). 

## <a name="add-an-informix-action"></a>Adicione uma ação Informix

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer, se não já estiver aberta.

1. Sob o passo em que pretende adicionar a ação Informix, selecione **Novo passo**.

   Para adicionar uma ação entre os passos existentes, mova o rato sobre a seta de ligação. Selecione o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Na caixa de pesquisa, introduza `informix` como filtro. A partir da lista de ações, selecione a ação que deseja, por exemplo:

   ![Selecione a ação Informix para executar](./media/connectors-create-api-informix/select-informix-connector-action.png)

   O conector fornece estas ações, que executam as operações correspondentes na base de dados:

   * Obter tabelas - Listar tabelas de bases de dados usando uma `CALL` declaração
   * Obter linhas - Leia todas as linhas usando uma `SELECT *` declaração
   * Get row - Leia uma linha usando uma `SELECT WHERE` declaração
   * Adicione uma linha usando uma `INSERT` declaração
   * Editar uma linha usando uma `UPDATE` declaração
   * Eliminar uma linha usando uma `DELETE` declaração

1. Se lhe for solicitado que forneça detalhes de ligação para a sua base de dados Informix, siga os [passos para criar a ligação](#create-connection)e, em seguida, continue com o passo seguinte.

1. Forneça as informações para a sua ação selecionada:

   | Ação | Descrição | Propriedades e descrições |
   |--------|-------------|-----------------------------|
   | **Obter mesas** | Listar as tabelas de bases de dados executando uma declaração de Chamada Informix. | Nenhum |
   | **Obter filas** | Pegue todas as linhas na tabela especificada executando uma declaração de `SELECT *` Informix. | **Nome da tabela**: O nome da tabela Informix que deseja <p><p>Para adicionar outras propriedades a esta ação, selecione-as da nova lista **de parâmetros Adicionar.** Para mais informações, consulte o [tópico de referência do conector.](/connectors/informix/) |
   | **Obter fila** | Pegue uma linha da tabela especificada executando uma declaração de `SELECT WHERE` Informix. | - **Nome da tabela**: O nome da tabela Informix que deseja <br>- **ID de linha**: O ID único para a linha, por exemplo, `9999` |
   | **Inserir linha** | Adicione uma linha à tabela Informix especificada executando uma declaração de `INSERT` Informix. | - **Nome da tabela**: O nome da tabela Informix que deseja <br>- **item**: A linha com os valores a adicionar |
   | **Linha de atualização** | Altere uma linha na tabela Informix especificada executando uma declaração de `UPDATE` Informix. | - **Nome da tabela**: O nome da tabela Informix que deseja <br>- **ID de linha**: O ID único para a linha atualizar, por exemplo, `9999` <br>- **Linha**: A linha com os valores atualizados, por exemplo, `102` |
   | **Eliminar linha** | Remova uma linha da tabela Informix especificada executando uma declaração de `DELETE` Informix. | - **Nome da tabela**: O nome da tabela Informix que deseja <br>- **ID de linha**: O ID único para a linha para apagar, por exemplo, `9999` |
   ||||

1. Guarde a sua aplicação lógica. Agora, ou [testa a tua aplicação lógica](#test-logic-app) ou continua a construir a tua aplicação lógica.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Ligar ao Informix

1. Se a sua aplicação lógica se ligar a uma base de dados no local, selecione **Connect via gateway de dados no local.**

1. Forneça esta informação de ligação e, em seguida, **selecione Criar**.

   | Propriedade | Propriedade JSON | Necessário | Valor de exemplo | Description |
   |----------|---------------|----------|---------------|-------------|
   | Nome da ligação | `name` | Yes | `informix-demo-connection` | O nome a utilizar para a ligação à sua base de dados Informix |
   | Servidor | `server` | Yes | - Nuvem: `informixdemo.cloudapp.net:9089` <br>- No local: `informixdemo:9089` | O endereço TCP/IP ou pseudónimo que se encontra no formato IPv4 ou IPv6, seguido de um cólon e um número de porta TCP/IP |
   | Base de Dados | `database` | Yes | `nwind` | O nome da base de dados relacional DRDA (RDBNAM) ou nome da base de dados Informix (dbname). Informix aceita uma corda de 128 bytes. |
   | Autenticação | `authentication` | Apenas no local | **Básico** ou **Windows** (kerberos) | O tipo de autenticação que é exigido pela sua base de dados Informix. Esta propriedade só aparece quando seleciona **Connect via gateway de dados no local.** |
   | Nome de utilizador | `username` | No | <*nome do utilizador da base de dados*> | Um nome de utilizador para a base de dados |
   | Palavra-passe | `password` | No | <*palavra-passe de base de dados*> | Uma senha para a base de dados |
   | Gateway | `gateway` | Apenas no local | - <*assinatura Azure*> <br>- <*Azure-on-premises-data-gateway-recurso*> | A subscrição Azure e o nome de recurso Azure para o portal de dados no local que criou no portal Azure. A propriedade **gateway** e sub-propriedades só aparecem quando seleciona **Connect via gateway de dados no local.** |
   ||||||

   Por exemplo:

   * **Base de dados em nuvem**

     ![Informações de ligação de base de dados em nuvem](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **Base de dados no local**

     ![Informações de ligação à base de dados no local](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. Guarde a sua aplicação lógica.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

1. Na barra de ferramentas Logic App Designer, selecione **Run**. Depois de a sua aplicação lógica ser executada, pode ver as saídas dessa execução.

1. A partir do menu da sua aplicação lógica, selecione **Overview**. No painel geral, na história **do**  >  **Resumo,** selecione a execução mais recente.

1. No **âmbito da aplicação Logic,** selecione **'Detalhes de execução'.**

1. Na lista de ações, selecione a ação com as saídas que pretende visualizar, por exemplo, **Get_tables**.

   Se a ação tiver sido bem sucedida, a sua propriedade **status** é marcada como **Bem Sucedida**.

1. Para visualizar as entradas, no **Link entradas,** selecione o link URL. Para visualizar as saídas, no link **Descosalho de Saídas,** selecione a ligação URL. Aqui estão algumas saídas de exemplo:

   * **Get_tables** mostra uma lista de tabelas:

     ![Saídas da ação "Obter mesas"](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** mostra uma lista de linhas:

     ![Saídas da ação "Get rows"](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** mostra a linha especificada:

     ![Saídas da ação "Get row"](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** mostra a nova linha:

     ![Saídas da ação "Inserir linha"](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** mostra a linha atualizada:

     ![Saídas da ação "Update row"](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** mostra a linha eliminada:

     ![Saídas da ação "Eliminar linha"](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição do Conector Swagger, consulte a [página de referência do conector](/connectors/informix/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](apis-list.md)
