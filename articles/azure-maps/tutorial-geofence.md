---
title: 'Tutorial: Criar uma geovedação e rastrear dispositivos num mapa Microsoft Azure Maps'
description: Saiba como configurar uma cerca geográfica e controlar dispositivos em relação à cerca geográfica usando o serviço espacial de mapas de Microsoft Azure.
author: farah-alyasari
ms.author: v-faalya
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: c1f08fa5623642538f2ea99b2de07947b1bd9206
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209601"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Tutorial: Criar uma geoveda através do Azure Maps

Este tutorial acompanha-o através dos passos básicos para configurar geovese usando o Azure Maps. Considere esse cenário, um gerente de site de construção precisa monitorar equipamentos perigosos potenciais. O gerente precisa garantir que o equipamento permaneça nas áreas de construção gerais escolhidas. Essa área de construção geral é um parâmetro físico. As regulamentações exigem que o equipamento permaneça dentro desse parâmetro e as violações são relatadas para o Operations Manager.  

Usamos a API de carregamento de dados para armazenar uma cerca geográfica e usar a API de cerca geográfica para verificar o local do equipamento em relação à cerca geográfica. A API de carregamento de dados e a API de cerca geográfica são do Azure Maps. Também usamos a grade de eventos do Azure para transmitir os resultados de cerca geográfica e configurar uma notificação com base nos resultados de cerca geográfica. Para saber mais sobre a Grelha de Eventos, consulte [a Grelha de Eventos Azure.](https://docs.microsoft.com/azure/event-grid/overview)

Neste tutorial, abordaremos como:

> [!div class="checklist"]
> * Faça upload da área geofence no Azure Maps, serviço de dados utilizando a API de upload de dados.
> *   Criar uma Grelha de Eventos para lidar com eventos geovedais.
> *   Configurar geofence eventos handler.
> *   Instale alertas em resposta a eventos geovedantes usando Apps Lógicas.
> *   Utilize o serviço de geovedação Azure Maps APIs para saber se um ativo de construção está ou não dentro do estaleiro de construção.


## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps 

Siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma subscrição de conta Azure Maps com nível de preços S1. Os passos para obter a [chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) mostram-lhe como recuperar a chave principal da sua conta. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps](./how-to-manage-authentication.md).

## <a name="upload-geofences"></a>Upload geofences

Supomos que o limite geográfico principal é subsite1, que tem um tempo de expiração definido. Pode criar geovedações mais aninhadas de acordo com as suas necessidades. Esses conjuntos de limites podem ser usados para controlar diferentes áreas de construção dentro da área de construção geral. Por exemplo, subsite1 poderia ser o local em que o trabalho está ocorrendo durante a semana 1 a 4 da agenda. subsite2 poderia ser onde o trabalho ocorre durante a semana de 5 a 7. Todos esses limites podem ser carregados como um único DataSet no início do projeto. Esses limites são usados para rastrear regras com base em tempo e espaço. 

Para carregar a cerca geográfica do site de construção usando a API de carregamento de dados, usamos o aplicativo do postmaster. Instale a [aplicação](https://www.getpostman.com/) do carteiro e faça uma conta gratuita. 

Depois que o aplicativo do postmaster estiver instalado, siga estas etapas para carregar a cerca geográfica do site de construção usando o mapas do Azure, API de carregamento de dados.

1. Abra a aplicação Postman e clique em novo Crie novo e selecione Pedido. Introduza um nome de Pedido para enviar dados geovetes, selecione uma recolha ou pasta para guardá-lo e clique em Guardar.

    ![Upload geofences usando carteiro](./media/tutorial-geofence/postman-new.png)

2. Selecione o método POST HTTP no separador construtor e introduza o seguinte URL para fazer um pedido de publicação.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    O parâmetro GEOJSON no caminho do URL representa o formato de dados dos dados que estão a ser carregados.

3. Clique em **Params**e introduza os seguintes pares chave/valor a utilizar para o URL de pedido de POST. Substitua {Subscription-Key} pela sua chave de assinatura do Azure Maps, também conhecida como chave primária.
   
    ![Parâmetros para dados de upload (geofence) no Carteiro](./media/tutorial-geofence/postman-key-vals.png)

4. Clique em **Body** e, em seguida, selecione o formato de entrada crua e escolha JSON como formato de entrada da lista de dropdown. Forneça o seguinte JSON como dados a serem carregados:

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

5. Clique em enviar e rever o cabeçalho de resposta. Após um pedido bem sucedido, o cabeçalho de **localização** conterá o estado URI. O URI de status é do formato a seguir. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Copie o URI de status e acrescente a chave de assinatura. O formato do URI de status deve ser semelhante ao mostrado abaixo. Observe que no formato abaixo, você alteraria a {Subscription-Key}, incluindo o {}, com sua chave de assinatura.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Para obter o `udId`, abra um novo separador na aplicação Postman e selecione o método GET HTTP no separador construtor. Faça um pedido GET no estado URI do passo anterior. Se o upload de dados tiver sido bem-sucedido, você receberá o udId no corpo da resposta. Copie o udId para posterior utilização.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>Configurar um manipulador de eventos

Nesta seção, criamos um manipulador de eventos que recebe notificações. Esse manipulador de eventos deve notificar a Operations Manager sobre eventos de entrada e saída de qualquer equipamento.

Fazemos dois serviços [de Aplicações Lógicas](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) para lidar com eventos de entrada e saída. Quando os eventos nos aplicativos lógicos são disparados, mais eventos são disparados em sequência. A ideia é enviar alertas, nesse caso, emails para o Operations Manager. A figura que se segue ilustra a criação de uma App Lógica para geovedação entrar em evento. Da mesma forma, pode criar outro para evento de saída. Você pode ver todos os manipuladores de [eventos suportados](https://docs.microsoft.com/azure/event-grid/event-handlers) para mais informações.

1. Criar uma App Lógica no portal Azure

   ![Crie aplicativos de lógica azure para lidar com eventos de geofence](./media/tutorial-geofence/logic-app.png)

2. No menu de definições para a App Lógica, navegue para **Logic App Designer**

3. Selecione um gatilho de solicitação HTTP e, em seguida, selecione "nova etapa". No conector do Outlook, selecione "enviar um email" como uma ação
  
   ![Esquema de Aplicativos Lógicos](./media/tutorial-geofence/logic-app-schema.png)

4. Preencha os campos para enviar um email. Deixe a URL HTTP, ela será gerada automaticamente depois que você clicar em "salvar"

   ![Gerar um ponto final de Apps Lógicas](./media/tutorial-geofence/logic-app-endpoint.png)

5. Guarde a aplicação lógica para gerar o ponto final do HTTP URL e copie o URL HTTP.

## <a name="create-an-azure-maps-events-subscription"></a>Criar uma subscrição de Eventos Azure Maps

O Azure Maps suporta três tipos de eventos. Você pode dar uma olhada nos tipos de eventos suportados pelo Azure Maps [aqui](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Precisamos de duas assinaturas de evento diferentes, uma para o evento Enter e uma para os eventos Exit.

Siga os passos abaixo para criar uma subscrição de evento para a geovedação entrar em eventos. Pode subscrever eventos de saída de geovedação de forma semelhante.

1. Navegue até sua conta do Azure Maps. No painel, selecione assinaturas. Clique no nome da subscrição e selecione **eventos** a partir do menu de definições.

   ![Navegue para a conta Azure Maps Eventos](./media/tutorial-geofence/events-tab.png)

2. Para criar uma subscrição de evento, selecione Subscrição de Eventos a partir da página de eventos.

   ![Criar uma subscrição de Eventos Azure Maps](./media/tutorial-geofence/create-event-subscription.png)

3. Nomeie a subscrição de eventos e subscreva o tipo de evento Enter. Agora, selecione Web Hook como "tipo de ponto de extremidade". Clique em "selecionar um ponto de extremidade" e copie o ponto de extremidade de URL HTTP do aplicativo lógico para "{Endpoint}"

   ![Detalhes da subscrição de Eventos Do Azure Maps](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Utilizar API Geofence

Pode utilizar a API Geofence para verificar se um **dispositivo,** neste caso, está dentro ou fora de uma geoveda. Permite consultar a API de obtenção de cerca geográfica em locais diferentes, em que um equipamento específico foi movido ao longo do tempo. A figura a seguir ilustra cinco locais com cinco equipamentos de construção. 

> [!Note]
> O cenário e o comportamento baseiam-se no mesmo id do **dispositivo** para que reflita os cinco locais diferentes como na figura abaixo.

O "DeviceID" é uma ID exclusiva que você fornece para seu dispositivo na solicitação GET, ao consultar seu local. Quando faz um pedido assíncrono à **geovedação**de pesquisa - GET API , o "deviceId" ajuda na publicação de eventos geovedais para esse dispositivo, em relação à geoveda especificada. Neste tutorial, fizemos solicitações assíncronas para a API com um "DeviceID" exclusivo. As solicitações no tutorial são feitas em ordem cronológica, como no diagrama. A propriedade "isEventPublished" na resposta é publicada sempre que um dispositivo entra ou sai do limite geográfico. Você não precisa registrar um dispositivo para acompanhar este tutorial.

Vamos voltar ao diagrama. Cada uma destas cinco localizações é usada para avaliar a alteração do estado de entrada e saída de geoveses contra a vedação. Se ocorrer uma mudança de estado, o serviço de geovete desencadeia um evento, que é enviado para a App Lógica pela Grelha de Eventos. Como resultado, o Gerenciador da operação receberá a notificação de inserção ou saída correspondente por email.

![Mapa geofence em mapas azure](./media/tutorial-geofence/geofence.png)

Na aplicação Postman, abra um novo separador na mesma coleção que criou acima. Selecione o método GET HTTP no separador construtor:

A seguir estão cinco solicitações de API HTTP GET de isolamento geográfico, com diferentes coordenadas de local do equipamento. As coordenadas são como observadas em ordem cronológica. Cada pedido é seguido pelo corpo de resposta.
 
1. Localização 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Consulta geoveda1](./media/tutorial-geofence/geofence-query1.png)

   Na resposta acima, a distância negativa do limite geográfico principal significa que o equipamento está dentro do limite geográfico. A distância positiva da cerca geográfica do subsite significa que o equipamento está fora da cerca geográfica do subsite. 

2. Localização 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Consulta geoveda 2](./media/tutorial-geofence/geofence-query2.png)

   Se você observar a resposta JSON anterior cuidadosamente, o equipamento estará fora do subsite, mas estará dentro do limite principal. Nenhum evento é disparado e nenhum email é enviado.

3. Localização 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Consulta geoveda 3](./media/tutorial-geofence/geofence-query3.png)

   Ocorreu uma mudança de Estado e agora o equipamento está dentro das geoveses principais e sublocais. Essa alteração faz com que um evento seja publicado e um email de notificação será enviado para a Operations Manager.

4. Localização 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Consulta geoveda 4](./media/tutorial-geofence/geofence-query4.png)

   Observando cuidadosamente a resposta correspondente, pode notar que nenhum evento é publicado aqui, mesmo que o equipamento tenha saído da geovedação do sublocal. Se você olhar a hora especificada do usuário na solicitação GET, poderá ver que a cerca geográfica do subsite expirou para esse período. O equipamento ainda está no limite geográfico principal. Também é possível ver a identificação de geometria do geosfence sublocal sob `expiredGeofenceGeometryId` no corpo de resposta.


5. Localização 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Consulta geoveda 5](./media/tutorial-geofence/geofence-query5.png)

   Pode ver que o equipamento deixou o local principal de geovedação. Um evento é publicado e um email de alerta é enviado para o Operations Manager.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu: como configurar a cerca geográfica carregando-a no Azure Maps e no serviço de dados usando a API de carregamento de dados. Também aprendeu a usar a Azure Maps Events Grid para subscrever e lidar com eventos de geofence. 

* Consulte os tipos de [conteúdo de Handle em Apps Lógicas Azure,](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)para aprender a usar aplicações lógicas para analisar a JSON para construir uma lógica mais complexa.
* Para saber mais sobre os manipuladores de eventos em Event Grid, consulte os manipuladores de [eventos suportados na Grelha de Eventos](https://docs.microsoft.com/azure/event-grid/event-handlers).
