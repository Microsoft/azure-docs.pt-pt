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
ms.date: 02/01/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 3ec94543a53e3e5b7709801de8f4cf1dde3fc3d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99428120"
---
# <a name="configure-token-lifetime-policies-preview"></a>Configure políticas de vida útil simbólicas (pré-visualização)
Pode especificar o tempo de vida útil de um token de acesso, SAML ou ID emitido pela plataforma de identidade Microsoft. Pode definir durações de tokens para todas as aplicações existentes na sua organização, para uma aplicação multi-inquilino (com várias organizações) ou para um principal de serviço específico na sua organização. Para mais informações, leia [as vidas simbólicas configuráveis.](active-directory-configurable-token-lifetimes.md)

Nesta secção, passamos por um cenário político comum que pode ajudá-lo a impor novas regras para a vida útil. No exemplo, aprende-se a criar uma política que exige que os utilizadores autentem mais frequentemente na sua aplicação web.

## <a name="get-started"></a>Introdução
Para começar, faça os seguintes passos:

1. Descarregue o mais recente lançamento do [Módulo Público Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Executar o `Connect` comando para iniciar seduca na sua conta de administração Azure. Executar este comando cada vez que começar uma nova sessão.

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. Para ver todas as políticas que foram criadas na sua organização, gere o [cmdlet Get-AzureADPolicy.](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)  Quaisquer resultados com valores de propriedade definidos que diferem dos incumprimentos acima indicados estão no âmbito da reforma.

    ```powershell
    Get-AzureADPolicy -All $true
    ```

1. Para ver quais aplicações e principais serviços estão ligados a uma política específica que identificou executar o seguinte [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) cmdlet, substituindo **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** por qualquer um dos seus IDs de política. Em seguida, pode decidir se configurar a frequência de acesso condicional ou permanecer com as predefinições Azure AD.

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

Se o seu inquilino tiver políticas que definam valores personalizados para as propriedades de configuração de atualização e sessão, a Microsoft recomenda que atualize essas políticas para valores que reflitam os padrão acima descritos. Se não forem feitas alterações, o Azure AD honrará automaticamente os valores predefinidos.

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

## <a name="create-token-lifetime-policies-for-refresh-and-session-tokens"></a>Criar políticas de vida simbólicas para tokens de atualização e sessão
> [!IMPORTANT]
> A partir de 30 de janeiro de 2021 não poderá configurar a renovação e a sessão de token lifes. O Azure Ative Directory já não honra a configuração de atualização e símbolo de sessão nas políticas existentes.  Os novos tokens emitidos após a expiração dos tokens existentes estão agora definidos para a [configuração padrão](active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties-after-the-retirement). Você ainda pode configurar o acesso, SAML, e ID token vidas após a renovação e sessão de configuração de configuração.
>
> A vida útil do símbolo existente não será alterada. Após o seu termo, um novo token será emitido com base no valor padrão.
>
> Se precisar de continuar a definir o período de tempo antes de um utilizador ser convidado a iniciar novamente o súmis, configurar a frequência de inscrição no Acesso Condicional. Para saber mais sobre Acesso Condicional, leia a gestão da [sessão de autenticação configurada com Acesso Condicional.](../conditional-access/howto-conditional-access-session-lifetime.md)

### <a name="manage-an-organizations-default-policy"></a>Gerir a política de incumprimento de uma organização
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

### <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Crie uma política para uma aplicação nativa que chame uma API web
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

### <a name="manage-an-advanced-policy"></a>Gerir uma política avançada
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
