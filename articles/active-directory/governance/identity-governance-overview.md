---
title: Governação da Identidade - Diretório Ativo Azure / Microsoft Docs
description: O Azure Ative Directory Identity Governance permite-lhe equilibrar a necessidade de segurança e produtividade dos colaboradores da sua organização com os processos e visibilidade certos.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f65cd11f61c25af205fa1b6400a8243382673e47
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85077885"
---
# <a name="what-is-azure-ad-identity-governance"></a>O que é o Azure AD Identity Governance?

AZure Ative Directory (Azure AD) A Governação de Identidade permite-lhe equilibrar a necessidade de segurança e produtividade dos colaboradores da sua organização com os processos e visibilidade certos. Fornece-lhe capacidades para garantir que as pessoas certas tenham o acesso certo aos recursos certos. Estas funcionalidades Azure AD e Enterprise Mobility + Security permitem-lhe mitigar o risco de acesso protegendo, monitorizando e auditando o acesso a ativos críticos -- garantindo ao mesmo tempo a produtividade dos colaboradores e parceiros de negócio.  

A Governação da Identidade confere às organizações a capacidade de fazer as seguintes tarefas entre colaboradores, parceiros de negócios e fornecedores, e através de serviços e aplicações tanto no local como em nuvens:

- Governar o ciclo de vida da identidade
- Governar o ciclo de vida de acesso
- Acesso privilegiado seguro para administração

Especificamente, destina-se a ajudar as organizações a abordar estas quatro questões-chave:

- Que utilizadores devem ter acesso a que recursos?
- O que é que os utilizadores estão a fazer com esse acesso?
- Existem controlos organizacionais eficazes para gerir o acesso?
- Os auditores podem verificar se os controlos estão a funcionar?

## <a name="identity-lifecycle"></a>Ciclo de vida identitário

A Governação da Identidade ajuda as organizações a alcançar um equilíbrio entre *produtividade* - Quão rapidamente uma pessoa pode ter acesso aos recursos de que precisa, como quando se junta à minha organização? E *segurança* - Como deve o seu acesso mudar ao longo do tempo, como devido a alterações ao estatuto laboral dessa pessoa?  A gestão do ciclo de vida identitária é a base para a Governança identitária, e uma governação eficaz em escala requer a modernização da infraestrutura de gestão do ciclo de vida identitária para aplicações.

![Ciclo de vida identitário](./media/identity-governance-overview/identity-lifecycle.png)

Para muitas organizações, o ciclo de vida identitário dos colaboradores está ligado à representação desse utilizador num sistema HCM (gestão de capital humano).  O Azure AD Premium mantém automaticamente as identidades dos utilizadores para as pessoas representadas no Workday tanto no Ative Directory como no Azure Ative Directory, conforme descrito no [tutorial de fornecimento de entrada de trabalho.](../saas-apps/workday-inbound-tutorial.md)  O Azure AD Premium também inclui [o Microsoft Identity Manager,](/microsoft-identity-manager/)que pode importar registos de sistemas HCM no local, tais como SAP, Oracle eBusiness e Oracle PeopleSoft.

Cada vez mais, os cenários requerem colaboração com pessoas fora da sua organização. A colaboração [Azure AD B2B](/azure/active-directory/b2b/) permite-lhe partilhar de forma segura as aplicações e serviços da sua organização com utilizadores convidados e parceiros externos de qualquer organização, mantendo o controlo sobre os seus próprios dados corporativos.  [A gestão de direitos AD AD](entitlement-management-overview.md) permite-lhe selecionar quais os utilizadores da organização que podem solicitar acesso e ser adicionados como hóspedes B2B ao diretório da sua organização, e garante que estes hóspedes são removidos quando já não precisam de acesso.

## <a name="access-lifecycle"></a>Aceder ao ciclo de vida

As organizações precisam de um processo para gerir o acesso para além do que foi inicialmente previsto para um utilizador quando a identidade desse utilizador foi criada.  Além disso, as organizações empresariais devem ser capazes de escalar eficazmente para poderem desenvolver e impor a política e os controlos de acesso numa base contínua.

![Aceder ao ciclo de vida](./media/identity-governance-overview/access-lifecycle.png)

Normalmente, os delegados de TI acedem a decisões de aprovação aos decisores empresariais.  Além disso, as TI podem envolver os próprios utilizadores.  Por exemplo, os utilizadores que acedam a dados confidenciais dos clientes na aplicação de marketing de uma empresa na Europa precisam de conhecer as políticas da empresa. Os utilizadores convidados podem desconhecer os requisitos de tratamento dos dados numa organização para a qual foram convidados.

As organizações podem automatizar o processo de ciclo de vida de acesso através de tecnologias como [grupos dinâmicos,](../users-groups-roles/groups-dynamic-membership.md)juntamente com o fornecimento de utilizadores a [aplicações SaaS](../saas-apps/tutorial-list.md) ou [apps integradas com o SCIM.](../app-provisioning/use-scim-to-provision-users-and-groups.md)  As organizações também podem controlar quais [os utilizadores convidados que têm acesso a aplicações no local.](../b2b/hybrid-cloud-to-on-premises.md)  Estes direitos de acesso podem então ser regularmente revistos utilizando [avaliações recorrentes de acesso a Azure AD](access-reviews-overview.md).   [A gestão de direitos AD AZure](entitlement-management-overview.md) também permite definir como os utilizadores solicitam acesso através de pacotes de membros de grupo e equipa, funções de aplicação e funções SharePoint Online.

Quando um utilizador tenta aceder a aplicações, a Azure AD aplica políticas [de Acesso Condicional.](/azure/active-directory/conditional-access/) Por exemplo, as políticas de Acesso Condicional podem incluir a exibição de um [termos de utilização](../conditional-access/terms-of-use.md) e [garantir que o utilizador concordou com esses termos](../conditional-access/require-tou.md) antes de poder aceder a uma aplicação.

## <a name="privileged-access-lifecycle"></a>Ciclo de vida de acesso privilegiado

Historicamente, o acesso privilegiado tem sido descrito por outros fornecedores como uma capacidade separada da Governação da Identidade. No entanto, na Microsoft, pensamos que o acesso privilegiado é uma parte fundamental da Governação da Identidade -- especialmente dado o potencial de uso indevido associado a esses direitos de administrador pode causar a uma organização. Os empregados, vendedores e empreiteiros que assumam direitos administrativos têm de ser governados.

![Ciclo de vida de acesso privilegiado](./media/identity-governance-overview/privileged-access-lifecycle.png)

[A Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) fornece controlos adicionais adaptados para garantir direitos de acesso a recursos, através de Azure AD, Azure e outros Serviços Online da Microsoft.  O acesso just-in-time e as capacidades de alerta de mudança de função fornecidas pela Azure AD PIM, além da autenticação multi-factor e do Acesso Condicional, fornecem um conjunto abrangente de controlos de governação para ajudar a garantir os recursos da sua empresa (diretório, Office 365 e funções de recursos Azure). Tal como acontece com outras formas de acesso, as organizações podem utilizar revisões de acesso para configurar a recertificação de acesso recorrente para todos os utilizadores em funções de administrador.

## <a name="getting-started"></a>Introdução

Consulte o separador 'Iniciar a Gestão de **Identidade'** no portal Azure para começar a utilizar a gestão de direitos, comentários de acesso, Gestão de Identidade Privilegiada e Termos de Utilização.

![Governação da Identidade a começar](./media/identity-governance-overview/getting-started.png)


Se tiver algum feedback sobre as funcionalidades de Governação de Identidade, clique em **Obter feedback** no portal Azure para submeter o seu feedback. A equipa revê regularmente o seu feedback.

Embora não exista uma solução ou recomendação perfeita para cada cliente, os seguintes guias de configuração também fornecem as políticas de base que a Microsoft recomenda que siga para garantir uma mão de obra mais segura e produtiva.

- [Configurações de acesso aos dispositivos e identidade](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [A proteger o acesso privilegiado](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Apêndice - funções menos privilegiadas para a gestão de funcionalidades de Governação da Identidade

É uma boa prática usar o papel menos privilegiado para desempenhar tarefas administrativas na Governação da Identidade. Recomendamos que utilize o Azure AD PIM para ativar uma função necessária para executar estas tarefas. Seguem-se os papéis de diretório menos privilegiados para configurar as características de Governação da Identidade:

| Funcionalidade | Papel menos privilegiado |
| ------- | --------------------- |
| Gestão de direitos | Administrador do utilizador (com exceção da adição de sites SharePoint Online a catálogos, o que requer administrador global) |
| Revisões de acesso | Administrador do utilizador (com exceção das revisões de acesso das funções Azure ou Azure AD, que requer administrador privilegiado) |
|Privileged Identity Management | Administrador privilegiado |
| Termos de utilização | Administrador de segurança ou administrador de acesso condicional |

## <a name="next-steps"></a>Passos seguintes

- [O que é a gestão de direitos do Azure AD?](entitlement-management-overview.md)
- [O que são comentários de acesso a Ad AZure?](access-reviews-overview.md)
- [O que é o Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [O que posso fazer com os Termos de utilização?](active-directory-tou.md)


