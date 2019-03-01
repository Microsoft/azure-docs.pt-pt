---
title: Como processar dados personalizados num mapa de varredura no Azure Maps | Documentos da Microsoft
description: Processe dados personalizados num mapa de varredura no Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 982a8b6ed67b955fcb68006072b67f7c59f29688
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010162"
---
# <a name="render-custom-data-on-a-raster-map"></a>Processar dados personalizados num mapa de varredura

Este artigo explica como utilizar o [serviço de imagem estática](https://docs.microsoft.com/rest/api/maps/render/getmapimage) com a funcionalidade de composição de imagem para permitir sobreposições por cima de um mapa de varredura. Composição de imagem inclui a capacidade de obter um mosaico de varredura novamente, com dados adicionais, como tachinhas às personalizados, rótulos e sobreposições de geometria.

Para compor tachinhas às personalizados, rótulos e sobreposições de geometria, pode utilizar a aplicação Postman. Pode utilizar o Azure Maps [APIs de serviço de dados](https://docs.microsoft.com/rest/api/maps/data) para armazenar e processar sobreposições.


## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps

Para concluir os procedimentos neste artigo, primeiro tem de [criar uma conta do Azure Maps](how-to-manage-account-keys.md) no escalão de preço S1.

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Compor tachinhas com etiquetas e uma imagem personalizada

> [!Note]
> O procedimento nesta secção requer uma conta do Azure Maps no escalão de preço S0 ou S1.

O Azure Maps de contas de suporte de escalão S0 apenas uma única instância do `pins` parâmetro. Permite-lhe compor tachinhas às até cinco, especificadas no pedido de URL, com uma imagem personalizada.

Para compor tachinhas com etiquetas e uma imagem personalizada, conclua estes passos:

1. Crie uma coleção para armazenar os pedidos. Na aplicação do Postman, selecione **New**. Na **criar novo** janela, selecione **coleção**. Nome da coleção e selecione o **criar** botão. 

2. Para criar o pedido, selecione **New** novamente. Na **criar novo** janela, selecione **pedir**. Introduza um **nome do pedido** para as tachinhas às, selecione a coleção que criou no passo anterior, como a localização na qual pretende guardar o pedido e, em seguida, selecione **guardar**.
    
    ![Criar um pedido no Postman](./media/tutorial-geofence/postman-new.png)

3. Selecione o método GET HTTP na guia builder e introduza o URL seguinte para criar um pedido GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    Segue-se a imagem resultante:

    ![Um pino personalizado com uma etiqueta](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Obter dados do armazenamento de dados do Azure Maps

> [!Note]
> O procedimento nesta secção requer uma conta do Azure Maps no escalão de preço S1.

Também pode obter as informações de localização do caminho e o pin ao utilizar o [a API de carregar dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Siga os passos abaixo para carregar os dados de caminho e pins.

1. Na aplicação do Postman, abra uma nova guia na coleção que criou na secção anterior. Selecione o método POST HTTP no separador builder e introduza o seguinte URL para fazer um pedido POST:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Sobre o **Params** separador, introduza os seguintes pares chave/valor, que são utilizados para o URL do pedido POST. Substitua o `subscription-key` valor com a sua chave de subscrição do Azure Maps.
    
    ![Parâmetros de chave/valor no Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. Sobre o **corpo** separador, selecione o formato de entrada não processado e escolher o JSON como formato de entrada na lista pendente. Fornece este JSON como dados a ser carregado:
    
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

4. Selecione **enviar** e rever o cabeçalho de resposta. O cabeçalho de localização contém o URI utilizado para aceder ou transferir os dados para utilização futura. Ele também contém um exclusivo `udId` para os dados carregados.  

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```

5. Utilize o `udId` valor recebido a partir da API de carregar dados para renderizar recursos no mapa. Para tal, abra uma nova guia na coleção que criou na secção anterior. Selecione o método GET HTTP na guia builder e introduza este URL para fazer um pedido GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. Segue-se a imagem resultante:

    ![Obter dados do armazenamento de dados do Azure Maps](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Compor um polígono com cor e a opacidade

> [!Note]
> O procedimento nesta secção requer uma conta do Azure Maps no escalão de preço S1.


Pode modificar a aparência de um polígono com modificadores de estilo com o [parâmetro de caminho](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. Na aplicação do Postman, abra uma nova guia na coleção que criou anteriormente. Selecione o método GET HTTP na guia builder e introduza o URL seguinte para configurar um pedido GET para compor um polígono com cor e a opacidade:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

Segue-se a imagem resultante:

![Compor um polígono opaco](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Compor um círculo e tachinhas com etiquetas personalizadas

> [!Note]
> O procedimento nesta secção requer uma conta do Azure Maps no escalão de preço S1.


Pode tornar tachinhas às e os rótulos maior ou menor, utilizando o `sc` modificador de estilo de dimensionamento. Este modificador assume um valor maior que zero. Um valor de 1 é o dimensionamento padrão. Valores maiores que 1 fará com que os pins maiores e valores menores do que 1, fará com que mais pequenos. Para obter mais informações sobre os modificadores de estilo, consulte [parâmetros de caminho do serviço de imagem estática](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Siga estes passos para compor um círculo e tachinhas com etiquetas personalizadas:

1. Na aplicação do Postman, abra uma nova guia na coleção que criou anteriormente. Selecione o método GET HTTP na guia builder e introduza este URL para fazer um pedido GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

Segue-se a imagem resultante:

![Compor um círculo com tachinhas às personalizado](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>Passos Seguintes


* Explore os [API de imagem de mapa de obter mapas do Azure](https://docs.microsoft.com/rest/api/maps/render/getmapimage) documentação.
* Para saber mais sobre o serviço de dados do Azure Maps, consulte a [documentação de serviço](https://docs.microsoft.com/rest/api/maps/data).

