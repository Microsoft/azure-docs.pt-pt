---
title: Solicitar eventos de conexão do dispositivo do Hub IoT do Azure usando Azure Cosmos DB | Microsoft Docs
description: Este artigo descreve como ordenar e registrar eventos de conexão de dispositivo do Hub IoT do Azure usando Azure Cosmos DB para manter o estado de conexão mais recente
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.openlocfilehash: a020221d841682d1e18d2b728a732ec4dfc35ef3
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67988292"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Solicitar eventos de conexão do dispositivo do Hub IoT do Azure usando o Azure Cosmos DB

A grade de eventos do Azure ajuda você a criar aplicativos baseados em eventos e integrar facilmente eventos de IoT em suas soluções de negócios. Este artigo explica uma configuração que pode ser usada para acompanhar e armazenar o estado de conexão do dispositivo mais recente no Cosmos DB. Usaremos o número de sequência disponível no dispositivo conectado e os eventos desconectados do dispositivo e armazenamos o estado mais recente em Cosmos DB. Vamos usar um procedimento armazenado, que é uma lógica de aplicativo que é executada em uma coleção no Cosmos DB.

O número de sequência é uma representação de cadeia de caracteres de um número hexadecimal. Você pode usar a comparação de cadeia de caracteres para identificar o número maior. Se você estiver convertendo a cadeia de caracteres em Hex, o número será um número de 256 bits. O número de sequência é estritamente crescente e o evento mais recente terá um número maior do que outros eventos. Isso será útil se você tiver freqüentes conexões de dispositivo e desconexões e quiser garantir que apenas o evento mais recente seja usado para disparar uma ação downstream, pois a grade de eventos do Azure não dá suporte à ordenação de eventos.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. Se não tiver uma, pode [criar uma conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

* Uma conta da API do SQL do Active Azure Cosmos DB. Se você ainda não tiver criado um, consulte [criar uma conta de banco de dados](../cosmos-db/create-sql-api-java.md#create-a-database-account) para obter instruções.

* Uma coleção em seu banco de dados. Consulte [Adicionar uma coleção](../cosmos-db/create-sql-api-java.md#add-a-container) para obter instruções. Ao criar sua coleção, use `/id` para a chave de partição.

* Um Hub IoT no Azure. Se ainda não criou um, consulte [Introdução ao Hub IoT](iot-hub-csharp-csharp-getstarted.md) para obter instruções.

## <a name="create-a-stored-procedure"></a>Criar um procedimento armazenado

Primeiro, crie um procedimento armazenado e configure-o para executar uma lógica que compare números de sequência de eventos de entrada e registre o evento mais recente por dispositivo no banco de dados.

1. Em sua API do cosmos DB SQL, selecione **Data Explorer** > **itens** > **novo procedimento armazenado**.

   ![Criar procedimento armazenado](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Insira **LatestDeviceConnectionState** para a ID do procedimento armazenado e cole o seguinte no **corpo do procedimento armazenado**. Observe que esse código deve substituir qualquer código existente no corpo do procedimento armazenado. Esse código mantém uma linha por ID de dispositivo e registra o estado de conexão mais recente dessa ID de dispositivo identificando o número de sequência mais alto.

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

3. Salve o procedimento armazenado:

    ![salvar procedimento armazenado](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

Primeiro, crie uma aplicação lógica e adicione um acionador do Event Grid que monitorize o grupo de recursos para a máquina virtual.

### <a name="create-a-logic-app-resource"></a>Criar um recurso de aplicação lógica

1. Na [portal do Azure](https://portal.azure.com), selecione **+ criar um recurso**, selecione **integração** e, em seguida, **aplicativo lógico**.

   ![Criar uma aplicação lógica](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Atribua um nome à aplicação lógica que seja exclusivo na sua subscrição e, em seguida, selecione essa subscrição, o grupo de recursos e a localização como o seu hub IoT.

   ![Novo aplicativo lógico](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Selecione **criar** para criar o aplicativo lógico.

   Acabou de criar um recurso do Azure para a aplicação lógica. Quando o Azure implementar a sua aplicação lógica, o Estruturador de Aplicações Lógicas mostra-lhe modelos de padrões comuns, para que possa começar mais depressa.

   > [!NOTE]
   > Para localizar e abrir seu aplicativo lógico novamente, selecione **grupos de recursos** e selecione o grupo de recursos que você está usando para este "como". Em seguida, selecione seu novo aplicativo lógico. Isso abre o designer do aplicativo lógico.

4. No designer do aplicativo lógico, role para a direita até ver os gatilhos comuns. Em **modelos**, escolha **aplicativo lógico em branco** para que você possa criar seu aplicativo lógico do zero.

### <a name="select-a-trigger"></a>Selecionar um acionador

Um acionador é um evento específico que inicia a sua aplicação lógica. Neste tutorial, o acionador que desencadeia o fluxo de trabalho está a receber um pedido através de HTTP.

1. Na barra de pesquisa conectores e gatilhos, digite **http** e pressione Enter.

2. Selecione **Pedido - Quando um pedido de HTTP é recebido** como o acionador.

   ![Selecionar o acionador de pedido de HTTP](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Selecione **Utilizar o payload de exemplo para gerar esquema**.

   ![Usar o conteúdo de exemplo para gerar um esquema](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

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

   ![Colar conteúdo JSON de exemplo](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Poderá receber uma notificação de pop-up com a indicação, **Lembre-se de incluir um cabeçalho Content-Type definido para application/json no seu pedido.** Pode ignorar esta sugestão com segurança e avançar para a secção seguinte.

### <a name="create-a-condition"></a>Criar uma condição

No fluxo de trabalho do aplicativo lógico, as condições ajudam a executar ações específicas depois de passar essa condição específica. Depois que a condição for atendida, uma ação desejada poderá ser definida. Para este tutorial, a condição é verificar se o eventType está conectado ao dispositivo ou dispositivo desconectado. A ação será executar o procedimento armazenado em seu banco de dados.

1. Selecione **+ nova etapa** e, em seguida, **interna**, localize e selecione **condição**. Clique em **escolher um valor** e uma caixa será exibida mostrando o conteúdo dinâmico – os campos que podem ser selecionados. Preencha os campos conforme mostrado abaixo para executá-los somente para eventos conectados ao dispositivo e dispositivos desconectados:

   * Escolha um valor: **EventType** – selecione-o nos campos no conteúdo dinâmico que aparecem quando você clica nesse campo.
   * Altere "é igual a" para **terminar com**.
   * Escolha um valor: **nected**.

     ![Condição de preenchimento](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. Na caixa de diálogo **se verdadeiro** , clique em **Adicionar uma ação**.
  
   ![Adicionar ação se verdadeiro](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Procure Cosmos DB e selecione o **procedimento armazenado Azure Cosmos DB-execute**

   ![Pesquisar por CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Preencha **cosmosdb-Connection** para o **nome da conexão** e selecione a entrada na tabela e, em seguida, selecione **criar**. Você verá o painel **Executar procedimento armazenado** . Insira os valores para os campos:

   **ID do banco de dados**: ToDoList

   **ID da coleção**: Itens

   **ID do SPROC**: LatestDeviceConnectionState

5. Selecione **Adicionar novo parâmetro**. No menu suspenso que aparece, marque as caixas ao lado de **chave de partição** e **parâmetros para o procedimento armazenado**e clique em qualquer outro lugar na tela; Ele adiciona um campo para o valor de chave de partição e um campo para parâmetros para o procedimento armazenado.

   ![popular ação do aplicativo lógico](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Agora, insira o valor e os parâmetros da chave de partição, conforme mostrado abaixo. Certifique-se de colocar entre colchetes e aspas duplas, conforme mostrado. Talvez seja necessário clicar em **adicionar conteúdo dinâmico** para obter os valores válidos que você pode usar aqui.

   ![popular ação do aplicativo lógico](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Na parte superior do painel em que ele diz **para cada**um, em **selecionar uma saída de etapas anteriores**, verifique se o **corpo** está selecionado.

   ![popular aplicativo lógico para-cada](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Guarde a aplicação lógica.

### <a name="copy-the-http-url"></a>Copiar o URL de HTTP

Antes de sair do designer de aplicativos lógicos, copie a URL que seu aplicativo lógico está ouvindo para um gatilho. Este URL é utilizado para configurar o Event Grid.

1. Expanda a caixa de configuração do acionador **Quando um pedido de HTTP é recebido** ao clicar nela.

2. Copie o valor de **URL do HTTP POST** ao selecionar o botão para copiar junto do mesmo.

   ![Copiar o URL do HTTP POST](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Guarde este URL de modo a poder referenciá-lo na secção seguinte.

## <a name="configure-subscription-for-iot-hub-events"></a>Configurar a subscrição de eventos do Hub IoT

Nesta secção, vai configurar o Hub IoT para publicar eventos à medida que estes ocorrem.

1. No portal do Azure, navegue para o seu hub IoT.

2. Selecione **Eventos**.

   ![Abrir os detalhes do Event Grid](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Selecione **+ assinatura de evento**.

   ![Criar nova subscrição de evento](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Preencha os **detalhes da assinatura do evento**: Forneça um nome descritivo e selecione **esquema de grade de eventos**.

5. Preencha os campos de **tipos de evento** . Na lista suspensa, selecione somente **dispositivo conectado** e **dispositivo desconectado** do menu. Clique em qualquer outro lugar na tela para fechar a lista e salvar suas seleções.

   ![Definir tipos de eventos a serem procurados](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. Para obter **detalhes do ponto de extremidade**, selecione tipo de ponto de extremidade como **gancho da Web** e clique em selecionar ponto de extremidade e cole a URL que você copiou do aplicativo lógico e confirme a seleção.

   ![Selecionar URL do ponto de extremidade](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. O formulário agora deve ser semelhante ao exemplo a seguir:

   ![Formulário de exemplo de subscrição de evento](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Selecione **Criar** para guardar a subscrição de evento.

## <a name="observe-events"></a>Observar eventos

Agora que sua assinatura de evento está configurada, vamos testar conectando um dispositivo.

### <a name="register-a-device-in-iot-hub"></a>Registrar um dispositivo no Hub IoT

1. No seu hub IoT, selecione **Dispositivos IoT**.

2. Selecione **+ Adicionar** na parte superior do painel.

3. Para **ID de Dispositivo**, introduza `Demo-Device-1`.

4. Selecione **Guardar**.

5. Você pode adicionar vários dispositivos com IDs de dispositivo diferentes.

   ![Dispositivos adicionados ao Hub](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Clique no dispositivo novamente; Agora, as cadeias de conexão e as chaves serão preenchidas. Copie a **cadeia de conexão--chave primária** para uso posterior.

   ![ConnectionString para dispositivo](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Iniciar o simulador do Raspberry Pi

Vamos usar o simulador da Web do Raspberry Pi para simular a conexão do dispositivo.

[Iniciar o simulador do Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Executar um aplicativo de exemplo no simulador da Web do Raspberry Pi

Isso irá disparar um evento conectado do dispositivo.

1. Na área codificação, substitua o espaço reservado na linha 15 pela cadeia de conexão do dispositivo do Hub IoT do Azure que você salvou no final da seção anterior.

   ![Colar na cadeia de conexão do dispositivo](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Execute o aplicativo selecionando **executar**.

Você verá algo semelhante à saída a seguir que mostra os dados do sensor e as mensagens que são enviadas para o Hub IoT.

   ![Executar a aplicação](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Clique em **parar** para interromper o simulador e disparar um evento desconectado do **dispositivo** .

Agora você executou um aplicativo de exemplo para coletar dados de sensor e enviá-los para o Hub IoT.

### <a name="observe-events-in-cosmos-db"></a>Observar eventos em Cosmos DB

Você pode ver os resultados do procedimento armazenado executado em seu documento Cosmos DB. Veja como ele se parece. Cada linha contém o estado de conexão do dispositivo mais recente por dispositivo.

   ![Como resultado](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

Em vez de usar o [portal do Azure](https://portal.azure.com), você pode realizar as etapas do Hub IOT usando o CLI do Azure. Para obter detalhes, consulte as páginas CLI do Azure para [criar uma assinatura de evento](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) e [criar um dispositivo IOT](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create).

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial utilizou recursos que incorrem em custos na sua subscrição do Azure. Quando você terminar de experimentar o tutorial e testar os resultados, desabilite ou exclua os recursos que não deseja manter.

Se não quiser perder o trabalho realizado na sua aplicação lógica, desative-a em vez de a eliminar.

1. Navegue até à sua aplicação lógica.

2. Na folha **visão geral** , selecione **excluir** ou **desabilitar**.

    Cada subscrição pode ter um hub IoT gratuito. Se tiver criado um hub gratuito para este tutorial, não precisa de o eliminar para evitar custos.

3. Navegue até ao seu hub IoT.

4. Na folha **visão geral** , selecione **excluir**.

    Mesmo que decida manter o hub IoT, poderá ser conveniente eliminar a subscrição de evento que criou.

5. No hub IoT, selecione **Event Grid**.

6. Selecione a subscrição de evento que pretende remover.

7. Selecione **Eliminar**.

Para remover uma conta de Azure Cosmos DB do portal do Azure, clique com o botão direito do mouse no nome da conta e clique em **Excluir conta**. Consulte instruções detalhadas para [excluir uma conta de Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/manage-account).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre como [reagir a eventos do Hub IOT usando a grade de eventos para disparar ações](../iot-hub/iot-hub-event-grid.md)

* [Experimente o tutorial de eventos do Hub IoT](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Saiba mais sobre o que mais você pode fazer com a [grade de eventos](../event-grid/overview.md)