---
title: Filtros de segurança para corte resultados - Azure Search
description: Controlo de acesso no conteúdo de Azure Search com filtros de segurança e as identidades dos utilizadores.
ms.service: search
ms.topic: conceptual
services: search
ms.date: 08/07/2017
author: brjohnstmsft
ms.author: brjohnst
manager: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: 326a449d3992d22a4be2d365061c99ef8b13aef9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453495"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Filtros de segurança para os resultados da remoção na Azure Search

Pode aplicar filtros de segurança para limitar os resultados da pesquisa no Azure Search com base na identidade do utilizador. Esta experiência de pesquisa geralmente requer a comparação entre a identidade de quem solicita a pesquisa em relação a um campo que contém os princípios que têm permissões para o documento. Quando é encontrada uma correspondência, o utilizador ou principal (por exemplo, um grupo ou função) tem acesso a um documento.

Uma forma de atingir a segurança de filtragem é por meio de uma disjunção complicada de expressões de igualdade: por exemplo, `Id eq 'id1' or Id eq 'id2'`, e assim por diante. Essa abordagem é propensa a erros, difícil de manter e pode atrasar em casos em que a lista contém centenas ou milhares de valores, tempo de resposta de consulta por vários segundos. 

Uma abordagem mais simples e rápida é por meio do `search.in` função. Se usar `search.in(Id, 'id1, id2, ...')` em vez de uma expressão de igualdade, pode esperar a resposta de frações de segundos vezes.

Este artigo mostra-lhe como realizar a filtragem de segurança utilizando os seguintes passos:
> [!div class="checklist"]
> * Criar um campo que contém os identificadores de principal 
> * Push ou atualizar documentos existentes com os identificadores de principal relevantes
> * Emitir um pedido de pesquisa com `search.in` `filter`

>[!NOTE]
> O processo de obtenção de identificadores de principal não é abrangido neste documento. Pode ser obtido a partir do seu fornecedor de serviços de identidade.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem um [subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [serviço Azure Search](https://docs.microsoft.com/azure/search/search-create-service-portal), e [índice da Azure Search](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Criar o campo de segurança

Os documentos têm de incluir um campo de especificar os grupos que têm acesso. Estas informações se torna os critérios de filtro em relação aos quais os documentos são selecionados ou rejeitados do conjunto de resultados devolvido para o emissor.
Vamos supor que temos um índice de arquivos protegidos, e cada arquivo é acessível por um conjunto diferente de usuários.
1. Adicionar campo `group_ids` (pode escolher qualquer nome aqui) como um `Collection(Edm.String)`. Certifique-se de que o campo tem um `filterable` atributo definido como `true` para que os resultados da pesquisa são filtrados com base no acesso do utilizador tem. Por exemplo, se definir o `group_ids` campo `["group_id1, group_id2"]` para o documento com `file_name` "secured_file_b", apenas os utilizadores que pertencem ao grupo ids "group_id1" ou "group_id2" leu o acesso ao ficheiro.
   Certificar-se de que o campo `retrievable` atributo está definido como `false` para que não é devolvido como parte da solicitação de pesquisa.
2. Adicionar também `file_id` e `file_name` campos para efeitos deste exemplo.  

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

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Enviar dados por push para o seu índice através da API REST
  
Emita um pedido HTTP POST para o ponto final do URL de seu índice. O corpo da solicitação HTTP é um objeto JSON que contém os documentos a adicionar:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=[api-version]  
Content-Type: application/json
api-key: [admin key]
```

No corpo do pedido, especifique o conteúdo dos seus documentos:

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

Para obter detalhes completos sobre adicionar ou atualizar documentos, pode ler [editar documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Aplique o filtro de segurança

Para cortar documentos com base na `group_ids` acesso, deve emitir uma consulta de pesquisa com um `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` filtro, onde "group_id1, group_id2,..." são os grupos a que pertence o emissor do pedido de pesquisa.
Este filtro corresponde a todos os documentos para o qual o `group_ids` campo contém um dos identificadores de determinado.
Para obter detalhes completos sobre a pesquisa de documentos com o Azure Search, pode ler [documentos sobre pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Tenha em atenção que este exemplo mostra como procurar nos documentos através de um pedido POST.

Emita o pedido de HTTP POST:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=[api-version]  
Content-Type: application/json  
api-key: [admin or query key]
```

Especifica o filtro no corpo do pedido:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Deve obter os documentos volta onde `group_ids` contém "group_id1" ou "group_id2". Em outras palavras, obtém os documentos para o qual o emissor do pedido tem acesso de leitura.

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

Esta é a forma como pode filtrar resultados com base na identidade de utilizador e o Azure Search `search.in()` função. Pode utilizar esta função para passar os identificadores de princípio do utilizador requerente para correspondência com identificadores principal associados a cada documento de destino. Quando um pedido de pesquisa é processado, o `search.in` função filtra os resultados da pesquisa para que nenhuma das entidades de segurança do utilizador tem acesso de leitura. Os identificadores principal podem representar coisas como grupos de segurança, funções ou até mesmo a identidade do utilizador.
 
## <a name="see-also"></a>Consulte também

+ [Controlo de acesso com base na identidade do Active Directory a utilizar os filtros de pesquisa do Azure](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtros no Azure Search](search-filters.md)
+ [Controlo de acesso e segurança de dados em operações de pesquisa do Azure](search-security-overview.md)