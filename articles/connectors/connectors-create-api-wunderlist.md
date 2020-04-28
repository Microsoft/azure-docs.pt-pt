---
title: Ligue-se ao Wunderlist a partir de Aplicações Lógicas Azure
description: Automatizar tarefas e fluxos de trabalho que monitorizam e gerem listas, tarefas, lembretes e muito mais na sua conta Wunderlist utilizando apps da Lógica Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5ac13595bd77238aaede5fa3bdc3a35ef69e8504
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789125"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Monitorize e gerencie o Wunderlist utilizando aplicações da Lógica Azure

Com as Aplicações Lógicas Azure e o conector Wunderlist, pode criar tarefas automatizadas e fluxos de trabalho que monitorizam e gerem listas de todo o conjunto, tarefas, lembretes e muito mais na sua conta Wunderlist, juntamente com outras ações, por exemplo:

* Monitorize quando novas tarefas forem criadas, quando as tarefas são devidas, ou os lembretes acontecem.
* Criar e gerir listas, notas, tarefas, subtarefas e muito mais.
* Desemos lembretes.
* Obtenha listas, tarefas, subtarefas, lembretes, ficheiros, notas, comentários e muito mais.

[Wunderlist](https://www.wunderlist.com/) é um serviço que o ajuda a planear, gerir e terminar os seus projetos, listas completas e tarefas - em qualquer dispositivo, em qualquer lugar. Pode utilizar gatilhos que obtêm respostas da sua conta Wunderlist e disponibilizar a saída a outras ações. Pode utilizar ações que executem tarefas com a sua conta Wunderlist. Também pode ter outras ações a utilizar a saída de ações wunderlist. Por exemplo, quando são devidas novas tarefas, pode publicar mensagens com o conector Slack. Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* A sua conta Wunderlist e credenciais de utilizador

   As suas credenciais autorizam a sua aplicação lógica para criar uma ligação e aceder à sua conta Wunderlist.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta Yammer. Para começar com um gatilho Wunderlist, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação Wunderlist, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **Recurrence.**

## <a name="connect-to-wunderlist"></a>Ligue-se a Wunderlist

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Escolha um caminho: 

   * Para aplicações lógicas em branco, na caixa de pesquisa, introduza "wunderlist" como filtro. 
   Na lista de gatilhos, selecione o gatilho que deseja. 

     -ou-

   * Para aplicações lógicas existentes: 
   
     * Sob o último passo em que pretende adicionar uma ação, escolha **novo passo**. 

       -ou-

     * Entre os degraus onde pretende adicionar uma ação, mova o ponteiro sobre a seta entre os degraus. 
     Escolha o sinal**+** de mais () que aparece e, em seguida, selecione **Adicionar uma ação**.
     
       Na caixa de pesquisa, introduza "wunderlist" como filtro. 
       Na lista de ações, selecione a ação que deseja.

1. Se for solicitado que inscreva-se no Wunderlist, inscreva-se agora para que possa ter acesso.

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionados e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição OpenAPI (ex-Swagger) do conector, reveja a página de [referência](/connectors/wunderlist/)do conector .

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)