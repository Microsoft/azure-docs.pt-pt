---
title: Configure suporte de identidade gerido para um novo cluster de Tecido de Serviço
description: Eis como permitir o suporte de identidades geridas num novo cluster azure service Fabric
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 0e35d2192fdcdb294b349105f3f0158564cec86b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76930469"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster-preview"></a>Configure suporte de identidade gerido para um novo cluster de Tecido de Serviço (pré-visualização)

Para utilizar [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) nas suas aplicações De Tecido de Serviço, primeiro ative o Serviço de *Fichas* de Identidade Gerida no cluster. Este serviço é responsável pela autenticação de aplicações de Tecido de Serviço utilizando as suas identidades geridas e pela obtenção de fichas de acesso em seu nome. Uma vez ativado o serviço, pode vê-lo no Service Fabric Explorer sob a secção **Sistema** no painel esquerdo, funcionando sob o nome **de tecido:/System/ManagedIdentityTokenService** ao lado de outros serviços do sistema.

> [!NOTE]
> A versão de execução do tecido de serviço 6.5.658.9590 ou superior é necessária para ativar o Serviço de **Fichas**de Identidade Gerida .  

## <a name="enable-the-managed-identity-token-service"></a>Ativar o serviço de fichas de identidade gerida

Para ativar o Serviço de Fichas de Identidade Gerida no tempo de criação do cluster, adicione o seguinte corte ao modelo do gestor de recursos azure cluster:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>Erros

Se a implementação falhar com esta mensagem, significa que o cluster não está na versão de Tecido de Serviço exigido (o tempo de funcionamento mínimo suportado é de 6,5 CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Artigos Relacionados

* Reveja [o suporte de identidade gerido](./concepts-managed-identity.md) no Tecido de Serviço Azure

* [Ativar suporte de identidade gerido num cluster de tecido de serviço Azure existente](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Passos seguintes

* [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída pelo sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída pelo utilizador](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Alavancar a identidade gerida de uma aplicação Service Fabric a partir do código de serviço](./how-to-managed-identity-service-fabric-app-code.md)
* [Conceder um acesso à aplicação Azure Service Fabric a outros recursos azure](./how-to-grant-access-other-resources.md)