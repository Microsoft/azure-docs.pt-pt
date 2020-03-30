---
title: Desativar os sintetários do utilizador para uma aplicação empresarial em Azure AD
description: Como desativar uma aplicação empresarial para que nenhum utilizador possa assinar no Diretório Ativo do Azure
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10553898376c4b9236ee62718fffccd45b12d70b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274096"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Desativar os insinos dos utilizadores para uma aplicação empresarial no Diretório Ativo do Azure

É fácil desativar uma aplicação empresarial para que nenhum utilizador possa iniciar sessão no Azure Ative Directory (Azure AD). Precisa das permissões adequadas para gerir a aplicação da empresa. E deve ser administrador global do diretório.

## <a name="how-do-i-disable-user-sign-ins"></a>Como desabilito os insinsos dos utilizadores?

1. Iniciar sessão no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
1. Selecione **Todos os serviços,** introduza o **Diretório Ativo Azure** na caixa de texto e, em seguida, selecione **Enter**.
1. No painel de -  ***diretório*** **ativo azure**(isto é, o painel Azure AD para o diretório que está a gerir), selecione **aplicações enterprise**.
1. Nas **aplicações da Enterprise - Todas as aplicações** painel, você vê uma lista das aplicações que você pode gerir. Selecione uma aplicação.
1. No painel de ***nome*** sinuoso (isto é, o painel com o nome da aplicação selecionada no título), selecione **Properties**.
1. No painel do nome de ***aplicação*** - **Properties,** selecione **No** for Enabled para os utilizadores iniciarem o **seu sessão?**
1. Selecione o comando **Guardar.**

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Use o Azure AD PowerShell para desativar uma aplicação não cotada

Se conhece o AppId de uma aplicação que não aparece na lista de aplicações da Enterprise (por exemplo, porque apagou a aplicação ou o diretor de serviço ainda não foi criado devido à aplicação ser pré-autorizada pela Microsoft), pode criar manualmente o principal de serviço para a app e depois desativá-la utilizando o [cmdlet Do PowerShell do AzureAD](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0).

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Passos seguintes

* [Veja todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](assign-user-or-group-access-portal.md)
* [Remova uma atribuição de utilizador ou grupo de uma aplicação empresarial](remove-user-or-group-access-portal.md)
* [Alterar o nome ou logotipo de uma aplicação empresarial](change-name-or-logo-portal.md)
