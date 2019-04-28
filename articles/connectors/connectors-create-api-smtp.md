---
title: Ligar ao SMTP a partir do Azure Logic Apps | Documentos da Microsoft
description: Automatizar tarefas e fluxos de trabalho que enviar e-mail através da sua conta de SMTP (Simple Mail Transfer Protocol) com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 78b1eb6272fa97ef392e97723454d29cf56bb4bf
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106155"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Enviar um e-mail da sua conta SMTP com o Azure Logic Apps

Com o Azure Logic Apps e o conector de protocolo de transferência de correio simples (SMTP), pode criar fluxos de trabalho que enviem o e-mail da conta do SMTP e tarefas automatizadas. Pode também ter outras ações utilizar a saída de ações de SMTP. Por exemplo, depois do SMTP, envia uma mensagem de e-mail, pode notificar a sua equipa no Slack com o conector do Slack. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* As credenciais de conta e do utilizador do SMTP

  As suas credenciais autorizar a aplicação lógica para criar uma ligação e aceder à sua conta de SMTP.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação de lógica onde pretende aceder à sua conta de SMTP. Para utilizar uma ação de SMTP, inicie a aplicação lógica com um acionador, tal como um acionador de Salesforce, se tiver uma conta do Salesforce.

  Por exemplo, pode começar a sua aplicação lógica com o **quando é criado um registo** acionador do Salesforce. 
  Este acionador é acionado sempre que um novo registo, como uma oportunidade potencial, é criado no Salesforce. 
  Em seguida, pode seguir este acionador com o SMTP **enviar correio eletrónico** ação. Dessa forma, quando é criado o novo registo, a sua aplicação lógica envia um e-mail da sua conta SMTP sobre o novo Registro.

## <a name="connect-to-smtp"></a>Ligue-se ao SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. No último passo para adicionar uma ação de SMTP onde pretende, escolha **novo passo**. 

   Para adicionar uma ação entre passos, mova o ponteiro do mouse sobre a seta entre passos. 
   Selecione o sinal de adição (**+**) que é apresentada e, em seguida, selecione **adicionar uma ação**.

1. Na caixa de pesquisa, introduza "smtp" como o filtro. Abaixo da lista de ações, selecione a ação que pretende.

1. Quando lhe for pedido, forneça estas informações de ligação:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da Ligação** | Sim | Um nome para a ligação ao seu servidor SMTP | 
   | **Endereço do servidor de SMTP** | Sim | O endereço do seu servidor SMTP | 
   | **Nome de utilizador** | Sim | O nome de utilizador para a sua conta de SMTP | 
   | **Palavra-passe** | Sim | A palavra-passe para a sua conta de SMTP | 
   | **Porta do servidor SMTP** | Não | Uma porta específica no seu servidor SMTP que pretende utilizar | 
   | **Ativar SSL?** | Não | Ative ou desative a encriptação SSL. | 
   |||| 

1. Forneça os detalhes necessários para a ação selecionada. 

1. Guardar a aplicação lógica ou continuar a criar o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](/connectors/smtpconnector/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)