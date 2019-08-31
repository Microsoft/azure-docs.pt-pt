---
title: Filtros de segurança para corte de resultados-Azure Search
description: Controle de acesso no conteúdo de Azure Search usando os filtros de segurança e as identidades de usuário.
ms.service: search
ms.topic: conceptual
services: search
ms.date: 05/02/2019
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
ms.custom: seodec2018
ms.openlocfilehash: 4d1ffa5b29a56d32a4f6a8ccf40f5bafd27795e6
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186486"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Filtros de segurança para aparar resultados em Azure Search

Você pode aplicar filtros de segurança para cortar os resultados da pesquisa em Azure Search com base na identidade do usuário. Essa experiência de pesquisa geralmente requer a comparação da identidade de quem solicita a pesquisa em um campo que contém os princípios que têm permissões para o documento. Quando uma correspondência é encontrada, o usuário ou a entidade de segurança (como um grupo ou função) tem acesso a esse documento.

Uma maneira de obter a filtragem de segurança é por meio de uma disjunção complicada de expressões de `Id eq 'id1' or Id eq 'id2'`igualdade: por exemplo,, e assim por diante. Essa abordagem é propensa a erros, difícil de manter e, em casos em que a lista contém centenas ou milhares de valores, reduz o tempo de resposta da consulta em muitos segundos. 

Uma abordagem mais simples e mais rápida é por `search.in` meio da função. Se você usar `search.in(Id, 'id1, id2, ...')` em vez de uma expressão de igualdade, poderá esperar tempos de resposta de segundo plano.

Este artigo mostra como realizar a filtragem de segurança usando as seguintes etapas:
> [!div class="checklist"]
> * Criar um campo que contenha os identificadores de entidade 
> * Enviar por Push ou atualizar documentos existentes com os identificadores de entidade relevantes
> * Emitir uma solicitação de pesquisa `search.in` com`filter`

>[!NOTE]
> O processo de recuperação de identificadores de entidade de segurança não é abordado neste documento. Você deve obtê-lo do seu provedor de serviços de identidade.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tenha uma [assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [Azure Search serviço](https://docs.microsoft.com/azure/search/search-create-service-portal)e [Azure Search índice](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Criar campo de segurança

Os documentos devem incluir um campo especificando quais grupos têm acesso. Essas informações se tornam os critérios de filtro em relação aos quais os documentos são selecionados ou rejeitados do conjunto de resultados retornados ao emissor.
Vamos supor que temos um índice de arquivos protegidos, e cada arquivo pode ser acessado por um conjunto diferente de usuários.
1. Adicionar campo `group_ids` (você pode escolher qualquer nome aqui) como um `Collection(Edm.String)`. Verifique se o campo tem um `filterable` atributo definido como `true` para que os resultados da pesquisa sejam filtrados com base no acesso que o usuário tem. Por exemplo, se você definir o `group_ids` campo como `["group_id1, group_id2"]` para o documento com `file_name` "secured_file_b", somente os usuários que pertencem a IDs de grupo "group_id1" ou "group_id2" terão acesso de leitura ao arquivo.
   Verifique se o atributo do `retrievable` campo está definido como `false` para que ele não seja retornado como parte da solicitação de pesquisa.
2. Além disso `file_id` , `file_name` adicione campos e para fins deste exemplo.  

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

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Enviando dados por push para o índice usando a API REST
  
Emita uma solicitação HTTP POST para o ponto de extremidade da URL do índice. O corpo da solicitação HTTP é um objeto JSON que contém os documentos a serem adicionados:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2019-05-06  
Content-Type: application/json
api-key: [admin key]
```

No corpo da solicitação, especifique o conteúdo dos seus documentos:

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

Se precisar atualizar um documento existente com a lista de grupos, você poderá usar a `merge` ação ou: `mergeOrUpload`

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

Para obter detalhes completos sobre como adicionar ou atualizar documentos, você pode ler [editar documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Aplicar o filtro de segurança

Para cortar documentos com base no `group_ids` acesso, você deve emitir uma consulta de pesquisa com um `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` filtro, onde ' group_id1, group_id2,... ' são os grupos aos quais o emissor de solicitação de pesquisa pertence.
Esse filtro corresponde a todos os documentos para `group_ids` os quais o campo contém um dos identificadores fornecidos.
Para obter detalhes completos sobre como pesquisar documentos usando Azure Search, você pode ler [documentos de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Observe que este exemplo mostra como pesquisar documentos usando uma solicitação POST.

Emita a solicitação HTTP POST:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2019-05-06
Content-Type: application/json  
api-key: [admin or query key]
```

Especifique o filtro no corpo da solicitação:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Você deve obter os documentos de volta `group_ids` onde contém "group_id1" ou "group_id2". Em outras palavras, você obtém os documentos aos quais o emissor da solicitação tem acesso de leitura.

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

É assim que você pode filtrar os resultados com base na identidade do `search.in()` usuário e Azure Search função. Você pode usar essa função para passar identificadores principais para que o usuário solicitante coincida com os identificadores de entidade de segurança associados a cada documento de destino. Quando uma solicitação de pesquisa é manipulada `search.in` , a função filtra os resultados da pesquisa para os quais nenhuma das entidades do usuário tem acesso de leitura. Os identificadores principais podem representar itens como grupos de segurança, funções ou até mesmo a própria identidade do usuário.
 
## <a name="see-also"></a>Consulte também

+ [Active Directory controle de acesso baseado em identidade usando filtros Azure Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtros no Azure Search](search-filters.md)
+ [Segurança de dados e controle de acesso em operações Azure Search](search-security-overview.md)