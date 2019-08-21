---
title: Service Fabric do Azure – implantar um novo cluster de Service Fabric do Azure com suporte para identidade gerenciada | Microsoft Docs
description: Este artigo mostra como criar um novo cluster Service Fabric com identidade gerenciada habilitada
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: cb32251c2507f3def7f5e830af15828ff2f04c29
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640727"
---
# <a name="create-a-new-azure-service-fabric-cluster-with-managed-identity-support-preview"></a>Criar um novo cluster de Service Fabric do Azure com suporte de identidade gerenciada (versão prévia)

Para acessar o recurso de identidade gerenciada para aplicativos Service Fabric do Azure, primeiro você deve habilitar o serviço de token de identidade gerenciado no cluster. Esse serviço é responsável pela autenticação de Service Fabric aplicativos usando suas identidades gerenciadas e pela obtenção de tokens de acesso em seu nome. Quando o serviço estiver habilitado, você poderá vê-lo em Service Fabric Explorer na seção **sistema** no painel esquerdo, executando sob o nome **Fabric:/System/ManagedIdentityTokenService** ao lado de outros serviços do sistema.

> [!NOTE]
> O Service Fabric Runtime versão 6.5.658.9590 ou superior é necessário para habilitar o **serviço de token de identidade gerenciado**.  

## <a name="enable-the-managed-identity-token-service"></a>Habilitar o serviço de token de identidade gerenciado 
Para habilitar o serviço de token de identidade gerenciado no momento da criação do cluster, você pode usar o seguinte trecho em um modelo de Azure Resource Manager:

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

Se a implantação falhar com essa mensagem, isso significa que o cluster não está na versão necessária do Service Fabric (o tempo de execução mínimo com suporte é 6,5 CU2):



```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```


## <a name="related-articles"></a>Artigos relacionados
* Examinar o [suporte de identidade gerenciada](./concepts-managed-identity.md) no Azure Service Fabric

* [Habilitar o suporte de identidade gerenciada em um cluster existente do Azure Service Fabric](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Passos seguintes
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Aproveite a identidade gerenciada de um aplicativo Service Fabric do código de serviço](./how-to-managed-identity-service-fabric-app-code.md)
* [Conceder a um aplicativo de Service Fabric do Azure acesso a outros recursos do Azure](./how-to-grant-access-other-resources.md)