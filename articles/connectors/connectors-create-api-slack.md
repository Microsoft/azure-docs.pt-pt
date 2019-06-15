---
title: Ligar ao Slack a partir do Azure Logic Apps | Documentos da Microsoft
description: Automatizar tarefas e fluxos de trabalho que arquivos de monitorizar e gerem canais, grupos e mensagens na sua conta do Slack com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 675e37120b06af3add58b564495f22875647a0fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105654"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Monitorizar e gerir o Slack com o Azure Logic Apps

Com o Azure Logic Apps e o conector do Slack, pode criar tarefas automatizadas e fluxos de trabalho que seus arquivos Slack de monitorizar e gerir os seus canais Slack, mensagens, grupos e assim por diante, por exemplo:

* Monitor quando são criados novos ficheiros.
* Criar, listar e Junte-se de canais 
* Publicar mensagens.
* Criar grupos e o conjunto não incomodar.

Pode usar acionadores que obtém as respostas da sua conta do Slack e disponibilizar a saída para outras ações. Pode utilizar as ações que executam tarefas com a sua conta do Slack. Pode também ter outras ações utilizar a saída de ações Slack. Por exemplo, quando um novo ficheiro é criado, pode enviar e-mails com o conector do Outlook do Office 365. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* Sua [Slack](https://slack.com/) credenciais de conta e de utilizador

  As suas credenciais autorizar a aplicação lógica para criar uma ligação e aceder à sua conta do Slack.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação de lógica onde pretende aceder à sua conta do Slack. Para começar com um acionador Slack, [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação Slack, iniciar a aplicação lógica com um acionador, tal como um acionador Slack ou outro acionador, tal como o **periodicidade** acionador.

## <a name="connect-to-slack"></a>Ligue-se ao Slack

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Para aplicações de lógica em branco, na caixa de pesquisa, introduza "slack" como o filtro. Abaixo da lista de disparadores, selecione o acionador que pretende. 

   -ou-

   Para logic apps existentes, sob a última etapa em que pretende adicionar uma ação, escolha **novo passo**. 
   Na caixa de pesquisa, introduza "slack" como o filtro. 
   Abaixo da lista de ações, selecione a ação que pretende.

   Para adicionar uma ação entre passos, mova o ponteiro do mouse sobre a seta entre passos. 
   Selecione o sinal de adição ( **+** ) que é apresentada e, em seguida, selecione **adicionar uma ação**.

1. Se lhe for pedido para iniciar sessão no Slack, inicie sessão na sua área de trabalho Slack. 

   ![Inicie sessão no Slack espaço de trabalho](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Autorize o acesso para a aplicação lógica.

   ![Autorizar o acesso ao Slack](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Forneça os detalhes necessários para o acionador selecionado ou ação. Para continuar a criar o fluxo de trabalho da sua aplicação lógica, adicione mais ações.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](/connectors/slack/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
