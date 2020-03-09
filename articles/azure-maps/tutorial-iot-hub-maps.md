---
title: 'Tutorial: Implementar análise espacial IoT  Microsoft Azure Maps'
description: Integre o IoT Hub com as APIs do serviço Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: f0367a195ca0aa5f26ff0819b00c50fabae1d271
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359580"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Tutorial: Implementar análise espacial IoT usando mapas azure

Num cenário ioT, é comum capturar e rastrear eventos relevantes que ocorrem no espaço e no tempo. Os cenários exemploincluem a gestão da frota, o rastreio de ativos, a mobilidade e as aplicações inteligentes da cidade. Este tutorial guia-o através de um padrão de solução utilizando as APIs do Azure Maps. Os eventos relevantes são capturados pelo IoT Hub, utilizando o modelo de subscrição do evento fornecido pela Grelha de Eventos.

Neste tutorial irá:

> [!div class="checklist"]
> * Criar um Hub IoT.
> * Faça upload da área geofence no Azure Maps, serviço de dados utilizando a API de upload de dados.
> * Criar uma função em Funções Azure, implementando a lógica empresarial baseada na análise espacial do Azure Maps.
> * Subscreva os eventos de telemetria do dispositivo IoT da função Azure via Event Grid.
> * Filtrar os eventos de telemetria utilizando o encaminhamento de mensagens IoT Hub.
> * Crie uma conta de armazenamento para armazenar dados relevantes do evento.
> * Simular um dispositivo IoT no veículo.
    

## <a name="use-case"></a>Caso de utilização

Esta solução demonstra um cenário em que uma empresa de aluguer de automóveis planeia monitorizar e registar eventos para os seus carros de aluguer. As empresas de aluguer de automóveis geralmente alugam carros para uma região geográfica específica. Precisam de localizar o paradeiro dos carros enquanto são alugados. As ocorrências de um carro que sai da região geográfica escolhida devem ser registadas. O registo de dados garante que as políticas, taxas e outros aspetos do negócio seriam tratados corretamente.

No nosso caso de utilização, os carros de aluguer estão equipados com dispositivos IoT que enviam regularmente dados de telemetria para o Azure IoT Hub. A telemetria inclui a localização atual e indica se o motor do carro está em funcionamento. O esquema de localização do dispositivo adere ao [IoT Plug e play schema para dados geoespaciais](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md). O esquema de telemetria do dispositivo de aluguer do carro parece:

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

Vamos usar a telemetria do dispositivo no veículo para atingir o nosso objetivo. Queremos executar regras de geofencing. E queremos responder sempre que recebemos um evento indicando que o carro se mexeu. Para isso, subscreveremos os eventos de telemetria do dispositivo do IoT Hub via Event Grid. 

Existem várias formas de subscrever a Grelha de Eventos, neste tutorial utilizamos funções Azure. A Azure Functions reage aos eventos publicados na Grelha de Eventos. Também implementa a lógica do negócio do aluguer de automóveis, que é baseado na análise espacial do Azure Maps. 

O código dentro da função Azure verifica se o veículo deixou a geoveda. Se o veículo saiu da geovedação, a função Azure recolhe informações adicionais, tais como o endereço associado à localização atual. A função também implementa a lógica para armazenar dados significativos de eventos num armazenamento de dados blob que ajuda a fornecer a descrição das circunstâncias do evento. 

As circunstâncias do evento podem ser úteis para a empresa de aluguel de carros e o cliente de aluguel. O diagrama que se segue dá-lhe uma visão geral de alto nível do sistema.

 
  <center>

  ![Visão geral do sistema](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

A figura que se segue representa a área geoveda realçada em azul. A rota do veículo alugado é indicada por uma linha verde.

  ![Rota geoveda](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Pré-requisitos 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Para completar os passos neste tutorial, é necessário primeiro criar um grupo de recursos no portal Azure. Para criar um grupo de recursos, faça os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **Grupos de recursos**.
    
   ![Grupos de recursos](./media/tutorial-iot-hub-maps/resource-group.png)

3. Em **grupos de Recursos,** selecione **Adicionar**.
    
   ![Adicionar grupo de recursos](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Insira os seguintes valores de propriedade:
    * **Subscrição:** Selecione a sua subscrição Azure.
    * **Grupo de recursos:** Insira "ContosoRental" como nome do grupo de recursos.
    * **Região:** Selecione uma região para o grupo de recursos.  

    ![Detalhes do grupo de recursos](./media/tutorial-iot-hub-maps/resource-details.png)

    Selecione **Rever + criar,** e depois selecione **Criar** na página seguinte.

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps 

Para implementar a lógica empresarial baseada na análise espacial do Azure Maps, precisamos de criar uma conta Azure Maps no grupo de recursos que criámos. Siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma subscrição de conta Azure Maps com nível de preços S1. Siga os passos na [chave principal](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave principal para a sua conta. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps](how-to-manage-authentication.md).



### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para registar os dados do evento, criaremos um **v2storage** de uso geral que fornece acesso a todos os serviços de Armazenamento Azure: blobs, ficheiros, filas, tabelas e discos.  Precisamos colocar esta conta de armazenamento no grupo de recursos "ContosoRental" para armazenar dados como bolhas. Para criar uma conta de armazenamento, siga as instruções para [criar uma conta](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)de armazenamento . Em seguida, precisamos criar um recipiente para armazenar bolhas. Siga os passos abaixo para fazê-lo:

1. Na sua "conta de armazenamento - blob, file, table, fila", navegar para Contentores.

    ![bolhas](./media/tutorial-iot-hub-maps/blobs.png)

2. Clique no botão do recipiente na parte superior esquerda e nomeie o seu contentor "registos de aluguer de contoso" e clique em "OK".

    ![blob-container](./media/tutorial-iot-hub-maps/blob-container.png)

3. Navegue para a lâmina de **chaves de acesso** na sua conta de armazenamento e copie o "nome da conta de armazenamento" e a "chave de acesso". São necessários num passo mais tarde.

    ![chaves de acesso](./media/tutorial-iot-hub-maps/access-keys.png)


Agora, temos uma conta de armazenamento e um recipiente para registar os dados do evento. Em seguida, criaremos um centro de iot.

### <a name="create-an-iot-hub"></a>Criar um Hub IoT

O IoT Hub é um serviço gerido na nuvem. O IoT Hub funciona como um centro de mensagens central para a comunicação bidirecional entre uma aplicação IoT e os dispositivos geridos por ele. Para encaminhar mensagens de telemetria para uma Grelha de Eventos, crie um Hub IoT dentro do grupo de recursos "ContosoRental". Criar uma integração de rota de mensagens onde filtraremos mensagens com base no estado do motor do carro. Também enviaremos mensagens de telemetria do dispositivo para a Grelha de Eventos sempre que o carro estiver em movimento.

> [!Note] 
> A funcionalidade do IoT Hub para publicar eventos de telemetria de dispositivos na Event Grid está em pré-visualização pública. As funcionalidades de pré-visualização pública estão disponíveis em todas as regiões, exceto **Leste dos EUA, Oeste dos EUA, Europa Ocidental, Governo Azure, Azure China 21Vianet** e **Azure Alemanha.** 

Crie um hub de iot seguindo os passos na [criação de uma secção IoT Hub](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).


### <a name="register-a-device"></a>Registar um dispositivo 

Para ligar ao Hub IoT, deve ser registado um dispositivo. Para registar um dispositivo com hub IoT, siga os passos abaixo:

1. No seu Hub IoT, clique na lâmina "IoT devices" e clique em "New".

    ![add-device](./media/tutorial-iot-hub-maps/add-device.png)

2. Na página de criar um dispositivo, nomeie o seu dispositivo IoT e clique em "Guardar".
    
    ![dispositivo de registo](./media/tutorial-iot-hub-maps/register-device.png)

3. Guarde a cadeia de **ligação primária** do seu dispositivo para a utilizar num passo posterior, no qual necessita de alterar um espaço reservado com esta cadeia de ligação.

    ![add-device](./media/tutorial-iot-hub-maps/connection-string.png)

## <a name="upload-geofence"></a>Upload geofence

Usaremos a [aplicação Postman](https://www.getpostman.com) para [fazer o upload da geovete](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) para o serviço Azure Maps utilizando a API de upload de dados do Azure Maps. Qualquer evento quando o carro estiver fora desta geovedação será registrado.

Abra a aplicação Postman e siga os passos abaixo para carregar a geovete usando o Azure Maps, Data Upload API.  

1. Na aplicação Postman, clique em novo / Crie novo e selecione Pedido. Introduza um nome de Pedido para enviar dados geovetes, selecione uma recolha ou pasta para guardá-lo e clique em Guardar.

    ![Upload geofences usando carteiro](./media/tutorial-iot-hub-maps/postman-new.png)

2. Selecione o método POST HTTP no separador construtor e introduza o seguinte URL para fazer um pedido de publicação.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    O valor "geojsão" face ao parâmetro `dataFormat` no caminho do URL representa o formato dos dados que estão a ser carregados.

3. Clique em **Params**e introduza os seguintes pares chave/valor a utilizar para o URL de pedido de POST. Substitua o valor da chave de subscrição pela chave Azure Maps.
   
    ![Carteiro de params de valor-chave](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Clique em **Body** e, em seguida, selecione o formato de entrada **crua** e escolha **JSON (aplicação/texto)** como formato de entrada da lista de drop-down. Abra aqui o [ficheiro](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)de dados jSON e copie o Json na secção do corpo como os dados para carregar e clicar em **Enviar**.
    
    ![publicar dados](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Reveja os cabeçalhos de resposta. Após um pedido bem sucedido, o cabeçalho de **localização** conterá o estado URI para verificar o estado atual do pedido de upload. O estado URI será do seguinte formato. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Copie o seu estado URI e alhe um parâmetro `subscription-key`. Atribua o valor da chave de subscrição da sua conta Azure Maps ao parâmetro `subscription-key`. O formato URI de estatuto deve ser como o abaixo, e `{Subscription-key}` substituído sem chave de subscrição.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Para obter o, `udId` abrir um novo separador na aplicação Postman e selecionar o método GET HTTP no separador construtor e fazer um pedido GET no estado URI. Se o seu upload de dados tiver sido bem sucedido, receberá um udId no corpo de resposta. Copie o udId para posterior utilização.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Em seguida, criaremos uma Função Azure dentro do grupo de recursos "ContosoRental" e, em seguida, criaremos uma rota de mensagem no IoT Hub para filtrar mensagens de telemetria do dispositivo.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Crie uma Função Azure e adicione uma subscrição da Grelha de Eventos

O Azure Functions é um serviço de computação sem servidores que nos permite executar código a pedido, sem a necessidade de fornecer ou gerir explicitamente a infraestrutura de cálculo. Para saber mais sobre as Funções Azure, dê uma olhada na documentação das [funções do Azure.](https://docs.microsoft.com/azure/azure-functions/functions-overview) 

A lógica que implementamos na função é usar os dados de localização provenientes da telemetria do dispositivo no veículo para avaliar o estado da geovedação. No caso de um determinado veículo sair da geovese, a função reunirá mais informações como o endereço do local através da [API Reverse Address Get](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Esta API traduz uma coordenada de localização em um endereço de rua compreensível humano. 

Todas as informações relevantes do evento são então guardadas na loja blob. O passo 5 abaixo aponta para o código executável que implementa tal lógica. Siga os passos abaixo para criar uma Função Azure que envie registos de dados para o recipiente blob na conta de armazenamento blob e adicione-lhe uma subscrição da Rede de Eventos.

1. No painel do portal Azure, selecione criar um recurso. Selecione **Compute** a partir da lista de tipos de recursos disponíveis e, em seguida, selecione **App de Funções**.

    ![criar recurso](./media/tutorial-iot-hub-maps/create-resource.png)

2. Na página de criação da **App função,** nomeie a sua aplicação de funções. No **Grupo de Recursos**, selecione Use **existente**, e selecione "ContosoRental" na lista de drop-down. Selecione ".NET Core" como a Stack runtime. Em **Hospedagem,** para **conta de armazenamento,** selecione o nome da conta de armazenamento a partir de um passo anterior. No nosso passo anterior, nomeámos a conta de armazenamento **v2storage.**  Em seguida, selecione **Review+Criar**.
    
    ![criar app](./media/tutorial-iot-hub-maps/rental-app.png)

2. Reveja os detalhes da aplicação de função e selecione "Criar".

3. Uma vez criada a aplicação, precisamos adicionar-lhe uma função. Vá ao aplicativo de funções. Clique em **Nova função** para adicionar uma função e escolha o **In-Portal** como o ambiente de desenvolvimento. Em seguida, selecione **Continuar**.

    ![criar função](./media/tutorial-iot-hub-maps/function.png)

4. Escolha **mais modelos** e clique em **Terminar e ver modelos**. 

5. Selecione o modelo com um gatilho de grelha de **evento seletiva**. Instale extensões se solicitado, nomeie a função e selecione **Criar**.

    ![modelo de função](./media/tutorial-iot-hub-maps/eventgrid-funct.png)
    
    O Gatilho do Hub de **Eventos Azure** e o Gatilho da Grelha de **Eventos Azure** têm ícones semelhantes. Certifique-se de que seleciona o gatilho da grelha de **eventos Azure**.

6. Copie [ C# ](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) o código na sua função.
 
7. No C# script, substitua os seguintes parâmetros. Clique em **Guardar**. Não clique em **Run** ainda
    * Substitua o **SUBSCRIPTION_KEY** pela chave de subscrição primária da conta Azure Maps.
    * Substitua o **UDID** pelo uídídio da geovete que carregou, 
    * A função **CreateBlobAsync** no script cria uma bolha por evento na conta de armazenamento de dados. Substitua a **ACCESS_KEY,** **ACCOUNT_NAME**e **STORAGE_CONTAINER_NAME** com a chave de acesso da sua conta de armazenamento, nome da conta e recipiente de armazenamento de dados.

10. Clique na **subscrição add Event Grid**.
    
    ![add-event-grid](./media/tutorial-iot-hub-maps/add-egs.png)

11. Preencha os detalhes da subscrição, em detalhes de subscrição do **EVENTO,** nomeie a subscrição do seu evento. Para o Evento Schema escolha "Event Grid Schema". Em **detalhes tópicos,** selecione "Contas Hub Azure IoT" como tipo tópico. Escolha a mesma subscrição que usou para criar o grupo de recursos, selecione "ContosoRental" como o "Grupo de Recursos". Escolha o IoT Hub que criou como um "Recurso". Escolha **a Telemetria** do Dispositivo como Tipo de Evento. Depois de escolher estas opções, verá a alteração "Tipo tópico" para "IoT Hub" automaticamente.

    ![assinatura de rede de eventos](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrar eventos usando o encaminhamento de mensagens IoT Hub

Depois de adicionar uma subscrição da Grelha de Eventos à Função Azure, verá uma rota de mensagem padrão para a Grelha de Eventos na lâmina de **encaminhamento** de mensagens do IoT Hub. O encaminhamento de mensagens permite-lhe direcionar diferentes tipos de dados para vários pontos finais. Por exemplo, pode encaminhar mensagens de telemetria do dispositivo, eventos de ciclo de vida do dispositivo e eventos de mudança de gémeos do dispositivo. Para saber mais sobre o encaminhamento de mensagens do hub IoT, consulte [o encaminhamento de mensagens Use IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![hub-EG-route](./media/tutorial-iot-hub-maps/hub-route.png)

No nosso cenário de exemplo, queremos filtrar todas as mensagens para onde o veículo de aluguer se move. Para publicar tais eventos de telemetria de dispositivos para Event Grid, usaremos a consulta de encaminhamento para filtrar os eventos onde a propriedade `Engine` é **"ON"** . Existem várias formas de consultar mensagens ioT dispositivo-para-nuvem, para saber mais sobre a sintaxe de encaminhamento de mensagens, ver [encaminhamento de mensagens IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Para criar uma consulta de encaminhamento, clique na rota **RouteToEventGrid** e substitua a **consulta de Encaminhamento** por **"Engine='ON'"** e clique em **Guardar**. Agora o hub IoT só publicará telemetria do dispositivo onde o motor está LIGADO.

![hub-EG-filtro](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Envie dados de telemetria para ioT Hub

Uma vez que a nossa Função Azure esteja em funcionamento, podemos agora enviar dados de telemetria para o IoT Hub, que irá encaminhá-lo para a Grelha de Eventos. Vamos usar uma C# aplicação para simular dados de localização para um dispositivo no veículo de um carro alugado. Para executar a aplicação, precisa do .NET Core SDK 2.1.0 ou superior na sua máquina de desenvolvimento. Siga os passos abaixo para enviar dados simulados de telemetria para o IoT Hub.

1. Descarregue o projeto [rentalCarSimulation.](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# 

2. Abra o ficheiro simulatedCar.cs num editor de texto à sua escolha e substitua o valor do `connectionString` pelo que guardou quando registou o dispositivo e guarde alterações no ficheiro.
 
3. Certifique-se de que tem o Núcleo .NET instalado na sua máquina. Na janela do terminal local, navegue C# até à pasta raiz do projeto e execute o seguinte comando para instalar as embalagens necessárias para aplicação simulada do dispositivo:
    
    ```cmd/sh
    dotnet restore
    ```

4. No mesmo terminal, execute o seguinte comando para construir e executar a aplicação de simulação de carro alugado:

    ```cmd/sh
    dotnet run
    ```

  O seu terminal local deve parecer o de baixo.

  ![Saída terminal](./media/tutorial-iot-hub-maps/terminal.png)

Se abrir o recipiente de armazenamento de bolhas agora, deverá poder ver quatro bolhas para locais onde o veículo estava fora da geoveda.

![Entrar blob](./media/tutorial-iot-hub-maps/blob.png)

O mapa abaixo mostra quatro pontos onde o veículo estava fora da geoveda, registado em intervalos de tempo regulares.

![mapa de violação](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>Passos seguintes

Para explorar as APIs do Azure Maps utilizadas neste tutorial, consulte:

* [Obter endereço de pesquisa invertido](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Obter Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Para obter uma lista completa de APIs REST DO Azure Maps, consulte:

* [Apis de descanso dos mapas azure](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Para saber mais sobre IoT Plug and Play, consulte:

* [Plug e Play IoT](https://docs.microsoft.com/azure/iot-pnp)

Para obter uma lista de dispositivos certificados azure para IoT, visite:

* [Dispositivos certificados azure](https://catalog.azureiotsolutions.com/)

Para saber mais sobre como enviar o dispositivo para a telemetria em nuvem e o contrário, consulte:

* [Send telemetry from a device](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet) (Enviar telemetria a partir de um dispositivo)
