---
title: Ligar ao Office 365 Outlook
description: Automatizar tarefas e fluxos de trabalho que gerenciam email, contatos e calendários no Office 365 Outlook usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/08/2020
tags: connectors
ms.openlocfilehash: b0f2b8b9c369fdb42c7e0e7f77fc090424ae3729
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732719"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Gerenciar emails, contatos e calendários no Office 365 Outlook usando o aplicativo lógico do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o [conector do Outlook para Office 365](/connectors/office365connector/), você pode criar tarefas automatizadas e fluxos de trabalho que gerenciam sua conta do Office 365 criando aplicativos lógicos. Por exemplo, você automatiza essas tarefas:

* Obter, enviar e responder a emails. 
* Agende reuniões no seu calendário.
* Adicionar e editar contatos. 

Você pode usar qualquer gatilho para iniciar o fluxo de trabalho, por exemplo, quando um novo email chegar, quando um item de calendário for atualizado ou quando um evento ocorrer em um serviço de diferença, como o Salesforce. Você pode usar ações que respondem ao evento de gatilho, por exemplo, enviar um email ou criar um novo evento de calendário. 

> [!NOTE]
> Para automatizar tarefas para uma conta de @outlook.com ou @hotmail.com, use o [conector do Outlook.com](../connectors/connectors-create-api-outlook.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta do Office 365](https://www.office.com/)

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* O aplicativo lógico no qual você deseja acessar sua conta do Outlook do Office 365. Para iniciar o fluxo de trabalho com um gatilho do Outlook do Office 365, você precisa ter um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para adicionar uma ação do Outlook do Office 365 ao seu fluxo de trabalho, seu aplicativo lógico precisa já ter um gatilho.

## <a name="add-a-trigger"></a>Adicionar um acionador

Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um evento que inicia o fluxo de trabalho em seu aplicativo lógico. Esse aplicativo lógico de exemplo usa um gatilho de "sondagem" que verifica qualquer evento de calendário atualizado em sua conta de email, com base no intervalo e na frequência especificados.

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico em branco no designer de aplicativo lógico.

1. Na caixa de pesquisa, insira `office 365 outlook` como seu filtro. Este exemplo seleciona **quando um evento futuro é iniciado em breve**.
   
   ![Selecione o gatilho para iniciar seu aplicativo lógico](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Se você for solicitado a entrar, forneça suas credenciais do Office 365 para que seu aplicativo lógico possa se conectar à sua conta. Caso contrário, se a conexão já existir, forneça as informações para as propriedades do gatilho.

   Este exemplo seleciona o calendário que o gatilho verifica, por exemplo:

   ![Configurar as propriedades do gatilho](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. No gatilho, defina os valores de **frequência** e **intervalo** . Para adicionar outras propriedades de gatilho disponíveis, como **fuso horário**, selecione essas propriedades na lista **Adicionar novo parâmetro** .

   Por exemplo, se você quiser que o gatilho Verifique o calendário a cada 15 minutos, defina a **frequência** como **minuto**e defina o **intervalo** como `15`. 

   ![Definir a frequência e o intervalo para o gatilho](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Na barra de ferramentas do designer, selecione **salvar**.

Agora, adicione uma ação que é executada Depois que o gatilho é acionado. Por exemplo, você pode adicionar a ação **Enviar mensagem** twilio, que envia um texto quando um evento de calendário é iniciado em 15 minutos.

## <a name="add-an-action"></a>Adicionar uma ação

Uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma operação que é executada pelo fluxo de trabalho em seu aplicativo lógico. Este aplicativo lógico de exemplo cria um novo contato no Office 365 Outlook. Você pode usar a saída de outro gatilho ou ação para criar o contato. Por exemplo, suponha que seu aplicativo lógico use o gatilho do Dynamics 365, **quando um registro é criado**. Você pode adicionar a ação **criar contato** do Office 365 Outlook e usar as saídas do gatilho do Salesforce para criar o novo contato.

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer do aplicativo lógico.

1. Para adicionar uma ação como a última etapa no fluxo de trabalho, selecione **nova etapa**. 

   Para adicionar uma ação entre etapas, mova o ponteiro sobre a seta entre essas etapas. Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira `office 365 outlook` como seu filtro. Este exemplo seleciona **criar contato**.

   ![Selecione a ação a ser executada em seu aplicativo lógico](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Se você for solicitado a entrar, forneça suas credenciais do Office 365 para que seu aplicativo lógico possa se conectar à sua conta. Caso contrário, se a conexão já existir, forneça as informações para as propriedades da ação.

   Este exemplo seleciona a pasta contatos na qual a ação cria o novo contato, por exemplo:

   ![Configurar as propriedades da ação](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Para adicionar outras propriedades de ação disponíveis, selecione essas propriedades na lista **Adicionar novo parâmetro** .

1. Na barra de ferramentas do designer, selecione **salvar**.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, conforme descrito no arquivo Swagger do conector, consulte a [página de referência do conector](/connectors/office365connector/). 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
