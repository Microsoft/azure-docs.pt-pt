---
title: 'Tutorial: Criar um geofence e rastrear dispositivos num Mapa do Microsoft Azure'
description: Tutorial sobre como criar uma geofence. Veja como rastrear dispositivos relativos à geofence utilizando o serviço Espacial Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/20/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 759adea3cf34b79c76b6facec3bd4626ca54107e
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625037"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Tutorial: configurar um perímetro geográfico com o Azure Maps

Este tutorial acompanha-o através dos fundamentos da criação e utilização dos serviços de geofência do Azure Maps. Fá-lo-ás no contexto do seguinte cenário:

*Um gestor de estaleiros deve acompanhar o equipamento à medida que entra e sai dos perímetros de uma área de construção. Sempre que um equipamento sai ou entra nestes perímetros, é enviada uma notificação por e-mail ao gestor de operações.*

A Azure Maps disponibiliza uma série de serviços de apoio ao rastreio de equipamentos que entram e saem da área de construção. Neste tutorial:

> [!div class="checklist"]
> * [Carrequim dados geofencing GeoJSON](geofence-geojson.md) que definem as áreas do estaleiro de construção que pretende monitorizar. Você usará a [API de upload de dados](/rest/api/maps/data/uploadpreview) para carregar geofences como coordenadas de polígono para a sua conta Azure Maps.
> * Crie duas [aplicações lógicas](../event-grid/handler-webhooks.md#logic-apps) que, quando desencadeadas, enviam notificações de email ao gestor de operações do estaleiro de construção quando o equipamento entra e sai da área de geofência.
> * Utilize [a Grelha de Eventos Azure](../event-grid/overview.md) para subscrever eventos de introdução e saída para a sua geofence Azure Maps. Criou duas subscrições de eventos webhook que chamam os pontos finais HTTP definidos nas suas duas aplicações lógicas. As aplicações lógicas enviam então as notificações de e-mail apropriadas de equipamentos que se movem para além ou entram na geofence.
> * Utilize [o Geofence Search Get API](/rest/api/maps/spatial/getgeofence) para receber notificações quando um pedaço de equipamento sai e entra nas áreas de geofência.

## <a name="prerequisites"></a>Pré-requisitos

1. [Criar uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).
2. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição.

Este tutorial usa a aplicação [Do Carteiro,](https://www.postman.com/) mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="upload-geofencing-geojson-data"></a>Carregar dados geofencing GeoJSON

Para este tutorial, você carrega os dados geofencing GeoJSON que contém um `FeatureCollection` . Contém `FeatureCollection` duas geo-posições que definem áreas poligonais dentro do estaleiro de construção. A primeira geofência não tem prazo de validade ou restrições. O segundo só pode ser questionado durante o horário comercial (9:00 AM-5:00 pm no Fuso Horário do Pacífico), e deixará de ser válido após 1 de janeiro de 2022. Para obter mais informações sobre o formato GeoJSON, consulte [os dados da GeoJSON geofencing.](geofence-geojson.md)

>[!TIP]
>Pode atualizar os seus dados de geofencing a qualquer momento. Para mais informações, consulte [a API do Upload de Dados.](/rest/api/maps/data/uploadpreview)

1. Abra a aplicação do Carteiro. Perto do topo, selecione **New**. Na janela **Criar Nova,** selecione **Coleção**. Nomeie a coleção e selecione **Criar**.

2. Para criar o pedido, selecione **New** novamente. Na janela **Criar Novo,** selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção criada no passo anterior e, em seguida, **selecione Guardar**.

3. Selecione o método **POST** HTTP no separador construtor e introduza o seguinte URL para carregar os dados de geofencing para Azure Maps. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    O `geojson` parâmetro na trajetória URL representa o formato de dados dos dados que estão a ser carregados.

4. Selecione o **separador Corpo.** Selecione **cru**, e, em seguida, **JSON** como o formato de entrada. Copiar e colar os seguintes dados da GeoJSON na área de texto **do Corpo:**

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

5. **Selecione Enviar** e aguarde que o pedido seja processado. Quando o pedido estiver concluído, vá ao **separador Headers** da resposta. Copie o valor da chave **localização,** que é a `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Para verificar o estado da chamada da API, crie um pedido **GET** HTTP sobre o `status URL` . Terá de anexar a chave de subscrição primária do URL para autenticação. O pedido **GET** deve gostar do seguinte URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Quando o pedido **GET** HTTP termina com sucesso, retorna a `resourceLocation` . Contém `resourceLocation` o único para o conteúdo `udid` carregado. Guarde isto `udid` para consultar a API Get Geofence na última secção deste tutorial. Opcionalmente, pode utilizar o `resourceLocation` URL para recuperar metadados deste recurso no passo seguinte.

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

9. Quando o pedido **GET** HTTP estiver concluído com sucesso, o corpo de resposta conterá o `udid` especificado no passo `resourceLocation` 7. Também conterá a localização para aceder e descarregar o conteúdo no futuro, e outros metadados sobre o conteúdo. Um exemplo da resposta global é:

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

## <a name="create-workflows-in-azure-logic-apps"></a>Criar fluxos de trabalho em Azure Logic Apps

Em seguida, cria dois pontos finais [de aplicações lógicas](../event-grid/handler-webhooks.md#logic-apps) que desencadeiam uma notificação de e-mail. Eis como criar o primeiro:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No canto superior esquerdo do portal Azure, selecione **Criar um recurso**.

3. Na **caixa De Pesquisa no Mercado,** escreva **App Lógica.**

4. A partir dos resultados, selecione **Logic App**  >  **Create**.

5. Na página **Da Aplicação Lógica,** insira os seguintes valores:
    * A **Subscrição** que pretende utilizar para esta aplicação lógica.
    * O nome **do grupo de recursos** para esta aplicação lógica. Pode optar por **criar um grupo de** recursos novo ou utilizar o grupo de recursos **existente.**
    * O **nome da Aplicação Lógica** da sua aplicação lógica. Neste caso, use `Equipment-Enter` como nome.

    Para efeitos deste tutorial, mantenha todos os outros valores nas suas definições predefinidos.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Screenshot de criar uma aplicação lógica.":::

6. Selecione **Rever + Criar**. Reveja as suas definições e **selecione Criar** para submeter a implementação. Quando a implementação estiver concluída com sucesso, selecione **Ir para o recurso**. Você é levado para **o Logic App Designer.**

7. Selecione um tipo de gatilho. Desloque-se até ao Início com uma secção **de gatilho comum.** Selecione **Quando um pedido HTTP for recebido**.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Screenshot de criar uma aplicação lógica HTTP trigger.":::

8. No canto superior direito do Logic App Designer, **selecione Save**. O **URL HTTP POST** é gerado automaticamente. Salve a URL. Precisa na secção seguinte para criar um ponto final do evento.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="Screenshot da Aplicação Lógica HTTP Request URL e JSON.":::

9. Selecione **+ Novo Passo**. Agora vais escolher uma ação. Digite `outlook.com email` a caixa de pesquisa. Na lista **de Ações,** desloque-se para baixo e selecione **Enviar por email o artigo V2.**
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Screenshot de criar um designer de aplicativos lógico.":::

10. Inscreva-se na sua conta Outlook. Certifique-se de selecionar **Sim** para permitir que a aplicação lógica aceda à conta. Preencha os campos para o envio de um e-mail.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Screenshot de criar uma aplicação lógica enviar passo de e-mail.":::

    >[!TIP]
    > Pode recuperar os dados de resposta da GeoJSON, tais `geometryId` como, `deviceId` por exemplo, nas notificações de e-mail. Pode configurar as Aplicações Lógicas para ler os dados enviados pela Grade de Eventos. Para obter informações sobre como configurar as Aplicações Lógicas para consumir e passar dados de eventos em notificações de email, consulte [Tutorial: Envie notificações de e-mail sobre eventos Azure IoT Hub utilizando a Grade de Eventos e Aplicações Lógicas](../event-grid/publish-iot-hub-events-to-logic-apps.md).

11. No canto superior esquerdo do Logic App Designer, **selecione Save**.

Para criar uma segunda aplicação lógica para notificar o gestor quando o equipamento sai do local de construção, repita os passos 3-11. Nomeie o aplicativo lógico `Equipment-Exit` .

## <a name="create-azure-maps-events-subscriptions"></a>Criar subscrições de eventos Azure Maps

O Azure Maps suporta [três tipos de eventos.](../event-grid/event-schema-azure-maps.md) Aqui, você precisa criar duas subscrições diferentes de eventos: uma para eventos de entrada de geofence e uma para eventos de saída de geofence.

Os passos seguintes mostram como criar uma subscrição de eventos para os eventos de entrada de geofence. Pode subscrever eventos de saída de geofence repetindo os passos da mesma forma.

1. Vá à sua conta Azure Maps. No painel de **instrumentos, selecione Subscrições**. Selecione o nome da subscrição e selecione **eventos** a partir do menu de definições.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Screenshot de ir a eventos de conta Azure Maps.":::

2. Para criar uma subscrição de eventos, selecione **+ Subscrição** de eventos a partir da página de eventos.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Screenshot de criar uma subscrição de eventos Azure Maps.":::

3. Na página **'Criar Subscrição de** Eventos', insira os seguintes valores:
    * O **Nome** da assinatura do evento.
    * O **Esquema do Evento** deve ser o Esquema de Grelha de *Eventos.*
    * O **Nome Tópico do Sistema** para esta subscrição do evento, que neste caso é `Contoso-Construction` .
    * Para **filtrar para tipos de eventos,** escolha `Geofence Entered` como tipo de evento.
    * Para **o tipo de ponto final,** escolha `Web Hook` .
    * Para **Endpoint**, copie o URL HTTP POST para a aplicação lógica insira o ponto final que criou na secção anterior. Se se esqueceu de guardá-lo, pode voltar ao Logic App Designer e copiá-lo a partir do passo do gatilho HTTP.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Screenshot dos detalhes da subscrição de eventos Azure Maps.":::

4. Selecione **Criar**.

Repita os passos 1-4 para o ponto final de saída da aplicação lógica que criou na secção anterior. No passo 3, certifique-se de escolher `Geofence Exited` como tipo de evento.

## <a name="use-spatial-geofence-get-api"></a>Use Geofence Espacial Obter API

Utilize [o Geofence Espacial Get API](/rest/api/maps/spatial/getgeofence) para enviar notificações de e-mail ao gestor de operações quando um equipamento entra ou sai das geo-garantias.

Cada equipamento tem `deviceId` um. Neste tutorial, você está rastreando um único equipamento, com uma identificação única de `device_1` .

O diagrama seguinte mostra as cinco localizações do equipamento ao longo do tempo, começando no local *start,* que está algures fora das geo-posições. Para efeitos deste tutorial, a localização *Iniciar* é indefinida, pois não vai consultar o dispositivo naquele local.

Ao consultar a [API de Geofence Espacial](/rest/api/maps/spatial/getgeofence) com uma localização do equipamento que indique a entrada ou saída de geofência inicial, a Grade de Eventos chama o ponto final da aplicação lógica apropriada para enviar uma notificação de e-mail ao gestor de operações.

Cada uma das seguintes secções faz pedidos de API utilizando as cinco coordenadas de localização diferentes do equipamento.

![Diagrama do mapa de geofence em Azure Maps](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Localização do equipamento 1 (47.638237,-122.132483)

1. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Novo,** selecione **Request**. Insira um **nome de Pedido** para o pedido. Faça-o *localização 1*. Selecione a coleção criada na [secção de dados de GeojSON de Geofencing upload](#upload-geofencing-geojson-data)e, em seguida, selecione **Save**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Certifique-se de que substitui `{Azure-Maps-Primary-Subscription-key}` a sua chave de subscrição primária e com a que `{udid}` `udid` guardou na secção de [dados de GeoJSON de Geofencing Upload](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Selecione **Enviar**. O seguinte GeoJSON aparece na janela de resposta.

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

Na resposta anterior à GeoJSON, a distância negativa da geofência do local principal significa que o equipamento está dentro da geofence. A distância positiva da geofência do subseita significa que o equipamento está fora da geofência do subseita. Porque esta é a primeira vez que este dispositivo está localizado dentro da geofência do local principal, o `isEventPublished` parâmetro é definido para `true` . O gestor de operações recebe uma notificação por e-mail de que o equipamento entrou na geofence.

### <a name="location-2-4763800-122132531"></a>Localização 2 (47.63800,-122.132531)

1. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Novo,** selecione **Request**. Insira um **nome de Pedido** para o pedido. Faça-o *Localização 2*. Selecione a coleção criada na [secção de dados de GeojSON de Geofencing upload](#upload-geofencing-geojson-data)e, em seguida, selecione **Save**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Certifique-se de que substitui `{Azure-Maps-Primary-Subscription-key}` a sua chave de subscrição primária e com a que `{udid}` `udid` guardou na secção de [dados de GeoJSON de Geofencing Upload](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Selecione **Enviar**. O seguinte GeoJSON aparece na janela de resposta:

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

Na resposta anterior à GeoJSON, o equipamento permaneceu na geofência do local principal e não entrou na geofência do subseita. Como resultado, o `isEventPublished` parâmetro está definido para , e o gestor de `false` operações não recebe nenhuma notificação de e-mail.

### <a name="location-3-4763810783315048-12213336020708084"></a>Localização 3 (47.63810783315048,-122.13336020708084)

1. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Novo,** selecione **Request**. Insira um **nome de Pedido** para o pedido. Faça-o *Localização 3*. Selecione a coleção criada na [secção de dados de GeojSON de Geofencing upload](#upload-geofencing-geojson-data)e, em seguida, selecione **Save**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Certifique-se de que substitui `{Azure-Maps-Primary-Subscription-key}` a sua chave de subscrição primária e com a que `{udid}` `udid` guardou na secção de [dados de GeoJSON de Geofencing Upload](#upload-geofencing-geojson-data).

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. Selecione **Enviar**. O seguinte GeoJSON aparece na janela de resposta:

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

Na resposta anterior à GeoJSON, o equipamento permaneceu na geofência do local principal, mas entrou na geofência do subseita. Como resultado, o `isEventPublished` parâmetro é definido para `true` . O gestor de operações recebe uma notificação por e-mail indicando que o equipamento entrou numa geofence.

>[!NOTE]
>Se o equipamento tivesse mudado para o local após o horário comercial, nenhum evento seria publicado e o gestor de operações não receberia nenhuma notificação.  

### <a name="location-4-47637988-1221338344"></a>Localização 4 (47.637988,-122.1338344)

1. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Novo,** selecione **Request**. Insira um **nome de Pedido** para o pedido. Faça-o *localização 4*. Selecione a coleção criada na [secção de dados de GeojSON de Geofencing upload](#upload-geofencing-geojson-data)e, em seguida, selecione **Save**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Certifique-se de que substitui `{Azure-Maps-Primary-Subscription-key}` a sua chave de subscrição primária e com a que `{udid}` `udid` guardou na secção de [dados de GeoJSON de Geofencing Upload](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Selecione **Enviar**. O seguinte GeoJSON aparece na janela de resposta:

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

Na resposta anterior à GeoJSON, o equipamento permaneceu na geofência do local principal, mas saiu da geofência do subseita. Note-se, no entanto, que o `userTime` valor é após o definido nos `expiredTime` dados de geofência. Como resultado, o `isEventPublished` parâmetro está definido para , e o gestor de `false` operações não recebe uma notificação de e-mail.

### <a name="location-5-4763799--122134505"></a>Localização 5 (47.63799, -122.134505)

1. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Novo,** selecione **Request**. Insira um **nome de Pedido** para o pedido. Faça-o *localização 5*. Selecione a coleção criada na [secção de dados de GeojSON de Geofencing upload](#upload-geofencing-geojson-data)e, em seguida, selecione **Save**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Certifique-se de que substitui `{Azure-Maps-Primary-Subscription-key}` a sua chave de subscrição primária e com a que `{udid}` `udid` guardou na secção de [dados de GeoJSON de Geofencing Upload](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63799&lon=-122.134505&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Selecione **Enviar**. O seguinte GeoJSON aparece na janela de resposta:

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

Na resposta anterior à GeoJSON, o equipamento saiu da geofência do local principal. Como resultado, o `isEventPublished` parâmetro é definido para , e o gestor de `true` operações recebe uma notificação de e-mail indicando que o equipamento saiu de uma geofence.


Também pode [enviar notificações por e-mail usando apps de rede de eventos e lógicas](../event-grid/publish-iot-hub-events-to-logic-apps.md) e verificar [handlers de eventos suportados na grelha de eventos](../event-grid/event-handlers.md) usando Azure Maps.

## <a name="clean-up-resources"></a>Limpar os recursos

Não há recursos que exijam limpeza.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Lidar com tipos de conteúdo em Azure Logic Apps](../logic-apps/logic-apps-content-type.md)