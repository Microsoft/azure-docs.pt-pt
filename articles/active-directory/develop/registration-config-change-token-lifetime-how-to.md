---
title: Alterar incumprimentos de vida simbólicos para aplicações personalizadas do Azure AD
description: Como atualizar as políticas token Lifetime para a sua aplicação que está a desenvolver no Azure AD
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80883358"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Como alterar os incumprimentos de vida simbólicos para uma aplicação desenvolvida sob medida

Este artigo mostra como usar o Azure AD PowerShell para definir uma política de vida simbólica. O Azure AD Premium permite que os desenvolvedores de aplicações e administradores de inquilinos configurem a vida útil dos tokens emitidos para clientes não confidenciais. As políticas de token vitalícia são definidas numa base de arrendamento ou os recursos a serem acedidos.

1. Para definir uma política simbólica de vida, precisa de descarregar o Módulo PowerShell Da [AD Azure](https://www.powershellgallery.com/packages/AzureADPreview).
1. Executar o comando **Connect-AzureAD -Confirmar.**

    Aqui está uma política de exemplo que define a idade máxima single fator refresh token. Criar a política:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Passos seguintes

* Consulte a [configurável vida útil em Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) para aprender a configurar vidas simbólicas emitidas pela Azure AD, incluindo como definir vidas simbólicas para todas as aplicações da sua organização, para uma aplicação multi-inquilino, ou para um diretor de serviço específico na sua organização. 
* [Referência simbólica azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
