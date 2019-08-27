---
title: Conectar-se ao Twitter por meio do aplicativo lógico do Azure | Microsoft Docs
description: Automatize tarefas e fluxos de trabalho que monitoram e gerenciem tweets, além de obter dados sobre seguidores, seus usuários seguidos, outros usuários, cronogramas e muito mais em sua conta do Twitter usando aplicativos lógicos do Azure
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
ms.openlocfilehash: 22b75b2d2b47dbd496eda06c09b418ae66f6403a
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050700"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Monitorar e gerenciar o Twitter usando os aplicativos lógicos do Azure

Com os aplicativos lógicos do Azure e o conector do Twitter, você pode criar tarefas e fluxos de trabalho automatizados que monitoram e gerenciam dados importantes para o Twitter, como tweets, seguidores, usuários e usuários seguidos, cronogramas e muito mais, juntamente com outras ações, por exemplo:

* Monitore, poste e pesquise tweets.
* Obter dados como seguidores, usuários seguidos, linhas do tempo e muito mais.

Você pode usar gatilhos que obtêm respostas de sua conta do Twitter e disponibilizam a saída para outras ações. Você pode usar ações que executam tarefas com sua conta do Twitter. Você também pode fazer com que outras ações usem a saída de ações do Twitter. Por exemplo, quando um novo tweet com uma hashtag específica é exibido, você pode enviar mensagens com o conector de margem de atraso. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Sua conta do Twitter e as credenciais do usuário

   Suas credenciais autorizam seu aplicativo lógico a criar uma conexão e acessar sua conta do Twitter.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta do Twitter. Para começar com um gatilho do Twitter, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação do Twitter, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho de recorrência.

## <a name="connect-to-twitter"></a>Ligar ao Twitter

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Escolha um caminho: 

   * Para aplicativos lógicos em branco, na caixa de pesquisa, digite "Twitter" como filtro. 
   Na lista de gatilhos, selecione o gatilho desejado. 

     -ou-

   * Para aplicativos lógicos existentes: 
   
     * Na última etapa em que você deseja adicionar uma ação, escolha **nova etapa**. 

       -ou-

     * Entre as etapas em que você deseja adicionar uma ação, mova o ponteiro sobre a seta entre as etapas. 
     Escolha o sinal de adição **+** () que aparece e, em seguida, selecione **Adicionar uma ação**.
     
       Na caixa de pesquisa, insira "Twitter" como seu filtro. 
       Na lista ações, selecione a ação desejada.

1. Se você for solicitado a entrar no Twitter, entre agora para que possa autorizar o acesso para seu aplicativo lógico.

1. Forneça os detalhes necessários para o gatilho ou ação selecionado e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="examples"></a>Exemplos

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Gatilho do Twitter: Quando um novo tweet é Postado

Esse gatilho inicia um fluxo de trabalho do aplicativo lógico quando o gatilho detecta um novo tweet, por exemplo, com a hashtag, #Seattle. Por exemplo, quando esses Tweets são encontrados, você pode adicionar um arquivo com o conteúdo do tweets ao armazenamento, como uma conta do Dropbox usando o conector do dropbox. 

Opcionalmente, você pode incluir uma condição que os tweets qualificados devem vir de usuários com pelo menos um número especificado de seguidores.

**Exemplo da empresa**: Você pode usar esse gatilho para monitorar tweets sobre sua empresa e carregar o conteúdo dos tweets em um banco de dados SQL.

### <a name="twitter-action-post-a-tweet"></a>Ação do Twitter: Publicar um tweet

Essa ação posta um tweet, mas você pode configurar a ação para que o tweet contenha o conteúdo de tweets encontrado pelo gatilho descrito anteriormente. 

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](/connectors/twitterconnector/)do conector.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outros conectores de [aplicativos lógicos](../connectors/apis-list.md)
