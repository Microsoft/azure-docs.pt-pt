---
title: Mapeamentos de campo para indexação automatizada usando indexadores-Azure Search
description: Configure Azure Search mapeamentos de campo do indexador para considerar as diferenças em nomes de campo e representações de dados.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 771a6e413cd08a338da41c09cd6a0da35e28e5e4
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840651"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>Mapeamentos de campo e transformações usando indexadores de Azure Search

Ao usar indexadores de Azure Search, às vezes você descobre que os dados de entrada não correspondem exatamente ao esquema de seu índice de destino. Nesses casos, você pode usar mapeamentos de **campo** para remodelar os dados durante o processo de indexação.

Algumas situações em que os mapeamentos de campo são úteis:

* Sua fonte de dados tem um campo `_id`chamado, mas Azure Search não permite nomes de campo que começam com um sublinhado. Um mapeamento de campo permite que você renomeie efetivamente um campo.
* Você deseja preencher vários campos no índice a partir dos mesmos dados de fonte de dados. Por exemplo, talvez você queira aplicar analisadores diferentes a esses campos.
* Você deseja preencher um campo de índice com dados de mais de uma fonte de dados e as fontes de dados usam nomes de campos diferentes.
* Você precisa codificar ou decodificar seus dados em base64. Os mapeamentos de campo dão suporte a várias **funções de mapeamento**, incluindo funções para codificação e decodificação de Base64.

> [!NOTE]
> O recurso de mapeamento de campos de Azure Search indexadores fornece uma maneira simples de mapear campos de dados para campos de índice, com algumas opções de conversão de dados. Dados mais complexos podem exigir pré-processamento para reformatá-lo em um formato que seja fácil de indexar.
>
> Microsoft Azure Data Factory é uma poderosa solução baseada em nuvem para importar e transformar dados. Você também pode escrever código para transformar dados de origem antes da indexação. Para obter exemplos de código, consulte [modelos de dados relacionais de modelo](search-example-adventureworks-modeling.md) e de [vários níveis de modelo](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Configurar mapeamentos de campo

Um mapeamento de campo consiste em três partes:

1. A `sourceFieldName`, que representa um campo em sua fonte de dados. Essa propriedade é necessária.
2. Um opcional `targetFieldName`, que representa um campo em seu índice de pesquisa. Se omitido, o mesmo nome da fonte de dados será usado.
3. Um opcional `mappingFunction`, que pode transformar seus dados usando uma das várias funções predefinidas. A lista completa de funções está [abaixo](#mappingFunctions).

Os mapeamentos de campo são adicionados `fieldMappings` à matriz da definição do indexador.

## <a name="map-fields-using-the-rest-api"></a>Mapear campos usando a API REST

Você pode adicionar mapeamentos de campo ao criar um novo indexador usando a solicitação [criar](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) API do indexador. Você pode gerenciar os mapeamentos de campo de um indexador existente usando a solicitação [Atualizar](https://docs.microsoft.com/rest/api/searchservice/update-indexer) API do indexador.

Por exemplo, veja como mapear um campo de origem para um campo de destino com um nome diferente:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Um campo de origem pode ser referenciado em vários mapeamentos de campo. O exemplo a seguir mostra como "bifurcar" um campo, copiando o mesmo campo de origem para dois campos de índice diferentes:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Search usa a comparação que não diferencia maiúsculas de minúsculas para resolver os nomes de campo e função em mapeamentos de campo. Isso é conveniente (você não precisa obter todas as maiúsculas e minúsculas), mas isso significa que sua fonte de dados ou índice não pode ter campos que diferem apenas por maiúsculas e minúsculas.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mapear campos usando o SDK do .NET

Você define mapeamentos de campo no SDK do .NET usando a classe [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) , que tem as `SourceFieldName` propriedades `TargetFieldName`e e uma referência `MappingFunction` opcional.

Você pode especificar mapeamentos de campo ao construir o indexador, ou mais tarde, definindo diretamente `Indexer.FieldMappings` a propriedade.

O exemplo C# a seguir define os mapeamentos de campo ao construir um indexador.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Funções de mapeamento de campo

Uma função de mapeamento de campos transforma o conteúdo de um campo antes que ele seja armazenado no índice. Atualmente, há suporte para as seguintes funções de mapeamento:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>função base64Encode

Executa a codificação Base64 *segura para URL* da cadeia de caracteres de entrada. Pressupõe que a entrada é codificada em UTF-8.

#### <a name="example---document-key-lookup"></a>Exemplo – pesquisa de chave de documento

Somente caracteres de URL segura podem aparecer em uma chave de documento Azure Search (porque os clientes devem ser capazes de resolver o documento usando a [API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Se o campo de origem da chave contiver caracteres não seguros de URL, você poderá usar `base64Encode` a função para convertê-la no momento da indexação.

Quando você recupera a chave codificada no momento da pesquisa, pode usar a `base64Decode` função para obter o valor de chave original e usá-lo para recuperar o documento de origem.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

Se você não incluir uma propriedade Parameters para sua função de mapeamento, ela usa como `{"useHttpServerUtilityUrlTokenEncode" : true}`padrão o valor.

Azure Search dá suporte a duas codificações Base64 diferentes. Você deve usar os mesmos parâmetros ao codificar e decodificar o mesmo campo. Para obter mais informações, consulte [Opções de codificação Base64](#base64details) para decidir quais parâmetros usar.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>função base64Decode

Executa a decodificação Base64 da cadeia de caracteres de entrada. Pressupõe-se que a entrada seja uma cadeia de caracteres codificada em base64 *segura para URL* .

#### <a name="example---decode-blob-metadata-or-urls"></a>Exemplo – decodificar metadados ou URLs de BLOB

Os dados de origem podem conter cadeias de caracteres codificadas em base64, como cadeias de caracteres de metadados de BLOB ou URLs da Web, que você deseja tornar pesquisáveis como texto sem formatação. Você pode usar a `base64Decode` função para transformar os dados codificados de volta em cadeias de caracteres regulares ao preencher o índice de pesquisa.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Se você não incluir uma propriedade Parameters, o valor `{"useHttpServerUtilityUrlTokenEncode" : true}`padrão será.

Azure Search dá suporte a duas codificações Base64 diferentes. Você deve usar os mesmos parâmetros ao codificar e decodificar o mesmo campo. Para obter mais detalhes, consulte [Opções de codificação Base64](#base64details) para decidir quais parâmetros usar.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>opções de codificação Base64

Azure Search dá suporte a duas codificações Base64 diferentes: **Token de URL HttpServerUtility**e **codificação Base64 segura para URL sem preenchimento**. Uma cadeia de caracteres codificada em base64 durante a indexação deve ser decodificada posteriormente com as mesmas opções de codificação, caso contrário, o resultado não corresponderá ao original.

Se os `useHttpServerUtilityUrlTokenEncode` parâmetros `useHttpServerUtilityUrlTokenDecode` ou para codificação e decodificação respectivamente forem definidos como `true`e, `base64Encode` em seguida, se comparecerem com `base64Decode` [HttpServerUtility. UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) e se comparecerem [com HttpServerUtility. UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Se você não estiver usando o .NET Framework completo (ou seja, estiver usando o .NET Core ou outra estrutura) para produzir os valores de chave para emular o comportamento de Azure Search, defina `useHttpServerUtilityUrlTokenEncode` e `useHttpServerUtilityUrlTokenDecode` como `false`. Dependendo da biblioteca usada, as funções codificação Base64 e decodificação podem ser diferentes das usadas pelo Azure Search.

A tabela a seguir compara codificações Base64 diferentes da cadeia `00>00?00`de caracteres. Para determinar o processamento adicional necessário (se houver) para suas funções de Base64, aplique a função de codificação de biblioteca `00>00?00` na cadeia de caracteres e compare a saída `MDA-MDA_MDA`com a saída esperada.

| Codificação | Saída de codificação Base64 | Processamento adicional após a codificação de biblioteca | Processamento adicional antes da decodificação de biblioteca |
| --- | --- | --- | --- |
| Base64 com preenchimento | `MDA+MDA/MDA=` | Usar caracteres com URL segura e remover o preenchimento | Usar caracteres Base64 padrão e adicionar preenchimento |
| Base64 sem preenchimento | `MDA+MDA/MDA` | Usar caracteres de URL segura | Usar caracteres Base64 padrão |
| Base64 de URL segura com preenchimento | `MDA-MDA_MDA=` | Remover preenchimento | Adicionar preenchimento |
| Base64 de URL segura sem preenchimento | `MDA-MDA_MDA` | Nenhum | Nenhum |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>função extractTokenAtPosition

Divide um campo de cadeia de caracteres usando o delimitador especificado e escolhe o token na posição especificada na divisão resultante.

Essa função usa os seguintes parâmetros:

* `delimiter`: uma cadeia de caracteres a ser usada como separador ao dividir a cadeia de caracteres de entrada.
* `position`: uma posição de inteiro com base em zero do token a ser escolhido depois que a cadeia de caracteres de entrada é dividida.

Por exemplo, se a entrada for `Jane Doe`, o `delimiter` for `" "`(Space) e o `position` for 0, o resultado será `Jane`; se `position` for 1, o resultado será `Doe`. Se a posição se referir a um token que não existe, um erro será retornado.

#### <a name="example---extract-a-name"></a>Exemplo – extrair um nome

Sua fonte de dados contém `PersonName` um campo e você deseja indexá-lo como dois `FirstName` campos `LastName` e separados. Você pode usar essa função para dividir a entrada usando o caractere de espaço como o delimitador.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>função jsonArrayToStringCollection

Transforma uma cadeia de caracteres formatada como uma matriz JSON de cadeias de caracteres em uma matriz String que pode ser usada `Collection(Edm.String)` para popular um campo no índice.

Por exemplo, se a cadeia de caracteres `["red", "white", "blue"]`de entrada for, o campo de `Collection(Edm.String)` destino do tipo será preenchido com os `red`três `white`valores, `blue`e. Para valores de entrada que não podem ser analisados como matrizes de cadeia de caracteres JSON, um erro é retornado.

#### <a name="example---populate-collection-from-relational-data"></a>Exemplo – preencher a coleção de dados relacionais

O banco de dados SQL do Azure não tem um tipo de dado interno que `Collection(Edm.String)` é mapeado naturalmente para campos em Azure Search. Para preencher os campos de coleção de cadeia de caracteres, você pode pré-processar seus dados de origem como uma matriz de cadeia `jsonArrayToStringCollection` de caracteres JSON e, em seguida, usar a função de mapeamento.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Para obter um exemplo detalhado que transforma dados relacionais em campos de coleção de índice, consulte [dados relacionais de modelo](search-example-adventureworks-modeling.md).

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>função urlEncode

Essa função pode ser usada para codificar uma cadeia de caracteres de forma que ela seja "segura para a URL". Quando usado com uma cadeia de caracteres que contém caractere que não é permitido em uma URL, essa função converterá os caracteres "não seguros" em equivalentes de entidade de caracteres. Essa função usa o formato de codificação UTF-8.

#### <a name="example---document-key-lookup"></a>Exemplo – pesquisa de chave de documento

`urlEncode`a função pode ser usada como uma alternativa à `base64Encode` função, se apenas caracteres não seguros de URL forem convertidos, mantendo outros caracteres como estão.

Digamos que a cadeia de caracteres `<hello>` de entrada seja-então, o `(Edm.String)` campo de destino do tipo será preenchido com o valor`%3chello%3e`

Quando você recupera a chave codificada no momento da pesquisa, pode usar a `urlDecode` função para obter o valor de chave original e usá-lo para recuperar o documento de origem.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>função urlDecode

 Essa função converte uma cadeia de caracteres codificada em URL em uma cadeia de caracteres decodificada usando o formato de codificação UTF-8.

 ### <a name="example---decode-blob-metadata"></a>Exemplo – decodificar metadados de BLOB

 Alguns clientes de armazenamento do Azure URL automaticamente codificam metadados de BLOB se contiverem caracteres não ASCII. No entanto, se você quiser tornar esses metadados pesquisáveis (como texto sem formatação), poderá usar `urlDecode` a função para transformar os dados codificados em cadeias de caracteres regulares ao preencher o índice de pesquisa.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```