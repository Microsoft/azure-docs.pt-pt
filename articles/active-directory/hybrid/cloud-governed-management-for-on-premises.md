---
title: Azure AD Cloud Rege gestão para cargas de trabalho no local - Azure
description: Este tópico descreve a gestão rege da nuvem para cargas de trabalho no local.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510a5562740260eb2946ded074a5c37804c55375
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67109511"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Como a AD Azure oferece gestão regeda em nuvem para cargas de trabalho no local

O Azure Ative Directory (Azure AD) é uma identidade abrangente como solução de serviço (IDaaS) utilizada por milhões de organizações que abrangem todos os aspetos da identidade, gestão de acessos e segurança. A Azure AD detém mais de mil milhões de identidades de utilizadores e ajuda os utilizadores a iniciar em sessão e aceder de forma segura a ambos:

* Recursos externos, como o Microsoft Office 365, o portal Azure, e milhares de outras aplicações de Software-as-a-Service (SaaS).
* Recursos internos, como aplicações na rede corporativa e intranet de uma organização, juntamente com quaisquer aplicações em nuvem desenvolvidas por essa organização.

As organizações podem usar o Azure AD se forem "pura nuvem", ou como uma implantação 'híbrida' se tiverem carga suportária no local. Uma implantação híbrida da Azure AD pode fazer parte de uma estratégia para uma organização migrar os seus ativos de TI para a nuvem, ou continuar a integrar a infraestrutura existente no local ao lado de novos serviços na nuvem.

Historicamente, as organizações 'híbridas' viram a AD Azure como uma extensão da sua infraestrutura existente no local. Nestas implementações, a administração de governança de identidade no local, o Diretório Ativo do Windows Server ou outros sistemas de diretório seletiva, são os pontos de controlo, e os utilizadores e grupos estão sincronizados desses sistemas para um diretório na nuvem como o Azure AD. Uma vez que essas identidades estão na nuvem, podem ser disponibilizadas ao Office 365, Azure e outras aplicações.

![Ciclo de vida de identidade](media/cloud-governed-management-for-on-premises//image1.png)

À medida que as organizações movem mais da sua infraestrutura de TI juntamente com as suas aplicações para a nuvem, muitos procuram a melhoria da segurança e as capacidades de gestão simplificadas da gestão da identidade como um serviço. As funcionalidades iDaaS entregues em nuvem em Azure AD aceleram a transição para a gestão governada pela nuvem, fornecendo as soluções e capacidades que permitem às organizações adotar rapidamente e mover mais da sua gestão de identidade dos sistemas tradicionais no local para a AD Azure, ao mesmo tempo que continua a apoiar as existentes, bem como novas aplicações.

Este artigo descreve a estratégia da Microsoft para o IDaaS híbrido e descreve como as organizações podem usar o Azure AD para as suas aplicações existentes.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>A abordagem da AD Azure à gestão de identidade governada pela nuvem

À medida que as organizações transitam para a nuvem, precisam de garantias de que têm controlos sobre o seu ambiente completo - mais segurança e mais visibilidade em atividades, apoiadas pela automação e insights pró-ativos. "**Cloud governado gestão**" descreve como as organizações gerem e governam os seus utilizadores, aplicações, grupos e dispositivos a partir da nuvem.

Neste mundo moderno, as organizações precisam de ser capazes de gerir eficazmente em escala, devido à proliferação de aplicações SaaS e ao crescente papel da colaboração e das identidades externas. O novo cenário de risco da nuvem significa que uma organização deve ser mais responsiva - um ator malicioso que compromete um utilizador de nuvem pode afetar as aplicações de nuvem e no local.

Em particular, as organizações híbridas precisam de ser capazes de delegar e automatizar tarefas, o que historicamente fez as TI manualmente. Para automatizar tarefas, precisam de APIs e processos que orquestrem o ciclo de vida dos diferentes recursos relacionados com a identidade (utilizadores, grupos, aplicações, dispositivos), para que possam delegar a gestão diária desses recursos a mais indivíduos fora do núcleo de pessoal de TI. A Azure AD aborda estes requisitos através da gestão da conta de utilizador e da autenticação nativa para os utilizadores sem necessitar de infraestrutura de identidade no local. Não construir infraestruturas no local pode beneficiar organizações que tenham novas comunidades de utilizadores, como parceiros de negócio, que não tiveram origem no seu diretório no local, mas cuja gestão de acesso é fundamental para alcançar os resultados do negócio.

Além disso, a gestão não está completa sem a governação --- e a governação neste novo mundo é uma parte integrada do sistema identitário e não um complemento. A governação identitária dá às organizações a capacidade de gerir a identidade e aceder ao ciclo de vida entre colaboradores, parceiros de negócios e fornecedores, e serviços e aplicações.

A incorporação da governação identitária facilita a transição da organização para a gestão governada pela nuvem, permite que as TI escalonem, abordem novos desafios com os hóspedes e proporcionem insights e automação mais profundas do que o que os clientes tinham com a infraestrutura no local. A governação neste novo mundo significa a capacidade de uma organização ter transparência, visibilidade e controlos adequados sobre o acesso aos recursos dentro da organização. Com a AD Azure, as operações de segurança e as equipas de auditoria têm visibilidade para quem tem --- e quem deve ter - acesso aos recursos da organização (em que dispositivos), o que esses utilizadores estão a fazer com esse acesso, e se a organização tem e usa controlos adequados para remover ou restringir o acesso de acordo com as políticas da empresa ou regulamentares.

O novo modelo de gestão beneficia as organizações com aplicações SaaS e line-of-business (LOB), uma vez que são mais facilmente capazes de gerir e garantir o acesso a essas aplicações. Ao integrar aplicações com a AD Azure, as organizações poderão utilizar e gerir o acesso através da nuvem e das identidades originadas no local de forma consistente. A gestão do ciclo de vida da aplicação torna-se mais automatizada, e a Azure AD fornece informações ricas sobre o uso da aplicação que não era facilmente alcançável na gestão de identidade no local. Através do Azure AD, Office 365 groups and Teams self-service features, as organizações podem facilmente criar grupos para gestão e colaboração de acesso e adicionar ou remover utilizadores na nuvem para permitir requisitos de colaboração e gestão de acesso.

A seleção das capacidades adido ida direita para gestão governada em nuvem depende das aplicações a utilizar, e de como essas aplicações serão integradas com a Azure AD. As seguintes secções delineiam as abordagens a tomar para aplicações integradas em AD, e aplicações que utilizam protocolos da federação (por exemplo, SAML, OAuth ou OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Gestão governada pela nuvem para aplicações integradas em AD

A Azure AD melhora a gestão das aplicações integradas no Ative Directory de uma organização através de acesso remoto seguro e acesso condicional a essas aplicações. Além disso, a Azure AD também fornece gestão de ciclo de vida e gestão de credenciais para as contas adexistentes do utilizador, incluindo:

* **Acesso remoto seguro e acesso condicional para aplicações no local**

Para muitas organizações, o primeiro passo na gestão do acesso a partir da nuvem para aplicações web integradas em AD e remotas é implementar o proxy de [aplicação](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) em frente a essas aplicações para fornecer acesso remoto seguro.

Após uma única entrada no Azure AD, os utilizadores podem aceder a aplicações cloud e no local através de um URL externo ou de um portal de aplicações interna. Por exemplo, o Application Proxy fornece acesso remoto e entrada única para desktop remoto, SharePoint, bem como aplicações como Tableau e Qlik, e aplicações de linha de negócios (LOB). Além disso, as políticas de Acesso Condicional podem incluir a exibição [dos termos de utilização](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) e a garantia de [que o utilizador concordou com elas](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) antes de poder aceder a uma aplicação.

![Arquitetura de Procuração de Aplicativos](media/cloud-governed-management-for-on-premises/image2.png)

* **Gestão automática de ciclos de vida para contas de Diretório Ativo**

A governação identitária ajuda as organizações a alcançar um equilíbrio entre *a produtividade* --- a rapidez com que uma pessoa pode ter acesso aos recursos de que necessita, como quando se junta à organização? --- e *a segurança* --- como deve o seu acesso mudar ao longo do tempo, como quando o estatuto laboral dessa pessoa muda? A gestão do ciclo de vida identitário é a base para a governação da identidade, e uma governação eficaz em escala requer a modernização da infraestrutura de gestão do ciclo de vida identitário para aplicações.

Para muitas organizações, o ciclo de vida identitário dos colaboradores está ligado à representação desse utilizador num sistema de gestão de capital humano (HCM). Para as organizações que usam o Workday como o seu sistema HCM, a Azure AD pode garantir que as contas dos utilizadores em AD são [automaticamente aprovisionadas e desprovisionadas para os trabalhadores no Dia de Trabalho.](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) Fazê-lo leva a uma melhoria da produtividade dos utilizadores através da automatização de contas de nascença e gere o risco garantindo que o acesso à aplicação é automaticamente atualizado quando um utilizador muda de funções ou sai da organização. O plano de [implementação](https://aka.ms/WorkdayDeploymentPlan) de fornecimento de utilizadores orientado pelo Workday é um guia passo a passo que percorre as organizações através das melhores práticas de implementação do Workday para a solução de provisionamento de utilizadores de diretório ativo num processo de cinco etapas.

O Azure AD Premium também inclui o Microsoft Identity Manager, que pode importar registos de outros sistemas HCM no local, incluindo SAP, Oracle eBusiness e Oracle PeopleSoft.

A colaboração entre empresas requer cada vez mais o acesso a pessoas fora da sua organização. A colaboração [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) permite que as organizações partilhem de forma segura as suas aplicações e serviços com utilizadores convidados e parceiros externos, mantendo o controlo sobre os seus próprios dados corporativos.

A Azure AD pode [criar automaticamente contas em AD para os utilizadores convidados,](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) conforme necessário, permitindo aos hóspedes empresariais aceder em aplicações integradas no local sem precisar de outra senha. As organizações podem configurar [políticas de autenticação multifactor (MFA) para os utilizadores convidados](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)para que os controlos de MFA sejam feitos durante a autenticação por procuração de aplicações. Além disso, quaisquer [avaliações](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) de acesso que sejam feitas na nuvem Os utilizadores B2B aplicam-se aos utilizadores no local. Por exemplo, se o utilizador da nuvem for eliminado através de políticas de gestão de ciclos de vida, o utilizador no local também é eliminado.

**Gestão credencial para contas de Diretório Ativo** O reset de palavra-passe de autosserviço da Azure AD permite que os utilizadores que se tenham esquecido das suas palavras-passe sejam reautenticados e redefiniras as suas palavras-passe, com as palavras-passe alteradas [escritas para o Diretório Ativo no local.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) O processo de redefinição da palavra-passe também pode utilizar as políticas de senha de diretório ativo no local: Quando um utilizador repõe a sua palavra-passe, é verificado para garantir que cumpre a política de Diretório Ativo no local antes de a comprometer com esse diretório. O plano de [implementação](https://aka.ms/deploymentplans/sspr) de palavras-passe de autosserviço descreve as melhores práticas para lançar a palavra-passe de autosserviço redefinida para os utilizadores através de experiências integradas na Web e no Windows.

![Arquitetura Azure AD SSPR](media/cloud-governed-management-for-on-premises/image3.png)

Finalmente, para as organizações que permitem aos utilizadores alterar as suas palavras-passe em AD, a AD pode ser configurada para usar a mesma política de passwords que a organização está a usar em Azure AD através da funcionalidade de proteção de [passwords Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), atualmente em pré-visualização pública.

Quando uma organização está pronta para mover uma aplicação integrada em AD para a nuvem, movendo o sistema operativo que acolhe a aplicação para o Azure, [o Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) fornece serviços de domínio compatíveis com AD (como aadesão ao domínio, política de grupo, LDAP e autenticação Kerberos/NTLM). A Azure AD Domain Services integra-se com o atual inquilino azure ad da organização, permitindo que os utilizadores possam assinar usando as suas credenciais corporativas. Além disso, os grupos existentes e as contas de utilizadores podem ser utilizados para garantir o acesso aos recursos, garantindo um "elevador e mudança" mais suaves dos recursos no local para os serviços de infraestrutura Azure.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Gestão governada em nuvem para aplicações baseadas na federação no local

Para uma organização que já utiliza um fornecedor de identidade no local, a mudança de aplicações para a Azure AD permite um acesso mais seguro e uma experiência administrativa mais fácil para a gestão da federação. A Azure AD permite configurar controlos de acesso granular por aplicação, incluindo a autenticação de multi-factores Azure, utilizando o Acesso Condicional Azure AD. A Azure AD suporta mais capacidades, incluindo certificados de assinatura de fichas específicos da aplicação e datas de validade do certificado configurável. Estas capacidades, ferramentas e orientação permitem às organizações reformar os seus fornecedores de identidade no local. A própria TI da Microsoft, por exemplo, transferiu 17.987 aplicações dos serviços internos da Federação de Diretórios Ativos da Microsoft (AD FS) para a Azure AD.

![Evolução da AD Azure](media/cloud-governed-management-for-on-premises/image5.png)

Para começar a migrar aplicações federadas para a AD https://aka.ms/migrateapps Azure como fornecedor de identidade, consulte que inclui links para:

* O livro branco [Migrating Your Applications to Azure Ative Directory](https://aka.ms/migrateapps/whitepaper), que apresenta os benefícios da migração e descreve como planear a migração em quatro fases claramente delineadas: descoberta, classificação, migração e gestão contínua. Você será guiado através de como pensar sobre o processo e dividir o seu projeto em pedaços fáceis de consumir. Ao longo do documento estão ligações a recursos importantes que o ajudarão ao longo do caminho.

* O guia de soluções [Para a Autenticação de Aplicações migratórias dos Serviços da Federação de Diretórios Ativos para o Diretório Ativo do Azure](https://aka.ms/migrateapps/adfssolutionguide) explora com mais detalhes as mesmas quatro fases de planeamento e execução de um projeto de migração de aplicações. Neste guia, você aprenderá a aplicar essas fases ao objetivo específico de mover uma aplicação dos Serviços da Federação de Diretório Ativo (AD FS) para a AD Azure.

* O Roteiro de Prontidão de Migração de Serviços da [Federação de Diretórios Ativos](https://aka.ms/migrateapps/adfstools) pode ser executado em servidores da Federação ativa de Diretórios (AD FS) existentes para determinar a prontidão dos pedidos de migração para a AD Azure.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Gestão contínua de acessoatravés de aplicações cloud e on-local

As organizações precisam de um processo para gerir o acesso que seja escalável. Os utilizadores continuam a acumular direitos de acesso e acabam por ficar para além do que inicialmente lhes era previsto. Além disso, as organizações empresariais têm de ser capazes de escalar de forma eficiente para desenvolver e impor a política e os controlos de acesso de forma contínua.

Normalmente, os delegados de TI acedem a decisões de aprovação a decisores empresariais. Além disso, a TI pode envolver os próprios utilizadores. Por exemplo, os utilizadores que acedem a dados confidenciais dos clientes na aplicação de marketing de uma empresa na Europa precisam de conhecer as políticas da empresa. Os utilizadores convidados também podem desconhecer os requisitos de manuseamento de dados numa organização para a qual foram convidados.

As organizações podem automatizar o processo de ciclo de vida de acesso através de tecnologias como [grupos dinâmicos,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)juntamente com o fornecimento de utilizadores a [aplicações SaaS,](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)ou [aplicações integradas utilizando a norma System for Cross-Domain Identity Management (SCIM).](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups) As organizações também podem controlar quais [os utilizadores convidados que têm acesso a aplicações no local.](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) Estes direitos de acesso podem então ser regularmente revistos através de avaliações recorrentes de [acesso ao Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

## <a name="future-directions"></a>Direções futuras

Em ambientes híbridos, a estratégia da Microsoft é permitir implementações onde a nuvem é o plano de **controlo para a identidade**, e os diretórios no local e outros sistemas de identidade, como o Ative Directory e outras aplicações no local, são o alvo para fornecer acesso aos utilizadores. Esta estratégia continuará a garantir os direitos, identidades e acesso supor-se às aplicações e cargas de trabalho que deles dependem. Neste estado final, as organizações serão capazes de impulsionar a produtividade do utilizador final inteiramente a partir da nuvem.

![Arquitetura do Azure AD](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como começar nesta viagem, consulte <https://aka.ms/deploymentplans> os planos de implantação da AD Azure, localizados em . Fornecem orientações de ponta a ponta sobre como implementar as capacidades do Azure Ative Directory (Azure AD). Cada plano explica o valor do negócio, considerações de planeamento, design e procedimentos operacionais necessários para lançar com sucesso as capacidades comuns da AD Azure. A Microsoft atualiza continuamente os planos de implementação com as melhores práticas aprendidas com as implementações dos clientes e outros feedbacks quando adicionamos novas capacidades para gerir a partir da nuvem com o Azure AD.
