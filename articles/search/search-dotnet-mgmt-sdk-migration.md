---
title: Atualizar para o SDK de gestão .NET da Azure Search versão 2 - Azure Search
description: Atualizar para o SDK de gestão .NET da Azure Search versão 2 de versões anteriores. Saiba quais são as novidades e quais alterações de código são necessárias.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 62c2ed555fcac56677f4950c10d38ded8fb0649d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65025181"
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version"></a>Atualizar para a versão do SDK de gestão do Azure Search .NET 

> [!Important]
> Este conteúdo está ainda estão em construção. A versão 3.0 do SDK de .NET de gestão do Azure Search está disponível em NuGet. Estamos a trabalhar sobre este guia de migração para explicar como atualizar para a nova versão de atualização. 
>

Se estiver a utilizar a versão 1.0.2 ou mais antigas do [SDK de gestão do Azure Search .NET](https://aka.ms/search-mgmt-sdk), este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 2.

Versão 2 do SDK de gestão do Azure Search .NET contém algumas alterações de versões anteriores. Principalmente são pequenas, para que alterar o seu código deve exigir muito pouco esforço. Ver [passos para atualizar](#UpgradeSteps) para obter instruções sobre como alterar o seu código para utilizar a nova versão do SDK.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>O que há de novo na versão 2
Versão 2 do SDK de gestão do Azure Search .NET destina-se a mesma versão disponível em geral da API de REST de gestão de pesquisa do Azure como versões anteriores do SDK, especificamente 2015-08-19. As alterações para o SDK estão estritamente lado do cliente de alterações para melhorar a usabilidade do SDK em si. Estas alterações incluem o seguinte:

* `Services.CreateOrUpdate` e suas versões assíncronas agora automaticamente de inquirir o aprovisionamento `SearchService` e não retorna até que o serviço aprovisionamento estiver concluído. Isso poupa de ter de escrever esse código de consulta por conta própria.
* Se pretender continuar a consultar o serviço de aprovisionamento manualmente, pode utilizar o novo `Services.BeginCreateOrUpdate` método ou uma das respetivas versões assíncronas.
* Novos métodos `Services.Update` e suas versões assíncronas foram adicionados para o SDK. Esses métodos usam o PATCH de HTTP para suporta a atualização incremental de um serviço. Por exemplo, pode dimensionar um serviço ao transmitir uma `SearchService` instância para esses métodos, que contém apenas o desejado `partitionCount` e `replicaCount` propriedades. O método antigo de chamar `Services.Get`, modificar retornado `SearchService`e passando-o para `Services.CreateOrUpdate` continua a ser suportado, mas já não é necessário. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Em primeiro lugar, atualizar a sua referência de NuGet para `Microsoft.Azure.Management.Search` utilizando a consola de Gestor de pacotes NuGet ou ao clicar com o botão direito em suas referências de projeto e selecionando "Gerir NuGet pacotes..." no Visual Studio.

Assim que o NuGet tiver baixado os novos pacotes e suas dependências, recrie seu projeto. Dependendo de como o seu código está estruturado, ele poderá reconstruir os com êxito. Nesse caso, está pronto para começar!

Se sua compilação falhar, é possível porque implementou algumas das interfaces SDK (por exemplo, para fins de teste de unidade), que foram alterados. Para resolver este problema, terá de implementar os novos métodos como `BeginCreateOrUpdateWithHttpMessagesAsync`.

Depois de ter de corrigir os erros de compilação, pode efetuar alterações à sua aplicação para tirar partido das novas funcionalidades, se desejar. Novas funcionalidades no SDK são detalhadas no [quais são as novidades na versão 2](#WhatsNew).

## <a name="conclusion"></a>Conclusão
Apreciamos os seus comentários sobre o SDK. Se tiver problemas, fique à vontade contacte-nos para obter ajuda sobre o [fórum MSDN do Azure Search](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Se encontrar um bug, comunique o assunto na [repositório do GitHub do Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se para o prefixo seu título do problema com o "[o Azure Search]".

Obrigado por utilizar o Azure Search!
