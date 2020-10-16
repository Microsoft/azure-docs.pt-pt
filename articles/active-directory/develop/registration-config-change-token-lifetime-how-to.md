---
title: Alterar incumprimentos de vida de token para aplicações AD personalizadas Azure
description: Como atualizar as políticas da Token Lifetime para a sua aplicação que está a desenvolver no Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 7eb01ccda3c3e13827a8977b8ee0e244aef6b0be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613243"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Como alterar os incumprimentos de vida de símbolo para uma aplicação desenvolvida sob medida

Este artigo mostra como usar a Azure AD PowerShell para definir uma política de vida simbólica. O Azure AD Premium permite que os desenvolvedores de aplicações e administradores de inquilinos configurem a vida de tokens emitidos para clientes não confidenciais. As políticas de vida token são definidas numa base de inquilino ou os recursos que estão a ser acedidos.

Para definir uma política de vida útil simbólica, você precisa baixar o [Módulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
Executar o **comando Connect-AzureAD -Confirmar.**

Aqui está uma política de exemplo que exige que os utilizadores autentem mais frequentemente na sua aplicação web. Esta política define a vida útil dos tokens de acesso/ID e a idade máxima de um token de sessão multi-factor para o principal de serviço da sua aplicação web. Crie a apólice e atribua-a ao seu diretor de serviço. Também precisa de obter o ObjectId do seu principal de serviço.

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>Passos seguintes

* Consulte [as vidas de token configurantes em Azure AD](./active-directory-configurable-token-lifetimes.md) para aprender a configurar vidas simbólicas emitidas pela Azure AD, incluindo como definir vidas simbólicas para todas as aplicações da sua organização, para uma app multi-inquilino, ou para um diretor de serviço específico na sua organização. 
* [Referência simbólica Azure Ad](./id-tokens.md)