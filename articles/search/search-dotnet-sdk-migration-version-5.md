---
title: Upgrade para Azure Search .NET SDK versão 5
titleSuffix: Azure Cognitive Search
description: Migrar código para a versão 5 do Azure Search .NET SDK a partir de versões mais antigas. Saiba o que é novo e quais as alterações de código que são necessárias.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bb0cd191ba7e5939c55d11b484ed7a2c422f8c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793029"
---
# <a name="upgrade-to-azure-search-net-sdk-version-5"></a>Upgrade para Azure Search .NET SDK versão 5

Se estiver a utilizar a versão 4.0-preview ou mais antiga do [Azure Search .NET SDK,](https://aka.ms/search-sdk)este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 5.

Para uma passagem mais geral do SDK, incluindo exemplos, consulte [Como utilizar a Pesquisa Azure a partir de uma aplicação .NET](search-howto-dotnet-sdk.md).

A versão 5 do Azure Search .NET SDK contém algumas alterações de versões anteriores. Estes são na sua maioria menores, por isso a alteração do seu código deve exigir apenas um esforço mínimo. Consulte [passos para atualizar](#UpgradeSteps) para obter instruções sobre como alterar o seu código para utilizar a nova versão SDK.

> [!NOTE]
> Se estiver a utilizar a versão 2.0-preview ou mais antiga, deve fazer o upgrade para a versão 3 primeiro e, em seguida, atualizar para a versão 5. Consulte o Upgrade para a versão 3 do [Azure Search .NET SDK](search-dotnet-sdk-migration.md) 3 para obter instruções.
>
> A sua instância de serviço Azure Search suporta várias versões REST API, incluindo a mais recente. Pode continuar a utilizar uma versão quando já não é a mais recente, mas recomendamos que emigra o seu código para utilizar a versão mais recente. Ao utilizar a API REST, deve especificar a versão API em todos os pedidos através do parâmetro da versão api. Ao utilizar o .NET SDK, a versão do SDK que está a utilizar determina a versão correspondente da API REST. Se estiver a utilizar um SDK mais antigo, pode continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para suportar uma versão API mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>O que há de novo na versão 5
A versão 5 do Azure Search .NET SDK tem como alvo a mais recente versão geralmente disponível da API de Pesquisa Azure, especificamente 2017-11-11. Isto permite utilizar novas funcionalidades da Pesquisa Azure a partir de uma aplicação .NET, incluindo as seguintes:

* [Sinónimos.](search-synonyms.md)
* Agora pode aceder programáticamente a avisos no `Warning` histórico `IndexerExecutionResult` de execução do indexador (ver a propriedade na [referência .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) para mais detalhes).
* Suporte para .NET Core 2.
* A nova estrutura do pacote suporta a utilização apenas das partes do SDK de que necessita (ver [alterações](#ListOfChanges) de rutura na versão 5 para mais detalhes).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Em primeiro lugar, atualize a sua referência NuGet para `Microsoft.Azure.Search` utilizar a Consola nuGet Package Manager ou clicando corretamente nas referências do seu projeto e selecionando "Gerir pacotes NuGet..." no Estúdio Visual.

Assim que o NuGet tiver descarregado os novos pacotes e as suas dependências, reconstrua o seu projeto. Dependendo da forma como o seu código é estruturado, pode reconstruir com sucesso. Se assim for, está pronto para ir!

Se a sua construção falhar, deve ver um erro de construção como o seguinte:

    The name 'SuggesterSearchMode' does not exist in the current context

O próximo passo é corrigir este erro de construção. Consulte [as alterações de Rutura na versão 5](#ListOfChanges) para obter detalhes sobre o que causa o erro e como corrigi-lo.

Tenha em adesão que, devido a alterações na embalagem do Azure Search .NET SDK, deve reconstruir a sua aplicação para utilizar a versão 5. Estas alterações são detalhadas em [Quebrar alterações na versão 5](#ListOfChanges).

Pode ver avisos adicionais de construção relacionados com métodos ou propriedades obsoletos. As advertências incluirão instruções sobre o que usar em vez da função depreciada. Por exemplo, se a `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` sua aplicação usar o método, deve receber um aviso que diz "Este comportamento está agora ativado por padrão, pelo que chamar este método já não é necessário."

Depois de corrigir quaisquer erros de construção ou avisos, pode fazer alterações na sua aplicação para tirar partido de novas funcionalidades, se desejar. As novas funcionalidades no SDK são detalhadas no [What's new in version 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Quebrar alterações na versão 5

### <a name="new-package-structure"></a>Nova Estrutura de Pacotes

A alteração de rutura mais substancial `Microsoft.Azure.Search` na versão 5 é que o conjunto e o seu conteúdo foram divididos em quatro conjuntos separados que agora são distribuídos como quatro pacotes NuGet separados:

 - `Microsoft.Azure.Search`: Este é um meta-pacote que inclui todos os outros pacotes de Pesquisa Azure como dependências. Se estiver a atualizar a partir de uma versão anterior do SDK, basta atualizar este pacote e reconstruir este pacote deve ser suficiente para começar a usar a nova versão.
 - `Microsoft.Azure.Search.Data`: Utilize este pacote se estiver a desenvolver uma aplicação .NET utilizando a Pesquisa Azure, e só precisa de consultar ou atualizar documentos nos seus índices. Se também precisar de criar ou atualizar índices, mapas sinónimos ou outros `Microsoft.Azure.Search` recursos ao nível do serviço, utilize o pacote em vez disso.
 - `Microsoft.Azure.Search.Service`: Utilize este pacote se estiver a desenvolver automação em .NET para gerir índices de Pesquisa Azure, mapas de sinónimo, indexadores, fontes de dados ou outros recursos ao nível do serviço. Se necessitar apenas de consultar ou atualizar documentos `Microsoft.Azure.Search.Data` nos seus índices, utilize o pacote em vez disso. Se necessitar de toda a funcionalidade do `Microsoft.Azure.Search` Azure Search, utilize o pacote em vez disso.
 - `Microsoft.Azure.Search.Common`: Tipos comuns necessários pelas bibliotecas Azure Search .NET. Não deve utilizar este pacote diretamente na sua aplicação; Destina-se apenas a ser usado como uma dependência.
 
Esta mudança está tecnicamente a quebrar-se, uma vez que muitos tipos foram movidos entre assembleias. É por isso que a reconstrução da sua aplicação é necessária para fazer upgrade para a versão 5 do SDK.

Existe um pequeno número de outras alterações de quebra na versão 5 que podem exigir alterações de código para além de reconstruir a sua aplicação.

### <a name="change-to-suggesters"></a>Alteração para Sugestionantes 

O `Suggester` construtor já não `enum` tem `SuggesterSearchMode`um parâmetro para . Este enum só tinha um valor, e por isso foi redundante. Se vir erros de construção como resultado disso, `SuggesterSearchMode` basta remover referências ao parâmetro.

### <a name="removed-obsolete-members"></a>Membros obsoletos removidos

Pode ver erros de construção relacionados com métodos ou propriedades que foram marcados como obsoletos em versões anteriores e posteriormente removidos na versão 5. Se encontrar tais erros, eis como resolvê-los:

- Se estava a `IndexingParametersExtensions.IndexStorageMetadataOnly` usar `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` o método, em vez disso, usa.
- Se estava a `IndexingParametersExtensions.SkipContent` usar `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` o método, em vez disso, usa.

### <a name="removed-preview-features"></a>Funcionalidades de pré-visualização removidas

Se estiver a atualizar da versão 4.0-preview para a versão 5, esteja ciente de que a matriz JSON e o suporte de análise CSV para Os Indexers Blob foram removidos uma vez que estas funcionalidades ainda estão em pré-visualização. Especificamente, foram removidos os seguintes métodos da `IndexingParametersExtensions` classe:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Se a sua aplicação tiver uma dependência dura destas funcionalidades, não poderá fazer o upgrade para a versão 5 do Azure Search .NET SDK. Pode continuar a utilizar a versão 4.0-preview. No entanto, tenha em mente que **não recomendamos a utilização de SDKs de pré-visualização em aplicações**de produção . As funcionalidades de pré-visualização são apenas para avaliação e podem mudar.

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre a utilização do Azure Search .NET SDK, consulte o [.NET How-to](search-howto-dotnet-sdk.md).

Congratulamo-nos com o seu feedback sobre o SDK. Se encontrar problemas, sinta-se à vontade para nos pedir ajuda no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se encontrar um bug, pode apresentar um problema no [repositório Azure.NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de pré-fixar o título de emissão com "[Pesquisa Azure]".

Obrigado por usar a Pesquisa Azure!
