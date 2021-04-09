---
title: Conceitos incrementais de enriquecimento (pré-visualização)
titleSuffix: Azure Cognitive Search
description: Cache conteúdo intermédio e alterações incrementais do gasoduto de enriquecimento de IA no Azure Storage para preservar os investimentos em documentos processados existentes. Esta funcionalidade encontra-se atualmente em visualização pública.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 2448609b1184c8e91947bffbd13cfea8e3fe5d52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100390866"
---
# <a name="incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Enriquecimento incremental e caching em Pesquisa Cognitiva Azure

> [!IMPORTANT] 
> O enriquecimento incremental está atualmente em visualização pública. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> [As versões de pré-visualização da API](search-api-preview.md) REST fornecem esta funcionalidade. Não existe porta ou suporte .NET SDK neste momento.

*O enriquecimento incremental* é uma característica que visa [as habilidades.](cognitive-search-working-with-skillsets.md) Aproveita o Azure Storage para salvar a produção de processamento emitida por um gasoduto de enriquecimento para reutilização em futuras operações de indexação. Sempre que possível, o indexante reutiliza qualquer saída em cache que ainda seja válida. 

Não só o enriquecimento incremental preserva o seu investimento monetário no processamento (em particular, OCR e processamento de imagem), como também torna um sistema mais eficiente. 

Um fluxo de trabalho que utiliza o caching incremental inclui os seguintes passos:

1. [Crie ou identifique uma conta de armazenamento Azure](../storage/common/storage-account-create.md) para armazenar a cache.
1. [Ativar o enriquecimento incremental](search-howto-incremental-index.md) no indexante.
1. [Crie um indexante](/rest/api/searchservice/create-indexer) - mais um [skillset](/rest/api/searchservice/create-skillset) - para invocar o oleoduto. Durante o processamento, são guardadas fases de enriquecimento para cada documento no armazenamento Blob para utilização futura.
1. Teste o seu código e, depois de escoar alterações, use [Atualização Skillset](/rest/api/searchservice/update-skillset) para modificar uma definição.
1. [Executar Indexer](/rest/api/searchservice/run-indexer) para invocar o gasoduto, recuperando a saída em cache para um processamento mais rápido e mais rentável.

Para obter mais informações sobre etapas e considerações ao trabalhar com um indexante existente, consulte [Configurar o enriquecimento incremental](search-howto-incremental-index.md).

## <a name="indexer-cache"></a>Cache indexante

O enriquecimento incremental adiciona uma cache ao gasoduto de enriquecimento. O indexante cache os resultados da quebra de documentos mais as saídas de cada habilidade para cada documento. Quando um skillset é atualizado, apenas as habilidades alteradas, ou a jusante, são reexecutadas. Os resultados atualizados são escritos na cache e o documento é atualizado no índice de pesquisa ou na loja de conhecimento.

Fisicamente, a cache é armazenada num recipiente de bolhas na sua conta de Armazenamento Azure. A cache também utiliza armazenamento de mesa para um registo interno de atualizações de processamento. Todos os índices dentro de um serviço de pesquisa podem partilhar a mesma conta de armazenamento para a cache indexante. A cada indexante é atribuído um identificador de cache único e imutável ao recipiente que está a utilizar.

## <a name="cache-configuration"></a>Configuração cache

Você precisará definir a `cache` propriedade no indexante para começar a beneficiar do enriquecimento incremental. O exemplo a seguir ilustra um indexante com caching ativado. Partes específicas desta configuração são descritas nas seguintes secções. Para mais informações, consulte [configurar o enriquecimento incremental.](search-howto-incremental-index.md)

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

A definição desta propriedade num indexante existente exigirá que reinicie e volte a ser o indexante, o que resultará no tratamento de novamente de todos os documentos da sua fonte de dados. Este passo é necessário para eliminar quaisquer documentos enriquecidos por versões anteriores do skillset. 

## <a name="cache-management"></a>Gestão de cache

O ciclo de vida da cache é gerido pelo indexante. Se a `cache` propriedade no indexante estiver definida para nula ou a cadeia de ligação for alterada, a cache existente é eliminada na sequência do indexante seguinte. O ciclo de vida da cache também está ligado ao ciclo de vida indexante. Se um indexante for eliminado, a cache associada também é eliminada.

Enquanto o enriquecimento incremental é projetado para detetar e responder a alterações sem intervenção da sua parte, existem parâmetros que pode usar para anular comportamentos predefinidos:

+ Priorizar novos documentos
+ Verificações de skillset de bypass
+ Verificações de fontes de dados de bypass
+ Avaliação de skillset de força

### <a name="prioritize-new-documents"></a>Priorizar novos documentos

Desaprova a propriedade para controlar o `enableReprocessing` processamento sobre os documentos de entrada já representados na cache. Quando `true` (predefinido), os documentos já na cache são reprocessados quando reexecutam o indexante, assumindo que a sua atualização de habilidades afeta esse doc. 

Quando, `false` os documentos existentes não são reprocessados, priorizando efetivamente novos conteúdos de entrada sobre o conteúdo existente. Só deve trabalhar `enableReprocessing` `false` temporariamente. Para garantir a consistência em todo o corpus, `enableReprocessing` deve ser a maior parte do `true` tempo, garantindo que todos os documentos, tanto novos como existentes, são válidos de acordo com a definição de skillset atual.

### <a name="bypass-skillset-evaluation"></a>Avaliação de skillset de bypass

Modificar um skillset e reprocessamento dessa habilidade normalmente anda de mãos dadas. No entanto, algumas alterações a um skillset não devem resultar no reprocessamento (por exemplo, implantar uma habilidade personalizada para um novo local ou com uma nova chave de acesso). Muito provavelmente, trata-se de modificações periféricas que não têm um impacto genuíno na substância do próprio skillset. 

Se sabe que uma mudança no skillset é realmente superficial, deve anular a avaliação de skillset definindo o `disableCacheReprocessingChangeDetection` parâmetro `true` para:

1. Ligue para a atualização de Skillset e modifique a definição de skillset.
1. Anexar o `disableCacheReprocessingChangeDetection=true` parâmetro do pedido.
1. Submeta a mudança.

A definição deste parâmetro garante que apenas são comprometidas atualizações à definição de skillset e a alteração não é avaliada para efeitos no corpus existente.

O exemplo a seguir mostra um pedido de Atualização skillset com o parâmetro:

```http
PUT https://[search service].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Verificações de validação de fontes de dados de bypass

A maioria das alterações a uma definição de fonte de dados invalidará a cache. No entanto, para cenários em que se sabe que uma alteração não deve invalidar a cache - como alterar uma cadeia de ligação ou rodar a chave na conta de armazenamento - anexar o `ignoreResetRequirement` parâmetro na atualização da fonte de dados. Definir este parâmetro `true` permite que o compromisso passe, sem desencadear uma condição de reset que resultaria na reconstrução de todos os objetos e povoados do zero.

```http
PUT https://[search service].search.windows.net/datasources/[data source name]?api-version=2020-06-30-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Avaliação de skillset de força

O objetivo da cache é evitar o processamento desnecessário, mas suponha que faça uma alteração para uma habilidade que o indexante não deteta (por exemplo, alterando algo em código externo, como uma habilidade personalizada).

Neste caso, pode utilizar as [Capacidades de Reset](/rest/api/searchservice/preview-api/reset-skills) para forçar o reprocessamento de uma determinada habilidade, incluindo quaisquer habilidades a jusante que tenham uma dependência da produção dessa habilidade. Esta API aceita um pedido de POST com uma lista de competências que devem ser invalidadas e marcadas para reprocessamento. Depois de redefinir competências, execute o indexante para invocar o pipeline.

### <a name="reset-documents"></a>Redefinir documentos

[O reset de um indexante](/rest/api/searchservice/reset-indexer) resultará no reprocessamento de todos os documentos do corpus de pesquisa. Em cenários em que apenas alguns documentos precisam de ser reprocessados, e a fonte de dados não pode ser atualizada, utilize [documentos de reset (pré-visualização)](/rest/api/searchservice/preview-api/reset-documents) para forçar o reprocessamento de documentos específicos. Quando um documento é reiniciado, o indexante invalida a cache desse documento e o documento é reprocessado lendo-o a partir da fonte de dados. Para obter mais informações, consulte [indexadores, competências e documentos de execução ou reposição.](search-howto-run-reset-indexers.md)

## <a name="change-detection"></a>Deteção da alteração

Uma vez ativado um cache, o indexante avalia as alterações na sua composição do pipeline para determinar que conteúdo pode ser reutilizado e que necessita de ser reprocessado. Esta secção enumera alterações que invalidam a cache imediatamente, seguidas de alterações que desencadeiam o processamento incremental. 

### <a name="changes-that-invalidate-the-cache"></a>Alterações que invalidam a cache

Uma alteração invalidante é aquela em que toda a cache já não é válida. Um exemplo de uma alteração invalidante é aquele em que a sua fonte de dados é atualizada. Aqui está a lista completa de alterações que invalidariam o seu cache:

* Altere para o seu tipo de fonte de dados
* Alteração para recipiente de origem de dados
* Credenciais da origem de dados
* Política de deteção de alterações de origem de dados
* Fontes de dados eliminam a política de deteção
* Mapeamentos de campo indexante
* Parâmetros indexantes
    * Modo de análise
    * Extensões de nome de ficheiro excluídas
    * Extensões indexadas de nome de ficheiro
    * Metadados de armazenamento de índice apenas para documentos de grandes dimensões
    * Cabeçalhos de texto delimitados
    * Limagem de textolimitado
    * Raiz de documento
    * Ação de Imagem (Alterações à forma como as imagens são extraídas)

### <a name="changes-that-trigger-incremental-processing"></a>Alterações que desencadeiam o processamento incremental

O processamento incremental avalia a definição de skillset e determina quais as habilidades a repetir, atualizando seletivamente as porções afetadas da árvore documental. Aqui está a lista completa de alterações que resultam em enriquecimento incremental:

* A habilidade no skillset tem um tipo diferente. O tipo de dados da habilidade é atualizado
* Parâmetros específicos de habilidades atualizados, por exemplo, o url, predefinições ou outros parâmetros
* As saídas de competências mudam, a habilidade devolve saídas adicionais ou diferentes
* As atualizações de habilidades resultantes são ascendência diferente, a corrente de habilidades mudou ou seja entradas de habilidades
* Qualquer invalidez de habilidade a montante, se uma habilidade que forneça uma entrada para esta habilidade é atualizada
* Atualizações para a localização da projeção da loja de conhecimento, resulta em documentos de reprojecção
* Alterações nas projeções da loja de conhecimento, resulta em documentos de reprojecção
* Mapeamentos de campo de saída alterados num indexante resultam na reprojecção de documentos para o índice

## <a name="api-reference"></a>Referência da API

A versão REST API `2020-06-30-Preview` proporciona enriquecimento incremental através de propriedades adicionais em indexadores. Skillsets e fontes de dados podem usar a versão geralmente disponível. Para além da documentação de referência, consulte o  [caching Configure para enriquecimento incremental](search-howto-incremental-index.md) para mais detalhes sobre como chamar as APIs.

+ [Criar Indexer (versão api=2020-06-30-Pré-visualização)](/rest/api/searchservice/create-indexer) 

+ [Índice de Atualização (versão api=2020-06-30-Pré-visualização)](/rest/api/searchservice/update-indexer) 

+ [Atualização Skillset (versão api=2020-06-30)](/rest/api/searchservice/update-skillset) (Novo parâmetro URI no pedido)

+ [Competências de Reset (versão api=2020-06-30)](/rest/api/searchservice/preview-api/reset-skills)

+ Indexantes de base de dados (Azure SQL, Cosmos DB). Alguns indexantes recuperam dados através de consultas. Para consultas que recuperem dados, [a Update Data Source](/rest/api/searchservice/update-data-source) suporta um novo parâmetro sobre um pedido **ignoreResetRequirement**, que deve ser definido para quando a `true` sua ação de atualização não deve invalidar a cache. 

  Use **ignoreResetRequirement** com moderação, pois pode levar a uma inconsistência não intencional nos seus dados que não será facilmente detetada.

## <a name="next-steps"></a>Passos seguintes

O enriquecimento incremental é uma característica poderosa que alarga o rastreio de mudanças às habilidades e ao enriquecimento de IA. O enriquecimento incremental permite a reutilização de conteúdos processados existentes à medida que itera sobre o design skillset.

Como passo seguinte, ative o cache num indexante existente ou adicione uma cache ao definir um novo indexante.

> [!div class="nextstepaction"]
> [Configure caching para enriquecimento incremental](search-howto-incremental-index.md)