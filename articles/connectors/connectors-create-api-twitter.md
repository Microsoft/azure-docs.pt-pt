---
title: Conecte-se ao Twitter a partir de Azure Logic Apps
description: Automatizar tarefas e fluxos de trabalho que monitorizam e gerem tweets, além de obter dados sobre seguidores, utilizadores seguidos, outros utilizadores, linhas de tempo e muito mais da sua conta de Twitter através de Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: f2db6d614c3c12cb1be87724e79d79a16769d6b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83829601"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Monitorizar e gerir o Twitter com o Azure Logic Apps

Com a Azure Logic Apps e o conector do Twitter, pode criar tarefas automatizadas e fluxos de trabalho que monitorizem e gerem dados que se preocupam no Twitter, como tweets, seguidores, utilizadores e utilizadores seguidos, linhas de tempo e muito mais, juntamente com outras ações, por exemplo:

* Monitor, post e tweets de pesquisa.
* Obtenha dados como seguidores, utilizadores seguidos, prazos e muito mais.

Pode utilizar gatilhos que obtenham respostas da sua conta de Twitter e disponibilize a saída para outras ações. Pode utilizar ações que executem tarefas com a sua conta de Twitter. Também pode ter outras ações que utilizem a saída das ações do Twitter. Por exemplo, quando um novo tweet com uma hashtag específica aparece, pode enviar mensagens com o conector Slack. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* A sua conta de Twitter e credenciais de utilizador

   As suas credenciais autorizam a sua aplicação lógica a criar uma ligação e aceder à sua conta de Twitter.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta de Twitter. Para começar com um gatilho do Twitter, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação no Twitter, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **de Recorrência.**

## <a name="connect-to-twitter"></a>Ligar ao Twitter

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se não abrir já.

1. Escolha um caminho: 

   * Para aplicações lógicas em branco, na caixa de pesquisa, insira o "twitter" como filtro. 
   Na lista de gatilhos, selecione o gatilho que pretende. 

     -ou-

   * Para aplicações lógicas existentes: 
   
     * Sob o último passo onde pretende adicionar uma ação, escolha **Novo passo**. 

       -ou-

     * Entre os passos onde pretende adicionar uma ação, mova o ponteiro sobre a seta entre os degraus. 
     Escolha o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.
     
       Na caixa de pesquisa, insira o "twitter" como filtro. 
       Na lista de ações, selecione a ação desejada.

1. Se for solicitado a iniciar sedutar no Twitter, inscreva-se agora para que possa autorizar o acesso à sua aplicação lógica.

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionado e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="examples"></a>Exemplos

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Trigger do Twitter: Quando um novo tweet é publicado

Este gatilho inicia um fluxo de trabalho de aplicações lógicas quando o gatilho deteta um novo tweet, por exemplo, com a hashtag, #Seattle. Assim, por exemplo, quando estes tweets são encontrados, pode adicionar um ficheiro com o conteúdo dos tweets ao armazenamento, como uma conta Dropbox utilizando o conector Dropbox. 

Opcionalmente, pode incluir uma condição de que os tweets elegíveis devem vir de utilizadores com pelo menos um número especificado de seguidores.

**Exemplo da empresa**: Pode utilizar este gatilho para monitorizar tweets sobre a sua empresa e fazer o upload do conteúdo dos tweets para uma base de dados SQL.

### <a name="twitter-action-post-a-tweet"></a>Twitter action: Post a tweet

Esta ação publica um tweet, mas pode configurar a ação para que o tweet contenha o conteúdo dos tweets encontrados pelo gatilho previamente descrito. 

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição openAPI (anteriormente Swagger) do conector, consulte a página de [referência](/connectors/twitterconnector/)do conector .

## <a name="get-support"></a>Obter suporte

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
