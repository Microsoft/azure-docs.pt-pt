---
title: 'Tutorial: criar uma cerca geográfica e controlar dispositivos em um mapa | Mapas do Microsoft Azure'
description: Saiba como configurar uma cerca geográfica e controlar dispositivos em relação à cerca geográfica usando o serviço espacial de mapas de Microsoft Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a88f03adab3beaea75ec2fa9a1c6f59b09739025
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76153153"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Tutorial: configurar uma cerca geográfica usando o Azure Maps

Este tutorial orienta você pelas etapas básicas para configurar a cerca geográfica usando o Azure Maps. Considere esse cenário, um gerente de site de construção precisa monitorar equipamentos perigosos potenciais. O gerente precisa garantir que o equipamento permaneça nas áreas de construção gerais escolhidas. Essa área de construção geral é um parâmetro físico. As regulamentações exigem que o equipamento permaneça dentro desse parâmetro e as violações são relatadas para o Operations Manager.  

Usamos a API de carregamento de dados para armazenar uma cerca geográfica e usar a API de cerca geográfica para verificar o local do equipamento em relação à cerca geográfica. A API de carregamento de dados e a API de cerca geográfica são do Azure Maps. Também usamos a grade de eventos do Azure para transmitir os resultados de cerca geográfica e configurar uma notificação com base nos resultados de cerca geográfica. Para saber mais sobre a grade de eventos, consulte a [grade de eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview).

Neste tutorial, abordaremos como:

> [!div class="checklist"]
> * Carregue a área de limite geográfico no Azure Maps, serviço de dados usando a API de carregamento de dados.
> *   Configure uma grade de eventos para manipular eventos de cerca geográfica.
> *   Configurar o manipulador de eventos de cerca geográfica.
> *   Configure alertas em resposta a eventos de cerca geográfica usando aplicativos lógicos.
> *   Use as APIs do serviço de limite geográfico do Azure Maps para controlar se um ativo de construção está dentro do site de construção ou não.


## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps 

Siga as instruções em [criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma assinatura de conta do Azure Maps com o tipo de preço S1. As etapas em [obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) mostram como recuperar a chave primária da sua conta. Para obter mais informações sobre autenticação no Azure Maps, consulte [gerenciar a autenticação no Azure Maps](./how-to-manage-authentication.md).

## <a name="upload-geofences"></a>Carregar limites geográficos

Supomos que o limite geográfico principal é subsite1, que tem um tempo de expiração definido. Você pode criar mais limites geoaninhados de acordo com seus requisitos. Esses conjuntos de limites podem ser usados para controlar diferentes áreas de construção dentro da área de construção geral. Por exemplo, subsite1 poderia ser o local em que o trabalho está ocorrendo durante a semana 1 a 4 da agenda. subsite2 poderia ser onde o trabalho ocorre durante a semana de 5 a 7. Todos esses limites podem ser carregados como um único DataSet no início do projeto. Esses limites são usados para rastrear regras com base em tempo e espaço. 

Para carregar a cerca geográfica do site de construção usando a API de carregamento de dados, usamos o aplicativo do postmaster. Instale o [aplicativo do postmaster](https://www.getpostman.com/) e faça uma conta gratuita. 

Depois que o aplicativo do postmaster estiver instalado, siga estas etapas para carregar a cerca geográfica do site de construção usando o mapas do Azure, API de carregamento de dados.

1. Abra o aplicativo de postmaster e clique em novo | Crie uma nova solicitação e selecione solicitar. Insira um nome de solicitação para carregar dados de limite geográfico, selecione uma coleção ou pasta para salvar e clique em salvar.

    ![Carregar limites geográficos usando o postmaster](./media/tutorial-geofence/postman-new.png)

2. Selecione o método HTTP POST na guia Construtor e insira a URL a seguir para fazer uma solicitação POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    O parâmetro geojson no caminho da URL representa o formato de dados dos dados que estão sendo carregados.

3. Clique em **params**e insira os seguintes pares de chave/valor a serem usados para a URL de solicitação post. Substitua {Subscription-Key} pela sua chave de assinatura do Azure Maps, também conhecida como chave primária.
   
    ![Parâmetros para carregar dados (cerca geográfica) no postmaster](./media/tutorial-geofence/postman-key-vals.png)

4. Clique em **corpo** e selecione o formato de entrada bruto e escolha JSON como o formato de entrada na lista suspensa. Forneça o JSON a seguir como dados a serem carregados:

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

5. Clique em enviar e examine o cabeçalho de resposta. Após uma solicitação bem-sucedida, o cabeçalho de **local** conterá o URI de status. O URI de status é do formato a seguir. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Copie o URI de status e acrescente a chave de assinatura. O formato do URI de status deve ser semelhante ao mostrado abaixo. Observe que no formato abaixo, você alteraria a {Subscription-Key}, incluindo o {}, com sua chave de assinatura.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Para obter o `udId`, abra uma nova guia no aplicativo de postmaster e selecione obter método HTTP na guia Construtor. faça uma solicitação GET no URI de status da etapa anterior. Se o upload de dados tiver sido bem-sucedido, você receberá o udId no corpo da resposta. Copie o udId para uso posterior.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>Configurar um manipulador de eventos

Nesta seção, criamos um manipulador de eventos que recebe notificações. Esse manipulador de eventos deve notificar a Operations Manager sobre eventos de entrada e saída de qualquer equipamento.

Fazemos dois serviços de [aplicativos lógicos](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) para manipular, inserir e sair de eventos. Quando os eventos nos aplicativos lógicos são disparados, mais eventos são disparados em sequência. A ideia é enviar alertas, nesse caso, emails para o Operations Manager. A figura a seguir ilustra a criação de um aplicativo lógico para o evento de entrada de cerca geográfica. Da mesma forma, você pode criar outro evento for Exit. Você pode ver todos os [manipuladores de eventos com suporte](https://docs.microsoft.com/azure/event-grid/event-handlers) para obter mais informações.

1. Criar um aplicativo lógico no portal do Azure

   ![Criar aplicativos lógicos do Azure para manipular eventos de cerca geográfica](./media/tutorial-geofence/logic-app.png)

2. No menu configurações do aplicativo lógico, navegue até designer de **aplicativo lógico**

3. Selecione um gatilho de solicitação HTTP e, em seguida, selecione "nova etapa". No conector do Outlook, selecione "enviar um email" como uma ação
  
   ![Esquema de aplicativos lógicos](./media/tutorial-geofence/logic-app-schema.png)

4. Preencha os campos para enviar um email. Deixe a URL HTTP, ela será gerada automaticamente depois que você clicar em "salvar"

   ![Gerar um ponto de extremidade de aplicativos lógicos](./media/tutorial-geofence/logic-app-endpoint.png)

5. Salve o aplicativo lógico para gerar o ponto de extremidade de URL HTTP e copie a URL HTTP.

## <a name="create-an-azure-maps-events-subscription"></a>Criar uma assinatura de eventos do Azure Maps

O mapas do Azure dá suporte a três tipos de evento. Você pode observar os tipos de evento com suporte do Azure Maps [aqui] (https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps. Precisamos de duas assinaturas de evento diferentes, uma para o evento Enter e uma para os eventos Exit.

Siga as etapas abaixo para criar uma assinatura de evento para a cerca geográfica inserir eventos. Você pode assinar eventos de saída de cerca geográfica de maneira semelhante.

1. Navegue até sua conta do Azure Maps. No painel, selecione assinaturas. Clique no nome da sua assinatura e selecione **eventos** no menu configurações.

   ![Navegue até os eventos da conta do Azure Maps](./media/tutorial-geofence/events-tab.png)

2. Para criar uma assinatura de evento, selecione assinatura de evento na página eventos.

   ![Criar uma assinatura de eventos do Azure Maps](./media/tutorial-geofence/create-event-subscription.png)

3. Nomeie a assinatura de eventos e assine o tipo de evento Enter. Agora, selecione Web Hook como "tipo de ponto de extremidade". Clique em "selecionar um ponto de extremidade" e copie o ponto de extremidade de URL HTTP do aplicativo lógico para "{Endpoint}"

   ![Detalhes da assinatura de eventos do Azure Maps](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Usar API de cerca geográfica

Você pode usar a API de cerca geográfica para verificar se um **dispositivo**, nesse caso, está dentro ou fora de uma cerca geográfica. Permite consultar a API de obtenção de cerca geográfica em locais diferentes, em que um equipamento específico foi movido ao longo do tempo. A figura a seguir ilustra cinco locais com cinco equipamentos de construção. 

> [!Note]
> O cenário e o comportamento baseiam-se na mesma **ID de dispositivo** para refletir os cinco locais diferentes, como na figura abaixo.

O "DeviceID" é uma ID exclusiva que você fornece para seu dispositivo na solicitação GET, ao consultar seu local. Quando você faz uma solicitação assíncrona para a **pesquisa geogeográfica-Get API**, o "DeviceID" ajuda na publicação de eventos de cerca geográfica para esse dispositivo, em relação ao limite geográfico especificado. Neste tutorial, fizemos solicitações assíncronas para a API com um "DeviceID" exclusivo. As solicitações no tutorial são feitas em ordem cronológica, como no diagrama. A propriedade "isEventPublished" na resposta é publicada sempre que um dispositivo entra ou sai do limite geográfico. Você não precisa registrar um dispositivo para acompanhar este tutorial.

Vamos voltar ao diagrama. Cada um desses cinco locais é usado para avaliar a mudança de status de entrada e saída de cerca geográfica em relação à cerca. Se ocorrer uma alteração de estado, o serviço de limite geográfico disparará um evento, que será enviado ao aplicativo lógico pela grade de eventos. Como resultado, o Gerenciador da operação receberá a notificação de inserção ou saída correspondente por email.

![Mapa de cerca geográfica no Azure Maps](./media/tutorial-geofence/geofence.png)

No aplicativo de postmaster, abra uma nova guia na mesma coleção que você criou acima. Selecione obter método HTTP na guia Construtor:

A seguir estão cinco solicitações de API HTTP GET de isolamento geográfico, com diferentes coordenadas de local do equipamento. As coordenadas são como observadas em ordem cronológica. Cada solicitação é seguida pelo corpo da resposta.
 
1. Local 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Consulta de limite geográfico 1](./media/tutorial-geofence/geofence-query1.png)

   Na resposta acima, a distância negativa do limite geográfico principal significa que o equipamento está dentro do limite geográfico. A distância positiva da cerca geográfica do subsite significa que o equipamento está fora da cerca geográfica do subsite. 

2. Local 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Consulta de limite geográfico 2](./media/tutorial-geofence/geofence-query2.png)

   Se você observar a resposta JSON anterior cuidadosamente, o equipamento estará fora do subsite, mas estará dentro do limite principal. Nenhum evento é disparado e nenhum email é enviado.

3. Local 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Consulta de limite geográfico 3](./media/tutorial-geofence/geofence-query3.png)

   Ocorreu uma alteração de estado e agora o equipamento está dentro dos limites geográficos principal e subsite. Essa alteração faz com que um evento seja publicado e um email de notificação será enviado para a Operations Manager.

4. Local 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Consulta de limite geográfico 4](./media/tutorial-geofence/geofence-query4.png)

   Ao observar atentamente a resposta correspondente, você pode observar que nenhum evento é publicado aqui mesmo que o equipamento tenha saído da cerca geográfica do subsite. Se você olhar a hora especificada do usuário na solicitação GET, poderá ver que a cerca geográfica do subsite expirou para esse período. O equipamento ainda está no limite geográfico principal. Você também pode ver a ID de geometria da cerca geográfica do subsite em `expiredGeofenceGeometryId` no corpo da resposta.


5. Local 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Consulta de limite geográfico 5](./media/tutorial-geofence/geofence-query5.png)

   Você pode ver que o equipamento saiu da cerca geográfica do site de construção principal. Um evento é publicado e um email de alerta é enviado para o Operations Manager.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu: como configurar a cerca geográfica carregando-a no Azure Maps e no serviço de dados usando a API de carregamento de dados. Você também aprendeu a usar a grade de eventos do Azure Maps para assinar e manipular eventos de cerca geográfica. 

* Consulte [manipular tipos de conteúdo em aplicativos lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)para saber como usar aplicativos lógicos para analisar o JSON para criar uma lógica mais complexa.
* Para saber mais sobre manipuladores de eventos na grade de eventos, confira [manipuladores de eventos com suporte na grade de eventos](https://docs.microsoft.com/azure/event-grid/event-handlers).
