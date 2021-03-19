---
title: Conecte-se ao SMTP a partir de apps Azure Logic
description: Automatizar tarefas e fluxos de trabalho que enviam e-mail através da sua conta SMTP (Simple Mail Transfer Protocol) utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 9989d0ebd95bfe5ee49be2ba76b73e07630b519a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87283950"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Envie e-mail da sua conta SMTP com Azure Logic Apps

Com aplicações lógicas Azure e o conector Simple Mail Transfer Protocol (SMTP), pode criar tarefas automatizadas e fluxos de trabalho que enviam e-mail a partir da sua conta SMTP. Também pode ter outras ações que utilizem a saída das ações SMTP. Por exemplo, depois de o seu SMTP enviar um e-mail, pode notificar a sua equipa em Slack com o conector Slack. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* A sua conta SMTP e as credenciais de utilizador

  As suas credenciais autorizam a sua aplicação lógica a criar uma ligação e aceder à sua conta SMTP.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta SMTP. Para utilizar uma ação SMTP, inicie a sua aplicação lógica com um gatilho, como um gatilho Salesforce, se tiver uma conta Salesforce.

  Por exemplo, pode iniciar a sua aplicação lógica com o gatilho De vendas quando **um disco é criado.** 
  Este gatilho dispara cada vez que um novo recorde, como um chumbo, é criado na Salesforce. 
  Em seguida, pode seguir este gatilho com a ação SMTP **Enviar e-mail.** Assim, quando o novo registo é criado, a sua aplicação lógica envia um e-mail da sua conta SMTP sobre o novo registo.

## <a name="connect-to-smtp"></a>Ligar ao SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se não abrir já.

1. Sob o último passo onde pretende adicionar uma ação SMTP, escolha **Novo passo**. 

   Para adicionar uma ação entre os degraus, mova o ponteiro sobre a seta entre os degraus. 
   Escolha o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Na caixa de pesquisa, introduza "smtp" como filtro. Na lista de ações, selecione a ação desejada.

1. Quando solicitado, forneça esta informação de ligação:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome de conexão** | Yes | Um nome para a ligação ao seu servidor SMTP | 
   | **Endereço do servidor SMTP** | Yes | O endereço do seu servidor SMTP | 
   | **Nome de Utilizador** | Yes | O seu nome de utilizador para a sua conta SMTP | 
   | **Palavra-passe** | Yes | A sua senha para a sua conta SMTP | 
   | **Porta de servidor SMTP** | No | Uma porta específica no seu servidor SMTP que pretende utilizar | 
   | **Ativar o SSL?** | No | Ligue ou desligue a encriptação TLS/SSL. | 
   |||| 

1. Forneça os detalhes necessários para a sua ação selecionada. 

1. Guarde a sua aplicação lógica ou continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como gatilhos, ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/smtpconnector/).

> [!NOTE]
> Para aplicações lógicas num ambiente de [serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão com rótulo ISE deste conector utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
