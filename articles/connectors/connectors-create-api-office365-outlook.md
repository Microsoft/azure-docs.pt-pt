---
title: Conectar-se ao Office 365 Outlook
description: Gerenciar emails, contatos e calendários com APIs REST do Office 365 e aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 858366947fe21a20d6f112fc51899d1533a36472
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789597"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Introdução ao conector do Outlook para Office 365
O conector do Outlook para Office 365 permite a interação com o Outlook no Office 365. Use esse conector para criar, editar e atualizar contatos e itens de calendário, além de obter, enviar e responder a emails.

Com o Outlook do Office 365, você:

* Crie seu fluxo de trabalho usando os recursos de email e calendário no Office 365. 
* Use gatilhos para iniciar o fluxo de trabalho quando houver um novo email, quando um item de calendário for atualizado e muito mais.
* Use ações para enviar um email, criar um novo evento de calendário e muito mais. Por exemplo, quando há um novo objeto no Salesforce (um gatilho), envie um email para o Outlook do Office 365 (uma ação). 

Este artigo mostra como usar o conector do Outlook do Office 365 em um aplicativo lógico e também lista os gatilhos e as ações.

> [!NOTE]
> Esta versão do artigo se aplica à disponibilidade geral de aplicativos lógicos (GA).
> 
> 

Para saber mais sobre os aplicativos lógicos, consulte [o que são aplicativos lógicos](../logic-apps/logic-apps-overview.md) e [criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office-365"></a>Ligar ao Office 365
Antes que seu aplicativo lógico possa acessar qualquer serviço, você primeiro cria uma *conexão* com o serviço. Uma conexão fornece conectividade entre um aplicativo lógico e outro serviço. Por exemplo, para se conectar ao Office 365 Outlook, você precisa primeiro de uma *conexão*do Office 365. Para criar uma conexão, insira as credenciais que você normalmente usa para acessar o serviço ao qual você deseja se conectar. Com o Outlook do Office 365, insira as credenciais para sua conta do Office 365 para criar a conexão.

## <a name="create-the-connection"></a>Criar a ligação
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Usar um gatilho
Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. Dispara a "sondagem" do serviço em um intervalo e uma frequência que você deseja. [Saiba mais sobre gatilhos](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. No aplicativo lógico, digite "Office 365" para obter uma lista dos gatilhos:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Selecione **Office 365 Outlook-quando um evento futuro for iniciado em breve**. Se já existir uma conexão, selecione um calendário na lista suspensa.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Se você for solicitado a entrar, insira os detalhes de entrada para criar a conexão. [Criar a conexão](connectors-create-api-office365-outlook.md#create-the-connection) neste tópico lista as etapas. 
   
   > [!NOTE]
   > Neste exemplo, o aplicativo lógico é executado quando um evento de calendário é atualizado. Para ver os resultados desse gatilho, adicione outra ação que envie uma mensagem de texto para você. Por exemplo, adicione a ação twilio *Enviar mensagem* que faz o texto quando o evento de calendário é iniciado em 15 minutos. 
   > 
   > 
3. Selecione o botão **Editar** e defina os valores de **frequência** e **intervalo** . Por exemplo, se você quiser que o gatilho seja sondado a cada 15 minutos, defina a **frequência** como **minuto**e defina o **intervalo** como **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Salve** suas alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

## <a name="use-an-action"></a>Usar uma ação
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Selecione o sinal de adição. Você verá várias opções: **Adicionar uma ação**, **Adicionar uma condição**ou uma das **mais** opções.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Escolha **Adicionar uma ação**.
3. Na caixa de texto, digite "Office 365" para obter uma lista de todas as ações disponíveis.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. Em nosso exemplo, escolha **Office 365 Outlook-criar contato**. Se já existir uma conexão, escolha a **ID da pasta**, o **nome fornecido**e outras propriedades:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Se as informações de conexão forem solicitadas, insira os detalhes para criar a conexão. [Criar a conexão](connectors-create-api-office365-outlook.md#create-the-connection) neste tópico descreve essas propriedades. 
   
   > [!NOTE]
   > Neste exemplo, criamos um novo contato no Office 365 Outlook. Você pode usar a saída de outro gatilho para criar o contato. Por exemplo, adicione um gatilho do SalesForce *quando um objeto for criado* . Em seguida, adicione a ação *criar contato* do Office 365 Outlook que usa os campos do Salesforce para criar o novo contato no Office 365. 
   > 
   > 
5. **Salve** suas alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba todos os gatilhos e ações definidos no Swagger e também veja os limites nos [detalhes do conector](/connectors/office365connector/). 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)