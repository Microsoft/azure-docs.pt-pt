---
title: Como alterar a duração do token é predefinido para uma aplicação personalizada desenvolvida | Documentos da Microsoft
description: Como atualizar as políticas de duração do Token para a sua aplicação que está a desenvolver no Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: celested
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04abdedf5ac19be3d5a43e7502cbc97f8f5fee43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298042"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Como alterar as predefinições de duração do token para uma aplicação personalizada desenvolvida

Este artigo mostra como utilizar o Azure AD PowerShell para definir uma política de duração do token. O Azure AD Premium permite que os desenvolvedores de aplicativos e administradores de inquilinos para configurar a duração de tokens emitidos para clientes não-confidenciais. Políticas de duração do token são definidas numa base de ao nível do inquilino ou os recursos que está sendo acessados.

1. Para definir uma política de duração do token, tem de transferir os [módulo do Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Executar o **Connect-AzureAD-confirmar** comando.

    Eis um exemplo de política que define o token de atualização de único fator a idade máxima. Crie a política: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Passos Seguintes

* Ver [durações de token configuráveis no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) para aprender a configurar durações de token emitidas pelo Azure AD, incluindo como definir durações de token para todas as aplicações na sua organização, para uma aplicação multi-inquilino ou para um serviço específico principal da sua organização. 
* [Referência de tokens do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

