---
title: Conectar-se ao Wunderlist de aplicativos lógicos do Azure
description: Automatizar tarefas e fluxos de trabalho que monitoram e gerenciam listas, tarefas, lembretes e muito mais em sua conta do Wunderlist usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5ac13595bd77238aaede5fa3bdc3a35ef69e8504
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789125"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Monitorar e gerenciar o Wunderlist usando aplicativos lógicos do Azure

Com os aplicativos lógicos do Azure e o conector do Wunderlist, você pode criar tarefas e fluxos de trabalho automatizados que monitoram e gerenciam listas, tarefas, lembretes e muito mais em sua conta do Wunderlist, juntamente com outras ações, por exemplo:

* Monitore quando novas tarefas são criadas, quando as tarefas são concluídas ou lembretes acontecem.
* Crie e gerencie listas, anotações, tarefas, subtarefas e muito mais.
* Definir lembretes.
* Obtenha listas, tarefas, subtarefas, lembretes, arquivos, observações, comentários e muito mais.

O [Wunderlist](https://www.wunderlist.com/) é um serviço que ajuda você a planejar, gerenciar e concluir seus projetos, as listas de tarefas pendentes, bem como qualquer dispositivo, em qualquer lugar. Você pode usar gatilhos que obtêm respostas de sua conta do Wunderlist e disponibilizam a saída para outras ações. Você pode usar ações que executam tarefas com sua conta do Wunderlist. Você também pode fazer com que outras ações usem a saída de ações do Wunderlist. Por exemplo, quando novas tarefas são concluídas, você pode postar mensagens com o conector de margem de atraso. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Sua conta e as credenciais de usuário do Wunderlist

   Suas credenciais autorizam seu aplicativo lógico a criar uma conexão e acessar sua conta do Wunderlist.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta do Yammer. Para começar com um gatilho do Wunderlist, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação do Wunderlist, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho de **recorrência** .

## <a name="connect-to-wunderlist"></a>Conectar-se ao Wunderlist

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Escolha um caminho: 

   * Para aplicativos lógicos em branco, na caixa de pesquisa, digite "Wunderlist" como filtro. 
   Na lista de gatilhos, selecione o gatilho desejado. 

     -ou-

   * Para aplicativos lógicos existentes: 
   
     * Na última etapa em que você deseja adicionar uma ação, escolha **nova etapa**. 

       -ou-

     * Entre as etapas em que você deseja adicionar uma ação, mova o ponteiro sobre a seta entre as etapas. 
     Escolha o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.
     
       Na caixa de pesquisa, insira "Wunderlist" como seu filtro. 
       Na lista ações, selecione a ação desejada.

1. Se você for solicitado a entrar no Wunderlist, entre agora para poder permitir o acesso.

1. Forneça os detalhes necessários para o gatilho ou ação selecionado e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](/connectors/wunderlist/)do conector.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)