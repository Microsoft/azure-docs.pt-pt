---
title: Ligar ao Twilio a partir do Azure Logic Apps | Documentos da Microsoft
description: Automatizar tarefas e fluxos de trabalho que gerir mensagens SMS, MMS e IP globais através da sua conta do Twilio com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: fab52236c701f10c8e8e23ac398362ca4583ea06
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104906"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Gerir mensagens no Twilio com o Azure Logic Apps

Com o Azure Logic Apps e o conector do Twilio, pode criar tarefas automatizadas e fluxos de trabalho que obterem, enviarem e listam mensagens no Twilio, que incluem mensagens SMS, MMS e IP globais. Pode utilizar estas ações para executar tarefas com a sua conta do Twilio. Pode também ter outras ações que utilize a saída do Twilio ações. Por exemplo, quando uma nova mensagem chega, pode enviar a mensagem de conteúdo com o conector do Slack. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* Partir [Twilio](https://www.twilio.com/): 

  * O ID da conta Twilio e [token de autenticação](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), que pode ser encontrado no seu dashboard do Twilio

    As suas credenciais autorizar a aplicação lógica para criar uma ligação e aceder à sua conta do Twilio da sua aplicação lógica. 
    Se estiver a utilizar uma conta de avaliação do Twilio, pode enviar apenas ao SMS *verificado* números de telefone.

  * Um número de telefone Twilio verificado que pode enviar uma SMS

  * Um número de telefone Twilio verificado que pode receber a SMS

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação de lógica onde pretende aceder à sua conta do Twilio. Para utilizar uma ação do Twilio, inicie a aplicação lógica com outro acionador, por exemplo, o **periodicidade** acionador.

## <a name="connect-to-twilio"></a>Ligue ao Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Escolha um caminho: 

     * Na última etapa em que pretende adicionar uma ação, escolha **novo passo**. 

       -ou-

     * Entre os passos em que pretende adicionar uma ação, mova o ponteiro do mouse sobre a seta entre passos. 
     Selecione o sinal de adição (**+**) que é apresentada e, em seguida, selecione **adicionar uma ação**.
     
       Na caixa de pesquisa, introduza "twilio" como o filtro. 
       Abaixo da lista de ações, selecione a ação que pretende.

1. Forneça os detalhes necessários para a sua ligação e, em seguida, escolha **criar**:

   * O nome a utilizar para a sua ligação
   * O ID da conta Twilio 
   * O token de acesso (autenticação) do Twilio

1. Forneça os detalhes necessários para a ação selecionada e continuar a criar o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](/connectors/twilio/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)