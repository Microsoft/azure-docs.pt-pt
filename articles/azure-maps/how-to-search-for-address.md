---
title: Procure uma localização utilizando os serviços de pesquisa do Azure Maps
description: Saiba mais sobre o serviço de pesquisa Azure Maps. Veja como usar este conjunto de APIs para geocoding, geocoding invertido, pesquisas difusas e buscas inversas nas ruas.
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/19/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: dddf56edf2037d87a28589a59834db32f8d04a4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98598364"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Procure uma localização utilizando os serviços de pesquisa do Azure Maps

O [Azure Maps Search Service](/rest/api/maps/search) é um conjunto de APIs RESTful projetado para ajudar os desenvolvedores a pesquisar endereços, locais e listas de negócios por nome, categoria e outras informações geográficas. Além de apoiar a geocodagem tradicional, os serviços também podem reverter endereços de geocódigo e atravessar ruas com base em latitudes e longitudes. Os valores de latitude e longitude devolvidos pela pesquisa podem ser usados como parâmetros em outros serviços Azure Maps, tais como serviços [De Rota](/rest/api/maps/route) e [Meteorologia.](/rest/api/maps/weather)


Neste artigo, vai aprender a:

* Solicite coordenadas de latitude e longitude para um endereço (localização do endereço geocódigo) utilizando a [API do Endereço de Pesquisa.]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
* Procure um endereço ou Ponto de Interesse (POI) utilizando a [API de Pesquisa Fuzzy](/rest/api/maps/search/getsearchfuzzy).
* Faça uma [pesquisa de endereço inverso](/rest/api/maps/search/getsearchaddressreverse) para traduzir a localização da coordenada para o endereço de rua.
* Traduza a localização da coordenada numa rua transversal compreensível humana utilizando [o Endereço de Pesquisa Reverse Cross Street API](/rest/api/maps/search/getsearchaddressreversecrossstreet).  Na maioria das vezes, isto é necessário em aplicações de rastreamento que recebem um feed GPS de um dispositivo ou ativo, e deseja saber onde a coordenada está localizada.

## <a name="prerequisites"></a>Pré-requisitos

1. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição.

Este tutorial usa a aplicação [Do Carteiro,](https://www.postman.com/) mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Solicitar latitude e longitude para um endereço (geocoding)

Neste exemplo, usaremos o Azure Maps [Obter Endereço de Pesquisa API](/rest/api/maps/search/getsearchaddress) para converter um endereço em coordenadas de latitude e longitude. Este processo também é chamado *de geocoding.* Além da devolução das coordenadas, a resposta devolverá também propriedades detalhadas de endereços como a rua, código postal, município e informação de país/região.

>[!TIP]
>Se tiver um conjunto de endereços para geocódigo, pode utilizar a API do [Lote de Endereço de Pesquisa Pós-Pesquisa](/rest/api/maps/search/postsearchaddressbatch) para enviar um lote de consultas numa única chamada API.

1. Abra a aplicação do Carteiro. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar.** Você usará esta coleção para o resto dos exemplos neste documento.

2. Para criar o pedido, selecione **New** novamente. Na janela **Criar Novo,** selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção criada no passo anterior e, em seguida, **selecione Guardar**.

3. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Neste pedido, estamos à procura de um endereço específico: `400 Braod St, Seattle, WA 98109` . Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária.

    ```http
    https://atlas.microsoft.com/search/address/json?&subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&language=en-US&query=400 Broad St, Seattle, WA 98109
    ```

4. Clique no botão **de enviar** azul. O corpo de resposta conterá dados para um único local.

5. Vamos procurar um endereço que tenha mais de uma possível localização. Na secção **Params,** mude `query` a chave para `400 Broad, Seattle` . Clique no botão **de enviar** azul.

    :::image type="content" source="./media/how-to-search-for-address/search-address.png" alt-text="Procurar endereço":::

6. Em seguida, tente definir a `query` chave para `400 Broa` .

7. Clique no botão **Enviar.** Pode agora ver que a resposta inclui respostas de vários países. Para os resultados da Geobias para a área relevante para os seus utilizadores, adicione sempre o maior número possível de detalhes de localização ao pedido.

## <a name="using-fuzzy-search-api"></a>Utilização de API de Pesquisa Fuzzy

A Azure Maps [Fuzzy Search API](/rest/api/maps/search/getsearchfuzzy) suporta pesquisas padrão de linha única e formulário livre. Recomendamos que utilize a Azure Maps Search Fuzzy API quando não conhece o seu tipo de entrada do utilizador para um pedido de pesquisa.  A entrada de consulta pode ser um endereço completo ou parcial. Também pode ser um símbolo do Ponto de Interesse (POI), como um nome de POI, categoria POI ou nome de marca. Além disso, para melhorar a relevância dos seus resultados de pesquisa, os resultados da consulta podem ser limitados por uma localização de coordenadas e raio, ou definindo uma caixa de delimitação.

>[!TIP]
>A maioria das consultas de pesquisa padrão para maxFuzzyLevel=1 para obter desempenho e reduzir resultados incomuns. Pode ajustar os níveis de fuzziness utilizando os `maxFuzzyLevel` parâmetros ou `minFuzzyLevel` parâmetros. Para obter mais informações `maxFuzzyLevel` e uma lista completa de todos os parâmetros opcionais, consulte os [parâmetros URI de pesquisa fuzzy](/rest/api/maps/search/getsearchfuzzy#uri-parameters)

### <a name="search-for-an-address-using-fuzzy-search"></a>Procure um endereço usando a Pesquisa Fuzzy

Neste exemplo, usaremos a Fuzzy Search para procurar em todo o `pizza` mundo.  Então, vamos mostrar-lhe como procurar o âmbito de um país específico. Finalmente, vamos mostrar-lhe como usar uma localização de coordenadas e raio para analisar uma pesquisa sobre uma área específica, e limitar o número de resultados devolvidos.

>[!IMPORTANT]
>Para os resultados da geobias para a área relevante para os seus utilizadores, adicione sempre o máximo de detalhes de localização possível. Para saber mais, consulte [as Melhores Práticas de Pesquisa.](how-to-use-best-practices-for-search.md#geobiased-search-results)

1. Abra a aplicação Carteiro, clique em **Novo** e selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção criada na secção anterior ou crie uma nova e, em seguida, **selecione Guardar**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária.

    ```http
   https://atlas.microsoft.com/search/fuzzy/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=pizza
    ```

    >[!NOTE]
    >O atributo _json_ no caminho URL determina o formato de resposta. Este artigo usa json para facilitar o uso e a legibilidade. Para encontrar outros formatos de resposta suportados, consulte a definição de `format` parâmetro na [documentação de referência do parâmetro URI](/rest/api/maps/search/getsearchfuzzy#uri-parameters).

3. Clique **em Enviar** e rever o corpo de resposta.

    A cadeia de consulta ambígua para "pizza" devolveu 10 [pontos de interesse](/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) nas categorias "pizza" e "restaurante". Cada resultado inclui detalhes como endereço de rua, valores de latitude e longitude, ver porta e pontos de entrada para a localização. Os resultados são agora variados para esta consulta, e não estão ligados a qualquer local de referência.
  
    No passo seguinte, usaremos o `countrySet` parâmetro para especificar apenas os países/regiões para os quais a sua aplicação precisa de cobertura. Para obter uma lista completa de países/regiões apoiados, consulte [a Cobertura de Pesquisa](./geocoding-coverage.md).

4. O comportamento padrão é procurar em todo o mundo, potencialmente devolvendo resultados desnecessários. A seguir, vamos procurar pizza apenas nos Estados Unidos. Adicione a `countrySet` chave à secção **Params** e defina o seu valor para `US` . Definir a `countrySet` chave para `US` vincular os resultados aos Estados Unidos.

    :::image type="content" source="./media/how-to-search-for-address/search-fuzzy-country.png" alt-text="Pesquisa de pizza nos Estados Unidos":::

    Os resultados estão agora limitados pelo código do país e a consulta devolve restaurantes de pizza nos Estados Unidos.

5. Para obter uma pesquisa ainda mais direcionada, você pode pesquisar o alcance de um lat./lon. par de coordenadas. Neste exemplo, vamos usar o lat./lon. da Agulha Espacial de Seattle. Já que só queremos devolver os resultados num raio de 400 metros, vamos adicionar o `radius` parâmetro. Além disso, vamos adicionar o `limit` parâmetro para limitar os resultados aos cinco lugares de pizza mais próximos.

    Na secção **Params,** adicione os seguintes pares de teclas/valor:

     | Chave | Valor |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |
    | raio | 400 |
    | limit | 5|

6. Clique em **Enviar**. A resposta inclui resultados para restaurantes de pizza perto da Seattle Space Needle.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Procure um endereço de rua utilizando a Pesquisa de Endereços Reverso

O Azure Maps [Obter Endereço de Pesquisa API reverso]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) traduz coordenadas em endereços de rua legíveis humanos. Esta API é frequentemente utilizada para aplicações que consomem feeds de GPS e querem descobrir endereços em pontos de coordenadas específicos.

>[!IMPORTANT]
>Para os resultados da geobias para a área relevante para os seus utilizadores, adicione sempre o máximo de detalhes de localização possível. Para saber mais, consulte [as Melhores Práticas de Pesquisa.](how-to-use-best-practices-for-search.md#geobiased-search-results)

>[!TIP]
>Se tiver um conjunto de localizações de coordenadas para reverter o geocódigo, pode utilizar a [API do Lote Inverso do Endereço de Pesquisa De Pós-Pesquisa](/rest/api/maps/search/postsearchaddressreversebatch) para enviar um lote de consultas numa única chamada API.

Neste exemplo, vamos fazer pesquisas inversas usando alguns dos parâmetros opcionais que estão disponíveis. Para obter a lista completa dos parâmetros opcionais, consulte [os parâmetros de pesquisa reversos.](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)

1. Na aplicação Carteiro, clique em **Novo** e selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção criada na primeira secção ou crie uma nova e, em seguida, **selecione Save**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária. O pedido deve parecer-se com o seguinte URL:

    ```http
    https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700&number=1
    ```

3. Clique **em Enviar** e rever o corpo de resposta. Devia ver um resultado de consulta. A resposta inclui informações de endereço chave sobre o Campo Safeco.
  
4. Agora, vamos adicionar os seguintes pares de chave/valor à secção **Params:**

    | Chave | Valor | Devoluções
    |-----|------------|------|
    | número | 1 |A resposta pode incluir o lado da rua (Esquerda/Direita) e também uma posição de compensação para o número.|
    | retornaSpeitLimit | true | Devolve o limite de velocidade no endereço.|
    | returnRoadUse | true | Devoluções de tipos de uso da estrada no endereço. Para todos os tipos possíveis de utilização rodoviária, consulte [os tipos de utilização rodoviária.](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)|
    | retornadoMatchType | true| Devolve o tipo de jogo. Para todos os valores possíveis, consulte [resultados de pesquisa de endereços invertidos](/rest/api/maps/search/getsearchaddressreverse#searchaddressreverseresult)

   :::image type="content" source="./media/how-to-search-for-address/search-reverse.png" alt-text="Procure ao contrário.":::

5. Clique **em Enviar** e rever o corpo de resposta.

6. Em seguida, vamos adicionar a `entityType` chave, e definir o seu valor para `Municipality` . A `entityType` chave irá sobrepor-se à `returnMatchType` chave no passo anterior. Também precisamos de remover `returnSpeedLimit` e `returnRoadUse` já que estamos a pedir informações sobre o município.  Para todos os tipos possíveis de entidades, consulte [Os Tipos de Entidade.](/rest/api/maps/search/getsearchaddressreverse#entitytype)

    :::image type="content" source="./media/how-to-search-for-address/search-reverse-entity-type.png" alt-text="Pesquisar entidade inversaType.":::

7. Clique em **Enviar**. Compare os resultados com os resultados devolvidos no passo 5.  Como o tipo de entidade solicitada é `municipality` agora, a resposta não inclui informações de endereço de rua. Além disso, o devolvido `geometryId` pode ser usado para solicitar o polígono de fronteira através do Azure Maps Obter [API de Poligon de Pesquisa.](/rest/api/maps/search/getsearchpolygon)

>[!TIP]
>Para obter mais informações sobre estes parâmetros, bem como para aprender sobre os outros, consulte a [secção Parâmetros de Pesquisa Inversa](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).

## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Pesquisa por cross street usando Reverse Address Cross Street Search

Neste exemplo, vamos procurar uma rua transversal baseada nas coordenadas de um endereço.

1. Na aplicação Carteiro, clique em **Novo** e selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção criada na primeira secção ou crie uma nova e, em seguida, **selecione Save**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária. O pedido deve parecer-se com o seguinte URL:
  
    ```http
   https://atlas.microsoft.com/search/address/reverse/crossstreet/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700
    ```

    :::image type="content" source="./media/how-to-search-for-address/search-address-cross.png" alt-text="Procurem na rua.":::
  
3. Clique **em Enviar** e rever o corpo de resposta. Vai notar que a resposta contém um `crossStreet` valor de `South Atlantic Street` .

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Azure Maps Serviço de Pesquisa REST API](/rest/api/maps/search)

> [!div class="nextstepaction"]
> [Azure Maps Search Service As melhores práticas](how-to-use-best-practices-for-search.md)