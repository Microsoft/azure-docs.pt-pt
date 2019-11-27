---
title: Gerenciar usuários no Azure Blockchain Workbench
description: Como gerenciar usuários no Azure Blockchain Workbench.
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 573ec477a3e75beb91f90da0545fb7d4c0f9bf39
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324717"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Gerenciar usuários no Azure Blockchain Workbench

O Azure Blockchain Workbench inclui gerenciamento de usuários para pessoas e organizações que fazem parte do seu consórcio.

## <a name="prerequisites"></a>Pré-requisitos

Uma implantação do Blockchain Workbench é necessária. Consulte [implantação do Azure Blockchain Workbench](deploy.md) para obter detalhes sobre a implantação.

## <a name="add-azure-ad-users"></a>Adicionar usuários do Azure AD

O Azure Blockchain Workbench usa o Azure Active Directory (Azure AD) para autenticação, controle de acesso e funções. Os usuários no locatário do Azure AD do Blockchain Workbench podem autenticar e usar o Blockchain Workbench. Adicione usuários à função de aplicativo administrador para interagir e executar ações.

Os usuários do Blockchain Workbench precisam existir no locatário do Azure AD antes que você possa atribuí-los a aplicativos e funções. Para adicionar usuários ao Azure AD, use as seguintes etapas:

1.  Inicie sessão no [Portal do Azure](https://portal.azure.com).
2.  Selecione sua conta no canto superior direito e alterne para o locatário do Azure AD associado ao Blockchain Workbench.
3.  Selecione **Azure Active Directory > usuários**. Você verá uma lista de usuários em seu diretório.
4.  Para adicionar usuários ao diretório, selecione **novo usuário**. Para usuários externos, selecione **novo usuário convidado**.

    ![Novo usuário](./media/manage-users/add-ad-user.png)

5.  Preencha os campos obrigatórios para o novo usuário. Selecione **Criar**.

Visite a documentação [do Azure ad](../../active-directory/fundamentals/add-users-azure-active-directory.md) para obter mais detalhes sobre como gerenciar usuários no Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Gerenciar administradores do Blockchain Workbench

Depois que os usuários tiverem sido adicionados ao diretório, a próxima etapa será escolher quais usuários são administradores do Blockchain Workbench. Os usuários no grupo **administrador** são associados à **função de aplicativo administrador** no Blockchain Workbench. Os administradores podem adicionar ou remover usuários, atribuir usuários a cenários específicos e criar novos aplicativos.

Para adicionar usuários ao grupo de **Administradores** no diretório do AD do Azure:

1.  Inicie sessão no [Portal do Azure](https://portal.azure.com).
2.  Verifique se você está no locatário do Azure AD associado ao Blockchain Workbench selecionando sua conta no canto superior direito.
3.  Selecione **Azure Active Directory > aplicativos empresariais**.
4.  Selecione o aplicativo cliente do Azure AD para o Blockchain Workbench
    
    ![Todos os registros de aplicativos empresariais](./media/manage-users/select-blockchain-client-app.png)

5.  Selecione **usuários e grupos > Adicionar usuário**.
6.  Em **Adicionar atribuição**, selecione **usuários**. Escolha ou procure o usuário que você deseja adicionar como administrador. Clique em **selecionar** quando terminar de escolher.

    ![Adicionar atribuição](./media/manage-users/add-user-assignment.png)

9.  Verifique se a **função** está definida como **administrador**
10. Selecione **Atribuir**. Os usuários adicionados são exibidos na lista com a função de administrador atribuída.

    ![Usuários do aplicativo cliente do Blockchain](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Gerenciando membros do Blockchain Workbench

Use o aplicativo Blockchain Workbench para gerenciar usuários e organizações que fazem parte do seu consórcio. Você pode adicionar ou remover usuários de aplicativos e funções.

1. [Abra o Blockchain Workbench](deploy.md#blockchain-workbench-web-url) no seu navegador e entre como administrador.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Os membros são adicionados a cada aplicativo. Os membros podem ter uma ou mais funções de aplicativo para iniciar contratos ou executar ações.

2. Para gerenciar membros de um aplicativo, selecione um bloco de aplicativo no painel **aplicativos** .

    O número de membros associados ao aplicativo selecionado é refletido no bloco Membros.

    ![Selecionar aplicativo](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Adicionar membro ao aplicativo

1. Selecione o bloco membro para exibir uma lista dos membros atuais.
2. Selecione **adicionar membros**.

    ![Adicionar membros](./media/manage-users/application-add-members.png)

3. Procure o nome do usuário.  Somente usuários do Azure AD que existem no locatário do Blockchain Workbench são listados. Se o usuário não for encontrado, você precisará [Adicionar usuários do Azure ad](#add-azure-ad-users).

    ![Adicionar membros](./media/manage-users/find-user.png)

4. Selecione uma **função** na lista suspensa.

    ![Selecionar membros da função](./media/manage-users/application-select-role.png)

5. Selecione **Adicionar** para adicionar o membro com a função associada ao aplicativo.

#### <a name="remove-member-from-application"></a>Remover membro do aplicativo

1. Selecione o bloco membro para exibir uma lista dos membros atuais.
2. Para o usuário que você deseja remover, escolha **remover** na lista suspensa função.

    ![Remover membro](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Alterar ou adicionar função

1. Selecione o bloco membro para exibir uma lista dos membros atuais.
2. Para o usuário que você deseja alterar, clique na lista suspensa e selecione a nova função.

    ![Alterar função](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo de instruções, você aprendeu a gerenciar usuários para o Azure Blockchain Workbench. Para saber como criar um aplicativo blockchain, vá para o próximo artigo de instruções.

> [!div class="nextstepaction"]
> [Criar um aplicativo blockchain no Azure Blockchain Workbench](create-app.md)
