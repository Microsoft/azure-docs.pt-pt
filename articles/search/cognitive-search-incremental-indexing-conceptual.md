---
title: Enriquecimento incremental (pré-visualização)
titleSuffix: Azure Cognitive Search
description: Conteúdo intermédio cache e alterações incrementais do gasoduto de enriquecimento de IA no Armazenamento Azure para preservar investimentos em documentos processados existentes. Esta funcionalidade encontra-se atualmente em pré-visualização pública.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 09003c26ead9108d07ae339fcf64235c246474a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024148"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Introdução ao enriquecimento incremental e ao cache na Pesquisa Cognitiva Azure

> [!IMPORTANT] 
> O enriquecimento incremental encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure . A [versão REST API 2019-05-06-Preview](search-api-preview.md) fornece esta funcionalidade. Não existe nenhum suporte de Portal ou .NET SDK neste momento.

O enriquecimento incremental adiciona cache e agudeza a um oleoduto de enriquecimento, preservando o seu investimento na produção existente, alterando apenas os documentos impactados por modificações particulares. Isto não só preserva o seu investimento monetário no processamento (em particular, OCR e processamento de imagem), como também torna um sistema mais eficiente. Quando as estruturas e o conteúdo são cache, um indexante pode determinar quais as competências que mudaram e executar apenas as que foram modificadas, bem como quaisquer habilidades dependentes a jusante. 

## <a name="indexer-cache"></a>Cache indexante

O enriquecimento incremental adiciona uma cache ao gasoduto de enriquecimento. O indexante caches os resultados de quebra de documentos mais as saídas de cada habilidade para cada documento. Quando uma habilidade é atualizada, apenas as habilidades alteradas, ou a jusante, são reexecutadas. Os resultados atualizados são escritos para a cache e o documento é atualizado no índice de pesquisa ou na loja de conhecimento.

Fisicamente, a cache é armazenada num recipiente de bolha na sua conta de Armazenamento Azure. A cache também utiliza o armazenamento de mesa para um registo interno de atualizações de processamento. Todos os índices dentro de um serviço de pesquisa podem partilhar a mesma conta de armazenamento para o cache indexante. A cada indexante é atribuído um identificador de cache único e imutável ao recipiente que está a utilizar.

## <a name="cache-configuration"></a>Configuração de cache

Você precisará definir a `cache` propriedade no indexante para começar a beneficiar do enriquecimento incremental. O exemplo seguinte ilustra um indexante com cache ativado. Partes específicas desta configuração são descritas nas seguintes secções. Para mais informações, consulte [Configurar o enriquecimento incremental](search-howto-incremental-index.md).

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

A definição desta propriedade num indexante existente exigirá que reponha e refaça o indexante, o que resultará em que todos os documentos da sua fonte de dados sejam novamente tratados. Este passo é necessário para eliminar quaisquer documentos enriquecidos por versões anteriores do skillset. 

## <a name="cache-management"></a>Gestão de cache

O ciclo de vida da cache é gerido pelo indexante. Se `cache` a propriedade do indexante estiver definida como nula ou a cadeia de ligação for alterada, a cache existente é eliminada na próxima execução do indexante. O ciclo de vida da cache também está ligado ao ciclo de vida do indexador. Se um indexante for eliminado, a cache associada também é eliminada.

Enquanto o enriquecimento incremental é projetado para detetar e responder a alterações sem nenhuma intervenção da sua parte, existem parâmetros que pode usar para sobrepor comportamentos predefinidos:

+ Priorizar novos documentos
+ Controlos de habilidades de bypass
+ Bypass verificações de fonte de dados
+ Avaliação de skillset de força

### <a name="prioritize-new-documents"></a>Priorizar novos documentos

Detete a propriedade para controlar o `enableReprocessing` processamento sobre documentos de entrada já representados na cache. Quando `true` (padrão), os documentos já na cache são reprocessados quando reexecuta o indexador, assumindo que a sua atualização de habilidades afeta esse doc. 

Quando `false`os documentos existentes não forem reprocessados, priorizando efetivamente novos conteúdos de entrada sobre os conteúdos existentes. Só deve `enableReprocessing` começar `false` temporariamente. Para garantir a consistência `enableReprocessing` em `true` todo o corpus, deve ser a maior parte do tempo, garantindo que todos os documentos, novos e existentes, sejam válidos de acordo com a definição atual de competências.

### <a name="bypass-skillset-evaluation"></a>Avaliação de habilidades de bypass

Modificar uma habilidade e reprocessamento dessa habilidade normalmente andam de mãos dadas. No entanto, algumas alterações a um skillset não devem resultar em reprocessamento (por exemplo, implementando uma habilidade personalizada para um novo local ou com uma nova chave de acesso). Muito provavelmente, trata-se de modificações periféricas que não têm um impacto genuíno na substância do próprio skillset. 

Se souber que uma alteração no skillset é de facto superficial, deve `disableCacheReprocessingChangeDetection` substituir a `true`avaliação do skillset definindo o parâmetro para:

1. Ligue para o Skillset de atualização e modifique a definição de skillset.
1. Anexar `disableCacheReprocessingChangeDetection=true` o parâmetro no pedido.
1. Submeta a alteração.

A definição deste parâmetro garante que apenas as atualizações à definição de skillset são comprometidas e a alteração não é avaliada para efeitos no corpus existente.

O exemplo seguinte mostra um pedido de atualização skillset com o parâmetro:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Controlos de validação de fonte de dados de bypass

A maioria das alterações a uma definição de fonte de dados invalidará a cache. No entanto, para cenários em que sabe que uma alteração não deve invalidar a cache -`ignoreResetRequirement` como alterar uma cadeia de ligação ou rodar a chave na conta de armazenamento - anexar o parâmetro na atualização da fonte de dados. Definir este parâmetro `true` para permitir que o compromisso passe, sem desencadear uma condição de reset que resultaria na reconstrução e povoamento de todos os objetos do zero.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Avaliação de skillset de força

O objetivo da cache é evitar o processamento desnecessário, mas suponha que você faça uma mudança para uma habilidade que o indexante não deteta (por exemplo, mudar algo em código externo, como uma habilidade personalizada).

Neste caso, você pode usar as Habilidades de [Reset](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) para forçar o reprocessamento de uma determinada habilidade, incluindo quaisquer habilidades a jusante que tenham uma dependência da saída dessa habilidade. Esta API aceita um pedido de POST com uma lista de competências que devem ser invalidadas e marcadas para reprocessamento. Depois de redefinir as habilidades, execute o indexante para invocar o oleoduto.

## <a name="change-detection"></a>Deteção de alterações

Uma vez ativado um cache, o indexante avalia as alterações na composição do seu pipeline para determinar que conteúdo pode ser reutilizado e que necessita de ser reprocessado. Esta secção enumera alterações que invalidam a cache, seguidas de alterações que desencadeiam o processamento incremental. 

### <a name="changes-that-invalidate-the-cache"></a>Alterações que invalidam a cache

Uma mudança invalidante é aquela em que toda a cache já não é válida. Um exemplo de uma mudança invalidante é aquele em que a sua fonte de dados é atualizada. Aqui está a lista completa de alterações que invalidariam a sua cache:

* Alterar para o seu tipo de fonte de dados
* Alteração para recipiente de origem de dados
* Credenciais da origem de dados
* Política de deteção de alterações de fonte de dados
* Fontes de dados eliminam política de deteção
* Mapeamentode campo indexador
* Parâmetros indexantes
    * Modo de análise
    * Extensões de nome de ficheiro excluídas
    * Extensões de nome de ficheiro indexadas
    * Metadados de armazenamento de índices apenas para documentos de grandes dimensões
    * Cabeçalhos de texto delimitados
    * Delimitador de texto limitado
    * Raiz de documento
    * Ação de Imagem (Alterações na forma como as imagens são extraídas)

### <a name="changes-that-trigger-incremental-processing"></a>Alterações que desencadeiam processamento incremental

O processamento incremental avalia a sua definição de skillset e determina quais as habilidades a reexecutar, atualizando seletivamente as partes afetadas da árvore documental. Aqui está a lista completa de alterações que resultam em enriquecimento incremental:

* A habilidade no skillset tem um tipo diferente. O tipo de odata da habilidade é atualizado
* Parâmetros específicos de habilidades atualizados, por exemplo, o url, os predefinidos ou outros parâmetros
* As saídas de habilidade saem, a habilidade devolve saídas adicionais ou diferentes
* As atualizações de habilidades resultantes são a ancestralidade diferente, a corrente de habilidade mudou, ou seja, inputs habilidade
* Qualquer invalidação de habilidadea montante, se uma habilidade que fornece uma entrada para esta habilidade é atualizada
* Atualizações para a localização da projeção da loja de conhecimento, resulta na reprojeção de documentos
* Alterações nas projeções da loja de conhecimento, resultam na reprojeção de documentos
* Mapeamentos de campo de saída alterados num indexante resulta na reprojeção de documentos para o índice

## <a name="api-reference"></a>Referência da API

A versão `2019-05-06-Preview` REST API fornece enriquecimento incremental através de propriedades adicionais em indexadores, habilidades e fontes de dados. Além da documentação de referência, consulte [o caching de Configuração para obter](search-howto-incremental-index.md) um enriquecimento incremental para obter detalhes sobre como chamar as APIs.

+ [Criar Indexer (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [Indexante de atualização (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [Atualização Skillset (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) (Novo parâmetro URI no pedido)

+ [Repor Competências (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ Índices de base de dados (Azure SQL, Cosmos DB). Alguns indexadores recuperam dados através de consultas. Para consultas que recuperem dados, [a Atualização Data Source](https://docs.microsoft.com/rest/api/searchservice/update-data-source) suporta um novo parâmetro num pedido ignore **ResetRequirement**, que deve ser definido para `true` quando a sua ação de atualização não deve invalidar a cache. 

  Utilize **ignore ResetRequirement** com moderação, pois pode levar a inconsistências não intencionais nos seus dados que não serão detetados facilmente.

## <a name="next-steps"></a>Passos seguintes

O enriquecimento incremental é uma característica poderosa que alarga o rastreio de mudança a habilidades e enriquecimento de IA. O enriquecimento AIncremental permite a reutilização do conteúdo processado existente à medida que itera sobre o design de skillset.

Como próximo passo, permita o cache num indexante existente ou adicione uma cache ao definir um novo indexante.

> [!div class="nextstepaction"]
> [Configurar o caching para enriquecimento incremental](search-howto-incremental-index.md)
