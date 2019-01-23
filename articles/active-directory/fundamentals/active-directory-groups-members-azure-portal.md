---
title: Adicionar ou remover membros do grupo - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como adicionar ou remover membros de um grupo com o Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.openlocfilehash: c92ac805fe9c8f3554044aa85306ecc43464411b
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54445350"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Adicionar ou remover membros do grupo com o Azure Active Directory
Utilizar o Azure Active Directory, pode continuar a adicionar e remover membros do grupo.

## <a name="to-add-group-members"></a>Para adicionar membros do grupo

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **do Azure Active Directory**e, em seguida, selecione **grupos**.

3. Partir do **grupos – todos os grupos** página, procure e selecione o grupo que pretende adicionar o membro para. Neste caso, utilize o nosso grupo criado anteriormente, **política MDM - oeste**.

    ![Página de grupos de todos os grupos, nome do grupo realçado](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Na página **Descrição Geral da Política MDM - Oeste**, selecione **Membros** na área **Gerir**.

    ![Política MDM - página de descrição geral do oeste, com a opção de membros realçada](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Selecione **adicionar membros**e, em seguida, procure e selecione cada um dos membros que pretende adicionar ao grupo e, em seguida, escolha **selecione**.

    Obterá uma mensagem que diz que os membros foram adicionados com êxito.

    ![Adicionar página de membros, com procurou membro mostrado](media/active-directory-groups-members-azure-portal/update-members.png)

6. Atualize o ecrã para ver todos os nomes de membro adicionados ao grupo.

## <a name="to-remove-group-members"></a>Para remover membros do grupo

1. Partir do **grupos – todos os grupos** página, procure e selecione o grupo que pretende remover o membro de. Novamente, usaremos, **política MDM - oeste**.

2. Selecione **membros** partir do **gerir** área, procure e selecione o nome do membro a remover e, em seguida, selecione **remover**.

    ![Página de informações do membro, com a opção Remover](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Passos Seguintes

- [Ver os seus grupos e membros](active-directory-groups-view-azure-portal.md)

- [Editar as suas definições de grupo](active-directory-groups-settings-azure-portal.md)

- [Gerir o acesso a recursos através de grupos](active-directory-manage-groups.md)

- [Gerir regras dinâmicas dos utilizadores num grupo](../users-groups-roles/groups-create-rule.md)

- [Associar ou adicionar uma subscrição do Azure ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
