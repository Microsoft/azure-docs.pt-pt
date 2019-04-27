---
title: Os requisitos para utilizar o PIM - Azure Active Directory de licença | Documentos da Microsoft
description: Descreve os requisitos de licenciamento para utilizar o Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/16/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e16209a185623b6e15650f70141edd6394e337
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60437461"
---
# <a name="license-requirements-to-use-pim"></a>Requisitos de licença para utilizar o PIM

Para utilizar o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), um diretório tem de ter uma licença válida. Além disso, tem de atribuir licenças para os administradores e utilizadores relevantes. Este artigo descreve os requisitos de licença para utilizar o PIM.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o PIM, o diretório tem de ter um dos seguintes pagos ou licenças de avaliação:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Para obter mais informações, consulte [What is Azure Active Directory? (O que é o Azure Active Directory?)](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Os utilizadores que tem de ter licenças?

Cada administrador ou utilizador que interage com ou recebe um benefício do PIM tem de ter uma licença. Os exemplos incluem:

- Os administradores com funções do Azure AD geridos através do PIM
- Administradores com funções de recursos do Azure geridos com o PIM
- Administradores atribuídos à função de administrador com função privilegiada
- Os utilizadores elegíveis para funções do Azure AD como geridos através do PIM
- Utilizadores podem aprovar/rejeitar pedidos no PIM
- Utilizadores atribuídos a uma função de recursos do Azure com just-in-time ou diretas atribuições (baseados no tempo)  
- Utilizadores atribuídos a uma revisão de acesso
- Utilizadores que realizam as revisões de acesso

Para obter informações sobre como atribuir licenças aos seus usos, consulte [atribuir ou remover licenças através do portal do Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>O que acontece quando uma licença expira?

Se um Azure AD Premium P2, EMS E5 ou licença de avaliação expirar, as funcionalidades PIM já não estarão disponíveis no seu diretório:

- Atribuições de função permanente para funções do Azure AD não serão afetadas.
- O serviço PIM no portal do Azure, bem como os cmdlets do Graph API e as interfaces de PowerShell do PIM, deixará de estar disponível para os utilizadores ativarem funções com privilégios, gerir o acesso privilegiado ou realizar revisões de acesso de funções com privilégios.
- Serão removidas as atribuições de função elegível de funções do Azure AD, como os utilizadores já não poderão ativar as funções com privilégios.
- Quaisquer revisões de acesso contínuo de funções do Azure AD irão terminar e as definições de configuração do PIM serão removidas.
- PIM já não irá enviar e-mails sobre as alterações de atribuição de função.

## <a name="next-steps"></a>Passos Seguintes

- [Implementar o PIM](pim-deployment-plan.md)
- [Comece a utilizar o PIM](pim-getting-started.md)
- [Funções não é possível gerir no PIM](pim-roles.md)
