---
title: Governação de identidade - Azure Active Directory | Documentos da Microsoft
description: Governação de identidade do Azure Active Directory permite-lhe equilibrar a necessidade da sua organização de segurança e o funcionário a produtividade com os processos de direito e a visibilidade.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7872a43d5000ddb1ba581f76358c8001902ab56b
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579721"
---
# <a name="what-is-azure-ad-identity-governance"></a>O que é a governação de identidade do Azure AD?

Governação de identidade do Azure Active Directory (Azure AD) permite-lhe equilibrar a necessidade da sua organização de segurança e o funcionário a produtividade com os processos de direito e a visibilidade. Ele fornece recursos para garantir que os utilizadores certos tenham o acesso aos recursos certos e permite-lhe proteger, monitorize e audite acesso a recursos críticos, garantindo também a produtividade dos funcionários.  

Governação de identidade dar às organizações a capacidade de fazer o seguinte em funcionários, parceiros de negócios e fornecedores e serviços e aplicações:

- Governar o ciclo de vida de identidade
- Governar o ciclo de vida de acesso
- Administração segura

Especificamente, seu objetivo é ajudar as organizações abordar essas quatro perguntas de chaves:

- Os utilizadores que devem ter acesso a quais recursos?
- O que esses usuários fazendo com que o acesso?
- Existem os controlos efetivos organizacionais para gerir o acesso?
- Podem auditores Certifique-se de que os controles estão a funcionar?

## <a name="identity-lifecycle"></a>Ciclo de vida de identidade

Governação de identidade ajuda as organizações a atingir um equilíbrio entre *produtividade* -quão rapidamente pode uma pessoa tem acesso aos recursos que precisa, como quando eles Junte-se a minha organização? E *segurança* -como deve respetivo acesso alterar ao longo do tempo, como devido a alterações ao estado de emprego essa pessoa?  Gerenciamento de ciclo de vida de identidade é a base de governação de identidade e, em vigor governação em escala requer modernizar a infraestrutura de gestão do ciclo de vida de identidade para aplicações.

Para muitas organizações, o ciclo de vida de identidade para os funcionários é associado à representação de usuário num sistema HCM (human capital management).  O Azure AD Premium mantém automaticamente as identidades dos utilizadores para as pessoas representadas no Workday no Active Directory e Azure Active Directory, conforme descrito no [tutorial de aprovisionamento (pré-visualização) entrada Workday](../saas-apps/workday-inbound-tutorial.md).  Também inclui o Azure AD Premium [do Microsoft Identity Manager](/microsoft-identity-manager/), que pode importar registos de sistemas HCM no local, como SAP, Oracle eBusiness e o Oracle PeopleSoft.

Cenários exigem cada vez mais, colaboração com pessoas fora da sua organização. [O Azure AD B2B](/azure/active-directory/b2b/) colaboração permite-lhe partilhar em segurança as aplicações e serviços da sua organização com utilizadores convidados e parceiros externos a partir de qualquer organização, e manter o controlo sobre os seus próprios dados empresariais.

## <a name="access-lifecycle"></a>Ciclo de vida de acesso

As organizações precisam de um processo para gerir o acesso além do que foi inicialmente aprovisionado para um utilizador quando a identidade do utilizador que foi criada.  Além disso, as empresas precisam de ser capaz de dimensionar com eficiência para poder desenvolver e impor a política de acesso e controles de forma contínua.

Normalmente, os delegados IT decisões de aprovação para os tomadores de decisões de acesso.  Além disso, IT pode envolver os próprios usuários.  Por exemplo, os utilizadores que acedem a dados confidenciais do cliente no aplicativo de marketing de uma empresa na Europa precisam de saber as políticas da empresa. Os utilizadores convidados podem ser não têm conhecimento dos requisitos de processamento de dados numa organização aos quais foram convidados.

As organizações podem automatizar o processo de ciclo de vida de acesso por meio de tecnologias como [grupos dinâmicos](../users-groups-roles/groups-dynamic-membership.md), conjugadas com o aprovisionamento do utilizador [aplicações SaaS](../saas-apps/tutorial-list.md) ou [as aplicações integradas com SCIM](../manage-apps/use-scim-to-provision-users-and-groups.md).  As organizações também podem controlar quais [os utilizadores convidados têm acesso a aplicações no local](../b2b/hybrid-cloud-to-on-premises.md).  Estes podem de direitos de acesso, em seguida, ser regularmente revisados recorrente [revisões de acesso do Azure AD](access-reviews-overview.md).

Quando um utilizador tenta aceder a aplicações, o Azure AD impõe [acesso condicional](/azure/active-directory/conditional-access/) políticas. Por exemplo, políticas de acesso condicional podem incluir exibindo um [termos de utilização](active-directory-tou.md) e [garantir que o usuário concordou esses termos](../conditional-access/require-tou.md) antes de poder aceder a uma aplicação.

## <a name="privileged-access-lifecycle"></a>Ciclo de vida do acesso privilegiado

Historicamente, acesso privilegiado foi descrito por outros fornecedores como um recurso separado de governação de identidade. No entanto, na Microsoft, acreditamos que regem o acesso privilegiado é uma parte fundamental de governação de identidade – principalmente com o potencial para uso indevido associado a esses direitos podem fazer com que uma organização de administrador. Os funcionários, fornecedores e prestadores de serviço que assumir de direitos administrativos tem de ser regidas.

O Azure AD Privileged Identity Management (PIM) fornece controlos adicionais adaptados para proteger o acesso de direitos para os recursos, no Azure AD, Azure e a outros serviços Online da Microsoft.  O acesso just-in-time e a alteração de funções alertas recursos fornecidos pelo Azure AD PIM, além de autenticação multifator e acesso condicional, fornecem que um conjunto abrangente de controles de governação para ajudar a proteger recursos da empresa (diretório, Office 365 e funções de recursos do Azure). Tal como acontece com outras formas de acesso, as organizações podem utilizar as revisões de acesso para configurar recorrentes recertification de acesso para todos os utilizadores nas funções de administrador.

## <a name="getting-started"></a>Introdução

Embora não haja nenhuma recomendação para cada cliente ou a solução perfeita, as seguintes configurações fornecem um guia para que políticas de linha de base, a Microsoft recomenda para garantir uma força de trabalho mais segura e produtiva.

- [Configurações de acesso de dispositivos e identidade](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [A proteger o acesso privilegiado](../users-groups-roles/directory-admin-roles-secure.md)


### <a name="access-reviews"></a>Revisões de acesso

- [O que é uma revisão de acesso?](access-reviews-overview.md)
- [Gerir o acesso de utilizador com as revisões de acesso](manage-user-access-with-access-reviews.md)
- [Gerir o acesso de convidado com as revisões de acesso](manage-guest-access-with-access-reviews.md)
- [Iniciar uma revisão de acesso de funções do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)

### <a name="terms-of-use"></a>Termos de utilização

- [O que posso fazer com os Termos de Utilização?](active-directory-tou.md)

### <a name="privileged-identity-management"></a>Privileged Identity Management

- [O que é o Azure AD PIM?](../privileged-identity-management/pim-configure.md)
