---
title: Ligue-se a SendGrid a partir de apps Azure Logic
description: Automatizar tarefas e fluxos de trabalho que enviam e-mails e gerem listas de correio eletrónico em SendGrid utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: a140ae0f27c61959d8ebc6854c5bcb2a916a0fc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87290448"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Envie e-mails e gere listas de correio em SendGrid usando Azure Logic Apps

Com as Apps Azure Logic e o conector SendGrid, pode criar tarefas e fluxos de trabalho automatizados que enviam e-mails e gerem as suas listas de destinatários, por exemplo:

* Envie e-mail.
* Adicione os destinatários às listas.
* Obter, adicionar e gerir a supressão global.

Pode utilizar as ações da SendGrid nas suas aplicações lógicas para executar estas tarefas. Também pode ter outras ações que utilizem a saída das ações da SendGrid. 

Este conector fornece apenas ações, por isso, para iniciar a sua aplicação lógica, utilize um gatilho separado, como um gatilho **de Recorrência.** Por exemplo, se adicionar regularmente destinatários às suas listas, pode enviar e-mails sobre destinatários e listas utilizando o conector do Office 365 Outlook ou o conector Outlook.com.
Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Uma [conta SendGrid](https://www.sendgrid.com/) e uma [chave API SendGrid](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   A sua chave API autoriza a sua aplicação lógica a criar uma ligação e aceder à sua conta SendGrid.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta SendGrid. Para utilizar uma ação SendGrid, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **de Recorrência.**

## <a name="connect-to-sendgrid"></a>Ligar ao SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se não abrir já.

1. Escolha um caminho: 

   * Sob o último passo onde pretende adicionar uma ação, escolha **Novo passo**. 

     -ou-

   * Entre os passos onde pretende adicionar uma ação, mova o ponteiro sobre a seta entre os degraus. 
   Escolha o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Na caixa de pesquisa, introduza "sendgrid" como filtro. Na lista de ações, selecione a ação desejada.

1. Indique um nome para a ligação. 

1. Insira a sua tecla SendGrid API e, em seguida, escolha **Criar**.

1. Forneça os detalhes necessários para a sua ação selecionada e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição openAPI (anteriormente Swagger) do conector, consulte a página de [referência](/connectors/sendgrid/)do conector .

## <a name="get-support"></a>Obter suporte

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
