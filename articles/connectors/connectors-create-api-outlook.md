---
title: Ligar-se a Outlook.com
description: Automatizar tarefas e fluxos de trabalho que gerem e-mails, calendários e contactos em Outlook.com utilizando aplicações lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "75707191"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>Gerir e-mails, calendários e contactos em Outlook.com utilizando aplicações Azure Logic

Com [a Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o [conector Outlook.com,](/connectors/outlook/)pode criar tarefas e fluxos de trabalho automatizados que gerem a sua @outlook.com conta ou conta através da @hotmail.com construção de aplicações lógicas. Por exemplo, automatiza estas tarefas:

* Receber, enviar e responder ao e-mail.
* Marque reuniões no seu calendário.
* Adicione e edite os contactos.

Pode utilizar qualquer gatilho para iniciar o seu fluxo de trabalho, por exemplo, quando um novo e-mail chega, quando um item de calendário é atualizado, ou quando um evento acontece num serviço de diferença. Pode utilizar ações que respondam ao evento de gatilho, por exemplo, enviar um e-mail ou criar um novo evento de calendário.

> [!NOTE]
> Para automatizar tarefas para uma conta de trabalho da Microsoft, @fabrikam.onmicrosoft.com como, utilize o [conector Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta Outlook.com](https://outlook.live.com/owa/)

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* A aplicação lógica onde pretende aceder à sua conta Outlook.com. Para iniciar o seu fluxo de trabalho com um gatilho Outlook.com, precisa de ter uma [aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para adicionar uma ação Outlook.com ao seu fluxo de trabalho, a sua aplicação lógica já precisa de ter um gatilho.

## <a name="add-a-trigger"></a>Adicionar um acionador

Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um evento que inicia o fluxo de trabalho na sua aplicação lógica. Esta aplicação lógica de exemplo utiliza um gatilho de "sondagens" que verifica qualquer novo e-mail na sua conta de e-mail, com base no intervalo e frequência especificados.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica em branco no Logic App Designer.

1. Na caixa de pesquisa, introduza "outlook.com" como filtro. Para este exemplo, selecione **Quando um novo e-mail chegar**.

1. Se for solicitado a iniciar scontabilidade, forneça as suas credenciais de Outlook.com para que a sua aplicação lógica possa ligar-se à sua conta. Caso contrário, se a sua ligação já existir, forneça as informações sobre as propriedades do gatilho:

1. No gatilho, desaccione os valores **de Frequência** e **Intervalo.**

   Por exemplo, se quiser que o gatilho faça sondagens a cada 15 minutos, desaccione a **Frequência** ao **Minuto** e desaccione o **Intervalo** para **15**.

1. Na barra de ferramentas do designer, **selecione Save**, que guarda a sua aplicação lógica.

Para responder ao gatilho, adicione outra ação. Por exemplo, pode adicionar a ação de mensagem Twilio **Enviar mensagens,** que envia um texto quando um e-mail chega.

## <a name="add-an-action"></a>Adicionar uma ação

Uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma operação que é gerida pelo fluxo de trabalho na sua aplicação lógica. Esta aplicação lógica de exemplo envia um e-mail da sua conta Outlook.com. Pode usar a saída de outro gatilho para preencher a ação. Por exemplo, suponha que a sua aplicação lógica utilize o SalesForce **Quando um objeto é criado** gatilho. Pode adicionar o Outlook.com Enviar uma ação **de e-mail** e utilizar as saídas do gatilho SalesForce no e-mail.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Para adicionar uma ação como último passo no seu fluxo de trabalho, selecione **Novo passo**. 

   Para adicionar uma ação entre os degraus, mova o ponteiro sobre a seta entre esses degraus. Selecione o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Na caixa de pesquisa, introduza "outlook.com" como filtro. Para este exemplo, selecione **Enviar um e-mail.** 

1. Se for solicitado a iniciar scontabilidade, forneça as suas credenciais de Outlook.com para que a sua aplicação lógica possa ligar-se à sua conta. Caso contrário, se a sua ligação já existir, forneça as informações sobre as propriedades de ação.

1. Na barra de ferramentas do designer, **selecione Save**, que guarda a sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, tais como gatilhos, ações e limites, conforme descrito pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/outlook/). 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
