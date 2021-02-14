---
title: Algoritmo de semelhança de classificação
titleSuffix: Azure Cognitive Search
description: Como definir o algoritmo de semelhança para tentar um novo algoritmo de semelhança para o ranking
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: e2caa09d41abb1842100ed8259e82ec411390ccb
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520634"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Algoritmo de classificação em Azure Cognitive Search

> [!IMPORTANT]
> A partir de 15 de julho de 2020, os serviços de pesquisa recém-criados utilizarão automaticamente a função de ranking BM25, o que tem comprovado na maioria dos casos fornecer rankings de pesquisa que se alinham melhor com as expectativas dos utilizadores do que com o ranking padrão atual. Além do ranking superior, o BM25 também permite opções de configuração para afinar resultados com base em fatores como o tamanho do documento.  
>
> Com esta alteração, provavelmente verá ligeiras alterações na encomenda dos resultados da sua pesquisa. Para quem quiser testar o impacto desta mudança, o algoritmo BM25 está disponível na versão api 2019-05-06-Preview e em 2020-06-30.  

Este artigo descreve como pode utilizar o novo algoritmo de classificação BM25 nos serviços de pesquisa existentes para novos índices criados e consultados utilizando a API de pré-visualização.

A Azure Cognitive Search está em processo de adotar a implementação oficial de Lucene do algoritmo Okapi BM25, *BM25Similarity,* que substituirá a implementação *da ClassicSimilarity* anteriormente utilizada. Tal como o algoritmo de altaidade classic mais antigo, o BM25Similarity é uma função de recuperação semelhante à TF-IDF que utiliza o termo frequência (TF) e a frequência de documento inversa (IDF) como variáveis para calcular pontuações de relevância para cada par de consultas de documento, que é então usado para classificação. 

Embora conceptualmente semelhante ao algoritmo de semelhança clássica mais antigo, o BM25 tem a sua raiz na recuperação de informação probabilística para melhorar. O BM25 também oferece opções avançadas de personalização, tais como permitir que o utilizador decida como a pontuação de relevância escala com o termo frequência de termos combinados.

## <a name="how-to-test-bm25-today"></a>Como testar o BM25 hoje

Quando criar um novo índice, pode definir uma propriedade **de semelhança** para especificar o algoritmo. Pode utilizar o `api-version=2019-05-06-Preview` , como mostrado abaixo, ou `api-version=2020-06-30` .

```http
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

A propriedade **de semelhança** é útil durante este período provisório quando ambos os algoritmos estão disponíveis, apenas em serviços existentes. 

| Propriedade | Descrição |
|----------|-------------|
| semelhança | Opcional. Valores válidos incluem *"#Microsoft.Azure.Search.ClassicSimilarity"* ou *"#Microsoft.Azure.Search.BM25Similarity".* <br/> Requer `api-version=2019-05-06-Preview` ou posteriormente um serviço de pesquisa criado antes de 15 de julho de 2020. |

Para novos serviços criados após 15 de julho de 2020, o BM25 é utilizado automaticamente e é o único algoritmo de semelhança. Se tentar definir **semelhanças** `ClassicSimilarity` com um novo serviço, um erro de 400 será devolvido porque esse algoritmo não é suportado num novo serviço.

Para os serviços existentes criados antes de 15 de julho de 2020, a semelhança clássica continua a ser o algoritmo padrão. Se a propriedade **de semelhança** for omitida ou definida para nula, o índice usa o algoritmo Clássico. Se quiser utilizar o novo algoritmo, terá de definir a **semelhança** descrita acima.

## <a name="bm25-similarity-parameters"></a>Parâmetros de semelhança BM25

A semelhança BM25 adiciona dois parâmetros personalizáveis ao utilizador para controlar a pontuação de relevância calculada.

### <a name="k1"></a>k1

O parâmetro *k1* controla a função de escala entre a frequência do termo de cada termos correspondentes à pontuação de relevância final de um par de consultas de documento.

Um valor de zero representa um "modelo binário", em que a contribuição de um único termo correspondente é a mesma para todos os documentos correspondentes, independentemente do número de vezes que esse termo aparece no texto, enquanto um valor k1 maior permite que a pontuação continue a aumentar à medida que mais instâncias do mesmo termo são encontradas no documento. Por predefinição, a Azure Cognitive Search utiliza um valor de 1.2 para o parâmetro k1. A utilização de um valor k1 superior pode ser importante nos casos em que esperamos que vários termos sejam parte de uma consulta de pesquisa. Nesses casos, podemos querer favorecer documentos que correspondam a muitos dos diferentes termos de consulta que estão a ser pesquisados sobre documentos que apenas correspondem a um único, várias vezes. Por exemplo, ao consultar o índice de documentos que contenham os termos "Apollo Spaceflight", talvez queiramos baixar a pontuação de um artigo sobre mitologia grega que contém o termo "Apollo" algumas dezenas de vezes, sem menções de "Spaceflight", em comparação com outro artigo que menciona explicitamente tanto "Apollo" como "Spaceflight" apenas algumas vezes. 
 
### <a name="b"></a>b

O parâmetro *b* controla como o comprimento de um documento afeta a pontuação de relevância.

Um valor de 0.0 significa que o comprimento do documento não influenciará a pontuação, enquanto um valor de 1.0 significa que o impacto da frequência de prazo na pontuação de relevância será normalizado pelo comprimento do documento. O valor predefinido utilizado na Pesquisa Cognitiva de Azure para o parâmetro b é de 0,75. Normalizar a frequência do termo pelo comprimento do documento é útil nos casos em que queremos penalizar documentos mais longos. Em alguns casos, documentos mais longos (como um romance completo), são mais propensos a conter muitos termos irrelevantes, em comparação com documentos muito mais curtos.

### <a name="setting-k1-and-b-parameters"></a>Definição de parâmetros k1 e b

Para personalizar os valores b ou k1, basta adicioná-los como propriedades ao objeto de semelhança ao utilizar o BM25:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

O algoritmo de semelhança só pode ser definido no tempo de criação de índices. Isto significa que o algoritmo de semelhança que está a ser usado não pode ser alterado para os índices existentes. Os parâmetros *"b"* e *"k1"* podem ser modificados ao atualizar uma definição de índice existente que utiliza BM25. Alterar estes valores num índice existente irá desativar o índice durante pelo menos alguns segundos, fazendo com que os seus pedidos de indexação e consulta falhem. Por isso, terá de definir o parâmetro "allowIndexDowntime=true" na sequência de consulta do seu pedido de atualização:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

## <a name="see-also"></a>Ver também  

+ [Referência API DE REPOUSO](/rest/api/searchservice/)
+ [Adicione perfis de pontuação ao seu índice](index-add-scoring-profiles.md)
+ [Criar API de Índice](/rest/api/searchservice/create-index)
+ [Pesquisa Cognitiva Azure .NET SDK](/dotnet/api/overview/azure/search)