---
title: Como renderizar dados personalizados em um mapa de rasterização no Azure Maps | Microsoft Docs
description: Neste artigo, você aprenderá a renderizar dados personalizados em um mapa de varredura usando o serviço de imagem estática do Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b77cb73f08852768d10de8c76cde5e299f0776c2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75408906"
---
# <a name="render-custom-data-on-a-raster-map"></a>Renderizar dados personalizados em um mapa de rasterização

Este artigo explica como usar o [serviço de imagem estática](https://docs.microsoft.com/rest/api/maps/render/getmapimage) com a funcionalidade de composição de imagem para permitir sobreposições na parte superior de um mapa de rasterização. A composição de imagem inclui a capacidade de obter um bloco de varredura de volta, com dados adicionais como anotações personalizadas, rótulos e sobreposições de geometria.

Para renderizar os pinos, rótulos e sobreposições de geometria personalizados, você pode usar o aplicativo do postmaster. Você pode usar as [APIs do serviço de dados](https://docs.microsoft.com/rest/api/maps/data) do Azure Maps para armazenar e renderizar sobreposições.

> [!Tip]
> Geralmente, é muito mais econômico usar o SDK para Web do Azure Maps para mostrar um mapa simples em uma página da Web do que usar o serviço de imagem estática. O SDK da Web usa blocos de mapa e, a menos que o usuário se sobreponha e Aplique zoom no mapa, geralmente irá gerar apenas uma fração de uma transação por carga de mapa. Observe que o SDK da Web do Azure Maps tem opções para desabilitar o movimento panorâmico e o zoom. Além disso, o SDK da Web do Azure Maps fornece um conjunto mais rico de opções de visualização de dados do que um serviço Web de mapa estático.  

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps

Para concluir os procedimentos deste artigo, primeiro você precisa criar uma conta do Azure Maps e fazer com que você mapeie a chave de conta. Siga as instruções em [criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma assinatura de conta do Azure Maps e siga as etapas em [obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave primária para sua conta. Para obter mais detalhes sobre a autenticação no Azure Maps, consulte [gerenciar a autenticação no Azure Maps](./how-to-manage-authentication.md).


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Renderizar anotações com rótulos e uma imagem personalizada

> [!Note]
> O procedimento nesta seção requer uma conta do Azure Maps no tipo de preço S0 ou S1.

A camada S0 da conta do Azure Maps dá suporte a apenas uma única instância do parâmetro `pins`. Ele permite renderizar até cinco anotações, especificadas na solicitação de URL, com uma imagem personalizada.

Para renderizar anotações com rótulos e uma imagem personalizada, conclua estas etapas:

1. Crie uma coleção na qual armazenar as solicitações. No aplicativo de postmaster, selecione **novo**. Na janela **criar nova** , selecione **coleção**. Nomeie a coleção e selecione o botão **criar** . 

2. Para criar a solicitação, selecione **novo** novamente. Na janela **criar nova** , selecione **solicitação**. Insira um **nome de solicitação** para as anotações, selecione a coleção que você criou na etapa anterior como o local no qual salvar a solicitação e, em seguida, selecione **salvar**.
    
    ![Criar uma solicitação no postmaster](./media/how-to-render-custom-data/postman-new.png)

3. Selecione o método HTTP GET na guia Construtor e insira a URL a seguir para criar uma solicitação GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Esta é a imagem resultante:

    ![Um pino personalizado com um rótulo](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Obter dados do armazenamento de dados do Azure Maps

> [!Note]
> O procedimento nesta seção requer uma conta do Azure Maps no tipo de preço S1.

Você também pode obter o caminho e fixar as informações de localização usando a [API de carregamento de dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Siga as etapas abaixo para carregar os dados do caminho e dos Pins.

1. No aplicativo de postmaster, abra uma nova guia na coleção que você criou na seção anterior. Selecione o método HTTP POST na guia Construtor e insira a seguinte URL para fazer uma solicitação POST:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Na guia **params** , insira os seguintes pares de chave/valor, que são usados para a URL de solicitação post. Substitua o valor `subscription-key` pela sua chave de assinatura do Azure Maps.
    
    ![Params de chave/valor no postmaster](./media/how-to-render-custom-data/postman-key-vals.png)

3. Na guia **corpo** , selecione o formato de entrada bruto e escolha JSON como o formato de entrada na lista suspensa. Forneça este JSON como dados a serem carregados:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Selecione **Enviar** e revisar o cabeçalho de resposta. Após uma solicitação bem-sucedida, o cabeçalho de local conterá o URI de status para verificar o status atual da solicitação de upload. O URI de status deve ser do formato a seguir.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Copie seu URI de status e acrescente o parâmetro Subscription-Key a ele com seu valor sendo sua chave de assinatura de conta do Azure Maps que você usou para carregar os dados. O formato do URI de status deve ser semelhante ao seguinte:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. Para obter o, udId abra uma nova guia no aplicativo de postmaster e selecione obter método HTTP na guia Construtor e faça uma solicitação GET no URI de status. Se o upload de dados tiver sido bem-sucedido, você receberá um udId no corpo da resposta. Copie o udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Use o valor `udId` recebido da API de carregamento de dados para renderizar recursos no mapa. Para fazer isso, abra uma nova guia na coleção que você criou na seção anterior. Selecione o método HTTP GET na guia Construtor e insira essa URL para fazer uma solicitação GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Aqui está a imagem de resposta:

    ![Obter dados do armazenamento de dados do Azure Maps](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Renderizar um polígono com cor e opacidade

> [!Note]
> O procedimento nesta seção requer uma conta do Azure Maps no tipo de preço S1.


Você pode modificar a aparência de um polígono usando modificadores de estilo com o [parâmetro path](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. No aplicativo de postmaster, abra uma nova guia na coleção que você criou anteriormente. Selecione o método HTTP GET na guia Construtor e insira a seguinte URL para configurar uma solicitação GET para renderizar um polígono com cor e opacidade:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Aqui está a imagem de resposta:

    ![Renderizar um polígono opaco](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Renderizar um círculo e anotações com rótulos personalizados

> [!Note]
> O procedimento nesta seção requer uma conta do Azure Maps no tipo de preço S1.


Você pode fazer anotações e seus rótulos maiores ou menores usando o modificador `sc` estilo de escala. Esse modificador usa um valor maior que zero. Um valor de 1 é a escala padrão. Valores maiores que 1 farão com que os Pins sejam maiores, e valores menores que 1 diminuirão os menores. Para obter mais informações sobre modificadores de estilo, consulte [parâmetros de caminho de serviço de imagem estática](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Siga estas etapas para renderizar um círculo e anotações com rótulos personalizados:

1. No aplicativo de postmaster, abra uma nova guia na coleção que você criou anteriormente. Selecione o método HTTP GET na guia Construtor e insira essa URL para fazer uma solicitação GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Aqui está a imagem de resposta:

    ![Renderizar um círculo com anotações personalizadas](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>Passos seguintes


* Explore a documentação da [API de obtenção de imagem do mapa do Azure Maps](https://docs.microsoft.com/rest/api/maps/render/getmapimage) .
* Para saber mais sobre o serviço de dados do Azure Maps, consulte a [documentação do serviço](https://docs.microsoft.com/rest/api/maps/data).

