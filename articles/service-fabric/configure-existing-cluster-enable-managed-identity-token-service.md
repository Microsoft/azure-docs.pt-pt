---
title: Configure suporte de identidade gerido num cluster de Tecido de Serviço existente
description: Aqui está como permitir o suporte de identidades geridas em um cluster de tecido de serviço Azure existente
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: cb6e4ab00afd80cba41881e46296f7046a905919
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934954"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster-preview"></a>Configure suporte de identidade gerido num cluster de tecido de serviço existente (pré-visualização)

Para utilizar [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) nas suas aplicações De Tecido de Serviço, primeiro ative o Serviço de *Fichas* de Identidade Gerida no cluster. Esse serviço é responsável pela autenticação de Service Fabric aplicativos usando suas identidades gerenciadas e pela obtenção de tokens de acesso em seu nome. Quando o serviço estiver habilitado, você poderá vê-lo em Service Fabric Explorer na seção **sistema** no painel esquerdo, executando sob o nome **Fabric:/System/ManagedIdentityTokenService**.

> [!NOTE]
> O Service Fabric Runtime versão 6.5.658.9590 ou superior é necessário para habilitar o **serviço de token de identidade gerenciado**.  
>
> Você pode encontrar a versão Service Fabric de um cluster do portal do Azure abrindo o recurso de cluster e verificando a propriedade **versão do Service Fabric** na seção **Essentials** .
>
> Se o cluster estiver no modo de atualização **manual** , será necessário primeiro atualizá-lo para 6.5.658.9590 ou posterior.

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

Para que as alterações entrem em vigor, você também precisará alterar a política de atualização para especificar uma reinicialização forçada do tempo de execução de Service Fabric em cada nó à medida que a atualização progride por meio do cluster. Essa reinicialização garante que o serviço de sistema habilitado recentemente seja iniciado e em execução em cada nó. No trecho de código abaixo, `forceRestart` é a configuração essencial; Use seus valores existentes para o restante das configurações.  

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
> Após a conclusão bem-sucedida da atualização, não se esqueça de reverter a configuração de `forceRestart` para minimizar o impacto das atualizações subsequentes. 

## <a name="errors-and-troubleshooting"></a>Erros e solução de problemas

Se a implantação falhar com a mensagem a seguir, isso significa que o cluster não está sendo executado em uma versão de Service Fabric alta o suficiente:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Passos seguintes
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Aproveite a identidade gerenciada de um aplicativo Service Fabric do código de serviço](./how-to-managed-identity-service-fabric-app-code.md)
* [Conceder a um aplicativo de Service Fabric do Azure acesso a outros recursos do Azure](./how-to-grant-access-other-resources.md)