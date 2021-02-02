---
title: Mapeamentos de campo nos indexadores
titleSuffix: Azure Cognitive Search
description: Configurar mapeamentos de campo num indexante para ter em conta as diferenças nos nomes de campo e nas representações de dados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: fb3a77291d8b24d5774094533f8c214f1527d771
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430450"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mapeamentos e transformações de campo usando indexantes de pesquisa cognitiva Azure

![Estágios indexantes](./media/search-indexer-field-mappings/indexer-stages-field-mappings.png "estágios indexantes")

Ao utilizar os indexadores de Pesquisa Cognitiva Azure, por vezes descobre que os dados de entrada não correspondem ao esquema do seu índice-alvo. Nesses casos, pode utilizar **mapeamentos de campo** para remodelar os seus dados durante o processo de indexação.

Algumas situações em que os mapeamentos de campo são úteis:

* A sua fonte de dados tem um campo chamado `_id` , mas a Azure Cognitive Search não permite nomes de campo que comecem com um sublinhado. Um mapeamento de campo permite-lhe efetivamente mudar o nome de um campo.
* Pretende preencher vários campos do índice a partir dos mesmos dados de origem de dados. Por exemplo, é melhor aplicar diferentes analisadores a esses campos.
* Pretende preencher um campo de índice com dados de mais de uma fonte de dados, e as fontes de dados utilizam cada um nomes de campo diferentes.
* Precisa de codificar ou descodificar os seus dados. Os mapeamentos de campo **suportam várias funções de mapeamento,** incluindo funções para codificação e descodição base64.

> [!NOTE]
> Os mapeamentos de campo nos indexadores são uma forma simples de mapear campos de dados para indexar campos, com alguma capacidade de conversão de dados leves. Dados mais complexos podem requerer o pré-processamento para remodelá-lo numa forma que é propícia à indexação. Uma opção que pode considerar é [a Azure Data Factory.](../data-factory/index.yml)

## <a name="set-up-field-mappings"></a>Configurar mapeamentos de campo

Um mapeamento de campo consiste em três partes:

1. A, `sourceFieldName` que representa um campo na sua fonte de dados. Esta propriedade é necessária.
2. Um `targetFieldName` opcional , que representa um campo no seu índice de pesquisa. Se omitido, é utilizado o mesmo nome que na fonte de dados.
3. Um `mappingFunction` opcional , que pode transformar os seus dados usando uma de várias funções predefinidas. Isto pode ser aplicado tanto nos mapeamentos de campo de entrada como de saída. A lista completa de funções está [abaixo](#mappingFunctions).

Os mapeamentos de campo são adicionados à `fieldMappings` matriz da definição do indexante.

> [!NOTE]
> Se não forem adicionados mapeamentos de campo, os indexantes assumem que os campos de origem de dados devem ser mapeados para indexar campos com o mesmo nome. A adição de um mapeamento de campo remove estes mapeamentos de campo predefinidos para o campo de origem e alvo. Alguns indexantes, como o [indexante de armazenamento de bolhas,](search-howto-indexing-azure-blob-storage.md)adicionam mapeamentos de campo predefinidos para o campo de chaves de índice.

## <a name="map-fields-using-rest"></a>Campos de mapas usando REST

Pode adicionar mapeamentos de campo ao criar um novo indexante utilizando o pedido de API [Create Indexer.](/rest/api/searchservice/create-Indexer) Pode gerir os mapeamentos de campo de um indexante existente utilizando o pedido de API [do Indexer de Atualização.](/rest/api/searchservice/update-indexer)

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

Um campo de origem pode ser referenciado em vários mapeamentos de campo. O exemplo a seguir mostra como "bifurcar" um campo, copiando o mesmo campo de origem para dois campos de índice diferentes:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> A Azure Cognitive Search usa comparação caso-insensível para resolver o campo e nomes de funções em mapeamentos de campo. Isto é conveniente (você não precisa obter todo o invólucro certo), mas significa que a sua fonte de dados ou índice não pode ter campos que diferem apenas por caso.  
>

## <a name="map-fields-using-net"></a>Campos de mapas usando .NET

Você define mapeamentos de campo no .NET SDK usando a classe [FieldMapping,](/dotnet/api/azure.search.documents.indexes.models.fieldmapping) que tem as propriedades `SourceFieldName` e , e uma referência `TargetFieldName` `MappingFunction` opcional.

Pode especificar mapeamentos de campo ao construir o indexante, ou mais tarde, definindo diretamente a `Indexer.FieldMappings` propriedade.

O exemplo C# a seguir define os mapeamentos de campo ao construir um indexante.

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
* [base64Deco código](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Função de código

Executa a codificação base64 segura do *URL* da cadeia de entrada. Assume que a entrada está codificada utf-8.

#### <a name="example---document-key-lookup"></a>Exemplo - análise da chave do documento

Apenas caracteres url-safe podem aparecer numa chave de documento de Pesquisa Cognitiva Azure (porque os clientes devem ser capazes de endereçar o documento usando a [API do Lookup).](/rest/api/searchservice/lookup-document) Se o campo de origem da sua chave contiver caracteres inseguros para o URL, pode utilizar a `base64Encode` função para convertê-la no tempo de indexação. No entanto, uma chave de documento (antes e depois da conversão) não pode ser superior a 1.024 caracteres.

Quando recuperar a chave codificada no tempo de pesquisa, pode então utilizar a `base64Decode` função para obter o valor original da chave e usá-la para recuperar o documento de origem.

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

#### <a name="example---preserve-original-values"></a>Exemplo - preservar valores originais

O [indexante de armazenamento blob](search-howto-indexing-azure-blob-storage.md) adiciona automaticamente um mapeamento de campo `metadata_storage_path` de , o URI da bolha, ao campo de chaves de índice se não for especificado nenhum mapeamento de campo. Este valor é codificado base64, por isso é seguro usar como chave de documento de Pesquisa Cognitiva Azure. O exemplo a seguir mostra como mapear simultaneamente uma versão codificada base64 *segura* por URL `metadata_storage_path` de um campo e preservar o valor original num `index_key` `metadata_storage_path` campo:

```JSON

"fieldMappings": [
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "metadata_storage_path"
  },
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "index_key",
    "mappingFunction": {
       "name": "base64Encode"
    }
  }
]
```

Se não incluir uma propriedade de parâmetros para a sua função de mapeamento, ele não tem o valor `{"useHttpServerUtilityUrlTokenEncode" : true}` .

A Azure Cognitive Search suporta duas codificações base64 diferentes. Deve utilizar os mesmos parâmetros ao codificar e descodi o mesmo campo. Para obter mais informações, consulte [as opções de codificação base64](#base64details) para decidir quais os parâmetros a utilizar.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Função de código

Executa a descodição base64 da cadeia de entrada. Presume-se que a entrada é uma cadeia codificada base64 segura por *URL.*

#### <a name="example---decode-blob-metadata-or-urls"></a>Exemplo - descodificar metadados blob ou URLs

Os seus dados de origem podem conter cadeias codificadas de Base64, tais como cordas de metadados blob ou URLs web, que pretende tornar pesjáveis como texto simples. Pode utilizar a `base64Decode` função para transformar os dados codificados de volta em cadeias regulares ao povoar o seu índice de pesquisa.

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

Se não incluir uma propriedade de parâmetros, ela não tem o valor `{"useHttpServerUtilityUrlTokenEncode" : true}` .

A Azure Cognitive Search suporta duas codificações base64 diferentes. Deve utilizar os mesmos parâmetros ao codificar e descodi o mesmo campo. Para mais detalhes, consulte [as opções de codificação base64](#base64details) para decidir quais os parâmetros a utilizar.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>opções de codificação base64

A Azure Cognitive Search suporta codificação de base 64 e codificação base normal64. Uma cadeia que é base64 codificada durante a indexação deve ser descodificada mais tarde com as mesmas opções de codificação, ou então o resultado não corresponderá ao original.

Se os `useHttpServerUtilityUrlTokenEncode` parâmetros ou `useHttpServerUtilityUrlTokenDecode` parâmetros para codificação e descodificamento respectivamente forem definidos para `true` , então `base64Encode` comporta-se como [HttpServerUtility.UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) e `base64Decode` comporta-se como [HttpServerUtility.urlTokenDecode](/dotnet/api/system.web.httpserverutility.urltokendecode).

> [!WARNING]
> Se `base64Encode` for utilizado para produzir valores-chave, deve ser definido como `useHttpServerUtilityUrlTokenEncode` verdadeiro. Apenas a codificação base 64 segura por URL pode ser utilizada para valores-chave. Consulte [as regras de nomeação &#40;&#41;de pesquisa cognitiva Azure ](/rest/api/searchservice/naming-rules) para o conjunto completo de restrições sobre caracteres em valores-chave.

As bibliotecas .NET em Azure Cognitive Search assumem o quadro completo .NET, que fornece codificação incorporada. As `useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` opções e as opções alavancam esta funcionalidade incorporada. Se estiver a utilizar o .NET Core ou outro quadro, recomendamos definir essas opções `false` e chamar diretamente as funções de codificação e descodição do seu quadro.

A tabela seguinte compara diferentes codificações base64 da `00>00?00` cadeia. Para determinar o processamento adicional necessário (se houver) para as suas funções base64, aplique a função de codificação da sua biblioteca na cadeia `00>00?00` e compare a saída com a saída prevista `MDA-MDA_MDA` .

| Encoding | Saída de codificação Base64 | Processamento adicional após codificação da biblioteca | Processamento adicional antes da descodagem da biblioteca |
| --- | --- | --- | --- |
| Base64 com estofamento | `MDA+MDA/MDA=` | Use caracteres seguros de URL e remova o estofamento | Use caracteres base64 padrão e adicione estofamento |
| Base64 sem enchimento | `MDA+MDA/MDA` | Use caracteres seguros url | Use caracteres base64 padrão |
| BASE URL-safe64 com enchimento | `MDA-MDA_MDA=` | Remover o estofamento | Adicionar estofamento |
| BASE URL-safe64 sem enchimento | `MDA-MDA_MDA` | Nenhum | Nenhum |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extrair FunçãoTokenAtPosition

Divide um campo de cordas utilizando o delimiter especificado e escolhe o símbolo na posição especificada na divisão resultante.

Esta função utiliza os seguintes parâmetros:

* `delimiter`: uma corda para usar como separador ao dividir a corda de entrada.
* `position`: uma posição completamente baseada em zero do token para colher depois da cadeia de entrada ser dividida.

Por exemplo, se a entrada for `Jane Doe` , o `delimiter` é `" "` (espaço) e o `position` é 0, o resultado `Jane` é; se o é `position` 1, o resultado é `Doe` . Se a posição se referir a um símbolo que não existe, um erro é devolvido.

#### <a name="example---extract-a-name"></a>Exemplo - extrair um nome

A sua fonte de dados contém um `PersonName` campo, e pretende indexá-lo como dois `FirstName` campos `LastName` separados e campos. Pode utilizar esta função para dividir a entrada utilizando o carácter espacial como delimiter.

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

Transforma uma corda formatada como uma matriz de cordas JSON numa matriz de cordas que pode ser usada para povoar um `Collection(Edm.String)` campo no índice.

Por exemplo, se a cadeia de entrada for `["red", "white", "blue"]` , então o campo alvo do tipo `Collection(Edm.String)` será povoado com os três valores `red` , e `white` `blue` . Para valores de entrada que não podem ser analisados como arrays de cordas JSON, um erro é devolvido.

#### <a name="example---populate-collection-from-relational-data"></a>Exemplo - povoar a recolha a partir de dados relacionais

A Azure SQL Database não tem um tipo de dados incorporado que naturalmente mapeia para `Collection(Edm.String)` campos em Azure Cognitive Search. Para preencher campos de recolha de cordas, pode pré-processar os seus dados de origem como uma matriz de cordas JSON e, em seguida, utilizar a `jsonArrayToStringCollection` função de mapeamento.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlFunção de código

Esta função pode ser usada para codificar uma corda de modo a que seja "URL safe". Quando usado com uma cadeia que contém caracteres que não são permitidos num URL, esta função converterá esses caracteres "inseguros" em equivalentes de entidade de caracteres. Esta função utiliza o formato de codificação UTF-8.

#### <a name="example---document-key-lookup"></a>Exemplo - análise da chave do documento

`urlEncode` a função pode ser usada como uma alternativa à `base64Encode` função, se apenas os caracteres inseguros url forem convertidos, mantendo outros caracteres como-é.

Digamos, a cadeia de entrada é `<hello>` - então o campo alvo do tipo `(Edm.String)` será povoado com o valor `%3chello%3e`

Quando recuperar a chave codificada no tempo de pesquisa, pode então utilizar a `urlDecode` função para obter o valor original da chave e usá-la para recuperar o documento de origem.

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

 ### <a name="urldecode-function"></a>urlFunção de código

 Esta função converte uma cadeia codificada por URL numa cadeia descodificada utilizando o formato de codificação UTF-8.

 ### <a name="example---decode-blob-metadata"></a>Exemplo - descodificar metadados blob

 Alguns clientes de armazenamento Azure codificam automaticamente metadados blob se contiver caracteres não ASCII. No entanto, se pretender tornar esses metadados pescáveis (como texto simples), pode utilizar a `urlDecode` função para transformar os dados codificados em cadeias regulares ao povoar o seu índice de pesquisa.

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
 
 ### <a name="fixedlengthencode-function"></a>fixoLengthEncode função
 
 Esta função converte uma cadeia de qualquer comprimento para uma corda de comprimento fixo.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Exemplo - chaves de documento de mapa que são muito longas
 
Quando se deparam com erros que se queixam de que a chave do documento é superior a 1024 caracteres, esta função pode ser aplicada para reduzir o comprimento da chave do documento.

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