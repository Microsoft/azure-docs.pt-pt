---
title: Torne os dados personalizados num mapa raster | Microsoft Azure Maps
description: Aprenda a adicionar pinos, rótulos e formas geométricas a um mapa raster. Veja como utilizar o serviço de imagem estática no Azure Maps para este fim.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 5c70835c11bafb3fd06645ba51099b33d1eb6149
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96906085"
---
# <a name="render-custom-data-on-a-raster-map"></a>Renderizar dados personalizados em um mapa raster

Este artigo explica como usar o serviço de [imagem estática,](/rest/api/maps/render/getmapimage)com funcionalidade de composição de imagem, para permitir sobreposições em cima de um mapa raster. A composição da imagem inclui a capacidade de obter um azulejo raster, com dados adicionais como pinos personalizados, etiquetas e sobreposições de geometria.

Para fazer pinos personalizados, etiquetas e sobreposições de geometria, pode utilizar a aplicação Do Carteiro. Pode utilizar [APIs do Serviço de Dados](/rest/api/maps/data) Azure Maps para armazenar e render sobreposições.

> [!Tip]
> É muitas vezes muito mais rentável usar o Azure Maps Web SDK para mostrar um mapa simples numa página web do que usar o serviço de imagem estática. A web SDK usa azulejos de mapa e, a menos que o utilizador faça o painel e faça zooms no mapa, muitas vezes geram apenas uma fração de uma transação por carga de mapa. Note que a Web SDK Azure Maps tem opções para desativar panorâmicas e zooming. Além disso, a web SDK Azure Maps fornece um conjunto mais rico de opções de visualização de dados do que um serviço web de mapa estático.  

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps

Para completar os procedimentos neste artigo, primeiro precisa de criar uma conta Azure Maps e obter a chave da conta dos seus mapas. Siga as instruções na [Criar uma conta](quick-demo-map-app.md#create-an-azure-maps-account) para criar uma subscrição de conta Azure Maps e seguir os passos na chave [principal](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave principal para obter a chave principal para a sua conta. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](./how-to-manage-authentication.md)


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Renderiza pinos com etiquetas e uma imagem personalizada

> [!Note]
> O procedimento nesta secção requer uma conta Azure Maps no nível de preços S0 ou S1.

A conta Azure Maps S0 suporta apenas uma única instância do `pins` parâmetro. Permite-lhe renderizar até cinco pinos, especificados no pedido url, com uma imagem personalizada.

Para fazer pinos com etiquetas e uma imagem personalizada, complete estes passos:

1. Crie uma coleção na qual guarde os pedidos. Na aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Coleção**. Nomeie a coleção e selecione o botão **Criar.** 

2. Para criar o pedido, selecione **New** novamente. Na janela **Criar Novo,** selecione **Request**. Introduza um **nome de pedido** para os pinos de pressão. Selecione a coleção que criou no passo anterior, como o local para guardar o pedido. Em seguida, **selecione Save**.
    
    ![Criar um pedido no Carteiro](./media/how-to-render-custom-data/postman-new.png)

3. Selecione o método GET HTTP no separador construtor e introduza o seguinte URL para criar um pedido GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Aqui está a imagem resultante:

    ![Um pushpin personalizado com uma etiqueta](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Obtenha dados do armazenamento de dados do Azure Maps

> [!Note]
> O procedimento nesta secção requer uma conta Azure Maps no nível de preços S1.

Também pode obter as informações de localização do caminho e pin, utilizando a [API de upload de dados.](/rest/api/maps/data/uploadpreview) Siga os passos abaixo para fazer o upload dos dados do caminho e dos pinos.

1. Na aplicação Postman, abra um novo separador na coleção que criou na secção anterior. Selecione o método POST HTTP no separador construtor e introduza o seguinte URL para fazer um pedido de CORREIO:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. No **separador Params, insira** os seguintes pares de tecla/valor, que são utilizados para o URL de pedido DEM. Substitua o `subscription-key` valor pela sua chave de subscrição Azure Maps.
    
    ![Parames chave/valor no Carteiro](./media/how-to-render-custom-data/postman-key-vals.png)

3. No **separador 'Corpo',** selecione o formato de entrada bruta e escolha o JSON como o formato de entrada da lista de dropdown. Forneça este JSON como dados a serem carregados:
    
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

4. Selecione **Enviar** e rever o cabeçalho de resposta. Após um pedido bem sucedido, o cabeçalho de localização conterá o estado URI para verificar o estado atual do pedido de upload. O status URI seria do seguinte formato.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Copie o seu status URI e adic peça-lhe o parâmetro da chave de subscrição com o valor da sua chave de subscrição Azure Maps. Utilize a mesma chave de subscrição de conta que usou para fazer o upload dos dados. O formato URI de estado deve parecer-se com o abaixo:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. Para obter o udId, abra um novo separador na aplicação Do Carteiro. Selecione o método GET HTTP no separador construtor. Faça um pedido GET no status URI. Se o seu upload de dados tiver sido bem sucedido, receberá um udId no corpo de resposta. Copie o udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Utilize o `udId` valor recebido da API de upload de dados para renderizar funcionalidades no mapa. Para tal, abra um novo separador na coleção que criou na secção anterior. Selecione o método GET HTTP no separador construtor, substitua a {chave de subscrição} e {udId} pelos seus valores e introduza este URL para fazer um pedido GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Aqui está a imagem de resposta:

    ![Obtenha dados do armazenamento de dados do Azure Maps](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Renderizar um polígono com cor e opacidade

> [!Note]
> O procedimento nesta secção requer uma conta Azure Maps no nível de preços S1.


Pode modificar a aparência de um polígono utilizando modificadores de estilo com o [parâmetro do caminho](/rest/api/maps/render/getmapimage#uri-parameters).

1. Na aplicação Postman, abra um novo separador na coleção que criou anteriormente. Selecione o método GET HTTP no separador construtor e introduza o seguinte URL para configurar um pedido GET para fazer um polígono com cor e opacidade:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Aqui está a imagem de resposta:

    ![Renderizar um polígono opaco](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Rendererá um círculo e pinos com etiquetas personalizadas

> [!Note]
> O procedimento nesta secção requer uma conta Azure Maps no nível de preços S1.


Pode modificar a aparência dos pinos adicionando modificadores de estilo. Por exemplo, para fazer pinos e suas etiquetas maiores ou menores, utilize o `sc` modificador de "estilo de escala". Este modificador tem um valor superior a zero. Um valor de 1 é a escala padrão. Valores maiores do que 1 tornarão os pinos maiores, e valores menores que 1 os tornarão menores. Para obter mais informações sobre modificadores de estilo, consulte [os parâmetros do percurso do serviço de imagem estática](/rest/api/maps/render/getmapimage#uri-parameters).


Siga estes passos para fazer um círculo e pinos com etiquetas personalizadas:

1. Na aplicação Postman, abra um novo separador na coleção que criou anteriormente. Selecione o método GET HTTP no separador construtor e introduza este URL para fazer um pedido GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Aqui está a imagem de resposta:

    ![Renderiza um círculo com pinos personalizados](./media/how-to-render-custom-data/circle-custom-pins.png)

2. Para alterar a cor dos pinos do último passo, mude o modificador de estilo "co". Olhe `pins=default|la15+50|al0.66|lc003C62|co002D62|` para, a cor atual seria especificada como #002D62 em CSS. Digamos que quer mudá-lo para #41d42a. Escreva o novo valor de cor após o especificador "co", como este: `pins=default|la15+50|al0.66|lc003C62|co41D42A|` . Faça um novo pedido GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Aqui está a imagem de resposta depois de mudar as cores dos pinos:

    ![Renderize um círculo com pinos atualizados](./media/how-to-render-custom-data/circle-updated-pins.png)

Da mesma forma, pode alterar, adicionar e remover outros modificadores de estilo.

## <a name="next-steps"></a>Passos seguintes


* Explore a documentação [AZure Maps Obter API de imagem de mapa.](/rest/api/maps/render/getmapimage)
* Para saber mais sobre o serviço de dados Azure Maps (Pré-visualização), consulte a documentação do [serviço.](/rest/api/maps/data)