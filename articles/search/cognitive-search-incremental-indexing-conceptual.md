---
title: Introdução à indexação incremental (versão prévia)
titleSuffix: Azure Cognitive Search
description: Configure seu pipeline de enriquecimento de ia para direcionar seus dados para a consistência eventual para lidar com quaisquer atualizações de habilidades, habilidades, indexadores ou fontes de dados. Este recurso está atualmente em visualização pública
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 94ce056185ff6a804521bf583ac4f6ffaa513fb0
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715429"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>O que é indexação incremental no Pesquisa Cognitiva do Azure?

> [!IMPORTANT] 
> A indexação incremental está atualmente em visualização pública. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para Portal ou SDK do .NET no momento.

A indexação incremental é um novo recurso do Azure Pesquisa Cognitiva que adiciona o cache e o estado ao conteúdo aprimorado em um determinado conhecimento, dando a você controle sobre o processamento e reprocessamento de etapas individuais em um pipeline de enriquecimento. Isso não apenas preserva seu investimento monetário em processamento, mas também torna um sistema mais eficiente. Quando estruturas e conteúdo são armazenados em cache, um indexador pode determinar quais habilidades foram alteradas e executar apenas aquelas que foram modificadas, bem como quaisquer habilidades dependentes de downstream. 

Com a indexação incremental, a versão atual do pipeline de enriquecimento faz a menor quantidade de trabalho para garantir a consistência de todos os documentos no índice. Para cenários em que você deseja controle total, você pode usar controles refinados para substituir os comportamentos esperados. Para obter mais informações sobre a configuração, consulte [Configurar a indexação incremental](search-howto-incremental-index.md).

## <a name="indexer-cache"></a>Cache do indexador

A indexação incremental adiciona um cache do indexador ao pipeline de enriquecimento. O indexador armazena em cache os resultados de quebra de documento e as saídas de cada uma das habilidades para cada documento. Quando um configurador de habilidades é atualizado, somente as habilidades alteradas ou downstream são executadas novamente. Os resultados atualizados são gravados no cache e o documento é atualizado no índice e na loja de conhecimento.

Fisicamente, o cache é uma conta de armazenamento. Todos os índices em um serviço de pesquisa podem compartilhar a mesma conta de armazenamento para o cache do indexador. Cada indexador recebe um identificador de cache exclusivo e imutável.

### <a name="cache-configuration"></a>Configuração de cache

Você precisará definir a propriedade `cache` no indexador para iniciar o beneficiando a partir da indexação incremental. O exemplo a seguir ilustra um indexador com Caching habilitado. Partes específicas dessa configuração são descritas nas seções a seguir.

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true,
        "id" : "Auto generated Id you do not need to set"
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters":{}
}
```

Definir essa propriedade pela primeira vez em um indexador existente exigirá que você também o redefina, o que fará com que todos os documentos na fonte de dados sejam processados novamente. O objetivo da indexação incremental é tornar os documentos em seu índice consistentes com sua fonte de dados e a versão atual do seu Skills. A redefinição do índice é a primeira etapa para essa consistência, pois ele elimina qualquer documento aprimorado pelas versões anteriores do Configurador de habilidades. O indexador precisa ser redefinido para iniciar com uma linha de base consistente.

### <a name="cache-lifecycle"></a>Ciclo de vida do cache

O ciclo de vida do cache é gerenciado pelo indexador. Se a propriedade `cache` no indexador for definida como NULL ou a cadeia de conexão for alterada, o cache existente será excluído. O ciclo de vida do cache também está vinculado ao ciclo de vida do indexador. Se um indexador for excluído, o cache associado também será excluído.

### <a name="indexer-cache-mode"></a>Modo de cache do indexador

O cache do indexador pode operar nos modos em que os dados são gravados apenas no cache ou os dados são gravados no cache e usados para reenriquecer os documentos.  Temporariamente, você pode suspender o enriquecimento incremental definindo a propriedade `enableReprocessing` no cache como `false`e, posteriormente, retomar a consistência incremental e impulsionar a coerência eventual definindo-a como `true`. Esse controle é particularmente útil quando você deseja priorizar a indexação de novos documentos em relação à garantia de consistência em seus corpus de documentos.

## <a name="change-detection-override"></a>Substituição de detecção de alteração

A indexação incremental oferece controle granular sobre todos os aspectos do pipeline de enriquecimento. Esse controle permite lidar com situações em que uma alteração pode ter consequências indesejadas. Por exemplo, editar um conjunto de qualificações e atualizar a URL de uma habilidade personalizada resultará no indexador invalidando os resultados em cache para essa habilidade. Se você estiver movendo apenas o ponto de extremidade para uma VM diferente ou reimplantando sua habilidade com uma nova chave de acesso, você realmente não deseja que nenhum documento existente seja processado novamente.

Para garantir que o indexador apenas faça os aprimoramentos que você precisa explicitamente, as atualizações para o conjunto de qualificações podem, opcionalmente, definir o parâmetro `disableCacheReprocessingChangeDetection` QueryString como `true`. Quando definido, esse parâmetro garantirá que apenas as atualizações do conjunto de qualificações sejam confirmadas e que a alteração não seja avaliada quanto a efeitos no Corpus existente.

O exemplo a seguir ilustra o uso de QueryString. Faz parte da solicitação, com pares chave-valor separado por &. 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>Invalidação de cache

O inverso desse cenário é aquele em que você pode implantar uma nova versão de uma habilidade personalizada, nada dentro do pipeline de enriquecimento é alterado, mas você precisa de uma habilidade específica invalidada e todos os documentos afetados são reprocessados para refletir os benefícios de um modelo atualizado. Nesses casos, você pode chamar a operação Invalidate Skills no skillset. A API redefinir habilidades aceita uma solicitação POST com a lista de saídas de habilidades no cache que devem ser invalidadas. Para obter mais informações sobre a API de redefinição de habilidades, consulte [Redefinir indexador (API REST de pesquisa)](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="bi-directional-change-detection"></a>Detecção bidirecional de alterações

Os indexadores não só avançam e processam novos documentos, mas agora são capazes de mover para trás e gerar documentos processados anteriormente para consistência. Com esse novo recurso, é importante entender como as alterações em seus componentes de pipeline de enriquecimento resultam no trabalho do indexador. O indexador colocará o trabalho em fila para ser feito quando identificar uma alteração que seja invalidada ou inconsistente em relação ao conteúdo armazenado em cache.

### <a name="invalidating-changes"></a>Invalidando alterações

A invalidação de alterações é rara, mas tem um efeito significativo no estado de seu pipeline de enriquecimento. Uma alteração de invalidação é aquela em que o cache inteiro não é mais válido. Um exemplo de uma alteração de invalidação é aquele em que a fonte de dados é atualizada. Para cenários em que você sabe que a alteração não deve invalidar o cache, como girar a chave na conta de armazenamento, o parâmetro `ignoreResetRequirement` QueryString deve ser definido como `true` na operação de atualização do recurso específico para garantir que a operação seja Não rejeitado.

Aqui está a lista completa de alterações que invalidarão seu cache:

* Alterar para o tipo de fonte de dados
* Alterar para o contêiner de fonte de dados
* Credenciais da fonte de dados
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

### <a name="inconsistent-changes"></a>Alterações inconsistentes

Um exemplo de alteração inconsistente é uma atualização para seu conjunto de qualificações que modifica uma habilidade. A modificação pode tornar uma parte do cache inconsistente. O indexador identificará o trabalho para tornar as coisas consistentes novamente.  

A lista completa de alterações resultando em inconsistência de cache:

* A habilidade no conferent tem um tipo diferente. O tipo de OData da habilidade é atualizado
* Parâmetros específicos da habilidade atualizados, por exemplo, a URL, padrões ou outros parâmetros
* As saídas de habilidades são alteradas, a habilidade retorna saídas adicionais ou diferentes
* As atualizações de habilidades resultantes são ancestrais diferentes, o encadeamento de habilidades mudou, ou seja, Entradas de habilidades
* Qualquer invalidação de habilidade upstream, se uma habilidade que fornece uma entrada para essa habilidade for atualizada
* As atualizações no local de projeção da loja de conhecimento resultam em reprojetar documentos
* Alterações nas projeções da loja de conhecimento resultam em reprojetar documentos
* Os mapeamentos de campo de saída alterados em um indexador resultam na Reprojeção de documentos para o índice

## <a name="rest-api-reference-for-incremental-indexing"></a>Referência da API REST para indexação incremental

REST `api-version=2019-05-06-Preview` fornece as APIs para indexação incremental, com adições a indexadores, habilidades e fontes de dados. A documentação de referência não inclui atualmente essas adições. A seção a seguir descreve as alterações de API.

### <a name="indexers"></a>Indexadores

[Criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer) e [Atualizar indexador](https://docs.microsoft.com/rest/api/searchservice/update-indexer) agora vão expor novas propriedades relacionadas ao cache:

* `StorageAccountConnectionString`: a cadeia de conexão para a conta de armazenamento que será usada para armazenar em cache os resultados intermediários.

* `CacheId`: o `cacheId` é o identificador do contêiner dentro da conta de armazenamento de `annotationCache` que será usada como o cache para esse indexador. Esse cache será exclusivo para esse indexador e, se o indexador for excluído e recriado com o mesmo nome, o `cacheId` será regenerado. O `cacheId` não pode ser definido, sempre é gerado pelo serviço.

* `EnableReprocessing`: definido como `true` por padrão, quando definido como `false`, os documentos continuarão a ser gravados no cache, mas nenhum documento existente será reprocessado com base nos dados do cache.

Alguns indexadores (por meio de [fontes de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)) recuperam dados por meio de consultas. Para consultas que recuperam dados, os indexadores também oferecerão suporte a um novo parâmetro de cadeia de caracteres de consulta: `ignoreResetRequirement` deve ser definido como `true` quando a ação de atualização não deve invalidar o cache.

### <a name="skillsets"></a>Conjuntos de competências

O habilidades não dará suporte a nenhuma nova operação, mas dará suporte a um novo parâmetro QueryString: `disableCacheReprocessingChangeDetection` deve ser definido como `true` quando você não quiser atualizações para documentos existentes com base na ação atual.

### <a name="datasources"></a>fontes

As fontes de armazenamento não oferecerão suporte a novas operações, mas dará suporte a um novo parâmetro QueryString: `ignoreResetRequirement` deve ser definido como `true` quando a ação de atualização não deve invalidar o cache.

## <a name="best-practices"></a>Melhores práticas

A abordagem recomendada para usar a indexação incremental é configurar a indexação incremental definindo a propriedade cache em um novo indexador ou redefinir um indexador existente e definir a propriedade cache.

Use o `ignoreResetRequirement` com moderação, pois isso pode levar à inconsistência indesejada em seus dados que não serão detectados com facilidade.

## <a name="takeaways"></a>Conclusões

A indexação incremental é um recurso poderoso que estende o controle de alterações da fonte de dados para todos os aspectos do pipeline de enriquecimento, incluindo a fonte de dados, a versão atual do seu conjunto de qualificações e o indexador. À medida que suas habilidades, habilidadess ou aprimoramentos evoluem, o pipeline de enriquecimento garante que o mínimo de trabalho possível seja feito e, ao mesmo tempo, conduza seus documentos para consistência eventual.

## <a name="next-steps"></a>Passos seguintes

Comece a usar a indexação incremental adicionando um cache a um indexador existente ou adicione o cache ao definir um novo indexador.

> [!div class="nextstepaction"]
> [Configurar a indexação incremental](search-howto-incremental-index.md)
