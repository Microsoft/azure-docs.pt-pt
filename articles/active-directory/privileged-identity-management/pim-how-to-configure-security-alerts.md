---
title: Configurar alertas de segurança para funções do Azure AD no PIM-Azure Active Directory | Microsoft Docs
description: Saiba como configurar alertas de segurança para funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e188ef651f6fe539932cf1670f914e8b57564567
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809083"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Configurar alertas de segurança para funções do Azure AD no Privileged Identity Management

Privileged Identity Management (PIM) gera alertas quando há atividade suspeita ou não segura em sua organização do Azure Active Directory (AD do Azure). Quando um alerta é disparado, ele aparece no painel Privileged Identity Management. Selecione o alerta para ver um relatório que lista os usuários ou funções que dispararam o alerta.

![Funções do Azure AD – painel de alerta listando alertas e a severidade](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Alertas de segurança

Esta seção lista todos os alertas de segurança para funções do Azure AD, juntamente com como corrigir e como evitar. A severidade tem o seguinte significado:

- **Alto**: requer ação imediata devido a uma violação de política.
- **Médio**: não requer ação imediata, mas sinaliza uma possível violação de política.
- **Baixo**: não requer ação imediata, mas sugere uma alteração de política preferida.

### <a name="administrators-arent-using-their-privileged-roles"></a>Os administradores não estão usando suas funções privilegiadas

| | |
| --- | --- |
| **Severity** | Baixo |
| **Por que obtenho esse alerta?** | Os usuários aos quais foram atribuídas funções privilegiadas não precisam aumentar a chance de um ataque. Também é mais fácil para os invasores permanecerem despercebidos em contas que não estão sendo usadas ativamente. |
| **Como corrigir?** | Examine os usuários na lista e remova-os das funções privilegiadas que eles não precisam. |
| **Prevenção** | Atribua funções privilegiadas somente a usuários que têm uma justificativa comercial. </br>Agende [revisões de acesso](pim-how-to-start-security-review.md) regulares para verificar se os usuários ainda precisam de seu acesso. |
| **Ação de mitigação no portal** | Remove a conta de sua função privilegiada. |
| **Acionador** | Disparado se um usuário passar por um número especificado de dias sem ativar uma função. |
| **Número de dias** | Essa configuração especifica o número máximo de dias, de 0 a 100, que um usuário pode ir sem ativar uma função.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>As funções não exigem autenticação multifator para ativação

| | |
| --- | --- |
| **Severity** | Baixo |
| **Por que obtenho esse alerta?** | Sem a autenticação multifator, os usuários comprometidos podem ativar funções privilegiadas. |
| **Como corrigir?** | Examine a lista de funções e [exija a autenticação multifator](pim-how-to-change-default-settings.md) para cada função. |
| **Prevenção** | [Exigir MFA](pim-how-to-change-default-settings.md) para cada função.  |
| **Ação de mitigação no portal** | Torna necessária a autenticação multifator para a ativação da função com privilégios. |

### <a name="the-tenant-doesnt-have-azure-ad-premium-p2"></a>O locatário não tem Azure AD Premium P2

| | |
| --- | --- |
| **Severity** | Baixo |
| **Por que obtenho esse alerta?** | O locatário atual não tem Azure AD Premium P2. |
| **Como corrigir?** | Examine as informações sobre as [edições do Azure ad](../fundamentals/active-directory-whatis.md). Atualize para o Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Contas obsoletas em potencial em uma função com privilégios

| | |
| --- | --- |
| **Severity** | Médio |
| **Por que obtenho esse alerta?** | As contas em uma função privilegiada não alteraram sua senha nos últimos 90 dias. Essas contas podem ser contas compartilhadas ou de serviço que não estão sendo mantidas e estão vulneráveis a invasores. |
| **Como corrigir?** | Examine as contas na lista. Se eles não precisarem mais de acesso, remova-os de suas funções privilegiadas. |
| **Prevenção** | Verifique se as contas compartilhadas estão girando senhas fortes quando há uma alteração nos usuários que conhecem a senha. </br>Examine regularmente as contas com funções privilegiadas usando [revisões de acesso](pim-how-to-start-security-review.md) e remova as atribuições de função que não são mais necessárias. |
| **Ação de mitigação no portal** | Remove a conta de sua função privilegiada. |
| **Melhores práticas** | As contas de acesso compartilhado, de serviço e de emergência que se autenticam usando uma senha e são atribuídas a funções administrativas altamente privilegiadas, como administrador global ou administrador de segurança, devem ter suas senhas giradas para os seguintes casos:<ul><li>Após um incidente de segurança envolvendo uso indevido ou comprometimento de direitos de acesso administrativo</li><li>Depois que os privilégios de qualquer usuário são alterados para que eles não sejam mais um administrador (por exemplo, depois que um funcionário que era um administrador o deixa ou sai da organização)</li><li>Em intervalos regulares (por exemplo, trimestral ou anual), mesmo se não houvesse nenhuma violação conhecida ou mudança para a equipe de ti</li></ul>Como várias pessoas têm acesso a essas credenciais de contas, as credenciais devem ser giradas para garantir que as pessoas que saíram de suas funções não possam mais acessar as contas. [Saiba mais](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>As funções estão sendo atribuídas fora do Privileged Identity Management

| | |
| --- | --- |
| **Severity** | Elevado |
| **Por que obtenho esse alerta?** | As atribuições de função com privilégios feitas fora do Privileged Identity Management não são monitoradas corretamente e podem indicar um ataque ativo. |
| **Como corrigir?** | Examine os usuários na lista e remova-os das funções privilegiadas atribuídas fora do Privileged Identity Management. |
| **Prevenção** | Investigue onde os usuários estão sendo atribuídos a funções privilegiadas fora do Privileged Identity Management e proíba as atribuições futuras a partir daí. |
| **Ação de mitigação no portal** | Remove o usuário de sua função privilegiada. |

### <a name="there-are-too-many-global-administrators"></a>Há muitos administradores globais

| | |
| --- | --- |
| **Severity** | Baixo |
| **Por que obtenho esse alerta?** | O administrador global é a função com privilégios mais altos. Se um administrador global estiver comprometido, o invasor ganhará acesso a todas as suas permissões, o que coloca todo o sistema em risco. |
| **Como corrigir?** | Examine os usuários na lista e remova qualquer que não precise absolutamente da função de administrador global. </br>Em vez disso, atribua funções com privilégios inferiores a esses usuários. |
| **Prevenção** | Atribua aos usuários a função menos privilegiada de que precisam. |
| **Ação de mitigação no portal** | Remove a conta de sua função privilegiada. |
| **Acionador** | Disparado se dois critérios diferentes forem atendidos e você puder configurar ambos. Primeiro, você precisa alcançar um certo limite de administradores globais. Em segundo lugar, um determinado percentual de suas atribuições de função total deve ser administradores globais. Se você atender apenas a uma dessas medidas, o alerta não será exibido. |
| **Número mínimo de administradores globais** | Essa configuração especifica o número de administradores globais, de 2 a 100, que você considera muito poucos para sua organização do Azure AD. |
| **Percentual de administradores globais** | Essa configuração especifica o percentual mínimo de administradores que são administradores globais, de 0% a 100%, abaixo do qual você não deseja que sua organização do Azure AD seja DIP. |

### <a name="roles-are-being-activated-too-frequently"></a>As funções estão sendo ativadas com muita frequência

| | |
| --- | --- |
| **Severity** | Baixo |
| **Por que obtenho esse alerta?** | Várias ativações para a mesma função com privilégios pelo mesmo usuário são um sinal de ataque. |
| **Como corrigir?** | Examine os usuários na lista e verifique se a [duração da ativação](pim-how-to-change-default-settings.md) para sua função privilegiada está definida por tempo suficiente para que eles executem suas tarefas. |
| **Prevenção** | Verifique se a [duração da ativação](pim-how-to-change-default-settings.md) para funções privilegiadas está definida por tempo suficiente para que os usuários executem suas tarefas.</br>[Exigir autenticação multifator](pim-how-to-change-default-settings.md) para funções privilegiadas que têm contas compartilhadas por vários administradores. |
| **Ação de mitigação no portal** | N/A |
| **Acionador** | Disparado se um usuário ativar a mesma função com privilégios várias vezes em um período especificado. Você pode configurar o período de tempo e o número de ativações. |
| **Período de renovação da ativação** | Essa configuração especifica em dias, horas, minutos e segundo o período de tempo que você deseja usar para controlar as renovações suspeitas. |
| **Número de renovações de ativação** | Essa configuração especifica o número de ativações, de 2 a 100, em que você deseja ser notificado, dentro do período de tempo escolhido. Você pode alterar essa configuração movendo o controle deslizante ou digitando um número na caixa de texto. |

## <a name="configure-security-alert-settings"></a>Definir configurações de alerta de segurança

Você pode personalizar alguns dos alertas de segurança no Privileged Identity Management para trabalhar com as necessidades e metas de segurança de sua organização. Siga estas etapas para abrir as configurações de alerta de segurança:

1. Abra **Privileged Identity Management** no Azure AD.

1. Selecione **funções do Azure ad**.

1. Selecione **configurações** e **alertas**.

    ![Funções do Azure AD – configurações com alertas selecionados](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Selecione um nome de alerta para definir a configuração para esse alerta.

    ![Para o alerta selecionado, painel configurações de alerta de segurança](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Passos seguintes

- [Definir as configurações de função do Azure AD no Privileged Identity Management](pim-how-to-change-default-settings.md)
