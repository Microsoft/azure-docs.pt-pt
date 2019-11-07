---
title: Obter visibilidade em todo o locatário para a central de segurança do Azure | Microsoft Docs
description: Saiba mais sobre como obter visibilidade em todo o locatário na central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: memildin
ms.openlocfilehash: 73b99b9ebcd18e1eb5b670d0809d1f0f6cbf8f9a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582917"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Obter visibilidade em todo o locatário para a central de segurança do Azure
Este artigo ajuda você a começar realizando várias ações que maximizam os benefícios fornecidos pela central de segurança do Azure. A execução dessas ações permite que você tenha visibilidade em todas as assinaturas do Azure vinculadas ao seu locatário Azure Active Directory e gerencie efetivamente a postura de segurança da sua organização em escala aplicando políticas de segurança em vários assinaturas de maneira agregada.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="management-groups"></a>Grupos de gestão
Os grupos de gerenciamento do Azure fornecem a capacidade de gerenciar com eficiência o acesso, as políticas e os relatórios de grupos de assinaturas, bem como gerenciar efetivamente todo o espaço do Azure executando ações no grupo de gerenciamento raiz. Cada locatário do Azure AD recebe um único grupo de gerenciamento de nível superior chamado grupo de gerenciamento raiz. Este grupo de gestão de raiz está incorporado na hierarquia para ter todos os grupos de gestão e subscrições associados ao mesmo. Esse grupo permite que políticas globais e atribuições de RBAC sejam aplicadas no nível do diretório. 

O grupo de gerenciamento raiz é criado automaticamente quando você realiza qualquer uma das seguintes ações: 
1. Opte por usar os grupos de gerenciamento do Azure navegando até **grupos de gerenciamento** no [portal do Azure](https://portal.azure.com).
2. Crie um grupo de gerenciamento por meio de uma chamada à API.
3. Crie um grupo de gerenciamento com o PowerShell.

Para obter uma visão geral detalhada dos grupos de gerenciamento, consulte o artigo [organizar seus recursos com grupos de gerenciamento do Azure](../azure-resource-manager/management-groups-overview.md) .

## <a name="create-a-management-group-in-the-azure-portal"></a>Criar um grupo de gerenciamento no portal do Azure
Você pode organizar as assinaturas em grupos de gerenciamento e aplicar suas políticas de governança aos grupos de gerenciamento. Todas as subscrições num grupo de gestão herdam automaticamente as políticas aplicadas ao grupo de gestão. Embora os grupos de gerenciamento não sejam necessários para integrar a central de segurança, é altamente recomendável que você crie pelo menos um grupo de gerenciamento para que o grupo de gerenciamento raiz seja criado. Depois que o grupo for criado, todas as assinaturas no seu locatário do Azure AD serão vinculadas a ele. Para obter instruções sobre o PowerShell e mais informações, consulte [criar grupos de gerenciamento para gerenciamento de recursos e da organização](../azure-resource-manager/management-groups-create.md).

 
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** > **grupos de gerenciamento**.
3. Na página principal, selecione **novo grupo de gerenciamento.** 

    ![Grupo principal](./media/security-center-management-groups/main.png) 
4.  Preencha o campo ID do grupo de gerenciamento. 
    - A **ID do grupo de gerenciamento** é o identificador exclusivo do diretório que é usado para enviar comandos nesse grupo de gerenciamento. Esse identificador não é editável após a criação, pois é usado em todo o sistema do Azure para identificar esse grupo. 
    - O campo nome de exibição é o nome que é exibido dentro do portal do Azure. Um nome de exibição separado é um campo opcional ao criar o grupo de gerenciamento e pode ser alterado a qualquer momento.  

      ![Criar](./media/security-center-management-groups/create_context_menu.png)  
5.  Selecione **Guardar**

### <a name="view-management-groups-in-the-azure-portal"></a>Exibir grupos de gerenciamento no portal do Azure
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Para exibir grupos de gerenciamento, selecione **todos os serviços** no menu principal do Azure.
3. Em **geral**, selecione **grupos de gerenciamento**.

    ![Criar um grupo de gestão](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Conceder visibilidade no nível do locatário e a capacidade de atribuir políticas

Para obter visibilidade da postura de segurança de todas as assinaturas registradas no locatário do Azure AD, é necessário atribuir uma função de RBAC com permissões de leitura suficientes no grupo de gerenciamento raiz.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Elevar o acesso para um administrador global no Azure Active Directory
Um administrador de locatários Azure Active Directory não tem acesso direto às assinaturas do Azure. No entanto, como um administrador de diretório, eles têm o direito de se elevar a uma função que tem acesso. Um administrador de locatário do Azure AD precisa se elevar ao administrador de acesso do usuário no nível do grupo de gerenciamento raiz para que ele possa atribuir funções de RBAC. Para obter instruções e informações adicionais do PowerShell, consulte [elevar o acesso para um administrador global no Azure Active Directory](../role-based-access-control/elevate-access-global-admin.md). 


1. Entre no [portal do Azure](https://portal.azure.com) ou no centro de [Administração do Azure Active Directory](https://aad.portal.azure.com).

2. Na lista de navegação, clique em **Azure Active Directory** e em **Propriedades**.

   ![Propriedades do Azure AD – captura de tela](./media/security-center-management-groups/aad-properties.png)

3. Em **Gerenciamento de acesso para recursos do Azure**, defina a opção para **Sim**.

   ![O administrador global pode gerenciar assinaturas do Azure e Grupos de Gerenciamento captura de tela](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Quando você define a opção para Sim, você recebe a função Administrador de acesso do usuário no RBAC do Azure no escopo raiz (/). Isso concede permissão para atribuir funções em todas as assinaturas do Azure e grupos de gerenciamento associados a este diretório do Azure AD. Essa opção só está disponível para usuários que recebem a função de administrador global no Azure AD.

   - Quando você define o switch como não, a função Administrador de acesso do usuário no RBAC do Azure é removida da sua conta de usuário. Você não pode mais atribuir funções em todas as assinaturas do Azure e grupos de gerenciamento associados a este diretório do Azure AD. Você pode exibir e gerenciar somente as assinaturas do Azure e os grupos de gerenciamento aos quais você recebeu acesso.

4. Clique em **salvar** para salvar sua configuração.

    - Essa configuração não é uma propriedade global e aplica-se somente ao usuário conectado no momento.

5. Execute as tarefas que você precisa fazer no acesso elevado. Quando terminar, defina o botão de volta como **não**.


### <a name="assign-rbac-roles-to-users"></a>Atribuir funções de RBAC a usuários
Para obter visibilidade de todas as assinaturas, os administradores de locatários precisam atribuir a função RBAC apropriada a todos os usuários que desejarem conceder visibilidade em todo o locatário, incluindo-as, no nível do grupo de gerenciamento raiz. As funções recomendadas para atribuir são o **administrador de segurança** ou o leitor de **segurança**. Em geral, a função Administrador de segurança é necessária para aplicar políticas no nível raiz, enquanto o leitor de segurança será suficiente para fornecer visibilidade no nível do locatário. Para obter mais informações sobre as permissões concedidas por essas funções, consulte a [Descrição da função interna do administrador de segurança](../role-based-access-control/built-in-roles.md#security-admin) ou a descrição da [função interna do leitor de segurança](../role-based-access-control/built-in-roles.md#security-reader).


#### <a name="assign-rbac-roles-to-users-through-the-azure-portal"></a>Atribua funções RBAC a usuários por meio do portal do Azure: 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
1. Para exibir grupos de gerenciamento, selecione **todos os serviços** no menu principal do Azure e, em seguida, selecione **grupos de gerenciamento**.
1.  Selecione um grupo de gerenciamento e clique em **detalhes**.

    ![Captura de tela detalhes do Grupos de Gerenciamento](./media/security-center-management-groups/management-group-details.PNG)
 
1. Clique em **controle de acesso (iam)** e em **atribuições de função**.

1. Clique em **Adicionar atribuição de função**.

1. Selecione a função a ser atribuída e o usuário e, em seguida, clique em **salvar**.  
   
   ![Captura de tela Adicionar função do leitor de segurança](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-rbac-roles-to-users-with-powershell"></a>Atribuir funções de RBAC a usuários com o PowerShell: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps).
2. Execute os seguintes comandos: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Quando solicitado, entre com as credenciais de administrador global. 

    ![Captura de tela do prompt de entrada](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Conceda permissões de função de leitor executando o seguinte comando:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Para remover a função, use o seguinte comando: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

### <a name="open-or-refresh-security-center"></a>Abrir ou atualizar a central de segurança
Depois de ter acesso elevado, abra ou atualize a central de segurança do Azure para verificar se você tem visibilidade em todas as assinaturas em seu locatário do Azure AD. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
2. Certifique-se de selecionar todas as assinaturas no seletor de assinatura que você deseja exibir na central de segurança.

    ![Captura de tela do seletor de assinatura](./media/security-center-management-groups/subscription-selector.png)

1. Selecione **todos os serviços** no menu principal do Azure e, em seguida, selecione **central de segurança**.
2. Na **visão geral**, há um gráfico de cobertura de assinatura.

    ![Captura de tela do gráfico de cobertura da assinatura](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Clique em **cobertura** para ver a lista de assinaturas cobertas. 

    ![Captura de tela da lista de cobertura de assinatura](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Remover acesso elevado 
Depois que as funções de RBAC tiverem sido atribuídas aos usuários, o administrador de locatários deverá se remover da função de administrador de acesso do usuário.

1. Entre no [portal do Azure](https://portal.azure.com) ou no centro de [Administração do Azure Active Directory](https://aad.portal.azure.com).

2. Na lista de navegação, clique em **Azure Active Directory** e em **Propriedades**.

3. Em **administrador global pode gerenciar assinaturas e grupos de gerenciamento do Azure**, defina a opção como **não**.

4. Clique em **salvar** para salvar sua configuração.



## <a name="adding-subscriptions-to-a-management-group"></a>Adicionando assinaturas a um grupo de gerenciamento
Você pode adicionar assinaturas ao grupo de gerenciamento que você criou. Essas etapas não são obrigatórias para obter visibilidade em todo o locatário e gerenciamento de acesso e política global.

1. Em **grupos de gerenciamento**, selecione um grupo de gerenciamento ao qual adicionar sua assinatura.

    ![Selecione um grupo de gerenciamento ao qual adicionar assinatura](./media/security-center-management-groups/management-group-subscriptions.png)

2. Selecione **Adicionar existente**.

    ![Adicionar existente](./media/security-center-management-groups/add-existing.png)

3. Insira assinatura em **Adicionar recurso existente** e clique em **salvar**.

4. Repita as etapas 1 a 3 até adicionar todas as assinaturas no escopo.

   > [!NOTE]
   > Os grupos de gerenciamento podem conter assinaturas e grupos de gerenciamento filho. Quando você atribui um usuário a uma função de RBAC ao grupo de gerenciamento pai, o acesso é herdado pelas assinaturas do grupo de gerenciamento filho. As políticas definidas no grupo de gerenciamento pai também são herdadas pelos filhos. 

## <a name="next-steps"></a>Passos seguintes
Neste artigo, você aprendeu a obter visibilidade de todo o locatário para a central de segurança do Azure. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

> [!div class="nextstepaction"]
> [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Gerenciar e responder a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md)

