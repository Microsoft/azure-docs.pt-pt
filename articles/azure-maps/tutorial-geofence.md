---
title: 'Tutorial: criar uma cerca geográfica e controlar dispositivos em um mapa | Mapas do Microsoft Azure'
description: Neste tutorial, saiba como configurar uma cerca geográfica e controlar dispositivos em relação à cerca geográfica usando Microsoft Azure mapear serviço espacial.
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 0e408adfe1daed402ef690224368e846bd0a97c8
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910950"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Tutorial: configurar uma cerca geográfica usando o Azure Maps

Este tutorial orienta você pelas etapas básicas para configurar a cerca geográfica usando o Azure Maps. O cenário que abordamos neste tutorial é ajudar a construção de gerenciadores de sites a monitorar equipamentos perigosos em potencial, movendo-se além das áreas de construção designadas. Um site de construção envolve equipamentos e regulamentos caros. Normalmente, isso requer que o equipamento permaneça dentro do site de construção e não deixe sem permissão.

Usaremos a API de carregamento de dados do Azure Maps para armazenar uma cerca geográfica e usar a API de limite geográfico do Azure Maps para verificar o local do equipamento em relação à cerca geográfica. Usaremos a grade de eventos do Azure para transmitir os resultados de cerca geográfica e configurar uma notificação com base nos resultados de cerca geográfica.
Para saber mais sobre a grade de eventos, consulte a [grade de eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview).
Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Carregue a área de limite geográfico no Azure Maps, serviço de dados usando a API de carregamento de dados.
> *   Configure uma grade de eventos para manipular eventos de cerca geográfica.
> *   Configurar o manipulador de eventos de cerca geográfica.
> *   Configure alertas em resposta a eventos de cerca geográfica usando aplicativos lógicos.
> *   Use as APIs do serviço de limite geográfico do Azure Maps para controlar se um ativo de construção está dentro do site de construção ou não.


## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps 

Para concluir as etapas neste tutorial, siga as instruções em [criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma assinatura da conta do Azure Maps com o tipo de preço S1 e siga as etapas em [obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave primária para sua conta. Para obter mais detalhes sobre a autenticação no Azure Maps, consulte [gerenciar a autenticação no Azure Maps](./how-to-manage-authentication.md).

## <a name="upload-geofences"></a>Carregar limites geográficos

Para carregar a cerca geográfica do site de construção usando a API de carregamento de dados, usaremos o aplicativo do postmaster. Para este tutorial, supomos que há uma área geral de construção do site, que é um parâmetro rígido que o equipamento de construção não deve violar. As violações desse limite são um ataque sério e são relatadas para o Operations Manager. Um conjunto otimizado de limites adicionais pode ser usado para acompanhar diferentes áreas de construção na área de construção geral, de acordo com a agenda. Podemos pressupor que a principal cerca geográfica tem um subsite1, que tem um tempo de expiração definido e expirará após esse período. Você pode criar mais limites geoaninhados de acordo com seus requisitos. Por exemplo, subsite1 poderia ser o local em que o trabalho está ocorrendo durante a semana 1 a 4 da agenda e o subsite 2 é o local em que o trabalho ocorre durante a semana de 5 a 7. Todos esses limites podem ser carregados como um único DataSet no início do projeto e usados para controlar as regras com base em tempo e espaço. Para obter mais informações sobre o formato de dados de cerca geográfica, consulte [dados GEOjson de limite](https://docs.microsoft.com/azure/azure-maps/geofence-geojson)geográfico. Para obter mais informações sobre como carregar dados para o serviço do Azure Maps, consulte a [documentação da API de carregamento de dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) .

Abra o aplicativo de postmaster e siga as etapas a seguir para carregar a cerca geográfica do site de construção usando o mapas do Azure, API de carregamento de dados.

1. Abra o aplicativo de postmaster e clique em novo | Crie uma nova solicitação e selecione solicitar. Insira um nome de solicitação para carregar dados de limite geográfico, selecione uma coleção ou pasta para salvar e clique em salvar.

    ![Carregar limites geográficos usando o postmaster](./media/tutorial-geofence/postman-new.png)

2. Selecione o método HTTP POST na guia Construtor e insira a URL a seguir para fazer uma solicitação POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    O parâmetro geojson no caminho da URL representa o formato de dados dos dados que estão sendo carregados.

3. Clique em **params**e insira os seguintes pares de chave/valor a serem usados para a URL de solicitação post. Substitua o valor da chave de assinatura pela sua chave do Azure Maps.
   
    ![Parâmetros para carregar dados (cerca geográfica) no postmaster](./media/tutorial-geofence/postman-key-vals.png)

4. Clique em **corpo** e selecione formato de entrada bruto e escolha JSON como o formato de entrada na lista suspensa. Forneça o JSON a seguir como dados a serem carregados:

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
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
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

5. Clique em enviar e examine o cabeçalho de resposta. Após uma solicitação bem-sucedida, o cabeçalho de **local** conterá o URI de status para verificar o status atual da solicitação de upload. O URI de status será do formato a seguir. 

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

## <a name="set-up-an-event-handler"></a>Configurar um manipulador de eventos

Para notificar o Operations Manager sobre eventos de inserir e sair, devemos criar um manipulador de eventos que receba as notificações.

Criaremos dois serviços de [aplicativos lógicos](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) para manipular, inserir e sair de eventos. Também criaremos gatilhos de eventos dentro dos aplicativos lógicos que são disparados por esses eventos. A ideia é enviar alertas, neste caso, emails para o Operations Manager sempre que o equipamento entra ou sai do site de construção. A figura a seguir ilustra a criação de um aplicativo lógico para o evento de entrada de cerca geográfica. Da mesma forma, você pode criar outro evento for Exit.
Você pode ver todos os [manipuladores de eventos com suporte](https://docs.microsoft.com/azure/event-grid/event-handlers) para obter mais informações.

1. Criar um aplicativo lógico no portal do Azure

   ![Criar aplicativos lógicos do Azure para manipular eventos de cerca geográfica](./media/tutorial-geofence/logic-app.png)

2. Selecione um gatilho de solicitação HTTP e, em seguida, selecione "enviar um email" como uma ação no conector do Outlook
  
   ![Esquema de aplicativos lógicos](./media/tutorial-geofence/logic-app-schema.png)

3. Salve o aplicativo lógico para gerar o ponto de extremidade de URL HTTP e copie a URL HTTP.

   ![Gerar um ponto de extremidade de aplicativos lógicos](./media/tutorial-geofence/logic-app-endpoint.png)


## <a name="create-an-azure-maps-events-subscription"></a>Criar uma assinatura de eventos do Azure Maps

O mapas do Azure dá suporte a três tipos de evento. Você pode observar os tipos de evento com suporte do Azure Maps [aqui](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Criaremos duas assinaturas diferentes, uma para Enter e outra para eventos Exit.

Siga as etapas abaixo para criar uma assinatura de evento para a cerca geográfica inserir eventos. Você pode assinar eventos de saída de cerca geográfica de maneira semelhante.

1. Navegue até sua conta do Azure Maps por meio [deste link do portal](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/dashboard/) e selecione a guia eventos.

   ![Navegue até os eventos da conta do Azure Maps](./media/tutorial-geofence/events-tab.png)

2. Para criar uma assinatura de evento, selecione assinatura de evento na página eventos.

   ![Criar uma assinatura de eventos do Azure Maps](./media/tutorial-geofence/create-event-subscription.png)

3. Nomeie a assinatura de eventos e assine o tipo de evento Enter. Agora, selecione o Web Hook como "tipo de ponto de extremidade" e copie o ponto de extremidade de URL HTTP do aplicativo lógico para "ponto de extremidade"

   ![Detalhes da assinatura de eventos do Azure Maps](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Usar API de cerca geográfica

Você pode usar a API de cerca geográfica para verificar se um **dispositivo** (equipamento faz parte do status) está dentro ou fora de uma cerca geográfica. Para entender melhor a API GET de cerca geográfica. Podemos consultá-lo em locais diferentes em que um equipamento específico foi movido ao longo do tempo. A figura a seguir ilustra cinco locais de um equipamento de construção específico com uma **ID de dispositivo** exclusiva, conforme observado em ordem cronológica. Cada um desses cinco locais é usado para avaliar a mudança de status de entrada e saída de cerca geográfica em relação à cerca. Se ocorrer uma alteração de estado, o serviço de limite geográfico disparará um evento, que será enviado ao aplicativo lógico pela grade de eventos. Como resultado, o gerente da operação receberá a notificação de inserção ou saída correspondente por email.

> [!Note]
> O cenário e o comportamento acima baseiam-se na mesma **ID de dispositivo** , de forma que ele reflita os cinco locais diferentes, como na figura abaixo.

![Mapa de cerca geográfica no Azure Maps](./media/tutorial-geofence/geofence.png)

No aplicativo de postmaster, abra uma nova guia na mesma coleção que você criou acima. Selecione obter método HTTP na guia Construtor:

A seguir estão cinco solicitações de API HTTP GET de isolamento geográfico, com diferentes coordenadas de localização correspondentes do equipamento, conforme observado em ordem cronológica. Cada solicitação é seguida pelo corpo da resposta.
 
1. Local 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Consulta de limite geográfico 1](./media/tutorial-geofence/geofence-query1.png)

   Se você observar a resposta acima, a distância negativa da cerca geográfica principal significa que o equipamento está dentro da cerca geográfica e o positivo da cerca geográfica do subsite significa que ele está fora da cerca geográfica do subsite. 

2. Local 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Consulta de limite geográfico 2](./media/tutorial-geofence/geofence-query2.png)

   Se você observar a resposta JSON anterior cuidadosamente, o equipamento estará fora do subsite, mas estará dentro do limite principal. Ele não aciona um evento e nenhum email é enviado.

3. Local 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Consulta de limite geográfico 3](./media/tutorial-geofence/geofence-query3.png)

   Ocorreu uma alteração de estado e agora o equipamento está dentro dos limites geográficos principal e subsite. Isso publica um evento e um email de notificação será enviado para a Operations Manager.

4. Local 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Consulta de limite geográfico 4](./media/tutorial-geofence/geofence-query4.png)

   Ao observar atentamente a resposta correspondente, você pode observar que nenhum evento é publicado aqui mesmo que o equipamento tenha saído da cerca geográfica do subsite. Se você olhar a hora especificada do usuário na solicitação GET, poderá ver que a cerca geográfica do subsite expirou em relação a esse tempo e o equipamento ainda está no limite geográfico principal. Você também pode ver a ID de geometria da cerca geográfica do subsite em `expiredGeofenceGeometryId` no corpo da resposta.


5. Local 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Consulta de limite geográfico 5](./media/tutorial-geofence/geofence-query5.png)

   Você pode ver que o equipamento saiu da cerca geográfica do site de construção principal. Ele publica um evento, é uma violação séria e um email de alerta crítico é enviado para a Operations Manager.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu, como configurar a cerca geográfica carregando-a no Azure Maps, serviço de dados usando a API de carregamento de dados. Você também aprendeu a usar a grade de eventos do Azure Maps para assinar e manipular eventos de cerca geográfica. 

* Consulte [manipular tipos de conteúdo em aplicativos lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)para saber como usar aplicativos lógicos para analisar o JSON para criar uma lógica mais complexa.
* Para saber mais sobre manipuladores de eventos na grade de eventos, confira [manipuladores de eventos com suporte na grade de eventos](https://docs.microsoft.com/azure/event-grid/event-handlers).
