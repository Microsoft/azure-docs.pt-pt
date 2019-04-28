---
title: Ligar ao Office 365 Outlook - Azure Logic Apps | Documentos da Microsoft
description: Gerir e-mails, contatos e calendários com APIs de REST do Office 365 e Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: v-yiso
origin.date: 10/18/2016
ms.date: 09/03/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 52abf17e869216e65780129a7b48df79bd79f67a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105059"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Começar com o conector do Outlook do Office 365
O conector do Outlook do Office 365 permite a interação com o Outlook do Office 365. Utilize este conector para criar, editar e atualizar contactos e itens de calendário e também obtém, enviar e responder a e-mail.

Com o Outlook do Office 365,:

* Crie o seu fluxo de trabalho usando os recursos de email e calendário no Office 365. 
* Utilize os acionadores para iniciar o seu fluxo de trabalho quando existe um novo e-mail, quando um item de calendário é atualizado e muito mais.
* Utilize ações para enviar um e-mail, criar um novo evento de calendário e muito mais. Por exemplo, quando existe um novo objeto no Salesforce (um acionador), envie um e-mail para o Outlook do Office 365 (uma ação). 

Este artigo mostra-lhe como utilizar o conector do Outlook do Office 365 numa aplicação lógica e também lista os acionadores e ações.

> [!NOTE]
> Esta versão do artigo aplica-se em disponibilidade geral do Logic Apps (GA).
> 
> 

Para saber mais sobre o Logic Apps, veja [o que são as aplicações lógicas](../logic-apps/logic-apps-overview.md) e [criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office-365"></a>Ligar ao Office 365
Antes da aplicação lógica pode aceder a qualquer serviço, primeiro tem de criar uma *ligação* ao serviço. Uma ligação fornece conectividade entre uma aplicação lógica e outro serviço. Por exemplo, para ligar ao Office 365 Outlook, primeiro tem de um Office 365 *ligação*. Para criar uma ligação, introduza as credenciais que normalmente utiliza para aceder ao serviço que deseja se conectar. Então, com o Outlook do Office 365, introduza as credenciais para a sua conta do Office 365 para criar a ligação.

## <a name="create-the-connection"></a>Criar a ligação
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Utilizar um acionador
Um acionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho definido numa aplicação lógica. Acionadores "consultam" o serviço num intervalo e a frequência que pretenda. [Saiba mais sobre os acionadores](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Na aplicação lógica, escreva "office 365" para obter uma lista dos acionadores:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Selecione **Outlook do Office 365 - quando um evento futuro está a iniciar em breve**. Se já existir uma ligação, em seguida, selecione um calendário da lista pendente.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Se lhe for pedido para iniciar sessão, em seguida, introduza o início de sessão nos detalhes para criar a ligação. [Criar a ligação](connectors-create-api-office365-outlook.md#create-the-connection) este tópico lista os passos. 
   
   > [!NOTE]
   > Neste exemplo, a aplicação lógica executa quando um evento de calendário é atualizado. Para ver os resultados deste acionador, adicione outra ação que envia uma mensagem de texto. Por exemplo, adicionar o Twilio *mensagem de envio* ação esse textos quando o evento do calendário é a partir de 15 minutos. 
   > 
   > 
3. Selecione o **edite** botão e defina a **frequência** e **intervalo** valores. Por exemplo, se pretender que o acionador para consultar a cada 15 minutos, em seguida, defina o **frequência** ao **minuto**e defina o **intervalo** para **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Guardar** as alterações (canto superior esquerdo da barra de ferramentas). A aplicação lógica é guardada e pode ser ativada automaticamente.

## <a name="use-an-action"></a>Utilize uma ação
Uma ação é uma operação levada a cabo pelo fluxo de trabalho definido numa aplicação lógica. [Saiba mais sobre as ações](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Selecione o sinal. Verá várias opções: **Adicionar uma ação**, **adicionar uma condição**, ou uma da **mais** opções.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Escolher **adicionar uma ação**.
3. Na caixa de texto, digite "office 365" para obter uma lista de todas as ações disponíveis.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. No nosso exemplo, escolha **Office 365 Outlook – criar contacto**. Se já existir uma ligação, em seguida, escolha o **ID da pasta**, **nome fornecido**e outras propriedades:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Se lhe for pedido para obter as informações de ligação, em seguida, introduza os detalhes para criar a ligação. [Criar a ligação](connectors-create-api-office365-outlook.md#create-the-connection) neste tópico descreve essas propriedades. 
   
   > [!NOTE]
   > Neste exemplo, vamos criar um novo contacto no Outlook do Office 365. Pode utilizar a saída de outro acionador para criar o contacto. Por exemplo, adicionar o SalesForce *quando é criado um objeto* acionador. Em seguida, adicione o Outlook do Office 365 *criar contacto* ação que utiliza os campos do SalesForce para criar o novo contacto no Office 365. 
   > 
   > 
5. **Guardar** as alterações (canto superior esquerdo da barra de ferramentas). A aplicação lógica é guardada e pode ser ativada automaticamente.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver os acionadores e as ações definidas no swagger e também ver quaisquer limites na [detalhes do conector](/connectors/office365connector/). 

## <a name="next-steps"></a>Próximos Passos
[Criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Explore os outros conectores disponíveis no Logic Apps em nosso [lista APIs](apis-list.md).

