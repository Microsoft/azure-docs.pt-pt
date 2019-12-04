---
title: Conectar-se ao Salesforce de aplicativos lógicos do Azure
description: Automatizar tarefas e fluxos de trabalho que monitoram, criam e gerenciam registros e trabalhos do Salesforce usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 000f4381ef2a7c0a2099a021b991087725ff2070
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789295"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Monitorar, criar e gerenciar recursos do Salesforce usando aplicativos lógicos do Azure

Com os aplicativos lógicos do Azure e o conector do Salesforce, você pode criar tarefas e fluxos de trabalho automatizados para seus recursos do Salesforce, como registros, trabalhos e objetos, por exemplo:

* Monitorar quando os registros são criados ou alterados. 
* Crie, obtenha e gerencie trabalhos e registros, incluindo ações de inserção, atualização e exclusão.

Você pode usar gatilhos do Salesforce que recebem respostas do Salesforce e disponibilizam a saída para outras ações. Você pode usar ações em seus aplicativos lógicos para executar tarefas com recursos do Salesforce. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Uma [conta do Salesforce](https://salesforce.com/)

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta do Salesforce. Para começar com um gatilho do Salesforce, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação do Salesforce, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho de **recorrência** .

## <a name="connect-to-salesforce"></a>Conectar-se ao Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Escolha um caminho: 

   * Para aplicativos lógicos em branco, na caixa de pesquisa, digite "Salesforce" como filtro. 
   Na lista de gatilhos, selecione o gatilho desejado. 

     -ou-

   * Para aplicativos lógicos existentes, na etapa em que você deseja adicionar uma ação, escolha **nova etapa**. Na caixa de pesquisa, digite "Salesforce" como filtro. Na lista ações, selecione a ação desejada.

1. Se você for solicitado a entrar no Salesforce, entre agora e permita o acesso.

   Suas credenciais autorizam seu aplicativo lógico a criar uma conexão com o Salesforce e acessar seus dados.

1. Forneça os detalhes necessários para o gatilho ou ação selecionado e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](/connectors/salesforce/)do conector.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)