---
title: Como procurar um endereço com o serviço de pesquisa de mapas do Azure | Documentos da Microsoft
description: Saiba como procurar um endereço com o serviço de pesquisa de mapas do Azure
author: walsehgal
ms.author: v-musehg
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8ab2c73030c0860fc709a774b9fd84d20a6d7c99
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277328"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Localizar um endereço com o serviço de pesquisa do Azure Maps

O serviço de pesquisa de mapas é um conjunto de APIs RESTful concebida para os programadores procurarem endereços, locais, pontos de interesse, listas de empresas e outras informações geográficas. O serviço atribui uma latitude/longitude para um endereço específico, Rua cruzada, funcionalidade geográfica ou ponto de interesse (POI). Valores de latitude e longitude devolvidos pela pesquisa podem ser usados como parâmetros noutros serviços de mapas, como o fluxo de trânsito e de rota.

Neste artigo, ficará a saber, como:

* Procure um através do endereço [API de pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Procurar um endereço, juntamente com as propriedades e as coordenadas
* Efetuar uma [pesquisa inversa de endereço](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para procurar um endereço de rua
* Procure um usando Rua cruzada [endereço inverter entre Rua API de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Pré-requisitos

Para fazer todas as chamadas para as APIs do serviço de mapas, terá de uma conta de mapas e a chave. Para obter informações sobre como criar uma conta e obter uma chave, consulte [como gerir a sua conta do Azure Maps e as chaves](how-to-manage-account-keys.md).

Este artigo utiliza a [aplicação Postman](https://www.getpostman.com/apps) para criar as chamadas REST. Pode usar qualquer ambiente de desenvolvimento de API que preferir.

## <a name="using-fuzzy-search"></a>Utilizar a pesquisa difusa

A API padrão para o serviço de pesquisa está [a pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) e é útil quando não sabe quais são suas entradas de utilizador para uma consulta de pesquisa. A API combina pesquisa POI e geocodificação numa "linha única pesquisa' canônica. Por exemplo, a API pode lidar com entradas de qualquer endereço ou uma combinação de token de POI. Também pode ser ponderada com uma posição contextual (lat./lon. par), restrita por uma coordenada e radius ou, mais geralmente, executado sem qualquer geo influenciando o ponto de ancoragem.

A maioria das consultas de pesquisa como padrão `maxFuzzyLevel=1` para obter o desempenho e reduzir os resultados invulgares. Este padrão pode ser substituído, conforme necessário por pedido, passando o parâmetro de consulta `maxFuzzyLevel=2` ou `3`.

### <a name="search-for-an-address-using-fuzzy-search"></a>Procurar um endereço com a pesquisa difusa

1. Abra a aplicação de Postman, clique em novo | Criar novo e selecione **solicitação GET**. Introduza um nome de pedido de **a pesquisa difusa**, selecione uma coleção ou uma pasta para guardá-lo para e clique em **guardar**.

2. No separador Builder, selecione o **obter** método HTTP e introduza o URL do pedido para o ponto final de API.

    ![Pesquisa difusa](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autorização | Sem autenticação |

    O **json** atributo no caminho do URL determina o formato de resposta. Está a utilizar o json ao longo deste artigo para facilidade de uso e legibilidade. Pode encontrar os formatos de resposta disponível na **obter pesquisa difusa** definição da [referência da API de mapas funcional](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Clique em **Params**e introduza a seguinte chave / valor pares para utilizar como parâmetros de consulta ou o caminho no URL do pedido:

    ![Pesquisa difusa](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Chave | Valor |
    |------------------|-------------------------|
    | versão de API | 1.0 |
    | subscription-key | \<a chave do Azure Maps\> |
    | consulta | pizza |

4. Clique em **enviar** e rever o corpo da resposta.

    A cadeia de consulta ambígua de "pizza" devolvido 10 [ponto do resultado de interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) resultados (POI) com as categorias cair no "pizza" e "restaurante". Cada resultado retorna um endereço de rua, latitude / ver os valores de longitude, portas e pontos de entrada para a localização.
  
    Os resultados são diversificados para essa consulta, não ligada a qualquer localização de referência específica. Pode utilizar o **countrySet** parâmetro para especificar apenas os países para os quais seu aplicativo precisa cobertura, como o comportamento padrão é pesquisar todo o mundo, potencialmente, retornando resultados desnecessários.

5. Adicione a seguinte chave / valor par para o **Params** secção e clique em **enviar**:

    | Chave | Valor |
    |------------------|-------------------------|
    | countrySet | EUA |
  
    Os resultados são agora vinculado de acordo com o código de país e a consulta devolve restaurantes pizza nos Estados Unidos.
  
    Para fornecer resultados para uma localização, pode consultar um ponto de interesse e utilizar os valores de longitude e latitude retornado em sua chamada para o serviço de pesquisa difusa. Neste caso, utilizou o serviço de pesquisa para devolver a localização do Space Needle de Seattle e usado o lat. / lon. valores para orientar a pesquisa.
  
6. Em parâmetros, introduza a seguinte chave / pares de valores e clique em **enviar**:

    ![Pesquisa difusa](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Chave | Valor |
    |-----|------------|
    | LAT | 47.620525 |
    | lon | -122.349274 |

## <a name="search-for-address-properties-and-coordinates"></a>Pesquisar propriedades de endereço e as coordenadas

Pode passar um endereço completo ou parcial para o endereço de pesquisa API e receber uma resposta que inclui as propriedades de endereçamento detalhadas, tais como o município ou subdivisão, bem como os valores posicionais na latitude e longitude.

1. No Postman, clique em **nova solicitação** | **pedido GET** e nomeie- **endereço pesquisa**.
2. No separador Builder, selecione o **obter** método HTTP, introduza o URL do pedido para o ponto final de API e selecione um protocolo de autorização, se aplicável.

    ![Pesquisa de endereço](./media/how-to-search-for-address/address_search_url.png)
  
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) |
    | Autorização | Sem autenticação |

3. Clique em **Params**e introduza a seguinte chave / valor pares para utilizar como parâmetros de consulta ou o caminho no URL do pedido:
  
    ![Pesquisa de endereço](./media/how-to-search-for-address/address_search_params.png)
  
    | Chave | Valor |
    |------------------|-------------------------|
    | versão de API | 1.0 |
    | subscription-key | \<a chave do Azure Maps\> |
    | consulta | 400 amplo St, Seattle, WA 98109 |
  
4. Clique em **enviar** e rever o corpo da resposta.
  
    Neste caso, especificou uma consulta de endereço completo e receber um resultado único no corpo da resposta.
  
5. Edite parâmetros, a cadeia de consulta para o seguinte valor:
    ```plaintext
        400 Broad, Seattle
    ```

6. Adicione a seguinte chave / valor par para o **Params** secção e clique em **enviar**:

    | Chave | Valor |
    |-----|------------|
    | typeahead | true |

    O **typeahead** sinalizador indica a API de pesquisa de endereço para tratar a consulta como uma entrada parcial e retornar uma matriz de valores de previsão.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Procure uma rua, através de pesquisa inversa de endereço

1. No Postman, clique em **nova solicitação** | **pedido GET** e nomeie- **pesquisa inversa de endereço**.

2. No separador Builder, selecione o **obter** método HTTP e introduza o URL do pedido para o ponto final de API.
  
    ![URL de pesquisa inversa de endereço](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autorização | Sem autenticação |
  
3. Clique em **Params**e introduza a seguinte chave / valor pares para utilizar como parâmetros de consulta ou o caminho no URL do pedido:
  
    ![Inverter os parâmetros de pesquisa de endereço](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Chave | Valor |
    |------------------|-------------------------|
    | versão de API | 1.0 |
    | subscription-key | \<a chave do Azure Maps\> |
    | consulta | 47.591180,-122.332700 |
  
4. Clique em **enviar** e rever o corpo da resposta.

    A resposta inclui informações de endereço chave sobre Safeco Field.
  
5. Adicione a seguinte chave / valor par para o **Params** secção e clique em **enviar**:

    | Chave | Valor |
    |-----|------------|
    | número | true |

    Se o [número](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) parâmetro de consulta é enviado com o pedido, a resposta pode incluir o lado da rua (esquerda/direita) e também uma posição de deslocamento para o número.
  
6. Adicione a seguinte chave / valor par para o **Params** secção e clique em **enviar**:

    | Chave | Valor |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Quando o [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) é definir o parâmetro de consulta, a resposta de retorno do limite de velocidade postado.

7. Adicione a seguinte chave / valor par para o **Params** secção e clique em **enviar**:

    | Chave | Valor |
    |-----|------------|
    | returnRoadUse | true |

    Quando o [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) parâmetro de consulta está definido, a resposta retorna a matriz de utilização de estrada para geocodes inversas no nível de rua.

8. Adicione a seguinte chave / valor par para o **Params** secção e clique em **enviar**:

    | Chave | Valor |
    |-----|------------|
    | roadUse | true |

    Pode restringir a consulta de geocódigo inverso para um tipo específico de estrada utilização com o [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) parâmetro de consulta.
  
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Procure a rua cruzada com pesquisa Rua em vários endereços inverso

1. No Postman, clique em **nova solicitação** | **pedido GET** e nomeie- **inverter endereço Cross pesquisa Rua**.

2. No separador Builder, selecione o **obter** método HTTP e introduza o URL do pedido para o ponto final de API.
  
    ![Reverter a pesquisa de rua cruzada de endereço](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do Pedido | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autorização | Sem autenticação |
  
3. Clique em **Params**e introduza a seguinte chave / valor pares para utilizar como parâmetros de consulta ou o caminho no URL do pedido:
  
    | Chave | Valor |
    |------------------|-------------------------|
    | versão de API | 1.0 |
    | subscription-key | \<a chave do Azure Maps\> |
    | consulta | 47.591180,-122.332700 |
  
4. Clique em **enviar** e rever o corpo da resposta.

## <a name="next-steps"></a>Passos Seguintes

- Explore os [serviço de pesquisa do Azure Maps](https://docs.microsoft.com/rest/api/maps/search) documentação da API.
