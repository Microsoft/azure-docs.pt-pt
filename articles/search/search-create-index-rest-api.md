---
title: Criar um índice no código utilizando a API REST - Azure Search
description: Crie um índice pesquisável de texto completo em código usando a pedidos HTTP e a API de REST do Azure Search.
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 98749e0900920d0d3541c78c79598123e4f987df
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313975"
---
# <a name="create-an-azure-search-index-using-the-rest-api"></a>Criar um índice da Azure Search utilizando a API REST
> [!div class="op_single_selector"]
>
> * [Descrição geral](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
>
>

Este artigo irá guiá-lo através do processo de criação de um [índice](https://docs.microsoft.com/rest/api/searchservice/Create-Index) da Azure Search utilizando a API REST da Azure Search.

Antes de consultar este guia e criar um índice, deverá já ter [criado um serviço Azure Search](search-create-service-portal.md).

Para criar um índice da Azure Search utilizando a API REST, irá emitir um pedido HTTP POST único para o ponto final do URL do seu serviço Azure Search. As suas definições de índice ficarão contidas no corpo do pedido como conteúdo JSON corretamente formado.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Identificar a sua chave de API do administrador do serviço Azure Search
Agora que aprovisionou um serviço Azure Search, pode emitir pedidos HTTP contra o ponto final do URL do seu serviço utilizando a API REST. *Todos* os pedidos de API devem incluir a chave de API que foi gerada para o serviço de Pesquisa que aprovisionou. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

1. Para localizar as chaves de API do seu serviço, tem de iniciar sessão no [portal do Azure](https://portal.azure.com/)
2. Aceda ao painel do seu serviço Azure Search
3. Clique no ícone "Chaves"

O seu serviço terá *chaves de administração* e *chaves de consulta*.

* As suas *chaves de administração* principal e secundária concedem direitos totais para todas as operações, incluindo a capacidade para gerir o serviço, criar e eliminar índices, indexadores e origens de dados. Existem duas chaves para que possa continuar a utilizar a chave secundária caso opte por voltar a gerar a chave principal e vice-versa.
* As suas *chaves de consulta* concedem acesso só de leitura para índices e documentos e, normalmente, são distribuídas por aplicações cliente que emitem pedidos de pesquisa.

Para o efeito de criação de um índice, pode utilizar tanto a chave de administrador principal como a secundária.

## <a name="define-your-azure-search-index-using-well-formed-json"></a>Definir o seu índice da Azure Search utilizando JSON corretamente formado
Um pedido HTTP POST único para o seu serviço criará o seu índice. O corpo do seu pedido de HTTP POST irá conter um único objeto JSON que define o seu índice da Azure Search.

1. A primeira propriedade deste objeto JSON é o nome do seu índice.
2. A segunda propriedade deste objeto JSON é uma matriz JSON com o nome `fields` que contém um objeto JSON separado para cada campo no seu índice. Cada um destes objetos JSON contém vários pares nome/valor para cada um dos atributos de campo, incluindo "nome", "tipo", etc.

É importante ter em consideração as suas necessidades comerciais e experiência do utilizador de pesquisa quando estiver a criar o seu índice, uma vez que cada campo deve receber os [atributos adequados](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Estes atributos estabelecem quais as funcionalidades de pesquisa (filtragem, facetamento, ordenação de pesquisa de texto completo, etc.) aplicáveis a cada campo. Qualquer atributo que não seja especificado, a predefinição será ativar a funcionalidade de pesquisa correspondente, a menos que a desative especificamente.

Para o nosso exemplo, atribuímos o nome "hotéis" ao nosso índice e definimos os nossos campos do seguinte modo:

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Escolhemos cuidadosamente os atributos de índice para cada campo com base na forma como acreditamos que serão utilizados numa aplicação. Por exemplo, `hotelId` é uma chave exclusiva que as pessoas que procuram hotéis provavelmente não conhecem, desta forma, desativamos a pesquisa em texto completo nesse campo através da definição de `searchable` para `false`, que poupa espaço no índice.

Tenha em atenção que exatamente um campo no seu índice do tipo `Edm.String` deve ser designado como o campo "chave".

A definição de índice acima utiliza um analisador de idioma para o campo `description_fr` porque destina-se ao armazenamento de texto em francês. Consulte [o tópico de suporte de idioma](https://docs.microsoft.com/rest/api/searchservice/Language-support), bem como a [mensagem de blogue](https://azure.microsoft.com/blog/language-support-in-azure-search/) correspondente, para obter mais informações sobre analisadores de idiomas.

## <a name="issue-the-http-request"></a>Emitir o pedido HTTP
1. Utilize a sua definição de índice, tal como o corpo do pedido, emita um pedido de HTTP POST para o seu URL de ponto final de serviço Azure Search. No URL, não se esqueça de utilizar o seu nome de serviço como nome do anfitrião e colocar a `api-version` adequada como um parâmetro de cadeia de consulta (a versão da API atual é `2017-11-11` no momento da publicação deste documento).
2. Nos cabeçalhos do pedido, especifique o `Content-Type` como `application/json`. Também terá de fornecer a chave de administrador do seu serviço que identificou no Passo I no cabeçalho `api-key`.

Terá de fornecer o nome do seu próprio serviço e uma chave de API para emitir o pedido abaixo:

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [api-key]


Para um pedido com êxito, deve ver o código de estado 201 (Criado). Para obter mais informações sobre a criação de um índice através da API REST, visite a [referência de API aqui](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Para obter mais informações sobre outros códigos de estado HTTP que possam ser devolvidos em caso de falha, consulte [Códigos de estado HTTP (Pesquisa do Azure)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

Quando terminar com um índice e pretender eliminá-lo, emita apenas um pedido de ELIMINAÇÃO HTTP. Por exemplo, aqui temos um procedimento para eliminar o índice "hotéis":

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11
    api-key: [api-key]


## <a name="next-steps"></a>Passos Seguintes
Depois de criar um índice da Azure Search, estará pronto para [carregar o conteúdo para o índice](search-what-is-data-import.md) para que possa começar a pesquisar os seus dados.
