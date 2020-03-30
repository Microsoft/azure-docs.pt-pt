---
title: Upgrade para o Azure Search .NET Management SDK versão 2
titleSuffix: Azure Cognitive Search
description: Upgrade para o Azure Search .NET Management SDK versão 2 a partir de versões anteriores. Saiba o que é novo e quais as mudanças de código que são necessárias.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b18e9688141ee64eb7dfcb82ce58db198e324b5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847541"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Atualizando versões do Azure Search .NET Management SDK

> [!Important]
> Este conteúdo ainda está em construção. A versão 3.0 da Azure Search Management .NET SDK está disponível no NuGet. Estamos a trabalhar na atualização deste guia de migração para explicar como fazer upgrade para a nova versão. 
>

Se estiver a utilizar a versão 1.0.2 ou mais antiga do [Azure Search .NET Management SDK,](https://aka.ms/search-mgmt-sdk)este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 2.

A versão 2 do Azure Search .NET Management SDK contém algumas alterações de versões anteriores. Estes são na sua maioria menores, por isso a alteração do seu código deve exigir apenas um esforço mínimo. Consulte [passos para atualizar](#UpgradeSteps) para obter instruções sobre como alterar o seu código para utilizar a nova versão SDK.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>O que há de novo na versão 2
A versão 2 do Azure Search .NET Management SDK tem como alvo a mesma versão geralmente disponível da API de Gestão de Pesquisa do Azure que as versões Anteriores do SDK, especificamente 2015-08-19. As alterações ao SDK são alterações estritamente do lado do cliente para melhorar a usabilidade do próprio SDK. Estas alterações incluem o seguinte:

* `Services.CreateOrUpdate`e as suas versões assíncronas agora `SearchService` sonam automaticamente o fornecimento e não regressam até que o fornecimento de serviços esteja concluído. Isto evita que tenha si mesmo de escrever esse código de votação.
* Se ainda quiser fazer sondagens manualmente, pode `Services.BeginCreateOrUpdate` utilizar o novo método ou uma das suas versões assíncronas.
* Foram `Services.Update` adicionados novos métodos e as suas versões assíncronas ao SDK. Estes métodos utilizam http patch para apoiar a atualização incremental de um serviço. Por exemplo, agora pode escalar um `SearchService` serviço passando uma instância `partitionCount` para `replicaCount` estes métodos que contêm apenas os desejados e propriedades. A velha forma `Services.Get`de chamar, `SearchService`modificar os `Services.CreateOrUpdate` devolvidos, e passá-lo ainda é suportado, mas já não é necessário. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Em primeiro lugar, atualize a sua referência NuGet para `Microsoft.Azure.Management.Search` utilizar a Consola nuGet Package Manager ou clicando corretamente nas referências do seu projeto e selecionando "Gerir pacotes NuGet..." no Estúdio Visual.

Assim que o NuGet tiver descarregado os novos pacotes e as suas dependências, reconstrua o seu projeto. Dependendo da forma como o seu código é estruturado, pode reconstruir com sucesso. Se assim for, está pronto para ir!

Se a sua construção falhar, pode ser porque implementou algumas das interfaces SDK (por exemplo, para efeitos de teste de unidade), que mudaram. Para resolver isto, terá de implementar os `BeginCreateOrUpdateWithHttpMessagesAsync`novos métodos, como .

Depois de corrigir quaisquer erros de construção, pode efazer alterações na sua aplicação para tirar partido de novas funcionalidades, se desejar. As novas funcionalidades no SDK são detalhadas no [What's new in version 2](#WhatsNew).

## <a name="next-steps"></a>Passos seguintes
Congratulamo-nos com o seu feedback sobre o SDK. Se encontrar problemas, por favor poste as suas perguntas para [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Se encontrar um bug, pode apresentar um problema no [repositório Azure.NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de rotular o título do seu emissão com "[search]".
