---
title: Conecte-se ao Trello a partir de apps Azure Logic
description: Automatizar tarefas e fluxos de trabalho que monitorizam e gerem listas, placas e cartões nos seus projetos Trello utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: d1426bbe2064067b26f53a8f8e5d4aa842ede8c0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83829703"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Monitorize e gere a Trello com apps Azure Logic

Com a Azure Logic Apps e o conector Trello, pode criar tarefas e fluxos de trabalho automatizados que monitorizam e gerem as suas listas de Trello, cartões, placas, membros da equipa, e assim por diante, por exemplo:

* Monitorize quando os novos cartões forem adicionados aos quadros e listas. 
* Criar, obter e gerir placas, cartões e listas.
* Adicione comentários e membros aos cartões.
* Placas de lista, etiquetas de placa, cartões nos quadros, comentários de cartão, membros do cartão, membros da equipa e equipas onde você é membro. 
* Arranja equipas.

Pode utilizar gatilhos que obtenham respostas da sua conta Trello e disponibilize a saída para outras ações. Pode utilizar ações que executem tarefas com a sua conta Trello. Também pode ter outras ações que utilizem a saída das ações da Trello. Por exemplo, quando um novo cartão é adicionado à placa ou lista, pode enviar mensagens com o conector Slack. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* A sua conta Trello e as credenciais de utilizador

  As suas credenciais autorizam a sua aplicação lógica a criar uma ligação e aceder à sua conta Trello.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta Trello. Para começar com um gatilho Trello, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação Trello, inicie a sua aplicação lógica com um gatilho, por exemplo, o gatilho **de Recorrência.**

## <a name="connect-to-trello"></a>Ligar a Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se não abrir já.

1. Para aplicações lógicas em branco, na caixa de pesquisa, introduza "trello" como filtro. Na lista de gatilhos, selecione o gatilho que pretende. 

   -ou-

   Para aplicações lógicas existentes, sob o último passo onde pretende adicionar uma ação, escolha **Novo passo**. 
   Na caixa de pesquisa, introduza "trello" como filtro. 
   Na lista de ações, selecione a ação desejada.

   Para adicionar uma ação entre os degraus, mova o ponteiro sobre a seta entre os degraus. 
   Escolha o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Se for solicitado a iniciar seducação na Trello, autorize o acesso à sua aplicação lógica e inscreva-se.

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionado e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição openAPI (anteriormente Swagger) do conector, consulte a página de [referência](/connectors/trello/)do conector .

## <a name="get-support"></a>Obter suporte

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)