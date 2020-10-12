---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 4eb794fa35164e3f86a5e3d6f67d446321f91f0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67133371"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Prepare-se para autenticar os pedidos do Gestor de Recursos Azure
Deve autenticar todas as operações que executa em recursos utilizando o [Azure Resource Manager][lnk-authenticate-arm] com o Azure Ative Directory (AD). A maneira mais fácil de configurar isto é usar PowerShell ou Azure CLI.

Instale os [cmdlets Azure PowerShell][lnk-powershell-install] antes de continuar.

Os passos a seguir mostram como configurar a autenticação de senha para uma aplicação AD utilizando o PowerShell. Pode executar estes comandos numa sessão padrão do PowerShell.

1. Inscreva-se na sua assinatura Azure utilizando o seguinte comando:

    ```powershell
    Connect-AzAccount
    ```

1. Se tiver várias subscrições do Azure, a subscrição do Azure dá-lhe acesso a todas as subscrições do Azure associadas às suas credenciais. Utilize o seguinte comando para listar as subscrições Azure disponíveis para utilizar:

    ```powershell
    Get-AzSubscription
    ```

    Utilize o seguinte comando para selecionar a subscrição que pretende utilizar para executar os comandos para gerir o seu hub IoT. Pode utilizar o nome ou o ID da subscrição da saída do comando anterior:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Tome nota do seu **TenantId** e **SubscriptionId.** Vai precisar deles mais tarde.
3. Criar uma nova aplicação Azure Ative Directory utilizando o seguinte comando, substituindo os detentores de lugares:
   
   * **{Display name}:** um nome de exibição para a sua aplicação, tal como **MySampleApp**
   * **{URL página inicial}:** o URL da página inicial da sua aplicação, tal como **http: \/ /mysampleapp/home**. Esta URL não precisa de apontar para uma aplicação real.
   * **{Identificador de aplicação}:** Um identificador único, como **http: \/ /mysampleapp**. Esta URL não precisa de apontar para uma aplicação real.
   * **{Palavra-passe}:** Uma palavra-passe que utiliza para autenticar com a sua aplicação.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Tome nota do **ApplicationId** da aplicação que criou. Precisa disto mais tarde.
5. Criar um novo chefe de serviço utilizando o seguinte comando, substituindo **{MyApplicationId}** com o **ApplicationId** do passo anterior:
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Configurar uma atribuição de função utilizando o seguinte comando, substituindo **{MyApplicationId}** pela sua **ApplicationId**.
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Terminou agora a criação da aplicação AZure AD que lhe permite autenticar a partir da sua aplicação C# personalizada. Precisa dos seguintes valores mais tarde neste tutorial:

* TenantId
* SubscriptionId
* ApplicationID
* Palavra-passe

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: /powershell/azure/install-az-ps
