---
title: Ligar a Outlook.com
description: Automatizar tarefas e fluxos de trabalho que gerem e-mails, calendários e contactos em Outlook.com através da utilização de Apps Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75707191"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>Gerir e-mails, calendários e contactos em Outlook.com utilizando aplicações lógicas do Azure

Com [as Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e o [conector Outlook.com,](/connectors/outlook/)pode criar tarefas automatizadas e fluxos de trabalho que gerem a sua @outlook.com ou @hotmail.com conta através da construção de aplicações lógicas. Por exemplo, automatiza estas tarefas:

* Obter, enviar e responder ao e-mail.
* Marque reuniões no seu calendário.
* Adicione e edite contactos.

Pode utilizar qualquer gatilho para iniciar o seu fluxo de trabalho, por exemplo, quando um novo e-mail chega, quando um item de calendário é atualizado, ou quando um evento acontece num serviço de diferença. Pode utilizar ações que respondam ao evento de gatilho, por exemplo, enviar um e-mail ou criar um novo evento de calendário.

> [!NOTE]
> Para automatizar tarefas para uma @fabrikam.onmicrosoft.comconta de trabalho da Microsoft, como , utilize o [conector Do Outlook do Office 365](../connectors/connectors-create-api-office365-outlook.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta Outlook.com](https://outlook.live.com/owa/)

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* A aplicação lógica onde pretende aceder à sua conta Outlook.com. Para iniciar o seu fluxo de trabalho com um gatilho Outlook.com, precisa de ter uma [aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para adicionar uma ação Outlook.com ao seu fluxo de trabalho, a sua aplicação lógica precisa de ter já um gatilho.

## <a name="add-a-trigger"></a>Adicionar um acionador

Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um evento que inicia o fluxo de trabalho na sua aplicação lógica. Esta aplicação lógica de exemplo usa um gatilho de "sondagem" que verifica qualquer novo e-mail na sua conta de e-mail, com base no intervalo e frequência especificados.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica em branco no Logic App Designer.

1. Na caixa de pesquisa, introduza "outlook.com" como filtro. Para este exemplo, selecione **Quando um novo e-mail chegar**.

1. Se for solicitado a iniciar sessão, forneça as suas credenciais Outlook.com para que a sua aplicação lógica possa ligar-se à sua conta. Caso contrário, se a sua ligação já existir, forneça as informações para as propriedades do gatilho:

1. No gatilho, despolete os valores **de Frequência** e **Intervalo.**

   Por exemplo, se quiser que o gatilho faça uma sondagem a cada 15 minutos, defino a **Frequência** para **o Minuto**e desloque o **intervalo** para **15**.

1. Na barra de ferramentas de design, selecione **Save**, que guarda a sua aplicação lógica.

Para responder ao gatilho, adicione outra ação. Por exemplo, pode adicionar a ação de mensagem Twilio **Enviar,** que envia um texto quando um e-mail chega.

## <a name="add-an-action"></a>Adicionar uma ação

Uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma operação que é gerida pelo fluxo de trabalho na sua aplicação lógica. Esta aplicação lógica de exemplo envia um e-mail da sua conta Outlook.com. Pode usar a saída de outro gatilho para povoar a ação. Por exemplo, suponha que a sua aplicação lógica utilize o SalesForce **Quando um objeto é criado** gatilho. Pode adicionar o Outlook.com Enviar uma ação por **e-mail** e utilizar as saídas do gatilho SalesForce no e-mail.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Para adicionar uma ação como o último passo no seu fluxo de trabalho, selecione **Novo passo**. 

   Para adicionar uma ação entre passos, mova o ponteiro sobre a seta entre esses passos. Selecione o**+** sinal de mais ( ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, introduza "outlook.com" como filtro. Para este exemplo, selecione **Enviar um e-mail**. 

1. Se for solicitado a iniciar sessão, forneça as suas credenciais Outlook.com para que a sua aplicação lógica possa ligar-se à sua conta. Caso contrário, se a sua ligação já existir, forneça as informações para as propriedades de ação.

1. Na barra de ferramentas de design, selecione **Save**, que guarda a sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos, tais como gatilhos, ações e limites, conforme descrito pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/outlook/). 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
