---
title: Upgrade para a versão SDK de gestão de pesquisa Azure .NET
titleSuffix: Azure Cognitive Search
description: Upgrade para a versão SDK de gestão Azure Search .NET SDK de versões anteriores. Saiba o que há de novo e que mudanças de código são necessárias.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b18e9688141ee64eb7dfcb82ce58db198e324b5b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "73847541"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Versões de upgrade do Azure Search .NET Management SDK

> [!Important]
> Este conteúdo ainda está em construção. A versão 3.0 do Azure Search Management .NET SDK está disponível no NuGet. Estamos a trabalhar na atualização deste guia de migração para explicar como fazer o upgrade para a nova versão. 
>

Se estiver a utilizar a versão 1.0.2 ou mais antiga do [Azure Search .NET Management SDK,](https://aka.ms/search-mgmt-sdk)este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 2.

A versão 2 do Azure Search .NET Management SDK contém algumas alterações a partir de versões anteriores. Estes são na sua maioria menores, por isso mudar o seu código deve exigir apenas o mínimo de esforço. Consulte [passos para atualizar](#UpgradeSteps) para obter instruções sobre como alterar o seu código para utilizar a nova versão SDK.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Novidades na versão 2
A versão 2 do Azure Search .NET Management SDK tem como alvo a mesma versão geralmente disponível da Azure Search Management REST API que as versões anteriores da SDK, especificamente 2015-08-19. As alterações ao SDK são alterações estritamente do lado do cliente para melhorar a usabilidade do próprio SDK. Estas alterações incluem o seguinte:

* `Services.CreateOrUpdate`e as suas versões assíncronas agora automaticamente pesquisam o provisionamento `SearchService` e não regressam até que o fornecimento de serviço esteja completo. Isto evita que tenha de escrever um código de sondagens.
* Se ainda quiser fazer sondagens manualmente, pode utilizar o novo `Services.BeginCreateOrUpdate` método ou uma das suas versões assíncronos.
* Novos `Services.Update` métodos e as suas versões assíncronos foram adicionados ao SDK. Estes métodos utilizam HTTP PATCH para suportar a atualização incremental de um serviço. Por exemplo, agora pode escalar um serviço passando um `SearchService` exemplo para estes métodos que contém apenas as `partitionCount` propriedades e propriedades desejadas. `replicaCount` A velha forma de `Services.Get` chamar, modificar o `SearchService` devolvido, e passá-lo `Services.CreateOrUpdate` para ainda é suportado, mas já não é necessário. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Em primeiro lugar, atualize a sua referência NuGet para `Microsoft.Azure.Management.Search` utilizar a consola Do Gestor de Pacotes NuGet ou clicando à direita nas referências do seu projeto e selecionando "Gerir pacotes nuget..." em Estúdio Visual.

Assim que o NuGet tiver descarregado os novos pacotes e as suas dependências, reconstrua o seu projeto. Dependendo da estrutura do seu código, pode reconstruir com sucesso. Se sim, está pronto para ir!

Se a sua construção falhar, pode ser porque implementou algumas das interfaces SDK (por exemplo, para efeitos de testes unitários), que mudaram. Para resolver isto, terá de implementar os novos métodos, tais `BeginCreateOrUpdateWithHttpMessagesAsync` como.

Uma vez corrigido quaisquer erros de construção, pode escoar alterações na sua aplicação para tirar partido de novas funcionalidades, se desejar. As novas funcionalidades no SDK são detalhadas no [que há de novo na versão 2](#WhatsNew).

## <a name="next-steps"></a>Próximos passos
Congratulamo-nos com o seu feedback sobre o SDK. Se encontrar problemas, por favor, coloque as suas perguntas no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Se encontrar um bug, pode arquivar um problema no [repositório Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de rotular o seu título de emissão com "[search]".
