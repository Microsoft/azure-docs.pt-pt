---
title: Delegar governação de acesso aos criadores de catálogos na gestão de direitos Azure AD - Azure Ative Directory
description: Saiba como delegar a governação de acesso de administradores de TI a criadores de catálogos e gestores de projetos para que possam gerir o acesso a si mesmos.
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
ms.openlocfilehash: e41f372cd0105896765d5a267456b6bda767dd4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798431"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Delegar governação de acesso a criadores de catálogos na gestão de direitos da Ad Azure

Um catálogo é um contentor de recursos e pacotes de acesso. Cria um catálogo quando pretende agrupar recursos relacionados e aceder a pacotes. Por padrão, um administrador Global ou um administrador do Utilizador pode [criar um catálogo](entitlement-management-catalog-create.md), e pode adicionar utilizadores adicionais como proprietários de catálogos.

Para delegar utilizadores que não são administradores, para que possam criar os seus próprios catálogos, pode adicionar esses utilizadores à função de criador de catálogos definidos pela Azure AD. Pode adicionar utilizadores individuais, ou pode adicionar um grupo, cujos membros são capazes de criar catálogos.  Depois de criarem um catálogo, podem posteriormente adicionar recursos que possuem ao seu catálogo.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Como administrador de TI, delegado a um criador de catálogos

Siga estes passos para atribuir um utilizador à função de criador do catálogo.

**Papel pré-requisito:** Administrador global ou administrador de utilizadores

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, na secção **de gestão de** **direitos,** clique em Definições .

1. Clique em **Editar**.

    ![Configurações para adicionar criadores de catálogos](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. Na secção **de gestão de direitos de delegado,** clique em **Adicionar criadores de catálogos** para selecionar os utilizadores ou grupos a que pretende delegar esta função de gestão de direitos.

1. Clique em **Selecionar**.

1. Clique em **Guardar**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Permitir funções delegadas para aceder ao portal Azure

Para permitir que funções delegadas, como criadores de catálogos e gestores de pacotes de acesso, acedam ao portal Azure para gerir pacotes de acesso, deve verificar a definição do portal da administração.

**Papel pré-requisito:** Administrador global ou administrador de utilizadores

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Utilizadores**.

1. No menu esquerdo, clique nas **definições do Utilizador**.

1. Certifique-se de que **o acesso restrito ao portal de administração AD Azure** está definido para **nº**.

    ![Definições de utilizador Azure AD - Portal da administração](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Passos seguintes

- [Criar e gerir um catálogo de recursos](entitlement-management-catalog-create.md)
- [Delegar governação de acesso a gestores de pacotes de acesso](entitlement-management-delegate-managers.md)

