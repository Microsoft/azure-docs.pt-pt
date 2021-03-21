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
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: d39f378171443f028ef6b549b120b22f2a3405c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99582946"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Como alterar os incumprimentos de vida de símbolo para uma aplicação desenvolvida sob medida

Este artigo mostra como usar a Azure AD PowerShell para definir uma política de vida simbólica de acesso. O Azure AD Premium permite que os desenvolvedores de aplicações e administradores de inquilinos configurem a vida de tokens emitidos para clientes não confidenciais. As políticas de vida token são definidas numa base de inquilino ou os recursos que estão a ser acedidos.

> [!IMPORTANT]
> Depois de maio de 2020, os inquilinos deixarão de poder configurar a renovação e a duração da sessão.  O Azure Ative Directory deixará de honrar a configuração de atualização e ficha de sessão existente em políticas após 30 de janeiro de 2021. Ainda pode configurar o acesso a vidas simbólicas após a depreciação. Para mais informações, leia [As vidas de símbolos configurais em Azure AD](./active-directory-configurable-token-lifetimes.md).
> Implementamos capacidades de [gestão de sessão de autenticação](../conditional-access/howto-conditional-access-session-lifetime.md)   no Acesso Condicionado AZURE AD. Pode utilizar esta nova funcionalidade para configurar as vidas de token de atualização, definindo o sinal na frequência.  

Para definir uma política de vida útil de acesso, descarregue o [Módulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
Executar o **comando Connect-AzureAD -Confirmar.**

Aqui está uma política de exemplo que exige que os utilizadores autentem mais frequentemente na sua aplicação web. Esta política define a vida útil do acesso ao principal serviço da sua aplicação web. Crie a apólice e atribua-a ao seu diretor de serviço. Também precisa de obter o ObjectId do seu principal de serviço.

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>Passos seguintes

* Consulte [as vidas de token configurantes em Azure AD](./active-directory-configurable-token-lifetimes.md) para aprender a configurar vidas simbólicas emitidas pela Azure AD, incluindo como definir vidas simbólicas para todas as aplicações da sua organização, para uma app multi-inquilino, ou para um diretor de serviço específico na sua organização. 
* [Referência simbólica Azure Ad](./id-tokens.md)
