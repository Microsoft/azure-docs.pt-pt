---
title: Configurar o cache e o enriquecimento incremental (visualização)
titleSuffix: Azure Cognitive Search
description: Habilite o Caching e preserve o estado do conteúdo aprimorado para processamento controlado em um conhecimento cognitiva. Este recurso está atualmente em visualização pública.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 66bac2a063a3257a2101ca2f30e5946264adb9ae
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989557"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Como configurar o Caching para aprimoramento incremental no Azure Pesquisa Cognitiva

> [!IMPORTANT] 
> O enriquecimento incremental está atualmente em visualização pública. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para Portal ou SDK do .NET no momento.

Este artigo mostra como adicionar o Caching a um pipeline de enriquecimento para que você possa modificar as etapas incrementalmente sem precisar recompilar todas as vezes. Por padrão, um qualificable é sem monitoração de estado e a alteração de qualquer parte de sua composição requer uma execução completa do indexador. Com o aprimoramento incremental, o indexador pode determinar quais partes da árvore de documentos precisam ser atualizadas com base nas alterações detectadas nas definições do concordador ou do indexador. A saída processada existente é preservada e reutilizada sempre que possível. 

O conteúdo armazenado em cache é colocado no armazenamento do Azure usando as informações de conta que você fornece. O contêiner, chamado `ms-az-search-indexercache-<alpha-numerc-string>`, é criado quando você executa o indexador. Ele deve ser considerado um componente interno gerenciado pelo serviço de pesquisa e não deve ser modificado.

Se você não estiver familiarizado com a configuração de indexadores, comece com a [visão geral do indexador](search-indexer-overview.md) e continue em [habilidades](cognitive-search-working-with-skillsets.md) para saber mais sobre pipelines de enriquecimento. Para obter mais informações sobre os principais conceitos, consulte [enriquecimento incremental](cognitive-search-incremental-indexing-conceptual.md).

## <a name="enable-caching-on-an-existing-indexer"></a>Habilitar o Caching em um indexador existente

Se você tiver um indexador existente que já tenha um qualificable, siga as etapas nesta seção para adicionar o Caching. Como uma operação única, você precisará redefinir e executar novamente o indexador completamente antes que o processamento incremental possa entrar em vigor.

> [!TIP]
> Como prova de conceito, você pode executar este guia de [início rápido do portal](cognitive-search-quickstart-blob.md) para criar os objetos necessários e, em seguida, usar o postmaster ou o portal para fazer suas atualizações. Talvez você queira anexar um recurso de serviços cognitivas cobráveis. A execução do indexador várias vezes esgotará a alocação diária gratuita antes que você possa concluir todas as etapas.

### <a name="step-1-get-the-indexer-definition"></a>Etapa 1: obter a definição do indexador

Comece com um indexador válido e existente que tenha estes componentes: fonte de dados, qualificações, índice. O indexador deve ser executável. 

Usando um cliente de API, construa uma [solicitação obter indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer) para obter a configuração atual do indexador. Quando utiliza a versão API de pré-visualização para o indexante GET, é adicionado um `cache` imóvel definido para nulo às definições.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Copie a definição do indexador da resposta.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>Etapa 2: modificar a propriedade de cache na definição do indexador

Por padrão, a propriedade `cache` é nula. Utilize um cliente API para definir a configuração da cache (o portal não suporta esta atualização de partículas). 

Modifique o objeto de cache para incluir as seguintes propriedades obrigatórias e opcionais: 

+ O `storageConnectionString` é necessário e deve ser definido como uma cadeia de conexão de armazenamento do Azure. 
+ A propriedade booleana `enableReprocessing` é opcional (`true` por padrão) e indica que o enriquecimento incremental está habilitado. Quando necessário, pode defini-lo para `false` suspender o processamento incremental enquanto outras operações intensivas de recursos, como a indexação de novos documentos, estão em andamento e depois voltar para `true` mais tarde.

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

### <a name="step-3-reset-the-indexer"></a>Etapa 3: redefinir o indexador

Uma redefinição do indexador é necessária ao configurar o enriquecimento incremental para os indexadores existentes para garantir que todos os documentos estejam em um estado consistente. Você pode usar o portal ou um cliente de API e a [API REST de redefinir indexador](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) para essa tarefa.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>Etapa 4: salvar a definição atualizada

[Atualize o indexante](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) com um pedido PUT, o corpo do pedido deve conter a definição atualizada do indexante que tem a propriedade cache. Se você receber um 400, verifique a definição do indexador para garantir que todos os requisitos sejam atendidos (fonte de dados, Configurador de habilidades, índice).

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

Se agora você emitir outra solicitação GET no indexador, a resposta do serviço incluirá uma propriedade `ID` no objeto cache. A cadeia de caracteres alfanumérica é anexada ao nome do contêiner que contém todos os resultados armazenados em cache e o estado intermediário de cada documento processado por esse indexador. A ID será usada para nomear exclusivamente o cache no armazenamento de BLOBs.

    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }

### <a name="step-5-run-the-indexer"></a>Etapa 5: executar o indexador

Para executar o indexante, pode utilizar o portal ou a API. No portal, a partir da lista de indexadores, selecione o indexador e clique em **Executar**. Uma vantagem de usar o portal é que você pode monitorar o status do indexador, observar a duração do trabalho e quantos documentos são processados. As páginas do portal são atualizadas a cada poucos minutos.

Em alternativa, pode utilizar o REST para [executar o indexante:](https://docs.microsoft.com/rest/api/searchservice/run-indexer)

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Após a corrida do indexante, você pode encontrar a cache no armazenamento Azure Blob. O nome do contêiner está no seguinte formato: `ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> Uma redefinição e uma nova execução do indexador resulta em uma recompilação completa para que o conteúdo possa ser armazenado em cache. Todos os aprimoramentos cognitivas serão executados novamente em todos os documentos.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>Etapa 6: modificar um habilidades e confirmar o enriquecimento incremental

Para modificar uma habilidade, pode utilizar o portal ou a API. Por exemplo, se você estiver usando a conversão de texto, uma alteração embutida simples de `en` para `es` ou outra linguagem será suficiente para o teste de prova de conceito de enriquecimento incremental.

Execute o indexador novamente. Somente as partes de uma árvore de documentos aprimoradas são atualizadas. Se você usou o [início rápido do portal](cognitive-search-quickstart-blob.md) como prova de conceito, modificando a habilidade de conversão de texto para ' es ', observará que apenas 8 documentos são atualizados em vez do 14 original. Os arquivos de imagem não afetados pelo processo de tradução são reutilizados do cache.

## <a name="enable-caching-on-new-indexers"></a>Habilitar o Caching em novos indexadores

Para configurar o enriquecimento incremental para um novo indexante, tudo o que tem de fazer é incluir a propriedade `cache` na carga útil da definição do indexante ao chamar [O Indexer de Criação (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer). 


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

## <a name="checking-for-cached-output"></a>Verificando a saída armazenada em cache

O cache é criado, usado e gerenciado pelo indexador, e seu conteúdo não é representado em um formato legível por humanos. A melhor maneira de determinar se o cache é usado é executando o indexador e comparar as métricas antes e depois do tempo de execução e das contagens de documentos. 

Por exemplo, suponha um técnico que começa com análise de imagem e OCR (reconhecimento óptico de caracteres) de documentos digitalizados, seguidos pela análise downstream do texto resultante. Se você modificar uma habilidade de texto downstream, o indexador poderá recuperar toda a imagem processada anteriormente e o conteúdo de OCR do cache, atualizar e processar apenas as alterações relacionadas a texto indicadas por suas edições. Você pode esperar que você veja menos documentos na contagem de documentos (por exemplo, 8/8 em vez de 14/14 na execução original), tempos de execução menores e menos encargos em sua fatura.

## <a name="working-with-the-cache"></a>Trabalhando com o cache

Quando o cache estiver operacional, os indexadores verificarão o cache sempre que o [indexador de execução](https://docs.microsoft.com/rest/api/searchservice/run-indexer) for chamado, para ver quais partes da saída existente podem ser usadas. 

A tabela a seguir resume como várias APIs se relacionam com o cache:

| API           | Impacto no cache     |
|---------------|------------------|
| [Criar Indexer (2019-05-06-Pré-visualização)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) | Cria e executa um indexador no primeiro uso, incluindo a criação de um cache se a definição do indexador o especificar. |
| [Executar indexador](https://docs.microsoft.com/rest/api/searchservice/run-indexer) | Executa um pipeline de enriquecimento sob demanda. Essa API lê o cache se ele existir, ou cria um cache se você adicionou o cache a uma definição de indexador atualizada. Quando você executa um indexador que tem o Caching habilitado, o indexador omite etapas se a saída armazenada em cache pode ser usada. Pode utilizar a versão API geralmente disponível ou pré-visualizada deste API.|
| [Redefinir indexador](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)| Limpa o indexador de qualquer informação de indexação incremental. A próxima execução do indexador (sob demanda ou agendamento) é o reprocessamento completo do zero, incluindo a nova execução de todas as habilidades e a recriação do cache. É funcionalmente equivalente a excluir o indexador e recriá-lo. Pode utilizar a versão API geralmente disponível ou pré-visualizada deste API.|
| [Reset Skills (2019-05-06-Pré-visualização)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) | Especifica quais habilidades executar novamente na próxima execução do indexador, mesmo que você não tenha modificado nenhuma habilidade. O cache é atualizado de acordo. As saídas, como uma loja de conhecimento ou um índice de pesquisa, são atualizadas usando dados reutilizáveis do cache, mais o novo conteúdo de acordo com a habilidade atualizada. |

Para obter mais informações sobre como controlar o que acontece com o cache, consulte [Gerenciamento de cache](cognitive-search-incremental-indexing-conceptual.md#cache-management).

## <a name="next-steps"></a>Passos seguintes

O enriquecimento incremental é aplicável a indexadores que contêm habilidades. Como uma próxima etapa, visite a documentação do skillset para entender os conceitos e a composição. 

Além disso, depois de habilitar o cache, você desejará saber sobre os parâmetros e as APIs que abrangem o cache, incluindo como substituir ou forçar comportamentos específicos. Para obter mais informações, consulte os links a seguir.

+ [Conceitos e composição do Skills](cognitive-search-working-with-skillsets.md)
+ [Como criar um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Introdução ao Caching e enriquecimento incremental](cognitive-search-incremental-indexing-conceptual.md)
