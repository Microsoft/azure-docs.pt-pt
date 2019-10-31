---
title: Governança de identidade-Azure Active Directory | Microsoft Docs
description: Azure Active Directory Identity Governance permite que você Equilibre a necessidade da sua organização de segurança e produtividade dos funcionários com os processos e a visibilidade certos.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: c90052d1d75ba65a8e0b0e7a2cd26a279019a82d
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173152"
---
# <a name="what-is-azure-ad-identity-governance"></a>O que é o Azure AD Identity Governance?

A governança de identidade Azure Active Directory (Azure AD) permite equilibrar a necessidade da sua organização de segurança e produtividade dos funcionários com os processos e a visibilidade certos. Ele fornece recursos para garantir que as pessoas certas tenham o acesso certo aos recursos certos. Esses recursos relacionados ao Azure AD e Enterprise Mobility + Security permitem que você reduza o risco de acesso protegendo, monitorando e auditando o acesso a ativos críticos, garantindo, ao mesmo tempo, a produtividade dos funcionários e dos parceiros comerciais.  

A governança de identidade fornece às organizações a capacidade de realizar as seguintes tarefas entre funcionários, parceiros de negócios e fornecedores e entre serviços e aplicativos locais e em nuvens:

- Governe o ciclo de vida de identidade
- Controle do ciclo de vida de acesso
- Proteger o acesso privilegiado para administração

Especificamente, destina-se a ajudar as organizações a lidar com essas quatro perguntas principais:

- Quais usuários devem ter acesso a quais recursos?
- O que os usuários estão fazendo com esse acesso?
- Há controles organizacionais efetivos para gerenciar o acesso?
- Os auditores podem verificar se os controles estão funcionando?

## <a name="identity-lifecycle"></a>Ciclo de vida da identidade

A governança de identidade ajuda as organizações a alcançar um equilíbrio entre a *produtividade* – com que rapidez uma pessoa pode ter acesso aos recursos de que precisam, como quando elas se unem à minha organização? E *segurança* -como o acesso deve ser alterado ao longo do tempo, por exemplo, devido a alterações no status de emprego dessa pessoa?  O gerenciamento do ciclo de vida da identidade é a base para governança de identidade, e a governança eficaz em escala requer modernização da infraestrutura de gerenciamento do ciclo de vida de identidade para aplicativos.

![Ciclo de vida da identidade](./media/identity-governance-overview/identity-lifecycle.png)

Para muitas organizações, o ciclo de vida da identidade para os funcionários está vinculado à representação desse usuário em um sistema HCM (gerenciamento de capital humano).  Azure AD Premium mantém automaticamente as identidades de usuário para as pessoas representadas no workday tanto no Active Directory quanto no Azure Active Directory, conforme descrito no [tutorial de provisionamento de entrada do workday](../saas-apps/workday-inbound-tutorial.md).  Azure AD Premium também inclui [Microsoft Identity Manager](/microsoft-identity-manager/), que pode importar registros de sistemas HCM locais, como SAP, Oracle eBusiness e Oracle PeopleSoft.

Cada vez mais, os cenários exigem colaboração com pessoas fora da sua organização. A colaboração [B2B do Azure ad](/azure/active-directory/b2b/) permite que você compartilhe com segurança os aplicativos e serviços de sua organização com usuários convidados e parceiros externos de qualquer organização, mantendo o controle sobre seus próprios dados corporativos.  O [Gerenciamento de direitos do Azure ad](entitlement-management-overview.md) permite que você Selecione quais usuários da organização têm permissão para solicitar acesso e são adicionados como convidados B2B ao diretório da sua organização e garante que esses convidados sejam removidos quando não precisarem mais de acesso.

## <a name="access-lifecycle"></a>Ciclo de vida de acesso

As organizações precisam de um processo para gerenciar o acesso além do que foi inicialmente provisionado para um usuário quando a identidade desse usuário foi criada.  Além disso, as organizações empresariais precisam ser capazes de dimensionar com eficiência para poder desenvolver e impor a política de acesso e os controles de forma contínua.

![Ciclo de vida de acesso](./media/identity-governance-overview/access-lifecycle.png)

Normalmente, ele delega decisões de aprovação de acesso para os tomadores de decisões de negócios.  Além disso, ele pode envolver os próprios usuários.  Por exemplo, os usuários que acessam dados confidenciais do cliente no aplicativo de marketing de uma empresa na Europa precisam conhecer as políticas da empresa. Os usuários convidados podem não estar cientes dos requisitos de manuseio de dados em uma organização para a qual foram convidados.

As organizações podem automatizar o processo de ciclo de vida de acesso por meio de tecnologias como [grupos dinâmicos](../users-groups-roles/groups-dynamic-membership.md), juntamente com o provisionamento de usuário para [aplicativos SaaS](../saas-apps/tutorial-list.md) ou [aplicativos integrados ao scim](../manage-apps/use-scim-to-provision-users-and-groups.md).  As organizações também podem controlar quais [usuários convidados têm acesso a aplicativos locais](../b2b/hybrid-cloud-to-on-premises.md).  Esses direitos de acesso podem ser examinados regularmente usando revisões recorrentes de [acesso do Azure ad](access-reviews-overview.md).   O [Gerenciamento de direitos do Azure ad](entitlement-management-overview.md) também permite que você defina como os usuários solicitam acesso entre pacotes de associações de grupo e de equipe, funções de aplicativo e funções do SharePoint Online.

Quando um usuário tenta acessar aplicativos, o Azure AD impõe políticas de [acesso condicional](/azure/active-directory/conditional-access/) . Por exemplo, as políticas de acesso condicional podem incluir a exibição de [termos de uso](../conditional-access/terms-of-use.md) e [a garantia de que o usuário concordou com esses termos](../conditional-access/require-tou.md) antes de poder acessar um aplicativo.

## <a name="privileged-access-lifecycle"></a>Ciclo de vida de acesso privilegiado

Historicamente, o acesso privilegiado foi descrito por outros fornecedores como um recurso separado da governança de identidade. No entanto, na Microsoft, acreditamos que o controle de acesso privilegiado é uma parte fundamental do controle de identidades, especialmente considerando o potencial para o uso inadequado de usuários associados a esses direitos de administrador pode causar uma organização. Os funcionários, fornecedores e prestadores de contrato que assumem direitos administrativos precisam ser regidos.

![Ciclo de vida de acesso privilegiado](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure ad Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) fornece controles adicionais adaptados para proteger os direitos de acesso para recursos, no Azure AD, no Azure e em outros serviços online da Microsoft.  O acesso just-in-time e os recursos de alerta de alteração de função fornecidos pelo Azure AD PIM, além da autenticação multifator e do acesso condicional, fornecem um conjunto abrangente de controles de governança para ajudar a proteger os recursos da sua empresa (diretório, Office 365 e funções de recurso do Azure). Assim como ocorre com outras formas de acesso, as organizações podem usar as revisões de acesso para configurar a recertificação de acesso recorrente para todos os usuários em funções de administrador.

## <a name="getting-started"></a>Introdução

Confira a guia introdução do **controle de identidade** no portal do Azure para começar a usar o gerenciamento de direitos, revisões de acesso, Privileged Identity Management e termos de uso.

![Introdução à governança de identidade](./media/identity-governance-overview/getting-started.png)


Se você tiver algum comentário sobre os recursos de governança de identidade, clique em **comentários obtidos?** no portal do Azure para enviar seus comentários. A equipe revisa seus comentários regularmente.

Embora não haja uma solução perfeita ou recomendação para todos os clientes, os seguintes guias de configuração também fornecem as políticas de linha de base que a Microsoft recomenda que você siga para garantir uma força de ti mais segura e produtiva.

- [Configurações de acesso de dispositivo e identidade](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [A proteger o acesso privilegiado](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Apêndice-funções com privilégios mínimos para gerenciar em recursos de governança de identidade

É uma prática recomendada usar a função menos privilegiada para executar tarefas administrativas no controle de identidade. Recomendamos que você use o Azure AD PIM para ativar uma função conforme necessário para executar essas tarefas. A seguir estão as funções de diretório menos privilegiadas para configurar os recursos de governança de identidade:

| Funcionalidade | Função com privilégios mínimos |
| ------- | --------------------- |
| Gestão de direitos | Administrador do usuário (com exceção da adição de sites do SharePoint Online a catálogos, que requer administrador global) |
| Revisões de acesso | Administrador do usuário (com exceção das revisões de acesso das funções do Azure ou do Azure AD, que exige administrador de função com privilégios) |
|Privileged Identity Management | Administrador de função com privilégios |
| Termos de utilização | Administradores de segurança ou administrador de acesso condicional |

## <a name="next-steps"></a>Passos seguintes

- [O que é a gestão de direitos do Azure AD?](entitlement-management-overview.md)
- [O que são as revisões de acesso do Azure AD?](access-reviews-overview.md)
- [O que é Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [O que posso fazer com os Termos de Utilização?](active-directory-tou.md)


