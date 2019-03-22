---
title: Gerir utilizadores no Azure Blockchain Workbench
description: Como gerir os utilizadores no Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 2df0fea4d1dbdfb532cff663ceabfef80d61fe45
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57994406"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Gerir utilizadores no Azure Blockchain Workbench

O Azure Blockchain Workbench inclui gestão de utilizadores para as pessoas e organizações que fazem parte do seu consortium.

## <a name="prerequisites"></a>Pré-requisitos

Uma implementação de Blockchain Workbench é necessária. Ver [implementação do Azure Blockchain Workbench](deploy.md) para obter detalhes sobre a implementação.

## <a name="add-azure-ad-users"></a>Adicionar utilizadores do Azure AD

O Azure Blockchain Workbench utiliza o Azure Active Directory (Azure AD) para autenticação, controlo de acesso e funções. Os utilizadores no inquilino do Azure AD do Blockchain Workbench podem autenticar e utilizar o Blockchain Workbench. Adicione utilizadores à função Administrador do aplicativo para interagir e executar ações.

Os utilizadores de Blockchain Workbench têm de existir no inquilino do Azure AD para poder atribuí-los para aplicações e funções. Para adicionar utilizadores ao Azure AD, utilize os seguintes passos:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
2.  Selecione a sua conta no canto superior direito e mude para o inquilino do Azure AD associado à Bancada de trabalho de Blockchain.
3.  Selecione **do Azure Active Directory > utilizadores**. É apresentada uma lista de utilizadores no seu diretório.
4.  Para adicionar utilizadores ao diretório, selecione **novo utilizador**. Para utilizadores externos, selecione **novo utilizador convidado**.

    ![Novo utilizador](./media/manage-users/add-ad-user.png)

5.  Preencha os campos obrigatórios para o novo utilizador. Selecione **Criar**.

Visite [do Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md) documentação para obter mais detalhes sobre como gerir os utilizadores no Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Gerir administradores de Blockchain Workbench

Assim que os utilizadores tiverem sido adicionados ao diretório, a próxima etapa é escolher quais usuários são administradores de Blockchain Workbench. Os utilizadores a **administrador** grupo estão associados a **função de administrador de aplicativo** na bancada de trabalho de Blockchain. Os administradores podem adicionar ou remover utilizadores, atribuir utilizadores a cenários específicos e criar novos aplicativos.

Para adicionar utilizadores para o **administrador** grupo no diretório do Azure AD:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
2.  Certifique-se de que está no inquilino do Azure AD associado à Blockchain Workbench ao selecionar a sua conta no canto superior direito.
3.  Selecione **do Azure Active Directory > aplicações empresariais**.
4.  Selecione a aplicação de cliente do Azure AD para o Blockchain Workbench
    
    ![Todos os registos de aplicação empresarial](./media/manage-users/select-blockchain-client-app.png)

5.  Selecione **utilizadores e grupos > Adicionar utilizador**.
6.  Na **adicionar atribuição**, selecione **utilizadores**. Escolha ou procure o utilizador que pretende adicionar como administrador. Clique em **selecione** quando terminar de selecionar.

    ![Adicionar atribuição](./media/manage-users/add-user-assignment.png)

9.  Certifique-se **função** está definida como **administrador**
10. Selecione **Atribuir**. Os utilizadores adicionados são apresentados na lista com a função de administrador atribuída.

    ![Utilizadores de aplicações de cliente de Blockchain](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Gerir membros de Blockchain Workbench

Utilize a aplicação de Blockchain Workbench para gerir utilizadores e as organizações que fazem parte do seu consortium. Pode adicionar ou remover utilizadores de aplicações e funções.

1. [Abra o Blockchain Workbench](deploy.md#blockchain-workbench-web-url) no seu browser e inicie sessão como administrador.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Os membros são adicionados a cada aplicativo. Os membros podem ter uma ou mais funções de aplicação para iniciar contratos ou executar ações.

2. Para gerir os membros de uma aplicação, selecione um mosaico de aplicação no **aplicativos** painel.

    O número de membros associados à aplicação selecionada é refletido no mosaico de membros.

    ![Selecionar aplicação](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Adicionar membro à aplicação

1. Selecione o mosaico de membro para apresentar uma lista dos membros atuais.
2. Selecione **adicionar membros**.

    ![Adicionar membros](./media/manage-users/application-add-members.png)

3. Procure o nome do utilizador.  Apenas são listados os utilizadores do Azure AD que existe no inquilino do Blockchain Workbench. Se o utilizador não for encontrado, precisará [adicionar utilizadores do Azure AD](#add-azure-ad-users).

    ![Adicionar membros](./media/manage-users/find-user.png)

4. Selecione um **função** na lista suspensa.

    ![Selecione os membros da função](./media/manage-users/application-select-role.png)

5. Selecione **adicionar** para adicionar o membro com a função associada à aplicação.

#### <a name="remove-member-from-application"></a>Remover membro da aplicação

1. Selecione o mosaico de membro para apresentar uma lista dos membros atuais.
2. Para o utilizador que pretende remover, escolha **remover** da função pendente.

    ![Remover membro](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Alterar ou Adicionar função

1. Selecione o mosaico de membro para apresentar uma lista dos membros atuais.
2. Para o usuário que deseja alterar, clique na lista pendente e selecione a nova função.

    ![Alterar função](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Passos Seguintes

Neste artigo de procedimentos, aprendeu como gerir os utilizadores para o Azure Blockchain Workbench. Para saber como criar uma aplicação de blockchain, avance para o próximo artigo de procedimento.

> [!div class="nextstepaction"]
> [Criar uma aplicação de blockchain no Azure Blockchain Workbench](create-app.md)
