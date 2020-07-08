---
title: Ligue-se aos feeds RSS a partir de Apps Azure Logic
description: Automatizar tarefas e fluxos de trabalho que monitorizam e gerem feeds RSS utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 1e95b7bfce3d57dfe579a5622c3c13fac2b6af68
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83834820"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Gerenciar feeds RSS usando apps Azure Logic

Com as Apps Azure Logic e o conector RSS, pode criar tarefas e fluxos de trabalho automatizados para qualquer feed RSS, por exemplo:

* Monitorize quando os itens de alimentação RSS forem publicados.
* Lista todos os itens de alimentação RSS.

RSS (Rich Site Summary), também chamado De Syndication Realmente Simples, é um formato popular para a sindicalização web e é usado para publicar conteúdo frequentemente atualizado, como posts de blog e manchetes de notícias. Muitos editores de conteúdo fornecem um feed RSS para que os utilizadores possam subscrever esse conteúdo. 

Pode utilizar um gatilho RSS que obtém respostas a partir de um feed RSS e disponibiliza a saída a outras ações. Pode utilizar uma ação RSS nas suas aplicações lógicas para executar uma tarefa com o feed RSS. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* O URL para um feed RSS

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder a um feed RSS. Para começar com um gatilho RSS, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação RSS, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **de Recorrência.**

## <a name="connect-to-an-rss-feed"></a>Ligar-se a um feed RSS

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se não abrir já.

1. Escolha um caminho: 

   * Para aplicações lógicas em branco, na caixa de pesquisa, introduza "rss" como filtro. Na lista de gatilhos, selecione o gatilho que pretende. 

     -ou-

   * Para aplicações lógicas existentes, sob o passo em que pretende adicionar uma ação, escolha **Novo passo**. Na caixa de pesquisa, escreva "rss" como o filtro. Na lista de ações, selecione a ação desejada.

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionado e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição openAPI (anteriormente Swagger) do conector, consulte a página de [referência](/connectors/rss/)do conector .

## <a name="get-support"></a>Obter suporte

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)