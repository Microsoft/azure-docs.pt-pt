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
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: fc71ebe61fba8c1bdb6b7625b16a50d8995a581a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80883358"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Como alterar os incumprimentos de vida de símbolo para uma aplicação desenvolvida sob medida

Este artigo mostra como usar a Azure AD PowerShell para definir uma política de vida simbólica. O Azure AD Premium permite que os desenvolvedores de aplicações e administradores de inquilinos configurem a vida de tokens emitidos para clientes não confidenciais. As políticas de vida token são definidas numa base de inquilino ou os recursos que estão a ser acedidos.

1. Para definir uma política de vida útil simbólica, você precisa baixar o [Módulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Executar o **comando Connect-AzureAD -Confirmar.**

    Aqui está uma política de exemplo que define o fator único de idade máxima a refreshken. Criar a política:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Próximos passos

* Consulte [as vidas de token configurantes em Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) para aprender a configurar vidas simbólicas emitidas pela Azure AD, incluindo como definir vidas simbólicas para todas as aplicações da sua organização, para uma app multi-inquilino, ou para um diretor de serviço específico na sua organização. 
* [Referência simbólica Azure Ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
