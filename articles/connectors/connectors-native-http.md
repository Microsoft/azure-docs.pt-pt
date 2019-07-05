---
title: Ligar a pontos finais HTTP ou HTTPS a partir do Azure Logic Apps
description: Monitorizar pontos finais HTTP ou HTTPS em tarefas automatizadas, processos e fluxos de trabalho com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: fa5fd3ef8b144826468f56ea2a14be592cef5dc1
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541265"
---
# <a name="call-http-or-https-endpoints-by-using-azure-logic-apps"></a>Chamar pontos finais HTTP ou HTTPS ao utilizar o Azure Logic Apps

Com o [do Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o conector HTTP incorporado, pode automatizar fluxos de trabalho que regularmente chamam qualquer ponto final HTTP ou HTTPS com a criação de aplicações lógicas. Por exemplo, pode monitorizar o ponto final de serviço do Web site ao verificar o ponto de extremidade com base numa agenda especificada. Quando um evento específico acontece naquele ponto de extremidade, como o seu Web site fica em baixo, o evento aciona o fluxo de trabalho da sua aplicação lógica e executa as ações especificadas.

Para verificar ou *poll* um ponto de extremidade com base numa agenda regular, pode usar o acionador HTTP como o primeiro passo no fluxo de trabalho. Em cada verificação, o acionador envia uma chamada ou *pedido* para o ponto final. Resposta do ponto de extremidade determina se a fluxo de trabalho da sua aplicação lógica é executada. O acionador passa ao longo de qualquer conteúdo da resposta às ações na sua aplicação lógica.

Pode utilizar a ação de HTTP como qualquer outro passo no fluxo de trabalho para chamar o ponto de extremidade quando quiser. Resposta do ponto de extremidade determina como executam ações restantes do seu fluxo de trabalho.

Com base em capacidade do destino do ponto de extremidade, o conector HTTP suporta o Transport Layer Security (TLS) versões 1.0, 1.1 e 1.2. O Logic Apps negocia com o ponto final com o uso a versão suportada mais elevada possível. Então, por exemplo, se o ponto final de suportar 1.2, o conector utiliza 1.2 pela primeira vez. Caso contrário, o conector utiliza a versão suportada mais alta seguinte.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O URL para o ponto de extremidade de destino que deseja chamar

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* A aplicação de lógica de onde deseja chamar o ponto de extremidade de destino. Para começar com o acionador HTTP [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar a ação de HTTP, inicie a aplicação lógica com qualquer acionador que pretende. Este exemplo utiliza o acionador HTTP, como a primeira etapa.

## <a name="add-an-http-trigger"></a>Adicionar um acionador HTTP

Este acionador incorporado faz uma chamada HTTP para o URL especificado para um ponto final e retorna uma resposta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a aplicação lógica em branco no Estruturador da aplicação lógica.

1. No estruturador, na caixa de pesquisa, introduza "http" como o filtro. Partir do **Acionadores** lista, selecione a **HTTP** acionador.

   ![Selecione o acionador HTTP](./media/connectors-native-http/select-http-trigger.png)

   Este exemplo muda o nome do acionador "Acionador de HTTP", para que a etapa tem um nome mais descritivo. Além disso, o exemplo adiciona mais tarde uma ação de HTTP, e ambos os nomes têm de ser exclusivos.

1. Forneça os valores para o [parâmetros de Acionador HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) que pretende incluir na chamada para o ponto de extremidade de destino. Configure a periodicidade para a frequência com que pretende que o acionador para verificar o ponto de extremidade de destino.

   ![Introduza os parâmetros de Acionador HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP, consulte [autenticar HTTP acionadores e ações](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Para adicionar outros parâmetros disponíveis, abra a **adicione o novo parâmetro** lista e selecione os parâmetros que pretende.

1. Continue a criar o fluxo de trabalho da sua aplicação lógica com ações executadas quando o acionador é acionado.

1. Quando tiver terminado, feito, não se esqueça de guardar a aplicação lógica. Na barra de ferramentas da estruturador, selecione **guardar**.

## <a name="add-an-http-action"></a>Adicionar uma ação de HTTP

Esta ação incorporada faz uma chamada HTTP para o URL especificado para um ponto final e retorna uma resposta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a aplicação lógica no Estruturador da aplicação lógica.

   Este exemplo utiliza o acionador HTTP, como a primeira etapa.

1. No passo em que pretende adicionar a ação de HTTP, selecione **novo passo**.

   Para adicionar uma ação entre passos, mova o ponteiro do mouse sobre a seta entre passos. Selecione o sinal ( **+** ) que é apresentada e, em seguida, selecione **adicionar uma ação**.

1. No estruturador, na caixa de pesquisa, introduza "http" como o filtro. Partir do **ações** lista, selecione a **HTTP** ação.

   ![Selecione a ação de HTTP](./media/connectors-native-http/select-http-action.png)

   Este exemplo muda o nome da ação como "Ação de HTTP", para que a etapa tem um nome mais descritivo.

1. Forneça os valores para o [parâmetros de ação de HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) que pretende incluir na chamada para o ponto de extremidade de destino.

   ![Introduza os parâmetros de ação de HTTP](./media/connectors-native-http/http-action-parameters.png)

   Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP, consulte [autenticar HTTP acionadores e ações](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Para adicionar outros parâmetros disponíveis, abra a **adicione o novo parâmetro** lista e selecione os parâmetros que pretende.

1. Quando tiver terminado, lembre-se de guardar a aplicação lógica. Na barra de ferramentas da estruturador, selecione **guardar**.

## <a name="connector-reference"></a>Referência do conector

Para obter mais informações sobre parâmetros de Acionador e ação, veja essas secções:

* [Parâmetros de Acionador HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [Parâmetros de ação de HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>Detalhes de saída

Aqui estão mais algumas informações sobre as saídas de um acionador HTTP ou a ação, que retorna essas informações:

| Nome da propriedade | Tipo | Descrição |
|---------------|------|-------------|
| Cabeçalhos | object | Os cabeçalhos do pedido |
| Corpo | object | Objeto JSON | O objeto com o conteúdo do corpo do pedido |
| Código de estado | int | O código de estado do pedido |
|||

| Código de estado | Descrição |
|-------------|-------------|
| 200 | OK |
| 202 | Aceite |
| 400 | Pedido incorreto |
| 401 | Não autorizado |
| 403 | Proibido |
| 404 | Não foi encontrado |
| 500 | Erro de servidor interno. Ocorreu um erro desconhecido. |
|||

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
