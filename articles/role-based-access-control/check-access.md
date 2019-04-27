---
title: Início rápido - vista o acesso a um utilizador tem de recursos do Azure | Documentos da Microsoft
description: Saiba como ver o acesso um utilizador ou de outra entidade de segurança tem de recursos do Azure com o controlo de acesso baseado em funções (RBAC) e o portal do Azure.
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
ms.openlocfilehash: f388215b2829066906ee7faf41abb17307bf3fff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782352"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Início rápido: Ver o acesso que um utilizador tem de recursos do Azure

Pode utilizar o **controlo de acesso (IAM)** painel no [controlo de acesso baseado em funções (RBAC)](overview.md) para ver o acesso um utilizador ou de outra entidade de segurança tem de recursos do Azure. No entanto, algumas vezes é preciso ver rapidamente o acesso a um único utilizador ou de outra entidade de segurança. A maneira mais fácil de fazê-lo é utilizar o **verificar acesso** recurso no portal do Azure.

## <a name="view-role-assignments"></a>Ver atribuições de funções

 A maneira como exibir o acesso para um utilizador é listar as suas atribuições de funções. Siga estes passos para ver as atribuições de funções para um único utilizador, grupo, principal de serviço ou uma identidade gerida no âmbito da subscrição.

1. No portal do Azure, clique em **todos os serviços** e, em seguida **subscrições**.

1. Clique na sua subscrição.

1. Clique em **controlo de acesso (IAM)**.

1. Clique nas **verificar acesso** separador.

    ![Controlo de acesso - separador de acesso de verificação](./media/check-access/access-control-check-access.png)

1. Na **encontrar** , selecione o tipo de entidade de segurança que pretende verificar o acesso.

1. Na caixa de pesquisa, introduza uma cadeia de caracteres para procurar o diretório para os nomes a apresentar, endereços de e-mail ou identificadores de objetos.

    ![Verifique a lista de seleção de acesso](./media/check-access/check-access-select.png)

1. Clique a entidade de segurança para abrir o **atribuições** painel.

    ![Painel de atribuições](./media/check-access/check-access-assignments.png)

    Neste painel, pode ver as funções atribuídas para a entidade de segurança selecionado e o escopo. Se existirem quaisquer negar atribuições neste âmbito ou herdadas para este âmbito, estes serão apresentados.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Conceder um acesso de utilizador aos recursos do Azure utilizando o RBAC e o portal do Azure](quickstart-assign-role-user-portal.md)
