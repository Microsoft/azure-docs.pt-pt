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
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 212d7f5352b58ee8f5b2c119bb1f5f828591f6bf
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490780"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Controlo de acesso baseado em funções (RBAC) para contas de serviços de multimédia

Atualmente, os serviços de multimédia do Azure não definida qualquer específicas de funções personalizadas para o serviço. Os clientes podem utilizar as funções incorporadas de **proprietário** ou **contribuinte** para obter acesso completo para uma conta de Media Services. A principal diferença entre estas funções é: o **proprietário** pode controlar quem tem acesso a um recurso e o **contribuinte** não é possível. A conta do leitor incorporado só tem acesso de leitura à conta de Media Services. 

## <a name="design-principles"></a>Princípios de conceção

Um dos principais princípios de design da API v3 é tornar a API mais segura. v3 APIs não devolverem segredos ou as credenciais no **Obtenha** ou **lista** operações. As chaves são sempre nulas, vazias ou saneadas da resposta. O utilizador precisa para chamar um método de ação separada para obter segredos ou as credenciais. O **leitor** função não é possível chamar operações para que ele não é possível chamar operações como Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Ter ações separadas permite-lhe definir permissões de segurança mais granulares do RBAC numa função personalizada, se assim o desejar.

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
- [Obter política chave conteúda através do .NET de serviços de suporte de dados](get-content-key-policy-dotnet-howto.md)

## <a name="next-steps"></a>Passos Seguintes

[Desenvolvimento com os serviços de multimédia de v3 APIs](media-services-apis-overview.md)
