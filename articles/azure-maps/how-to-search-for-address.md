---
title: Procure uma localização utilizando os serviços de Pesquisa Azure Maps Microsoft Azure Maps
description: Neste artigo, você vai aprender a procurar uma localização usando o Microsoft Azure Maps Search Service para geocoding e geocoding reverso.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cf0e5267885df1ace51271c53bb2d68ee5002f00
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335436"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Procure uma localização utilizando os serviços de pesquisa do Azure Maps

O Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) é um conjunto de APIs RESTful projetado para ajudar os desenvolvedores a pesquisar endereços, lugares, listas de negócios por nome ou categoria, e outras informações geográficas. Além de apoiar a geocodagem tradicional, os serviços também podem reverter endereços de geocódigo e atravessar ruas com base em latitudes e longitudes. Os valores de latitude e longitude devolvidos pela pesquisa podem ser usados como parâmetros em outros serviços Azure Maps, como serviços [Route](https://docs.microsoft.com/rest/api/maps/route) e [Weather.](https://docs.microsoft.com/rest/api/maps/weather)

Neste artigo você vai aprender, como:

* Solicite coordenadas de latitude e longitude para um endereço (localização do endereço geocódigo) utilizando [a API do Endereço de Busca]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
* Procure um endereço ou Ponto de Interesse (POI) usando [API de pesquisa fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Procure um endereço juntamente com propriedades e coordenadas
* Faça uma [pesquisa de endereço inverso](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para traduzir a localização de coordenadas para endereço de rua
* Procure uma rua transversal usando [o endereço de pesquisa Reverse Cross Street API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste artigo, você precisa primeiro criar uma conta Azure Maps e obter a chave de subscrição de conta de mapas. Siga as instruções na [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma subscrição de conta Azure Maps e seguir os passos na chave [principal](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave principal para obter a chave principal para a sua conta. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](./how-to-manage-authentication.md)

Este artigo utiliza a [aplicação Do Carteiro](https://www.getpostman.com/apps) para construir chamadas REST. Você pode usar qualquer ambiente de desenvolvimento da API que você preferir.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Solicitar latitude e longitude para um endereço (geocoding)

Neste exemplo, estamos a usar a Azure Maps [Obter Endereço de Pesquisa API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) para converter o endereço de rua em coordenadas de latitude e longitude. Você pode passar um endereço de rua completo ou parcial para a API e receber uma resposta que inclui propriedades de endereço detalhado, como rua, código postal e país/região, bem como valores posicionais em latitude e longitude.

Se tiver um conjunto de endereços para geocódigo, pode utilizar a API do [Lote de Endereço de Pesquisa Pós-Pesquisa](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) para enviar um lote de consultas numa única chamada API.

1. No Carteiro, clique em **Novo Pedido**  |  **GET e** nomeie-o Procurar **endereço.**

2. No separador Construtor, selecione o método **GET** HTTP, introduza o URL de pedido para o seu ponto final da API e selecione um protocolo de autorização, se houver.

![Pesquisa de endereços](./media/how-to-search-for-address/address_search_url.png)

| Parâmetro | Valor sugerido |
|---------------|------------------------------------------------| 
| Método HTTP | GET |
| URL do Pedido | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| Autorização | No Auth |

3. Clique em **Params**, e introduza os seguintes pares de tecla /valor para usar como parâmetros de consulta ou caminho no URL de pedido: 

![Pesquisa de endereços](./media/how-to-search-for-address/address_search_params.png) 

| Chave | Valor | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| chave de assinatura | \<your Azure Maps key\> | 
| consulta | 400 Broad St, Seattle, WA 98109 | 

4. Clique **em Enviar** e rever o corpo de resposta. 

Neste caso, especificou uma consulta completa do endereço e recebeu um único resultado no corpo de resposta. 

5. Em Params, edite a cadeia de consulta para o seguinte valor: 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. Adicione o seguinte par de tecla / valor à secção **Params** e clique em **Enviar**: 

| Chave | Valor | 
|-----|------------| 
| cabeça de tipo | true | 

A bandeira **typeahead** diz à API de Pesquisa de Endereços para tratar a consulta como uma entrada parcial e devolver uma série de valores preditivos.

## <a name="using-fuzzy-search-api"></a>Utilização de API de Pesquisa Fuzzy

A Azure Maps[ Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) é recomendado o serviço para usar quando não sabe quais são as entradas do seu utilizador para uma consulta de pesquisa. A API combina a pesquisa do Ponto de Interesse (POI) e a geocodificação numa "pesquisa de linha única" canónica. Por exemplo, a API pode lidar com entradas de qualquer endereço ou combinação de fichas POI. Também pode ser ponderado com uma posição contextual (lat./lon. par), totalmente limitado por uma coordenada e raio, ou executado de forma mais geral sem qualquer ponto de ancoragem geoduciante.

A maioria das consultas de pesquisa padrão `maxFuzzyLevel=1` para obter desempenho e reduzir resultados incomuns. Este padrão pode ser ultrapassado conforme necessário por pedido, passando no parâmetro de consulta `maxFuzzyLevel=2` ou `3` .

### <a name="search-for-an-address-using-fuzzy-search"></a>Procure um endereço usando a Pesquisa Fuzzy

1. Abra a app Do Carteiro, clique em Novo Crie novo e selecione **pedido GET**. Introduza um nome de Pedido de **pesquisa fuzzy,** selecione uma coleção ou pasta para guardá-la e clique em **Guardar**.

2. No separador 'Construtor', selecione o método **GET** HTTP e introduza o URL de pedido para o seu ponto final da API.

    ![Pesquisa fuzzy](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autorização | No Auth |

    O atributo **json** no caminho URL determina o formato de resposta. Este artigo usa json para facilitar o uso e a legibilidade. Pode encontrar os formatos de resposta disponíveis na definição **Get Search Fuzzy** da [referência API Funcional](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)do Maps.

3. Clique em **Params**, e introduza os seguintes pares de tecla /valor para usar como parâmetros de consulta ou caminho no URL de pedido:

    ![Pesquisa fuzzy](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | chave de assinatura | \<your Azure Maps key\> |
    | consulta | pizza |

4. Clique **em Enviar** e rever o corpo de resposta.

    A cadeia de consulta ambígua para "pizza" devolveu 10 [pontos de interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) nas categorias "pizza" e "restaurante". Cada resultado devolve um endereço de rua, valores de latitude e longitude, ver porta e pontos de entrada para a localização.
  
    Os resultados são variados para esta consulta, não ligados a qualquer local de referência em particular. Pode utilizar o parâmetro **countrySet** para especificar apenas os países/regiões para os quais a sua aplicação necessita de cobertura. O comportamento padrão é procurar em todo o mundo, potencialmente devolvendo resultados desnecessários.

5. Adicione o seguinte par de tecla / valor à secção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |------------------|-------------------------|
    | paísSet | EUA |
  
    Os resultados estão agora limitados pelo código do país e a consulta devolve restaurantes de pizza nos Estados Unidos.
  
    Para fornecer resultados para uma localização, pode consultar um ponto de interesse e utilizar os valores de latitude e longitude devolvidos na sua chamada para o serviço de Pesquisa Fuzzy. Neste caso, usou o serviço de Pesquisa para devolver a localização da Agulha Espacial de Seattle e usou o lat. / Lon. valores para orientar a procura.
  
6. Em Params, insira os seguintes pares de teclas / valor e clique em **Enviar**:

    ![Pesquisa fuzzy](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Chave | Valor |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>Procure um endereço de rua utilizando a Pesquisa de Endereços Reverso

Azure Maps [Obter Endereço de Pesquisa API reverso]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) ajuda a traduzir uma coordenada (exemplo: 37.786505, -122.3862) num endereço de rua compreensível humano. Na maioria das vezes, isso é necessário em aplicações de rastreio onde você recebe um feed GPS do dispositivo ou ativo e deseja saber qual o endereço onde a coordenada está localizada.
Se tiver um conjunto de localizações de coordenadas para reverter o geocódigo, pode utilizar a [API do Lote Inverso do Endereço de Pesquisa De Pós-Pesquisa](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) para enviar um lote de consultas numa única chamada API.


1. No Carteiro, clique em **Novo Pedido**  |  **GET e** nomeie-o Reverse Address **Search**.

2. No separador 'Construtor', selecione o método **GET** HTTP e introduza o URL de pedido para o seu ponto final da API.
  
    ![URL de pesquisa de endereço invertido](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autorização | No Auth |
  
3. Clique em **Params**, e introduza os seguintes pares de tecla /valor para usar como parâmetros de consulta ou caminho no URL de pedido:
  
    ![Parâmetros de pesquisa de endereço invertido](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | chave de assinatura | \<your Azure Maps key\> |
    | consulta | 47.591180,-122.332700 |
  
4. Clique **em Enviar** e rever o corpo de resposta.

    A resposta inclui informações de endereço chave sobre o Campo Safeco.
  
5. Adicione o seguinte par de tecla / valor à secção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | número | true |

    Se o parâmetro de consulta [de números](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) for enviado com o pedido, a resposta pode incluir o lado da rua (Esquerda ou Direita) e também uma posição de compensação para esse número.
  
6. Adicione o seguinte par de tecla / valor à secção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | retornaSpeitLimit | true |
  
    Quando o parâmetro de consulta [de velocidade de retorno](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) é definido, a resposta devolve o limite de velocidade registado.

7. Adicione o seguinte par de tecla / valor à secção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | returnRoadUse | true |

    Quando o parâmetro de consulta [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) é definido, a resposta devolve a matriz de utilização da estrada para geocódigos invertidos ao nível da rua.

8. Adicione o seguinte par de tecla / valor à secção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | roadUse | true |

    Pode restringir a consulta de geocódigo inverso a um tipo específico de estrada utilizando o parâmetro de consulta [roadUse.](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Pesquisa por cross street usando Reverse Address Cross Street Search

1. No Carteiro, clique em **Novo Pedido**  |  **GET request** e nomeie-o Reverse Address Cross Street **Search**.

2. No separador 'Construtor', selecione o método **GET** HTTP e introduza o URL de pedido para o seu ponto final da API.
  
    ![Pesquisa inversa de endereço Cross Street](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autorização | No Auth |
  
3. Clique em **Params**, e introduza os seguintes pares de tecla /valor para usar como parâmetros de consulta ou caminho no URL de pedido:
  
    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | chave de assinatura | \<your Azure Maps key\> |
    | consulta | 47.591180,-122.332700 |
  
4. Clique **em Enviar** e rever o corpo de resposta.

## <a name="next-steps"></a>Passos seguintes

- Explore a documentação do [Serviço de Pesquisa AZure Maps REST API](https://docs.microsoft.com/rest/api/maps/search).
- Saiba mais sobre as [melhores práticas do Azure Maps Search Service](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) e como otimizar as suas consultas.
