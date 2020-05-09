---
title: List Azure nega atribuições usando o portal Azure - Azure RBAC
description: Saiba como listar os utilizadores, grupos, diretores de serviçoe identidades geridas que foram negados acesso a ações específicas de recursos Azure em determinados âmbitos utilizando o portal Azure e o controlo de acesso baseado em funções azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 97c03d417f8bf123de3332142344f292de00e3b2
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734115"
---
# <a name="list-azure-deny-assignments-using-the-azure-portal"></a>List Azure nega atribuições usando o portal Azure

[O Azure nega que as atribuições](deny-assignments.md) impeçam os utilizadores de realizarem ações específicas de recursos Do Azure, mesmo que uma atribuição de funções lhes conceda acesso. Este artigo descreve como listar missões de negação usando o portal Azure.

> [!NOTE]
> Não podes criar diretamente as tuas próprias missões de negação. Para obter informações sobre como as missões de negação são criadas, consulte [Azure negar atribuições.](deny-assignments.md)

## <a name="prerequisites"></a>Pré-requisitos

Para obter informações sobre uma atribuição de negação, deve ter:

- `Microsoft.Authorization/denyAssignments/read`permissão, que está incluída na maioria das [funções azure incorporadas.](built-in-roles.md)

## <a name="list-deny-assignments"></a>Listar atribuições de negação

Siga estes passos para listar a negação de atribuições no âmbito do grupo de subscrição ou de gestão.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, **grupos** de gestão ou **subscrições.**

1. Clique no grupo de gestão ou subscrição que pretende listar.

1. Clique em **Controlo de acesso (IAM)**.

1. Clique no separador **de atribuições Negar** (ou clique no botão **'Ver'** no 'Ver negar o azulejo de atribuição').

    Se houver alguma missão de negação neste âmbito ou herdada para este âmbito, serão listadas.

    ![Controlo de acesso - Negar o separador de atribuições](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Para exibir colunas adicionais, clique em **Colunas de Edição**.

    ![Negar atribuições - Colunas](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Nome** | Nome da missão de negação. |
    | **Tipo de principal** | Utilizador, grupo, grupo definido pelo sistema ou diretor de serviço. |
    | **Negado**  | Nome do diretor de segurança que está incluído na missão de negação. |
    | **Id** | Identificador único para a atribuição de negação. |
    | **Principais excluídos** | Se há diretores de segurança que estão excluídos da missão de negação. |
    | **Não se aplica a crianças** | Se a atribuição de negação é herdada para subscópios. |
    | **Sistema protegido** | Se a atribuição de negação é gerida por Azure. Atualmente, sempre sim. |
    | **Âmbito** | Grupo de gestão, subscrição, grupo de recursos ou recurso. |

1. Adicione uma marca de verificação a qualquer um dos itens ativados e, em seguida, clique em **OK** para exibir as colunas selecionadas.

## <a name="list-details-about-a-deny-assignment"></a>Lista rés em detalhes sobre uma atribuição de negação

Siga estes passos para listar detalhes adicionais sobre uma atribuição de negação.

1. Abra o painel de **atribuições Deny** como descrito na secção anterior.

1. Clique no nome de atribuição de negação para abrir a lâmina **utilizadores.**

    ![Negar a atribuição - Utilizadores](./media/deny-assignments-portal/deny-assignment-users.png)

    A lâmina **utilizadores** inclui as duas secções seguintes.

    |  |  |
    | --- | --- |
    | **Negar atribuição aplica-se a**  | Diretores de segurança a que a atribuição de negação se aplica. |
    | **Negar atribuição exclui** | Diretores de segurança que estão excluídos da missão de negação. |

    **O Diretor Definido pelo Sistema** representa todos os utilizadores, grupos, diretores de serviçoe identidades geridas num diretório Azure AD.

1. Para ver uma lista das permissões que são negadas, clique em **Permissões Negadas**.

    ![Negar a atribuição - Permissões Negadas](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Tipo de ação | Descrição |
    | --- | --- |
    | **Actions (Ações)**  | Operações de gestão negadas. |
    | **NotActions** | Operações de gestão excluídas da operação de gestão negada. |
    | **Ações de Dados**  | Operações de dados negadas. |
    | **NotDataActions** | Operações de dados excluídas da operação de dados negados. |

    Para o exemplo mostrado na imagem anterior, as seguintes são as permissões eficazes:

    - Todas as operações de armazenamento no avião de dados são negadas, exceto para operações de computação.

1. Para ver as propriedades para uma atribuição de negação, clique em **Propriedades**.

    ![Atribuição de negação - Propriedades](./media/deny-assignments-portal/deny-assignment-properties.png)

    Na lâmina **Propriedades,** pode ver o nome de atribuição de negação, id, descrição e âmbito. O Não se aplica à troca de **crianças** indica se a atribuição de negação é herdada em subscópios. O interruptor **protegido pelo Sistema** indica se esta atribuição de negação é gerida pelo Azure. Atualmente, isto é **Sim** em todos os casos.

## <a name="next-steps"></a>Passos seguintes

* [Entenda Azure negar atribuições](deny-assignments.md)
* [List Azure nega atribuições usando Azure PowerShell](deny-assignments-powershell.md)
