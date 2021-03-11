---
title: Acesso externo seguro com grupos no Azure Ative Directory e Microsoft 365
description: O Azure Ative Directory e os Grupos Microsoft 365 podem ser utilizados para aumentar a segurança quando os utilizadores externos acedem aos seus recursos.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d9d63c7a703987d7b17e6e03d8b5596d5f1dfa5
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560621"
---
# <a name="securing-external-access-with-groups"></a>Garantir o acesso externo com grupos 

Os grupos são uma parte essencial de qualquer estratégia de controlo de acessos. Os grupos de segurança Azure Ative Directory (Azure AD) e os grupos Microsoft 365 (M365) podem ser usados como base para garantir o acesso aos recursos.

Os grupos são a melhor opção para utilizar como base para os seguintes mecanismos de controlo de acesso:

* Políticas de Acesso Condicional

* Pacotes de acesso à gestão de direitos 

* Acesso aos recursos M365, Microsoft Teams e sites SharePoint

Os grupos têm as seguintes funções:

* Proprietários – Os proprietários do grupo gerem as configurações do grupo e a sua adesão.

* Membros – Deputados que herdam as permissões e acessos atribuídos ao grupo.

* Convidados – Os hóspedes são membros de fora da sua organização. 

## <a name="determine-your-group-strategy"></a>Determine a sua estratégia de grupo

Ao desenvolver a sua estratégia de grupo para garantir o acesso externo aos seus recursos, considere [a sua postura de segurança desejada](1-secure-access-posture.md) para determinar as seguintes escolhas.

* **Quem deve ser capaz de criar grupos?** Deseja que apenas os administradores criem grupos, ou quer que os colaboradores e utilizadores externos também criem estes grupos.

   * *Por predefinição, qualquer membro do inquilino pode criar grupos de segurança Azure AD*. 

      * Pode [restringir o acesso ao portal para não administradores](../develop/howto-restrict-your-app-to-a-set-of-users.md) e desativar a capacidade de criação de grupos no [PowerShell.](../enterprise-users/groups-troubleshooting.md) 

      * Também pode [configurar a gestão do grupo self-service no Azure Ative Directory](../enterprise-users/groups-self-service-management.md). 

   * *Por predefinição, todos os utilizadores podem criar Grupos M365 e os grupos estão abertos para todos os utilizadores (internos e externos) do seu inquilino aderirem.* 

      * [Pode restringir a criação do Grupo Microsoft 365](/microsoft-365/solutions/manage-creation-of-groups) aos membros de um determinado grupo de segurança. Utilize o Windows PowerShell para configurar esta definição. 

* **Quem deve ser capaz de convidar as pessoas para grupos?** Todos os membros do grupo podem adicionar outros membros, ou só os proprietários do grupo podem adicionar membros?

* **Quem pode ser convidado para grupos?** Por predefinição, os utilizadores externos podem ser adicionados aos grupos. 

### <a name="assign-users-to-groups"></a>Atribuir utilizadores a grupos

Os utilizadores podem ser atribuídos a grupos tanto manualmente com base nos atributos do utilizador no seu objeto de utilizador, como em outros critérios. Os utilizadores só podem ser atribuídos a grupos dinamicamente com base nos seus atributos.

Por exemplo, pode atribuir utilizadores a grupos com base nas suas:

* título ou departamento de emprego específico

* organização parceira a que pertencem (manualmente, ou através de organizações conectadas)

* tipo de utilizador (Membro ou Convidado)

* participação num projeto específico (manualmente)

* localização

Os grupos dinâmicos podem conter utilizadores ou dispositivos, mas não ambos. Adiciona consultas com base em atributos do utilizador para atribuir utilizadores ao grupo dinâmico. O exemplo abaixo mostra consultas que adicionam utilizadores ao grupo se forem membros (não convidados) e no departamento financeiro.

![Screenshot de configurar regras de adesão dinâmica.](media/secure-external-access/4-dynamic-membership-rules.png)

Para obter mais informações sobre grupos dinâmicos, consulte [Criar ou atualizar um grupo dinâmico no Diretório Ativo Azure.](../enterprise-users/groups-create-rule.md)

### <a name="do-not-use-groups-for-multiple-purposes"></a>Não utilize grupos para fins múltiplos

Ao utilizar grupos para fins de segurança ou acesso a recursos, é importante que tenham uma única função. Se um grupo é usado para conceder acesso a recursos, não deve ser usado para qualquer outro propósito. Se um grupo for utilizado para fins genéricos, como definir a localização ou a adesão à equipa, também não deve ser usado para garantir o acesso. 

Recomendamos uma convenção de nomeação para grupos de segurança que torne o propósito claro. Por exemplo:

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>Tipos de grupos

Tanto os grupos de segurança AZure AD como os grupos Microsoft 365 podem ser criados a partir do portal AD Azure ou do portal de administração M365. Ambos os tipos podem ser usados como base para garantir o acesso externo:

|Considerações | Grupos de segurança AZure AD (manual e dinâmico)| Microsoft 365 Grupos |
| - | - | - |
| O que pode o grupo conter?| Utilizadores<br>Grupos<br>Principais de serviço<br>Dispositivos| Apenas utilizadores |
| Onde é criado o grupo?| Portal Azure AD<br>Portal M365 (se for ativado por correio)<br>PowerShell<br>Microsoft Graph<br>Portal do utilizador final| Portal M365<br>Portal Azure AD<br>PowerShell<br>Microsoft Graph<br>Nas aplicações microsoft 365 |
| Quem cria por defeito?| Administradores <br>Utilizadores finais| Administradores<br>Utilizadores finais |
| Quem pode ser adicionado por defeito?| Utilizadores internos (membros)| Membros do inquilino e hóspedes de qualquer organização |
| A que dá acesso?| Apenas recursos aos quais é atribuído.| Todos os recursos relacionados com o grupo:<br>(Caixa de correio de grupo, site, equipa, chats e outros recursos M365 incluídos)<br>Quaisquer outros recursos a que grupo é adicionado |
| Pode ser usado com| Acesso Condicional<br>Gestão de Direitos<br>Licenciamento de grupo| Acesso Condicional<br>Gestão de Direitos<br>Etiquetas de confidencialidade |



Utilize grupos Microsoft 365 para criar e gerir um conjunto de recursos microsoft 365, como uma Equipa e seus sites e conteúdos associados. São uma ótima escolha para um esforço baseado em projetos. 

 

## <a name="azure-ad-security-groups"></a>Grupos de segurança do Azure AD 

[Os grupos de segurança AZure AD](./active-directory-manage-groups.md) podem conter utilizadores ou dispositivos e podem ser usados para gerir o acesso a 

* Recursos azure como aplicações microsoft 365, aplicações personalizadas e aplicativos de software como um serviço (SaaS) como o ServiceNow do Dropbox.

* Dados e subscrições do Azure.

* Serviços Azure.

Os grupos de segurança Azure AD também podem ser usados para:

* atribuir licenças para serviços como M365, Dynamics 365 e Mobilidade Empresarial e Segurança. Para obter mais informações, consulte [o licenciamento baseado em grupo.](./active-directory-licensing-whatis-azure-portal.md)

* atribuir permissões elevadas. Para obter mais informações, consulte [Utilize grupos de nuvem para gerir atribuições de funções (pré-visualização).](../roles/groups-concept.md) 

Para criar um grupo [no portal Azure](./active-directory-groups-create-azure-portal.md) navegue para O Diretório Ativo Azure e, em seguida, para Grupos. Também pode criar grupos de segurança Azure AD utilizando [cmdlets PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md). 

> [!NOTE]
> Um grupo de segurança pode ser usado para a atribuição de até 1500 aplicações, mas não mais. 

![Screenshot de criar um grupo de segurança.](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> **Para criar um grupo de segurança ativado por correio, vá ao [microsoft 365 Admin center](https://admin.microsoft.com/)**. Não é possível criá-lo no portal AD Azure. 
<br>Deve permitir um grupo de segurança para o correio no momento da criação. Não pode permitir mais tarde.

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>Organizações híbridas e grupos de segurança AZure AD

As organizações híbridas têm uma infraestrutura no local e uma infraestrutura em nuvem AZure. Muitas organizações híbridas que usam o Ative Directory criam os seus grupos de segurança no local e sincronizam-nos com a nuvem. Utilizando este método, apenas os utilizadores no ambiente no local podem ser adicionados aos grupos de segurança.

**Proteja a sua infraestrutura no local de compromisso, uma vez que uma violação no local pode ser usada para ter acesso ao seu inquilino Microsoft 365.** Consulte [a Proteção da Microsoft 365 contra ataques no local](./protect-m365-from-on-premises-attacks.md) para obter orientação.

## <a name="microsoft-365-groups"></a>Microsoft 365 Grupos

[Os Grupos Microsoft 365](/microsoft-365/admin/create-groups/office-365-groups) são o serviço de subscrição fundamental que impulsiona todo o acesso através da M365. Podem ser criados a partir do [portal Azure,](https://portal.azure.com/)ou do [portal M365.](https://admin.microsoft.com/) Quando um grupo M365 é criado, você concede acesso a um grupo de recursos usados para colaborar. Consulte [a visão geral dos grupos Microsoft 365 para administradores](/microsoft-365/admin/create-groups/office-365-groups) para obter uma listagem completa destes recursos.

Os grupos M365 têm as seguintes nuances para as suas funções

* **Proprietários** - Os proprietários do grupo podem adicionar ou remover membros e ter permissões únicas como a capacidade de apagar conversas da caixa de entrada partilhada ou alterar as definições de grupo. Os proprietários do grupo podem renomear o grupo, atualizar a descrição ou imagem e muito mais.

* **Membros** - Os membros podem aceder a tudo no grupo, mas não podem alterar as configurações do grupo. Por predefinição, os membros do grupo podem convidar os hóspedes a juntarem-se ao seu grupo, embora possa [controlar essa definição](/microsoft-365/admin/create-groups/manage-guest-access-in-groups).

* **Hóspedes** - Os hóspedes do grupo são membros de fora da sua organização. Os hóspedes por defeito têm alguns limites para a funcionalidade em Equipas.

 

### <a name="m365-group-settings"></a>Configurações do Grupo M365

Selecione pseudónimo de e-mail, privacidade e se deve ativar o grupo para equipas no momento da configuração. 

![Screenshot da edição das definições do Grupo Microsoft 365](media/secure-external-access/4-edit-group-settings.png)

Após a configuração, adicione membros e configurar definições para utilização de e-mail, etc.

### <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a garantia do acesso externo aos recursos. Recomendamos que tome as ações na ordem listada.

1. [Determine a sua desejada postura de segurança para acesso externo](1-secure-access-posture.md)

2. [Descubra o seu estado atual](2-secure-access-current-state.md)

3. [Criar um plano de governação](3-secure-access-plan.md)

4. [Utilize grupos para segurança](4-secure-access-groups.md) (Está aqui.)

5. [Transição para Azure AD B2B](5-secure-access-b2b.md)

6. [Acesso seguro com Gestão de Direitos](6-secure-access-entitlement-managment.md)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md)

8. [Acesso seguro com etiquetas de sensibilidade](8-secure-access-sensitivity-labels.md)

9. [Acesso seguro a Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)