---
title: Configurar um indexador Blob
titleSuffix: Azure Cognitive Search
description: Crie um indexador Azure Blob para automatizar a indexação do conteúdo blob para operações completas de pesquisa de texto na Pesquisa Cognitiva Azure.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 74813fabec4d5fe43cd158bb4aa359c2a3b0188a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988719"
---
# <a name="how-to-configure-blob-indexing-in-cognitive-search"></a>Como configurar a indexação do blob na Pesquisa Cognitiva

Este artigo mostra-lhe como configurar um indexante blob para indexar documentos baseados em texto (tais como PDFs, documentos do Microsoft Office, entre outros) na Azure Cognitive Search. Se não estiver familiarizado com os conceitos indexantes, comece com [indexers em Azure Cognitive Search](search-indexer-overview.md) e [Crie um indexante de pesquisa](search-howto-create-indexers.md) antes de mergulhar na indexação de blob.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Formatos de documento suportados

O indexante de blob de pesquisa cognitiva Azure pode extrair texto dos seguintes formatos de documento:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="data-source-definitions"></a>Definições de fonte de dados

A diferença entre um indexante blob e qualquer outro indexante é a definição de fonte de dados que é atribuída ao indexante. A fonte de dados encapsula todas as propriedades que especificam o tipo, ligação e localização do conteúdo a indexar.

Uma definição de fonte de dados blob é semelhante ao exemplo abaixo:

```http
{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}
```

A `"credentials"` propriedade pode ser uma cadeia de conexão, como mostrado no exemplo acima, ou uma das abordagens alternativas descritas na secção seguinte. A `"container"` propriedade fornece a localização do conteúdo dentro do Azure Storage, e é usado para `"query"` especificar uma sub-dobradura no recipiente. Para obter mais informações sobre definições de fonte de dados, consulte [Criar Fonte de Dados (REST)](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>

## <a name="credentials"></a>Credenciais

Pode fornecer as credenciais para o recipiente blob de uma destas formas:

**Cadeia de ligação de identidade gerida:**`{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

Esta cadeia de ligação não requer uma chave de conta, mas deve seguir as instruções para [configurar uma ligação a uma conta de Armazenamento Azure utilizando uma identidade gerida](search-howto-managed-identities-storage.md).

**Cadeia de ligação de conta de armazenamento de acesso completo:**`{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

Pode obter o fio de ligação a partir do portal Azure navegando na lâmina da conta de armazenamento > Definições > Chaves (para contas de armazenamento clássico) ou Definições > Teclas de acesso (para contas de armazenamento do Gestor de Recursos Azure).

Cadeia de conexão de assinatura de acesso partilhado (SAS) de **conta de armazenamento:**`{ "connectionString" : "BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl;" }`

O SAS deve ter a lista e ler permissões em recipientes e objetos (bolhas neste caso).

**Assinatura de acesso partilhado do contentor:**`{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

O SAS deve ter a lista e ler permissões no recipiente. Para obter mais informações sobre as assinaturas de acesso partilhado de armazenamento, consulte [usando assinaturas de acesso partilhado.](../storage/common/storage-sas-overview.md)

> [!NOTE]
> Se utilizar credenciais SAS, terá de atualizar periodicamente as credenciais de origem de dados com assinaturas renovadas para evitar a sua expiração. Se as credenciais SAS expirarem, o indexante falhará com uma mensagem de erro semelhante a "As credenciais fornecidas na cadeia de ligação são inválidas ou caducaram".  

## <a name="index-definitions"></a>Definições de índice

O índice especifica os campos num documento, atributos e outras construções que moldam a experiência de pesquisa. O exemplo a seguir cria um índice simples utilizando o [Índice de Criação (REST API)](/rest/api/searchservice/create-index). 

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
      "name" : "my-target-index",
      "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
      ]
}
```

As definições de índice requerem que um campo na `"fields"` coleção atue como a chave do documento. As definições de índice também devem incluir campos para conteúdo e metadados.

Um **`content`** campo é utilizado para armazenar o texto extraído das bolhas. Sua definição deste campo pode parecer semelhante à acima. Não é obrigado a usar este nome, mas fazê-lo permite-lhe tirar partido de mapeamentos de campo implícitos. O indexante blob pode enviar conteúdo blob para um conteúdo Edm.String field no índice, sem necessidade de mapeamentos de campo.

Também pode adicionar campos para quaisquer metadados blob que queira no índice. O indexante pode ler propriedades de metadados personalizados, propriedades [de metadados padrão](#indexing-blob-metadata) e propriedades [de metadados específicos do conteúdo.](search-blob-metadata-properties.md) Para obter mais informações sobre índices, consulte [Criar um índice](search-what-is-an-index.md).

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>Definição de chaves de documento e mapeamentos de campo

Num índice de pesquisa, a chave do documento identifica exclusivamente cada documento. O campo que escolher deve ser do `Edm.String` tipo. Para o conteúdo blob, os melhores candidatos para uma chave de documento são propriedades de metadados na bolha.

+ **`metadata_storage_name`** - esta propriedade é um candidato, mas apenas se os nomes forem únicos em todos os contentores e pastas que você está indexando. Independentemente da localização do blob, o resultado final é que a chave do documento (nome) deve ser única no índice de pesquisa depois de todo o conteúdo ter sido indexado. 

  Outra questão potencial sobre o nome de armazenamento é que pode conter caracteres que são inválidos para chaves de documento, como traços. Pode manusear caracteres inválidos utilizando a `base64Encode` [função de mapeamento de campo](search-indexer-field-mappings.md#base64EncodeFunction). Se o fizer, lembre-se de codificar também as chaves do documento ao passá-las em chamadas API como [o Lookup Document (REST)](/rest/api/searchservice/lookup-document). Em .NET, pode utilizar o [método UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) para codificar caracteres.

+ **`metadata_storage_path`**- a utilização do caminho completo garante a singularidade, mas o caminho contém definitivamente `/` caracteres que são [inválidos numa chave de documento.](/rest/api/searchservice/naming-rules) Como acima, pode utilizar a `base64Encode` [função](search-indexer-field-mappings.md#base64EncodeFunction) para codificar caracteres.

+ Uma terceira opção é adicionar uma propriedade de metadados personalizados às bolhas. Esta opção requer que o seu processo de upload de blob adicione essa propriedade de metadados a todas as bolhas. Uma vez que a chave é uma propriedade necessária, quaisquer bolhas que faltem a um valor não serão indexadas.

> [!IMPORTANT]
> Se não houver um mapeamento explícito para o campo chave no índice, a Azure Cognitive Search utiliza automaticamente `metadata_storage_path` como chave e a base-64 codifica valores-chave (a segunda opção acima).
>

#### <a name="example"></a>Exemplo

O exemplo que se segue demonstra `metadata_storage_name` como a chave do documento. Assuma que o índice tem um campo chave nomeado `key` e outro campo nomeado para armazenar o tamanho do `fileSize` documento. [Os mapeamentos](search-indexer-field-mappings.md) de campo na definição do indexante estabelecem associações de campo, e `metadata_storage_name` tem a [ `base64Encode` função de mapeamento](search-indexer-field-mappings.md#base64EncodeFunction) de campo para lidar com caracteres não suportados.

```http
PUT https://[service name].search.windows.net/indexers/my-blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : "my-blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
  ]
}
```

#### <a name="how-to-make-an-encoded-field-searchable"></a>Como tornar um campo codificado "pesmável"

Há momentos em que é necessário utilizar uma versão codificada de um campo `metadata_storage_path` como a chave, mas também precisa que esse campo seja pesquisável (sem codificar) no índice de pesquisa. Para suportar ambos os casos de uso, pode mapear `metadata_storage_path` dois campos; um para a chave (codificado) e um segundo para um campo de caminho que podemos assumir que é atribuído como "pesmável" no esquema de índice. O exemplo abaixo mostra dois mapeamentos de campo para `metadata_storage_path` .

```http
PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : " blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
  ]
}
```

<a name="PartsOfBlobToIndex"></a>

## <a name="index-content-and-metadata"></a>Conteúdo de índice e metadados

As bolhas contêm conteúdo e metadados. Pode controlar quais as partes das bolhas indexadas utilizando o `dataToExtract` parâmetro de configuração. Pode assumir os seguintes valores:

+ `contentAndMetadata` - especifica que todos os metadados e conteúdos texuais extraídos da bolha estão indexados. Este é o valor predefinido.

+ `storageMetadata` - especifica que apenas as [propriedades padrão blob e metadados especificados pelo utilizador](../storage/blobs/storage-blob-container-properties-metadata.md) estão indexados.

+ `allMetadata` - especifica que as propriedades blob padrão e quaisquer [metadados para tipos](search-blob-metadata-properties.md) de conteúdo encontrados são extraídos do conteúdo blob e indexados.

Por exemplo, para indexar apenas os metadados de armazenamento, utilize:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
}
```

<a name="how-azure-search-indexes-blobs"></a>

### <a name="indexing-blob-content"></a>Indexação do conteúdo do blob

Por predefinição, as bolhas com conteúdo estruturado, como JSON ou CSV, são indexadas como um único pedaço de texto. Mas se os documentos JSON ou CSV tiverem uma estrutura interna (delimiters), pode atribuir modos de análise para gerar documentos de pesquisa individuais para cada linha ou elemento. Para obter mais informações, consulte [as bolhas JSON indexantes](search-howto-index-json-blobs.md) e [as bolhas de CSV indexante .](search-howto-index-csv-blobs.md)

Um documento composto ou incorporado (como um arquivo ZIP, um documento word com e-mail do Outlook incorporado contendo anexos, ou um . O ficheiro MSG com anexos) também é indexado como um único documento. Por exemplo, todas as imagens extraídas dos anexos de um . O ficheiro MSG será devolvido no campo normalized_images.

O conteúdo textual do documento é extraído num campo de cordas denominado `content` .

  > [!NOTE]
  > A Azure Cognitive Search limita a quantidade de texto que extrai dependendo do nível de preços. Os [limites](search-limits-quotas-capacity.md#indexer-limits) de serviço atuais são 32.000 caracteres para free tier, 64.000 para Basic, 4 milhões para Standard, 8 milhões para Standard S2 e 16 milhões para Standard S3. Uma advertência está incluída na resposta do estado do indexante para documentos truncados.  

<a name="indexing-blob-metadata"></a>

### <a name="indexing-blob-metadata"></a>Indexação de metadados blob

Os indexantes também podem indexar metadados blob. Em primeiro lugar, quaisquer propriedades de metadados especificados pelo utilizador podem ser extraídos verbatim. Para receber os valores, tem de definir o campo no índice de pesquisa do `Edm.String` tipo, com o mesmo nome que a tecla de metadados da bolha. Por exemplo, se uma bolha tiver uma chave de metadados `Sensitivity` com `High` valor, deverá definir um campo nomeado `Sensitivity` no seu índice de pesquisa e será povoado com o valor `High` .

Em segundo lugar, as propriedades de metadados blob padrão podem ser extraídas nos campos listados abaixo. O indexante blob cria automaticamente mapeamentos de campo internos para estas propriedades de metadados blob. Ainda tem de adicionar os campos que pretende utilizar a definição de índice, mas pode omitir a criação de mapeamentos de campo no indexante.

  + **metadata_storage_name** o `Edm.String` nome do ficheiro da bolha. Por exemplo, se tiver uma bolha /meu-recipiente/minha pasta/sub-dobra/resume.pdf, o valor deste campo é `resume.pdf` .

  + **metadata_storage_path** ( `Edm.String` ) - o URI completo da bolha, incluindo a conta de armazenamento. Por exemplo, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  + **metadata_storage_content_type** ( `Edm.String` ) - tipo de conteúdo especificado pelo código utilizado para carregar a bolha. Por exemplo, `application/octet-stream`.

  + **metadata_storage_last_modified** ( `Edm.DateTimeOffset` ) - última estampada modificada para a bolha. A Azure Cognitive Search usa esta estampação de tempo para identificar bolhas alteradas, para evitar reindexar tudo após a indexação inicial.

  + **metadata_storage_size** ( `Edm.Int64` ) - tamanho de bolha em bytes.

  + **metadata_storage_content_md5** ( `Edm.String` ) - MD5 hash do conteúdo do blob, se disponível.

  + **metadata_storage_sas_token** ( `Edm.String` ) - Um símbolo SAS temporário que pode ser usado por [habilidades personalizadas](cognitive-search-custom-skill-interface.md) para ter acesso à bolha. Este token não deve ser guardado para utilização posterior, uma vez que pode expirar.

Por último, quaisquer propriedades de metadados específicas do formato documental das bolhas que está a indexar também podem ser representadas no esquema de índice. Para obter mais informações sobre metadados específicos de conteúdo, consulte [as propriedades dos metadados de conteúdo.](search-blob-metadata-properties.md)

É importante salientar que não precisa de definir campos para todas as propriedades acima no seu índice de pesquisa - basta capturar as propriedades que precisa para a sua aplicação.

<a name="WhichBlobsAreIndexed"></a>

## <a name="how-to-control-which-blobs-are-indexed"></a>Como controlar quais as bolhas indexadas

Pode controlar quais as bolhas indexadas, e que são ignoradas, pelo tipo de ficheiro da bolha ou por definir propriedades na própria bolha, fazendo com que o indexante salte por cima deles.

### <a name="include-specific-file-extensions"></a>Incluir extensões de ficheiros específicas

Utilize `indexedFileNameExtensions` para fornecer uma lista separada de vírgulas de extensões de ficheiros para indexar (com um ponto líder). Por exemplo, para indexar apenas o . PDF e . Bolhas DOCX, faça isto:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-specific-file-extensions"></a>Excluir extensões de ficheiros específicas

Utilize `excludedFileNameExtensions` para fornecer uma lista separada por vírgula de extensões de ficheiros para saltar (novamente, com um ponto de liderança). Por exemplo, para indexar todas as bolhas exceto aquelas com o . PNG e . Extensões JPEG, faça isto:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Se ambos `indexedFileNameExtensions` e `excludedFileNameExtensions` os parâmetros estiverem presentes, o indexante primeiro olha `indexedFileNameExtensions` para , em seguida, em `excludedFileNameExtensions` . Se a mesma extensão de ficheiro estiver em ambas as listas, será excluída da indexação.

### <a name="add-skip-metadata-the-blob"></a>Adicione metadados "skip" a bolha

Os parâmetros de configuração do indexante aplicam-se a todas as bolhas do recipiente ou da pasta. Às vezes, queremos controlar como *as bolhas individuais* são indexadas. Pode fazê-lo adicionando as seguintes propriedades e valores de metadados a bolhas no armazenamento blob. Quando o indexante encontrar estas propriedades, saltará a bolha ou o seu conteúdo na corrida de indexação.

| Nome da propriedade | Valor patrimonial | Explicação |
| ------------- | -------------- | ----------- |
| `AzureSearch_Skip` |`"true"` |Instrui o indexante blob a saltar completamente a bolha. Não se tenta nem metadados nem extração de conteúdo. Isto é útil quando uma bolha particular falha repetidamente e interrompe o processo de indexação. |
| `AzureSearch_SkipContent` |`"true"` |Isto equivale à `"dataToExtract" : "allMetadata"` definição [acima](#PartsOfBlobToIndex) descrita a uma determinada bolha. |

## <a name="index-large-datasets"></a>Índice de grandes conjuntos de dados

Indexar bolhas pode ser um processo demorado. Nos casos em que tenha milhões de bolhas para indexar, pode acelerar a indexação dividindo os seus dados e utilizando vários indexantes para [processar os dados em paralelo](search-howto-large-index.md#parallel-indexing). Eis como pode configurar isto:

+ Dividir os seus dados em vários recipientes blob ou pastas virtuais

+ Configurar várias fontes de dados, uma por contentor ou pasta. Para apontar para uma pasta de bolhas, utilize o `query` parâmetro:

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

+ Crie um indexante correspondente para cada fonte de dados. Todos os indexantes devem apontar para o mesmo índice de pesquisa de alvos.  

+ Uma unidade de pesquisa no seu serviço pode executar um indexante a qualquer momento. A criação de múltiplos indexantes como descrito acima só é útil se realmente funcionarem em paralelo.

  Para executar vários indexadores em paralelo, escale o seu serviço de pesquisa criando um número adequado de divisórias e réplicas. Por exemplo, se o seu serviço de pesquisa tiver 6 unidades de pesquisa (por exemplo, 2 divisórias x 3 réplicas), então 6 indexantes podem ser executados simultaneamente, resultando num aumento de seis vezes na produção de indexação. Para saber mais sobre dimensionamento e planeamento de capacidade, consulte [Ajustar a capacidade de um serviço de Pesquisa Cognitiva Azure.](search-capacity-planning.md)

<a name="DealingWithErrors"></a>

## <a name="handling-errors"></a>Processar erros

Os erros que ocorrem geralmente durante a indexação incluem tipos de conteúdo não suportados, conteúdo em falta ou bolhas de grandes dimensões.

Por predefinição, o indexante blob para assim que encontra uma bolha com um tipo de conteúdo não suportado (por exemplo, uma imagem). Pode utilizar o `excludedFileNameExtensions` parâmetro para saltar certos tipos de conteúdo. No entanto, é melhor indexar para proceder mesmo que ocorram erros e, em seguida, depurar documentos individuais mais tarde. Para obter mais informações sobre erros indexantes, consulte [problemas de resolução de problemas comuns](search-indexer-troubleshooting.md) de indexante e [erros e avisos indexantes](cognitive-search-common-errors-warnings.md).

### <a name="respond-to-errors"></a>Responder a erros

Existem quatro propriedades indexantes que controlam a resposta do indexante quando ocorrem erros. Os exemplos a seguir mostram como definir estas propriedades na definição do indexante. Se um indexante já existir, pode adicionar estas propriedades editando a definição no portal.

#### <a name="maxfaileditems-and-maxfaileditemsperbatch"></a>`"maxFailedItems"` e `"maxFailedItemsPerBatch"`

Continue a indexar se os erros ocorrerem em qualquer ponto de processamento, quer enquanto analisa as bolhas, quer adicionando documentos a um índice. Desa esta aduso destas propriedades. Um valor `-1` de processamento não importa quantos erros ocorram. Caso contrário, o valor é um inteiro positivo.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

#### <a name="failonunsupportedcontenttype-and-failonunprocessabledocument"></a>`"failOnUnsupportedContentType"` e `"failOnUnprocessableDocument"` 

Para algumas bolhas, a Azure Cognitive Search não consegue determinar o tipo de conteúdo, ou não é capaz de processar um documento de um tipo de conteúdo suportado de outra forma. Para ignorar estas condições de falha, desate os parâmetros de configuração `false` para:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="relax-indexer-constraints"></a>Relaxe as restrições do indexante

Também pode definir [propriedades de configuração blob](/rest/api/searchservice/create-indexer#blob-configuration-parameters) que determinam eficazmente se existe uma condição de erro. A propriedade a seguir pode relaxar constrangimentos, suprimindo erros que de outra forma ocorreriam.

+ `"indexStorageMetadataOnlyForOversizedDocuments"` para indexar metadados de armazenamento para conteúdo blob que é demasiado grande para processar. As bolhas de grandes dimensões são tratadas como erros por defeito. Para limites no tamanho da bolha, consulte [limites de serviço](search-limits-quotas-capacity.md).

## <a name="see-also"></a>Ver também

+ [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
+ [Criar um indexador](search-howto-create-indexers.md)
+ [Enriquecimento de IA sobre visão geral de bolhas](search-blob-ai-integration.md)
+ [Procure por blobs visão geral](search-blob-storage-integration.md)