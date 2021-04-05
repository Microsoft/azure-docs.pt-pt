---
title: Configurar suporte de identidade gerido num cluster de tecido de serviço existente
description: Aqui está como permitir o suporte de identidades geridas em um cluster de tecido de serviço Azure existente
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: dc341688cae6d98f53be10351e4e4572a3539e4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790044"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>Configurar suporte de identidade gerido num cluster de tecido de serviço existente

Para utilizar [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) nas suas aplicações de Tecido de Serviço, primeiro ative o *Serviço de Token de Identidade Gerida* no cluster. Este serviço é responsável pela autenticação de aplicações do Service Fabric utilizando as suas identidades geridas, e pela obtenção de fichas de acesso em seu nome. Uma vez que o serviço esteja ativado, pode vê-lo no Service Fabric Explorer sob a secção **Sistema** no painel esquerdo, funcionando sob o tecido de **nome:/System/ManagedIdentityTokenService**.

> [!NOTE]
> A versão de execução do tecido de serviço 6.5.658.9590 é necessária para ativar o **Serviço de Token de Identidade Gerida**.  
>
> Pode encontrar a versão Service Fabric de um cluster a partir do portal Azure, abrindo o recurso de cluster e verificando a propriedade da **versão Service Fabric** na secção **Essentials.**
>
> Se o cluster estiver no modo de atualização **manual,** terá de o atualizar primeiro para 6.5.658.9590 ou mais tarde.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Ativar *o Serviço de Token de Identidade Gerida* num cluster existente

Para ativar o Serviço de Token de Identidade Gerida num cluster existente, terá de iniciar uma atualização de cluster especificando duas alterações: (1) Ativar o Serviço de Token de Identidade Gerida e (2) solicitar o reinício de cada nó. Em primeiro lugar, adicione o seguinte corte do seu modelo de cluster Azure Resource Manager:

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

Para que as alterações entrem em vigor, também terá de alterar a política de atualização para especificar um reinício vigoroso do tempo de funcionamento do Tecido de Serviço em cada nó à medida que a atualização progride através do cluster. Este reinício garante que o serviço de sistema recentemente ativado seja iniciado e em funcionamento em cada nó. No corte abaixo, `forceRestart` é a definição essencial para permitir o reinício. Para os parâmetros restantes, utilize valores descritos abaixo ou utilize valores personalizados já especificados para o recurso cluster. As definições personalizadas para a Política de Atualização do Tecido ('upgradeDscription') podem ser vistas a partir do Portal Azure selecionando a opção 'Fabric Upgrades' no recurso 'Fabric Fabric' ou resources.azure.com. As opções predefinidos para a política de atualização ('upgradeDscription') não são visualizais a partir de powershell ou resources.azure.com. Consulte [clusterUpgradePolicy](/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy) para obter informações adicionais.  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> Após a conclusão bem sucedida da atualização, não se esqueça de reverter a `forceRestart` configuração, para minimizar o impacto das atualizações subsequentes. 

## <a name="errors-and-troubleshooting"></a>Erros e resolução de problemas

Se a implementação falhar com a seguinte mensagem, significa que o cluster não está a funcionar numa versão de Tecido de Serviço suficientemente alta:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Passos seguintes
* [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída ao sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída pelo utilizador](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Alavancar a identidade gerida de uma aplicação de Tecido de Serviço a partir do código de serviço](./how-to-managed-identity-service-fabric-app-code.md)
* [Conceder a uma aplicação Azure Service Fabric acesso a outros recursos da Azure](./how-to-grant-access-other-resources.md)
