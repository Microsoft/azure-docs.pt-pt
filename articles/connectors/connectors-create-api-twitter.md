---
title: Ligar ao Twitter a partir do Azure Logic Apps | Documentos da Microsoft
description: Automatizar tarefas e fluxos de trabalho que monitorizarem e gerir os tweets, além de obtém dados sobre seguidores, os utilizadores seguidos, outros utilizadores, linhas cronológicas e mais a partir da sua conta do Twitter com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 0fbd89202796cb4543dbecbeee605c9b87cc9d05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104991"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Monitorizar e gerir o Twitter através do Azure Logic Apps

Com o Azure Logic Apps e o conector do Twitter, pode criar tarefas automatizadas e fluxos de trabalho que monitorizarem e gerir os dados que mais lhe interessa no Twitter como publicar tweets, seguidores, os utilizadores e seguido de utilizadores, as linhas do tempo e muito mais, juntamente com outras ações, por exemplo:

* Monitorizar, publicar e procure tweets.
* Obter dados, tais como seguidores utilizadores seguidos, linhas cronológicas e muito mais.

Pode usar acionadores que obtém as respostas da sua conta do Twitter e disponibilizar a saída para outras ações. Pode utilizar as ações que executam tarefas com a sua conta do Twitter. Pode também ter outras ações utilizar a saída de ações do Twitter. Por exemplo, quando for apresentado um novo tweet com uma hashtag específica, pode enviar mensagens com o conector do Slack. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* As credenciais de utilizador e conta do Twitter

   As suas credenciais autorizar a aplicação lógica para criar uma ligação e aceder à sua conta do Twitter.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação de lógica onde pretende aceder à sua conta do Twitter. Para começar com um acionador do Twitter [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação do Twitter, inicie a aplicação lógica com outro acionador, por exemplo, o **periodicidade** acionador.

## <a name="connect-to-twitter"></a>Ligar ao Twitter

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Escolha um caminho: 

   * Para aplicações de lógica em branco, na caixa de pesquisa, introduza "twitter" como o filtro. 
   Abaixo da lista de disparadores, selecione o acionador que pretende. 

     -ou-

   * Para o logic apps existentes: 
   
     * Na última etapa em que pretende adicionar uma ação, escolha **novo passo**. 

       -ou-

     * Entre os passos em que pretende adicionar uma ação, mova o ponteiro do mouse sobre a seta entre passos. 
     Selecione o sinal de adição ( **+** ) que é apresentada e, em seguida, selecione **adicionar uma ação**.
     
       Na caixa de pesquisa, introduza "twitter" como o filtro. 
       Abaixo da lista de ações, selecione a ação que pretende.

1. Se lhe for pedido para iniciar sessão no Twitter, inicie sessão agora para autorizar o acesso para a aplicação lógica.

1. Forneça os detalhes necessários para o seu acionador selecionado ou a ação e continuar a criar o fluxo de trabalho da sua aplicação lógica.

## <a name="examples"></a>Exemplos

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Acionador do twitter: Quando um novo tweet é publicado

Este acionador é iniciado um fluxo de trabalho de aplicação lógica quando o acionador detetar um novo tweet, por exemplo, com o hashtag, #Seattle. Por exemplo, quando são encontrados esses tweets, pode adicionar um ficheiro com o conteúdo dos tweets no armazenamento, como uma conta do Dropbox ao utilizar o conector da Dropbox. 

Opcionalmente, pode incluir uma condição que tweets elegíveis deverão ser provenientes de utilizadores com, pelo menos, um número especificado de seguidores.

**Exemplo de Enterprise**: Pode utilizar este acionador para monitorizar tweets sobre a sua empresa e carregar o conteúdo dos tweets numa base de dados SQL.

### <a name="twitter-action-post-a-tweet"></a>Ação de twitter: Publicar um tweet

Esta ação publica um tweet, mas pode configurar a ação para que o tweet contém o conteúdo de tweets encontrados pelo acionador descrito anteriormente. 

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](/connectors/twitterconnector/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
