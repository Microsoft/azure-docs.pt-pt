---
title: Remova as atribuições de utilizador ou grupo de uma aplicação em Azure AD
description: Como remover a atribuição de acesso de um utilizador ou grupo de uma aplicação empresarial em Diretório Ativo Azure
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
ms.openlocfilehash: edf918b57212cf2adfbffb358a0257d9dbea85e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275867"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Remova uma atribuição de utilizador ou grupo de uma aplicação empresarial no Diretório Ativo azure

É fácil remover um utilizador ou um grupo do acesso atribuído a uma das suas aplicações empresariais no Azure Ative Directory (Azure AD). Precisa das permissões adequadas para gerir a aplicação da empresa. E deve ser administrador global do diretório.

> [!NOTE]
> Para aplicações da Microsoft (como aplicações do Office 365), utilize o PowerShell para remover os utilizadores para uma aplicação empresarial.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Como remover uma atribuição de utilizador ou grupo a uma aplicação empresarial no portal Azure?

1. Iniciar sessão no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
1. Selecione **Todos os serviços,** introduza o **Diretório Ativo Azure** na caixa de texto e, em seguida, selecione **Enter**.
1. No **Azure Ative Directory - *directoryname* ** página de nome de diretório (isto é, a página Azure AD para o diretório que está a gerir), selecione **aplicações Enterprise**.
1. Na página de **aplicações da Enterprise - Todas as aplicações,** verá uma lista das aplicações que pode gerir. Selecione uma aplicação.
1. Na página de visão geral do nome do ***aplicativo*** (isto é, a página com o nome da aplicação selecionada no título), selecione **Utilizadores & Grupos**.
1. No ***nome*** do aplicativo **- Página de Atribuição de Grupo & utilizador,** selecione um dos mais utilizadores ou grupos e, em seguida, selecione o comando **Remover.** Confirme a sua decisão no momento.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Como remover uma atribuição de utilizador ou grupo a uma aplicação empresarial utilizando o PowerShell?

1. Abra um pedido de comando Windows PowerShell elevado.

   > [!NOTE]
   > É necessário instalar o módulo AzureAD `Install-Module -Name AzureAD`(utilize o comando). Se solicitado a instalar um módulo NuGet ou o novo módulo PowerShell do Diretório Ativo Azure, escreva Y e prima ENTER.

1. Executar `Connect-AzureAD` e iniciar sessão com uma conta de utilizador global da Admin.
1. Utilize o seguinte script para remover um utilizador e função de uma aplicação:

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

## <a name="next-steps"></a>Passos seguintes

- [Veja todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Atribuir um utilizador ou grupo a uma aplicação empresarial](assign-user-or-group-access-portal.md)
- [Desativar os sintetantes do utilizador para uma aplicação empresarial](disable-user-sign-in-portal.md)
- [Alterar o nome ou logotipo de uma aplicação empresarial](change-name-or-logo-portal.md)
