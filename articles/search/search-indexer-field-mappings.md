---
title: Mapeamentos de campo para automatizada de indexação com indexadores - Azure Search
description: Configure mapeamentos de campo do indexador de Azure Search para considerar as diferenças nos nomes de campo e representações de dados.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 3546e342b535a122ea4ed3f844cd5e28a76d551a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147798"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>Mapeamentos de campo e transformações com indexadores do Azure Search

Quando utilizar indexadores do Azure Search, por vezes encontrar que os dados de entrada bastante não corresponde ao esquema do seu índice de destino. Nesses casos, pode usar **mapeamentos de campo** reformatar os dados durante o processo de indexação.

Algumas situações em que os mapeamentos de campo são úteis:

* A origem de dados tem um campo chamado `_id`, mas o Azure Search não permite que os nomes de campos que começam com um caráter de sublinhado. Um mapeamento de campo permite que efetivamente mudar o nome de um campo.
* Deseja preencher vários campos no índice dos mesmos dados de origem de dados. Por exemplo, pode querer aplicar analisadores diferentes a esses campos.
* Deseja preencher um campo de índice com dados de mais de uma origem de dados e as origens de dados, de cada utilizam nomes de campos diferentes.
* Precisa para Base64 codificar ou decodificar os dados. Mapeamentos de campo suportam vários **mapeamento de funções**, incluindo funções para Base64 codificação e decodificação.

> [!NOTE]
> A funcionalidade de mapeamento de campo de indexadores do Azure Search fornece uma forma simples para mapear campos de dados para os campos de índice, com algumas opções para conversão de dados. Dados mais complexos podem necessitar de pré-processamento para reformatá-lo numa forma fácil de índice.
>
> Microsoft Azure Data Factory é uma solução poderosa para importar e transformar dados com base na cloud. Também pode escrever código para transformar dados de origem antes da indexação. Para obter exemplos de código, consulte [modelar dados relacionais](search-example-adventureworks-modeling.md) e [facetas vários níveis de modelo](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Configurar mapeamentos de campo

Um mapeamento de campo consiste em três partes:

1. A `sourceFieldName`, que representa um campo na origem de dados. Esta propriedade é necessária.
2. Opcional `targetFieldName`, que representa um campo no seu índice de pesquisa. Se for omitido, é utilizado o mesmo nome, tal como a origem de dados.
3. Opcional `mappingFunction`, que pode transformar os dados através de um dos vários predefinidos das funções. A lista completa de funções é [abaixo](#mappingFunctions).

Mapeamentos de campo são adicionados para o `fieldMappings` matriz da definição de indexador.

## <a name="map-fields-using-the-rest-api"></a>Campos do mapa com a API REST

Pode adicionar mapeamentos de campo durante a criação de um indexador novo com o [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) pedido de API. Pode gerir os mapeamentos de campo de indexador uma existente, utilizando o [indexador de atualização](https://docs.microsoft.com/rest/api/searchservice/update-indexer) pedido de API.

Por exemplo, eis como mapear um campo de origem para um campo de destino com um nome diferente:

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

Um campo de origem pode ser referenciado em vários mapeamentos de campo. O exemplo seguinte mostra como "bifurcar" um campo, copiar o mesmo campo de origem para dois campos de índice diferentes:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> O Azure Search utiliza a comparação de maiúsculas e minúsculas para resolver os nomes de campo e a função em mapeamentos de campo. Isso é conveniente (não precisa de obter todas as maiúsculas e minúsculas corretas), mas significa que a sua origem de dados ou o índice não pode ter campos que diferem apenas por caso.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Campos do mapa com o SDK .NET

Definir mapeamentos de campo no SDK do .NET com o [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) classe, que tem as propriedades `SourceFieldName` e `TargetFieldName`e opcional `MappingFunction` referência.

Pode especificar mapeamentos de campo ao construir o indexador ou, mais tarde ao definir diretamente o `Indexer.FieldMappings` propriedade.

O seguinte C# exemplo define os mapeamentos de campo ao construir um indexador.

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

Uma função de mapeamento de campo transforma o conteúdo de um campo antes de serem armazenado no índice. Atualmente são suportadas as seguintes funções de mapeamento:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>função de base64Encode

Executa *URL seguro* codificação Base64 de cadeia de entrada. Pressupõe-se de que a entrada é codificado UTF-8.

#### <a name="example---document-key-lookup"></a>Exemplo - pesquisa de chave do documento

Carateres de seguros de URL apenas podem aparecer numa chave de documento do Azure Search (porque os clientes tem de ser capazes de enfrentar o documento com o [API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Se o campo de origem para a sua chave contém carateres não seguros de URL, pode utilizar o `base64Encode` função para convertê-la em tempo de indexação.

Ao obter a chave com codificação em tempo de pesquisa, em seguida, pode utilizar o `base64Decode` funcionar para obter o valor da chave original e usá-lo para obter o documento de origem.

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

Se não incluir uma propriedade de parâmetros para a função de mapeamento, é assumida como predefinição para o valor `{"useHttpServerUtilityUrlTokenEncode" : true}`.

O Azure Search suporta dois diferentes codificações de Base64. Deve usar os mesmos parâmetros quando a codificação e decodificação do mesmo campo. Para obter mais informações, consulte [opções de codificação de base64](#base64details) para decidir quais os parâmetros a utilizar.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>função de base64Decode

Executa Base64 decodificação de cadeia de entrada. A entrada é considerada como um *URL seguro* cadeia codificada em Base64.

#### <a name="example---decode-blob-metadata-or-urls"></a>Exemplo - descodificar metadados do blob ou URLs

Sua fonte de dados pode conter codificada em Base64 cadeias de caracteres, como cadeias de caracteres de metadados de BLOBs ou URLs de web, o que deseja tornar pesquisável como texto simples. Pode utilizar o `base64Decode` função para transformar os dados codificados novamente em cadeias de caracteres normais ao povoar o índice de pesquisa.

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

Se não incluir uma propriedade de parâmetros, é assumida como predefinição para o valor `{"useHttpServerUtilityUrlTokenEncode" : true}`.

O Azure Search suporta dois diferentes codificações de Base64. Deve usar os mesmos parâmetros quando a codificação e decodificação do mesmo campo. Para obter mais detalhes, consulte [opções de codificação de base64](#base64details) para decidir quais os parâmetros a utilizar.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Opções de codificação de Base64

O Azure Search oferece suporte a dois diferentes codificações de Base64: **Token de URL de HttpServerUtility**, e **codificação Base64 de URL seguro sem preenchimento**. Uma cadeia de caracteres está codificada em base64 durante a indexação mais tarde deve ser descodificada com as mesmas opções de codificação, caso contrário, o resultado não corresponderá ao original.

Se o `useHttpServerUtilityUrlTokenEncode` ou `useHttpServerUtilityUrlTokenDecode` parâmetros para codificação e decodificação respectivamente estão definidos como `true`, em seguida, `base64Encode` se comporta como [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) e `base64Decode` se comporta como [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Se não estiver a utilizar o .NET Framework completo (ou seja, está a utilizar .NET Core ou outra estrutura) para produzir os valores de chave para emular o comportamento de Azure Search, em seguida, deve definir `useHttpServerUtilityUrlTokenEncode` e `useHttpServerUtilityUrlTokenDecode` para `false`. Consoante a biblioteca a que utilizar, a codificação base64 e decodificar as funções podem divergir das utilizado pelo Azure Search.

A tabela seguinte compara as codificações de base64 diferentes da cadeia de caracteres `00>00?00`. Para determinar o processamento adicional necessário (se houver) para as suas funções de base64, aplicam-se sua biblioteca de função na cadeia de codificar `00>00?00` e comparar o resultado com a saída esperada `MDA-MDA_MDA`.

| Codificação | Saída de codificar em Base64 | Processamento após a biblioteca de codificação adicional | Processamento antes de decodificação de biblioteca adicional |
| --- | --- | --- | --- |
| Base64 com preenchimento | `MDA+MDA/MDA=` | Utilize os carateres de URL seguro e remova o preenchimento | Utilizar carateres base64 padrão e adicione preenchimento |
| Base64 sem preenchimento | `MDA+MDA/MDA` | Utilizar os carateres de URL seguro | Utilizar carateres base64 padrão |
| URL seguro base64 com preenchimento | `MDA-MDA_MDA=` | Remover o preenchimento | Adicionar preenchimento |
| URL seguro base64 sem preenchimento | `MDA-MDA_MDA` | Nenhuma | Nenhuma |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>função de extractTokenAtPosition

Divide um campo de cadeia, utilizando o delimitador especificado e escolhe o token na posição especificada na divisão resultante.

Esta função utiliza os seguintes parâmetros:

* `delimiter`: uma cadeia de caracteres para utilizar como separador ao dividir a cadeia de entrada.
* `position`: uma posição de número inteiro baseado em zero do token para escolher depois da cadeia de entrada é dividida.

Por exemplo, se a entrada for `Jane Doe`, o `delimiter` é `" "`(espaço) e o `position` é 0, o resultado é `Jane`; se o `position` for 1, o resultado é `Doe`. Se a posição se refere a um token que não existe, é devolvido um erro.

#### <a name="example---extract-a-name"></a>Exemplo - extrair um nome

A origem de dados contém um `PersonName` campo e deseja indexá-lo como dois separado `FirstName` e `LastName` campos. Pode utilizar esta função para dividir a entrada com o caráter de espaço como delimitador.

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

### <a name="jsonarraytostringcollection-function"></a>função de jsonArrayToStringCollection

Transforma uma cadeia de caracteres formatada como uma matriz JSON de cadeias de caracteres numa matriz de cadeia de caracteres que pode ser utilizada para preencher um `Collection(Edm.String)` campo no índice.

Por exemplo, se a cadeia de entrada for `["red", "white", "blue"]`, em seguida, o campo de destino do tipo `Collection(Edm.String)` será preenchida com os três valores `red`, `white`, e `blue`. Para valores de entrada que não não possível analisar como matrizes de seqüência de caracteres do JSON, é devolvido um erro.

#### <a name="example---populate-collection-from-relational-data"></a>Exemplo - popular a coleção de dados relacionais

Base de dados SQL do Azure não tem um tipo de dados internos que naturalmente mapeia para `Collection(Edm.String)` campos no Azure Search. Para preencher os campos de coleção de cadeia de caracteres, pode pré-processar os dados de origem como uma matriz de cadeia de caracteres do JSON e, em seguida, utilizar o `jsonArrayToStringCollection` mapeamento de função.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Para obter um exemplo detalhado que transforma dados relacionais em campos de coleção de índice, consulte [modelar dados relacionais](search-example-adventureworks-modeling.md).
