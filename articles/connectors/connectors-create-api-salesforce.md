---
title: Ligar ao Salesforce a partir do Azure Logic Apps | Documentos da Microsoft
description: Automatizar tarefas e fluxos de trabalho que monitorizar, criarem e gerem registos de Salesforce e tarefas com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 292d517f2c99974f4674a4c94472a0a320320ce4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106020"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Monitorizar, criar e gerir recursos de Salesforce com o Azure Logic Apps

Com o Azure Logic Apps e o conector do Salesforce, pode criar fluxos de trabalho e tarefas automatizadas para os seus recursos do Salesforce, como registos, tarefas e objetos, por exemplo:

* Monitor de quando os registos são criados ou alterados. 
* Criar, obter e gerir tarefas e registros, incluindo inserção, atualização e eliminar ações.

Pode usar os acionadores do Salesforce que obtém as respostas do Salesforce e disponibilizar a saída para outras ações. Pode utilizar ações nas suas aplicações lógicas para executar tarefas com os recursos do Salesforce. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* A [conta do Salesforce](https://salesforce.com/)

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação de lógica onde pretende aceder à sua conta do Salesforce. Para começar com um acionador de Salesforce [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação de Salesforce, inicie a aplicação lógica com outro acionador, por exemplo, o **periodicidade** acionador.

## <a name="connect-to-salesforce"></a>Ligar ao Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Escolha um caminho: 

   * Para aplicações de lógica em branco, na caixa de pesquisa, introduza "salesforce" como o filtro. 
   Abaixo da lista de disparadores, selecione o acionador que pretende. 

     -ou-

   * Para logic apps existentes, no passo em que pretende adicionar uma ação, escolha **novo passo**. Na caixa de pesquisa, introduza "salesforce" como o filtro. Abaixo da lista de ações, selecione a ação que pretende.

1. Se lhe for pedido para iniciar sessão no Salesforce, inicie sessão agora e permitir o acesso.

   As suas credenciais autorizar a aplicação lógica para criar uma ligação ao Salesforce e aceda aos seus dados.

1. Forneça os detalhes necessários para o seu acionador selecionado ou a ação e continuar a criar o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](/connectors/salesforce/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)