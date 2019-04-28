---
title: Ligar ao SendGrid a partir do Azure Logic Apps | Documentos da Microsoft
description: Automatizar tarefas e fluxos de trabalho que enviar e-mails e gerir listas de correio SendGrid com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 7eecd3908883b195b52755d03e70872afe9180bb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105739"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Enviar e-mails e gerir listas de correio SendGrid com o Azure Logic Apps

Com o Azure Logic Apps e o conector do SendGrid, pode criar tarefas automatizadas e fluxos de trabalho que enviar e-mails e gerir as suas listas de destinatários, por exemplo:

* Envie mensagem de e-mail.
* Adicione os destinatários às listas.
* Obter, adicionar e gerir a supressão global.

Pode utilizar o SendGrid ações nas suas aplicações lógicas para executar estas tarefas. Pode também ter outras ações utilizar a saída de ações do SendGrid. 

Este conector proporciona apenas ações como, por isso, para iniciar a sua aplicação lógica, utilizam um acionador separado, como um **periodicidade** acionador. Por exemplo, se regularmente, adicionar os destinatários às suas listas, pode enviar por e-mail sobre os destinatários e listas de utilizar o conector do Outlook do Office 365 ou o conector do Outlook.com.
Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* R [conta de SendGrid](https://www.sendgrid.com/) e um [chave de API de SendGrid](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   A chave de API autoriza a aplicação lógica para criar uma ligação e aceder à sua conta do SendGrid.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação de lógica onde pretende aceder à sua conta do SendGrid. Para utilizar uma ação do SendGrid, inicie a aplicação lógica com outro acionador, por exemplo, o **periodicidade** acionador.

## <a name="connect-to-sendgrid"></a>Ligue ao SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Escolha um caminho: 

   * Na última etapa em que pretende adicionar uma ação, escolha **novo passo**. 

     -ou-

   * Entre os passos em que pretende adicionar uma ação, mova o ponteiro do mouse sobre a seta entre passos. 
   Selecione o sinal de adição (**+**) que é apresentada e, em seguida, selecione **adicionar uma ação**.

1. Na caixa de pesquisa, introduza "sendgrid" como o filtro. Abaixo da lista de ações, selecione a ação que pretende.

1. Indique um nome para a ligação. 

1. Introduza a chave de API do SendGrid e, em seguida, escolha **criar**.

1. Forneça os detalhes necessários para a ação selecionada e continuar a criar o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](/connectors/sendgrid/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)