---
title: Ligar a qualquer ponto final de HTTP com o Azure Logic Apps | Documentos da Microsoft
description: Automatizar tarefas e fluxos de trabalho que se comunicar com qualquer ponto final de HTTP utilizando o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 01da06ca55199989a3a27012bec101580f5ef853
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442210"
---
# <a name="call-http-or-https-endpoints-with-azure-logic-apps"></a>Chamar pontos finais HTTP ou HTTPS com o Azure Logic Apps

Com o Azure Logic Apps e o conector de protocolo HTTP (Hypertext Transfer), pode automatizar fluxos de trabalho que comunicam com qualquer ponto final HTTP ou HTTPS através da criação de aplicações lógicas. Por exemplo, pode monitorizar o ponto final de serviço para o seu site. Quando ocorre um evento naquele ponto de extremidade, como o seu Web site fica em baixo, o evento aciona o fluxo de trabalho da sua aplicação lógica e executa as ações especificadas. 

Pode utilizar o acionador HTTP como o primeiro passo no seu fluxo de trabalho para a verificação ou *consulta* um ponto de extremidade com base numa agenda regular. Em cada verificação, o acionador envia uma chamada ou *pedido* para o ponto final. Resposta do ponto de extremidade determina se a fluxo de trabalho da sua aplicação lógica é executada. O acionador passa ao longo de qualquer conteúdo da resposta às ações na sua aplicação lógica. 

Pode utilizar a ação de HTTP como qualquer outro passo no fluxo de trabalho para chamar o ponto de extremidade quando quiser. Resposta do ponto de extremidade determina como executam ações restantes do seu fluxo de trabalho.

Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* O URL para o ponto de extremidade de destino que pretende chamar 

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação de lógica de onde deseja chamar o ponto de extremidade de destino para iniciar com o acionador HTTP [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar a ação de HTTP, inicie a aplicação lógica com um acionador.

## <a name="add-http-trigger"></a>Adicionar acionador HTTP

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica em branco no Estruturador da aplicação lógica, se não estiver já abrir.

1. Na caixa de pesquisa, introduza "http" como o filtro. Abaixo da lista de disparadores, selecione o **HTTP** acionador. 

   ![Selecione o acionador HTTP](./media/connectors-native-http/select-http-trigger.png)

1. Forneça o [parâmetros e valores de Acionador HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) que pretende incluir na chamada para o ponto de extremidade de destino. Configure periodicidade para a frequência com que pretende que o acionador para verificar o ponto de extremidade de destino.

   ![Introduza os parâmetros de Acionador HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Para obter mais informações sobre o acionador HTTP, parâmetros e valores, consulte [referência de tipos de Acionador e ação](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger).

1. Continue a criar o fluxo de trabalho da sua aplicação lógica com ações executadas quando o acionador é acionado.

## <a name="add-http-action"></a>Adicionar ação de HTTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Na última etapa em que pretende adicionar a ação de HTTP, escolha **novo passo**. 

   Neste exemplo, a aplicação lógica começa com o acionador HTTP como o primeiro passo.

1. Na caixa de pesquisa, introduza "http" como o filtro. Abaixo da lista de ações, selecione o **HTTP** ação.

   ![Selecione a ação de HTTP](./media/connectors-native-http/select-http-action.png)

   Para adicionar uma ação entre passos, mova o ponteiro do mouse sobre a seta entre passos. 
   Selecione o sinal de adição (**+**) que é apresentada e, em seguida, selecione **adicionar uma ação**.

1. Forneça o [parâmetros e valores de ação de HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) que pretende incluir na chamada para o ponto de extremidade de destino. 

   ![Introduza os parâmetros de ação de HTTP](./media/connectors-native-http/http-action-parameters.png)

1. Quando tiver terminado, certifique-se de que guardar a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**. 

## <a name="authentication"></a>Authentication

Para configurar a autenticação, escolha **Mostrar opções avançadas** dentro da ação ou acionador. Para obter mais informações sobre os tipos de autenticação disponíveis para ações e acionadores HTTP, consulte [referência de tipos de Acionador e ação](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
