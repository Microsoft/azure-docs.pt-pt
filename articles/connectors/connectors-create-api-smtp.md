---
title: Conectar-se ao SMTP do aplicativo lógico do Azure | Microsoft Docs
description: Automatizar tarefas e fluxos de trabalho que enviam email por meio de sua conta SMTP (protocolo de transferência de email simples) usando aplicativos lógicos do Azure
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
ms.openlocfilehash: 1649f197d4dbd88e2b485ab32f254a2d09696a84
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050736"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Enviar email de sua conta SMTP com o aplicativo lógico do Azure

Com os aplicativos lógicos do Azure e o conector do protocolo SMTP, você pode criar tarefas automatizadas e fluxos de trabalho que enviam emails de sua conta SMTP. Você também pode fazer com que outras ações usem a saída de ações SMTP. Por exemplo, depois que o SMTP envia um email, você pode notificar sua equipe em atraso com o conector de margem de atraso. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Sua conta SMTP e as credenciais do usuário

  Suas credenciais autorizam seu aplicativo lógico a criar uma conexão e acessar sua conta SMTP.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta SMTP. Para usar uma ação SMTP, inicie seu aplicativo lógico com um gatilho, como um gatilho do Salesforce, se você tiver uma conta do Salesforce.

  Por exemplo, você pode iniciar seu aplicativo lógico com o gatilho **quando um registro é criado** do Salesforce. 
  Esse gatilho é acionado cada vez que um novo registro, como um cliente potencial, é criado no Salesforce. 
  Em seguida, você pode seguir esse gatilho com a ação **Enviar email** SMTP. Dessa forma, quando o novo registro for criado, seu aplicativo lógico enviará um email da sua conta SMTP sobre o novo registro.

## <a name="connect-to-smtp"></a>Conectar-se ao SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Na última etapa em que você deseja adicionar uma ação SMTP, escolha **nova etapa**. 

   Para adicionar uma ação entre etapas, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal de adição **+** () que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, digite "SMTP" como filtro. Na lista ações, selecione a ação desejada.

1. Quando solicitado, forneça estas informações de conexão:

   | Propriedade | Requerido | Descrição |
   |----------|----------|-------------|
   | **Nome da Ligação** | Sim | Um nome para a conexão com o servidor SMTP | 
   | **Endereço do servidor SMTP** | Sim | O endereço do servidor SMTP | 
   | **Nome de usuário** | Sim | Seu nome de usuário para sua conta SMTP | 
   | **Palavra-passe** | Sim | Sua senha para sua conta SMTP | 
   | **Porta do servidor SMTP** | Não | Uma porta específica no servidor SMTP que você deseja usar | 
   | **Habilitar SSL?** | Não | Ative ou desative a criptografia SSL. | 
   |||| 

1. Forneça os detalhes necessários para a ação selecionada. 

1. Salve seu aplicativo lógico ou continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](/connectors/smtpconnector/)do conector.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outros conectores de [aplicativos lógicos](../connectors/apis-list.md)