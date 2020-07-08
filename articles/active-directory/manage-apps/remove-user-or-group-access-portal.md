---
title: Remova as atribuições de utilizador ou grupo de uma aplicação em Azure AD
description: Como remover a atribuição de acesso de um utilizador ou grupo de uma aplicação empresarial no Azure Ative Directory
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
ms.openlocfilehash: a09b3307403cb78da3bd22f3cc85bd75d7ebd858
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763062"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Remova uma atribuição de utilizador ou grupo de uma aplicação empresarial no Azure Ative Directory

É fácil remover um utilizador ou um grupo do acesso atribuído a uma das suas aplicações empresariais no Azure Ative Directory (Azure AD). Precisa das permissões adequadas para gerir a aplicação da empresa. E deve ser administrador global para o diretório.

> [!NOTE]
> Para aplicações da Microsoft (como aplicações do Office 365), utilize o PowerShell para remover os utilizadores para uma aplicação da empresa.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Como posso remover uma atribuição de utilizador ou grupo a uma aplicação empresarial no portal Azure?

1. Iniciar sessão no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
1. Selecione **Todos os serviços**, insira o **Diretório Ativo Azure** na caixa de texto e, em seguida, selecione **Enter**.
1. Na página **Azure Ative Directory - *directoryname* ** (isto é, a página AD AZure para o diretório que está a gerir), selecione **aplicações da Enterprise**.
1. Nas **aplicações Da Empresa - Todas as aplicações,** verá uma lista das aplicações que pode gerir. Selecione uma aplicação.
1. Na página geral do nome do ***app*** (isto é, a página com o nome da aplicação selecionada no título), selecione **Utilizadores & Grupos**.
1. No ***nome de aplicação*** **- Página de atribuição de grupo & utilizador,** selecione um dos mais utilizadores ou grupos e, em seguida, selecione o comando **Remover.** Confirme a sua decisão no momento.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Como posso remover uma atribuição de utilizador ou grupo a uma aplicação empresarial utilizando o PowerShell?

1. Abra uma solicitação elevada de comando Do Windows PowerShell.

   > [!NOTE]
   > É necessário instalar o módulo AzureAD (utilize o `Install-Module -Name AzureAD` comando). Se solicitado para instalar um módulo NuGet ou o novo módulo Azure Ative Directory V2 PowerShell, escreva Y e prima ENTER.

1. Corra `Connect-AzureAD` e inscreva-se com uma conta de utilizador da Global Admin.
1. Utilize o seguinte script para remover um utilizador e uma função de uma aplicação:

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

## <a name="next-steps"></a>Próximos passos

- [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Atribuir um utilizador ou grupo a uma aplicação empresarial](assign-user-or-group-access-portal.md)
- [Desativar as ins ins ins do utilizador para uma aplicação da empresa](disable-user-sign-in-portal.md)
- [Alterar o nome ou logotipo de uma aplicação da empresa](change-name-or-logo-portal.md)
