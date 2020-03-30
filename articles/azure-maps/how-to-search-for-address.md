---
title: Procure um local utilizando serviços de pesquisa do Azure Maps Microsoft Azure Maps
description: Neste artigo, você aprenderá a procurar um local usando o Microsoft Azure Maps Search Service para geocodificação e geocodificação inversa.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cf0e5267885df1ace51271c53bb2d68ee5002f00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335436"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Procure um local utilizando serviços de pesquisa do Azure Maps

O Serviço de [Pesquisa](https://docs.microsoft.com/rest/api/maps/search) de Mapas Azure é um conjunto de APIs RESTful projetados para ajudar os desenvolvedores a pesquisar endereços, lugares, listas de negócios por nome ou categoria, e outras informações geográficas. Além de apoiar a geocodificação tradicional, os serviços também podem reverter endereços de geocódigo e atravessar ruas com base em latitudes e longitudes. Os valores de latitude e longitude devolvidos pela pesquisa podem ser usados como parâmetros em outros serviços do Azure Maps, como os serviços [Route](https://docs.microsoft.com/rest/api/maps/route) e [Weather.](https://docs.microsoft.com/rest/api/maps/weather)

Neste artigo aprenderá, como:

* Solicite coordenadas de latitude e longitude para um endereço (localização de endereço de geocódigo) utilizando [a API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) do endereço de busca
* Pesquisar por um endereço ou Ponto de Interesse (POI) utilizando a API de [pesquisa fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Procurar um endereço juntamente com propriedades e coordenadas
* Faça uma Pesquisa de [Endereço Inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para traduzir localização de coordenadas para endereço de rua
* Procure uma rua transversal usando [o Endereço de Busca Reverse Cross Street API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste artigo, é necessário criar primeiro uma conta Azure Maps e obter-lhe a chave de subscrição da conta. Siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma subscrição de conta Azure Maps e siga os passos para obter a [chave](quick-demo-map-app.md#get-the-primary-key-for-your-account) principal para obter a chave principal para a sua conta. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps](./how-to-manage-authentication.md).

Este artigo usa a [app Postman](https://www.getpostman.com/apps) para construir chamadas REST. Você pode usar qualquer ambiente de desenvolvimento de API que você preferir.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Solicitar latitude e longitude para um endereço (geocoding)

Neste exemplo, estamos a usar a [API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) do Endereço de Busca Do Azure Maps para converter endereços de rua em coordenadas de latitude e longitude. Pode passar um endereço de rua completo ou parcial à API e receber uma resposta que inclua propriedades de endereços detalhados, tais como ruas, código postal e país/região, bem como valores posicionais em latitude e longitude.

Se tiver um conjunto de endereços para o geocódigo, pode utilizar a API do lote de [endereços postais](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) para enviar um lote de consultas numa única chamada API.

1. No Carteiro, clique em **Novo Pedido** | **GET pedido** e nomeie-o Para Procurar **Endereço**.

2. No separador Construtor, selecione o método **GET** HTTP, introduza o URL de pedido para o seu ponto final da API e selecione um protocolo de autorização, se houver.

![Pesquisa de endereços](./media/how-to-search-for-address/address_search_url.png)

| Parâmetro | Valor sugerido |
|---------------|------------------------------------------------| 
| Método HTTP | GET |
| URL do Pedido | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| Autorização | Sem Auth |

3. Clique em **Params**e introduza os seguintes pares chave / valor para usar como parâmetros de consulta ou caminho no URL de pedido: 

![Pesquisa de endereços](./media/how-to-search-for-address/address_search_params.png) 

| Chave | Valor | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| chave de assinatura | \<a sua chave Azure Maps\> | 
| consulta | 400 Broad St, Seattle, WA 98109 | 

4. Clique em **Enviar** e rever o corpo de resposta. 

Neste caso, especificou uma consulta completa de endereço e recebeu um único resultado no corpo de resposta. 

5. Em Params, edite a corda de consulta para o seguinte valor: 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. Adicione o seguinte par chave / valor à secção **Params** e clique **Enviar:** 

| Chave | Valor | 
|-----|------------| 
| tipo de tempo | true | 

A bandeira da **tipografia** diz à API de Pesquisa de Endereços para tratar a consulta como uma entrada parcial e devolver uma série de valores preditivos.

## <a name="using-fuzzy-search-api"></a>Utilização de API de pesquisa fuzzy

O Azure Maps[ Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) é recomendado para utilizar quando não sabe quais são as suas inputs de utilizador para uma consulta de pesquisa. A API combina a pesquisa do Point of Interest (POI) e a geocodificação numa "pesquisa de linha única" canónica. Por exemplo, a API pode lidar com as inputs de qualquer endereço ou combinação de fichas POI. Também pode ser ponderado com uma posição contextual (lat./lon. par), totalmente limitado por uma coordenada e raio, ou executado de forma mais geral sem qualquer ponto de ancoragem geográfico.

A maioria das `maxFuzzyLevel=1` consultas de pesquisa padrão para obter desempenho e reduzir resultados incomuns. Este padrão pode ser ultrapassado conforme necessário por pedido, `maxFuzzyLevel=2` passando no parâmetro de consulta ou `3`.

### <a name="search-for-an-address-using-fuzzy-search"></a>Procure um endereço usando a Pesquisa Fuzzy

1. Abra a aplicação Postman, clique em Novo / Criar Novo e selecionar **pedido GET**. Introduza um nome de Pedido de **pesquisa fuzzy,** selecione uma coleção ou pasta para guardá-la e clique em **Guardar**.

2. No separador Construtor, selecione o método **GET** HTTP e introduza o URL de pedido para o seu ponto final aPi.

    ![Pesquisa fuzzy](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autorização | Sem Auth |

    O atributo **json** no caminho do URL determina o formato de resposta. Este artigo usa json para facilitar o uso e a legibilidade. Pode encontrar os formatos de resposta disponíveis na definição **Get Search Fuzzy** da [referência API funcional](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)do Maps .

3. Clique em **Params**e introduza os seguintes pares chave / valor para usar como parâmetros de consulta ou caminho no URL de pedido:

    ![Pesquisa fuzzy](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | chave de assinatura | \<a sua chave Azure Maps\> |
    | consulta | pizza |

4. Clique em **Enviar** e rever o corpo de resposta.

    A corda de consulta ambígua para "pizza" devolveu 10 [pontos de interesse resultado](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) nas categorias de "pizza" e "restaurante". Cada resultado devolve um endereço de rua, valores de latitude e longitude, vista portuária e pontos de entrada para a localização.
  
    Os resultados são variados para esta consulta, não ligados a nenhum local de referência particular. Pode utilizar o parâmetro **countrySet** para especificar apenas os países/regiões para os quais a sua aplicação necessita de cobertura. O comportamento padrão é procurar em todo o mundo, potencialmente devolvendo resultados desnecessários.

5. Adicione o seguinte par chave / valor à secção **Params** e clique **Enviar:**

    | Chave | Valor |
    |------------------|-------------------------|
    | paísSet | EUA |
  
    Os resultados estão agora limitados pelo código do país e a consulta devolve restaurantes de pizza nos Estados Unidos.
  
    Para fornecer resultados para uma localização, pode consultar um ponto de interesse e usar os valores de latitude e longitude devolvidos na sua chamada para o serviço de Pesquisa Fuzzy. Neste caso, usou o serviço de pesquisa para devolver a localização da Agulha Espacial de Seattle e usou o lat. / Lon. valores para orientar a procura.
  
6. Em Params, introduza os seguintes pares chave / valor e clique **Enviar:**

    ![Pesquisa fuzzy](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Chave | Valor |
    |-----|------------|
    | lat | 47.620525 |
    | Lon | -122.349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>Procure um endereço de rua usando a pesquisa de endereço sinuoso

O Azure Maps [Obter Endereço de Busca Reverse API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) ajuda a traduzir uma coordenada (exemplo: 37.786505, -122.3862) num endereço de rua compreensível humano. Na maioria das vezes, isto é necessário para rastrear aplicações onde recebe um feed GPS do dispositivo ou do ativo e deseja saber qual o endereço onde a coordenada está localizada.
Se tiver um conjunto de localizações coordenadas para reverter o geocódigo, pode utilizar a [API do lote](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) de pesquisa de correio para enviar um lote de consultas numa única chamada aPI.


1. No Carteiro, clique em **Novo Pedido** | **GET pedido** e nomeie-o Reverter A Procura de **Endereços**.

2. No separador Construtor, selecione o método **GET** HTTP e introduza o URL de pedido para o seu ponto final aPi.
  
    ![URL de pesquisa de endereço invertido](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autorização | Sem Auth |
  
3. Clique em **Params**e introduza os seguintes pares chave / valor para usar como parâmetros de consulta ou caminho no URL de pedido:
  
    ![Parâmetros de pesquisa de endereço invertido](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | chave de assinatura | \<a sua chave Azure Maps\> |
    | consulta | 47.591180,-122.332700 |
  
4. Clique em **Enviar** e rever o corpo de resposta.

    A resposta inclui informações de endereço chave sobre o Campo Safeco.
  
5. Adicione o seguinte par chave / valor à secção **Params** e clique **Enviar:**

    | Chave | Valor |
    |-----|------------|
    | número | true |

    Se o parâmetro de consulta [de número](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) for enviado com o pedido, a resposta pode incluir o lado da rua (Esquerda ou Direita) e também uma posição de compensação para esse número.
  
6. Adicione o seguinte par chave / valor à secção **Params** e clique **Enviar:**

    | Chave | Valor |
    |-----|------------|
    | retornoSpeedLimit | true |
  
    Quando o parâmetro de consulta [ReturnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) é definido, a resposta devolve o limite de velocidade registado.

7. Adicione o seguinte par chave / valor à secção **Params** e clique **Enviar:**

    | Chave | Valor |
    |-----|------------|
    | retornoRoadUse | true |

    Quando o parâmetro de consulta [ReturnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) é definido, a resposta devolve a matriz de utilização da estrada para geocódigos invertidos ao nível da rua.

8. Adicione o seguinte par chave / valor à secção **Params** e clique **Enviar:**

    | Chave | Valor |
    |-----|------------|
    | estradaUse | true |

    Pode restringir a consulta de geocódigo inversa a um tipo específico de estrada utilizando o parâmetro de consulta de [consulta](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) rodoviária.
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Pesquisa por cross street usando Reverse Address Cross Street Search

1. No Carteiro, clique em **New Request** | **GET request** e nomeie-o Reverter O Endereço Cross Street **Search**.

2. No separador Construtor, selecione o método **GET** HTTP e introduza o URL de pedido para o seu ponto final aPi.
  
    ![Reverter endereço Cross Street Search](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autorização | Sem Auth |
  
3. Clique em **Params**e introduza os seguintes pares chave / valor para usar como parâmetros de consulta ou caminho no URL de pedido:
  
    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | chave de assinatura | \<a sua chave Azure Maps\> |
    | consulta | 47.591180,-122.332700 |
  
4. Clique em **Enviar** e rever o corpo de resposta.

## <a name="next-steps"></a>Passos seguintes

- Explore a documentação do Serviço de [Pesquisa do Azure Maps REST API](https://docs.microsoft.com/rest/api/maps/search).
- Saiba mais sobre as [melhores práticas do Serviço](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) de Pesquisa do Azure Maps e como otimizar as suas consultas.
