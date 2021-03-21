---
title: Chamar aplicações lógicas com Funções do Azure
description: Ligue ou desencadeie aplicativos lógicos utilizando Azure Functions e Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a7df9ba1318f40de8af392cfaedbe51d7a5df755
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784940"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Ligue ou desencadeie aplicativos lógicos utilizando Azure Functions e Azure Service Bus

Pode utilizar [as Funções Azure](../azure-functions/functions-overview.md) para desencadear uma aplicação lógica quando necessitar de implementar um ouvinte ou tarefa de longa duração. Por exemplo, pode criar uma função que escuta numa fila [de autocarros da Azure Service](../service-bus-messaging/service-bus-messaging-overview.md) Bus e dispara imediatamente uma aplicação lógica como um gatilho de impulso.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um espaço de nomes para autocarros da Azure Service. Se não tiver um espaço de nome, [crie primeiro o seu espaço de nome.](../service-bus-messaging/service-bus-create-namespace-portal.md)

* Uma aplicação de função, que é um recipiente para as suas funções. Se não tiver uma aplicação de função, [crie primeiro a sua aplicação de função](../azure-functions/functions-get-started.md)e certifique-se de que seleciona .NET como a pilha de tempo de execução.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Criar uma aplicação lógica

Para este cenário, tem uma função a executar cada aplicação lógica que pretende desencadear. Em primeiro lugar, crie uma aplicação lógica que comece com um gatilho de pedido HTTP. A função chama esse ponto final sempre que uma mensagem de fila é recebida.

1. Inscreva-se no [portal Azure](https://portal.azure.com)e crie uma aplicação lógica em branco.

   Se é novo em aplicações lógicas, reveja [Quickstart: Crie a sua primeira aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

1. Na caixa de pesquisa, introduza `http request`. A partir da lista de gatilhos, selecione o **pedido HTTP Quando um pedido HTTP é recebido.**

   ![Selecione o gatilho](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Com o gatilho 'Pedido', pode introduzir opcionalmente um esquema JSON para utilizar com a mensagem de fila. Os esquemas JSON ajudam o Logic App Designer a compreender a estrutura dos dados de entrada e a facilitar a utilização das saídas no seu fluxo de trabalho.

1. Para especificar um esquema, introduza o esquema na caixa **de esquemas JSON do corpo solicitado,** por exemplo:

   ![Especificar o esquema JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Se não tiver um esquema, mas tiver uma carga útil de amostra no formato JSON, pode gerar um esquema a partir dessa carga útil.

   1. No gatilho 'Pedido', **selecione Utilize a carga útil da amostra para gerar esquema**.

   1. Em **Entrar ou colar uma amostra de carga JSON,** insira a carga útil da amostra e, em seguida, selecione **Fazer**.

      ![Insira a carga útil da amostra](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

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

1. Adicione quaisquer outras ações que pretenda executar depois de receber a mensagem de fila.

   Por exemplo, pode enviar um e-mail com o conector Office 365 Outlook.

1. Guarde a sua aplicação lógica, que gera o URL de retorno para o gatilho nesta aplicação lógica. Mais tarde, utilize este URL de retorno no código para o gatilho da fila do autocarro do serviço Azure.

   O URL de retorno aparece na propriedade **HTTP POST URL.**

   ![URL de retorno gerado para o gatilho](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-a-function"></a>Criar uma função

Em seguida, crie a função que funciona como o gatilho e ouve a fila.

1. No portal Azure, abra e expanda a sua app de função, se não já estiver aberta. 

1. Sob o nome da aplicação da sua função, expanda **funções**. No painel **de funções,** selecione **Nova função**.

   ![Expandir "Funções" e selecionar "Nova função"](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Selecione este modelo com base no facto de ter criado uma nova aplicação de função onde selecionou .NET como stack de tempo de execução, ou se está a utilizar uma aplicação de função existente.

   * Para novas aplicações de funções, selecione este modelo: **Acionar o gatilho da fila do autocarro**

     ![Selecione o modelo para nova aplicação de função](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Para uma aplicação de função existente, selecione este modelo: **Acionar o gatilho da fila do autocarro - C#**

     ![Selecione o modelo para a aplicação de função existente](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. No painel de gatilho da fila do autocarro da **Azure Service,** forneça um nome para o seu gatilho e confie a **ligação Service Bus** para a fila, que utiliza o ouvinte SDK do autocarro do serviço Azure `OnMessageReceive()` e selecione **Create**.

1. Escreva uma função básica para chamar o ponto final da aplicação lógica anteriormente criado, usando a mensagem de fila como um gatilho. Antes de escrever a sua função, reveja estas considerações:

   * Este exemplo utiliza o `application/json` tipo de conteúdo de mensagem, mas pode alterar este tipo conforme necessário.
   
   * Devido a possíveis funções de funcionamento simultâneo, volumes elevados ou cargas pesadas, evite instantanear a [classe HTTPClient](/dotnet/api/system.net.http.httpclient) com a `using` declaração e criar diretamente instâncias HTTPClient por pedido. Para obter mais informações, consulte [a HttpClientFactory para implementar pedidos HTTP resilientes.](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core)
   
   * Se possível, reutilizar a instância dos clientes HTTP. Para obter mais informações, consulte [Gerir as ligações em Funções Azure](../azure-functions/manage-connections.md).

   Este exemplo utiliza o [ `Task.Run` método](/dotnet/api/system.threading.tasks.task.run) em modo [assíncronos.](/dotnet/csharp/language-reference/keywords/async) Para mais informações, consulte [a programação assíncronia com async e aguarde](/dotnet/csharp/programming-guide/concepts/async/).

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

   A aplicação lógica ativa imediatamente após a função receber a mensagem.

## <a name="next-steps"></a>Passos seguintes

* [Rebome, desencadeie ou nestflows utilizando pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md)