---
title: Como procurar um endereço usando o serviço de pesquisa do Azure Maps | Microsoft Docs
description: Saiba como procurar um endereço usando o serviço de pesquisa do Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a41a811e9313f79c9c3165e02cb5eaa4353b65ab
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914475"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Localizar um endereço usando o serviço de pesquisa do Azure Maps

O serviço de pesquisa do Maps é um conjunto de APIs RESTful projetado para os desenvolvedores procurarem endereços, locais, pontos de interesse, listas de negócios e outras informações geográficas. O serviço atribui uma latitude/longitude a um endereço específico, entre as ruas, o recurso geográfico ou o POI (ponto de interesse). Os valores de latitude e longitude retornados pela pesquisa podem ser usados como parâmetros em outros serviços de mapas, como rota e fluxo de tráfego.

Neste artigo, você aprenderá a:

* Pesquisar um endereço usando a [API de pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Pesquisar um endereço juntamente com propriedades e coordenadas
* Fazer uma [pesquisa de endereço inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para pesquisar um endereço
* Procurar uma cruzada usando o [endereço de pesquisa API de várias ruas inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Pré-requisitos

Para fazer chamadas para as APIs de serviço do Maps, você precisa de uma conta e chave do Maps. Para obter informações sobre como criar uma conta, siga as instruções em [gerenciar conta](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) e siga as etapas em [obter chave primária](./tutorial-search-location.md#getkey) para recuperar uma chave de assinatura primária para sua conta.

Este artigo usa o [aplicativo de postmaster](https://www.getpostman.com/apps) para criar chamadas REST. Você pode usar qualquer ambiente de desenvolvimento de API que preferir.

## <a name="using-fuzzy-search"></a>Usando a pesquisa difusa

A API padrão para o serviço de pesquisa é a [pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) e é útil quando você não sabe quais são suas entradas de usuário para uma consulta de pesquisa. A API combina a pesquisa POI e geocodificação em uma ' pesquisa de linha única ' canônica. Por exemplo, a API pode manipular entradas de qualquer combinação de token de endereço ou POI. Ele também pode ser ponderado com uma posição contextual (lat./Lon. par), totalmente restrito por uma coordenada e um raio, ou executados mais geralmente sem nenhum ponto de ancoragem de inclinação geográfica.

A maioria das consultas de `maxFuzzyLevel=1` pesquisa usa o padrão para obter desempenho e reduzir resultados incomuns. Esse padrão pode ser substituído conforme necessário por solicitação passando o parâmetro `maxFuzzyLevel=2` de consulta ou. `3`

### <a name="search-for-an-address-using-fuzzy-search"></a>Pesquisar um endereço usando a pesquisa difusa

1. Abra o aplicativo do postmaster, clique em novo | Crie um novo e selecione **solicitação get**. Insira um nome de solicitação de **pesquisa difusa**, selecione uma coleção ou pasta para salvar e clique em **salvar**.

2. Na guia Construtor, selecione o método http **Get** e insira a URL de solicitação para seu ponto de extremidade de API.

    ![Pesquisa difusa](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autorização | Sem autenticação |

    O atributo **JSON** no caminho da URL determina o formato de resposta. Você está usando JSON em todo este artigo para facilitar o uso e a legibilidade. Você pode encontrar os formatos de resposta disponíveis na definição **obter pesquisa difusa** da referência da [API funcional do Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Clique em **params**e insira os seguintes pares de chave/valor para usar como parâmetros de consulta ou caminho na URL da solicitação:

    ![Pesquisa difusa](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Chave | Value |
    |------------------|-------------------------|
    | versão de API | 1.0 |
    | chave de assinatura | \<sua chave do Azure Maps\> |
    | query | pizza |

4. Clique em **Enviar** e examine o corpo da resposta.

    A cadeia de caracteres de consulta ambígua de "pizza" retornou 10 resultados [de resultado de interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) com categorias que ficam em "pizza" e "restaurante". Cada resultado retorna um endereço de rua, valores de latitude/longitude, porta de exibição e pontos de entrada para o local.
  
    Os resultados são variados para essa consulta, não vinculados a nenhum local de referência específico. Você pode usar o parâmetro **countryset** para especificar apenas os países/regiões para os quais seu aplicativo precisa de cobertura, pois o comportamento padrão é Pesquisar o mundo inteiro, potencialmente retornando resultados desnecessários.

5. Adicione o seguinte par de chave/valor à seção **params** e clique em **Enviar**:

    | Chave | Value |
    |------------------|-------------------------|
    | countrySet | EUA |
  
    Os resultados agora são limitados pelo código do país e a consulta retorna restaurantes no Estados Unidos.
  
    Para fornecer resultados para um local, você pode consultar um ponto de interesse e usar os valores de latitude e longitude retornados em sua chamada para o serviço de pesquisa difusa. Nesse caso, você usou o serviço de pesquisa para retornar o local da agulha de espaço de Seattle e usou a lat. Lon. valores para orientar a pesquisa.
  
6. Em params, insira os seguintes pares de chave/valor e clique em **Enviar**:

    ![Pesquisa difusa](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Chave | Value |
    |-----|------------|
    | Lat | 47,620525 |
    | Lon | -122.349274 |

## <a name="search-for-address-properties-and-coordinates"></a>Pesquisar Propriedades de endereço e coordenadas

Você pode passar um endereço de rua completo ou parcial para a API de endereço de pesquisa e receber uma resposta que inclui propriedades de endereço detalhadas, como o município ou a subdivisão, bem como valores de posição na latitude e longitude.

1. No postmaster, clique em **nova solicitação** | **obter solicitação** e nomeie-a **pesquisa de endereço**.
2. Na guia Construtor, selecione o método http **Get** , insira a URL de solicitação para seu ponto de extremidade de API e selecione um protocolo de autorização, se houver.

    ![Pesquisa de endereço](./media/how-to-search-for-address/address_search_url.png)
  
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) |
    | Autorização | Sem autenticação |

3. Clique em **params**e insira os seguintes pares de chave/valor para usar como parâmetros de consulta ou caminho na URL da solicitação:
  
    ![Pesquisa de endereço](./media/how-to-search-for-address/address_search_params.png)
  
    | Chave | Value |
    |------------------|-------------------------|
    | versão de API | 1.0 |
    | chave de assinatura | \<sua chave do Azure Maps\> |
    | query | 400 de Santa ampla, Seattle, WA 98109 |
  
4. Clique em **Enviar** e examine o corpo da resposta.
  
    Nesse caso, você especificou uma consulta de endereço completa e receberá um único resultado no corpo da resposta.
  
5. Em params, edite a cadeia de caracteres de consulta para o seguinte valor:
    ```plaintext
        400 Broad, Seattle
    ```

6. Adicione o seguinte par de chave/valor à seção **params** e clique em **Enviar**:

    | Chave | Value |
    |-----|------------|
    | Typeahead | true |

    O sinalizador **typeahead** informa à API de pesquisa de endereço para tratar a consulta como uma entrada parcial e retornar uma matriz de valores de previsão.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Pesquisar um endereço usando a pesquisa de endereço inversa

1. No postmaster, clique em **nova solicitação** | **obter solicitação** e nomeie-a **pesquisa de endereço inversa**.

2. Na guia Construtor, selecione o método http **Get** e insira a URL de solicitação para seu ponto de extremidade de API.
  
    ![URL de pesquisa de endereço inversa](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autorização | Sem autenticação |
  
3. Clique em **params**e insira os seguintes pares de chave/valor para usar como parâmetros de consulta ou caminho na URL da solicitação:
  
    ![Parâmetros de pesquisa de endereço inversa](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Chave | Value |
    |------------------|-------------------------|
    | versão de API | 1.0 |
    | chave de assinatura | \<sua chave do Azure Maps\> |
    | query | 47.591180,-122,332700 |
  
4. Clique em **Enviar** e examine o corpo da resposta.

    A resposta inclui informações de endereço de chave sobre o campo Safeco Field.
  
5. Adicione o seguinte par de chave/valor à seção **params** e clique em **Enviar**:

    | Chave | Value |
    |-----|------------|
    | number | true |

    Se o parâmetro de consulta [Number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) for enviado com a solicitação, a resposta poderá incluir o lado da rua (esquerda/direita) e também uma posição de deslocamento para esse número.
  
6. Adicione o seguinte par de chave/valor à seção **params** e clique em **Enviar**:

    | Chave | Value |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Quando o parâmetro de consulta [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) é definido, o retorno da resposta do limite de velocidade lançado.

7. Adicione o seguinte par de chave/valor à seção **params** e clique em **Enviar**:

    | Chave | Value |
    |-----|------------|
    | returnRoadUse | true |

    Quando o parâmetro de consulta [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) é definido, a resposta retorna a matriz de uso da estrada para os geocódigos inversos no nível da rua.

8. Adicione o seguinte par de chave/valor à seção **params** e clique em **Enviar**:

    | Chave | Value |
    |-----|------------|
    | roadUse | true |

    Você pode restringir a consulta de geocódigo inversa a um tipo específico de estrada usado usando o parâmetro de consulta [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
  
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Pesquisar a rua cruzada usando a pesquisa de endereços inversos

1. No postmaster, clique em **nova solicitação** | **obter solicitação** e nomeie-a como **endereço inverso de pesquisa entre ruas**.

2. Na guia Construtor, selecione o método http **Get** e insira a URL de solicitação para seu ponto de extremidade de API.
  
    ![Pesquisa de rua cruzada de endereço reverso](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autorização | Sem autenticação |
  
3. Clique em **params**e insira os seguintes pares de chave/valor para usar como parâmetros de consulta ou caminho na URL da solicitação:
  
    | Chave | Value |
    |------------------|-------------------------|
    | versão de API | 1.0 |
    | chave de assinatura | \<sua chave do Azure Maps\> |
    | query | 47.591180,-122,332700 |
  
4. Clique em **Enviar** e examine o corpo da resposta.

## <a name="next-steps"></a>Passos Seguintes

- Explore a documentação da API do [serviço de pesquisa do Azure Maps](https://docs.microsoft.com/rest/api/maps/search) .
