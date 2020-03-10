---
title: Gerir utilizadores na bancada azure blockchain
description: Como gerir os utilizadores na bancada azure Blockchain.
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 573ec477a3e75beb91f90da0545fb7d4c0f9bf39
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381600"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Gerir utilizadores na bancada de trabalho Azure Blockchain

A Azure Blockchain Workbench inclui a gestão de utilizadores para pessoas e organizações que fazem parte do seu consórcio.

## <a name="prerequisites"></a>Pré-requisitos

É necessária uma implantação da bancada blockchain. Consulte a implementação da [bancada de trabalho Azure Blockchain](deploy.md) para obter detalhes sobre a implementação.

## <a name="add-azure-ad-users"></a>Adicionar utilizadores de Anúncios Azure

A bancada azure Blockchain workbench utiliza o Azure Ative Directory (Azure AD) para autenticação, controlo de acesso e funções. Os utilizadores do inquilino da Blockchain Workbench Azure AD podem autenticar e usar a bancada blockchain Workbench. Adicione os utilizadores à função de aplicação do Administrador para interagir e executar ações.

Os utilizadores da bancada blockchain precisam de existir no inquilino da AD Azure antes de poder atribuí-los a aplicações e funções. Para adicionar utilizadores ao Azure AD, utilize os seguintes passos:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
2.  Selecione a sua conta no canto superior direito e mude para o inquilino Azure AD associado à Bancada de Trabalho blockchain.
3.  Selecione **Diretório Ativo Azure > Utilizadores.** Vê uma lista de utilizadores no seu diretório.
4.  Para adicionar utilizadores ao diretório, selecione **Novo utilizador**. Para utilizadores externos, selecione **Novo utilizador convidado**.

    ![Novo utilizador](./media/manage-users/add-ad-user.png)

5.  Complete os campos necessários para o novo utilizador. Selecione **Criar**.

Visite a documentação [da AD Azure](../../active-directory/fundamentals/add-users-azure-active-directory.md) para obter mais detalhes sobre como gerir os utilizadores dentro do Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Gerir administradores da bancada de trabalho blockchain

Uma vez adicionados os utilizadores ao diretório, o próximo passo é escolher quais os utilizadores que são administradores da Bancada de Trabalho blockchain. Os utilizadores do grupo **Administrador** estão associados à função de **aplicação do Administrador** na Bancada de Trabalho blockchain. Os administradores podem adicionar ou remover os utilizadores, atribuir utilizadores a cenários específicos e criar novas aplicações.

Para adicionar utilizadores ao grupo **Administrador** no diretório Azure AD:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
2.  Verifique se está no inquilino da AD Azure associado à Bancada de Trabalho blockchain, selecionando a sua conta no canto superior direito.
3.  Selecione **aplicações azure Ative Directory > Enterprise.**
4.  Selecione a aplicação de cliente Azure AD para blockchain Workbench
    
    ![Todos os registos de candidaturas empresariais](./media/manage-users/select-blockchain-client-app.png)

5.  Selecione **Utilizadores e grupos > Adicionar utilizador**.
6.  No **Add Assignment,** selecione **Utilizadores**. Escolha ou procure o utilizador que pretende adicionar como administrador. Clique em **Selecionar** quando terminar a escolha.

    ![Adicionar atribuição](./media/manage-users/add-user-assignment.png)

9.  Verificar **a função** está definida para **administrador**
10. Selecione **Atribuir**. Os utilizadores adicionados são apresentados na lista com a função de administrador atribuída.

    ![Utilizadores de aplicativos de cliente blockchain](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Gerir membros da bancada de trabalho blockchain

Utilize a aplicação Blockchain Workbench para gerir utilizadores e organizações que fazem parte do seu consórcio. Pode adicionar ou remover utilizadores a aplicações e funções.

1. [Abra a bancada blockchain](deploy.md#blockchain-workbench-web-url) no seu navegador e inscreva-se como administrador.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Os membros são adicionados a cada pedido. Os membros podem ter uma ou mais funções de candidatura para iniciar contratos ou tomar medidas.

2. Para gerir os membros para uma aplicação, selecione um azulejo de aplicação no painel **de aplicações.**

    O número de membros associados à aplicação selecionada reflete-se no azulejo dos membros.

    ![Selecione aplicação](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Adicionar membro à aplicação

1. Selecione o azulejo do membro para apresentar uma lista dos membros atuais.
2. Selecione **Adicionar membros**.

    ![Adicionar membros](./media/manage-users/application-add-members.png)

3. Procure o nome do utilizador.  Apenas os utilizadores de Anúncios Azure que existem no inquilino da bancada blockchain estão listados. Se o utilizador não for encontrado, tem de [adicionar utilizadores de Anúncios Azure](#add-azure-ad-users).

    ![Adicionar membros](./media/manage-users/find-user.png)

4. Selecione um **Papel** a partir da entrega.

    ![Selecione membros de papéis](./media/manage-users/application-select-role.png)

5. Selecione **Adicionar** para adicionar o membro com a função associada à aplicação.

#### <a name="remove-member-from-application"></a>Remover membro da aplicação

1. Selecione o azulejo do membro para apresentar uma lista dos membros atuais.
2. Para o utilizador que pretende remover, escolha **Remover** a partir da função drop-down.

    ![Remover membro](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Alterar ou adicionar papel

1. Selecione o azulejo do membro para apresentar uma lista dos membros atuais.
2. Para o utilizador que pretende alterar, clique na gota-de-desconto e selecione a nova função.

    ![Alterar função](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo de como fazer, aprendeu a gerir os utilizadores para a Bancada de Trabalho Azure Blockchain. Para aprender a criar uma aplicação blockchain, continue para o próximo artigo como fazer.

> [!div class="nextstepaction"]
> [Criar uma aplicação blockchain na bancada azure blockchain](create-app.md)
