---
title: Adicionar ou excluir usuários-Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar novos usuários ou excluir usuários existentes usando Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e98b09d8b07c625613e3be149e64ac8f06adc089
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805551"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Adicionar ou excluir usuários usando Azure Active Directory
Adicione novos usuários ou exclua usuários existentes da organização do Azure Active Directory (Azure AD). Para adicionar ou excluir usuários, você deve ser um administrador de usuário ou um administrador global. 

## <a name="add-a-new-user"></a>Adicionar um novo usuário
Você pode criar um novo usuário usando o portal de Azure Active Directory.

### <a name="to-add-a-new-user"></a>Para adicionar um novo usuário
1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador de usuário para a organização.

2. Selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **novo usuário**.

    ![Usuários – página todos os usuários com novo usuário realçado](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Na página **Utilizador**, preencha as informações necessárias.

    ![Adicionar novo usuário, página do usuário com informações do usuário](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **Nome (obrigatório).** O nome e o sobrenome do novo usuário. Por exemplo, Mary Parker.

   - **Nome de usuário (obrigatório).** O nome de usuário do novo usuário. Por exemplo, mary@contoso.com.
    
       A parte do domínio do nome de usuário deve usar o nome de domínio padrão inicial, <_yourdomainname_>. onmicrosoft. com ou um nome de domínio personalizado, como contoso.com. Para obter mais informações sobre como criar um nome de domínio personalizado, consulte [como adicionar um nome de domínio personalizado a Azure Active Directory](add-custom-domain.md).

   - **Criar.** Opcionalmente, você pode adicionar mais informações sobre o usuário. Você também pode adicionar informações do usuário posteriormente. Para obter mais informações sobre como adicionar informações do usuário, consulte [como adicionar ou alterar as informações de perfil do usuário](active-directory-users-profile-azure-portal.md).

   - **Agrupa.** Opcionalmente, você pode adicionar o usuário a um ou mais grupos existentes. Você também pode adicionar o usuário a grupos em um momento posterior. Para obter mais informações sobre como adicionar usuários a grupos, consulte [como criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md).

   - **Função de diretório.** Opcionalmente, você pode adicionar o usuário a uma função de administrador do Azure AD. Você pode atribuir o usuário para ser um administrador global ou uma ou mais das funções de administrador limitadas no Azure AD. Para obter mais informações sobre a atribuição de funções, consulte [como atribuir funções a usuários](active-directory-users-assign-role-azure-portal.md).

4. Copie a senha gerada automaticamente fornecida na caixa **senha** . Você precisará fornecer essa senha ao usuário para o processo de entrada inicial.

5. Selecione **Criar**.

    O usuário é criado e adicionado ao seu locatário do Azure AD.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Adicionar um novo usuário em um ambiente híbrido
Se você tiver um ambiente com o Azure Active Directory (nuvem) e o Windows Server Active Directory (local), poderá adicionar novos usuários sincronizando os dados da conta de usuário existente. Para obter mais informações sobre ambientes híbridos e usuários, consulte [integrar seus diretórios locais com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Eliminar um utilizador
Você pode excluir um usuário existente usando Azure Active Directory Portal.

### <a name="to-delete-a-user"></a>Para excluir um usuário
1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador de usuário para a organização.

2. Selecione **Azure Active Directory**, selecione **usuários**e, em seguida, pesquise e selecione o usuário que você deseja excluir do seu locatário do Azure AD. Por exemplo, _Mary Parker_.

3. Selecione **excluir usuário**.

    ![Usuários – página todos os usuários com excluir usuário realçado](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    O usuário é excluído e não aparece mais na página **usuários – todos os usuários** . O usuário pode ser visto na página **usuários excluídos** nos próximos 30 dias e pode ser restaurado durante esse período. Para obter mais informações sobre como restaurar um usuário, consulte [como restaurar ou remover permanentemente um usuário excluído recentemente](active-directory-users-restore.md). Quando um usuário é excluído, todas as licenças consumidas pelo usuário são disponibilizadas para que outros usuários sejam consumidos.

    >[!Note]
    >Você deve usar o Windows Server Active Directory para atualizar a identidade, informações de contato ou informações do trabalho para usuários cuja origem de autoridade é Active Directory do Windows Server. Depois de concluir a atualização, você deve aguardar a conclusão do próximo ciclo de sincronização antes de ver as alterações.

## <a name="next-steps"></a>Passos seguintes

Depois de adicionar os usuários, você pode executar os seguintes processos básicos:

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)

- [Atribuir funções a utilizadores](active-directory-users-assign-role-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Trabalhar com grupos dinâmicos e usuários](../users-groups-roles/groups-create-rule.md)

Ou você pode executar outras tarefas de gerenciamento de usuário, como [Adicionar usuários convidados de outro diretório](../b2b/what-is-b2b.md) ou [restaurar um usuário excluído](active-directory-users-restore.md). Para obter mais informações sobre outras ações disponíveis, consulte [Azure Active Directory documentação de gerenciamento de usuário](../users-groups-roles/index.yml).
