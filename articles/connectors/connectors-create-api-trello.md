---
title: Ligue-se a Trello a partir de Aplicações Lógicas Azure
description: Automatizar tarefas e fluxos de trabalho que monitorizam e gerem listas, quadros e cartões nos seus projetos Trello utilizando apps da Lógica Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5c4fcb9b4fea1a4d982b5cf665564599d371b7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789142"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Monitorize e gerencie Trello com aplicações lógicas azure

Com as Aplicações Lógicas Azure e o conector Trello, pode criar tarefas automatizadas e fluxos de trabalho que monitorizam e gerem as suas listas trello, cartões, quadros, membros da equipa, e assim por diante, por exemplo:

* Monitorize quando os novos cartões forem adicionados aos quadros e listas. 
* Criar, obter e gerir quadros, cartões e listas.
* Adicione comentários e membros às cartas.
* Quadros de listas, etiquetas de tabuleiro, cartões em placas, comentários de cartões, membros do cartão, membros da equipa e equipas onde você é membro. 
* Arranja equipas.

Pode utilizar gatilhos que obtêm respostas da sua conta Trello e disponibilizar a saída a outras ações. Pode utilizar ações que executem tarefas com a sua conta Trello. Também pode ter outras ações a utilizar a saída das ações da Trello. Por exemplo, quando um novo cartão é adicionado à placa ou lista, pode enviar mensagens com o conector Slack. Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* A sua conta Trello e credenciais de utilizador

  As suas credenciais autorizam a sua aplicação lógica para criar uma ligação e aceder à sua conta Trello.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta Trello. Para começar com um gatilho Trello, [crie uma aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para utilizar uma ação Trello, inicie a sua aplicação lógica com um gatilho, por exemplo, o gatilho **recurrence.**

## <a name="connect-to-trello"></a>Ligue-se a Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Para aplicações lógicas em branco, na caixa de pesquisa, introduza "trello" como filtro. Na lista de gatilhos, selecione o gatilho que deseja. 

   -ou-

   Para aplicações lógicas existentes, sob o último passo em que pretende adicionar uma ação, escolha **novo passo**. 
   Na caixa de pesquisa, introduza "trello" como filtro. 
   Na lista de ações, selecione a ação que deseja.

   Para adicionar uma ação entre passos, mova o ponteiro sobre a seta entre os degraus. 
   Escolha o sinal**+** de mais () que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Se for solicitado que inscreva-se na Trello, autorize o acesso à sua aplicação lógica e faça o seu insto.

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionados e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição OpenAPI (ex-Swagger) do conector, reveja a página de [referência](/connectors/trello/)do conector .

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)