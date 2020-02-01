---
title: Pesquisar sobre o conteúdo de armazenamento da Blob Azure
titleSuffix: Azure Cognitive Search
description: Saiba como indexar o Armazenamento de Blob Azure e extrair texto de documentos com pesquisa cognitiva Azure.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cd2e34be7ef55c4ee6d18c6db6010134a7d935d1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76895950"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>Como indexar documentos no Armazenamento de Blob Azure com pesquisa cognitiva azure

Este artigo mostra como usar a Pesquisa Cognitiva Azure para indexar documentos (tais como PDFs, documentos do Microsoft Office e vários outros formatos comuns) armazenados no armazenamento do Azure Blob. Primeiro, explica o básico de configurar e configurar um indexador de bolhas. Depois, oferece uma exploração mais profunda de comportamentos e cenários que é provável que encontre.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Formatos de documento suportados
O indexante blob pode extrair texto dos seguintes formatos documentais:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Configuração da indexação de blob
Pode configurar um indexador de armazenamento Azure Blob utilizando:

* [Portal do Azure](https://ms.portal.azure.com)
* [API de](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) pesquisa cognitiva azure
* Pesquisa Cognitiva Azure [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Algumas funcionalidades (por exemplo, mapeamentos de campo) ainda não estão disponíveis no portal, e têm de ser usadas programáticamente.
>

Aqui, demonstramos o fluxo usando a API REST.

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados
Uma fonte de dados especifica quais os dados indexados, credenciais necessárias para aceder aos dados e políticas para identificar eficientemente alterações nos dados (novas linhas modificadas ou eliminadas). Uma fonte de dados pode ser usada por vários indexadores no mesmo serviço de pesquisa.

Para a indexação de blob, a fonte de dados deve ter as seguintes propriedades necessárias:

* **o nome** é o nome único da fonte de dados dentro do seu serviço de pesquisa.
* **tipo** deve ser `azureblob`.
* **credenciais** fornece a cadeia de ligação da conta de armazenamento como o parâmetro `credentials.connectionString`. Consulte [como especificar credenciais](#Credentials) abaixo para obter detalhes.
* **o recipiente** especifica um recipiente na sua conta de armazenamento. Por defeito, todas as bolhas dentro do recipiente são recuperáveis. Se quiser indexar bolhas num determinado diretório virtual, pode especificar esse diretório utilizando o parâmetro de **consulta** opcional.

Para criar uma fonte de dados:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Para mais informações sobre a Create Datasource API, consulte [Create Datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Como especificar credenciais ####

Pode fornecer as credenciais para o recipiente de bolhas de uma destas formas:

- **Cadeia de ligação**à conta de armazenamento de acesso completo : `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` Pode obter a cadeia de ligação do portal Azure navegando até à lâmina da conta de armazenamento > Definições > Chaves (para contas de armazenamento clássicas) ou Definições > Chaves de acesso (para contas de armazenamento do Gestor de Recursos Azure).
- Cadeia de **ligação de assinatura** de acesso partilhado (SAS) da conta de armazenamento: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` A SAS deve ter a lista e ler permissões em recipientes e objetos (bolhas neste caso).
-  **Assinatura**de acesso partilhado do contentor : `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` A SAS deve ter a lista e ler permissões no recipiente.

Para obter mais informações sobre o armazenamento de assinaturas de acesso partilhado, consulte [A Utilização de Assinaturas de Acesso Partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Se utilizar as credenciais SAS, terá de atualizar periodicamente as credenciais de origem de dados com assinaturas renovadas para evitar a sua expiração. Se as credenciais SAS expirarem, o indexante falhará com uma mensagem de erro semelhante à `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Passo 2: criar um índice
O índice especifica os campos num documento, atributos e outras construções que moldam a experiência de pesquisa.

Aqui está como criar um índice com um campo de `content` pesquisável para armazenar o texto extraído de bolhas:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Para mais informações sobre a criação de índices, consulte [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Passo 3: Criar um indexador
Um indexante liga uma fonte de dados a um índice de pesquisa de alvo, e fornece um horário para automatizar a atualização de dados.

Depois que o índice e a fonte de dados tiverem sido criados, você estará pronto para criar o indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Este indexante funcionará de duas em duas horas (o intervalo de horário está definido para "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo como "PT30M". O intervalo mais curto com suporte é de 5 minutos. A agenda é opcional-se omitida, um indexador será executado apenas uma vez quando for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.   

Para obter mais detalhes sobre a API criar indexador, confira [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Para obter mais informações sobre a definição de horários indexantes, consulte [como agendar indexadores para pesquisa cognitiva azure](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Como a Pesquisa Cognitiva Azure indexa bolhas

Dependendo da configuração do [indexador,](#PartsOfBlobToIndex)o indexante blob só pode indexar metadados de armazenamento (útil quando só se preocupa com os metadados e não precisa de indexar o conteúdo das bolhas), dos metadados de armazenamento e conteúdo, ou tanto de metadados como de conteúdo textual. Por padrão, o indexante extrai metadados e conteúdos.

> [!NOTE]
> Por predefinição, as bolhas com conteúdo estruturado, tais como JSON ou CSV, são indexadas como um único pedaço de texto. Se quiser indexar as bolhas JSON e CSV de forma estruturada, consulte [bolhas JSON indexantes](search-howto-index-json-blobs.md) e [bolhas CSV indexantes](search-howto-index-csv-blobs.md) para obter mais informações.
>
> Um documento composto ou incorporado (como um arquivo ZIP ou um documento Word com e-mail do Outlook incorporado contendo anexos) também é indexado como um único documento.

* O conteúdo textual do documento é extraído num campo de cordas chamado `content`.

> [!NOTE]
> A Pesquisa Cognitiva Azure limita a quantidade de texto que extrai dependendo do nível de preços: 32.000 caracteres para o free tier, 64.000 para Basic, 4 milhões para standard, 8 milhões para standard S2 e 16 milhões para standard S3. Um aviso está incluído na resposta do estado do indexador para documentos truncados.  

* As propriedades de metadados especificadas pelo utilizador presentes na bolha, caso existam, são extraídas verbatim.
* As propriedades padrão dos metadados blob são extraídas nos seguintes campos:

  * **metadados\_\_nome de armazenagem** (Edm.String) - o nome do ficheiro da bolha. Por exemplo, se tiver uma bolha /my-container/my-folder/subfolder/resume.pdf, o valor deste campo é `resume.pdf`.
  * **metadados\_caminho\_de armazenamento** (Edm.String) - o URI completo da bolha, incluindo a conta de armazenamento. Por exemplo, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadados\_armazenamento\_conteúdo\_tipo** (Edm.String) - tipo de conteúdo especificado pelo código utilizado para carregar a bolha. Por exemplo, `application/octet-stream`.
  * **metadados\_armazenamento\_\_modificados** (Edm.DateTimeOffset) - última marca de tempo modificada para a bolha. A Azure Cognitive Search usa esta marca de tempo para identificar bolhas alteradas, para evitar reindexar tudo após a indexação inicial.
  * **metadados\_tamanho\_de armazenamento** (Edm.Int64) - tamanho blob em bytes.
  * **metadados\_armazenamento\_conteúdo\_md5** (Edm.String) - Hash MD5 do conteúdo blob, se disponível.
  * **metadados\_armazenamento\_sas\_token** (Edm.String) - Um token SAS temporário que pode ser usado por [habilidades personalizadas](cognitive-search-custom-skill-interface.md) para ter acesso à bolha. Esta ficha não deve ser armazenada para posterior utilização, uma vez que pode caducar.

* As propriedades dos metadados específicas de cada formato de documento são extraídas nos campos listados [aqui](#ContentSpecificMetadata).

Você não precisa definir campos para todas as propriedades acima no seu índice de pesquisa - basta capturar as propriedades que você precisa para a sua aplicação.

> [!NOTE]
> Muitas vezes, os nomes de campo no seu índice existente serão diferentes dos nomes de campo gerados durante a extração de documentos. Pode utilizar **mapeamentos** de campo para mapear os nomes de propriedade fornecidos pela Azure Cognitive Search para os nomes de campo no seu índice de pesquisa. Verá um exemplo de mapeamento de campo usado abaixo.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definição de chaves de documentos e mapeamentos de campo
Na Pesquisa Cognitiva Azure, a chave do documento identifica um documento de forma única. Todos os índices de pesquisa devem ter exatamente um campo chave do tipo Edm.String. O campo-chave é necessário para cada documento que está a ser adicionado ao índice (na verdade é o único campo necessário).  

Deve considerar cuidadosamente qual o campo extraído que deve mapear para o campo chave para o seu índice. Os candidatos são:

* **metadados\_\_nome** - este pode ser um candidato conveniente, mas note que 1) os nomes podem não ser únicos, pois pode ter bolhas com o mesmo nome em diferentes pastas, e 2) o nome pode conter caracteres que são inválidos em chaves de documentos, como traços. Você pode lidar com caracteres inválidos usando a função de [mapeamento](search-indexer-field-mappings.md#base64EncodeFunction) de campo `base64Encode` - se você faz isso, lembre-se de codificar chaves de documento ao passá-las em chamadas API como Lookup. (Por exemplo, em .NET pode utilizar o [método UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) para o efeito).
* **metadados\_caminho\_de armazenamento** - usando o caminho completo garante a singularidade, mas o caminho contém definitivamente caracteres `/` que são [inválidos numa chave](https://docs.microsoft.com/rest/api/searchservice/naming-rules)de documentos .  Tal como acima, tem a opção de codificar as teclas utilizando a [função](search-indexer-field-mappings.md#base64EncodeFunction)`base64Encode` .
* Se nenhuma das opções acima funcionar para si, pode adicionar uma propriedade personalizada de metadados às bolhas. Esta opção requer, no entanto, o seu processo de upload blob para adicionar essa propriedade de metadados a todas as bolhas. Uma vez que a chave é uma propriedade necessária, todas as bolhas que não têm essa propriedade não serão indexadas.

> [!IMPORTANT]
> Se não houver mapeamento explícito para o campo-chave no índice, a Pesquisa Cognitiva Azure utiliza automaticamente `metadata_storage_path` como a chave e base-64 codifica valores-chave (a segunda opção acima).
>
>

Para este exemplo, vamos escolher o campo `metadata_storage_name` como chave de documento. Vamos também assumir que o seu índice tem um campo-chave chamado `key` e um campo `fileSize` para armazenar o tamanho do documento. Para ligar as coisas conforme desejado, especifique os seguintes mapeamentos de campo ao criar ou atualizar o seu indexante:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Para juntar tudo isto, eis como pode adicionar mapeamentos de campo e permitir a codificação base-64 de chaves para um indexante existente:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }

> [!NOTE]
> Para saber mais sobre mapeamentos de campo, consulte [este artigo.](search-indexer-field-mappings.md)
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Controlando quais bolhas estão indexadas
Pode controlar quais as bolhas indexadas e que são ignoradas.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indexar apenas as bolhas com extensões específicas de ficheiros
Só pode indexar as bolhas com as extensões de nome de ficheiro que especifica utilizando o parâmetro de configuração do indexante `indexedFileNameExtensions`. O valor é uma cadeia que contém uma lista separada de extensões de ficheiros separadas pela vírem (com um ponto de liderança). Por exemplo, para indexar apenas o . PDF e . Blobs DOCX, faça isto:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Excluir bolhas com extensões específicas de ficheiros
Pode excluir bolhas com extensões específicas de nome de ficheiro da indexação utilizando o parâmetro de configuração `excludedFileNameExtensions`. O valor é uma cadeia que contém uma lista separada de extensões de ficheiros separadas pela vírem (com um ponto de liderança). Por exemplo, para indexar todas as bolhas exceto as que têm . PNG e . Extensões JPEG, faça isto:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Se os parâmetros `indexedFileNameExtensions` e `excludedFileNameExtensions` estiverem presentes, a Pesquisa Cognitiva Azure olha primeiro para `indexedFileNameExtensions`, depois para `excludedFileNameExtensions`. Isto significa que, se a mesma extensão de ficheiro estiver presente em ambas as listas, será excluída da indexação.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Controlando quais partes da bolha estão indexadas

Pode controlar quais as partes das bolhas indexadas utilizando o parâmetro de configuração `dataToExtract`. Pode levar os seguintes valores:

* `storageMetadata` - especifica que apenas as propriedades padrão de [blob e os metadados especificados](../storage/blobs/storage-properties-metadata.md) pelo utilizador estão indexados.
* `allMetadata` - especifica que os metadados de armazenamento e os [metadados específicos](#ContentSpecificMetadata) do tipo de conteúdo extraídos do conteúdo blob estão indexados.
* `contentAndMetadata` - especifica que todos os metadados e conteúdos texuais extraídos da bolha estão indexados. Este é o valor predefinido.

Por exemplo, para indexar apenas os metadados de armazenamento, utilize:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Usando metadados blob para controlar como as bolhas são indexadas

Os parâmetros de configuração acima descritos aplicam-se a todas as bolhas. Às *vezes,* pode querer controlar como as bolhas individuais são indexadas. Pode fazê-lo adicionando as seguintes propriedades e valores de metadados blob:

| Nome da propriedade | Valor patrimonial | Explicação |
| --- | --- | --- |
| AzureSearch_Skip |"Verdadeiro" |Instrui o indexante blob a saltar completamente a bolha. Nem os metadados nem a extração de conteúdos são tentados. Isto é útil quando uma bolha particular falha repetidamente e interrompe o processo de indexação. |
| AzureSearch_SkipContent |"Verdadeiro" |Isto equivale a `"dataToExtract" : "allMetadata"` definição descrita [acima do](#PartsOfBlobToIndex) âmbito de aplicação a uma bolha específica. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Lidar com erros

Por padrão, o indexante blob para assim que encontra uma bolha com um tipo de conteúdo não suportado (por exemplo, uma imagem). É claro que pode utilizar o parâmetro `excludedFileNameExtensions` para saltar certos tipos de conteúdo. No entanto, pode ser necessário indexar bolhas sem conhecer antecipadamente todos os tipos de conteúdo possíveis. Para continuar a indexação quando for encontrado um tipo de conteúdo não suportado, defina o parâmetro de configuração `failOnUnsupportedContentType` para `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Para algumas bolhas, a Pesquisa Cognitiva Azure não consegue determinar o tipo de conteúdo, ou não consegue processar um documento do tipo de conteúdo suportado de outra forma. Para ignorar este modo de falha, defina o parâmetro de configuração `failOnUnprocessableDocument` para falso:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

A Pesquisa Cognitiva Azure limita o tamanho das bolhas indexadas. Estes limites estão documentados nos limites de [serviço na Pesquisa Cognitiva Azure.](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) Bolhas de grandes dimensões são tratadas como erros por padrão. No entanto, ainda pode indexar metadados de armazenamento de bolhas de grandes dimensões se definir `indexStorageMetadataOnlyForOversizedDocuments` parâmetro de configuração verdadeiro: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Também pode continuar a indexar se os erros ocorrerem em qualquer ponto de processamento, quer enquanto analisa bolhas ou ao mesmo tempo que adiciona documentos a um índice. Para ignorar um número específico de erros, defina os parâmetros de configuração `maxFailedItems` e `maxFailedItemsPerBatch` para os valores desejados. Por exemplo:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Deteção incremental de indexação e eliminação
Quando se cria um indexante de bolha para funcionar num horário, reindexa apenas as bolhas alteradas, conforme determinado pela `LastModified` carimbo temporal da bolha.

> [!NOTE]
> Não é necessário especificar uma política de deteção de alterações – o indexante incremental está ativado automaticamente para si.

Para apoiar a eliminação de documentos, utilize uma abordagem de "apagar suavemente". Se eliminar as bolhas, os documentos correspondentes não serão removidos do índice de pesquisa. Em vez disso, utilize os seguintes passos:  

1. Adicione uma propriedade personalizada de metadados à bolha para indicar ao Azure Cognitive Search que é logicamente eliminado
2. Configure uma política de deteção de eliminação suave na fonte de dados
3. Uma vez que o indexante tenha processado a bolha (como mostra o estado indexante API), pode eliminar fisicamente a bolha

Por exemplo, a seguinte política considera que uma bolha deve ser eliminada se tiver uma propriedade de metadados `IsDeleted` com o valor `true`:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

## <a name="indexing-large-datasets"></a>Indexação de grandes conjuntos de dados

Indexar bolhas pode ser um processo demorado. Nos casos em que tem milhões de bolhas para indexar, pode acelerar a indexação dividindo os seus dados e usando vários indexadores para processar os dados em paralelo. Eis como pode supor isto:

- Partição dos seus dados em vários recipientes de blob ou pastas virtuais
- Configurar várias fontes de dados de Pesquisa Cognitiva Azure, uma por recipiente ou pasta. Para apontar para uma pasta blob, utilize o parâmetro `query`:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Crie um indexador correspondente para cada fonte de dados. Todos os indexadores podem apontar para o mesmo índice de pesquisa alvo.  

- Uma unidade de pesquisa ao seu serviço pode executar um indexante a qualquer momento. A criação de múltiplos indexadores, tal como descrito acima, só é útil se funcionarem em paralelo. Para executar vários indexantes em paralelo, esforce o seu serviço de pesquisa criando um número adequado de divisórias e réplicas. Por exemplo, se o seu serviço de pesquisa tiver 6 unidades de pesquisa (por exemplo, 2 divisórias x 3 réplicas), então 6 indexadores podem funcionar simultaneamente, resultando num aumento de seis vezes na entrada de indexação. Para saber mais sobre o dimensionamento e o planeamento da capacidade, consulte os níveis de recursos de escala para consulta e indexação de cargas de [trabalho na Pesquisa Cognitiva Azure.](search-capacity-planning.md)

## <a name="indexing-documents-along-with-related-data"></a>Indexação de documentos juntamente com dados relacionados

Pode querer "montar" documentos de várias fontes do seu índice. Por exemplo, pode querer fundir texto de blobs com outros metadados armazenados em Cosmos DB. Pode até utilizar a API de indexação de impulso juntamente com vários indexadores para construir documentos de pesquisa a partir de várias partes. 

Para que isto funcione, todos os indexadores e outros componentes têm de concordar com a chave do documento. Para mais detalhes sobre este tópico, consulte o Index multiple Source de [dados Azure](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources). Para uma análise detalhada, consulte este artigo externo: [Combine documentos com outros dados em Pesquisa Cognitiva Azure](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indexação do texto simples 

Se todas as suas bolhas contiverem texto simples na mesma codificação, pode melhorar significativamente o desempenho da indexação utilizando o modo de análise de **texto**. Para utilizar o modo de análise de texto, detete a propriedade de configuração `parsingMode` para `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Por padrão, as `UTF-8` codificação são assumidas. Para especificar uma codificação diferente, utilize a propriedade de configuração `encoding`: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Propriedades de metadados específicos do tipo de conteúdo
A tabela seguinte resume o processamento feito para cada formato de documento, e descreve as propriedades dos metadados extraídas pela Pesquisa Cognitiva Azure.

| Formato de documento / tipo de conteúdo | Propriedades específicas de metadados do tipo de conteúdo | Processamento de detalhes |
| --- | --- | --- |
| HTML (texto/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Strip HTML de marcação e extrato de texto |
| PDF (aplicação/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extrair texto, incluindo documentos embutidos (excluindo imagens) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrair texto, incluindo documentos embutidos |
| DOC (aplicação/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrair texto, incluindo documentos embutidos |
| DOCM (aplicação/vnd.ms-word.document.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrair texto, incluindo documentos embutidos |
| WORD XML (aplicação/vnd.ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Strip XML de marcação e extrato de texto |
| PALAVRA 2003 XML (aplicação/vnd.ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Strip XML de marcação e extrato de texto |
| XLSX (aplicação/vnd.openxmlformats-officedocument.sheetml.sheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrair texto, incluindo documentos embutidos |
| XLS (aplicação/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrair texto, incluindo documentos embutidos |
| XLSM (aplicação/vnd.ms-excel.sheet.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrair texto, incluindo documentos embutidos |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrair texto, incluindo documentos embutidos |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrair texto, incluindo documentos embutidos |
| PPTM (aplicação/vnd.ms-powerpoint.presentation.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrair texto, incluindo documentos embutidos |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrair texto, incluindo anexos. `metadata_message_to_email`, `metadata_message_cc_email` e `metadata_message_bcc_email` são coleções de cordas, o resto dos campos são cordas.|
| ODT (application/vnd.oasis.opendocument.text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrair texto, incluindo documentos embutidos |
| ODS (application/vnd.oasis.opendocument.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrair texto, incluindo documentos embutidos |
| ODP (application/vnd.oasis.opendocument.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Extrair texto, incluindo documentos embutidos |
| ZIP (aplicação/zip) |`metadata_content_type` |Extrair texto de todos os documentos do arquivo |
| GZ (aplicação/gzip) |`metadata_content_type` |Extrair texto de todos os documentos do arquivo |
| EPUB (aplicação/epub+zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Extrair texto de todos os documentos do arquivo |
| XML (aplicação/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Strip XML de marcação e extrato de texto |
| JSON (aplicação/json) |`metadata_content_type`<br/>`metadata_content_encoding` |Extrair texto<br/>NOTA: Se precisar de extrair vários campos de documentos de uma bolha JSON, consulte [bolhas JSON indexantes](search-howto-index-json-blobs.md) para obter mais detalhes |
| EML (mensagem/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extrair texto, incluindo anexos |
| RTF (aplicação/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Extrair texto|
| Texto simples (texto/planície) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Extrair texto|


## <a name="help-us-make-azure-cognitive-search-better"></a>Ajude-nos a tornar o Azure Pesquisa Cognitiva melhor
Se tiver pedidos de funcionalidades ou ideias para melhorias, informe-nos no nosso [site userVoice](https://feedback.azure.com/forums/263029-azure-search/).
