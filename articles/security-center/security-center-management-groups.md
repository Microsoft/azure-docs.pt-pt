---
title: Organizar subscrições em grupos de gestão e atribuir funções aos utilizadores para o Azure Security Center
description: Saiba como organizar as suas subscrições Azure em grupos de gestão no Azure Security Center e atribuir funções aos utilizadores da sua organização
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 3508d508a19d6ce7fba4f3ef3a4fa545a58a167d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099391"
---
# <a name="organize-subscriptions-into-management-groups-and-assign-roles-to-users"></a>Organizar subscrições em grupos de gestão e atribuir funções aos utilizadores

Este artigo explica como gerir a postura de segurança da sua organização em escala, aplicando políticas de segurança a todas as subscrições da Azure ligadas ao seu inquilino Azure Ative Directory.

Para obter visibilidade na postura de segurança de todas as subscrições registadas no inquilino Azure AD, é necessário atribuir um papel Azure com permissões de leitura suficientes para ser atribuído no grupo de gestão de raiz.


## <a name="organize-your-subscriptions-into-management-groups"></a>Organize as suas assinaturas em grupos de gestão

### <a name="introduction-to-management-groups"></a>Introdução aos grupos de gestão

Os grupos de gestão Azure proporcionam a capacidade de gerir eficientemente o acesso, políticas e relatórios sobre grupos de subscrições, bem como gerir eficazmente toda a propriedade da Azure através da realização de ações no grupo de gestão de raiz. Pode organizar subscrições em grupos de gestão e aplicar as suas políticas de governação aos grupos de gestão. Todas as subscrições num grupo de gestão herdam automaticamente as políticas aplicadas ao grupo de gestão. 

Cada inquilino AZURE AD recebe um único grupo de gestão de alto nível chamado grupo de **gestão de raiz.** Este grupo de gestão de raiz está incorporado na hierarquia para ter todos os grupos de gestão e subscrições associados ao mesmo. Este grupo permite que políticas globais e atribuições de funções Azure sejam aplicadas ao nível do diretório. 

O grupo de gestão de raiz é criado automaticamente quando faz qualquer uma das seguintes ações: 
- **Grupos de Gestão** Aberta no [portal Azure](https://portal.azure.com).
- Criar um grupo de gestão com uma chamada de API.
- Criar um grupo de gestão com a PowerShell. Para obter instruções powerShell, consulte [Criar grupos de gestão para gestão de recursos e organizações.](../governance/management-groups/create-management-group-portal.md)

Os grupos de gestão não são obrigados a embarcar no Security Center, mas recomendamos que crie pelo menos um para que o grupo de gestão de raiz seja criado. Após a criação do grupo, todas as subscrições sob o seu inquilino AZure AD estarão ligadas a ele. 


Para uma visão detalhada dos grupos de gestão, consulte o Artigo De Organizar os seus recursos com o artigo [dos grupos de gestão Azure.](../governance/management-groups/overview.md)

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Ver e criar grupos de gestão no portal Azure

1. A partir do [portal Azure,](https://portal.azure.com)utilize a caixa de pesquisa na barra superior para encontrar e abrir **Grupos de Gestão.**

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="Aceder aos seus grupos de gestão":::

    A lista dos seus grupos de gestão aparece.

1. Para criar um grupo de gestão, **selecione Adicionar grupo de gestão,** insira os detalhes relevantes e selecione **Save**.

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="Adicionar um grupo de gestão ao Azure":::

    - O **ID do Grupo de Gestão** é o identificador único do diretório que é usado para submeter comandos neste grupo de gestão. Este identificador não é editável após a criação, uma vez que é usado em todo o sistema Azure para identificar este grupo. 
    - O campo de nome de exibição é o nome que é apresentado dentro do portal Azure. Um nome de exibição separado é um campo opcional ao criar o grupo de gestão e pode ser alterado a qualquer momento.  


### <a name="add-subscriptions-to-a-management-group"></a>Adicionar subscrições a um grupo de gestão
Pode adicionar subscrições ao grupo de gestão que criou.

1. Em **Grupos de Gestão,** selecione o grupo de gestão para a sua subscrição.

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="Selecione um grupo de gestão para a sua subscrição":::

1. Quando a página do grupo abrir, selecione **Detalhes**

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="Abertura da página de detalhes de um grupo de gestão":::

1. Na página de detalhes do grupo, selecione **Adicionar subscrição,** em seguida, selecione as suas subscrições e selecione **Guardar**. Repita até adicionar todas as subscrições no âmbito.

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="Adicionar uma subscrição a um grupo de gestão":::
   > [!IMPORTANT]
   > Os grupos de gestão podem conter assinaturas e grupos de gestão de crianças. Quando atribui um papel de Azure a um utilizador ao grupo de gestão dos pais, o acesso é herdado pelas assinaturas do grupo de gestão infantil. As políticas definidas no grupo de gestão dos pais também são herdadas pelas crianças. 



## <a name="assign-azure-roles-to-other-users"></a>Atribuir funções Azure a outros utilizadores

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Atribuir funções Azure aos utilizadores através do portal Azure: 
1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Para visualizar grupos de gestão, selecione **Todos os serviços** no menu principal do Azure e, em seguida, selecione **Grupos de Gestão**.
1.  Selecione um grupo de gestão e selecione **detalhes.**

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="Grupos de Gestão detalham a imagem":::

1. Selecione **o controlo de acesso (IAM)** e depois **atribuições de funções**.
1. Selecione **Adicionar atribuição de função**.
1. Selecione a função a atribuir e o utilizador e, em seguida, **selecione Guardar**.  
   
   ![Adicionar imagem de função do Leitor de Segurança](./media/security-center-management-groups/asc-security-reader.png)

### <a name="assign-azure-roles-to-users-with-powershell"></a>Atribuir funções Azure aos utilizadores com PowerShell: 
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

## <a name="remove-elevated-access"></a>Remover acesso elevado 

Uma vez atribuídas as funções Azure aos utilizadores, o administrador do arrendatário deve retirar-se da função de administrador de acesso ao utilizador.

1. Inscreva-se no [portal Azure](https://portal.azure.com) ou no [Centro de Administração Azure Ative.](https://aad.portal.azure.com)

2. Na lista de navegação, selecione **Azure Ative Directory** e, em seguida, selecione **Propriedades**.

3. Sob **a gestão do Access para recursos Azure,** altere o interruptor para **Nº**.

4. Para guardar a sua definição, **selecione Guardar**.



## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a organizar subscrições em grupos de gestão e a atribuir funções aos utilizadores. Para obter informações relacionadas, consulte:

- [Permissions in Azure Security Center](security-center-permissions.md) (Permissões no Centro de Segurança do Azure)