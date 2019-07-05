---
title: Criar tarefas com base em eventos e fluxos de trabalho no Azure Logic Apps
description: Acionar, colocar em pausa e retomar tarefas automatizadas, processos e fluxos de trabalho com base em eventos que ocorrem num ponto final com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: c2658df185d4836210c496d2c46a00a3541257a2
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541354"
---
# <a name="automate-event-based-tasks-and-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Para automatizar tarefas com base em eventos e fluxos de trabalho através de webhooks HTTP no Azure Logic Apps

Com o [do Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o conector do Webhook de HTTP incorporado, pode automatizar fluxos de trabalho que aguarde e executam com base em eventos específicos que ocorrem num ponto de extremidade HTTP ou HTTPS com a criação de aplicações lógicas. Por exemplo, pode criar uma aplicação lógica que monitoriza o ponto final de serviço ao aguardar por um evento específico antes de acionar o fluxo de trabalho e executar as ações especificadas, em vez de verificar regularmente ou *consulta* esse ponto final.

Eis um exemplo baseado em evento fluxos de trabalho de:

* Aguarde um item a chegada de uma [Hub de eventos do Azure](https://github.com/logicappsio/EventHubAPI) antes de acionar uma execução da aplicação lógica.
* Aguarde uma aprovação antes de continuar um fluxo de trabalho.

## <a name="how-do-webhooks-work"></a>Como funcionam os webhooks?

Um acionador de webhook HTTP é baseado em evento, que não depende de a verificação ou a consultar regularmente de novos itens. Quando guardar uma aplicação lógica que começa com um acionador de webhook, ou quando alterar a sua aplicação lógica de desativado para ativado, o acionador de webhook *subscreve* para um serviço específico ou um ponto final ao se registrar um *URL de retorno de chamada* com esse serviço ou o ponto final. O acionador, em seguida, aguarda para esse serviço ou o ponto final para chamar o URL que começa a executar a aplicação lógica. Semelhante a [acionador de pedido](connectors-native-reqres.md), a aplicação lógica é acionado imediatamente quando ocorre o evento especificado. O acionador *anulações de subscrição* do serviço ou ponto final, se remover o acionador e guardar a aplicação lógica ou ao alterar a aplicação de lógica de ativado para desativado.

Uma ação do webhook HTTP também é baseada em eventos e *subscreve* para um serviço específico ou um ponto final ao se registrar um *URL de retorno de chamada* com esse serviço ou o ponto final. A ação do webhook interrompe o fluxo de trabalho da aplicação lógica e aguarda até o serviço ou o ponto final de chamadas a URL antes da retoma de aplicação lógica em execução. A aplicação de lógica de ação *anulações de subscrição* do serviço ou ponto de extremidade nestes casos:

* Quando a ação do webhook for concluída com êxito
* Se a execução da aplicação lógica é cancelada ao aguardar uma resposta
* Antes da lógica de aplicação exceder o tempo limite

Por exemplo, o Outlook do Office 365 conector [ **enviar e-mail de aprovação** ](connectors-create-api-office365-outlook.md) ação é um exemplo de ação do webhook que se segue este padrão. Pode estender esse padrão em qualquer serviço com a ação do webhook.

Para obter mais informações, veja estes tópicos:

* [Parâmetros de Acionador de Webhook de HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [As assinaturas e Webhooks](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Criar APIs personalizadas que suportam um webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O URL para um ponto final já implementado ou API que suporta o webhook inscrever-se e cancelar sua assinatura padrão para [acionadores de webhook no logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) ou [ações de webhook no logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions) conforme adequado

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* A aplicação de lógica onde pretende aguardar por eventos específicos no ponto de final de destino. Para começar com o acionador de Webhook de HTTP [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar a ação do Webhook de HTTP, inicie a aplicação lógica com qualquer acionador que pretende. Este exemplo utiliza o acionador HTTP, como a primeira etapa.

## <a name="add-an-http-webhook-trigger"></a>Adicionar um acionador de Webhook de HTTP

Este acionador incorporada registra um URL de retorno de chamada com o serviço especificado e aguarda para esse serviço enviar um pedido HTTP POST para esse URL. Quando este evento ocorre, o acionador é acionado e executa imediatamente a aplicação lógica.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a aplicação lógica em branco no Estruturador da aplicação lógica.

1. No estruturador, na caixa de pesquisa, introduza "webhook de http" como o filtro. Partir do **Acionadores** lista, selecione a **Webhook de HTTP** acionador.

   ![Selecione o acionador de Webhook de HTTP](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Este exemplo muda o nome do acionador "Acionador de Webhook de HTTP", para que a etapa tem um nome mais descritivo. Além disso, o exemplo adiciona mais tarde uma ação do Webhook de HTTP, e ambos os nomes têm de ser exclusivos.

1. Forneça os valores para o [parâmetros de Acionador de Webhook de HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) que pretende utilizar para subscrever e anular a subscrição chamadas, por exemplo:

   ![Introduza os parâmetros de Acionador de Webhook de HTTP](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. Para adicionar outros parâmetros disponíveis, abra a **adicione o novo parâmetro** lista e selecione os parâmetros que pretende.

   Para obter mais informações sobre os tipos de autenticação disponíveis do Webhook de HTTP, consulte [autenticar HTTP acionadores e ações](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Continue a criar o fluxo de trabalho da sua aplicação lógica com ações executadas quando o acionador é acionado.

1. Quando tiver terminado, feito, não se esqueça de guardar a aplicação lógica. Na barra de ferramentas da estruturador, selecione **guardar**.

   A guardar a sua aplicação lógica chama o ponto final de inscrever-se e registra o URL de retorno de chamada para acionar esta aplicação lógica.

1. Agora, sempre que o serviço de destino envia um `HTTP POST` pedir para o URL de retorno de chamada, dispara de aplicação lógica e inclui todos os dados que são transmitidos através do pedido.

## <a name="add-an-http-webhook-action"></a>Adicionar uma ação do Webhook de HTTP

Esta ação incorporada registra um URL de retorno de chamada com o serviço especificado, interrompe o fluxo de trabalho da aplicação lógica e aguarda para esse serviço enviar um pedido HTTP POST para esse URL. Quando este evento ocorre, a ação de retoma de executar a aplicação lógica.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a aplicação lógica no Estruturador da aplicação lógica.

   Este exemplo utiliza o acionador de Webhook de HTTP, como a primeira etapa.

1. No passo em que pretende adicionar a ação do Webhook de HTTP, selecione **novo passo**.

   Para adicionar uma ação entre passos, mova o ponteiro do mouse sobre a seta entre passos. Selecione o sinal ( **+** ) que é apresentada e, em seguida, selecione **adicionar uma ação**.

1. No estruturador, na caixa de pesquisa, introduza "webhook de http" como o filtro. Partir do **ações** lista, selecione a **Webhook de HTTP** ação.

   ![Selecione a ação do Webhook de HTTP](./media/connectors-native-webhook/select-http-webhook-action.png)

   Este exemplo muda o nome da ação como "Ação do Webhook de HTTP", para que a etapa tem um nome mais descritivo.

1. Forneça os valores do webhook de HTTP de parâmetros de ação, o que são semelhantes para o [parâmetros de Acionador de Webhook de HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger) que pretende utilizar para subscrever e anular a subscrição chamadas, por exemplo:

   ![Introduza os parâmetros de ação do Webhook de HTTP](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   Durante o tempo de execução, a aplicação lógica chama o ponto final de inscrever-se ao executar esta ação. A aplicação lógica, em seguida, interrompe o fluxo de trabalho e aguarda que o serviço de destino para enviar um `HTTP POST` pedido para o URL de retorno de chamada. Se a ação for concluída com êxito, a ação de anulações de subscrição do ponto final e retoma a sua aplicação lógica com o fluxo de trabalho.

1. Para adicionar outros parâmetros disponíveis, abra a **adicione o novo parâmetro** lista e selecione os parâmetros que pretende.

   Para obter mais informações sobre os tipos de autenticação disponíveis do Webhook de HTTP, consulte [autenticar HTTP acionadores e ações](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Quando tiver terminado, lembre-se de guardar a aplicação lógica. Na barra de ferramentas da estruturador, selecione **guardar**.

## <a name="connector-reference"></a>Referência do conector

Para obter mais informações sobre parâmetros de Acionador e ação, que são semelhantes entre si, consulte [parâmetros do Webhook de HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger).

### <a name="output-details"></a>Detalhes de saída

Aqui estão mais algumas informações sobre as saídas de um acionador de Webhook de HTTP ou a ação, que retorna essas informações:

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
