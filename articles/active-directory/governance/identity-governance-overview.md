---
title: Governança da Identidade - Diretório Ativo Azure [ Azure Ative Diretório ] Microsoft Docs
description: A Azure Ative Directory Identity Governance permite-lhe equilibrar a necessidade da sua organização de segurança e produtividade dos colaboradores com os processos e visibilidade certos.
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
ms.openlocfilehash: bd319dd6a83a392f6df26d07a58be22a9c8bdb61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063684"
---
# <a name="what-is-azure-ad-identity-governance"></a>O que é o Azure AD Identity Governance?

A Governação de Identidade do Diretório Ativo Azure (Azure AD) permite equilibrar a necessidade da sua organização de segurança e produtividade dos colaboradores com os processos e visibilidade certos. Fornece-lhe capacidades para garantir que as pessoas certas tenham o acesso certo aos recursos certos. Estas funcionalidades relacionadas com a Azure AD e enterprise Mobility + Security permitem-lhe mitigar o risco de acesso protegendo, monitorizando e auditando o acesso a ativos críticos -- garantindo ao mesmo tempo a produtividade dos colaboradores e parceiros empresariais.  

A Governação da Identidade dá às organizações a capacidade de fazer as seguintes tarefas entre colaboradores, parceiros de negócios e fornecedores, bem como através de serviços e aplicações tanto no local como nas nuvens:

- Governar o ciclo de vida da identidade
- Governar o ciclo de vida de acesso
- Acesso privilegiado seguro para administração

Especificamente, destina-se a ajudar as organizações a abordar estas quatro questões-chave:

- Que utilizadores devem ter acesso aos recursos?
- O que estão os utilizadores a fazer com esse acesso?
- Existem controlos organizacionais eficazes para gerir o acesso?
- Os auditores podem verificar se os controlos estão a funcionar?

## <a name="identity-lifecycle"></a>Ciclo de vida de identidade

A Governação da Identidade ajuda as organizações a alcançar um equilíbrio entre *a produtividade* - Quão rapidamente uma pessoa pode ter acesso aos recursos de que precisa, como quando se juntam à minha organização? E *segurança* - Como deve o seu acesso mudar ao longo do tempo, como devido a alterações ao estatuto laboral dessa pessoa?  A gestão do ciclo de vida identitário é a base para a Governação da Identidade, e uma governação eficaz em escala requer a modernização da infraestrutura de gestão do ciclo de vida identitário para aplicações.

![Ciclo de vida de identidade](./media/identity-governance-overview/identity-lifecycle.png)

Para muitas organizações, o ciclo de vida identitário dos colaboradores está ligado à representação desse utilizador num sistema hcm (gestão de capital humano).  O Azure AD Premium mantém automaticamente as identidades dos utilizadores para pessoas representadas no Workday tanto no Ative Directory como no Azure Ative Directory, conforme descrito no tutorial de fornecimento de [entrada do Workday.](../saas-apps/workday-inbound-tutorial.md)  O Azure AD Premium também inclui o [Microsoft Identity Manager,](/microsoft-identity-manager/)que pode importar registos de sistemas HCM no local, tais como SAP, Oracle eBusiness e Oracle PeopleSoft.

Cada vez mais, os cenários requerem colaboração com pessoas fora da sua organização. A colaboração [Azure AD B2B](/azure/active-directory/b2b/) permite-lhe partilhar de forma segura as aplicações e serviços da sua organização com utilizadores convidados e parceiros externos de qualquer organização, mantendo ao mesmo tempo o controlo sobre os seus próprios dados corporativos.  [A gestão de direitos da Azure AD](entitlement-management-overview.md) permite-lhe selecionar quais os utilizadores da organização que podem solicitar acesso e ser adicionados como hóspedes B2B ao diretório da sua organização, e garante que estes hóspedes são removidos quando já não precisam de acesso.

## <a name="access-lifecycle"></a>Ciclo de vida de acesso

As organizações precisam de um processo para gerir o acesso para além do que foi inicialmente previsto para um utilizador quando a identidade desse utilizador foi criada.  Além disso, as organizações empresariais têm de ser capazes de escalar de forma eficiente para poderem desenvolver e impor a política e os controlos de acesso de forma contínua.

![Ciclo de vida de acesso](./media/identity-governance-overview/access-lifecycle.png)

Normalmente, os delegados de TI acedem a decisões de aprovação a decisores empresariais.  Além disso, a TI pode envolver os próprios utilizadores.  Por exemplo, os utilizadores que acedem a dados confidenciais dos clientes na aplicação de marketing de uma empresa na Europa precisam de conhecer as políticas da empresa. Os utilizadores convidados podem desconhecer os requisitos de manuseamento de dados numa organização para a qual foram convidados.

As organizações podem automatizar o processo de ciclo de vida de acesso através de tecnologias como [grupos dinâmicos,](../users-groups-roles/groups-dynamic-membership.md)juntamente com o fornecimento de utilizadores a apps ou aplicações [SaaS](../saas-apps/tutorial-list.md) integradas com o [SCIM.](../app-provisioning/use-scim-to-provision-users-and-groups.md)  As organizações também podem controlar quais [os utilizadores convidados que têm acesso a aplicações no local.](../b2b/hybrid-cloud-to-on-premises.md)  Estes direitos de acesso podem então ser regularmente revistos através de avaliações recorrentes de [acesso ao Azure AD](access-reviews-overview.md).   [A gestão de direitos da Azure AD](entitlement-management-overview.md) também permite definir como os utilizadores solicitam acesso através de pacotes de membros de grupo e de equipa, funções de aplicação e sharePoint Online.

Quando um utilizador tenta aceder a aplicações, a Azure AD aplica as políticas de [Acesso Condicional.](/azure/active-directory/conditional-access/) Por exemplo, as políticas de Acesso Condicional podem incluir apresentar um [termos de utilização](../conditional-access/terms-of-use.md) e [garantir que o utilizador concordou com esses termos](../conditional-access/require-tou.md) antes de poder aceder a uma aplicação.

## <a name="privileged-access-lifecycle"></a>Ciclo de vida privilegiado de acesso

Historicamente, o acesso privilegiado tem sido descrito por outros fornecedores como uma capacidade separada da Governação da Identidade. No entanto, na Microsoft, pensamos que governar o acesso privilegiado é uma parte fundamental da Governação da Identidade -- especialmente dado o potencial de utilização indevida associado a esses direitos de administrador pode causar a uma organização. Os trabalhadores, vendedores e empreiteiros que assumam direitos administrativos têm de ser regidos.

![Ciclo de vida privilegiado de acesso](./media/identity-governance-overview/privileged-access-lifecycle.png)

A [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) fornece controlos adicionais adaptados à garantia de direitos de acesso para recursos, através da Azure AD, Azure e outros Serviços Online da Microsoft.  O acesso just-in-time e as capacidades de alerta de mudança de papéis fornecidas pela Azure AD PIM, além da autenticação multifactor e do Acesso Condicional, fornecem um conjunto abrangente de controlos de governação para ajudar a garantir os recursos da sua empresa (diretório, Escritório 365, e funções de recurso Azure). Tal como acontece com outras formas de acesso, as organizações podem utilizar avaliações de acesso para configurar a recertificação de acesso recorrente para todos os utilizadores nas funções de administrador.

## <a name="getting-started"></a>Introdução

Confira o separador De Saída da **Governança de Identidade** no portal Azure para começar a utilizar a gestão de direitos, avaliações de acesso, Gestão de Identidade Privilegiada e Termos de utilização.

![Governança de Identidade a começar](./media/identity-governance-overview/getting-started.png)


Se tiver algum feedback sobre as funcionalidades de Governança de Identidade, clique em **obter feedback?** A equipa revê regularmente o seu feedback.

Embora não exista uma solução ou recomendação perfeita para cada cliente, os seguintes guias de configuração também fornecem as políticas de base que a Microsoft recomenda que siga para garantir uma mão de obra mais segura e produtiva.

- [Configurações de acesso aos dispositivos e identidade](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [A proteger o acesso privilegiado](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Apêndice - funções menos privilegiadas para a gestão de funcionalidades de Governança de Identidade

É uma boa prática usar o papel menos privilegiado para executar tarefas administrativas na Governação da Identidade. Recomendamos que utilize o AZURE AD PIM para ativar uma função necessária para executar estas tarefas. Seguem-se as funções de diretório menos privilegiadas para configurar as funcionalidades de Governança de Identidade:

| Funcionalidade | Papel menos privilegiado |
| ------- | --------------------- |
| Gestão de direitos | Administrador de utilizador (com exceção da adição de sites SharePoint Online a catálogos, o que requer administrador global) |
| Revisões de acesso | Administrador de utilizadores (com exceção das revisões de acesso das funções Azure ou Azure AD, que requer administrador privilegiado de funções) |
|Privileged Identity Management | Administrador privilegiado |
| Termos de utilização | Administrador de segurança ou administrador de acesso condicional |

## <a name="next-steps"></a>Passos seguintes

- [O que é a gestão de direitos do Azure AD?](entitlement-management-overview.md)
- [O que são as avaliações de acesso da Azure AD?](access-reviews-overview.md)
- [O que é o Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [O que posso fazer com os Termos de utilização?](active-directory-tou.md)


