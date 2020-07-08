---
title: Ligue-se à Salesforce a partir de Apps Azure Logic
description: Automatizar tarefas e fluxos de trabalho que monitorizam, criam e gerem registos e empregos da Salesforce utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 73aeef24fde9fe58b89f1aa7789970e77e985d50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83834837"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Monitorizar, criar e gerir recursos do Salesforce com o Azure Logic Apps

Com a Azure Logic Apps e o conector Salesforce, pode criar tarefas e fluxos de trabalho automatizados para os seus recursos Salesforce, tais como registos, empregos e objetos, por exemplo:

* Monitorize quando os registos forem criados ou alterados. 
* Criar, obter e gerir empregos e registos, incluindo inserir, atualizar e eliminar ações.

Pode utilizar gatilhos salesforce que obtenham respostas da Salesforce e disponibilizem a produção para outras ações. Pode utilizar ações nas suas aplicações lógicas para executar tarefas com recursos salesforce. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Uma [conta Salesforce](https://salesforce.com/)

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta Salesforce. Para começar com um gatilho Salesforce, [crie uma aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para utilizar uma ação Salesforce, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **de Recorrência.**

## <a name="connect-to-salesforce"></a>Ligar ao Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se não abrir já.

1. Escolha um caminho: 

   * Para aplicações lógicas em branco, na caixa de pesquisa, introduza a "salesforce" como filtro. 
   Na lista de gatilhos, selecione o gatilho que pretende. 

     -ou-

   * Para aplicações lógicas existentes, sob o passo em que pretende adicionar uma ação, escolha **Novo passo**. Na caixa de pesquisa, introduza "salesforce" como filtro. Na lista de ações, selecione a ação desejada.

1. Se lhe for solicitado que inscreva-se na Salesforce, inscreva-se agora e permita o acesso.

   As suas credenciais autorizam a sua aplicação lógica a criar uma ligação à Salesforce e a aceder aos seus dados.

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionado e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição openAPI (anteriormente Swagger) do conector, consulte a página de [referência](/connectors/salesforce/)do conector .

## <a name="get-support"></a>Obter suporte

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)