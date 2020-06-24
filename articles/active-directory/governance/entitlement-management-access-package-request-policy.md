---
title: Alterar as definições de pedido e aprovação para um pacote de acesso na gestão de direitos Azure AD - Azure Ative Directory
description: Saiba como alterar as definições de pedido e aprovação para um pacote de acesso na gestão de direitos do Azure Ative Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ef2faf2a1d1a131dc5f2a01d3fa46cc61a06fb6
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85078807"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Alterar as definições de pedido e aprovação para um pacote de acesso na gestão de direitos AD da Azure

Como gestor de pacotes de acesso, pode alterar os utilizadores que podem solicitar um pacote de acesso a qualquer momento, editando a política ou adicionando uma nova política. Também pode alterar as definições de aprovação.

Este artigo descreve como alterar as definições de pedido e aprovação para um pacote de acesso existente.

## <a name="choose-between-one-or-multiple-polices"></a>Escolha entre uma ou várias polícias

A forma como especifica quem pode solicitar um pacote de acesso é com uma apólice. Ao criar um pacote de acesso, especifique a definição de pedido e aprovação que cria uma política. A maioria dos pacotes de acesso terá uma única política, mas um único pacote de acesso pode ter várias políticas. Criaria várias políticas para um pacote de acesso se quiser permitir que diferentes conjuntos de utilizadores recebam atribuições com diferentes configurações de pedido e aprovação. Por exemplo, não se pode utilizar uma única política para atribuir utilizadores internos e externos ao mesmo pacote de acesso. No entanto, pode criar duas políticas no mesmo pacote de acesso -- uma para utilizadores internos e outra para utilizadores externos. Se existirem múltiplas políticas aplicáveis a um utilizador, estas serão solicitadas no momento do seu pedido para selecionar a política a que gostariam de ser atribuídas. O diagrama que se segue mostra um pacote de acesso com duas políticas.

![Múltiplas políticas num pacote de acesso](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Cenário | Número de políticas |
| --- | --- |
| Quero que todos os utilizadores do meu diretório tenham as mesmas definições de pedido e aprovação para um pacote de acesso | Um |
| Quero que todos os utilizadores de certas organizações conectadas possam solicitar um pacote de acesso | Um |
| Quero permitir que os utilizadores do meu diretório e também utilizadores fora do meu diretório solicitem um pacote de acesso | Vários |
| Quero especificar diferentes definições de aprovação para alguns utilizadores | Vários |
| Quero que alguns utilizadores acedam a atribuição de pacotes enquanto outros utilizadores podem alargar o seu acesso | Vários |

Para obter informações sobre a lógica prioritária que é utilizada quando se aplicam várias políticas, consulte [várias políticas](entitlement-management-troubleshoot.md#multiple-policies
).

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Abrir uma política existente de definições de pedido e aprovação

Para alterar as definições de pedido e aprovação de um pacote de acesso, é necessário abrir a política correspondente. Siga estes passos para abrir as definições de pedido e aprovação para um pacote de acesso.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo ou gestor de pacotes access

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de Acesso** e, em seguida, abra o pacote de acesso.

1. Clique em **Políticas** e, em seguida, clique na política que pretende editar.

    O painel de detalhes da Política abre na parte inferior da página.

    ![Pacote de acesso - Painel de detalhes de política](./media/entitlement-management-shared/policy-details.png)

1. Clique **em Editar** para editar a política.

    ![Pacote de acesso - Política de edição](./media/entitlement-management-shared/policy-edit.png)

1. Clique no separador **Pedidos** para abrir as definições de pedido e aprovação.

1. Execute os passos numa das seguintes secções de pedido.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Adicione uma nova política de definições de pedido e aprovação

Se tiver um conjunto de utilizadores que deverão ter diferentes configurações de pedido e aprovação, provavelmente terá de criar uma nova política. Siga estes passos para começar a adicionar uma nova política a um pacote de acesso existente.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo ou gestor de pacotes access

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de Acesso** e, em seguida, abra o pacote de acesso.

1. Clique **em Políticas** e, em seguida, Adicione a **política**.

1. Escreva um nome e uma descrição para a apólice.

    ![Criar política com nome e descrição](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Clique **ao lado** para abrir o separador **Pedidos.**

1. Execute os passos numa das seguintes secções de pedido.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Se estiver a editar uma política clique em **Update**. Se estiver a adicionar uma nova política, clique em **Criar**.

## <a name="next-steps"></a>Passos seguintes

- [Alterar as definições do ciclo de vida para um pacote de acesso](entitlement-management-access-package-lifecycle-policy.md)
- [Ver pedidos de um pacote de acesso](entitlement-management-access-package-requests.md)