---
title: Delegar governança de acesso para acessar gerenciadores de pacotes no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba como delegar a governança de acesso dos administradores de ti para acessar gerenciadores de pacotes e gerentes de projeto para que eles possam gerenciar o acesso em si.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: efdc61b82a19cc5d370d6069e8c9dcd3ce5e8ae5
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174368"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Delegar governança de acesso para acessar gerenciadores de pacotes no gerenciamento de direitos do Azure AD

Para delegar a criação e o gerenciamento de pacotes de acesso em um catálogo, você adiciona usuários à função Gerenciador de pacotes de acesso. Os gerenciadores de pacotes do Access devem estar familiarizados com a necessidade de os usuários solicitarem acesso a recursos em um catálogo. Por exemplo, se um catálogo for usado para um projeto, um líder de projeto poderá ser um Gerenciador de pacotes de acesso para esse catálogo.  Os gerenciadores de pacotes do Access não podem adicionar recursos a um catálogo, mas podem gerenciar os pacotes e as políticas de acesso em um catálogo.  Ao delegar a um Gerenciador de pacotes do Access, essa pessoa pode ser responsável por:

- Quais funções um usuário terá para os recursos em um catálogo
- Quem precisará de acesso
- Quem precisa aprovar as solicitações de acesso
- Por quanto tempo o projeto durará

Este vídeo fornece uma visão geral de como delegar a governança de acesso do proprietário do catálogo para acessar o Gerenciador de pacotes.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Como proprietário do catálogo, delegar a um Gerenciador de pacotes do Access

Siga estas etapas para atribuir um usuário à função de Gerenciador de pacotes do Access:

**Função de pré-requisito:** Administrador global, administrador de usuário ou proprietário do catálogo

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo ao qual você deseja adicionar administradores.

1. No menu à esquerda, clique em **funções e administradores**.

    ![Cataloga funções e administradores](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Clique em **Adicionar gerenciadores de pacotes de acesso** para selecionar os membros para essas funções.

1. Clique em **selecionar** para adicionar esses membros.

## <a name="remove-an-access-package-manager"></a>Remover um Gerenciador de pacotes de acesso

Siga estas etapas para remover um usuário da função Gerenciador de pacotes do Access:

**Função de pré-requisito:** Administrador global, administrador de usuário ou proprietário do catálogo

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo ao qual você deseja adicionar administradores.

1. No menu à esquerda, clique em **funções e administradores**.

1. Adicione uma marca de seleção ao lado de um Gerenciador de pacotes do Access que você deseja remover.

1. Clique em **remover**.

## <a name="next-steps"></a>Passos seguintes

- [Criar um novo pacote de acesso](entitlement-management-access-package-create.md)
