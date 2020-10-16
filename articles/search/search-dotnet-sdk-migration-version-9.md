---
title: Upgrade para Azure Search .NET SDK versão 9
titleSuffix: Azure Cognitive Search
description: Migrar o código para a versão 9 do Azure Search .NET SDK a partir de versões mais antigas. Saiba o que é novo e quais as alterações de código necessárias.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 7726877efcb72caf5bb4c92e2e1c65e5df68f270
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89002713"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Upgrade para Azure Search .NET SDK versão 9

Se estiver a utilizar a versão 7.0 pré-visualização ou mais antiga do [Azure Search .NET SDK,](/dotnet/api/overview/azure/search)este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 9.

> [!NOTE]
> Se pretender utilizar a versão 8.0-pré-visualização para avaliar funcionalidades que não estão geralmente disponíveis ainda, também pode seguir as instruções deste artigo para atualizar para 8.0-pré-visualização de versões anteriores.

Para uma passagem mais geral do SDK, incluindo exemplos, consulte [Como utilizar a Azure Search a partir de uma aplicação .NET](search-howto-dotnet-sdk.md).

A versão 9 do Azure Search .NET SDK contém muitas alterações em versões anteriores. Algumas destas alterações estão a quebrar alterações, mas devem exigir apenas alterações relativamente pequenas no seu código. Consulte [passos para atualizar](#UpgradeSteps) para obter instruções sobre como alterar o seu código para utilizar a nova versão SDK.

> [!NOTE]
> Se estiver a utilizar a versão 4.0 pré-visualização ou mais antiga, deve atualizar para a versão 5 primeiro e, em seguida, atualizar para a versão 9. Consulte [a atualização para a versão 5 da Azure Search .NET SDK](search-dotnet-sdk-migration-version-5.md) para obter instruções.
>
> A sua instância do serviço Azure Search suporta várias versões REST API, incluindo a mais recente. Pode continuar a utilizar uma versão quando já não é a mais recente, mas recomendamos que emigre o seu código para utilizar a versão mais recente. Ao utilizar a API REST, deve especificar a versão API em cada pedido através do parâmetro da versão API. Ao utilizar o .NET SDK, a versão do SDK que está a utilizar determina a versão correspondente da API REST. Se estiver a utilizar um SDK mais antigo, pode continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para suportar uma versão API mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Novidades na versão 9
Versão 9 do Azure Search .NET SDK tem como alvo a versão 2019-05-06 da Azure Search REST API, com as seguintes funcionalidades:

* [O enriquecimento de IA](cognitive-search-concept-intro.md) é a capacidade de extrair texto de imagens, bolhas e outras fontes de dados não estruturadas - enriquecendo o conteúdo para torná-lo mais pes pescável num índice de Pesquisa de Azure.
* O suporte para [tipos complexos](search-howto-complex-data-types.md) permite-lhe modelar quase todas as estruturas JSON aninhadas num índice de Pesquisa Azure.
* [O autocomplete](search-autocomplete-tutorial.md) fornece uma alternativa à **API sugestiva** para implementar comportamentos de pesquisa como você. "Finishe".
* [O modo de análise JsonLines](search-howto-index-json-blobs.md), parte da indexação Azure Blob, cria um documento de pesquisa por entidade JSON que é separado por uma nova linha.

### <a name="new-preview-features-in-version-80-preview"></a>Novas funcionalidades de pré-visualização na versão 8.0-preview
Versão 8.0-pré-visualização do Azure Search .NET SDK visa a versão API 2017-11-11-Preview. Esta versão inclui todas as mesmas funcionalidades da versão 9, mais:

* [As chaves de encriptação geridas pelo cliente](search-security-manage-encryption-keys.md) para encriptação do lado do serviço em repouso é uma nova funcionalidade de pré-visualização. Além da encriptação incorporada em repouso gerida pela Microsoft, pode aplicar uma camada adicional de encriptação onde é o único proprietário das chaves.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Em primeiro lugar, atualize a sua referência NuGet para `Microsoft.Azure.Search` utilizar a consola Do Gestor de Pacotes NuGet ou clicando à direita nas referências do seu projeto e selecionando "Gerir pacotes nuget..." em Estúdio Visual.

Assim que o NuGet tiver descarregado os novos pacotes e as suas dependências, reconstrua o seu projeto. Dependendo da estrutura do seu código, pode reconstruir com sucesso. Se sim, está pronto para ir!

Se a sua construção falhar, terá de corrigir cada erro de construção. Consulte [alterações de Breaking na versão 9](#ListOfChanges) para obter detalhes sobre como resolver cada potencial erro de construção.

Pode ver avisos adicionais de construção relacionados com métodos ou propriedades obsoletos. As advertências incluirão instruções sobre o que usar em vez da função prevada. Por exemplo, se a sua aplicação usar a `DataSourceType.DocumentDb` propriedade, deve receber um aviso que diz "Este membro está precotado. Use o CosmosDb em vez disso".

Uma vez corrigido quaisquer erros ou avisos de construção, pode escoar alterações na sua aplicação para tirar partido de novas funcionalidades, se desejar. As novas funcionalidades no SDK são detalhadas no [que há de novo na versão 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Quebrando mudanças na versão 9

Existem várias alterações na versão 9 que podem exigir alterações de código para além de reconstruir a sua aplicação.

> [!NOTE]
> A lista de alterações abaixo não é exaustiva. Algumas alterações provavelmente não resultarão em erros de construção, mas estão tecnicamente a quebrar uma vez que quebram a compatibilidade binária com conjuntos que dependem de versões anteriores dos conjuntos Azure Search .NET SDK. Tais alterações não constam da lista abaixo. Por favor, reconstrua a sua aplicação ao atualizar para a versão 9 para evitar quaisquer problemas de compatibilidade binária.

### <a name="immutable-properties"></a>Propriedades imutáveis

As propriedades públicas de várias classes de modelos são agora imutáveis. Se precisar de criar instâncias personalizadas destas classes para testes, pode utilizar os novos construtores parametrizados:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Alterações no Campo

A `Field` classe mudou agora que também pode representar campos complexos.

As `bool` seguintes propriedades são agora anuladas:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Isto porque estas propriedades devem agora ser `null` no caso de campos complexos. Se tiver um código que leia estas propriedades, tem de estar preparado para manusear `null` . Note que todas as outras propriedades `Field` sempre foram e continuam a ser anulados, e algumas delas também serão no caso de campos `null` complexos - especificamente o seguinte:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

O construtor sem parâmetros `Field` foi `internal` feito. A partir de agora, cada `Field` um requer um nome explícito e um tipo de dados no momento da construção.

### <a name="simplified-batch-and-results-types"></a>Tipos de lote e resultados simplificados

Na versão 7.0-pré-visualização e anterior, as várias classes que encapsulam grupos de documentos foram estruturadas em hierarquias de classe paralelas:

  -  `DocumentSearchResult` e `DocumentSearchResult<T>` herdado de `DocumentSearchResultBase`
  -  `DocumentSuggestResult` e `DocumentSuggestResult<T>` herdado de `DocumentSuggestResultBase`
  -  `IndexAction` e `IndexAction<T>` herdado de `IndexActionBase`
  -  `IndexBatch` e `IndexBatch<T>` herdado de `IndexBatchBase`
  -  `SearchResult` e `SearchResult<T>` herdado de `SearchResultBase`
  -  `SuggestResult` e `SuggestResult<T>` herdado de `SuggestResultBase`

Os tipos derivados sem um parâmetro genérico do tipo destinavam-se a ser utilizados em cenários "de tipo dinâmico" e assumiam a utilização do `Document` tipo.

Começando pela versão 8.0-pré-visualização, as classes base e as classes derivadas não genéricas foram todas removidas. Para cenários de tipo dinâmico, pode usar `IndexBatch<Document>` , e assim por `DocumentSearchResult<Document>` diante.
 
### <a name="removed-extensibleenum"></a>ExtensibleEnum removido

A `ExtensibleEnum` classe base foi removida. Todas as classes que dele derivaram são agora structs, como `AnalyzerName` `DataType` , e por `DataSourceType` exemplo. Os seus `Create` métodos também foram removidos. Basta remover chamadas para `Create` uma vez que estes tipos são implicitamente descapotáveis das cordas. Se isso resultar em erros de compilador, pode invocar explicitamente o operador de conversão através da fundição para tipos de desambiguados. Por exemplo, pode alterar códigos como este:

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

As propriedades que detinham valores opcionais deste tipo são agora explicitamente digitadas como nulas, pelo que continuam a ser opcionais.

### <a name="removed-facetresults-and-hithighlights"></a>RemovedtResults e HitHighlights

As `FacetResults` `HitHighlights` aulas foram removidas. Os resultados facetas são agora dactilografados como `IDictionary<string, IList<FacetResult>>` e atingem os destaques como `IDictionary<string, IList<string>>` . Uma forma rápida de resolver erros de construção introduzidos por esta alteração é adicionar `using` pseudónimos no topo de cada ficheiro que utiliza os tipos removidos. Por exemplo:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Alteração para SynonymMap 

O `SynonymMap` construtor já não tem um parâmetro para `enum` `SynonymMapFormat` . Este enum tinha apenas um valor, e foi, portanto, redundante. Se vir erros de construção como resultado disto, basta remover referências ao `SynonymMapFormat` parâmetro.

### <a name="miscellaneous-model-class-changes"></a>Mudanças de classe de modelo diversos

A `AutocompleteMode` propriedade de não é mais `AutocompleteParameters` anulado. Se tiver um código que atribui a este `null` imóvel, pode simplesmente removê-lo e a propriedade será automaticamente inicializada ao valor predefinido.

A ordem dos parâmetros para o `IndexAction` construtor mudou agora que este construtor é gerado automaticamente. Em vez de utilizar o construtor, recomendamos a utilização dos métodos de `IndexAction.Upload` `IndexAction.Merge` fábrica, e assim por diante.

### <a name="removed-preview-features"></a>Funcionalidades de pré-visualização removidas

Se estiver a atualizar a partir da versão 8.0 pré-visualização para a versão 9, esteja ciente de que a encriptação com as teclas geridas pelo cliente foi removida uma vez que esta funcionalidade ainda se encontra em pré-visualização. Especificamente, as `EncryptionKey` propriedades de e foram `Index` `SynonymMap` removidas.

Se a sua aplicação tiver uma forte dependência desta funcionalidade, não poderá fazer upgrade para a versão 9 do Azure Search .NET SDK. Pode continuar a utilizar a versão 8.0-pré-visualização. No entanto, tenha em mente que **não recomendamos a utilização de SDKs de pré-visualização em aplicações de produção**. As funcionalidades de pré-visualização são apenas para avaliação e podem ser alteradas.

> [!NOTE]
> Se criou índices encriptados ou mapas de sinónimo utilizando a versão 8.0-pré-visualização do SDK, ainda poderá utilizá-los e modificar as suas definições utilizando a versão 9 do SDK sem afetar negativamente o seu estado de encriptação. A versão 9 do SDK não enviará a `encryptionKey` propriedade para a API REST e, consequentemente, a API REST não alterará o estado de encriptação do recurso. 

### <a name="behavioral-change-in-data-retrieval"></a>Mudança comportamental na recuperação de dados

Se estiver a utilizar as "dinâmicas dactilografada", `Search` `Suggest` ou `Get` APIs que devolvem instâncias do `Document` tipo, saiba que agora deseralizam matrizes JSON vazias para `object[]` em vez de `string[]` .

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre a utilização do Azure Search .NET SDK, consulte o [.NET How-to](search-howto-dotnet-sdk.md).

Congratulamo-nos com o seu feedback sobre o SDK. Se encontrar problemas, sinta-se à vontade para nos pedir ajuda no [Stack Overflow.](https://stackoverflow.com/questions/tagged/azure-search) Se encontrar um bug, pode arquivar um problema no [repositório Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de pré-afixar o seu título de emissão com "[Azure Search]".

Obrigado por usar a Azure Search!