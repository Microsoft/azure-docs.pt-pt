---
title: Alertas de segurança para funções da AD Azure na PIM - Azure AD ! Microsoft Docs
description: Configure alertas de segurança para funções Azure AD Gestão de Identidade Privilegiada em Diretório Ativo Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 706770db4309d1a909bc1161ab9d6657b6c5310a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533553"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Configure alertas de segurança para funções de Ad Azure em Gestão de Identidade Privilegiada

A Gestão privilegiada de Identidade (PIM) gera alertas quando há atividade suspeita ou insegura na sua organização Azure Ative Directory (Azure AD). Quando um alerta é acionado, aparece no painel de gestão de identidade privilegiada. Selecione o alerta para ver um relatório que lista os utilizadores ou funções que desencadearam o alerta.

## <a name="determine-your-version-of-pim"></a>Determine a sua versão de PIM

A partir de novembro de 2019, a parte de funções da AZure AD da Gestão de Identidade Privilegiada está a ser atualizada para uma nova versão que corresponde às experiências para funções de recursos Azure. Isto cria funcionalidades adicionais, bem como [alterações à API existente.](azure-ad-roles-features.md#api-changes) Enquanto a nova versão está a ser lançada, quais os procedimentos que segue neste artigo dependem da versão de Gestão de Identidade Privilegiada que tem atualmente. Siga os passos nesta secção para determinar qual a versão da Gestão de Identidade Privilegiada que tem. Depois de conhecer a sua versão de Gestão de Identidade Privilegiada, pode selecionar os procedimentos deste artigo que correspondam a essa versão.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que esteja na [função de administrador](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) privilegiado.
1. Open **Azure AD Gestão de Identidade Privilegiada.** Se tiver um banner no topo da página geral, siga as instruções no separador **Nova versão** deste artigo. Caso contrário, siga as instruções no separador **versão anterior.**

  [![Selecione Azure AD > Gestão de Identidade Privilegiada.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Siga as etapas deste artigo para investigar alertas de segurança para as funções de Azure AD.

# <a name="new-version"></a>[Nova versão](#tab/new)

![Screenshot que mostra a página "Alertas" com uma lista de alertas e sua gravidade.](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>Alertas de segurança

Esta secção lista todos os alertas de segurança para as funções Azure AD, juntamente com como corrigir e como prevenir. A severidade tem o seguinte significado:

- **High**: Requer ação imediata por causa de uma violação de política.
- **Médio**: Não requer ação imediata, mas sinaliza uma potencial violação da política.
- **Baixo**: Não requer ação imediata, mas sugere uma mudança de política preferível.

### <a name="administrators-arent-using-their-privileged-roles"></a>Os administradores não estão a usar as suas funções privilegiadas.

| | |
| --- | --- |
| **Gravidade** | Baixo |
| **Por que recebo este alerta?** | Os utilizadores que lhes foram atribuídos papéis privilegiados não precisam aumenta a hipótese de um ataque. Também é mais fácil para os atacantes ficarem despercebidos em contas que não estão a ser utilizadas ativamente. |
| **Como consertar?** | Reveja os utilizadores da lista e remova-os das funções privilegiadas de que não necessitam. |
| **Prevenção** | Atribuir funções privilegiadas apenas a utilizadores que tenham uma justificação de negócio. </br>Agende [avaliações regulares de acesso](pim-how-to-start-security-review.md) para verificar se os utilizadores ainda precisam do seu acesso. |
| **Ação de mitigação no portal** | Retira a conta do seu papel privilegiado. |
| **Acionador** | Acionado se um utilizador passar por um determinado número de dias sem ativar uma função. |
| **Número de dias** | Esta definição especifica o número máximo de dias, de 0 a 100, que um utilizador pode ir sem ativar uma função.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>As funções não requerem autenticação multi-factor para ativação

| | |
| --- | --- |
| **Gravidade** | Baixo |
| **Por que recebo este alerta?** | Sem a autenticação de vários fatores, os utilizadores comprometidos podem ativar funções privilegiadas. |
| **Como consertar?** | Reveja a lista de funções e [exija autenticação multi-factor](pim-how-to-change-default-settings.md) para cada papel. |
| **Prevenção** | [Requer MFA](pim-how-to-change-default-settings.md) para cada papel.  |
| **Ação de mitigação no portal** | Faz a autenticação de vários fatores necessária para a ativação do papel privilegiado. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>A organização não tem Azure AD Premium P2

| | |
| --- | --- |
| **Gravidade** | Baixo |
| **Por que recebo este alerta?** | A atual organização AZure AD não tem Azure AD Premium P2. |
| **Como consertar?** | Reveja informações sobre [as edições AD da Azure.](../fundamentals/active-directory-whatis.md) Upgrade para Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potenciais contas velhas num papel privilegiado

| | |
| --- | --- |
| **Gravidade** | Médio |
| **Por que recebo este alerta?** | As contas de um papel privilegiado não alteraram a sua palavra-passe nos últimos 90 dias. Estas contas podem ser de serviço ou contas partilhadas que não estão a ser mantidas e vulneráveis aos atacantes. |
| **Como consertar?** | Reveja as contas da lista. Se já não precisarem de acesso, removam-nos dos seus papéis privilegiados. |
| **Prevenção** | Certifique-se de que as contas que são partilhadas estão a rodar senhas fortes quando há uma alteração nos utilizadores que conhecem a palavra-passe. </br>Reveja regularmente contas com funções privilegiadas usando [revisões de acesso](pim-how-to-start-security-review.md) e remova atribuições de funções que já não são necessárias. |
| **Ação de mitigação no portal** | Retira a conta do seu papel privilegiado. |
| **Melhores práticas** | As contas partilhadas, de serviço e de acesso de emergência que autenticam usando uma palavra-passe e que são atribuídas a funções administrativas altamente privilegiadas, como administrador global ou administrador de Segurança, devem ter as suas palavras-passe rotativas para os seguintes casos:<ul><li>Após um incidente de segurança envolvendo uso indevido ou compromisso de direitos de acesso administrativo</li><li>Depois de os privilégios de qualquer utilizador serem alterados para que deixem de ser administradores (por exemplo, depois de um funcionário que foi administrador deixar a TI ou deixar a organização)</li><li>A intervalos regulares (por exemplo, trimestrais ou ano a ano), mesmo que não se tenha sabido violação ou alteração do pessoal de TI</li></ul>Uma vez que várias pessoas têm acesso às credenciais destas contas, as credenciais devem ser giradas para garantir que as pessoas que deixaram as suas funções já não possam aceder às contas. [Saiba mais sobre a garantia de contas](../users-groups-roles/directory-admin-roles-secure.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>As funções estão a ser atribuídas fora da Gestão de Identidade Privilegiada

| | |
| --- | --- |
| **Gravidade** | Alto |
| **Por que recebo este alerta?** | As atribuições privilegiadas de funções feitas fora da Gestão de Identidade Privilegiada não são devidamente monitorizadas e podem indicar um ataque ativo. |
| **Como consertar?** | Reveja os utilizadores da lista e remova-os das funções privilegiadas atribuídas fora da Gestão de Identidade Privilegiada. |
| **Prevenção** | Investigue onde os utilizadores estão a ser atribuídos funções privilegiadas fora da Gestão de Identidade Privilegiada e proíba futuras atribuições a partir daí. |
| **Ação de mitigação no portal** | Retira o utilizador do seu papel privilegiado. |

### <a name="there-are-too-many-global-administrators"></a>Há demasiados administradores globais

| | |
| --- | --- |
| **Gravidade** | Baixo |
| **Por que recebo este alerta?** | O administrador global é o papel privilegiado mais alto. Se um Administrador Global estiver comprometido, o intruso obtém acesso a todas as suas permissões, o que coloca todo o seu sistema em risco. |
| **Como consertar?** | Reveja os utilizadores da lista e remova quaisquer que não necessitem absolutamente da função de administrador global. </br>Atribuir papéis privilegiados mais baixos a estes utilizadores. |
| **Prevenção** | Atribua aos utilizadores o papel menos privilegiado de que necessitam. |
| **Ação de mitigação no portal** | Retira a conta do seu papel privilegiado. |
| **Acionador** | Desencadeado se forem cumpridos dois critérios diferentes, pode configurar os dois. Primeiro, tens de atingir um determinado limiar de atribuições de funções de administrador global. Em segundo lugar, uma certa percentagem das suas atribuições totais de funções devem ser administradores globais. Se apenas encontrar uma destas medições, o alerta não aparece. |
| **Número mínimo de administradores globais** | Esta definição especifica o número de atribuições de funções de Administrador Global, de 2 a 100, que considera serem muito poucas para a sua organização AZure AD. |
| **Percentagem de Administradores Globais** | Esta definição especifica a percentagem mínima de administradores que são administradores globais, de 0% a 100%, abaixo da qual não quer que a sua organização Azure AD diminua. |

### <a name="roles-are-being-activated-too-frequently"></a>As funções estão a ser ativadas com demasiada frequência.

| | |
| --- | --- |
| **Gravidade** | Baixo |
| **Por que recebo este alerta?** | Múltiplas ativações para o mesmo papel privilegiado pelo mesmo utilizador é um sinal de um ataque. |
| **Como consertar?** | Reveja os utilizadores da lista e certifique-se de que a duração de [ativação](pim-how-to-change-default-settings.md) para o seu papel privilegiado é definida o tempo suficiente para que eles executem as suas tarefas. |
| **Prevenção** | Certifique-se de que a duração de [ativação](pim-how-to-change-default-settings.md) das funções privilegiadas é definida o tempo suficiente para que os utilizadores realizem as suas tarefas.</br>[Requerem autenticação multi-factor](pim-how-to-change-default-settings.md) para funções privilegiadas que tenham contas partilhadas por vários administradores. |
| **Ação de mitigação no portal** | N/D |
| **Acionador** | Desencadeado se um utilizador ativar a mesma função privilegiada várias vezes num período determinado. Pode configurar tanto o período de tempo como o número de ativações. |
| **Prazo de renovação da ativação** | Esta definição especifica em dias, horas, minutos e segundo o período de tempo que pretende utilizar para rastrear renovações suspeitas. |
| **Número de renovações de ativação** | Esta definição especifica o número de ativações, de 2 a 100, nas quais gostaria de ser notificado, dentro do prazo escolhido. Pode alterar esta definição movendo o slider ou digitando um número na caixa de texto. |

## <a name="customize-security-alert-settings"></a>Personalize as definições de alerta de segurança

Na página **Alertas,** selecione **Definições**.

![Página de alertas com Definições em destaque](media/pim-how-to-configure-security-alerts/alert-settings.png)

Personalize as definições nos diferentes alertas para trabalhar com o seu ambiente e objetivos de segurança.

![Definição de página para um alerta para ativar e configurar definições](media/pim-how-to-configure-security-alerts/security-alert-settings.png)

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

![Funções AD AD - Alerta de listagem de painéis e gravidade](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alert-details"></a>Detalhes do alerta de segurança

Esta secção lista todos os alertas de segurança para as funções Azure AD, juntamente com como corrigir e como prevenir. A severidade tem o seguinte significado:

- **High**: Requer ação imediata por causa de uma violação de política.
- **Médio**: Não requer ação imediata, mas sinaliza uma potencial violação da política.
- **Baixo**: Não requer ação imediata, mas sugere uma mudança de política preferível.

### <a name="administrators-arent-using-their-privileged-roles"></a>Os administradores não estão a usar as suas funções privilegiadas.

| | |
| --- | --- |
| **Gravidade** | Baixo |
| **Por que recebo este alerta?** | Os utilizadores que lhes foram atribuídos papéis privilegiados não precisam aumenta a hipótese de um ataque. Também é mais fácil para os atacantes ficarem despercebidos em contas que não estão a ser utilizadas ativamente. |
| **Como consertar?** | Reveja os utilizadores da lista e remova-os das funções privilegiadas de que não necessitam. |
| **Prevenção** | Atribuir funções privilegiadas apenas a utilizadores que tenham uma justificação de negócio. </br>Agende [avaliações regulares de acesso](pim-how-to-start-security-review.md) para verificar se os utilizadores ainda precisam do seu acesso. |
| **Ação de mitigação no portal** | Retira a conta do seu papel privilegiado. |
| **Acionador** | Acionado se um utilizador passar por um determinado número de dias sem ativar uma função. |
| **Número de dias** | Esta definição especifica o número máximo de dias, de 0 a 100, que um utilizador pode ir sem ativar uma função.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>As funções não requerem autenticação multi-factor para ativação

| | |
| --- | --- |
| **Gravidade** | Baixo |
| **Por que recebo este alerta?** | Sem a autenticação de vários fatores, os utilizadores comprometidos podem ativar funções privilegiadas. |
| **Como consertar?** | Reveja a lista de funções e [exija autenticação multi-factor](pim-how-to-change-default-settings.md) para cada papel. |
| **Prevenção** | [Requer MFA](pim-how-to-change-default-settings.md) para cada papel.  |
| **Ação de mitigação no portal** | Faz a autenticação de vários fatores necessária para a ativação do papel privilegiado. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>A organização não tem Azure AD Premium P2

| | |
| --- | --- |
| **Gravidade** | Baixo |
| **Por que recebo este alerta?** | A atual organização AZure AD não tem Azure AD Premium P2. |
| **Como consertar?** | Reveja informações sobre [as edições AD da Azure.](../fundamentals/active-directory-whatis.md) Upgrade para Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potenciais contas velhas num papel privilegiado

| | |
| --- | --- |
| **Gravidade** | Médio |
| **Por que recebo este alerta?** | As contas de um papel privilegiado não alteraram a sua palavra-passe nos últimos 90 dias. Estas contas podem ser de serviço ou contas partilhadas que não estão a ser mantidas e vulneráveis aos atacantes. |
| **Como consertar?** | Reveja as contas da lista. Se já não precisarem de acesso, removam-nos dos seus papéis privilegiados. |
| **Prevenção** | Certifique-se de que as contas que são partilhadas estão a rodar senhas fortes quando há uma alteração nos utilizadores que conhecem a palavra-passe. </br>Reveja regularmente contas com funções privilegiadas usando [revisões de acesso](pim-how-to-start-security-review.md) e remova atribuições de funções que já não são necessárias. |
| **Ação de mitigação no portal** | Retira a conta do seu papel privilegiado. |
| **Melhores práticas** | As contas partilhadas, de serviço e de acesso de emergência que autenticam usando uma palavra-passe e que são atribuídas a funções administrativas altamente privilegiadas, como administrador global ou administrador de Segurança, devem ter as suas palavras-passe rotativas para os seguintes casos:<ul><li>Após um incidente de segurança envolvendo uso indevido ou compromisso de direitos de acesso administrativo</li><li>Depois de os privilégios de qualquer utilizador serem alterados para que deixem de ser administradores (por exemplo, depois de um funcionário que foi administrador deixar a TI ou deixar a organização)</li><li>A intervalos regulares (por exemplo, trimestrais ou ano a ano), mesmo que não se tenha sabido violação ou alteração do pessoal de TI</li></ul>Uma vez que várias pessoas têm acesso às credenciais destas contas, as credenciais devem ser giradas para garantir que as pessoas que deixaram as suas funções já não possam aceder às contas. [Saiba mais](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>As funções estão a ser atribuídas fora da Gestão de Identidade Privilegiada

| | |
| --- | --- |
| **Gravidade** | Alto |
| **Por que recebo este alerta?** | As atribuições privilegiadas de funções feitas fora da Gestão de Identidade Privilegiada não são devidamente monitorizadas e podem indicar um ataque ativo. |
| **Como consertar?** | Reveja os utilizadores da lista e remova-os das funções privilegiadas atribuídas fora da Gestão de Identidade Privilegiada. |
| **Prevenção** | Investigue onde os utilizadores estão a ser atribuídos funções privilegiadas fora da Gestão de Identidade Privilegiada e proíba futuras atribuições a partir daí. |
| **Ação de mitigação no portal** | Retira o utilizador do seu papel privilegiado. |

### <a name="there-are-too-many-global-administrators"></a>Há demasiados administradores globais

| | |
| --- | --- |
| **Gravidade** | Baixo |
| **Por que recebo este alerta?** | O administrador global é o papel privilegiado mais alto. Se um Administrador Global estiver comprometido, o intruso obtém acesso a todas as suas permissões, o que coloca todo o seu sistema em risco. |
| **Como consertar?** | Reveja os utilizadores da lista e remova quaisquer que não necessitem absolutamente da função de administrador global. </br>Atribuir papéis privilegiados mais baixos a estes utilizadores. |
| **Prevenção** | Atribua aos utilizadores o papel menos privilegiado de que necessitam. |
| **Ação de mitigação no portal** | Retira a conta do seu papel privilegiado. |
| **Acionador** | Desencadeado se forem cumpridos dois critérios diferentes, pode configurar os dois. Primeiro, tens de atingir um certo limiar de administradores globais. Em segundo lugar, uma certa percentagem das suas atribuições totais de funções devem ser administradores globais. Se apenas encontrar uma destas medições, o alerta não aparece. |
| **Número mínimo de administradores globais** | Esta definição especifica o número de administradores globais, de 2 a 100, que considera serem poucos para a sua organização AZure AD. |
| **Percentagem de Administradores Globais** | Esta definição especifica a percentagem mínima de administradores que são administradores globais, de 0% a 100%, abaixo da qual não quer que a sua organização Azure AD diminua. |

### <a name="roles-are-being-activated-too-frequently"></a>As funções estão a ser ativadas com demasiada frequência.

| | |
| --- | --- |
| **Gravidade** | Baixo |
| **Por que recebo este alerta?** | Múltiplas ativações para o mesmo papel privilegiado pelo mesmo utilizador é um sinal de um ataque. |
| **Como consertar?** | Reveja os utilizadores da lista e certifique-se de que a duração de [ativação](pim-how-to-change-default-settings.md) para o seu papel privilegiado é definida o tempo suficiente para que eles executem as suas tarefas. |
| **Prevenção** | Certifique-se de que a duração de [ativação](pim-how-to-change-default-settings.md) das funções privilegiadas é definida o tempo suficiente para que os utilizadores realizem as suas tarefas.</br>[Requerem autenticação multi-factor](pim-how-to-change-default-settings.md) para funções privilegiadas que tenham contas partilhadas por vários administradores. |
| **Ação de mitigação no portal** | N/D |
| **Acionador** | Desencadeado se um utilizador ativar a mesma função privilegiada várias vezes num período determinado. Pode configurar tanto o período de tempo como o número de ativações. |
| **Prazo de renovação da ativação** | Esta definição especifica em dias, horas, minutos e segundo o período de tempo que pretende utilizar para rastrear renovações suspeitas. |
| **Número de renovações de ativação** | Esta definição especifica o número de ativações, de 2 a 100, nas quais gostaria de ser notificado, dentro do prazo escolhido. Pode alterar esta definição movendo o slider ou digitando um número na caixa de texto. |

## <a name="configure-security-alert-settings"></a>Configurar configurações de alerta de segurança

Pode personalizar alguns dos alertas de segurança na Gestão de Identidade Privilegiada para trabalhar com as necessidades e objetivos de segurança da sua organização. Siga estes passos para abrir as definições de alerta de segurança:

1. **Gestão de Identidade Privilegiada Aberta** em Azure AD.

1. Selecione **funções AD Azure**.

1. Selecione **Definições** e, em seguida, **Alertas**.

    ![Funções AD AZure - Configurações com alertas selecionados](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Selecione um nome de alerta para configurar a definição para esse alerta.

    ![Para o alerta selecionado, o painel de definições de alerta de segurança](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>Passos seguintes

- [Configurar definições de papel de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-change-default-settings.md)
