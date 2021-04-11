---
title: Lista Azure nega atribuições usando o portal Azure - Azure RBAC
description: Saiba como listar os utilizadores, grupos, princípios de serviço e identidades geridas que foram impedidas de aceder a ações específicas de recursos Azure em determinados âmbitos utilizando o portal Azure e o controlo de acesso baseado em funções Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1e644d81c2a47ec07833010ae93f4dbb0c57474b
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077844"
---
# <a name="list-azure-deny-assignments-using-the-azure-portal"></a>Lista Azure nega atribuições usando o portal Azure

[O Azure nega que as atribuições](deny-assignments.md) impeçam os utilizadores de realizarem ações específicas de recursos Azure, mesmo que uma atribuição de papel lhes conceda acesso. Este artigo descreve como listar atribuições de negação usando o portal Azure.

> [!NOTE]
> Não podes criar diretamente as tuas próprias missões de negação. Para obter informações sobre como as missões de negação são criadas, consulte [Azure negar atribuições](deny-assignments.md).

## <a name="prerequisites"></a>Pré-requisitos

Para obter informações sobre uma missão de negação, você deve ter:

- `Microsoft.Authorization/denyAssignments/read`permissão, que está incluída na maioria [dos papéis incorporados do Azure.](built-in-roles.md)

## <a name="list-deny-assignments"></a>Listar atribuições de negação

Siga estes passos para listar as atribuições de negação no âmbito do grupo de subscrição ou gestão.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, **grupos de Gestão** ou **Assinaturas**.

1. Clique no grupo de gestão ou subscrição que pretende listar.

1. Clique em **Controlo de acesso (IAM)**.

1. Clique no separador **'Negar atribuições'** (ou clique no botão **Ver** no azulejo 'Negar atribuições').

    Se houver alguma atribuição de negação neste âmbito ou herdada a este âmbito, serão listadas.

    ![Controlo de acesso - Negar separador de atribuições](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Para exibir colunas adicionais, clique **em Editar Colunas**.

    ![Missões de negação - Colunas](./media/deny-assignments-portal/deny-assignments-columns.png)

    | Coluna | Descrição  |
    | --- | --- |
    | **Nome** | O nome da missão de negação. |
    | **Tipo de principal** | Utilizador, grupo, grupo definido pelo sistema ou principal de serviço. |
    | **Negado**  | Nome do chefe de segurança que está incluído na missão de negar. |
    | **ID** | Identificador único para a missão de negar. |
    | **Principais excluídos** | Se existem princípios de segurança que estão excluídos da atribuição de negação. |
    | **Não se aplica a crianças** | Se a atribuição de negação é herdada para subscópios. |
    | **Sistema protegido** | Se a missão de negação é gerida pelo Azure. Atualmente, sempre sim. |
    | **Âmbito** | Grupo de gestão, subscrição, grupo de recursos ou recursos. |

1. Adicione uma marca de verificação a qualquer um dos itens ativados e, em seguida, clique em **OK** para visualizar as colunas selecionadas.

## <a name="list-details-about-a-deny-assignment"></a>Listar detalhes sobre uma atribuição de negação

Siga estes passos para listar detalhes adicionais sobre uma atribuição de negação.

1. Abra o painel **de atribuições Deny** como descrito na secção anterior.

1. Clique no nome de atribuição de negar para abrir a lâmina **do Utilizadores.**

    ![Atribuição de negação - Utilizadores](./media/deny-assignments-portal/deny-assignment-users.png)

    A lâmina **do Utilizadores** inclui as duas secções seguintes.

    | Negar definição  | Description |
    | --- | --- |
    | **A atribuição de negação aplica-se a**  | Princípios de segurança a que a atribuição de negação se aplica. |
    | **Negar atribuição exclui** | Princípios de segurança que estão excluídos da missão de negação. |

    **O Principal Definido pelo Sistema** representa todos os utilizadores, grupos, diretores de serviços e identidades geridas num diretório AD Azure.

1. Para ver uma lista das permissões que são negadas, clique em **Permissões Negadas**.

    ![Atribuição de negação - Permissões Negadas](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Tipo de ação | Description |
    | --- | --- |
    | **Ações**  | Negadas operações de gestão. |
    | **NotActions** | Operações de gestão excluídas da operação de gestão negada. |
    | **DataActions**  | Negadas operações de dados. |
    | **NotDataActions** | Operações de dados excluídas da operação de dados negada. |

    Para o exemplo mostrado na imagem anterior, as seguintes são as permissões eficazes:

    - Todas as operações de armazenamento no plano de dados são negadas, exceto para operações de computação.

1. Para ver as propriedades para uma atribuição de negação, clique em **Propriedades**.

    ![Atribuição de negação - Propriedades](./media/deny-assignments-portal/deny-assignment-properties.png)

    Na lâmina **propriedades,** pode ver o nome de atribuição de negação, ID, descrição e âmbito. O **Não se aplica a troca de crianças** indica se a atribuição de negação é herdada a subscópios. O **interruptor protegido do Sistema** indica se esta atribuição de negação é gerida pela Azure. Atualmente, este é **sim** em todos os casos.

## <a name="next-steps"></a>Passos seguintes

* [Compreenda que Azure nega atribuições](deny-assignments.md)
* [Lista Azure nega atribuições usando Azure PowerShell](deny-assignments-powershell.md)
