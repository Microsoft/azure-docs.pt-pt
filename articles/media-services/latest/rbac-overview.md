---
title: Controlo de acesso baseado em papéis para contas de Serviços de Media - Azure Microsoft Docs
description: Este artigo discute o controlo de acesso baseado em papéis (RBAC) para contas azure media services.
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "66236907"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Controlo de acesso baseado em funções (RBAC) para contas de Serviços de Media

Atualmente, a Azure Media Services não define quaisquer funções personalizadas específicas do serviço. Para ter acesso total à conta de Media Services, os clientes podem utilizar as funções incorporadas do **Proprietário** ou **Colaborador.** A principal diferença entre estas funções é: o **Proprietário** pode controlar quem tem acesso a um recurso e o **Contribuinte** não pode. A função **de Leitor** incorporado também pode ser utilizada, mas o utilizador ou aplicação só terá lido o acesso às APIs dos Serviços de Comunicação Social. 

## <a name="design-principles"></a>Princípios de conceção

Um dos principais princípios de design da API v3 é tornar a API mais segura. v3 APIs não devolvem segredos ou credenciais nas operações **de Get** ou **List.** As chaves são sempre nulas, vazias ou saneadas da resposta. O utilizador precisa de chamar um método de ação separado para obter segredos ou credenciais. A função **do Leitor** não pode chamar operações como Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Ter ações separadas permite-lhe definir permissões de segurança RBAC mais granulares numa função personalizada, se desejar.

Para listar as operações suportes dos Media Services, faça:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

O artigo [de definições de papéis incorporados diz-lhe](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) exatamente qual o papel que concede. 

Consulte os seguintes artigos para obter mais informações:

- [Funções de administrador de subscrição clássica, funções RBAC do Azure e funções de administrador do Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [O que é RBAC para os recursos Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Use o RBAC para gerir o acesso](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Operações de prestadorde de serviços de mídia](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Passos seguintes

- [Desenvolvimento com Media Services v3 APIs](media-services-apis-overview.md)
- [Obtenha a política chave de conteúdo usando Media Services .NET](get-content-key-policy-dotnet-howto.md)
