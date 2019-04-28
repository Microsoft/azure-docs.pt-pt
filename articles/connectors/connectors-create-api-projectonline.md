---
title: Ligar ao Project Online a partir do Azure Logic Apps | Documentos da Microsoft
description: Automatizar fluxos de trabalho que monitorizar, criarem e gerem recursos, tarefas e projetos do Project Online com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 663363d05c1875d22a0ecc0478abcf7e0ec89c99
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105635"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Gerir projetos, tarefas e recursos Online do projeto com o Azure Logic Apps

Com o Azure Logic Apps e o conector do Project Online, pode criar tarefas automatizadas e fluxos de trabalho para os seus projetos, tarefas e recursos no Project Online através do Office 365. Os fluxos de trabalho podem realizar estas ações e outras pessoas, por exemplo:

* Monitor quando são criados novos projetos, tarefas ou recursos. Em alternativa, monitorar quando são publicados novos projetos.
* Crie novos projetos, tarefas ou recursos.
* Liste os projetos existentes ou tarefas.
* Confira, check-in ou publicar projetos.

Project Online ajuda-o a planear, priorizar e gerenciar projetos e investimentos de Portfólio de projetos de praticamente qualquer lugar em quase qualquer dispositivo, fornecendo recursos de gerenciamento de projeto poderosas. Pode usar o Project Online acionadores que obtém as respostas do Project Online e disponibilizar a saída para outras ações. Pode utilizar ações nas suas aplicações lógicas para realizar várias tarefas no Project Online. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* Projeto Online, disponível através de um [conta do Office 365](https://www.office.com/), 

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação de lógica onde pretende aceder aos dados do Project Online. Para começar com um acionador Project Online, [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar as ações de projeto Online, inicie a aplicação lógica com outro acionador, por exemplo, o **periodicidade** acionador.

## <a name="connect-to-project-online"></a>Ligar ao projeto Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Escolha um caminho: 

   * Para aplicações de lógica em branco, na caixa de pesquisa, introduza "Project Online" como o filtro. 
   Abaixo da lista de disparadores, selecione o acionador que pretende. 

     -ou-

   * Para logic apps existentes, no passo em que pretende adicionar uma ação, escolha **novo passo**. Na caixa de pesquisa, introduza "Project Online" como o filtro. Abaixo da lista de ações, selecione a ação que pretende.

1. Se lhe for pedido para iniciar sessão no Project Online, inicie sessão agora.

   As suas credenciais autorizar a aplicação lógica para criar uma ligação ao Project Online e aceder aos seus dados.

1. Forneça os detalhes necessários para o seu acionador selecionado ou a ação e continuar a criar o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](/connectors/projectonline/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)