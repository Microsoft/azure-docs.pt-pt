---
title: Notificações de Azure Active Directory Identity Protection
description: Saiba como as notificações dão suporte às suas atividades de investigação.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c83aa6e476bbd898999fb6efe490c7847a809ff
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382161"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notificações de Azure Active Directory Identity Protection

Azure AD Identity Protection envia dois tipos de emails de notificação automatizados para ajudá-lo a gerenciar as detecções de risco e risco do usuário:

- Email de usuários em risco detectados
- Email de resumo semanal

Este artigo fornece uma visão geral dos dois emails de notificação.

## <a name="users-at-risk-detected-email"></a>Email de usuários em risco detectados

Em resposta a uma conta detectada em risco, Azure AD Identity Protection gera um alerta de email com **usuários em risco detectado** como assunto. O email inclui um link para o relatório **[usuários sinalizados para risco](../reports-monitoring/concept-user-at-risk.md)** . Como prática recomendada, você deve investigar imediatamente os usuários em risco.

A configuração para esse alerta permite especificar em qual nível de risco de usuário você deseja que o alerta seja gerado. O email será gerado quando o nível de risco do usuário atingir o que você especificou; no entanto, você não receberá novos usuários em risco os alertas de email detectados para esse usuário depois que eles forem movidos para esse nível de risco do usuário. Por exemplo, se você definir a política para alertar sobre o risco de usuário médio e seu usuário João passar para risco médio, você receberá os usuários em risco o email detectado para João. No entanto, você não receberá um segundo usuário em risco detectado alerta se João passar para alto risco ou tiver detecções de risco adicionais.

![Email de usuários em risco detectados](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Configurar usuários em risco os alertas detectados

Como administrador, você pode definir:

- **O nível de risco do usuário que dispara a geração desse email** -por padrão, o nível de risco é definido como risco "alto".
- **Os destinatários deste email** -por padrão, os destinatários incluem todos os administradores globais. Os administradores globais também podem adicionar outros administradores globais, administradores de segurança, leitores de segurança como destinatários.
   - Opcionalmente, você pode **Adicionar emails adicionais para receber notificações de alerta** esse recurso é uma visualização e os usuários definidos devem ter as permissões apropriadas para exibir os relatórios vinculados no portal do Azure.

Configure os usuários em risco email no **portal do Azure** em **Azure Active Directory** > **segurança** > **identidade proteção** > **usuários em risco os alertas detectados**.

## <a name="weekly-digest-email"></a>Email de resumo semanal

O email de resumo semanal contém um resumo das novas detecções de risco.  
Ele inclui:

- Utilizadores em risco
- Atividades suspeitas
- Vulnerabilidades detectadas
- Links para os relatórios relacionados no Identity Protection

![Email de resumo semanal](./media/howto-identity-protection-configure-notifications/400.png)

Por padrão, os destinatários incluem todos os administradores globais. Os administradores globais também podem adicionar outros administradores globais, administradores de segurança, leitores de segurança como destinatários.

### <a name="configure-weekly-digest-email"></a>Configurar email de resumo semanal

Como administrador, você pode alternar o envio de um email de resumo semanal ou desativá-lo e escolher os usuários atribuídos para receber o email.

Configure o email de resumo semanal no **portal do Azure** em **Azure Active Directory** > **segurança** > **identidade Protection** > **resumo semanal**.

## <a name="see-also"></a>Consulte também

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
