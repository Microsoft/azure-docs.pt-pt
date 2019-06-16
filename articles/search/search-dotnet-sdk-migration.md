---
title: Atualizar para o SDK .NET da Azure Search versão 3 - Azure Search
description: Migre o código para o SDK .NET da Azure Search versão 3 das versões mais antigas. Saiba quais são as novidades e quais alterações de código são necessárias.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d41c2b541bf80448d180a1d081c255e5bf754e5e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147337"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Atualizar para o SDK .NET da Azure Search versão 3

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Se estiver a utilizar versão pré-visualização do 2.0 ou mais antiga do [SDK .NET da Azure Search](https://aka.ms/search-sdk), este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 3.

Para obter instruções mais geral do SDK, incluindo exemplos, consulte [como utilizar o Azure Search a partir de um aplicativo .NET](search-howto-dotnet-sdk.md).

A versão 3 do Azure Search .NET SDK contém algumas alterações de versões anteriores. Principalmente são pequenas, para que alterar o seu código deve exigir muito pouco esforço. Ver [passos para atualizar](#UpgradeSteps) para obter instruções sobre como alterar o seu código para utilizar a nova versão do SDK.

> [!NOTE]
> Se estiver a utilizar 1.0.2-preview de versão ou mais, deve atualizar para a versão 1.1 e, em seguida, atualize para a versão 3. Ver [atualizar para o SDK .NET da Azure Search versão 1.1](search-dotnet-sdk-migration-version-1.md) para obter instruções.
>
> A instância de serviço do Azure Search oferece suporte a várias versões de REST API, incluindo o mais recente. Pode continuar a utilizar uma versão quando ele é já não é a mais recente, mas recomendamos que migre o código para usar a versão mais recente. Ao utilizar a API REST, tem de especificar a versão de API em cada solicitação por meio do parâmetro de versão de api. Quando utilizar o SDK de .NET, a versão do SDK que está a utilizar determina a versão da REST API correspondente. Se estiver a utilizar um SDK mais antigo, pode continuar a executar esse código sem alterações, mesmo que o serviço é atualizado para suportar uma versão mais recente do API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>O que há de novo na versão 3
A versão 3 do SDK de .NET de pesquisa do Azure destina-se a versão mais recente versão disponível em geral da API de REST de pesquisa do Azure, especificamente 2016-09-01. Isto torna possível utilizar muitas funcionalidades novas do Azure Search a partir de uma aplicação .NET, incluindo o seguinte:

* [Analisadores personalizados](https://aka.ms/customanalyzers)
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md) e [Table Storage do Azure](search-howto-indexing-azure-tables.md) suporte de indexador
* Personalização de indexador via [mapeamentos de campo](search-indexer-field-mappings.md)
* Suporte de ETags para ativar a atualização em simultâneo segura de definições de índice, indexadores e origens de dados
* Suporte para a criação de definições de campo de índice declarativamente decorando a classe do modelo e usando o novo `FieldBuilder` classe.
* Suporte para .NET Core e o perfil do .NET portátil 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Em primeiro lugar, atualizar a sua referência de NuGet para `Microsoft.Azure.Search` utilizando a consola de Gestor de pacotes NuGet ou ao clicar com o botão direito em suas referências de projeto e selecionando "Gerir NuGet pacotes..." no Visual Studio.

Assim que o NuGet tiver baixado os novos pacotes e suas dependências, recrie seu projeto. Dependendo de como o seu código está estruturado, ele poderá reconstruir os com êxito. Nesse caso, está pronto para começar!

Se sua compilação falhar, deverá ver um erro de compilação semelhante ao seguinte:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

A próxima etapa é corrigir este erro de compilação. Ver [alterações significativas na versão 3](#ListOfChanges) para obter detalhes sobre o que faz com que o erro e como corrigi-lo.

Poderá ver avisos de compilação adicionais relacionados com a propriedades ou métodos obsoletos. Os avisos inclui instruções sobre o que utilizar em vez da funcionalidade despromovida. Por exemplo, se a sua aplicação utiliza o `IndexingParameters.Base64EncodeKeys` propriedade, obterá um aviso que diz `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Depois de ter de corrigir os erros de compilação, pode efetuar alterações à sua aplicação para tirar partido das novas funcionalidades, se desejar. Novas funcionalidades no SDK são detalhadas no [quais são as novidades na versão 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Alterações significativas na versão 3
Há um pequeno número de alterações significativas na versão 3, que pode exigir código altera-se para além de reconstruir a sua aplicação.

### <a name="indexesgetclient-return-type"></a>Tipo de retorno de Indexes.GetClient
O `Indexes.GetClient` método tem um novo tipo de retorno. Anteriormente, ele retornado `SearchIndexClient`, mas isso foi alterado para `ISearchIndexClient` na versão 2.0-preview e que alterações apresenta para a versão 3. Isso é para apoiar os clientes que pretendem simular a `GetClient` método para testes de unidade, retornando uma implementação fictícia do `ISearchIndexClient`.

#### <a name="example"></a>Exemplo
Se o seu código é semelhante a isto:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Pode alterá-lo a este para corrigir quaisquer erros de compilação:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName, tipo de dados e outras pessoas já não são implicitamente convertidas para cadeias de caracteres
Existem muitos tipos no SDK de .NET da Azure Search, que derivam de `ExtensibleEnum`. Anteriormente foram todos esses tipos implicitamente conversível para o tipo `string`. No entanto, um bug foi descoberto no `Object.Equals` implementação para essas classes e corrigir o erro necessário desabilitar essa conversão implícita. A conversão explícita para `string` ainda é permitido.

#### <a name="example"></a>Exemplo
Se o seu código é semelhante a isto:

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

Pode alterá-lo a este para corrigir quaisquer erros de compilação:

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

### <a name="removed-obsolete-members"></a>Remover membros obsoletos

Poderá ver erros de compilação relacionados para métodos ou propriedades que foram marcadas como obsoletos na versão 2.0-pré-visualização e, em seguida, removida na versão 3. Se encontrar esses erros, eis como resolvê-los:

- Se estivesse usando esse construtor: `ScoringParameter(string name, string value)`, utilizá-lo em vez disso: `ScoringParameter(string name, IEnumerable<string> values)`
- Se estava a utilizar o `ScoringParameter.Value` propriedade, utilize o `ScoringParameter.Values` propriedade ou o `ToString` método em vez disso.
- Se estava a utilizar o `SearchRequestOptions.RequestId` propriedade, utilize o `ClientRequestId` propriedade em vez disso.

### <a name="removed-preview-features"></a>Funcionalidades de pré-visualização foi removido

Se estiver a atualizar da versão 2.0-pré-visualização para a versão 3, lembre-se de que JSON e suporte para indexadores de BLOBs de análise de CSV foi removido, uma vez que estas funcionalidades ainda estão em pré-visualização. Especificamente, os seguintes métodos do `IndexingParametersExtensions` classe foram removidos:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Se seu aplicativo tiver uma dependência sobre estas funcionalidades, não será capaz de atualizar para a versão 3 do SDK de .NET de pesquisa do Azure. Pode continuar a utilizar a versão 2.0-preview. No entanto,. tenha em atenção que **Recomendamos que não utilize SDKs de pré-visualização em aplicações de produção**. Funcionalidades de pré-visualização destinam-se apenas de avaliação e pode ser alterada.

## <a name="conclusion"></a>Conclusão
Se precisar de obter mais detalhes sobre como utilizar o SDK .NET da Azure Search, consulte a [.NET procedimentos](search-howto-dotnet-sdk.md).

Apreciamos os seus comentários sobre o SDK. Se tiver problemas, fique à vontade contacte-nos para obter ajuda na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se encontrar um bug, comunique o assunto na [repositório do GitHub do Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se para o prefixo seu título do problema com o "[o Azure Search]".

Obrigado por utilizar o Azure Search!
