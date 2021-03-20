---
title: Governação da Identidade - Diretório Ativo Azure | Microsoft Docs
description: O Azure Ative Directory Identity Governance permite-lhe equilibrar a necessidade de segurança e produtividade dos colaboradores da sua organização com os processos e visibilidade certos.
services: active-directory
documentationcenter: ''
author: ajburnle
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
ms.openlocfilehash: e02df83d4b7874a1d158aae45f1619eb543e0aec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92362455"
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

Para muitas organizações, o ciclo de vida identitário dos colaboradores está ligado à representação desse utilizador num sistema HCM (gestão de capital humano).  O Azure AD Premium mantém automaticamente as identidades dos utilizadores para pessoas representadas no Workday e no SuccessFactors tanto no Ative Directory como no Azure Ative Directory, conforme descrito na aplicação de HR em [nuvem ao guia de planeamento de provisionamento de utilizadores do Azure Ative Directory.](../app-provisioning/plan-cloud-hr-provision.md)  O Azure AD Premium também inclui [o Microsoft Identity Manager,](/microsoft-identity-manager/)que pode importar registos de sistemas HCM no local, tais como SAP HCM, Oracle eBusiness e Oracle PeopleSoft.

Cada vez mais, os cenários requerem colaboração com pessoas fora da sua organização. A colaboração [Azure AD B2B](/azure/active-directory/b2b/) permite-lhe partilhar de forma segura as aplicações e serviços da sua organização com utilizadores convidados e parceiros externos de qualquer organização, mantendo o controlo sobre os seus próprios dados corporativos.  [A gestão de direitos AD AD](entitlement-management-overview.md) permite-lhe selecionar quais os utilizadores da organização que podem solicitar acesso e ser adicionados como hóspedes B2B ao diretório da sua organização, e garante que estes hóspedes são removidos quando já não precisam de acesso.

## <a name="access-lifecycle"></a>Aceder ao ciclo de vida

As organizações precisam de um processo para gerir o acesso para além do que foi inicialmente previsto para um utilizador quando a identidade desse utilizador foi criada.  Além disso, as organizações empresariais devem ser capazes de escalar eficazmente para poderem desenvolver e impor a política e os controlos de acesso numa base contínua.

![Aceder ao ciclo de vida](./media/identity-governance-overview/access-lifecycle.png)

Normalmente, os delegados de TI acedem a decisões de aprovação aos decisores empresariais.  Além disso, as TI podem envolver os próprios utilizadores.  Por exemplo, os utilizadores que acedam a dados confidenciais dos clientes na aplicação de marketing de uma empresa na Europa precisam de conhecer as políticas da empresa. Os utilizadores convidados podem desconhecer os requisitos de tratamento dos dados numa organização para a qual foram convidados.

As organizações podem automatizar o processo de ciclo de vida de acesso através de tecnologias como [grupos dinâmicos,](../enterprise-users/groups-dynamic-membership.md)juntamente com o fornecimento de utilizadores a [aplicações SaaS](../saas-apps/tutorial-list.md) ou [apps integradas com o SCIM.](../app-provisioning/use-scim-to-provision-users-and-groups.md)  As organizações também podem controlar quais [os utilizadores convidados que têm acesso a aplicações no local.](../external-identities/hybrid-cloud-to-on-premises.md)  Estes direitos de acesso podem então ser regularmente revistos utilizando [avaliações recorrentes de acesso a Azure AD](access-reviews-overview.md).   [A gestão de direitos AD AZure](entitlement-management-overview.md) também permite definir como os utilizadores solicitam acesso através de pacotes de membros de grupo e equipa, funções de aplicação e funções SharePoint Online.

Quando um utilizador tenta aceder a aplicações, a Azure AD aplica políticas [de Acesso Condicional.](../conditional-access/index.yml) Por exemplo, as políticas de Acesso Condicional podem incluir a exibição de um [termos de utilização](../conditional-access/terms-of-use.md) e [garantir que o utilizador concordou com esses termos](../conditional-access/require-tou.md) antes de poder aceder a uma aplicação.

## <a name="privileged-access-lifecycle"></a>Ciclo de vida de acesso privilegiado

Historicamente, o acesso privilegiado tem sido descrito por outros fornecedores como uma capacidade separada da Governação da Identidade. No entanto, na Microsoft, pensamos que o acesso privilegiado é uma parte fundamental da Governação da Identidade -- especialmente dado o potencial de uso indevido associado a esses direitos de administrador pode causar a uma organização. Os empregados, vendedores e empreiteiros que assumam direitos administrativos têm de ser governados.

![Ciclo de vida de acesso privilegiado](./media/identity-governance-overview/privileged-access-lifecycle.png)

[A Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) fornece controlos adicionais adaptados para garantir direitos de acesso a recursos, através de Azure AD, Azure e outros Serviços Online da Microsoft.  O acesso just-in-time e as capacidades de alerta de mudança de função fornecidas pela Azure AD PIM, além da autenticação multi-factor e do Acesso Condicional, fornecem um conjunto abrangente de controlos de governação para ajudar a garantir os recursos da sua empresa (diretório, Microsoft 365 e funções de recursos Azure). Tal como acontece com outras formas de acesso, as organizações podem utilizar revisões de acesso para configurar a recertificação de acesso recorrente para todos os utilizadores em funções de administrador.

## <a name="governance-capabilities-in-other-azure-ad-features"></a>Capacidades de governação em outras funcionalidades da AD Azure

Além das funcionalidades acima enumeradas, as funcionalidades adicionais da AZURE AD frequentemente utilizadas para fornecer cenários de governação de identidade incluem:

| Funcionalidade | Scenario |Funcionalidade
| ------- | --------------------- |-----|
|Ciclo de vida identitário (empregados)|Os administradores podem permitir o fornecimento de conta de utilizador a partir de Workday ou SuccessFactors cloud HR, ou no local HR.|[fornecimento de utilizadores de RH em nuvem para Azure AD](../app-provisioning/plan-cloud-hr-provision.md)|
|Ciclo de vida identitário (convidados)|Os administradores podem permitir o acesso de um cliente de autosserviço a bordo de outro inquilino AD AZure, federação direta, Código de Uma Hora (OTP) ou contas do Google.  Os utilizadores convidados são automaticamente aprovisionados e desprovisionados sujeitos às políticas de ciclo de vida.|[Gestão de direitos](entitlement-management-overview.md) utilizando [B2B](../external-identities/what-is-b2b.md)|
|Gestão de direitos|Os proprietários de recursos podem criar pacotes de acesso contendo apps, equipas, Ad AD e Microsoft 365 e sites SharePoint Online.|[Gestão de direitos](entitlement-management-overview.md)|
|Pedidos de acesso|Os utilizadores finais podem solicitar a adesão ao grupo ou o acesso à aplicação. Os utilizadores finais, incluindo hóspedes de outras organizações, podem solicitar acesso a pacotes de acesso.|[Gestão de direitos](entitlement-management-overview.md)|
|Fluxo de trabalho|Os proprietários de recursos podem definir os aprovadores e aprovadores de escalada para pedidos de acesso e aprovadores para pedidos de ativação de funções.  |[Gestão de direitos](entitlement-management-overview.md) e [PIM](../privileged-identity-management/pim-configure.md)|
|Política e gestão de funções|A administração pode definir políticas de acesso condicional para acesso a prazo às aplicações.  Os proprietários de recursos podem definir políticas de acesso do utilizador através de pacotes de acesso.|[Políticas de acesso condicional](../conditional-access/overview.md) e [gestão de direitos](entitlement-management-overview.md)|
|Certificação de acesso|Os administradores podem permitir a recertificação de acesso recorrente para: aplicações SaaS ou membros do grupo cloud, atribuições de funções AZure AD ou Azure Resource. Remova automaticamente o acesso a recursos, bloqueie o acesso ao hóspede e elimine as contas dos hóspedes.|[Comentários de acesso](access-reviews-overview.md), também surgiram em [PIM](../privileged-identity-management/pim-how-to-start-security-review.md)|
|Cumprimento e provisionamento|Provisão automática e desprovisionamento em aplicações conectadas a Azure AD, incluindo via SCIM e em sites SharePoint Online. |[fornecimento de utilizadores](../app-provisioning/user-provisioning.md)|
|Reportagem e análise|Os administradores podem recuperar registos de auditoria de fornecimentos recentes de utilizadores e assinar em atividade. Integração com o Azure Monitor e "quem tem acesso" através de pacotes de acesso.|Relatórios e [monitorização](../reports-monitoring/overview-monitoring.md) [da AD Azure](../reports-monitoring/overview-reports.md)|
|Acesso privilegiado|Acesso just-in-time e programado, alerta, fluxos de trabalho de aprovação para funções AD Azure (incluindo funções personalizadas) e funções de Recursos Azure.|[Azure AD PIM](../privileged-identity-management/pim-configure.md)|
|Auditoria|Os administradores podem ser alertados para a criação de contas de administração.|[Alertas Azure AD PIM](../privileged-identity-management/pim-how-to-configure-security-alerts.md)|

## <a name="getting-started"></a>Introdução

Consulte o separador 'Iniciar a Gestão de **Identidade'** no portal Azure para começar a utilizar a gestão de direitos, comentários de acesso, Gestão de Identidade Privilegiada e Termos de Utilização.

![Governação da Identidade a começar](./media/identity-governance-overview/getting-started.png)


Se tiver algum feedback sobre as funcionalidades de Governação de Identidade, clique em **Obter feedback** no portal Azure para submeter o seu feedback. A equipa revê regularmente o seu feedback.

Embora não exista uma solução ou recomendação perfeita para cada cliente, os seguintes guias de configuração também fornecem as políticas de base que a Microsoft recomenda que siga para garantir uma mão de obra mais segura e produtiva.

- [Configurações de acesso aos dispositivos e identidade](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [A proteger o acesso privilegiado](../roles/security-planning.md)

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
- [O que posso fazer com os Termos de utilização?](../conditional-access/terms-of-use.md)