---
title: Ordem de eventos de ligação do dispositivo IoT Hub do Azure com o Azure Cosmos DB | Documentos da Microsoft
description: Este artigo descreve como pedir e registar eventos de ligação do dispositivo IoT Hub do Azure com o Azure Cosmos DB para manter o estado de ligação mais recente
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.openlocfilehash: ff8f8c6656c4cd095749b3e048c72572d113f1ad
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "66015255"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Ordenar dispositivo eventos de ligação do IoT Hub com o Azure Cosmos DB

O Azure Event Grid ajuda-o a criar aplicativos baseados em eventos e integre facilmente os eventos de IoT nas suas soluções de negócios. Este artigo orienta-o através de um programa de configuração que pode ser utilizado para controlar e armazenar o estado de ligação do dispositivo mais recente no Cosmos DB. Iremos utilizar o número de sequência disponível nos eventos dispositivo ligado e desligado do dispositivo e armazenar o estado mais recente no Cosmos DB. Vamos usar um procedimento armazenado, o que é uma lógica de aplicação que é executada em relação a uma coleção do Cosmos DB.

O número de sequência é uma representação de cadeia de caracteres de um número hexadecimal. Pode usar a comparação de cadeia de caracteres para identificar o número maior. Se estiver a converter a cadeia de caracteres para hexadecimal, o número será um número de 256 bits. O número de sequência estritamente está aumentando, e o evento mais recente terá um número mais alto do que outros eventos. Isto é útil se tiver frequentes dispositivo liga e desliga e quer garantir que apenas o evento mais recente é utilizado para acionar uma ação de downstream, como o Azure Event Grid não suporta a ordenação de eventos.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. Se não tiver uma, pode [criar uma conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

* Uma conta ativa do Azure Cosmos DB SQL API. Se ainda não criou uma, veja [criar uma conta de base de dados](../cosmos-db/create-sql-api-dotnet.md#create-an-azure-cosmos-db-account) para obter instruções.

* Uma coleção na sua base de dados. Ver [adicionar uma coleção](../cosmos-db/create-sql-api-dotnet.md#add-a-database-and-a-collection) para obter instruções. Ao criar a sua coleção, utilize `/id` da chave de partição.

* Um Hub IoT no Azure. Se ainda não criou um, consulte [Introdução ao Hub IoT](iot-hub-csharp-csharp-getstarted.md) para obter instruções.

## <a name="create-a-stored-procedure"></a>Criar um procedimento armazenado

Em primeiro lugar, crie um procedimento armazenado e definir a cópia de segurança para executar uma lógica que compara o número de sequência de eventos de entrada e regista o evento mais recentes por dispositivo na base de dados.

1. Na sua API de SQL do Cosmos DB, selecione **Data Explorer** > **itens** > **novo procedimento armazenado**.

   ![Criar procedimento armazenado](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Introduza **LatestDeviceConnectionState** para o ID do procedimento armazenado e cole o seguinte no **corpo do procedimento armazenado**. Observe que esse código deve substituir qualquer código existente no corpo do procedimento armazenado. Esse código mantém uma linha por ID de dispositivo e regista o estado de ligação mais recente da ID desse dispositivo ao identificar o maior número de sequência.

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};

      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. Guarde o procedimento armazenado:

    ![guardar o procedimento armazenado](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

Primeiro, crie uma aplicação lógica e adicione um acionador do Event Grid que monitorize o grupo de recursos para a máquina virtual.

### <a name="create-a-logic-app-resource"></a>Criar um recurso de aplicação lógica

1. Na [portal do Azure](https://portal.azure.com), selecione **+ criar um recurso**, selecione **integração** e, em seguida **aplicação lógica**.

   ![Criar uma aplicação lógica](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Atribua um nome à aplicação lógica que seja exclusivo na sua subscrição e, em seguida, selecione essa subscrição, o grupo de recursos e a localização como o seu hub IoT.

   ![Nova aplicação lógica](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Selecione **criar** para criar a aplicação lógica.

   Acabou de criar um recurso do Azure para a aplicação lógica. Quando o Azure implementar a sua aplicação lógica, o Estruturador de Aplicações Lógicas mostra-lhe modelos de padrões comuns, para que possa começar mais depressa.

   > [!NOTE]
   > Para encontrar e abrir novamente a sua aplicação lógica, selecione **grupos de recursos** e selecione o grupo de recursos está a utilizar para este procedimentos. Em seguida, selecione a sua nova aplicação lógica. Esta ação abre o Estruturador da aplicação lógica.

4. No Estruturador da aplicação lógica, desloque-se à direita até ver acionadores comuns. Sob **modelos**, escolha **aplicação lógica em branco** para que pode criar a sua aplicação do zero.

### <a name="select-a-trigger"></a>Selecione um acionador

Um acionador é um evento específico que inicia a sua aplicação lógica. Neste tutorial, o acionador que desencadeia o fluxo de trabalho está a receber um pedido através de HTTP.

1. Nos conectores e acionadores barra de pesquisa, escreva **HTTP** e prima Enter.

2. Selecione **Pedido - Quando um pedido de HTTP é recebido** como o acionador.

   ![Selecionar o acionador de pedido de HTTP](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Selecione **Utilizar o payload de exemplo para gerar esquema**.

   ![Utilizar o payload de amostra para gerar um esquema](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. Cole o seguinte código JSON de exemplo na caixa de texto e, em seguida, selecione **Concluído**:

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

   ![Payload JSON de exemplo de colar](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Poderá receber uma notificação de pop-up com a indicação, **Lembre-se de incluir um cabeçalho Content-Type definido para application/json no seu pedido.** Pode ignorar esta sugestão com segurança e avançar para a secção seguinte.

### <a name="create-a-condition"></a>Criar uma condição

No seu fluxo de trabalho de aplicação lógica, condições de ajudar a executar ações específicas depois de passar essa condição específica. Assim que a condição for cumprida, pode ser definida a ação desejada. Para este tutorial, a condição é verificar se eventType é dispositivo ligado ou desligado do dispositivo. A ação estará de executar o procedimento armazenado na base de dados.

1. Selecione **+ novo passo** , em seguida, **incorporadas**, em seguida, localize e selecione **condição**. Clique em **escolher um valor** e será exibida uma caixa que mostra o conteúdo dinâmico, os campos que podem ser selecionados. Preencha os campos, conforme mostrado abaixo, apenas executá-lo para eventos de dispositivo ligado e desligado do dispositivo:

   * Escolher um valor: **eventType** – Selecione esta opção dos campos no conteúdo dinâmico que aparecem quando clica neste campo.
   * Alteração "é igual a" **termina com**.
   * Escolher um valor: **nected**.

     ![Condição de preenchimento](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. Na **se for true** caixa de diálogo, clique em **adicionar uma ação**.
  
   ![Adicionar ação, se for VERDADEIRO](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Procure o Cosmos DB e selecione **Azure Cosmos DB - executar procedimento armazenado**

   ![Pesquisa para CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Preencha **cosmosdb-connection** para o **nome da ligação** e selecione a entrada na tabela, em seguida, selecione **criar**. Verá o **executar procedimento armazenado** painel. Introduza os valores para os campos:

   **ID de base de dados**: ToDoList

   **ID da coleção**: Itens

   **ID de Sproc**: LatestDeviceConnectionState

5. Selecione **adicione o novo parâmetro**. Na lista pendente que é apresentada, selecione as caixas junto **chave de partição** e **parâmetros para o procedimento armazenado**, em seguida, clique em qualquer outra coisa na tela; ele adiciona um campo de valor de chave de partição e um campo para parâmetros do procedimento armazenado.

   ![preencher a ação da aplicação lógica](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Insira agora o valor da chave de partição e os parâmetros conforme mostrado abaixo. Certifique-se de que colocar os colchetes e aspas duplas conforme mostrado. Poderá ter de clicar **adicionar conteúdo dinâmico** para obter os valores válidos, pode utilizar aqui.

   ![preencher a ação da aplicação lógica](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Na parte superior do painel em que se lê **para cada**, em **selecionar uma saída dos passos anteriores**, certifique-se de **corpo** está selecionada.

   ![preencher a aplicação lógica para cada](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Guarde a aplicação lógica.

### <a name="copy-the-http-url"></a>Copiar o URL de HTTP

Antes de sair do estruturador de aplicações lógicas, copie o URL que a aplicação lógica está escutando para um acionador. Este URL é utilizado para configurar o Event Grid.

1. Expanda a caixa de configuração do acionador **Quando um pedido de HTTP é recebido** ao clicar nela.

2. Copie o valor de **URL do HTTP POST** ao selecionar o botão para copiar junto do mesmo.

   ![Copiar o URL do HTTP POST](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Guarde este URL de modo a poder referenciá-lo na secção seguinte.

## <a name="configure-subscription-for-iot-hub-events"></a>Configurar a subscrição de eventos do Hub IoT

Nesta secção, vai configurar o Hub IoT para publicar eventos à medida que estes ocorrem.

1. No portal do Azure, navegue para o seu hub IoT.

2. Selecione **Eventos**.

   ![Abrir os detalhes do Event Grid](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Selecione **+ subscrição de evento**.

   ![Criar nova subscrição de evento](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Preencha **detalhes da subscrição de evento**: Forneça um nome descritivo e selecione **esquema de grelha de eventos**.

5. Preencha os **tipos de evento** campos. Desmarque **subscrever todos os tipos de eventos** e selecione **dispositivo ligado** e **dispositivo desligado** no menu.

   ![Definir tipos de eventos para procurar](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. Para **detalhes do ponto final**, selecione o tipo de ponto final como **Hook de Web** e clique no ponto de extremidade selecione e cole o URL que copiou da sua aplicação lógica e confirme a seleção.

   ![Selecione o url de ponto final](./media/iot-hub-how-to-order-connection-state-events/endpoint-url.png)

7. O formulário deve agora ter um aspeto semelhante ao seguinte exemplo:

   ![Formulário de exemplo de subscrição de evento](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Selecione **Criar** para guardar a subscrição de evento.

## <a name="observe-events"></a>Observar eventos

Agora que a sua subscrição de evento é configurada, vamos testar conectando-se um dispositivo.

### <a name="register-a-device-in-iot-hub"></a>Registar um dispositivo no IoT Hub

1. No seu hub IoT, selecione **Dispositivos IoT**.

2. Selecione **+ adicionar** na parte superior do painel.

3. Para **ID de Dispositivo**, introduza `Demo-Device-1`.

4. Selecione **Guardar**.

5. Pode adicionar vários dispositivos com identificações de dispositivo diferentes.

   ![Dispositivos adicionados ao hub](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Clique no dispositivo novamente; agora as chaves e cadeias de caracteres de conexão serão preenchidas. Copiar o **cadeia de ligação – chave primária** para utilização posterior.

   ![ConnectionString para dispositivo](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

HostName=test-eventgrid-hub.azure-devices.net;DeviceId=Demo-Device-1;SharedAccessKey=cv8uPNixe7E2R9EHtimoY/PlJfBV/lOYCMajVOp/Cuw=

### <a name="start-raspberry-pi-simulator"></a>Iniciar o simulador de Raspberry Pi

1. Vamos utilizar o simulador web Raspberry Pi para simular a ligação do dispositivo.

[Iniciar o simulador de Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Executar um exemplo de aplicação no simulador web Raspberry Pi

Isto irá acionar um evento de ligada do dispositivo.

1. Na área de codificação, substitua o marcador de posição na linha 15 a cadeia de ligação de dispositivo IoT Hub do Azure que guardou no final da secção anterior.

   ![Cole a cadeia de ligação do dispositivo](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Executar a aplicação, selecionando **executar**.

Verá algo semelhante à saída seguinte que mostra os dados de sensor e as mensagens que são enviadas ao seu hub IoT.

   ![Executar a aplicação](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Clique em **parar** para parar a simulador e acionar uma **dispositivo desligado** eventos.

Agora, tem de executar um aplicativo de exemplo para recolher dados de sensor e enviá-lo ao seu hub IoT.

### <a name="observe-events-in-cosmos-db"></a>Observar eventos no Cosmos DB

Pode ver os resultados do procedimento armazenado foi executado no documento do Cosmos DB. Eis o que parece. Cada linha contém o último Estado de ligação do dispositivo por dispositivo.

   ![Como resultado](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

Em vez de utilizar o [portal do Azure](https://portal.azure.com), pode realizar os passos do IoT Hub com a CLI do Azure. Para obter detalhes, consulte as páginas de CLI do Azure dos [criar uma subscrição de evento](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) e [criação de um dispositivo IoT](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create).

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial utilizou recursos que incorrem em custos na sua subscrição do Azure. Quando tiver acabado de experimentar o tutorial e de testar os resultados, desative ou elimine os recursos que não quiser manter.

Se não quiser perder o trabalho realizado na sua aplicação lógica, desative-a em vez de a eliminar.

1. Navegue até à sua aplicação lógica.

2. Sobre o **descrição geral** painel, selecione **eliminar** ou **desativar**.

    Cada subscrição pode ter um hub IoT gratuito. Se tiver criado um hub gratuito para este tutorial, não precisa de o eliminar para evitar custos.

3. Navegue até ao seu hub IoT.

4. Sobre o **descrição geral** painel, selecione **eliminar**.

    Mesmo que decida manter o hub IoT, poderá ser conveniente eliminar a subscrição de evento que criou.

5. No hub IoT, selecione **Event Grid**.

6. Selecione a subscrição de evento que pretende remover.

7. Selecione **Eliminar**.

Para remover uma conta do Azure Cosmos DB no portal do Azure, faça duplo clique o nome da conta e clique em **eliminar conta**. Consulte para obter instruções detalhadas [eliminar uma conta do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/manage-account).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [reagir a eventos do IoT Hub com o Event Grid para realizar ações](../iot-hub/iot-hub-event-grid.md)

* [Experimente o tutorial de eventos do IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Saiba mais sobre o que mais pode fazer com [Event Grid](../event-grid/overview.md)