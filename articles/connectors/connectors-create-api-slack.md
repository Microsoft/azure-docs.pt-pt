---
title: Conecte-se à Slack a partir de apps Azure Logic
description: Automatizar tarefas e fluxos de trabalho que monitorizam ficheiros e gerem canais, grupos e mensagens na sua conta Slack utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 194f75fbb486d5c53a8e650631c16fcf2649f277
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83826031"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Monitorize e gere slack com Azure Logic Apps

Com as Apps Azure Logic e o conector Slack, pode criar tarefas e fluxos de trabalho automatizados que monitorizam os seus ficheiros Slack e gerem os seus canais, mensagens, grupos e assim por diante, por exemplo:

* Monitorize quando forem criados novos ficheiros.
* Criar, listar e juntar canais 
* Poste mensagens.
* Criar grupos e definir não perturbe.

Pode utilizar gatilhos que obtenham respostas da sua conta Slack e disponibilize a saída para outras ações. Pode utilizar ações que executem tarefas com a sua conta Slack. Também pode ter outras ações que utilizem a saída das ações slack. Por exemplo, quando um novo ficheiro é criado, pode enviar e-mail com o conector Office 365 Outlook. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* A sua conta [Slack](https://slack.com/) e credenciais de utilizador

  As suas credenciais autorizam a sua aplicação lógica a criar uma ligação e aceder à sua conta Slack.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta Slack. Para começar com um gatilho Slack, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação Slack, inicie a sua aplicação lógica com um gatilho, como um gatilho Slack ou outro gatilho, como o gatilho **Recorrence.**

## <a name="connect-to-slack"></a>Ligar-se a Slack

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se não abrir já.

1. Para aplicações lógicas em branco, na caixa de pesquisa, introduza "slack" como filtro. Na lista de gatilhos, selecione o gatilho que pretende. 

   -ou-

   Para aplicações lógicas existentes, sob o último passo onde pretende adicionar uma ação, escolha **Novo passo**. 
   Na caixa de pesquisa, introduza "slack" como filtro. 
   Na lista de ações, selecione a ação desejada.

   Para adicionar uma ação entre os degraus, mova o ponteiro sobre a seta entre os degraus. 
   Escolha o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Se lhe pedirem para se inscrever no Slack, inscreva-se no seu espaço de trabalho slack. 

   ![Inscreva-se no espaço de trabalho slack](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Autorize o acesso para a sua aplicação lógica.

   ![Autorizar o acesso à Slack](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionados. Para continuar a construir o fluxo de trabalho da sua aplicação lógica, adicione mais ações.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição openAPI (anteriormente Swagger) do conector, consulte a página de [referência](/connectors/slack/)do conector .

## <a name="get-support"></a>Obter suporte

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
