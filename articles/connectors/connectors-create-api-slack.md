---
title: Conectar-se à margem de atraso dos aplicativos lógicos do Azure
description: Automatizar tarefas e fluxos de trabalho que monitoram arquivos e gerenciam canais, grupos e mensagens em sua conta de margem de atraso usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5f61009ee7b43be618e37acb4a783a54dbf11e55
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789176"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Monitorar e gerenciar a margem de atraso com os aplicativos lógicos do Azure

Com os aplicativos lógicos do Azure e o conector de margem de atraso, você pode criar tarefas e fluxos de trabalho automatizados que monitorem seus arquivos de margem de atraso e gerenciem seus canais de margem de atraso, mensagens, grupos e assim por diante, por exemplo:

* Monitor quando novos arquivos são criados.
* Criar, listar e unir canais 
* Postar mensagens.
* Crie grupos e defina não incomodar.

Você pode usar gatilhos que obtêm respostas de sua conta de margem de atraso e disponibilizam a saída para outras ações. Você pode usar ações que executam tarefas com sua conta de margem de atraso. Você também pode fazer com que outras ações usem a saída das ações de margem de atraso. Por exemplo, quando um novo arquivo é criado, você pode enviar email com o conector do Outlook para Office 365. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Sua conta de [margem de atraso](https://slack.com/) e as credenciais do usuário

  Suas credenciais autorizam seu aplicativo lógico a criar uma conexão e acessar sua conta de margem de atraso.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta de margem de atraso. Para começar com um gatilho de margem de atraso, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação de margem de atraso, inicie seu aplicativo lógico com um gatilho, como um gatilho de margem de atraso ou outro gatilho, como o gatilho de **recorrência** .

## <a name="connect-to-slack"></a>Conectar à margem de atraso

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, digite "margem de atraso" como filtro. Na lista de gatilhos, selecione o gatilho desejado. 

   -ou-

   Para os aplicativos lógicos existentes, na última etapa em que você deseja adicionar uma ação, escolha **nova etapa**. 
   Na caixa de pesquisa, insira "margem de atraso" como filtro. 
   Na lista ações, selecione a ação desejada.

   Para adicionar uma ação entre etapas, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Se você for solicitado a entrar na margem de atraso, entre no espaço de trabalho de margem de atraso. 

   ![Espaço de trabalho entrar em margem de atraso](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Autorize o acesso para seu aplicativo lógico.

   ![Autorizar o acesso à margem de atraso](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Forneça os detalhes necessários para o gatilho ou a ação selecionada. Para continuar criando o fluxo de trabalho do aplicativo lógico, adicione mais ações.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](/connectors/slack/)do conector.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
