---
title: 'Tutorial: Implementar análise espacial IoT / Microsoft Azure Maps'
description: Integre o Hub IoT com apis do serviço Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 2bb5876424730e55d15cc52aeb98aa04af040821
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133406"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Tutorial: Implementar análises espaciais IoT usando mapas Azure

Num cenário IoT, é comum capturar e rastrear eventos relevantes que ocorrem no espaço e no tempo. Os cenários de exemplo incluem a gestão de frotas, rastreio de ativos, mobilidade e aplicações inteligentes da cidade. Este tutorial guia-o através de um padrão de solução utilizando as APIs do Azure Maps. Os eventos relevantes são capturados pelo IoT Hub, utilizando o modelo de subscrição de eventos fornecido pela Grade de Eventos.

Neste tutorial você:

> [!div class="checklist"]
> * Criar um Hub IoT.
> * Faça upload da área de geofência no Azure Maps, serviço de dados utilizando a API de upload de dados.
> * Criar uma função em Funções Azure, implementando lógica de negócio baseada na análise espacial Azure Maps.
> * Subscreva os eventos de telemetria do dispositivo IoT a partir da função Azure via Event Grid.
> * Filtrar os eventos de telemetria utilizando o encaminhamento de mensagens IoT Hub.
> * Crie uma conta de armazenamento para armazenar dados de eventos relevantes.
> * Simular um dispositivo IoT no veículo.
    

## <a name="use-case"></a>Caso de utilização

Esta solução demonstra um cenário em que uma empresa de aluguer de automóveis planeia monitorizar e registar eventos para os seus carros de aluguer. As empresas de aluguer de automóveis costumam alugar carros para uma região geográfica específica. Precisam de localizar os carros enquanto são alugados. Os casos de um carro que sai da região geográfica escolhida devem ser registados. Os dados de registo garantem que as políticas, taxas e outros aspetos do negócio seriam tratados corretamente.

No nosso caso de utilização, os carros de aluguer estão equipados com dispositivos IoT que enviam regularmente dados de telemetria para o Azure IoT Hub. A telemetria inclui a localização atual e indica se o motor do carro está em funcionamento. O esquema de localização do dispositivo adere ao esquema IoT [Plug and Play para dados geoespaciais](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). O esquema de telemetria do dispositivo de aluguer do carro parece:

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

Vamos usar a telemetria do dispositivo no veículo para atingir o nosso objetivo. Queremos executar regras de geofencing. E queremos responder sempre que recebermos um evento que indique que o carro se mexeu. Para tal, subscreveremos os eventos de telemetria do dispositivo a partir do IoT Hub via Event Grid. 

Existem várias formas de subscrever a Grade de Eventos, neste tutorial usamos Funções Azure. A Azure Functions reage a eventos publicados na Grelha de Eventos. Também implementa a lógica de negócio de aluguer de carros, que é baseada em análise espacial Azure Maps. 

O código dentro da função Azure verifica se o veículo deixou a geofence. Se o veículo saiu da geofence, a função Azure recolhe informações adicionais, como o endereço associado à localização atual. A função também implementa a lógica para armazenar dados significativos de eventos num armazenamento de bolha de dados que ajuda a fornecer a descrição das circunstâncias do evento. 

As circunstâncias do evento podem ser úteis para a empresa de aluguel de carros e para o cliente de aluguel. O diagrama seguinte dá-lhe uma visão geral de alto nível do sistema.

 
  <center>

  ![Visão geral do sistema](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

A figura a seguir representa a área de geofência realçada em azul. A rota do veículo de aluguer é indicada por uma linha verde.

  ![Rota da Geofência](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Pré-requisitos 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para completar os passos neste tutorial, primeiro precisa criar um grupo de recursos no portal Azure. Para criar um grupo de recursos, faça os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **grupos de recursos**.
    
   ![Grupos de recursos](./media/tutorial-iot-hub-maps/resource-group.png)

3. Nos **grupos de recursos**, selecione **Adicionar**.
    
   ![Adicionar grupo de recursos](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Introduza os seguintes valores de propriedade:
    * **Assinatura:** Selecione a sua subscrição Azure.
    * **Grupo de recursos:** Insira "ContosoRental" como o nome do grupo de recursos.
    * **Região:** Selecione uma região para o grupo de recursos.  

    ![Detalhes do grupo de recursos](./media/tutorial-iot-hub-maps/resource-details.png)

    Selecione **'Rever + criar'** e, em seguida, selecione **Criar** na página seguinte.

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps 

Para implementar a lógica de negócio baseada na análise espacial Azure Maps, precisamos de criar uma conta Azure Maps no grupo de recursos que criámos. Siga as instruções na [Criar uma conta](quick-demo-map-app.md#create-an-azure-maps-account) para criar uma subscrição de conta Azure Maps com nível de preços S1. Siga os passos na [chave principal](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave principal para a sua conta. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](how-to-manage-authentication.md)



### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para registar os dados do evento, criaremos um **v2storage** de uso geral que fornece acesso a todos os serviços de Armazenamento Azure: bolhas, ficheiros, filas, tabelas e discos.  Precisamos colocar esta conta de armazenamento no grupo de recursos "ContosoRental" para armazenar dados como bolhas. Para criar uma conta de armazenamento, siga as instruções para [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal). Em seguida, precisamos criar um recipiente para armazenar bolhas. Siga os passos abaixo para o fazer:

1. Na sua "conta de armazenamento - blob, arquivo, mesa, fila", navegue para Contentores.

    ![bolhas](./media/tutorial-iot-hub-maps/blobs.png)

2. Clique no botão do recipiente na parte superior esquerda e diga o nome do seu recipiente "contoso-rental-logs" e clique em "OK".

    ![blob-recipiente](./media/tutorial-iot-hub-maps/blob-container.png)

3. Navegue na lâmina das **teclas Access** na sua conta de armazenamento e copie o "nome da conta de armazenamento" e "chave de acesso". São necessários num passo posterior.

    ![acesso-chaves](./media/tutorial-iot-hub-maps/access-keys.png)


Agora, temos uma conta de armazenamento e um contentor para registar dados do evento. Em seguida, vamos criar um hub IoT.

### <a name="create-an-iot-hub"></a>Criar um Hub IoT

O IoT Hub é um serviço gerido na nuvem. O IoT Hub funciona como um centro de mensagens central para a comunicação bidis entre uma aplicação IoT e os dispositivos por si geridos. Para encaminhar as mensagens de telemetria do dispositivo para uma Grade de Eventos, crie um Hub IoT dentro do grupo de recursos "ContosoRental". Crie uma integração de rota de mensagem onde filtraremos mensagens com base no estado do motor do carro. Também enviaremos mensagens de telemetria do dispositivo para a Grelha de Eventos sempre que o carro estiver em movimento.

> [!Note] 
> A funcionalidade do IoT Hub de publicar eventos de telemetria de dispositivos na Grade de Eventos está em pré-visualização pública. As funcionalidades de pré-visualização públicas estão disponíveis em todas as regiões, exceto **nos EUA, Eua Ocidental, Europa Ocidental, Governo Azure, Azure China 21Vianet** e **Azure Germany.** 

Crie um Hub Iot seguindo os passos na [criação de uma secção IoT Hub](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).


### <a name="register-a-device"></a>Registar um dispositivo 

Para se ligar ao Hub IoT, deve ser registado um dispositivo. Para registar um dispositivo com hub IoT, siga os passos abaixo:

1. No seu IoT Hub, clique na lâmina "dispositivos IoT" e clique em "Novo".

    ![adicionar dispositivo](./media/tutorial-iot-hub-maps/add-device.png)

2. Na página do dispositivo, nomeie o seu dispositivo IoT e clique em "Guardar".
    
    ![dispositivo de registo](./media/tutorial-iot-hub-maps/register-device.png)

3. Guarde a cadeia de **ligação primária** do seu dispositivo para a utilizar num passo posterior, no qual necessita de alterar um espaço reservado com esta cadeia de ligação.

    ![adicionar dispositivo](./media/tutorial-iot-hub-maps/connection-string.png)

## <a name="upload-geofence"></a>Carregar geofence

Usaremos a [aplicação Postman](https://www.getpostman.com) para [enviar o geofence](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) para o serviço Azure Maps usando a API de data upload de dados do Azure Maps. Qualquer evento quando o carro estiver fora desta geofência será registado.

Abra a aplicação Do Carteiro e siga os passos abaixo para carregar o geofence usando o Azure Maps, Data Upload API.  

1. Na aplicação Do Carteiro, clique em novo Crie novo e selecione Request. Introduza um nome de Pedido para carregar dados de geofence, selecione uma recolha ou pasta para guardá-lo e clique em Guardar.

    ![Carregar geofences usando o Carteiro](./media/tutorial-iot-hub-maps/postman-new.png)

2. Selecione O método POST HTTP no separador construtor e introduza o seguinte URL para fazer um pedido DE POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    O valor "geojson" contra o `dataFormat` parâmetro no caminho URL representa o formato dos dados que estão a ser carregados.

3. Clique em **Params**e introduza os seguintes pares de Tecla/Valor a utilizar para o URL de pedido de CORREIO. Substitua o valor da chave de subscrição pela tecla Azure Maps.
   
    ![Params de valor chave Carteiro](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Clique em **'Corpo'** e selecione o formato de entrada **bruta** e escolha **o JSON (aplicação/texto)** como o formato de entrada da lista de drop-down. Abra o ficheiro de dados JSON [aqui,](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)e copie o Json na secção do corpo como dados para carregar e clicar **Enviar**.
    
    ![post dados](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Reveja os cabeçalhos de resposta. Após um pedido bem sucedido, o cabeçalho **de localização** conterá o estado URI para verificar o estado atual do pedido de upload. O status URI será do seguinte formato. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Copie o seu URI de estado e apeça-lhe um `subscription-key` parâmetro. Atribua o valor da sua chave de subscrição da conta Azure Maps ao `subscription-key` parâmetro. O formato URI de estado deve ser como o abaixo e `{Subscription-key}` substituído pela sua chave de subscrição.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Para obter o, `udId` abra um novo separador na aplicação Do Carteiro e selecione o método GET HTTP no separador construtor e faça um pedido GET no status URI. Se o seu upload de dados tiver sido bem sucedido, receberá um udId no corpo de resposta. Copie o udId para utilização posterior.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Em seguida, criaremos uma Função Azure dentro do grupo de recursos "ContosoRental" e, em seguida, configuraremos uma rota de mensagem no IoT Hub para filtrar mensagens de telemetria do dispositivo.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Crie uma Função Azure e adicione uma subscrição de Grade de Eventos

A azure Functions é um serviço de computação sem servidor que nos permite executar código a pedido, sem a necessidade de provisões ou gestão explícita de infraestruturas de computação. Para saber mais sobre as Funções Azure, veja a documentação das [funções Azure.](https://docs.microsoft.com/azure/azure-functions/functions-overview) 

A lógica que implementamos na função é usar os dados de localização provenientes da telemetria do dispositivo no veículo para avaliar o estado da geofência. Caso um determinado veículo saia da geofence, a função recolherá mais informações como o endereço do local através da [API reversa do Endereço de Pesquisa.](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) Esta API traduz uma coordenada de localização num endereço de rua compreensível humano. 

Todas as informações relevantes do evento são então mantidas na loja blob. Passo 5 abaixo aponta para o código executável implementando tal lógica. Siga os passos abaixo para criar uma Função Azure que envia registos de dados para o recipiente blob na conta de armazenamento de bolhas e adicione uma subscrição de Grade de Eventos à sua conta.

1. No painel de instrumentos do portal Azure, selecione criar um recurso. **Selecione Computar** a partir da lista de tipos de recursos disponíveis e, em seguida, selecione **App de função**.

    ![criar recursos](./media/tutorial-iot-hub-maps/create-resource.png)

2. Na página de criação **da Aplicação de Função,** nomeie a sua aplicação de função. No **Grupo de Recursos**, selecione Use **existente**e selecione "ContosoRental" da lista de drop-down. Selecione ".NET Core" como a Pilha de Tempo de Execução. Em **Alojamento**, para **conta de Armazenamento**, selecione o nome da conta de armazenamento a partir de um passo anterior. No nosso passo anterior, nomeamos a conta de armazenamento **v2storage.**  Em seguida, selecione **Review+Create**.
    
    ![criar aplicativo](./media/tutorial-iot-hub-maps/rental-app.png)

2. Reveja os detalhes da aplicação de funções e selecione "Criar".

3. Uma vez criada a aplicação, precisamos de adicionar uma função à sua. Vá ao aplicativo de função. Clique em **Nova função** para adicionar uma função e escolha **In-Portal** como ambiente de desenvolvimento. Em seguida, **selecione Continue**.

    ![criar função](./media/tutorial-iot-hub-maps/function.png)

4. Escolha **mais modelos** e clique em **Terminar e ver modelos.** 

5. Selecione o modelo com um **gatilho de grelha de evento Azure**. Instale extensões se solicitadas, nomeie a função e selecione **Criar**.

    ![modelo de função](./media/tutorial-iot-hub-maps/eventgrid-funct.png)
    
    O **Azure Event Hub Trigger** e o **Azure Event Grid Trigger** têm ícones semelhantes. Certifique-se de que seleciona o **Gatilho da grelha de eventos Azure**.

6. Copie o [código C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) para a sua função.
 
7. No script C# substitua os seguintes parâmetros. Clique em **Save** (Guardar). Não clique em **Executar** ainda
    * Substitua o **SUBSCRIPTION_KEY** pela chave de subscrição primária da conta Azure Maps.
    * Substitua o **UDID** pelo udId da geofence que carregou, 
    * A função **CreateBlobAsync** no script cria uma bolha por evento na conta de armazenamento de dados. Substitua o **ACCESS_KEY**, **ACCOUNT_NAME**, e **STORAGE_CONTAINER_NAME** pela chave de acesso da sua conta de armazenamento, nome da conta e recipiente de armazenamento de dados.

10. Clique na **subscrição 'Adicionar Grelha de Eventos'.**
    
    ![adicionar-evento-grelha](./media/tutorial-iot-hub-maps/add-egs.png)

11. Preencha os detalhes da subscrição, em **DETALHES DE SUBSCRIÇÃO DE EVENTOS** nomeie a subscrição do seu evento. Para o Caso Schema escolha "Event Grid Schema". Em **DETALHES TÓPICOS** selecione "Azure IoT Hub Accounts" como tipo tópico. Escolha a mesma subscrição que utilizou para criar o grupo de recursos, selecione "ContosoRental" como "Grupo de Recursos". Escolha o Hub IoT que criou como "Recurso". Escolha **a telemetria do dispositivo** como tipo de evento. Depois de escolher estas opções, verá automaticamente a alteração "Topic Type" para "IoT Hub".

    ![subscrição de grelha de eventos](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrar eventos usando encaminhamento de mensagens IoT Hub

Depois de adicionar uma subscrição de Grade de Eventos à Função Azure, verá uma rota de mensagem padrão para a Grelha de Eventos na lâmina de encaminhamento de **mensagens** do IoT Hub. O encaminhamento de mensagens permite-lhe encaminhar diferentes tipos de dados para vários pontos finais. Por exemplo, pode encaminhar mensagens de telemetria do dispositivo, eventos de ciclo de vida do dispositivo e eventos de mudança de dois dispositivos. Para saber mais sobre o encaminhamento de mensagens ioT hub, consulte [o encaminhamento de mensagens IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![hub-EG-rota](./media/tutorial-iot-hub-maps/hub-route.png)

No nosso cenário de exemplo, queremos filtrar todas as mensagens onde o veículo de aluguer está em movimento. Para publicar tais eventos de telemetria de dispositivo para a Event Grid, usaremos a consulta de encaminhamento para filtrar os eventos onde a `Engine` propriedade é **"ON".** Existem várias formas de consultar mensagens IoT dispositivo-nuvem, para saber mais sobre a sintaxe de encaminhamento de mensagens, ver [encaminhamento de mensagens IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Para criar uma consulta de encaminhamento, clique na rota **RouteToEventGrid** e substitua a **consulta de encaminhamento** por **"Engine='ON'"** e clique em **Guardar**. Agora o hub IoT só publicará telemetria do dispositivo onde o motor está ligado.

![hub-EG-filtro](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Enviar dados de telemetria para o IoT Hub

Uma vez que a nossa Função Azure esteja em funcionamento, podemos agora enviar dados de telemetria para o IoT Hub, que irá encaminhá-lo para a Grelha de Eventos. Vamos usar uma aplicação C# para simular dados de localização para um dispositivo no veículo de um carro alugado. Para executar a aplicação, precisa do .NET Core SDK 2.1.0 ou superior na sua máquina de desenvolvimento. Siga os passos abaixo para enviar dados simulados de telemetria para o IoT Hub.

1. Descarregue o [projeto De AluguerCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C#. 

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

  ![Saída terminal](./media/tutorial-iot-hub-maps/terminal.png)

Se abrir o recipiente de armazenamento de bolhas agora, deverá ser capaz de ver quatro bolhas para locais onde o veículo estava fora da geofence.

![Insira bolha](./media/tutorial-iot-hub-maps/blob.png)

O mapa abaixo mostra quatro pontos onde o veículo estava fora da geofence, registado em intervalos de tempo regulares.

![mapa de violação](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>Próximos passos

Para explorar as APIs do Azure Maps utilizadas neste tutorial, consulte:

* [Obter Endereço de Pesquisa Ao Contrário](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Obter Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Para obter uma lista completa das APIs de REST do Azure Maps, consulte:

* [Azure Maps REST APIs](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Para saber mais sobre ioT Plug and Play, consulte:

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Para obter uma lista de dispositivos certificados Azure para IoT, visite:

* [Dispositivos certificados Azure](https://catalog.azureiotsolutions.com/)

Para saber mais sobre como enviar o dispositivo para a telemetria em nuvem e o contrário, consulte:

* [Enviar telemetria a partir de um dispositivo](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
