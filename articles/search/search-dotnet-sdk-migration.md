---
title: Upgrade para Azure Search .NET SDK versão 3
titleSuffix: Azure Cognitive Search
description: Migrar código para a versão 3 do Azure Search .NET SDK a partir de versões mais antigas. Saiba o que é novo e quais as alterações de código que são necessárias.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcad05749892e3a652e110a7e351450bffaca6f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72792972"
---
# <a name="upgrade-to-azure-search-net-sdk-version-3"></a>Upgrade para Azure Search .NET SDK versão 3

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Se estiver a utilizar a versão 2.0-preview ou mais antiga do [Azure Search .NET SDK,](https://aka.ms/search-sdk)este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 3.

Para uma passagem mais geral do SDK, incluindo exemplos, consulte [Como utilizar a Pesquisa Azure a partir de uma aplicação .NET](search-howto-dotnet-sdk.md).

A versão 3 do Azure Search .NET SDK contém algumas alterações de versões anteriores. Estes são na sua maioria menores, por isso a alteração do seu código deve exigir apenas um esforço mínimo. Consulte [passos para atualizar](#UpgradeSteps) para obter instruções sobre como alterar o seu código para utilizar a nova versão SDK.

> [!NOTE]
> Se estiver a utilizar a versão 1.0.2-preview ou mais antiga, deve fazer o upgrade para a versão 1.1 primeiro e, em seguida, atualizar para a versão 3. Consulte [o Upgrade para a versão 1.1 do Azure Search .NET SDK](search-dotnet-sdk-migration-version-1.md) para obter instruções.
>
> A sua instância de serviço Azure Search suporta várias versões REST API, incluindo a mais recente. Pode continuar a utilizar uma versão quando já não é a mais recente, mas recomendamos que emigra o seu código para utilizar a versão mais recente. Ao utilizar a API REST, deve especificar a versão API em todos os pedidos através do parâmetro da versão api. Ao utilizar o .NET SDK, a versão do SDK que está a utilizar determina a versão correspondente da API REST. Se estiver a utilizar um SDK mais antigo, pode continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para suportar uma versão API mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>O que há de novo na versão 3
A versão 3 do Azure Search .NET SDK tem como alvo a mais recente versão geralmente disponível da API de Pesquisa Azure, especificamente 2016-09-01. Isto permite utilizar muitas novidades da Pesquisa Azure a partir de uma aplicação .NET, incluindo as seguintes:

* [Analisadores personalizados](https://aka.ms/customanalyzers)
* Suporte do indexante de [armazenamento de blob azure](search-howto-indexing-azure-blob-storage.md) e [azure](search-howto-indexing-azure-tables.md)
* Personalização indexante através de [mapeamentos de campo](search-indexer-field-mappings.md)
* Suporte eTags para permitir a atualização simultânea segura de definições de índices, indexadores e fontes de dados
* Suporte para definições de campo de índice de construção `FieldBuilder` declarativa, decorando a sua classe modelo e usando a nova classe.
* Suporte para .NET Core e .NET Perfil portátil 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Em primeiro lugar, atualize a sua referência NuGet para `Microsoft.Azure.Search` utilizar a Consola nuGet Package Manager ou clicando corretamente nas referências do seu projeto e selecionando "Gerir pacotes NuGet..." no Estúdio Visual.

Assim que o NuGet tiver descarregado os novos pacotes e as suas dependências, reconstrua o seu projeto. Dependendo da forma como o seu código é estruturado, pode reconstruir com sucesso. Se assim for, está pronto para ir!

Se a sua construção falhar, deve ver um erro de construção como o seguinte:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

O próximo passo é corrigir este erro de construção. Consulte [as alterações de Rutura na versão 3](#ListOfChanges) para obter detalhes sobre o que causa o erro e como corrigi-lo.

Pode ver avisos adicionais de construção relacionados com métodos ou propriedades obsoletos. As advertências incluirão instruções sobre o que usar em vez da função depreciada. Por exemplo, se a `IndexingParameters.Base64EncodeKeys` sua aplicação usar a propriedade, você deve receber um aviso que diz`"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Depois de corrigir quaisquer erros de construção, pode efazer alterações na sua aplicação para tirar partido de novas funcionalidades, se desejar. As novas funcionalidades no SDK são detalhadas no [What's new in version 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Quebrar alterações na versão 3
Existe um pequeno número de alterações de quebra na versão 3 que podem exigir alterações de código para além de reconstruir a sua aplicação.

### <a name="indexesgetclient-return-type"></a>Índices.GetClient tipo de devolução
O `Indexes.GetClient` método tem um novo tipo de retorno. Anteriormente, voltou `SearchIndexClient`, mas `ISearchIndexClient` esta foi alterada para na versão 2.0-preview, e essa mudança passa para a versão 3. Isto é para apoiar os `GetClient` clientes que desejem ridicularizar o `ISearchIndexClient`método dos testes unitários devolvendo uma implementação falsa de .

#### <a name="example"></a>Exemplo
Se o seu código se parecer com este:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Pode mudá-lo para isto para corrigir eventuais erros de construção:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>O Nome analisador, dataType e outros já não são implicitamente convertíveis a cordas
Existem muitos tipos no Azure Search .NET `ExtensibleEnum`SDK que derivam de . Anteriormente, estes tipos eram todos `string`implicitamente convertíveis para escrever . No entanto, um `Object.Equals` bug foi descoberto na implementação destas classes, e a fixação do bug exigiu desativar esta conversão implícita. A conversão `string` explícita para ainda é permitida.

#### <a name="example"></a>Exemplo
Se o seu código se parecer com este:

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

Pode mudá-lo para isto para corrigir eventuais erros de construção:

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

- Se estava a usar `ScoringParameter(string name, string value)`este construtor: use este em vez disso:`ScoringParameter(string name, IEnumerable<string> values)`
- Se você estava `ScoringParameter.Value` usando a `ScoringParameter.Values` propriedade, `ToString` use a propriedade ou o método em vez disso.
- Se você estava `SearchRequestOptions.RequestId` usando a `ClientRequestId` propriedade, use a propriedade em vez disso.

### <a name="removed-preview-features"></a>Funcionalidades de pré-visualização removidas

Se estiver a atualizar da versão 2.0-preview para a versão 3, esteja ciente de que o suporte de análise JSON e CSV para Os Indexers Blob foi removido uma vez que estas funcionalidades ainda estão em pré-visualização. Especificamente, foram removidos os seguintes métodos da `IndexingParametersExtensions` classe:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Se a sua aplicação tiver uma dependência dura destas funcionalidades, não poderá fazer o upgrade para a versão 3 do Azure Search .NET SDK. Pode continuar a utilizar a versão 2.0-preview. No entanto, tenha em mente que **não recomendamos a utilização de SDKs de pré-visualização em aplicações**de produção . As funcionalidades de pré-visualização são apenas para avaliação e podem mudar.

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre a utilização do Azure Search .NET SDK, consulte o [.NET How-to](search-howto-dotnet-sdk.md).

Congratulamo-nos com o seu feedback sobre o SDK. Se encontrar problemas, sinta-se à vontade para nos pedir ajuda no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se encontrar um bug, pode apresentar um problema no [repositório Azure.NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de pré-fixar o título de emissão com "[Pesquisa Azure]".

Obrigado por usar a Pesquisa Azure!
