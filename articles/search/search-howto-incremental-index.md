---
title: Cache de configuração e enriquecimento incremental (pré-visualização)
titleSuffix: Azure Cognitive Search
description: Permitir o estado de caching e preservar o estado de conteúdo enriquecido para o processamento controlado numa habilidade cognitiva. Esta funcionalidade encontra-se atualmente em visualização pública.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a1b317b651b0e17c07eb17dbdb8a7c6657d39564
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90971616"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Como configurar caching para enriquecimento incremental em Azure Cognitive Search

> [!IMPORTANT] 
> O enriquecimento incremental está atualmente em visualização pública. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> [As versões de pré-visualização da API](search-api-preview.md) REST fornecem esta funcionalidade. Não existe porta ou suporte .NET SDK neste momento.

Este artigo mostra-lhe como adicionar caching a um oleoduto de enriquecimento para que possa modificar gradualmente passos sem ter que reconstruir cada vez. Por padrão, um skillset é apátrida, e mudar qualquer parte da sua composição requer uma repetição completa do indexante. Com o enriquecimento incremental, o indexante pode determinar quais as partes da árvore documental que precisam de ser atualizadas com base em alterações detetadas nas definições de skillset ou indexante. A produção processada existente é preservada e reutilizada sempre que possível. 

O conteúdo em cache é colocado no Azure Storage utilizando informações de conta que fornece. O recipiente, `ms-az-search-indexercache-<alpha-numerc-string>` denominado, é criado quando se funciona o indexante. Deve ser considerado um componente interno gerido pelo seu serviço de pesquisa e não deve ser modificado.

Se não está familiarizado com a configuração de indexantes, comece com [a visão geral do indexante](search-indexer-overview.md) e, em seguida, continue a [ter habilidades](cognitive-search-working-with-skillsets.md) para aprender sobre os oleodutos de enriquecimento. Para obter mais antecedentes em conceitos-chave, consulte [o enriquecimento incremental.](cognitive-search-incremental-indexing-conceptual.md)

## <a name="enable-caching-on-an-existing-indexer"></a>Ativar o caching num indexante existente

Se tiver um indexante existente que já tenha uma habilidade, siga os passos nesta secção para adicionar caching. Como uma operação única, terá de reiniciar e refazer o indexante na íntegra antes que o processamento incremental possa produzir efeitos.

> [!TIP]
> Como prova de conceito, pode correr através deste [portal de arranque rápido](cognitive-search-quickstart-blob.md) para criar objetos necessários e, em seguida, usar o Carteiro ou o portal para fazer as suas atualizações. É melhor anexar um recurso de Serviços Cognitivos faturados. Executar o indexante várias vezes esgotará a atribuição diária gratuita antes de poder completar todos os passos.

### <a name="step-1-get-the-indexer-definition"></a>Passo 1: Obter a definição de indexante

Comece com um indexante válido e existente que tenha estes componentes: fonte de dados, skillset, índice. O seu indexante deve ser executado. 

Utilizando um cliente API, construa um [pedido get indexer](/rest/api/searchservice/get-indexer) para obter a configuração atual do indexante. Quando utiliza a versão API de pré-visualização para o INDEXADOR GET, `cache` um conjunto de propriedade para nulo é adicionado às definições.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Copie a definição do indexante a partir da resposta.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>Passo 2: Modificar a propriedade cache na definição do indexante

Por defeito, a `cache` propriedade é nula. Utilize um cliente API para definir a configuração da cache (o portal não suporta esta atualização de partículas). 

Modifique o objeto de cache para incluir as seguintes propriedades necessárias e opcionais: 

+ O é necessário e deve ser definido para uma cadeia de `storageConnectionString` ligação de armazenamento Azure. 
+ A `enableReprocessing` propriedade booleana é opcional `true` (por padrão), e indica que o enriquecimento incremental está ativado. Quando necessário, pode defini-lo `false` para suspender o processamento incremental enquanto outras operações intensivas de recursos, como a indexação de novos documentos, estão em andamento e, em seguida, lançá-lo de volta para mais `true` tarde.

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>Passo 3: Redefinir o indexante

É necessário repor o indexante na criação de enriquecimento incremental para os indexantes existentes para garantir que todos os documentos estão em estado consistente. Pode utilizar o portal ou um cliente API e a [API do Indexer reset](/rest/api/searchservice/reset-indexer) para esta tarefa.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>Passo 4: Guarde a definição atualizada

[Atualizar o indexante](/rest/api/searchservice/preview-api/update-indexer) com um pedido PUT, o corpo do pedido deve conter a definição de indexante atualizada que tem a propriedade cache. Se conseguir um 400, verifique a definição do indexante para se certificar de que todos os requisitos estão cumpridos (fonte de dados, skillset, índice).

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

Se agora emitir outro pedido GET no indexante, a resposta do serviço incluirá uma `ID` propriedade no objeto cache. A cadeia alfanumérica é anexada ao nome do recipiente contendo todos os resultados em cache e o estado intermédio de cada documento processado por este indexante. O ID será usado para nomear exclusivamente a cache no armazenamento Blob.

```http
    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }
```

### <a name="step-5-run-the-indexer"></a>Passo 5: Executar o indexante

Para executar indexante, pode utilizar o portal ou a API. No portal, a partir da lista de indexadores, selecione o indexante e clique em **Executar**. Uma vantagem para usar o portal é que você pode monitorizar o estado do indexante, observar a duração do trabalho e quantos documentos são processados. As páginas do portal são atualizadas a cada poucos minutos.

Em alternativa, pode utilizar o REST para [executar o indexante:](/rest/api/searchservice/run-indexer)

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Depois do indexer, pode encontrar a cache no armazenamento da Azure Blob. O nome do recipiente encontra-se no seguinte formato: `ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> Um reset e uma repetição do indexante resulta numa reconstrução completa para que o conteúdo possa ser em cache. Todos os enriquecimentos cognitivos serão reexame em todos os documentos.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>Passo 6: Modificar um skillset e confirmar o enriquecimento incremental

Para modificar um skillset, pode utilizar o portal ou a API. Por exemplo, se estiver a utilizar a tradução de texto, uma simples alteração inline de `en` ou de outra língua é suficiente para testar a prova de conceito de `es` enriquecimento incremental.

Volte a executar o indexante. Apenas as partes de uma árvore de documento enriquecido são atualizadas. Se usou o [portal de arranque rápido](cognitive-search-quickstart-blob.md) como prova de conceito, modificando a habilidade de tradução de texto para 'es', irá notar que apenas 8 documentos são atualizados em vez dos 14 originais. Os ficheiros de imagem não afetados pelo processo de tradução são reutilizados a partir de cache.

## <a name="enable-caching-on-new-indexers"></a>Permitir o caching em novos indexadores

Para configurar o enriquecimento incremental para um novo indexante, tudo o que tem de fazer é incluir a `cache` propriedade na carga útil de definição de indexante ao chamar o Create [Indexer (2020-06-30-Preview)](/rest/api/searchservice/preview-api/create-indexer). 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>Verificação da saída em cache

A cache é criada, usada e gerida pelo indexante, e o seu conteúdo não está representado num formato que seja legível pelo homem. A melhor maneira de determinar se a cache é usada é executando o indexante e comparando as métricas antes e depois para o tempo de execução e contagem de documentos. 

Por exemplo, assuma um skillset que comece com a análise de imagem e reconhecimento de caracteres óticos (OCR) de documentos digitalizados, seguido de análise a jusante do texto resultante. Se modificar uma habilidade de texto a jusante, o indexante pode recuperar todos os conteúdos de imagem e OCR previamente processados a partir de cache, atualizando e processando apenas alterações relacionadas com textos indicadas pelas suas edições. Pode esperar ver menos documentos na contagem de documentos (por exemplo, 8/8 em oposição a 14/14 na execução original), tempos de execução mais curtos e menos encargos na sua conta.

## <a name="working-with-the-cache"></a>Trabalhar com a cache

Uma vez que a cache esteja operacional, os indexadores verificam a cache sempre que o [Índice de Execução](/rest/api/searchservice/run-indexer) é chamado, para ver quais as partes da saída existente. 

O quadro que se segue resume a forma como várias APIs se relacionam com a cache:

| API           | Impacto cache     |
|---------------|------------------|
| [Criar Indexer (2020-06-30-Pré-visualização)](/rest/api/searchservice/preview-api/create-indexer) | Cria e executa um indexante na primeira utilização, incluindo a criação de uma cache se a definição do indexante o especificar. |
| [Indexante de Execução](/rest/api/searchservice/run-indexer) | Executa um oleoduto de enriquecimento a pedido. Esta API lê a partir da cache se existe, ou cria uma cache se adicionar cache a uma definição de indexante atualizada. Quando executam um indexante que tem o cache ativado, o indexante omite passos se a saída em cache puder ser utilizada. Pode utilizar a versão API geralmente disponível ou de pré-visualização desta API.|
| [Indexante de Reset](/rest/api/searchservice/reset-indexer)| Limpa o indexante de qualquer informação de indexação incremental. A próxima execução do indexante (a pedido ou horário) é o reprocessamento completo do zero, incluindo a recorrência de todas as competências e a reconstrução da cache. É funcionalmente equivalente a eliminar o indexante e recriá-lo. Pode utilizar a versão API geralmente disponível ou de pré-visualização desta API.|
| [Competências de Reset](/rest/api/searchservice/preview-api/reset-skills) | Especifica quais as habilidades a repetir na próxima corrida do indexante, mesmo que não tenha modificado nenhuma habilidade. A cache é atualizada em conformidade. As saídas, como uma loja de conhecimento ou índice de pesquisa, são atualizadas usando dados reutilizáveis da cache mais novos conteúdos por habilidade atualizada. |

Para obter mais informações sobre o controlo do que acontece com a cache, consulte a [gestão cache.](cognitive-search-incremental-indexing-conceptual.md#cache-management)

## <a name="next-steps"></a>Passos seguintes

O enriquecimento incremental é aplicável em indexadores que contêm habilidades. Como próximo passo, visite a documentação skillset para entender conceitos e composição. 

Além disso, uma vez que você ativar a cache, você vai querer saber sobre os parâmetros e APIs que fator para cache, incluindo como sobrepor ou forçar comportamentos particulares. Para mais informações, consulte os seguintes links.

+ [Conceitos e composição de skillset](cognitive-search-working-with-skillsets.md)
+ [Como criar um skillset](cognitive-search-defining-skillset.md)
+ [Introdução ao enriquecimento incremental e caching](cognitive-search-incremental-indexing-conceptual.md)