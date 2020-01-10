---
title: Redefinir habilidades (API-Version = 2019-050-06-Preview)
titleSuffix: Azure Cognitive Search
description: API REST de visualização usada para aprimorar a incrementação quando você precisar de um reprocessamento total ou parcial de um Skill.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4557e966c431eca31aa7577bebc75caae9365dc9
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832162"
---
# <a name="reset-skills-api-version2019-05-06-preview"></a>Redefinir habilidades (API-Version = 2019-05-06-Preview)

> [!IMPORTANT] 
> O enriquecimento incremental está atualmente em visualização pública. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para Portal ou SDK do .NET no momento.

A solicitação **Redefinir habilidades** especifica quais habilidades processar na próxima execução do indexador. Para indexadores que têm o Caching habilitado, você pode solicitar explicitamente o processamento de atualizações de habilidades que o indexador não pode detectar. Por exemplo, se você fizer alterações externas, como revisões para uma habilidade personalizada, poderá usar essa API para executar novamente a habilidade. As saídas, como uma loja de conhecimento ou um índice de pesquisa, são atualizadas usando dados reutilizáveis do cache e o novo conteúdo de acordo com a habilidade atualizada.

Você pode redefinir um confirmador de [habilidades](https://docs.microsoft.com/rest/api/searchservice/create-skillset) existente usando um http put, especificando o nome do qualificable a ser atualizado no URI de solicitação. Você deve usar **API-Version = 2019-05-06-Preview** na solicitação.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```  

Como alternativa, use POST e coloque o nome do indexador no corpo da solicitação:

```http
POST https://[service name].search.windows.net/indexers/resetskills?api-version=[api-version]  
api-key: [admin key]  
```  

## <a name="request-headers"></a>Cabeçalhos do pedido  

 A tabela a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.  

|Cabeçalho do Pedido|Descrição|  
|--------------------|-----------------|  
|*Content-Type:*|Necessário. Defina isso para `application/json`|  
|*chave de API:*|Necessário. O `api-key` é usado para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo para seu serviço. A solicitação redefinir conjunto de **qualificações** deve incluir um cabeçalho de `api-key` definido para sua chave de administração (em oposição a uma chave de consulta).|  

Você também precisa do nome do serviço para construir a URL de solicitação. Você pode obter o nome do serviço e `api-key` na sua página de visão geral do serviço na portal do Azure. Consulte [criar um serviço de pesquisa cognitiva do Azure](https://docs.microsoft.com/azure/search/search-create-service-portal) para obter ajuda de navegação de página.  

## <a name="request-body-syntax"></a>Sintaxe do corpo da solicitação  

O corpo da solicitação é uma matriz de nomes de habilidades.

```json
{   
    "skillNames" : ["<SKILL-1>", "SKILL-n"]
}  
```

## <a name="response"></a>Resposta  

Código de status: 204 nenhum conteúdo para uma resposta bem-sucedida. 

## <a name="see-also"></a>Ver também

+ [Pesquisar APIs REST](https://docs.microsoft.com/rest/api/searchservice)
+ [Códigos de estado HTTP](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)  
+ [Visão geral de enriquecimento de ia](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)
+ [Configurar o Caching e o enriquecimento incremental](search-howto-incremental-index.md)
+ [Enriquecimento incremental](cognitive-search-incremental-indexing-conceptual.md)