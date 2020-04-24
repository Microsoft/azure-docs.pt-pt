---
title: Mapeamentos de campo nos indexadores
titleSuffix: Azure Cognitive Search
description: Configure mapeamentos de campo num indexante para explicar diferenças nos nomes de campo e representações de dados.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 17a96479b80410cbfcb2a6061904491f95c45f10
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116269"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mapeamentos e transformações de campo usando indexadores de pesquisa cognitiva Azure

Ao utilizar os indexadores de pesquisa cognitiva Azure, por vezes descobre que os dados de entrada não correspondem ao esquema do seu índice de destino. Nesses casos, pode utilizar **mapeamentos** de campo para reformular os seus dados durante o processo de indexação.

Algumas situações em que os mapeamentos de campo são úteis:

* A sua fonte de `_id`dados tem um campo chamado , mas a Pesquisa Cognitiva Azure não permite nomes de campo que começam com um sublinhado. Um mapeamento de campo permite-lhe efetivamente mudar o nome de um campo.
* Você quer povoar vários campos no índice a partir dos mesmos dados de fonte de dados. Por exemplo, é melhor aplicar diferentes analisadores a esses campos.
* Você quer povoar um campo de índice com dados de mais de uma fonte de dados, e as fontes de dados cada um usa diferentes nomes de campo.
* Precisa de codificar ou descodificar os seus dados no Base64. Os mapeamentos de campo suportam **várias funções de mapeamento,** incluindo funções para codificação e descodificação base64.

> [!NOTE]
> Os mapeamentos de campo nos indexadores são uma forma simples de mapear campos de dados para campos de índice, com alguma capacidade de conversão de dados de peso leve. Dados mais complexos podem exigir o pré-processamento para remodelá-lo em uma forma que é propícia à indexação. Uma opção que pode considerar é [a Azure Data Factory.](https://docs.microsoft.com/zure/data-factory/)

## <a name="set-up-field-mappings"></a>Configurar mapeamentos de campo

Um mapeamento de campo é composto por três partes:

1. A `sourceFieldName`, que representa um campo na sua fonte de dados. Esta propriedade é necessária.
2. Um `targetFieldName`opcional, que representa um campo no seu índice de pesquisa. Se omitida, é utilizado o mesmo nome que na fonte de dados.
3. Um `mappingFunction`opcional, que pode transformar os seus dados usando uma de várias funções predefinidas. A lista completa de funções está [abaixo](#mappingFunctions).

Os mapeamentos de `fieldMappings` campo são adicionados à matriz da definição do indexador.

## <a name="map-fields-using-the-rest-api"></a>Campos de mapas usando a API REST

Pode adicionar mapeamentos de campo ao criar um novo indexante utilizando o pedido de API do [Indexante Create.](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) Pode gerir os mapeamentos de campo de um indexante existente utilizando o pedido aPI do Indexante de [Atualização.](https://docs.microsoft.com/rest/api/searchservice/update-indexer)

Por exemplo, aqui está como mapear um campo de origem para um campo alvo com um nome diferente:

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

Um campo de origem pode ser referenciado em vários mapeamentos de campo. O exemplo que se segue mostra como "bifurcar" um campo, copiando o mesmo campo de origem para dois campos de índice diferentes:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> A Pesquisa Cognitiva Azure usa uma comparação insensível para resolver o campo e os nomes de funções em mapeamentos de campo. Isto é conveniente (não tem de acertar todo o invólucro), mas significa que a sua fonte de dados ou índice não pode ter campos que diferem apenas por caso.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Campos de mapas usando o .NET SDK

Define mapeamentos de campo no .NET SDK utilizando a `SourceFieldName` `TargetFieldName`classe [FieldMapping,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) que tem as propriedades e, e uma referência opcional. `MappingFunction`

Pode especificar mapeamentos de campo ao construir o indexador, ou posteriormente, definindo diretamente a `Indexer.FieldMappings` propriedade.

O exemplo C# seguinte define os mapeamentos de campo ao construir um indexante.

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

Uma função de mapeamento de campo transforma o conteúdo de um campo antes de ser armazenado no índice. As seguintes funções de mapeamento são atualmente suportadas:

* [base64Encode](#base64EncodeFunction)
* [base64Código](#base64DecodeFunction)
* [extractoTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>função base64Encode

Executa a codificação base64 *de base64 de url-safe* da cadeia de entrada. Assume que a entrada é codificada por UTF-8.

#### <a name="example---document-key-lookup"></a>Exemplo - verificação da chave de documentos

Apenas caracteres de URL-safe podem aparecer numa chave de documento supérrea de pesquisa cognitiva (porque os clientes devem ser capazes de abordar o documento usando a [API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Se o campo de origem da sua chave contiver caracteres inseguros de URL, pode utilizar a `base64Encode` função para convertê-la no momento de indexação. No entanto, uma chave de documentos (antes e depois da conversão) não pode ser superior a 1.024 caracteres.

Quando recuperar a chave codificada no momento da `base64Decode` pesquisa, pode utilizar a função para obter o valor chave original e usá-lo para recuperar o documento de origem.

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

Se não incluir uma propriedade de parâmetros para a sua `{"useHttpServerUtilityUrlTokenEncode" : true}`função de mapeamento, não se aplica ao valor .

A Pesquisa Cognitiva Azure suporta duas codificações de Base64 diferentes. Deve utilizar os mesmos parâmetros ao codificar e descodificar o mesmo campo. Para mais informações, consulte as opções de [codificação base64](#base64details) para decidir quais os parâmetros a utilizar.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>função base64Decode

Executa a descodificação base64 da cadeia de entrada. Presume-se que a entrada é uma cadeia codificada por *URL Base64.*

#### <a name="example---decode-blob-metadata-or-urls"></a>Exemplo - descodificar metadados ou URLs blob

Os seus dados de origem podem conter cordas codificadas pelo Base64, tais como cadeias de metadados blob ou URLs web, que pretende tornar pesquisáveis como texto simples. Pode utilizar `base64Decode` a função para transformar os dados codificados em cordas regulares ao povoar o seu índice de pesquisa.

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

Se não incluir uma propriedade de parâmetros, não `{"useHttpServerUtilityUrlTokenEncode" : true}`se aplica ao valor .

A Pesquisa Cognitiva Azure suporta duas codificações de Base64 diferentes. Deve utilizar os mesmos parâmetros ao codificar e descodificar o mesmo campo. Para mais detalhes, consulte as opções de [codificação base64](#base64details) para decidir quais os parâmetros a utilizar.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>opções de codificação base64

A Pesquisa Cognitiva Azure suporta a codificação de base 64 e a codificação normal de base64. Uma cadeia que seja de base64 codificada durante a indexação deve ser descodificada mais tarde com as mesmas opções de codificação, ou então o resultado não corresponderá ao original.

Se `useHttpServerUtilityUrlTokenEncode` os `useHttpServerUtilityUrlTokenDecode` parâmetros ou parâmetros para codificar `true`e `base64Encode` descodificar respectivamente estiverem definidos `base64Decode` para, então comporta-se como [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) e comporta-se como [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

> [!WARNING]
> Se `base64Encode` for utilizado para `useHttpServerUtilityUrlTokenEncode` produzir valores-chave, deve ser definido como verdadeiro. Só a codificação de base 64 de url-safe pode ser utilizada para valores-chave. Consulte [as regras de nomeação &#40;Pesquisa Cognitiva Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/naming-rules) para o conjunto completo de restrições sobre caracteres em valores-chave.

As bibliotecas .NET em Azure Cognitive Search assumem a estrutura completa .NET, que fornece codificação incorporada. As `useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` opções e opções alavancam esta funcionalidade incorporada. Se estiver a utilizar o Núcleo .NET ou `false` outro quadro, recomendamos que estabeleça essas opções e chame diretamente as funções de codificação e descodificação do seu quadro.

A tabela seguinte compara diferentes codificações `00>00?00`base64 da cadeia . Para determinar o processamento adicional necessário (se houver) para as suas funções base64, aplique a função de codificação da sua biblioteca na cadeia `00>00?00` e compare a saída com a saída `MDA-MDA_MDA`esperada .

| Codificação | Saída de código Base64 | Processamento adicional após codificação da biblioteca | Processamento adicional antes da descodificação da biblioteca |
| --- | --- | --- | --- |
| Base64 com enchimento | `MDA+MDA/MDA=` | Utilize caracteres de url-safe e remova o enchimento | Use caracteres base64 padrão e adicione enchimento |
| Base64 sem estofamento | `MDA+MDA/MDA` | Utilize caracteres de url-safe | Use caracteres base64 padrão |
| Base de segurança URL64 com enchimento | `MDA-MDA_MDA=` | Remover o acolchoado | Adicionar acolchoado |
| Base de segurança URL64 sem estofamento | `MDA-MDA_MDA` | Nenhuma | Nenhuma |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>função extracttokenAtPosition

Divide um campo de cordas utilizando o delimitador especificado e escolhe o símbolo na posição especificada na divisão resultante.

Esta função utiliza os seguintes parâmetros:

* `delimiter`: uma corda para usar como separador ao dividir a cadeia de entrada.
* `position`: uma posição de base zero do token a escolher após a separação da cadeia de entrada.

Por exemplo, se a `Jane Doe`entrada `delimiter` `" "`for , o `position` é (espaço) `Jane`e o é 0, o resultado é; se `position` o é 1, `Doe`o resultado é . Se a posição se refere a um símbolo que não existe, um erro é devolvido.

#### <a name="example---extract-a-name"></a>Exemplo - extrair um nome

A sua fonte `PersonName` de dados contém um campo, `FirstName` `LastName` e quer indexá-lo como dois campos separados e campos. Pode utilizar esta função para dividir a entrada utilizando o carácter espacial como delimitador.

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

Transforma uma corda formatada como uma matriz de cordas JSON em `Collection(Edm.String)` uma matriz de cordas que pode ser usada para povoar um campo no índice.

Por exemplo, se a `["red", "white", "blue"]`cadeia de entrada for `Collection(Edm.String)` , então o `red`campo-alvo do tipo será povoado com os três valores , `white`e `blue`. Para valores de entrada que não podem ser analisados como conjuntos de cordas JSON, um erro é devolvido.

#### <a name="example---populate-collection-from-relational-data"></a>Exemplo - recolha de povoar a partir de dados relacionais

A Base de Dados Azure SQL não tem um `Collection(Edm.String)` tipo de dados incorporado que mapeie naturalmente para campos em Pesquisa Cognitiva Azure. Para preencher campos de recolha de cordas, pode pré-processar os seus `jsonArrayToStringCollection` dados de origem como uma matriz de cordas JSON e, em seguida, utilizar a função de mapeamento.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>função urlEncode

Esta função pode ser utilizada para codificar uma corda de modo a que seja "URL safe". Quando usada com uma cadeia que contém caracteres que não são permitidos num URL, esta função converterá esses caracteres "inseguros" em equivalentes de entidadede caracteres. Esta função utiliza o formato de codificação UTF-8.

#### <a name="example---document-key-lookup"></a>Exemplo - verificação da chave de documentos

`urlEncode`a função pode ser `base64Encode` usada como uma alternativa à função, se apenas os caracteres inseguros URL forem convertidos, mantendo outros caracteres como-e-is.

Digamos, a cadeia `<hello>` de entrada é `(Edm.String)` - então o campo alvo do tipo será povoado com o valor`%3chello%3e`

Quando recuperar a chave codificada no momento da `urlDecode` pesquisa, pode utilizar a função para obter o valor chave original e usá-lo para recuperar o documento de origem.

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

 Esta função converte uma cadeia codificada por URL numa cadeia descodificada utilizando o formato de codificação UTF-8.

 ### <a name="example---decode-blob-metadata"></a>Exemplo - descodificar metadados blob

 Alguns clientes de armazenamento Azure url automaticamente codificam metadados blob se contiver caracteres não-ASCII. No entanto, se pretender tornar esses metadados pesquisáveis `urlDecode` (como texto simples), pode utilizar a função para transformar os dados codificados de volta em cadeias regulares ao povoar o seu índice de pesquisa.

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
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>função código de comprimento fixo
 
 Esta função converte uma cadeia de qualquer comprimento para uma corda de comprimento fixo.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Exemplo - chaves de documento de mapa que são muito longas
 
Ao enfrentar erros queixando-se de que a chave do documento seja superior a 1024 caracteres, esta função pode ser aplicada para reduzir o comprimento da chave do documento.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```
