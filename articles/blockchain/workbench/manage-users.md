---
title: Gerir utilizadores em Azure Blockchain Workbench
description: Como gerir os utilizadores na Azure Blockchain Workbench.
ms.date: 07/15/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: be078e7149ba008ab8b7ad44a016a0d1cf82df0e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91263016"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Gerir utilizadores em Azure Blockchain Workbench

O Azure Blockchain Workbench inclui a gestão de utilizadores para pessoas e organizações que fazem parte do seu consórcio.

## <a name="prerequisites"></a>Pré-requisitos

É necessária uma implantação blockchain Workbench. Consulte [a implementação da bancada de trabalho Azure Blockchain](deploy.md) para obter detalhes sobre a implementação.

## <a name="add-azure-ad-users"></a>Adicionar utilizadores AD Azure

O Azure Blockchain Workbench utiliza o Azure Ative Directory (Azure AD) para autenticação, controlo de acesso e funções. Os utilizadores do inquilino blockchain Workbench Azure AD podem autenticar e utilizar a bancada blockchain Workbench. Adicione os utilizadores à função de aplicação do Administrador para interagir e executar ações.

Os utilizadores da Blockchain Workbench precisam de existir no inquilino Azure AD antes de os poder atribuir a aplicações e funções. Para adicionar utilizadores ao Azure AD, utilize os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione a sua conta no canto superior direito e mude para o inquilino AD AZure associado à Blockchain Workbench.
1. Selecione **Azure Ative Directory > Utilizadores**. Vê uma lista de utilizadores no seu diretório.
1. Para adicionar utilizadores ao diretório, selecione **Novo utilizador**. Para utilizadores externos, selecione **Novo utilizador convidado**.
1. Preencha os campos necessários para o novo utilizador. Selecione **Criar**.

Visite a documentação [da Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md) para obter mais detalhes sobre como gerir os utilizadores dentro do Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Gerir administradores da Blockchain Workbench

Uma vez adicionados aos utilizadores ao diretório, o próximo passo é escolher quais os utilizadores que são administradores da Blockchain Workbench. Os utilizadores do grupo **administrador** estão associados à **função de aplicação do Administrador** na Bancada Blockchain. Os administradores podem adicionar ou remover utilizadores, atribuir utilizadores a cenários específicos e criar novas aplicações.

Para adicionar utilizadores ao grupo **administrador** no diretório AD Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Verifique se está no inquilino AZure AD associado à Blockchain Workbench selecionando a sua conta no canto superior direito.
1. Selecione **Azure Ative Directory > Aplicações Enterprise**.
1. Alterar O filtro drop-down **do tipo aplicação** para **todas as aplicações** e selecione **Aplicar**.
1. Selecione a aplicação do cliente Azure AD para a Azure Blockchain Workbench

    ![Todos os registos de candidaturas à empresa](./media/manage-users/select-blockchain-client-app.png)

1. Selecione **Utilizadores e grupos > Adicionar utilizador**.
1. Em **Adicionar Atribuição**, selecione **Utilizadores**. Escolha ou procure o utilizador que pretende adicionar como administrador. Clique **em Selecionar** quando terminar de escolher.

    ![Adicionar atribuição](./media/manage-users/add-user-assignment.png)

1. Verificar **a função** está definida para **administrador**
1. Selecione **Atribuir**. Os utilizadores adicionados são apresentados na lista com a função de administrador atribuída.

    ![Utilizadores de aplicativos de cliente blockchain](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Gerir membros da Blockchain Workbench

Utilize a aplicação Blockchain Workbench para gerir utilizadores e organizações que fazem parte do seu consórcio. Pode adicionar ou remover utilizadores a aplicações e funções.

1. [Abra a bancada blockchain](deploy.md#blockchain-workbench-web-url) no seu navegador e inscreva-se como administrador.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Os membros são adicionados a cada pedido. Os membros podem ter uma ou mais funções de candidatura para iniciar contratos ou tomar medidas.

1. Para gerir os membros para uma candidatura, selecione um azulejo de aplicação no painel **de aplicações.**

    O número de membros associados à candidatura selecionada reflete-se no azulejo dos membros.

    ![Selecione a aplicação](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Adicionar membro à aplicação

1. Selecione o azulejo do membro para apresentar uma lista dos membros atuais.
1. Selecione **Adicionar os membros**.

    ![A screenshot mostra a janela de membro da aplicação com o botão adicionar um membro realçado.](./media/manage-users/application-add-members.png)

1. Procure o nome do utilizador.  Apenas os utilizadores de AD Azure que existem no blockchain Workbench inquilino estão listados. Se o utilizador não for encontrado, tem de [adicionar utilizadores AD Azure](#add-azure-ad-users).

    ![Adicionar membros](./media/manage-users/find-user.png)

1. Selecione um **papel** a partir do drop-down.

    ![Selecione membros de funções](./media/manage-users/application-select-role.png)

1. **Selecione Adicionar** o membro com a função associada à aplicação.

#### <a name="remove-member-from-application"></a>Remover membro da aplicação

1. Selecione o azulejo do membro para apresentar uma lista dos membros atuais.
1. Para o utilizador que pretende remover, escolha **Remover** da entrega de funções.

    ![Remover membro](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Alterar ou adicionar papel

1. Selecione o azulejo do membro para apresentar uma lista dos membros atuais.
1. Para o utilizador que pretende alterar, clique no drop-down e selecione a nova função.

    ![Alterar função](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo de como fazer, aprendeu a gerir os utilizadores para a Azure Blockchain Workbench. Para aprender a criar uma aplicação blockchain, continue para o próximo artigo como fazer.

> [!div class="nextstepaction"]
> [Criar uma aplicação blockchain em Azure Blockchain Workbench](create-app.md)
