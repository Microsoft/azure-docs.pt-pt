---
title: Controlo de acesso baseado em funções para contas de Serviços de Media
description: Este artigo discute o controlo de acesso baseado em funções (Azure RBAC) para contas Azure Media Services.
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
ms.openlocfilehash: 7c9edce71e0d0565de80153bfd5b3d5f1edb2e8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955161"
---
# <a name="azure-role-based-access-control-azure-rbac-for-media-services-accounts"></a>Controlo de acesso baseado em funções Azure (Azure RBAC) para contas de Serviços de Media

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Atualmente, a Azure Media Services não define quaisquer funções personalizadas específicas do serviço. Para ter acesso total à conta dos Serviços de Comunicação Social, os clientes podem utilizar as funções incorporadas de **Proprietário** ou **Colaborador.** A principal diferença entre estas funções é: o **Proprietário** pode controlar quem tem acesso a um recurso e o **Contribuinte** não pode. A função **de Leitor** incorporado também pode ser utilizada, mas o utilizador ou aplicação só terá lido o acesso às APIs dos Serviços de Comunicação Social. 

## <a name="design-principles"></a>Princípios de conceção

Um dos principais princípios de design da API v3 é tornar a API mais segura. V3 APIs não devolvem segredos ou credenciais em operações **Get** ou **List.** As chaves são sempre nulas, vazias ou saneadas da resposta. O utilizador precisa de chamar um método de ação separado para obter segredos ou credenciais. A função **Reader** não pode chamar operações como Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Ter ações separadas permite-lhe definir permissões de segurança RBAC mais granulares num papel personalizado, se desejar.

Para listar as operações de suporte dos Serviços de Media, faça:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

O artigo [de definições de papel incorporado diz-lhe](../../role-based-access-control/built-in-roles.md) exatamente o que o papel concede. 

Consulte os seguintes artigos para obter mais informações:

- [Funções clássicas de administrador de subscrição, funções de Azure e Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [O que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
- [Utilizar a API REST para adicionar ou remover atribuições de funções do Azure](../../role-based-access-control/role-assignments-rest.md)
- [Operações de prestadores de recursos dos Serviços de Mídia](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>Passos seguintes

- [Desenvolvimento com Serviços de Mídia v3 APIs](media-services-apis-overview.md)
- [Obtenha a política de chave de conteúdo usando os Media Services .NET](get-content-key-policy-dotnet-howto.md)
