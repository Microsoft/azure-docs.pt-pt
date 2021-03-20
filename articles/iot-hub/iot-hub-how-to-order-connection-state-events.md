---
title: Encomende eventos de conexão do dispositivo fr Azure IoT Hub c/Azure Cosmos DB
description: Este artigo descreve como encomendar e registar eventos de ligação de dispositivos a partir do Azure IoT Hub usando Azure Cosmos DB para manter o estado de ligação mais recente
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.custom: devx-track-azurecli
ms.openlocfilehash: 90b7b6aebfce1c37bef76d371d829048d755e39e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147270"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Ordenar eventos de ligação do dispositivo do Hub IoT com o Azure Cosmos DB

A Azure Event Grid ajuda-o a construir aplicações baseadas em eventos e a integrar facilmente eventos IoT nas suas soluções de negócio. Este artigo acompanha-o através de uma configuração que pode ser usada para rastrear e armazenar o mais recente estado de ligação do dispositivo em Cosmos DB. Utilizaremos o número de sequência disponível nos eventos ligados e desligados do dispositivo e armazenaremos o estado mais recente em Cosmos DB. Vamos usar um procedimento armazenado, que é uma lógica de aplicação que é executada contra uma coleção em Cosmos DB.

O número da sequência é uma representação de uma sequência de um número hexadecimal. Pode utilizar a comparação de cordas para identificar o número maior. Se estiver a converter a corda em hexáxinho, o número será um número de 256 bits. O número da sequência está a aumentar estritamente, e o evento mais recente terá um número maior do que outros eventos. Isto é útil se tiver ligações e desconexões frequentes de dispositivos, e pretender garantir que apenas o evento mais recente é usado para desencadear uma ação a jusante, uma vez que a Azure Event Grid não suporta a encomenda de eventos.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. Se não tiver uma, pode [criar uma conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

* Uma conta Azure Cosmos DB API. Se ainda não criou um, consulte [criar uma conta de base de dados](../cosmos-db/create-sql-api-java.md#create-a-database-account) para uma passagem.

* Uma coleção na sua base de dados. Consulte [uma coleção](../cosmos-db/create-sql-api-java.md#add-a-container) para uma passagem. Quando criar a sua coleção, use `/id` para a chave de partição.

* Um Hub IoT no Azure. Se ainda não criou um, consulte [Introdução ao Hub IoT](./quickstart-send-telemetry-dotnet.md) para obter instruções.

## <a name="create-a-stored-procedure"></a>Criar um procedimento armazenado

Em primeiro lugar, crie um procedimento armazenado e crie-o para executar uma lógica que compare os números de sequência de eventos de entrada e regista o mais recente evento por dispositivo na base de dados.

1. No seu Cosmos DB SQL API, selecione **Data Explorer**  >  **Items**  >  **New Stored Procedure**.

   ![Criar procedimento armazenado](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Introduza **o Mais Recente Estado de Identificação deDevice** para o ID do procedimento armazenado e cole o seguinte no organismo de procedimento **armazenado**. Note que este código deve substituir qualquer código existente no organismo de procedimentos armazenado. Este código mantém uma linha por ID do dispositivo e regista o estado de ligação mais recente desse ID do dispositivo identificando o número de sequência mais elevado.

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

    ![salvar o procedimento armazenado](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

Primeiro, crie uma aplicação lógica e adicione um acionador do Event Grid que monitorize o grupo de recursos para a máquina virtual.

### <a name="create-a-logic-app-resource"></a>Criar um recurso de aplicação lógica

1. No [portal Azure](https://portal.azure.com), selecione **+Criar um recurso,** selecione **Integração** e, em seguida, **Aplicação Lógica**.

   ![Criar uma aplicação lógica](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Atribua um nome à aplicação lógica que seja exclusivo na sua subscrição e, em seguida, selecione essa subscrição, o grupo de recursos e a localização como o seu hub IoT.

   ![Nova aplicação lógica](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Selecione **Criar** para criar a aplicação lógica.

   Acabou de criar um recurso do Azure para a aplicação lógica. Quando o Azure implementar a sua aplicação lógica, o Estruturador de Aplicações Lógicas mostra-lhe modelos de padrões comuns, para que possa começar mais depressa.

   > [!NOTE]
   > Para encontrar e abrir novamente a sua aplicação lógica, selecione **grupos de Recursos** e selecione o grupo de recursos que está a usar para este como fazê-lo. Em seguida, selecione a sua nova aplicação lógica. Isto abre o Logic App Designer.

4. No Logic App Designer, percorra para a direita até ver os gatilhos comuns. Em **Modelos,** escolha **a App Blank Logic** para que possa construir a sua aplicação lógica a partir do zero.

### <a name="select-a-trigger"></a>Selecionar um acionador

Um acionador é um evento específico que inicia a sua aplicação lógica. Neste tutorial, o acionador que desencadeia o fluxo de trabalho está a receber um pedido através de HTTP.

1. Nos conectores e aciona a barra de pesquisa, escreva **HTTP** e bata Enter.

2. Selecione **Pedido - Quando um pedido de HTTP é recebido** como o acionador.

   ![Selecionar o acionador de pedido de HTTP](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Selecione **Utilizar o payload de exemplo para gerar esquema**.

   ![Use a carga útil da amostra para gerar um esquema](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

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

   ![Pasta amostra JSON carga útil](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Poderá receber uma notificação de pop-up com a indicação, **Lembre-se de incluir um cabeçalho Content-Type definido para application/json no seu pedido.** Pode ignorar esta sugestão com segurança e avançar para a secção seguinte.

### <a name="create-a-condition"></a>Criar uma condição

No seu fluxo de trabalho de aplicativos de lógica, as condições ajudam a executar ações específicas depois de passar essa condição específica. Uma vez que a condição é satisfeita, uma ação desejada pode ser definida. Para este tutorial, a condição é verificar se o EventType está ligado ao dispositivo ou se o dispositivo está desligado. A ação será executar o procedimento armazenado na sua base de dados.

1. Selecione **+ Novo passo** em **seguida, incorporado, em** seguida, encontrar e selecionar **Condição**. Clique em **Escolher um valor** e aparecerá uma caixa mostrando o conteúdo Dynamic -- os campos que podem ser selecionados. Preencha os campos como mostrado abaixo para apenas executá-lo para eventos ligados ao dispositivo e desligados do dispositivo:

   * Escolha um valor: **eventType** -- selecione isto a partir dos campos no conteúdo dinâmico que aparecem quando clica neste campo.
   * A mudança "é igual" a **termina com**.
   * Escolha um valor: **néctado**.

     ![Condição de preenchimento](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. No **diálogo se verdadeiro,** clique em **Adicionar uma ação**.
  
   ![Adicione ação se for verdade](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Pesquisa rumo ao Cosmos DB e selecione **Azure Cosmos DB - Execute o procedimento armazenado**

   ![Pesquisa rumo ao CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Preencha **a ligação cosmosdb** para o **Nome de Ligação** e selecione a entrada na tabela e, em seguida, selecione **Criar**. Vê o painel **de procedimentos do Executo.** Insira os valores para os campos:

   **ID da base de dados**: ToDoList

   **ID de coleção**: Itens

   **Sproc ID**: Últimas Ligações deDevicion

5. **Selecione Adicionar novo parâmetro**. No dropdown que aparece, verifique as caixas ao lado da chave de **partição** e **parâmetros para o procedimento armazenado,** em seguida, clique em qualquer outro lugar no ecrã; adiciona um campo para o valor da chave de partição e um campo para parâmetros para o procedimento armazenado.

   ![A screenshot mostra um item de procedimento de execução armazenado com adicionar novo parâmetro selecionado.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Introduza agora o valor e parâmetros da chave de partição, como mostrado abaixo. Certifique-se de colocar os suportes e as cotações duplas, como mostrado. Pode ter de clicar **em Adicionar conteúdo dinâmico** para obter os valores válidos que pode utilizar aqui.

   ![A screenshot mostra um item de procedimento de execução armazenado com parâmetros introduzidos.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Na parte superior do painel onde diz **Para Cada um**, em **Selecione uma saída dos passos anteriores**, certifique-se de que  **o Corpo** está selecionado.

   ![povoar app lógica para cada](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Guarde a sua aplicação lógica.

### <a name="copy-the-http-url"></a>Copiar o URL de HTTP

Antes de sair do Logic Apps Designer, copie o URL que a sua aplicação lógica está a ouvir para um gatilho. Este URL é utilizado para configurar o Event Grid.

1. Expanda a caixa de configuração do acionador **Quando um pedido de HTTP é recebido** ao clicar nela.

2. Copie o valor de **URL do HTTP POST** ao selecionar o botão para copiar junto do mesmo.

   ![Copiar o URL do HTTP POST](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Guarde este URL de modo a poder referenciá-lo na secção seguinte.

## <a name="configure-subscription-for-iot-hub-events"></a>Configurar a subscrição de eventos do Hub IoT

Nesta secção, vai configurar o Hub IoT para publicar eventos à medida que estes ocorrem.

1. No portal do Azure, navegue para o seu hub IoT.

2. Selecione **Eventos**.

   ![Abrir os detalhes do Event Grid](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Selecione **+ Subscrição do evento**.

   ![Criar nova subscrição de evento](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Preencha **os detalhes da subscrição do evento**: Forneça um nome descritivo e selecione o Esquema de Grelha de **Eventos**.

5. Preencha os campos **'Tipos de** Eventos'. Na lista de suspensos, selecione **apenas dispositivo ligado** e **dispositivo desligado** do menu. Clique em qualquer outro lugar do ecrã para fechar a lista e guardar as suas seleções.

   ![Definir tipos de eventos para procurar](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. Para **detalhes de ponto final**, selecione Endpoint Type como Web **Hook** e clique no ponto final selecionado e cole o URL que copiou da sua aplicação lógica e confirme a seleção.

   ![Selecione url de ponto final](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. O formulário deve agora ser semelhante ao seguinte exemplo:

   ![Formulário de exemplo de subscrição de evento](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Selecione **Criar** para guardar a subscrição de eventos.

## <a name="observe-events"></a>Observar eventos

Agora que a subscrição do seu evento está configurada, vamos testar ligando um dispositivo.

### <a name="register-a-device-in-iot-hub"></a>Registar um dispositivo no IoT Hub

1. A partir do seu hub IoT, selecione **Dispositivos IoT**.

2. **Selecione +Adicione** na parte superior do painel.

3. Em **ID do Dispositivo**, introduza `Demo-Device-1`.

4. Selecione **Guardar**.

5. Pode adicionar vários dispositivos com diferentes IDs de dispositivo.

   ![Dispositivos adicionados ao hub](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Clique novamente no dispositivo; agora as cordas e as teclas de ligação serão preenchidas. Copie a **cadeia de ligação - chave primária** para utilização posterior.

   ![Ligação Adestamos para dispositivo](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Iniciar simulador raspberry Pi

Vamos usar o simulador da Raspberry Pi para simular a ligação do dispositivo.

[Iniciar simulador raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Executar uma aplicação de amostra no simulador web Raspberry Pi

Isto irá desencadear um evento ligado ao dispositivo.

1. Na área de codificação, substitua o espaço reservado na Linha 15 pela sua cadeia de ligação do dispositivo Azure IoT Hub que guardou no final da secção anterior.

   ![Pasta na cadeia de ligação do dispositivo](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Executar a aplicação selecionando **Run**.

Vê algo semelhante à seguinte saída que mostra os dados do sensor e as mensagens que são enviadas para o seu hub IoT.

   ![Executar a aplicação](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Clique **em Stop** para parar o simulador e desencadeie um evento desligado do **dispositivo.**

Agora executou uma aplicação de amostra para recolher dados de sensores e enviá-lo para o seu hub IoT.

### <a name="observe-events-in-cosmos-db"></a>Observar eventos em Cosmos DB

Pode ver os resultados do procedimento armazenado executado no seu documento Cosmos DB. O aspeto da janela é o seguinte: Cada linha contém o estado de ligação do dispositivo mais recente por dispositivo.

   ![Como o resultado](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

Em vez de utilizar o [portal Azure,](https://portal.azure.com)pode realizar os passos do Hub IoT utilizando o Azure CLI. Para mais detalhes, consulte as páginas do Azure CLI para [criar uma subscrição de eventos](/cli/azure/eventgrid/event-subscription) e [criar um dispositivo IoT](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create).

## <a name="clean-up-resources"></a>Limpar os recursos

Este tutorial utilizou recursos que incorrem em custos na sua subscrição do Azure. Quando terminar de experimentar o tutorial e testar os seus resultados, desative ou elimine recursos que não quer manter.

Se não quiser perder o trabalho realizado na sua aplicação lógica, desative-a em vez de a eliminar.

1. Navegue até à sua aplicação lógica.

2. Na lâmina **'Vista Geral',** selecione **Eliminar** ou **Desativar**.

    Cada subscrição pode ter um hub IoT gratuito. Se tiver criado um hub gratuito para este tutorial, não precisa de o eliminar para evitar custos.

3. Navegue até ao seu hub IoT.

4. Na lâmina **'Vista Geral',** selecione **Delete**.

    Mesmo que decida manter o hub IoT, poderá ser conveniente eliminar a subscrição de evento que criou.

5. No hub IoT, selecione **Event Grid**.

6. Selecione a subscrição de evento que pretende remover.

7. Selecione **Eliminar**.

Para remover uma conta DB do Azure Cosmos do portal Azure, clique com o nome da conta e clique na **conta Eliminar**. Consulte instruções detalhadas para [a eliminação de uma conta DB da Azure Cosmos](../cosmos-db/how-to-manage-database-account.md).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [reagir a eventos IoT Hub usando a Grade de Eventos para desencadear ações](../iot-hub/iot-hub-event-grid.md)

* [Experimente o tutorial de eventos IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Saiba o que mais pode fazer com [a Grade de Eventos](../event-grid/overview.md)