---
title: Adicionar indexação incremental (versão prévia)
titleSuffix: Azure Cognitive Search
description: Habilite o controle de alterações e preserve o estado do conteúdo aprimorado para processamento controlado em um conjunto de qualificações cognitiva. Este recurso está atualmente em visualização pública.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 348bc2d92f636d1f3c3b50ea31334355da59a60f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790495"
---
# <a name="how-to-set-up-incremental-indexing-of-enriched-documents-in-azure-cognitive-search"></a>Como configurar a indexação incremental de documentos aprimorados no Azure Pesquisa Cognitiva

> [!IMPORTANT] 
> A indexação incremental está atualmente em visualização pública. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para Portal ou SDK do .NET no momento.

Este artigo mostra como adicionar o estado e o cache a documentos aprimorados que se movem por meio de um pipeline de enriquecimento de Pesquisa Cognitiva do Azure para que você possa indexar incrementalmente documentos de qualquer uma das fontes de dados com suporte. Por padrão, um qualificable é sem monitoração de estado e a alteração de qualquer parte de sua composição requer uma execução completa do indexador. Com a indexação incremental, o indexador pode determinar quais partes do pipeline foram alteradas, reutilizar os aprimoramentos existentes para partes inalteradas e revisar os aprimoramentos das etapas que são alteradas. O conteúdo armazenado em cache é colocado no armazenamento do Azure.

Se você não estiver familiarizado com a configuração de indexadores, comece com a [visão geral do indexador](search-indexer-overview.md) e continue em [habilidades](cognitive-search-working-with-skillsets.md) para saber mais sobre pipelines de enriquecimento. Para obter mais informações sobre os principais conceitos, consulte [indexação incremental](cognitive-search-incremental-indexing-conceptual.md).

## <a name="modify-an-existing-indexer"></a>Modificar um indexador existente

Se você tiver um indexador existente, siga estas etapas para habilitar a indexação incremental.

### <a name="step-1-get-the-indexer"></a>Etapa 1: obter o indexador

Comece com um indexador válido existente que tenha a fonte de dados e o índice necessários já definidos. O indexador deve ser executável. Usando um cliente de API, construa uma [solicitação get](https://docs.microsoft.com/rest/api/searchservice/get-indexer) para obter a configuração atual do indexador, ao qual você deseja adicionar a indexação incremental.

```http
GET https://[service name].search.windows.net/indexers/[your indexer name]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-2-add-the-cache-property"></a>Etapa 2: adicionar a propriedade de cache

< < < < < < < cabeçalho edite a resposta da solicitação GET para adicionar a propriedade `cache` ao indexador. O objeto de cache requer apenas uma única propriedade, `storageConnectionString` que é a cadeia de conexão para a conta de armazenamento. = = = = = = = Editar a resposta da solicitação GET para adicionar a propriedade `cache` ao indexador. O objeto de cache requer apenas uma única propriedade, que é a cadeia de conexão para uma conta de armazenamento do Azure.
>>>>>>> 3519a330aa86b6827d31403690529105825b1b16

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
    "parameters": {
        "configuration": {
            "enableAnnotationCache": true
        }
    }
}
```
#### <a name="enable-reporocessing"></a>Habilitar reporocessing

Opcionalmente, você pode definir a propriedade booleana `enableReprocessing` no cache, que é definida por padrão como true. O sinalizador `enableReprocessing` permite que você controle o comportamento do seu indexador. Em cenários em que você deseja que o indexador priorize a adição de novos documentos ao índice, defina o sinalizador como false. Depois que o indexador for atualizado com os novos documentos, a inversão do sinalizador para true permitiria que o indexador começasse a impulsionar os documentos existentes para a consistência eventual. Durante o período em que o sinalizador de `enableReprocessing` é definido como false, o indexador só grava no cache, mas não processará nenhum documento existente com base nas alterações identificadas no pipeline de enriquecimento.

### <a name="step-3-reset-the-indexer"></a>Etapa 3: redefinir o indexador

> [!NOTE]
> A redefinição do indexador fará com que todos os documentos na fonte de dados sejam processados novamente para que o conteúdo possa ser armazenado em cache. Todos os aprimoramentos cognitivas serão executados novamente em todos os documentos.
>

Uma redefinição do indexador é necessária ao configurar a indexação incremental para os indexadores existentes para garantir que todos os documentos estejam em um estado consistente. Redefina o indexador usando a [API REST](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

```http
POST https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-4-save-the-updated-definition"></a>Etapa 4: salvar a definição atualizada

Atualize a definição do indexador com uma solicitação PUT, o corpo da solicitação deve conter a definição do indexador atualizado.

```http
PUT https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
{
    "name" : "your indexer name",
    ...
    "cache": {
        "storageConnectionString": "[your storage connection string]",
        "enableReprocessing": true
    }
}
```

Se agora você emitir outra solicitação GET no indexador, a resposta do serviço incluirá uma propriedade `cacheId` no objeto cache. O `cacheId` é o nome do contêiner que conterá todos os resultados armazenados em cache e o estado intermediário de cada documento processado por esse indexador.

## <a name="enable-incremental-indexing-on-new-indexers"></a>Habilitar indexação incremental em novos indexadores

Para configurar a indexação incremental para um novo indexador, inclua a propriedade `cache` na carga de definição do indexador. Verifique se você está usando a versão `2019-05-06-Preview` da API.

## <a name="overriding-incremental-indexing"></a>Substituindo indexação incremental

Quando configurado, a indexação incremental controla as alterações em seu pipeline de indexação e orienta os documentos para consistência eventual em seu índice e projeções. Em alguns casos, você precisará substituir esse comportamento para garantir que o indexador não faça trabalho adicional como resultado de uma atualização para o pipeline de indexação. Por exemplo, atualizar a cadeia de conexão DataSource exigirá uma redefinição e reindexação de indexador de todos os documentos à medida que a fonte de caracteres tiver sido alterada. Mas se você estiver atualizando apenas a cadeia de conexão com uma nova chave, não iria querer que a alteração resulte em quaisquer atualizações nos documentos existentes. Por outro lado, você pode querer que o indexador invalidasse o cache e enriquecer documentos mesmo se nenhuma alteração no pipeline de indexação for feita. Por exemplo, talvez você queira invalidar o indexador se reimplantar uma habilidade personalizada com um novo modelo e quisesse que a habilidade fosse executada novamente em todos os seus documentos.

### <a name="override-reset-requirement"></a>Substituir requisito de redefinição

Ao fazer alterações no pipeline de indexação, todas as alterações que resultam em uma invalidação do cache exigem uma redefinição do indexador. Se você estiver fazendo uma alteração no pipeline do indexador e não quiser que o controle de alterações invalidasse o cache, você precisará definir o parâmetro `ignoreResetRequirement` QueryString como `true` para operações no indexador ou na fonte de fontes.

### <a name="override-change-detection"></a>Substituir detecção de alteração

Ao fazer atualizações no conjunto de qualificações que resultaria em documentos sendo sinalizados como inconsistentes, por exemplo, atualizando uma URL de habilidade personalizada quando a habilidade é reimplantada, defina o parâmetro de cadeia de caracteres de consulta `disableCacheReprocessingChangeDetection` para `true` em atualizações de conjunto de qualificações.

### <a name="force-change-detection"></a>Forçar detecção de alteração

Quando você quiser que o pipeline de indexação reconheça uma alteração em uma entidade externa, como implantar uma nova versão de uma habilidade personalizada, você precisará atualizar o conjunto de qualificações e "tocar" a habilidade específica editando a definição de habilidades, especificamente a URL a ser forçada alterar a detecção e invalidar o cache para essa habilidade.

## <a name="next-steps"></a>Passos seguintes

Este artigo aborda a indexação incremental para indexadores que incluem habilidades. Para arredondar ainda mais seu conhecimento, examine os artigos sobre a reindexação em geral, aplicável a todos os cenários de indexação no Azure Pesquisa Cognitiva.

+ [Como recriar um índice de pesquisa cognitiva do Azure](search-howto-reindex.md). 
+ [Como indexar grandes conjuntos de dados no Azure pesquisa cognitiva](search-howto-large-index.md). 
