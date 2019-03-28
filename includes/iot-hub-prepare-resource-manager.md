---
author: robinsh
ms.author: robin.shahan
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: c30f31903e59c505d778c95c58adbb48af0db96f
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505795"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Preparar para autenticar pedidos do Azure Resource Manager
Tem de autenticar todas as operações que executar nos recursos com o [do Azure Resource Manager] [ lnk-authenticate-arm] com o Azure Active Directory (AD). A maneira mais fácil de configurar esta opção é utilizar o PowerShell ou da CLI do Azure.

Instalar o [cmdlets do Azure PowerShell] [ lnk-powershell-install] antes de continuar.

Os passos seguintes mostram como configurar a autenticação de palavra-passe para uma aplicação do AD com o PowerShell. Pode executar estes comandos numa sessão do PowerShell padrão.

1. Inicie sessão sua subscrição do Azure com o seguinte comando:

    ```powershell
    Connect-AzAccount
    ```

1. Se tiver várias subscrições do Azure, iniciar sessão no Azure dá-lhe acesso a todas as subscrições Azure associadas com as suas credenciais. Utilize o seguinte comando para listar as subscrições do Azure disponíveis que pode utilizar:

    ```powershell
    Get-AzSubscription
    ```

    Utilize o seguinte comando para selecionar a subscrição que pretende utilizar para executar os comandos para gerir o seu hub IoT. Pode utilizar o nome ou o ID da subscrição da saída do comando anterior:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Tome nota da sua **TenantId** e **SubscriptionId**. Vai precisar deles mais tarde.
3. Crie uma nova aplicação do Azure Active Directory com o seguinte comando, substituindo os espaços reservados:
   
   * **{Nome a apresentar}:** um nome a apresentar para a sua aplicação, tais como **MySampleApp**
   * **{URL da Home page}:** o URL da home page da sua aplicação, tais como **http:\//mysampleapp/doméstica**. Este URL não é necessário apontar para um aplicativo real.
   * **{Identificador da aplicação}:** Um identificador exclusivo, como **http:\//mysampleapp**. Este URL não é necessário apontar para um aplicativo real.
   * **{Password}:** Uma palavra-passe que utiliza para autenticar com a sua aplicação.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Anote o **ApplicationId** da aplicação que criou. Precisa deste mais tarde.
5. Crie um novo principal de serviço com o seguinte comando, substituindo **{MyApplicationId}** com o **ApplicationId** do passo anterior:
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Configurar uma atribuição de função com o seguinte comando, substituindo **{MyApplicationId}** com o seu **ApplicationId**.
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Agora concluir a criação de aplicação do Azure AD, que permite-lhe autenticar a partir da sua personalizada C# aplicação. Terá dos seguintes valores mais tarde neste tutorial:

* TenantId
* SubscriptionId
* ApplicationId
* Palavra-passe

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/azurerm/install-Az-ps
