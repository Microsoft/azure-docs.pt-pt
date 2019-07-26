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
ms.date: 04/29/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f09a8c2f8caae3cbb182cf2dc4621deb95f7e5c
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499667"
---
# <a name="what-is-azure-ad-identity-governance"></a>O que é Azure AD Identity Governance?

A governança de identidade Azure Active Directory (Azure AD) permite equilibrar a necessidade da sua organização de segurança e produtividade dos funcionários com os processos e a visibilidade certos. Ele fornece recursos para garantir que os usuários certos tenham o acesso certo aos recursos certos, além de permitir que você proteja, monitore e audite o acesso a ativos críticos, garantindo, ao mesmo tempo, a produtividade dos funcionários.  

A governança de identidade fornece às organizações a capacidade de realizar as seguintes tarefas em funcionários, parceiros de negócios e fornecedores, além de serviços e aplicativos:

- Controlar o ciclo de vida da identidade
- Controle do ciclo de vida de acesso
- Administração segura

Especificamente, destina-se a ajudar as organizações a lidar com essas quatro perguntas principais:

- Quais usuários devem ter acesso a quais recursos?
- O que os usuários estão fazendo com esse acesso?
- Há controles organizacionais efetivos para gerenciar o acesso?
- Os auditores podem verificar se os controles estão funcionando?

## <a name="identity-lifecycle"></a>Ciclo de vida da identidade

A governança de identidade ajuda as organizações a alcançar um equilíbrio entre a *produtividade* – com que rapidez uma pessoa pode ter acesso aos recursos de que precisam, como quando elas se unem à minha organização? E *segurança* -como o acesso deve ser alterado ao longo do tempo, por exemplo, devido a alterações no status de emprego dessa pessoa?  O gerenciamento do ciclo de vida da identidade é a base para governança de identidade, e a governança eficaz em escala requer modernização da infraestrutura de gerenciamento do ciclo de vida de identidade para aplicativos.

![Ciclo de vida da identidade](./media/identity-governance-overview/identity-lifecycle.png)

Para muitas organizações, o ciclo de vida da identidade para os funcionários está vinculado à representação desse usuário em um sistema HCM (gerenciamento de capital humano).  Azure AD Premium mantém automaticamente as identidades de usuário para as pessoas representadas no workday tanto no Active Directory quanto no Azure Active Directory, conforme descrito no [tutorial de provisionamento de entrada do workday (versão prévia)](../saas-apps/workday-inbound-tutorial.md).  Azure AD Premium também inclui [Microsoft Identity Manager](/microsoft-identity-manager/), que pode importar registros de sistemas HCM locais, como SAP, Oracle eBusiness e Oracle PeopleSoft.

Cada vez mais, os cenários exigem colaboração com pessoas fora da sua organização. A colaboração [B2B do Azure ad](/azure/active-directory/b2b/) permite que você compartilhe com segurança os aplicativos e serviços de sua organização com usuários convidados e parceiros externos de qualquer organização, mantendo o controle sobre seus próprios dados corporativos.

## <a name="access-lifecycle"></a>Ciclo de vida de acesso

As organizações precisam de um processo para gerenciar o acesso além do que foi inicialmente provisionado para um usuário quando a identidade desse usuário foi criada.  Além disso, as organizações empresariais precisam ser capazes de dimensionar com eficiência para poder desenvolver e impor a política de acesso e os controles de forma contínua.

![Ciclo de vida de acesso](./media/identity-governance-overview/access-lifecycle.png)

Normalmente, ele delega decisões de aprovação de acesso para os tomadores de decisões de negócios.  Além disso, ele pode envolver os próprios usuários.  Por exemplo, os usuários que acessam dados confidenciais do cliente no aplicativo de marketing de uma empresa na Europa precisam conhecer as políticas da empresa. Os usuários convidados podem não estar cientes dos requisitos de manuseio de dados em uma organização para a qual foram convidados.

As organizações podem automatizar o processo de ciclo de vida de acesso por meio de tecnologias como [grupos dinâmicos](../users-groups-roles/groups-dynamic-membership.md), juntamente com o provisionamento de usuário para [aplicativos SaaS](../saas-apps/tutorial-list.md) ou [aplicativos integrados ao scim](../manage-apps/use-scim-to-provision-users-and-groups.md).  As organizações também podem controlar quais [usuários convidados têm acesso a aplicativos locais](../b2b/hybrid-cloud-to-on-premises.md).  Esses direitos de acesso podem ser examinados regularmente usando revisões recorrentes de [acesso do Azure ad](access-reviews-overview.md).

Quando um usuário tenta acessar aplicativos, o Azure AD impõe políticas de [acesso condicional](/azure/active-directory/conditional-access/) . Por exemplo, as políticas de acesso condicional podem incluir a exibição de [termos de uso](../conditional-access/terms-of-use.md) e [a garantia de que o usuário concordou com esses termos](../conditional-access/require-tou.md) antes de poder acessar um aplicativo.

## <a name="privileged-access-lifecycle"></a>Ciclo de vida de acesso privilegiado

Historicamente, o acesso privilegiado foi descrito por outros fornecedores como um recurso separado da governança de identidade. No entanto, na Microsoft, acreditamos que o controle de acesso privilegiado é uma parte fundamental do controle de identidades, especialmente considerando o potencial para o uso inadequado de usuários associados a esses direitos de administrador pode causar uma organização. Os funcionários, fornecedores e prestadores de contrato que assumem direitos administrativos precisam ser regidos.

![Ciclo de vida de acesso privilegiado](./media/identity-governance-overview/privileged-access-lifecycle.png)

Azure AD Privileged Identity Management (PIM) fornece controles adicionais adaptados para proteger os direitos de acesso para recursos, no Azure AD, no Azure e em outros serviços online da Microsoft.  O acesso just-in-time e os recursos de alerta de alteração de função fornecidos pelo Azure AD PIM, além da autenticação multifator e do acesso condicional, fornecem um conjunto abrangente de controles de governança para ajudar a proteger os recursos da sua empresa (diretório, Office 365 e funções de recurso do Azure). Assim como ocorre com outras formas de acesso, as organizações podem usar as revisões de acesso para configurar a recertificação de acesso recorrente para todos os usuários em funções de administrador.

## <a name="getting-started"></a>Introdução

Embora não haja uma solução perfeita ou recomendação para cada cliente, as configurações a seguir fornecem um guia para as políticas de linha de base que a Microsoft recomenda seguir para garantir uma força de ti mais segura e produtiva.

- [Configurações de acesso de dispositivos e identidade](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [A proteger o acesso privilegiado](../users-groups-roles/directory-admin-roles-secure.md)

Você também pode conferir a guia introdução do **controle de identidade** no portal do Azure para começar a usar o gerenciamento de direitos, revisões de acesso, Privileged Identity Management e termos de uso.

![Introdução à governança de identidade](./media/identity-governance-overview/getting-started.png)

## <a name="next-steps"></a>Passos Seguintes

- [O que é o gerenciamento de direitos do AD do Azure? (Pré-visualização)](entitlement-management-overview.md)
- [O que são as revisões de acesso do Azure AD?](access-reviews-overview.md)
- [O que é Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [O que posso fazer com os Termos de Utilização?](active-directory-tou.md)
