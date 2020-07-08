---
title: Ligue-se ao Twilio a partir de Azure Logic Apps
description: Automatizar tarefas e fluxos de trabalho que gerem mensagens globais de SMS, MMS e IP através da sua conta Twilio utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: bb7be1815364b3def9b22e50454dceabd17e6150
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83829652"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Gerir mensagens em Twilio com Azure Logic Apps

Com a Azure Logic Apps e o conector Twilio, pode criar tarefas automatizadas e fluxos de trabalho que obtenham, enviem e listem mensagens em Twilio, que incluem mensagens de SMS, MMS e IP globais. Pode utilizar estas ações para executar tarefas com a sua conta Twilio. Você também pode ter outras ações usar a saída das ações de Twilio. Por exemplo, quando uma nova mensagem chega, pode enviar o conteúdo da mensagem com o conector Slack. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* De [Twilio:](https://www.twilio.com/) 

  * O seu ID de conta Twilio e [ficha de autenticação,](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them)que pode encontrar no seu painel Twilio

    As suas credenciais autorizam a sua aplicação lógica a criar uma ligação e aceder à sua conta Twilio a partir da sua aplicação lógica. 
    Se estiver a utilizar uma conta de teste Twilio, pode enviar SMS apenas para *números* de telefone verificados.

  * Um número de telefone Twilio verificado que pode enviar SMS

  * Um número de telefone Twilio verificado que pode receber SMS

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta Twilio. Para utilizar uma ação Twilio, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **de Recorrência.**

## <a name="connect-to-twilio"></a>Ligue-se a Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se não abrir já.

1. Escolha um caminho: 

     * Sob o último passo onde pretende adicionar uma ação, escolha **Novo passo**. 

       -ou-

     * Entre os passos onde pretende adicionar uma ação, mova o ponteiro sobre a seta entre os degraus. 
     Escolha o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.
     
       Na caixa de pesquisa, introduza "twilio" como filtro. 
       Na lista de ações, selecione a ação desejada.

1. Forneça os detalhes necessários para a sua ligação e, em seguida, escolha **Criar**:

   * O nome a usar para a sua ligação
   * A sua conta Twilio ID 
   * O seu sinal de acesso twilio (autenticação)

1. Forneça os detalhes necessários para a sua ação selecionada e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição openAPI (anteriormente Swagger) do conector, consulte a página de [referência](/connectors/twilio/)do conector .

## <a name="get-support"></a>Obter suporte

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)