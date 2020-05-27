---
title: Ligue-se a Yammer a partir de Aplicações Lógicas Azure
description: Automatizar tarefas e fluxos de trabalho que monitorizam, publicam e gerem mensagens, feeds e muito mais em Yammer usando apps lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 3023aa72d713dc25351a6e509319e1c4c0ed609a
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829669"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Monitorize e gerencie a sua conta Yammer utilizando aplicações da Lógica Azure

Com as Aplicações Lógicas Azure e o conector Yammer, pode criar tarefas automatizadas e fluxos de trabalho que monitorizam e gerem mensagens, feeds e muito mais na sua conta Yammer, juntamente com outras ações, por exemplo:

* Monitorize quando novas mensagens aparecerem em feeds e grupos seguidos.
* Obtenha mensagens, grupos, redes, detalhes dos utilizadores e muito mais.
* Post e como mensagens.

Pode utilizar gatilhos que obtêm respostas da sua conta Yammer e disponibilizar a saída a outras ações. Pode usar ações que executam tarefas com a sua conta Yammer. Também pode ter outras ações que usem a saída das ações da Yammer. Por exemplo, quando novas mensagens aparecem em feeds ou grupos, pode partilhar essas mensagens com o conector Slack. Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* A sua conta Yammer e credenciais de utilizador

   As suas credenciais autorizam a sua aplicação lógica para criar uma ligação e aceder à sua conta Yammer.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta Yammer. Para começar com um gatilho Yammer, [crie uma aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para utilizar uma ação Yammer, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **recurrence.**

## <a name="connect-to-yammer"></a>Ligue-se a Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Escolha um caminho: 

   * Para aplicações lógicas em branco, na caixa de pesquisa, introduza "yammer" como filtro. 
   Na lista de gatilhos, selecione o gatilho que deseja. 

     -ou-

   * Para aplicações lógicas existentes: 
   
     * Sob o último passo em que pretende adicionar uma ação, escolha **novo passo**. 

       -ou-

     * Entre os degraus onde pretende adicionar uma ação, mova o ponteiro sobre a seta entre os degraus. 
     Escolha o sinal de mais **+** () que aparece e, em seguida, selecione **Adicionar uma ação**.
     
       Na caixa de pesquisa, introduza "yammer" como filtro. 
       Na lista de ações, selecione a ação que deseja.

1. Se for solicitado que inscreva-se no Yammer, inscreva-se agora para que possa ter acesso.

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionados e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição OpenAPI (ex-Swagger) do conector, reveja a página de [referência](/connectors/yammer/)do conector .

## <a name="get-support"></a>Obter suporte

* Para perguntas, visite o [Microsoft Q&Uma página de perguntas para aplicações lógicas do Azure](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)