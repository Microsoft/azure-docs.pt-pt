---
title: Chamar aplicações lógicas com Funções do Azure
description: Criar funções Azure que liguem ou desencadeiem aplicações lógicas ouvindo o Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: afd2735bae2a79ad942c347219019ef200b61070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428714"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Ligue ou desencadeie aplicações lógicas utilizando funções azure e ônibus de serviço Azure

Pode utilizar [funções Azure](../azure-functions/functions-overview.md) para desencadear uma aplicação lógica quando precisa de implementar um ouvinte ou tarefa de longa duração. Por exemplo, pode criar uma função Azure que ouve numa fila do [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) e imediatamente dispara uma aplicação lógica como um gatilho.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um espaço de nome de ônibus de serviço Azure. Se não tiver um espaço de nome, [crie primeiro o seu espaço](../service-bus-messaging/service-bus-create-namespace-portal.md)de nome .

* Uma aplicação de função Azure, que é um recipiente para funções Azure. Se não tiver uma aplicação de função, crie primeiro a [sua aplicação](../azure-functions/functions-create-first-azure-function.md)de funções e certifique-se de que seleciona .NET como a stack de tempo de execução.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Criar uma aplicação lógica

Para este cenário, tem uma função executando cada aplicação lógica que pretende desencadear. Em primeiro lugar, crie uma aplicação lógica que comece com um gatilho de pedido HTTP. A função chama esse ponto final sempre que uma mensagem de fila é recebida.

1. Inscreva-se no [portal Azure](https://portal.azure.com)e crie uma aplicação lógica em branco.

   Se é novo em aplicações lógicas, reveja [Quickstart: Crie a sua primeira aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

1. Na caixa de pesquisa, introduza `http request`. A partir da lista de gatilhos, selecione o **gatilho Quando um pedido HTTP é recebido.**

   ![Selecione gatilho](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Com o gatilho de Pedido, pode introduzir opcionalmente um esquema JSON para utilizar com a mensagem de fila. Os esquemas jSON ajudam o Logic App Designer a compreender a estrutura dos dados de entrada e a facilitar a utilização das saídas no seu fluxo de trabalho.

1. Para especificar um esquema, insira o esquema na caixa **Do Corpo de Pedido JSON Schema,** por exemplo:

   ![Especificar esquema JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Se não tiver um esquema, mas tiver uma carga útil de amostra no formato JSON, pode gerar um esquema a partir dessa carga útil.

   1. No gatilho Solicitar, selecione Utilize a carga útil da **amostra para gerar esquema**.

   1. Em **'Entrar' ou colar uma amostra de carga útil JSON,** introduza a carga útil da amostra e, em seguida, selecione **Done**.

      ![Introduzir a carga útil da amostra](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Esta carga útil da amostra gera este esquema que aparece no gatilho:

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

1. Adicione quaisquer outras ações que queira executar depois de receber a mensagem da fila.

   Por exemplo, pode enviar um e-mail com o conector Do Outlook do Office 365.

1. Guarde a sua aplicação lógica, que gera o URL de callback para o gatilho nesta aplicação lógica. Mais tarde, utilize este URL de callback no código para o gatilho da fila de autocarros do Serviço Azure.

   O URL de callback aparece na propriedade **HTTP POST URL.**

   ![URL de callback gerado para o gatilho](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Criar função Azure

Em seguida, crie a função que funciona como o gatilho e ouve a fila.

1. No portal Azure, abra e expanda a sua aplicação de funções, se ainda não estiver aberta. 

1. Sob o nome da sua app de função, expandir **Funções**. No painel **funções,** selecione **Nova função**.

   ![Expandir "Funções" e selecionar "Nova função"](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Selecione este modelo com base no facto de ter criado uma nova aplicação de função onde selecionou .NET como a stack de tempo de execução, ou se está a utilizar uma aplicação de função existente.

   * Para novas aplicações de função, selecione este modelo: Gatilho de fila de **ônibus de serviço**

     ![Selecione modelo para nova aplicação de função](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Para uma aplicação de função existente, selecione este modelo: Gatilho de fila de **ônibus de serviço - C#**

     ![Selecione modelo para aplicação de função existente](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. No painel de gatilho sapateado azure Service Bus, forneça um nome para o seu gatilho e `OnMessageReceive()` instale a **ligação service Bus** para a fila, que utiliza o ouvinte SDK de ônibus de serviço **Azure,** e selecione **Create**.

1. Escreva uma função básica para chamar o ponto final da aplicação lógica previamente criada usando a mensagem de fila como um gatilho. Antes de escrever a sua função, reveja estas considerações:

   * Este exemplo `application/json` utiliza o tipo de conteúdo da mensagem, mas pode alterar este tipo conforme necessário.
   
   * Devido a possíveis funções de funcionamento simultâneo, volumes elevados ou cargas `using` pesadas, evite instantaneamente a [classe HTTPClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) com a declaração e crie diretamente instâncias HTTPClient por pedido. Para mais informações, consulte [Use HttpClientFactory para implementar pedidos RESILIENTES HTTP](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core).
   
   * Se possível, reutilize a instância dos clientes HTTP. Para mais informações, consulte [Gerir ligações em Funções Azure](../azure-functions/manage-connections.md).

   Este exemplo [ `Task.Run` ](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task.run) utiliza o método em modo [assíncrono.](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/async) Para mais informações, consulte [a programação assíncrona com asincronização e aguarde.](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. Para testar a função, adicione uma mensagem de fila utilizando uma ferramenta como o [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

   A aplicação lógica dispara imediatamente após a função receber a mensagem.

## <a name="next-steps"></a>Passos seguintes

* [Call, trigger ou nest workflows utilizando pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md)
