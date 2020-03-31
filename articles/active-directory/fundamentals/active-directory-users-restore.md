---
title: Restaurar ou remover permanentemente o utilizador recentemente eliminado - Azure AD
description: Como visualizar utilizadores reputáveis, restaurar um utilizador eliminado ou eliminar permanentemente um utilizador com o Diretório Ativo Do Azure.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 573269da1ca6b9ee09b493f4e758e78121d6c2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422868"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Restaurar ou remover um utilizador recentemente eliminado usando o Diretório Ativo Do Azure
Depois de eliminar um utilizador, a conta permanece em estado suspenso durante 30 dias. Durante essa janela de 30 dias, a conta de utilizador pode ser restaurada, juntamente com todas as suas propriedades. Após os passes de janela de 30 dias, o utilizador é automaticamente e permanentemente eliminado.

Pode visualizar os seus utilizadores restoráveis, restaurar um utilizador eliminado ou eliminar permanentemente um utilizador utilizando o Azure Ative Directory (Azure AD) no portal Azure.

>[!Important]
>Nem você nem o suporte ao cliente da Microsoft podem restaurar um utilizador permanentemente eliminado.

## <a name="required-permissions"></a>Permissões obrigatórias
Deve ter uma das seguintes funções para restaurar e eliminar permanentemente os utilizadores.

- Administrador global

- Suporte de parceiro tier1

- Suporte de parceiro tier2

- Administrador de utilizadores

## <a name="view-your-restorable-users"></a>Veja os seus utilizadores restoráveis
Pode ver todos os utilizadores que foram apagados há menos de 30 dias. Estes utilizadores podem ser restaurados.

### <a name="to-view-your-restorable-users"></a>Para ver os seus utilizadores restoráveis
1. Inscreva-se no [portal Azure](https://portal.azure.com/) usando uma conta de administrador global para a organização.

2. Selecione **Diretório Ativo Azure,** selecione **Utilizadores**e, em seguida, selecione **utilizadores Eliminados**.

    Reveja a lista de utilizadores que estão disponíveis para restaurar.

    ![Utilizadores - Página de utilizadores apagados, com utilizadores que ainda podem ser restaurados](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Restaurar um utilizador recentemente eliminado

Quando uma conta de utilizador é eliminada da organização, a conta encontra-se em estado suspenso e todas as informações relacionadas com a organização são preservadas. Quando restaura um utilizador, esta informação da organização também é restaurada.

> [!Note]
> Uma vez restaurado o utilizador, as licenças que foram atribuídas ao utilizador no momento da eliminação também são restauradas mesmo que não existam lugares disponíveis para essas licenças. Se estiver a consumir mais licenças do que comprou, a sua organização poderá estar temporariamente fora do cumprimento do uso da licença.

### <a name="to-restore-a-user"></a>Para restaurar um utilizador
1. Na página **Utilizadores - Utilizadores apagados,** procure e selecione um dos utilizadores disponíveis. Por exemplo, _Mary Parker._

2. Selecione **Restaurar o utilizador**.

    ![Utilizadores - Página de utilizadores eliminados, com a opção restaurar o utilizador em destaque](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Excluir permanentemente um utilizador
Pode eliminar permanentemente um utilizador da sua organização sem esperar os 30 dias para a eliminação automática. Um utilizador permanentemente eliminado não pode ser restaurado por si, outro administrador, nem pelo suporte ao cliente da Microsoft.

>[!Note]
>Se eliminar permanentemente um utilizador por engano, terá de criar um novo utilizador e introduzir manualmente todas as informações anteriores. Para mais informações sobre a criação de um novo utilizador, consulte [Adicionar ou excluir utilizadores](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Para eliminar permanentemente um utilizador

1. Na página **Utilizadores - Utilizadores apagados,** procure e selecione um dos utilizadores disponíveis. Por exemplo, _Rae Huff._

2. **Selecione Eliminar permanentemente**.

    ![Utilizadores - Página de utilizadores eliminados, com a opção restaurar o utilizador em destaque](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Passos seguintes
Depois de restaurar ou apagar os seus utilizadores, pode realizar os seguintes processos básicos:

- [Adicionar ou eliminar utilizadores](add-users-azure-active-directory.md)

- [Atribuir funções a utilizadores](active-directory-users-assign-role-azure-portal.md)

- [Adicionar ou alterar informações sobre o perfil](active-directory-users-profile-azure-portal.md)

- [Adicione utilizadores convidados de outra organização](../b2b/what-is-b2b.md)

Para mais informações sobre outras tarefas de gestão de utilizadores disponíveis, documentação de gestão de utilizadores da [Azure AD.](../users-groups-roles/index.yml)
