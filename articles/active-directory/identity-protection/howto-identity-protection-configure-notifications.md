---
title: Notificações de Proteção de Identidade do Diretório Ativo Azure
description: Saiba como as notificações apoiam as suas atividades de investigação.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120123"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notificações de Proteção de Identidade do Diretório Ativo Azure

A Azure AD Identity Protection envia dois tipos de e-mails de notificação automatizados para ajudá-lo a gerir o risco do utilizador e deteções de risco:

- Utilizadores em risco detetados e-mails
- E-mail semanal de digestão

Este artigo fornece-lhe uma visão geral de ambos os e-mails de notificação.

## <a name="users-at-risk-detected-email"></a>Utilizadores em risco detetados e-mails

Em resposta a uma conta detetada em risco, a Azure AD Identity Protection gera um alerta de e-mail com **utilizadores em risco detetados** como sujeitos. O e-mail inclui um link para os **[Utilizadores sinalizados para](../reports-monitoring/concept-user-at-risk.md)** relatório de risco. Como uma boa prática, deve investigar imediatamente os utilizadores em risco.

A configuração para este alerta permite especificar em que nível de risco do utilizador pretende que o alerta seja gerado. O e-mail será gerado quando o nível de risco do utilizador atingir o que especificou; no entanto, não receberá novos utilizadores em alertas de email detetados em risco para este utilizador depois de estes se mudarem para este nível de risco do utilizador. Por exemplo, se definir a política para alertar sobre o risco médio do utilizador e o seu utilizador John se mover para um risco médio, receberá os utilizadores em risco detetados por e-mail para John. No entanto, não receberá um segundo utilizador em alerta detetado em risco se John se deslocar para alto risco ou tiver detetações adicionais de risco.

![Utilizadores em risco detetados e-mails](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Configurar utilizadores em alertas de risco detetados

Como administrador, pode definir:

- **O nível de risco do utilizador que desencadeia a geração deste e-mail** - Por padrão, o nível de risco está definido para o risco "Elevado".
- **Os destinatários deste e-mail** - Por padrão, os destinatários incluem todos os Administradores Globais. Os Administradores Globais também podem adicionar outros Administradores Globais, Administradores de Segurança, Leitores de Segurança como destinatários.
   - Opcionalmente pode **Adicionar e-mails adicionais para receber notificações** de alerta esta funcionalidade é uma pré-visualização e os utilizadores definidos devem ter as permissões adequadas para visualizar os relatórios ligados no portal Azure.

Configure os utilizadores em e-mail de risco no **portal Azure** sob o **azure Ative Directory** > **Security Identity** > **Protection** > **Users em alertas detetados**em risco.

## <a name="weekly-digest-email"></a>E-mail semanal de digestão

O e-mail semanal de digestão contém um resumo de novas deteções de risco.  
Inclui:

- Utilizadores em risco
- Atividades suspeitas
- Vulnerabilidades detetadas
- Ligações aos relatórios relacionados na Proteção de Identidade

![E-mail semanal de digestão](./media/howto-identity-protection-configure-notifications/400.png)

Por padrão, os destinatários incluem todos os Administradores Globais. Os Administradores Globais também podem adicionar outros Administradores Globais, Administradores de Segurança, Leitores de Segurança como destinatários.

### <a name="configure-weekly-digest-email"></a>Configurar e-mail semanal de digestão

Como administrador, pode trocar de envio de um e-mail semanal de digestão dentro ou fora e escolher os utilizadores designados para receber o e-mail.

Configure o e-mail semanal de digestão no **portal Azure** sob o **azure Ative Directory** > **Security Identity** > **Protection** > **Weekly**digest .

## <a name="see-also"></a>Consulte também

- [Proteção de Identidade do Diretório Ativo Azure](../active-directory-identityprotection.md)
