---
title: Ligue-se à Salesforce a partir de Aplicações Lógicas Azure
description: Automatizar tarefas e fluxos de trabalho que monitorizam, criam e gerem registos e empregos da Salesforce utilizando apps da Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 000f4381ef2a7c0a2099a021b991087725ff2070
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789295"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Monitorize, crie e gere os recursos salesforce utilizando aplicações lógicas azure

Com as Aplicações Lógicas Azure e o conector Salesforce, pode criar tarefas automatizadas e fluxos de trabalho para os seus recursos Salesforce, tais como registos, empregos e objetos, por exemplo:

* Monitorize quando os registos são criados ou alterados. 
* Criar, obter e gerir empregos e registos, incluindo inserir, atualizar e eliminar ações.

Pode utilizar gatilhos Salesforce que obtêm respostas da Salesforce e disponibilizar a produção a outras ações. Pode utilizar ações nas suas aplicações lógicas para realizar tarefas com recursos da Salesforce. Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Uma [conta Salesforce](https://salesforce.com/)

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta Salesforce. Para começar com um gatilho Salesforce, [crie uma aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para utilizar uma ação Salesforce, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **Recurrence.**

## <a name="connect-to-salesforce"></a>Ligar ao Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Escolha um caminho: 

   * Para aplicações lógicas em branco, na caixa de pesquisa, insira "salesforce" como filtro. 
   Na lista de gatilhos, selecione o gatilho que deseja. 

     -ou-

   * Para aplicações lógicas existentes, sob o passo em que pretende adicionar uma ação, escolha **Novo passo**. Na caixa de pesquisa, introduza "salesforce" como filtro. Na lista de ações, selecione a ação que deseja.

1. Se for solicitado que assine a Salesforce, inscreva-se agora e permita o acesso.

   As suas credenciais autorizam a sua aplicação lógica para criar uma ligação à Salesforce e aceder aos seus dados.

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionados e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição OpenAPI (ex-Swagger) do conector, reveja a página de [referência](/connectors/salesforce/)do conector .

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)