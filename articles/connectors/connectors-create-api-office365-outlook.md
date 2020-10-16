---
title: Ligar ao Office 365 Outlook
description: Automatizar tarefas e fluxos de trabalho que gerem e-mails, contactos e calendários no Office 365 Outlook utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 07/27/2020
tags: connectors
ms.openlocfilehash: 9b10778e665675e9e033953e2a8b9df16dd636d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400779"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Gerir e-mails, contactos e calendários no Outlook do Office 365 com o Azure Logic Apps

Com [as Apps Azure Logic](../logic-apps/logic-apps-overview.md) e o [conector Office 365 Outlook,](/connectors/office365connector/)pode criar tarefas e fluxos de trabalho automatizados que gerem o seu trabalho ou conta escolar através da construção de aplicações lógicas. Por exemplo, automatiza estas tarefas:

* Receber, enviar e responder ao e-mail. 
* Marque reuniões no seu calendário.
* Adicione e edite os contactos. 

Pode utilizar qualquer gatilho para iniciar o seu fluxo de trabalho, por exemplo, quando um novo e-mail chega, quando um item de calendário é atualizado, ou quando um evento acontece num serviço de diferença, como o Salesforce. Pode utilizar ações que respondam ao evento de gatilho, por exemplo, enviar um e-mail ou criar um novo evento de calendário. 

> [!NOTE]
> Para automatizar tarefas para uma @outlook.com ou @hotmail.com conta, utilize o [conector Outlook.com](../connectors/connectors-create-api-outlook.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

* Uma [conta de trabalho ou escola](https://www.office.com/)

* A aplicação lógica onde pretende aceder ao seu trabalho ou à sua conta escolar. Para iniciar o seu fluxo de trabalho com um gatilho do Office 365 Outlook, precisa de ter uma [aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para adicionar uma ação do Office 365 Outlook ao seu fluxo de trabalho, a sua aplicação lógica já precisa de ter um gatilho.

## <a name="add-a-trigger"></a>Adicionar um acionador

Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um evento que inicia o fluxo de trabalho na sua aplicação lógica. Esta aplicação lógica de exemplo utiliza um gatilho de "sondagens" que verifica qualquer evento de calendário atualizado na sua conta de e-mail, com base no intervalo e frequência especificados.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica em branco no Logic App Designer.

1. Na caixa de pesquisa, introduza `office 365 outlook` como filtro. Este exemplo seleciona **Quando um próximo evento começa em breve**.
   
   ![Selecione o gatilho para iniciar a sua aplicação lógica](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Se for solicitado a iniciar sedutar, forneça as suas credenciais de trabalho ou escola para que a sua aplicação lógica possa ligar-se à sua conta. Caso contrário, se a sua ligação já existir, forneça as informações para as propriedades do gatilho.

   > [!NOTE]
   > A sua ligação não expira até ser revogada, mesmo que altere as suas credenciais de inscrição. Para obter mais informações, consulte [as vidas de símbolos configurantes no Diretório Ativo Azure](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   Este exemplo seleciona o calendário que o gatilho verifica, por exemplo:

   ![Configure as propriedades do gatilho](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. No gatilho, desaccione os valores **de Frequência** e **Intervalo.** Para adicionar outras propriedades disponíveis do gatilho, como **o Fuso Horário,** selecione essas propriedades da nova lista de **parâmetros Add.**

   Por exemplo, se quiser que o gatilho verifique o calendário a cada 15 minutos, desaccione **a frequência** ao **minuto**e desemocione **o intervalo** para `15` . 

   ![Definir frequência e intervalo para o gatilho](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Na barra de ferramentas do designer, **selecione Save**.

Agora adicione uma ação que corre depois do gatilho disparar. Por exemplo, pode adicionar a ação de mensagem Twilio **Enviar,** que envia um texto quando um evento de calendário começa em 15 minutos.

## <a name="add-an-action"></a>Adicionar uma ação

Uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma operação que é gerida pelo fluxo de trabalho na sua aplicação lógica. Esta aplicação lógica de exemplo cria um novo contacto no Office 365 Outlook. Pode utilizar a saída a partir de outro gatilho ou ação para criar o contacto. Por exemplo, suponha que a sua aplicação lógica utilize o gatilho Dynamics 365, **quando um disco é criado**. Pode adicionar o Office 365 Outlook **Criar ação de contacto** e utilizar as saídas do gatilho SalesForce para criar o novo contacto.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Para adicionar uma ação como último passo no seu fluxo de trabalho, selecione **Novo passo**. 

   Para adicionar uma ação entre os degraus, mova o ponteiro sobre a seta entre esses degraus. Selecione o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Na caixa de pesquisa, introduza `office 365 outlook` como filtro. Este exemplo seleciona **Criar contacto.**

   ![Selecione a ação para executar na sua aplicação lógica](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Se for solicitado a iniciar sedutar, forneça as suas credenciais de trabalho ou escola para que a sua aplicação lógica possa ligar-se à sua conta. Caso contrário, se a sua ligação já existir, forneça as informações para os imóveis da ação.

   > [!NOTE]
   > A sua ligação não expira até ser revogada, mesmo que altere as suas credenciais de inscrição. Para obter mais informações, consulte [as vidas de símbolos configurantes no Diretório Ativo Azure](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   Este exemplo seleciona a pasta de contactos onde a ação cria o novo contacto, por exemplo:

   ![Configure os imóveis da ação](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Para adicionar outras propriedades de ação disponíveis, selecione essas propriedades da nova lista de **parâmetros Adicionar.**

1. Na barra de ferramentas do designer, **selecione Save**.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre este conector, tais como gatilhos, ações e limites, conforme descrito pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/office365/). 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)