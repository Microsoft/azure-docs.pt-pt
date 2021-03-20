---
title: Upgrade para Azure Search .NET SDK versão 5
titleSuffix: Azure Cognitive Search
description: Migrar o código para a versão 5 do Azure Search .NET SDK a partir de versões mais antigas. Saiba o que é novo e quais as alterações de código necessárias.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7f0e994cd41fc55824cf347a51ccfed25589a310
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91539542"
---
# <a name="upgrade-to-azure-search-net-sdk-version-5"></a>Upgrade para Azure Search .NET SDK versão 5

Se estiver a utilizar a versão 4.0 pré-visualização ou mais antiga do [.NET SDK,](/dotnet/api/overview/azure/search)este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 5.

Para uma passagem mais geral do SDK, incluindo exemplos, consulte [Como utilizar a Azure Search a partir de uma aplicação .NET](search-howto-dotnet-sdk.md).

A versão 5 do Azure Search .NET SDK contém algumas alterações a partir de versões anteriores. Estes são na sua maioria menores, por isso mudar o seu código deve exigir apenas o mínimo de esforço. Consulte [passos para atualizar](#UpgradeSteps) para obter instruções sobre como alterar o seu código para utilizar a nova versão SDK.

> [!NOTE]
> Se estiver a utilizar a versão 2.0 pré-visualização ou mais antiga, deve atualizar para a versão 3 primeiro e, em seguida, atualizar para a versão 5. Consulte [a atualização para a versão 3 da Azure Search .NET SDK](search-dotnet-sdk-migration.md) para obter instruções.
>
> A sua instância do serviço Azure Search suporta várias versões REST API, incluindo a mais recente. Pode continuar a utilizar uma versão quando já não é a mais recente, mas recomendamos que emigre o seu código para utilizar a versão mais recente. Ao utilizar a API REST, deve especificar a versão API em cada pedido através do parâmetro da versão API. Ao utilizar o .NET SDK, a versão do SDK que está a utilizar determina a versão correspondente da API REST. Se estiver a utilizar um SDK mais antigo, pode continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para suportar uma versão API mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Novidades na versão 5
A versão 5 do Azure Search .NET SDK tem como alvo a versão mais recente geralmente disponível da Azure Search REST API, especificamente 2017-11-11. Isto permite utilizar novas funcionalidades da Azure Search a partir de uma aplicação .NET, incluindo as seguintes:

* [Sinónimos.](search-synonyms.md)
* Pode agora aceder programáticamente a avisos no histórico de execução do indexante (ver `Warning` a propriedade da referência `IndexerExecutionResult` [.NET](/dotnet/api/microsoft.azure.search.models.indexerexecutionresult) para mais detalhes).
* Suporte para .NET Core 2.
* A nova estrutura do pacote suporta usando apenas as partes do SDK de que necessita (ver [Alterações de Rutura na versão 5](#ListOfChanges) para mais detalhes).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Em primeiro lugar, atualize a sua referência NuGet para `Microsoft.Azure.Search` utilizar a consola Do Gestor de Pacotes NuGet ou clicando à direita nas referências do seu projeto e selecionando "Gerir pacotes nuget..." em Estúdio Visual.

Assim que o NuGet tiver descarregado os novos pacotes e as suas dependências, reconstrua o seu projeto. Dependendo da estrutura do seu código, pode reconstruir com sucesso. Se sim, está pronto para ir!

Se a sua construção falhar, deverá ver um erro de construção como o seguinte:

```output
The name 'SuggesterSearchMode' does not exist in the current context
```

O próximo passo é corrigir este erro de construção. Consulte [Alterações de Breaking na versão 5](#ListOfChanges) para obter mais detalhes sobre o que causa o erro e como corrigi-lo.

Por favor, note que devido a alterações na embalagem do Azure Search .NET SDK, tem de reconstruir a sua aplicação para utilizar a versão 5. Estas alterações são detalhadas em [Breaking changes na versão 5](#ListOfChanges).

Pode ver avisos adicionais de construção relacionados com métodos ou propriedades obsoletos. As advertências incluirão instruções sobre o que usar em vez da função prevada. Por exemplo, se a sua aplicação utilizar o `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` método, deve receber um aviso que diz "Este comportamento está agora ativado por padrão, pelo que chamar este método já não é necessário."

Uma vez corrigido quaisquer erros ou avisos de construção, pode escoar alterações na sua aplicação para tirar partido de novas funcionalidades, se desejar. As novas funcionalidades no SDK são detalhadas no [que há de novo na versão 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Quebrando mudanças na versão 5

### <a name="new-package-structure"></a>Nova estrutura de pacotes

A mudança de rutura mais substancial na versão 5 é que a montagem e o `Microsoft.Azure.Search` seu conteúdo foram divididos em quatro conjuntos separados que agora são distribuídos como quatro pacotes NuGet separados:

 - `Microsoft.Azure.Search`: Este é um meta-pacote que inclui todos os outros pacotes de Pesquisa Azure como dependências. Se estiver a atualizar a partir de uma versão anterior do SDK, simplesmente atualizar este pacote e re-construir deve ser suficiente para começar a usar a nova versão.
 - `Microsoft.Azure.Search.Data`: Utilize este pacote se estiver a desenvolver uma aplicação .NET utilizando a Azure Search, e só precisa de consultar ou atualizar documentos nos seus índices. Se também precisar de criar ou atualizar índices, mapas de sinónimo ou outros recursos de nível de serviço, utilize o `Microsoft.Azure.Search` pacote em vez disso.
 - `Microsoft.Azure.Search.Service`: Utilize este pacote se estiver a desenvolver automatização em .NET para gerir índices de pesquisa de Azure, mapas de sinónimos, indexadores, fontes de dados ou outros recursos de nível de serviço. Se necessitar apenas de consultar ou atualizar documentos nos seus índices, utilize a `Microsoft.Azure.Search.Data` embalagem. Se precisar de toda a funcionalidade de Azure Search, utilize a `Microsoft.Azure.Search` embalagem.
 - `Microsoft.Azure.Search.Common`: Tipos comuns necessários pelas bibliotecas Azure Search .NET. Não deverá utilizar este pacote diretamente na sua aplicação; Destina-se apenas a ser usada como dependência.
 
Esta mudança está tecnicamente a quebrar uma vez que muitos tipos foram movidos entre conjuntos. É por isso que a reconstrução da sua aplicação é necessária para atualizar para a versão 5 do SDK.

Existe um pequeno número de outras alterações na versão 5 que podem exigir alterações de código para além de reconstruir a sua aplicação.

### <a name="change-to-suggesters"></a>Alteração para Sugestivos 

O `Suggester` construtor já não tem um parâmetro para `enum` `SuggesterSearchMode` . Este enum tinha apenas um valor, e foi, portanto, redundante. Se vir erros de construção como resultado disto, basta remover referências ao `SuggesterSearchMode` parâmetro.

### <a name="removed-obsolete-members"></a>Membros obsoletos removidos

Pode ver erros de construção relacionados com métodos ou propriedades que foram marcados como obsoletos em versões anteriores e posteriormente removidos na versão 5. Se encontrar tais erros, eis como resolvê-los:

- Se estava a usar o `IndexingParametersExtensions.IndexStorageMetadataOnly` método, use `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` em vez disso.
- Se estava a usar o `IndexingParametersExtensions.SkipContent` método, use `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` em vez disso.

### <a name="removed-preview-features"></a>Funcionalidades de pré-visualização removidas

Se estiver a atualizar da versão 4.0 para a versão 5, esteja ciente de que o suporte de análise de matriz JSON e CSV para Indexadores Blob foi removido uma vez que estas funcionalidades ainda estão em pré-visualização. Especificamente, foram removidos os seguintes métodos da `IndexingParametersExtensions` classe:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Se a sua aplicação tiver uma forte dependência destas funcionalidades, não poderá fazer upgrade para a versão 5 do Azure Search .NET SDK. Pode continuar a utilizar a versão 4.0-pré-visualização. No entanto, tenha em mente que **não recomendamos a utilização de SDKs de pré-visualização em aplicações de produção**. As funcionalidades de pré-visualização são apenas para avaliação e podem ser alteradas.

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre a utilização do Azure Search .NET SDK, consulte o [.NET How-to](search-howto-dotnet-sdk.md).

Congratulamo-nos com o seu feedback sobre o SDK. Se encontrar problemas, sinta-se à vontade para nos pedir ajuda no [Stack Overflow.](https://stackoverflow.com/questions/tagged/azure-search) Se encontrar um bug, pode arquivar um problema no [repositório Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de pré-afixar o seu título de emissão com "[Azure Search]".

Obrigado por usar a Azure Search!