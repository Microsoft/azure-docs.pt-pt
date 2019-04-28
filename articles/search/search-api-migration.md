---
title: Atualizar para a versão mais recente do API de REST do serviço de pesquisa do Azure - Azure Search
description: Reveja as diferenças em versões de API e saiba quais ações são necessárias para migrar o código existente para a versão mais recente do API de REST do serviço de pesquisa do Azure.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 23003859b9a75fb986fe65f5528004f3dd150f9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126996"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Atualizar para a versão mais recente do API de REST do serviço de pesquisa do Azure
Se estiver a utilizar uma versão anterior dos [API de REST do serviço do Azure Search](https://docs.microsoft.com/rest/api/searchservice/), este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão mais recente versão de API em disponibilidade geral, 2017-11-11.

Versão 2017-11-11 da REST API contém algumas alterações de versões anteriores. Estes são principalmente compatíveis com versões anteriores, para que alterar o seu código deve exigir um esforço mínimo apenas, dependendo da versão estava a utilizar antes. Ver [passos para atualizar](#UpgradeSteps) para obter instruções sobre como alterar o seu código para utilizar a nova versão de API.

> [!NOTE]
> A instância de serviço do Azure Search oferece suporte a várias versões de REST API, incluindo o mais recente. Pode continuar a utilizar uma versão quando ele é já não é a mais recente, mas recomendamos que migre o código para usar a versão mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2017-11-11"></a>O que há de novo na versão 2017-11-11
Versão 2017-11-11 é a versão mais recente lançamento em disponibilidade geral da API de REST do serviço de pesquisa do Azure. Novas funcionalidades nesta versão de API incluem:

* [Sinónimos](search-synonyms.md). A nova funcionalidade de sinónimos permite-lhe definir termos equivalentes e expandir o âmbito da consulta.
* [Suporte para indexação com eficiência os blobs de texto](https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#IndexingPlainText). O novo `text` análise significativamente o modo para indexadores de Blobs do Azure melhora o desempenho da indexação.
* [Estatísticas de API de serviço](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). Ver a utilização atual e os limites de recursos no Azure Search com esta nova API.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Se estiver a atualizar a partir de uma versão de disponibilidade geral, 2015-02-28 ou 2016-09-01, provavelmente não precisará que fazer alterações ao seu código, além de alterar o número de versão. As únicas situações em que poderá ter de alterar o código são quando:

* Seu código falha quando propriedades não reconhecidas são retornadas numa resposta de API. Por predefinição, a aplicação deve ignorar propriedades que não compreende.
* O código mantém os pedidos de API e tenta reenviá-los para a nova versão de API. Por exemplo, isto pode acontecer se a sua aplicação persistir tokens de continuação devolvidos a partir da API de pesquisa (para obter mais informações, procure `@search.nextPageParameters` no [referência da API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Se qualquer uma destas situações se aplicar a, poderá ter de alterar o seu código em conformidade. Caso contrário, não existem alterações não será necessárias, a menos que deseja começar a utilizar o [novos recursos](#WhatsNew) da versão 2017-11-11.

Se estiver a atualizar a partir de uma versão de api de pré-visualização, o procedimento acima também se aplica, mas também deve estar ciente de que algumas funcionalidades de pré-visualização não estão disponíveis na versão 2017-11-11:

* Suporte de indexador de armazenamento de Blobs do Azure para ficheiros CSV e de blobs que contém as matrizes de JSON.
* Consultas de "Mais semelhante à seguinte"

Se o seu código Use esses recursos, não será capaz de atualizar para 2017-11-11 sem remover a sua utilização dos mesmos.

> [!IMPORTANT]
> . Lembre-se, pré-visualizar APIs destinam-se para teste e avaliação e não deve ser usadas em ambientes de produção.
> 
> 

## <a name="conclusion"></a>Conclusão
Se precisar de obter mais detalhes sobre como utilizar a API de REST do serviço de pesquisa do Azure, veja recém-atualizado [referência da API](https://docs.microsoft.com/rest/api/searchservice/) no MSDN.

Apreciamos os seus comentários sobre o Azure Search. Se tiver problemas, fique à vontade contacte-nos para obter ajuda sobre o [fórum MSDN do Azure Search](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) ou [StackOverflow](https://stackoverflow.com/). Se está pedindo uma pergunta sobre o Azure Search no Stack Overflow, certifique-se para marcá-la com `azure-search`.

Obrigado por utilizar o Azure Search!

