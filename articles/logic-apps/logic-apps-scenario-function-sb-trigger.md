---
title: Chamar ou acionar aplicações lógicas com funções do Azure e o Azure Service Bus
description: Criar funções do Azure que chamam ou acionam aplicações lógicas com o Azure Service Bus
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 06/04/2019
ms.openlocfilehash: 3d4f642ae25a179ea2c3241240996da774cd8c23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494987"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Chamar ou acionar aplicações lógicas com funções do Azure e Azure Service Bus

Pode usar [as funções do Azure](../azure-functions/functions-overview.md) para acionar uma aplicação lógica quando precisa implantar uma longa serviço de escuta ou a tarefa. Por exemplo, pode criar uma função do Azure que escuta numa [do Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) da fila e aciona imediatamente uma aplicação lógica como um acionador de push.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um espaço de nomes do Service bus do Azure. Se não tiver um espaço de nomes [primeiro a criar o seu espaço de nomes](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Uma aplicação de função do Azure, que é um contentor para as funções do Azure. Se não tiver uma aplicação de funções [primeiro a criar a sua aplicação function app](../azure-functions/functions-create-first-azure-function.md)e certifique-se de que seleciona .NET como a pilha de runtime.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Criar uma aplicação lógica

Para este cenário, tem uma função sendo executada a cada aplicação de lógica que pretende acionar. Primeiro, crie uma aplicação lógica que começa com um acionador de pedido HTTP. A função chama o ponto de extremidade, sempre que for recebida uma mensagem de fila.  

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e criar a aplicação lógica em branco.

   Se estiver familiarizado com aplicações lógicas, reveja [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Na caixa de pesquisa, introduza "pedido de http". Na lista de disparadores, selecione este acionador: **Quando é recebido um pedido HTTP**

   ![Selecionar acionador](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Com o acionador de pedido, pode introduzir opcionalmente um esquema JSON para utilizar com a mensagem de fila. Esquemas JSON ajudam o Estruturador da aplicação lógica compreender a estrutura para os dados de entrada e facilitar as saídas que pode utilizar no seu fluxo de trabalho.

1. Para especificar um esquema, introduza o esquema na **pedir esquema JSON do corpo** caixa, por exemplo:

   ![Especificar o esquema JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Se não tem um esquema, mas tem um payload de exemplo no formato JSON, pode gerar um esquema a partir desse payload.

   1. No acionador do pedido, escolha **utilizar payload de amostra para gerar esquema**.

   1. Sob **introduza ou cole um payload JSON de exemplo**, introduza o payload de exemplo e, em seguida, escolha **feito**.

      ![Introduza o payload de exemplo](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Este payload de exemplo gera este esquema que é apresentado no acionador:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Adicione outras ações que pretende executar depois de receber a mensagem de fila.

   Por exemplo, pode enviar um e-mail com o conector do Outlook do Office 365.

1. Guarde a aplicação lógica, que gera o URL de retorno de chamada para o acionador nesta aplicação lógica. Posteriormente, utilizar este URL de retorno de chamada no código para o acionador de fila do Azure Service Bus.

   O URL aparece no retorno de chamada a **URL do HTTP POST** propriedade.

   ![URL de chamada de retorno gerado para o acionador](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Criar função do Azure

Em seguida, crie a função que age como o acionador e escuta para a fila.

1. No portal do Azure, abra e expanda a sua aplicação function app, se não estiver aberto. 

1. No nome da aplicação de função, expanda **funções**. Sobre o **funções** painel, escolha **nova função**.

   ![Expanda "Funções" e selecione "Nova função"](./media/logic-apps-scenario-function-sb-trigger/create-new-function.png)

1. Selecione este modelo com base em se criou uma nova aplicação de função onde selecionou .NET como a pilha de runtime ou estiver a utilizar uma aplicação de função existente.

   * Para novas aplicações de função, selecione este modelo: **Acionador de fila do Service Bus**

     ![Selecione o modelo para a nova aplicação de funções](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Para uma aplicação de função existente, selecione este modelo: **Acionador de fila do Service Bus-C#**

     ![Selecione o modelo para a aplicação de função](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. Na **acionador de fila do Azure Service Bus** painel, forneça um nome para o acionador e configurar o **ligação de Service Bus** para a fila, que utiliza o SDK do Service Bus `OnMessageReceive()` serviço de escuta e escolha **Criar**.

1. Escreva uma função básica para chamar o ponto de final de aplicação lógica criada anteriormente ao utilizar a mensagem de fila como acionador. Este exemplo utiliza o `application/json` tipo de conteúdo da mensagem, mas pode alterar este tipo conforme necessário. Se possível, reutilize a instância de clientes HTTP. Para obter mais informações, consulte [gerir ligações nas funções do Azure](../azure-functions/manage-connections.md).

   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Callback URL for previously created Request trigger
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible
   private static HttpClient httpClient = new HttpClient();

   public static void Run(string myQueueItem, ILogger log)
   {
       log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

1. Para testar a função, adicione uma mensagem de fila usando uma ferramenta como o [Explorador do Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer).

   Os acionadores da aplicação lógica imediatamente após a função recebe a mensagem.

## <a name="next-steps"></a>Passos Seguintes

[Chamar, acionar ou aninhar fluxos de trabalho ao utilizar pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md)