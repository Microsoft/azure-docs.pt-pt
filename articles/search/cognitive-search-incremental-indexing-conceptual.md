---
title: Enriquecimento incremental (visualização)
titleSuffix: Azure Cognitive Search
description: Conteúdo intermediário de cache e alterações incrementais do pipeline de enriquecimento de AI no armazenamento do Azure para preservar investimentos em documentos processados existentes. Este recurso está atualmente em visualização pública.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 09003c26ead9108d07ae339fcf64235c246474a4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024148"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Introdução ao Caching e enriquecimento incremental no Azure Pesquisa Cognitiva

> [!IMPORTANT] 
> O enriquecimento incremental está atualmente em visualização pública. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para Portal ou SDK do .NET no momento.

A sofisticação incremental adiciona cache e monitoração de estado a um pipeline de enriquecimento, preservando seu investimento na saída existente, ao mesmo tempo em que altera apenas os documentos afetados por determinada modificação. Isso não apenas preserva seu investimento monetário em processamento (em particular, processamento de OCR e imagens), mas também torna um sistema mais eficiente. Quando estruturas e conteúdo são armazenados em cache, um indexador pode determinar quais habilidades foram alteradas e executar apenas aquelas que foram modificadas, bem como quaisquer habilidades dependentes de downstream. 

## <a name="indexer-cache"></a>Cache do indexador

O enriquecimento incremental adiciona um cache ao pipeline de enriquecimento. O indexador armazena em cache os resultados de quebra de documento mais as saídas de cada habilidade para cada documento. Quando um configurador de habilidades é atualizado, somente as habilidades alteradas ou downstream são executadas novamente. Os resultados atualizados são gravados no cache e o documento é atualizado no índice de pesquisa ou na loja de conhecimento.

Fisicamente, o cache é armazenado em um contêiner de BLOB em sua conta de armazenamento do Azure. A cache também utiliza o armazenamento de mesa para um registo interno de atualizações de processamento. Todos os índices em um serviço de pesquisa podem compartilhar a mesma conta de armazenamento para o cache do indexador. Cada indexador recebe um identificador de cache exclusivo e imutável para o contêiner que está usando.

## <a name="cache-configuration"></a>Configuração de cache

Você precisará definir a propriedade `cache` no indexador para iniciar o beneficiando do enriquecimento incremental. O exemplo a seguir ilustra um indexador com Caching habilitado. Partes específicas dessa configuração são descritas nas seções a seguir. Para obter mais informações, consulte [Configurar o enriquecimento incremental](search-howto-incremental-index.md).

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

Definir essa propriedade em um indexador existente exigirá que você redefina e execute novamente o indexador, o que fará com que todos os documentos na fonte de dados sejam processados novamente. Essa etapa é necessária para eliminar todos os documentos aprimorados pelas versões anteriores do Configurador de habilidades. 

## <a name="cache-management"></a>Gerenciamento de cache

O ciclo de vida do cache é gerenciado pelo indexador. Se a propriedade `cache` no indexador for definida como nula ou a cadeia de conexão for alterada, o cache existente será excluído na próxima execução do indexador. O ciclo de vida do cache também está vinculado ao ciclo de vida do indexador. Se um indexador for excluído, o cache associado também será excluído.

Embora o aperfeiçoamento incremental seja projetado para detectar e responder a alterações sem intervenção de sua parte, há parâmetros que podem ser usados para substituir os comportamentos padrão:

+ Priorizar novos documentos
+ Ignorar verificações de conferentes
+ Ignorar verificações da fonte de dados
+ Forçar avaliação do conforçador de habilidades

### <a name="prioritize-new-documents"></a>Priorizar novos documentos

Defina a propriedade `enableReprocessing` para controlar o processamento de documentos de entrada já representados no cache. Quando `true` (padrão), os documentos que já estão no cache são reprocessados quando você executa novamente o indexador, supondo que a atualização de suas habilidades afete esse documento. 

Quando `false`, os documentos existentes não são reprocessados, priorizando efetivamente o conteúdo novo e recebido sobre o conteúdo existente. Você só deve definir `enableReprocessing` para `false` em uma base temporária. Para garantir a consistência em todo o corpus, `enableReprocessing` deve ser `true` na maior parte do tempo, garantindo que todos os documentos, novos e existentes, sejam válidos de acordo com a definição atual do Configurador de qualificações.

### <a name="bypass-skillset-evaluation"></a>Ignorar avaliação do contorno de habilidades

A modificação de um conjunto de qualificações e o reprocessamento desse conjunto de qualificações normalmente se encontram em mãos. No entanto, algumas alterações em um conjunto de qualificações não devem resultar em reprocessamento (por exemplo, implantação de uma habilidade personalizada em um novo local ou com uma nova chave de acesso). Provavelmente, essas são modificações periféricas que não têm nenhum impacto original sobre a substância do próprio próprio configurador. 

Se você sabe que uma alteração no conjunto de qualificações é realmente superficial, você deve substituir a avaliação do conconjunto de qualificações definindo o parâmetro `disableCacheReprocessingChangeDetection` como `true`:

1. Chame Update Skillble e modifique a definição do conskillr.
1. Acrescente o parâmetro `disableCacheReprocessingChangeDetection=true` na solicitação.
1. Envie a alteração.

A definição desse parâmetro garante que apenas as atualizações da definição do conjunto de qualificações sejam confirmadas e a alteração não seja avaliada quanto a efeitos no Corpus existente.

O exemplo a seguir mostra uma solicitação Updateset de atualização com o parâmetro:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Ignorar verificações de validação da fonte de dados

A maioria das alterações em uma definição de fonte de dados invalidará o cache. No entanto, para cenários em que você sabe que uma alteração não deve invalidar o cache, como alterar uma cadeia de conexão ou girar a chave na conta de armazenamento, acrescente o parâmetro`ignoreResetRequirement` na atualização da fonte de dados. Definir esse parâmetro como `true` permite que a confirmação Continue, sem disparar uma condição de redefinição que resultaria em todos os objetos recriados e populados a partir do zero.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Forçar avaliação do conforçador de habilidades

A finalidade do cache é evitar o processamento desnecessário, mas suponha que você faça uma alteração em uma habilidade que o indexador não detecta (por exemplo, alterando algo no código externo, como uma habilidade personalizada).

Nesse caso, você pode usar as [habilidades de redefinição](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) para forçar o reprocessamento de uma determinada habilidade, incluindo quaisquer habilidades de downstream que tenham uma dependência na saída dessa habilidade. Essa API aceita uma solicitação POST com uma lista de habilidades que devem ser invalidadas e marcadas para reprocessamento. Após a redefinição das habilidades, execute o indexador para invocar o pipeline.

## <a name="change-detection"></a>Detecção de alteração

Depois de habilitar um cache, o indexador avalia as alterações na composição do pipeline para determinar qual conteúdo pode ser reutilizado e que exige reprocessamento. Esta seção enumera as alterações que invalidam o cache de forma totalmente seguida por alterações que disparam o processamento incremental. 

### <a name="changes-that-invalidate-the-cache"></a>Alterações que invalidam o cache

Uma alteração de invalidação é aquela em que o cache inteiro não é mais válido. Um exemplo de uma alteração de invalidação é aquele em que a fonte de dados é atualizada. Aqui está a lista completa de alterações que invalidarão seu cache:

* Alterar para o tipo de fonte de dados
* Alterar para o contêiner de fonte de dados
* Credenciais de fonte de dados
* Política de detecção de alteração da fonte de dados
* Política de detecção de exclusão de fontes de dados
* Mapeamentos de campo do indexador
* Parâmetros do indexador
    * Modo de análise
    * Extensões de nome de arquivo excluídas
    * Extensões de nome de arquivo indexado
    * Indexar metadados de armazenamento somente para documentos superdimensionados
    * Cabeçalhos de texto delimitados
    * Delimitador de texto delimitado
    * Raiz do documento
    * Ação de imagem (alterações de como as imagens são extraídas)

### <a name="changes-that-trigger-incremental-processing"></a>Alterações que disparam o processamento incremental

O processamento incremental avalia sua definição de qualificações e determina quais habilidades executar novamente, atualizando seletivamente as partes afetadas da árvore de documentos. Aqui está a lista completa de alterações que resultam em enriquecimento incremental:

* A habilidade no conferent tem um tipo diferente. O tipo de OData da habilidade é atualizado
* Parâmetros específicos da habilidade atualizados, por exemplo, a URL, padrões ou outros parâmetros
* As saídas de habilidades são alteradas, a habilidade retorna saídas adicionais ou diferentes
* As atualizações de habilidades resultantes são ancestrais diferentes, o encadeamento de habilidades mudou, ou seja, Entradas de habilidades
* Qualquer invalidação de habilidade upstream, se uma habilidade que fornece uma entrada para essa habilidade for atualizada
* As atualizações no local de projeção da loja de conhecimento resultam em reprojetar documentos
* Alterações nas projeções da loja de conhecimento resultam em reprojetar documentos
* Os mapeamentos de campo de saída alterados em um indexador resultam na Reprojeção de documentos para o índice

## <a name="api-reference"></a>Referência de API

A versão REST API `2019-05-06-Preview` fornece enriquecimento incremental através de propriedades adicionais em indexadores, habilidades e fontes de dados. Além da documentação de referência, consulte [o caching de Configuração para obter](search-howto-incremental-index.md) um enriquecimento incremental para obter detalhes sobre como chamar as APIs.

+ [Criar Indexer (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [Indexante de atualização (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [Atualização Skillset (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) (Novo parâmetro URI no pedido)

+ [Reset Skills (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ Índices de base de dados (Azure SQL, Cosmos DB). Alguns indexadores recuperam dados por meio de consultas. Para consultas que recuperem dados, [a Atualização Data Source](https://docs.microsoft.com/rest/api/searchservice/update-data-source) suporta um novo parâmetro num pedido ignore **ResetRequirement**, que deve ser definido para `true` quando a sua ação de atualização não deve invalidar a cache. 

  Utilize **ignore ResetRequirement** com moderação, pois pode levar a inconsistências não intencionais nos seus dados que não serão detetados facilmente.

## <a name="next-steps"></a>Passos seguintes

O aprimoramento incremental é um recurso poderoso que estende o controle de alterações para o habilidades e o enriquecimento de ia. O enriquecimento AIncremental permite a reutilização do conteúdo processado existente à medida que itera sobre o design de skillset.

Como próximo passo, permita o cache num indexante existente ou adicione uma cache ao definir um novo indexante.

> [!div class="nextstepaction"]
> [Configurar o Caching para enriquecimento incremental](search-howto-incremental-index.md)
