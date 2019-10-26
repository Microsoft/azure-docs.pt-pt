---
title: Requisitos de licença para usar o Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Descreve os requisitos de licenciamento para usar o Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6e336ec40ba2b9f6d3018e6a4f5b2ac721077aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895119"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Requisitos de licença para usar o Privileged Identity Management

Para usar o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), um diretório deve ter uma licença válida. Além disso, as licenças devem ser atribuídas aos administradores e aos usuários relevantes. Este artigo descreve os requisitos de licença para usar o Privileged Identity Management.

## <a name="prerequisites"></a>Pré-requisitos

Para usar Privileged Identity Management, seu diretório deve ter uma das seguintes licenças pagas ou de avaliação:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) e5
- Microsoft 365 M5

Para obter mais informações, consulte [What is Azure Active Directory? (O que é o Azure Active Directory?)](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Quais usuários devem ter licenças?

Cada administrador ou usuário que interage com ou recebe um benefício de Privileged Identity Management deve ter uma licença. Alguns exemplos:

- Administradores com funções do Azure AD gerenciadas usando o PIM
- Administradores com funções de recurso do Azure gerenciadas usando o PIM
- Administradores atribuídos à função de administrador de função com privilégios
- Usuários atribuídos como qualificados para funções do Azure AD gerenciadas usando o PIM
- Usuários capazes de aprovar/rejeitar solicitações no PIM
- Usuários atribuídos a uma função de recurso do Azure com atribuições just-in-time ou diretas (baseadas em tempo)  
- Usuários atribuídos a uma revisão de acesso
- Usuários que executam revisões de acesso

Para obter informações sobre como atribuir licenças aos seus usos, consulte [atribuir ou remover licenças usando o portal de Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>O que acontece quando uma licença expira?

Se uma licença Azure AD Premium P2, EMS E5 ou de avaliação expirar, Privileged Identity Management recursos não estarão mais disponíveis em seu diretório:

- As atribuições de função permanentes às funções do Azure AD não serão afetadas.
- O serviço de Privileged Identity Management no portal do Azure, bem como os cmdlets do API do Graph e as interfaces do PowerShell do Privileged Identity Management, não estarão mais disponíveis para que os usuários ativem funções privilegiadas, gerenciem o acesso privilegiado ou executem revisões de acesso de funções com privilégios.
- As atribuições de função qualificadas das funções do Azure AD serão removidas, pois os usuários não poderão mais ativar funções privilegiadas.
- Todas as revisões de acesso em andamento das funções do Azure AD serão encerradas e Privileged Identity Management definições de configuração serão removidas.
- Privileged Identity Management não enviará mais emails sobre alterações de atribuição de função.

## <a name="next-steps"></a>Passos seguintes

- [Implantar Privileged Identity Management](pim-deployment-plan.md)
- [Começar a usar o Privileged Identity Management](pim-getting-started.md)
- [Funções que você não pode gerenciar no Privileged Identity Management](pim-roles.md)
