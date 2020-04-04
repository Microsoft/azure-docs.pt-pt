---
title: Ligue-se ao SMTP a partir de Aplicações Lógicas Azure
description: Automatizar tarefas e fluxos de trabalho que enviam e-mail através da sua conta SMTP (Simple Mail Transfer Protocol) utilizando aplicações da Lógica Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 1cfc53dcd730262101c0e879e0419ba3f2db4d38
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656990"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Envie e-mail da sua conta SMTP com Aplicações Lógicas Azure

Com aplicativos azure logic e o conector Simple Mail Transfer Protocol (SMTP), pode criar tarefas e fluxos de trabalho automatizados que enviam e-mail da sua conta SMTP. Também pode ter outras ações a utilizar a saída a partir de ações SMTP. Por exemplo, depois de o seu SMTP enviar um e-mail, pode notificar a sua equipa em Slack com o conector Slack. Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* A sua conta SMTP e credenciais de utilizador

  As suas credenciais autorizam a sua aplicação lógica para criar uma ligação e aceder à sua conta SMTP.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta SMTP. Para utilizar uma ação SMTP, inicie a sua aplicação lógica com um gatilho, como um gatilho Salesforce, se tiver uma conta Salesforce.

  Por exemplo, pode iniciar a sua aplicação lógica com o gatilho **When a record is created** Salesforce. 
  Este gatilho dispara cada vez que um novo recorde, como um chumbo, é criado na Salesforce. 
  Pode então seguir este gatilho com a ação SMTP **Enviar e-mail.** Desta forma, quando o novo disco é criado, a sua aplicação lógica envia um e-mail da sua conta SMTP sobre o novo disco.

## <a name="connect-to-smtp"></a>Ligar ao SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Sob o último passo em que pretende adicionar uma ação SMTP, escolha **novo passo**. 

   Para adicionar uma ação entre passos, mova o ponteiro sobre a seta entre os degraus. 
   Escolha o sinal**+** de mais () que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, introduza "smtp" como filtro. Na lista de ações, selecione a ação que deseja.

1. Quando solicitado, forneça esta informação de ligação:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome de ligação** | Sim | Um nome para a ligação ao seu servidor SMTP | 
   | **Endereço do servidor SMTP** | Sim | O endereço do seu servidor SMTP | 
   | **Nome do utilizador** | Sim | O seu nome de utilizador para a sua conta SMTP | 
   | **Palavra-passe** | Sim | A sua palavra-passe para a sua conta SMTP | 
   | **Porta do servidor SMTP** | Não | Uma porta específica no seu servidor SMTP que pretende utilizar | 
   | **Ativar o SSL?** | Não | Ligue ou desligue a encriptação TLS/SSL. | 
   |||| 

1. Forneça os detalhes necessários para a sua ação selecionada. 

1. Guarde a sua aplicação lógica ou continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como gatilhos, ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/smtpconnector/).

> [!NOTE]
> Para aplicações lógicas num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão do conector com o rótulo ISE utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)