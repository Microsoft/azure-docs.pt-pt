---
title: Remover atribuições de usuário ou grupo de um aplicativo no Azure Active Directory | Microsoft Docs
description: Como remover a atribuição de acesso de um usuário ou grupo de um aplicativo empresarial no Azure Active Directory
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
ms.openlocfilehash: 380816283156969c47f45a9b47435688df91f4ca
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381041"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Remover uma atribuição de usuário ou grupo de um aplicativo empresarial no Azure Active Directory

É fácil remover um usuário ou um grupo do acesso atribuído a um de seus aplicativos empresariais no Azure Active Directory (AD do Azure). Você precisa das permissões apropriadas para gerenciar o aplicativo empresarial. E você deve ser administrador global para o diretório.

> [!NOTE]
> Para aplicativos da Microsoft (como aplicativos do Office 365), use o PowerShell para remover usuários de um aplicativo empresarial.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Como fazer remover uma atribuição de usuário ou grupo para um aplicativo empresarial na portal do Azure?

1. Iniciar sessão no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
1. Selecione **todos os serviços**, insira **Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.
1. Na página **Azure Active Directory- *DirectoryName***  (ou seja, a página do Azure ad para o diretório que você está gerenciando), selecione **aplicativos empresariais**.
1. Na página **aplicativos empresariais – todos os aplicativos** , você verá uma lista dos aplicativos que você pode gerenciar. Selecione um aplicativo.
1. Na página Visão geral do ***AppName*** (ou seja, a página com o nome do aplicativo selecionado no título), selecione **usuários & grupos**.
1. Na página de **atribuição de grupo de & de usuário** do ***AppName*** , selecione um dos mais usuários ou grupos e, em seguida, selecione o comando **remover** . Confirme sua decisão no prompt.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Como fazer remover uma atribuição de usuário ou grupo para um aplicativo empresarial usando o PowerShell?

1. Abra um prompt de comando elevado do Windows PowerShell.

   > [!NOTE]
   > Você precisa instalar o módulo AzureAD (use o comando `Install-Module -Name AzureAD`). Se for solicitado a instalar um módulo NuGet ou o novo módulo Azure Active Directory v2 do PowerShell, digite Y e pressione ENTER.

1. Execute `Connect-AzureAD` e entre com uma conta de usuário de administrador global.
1. Use o script a seguir para remover um usuário e uma função de um aplicativo:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```

## <a name="next-steps"></a>Passos Seguintes

- [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Atribuir um usuário ou grupo a um aplicativo empresarial](assign-user-or-group-access-portal.md)
- [Desabilitar entradas de usuário para um aplicativo empresarial](disable-user-sign-in-portal.md)
- [Alterar o nome ou o logotipo de um aplicativo empresarial](change-name-or-logo-portal.md)
