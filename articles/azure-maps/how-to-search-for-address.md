---
title: Pesquisar locais usando o Azure Maps Serviço de Pesquisa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a procurar um local usando os mapas de Microsoft Azure Serviço de Pesquisa.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 20a2c18875096680cd1eba7601e88965fcbcc568
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715348"
---
# <a name="using-azure-maps-search-services-for-geocoding-and-reverse-geocoding"></a>Utilização de serviços de pesquisa de mapas azure para geocodificação e geocodificação inversa

O Serviço de [Pesquisa](https://docs.microsoft.com/rest/api/maps/search) de Mapas Azure é um conjunto de APIs RESTful projetados para ajudar os desenvolvedores a pesquisar endereços, lugares, listas de negócios por nome ou categoria, e outras informações geográficas. Além de apoiar a geocodificação tradicional, os serviços também podem reverter endereços de geocódigo e atravessar ruas com base em latitudes e longitudes. Os valores de latitude e longitude devolvidos pela pesquisa podem ser usados como parâmetros em outros serviços do Azure Maps, como os serviços [Route](https://docs.microsoft.com/rest/api/maps/route) e [Weather.](https://docs.microsoft.com/rest/api/maps/weather)

Vamos aprender, como:

* Solicite coordenadas de latitude e longitude para um endereço (localização de endereço de geocódigo) utilizando [a API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) do endereço de busca
* Pesquisar por um endereço ou Ponto de Interesse (POI) utilizando a API de [pesquisa fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Pesquisar um endereço juntamente com propriedades e coordenadas
* Faça uma Pesquisa de [Endereço Inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para traduzir localização de coordenadas para endereço de rua
* Procurar uma cruzada usando o [endereço de pesquisa API de várias ruas inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste artigo, é necessário criar primeiro uma conta Azure Maps e obter-lhe a chave de subscrição da conta. Siga as instruções em [criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma assinatura de conta do Azure Maps e siga as etapas em [obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave primária para sua conta. Para obter mais informações sobre autenticação no Azure Maps, consulte [gerenciar a autenticação no Azure Maps](./how-to-manage-authentication.md).

Este artigo usa o [aplicativo de postmaster](https://www.getpostman.com/apps) para criar chamadas REST. Você pode usar qualquer ambiente de desenvolvimento de API que preferir.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Solicitar latitude e longitude para um endereço (geocoding)

Neste exemplo, estamos a usar a [API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) do Endereço de Busca Do Azure Maps para converter endereços de rua em coordenadas de latitude e longitude. Pode passar um endereço de rua completo ou parcial à API e receber uma resposta que inclua propriedades de endereços detalhados, tais como ruas, código postal e país/região, bem como valores posicionais em latitude e longitude.

Se tiver um conjunto de endereços para o geocódigo, pode utilizar a API do lote de [endereços postais](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) para enviar um lote de consultas numa única chamada API.

1. No postmaster, clique em **nova solicitação** | **obter solicitação** e nomeie-a na **pesquisa de endereço**.

2. Na guia Construtor, selecione o método http **Get** , insira a URL de solicitação para seu ponto de extremidade de API e selecione um protocolo de autorização, se houver.

![Pesquisa de endereço](./media/how-to-search-for-address/address_search_url.png)

| Parâmetro | Valor sugerido |
|---------------|------------------------------------------------| 
| Método HTTP | GET |
| URL do Pedido | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| Autorização | Sem Auth |

3. Clique em **params**e insira os seguintes pares de chave/valor para usar como parâmetros de consulta ou caminho na URL da solicitação: 

![Pesquisa de endereço](./media/how-to-search-for-address/address_search_params.png) 

| Chave | Valor | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| chave de assinatura | \<sua chave do Azure Maps\> | 
| consulta | 400 de Santa ampla, Seattle, WA 98109 | 

4. Clique em **Enviar** e examine o corpo da resposta. 

Nesse caso, você especificou uma consulta de endereço completa e receberá um único resultado no corpo da resposta. 

5. Em params, edite a cadeia de caracteres de consulta para o seguinte valor: 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. Adicione o seguinte par de chave/valor à seção **params** e clique em **Enviar**: 

| Chave | Valor | 
|-----|------------| 
| typeahead | true | 

O sinalizador **typeahead** informa à API de pesquisa de endereço para tratar a consulta como uma entrada parcial e retornar uma matriz de valores de previsão.

## <a name="search-for-an-address-using-fuzzy-search-api"></a>Procure um endereço usando a API de pesquisa fuzzy

O Azure Maps[ Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) é recomendado para utilizar quando não sabe quais são as suas inputs de utilizador para uma consulta de pesquisa. A API combina a pesquisa do Point of Interest (POI) e a geocodificação numa "pesquisa de linha única" canónica. Por exemplo, a API pode manipular entradas de qualquer combinação de token de endereço ou POI. Ele também pode ser ponderado com uma posição contextual (lat./Lon. par), totalmente restrito por uma coordenada e um raio, ou executados mais geralmente sem nenhum ponto de ancoragem de inclinação geográfica.

A maioria das consultas de pesquisa usa como padrão `maxFuzzyLevel=1` para obter desempenho e reduzir resultados incomuns. Esse padrão pode ser substituído conforme necessário por solicitação passando o parâmetro de consulta `maxFuzzyLevel=2` ou `3`.

### <a name="search-for-an-address-using-fuzzy-search"></a>Pesquisar um endereço usando a pesquisa difusa

1. Abra o aplicativo do postmaster, clique em novo | Crie um novo e selecione **solicitação get**. Insira um nome de solicitação de **pesquisa difusa**, selecione uma coleção ou pasta para salvar e clique em **salvar**.

2. Na guia Construtor, selecione o método http **Get** e insira a URL de solicitação para seu ponto de extremidade de API.

    ![Pesquisa difusa](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autorização | Sem Auth |

    O atributo **JSON** no caminho da URL determina o formato de resposta. Este artigo usa o JSON para facilitar o uso e a legibilidade. Você pode encontrar os formatos de resposta disponíveis na definição **obter pesquisa difusa** da referência da [API funcional do Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Clique em **params**e insira os seguintes pares de chave/valor para usar como parâmetros de consulta ou caminho na URL da solicitação:

    ![Pesquisa difusa](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | chave de assinatura | \<sua chave do Azure Maps\> |
    | consulta | pizza |

4. Clique em **Enviar** e examine o corpo da resposta.

    A cadeia de caracteres de consulta ambígua para "pizza" retornou 10 [resultados de POI (ponto de interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) ) nas categorias "pizza" e "restaurante". Cada resultado retorna um endereço de rua, valores de latitude e longitude, porta de exibição e pontos de entrada para o local.
  
    Os resultados são variados para essa consulta, não vinculados a nenhum local de referência específico. Você pode usar o parâmetro **countryset** para especificar apenas os países/regiões para os quais seu aplicativo precisa de cobertura. O comportamento padrão é Pesquisar o mundo inteiro, potencialmente retornando resultados desnecessários.

5. Adicione o seguinte par de chave/valor à seção **params** e clique em **Enviar**:

    | Chave | Valor |
    |------------------|-------------------------|
    | countrySet | EUA |
  
    Os resultados agora são limitados pelo código do país e a consulta retorna restaurantes no Estados Unidos.
  
    Para fornecer resultados para um local, você pode consultar um ponto de interesse e usar os valores de latitude e longitude retornados em sua chamada para o serviço de pesquisa difusa. Nesse caso, você usou o serviço de pesquisa para retornar o local da agulha de espaço de Seattle e usou a lat. Lon. valores para orientar a pesquisa.
  
6. Em params, insira os seguintes pares de chave/valor e clique em **Enviar**:

    ![Pesquisa difusa](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Chave | Valor |
    |-----|------------|
    | Lat | 47,620525 |
    | Lon | -122.349274 |

## <a name="search-for-address-properties-and-coordinates"></a>Pesquisar Propriedades de endereço e coordenadas

Você pode passar um endereço de rua completo ou parcial para a API de endereço de pesquisa. Você ainda receberá uma resposta que inclui propriedades de endereço detalhadas. As propriedades de endereço detalhadas são valores como valores posicionais em altitude e longitude, município ou subdivisão.

1. No postmaster, clique em **nova solicitação** | **obter solicitação** e nomeie-a na **pesquisa de endereço**.
2. Na guia Construtor, selecione o método http **Get** , insira a URL de solicitação para seu ponto de extremidade de API e selecione um protocolo de autorização, se houver.

    ![Pesquisa de endereço](./media/how-to-search-for-address/address_search_url.png)
  
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) |
    | Autorização | Sem Auth |

3. Clique em **params**e insira os seguintes pares de chave/valor para usar como parâmetros de consulta ou caminho na URL da solicitação:
  
    ![Pesquisa de endereço](./media/how-to-search-for-address/address_search_params.png)
  
    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | chave de assinatura | \<sua chave do Azure Maps\> |
    | consulta | 400 de Santa ampla, Seattle, WA 98109 |
  
4. Clique em **Enviar** e examine o corpo da resposta.
  
    Nesse caso, você especificou uma consulta de endereço completa e receberá um único resultado no corpo da resposta.
  
5. Em params, edite a cadeia de caracteres de consulta para o seguinte valor:
    ```plaintext
        400 Broad, Seattle
    ```

6. Adicione o seguinte par de chave/valor à seção **params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | typeahead | true |

    O sinalizador **typeahead** informa à API de pesquisa de endereço para tratar a consulta como uma entrada parcial e retornar uma matriz de valores de previsão.

## <a name="make-a-reverse-address-search"></a>Faça uma pesquisa de endereço invertido

1. No postmaster, clique em **nova solicitação** | **solicitação get** e nomeie-a como **inverter pesquisa de endereço**.

2. Na guia Construtor, selecione o método http **Get** e insira a URL de solicitação para seu ponto de extremidade de API.
  
    ![URL de pesquisa de endereço inversa](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autorização | Sem Auth |
  
3. Clique em **params**e insira os seguintes pares de chave/valor para usar como parâmetros de consulta ou caminho na URL da solicitação:
  
    ![Parâmetros de pesquisa de endereço inversa](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | chave de assinatura | \<sua chave do Azure Maps\> |
    | consulta | 47.591180,-122,332700 |
  
4. Clique em **Enviar** e examine o corpo da resposta.

    A resposta inclui informações de endereço de chave sobre o campo Safeco Field.
  
5. Adicione o seguinte par de chave/valor à seção **params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | número | true |

    Se o parâmetro de consulta [Number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) for enviado com a solicitação, a resposta poderá incluir o lado da rua (esquerda ou direita) e também uma posição de deslocamento para esse número.
  
6. Adicione o seguinte par de chave/valor à seção **params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Quando o parâmetro de consulta [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) é definido, a resposta retorna o limite de velocidade lançado.

7. Adicione o seguinte par de chave/valor à seção **params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | returnRoadUse | true |

    Quando o parâmetro de consulta [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) é definido, a resposta retorna a matriz de uso da estrada para os geocódigos inversos no nível da rua.

8. Adicione o seguinte par de chave/valor à seção **params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | roadUse | true |

    Pode restringir a consulta de geocódigo inversa a um tipo específico de estrada utilizando o parâmetro de consulta de [consulta](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) rodoviária.
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Pesquisa por cross street usando Reverse Address Cross Street Search

1. No postmaster, clique em **nova solicitação** | **solicitação get** e nomeie-a como **endereço inverso pesquisa entre as ruas**.

2. Na guia Construtor, selecione o método http **Get** e insira a URL de solicitação para seu ponto de extremidade de API.
  
    ![Pesquisa de rua cruzada de endereço reverso](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autorização | Sem Auth |
  
3. Clique em **params**e insira os seguintes pares de chave/valor para usar como parâmetros de consulta ou caminho na URL da solicitação:
  
    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | chave de assinatura | \<sua chave do Azure Maps\> |
    | consulta | 47.591180,-122,332700 |
  
4. Clique em **Enviar** e examine o corpo da resposta.

## <a name="next-steps"></a>Passos seguintes

- Explore a documentação da API do [serviço de pesquisa do Azure Maps](https://docs.microsoft.com/rest/api/maps/search) .
