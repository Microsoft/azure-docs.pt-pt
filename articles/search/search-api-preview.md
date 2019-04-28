---
title: REST API de pré-visualização para pré-visualização do Azure Search 2017-11-11-- o Azure Search
description: Do Azure Search Service REST 2017 versão de API-11-11-pré-visualização inclui funcionalidades experimentais, como pesquisas de sinónimos e moreLikeThis.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 06/28/2018
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 524c1a6d083db02349c7dae9a0131228613dc170
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127098"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Versão de api REST do serviço de pesquisa do Azure 2017-11-11-pré-visualização
Este artigo descreve o `api-version=2017-11-11-Preview` versão do serviço de Azure Search REST API, oferecendo funcionalidades experimentais não ainda em disponibilidade geral.

> [!NOTE]
> Funcionalidades de pré-visualização estão disponíveis para teste e experimentação com o objetivo de recolha de comentários e estão sujeitos a alterações. É altamente recomendável contra a utilização de APIs de pré-visualização em aplicações de produção.


## <a name="new-in-2017-11-11-preview"></a>Novo no 2017-11-11-pré-visualização

[**Preenchimento automático** ](search-autocomplete-tutorial.md) associa a atual [sugestões API](https://docs.microsoft.com/rest/api/searchservice/suggestions) adicionar experiências complementar antecipada à barra de pesquisa. Preenchimento automático devolve Release candidate termos de consulta, que um usuário pode escolher como a cadeia de consulta de uma pesquisa subsequente. Sugestões devolve os documentos reais em resposta às entradas parciais: os resultados da pesquisa são imediatos e alterar dinamicamente à medida que aumenta a entrada do termo de pesquisa de comprimento e a especificidade.

[**Pesquisa cognitiva**](cognitive-search-concept-intro.md), uma nova funcionalidade de melhoria no Azure Search encontra informações latentes em origens que não sejam de texto e texto indiferenciado, transformá-los em conteúdo pesquisável de texto completo no Azure Search. Os recursos a seguir são apresentados ou modificados na pré-visualização da REST API. Todas as outras APIs REST são as mesmas, quer chamar disponível em geral ou a versão de pré-visualização.

+ [Conjunto de capacidades operations(api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/skillset-operations)

+ [Criar indexador (api-version = 2017-11-11-pré-visualização)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

+ [Competências predefinidas](cognitive-search-predefined-skills.md)

Todas as outras APIs REST são iguais, independentemente de como definir a versão de api. Por exemplo, `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` e `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11` (sem `Preview`) são funcionalmente equivalentes.

## <a name="other-preview-features"></a>Outras funcionalidades de pré-visualização

Funcionalidades anunciadas em pré-visualizações anteriores ainda estão em pré-visualização pública. Se estiver chamando uma API com uma api-version anterior pré-visualização, pode continuar a utilizar essa versão ou mudar para `2017-11-11-Preview` sem alterações ao comportamento esperado.

+ [Ficheiros CSV na indexação de Blobs do Azure](search-howto-index-csv-blobs.md), introduzida nos `api-version=2015-02-28-Preview`, permanece uma funcionalidade de pré-visualização. Esta funcionalidade faz parte de indexação de Blobs do Azure e é invocada por uma definição de parâmetro. Cada linha de um arquivo CSV é indexada como um documento separado.

+ [Matrizes JSON na indexação de Blobs do Azure](search-howto-index-json-blobs.md), introduzida nos `api-version=2015-02-28-Preview`, permanece uma funcionalidade de pré-visualização. Esta funcionalidade faz parte de indexação de Blobs do Azure e é invocada por uma definição de parâmetro. em que cada elemento da matriz é indexado como um documento separado.

+ [parâmetro de consulta de moreLikeThis](search-more-like-this.md) documentos que são relevantes para um documento específico. Esta funcionalidade está pré-visualizações anteriores. 


## <a name="how-to-call-a-preview-api"></a>Como chamar uma API de pré-visualização

Pré-visualizações mais antigas são ainda está operacionais, mas se tornem obsoletas ao longo do tempo. Se o seu código chamará `api-version=2016-09-01-Preview` ou `api-version=2015-02-28-Preview`, essas chamadas ainda são válidas. No entanto, apenas a versão de pré-visualização mais recente é atualizada com melhorias. 

A sintaxe de exemplo seguinte ilustra uma chamada para a versão de API de pré-visualização.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

Serviço do Azure Search está disponível em várias versões. Para obter mais informações, consulte [versões de API](search-api-versions.md).
