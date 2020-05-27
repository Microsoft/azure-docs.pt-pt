---
title: Ligue-se a SendGrid a partir de Aplicações Lógicas Azure
description: Automatizar tarefas e fluxos de trabalho que enviam e-mails e gerem listas de correio em SendGrid utilizando aplicações da Lógica Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: b86a38278ae345fac376958d18912a878a53872b
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834803"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Envie e-mails e gereas listas de correio em SendGrid utilizando aplicações da Lógica Azure

Com as Aplicações Lógicas Azure e o conector SendGrid, pode criar tarefas automatizadas e fluxos de trabalho que enviam e-mails e gerem as listas de destinatários, por exemplo:

* Envie e-mail.
* Adicione destinatários a listas.
* Obter, adicionar e gerir a supressão global.

Pode utilizar ações do SendGrid nas suas aplicações lógicas para executar estas tarefas. Também pode ter outras ações a utilizar a saída a partir de ações da SendGrid. 

Este conector fornece apenas ações, para iniciar a sua aplicação lógica, usar um gatilho separado, como um gatilho **de Recorrência.** Por exemplo, se adicionar regularmente destinatários às suas listas, pode enviar e-mail sobre destinatários e listas utilizando o conector do Office 365 Outlook ou Outlook.com conector.
Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Uma [conta SendGrid](https://www.sendgrid.com/) e uma [chave SendGrid API](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   A sua chave API autoriza a sua aplicação lógica para criar uma ligação e aceder à sua conta SendGrid.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta SendGrid. Para utilizar uma ação SendGrid, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **Recurrence.**

## <a name="connect-to-sendgrid"></a>Ligar ao SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Escolha um caminho: 

   * Sob o último passo em que pretende adicionar uma ação, escolha **novo passo**. 

     -ou-

   * Entre os degraus onde pretende adicionar uma ação, mova o ponteiro sobre a seta entre os degraus. 
   Escolha o sinal de mais **+** () que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, introduza "sendgrid" como filtro. Na lista de ações, selecione a ação que deseja.

1. Indique um nome para a ligação. 

1. Introduza a sua chave SendGrid API e, em seguida, escolha **Criar**.

1. Forneça os detalhes necessários para a sua ação selecionada e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição OpenAPI (ex-Swagger) do conector, reveja a página de [referência](/connectors/sendgrid/)do conector .

## <a name="get-support"></a>Obter suporte

* Para perguntas, visite o [Microsoft Q&Uma página de perguntas para aplicações lógicas do Azure](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)