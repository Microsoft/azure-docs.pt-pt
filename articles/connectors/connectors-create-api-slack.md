---
title: Ligue-se à Folga das Aplicações Lógicas Azure
description: Automatizar tarefas e fluxos de trabalho que monitorizam ficheiros e gerem canais, grupos e mensagens na sua conta Slack utilizando apps da Lógica Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5f61009ee7b43be618e37acb4a783a54dbf11e55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789176"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Monitorize e gerencie slack com aplicações lógicas azure

Com as Aplicações Lógicas Azure e o conector Slack, pode criar tarefas e fluxos de trabalho automatizados que monitorizam os seus ficheiros Slack e gerem os seus canais Slack, mensagens, grupos, e assim por diante, por exemplo:

* Monitorize quando forem criados novos ficheiros.
* Criar, listar e juntar canais 
* Postando mensagens.
* Criar grupos e definir não perturbe.

Pode utilizar gatilhos que obtêm respostas da sua conta Slack e disponibilizar a saída a outras ações. Pode utilizar ações que executem tarefas com a sua conta Slack. Também pode ter outras ações que utilizem a saída de ações de Slack. Por exemplo, quando um novo ficheiro é criado, pode enviar e-mail com o conector Do Outlook office 365. Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* A sua conta [Slack](https://slack.com/) e credenciais de utilizador

  As suas credenciais autorizam a sua aplicação lógica para criar uma ligação e aceder à sua conta Slack.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta Slack. Para começar com um gatilho Slack, [crie uma aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para utilizar uma ação Slack, inicie a sua aplicação lógica com um gatilho, como um gatilho Slack ou outro gatilho, como o gatilho **recorrência.**

## <a name="connect-to-slack"></a>Ligar a Slack

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Para aplicações lógicas em branco, na caixa de pesquisa, introduza "slack" como filtro. Na lista de gatilhos, selecione o gatilho que deseja. 

   -ou-

   Para aplicações lógicas existentes, sob o último passo em que pretende adicionar uma ação, escolha **novo passo**. 
   Na caixa de pesquisa, introduza "frouxa" como filtro. 
   Na lista de ações, selecione a ação que deseja.

   Para adicionar uma ação entre passos, mova o ponteiro sobre a seta entre os degraus. 
   Escolha o sinal**+** de mais () que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Se for solicitado que inscreva-se no Slack, inscreva-se no seu espaço de trabalho Slack. 

   ![Inscreva-se no espaço de trabalho Slack](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Autorize o acesso à sua aplicação lógica.

   ![Autorizar o acesso a Slack](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionado. Para continuar a construir o fluxo de trabalho da sua aplicação lógica, adicione mais ações.

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição OpenAPI (ex-Swagger) do conector, reveja a página de [referência](/connectors/slack/)do conector .

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
