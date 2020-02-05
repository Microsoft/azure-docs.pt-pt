---
title: 'Tutorial: implementar análise espacial de IoT | Mapas do Microsoft Azure'
description: Integrar o Hub IoT com as APIs de serviço do Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 24295e27a3b94f6960777a8704fdf448697da4e1
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987303"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Tutorial: implementar análise espacial de IoT usando mapas do Azure

Acompanhar e capturar eventos relevantes que ocorrem em espaço e tempo é um cenário de IoT comum. Os cenários exemploincluem a gestão da frota, o rastreio de ativos, a mobilidade e as aplicações inteligentes da cidade. Este tutorial guia-o através de um padrão de solução para a utilização de APIs do Azure Maps. Os eventos relevantes são capturados pelo IoT Hub, utilizando o modelo de subscrição do evento fornecido pela Event Grid.

Neste tutorial, você vai:

> [!div class="checklist"]
> * Criar um Hub IoT.
> * Carregue a área de limite geográfico no Azure Maps, serviço de dados usando a API de carregamento de dados.
> * Crie uma função no Azure Functions, implementando a lógica de negócios com base na análise espacial do Azure Maps.
> * Assine eventos de telemetria do dispositivo IoT do Azure function por meio da grade de eventos.
> * Filtre os eventos de telemetria usando o roteamento de mensagens do Hub IoT.
> * Crie uma conta de armazenamento para armazenar dados de eventos relevantes.
> * Simule um dispositivo IoT no veículo.
    

## <a name="use-case"></a>Caso de utilização

Esta solução demonstra um cenário em que uma empresa de aluguer de automóveis planeia monitorizar e registar eventos para os seus carros de aluguer. Muitas vezes, as empresas de aluguel de carros disparam carros para uma região geográfica específica e precisam manter o controle de suas localização enquanto elas são alugadas. Os casos de um carro que sai da região geográfica escolhida precisam de ser registados. O registo de dados garante que as políticas, taxas e outros aspetos do negócio seriam tratados corretamente.

Em nosso caso de uso, os carros de aluguel são equipados com dispositivos IoT que enviam dados de telemetria para o Hub IoT do Azure regularmente. A telemetria inclui a localização atual e indica se o motor do carro está em funcionamento. O esquema de localização do dispositivo adere ao [esquema de plug and Play de IOT para dados geoespaciais](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md). O esquema de telemetria do dispositivo do carro de aluguel é semelhante a:

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

Vamos usar a telemetria do dispositivo no veículo para atingir o nosso objetivo. Queremos executar regras de geofencing e responder sempre que recebemos um evento indicando que o carro se moveu. Para isso, subscreveremos os eventos de telemetria do dispositivo do IoT Hub via Event Grid. Existem várias formas de subscrever a Grelha de Eventos, neste tutorial utilizamos funções Azure. A Azure Functions reage aos eventos publicados na Grelha de Eventos. Também implementa a lógica do negócio do aluguer de automóveis, que é baseado na análise espacial do Azure Maps. O código dentro da função Azure verifica se o veículo deixou a geoveda. Se o veículo saiu da geovedação, a função Azure recolhe informações adicionais, tais como o endereço associado à localização atual. A função também implementa a lógica para armazenar dados significativos de eventos num armazenamento de dados blob que ajuda a fornecer a descrição das circunstâncias do evento. As circunstâncias do evento podem ser úteis para a empresa de aluguel de carros e o cliente de aluguel.

O diagrama a seguir fornece uma visão geral de alto nível do sistema.

 
  <center>

  ![Visão geral do sistema](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

O seguinte valor representa a área geoveda, realçada em azul, e a rota do veículo alugado, indicada por uma linha verde.

  ![Rota de cerca geográfica](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Pré-requisitos 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para concluir as etapas deste tutorial, primeiro você precisa criar um grupo de recursos no portal do Azure. Para criar um grupo de recursos, faça o seguinte:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Selecione **Grupos de recursos**.
    
   ![Grupos de recursos](./media/tutorial-iot-hub-maps/resource-group.png)

3. Em **grupos de Recursos,** selecione **Adicionar**.
    
   ![Adicionar grupo de recursos](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Insira os seguintes valores de propriedade:
    * **Subscrição:** Selecione a sua subscrição Azure.
    * **Grupo de recursos:** Insira "ContosoRental" como o nome do grupo de recursos.
    * **Região:** Selecione uma região para o grupo de recursos.  

    ![Detalhes do grupo de recursos](./media/tutorial-iot-hub-maps/resource-details.png)

    Selecione **Rever + criar,** e depois selecione **Criar** na página seguinte.

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps 

Para implementar a lógica empresarial baseada na análise espacial do Azure Maps, precisamos de criar uma conta Azure Maps no grupo de recursos que criámos. Siga as instruções em [criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma assinatura da conta do Azure Maps com o tipo de preço S1 e siga as etapas em [obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave primária da sua conta. Para obter mais informações sobre autenticação no Azure Maps, consulte [gerenciar a autenticação no Azure Maps](how-to-manage-authentication.md).



### <a name="create-a-storage-account"></a>Create a storage account

Para registar os dados do evento, criaremos uma conta **de armazenamento v2** de uso geral no grupo de recursos "ContosoRental" para armazenar dados como bolhas. Para criar uma conta de armazenamento, siga a instrução em [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal). Em seguida, precisaremos criar um contêiner para armazenar blobs. Siga as etapas abaixo para fazer isso:

1. Na sua conta de armazenamento, navegue para contentores.

    ![BLOBs](./media/tutorial-iot-hub-maps/blobs.png)

2. Clique no botão contêiner na parte superior esquerda e nomeie o contêiner "contoso-aluguel-logs" e clique em "OK".

    ![blob-contêiner](./media/tutorial-iot-hub-maps/blob-container.png)

3. Navegue para a lâmina de **chaves de acesso** na sua conta de armazenamento e copie o "nome da conta de armazenamento" e a "chave de acesso". São necessários num passo posterior.

    ![chaves de acesso](./media/tutorial-iot-hub-maps/access-keys.png)


Agora, temos uma conta de armazenamento e um recipiente para registar os dados do evento. Em seguida, criaremos um centro de ioT.

### <a name="create-an-iot-hub"></a>Criar um Hub IoT

Um IoT Hub é um serviço gerido na nuvem que funciona como um centro de mensagens central para a comunicação bidirecional entre uma aplicação IoT e os dispositivos geridos por ele. Para encaminhar mensagens de telemetria para uma Grelha de Eventos, crie um Hub IoT dentro do grupo de recursos "ContosoRental". Criar uma integração de rota de mensagens onde filtraremos mensagens com base no estado do motor do carro. Também enviaremos mensagens de telemetria do dispositivo para a Grelha de Eventos sempre que o carro estiver em movimento.

> [!Note] 
> A funcionalidade do Hub IoT para publicar eventos de telemetria de dispositivo na grade de eventos está em visualização pública. Os recursos de visualização pública estão disponíveis em todas as regiões **, exceto leste dos EUA, oeste dos EUA, Europa Ocidental, Azure governamental, Azure China 21vianet** e **Azure Alemanha**. 

Crie um hub IOT seguindo as etapas na [seção criar um hub IOT](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).


### <a name="register-a-device"></a>Registar um dispositivo 

Para se conectar ao Hub IoT, um dispositivo deve ser registrado. Para registrar um dispositivo com o Hub IoT, siga as etapas abaixo:

1. No Hub IoT, clique na folha "dispositivos IoT" e clique em "novo".

    ![Adicionar dispositivo](./media/tutorial-iot-hub-maps/add-device.png)

2. Na página criar um dispositivo, nomeie o dispositivo IoT e clique em "salvar".
    
    ![registrar-dispositivo](./media/tutorial-iot-hub-maps/register-device.png)


## <a name="upload-geofence"></a>Carregar cerca geográfica

Usaremos o aplicativo de [postmaster](https://www.getpostman.com) para [carregar a cerca geográfica](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) para o serviço do Azure Maps usando a API de carregamento de dados do Azure Maps. Qualquer evento quando o carro estiver fora desse limite geográfico será registrado.

Abra o aplicativo de postmaster e siga as etapas abaixo para carregar a cerca geográfica usando o mapas do Azure, API de carregamento de dados.  

1. No aplicativo do postmaster, clique em novo | Crie uma nova solicitação e selecione solicitar. Insira um nome de solicitação para carregar dados de limite geográfico, selecione uma coleção ou pasta para salvar e clique em salvar.

    ![Carregar limites geográficos usando o postmaster](./media/tutorial-iot-hub-maps/postman-new.png)

2. Selecione o método HTTP POST na guia Construtor e insira a URL a seguir para fazer uma solicitação POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    O valor "geojson" em relação ao parâmetro `dataFormat` no caminho da URL representa o formato dos dados que estão sendo carregados.

3. Clique em **params**e insira os seguintes pares de chave/valor a serem usados para a URL de solicitação post. Substitua o valor da chave de assinatura pela sua chave do Azure Maps.
   
    ![Chave-valor params do postmaster](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Clique em **Body** e, em seguida, selecione o formato de entrada **crua** e escolha **JSON (aplicação/texto)** como formato de entrada da lista de drop-down. Abra aqui o [ficheiro](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)de dados jSON e copie o Json na secção do corpo como os dados para carregar e clicar em **Enviar**.
    
    ![postar dados](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Examine os cabeçalhos de resposta. Após uma solicitação bem-sucedida, o cabeçalho de **local** conterá o URI de status para verificar o status atual da solicitação de upload. O URI de status será do formato a seguir. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Copie seu URI de status e acrescente um `subscription-key` parâmetro a ele com seu valor sendo sua chave de assinatura de conta do Azure Maps. O formato do URI de status deve ser semelhante ao mostrado abaixo:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Para obter o, `udId` abra uma nova guia no aplicativo de postmaster e selecione obter método HTTP na guia Construtor e faça uma solicitação GET no URI de status. Se o upload de dados tiver sido bem-sucedido, você receberá um udId no corpo da resposta. Copie o udId para uso posterior.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Em seguida, criaremos uma função do Azure dentro do grupo de recursos "ContosoRental" e, em seguida, configuraremos uma rota de mensagem no Hub IoT para filtrar as mensagens de telemetria do dispositivo.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Criar uma função do Azure e adicionar uma assinatura da grade de eventos

Azure Functions é um serviço de computação sem servidor que nos permite executar o código sob demanda, não a necessidade de provisionar ou gerenciar explicitamente a infraestrutura de computação. Para saber mais sobre Azure Functions, dê uma olhada na documentação do [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) . A lógica que implementamos na função é usar os dados de localização provenientes da telemetria do dispositivo no veículo para avaliar o status da cerca geográfica. Caso um determinado veículo fique fora do limite geográfico, a função coletará mais informações como o endereço do local por meio da [API de obtenção de endereço de pesquisa reversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) que converte uma determinada coordenada de local em um endereço de compreensível humano. Todas as informações de evento relevantes são armazenadas no repositório de BLOB. A etapa 5 abaixo aponta para o código executável que implementa essa lógica. Siga as etapas abaixo para criar uma função do Azure que envia logs de dados para o contêiner de blob na conta de armazenamento e adicione uma assinatura de grade de eventos a ele.

1. No painel portal do Azure, selecione criar um recurso. Selecione **computação** na lista de tipos de recursos disponíveis e, em seguida, selecione **aplicativo de funções**.

    ![criar-recurso](./media/tutorial-iot-hub-maps/create-resource.png)

2. Na página de criação da **App função,** nomeie a sua aplicação de funções. No **Grupo de Recursos**, selecione Use **existente**, e selecione "ContosoRental" na lista de drop-down. Selecione ".NET Core" como a Stack runtime. Em **Armazenamento**, selecione **Use os existentes,** selecione "contosorentaldata" da lista de drop-down e, em seguida, selecione **Review+Create**.
    
    ![criar aplicativo](./media/tutorial-iot-hub-maps/rental-app.png)

2. Reveja os detalhes da aplicação de função e selecione "Criar".

3. Depois que o aplicativo é criado, precisamos adicionar uma função a ele. Vá à aplicação de funções e clique em **Nova função** para adicionar uma função, escolha **o In-Portal** como o ambiente de desenvolvimento e, em seguida, selecione **Continuar**.

    ![criar função](./media/tutorial-iot-hub-maps/function.png)

4. Escolha **mais modelos** e clique em **concluir e exibir modelos**. 

5. Selecione o modelo com um **gatilho de grade de eventos do Azure**. Instale extensões se solicitado, nomeie a função e selecione **Criar**.

    ![função-modelo](./media/tutorial-iot-hub-maps/eventgrid-funct.png)

6. Copie o [código c#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) em sua função e clique em **salvar**.
 
7. No script c#, substitua os seguintes parâmetros:
    * Substitua o **SUBSCRIPTION_KEY** pela chave de subscrição primária da conta Azure Maps.
    * Substitua o **UDID** pelo UDID da cerca geográfica que você carregou, 
    * A função **CreateBlobAsync** no script cria um blob por evento na conta de armazenamento de dados. Substitua a **ACCESS_KEY,** **ACCOUNT_NAME** e **STORAGE_CONTAINER_NAME** pela chave de acesso da sua conta de armazenamento, nome da conta e recipiente de armazenamento de dados.

10. Clique em **Adicionar assinatura da grade de eventos**.
    
    ![Adicionar-evento-grade](./media/tutorial-iot-hub-maps/add-egs.png)

11. Preencha os detalhes da assinatura, em **detalhes da assinatura de evento** , nomeie sua assinatura e, para esquema de evento, escolha "esquema de grade de eventos". Em **detalhes tópicos,** selecione "Contas Hub Azure IoT" como tipo tópico. Escolha a mesma subscrição que usou para criar o grupo de recursos, selecione "ContosoRental" como "Grupo de Recursos" e escolha o IoT Hub que criou como "Recurso". Escolha **telemetria do dispositivo** como tipo de evento. Depois de escolher essas opções, você verá o "tipo de tópico" alterar para "Hub IoT" automaticamente.

    ![evento-grade-assinatura](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrar eventos usando o roteamento de mensagens do Hub IoT

Depois de adicionar uma subscrição da Grelha de Eventos à Função Azure, agora poderá ver uma rota de mensagem padrão para a Grelha de Eventos na lâmina de **encaminhamento** de mensagens do IoT Hub. O roteamento de mensagens permite que você encaminhe tipos de dados diferentes, como mensagens de telemetria de dispositivo, eventos de ciclo de vida do dispositivo e eventos de alteração de dispositivos de mudança para vários pontos de extremidade. Para saber mais sobre o roteamento de mensagens do Hub IoT, confira [usar o roteamento de mensagens do Hub IOT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![Hub-ex-rota](./media/tutorial-iot-hub-maps/hub-route.png)

Em nosso cenário de exemplo, desejamos filtrar todas as mensagens nas quais o veículo de aluguel está sendo movido. Para publicar esses eventos de telemetria de dispositivo na grade de eventos, usaremos a consulta de roteamento para filtrar os eventos em que a propriedade `Engine` está **"ativada"** . Há várias maneiras de consultar mensagens do dispositivo para a nuvem do IoT, para saber mais sobre a sintaxe de roteamento de mensagens, consulte [Roteamento de mensagens do Hub IOT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Para criar uma consulta de roteamento, clique na rota **RouteToEventGrid** e substitua a **consulta de roteamento** por **"Engine =" em ""** e clique em **salvar**. Agora, o Hub IoT publicará apenas a telemetria do dispositivo onde o mecanismo está ativado.

![Hub-ex-filtro](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Enviar dados de telemetria ao Hub IoT

Uma vez que a nossa Função Azure esteja em funcionamento, podemos agora enviar dados de telemetria para o IoT Hub, que irá encaminhá-lo para a Grelha de Eventos. Vamos usar uma aplicação c# para simular dados de localização para um dispositivo no veículo de um carro alugado. Para executar o aplicativo, você precisa do SDK do .NET Core 2.1.0 ou superior em seu computador de desenvolvimento. Siga as etapas abaixo para enviar dados de telemetria simulados para o Hub IoT.

1. Baixe o projeto [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) c#. 

2. Abra o arquivo simulatedCar.cs em um editor de texto de sua escolha e substitua o valor do `connectionString` pelo que você salvou quando registrou o dispositivo e salve as alterações no arquivo.
 
3. Certifique-se de que tem o Núcleo .NET instalado na sua máquina. Na janela do terminal local, navegue até a pasta raiz do C# projeto e execute o seguinte comando para instalar os pacotes necessários para o aplicativo de dispositivo simulado:
    
    ```cmd/sh
    dotnet restore
    ```

4. No mesmo terminal, execute o seguinte comando para compilar e executar o aplicativo de simulação de carros de aluguel:

    ```cmd/sh
    dotnet run
    ```

  O terminal local deve ser semelhante ao mostrado abaixo.

  ![Saída do terminal](./media/tutorial-iot-hub-maps/terminal.png)

Se você abrir o contêiner de armazenamento de BLOBs agora, você poderá ver quatro BLOBs para locais onde o veículo estava fora do limite geográfico.

![Inserir blob](./media/tutorial-iot-hub-maps/blob.png)

O mapa abaixo mostra quatro pontos em que o veículo estava fora da cerca geográfica, registrada em intervalos de tempo regulares.

![mapa de violação](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>Passos seguintes

Para explorar as APIs do Azure Maps usadas neste tutorial, consulte:

* [Obter reversão de endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Obter limite geográfico](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Para obter uma lista completa das APIs REST do Azure Maps, consulte:

* [APIs REST do Azure Maps](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Para saber mais sobre Plug and Play de IoT, confira:

* [Plug and Play de IoT](https://docs.microsoft.com/azure/iot-pnp)

Para obter uma lista de dispositivos que são certificados pelo Azure para IoT, visite:

* [Dispositivos do Azure Certified](https://catalog.azureiotsolutions.com/)

Para saber mais sobre como enviar o dispositivo para a telemetria em nuvem e o contrário, consulte:

* [Send telemetry from a device](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet) (Enviar telemetria a partir de um dispositivo)
