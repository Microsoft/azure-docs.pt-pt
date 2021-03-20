---
title: Delegar governação de acesso a gestores de pacotes de acesso na gestão de direitos Azure AD - Azure Ative Directory
description: Saiba como delegar a governação de acesso dos administradores de TI para aceder a gestores de pacotes e gestores de projetos para que eles possam gerir o acesso a si mesmos.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e81389ec953829115062a31a019caf040850cbea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87798647"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Delegar governação de acesso a gestores de pacotes de acesso na gestão de direitos Azure AD

Para delegar a criação e gestão de pacotes de acesso num catálogo, adicione os utilizadores à função de gestor de pacotes de acesso. Os gestores de pacotes de acesso devem estar familiarizados com a necessidade de os utilizadores solicitarem o acesso aos recursos num catálogo. Por exemplo, se um catálogo for usado para um projeto, então um líder de projeto pode ser um gestor de pacotes de acesso para esse catálogo.  Os gestores de pacotes de acesso não podem adicionar recursos a um catálogo, mas podem gerir os pacotes e políticas de acesso num catálogo.  Ao delegar a um gestor de pacotes de acesso, essa pessoa pode então ser responsável por:

- Que funções um utilizador terá para os recursos de um catálogo
- Quem vai precisar de acesso
- Quem precisa aprovar os pedidos de acesso
- Quanto tempo o projeto vai durar

Este vídeo fornece uma visão geral de como delegar a governação de acesso do proprietário do catálogo para aceder ao gestor de pacotes.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Como proprietário de catálogo, delegado a um gestor de pacotes de acesso

Siga estes passos para atribuir um utilizador à função de gestor de pacotes de acesso:

**Papel pré-requisito:** Administrador global, administrador de utilizadores ou proprietário do catálogo

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Catálogos** e, em seguida, abra o catálogo a que pretende adicionar administradores.

1. No menu esquerdo, clique em **Funções e administradores.**

    ![Funções de catálogos e administradores](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Clique **em Adicionar gestores de pacotes de acesso** para selecionar os membros para estas funções.

1. Clique **em Selecionar** para adicionar estes membros.

## <a name="remove-an-access-package-manager"></a>Remover um gestor de pacotes de acesso

Siga estes passos para remover um utilizador da função de gestor de pacotes de acesso:

**Papel pré-requisito:** Administrador global, administrador de utilizadores ou proprietário do catálogo

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Catálogos** e, em seguida, abra o catálogo a que pretende adicionar administradores.

1. No menu esquerdo, clique em **Funções e administradores.**

1. Adicione uma marca de verificação ao lado de um gestor de pacotes de acesso que pretende remover.

1. Clique em **Remover**.

## <a name="next-steps"></a>Passos seguintes

- [Criar um novo pacote de acesso](entitlement-management-access-package-create.md)
