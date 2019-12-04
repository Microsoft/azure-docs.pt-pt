---
title: Conectar-se ao Trello de aplicativos lógicos do Azure
description: Automatizar tarefas e fluxos de trabalho que monitoram e gerenciam listas, placas e cartões em seus projetos do Trello usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5c4fcb9b4fea1a4d982b5cf665564599d371b7cb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789142"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Monitorar e gerenciar o Trello com os aplicativos lógicos do Azure

Com os aplicativos lógicos do Azure e o conector do Trello, você pode criar tarefas e fluxos de trabalho automatizados que monitoram e gerenciam suas listas, cartões, placas, membros da equipe e assim por diante, por exemplo:

* Monitore quando novos cartões são adicionados a placas e listas. 
* Crie, obtenha e gerencie quadros, cartões e listas.
* Adicionar comentários e membros a cartões.
* Placas de lista, rótulos de quadro, cartões em placas, comentários de cartão, membros de cartão, membros da equipe e equipes em que você é membro. 
* Obtenha equipes.

Você pode usar gatilhos que obtêm respostas de sua conta do Trello e disponibilizam a saída para outras ações. Você pode usar ações que executam tarefas com sua conta do Trello. Você também pode fazer com que outras ações usem a saída de ações Trello. Por exemplo, quando um novo cartão é adicionado ao quadro ou à lista, você pode enviar mensagens com o conector de margem de atraso. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Sua conta do Trello e as credenciais do usuário

  Suas credenciais autorizam seu aplicativo lógico a criar uma conexão e acessar sua conta do Trello.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta do Trello. Para começar com um gatilho Trello, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação Trello, inicie o aplicativo lógico com um gatilho, por exemplo, o gatilho **recorrência** .

## <a name="connect-to-trello"></a>Conectar-se ao Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, digite "trello" como filtro. Na lista de gatilhos, selecione o gatilho desejado. 

   -ou-

   Para os aplicativos lógicos existentes, na última etapa em que você deseja adicionar uma ação, escolha **nova etapa**. 
   Na caixa de pesquisa, digite "trello" como filtro. 
   Na lista ações, selecione a ação desejada.

   Para adicionar uma ação entre etapas, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Se você for solicitado a entrar no Trello, autorize o acesso para seu aplicativo lógico e entre.

1. Forneça os detalhes necessários para o gatilho ou ação selecionado e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](/connectors/trello/)do conector.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)