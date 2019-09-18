---
title: Kit de adoção de acesso condicional-Azure Active Directory
description: Adotando o acesso condicional do Azure AD para acesso aos recursos
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: c75ec0ec212c99e474d13892178acbdc14e5f386
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075279"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Adotando o acesso condicional do Azure AD

Em um mundo móvel e em nuvem, os usuários podem acessar os recursos da sua organização em qualquer lugar usando diferentes tipos de dispositivos e aplicativos. Como resultado, apenas se concentrando em quem pode acessar um recurso não é mais suficiente. Você pode controlar quem tem acesso e identificar onde o usuário está e qual dispositivo está sendo usado e muito mais.

Para fornecer esse controle, o **acesso condicional do Azure Active Directory (AD)** permite que você especifique as condições que qualquer usuário deve atender para acessar um aplicativo, como a MFA (autenticação multifator). O uso de políticas de acesso condicional controla como os usuários autorizados (usuários que receberam acesso a um aplicativo de nuvem) acessam aplicativos de nuvem sob condições específicas. Consulte [o que é o acesso condicional no Azure Active Directory](overview.md) para obter mais informações.

## <a name="key-benefits"></a>Principais vantagens

Os principais benefícios de usar o acesso condicional do Azure AD são:

* **Aumentar a produtividade:** As políticas de acesso condicional (CA) permitem que você direcione o ponto em que os usuários são solicitados a usar a MFA, têm acesso bloqueado ou são necessários para usar um dispositivo confiável. Por exemplo, você pode definir políticas, como exigir apenas usuários para MFA em um aplicativo quando estiver fora da rede corporativa. A redução de solicitações de MFA mantém os usuários mais produtivos do que se eles precisarem de MFA cada vez que entrarem. Além disso, o acesso condicional do Azure AD permite que você especifique políticas por usuário e também cria políticas específicas do aplicativo.
* **Gerenciar risco:** A habilitação de políticas de acesso condicional fornece proteção de identidade em escala de nuvem, recursos de controle de acesso baseado em risco e suporte à autenticação multifator nativo. O acoplamento do acesso condicional com a proteção de identidade permite que você defina quando o acesso a um aplicativo é bloqueado ou restringido.
* **Atender à conformidade e governança:** A auditoria de solicitações de acesso e aprovações para o aplicativo e a compreensão do uso geral do aplicativo é mais fácil com o Azure AD, pois oferece suporte a logs de auditoria nativos para cada solicitação de acesso do aplicativo executada. A auditoria inclui a identidade do solicitante, a data solicitada, a justificativa de negócios, o status de aprovação e a identidade do aprovador. Esses dados também estão disponíveis em uma API, o que permitirá a importação desses dados em um sistema de opções de SIEM (monitoramento de eventos e incidentes de segurança) de sua escolha.
* **Gerenciar custo:** Mover as políticas de acesso para o Azure AD reduz a dependência em soluções personalizadas ou locais, como o Serviços de Federação do Active Directory (AD FS) (ADFS) para acesso condicional, reduzindo o custo da execução dessa infraestrutura.

## <a name="customer-case-studies"></a>Casos práticos de clientes

Descubra como a maioria das organizações usa o acesso condicional do Azure AD para definir e implementar decisões de controle de acesso automatizadas para acessar aplicativos de nuvem com base em condições. As seguintes histórias em destaque demonstram como essas necessidades do cliente são atendidas.

* [O **Wipro** impulsiona a produtividade móvel com as ferramentas de segurança de nuvem da Microsoft para melhorar os compromissos com o cliente.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) As políticas de acesso condicional no Azure AD permitiram que a empresa compartilhe documentos, recursos e aplicativos com entidades externas confiáveis---que possam usar suas próprias credenciais---ao mesmo tempo em que mantém o controle sobre seus próprios dados corporativos.
* [A **Accenture** protege sua mudança para a nuvem com Microsoft Cloud app Security](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) Accenture está avaliando o recurso [controle de aplicativos de acesso condicional](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) da Cloud app Security, que usa Azure Active Directory acesso condicional ao Gate acesso ao aplicativo com base em determinadas condições. LePenske diz que esse recurso pode ser útil para, digamos, habilitar o acesso a arquivos somente leitura e proibir downloads.
* [Entrega **ARAMEX** limitada-a logística global e a empresa de transporte criam um escritório conectado à nuvem com a solução de gerenciamento de identidade e acesso](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Garantir o acesso seguro era especialmente difícil com os funcionários remotos do ARAMEX. A empresa agora está aplicando o acesso condicional para permitir que esses funcionários remotos acessem seus aplicativos SaaS de fora da rede. A regra de acesso condicional decidirá se deseja impor a autenticação multifator, concedendo apenas às pessoas certas o acesso certo.

Para saber mais sobre as experiências de clientes e parceiros no acesso condicional do Azure AD, visite- [Veja as coisas incríveis que as pessoas estão fazendo com o Azure](https://azure.microsoft.com/case-studies/?service=active-directory).

## <a name="announcements"></a>Anúncios

O Azure AD recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, consulte [o que há de novo na Azure Active Directory?](../fundamentals/whats-new.md)

Blogs recentes da comunidade técnica e da divisão de identidade da Microsoft:

* 24 de setembro de 2018, [Azure Active Directory acesso condicional no Azure Databricks](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 21 de setembro de 2018, os [controles personalizados de acesso condicional do Azure ad estão em visualização pública](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 21 de setembro de 2018, o [suporte ao acesso condicional do Azure ad para acesso limitado com o Microsoft Cloud app Security agora está disponível](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 21 de setembro de 2018 [, acesso condicional do Azure AD: Suporte de navegador gerenciado para plataformas iOS/Android agora em visualização](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 21 de setembro de 2018, o [acesso condicional do Azure ad para códigos de país está em visualização pública](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 21 de setembro de 2018, os [termos de uso do Azure ad agora estão disponíveis](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>Recursos de aprendizagem

Siga os links abaixo para obter uma visão geral de como o acesso condicional do Azure AD funciona.

* Saiba "[o que é o acesso condicional no Azure Active Directory?](overview.md)"
* Sabe "[o que são condições no Azure Active Directory acesso condicional?](conditions.md)"
* Saiba "[qual é a condição de localização em Azure Active Directory acesso condicional?](location-condition.md)"
* Sabe "[o que são controles de acesso no Azure Active Directory acesso condicional?](controls.md)"
* Localizar "[o que é a ferramenta e se em Azure Active Directory acesso condicional?"](what-if-tool.md)
* Siga [as práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md)

Além disso, consulte os links a seguir para obter diretrizes para proteger o acesso a todos os serviços que são integrados com o Azure Active Directory.

* [O que é proteção de linha de base (versão prévia)?](baseline-protection.md) A proteção de linha de base garante que você tenha pelo menos o nível de linha de base de segurança habilitado em seu ambiente de Azure Active Directory.
* [Configurações de acesso de dispositivo e identidade](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). Descreve como configurar o acesso seguro aos serviços de nuvem por meio de Enterprise Mobility + Security produtos implementando um ambiente e uma configuração recomendados, incluindo um conjunto prescrito de políticas de acesso condicional e recursos relacionados.
* [Azure Active Directory referência de configurações de acesso condicional](technical-reference.md). Saber
   * Quais aplicativos usam o acesso condicional?
   * Quais serviços estão habilitados com acesso condicional?
* [Habilite Azure Active Directory acesso condicional para proteger o acesso do usuário](https://www.youtube.com/watch?v=eLAYBwjCGoA). Assista a este vídeo para saber como o acesso condicional desempenha uma função em outras cargas de trabalho do Enterprise e do Mobility Suite.

### <a name="training-videos"></a>Vídeos de formação

**Acesso condicional no Enterprise Mobility + Security**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Acesso condicional com base no dispositivo**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**Habilitar Azure Active Directory para acesso condicional para acesso de usuário seguro**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>Cursos online

Consulte os seguintes cursos de acesso condicional e muito mais em [pluralsight.com](https://www.pluralsight.com/):

* Pluralsight.com: [Projete o gerenciamento de identidades no Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "Este curso orienta você pelos principais itens que você precisa saber para projetar sua solução de gerenciamento de identidades com o Azure AD". O acesso condicional do Azure AD é abordado no módulo "usando funções e controle de acesso com o Azure AD".

* Pluralsight.com: [Autenticação de design para Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "Este curso explica como usar o Azure AD para resolver todos os seus requisitos de autenticação de nuvem." O acesso condicional do Azure AD é abordado no módulo "requisitos de autenticação para cenários diferentes".

* Pluralsight.com: [Autorização de design para Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "Este curso ensina as opções de autorização disponíveis com o Azure e o Azure AD". O acesso condicional do Azure AD é abordado no módulo "autorização com Azure Resource Manager e Azure AD".

### <a name="books"></a>Livros

* O ' Reilly- [implementando o Azure Solutions-Second Edition.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "Comece a executar com os serviços do Azure e saiba como implementá-los em sua organização. O acesso condicional do Azure AD é abordado no capítulo [Implantando e sincronizando Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml). "

* Wiley [serviços de infraestrutura de Microsoft Azure de mestre](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * "Aqui está tudo o que você precisa para entender, avaliar, implantar e manter ambientes que utilizam Microsoft Azure".

## <a name="white-papers"></a>Documentação técnica

* Publicado em 18 de dezembro de 2018, [crie uma estratégia de gerenciamento de controle de acesso resiliente com Azure Active Directory](../authentication/concept-resilient-controls.md)
   * Este documento fornece orientação sobre estratégias que uma organização pode adotar para fornecer resiliência para reduzir o risco de bloqueio durante interrupções imprevistas.

* Publicado em 18 de setembro de 2018, [recursos para migrar aplicativos para Azure Active Directory](../manage-apps/migration-resources.md)
   * Este White Paper inclui uma lista de recursos para ajudá-lo a migrar o acesso e a autenticação do aplicativo para o Azure Active Directory (Azure AD).

* Publicado em 12 de julho [de 2018 Blueprint de segurança e conformidade do Azure: Hospedagem de aplicativos Web de PaaS para cargas de trabalho oficiais do Reino Unido](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Os planos gráficos do Azure consistem em documentos de orientação e modelos de automação que implantam arquiteturas baseadas em nuvem para oferecer soluções a cenários com requisitos de certificação ou conformidade.

## <a name="guidance-for-it-administrators"></a>Diretrizes para administradores de ti

Entre no [portal do Azure](https://portal.azure.com/) como administrador global, administrador de segurança ou administrador de acesso condicional. Consulte [permissões de função de administrador no Azure Active Directory.](../users-groups-roles/directory-assign-admin-roles.md)

Como um administrador de ti, use o [acesso condicional do Azure ad](overview.md) para exigir que os usuários se autentiquem usando a autenticação multifator do Azure, entre de uma rede confiável ou de um dispositivo confiável.

Aqui estão links úteis para ajudá-lo a começar:

* [Práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md)
* [Usar as revisões de acesso do Azure AD para gerenciar usuários que foram excluídos das políticas de acesso condicional](../governance/conditional-access-exclusion.md)
* [How To: Planejar sua implantação de acesso condicional no Azure Active Directory](plan-conditional-access.md)
* [Quickstart: Exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional](app-based-mfa.md)
* [Quickstart: Exigir que os termos de uso sejam aceitos antes de acessar os aplicativos de nuvem](require-tou.md)
* [Quickstart: Bloquear o acesso quando um risco de sessão for detectado com Azure Active Directory acesso condicional](app-sign-in-risk.md)
* [Perguntas frequentes de acesso condicional do Azure AD](faqs.md)
   * Para outras perguntas, você também pode exibir o [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure).
   * Se você não conseguir encontrar a resposta a um problema, nossas equipes de suporte estarão sempre disponíveis para ajudá-lo. Use [entre em contato com o suporte da Microsoft](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support).

### <a name="tutorials"></a>Tutoriais

* [**TUTORIAIS Exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional**](app-based-mfa.md)
   * Este guia de início rápido mostra como configurar uma política de acesso condicional do Azure AD que requer autenticação multifator para um aplicativo de nuvem selecionado em seu ambiente.

* [**TUTORIAIS Exigir que os termos de uso sejam aceitos antes de acessar os aplicativos de nuvem**](require-tou.md)
   * Este guia de início rápido mostra como configurar uma política de acesso condicional do Azure AD que exige que um ToU seja aceito para um aplicativo de nuvem selecionado em seu ambiente.

* [**TUTORIAIS Bloquear o acesso quando um risco de sessão for detectado com Azure Active Directory acesso condicional**](app-sign-in-risk.md)
   * Este guia de início rápido mostra como configurar uma política de acesso condicional que bloqueia uma entrada quando um nível de risco de entrada configurado for detectado.

* [Tutorial: **Migre uma política clássica que exija autenticação multifator no portal do Azure**](policy-migration-mfa.md)
   * Este tutorial mostra como migrar uma política clássica que exige a autenticação multifator (MFA) para um aplicativo de nuvem.

## <a name="end-user-readiness-and-communication"></a>Prontidão e comunicação do usuário final

O acesso condicional usa outros recursos do Azure AD que podem afetar a experiência do usuário final. Por exemplo, você pode usar a autenticação multifator do Azure para habilitar a autenticação forte para usuários. Nesse caso, você usará os modelos de usuário final do Azure MFA.

## <a name="next-steps"></a>Passos Seguintes

* Inicie sua implantação com a [documentação de planejamento de implantação de acesso condicional](plan-conditional-access.md).
