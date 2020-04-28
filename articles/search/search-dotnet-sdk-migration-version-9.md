---
title: Upgrade para Azure Search .NET SDK versão 9
titleSuffix: Azure Cognitive Search
description: Migrar código para o Azure Search .NET SDK versão 9 a partir de versões mais antigas. Saiba o que é novo e quais as alterações de código que são necessárias.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcc70267754f7e66f29dd1b855d3efb8b814e78b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72793005"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Upgrade para Azure Search .NET SDK versão 9

Se estiver a utilizar a versão 7.0-preview ou mais antiga do [Azure Search .NET SDK,](https://aka.ms/search-sdk)este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 9.

> [!NOTE]
> Se desejar utilizar a versão 8.0-preview para avaliar funcionalidades que ainda não estão disponíveis, também pode seguir as instruções deste artigo para atualizar para 8.0-preview de versões anteriores.

Para uma passagem mais geral do SDK, incluindo exemplos, consulte [Como utilizar a Pesquisa Azure a partir de uma aplicação .NET](search-howto-dotnet-sdk.md).

A versão 9 do Azure Search .NET SDK contém muitas alterações de versões anteriores. Algumas destas alterações são de rutura, mas só devem exigir alterações relativamente pequenas no seu código. Consulte [passos para atualizar](#UpgradeSteps) para obter instruções sobre como alterar o seu código para utilizar a nova versão SDK.

> [!NOTE]
> Se estiver a utilizar a versão 4.0-preview ou mais antiga, deve fazer o upgrade para a versão 5 primeiro e, em seguida, atualizar para a versão 9. Consulte [o Upgrade para a versão 5 do Azure Search .NET SDK](search-dotnet-sdk-migration-version-5.md) para obter instruções.
>
> A sua instância de serviço Azure Search suporta várias versões REST API, incluindo a mais recente. Pode continuar a utilizar uma versão quando já não é a mais recente, mas recomendamos que emigra o seu código para utilizar a versão mais recente. Ao utilizar a API REST, deve especificar a versão API em todos os pedidos através do parâmetro da versão api. Ao utilizar o .NET SDK, a versão do SDK que está a utilizar determina a versão correspondente da API REST. Se estiver a utilizar um SDK mais antigo, pode continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para suportar uma versão API mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>O que há de novo na versão 9
A versão 9 do Azure Search .NET SDK tem como alvo a mais recente versão geralmente disponível da API de Pesquisa Azure, especificamente 2019-05-06. Isto permite utilizar novas funcionalidades da Pesquisa Azure a partir de uma aplicação .NET, incluindo as seguintes:

* [O enriquecimento de IA](cognitive-search-concept-intro.md) é a capacidade de extrair texto de imagens, bolhas e outras fontes de dados não estruturadas - enriquecendo o conteúdo para torná-lo mais pesquisável num índice de Pesquisa Azure.
* O suporte para [tipos complexos](search-howto-complex-data-types.md) permite-lhe modelar quase qualquer estrutura JSON aninhada num índice de Pesquisa Azure.
* [A Autocomplete](search-autocomplete-tutorial.md) fornece uma alternativa à **API sugerir** para implementar comportamentos de pesquisa como o seu tipo. Complete automaticamente "termina" a palavra ou frase que um utilizador está atualmente a escrever.
* O [modo de análise JsonLines,](search-howto-index-json-blobs.md)parte da indexação do Azure Blob, cria um documento de pesquisa por entidade JSON que é separado por uma novalinha.

### <a name="new-preview-features-in-version-80-preview"></a>Novas funcionalidades de pré-visualização na versão 8.0-preview
Versão 8.0-pré-visualização do Azure Search .NET SDK tem como alvo a versão API 2017-11-11-Preview. Esta versão inclui todas as mesmas funcionalidades da versão 9, além de:

* As [chaves de encriptação geridas pelo cliente](search-security-manage-encryption-keys.md) para encriptação do lado do serviço em repouso é uma nova funcionalidade de pré-visualização. Além da encriptação incorporada gerida pela Microsoft, pode aplicar uma camada adicional de encriptação onde é o único proprietário das chaves.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Em primeiro lugar, atualize a sua referência NuGet para `Microsoft.Azure.Search` utilizar a Consola nuGet Package Manager ou clicando corretamente nas referências do seu projeto e selecionando "Gerir pacotes NuGet..." no Estúdio Visual.

Assim que o NuGet tiver descarregado os novos pacotes e as suas dependências, reconstrua o seu projeto. Dependendo da forma como o seu código é estruturado, pode reconstruir com sucesso. Se assim for, está pronto para ir!

Se a sua construção falhar, terá de corrigir cada erro de construção. Consulte [as alterações de Rutura na versão 9](#ListOfChanges) para obter detalhes sobre como resolver cada erro de construção potencial.

Pode ver avisos adicionais de construção relacionados com métodos ou propriedades obsoletos. As advertências incluirão instruções sobre o que usar em vez da função depreciada. Por exemplo, se a `DataSourceType.DocumentDb` sua aplicação usar a propriedade, deve receber um aviso que diz "Este membro é depreciado. Use cosmosDb em vez disso".

Depois de corrigir quaisquer erros de construção ou avisos, pode fazer alterações na sua aplicação para tirar partido de novas funcionalidades, se desejar. As novas funcionalidades no SDK são detalhadas no [What's new in version 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Quebrar alterações na versão 9

Existem várias alterações de quebra na versão 9 que podem exigir alterações de código para além de reconstruir a sua aplicação.

> [!NOTE]
> A lista de alterações abaixo não é exaustiva. Algumas alterações provavelmente não resultarão em erros de construção, mas estão tecnicamente a quebrar uma vez que quebram a compatibilidade binária com conjuntos que dependem de versões anteriores dos conjuntos Azure Search .NET SDK. Tais alterações não estão listadas abaixo. Por favor, reconstrua a sua aplicação ao atualizar para a versão 9 para evitar quaisquer problemas de compatibilidade binários.

### <a name="immutable-properties"></a>Propriedades imutáveis

As propriedades públicas de várias classes modelo são agora imutáveis. Se precisar de criar instâncias personalizadas destas classes para testes, pode utilizar os novos construtores parametrizados:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Alterações ao Campo

A `Field` classe mudou agora que também pode representar campos complexos.

As `bool` seguintes propriedades são agora anuladas:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Isto porque estas propriedades `null` devem agora ser no caso de campos complexos. Se tem um código que lê estas propriedades, `null`tem de estar preparado para manusear . Note que todas `Field` as outras propriedades sempre foram e continuam a `null` ser anuladas, e algumas delas também serão no caso de campos complexos - especificamente o seguinte:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

O construtor sem `Field` parâmetros `internal`foi feito. A partir de `Field` agora, todos requerem um nome explícito e tipo de dados no momento da construção.

### <a name="simplified-batch-and-results-types"></a>Tipos de lote e resultados simplificados

Na versão 7.0-pré-visualização e anterior, as várias classes que encapsulam grupos de documentos foram estruturadas em hierarquias de classes paralelas:

  -  `DocumentSearchResult`e `DocumentSearchResult<T>` herdado de`DocumentSearchResultBase`
  -  `DocumentSuggestResult`e `DocumentSuggestResult<T>` herdado de`DocumentSuggestResultBase`
  -  `IndexAction`e `IndexAction<T>` herdado de`IndexActionBase`
  -  `IndexBatch`e `IndexBatch<T>` herdado de`IndexBatchBase`
  -  `SearchResult`e `SearchResult<T>` herdado de`SearchResultBase`
  -  `SuggestResult`e `SuggestResult<T>` herdado de`SuggestResultBase`

Os tipos derivados sem parâmetro de tipo genérico destinavam-se a ser utilizados `Document` em cenários "dinamicamente digitados" e assumiram a utilização do tipo.

Começando com a versão 8.0-pré-visualização, as classes base e as classes derivadas não genéricas foram todas removidas. Para cenários dinamicamente dactilografados, pode utilizar, `IndexBatch<Document>` `DocumentSearchResult<Document>`e assim por diante.
 
### <a name="removed-extensibleenum"></a>ExtensibleEnum removido

A `ExtensibleEnum` classe base foi removida. Todas as classes que dela derivam são agora `AnalyzerName` `DataType`estruturadas, como, por exemplo, e `DataSourceType` por exemplo. Os `Create` seus métodos também foram removidos. Pode apenas remover `Create` chamadas, uma vez que estes tipos são implicitamente convertíveis de cordas. Se isso resultar em erros compiladores, pode invocar explicitamente o operador de conversão através de fundição para desambiguar tipos. Por exemplo, pode alterar código assim:

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

As propriedades que detinham valores opcionais deste tipo são agora explicitamente dactilografadas como nulidades, pelo que continuam a ser opcionais.

### <a name="removed-facetresults-and-hithighlights"></a>FacetResults removidos e hitHighlights

As `FacetResults` `HitHighlights` aulas e as aulas foram removidas. Os resultados da faceta são agora dactilografados como `IDictionary<string, IList<FacetResult>>` e acertam os destaques como `IDictionary<string, IList<string>>`. Uma forma rápida de resolver os erros de `using` construção introduzidos por esta alteração é adicionar pseudónimos na parte superior de cada ficheiro que utiliza os tipos removidos. Por exemplo:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Alteração para SynonymMap 

O `SynonymMap` construtor já não `enum` tem `SynonymMapFormat`um parâmetro para . Este enum só tinha um valor, e por isso foi redundante. Se vir erros de construção como resultado disso, `SynonymMapFormat` basta remover referências ao parâmetro.

### <a name="miscellaneous-model-class-changes"></a>Mudanças de classe de modelos diversos

A `AutocompleteMode` propriedade `AutocompleteParameters` de já não é nula. Se tiver um código que `null`atribui esta propriedade, pode simplesmente removê-la e a propriedade será automaticamente inicializada ao valor padrão.

A ordem dos parâmetros `IndexAction` para o construtor mudou agora que este construtor é gerado automaticamente. Em vez de utilizar o construtor, `IndexAction.Upload`recomendamos a utilização dos métodos de fábrica, `IndexAction.Merge`e assim por diante.

### <a name="removed-preview-features"></a>Funcionalidades de pré-visualização removidas

Se estiver a atualizar da versão 8.0-preview para a versão 9, esteja ciente de que a encriptação com chaves geridas pelo cliente foi removida uma vez que esta funcionalidade ainda está em pré-visualização. Especificamente, `EncryptionKey` as `Index` `SynonymMap` propriedades de e foram removidas.

Se a sua aplicação tiver uma dependência dura desta funcionalidade, não poderá fazer o upgrade para a versão 9 do Azure Search .NET SDK. Pode continuar a utilizar a versão 8.0-preview. No entanto, tenha em mente que **não recomendamos a utilização de SDKs de pré-visualização em aplicações**de produção . As funcionalidades de pré-visualização são apenas para avaliação e podem mudar.

> [!NOTE]
> Se criou índices encriptados ou mapas de sinónimo utilizando a versão 8.0-preview do SDK, ainda poderá utilizá-los e modificar as suas definições utilizando a versão 9 do SDK sem afetar negativamente o seu estado de encriptação. A versão 9 do SDK `encryptionKey` não enviará a propriedade para a API REST, e como resultado, a API REST não alterará o estado de encriptação do recurso. 

### <a name="behavioral-change-in-data-retrieval"></a>Mudança comportamental na recuperação de dados

Se estiver a `Search`utilizar as "disctiladas dinamicamente", `Suggest`ou `Get` APIs que devolvem instâncias de tipo, `Document`esteja ciente de que agora desserializam as matrizes JSON vazias para `object[]` em vez de `string[]`.

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre a utilização do Azure Search .NET SDK, consulte o [.NET How-to](search-howto-dotnet-sdk.md).

Congratulamo-nos com o seu feedback sobre o SDK. Se encontrar problemas, sinta-se à vontade para nos pedir ajuda no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se encontrar um bug, pode apresentar um problema no [repositório Azure.NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de pré-fixar o título de emissão com "[Pesquisa Azure]".

Obrigado por usar a Pesquisa Azure!
