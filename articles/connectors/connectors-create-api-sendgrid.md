---
title: Conectar-se ao SendGrid de aplicativos lógicos do Azure | Microsoft Docs
description: Automatizar tarefas e fluxos de trabalho que enviam emails e gerenciam listas de endereçamento no SendGrid usando aplicativos lógicos do Azure
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
ms.openlocfilehash: 4f5efd73ef2a08069e3b9f2c7d60be99c209ddca
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050805"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Enviar emails e gerenciar listas de endereçamento no SendGrid usando os aplicativos lógicos do Azure

Com os aplicativos lógicos do Azure e o conector do SendGrid, você pode criar tarefas e fluxos de trabalho automatizados que enviam emails e gerenciam suas listas de destinatários, por exemplo:

* Enviar email.
* Adicionar destinatários a listas.
* Obtenha, adicione e gerencie a supressão global.

Você pode usar ações SendGrid em seus aplicativos lógicos para executar essas tarefas. Você também pode fazer com que outras ações usem a saída de ações SendGrid. 

Esse conector fornece apenas ações, portanto, para iniciar seu aplicativo lógico, use um gatilho separado, como um gatilho de recorrência. Por exemplo, se você Adicionar destinatários regularmente às suas listas, poderá enviar um email sobre destinatários e listas usando o conector do Outlook do Office 365 ou o conector do Outlook.com.
Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Uma [conta do SendGrid](https://www.sendgrid.com/) e uma [chave de API do SendGrid](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Sua chave de API autoriza seu aplicativo lógico a criar uma conexão e acessar sua conta do SendGrid.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta do SendGrid. Para usar uma ação SendGrid, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho recorrência.

## <a name="connect-to-sendgrid"></a>Conectar-se ao SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Escolha um caminho: 

   * Na última etapa em que você deseja adicionar uma ação, escolha **nova etapa**. 

     -ou-

   * Entre as etapas em que você deseja adicionar uma ação, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal de adição **+** () que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, digite "sendgrid" como filtro. Na lista ações, selecione a ação desejada.

1. Indique um nome para a ligação. 

1. Insira sua chave de API do SendGrid e escolha **criar**.

1. Forneça os detalhes necessários para a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](/connectors/sendgrid/)do conector.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outros conectores de [aplicativos lógicos](../connectors/apis-list.md)