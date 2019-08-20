---
title: Acesso condicional-exigir MFA para gerenciamento do Azure-Azure Active Directory
description: Criar uma política de acesso condicional personalizada para exigir autenticação multifator para tarefas de gerenciamento do Azure
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 884d96fae226497991f5642072d040ccd81134b0
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576748"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Acesso condicional: Exigir MFA para gerenciamento do Azure

As organizações usam uma variedade de serviços do Azure e os gerenciam de ferramentas baseadas em Azure Resource Manager como:

* Portal do Azure
* Azure PowerShell
* CLI do Azure

Essas ferramentas podem fornecer acesso altamente privilegiado a recursos, que podem alterar configurações de toda a assinatura, configurações de serviço e cobrança de assinatura. Para proteger esses recursos privilegiados, a Microsoft recomenda exigir a autenticação multifator para que qualquer usuário acesse esses recursos.

## <a name="user-exclusions"></a>Exclusões de usuário

As políticas de acesso condicional são ferramentas poderosas, recomendamos excluir as seguintes contas da sua política:

* Contas de **acesso de emergência** ou de **vidro** para impedir o bloqueio de conta em todo o locatário. No cenário improvável, todos os administradores são bloqueados de seu locatário, sua conta administrativa de acesso de emergência pode ser usada para fazer logon no locatário. siga as etapas para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo [gerenciar contas de acesso de emergência no Azure ad](../users-groups-roles/directory-emergency-access.md).
* **Contas de serviço** e **princípios de serviço**, como a conta de sincronização de Azure ad Connect. As contas de serviço são contas não interativas que não estão ligadas a nenhum usuário específico. Normalmente, eles são usados por serviços de back-end e permitem acesso programático a aplicativos. As contas de serviço devem ser excluídas, pois a MFA não pode ser concluída programaticamente.
   * Se sua organização tiver essas contas em uso em scripts ou código, considere substituí-las [](../managed-identities-azure-resources/overview.md)por identidades gerenciadas. Como solução alternativa temporária, você pode excluir essas contas específicas da política de linha de base.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para exigir que as funções administrativas atribuídas executem a autenticação multifator.

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** > **acesso condicional**.
1. Selecione **nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **atribuições**, selecione **usuários e grupos**
   1. Em **incluir**, selecione **todos os usuários**.
   1. Em **excluir**, selecione **usuários e grupos** e escolha o acesso de emergência da sua organização ou contas de vidro. 
   1. Selecione **Done** (Concluído).
1. Em **aplicativos de nuvem ou ações** > **incluem**, selecione **selecionar aplicativos**, escolha **Gerenciamento de Microsoft Azure**e selecione **selecionar** e **concluído**.
1. Em**concessão**de **controles** > de acesso, selecione **conceder acesso**, **exigir autenticação**multifator e selecione **selecionar**.
1. Confirme suas configurações e defina **habilitar política** como **ativado**.
1. Selecione **criar** para criar para habilitar a política.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Simular comportamento de entrada usando a ferramenta de What If de acesso condicional](troubleshoot-conditional-access-what-if.md)
