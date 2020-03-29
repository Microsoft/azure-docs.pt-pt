---
title: Configure suporte de identidade gerido num cluster de Tecido de Serviço existente
description: Aqui está como permitir o suporte de identidades geridas em um cluster de tecido de serviço Azure existente
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: cb6e4ab00afd80cba41881e46296f7046a905919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934954"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster-preview"></a>Configure suporte de identidade gerido num cluster de tecido de serviço existente (pré-visualização)

Para utilizar [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) nas suas aplicações De Tecido de Serviço, primeiro ative o Serviço de *Fichas* de Identidade Gerida no cluster. Este serviço é responsável pela autenticação de aplicações de Tecido de Serviço utilizando as suas identidades geridas e pela obtenção de fichas de acesso em seu nome. Uma vez ativado o serviço, pode vê-lo no Service Fabric Explorer sob a secção **Sistema** no painel esquerdo, funcionando sob o nome **de tecido:/System/ManagedIdentityTokenService**.

> [!NOTE]
> A versão de execução do tecido de serviço 6.5.658.9590 ou superior é necessária para ativar o Serviço de **Fichas**de Identidade Gerida .  
>
> Pode encontrar a versão Service Fabric de um cluster do portal Azure abrindo o recurso de cluster e verificando a propriedade da **versão Service Fabric** na secção **Essentials.**
>
> Se o cluster estiver no modo de atualização **manual,** terá de o atualizar primeiro para 6.5.658.9590 ou mais tarde.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Ativar *o Serviço de Fichas* de Identidade Gerida num cluster existente

Para ativar o Serviço de Fichas de Identidade Gerida num cluster existente, terá de iniciar uma atualização de cluster especificando duas alterações: (1) Habilitar o Serviço de Fichas de Identidade Gerida e (2) solicitar o reinício de cada nó. Em primeiro lugar, adicione o seguinte corte do seu modelo de gestor de recursos azure cluster:

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

Para que as alterações entrem em vigor, também terá de alterar a política de atualização para especificar um reinício vigoroso do tempo de execução do Tecido de Serviço em cada nó à medida que a atualização progride através do cluster. Este reinício garante que o serviço de sistema recém-activado é iniciado e funcionando em cada nó. No corte abaixo, `forceRestart` encontra-se a definição essencial; utilize os valores existentes para o resto das definições.  

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
> Após a conclusão bem sucedida da atualização, não se esqueça de reverter a `forceRestart` definição, para minimizar o impacto das atualizações subsequentes. 

## <a name="errors-and-troubleshooting"></a>Erros e resolução de problemas

Se a implementação falhar com a seguinte mensagem, significa que o cluster não está a funcionar numa versão suficientemente alta do Tecido de Serviço:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Passos seguintes
* [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída pelo sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída pelo utilizador](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Alavancar a identidade gerida de uma aplicação Service Fabric a partir do código de serviço](./how-to-managed-identity-service-fabric-app-code.md)
* [Conceder um acesso à aplicação Azure Service Fabric a outros recursos azure](./how-to-grant-access-other-resources.md)