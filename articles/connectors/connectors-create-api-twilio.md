---
title: Conectar-se ao twilio de aplicativos lógicos do Azure
description: Automatizar tarefas e fluxos de trabalho que gerenciam mensagens SMS, MMS e IP globais por meio de sua conta do twilio usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: e5b218efd9c8cfaad99d76d8118d181390a977c3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789108"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Gerenciar mensagens no twilio com os aplicativos lógicos do Azure

Com os aplicativos lógicos do Azure e o conector do twilio, você pode criar tarefas e fluxos de trabalho automatizados que recebem, enviam e listam mensagens no twilio, que incluem mensagens de SMS, MMS e IP globais. Você pode usar essas ações para executar tarefas com sua conta do twilio. Você também pode fazer com que outras ações usem a saída de ações twilio. Por exemplo, quando uma nova mensagem chega, você pode enviar o conteúdo da mensagem com o conector de margem de atraso. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* De [twilio](https://www.twilio.com/): 

  * A ID da conta do twilio e o [token de autenticação](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), que podem ser encontrados no painel do twilio

    Suas credenciais autorizam seu aplicativo lógico a criar uma conexão e acessar sua conta do twilio de seu aplicativo lógico. 
    Se você estiver usando uma conta de avaliação do twilio, poderá enviar SMS somente para números de telefone *verificados* .

  * Um número de telefone twilio verificado que pode enviar SMS

  * Um número de telefone twilio verificado que pode receber SMS

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta do twilio. Para usar uma ação twilio, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho **recorrência** .

## <a name="connect-to-twilio"></a>Conectar-se ao twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Escolha um caminho: 

     * Na última etapa em que você deseja adicionar uma ação, escolha **nova etapa**. 

       -ou-

     * Entre as etapas em que você deseja adicionar uma ação, mova o ponteiro sobre a seta entre as etapas. 
     Escolha o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.
     
       Na caixa de pesquisa, digite "twilio" como filtro. 
       Na lista ações, selecione a ação desejada.

1. Forneça os detalhes necessários para sua conexão e, em seguida, escolha **criar**:

   * O nome a ser usado para a conexão
   * Sua ID da conta do twilio 
   * Seu token de acesso do twilio (autenticação)

1. Forneça os detalhes necessários para a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](/connectors/twilio/)do conector.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)