---
title: Vista de negar atribuições para recursos do Azure no portal do Azure | Documentos da Microsoft
description: Saiba como ver os utilizadores, grupos, principais de serviço e de identidades geridas que quais foi negadas o acesso a ações de recursos do Azure específica num âmbito específico com o portal do Azure.
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
ms.date: 03/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2dcbcbec9054b31312043ef6642f59fa64728b30
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005796"
---
# <a name="view-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Vista de negar atribuições para recursos do Azure no portal do Azure

[Negar atribuições](deny-assignments.md) impedir que os utilizadores a efetuar ações de recursos do Azure específica, mesmo se uma atribuição de função lhes concede acesso. Este artigo descreve como utilizar o portal do Azure para ver as atribuições de negação.

> [!NOTE]
> Neste momento, a única maneira que pode adicionar seus próprios negar atribuições é através da utilização do Azure esquemas. Para obter mais informações, consulte [proteger os recursos novos com bloqueios de recursos do Azure esquemas](../governance/blueprints/tutorials/protect-new-resources.md).

## <a name="prerequisites"></a>Pré-requisitos

Para obter informações sobre uma atribuição de negar, tem de ter:

- `Microsoft.Authorization/denyAssignments/read` permissão, o que está incluído na maioria [funções incorporadas para recursos do Azure](built-in-roles.md).

## <a name="view-deny-assignments"></a>Ver atribuições de negação

Siga estes passos para ver negar atribuições no âmbito de grupo de subscrição ou gestão.

1. No portal do Azure, clique em **todos os serviços** e, em seguida **grupos de gestão** ou **subscrições**.

1. Clique no grupo de gestão ou a subscrição que pretende ver.

1. Clique em **controlo de acesso (IAM)**.

1. Clique nas **negar atribuições** separador (ou clique no **vista** botão na exibição negar atribuições de mosaico).

    Se existirem quaisquer negar atribuições neste âmbito ou herdadas para este âmbito, estes serão apresentados.

    ![Controlo de acesso – separador atribuições de negação](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Para apresentar as colunas adicionais, clique em **Edit Columns**.

    ![Negar atribuições - colunas](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Nome** | Nome da atribuição de negação. |
    | **Tipo de principal** | Utilizador, grupo, grupo definido pelo sistema ou principal de serviço. |
    | **Negado**  | Nome do principal de segurança que está incluído na atribuição de negação. |
    | **Id** | Identificador exclusivo para a atribuição de negação. |
    | **Principais excluídos** | Se existem entidades de segurança que são excluídas a atribuição de negação. |
    | **Não é aplicável a elementos subordinados** | Se a atribuição de negação é herdada para subscopes. |
    | **Sistema protegido** | Se a atribuição de negação é gerida pelo Azure. Atualmente, sempre Sim. |
    | **Âmbito** | Grupo de gestão, subscrição, grupo de recursos ou recurso. |

1. Adicionar uma marca de verificação para qualquer um dos itens ativados e, em seguida, clique em **OK** para apresentar as colunas selecionadas.

## <a name="view-details-about-a-deny-assignment"></a>Ver detalhes sobre uma atribuição de negação

Siga estes passos para ver detalhes adicionais sobre uma atribuição de negação.

1. Abra o **negar atribuições** painel, conforme descrito na secção anterior.

1. Clique no nome da atribuição de negação para abrir o **utilizadores** painel.

    ![Negar atribuição - utilizadores](./media/deny-assignments-portal/deny-assignment-users.png)

    O **utilizadores** painel inclui as duas secções seguintes.

    |  |  |
    | --- | --- |
    | **Negar a atribuição aplica-se a**  | Entidades de segurança que se aplica a atribuição de negação. |
    | **Negar a exclui da atribuição** | Entidades de segurança que são excluídas a atribuição de negação. |

    **Definido pelo sistema Principal** representa todos os utilizadores, grupos, os principais de serviço e identidades geridas num diretório do Azure AD.

1. Para ver uma lista das permissões que são negado, clique em **negado permissões**.

    ![Negar atribuição - permissões negado](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Tipo de ação | Descrição |
    | --- | --- |
    | **Ações**  | Negado operações de gestão. |
    | **NotActions** | Operações de gestão excluídas da operação de gestão negado. |
    | **DataActions**  | Negado operações de dados. |
    | **NotDataActions** | Operações de dados excluídas negado a operação de dados. |

    No exemplo mostrado na captura de ecrã anterior, seguem-se as permissões efetivas:

    - Armazenamento de todas as operações no plano de dados serão negadas, exceto para operações de computação.

1. Para ver as propriedades de uma atribuição de negar, clique em **propriedades**.

    ![Negar atribuição - propriedades](./media/deny-assignments-portal/deny-assignment-properties.png)

    Sobre o **propriedades** painel, pode ver o nome da atribuição de negar, o ID, a descrição e o âmbito. O **não se aplica aos filhos** comutador indica se a atribuição de negação é herdada para subscopes. O **sistema protegido** comutador indica se negar atribuição é gerenciada pelo Azure. Atualmente, isto é **Sim** em todos os casos.

## <a name="next-steps"></a>Passos Seguintes

* [Compreender negar atribuições para recursos do Azure](deny-assignments.md)
* [Lista de atribuições para recursos do Azure com a API REST de negação](deny-assignments-rest.md)
