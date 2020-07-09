---
title: Gerir utilizadores em Azure Blockchain Workbench
description: Como gerir os utilizadores na Azure Blockchain Workbench.
ms.date: 05/09/2019
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 78841ca830ae56f6c07f796bdde85f6a6ba9c921
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212739"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Gerir utilizadores em Azure Blockchain Workbench

O Azure Blockchain Workbench inclui a gestão de utilizadores para pessoas e organizações que fazem parte do seu consórcio.

## <a name="prerequisites"></a>Pré-requisitos

É necessária uma implantação blockchain Workbench. Consulte [a implementação da bancada de trabalho Azure Blockchain](deploy.md) para obter detalhes sobre a implementação.

## <a name="add-azure-ad-users"></a>Adicionar utilizadores AD Azure

O Azure Blockchain Workbench utiliza o Azure Ative Directory (Azure AD) para autenticação, controlo de acesso e funções. Os utilizadores do inquilino blockchain Workbench Azure AD podem autenticar e utilizar a bancada blockchain Workbench. Adicione os utilizadores à função de aplicação do Administrador para interagir e executar ações.

Os utilizadores da Blockchain Workbench precisam de existir no inquilino Azure AD antes de os poder atribuir a aplicações e funções. Para adicionar utilizadores ao Azure AD, utilize os seguintes passos:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
2.  Selecione a sua conta no canto superior direito e mude para o inquilino AD AZure associado à Blockchain Workbench.
3.  Selecione **Azure Ative Directory > Utilizadores**. Vê uma lista de utilizadores no seu diretório.
4.  Para adicionar utilizadores ao diretório, selecione **Novo utilizador**. Para utilizadores externos, selecione **Novo utilizador convidado**.

    ![Novo utilizador](./media/manage-users/add-ad-user.png)

5.  Preencha os campos necessários para o novo utilizador. Selecione **Criar**.

Visite a documentação [da Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md) para obter mais detalhes sobre como gerir os utilizadores dentro do Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Gerir administradores da Blockchain Workbench

Uma vez adicionados aos utilizadores ao diretório, o próximo passo é escolher quais os utilizadores que são administradores da Blockchain Workbench. Os utilizadores do grupo **administrador** estão associados à **função de aplicação do Administrador** na Bancada Blockchain. Os administradores podem adicionar ou remover utilizadores, atribuir utilizadores a cenários específicos e criar novas aplicações.

Para adicionar utilizadores ao grupo **administrador** no diretório AD Azure:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
2.  Verifique se está no inquilino AZure AD associado à Blockchain Workbench selecionando a sua conta no canto superior direito.
3.  Selecione **Azure Ative Directory > Aplicações Enterprise**.
4.  Selecione a aplicação do cliente Azure AD para blockchain Workbench
    
    ![Todos os registos de candidaturas à empresa](./media/manage-users/select-blockchain-client-app.png)

5.  Selecione **Utilizadores e grupos > Adicionar utilizador**.
6.  Em **Adicionar Atribuição**, selecione **Utilizadores**. Escolha ou procure o utilizador que pretende adicionar como administrador. Clique **em Selecionar** quando terminar de escolher.

    ![Adicionar atribuição](./media/manage-users/add-user-assignment.png)

9.  Verificar **a função** está definida para **administrador**
10. Selecione **Atribuir**. Os utilizadores adicionados são apresentados na lista com a função de administrador atribuída.

    ![Utilizadores de aplicativos de cliente blockchain](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Gerir membros da Blockchain Workbench

Utilize a aplicação Blockchain Workbench para gerir utilizadores e organizações que fazem parte do seu consórcio. Pode adicionar ou remover utilizadores a aplicações e funções.

1. [Abra a bancada blockchain](deploy.md#blockchain-workbench-web-url) no seu navegador e inscreva-se como administrador.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Os membros são adicionados a cada pedido. Os membros podem ter uma ou mais funções de candidatura para iniciar contratos ou tomar medidas.

2. Para gerir os membros para uma candidatura, selecione um azulejo de aplicação no painel **de aplicações.**

    O número de membros associados à candidatura selecionada reflete-se no azulejo dos membros.

    ![Selecione a aplicação](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Adicionar membro à aplicação

1. Selecione o azulejo do membro para apresentar uma lista dos membros atuais.
2. Selecione **Adicionar os membros**.

    ![Adicionar membros](./media/manage-users/application-add-members.png)

3. Procure o nome do utilizador.  Apenas os utilizadores de AD Azure que existem no blockchain Workbench inquilino estão listados. Se o utilizador não for encontrado, tem de [adicionar utilizadores AD Azure](#add-azure-ad-users).

    ![Adicionar membros](./media/manage-users/find-user.png)

4. Selecione um **papel** a partir do drop-down.

    ![Selecione membros de funções](./media/manage-users/application-select-role.png)

5. **Selecione Adicionar** o membro com a função associada à aplicação.

#### <a name="remove-member-from-application"></a>Remover membro da aplicação

1. Selecione o azulejo do membro para apresentar uma lista dos membros atuais.
2. Para o utilizador que pretende remover, escolha **Remover** da entrega de funções.

    ![Remover membro](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Alterar ou adicionar papel

1. Selecione o azulejo do membro para apresentar uma lista dos membros atuais.
2. Para o utilizador que pretende alterar, clique no drop-down e selecione a nova função.

    ![Alterar função](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Próximos passos

Neste artigo de como fazer, aprendeu a gerir os utilizadores para a Azure Blockchain Workbench. Para aprender a criar uma aplicação blockchain, continue para o próximo artigo como fazer.

> [!div class="nextstepaction"]
> [Criar uma aplicação blockchain em Azure Blockchain Workbench](create-app.md)
