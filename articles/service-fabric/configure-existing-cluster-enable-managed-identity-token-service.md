---
title: Service Fabric do Azure – configurar um cluster existente do Service Fabric do Azure para habilitar o suporte de identidade gerenciada | Microsoft Docs
description: Este artigo mostra como configurar um cluster existente do Service Fabric do Azure para habilitar o suporte para identidades gerenciadas
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: adc21358011454c8687998dc5d257052959b933b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640739"
---
# <a name="configure-an-existing-azure-service-fabric-cluster-to-enable-managed-identity-support-preview"></a>Configurar um cluster existente do Service Fabric do Azure para habilitar o suporte de identidade gerenciada (versão prévia)
Para acessar o recurso de identidade gerenciada para aplicativos Service Fabric do Azure, primeiro você deve habilitar o **serviço de token de identidade gerenciado** no cluster. Esse serviço é responsável pela autenticação de Service Fabric aplicativos usando suas identidades gerenciadas e pela obtenção de tokens de acesso em seu nome. Quando o serviço estiver habilitado, você poderá vê-lo em Service Fabric Explorer na seção **sistema** no painel esquerdo, executando sob o nome **Fabric:/System/ManagedIdentityTokenService**.

> [!NOTE]
> O Service Fabric Runtime versão 6.5.658.9590 ou superior é necessário para habilitar o **serviço de token de identidade gerenciado**.  
> 
> Você pode encontrar a versão Service Fabric de um cluster do portal do Azure abrindo o recurso de cluster e verificando a propriedade **versão do Service Fabric** na seção **Essentials** .
> 
> Se o cluster estiver no modo de atualização **manual** , será necessário primeiro atualizá-lo para 6.5.658.9590 ou posterior.


## <a name="enable-the-managed-identity-token-service-in-an-existing-cluster"></a>Habilitar o serviço de token de identidade gerenciado em um cluster existente
Para habilitar o serviço de token de identidade gerenciado em um cluster existente, você precisará iniciar uma atualização de cluster especificando duas alterações: Habilitando o serviço de token de identidade gerenciado e solicitando uma reinicialização de cada nó. Para fazer isso, adicione os dois trechos de código a seguir no modelo de Azure Resource Manager:

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

Para que as alterações entrem em vigor, você também precisará alterar a política de atualização para especificar uma reinicialização forçada do tempo de execução de Service Fabric em cada nó à medida que a atualização progride por meio do cluster. Essa reinicialização garante que o serviço de sistema habilitado recentemente seja iniciado e em execução em cada nó. No trecho de código abaixo `forceRestart` , é a configuração essencial; use os valores existentes para o restante das configurações.  

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
> Após a conclusão bem-sucedida da atualização, não se esqueça de reverter a `forceRestart` configuração para minimizar o impacto das atualizações subsequentes. 

## <a name="errors-and-troubleshooting"></a>Erros e solução de problemas

Se a implantação falhar com a mensagem a seguir, isso significa que o cluster não está sendo executado em uma versão de Service Fabric alta o suficiente:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Passos Seguintes
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Aproveite a identidade gerenciada de um aplicativo Service Fabric do código de serviço](./how-to-managed-identity-service-fabric-app-code.md)
* [Conceder a um aplicativo de Service Fabric do Azure acesso a outros recursos do Azure](./how-to-grant-access-other-resources.md)