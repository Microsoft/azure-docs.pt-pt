---
title: Atualizar para o SDK .NET da Azure Search versão 9 - Azure Search
description: Migre o código para o SDK .NET da Azure Search versão 9 de versões mais antigas. Saiba quais são as novidades e quais alterações de código são necessárias.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: a59deed4ac0cec669ddc5e0335f7274586c702e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65541768"
---
# <a name="upgrade-to-the-azure-search-net-sdk-version-9"></a>Atualizar para o SDK .NET da Azure Search versão 9

Se estiver a utilizar versão 7.0-pré-visualização ou mais antiga do [SDK .NET da Azure Search](https://aka.ms/search-sdk), este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 9.

> [!NOTE]
> Se pretender utilizar a versão 8.0-pré-visualização para avaliar os recursos que ainda não estão em disponibilidade geral, também pode seguir as instruções neste artigo para atualizar 8.0-pré-visualização às versões anteriores.

Para obter instruções mais geral do SDK, incluindo exemplos, consulte [como utilizar o Azure Search a partir de um aplicativo .NET](search-howto-dotnet-sdk.md).

Versão 9 do Azure Search .NET SDK contém muitas alterações de versões anteriores. Algumas dessas alterações são significativas, mas eles só devem exigir feitas pequenas alterações no seu código. Ver [passos para atualizar](#UpgradeSteps) para obter instruções sobre como alterar o seu código para utilizar a nova versão do SDK.

> [!NOTE]
> Se estiver usando a versão 4.0-pré-visualização ou mais antigo, deve atualizar primeiro para a versão 5 e, em seguida, atualize para a versão 9. Ver [atualizar para o SDK .NET da Azure Search versão 5](search-dotnet-sdk-migration-version-5.md) para obter instruções.
>
> A instância de serviço do Azure Search oferece suporte a várias versões de REST API, incluindo o mais recente. Pode continuar a utilizar uma versão quando ele é já não é a mais recente, mas recomendamos que migre o código para usar a versão mais recente. Ao utilizar a API REST, tem de especificar a versão de API em cada solicitação por meio do parâmetro de versão de api. Quando utilizar o SDK de .NET, a versão do SDK que está a utilizar determina a versão da REST API correspondente. Se estiver a utilizar um SDK mais antigo, pode continuar a executar esse código sem alterações, mesmo que o serviço é atualizado para suportar uma versão mais recente do API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>O que há de novo na versão 9
Versão 9 do SDK de .NET de pesquisa do Azure destina-se a versão mais recente versão disponível em geral da API de REST de pesquisa do Azure, especificamente 2019-05-06. Isto torna possível usar novos recursos do Azure Search a partir de um aplicativo .NET, incluindo o seguinte:

* [Pesquisa cognitiva](cognitive-search-concept-intro.md) é uma funcionalidade de IA no Azure Search, utilizado para extrair texto de imagens, blobs e outras origens de dados não estruturados - aprimorando o conteúdo para torná-lo mais pesquisáveis no índice da Azure Search.
* Suporte para [tipos complexos](search-howto-complex-data-types.md) , pode modelar quase qualquer estrutura JSON aninhada num índice da Azure Search.
* [Preenchimento automático](search-autocomplete-tutorial.md) fornece uma alternativa para o **sugere** API para implementar o comportamento de pesquisa-como--type. Preenchimento automático "termina" a palavra ou expressão que um usuário está digitando atualmente.
* [Modo de análise de JsonLines](search-howto-index-json-blobs.md), parte de Blobs do Azure, indexação, cria um documento de pesquisa por entidade JSON que é separado por um caractere de nova linha.

### <a name="new-preview-features-in-version-80-preview"></a>Novas funcionalidades de pré-visualização na versão 8.0-pré-visualização
Versão 8.0-pré-visualização do Azure Search .NET SDK destina-se versão 2017-11-11-a pré-visualização da API. Esta versão inclui as mesmas funcionalidades da versão 9, além de:

* [Chaves de encriptação gerida pelo cliente](search-security-manage-encryption-keys.md) para o lado do serviço de encriptação em repouso é uma nova funcionalidade de pré-visualização. Para além do incorporada encriptação em repouso gerenciado pela Microsoft, pode aplicar uma camada adicional de onde está a única proprietária das chaves de encriptação.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Em primeiro lugar, atualizar a sua referência de NuGet para `Microsoft.Azure.Search` utilizando a consola de Gestor de pacotes NuGet ou ao clicar com o botão direito em suas referências de projeto e selecionando "Gerir NuGet pacotes..." no Visual Studio.

Assim que o NuGet tiver baixado os novos pacotes e suas dependências, recrie seu projeto. Dependendo de como o seu código está estruturado, ele poderá reconstruir os com êxito. Nesse caso, está pronto para começar!

Se sua compilação falhar, terá de corrigir cada erro de compilação. Ver [alterações significativas na versão 9](#ListOfChanges) para obter detalhes sobre como resolver cada potencial erro de compilação.

Poderá ver avisos de compilação adicionais relacionados com a propriedades ou métodos obsoletos. Os avisos inclui instruções sobre o que utilizar em vez da funcionalidade despromovida. Por exemplo, se a sua aplicação utiliza o `DataSourceType.DocumentDb` propriedade, obterá um aviso que diz "este membro é preterido. Utilize o cosmos DB em vez disso,".

Uma vez que corrigiu os erros de compilação e avisos, pode efetuar alterações à sua aplicação para tirar partido das novas funcionalidades, se desejar. Novas funcionalidades no SDK são detalhadas no [quais são as novidades na versão 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Alterações significativas na versão 9

Existem várias alterações de última hora na versão 9 que pode necessitar de alterações de código, além de reconstruir a sua aplicação.

> [!NOTE]
> A lista de alterações abaixo não é exaustiva. Algumas alterações provavelmente não resultará em erros de compilação, mas são tecnicamente significativas, uma vez que eles quebraram a compatibilidade binária com assemblies que dependem de versões anteriores dos assemblies do SDK .NET da Azure Search. Essas alterações não estão listadas abaixo. Recrie seu aplicativo ao atualizar para versão 9 para evitar quaisquer problemas de compatibilidade binária.

### <a name="immutable-properties"></a>Propriedades imutáveis

As propriedades públicas de várias classes de modelo agora são imutáveis. Se precisar de criar personalizadas instâncias dessas classes para fins de teste, pode usar os novos construtores parametrizados:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Alterações ao campo

O `Field` classe foi alterado, agora que também possa representar campos complexos.

O seguinte `bool` propriedades agora são anuláveis:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Isto acontece porque essas propriedades agora tem de ser `null` no caso de campos complexos. Se tiver o código que lê essas propriedades, tem de ser preparado para lidar com `null`. Tenha em atenção que todas as outras propriedades de `Field` sempre foram e continuam a ser anulável, e alguns desses também será `null` no caso de campos complexos, especificamente o seguinte:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

O construtor sem parâmetros de `Field` tornou `internal`. De agora em diante, cada `Field` requer um tipo de dados e o nome explícito no momento da construção.

### <a name="simplified-batch-and-results-types"></a>Tipos de batch e os resultados simplificados

Na versão 7.0-pré-visualização e anterior, as diversas classes que encapsulam os grupos de documentos foram estruturadas em hierarquias de classe parallel:

  -  `DocumentSearchResult` e `DocumentSearchResult<T>` herdado de `DocumentSearchResultBase`
  -  `DocumentSuggestResult` e `DocumentSuggestResult<T>` herdado de `DocumentSuggestResultBase`
  -  `IndexAction` e `IndexAction<T>` herdado de `IndexActionBase`
  -  `IndexBatch` e `IndexBatch<T>` herdado de `IndexBatchBase`
  -  `SearchResult` e `SearchResult<T>` herdado de `SearchResultBase`
  -  `SuggestResult` e `SuggestResult<T>` herdado de `SuggestResultBase`

Os tipos derivados sem um parâmetro de tipo genérico foram indicados para ser usado em cenários de "dinâmicos" e pressupõe-se a utilização do `Document` tipo.

A partir da versão 8.0-preview, as base classes e classes derivadas de não-genérica todos foram removidos. Para cenários dinâmicos, pode utilizar `IndexBatch<Document>`, `DocumentSearchResult<Document>`e assim por diante.
 
### <a name="removed-extensibleenum"></a>ExtensibleEnum removido

O `ExtensibleEnum` classe base foi removido. Todas as classes derivam de ele agora são estruturas, como `AnalyzerName`, `DataType`, e `DataSourceType` por exemplo. Seus `Create` métodos também foram removidos. Apenas pode remover chamadas para `Create` uma vez que esses tipos são implicitamente convertidos de cadeias de caracteres. Se o que resulta em erros do compilador, pode invocar explicitamente o operador de conversão por meio de transmissão para eliminar a ambiguidade tipos. Por exemplo, pode alterar o código como este:

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

Propriedades que mantidos valores opcionais desses tipos são agora explicitamente o tipo que pode ser nulo para que eles continuam sendo opcional.

### <a name="removed-facetresults-and-hithighlights"></a>FacetResults removidos e HitHighlights

O `FacetResults` e `HitHighlights` classes foram removidas. Agora tipificados como resultados de faceta `IDictionary<string, IList<FacetResult>>` e prima realça como `IDictionary<string, IList<string>>`. Uma forma rápida de resolver erros de compilação introduzidos por esta alteração é adicionar `using` aliases na parte superior de cada arquivo que usa os tipos foi removidos. Por exemplo:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Alterar para SynonymMap 

O `SynonymMap` construtor já não tem uma `enum` parâmetro para `SynonymMapFormat`. Essa enumeração apenas tinha um valor e, portanto, era redundante. Se vir erros como resultado de compilação, simplesmente remover as referências para o `SynonymMapFormat` parâmetro.

### <a name="miscellaneous-model-class-changes"></a>Alterações de classe de modelo diversos

O `AutocompleteMode` propriedade do `AutocompleteParameters` já não pode ser nula. Se tiver o código que atribui essa propriedade `null`, simplesmente pode removê-lo e a propriedade será inicializada automaticamente para o valor predefinido.

A ordem dos parâmetros para o `IndexAction` construtor mudou agora que esse construtor é gerado automaticamente. Em vez de usar o construtor, recomendamos que utilize os métodos de fábrica `IndexAction.Upload`, `IndexAction.Merge`e assim por diante.

### <a name="removed-preview-features"></a>Funcionalidades de pré-visualização foi removido

Se estiver a atualizar da versão 8.0-pré-visualização para a versão 9, tenha em atenção que a encriptação com chaves geridas pelo cliente foi removida, uma vez que esta funcionalidade ainda está em pré-visualização. Especificamente, o `EncryptionKey` propriedades de `Index` e `SynonymMap` foram removidos.

Se seu aplicativo tiver uma dependência sobre esta funcionalidade, não será capaz de atualizar para a versão 9 do SDK de .NET de pesquisa do Azure. Pode continuar a utilizar a versão 8.0-preview. No entanto,. tenha em atenção que **Recomendamos que não utilize SDKs de pré-visualização em aplicações de produção**. Funcionalidades de pré-visualização destinam-se apenas de avaliação e pode ser alterada.

> [!NOTE]
> Se tiver criado encriptados índices ou sinónimo mapas usando a versão 8.0-pré-visualização do SDK, continuará a poder utilizá-los e modificar as respetivas definições a utilizar a versão 9 do SDK sem afetar negativamente o respetivo estado de encriptação. Versão 9 do SDK não envia o `encryptionKey` propriedade para a API REST e, como resultado da API REST não alterará o estado de encriptação do recurso. 

### <a name="behavioral-change-in-data-retrieval"></a>Alteração comportamental na obtenção de dados

Se estiver a utilizar o "tipo dinâmico" `Search`, `Suggest`, ou `Get` APIs que retornam instâncias do tipo `Document`, lembre-se de que eles agora anular a serialização matrizes JSON vazias `object[]` em vez de `string[]`.

## <a name="conclusion"></a>Conclusão
Se precisar de obter mais detalhes sobre como utilizar o SDK .NET da Azure Search, consulte a [.NET procedimentos](search-howto-dotnet-sdk.md).

Apreciamos os seus comentários sobre o SDK. Se tiver problemas, fique à vontade contacte-nos para obter ajuda na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se encontrar um bug, comunique o assunto na [repositório do GitHub do Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se para o prefixo seu título do problema com o "[o Azure Search]".

Obrigado por utilizar o Azure Search!
