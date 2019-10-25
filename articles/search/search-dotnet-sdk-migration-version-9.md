---
title: Atualizar para Azure Search SDK do .NET versão 9
titleSuffix: Azure Cognitive Search
description: Migre o código para o Azure Search SDK do .NET versão 9 de versões mais antigas. Saiba o que há de novo e quais alterações de código são necessárias.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcc70267754f7e66f29dd1b855d3efb8b814e78b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793005"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Atualizar para Azure Search SDK do .NET versão 9

Se você estiver usando a versão 7,0-Preview ou anterior do [SDK Azure Search .net](https://aka.ms/search-sdk), este artigo ajudará você a atualizar seu aplicativo para usar a versão 9.

> [!NOTE]
> Se você quiser usar a versão 8,0-Preview para avaliar recursos que ainda não estão disponíveis, você também pode seguir as instruções neste artigo para atualizar para 8,0-preview de versões anteriores.

Para obter uma explicação mais geral do SDK, incluindo exemplos, consulte [como usar Azure Search de um aplicativo .net](search-howto-dotnet-sdk.md).

A versão 9 do SDK do .NET Azure Search contém muitas alterações de versões anteriores. Algumas dessas alterações são significativas, mas elas devem exigir apenas alterações relativamente secundárias em seu código. Consulte [as etapas para atualizar](#UpgradeSteps) para obter instruções sobre como alterar seu código para usar a nova versão do SDK.

> [!NOTE]
> Se você estiver usando a versão 4,0-Preview ou mais antiga, atualize para a versão 5 primeiro e, em seguida, atualize para a versão 9. Consulte [Atualizando para o SDK do .net Azure Search versão 5](search-dotnet-sdk-migration-version-5.md) para obter instruções.
>
> Sua instância do serviço de Azure Search dá suporte a várias versões da API REST, incluindo a mais recente. Você pode continuar a usar uma versão quando ela não for mais a mais recente, mas é recomendável migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro API-Version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se você estiver usando um SDK mais antigo, poderá continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para dar suporte a uma versão mais recente da API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>O que há de novo na versão 9
A versão 9 do SDK do .NET Azure Search tem como alvo a versão mais recente disponível da API REST do Azure Search, especificamente 2019-05-06. Isso possibilita o uso de novos recursos do Azure Search de um aplicativo .NET, incluindo o seguinte:

* O [enriquecimento de ia](cognitive-search-concept-intro.md) é a capacidade de extrair texto de imagens, BLOBs e outras fontes de dados não estruturadas – enriquecendo o conteúdo para torná-lo mais pesquisável em um índice de Azure Search.
* O suporte para [tipos complexos](search-howto-complex-data-types.md) permite modelar quase todas as estruturas JSON aninhadas em um índice de Azure Search.
* O [preenchimento automático](search-autocomplete-tutorial.md) fornece uma alternativa para a API de **sugestão** para implementar o comportamento de pesquisa conforme o tipo. Preenchimento automático "conclui" a palavra ou frase que um usuário está digitando no momento.
* O [modo de análise de JsonLines](search-howto-index-json-blobs.md), parte da indexação de BLOBs do Azure, cria um documento de pesquisa por entidade JSON que é separado por uma nova linha.

### <a name="new-preview-features-in-version-80-preview"></a>Novos recursos de visualização na versão 8,0-Preview
Versão 8,0-visualização da API de destinos do SDK do .NET Azure Search versão 2017-11-11-Preview. Esta versão inclui todos os mesmos recursos da versão 9, além de:

* [As chaves de criptografia gerenciadas pelo cliente](search-security-manage-encryption-keys.md) para criptografia do lado do serviço em repouso são um novo recurso de visualização. Além da criptografia interna em repouso gerenciada pela Microsoft, você pode aplicar uma camada adicional de criptografia em que você é o único proprietário das chaves.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas para atualizar
Primeiro, atualize a referência do NuGet para `Microsoft.Azure.Search` usando o console do Gerenciador de pacotes NuGet ou clicando com o botão direito do mouse nas referências do projeto e selecionando "gerenciar pacotes NuGet..." no Visual Studio.

Depois que o NuGet tiver baixado os novos pacotes e suas dependências, recompile o projeto. Dependendo de como seu código é estruturado, ele pode ser recriado com êxito. Nesse caso, você está pronto para começar!

Se sua compilação falhar, você precisará corrigir cada erro de compilação. Consulte [alterações significativas na versão 9](#ListOfChanges) para obter detalhes sobre como resolver cada erro de compilação em potencial.

Você pode ver avisos de compilação adicionais relacionados a propriedades ou métodos obsoletos. Os avisos incluirão instruções sobre o que usar em vez do recurso preterido. Por exemplo, se seu aplicativo usar a propriedade `DataSourceType.DocumentDb`, você deverá receber um aviso dizendo que "Este membro foi preterido. Use CosmosDb em vez disso ".

Depois de corrigir quaisquer erros ou avisos de compilação, você poderá fazer alterações em seu aplicativo para tirar proveito da nova funcionalidade, se desejar. Os novos recursos do SDK são detalhados no [que há de novo na versão 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Alterações recentes na versão 9

Há várias alterações significativas na versão 9 que podem exigir alterações de código, além de recompilar seu aplicativo.

> [!NOTE]
> A lista de alterações abaixo não é exaustiva. Algumas alterações provavelmente não resultarão em erros de compilação, mas são tecnicamente quebradas, pois elas violam a compatibilidade binária com assemblies que dependem de versões anteriores do Azure Search assemblies do SDK do .NET. Essas alterações não estão listadas abaixo. Recompile seu aplicativo ao atualizar para a versão 9 para evitar problemas de compatibilidade binária.

### <a name="immutable-properties"></a>Propriedades imutáveis

As propriedades públicas de várias classes de modelo agora são imutáveis. Se você precisar criar instâncias personalizadas dessas classes para teste, poderá usar os novos construtores com parâmetros:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Alterações no campo

A classe `Field` foi alterada agora que ela também pode representar campos complexos.

As propriedades de `bool` a seguir agora são anuláveis:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Isso ocorre porque essas propriedades agora devem ser `null` no caso de campos complexos. Se você tiver um código que leia essas propriedades, ele precisa estar preparado para lidar com `null`. Observe que todas as outras propriedades de `Field` sempre foram e continuam anuláveis, e algumas delas também serão `null` no caso de campos complexos, especificamente o seguinte:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

O construtor sem parâmetros de `Field` foi feito `internal`. De agora em diante, cada `Field` requer um nome explícito e um tipo de dados no momento da construção.

### <a name="simplified-batch-and-results-types"></a>Tipos de resultados e lote simplificados

Na versão 7,0-Preview e versões anteriores, as várias classes que encapsulam grupos de documentos foram estruturadas em hierarquias de classe paralelas:

  -  `DocumentSearchResult` e `DocumentSearchResult<T>` herdados de `DocumentSearchResultBase`
  -  `DocumentSuggestResult` e `DocumentSuggestResult<T>` herdados de `DocumentSuggestResultBase`
  -  `IndexAction` e `IndexAction<T>` herdados de `IndexActionBase`
  -  `IndexBatch` e `IndexBatch<T>` herdados de `IndexBatchBase`
  -  `SearchResult` e `SearchResult<T>` herdados de `SearchResultBase`
  -  `SuggestResult` e `SuggestResult<T>` herdados de `SuggestResultBase`

Os tipos derivados sem um parâmetro de tipo genérico foram destinados a serem usados em cenários de "tipo dinâmico" e uso assumido do tipo de `Document`.

A partir da versão 8,0-Preview, as classes base e as classes derivadas não genéricas foram removidas. Para cenários de tipo dinâmico, você pode usar `IndexBatch<Document>`, `DocumentSearchResult<Document>`e assim por diante.
 
### <a name="removed-extensibleenum"></a>ExtensibleEnum removido

A classe base `ExtensibleEnum` foi removida. Todas as classes que derivam dela agora são structs, como `AnalyzerName`, `DataType`e `DataSourceType`, por exemplo. Seus métodos de `Create` também foram removidos. Você pode apenas remover chamadas para `Create`, já que esses tipos são implicitamente conversíveis de cadeias de caracteres. Se isso resultar em erros de compilador, você poderá invocar explicitamente o operador de conversão via conversão para tipos de ambiguidade. Por exemplo, você pode alterar um código como este:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

para este:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

As propriedades que mantinham valores opcionais desses tipos agora são explicitamente digitadas como anuláveis para que continuem a ser opcionais.

### <a name="removed-facetresults-and-hithighlights"></a>FacetResults e HitHighlights removidos

As classes `FacetResults` e `HitHighlights` foram removidas. Os resultados da faceta agora são digitados como `IDictionary<string, IList<FacetResult>>` e os realces de clique como `IDictionary<string, IList<string>>`. Uma maneira rápida de resolver erros de compilação apresentados por essa alteração é adicionar `using` aliases na parte superior de cada arquivo que usa os tipos removidos. Por exemplo:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Alterar para SynonymMap 

O construtor de `SynonymMap` não tem mais um parâmetro `enum` para `SynonymMapFormat`. Esse enum tem apenas um valor e, portanto, era redundante. Se você vir erros de compilação como resultado disso, simplesmente remova as referências ao parâmetro `SynonymMapFormat`.

### <a name="miscellaneous-model-class-changes"></a>Alterações de classe de modelo diversas

A propriedade `AutocompleteMode` de `AutocompleteParameters` não é mais anulável. Se você tiver um código que atribui essa propriedade a `null`, poderá simplesmente removê-la e a propriedade será inicializada automaticamente para o valor padrão.

A ordem dos parâmetros para o construtor de `IndexAction` foi alterada agora que esse construtor é gerado automaticamente. Em vez de usar o construtor, é recomendável usar os métodos de fábrica `IndexAction.Upload`, `IndexAction.Merge`e assim por diante.

### <a name="removed-preview-features"></a>Recursos de visualização removidos

Se você estiver atualizando da versão 8,0-Preview para a versão 9, lembre-se de que a criptografia com chaves gerenciadas pelo cliente foi removida, pois esse recurso ainda está em versão prévia. Especificamente, as propriedades `EncryptionKey` de `Index` e `SynonymMap` foram removidas.

Se seu aplicativo tiver uma dependência difícil desse recurso, você não poderá atualizar para a versão 9 do SDK do .NET Azure Search. Você pode continuar a usar a versão 8,0-Preview. No entanto, tenha em mente que não **recomendamos o uso de SDKs de visualização em aplicativos de produção**. Os recursos de visualização são apenas para avaliação e podem ser alterados.

> [!NOTE]
> Se você criou índices criptografados ou mapas de sinônimos usando a versão 8,0-preview do SDK, ainda poderá usá-los e modificar suas definições usando a versão 9 do SDK sem afetar negativamente seu status de criptografia. A versão 9 do SDK não enviará a propriedade `encryptionKey` para a API REST e, como resultado, a API REST não alterará o status de criptografia do recurso. 

### <a name="behavioral-change-in-data-retrieval"></a>Alteração comportamental na recuperação de dados

Se você estiver usando as APIs de `Search`"tipado dinamicamente", `Suggest`ou `Get` que retornam instâncias do tipo `Document`, lembre-se de que agora desserializar as matrizes JSON vazias para `object[]` em vez de `string[]`.

## <a name="conclusion"></a>Conclusão
Se você precisar de mais detalhes sobre como usar o SDK do .NET Azure Search, consulte o [instruções .net](search-howto-dotnet-sdk.md).

Agradecemos seus comentários sobre o SDK. Se você encontrar problemas, sinta-se à vontade para nos pedir ajuda sobre [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se você encontrar um bug, poderá arquivar um problema no [repositório GitHub do SDK do .net do Azure](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de prefixar o título do problema com "[Azure Search]".

Obrigado por usar Azure Search!
