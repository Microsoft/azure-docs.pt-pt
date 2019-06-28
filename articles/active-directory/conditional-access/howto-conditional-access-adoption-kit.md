---
title: Kit de adoção de acesso condicional - Azure Active Directory
description: Adotar o acesso condicional do Azure AD para acesso a recursos
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
ms.openlocfilehash: 2cc4ff5fb528760be8c910f3da7d5691a6aae0d8
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2019
ms.locfileid: "67387576"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Adotar o acesso condicional do Azure AD

Num mundo de dispositivos móveis e da cloud, os utilizadores podem aceder de recursos da sua organização de qualquer lugar usando diferentes tipos de dispositivos e aplicações. Como resultado, se concentrar apenas na que pode aceder a um recurso já não é suficiente. Pode controlar quem tem acesso e identificar em que é o utilizador e dispositivos que está a ser utilizado e muito mais.

Para fornecer esse controle **acesso condicional do Azure Active Directory (AD)** permite-lhe especificar as condições de qualquer utilizador tem de cumprir para acesso a uma aplicação, como o multi-factor Authentication (MFA). Utilizar políticas de acesso condicional controla como autorizados (utilizadores que tenham tido acesso a uma aplicação da cloud) acedam a aplicações na cloud em condições específicas. Consulte a [o que é o acesso condicional no Azure Active Directory](overview.md#conditional-access-policies) para obter mais informações.

## <a name="key-benefits"></a>Principais vantagens

Os principais benefícios do uso de acesso condicional do Azure AD são:

* **Aumente a produtividade:** As políticas de acesso (AC) condicional lhe permitem destinar o ponto em que é pedido aos utilizadores para utilizar a MFA, tem acesso bloqueado ou são necessárias para utilizar um dispositivo fidedigno. Por exemplo, pode definir políticas, tais como apenas que estes tenham de MFA num aplicativo quando desconectados da rede corporativa. A redução de pedidos MFA evita que os usuários mais produtivas do que se eles têm a MFA sempre que iniciam sessão. Além disso, o acesso condicional do Azure AD permite-lhe especificar a base de por utilizador de políticas e também cria políticas específicas de aplicação.
* **Gerir o risco:** Ativar políticas de acesso condicional fornece proteção de identidade de escala da cloud, os recursos de controle de acesso baseado em risco e suporte nativo multi-factor authentication. O acoplamento acesso condicional com o identity protection permite-lhe definir quando o acesso a uma aplicação é bloqueado ou Check controlado.
* **Governação e conformidade de endereço:** Pedidos de acesso e aprovações para o aplicativo de auditoria e compreender a utilização geral do aplicativo são mais fácil com o Azure AD uma vez que suporta os registos de auditoria de nativo para cada solicitação de acesso da aplicação realizada. Auditoria inclui a identidade do autor do pedido, data solicitada, justificação de negócio, status de aprovação e identidade do aprovador. Estes dados também estão disponíveis a partir de uma API, o que permitirá a importação destes dados para um incidente de segurança e sistema de monitorização de eventos (SIEM) à escolha.
* **Gerir o custo:** Mover as políticas de acesso para o Azure AD reduz a dependência personalizadas ou soluções no local, como o Active Directory Federation Services (ADFS) para o acesso condicional, reduzindo o custo de executar essa infraestrutura.

## <a name="customer-case-studies"></a>Casos práticos de clientes

Descubra como a maioria das organizações utilizar acesso condicional do Azure AD para definir e implementar as decisões de controlo de acesso automatizado para aceder a aplicações na cloud com base em condições. As histórias em destaque seguintes demonstram como essas necessidades dos clientes são cumpridas.

* [**A Wipro** impulsionam a produtividade móvel com as ferramentas de segurança do Microsoft cloud para melhorar o envolvimento com o cliente.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) As políticas de acesso condicional no Azure AD tem ativado a empresa partilhar documentos, recursos e aplicativos com entidades externas fidedignas---que pode utilizar as suas próprias credenciais---e manter o controlo sobre os seus próprios dados empresariais.
* [**Accenture** protege sua mudança para a nuvem com o Microsoft Cloud App security](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) Accenture está a avaliar o [controlo de aplicação de acesso condicional](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) recurso do Cloud App Security, que utiliza o Active Directory do Azure Acesso condicional para acesso de aplicação de porta com base em determinadas condições. LePenske diz que esta funcionalidade pode ser útil para, digamos, permitir o acesso de ficheiros só de leitura e a proibição de downloads.
* [**Aramex** entrega limitado - empresa Global de logística e transportes cria office ligadas à cloud com a solução de gestão de identidades e acessos](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Garantir acesso seguro foi especialmente difícil com funcionários de remotos do Aramex. A empresa está agora a aplicar acesso condicional para permitir que estes funcionários remotos acesso às suas aplicações de SaaS a partir de fora da rede. A regra de acesso condicional irá decidir se pretende impor o multi-factor Authentication, dando o acesso a apenas as pessoas certas.

Para saber mais sobre as experiências de clientes e parceiros com acesso condicional do Azure AD, visite - [veja estão a fazer as pessoas de coisas fantásticas com o Azure](https://azure.microsoft.com/case-studies/?service=active-directory).

## <a name="announcements"></a>Anúncios

O Azure AD recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, consulte [o que há de novo no Azure Active Directory?](../fundamentals/whats-new.md)

Blogs recentes pela Comunidade tecnológica e divisão de identidade da Microsoft:

* 24 de Setembro de 2018, [acesso condicional do Azure Active Directory no Azure Databricks](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 21 de Setembro de 2018, [acesso condicional do Azure AD controles personalizados estão em pré-visualização pública](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 21 de Setembro de 2018, [suporte de acesso condicional do Azure AD para acesso limitado ao Microsoft Cloud App Security está agora disponível](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 21 de Setembro de 2018, [acesso condicional do Azure AD: Suporte a navegador para plataformas iOS/Android, agora em pré-visualização gerenciados](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 21 de Setembro de 2018, [acesso condicional do Azure AD para códigos de país está em pré-visualização pública](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 21 de Setembro de 2018, [do Azure AD termos de uso agora disponível](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>Recursos de aprendizado

Siga os links abaixo para obter uma visão geral do acesso condicional do AD como o Azure.

* Saiba mais "[o que é o acesso condicional no Azure Active Directory?](overview.md)"
* Saber "[quais são condições no Azure Active Directory condicional Access?](conditions.md)"
* Saber "[o que é a condição de localização no Azure Active Directory condicional acesso?](location-condition.md)"
* Saber "[o que são acedidos controles no Azure Active Directory condicional acesso?](controls.md)"
* Localizar "[o que é o que, se a ferramenta no Azure Active Directory de acesso condicional?"](what-if-tool.md)
* Siga [melhores práticas para acesso condicional no Azure Active Directory](best-practices.md)

Além disso, veja as ligações seguintes para obter orientações proteger o acesso a todos os serviços que estão integradas no Azure Active Directory.

* [O que é a proteção de linha de base (pré-visualização)?](baseline-protection.md) Proteção de linha de base garante que tem, pelo menos, o nível de linha de base de segurança ativada no seu ambiente do Azure Active Directory.
* [Configurações de acesso de dispositivos e identidade](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). Descreve como configurar o acesso seguro aos serviços cloud através do Enterprise Mobility + produtos de segurança através da implementação de um ambiente recomendada e a configuração, incluindo um conjunto específico de políticas de acesso condicional e recursos relacionados.
* [Referência de definições de acesso condicional de diretório Active Directory do Azure](technical-reference.md). Saiba mais:
   * Que aplicações utilizam o acesso condicional?
   * Que serviços estão ativados com o acesso condicional?
* [Ativar o acesso condicional do Azure Active Directory para acesso de usuário segura](https://www.youtube.com/watch?v=eLAYBwjCGoA). Veja este vídeo para saber como o acesso condicional desempenha um papel em cargas de trabalho de outras empresas e Mobility Suite.

### <a name="training-videos"></a>Vídeos de formação

**Acesso condicional no Enterprise Mobility + Security**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Acesso condicional com base no dispositivo**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**Ativar o Azure Active Directory para o acesso condicional para o acesso de usuário segura**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>Cursos online

Consulte os seguintes cursos de acesso condicional e muito mais no [Pluralsight.com](https://www.pluralsight.com/):

* Pluralsight.com: [Gestão de identidades de design no Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "Este curso orienta os itens principais que precisa saber para estruturar a solução de gestão de identidade com o Azure AD." Acesso condicional do Azure AD é abordado em "Utilizar funções e controlo de acesso com o Azure AD" módulo.

* Pluralsight.com: [Autenticação de design para o Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "Este curso explica como utilizar o Azure AD para resolver todos os seus requisitos de autenticação na nuvem." Acesso condicional do Azure AD é abordado no módulo "Requisitos para diferentes cenários de autenticação".

* Pluralsight.com: [Autorização de design para o Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "Este curso ensina opções de autorização disponíveis com o Azure e Azure AD." Acesso condicional do Azure AD é abordado no módulo "Autorização com o Azure Resource Manager e o Azure AD".

### <a name="books"></a>Livros

* O ' Reilly - [implementar soluções do Azure - Second Edition.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "Começar a trabalhar com serviços do Azure e saiba como implementá-los na sua organização. Acesso condicional do Azure AD é abordado no capítulo [Deploying e sincronização do Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml). "

* Wiley- [dominar os serviços de infraestrutura do Microsoft Azure](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * "Eis tudo o que precisa para compreender, avaliar, implantar e manter ambientes que utilizam o Microsoft Azure."

## <a name="white-papers"></a>Documentação técnica

* Publicado a 18 de Dezembro de 2018, [criar uma estratégia de gestão de controlo de acesso resilientes no Azure Active Directory](../authentication/concept-resilient-controls.md)
   * Este documento fornece orientação sobre estratégias de uma organização pode adotar para fornecer resiliência para reduzir o risco de bloqueio durante as interrupções imprevistas.

* Publicado a 18 de Setembro de 2018, [recursos para a migração de aplicativos para o Azure Active Directory](../manage-apps/migration-resources.md)
   * Este White Paper, inclui uma lista de recursos para ajudar a migrar o acesso à aplicação e a autenticação para o Azure Active Directory (Azure AD).

* Publicado a 12 de Julho de 2018 [Azure Security and Compliance Blueprint: Aplicação Web de PaaS de alojamento para cargas de trabalho oficial do Reino Unido](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Planos gráficos do Azure é composto por documentos de diretrizes e modelos de automatização que implementar arquiteturas com base na cloud para oferecer soluções para cenários que têm requisitos de compatibilidade ou acreditação.

## <a name="guidance-for-it-administrators"></a>Documentação de orientação para os administradores de TI

Inicie sessão para o [portal do Azure](https://portal.azure.com/) como um administrador global, administrador de segurança ou administrador de acesso condicional. Consulte [permissões da função de administrador no Azure Active Directory.](../users-groups-roles/directory-assign-admin-roles.md)

Como um administrador de TI, utilize [acesso condicional do Azure AD](overview.md) para exigir que os utilizadores autenticar com o Azure multi-factor Authentication, iniciar sessão a partir de uma rede fidedigna ou dispositivo fidedigno.

Seguem-se links úteis para ajudar a começar:

* [Melhores práticas para acesso condicional no Azure Active Directory](best-practices.md)
* [Utilize o Azure AD as revisões de acesso para gerir utilizadores que foram excluídos da políticas de acesso condicional](../governance/conditional-access-exclusion.md)
* [How To: Planear a implementação de acesso condicional no Azure Active Directory](plan-conditional-access.md)
* [Quickstart: Exigir a MFA para aplicações específicas com o Azure Active Directory condicional acesso](app-based-mfa.md)
* [Quickstart: Exigir a termos de utilização para ser aceite antes de aceder a aplicações na cloud](require-tou.md)
* [Quickstart: Bloquear o acesso quando um risco de sessão é detetado com o Azure Active Directory condicional acesso](app-sign-in-risk.md)
* [FAQs de acesso condicional do Azure AD](faqs.md)
   * Para mais questões, também pode ver o [fórum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure).
   * Se não conseguir encontrar a resposta a um problema, a nossas equipas de suporte estão sempre disponíveis para ajudá-lo ainda mais. Uso [suporte da Microsoft de contacto](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support).

### <a name="tutorials"></a>Tutoriais

* [**Início rápido: Exigir a MFA para aplicações específicas com o Azure Active Directory condicional acesso**](app-based-mfa.md)
   * Este guia de introdução mostra como configurar uma política de acesso condicional do Azure AD que requer autenticação multifator para uma aplicação em nuvem selecionado no seu ambiente.

* [**Início rápido: Exigir a termos de utilização para ser aceite antes de aceder a aplicações na cloud**](require-tou.md)
   * Este início rápido mostra como configurar uma política de acesso condicional do Azure AD que requeira um termos de utilização sejam aceites para uma aplicação em nuvem selecionado no seu ambiente.

* [**Início rápido: Bloquear o acesso quando um risco de sessão é detetado com o Azure Active Directory condicional acesso**](app-sign-in-risk.md)
   * Este início rápido mostra como configurar uma política de acesso condicional bloqueia um início de sessão, quando foi detetado um nível de risco de início de sessão configurados.

* [Tutorial: **Migrar de uma política clássica que exija a autenticação multifator no portal do Azure**](policy-migration-mfa.md)
   * Este tutorial mostra como migrar uma política clássica, que requer autenticação multifator (MFA) para uma aplicação na cloud.

## <a name="end-user-readiness-and-communication"></a>Preparação para o utilizador final e a comunicação

Acesso condicional utiliza outros recursos do Azure AD que possam afetar a experiência do utilizador final. Por exemplo, pode utilizar o Azure multi-factor authentication para ativar a autenticação forte para os utilizadores. Nesse caso, irá utilizar os modelos de utilizador final do MFA do Azure.

## <a name="next-steps"></a>Passos Seguintes

* Iniciar a implementação com o [documentação de planeamento de implementação de acesso condicional](plan-conditional-access.md).
