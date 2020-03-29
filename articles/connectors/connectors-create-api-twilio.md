---
title: Ligue-se a Twilio a partir de Aplicações Lógicas Azure
description: Automatizar tarefas e fluxos de trabalho que gerem mensagens globais de SMS, MMS e IP através da sua conta Twilio utilizando aplicações lógicas azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: e5b218efd9c8cfaad99d76d8118d181390a977c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789108"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Gerir mensagens em Twilio com aplicações lógicas azure

Com as Aplicações Lógicas Azure e o conector Twilio, pode criar tarefas automatizadas e fluxos de trabalho que recebem, enviam e listam mensagens em Twilio, que incluem mensagens globais de SMS, MMS e IP. Pode utilizar estas ações para executar tarefas com a sua conta Twilio. Também pode ter outras ações a utilizar a saída das ações do Twilio. Por exemplo, quando uma nova mensagem chegar, pode enviar o conteúdo da mensagem com o conector Slack. Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* De [Twilio:](https://www.twilio.com/) 

  * O id da sua conta Twilio e ficha de [autenticação,](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them)que pode encontrar no seu painel twilio

    As suas credenciais autorizam a sua aplicação lógica para criar uma ligação e aceder à sua conta Twilio a partir da sua aplicação lógica. 
    Se estiver a usar uma conta de teste twilio, pode enviar SMS apenas para números de telefone *verificados.*

  * Um número de telefone twilio verificado que pode enviar SMS

  * Um número de telefone twilio verificado que pode receber SMS

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta Twilio. Para utilizar uma ação Twilio, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **recurrence.**

## <a name="connect-to-twilio"></a>Ligue-se a Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Escolha um caminho: 

     * Sob o último passo em que pretende adicionar uma ação, escolha **novo passo**. 

       -ou-

     * Entre os degraus onde pretende adicionar uma ação, mova o ponteiro sobre a seta entre os degraus. 
     Escolha o sinal**+** de mais () que aparece e, em seguida, selecione **Adicionar uma ação**.
     
       Na caixa de pesquisa, introduza "twilio" como filtro. 
       Na lista de ações, selecione a ação que deseja.

1. Forneça os detalhes necessários para a sua ligação e, em seguida, escolha **Criar:**

   * O nome a usar para a sua ligação
   * O seu ID da conta Twilio 
   * O seu sinal de acesso Twilio (autenticação)

1. Forneça os detalhes necessários para a sua ação selecionada e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição OpenAPI (ex-Swagger) do conector, reveja a página de [referência](/connectors/twilio/)do conector .

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)