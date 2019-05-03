---
title: Atualizar para o SDK .NET da Azure Search versão 5 - Azure Search
description: Migre o código para o SDK .NET da Azure Search versão 5 de versões mais antigas. Saiba quais são as novidades e quais alterações de código são necessárias.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 980f16680ecc65bf8ac30a49e02f39b0f18a15f6
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024105"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-5"></a>Atualizar para o SDK .NET da Azure Search versão 5

> [!Important]
> Este conteúdo está ainda estão em construção. Versão 9.0 do SDK de .NET de pesquisa do Azure está disponível em NuGet. Estamos a trabalhar neste artigo para explicar como atualizar para 9.0. 
> 

Se estiver a utilizar versão 4.0-pré-visualização ou mais antiga do [SDK .NET da Azure Search](https://aka.ms/search-sdk), este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 5.

Para obter instruções mais geral do SDK, incluindo exemplos, consulte [como utilizar o Azure Search a partir de um aplicativo .NET](search-howto-dotnet-sdk.md).

Versão 5 do Azure Search .NET SDK contém algumas alterações de versões anteriores. Principalmente são pequenas, para que alterar o seu código deve exigir muito pouco esforço. Ver [passos para atualizar](#UpgradeSteps) para obter instruções sobre como alterar o seu código para utilizar a nova versão do SDK.

> [!NOTE]
> Se estiver usando a versão pré-visualização do 2.0 ou mais antigo, deve atualizar primeiro para a versão 3 e, em seguida, atualize para a versão 5. Ver [atualizar para o SDK .NET da Azure Search versão 3](search-dotnet-sdk-migration.md) para obter instruções.
>
> A instância de serviço do Azure Search oferece suporte a várias versões de REST API, incluindo o mais recente. Pode continuar a utilizar uma versão quando ele é já não é a mais recente, mas recomendamos que migre o código para usar a versão mais recente. Ao utilizar a API REST, tem de especificar a versão de API em cada solicitação por meio do parâmetro de versão de api. Quando utilizar o SDK de .NET, a versão do SDK que está a utilizar determina a versão da REST API correspondente. Se estiver a utilizar um SDK mais antigo, pode continuar a executar esse código sem alterações, mesmo que o serviço é atualizado para suportar uma versão mais recente do API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>O que há de novo na versão 5
Versão 5 do SDK de .NET de pesquisa do Azure destina-se a versão mais recente versão disponível em geral da API de REST de pesquisa do Azure, especificamente 2017-11-11. Isto torna possível usar novos recursos do Azure Search a partir de um aplicativo .NET, incluindo o seguinte:

* [Sinónimos](search-synonyms.md).
* Agora pode aceder programaticamente avisos no histórico de execução do indexador (consulte a `Warning` propriedade do `IndexerExecutionResult` no [referência de .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) para obter mais detalhes).
* Suporte para .NET Core 2.
* Nova estrutura do pacote suporta a utilização de apenas as partes do SDK que precisa (consulte [alterações significativas na versão 5](#ListOfChanges) para obter detalhes).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Em primeiro lugar, atualizar a sua referência de NuGet para `Microsoft.Azure.Search` utilizando a consola de Gestor de pacotes NuGet ou ao clicar com o botão direito em suas referências de projeto e selecionando "Gerir NuGet pacotes..." no Visual Studio.

Assim que o NuGet tiver baixado os novos pacotes e suas dependências, recrie seu projeto. Dependendo de como o seu código está estruturado, ele poderá reconstruir os com êxito. Nesse caso, está pronto para começar!

Se sua compilação falhar, deverá ver um erro de compilação semelhante ao seguinte:

    The name 'SuggesterSearchMode' does not exist in the current context

A próxima etapa é corrigir este erro de compilação. Ver [alterações significativas na versão 5](#ListOfChanges) para obter detalhes sobre o que faz com que o erro e como corrigi-lo.

Tenha em atenção que devido a alterações do pacote de SDK .NET da Azure Search, é preciso reconstruir a sua aplicação para utilizar a versão 5. Estas alterações são detalhadas no [alterações significativas na versão 5](#ListOfChanges).

Poderá ver avisos de compilação adicionais relacionados com a propriedades ou métodos obsoletos. Os avisos inclui instruções sobre o que utilizar em vez da funcionalidade despromovida. Por exemplo, se a sua aplicação utiliza o `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` método, obterá um aviso que diz "Este comportamento está agora ativado por predefinição, para chamar este método já não é necessário."

Uma vez que corrigiu os erros de compilação e avisos, pode efetuar alterações à sua aplicação para tirar partido das novas funcionalidades, se desejar. Novas funcionalidades no SDK são detalhadas no [quais são as novidades na versão 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Alterações significativas na versão 5

### <a name="new-package-structure"></a>Nova estrutura de pacotes

A alteração de última hora mais substancial na versão 5 é que o `Microsoft.Azure.Search` assembly e o respetivo conteúdo foram dividido em quatro assemblies separados que agora são distribuídos como quatro pacotes do NuGet separados:

 - `Microsoft.Azure.Search`: Este é um pacote de metadados que inclui todos os outros pacotes do Azure Search como dependências. Se estiver a atualizar de uma versão anterior do SDK, basta atualizar este pacote e voltar a criar devem ser suficiente para começar a utilizar a nova versão.
 - `Microsoft.Azure.Search.Data`: Utilize este pacote, se estiver desenvolvendo um aplicativo do .NET com o Azure Search e só tem de consultar ou atualizar documentos em índices. Se também terá de criar ou atualizar índices, mapas de sinónimos, ou outros recursos de nível de serviço, utilizam o `Microsoft.Azure.Search` em vez disso, o pacote.
 - `Microsoft.Azure.Search.Service`: Utilize este pacote, se estiver a desenvolver automatização no .NET para gerir os índices da Azure Search, mapas de sinónimos, indexadores, origens de dados ou outros recursos de nível de serviço. Se só precisa de documentos de consulta ou atualização em seus índices, utilize o `Microsoft.Azure.Search.Data` em vez disso, o pacote. Se precisar de todas as funcionalidades do Azure Search, utilize o `Microsoft.Azure.Search` em vez disso, o pacote.
 - `Microsoft.Azure.Search.Common`: Tipos comuns necessários para as bibliotecas .NET de pesquisa do Azure. Não é preciso usar esse pacote diretamente em seu aplicativo; Destina-se apenas a ser utilizado como uma dependência.
 
Esta alteração está interrompendo tecnicamente, uma vez que muitos tipos foram movidos entre assemblies. É por isso reconstruir a sua aplicação é necessário para atualizar para versão 5 do SDK.

Há um pequeno número de outras alterações de última hora na versão 5, que podem exigir código altera-se para além de reconstruir a sua aplicação.

### <a name="change-to-suggesters"></a>Alterar para Sugestores 

O `Suggester` construtor já não tem uma `enum` parâmetro para `SuggesterSearchMode`. Essa enumeração apenas tinha um valor e, portanto, era redundante. Se vir erros como resultado de compilação, simplesmente remover as referências para o `SuggesterSearchMode` parâmetro.

### <a name="removed-obsolete-members"></a>Remover membros obsoletos

Poderá ver erros relacionados com métodos ou propriedades que foram marcadas como obsoletos em versões anteriores e, em seguida, removidas numa versão 5 de compilação. Se encontrar esses erros, eis como resolvê-los:

- Se estava a utilizar o `IndexingParametersExtensions.IndexStorageMetadataOnly` método, utilize `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` em vez disso.
- Se estava a utilizar o `IndexingParametersExtensions.SkipContent` método, utilize `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` em vez disso.

### <a name="removed-preview-features"></a>Funcionalidades de pré-visualização foi removido

Se estiver a atualizar versão 4.0-preview para a versão 5, lembre-se de que a matriz JSON e suporte para indexadores de BLOBs de análise de CSV foi removido, uma vez que estas funcionalidades ainda estão em pré-visualização. Especificamente, os seguintes métodos do `IndexingParametersExtensions` classe foram removidos:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Se seu aplicativo tiver uma dependência sobre estas funcionalidades, não será capaz de atualizar para a versão 5 do SDK de .NET de pesquisa do Azure. Pode continuar a utilizar a versão 4.0-preview. No entanto,. tenha em atenção que **Recomendamos que não utilize SDKs de pré-visualização em aplicações de produção**. Funcionalidades de pré-visualização destinam-se apenas de avaliação e pode ser alterada.

## <a name="conclusion"></a>Conclusão
Se precisar de obter mais detalhes sobre como utilizar o SDK .NET da Azure Search, consulte a [.NET procedimentos](search-howto-dotnet-sdk.md).

Apreciamos os seus comentários sobre o SDK. Se tiver problemas, fique à vontade contacte-nos para obter ajuda sobre o [fórum MSDN do Azure Search](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Se encontrar um bug, comunique o assunto na [repositório do GitHub do Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se para o prefixo seu título do problema com o "[o Azure Search]".

Obrigado por utilizar o Azure Search!
