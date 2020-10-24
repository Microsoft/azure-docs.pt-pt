---
title: Restaurar ou remover permanentemente o utilizador recentemente eliminado - Azure AD
description: Como visualizar utilizadores restauradores, restaurar um utilizador eliminado ou eliminar permanentemente um utilizador com o Azure Ative Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/23/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4d42e1cafa58aaf6a47188c9e86c24ac175047
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514394"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Restaurar ou remover um utilizador recentemente eliminado usando o Azure Ative Directory
Depois de eliminar um utilizador, a conta permanece em estado suspenso durante 30 dias. Durante essa janela de 30 dias, a conta do utilizador pode ser restaurada, juntamente com todas as suas propriedades. Após a janela de 30 dias passar, o processo de eliminação permanente é automaticamente iniciado.

Pode ver os seus utilizadores restauradores, restaurar um utilizador eliminado ou eliminar permanentemente um utilizador utilizando o Azure Ative Directory (AZure AD) no portal Azure.

>[!Important]
>Nem você nem o suporte ao cliente da Microsoft podem restaurar um utilizador permanentemente eliminado.

## <a name="required-permissions"></a>Permissões obrigatórias
Deve ter uma das seguintes funções para restaurar e eliminar permanentemente os utilizadores.

- Administrador global

- Suporte parceiro Tier1

- Suporte parceiro Tier2

- Administrador de utilizadores

## <a name="view-your-restorable-users"></a>Ver os seus utilizadores restauradores
Pode ver todos os utilizadores que foram apagados há menos de 30 dias. Estes utilizadores podem ser restaurados.

### <a name="to-view-your-restorable-users"></a>Para ver os seus utilizadores restauradores
1. Inscreva-se no [portal Azure](https://portal.azure.com/) usando uma conta de administrador global para a organização.

2. Selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, **selecione utilizadores eliminados**.

    Reveja a lista de utilizadores que estão disponíveis para restaurar.

    ![Utilizadores - Página de utilizadores eliminados, com utilizadores que ainda podem ser restaurados](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Restaurar um utilizador recentemente eliminado

Quando uma conta de utilizador é eliminada da organização, a conta encontra-se em estado suspenso e todas as informações relacionadas com a organização são preservadas. Quando restaura um utilizador, esta informação da organização também é restaurada.

> [!Note]
> Uma vez restaurado o utilizador, as licenças que foram atribuídas ao utilizador no momento da eliminação também são restauradas, mesmo que não existam lugares disponíveis para essas licenças. Se estiver a consumir mais licenças do que comprou, a sua organização poderá ficar temporariamente fora do cumprimento do uso da licença.

### <a name="to-restore-a-user"></a>Para restaurar um utilizador
1. Na página **dos Utilizadores - Eliminados,** procure e selecione um dos utilizadores disponíveis. Por exemplo, _Mary Parker._

2. Selecione **Restaurar o utilizador**.

    ![Utilizadores - Página de utilizadores eliminada, com a opção de utilizador Restaurar em destaque](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Eliminar permanentemente um utilizador
Pode eliminar permanentemente um utilizador da sua organização sem esperar os 30 dias para a eliminação automática. Um utilizador permanentemente eliminado não pode ser restaurado por si, outro administrador, nem pelo suporte ao cliente da Microsoft.

>[!Note]
>Se eliminar permanentemente um utilizador por engano, terá de criar um novo utilizador e introduzir manualmente todas as informações anteriores. Para obter mais informações sobre a criação de um novo utilizador, consulte [Adicionar ou eliminar utilizadores](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Para eliminar permanentemente um utilizador

1. Na página **dos Utilizadores - Eliminados,** procure e selecione um dos utilizadores disponíveis. Por exemplo, _Rae Huff._

2. **Selecione Eliminar permanentemente**.

    ![Utilizadores - Página de utilizadores eliminada, com a opção de utilizador delete em destaque](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Passos seguintes
Depois de restaurar ou apagar os seus utilizadores, pode:

- [Adicionar ou eliminar utilizadores](add-users-azure-active-directory.md)

- [Atribuir funções a utilizadores](active-directory-users-assign-role-azure-portal.md)

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)

- [Adicionar utilizadores convidados de outra organização](../external-identities/what-is-b2b.md)

Para mais informações sobre outras tarefas disponíveis de gestão de [utilizadores, a documentação de gestão de utilizadores Azure AD](../enterprise-users/index.yml).
