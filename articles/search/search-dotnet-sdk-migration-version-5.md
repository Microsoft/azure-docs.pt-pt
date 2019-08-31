---
title: Atualizar para o SDK do .NET Azure Search versão 5-Azure Search
description: Migre o código para o Azure Search .NET SDK versão 5 de versões mais antigas. Saiba o que há de novo e quais alterações de código são necessárias.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: c64d13e4bcad11ef729f34ee71b7c7461a507fc7
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183254"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-5"></a>Atualizando para o SDK do .NET Azure Search versão 5

Se você estiver usando a versão 4,0-Preview ou anterior do [SDK Azure Search .net](https://aka.ms/search-sdk), este artigo ajudará você a atualizar seu aplicativo para usar a versão 5.

Para obter uma explicação mais geral do SDK, incluindo exemplos, consulte [como usar Azure Search de um aplicativo .net](search-howto-dotnet-sdk.md).

A versão 5 do SDK do .NET Azure Search contém algumas alterações de versões anteriores. Elas são basicamente secundárias, portanto, alterar seu código deve exigir apenas um mínimo de esforço. Consulte [as etapas para atualizar](#UpgradeSteps) para obter instruções sobre como alterar seu código para usar a nova versão do SDK.

> [!NOTE]
> Se você estiver usando a versão 2,0-Preview ou mais antiga, atualize para a versão 3 primeiro e, em seguida, atualize para a versão 5. Consulte [Atualizando para o SDK do .net Azure Search versão 3](search-dotnet-sdk-migration.md) para obter instruções.
>
> Sua instância do serviço de Azure Search dá suporte a várias versões da API REST, incluindo a mais recente. Você pode continuar a usar uma versão quando ela não for mais a mais recente, mas é recomendável migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro API-Version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se você estiver usando um SDK mais antigo, poderá continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para dar suporte a uma versão mais recente da API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>O que há de novo na versão 5
A versão 5 do SDK do .NET Azure Search tem como alvo a versão mais recente disponível da API REST do Azure Search, especificamente 2017-11-11. Isso possibilita o uso de novos recursos do Azure Search de um aplicativo .NET, incluindo o seguinte:

* [Sinônimos](search-synonyms.md).
* Agora você pode acessar avisos de forma programática no histórico de execução do indexador (consulte a `Warning` propriedade de `IndexerExecutionResult` na [referência do .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) para obter mais detalhes).
* Suporte para .NET Core 2.
* A nova estrutura de pacote dá suporte ao uso de apenas as partes do SDK de que você precisa (consulte [alterações recentes na versão 5](#ListOfChanges) para obter detalhes).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas para atualizar
Primeiro, atualize a referência do NuGet `Microsoft.Azure.Search` para usar o console do Gerenciador de pacotes NuGet ou clicando com o botão direito do mouse nas referências do projeto e selecionando "gerenciar pacotes NuGet..." no Visual Studio.

Depois que o NuGet tiver baixado os novos pacotes e suas dependências, recompile o projeto. Dependendo de como seu código é estruturado, ele pode ser recriado com êxito. Nesse caso, você está pronto para começar!

Se sua compilação falhar, você deverá ver um erro de compilação como o seguinte:

    The name 'SuggesterSearchMode' does not exist in the current context

A próxima etapa é corrigir esse erro de compilação. Consulte [alterações recentes na versão 5](#ListOfChanges) para obter detalhes sobre o que causa o erro e como corrigi-lo.

Observe que, devido a alterações no empacotamento do SDK do .NET Azure Search, você deve recompilar seu aplicativo para usar a versão 5. Essas alterações são detalhadas em [alterações significativas na versão 5](#ListOfChanges).

Você pode ver avisos de compilação adicionais relacionados a propriedades ou métodos obsoletos. Os avisos incluirão instruções sobre o que usar em vez do recurso preterido. Por exemplo, se seu aplicativo usar o `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` método, você deverá receber um aviso dizendo "esse comportamento agora está habilitado por padrão, portanto, chamar esse método não é mais necessário".

Depois de corrigir quaisquer erros ou avisos de compilação, você poderá fazer alterações em seu aplicativo para tirar proveito da nova funcionalidade, se desejar. Os novos recursos do SDK são detalhados no [que há de novo na versão 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Alterações recentes na versão 5

### <a name="new-package-structure"></a>Nova estrutura de pacote

A alteração significativa mais significativa na versão 5 é que o `Microsoft.Azure.Search` assembly e seu conteúdo foram divididos em quatro assemblies separados que agora são distribuídos como quatro pacotes NuGet separados:

 - `Microsoft.Azure.Search`: Esse é um meta-Package que inclui todos os outros pacotes Azure Search como dependências. Se você estiver atualizando de uma versão anterior do SDK, simplesmente atualizar este pacote e recompilar deve ser suficiente para começar a usar a nova versão.
 - `Microsoft.Azure.Search.Data`: Use este pacote se você estiver desenvolvendo um aplicativo .NET usando Azure Search e precisar apenas consultar ou atualizar documentos em seus índices. Se você também precisar criar ou atualizar índices, mapas de sinônimos ou outros recursos de nível de serviço, use `Microsoft.Azure.Search` o pacote em vez disso.
 - `Microsoft.Azure.Search.Service`: Use este pacote se você estiver desenvolvendo automação no .NET para gerenciar índices de Azure Search, mapas de sinônimos, indexadores, fontes de dados ou outros recursos de nível de serviço. Se você precisar apenas consultar ou atualizar documentos em seus índices, use o `Microsoft.Azure.Search.Data` pacote em vez disso. Se você precisar de toda a funcionalidade do Azure Search, use `Microsoft.Azure.Search` o pacote em vez disso.
 - `Microsoft.Azure.Search.Common`: Tipos comuns necessários para as bibliotecas do Azure Search .NET. Você não precisa usar esse pacote diretamente em seu aplicativo; Ele destina-se apenas a ser usado como uma dependência.
 
Essa alteração é tecnicamente quebrada, pois muitos tipos foram movidos entre assemblies. É por isso que recompilar seu aplicativo é necessário para atualizar para a versão 5 do SDK.

Há um pequeno número de outras alterações significativas na versão 5 que podem exigir alterações de código, além da recriação do aplicativo.

### <a name="change-to-suggesters"></a>Alterar para sugestores 

O `Suggester` Construtor não tem mais um `enum` parâmetro para `SuggesterSearchMode`. Esse enum tem apenas um valor e, portanto, era redundante. Se você vir erros de compilação como resultado disso, simplesmente remova as `SuggesterSearchMode` referências ao parâmetro.

### <a name="removed-obsolete-members"></a>Membros obsoletos removidos

Você pode ver erros de compilação relacionados a métodos ou propriedades que foram marcados como obsoletos em versões anteriores e, subsequentemente, removidos na versão 5. Se você encontrar esses erros, aqui está como resolvê-los:

- Se você estava usando o `IndexingParametersExtensions.IndexStorageMetadataOnly` método, use `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` em vez disso.
- Se você estava usando o `IndexingParametersExtensions.SkipContent` método, use `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` em vez disso.

### <a name="removed-preview-features"></a>Recursos de visualização removidos

Se você estiver atualizando da versão 4,0-Preview para a versão 5, lembre-se de que a matriz JSON e o suporte à análise de CSV para indexadores de blob foram removidos, pois esses recursos ainda estão em versão prévia. Especificamente, os seguintes métodos da `IndexingParametersExtensions` classe foram removidos:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Se seu aplicativo tiver uma dependência rígida desses recursos, você não poderá atualizar para a versão 5 do SDK do .NET Azure Search. Você pode continuar a usar a versão 4,0-Preview. No entanto, tenha em mente que não recomendamos o **uso de SDKs de visualização em aplicativos de produção**. Os recursos de visualização são apenas para avaliação e podem ser alterados.

## <a name="conclusion"></a>Conclusão
Se você precisar de mais detalhes sobre como usar o SDK do .NET Azure Search, consulte o [instruções .net](search-howto-dotnet-sdk.md).

Agradecemos seus comentários sobre o SDK. Se você encontrar problemas, sinta-se à vontade para nos pedir ajuda sobre [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se você encontrar um bug, poderá arquivar um problema no [repositório GitHub do SDK do .net do Azure](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de prefixar o título do problema com "[Azure Search]".

Obrigado por usar Azure Search!
