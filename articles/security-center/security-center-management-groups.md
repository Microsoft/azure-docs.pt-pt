---
title: Ganhe visibilidade em todo o inquilino para o Azure Security Center Microsoft Docs
description: Este artigo explica como gerir a sua postura de segurança em escala aplicando políticas a todas as subscrições ligadas ao seu inquilino Azure Ative Directory.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: memildin
ms.openlocfilehash: 6bbc38d79f51ba4ffcc3795718d276a7e9c0bf03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91440783"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Ganhe visibilidade em todo o inquilino para o Azure Security Center
Este artigo explica como gerir a postura de segurança da sua organização em escala, aplicando políticas de segurança a todas as subscrições da Azure ligadas ao seu inquilino Azure Ative Directory.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="management-groups"></a>Grupos de gestão
Os grupos de gestão Azure proporcionam a capacidade de gerir eficientemente o acesso, políticas e relatórios sobre grupos de subscrições, bem como gerir eficazmente toda a propriedade da Azure através da realização de ações no grupo de gestão de raiz. Cada inquilino da AD Azure recebe um único grupo de gestão de alto nível chamado grupo de gestão de raiz. Este grupo de gestão de raiz está incorporado na hierarquia para ter todos os grupos de gestão e subscrições associados ao mesmo. Este grupo permite que políticas globais e atribuições de funções Azure sejam aplicadas ao nível do diretório. 

O grupo de gestão de raiz é criado automaticamente quando faz qualquer uma das seguintes ações: 
1. Opte por utilizar grupos de gestão Azure navegando para **grupos de gestão** no [portal Azure.](https://portal.azure.com)
2. Criar um grupo de gestão através de uma chamada API.
3. Criar um grupo de gestão com a PowerShell.

Para uma visão detalhada dos grupos de gestão, consulte o Artigo De Organizar os seus recursos com o artigo [dos grupos de gestão Azure.](../azure-resource-manager/management-groups-overview.md)

## <a name="create-a-management-group-in-the-azure-portal"></a>Criar um grupo de gestão no portal Azure
Pode organizar subscrições em grupos de gestão e aplicar as suas políticas de governação aos grupos de gestão. Todas as subscrições num grupo de gestão herdam automaticamente as políticas aplicadas ao grupo de gestão. Embora os grupos de gestão não sejam necessários para embarcar no Security Center, é altamente recomendado que crie pelo menos um grupo de gestão para que o grupo de gestão de raiz seja criado. Após a criação do grupo, todas as subscrições sob o seu inquilino AZure AD estarão ligadas a ele. Para obter instruções para o PowerShell e mais informações, consulte [Criar grupos de gestão para gestão de recursos e organizações.](../azure-resource-manager/management-groups-create.md)

 
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os**  >  **grupos de gestão de serviços**.
3. Na página principal, selecione **New Management group.** 

    ![Grupo Principal](./media/security-center-management-groups/main.png) 
4.  Preencha o campo de identificação do grupo de gestão. 
    - O **ID do Grupo de Gestão** é o identificador único do diretório que é usado para submeter comandos neste grupo de gestão. Este identificador não é editável após a criação, uma vez que é usado em todo o sistema Azure para identificar este grupo. 
    - O campo de nome de exibição é o nome que é apresentado dentro do portal Azure. Um nome de exibição separado é um campo opcional ao criar o grupo de gestão e pode ser alterado a qualquer momento.  

      ![Criar](./media/security-center-management-groups/create_context_menu.png)  
5.  Selecione **Guardar**

### <a name="view-management-groups-in-the-azure-portal"></a>Ver grupos de gestão no portal Azure
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Para ver grupos de gestão, selecione **Todos os serviços** no menu principal do Azure.
3. Sob **General**, selecione **Grupos de Gestão.**

    ![Criar um grupo de gestão](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Conceder visibilidade ao nível do inquilino e a capacidade de atribuir políticas

Para obter visibilidade na postura de segurança de todas as subscrições registadas no inquilino Azure AD, é necessário atribuir um papel Azure com permissões de leitura suficientes para ser atribuído no grupo de gestão de raiz.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Elevar o acesso a um administrador global no Azure Ative Directory
Um administrador de inquilino do Azure Ative Directory não tem acesso direto às assinaturas da Azure. No entanto, como administrador de diretório, têm o direito de se elevar a um papel que tem acesso. Um administrador inquilino da Azure AD precisa de se elevar ao administrador de acesso ao utilizador ao nível do grupo de gestão de raiz para que possam atribuir funções de Azure. Para obter instruções powerShell e informações adicionais, consulte [o acesso da Elevate a um administrador global no Diretório Ativo Azure](../role-based-access-control/elevate-access-global-admin.md). 


1. Inscreva-se no [portal Azure](https://portal.azure.com) ou no [Centro de Administração Azure Ative.](https://aad.portal.azure.com)

2. Na lista de navegação, clique em **Azure Ative Directory** e, em seguida, clique em **Propriedades**.

   ![Propriedades AD AZure - screenshot](./media/security-center-management-groups/aad-properties.png)

3. Sob **a gestão de acesso para recursos Azure,** altere o interruptor para **Sim**.

   ![Gestão de acesso para recursos Azure - screenshot](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Quando define o interruptor para Sim, é-lhe atribuída a função de Administrador de Acesso ao Utilizador em Azure RBAC no âmbito raiz (/). Isto permite-lhe atribuir funções em todas as subscrições e grupos de gestão Azure associados a este diretório AZure AD. Este switch só está disponível para utilizadores que lhe sejam atribuídas a função de Administrador Global em AZure AD.

   - Quando define o interruptor para Nº, a função de Administrador de Acesso ao Utilizador no RBAC Azure é removida da sua conta de utilizador. Já não é possível atribuir funções em todas as subscrições e grupos de gestão da Azure que estejam associados a este diretório AD Azure. Pode ver e gerir apenas as assinaturas e grupos de gestão Azure aos quais lhe foi concedido acesso.

4. Clique **em Guardar** para guardar a sua definição.

    - Esta definição não é uma propriedade global e aplica-se apenas ao utilizador atualmente registado.

5. Execute as tarefas necessárias para fazer no acesso elevado. Quando terminar, volte a ligar o interruptor para **nº.**


### <a name="assign-azure-roles-to-users"></a>Atribuir funções Azure aos utilizadores
Para ganhar visibilidade a todas as subscrições, os administradores de inquilinos precisam de atribuir o papel adequado do Azure a todos os utilizadores que desejem conceder visibilidade ao inquilina, incluindo a si mesmos, ao nível do grupo de gestão de raiz. As funções recomendadas a atribuir são **o Administrador de Segurança** ou o Leitor de **Segurança**. Geralmente, o papel de Administrador de Segurança é necessário para aplicar políticas ao nível da raiz, enquanto o Leitor de Segurança será suficiente para fornecer visibilidade ao nível do inquilino. Para obter mais informações sobre as permissões concedidas por estas funções, consulte a descrição da [função incorporada do Administrador de Segurança](../role-based-access-control/built-in-roles.md#security-admin) ou a descrição da [função incorporada](../role-based-access-control/built-in-roles.md#security-reader)do Leitor de Segurança .


#### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Atribuir funções Azure aos utilizadores através do portal Azure: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Para visualizar grupos de gestão, selecione **Todos os serviços** no menu principal do Azure e, em seguida, selecione **Grupos de Gestão**.
1.  Selecione um grupo de gestão e clique em **detalhes.**

    ![Grupos de Gestão detalham a imagem](./media/security-center-management-groups/management-group-details.PNG)
 
1. Clique no **controlo de acesso (IAM)** e depois **atribuições de funções**.

1. Clique **em Adicionar a atribuição de função**.

1. Selecione a função a atribuir e o utilizador, em seguida, clique em **Guardar**.  
   
   ![Adicionar imagem de função do Leitor de Segurança](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-azure-roles-to-users-with-powershell"></a>Atribuir funções Azure aos utilizadores com PowerShell: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Instalar [a Azure PowerShell](/powershell/azure/install-az-ps).
2. Execute os seguintes comandos: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Quando solicitado, inscreva-se com credenciais de administração global. 

    ![Iniciar s-screva na imagem de aviso](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Conceder permissões de função de leitor executando o seguinte comando:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Para remover a função, utilize o seguinte comando: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

### <a name="open-or-refresh-security-center"></a>Abrir ou atualizar Centro de Segurança
Uma vez elevado acesso, abra ou refresque o Centro de Segurança Azure para verificar se tem visibilidade em todas as subscrições sob o seu inquilino AZure AD. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Certifique-se de que seleciona todas as subscrições no seletor de subscrição que gostaria de ver no Centro de Segurança.

    ![Screenshot do seletor de assinatura](./media/security-center-management-groups/subscription-selector.png)

1. Selecione **Todos os serviços** no menu principal do Azure e, em seguida, selecione **Security Center**.
2. No **Panorama Geral,** há um gráfico de cobertura de subscrição.

    ![Screenshot do gráfico de cobertura de subscrição](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Clique em **Cobertura** para ver a lista de subscrições abrangidas. 

    ![Screenshot da lista de cobertura de subscrição](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Remover acesso elevado 
Uma vez atribuídas as funções Azure aos utilizadores, o administrador do arrendatário deve retirar-se da função de administrador de acesso ao utilizador.

1. Inscreva-se no [portal Azure](https://portal.azure.com) ou no [Centro de Administração Azure Ative.](https://aad.portal.azure.com)

2. Na lista de navegação, clique em **Azure Ative Directory** e, em seguida, clique em **Propriedades**.

3. Sob **a gestão do Access para recursos Azure,** altere o interruptor para **Nº**.

4. Clique **em Guardar** para guardar a sua definição.



## <a name="adding-subscriptions-to-a-management-group"></a>Adicionar subscrições a um grupo de gestão
Pode adicionar subscrições ao grupo de gestão que criou. Estes passos não são obrigatórios para ganhar visibilidade em todo o inquilino e política global e gestão de acessos.

1. Em **Grupos de Gestão,** selecione um grupo de gestão para adicionar a sua subscrição.

    ![Selecione um grupo de gestão para adicionar subscrição](./media/security-center-management-groups/management-group-subscriptions.png)

2. **Selecione Adicionar existente**.

    ![Adicionar a existência](./media/security-center-management-groups/add-existing.png)

3. Introduza a subscrição em **Adicionar o recurso existente** e clique em **Guardar**.

4. Repita os passos 1 a 3 até adicionar todas as subscrições no âmbito.

   > [!NOTE]
   > Os grupos de gestão podem conter assinaturas e grupos de gestão de crianças. Quando atribui um papel de Azure a um utilizador ao grupo de gestão dos pais, o acesso é herdado pelas assinaturas do grupo de gestão infantil. As políticas definidas no grupo de gestão dos pais também são herdadas pelas crianças. 

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a ganhar visibilidade para o Centro de Segurança Azure. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

> [!div class="nextstepaction"]
> [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md)
