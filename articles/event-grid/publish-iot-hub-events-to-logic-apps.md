---
title: 'Tutorial: usar eventos do Hub IoT para disparar aplicativos lógicos do Azure'
description: 'Tutorial: usando o serviço de roteamento de eventos da grade de eventos do Azure, crie processos automatizados para executar ações de aplicativos lógicos do Azure com base em eventos do Hub IoT.'
services: iot-hub
documentationcenter: ''
author: kgremban
manager: philmea
editor: ''
ms.service: iot-hub
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: kgremban
ms.openlocfilehash: e003cb650b0589ab43c984850838c56cbbf1ff2f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106773"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>Tutorial: Enviar por e-mail notificações sobre eventos do IoT Hub do Azure com o Logic Apps

O Azure Event Grid permite-lhe reagir a eventos no Hub IoT ao acionar ações nas suas aplicações empresariais a jusante.

Este artigo descreve uma configuração de exemplo que utiliza o Hub IoT e o Event Grid. Quando chegar ao fim, terá uma aplicação lógica do Azure configurada para enviar uma mensagem de e-mail de notificação sempre que é adicionado um dispositivo ao seu hub IoT. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de e-mail de um fornecedor de e-mail suportado pelo Azure Logic Apps, como o Outlook do Office 365, o Outlook.com ou o Gmail. Esta conta de e-mail é utilizada para enviar notificações de eventos. Para obter uma lista completa dos conectores suportados do Logic Apps, consulte a [Descrição geral de conectores](https://docs.microsoft.com/connectors/)
* Uma conta ativa do Azure. Se não tiver uma, pode [criar uma conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Um Hub IoT no Azure. Se ainda não criou um, consulte [Introdução ao Hub IoT](../iot-hub/iot-hub-csharp-csharp-getstarted.md) para obter instruções. 

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

Primeiro, crie uma aplicação lógica e adicione um acionador do Event Grid que monitorize o grupo de recursos para a máquina virtual. 

### <a name="create-a-logic-app-resource"></a>Criar um recurso de aplicação lógica

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Integração** > **Aplicação Lógica**.

   ![Criar uma aplicação lógica](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. Atribua um nome à aplicação lógica que seja exclusivo na sua subscrição e, em seguida, selecione essa subscrição, o grupo de recursos e a localização como o seu hub IoT. 
3. Selecione **Criar**.

4. Depois que o recurso for criado, navegue até seu aplicativo lógico. 

5. O designer de aplicativos lógicos mostra modelos para padrões comuns para que você possa começar mais rapidamente. No Estruturador de Aplicações Lógicas, em **Modelos**, escolha **Aplicação Lógica em Branco** para criar a sua aplicação lógica de raiz.

### <a name="select-a-trigger"></a>Selecionar um acionador

Um acionador é um evento específico que inicia a sua aplicação lógica. Neste tutorial, o acionador que desencadeia o fluxo de trabalho está a receber um pedido através de HTTP.  

1. Na barra de pesquisa de conectores e acionadores, escreva **HTTP**.
2. Selecione **Pedido - Quando um pedido de HTTP é recebido** como o acionador. 

   ![Selecionar o acionador de pedido de HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. Selecione **Utilizar o payload de exemplo para gerar esquema**. 

   ![Selecionar o acionador de pedido de HTTP](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. Cole o seguinte código JSON de exemplo na caixa de texto e, em seguida, selecione **Concluído**:

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

5. Poderá receber uma notificação de pop-up com a indicação, **Lembre-se de incluir um cabeçalho Content-Type definido para application/json no seu pedido.** Pode ignorar esta sugestão com segurança e avançar para a secção seguinte. 

### <a name="create-an-action"></a>Criar uma ação

Ações são os passos que ocorrem depois de o acionador iniciar o fluxo de trabalho da aplicação lógica. Neste tutorial, a ação consiste no envio de uma notificação por e-mail a partir do seu fornecedor de e-mail. 

1. Selecione **Novo passo**. Esta ação irá abrir uma janela para **Escolher uma ação**.

2. Procure por **E-mail**.

3. Com base no seu fornecedor de e-mail, localize e selecione o conector correspondente. Este tutorial utiliza o **Outlook do Office 365**. Os passos para outros fornecedores de e-mail são semelhantes. 

   ![Selecionar o conector do fornecedor de e-mail](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. Selecione a ação **Enviar uma mensagem de e-mail**. 

5. Se tal lhe for solicitado, inicie sessão na sua conta de e-mail. 

6. Crie o seu modelo de e-mail. 
   * **Para**: introduza o endereço de e-mail para receber as mensagens de e-mail de notificação. Neste tutorial, utilize uma conta de e-mail a que tenha acesso para fins de teste. 
   * **Assunto** e **Corpo**: escreva o texto da sua mensagem de e-mail. Selecione as propriedades de JSON na ferramenta do seletor para incluir conteúdo dinâmico baseado em dados de eventos.  

   O modelo de e-mail pode ter um aspeto semelhante ao deste exemplo:

   ![Preencher as informações do e-mail](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. Guarde a aplicação lógica. 

### <a name="copy-the-http-url"></a>Copiar o URL de HTTP

Antes de sair do Estruturador de Aplicações Lógicas, copie o URL que as aplicações lógicas estão a escutar a fim de detetar um acionador. Este URL é utilizado para configurar o Event Grid. 

1. Expanda a caixa de configuração do acionador **Quando um pedido de HTTP é recebido** ao clicar nela. 
2. Copie o valor de **URL do HTTP POST** ao selecionar o botão para copiar junto do mesmo. 

   ![Copiar o URL do HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. Guarde este URL de modo a poder referenciá-lo na secção seguinte. 

## <a name="configure-subscription-for-iot-hub-events"></a>Configurar a subscrição de eventos do Hub IoT

Nesta secção, vai configurar o Hub IoT para publicar eventos à medida que estes ocorrem. 

1. No portal do Azure, navegue para o seu hub IoT. 
2. Selecione **Eventos**.

   ![Abrir os detalhes do Event Grid](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Selecione **Subscrição de evento**. 

   ![Criar nova subscrição de evento](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Crie a subscrição de evento com os seguintes valores: 
   * **Tipo de Evento**: desmarque Subscrever todos os tipos de eventos e selecione **Dispositivo Criado** no menu.
   * **Detalhes do Ponto Final**: selecione o Tipo de Ponto Final como **Webhook** e clique em selecionar ponto final e cole o URL que copiou da sua aplicação lógica e confirme a seleção.

     ![selecione o url do ponto final](./media/publish-iot-hub-events-to-logic-apps/endpoint-url.png)

   * **Detalhes da Subscrição de Evento**: forneça um nome descritivo e selecione **Esquema da Grelha de Eventos**

   Quando tiver terminado, o aspeto do formulário deverá ser semelhante ao do exemplo seguinte: 

    ![Formulário de exemplo de subscrição de evento](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Pode guardar a subscrição de evento aqui e receber notificações para cada dispositivo criado no seu hub IoT. Para este tutorial, no entanto, vamos usar os campos opcionais para filtrar dispositivos específicos. Selecione **recursos adicionais** na parte superior do formulário. 

6. Crie os seguintes filtros:

   * **Assunto Começa Com**: introduza `devices/Building1_` para filtrar os eventos de dispositivo no edifício 1.
   * **Assunto Termina Com**: introduza `_Temperature` para filtrar eventos de dispositivo relacionados com temperatura.

5. Selecione **Criar** para guardar a subscrição de evento.

## <a name="create-a-new-device"></a>Criar um novo dispositivo

Teste a sua aplicação lógica através da criação de um novo dispositivo para acionar uma mensagem de e-mail de notificação de evento. 

1. No seu hub IoT, selecione **Dispositivos IoT**. 
2. Selecione **Adicionar**.
3. Para **ID de Dispositivo**, introduza `Building1_Floor1_Room1_Temperature`.
4. Selecione **Guardar**. 
5. Pode adicionar vários dispositivos com IDs de dispositivo diferentes para testar os filtros de subscrição de evento. Experimente estes exemplos: 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

Após adicionar alguns dispositivos ao seu hub IoT, verifique o e-mail para ver quais foram os que acionaram a aplicação lógica. 

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

Em vez de utilizar o portal do Azure, pode realizar os passos do Hub IoT com a CLI do Azure. Para obter mais informações, consulte as páginas da CLI do Azure sobre como [criar uma subscrição de evento](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) e como [criar um dispositivo IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity)

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial utilizou recursos que incorrem em custos na sua subscrição do Azure. Quando tiver acabado de experimentar o tutorial e de testar os resultados, desative ou elimine os recursos que não quiser manter. 

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


