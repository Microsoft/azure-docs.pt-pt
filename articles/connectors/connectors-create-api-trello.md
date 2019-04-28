---
title: Ligue ao Trello a partir do Azure Logic Apps | Documentos da Microsoft
description: Automatizar tarefas e fluxos de trabalho que monitorizar e gerem listas, quadros e cartões em seus projetos de Trello com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: fe7a4377-5c24-4f72-ab1a-6d9d23e8d895
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 31357fa313cfa31c0f8a90c0f7722f627e4394d1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106172"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Monitorizar e gerir o Trello com o Azure Logic Apps

Com o Azure Logic Apps e o conector do Trello, pode criar tarefas automatizadas e fluxos de trabalho que monitorizar e gerir listas de Trello, cartões, quadros, os membros da Equipe e assim por diante, por exemplo:

* Monitor quando novos cartões são adicionados a listas e quadros. 
* Criar, obter e gerir quadros, cartões e listas.
* Adicione comentários e membros para cartões.
* Listar quadros, etiquetas de quadro, cartões em quadros, comentários de cartão, membros de cartão, os membros da Equipe e as equipes de onde é membro. 
* Obtenha as equipes.

Pode usar acionadores que obtém as respostas da sua conta do Trello e disponibilizar a saída para outras ações. Pode utilizar as ações que executam tarefas com a sua conta do Trello. Pode também ter outras ações utilizar a saída de ações do Trello. Por exemplo, quando é adicionado um novo cartão ao quadro ou lista, pode enviar mensagens com o conector do Slack. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* As credenciais de conta e do usuário do Trello

  As suas credenciais autorizar a aplicação lógica para criar uma ligação e aceder à sua conta do Trello.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação de lógica onde pretende aceder à sua conta do Trello. Para começar com um acionador de Trello [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação do Trello, inicie a aplicação lógica com um acionador, por exemplo, o **periodicidade** acionador.

## <a name="connect-to-trello"></a>Ligue ao Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Para aplicações de lógica em branco, na caixa de pesquisa, introduza "trello" como o filtro. Abaixo da lista de disparadores, selecione o acionador que pretende. 

   -ou-

   Para logic apps existentes, sob a última etapa em que pretende adicionar uma ação, escolha **novo passo**. 
   Na caixa de pesquisa, introduza "trello" como o filtro. 
   Abaixo da lista de ações, selecione a ação que pretende.

   Para adicionar uma ação entre passos, mova o ponteiro do mouse sobre a seta entre passos. 
   Selecione o sinal de adição (**+**) que é apresentada e, em seguida, selecione **adicionar uma ação**.

1. Se lhe for pedido para iniciar sessão no Trello, autorizar o acesso para a aplicação lógica e inicie sessão.

1. Forneça os detalhes necessários para o seu acionador selecionado ou a ação e continuar a criar o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](/connectors/trello/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)