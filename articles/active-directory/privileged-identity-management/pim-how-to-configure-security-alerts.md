---
title: Configurar alertas de segurança para funções do Azure AD no PIM - Azure Active Directory | Documentos da Microsoft
description: Saiba como configurar alertas de segurança para funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6875ab8f184c67b02c91d1a8e312959f3ba9553b
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476425"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-pim"></a>Configurar alertas de segurança para funções do Azure AD no PIM

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) gera alertas quando houver atividade suspeita ou não segura no seu ambiente. Quando for acionado um alerta, este aparece no dashboard do PIM. Selecione o alerta para ver um relatório que lista os utilizadores ou funções que acionou o alerta.

![Funções do AD do Azure – painel alerta listagem alertas e a gravidade](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Alertas de segurança

Esta secção lista todos os alertas de segurança para funções do Azure AD, além de como corrigir e como impedir. Gravidade tem o significado seguinte:

* **Alta**: Requer uma ação imediata devido a uma violação de política.
* **Médio**: Não necessita de uma ação imediata, mas sinaliza uma potencial violação de política.
* **Baixa**: Não necessita de uma ação imediata, mas sugere uma alteração de política preferível.

### <a name="administrators-arent-using-their-privileged-roles"></a>Os administradores não estiverem a utilizar as suas funções com privilégios

| | |
| --- | --- |
| **Gravidade** | Baixa |
| **Por que motivo recebo este alerta?** | Os utilizadores que tenham sido atribuídos a funções com privilégios que não precisam aumenta as chances de um ataque. Também é mais fácil para os atacantes permaneça despercebidas em contas que ativamente não estão a ser utilizadas. |
| **Como corrigir?** | Rever os utilizadores na lista e removê-los a partir de funções com privilégios, não é necessário. |
| **Prevenção** | Só atribua funções com privilégios para os utilizadores que tenham uma justificativa comercial. </br>Agendar regular [as revisões de acesso](pim-how-to-start-security-review.md) para verificar que os usuários ainda precisam de seu acesso. |
| **Ação de redução de dentro do portal** | Remove a conta da respetiva função com privilégios. |
| **Acionador** | Se um utilizador der um determinado período de tempo sem ativar uma função de acionada. |
| **Número de dias** | Esta definição especifica o número de dias, de 0 a 100, o que um utilizador pode aceder sem ativar uma função.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Funções não exigem autenticação multifator para a ativação

| | |
| --- | --- |
| **Gravidade** | Baixa |
| **Por que motivo recebo este alerta?** | Sem MFA, os utilizadores comprometidos podem ativarem funções com privilégios. |
| **Como corrigir?** | Reveja a lista de funções e [exigir a MFA](pim-how-to-change-default-settings.md) para cada função. |
| **Prevenção** | [Exigir a MFA](pim-how-to-change-default-settings.md) para cada função.  |
| **Ação de redução de dentro do portal** | Torna o MFA necessária para a ativação da função com privilégios. |

### <a name="the-tenant-doesnt-have-azure-ad-premium-p2"></a>O inquilino não tem o Azure AD Premium P2

| | |
| --- | --- |
| **Gravidade** | Baixa |
| **Por que motivo recebo este alerta?** | O inquilino atual não tem o Azure AD Premium P2. |
| **Como corrigir?** | Rever as informações sobre [edições do Azure AD](../fundamentals/active-directory-whatis.md). Atualizar para o Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Contas obsoletas potenciais numa função com privilégios

| | |
| --- | --- |
| **Gravidade** | Médio |
| **Por que motivo recebo este alerta?** | Contas numa função com privilégios que não foram alterados a palavra-passe nos últimos 90 dias. Estas contas podem ser o serviço ou as contas que não estão a ser mantidas e são vulneráveis aos atacantes partilhadas. |
| **Como corrigir?** | Reveja as contas na lista. Se já não precisam de acesso, removê-los a partir de suas funções com privilégios. |
| **Prevenção** | Certifique-se de que as contas que são partilhadas são a rotação das palavras-passe seguras quando existe uma alteração nos utilizadores que sabe a palavra-passe. </br>Rever regularmente as contas com funções com privilégios usando [as revisões de acesso](pim-how-to-start-security-review.md) e remover atribuições de funções que já não são necessários. |
| **Ação de redução de dentro do portal** | Remove a conta da respetiva função com privilégios. |
| **Melhores práticas** | Partilhado, serviço, e as contas de acesso de emergência que se autenticam utilizando uma palavra-passe e são atribuídas a funções administrativas com privilégios elevados, como Administrador Global ou administrador de segurança devem ter as palavras-passe giradas para os seguintes casos:<ul><li>Após um incidente de segurança que envolvem a utilizações indevidas ou o comprometimento dos direitos de acesso administrativo</li><li>Depois de privilégios de qualquer utilizador forem alterados, para que já não forem um administrador (por exemplo, após um funcionário que foi deixa um administrador IT ou sai da organização)</li><li>Em intervalos regulares (por exemplo, trimestralmente ou anual), mesmo se não ocorreu nenhuma violação conhecida ou a alteração para IT pessoal</li></ul>Uma vez que várias pessoas têm acesso às credenciais destas contas, as credenciais devem ser giradas para garantir que as pessoas que restam suas funções já não podem aceder as contas. [Saiba mais](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-pim"></a>Funções estão a ser atribuídas fora do PIM

| | |
| --- | --- |
| **Gravidade** | Alta |
| **Por que motivo recebo este alerta?** | Atribuições de funções privilegiadas efetuadas fora do PIM não são monitorizadas corretamente e podem indicar um ataque de Active Directory. |
| **Como corrigir?** | Rever os utilizadores na lista e removê-los a partir de funções com privilégios atribuídas fora do PIM. |
| **Prevenção** | Investigar em que os utilizadores estão a ser atribuídos funções com privilégios fora do PIM e proibir futuras atribuições a partir daí. |
| **Ação de redução de dentro do portal** | Remove a conta da respetiva função com privilégios. |

### <a name="there-are-too-many-global-administrators"></a>Existem muitos administradores globais

| | |
| --- | --- |
| **Gravidade** | Baixa |
| **Por que motivo recebo este alerta?** | Administrador global é a função com privilégios mais alta. Se um Administrador Global for comprometido, o atacante obtém acesso a todas as suas permissões, que coloca o seu sistema inteiro em risco. |
| **Como corrigir?** | Reveja os utilizadores na lista e remova qualquer um que não é absolutamente necessário a função de Administrador Global. </br>Atribua estes utilizadores a funções com privilégios mais baixas. |
| **Prevenção** | Atribua utilizadores a função com privilégios mínimos que precisam. |
| **Ação de redução de dentro do portal** | Remove a conta da respetiva função com privilégios. |
| **Acionador** | Acionado se dois critérios diferentes são cumpridos e pode configurar os dois. Em primeiro lugar, precisa atingir um certo limite de administradores globais. Em segundo lugar, um determinado percentual das atribuições de função total tem de ser administradores globais. Se cumprir apenas um dessas medidas, o alerta não aparecer. |
| **Número mínimo de administradores globais** | Esta definição especifica o número de administradores globais, de 2 para 100, que considere uma quantidade de não segura. |
| **Percentagem de administradores globais** | Esta definição especifica a percentagem mínima de administradores que são administradores globais, de % 0 e 100%, o que é inseguro no seu ambiente. |

### <a name="roles-are-being-activated-too-frequently"></a>Funções estão a ser ativadas com demasiada frequência

| | |
| --- | --- |
| **Gravidade** | Baixa |
| **Por que motivo recebo este alerta?** | Várias ativações para a mesma função com privilégios pelo mesmo utilizador é um sinal de um ataque. |
| **Como corrigir?** | Reveja os utilizadores na lista e certifique-se de que o [duração da ativação](pim-how-to-change-default-settings.md) respetiva função com privilégios está definida tempo suficiente para os mesmos executar suas tarefas. |
| **Prevenção** | Certifique-se de que o [duração da ativação](pim-how-to-change-default-settings.md) para funções com privilégios é definido tempo suficiente para os utilizadores executar suas tarefas.</br>[Exigir a MFA](pim-how-to-change-default-settings.md) para funções privilegiadas que tenham contas compartilhadas por vários administradores. |
| **Ação de redução de dentro do portal** | N/A |
| **Acionador** | Acionado se um utilizador ativa a mesma função com privilégios a várias vezes dentro de um período de tempo especificado. Pode configurar o período de tempo e o número de ativações. |
| **Período de tempo de renovação de ativação** | Esta definição especifica em dias, horas, minutos e, segundo o período de tempo que pretende utilizar para controlar as renovações suspeitas. |
| **Número de renovações de ativação** | Esta definição especifica o número de ativações, de 2 para 100, o que considerar digna de alerta, durante o período de tempo que escolheu. Pode alterar esta definição ao mover o controlo de deslize ou digitando um número na caixa de texto. |

## <a name="configure-security-alert-settings"></a>Configurar definições de alerta de segurança

Pode personalizar algumas dos alertas de segurança no PIM para trabalhar com o seu ambiente e os objetivos de segurança. Siga estes passos para abrir as definições de alerta de segurança:

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **funções do Azure AD**.

1. Clique em **configurações** e, em seguida **alertas**.

    ![Funções do Azure do AD - definições com os alertas selecionados](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Clique num nome de alerta para configurar a definição para esse alerta.

    ![Para o painel de definições de alerta de segurança alerta, selecionada](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Passos Seguintes

- [Configurar definições de função do Azure AD no PIM](pim-how-to-change-default-settings.md)
