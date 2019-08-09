---
title: Atribuir um usuário ou grupo a um aplicativo empresarial no Azure Active Directory | Microsoft Docs
description: Como selecionar um aplicativo empresarial para atribuir um usuário ou grupo a ele em Azure Active Directory
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15f7e830079c224e9e15dd45d14c1741376f8762
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851703"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Atribuir um usuário ou grupo a um aplicativo empresarial no Azure Active Directory

Para atribuir um usuário ou grupo a um aplicativo empresarial, você deve ter as permissões apropriadas para gerenciar o aplicativo empresarial e deve ser administrador global para o diretório. Para aplicativos da Microsoft (como aplicativos do Office 365), use o PowerShell para atribuir usuários a um aplicativo empresarial.

> [!NOTE]
> Para obter os requisitos de licenciamento para os recursos discutidos neste artigo, consulte a [página de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="assign-a-user-to-an-app---portal"></a>Atribuir um usuário a um portal do aplicativo

1. Iniciar sessão no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
1. Selecione **todos os serviços**, insira Azure Active Directory na caixa de texto e, em seguida, selecione **Enter**.
1. Selecione **aplicativos empresariais**.
1. No painel **aplicativos empresariais – todos os aplicativos** , você vê uma lista dos aplicativos que você pode gerenciar. Selecione um aplicativo.
1. No painel ***AppName*** (ou seja, o painel com o nome do aplicativo selecionado no título), selecione **usuários & grupos**.
1. No painel ***AppName*** **– usuário e grupos** , selecione **Adicionar usuário**.
1. No painel **Adicionar atribuição** , selecione **usuários e grupos**.

   ![Atribuir um usuário ou grupo ao aplicativo](./media/assign-user-or-group-access-portal/assign-users.png)

1. No painel **usuários e grupos** , selecione um ou mais usuários ou grupos na lista e, em seguida, escolha o botão **selecionar** na parte inferior do painel.
1. No painel **Adicionar atribuição** , selecione **função**. Em seguida, no painel **selecionar função** , selecione uma função a ser aplicada aos usuários ou grupos selecionados e, em seguida, selecione **OK** na parte inferior do painel.
1. No painel **Adicionar atribuição** , selecione o botão **atribuir** na parte inferior do painel. Os usuários ou grupos atribuídos têm as permissões definidas pela função selecionada para este aplicativo empresarial.

## <a name="allow-all-users-to-access-an-app---portal"></a>Permitir que todos os usuários acessem um portal do aplicativo

1. Iniciar sessão no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
1. Selecione **todos os serviços**, insira Azure Active Directory na caixa de texto e, em seguida, selecione **Enter**.
1. Selecione **aplicativos empresariais**.
1. No painel **aplicativos empresariais** , selecione **todos os aplicativos**. Isso lista os aplicativos que você pode gerenciar.
1. No painel **aplicativos empresariais – todos os aplicativos** , selecione um aplicativo.
1. No painel ***AppName*** , selecione **Propriedades**.
1. No painel  ***AppName* -Properties** , defina a **atribuição de usuário necessária?** definindo como **não**.

A **atribuição de usuário é necessária?** opção:

- Se essa opção for definida como Sim, os usuários deverão primeiro ser atribuídos a esse aplicativo antes de poder acessá-lo.
- Se essa opção for definida como não, todos os usuários que navegarem para a URL de link profundo do aplicativo ou URL do aplicativo receberão acesso diretamente
- Não afeta se um aplicativo aparece ou não no painel de acesso do aplicativo. Para mostrar o aplicativo no painel de acesso, você precisa atribuir um usuário ou grupo apropriado ao aplicativo.
- Somente o funciona com os aplicativos de nuvem configurados para logon único do SAML, aplicativos de proxy de aplicativo que usam Azure Active Directory pré-autenticação ou aplicativos criados diretamente na plataforma de aplicativo do Azure AD que usam o OAuth 2,0/ Autenticação do OpenID Connect depois que um usuário ou administrador tiver consentido o aplicativo. Consulte [logon único para aplicativos](what-is-single-sign-on.md). Consulte [Configurar a maneira como os usuários finais concordam com um aplicativo](configure-user-consent.md).
- Essa opção não tem efeito quando um aplicativo é configurado para qualquer um dos outros modos de logon único.

## <a name="assign-a-user-to-an-app---powershell"></a>Atribuir um usuário a um aplicativo-PowerShell

1. Abra um prompt de comando elevado do Windows PowerShell.

   > [!NOTE]
   > Você precisa instalar o módulo AzureAD (use o comando `Install-Module -Name AzureAD`). Se for solicitado a instalar um módulo NuGet ou o novo módulo Azure Active Directory v2 do PowerShell, digite Y e pressione ENTER.

1. Execute `Connect-AzureAD` e entre com uma conta de usuário de administrador global.
1. Use o script a seguir para atribuir um usuário e uma função a um aplicativo:

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

Para obter mais informações sobre como atribuir um usuário a uma função de aplicativo, visite a documentação para [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Para atribuir um grupo a um aplicativo empresarial, você precisa substituir `Get-AzureADUser` por. `Get-AzureADGroup`

### <a name="example"></a>Exemplo

Este exemplo atribui o usuário Brenda Simon ao aplicativo [Microsoft Workplace Analytics usando o](https://products.office.com/business/workplace-analytics) PowerShell.

1. No PowerShell, atribua os valores correspondentes às variáveis $username, $app _name e $app _role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. Neste exemplo, não sabemos qual é o nome exato da função de aplicativo que desejamos atribuir ao Brenda Simon. Execute os comandos a seguir para obter o usuário ($user) e a entidade de serviço ($sp) usando o UPN do usuário e os nomes de exibição da entidade de serviço.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Execute o comando `$sp.AppRoles` para exibir as funções disponíveis para o aplicativo Workplace Analytics. Neste exemplo, queremos atribuir Brenda Simon à função de analista (acesso limitado).

   ![Mostra as funções disponíveis para um usuário usando a função de análise de local de trabalho](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Atribua o nome da função à `$app_role_name` variável.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Execute o seguinte comando para atribuir o usuário à função de aplicativo:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Passos Seguintes

- [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Remover uma atribuição de usuário ou grupo de um aplicativo empresarial](remove-user-or-group-access-portal.md)
- [Desabilitar entradas de usuário para um aplicativo empresarial](disable-user-sign-in-portal.md)
- [Alterar o nome ou o logotipo de um aplicativo empresarial](change-name-or-logo-portal.md)
