---
title: Início rápido-exibir o acesso que um usuário tem aos recursos do Azure
description: Neste guia de início rápido, saiba como exibir o acesso que um usuário ou outra entidade de segurança tem aos recursos do Azure usando o RBAC (controle de acesso baseado em função) e o portal do Azure.
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
ms.openlocfilehash: b23c10fc2a551b8044b208911dbc048968b06564
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419613"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Início rápido: exibir o acesso que um usuário tem aos recursos do Azure

Você pode usar a folha **controle de acesso (iam)** no [controle de acesso baseado em função (RBAC)](overview.md) para exibir o acesso que um usuário ou outra entidade de segurança tem aos recursos do Azure. No entanto, às vezes você só precisa exibir rapidamente o acesso para um único usuário ou outra entidade de segurança. A maneira mais fácil de fazer isso é usar o recurso **verificar acesso** no portal do Azure.

## <a name="view-role-assignments"></a>Exibir atribuições de função

 A maneira de exibir o acesso de um usuário é listar suas atribuições de funções. Siga estas etapas para exibir as atribuições de função para um único usuário, grupo, entidade de serviço ou identidade gerenciada no escopo da assinatura.

1. No portal do Azure, clique em **todos os serviços** e em **assinaturas**.

1. Clique em sua assinatura.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique na guia **verificar acesso** .

    ![Controle de acesso – guia acesso de verificação](./media/check-access/access-control-check-access.png)

1. Na lista **Localizar** , selecione o tipo de entidade de segurança para a qual você deseja verificar o acesso.

1. Na caixa de pesquisa, insira uma cadeia de caracteres para pesquisar o diretório em busca de nomes de exibição, endereços de email ou identificadores de objeto.

    ![Marcar lista de seleção de acesso](./media/check-access/check-access-select.png)

1. Clique na entidade de segurança para abrir o painel **atribuições** .

    ![painel atribuições](./media/check-access/check-access-assignments.png)

    Nesse painel, você pode ver as funções atribuídas à entidade de segurança selecionada e ao escopo. Se houver quaisquer atribuições de negação neste escopo ou herdadas para esse escopo, elas serão listadas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: conceder a um usuário acesso aos recursos do Azure usando o RBAC e o portal do Azure](quickstart-assign-role-user-portal.md)
