---
title: Implementar análise espacial de IoT usando mapas do Azure | Microsoft Docs
description: Integre o Hub IoT com as APIs de serviço do Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 08/13/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 7c3f72c3ce6acfb63e682b479519dba02a9900eb
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844906"
---
# <a name="implement-iot-spatial-analytics-using-azure-maps"></a>Implementar análise espacial de IoT usando mapas do Azure

Acompanhar e capturar eventos relevantes que ocorrem em espaço e tempo é um cenário de IoT comum. Por exemplo, nos aplicativos gerenciamento de frota, acompanhamento de ativos, mobilidade e cidade inteligente. Este tutorial orienta você por meio de um padrão de solução para usar APIs do Azure Maps em relação a eventos relevantes capturados pelo Hub IoT, usando o modelo de assinatura de evento fornecido pela grade de eventos.

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

Exemplificaremos a solução para um cenário em que uma empresa de aluguel de carros planeja monitorar e registrar eventos para seus carros alugados. Muitas vezes, as empresas de aluguel de carros disparam carros para uma região geográfica específica e precisam manter o controle de suas localização enquanto elas são alugadas. Qualquer instância, que envolve um carro deixando a região geográfica designada, precisa ser registrada de forma que as políticas, as tarifas e outros aspectos comerciais possam ser tratados adequadamente.

Em nosso caso de uso, os carros de aluguel são equipados com dispositivos IoT que enviam dados de telemetria para o Hub IoT do Azure regularmente. A telemetria inclui o local atual e indica se o mecanismo do carro está em execução ou não. O esquema de localização do dispositivo adere ao [esquema de plug and Play de IOT para dados geoespaciais](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md). O esquema de telemetria do dispositivo do carro de aluguel é semelhante a:

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

A telemetria do dispositivo no veículo pode ser usada para atingir o objetivo. Nossa meta é executar regras de isolamento geográfico e acompanhar adequadamente cada vez que um evento indicando que o carro foi movido é recebido. Para fazer isso, assinaremos os eventos de telemetria do dispositivo do Hub IoT por meio da grade de eventos, para que a lógica de negócios do cliente desejada possa ser executada somente quando apropriado. Há várias maneiras de assinar a grade de eventos, neste tutorial, vamos usar Azure Functions. O Azure Functions reage a eventos publicados na grade de eventos e implementa a lógica de negócios de aluguel de carros com base na análise espacial do mapas do Azure. A função do Azure consiste em verificar se o veículo saiu da cerca geográfica e, em caso afirmativo, reunindo informações adicionais, como o endereço associado ao local atual. A função também implementa a lógica para armazenar dados de eventos significativos em um armazenamento de blobs de dados que ajuda a fornecer uma descrição precisa das circunstâncias do evento para o analista de aluguel de carro, bem como para o cliente de aluguel.

O diagrama a seguir fornece uma visão geral de alto nível do sistema.

 
  <center>

  ![Visão geral do sistema](./media/tutorial-iot-hub-maps/system-diagram.png)</center>

A figura a seguir representa a área de limite geodestacada em azul e a rota do veículo de aluguel como uma linha verde.

  ![Rota de cerca geográfica](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Pré-requisitos 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para concluir as etapas deste tutorial, primeiro você precisa criar um grupo de recursos no portal do Azure. Para criar um grupo de recursos, siga as etapas abaixo:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Selecione **grupos de recursos**.
    
   ![Grupos de recursos](./media/tutorial-iot-hub-maps/resource-group.png)

3. Em grupos de recursos, selecione **Adicionar**.
    
   ![Adicionar grupo de recursos](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Insira os seguintes valores de propriedade:
    * **Scriçõe** Selecione sua assinatura do Azure.
    * **Grupo de recursos:** Insira "ContosoRental" como o nome do grupo de recursos.
    * **Regionais** Selecione uma região para o grupo de recursos.  

    ![Detalhes do grupo de recursos](./media/tutorial-iot-hub-maps/resource-details.png)

    Clique em **revisão + criar** e escolha **criar** na próxima página.

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps 

Para implementar a lógica de negócios com base na análise espacial do Azure Maps, precisamos criar uma conta do Azure Maps no grupo de recursos que criamos. Siga as instruções em [gerenciar conta](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys) para criar uma assinatura de conta do Azure Maps com o tipo de preço S1 e consulte os [detalhes de autenticação](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication#view-authentication-details) para saber como obter sua chave de assinatura.


### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para registrar dados de evento, criaremos uma conta de **v2storage** de uso geral no grupo de recursos "ContosoRental" para armazenar dados como BLOBs. Para criar uma conta de armazenamento, siga a instrução em [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal). Em seguida, precisaremos criar um contêiner para armazenar blobs. Siga as etapas abaixo para fazer isso:

1. Em sua conta de armazenamento, navegue até BLOBs.

    ![BLOBs](./media/tutorial-iot-hub-maps/blobs.png)

2. Clique no botão contêiner na parte superior esquerda e nomeie o contêiner "contoso-aluguel-logs" e clique em "OK".

    ![blob-contêiner](./media/tutorial-iot-hub-maps/blob-container.png)

3. Navegue até a folha de **chaves de acesso** em sua conta de armazenamento e copie o nome e a chave de acesso da conta. nós os usaremos mais tarde.

    ![chaves de acesso](./media/tutorial-iot-hub-maps/access-keys.png)


Agora que temos uma conta de armazenamento e um contêiner para registrar dados de evento, em seguida criaremos um hub IoT.

### <a name="create-an-iot-hub"></a>Criar um Hub IoT

Um hub IoT é um serviço gerenciado na nuvem que atua como um hub de mensagens central para comunicação bidirecional entre um aplicativo IoT e os dispositivos que são gerenciados por ele. Para rotear mensagens de telemetria de dispositivo para uma grade de eventos, criaremos um hub IoT dentro do grupo de recursos "ContosoRental" e configuraremos uma integração de rota de mensagem onde filtraremos as mensagens com base no status do mecanismo do carro e enviaremos a telemetria do dispositivo mensagens para a grade de eventos sempre que o carro estiver sendo movido. 

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
    
    O valor "geojson" em relação `dataFormat` ao parâmetro no caminho da URL representa o formato dos dados que estão sendo carregados.

3. Clique em **params**e insira os seguintes pares de chave/valor a serem usados para a URL de solicitação post. Substitua o valor da chave de assinatura pela sua chave de assinatura do Azure Maps.
   
    ![Chave-valor params do postmaster](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Clique em **corpo** e selecione formato de entrada **bruto** e escolha **JSON (aplicativo/texto)** como o formato de entrada na lista suspensa. Abra o arquivo de dados JSON [aqui](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)e copie o JSON na seção corpo no postmaster como os dados a serem carregados e clique em **Enviar**.
    
    ![postar dados](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Examine os cabeçalhos de resposta. Após uma solicitação bem-sucedida, o cabeçalho de **local** conterá o URI de status para verificar o status atual da solicitação de upload. O URI de status será do formato a seguir. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Copie seu URI de status e acrescente `subscription-key` um parâmetro a ele com seu valor sendo sua chave de assinatura de conta do Azure Maps. O formato do URI de status deve ser semelhante ao mostrado abaixo:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Para obter o, `udId` abra uma nova guia no aplicativo de postmaster e selecione obter método http na guia Construtor e faça uma solicitação get no URI de status. Se o upload de dados tiver sido bem-sucedido, você receberá um udId no corpo da resposta. Copie o udId para uso posterior.

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

2. Na página criação do aplicativo de funções, nomeie seu aplicativo de funções, em **grupo de recursos** selecione **usar existente**e selecione "ContosoRental" na lista suspensa. Selecione ".NET Core" como a pilha de tempo de execução, em **armazenamento** selecione **existente** e selecione "contosorentaldata" na lista suspensa e clique em **criar**.
    
    ![criar aplicativo](./media/tutorial-iot-hub-maps/rental-app.png)

3. Depois que o aplicativo é criado, precisamos adicionar uma função a ele. Vá para o aplicativo de funções e clique em **nova função** para adicionar uma função, escolha **no portal** como o ambiente de desenvolvimento e selecione **continuar**.

    ![criar função](./media/tutorial-iot-hub-maps/function.png)

4. Escolha **mais modelos** e clique em **concluir e exibir modelos**. 

5. Selecione o modelo com um **gatilho de grade de eventos do Azure**. Instale extensões, se solicitado, nomeie a função e clique em **criar**.

    ![função-modelo](./media/tutorial-iot-hub-maps/eventgrid-funct.png)

6. Copie o [código c#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) em sua função e clique em **salvar**.
 
7. No script c#, substitua os seguintes parâmetros:
    * Substitua o **SUBSCRIPTION_KEY** pela chave de assinatura da conta do Azure Maps.
    * Substitua o **UDID** pelo UDID da cerca geográfica que você carregou, 
    * A função **CreateBlobAsync** no script cria um blob por evento na conta de armazenamento de dados. Substitua **ACCESS_KEY**, **ACCOUNT_NAME** e **STORAGE_CONTAINER_NAME** pela chave de acesso da sua conta de armazenamento e pelo nome da conta e pelo contêiner de armazenamento de dados.

10. Clique em **Adicionar assinatura da grade de eventos**.
    
    ![Adicionar-evento-grade](./media/tutorial-iot-hub-maps/add-egs.png)

11. Preencha os detalhes da assinatura, em **detalhes da assinatura de evento** , nomeie sua assinatura e, para esquema de evento, escolha "esquema de grade de eventos". Em **detalhes do tópico** , selecione "contas do Hub IOT do Azure" como tipo de tópico, escolha a mesma assinatura usada para criar o grupo de recursos, selecione "ContosoRental" como o "grupo de recursos" e escolha o Hub IOT criado como "recurso". Escolha **telemetria do dispositivo** como tipo de evento. Depois de escolher essas opções, você verá o "tipo de tópico" alterar para "Hub IoT" automaticamente.

    ![evento-grade-assinatura](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrar eventos usando o roteamento de mensagens do Hub IoT

Depois de adicionar uma assinatura de grade de eventos à função do Azure, agora será possível ver uma rota de mensagem padrão para a grade de eventos na folha de **Roteamento de mensagens** do Hub IOT. O roteamento de mensagens permite que você encaminhe tipos de dados diferentes, como mensagens de telemetria de dispositivo, eventos de ciclo de vida do dispositivo e eventos de alteração de dispositivos de mudança para vários pontos de extremidade. Para saber mais sobre o roteamento de mensagens do Hub IoT, confira [usar o roteamento de mensagens do Hub IOT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![Hub-ex-rota](./media/tutorial-iot-hub-maps/hub-route.png)

Em nosso cenário de exemplo, desejamos filtrar todas as mensagens nas quais o veículo de aluguel está sendo movido. Para publicar esses eventos de telemetria de dispositivo na grade de eventos, usaremos a consulta de roteamento para filtrar os eventos em `Engine` que a propriedade está **"ativada"** . Há várias maneiras de consultar mensagens do dispositivo para a nuvem do IoT, para saber mais sobre a sintaxe de roteamento de mensagens, consulte [Roteamento de mensagens do Hub IOT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Para criar uma consulta de roteamento, clique na rota **RouteToEventGrid** e substitua a **consulta de roteamento** por **"Engine =" em ""** e clique em **salvar**. Agora, o Hub IoT publicará apenas a telemetria do dispositivo onde o mecanismo está ativado.

![Hub-ex-filtro](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Enviar dados de telemetria ao Hub IoT

Depois que nossa função do Azure estiver em execução, agora enviaremos dados de telemetria para o Hub IoT, que o encaminhará para a grade de eventos. Usaremos um aplicativo c# para simular dados de localização para um dispositivo no veículo de um carro de aluguel. Para executar o aplicativo, você precisa do SDK do .NET Core 2.1.0 ou superior em seu computador de desenvolvimento. Siga as etapas abaixo para enviar dados de telemetria simulados para o Hub IoT.

1. Baixe o projeto [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) c#. 

2. Abra o arquivo simulatedCar.cs em um editor de texto de sua escolha e substitua o valor do `connectionString` pelo que você salvou quando registrou o dispositivo e salve as alterações no arquivo.
 
3. Na janela do terminal local, navegue até a pasta raiz do C# projeto e execute o seguinte comando para instalar os pacotes necessários para o aplicativo de dispositivo simulado:
    
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

Para saber mais sobre como enviar o dispositivo para a telemetria de nuvem e vice-versa, confira:

* [Send telemetry from a device](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet) (Enviar telemetria a partir de um dispositivo)
