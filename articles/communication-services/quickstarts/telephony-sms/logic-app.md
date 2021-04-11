---
title: Quickstart - Envie mensagens SMS em Azure Logic Apps usando serviços de comunicação Azure
titleSuffix: An Azure Communication Services quickstart
description: Neste arranque rápido, aprenda a enviar mensagens SMS em fluxos de trabalho Azure Logic Apps utilizando o conector Azure Communication Services.
author: tophpalmer
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 11cce223a0802c20c5fc144eed681327f989a949
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103488385"
---
# <a name="quickstart-send-sms-messages-in-azure-logic-apps-with-azure-communication-services"></a>Quickstart: Envie mensagens SMS em Azure Logic Apps com Serviços de Comunicação Azure

Ao utilizar o conector [SMS dos Serviços de Comunicação Azure](../../overview.md) e [as Aplicações Lógicas Azure,](../../../logic-apps/logic-apps-overview.md)pode criar fluxos de trabalho automatizados ou *aplicações lógicas* que podem enviar mensagens SMS. Este quickstart mostra como enviar automaticamente mensagens de texto em resposta a um evento de gatilho, que é o primeiro passo de um fluxo de trabalho de aplicações lógicas. Um evento de gatilho pode ser uma mensagem de e-mail recebida, um horário de recorrência, um evento de recursos [Azure Event Grid](../../../event-grid/overview.md) ou qualquer outro [gatilho que seja suportado por Azure Logic Apps](/connectors/connector-reference/connector-reference-logicapps-connectors).

:::image type="content" source="./media/logic-app/azure-communication-services-connector.png" alt-text="Screenshot que mostra o portal Azure, que está aberto ao Logic App Designer, e mostra uma aplicação lógica de exemplo que utiliza a ação SMS enviar para o conector Azure Communication Services.":::

Embora este arranque rápido se concentre na utilização do conector para responder a um gatilho, também pode utilizar o conector para responder a outras ações, que são os passos que seguem o gatilho num fluxo de trabalho. Se é novo em Aplicações Lógicas, [reveja o que é Azure Logic Apps](../../../logic-apps/logic-apps-overview.md) antes de começar.

> [!NOTE]
> Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa, ou [criar gratuitamente uma conta Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Um recurso ativo dos Serviços de Comunicação Azure, ou [criar um recurso de Serviços de Comunicação.](../create-communication-resource.md)

- Um recurso de Aplicações Lógicas ativas (aplicação lógica), ou [criar uma aplicação lógica em branco, mas com o gatilho que pretende utilizar.](../../../logic-apps/quickstart-create-first-logic-app-workflow.md) Atualmente, o conector SMS dos Serviços de Comunicação Azure fornece apenas ações, pelo que a sua aplicação lógica requer um gatilho, no mínimo.

  Este quickstart utiliza o **gatilho Quando um novo e-mail chega,** que está disponível com o [conector Office 365 Outlook](/connectors/office365/).

- Um número de telefone ativado por SMS ou [um número de telefone](./get-phone-number.md).

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

## <a name="add-an-sms-action"></a>Adicione uma ação SMS

Para adicionar a ação **SMS enviar** como um novo passo no seu fluxo de trabalho utilizando o conector SMS dos Serviços de Comunicação Azure, siga estes passos no [portal Azure](https://portal.azure.com) com o fluxo de trabalho da sua aplicação lógica aberta no Logic App Designer:

1. No designer, sob o passo em que pretende adicionar a nova ação, selecione **Novo passo**. Em alternativa, para adicionar a nova ação entre passos, mova o ponteiro sobre a seta entre esses passos, selecione o sinal de mais **+** () e **selecione Adicione uma ação**.

1. Na caixa de pesquisa de **operação Escolha,** insira `Azure Communication Services` . A partir da lista de ações, selecione **Enviar SMS.**

   :::image type="content" source="./media/logic-app/select-send-sms-action.png" alt-text="Screenshot que mostra o Logic App Designer e o conector Azure Communication Services com a ação SMS de envio selecionado.":::

1. Agora crie uma ligação ao seu recurso de Serviços de Comunicação.

   1. Forneça um nome para a ligação.

   1. Selecione o seu recurso Azure Communication Services.

   1. Selecione **Criar**.

   :::image type="content" source="./media/logic-app/send-sms-configuration.png" alt-text="Screenshot que mostra a configuração de ação enviar SMS com informações de amostra.":::

1. Na ação **Enviar SMS,** forneça as seguintes informações: 

   * Os números de telefone de origem e destino. Para efeitos de teste, pode utilizar o seu próprio número de telefone como número de telefone de destino.

   * O conteúdo da mensagem que pretende enviar, por exemplo, "Olá das Aplicações Lógicas!".

   Aqui está uma ação **enviar SMS** com informações de exemplo:

   :::image type="content" source="./media/logic-app/send-sms-action.png" alt-text="Screenshot que mostra a ação enviar SMS com informações de amostra.":::

1. Quando terminar, na barra de ferramentas do designer, **selecione Save**.

Em seguida, execute a sua aplicação lógica para testes.

## <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

Para iniciar manualmente a sua aplicação lógica, na barra de ferramentas do designer, selecione **Run**. Ou pode esperar que a sua aplicação lógica desencadeie. Em ambos os casos, a aplicação lógica deve enviar uma mensagem SMS para o seu número de telefone de destino especificado. Para obter mais informações sobre a execução da sua app lógica, [reveja como executar a sua aplicação lógica](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#run-your-logic-app)

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover uma subscrição de Serviços de Comunicação, elimine o recurso ou grupo de recursos dos Serviços de Comunicação. A eliminação do grupo de recursos também elimina quaisquer outros recursos desse grupo. Para mais informações, [reveja como limpar os recursos dos Serviços de Comunicação.](../create-communication-resource.md#clean-up-resources)

Para limpar o fluxo de trabalho da sua aplicação lógica e recursos relacionados, [reveja como limpar os recursos das Apps Lógicas.](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a enviar mensagens SMS utilizando apps Azure Logic e Serviços de Comunicação Azure. Para saber mais, continue com a subscrição de eventos SMS:

> [!div class="nextstepaction"]
> [Subscreva os Eventos SMS](./handle-sms-events.md)

Para obter mais informações sobre SMS nos Serviços de Comunicação Azure, consulte estes artigos:

- [Conceitos de SMS](../../concepts/telephony-sms/concepts.md)
- [Tipos de números de telefone](../../concepts/telephony-sms/plan-solution.md)
- [SMS SDK](../../concepts/telephony-sms/sdk-features.md)
