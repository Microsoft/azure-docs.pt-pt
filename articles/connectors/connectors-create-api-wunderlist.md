---
title: Ligar ao Wunderlist a partir do Azure Logic Apps | Documentos da Microsoft
description: Automatizar tarefas e fluxos de trabalho que monitorizar e gerem listas, tarefas, lembretes e muito mais na sua conta de Wunderlist com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: e3570ab1227ca388ac62bffdc74bb68b1ddc41d1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105671"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Monitorizar e gerir o Wunderlist com o Azure Logic Apps

Com o Azure Logic Apps e o conector Wunderlist, pode criar tarefas automatizadas e fluxos de trabalho que monitorizar e gerir listas de tarefas, tarefas, lembretes e muito mais na sua conta do Wunderlist, juntamente com outras ações, por exemplo:

* Monitor quando novas tarefas criadas, se as tarefas são devidas ou lembretes acontecem.
* Criar e gerir listas, anotações, tarefas, subtarefas e muito mais.
* Definir lembretes.
* Obter listas, tarefas, subtarefas, lembretes, ficheiros, notas, comentários e muito mais.

[Wunderlist](https://www.wunderlist.com/) é um serviço que ajuda a planejar, gerenciar e concluir os seus projetos, listas de tarefas e tarefas - em qualquer dispositivo, em qualquer lugar. Pode usar acionadores que obtém respostas a partir da sua conta de Wunderlist e disponibilizar a saída para outras ações. Pode utilizar as ações que executam tarefas com a sua conta de Wunderlist. Pode também ter outras ações utilizar a saída de ações de Wunderlist. Por exemplo, quando novas tarefas são devidas, pode postar mensagens com o conector do Slack. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* As credenciais de conta e do usuário do Wunderlist

   As suas credenciais autorizar a aplicação lógica para criar uma ligação e aceder à sua conta de Wunderlist.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação de lógica onde pretende aceder à sua conta do Yammer. Para começar com um acionador de Wunderlist [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação de Wunderlist, inicie a aplicação lógica com outro acionador, por exemplo, o **periodicidade** acionador.

## <a name="connect-to-wunderlist"></a>Ligue ao Wunderlist

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Escolha um caminho: 

   * Para aplicações de lógica em branco, na caixa de pesquisa, introduza "wunderlist" como o filtro. 
   Abaixo da lista de disparadores, selecione o acionador que pretende. 

     -ou-

   * Para o logic apps existentes: 
   
     * Na última etapa em que pretende adicionar uma ação, escolha **novo passo**. 

       -ou-

     * Entre os passos em que pretende adicionar uma ação, mova o ponteiro do mouse sobre a seta entre passos. 
     Selecione o sinal de adição (**+**) que é apresentada e, em seguida, selecione **adicionar uma ação**.
     
       Na caixa de pesquisa, introduza "wunderlist" como o filtro. 
       Abaixo da lista de ações, selecione a ação que pretende.

1. Se lhe for pedido para iniciar sessão no Wunderlist, inicie sessão agora para que pode permitir o acesso.

1. Forneça os detalhes necessários para o seu acionador selecionado ou a ação e continuar a criar o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](/connectors/wunderlist/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)