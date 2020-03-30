---
title: Algoritmo de semelhança de ranking
titleSuffix: Azure Cognitive Search
description: Como definir o algoritmo de semelhança para experimentar novo algoritmo de semelhança para o ranking
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: c327440649300533c94c2a1956e3c45f433c9780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409976"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Algoritmo de ranking em Pesquisa Cognitiva Azure

> [!IMPORTANT]
> A partir de 15 de julho de 2020, os serviços de pesquisa recém-criados utilizarão a função de ranking BM25, que tem comprovado na maioria dos casos fornecer rankings de pesquisa que se alinham melhor com as expectativas dos utilizadores do que com o ranking padrão atual.  Além do ranking superior, o BM25 também permite opções de configuração para afinar resultados com base em fatores como o tamanho do documento.  
>
> Com esta alteração, é provável que veja ligeiras alterações na encomenda dos resultados da sua pesquisa.   Para aqueles que querem testar o impacto desta mudança, disponibilizámos na API 2019-05-06-Preview uma capacidade de permitir a pontuação do BM25 em novos índices.  

Este artigo descreve como pode atualizar um serviço criado antes de 15 de julho de 2020 para usar o novo algoritmo de classificação BM25.

A Azure Cognitive Search utilizará a implementação oficial de Lucene do algoritmo Okapi BM25, *BM25Similarity,* que substituirá a implementação da *ClassicSimilarity* anteriormente utilizada. Tal como o algoritmo de classicidade mais antigo, bM25Semelhança é uma função de recuperação semelhante a TF-IDF que usa a frequência de termo (TF) e a frequência de documento sinversa (IDF) como variáveis para calcular pontuações de relevância para cada par de consulta de documentos, que é então usado para classificação. Embora conceptualmente semelhante ao antigo algoritmo de semelhança clássica, bM25 toma a sua raiz na recuperação probabilística de informação para melhorar sobre ele. O BM25 também oferece opções avançadas de personalização, tais como permitir que o utilizador decida como a pontuação de relevância escala com a frequência de prazo de termos combinados.

## <a name="how-to-test-bm25-today"></a>Como testar bM25 hoje

Quando se cria um novo índice, pode definir uma propriedade de "semelhança". Terá de utilizar a versão *2019-05-06-Preview,* como mostrado abaixo.

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
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

Para serviços criados antes de 15 de julho de 2020: Se a semelhança for omitida ou definida como nula, o índice usará o antigo algoritmo de semelhança clássico.

Para serviços criados após 15 de julho de 2020: Se a semelhança for omitida ou definida como nula, o índice utilizará o novo algoritmo de semelhança BM25.

Também pode definir explicitamente o valor da semelhança para ser um dos dois valores seguintes: *"#Microsoft.Azure.Search.ClassicSimilarity"* ou *"#Microsoft.Azure.Search.BM25Similarity".*


## <a name="bm25-similarity-parameters"></a>Parâmetros de semelhança BM25

A semelhança BM25 adiciona dois parâmetros personalizáveis do utilizador para controlar a pontuação de relevância calculada:

### <a name="k1"></a>k1

O parâmetro *k1* controla a função de escalaentre a frequência de cada termos correspondentes à pontuação final de relevância de um par de consulta de documentos.

Um valor de zero representa um "modelo binário", em que a contribuição de um único termo correspondente é a mesma para todos os documentos correspondentes, independentemente do número de vezes que esse termo aparece no texto, enquanto um valor de K1 maior permite que a pontuação continue a aumentar como mais as instâncias do mesmo termo encontram-se no documento. Por padrão, a Pesquisa Cognitiva Azure utiliza um valor de 1.2 para o parâmetro k1. Usar um valor de K1 mais elevado pode ser importante nos casos em que esperamos que vários termos façam parte de uma consulta de pesquisa. Nesses casos, podemos querer favorecer documentos que correspondam a muitos dos diferentes termos de consulta que estão a ser pesquisados por documentos que só correspondem a um único, várias vezes. Por exemplo, ao consultar o índice de documentos que contenham os termos "Voo Espacial Apollo", podemos querer baixar a pontuação de um artigo sobre a Mitologia Grega que contém o termo "Apollo" algumas dezenas de vezes, sem menções de "Voo Espacial", em comparação com outro artigo que menciona explicitamente tanto "Apollo" como "Spaceflight" um punhado de vezes apenas. 
 
### <a name="b"></a>b

O parâmetro *b* controla como o comprimento de um documento afeta a pontuação de relevância.

Um valor de 0.0 significa que o comprimento do documento não influenciará a pontuação, enquanto um valor de 1.0 significa que o impacto da frequência de termo na pontuação de relevância será normalizado pelo comprimento do documento. O valor predefinido utilizado na Pesquisa Cognitiva Azure para o parâmetro b é de 0,75. Normalizar a frequência de termo pelo comprimento do documento é útil nos casos em que queremos penalizar documentos mais longos. Em alguns casos, documentos mais longos (como um romance completo), são mais propensos a conter muitos termos irrelevantes, em comparação com documentos muito mais curtos.

### <a name="setting-k1-and-b-parameters"></a>Definição de parâmetros k1 e b

Para personalizar os valores b ou k1, basta adicioná-los como propriedades ao objeto de semelhança ao utilizar bM25:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

O algoritmo de semelhança só pode ser definido no momento da criação do índice. Isto significa que o algoritmo de semelhança que está a ser utilizado não pode ser alterado para os índices existentes. Os parâmetros *"b"* e *"k1"* podem ser modificados ao atualizar uma definição de índice existente que utiliza BM25. A alteração desses valores num índice existente irá desativar o índice durante pelo menos alguns segundos, fazendo com que os seus pedidos de indexação e consulta falhem. Por isso, terá de definir o parâmetro "allowIndexDowntime=true" na cadeia de consulta do seu pedido de atualização:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```


## <a name="see-also"></a>Consulte também  

 [Repouso de Pesquisa Cognitiva Azure](https://docs.microsoft.com/rest/api/searchservice/)   
 [Adicione perfis de pontuação ao seu índice](index-add-scoring-profiles.md)    
 [Criar índice &#40;&#41;de API de pesquisa cognitiva de pesquisa cognitiva](https://docs.microsoft.com/rest/api/searchservice/create-index)   
  [Pesquisa Cognitiva Azure .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
