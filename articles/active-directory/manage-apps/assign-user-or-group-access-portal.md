---
title: Atribuir um utilizador ou grupo a uma aplicação empresarial em Azure AD
description: Como selecionar uma aplicação empresarial para atribuir um utilizador ou grupo a ela no Azure Ative Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeab9cbb68567b6af3b1381b8ec5e67e82461ec
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763742"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Atribuir um utilizador ou um grupo a uma aplicação empresarial no Azure Ative Directory

Este artigo mostra-lhe como atribuir utilizadores ou grupos a aplicações empresariais no Azure Ative Directory (Azure AD), quer dentro do portal Azure, quer através da utilização do PowerShell. Quando atribui um utilizador a uma aplicação, a aplicação aparece no painel de [acesso my apps](https://myapps.microsoft.com/) do utilizador para facilitar o acesso. Se a aplicação expor funções, também pode atribuir uma função específica ao utilizador.

Para um maior controlo, certos tipos de aplicações empresariais podem ser configuradas para exigir a [atribuição do utilizador](#configure-an-application-to-require-user-assignment). 

Para [atribuir um utilizador ou grupo a uma aplicação empresarial,](#assign-users-or-groups-to-an-app-via-the-azure-portal)terá de se inscrever como administrador global, administrador de aplicação, administrador de aplicações em nuvem ou o proprietário designado da aplicação da empresa.

> [!NOTE]
> A atribuição baseada em grupo requer a edição Azure Ative Directory Premium P1 ou P2. A atribuição baseada em grupo é apoiada apenas para grupos de segurança. Os membros do grupo Aninhado e os grupos do Office 365 não são atualmente apoiados. Para obter mais requisitos de licenciamento para as funcionalidades discutidas neste artigo, consulte a página de preços do [Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Configure uma aplicação para exigir a atribuição do utilizador

Com os seguintes tipos de aplicações, tem a opção de exigir que os utilizadores sejam designados para a aplicação antes de poderem aceder à aplicação:

- Aplicações configuradas para o sign-on único federado (SSO) com autenticação baseada em SAML
- Aplicações Proxy que usam pré-autenticação do Diretório Ativo Azure
- Aplicações construídas na plataforma de aplicação AD Azure que utilizam AAuth 2.0 / OpenID Connect Authentication depois de um utilizador ou administrador ter consentido com essa aplicação.

Quando for necessária a atribuição do utilizador, apenas os utilizadores que atribuísse explicitamente à aplicação (seja através da atribuição direta do utilizador ou com base na adesão ao grupo) poderão iniciar sessão. Podem aceder à aplicação na sua página My Apps ou através de um link direto. 

Quando a atribuição não for *necessária*, seja porque definiu esta opção para **Nº** ou porque a aplicação utiliza outro modo SSO, qualquer utilizador poderá aceder à aplicação se tiver uma ligação direta com a aplicação ou o URL de Acesso ao **Utilizador** na página **Propriedades** da aplicação. 

Esta definição não afeta se uma aplicação aparece ou não no painel de acesso das Minhas Apps. As aplicações aparecem nos painéis de acesso my apps dos utilizadores depois de ter atribuído um utilizador ou grupo à aplicação. Para obter antecedentes, consulte [Gerir o acesso às aplicações.](what-is-access-management.md)


Para exigir a atribuição do utilizador para uma aplicação:

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta de administrador ou como proprietário da aplicação.

2. Selecione **Azure Active Directory**. No menu de navegação à esquerda, selecione **aplicações Enterprise**.

3. Selecione a inscrição na lista. Se não vir a aplicação, comece a escrever o seu nome na caixa de pesquisa. Ou utilize os comandos do filtro para selecionar o tipo de aplicação, o estado ou a visibilidade e, em seguida, **selecione Apply**.

4. No menu de navegação à esquerda, selecione **Propriedades.**

5. Certifique-se de que a atribuição do **Yes** **Utilizador necessária?**

   > [!NOTE]
   > Se a atribuição do **Utilizador não** estiver disponível, pode utilizar o PowerShell para definir a propriedade appRoleAssignmentRequired no principal de serviço.

6. Selecione o botão **Guardar** na parte superior do ecrã.

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Atribuir utilizadores ou grupos a uma aplicação através do portal Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com) com um administrador global, administrador de aplicação ou conta de administrador de aplicação na nuvem, ou como o proprietário designado da app da empresa.
2. Selecione **Azure Active Directory**. No menu de navegação à esquerda, selecione **aplicações Enterprise**.
3. Selecione a inscrição na lista. Se não vir a aplicação, comece a escrever o seu nome na caixa de pesquisa. Ou utilize os comandos do filtro para selecionar o tipo de aplicação, o estado ou a visibilidade e, em seguida, **selecione Apply**.
4. No menu de navegação à esquerda, selecione **Utilizadores e grupos**.
   > [!NOTE]
   > Se pretender atribuir utilizadores a aplicações da Microsoft, como aplicações do Office 365, algumas destas aplicações utilizam o PowerShell. 
5. Selecione o botão **de utilizador Adicionar.**
6. No painel **de atribuição de** adicionar, selecione **Utilizadores e grupos**.
7. Selecione o utilizador ou grupo que pretende atribuir à aplicação, ou comece a digitar o nome do utilizador ou grupo na caixa de pesquisa. Pode escolher vários utilizadores e grupos e as suas seleções aparecerão em **itens selecionados.**
8. Quando terminar, clique **em Select**.

   ![Atribuir um utilizador ou grupo à aplicação](./media/assign-user-or-group-access-portal/assign-users.png)

9. No painel **de grupos de Utilizadores e grupos,** selecione um ou mais utilizadores ou grupos da lista e, em seguida, escolha o botão **Select** na parte inferior do painel.
10. Se a aplicação o suportar, pode atribuir uma função ao utilizador ou grupo. No painel **de atribuição de** adicionar, escolha **Função Selecionada.** Em seguida, no painel **'Escolha',** escolha uma função a aplicar aos utilizadores ou grupos selecionados e, em seguida, selecione **OK** na parte inferior do painel. 

    > [!NOTE]
    > Se a aplicação não apoiar a seleção de funções, a função de acesso predefinida é atribuída. Neste caso, a aplicação gere o nível de acesso que os utilizadores têm.

2. No painel **de atribuição de** adicionar, selecione o botão **Atribuir** na parte inferior do painel.

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Atribuir utilizadores ou grupos a uma aplicação via PowerShell

1. Abra uma solicitação elevada de comando Do Windows PowerShell.

   > [!NOTE]
   > É necessário instalar o módulo AzureAD (utilize o `Install-Module -Name AzureAD` comando). Se solicitado para instalar um módulo NuGet ou o novo módulo Azure Ative Directory V2 PowerShell, escreva Y e prima ENTER.

1. Corra `Connect-AzureAD` e inscreva-se com uma conta de utilizador da Global Admin.
1. Utilize o seguinte script para atribuir um utilizador e uma função a uma aplicação:

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

Para obter mais informações sobre como atribuir um utilizador a uma função de aplicação, consulte a documentação para [new-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0).

Para atribuir um grupo a uma aplicação de empresa, deve substituir `Get-AzureADUser` `Get-AzureADGroup` e substituir por `New-AzureADUserAppRoleAssignment` `New-AzureADGroupAppRoleAssignment` .

Para obter mais informações sobre como atribuir um grupo a uma função de candidatura, consulte a documentação para [new-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureadgroupapproleassignment?view=azureadps-2.0).

### <a name="example"></a>Exemplo

Este exemplo atribui o utilizador Britta Simon à aplicação [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) utilizando o PowerShell.

1. No PowerShell, atribua os valores correspondentes às variáveis $username, $app_name e $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. Neste exemplo, não sabemos qual é o nome exato do papel de candidatura que queremos atribuir a Britta Simon. Executar os seguintes comandos para obter o utilizador ($user) e o principal de serviço ($sp) utilizando o utilizador UPN e os nomes principais de exibição do serviço.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Executar o comando `$sp.AppRoles` para mostrar as funções disponíveis para a aplicação Workplace Analytics. Neste exemplo, queremos atribuir a Britta Simon o Papel de Analista (Acesso Limitado).

   ![Mostra as funções disponíveis para um utilizador que usa o Workplace Analytics Role](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Atribua o nome da função à `$app_role_name` variável.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Executar o seguinte comando para atribuir o utilizador à função da aplicação:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="related-articles"></a>Artigos relacionados

- [Saiba mais sobre o acesso do utilizador final às aplicações](end-user-experiences.md)
- [Planeie uma implementação de painel de acesso AD AZure](access-panel-deployment-plan.md)
- [Gerir o acesso a apps](what-is-access-management.md)
 
## <a name="next-steps"></a>Passos seguintes

- [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Remova uma atribuição de utilizador ou grupo de uma aplicação da empresa](remove-user-or-group-access-portal.md)
- [Desativar as ins ins ins do utilizador para uma aplicação da empresa](disable-user-sign-in-portal.md)
- [Alterar o nome ou logotipo de uma aplicação da empresa](change-name-or-logo-portal.md)
