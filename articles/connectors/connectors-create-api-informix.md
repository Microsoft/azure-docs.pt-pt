---
title: Ligue-se à base de dados IBM Informix
description: Automatizar tarefas e fluxos de trabalho que gerem recursos armazenados no IBM Informix utilizando aplicações da Lógica Azure
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: dccb715c974037b4e3080f3e51576feae34c03df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757973"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>Gerir os recursos da base de dados IBM Informix utilizando aplicações da Azure Logic

Com [aplicativos azure logic](../logic-apps/logic-apps-overview.md) e o [conector Informix,](/connectors/informix/)pode criar tarefas automatizadas e fluxos de trabalho que gerem recursos numa base de dados IBM Informix. Este conector inclui um cliente da Microsoft que comunica com computadores de servidores Informix remotos através de uma rede TCP/IP, incluindo bases de dados baseadas em nuvem, como o IBM Informix para windows que funciona em bases de dados de virtualização azure e no local quando utiliza a gateway de [dados no local.](../logic-apps/logic-apps-gateway-connection.md) Pode ligar-se a estas plataformas e versões Informix se estiverem configuradas para suportar as ligações do cliente Distributeal Database Architecture (DRDA):

* IBM Informix 12.1
* IBM Informix 11.7

Este tópico mostra como usar o conector numa aplicação lógica para processar operações de base de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Para bases de dados no local, [descarregue e instale a porta de dados no local](../logic-apps/logic-apps-gateway-install.md) num computador local e, em seguida, crie um recurso de gateway de dados [Azure no portal Azure](../logic-apps/logic-apps-gateway-connection.md).

* A aplicação lógica onde precisa de acesso à sua base de dados Informix. Este conector fornece apenas ações, pelo que a sua aplicação lógica já deve começar com um gatilho, por exemplo, o [gatilho de Recorrência](../connectors/connectors-native-recurrence.md). 

## <a name="add-an-informix-action"></a>Adicione uma ação Informix

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Sob o degrau onde pretende adicionar a ação Informix, selecione **Novo passo**.

   Para adicionar uma ação entre os passos existentes, mova o rato sobre a seta de ligação. Selecione o**+** sinal de mais ( ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de `informix` pesquisa, introduza como filtro. A partir da lista de ações, selecione a ação que deseja, por exemplo:

   ![Selecione a ação Informix para executar](./media/connectors-create-api-informix/select-informix-connector-action.png)

   O conector fornece estas ações, que executam as operações de base de dados correspondentes:

   * Obtenha tabelas - Listar `CALL` tabelas de bases de dados usando uma declaração
   * Obtenha filas - Leia todas as `SELECT *` linhas usando uma declaração
   * Get row - Leia uma `SELECT WHERE` linha usando uma declaração
   * Adicione uma linha `INSERT` usando uma declaração
   * Editar uma linha `UPDATE` usando uma declaração
   * Eliminar uma linha `DELETE` usando uma declaração

1. Se for solicitado que forneça detalhes de ligação para a sua base de dados Informix, siga os [passos para criar a ligação](#create-connection)e continue com o próximo passo.

1. Forneça as informações para a sua ação selecionada:

   | Ação | Descrição | Propriedades e descrições |
   |--------|-------------|-----------------------------|
   | **Obter mesas** | Lista as tabelas de bases de dados executando uma declaração informix CALL. | Nenhuma |
   | **Obter filas** | Pegue todas as linhas da tabela especificada executando uma declaração informix. `SELECT *` | **Nome da mesa**: O nome da tabela Informix que deseja <p><p>Para adicionar outras propriedades a esta ação, selecione-as a partir da nova lista de **parâmetros Adicionar.** Para mais informações, consulte o [tópico de referência do conector.](/connectors/informix/) |
   | **Obter fila** | Pegue uma linha da tabela especificada executando uma declaração informix. `SELECT WHERE` | - **Nome da mesa**: O nome da tabela Informix que deseja <br>- **ID da linha**: O ID único para a linha, por exemplo,`9999` |
   | **Inserir linha** | Adicione uma linha à tabela Informix especificada `INSERT` executando uma declaração informix. | - **Nome da mesa**: O nome da tabela Informix que deseja <br>- **item**: A linha com os valores a adicionar |
   | **Linha de atualização** | Mude uma linha na tabela Informix especificada `UPDATE` executando uma declaração informix. | - **Nome da mesa**: O nome da tabela Informix que deseja <br>- **ID da linha**: O ID único para a linha atualizar, por exemplo,`9999` <br>- **Linha**: A linha com os valores atualizados, por exemplo,`102` |
   | **Excluir linha** | Retire uma linha da tabela Informix especificada `DELETE` executando uma declaração informix. | - **Nome da mesa**: O nome da tabela Informix que deseja <br>- **ID da linha**: O ID único para a linha apagar, por exemplo,`9999` |
   ||||

1. Guarde a aplicação lógica. Agora, ou [testa a sua aplicação lógica](#test-logic-app) ou continue a construir a sua aplicação lógica.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Ligue-se ao Informix

1. Se a sua aplicação lógica se ligar a uma base de dados no local, selecione **Connect via gateway de dados no local**.

1. Forneça esta informação de ligação e, em seguida, selecione **Criar**.

   | Propriedade | Propriedade JSON | Necessário | Valor de exemplo | Descrição |
   |----------|---------------|----------|---------------|-------------|
   | Nome da ligação | `name` | Sim | `informix-demo-connection` | O nome a utilizar para a ligação à sua base de dados Informix |
   | Server | `server` | Sim | - Nuvem:`informixdemo.cloudapp.net:9089` <br>- No local:`informixdemo:9089` | O endereço TCP/IP ou pseudónimo que se encontra em formato IPv4 ou IPv6, seguido de um cólon e um número de porta TCP/IP |
   | Base de Dados | `database` | Sim | `nwind` | O nome da base de dados relacional DRDA (RDBNAM) ou o nome da base de dados Informix (nome de dbname). O Informix aceita uma corda de 128 bytes. |
   | Autenticação | `authentication` | Apenas no local | **Básico** ou **Windows** (kerberos) | O tipo de autenticação que é exigido pela sua base de dados Informix. Esta propriedade só aparece quando seleciona **Connect via gateway de dados no local**. |
   | Nome de utilizador | `username` | Não | <*base de dados-nome do utilizador*> | Um nome de utilizador para a base de dados |
   | Palavra-passe | `password` | Não | <*base de dados-senha*> | Uma senha para a base de dados |
   | Gateway | `gateway` | Apenas no local | - <*assinatura Azure*> <br>- <*Azure-on-local-data-gateway-recurso*> | A subscrição Azure e o nome de recurso Azure para o portal de dados no local que criou no portal Azure. A propriedade **gateway** e sub-propriedades só aparecem quando seleciona **Connect via gateway de dados no local**. |
   ||||||

   Por exemplo:

   * **Base de dados cloud**

     ![Informações de ligação à base de dados em nuvem](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **Base de dados no local**

     ![Informações de ligação à base de dados no local](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. Guarde a aplicação lógica.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>Teste a sua aplicação lógica

1. Na barra de ferramentas Logic App Designer, selecione **Run**. Depois da aplicação lógica, pode ver as saídas a partir dessa execução.

1. A partir do menu da sua aplicação lógica, selecione **Visão Geral**. No painel de visão geral, sob a história **de Sumário** > **Corre,** selecione a mais recente execução.

1. Sob **a execução da aplicação Logic,** selecione **Executar Detalhes**.

1. Na lista de ações, selecione a ação com as saídas que pretende ver, por exemplo, **Get_tables**.

   Se a ação foi bem sucedida, a sua propriedade **status** é marcada como **Bem sucedida.**

1. Para visualizar as inputs, em **'Inputs Link',** selecione o link URL. Para visualizar as saídas, sob a ligação de ligação de ligação de **saídas,** selecione a ligação URL. Aqui estão algumas saídas de exemplo:

   * **Get_tables** mostra uma lista de tabelas:

     ![Saídas da ação "Get tables"](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** mostra uma lista de linhas:

     ![Saídas da ação "Get rows"](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** mostra a linha especificada:

     ![Saídas da ação "Get row"](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** mostra a nova linha:

     ![Saídas da ação "Insert row"](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** mostra a linha atualizada:

     ![Saídas da ação "Update row"](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** mostra a linha apagada:

     ![Saídas da ação "Eliminar linha"](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição swagger do conector, reveja a página de [referência do conector](/connectors/informix/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](apis-list.md)
