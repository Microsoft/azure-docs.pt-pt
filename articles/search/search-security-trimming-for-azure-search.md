---
title: Filtros de segurança para aparar resultados
titleSuffix: Azure Cognitive Search
description: Controlo de acesso ao conteúdo da Pesquisa Cognitiva Azure utilizando filtros de segurança e identidades do utilizador.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 24f168f68a60ebb0408b7f1c367039ea5caea6d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794269"
---
# <a name="security-filters-for-trimming-results-in-azure-cognitive-search"></a>Filtros de segurança para aparar resultados em Pesquisa Cognitiva Azure

Pode aplicar filtros de segurança para aparar resultados de pesquisa em Pesquisa Cognitiva Azure com base na identidade do utilizador. Esta experiência de pesquisa geralmente requer comparar a identidade de quem solicita a pesquisa contra um campo que contém os princípios que têm permissões ao documento. Quando uma correspondência é encontrada, o utilizador ou principal (como um grupo ou função) tem acesso a esse documento.

Uma forma de conseguir a filtragem da segurança é através de `Id eq 'id1' or Id eq 'id2'`uma complicada disjunção de expressões de igualdade: por exemplo, e assim por diante. Esta abordagem é propensa a erros, difícil de manter, e nos casos em que a lista contém centenas ou milhares de valores, atrasa o tempo de resposta à consulta em muitos segundos. 

Uma abordagem mais simples `search.in` e rápida é através da função. Se usar `search.in(Id, 'id1, id2, ...')` em vez de uma expressão de igualdade, pode esperar tempos de resposta sub-segundo.

Este artigo mostra-lhe como realizar a filtragem de segurança utilizando os seguintes passos:
> [!div class="checklist"]
> * Criar um campo que contenha os principais identificadores 
> * Empurre ou atualize os documentos existentes com os principais identificadores relevantes
> * Emitir um pedido `search.in` de pesquisa com`filter`

>[!NOTE]
> O processo de recuperação dos principais identificadores não está abrangido neste documento. Devia sacá-lo ao seu fornecedor de serviços de identidade.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que tem uma [subscrição Azure,](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)serviço de [Pesquisa Cognitiva Azure](https://docs.microsoft.com/azure/search/search-create-service-portal)e Índice de [Pesquisa Cognitiva Azure.](https://docs.microsoft.com/azure/search/search-create-index-portal)  

## <a name="create-security-field"></a>Criar campo de segurança

Os seus documentos devem incluir um campo que especifique quais os grupos que têm acesso. Estas informações tornam-se os critérios de filtro contra os quais os documentos são selecionados ou rejeitados do conjunto de resultados devolvido ao emitente.
Vamos assumir que temos um índice de ficheiros protegidos, e cada ficheiro é acessível por um conjunto diferente de utilizadores.
1. Adicione `group_ids` o campo (pode escolher qualquer `Collection(Edm.String)`nome aqui) como . Certifique-se de `filterable` que o `true` campo tem um atributo definido para que os resultados da pesquisa sejam filtrados com base no acesso que o utilizador tem. Por exemplo, se `group_ids` definir `["group_id1, group_id2"]` o campo `file_name` para o documento com "secured_file_b", apenas os utilizadores que pertencem a ids de grupo "group_id1" ou "group_id2" leram o acesso ao ficheiro.
   Certifique-se de `retrievable` que o atributo do campo está definido para `false` que não seja devolvido como parte do pedido de pesquisa.
2. Adicione `file_id` também `file_name` e campos para o bem deste exemplo.  

```JSON
{
    "name": "securedfiles",  
    "fields": [
        {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "file_name", "type": "Edm.String"},
        {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
    ]
}
```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Empurrando dados para o seu índice usando a API REST
  
Emita um pedido HTTP POST ao ponto final do URL do seu índice. O corpo do pedido HTTP é um objeto JSON contendo os documentos a adicionar:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2019-05-06  
Content-Type: application/json
api-key: [admin key]
```

No organismo de pedido, especifique o conteúdo dos seus documentos:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Se precisar de atualizar um documento existente com a lista `merge` `mergeOrUpload` de grupos, pode utilizar a ou ação:

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

Para mais detalhes sobre a adição ou atualização de documentos, pode ler [documentos editar](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Aplicar o filtro de segurança

Para aparar documentos `group_ids` com base no acesso, deve `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` emitir uma consulta de pesquisa com um filtro, onde "group_id1, group_id2,..." são os grupos a que pertence o emitente de pedido de busca.
Este filtro corresponde a `group_ids` todos os documentos para os quais o campo contém um dos identificadores dado.
Para mais detalhes sobre a pesquisa de documentos que utilizam a Pesquisa Cognitiva Azure, pode ler [Documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents)de Pesquisa .
Note que esta amostra mostra como pesquisar documentos usando um pedido post.

Emissão do pedido HTTP POST:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2019-05-06
Content-Type: application/json  
api-key: [admin or query key]
```

Especifique o filtro no organismo de pedido:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Deve reaver os `group_ids` documentos onde contém "group_id1" ou "group_id2". Por outras palavras, obtém-se os documentos a que o emitente de pedido leu o acesso.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```
## <a name="conclusion"></a>Conclusão

É assim que pode filtrar os resultados `search.in()` com base na identidade do utilizador e na função de Pesquisa Cognitiva Azure. Pode utilizar esta função para passar em identificadores de princípio para o utilizador que solicita coincidir com os principais identificadores associados a cada documento-alvo. Quando um pedido de pesquisa `search.in` é tratado, a função filtra os resultados de pesquisa para os quais nenhum dos principais do utilizador tem acesso lido. Os principais identificadores podem representar coisas como grupos de segurança, papéis ou até mesmo a própria identidade do utilizador.
 
## <a name="see-also"></a>Consulte também

+ [Controlo de acesso baseado na identidade do Diretório Ativo utilizando filtros de pesquisa cognitiva Azure](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
+ [Segurança de dados e controlo de acesso em operações de Pesquisa Cognitiva Azure](search-security-overview.md)