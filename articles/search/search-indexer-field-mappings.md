---
title: Mapeamentos de campo para automatizada de indexação com indexadores - Azure Search
description: Configure mapeamentos de campo do indexador de Azure Search para considerar as diferenças nos nomes de campo e representações de dados.
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 9bb8a168f54658ea14b174d19a1795e575646304
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310391"
---
# <a name="field-mappings-in-azure-search-indexers"></a>Mapeamentos de campo nos indexadores do Azure Search
Quando utilizar indexadores do Azure Search, ocasionalmente, pode encontrar-se em situações onde os dados de entrada não correspondem bem o esquema do seu índice de destino. Nesses casos, pode usar **mapeamentos de campo** para transformar os seus dados para o formato desejado.

Algumas situações em que os mapeamentos de campo são úteis:

* A origem de dados tem um campo `_id`, mas o Azure Search não permite que os nomes de campo começando com um caráter de sublinhado. Um mapeamento de campo permite-lhe "mudar o nome" um campo.
* Deseja preencher vários campos no índice com os dados de origem de dados mesmo, por exemplo, uma vez que deseja aplicar analisadores diferentes a esses campos. Mapeamentos de campo permitem-lhe "bifurcar" um campo de origem de dados.
* Precisa para Base64 codificar ou decodificar os dados. Mapeamentos de campo suportam vários **mapeamento de funções**, incluindo funções para Base64 codificação e decodificação.   

## <a name="setting-up-field-mappings"></a>Configuração de mapeamentos de campo
Pode adicionar mapeamentos de campo durante a criação de um indexador novo com o [criar indexador](https://msdn.microsoft.com/library/azure/dn946899.aspx) API. Pode gerir os mapeamentos de campo no indexador uma indexação a utilizar o [indexador de atualização](https://msdn.microsoft.com/library/azure/dn946892.aspx) API.

Um mapeamento de campo consiste em três partes:

1. A `sourceFieldName`, que representa um campo na origem de dados. Esta propriedade é necessária.
2. Opcional `targetFieldName`, que representa um campo no seu índice de pesquisa. Se for omitido, é utilizado o mesmo nome, tal como a origem de dados.
3. Opcional `mappingFunction`, que pode transformar os dados através de um dos vários predefinidos das funções. A lista completa de funções é [abaixo](#mappingFunctions).

Mapeamentos de campos são adicionados ao `fieldMappings` matriz na definição do indexador.

Por exemplo, eis como pode acomodar diferenças em nomes de campo:

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

Um indexador pode ter vários mapeamentos de campo. Por exemplo, aqui está como pode "bifurcar" um campo:

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

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Funções de mapeamento de campo
Estas funções são atualmente suportadas:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

## <a name="base64encode"></a>base64Encode
Executa *URL seguro* codificação Base64 de cadeia de entrada. Pressupõe-se de que a entrada é codificado UTF-8.

### <a name="sample-use-case---document-key-lookup"></a>Caso de utilização de exemplo - pesquisa de chave do documento
Carateres de seguros de URL apenas podem aparecer numa chave de documento do Azure Search (porque os clientes tem de ser capazes de enfrentar o documento com o [API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/lookup-document), por exemplo). Se seus dados contiverem carateres não seguros de URL e pretende utilizá-lo para preencher um campo de chave no seu índice de pesquisa, use essa função. Depois da chave é codificada, poderá usar base64 a descodificar para recuperar o valor original. Para obter detalhes, consulte a [base64 codificação e decodificação](#base64details) secção.

#### <a name="example"></a>Exemplo
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

### <a name="sample-use-case---retrieve-original-key"></a>Caso de utilização de exemplo - obter chave original
Tem um indexador de BLOBs de blobs de índices com os metadados de caminho do blob como a chave do documento. Depois de recuperar a chave do documento codificado, que pretende decodificar o caminho e transferir o blob.

#### <a name="example"></a>Exemplo
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode", "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false } }
  }]
 ```

Se não precisa procurar documentos pelas chaves e também não precisa decodificar codificada conteúdo, omitir `parameters` para a função de mapeamento, que assume a predefinição `useHttpServerUtilityUrlTokenEncode` para `true`. Caso contrário, veja [detalhes de base64](#base64details) secção para decidir quais as definições a utilizar.

<a name="base64DecodeFunction"></a>

## <a name="base64decode"></a>base64Decode
Executa Base64 decodificação de cadeia de entrada. A entrada é considerada como um *URL seguro* cadeia codificada em Base64.

### <a name="sample-use-case"></a>Caso de utilização de exemplo
Valores de metadados personalizados do blob tem de ser codificado em ASCII. Pode usar a codificação Base64 para representar as cadeias de caracteres arbitrárias UTF-8 no blob de metadados personalizados. No entanto, para fazer a pesquisa significativo, pode utilizar esta função para transformar os dados codificados novamente em cadeias de caracteres "normais" ao povoar o índice de pesquisa.

#### <a name="example"></a>Exemplo
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode", "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false } }
  }]
```

Se não especificar qualquer `parameters`, em seguida, o valor predefinido `useHttpServerUtilityUrlTokenDecode` é `true`. Ver [detalhes de base64](#base64details) secção para decidir quais as definições a utilizar.

<a name="base64details"></a>

### <a name="details-of-base64-encoding-and-decoding"></a>Detalhes de base64 codificação e descodificação
O Azure Search oferece suporte a dois base64 codificações: Token de URL de HttpServerUtility e codificação de base64 URL seguro sem preenchimento. Tem de utilizar a mesma codificação como as funções de mapeamento para codificar uma chave de documento para ver a cópia de segurança, codificar um valor a possível descodificar o indexador ou decodificar um campo com o indexador de codificação.

Se `useHttpServerUtilityUrlTokenEncode` ou `useHttpServerUtilityUrlTokenDecode` parâmetros para codificação e decodificação respectivamente estão definidos como `true`, em seguida, `base64Encode` se comporta como [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) e `base64Decode` se comporta como [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Se não estiver a utilizar o .NET Framework completo (ou seja, está a utilizar .NET Core ou outro ambiente de programação) para produzir os valores de chave para emular o comportamento de Azure Search, em seguida, deve definir `useHttpServerUtilityUrlTokenEncode` e `useHttpServerUtilityUrlTokenDecode` para `false`. Consoante a biblioteca a utilizar, o base64 codificar e decodificar o utilitário funções podem ser diferentes da Azure Search.

A tabela seguinte compara as codificações de base64 diferentes da cadeia de caracteres `00>00?00`. Para determinar o processamento adicional necessário (se houver) para as suas funções de base64, aplicam-se sua biblioteca de função na cadeia de codificar `00>00?00` e comparar o resultado com a saída esperada `MDA-MDA_MDA`.

| Codificação | Saída de codificar em Base64 | Processamento após a biblioteca de codificação adicional | Processamento antes de decodificação de biblioteca adicional |
| --- | --- | --- | --- |
| Base64 com preenchimento | `MDA+MDA/MDA=` | Utilize os carateres de URL seguro e remova o preenchimento | Utilizar carateres base64 padrão e adicione preenchimento |
| Base64 sem preenchimento | `MDA+MDA/MDA` | Utilizar os carateres de URL seguro | Utilizar carateres base64 padrão |
| URL seguro base64 com preenchimento | `MDA-MDA_MDA=` | Remover o preenchimento | Adicionar preenchimento |
| URL seguro base64 sem preenchimento | `MDA-MDA_MDA` | Nenhuma | Nenhuma |

<a name="extractTokenAtPositionFunction"></a>

## <a name="extracttokenatposition"></a>extractTokenAtPosition
Divide um campo de cadeia, utilizando o delimitador especificado e escolhe o token na posição especificada na divisão resultante.

Por exemplo, se a entrada for `Jane Doe`, o `delimiter` é `" "`(espaço) e o `position` é 0, o resultado é `Jane`; se o `position` for 1, o resultado é `Doe`. Se a posição se refere a um token que não existe, é devolvido um erro.

### <a name="sample-use-case"></a>Caso de utilização de exemplo
A origem de dados contém um `PersonName` campo e deseja indexá-lo como dois separado `FirstName` e `LastName` campos. Pode utilizar esta função para dividir a entrada com o caráter de espaço como delimitador.

### <a name="parameters"></a>Parâmetros
* `delimiter`: uma cadeia de caracteres para utilizar como separador ao dividir a cadeia de entrada.
* `position`: uma posição de número inteiro baseado em zero do token para escolher depois da cadeia de entrada é dividida.    

### <a name="example"></a>Exemplo
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

## <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection
Transforma uma cadeia de caracteres formatada como uma matriz JSON de cadeias de caracteres numa matriz de cadeia de caracteres que pode ser utilizada para preencher um `Collection(Edm.String)` campo no índice.

Por exemplo, se a cadeia de entrada for `["red", "white", "blue"]`, em seguida, o campo de destino do tipo `Collection(Edm.String)` será preenchida com os três valores `red`, `white`, e `blue`. Para valores de entrada que não não possível analisar como matrizes de seqüência de caracteres do JSON, é devolvido um erro.

### <a name="sample-use-case"></a>Caso de utilização de exemplo
Base de dados SQL do Azure não tem um tipo de dados internos que naturalmente mapeia para `Collection(Edm.String)` campos no Azure Search. Para preencher os campos de coleção de cadeia de caracteres, formatar os seus dados de origem como uma matriz de cadeia de caracteres do JSON e utilizar esta função.

### <a name="example"></a>Exemplo
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```


## <a name="help-us-make-azure-search-better"></a>Ajude-na melhorar o Azure Search
Se tiver de pedidos de funcionalidades ou ideias para melhorias, contacte-no nosso [site do UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
