---
title: Gestão para cargas de trabalho no local - Azure regem-se da Cloud do Azure AD
description: Este tópico descreve a gestão da cloud regem-se para cargas de trabalho no local.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109511"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Como o Azure AD fornece Cloud regem-se a gestão de cargas de trabalho no local

Azure Active Directory (Azure AD) é uma solução identidade como uma solução de serviço (IDaaS) utilizada por milhões de organizações que abrangem todos os aspetos da identidade, gestão de acesso e segurança. Azure AD armazena identidades de utilizador de mais de um bilhão e ajuda os utilizadores iniciar sessão e aceder em segurança ambos:

* Recursos externos, como o Microsoft Office 365, o portal do Azure e milhares de outras aplicações de Software-como-serviço (SaaS).
* Recursos internos, tais como aplicações numa rede empresarial da organização e a intranet, juntamente com quaisquer aplicações de cloud desenvolvida nessa organização.

As organizações podem utilizar o Azure AD, se eles são "cloud pura", ou como um "híbrido" implementação se tiverem no local cargas de trabalho. Implementação híbrida do Azure AD pode fazer parte de uma estratégia para uma organização para migrar os seus ativos de TI para a cloud ou para continuar para integrar a infraestrutura no local existente, juntamente com os novos serviços em nuvem.

Historicamente, as organizações de "híbrido" tenham visto do Azure AD, como uma extensão existente infraestrutura no local. Estas implementações, a administração de governação de identidade no local, Windows Server Active Directory ou outros sistemas de diretório internos, são os pontos de controle e os utilizadores e grupos são sincronizados a partir desses sistemas com um diretório na cloud como o Azure AD. Assim que são essas identidades na cloud, eles podem ser disponibilizados para o Office 365, Azure e outros aplicativos.

![Ciclo de vida de identidade](media/cloud-governed-management-for-on-premises//image1.png)

À medida que as organizações se mais da infraestrutura de TI, juntamente com seus aplicativos para a cloud, muitos estão procurando o melhor segurança e as capacidades de gestão simplificada de gestão de identidades como um serviço. As funcionalidades de IDaaS fornecida pela cloud no Azure AD acelerar a transição para a cloud de gestão regulado, fornecendo as soluções e capacidades que permitem às organizações rapidamente adotar e mover mais o gerenciamento de identidade do tradicional no local sistemas com o Azure AD, enquanto continua a oferecer suporte a aplicativos existentes, bem como novas.

Este documento descreve a estratégia da Microsoft para híbrida IDaaS e descreve a forma como as organizações podem utilizar o Azure AD para seus aplicativos existentes.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>A abordagem do Azure AD para a cloud de gestão de identidades regulados

Como a transição de organizações para a cloud, que precisam de garantias de que eles têm controles em seu ambiente completo - segurança mais e mais visibilidade em atividades, suportado pela automatização e os insights proativa. "**Cloud regida gestão**" descreve como as organizações a gerirem e regulam a seus usuários, aplicativos, grupos e dispositivos a partir da cloud.

Neste mundo moderno, as organizações precisam ser capazes de gerenciar com eficiência em escala, devido a proliferação de aplicações SaaS e a função de cada vez maior de colaboração e de identidades externas. O novo cenário de risco da cloud significa que uma organização tem de ser mais reativa – um ator malicioso que compromete um usuário na nuvem pode afetar a aplicações na cloud e no local.

Em particular, híbrido, as organizações precisam poder delegar e automatizar tarefas, que historicamente IT fez manualmente. Para automatizar tarefas, elas precisam de APIs e os processos que orquestrar o ciclo de vida dos diferentes relacionadas com a identidade recursos (utilizadores, grupos, aplicações, dispositivos), para que eles podem delegar o gerenciamento diário desses recursos para mais pessoas fora do a equipe de TI de núcleo. Azure AD lida com esses requisitos através da gestão de conta de utilizador e autenticação nativo para os utilizadores sem a necessidade de infraestrutura no local identidade. Não a criação de infraestrutura no local pode trazer benefícios para as organizações que tenham a novas Comunidades de utilizadores, tais como parceiros de negócios, que não têm origem em seu diretório no local, mas cuja gestão de acesso é crítico para alcançar os resultados empresariais.

Além disso, a gestão não está completa sem governação--- e governação no novo mundo de hoje é uma parte integrada do sistema de identidade, em vez de um suplemento. Governação de identidade proporciona às organizações a capacidade de gerir a identidade e acesso ciclo de vida em funcionários, parceiros de negócios e fornecedores e serviços e aplicações.

Incorporando a governação de identidade torna mais fácil permitir que a organização a transição para a cloud de gestão regulado, permite que a TI aumenta, aborda os novos desafios com convidados e fornece mais informações e automatização que o que os clientes tinham de com infraestrutura no local. Governação neste novo mundo significa que a capacidade para uma organização ter transparência, visibilidade e controlos adequados sobre o acesso a recursos dentro da organização. Com o Azure AD, operações de segurança e as equipas de auditoria tem acesso de visibilidade sobre quem tem--- e quem precisa ter - a que recursos na organização (em que dispositivos), o que os utilizadores estão a fazer com que o acesso e se a organização tem e utiliza apropriada controlos para remover ou restringir o acesso em conformidade com a empresa ou as políticas de regulamentação.

O novo modelo de gestão beneficia as organizações com SaaS e aplicações de linha de negócio (LOB), como eles são mais facilmente capazes de gerenciar e proteger o acesso a essas aplicações. Ao integrar aplicações com o Azure AD, as organizações serão capazes de usar e gerir o acesso em ambos na cloud e no local teve origem identidades de forma consistente. Gerenciamento de ciclo de vida de aplicativos se torna mais automatizado, e o Azure AD fornece informações detalhadas sobre a utilização da aplicação que não era facilmente realizáveis em gestão de identidades no local. Através do Azure AD, grupos do Office 365 e funcionalidades de self-service de Equipes, as organizações podem facilmente criar grupos de gestão de acesso e de colaboração e adicionar ou remover utilizadores na cloud, para permitir a colaboração e aceder aos requisitos de gestão.

Selecionar as capacidades da direita do Azure AD para a cloud de gestão governado depende dos aplicativos a ser utilizada e como esses aplicativos serão integrados com o Azure AD. As secções seguintes descrevem as abordagens de efetuar para aplicações integradas no AD e aplicações que utilizam os protocolos de Federação (por exemplo, SAML, OAuth ou OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Gestão de cloud regem-se para aplicações integradas no AD

O Azure AD melhora a gestão para aplicações no local uma organização integrado ao Active Directory através do acesso remoto seguro e de acesso condicional para esses aplicativos. Além disso, o Azure AD também fornece gerenciamento de ciclo de vida da conta e a gestão de credenciais para AD as contas existentes o utilizador, incluindo:

* **Acesso remoto seguro e de acesso condicional para aplicações no local**

Para muitas organizações, é o primeiro passo para gerir o acesso a partir da cloud para a web de integrada no AD no local e aplicações com base no ambiente de trabalho remotas implementar o [proxy de aplicações](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) à frente esses aplicativos para fornecer seguro acesso remoto.

Após um início de sessão único para o Azure AD, os utilizadores podem aceder aplicações na cloud e no local através de um URL externo ou um portal de aplicação interna. Por exemplo, o Proxy de aplicações fornece acesso remoto e início de sessão único para o ambiente de trabalho remoto, SharePoint, bem como aplicações, como Tableau, Qlik e aplicativos linha de negócios (LOB). Além disso, as políticas de acesso condicional podem incluir exibindo os [termos de utilização](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) e [garantir que o usuário concordou-se aos mesmos](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) antes de poder aceder a uma aplicação.

![Arquitetura de Proxy de aplicações](media/cloud-governed-management-for-on-premises/image2.png)

* **Gestão de ciclo de vida de automática para contas do Active Directory**

Governação de identidade ajuda as organizações a atingir um equilíbrio entre *produtividade* ---quão rapidamente pode uma pessoa tem acesso aos recursos que precisa, como quando eles Junte-se a organização? --- e *segurança* ---como deve respetivo acesso alteram ao longo do tempo, por exemplo, quando altera o estado de emprego essa pessoa? Gerenciamento de ciclo de vida de identidade é a base de governação de identidade e, em vigor governação em escala requer modernizar a infraestrutura de gestão do ciclo de vida de identidade para aplicações.

Para muitas organizações, o ciclo de vida de identidade para os funcionários é associado à representação de que o utilizador num sistema de gestão do capital humano (HCM). Para organizações que utilizam o Workday como seus sistemas HCM, do Azure AD pode assegurar que as contas de utilizador no AD são [automaticamente aprovisionado e desaprovisionado para trabalhadores no Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial). Fazer assim, leva a produtividade do usuário aprimorada através da automatização de birthright contas e gerencia os riscos ao garantir que a aplicação de acesso é atualizado automaticamente quando um utilizador altera as funções ou sai da organização. O aprovisionamento de utilizadores e orientada para Workday [plano de implantação](https://aka.ms/WorkdayDeploymentPlan) é um guia passo a passo que descreve as organizações através da implementação de práticas recomendada do Workday para a solução de aprovisionamento e de utilizadores do Active Directory num processo de cinco etapas.

O Azure AD Premium também inclui o Microsoft Identity Manager, pode importar registos de outros sistemas HCM no local, incluindo SAP, Oracle eBusiness e o Oracle PeopleSoft.

Colaboração de empresa-empresa exige cada vez mais conceder acesso a pessoas fora da sua organização. [O Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) colaboração permite que as organizações partilhar em segurança seus serviços e aplicações com os utilizadores convidados e parceiros externos e manter o controlo sobre os seus próprios dados empresariais.

O Azure AD pode [automaticamente criar contas no AD para utilizadores convidados](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) conforme necessário, permitindo que os convidados empresariais aceder a aplicações no local integrada no AD sem a necessidade de outra palavra-passe. Podem configurar organizações [políticas de autenticação multifator (MFA) para o utilizador convidado](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)s para MFA verificações são feitas durante a autenticação de proxy de aplicação. Além disso, qualquer [as revisões de acesso](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) que são efetuadas na cloud B2B utilizadores aplicar aos utilizadores no local. Por exemplo, se o utilizador de cloud for eliminado através de políticas de gestão do ciclo de vida, também é eliminar o utilizador no local.

**Gerenciamento de contas do Active Directory de credenciais** do Azure AD do self-service a reposição de palavra-passe permite que os utilizadores que tenham esquecido suas palavras-passe pode ser reautenticada e repor as palavras-passe, com as palavras-passe alteradas [escritos no local do Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback). O processo de reposição de palavra-passe também pode utilizar as políticas de palavra-passe do Active Directory no local: Quando um utilizador repõe a palavra-passe, ele é verificado para assegurar cumpre a política do Active Directory no local antes de consolidá-lo para esse diretório. Repor a palavra-passe self-service [plano de implantação](https://aka.ms/deploymentplans/sspr) realça as melhores práticas para implementar o self-service reposição palavra-passe para utilizadores através da web e integrado ao Windows experiências.

![Arquitetura do Azure AD SSPR](media/cloud-governed-management-for-on-premises/image3.png)

Por fim, para organizações que permitem aos utilizadores alterarem as palavras-passe no AD, AD pode ser configurado para utilizar a mesma política de palavra-passe, conforme a organização está a utilizar no Azure AD através da [funcionalidade de proteção de palavra-passe do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), atualmente em pré-visualização pública.

Quando uma organização está pronta para migrar uma aplicação integrada no AD para a cloud ao mover o sistema operativo que aloja a aplicação no Azure, [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) fornece serviços de domínio do AD compatível (por exemplo, a associação a um domínio, diretiva de grupo, LDAP e Kerberos/NTLM authentication). O Azure AD Domain Services integra-se com o inquilino da organização existente do Azure AD, tornando possível para os utilizadores iniciem sessão com as credenciais da empresa. Além disso, os grupos existentes e contas de utilizador podem ser utilizadas para proteger o acesso a recursos, garantindo uma mais suave "migração lift-and-shift" de recursos no local para serviços de infraestrutura do Azure.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Gestão da cloud regem-se para aplicações de baseada em federação no local

Para uma organização que já utiliza um fornecedor de identidade no local, a mover aplicações para o Azure AD permite acesso mais seguro e uma experiência administrativa mais fácil para a gestão de Federação. O Azure AD permite configurar os controlos de acesso granulares por aplicação, incluindo multi-factor Authentication do Azure, com o acesso condicional do Azure AD. O Azure AD suporta capacidades mais, incluindo certificados de assinatura de tokens de específico do aplicativo e datas de expiração de certificados configuráveis. Esses recursos, ferramentas e orientações permitem às organizações extinguir os respetivos fornecedores de identidade no local. Do Microsoft IT, para obter um exemplo, foi movido 17,987 aplicativos de interna Active Directory Federation Services da Microsoft (AD FS) para o Azure AD.

![Evolução do Azure AD](media/cloud-governed-management-for-on-premises/image5.png)

Para começar a migrar aplicações federadas com o Azure AD como fornecedor de identidade, consulte https://aka.ms/migrateapps que inclui ligações para:

* O white paper [migrar seus aplicativos para o Azure Active Directory](https://aka.ms/migrateapps/whitepaper), que apresenta as vantagens da migração e descreve como planejar a migração em quatro fases, claramente descritos: deteção, classificação, migração, e gestão contínua. Vai ser orientado ao longo como pensar sobre o processo e dividir seu projeto em partes de fácil compreensão. Ao longo do documento são links para recursos importantes que ajudarão ao longo do caminho.

* O guia de soluções [migrar autenticação de aplicação de serviços de Federação do Active Directory ao Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide) explora mais detalhadamente as mesmas quatro fases de planejamento e a execução de um projeto de migração de aplicativos . Neste guia, aprenderá como aplicar essas fases para o objetivo específico de mover um aplicativo de serviços de Federação do Active Directory (AD FS) para o Azure AD.

* O [Active Directory Federação serviços preparação para o Script de migração](https://aka.ms/migrateapps/adfstools) podem ser executadas em servidores de serviços de Federação do Active Directory (AD FS) no local existentes para determinar a preparação de aplicativos para a migração para o Azure AD.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Gestão de acesso em curso em aplicações na cloud e no local

As organizações precisam de um processo para gerir o acesso escalonável. Os utilizadores continuam a acumular direitos de acesso e acabar com além do que foi inicialmente provisionado para eles. Além disso, as empresas precisam de ser capaz de dimensionar de forma eficiente para desenvolver e impor a política de acesso e controles de forma contínua.

Normalmente, os delegados IT decisões de aprovação para os tomadores de decisões de acesso. Além disso, IT pode envolver os próprios usuários. Por exemplo, os utilizadores que acedem a dados confidenciais do cliente no aplicativo de marketing de uma empresa na Europa precisam de saber as políticas da empresa. Os utilizadores convidados também podem não ter conhecimento dos requisitos de tratamento para os dados numa organização para o qual foi convidados.

As organizações podem automatizar o processo de ciclo de vida de acesso por meio de tecnologias como [grupos dinâmicos](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership), conjugadas com o aprovisionamento do utilizador [aplicações SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list), ou [aplicativos integrado com o sistema para a gestão de identidade entre domínios (SCIM](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)) padrão. As organizações também podem controlar quais [os utilizadores convidados têm acesso a aplicações no local](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises). Estes podem de direitos de acesso, em seguida, ser regularmente revisados recorrente [revisões de acesso do Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

## <a name="future-directions"></a>Perspectivas para o futuro

Em ambientes híbridos, estratégia da Microsoft é permitir implementações em que o **cloud é o plano de controlo para a identidade**e diretórios no local e outros sistemas de identidade, como o Active Directory e outros no local os aplicativos, são o destino para o aprovisionamento de utilizadores com acesso. Esta estratégia continuará a ter garantir que os direitos, identidades e acesso nesses aplicativos e cargas de trabalho que contam com eles. Neste estado final, as organizações serão capazes de produtividade do utilizador final de unidade inteiramente a partir da cloud.

![Arquitetura do Azure AD](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como começar a utilizar durante este processo, veja os planos de implementação do Azure AD, localizados em <https://aka.ms/deploymentplans> . Eles fornecem documentação de orientação de ponta a ponta sobre como implementar recursos do Azure Active Directory (Azure AD). Cada plano explica o valor comercial, planeamento considerações de design e procedimentos operacionais necessários para implementar com êxito a capacidades de comuns do Azure AD. Microsoft atualiza continuamente os planos de implantação com práticas recomendadas aprendidas implementações dos clientes e outros comentários quando adicionamos novas capacidades para gerir a partir da cloud com o Azure AD.
