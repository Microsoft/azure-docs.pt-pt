---
title: Configurar cache e enriquecimento incremental (pré-visualização)
titleSuffix: Azure Cognitive Search
description: Permitir o estado de cache e preservar o estado de conteúdo enriquecido para o processamento controlado numa habilidade cognitiva. Esta funcionalidade encontra-se atualmente em pré-visualização pública.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 66bac2a063a3257a2101ca2f30e5946264adb9ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76989557"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Como configurar o caching para enriquecimento incremental em Pesquisa Cognitiva Azure

> [!IMPORTANT] 
> O enriquecimento incremental encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure . A [versão REST API 2019-05-06-Preview](search-api-preview.md) fornece esta funcionalidade. Não existe nenhum suporte de Portal ou .NET SDK neste momento.

Este artigo mostra-lhe como adicionar cache a um oleoduto de enriquecimento para que possa modificar gradualmente passos sem ter que reconstruir cada vez. Por padrão, um skillset é apátrida, e mudar qualquer parte da sua composição requer uma reexecução completa do indexante. Com o enriquecimento incremental, o indexante pode determinar quais as partes da árvore documental que precisam de ser renovadas com base nas alterações detetadas nas definições de skillset ou indexer. A produção processada existente é preservada e reutilizada sempre que possível. 

O conteúdo em cache é colocado no Armazenamento Azure usando informações de conta que fornece. O recipiente, `ms-az-search-indexercache-<alpha-numerc-string>`chamado , é criado quando se executa o indexador. Deve ser considerado um componente interno gerido pelo seu serviço de pesquisa e não deve ser modificado.

Se não está familiarizado com a configuração de indexantes, comece com a [visão geral do indexante](search-indexer-overview.md) e continue a ter [habilidades](cognitive-search-working-with-skillsets.md) para aprender sobre os oleodutos de enriquecimento. Para mais informações sobre conceitos-chave, consulte [o enriquecimento incremental.](cognitive-search-incremental-indexing-conceptual.md)

## <a name="enable-caching-on-an-existing-indexer"></a>Ativar o cache num indexante existente

Se tiver um indexante existente que já tenha uma habilidade, siga os passos nesta secção para adicionar cache. Como uma operação única, terá de redefinir e reexecutar o indexante na íntegra antes que o processamento incremental possa produzir efeito.

> [!TIP]
> Como prova de conceito, pode passar por este [portal rapidamente](cognitive-search-quickstart-blob.md) para criar objetos necessários e, em seguida, usar o Carteiro ou o portal para fazer as suas atualizações. Talvez queira anexar um recurso de Serviços Cognitivos faturados. Executar o indexador várias vezes irá esgotar a alocação diária gratuita antes de poder completar todos os passos.

### <a name="step-1-get-the-indexer-definition"></a>Passo 1: Obter a definição de indexante

Comece com um indexador válido e existente que tenha estes componentes: fonte de dados, skillset, índice. O seu indexante deve ser executado. 

Utilizando um cliente API, construa um pedido get [Indexer](https://docs.microsoft.com/rest/api/searchservice/get-indexer) para obter a configuração atual do indexador. Quando utiliza a versão API de pré-visualização para o indexante GET, é adicionado um `cache` conjunto de propriedades a nula às definições.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Copie a definição do indexador a partir da resposta.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>Passo 2: Modificar a propriedade cache na definição do indexante

Por defeito, a `cache` propriedade é nula. Utilize um cliente API para definir a configuração da cache (o portal não suporta esta atualização de partículas). 

Modificar o objeto cache para incluir as seguintes propriedades necessárias e opcionais: 

+ O `storageConnectionString` é necessário e deve ser definido para uma cadeia de ligação de armazenamento Azure. 
+ A `enableReprocessing` propriedade booleana`true` é opcional (por padrão), e indica que o enriquecimento incremental está ativado. Quando necessário, pode defini-lo para `false` suspender o processamento incremental enquanto outras operações intensivas de `true` recursos, como a indexação de novos documentos, estão em andamento e depois voltar para mais tarde.

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

É necessário um reset do indexante na criação de um enriquecimento incremental para os indexadores existentes para garantir que todos os documentos estão num estado consistente. Pode utilizar o portal ou um cliente API e a API do [Reset Indexer REST](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) Para esta tarefa.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>Passo 4: Salvar a definição atualizada

[Atualize o indexante](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) com um pedido PUT, o corpo do pedido deve conter a definição atualizada do indexante que tem a propriedade cache. Se obtém um 400, verifique a definição do indexante para se certificar de que todos os requisitos são cumpridos (fonte de dados, skillset, índice).

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
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

Se emitir agora outro pedido get no indexador, a `ID` resposta do serviço incluirá uma propriedade no objeto cache. A cadeia alfanumérica é anexada ao nome do recipiente que contém todos os resultados em cache e estado intermédio de cada documento processado por este indexante. O ID será usado exclusivamente para nomear a cache no armazenamento Blob.

    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }

### <a name="step-5-run-the-indexer"></a>Passo 5: Executar o indexador

Para executar o indexante, pode utilizar o portal ou a API. No portal, a partir da lista de indexadores, selecione o indexador e clique em **Executar**. Uma vantagem para usar o portal é que você pode monitorizar o estado do indexador, notar a duração do trabalho, e quantos documentos são processados. As páginas do portal são refrescadas a cada poucos minutos.

Em alternativa, pode utilizar o REST para [executar o indexante:](https://docs.microsoft.com/rest/api/searchservice/run-indexer)

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Após a corrida do indexante, você pode encontrar a cache no armazenamento Azure Blob. O nome do recipiente encontra-se no seguinte formato:`ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> Uma reset e reexecução do indexante resulta numa reconstrução completa para que o conteúdo possa ser cache. Todos os enriquecimentos cognitivos serão reexecutados em todos os documentos.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>Passo 6: Modificar uma habilidade e confirmar o enriquecimento incremental

Para modificar uma habilidade, pode utilizar o portal ou a API. Por exemplo, se estiver a utilizar a tradução de texto, uma simples alteração inline de `en` ou `es` de outra língua é suficiente para testar a prova de conceito do enriquecimento incremental.

Volte a correr o indexador. Apenas as partes de uma árvore de documentoenriquecida são atualizadas. Se usou o [portal rapidamente](cognitive-search-quickstart-blob.md) como prova de conceito, modificando a habilidade de tradução de texto para 'es', notará que apenas 8 documentos são atualizados em vez dos 14 originais. Os ficheiros de imagem não afetados pelo processo de tradução são reutilizados a partir de cache.

## <a name="enable-caching-on-new-indexers"></a>Ativar o cache em novos indexers

Para configurar o enriquecimento incremental para um novo indexante, tudo o que tem de fazer é incluir a `cache` propriedade na carga útil da definição indexante ao chamar O [Indexer de Criação (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer). 


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

A cache é criada, utilizada e gerida pelo indexante, e o seu conteúdo não está representado num formato que seja legível pelo ser humano. A melhor maneira de determinar se a cache é usada é executando o indexador e comparando métricas antes e depois para tempo de execução e contagens de documentos. 

Por exemplo, assuma uma habilidade que comece com a análise de imagem e o reconhecimento ótico de caracteres (OCR) de documentos digitalizados, seguido de uma análise a jusante do texto resultante. Se modificar uma habilidade de texto a jusante, o indexante pode recuperar todo o conteúdo de imagem e OCR previamente processado a partir de cache, atualização e processamento apenas alterações relacionadas com texto indicadas pelas suas edições. Pode esperar ver menos documentos na contagem de documentos (por exemplo 8/8 em oposição a 14/14 na execução original), prazos de execução mais curtos e menos encargos na sua conta.

## <a name="working-with-the-cache"></a>Trabalhando com a cache

Uma vez que a cache está operacional, os indexadores verificam a cache sempre que o Indexer de [execução](https://docs.microsoft.com/rest/api/searchservice/run-indexer) é chamado, para ver quais as partes da saída existente podem ser usadas. 

A tabela que se segue resume a forma como várias APIs se relacionam com a cache:

| API           | Impacto da cache     |
|---------------|------------------|
| [Criar Indexer (2019-05-06-Pré-visualização)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) | Cria e executa um indexante na primeira utilização, incluindo a criação de uma cache se a definição do indexante o especificar. |
| [Indexador de execução](https://docs.microsoft.com/rest/api/searchservice/run-indexer) | Executa um oleoduto de enriquecimento a pedido. Esta API lê a partir da cache se ela existir, ou cria uma cache se adicionar escadote a uma definição de indexante atualizada. Quando executa um indexante que tem o cache ativado, o indexante omite passos se a saída em cache puder ser utilizada. Pode utilizar a versão API geralmente disponível ou pré-visualizada deste API.|
| [Indexante de reset](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)| Limpa o indexador de qualquer informação incremental de indexação. A próxima corrida do indexante (a pedido ou programação) é o reprocessamento completo do zero, incluindo a reexecução de todas as competências e a reconstrução da cache. É funcionalmente equivalente a apagar o indexador e recriá-lo. Pode utilizar a versão API geralmente disponível ou pré-visualizada deste API.|
| [Reset Skills (2019-05-06-Pré-visualização)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) | Especifica quais as habilidades a reexecutar na próxima execução do indexante, mesmo que não tenha modificado nenhuma habilidade. A cache é atualizada em conformidade. As saídas, como uma loja de conhecimentos ou um índice de pesquisa, são atualizadas utilizando dados reutilizáveis a partir da cache mais novos conteúdos por habilidade atualizada. |

Para obter mais informações sobre o controlo do que acontece com a cache, consulte a [gestão cache](cognitive-search-incremental-indexing-conceptual.md#cache-management).

## <a name="next-steps"></a>Passos seguintes

O enriquecimento incremental é aplicável em indexadores que contenham habilidades. Como próximo passo, visite a documentação de skillset para entender conceitos e composição. 

Além disso, uma vez que você ativar a cache, você vai querer saber sobre os parâmetros e APIs que fator em cache, incluindo como sobrepor ou forçar comportamentos particulares. Para mais informações, consulte os seguintes links.

+ [Conceitos de skillset e composição](cognitive-search-working-with-skillsets.md)
+ [Como criar uma habilidade](cognitive-search-defining-skillset.md)
+ [Introdução ao enriquecimento incremental e ao cache](cognitive-search-incremental-indexing-conceptual.md)
