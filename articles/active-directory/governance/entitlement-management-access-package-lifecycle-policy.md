---
title: Alterar as configurações do ciclo de vida de um pacote de acesso no gerenciamento de direitos do Azure AD (versão prévia)-Azure Active Directory
description: Saiba como alterar as configurações do ciclo de vida de um pacote do Access no gerenciamento de direitos Azure Active Directory (versão prévia).
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
ms.openlocfilehash: 46d63a12443edf04db3570fa43fbd8f20619122e
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392348"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Alterar as configurações do ciclo de vida de um pacote de acesso no gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> O gerenciamento de direitos do Azure Active Directory (AD do Azure) está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Como um Gerenciador de pacotes do Access, você pode alterar as configurações do ciclo de vida de um pacote do Access a qualquer momento editando uma política existente. Se você alterar a data de validade de uma política, a data de expiração das solicitações que já estão em um estado de aprovação pendente ou aprovado não será alterada.

Este artigo descreve como alterar as configurações de ciclo de vida de um pacote do Access existente.

## <a name="open-lifecycle-settings"></a>Abrir configurações do ciclo de vida

Para alterar as configurações do ciclo de vida de um pacote do Access, você precisa abrir a política correspondente. Siga estas etapas para abrir as configurações de ciclo de vida de um pacote do Access.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **políticas** e, em seguida, clique na política que tem as configurações de ciclo de vida que você deseja editar.

    O painel detalhes da política é aberto na parte inferior da página.

    ![Pacote de acesso-painel detalhes da política](./media/entitlement-management-shared/policy-details.png)

1. Clique em **Editar** para editar a política.

    ![Pacote de acesso-editar política](./media/entitlement-management-shared/policy-edit.png)

1. Clique na guia **ciclo de vida** para abrir as configurações do ciclo de vida.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Passos seguintes

- [Alterar as configurações de solicitação e aprovação para um pacote de acesso](entitlement-management-access-package-request-policy.md)