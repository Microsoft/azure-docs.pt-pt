---
title: Filtros de segurança para aparar resultados
titleSuffix: Azure Cognitive Search
description: Saiba como implementar privilégios de segurança ao nível do documento para os resultados da pesquisa cognitiva do Azure, utilizando filtros de segurança e identidades do utilizador.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: 8bd162fcf2011d2ccce716564763e7f54f19ff69
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97631808"
---
# <a name="security-filters-for-trimming-results-in-azure-cognitive-search"></a>Filtros de segurança para aparar resultados na Pesquisa Cognitiva Azure

Pode aplicar filtros de segurança para aparar os resultados da pesquisa em Azure Cognitive Search com base na identidade do utilizador. Esta experiência de pesquisa geralmente requer comparar a identidade de quem solicita a pesquisa contra um campo contendo os princípios que têm permissões para o documento. Quando uma correspondência é encontrada, o utilizador ou principal (como um grupo ou papel) tem acesso a esse documento.

Uma forma de conseguir a filtragem da segurança é através de uma disjunção complicada das expressões de igualdade: por exemplo, `Id eq 'id1' or Id eq 'id2'` e assim por diante. Esta abordagem é propensa a erros, difícil de manter, e nos casos em que a lista contém centenas ou milhares de valores, abranda o tempo de resposta da consulta em muitos segundos. 

Uma abordagem mais simples e rápida é através da `search.in` função. Se utilizar `search.in(Id, 'id1, id2, ...')` em vez de uma expressão de igualdade, pode esperar tempos de resposta de sub-segundo.

Este artigo mostra-lhe como realizar a filtragem de segurança utilizando os seguintes passos:
> [!div class="checklist"]
> * Criar um campo que contenha os principais identificadores 
> * Empurre ou atualize os documentos existentes com os identificadores principais relevantes
> * Emite um pedido de pesquisa com `search.in``filter`

>[!NOTE]
> O processo de recuperação dos identificadores principais não está abrangido pelo presente documento. Devia obtê-lo do seu fornecedor de serviços de identidade.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma [subscrição Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), um[serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md), e um [índice](search-what-is-an-index.md).  

## <a name="create-security-field"></a>Criar campo de segurança

Os seus documentos devem incluir um campo que especifique quais os grupos que têm acesso. Estas informações tornam-se os critérios de filtragem contra os quais os documentos são selecionados ou rejeitados do conjunto de resultados devolvidos ao emitente.
Vamos supor que temos um índice de ficheiros seguros, e cada ficheiro é acessível por um conjunto diferente de utilizadores.

1. Adicione campo `group_ids` (pode escolher qualquer nome aqui) como um `Collection(Edm.String)` . Certifique-se de que o campo tem um `filterable` atributo definido para que os `true` resultados da pesquisa sejam filtrados com base no acesso que o utilizador tem. Por exemplo, se definir o `group_ids` campo para o documento com `["group_id1, group_id2"]` `file_name` "secured_file_b", apenas os utilizadores que pertencem aos ids de grupo "group_id1" ou "group_id2" leram o acesso ao ficheiro.
   
   Certifique-se de que o atributo do campo `retrievable` está definido para que não seja devolvido como parte do pedido de `false` pesquisa.

2. Adicione `file_id` também e `file_name` campos para o bem deste exemplo.  

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
  
Emita um pedido HTTP POST para o ponto final do URL do seu índice. O corpo do pedido HTTP é um objeto JSON que contém os documentos a adicionar:

```http
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2020-06-30  
Content-Type: application/json
api-key: [admin key]
```

No órgão de pedido, especifique o conteúdo dos seus documentos:

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

Se precisar de atualizar um documento existente com a lista de grupos, pode utilizar o `merge` ou `mergeOrUpload` ação:

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

Para obter detalhes completos sobre a adição ou atualização de documentos, pode ler [documentos de Edição](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="apply-the-security-filter"></a>Aplicar o filtro de segurança

Para aparar documentos com base no `group_ids` acesso, deve emitir uma consulta de pesquisa com um `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` filtro, onde "group_id1, group_id2,..." são os grupos a que pertence o emitente de pedido de pesquisa.

Este filtro corresponde a todos os documentos para os quais o `group_ids` campo contém um dos identificadores.
Para obter detalhes completos sobre a pesquisa de documentos usando a Azure Cognitive Search, pode ler [Documentos de Pesquisa.](/rest/api/searchservice/search-documents)
Note que esta amostra mostra como pesquisar documentos usando um pedido DEM.

Emite o pedido HTTP POST:

```http
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2020-06-30
Content-Type: application/json  
api-key: [admin or query key]
```

Especificar o filtro no corpo do pedido:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Deve obter os documentos de volta onde `group_ids` contém "group_id1" ou "group_id2". Por outras palavras, obtém-se os documentos aos quais o emitente de pedido leu o acesso.

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

## <a name="next-steps"></a>Passos seguintes

Este artigo descreveu um padrão para filtrar resultados com base na identidade do utilizador e na `search.in()` função. Pode utilizar esta função para passar, em princípio, identificadores para que o utilizador que solicita corresponda aos identificadores principais associados a cada documento-alvo. Quando um pedido de pesquisa é tratado, a `search.in` função filtra os resultados de pesquisa para os quais nenhum dos principais do utilizador leu o acesso. Os principais identificadores podem representar coisas como grupos de segurança, papéis ou mesmo a própria identidade do utilizador.

Para obter um padrão alternativo baseado no Ative Directory, ou para revisitar outras funcionalidades de segurança, consulte os seguintes links.

* [Filtros de segurança para aparar resultados usando identidades do Ative Directory](search-security-trimming-for-azure-search-with-aad.md)
* [Segurança em Pesquisa Cognitiva Azure](search-security-overview.md)