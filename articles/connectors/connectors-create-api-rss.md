---
title: Ligue-se aos feeds RSS de Aplicações Lógicas Azure
description: Automatizar tarefas e fluxos de trabalho que monitorizam e gerem feeds RSS utilizando aplicações lógicas azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 3f1e092c2ff325cdcbc32c617af316d6fbe6dd74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789346"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Gerir feeds RSS utilizando aplicações lógicas azure

Com aplicativos azure logic e o conector RSS, você pode criar tarefas automatizadas e fluxos de trabalho para qualquer feed RSS, por exemplo:

* Monitorize quando os alimentos para alimentação RSS forem publicados.
* Lista rss todos os itens de alimentação RSS.

O RSS (Rich Site Sumário), também chamado De Sindicância Realmente Simples, é um formato popular para a sindicalização web e é usado para publicar conteúdo frequentemente atualizado, como posts de blog e manchetes de notícias. Muitos editores de conteúdo fornecem um feed RSS para que os utilizadores possam subscrever esse conteúdo. 

Pode utilizar um gatilho RSS que obtém respostas de um feed RSS e disponibiliza a saída para outras ações. Pode utilizar uma ação RSS nas suas aplicações lógicas para executar uma tarefa com o feed RSS. Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* O URL para um feed RSS

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder a um feed RSS. Para começar com um gatilho RSS, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação RSS, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **recorrência.**

## <a name="connect-to-an-rss-feed"></a>Ligar a um feed RSS

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Escolha um caminho: 

   * Para aplicações lógicas em branco, na caixa de pesquisa, introduza "rss" como filtro. Na lista de gatilhos, selecione o gatilho que deseja. 

     -ou-

   * Para aplicações lógicas existentes, sob o passo em que pretende adicionar uma ação, escolha **Novo passo**. Na caixa de pesquisa, escreva "rss" como o filtro. Na lista de ações, selecione a ação que deseja.

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionados e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição OpenAPI (ex-Swagger) do conector, reveja a página de [referência](/connectors/rss/)do conector .

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)