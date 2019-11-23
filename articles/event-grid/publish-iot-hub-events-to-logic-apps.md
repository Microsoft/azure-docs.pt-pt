---
title: Tutorial - Use IoT Hub events to trigger Azure Logic Apps
description: Tutorial - Using the event routing service of Azure Event Grid, create automated processes to perform Azure Logic Apps actions based on IoT Hub events.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 70ad74715446a54605a23a049ebc92a81d7ee673
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423746"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Tutorial: Send email notifications about Azure IoT Hub events using Event Grid and Logic Apps

O Azure Event Grid permite-lhe reagir a eventos no Hub IoT ao acionar ações nas suas aplicações empresariais a jusante.

This article walks through a sample configuration that uses IoT Hub and Event Grid. At the end, you have an Azure logic app set up to send a notification email every time a device is added to your IoT hub. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de e-mail de um fornecedor de e-mail suportado pelo Azure Logic Apps, como o Outlook do Office 365, o Outlook.com ou o Gmail. Esta conta de e-mail é utilizada para enviar notificações de eventos. Para obter uma lista completa dos conectores suportados do Logic Apps, consulte a [Descrição geral de conectores](https://docs.microsoft.com/connectors/)
* Uma conta ativa do Azure. Se não tiver uma, pode [criar uma conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Um Hub IoT no Azure. Se ainda não criou um, consulte [Introdução ao Hub IoT](../iot-hub/iot-hub-csharp-csharp-getstarted.md) para obter instruções. 

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

First, create a logic app and add an event grid trigger that monitors the resource group for your virtual machine. 

### <a name="create-a-logic-app-resource"></a>Criar um recurso de aplicação lógica

1. In the [Azure portal](https://portal.azure.com), select **Create a resource**, then type "logic app" in the search box and select return. Select **Logic App** from the results.

   ![Criar uma aplicação lógica](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. On the next screen, select **Create**. 

1. Atribua um nome à aplicação lógica que seja exclusivo na sua subscrição e, em seguida, selecione essa subscrição, o grupo de recursos e a localização como o seu hub IoT. 

   ![Fields for create logic app](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Selecione **Criar**.

1. Once the resource is created, navigate to your logic app. To do this, select **Resource groups**, then select the resource group you created for this tutorial. Then find the logic app in the list of resources and select it. 

1. In the Logic Apps Designer, page down to see **Templates**. Choose **Blank Logic App** so that you can build your logic app from scratch.

### <a name="select-a-trigger"></a>Selecionar um acionador

Um acionador é um evento específico que inicia a sua aplicação lógica. Neste tutorial, o acionador que desencadeia o fluxo de trabalho está a receber um pedido através de HTTP.  

1. Na barra de pesquisa de conectores e acionadores, escreva **HTTP**.

1. Selecione **Pedido - Quando um pedido de HTTP é recebido** como o acionador. 

   ![Selecionar o acionador de pedido de HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Selecione **Utilizar o payload de exemplo para gerar esquema**. 

   ![Selecionar o acionador de pedido de HTTP](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Cole o seguinte código JSON de exemplo na caixa de texto e, em seguida, selecione **Concluído**:

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

1. Poderá receber uma notificação de pop-up com a indicação, **Lembre-se de incluir um cabeçalho Content-Type definido para application/json no seu pedido.** Pode ignorar esta sugestão com segurança e avançar para a secção seguinte. 

### <a name="create-an-action"></a>Criar uma ação

Ações são os passos que ocorrem depois de o acionador iniciar o fluxo de trabalho da aplicação lógica. Neste tutorial, a ação consiste no envio de uma notificação por e-mail a partir do seu fornecedor de e-mail. 

1. Selecione **Novo passo**. This opens a window to **Choose an action**.

1. Procure por **E-mail**.

1. Com base no seu fornecedor de e-mail, localize e selecione o conector correspondente. Este tutorial utiliza o **Outlook do Office 365**. Os passos para outros fornecedores de e-mail são semelhantes. 

   ![Selecionar o conector do fornecedor de e-mail](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Selecione a ação **Enviar uma mensagem de e-mail**. 

1. Se tal lhe for solicitado, inicie sessão na sua conta de e-mail. 

1. Crie o seu modelo de e-mail. 

   * **Para**: introduza o endereço de e-mail para receber as mensagens de e-mail de notificação. Neste tutorial, utilize uma conta de e-mail a que tenha acesso para fins de teste. 

   * **Subject**: Fill in the text for the subject. When you click on the Subject text box, you can select dynamic content to include. For example, this tutorial uses `IoT Hub alert: {event Type}`. If you can't see Dynamic content, select the **Add dynamic content** hyperlink -- this toggles it on and off.

   * **Body**: Write the text for your email. Selecione as propriedades de JSON na ferramenta do seletor para incluir conteúdo dinâmico baseado em dados de eventos. If you can't see the Dynamic content, select the **Add dynamic content** hyperlink under the **Body** text box. If it doesn't show you the fields you want, click *more* in the Dynamic content screen to include the fields from the previous action.

   O modelo de e-mail pode ter um aspeto semelhante ao deste exemplo:

   ![Preencher as informações do e-mail](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Guarde a aplicação lógica. 

### <a name="copy-the-http-url"></a>Copiar o URL de HTTP

Antes de sair do Estruturador de Aplicações Lógicas, copie o URL que as aplicações lógicas estão a escutar a fim de detetar um acionador. Este URL é utilizado para configurar o Event Grid. 

1. Expanda a caixa de configuração do acionador **Quando um pedido de HTTP é recebido** ao clicar nela. 

1. Copie o valor de **URL do HTTP POST** ao selecionar o botão para copiar junto do mesmo. 

   ![Copiar o URL do HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Guarde este URL de modo a poder referenciá-lo na secção seguinte. 

## <a name="configure-subscription-for-iot-hub-events"></a>Configurar a subscrição de eventos do Hub IoT

Nesta secção, vai configurar o Hub IoT para publicar eventos à medida que estes ocorrem. 

1. No portal do Azure, navegue para o seu hub IoT. You can do this by selecting **Resource groups**, then select the resource group for this tutorial, and then select your IoT hub from the list of resources.

2. Selecione **Eventos**.

   ![Abrir os detalhes do Event Grid](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Selecione **Subscrição de evento**. 

   ![Criar nova subscrição de evento](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Crie a subscrição de evento com os seguintes valores: 

   * **Event Subscription Details**: Provide a descriptive name and select **Event Grid Schema**.

   * **Event Types**: In the **Filter to Event Types**, uncheck all of the choices except **Device Created**.

       ![subscription event types](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Endpoint Details**: Select Endpoint Type as **Web Hook** and select *select an endpoint* and paste the URL that you copied from your logic app and confirm selection.

     ![selecione o url do ponto final](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   When you're done, the pane should look like the following example: 

    ![Formulário de exemplo de subscrição de evento](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Pode guardar a subscrição de evento aqui e receber notificações para cada dispositivo criado no seu hub IoT. For this tutorial, though, let's use the optional fields to filter for specific devices. Select **Filters** at the top of the pane.

6. Select **Add new filter**. Fill in the fields with these values:

   * **Key**: Select `Subject`.

   * **Operator**: Select `String begins with`.

   * **Value**:  Enter `devices/Building1_` to filter for device events in building 1.
  
   Add another filter with these values:

   * **Key**: Select `Subject`.

   * **Operator**: Select `String ends with`.

   * **Value**: Enter `_Temperature` to filter for device events related to temperature.

   The **Filters** tab of your event subscription should now look similar to this image:

   ![Adding filters to event subscription](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. Selecione **Criar** para guardar a subscrição de evento.

## <a name="create-a-new-device"></a>Criar um novo dispositivo

Teste a sua aplicação lógica através da criação de um novo dispositivo para acionar uma mensagem de e-mail de notificação de evento. 

1. No seu hub IoT, selecione **Dispositivos IoT**. 

2. Selecione **Novo**.

3. Para **ID de Dispositivo**, introduza `Building1_Floor1_Room1_Light`.

4. Selecione **Guardar**. 

5. Pode adicionar vários dispositivos com IDs de dispositivo diferentes para testar os filtros de subscrição de evento. Experimente estes exemplos: 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   If you added the four examples, your list of IoT devices should look like the following image:

   ![IoT Hub device list](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Após adicionar alguns dispositivos ao seu hub IoT, verifique o e-mail para ver quais foram os que acionaram a aplicação lógica. 

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

Em vez de utilizar o portal do Azure, pode realizar os passos do Hub IoT com a CLI do Azure. For details, see the Azure CLI pages for [creating an event subscription](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) and [creating an IoT device](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial utilizou recursos que incorrem em custos na sua subscrição do Azure. When you're finished trying out the tutorial and testing your results, disable or delete resources that you don't want to keep. 

To delete all of the resources created in this tutorial, delete the resource group. 

1. Select **Resource groups**, then select the resource group you created for this tutorial.

2. On the Resource group pane, select **Delete resource group**. You are prompted to enter the resource group name, and then you can delete it. All of the resources contained therein are also removed.

If you don't want to remove all of the resources, you can manage them one by one. 

Se não quiser perder o trabalho realizado na sua aplicação lógica, desative-a em vez de a eliminar. 

1. Navegue até à sua aplicação lógica.

2. No painel **Descrição Geral**, selecione **Eliminar** ou **Desativar**. 

Cada subscrição pode ter um hub IoT gratuito. Se tiver criado um hub gratuito para este tutorial, não precisa de o eliminar para evitar custos.

1. Navegue até ao seu hub IoT. 

2. No painel **Descrição Geral**, selecione **Eliminar**. 

Mesmo que decida manter o hub IoT, poderá ser conveniente eliminar a subscrição de evento que criou. 

1. No hub IoT, selecione **Event Grid**.

2. Selecione a subscrição de evento que pretende remover. 

3. Selecione **Eliminar**. 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como [Reagir aos eventos do Hub IoT com o Event Grid para acionar ações](../iot-hub/iot-hub-event-grid.md).
* [Saiba como encomendar eventos de dispositivos ligados e desligados](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Saiba o que mais pode fazer com o [Event Grid](overview.md).