---
title: Criar um perímetro geográfico com o Azure Maps | Documentos da Microsoft
description: Configure um perímetro geográfico com o Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 7bd4c261af4159429a91bd8b425180037eec8c23
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670898"
---
# <a name="set-up-a-geofence-by-using-azure-maps"></a>Configure um perímetro geográfico, utilizando o Azure Maps

Este tutorial explica-lhe os passos de noções básicas para configurar o perímetro geográfico com o Azure Maps. O cenário que abordamos neste tutorial é ajuda construção site gerentes monitor potenciais perigosa equipamento ao se mover as áreas de construção designado. Um site de construção envolve equipamento dispendioso e regulamentos. Normalmente requer que o equipamento permanece dentro do site de construção e não deixa sem permissão.

Nós usaremos mapeia dados carregar API do Azure para armazenar um perímetro geográfico e API de perímetro geográfico para verificar a localização de equipamento relativo ao perímetro geográfico de mapas de utilização do Azure. Nós usaremos o Azure Event Grid para transmitir os resultados do perímetro geográfico e configurar uma notificação com base nos resultados do perímetro geográfico.
Para saber mais sobre o Event Grid, veja [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).
Neste tutorial, ficará a saber, como:

> [!div class="checklist"]
* Carregue a área do perímetro geográfico no serviço de dados usando a API de carregamento de dados do Azure Maps.
*   Configure uma grelha de eventos para manipular eventos de perímetro geográfico.
*   Configure o manipulador de eventos do perímetro geográfico.
*   Configure alertas em resposta a eventos de perímetro geográfico, utilizar o Logic Apps.
*   Utilize as APIs de serviço do Azure Maps perímetro geográfico para controlar se um recurso de construção é dentro do site de construção, ou não.


## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps 

Para concluir os passos neste tutorial, tem primeiro de ver [gerir conta e chaves](how-to-manage-account-keys.md) para criar e gerir a sua subscrição de conta com S1 escalão de preço.

## <a name="upload-geofences"></a>Carregar perímetros geográficos

Para carregar o perímetro geográfico para o site de construção com a API de carregamento de dados, utilizamos o aplicativo do postman. Tendo em vista neste tutorial, presumimos que existe uma área de site de construção geral, o que é um parâmetro de disco rígido que o equipamento de construção não deve viole. Violações deste cerca são uma séria ofensas e são relatadas para o Operations Manager. Pode ser utilizado um conjunto otimizado de limites adicionais que controlam a construção de diferente áreas dentro da área de construção geral de acordo com a agenda. Podemos supor que o perímetro geográfico principal tem um subsite1, que tem uma conjunto de expiração de tempo e irão expirar após esse tempo. Pode criar mais perímetros geográficos aninhados de acordo com os seus requisitos. Por exemplo, subsite1 poderia ser em que trabalho está a ocorrer durante a semana de 1 a 4 da agenda e subsite 2 é onde trabalho ocorre durante a semana de 5 a 7. Todos os tais limites podem ser carregados como um único conjunto de dados no início do projeto e utilizados para controlar as regras com base no tempo e espaço. Para obter mais informações sobre o formato de dados do perímetro geográfico, consulte [dados de perímetro geográfico GeoJSON](https://docs.microsoft.com/azure/azure-maps/geofence-geojson). Para obter mais informações sobre a carregar dados para o serviço do Azure Maps, consulte [documentação da API de carregar dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) .

Abra a aplicação Postman e siga os passos seguintes para carregar o site de construção perímetro geográfico com o Azure Maps, a API de carregar dados.

1. Abra a aplicação Postman e clique em novo | Criar um novo e selecione o pedido. Introduza um nome de pedido para o Upload perímetro geográfico dados, selecionados uma coleção ou uma pasta para guardá-lo para e clique em Guardar.

    ![Carregar perímetros geográficos com o Postman](./media/tutorial-geofence/postman-new.png)

2. Selecione o método POST HTTP no separador builder e introduza o seguinte URL para fazer um pedido POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    O parâmetro GEOJSON no caminho do URL representa o formato dos dados dos dados a ser carregados.

3. Clique em **Params**e introduza os seguintes pares de chave/valor a ser utilizado para o URL do pedido POST. Substitua o valor de chave de subscrição com a sua chave de subscrição do Azure Maps.
   
    ![Parâmetros de chave-valor Postman](./media/tutorial-geofence/postman-key-vals.png)

4. Clique em **corpo** , em seguida, selecione o formato de entrada não processado e escolha o JSON como o formato de entrada na lista pendente. Fornece o seguinte JSON como dados a ser carregado:

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

5. Clique em enviar e rever o cabeçalho de resposta. O cabeçalho de localização contém o URI para aceder ou transferir os dados para utilização futura. Ele também contém um exclusivo `udId` para os dados carregados.

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```

## <a name="set-up-an-event-handler"></a>Configurar um manipulador de eventos

Para notificar o Operations Manager sobre eventos de enter e exit, podemos deve criar um manipulador de eventos que recebe as notificações.

Iremos criar dois [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) serviços para processar, introduza e eventos de saída. Também iremos criar acionadores de eventos no Logic Apps que obter acionada por esses eventos. A idéia é enviar alertas, neste caso envia um e-mail para o Operations Manager sempre que o equipamento entra ou sai do site de construção. A figura seguinte ilustra a criação de uma aplicação lógica para o evento de entrada do perímetro geográfico. Da mesma forma, pode criar uma outra para o evento de saída.
Pode ver todas [suportado manipuladores de eventos](https://docs.microsoft.com/azure/event-grid/event-handlers) para obter mais informações.

1. Criar uma aplicação lógica no portal do Azure

  ![criar aplicações lógicas](./media/tutorial-geofence/logic-app.png)

2. Selecione um acionador de pedido HTTP e, em seguida, selecione "enviar e-mail" como uma ação no conector do outlook
  
  ![Esquema de aplicações lógicas](./media/tutorial-geofence/logic-app-schema.png)

3. Guarde a aplicação de lógica para gerar o ponto final do URL de HTTP e copie o URL de HTTP.

  ![O ponto de extremidade do Logic Apps](./media/tutorial-geofence/logic-app-endpoint.png)


## <a name="create-an-azure-maps-events-subscription"></a>Criar uma subscrição de eventos do Azure Maps

Mapas do Azure suporta três tipos de evento. Pode dar uma olhada nos tipos de eventos do Azure Maps suportado [aqui](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Iremos criar duas subscrições diferentes, uma para enter e outro para eventos de saída.

Siga os passos abaixo para criar uma subscrição de evento para o perímetro geográfico introduza eventos. Pode subscrever a eventos de saída do perímetro geográfico de forma semelhante.

1. Navegue para a sua conta do Azure Maps via [esta ligação portal](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/dashboard/) e selecione os separador de eventos.

   ![Eventos do Azure Maps](./media/tutorial-geofence/events-tab.png)

2. Para criar uma subscrição de evento, selecione a subscrição de evento a partir da página de eventos.

   ![Subscrição de eventos de mapas do Azure](./media/tutorial-geofence/create-event-subscription.png)

3. Nomeie a subscrição de eventos e subscrever o tipo de evento de Enter. Agora, selecione Hook de Web como "Tipo de ponto final" e copie o ponto de final do URL de HTTP de aplicação lógica para o "Ponto final"

   ![Subscrição de eventos](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Utilizar o perímetro geográfico API

Pode usar a API do perímetro geográfico para verificar se um **dispositivo** (equipamento faz parte do Estado) estiver dentro ou fora de um perímetro geográfico. Para compreender melhor o obter a API do perímetro geográfico. Podemos consultá-los em diferentes locais em que um determinado equipamento foi movido ao longo do tempo. A figura seguinte ilustra a cinco locais de um equipamento de construção específica com um exclusivo **id de dispositivo** que observado em ordem cronológica. Cada um destes cinco locais é utilizada para avaliar o perímetro geográfico entrar e sair de alteração de estado em relação a cerca. Se ocorrer uma alteração de estado, o serviço do perímetro geográfico aciona um evento, que é enviado para a aplicação lógica, o Event Grid. Como resultado o Gestor da operação irá receber o enter correspondente ou sair de notificação através de uma mensagem de e-mail.

> [!Note]
> O cenário e o comportamento acima baseia-se no mesmo **id de dispositivo** para que reflitam os cinco locais diferentes, como na figura abaixo.

![Geofence Map](./media/tutorial-geofence/geofence.png)

Na aplicação do Postman, abra uma nova guia na mesma coleção que criou acima. Selecione o método GET HTTP na guia construtor:

Seguem-se cinco pedidos de HTTP obter a API de barreira geográfica, com diferentes coordenadas de localização correspondente do equipamento que observado em ordem cronológica. Cada solicitação é seguida do corpo de resposta.
 
1. Localização 1:
    
  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```
  ![Consulta de perímetro geográfico 1](./media/tutorial-geofence/geofence-query1.png)

  Se examinar a resposta acima, a distância negativa do perímetro geográfico principal significa que o equipamento está dentro do perímetro geográfico e positivo do perímetro geográfico subsite significa que está fora do perímetro geográfico subsite. 

2. Localização 2: 
   
  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```
    
  ![Consulta de perímetro geográfico 2](./media/tutorial-geofence/geofence-query2.png)

  Se analisar a resposta JSON anterior cuidadosamente o equipamento está fora contendo, mas é dentro de cerca de principal. A mesma não aciona um evento e nenhum e-mail é enviado.

3. Localização 3: 
  
  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```

  ![Consulta de perímetro geográfico 3](./media/tutorial-geofence/geofence-query3.png)

  Ocorreu uma alteração de estado e agora o equipamento está dentro de ambas as principal e subsites perímetros geográficos. Isso publica um evento e uma notificação por e-mail será enviado para o Operations Manager.

4. Localização 4: 

  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```
  
  ![Consulta de perímetro geográfico 4](./media/tutorial-geofence/geofence-query4.png)

   Ao observar com cuidado a resposta correspondente, pode observar que nenhum evento é publicado aqui, apesar dos equipamentos tem saído do perímetro geográfico subsite. Se examinar o período de tempo especificado do utilizador no pedido GET, pode ver que o perímetro geográfico subsite expirou em relação ao momento e o equipamento ainda está no perímetro geográfico principal. Também pode ver o ID de geometria do perímetro geográfico subsite em `expiredGeofenceGeometryId` no corpo da resposta.


5. Localização 5:
      
  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```

  ![Consulta de perímetro geográfico 5](./media/tutorial-geofence/geofence-query5.png)

  Pode ver que o equipamento tiver saído do perímetro geográfico de site de construção principal. Publica um evento, é uma violação séria e é enviado um e-mail de alerta crítico para o Operations Manager.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial aprendeu, como configurar o perímetro geográfico ao carregá-lo no serviço de dados usando a API de carregamento de dados do Azure Maps. Também aprendeu como utilizar o Azure Maps eventos Grid para subscrever e manipular eventos de perímetro geográfico. 

* Ver [lidar com tipos de conteúdo no Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type), para saber como utilizar o Logic Apps para analisar o JSON para criar uma lógica mais complexa.
* Para saber mais sobre os manipuladores de eventos no Event Grid, veja [suportados manipuladores de eventos no Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers).
