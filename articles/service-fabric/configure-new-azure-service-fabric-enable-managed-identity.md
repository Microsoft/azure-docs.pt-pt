---
title: Configure suporte de identidade gerido para um novo cluster de Tecido de Serviço
description: Eis como permitir o suporte de identidades geridas num novo cluster azure service Fabric
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 0e35d2192fdcdb294b349105f3f0158564cec86b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76930469"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster-preview"></a>Configure suporte de identidade gerido para um novo cluster de Tecido de Serviço (pré-visualização)

Para utilizar [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) nas suas aplicações De Tecido de Serviço, primeiro ative o Serviço de *Fichas* de Identidade Gerida no cluster. Esse serviço é responsável pela autenticação de Service Fabric aplicativos usando suas identidades gerenciadas e pela obtenção de tokens de acesso em seu nome. Quando o serviço estiver habilitado, você poderá vê-lo em Service Fabric Explorer na seção **sistema** no painel esquerdo, executando sob o nome **Fabric:/System/ManagedIdentityTokenService** ao lado de outros serviços do sistema.

> [!NOTE]
> O Service Fabric Runtime versão 6.5.658.9590 ou superior é necessário para habilitar o **serviço de token de identidade gerenciado**.  

## <a name="enable-the-managed-identity-token-service"></a>Habilitar o serviço de token de identidade gerenciado

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