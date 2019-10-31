---
title: Delegar governança de acesso para criadores de catálogo no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba como delegar a governança de acesso dos administradores de ti aos criadores de catálogo e aos gerentes de projeto para que eles possam gerenciar o acesso.
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: f71007b886d3cc25a7cf9dc23d784144ed4e1fbd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174371"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Delegar governança de acesso para criadores de catálogo no gerenciamento de direitos do Azure AD

Para delegar a usuários que não são administradores, para que eles possam criar seus próprios catálogos, você pode adicionar esses usuários à função de criador de catálogo definida pelo gerenciamento de direitos do Azure AD. Você pode adicionar usuários individuais ou pode adicionar um grupo, cujos membros são então capazes de criar catálogos.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Como um administrador de ti, delegado a um criador de catálogo

Siga estas etapas para atribuir um usuário à função de criador de catálogo.

**Função de pré-requisito:** Administrador global ou administrador de usuários

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, na seção **Gerenciamento de direitos** , clique em **configurações**.

1. Clique em **Editar**.

    ![Configurações para adicionar criadores de catálogo](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. Na seção **Gerenciamento de direitos de representante** , clique em **Adicionar criadores de catálogo** para selecionar os usuários ou grupos aos quais você deseja delegar essa função de gerenciamento de direitos.

1. Clique em **Selecionar**.

1. Clique em **Guardar**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Permitir que funções delegadas acessem o portal do Azure

Para permitir que funções delegadas, como criadores de catálogo e gerenciadores de pacotes de acesso, acessem o portal do Azure para gerenciar pacotes de acesso, você deve verificar a configuração do portal de administração.

**Função de pré-requisito:** Administrador global ou administrador de usuários

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **usuários**.

1. No menu à esquerda, clique em **configurações do usuário**.

1. Certifique-se de que **restringir o acesso ao portal de administração do Azure ad** esteja definido como **não**.

    ![Configurações de usuário do Azure AD-portal de administração](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Passos seguintes

- [Criar e gerenciar um catálogo de recursos](entitlement-management-catalog-create.md)
- [Delegar governança de acesso para acessar gerenciadores de pacotes](entitlement-management-delegate-managers.md)

