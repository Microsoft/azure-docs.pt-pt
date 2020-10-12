---
title: Configurar um indexador Blob
titleSuffix: Azure Cognitive Search
description: Crie um indexador Azure Blob para automatizar a indexação do conteúdo blob para operações completas de pesquisa de texto na Pesquisa Cognitiva Azure.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e3419711c9a7358914f85574f6dbd5af29def1cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403620"
---
# <a name="how-to-configure-a-blob-indexer-in-azure-cognitive-search"></a>Como configurar um indexante blob na Pesquisa Cognitiva Azure

Este artigo mostra-lhe como usar a Azure Cognitive Search para indexar documentos baseados em texto (tais como PDFs, documentos do Microsoft Office e vários outros formatos comuns) armazenados no armazenamento de Azure Blob. Primeiro, explica o básico da configuração e configuração de um indexante blob. Depois, oferece uma exploração mais profunda de comportamentos e cenários que é provável que encontre.

<a name="SupportedFormats"></a>

## <a name="supported-formats"></a>Formatos suportados

O indexante blob pode extrair texto dos seguintes formatos de documento:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="set-up-blob-indexing"></a>Configurar indexação de bolhas

Pode configurar um indexador de armazenamento Azure Blob utilizando:

* [Portal do Azure](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](/rest/api/searchservice/Indexer-operations)
* Pesquisa Cognitiva Azure [.NET SDK](/dotnet/api/overview/azure/search)

> [!NOTE]
> Algumas funcionalidades (por exemplo, mapeamentos de campo) ainda não estão disponíveis no portal e têm de ser utilizadas programáticamente.
>

Aqui, demonstramos o fluxo utilizando a API REST.

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados

Uma fonte de dados especifica quais os dados a indexar, credenciais necessárias para aceder aos dados e políticas para identificar de forma eficiente as alterações nos dados (linhas novas, modificadas ou eliminadas). Uma fonte de dados pode ser usada por vários indexadores no mesmo serviço de pesquisa.

Para a indexação de bolhas, a fonte de dados deve ter as seguintes propriedades necessárias:

* **nome** é o nome único da fonte de dados dentro do seu serviço de pesquisa.
* **tipo** deve ser `azureblob` .
* **as credenciais fornecem a cadeia de ligação da conta de armazenamento como `credentials.connectionString` parâmetro. Veja [como especificar as credenciais](#Credentials) abaixo para mais detalhes.
* **o recipiente** especifica um recipiente na sua conta de armazenamento. Por predefinição, todas as bolhas dentro do recipiente são recuperáveis. Se quiser apenas indexar bolhas num determinado diretório virtual, pode especificar esse diretório utilizando o parâmetro de **consulta** opcional.

Para criar uma fonte de dados:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }
```

Para obter mais informações sobre a API de Fonte de Dados, consulte [Criar Fonte de Dados.](/rest/api/searchservice/create-data-source)

<a name="Credentials"></a>

#### <a name="how-to-specify-credentials"></a>Como especificar credenciais

Pode fornecer as credenciais para o recipiente blob de uma destas formas:

* **Cadeia de ligação de identidade gerida:**`ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` 

  Esta cadeia de ligação não requer uma chave de conta, mas deve seguir as instruções para [configurar uma ligação a uma conta de Armazenamento Azure utilizando uma identidade gerida](search-howto-managed-identities-storage.md).

* **Cadeia de ligação de conta de armazenamento de acesso completo:**`DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`

  Pode obter o fio de ligação a partir do portal Azure navegando na lâmina da conta de armazenamento > Definições > Chaves (para contas de armazenamento clássico) ou Definições > Teclas de acesso (para contas de armazenamento do Gestor de Recursos Azure).

* Cadeia de conexão de assinatura de acesso partilhado (SAS) de **conta de armazenamento:**`BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl`

  O SAS deve ter a lista e ler permissões em recipientes e objetos (bolhas neste caso).

* **Assinatura de acesso partilhado do contentor:**`ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl`

  O SAS deve ter a lista e ler permissões no recipiente.

Para obter mais informações sobre as assinaturas de acesso partilhado de armazenamento, consulte [usando assinaturas de acesso partilhado.](../storage/common/storage-sas-overview.md)

> [!NOTE]
> Se utilizar credenciais SAS, terá de atualizar periodicamente as credenciais de origem de dados com assinaturas renovadas para evitar a sua expiração. Se as credenciais SAS expirarem, o indexante falhará com uma mensagem de erro semelhante a `Credentials provided in the connection string are invalid or have expired.` .  

### <a name="step-2-create-an-index"></a>Passo 2: criar um índice

O índice especifica os campos num documento, atributos e outras construções que moldam a experiência de pesquisa.

Aqui está como criar um índice com um `content` campo pesquisável para armazenar o texto extraído de bolhas:

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

Para obter mais informações, consulte [Create Index (REST API)](/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Passo 3: Criar um indexador

Um indexante conecta uma fonte de dados com um índice de pesquisa de alvo, e fornece um horário para automatizar a atualização de dados.

Uma vez criado o índice e a fonte de dados, está pronto para criar o indexador:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Este indexante será executado de duas em duas horas (o intervalo de horário está definido para "PT2H"). Para executar um indexante a cada 30 minutos, desajuste o intervalo para "PT30M". O intervalo suportado mais curto é de 5 minutos. O horário é opcional - se omitido, um indexante funciona apenas uma vez quando é criado. No entanto, pode executar um indexante a qualquer momento.   

Para obter mais informações, consulte [Create Indexer (REST API)](/rest/api/searchservice/create-indexer). Para obter mais informações sobre a definição de horários de indexantes, consulte [Como agendar indexadores para a Pesquisa Cognitiva do Azure](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-blobs-are-indexed"></a>Como as bolhas são indexadas

Dependendo da configuração do [indexante,](#PartsOfBlobToIndex)o indexante blob só pode indexar metadados de armazenamento (úteis quando se preocupa apenas com os metadados e não precisa de indexar o conteúdo das bolhas), armazenamento e metadados de conteúdo, ou tanto metadados como conteúdo textual. Por padrão, o indexante extrai tanto metadados como conteúdo.

> [!NOTE]
> Por predefinição, as bolhas com conteúdo estruturado, tais como JSON ou CSV, são indexadas como um único pedaço de texto. Se pretender indexar as bolhas JSON e CSV de forma estruturada, consulte [as bolhas JSON indexantes](search-howto-index-json-blobs.md) e [as bolhas de CSV indexantes](search-howto-index-csv-blobs.md) para obter mais informações.
>
> Um documento composto ou incorporado (como um arquivo ZIP, um documento word com e-mail do Outlook incorporado contendo anexos, ou um . O ficheiro MSG com anexos) também é indexado como um único documento. Por exemplo, todas as imagens extraídas dos anexos de um . O ficheiro MSG será devolvido no campo normalized_images.

* O conteúdo textual do documento é extraído num campo de cordas denominado `content` .

  > [!NOTE]
  > A Azure Cognitive Search limita quanto texto extrai dependendo do nível de preços: 32.000 caracteres para free tier, 64.000 para Basic, 4 milhões para Standard, 8 milhões para Standard S2 e 16 milhões para Standard S3. Uma advertência está incluída na resposta do estado do indexante para documentos truncados.  

* As propriedades de metadados especificados pelo utilizador presentes na bolha, se houver, são extraídas verbatim. Note que isto requer que um campo seja definido no índice com o mesmo nome que a chave de metadados da bolha. Por exemplo, se a sua bolha tiver uma chave de metadados `Sensitivity` com `High` valor, deverá definir um campo nomeado `Sensitivity` no seu índice de pesquisa e será povoado com o valor `High` .

* As propriedades padrão de metadados blob são extraídas nos seguintes campos:

  * **Nome \_ \_ de armazenamento de metadados** (Edm.String) - o nome do ficheiro da bolha. Por exemplo, se tiver uma bolha /meu-recipiente/minha pasta/sub-dobra/resume.pdf, o valor deste campo é `resume.pdf` .

  * **Caminho \_ \_ de armazenamento de metadados** (Edm.String) - o URI completo da bolha, incluindo a conta de armazenamento. Por exemplo, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  * **Tipo \_ \_ \_ de conteúdo de armazenamento de metadados** (Edm.String) - tipo de conteúdo especificado pelo código utilizado para carregar a bolha. Por exemplo, `application/octet-stream`.

  * **Armazenamento de metadados \_ \_ modificado \_ pela última vez** (Edm.DateTimeOffset) - último tempo de tempo modificado para a bolha. A Azure Cognitive Search usa esta estampação de tempo para identificar bolhas alteradas, para evitar reindexar tudo após a indexação inicial.

  * **tamanho \_ de \_ armazenamento de metadados** (Edm.Int64) - tamanho da bolha em bytes.

  * **Conteúdo de armazenamento de metadados \_ \_ \_ md5** (Edm.String) - Hash MD5 do conteúdo blob, se disponível.

  * **metadados \_ armazenam \_ sas \_ token** (Edm.String) - Um token SAS temporário que pode ser usado por [habilidades personalizadas](cognitive-search-custom-skill-interface.md) para ter acesso à bolha. Este token não deve ser guardado para utilização posterior, uma vez que pode expirar.

* As propriedades dos metadados específicas de cada formato de documento são extraídas nos campos [listados aqui.](#ContentSpecificMetadata)

Não precisa de definir campos para todas as propriedades acima no seu índice de pesquisa - basta capturar as propriedades que precisa para a sua aplicação.

> [!NOTE]
> Muitas vezes, os nomes de campo no seu índice existente serão diferentes dos nomes de campo gerados durante a extração do documento. Pode utilizar **mapeamentos de campo** para mapear os nomes de propriedade fornecidos pela Azure Cognitive Search para os nomes de campo no seu índice de pesquisa. Você verá um exemplo de mapeamentos de campo usados abaixo.
>

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>Definição de chaves de documento e mapeamentos de campo

Na Pesquisa Cognitiva Azure, a chave do documento identifica um documento de forma única. Cada índice de pesquisa deve ter exatamente um campo chave do tipo Edm.String. O campo-chave é necessário para cada documento que está a ser adicionado ao índice (na verdade é o único campo necessário).  

Deve considerar cuidadosamente qual o campo extraído que deve mapear para o campo chave para o seu índice. Os candidatos são:

* **Nome \_ \_ de armazenamento de metadados** - este pode ser um candidato conveniente, mas note que 1) os nomes podem não ser únicos, pois você pode ter bolhas com o mesmo nome em diferentes pastas, e 2) o nome pode conter caracteres que são inválidos em chaves de documento, como traços. Pode lidar com caracteres inválidos utilizando a `base64Encode` [função de mapeamento](search-indexer-field-mappings.md#base64EncodeFunction) de campo - se o fizer, lembre-se de codificar as chaves do documento ao passá-las em chamadas API como o Lookup. (Por exemplo, em .NET pode utilizar o [método UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) para o efeito).

* **caminho \_ \_ de armazenamento de metadados** - usando o caminho completo garante a singularidade, mas o caminho definitivamente contém caracteres que são `/` [inválidos numa chave de documento](/rest/api/searchservice/naming-rules).  Como acima, tem a opção de codificar as teclas utilizando a `base64Encode` [função](search-indexer-field-mappings.md#base64EncodeFunction).

* Uma terceira opção é adicionar uma propriedade de metadados personalizados às bolhas. Esta opção requer, no entanto, que o seu processo de upload de blob adicione essa propriedade de metadados a todas as bolhas. Uma vez que a chave é uma propriedade necessária, todas as bolhas que não têm essa propriedade deixarão de ser indexadas.

> [!IMPORTANT]
> Se não houver um mapeamento explícito para o campo chave no índice, a Azure Cognitive Search utiliza automaticamente `metadata_storage_path` como chave e a base-64 codifica valores-chave (a segunda opção acima).
>
>

Para este exemplo, vamos escolher o `metadata_storage_name` campo como chave de documento. Vamos também assumir que o seu índice tem um campo chave nomeado `key` e um campo para armazenar o tamanho do `fileSize` documento. Para ligar as coisas conforme desejado, especifique os seguintes mapeamentos de campo ao criar ou atualizar o seu indexante:

```http
    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]
```

Para juntar tudo isto, eis como pode adicionar mapeamentos de campo e ativar a codificação de chaves base-64 para um indexante existente:

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
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
```

Para obter mais informações, consulte [mapeamentos e transformações de campo.](search-indexer-field-mappings.md)

#### <a name="what-if-you-need-to-encode-a-field-to-use-it-as-a-key-but-you-also-want-to-search-it"></a>E se precisar de codificar um campo para usá-lo como chave, mas também quer revistá-lo?

Há momentos em que é necessário utilizar uma versão codificada de um campo como metadata_storage_path como chave, mas também é necessário que esse campo seja pesquisável (sem codificação). Para resolver esta questão, pode mapeá-la em dois campos; uma que será usada para a chave, e outra que será usada para fins de pesquisa. No exemplo abaixo, o campo *de chaves* contém o caminho codificado, enquanto o campo de *caminho* não está codificado e será usado como o campo pes pes pescável no índice.

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
<a name="WhichBlobsAreIndexed"></a>

## <a name="index-by-file-type"></a>Índice por tipo de ficheiro

Pode controlar quais as bolhas indexadas e que são ignoradas.

### <a name="include-blobs-having-specific-file-extensions"></a>Incluir blobs com extensões de ficheiros específicas

Só pode indexar as bolhas com as extensões de nome de ficheiro que especifica utilizando o parâmetro de configuração do `indexedFileNameExtensions` indexante. O valor é uma cadeia que contém uma lista separada por vírgula de extensões de ficheiros (com um ponto principal). Por exemplo, para indexar apenas o . PDF e . Bolhas DOCX, faça isto:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }
```

### <a name="exclude-blobs-having-specific-file-extensions"></a>Excluir bolhas com extensões de ficheiros específicas

Pode excluir blobs com extensões específicas de nome de ficheiros de indexação utilizando o `excludedFileNameExtensions` parâmetro de configuração. O valor é uma cadeia que contém uma lista separada por vírgula de extensões de ficheiros (com um ponto principal). Por exemplo, para indexar todas as bolhas exceto aquelas com o . PNG e . Extensões JPEG, faça isto:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }
```

Se ambos `indexedFileNameExtensions` e `excludedFileNameExtensions` parâmetros estiverem presentes, a Azure Cognitive Search primeiro olha `indexedFileNameExtensions` para , em seguida, em `excludedFileNameExtensions` . Isto significa que, se a mesma extensão de ficheiro estiver presente em ambas as listas, será excluída da indexação.

<a name="PartsOfBlobToIndex"></a>

## <a name="index-parts-of-a-blob"></a>Partes de índice de uma bolha

Pode controlar quais as partes das bolhas indexadas utilizando o `dataToExtract` parâmetro de configuração. Pode assumir os seguintes valores:

* `storageMetadata` - especifica que apenas as [propriedades padrão blob e metadados especificados pelo utilizador](../storage/blobs/storage-blob-container-properties-metadata.md) estão indexados.
* `allMetadata` - especifica que os metadados de armazenamento e os [metadados específicos](#ContentSpecificMetadata) do tipo de conteúdo extraídos do conteúdo blob são indexados.
* `contentAndMetadata` - especifica que todos os metadados e conteúdos texuais extraídos da bolha estão indexados. Este é o valor predefinido.

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

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Usando metadados blob para controlar como as bolhas são indexadas

Os parâmetros de configuração acima descritos aplicam-se a todas as bolhas. Às vezes, pode querer controlar como *as bolhas individuais* são indexadas. Pode fazê-lo adicionando as seguintes propriedades e valores de metadados blob:

| Nome da propriedade | Valor patrimonial | Explicação |
| --- | --- | --- |
| AzureSearch_Skip |"verdade" |Instrui o indexante blob a saltar completamente a bolha. Não se tenta nem metadados nem extração de conteúdo. Isto é útil quando uma bolha particular falha repetidamente e interrompe o processo de indexação. |
| AzureSearch_SkipContent |"verdade" |Isto equivale à `"dataToExtract" : "allMetadata"` definição [acima](#PartsOfBlobToIndex) descrita a uma determinada bolha. |

## <a name="index-from-multiple-sources"></a>Índice de várias fontes

Pode querer "montar" documentos de várias fontes no seu índice. Por exemplo, pode querer fundir texto de bolhas com outros metadados armazenados em Cosmos DB. Pode até usar o push indexing API juntamente com vários indexantes para acumular documentos de pesquisa de várias partes.

Para que isto funcione, todos os indexantes e outros componentes têm de concordar com a chave do documento. Para obter mais detalhes sobre este tópico, consulte [várias fontes de dados do Azure](./tutorial-multiple-data-sources.md) ou este post de blog, [Combine documentos com outros dados em Azure Cognitive Search](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

## <a name="index-large-datasets"></a>Índice de grandes conjuntos de dados

Indexar bolhas pode ser um processo demorado. Nos casos em que tem milhões de bolhas para indexar, pode acelerar a indexação dividindo os seus dados e usando vários indexantes para processar os dados em paralelo. Eis como pode configurar isto:

* Dividir os seus dados em vários recipientes blob ou pastas virtuais

* Configurar várias fontes de dados de pesquisa cognitiva Azure, uma por recipiente ou pasta. Para apontar para uma pasta de bolhas, utilize o `query` parâmetro:

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

* Crie um indexante correspondente para cada fonte de dados. Todos os indexantes podem apontar para o mesmo índice de pesquisa de alvos.  

* Uma unidade de pesquisa no seu serviço pode executar um indexante a qualquer momento. A criação de múltiplos indexantes como descrito acima só é útil se realmente funcionarem em paralelo. Para executar vários indexadores em paralelo, escale o seu serviço de pesquisa criando um número adequado de divisórias e réplicas. Por exemplo, se o seu serviço de pesquisa tiver 6 unidades de pesquisa (por exemplo, 2 divisórias x 3 réplicas), então 6 indexantes podem ser executados simultaneamente, resultando num aumento de seis vezes na produção de indexação. Para saber mais sobre dimensionamento e planeamento de capacidade, consulte [Ajustar a capacidade de um serviço de Pesquisa Cognitiva Azure.](search-capacity-planning.md)

<a name="DealingWithErrors"></a>

## <a name="handle-errors"></a>Processar erros

Por predefinição, o indexante blob para assim que encontra uma bolha com um tipo de conteúdo não suportado (por exemplo, uma imagem). É claro que pode utilizar o `excludedFileNameExtensions` parâmetro para saltar certos tipos de conteúdo. No entanto, poderá ser necessário indexar as bolhas sem conhecer antecipadamente todos os tipos de conteúdo possíveis. Para continuar a indexar quando for encontrado um tipo de conteúdo não suportado, defina o `failOnUnsupportedContentType` parâmetro de configuração `false` para:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }
```

Para algumas bolhas, a Azure Cognitive Search não consegue determinar o tipo de conteúdo, ou não é capaz de processar um documento do tipo de conteúdo suportado de outra forma. Para ignorar este modo de falha, desa ajuste o parâmetro de `failOnUnprocessableDocument` configuração para falso:

```http
      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

A Azure Cognitive Search limita o tamanho das bolhas que são indexadas. Estes limites estão documentados nos [Limites de Serviço na Pesquisa Cognitiva Azure.](./search-limits-quotas-capacity.md) As bolhas de grandes dimensões são tratadas como erros por defeito. No entanto, ainda pode indexar metadados de armazenamento de bolhas de grandes dimensões se definir `indexStorageMetadataOnlyForOversizedDocuments` o parâmetro de configuração para ser verdadeiro:

```http
    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

Também pode continuar a indexar se os erros ocorrerem em qualquer ponto de processamento, quer enquanto analisa as bolhas, quer adicionando documentos a um índice. Para ignorar um número específico de erros, desa um determinado `maxFailedItems` parâmetros e `maxFailedItemsPerBatch` configurar para os valores pretendidos. Por exemplo:

```http
    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }
```

<a name="ContentSpecificMetadata"></a>

## <a name="content-type-specific-metadata-properties"></a>Propriedades de metadados específicos do tipo de conteúdo

A tabela seguinte resume o processamento feito para cada formato de documento, e descreve as propriedades de metadados extraídas pela Azure Cognitive Search.

| Formato documental / tipo de conteúdo | Metadados extraídos | Detalhes do processamento |
| --- | --- | --- |
| HTML (texto/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Strip HTML marcação e texto de extrato |
| PDF (aplicação/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Texto de extração, incluindo documentos incorporados (excluindo imagens) |
| DOCX (aplicação/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Texto de extração, incluindo documentos incorporados |
| DOC (aplicação/palavra) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Texto de extração, incluindo documentos incorporados |
| DOCM (aplicação/vnd.ms-word.document.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Texto de extração, incluindo documentos incorporados |
| WORD XML (aplicação/vnd.ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Strip XML marcação e texto de extrato |
| PALAVRA 2003 XML (aplicação/vnd.ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Strip XML marcação e texto de extrato |
| XLSX (aplicação/vnd.openxmlformats-officedocument.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Texto de extração, incluindo documentos incorporados |
| XLS (aplicação/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Texto de extração, incluindo documentos incorporados |
| XLSM (aplicação/vnd.ms-excel.sheet.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Texto de extração, incluindo documentos incorporados |
| PPTX (aplicação/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Texto de extração, incluindo documentos incorporados |
| PPT (aplicação/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Texto de extração, incluindo documentos incorporados |
| PPTM (aplicação/vnd.ms-powerpoint.presentation.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Texto de extração, incluindo documentos incorporados |
| MSG (aplicação/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrair texto, incluindo texto extraído de anexos. `metadata_message_to_email`, `metadata_message_cc_email` e `metadata_message_bcc_email` são coleções de cordas, o resto dos campos são cordas.|
| ODT (aplicação/vnd.oasis.opendocument.text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Texto de extração, incluindo documentos incorporados |
| ODS (aplicação/vnd.oasis.opendocument.sheet de cálculo) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Texto de extração, incluindo documentos incorporados |
| ODP (aplicação/vnd.oasis.opendocument.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Texto de extração, incluindo documentos incorporados |
| ZIP (aplicação/fecho) |`metadata_content_type` |Extrair texto de todos os documentos do arquivo |
| GZ (aplicação/gzip) |`metadata_content_type` |Extrair texto de todos os documentos do arquivo |
| EPUB (aplicação/epub+zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Extrair texto de todos os documentos do arquivo |
| XML (aplicação/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Strip XML marcação e texto de extrato |
| JSON (aplicação/json) |`metadata_content_type`<br/>`metadata_content_encoding` |Extrair texto<br/>NOTA: Se necessitar de extrair vários campos de documentos de uma bolha JSON, consulte [as bolhas JSON indexantes](search-howto-index-json-blobs.md) para obter mais detalhes |
| EML (mensagem/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Texto de extração, incluindo anexos |
| RTF (aplicação/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Extrair texto|
| Texto simples (texto/planície) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Extrair texto|

## <a name="see-also"></a>Consulte também

* [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
* [Compreender bolhas usando IA](search-blob-ai-integration.md)
* [Visão geral da indexação da bolha](search-blob-storage-integration.md)