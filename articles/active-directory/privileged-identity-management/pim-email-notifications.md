---
title: Notificações por email no PIM-Azure Active Directory | Microsoft Docs
description: Descreve as notificações por email no Azure AD Privileged Identity Management (PIM).
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
ms.date: 01/05/2019
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee5f2edbae28276f8485ae774a5b1c52e1af2fd1
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756391"
---
# <a name="email-notifications-in-pim"></a>Notificações por email no PIM

Privileged Identity Management (PIM) permite que você saiba quando eventos importantes ocorrem em sua organização do Azure Active Directory (Azure AD), como quando uma função é atribuída ou ativada. Privileged Identity Management mantém você informado enviando-lhe e outros participantes notificações por email. Esses emails também podem incluir links para tarefas relevantes, como ativar ou renovar uma função. Este artigo descreve de que forma esses emails se parecem, quando eles são enviados e quem os recebe.

## <a name="sender-email-address-and-subject-line"></a>Endereço de email do remetente e linha do assunto

Os emails enviados de Privileged Identity Management para as funções de recurso do Azure AD e do Azure têm o seguinte endereço de email do remetente:

- Endereço de email: **Azure-noresponder\@Microsoft.com**
- Nome de exibição: Microsoft Azure

Esses emails incluem um prefixo de **PIM** na linha de assunto. Segue-se um exemplo:

- PIM: Alain Charon atribuiu permanentemente a função de leitor de backup

## <a name="notifications-for-azure-ad-roles"></a>Notificações para funções do Azure AD

Privileged Identity Management envia emails quando os seguintes eventos ocorrem para funções do Azure AD:

- Quando uma ativação de função privilegiada estiver com aprovação pendente
- Quando uma solicitação de ativação de função privilegiada for concluída
- Quando Azure AD Privileged Identity Management está habilitado

Quem recebe esses emails para as funções do Azure AD depende de sua função, do evento e da configuração de notificações:

| Utilizador | A ativação da função está com aprovação pendente | A solicitação de ativação de função foi concluída | O PIM está habilitado |
| --- | --- | --- | --- |
| Administrador de função com privilégios</br>(Ativado/qualificado) | Sim</br>(somente se nenhum aprovador explícito for especificado) | Sim* | Sim |
| Administrador de segurança</br>(Ativado/qualificado) | Não | Sim* | Sim |
| Administrador Global</br>(Ativado/qualificado) | Não | Sim* | Sim |

\* se a [configuração **notificações** ](pim-how-to-change-default-settings.md#notifications) estiver definida como **habilitar**.

Veja a seguir um email de exemplo que é enviado quando um usuário ativa uma função do Azure AD para a organização fictícia Contoso.

![Novo Privileged Identity Management email para funções do Azure AD](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Email de resumo semanal Privileged Identity Management para funções do Azure AD

Um email de resumo Privileged Identity Management semanal para funções do Azure AD é enviado para administradores de função com privilégios, administradores de segurança e administradores globais que habilitaram o Privileged Identity Management. Este email semanal fornece um instantâneo de Privileged Identity Management atividades para a semana, bem como atribuições de função com privilégios. Ele só está disponível para locatários na nuvem pública. Aqui está um exemplo de email:

![Email de resumo semanal Privileged Identity Management para funções do Azure AD](./media/pim-email-notifications/email-directory-weekly.png)

O email inclui quatro blocos:

| Bloco | Descrição |
| --- | --- |
| **Usuários ativados** | Número de vezes que os usuários ativaram sua função qualificada dentro do locatário. |
| **Usuários que se tornaram permanentes** | Número de vezes que os usuários com uma atribuição qualificada são tornados permanentes. |
| **Atribuições de função no Privileged Identity Management** | Número de vezes que os usuários recebem uma função qualificada dentro Privileged Identity Management. |
| **Atribuições de função fora do PIM** | Número de vezes que os usuários recebem uma função permanente fora do Privileged Identity Management (dentro do Azure AD). |

A **visão geral da seção de funções principais** lista as cinco principais funções em seu locatário com base no número total de administradores permanentes e qualificados para cada função. O link **executar ação** abre o [Assistente do PIM](pim-security-wizard.md) , no qual você pode converter administradores permanentes em lotes qualificados em lotes.

## <a name="pim-emails-for-azure-resource-roles"></a>Emails do PIM para funções de recurso do Azure

Privileged Identity Management envia emails aos proprietários e aos administradores de acesso do usuário quando os seguintes eventos ocorrem para as funções de recurso do Azure:

- Quando uma atribuição de função está com aprovação pendente
- Quando uma função é atribuída
- Quando uma função estiver em breve para expirar
- Quando uma função está qualificada para estender
- Quando uma função está sendo renovada por um usuário final
- Quando uma solicitação de ativação de função é concluída

Privileged Identity Management envia emails aos usuários finais quando os seguintes eventos ocorrem para as funções de recurso do Azure:

- Quando uma função é atribuída ao usuário
- Quando a função de um usuário está expirada
- Quando a função de um usuário é estendida
- Quando a solicitação de ativação de função de um usuário for concluída

Veja a seguir um email de exemplo que é enviado quando um usuário recebe uma função de recurso do Azure para a organização fictícia Contoso.

![Novo Privileged Identity Management email para funções de recurso do Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Passos seguintes

- [Definir as configurações de função do Azure AD no Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Aprovar ou negar solicitações para funções do Azure AD no Privileged Identity Management](azure-ad-pim-approval-workflow.md)
