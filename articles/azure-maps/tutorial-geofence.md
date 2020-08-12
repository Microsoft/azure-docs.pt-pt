---
title: 'Tutorial: Criar um geofence e rastrear dispositivos num Mapa do Microsoft Azure'
description: Aprenda a criar uma geofence. Veja como rastrear dispositivos em relação à geofence utilizando o serviço Azure Maps Spatial.
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/11/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: b88d9132ec1548c9d94fc418af35b55ac2836e96
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121243"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Tutorial: configurar um perímetro geográfico com o Azure Maps

Este tutorial acompanha-o através dos fundamentos da criação e utilização dos serviços de Geofência Azure Maps no contexto do seguinte cenário:

*Um gestor de estaleiros de construção deve rastrear o equipamento à medida que entra e sai dos perímetros de uma área de construção. Sempre que um equipamento sai ou entra nestes perímetros, será enviada uma notificação por e-mail ao Gestor de Operações.*

A Azure Maps disponibiliza uma série de serviços de apoio ao rastreio de equipamentos que entram e saem da área de construção no cenário acima referido. Neste tutorial cobrimos como:

> [!div class="checklist"]
> * Carregar [dados geofencing GeoJSON](geofence-geojson.md) que definem as áreas do estaleiro de construção que queremos monitorizar. Usaremos a [API de upload de dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) para carregar geofences como coordenadas de polígono na sua conta Azure Maps.
> * Crie duas [Aplicações Lógicas](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) que, quando desencadeadas, enviarão notificações por e-mail ao Gestor de Operações do estaleiro de construção quando o equipamento entrar e sair da área de geofência.
> * Utilize a [Grelha de Eventos Azure](https://docs.microsoft.com/azure/event-grid/overview) para subscrever eventos de entrada e saída de geofence do Azure Maps. Configuraremos duas subscrições de eventos Web Hook que chamarão os pontos finais HTTP definidos nas suas duas Aplicações Lógicas. As Aplicações Lógicas enviarão então as notificações de e-mail apropriadas de equipamentos que vão além ou entram na geofence.
> * Utilize [o Geofence Search Get API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) para receber notificações quando um pedaço de equipamento sai e entra nas áreas de geofência.

## <a name="prerequisites"></a>Pré-requisitos

1. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição.

Este tutorial usa a aplicação [Do Carteiro,](https://www.postman.com/) mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="upload-geofencing-geojson-data"></a>Carregar dados geofencing GeoJSON

Neste tutorial, vamos carregar os dados da GeoJSON que contêm um `FeatureCollection` . Contém `FeatureCollection` duas geo-posições que definem áreas poligonais dentro do estaleiro de construção. A primeira geofência não tem prazo de validade ou restrições. O segundo só pode ser questionado durante o horário comercial (21h00 às 17h00). PST), e deixará de ser válido depois de 1 de janeiro de 2022. Para obter mais informações sobre o formato GeoJSON, consulte [os dados da GeoJSON geofencing.](geofence-geojson.md)

>[!TIP]
>Pode atualizar os seus dados de Geofencing a qualquer momento. Para obter mais informações sobre como atualizar os seus dados, consulte [a API do Upload de Dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)

1. Abra a aplicação do Carteiro. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar.**

2. Para criar o pedido, selecione **New** novamente. Na janela **Criar Novo,** selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção criada no passo anterior e, em seguida, **selecione Guardar**.

3. Selecione o método **POST** HTTP no separador construtor e introduza o seguinte URL para carregar os dados de geofencing para o serviço Azure Maps. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    O parâmetro _geojson_ no caminho URL representa o formato de dados dos dados que estão a ser carregados.

4. Clique no **separador 'Corpo'.** Selecione **cru**e, em seguida, **JSON** como formato de entrada. Copiar e colar os seguintes dados da GeoJSON na área de texto **do Corpo:**

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. Clique no botão **'Enviar'** azul e aguarde que o pedido seja processado. Assim que o pedido estiver concluído, vá ao **separador Cabeçalhos** da resposta. Copie o valor da chave **localização,** que é a `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Para verificar o estado da chamada da API, crie um pedido **GET** HTTP sobre o `status URL` . Terá de anexar a chave de subscrição primária do URL para autenticação. O pedido **GET** deve gostar do seguinte URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Quando o pedido **GET** HTTP estiver concluído com sucesso, retornará um `resourceLocation` . Contém `resourceLocation` o único para o conteúdo `udid` carregado. Você precisará guardar isso `udid` para consultar a API Get Geofence na última secção deste tutorial. Opcionalmente, pode utilizar o `resourceLocation` URL para recuperar metadados deste recurso no passo seguinte.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

8. Para recuperar metadados de conteúdo, crie um pedido **GET** HTTP no `resourceLocation` URL que foi recuperado no passo 7. Certifique-se de anexar a chave de subscrição primária ao URL para autenticação. O pedido **GET** deve gostar do seguinte URL:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. Quando o pedido **GET** HTTP estiver concluído com sucesso, o organismo de resposta conterá `udid` o especificado no passo `resourceLocation` 7, a localização para aceder/descarregar o conteúdo no futuro, e outros metadados sobre o conteúdo como a data, tamanho e assim por diante criados/atualizados. Um exemplo da resposta global é:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-logic-app-workflows"></a>Criar fluxos de trabalho de aplicações lógicas

Nesta secção, vamos criar dois pontos finais [da Logic App](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) que irão desencadear uma notificação por e-mail. Vamos mostrar-lhe como criar o primeiro gatilho que enviará notificações por e-mail sempre que o seu ponto final for chamado.

1. Inicie sessão no [portal do Azure](https://portal.azure.com)

2. No canto superior esquerdo do [portal Azure,](https://portal.azure.com)clique em **Criar um recurso**.

3. Na *caixa De Pesquisa no Mercado,* escreva **App Lógica.**

4. A partir dos *Resultados*, selecione **Logic App**. Clique no botão **Criar.**

5. Na página **Da Aplicação Lógica,** insira os seguintes valores:
    * A *Subscrição* que pretende utilizar para esta aplicação lógica.
    * O nome *do grupo de recursos* para esta aplicação lógica. Pode optar por *Criar um grupo de recursos novo* ou *Utilizar um grupo de recursos existente*.
    * O *nome da Aplicação Lógica* da sua aplicação lógica. Neste caso, usaremos `Equipment-Enter` como nome.

    Para efeitos deste tutorial, mantenha o resto dos valores nas suas definições predefinidos.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Criar uma aplicação lógica":::

6. Clique no botão **'Rever + Criar'.** Reveja as suas definições e clique em **Criar** para submeter a implementação. Quando a implementação estiver concluída com sucesso, clique em **Ir para o recurso**. Você será levado para o **Designer de Aplicativos Lógica**

7. Agora, vamos selecionar um tipo de gatilho. Percorra um pouco para baixo para o Início com uma secção *comum de gatilho** Clique em **Quando um pedido HTTP é recebido.**

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Criar uma aplicação lógica HTTP trigger":::

8. Clique em **Guardar** no canto superior direito do Designer. O **URL HTTP POST** será gerado automaticamente. Guarde o URL, pois vai precisar na secção seguinte para criar um ponto final do evento.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="Logic App HTTP Request URL e JSON":::

9. Selecione **+ Novo Passo**. Agora vamos escolher uma ação. Digite `outlook.com email` a caixa de pesquisa. Na lista **de Ações,** desloque-se para baixo e clique em **Enviar um e-mail (V2)**.
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Criar um designer de aplicativos lógico":::

10. Inscreva-se na sua conta Outlook.com. Certifique-se de clicar **sim** para permitir que a App Lógica aceda à conta. Preencha os campos para o envio de um e-mail.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Criar uma aplicação lógica enviar passo de e-mail":::

    >[!TIP]
    > Pode recuperar os dados de resposta da GeoJSON, tais como `geometryId` ou `deviceId` nas notificações de e-mail, configurando a Logic App para ler os dados enviados pela Grelha de Eventos. Para obter informações sobre como configurar a Logic App para consumir e passar dados de eventos em notificações de email, consulte [Tutorial: Envie notificações de e-mail sobre eventos Azure IoT Hub utilizando a Grade de Eventos e Aplicações Lógicas](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps).

11. Clique em **Guardar** no canto superior esquerdo do Designer de Aplicações Lógicas.

12. Repita os passos 3-11 para criar uma segunda App Lógica para notificar o gestor quando o equipamento sai do local de construção. Nomeie a Aplicação `Equipment-Exit` Lógica.

## <a name="create-azure-maps-events-subscriptions"></a>Criar subscrições de Eventos Azure Maps

O Azure Maps suporta três tipos de eventos. Você pode dar uma olhada nos tipos de eventos suportados Azure Maps [aqui.](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps)  Precisamos de criar duas subscrições diferentes de eventos: uma para eventos de geofence e outra para os eventos de saída de geofence.

Siga os passos abaixo para criar uma subscrição de eventos para os eventos de entrada de geofence. Pode subscrever eventos de saída de geofence repetindo os passos da mesma forma.

1. Navegue para a sua conta Azure Maps. No painel de **instrumentos, selecione Subscrições**. Clique no nome da sua subscrição e selecione **eventos** a partir do menu de definições.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Navegue para Azure Maps eventos de conta":::

2. Para criar uma subscrição de eventos, selecione **+ Subscrição** de eventos a partir da página de eventos.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Criar uma subscrição de Azure Maps Events":::

3. Na página **'Criar Subscrição de** Eventos', insira os seguintes valores:
    * O *Nome* da assinatura do evento.
    * O *Esquema do Evento* deve ser o Esquema de Grelha de *Eventos.*
    * O *Nome Tópico do Sistema* para esta subscrição do evento. Neste caso, vamos `Contoso-Construction` usar.
    * No *Filtro para Tipos de Eventos,* escolha `Geofence Entered` como tipo de evento.
    * Para o *Tipo ponto final,* escolha `Web Hook` .
    * Para *o Endpoint*, copie o URL HTTP POST para o ponto final de Entrada de Aplicação lógica que criou na secção anterior. Se se esqueceu de guardá-lo, pode voltar ao Logic App Designer e copiá-lo a partir do passo do gatilho HTTP.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Detalhes da subscrição de Azure Maps Events":::

4. Clique em **Criar**.

5. Repita os passos 1-4 para o ponto final de saída da aplicação lógica que criou na secção anterior. No passo 3, certifique-se de escolher `Geofence Exited` como tipo de evento.

## <a name="use-search-geofence-get-api"></a>Use Geofence De pesquisa Obter API

Agora, vamos usar o [Geofence Get API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) para enviar notificações de e-mail ao Gestor de Operações, quando um equipamento entra ou sai das geo-garantias.

Cada equipamento tem `deviceId` um. Neste tutorial, vamos seguir um único equipamento, cujo ID único `device_1` é.

Para maior clareza, o diagrama seguinte mostra as cinco localizações do equipamento ao longo do tempo, começando no local *start,* que está algures fora das geo-posições. Para efeitos deste tutorial, a localização *Iniciar* é indefinida, uma vez que não consultaremos o dispositivo naquele local.

Quando consultamos a [API de Geofence de Busca](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) com uma localização do equipamento que indique a entrada ou saída de geofência inicial, a Grelha de Eventos ligará para o ponto final apropriado da App Lógica para enviar uma notificação de e-mail ao Gestor de Operações.

Cada uma das seguintes secções faz http GET Pedidos de API de geofencing utilizando as cinco coordenadas de localização diferentes do equipamento.

![Mapa de Geofence em Mapas Azure](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Localização do equipamento 1 (47.638237,-122.132483)

1. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Novo,** selecione **Request**.  Insira um **nome de Pedido** para o pedido. Usaremos o nome, *Localização 1.* Selecione a coleção criada na [secção de dados de GeojSON de Geofencing upload](#upload-geofencing-geojson-data)e, em seguida, selecione **Save**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL Certifique-se de que substitui `{Azure-Maps-Primary-Subscription-key}` pela sua chave de subscrição primária e pela que `{udid}` `udid` guardou na secção de [dados de GeoJSON de Geofencing upload](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Clique no botão **Enviar.** O seguinte GeoJSON aparecerá na janela de resposta.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

4. Na resposta geoJSON acima, a distância negativa da geofência do local principal significa que o equipamento está dentro da geofência. A distância positiva da geofência do subseita significa que o equipamento está fora da geofência do subseita. Uma vez que esta é a primeira vez que este dispositivo está localizado dentro da geofência do site principal, o `isEventPublished` parâmetro está definido para e o Gestor de `true` Operações teria recebido uma notificação por e-mail de que o equipamento entrou na geofence.

### <a name="location-2-4763800-122132531"></a>Localização 2 (47.63800,-122.132531)

1. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Novo,** selecione **Request**.  Insira um **nome de Pedido** para o pedido. Usaremos o nome, *Localização 2.* Selecione a coleção criada na [secção de dados de GeojSON de Geofencing upload](#upload-geofencing-geojson-data)e, em seguida, selecione **Save**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL Certifique-se de que substitui `{Azure-Maps-Primary-Subscription-key}` pela sua chave de subscrição primária e pela que `{udid}` `udid` guardou na secção de [dados de GeoJSON de Geofencing upload](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Clique no botão **Enviar.** Aparecerão na janela de resposta o seguinte GeoJSON:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

4. Na resposta geoJSON acima, o equipamento permaneceu na geofência do local principal e não entrou na geofência do subseita. Como resultado, o `isEventPublished` parâmetro está definido e o Gestor de `false` Operações não receberá notificações por e-mail.

### <a name="location-3-4763810783315048-12213336020708084"></a>Localização 3 (47.63810783315048,-122.13336020708084)

1. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Novo,** selecione **Request**.  Insira um **nome de Pedido** para o pedido. Usaremos o nome, *Localização 3.* Selecione a coleção criada na [secção de dados de GeojSON de Geofencing upload](#upload-geofencing-geojson-data)e, em seguida, selecione **Save**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL Certifique-se de que substitui `{Azure-Maps-Primary-Subscription-key}` pela sua chave de subscrição primária e pela que `{udid}` `udid` guardou na secção de [dados de GeoJSON de Geofencing upload](#upload-geofencing-geojson-data).

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. Clique no botão **Enviar.** Aparecerão na janela de resposta o seguinte GeoJSON:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

4. Na resposta geoJSON acima, o equipamento permaneceu na geofência do local principal, mas entrou na geofência do subseita. Como resultado, o `isEventPublished` parâmetro está definido e o Gestor de `true` Operações receberá uma notificação por e-mail indicando que o equipamento entrou numa geofence.

    >[!NOTE]
    >Se o equipamento tivesse entrado no local após o horário comercial, nenhum evento seria publicado e o Gestor de Operações não receberia notificações.  

### <a name="location-4-47637988-1221338344"></a>Localização 4 (47.637988,-122.1338344)

1. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Novo,** selecione **Request**.  Insira um **nome de Pedido** para o pedido. Usaremos o nome, *Localização 4.* Selecione a coleção criada na [secção de dados de GeojSON de Geofencing upload](#upload-geofencing-geojson-data)e, em seguida, selecione **Save**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL Certifique-se de que substitui `{Azure-Maps-Primary-Subscription-key}` pela sua chave de subscrição primária e pela que `{udid}` `udid` guardou na secção de [dados de GeoJSON de Geofencing upload](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Clique no botão **Enviar.** Aparecerão na janela de resposta o seguinte GeoJSON:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

4. Na resposta geoJSON acima, o equipamento permaneceu na geofência do local principal, mas saiu da geofência do subseita. No entanto, se notar, o `userTime` valor é após o definido nos `expiredTime` dados de geofência. Como resultado, o `isEventPublished` parâmetro está definido e o Gestor de `false` Operações não receberá uma notificação por e-mail.

### <a name="location-547637988-1221338344"></a>Localização 5 (47.637988,-122.1338344)

1. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Novo,** selecione **Request**.  Insira um **nome de Pedido** para o pedido. Usaremos o nome, *Localização 4.* Selecione a coleção criada na [secção de dados de GeojSON de Geofencing upload](#upload-geofencing-geojson-data)e, em seguida, selecione **Save**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL Certifique-se de que substitui `{Azure-Maps-Primary-Subscription-key}` pela sua chave de subscrição primária e pela que `{udid}` `udid` guardou na secção de [dados de GeoJSON de Geofencing upload](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Clique no botão **Enviar.** Aparecerão na janela de resposta o seguinte GeoJSON:

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

4. Na resposta geoJSON acima, o equipamento saiu da geofência do local principal. Como resultado, o `isEventPublished` parâmetro está definido e o Gestor de `true` Operações receberá uma notificação por e-mail indicando que o equipamento saiu de uma geofence.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Lidar com tipos de conteúdo em Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)

> [!div class="nextstepaction"]
> [Envie notificações de e-mail usando redes de eventos e aplicações lógicas](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps)

> [!div class="nextstepaction"]
> [Manipuladores de Eventos Suportados na Grade de Eventos.](https://docs.microsoft.com/azure/event-grid/event-handlers)
