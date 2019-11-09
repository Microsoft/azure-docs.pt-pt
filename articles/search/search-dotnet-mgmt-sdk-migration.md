---
title: Atualizar para o SDK de gerenciamento do .NET Azure Search versão 2
titleSuffix: Azure Cognitive Search
description: Atualize para o SDK de gerenciamento do .NET Azure Search versão 2 de versões anteriores. Saiba o que há de novo e quais alterações de código são necessárias.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b18e9688141ee64eb7dfcb82ce58db198e324b5b
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847541"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Atualizando versões do SDK de gerenciamento do .NET Azure Search

> [!Important]
> Este conteúdo ainda está em construção. A versão 3,0 do SDK do .NET de gerenciamento de Azure Search está disponível no NuGet. Estamos trabalhando para atualizar este guia de migração para explicar como atualizar para a nova versão. 
>

Se você estiver usando a versão 1.0.2 ou mais antiga do [SDK de gerenciamento do Azure Search .net](https://aka.ms/search-mgmt-sdk), este artigo ajudará você a atualizar seu aplicativo para usar a versão 2.

A versão 2 do SDK de gerenciamento do .NET Azure Search contém algumas alterações de versões anteriores. Elas são basicamente secundárias, portanto, alterar seu código deve exigir apenas um mínimo de esforço. Consulte [as etapas para atualizar](#UpgradeSteps) para obter instruções sobre como alterar seu código para usar a nova versão do SDK.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>O que há de novo na versão 2
A versão 2 do SDK de gerenciamento do .NET Azure Search tem como destino a mesma versão disponível em geral da API REST de gerenciamento de Azure Search como versões anteriores do SDK, especificamente 2015-08-19. As alterações no SDK são estritamente alterações no lado do cliente para melhorar a usabilidade do próprio SDK. Essas alterações incluem o seguinte:

* `Services.CreateOrUpdate` e suas versões assíncronas agora sondam automaticamente o `SearchService` de provisionamento e não retornam até que o provisionamento do serviço seja concluído. Isso evita que você tenha que escrever tal código de sondagem por conta própria.
* Se você ainda quiser sondar manualmente o provisionamento de serviço, poderá usar o novo método `Services.BeginCreateOrUpdate` ou uma de suas versões assíncronas.
* Novos métodos `Services.Update` e suas versões assíncronas foram adicionados ao SDK. Esses métodos usam PATCH HTTP para dar suporte à atualização incremental de um serviço. Por exemplo, agora você pode dimensionar um serviço passando uma instância de `SearchService` para esses métodos que contenham apenas as propriedades desejadas `partitionCount` e `replicaCount`. A maneira antiga de chamar `Services.Get`, modificar o `SearchService`retornado e passá-lo para `Services.CreateOrUpdate` ainda tem suporte, mas não é mais necessária. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas para atualizar
Primeiro, atualize a referência do NuGet para `Microsoft.Azure.Management.Search` usando o console do Gerenciador de pacotes NuGet ou clicando com o botão direito do mouse nas referências do projeto e selecionando "gerenciar pacotes NuGet..." no Visual Studio.

Depois que o NuGet tiver baixado os novos pacotes e suas dependências, recompile o projeto. Dependendo de como seu código é estruturado, ele pode ser recriado com êxito. Nesse caso, você está pronto para começar!

Se sua compilação falhar, pode ser porque você implementou algumas das interfaces do SDK (por exemplo, para fins de teste de unidade), que foram alteradas. Para resolver isso, você precisará implementar os novos métodos, como `BeginCreateOrUpdateWithHttpMessagesAsync`.

Depois de corrigir os erros de compilação, você poderá fazer alterações em seu aplicativo para tirar proveito da nova funcionalidade, se desejar. Os novos recursos do SDK são detalhados no [que há de novo na versão 2](#WhatsNew).

## <a name="next-steps"></a>Passos seguintes
Agradecemos seus comentários sobre o SDK. Se você encontrar problemas, envie suas perguntas para [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Se você encontrar um bug, poderá arquivar um problema no [repositório GitHub do SDK do .net do Azure](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de rotular o título do problema com "[Pesquisar]".
