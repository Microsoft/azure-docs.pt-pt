---
title: Alterar as definições de ciclo de vida para um pacote de acesso na gestão de direitos da AD Azure - Diretório Ativo Azure
description: Saiba como alterar as definições de ciclo de vida para um pacote de acesso na gestão de direitos do Diretório Ativo Azure.
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
ms.openlocfilehash: 959d85f496a4a573a969bf736aba137d5b86154a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261986"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Alterar as definições de ciclo de vida para um pacote de acesso na gestão de direitos da AD Azure

Como gestor de pacotes de acesso, pode alterar as definições de ciclo de vida para um pacote de acesso a qualquer momento, editando uma política existente. Se alterar a data de validade de uma apólice, a data de validade dos pedidos que já estão em aprovação pendente ou estado aprovado não se alterará.

Este artigo descreve como alterar as definições do ciclo de vida para um pacote de acesso existente.

## <a name="open-lifecycle-settings"></a>Definições de ciclo de vida abertos

Para alterar as definições do ciclo de vida para um pacote de acesso, é necessário abrir a política correspondente. Siga estes passos para abrir as definições do ciclo de vida para um pacote de acesso.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **Políticas** e, em seguida, clique na política que tem as definições de ciclo de vida que pretende editar.

    O painel de detalhes da Política abre na parte inferior da página.

    ![Pacote de acesso - Painel de detalhes de política](./media/entitlement-management-shared/policy-details.png)

1. Clique em **Editar** para editar a política.

    ![Pacote de acesso - Editar política](./media/entitlement-management-shared/policy-edit.png)

1. Clique no separador **Lifecycle** para abrir as definições do ciclo de vida.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Passos seguintes

- [Alterar as definições de pedido e aprovação para um pacote de acesso](entitlement-management-access-package-request-policy.md)