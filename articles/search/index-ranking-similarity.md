---
title: Configure o algoritmo de semelhança
titleSuffix: Azure Cognitive Search
description: Saiba como ativar o BM25 em serviços de pesquisa mais antigos e como os parâmetros BM25 podem ser modificados para melhor acomodar o conteúdo dos seus índices.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 52b3523d3c092f1b9375f53038cc3b20d0ddedcc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232839"
---
# <a name="configure-the-similarity-ranking-algorithm-in-azure-cognitive-search"></a>Configure o algoritmo de classificação de semelhança na Pesquisa Cognitiva Azure

A Azure Cognitive Search suporta dois algoritmos de classificação de semelhança:

+ Um algoritmo *clássico de semelhança,* usado por todos os serviços de pesquisa até 15 de julho de 2020.
+ Uma implementação do algoritmo *Okapi BM25,* utilizado em todos os serviços de pesquisa criados após 15 de julho.

O ranking BM25 é o novo padrão porque tende a produzir rankings de pesquisa que se alinham melhor com as expectativas dos utilizadores. Vem com [parâmetros](#set-bm25-parameters) para afinação de resultados com base em fatores como o tamanho do documento. 

Para novos serviços criados após 15 de julho de 2020, o BM25 é utilizado automaticamente e é o único algoritmo de semelhança. Se tentar definir semelhanças com a ClassicSimilarity num novo serviço, será devolvido um erro HTTP 400 porque esse algoritmo não é suportado pelo serviço.

Para os serviços mais antigos criados antes de 15 de julho de 2020, a semelhança clássica continua a ser o algoritmo padrão. Os serviços mais antigos podem ser atualizados para BM25 por índice, como explicado abaixo. Se estiver a mudar de clássico para BM25, pode esperar ver algumas diferenças na forma como os resultados da pesquisa são encomendados.

> [!NOTE]
> O ranking semântico, atualmente em pré-visualização dos serviços standard em regiões selecionadas, constitui um passo adicional na produção de resultados mais relevantes. Ao contrário dos outros algoritmos, é uma funcionalidade de complemento que itera sobre um conjunto de resultados existente. Para mais informações, consulte [a visão geral da pesquisa semântica](semantic-search-overview.md) e [o ranking semântico.](semantic-ranking.md)

## <a name="enable-bm25-scoring-on-older-services"></a>Ativar a pontuação de BM25 em serviços mais antigos

Se estiver a executar um serviço de pesquisa criado antes de 15 de julho de 2020, pode ativar o BM25 definindo uma propriedade de semelhança em novos índices. A propriedade só está exposta em novos índices, pelo que se quiser O BM25 num índice existente, deve cair e [reconstruir o índice](search-howto-reindex.md) com uma nova propriedade de similaridade definida para "Microsoft.Azure.Search.BM25Similarity".

Uma vez que exista um índice com uma propriedade de semelhança, pode alternar entre BM25Similarity ou ClassicSimilarity. 

Os seguintes links descrevem a propriedade de semelhança nos Azure SDKs. 

| Biblioteca de cliente | Propriedade de semelhança |
|----------------|---------------------|
| .NET  | [SearchIndex.Similaridade](/dotnet/api/azure.search.documents.indexes.models.searchindex.similarity) |
| Java | [SearchIndex.setSimilarity](/java/api/com.azure.search.documents.indexes.models.searchindex.setsimilarity) |
| JavaScript | [SearchIndex.Similaridade](/javascript/api/@azure/search-documents/searchindex#similarity) |
| Python | [propriedade de semelhança no SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) |

### <a name="rest-example"></a>Exemplo DE REPOUSO

Também pode utilizar a [API REST,](/rest/api/searchservice/create-index)como ilustra o seguinte exemplo:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

## <a name="set-bm25-parameters"></a>Definir parâmetros BM25

A semelhança BM25 adiciona dois parâmetros personalizáveis ao utilizador para controlar a pontuação de relevância calculada. Pode definir parâmetros BM25 durante a criação de índices ou como uma atualização de índice se o algoritmo BM25 foi especificado durante a criação do índice.

| Propriedade | Tipo | Description |
|----------|------|-------------|
| k1 | número | Controla a função de escala entre a frequência do termo de cada termos correspondentes à pontuação de relevância final de um par de consultas de documento. Os valores são geralmente de 0,0 a 3,0, com 1.2 como padrão. </br></br>Um valor de 0.0 representa um "modelo binário", em que a contribuição de um único termo correspondente é a mesma para todos os documentos correspondentes, independentemente do número de vezes que esse termo aparece no texto, enquanto um valor k1 maior permite que a pontuação continue a aumentar à medida que mais instâncias do mesmo termo são encontradas no documento. </br></br>A utilização de um valor k1 superior pode ser importante nos casos em que esperamos que vários termos sejam parte de uma consulta de pesquisa. Nesses casos, podemos querer favorecer documentos que correspondam a muitos dos diferentes termos de consulta que estão a ser pesquisados sobre documentos que apenas correspondem a um único, várias vezes. Por exemplo, ao consultar o índice de documentos que contenham os termos "Apollo Spaceflight", talvez queiramos baixar a pontuação de um artigo sobre mitologia grega que contém o termo "Apollo" algumas dezenas de vezes, sem menções de "Spaceflight", em comparação com outro artigo que menciona explicitamente tanto "Apollo" como "Spaceflight" apenas algumas vezes. |
| b | número | Controla como o comprimento de um documento afeta a pontuação de relevância. Os valores estão entre 0 e 1, com 0,75 como o padrão. </br></br>Um valor de 0.0 significa que o comprimento do documento não influenciará a pontuação, enquanto um valor de 1.0 significa que o impacto da frequência de prazo na pontuação de relevância será normalizado pelo comprimento do documento. </br></br>Normalizar a frequência do termo pelo comprimento do documento é útil nos casos em que queremos penalizar documentos mais longos. Em alguns casos, documentos mais longos (como um romance completo), são mais propensos a conter muitos termos irrelevantes, em comparação com documentos muito mais curtos. |

### <a name="setting-k1-and-b-parameters"></a>Definição de parâmetros k1 e b

Para definir ou modificar valores b ou k1, adicione-os ao objeto de semelhança BM25. A definição ou alteração destes valores num índice existente irá desativar o índice durante pelo menos alguns segundos, fazendo com que os pedidos de indexação e consulta ativos falhem. Consequentemente, deve definir o parâmetro "allowIndexDowntime=true" do pedido de atualização:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30&allowIndexDowntime=true
{
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
}
```

## <a name="see-also"></a>Ver também  

+ [Referência API DE REPOUSO](/rest/api/searchservice/)
+ [Adicione perfis de pontuação ao seu índice](index-add-scoring-profiles.md)
+ [Criar API de Índice](/rest/api/searchservice/create-index)
+ [Pesquisa Cognitiva Azure .NET SDK](/dotnet/api/overview/azure/search)