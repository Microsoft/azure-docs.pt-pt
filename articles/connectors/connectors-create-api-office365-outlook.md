---
title: Ligar ao Office 365 Outlook
description: Automatizar tarefas e fluxos de trabalho que gerem e-mails, contactos e calendários no Office 365 Outlook utilizando apps lógicas azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/08/2020
tags: connectors
ms.openlocfilehash: b0f2b8b9c369fdb42c7e0e7f77fc090424ae3729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732719"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Gerir e-mails, contactos e calendários no Outlook do Office 365 com o Azure Logic Apps

Com [as Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e o [conector Do Outlook 365,](/connectors/office365connector/)pode criar tarefas e fluxos de trabalho automatizados que gerem a sua conta Office 365 através da construção de aplicações lógicas. Por exemplo, automatiza estas tarefas:

* Obter, enviar e responder ao e-mail. 
* Marque reuniões no seu calendário.
* Adicione e edite contactos. 

Pode utilizar qualquer gatilho para iniciar o seu fluxo de trabalho, por exemplo, quando um novo e-mail chega, quando um item de calendário é atualizado, ou quando um evento acontece num serviço de diferença, como o Salesforce. Pode utilizar ações que respondam ao evento de gatilho, por exemplo, enviar um e-mail ou criar um novo evento de calendário. 

> [!NOTE]
> Para automatizar tarefas @outlook.com @hotmail.com para uma ou conta, utilize o [conector Outlook.com](../connectors/connectors-create-api-outlook.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta do Office 365](https://www.office.com/)

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* A aplicação lógica onde pretende aceder à sua conta de Outlook do Office 365. Para iniciar o seu fluxo de trabalho com um gatilho do Office 365 Outlook, precisa de ter uma [aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para adicionar uma ação do Office 365 Outlook ao seu fluxo de trabalho, a sua aplicação lógica precisa de ter já um gatilho.

## <a name="add-a-trigger"></a>Adicionar um acionador

Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um evento que inicia o fluxo de trabalho na sua aplicação lógica. Esta aplicação lógica de exemplo usa um gatilho de "sondagem" que verifica qualquer evento de calendário atualizado na sua conta de e-mail, com base no intervalo e frequência especificados.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica em branco no Logic App Designer.

1. Na caixa de `office 365 outlook` pesquisa, introduza como filtro. Este exemplo seleciona **Quando um próximo evento começa em breve**.
   
   ![Selecione gatilho para iniciar a sua aplicação lógica](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Se for solicitado que faça o seu insessão, forneça as suas credenciais do Office 365 para que a sua aplicação lógica possa ligar-se à sua conta. Caso contrário, se a sua ligação já existir, forneça as informações para as propriedades do gatilho.

   Este exemplo seleciona o calendário que o gatilho verifica, por exemplo:

   ![Configure as propriedades do gatilho](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. No gatilho, despolete os valores **de Frequência** e **Intervalo.** Para adicionar outras propriedades disponíveis do gatilho, como **fuso horário,** selecione essas propriedades da **lista de novos parâmetros Adicionar.**

   Por exemplo, se pretender que o gatilho verifique o calendário a cada 15 `15`minutos, coloque a **Frequência** ao **Minuto**e desempente o **intervalo** para . 

   ![Definir frequência e intervalo para o gatilho](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Na barra de ferramentas de design, selecione **Guardar**.

Agora adicione uma ação que corre após os incêndios de gatilho. Por exemplo, pode adicionar a ação de mensagem Twilio **Enviar,** que envia um texto quando um evento de calendário começa em 15 minutos.

## <a name="add-an-action"></a>Adicionar uma ação

Uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma operação que é gerida pelo fluxo de trabalho na sua aplicação lógica. Esta aplicação lógica de exemplo cria um novo contacto no Office 365 Outlook. Pode utilizar a saída de outro gatilho ou ação para criar o contacto. Por exemplo, suponha que a sua aplicação lógica utilize o gatilho Dynamics 365, **quando um disco é criado**. Pode adicionar o Office 365 Outlook **Create ação** de contacto e utilizar as saídas do gatilho SalesForce para criar o novo contacto.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Para adicionar uma ação como o último passo no seu fluxo de trabalho, selecione **Novo passo**. 

   Para adicionar uma ação entre passos, mova o ponteiro sobre a seta entre esses passos. Selecione o**+** sinal de mais ( ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de `office 365 outlook` pesquisa, introduza como filtro. Este exemplo seleciona **Criar contacto**.

   ![Selecione a ação a executar na sua aplicação lógica](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Se for solicitado que faça o seu insessão, forneça as suas credenciais do Office 365 para que a sua aplicação lógica possa ligar-se à sua conta. Caso contrário, se a sua ligação já existir, forneça as informações para as propriedades da ação.

   Este exemplo seleciona a pasta de contactos onde a ação cria o novo contacto, por exemplo:

   ![Configure as propriedades da ação](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Para adicionar outras propriedades de ação disponíveis, selecione essas propriedades da **lista de novos parâmetros.**

1. Na barra de ferramentas de design, selecione **Guardar**.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites descritos no ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/office365connector/). 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
