---
title: Controlo de acesso baseado em funções para contas de Media Services - Azure Microsoft Docs
description: Este artigo discute o controlo de acesso baseado em funções (RBAC) para contas Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "66236907"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Controlo de acesso baseado em funções (RBAC) para contas de Serviços de Mídia

Atualmente, a Azure Media Services não define quaisquer funções personalizadas específicas do serviço. Para ter acesso total à conta dos Serviços de Comunicação Social, os clientes podem utilizar as funções incorporadas de **Proprietário** ou **Colaborador.** A principal diferença entre estas funções é: o **Proprietário** pode controlar quem tem acesso a um recurso e o **Contribuinte** não pode. A função **de Leitor** incorporado também pode ser utilizada, mas o utilizador ou aplicação só terá lido o acesso às APIs dos Serviços de Comunicação Social. 

## <a name="design-principles"></a>Princípios de conceção

Um dos principais princípios de design da API v3 é tornar a API mais segura. V3 APIs não devolvem segredos ou credenciais em operações **Get** ou **List.** As chaves são sempre nulas, vazias ou saneadas da resposta. O utilizador precisa de chamar um método de ação separado para obter segredos ou credenciais. A função **Reader** não pode chamar operações como Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Ter ações separadas permite-lhe definir mais permissões de segurança RBAC granulares numa função personalizada, se desejar.

Para listar as operações de suporte dos Serviços de Media, faça:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

O artigo [de definições de papel incorporado diz-lhe](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) exatamente o que o papel concede. 

Consulte os seguintes artigos para obter mais informações:

- [Funções de administrador de subscrição clássica, funções RBAC do Azure e funções de administrador do Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [O que é RBAC para os recursos Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Use o RBAC para gerir o acesso](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Operações de prestadores de recursos dos Serviços de Mídia](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Passos seguintes

- [Desenvolvimento com Serviços de Mídia v3 APIs](media-services-apis-overview.md)
- [Obtenha a política de chave de conteúdo usando os Media Services .NET](get-content-key-policy-dotnet-howto.md)
