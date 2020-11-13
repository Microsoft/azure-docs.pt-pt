---
title: Tutorial - Use eventos IoT Hub para desencadear apps da Azure Logic
description: Este tutorial mostra como usar o serviço de encaminhamento de eventos da Azure Event Grid, criar processos automatizados para executar ações de Azure Logic Apps com base em eventos IoT Hub.
services: iot-hub, event-grid
author: philmea
ms.service: iot-hub
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 857ae8d824443e9a8abdac7c4a66e2b014be2be0
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566355"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Tutorial: Enviar notificações por e-mail sobre eventos do Hub IoT do Azure com o Event Grid e o Logic Apps

O Azure Event Grid permite-lhe reagir a eventos no Hub IoT ao acionar ações nas suas aplicações empresariais a jusante.

Este artigo percorre uma configuração de amostra que usa IoT Hub e Grade de Eventos. No final, tem uma aplicação lógica Azure configurada para enviar um e-mail de notificação sempre que um dispositivo se conecta ou desliga para o seu hub IoT. A Grelha de Eventos pode ser usada para obter uma notificação oportuna sobre a desconexão de dispositivos críticos. As métricas e diagnósticos podem levar vários (ou seja, 20 ou mais -- embora não queiramos colocar um número nele) minutos para aparecer em registos/alertas. Isso pode ser inaceitável para infraestruturas críticas.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de e-mail de qualquer fornecedor de e-mail que seja suportado por Azure Logic Apps, como Office 365 Outlook ou Outlook.com. Esta conta de e-mail é utilizada para enviar notificações de eventos.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Pode criar rapidamente um novo hub IoT utilizando o terminal Azure Cloud Shell no portal.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. No canto superior direito da página, selecione o botão Cloud Shell.

   ![Botão do Cloud Shell](./media/publish-iot-hub-events-to-logic-apps/portal-cloud-shell.png)

1. Execute o seguinte comando para criar um novo grupo de recursos:

   ```azurecli
   az group create --name {your resource group name} --location westus
   ```
    
1. Executar o seguinte comando para criar um hub IoT:

   ```azurecli
   az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1 
   ```

1. Minimize o terminal Cloud Shell. Voltará para a concha mais tarde no tutorial.

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

Em seguida, crie uma aplicação lógica e adicione um gatilho de grelha de evento HTTP que processa pedidos a partir do hub IoT. 

### <a name="create-a-logic-app-resource"></a>Criar um recurso de aplicação lógica

1. No [portal Azure](https://portal.azure.com), **selecione Criar um recurso,** em seguida, digite "logic app" na caixa de pesquisa e selecione o retorno. Selecione **a Aplicação Lógica** a partir dos resultados.

   ![Criar uma aplicação lógica](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. No ecrã seguinte, **selecione Criar**. 

1. Atribua um nome à aplicação lógica que seja exclusivo na sua subscrição e, em seguida, selecione essa subscrição, o grupo de recursos e a localização como o seu hub IoT. 

   ![Campos para criar app lógica](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Selecione **Rever + criar**.

1. Verifique as definições e, em seguida, **selecione Criar**.

1. Assim que o recurso for criado, selecione **Ir para o recurso**. 

1. No Designer de Aplicações Lógicas, página para baixo para ver **modelos.** Escolha **a Blank Logic App** para que possa construir a sua aplicação lógica a partir do zero.

### <a name="select-a-trigger"></a>Selecionar um acionador

Um acionador é um evento específico que inicia a sua aplicação lógica. Neste tutorial, o acionador que desencadeia o fluxo de trabalho está a receber um pedido através de HTTP.  

1. Na barra de pesquisa de conectores e acionadores, escreva **HTTP**.

1. Percorra os resultados e selecione **Pedido - Quando um pedido HTTP for recebido** como gatilho. 

   ![Selecionar o acionador de pedido de HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Selecione **Utilizar o payload de exemplo para gerar esquema**. 

   ![Use a carga útil da amostra](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Cole o *esquema de evento ligado* ao dispositivo JSON na caixa de texto e, em seguida, selecione **Fazer** :

   ```json
     [{  
      "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
      "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
      "subject": "devices/LogicAppTestDevice",
      "eventType": "Microsoft.Devices.DeviceConnected",
      "eventTime": "2018-06-02T19:17:44.4383997Z",
      "data": {
          "deviceConnectionStateEventInfo": {
            "sequenceNumber":
              "000000000000000001D4132452F67CE200000002000000000000000000000001"
          },
        "hubName": "egtesthub1",
        "deviceId": "LogicAppTestDevice",
        "moduleId" : "DeviceModuleID"
      }, 
      "dataVersion": "1",
      "metadataVersion": "1"
    }]
   ```

   Este evento publica quando um dispositivo está ligado a um hub IoT.

> [!NOTE]
> Poderá receber uma notificação de pop-up com a indicação, **Lembre-se de incluir um cabeçalho Content-Type definido para application/json no seu pedido.** Pode ignorar esta sugestão com segurança e avançar para a secção seguinte. 

### <a name="create-an-action"></a>Create an action (Criar uma ação)

Ações são os passos que ocorrem depois de o acionador iniciar o fluxo de trabalho da aplicação lógica. Neste tutorial, a ação consiste no envio de uma notificação por e-mail a partir do seu fornecedor de e-mail. 

1. Selecione **Novo passo**. Isto abre uma janela para **escolher uma ação.**

1. Pesquisar por **Outlook**.

1. Com base no seu fornecedor de e-mail, localize e selecione o conector correspondente. Este tutorial usa **Outlook.com.** Os passos para outros fornecedores de e-mail são semelhantes. 

   ![Selecionar o conector do fornecedor de e-mail](./media/publish-iot-hub-events-to-logic-apps/outlook-step.png)

1. Selecione a ação **Enviar um e-mail (V2).** 

1. Selecione **Iniciar sôms** e inscreva-se na sua conta de e-mail. Selecione **Sim** para permitir que a aplicação aceda à sua informação.

1. Crie o modelo de e-mail. 

   * **Para** : introduza o endereço de e-mail para receber os e-mails de notificação. Neste tutorial, utilize uma conta de e-mail a que pode aceder para fazer o teste. 

   * **Assunto** : preencha o texto do assunto. Ao clicar na caixa de texto 'Assunto', pode selecionar conteúdo dinâmico para incluir. Por exemplo, este tutorial `IoT Hub alert: {eventType}` utiliza. Se não conseguir ver o conteúdo Dynamic, selecione a hiperligação **de conteúdo dinâmico Add** - isto alterna-o para dentro e para fora.

   * **Corpo** : escreva o texto do e-mail. Selecione as propriedades JSON da ferramenta de seletor para incluir conteúdo dinâmico com base nos dados de eventos. Se não conseguir ver o conteúdo Dynamic, selecione a hiperligação **de conteúdo dinâmico Add** sob a caixa de texto do **Corpo.** Se não lhe mostrar os campos que deseja, clique *mais* no ecrã de conteúdo dinâmico para incluir os campos da ação anterior.

   O modelo de e-mail pode ter um aspeto semelhante ao deste exemplo:

   ![Preencher as informações do e-mail](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. **Selecione Save** in the Logic Apps Designer.  

### <a name="copy-the-http-url"></a>Copiar o URL de HTTP

Antes de sair do Estruturador de Aplicações Lógicas, copie o URL que as aplicações lógicas estão a escutar a fim de detetar um acionador. Este URL é utilizado para configurar o Event Grid. 

1. Expanda a caixa de configuração do acionador **Quando um pedido de HTTP é recebido** ao clicar nela. 

1. Copie o valor de **URL do HTTP POST** ao selecionar o botão para copiar junto do mesmo. 

   ![Copiar o URL do HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Guarde este URL de modo a poder referenciá-lo na secção seguinte. 

## <a name="configure-subscription-for-iot-hub-events"></a>Configurar a subscrição de eventos do Hub IoT

Nesta secção, vai configurar o Hub IoT para publicar eventos à medida que estes ocorrem. 

1. No portal do Azure, navegue para o seu hub IoT. Pode fazê-lo selecionando **grupos de Recursos,** em seguida, selecione o grupo de recursos para este tutorial e, em seguida, selecione o seu hub IoT da lista de recursos.

1. Selecione **Eventos**.

   ![Abrir os detalhes do Event Grid](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

1. Selecione **a subscrição do Evento**. 

   ![Criar nova subscrição de evento](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

1. Crie a subscrição de evento com os seguintes valores: 

   1. Na secção DETALHES DA SUBSCRIÇÃO DO **EVENTO:**
      1. Forneça um **nome** para a subscrição do evento. 
      2. Selecione **Esquema de grelha de evento** para o esquema do **evento**. 
   2. Na secção **DETALHES TÓPICOS:**
      1. Confirme se o **tipo tópico** está definido para **IoT Hub**. 
      2. Confirme que o nome do hub IoT é definido como o valor para o campo **De Recursos Fonte.** 
      3. Insira um nome para o tópico do **sistema** que será criado para si. Para conhecer os tópicos do sistema, consulte [a visão geral dos tópicos do sistema.](system-topics.md)
   3. Na secção **TIPOS DE EVENTO:**
      1. Selecione o filtro para os tipos de **eventos** que descem.
      1. Desmarcar as caixas de verificação **criadas** e **eliminadas do dispositivo,** deixando apenas as caixas de verificação **ligadas** e **desligadas do dispositivo** selecionadas.

         ![selecionar tipos de eventos de subscrição](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   
   4. Na secção **ENDPOINT DETAILS:** 
       1. Selecione **o Tipo de Ponto Final** como Web **Hook**.
       2. Clique **em selecionar um ponto final,** cole o URL que copiou da sua aplicação lógica e confirme a seleção.

         ![selecione o url do ponto final](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         Quando terminar, o painel deverá ter um aspeto semelhante ao seguinte exemplo: 

         ![Formulário de exemplo de subscrição de evento](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

1.  Selecione **Criar**.

## <a name="simulate-a-new-device-connecting-and-sending-telemetry"></a>Simular um novo dispositivo de ligação e envio de telemetria

Teste a sua aplicação lógica simulando rapidamente uma ligação do dispositivo utilizando o Azure CLI. 

1. Selecione o botão Cloud Shell para reabrir o seu terminal.

1. Executar o seguinte comando para criar uma identidade simulada do dispositivo:
    
     ```azurecli 
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName}
    ```

1. Executar o seguinte comando para simular a ligação do seu dispositivo ao IoT Hub e enviar telemetria:

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. Quando o dispositivo simulado se ligar ao IoT Hub, receberá um e-mail a notificá-lo de um evento "DeviceConnected".

1. Quando a simulação terminar, receberá um e-mail a notificá-lo de um evento "DeviceDisconnected". 

    ![Correio de alerta de exemplo](./media/publish-iot-hub-events-to-logic-apps/alert-mail.png)

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial utilizou recursos que incorrem em custos na sua subscrição do Azure. Quando terminar de experimentar o tutorial e testar os seus resultados, desative ou elimine recursos que não quer manter. 

Para eliminar todos os recursos criados neste tutorial, elimine o grupo de recursos. 

1. Selecione **grupos de recursos** e, em seguida, selecione o grupo de recursos que criou para este tutorial.

2. No painel de grupo de recursos, **selecione Eliminar o grupo de recursos**. É-lhe pedido que introduza o nome do grupo de recursos e, em seguida, pode eliminá-lo. Todos os recursos aí contidos são também removidos.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como [Reagir aos eventos do Hub IoT com o Event Grid para acionar ações](../iot-hub/iot-hub-event-grid.md).
* [Saiba como encomendar eventos de dispositivos ligados e desligados](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Saiba o que mais pode fazer com o [Event Grid](overview.md).

Para obter uma lista completa dos conectores Logic App suportados, consulte a visão geral dos [Conectores](/connectors/).
