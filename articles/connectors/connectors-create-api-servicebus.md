---
title: Enviar e receber mensagens com o Azure Service Bus - Azure Logic Apps | Documentos da Microsoft
description: Configurar a cloud empresarial de mensagens com o Azure Service Bus no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 68378c87e18df874059579445352b8fd1b2b6c13
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105585"
---
# <a name="exchange-messages-in-the-cloud-with-azure-service-bus-and-azure-logic-apps"></a>Mensagens do Exchange na nuvem com o Azure Service Bus e Azure Logic Apps

Com o Azure Logic Apps e o conector do Service bus do Azure, pode criar tarefas automatizadas e fluxos de trabalho que a transferência de dados, como vendas e ordens de compra, diários e movimentos de inventário em todas as aplicações para a sua organização. O conector não apenas monitoriza, envia e gerencia as mensagens, mas também executa ações com filas, sessões, tópicos, subscrições e assim por diante, por exemplo:

* Monitor de quando as mensagens chegam (concluir automaticamente) ou são recebido (bloqueio de pré-visualização) em filas, tópicos e subscrições de tópicos. 
* Envie mensagens.
* Criar e eliminar subscrições de tópicos.
* Gerir mensagens nas filas e subscrições de tópicos, por exemplo, obter, obter diferidos, concluir, diferir, abandono e mensagens não entregues.
* Renove bloqueios em sessões nas filas e subscrições de tópicos e as mensagens.
* Fechar as sessões nas filas e tópicos.

Pode usar acionadores que obtém respostas a partir do Service Bus e disponibilizar a saída para outras ações nas suas aplicações lógicas. Pode também ter outras ações utilizar a saída de ações do Service Bus. Se estiver familiarizado com o Service Bus e o Logic Apps, consulte [o que é o Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) e [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* Um espaço de nomes do Service Bus e a entidade de mensagens, por exemplo, uma fila. Se não tiver estes itens, saiba como [criar o espaço de nomes do Service Bus e uma fila](../service-bus-messaging/service-bus-create-namespace-portal.md). 

  Estes itens têm de existir na mesma subscrição do Azure que as aplicações lógicas que utilizam esses itens.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica em que pretende utilizar o Service Bus. A aplicação lógica tem de existir na mesma subscrição do Azure como seu do service bus. Para começar com um acionador de Service Bus [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação de barramento de serviço, iniciar a aplicação lógica com outro acionador, por exemplo, o **periodicidade** acionador.

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Verifique as permissões

Confirme que a aplicação lógica tem permissões para aceder ao seu espaço de nomes do Service Bus. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

2. Vá para o Service Bus *espaço de nomes*. Na página do espaço de nomes, sob **configurações**, selecione **políticas de acesso partilhado**. Sob **afirmações**, verifique se tem **gerir** permissões para esse espaço de nomes

   ![Gerir permissões para o espaço de nomes do Service Bus](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Obtenha a cadeia de ligação para o espaço de nomes do Service Bus. Precisa essa cadeia de caracteres ao introduzir as informações da ligação na sua aplicação lógica.

   1. Selecione **RootManageSharedAccessKey**. 
   
   1. Junto a sua cadeia de ligação primária, escolha o botão de cópia. Guarde a cadeia de ligação para utilização posterior.

      ![Copie a cadeia de ligação do espaço de nomes do Service Bus](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Para confirmar se a cadeia de ligação está associada ao seu espaço de nomes do Service Bus ou uma entidade de mensagens como, por exemplo, uma fila, procure a cadeia de ligação para o `EntityPath`  parâmetro. Se encontrar este parâmetro, a cadeia de ligação é de uma entidade específica e não a cadeia de caracteres correta para utilizar com a sua aplicação lógica.

## <a name="add-trigger-or-action"></a>Adicionar acionador ou ação

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Para adicionar um *acionador* para uma aplicação lógica em branco, na caixa de pesquisa, introduza "Do Azure Service Bus" como o filtro. Abaixo da lista de disparadores, selecione o acionador que pretende. 

   Por exemplo, para acionar a sua aplicação lógica quando um novo item é enviado para uma fila do Service Bus, selecione este acionador: **Quando uma mensagem é recebida numa fila (concluir automaticamente)**

   ![Selecione o acionador do Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Alguns acionadores pode retornar um ou de mensagens, por exemplo, o acionador **quando uma ou mais mensagens chegam a uma fila (concluir automaticamente)** . Quando estes acionadores são disparados, que elas retornam entre um e o número de mensagens especificado do acionador **número máximo de mensagem** propriedade.

   *Todos os acionadores do Service Bus são consulta longa de acionadores*, que significa que, quando o acionador é acionado, o acionador processa todas as mensagens e, em seguida, aguarda 30 segundos para mais mensagens sejam apresentadas na subscrição de fila ou tópico. 
   Se nenhuma mensagem for apresentada a 30 segundos, a execução de Acionador é ignorada. 
   Caso contrário, o acionador continua a ler as mensagens, até que a subscrição de fila ou tópico está vazia. A próxima pesquisa de Acionador baseia-se o intervalo de periodicidade especificado nas propriedades do acionador.

1. Para adicionar um *ação* para uma aplicação lógica existente, siga estes passos: 

   1. Na última etapa em que pretende adicionar uma ação, escolha **novo passo**. 

      Para adicionar uma ação entre passos, mova o ponteiro do mouse sobre a seta entre passos. 
      Selecione o sinal de adição ( **+** ) que é apresentada e, em seguida, selecione **adicionar uma ação**.

   1. Na caixa de pesquisa, introduza "Do Azure Service Bus" como o filtro. 
   Abaixo da lista de ações, selecione a ação que pretende. 
 
      Por exemplo, selecione a ação: **Enviar mensagem**

      ![Selecione a ação de barramento de serviço](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Se estiver a ligar a aplicação lógica para o espaço de nomes do Service Bus pela primeira vez, o Estruturador da aplicação lógica agora pede-lhe as informações da ligação. 

   1. Forneça um nome para a sua ligação e selecione o seu espaço de nomes do Service Bus.

      ![Criar ligação de Service Bus, parte 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Para introduzir manualmente a cadeia de ligação em vez disso, escolha **introduzir manualmente as informações de ligação**. 
      Se não tiver a cadeia de ligação, saiba [como encontrar a cadeia de ligação](#permissions-connection-string).

   1. Agora, selecione a política do Service Bus e, em seguida, escolha **criar**.

      ![Criar ligação de Service Bus, parte 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

1. Neste exemplo, selecione a entidade de mensagens que pretende, como uma fila ou tópico. Neste exemplo, selecione a fila do Service Bus. 
   
   ![Selecione a fila do Service Bus](./media/connectors-create-api-azure-service-bus/service-bus-select-queue.png)

1. Forneça os detalhes necessários para o seu acionador ou ação. Neste exemplo, siga os passos relevantes para o seu acionador ou ação: 

   * **Para o acionador de exemplo**: Defina o intervalo de consulta e a frequência para verificar a fila.

     ![Configurar o intervalo de consulta](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

     Quando tiver terminado, continue a criar o fluxo de trabalho da sua aplicação lógica, adicionando as ações desejadas. Por exemplo, pode adicionar uma ação que envia um e-mail quando uma nova mensagem chega.
     Quando o acionador verifica sua fila e localiza uma nova mensagem, a sua aplicação lógica executa suas ações selecionadas para a mensagem foi encontrada.

   * **Para a ação de exemplo**: Introduza o conteúdo da mensagem e outros detalhes. 

     ![Fornecer conteúdo da mensagem e detalhes](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

     Quando tiver terminado, continue a criar o fluxo de trabalho da sua aplicação lógica, adicionando outras ações que pretende. Por exemplo, pode adicionar uma ação que envia um e-mail a confirmar que a mensagem foi enviada.

1. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](/connectors/servicebus/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)