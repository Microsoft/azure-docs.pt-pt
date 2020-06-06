---
title: Notificações de Proteção de Identidade Azure Ative
description: Saiba como as notificações suportam as suas atividades de investigação.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7b5208d2da3635e822049859cae9c8f17b6105a
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2020
ms.locfileid: "84464254"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notificações de Proteção de Identidade Azure Ative

A Azure AD Identity Protection envia dois tipos de e-mails de notificação automatizados para ajudá-lo a gerir o risco do utilizador e as deteções de riscos:

- Utilizadores em risco detetado e-mail
- E-mail semanal de digestão

Este artigo fornece-lhe uma visão geral de ambos os e-mails de notificação.

## <a name="users-at-risk-detected-email"></a>Utilizadores em risco detetado e-mail

Em resposta a uma conta detetada em risco, a Azure AD Identity Protection gera um alerta de e-mail com **os Utilizadores em risco detetados** como sujeitos. O e-mail inclui um link para os **[Utilizadores sinalizados para](../reports-monitoring/concept-user-at-risk.md)** relatório de risco. Como uma boa prática, deve investigar imediatamente os utilizadores em risco.

A configuração deste alerta permite especificar a que nível de risco o utilizador pretende que o alerta seja gerado. O e-mail será gerado quando o nível de risco do utilizador atingir o que especificou; no entanto, não irá receber novos utilizadores em risco de alertas de e-mail detetados para este utilizador depois de se deslocarem para este nível de risco de utilizador. Por exemplo, se definir a política para alertar sobre o risco médio do utilizador e o seu utilizador John se deslocar para risco médio, receberá os utilizadores em risco detetado e-mail para John. No entanto, não receberá um segundo utilizador em risco detetado em alerta se John avançar para um risco elevado ou tiver deteções de risco adicionais.

![Utilizadores em risco detetado e-mail](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Configure utilizadores em alertas de risco detetados

Como administrador, pode definir:

- **O nível de risco do utilizador que desencadeia a geração deste e-mail** - Por padrão, o nível de risco é definido para risco "elevado".
- **Os destinatários deste e-mail** - Por padrão, os destinatários incluem todos os Administradores Globais. Os Administradores Globais também podem adicionar outros Administradores Globais, Administradores de Segurança, Leitores de Segurança como destinatários.
   - Opcionalmente pode **adicionar e-mails adicionais para receber notificações de alerta** esta funcionalidade é uma pré-visualização e os utilizadores definidos devem ter as permissões adequadas para ver os relatórios ligados no portal Azure.

Configure os utilizadores em risco no **portal Azure** ao abrigo do **Azure Ative Directory**  >  **Security**  >  **Protection**  >  **Users em alertas detetados**.

## <a name="weekly-digest-email"></a>E-mail semanal de digestão

O e-mail semanal de digestão contém um resumo de novas deteções de risco.  
Inclui:

- Detetados novos utilizadores de risco
- Detetados novos sinais de risco (em tempo real)
- Ligações aos relatórios relacionados na Proteção de Identidade

![E-mail semanal de digestão](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

Por padrão, os destinatários incluem todos os Administradores Globais. Os Administradores Globais também podem adicionar outros Administradores Globais, Administradores de Segurança, Leitores de Segurança como destinatários.

### <a name="configure-weekly-digest-email"></a>Configure e-mail semanal de digestão

Como administrador, pode mudar o envio de um e-mail semanal de digestão ligado ou desligado e escolher os utilizadores designados para receber o e-mail.

Configure o e-mail semanal de digestão no **portal Azure** no âmbito da **Azure Ative**  >  **Directory Security**Identity  >  **Protection**Weekly  >  **digest**.

## <a name="see-also"></a>Consulte também

- [Proteção de Identidade do Diretório Ativo Azure](../active-directory-identityprotection.md)
