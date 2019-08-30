---
title: Atualizar para o SDK do .NET Azure Search versão 3-Azure Search
description: Migre o código para o Azure Search .NET SDK versão 3 de versões mais antigas. Saiba o que há de novo e quais alterações de código são necessárias.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: cab0da93bbea117c216969faf2f1e194e16d675f
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183213"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Atualizando para o SDK do .NET Azure Search versão 3

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Se você estiver usando a versão 2,0-Preview ou anterior do [SDK Azure Search .net](https://aka.ms/search-sdk), este artigo ajudará você a atualizar seu aplicativo para usar a versão 3.

Para obter uma explicação mais geral do SDK, incluindo exemplos, consulte [como usar Azure Search de um aplicativo .net](search-howto-dotnet-sdk.md).

A versão 3 do SDK do .NET Azure Search contém algumas alterações de versões anteriores. Elas são basicamente secundárias, portanto, alterar seu código deve exigir apenas um mínimo de esforço. Consulte [as etapas para atualizar](#UpgradeSteps) para obter instruções sobre como alterar seu código para usar a nova versão do SDK.

> [!NOTE]
> Se você estiver usando a versão 1.0.2-Preview ou mais antiga, deverá atualizar para a versão 1,1 primeiro e, em seguida, atualizar para a versão 3. Consulte [Atualizando para o SDK do .net Azure Search versão 1,1](search-dotnet-sdk-migration-version-1.md) para obter instruções.
>
> Sua instância do serviço de Azure Search dá suporte a várias versões da API REST, incluindo a mais recente. Você pode continuar a usar uma versão quando ela não for mais a mais recente, mas é recomendável migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro API-Version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se você estiver usando um SDK mais antigo, poderá continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para dar suporte a uma versão mais recente da API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>O que há de novo na versão 3
A versão 3 do SDK do .NET Azure Search tem como alvo a versão mais recente disponível da API REST do Azure Search, especificamente 2016-09-01. Isso possibilita o uso de muitos recursos novos do Azure Search de um aplicativo .NET, incluindo o seguinte:

* [Analisadores personalizados](https://aka.ms/customanalyzers)
* Suporte ao [armazenamento de BLOBs do Azure](search-howto-indexing-azure-blob-storage.md) e ao indexador de [armazenamento de tabela do Azure](search-howto-indexing-azure-tables.md)
* Personalização do indexador por meio de mapeamentos de [campo](search-indexer-field-mappings.md)
* As ETags dão suporte para habilitar a atualização simultânea segura de definições de índice, indexadores e fontes de dados
* Suporte para a criação de definições de campo de índice declarativamente decorando sua classe de `FieldBuilder` modelo e usando a nova classe.
* Suporte para .NET Core e perfil portátil .NET 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas para atualizar
Primeiro, atualize a referência do NuGet `Microsoft.Azure.Search` para usar o console do Gerenciador de pacotes NuGet ou clicando com o botão direito do mouse nas referências do projeto e selecionando "gerenciar pacotes NuGet..." no Visual Studio.

Depois que o NuGet tiver baixado os novos pacotes e suas dependências, recompile o projeto. Dependendo de como seu código é estruturado, ele pode ser recriado com êxito. Nesse caso, você está pronto para começar!

Se sua compilação falhar, você deverá ver um erro de compilação como o seguinte:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

A próxima etapa é corrigir esse erro de compilação. Consulte [alterações significativas na versão 3](#ListOfChanges) para obter detalhes sobre o que causa o erro e como corrigi-lo.

Você pode ver avisos de compilação adicionais relacionados a propriedades ou métodos obsoletos. Os avisos incluirão instruções sobre o que usar em vez do recurso preterido. Por exemplo, se seu aplicativo usar a `IndexingParameters.Base64EncodeKeys` Propriedade, você deverá receber um aviso dizendo`"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Depois de corrigir os erros de compilação, você poderá fazer alterações em seu aplicativo para tirar proveito da nova funcionalidade, se desejar. Os novos recursos do SDK são detalhados no [que há de novo na versão 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Alterações recentes na versão 3
Há um pequeno número de alterações significativas na versão 3 que podem exigir alterações de código, além de recompilar seu aplicativo.

### <a name="indexesgetclient-return-type"></a>Tipo de retorno de indexes. getClient
O `Indexes.GetClient` método tem um novo tipo de retorno. Anteriormente, ele retornou `SearchIndexClient`, mas isso foi alterado para `ISearchIndexClient` na versão 2,0-Preview, e essa alteração é transferida para a versão 3. Isso é para dar suporte a clientes que desejam simular o `GetClient` método para testes de unidade, retornando uma implementação fictícia de. `ISearchIndexClient`

#### <a name="example"></a>Exemplo
Se o seu código tiver esta aparência:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Você pode alterá-lo para corrigir quaisquer erros de compilação:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>Analyzername, DataType e outros não são mais implicitamente conversíveis para cadeias de caracteres
Há muitos tipos no SDK Azure Search .NET que derivam de `ExtensibleEnum`. Anteriormente, esses tipos eram implicitamente conversíveis para `string`o tipo. No entanto, um bug foi descoberto `Object.Equals` na implementação para essas classes e a correção do bug exigia desabilitar essa conversão implícita. A conversão explícita `string` para ainda é permitida.

#### <a name="example"></a>Exemplo
Se o seu código tiver esta aparência:

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

Você pode alterá-lo para corrigir quaisquer erros de compilação:

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

Você pode ver erros de compilação relacionados a métodos ou propriedades que foram marcados como obsoletos na versão 2,0-Preview e subsequentemente removidos na versão 3. Se você encontrar esses erros, aqui está como resolvê-los:

- Se você estivesse usando este construtor: `ScoringParameter(string name, string value)`, use este:`ScoringParameter(string name, IEnumerable<string> values)`
- Se você estiver usando a `ScoringParameter.Value` Propriedade, use a `ScoringParameter.Values` propriedade ou o `ToString` método em vez disso.
- Se você estiver usando a `SearchRequestOptions.RequestId` Propriedade, use a `ClientRequestId` Propriedade em vez disso.

### <a name="removed-preview-features"></a>Recursos de visualização removidos

Se você estiver atualizando da versão 2,0-Preview para a versão 3, lembre-se de que o suporte à análise de JSON e CSV para indexadores de blob foi removido, pois esses recursos ainda estão em versão prévia. Especificamente, os seguintes métodos da `IndexingParametersExtensions` classe foram removidos:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Se seu aplicativo tiver uma dependência rígida desses recursos, você não poderá atualizar para a versão 3 do SDK do .NET Azure Search. Você pode continuar a usar a versão 2,0-Preview. No entanto, tenha em mente que não recomendamos o **uso de SDKs de visualização em aplicativos de produção**. Os recursos de visualização são apenas para avaliação e podem ser alterados.

## <a name="conclusion"></a>Conclusão
Se você precisar de mais detalhes sobre como usar o SDK do .NET Azure Search, consulte o [instruções .net](search-howto-dotnet-sdk.md).

Agradecemos seus comentários sobre o SDK. Se você encontrar problemas, sinta-se à vontade para nos pedir ajuda sobre [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se você encontrar um bug, poderá arquivar um problema no [repositório GitHub do SDK do .net do Azure](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de prefixar o título do problema com "[Azure Search]".

Obrigado por usar Azure Search!
