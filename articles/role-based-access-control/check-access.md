---
title: Quickstart - Verifique o acesso de um utilizador aos recursos da Azure - Azure RBAC
description: Neste arranque rápido, você aprende a verificar o acesso por si ou por outro utilizador aos recursos da Azure utilizando o portal Azure e o controlo de acesso baseado em funções Azure (Azure RBAC).
services: role-based-access-control
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: quickstart
ms.workload: identity
ms.date: 12/09/2020
ms.author: rolyon
ms.custom: contperf-fy21q2
ms.openlocfilehash: 7cf4020ad38224b25ea8bb7dc7f0fdea7dd6f3b1
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97034008"
---
# <a name="quickstart-check-access-for-a-user-to-azure-resources"></a>Quickstart: Verifique o acesso de um utilizador aos recursos da Azure

Por vezes é necessário verificar o acesso que um utilizador tem a um conjunto de recursos Azure. Verifica-se o acesso deles, enumerando as suas atribuições. Uma forma rápida de verificar o acesso de um único utilizador é utilizar a funcionalidade de acesso ao Controlo de **Verificação** na página **do controlo de acesso (IAM).**

## <a name="step-1-open-the-azure-resources"></a>Passo 1: Abra os recursos do Azure

Para verificar o acesso a um utilizador, primeiro tem de abrir os recursos Azure que pretende verificar o acesso. Os recursos azuis são organizados em níveis que são tipicamente chamados de *âmbito.* Em Azure, pode especificar um âmbito a quatro níveis de ampla a estreita: grupo de gestão, subscrição, grupo de recursos e recursos.

![Níveis de âmbito para Azure RBAC](../../includes/role-based-access-control/media/scope-levels.png)

Siga estes passos para abrir o conjunto de recursos Azure para os que pretende verificar o acesso.

1. Abra o [portal do Azure](https://portal.azure.com).

1. Abra o conjunto de recursos Azure, tais como grupos de **Gestão,** **Assinaturas, Grupos** **de Recursos** ou um recurso específico.

1. Clique no recurso específico nesse âmbito.

    O seguinte mostra um grupo de recursos exemplo.

    ![Visão geral do grupo de recursos](./media/check-access/rg-overview.png)

## <a name="step-2-check-access-for-a-user"></a>Passo 2: Verificar o acesso de um utilizador

Siga estes passos para verificar o acesso de um único utilizador, grupo, principal de serviço ou identidade gerida aos recursos Azure previamente selecionados.

1. Clique em **Controlo de acesso (IAM)**.

    O seguinte mostra um exemplo da página access control (IAM) para um grupo de recursos.

    ![Controlo de acesso a grupos de recursos - Verifique o separador de acesso](./media/check-access/rg-access-control.png)

1. No separador **de acesso a Verificação,** na lista **'Localizar',** selecione o utilizador, grupo, principal de serviço ou identidade gerida que pretende verificar o acesso.

1. Na caixa de pesquisa, introduza uma cadeia para pesquisar nomes de exibição, endereços de e-mail ou identificadores de objetos.

    ![Verifique a lista de seleção de acessos](./media/shared/rg-check-access-select.png)

1. Clique no principal de segurança para abrir o painel **de atribuições.**

    Neste painel, pode ver o acesso ao principal de segurança selecionado neste âmbito e herdado a este âmbito. As atribuições em âmbitos infantis não estão listadas. Vê as seguintes atribuições:

    - Atribuições de funções adicionadas com Azure RBAC.
    - Nego atribuições adicionadas usando a Azure Blueprints ou aplicações geridas pela Azure.
    - Administrador de serviço clássico ou Co-Administrator atribuições para implementações clássicas. 

    ![Papel e negar atribuições para um utilizador](./media/shared/rg-check-access-assignments-user.png)

## <a name="step-3-check-your-access"></a>Passo 3: Verifique o seu acesso

Siga estes passos para verificar o seu acesso aos recursos Azure previamente selecionados.

1. Clique em **Controlo de acesso (IAM)**.

1. No **separador de acesso do Cheque,** clique no botão **Ver o meu acesso.**

    Aparece um painel de atribuições que lista o seu acesso neste âmbito e é herdado a este âmbito. As atribuições em âmbitos infantis não estão listadas.

    ![Papel e negar atribuições painel](./media/check-access/rg-check-access-assignments.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Liste atribuições de funções Azure usando o portal Azure](role-assignments-list-portal.md)
