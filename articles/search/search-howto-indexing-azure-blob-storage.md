---
title: Indexar conteúdo de armazenamento de Blobs do Azure para pesquisa em texto completo - Azure Search
description: Saiba como indexar o Blob Storage do Azure e extrair texto de documentos com o Azure Search.
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 56afc933eff2e06df270b17403b767ebaddd4154
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873003"
---
# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Indexar documentos no armazenamento de Blobs do Azure com o Azure Search
Este artigo mostra como utilizar o Azure Search para documentos de índice (como PDFs, documentos do Microsoft Office e diversos outros formatos comuns) armazenados no armazenamento de Blobs do Azure. Em primeiro lugar, ele explica as noções básicas de definir e configurar um indexador de Blobs. Em seguida, ele oferece uma discussão mais aprofundada dos comportamentos e cenários que tem probabilidade de encontrar.

## <a name="supported-document-formats"></a>Formatos de documento suportados
O indexador blob pode extrair texto de formatos de documento seguinte:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Configurar a indexação de BLOBs
Pode configurar um indexador do armazenamento de Blobs do Azure utilizando:

* [Portal do Azure](https://ms.portal.azure.com)
* O Azure Search [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* O Azure Search [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Algumas funcionalidades (por exemplo, mapeamentos de campo) ainda não estão disponíveis no portal e têm de ser utilizadas por meio de programação.
>

Aqui, vamos demonstrar o fluxo com a API REST.

### <a name="step-1-create-a-data-source"></a>Passo 1: Criar uma origem de dados
Uma origem de dados especifica os dados a indexar, as credenciais necessárias para aceder a dados e as políticas de forma eficiente identificar alterações nos dados (novas, modificadas ou eliminadas linhas). Uma origem de dados pode ser utilizada por vários indexadores no mesmo serviço de pesquisa.

Para a indexação de BLOBs, a origem de dados tem de ter as seguintes propriedades necessárias:

* **nome** é o nome exclusivo da origem de dados dentro do seu serviço de pesquisa.
* **tipo** tem de ser `azureblob`.
* **credenciais** fornece a cadeia de ligação de conta de armazenamento como o `credentials.connectionString` parâmetro. Ver [como especificar credenciais](#Credentials) abaixo para obter detalhes.
* **contentor** Especifica um contentor na conta de armazenamento. Por predefinição, todos os blobs no contentor são recuperáveis. Se pretender apenas blobs de índice num determinado diretório virtual, pode especificar esse diretório usando o opcional **consulta** parâmetro.

Para criar uma origem de dados:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Para saber mais sobre a API de origem de dados criar, ver [criar origem de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Como especificar credenciais ####

Pode fornecer as credenciais para o contentor de BLOBs de uma das seguintes formas:

- **Cadeia de ligação da conta de armazenamento de acesso total**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`. Pode obter a cadeia de ligação do portal do Azure ao navegar para o painel de conta de armazenamento > Definições > chaves (para contas de armazenamento clássico) ou definições > chaves (para contas de armazenamento do Azure Resource Manager) de acesso.
- **Assinatura de acesso partilhado de conta de armazenamento** cadeia de ligação (SAS): `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` A SAS devem ter a lista e permissões de leitura no contentores e objetos (blobs neste caso).
-  **Assinatura de acesso partilhado do contentor**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` A SAS devem ter a lista e permissões de leitura no contentor.

Para obter mais informações sobre o armazenamento partilhado assinaturas de acesso, consulte [utilizar assinaturas de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Se utilizar credenciais SAS, terá de atualizar as credenciais da origem de dados periodicamente com assinaturas renovadas para evitar que sua expiração. Se as credenciais SAS expirarem, o indexador irá falhar com uma mensagem de erro semelhante a `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Passo 2: Criar um índice
O índice Especifica os campos num documento, atributos, e a experiência de outras construções que a pesquisa de forma.

Eis como criar um índice com um pesquisável `content` campo para armazenar os textos extraídos dos blobs:   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
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
Um indexador liga uma origem de dados com um índice de pesquisa de destino e fornece uma agenda para automatizar a atualização de dados.

Assim que tiver sido criado a origem de dados e índice, está pronto para criar o indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Este indexador será executado a cada duas horas (intervalo de agendamento está definido para "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo para "PT30M". O mais curto intervalo suportado é de 5 minutos. A agenda é opcional - se for omitido, um indexador é executado apenas uma vez, quando é criado. No entanto, pode executar uma indexador sob demanda em qualquer altura.   

Para obter mais detalhes sobre a API de indexador criar, confira [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="how-azure-search-indexes-blobs"></a>Como o Azure Search indexa os blobs

Consoante a [a configuração do indexador](#PartsOfBlobToIndex), o indexador blob pode indexar apenas os metadados de armazenamento (útil quando se preocupar apenas os metadados e não precisa de indexar o conteúdo de blobs), os metadados de armazenamento e de conteúdo, ou ambos os metadados e conteúdo textual. Por predefinição, o indexador extrai metadados e conteúdo.

> [!NOTE]
> Por predefinição, os blobs com conteúdo estruturado como JSON ou CSV são indexadas como um único segmento de texto. Se quiser indexar blobs CSV e JSON de uma forma estruturada, veja [blobs JSON de indexação](search-howto-index-json-blobs.md) e [blobs CSV de indexação](search-howto-index-csv-blobs.md) funcionalidades de pré-visualização.
>
> Um documento composto ou incorporado (como um arquivo ZIP ou um documento do Word com o embedded e-mail do Outlook que contém anexos) também é indexado como um único documento.

* O conteúdo textual do documento é extraído para um campo de cadeia de caracteres chamado `content`.

> [!NOTE]
> O Azure Search limita a quantidade texto extrai consoante o escalão de preço: 32.000 caracteres gratuitamente da camada, 64.000 para o básico e milhões de 4 para os escalões Standard, Standard S2 e Standard S3. Um aviso está incluído na resposta de estado de indexador para documentos truncados.  

* Propriedades de metadados especificado pelo utilizador presentes no blob, caso existam, foram extraídas literalmente.
* Propriedades de metadados de BLOBs padrão são extraídas para os seguintes campos:

  * **Metadados\_armazenamento\_nome** (EDM) - o nome de ficheiro do blob. Por exemplo, se tiver um /my-container/my-folder/subfolder/resume.pdf blob, o valor deste campo é `resume.pdf`.
  * **Metadados\_armazenamento\_caminho** (EDM) - o URI completo do blob, incluindo a conta de armazenamento. Por exemplo, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **Metadados\_armazenamento\_conteúdo\_tipo** (EDM) - tipo como especificado pelo código usado para carregar o blob de conteúdo. Por exemplo, `application/octet-stream`.
  * **Metadados\_armazenamento\_último\_modificado** (Edm.DateTimeOffset) - última modificação timestamp para o blob. O Azure Search utiliza este timestamp para identificar blobs alterados, para evitar a reindexação tudo após a indexação inicial.
  * **Metadados\_armazenamento\_tamanho** (Edm.Int64) - tamanho em bytes do blob.
  * **Metadados\_armazenamento\_conteúdo\_md5** (EDM) - hash MD5 do conteúdo do blob, se disponível.
* Propriedades de metadados específicas para cada formato de documento são extraídas nos campos listados [aqui](#ContentSpecificMetadata).

Não precisa de definir os campos para todas as propriedades acima no seu índice de pesquisa - capturar apenas as propriedades que precisa para a sua aplicação.

> [!NOTE]
> Muitas vezes, os nomes de campos no seu índice existente será diferentes dos nomes de campos gerados durante a extração do documento. Pode usar **mapeamentos de campo** para mapear os nomes das propriedades fornecidas pelo Azure Search para os nomes de campos no seu índice de pesquisa. Verá um exemplo de campo mapeamentos utilizam abaixo.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definir chaves de documento e mapeamentos de campo
No Azure Search, a chave do documento identifica exclusivamente um documento. Todos os índices de pesquisa tem de ter exatamente um campo de chave do tipo EDM. O campo de chave é necessário para cada documento que está a ser adicionado ao índice (é, na verdade, o único campo obrigatório).  

Deve considerar cuidadosamente qual campo extraído deve ser mapeada para o campo de chave para o seu índice. Os candidatos são:

* **Metadados\_armazenamento\_nome** - isso pode ser um candidato conveniente, mas tenha em atenção que 1) os nomes podem não ser exclusivos, como pode ter blobs com o mesmo nome em pastas diferentes e 2) o nome pode conter carateres que são inválido nas chaves de documento, como travessões. Pode lidar com carateres inválidos ao utilizar o `base64Encode` [função de mapeamento de campo](search-indexer-field-mappings.md#base64EncodeFunction) – se fizer isso, não se esqueça de codificar chaves de documento ao passá-las na API de chamadas, como pesquisa. (Por exemplo, no .NET pode utilizar o [UrlTokenEncode método](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) para essa finalidade).
* **Metadados\_armazenamento\_caminho** – utilizar o caminho completo garante a exclusividade, mas definitivamente contém o caminho `/` carateres que são [inválido numa chave de documento](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Conforme apresentado acima, tem a opção de codificação de chaves utilizando o `base64Encode` [função](search-indexer-field-mappings.md#base64EncodeFunction).
* Se nenhuma das opções acima trabalharem por si, pode adicionar uma propriedade de metadados personalizados para os blobs. Esta opção exige, no entanto, o processo de carregamento de BLOBs para adicionar essa propriedade de metadados para todos os blobs. Uma vez que a chave é uma propriedade necessária, todos os blobs que não têm essa propriedade serão não ser indexados.

> [!IMPORTANT]
> Se não houver nenhum mapeamento explícito para o campo de chave no índice, Azure Search utiliza automaticamente `metadata_storage_path` como a chave e a base 64 codifica valores de chave (a segunda opção acima).
>
>

Neste exemplo, vamos escolher o `metadata_storage_name` campo como a chave do documento. Vamos também assumir que o seu índice tem um campo de chave com o nome `key` e um campo `fileSize` para armazenar o tamanho do documento. Para conectar as coisas como pretendido, especifique os seguintes mapeamentos de campo quando criar ou atualizar o indexador:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Para colocar isso em conjunto, eis como pode adicionar mapeamentos de campo e ativar a codificação base 64 de chaves para um indexador existente:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2017-11-11
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
> Para saber mais sobre os mapeamentos de campo, veja [este artigo](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Controlar os blobs são indexados
Pode controlar quais blobs são indexados e que é ignorado.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Apenas os blobs com extensões de ficheiro específicas de índice
Permite-lhe indexar apenas os blobs com as extensões de nome de ficheiro especificado usando a `indexedFileNameExtensions` parâmetro de configuração do indexador. O valor é uma cadeia de caracteres que contém uma lista separada por vírgulas das extensões de ficheiro (com um ponto). Por exemplo, para o índice apenas o. PDF e. Blobs DOCX, fazer o seguinte:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Excluir blobs com extensões de ficheiro específicas
Pode excluir blobs com extensões de nome de ficheiro específicas de indexação ao utilizar o `excludedFileNameExtensions` parâmetro de configuração. O valor é uma cadeia de caracteres que contém uma lista separada por vírgulas das extensões de ficheiro (com um ponto). Por exemplo, para o índice de todos os blobs, exceto aqueles com o. PNG e. Extensões JPEG, fazer o seguinte:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Se os dois `indexedFileNameExtensions` e `excludedFileNameExtensions` parâmetros estão presentes, a Azure Search primeiro analisa `indexedFileNameExtensions`, em seguida, em `excludedFileNameExtensions`. Isso significa que se a mesma extensão de ficheiro está presente em duas listas, será excluído da indexação.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Controlar quais partes do blob são indexadas

Pode controlar quais partes dos blobs são indexados com o `dataToExtract` parâmetro de configuração. Ele pode ter os seguintes valores:

* `storageMetadata` -Especifica que apenas os [propriedades de BLOBs padrão e metadados especificado pelo utilizador](../storage/blobs/storage-properties-metadata.md) são indexados.
* `allMetadata` -Especifica esses metadados de armazenamento e o [metadados específicos do tipo de conteúdo](#ContentSpecificMetadata) extraiu o blob do conteúdo são indexados.
* `contentAndMetadata` -Especifica que todos os metadados e conteúdo textual extraídos a partir do blob são indexados. Este é o valor predefinido.

Por exemplo, para indexar apenas os metadados de armazenamento, utilize:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Utilizando os metadados de blob para controlar a forma como os blobs são indexados

Os parâmetros de configuração descritos acima aplicam-se para todos os blobs. Às vezes, poderá querer controle como *blobs individuais* são indexados. Pode fazê-lo ao adicionar as seguintes propriedades de metadados do blob e os valores:

| Nome da propriedade | Valor da propriedade | Explicação |
| --- | --- | --- |
| AzureSearch_Skip |"true" |Instrui o indexador de BLOBs para ignorar completamente o blob. Extração nem metadados nem o conteúdo é tentada. Isto é útil quando um determinado blob falha repetidamente e interrompe o processo de indexação. |
| AzureSearch_SkipContent |"true" |Este é o equivalente `"dataToExtract" : "allMetadata"` definição descrito [acima](#PartsOfBlobToIndex) no âmbito determinado blob. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Lidar com erros

Por predefinição, o indexador blob será interrompido assim que ele encontra um blob com um tipo de conteúdo não suportado (por exemplo, uma imagem). Certamente pode usar o `excludedFileNameExtensions` parâmetro para ignorar determinados tipos de conteúdo. No entanto, poderá ter para blobs de índice sem saber com antecedência os possíveis tipos de conteúdo. Para continuar a indexação quando é encontrado um tipo de conteúdo não suportado, defina o `failOnUnsupportedContentType` parâmetro de configuração para `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Para alguns blobs, do Azure Search não consegue determinar o tipo de conteúdo ou não é possível processar um documento de outra forma suportada tipo de conteúdo. Para ignorar este modo de falha, defina o `failOnUnprocessableDocument` parâmetro de configuração como false:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

O Azure Search limita o tamanho dos blobs são indexados. Estes limites estão documentados em [limites de serviço do Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Blobs demasiado grandes são tratados como erros por predefinição. No entanto, pode ainda indexar os metadados de armazenamento de blobs demasiado grandes se definir `indexStorageMetadataOnlyForOversizedDocuments` parâmetro de configuração como true: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Também pode continuar a indexação se acontecem de erros em qualquer ponto de processamento, durante a análise de blobs ou ao adicionar documentos para um índice. Para ignorar um determinado número de erros, defina o `maxFailedItems` e `maxFailedItemsPerBatch` parâmetros de configuração para os valores pretendidos. Por exemplo:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Deteção de indexação e a eliminação incremental
Quando configurar um indexador de BLOBs para ser executada com base numa agenda, ele reindexes apenas os blobs alterados, conforme determinado do blob `LastModified` timestamp.

> [!NOTE]
> Não tem de especificar uma política de deteção de alteração – a indexação incremental é ativado automaticamente para.

Para suportar a eliminação de documentos, utilize uma abordagem de "eliminação de forma recuperável". Se eliminar os blobs imediatamente, documentos correspondentes não serão removidos do índice de pesquisa. Em vez disso, utilize os seguintes passos:  

1. Adicionar uma propriedade de metadados personalizados para o blob para indicar ao Azure Search, que é logicamente eliminado
2. Configurar uma política de deteção de eliminação de forma recuperável na origem de dados
3. Depois do indexador processou o blob (conforme exibido o status de indexador API), pode eliminar fisicamente o blob

Por exemplo, a seguinte política considera um blob para ser excluído se ele tem uma propriedade de metadados `IsDeleted` com o valor `true`:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2017-11-11
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

Indexar blobs, pode ser um processo demorado. Em casos em que tem milhões de blobs para indexar, pode acelerar indexação seus dados de criação de partições e usando vários indexadores para processar os dados em paralelo. Eis como pode configurar isto:

- Particionar os dados em vários contentores de BLOBs ou pastas virtual
- Configure várias origens de dados do Azure Search, um por pasta ou do contentor. Para apontar para uma pasta de BLOBs, utilize o `query` parâmetro:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Crie um indexador correspondente para cada origem de dados. Todos os indexadores podem apontar para o mesmo índice de pesquisa de destino.  

- Uma unidade de pesquisa no seu serviço pode executar um indexador em qualquer momento. Criar vários indexadores, conforme descrito acima só será útil se, na verdade, execute em paralelo. Para executar vários indexadores em paralelo, ampliar o seu serviço de pesquisa através da criação de um número adequado de réplicas e partições. Por exemplo, se o serviço de pesquisa tiver 6 unidades de pesquisa (por exemplo, as réplicas 2 partições x 3), em seguida, 6 indexadores podem executar em simultâneo, resultando num six-fold aumento no débito de indexação. Para saber mais sobre o planejamento de capacidade e dimensionamento, veja [Dimensionar níveis de recursos para consulta e indexação de cargas de trabalho no Azure Search](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indexar documentos juntamente com dados relacionados

Pode querer "montar" documentos de várias origens no seu índice. Por exemplo, pode querer mesclar o texto de blobs com outros metadados armazenados no Cosmos DB. Pode até mesmo usar push indexação API em conjunto com vários indexadores para criar documentos de pesquisa a partir de várias partes. 

Para isso funcionar, todos os indexadores e outros componentes devem concordar quanto a chave do documento. Para instruções detalhadas, consulte este artigo externo: [Combinar documentos com outros dados no Azure Search](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indexação de sem formatação texto 

Se todos os blobs contiverem texto simples na mesma codificação, pode significativamente melhorar desempenho indexação utilizando **modo de análise de texto**. Para utilizar o modo de análise de texto, defina o `parsingMode` propriedade de configuração para `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Por predefinição, o `UTF-8` codificação pressupõe-se. Para especificar uma codificação diferente, utilize o `encoding` propriedade de configuração: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Propriedades de metadados específicos do tipo de conteúdo
A tabela seguinte resume um processamento para cada formato de documento e descreve as propriedades de metadados extraídas pelo Azure Search.

| Formato de documento / tipo de conteúdo | Propriedades de metadados específicos do tipo de conteúdo | Detalhes de processamento |
| --- | --- | --- |
| HTML (`text/html`) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Marcação HTML de faixa e extrair texto |
| PDF (`application/pdf`) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extrair texto, incluindo documentos do embedded (excluindo as imagens) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrair texto, incluindo documentos do embedded |
| DOCUMENTO (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrair texto, incluindo documentos do embedded |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrair texto, incluindo documentos do embedded |
| XLS (application/vnd.ms-do excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrair texto, incluindo documentos do embedded |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrair texto, incluindo documentos do embedded |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrair texto, incluindo documentos do embedded |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrair texto, incluindo anexos |
| ZIP (application/zip) |`metadata_content_type` |Extrair texto de todos os documentos no arquivo morto |
| XML (application/xml) |`metadata_content_type`</br>`metadata_content_encoding`</br> |Marcação XML de faixa e extrair texto |
| JSON (application/json) |`metadata_content_type`</br>`metadata_content_encoding` |Extrair texto<br/>NOTA: Se precisar de extrair vários campos de documentos de um blob JSON, veja [blobs JSON de indexação](search-howto-index-json-blobs.md) para obter detalhes |
| EML (mensagens/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extrair texto, incluindo anexos |
| RTF (application/rtf) |`metadata_content_type`</br>`metadata_author`</br>`metadata_character_count`</br>`metadata_creation_date`</br>`metadata_page_count`</br>`metadata_word_count`</br> | Extrair texto|
| Texto sem formatação (text/plain) |`metadata_content_type`</br>`metadata_content_encoding`</br> | Extrair texto|


## <a name="help-us-make-azure-search-better"></a>Ajude-na melhorar o Azure Search
Se tiver pedidos de funcionalidades ou ideias para melhorias, fale na nossa [site do UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
