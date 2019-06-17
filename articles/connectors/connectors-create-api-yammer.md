---
title: Ligue ao Yammer do Azure Logic Apps | Documentos da Microsoft
description: Automatizar tarefas e fluxos de trabalho que monitorizar, publicam e gerem as mensagens, feeds e muito mais no Yammer com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: ca2d28f3438fd166fa282488206662c95777bf3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104736"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Monitorizar e gerir a sua conta do Yammer com o Azure Logic Apps

Com o Azure Logic Apps e o conector do Yammer, pode criar tarefas automatizadas e fluxos de trabalho que monitorar e gerenciar mensagens, feeds e muito mais na sua conta do Yammer, juntamente com outras ações, por exemplo:

* Monitor quando novas mensagens apresentadas em feeds seguidos e grupos.
* Obter mensagens, grupos, redes, detalhes dos utilizadores e muito mais.
* Publicar e mensagens do tipo.

Pode usar acionadores que obtém as respostas da sua conta do Yammer e disponibilizar a saída para outras ações. Pode utilizar as ações que executam tarefas com a sua conta do Yammer. Pode também ter outras ações utilizar a saída de ações do Yammer. Por exemplo, quando novas mensagens apresentadas no feeds ou grupos, pode compartilhar essas mensagens com o conector do Slack. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* As credenciais de conta e do utilizador do Yammer

   As suas credenciais autorizar a aplicação lógica para criar uma ligação e aceder à sua conta do Yammer.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação de lógica onde pretende aceder à sua conta do Yammer. Para começar com um acionador de Yammer [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação do Yammer, inicie a aplicação lógica com outro acionador, por exemplo, o **periodicidade** acionador.

## <a name="connect-to-yammer"></a>Ligue ao Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Escolha um caminho: 

   * Para aplicações de lógica em branco, na caixa de pesquisa, introduza "yammer" como o filtro. 
   Abaixo da lista de disparadores, selecione o acionador que pretende. 

     -ou-

   * Para o logic apps existentes: 
   
     * Na última etapa em que pretende adicionar uma ação, escolha **novo passo**. 

       -ou-

     * Entre os passos em que pretende adicionar uma ação, mova o ponteiro do mouse sobre a seta entre passos. 
     Selecione o sinal de adição ( **+** ) que é apresentada e, em seguida, selecione **adicionar uma ação**.
     
       Na caixa de pesquisa, introduza "yammer" como o filtro. 
       Abaixo da lista de ações, selecione a ação que pretende.

1. Se lhe for pedido para iniciar sessão no Yammer, inicie sessão no início de sessão agora agora para que pode permitir o acesso.

1. Forneça os detalhes necessários para o seu acionador selecionado ou a ação e continuar a criar o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](/connectors/yammer/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)