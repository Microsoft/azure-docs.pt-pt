---
title: Adicionar ou eliminar utilizadores - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como adicionar novos utilizadores ou eliminar os utilizadores existentes com o Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8770648a3683c4f612536c9a04921682a01bcd0c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089816"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Adicionar ou eliminar os utilizadores que utilizam o Azure Active Directory
Adicionar novos utilizadores ou eliminar os utilizadores existentes do seu inquilino do Azure Active Directory (Azure AD).

## <a name="add-a-new-user"></a>Adicionar um novo utilizador
Pode criar um novo utilizador com o portal do Azure Active Directory.

### <a name="to-add-a-new-user"></a>Para adicionar um novo utilizador
1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) como um Administrador Global ou administrador de utilizadores do diretório.

2. Selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **novo utilizador**.

    ![Utilizadores - página de todos os utilizadores com o novo utilizador realçado](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Sobre o **utilizador** página, preencha as informações necessárias.

    ![Adicionar novo utilizador, a página de utilizador com informações do utilizador](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **Nome (obrigatório).** O nome próprio e apelido do utilizador novo. Por exemplo, Mary Parker.

   - **Nome de utilizador (obrigatório).** O nome de utilizador do utilizador novo. Por exemplo, mary@contoso.com. 
    
       A parte do domínio do nome do utilizador tem de utilizar qualquer um do predefinido inicial nome de domínio <_NomeDominio_>. onmicrosoft.com ou um nome de domínio personalizado, como contoso.com. Para obter mais informações sobre como criar um nome de domínio personalizado, consulte [como adicionar um nome de domínio personalizado ao Azure Active Directory](add-custom-domain.md).

   - **Perfil.** Opcionalmente, pode adicionar mais informações sobre o utilizador. Também pode adicionar informações de utilizador num momento posterior. Para obter mais informações sobre como adicionar informações do utilizador, consulte [como adicionar ou alterar as informações de perfil do usuário](active-directory-users-profile-azure-portal.md).

   - **Grupos.** Opcionalmente, pode adicionar o utilizador a um ou mais grupos existentes. Também pode adicionar o utilizador a grupos num momento posterior. Para obter mais informações sobre como adicionar utilizadores a grupos, consulte [como criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md).

   - **Função de diretório.** Opcionalmente, pode adicionar o utilizador a uma função de diretório. Pode atribuir ao utilizador para ser um administrador global, ou a um ou mais das outras funções de administrador no Azure AD. Para obter mais informações sobre a atribuição de funções, consulte [como atribuir funções a utilizadores](active-directory-users-assign-role-azure-portal.md).

4. Copie o gerado automaticamente palavra-passe fornecida no **palavra-passe** caixa. Terá de dar esta palavra-passe ao utilizador para o processo de início de sessão inicial.

5. Selecione **Criar**.

    O utilizador é criado e adicionado ao inquilino do Azure AD.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Adicionar um novo utilizador dentro de um ambiente híbrido
Se tiver um ambiente com o Azure Active Directory (cloud) e o Windows Server Active Directory (no local), pode adicionar novos utilizadores ao sincronizar os dados da conta de utilizador existente. Para obter mais informações sobre ambientes híbridos e utilizadores, consulte [integrar seus diretórios no local com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Eliminar um utilizador
Pode eliminar um utilizador existente através do portal do Azure Active Directory.

### <a name="to-delete-a-user"></a>Para eliminar um utilizador
1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

2. Selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, procure e selecione o utilizador que pretende eliminar do seu inquilino do Azure AD. Por exemplo, _Mary Parker_.

3. Selecione **eliminação do utilizador**.

    ![Utilizadores - página de todos os utilizadores com a eliminação do utilizador realçada](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    O utilizador é eliminado e já não aparece no **utilizadores - todos os utilizadores** página. O utilizador pode ser visto no **utilizadores eliminados** página para os próximos 30 dias e podem ser restaurados durante esse período. Para obter mais informações sobre como restaurar um utilizador, consulte [como restaurar ou remover permanentemente um utilizador eliminado recentemente](active-directory-users-restore.md).

    >[!Note]
    >Tem de utilizar o Windows Server Active Directory para atualizar a identidade, informações de contacto ou informações da tarefa para os utilizadores cuja origem de autoridade é o Windows Server Active Directory. Depois de concluir a atualização, tem de aguardar o próximo ciclo de sincronização seja concluída antes de verá as alterações.

## <a name="next-steps"></a>Passos Seguintes
Depois de adicionar os seus utilizadores, pode efetuar os seguintes processos de basic:

- [Adicionar ou alterar as informações de perfil](active-directory-users-profile-azure-portal.md)

- [Atribuir funções a utilizadores](active-directory-users-assign-role-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Trabalhar com grupos dinâmicos e utilizadores](../users-groups-roles/groups-create-rule.md)

Ou pode realizar outras tarefas de gestão do utilizador, tal como [adicionar utilizadores convidados a partir de outro diretório](../b2b/what-is-b2b.md) ou [restaurar um utilizador eliminado](active-directory-users-restore.md). Para obter mais informações sobre outras ações disponíveis, consulte [documentação de gestão de utilizador do Azure Active Directory](../users-groups-roles/index.yml).
