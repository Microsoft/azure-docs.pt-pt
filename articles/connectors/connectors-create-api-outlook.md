---
title: Ligar ao Outlook.com - Azure Logic Apps | Documentos da Microsoft
description: Gerir o e-mail, calendários e contatos com as APIs REST do Outlook.com e o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.topic: article
ms.date: 08/18/2016
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: fd6836451a73551487b8f97903594154a2efc894
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105807"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Gerir o e-mail, calendários e contatos em Outlook.com com o Azure Logic Apps

Este artigo mostra como pode criar e gerir a sua conta do Outlook.com no interior de uma aplicação lógica com o conector de caixa. Dessa forma, pode criar aplicações lógicas que automatizam as tarefas e fluxos de trabalho para a sua conta do Outlook.com, por exemplo:

* Envie mensagem de e-mail. 
* Agendar reuniões.
* Adicione contactos. 

Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um [conta do Outlook.com](https://outlook.live.com/owa/)

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* A aplicação de lógica onde pretende aceder à sua conta do Outlook.com. Para começar a sua aplicação lógica com um acionador do Outlook, tem um [aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-outlookcom"></a>Ligar ao Outlook.com

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como disparadores, ações e limites, conforme descrito pelo ficheiro de Swagger do conector, consulte a [página de referência do conector](/connectors/outlook/). 

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)