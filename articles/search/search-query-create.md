---
title: Criar uma consulta básica
titleSuffix: Azure Cognitive Search
description: Aprenda a construir um pedido de consulta na Pesquisa Cognitiva, quais as ferramentas e APIs a usar para testes e códigos, e como as decisões de consulta começam com o design do índice.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ace887396bacf264f0ffbd186ef1349e96496786
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371178"
---
# <a name="create-a-basic-query-in-azure-cognitive-search"></a>Criar uma consulta básica na Pesquisa Cognitiva Azure

Este artigo explica a consulta de construção passo a passo. Os exemplos estão em REST para que possa copiar cordas no **Search Explorer** no portal, ou criar consultas interativamente, utilizando o código do Carteiro ou do Estúdio Visual. Pode utilizar qualquer nível ou versão da Pesquisa Cognitiva para os exemplos deste artigo.

## <a name="choose-a-tool-or-api"></a>Escolha uma ferramenta ou API

Escolha entre as seguintes ferramentas e APIs para criar consultas para testes ou cargas de trabalho de produção.

| Metodologia | Descrição |
|-------------|-------------|
| Portal| [O Explorador de Pesquisa (portal)](search-explorer.md) fornece uma barra de pesquisa e opções para seleções de versão index e api. Os resultados são devolvidos como documentos JSON. Recomendado para investigação, teste e validação antecipadas. <br/>[Saiba mais.](search-explorer.md) |
| Ferramentas de teste web| [O Código do Estúdio Visual ou Carteiro ou Visual](search-get-started-rest.md) são escolhas fortes para formular chamadas [de Documentos de Busca](/rest/api/searchservice/search-documents) REST. A API REST suporta todas as operações programáticas em Azure Cognitive Search, para que possa emitir pedidos interativamente para entender como funciona antes de investir em código.  |
| Azure SDK | [SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) pode ser usado para consultar um índice de pesquisa em C#.  [Saiba mais.](search-howto-dotnet-sdk.md) <br/><br/>[SearchClient (Python)](/dotnet/api/azure.search.documents.searchclient) pode ser usado para consultar um índice de pesquisa em Python. [Saiba mais.](search-get-started-python.md) <br/><br/> [SearchClient (JavaScript)](/dotnet/api/azure.search.documents.searchclient) pode ser usado para consultar um índice de pesquisa em JavaScript. [Saiba mais.](search-get-started-javascript.md)  |

## <a name="set-up-a-search-client"></a>Criar um cliente de pesquisa

Um cliente de pesquisa autentica-se no serviço de pesquisa, envia pedidos e trata de respostas. As consultas são sempre direcionadas para a recolha de documentos de um único índice. Não é possível aderir a índices ou criar estruturas de dados personalizadas ou temporárias como alvo de consulta.

### <a name="in-the-portal"></a>No portal

O Search Explorer e outras ferramentas do portal têm uma ligação ao cliente incorporada ao serviço, com índices de acesso direto e outros objetos a partir de páginas do portal. O acesso a ferramentas, assistentes e objetos assume que tem direitos administrativos no serviço. Com o Search Explorer, pode concentrar-se especificando a cadeia de pesquisa e outros parâmetros. 

### <a name="using-rest"></a>Utilizar REST

Para chamadas REST, pode utilizar [o Carteiro ou ferramentas semelhantes](search-get-started-rest.md) às do cliente para especificar um pedido [de Documentos de Busca.](/rest/api/searchservice/search-documents) Cada pedido é autónomo, pelo que deve fornecer o ponto final (URL ao serviço) e uma chave API de administração ou consulta para acesso. Dependendo do pedido, o URL também pode incluir o nome do índice, a recolha de documentos e outras propriedades. Algumas propriedades, tais como o Tipo de Conteúdo e a tecla API são transmitidas no cabeçalho de pedido. Outros parâmetros podem ser transmitidos no URL ou no corpo do pedido. Todas as chamadas REST requerem uma chave API para autenticação e uma versão api.

### <a name="using-azure-sdks"></a>Usando Azure SDKs

Os Azure SDKs fornecem clientes de pesquisa que podem persistir, permitindo a reutilização de ligação. Para operações de consulta, você instantaneamente um SearchClient e fornecerá valores para as seguintes propriedades: Ponto final, chave, índice. Em seguida, pode ligar para o método 'Procurar' para fornecer o fio de consulta. 

| Linguagem | Cliente | Exemplo |
|----------|--------|---------|
| C# e .NET | [PesquisaDesseient](/dotnet/api/azure.search.documents.searchclient) | [Envie a sua primeira consulta de pesquisa em C #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [PesquisaDesseient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Envie a sua primeira consulta de pesquisa em Python](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [PesquisaDesseient](/java/api/com.azure.search.documents.searchclient) | [Envie a sua primeira consulta de pesquisa em Java](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [PesquisaDesseient](/javascript/api/@azure/search-documents/searchclient) | [Envie a sua primeira consulta de pesquisa em JavaScript](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>Escolha um parser: simples / cheio

A Azure Cognitive Search dá-lhe uma escolha entre dois parsers de consulta para lidar com consultas típicas e especializadas. Os pedidos que utilizam o simples parser são normalmente consultas de pesquisa de texto completas, formuladas utilizando a [simples sintaxe de consulta,](query-simple-syntax.md)selecionada como padrão para a sua velocidade e eficácia em consultas de texto de forma livre. Esta sintaxe suporta uma série de operadores de pesquisa comuns, incluindo os operadores E, OU, NÃO, frase, sufixo e precedência.

A [sintaxe de consulta lucene completa,](query-Lucene-syntax.md#bkmk_syntax)ativada quando adiciona `queryType=full` ao pedido, expõe a linguagem de consulta amplamente adotada e expressiva desenvolvida como parte do [Apache Lucene.](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) A sintaxe completa estende a sintaxe simples. Qualquer consulta que escreva para a simples sintaxe passa por baixo do parser lucene completo. 

Os exemplos a seguir ilustram o ponto: mesma consulta, mas com **`queryType`** configurações diferentes, que produzem resultados diferentes. Na primeira consulta, o `^3` seguinte é tratado como parte do termo de `historic` pesquisa. O resultado mais bem classificado para esta consulta é o "Marquês Plaza & Suites", que tem *oceano* na sua descrição.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

A mesma consulta utilizando o parser lucene completo interpreta `^3` como um impulsionador de prazos em campo. A troca de parsers muda de posição, com os resultados que contêm o termo *histórico* movendo-se para o topo.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

## <a name="enable-query-behaviors-in-an-index"></a>Ativar comportamentos de consulta num índice

O design de índice e o design de consulta estão fortemente acopdos na Pesquisa Cognitiva Azure. O *esquema de índice,* com atributos em cada campo, determina o tipo de consulta que pode construir.

Os atributos de índice num campo definem as operações permitidas - se um campo é *pesmável* no índice, *recuperável* em resultados, *ordenado,* *filtrado*, e assim por diante. Nas consultas de exemplo, `"$orderby": "Rating desc"` só funciona porque o campo de Classificação é marcado como *classificado* no esquema de índice.

![Definição de índice para a amostra do hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definição de índice para a amostra do hotel")

A imagem acima é uma lista parcial de atributos de índice para o [índice de amostra de hotéis.](search-get-started-portal.md) Pode criar e visualizar todo o esquema de índice no portal. Para obter mais informações sobre atributos de índice, consulte [Create Index (REST API)](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Passos seguintes

Agora que entende como o pedido é construído, experimente exemplos usando a sintaxe simples e completa.

+ [Exemplos de consultas simples](search-query-simple-examples.md)
+ [Exemplos de consulta de sintaxe lucene para a construção de consultas avançadas](search-query-lucene-examples.md)
+ [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)