---
title: Tutorial-usar eventos do Hub IoT para disparar aplicativos lógicos do Azure
description: Tutorial – usando o serviço de roteamento de eventos da grade de eventos do Azure, crie processos automatizados para executar ações de aplicativos lógicos do Azure com base em eventos do Hub IoT.
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
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Tutorial: enviar notificações por email sobre os eventos do Hub IoT do Azure usando a grade de eventos e os aplicativos lógicos

O Azure Event Grid permite-lhe reagir a eventos no Hub IoT ao acionar ações nas suas aplicações empresariais a jusante.

Este artigo percorre uma configuração de exemplo que usa o Hub IoT e a grade de eventos. No final, você tem um aplicativo lógico do Azure configurado para enviar um email de notificação toda vez que um dispositivo é adicionado ao seu hub IoT. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de e-mail de um fornecedor de e-mail suportado pelo Azure Logic Apps, como o Outlook do Office 365, o Outlook.com ou o Gmail. Esta conta de e-mail é utilizada para enviar notificações de eventos. Para obter uma lista completa dos conectores suportados do Logic Apps, consulte a [Descrição geral de conectores](https://docs.microsoft.com/connectors/)
* Uma conta ativa do Azure. Se não tiver uma, pode [criar uma conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Um Hub IoT no Azure. Se ainda não criou um, consulte [Introdução ao Hub IoT](../iot-hub/iot-hub-csharp-csharp-getstarted.md) para obter instruções. 

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

Primeiro, crie um aplicativo lógico e adicione um gatilho de grade de eventos que monitora o grupo de recursos para sua máquina virtual. 

### <a name="create-a-logic-app-resource"></a>Criar um recurso de aplicação lógica

1. Na [portal do Azure](https://portal.azure.com), selecione **criar um recurso**, digite "aplicativo lógico" na caixa de pesquisa e selecione retornar. Selecione **aplicativo lógico** nos resultados.

   ![Criar uma aplicação lógica](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Na próxima tela, selecione **criar**. 

1. Atribua um nome à aplicação lógica que seja exclusivo na sua subscrição e, em seguida, selecione essa subscrição, o grupo de recursos e a localização como o seu hub IoT. 

   ![Campos para criar aplicativo lógico](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Selecione **Criar**.

1. Depois que o recurso for criado, navegue até seu aplicativo lógico. Para fazer isso, selecione **grupos de recursos**e, em seguida, selecione o grupo de recursos que você criou para este tutorial. Em seguida, localize o aplicativo lógico na lista de recursos e selecione-o. 

1. No designer de aplicativos lógicos, Page Down para ver **modelos**. Escolha **aplicativo lógico em branco** para que você possa criar seu aplicativo lógico do zero.

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

1. Selecione **Novo passo**. Isso abre uma janela para **escolher uma ação**.

1. Procure por **E-mail**.

1. Com base no seu fornecedor de e-mail, localize e selecione o conector correspondente. Este tutorial utiliza o **Outlook do Office 365**. Os passos para outros fornecedores de e-mail são semelhantes. 

   ![Selecionar o conector do fornecedor de e-mail](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Selecione a ação **Enviar uma mensagem de e-mail**. 

1. Se tal lhe for solicitado, inicie sessão na sua conta de e-mail. 

1. Crie o seu modelo de e-mail. 

   * **Para**: introduza o endereço de e-mail para receber as mensagens de e-mail de notificação. Neste tutorial, utilize uma conta de e-mail a que tenha acesso para fins de teste. 

   * **Assunto**: Preencha o texto do assunto. Ao clicar na caixa de texto assunto, você pode selecionar o conteúdo dinâmico a ser incluído. Por exemplo, este tutorial usa `IoT Hub alert: {event Type}`. Se não for possível ver o conteúdo dinâmico, selecione o hiperlink **adicionar conteúdo dinâmico** --isso liga e desliga.

   * **Corpo**: escreva o texto para seu email. Selecione as propriedades de JSON na ferramenta do seletor para incluir conteúdo dinâmico baseado em dados de eventos. Se você não conseguir ver o conteúdo dinâmico, selecione o hiperlink **adicionar conteúdo dinâmico** na caixa de texto **corpo** . Se ele não mostrar os campos desejados, clique em *mais* na tela de conteúdo dinâmico para incluir os campos da ação anterior.

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

1. No portal do Azure, navegue para o seu hub IoT. Você pode fazer isso selecionando **grupos de recursos**e, em seguida, selecionando o grupo de recursos para este tutorial e, em seguida, selecionando o Hub IOT na lista de recursos.

2. Selecione **Eventos**.

   ![Abrir os detalhes do Event Grid](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Selecione **Subscrição de evento**. 

   ![Criar nova subscrição de evento](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Crie a subscrição de evento com os seguintes valores: 

   * **Detalhes da assinatura de evento**: forneça um nome descritivo e selecione **esquema de grade de eventos**.

   * **Tipos de evento**: no **filtro para tipos de evento**, desmarque todas as opções, exceto o **dispositivo criado**.

       ![tipos de evento de assinatura](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Detalhes do ponto de extremidade**: selecione o tipo de ponto de extremidade como um **gancho da Web** e selecione *selecionar um ponto de extremidade* e cole a URL que você copiou do aplicativo lógico e confirme a seleção.

     ![selecione o url do ponto final](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   Quando terminar, o painel deverá ser semelhante ao exemplo a seguir: 

    ![Formulário de exemplo de subscrição de evento](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Pode guardar a subscrição de evento aqui e receber notificações para cada dispositivo criado no seu hub IoT. Para este tutorial, no entanto, vamos usar os campos opcionais para filtrar dispositivos específicos. Selecione **filtros** na parte superior do painel.

6. Selecione **Adicionar novo filtro**. Preencha os campos com estes valores:

   * **Chave**: selecione `Subject`.

   * **Operador**: selecione `String begins with`.

   * **Valor**: Insira `devices/Building1_` para filtrar eventos de dispositivo na compilação 1.
  
   Adicione outro filtro com estes valores:

   * **Chave**: selecione `Subject`.

   * **Operador**: selecione `String ends with`.

   * **Valor**: Insira `_Temperature` para filtrar eventos de dispositivo relacionados à temperatura.

   A guia **filtros** da sua assinatura de evento agora deve ser semelhante a esta imagem:

   ![Adicionando filtros à assinatura de evento](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

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

   Se você adicionou os quatro exemplos, sua lista de dispositivos IoT deve ser parecida com a imagem a seguir:

   ![Lista de dispositivos do Hub IoT](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Após adicionar alguns dispositivos ao seu hub IoT, verifique o e-mail para ver quais foram os que acionaram a aplicação lógica. 

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

Em vez de utilizar o portal do Azure, pode realizar os passos do Hub IoT com a CLI do Azure. Para obter detalhes, consulte as páginas CLI do Azure para [criar uma assinatura de evento](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) e [criar um dispositivo IOT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial utilizou recursos que incorrem em custos na sua subscrição do Azure. Quando você terminar de experimentar o tutorial e testar os resultados, desabilite ou exclua os recursos que não deseja manter. 

Para excluir todos os recursos criados neste tutorial, exclua o grupo de recursos. 

1. Selecione **grupos de recursos**e, em seguida, selecione o grupo de recursos que você criou para este tutorial.

2. No painel grupo de recursos, selecione **excluir grupo de recursos**. Você será solicitado a inserir o nome do grupo de recursos e, em seguida, poderá excluí-lo. Todos os recursos contidos aqui também foram removidos.

Se você não quiser remover todos os recursos, poderá gerenciá-los um a um. 

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