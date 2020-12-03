---
title: Solicite dados de elevação utilizando o serviço Azure Maps Elevação
description: Saiba como solicitar dados de elevação utilizando o serviço Azure Maps Elevation.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9937d72b44eb33df8027eddb9a9f500a372c9037
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554265"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service"></a>Solicite dados de elevação utilizando o serviço Azure Maps Elevação

O serviço Azure Maps [Elevation](https://docs.microsoft.com/rest/api/maps/elevation) fornece APIs para consultar dados de elevação para locais na Terra. Pode solicitar dados de elevação amostrados ao longo de caminhos, dentro de uma caixa de delimitação definida, ou em coordenadas específicas. Além disso, pode utilizar o [Render V2 - Obter API de Azulejos do Mapa](https://docs.microsoft.com/rest/api/maps/renderv2) para recuperar dados de elevação em formato de azulejo. Os azulejos são entregues em formato GeoTIFF raster. Este artigo mostra-lhe como usar o serviço Azure Maps Elevação e a API get tile do mapa para solicitar dados de elevação. Os dados de elevação podem ser solicitados tanto nos formatos GeoJSON como no GeoTiff.

## <a name="prerequisites"></a>Pré-requisitos

1. [Faça uma conta Azure Maps no nível de preços S1](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição.

Para mais informações sobre a autenticação em Azure Maps, [Gerencie a autenticação no Azure Maps.](how-to-manage-authentication.md)

Este artigo usa a aplicação [Do Carteiro,](https://www.postman.com/) mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="request-elevation-data-in-raster-tiled-format"></a>Solicite dados de elevação em formato de azulejos rasterizadoos

Para solicitar dados de elevação em formato de azulejo raster, utilize o [Render V2 - Get Map Tile API](https://docs.microsoft.com/rest/api/maps/renderv2). Se o azulejo puder ser encontrado, a API devolve o azulejo como Um GeoTIFF. Caso contrário, a API retorna 0. Todos os azulejos RASTER DEM estão a usar o modo Terra geoid (nível do mar). Neste exemplo, vamos solicitar dados de elevação para o Mt. O Everest.

>[!TIP]
>Para recuperar um azulejo numa área específica do mapa mundial, você precisará encontrar o azulejo correto no nível de zoom apropriado. Note-se também que o WorldDEM cobre toda a massa terrestre global, mas não cobre os oceanos.  Para obter mais informações, consulte [os níveis de Zoom e a grelha de azulejos.](zoom-levels-and-tile-grid.md)

1. Abra a aplicação do Carteiro. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar.**

2. Para criar o pedido, selecione **New** novamente. Na janela **Criar Novo,** selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção criada no passo anterior e, em seguida, **selecione Guardar**.

3. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL para solicitar o azulejo raster. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária.

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

4. Clique no botão **Enviar.** Deverá receber o azulejo raster que contém os dados de elevação no formato GeoTIFF. Cada pixel dentro do azulejo raster é do tipo `float` . O valor de cada pixel representa a altura de elevação nos metros.

## <a name="request-elevation-data-in-geojson-format"></a>Solicite dados de elevação no formato GeoJSON

Utilize as APIs do serviço elevação para solicitar dados de elevação no formato GeoJSON. Esta secção irá mostrar-lhe cada uma das três APIs:

* [Obter dados para pontos](https://docs.microsoft.com/rest/api/maps/elevation/getdataforlatlongcoordinates)
* [Post Dados para Pontos](https://docs.microsoft.com/rest/api/maps/elevation/postdataforlatlongcoordinates)
* [Obter Dados para Poliline](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline)
* [Post Dados para Poliline](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline)
* [Obtenha dados para a caixa de limites](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> Quando nenhum dado pode ser devolvido, todas as APIs `0` regressam.

### <a name="request-elevation-data-for-points"></a>Solicite dados de elevação para pontos

Neste exemplo, usaremos o [Get Data for Points API](https://docs.microsoft.com/rest/api/maps/elevation/getdataforlatlongcoordinates) para solicitar dados de elevação no Mt. Montanhas Everest e Chamlang. Em seguida, usaremos os [Dados Post para Pontos API](https://docs.microsoft.com/rest/api/maps/elevation/postdataforlatlongcoordinates) para solicitar dados de elevação usando os mesmos dois pontos. Espera-se que latitudes e longitudes no URL estejam em grau decimal WGS84 (World Geodetic System).

 >[!IMPORTANT]
 >Devido ao limite de comprimento do personagem URL de 2048, não é possível passar mais de 100 coordenadas como uma cadeia delimitada de pipeline num pedido URL GET. Se pretender passar mais de 100 coordenadas como uma cadeia delimitada por gasoduto, utilize os dados post para pontos.

1. Para criar o pedido, selecione **New** novamente. Na janela **Criar Novo,** selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção criada no passo anterior e, em seguida, **selecione Guardar**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

3. Clique no botão **Enviar.**  Receberá a seguinte resposta JSON:

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

4. Agora, vamos ligar para o [Post Data for Points API](https://docs.microsoft.com/rest/api/maps/elevation/postdataforlatlongcoordinates) para obter dados de elevação para os mesmos dois pontos. Selecione o método **POST** HTTP no separador construtor e introduza o seguinte URL. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Nos **Cabeçalhos** do pedido **DOM,** definido `Content-Type` para `application/json` . No **Corpo,** forneça as informações do ponto de coordenada abaixo. Quando terminar, clique em **Enviar.**

     ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

### <a name="request-elevation-data-samples-along-a-polyline"></a>Solicite amostras de dados de elevação ao longo de uma Linha Poliline

Neste exemplo, usaremos o [Get Data for Polyline](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline) para solicitar cinco amostras igualmente espaçadas de dados de elevação ao longo de uma linha reta entre coordenadas no Mt. Montanhas Everest e Chamlang. Ambas as coordenadas devem ser definidas no formato Long/Lat. Se não especificar um valor para o `samples` parâmetro, o número de amostras é de 10. O número máximo de amostras é de 2.000.

Em seguida, usaremos o Get Data for Polyline para solicitar três amostras igualmente espaçadas de dados de elevação ao longo de um caminho. Vamos definir a localização exata para as amostras passando em três pares de coordenadas Long/Lat.

Finalmente, usaremos os [dados post para a API polilina](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline) para solicitar dados de elevação nas mesmas três amostras igualmente espaçadas.

Espera-se que latitudes e longitudes no URL estejam em grau decimal WGS84 (World Geodetic System).

 >[!IMPORTANT]
 >Devido ao limite de comprimento do personagem URL de 2048, não é possível passar mais de 100 coordenadas como uma cadeia delimitada de pipeline num pedido URL GET. Se pretender passar mais de 100 coordenadas como uma cadeia delimitada por gasoduto, utilize os dados post para pontos.

1. Selecione **Nova**. Na janela **Criar Novo,** selecione **Request**. Insira um **nome De pedido** e selecione uma coleção. Clique em **Guardar**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária.

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

3. Clique no botão **Enviar.**  Receberá a seguinte resposta JSON:

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

4. Agora, vamos pedir três amostras de dados de elevação ao longo de um caminho entre coordenadas nas montanhas Do Monte Everest, Chamlang e Jannu. Na secção **Params,** copie o conjunto de coordenadas a seguir para o valor da `lines` chave de consulta.

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

5. Altere o `samples` valor chave de consulta para `3` .  A imagem abaixo mostra os novos valores.

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="Recupere três amostras de dados de elevação.":::

6. Clique no botão **de enviar** azul. Receberá a seguinte resposta JSON:

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

7. Agora, vamos ligar para o [Post Data for Polyline API](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline) para obter dados de elevação para os mesmos três pontos. Selecione o método **POST** HTTP no separador construtor e introduza o seguinte URL. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária.

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

8. Nos **Cabeçalhos** do pedido **DOM,** definido `Content-Type` para `application/json` . No **Corpo,** forneça as informações do ponto de coordenada abaixo. Quando terminar, clique em **Enviar.**

     ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

### <a name="request-elevation-data-by-bounding-box"></a>Solicite dados de elevação por Bounding Box

Agora vamos usar o [Get Data for Bounding Box](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox) para solicitar dados de elevação perto de Mt. Rainier, WA. Os dados de elevação serão devolvidos em locais igualmente espaçados dentro de uma caixa de delimitação. A área de delimitação definida por (2) conjuntos de coordenadas lat/long (latitude sul, longitude oeste / latitude norte, longitude leste) é dividida em linhas e colunas. As bordas da caixa de delimitação representam duas (2) das linhas e duas (2) das colunas. As elevações são devolvidas para os vértices da grelha criados nas intersecções de linha e colunas. Até 2000 elevações podem ser devolvidas num único pedido.

Neste exemplo, especificaremos linhas=3 e colunas=6. 18 valores de elevação são devolvidos na resposta. No diagrama seguinte, os valores de elevação são ordenados começando pelo canto sudoeste, e depois continuar de oeste para leste e sul para norte.  Os pontos de elevação estão numerados na ordem de serem devolvidos.

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="As coordenadas da caixa de limitação nos cantos NE e SE.":::

1. Selecione **Nova**. Na janela **Criar Novo,** selecione **Request**. Insira um **nome De pedido** e selecione uma coleção. Clique em **Guardar**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária.

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

3. Clique no botão **de enviar** azul. 18 amostras de dados de elevação, uma para cada vértice da grelha, são devolvidas na resposta.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-apis-in-azure-maps-control"></a>Amostras: Use APIs de serviço de elevação no controlo de mapas Azure

### <a name="get-elevation-data-by-coordinate-position"></a>Obtenha dados de elevação por posição de coordenada

A página web da amostra que se segue mostra como usar o controlo do mapa para exibir dados de elevação num ponto de coordenadas. Quando o utilizador arrastar o marcador, o mapa apresentará os dados de elevação num pop-up.

<br/>

<iframe height="500" style="width:100%;" scrolling="no" title="Obter elevação na posição" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>pen Obter elevação na posição</a> por Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>Obtenha dados de elevação através da caixa de delimitação

A página web da amostra que se segue mostra como usar o controlo do mapa para exibir dados de elevação contidos numa caixa de delimitação. O utilizador define a caixa de delimitação clicando no `square` ícone no canto superior esquerdo e desenhando o quadrado em qualquer lugar do mapa. O controlo do mapa irá então tornar os dados de elevação de acordo com as cores especificadas na chave que está localizada no canto superior direito.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Elevações por caixa de delimitação" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>Elevações pen por limite de</a> Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>Obtenha dados de elevação pelo caminho PolyLine

A página web da amostra que se segue mostra como usar o controlo do mapa para exibir dados de elevação ao longo de um caminho. O utilizador define o caminho clicando no `PolyLine` ícone no canto superior esquerdo e desenhando o PolyLine no mapa. O controlo do mapa torna então os dados de elevação em cores especificadas na chave localizada no canto superior direito.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Gradiente do caminho de elevação" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>gradiente do caminho de elevação da</a> pen por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos seguintes

Para explorar ainda mais as APIs de Elevação de Mapas Azure, consulte:

> [!div class="nextstepaction"]
> [Elevação - Obtenha dados para coordenadas lat long](https://docs.microsoft.com/rest/api/maps/elevation/getdataforlatlongcoordinates)

> [!div class="nextstepaction"]
> [Elevação - Obtenha dados para a caixa de limites](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [Elevação - Obter dados para Poliline](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [Render V2 – Get Map Tile](https://docs.microsoft.com/rest/api/maps/renderv2)

Para obter uma lista completa das APIs de REST do Azure Maps, consulte:

> [!div class="nextstepaction"]
> [Azure Maps REST APIs](https://docs.microsoft.com/rest/api/maps/)
