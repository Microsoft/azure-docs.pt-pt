---
title: Alertas de segurança para funções da Azure AD na PIM - Azure AD Microsoft Docs
description: Configure alertas de segurança para funções Azure AD Privileged Identity Management in Azure Ative Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/13/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 530eebea19d9e53f85a0079d6fba91c615ee6dd1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375465"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Configure alertas de segurança para funções da AD Azure na Gestão de Identidade Privilegiada

A Privileged Identity Management (PIM) gera alertas quando existe atividade suspeita ou insegura na sua organização Azure Ative Directory (Azure AD). Quando um alerta é desencadeado, aparece no painel de gestão de identidade privilegiada. Selecione o alerta para ver um relatório que lista os utilizadores ou funções que desencadearam o alerta.

## <a name="determine-your-version-of-pim"></a>Determine a sua versão do PIM

A partir de novembro de 2019, a parte da AD Azure da Privileged Identity Management está a ser atualizada para uma nova versão que corresponde às experiências para funções de recursos Do Azure. Isto cria funcionalidades adicionais, bem como [alterações à API existente.](azure-ad-roles-features.md#api-changes) Enquanto a nova versão está a ser lançada, quais os procedimentos que segue neste artigo dependem da versão de Gestão de Identidade Privilegiada que tem atualmente. Siga os passos nesta secção para determinar qual a versão da Gestão de Identidade Privilegiada que tem. Depois de conhecer a sua versão de Gestão de Identidade Privilegiada, pode selecionar os procedimentos neste artigo que correspondam a essa versão.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que esteja no papel de [administrador privilegiado.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Open **Azure AD Privileged Identity Management.** Se tiver um banner no topo da página de visão geral, siga as instruções no separador **versão Nova** deste artigo. Caso contrário, siga as instruções no separador versão **anterior.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Siga as medidas deste artigo para investigar alertas de segurança para funções da AD Azure.

# <a name="new-version"></a>[Nova versão](#tab/new)

![Funções Azure AD - Alerta de alerta de alerta de alerta e gravidade](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>Alertas de segurança

Esta secção lista todos os alertas de segurança para funções da AD Azure, juntamente com a forma de corrigir e como prevenir. A gravidade tem o seguinte significado:

- **Alto**: Requer uma ação imediata por causa de uma violação de política.
- **Médio**: Não requer medidas imediatas, mas sinaliza uma potencial violação da política.
- **Baixo**: Não requer medidas imediatas, mas sugere uma mudança de política preferível.

### <a name="administrators-arent-using-their-privileged-roles"></a>Os administradores não estão a usar os seus papéis privilegiados.

| | |
| --- | --- |
| **Gravidade** | Baixa |
| **Por que recebo este alerta?** | Os utilizadores que receberam papéis privilegiados que não precisam aumentam a hipótese de um ataque. Também é mais fácil para os atacantes permanecerem despercebidos em contas que não estão a ser utilizadas ativamente. |
| **Como consertar?** | Reveja os utilizadores da lista e remova-os de papéis privilegiados de que não necessitam. |
| **Prevenção** | Atribuir funções privilegiadas apenas a utilizadores que tenham uma justificação de negócio. </br>Agende [avaliações](pim-how-to-start-security-review.md) regulares de acesso para verificar se os utilizadores ainda precisam do seu acesso. |
| **Ação de mitigação no portal** | Remove a conta do seu papel privilegiado. |
| **Acionador** | Acionado se um utilizador passar um determinado número de dias sem ativar uma função. |
| **Número de dias** | Esta definição especifica o número máximo de dias, de 0 a 100, para que um utilizador possa ir sem ativar uma função.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Funções não requerem autenticação multifactor para ativação

| | |
| --- | --- |
| **Gravidade** | Baixa |
| **Por que recebo este alerta?** | Sem autenticação de vários fatores, os utilizadores comprometidos podem ativar funções privilegiadas. |
| **Como consertar?** | Reveja a lista de funções e exija a [autenticação de vários fatores](pim-how-to-change-default-settings.md) para cada função. |
| **Prevenção** | [Exigir MFA](pim-how-to-change-default-settings.md) para cada papel.  |
| **Ação de mitigação no portal** | Faz a autenticação de vários fatores necessária para a ativação do papel privilegiado. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>A organização não tem Azure AD Premium P2

| | |
| --- | --- |
| **Gravidade** | Baixa |
| **Por que recebo este alerta?** | A atual organização da Azure AD não tem Azure AD Premium P2. |
| **Como consertar?** | Reveja as informações sobre [as edições da AD Azure.](../fundamentals/active-directory-whatis.md) Upgrade para Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potenciais contas velhas num papel privilegiado

| | |
| --- | --- |
| **Gravidade** | Médio |
| **Por que recebo este alerta?** | As contas num papel privilegiado não mudaram a sua palavra-passe nos últimos 90 dias. Estas contas podem ser serviços ou contas partilhadas que não estão a ser mantidas e são vulneráveis aos agressores. |
| **Como consertar?** | Reveja as contas da lista. Se já não precisarem de acesso, retirem-nos dos seus papéis privilegiados. |
| **Prevenção** | Certifique-se de que as contas partilhadas estão a rodar senhas fortes quando houver uma alteração nos utilizadores que conheçam a palavra-passe. </br>Reveja regularmente as contas com funções privilegiadas utilizando [avaliações](pim-how-to-start-security-review.md) de acesso e remova atribuições de funções que já não sejam necessárias. |
| **Ação de mitigação no portal** | Remove a conta do seu papel privilegiado. |
| **Melhores práticas** | As contas partilhadas, de serviço e de acesso de emergência que autenticam usando uma palavra-passe e que são atribuídas a funções administrativas altamente privilegiadas, como administrador global ou administrador de segurança, devem ter as suas palavras-passe rodadas para os seguintes casos:<ul><li>Após um incidente de segurança envolvendo uso indevido ou compromisso de direitos de acesso administrativo</li><li>Depois de os privilégios de qualquer utilizador serem alterados para que deixem de ser administradores (por exemplo, depois de um funcionário que foi administrador deixar a TI ou deixar a organização)</li><li>Em intervalos regulares (por exemplo, trimestral ou anual), mesmo que não tenha havido violação ou alteração do pessoal de TI</li></ul>Uma vez que várias pessoas têm acesso às credenciais destas contas, as credenciais devem ser giradas para garantir que as pessoas que abandonaram as suas funções já não possam aceder às contas. [Saiba mais](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>As funções estão a ser atribuídas fora da Gestão de Identidade Privilegiada

| | |
| --- | --- |
| **Gravidade** | Alta |
| **Por que recebo este alerta?** | As atribuições de funções privilegiadas feitas fora da Gestão de Identidade Privilegiada não são devidamente monitorizadas e podem indicar um ataque ativo. |
| **Como consertar?** | Reveja os utilizadores da lista e remova-os das funções privilegiadas atribuídas fora da Gestão de Identidade Privilegiada. |
| **Prevenção** | Investigue onde os utilizadores estão a ser atribuídos papéis privilegiados fora da Gestão de Identidade Privilegiada e proíba futuras atribuições a partir daí. |
| **Ação de mitigação no portal** | Remove o utilizador do seu papel privilegiado. |

### <a name="there-are-too-many-global-administrators"></a>Há demasiados administradores globais

| | |
| --- | --- |
| **Gravidade** | Baixa |
| **Por que recebo este alerta?** | O administrador global é o papel privilegiado mais alto. Se um Administrador Global estiver comprometido, o intruso obtém acesso a todas as suas permissões, o que coloca todo o seu sistema em risco. |
| **Como consertar?** | Reveja os utilizadores da lista e remova qualquer um que não precise absolutamente do papel de administrador global. </br>Atribuir papéis privilegiados mais baixos a estes utilizadores. |
| **Prevenção** | Atribuir aos utilizadores o papel menos privilegiado de que necessitam. |
| **Ação de mitigação no portal** | Remove a conta do seu papel privilegiado. |
| **Acionador** | Desencadeado se dois critérios diferentes forem cumpridos, e pode configurar os dois. Primeiro, tens de atingir um certo limiar de administradores globais. Em segundo lugar, uma certa percentagem das suas atribuições totais deve ser administradores globais. Se só cumprir uma destas medições, o alerta não aparece. |
| **Número mínimo de Administradores Globais** | Esta definição especifica o número de administradores globais, de 2 a 100, que considera serem muito poucos para a sua organização Azure AD. |
| **Percentagem de Administradores Globais** | Esta definição especifica a percentagem mínima de administradores que são administradores globais, de 0% a 100%, abaixo do qual não quer que a sua organização Azure AD baixe. |

### <a name="roles-are-being-activated-too-frequently"></a>As funções estão a ser ativadas com demasiada frequência.

| | |
| --- | --- |
| **Gravidade** | Baixa |
| **Por que recebo este alerta?** | Múltiplas ativações para o mesmo papel privilegiado pelo mesmo utilizador é um sinal de um ataque. |
| **Como consertar?** | Reveja os utilizadores da lista e certifique-se de que a [duração de ativação](pim-how-to-change-default-settings.md) para a sua função privilegiada é definida o tempo suficiente para que eles executem as suas tarefas. |
| **Prevenção** | Certifique-se de que a [duração de ativação](pim-how-to-change-default-settings.md) para funções privilegiadas é definida o tempo suficiente para que os utilizadores executem as suas tarefas.</br>[Requerer a autenticação de vários fatores](pim-how-to-change-default-settings.md) para funções privilegiadas que tenham contas partilhadas por vários administradores. |
| **Ação de mitigação no portal** | N/A |
| **Acionador** | Acionado se um utilizador ativar a mesma função privilegiada várias vezes num período especificado. Pode configurar tanto o período de tempo como o número de ativações. |
| **Prazo de renovação da ativação** | Esta definição especifica em dias, horas, minutos e segundo o período de tempo que pretende utilizar para rastrear renovações suspeitas. |
| **Número de renovações de ativação** | Esta definição especifica o número de ativações, de 2 a 100, nas quais gostaria de ser notificada, dentro do prazo que escolheu. Pode alterar esta definição movendo o slider ou digitando um número na caixa de texto. |

## <a name="configure-security-alert-settings"></a>Configurar as definições de alerta de segurança

A partir da página Alertas, vá a **Definições**.

![Página de alertas com Definições destacadas](media/pim-how-to-configure-security-alerts/alert-settings.png)

Personalize as definições nos diferentes alertas para trabalhar com o seu ambiente e objetivos de segurança.

![Página de definição para um alerta para ativar e configurar definições](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

![Funções Azure AD - Alerta de alerta de alerta de alerta e gravidade](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Alertas de segurança

Esta secção lista todos os alertas de segurança para funções da AD Azure, juntamente com a forma de corrigir e como prevenir. A gravidade tem o seguinte significado:

- **Alto**: Requer uma ação imediata por causa de uma violação de política.
- **Médio**: Não requer medidas imediatas, mas sinaliza uma potencial violação da política.
- **Baixo**: Não requer medidas imediatas, mas sugere uma mudança de política preferível.

### <a name="administrators-arent-using-their-privileged-roles"></a>Os administradores não estão a usar os seus papéis privilegiados.

| | |
| --- | --- |
| **Gravidade** | Baixa |
| **Por que recebo este alerta?** | Os utilizadores que receberam papéis privilegiados que não precisam aumentam a hipótese de um ataque. Também é mais fácil para os atacantes permanecerem despercebidos em contas que não estão a ser utilizadas ativamente. |
| **Como consertar?** | Reveja os utilizadores da lista e remova-os de papéis privilegiados de que não necessitam. |
| **Prevenção** | Atribuir funções privilegiadas apenas a utilizadores que tenham uma justificação de negócio. </br>Agende [avaliações](pim-how-to-start-security-review.md) regulares de acesso para verificar se os utilizadores ainda precisam do seu acesso. |
| **Ação de mitigação no portal** | Remove a conta do seu papel privilegiado. |
| **Acionador** | Acionado se um utilizador passar um determinado número de dias sem ativar uma função. |
| **Número de dias** | Esta definição especifica o número máximo de dias, de 0 a 100, para que um utilizador possa ir sem ativar uma função.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Funções não requerem autenticação multifactor para ativação

| | |
| --- | --- |
| **Gravidade** | Baixa |
| **Por que recebo este alerta?** | Sem autenticação de vários fatores, os utilizadores comprometidos podem ativar funções privilegiadas. |
| **Como consertar?** | Reveja a lista de funções e exija a [autenticação de vários fatores](pim-how-to-change-default-settings.md) para cada função. |
| **Prevenção** | [Exigir MFA](pim-how-to-change-default-settings.md) para cada papel.  |
| **Ação de mitigação no portal** | Faz a autenticação de vários fatores necessária para a ativação do papel privilegiado. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>A organização não tem Azure AD Premium P2

| | |
| --- | --- |
| **Gravidade** | Baixa |
| **Por que recebo este alerta?** | A atual organização da Azure AD não tem Azure AD Premium P2. |
| **Como consertar?** | Reveja as informações sobre [as edições da AD Azure.](../fundamentals/active-directory-whatis.md) Upgrade para Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potenciais contas velhas num papel privilegiado

| | |
| --- | --- |
| **Gravidade** | Médio |
| **Por que recebo este alerta?** | As contas num papel privilegiado não mudaram a sua palavra-passe nos últimos 90 dias. Estas contas podem ser serviços ou contas partilhadas que não estão a ser mantidas e são vulneráveis aos agressores. |
| **Como consertar?** | Reveja as contas da lista. Se já não precisarem de acesso, retirem-nos dos seus papéis privilegiados. |
| **Prevenção** | Certifique-se de que as contas partilhadas estão a rodar senhas fortes quando houver uma alteração nos utilizadores que conheçam a palavra-passe. </br>Reveja regularmente as contas com funções privilegiadas utilizando [avaliações](pim-how-to-start-security-review.md) de acesso e remova atribuições de funções que já não sejam necessárias. |
| **Ação de mitigação no portal** | Remove a conta do seu papel privilegiado. |
| **Melhores práticas** | As contas partilhadas, de serviço e de acesso de emergência que autenticam usando uma palavra-passe e que são atribuídas a funções administrativas altamente privilegiadas, como administrador global ou administrador de segurança, devem ter as suas palavras-passe rodadas para os seguintes casos:<ul><li>Após um incidente de segurança envolvendo uso indevido ou compromisso de direitos de acesso administrativo</li><li>Depois de os privilégios de qualquer utilizador serem alterados para que deixem de ser administradores (por exemplo, depois de um funcionário que foi administrador deixar a TI ou deixar a organização)</li><li>Em intervalos regulares (por exemplo, trimestral ou anual), mesmo que não tenha havido violação ou alteração do pessoal de TI</li></ul>Uma vez que várias pessoas têm acesso às credenciais destas contas, as credenciais devem ser giradas para garantir que as pessoas que abandonaram as suas funções já não possam aceder às contas. [Saiba mais](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>As funções estão a ser atribuídas fora da Gestão de Identidade Privilegiada

| | |
| --- | --- |
| **Gravidade** | Alta |
| **Por que recebo este alerta?** | As atribuições de funções privilegiadas feitas fora da Gestão de Identidade Privilegiada não são devidamente monitorizadas e podem indicar um ataque ativo. |
| **Como consertar?** | Reveja os utilizadores da lista e remova-os das funções privilegiadas atribuídas fora da Gestão de Identidade Privilegiada. |
| **Prevenção** | Investigue onde os utilizadores estão a ser atribuídos papéis privilegiados fora da Gestão de Identidade Privilegiada e proíba futuras atribuições a partir daí. |
| **Ação de mitigação no portal** | Remove o utilizador do seu papel privilegiado. |

### <a name="there-are-too-many-global-administrators"></a>Há demasiados administradores globais

| | |
| --- | --- |
| **Gravidade** | Baixa |
| **Por que recebo este alerta?** | O administrador global é o papel privilegiado mais alto. Se um Administrador Global estiver comprometido, o intruso obtém acesso a todas as suas permissões, o que coloca todo o seu sistema em risco. |
| **Como consertar?** | Reveja os utilizadores da lista e remova qualquer um que não precise absolutamente do papel de administrador global. </br>Atribuir papéis privilegiados mais baixos a estes utilizadores. |
| **Prevenção** | Atribuir aos utilizadores o papel menos privilegiado de que necessitam. |
| **Ação de mitigação no portal** | Remove a conta do seu papel privilegiado. |
| **Acionador** | Desencadeado se dois critérios diferentes forem cumpridos, e pode configurar os dois. Primeiro, tens de atingir um certo limiar de administradores globais. Em segundo lugar, uma certa percentagem das suas atribuições totais deve ser administradores globais. Se só cumprir uma destas medições, o alerta não aparece. |
| **Número mínimo de Administradores Globais** | Esta definição especifica o número de administradores globais, de 2 a 100, que considera serem muito poucos para a sua organização Azure AD. |
| **Percentagem de Administradores Globais** | Esta definição especifica a percentagem mínima de administradores que são administradores globais, de 0% a 100%, abaixo do qual não quer que a sua organização Azure AD baixe. |

### <a name="roles-are-being-activated-too-frequently"></a>As funções estão a ser ativadas com demasiada frequência.

| | |
| --- | --- |
| **Gravidade** | Baixa |
| **Por que recebo este alerta?** | Múltiplas ativações para o mesmo papel privilegiado pelo mesmo utilizador é um sinal de um ataque. |
| **Como consertar?** | Reveja os utilizadores da lista e certifique-se de que a [duração de ativação](pim-how-to-change-default-settings.md) para a sua função privilegiada é definida o tempo suficiente para que eles executem as suas tarefas. |
| **Prevenção** | Certifique-se de que a [duração de ativação](pim-how-to-change-default-settings.md) para funções privilegiadas é definida o tempo suficiente para que os utilizadores executem as suas tarefas.</br>[Requerer a autenticação de vários fatores](pim-how-to-change-default-settings.md) para funções privilegiadas que tenham contas partilhadas por vários administradores. |
| **Ação de mitigação no portal** | N/A |
| **Acionador** | Acionado se um utilizador ativar a mesma função privilegiada várias vezes num período especificado. Pode configurar tanto o período de tempo como o número de ativações. |
| **Prazo de renovação da ativação** | Esta definição especifica em dias, horas, minutos e segundo o período de tempo que pretende utilizar para rastrear renovações suspeitas. |
| **Número de renovações de ativação** | Esta definição especifica o número de ativações, de 2 a 100, nas quais gostaria de ser notificada, dentro do prazo que escolheu. Pode alterar esta definição movendo o slider ou digitando um número na caixa de texto. |

## <a name="configure-security-alert-settings"></a>Configurar as definições de alerta de segurança

Pode personalizar alguns dos alertas de segurança na Gestão de Identidade Privilegiada para trabalhar com as necessidades e objetivos de segurança da sua organização. Siga estes passos para abrir as definições de alerta de segurança:

1. Open **Privileged Identity Management** in Azure AD.

1. Selecione **funções Azure AD**.

1. Selecione **Definições e,** em seguida, alertas .

    ![Funções Azure AD - Definições com Alertas selecionados](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Selecione um nome de alerta para configurar a definição para esse alerta.

    ![Para o alerta selecionado, painel de definições de alerta de segurança](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>Passos Seguintes

- [Configure definições de funções de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-change-default-settings.md)
