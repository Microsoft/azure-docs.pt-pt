---
title: Início rápido para ver os grupos e membros - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como pesquisar e ver os grupos da sua organização e seus membros atribuídos.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: lizross
ms.custom: it-pro, seodec18, seo-update-azuread-jan
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8eef6f7a363fe7b020a3ef18ae26799d7d5452ed
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57881015"
---
<!--As a brand-new Azure AD administrator, I need to view my organization’s groups along with the assigned members, so I can manage permissions to apps and services for people in my organization-->

# <a name="quickstart-view-your-organizations-groups-and-members-in-azure-active-directory"></a>Início rápido: Ver grupos e membros da sua organização no Azure Active Directory
Pode ver os grupos e membros de grupos da sua organização com o portal do Azure. Os grupos são utilizados para gerir os utilizadores (membros) que precisam do mesmo acesso e permissões para aplicações e serviços potencialmente restritos.

Neste início rápido, irá ver todos os grupos existentes da sua organização e ver os membros atribuídos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, precisará de:

- Criar um inquilino do Azure Active Directory. Para obter mais informações, veja [Aceder ao portal do Azure Active Directory e criar um novo inquilino](active-directory-access-create-new-tenant.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Tem de iniciar sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

## <a name="create-a-new-group"></a>Criar um novo grupo 
Crie um novo grupo, com o nome _Política MDM - Oeste_. Para obter mais informações sobre como criar um grupo, veja [Como criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md).

1. Selecione **Azure Active Directory**, **Grupos** e, em seguida, selecione **Novo grupo**.

2. Preencha a página **Grupo**:
    
    - **Tipo de grupo:** Selecione **segurança**
    
    - **Nome do grupo:** Tipo de _política da MDM - oeste_
    
    - **Tipo de associação:** Selecione **atribuídos**.

3. Selecione **Criar**.

## <a name="create-a-new-user"></a>Criar um novo utilizador
Crie um novo utilizador, com o nome _Alain Charon_. O utilizador tem de existir antes de ser adicionado como membro do grupo. Para obter mais informações sobre a criação de um utilizador, veja [Como adicionar ou eliminar utilizadores](add-users-azure-active-directory.md).

1. Selecione **Azure Active Directory**, **Utilizadores** e, em seguida, selecione **Novo utilizador**.

2. Preencha a página **Utilizador**:

    - **Nome:** Tipo _Alain Charon_.

    - **Nome de utilizador:** Tipo *alain\@contoso.com*.

3. Copie a palavra-passe gerada automaticamente,fornecida na caixa **Palavra-passe** e, em seguida, selecione **Criar**.

## <a name="add-a-group-member"></a>Adicionar um membro ao grupo
Agora que tem um grupo e um utilizador, pode adicionar _Alain Charon_ como membro ao grupo _Política MDM - Oeste_. Para obter mais informações sobre a adição de membros a grupos, veja [Como adicionar ou remover membros de um grupo](active-directory-groups-members-azure-portal.md).

1. Selecione **Azure Active Directory** > **Grupos**.

2. Na página **Grupos - Todos os grupos**, procure e selecione o grupo **Política MDM - Oeste**.

3. Na página **Descrição Geral da Política MDM - Oeste**, selecione **Membros** na área **Gerir**.

4. Selecione **Adicionar membros** e, em seguida, procure e selecione **Alain Charon**.

5. Escolha **Selecionar**.

## <a name="view-all-groups"></a>Ver todos os grupos
Pode ver todos os grupos da sua organização na página **Grupos – Todos os grupos** do portal do Azure.

- Selecione Azure **Active Directory** > **Grupos**.

    A página **Grupos – Todos os grupos** é apresentada, mostrando todos os grupos ativos.

    ![Página Grupos – Todos os grupos, que mostra todos os grupos existentes](media/active-directory-groups-view-azure-portal/groups-all-groups-blade-with-all-groups.png)

## <a name="search-for-the-group"></a>Procurar o grupo
Procure na página **Grupos – Todos os grupos** para encontrar o grupo **Política MDM – Oeste**.

1. Na página **Grupos – Todos os grupos**. escreva _MDM_ na caixa **Pesquisar**.

    Os resultados da pesquisa são apresentados na caixa **Pesquisar**, incluindo o grupo _Política MDM - Oeste_.

    ![Grupos – todos as páginas de grupos com a caixa de pesquisa preenchida](media/active-directory-groups-view-azure-portal/search-for-specific-group.png)

3. Selecione o grupo **Política MDM – Oeste**.

4. Veja as informações do grupo na página **Descrição Geral da Política MDM - Oeste**, incluindo o número de membros desse grupo.

    ![Descrição Geral da Política MDM - Oeste, com informações dos membros](media/active-directory-groups-view-azure-portal/group-overview-blade.png)

## <a name="view-group-members"></a>Ver os membros do grupo
Agora que encontrou o grupo, pode ver todos os membros atribuídos.

- Selecione **Membros** na área **Gerir** e, em seguida, reveja a lista completa de nomes dos membros atribuídos a esse grupo específico, incluindo _Alain Charon_.

    ![Lista de membros atribuídos ao grupo Política MDM – Oeste](media/active-directory-groups-view-azure-portal/groups-all-members.png)

## <a name="clean-up-resources"></a>Limpar recursos
Este grupo é utilizado em vários processos de procedimentos que estão disponíveis na secção **Manuais de instruções** desta documentação. No entanto, se preferir não utilizar este grupo, pode eliminá-lo e aos respetivos membros atribuídos com os seguintes passos:

1. Na página **Grupos - Todos os grupos**, procure o grupo **Política MDM - Oeste**.

2.  Selecione o grupo **Política MDM - Oeste**.

    A página **Descrição Geral da Política MDM - Oeste** é apresentada.

3. Selecione **Eliminar**.

    O grupo e os respetivos membros associados são eliminados.

    ![Página Descrição Geral da Política MDM - Oeste com a hiperligação Eliminar realçada](media/active-directory-groups-view-azure-portal/group-overview-blade-delete.png)

    >[!Important]
    >Esta ação não elimina o utilizador Alain Charon, apenas elimina a sua associação do grupo eliminado.

## <a name="next-steps"></a>Passos Seguintes
Avance para o artigo seguinte para saber como associar uma subscrição ao diretório do Azure AD.

> [!div class="nextstepaction"]
> [Associar uma subscrição do Azure](active-directory-how-subscriptions-associated-directory.md)
