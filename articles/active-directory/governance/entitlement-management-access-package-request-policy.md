---
title: Alterar as definições de pedido e aprovação para um pacote de acesso na gestão de direitos da AD Azure - Diretório Ativo Azure
description: Saiba como alterar as definições de pedido e aprovação para um pacote de acesso na gestão de direitos do Diretório Ativo Azure.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 493ba6396a7ceb11b917fbda5dd6c37c070f2fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261934"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Alterar as definições de pedido e aprovação para um pacote de acesso na gestão de direitos da AD Azure

Como gestor de pacotes de acesso, pode alterar os utilizadores que podem solicitar um pacote de acesso a qualquer momento, editando a apólice ou adicionando uma nova política. Também pode alterar as definições de aprovação.

Este artigo descreve como alterar as definições de pedido e aprovação para um pacote de acesso existente.

## <a name="choose-between-one-or-multiple-polices"></a>Escolha entre uma ou várias polícias

A forma como especifica quem pode solicitar um pacote de acesso é com uma apólice. Ao criar um pacote de acesso, especifice o pedido e a definição de aprovação que cria uma política. A maioria dos pacotes de acesso terá uma única política, mas um único pacote de acesso pode ter múltiplas políticas. Criaria múltiplas políticas para um pacote de acesso se pretendesse permitir que diferentes conjuntos de utilizadores recebessem atribuições com diferentes definições de pedido e aprovação. Por exemplo, uma única política não pode ser utilizada para atribuir aos utilizadores internos e externos o mesmo pacote de acesso. No entanto, pode criar duas políticas no mesmo pacote de acesso -- uma para utilizadores internos e outra para utilizadores externos. Se existirem múltiplas políticas aplicáveis a um utilizador, serão solicitadas no momento do seu pedido para selecionar a política a que gostariam de ser atribuídas. O diagrama seguinte mostra um pacote de acesso com duas políticas.

![Múltiplas políticas num pacote de acesso](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Cenário | Número de políticas |
| --- | --- |
| Quero que todos os utilizadores do meu diretório tenham as mesmas definições de pedido e aprovação para um pacote de acesso | Um |
| Quero que todos os utilizadores de certas organizações conectadas possam solicitar um pacote de acesso | Um |
| Quero permitir que os utilizadores do meu diretório e também utilizadores fora do meu diretório solicitem um pacote de acesso | Vários |
| Quero especificar diferentes definições de aprovação para alguns utilizadores | Vários |
| Quero que alguns utilizadores acedam a tarefas de pacotes para expirar enquanto outros utilizadores podem estender o seu acesso | Vários |

Para obter informações sobre a lógica prioritária que é usada quando se aplicam várias políticas, consulte [Múltiplas políticas.](entitlement-management-troubleshoot.md#multiple-policies
)

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Abrir uma política de definições de pedido e aprovação existentes

Para alterar as definições de pedido e aprovação para um pacote de acesso, é necessário abrir a política correspondente. Siga estas medidas para abrir as definições de pedido e aprovação para um pacote de acesso.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **Políticas** e, em seguida, clique na política que pretende editar.

    O painel de detalhes da Política abre na parte inferior da página.

    ![Pacote de acesso - Painel de detalhes de política](./media/entitlement-management-shared/policy-details.png)

1. Clique em **Editar** para editar a política.

    ![Pacote de acesso - Editar política](./media/entitlement-management-shared/policy-edit.png)

1. Clique no separador **Pedidos** para abrir as definições de pedido e aprovação.

1. Execute os passos numa das seguintes secções de pedido.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Adicione uma nova política de definições de pedido e aprovação

Se tiver um conjunto de utilizadores que deverão ter diferentes definições de pedido e aprovação, provavelmente terá de criar uma nova política. Siga estes passos para começar a adicionar uma nova política a um pacote de acesso existente.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **Políticas** **e,** em seguida, adicionar a política .

1. Digite um nome e uma descrição para a apólice.

    ![Criar política com nome e descrição](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Clique em **Seguir** para abrir o separador **Pedidos.**

1. Execute os passos numa das seguintes secções de pedido.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Se estiver a editar uma política clique em **Atualizar**. Se estiver a adicionar uma nova política, clique em **Criar**.

## <a name="next-steps"></a>Passos seguintes

- [Alterar as definições de ciclo de vida para um pacote de acesso](entitlement-management-access-package-lifecycle-policy.md)
- [Ver pedidos de um pacote de acesso](entitlement-management-access-package-requests.md)