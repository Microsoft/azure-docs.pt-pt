---
title: 'Tutorial: Implementar análise espacial IoT | Microsoft Azure Maps'
description: Tutorial sobre como integrar o IoT Hub com apIs de serviço microsoft Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: a3481830a09b183213e84490b5300f2fb38f8d19
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625069"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>Tutorial: Implementar análises espaciais IoT utilizando mapas Azure

Num cenário IoT, é comum capturar e rastrear eventos relevantes que ocorrem no espaço e no tempo. Exemplos incluem gestão de frotas, rastreio de ativos, mobilidade e aplicações inteligentes da cidade. Este tutorial guia-o através de uma solução que rastreia o movimento de aluguer de carros usados usando as APIs do Azure Maps.

Neste tutorial você:

> [!div class="checklist"]
> * Crie uma conta de armazenamento Azure para registar dados de rastreamento de carros.
> * Faça o upload de uma geofence para o serviço de dados Azure Maps (Preview) utilizando a API de upload de dados.
> * Crie um hub no Azure IoT Hub e registe um dispositivo.
> * Criar uma função em Funções Azure, implementando lógica de negócio baseada na análise espacial Azure Maps.
> * Subscreva os eventos de telemetria do dispositivo IoT da função Azure via Azure Event Grid.
> * Filtrar os eventos de telemetria utilizando o encaminhamento de mensagens IoT Hub.

## <a name="prerequisites"></a>Pré-requisitos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. [Criar uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição. Para mais informações, consulte [a autenticação em Azure Maps.](how-to-manage-authentication.md)

4. [Criar um grupo de recursos.](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) Neste tutorial, vamos nomear o nosso grupo de recursos *ContosoRental,* mas pode escolher o nome que quiser.

5. Descarregue o [projeto De AluguerCarSimulation C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

Este tutorial usa a aplicação [Do Carteiro,](https://www.postman.com/) mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="use-case-rental-car-tracking"></a>Caso de utilização: rastreio de carro alugado

Digamos que uma empresa de aluguer de carros quer registar informações de localização, distância percorrida, e correr o estado para seus carros alugados. A empresa também quer armazenar esta informação sempre que um carro sai da região geográfica autorizada correta.

Os carros de aluguer estão equipados com dispositivos IoT que enviam regularmente dados de telemetria para o IoT Hub. A telemetria inclui a localização atual e indica se o motor do carro está em funcionamento. O esquema de localização do dispositivo adere ao esquema IoT [Plug and Play para dados geoespaciais](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). O esquema de telemetria do dispositivo de aluguer do carro parece o seguinte código JSON:

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

Neste tutorial, só se rastreia um veículo. Depois de configurar os serviços Azure, precisa de descarregar o [projeto de aluguerCarSimulation C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) para executar o simulador do veículo. Todo o processo, desde o evento até à execução de funções, é resumido nos seguintes passos:

1. O dispositivo no veículo envia dados de telemetria para o IoT Hub.

2. Se o motor do carro estiver em funcionamento, o hub publica os dados de telemetria à Grade de Eventos.

3. Uma função Azure é desencadeada devido à sua subscrição de eventos para eventos de telemetria do dispositivo.

4. A função regista as coordenadas de localização do dispositivo do veículo, a hora do evento e o ID do dispositivo. Em seguida, utiliza o [Geofence Espacial Get API](/rest/api/maps/spatial/getgeofence) para determinar se o carro saiu da geofência. Se tiver viajado para fora dos limites de geofência, a função armazena os dados de localização recebidos do evento num recipiente de bolhas. A função também consulta o [Endereço de Pesquisa Reverso](/rest/api/maps/search/getsearchaddressreverse) para traduzir a localização da coordenada para um endereço de rua, e armazena-o com o resto dos dados de localização do dispositivo.

O diagrama seguinte mostra uma visão geral de alto nível do sistema.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Diagrama de visão geral do sistema.":::

A figura a seguir destaca a área de geofência em azul. O percurso do carro alugado é indicado por uma linha verde.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Figura mostrando rota de geofência.":::

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

Para armazenar dados de rastreio de violação de carro, crie uma [conta de armazenamento v2 para fins gerais](../storage/common/storage-account-overview.md#general-purpose-v2-accounts) no seu grupo de recursos. Se não criou um grupo de recursos, siga as instruções para [criar um grupo de recursos.](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) Neste tutorial, você vai nomear o seu grupo de recursos *ContosoRental.*

Para criar uma conta de armazenamento, siga as instruções na [criação de uma conta de armazenamento](../storage/common/storage-account-create.md?tabs=azure-portal). Neste tutorial, nomeie a conta de *armazenamento,* mas em geral pode nomeá-la como quiser.

Quando criar com sucesso a sua conta de armazenamento, terá de criar um recipiente para armazenar dados de registo.

1. Vá para a sua conta de armazenamento recém-criada. Na secção **Essentials,** selecione a ligação **Contentores.**

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Screenshot de recipientes para armazenamento de bolhas.":::

2. No canto superior esquerdo, selecione **+ Recipiente**. Um painel aparece no lado direito do navegador. Nomeie o seu contentor *contoso-rental-logs* e **selecione Criar**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Screenshot de criar um recipiente de bolhas.":::

3. Vá ao painel **de chaves Access** na sua conta de armazenamento e copie o nome da conta de **Armazenamento** e o valor **chave** na secção **key1.** Precisa de ambos estes valores na secção "Criar uma Função Azure" e adicionar uma subscrição de Grade de Eventos.

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Screenshot do nome e chave da conta de armazenamento de cópia.":::

## <a name="upload-a-geofence"></a>Faça upload de uma geofence

Em seguida, use a [aplicação Do Carteiro](https://www.getpostman.com) para [carregar o geofence](./geofence-geojson.md) para Azure Maps. A geofência define a área geográfica autorizada para o nosso veículo de aluguer. Você vai usar a geo-confesca na sua função Azure para determinar se um carro se moveu para fora da área de geofência.

Siga estes passos para carregar a geofence utilizando a API de upload de dados do Azure Maps: 

1. Abra a aplicação Do Carteiro e selecione **New**. Na janela **Criar Nova,** selecione **Coleção**. Nomeie a coleção e selecione **Criar**.

2. Para criar o pedido, selecione **New** novamente. Na janela **Criar Novo,** selecione **'Pedido'** e introduza um nome de pedido para o pedido. Selecione a coleção criada no passo anterior e, em seguida, **selecione Guardar**.

3. Selecione o método **POST** HTTP no separador construtor e introduza o seguinte URL para carregar o geofence para a API de upload de dados. Certifique-se de que substitui `{subscription-key}` a sua chave de subscrição primária.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Na trajetória URL, o `geojson` valor contra o parâmetro representa o formato dos `dataFormat` dados que estão a ser carregados.

4. Selecione **Body**  >  **raw** para o formato de entrada e escolha **JSON** na lista de drop-down. [Abra o ficheiro de dados JSON](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)e copie o JSON na secção do corpo. Selecione **Enviar**.

5. Selecione **Enviar** e aguardar o pedido para processar. Após o pedido concluído, vá ao **separador Headers** da resposta. Copie o valor da chave **localização,** que é a `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Para verificar o estado da chamada da API, crie um pedido **GET** HTTP sobre o `status URL` . Terá de anexar a chave de subscrição primária do URL para autenticação. O pedido **GET** deve gostar do seguinte URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}
   ```
   
7. Quando o pedido **GET** HTTP termina com sucesso, retorna a `resourceLocation` . Contém `resourceLocation` o único para o conteúdo `udid` carregado. Copie isto `udid` para posterior utilização neste tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-iot-hub"></a>Criar um hub IoT

O IoT Hub permite uma comunicação bidirecional segura e fiável entre uma aplicação IoT e os dispositivos que gere. Para este tutorial, você deseja obter informações do seu dispositivo no veículo para determinar a localização do carro alugado. Nesta secção, você cria um hub IoT dentro do grupo de recursos *ContosoRental.* Este hub será responsável pela publicação dos eventos de telemetria do seu dispositivo.

> [!NOTE]
> A capacidade de publicar eventos de telemetria de dispositivos na Grade de Eventos está atualmente em pré-visualização. Esta funcionalidade está disponível em todas as regiões, exceto nas seguintes: Eua Oriental, EUA Ocidentais, Europa Ocidental, Governo Azure, Azure China 21Vianet e Azure Germany.

Para criar um hub IoT no grupo de recursos *ContosoRental,* siga os passos na [criação de um hub IoT](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).

## <a name="register-a-device-in-your-iot-hub"></a>Registe um dispositivo no seu hub IoT

Os dispositivos não podem ligar-se ao hub IoT a menos que estejam registados no registo de identidade do hub IoT. Aqui, irá criar um único dispositivo com o nome *InVehicleDevice*. Para criar e registar o dispositivo dentro do seu hub IoT, siga os passos para [registar um novo dispositivo no hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub). Certifique-se de que copia a cadeia de ligação primária do seu dispositivo. Precisará dela mais tarde.

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>Crie uma função e adicione uma subscrição de Grade de Eventos

A azure Functions é um serviço de computação sem servidor que lhe permite executar pequenas peças de código ("funções"), sem a necessidade de provisões ou gestão explícitas de infraestruturas de computação. Para saber mais, consulte [as Funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview).

Uma função é desencadeada por um certo evento. Aqui, irá criar uma função que é desencadeada por um gatilho da Grade de Eventos. Crie a relação entre o gatilho e a função criando uma subscrição de eventos para eventos de telemetria de dispositivo IoT Hub. Quando ocorre um evento de telemetria do dispositivo, a sua função é chamada de ponto final e recebe os dados relevantes para o dispositivo que registou anteriormente no IoT Hub.

Aqui está o código de [script C# que a sua função irá conter](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Agora, estabeleça a sua função Azure.

1. No painel de instrumentos do portal Azure, **selecione Criar um recurso**. Tipo **De função** na caixa de texto de pesquisa. Selecione **a aplicação de funções**  >  **Criar**.

1. Na página de criação **da Aplicação de Função,** nomeie a sua aplicação de função. No **Grupo de Recursos,** selecione **ContosoRental** da lista de drop-down. Selecione **.NET Core** como a **Pilha de Tempo de Execução**. Na parte inferior da página, selecione **Seguinte: Hospedagem >**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Screenshot de criar uma aplicação de função.":::

1. Para **a conta de Armazenamento**, selecione a conta de armazenamento que criou na Criar uma conta de armazenamento [Azure](#create-an-azure-storage-account). Selecione **Rever + criar**.

1. Reveja os detalhes da aplicação de funções e **selecione Criar**.

1. Após a criação da aplicação, adiciona-lhe uma função. Vá ao aplicativo de função. Selecione o painel **de funções.** No topo da página, selecione **+ Adicionar**. Aparece o painel do modelo de função. Percorra o painel e selecione **o gatilho da grelha de eventos Azure**.

     >[!IMPORTANT]
    > O **Azure Event Hub Trigger** e os modelos **Azure Event Grid Trigger** têm nomes semelhantes. Certifique-se de que seleciona o modelo **Azure Event Grid Trigger.**

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Screenshot de criar uma função.":::

1. Dê um nome à função. Neste tutorial, você usará o nome *GetGeoFunction,* mas em geral você pode usar qualquer nome que você quiser. Selecione **Criar função**.

1. No menu esquerdo, selecione o **painel Code + Test.** Copie e cole o [script C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) na janela de código.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Copiar/screenshot do código de pasta na janela da função.":::

1. No código C#, substitua os seguintes parâmetros:
    * Substitua **SUBSCRIPTION_KEY** pela chave de subscrição primária da conta Azure Maps.
    * Substitua o **UDID** pela `udid` geofence que carregou no [Upload a geofence](#upload-a-geofence).
    * A `CreateBlobAsync` função no script cria uma bolha por evento na conta de armazenamento de dados. Substitua o **ACCESS_KEY**, **ACCOUNT_NAME**, e **STORAGE_CONTAINER_NAME** pela chave de acesso da sua conta de armazenamento, nome da conta e recipiente de armazenamento de dados. Estes valores foram gerados quando criou a sua conta de armazenamento na [Create a azure storage account](#create-an-azure-storage-account).

1. No menu esquerdo, selecione o painel **de integração.** Selecione **O Gatilho da grelha de eventos** no diagrama. Digite um nome para o gatilho, *eventGridEvent,* e selecione **a subscrição Create Event Grid**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Screenshot da subscrição de evento de adicionar.":::

1. Preencha os detalhes da subscrição. Nomeie a subscrição do evento. Para **o Esquema de Eventos**, selecione Event Grid **Schema**. Para **tipos de tópicos**, selecione **Contas Azure IoT Hub**. Para **o Grupo de Recursos,** selecione o grupo de recursos que criou no início deste tutorial. Para **recurso**, selecione o hub IoT que criou em "Create a Azure IoT hub". Para **filtrar para tipos de eventos**, selecione **Telemetria do Dispositivo**.

   Depois de escolher estas opções, verá a alteração do **Tipo Tópico** para **IoT Hub.** Para **o Nome tópico do sistema,** pode utilizar o mesmo nome que o seu recurso. Finalmente, na secção de detalhes do **Ponto Final,** **selecione Selecione um ponto final**. Aceite todas as definições e **selecione Confirmar Seleção**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Screenshot da assinatura do evento de criação.":::

1. Reveja as suas definições. Certifique-se de que o ponto final especifica a função criada no início desta secção. Selecione **Criar**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Screenshot da criação de confirmação de subscrição de eventos.":::

1. Agora estás de volta ao painel **editar.** Selecione **Guardar**.

## <a name="filter-events-by-using-iot-hub-message-routing"></a>Filtrar eventos utilizando o encaminhamento de mensagens IoT Hub

Quando adiciona uma subscrição de Grade de Eventos à função Azure, uma rota de mensagens é automaticamente criada no hub IoT especificado. O encaminhamento de mensagens permite-lhe encaminhar diferentes tipos de dados para vários pontos finais. Por exemplo, pode encaminhar mensagens de telemetria do dispositivo, eventos de ciclo de vida do dispositivo e eventos de mudança de dois dispositivos. Para obter mais informações, consulte [o encaminhamento de mensagens IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Screenshot do encaminhamento de mensagens no hub IoT.":::

No seu cenário de exemplo, só pretende receber mensagens quando o carro alugado está em movimento. Crie uma consulta de encaminhamento para filtrar os eventos em que a `Engine` propriedade é igual a **"ON".** Para criar uma consulta de encaminhamento, selecione a rota **RouteToEventGrid** e substitua a **consulta de encaminhamento** por **"Engine='ON'"**. Em seguida, selecione **Guardar**. Agora, o hub IoT só publica telemetria do dispositivo onde o motor está ligado.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Screenshot de mensagens de encaminhamento de filtros.":::

>[!TIP]
>Existem várias formas de consultar mensagens IoT dispositivo-nuvem. Para saber mais sobre a sintaxe de encaminhamento de mensagens, consulte o [encaminhamento de mensagens IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax).

## <a name="send-telemetry-data-to-iot-hub"></a>Enviar dados de telemetria para o IoT Hub

Quando a sua função Azure estiver em funcionamento, pode agora enviar dados de telemetria para o hub IoT, que o encaminhará para a Grade de Eventos. Utilize uma aplicação C# para simular dados de localização para um dispositivo no veículo de um carro alugado. Para executar a aplicação, precisa do .NET Core SDK 2.1.0 ou mais tarde no seu computador de desenvolvimento. Siga estes passos para enviar dados de telemetria simulados para o hub IoT:

1. Se ainda não o fez, faça o download do projeto [RentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C#.

2. Abra o `simulatedCar.cs` ficheiro num editor de texto à sua escolha e substitua o valor do produto pelo que `connectionString` guardou quando registou o dispositivo. Guarde as alterações no ficheiro.

3. Certifique-se de que tem .NET Core instalado na sua máquina. Na janela do terminal local, vá à pasta raiz do projeto C# e execute o seguinte comando para instalar as embalagens necessárias para aplicação simulada do dispositivo:

    ```cmd/sh
    dotnet restore
    ```

4. No mesmo terminal, executar o seguinte comando para construir e executar a aplicação de simulação de carro de aluguer:

    ```cmd/sh
    dotnet run
    ```


  O seu terminal local deve parecer o de baixo.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Screenshot da saída do terminal.":::

Se abrir o recipiente de armazenamento de bolhas agora, pode ver quatro bolhas para locais onde o veículo estava fora da geofence.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Screenshot de bolhas de visão dentro do recipiente.":::

O mapa que se segue mostra quatro pontos de localização do veículo fora da geofence. Cada local foi registado em intervalos de tempo regulares.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Screenshot do mapa de violação.":::

## <a name="explore-azure-maps-and-iot"></a>Explore Azure Maps e IoT

Para explorar as APIs Azure Maps utilizadas neste tutorial, consulte:

* [Obter Endereço de Pesquisa Ao Contrário](/rest/api/maps/search/getsearchaddressreverse)
* [Obter Geofence](/rest/api/maps/spatial/getgeofence)

Para obter uma lista completa das APIs de REST do Azure Maps, consulte:

* [Azure Maps REST APIs](/rest/api/maps/spatial/getgeofence)

* [IoT Plug and Play](../iot-pnp/index.yml)

Para obter uma lista de dispositivos certificados Azure para IoT, visite:

* [Dispositivos certificados Azure](https://catalog.azureiotsolutions.com/)

## <a name="clean-up-resources"></a>Limpar os recursos

Não há recursos que exijam limpeza.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como enviar telemetria dispositivo-nuvem, e ao contrário, consulte:


> [!div class="nextstepaction"]
> [Enviar telemetria a partir de um dispositivo](../iot-hub/quickstart-send-telemetry-dotnet.md)
