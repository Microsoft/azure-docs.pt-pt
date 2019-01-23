---
title: Notificações do Active Directory Identity Protection do Azure | Documentos da Microsoft
description: Saiba como notificações de suportam a suas atividades de investigação.
services: active-directory
keywords: proteção de identidade do Azure Active Directory, descoberta de aplicações na cloud, gestão de aplicações, a segurança, a risco, a nível de risco, a vulnerabilidade, a política de segurança
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: a105e6d85f1c62b166c08ae269928694d2fcb48e
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465824"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notificações do Azure Active Directory Identity Protection

O Azure AD Identity Protection envia dois tipos de e-mails de notificação automatizada para ajudar a gerir o risco de utilizador e eventos de risco:

- Utilizadores em risco detetado por e-mail
- E-mail de resumo semanal

Este artigo fornece uma descrição geral de ambas as mensagens de e-mail de notificação.


## <a name="users-at-risk-detected-email"></a>Utilizadores em risco detetado por e-mail

Em resposta a uma conta detetada em risco, o Azure AD Identity Protection gera um alerta de e-mail com **utilizadores em risco detetado** como assunto. O e-mail inclui uma ligação para o **[utilizadores sinalizados para risco](../reports-monitoring/concept-user-at-risk.md)** relatório. Como melhor prática, deve investigar imediatamente os utilizadores em risco.

![Utilizadores em risco detetado por e-mail](./media/notifications/01.png)


### <a name="configuration"></a>Configuração

Como administrador, pode definir:

- **O nível de risco que aciona a geração deste e-mail** -por predefinição, o nível de risco é definido como "Alto" risco.
- **Os destinatários deste e-mail** -por predefinição, os destinatários incluem todos os administradores globais. Os administradores globais também podem adicionar outros leitores de segurança de administradores globais, administradores de segurança, como os destinatários.  


Para abrir a caixa de diálogo relacionada, clique em **alertas** no **definições** seção o **Identity Protection** página.

![Utilizadores em risco detetado por e-mail](./media/notifications/05.png)


## <a name="weekly-digest-email"></a>E-mail de resumo semanal

O e-mail de resumo semanal contém um resumo dos novos eventos de risco.  
Ele inclui:

- Utilizadores em risco

- Atividades suspeitas

- Vulnerabilidades detetadas

- Links para os relatórios relacionados no Identity Protection

    ![Remediação](./media/notifications/400.png "remediação")

### <a name="configuration"></a>Configuração

Como administrador, pode mudar a enviar um e-mail de resumo semanal.

![Riscos de usuário](./media/notifications/62.png "riscos de utilizador")

Para abrir a caixa de diálogo relacionada, clique em **resumo semanal** no **definições** seção o **Identity Protection** página.

![Utilizadores em risco detetado por e-mail](./media/notifications/04.png)


## <a name="see-also"></a>Consulte também

- [O Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
