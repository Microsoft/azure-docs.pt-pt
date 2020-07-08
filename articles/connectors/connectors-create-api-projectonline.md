---
title: Conecte-se ao Projeto Online a partir de Azure Logic Apps
description: Automatizar fluxos de trabalho que monitorizam, criam e gerem projetos, tarefas e recursos do Project Online utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: a3e90fa3e3f57c1575a7ab09f9ce6941c13adcd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83834871"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Gerir projetos, tarefas e recursos do Projeto Online utilizando apps Azure Logic

Com a Azure Logic Apps e o conector Project Online, pode criar tarefas e fluxos de trabalho automatizados para os seus projetos, tarefas e recursos no Project Online através do Office 365. Os seus fluxos de trabalho podem realizar estas ações e outras, por exemplo:

* Monitorize quando forem criados novos projetos, tarefas ou recursos. Ou, monitorize quando novos projetos forem publicados.
* Criar novos projetos, tarefas ou recursos.
* Listar projetos ou tarefas existentes.
* Check-out, check-in ou publicar projetos.

O Project Online ajuda-o a planear, priorizar e gerir projetos e investimentos em portfólios de projetos de quase qualquer lugar em qualquer dispositivo, fornecendo poderosas capacidades de gestão de projetos. Pode utilizar os gatilhos do Project Online que obtêm respostas do Project Online e disponibilizar a saída para outras ações. Pode utilizar ações nas suas aplicações lógicas para executar várias tarefas no Project Online. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Projeto Online, disponível através de uma [conta do Office 365,](https://www.office.com/) 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder aos seus dados do Project Online. Para começar com um gatilho Project Online, [crie uma aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para utilizar as ações do Project Online, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **de Recorrência.**

## <a name="connect-to-project-online"></a>Conecte-se ao Projeto Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se não abrir já.

1. Escolha um caminho: 

   * Para aplicações lógicas em branco, na caixa de pesquisa, insira "Project Online" como filtro. 
   Na lista de gatilhos, selecione o gatilho que pretende. 

     -ou-

   * Para aplicações lógicas existentes, sob o passo em que pretende adicionar uma ação, escolha **Novo passo**. Na caixa de pesquisa, introduza "Project Online" como filtro. Na lista de ações, selecione a ação desejada.

1. Se for solicitado a iniciar sedutar no Project Online, inscreva-se agora.

   As suas credenciais autorizam a sua aplicação lógica a criar uma ligação ao Project Online e a aceder aos seus dados.

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionado e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição openAPI (anteriormente Swagger) do conector, consulte a página de [referência](/connectors/projectonline/)do conector .

## <a name="get-support"></a>Obter suporte

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)