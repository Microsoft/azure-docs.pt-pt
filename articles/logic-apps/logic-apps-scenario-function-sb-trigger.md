---
title: Chamar aplicativos lógicos com Azure Functions-aplicativos lógicos do Azure
description: Criar funções do Azure que chamam ou disparam aplicativos lógicos ouvindo o barramento de serviço do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 2ab6ace7c30c3dd385928b6b0ae8000485d5f495
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680148"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Chamar ou disparar aplicativos lógicos usando o Azure Functions e o barramento de serviço do Azure

Você pode usar [Azure Functions](../azure-functions/functions-overview.md) para disparar um aplicativo lógico quando precisar implantar um ouvinte ou uma tarefa de execução longa. Por exemplo, você pode criar uma função do Azure que escuta em uma fila [do barramento de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md) e imediatamente aciona um aplicativo lógico como um gatilho de envio por push.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um namespace do barramento de serviço do Azure. Se você não tiver um namespace, [primeiro crie seu namespace](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Um aplicativo de funções do Azure, que é um contêiner do Azure functions. Se você não tiver um aplicativo de funções, [crie seu aplicativo de funções primeiro](../azure-functions/functions-create-first-azure-function.md)e certifique-se de selecionar .net como a pilha de tempo de execução.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Criar uma aplicação lógica

Para esse cenário, você tem uma função executando cada aplicativo lógico que deseja disparar. Primeiro, crie um aplicativo lógico que comece com um gatilho de solicitação HTTP. A função chama esse ponto de extremidade sempre que uma mensagem da fila é recebida.  

1. Entre no [portal do Azure](https://portal.azure.com)e crie um aplicativo lógico em branco.

   Se você for novo em aplicativos lógicos, examine [início rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Na caixa de pesquisa, digite "solicitação HTTP". Na lista de gatilhos, selecione este gatilho: **quando uma solicitação HTTP é recebida**

   ![Selecionar gatilho](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Com o gatilho de solicitação, você pode opcionalmente inserir um esquema JSON a ser usado com a mensagem da fila. Os esquemas JSON ajudam o designer de aplicativo lógico a entender a estrutura dos dados de entrada e tornar as saídas mais fáceis de usar em seu fluxo de trabalho.

1. Para especificar um esquema, insira o esquema na caixa **esquema JSON do corpo da solicitação** , por exemplo:

   ![Especificar esquema JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Se você não tiver um esquema, mas tiver um conteúdo de exemplo no formato JSON, poderá gerar um esquema a partir dessa carga.

   1. No gatilho de solicitação, escolha **usar conteúdo de exemplo para gerar o esquema**.

   1. Em **Inserir ou colar um exemplo de carga JSON**, insira seu conteúdo de exemplo e, em seguida, escolha **concluído**.

      ![Inserir conteúdo de exemplo](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Esse conteúdo de exemplo gera esse esquema que aparece no gatilho:

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

1. Adicione outras ações que você deseja executar depois de receber a mensagem da fila.

   Por exemplo, você pode enviar um email com o conector do Outlook para Office 365.

1. Salve seu aplicativo lógico, que gera a URL de retorno de chamada para o gatilho neste aplicativo lógico. Posteriormente, você usará essa URL de retorno de chamada no código para o gatilho de fila do barramento de serviço do Azure.

   A URL de retorno de chamada aparece na propriedade **URL http post** .

   ![URL de retorno de chamada gerada para o gatilho](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Criar Azure function

Em seguida, crie a função que atua como o gatilho e escuta a fila.

1. No portal do Azure, abra e expanda seu aplicativo de funções, se ainda não estiver aberto. 

1. Em nome do aplicativo de funções, expanda **funções**. No painel **funções** , escolha **nova função**.

   ![Expanda "funções" e escolha "nova função"](./media/logic-apps-scenario-function-sb-trigger/create-new-function.png)

1. Selecione este modelo com base em se você criou um novo aplicativo de funções no qual selecionou o .NET como a pilha de tempo de execução ou está usando um aplicativo de funções existente.

   * Para novos aplicativos de funções, selecione este modelo: **gatilho de fila do barramento de serviço**

     ![Selecionar modelo para o novo aplicativo de funções](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Para um aplicativo de funções existente, selecione este modelo: **gatilho de fila do C# barramento de serviço-**

     ![Selecionar modelo para o aplicativo de funções existente](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. No painel **gatilho de fila do barramento de serviço do Azure** , forneça um nome para o gatilho e configure a **conexão do barramento de serviço** para a fila, que usa o SDK do barramento de serviço do Azure `OnMessageReceive()` ouvinte e escolha **criar**.

1. Escreva uma função básica para chamar o ponto de extremidade do aplicativo lógico criado anteriormente usando a mensagem da fila como um gatilho. Este exemplo usa o tipo de conteúdo da mensagem `application/json`, mas você pode alterar esse tipo conforme necessário. Se possível, reutilize a instância de clientes HTTP. Para obter mais informações, consulte [Manage Connections in Azure Functions](../azure-functions/manage-connections.md).

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

1. Para testar a função, adicione uma mensagem da fila usando uma ferramenta como o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer).

   O aplicativo lógico é disparado imediatamente após a função receber a mensagem.

## <a name="next-steps"></a>Passos seguintes

[Chamar, disparar ou aninhar fluxos de trabalho usando pontos de extremidade HTTP](../logic-apps/logic-apps-http-endpoint.md)