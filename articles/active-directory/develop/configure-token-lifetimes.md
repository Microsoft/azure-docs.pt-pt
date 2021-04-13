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
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 66e9817c6d3bbcd199418b9afd78eda016c5f291
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363891"
---
# <a name="configure-token-lifetime-policies-preview"></a>Configure políticas de vida útil simbólicas (pré-visualização)
Pode especificar o tempo de vida útil de um token de acesso, SAML ou ID emitido pela plataforma de identidade Microsoft. Pode definir durações de tokens para todas as aplicações existentes na sua organização, para uma aplicação multi-inquilino (com várias organizações) ou para um principal de serviço específico na sua organização. Para mais informações, leia [as vidas simbólicas configuráveis.](active-directory-configurable-token-lifetimes.md)

Nesta secção, passamos por um cenário político comum que pode ajudá-lo a impor novas regras para a vida útil. No exemplo, aprende-se a criar uma política que exige que os utilizadores autentem mais frequentemente na sua aplicação web.

## <a name="get-started"></a>Introdução

Para começar, descarregue o mais recente lançamento do [Módulo Público Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Em seguida, executar o `Connect` comando para iniciar seduca na sua conta de administração Azure. Executar este comando cada vez que começar uma nova sessão.

```powershell
Connect-AzureAD -Confirm
```

## <a name="create-a-policy-for-web-sign-in"></a>Criar uma política para o sessão web

Neste exemplo, cria-se uma política que exige que os utilizadores autentem mais frequentemente na sua aplicação web. Esta política define a vida útil dos tokens de acesso/ID ao principal serviço da sua aplicação web.

1. Criar uma política de vida simbólica.

    Esta política, para o início de seduque, define o prazo de vida de acesso/ID para duas horas.

    Para criar a política, executar o [cmdlet New-AzureADPolicy:](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)

    ```powershell
    $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
    ```

    Para ver a sua nova política, e para obter a política **ObjectId,** executar o cmdlet [Get-AzureADPolicy:](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)

    ```powershell
    Get-AzureADPolicy -Id $policy.Id
    ```

1. Atribua a apólice ao seu diretor de serviço. Também precisa de obter o **ObjectId** do seu principal de serviço.

    Utilize o [cmdlet Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) para ver todos os principais serviços da sua organização ou um único responsável de serviço.

    ```powershell
    # Get ID of the service principal
    $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
    ```

    Quando tiver o principal serviço, execute o [cmdlet Add-AzureADServicePrincipalPolicy:](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true)

    ```powershell
    # Assign policy to a service principal
    Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
    ```

## <a name="view-existing-policies-in-a-tenant"></a>Ver as políticas existentes num inquilino

Para ver todas as políticas que foram criadas na sua organização, gere o [cmdlet Get-AzureADPolicy.](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)  Quaisquer resultados com valores de propriedade definidos que diferem dos incumprimentos acima indicados estão no âmbito da reforma.

```powershell
Get-AzureADPolicy -All $true
```

Para ver quais aplicações e principais serviços estão ligados a uma política específica que identificou executar o seguinte [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) cmdlet, substituindo **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** por qualquer um dos seus IDs de política. Em seguida, pode decidir se configurar a frequência de acesso condicional ou permanecer com as predefinições Azure AD.

```powershell
Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
```

Se o seu inquilino tiver políticas que definam valores personalizados para as propriedades de configuração de atualização e sessão, a Microsoft recomenda que atualize essas políticas para valores que reflitam os padrão acima descritos. Se não forem feitas alterações, o Azure AD honrará automaticamente os valores predefinidos.

### <a name="troubleshooting"></a>Resolução de problemas
Alguns utilizadores relataram um `Get-AzureADPolicy : The term 'Get-AzureADPolicy' is not recognized` erro depois de executarem o `Get-AzureADPolicy` cmdlet. Como solução alternativa, confende o seguinte para desinstalar/reinstalar o módulo AzureAD e, em seguida, instalar o módulo AzureADPreview:

```powershell
# Uninstall the AzureAD Module
UnInstall-Module AzureAD

# Re-install the AzureAD Module
Install-Module AzureAD

# Install the AzureAD Preview Module adding the -AllowClobber
Install-Module AzureADPreview -AllowClobber

Connect-AzureAD
Get-AzureADPolicy -All $true
```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [as capacidades de gestão da sessão de autenticação](../conditional-access/howto-conditional-access-session-lifetime.md) no Acesso Condicionado Azure AD.
