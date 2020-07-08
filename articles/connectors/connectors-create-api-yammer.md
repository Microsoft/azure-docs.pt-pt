---
title: Conecte-se a Yammer a partir de Azure Logic Apps
description: Automatizar tarefas e fluxos de trabalho que monitorizem, publiquem e gerem mensagens, feeds e muito mais em Yammer utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 3023aa72d713dc25351a6e509319e1c4c0ed609a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83829669"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Monitorize e gere a sua conta Yammer utilizando apps Azure Logic

Com a Azure Logic Apps e o conector Yammer, pode criar tarefas e fluxos de trabalho automatizados que monitorizam e gerem mensagens, feeds e muito mais na sua conta Yammer, juntamente com outras ações, por exemplo:

* Monitorize quando novas mensagens aparecerem em feeds e grupos seguidos.
* Receba mensagens, grupos, redes, detalhes dos utilizadores e muito mais.
* Post e como mensagens.

Pode utilizar gatilhos que obtenham respostas da sua conta Yammer e disponibilize a saída para outras ações. Pode utilizar ações que executem tarefas com a sua conta Yammer. Você também pode ter outras ações usar a saída das ações da Yammer. Por exemplo, quando novas mensagens aparecem em feeds ou grupos, pode partilhar essas mensagens com o conector Slack. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* A sua conta Yammer e credenciais de utilizador

   As suas credenciais autorizam a sua aplicação lógica a criar uma ligação e aceder à sua conta Yammer.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta Yammer. Para começar com um gatilho Yammer, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação Yammer, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **de Recorrência.**

## <a name="connect-to-yammer"></a>Conecte-se a Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se não abrir já.

1. Escolha um caminho: 

   * Para aplicações lógicas em branco, na caixa de pesquisa, insira "yammer" como filtro. 
   Na lista de gatilhos, selecione o gatilho que pretende. 

     -ou-

   * Para aplicações lógicas existentes: 
   
     * Sob o último passo onde pretende adicionar uma ação, escolha **Novo passo**. 

       -ou-

     * Entre os passos onde pretende adicionar uma ação, mova o ponteiro sobre a seta entre os degraus. 
     Escolha o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.
     
       Na caixa de pesquisa, introduza "yammer" como filtro. 
       Na lista de ações, selecione a ação desejada.

1. Se lhe for pedido que assine o contrato com o Yammer, inscreva-se agora para que possa permitir o acesso.

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionado e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição openAPI (anteriormente Swagger) do conector, consulte a página de [referência](/connectors/yammer/)do conector .

## <a name="get-support"></a>Obter suporte

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)