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
ms.openlocfilehash: 8de6a7aafdd402e4ee75862e69ac60af3af0e041
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88114936"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Como alterar os incumprimentos de vida de símbolo para uma aplicação desenvolvida sob medida

Este artigo mostra como usar a Azure AD PowerShell para definir uma política de vida simbólica. O Azure AD Premium permite que os desenvolvedores de aplicações e administradores de inquilinos configurem a vida de tokens emitidos para clientes não confidenciais. As políticas de vida token são definidas numa base de inquilino ou os recursos que estão a ser acedidos.

1. Para definir uma política de vida útil simbólica, você precisa baixar o [Módulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Executar o **comando Connect-AzureAD -Confirmar.**

    Aqui está uma política de exemplo que define o fator único de idade máxima a refreshken. Criar a política:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Passos seguintes

* Consulte [as vidas de token configurantes em Azure AD](./active-directory-configurable-token-lifetimes.md) para aprender a configurar vidas simbólicas emitidas pela Azure AD, incluindo como definir vidas simbólicas para todas as aplicações da sua organização, para uma app multi-inquilino, ou para um diretor de serviço específico na sua organização. 
* [Referência simbólica Azure Ad](./id-tokens.md)