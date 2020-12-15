---
title: Definir vidas para fichas
titleSuffix: Microsoft identity platform
description: Saiba como definir vidas para fichas emitidas pela plataforma de identidade microsoft. Aprenda a gerir a política padrão de uma organização, crie uma política de sessão web, crie uma política para uma aplicação nativa que chame uma API web e gere uma política avançada.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: e663cdd3846e804d1dcf96076c07b9a3db84272c
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507749"
---
# <a name="configure-token-lifetime-policies-preview"></a>Configure políticas de vida útil simbólicas (pré-visualização)
Muitos cenários são possíveis no Azure AD quando você pode criar e gerir vidas simbólicas para apps, diretores de serviços e sua organização geral.  

> [!IMPORTANT]
> Depois de maio de 2020, os inquilinos deixarão de poder configurar a renovação e a duração da sessão.  O Azure Ative Directory deixará de honrar a configuração de atualização e ficha de sessão existente em políticas após 30 de janeiro de 2021. Ainda pode configurar o acesso a vidas simbólicas após a depreciação.  Para saber mais, leia [as vidas de símbolos configurais na plataforma de identidade da Microsoft.](active-directory-configurable-token-lifetimes.md)
> Implementamos capacidades de [gestão de sessão de autenticação](../conditional-access/howto-conditional-access-session-lifetime.md)   no Acesso Condicionado AZURE AD. Pode utilizar esta nova funcionalidade para configurar as vidas de token de atualização, definindo o sinal na frequência.


Nesta secção, passamos por alguns cenários políticos comuns que podem ajudá-lo a impor novas regras para:

* Vida útil simbólica
* Tempo inativo token max
* Idade máxima token

Nos exemplos, pode aprender a:

* Gerir a política de incumprimento de uma organização
* Criar uma política para o sessão web
* Crie uma política para uma aplicação nativa que chame uma API web
* Gerir uma política avançada

## <a name="prerequisites"></a>Pré-requisitos
Nos exemplos seguintes, cria, atualiza, liga e elimina políticas para apps, diretores de serviços e organização em geral. Se você é novo no AZure AD, recomendamos que você aprenda sobre [como obter um inquilino AZure AD](quickstart-create-new-tenant.md) antes de prosseguir com estes exemplos.  

Para começar, faça os seguintes passos:

1. Descarregue o mais recente lançamento do [Módulo Público Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
2. Executar o `Connect` comando para iniciar seduca na sua conta de administração Azure. Executar este comando cada vez que começar uma nova sessão.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Para ver todas as políticas que foram criadas na sua organização, executar o seguinte comando. Executar este comando após a maioria das operações nos seguintes cenários. Executar o comando também ajuda a obter o ** ** das suas políticas.

    ```powershell
    Get-AzureADPolicy
    ```

## <a name="manage-an-organizations-default-policy"></a>Gerir a política de incumprimento de uma organização
Neste exemplo, cria-se uma política que permite que os seus utilizadores assinem menos frequentemente em toda a sua organização. Para isso, crie uma política de vida simbólica para tokens de atualização de fatores únicos, que é aplicado em toda a sua organização. A política é aplicada a todas as aplicações da sua organização, e a cada diretor de serviço que ainda não tenha um conjunto de políticas.

1. Criar uma política de vida simbólica.

    1. Desacorra o token de atualização de fator único para "até revogar". O símbolo não expira até que o acesso seja revogado. Criar a seguinte definição de política:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Para criar a política, executar o [cmdlet New-AzureADPolicy:](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Para remover qualquer espaço em branco, executar o [cmdlet Get-AzureADPolicy:](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Para ver a sua nova política, e para obter o **ObjectId** da apólice, executar o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Atualize a apólice.

    Pode decidir que a primeira política que deu neste exemplo não é tão rigorosa como o seu serviço exige. Para definir o seu token de atualização de fator único para expirar em dois dias, executar o seguinte comando:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

## <a name="create-a-policy-for-web-sign-in"></a>Criar uma política para o sessão web

Neste exemplo, cria-se uma política que exige que os utilizadores autentem mais frequentemente na sua aplicação web. Esta política define a vida útil dos tokens de acesso/ID e a idade máxima de um token de sessão multi-factor para o principal de serviço da sua aplicação web.

1. Criar uma política de vida simbólica.

    Esta política, para o início de sessão web, define a vida útil do token de acesso/ID e a idade máxima de token de sessão de fator único para duas horas.

    1. Para criar a política, executar o [cmdlet New-AzureADPolicy:](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Para ver a sua nova política, e para obter a política **ObjectId,** executar o cmdlet [Get-AzureADPolicy:](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Atribua a apólice ao seu diretor de serviço. Também precisa de obter o **ObjectId** do seu principal de serviço.

    1. Utilize o [cmdlet Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) para ver todos os principais serviços da sua organização ou um único responsável de serviço.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Quando tiver o principal serviço, execute o [cmdlet Add-AzureADServicePrincipalPolicy:](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true)
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Crie uma política para uma aplicação nativa que chame uma API web
Neste exemplo, cria-se uma política que exige que os utilizadores autentem com menos frequência. A política também prolonga o tempo que um utilizador pode estar inativo antes de o utilizador ter de reautenticar. A política é aplicada à API web. Quando a aplicação nativa solicita a API web como um recurso, esta política é aplicada.

1. Criar uma política de vida simbólica.

    1. Para criar uma política rigorosa para uma API web, executar o cmdlet [New-AzureADPolicy:](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Para ver a sua nova política, executar o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Atribua a apólice à sua API web. Também precisa de obter o **ObjectId** da sua aplicação. Utilize o [cmdlet Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) para encontrar o **ObjectId** da sua aplicação, ou utilize o [portal Azure](https://portal.azure.com/).

    Obtenha o **ObjectId** da sua aplicação e atribua a política:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

## <a name="manage-an-advanced-policy"></a>Gerir uma política avançada
Neste exemplo, cria-se algumas políticas para aprender como funciona o sistema prioritário. Também aprende a gerir várias políticas que são aplicadas a vários objetos.

1. Criar uma política de vida simbólica.

    1. Para criar uma política de incumprimento da organização que define a duração de vida simbólica de um único fator para 30 dias, executar o cmdlet [New-AzureADPolicy:](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Para ver a sua nova política, gere o [cmdlet Get-AzureADPolicy:](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Atribua a apólice a um diretor de serviço.

    Agora, tem uma política que se aplica a toda a organização. Talvez queira preservar esta política de 30 dias para um responsável de serviços específico, mas mude a política de incumprimento da organização para o limite máximo de "até revogação".

    1. Para ver todos os principais serviços da sua organização, utilize o [cmdlet Get-AzureADServicePrincipal.](/powershell/module/azuread/get-azureadserviceprincipal)

    1. Quando tiver o principal serviço, execute o [cmdlet Add-AzureADServicePrincipalPolicy:](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true)

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Coloque a `IsOrganizationDefault` bandeira em falso:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Criar uma nova política de incumprimento da organização:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Tem agora a política original ligada ao seu principal de serviço, e a nova política está definida como a política de incumprimento da sua organização. É importante lembrar que as políticas aplicadas aos principais serviços têm prioridade sobre as políticas de incumprimento da organização.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [as capacidades de gestão da sessão de autenticação](../conditional-access/howto-conditional-access-session-lifetime.md) no Acesso Condicionado Azure AD.