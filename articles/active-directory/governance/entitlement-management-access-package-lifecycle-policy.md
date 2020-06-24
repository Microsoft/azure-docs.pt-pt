---
title: Alterar as definições do ciclo de vida para um pacote de acesso na gestão de direitos Azure AD - Azure Ative Directory
description: Saiba como alterar as definições do ciclo de vida para um pacote de acesso na gestão de direitos do Azure Ative Directory.
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
ms.openlocfilehash: 538ed31c1f3bbca8c2151a6ac0d2a3ad561bc6a7
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85078779"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Alterar as definições do ciclo de vida para um pacote de acesso na gestão de direitos Ad Azure

Como gestor de pacotes de acesso, pode alterar as definições do ciclo de vida para um pacote de acesso a qualquer momento, editando uma política existente. Se alterar a data de validade de uma apólice, a data de validade dos pedidos que já se encontram em aprovação pendente ou estado aprovado não será alterada.

Este artigo descreve como alterar as definições do ciclo de vida para um pacote de acesso existente.

## <a name="open-lifecycle-settings"></a>Definições de ciclo de vida aberto

Para alterar as definições do ciclo de vida para um pacote de acesso, é necessário abrir a política correspondente. Siga estes passos para abrir as definições do ciclo de vida para um pacote de acesso.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo ou gestor de pacotes access

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de Acesso** e, em seguida, abra o pacote de acesso.

1. Clique em **Políticas** e, em seguida, clique na política que tem as definições do ciclo de vida que pretende editar.

    O painel de detalhes da Política abre na parte inferior da página.

    ![Pacote de acesso - Painel de detalhes de política](./media/entitlement-management-shared/policy-details.png)

1. Clique **em Editar** para editar a política.

    ![Pacote de acesso - Política de edição](./media/entitlement-management-shared/policy-edit.png)

1. Clique no **separador Ciclo de Vida** para abrir as definições do ciclo de vida.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Passos seguintes

- [Alterar as definições de pedido e aprovação para um pacote de acesso](entitlement-management-access-package-request-policy.md)