---
title: Obtenha visibilidade ao nível do inquilino para o Centro de segurança do Azure | Documentos da Microsoft
description: Saiba mais sobre a obtenção de visibilidade ao nível do inquilino no Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: rkarlin
ms.openlocfilehash: 7f09db1f37617519926955daf0c29c13993dbf80
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728459"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Obtenha visibilidade ao nível do inquilino para o Centro de segurança do Azure
Este artigo ajuda-o a começar a utilizar ao fazer várias ações que maximizar os benefícios de que Centro de segurança do Azure oferece. Realizar essas ações permite-lhe obter visibilidade em todas as subscrições do Azure que estejam ligadas ao seu inquilino do Azure Active Directory e eficaz de gerir a postura de segurança da sua organização em escala ao aplicar políticas de segurança em vários assinaturas de forma aggregative.

## <a name="management-groups"></a>Grupos de gestão
Grupos de gestão do Azure oferecem a capacidade de gerenciar com eficiência o acesso, políticas e relatórios sobre os grupos de subscrições, bem como gerir eficazmente o estado do Azure de todo o executar ações sobre o grupo de gestão de raiz. Cada inquilino do Azure AD é atribuído um grupo de gestão único de nível superior denominado o grupo de gestão de raiz. Este grupo de gestão de raiz está incorporado na hierarquia para ter todos os grupos de gestão e subscrições associados ao mesmo. Este grupo permite políticas globais e atribuições de RBAC a ser aplicado ao nível do diretório. 

O grupo de gestão de raiz é criado automaticamente quando o fizer qualquer uma das seguintes ações: 
1. Opte por utilizar grupos de gestão do Azure ao navegar até **grupos de gestão** no [portal do Azure](https://portal.azure.com).
2. Crie um grupo de gestão através de uma chamada à API.
3. Crie um grupo de gestão com o PowerShell.

Para obter uma descrição detalhada dos grupos de gestão, consulte a [organizar os recursos com grupos de gestão do Azure](../azure-resource-manager/management-groups-overview.md) artigo.

## <a name="create-a-management-group-in-the-azure-portal"></a>Criar um grupo de gestão no portal do Azure
Pode organizar subscrições para grupos de gestão e aplicar as políticas de governação para os grupos de gestão. Todas as subscrições num grupo de gestão herdam automaticamente as políticas aplicadas ao grupo de gestão. Enquanto os grupos de gestão não é obrigados a carregar o Centro de segurança, é altamente recomendado que crie, pelo menos, um grupo de gestão para que o grupo de gestão de raiz é criado. Depois de criar o grupo, todas as subscrições no inquilino do Azure AD serão ligadas à mesma. Para obter instruções do PowerShell e obter mais informações, consulte [criar grupos de gestão para a gestão de recursos e a organização](../azure-resource-manager/management-groups-create.md).

 
1. Inicie sessão no [portal do Azure](http://portal.azure.com).
2. Selecione **todos os serviços** > **grupos de gestão**.
3. Na página principal, selecione **grupo de gestão de novo.** 

    ![Grupo principal](./media/security-center-management-groups/main.png) 
4.  Preencha o campo de ID do grupo de gestão. 
    - O **ID do grupo de gestão** é o identificador exclusivo do diretório que é utilizado para submeter os comandos neste grupo de gestão. Este identificador não é editável após a criação pois ele é usado em todo o sistema do Azure para identificar este grupo. 
    - O campo de nome de exibição é o nome que é apresentado no portal do Azure. Um nome de apresentação separada é um campo opcional ao criar a gestão de grupo e pode ser alterado em qualquer altura.  

      ![Criar](./media/security-center-management-groups/create_context_menu.png)  
5.  Selecione **guardar**

### <a name="view-management-groups-in-the-azure-portal"></a>Ver grupos de gestão no portal do Azure
1. Inicie sessão para o [portal do Azure](http://portal.azure.com).
2. Para ver os grupos de gestão, selecione **todos os serviços** no menu principal do Azure.
3. Sob **gerais**, selecione **grupos de gestão**.

    ![Criar um grupo de gestão](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Concedem visibilidade de nível de inquilino e a capacidade de atribuir políticas

Para obter visibilidade sobre a postura de segurança de todas as subscrições registado no inquilino do Azure AD, uma função RBAC com permissões suficientes de leitura é necessária atribuir no grupo de gestão de raiz.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Elevar o acesso de administrador global no Azure Active Directory
Um administrador de inquilino do Azure Active Directory não tem acesso direto às subscrições do Azure. No entanto, como um administrador do diretório, eles têm direito a se elevarem a uma função que tenha acesso. Administrador de inquilino do Azure AD precisa elevar-se ao administrador de acesso de usuário no nível de grupo de gestão de raiz, para que podem atribuir funções RBAC. Para instruções do PowerShell e informações adicionais, consulte [elevar o acesso de Administrador Global no Azure Active Directory](../role-based-access-control/elevate-access-global-admin.md). 


1. Inicie sessão para o [portal do Azure](https://portal.azure.com) ou o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com).

2. Na lista de navegação, clique em **do Azure Active Directory** e, em seguida, clique em **propriedades**.

   ![Propriedades do AD do Azure - captura de ecrã](./media/security-center-management-groups/aad-properties.png)

3. Sob **Access management para recursos do Azure**, defina o comutador para **Sim**.

   ![Administrador global pode gerir as subscrições do Azure e grupos de gestão - captura de ecrã](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Quando definir a opção para Sim, são atribuídos a função de administrador de acesso de utilizador no RBAC do Azure no âmbito da raiz (/). Isso lhe concede permissão para atribuir funções em todas as subscrições do Azure e grupos de gestão associados a este diretório do Azure AD. Essa opção só está disponível para utilizadores que estão atribuídos a função de Administrador Global no Azure AD.

  - Ao definir a opção para não, a função de administrador de acesso de utilizador no Azure RBAC é removida da sua conta de utilizador. Já não pode atribuir funções em todas as subscrições do Azure e grupos de gestão que estão associados este diretório do Azure AD. Pode ver e gerir apenas as subscrições do Azure e os grupos de gestão ao qual lhe foi concedido acesso.

4. Clique em **guardar** para salvar sua configuração.

    - Esta definição não é uma propriedade global e aplica-se apenas ao utilizador atualmente com sessão iniciado.

5. Execute as tarefas que precisa fazer no acesso elevado. Quando tiver terminado, defina o comutador para **não**.


### <a name="assign-rbac-roles-to-users"></a>Atribua funções RBAC a utilizadores
Ganhar visibilidade para todas as subscrições, os administradores de inquilinos tem de atribuir a função RBAC adequada para qualquer usuário que desejam concedem visibilidade ao nível do inquilino, incluindo si próprios, ao nível do grupo de gestão raiz. As funções recomendadas para atribuir são **administrador de segurança** ou **leitor de segurança**. Em geral, a função de administrador de segurança é necessária para aplicar políticas ao nível da raiz, enquanto o leitor de segurança será suficiente para fornecer visibilidade de nível de inquilino. Para obter mais informações sobre as permissões concedidas por estas funções, consulte a [descrição da função incorporada de administrador de segurança](../role-based-access-control/built-in-roles.md#security-admin) ou o [descrição da função incorporada de leitor de segurança](../role-based-access-control/built-in-roles.md#security-reader).


#### <a name="assign-rbac-roles-to-users-through-the-azure-portal"></a>Atribua funções RBAC a utilizadores através do portal do Azure: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Para ver os grupos de gestão, selecione **todos os serviços** no menu principal do Azure, em seguida, selecione **grupos de gestão**.
1.  Selecione um grupo de gestão e clique em **detalhes**.

    ![Captura de ecrã de detalhes de grupos de gestão](./media/security-center-management-groups/management-group-details.PNG)
 
1. Clique em **controlo de acesso (IAM)** , em seguida, **atribuições de funções**.

1. Clique em **adicionar atribuição de função**.

1. Selecione a função para atribuir e o utilizador, em seguida, clique em **guardar**.  
   
   ![Adicionar captura de ecrã de função de leitor de segurança](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-rbac-roles-to-users-with-powershell"></a>Atribua funções RBAC a utilizadores com o PowerShell: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps).
2. Execute os seguintes comandos: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Quando lhe for pedido, inicie sessão com credenciais de administrador global. 

    ![Inicie sessão na captura de ecrã de linha de comandos](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Conceda permissões de função de leitor ao executar o seguinte comando:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Para remover a função, utilize o seguinte comando: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

### <a name="open-or-refresh-security-center"></a>Abrir ou atualizar o Centro de segurança
Uma vez que elevou o acesso, abra ou Atualize o Centro de segurança do Azure para verificar que tem visibilidade para todas as subscrições no inquilino do Azure AD. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Certificar-se de que seleciona todas as subscrições no Seletor de subscrição que gostaria de ver no Centro de segurança.

    ![Captura de ecrã de Seletor de subscrição](./media/security-center-management-groups/subscription-selector.png)

1. Selecione **todos os serviços** no menu principal do Azure, em seguida, selecione **Centro de segurança**.
2. Na **descrição geral**, está um gráfico de cobertura de subscrição.

    ![Captura de ecrã de gráfico de cobertura de subscrição](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Clique em **cobertura** para ver a lista de subscrições abordados. 

    ![Captura de ecrã de lista de cobertura de subscrição](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Remover acesso elevado 
Assim que foram atribuídas as funções do RBAC para os utilizadores, o administrador de inquilino deve remover o próprio da função de administrador de acesso de utilizador.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) ou o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com).

2. Na lista de navegação, clique em **do Azure Active Directory** e, em seguida, clique em **propriedades**.

3. Sob **Administrador Global pode gerir as subscrições do Azure e grupos de gestão**, defina o comutador para **não**.

4. Clique em **guardar** para salvar sua configuração.



## <a name="adding-subscriptions-to-a-management-groups"></a>Adicionar subscrições a grupos de gestão
Pode adicionar assinaturas para o grupo de gestão que criou. Estes passos não são obrigatórios para ganhar visibilidade ao nível do inquilino e a política global e a gestão de acesso.

1. Sob **grupos de gestão**, selecione um grupo de gestão para adicionar a subscrição.

    ![Selecione um grupo de gestão para adicionar subscrição ao](./media/security-center-management-groups/management-group-subscriptions.png)

2. Selecione **adicionar existente**.

    ![Adicionar existente](./media/security-center-management-groups/add-existing.png)

3. Introduza a subscrição sob **adicione o recurso existente** e clique em **guardar**.

4. Repita os passos 1 a 3 até ter adicionado todas as subscrições no âmbito.

 > [!NOTE]
 > Grupos de gestão podem conter subscrições e grupos de gestão de subordinados. Quando atribui um utilizador de uma função RBAC para o grupo de gestão principal, o acesso é herdado por subscrições do grupo de gestão de subordinados. As políticas definidas no grupo de gestão principal também são herdadas pelos filhos. 

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a ganhar visibilidade ao nível do inquilino para o Centro de segurança do Azure. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

> [!div class="nextstepaction"]
> [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md)

