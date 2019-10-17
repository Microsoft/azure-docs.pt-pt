---
title: Alterar as configurações de solicitação e aprovação para um pacote de acesso no gerenciamento de direitos do Azure AD (versão prévia)-Azure Active Directory
description: Saiba como alterar as configurações de solicitação e aprovação de um pacote do Access no gerenciamento de direitos Azure Active Directory (versão prévia).
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
ms.openlocfilehash: 5b060a17d9e7f795b6aad7df0e3e6349bbbd81b3
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392504"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Alterar as configurações de solicitação e aprovação para um pacote de acesso no gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> O gerenciamento de direitos do Azure Active Directory (AD do Azure) está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Como um Gerenciador de pacotes do Access, você pode alterar os usuários que podem solicitar um pacote do Access a qualquer momento editando a política ou adicionando uma nova política. Você também pode alterar as configurações de aprovação.

Este artigo descreve como alterar as configurações de solicitação e aprovação para um pacote do Access existente.

## <a name="choose-between-one-or-multiple-polices"></a>Escolha entre uma ou várias políticas

A maneira de especificar quem pode solicitar um pacote de acesso é com uma política. Ao criar um pacote do Access, você especifica a configuração de solicitação e aprovação que cria uma política. A maioria dos pacotes de acesso terá uma única política, mas um único pacote de acesso pode ter várias políticas. Você criaria várias políticas para um pacote de acesso se quiser permitir que diferentes conjuntos de usuários recebam atribuições com diferentes configurações de solicitação e aprovação. Por exemplo, uma única política não pode ser usada para atribuir usuários internos e externos ao mesmo pacote de acesso. No entanto, você pode criar duas políticas no mesmo pacote de acesso, uma para usuários internos e outra para usuários externos. Se houver várias políticas que se aplicam a um usuário, elas serão solicitadas no momento da solicitação para selecionar a política à qual desejam ser atribuídos. O diagrama a seguir mostra um pacote de acesso com duas políticas.

![Várias políticas em um pacote do Access](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Cenário | Número de políticas |
| --- | --- |
| Quero que todos os usuários em meu diretório tenham as mesmas configurações de solicitação e aprovação para um pacote de acesso | Só |
| Quero que todos os usuários em determinadas organizações conectadas possam solicitar um pacote de acesso | Só |
| Quero permitir usuários em meu diretório e também usuários fora do meu diretório para solicitar um pacote de acesso | Vários |
| Desejo especificar configurações de aprovação diferentes para alguns usuários | Vários |
| Quero que alguns usuários acessem as atribuições de pacote para expirar enquanto outros usuários podem estender seu acesso | Vários |

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Abrir uma política existente de configurações de solicitação e aprovação

Para alterar as configurações de solicitação e aprovação de um pacote do Access, você precisa abrir a política correspondente. Siga estas etapas para abrir as configurações de solicitação e aprovação para um pacote do Access.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **políticas** e, em seguida, clique na política que você deseja editar.

    O painel detalhes da política é aberto na parte inferior da página.

    ![Pacote de acesso-painel detalhes da política](./media/entitlement-management-shared/policy-details.png)

1. Clique em **Editar** para editar a política.

    ![Pacote de acesso-editar política](./media/entitlement-management-shared/policy-edit.png)

1. Clique na guia **solicitações** para abrir as configurações de solicitação e aprovação.

1. Execute as etapas em uma das seções de solicitação a seguir.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Adicionar uma nova política de configurações de solicitação e aprovação

Se você tiver um conjunto de usuários que deve ter diferentes configurações de solicitação e aprovação, provavelmente precisará criar uma nova política. Siga estas etapas para começar a adicionar uma nova política a um pacote do Access existente.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **políticas** e em **Adicionar política**.

1. Digite um nome e uma descrição para a política.

    ![Criar política com nome e descrição](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Clique em **Avançar** para abrir a guia **solicitações** .

1. Execute as etapas em uma das seções de solicitação a seguir.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Se você estiver editando uma política, clique em **Atualizar**. Se você estiver adicionando uma nova política, clique em **criar**.

## <a name="next-steps"></a>Passos seguintes

- [Alterar as configurações do ciclo de vida de um pacote de acesso](entitlement-management-access-package-lifecycle-policy.md)
- [Exibir solicitações para um pacote de acesso](entitlement-management-access-package-requests.md)