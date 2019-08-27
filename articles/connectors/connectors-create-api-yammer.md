---
title: Conectar-se ao Yammer por meio do aplicativo lógico do Azure | Microsoft Docs
description: Automatizar tarefas e fluxos de trabalho que monitoram, postam e gerenciam mensagens, feeds e muito mais no Yammer usando aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 9228a94dcf27d8987b16e2caa2681cf973db0657
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050635"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Monitorar e gerenciar sua conta do Yammer usando aplicativos lógicos do Azure

Com os aplicativos lógicos do Azure e o conector do Yammer, você pode criar tarefas e fluxos de trabalho automatizados que monitoram e gerenciam mensagens, feeds e muito mais em sua conta do Yammer, juntamente com outras ações, por exemplo:

* Monitore quando novas mensagens aparecem em feeds e grupos seguidos.
* Obtenha mensagens, grupos, redes, detalhes dos usuários e muito mais.
* Postar e curtir mensagens.

Você pode usar gatilhos que obtêm respostas de sua conta do Yammer e disponibilizar a saída para outras ações. Você pode usar ações que executam tarefas com sua conta do Yammer. Você também pode fazer com que outras ações usem a saída de ações do Yammer. Por exemplo, quando novas mensagens aparecem em feeds ou grupos, você pode compartilhar essas mensagens com o conector de margem de atraso. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Sua conta do Yammer e as credenciais do usuário

   Suas credenciais autorizam seu aplicativo lógico a criar uma conexão e acessar sua conta do Yammer.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta do Yammer. Para começar com um gatilho do Yammer, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação do Yammer, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho de recorrência.

## <a name="connect-to-yammer"></a>Conectar-se ao Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Escolha um caminho: 

   * Para aplicativos lógicos em branco, na caixa de pesquisa, insira "Yammer" como filtro. 
   Na lista de gatilhos, selecione o gatilho desejado. 

     -ou-

   * Para aplicativos lógicos existentes: 
   
     * Na última etapa em que você deseja adicionar uma ação, escolha **nova etapa**. 

       -ou-

     * Entre as etapas em que você deseja adicionar uma ação, mova o ponteiro sobre a seta entre as etapas. 
     Escolha o sinal de adição **+** () que aparece e, em seguida, selecione **Adicionar uma ação**.
     
       Na caixa de pesquisa, insira "Yammer" como seu filtro. 
       Na lista ações, selecione a ação desejada.

1. Se você for solicitado a entrar no Yammer, entre agora para que possa permitir o acesso.

1. Forneça os detalhes necessários para o gatilho ou ação selecionado e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](/connectors/yammer/)do conector.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outros conectores de [aplicativos lógicos](../connectors/apis-list.md)