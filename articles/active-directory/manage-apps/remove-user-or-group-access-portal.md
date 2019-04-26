---
title: Remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial no Azure Active Directory | Documentos da Microsoft
description: Como remover a atribuição de acesso de um utilizador ou grupo de uma aplicação empresarial no Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97759ae992ebe38aa85e9b4724edeebb5285db4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60443085"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial no Azure Active Directory
É fácil remover um utilizador ou um grupo de acesso atribuído a uma das suas aplicações empresariais no Azure Active Directory (Azure AD). Tem as permissões adequadas para gerir a aplicação da empresa. E, tem de ser administrador global do diretório.

> [!NOTE]
> For Microsoft Applications (por exemplo, aplicações do Office 365), utilize o PowerShell para remover utilizadores de uma aplicação empresarial.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Como remover um utilizador ou a atribuição de grupo para uma aplicação empresarial no portal do Azure?
1. Iniciar sessão no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
1. Selecione **todos os serviços**, introduza **Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.
1. Sobre o **Azure Active Directory – *directoryname***  página (ou seja, página do Azure AD para o diretório que está gerenciando), selecione **aplicações empresariais**.
1. Sobre o **aplicações empresariais - todas as aplicações** página, verá uma lista de aplicações pode gerir. Selecione uma aplicação.
1. Sobre o ***appname*** página de descrição geral (ou seja, a página com o nome da aplicação selecionada no título), selecione **utilizadores e grupos**.
1. Sobre o ***appname*** **-atribuição de grupos de & utilizador** página, selecione uma das mais utilizadores ou grupos e, em seguida, selecione o **remover** comando. Confirme a sua decisão na linha de comandos.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Como posso remover um utilizador ou a atribuição de grupo para uma aplicação empresarial com o PowerShell?
1. Abra uma linha de comandos elevada do Windows PowerShell.

    >[!NOTE] 
    > Tem de instalar o módulo AzureAD (utilize o comando `Install-Module -Name AzureAD`). Se lhe for pedido para instalar um módulo de NuGet ou o novo módulo do Azure Active Directory V2 PowerShell, digite s e pressione ENTER.

1. Executar `Connect-AzureAD` e inicie sessão com uma conta de utilizador de Administrador Global.
1. Utilize o seguinte script para remover um utilizador e a função de um aplicativo:

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
- [Atribuir um utilizador ou grupo a uma aplicação empresarial](assign-user-or-group-access-portal.md)
- [Desativar utilizador-inícios de sessão de uma aplicação empresarial](disable-user-sign-in-portal.md)
- [Alterar o nome ou logótipo de uma aplicação empresarial](change-name-or-logo-portal.md)
