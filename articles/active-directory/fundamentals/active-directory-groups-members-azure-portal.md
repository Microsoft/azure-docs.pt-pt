---
title: Adicionar ou remover membros do grupo - Azure Ative Directory / Microsoft Docs
description: Instruções sobre como adicionar ou remover membros de um grupo que utiliza o Diretório Azure Ative.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/23/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbb7b7da4ed5268e66260e48d6367bbe02f0845a
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797581"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Adicionar ou remover membros do grupo usando o Azure Ative Directory
Utilizando o Azure Ative Directory, pode continuar a adicionar e remover membros do grupo.

## <a name="to-add-group-members"></a>Para adicionar membros do grupo

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **Azure Ative Directory**e, em seguida, selecione **Grupos**.

3. A partir da página **Grupos - Todos os grupos,** procure e selecione o grupo a que pretende adicionar o membro. Neste caso, use o nosso grupo anteriormente criado, **a política do MDM - West**.

    ![Grupos-Todos os grupos página, nome de grupo destacado](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Na página **Descrição Geral da Política MDM - Oeste**, selecione **Membros** na área **Gerir**.

    ![Política do MDM - Página geral do Oeste, com a opção dos membros em destaque](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. **Selecione Adicionar os membros**e, em seguida, pesquisar e selecionar cada um dos membros que pretende adicionar ao grupo e, em seguida, escolher **Selecione**.

    Receberá uma mensagem que diz que os membros foram adicionados com sucesso.

    ![Adicione a página dos membros, com procurado para membro mostrado](media/active-directory-groups-members-azure-portal/update-members.png)

6. Refresque o ecrã para ver todos os nomes dos membros adicionados ao grupo.

## <a name="to-remove-group-members"></a>Para remover membros do grupo

1. A partir da página **Grupos - Todos os grupos,** procure e selecione o grupo de onde pretende remover o membro. Mais uma vez usaremos a política do **MDM - West**.

2. Selecione **Os Membros** da área **'Gerir',** procure e selecione o nome do membro a remover e, em seguida, selecione **Remover**.

    ![Página de informações do membro, com opção Remover](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Passos seguintes

- [Ver os seus grupos e membros](active-directory-groups-view-azure-portal.md)

- [Editar as suas definições de grupo](active-directory-groups-settings-azure-portal.md)

- [Gerir o acesso a recursos através de grupos](active-directory-manage-groups.md)

- [Gerir regras dinâmicas dos utilizadores num grupo](../users-groups-roles/groups-create-rule.md)

- [Associar ou adicionar uma assinatura Azure ao Azure Ative Directory](active-directory-how-subscriptions-associated-directory.md)
