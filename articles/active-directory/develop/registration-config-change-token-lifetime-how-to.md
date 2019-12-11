---
title: Alterar padrões de tempo de vida do token para aplicativos personalizados do Azure AD | Microsoft Docs
description: Como atualizar as políticas de tempo de vida do token para seu aplicativo que você está desenvolvendo no Azure AD
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: a603be6d57581541c0868b9f48a0bf9997cadd71
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962836"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Como alterar os padrões de tempo de vida do token para um aplicativo de desenvolvimento personalizado

Este artigo mostra como usar o PowerShell do Azure AD para definir uma política de tempo de vida do token. Azure AD Premium permite que os desenvolvedores de aplicativos e administradores de locatário configurem o tempo de vida de tokens emitidos para clientes não confidenciais. As políticas de tempo de vida do token são definidas em todo o locatário ou os recursos que estão sendo acessados.

1. Para definir uma política de tempo de vida do token, você precisa baixar o [módulo do PowerShell do Azure ad](https://www.powershellgallery.com/packages/AzureADPreview).
1. Execute o comando **Connect-AzureAD-Confirm** .

    Aqui está um exemplo de política que define o token de atualização de fator único de idade máxima. Crie a política: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Passos seguintes

* Consulte [tempos de vida de token configuráveis no Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) para saber como configurar tempos de vida de token emitidos pelo Azure AD, incluindo como definir tempos de vida de token para todos os aplicativos em sua organização, para um aplicativo multilocatário ou para uma entidade de serviço específica em sua organização. 
* [Referência de token do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
