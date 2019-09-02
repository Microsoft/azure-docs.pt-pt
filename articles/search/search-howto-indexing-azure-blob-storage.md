---
title: Indexar o conteúdo do armazenamento de BLOBs do Azure para pesquisa de texto completo-Azure Search
description: Saiba como indexar o armazenamento de BLOBs do Azure e extrair texto de documentos com Azure Search.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 902887c2a765fa50c7075cbdcb835f53e84f583f
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208265"
---
# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Indexação de documentos no armazenamento de BLOBs do Azure com Azure Search
Este artigo mostra como usar Azure Search para indexar documentos (como, por exemplo, PDFs, Microsoft Office documentos e vários outros formatos comuns) armazenados no armazenamento de BLOBs do Azure. Primeiro, ele explica as noções básicas de configuração e configuração de um indexador de BLOB. Em seguida, ele oferece uma exploração mais profunda dos comportamentos e cenários que você provavelmente encontrará.

## <a name="supported-document-formats"></a>Formatos de documento com suporte
O indexador de blob pode extrair texto dos seguintes formatos de documento:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Configurando a indexação de BLOB
Você pode configurar um indexador de armazenamento de BLOBs do Azure usando:

* [Azure portal](https://ms.portal.azure.com)
* [API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) do Azure Search
* [SDK do .net](https://aka.ms/search-sdk) Azure Search

> [!NOTE]
> Alguns recursos (por exemplo, mapeamentos de campo) ainda não estão disponíveis no portal e precisam ser usados programaticamente.
>

Aqui, demonstramos o fluxo usando a API REST.

### <a name="step-1-create-a-data-source"></a>Passo 1: Criar uma origem de dados
Uma fonte de dados especifica quais dados indexar, as credenciais necessárias para acessar os dados e as políticas para identificar com eficiência as alterações nos dados (linhas novas, modificadas ou excluídas). Uma fonte de dados pode ser usada por vários indexadores no mesmo serviço de pesquisa.

Para indexação de BLOB, a fonte de dados deve ter as seguintes propriedades obrigatórias:

* **nome** é o nome exclusivo da fonte de dados no serviço de pesquisa.
* o **tipo** deve `azureblob`ser.
* **as credenciais** fornecem a cadeia de conexão da conta `credentials.connectionString` de armazenamento como o parâmetro. Consulte [como especificar as credenciais](#Credentials) abaixo para obter detalhes.
* **contêiner** especifica um contêiner em sua conta de armazenamento. Por padrão, todos os BLOBs no contêiner são recuperáveis. Se você quiser indexar apenas BLOBs em um determinado diretório virtual, poderá especificar esse diretório usando o parâmetro de **consulta** opcional.

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

Para obter mais informações sobre a API Create DataSource, consulte [Create DataSource](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Como especificar credenciais ####

Você pode fornecer as credenciais para o contêiner de blob de uma das seguintes maneiras:

- **Cadeia de conexão da conta de armazenamento de acesso completo**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`Você pode obter a cadeia de conexão do portal do Azure navegando até a folha da conta de armazenamento > Configurações > chaves (para contas de armazenamento clássicas) ou configurações > chaves de acesso (para contas de armazenamento Azure Resource Manager).
- **Assinatura de acesso compartilhado da conta de armazenamento** (SAS) cadeia de conexão: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl`A SAS deve ter a lista e permissões de leitura em contêineres e objetos (BLOBs, nesse caso).
-  **Assinatura de acesso compartilhado do contêiner**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl`A SAS deve ter as permissões de lista e leitura no contêiner.

Para obter mais informações sobre assinaturas de acesso compartilhado de armazenamento, consulte [usando assinaturas de acesso compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Se você usar credenciais SAS, precisará atualizar as credenciais da fonte de dados periodicamente com assinaturas renovadas para evitar sua expiração. Se as credenciais SAS expirarem, o indexador falhará com uma mensagem de erro `Credentials provided in the connection string are invalid or have expired.`semelhante a.  

### <a name="step-2-create-an-index"></a>Passo 2: Criar um índice
O índice especifica os campos em um documento, atributos e outras construções que modelam a experiência de pesquisa.

Veja como criar um índice com um `content` campo pesquisável para armazenar o texto extraído dos BLOBs:   

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

Para obter mais informações sobre a criação de índices, consulte [criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Passo 3: Criar um indexador
Um indexador conecta uma fonte de dados a um índice de pesquisa de destino e fornece uma agenda para automatizar a atualização de dados.

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

Esse indexador será executado a cada duas horas (o intervalo de agendamento é definido como "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo como "PT30M". O intervalo mais curto com suporte é de 5 minutos. A agenda é opcional-se omitida, um indexador será executado apenas uma vez quando for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.   

Para obter mais detalhes sobre a API criar indexador, confira [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Para obter mais informações sobre como definir agendas do indexador, consulte [como agendar indexadores para Azure Search](search-howto-schedule-indexers.md).

## <a name="how-azure-search-indexes-blobs"></a>Como Azure Search os blobs de índices

Dependendo da configuração do [indexador](#PartsOfBlobToIndex), o indexador de blob só pode indexar metadados de armazenamento (útil quando você se preocupa apenas com os metadados e não precisa indexar o conteúdo de BLOBs), armazenamento e metadados de conteúdo ou metadados e conteúdo textual. Por padrão, o indexador extrai metadados e conteúdo.

> [!NOTE]
> Por padrão, os BLOBs com conteúdo estruturado, como JSON ou CSV, são indexados como uma única parte do texto. Se você quiser indexar BLOBs JSON e CSV de forma estruturada, consulte [indexando BLOBs JSON](search-howto-index-json-blobs.md) e [indexando BLOBs CSV](search-howto-index-csv-blobs.md) para obter mais informações.
>
> Um documento composto ou incorporado (como um arquivo ZIP ou um documento do Word com emails inseridos do Outlook contendo anexos) também é indexado como um único documento.

* O conteúdo textual do documento é extraído em um campo de cadeia `content`de caracteres chamado.

> [!NOTE]
> Azure Search limita a quantidade de texto que ele extrai, dependendo do tipo de preço: 32.000 caracteres para camada gratuita, 64.000 para básico e 4 milhões para as camadas Standard, Standard S2 e Standard S3. Um aviso é incluído na resposta de status do indexador para documentos truncados.  

* As propriedades de metadados especificadas pelo usuário presentes no BLOB, se houver, são extraídas literalmente.
* As propriedades de metadados de blob padrão são extraídas nos seguintes campos:

  * **nome\_dearmazenamento\_de metadados** (EDM. String)-o nome de arquivo do blob. Por exemplo, se você tiver um blob/My-container/My-Folder/subfolder/resume.pdf, o valor desse campo será `resume.pdf`.
  * **caminho\_dearmazenamento\_de metadados** (EDM. String)-o URI completo do blob, incluindo a conta de armazenamento. Por exemplo, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **tipo\_de\_conteúdode\_armazenamento de metadados** (EDM. String)-tipo de conteúdo conforme especificado pelo código usado para carregar o blob. Por exemplo, `application/octet-stream`.
  * **armazenamento\_demetadados\_última modificação (EDM. DateTimeOffset)-carimbo de data/hora da última modificação para o blob.\_** Azure Search usa esse carimbo de data/hora para identificar BLOBs alterados, para evitar a reindexação de tudo após a indexação inicial.
  * **tamanho\_doarmazenamento\_de metadados** (EDM. Int64)-tamanho do blob em bytes.
  * **\_conteúdo\_de armazenamento de metadados MD5 (EDM. String)-hash MD5 do conteúdo do blob, se disponível.\_**
  * **token\_SAS\_de armazenamento de metadados (EDM. String)-um token SAS temporário que pode ser usado por habilidades personalizadas para obter acesso ao blob.\_** [](cognitive-search-custom-skill-interface.md) Esse token não deve ser armazenado para uso posterior, pois ele pode expirar.

* As propriedades de metadados específicas para cada formato de documento são extraídas nos campos listados [aqui](#ContentSpecificMetadata).

Você não precisa definir campos para todas as propriedades acima em seu índice de pesquisa – apenas capture as propriedades necessárias para seu aplicativo.

> [!NOTE]
> Geralmente, os nomes de campo no índice existente serão diferentes dos nomes de campo gerados durante a extração de documentos. Você pode usar **mapeamentos de campo** para mapear os nomes de propriedade fornecidos pelo Azure Search para os nomes de campo no índice de pesquisa. Você verá um exemplo de mapeamentos de campo que são usados abaixo.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definindo chaves de documento e mapeamentos de campo
No Azure Search, a chave do documento identifica exclusivamente um documento. Cada índice de pesquisa deve ter exatamente um campo de chave do tipo EDM. String. O campo de chave é necessário para cada documento que está sendo adicionado ao índice (na verdade, é o único campo obrigatório).  

Você deve considerar cuidadosamente qual campo extraído deve ser mapeado para o campo de chave do índice. Os candidatos são:

* **nome\_dearmazenamento\_de metadados** -pode ser um candidato conveniente, mas observe que 1) os nomes podem não ser exclusivos, pois você pode ter BLOBs com o mesmo nome em pastas diferentes e 2) o nome pode conter caracteres inválidos em chaves do documento, como traços. Você pode lidar com caracteres inválidos usando `base64Encode` a [função de mapeamento de campos](search-indexer-field-mappings.md#base64EncodeFunction) . se você fizer isso, lembre-se de codificar as chaves do documento ao passá-las em chamadas à API, como pesquisa. (Por exemplo, no .NET, você pode usar o [método UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) para essa finalidade).
* **caminho\_dearmazenamento\_de metadados** -usar o caminho completo garante a exclusividade, mas o caminho `/` definitivamente contém caracteres que são inválidos [em uma chave de documento](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Como acima, você tem a opção de codificar as chaves usando a `base64Encode` [função](search-indexer-field-mappings.md#base64EncodeFunction).
* Se nenhuma das opções acima funcionar para você, você poderá adicionar uma propriedade de metadados personalizada aos BLOBs. No entanto, essa opção requer que o processo de carregamento de blob adicione essa propriedade de metadados a todos os BLOBs. Como a chave é uma propriedade necessária, todos os blobs que não têm essa propriedade não serão indexados.

> [!IMPORTANT]
> Se não houver nenhum mapeamento explícito para o campo de chave no índice, Azure Search usará `metadata_storage_path` automaticamente como a chave e os valores de chave de 64 codificações (a segunda opção acima).
>
>

Para este exemplo, vamos escolher o `metadata_storage_name` campo como a chave do documento. Vamos supor também que o índice tem um campo de chave `key` chamado e um `fileSize` campo para armazenar o tamanho do documento. Para conectar as coisas conforme desejado, especifique os seguintes mapeamentos de campo ao criar ou atualizar o indexador:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Para reunir tudo isso, veja como você pode adicionar mapeamentos de campo e habilitar a codificação base 64 de chaves para um indexador existente:

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
> Para saber mais sobre mapeamentos de campo, consulte [Este artigo](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Controlando quais BLOBs são indexados
Você pode controlar quais BLOBs são indexados e quais são ignorados.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indexar somente os BLOBs com extensões de arquivo específicas
Você pode indexar apenas os BLOBs com as extensões de nome de arquivo especificadas usando `indexedFileNameExtensions` o parâmetro de configuração do indexador. O valor é uma cadeia de caracteres que contém uma lista separada por vírgulas de extensões de arquivo (com um ponto à esquerda). Por exemplo, para indexar apenas o. PDF e. BLOBs DOCX, faça o seguinte:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Excluir BLOBs com extensões de arquivo específicas
Você pode excluir BLOBs com extensões de nome de arquivo específicas da indexação usando `excludedFileNameExtensions` o parâmetro de configuração. O valor é uma cadeia de caracteres que contém uma lista separada por vírgulas de extensões de arquivo (com um ponto à esquerda). Por exemplo, para indexar todos os BLOBs, exceto aqueles com o. PNG e. Extensões JPEG, faça o seguinte:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Se ambos `indexedFileNameExtensions` os `excludedFileNameExtensions` parâmetros e estiverem presentes `indexedFileNameExtensions`, Azure Search primeiro examinará, em `excludedFileNameExtensions`seguida, em. Isso significa que, se a mesma extensão de arquivo estiver presente em ambas as listas, ela será excluída da indexação.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Controlando quais partes do blob são indexadas

Você pode controlar quais partes dos BLOBs são indexadas usando o `dataToExtract` parâmetro de configuração. Ele pode usar os seguintes valores:

* `storageMetadata`-Especifica que somente as [Propriedades de blob padrão e os metadados especificados pelo usuário](../storage/blobs/storage-properties-metadata.md) são indexados.
* `allMetadata`-Especifica que os metadados de armazenamento e os [metadados específicos do tipo de conteúdo](#ContentSpecificMetadata) extraídos do conteúdo do blob são indexados.
* `contentAndMetadata`-Especifica que todos os metadados e conteúdo textual extraídos do blob são indexados. Este é o valor predefinido.

Por exemplo, para indexar apenas os metadados de armazenamento, use:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Usando metadados de BLOB para controlar como os BLOBs são indexados

Os parâmetros de configuração descritos acima se aplicam a todos os BLOBs. Às vezes, talvez você queira controlar como os *BLOBs individuais* são indexados. Você pode fazer isso adicionando os seguintes valores e propriedades de metadados de blob:

| Nome da propriedade | Valor da propriedade | Explicação |
| --- | --- | --- |
| AzureSearch_Skip |true |Instrui o indexador de blob a ignorar completamente o blob. Não há tentativas de extração de conteúdo nem metadados. Isso é útil quando um blob específico falha repetidamente e interrompe o processo de indexação. |
| AzureSearch_SkipContent |true |Isso é equivalente à `"dataToExtract" : "allMetadata"` configuração descrita [acima](#PartsOfBlobToIndex) do escopo para um blob específico. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Lidando com erros

Por padrão, o indexador de BLOBs é interrompido assim que encontra um blob com um tipo de conteúdo sem suporte (por exemplo, uma imagem). É claro que você pode usar `excludedFileNameExtensions` o parâmetro para ignorar determinados tipos de conteúdo. No entanto, talvez seja necessário indexar BLOBs sem conhecer todos os tipos de conteúdo possíveis com antecedência. Para continuar a indexação quando um tipo de conteúdo sem suporte for encontrado, defina `failOnUnsupportedContentType` o parâmetro de `false`configuração como:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Para alguns BLOBs, Azure Search não é capaz de determinar o tipo de conteúdo ou não é possível processar um documento de tipo de conteúdo com suporte de outra forma. Para ignorar esse modo de falha, defina `failOnUnprocessableDocument` o parâmetro de configuração como false:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Azure Search limita o tamanho dos BLOBs que são indexados. Esses limites são documentados em [limites de serviço no Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). BLOBs superdimensionados são tratados como erros por padrão. No entanto, você ainda poderá indexar metadados de armazenamento de BLOBs superdimensionados se definir `indexStorageMetadataOnlyForOversizedDocuments` o parâmetro de configuração como true: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Você também pode continuar a indexação se ocorrerem erros em qualquer ponto de processamento, durante a análise de BLOBs ou ao adicionar documentos a um índice. Para ignorar um número específico de erros, defina os `maxFailedItems` parâmetros `maxFailedItemsPerBatch` de configuração e para os valores desejados. Por exemplo:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Indexação incremental e detecção de exclusão
Quando você configura um indexador de BLOB para ser executado em um agendamento, ele reindexa apenas os BLOBs alterados, conforme determinado pelo carimbo de data `LastModified` /hora do blob.

> [!NOTE]
> Você não precisa especificar uma política de detecção de alterações – a indexação incremental é habilitada para você automaticamente.

Para dar suporte à exclusão de documentos, use uma abordagem de "exclusão reversível". Se você excluir os BLOBs imediatamente, os documentos correspondentes não serão removidos do índice de pesquisa. Em vez disso, use as seguintes etapas:  

1. Adicione uma propriedade de metadados personalizada ao blob para indicar a Azure Search que ele foi excluído logicamente
2. Configurar uma política de detecção de exclusão reversível na fonte de dados
3. Depois que o indexador tiver processado o blob (conforme mostrado pela API de status do indexador), você poderá excluir fisicamente o blob

Por exemplo, a política a seguir considera um blob a ser excluído se ele tiver uma propriedade `IsDeleted` de metadados com `true`o valor:

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

## <a name="indexing-large-datasets"></a>Indexando conjuntos de grandes volumes

Os blobs de indexação podem ser um processo demorado. Nos casos em que você tem milhões de BLOBs para indexar, é possível acelerar a indexação Particionando seus dados e usando vários indexadores para processar os dados em paralelo. Veja como você pode configurar isso:

- Particione seus dados em vários contêineres de BLOB ou pastas virtuais
- Configure várias Azure Search fontes de dados, uma por contêiner ou pasta. Para apontar para uma pasta de BLOB, use `query` o parâmetro:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Crie um indexador correspondente para cada fonte de dados. Todos os indexadores podem apontar para o mesmo índice de pesquisa de destino.  

- Uma unidade de pesquisa em seu serviço pode executar um indexador em um determinado momento. A criação de vários indexadores, conforme descrito acima, só será útil se eles realmente forem executados em paralelo. Para executar vários indexadores em paralelo, escale horizontalmente o serviço de pesquisa criando um número apropriado de partições e réplicas. Por exemplo, se o serviço de pesquisa tiver 6 unidades de pesquisa (por exemplo, 2 partições x 3 réplicas), seis indexadores poderão ser executados simultaneamente, resultando em um aumento de seis vezes na taxa de transferência de indexação. Para saber mais sobre dimensionamento e planejamento de capacidade, consulte [dimensionar níveis de recursos para cargas de trabalho de consulta e indexação em Azure Search](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indexação de documentos juntamente com os dados relacionados

Talvez você queira "montar" documentos de várias fontes no índice. Por exemplo, talvez você queira mesclar texto de BLOBs com outros metadados armazenados no Cosmos DB. Você pode até mesmo usar a API de indexação por push junto com vários indexadores para criar documentos de pesquisa de várias partes. 

Para que isso funcione, todos os indexadores e outros componentes precisam concordar com a chave do documento. Para obter um passo a passo detalhado, consulte este artigo externo: [Combine documentos com outros dados no Azure Search](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indexação de texto sem formatação 

Se todos os seus BLOBs contiverem texto sem formatação na mesma codificação, você poderá melhorar significativamente o desempenho de indexação usando o **modo de análise de texto**. Para usar o modo de análise de texto, `parsingMode` defina a propriedade `text`de configuração como:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Por padrão, a `UTF-8` codificação é assumida. Para especificar uma codificação diferente, use a `encoding` propriedade de configuração: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Propriedades de metadados específicas do tipo de conteúdo
A tabela a seguir resume o processamento feito para cada formato de documento e descreve as propriedades de metadados extraídas por Azure Search.

| Formato de documento/tipo de conteúdo | Propriedades de metadados específicas do tipo de conteúdo | Detalhes do processamento |
| --- | --- | --- |
| HTML (texto/HTML) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Remover marcação HTML e extrair texto |
| PDF (aplicativo/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extrair texto, incluindo documentos inseridos (excluindo imagens) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrair texto, incluindo documentos inseridos |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrair texto, incluindo documentos inseridos |
| DOCM (aplicativo/vnd. MS-Word. Document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrair texto, incluindo documentos inseridos |
| XML do WORD (application/vnd. ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Dividir marcação XML e extrair texto |
| WORD 2003 XML (application/vnd. ms-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Dividir marcação XML e extrair texto |
| XLSX (application/vnd. openxmlformats-officeDocument. SpreadsheetML. Sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrair texto, incluindo documentos inseridos |
| XLS (application/vnd. MS-Excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrair texto, incluindo documentos inseridos |
| XLSM (application/vnd. MS-Excel. Sheet. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrair texto, incluindo documentos inseridos |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrair texto, incluindo documentos inseridos |
| PPT (application/vnd. ms-PowerPoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrair texto, incluindo documentos inseridos |
| PPTM (application/vnd. ms-PowerPoint. Presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrair texto, incluindo documentos inseridos |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrair texto, incluindo anexos |
| ODT (application/vnd. Oasis. OpenDocument. Text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrair texto, incluindo documentos inseridos |
| ODS (application/vnd. Oasis. OpenDocument. Spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrair texto, incluindo documentos inseridos |
| ODP (application/vnd. Oasis. OpenDocument. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Extrair texto, incluindo documentos inseridos |
| ZIP (aplicativo/zip) |`metadata_content_type` |Extrair texto de todos os documentos no arquivo |
| GZ (aplicativo/gzip) |`metadata_content_type` |Extrair texto de todos os documentos no arquivo |
| EPUB (Application/ePub + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Extrair texto de todos os documentos no arquivo |
| XML (aplicativo/XML) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Dividir marcação XML e extrair texto |
| JSON (aplicativo/JSON) |`metadata_content_type`<br/>`metadata_content_encoding` |Extrair texto<br/>NOTA: Se você precisar extrair vários campos de documento de um blob JSON, consulte [indexando BLOBs JSON](search-howto-index-json-blobs.md) para obter detalhes |
| EML (mensagem/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extrair texto, incluindo anexos |
| RTF (aplicativo/RTF) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Extrair texto|
| Texto sem formatação (texto/simples) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Extrair texto|


## <a name="help-us-make-azure-search-better"></a>Ajude-nos a tornar Azure Search melhor
Se você tiver solicitações de recursos ou ideias para melhorias, informe-nos em nosso [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
