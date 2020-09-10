---
title: 'Tutorial: Implementar análises espaciais IoT com o Microsoft Azure Maps'
description: Integre o Hub IoT com apis do serviço Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 86ae186d3d8b131955be7d9fa2c305316dea9f00
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658437"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Tutorial: Implementar análises espaciais IoT usando mapas Azure

Num cenário IoT, é comum capturar e rastrear eventos relevantes que ocorrem no espaço e no tempo. Os cenários de exemplo incluem a gestão de frotas, rastreio de ativos, mobilidade e aplicações inteligentes da cidade. Este tutorial guia-o através de uma solução que rastreia o movimento de aluguer de carros usados usando as APIs do Azure Maps.

Neste tutorial você:

> [!div class="checklist"]
> * Crie uma conta de Armazenamento Azure para registar dados de rastreio de carros.
> * Faça o upload de uma geofence para o serviço Azure Maps Data utilizando a API de upload de dados.
> * Crie um Hub IoT e registe um dispositivo.
> * Criar uma função em Funções Azure, implementando lógica de negócio baseada na análise espacial Azure Maps.
> * Subscreva os eventos de telemetria do dispositivo IoT a partir da função Azure via Event Grid.
> * Filtrar os eventos de telemetria utilizando o encaminhamento de mensagens IoT Hub.

## <a name="prerequisites"></a>Pré-requisitos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. [Criar uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](how-to-manage-authentication.md)

4. [Criar um grupo de recursos.](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups) Neste tutorial, vamos nomear o nosso grupo de recursos *ContosoRental,* mas pode escolher o nome que quiser.

5. Descarregue o [projeto De AluguerCarSimulation C# ](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

Este tutorial usa a aplicação [Do Carteiro,](https://www.postman.com/) mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="use-case-rental-car-tracking"></a>Caso de utilização: rastreio de carro alugado

Este tutorial demonstra o seguinte cenário: Uma empresa de aluguer de automóveis quer registar informações de localização, distância percorrida e estado de corrida para os seus carros de aluguer. Além disso, a empresa pretende armazenar esta informação sempre que um carro sai da região geográfica autorizada correta.

No nosso caso de utilização, os carros de aluguer estão equipados com dispositivos IoT que enviam regularmente dados de telemetria para o Azure IoT Hub. A telemetria inclui a localização atual e indica se o motor do carro está em funcionamento. O esquema de localização do dispositivo adere ao esquema IoT [Plug and Play para dados geoespaciais](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). O esquema de telemetria do dispositivo de aluguer do carro parece o seguinte código JSON:

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

Neste tutorial, só vamos seguir um veículo. Depois de configurarmos os serviços Azure, você precisará baixar o [projeto de aluguerCarSimulation C# ](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) para executar o simulador do veículo. Todo o processo, desde o evento até à execução de funções, é resumido nos seguintes passos:

1. O dispositivo no veículo envia dados de telemetria para o hub IoT.

2. Se o motor do carro estiver em funcionamento, o hub IoT publica os dados de telemetria para a Grelha de Eventos.

3. Uma função Azure é desencadeada devido à sua subscrição de eventos para eventos de telemetria do dispositivo.

4. A função registará as coordenadas de localização do dispositivo do veículo, a hora do evento e o ID do dispositivo. Em seguida, utilizará a [Geofence OPi espacial](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) para determinar se o carro saiu da geofência. Se tiver viajado para fora dos limites de geofência, a função armazena os dados de localização recebidos do evento no nosso recipiente blob. Além disso, a nossa função consulta a [Pesquisa de Endereço Inverso](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para traduzir a localização da coordenada para um endereço de rua, e armazená-la com o resto dos dados de localização do dispositivo.

O diagrama seguinte dá-lhe uma visão geral de alto nível do sistema.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Visão geral do sistema":::

A figura a seguir destaca a área de geofência em azul. O percurso do carro alugado é indicado por uma linha verde.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Rota da Geofência":::

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

Para armazenar dados de rastreio de violação de carros, criaremos uma [conta de armazenamento v2 de uso geral](https://docs.microsoft.com/azure/storage/common/storage-account-overview#general-purpose-v2-accounts) no seu grupo de recursos. Se não criou um grupo de recursos, siga as instruções para [criar um grupo de recursos.](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups) Neste tutorial, vamos nomear o nosso grupo de recursos *ContosoRental.*

Para criar uma conta de armazenamento, siga as instruções na [criação de uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal). Neste tutorial, estamos a nomear a conta de armazenamento, mas podes dar-lhe o nome de tudo o que *quiseres.*

Uma vez criada a sua conta de armazenamento com sucesso, precisamos de criar um recipiente para armazenar dados de registo.

1. Navegue para a sua conta de armazenamento recém-criada. Clique no link **Contentores** na secção Essencial.

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Recipientes para armazenamento de bolhas":::

2. Clique no botão **+ Recipiente** no canto superior esquerdo. Um painel será exibido no lado direito do navegador. Nomeie o seu contentor *contoso-rental-logs* e clique em **Criar**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Criar um contentor de blobs":::

3. Navegue na lâmina das **teclas Access** na sua conta de armazenamento e copie o nome da **conta de Armazenamento** e o valor **chave** na secção **key1.** Precisaremos de ambos os valores na [Função Criar um Azure e adicionar uma](#create-an-azure-function-and-add-an-event-grid-subscription) secção de subscrição de Grade de Eventos.

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Copiar nome e chave da conta de armazenamento":::

## <a name="upload-a-geofence"></a>Faça upload de uma geofence

Vamos agora usar a [aplicação Do Carteiro](https://www.getpostman.com) para [enviar o geofence](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) para o serviço Azure Maps. A geofência define a área geográfica autorizada para o nosso veículo de aluguer.  Vamos usar a geo-engenhência na nossa função Azure para determinar se um carro se moveu para fora da área de geofência.

Abra a aplicação Do Carteiro e siga os passos abaixo para carregar o geofence usando a API de upload de dados do Azure Maps.  

1. Abra a aplicação do Carteiro. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar.**

2. Para criar o pedido, selecione **New** novamente. Na janela **Criar Novo,** selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção criada no passo anterior e, em seguida, **selecione Guardar**.

3. Selecione o método **POST** HTTP no separador construtor e introduza o seguinte URL para carregar o geofence para a API de upload de dados. Certifique-se de que substitui `{subscription-key}` a sua chave de subscrição primária.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    O valor "geojson" contra o `dataFormat` parâmetro no caminho URL representa o formato dos dados que estão a ser carregados.

4. Clique em **Body** e, em seguida, selecione o formato de entrada **bruta** e escolha **JSON** como o formato de entrada da lista de drop-down. Abra o ficheiro de dados JSON [aqui](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)e copie o JSON na secção do corpo. Clique em **Enviar**.

5. Clique no botão **'Enviar'** azul e aguarde que o pedido seja processado. Assim que o pedido estiver concluído, vá ao **separador Cabeçalhos** da resposta. Copie o valor da chave **localização,** que é a `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Para verificar o estado da chamada da API, crie um pedido **GET** HTTP sobre o `status URL` . Terá de anexar a chave de subscrição primária do URL para autenticação. O pedido **GET** deve gostar do seguinte URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}

7. When the **GET** HTTP request completes successfully, it will return a `resourceLocation`. The `resourceLocation` contains the unique `udid` for the uploaded content. You'll need to copy this `udid` for later use in this tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-azure-iot-hub"></a>Criar um hub IoT do Azure

O hub Azure IoT permite uma comunicação bidirecional segura e fiável entre uma aplicação IoT e os dispositivos que gere.  No nosso cenário, queremos obter informações do nosso dispositivo no veículo para determinar a localização do carro alugado. Nesta secção, vamos criar um hub IoT dentro do grupo de recursos *ContosoRental.* O hub IoT será responsável pela publicação dos eventos de telemetria do nosso dispositivo.

> [!NOTE]
> A funcionalidade do hub IoT para publicar eventos de telemetria de dispositivos na Grade de Eventos está em pré-visualização pública. As funcionalidades de pré-visualização públicas estão disponíveis em todas as regiões, exceto **nos EUA, Eua Ocidental, Europa Ocidental, Governo Azure, Azure China 21Vianet** e **Azure Germany.**

Para criar um hub Iot no grupo de recursos *ContosoRental,* siga os passos na [criação de um hub IoT.](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)

## <a name="register-a-device-in-iot-hub"></a>Registar um dispositivo no hub IoT

Os dispositivos não podem ligar-se ao hub IoT a menos que estejam registados no registo de identidade do hub IoT. No nosso cenário, criaremos um único dispositivo com o *nome, InVehicleDevice.* Para criar e registar o dispositivo dentro do hub IoT, siga os passos para [registar um novo dispositivo no hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub). Certifique-se de copiar a cadeia de **ligação primária** do seu dispositivo, pois iremos usá-lo num passo posterior.

## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Crie uma Função Azure e adicione uma subscrição de Grade de Eventos

A azure Functions é um serviço de computação sem servidor, que lhe permite executar pequenas peças de código ("funções"), sem a necessidade de provisões ou gestão explícitas de infraestruturas de computação. Para saber mais sobre as Funções Azure, consulte a documentação [das funções Azure.](https://docs.microsoft.com/azure/azure-functions/functions-overview)

Uma função é "desencadeada" por um determinado evento. No nosso cenário, criaremos uma função que é desencadeada por um Trigger de Grelha de Evento. Criamos a relação entre o gatilho e a função criando uma subscrição de eventos para eventos de telemetria de dispositivo ioT. Quando ocorrer um evento de telemetria do dispositivo, a nossa função será chamada de ponto final e receberá os dados relevantes para o [dispositivo que anteriormente registámos no hub IoT](#register-a-device-in-iot-hub).

O código de script C# que a nossa função irá conter pode ser visto [aqui](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Agora, vamos preparar a nossa função Azure.

1. No painel de instrumentos do portal Azure, clique em **Criar um recurso**. Tipo **De função** na caixa de texto de pesquisa. Clique na **App de Função**. Clique em **Criar**.

2. Na página de criação **da Aplicação de Função,** nomeie a sua aplicação de função. No **Grupo de Recursos,** selecione *ContosoRental* da lista de drop-down.  Selecione *.NET Core* como a **Pilha de Tempo de Execução**. Clique **em seguida: hospedar >** na parte inferior da página.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Criar uma aplicação de funções":::

3. Para **a conta de Armazenamento**, selecione a conta de armazenamento que criou na Criar uma conta de armazenamento [Azure](#create-an-azure-storage-account). Clique em **Rever + criar**.

4. Reveja os detalhes da aplicação de funções e clique em **Criar**.

5. Assim que a aplicação for criada, adicionaremos uma função. Vá ao aplicativo de função. Clique na lâmina **funções.** Clique em **+ Adicione** no topo da página. O painel do modelo de função será exibido. Desloque-se para baixo no painel. Clique no **gatilho da grelha de eventos Azure**.

     >[!WARNING]
    > O **Azure Event Hub Trigger** e os modelos **Azure Event Grid Trigger** têm nomes semelhantes. Certifique-se de clicar no modelo **Azure Event Grid Trigger.**

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Criar uma função":::

6. Dê um nome à função. Neste tutorial, usaremos o nome *GetGeoFunction,* mas pode usar o nome que quiser. Clique **na função Criar**.

7. Clique na lâmina **Code + Test** no menu da esquerda. Copie e cole o [script C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) na janela de código.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Copiar/colar código na janela da função":::

8. No código C#, substitua os seguintes parâmetros. Clique em **Guardar**. Não clique em **Teste/Execução** ainda
    * Substitua **SUBSCRIPTION_KEY** pela chave de subscrição primária da conta Azure Maps.
    * Substitua o **UDID** pela `udid` geofence que carregou no [Upload a geofence](#upload-a-geofence).
    * A função **CreateBlobAsync** no script cria uma bolha por evento na conta de armazenamento de dados. Substitua o **ACCESS_KEY**, **ACCOUNT_NAME**, e **STORAGE_CONTAINER_NAME** pela chave de acesso da sua conta de armazenamento, nome da conta e recipiente de armazenamento de dados. Estes valores foram gerados quando criou a sua conta de armazenamento na [Create a azure storage account](#create-an-azure-storage-account).

9. Clique na lâmina **de integração** no menu da esquerda. Clique no **Gatilho da grelha de evento** no diagrama. Digite um nome para o gatilho, *eventGridEvent,* e clique na **subscrição Create Event Grid**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Adicionar subscrição de evento":::

10. Preencha os detalhes da subscrição. Nomeie a subscrição do evento. Para *o Esquema de Eventos*, selecione Event Grid *Schema*. Para **tipos de tópicos**, selecione *Contas Azure IoT Hub*. Para **o Grupo de Recursos,** selecione o grupo de recursos que criou no início deste tutorial. Para **obter recursos,** selecione o hub IoT que criou no [Create a Azure IoT hub](#create-an-azure-iot-hub). Para **filtrar para tipos de eventos**, selecione *Telemetria do Dispositivo*. Depois de escolher estas opções, verá a alteração do **Tipo Tópico** para *IoT Hub.* Para **o Nome tópico do sistema,** pode utilizar o mesmo nome que o seu recurso.  Por fim, clique em **Selecionar um ponto final** na secção detalhes do **Ponto Final.** Aceite todas as definições e clique em **Confirmar Seleção.**

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Criar subscrição de eventos":::

11. Reveja as suas definições. Certifique-se de que o ponto final especifica a função criada no início desta secção. Clique em **Criar**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Criar confirmação de subscrição de eventos":::

12. Agora estás de volta ao painel **editar.** Clique em **Guardar**.

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrar eventos usando encaminhamento de mensagens IoT Hub

Quando adiciona uma subscrição de Grade de Eventos à Função Azure, uma rota de mensagens é automaticamente criada no hub IoT especificado. O encaminhamento de mensagens permite-lhe encaminhar diferentes tipos de dados para vários pontos finais. Por exemplo, pode encaminhar mensagens de telemetria do dispositivo, eventos de ciclo de vida do dispositivo e eventos de mudança de dois dispositivos. Para saber mais sobre o encaminhamento de mensagens ioT hub, consulte [o encaminhamento de mensagens IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Encaminhamento de mensagens no hub IoT":::

No nosso cenário de exemplo, só queremos receber mensagens quando o carro alugado está em movimento. Criaremos uma consulta de encaminhamento para filtrar os eventos em que a `Engine` propriedade é igual a **"ON".** Para criar uma consulta de encaminhamento, clique na rota **RouteToEventGrid** e substitua a **consulta de encaminhamento** por **"Engine='ON'"** e clique em **Guardar**. Agora o hub IoT só publicará telemetria do dispositivo onde o motor está ligado.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Mensagens de encaminhamento de filtros":::

>[!TIP]
>Existem várias formas de consultar mensagens IoT dispositivo-nuvem, para saber mais sobre a sintaxe de encaminhamento de mensagens, ver [encaminhamento de mensagens IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax).

## <a name="send-telemetry-data-to-iot-hub"></a>Enviar dados de telemetria para o IoT Hub

Uma vez que a nossa Função Azure esteja em funcionamento, podemos agora enviar dados de telemetria para o hub IoT, que irá encaminhá-lo para a Grelha de Eventos. Vamos usar uma aplicação C# para simular dados de localização para um dispositivo no veículo de um carro alugado. Para executar a aplicação, precisa do .NET Core SDK 2.1.0 ou superior na sua máquina de desenvolvimento. Siga os passos abaixo para enviar dados simulados de telemetria para o IoT Hub.

1. Se ainda não o fez, faça o download do projeto [RentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C#.

2. Abra o ficheiro simulatedCar.cs num editor de texto à sua escolha e substitua o valor do `connectionString` que guardou quando registou o dispositivo e guarde alterações no ficheiro.

3. Certifique-se de que tem .NET Core instalado na sua máquina. Na janela do terminal local, navegue para a pasta raiz do projeto C# e execute o seguinte comando para instalar as embalagens necessárias para aplicação simulada do dispositivo:

    ```cmd/sh
    dotnet restore
    ```

4. No mesmo terminal, executar o seguinte comando para construir e executar a aplicação de simulação de carro de aluguer:

    ```cmd/sh
    dotnet run
    ```

  O seu terminal local deve parecer o de baixo.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Saída terminal":::

Se abrir o recipiente de armazenamento de bolhas agora, pode ver quatro bolhas para locais onde o veículo estava fora da geofence.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Ver bolhas dentro do recipiente":::

O mapa abaixo mostra quatro pontos de localização do veículo fora da geofence. Cada local foi registado em intervalos de tempo regulares.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Mapa de violação":::

## <a name="explore-azure-maps-and-iot"></a>Explore Azure Maps e IoT

Para explorar as APIs do Azure Maps utilizadas neste tutorial, consulte:

* [Obter Endereço de Pesquisa Ao Contrário](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Obter Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Para obter uma lista completa das APIs de REST do Azure Maps, consulte:

* [Azure Maps REST APIs](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Para saber mais sobre ioT Plug and Play, consulte:

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Para obter uma lista de dispositivos certificados Azure para IoT, visite:

* [Dispositivos certificados Azure](https://catalog.azureiotsolutions.com/)

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como enviar o dispositivo para a telemetria em nuvem e o contrário, consulte:

> [!div class="nextstepaction"]
> [Enviar telemetria a partir de um dispositivo](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
