---
title: Adicionar ou remover membros do grupo - Diretório Ativo Azure [ Azure Ative Diretório ] Microsoft Docs
description: Instruções sobre como adicionar ou remover membros de um grupo utilizando o Diretório Ativo Azure.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1c83c57be63ae9e2a4d4113accaefe8a2c2b525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561963"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Adicionar ou remover membros do grupo utilizando o Diretório Ativo Azure
Utilizando o Diretório Ativo Azure, pode continuar a adicionar e remover membros do grupo.

## <a name="to-add-group-members"></a>Para adicionar membros do grupo

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **Diretório Ativo Azure**e, em seguida, selecione **Grupos**.

3. A partir da página **Grupos - Todos os grupos,** procure e selecione o grupo a que pretende adicionar o membro. Neste caso, use o nosso grupo anteriormente criado, **a política do MDM - West**.

    ![Página de grupos-todos os grupos, nome de grupo destacado](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Na página **Descrição Geral da Política MDM - Oeste**, selecione **Membros** na área **Gerir**.

    ![Política do MDM - Página de visão geral ocidental, com opção dos deputados em destaque](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Selecione **Adicionar membros**, e, em seguida, pesquisar e selecionar cada um dos membros que pretende adicionar ao grupo e, em seguida, escolher **Selecionar**.

    Receberá uma mensagem que diz que os membros foram adicionados com sucesso.

    ![Adicionar página de membros, com pesquisado para membro mostrado](media/active-directory-groups-members-azure-portal/update-members.png)

6. Refresque o ecrã para ver todos os nomes dos membros adicionados ao grupo.

## <a name="to-remove-group-members"></a>Para remover membros do grupo

1. A partir da página **Grupos - Todos os grupos,** procure e selecione o grupo de que pretende remover o membro. Mais uma vez usaremos a política do **MDM - Oeste.**

2. Selecione **Membros** da área **'Gerir',** procure e selecione o nome do membro para remover e, em seguida, selecione **Remover**.

    ![Página de informações do membro, com opção Remover](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Passos seguintes

- [Ver os seus grupos e membros](active-directory-groups-view-azure-portal.md)

- [Editar as suas definições de grupo](active-directory-groups-settings-azure-portal.md)

- [Gerir o acesso a recursos através de grupos](active-directory-manage-groups.md)

- [Gerir regras dinâmicas dos utilizadores num grupo](../users-groups-roles/groups-create-rule.md)

- [Associar ou adicionar uma subscrição Azure ao Azure Ative Directory](active-directory-how-subscriptions-associated-directory.md)
