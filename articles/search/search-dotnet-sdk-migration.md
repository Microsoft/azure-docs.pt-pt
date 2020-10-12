---
title: Upgrade para Azure Search .NET SDK versão 3
titleSuffix: Azure Cognitive Search
description: Migrar o código para a versão 3 do Azure Search .NET SDK a partir de versões mais antigas. Saiba o que há de novo e quais as alterações de código necessárias.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e9f4602582a8c24f74d6603b732e7b91d97fa8d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89002696"
---
# <a name="upgrade-to-azure-search-net-sdk-version-3"></a>Upgrade para Azure Search .NET SDK versão 3

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Se estiver a utilizar a versão 2.0 pré-visualização ou mais antiga do [Azure Search .NET SDK,](/dotnet/api/overview/azure/search)este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 3.

Para uma passagem mais geral do SDK, incluindo exemplos, consulte [Como utilizar a Azure Search a partir de uma aplicação .NET](search-howto-dotnet-sdk.md).

A versão 3 do Azure Search .NET SDK contém algumas alterações a partir de versões anteriores. Estes são na sua maioria menores, por isso mudar o seu código deve exigir apenas o mínimo de esforço. Consulte [passos para atualizar](#UpgradeSteps) para obter instruções sobre como alterar o seu código para utilizar a nova versão SDK.

> [!NOTE]
> Se estiver a utilizar a versão 1.0.2 pré-visualização ou mais antiga, deve atualizar para a versão 1.1 primeiro e, em seguida, atualizar para a versão 3. Consulte [a atualização para a versão 1.1 da Azure Search .NET SDK](search-dotnet-sdk-migration-version-1.md) para obter instruções.
>
> A sua instância do serviço Azure Search suporta várias versões REST API, incluindo a mais recente. Pode continuar a utilizar uma versão quando já não é a mais recente, mas recomendamos que emigre o seu código para utilizar a versão mais recente. Ao utilizar a API REST, deve especificar a versão API em cada pedido através do parâmetro da versão API. Ao utilizar o .NET SDK, a versão do SDK que está a utilizar determina a versão correspondente da API REST. Se estiver a utilizar um SDK mais antigo, pode continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para suportar uma versão API mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Novidades na versão 3
A versão 3 do Azure Search .NET SDK tem como alvo a versão mais recente geralmente disponível da Azure Search REST API, especificamente 2016-09-01. Isto permite utilizar muitas novas funcionalidades da Azure Search a partir de uma aplicação .NET, incluindo as seguintes:

* [Analisadores personalizados](index-add-custom-analyzers.md)
* [Suporte ao indexante de armazenamento de mesa azure Blob](search-howto-indexing-azure-blob-storage.md) e [azure](search-howto-indexing-azure-tables.md)
* Personalização do indexante através de [mapeamentos de campo](search-indexer-field-mappings.md)
* Suporte ETags para permitir uma atualização simultânea segura de definições de índices, indexadores e fontes de dados
* Suporte para construir definições de campo de índice declarativamente decorando a sua classe de modelo e usando a nova `FieldBuilder` classe.
* Suporte para .NET Core e .NET Perfil portátil 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Em primeiro lugar, atualize a sua referência NuGet para `Microsoft.Azure.Search` utilizar a consola Do Gestor de Pacotes NuGet ou clicando à direita nas referências do seu projeto e selecionando "Gerir pacotes nuget..." em Estúdio Visual.

Assim que o NuGet tiver descarregado os novos pacotes e as suas dependências, reconstrua o seu projeto. Dependendo da estrutura do seu código, pode reconstruir com sucesso. Se sim, está pronto para ir!

Se a sua construção falhar, deverá ver um erro de construção como o seguinte:

```output
Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)
```

O próximo passo é corrigir este erro de construção. Consulte [Breaking changes na versão 3](#ListOfChanges) para obter detalhes sobre o que causa o erro e como corrigi-lo.

Pode ver avisos adicionais de construção relacionados com métodos ou propriedades obsoletos. As advertências incluirão instruções sobre o que usar em vez da função prevada. Por exemplo, se a sua aplicação usar a `IndexingParameters.Base64EncodeKeys` propriedade, deve receber um aviso que diz `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Uma vez corrigido quaisquer erros de construção, pode escoar alterações na sua aplicação para tirar partido de novas funcionalidades, se desejar. As novas funcionalidades no SDK são detalhadas no [que há de novo na versão 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Quebrando mudanças na versão 3
Há um pequeno número de alterações na versão 3 que podem exigir alterações de código para além de reconstruir a sua aplicação.

### <a name="indexesgetclient-return-type"></a>Índices.Tipo de retorno de Pensão
O `Indexes.GetClient` método tem um novo tipo de retorno. Anteriormente, voltou `SearchIndexClient` - mas este foi alterado para a versão `ISearchIndexClient` 2.0-preview, e essa mudança passa para a versão 3. Isto é para apoiar os clientes que desejem gozar com o `GetClient` método para os testes unitários, devolvendo uma implementação simulada de `ISearchIndexClient` .

#### <a name="example"></a>Exemplo
Se o seu código se parece com isto:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Pode alterá-lo para isto para corrigir quaisquer erros de construção:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalisadorName, DataType, e outros já não são implicitamente convertíveis às cordas
Existem muitos tipos no Azure Search .NET SDK que derivam de `ExtensibleEnum` . Anteriormente, estes tipos eram todos implicitamente convertíveis para `string` escrever. No entanto, foi descoberto um bug na `Object.Equals` implementação destas classes, e a correção do bug exigia desativar esta conversão implícita. A conversão explícita `string` ainda é permitida.

#### <a name="example"></a>Exemplo
Se o seu código se parece com isto:

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

Pode alterá-lo para isto para corrigir quaisquer erros de construção:

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Membros obsoletos removidos

Pode ver erros de construção relacionados com métodos ou propriedades que foram marcados como obsoletos na versão 2.0-pré-visualização e posteriormente removidos na versão 3. Se encontrar tais erros, eis como resolvê-los:

- Se estava a usar este construtor: `ScoringParameter(string name, string value)` use este em vez disso: `ScoringParameter(string name, IEnumerable<string> values)`
- Se você estava usando a `ScoringParameter.Value` propriedade, use a `ScoringParameter.Values` propriedade ou o método em vez `ToString` disso.
- Se você estava usando a `SearchRequestOptions.RequestId` propriedade, use a `ClientRequestId` propriedade em vez disso.

### <a name="removed-preview-features"></a>Funcionalidades de pré-visualização removidas

Se estiver a atualizar da versão 2.0 para a versão 3, esteja ciente de que o suporte de análise JSON e CSV para Indexadores Blob foi removido uma vez que estas funcionalidades ainda estão em pré-visualização. Especificamente, foram removidos os seguintes métodos da `IndexingParametersExtensions` classe:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Se a sua aplicação tiver uma forte dependência destas funcionalidades, não poderá fazer upgrade para a versão 3 do Azure Search .NET SDK. Pode continuar a utilizar a versão 2.0-pré-visualização. No entanto, tenha em mente que **não recomendamos a utilização de SDKs de pré-visualização em aplicações de produção**. As funcionalidades de pré-visualização são apenas para avaliação e podem ser alteradas.

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre a utilização do Azure Search .NET SDK, consulte o [.NET How-to](search-howto-dotnet-sdk.md).

Congratulamo-nos com o seu feedback sobre o SDK. Se encontrar problemas, sinta-se à vontade para nos pedir ajuda no [Stack Overflow.](https://stackoverflow.com/questions/tagged/azure-search) Se encontrar um bug, pode arquivar um problema no [repositório Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de pré-afixar o seu título de emissão com "[Azure Search]".

Obrigado por usar a Azure Search!