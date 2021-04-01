---
title: Notificações de e-mail em PIM - Azure Ative Directory | Microsoft Docs
description: Descreve notificações de e-mail na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/28/2020
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe57a44a5a6fa9a631604d92419fd8f5ebcce50a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93394735"
---
# <a name="email-notifications-in-pim"></a>Notificações de e-mail em PIM

A Gestão de Identidade Privilegiada (PIM) informa-o quando ocorrem eventos importantes na sua organização Azure Ative Directory (Azure AD), como quando uma função é atribuída ou ativada. A Gestão de Identidade Privilegiada mantém-no informado enviando-lhe e a outros participantes notificações de e-mail. Estes e-mails também podem incluir links para tarefas relevantes, tais como ativar ou renovar uma função. Este artigo descreve como estes e-mails são, quando são enviados, e quem os recebe.

## <a name="sender-email-address-and-subject-line"></a>Endereço de e-mail remetente e linha de assunto

Os e-mails enviados da Gestão de Identidade Privilegiada para as funções de Azure AD e Azure têm o seguinte endereço de e-mail remetente:

- Endereço  **de e-mail: azure-nor profundamente \@ microsoft.com**
- Nome do ecrã: Microsoft Azure

Estes e-mails incluem um prefixo **PIM** na linha de assunto. Eis um exemplo:

- PIM: Alain Charon foi permanentemente atribuído o papel de Leitor de Backup

## <a name="notifications-for-azure-ad-roles"></a>Notificações para funções AZURE AD

Gestão de Identidade Privilegiada envia e-mails quando ocorrem os seguintes eventos para funções AD Azure:

- Quando uma ativação de função privilegiada está pendente de aprovação
- Quando um pedido privilegiado de ativação de função é concluído
- Quando a Azure AD Gestão de Identidade Privilegiada estiver ativada

Quem recebe estes e-mails para funções Ad Azure depende do seu papel, do evento e da definição de notificações:

| User | A ativação de funções está pendente de aprovação | O pedido de ativação de funções está concluído | PIM está ativado |
| --- | --- | --- | --- |
| Administrador privilegiado</br>(Ativado/Elegível) | Yes</br>(apenas se não forem especificados aprovadores explícitos) | Sim* | Yes |
| Administrador de Segurança</br>(Ativado/Elegível) | No | Sim* | Yes |
| Administrador Global</br>(Ativado/Elegível) | No | Sim* | Yes |

\*Se a [definição **de Notificações**](pim-how-to-change-default-settings.md#notifications) estiver definida para **Ativar**.

O seguinte mostra um e-mail de exemplo que é enviado quando um utilizador ativa um papel AD Azure para a organização ficcional Contoso.

![Novo e-mail privilegiado de Gestão de Identidade para funções de AD Azure](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Gestão de Identidade Privilegiada Semanal digerir e-mail para funções AZure AD

Um e-mail de gestão de identidade privilegiada semanal para funções Azure AD é enviado para Administradores de Funções Privilegiados, Administradores de Segurança e Administradores Globais que habilitaram a Gestão de Identidade Privilegiada. Este e-mail semanal fornece uma foto das atividades de Gestão de Identidade Privilegiada para a semana, bem como atribuições de funções privilegiadas. Só está disponível para organizações AD AZure na nuvem pública. Aqui está um e-mail de exemplo:

![Gestão de Identidade Privilegiada Semanal digerir e-mail para funções AZure AD](./media/pim-email-notifications/email-directory-weekly.png)

O e-mail inclui quatro azulejos:

| Mosaico | Description |
| --- | --- |
| **Utilizadores ativados** | Número de vezes que os utilizadores ativaram o seu papel elegível dentro da organização. |
| **Utilizadores tornados permanentes** | O número de vezes que os utilizadores com uma atribuição elegível são permanentes. |
| **Atribuições de funções na Gestão de Identidade Privilegiada** | Número de vezes que os utilizadores recebem uma função elegível dentro da Gestão de Identidade Privilegiada. |
| **Atribuições de funções fora da PIM** | Número de vezes que os utilizadores recebem uma função permanente fora da Gestão de Identidade Privilegiada (dentro da Azure AD). |

A visão geral da sua secção **de papéis principais** lista as cinco principais funções da sua organização com base no número total de administradores permanentes e elegíveis para cada função. O link **de ação Take** abre o assistente [PIM](pim-security-wizard.md) onde pode converter administradores permanentes a administradores elegíveis em lotes.

## <a name="email-timing-for-activation-approvals"></a>Tempo de e-mail para aprovações de ativação

Quando os utilizadores ativarem a sua função e a definição de função necessitar de aprovação, os aprovadores receberão dois e-mails para cada aprovação:

- Pedido de aprovação ou negação do pedido de ativação do utilizador (enviado pelo motor de aprovação do pedido)
- O pedido do utilizador é aprovado (enviado pelo motor de aprovação do pedido)

Além disso, administradores globais e administradores de funções privilegiadas recebem um e-mail para cada aprovação:

- A função do utilizador é ativada (enviada pela Gestão de Identidade Privilegiada)

Os dois primeiros e-mails enviados pelo motor de aprovação do pedido podem ser atrasados. Atualmente, 90% dos e-mails demoram de três a dez minutos, mas para 1% os clientes pode ser muito mais longo, até 15 minutos.

Se um pedido de aprovação for aprovado no portal Azure antes do envio do primeiro e-mail, o primeiro e-mail deixará de ser acionado e outros aprovadores não serão notificados por e-mail do pedido de aprovação. Pode parecer que não receberam um e-mail, mas é o comportamento esperado.

## <a name="pim-emails-for-azure-resource-roles"></a>E-mails PIM para funções de recursos Azure

A Gestão de Identidade Privilegiada envia e-mails aos Proprietários e Administradores de Acesso ao Utilizador quando ocorrem os seguintes eventos para funções de recursos Azure:

- Quando uma atribuição de papel está pendente de aprovação
- Quando um papel é atribuído
- Quando um papel está prestes a expirar
- Quando um papel é elegível para estender
- Quando um papel está a ser renovado por um utilizador final
- Quando um pedido de ativação de função é concluído

A Gestão de Identidade Privilegiada envia e-mails aos utilizadores finais quando ocorrem os seguintes eventos para funções de recursos Azure:

- Quando uma função é atribuída ao utilizador
- Quando o papel de um utilizador é expirado
- Quando o papel de um utilizador é estendido
- Quando o pedido de ativação de função de um utilizador é concluído

O seguinte mostra um e-mail de exemplo que é enviado quando um utilizador é atribuído um papel de recurso Azure para a organização ficcional Contoso.

![Novo e-mail privilegiado de Gestão de Identidade para funções de recursos da Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Passos seguintes

- [Configurar definições de papel de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-change-default-settings.md)
- [Aprovar ou negar pedidos de funções de Azure AD na Gestão de Identidade Privilegiada](azure-ad-pim-approval-workflow.md)
