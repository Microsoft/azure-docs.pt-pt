---
title: Azure AD Cloud Regegered Management for On-Premises Workloads - Azure
description: Este tópico descreve a gestão governada em nuvem para cargas de trabalho no local.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9cb101e415499150cd3d825fe5f42ce0dbc766fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89662527"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Como a AZure AD fornece gestão governada em nuvem para cargas de trabalho no local

O Azure Ative Directory (Azure AD) é uma identidade abrangente como solução de serviço (IDaaS) utilizada por milhões de organizações que abrangem todos os aspetos da identidade, gestão de acessos e segurança. A Azure AD detém mais de mil milhões de identidades de utilizadores e ajuda os utilizadores a iniciar súmis e aceder de forma segura a ambos:

* Recursos externos, como o Microsoft 365, o portal Azure, e milhares de outras aplicações de Software-as-a-Service (SaaS).
* Recursos internos, como aplicações na rede corporativa e intranet de uma organização, juntamente com quaisquer aplicações em nuvem desenvolvidas por essa organização.

As organizações podem usar a Azure AD se forem "nuvem pura", ou como uma implantação 'híbrida' se tiverem cargas de trabalho no local. Uma implantação híbrida do Azure AD pode fazer parte de uma estratégia para uma organização migrar os seus ativos de TI para a nuvem, ou para continuar a integrar a infraestrutura existente no local ao lado de novos serviços na nuvem.

Historicamente, as organizações 'híbridas' viram a Azure AD como uma extensão da sua infraestrutura existente no local. Nestas implementações, a administração de governação de identidade no local, o Windows Server Ative Directory ou outros sistemas de diretório internos, são os pontos de controlo, e os utilizadores e grupos são sincronizados desses sistemas para um diretório em nuvem como o Azure AD. Uma vez que estas identidades estejam na nuvem, podem ser disponibilizadas para o Microsoft 365, Azure e outras aplicações.

![Ciclo de vida identitário](media/cloud-governed-management-for-on-premises//image1.png)

À medida que as organizações movem mais da sua infraestrutura de TI juntamente com as suas aplicações para a nuvem, muitos procuram a melhoria da segurança e capacidades de gestão simplificadas da gestão de identidade como um serviço. As funcionalidades IDaaS em nuvem em Azure AD aceleram a transição para a gestão governada em nuvem, fornecendo as soluções e capacidades que permitem às organizações adotar e mover mais rapidamente a sua gestão de identidade dos sistemas tradicionais no local para a Azure AD, ao mesmo tempo que continua a apoiar as aplicações existentes, bem como novas aplicações.

Este artigo descreve a estratégia da Microsoft para o IDaaS híbrido e descreve como as organizações podem usar a AZure AD para as suas aplicações existentes.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>A abordagem AD do AD azul à gestão de identidade governada pela nuvem

À medida que as organizações transitam para a nuvem, precisam de garantias de que têm controlos sobre o seu ambiente completo - mais segurança e mais visibilidade em atividades, apoiadas pela automação e insights proactivos. "**Cloud governed management**" descreve como as organizações gerem e governam os seus utilizadores, aplicações, grupos e dispositivos da nuvem.

Neste mundo moderno, as organizações precisam de ser capazes de gerir eficazmente à escala, devido à proliferação de aplicações saaS e ao papel crescente da colaboração e das identidades externas. A nova paisagem de risco da nuvem significa que uma organização deve ser mais responsiva - um ator malicioso que comprometa um utilizador em nuvem pode afetar aplicações de nuvem e no local.

Em particular, as organizações híbridas precisam de ser capazes de delegar e automatizar tarefas, o que historicamente fez manualmente. Para automatizar tarefas, necessitam de APIs e processos que orquestram o ciclo de vida dos diferentes recursos relacionados com a identidade (utilizadores, grupos, aplicações, dispositivos), para que possam delegar a gestão diária desses recursos a mais indivíduos fora do pessoal de TI principal. A Azure AD aborda estes requisitos através da gestão da conta de utilizador e da autenticação nativa para os utilizadores sem necessidade de infraestrutura de identidade no local. Não construir infraestruturas no local pode beneficiar organizações que têm novas comunidades de utilizadores, como parceiros de negócio, que não tiveram origem no seu diretório no local, mas cuja gestão de acessos é fundamental para alcançar resultados de negócio.

Além disso, a gestão não está completa sem a governação --- e a governação neste novo mundo é uma parte integrada do sistema de identidade em vez de um complemento. A governação identitária dá às organizações a capacidade de gerir a identidade e aceder ao ciclo de vida entre colaboradores, parceiros de negócios e fornecedores, e serviços e aplicações.

A incorporação da governação identitária facilita a transição para a gestão governada em nuvem, permite que as TI se dimensionem, abordam novos desafios com os hóspedes e fornecem insights e automação mais profundos do que o que os clientes tinham com a infraestrutura no local. Governação neste novo mundo significa a capacidade de uma organização ter transparência, visibilidade e controlos adequados no acesso aos recursos dentro da organização. Com a Azure AD, as equipas de segurança e auditoria têm visibilidade sobre quem tem --- e quem deve ter - acesso a que recursos na organização (em que dispositivos), o que esses utilizadores estão a fazer com esse acesso, e se a organização tem e utiliza controlos adequados para remover ou restringir o acesso de acordo com as políticas da empresa ou regulamentares.

O novo modelo de gestão beneficia organizações com aplicações SaaS e line-of-business (LOB), uma vez que são mais facilmente capazes de gerir e garantir o acesso a essas aplicações. Ao integrar aplicações com a Azure AD, as organizações poderão utilizar e gerir o acesso em toda a nuvem e no local com identidades originadas de forma consistente. A gestão do ciclo de vida da aplicação torna-se mais automatizada, e a Azure AD fornece insights ricos sobre o uso da aplicação que não era facilmente alcançável na gestão de identidade no local. Através do Azure AD, os grupos Microsoft 365 e as funcionalidades de self-service da Microsoft, as organizações podem facilmente criar grupos de gestão e colaboração de acesso e adicionar ou remover utilizadores na nuvem para permitir requisitos de colaboração e gestão de acesso.

A seleção das capacidades AD Azure certas para a gestão governada na nuvem depende das aplicações a serem utilizadas e de como essas aplicações serão integradas com Azure AD. As secções seguintes descrevem as abordagens a tomar para aplicações integradas com AD, e aplicações que utilizam protocolos da federação (por exemplo, SAML, OAuth ou OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Gestão governada pela Cloud para aplicações integradas em AD

O Azure AD melhora a gestão das aplicações integradas no Ative Directory de uma organização através de acesso remoto seguro e acesso condicional a essas aplicações. Além disso, a Azure AD também fornece gestão do ciclo de vida da conta e gestão credencial para as contas AD existentes do utilizador, incluindo:

* **Acesso remoto seguro e acesso condicional para aplicações no local**

Para muitas organizações, o primeiro passo para gerir o acesso a partir da nuvem para aplicações integradas na web e remotas baseadas em computadores de secretária é implementar o proxy de [aplicação](../manage-apps/application-proxy.md) em frente a essas aplicações para fornecer acesso remoto seguro.

Após um único s-on para Azure AD, os utilizadores podem aceder a aplicações tanto em nuvem como no local através de um URL externo ou de um portal de aplicações internas. Por exemplo, o Application Proxy fornece acesso remoto e um único acesso ao Remote Desktop, SharePoint, bem como aplicações como Tableau e Qlik, e aplicações de linha de negócios (LOB). Além disso, as políticas de Acesso Condicional podem incluir a exibição [dos termos de utilização](../conditional-access/terms-of-use.md) e [garantir que o utilizador concordou com eles](../conditional-access/require-tou.md) antes de poder aceder a uma aplicação.

![App Proxy arquitetura](media/cloud-governed-management-for-on-premises/image2.png)

* **Gestão automática do ciclo de vida das contas do Diretório Ativo**

A governação identitária ajuda as organizações a alcançar um equilíbrio entre *produtividade* --- quão rapidamente uma pessoa pode ter acesso aos recursos de que precisa, como quando se junta à organização? --- e --- *segurança* como é que o seu acesso deve mudar ao longo do tempo, como quando o estatuto laboral dessa pessoa muda? A gestão do ciclo de vida identitário é a base para a governação da identidade, e uma governação eficaz em escala requer a modernização da infraestrutura de gestão do ciclo de vida identitária para aplicações.

Para muitas organizações, o ciclo de vida identitário dos colaboradores está ligado à representação desse utilizador num sistema de gestão de capital humano (HCM). Para as organizações que utilizam o Workday como o seu sistema HCM, a Azure AD pode garantir que as contas de utilizador em AD são [automaticamente aprovisionadas e desprovisionadas para os trabalhadores no Workday](../saas-apps/workday-inbound-tutorial.md). Ao fazê-lo, leva a uma melhor produtividade do utilizador através da automatização das contas de nascença e gere o risco, garantindo que o acesso à aplicação é automaticamente atualizado quando um utilizador muda de funções ou sai da organização. O plano de [implementação](https://aka.ms/WorkdayDeploymentPlan) de fornecimento de utilizadores orientado para o Workday é um guia passo a passo que acompanha as organizações através das melhores práticas implementação da solução workday para ative directory user Provisioning num processo de cinco etapas.

O Azure AD Premium também inclui o Microsoft Identity Manager, que pode importar registos de outros sistemas HCM no local, incluindo SAP, Oracle eBusiness e Oracle PeopleSoft.

A colaboração entre empresas requer cada vez mais acesso a pessoas fora da sua organização. A colaboração [Azure AD B2B](/azure/active-directory/b2b/) permite que as organizações partilhem de forma segura as suas aplicações e serviços com utilizadores convidados e parceiros externos, mantendo o controlo sobre os seus próprios dados corporativos.

O Azure AD pode [automaticamente criar contas em AD para os utilizadores convidados,](../external-identities/hybrid-cloud-to-on-premises.md) conforme necessário, permitindo aos hóspedes empresariais aceder em aplicações integradas a AD no local sem precisar de outra senha. As organizações podem configurar [políticas de autenticação de vários fatores (MFA) para os utilizadores convidados,](../external-identities/conditional-access.md)pelo que os controlos de MFA são feitos durante a autenticação por procuração de aplicação. Além disso, quaisquer [comentários de acesso](../governance/manage-guest-access-with-access-reviews.md) que sejam feitos na nuvem B2B os utilizadores aplicam-se aos utilizadores no local. Por exemplo, se o utilizador da nuvem for eliminado através de políticas de gestão do ciclo de vida, o utilizador no local também é eliminado.

**Gestão credencial para contas de Diretório Ativo** O reset da palavra-passe de autosserviço da AD AD permite aos utilizadores que se esqueceram das suas palavras-passe para serem reautenticados e redefinirem as suas palavras-passe, com as palavras-passe alteradas [escritas para o Ative Directory .](../authentication/concept-sspr-writeback.md) O processo de reset de palavra-passe também pode utilizar as políticas de senha de ative diretório no local: Quando um utilizador reinicia a sua palavra-passe, é verificado para garantir que cumpre a política de Diretório Ativo no local antes de a comprometer com esse diretório. O plano de [implementação](https://aka.ms/deploymentplans/sspr) de redefinição de palavra-passe de autosserviço descreve as melhores práticas para lançar a palavra-passe de autosserviço reposta aos utilizadores através de experiências integradas na Web e no Windows.

![Arquitetura Azure AD SSPR](media/cloud-governed-management-for-on-premises/image3.png)

Finalmente, para as organizações que permitem que os utilizadores alterem as suas palavras-passe em AD, a AD pode ser configurada para usar a mesma política de senha que a organização está a usar em Azure AD através da [funcionalidade de proteção de senha AZure AD](../authentication/concept-password-ban-bad-on-premises.md), atualmente em pré-visualização pública.

Quando uma organização está pronta para mover uma aplicação integrada com AD para a nuvem, movendo o sistema operativo que hospeda a aplicação para Azure, a [Azure AD Domain Services](../../active-directory-domain-services/overview.md) fornece serviços de domínio compatíveis com AD (tais como adesão de domínio, política de grupo, LDAP e autenticação Kerberos/NTLM). A Azure AD Domain Services integra-se com o inquilino Azure AD existente da organização, permitindo que os utilizadores assinem usando as suas credenciais corporativas. Além disso, os grupos existentes e as contas dos utilizadores podem ser utilizados para garantir o acesso aos recursos, garantindo um "elevador e mudança" mais suave dos recursos no local para os serviços de infraestruturas da Azure.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Gestão governada pela Cloud para aplicações baseadas em federaçãos no local

Para uma organização que já utiliza um fornecedor de identidade no local, a mudança de aplicações para a Azure AD permite um acesso mais seguro e uma experiência administrativa mais fácil para a gestão da federação. O Azure AD permite configurar controlos de acesso granular por aplicação, incluindo autenticação multi-factor Azure, utilizando o Acesso Condicionado AD Azure. A Azure AD suporta mais capacidades, incluindo certificados de assinatura de fichas específicos de aplicação e datas de validade de certificados configuráveis. Estas capacidades, ferramentas e orientações permitem às organizações reformar os seus fornecedores de identidade no local. A própria TI da Microsoft, por exemplo, transferiu 17.987 aplicações dos Serviços internos da Microsoft para a Federação de Diretórios Ativos (AD FS) para Azure AD.

![Evolução do Ad Azure](media/cloud-governed-management-for-on-premises/image5.png)

Para começar a migrar aplicações federadas para a Azure AD como fornecedor de identidade, consulte https://aka.ms/migrateapps o que inclui links para:

* O livro branco [Migrando as Suas Aplicações para o Diretório Ativo do Azure,](https://aka.ms/migrateapps/whitepaper)que apresenta os benefícios da migração e descreve como planear a migração em quatro fases claramente delineadas: descoberta, classificação, migração e gestão contínua. Você será guiado através de como pensar sobre o processo e dividir o seu projeto em peças fáceis de consumir. Ao longo do documento estão ligações a recursos importantes que o ajudarão ao longo do caminho.

* O guia de solução [Migração de Autenticação de Aplicações de Serviços da Federação Ativa para O Diretório Ativo](https://aka.ms/migrateapps/adfssolutionguide) explora em maior detalhe as mesmas quatro fases de planeamento e execução de um projeto de migração de aplicações. Neste guia, você aprenderá a aplicar essas fases ao objetivo específico de mover uma aplicação dos Serviços da Federação de Diretório Ativo (AD FS) para Azure AD.

* O [Roteiro de Prontidão de Migração dos Serviços da Federação de Diretório Ativo](https://aka.ms/migrateapps/adfstools) pode ser executado em servidores da Federação De Diretórios Ativos (AD FS) existentes para determinar a prontidão de pedidos de migração para Azure AD.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Gestão contínua de acessos através de aplicações de cloud e on-in

As organizações precisam de um processo para gerir o acesso que seja escalável. Os utilizadores continuam a acumular direitos de acesso e acabam por ficar para além do que inicialmente estava previsto para os mesmos. Além disso, as organizações empresariais devem ser capazes de escalar eficazmente para desenvolver e impor a política e os controlos de acesso numa base contínua.

Normalmente, os delegados de TI acedem a decisões de aprovação aos decisores empresariais. Além disso, as TI podem envolver os próprios utilizadores. Por exemplo, os utilizadores que acedam a dados confidenciais dos clientes na aplicação de marketing de uma empresa na Europa precisam de conhecer as políticas da empresa. Os utilizadores convidados também podem desconhecer os requisitos de tratamento dos dados numa organização para a qual foram convidados.

As organizações podem automatizar o processo de ciclo de vida de acesso através de tecnologias como [grupos dinâmicos,](../users-groups-roles/groups-dynamic-membership.md)juntamente com o fornecimento de utilizadores às [aplicações SaaS,](../saas-apps/tutorial-list.md)ou [aplicações integradas utilizando a norma System for Cross-Domain Identity Management (SCIM).](../app-provisioning/use-scim-to-provision-users-and-groups.md) As organizações também podem controlar quais [os utilizadores convidados que têm acesso a aplicações no local.](../external-identities/hybrid-cloud-to-on-premises.md) Estes direitos de acesso podem então ser regularmente revistos utilizando [avaliações recorrentes de acesso a Azure AD](../governance/access-reviews-overview.md).

## <a name="future-directions"></a>Direções futuras

Em ambientes híbridos, a estratégia da Microsoft é permitir implementações em que a nuvem é o plano de **controlo para a identidade**, e os diretórios no local e outros sistemas de identidade, como o Ative Directory e outras aplicações no local, são o alvo para o fornecimento de acesso aos utilizadores. Esta estratégia continuará a garantir os direitos, identidades e acesso nas aplicações e cargas de trabalho que deles dependem. Neste estado final, as organizações serão capazes de impulsionar a produtividade do utilizador final inteiramente a partir da nuvem.

![Arquitetura do Azure AD](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como começar esta viagem, consulte os planos de implantação da AD Azure, localizados em <https://aka.ms/deploymentplans> . Fornecem orientações de ponta a ponta sobre como implementar capacidades do Azure Ative Directory (Azure AD). Cada plano explica o valor do negócio, as considerações de planeamento, o design e os procedimentos operacionais necessários para lançar com sucesso as capacidades AD do Azure. A Microsoft atualiza continuamente os planos de implementação com as melhores práticas aprendidas com as implementações dos clientes e outros feedbacks quando adicionamos novas capacidades para gerir a partir da nuvem com Azure AD.