---
title: Ganhe visibilidade em todo o inquilino para o Centro de Segurança Azure [ Microsoft Docs
description: Este artigo explica como gerir a sua postura de segurança em escala aplicando políticas a todas as subscrições ligadas ao seu inquilino do Diretório Ativo Azure.
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
ms.openlocfilehash: 734876380d22f5d4d6dae0dd375b238fd5f6ffed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74559353"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Ganhe visibilidade em todo o inquilino para o Centro de Segurança Azure
Este artigo explica como gerir a postura de segurança da sua organização em escala aplicando políticas de segurança a todas as subscrições do Azure ligadas ao seu inquilino do Diretório Ativo Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="management-groups"></a>Grupos de gestão
Os grupos de gestão Azure fornecem a capacidade de gerir eficientemente o acesso, as políticas e os relatórios sobre grupos de subscrições, bem como gerir eficazmente toda a propriedade azure através da realização de ações sobre o grupo de gestão de raiz. A cada inquilino da AD Azure é dado um único grupo de gestão de alto nível chamado grupo de gestão de raiz. Este grupo de gestão de raiz está incorporado na hierarquia para ter todos os grupos de gestão e subscrições associados ao mesmo. Este grupo permite que políticas globais e atribuições RBAC sejam aplicadas ao nível do diretório. 

O grupo de gestão de raízes é criado automaticamente quando faz qualquer uma das seguintes ações: 
1. Opte por utilizar grupos de gestão Azure navegando para **Grupos** de Gestão no [portal Azure.](https://portal.azure.com)
2. Crie um grupo de gestão através de uma chamada da API.
3. Crie um grupo de gestão com a PowerShell.

Para uma visão detalhada dos grupos de gestão, consulte o artigo do Organize os seus recursos com o artigo dos grupos de [gestão Azure.](../azure-resource-manager/management-groups-overview.md)

## <a name="create-a-management-group-in-the-azure-portal"></a>Criar um grupo de gestão no portal Azure
Pode organizar subscrições em grupos de gestão e aplicar as suas políticas de governação aos grupos de gestão. Todas as subscrições num grupo de gestão herdam automaticamente as políticas aplicadas ao grupo de gestão. Embora os grupos de gestão não sejam obrigados a embarcar no Centro de Segurança, é altamente recomendado que crie pelo menos um grupo de gestão para que o grupo de gestão de raízes seja criado. Após a criação do grupo, todas as subscrições sob o seu inquilino Azure AD estarão ligadas a ele. Para obter instruções para o PowerShell e mais informações, consulte Criar grupos de [gestão para gestão](../azure-resource-manager/management-groups-create.md)de recursos e organização.

 
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **todos os grupos** > de gestão de**serviços**.
3. Na página principal, selecione **grupo New Management.** 

    ![Grupo Principal](./media/security-center-management-groups/main.png) 
4.  Preencha o campo de ID do grupo de gestão. 
    - O Id do **Grupo de Gestão** é o identificador único de diretório que é usado para enviar comandos neste grupo de gestão. Este identificador não é editável após a criação, uma vez que é usado em todo o sistema Azure para identificar este grupo. 
    - O campo de nome do visor é o nome que é exibido dentro do portal Azure. Um nome de exibição separado é um campo opcional ao criar o grupo de gestão e pode ser alterado a qualquer momento.  

      ![Criar](./media/security-center-management-groups/create_context_menu.png)  
5.  Selecione **Guardar**

### <a name="view-management-groups-in-the-azure-portal"></a>Ver grupos de gestão no portal Azure
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Para visualizar grupos de gestão, selecione **Todos os serviços** sob o menu principal do Azure.
3. Em **Geral,** selecione **Grupos de Gestão**.

    ![Criar um grupo de gestão](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Conceder visibilidade ao nível do arrendatário e a capacidade de atribuir políticas

Para obter visibilidade na postura de segurança de todas as subscrições registadas no inquilino da AD Azure, é necessário atribuir um papel RBAC com permissões de leitura suficientes para ser atribuído no grupo de gestão de raiz.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Elevar o acesso a um administrador global no Diretório Ativo do Azure
Um administrador de inquilinos do Azure Ative Directory não tem acesso direto às subscrições do Azure. No entanto, como administrador de diretórios, têm o direito de se elevara a um papel que tem acesso. Um administrador de inquilino sinuoso azure ad cede a administrador de acesso ao utilizador ao nível do grupo de gestão de raízes para que possam atribuir funções rBAC. Para instruções da PowerShell e informações adicionais, consulte [O acesso elevado a um administrador global no Diretório Ativo Azure](../role-based-access-control/elevate-access-global-admin.md). 


1. Inscreva-se no [portal Azure](https://portal.azure.com) ou no centro de [administração do Azure Ative Directory](https://aad.portal.azure.com).

2. Na lista de navegação, clique em **Azure Ative Directory** e, em seguida, clique em **Propriedades**.

   ![Propriedades Azure AD - screenshot](./media/security-center-management-groups/aad-properties.png)

3. Sob **gestão de acesso para recursos Azure,** deset a mudança para **Sim**.

   ![Administração global pode gerir Assinaturas Azure e Grupos de Gestão - screenshot](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Quando configura o interruptor para Sim, é-lhe atribuída a função de Administrador de Acesso ao Utilizador no Azure RBAC no âmbito raiz (/). Isto concede-lhe permissão para atribuir funções em todas as subscrições e grupos de gestão azure associados a este diretório Azure AD. Este switch só está disponível para utilizadores a quem seja atribuído o papel de Administrador Global em Azure AD.

   - Quando configura o interruptor para Nº, a função de Administrador de Acesso ao Utilizador no Azure RBAC é removida da sua conta de utilizador. Já não é possível atribuir funções em todas as subscrições e grupos de gestão do Azure que estão associados a este diretório Azure AD. Pode ver e gerir apenas as subscrições e grupos de gestão azure a que lhe foi concedido acesso.

4. Clique em **Guardar** para salvar a sua definição.

    - Esta configuração não é uma propriedade global e aplica-se apenas ao atualmente registado no utilizador.

5. Execute as tarefas que precisa de fazer no acesso elevado. Quando terminar, volte a ligar o interruptor para **Nº**.


### <a name="assign-rbac-roles-to-users"></a>Atribuir funções RBAC aos utilizadores
Para ganhar visibilidade a todas as subscrições, os administradores dos inquilinos precisam de atribuir o papel rBAC adequado a todos os utilizadores que desejem conceder visibilidade a nível de inquilinos, incluindo eles próprios, ao nível do grupo de gestão de raízes. As funções recomendadas para atribuir são **o Administrador de Segurança** ou o Leitor de **Segurança**. Geralmente, o papel de Administrador de Segurança é necessário para aplicar políticas no nível raiz, enquanto o Security Reader será suficiente para fornecer visibilidade ao nível dos inquilinos. Para obter mais informações sobre as permissões concedidas por estas funções, consulte a [descrição](../role-based-access-control/built-in-roles.md#security-admin) do papel incorporado do Administrador de Segurança ou a descrição do papel incorporado do Leitor de [Segurança.](../role-based-access-control/built-in-roles.md#security-reader)


#### <a name="assign-rbac-roles-to-users-through-the-azure-portal"></a>Atribuir funções RBAC aos utilizadores através do portal Azure: 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
1. Para visualizar grupos de gestão, selecione **Todos os serviços** sob o menu principal do Azure e, em seguida, selecione **Grupos de Gestão**.
1.  Selecione um grupo de gestão e clique **em detalhes**.

    ![Grupos de Gestão detalham screenshot](./media/security-center-management-groups/management-group-details.PNG)
 
1. Clique no controlo de **acesso (IAM)** e depois **nas atribuições**de role .

1. Clique em **Adicionar a atribuição de funções**.

1. Selecione a função para atribuir e o utilizador e, em seguida, clique em **Guardar**.  
   
   ![Adicionar imagem de papel do leitor de segurança](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-rbac-roles-to-users-with-powershell"></a>Atribuir funções RBAC aos utilizadores com PowerShell: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Instale [o Azure PowerShell](/powershell/azure/install-az-ps).
2. Execute os seguintes comandos: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Quando solicitado, inscreva-se com credenciais de administração globais. 

    ![Inscreva-se em screenshot de pronta](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Conceda permissões de papel do leitor executando o seguinte comando:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Para remover a função, utilize o seguinte comando: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

### <a name="open-or-refresh-security-center"></a>Open ou refresh Security Center
Uma vez que tenha acesso elevado, abra ou refresque o Azure Security Center para verificar se tem visibilidade em todas as subscrições sob o seu inquilino Azure AD. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
2. Certifique-se de que seleciona todas as subscrições do seletor de subscrição que gostaria de ver no Security Center.

    ![Screenshot do seletor de subscrição](./media/security-center-management-groups/subscription-selector.png)

1. **Selecione todos os serviços** sob o menu principal do Azure e, em seguida, selecione **Security Center**.
2. No **Overview,** há um gráfico de cobertura de subscrição.

    ![Screenshot do gráfico de cobertura de subscrição](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Clique na **Cobertura** para ver a lista de subscrições abrangidas. 

    ![Screenshot da lista de cobertura de subscrição](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Remover o acesso elevado 
Uma vez atribuídas as funções RBAC aos utilizadores, o administrador do arrendatário deve retirar-se da função de administrador de acesso ao utilizador.

1. Inscreva-se no [portal Azure](https://portal.azure.com) ou no centro de [administração do Azure Ative Directory](https://aad.portal.azure.com).

2. Na lista de navegação, clique em **Azure Ative Directory** e, em seguida, clique em **Propriedades**.

3. No âmbito **da Administração Global pode gerir as Assinaturas e Grupos de Gestão do Azure,** definir o interruptor para **Nº**.

4. Clique em **Guardar** para salvar a sua definição.



## <a name="adding-subscriptions-to-a-management-group"></a>Adicionar subscrições a um grupo de gestão
Pode adicionar subscrições ao grupo de gestão que criou. Estes passos não são obrigatórios para ganhar visibilidade a nível dos inquilinos e a gestão da política global e do acesso.

1. No âmbito **dos Grupos de Gestão,** selecione um grupo de gestão para adicionar a sua subscrição.

    ![Selecione um grupo de gestão para adicionar subscrição](./media/security-center-management-groups/management-group-subscriptions.png)

2. **Selecione Adicionar existente**.

    ![Adicionar existente](./media/security-center-management-groups/add-existing.png)

3. Introduza a subscrição em **adicionar recurso existente** e clique em **Guardar**.

4. Repita os passos 1 a 3 até adicionar todas as subscrições no âmbito.

   > [!NOTE]
   > Os grupos de gestão podem conter subscrições e grupos de gestão de crianças. Ao atribuir a um utilizador uma função RBAC ao grupo de gestão dos pais, o acesso é herdado pelas subscrições do grupo de gestão infantil. As políticas definidas no grupo de gestão dos pais também são herdadas pelas crianças. 

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a ganhar visibilidade em todo o inquilino para o Azure Security Center. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

> [!div class="nextstepaction"]
> [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md)

