---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 4eb794fa35164e3f86a5e3d6f67d446321f91f0a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67133371"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Prepare-se para autenticar pedidos do Gestor de Recursos Azure
Deve autenticar todas as operações que realiza em recursos utilizando o Gestor de [Recursos Azure][lnk-authenticate-arm] com o Diretório Ativo Azure (AD). A forma mais fácil de configurar isto é utilizar o PowerShell ou o Azure CLI.

Instale os [cmdlets Azure PowerShell][lnk-powershell-install] antes de continuar.

Os seguintes passos mostram como configurar a autenticação de senha para uma aplicação AD utilizando powerShell. Pode executar estes comandos numa sessão padrão da PowerShell.

1. Inscreva-se na subscrição do Azure utilizando o seguinte comando:

    ```powershell
    Connect-AzAccount
    ```

1. Se tiver várias subscrições do Azure, a sessão no Azure dá-lhe acesso a todas as subscrições do Azure associadas às suas credenciais. Utilize o seguinte comando para listar as subscrições Azure disponíveis para que possa utilizar:

    ```powershell
    Get-AzSubscription
    ```

    Utilize o seguinte comando para selecionar a subscrição que pretende utilizar para executar os comandos para gerir o seu hub IoT. Pode utilizar o nome ou o ID da subscrição da saída do comando anterior:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Tome nota do seu **TenantId** e **SubscriptionId**. Vai precisar deles mais tarde.
3. Criar uma nova aplicação Azure Ative Directory utilizando o seguinte comando, substituindo os suportes de lugares:
   
   * **{Nome do ecrã}:** um nome de exibição para a sua aplicação como **MySampleApp**
   * **{Home page URL}:** o URL da página inicial da sua aplicação como **http:\//mysampleapp/home**. Este URL não precisa de apontar para uma aplicação real.
   * **{Identificador de aplicação}:** Um identificador único como **http:\//mysampleapp**. Este URL não precisa de apontar para uma aplicação real.
   * **{Password}:** Uma palavra-passe que usa para autenticar com a sua aplicação.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Tome nota do **Id** aplicação da aplicação que criou. Precisa disto mais tarde.
5. Criar um novo diretor de serviço utilizando o seguinte comando, substituindo **{MyApplicationId}** pelo **ApplicationId** do passo anterior:
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Configurar uma tarefa utilizando o seguinte comando, substituindo **{MyApplicationId}** pelo seu **Id aplicação**.
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Já terminou de criar a aplicação Azure AD que lhe permite autenticar a partir da sua aplicação C# personalizada. Você precisa dos seguintes valores mais tarde neste tutorial:

* TenantId
* SubscriptionId
* ApplicationID
* Palavra-passe

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: /powershell/azure/install-az-ps
