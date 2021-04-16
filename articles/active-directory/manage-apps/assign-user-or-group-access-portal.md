---
title: Gerir a atribuição de utilizadores para uma aplicação no Azure Active Directory
description: Saiba como atribuir e desatribuir utilizadores e grupos para uma aplicação que utilize o Azure Ative Directory para gestão de identidade.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: iangithinji
ms.reviewer: luleon
ms.openlocfilehash: 097bf55cc7e5372749240c19afb09ba374d437af
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377977"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Gerir a atribuição de utilizadores para uma aplicação no Azure Active Directory

Este artigo mostra-lhe como atribuir utilizadores, e grupos, a aplicações empresariais no Azure Ative Directory (Azure AD), quer dentro do portal Azure, quer através da utilização do PowerShell. Quando atribui um utilizador a uma aplicação, a aplicação aparece nas [Minhas Apps](https://myapps.microsoft.com/) do utilizador para facilitar o acesso. Se a aplicação expor funções, também pode atribuir uma função específica ao utilizador.

Para um maior controlo, certos tipos de aplicações empresariais podem ser configuradas para exigir a [atribuição do utilizador](#configure-an-application-to-require-user-assignment). 

> [!IMPORTANT]
> Quando atribui um grupo a uma aplicação, apenas os utilizadores do grupo terão acesso. A atribuição não se cascata para grupos aninhados.

> [!NOTE]
> A atribuição baseada em grupo requer a edição Azure Ative Directory Premium P1 ou P2. A atribuição baseada em grupo é apoiada apenas para grupos de segurança. Os membros do grupo Aninhado e os grupos Microsoft 365 não são suportados atualmente. Para obter mais requisitos de licenciamento para as funcionalidades discutidas neste artigo, consulte a página de preços do [Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Configure uma aplicação para exigir a atribuição do utilizador

Com os seguintes tipos de aplicações, tem a opção de exigir que os utilizadores sejam designados para a aplicação antes de poderem aceder à aplicação:

- Aplicações configuradas para o sign-on único federado (SSO) com autenticação baseada em SAML
- Aplicações Proxy que usam pré-autenticação do Diretório Ativo Azure
- Aplicações construídas na plataforma de aplicação AD Azure que utilizam AAuth 2.0 / OpenID Connect Authentication depois de um utilizador ou administrador ter consentido com essa aplicação.

Quando for necessária a atribuição do utilizador, apenas os utilizadores que atribuísse explicitamente à aplicação (seja através da atribuição direta do utilizador ou com base na adesão ao grupo) poderão iniciar sessão. Podem aceder à aplicação na sua página My Apps ou através de um link direto. 

Quando a atribuição não for *necessária*, seja porque definiu esta opção para **Nº** ou porque a aplicação utiliza outro modo SSO, qualquer utilizador poderá aceder à aplicação se tiver uma ligação direta com a aplicação ou o URL de Acesso ao **Utilizador** na página **Propriedades** da aplicação. 

Esta definição não afeta se uma aplicação aparece ou não nas Minhas Apps. As aplicações aparecem nos painéis de acesso my apps dos utilizadores depois de ter atribuído um utilizador ou grupo à aplicação. Para obter antecedentes, consulte [Gerir o acesso às aplicações.](what-is-access-management.md)

Para exigir a atribuição do utilizador para uma aplicação:
1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta de administrador ou como proprietário da aplicação.
2. Selecione **Azure Active Directory**. No menu de navegação à esquerda, selecione **aplicações Enterprise**.
3. Selecione a inscrição na lista. Se não vir a aplicação, comece a escrever o seu nome na caixa de pesquisa. Ou utilize os comandos do filtro para selecionar o tipo de aplicação, o estado ou a visibilidade e, em seguida, **selecione Apply**.
4. No menu de navegação à esquerda, selecione **Propriedades.**
5. Certifique-se de que a atribuição do  **Utilizador necessária?**
   > [!NOTE]
   > Se a atribuição do **Utilizador não** estiver disponível, pode utilizar o PowerShell para definir a propriedade appRoleAssignmentRequired no principal de serviço.
6. Selecione o botão **Guardar** na parte superior do ecrã.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Atribuir ou não assinar utilizadores e grupos para uma aplicação que utilize o portal Azure
Para aprender a atribuir, ou não assinar, um utilizador ou grupo utilizando o portal Azure, consulte a [Série Quickstart em Gestão de Aplicações](add-application-portal-assign-users.md).

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Atribuir ou não assinar utilizadores e grupos para uma aplicação que utilize a API do gráfico
Pode utilizar a API do Gráfico para atribuir ou não designar utilizadores, e grupos, para uma aplicação. Para saber mais, consulte [atribuições de funções da App.](/graph/api/resources/approleassignment)

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>Atribua utilizadores e grupos a uma aplicação que utilize o PowerShell
1. Abra uma solicitação elevada de comando Do Windows PowerShell.
   > [!NOTE]
   > É necessário instalar o módulo AzureAD (utilize o `Install-Module -Name AzureAD` comando). Se solicitado para instalar um módulo NuGet ou o novo módulo Azure Ative Directory V2 PowerShell, escreva Y e prima ENTER.
2. Corra `Connect-AzureAD` e inscreva-se com uma conta de utilizador da Global Admin.
3. Utilize o seguinte script para atribuir um utilizador e uma função a uma aplicação:

    ```powershell
    # Assign the values to the variables
    $username = "<Your user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```
Para obter mais informações sobre como atribuir um utilizador a uma função de aplicação, consulte a documentação para [new-AzureADUserAppRoleAssignment](/powershell/module/azuread/new-azureaduserapproleassignment).

Para atribuir um grupo a uma aplicação de empresa, deve substituir `Get-AzureADUser` `Get-AzureADGroup` e substituir por `New-AzureADUserAppRoleAssignment` `New-AzureADGroupAppRoleAssignment` .

Para obter mais informações sobre como atribuir um grupo a uma função de candidatura, consulte a documentação para [new-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment).

### <a name="example"></a>Exemplo

Este exemplo atribui o utilizador Britta Simon à aplicação [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) utilizando o PowerShell.

1. No PowerShell, atribua os valores correspondentes às variáveis $username, $app_name e $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```
2. Neste exemplo, não sabemos qual é o nome exato do papel de candidatura que queremos atribuir a Britta Simon. Executar os seguintes comandos para obter o utilizador ($user) e o principal de serviço ($sp) utilizando o utilizador UPN e os nomes principais de exibição do serviço.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
3. Executar o comando `$sp.AppRoles` para mostrar as funções disponíveis para a aplicação Workplace Analytics. Neste exemplo, queremos atribuir a Britta Simon o Papel de Analista (Acesso Limitado).
   ![Mostra as funções disponíveis para um utilizador que usa o Workplace Analytics Role](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. Atribua o nome da função à `$app_role_name` variável.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```
5. Executar o seguinte comando para atribuir o utilizador à função da aplicação:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>Unassign utilizadores e grupos, a partir de uma app usando PowerShell

1. Abra uma solicitação elevada de comando Do Windows PowerShell.
   > [!NOTE]
   > É necessário instalar o módulo AzureAD (utilize o `Install-Module -Name AzureAD` comando). Se solicitado para instalar um módulo NuGet ou o novo módulo Azure Ative Directory V2 PowerShell, escreva Y e prima ENTER.
2. Corra `Connect-AzureAD` e inscreva-se com uma conta de utilizador da Global Admin.
3. Utilize o seguinte script para remover um utilizador e uma função de uma aplicação:

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


## <a name="related-articles"></a>Artigos relacionados

- [Saiba mais sobre o acesso do utilizador final às aplicações](end-user-experiences.md)
- [Planeie uma implementação de Azure AD My Apps](my-apps-deployment-plan.md)
- [Gerir o acesso a aplicações](what-is-access-management.md)
 
## <a name="next-steps"></a>Passos seguintes

- [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Remova uma atribuição de utilizador ou grupo de uma aplicação da empresa]()
- [Desativar os inícios de sessão do utilizador para uma aplicação empresarial](disable-user-sign-in-portal.md)
- [Alterar o nome ou logotipo de uma aplicação da empresa](./add-application-portal-configure.md)
