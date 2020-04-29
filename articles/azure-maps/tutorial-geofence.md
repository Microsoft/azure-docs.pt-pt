---
title: 'Tutorial: Criar uma geovedação e rastrear dispositivos num mapa Microsoft Azure Maps'
description: Aprenda a configurar um geofence e rastrear dispositivos relativos à geoveda utilizando o Serviço Espacial Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 126829f12d71e40511c26e781cb191988c1d031e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80333872"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Tutorial: Criar uma geoveda através do Azure Maps

Este tutorial acompanha-o através dos passos básicos para configurar geovese usando o Azure Maps. Considere este cenário, um Gestor de Estaleiros de Construção tem que monitorizar potenciais equipamentos perigosos. O gestor tem de garantir que o equipamento se mantém nas áreas de construção globais escolhidas. Esta área de construção geral é um parâmetro difícil. Os regulamentos exigem que o equipamento permaneça dentro deste parâmetro e as violações são comunicadas ao Gestor de Operações.  

Utilizamos a API de upload de dados para armazenar uma geovete e usamos a API Geofence para verificar a localização do equipamento em relação à geoveda. Tanto a API de Carregamento de Dados como a Geofence API são do Azure Maps. Também usamos a Azure Event Grid para transmitir os resultados da geovese e configurar uma notificação com base nos resultados da geoveda. Para saber mais sobre a Grelha de Eventos, consulte [a Grelha de Eventos Azure.](https://docs.microsoft.com/azure/event-grid/overview)

Neste tutorial cobrimos como:

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

Assumimos que a principal geovedação é o subsite1, que tem um tempo de validade definido. Pode criar geovedações mais aninhadas de acordo com as suas necessidades. Estes conjuntos de cercas podem ser usados para rastrear diferentes áreas de construção dentro da área de construção global. Por exemplo, o subsite1 pode ser onde os trabalhos estão a decorrer durante a semana 1 a 4 do horário. subsite2 pode ser onde o trabalho ocorre durante a semana 5 a 7. Todas estas vedações podem ser carregadas como um único conjunto de dados no início do projeto. Estas cercas são usadas para rastrear regras baseadas no tempo e no espaço. 

Para fazer o upload da geoveda para o local de construção utilizando a API de upload de dados, utilizamos a aplicação de carteiro. Instale a [aplicação](https://www.getpostman.com/) do carteiro e faça uma conta gratuita. 

Assim que a aplicação Postman estiver instalada, siga estes passos para carregar a geovedação do local de construção utilizando o Azure Maps, Data Upload API.

1. Abra a aplicação Postman e clique em novo Crie novo e selecione Pedido. Introduza um nome de Pedido para enviar dados geovetes, selecione uma recolha ou pasta para guardá-lo e clique em Guardar.

    ![Upload geofences usando carteiro](./media/tutorial-geofence/postman-new.png)

2. Selecione o método POST HTTP no separador construtor e introduza o seguinte URL para fazer um pedido de publicação.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    O parâmetro GEOJSON no caminho do URL representa o formato de dados dos dados que estão a ser carregados.

3. Clique em **Params**e introduza os seguintes pares chave/valor a utilizar para o URL de pedido de POST. Substitua {chave de subscrição} pela chave de subscrição do Azure Maps, também conhecida como chave principal.
   
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

5. Clique em enviar e rever o cabeçalho de resposta. Após um pedido bem sucedido, o cabeçalho de **localização** conterá o estado URI. O estado URI é do seguinte formato. O valor do uploadStatusId não está entre { }. É uma prática comum usar { } para mostrar valores que o utilizador deve introduzir, ou valores diferentes para diferentes utilizadores.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Copie o seu estado URI e apreenda a chave de subscrição. O formato URI de estatuto deve ser como o de baixo. Note que no formato abaixo, alteraria a {subscrição-key}, não incluindo o {}, com a sua chave de subscrição.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Para obter `udId`o , abra um novo separador na aplicação Postman e selecione o método GET HTTP no separador construtor. Faça um pedido GET no estado URI do passo anterior. Se o seu upload de dados tiver sido bem sucedido, receberá o udId no corpo de resposta. Copie o udId para posterior utilização.

   ```JSON
   {
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udId}?api-version=1.0"
   }
   ```

## <a name="set-up-an-event-handler"></a>Configurar um manipulador de eventos

Nesta secção, criamos um manipulador de eventos que recebe notificações. Este manipulador de eventos deve notificar o Gestor de Operações sobre os eventos de entrada e saída de qualquer equipamento.

Fazemos dois serviços [de Aplicações Lógicas](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) para lidar com eventos de entrada e saída. Quando os eventos nas Aplicações Lógicas disparam, mais eventos desencadeiam em sequência. A ideia é enviar alertas, neste caso e-mails, ao Gestor de Operações. A figura que se segue ilustra a criação de uma App Lógica para geovedação entrar em evento. Da mesma forma, pode criar outro para evento de saída. Você pode ver todos os manipuladores de [eventos suportados](https://docs.microsoft.com/azure/event-grid/event-handlers) para mais informações.

1. Crie uma App Lógica no portal Azure. Selecione a Aplicação Lógica no Mercado Azure. Em seguida, selecione o botão **Criar.**

   ![Crie aplicativos de lógica azure para lidar com eventos de geofence](./media/tutorial-geofence/logic-app.png)

2. No menu de definições para a App Lógica, navegue para **Logic App Designer**

3. Selecione um gatilho de pedido HTTP e, em seguida, selecione "New Step". No conector outlook, selecione "enviar um e-mail" como uma ação
  
   ![Esquema de Aplicativos Lógicos](./media/tutorial-geofence/logic-app-schema.png)

4. Preencha os campos para enviar um e-mail. Deixe o URL HTTP, gerando automaticamente depois de clicar em "guardar"

   ![Gerar um ponto final de Apps Lógicas](./media/tutorial-geofence/logic-app-endpoint.png)

5. Guarde a aplicação lógica para gerar o ponto final do HTTP URL e copie o URL HTTP.

## <a name="create-an-azure-maps-events-subscription"></a>Criar uma subscrição de Eventos Azure Maps

O Azure Maps suporta três tipos de eventos. Você pode dar uma olhada nos tipos de eventos suportados pelo Azure Maps [aqui](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Precisamos de duas subscrições de eventos diferentes, uma para o evento de entrada e outra para os eventos de saída.

Siga os passos abaixo para criar uma subscrição de evento para a geovedação entrar em eventos. Pode subscrever eventos de saída de geovedação de forma semelhante.

1. Navegue na sua conta Azure Maps. No painel de instrumentos, selecione Assinaturas. Clique no nome da subscrição e selecione **eventos** a partir do menu de definições.

   ![Navegue para a conta Azure Maps Eventos](./media/tutorial-geofence/events-tab.png)

2. Para criar uma subscrição de evento, selecione Subscrição de Eventos a partir da página de eventos.

   ![Criar uma subscrição de Eventos Azure Maps](./media/tutorial-geofence/create-event-subscription.png)

3. Nomeie a subscrição de eventos e subscreva o tipo de evento Enter. Agora, selecione Web Hook como "Endpoint Type". Clique em "Selecione um ponto final" e copie o ponto final do URL http da sua aplicação lógica EM "{Endpoint}"

   ![Detalhes da subscrição de Eventos Do Azure Maps](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Utilizar API Geofence

Pode utilizar a API Geofence para verificar se um **dispositivo,** neste caso, está dentro ou fora de uma geoveda. Vamos consultar a Geofence GET API contra diferentes locais, onde um determinado equipamento se moveu ao longo do tempo. A figura que se segue ilustra cinco locais com cinco equipamentos de construção. 

> [!Note]
> O cenário e o comportamento baseiam-se no mesmo id do **dispositivo** para que reflita os cinco locais diferentes como na figura abaixo.

O "deviceId" é um ID único que fornece para o seu dispositivo no pedido GET, ao consultar a sua localização. Quando faz um pedido assíncrono à **geovedação**de pesquisa - GET API , o "deviceId" ajuda na publicação de eventos geovedais para esse dispositivo, em relação à geoveda especificada. Neste tutorial, fizemos pedidos assíncronos à API com um "dispositivoId" único. Os pedidos no tutorial são feitos por ordem cronológica, como no diagrama. A propriedade "isEventPublished" na resposta é publicada sempre que um dispositivo entra ou sai da geoveda. Não é preciso registar um dispositivo para seguir com este tutorial.

Vamos olhar para trás para o diagrama. Cada uma destas cinco localizações é usada para avaliar a alteração do estado de entrada e saída de geoveses contra a vedação. Se ocorrer uma mudança de estado, o serviço de geovete desencadeia um evento, que é enviado para a App Lógica pela Grelha de Eventos. Como resultado, o gestor da operação receberá a notificação de entrada ou saída correspondente através de um e-mail.

![Mapa geofence em mapas azure](./media/tutorial-geofence/geofence.png)

Na aplicação Postman, abra um novo separador na mesma coleção que criou acima. Selecione o método GET HTTP no separador construtor:

Seguem-se cinco pedidos de API http get Geofencing, com diferentes coordenadas de localização do equipamento. As coordenadas são observadas por ordem cronológica. Cada pedido é seguido pelo corpo de resposta.
 
1. Localização 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Consulta geoveda1](./media/tutorial-geofence/geofence-query1.png)

   Na resposta acima, a distância negativa da geoveda principal significa que o equipamento está dentro da geoveda. A distância positiva da geovedação sublocal significa que o equipamento está fora da geovedação sublocal. 

2. Localização 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Consulta geoveda 2](./media/tutorial-geofence/geofence-query2.png)

   Se olhar atentamente para a resposta json anterior, o equipamento está fora do sublocal, mas encontra-se dentro da vedação principal. Nenhum evento é desencadeado e nenhum e-mail é enviado.

3. Localização 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Consulta geoveda 3](./media/tutorial-geofence/geofence-query3.png)

   Ocorreu uma mudança de Estado e agora o equipamento está dentro das geoveses principais e sublocais. Esta alteração faz com que um evento publique e será enviado um e-mail de notificação ao Gestor de Operações.

4. Localização 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Consulta geoveda 4](./media/tutorial-geofence/geofence-query4.png)

   Observando cuidadosamente a resposta correspondente, pode notar que nenhum evento é publicado aqui, mesmo que o equipamento tenha saído da geovedação do sublocal. Se olhar para o tempo especificado do utilizador no pedido GET, pode ver que a geovedação do subsite expirou para este momento. O equipamento ainda está na geocerca principal. Também pode ver a identificação de geometria do geoscerca sublocal no `expiredGeofenceGeometryId` corpo de resposta.


5. Localização 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Consulta geoveda 5](./media/tutorial-geofence/geofence-query5.png)

   Pode ver que o equipamento deixou o local principal de geovedação. Um evento é publicado e um e-mail de alerta é enviado para o Gestor de Operações.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial aprendeu: como configurar geovese, enviando-a no serviço De dados e mapas do Azure utilizando a API de Upload de Dados. Também aprendeu a usar a Azure Maps Events Grid para subscrever e lidar com eventos de geofence. 

* Consulte os tipos de [conteúdo de Handle em Apps Lógicas Azure,](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)para aprender a usar aplicações lógicas para analisar a JSON para construir uma lógica mais complexa.
* Para saber mais sobre os manipuladores de eventos em Event Grid, consulte os manipuladores de [eventos suportados na Grelha de Eventos](https://docs.microsoft.com/azure/event-grid/event-handlers).
