---
title: Quickstart - Ver o acesso que um utilizador tem aos recursos da Azure - Azure RBAC
description: Neste arranque rápido, aprende-se a visualizar o acesso que um utilizador ou outro diretor de segurança tem aos recursos da Azure utilizando o portal Azure e o controlo de acesso baseado em funções Azure (Azure RBAC).
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9be53aa964e75bab0b90495640537fe927a5af0e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "82734166"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Quickstart: Ver o acesso que um utilizador tem aos recursos da Azure

Pode utilizar a lâmina de controlo de **acesso (IAM)** no controlo de acesso baseado em [funções (Azure RBAC)](overview.md) para visualizar o acesso que um utilizador ou outro diretor de segurança tem aos recursos da Azure. No entanto, por vezes basta visualizar rapidamente o acesso a um único utilizador ou a outro responsável pela segurança. A forma mais fácil de o fazer é utilizar a funcionalidade **de acesso ao Check** no portal Azure.

## <a name="view-role-assignments"></a>Ver atribuições de funções

 A forma como vê o acesso a um utilizador é listar as suas atribuições de funções. Siga estes passos para visualizar as atribuições de funções para um único utilizador, grupo, principal de serviço ou identidade gerida no âmbito de subscrição.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, **Em Assinaturas**.

1. Clique na sua assinatura.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador **De acesso a Verificação.**

    ![Controlo de acesso - Verifique o separador de acesso](./media/check-access/access-control-check-access.png)

1. Na lista **Localizar,** selecione o tipo de princípio de segurança para o qual pretende verificar o acesso.

1. Na caixa de pesquisa, introduza uma cadeia para pesquisar nomes de exibição, endereços de e-mail ou identificadores de objetos.

    ![Verifique a lista de seleção de acessos](./media/check-access/check-access-select.png)

1. Clique no principal de segurança para abrir o painel **de atribuições.**

    ![pane atribuições](./media/check-access/check-access-assignments.png)

    Neste painel, pode ver as funções atribuídas ao principal de segurança selecionado e o âmbito. Se houver alguma atribuição de negação neste âmbito ou herdada a este âmbito, serão listadas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Conceder acesso a um utilizador aos recursos do Azure através do portal Azure](quickstart-assign-role-user-portal.md)
