---
title: Delegar governação de acesso a criadores de catálogos na gestão de direitos da AD Azure - Diretório Ativo Azure
description: Aprenda a delegar a governação de acesso dos administradores de TI para catalogar criadores e gestores de projetos para que eles possam gerir o acesso a si mesmos.
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
ms.openlocfilehash: 51e37ad006da5042291614c773f242b5a1f3be97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120191"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Delegar governação de acesso a criadores de catálogos na gestão de direitos da AD Azure

Um catálogo é um recipiente de recursos e pacotes de acesso. Cria um catálogo quando pretende agrupar recursos relacionados e pacotes de acesso. Por padrão, um administrador global ou um administrador de utilizador podem [criar um catálogo,](entitlement-management-catalog-create.md)e podem adicionar utilizadores adicionais como proprietários de catálogos.

Para delegar utilizadores que não são administradores, para que possam criar os seus próprios catálogos, pode adicionar esses utilizadores à função de criador de catálogo definida pela Azure AD. Pode adicionar utilizadores individuais, ou pode adicionar um grupo, cujos membros são então capazes de criar catálogos.  Depois de criarem um catálogo, podem posteriormente adicionar recursos que possuem ao seu catálogo.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Como administrador de TI, delegado a um criador de catálogo

Siga estes passos para atribuir um utilizador ao papel de criador do catálogo.

**Papel pré-requisito:** Administrador global ou administrador de utilizador

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, na secção **de gestão** do Direito, clique em **Definições**.

1. Clique em **Editar**.

    ![Definições para adicionar criadores de catálogo](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. Na secção de gestão de **direitos do Delegado,** clique em **Adicionar criadores** de catálogo para selecionar os utilizadores ou grupos aos quais pretende delegar esta função de gestão de direitos.

1. Clique em **Selecionar**.

1. Clique em **Guardar**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Permitir funções delegadas para aceder ao portal Azure

Para permitir que papéis delegados, como criadores de catálogos e gestores de pacotes de acesso, acedam ao portal Azure para gerir pacotes de acesso, deverá verificar a definição do portal da administração.

**Papel pré-requisito:** Administrador global ou administrador de utilizador

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Utilizadores**.

1. No menu esquerdo, clique nas **definições do Utilizador**.

1. Certifique-se de restringir o acesso ao portal de **administração da AD Azure** definido para **O**.

    ![Definições de utilizadores da AD Azure - Portal de administração](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Passos seguintes

- [Criar e gerir um catálogo de recursos](entitlement-management-catalog-create.md)
- [Delegar governação de acesso aos gestores de pacotes de acesso](entitlement-management-delegate-managers.md)

