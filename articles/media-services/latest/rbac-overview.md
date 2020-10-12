---
title: Controlo de acesso baseado em funções para contas de Media Services - Azure Microsoft Docs
description: Este artigo discute o controlo de acesso baseado em funções (RBAC) para contas Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: d6bc37a8aaddfb48e6d06eb46d9c1648e815b5ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89289261"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Controlo de acesso baseado em funções (RBAC) para contas de Serviços de Mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

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

O artigo [de definições de papel incorporado diz-lhe](../../role-based-access-control/built-in-roles.md) exatamente o que o papel concede. 

Consulte os seguintes artigos para obter mais informações:

- [Funções clássicas de administrador de subscrição, funções de Azure e funções de administrador ad Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [O que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
- [Use o RBAC para gerir o acesso](../../role-based-access-control/role-assignments-rest.md)
- [Operações de prestadores de recursos dos Serviços de Mídia](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>Passos seguintes

- [Desenvolvimento com Serviços de Mídia v3 APIs](media-services-apis-overview.md)
- [Obtenha a política de chave de conteúdo usando os Media Services .NET](get-content-key-policy-dotnet-howto.md)
