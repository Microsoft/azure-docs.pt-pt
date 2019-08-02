---
title: Editar as suas informações de grupo - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como editar informações do seu grupo com o Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc06abca08b2522ac57552e85f7c1bac3ef854af
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561887"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Editar as suas informações de grupo com o Azure Active Directory

Utilizar o Azure Active Directory (Azure AD), pode editar as definições de um grupo, incluindo a atualização do seu tipo de associação, nome ou descrição.

## <a name="to-edit-your-group-settings"></a>Para editar as definições de grupo
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **do Azure Active Directory**e, em seguida, selecione **grupos**.

    O **grupos – todos os grupos** é apresentada a página, que mostra todos os grupos do Active Directory.

3. Do **grupos – todos os grupos de** página, escreva o máximo do nome do grupo possível para o **pesquisa** caixa. Para os fins deste artigo, estamos procurando os **política MDM - oeste** grupo.

    Os resultados da pesquisa são apresentados no **pesquisa** caixa, a atualização à medida que escreve mais carateres.

    ![Todos os grupos de página, com texto de pesquisa na caixa de pesquisa](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Selecione o grupo de **política MDM - oeste**e, em seguida, selecione **propriedades** partir o **gerir** área.

    ![Página Visão geral do grupo, com a opção de membro e as informações realçadas](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Atualização do **definições gerais** informações conforme necessário, incluindo:

    ![Definições de propriedades para um grupo](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Nome do grupo.** Edite o nome do grupo existente.
    
    - **Descrição do grupo.** Edite a descrição do grupo existente.

    - **Tipo de grupo.** Não é possível alterar o tipo de grupo depois de ser criado. Para alterar o **tipo de grupo**, tem de eliminar o grupo e criar um novo.
    
    - **Tipo de associação.** Altere o tipo de associação. Para obter mais informações sobre os vários tipos de associação disponíveis [, consulte Como: Crie um grupo básico e adicione membros usando o portal](active-directory-groups-create-azure-portal.md)de Azure Active Directory.
    
    - **ID de objeto.** Não é possível alterar o ID de objeto, mas pode copiá-lo para utilizar nos seus comandos do PowerShell para o grupo. Para mais informações sobre como utilizar cmdlets do PowerShell, veja [cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Passos Seguintes
Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

- [Ver os seus grupos e membros](active-directory-groups-view-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Como adicionar ou remover membros de um grupo](active-directory-groups-members-azure-portal.md)

- [Gerir regras dinâmicas dos utilizadores num grupo](../users-groups-roles/groups-create-rule.md)

- [Gerir associações de um grupo](active-directory-groups-membership-azure-portal.md)

- [Gerir o acesso a recursos através de grupos](active-directory-manage-groups.md)

- [Associar ou adicionar uma subscrição do Azure ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
