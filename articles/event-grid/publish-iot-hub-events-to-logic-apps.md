---
title: Tutorial - Use eventos IoT Hub para desencadear aplicações lógicas azure
description: Este tutorial mostra como usar o serviço de encaminhamento de eventos da Azure Event Grid, criar processos automatizados para realizar ações de Aplicações Lógicas Azure baseadas em eventos Do IoT Hub.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 334b7b2c59b328e8eff3c7c2b9c3ed46bffc3442
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706438"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Tutorial: Envie notificações por e-mail sobre eventos do Hub Azure IoT usando a Grelha de Eventos e Aplicações Lógicas

O Azure Event Grid permite-lhe reagir a eventos no Hub IoT ao acionar ações nas suas aplicações empresariais a jusante.

Este artigo percorre uma configuração de amostra que utiliza o Hub IoT e a Grelha de Eventos. No final, tem uma aplicação lógica Azure configurada para enviar um e-mail de notificação sempre que um dispositivo é adicionado ao seu hub IoT. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de e-mail de um fornecedor de e-mail suportado pelo Azure Logic Apps, como o Outlook do Office 365, o Outlook.com ou o Gmail. Esta conta de e-mail é utilizada para enviar notificações de eventos. Para obter uma lista completa dos conectores suportados do Logic Apps, consulte a [Descrição geral de conectores](https://docs.microsoft.com/connectors/)
* Uma conta ativa do Azure. Se não tiver uma, pode [criar uma conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Um Hub IoT no Azure. Se ainda não criou um, consulte [Introdução ao Hub IoT](../iot-hub/iot-hub-csharp-csharp-getstarted.md) para obter instruções. 

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

Em primeiro lugar, crie uma aplicação lógica e adicione um gatilho de rede de eventos que monitorize o grupo de recursos para a sua máquina virtual. 

### <a name="create-a-logic-app-resource"></a>Criar um recurso de aplicação lógica

1. No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso,** em seguida, digite "aplicação lógica" na caixa de pesquisa e selecione retorno. Selecione **Logic App** a partir dos resultados.

   ![Criar uma aplicação lógica](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. No ecrã seguinte, selecione **Criar**. 

1. Atribua um nome à aplicação lógica que seja exclusivo na sua subscrição e, em seguida, selecione essa subscrição, o grupo de recursos e a localização como o seu hub IoT. 

   ![Campos para criar app lógica](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Selecione **Criar**.

1. Assim que o recurso for criado, navegue para a sua aplicação lógica. Para isso, selecione **grupos de Recursos,** em seguida, selecione o grupo de recursos que criou para este tutorial. Em seguida, encontre a aplicação lógica na lista de recursos e selecione-a. 

1. No Designer de Aplicações Lógicas, previr para baixo para ver **Modelos**. Escolha **a Blank Logic App** para que possa construir a sua aplicação lógica de raiz.

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

1. Selecione **Novo passo**. Isto abre uma janela para **escolher uma ação.**

1. Procure por **E-mail**.

1. Com base no seu fornecedor de e-mail, localize e selecione o conector correspondente. Este tutorial utiliza o **Office 365 Outlook.** Os passos para outros fornecedores de e-mail são semelhantes. 

   ![Selecionar o conector do fornecedor de e-mail](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Selecione a ação **Enviar uma mensagem de e-mail**. 

1. Se tal lhe for solicitado, inicie sessão na sua conta de e-mail. 

1. Crie o seu modelo de e-mail. 

   * **Para**: introduza o endereço de e-mail para receber as mensagens de e-mail de notificação. Neste tutorial, utilize uma conta de e-mail a que tenha acesso para fins de teste. 

   * **Assunto**: Preencha o texto para o assunto. Quando clicar na caixa de texto do Assunto, pode selecionar conteúdo dinâmico para incluir. Por exemplo, este `IoT Hub alert: {event Type}`tutorial utiliza. Se não conseguir ver conteúdo dinâmico, selecione o hiperlink **de conteúdo dinâmico Adicionar** - este alterna-o dentro e fora.

   * **Corpo**: Escreva o texto para o seu e-mail. Selecione as propriedades de JSON na ferramenta do seletor para incluir conteúdo dinâmico baseado em dados de eventos. Se não conseguir ver o conteúdo Dinâmico, selecione a hiperligação dinâmica de **conteúdo adicionar** sob a caixa de texto **Body.** Se não mostrar os campos que deseja, clique *mais* no ecrã de conteúdo Dinâmico para incluir os campos da ação anterior.

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

1. No portal do Azure, navegue para o seu hub IoT. Pode fazê-lo selecionando **grupos de Recursos,** depois selecione o grupo de recursos para este tutorial e, em seguida, selecione o seu hub IoT da lista de recursos.

2. Selecione **Eventos**.

   ![Abrir os detalhes do Event Grid](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. **Selecione subscrição do Evento**. 

   ![Criar nova subscrição de evento](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Crie a subscrição de evento com os seguintes valores: 

   * **Detalhes da subscrição do evento**: Forneça um nome descritivo e selecione **Event Grid Schema**.

   * **Tipos de eventos**: No filtro para tipos de **eventos,** verifique todas as escolhas exceto **o dispositivo criado**.

       ![tipos de eventos de subscrição](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Detalhes do ponto final**: Selecione Endpoint Type como **Web Hook** e *selecione um ponto final* e colhe o URL que copiou da sua aplicação lógica e confirme a seleção.

     ![selecione o url do ponto final](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   Quando terminar, o painel deve parecer o seguinte exemplo: 

    ![Formulário de exemplo de subscrição de evento](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Pode guardar a subscrição de evento aqui e receber notificações para cada dispositivo criado no seu hub IoT. Para este tutorial, no entanto, vamos usar os campos opcionais para filtrar para dispositivos específicos. Selecione **Filtros** na parte superior do painel.

6. **Selecione Adicionar novo filtro**. Preencha os campos com estes valores:

   * **Chave**: `Subject`Selecione .

   * **Operador**: `String begins with`Selecione .

   * **Valor**: `devices/Building1_` Introduza para filtrar os eventos do dispositivo no edifício 1.
  
   Adicione outro filtro com estes valores:

   * **Chave**: `Subject`Selecione .

   * **Operador**: `String ends with`Selecione .

   * **Valor**: `_Temperature` Introduza para filtrar para eventos do dispositivo relacionados com a temperatura.

   O separador **Filtros** da subscrição do seu evento deve agora ser semelhante a esta imagem:

   ![Adicionar filtros à subscrição de eventos](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. Selecione **Criar** para guardar a subscrição de evento.

## <a name="create-a-new-device"></a>Criar um novo dispositivo

Teste a sua aplicação lógica através da criação de um novo dispositivo para acionar uma mensagem de e-mail de notificação de evento. 

1. No seu hub IoT, selecione **Dispositivos IoT**. 

2. Selecione **New**.

3. Para **ID de Dispositivo**, introduza `Building1_Floor1_Room1_Light`.

4. Selecione **Guardar**. 

5. Pode adicionar vários dispositivos com IDs de dispositivo diferentes para testar os filtros de subscrição de evento. Experimente estes exemplos: 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   Se adicionou os quatro exemplos, a sua lista de dispositivos IoT deve parecer a seguinte imagem:

   ![Lista de dispositivos IoT Hub](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Após adicionar alguns dispositivos ao seu hub IoT, verifique o e-mail para ver quais foram os que acionaram a aplicação lógica. 

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

Em vez de utilizar o portal do Azure, pode realizar os passos do Hub IoT com a CLI do Azure. Para mais detalhes, consulte as páginas Do CLI do Azure para [criar uma subscrição](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) de eventos e [criar um dispositivo IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial utilizou recursos que incorrem em custos na sua subscrição do Azure. Quando terminar de experimentar o tutorial e testar os seus resultados, desative ou elimine recursos que não quer manter. 

Para eliminar todos os recursos criados neste tutorial, elimine o grupo de recursos. 

1. Selecione **grupos de Recursos**e, em seguida, selecione o grupo de recursos que criou para este tutorial.

2. No painel do grupo Recurso, **selecione Eliminar o grupo de recursos**. É-lhe pedido que introduza o nome do grupo de recursos e, em seguida, pode apagá-lo. Todos os recursos nele contidos também são removidos.

Se não quiser remover todos os recursos, pode geri-los um a um. 

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