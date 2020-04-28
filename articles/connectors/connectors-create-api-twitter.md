---
title: Ligue-se ao Twitter a partir de Aplicações Lógicas Azure
description: Automatizar tarefas e fluxos de trabalho que monitorizam e gerem tweets, além de obter dados sobre seguidores, utilizadores seguidos, outros utilizadores, linhas de tempo e muito mais da sua conta de Twitter usando Apps Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 8ffd0fd558cf759fadd912de9dff4acf49d9659f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789091"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Monitorize e gerencie o Twitter usando aplicações lógicas azure

Com as Aplicações Lógicas Azure e o conector do Twitter, pode criar tarefas automatizadas e fluxos de trabalho que monitorizam e gerem dados com os que se preocupam no Twitter, como tweets, seguidores, utilizadores e utilizadores seguidos, linhas de tempo e muito mais, juntamente com outras ações, por exemplo:

* Monitor, post e tweets de pesquisa.
* Obtenha dados como seguidores, utilizadores seguidos, linhas de tempo e muito mais.

Pode utilizar gatilhos que obtêm respostas na sua conta do Twitter e disponibilizar a produção a outras ações. Pode utilizar ações que executem tarefas com a sua conta de Twitter. Também pode ter outras ações a utilizar a saída das ações do Twitter. Por exemplo, quando aparece um novo tweet com uma hashtag específica, pode enviar mensagens com o conector Slack. Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* A sua conta de Twitter e credenciais de utilizador

   As suas credenciais autorizam a sua aplicação lógica para criar uma ligação e aceder à sua conta de Twitter.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta de Twitter. Para começar com um gatilho do Twitter, [crie uma aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para utilizar uma ação do Twitter, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **recurrence.**

## <a name="connect-to-twitter"></a>Ligar ao Twitter

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Escolha um caminho: 

   * Para aplicações lógicas em branco, na caixa de pesquisa, introduza o "twitter" como filtro. 
   Na lista de gatilhos, selecione o gatilho que deseja. 

     -ou-

   * Para aplicações lógicas existentes: 
   
     * Sob o último passo em que pretende adicionar uma ação, escolha **novo passo**. 

       -ou-

     * Entre os degraus onde pretende adicionar uma ação, mova o ponteiro sobre a seta entre os degraus. 
     Escolha o sinal**+** de mais () que aparece e, em seguida, selecione **Adicionar uma ação**.
     
       Na caixa de pesquisa, introduza "twitter" como filtro. 
       Na lista de ações, selecione a ação que deseja.

1. Se for solicitado a iniciar sessão no Twitter, inscreva-se agora para que possa autorizar o acesso à sua aplicação lógica.

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionados e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="examples"></a>Exemplos

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Twitter trigger: Quando um novo tweet é publicado

Este gatilho inicia um fluxo de trabalho de aplicação lógica quando o gatilho deteta um novo tweet, por exemplo, com a hashtag, #Seattle. Assim, por exemplo, quando estes tweets são encontrados, pode adicionar um ficheiro com o conteúdo dos tweets ao armazenamento, como uma conta Dropbox utilizando o conector Dropbox. 

Opcionalmente, pode incluir uma condição de que os tweets elegíveis devem vir de utilizadores com pelo menos um número especificado de seguidores.

**Exemplo da empresa:** Pode utilizar este gatilho para monitorizar tweets sobre a sua empresa e enviar o conteúdo dos tweets para uma base de dados SQL.

### <a name="twitter-action-post-a-tweet"></a>Ação do Twitter: Publique um tweet

Esta ação publica um tweet, mas pode configurar a ação para que o tweet contenha o conteúdo de tweets encontrados pelo gatilho previamente descrito. 

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição OpenAPI (ex-Swagger) do conector, reveja a página de [referência](/connectors/twitterconnector/)do conector .

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
