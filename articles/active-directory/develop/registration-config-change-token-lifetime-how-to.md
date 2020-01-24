---
title: Alterar incumprimentos de vida simbólicos para aplicações personalizadas do Azure AD  Microsoft Docs
description: Como atualizar as políticas token Lifetime para a sua aplicação que está a desenvolver no Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 431f18b9babb52b5000d3bf4cca75a0f5e29bb93
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702815"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Como alterar os incumprimentos de vida simbólicos para uma aplicação desenvolvida sob medida

Este artigo mostra como usar o Azure AD PowerShell para definir uma política de vida simbólica. O Azure AD Premium permite que os desenvolvedores de aplicações e administradores de inquilinos configurem a vida útil dos tokens emitidos para clientes não confidenciais. As políticas de token vitalícia são definidas numa base de arrendamento ou os recursos a serem acedidos.

1. Para definir uma política simbólica de vida, precisa de descarregar o Módulo PowerShell Da [AD Azure](https://www.powershellgallery.com/packages/AzureADPreview).
1. Executar o comando **Connect-AzureAD -Confirmar.**

    Aqui está uma política de exemplo que define a idade máxima single fator refresh token. Criar a política: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Passos seguintes

* Consulte a [configurável vida útil em Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) para aprender a configurar vidas simbólicas emitidas pela Azure AD, incluindo como definir vidas simbólicas para todas as aplicações da sua organização, para uma aplicação multi-inquilino, ou para um diretor de serviço específico na sua organização. 
* [Referência simbólica azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
