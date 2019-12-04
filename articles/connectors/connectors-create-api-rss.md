---
title: Conectar-se a RSS feeds de aplicativos lógicos do Azure
description: Automatizar tarefas e fluxos de trabalho que monitoram e gerenciam RSS feeds usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 3f1e092c2ff325cdcbc32c617af316d6fbe6dd74
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789346"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Gerenciar RSS feeds usando o aplicativo lógico do Azure

Com os aplicativos lógicos do Azure e o conector de RSS, você pode criar tarefas e fluxos de trabalho automatizados para qualquer RSS feed, por exemplo:

* Monitore quando os itens do RSS feed são publicados.
* Listar todos os itens do RSS feed.

RSS (Resumo de site avançado), também chamado de distribuição realmente simples, é um formato popular para a distribuição na Web e é usado para publicar conteúdo atualizado com frequência, como Postagens de blog e manchetes de notícias. Muitos editores de conteúdo fornecem um RSS feed para que os usuários possam assinar esse conteúdo. 

Você pode usar um gatilho RSS que obtém respostas de um RSS feed e disponibiliza a saída para outras ações. Você pode usar uma ação de RSS em seus aplicativos lógicos para executar uma tarefa com o RSS feed. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* A URL de um RSS feed

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar um RSS feed. Para começar com um gatilho de RSS, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação de RSS, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho de **recorrência** .

## <a name="connect-to-an-rss-feed"></a>Conectar-se a um RSS feed

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Escolha um caminho: 

   * Para aplicativos lógicos em branco, na caixa de pesquisa, digite "RSS" como filtro. Na lista de gatilhos, selecione o gatilho desejado. 

     -ou-

   * Para aplicativos lógicos existentes, na etapa em que você deseja adicionar uma ação, escolha **nova etapa**. Na caixa de pesquisa, escreva "rss" como o filtro. Na lista ações, selecione a ação desejada.

1. Forneça os detalhes necessários para o gatilho ou ação selecionado e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](/connectors/rss/)do conector.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)