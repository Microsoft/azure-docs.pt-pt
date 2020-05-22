---
title: Atribuir um utilizador ou grupo a uma aplicação empresarial em Azure AD
description: Como selecionar uma aplicação empresarial para atribuir um utilizador ou grupo a ela no Diretório Ativo do Azure
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: b52bc45287e0e3a8f4908630cb6e57130c1725df
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772425"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Atribuir um utilizador ou grupo a uma aplicação empresarial no Azure Ative Directory

Este artigo mostra-lhe como atribuir utilizadores ou grupos a aplicações empresariais no Azure Ative Directory (Azure AD), quer a partir do portal Azure quer através da utilização do PowerShell. Ao atribuir um utilizador a uma aplicação, a aplicação aparece no painel de [acesso my apps](https://myapps.microsoft.com/) do utilizador para facilitar o acesso. Se a aplicação expor funções, também pode atribuir uma função específica ao utilizador.

Para um maior controlo, certos tipos de aplicações empresariais podem ser configurados para exigir a [atribuição do utilizador](#configure-an-application-to-require-user-assignment). 

Para [atribuir um utilizador ou grupo a uma aplicação](#assign-users-or-groups-to-an-app-via-the-azure-portal)empresarial, terá de se inscrever como administrador global, administrador de aplicações, administrador de aplicações na nuvem ou pelo proprietário designado da aplicação da empresa.

> [!NOTE]
> A atribuição baseada em grupo requer a edição Azure Ative Directory Premium P1 ou P2. A atribuição baseada em grupo é apoiada apenas para grupos de segurança. Os membros do grupo aninhado e os grupos do Office 365 não são atualmente apoiados. Para obter mais requisitos de licenciamento para as funcionalidades discutidas neste artigo, consulte a página de [preços do Diretório Ativo Azure](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Configure uma aplicação para exigir a atribuição do utilizador

Com os seguintes tipos de aplicações, tem a opção de exigir que os utilizadores sejam atribuídos à aplicação antes de poderem aceder à sua aplicação:

- Aplicações configuradas para inscrição única federada (SSO) com autenticação baseada em SAML
- Aplicações proxy de aplicação que usam pré-autenticação de diretório ativo Azure
- Aplicações construídas na plataforma de aplicação Azure AD que utilizam a Autenticação de Ligação OAuth 2.0 / OpenID depois de um utilizador ou administrador ter consentido com essa aplicação.

Quando a atribuição do utilizador for necessária, apenas os utilizadores que atribuir explicitamente à aplicação (seja através da atribuição direta do utilizador ou com base na adesão ao grupo) poderão iniciar sessão. Podem aceder à aplicação na sua página My Apps ou através de um link direto. 

Quando a atribuição *não*for necessária , quer porque definiu esta opção para **Nº** ou porque a aplicação utiliza outro modo SSO, qualquer utilizador poderá aceder à aplicação se tiver um link direto para a aplicação ou o URL de Acesso ao **Utilizador** na página **'Propriedades'** da aplicação. 

Esta definição não afeta se uma aplicação aparece ou não no painel de acesso das Minhas Apps. As aplicações aparecem nos painéis de acesso das Minhas Aplicações aos utilizadores uma vez que tenha atribuído um utilizador ou grupo à aplicação. Para segundo plano, consulte [Gerir o acesso a apps.](what-is-access-management.md)


Para exigir a atribuição do utilizador para uma aplicação:

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta de administrador ou como proprietário do pedido.

2. Selecione **Azure Active Directory**. No menu de navegação à esquerda, selecione **aplicações Enterprise**.

3. Selecione a aplicação da lista. Se não vir a aplicação, comece a digitar o seu nome na caixa de pesquisa. Ou utilize os controlos do filtro para selecionar o tipo de aplicação, o estado ou a visibilidade e, em seguida, selecione **Aplicar**.

4. No menu de navegação à esquerda, selecione **Propriedades**.

5. Certifique-se de que a atribuição do **Yes** **Utilizador necessária?**

   > [!NOTE]
   > Se a atribuição do **Utilizador for necessária?**

6. Selecione o botão **Guardar** na parte superior do ecrã.

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Atribuir utilizadores ou grupos a uma aplicação através do portal Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com) com um administrador global, administrador de aplicações ou conta de administrador de aplicações na nuvem, ou como proprietário designado da app da empresa.
2. Selecione **Azure Active Directory**. No menu de navegação à esquerda, selecione **aplicações Enterprise**.
3. Selecione a aplicação da lista. Se não vir a aplicação, comece a digitar o seu nome na caixa de pesquisa. Ou utilize os controlos do filtro para selecionar o tipo de aplicação, o estado ou a visibilidade e, em seguida, selecione **Aplicar**.
4. No menu de navegação à esquerda, selecione **Utilizadores e grupos.**
   > [!NOTE]
   > Se pretender atribuir utilizadores a aplicações da Microsoft, como aplicações do Office 365, algumas destas aplicações utilizam o PowerShell. 
5. Selecione o botão **de utilizador Adicionar.**
6. No painel **adicionar atribuição,** selecione **Utilizadores e grupos**.
7. Selecione o utilizador ou grupo que pretende atribuir à aplicação ou comece a digitar o nome do utilizador ou do grupo na caixa de pesquisa. Pode escolher vários utilizadores e grupos, e as suas seleções aparecerão em **itens Selecionados.**
8. Quando terminar, clique em **Selecionar**.

   ![Atribuir um utilizador ou grupo à aplicação](./media/assign-user-or-group-access-portal/assign-users.png)

9. No painel **utilizadores e grupos,** selecione um ou mais utilizadores ou grupos da lista e, em seguida, escolha o botão **Select** na parte inferior do painel.
10. Se a aplicação a suportar, pode atribuir uma função ao utilizador ou grupo. No painel **adicionar atribuição,** escolha **Função Select**. Em seguida, no painel **Select Role,** escolha uma função para aplicar aos utilizadores ou grupos selecionados e, em seguida, selecione **OK** na parte inferior do painel. 

    > [!NOTE]
    > Se a aplicação não apoiar a seleção de funções, a função de acesso padrão é atribuída. Neste caso, a aplicação gere o nível de acesso que os utilizadores têm.

2. No painel **adicionar atribuição,** selecione o botão **de atribuição** na parte inferior do painel.

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Atribuir utilizadores ou grupos a uma aplicação via PowerShell

1. Abra um pedido de comando Windows PowerShell elevado.

   > [!NOTE]
   > É necessário instalar o módulo AzureAD (utilize o `Install-Module -Name AzureAD` comando). Se solicitado a instalar um módulo NuGet ou o novo módulo PowerShell do Diretório Ativo Azure, escreva Y e prima ENTER.

1. Executar e iniciar sessão com uma conta de `Connect-AzureAD` utilizador global da Admin.
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

Para obter mais informações sobre como atribuir um utilizador a uma função de aplicação, consulte a documentação para [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0).

Para atribuir um grupo a uma aplicação empresarial, deve substituir `Get-AzureADUser` `Get-AzureADGroup` e substituir por `New-AzureADUserAppRoleAssignment` `New-AzureADGroupAppRoleAssignment` .

Para obter mais informações sobre como atribuir um grupo a uma função de aplicação, consulte a documentação para [New-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureadgroupapproleassignment?view=azureadps-2.0).

### <a name="example"></a>Exemplo

Este exemplo atribui a utilizadora Britta Simon à aplicação [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) utilizando o PowerShell.

1. No PowerShell, atribua os valores correspondentes às variáveis $username, $app_name e $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. Neste exemplo, não sabemos qual é o nome exato do papel de candidatura que queremos atribuir a Britta Simon. Executar os seguintes comandos para obter o utilizador ($user) e o diretor de serviço ($sp) utilizando o utilizador UPN e os principais nomes de exibição do serviço.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Executar o comando `$sp.AppRoles` para exibir as funções disponíveis para a aplicação Workplace Analytics. Neste exemplo, queremos atribuir a Britta Simon o papel de Analista (acesso limitado).

   ![Mostra as funções disponíveis para um utilizador que utiliza o Workplace Analytics Role](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Atribuir o nome do papel à `$app_role_name` variável.

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
- [Planeie uma implantação de um painel de acesso Azure AD](access-panel-deployment-plan.md)
- [Gerir o acesso a apps](what-is-access-management.md)
 
## <a name="next-steps"></a>Passos seguintes

- [Veja todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Remova uma atribuição de utilizador ou grupo de uma aplicação empresarial](remove-user-or-group-access-portal.md)
- [Desativar os sintetantes do utilizador para uma aplicação empresarial](disable-user-sign-in-portal.md)
- [Alterar o nome ou logotipo de uma aplicação empresarial](change-name-or-logo-portal.md)
