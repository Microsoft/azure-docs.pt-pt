---
title: Cenário – acionador logic apps com as funções do Azure e o Azure Service Bus | Documentos da Microsoft
description: Crie funções que acionam aplicações lógicas com funções do Azure e Azure Service Bus
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 08/25/2018
ms.openlocfilehash: 3a72689be1902a05a2df409366bc5caba94d6a77
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436107"
---
# <a name="scenario-trigger-logic-apps-with-azure-functions-and-azure-service-bus"></a>Cenário: Acionador logic apps com as funções do Azure e o Azure Service Bus

Pode usar as funções do Azure para criar um acionador para uma aplicação lógica, quando precisa implantar uma longa serviço de escuta ou a tarefa. Por exemplo, pode criar uma função que fica à escuta fila e, em seguida, acionar imediatamente uma aplicação lógica como um acionador de push.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Antes de poder criar uma função do Azure, [criar uma aplicação de funções](../azure-functions/functions-create-function-app-portal.md).

## <a name="create-logic-app"></a>Criar uma aplicação lógica

Neste exemplo, tem uma função em execução para cada aplicação de lógica que tem de ser acionado. Primeiro, crie uma aplicação lógica que tem um acionador de pedido HTTP. A função chama o ponto de extremidade, sempre que for recebida uma mensagem de fila.  

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e criar a aplicação lógica em branco. 

   Se estiver familiarizado com aplicações lógicas, reveja [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Na caixa de pesquisa, introduza "pedido de http". Abaixo da lista de disparadores, selecione este acionador: **Quando é recebido um pedido HTTP**

   ![Selecionar acionador](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

1. Para o **pedir** acionador, opcionalmente, pode introduzir um esquema JSON para utilização com a mensagem de fila. Esquemas JSON ajudam o Estruturador da aplicação lógica compreender a estrutura de saídas faz e dados de entrada, mais fácil para que possa selecioná em todo o fluxo de trabalho. 

   Para especificar um esquema, introduza o esquema na **pedir esquema JSON do corpo** caixa, por exemplo: 

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

1. Adicione outras ações que deve acontecer após receber a mensagem de fila. 

   Por exemplo, pode enviar um e-mail com o conector do Outlook do Office 365.

1. Guarde a aplicação lógica, que gera o URL de retorno de chamada para o acionador nesta aplicação lógica. Este URL é apresentado na **URL do HTTP POST** propriedade.

   ![URL de chamada de retorno gerado para o acionador](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Criar função do Azure

Em seguida, crie a função que age como o acionador e escuta para a fila. 

1. No portal do Azure, abra e expanda a sua aplicação function app, se não estiver aberto. 

1. No nome da aplicação de função, expanda **funções**. Sobre o **funções** painel, escolha **nova função**. Selecione este modelo: **Acionador de fila do Service Bus-C#**
   
   ![Selecione o portal de funções do Azure](./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png)

1. Forneça um nome para o acionador e, em seguida, configure a ligação para a fila do Service Bus, que utiliza o SDK do Service Bus `OnMessageReceive()` serviço de escuta.

1. Escreva uma função básica para chamar o ponto de final de aplicação lógica criada anteriormente utilizando a mensagem de fila como um acionador, por exemplo: 
   
   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   // Re-use instance of http clients if possible - https://docs.microsoft.com/en-us/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

   Este exemplo utiliza o `application/json` tipo de conteúdo da mensagem, mas pode alterar este tipo conforme necessário.

1. Para testar a função, adicione uma mensagem de fila usando uma ferramenta como o [Explorador do Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer). 

   Os acionadores da aplicação lógica imediatamente após a função recebe a mensagem.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

