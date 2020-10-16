---
title: Desativar as insuposições do utilizador para uma aplicação da empresa em Azure AD
description: Como desativar uma aplicação da empresa para que nenhum utilizadores possa entrar no Azure Ative Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/12/2019
ms.author: kenwith
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c6fc9a4716f1427257b3bbf18e5fa653567e141
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763385"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Desativar as insuposições do utilizador para uma aplicação da empresa no Azure Ative Directory

É fácil desativar uma aplicação da empresa para que nenhum utilizadores possa inscrevê-la no Azure Ative Directory (Azure AD). Precisa das permissões adequadas para gerir a aplicação da empresa. E deve ser administrador global para o diretório.

## <a name="how-do-i-disable-user-sign-ins"></a>Como desativar as inscrições dos utilizadores?

1. Iniciar sessão no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
1. Selecione **Todos os serviços**, insira o **Diretório Ativo Azure** na caixa de texto e, em seguida, selecione **Enter**.
1. No painel de diretoria do **Azure Ative**  -   ***Directory*** (isto é, o painel AD AD Azure para o diretório que está a gerir), selecione **aplicações da Enterprise**.
1. Nas **aplicações Da Enterprise - Todas as aplicações** painel, você vê uma lista das aplicações que você pode gerir. Selecione uma aplicação.
1. No painel de ***nomes*** (isto é, o painel com o nome da aplicação selecionada no título), selecione **Properties**.
1. No painel de propriedades do nome de ***aplicação,***  -  **Properties** selecione **No** for **Enabled para os utilizadores iniciarem sação?**
1. Selecione o comando **Guardar.**

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Use a Azure AD PowerShell para desativar uma aplicação não cotada

Se conhece o AppId de uma aplicação que não aparece na lista de aplicações da Enterprise (por exemplo, porque apagou a aplicação ou o principal de serviço ainda não foi criado devido à pré-autorização da app pela Microsoft), pode criar manualmente o principal do serviço para a aplicação e depois desativá-la utilizando o [cmdlet AzureAD PowerShell](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0).

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

* [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](assign-user-or-group-access-portal.md)
* [Remova uma atribuição de utilizador ou grupo de uma aplicação da empresa](remove-user-or-group-access-portal.md)
* [Alterar o nome ou logotipo de uma aplicação da empresa](change-name-or-logo-portal.md)
