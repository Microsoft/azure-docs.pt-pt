---
title: Controlo de acesso baseado em funções para contas de serviços de multimédia - Azure | Documentos da Microsoft
description: Este artigo aborda o controlo de acesso baseado em funções (RBAC) para contas de serviços de multimédia do Azure.
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
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236907"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Controlo de acesso baseado em funções (RBAC) para contas de serviços de multimédia

Atualmente, os serviços de multimédia do Azure não define quaisquer funções personalizadas específicas para o serviço. Para obter acesso total à conta de Media Services, os clientes podem utilizar as funções incorporadas de **proprietário** ou **contribuinte**. A principal diferença entre estas funções é: o **proprietário** pode controlar quem tem acesso a um recurso e o **contribuinte** não é possível. O incorporado **leitor** também pode ser utilizada a função, mas o utilizador ou a aplicação apenas terá acesso de leitura para as APIs de serviços de multimédia. 

## <a name="design-principles"></a>Princípios de conceção

Um dos principais princípios de design da API v3 é tornar a API mais segura. v3 APIs não devolverem segredos ou as credenciais no **Obtenha** ou **lista** operações. As chaves são sempre nulas, vazias ou saneadas da resposta. O utilizador precisa para chamar um método de ação separada para obter segredos ou as credenciais. O **leitor** função não é possível chamar operações como Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Ter ações separadas permite-lhe definir permissões de segurança mais granulares do RBAC numa função personalizada, se assim o desejar.

Para listar as operações dos serviços de multimédia suporta, fazer:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

O [definições de funções incorporadas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) artigo mostra-lhe exatamente o que a função concede. 

Veja os artigos seguintes para obter mais informações:

- [Funções de administrador de subscrição clássica, funções RBAC do Azure e funções de administrador do Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [O que é o RBAC para recursos do Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Utilizar o RBAC para gerir o acesso](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Operações de fornecedor de recursos de serviços de multimédia](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Passos Seguintes

- [Desenvolvimento com os serviços de multimédia de v3 APIs](media-services-apis-overview.md)
- [Obter política chave conteúda através do .NET de serviços de suporte de dados](get-content-key-policy-dotnet-howto.md)
