---
title: Ligar a RSS feeds a partir do Azure Logic Apps | Documentos da Microsoft
description: Automatizar tarefas e fluxos de trabalho que monitorizar e gerir os RSS feeds com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 01573871700bbeeb653ce3efdbf6c6aca88fd454
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104889"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Gerir os RSS feeds com o Azure Logic Apps

Com o Azure Logic Apps e o conector RSS, pode criar tarefas automatizadas e fluxos de trabalho para qualquer RSS feed, por exemplo:

* Monitor quando itens do feed RSS é publicado.
* Liste todos os itens do feed de RSS.

RSS (Rich Site resumo), também denominado Really Simple Syndication, é um formato popular para distribuição de web e é utilizado para publicar conteúdo atualizado com freqüência, como mensagens de blogue e títulos de notícias. Muitos publicadores de conteúdo fornecem que um RSS feed para que os utilizadores podem subscrever esse conteúdo. 

Pode utilizar um acionador RSS que obtém as respostas a partir de um feed RSS e disponibiliza a saída para outras ações. Pode utilizar uma ação de RSS nas suas aplicações lógicas para realizar uma tarefa com o RSS feed. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* O URL para um RSS feed

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica em que deseja acessar um RSS feed. Para começar com um acionador RSS [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação de RSS, inicie a aplicação lógica com outro acionador, por exemplo, o **periodicidade** acionador.

## <a name="connect-to-an-rss-feed"></a>Ligar a um feed RSS

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Escolha um caminho: 

   * Para aplicações de lógica em branco, na caixa de pesquisa, escreva "rss" como o filtro. Abaixo da lista de disparadores, selecione o acionador que pretende. 

     -ou-

   * Para logic apps existentes, no passo em que pretende adicionar uma ação, escolha **novo passo**. Na caixa de pesquisa, escreva "rss" como o filtro. Abaixo da lista de ações, selecione a ação que pretende.

1. Forneça os detalhes necessários para o seu acionador selecionado ou a ação e continuar a criar o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](/connectors/rss/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)