---
title: Migrar a autenticação de aplicações para o Azure Ative Directory
description: Este livro branco detalha o planeamento e benefícios de migrar a autenticação da sua aplicação para a Azure AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/05/2021
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c1a0be0b7834632ba79af7dfe6c3a4fa25c0316
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645516"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Migrar a autenticação de aplicações para o Azure Ative Directory

## <a name="about-this-paper"></a>Sobre este papel

Este livro branco detalha o planeamento e benefícios de migrar a autenticação da sua aplicação para a Azure AD. É projetado para administradores da Azure e profissionais de identidade.

Quebrando o processo em quatro fases, cada uma com critérios de planeamento e saída detalhados, é projetado para ajudá-lo a planear a sua estratégia de migração e entender como a autenticação AD AD AZure suporta os seus objetivos organizacionais.

## <a name="introduction"></a>Introdução

Hoje em dia, a sua organização requer uma série de aplicações (apps) para que os utilizadores consigam trabalhar. É provável que continue a adicionar, desenvolver ou reformar aplicações todos os dias. Os utilizadores acedem a estas aplicações a partir de uma vasta gama de dispositivos corporativos e pessoais, e locais. Abrem aplicações de muitas formas, incluindo:

- através de uma página inicial da empresa ou portal

- marcando nos seus navegadores

- através do URL de um fornecedor para software como um serviço (SaaS) apps

- links empurrados diretamente para os ambientes de trabalho ou dispositivos móveis do utilizador através de uma solução móvel de gestão de dispositivos/aplicações (MDM/MAM)

As suas aplicações estão provavelmente a utilizar os seguintes tipos de autenticação:

- Soluções da federação no local (tais como Serviços da Federação de Diretórios Ativos (ADFS) e Ping)

- Diretório Ativo (como Kerberos Auth e Windows Integrated Auth)

- Outras soluções de gestão de identidade e acesso (IAM) baseadas na nuvem (como Okta ou Oracle)

- Infraestruturas web no local (como IIS e Apache)

- Infraestruturas hospedadas em nuvem (como Azure e AWS)

**Para garantir que os utilizadores podem aceder de forma fácil e segura às aplicações, o seu objetivo é ter um único conjunto de controlos de acesso e políticas em todos os seus locais e ambientes em nuvem.**

[O Azure Ative Directory (Azure AD)](../fundamentals/active-directory-whatis.md) oferece uma plataforma de identidade universal que fornece às suas pessoas, parceiros e clientes uma única identidade para aceder às aplicações que querem e colaborar em qualquer plataforma e dispositivo.

![Um diagrama de conectividade Azure Ative Directory](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

A Azure AD tem um [conjunto completo de capacidades de gestão de identidade.](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad) A normalização da autenticação da sua aplicação e a autorização para a Azure AD permite-lhe obter os benefícios que estas capacidades proporcionam.

Ver recursos migratórios adicionais em [https://aka.ms/migrateapps](./migration-resources.md)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Benefícios da autenticação de aplicações migratórias para a Azure AD

A autenticação da aplicação para a Azure AD irá ajudá-lo a gerir o risco e o custo, aumentar a produtividade e abordar os requisitos de conformidade e governação.

### <a name="manage-risk"></a>Faça a gestão do risco.

A salvaguarda das suas apps requer que tenha uma visão completa de todos os fatores de risco. Migrar as suas aplicações para Azure AD consolida as suas soluções de segurança. Com ele pode:

- Melhorar o acesso seguro dos utilizadores a aplicações e dados corporativos associados utilizando [políticas de acesso condicional,](../conditional-access/overview.md) [autenticação multi-factor](../authentication/concept-mfa-howitworks.md)e tecnologias de [proteção](../identity-protection/overview-identity-protection.md) de identidade baseadas em risco em tempo real.

- Proteja o acesso privilegiado do utilizador ao seu ambiente com acesso a administração [Just-In-Time.](../../azure-resource-manager/managed-applications/request-just-in-time-access.md)

- Use o [design multi-inquilino, geo-distribuído e de alta disponibilidade do AZure AD](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)para as suas necessidades comerciais mais críticas.

- Proteja as suas aplicações antigas com uma das [nossas integrações de parceiros de acesso híbridos seguros](https://aka.ms/secure-hybrid-access) que já possa ter implementado.

### <a name="manage-cost"></a>Gerir o custo

A sua organização pode ter múltiplas soluções de Gestão de Acesso à Identidade (IAM) no local. Migrar para uma infraestrutura AD Azure é uma oportunidade para reduzir as dependências das licenças IAM (no local ou na nuvem) e nos custos de infraestrutura. Nos casos em que já tenha pago a Azure AD através das licenças M365, não há razão para pagar o custo adicional de outra solução IAM.

**Com a Azure AD, pode reduzir os custos de infraestrutura através de:**

- Fornecendo acesso remoto seguro a aplicações no local usando [O Proxy aplicação AD AD Azure.](./application-proxy.md)

- Dissociando as aplicações da abordagem de credencial on-prem no seu [inquilino, criando a Azure AD como o fornecedor de identidade universal confiável](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization).

### <a name="increase-productivity"></a>Aumentar a produtividade

Os benefícios económicos e de segurança levam as organizações a adotar a Azure AD, mas a adoção e a conformidade completas são mais prováveis se os utilizadores também beneficiarem. Com a Azure AD, pode:

- Melhorar a experiência [single Sign-On (SSO)](./what-is-single-sign-on.md) do utilizador final através de um acesso perfeito e seguro a qualquer aplicação, a partir de qualquer dispositivo e qualquer local.

- Alavancar as capacidades IAM de autosserviço, tais como [resets de senha de autosserviço](../authentication/concept-sspr-howitworks.md) e [gestão do grupo SelfService](../enterprise-users/groups-self-service-management.md).

- Reduzir as despesas administrativas gerindo apenas uma única identidade para cada utilizador em ambientes de nuvem e no local:

  - [Automatizar o fornecimento](../app-provisioning/user-provisioning.md) de contas de utilizador (na [Galeria Azure AD)](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)com base em identidades AD Azure
  - Aceda a todas as suas aplicações a partir do painel MyApps no [portal Azure ](https://portal.azure.com/)

- Permitir que os desenvolvedores garantam o acesso às suas apps e melhorem a experiência do utilizador final utilizando a [Plataforma de Identidade](../develop/v2-overview.md) da Microsoft com a Microsoft Authentication Library (MSAL).

- Capacitar os seus parceiros com acesso a recursos na nuvem utilizando [a colaboração Azure AD B2B.](../external-identities/what-is-b2b.md) Isto remove a sobrecarga da configuração ponto-a-ponto da federação com os seus parceiros.

### <a name="address-compliance-and-governance"></a>Abordar a conformidade e a governação

Garantir o cumprimento dos requisitos regulamentares, aplicando políticas de acesso às empresas e monitorizando o acesso dos utilizadores a aplicações e dados associados utilizando ferramentas de auditoria integradas e APIs. Com o Azure AD, pode monitorizar os pedidos de inscrição através de relatórios que aproveitam [as ferramentas de Segurança Incidente e Monitorização de Eventos (SIEM).](../reports-monitoring/plan-monitoring-and-reporting.md) Pode aceder aos relatórios a partir do portal ou APIs e auditar programaticamente quem tem acesso às suas aplicações e remover o acesso a utilizadores inativos através de avaliações de acesso.

## <a name="plan-your-migration-phases-and-project-strategy"></a>Planeie as suas fases de migração e estratégia de projeto

Quando os projetos tecnológicos falham, muitas vezes deve-se a expectativas desajustadas, às partes interessadas certas que não estão envolvidas, ou à falta de comunicação. Garanta o seu sucesso planeando o próprio projeto.

### <a name="the-phases-of-migration"></a>As fases da migração

Antes de entrarmos nas ferramentas, deve entender como pensar através do processo de migração. Através de vários workshops diretos ao cliente, recomendamos as seguintes quatro fases:

![Um diagrama das fases da migração](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>Reúna a equipa do projeto

A migração de aplicações é um esforço de equipa, e você precisa garantir que você tem todas as posições vitais preenchidas. O apoio dos líderes empresariais seniores é importante. Certifique-se de que envolve o conjunto certo de patrocinadores executivos, decisores empresariais e especialistas em assuntos (PME).)

Durante o projeto de migração, uma pessoa pode desempenhar múltiplos papéis, ou várias pessoas cumprem cada papel, dependendo do tamanho e estrutura da sua organização. Você também pode ter uma dependência de outras equipas que desempenham um papel fundamental no seu panorama de segurança.

O quadro seguinte inclui as funções-chave e as suas contribuições:

| Função          | Contributions                                              |
| ------------- | ---------------------------------------------------------- |
| **Gestor de Projeto** | O project coach responsável por orientar o projeto, incluindo:<br /> - ganhar apoio executivo<br /> - trazer as partes interessadas<br /> - gerir horários, documentação e comunicações |
| **Arquiteto de identidade / Administrador de Aplicativo Ad Azure** | São responsáveis pelo seguinte:<br /> - conceber a solução em cooperação com as partes interessadas<br /> - documentar a conceção da solução e os procedimentos operacionais para a entrega à equipa de operações<br /> - gerir os ambientes de pré-produção e de produção |
| **Nas instalações, equipa de operações da AD** | A organização que gere as diferentes fontes de identidade no local, tais como florestas de AD, diretórios LDAP, sistemas de RH, etc.<br /> - executar quaisquer tarefas de reparação necessárias antes de sincronizar<br /> - Fornecer as contas de serviço necessárias para a sincronização<br /> - fornecer acesso à federação de configuração à Azure AD |
| **Gestor de Suporte de TI** | Um representante da organização de suporte de TI que pode fornecer informações sobre a sustentabilidade desta mudança numa perspetiva de helpdesk. |
| **Proprietário de Segurança**  | Um representante da equipa de segurança que pode garantir que o plano irá cumprir os requisitos de segurança da sua organização. |
| **Proprietários técnicos de aplicação** | Inclui proprietários técnicos das aplicações e serviços que se integrarão com a Azure AD. Fornecem os atributos de identidade das aplicações que devem incluir no processo de sincronização. Geralmente têm uma relação com representantes do CSV. |
| **Empresa de aplicação Proprietários** | Colegas representativos que podem fornecer informações sobre a experiência do utilizador e a utilidade desta mudança do ponto de vista de um utilizador e detêm o aspeto de negócio geral da aplicação, que pode incluir a gestão do acesso. |
| **Grupo piloto de utilizadores** | Utilizadores que irão testar como parte do seu trabalho diário, a experiência piloto, e fornecer feedback para orientar o resto das implementações. |

### <a name="plan-communications"></a>Planear as comunicações

O envolvimento e a comunicação dos negócios eficazes são a chave para o sucesso. É importante dar às partes interessadas e aos utilizadores finais uma forma de obter informações e manter-se informados sobre as atualizações dos horários. Educar todos sobre o valor da migração, quais são os prazos esperados, e como planear qualquer perturbação temporária do negócio. Use várias avenidas, tais como sessões de briefing, e-mails, reuniões um-para-um, banners e câmaras municipais.

Com base na estratégia de comunicação que escolheu para a app, talvez queira lembrar aos utilizadores o tempo de inatividade pendente. Deve também verificar se não existem alterações recentes ou impactos empresariais que exijam adiar a implementação.

No quadro seguinte encontrará a comunicação mínima sugerida para manter as suas partes interessadas informadas:

**Fases do plano e estratégia de projeto:**

| Comunicação      | Audiência                                          |
| ------------------ | ------------------------------------------------- |
| Sensibilização e valor empresarial/técnico do projeto | Todos, exceto utilizadores finais |
| Solicitação para aplicações piloto | - Proprietários de negócios de aplicativos<br />- Proprietários técnicos de aplicativos<br />- Equipa de Arquitetos e Identidade |

**Fase 1- Descobrir e Âmbito:**

| Comunicação      | Audiência                                          |
| ------------------ | ------------------------------------------------- |
| - Solicitação para informação sobre aplicações<br />- Resultado do exercício de scoping | - Proprietários técnicos de aplicativos<br />- Proprietários de negócios de aplicativos |

**Fase 2- Classificar as aplicações e planear o piloto:**

| Comunicação      | Audiência                                          |
| ------------------ | ------------------------------------------------- |
| - Resultado das classificações e o que isso significa para o calendário de migração<br />- Calendário preliminar de migração | - Proprietários técnicos de aplicativos<br /> - Proprietários de negócios de aplicativos |

**Fase 3 - Planear a migração e os testes:**

| Comunicação      | Audiência                                          |
| ------------------ | ------------------------------------------------- |
| - Resultado dos testes de migração de aplicações | - Proprietários técnicos de aplicativos<br />- Proprietários de negócios de aplicativos |
| - Notificação de que a migração está a chegar e explicação das experiências resultantes do utilizador final.<br />- Tempo de inatividade e comunicações completas, incluindo o que devem fazer agora, feedback e como obter ajuda | - Utilizadores finais (e todos os outros) |

**Fase 4 - Gerir e obter insights:**

| Comunicação      | Audiência                                          |
| ------------------ | ------------------------------------------------- |
| Análise disponível e como aceder | - Proprietários técnicos de aplicativos<br />- Proprietários de negócios de aplicativos |

### <a name="migration-states-communication-dashboard"></a>Painel de comunicação dos estados de migração

Comunicar o estado geral do projeto de migração é crucial, pois mostra progresso, e ajuda os proprietários de aplicações cujas apps estão a chegar para a migração para se prepararem para a mudança. Pode montar um painel simples utilizando o Power BI ou outras ferramentas de reporte para dar visibilidade ao estado das aplicações durante a migração.

Os estados de migração que pode considerar usar são os seguintes:

| Estados migratórios       | Plano de ação                                   |
| ---------------------- | --------------------------------------------- |
| **Pedido Inicial** | Encontre a app e contacte o proprietário para mais informações |
| **Avaliação Completa** | O dono da app avalia os requisitos da aplicação e devolve o questionário da aplicação</td>
| **Configuração em Progresso** | Desenvolver as alterações necessárias para gerir a autenticação contra a Azure AD |
| **Configuração de teste bem sucedida** | Avalie as alterações e autente a app contra o inquilino Azure AD de teste no ambiente de teste |
| **Configuração de produção bem sucedida** | Alterar as configurações para trabalhar contra o inquilino de produção AD e avaliar a autenticação da app no ambiente de teste |
| **Complete / Assinar Fora** | Implementar as alterações para a app para o ambiente de produção e executar o contra a produção Azure AD inquilino |

Isto irá garantir que os proprietários de aplicações saibam qual é a migração e o calendário de testes da aplicação quando as suas apps estão em migração, e quais os resultados de outras apps que já foram migradas. Também pode considerar fornecer links para a sua base de dados de rastreadores de erros para que os proprietários possam arquivar e ver problemas para apps que estão a ser migradas.

### <a name="best-practices"></a>Melhores práticas

Seguem-se as histórias de sucesso do nosso cliente e parceiro, e sugeridas as melhores práticas:

- [Cinco dicas para melhorar o processo de migração para o Azure Ative Directory](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364) da Patriot Consulting, um membro da nossa rede de parceiros que se foca em ajudar os clientes a implementar soluções de nuvem microsoft de forma segura.

- [Desenvolva uma estratégia de gestão de risco para a migração da sua aplicação Azure por](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488) Edgile, um parceiro que se foca em soluções de IAM e gestão de riscos.

## <a name="phase-1-discover-and-scope-apps"></a>Fase 1: Descubra e alcance apps

**A descoberta e análise de aplicações é um exercício fundamental para lhe dar um bom começo.** Pode não saber de tudo, por isso esteja preparado para acomodar as aplicações desconhecidas.

### <a name="find-your-apps"></a>Encontre as suas apps

O primeiro ponto de decisão numa migração de aplicações é quais as aplicações para migrar, que se algumas devem permanecer, e quais as aplicações para depreciar. Há sempre a oportunidade de depreciar as aplicações que não irá utilizar na sua organização. Existem várias formas de encontrar aplicativos na sua organização. **Ao descobrir apps, certifique-se de que está a incluir aplicações em desenvolvimento e planeadas. Use a Azure AD para autenticação em todas as aplicações futuras.**

**Utilização de Serviços da Federação de Diretórios Ativos (AD FS) para recolher um inventário de aplicações correto:**

- **Utilizar o Azure AD Connect Health.** Se tiver uma licença Azure AD Premium, recomendamos a implementação do [Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md) para analisar o uso da aplicação no seu ambiente no local. Pode utilizar o relatório de candidatura da [ADFS](./migrate-adfs-application-activity.md) (pré-visualização) para descobrir aplicações ADFS que podem ser migradas e avaliar a prontidão da aplicação a migrar. Depois de completar a sua migração, implemente [o Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) que lhe permite monitorizar continuamente o Shadow IT na sua organização assim que estiver na nuvem.

- **Análise de registo de AD FS**. Se não tiver licenças Azure AD Premium, recomendamos a utilização das ferramentas de migração de aplicações ADFS para Azure com base no [PowerShell.](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration) Consulte o [guia de solução:](./migrate-adfs-apps-to-azure.md)

[Aplicativos migratórios dos Serviços da Federação de Diretórios Ativos (AD FS) para Azure AD.](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>Utilizando outros fornecedores de identidade (IdPs)

Para outros fornecedores de identidade (como Okta ou Ping), pode utilizar as suas ferramentas para exportar o inventário de aplicações. Pode considerar os princípios de serviço registados no Ative Directory que correspondem às aplicações web da sua organização.

### <a name="using-cloud-discovery-tools"></a>Usando ferramentas de descoberta de nuvem

No ambiente em nuvem, você precisa de visibilidade rica, controle sobre viagens de dados, e análise sofisticada para encontrar e combater ameaças cibernéticas em todos os seus serviços na nuvem. Pode recolher o seu inventário de aplicativos em nuvem utilizando as seguintes ferramentas:

- **Cloud Access Security Broker (CASB)**– Um [CASB](/cloud-app-security/) normalmente funciona ao lado da sua firewall para fornecer visibilidade no uso da aplicação em nuvem dos seus colaboradores e ajuda-o a proteger os seus dados corporativos de ameaças à cibersegurança. O relatório CASB pode ajudá-lo a determinar as aplicações mais usadas na sua organização, e os primeiros alvos para migrar para Azure AD.

- **Cloud Discovery** - Ao configurar [o Cloud Discovery,](/cloud-app-security/set-up-cloud-discovery)ganha visibilidade no uso da aplicação em nuvem, e pode descobrir aplicações não higides ou Shadow IT.

- **APIs** - Para aplicações ligadas à infraestrutura em nuvem, pode utilizar as APIs e ferramentas nesses sistemas para começar a fazer um inventário de aplicações hospedadas. No ambiente Azure:

  - Use o [cmdlet Get-AzureWebsite](/powershell/module/servicemanagement/azure/get-azurewebsite?view=azuresmps-4.0.0&redirectedfrom=MSDN&preserve-view=true)para obter informações sobre os websites da Azure.

  - Utilize o [cmdlet Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.2.0&preserve-view=true)para obter informações sobre as suas Aplicações Web Azure.

  - Pode encontrar todas as aplicações em execução no Microsoft IIS a partir da linha de comando do Windows utilizando [AppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools).

  - Utilize [aplicações](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) e [diretores de serviço](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) para obter informações sobre uma aplicação e instância de aplicações num diretório em Azure AD.

### <a name="using-manual-processes"></a>Utilização de processos manuais

Depois de ter tomado as abordagens automatizadas acima descritas, terá uma boa pega nas suas aplicações. No entanto, poderá considerar fazer o seguinte para garantir uma boa cobertura em todas as áreas de acesso ao utilizador:

- Contacte os vários empresários da sua organização para encontrar as aplicações em uso na sua organização.

- Executar uma ferramenta de inspeção HTTP no seu servidor proxy, ou analisar registos de procuração, para ver onde o tráfego é normalmente encaminhado.

- Reveja os blogs dos sites populares do portal da empresa para ver quais as ligações que os utilizadores mais acedem.

- Contacte executivos ou outros membros do negócio chave para garantir que cobriu as aplicações críticas ao negócio.

### <a name="type-of-apps-to-migrate"></a>Tipo de aplicativos para migrar

Assim que encontrar as suas aplicações, irá identificar este tipo de aplicações na sua organização:

- Aplicativos que já usam protocolos de autenticação modernos

- Aplicativos que usam protocolos de autenticação legado que escolhe para modernizar

- Aplicativos que usam protocolos de autenticação legado que escolhe não para modernizar

- Novas aplicações da Linha de Negócios (LoB)

### <a name="apps-that-use-modern-authentication-already"></a>Aplicativos que já usam a autenticação moderna

As aplicações já modernizadas são as mais prováveis de serem transferidas para a Azure AD. Estas aplicações já utilizam protocolos de autenticação modernos (como SAML ou OpenID Connect) e podem ser reconfiguradas para autenticar com Azure AD.

Além das escolhas na galeria de [aplicações AD AZure,](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) estas podem ser aplicações que já existem na sua organização ou em quaisquer aplicações de terceiros de um fornecedor que não faça parte da galeria AD AZure[(aplicações não-galeria)](./add-application-portal.md).

Aplicativos legados que escolhes modernizar

Para aplicações antigas que pretende modernizar, mudar-se para Azure AD para autenticação e autorização de núcleo desbloqueia toda a riqueza de energia e dados que o [Gráfico](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) microsoft e [o Gráfico de Segurança Inteligente](https://www.microsoft.com/security/operations/intelligence?rtc=1) têm para oferecer.

Recomendamos **a atualização do código da pilha** de autenticação para estas aplicações a partir do protocolo legado (como autenticação integrada do Windows, delegação constrangida kerberos, autenticação baseada em cabeçalhos HTTP) para um protocolo moderno (como SAML ou OpenID Connect).

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>Aplicativos legados que escolhes NÃO modernizar

Para certas aplicações que utilizam protocolos de autenticação legado, por vezes modernizar a sua autenticação não é a coisa certa a fazer por razões comerciais. Estes incluem os seguintes tipos de aplicações:

- Aplicações mantidas no local por razões de conformidade ou controlo.

- Aplicações ligadas a uma identidade no local ou provedor de federação que não quer alterar.

- Aplicações desenvolvidas usando padrões de autenticação no local que você não tem planos para mover

O Azure AD pode trazer grandes benefícios para estas aplicações antigas, pois pode ativar [funcionalidades modernas](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review) de segurança e governação da AD Azure, como [a Autenticação Multi-Factor,](../authentication/concept-mfa-howitworks.md) [Acesso Condicional,](../conditional-access/overview.md) [Proteção](../identity-protection/index.yml)de Identidade, [Acesso delegado de Aplicações](./access-panel-manage-self-service-access.md)e Comentários de Acesso contra estas aplicações sem tocar na app!

Comece por **estender estas aplicações para a nuvem** com o Azure AD Application [Proxy Usando](./application-proxy-configure-single-sign-on-password-vaulting.md) meios simples de autenticação (como o Cofre de Palavras-Passe) para fazer com que os seus utilizadores migram rapidamente, ou através de integrações dos [nossos parceiros](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/) com controladores de entrega de aplicações que já possa ter implementado.

### <a name="new-line-of-business-lob-apps"></a>Novas aplicações da Linha de Negócios (LoB)

Normalmente desenvolve aplicativos LoB para uso interno da sua organização. Se tiver novas aplicações no oleoduto, recomendamos que utilize a [Plataforma de Identidade](../develop/v2-overview.md) da Microsoft para implementar o OpenID Connect.

### <a name="apps-to-deprecate"></a>Aplicativos para depreciar

Aplicações sem proprietários claros e manutenção e monitorização claras apresentam um risco de segurança para a sua organização. Considere depreciar as aplicações quando:

- a sua **funcionalidade é altamente redundante** com outros sistemas • não há **nenhum empresário**

- não há claramente **uso**.

É claro que **não depreciar aplicações de alto impacto, críticas ao negócio.** Nesses casos, trabalhe com empresários para determinar a estratégia certa.

### <a name="exit-criteria"></a>Critérios de saída

Você tem sucesso nesta fase com:

- Uma boa compreensão dos sistemas em questão para a sua migração (que pode reformar-se depois de se mudar para Azure AD)

- Uma lista de aplicações que inclui:

  - Que sistemas essas aplicações se ligam a o A partir de onde e em que dispositivos os utilizadores acedem às suas

  - Se serão migrados, precotados ou ligados ao [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md).

> [!NOTE]
> Pode descarregar a [Tabela de Descobertas](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) de Aplicações para registar as aplicações que pretende migrar para a autenticação AD Azure, e aquelas que pretende sair, mas que gere utilizando o [Azure AD Connect.](../hybrid/whatis-azure-ad-connect.md)

## <a name="phase-2-classify-apps-and-plan-pilot"></a>Fase 2: Classificar apps e planear piloto

Classificar a migração das suas apps é um exercício importante. Nem todas as aplicações precisam de ser migradas e transitadas ao mesmo tempo. Uma vez recolhidas informações sobre cada uma das aplicações, pode racionalizar quais as aplicações que devem ser migradas primeiro e que podem demorar mais tempo.

### <a name="classify-in-scope-apps"></a>Classificar aplicações em âmbito

Uma maneira de pensar sobre isso é ao longo dos eixos da criticalidade, uso e tempo de vida do negócio, cada um dos quais depende de múltiplos fatores.

### <a name="business-criticality"></a>Importância crítica para a empresa

A criticidade do negócio assumirá diferentes dimensões para cada negócio, mas as duas medidas que deve considerar são **funcionalidades e funcionalidades** e **perfis de utilizador.** Atribua às aplicações com uma funcionalidade única um valor de ponto mais elevado do que aqueles com funcionalidades redundantes ou obsoletas.

![Um diagrama dos espectros de funcionalidades & funcionalidade e perfis de utilizador](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>Utilização

As aplicações com **elevados números de utilização** devem receber um valor superior ao das aplicações com baixa utilização. Atribua um valor mais elevado a apps com utilizadores externos, executivos ou de equipa de segurança. Para cada app no seu portfólio de migração, complete estas avaliações.

![Um diagrama dos espectros do volume de utilizador e da amplitude do utilizador](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

Uma vez determinados valores para a criticidade e utilização do negócio, pode então determinar o tempo de vida útil da **aplicação** e criar uma matriz de prioridade. Veja uma dessas matrizes abaixo:

![Um diagrama de triângulo que mostra as relações entre o uso, o tempo de vida esperado e a criticalidade empresarial](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>Priorizar aplicativos para migração

Pode optar por iniciar a migração da aplicação com as aplicações de menor prioridade ou com as aplicações de maior prioridade baseadas nas necessidades da sua organização.

Num cenário em que poderá não ter experiência em utilizar os serviços Azure AD e Identity, considere primeiro transferir as suas **aplicações de menor prioridade** para a Azure AD. Isto irá minimizar o impacto do seu negócio, e você pode construir impulso. Depois de ter mudado estas aplicações com sucesso e ter ganho a confiança das partes interessadas, pode continuar a migrar as outras apps.

Se não houver uma prioridade clara, deve considerar a possibilidade de mover as aplicações que estão na [Galeria AD Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) em primeiro lugar e apoiar vários fornecedores de identidade (ADFS ou Okta) porque são mais fáceis de integrar. É provável que estas aplicações sejam as **aplicações mais prioritárias** da sua organização. Para ajudar a integrar as suas aplicações SaaS com a Azure AD, desenvolvemos uma coleção de [tutoriais](../saas-apps/tutorial-list.md) que o acompanham através da configuração.

Quando tiver um prazo para migrar as apps, estas aplicações de maior prioridade terão a maior carga de trabalho. Pode eventualmente selecionar as aplicações de menor prioridade, uma vez que não alteram o custo, mesmo que tenha alterado o prazo. Mesmo que tenha que renovar a licença, será por uma pequena quantia.

Além desta classificação e dependendo da urgência da sua migração, poderá também considerar a criação de um **calendário de migração** dentro do qual os proprietários de aplicações devem envolver-se para que as suas apps sejam migradas. No final deste processo, deverá ter uma lista de todas as aplicações em baldes prioritários para migração.

### <a name="document-your-apps"></a>Documente as suas apps

Primeiro, comece por recolher detalhes importantes sobre as suas aplicações. A [Folha de Descoberta de Aplicações](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)irá ajudá-lo a tomar as suas decisões de migração rapidamente e obter uma recomendação para o seu grupo empresarial em pouco tempo.

A informação que é importante para tomar a sua decisão de migração inclui:

- **Nome de app** – o que é esta app conhecida como para o negócio?

- **Tipo de aplicativo** – é uma aplicação SaaS de 3ª parte? Uma linha personalizada de aplicações web de negócios? Uma API?

- **Criticidade do negócio** – é a sua elevada criticidade? Baixo? Ou algures no meio?

- **Volume de acesso ao utilizador** – todos acedem a esta aplicação ou apenas a algumas pessoas?

- **Tempo de vida planeado** – quanto tempo vai estar esta app? Menos de 6 meses? Mais de 2 anos?

- **Fornecedor de identidade atual** – qual é o IdP primário para esta app? Ou depende do armazenamento local?

- **Método de autenticação** – a aplicação autentica-se utilizando padrões abertos?

- **Se planeia atualizar o código da aplicação** – a app está planeada ou em desenvolvimento ativo?

- **Se planeia manter a aplicação no local** – pretende manter a aplicação no seu datacenter a longo prazo?

- **Se a aplicação depende de outras apps ou APIs** – a app atualmente chama para outras apps ou APIs?

- **Se a aplicação está na galeria AD AD –** a aplicação já está integrada na [Galeria AD AZure](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)?

Outros dados que o ajudarão mais tarde, mas que não precisa de tomar uma decisão de migração imediata inclui:

- **URL da app** – onde é que os utilizadores vão aceder à aplicação?

- **Descrição da aplicação** – o que é uma breve descrição do que a aplicação faz?

- **Dono de app** – quem no negócio é o principal POC para a app?

- **Comentários gerais ou notas** – qualquer outra informação geral sobre a app ou propriedade do negócio

Uma vez classificado a sua aplicação e documentou os detalhes, então certifique-se de ganhar a compra do proprietário do negócio para a sua estratégia de migração planeada.

### <a name="plan-a-pilot"></a>Planeie um piloto

As aplicações que seleciona para o piloto devem representar os principais requisitos de identidade e segurança da sua organização, e deve ter uma entrada clara dos proprietários da aplicação. Os pilotos normalmente funcionam num ambiente de teste separado. Consulte [as melhores práticas para pilotos](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) na página de planos de implantação.

**Não se esqueça dos seus parceiros externos.** Certifique-se de que participam em horários de migração e testes. Finalmente, certifique-se de que eles têm uma maneira de aceder ao seu helpdesk em caso de quebra de problemas.

### <a name="plan-for-limitations"></a>Plano de limitações

Embora algumas aplicações sejam fáceis de migrar, outras podem demorar mais tempo devido a vários servidores ou instâncias. Por exemplo, a migração do SharePoint pode demorar mais tempo devido ao sinal personalizado nas páginas.

Muitos fornecedores de aplicações SaaS cobram por alterar a ligação SSO. Verifique com eles e planeie isto.

A Azure AD também tem limites de [serviço e restrições](../enterprise-users/directory-service-limits-restrictions.md) que deve estar ciente.

### <a name="app-owner-sign-off"></a>Assinatura do proprietário da app

Aplicações críticas e universalmente utilizadas podem necessitar de um grupo de utilizadores piloto para testar a aplicação na fase piloto. Depois de ter testado uma aplicação no ambiente pré-produção ou piloto, certifique-se de que os donos de negócios de aplicações assinam o desempenho antes da migração da app e de todos os utilizadores para a utilização da Azure AD para autenticação.

### <a name="plan-the-security-posture"></a>Planeie a postura de segurança

Antes de iniciar o processo de migração, tenha tempo para considerar plenamente a postura de segurança que deseja desenvolver para o seu sistema de identidade corporativa. Isto baseia-se na recolha destes valiosos conjuntos de informações: **identidades e dados, que estão a aceder aos seus dados, dispositivos e locais.**

### <a name="identities-and-data"></a>Identidades e dados

A maioria das organizações tem requisitos específicos sobre identidades e proteção de dados que variam de acordo com o segmento da indústria e por funções de trabalho dentro das organizações. Consulte [as configurações de acesso](/microsoft-365/enterprise/microsoft-365-policies-configurations) à identidade e ao dispositivo para as nossas recomendações, incluindo um conjunto prescrito de políticas de [acesso condicional](../conditional-access/overview.md) e capacidades relacionadas.

Pode utilizar esta informação para proteger o acesso a todos os serviços integrados com a Azure AD. Estas recomendações estão alinhadas com o Microsoft Secure Score, bem como com a [pontuação de identidade no Azure AD](../fundamentals/identity-secure-score.md). A classificação ajuda a:

- Medir objetivamente a sua postura de segurança de identidade

- Planear melhorias de segurança de identidade

- Analisar o sucesso das suas melhorias

Isto também o ajudará a implementar os [cinco passos para garantir a sua infraestrutura de identidade.](../../security/fundamentals/steps-secure-identity.md) Use a orientação como ponto de partida para a sua organização e ajuste as políticas para satisfazer os requisitos específicos da sua organização.

### <a name="who-is-accessing-your-data"></a>Quem está a aceder aos seus dados?

Existem duas categorias principais de utilizadores das suas apps e recursos que a Azure AD suporta:

- **Interno:** Colaboradores, empreiteiros e fornecedores que tenham contas dentro do seu fornecedor de identidade. Isto pode precisar de mais pivôs com regras diferentes para gestores ou lideranças contra outros funcionários.

- **Externo:** Fornecedores, fornecedores, distribuidores ou outros parceiros de negócio que interagem com a sua organização no curso regular de negócios com a [colaboração Azure AD B2B.](../external-identities/what-is-b2b.md)

Pode definir grupos para estes utilizadores e povoar estes grupos de diversas formas. Pode escolher que um administrador deve adicionar manualmente membros a um grupo, ou pode ativar a adesão ao grupo selfservice. Podem ser estabelecidas regras que adicionem automaticamente os membros em grupos com base nos critérios especificados utilizando [grupos dinâmicos](../enterprise-users/groups-dynamic-membership.md).

Os utilizadores externos podem também consultar os clientes que requerem uma consideração especial. [Azure AD B2C,](../../active-directory-b2c/overview.md)um produto separado suporta a autenticação do cliente. No entanto, está fora do âmbito deste trabalho.

### <a name="devicelocation-used-to-access-data"></a>Dispositivo/localização utilizado para aceder aos dados

O dispositivo e a localização que um utilizador utiliza para aceder a uma aplicação também são importantes. Os dispositivos fisicamente ligados à sua rede corporativa são mais seguros. As ligações de fora da rede sobre a VPN podem necessitar de escrutínio.

![Um diagrama que mostra a relação entre a localização do utilizador e o acesso aos dados](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

Tendo em mente estes aspetos de recursos, utilizador e dispositivo, poderá optar por utilizar as capacidades de [Acesso Condicionado AD Azure.](../conditional-access/overview.md) O acesso condicional vai além das permissões do utilizador: baseia-se numa combinação de fatores, como a identidade de um utilizador ou grupo, a rede a que o utilizador está ligado, o dispositivo e a aplicação que estão a utilizar, e o tipo de dados a que estão a tentar aceder. O acesso concedido ao utilizador adapta-se a este conjunto mais alargado de condições.

### <a name="exit-criteria"></a>Critérios de saída

Tem sucesso nesta fase quando:

- Conheça as suas apps
  - Documente totalmente as aplicações que pretende migrar
  - Priorizaram aplicações baseadas na criticidade do negócio, no volume de utilização e no tempo de vida

- Selecione aplicativos que representam os seus requisitos para um piloto

- Compra do empresário à sua priorização e estratégia

- Compreenda as suas necessidades de postura de segurança e como implementá-las

## <a name="phase-3-plan-migration-and-testing"></a>Fase 3: Planear a migração e os testes

Depois de ter adquirido a compra de negócios, o próximo passo é começar a migrar estas aplicações para a autenticação AZure AD.

### <a name="migration-tools-and-guidance"></a>Ferramentas e orientações de migração

Utilize as ferramentas e orientações abaixo para seguir os passos precisos necessários para migrar as suas aplicações para Azure AD:

- **Orientação geral de migração** – Use o papel branco, ferramentas, modelos de e-mail e questionário de aplicações no [kit de ferramentas de migração de aplicações AD Azure](./migration-resources.md) para descobrir, classificar e migrar as suas aplicações.

- **Aplicações SaaS** – Veja a nossa lista de [centenas de tutoriais de aplicações saaS](../saas-apps/tutorial-list.md) e o plano completo de [implementação do Azure AD SSO](https://aka.ms/ssodeploymentplan) para percorrer o processo de ponta a ponta.

- **Aplicações em execução no local** – Saiba tudo [sobre o Proxy aplicação AD AZure](./application-proxy.md) e use o plano completo de implementação de [aplicação AD AD](https://aka.ms/AppProxyDPDownload) para começar rapidamente.

- **Apps que está a desenvolver** – Leia passo a passo a [orientação](../develop/quickstart-register-app.md) de integração e [registo.](../develop/quickstart-register-app.md)

Após a migração, pode optar por enviar comunicações informando os utilizadores da implementação bem sucedida e lembrá-los de quaisquer novos passos que precisem de tomar.

### <a name="plan-testing"></a>Testes de plano

Durante o processo de migração, a sua aplicação pode já ter um ambiente de teste utilizado durante as implementações regulares. Pode continuar a usar este ambiente para testes de migração. Se um ambiente de teste não estiver disponível atualmente, poderá ser capaz de configurar um utilizando o Azure App Service ou a Azure Virtual Machines, dependendo da arquitetura da aplicação. Pode optar por configurar um inquilino Azure AD de teste separado para usar à medida que desenvolve as configurações da sua aplicação. Este inquilino começará em estado limpo e não será configurado para sincronizar com qualquer sistema.

Pode testar cada aplicação iniciando sessão com um utilizador de teste e certificar-se de que todas as funcionalidades são as mesmas que antes da migração. Se determinar durante o teste que os utilizadores terão de atualizar as suas definições [de MFA](/active-directory/authentication/howto-mfa-userstates) ou [SSPR,](../authentication/tutorial-enable-sspr.md)ou se estiver a adicionar esta funcionalidade durante a migração, certifique-se de que o adicionará ao seu plano de comunicação do utilizador final. Consulte os modelos de comunicação do utilizador final [MFA](https://aka.ms/mfatemplates) e [SSPR.](https://aka.ms/ssprtemplates)

Depois de migrar as aplicações, vá ao [Portal Azure](https://aad.portal.azure.com/) para testar se a migração foi um sucesso. Siga as instruções abaixo:

- Selecione **Aplicações empresariais &gt; Todas as aplicações** e encontre a sua aplicação na lista.

- **Selecione Gerir &gt; Utilizadores e grupos** para atribuir pelo menos um utilizador ou grupo à aplicação.

- **Selecione Gerir o Acesso &gt; Condicional**. Reveja a sua lista de políticas e certifique-se de que não está a bloquear o acesso à aplicação com uma [política de acesso condicional.](../conditional-access/overview.md)

Dependendo da configuração da sua aplicação, verifique se o SSO funciona corretamente.

| Tipo de autenticação      | Testar                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth / OpenID Connect** | Selecione **&gt; permissões de aplicações da Empresa** e certifique-se de que consentiu na aplicação a ser usada na sua organização nas definições do utilizador para a sua aplicação. |
| **SAML-based SSO (SSO baseado no SAML)** | Utilize o botão [de definições DE TESTE SAML](./debug-saml-sso-issues.md) encontrado sob **o sign-on único.** |
| **SSO baseado em palavra-passe** | Descarregue e instale a [extensão de insífio Do MyApps.](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) Esta extensão ajuda-o a iniciar qualquer uma das aplicações em nuvem da sua organização que o exijam a utilizar um processo SSO. |

| **[| de procuração de aplicação](./application-proxy.md)** Certifique-se de que o seu conector está em funcionamento e atribuído à sua aplicação. Visite o [guia de resolução de problemas da Aplicação Proxy](./application-proxy-troubleshoot.md) para obter mais assistência. |

### <a name="troubleshoot"></a>Resolução de problemas

Se tiver problemas, consulte o [guia de resolução de problemas das nossas apps](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md) para obter ajuda. Consulte também [problemas de inscrição numa aplicação desenvolvida sob medida.](./application-sign-in-problem-federated-sso-gallery.md)

### <a name="plan-rollback"></a>Plano de reversão

Se a sua migração falhar, a melhor estratégia é reverter e testar. Aqui estão os passos que pode tomar para mitigar as questões migratórias:

- **Tire as imagens** da configuração existente da sua aplicação. Pode olhar para trás se tiver de reconfigurar a aplicação mais uma vez.

- Pode também considerar **fornecer links para a autenticação do legado,** em caso de problemas com autenticação em nuvem.

- Antes de concluir a sua migração, **não altere a configuração existente** com o fornecedor de identidade anterior.

- Comece por migrar **as aplicações que suportam vários IDPs**. Se algo correr mal, pode sempre mudar para a configuração do IdP preferido.

- Certifique-se de que a sua experiência de aplicação tem um **botão de Feedback** ou ponteiros para o seu **helpdesk** em caso de problemas.

### <a name="exit-criteria"></a>Critérios de saída

Você tem sucesso nesta fase quando tem:

- Determinado como cada app será migrada

- Revendo as ferramentas de migração

- Planeou o seu teste, incluindo ambientes de teste e grupos

- Reversão planeada

## <a name="phase-4-plan-management-and-insights"></a>Fase 4: Gestão de planos e insights

Uma vez que as aplicações são migradas, você deve garantir que:

- Os utilizadores podem aceder e gerir de forma segura

- Você pode obter os insights apropriados sobre o uso e saúde de aplicativos

Recomendamos que tome as seguintes ações adequadas à sua organização.

### <a name="manage-your-users-app-access"></a>Gerir o acesso à aplicação dos seus utilizadores

Uma vez migradas as aplicações, pode enriquecer a experiência do seu utilizador de várias maneiras

**Tornar as aplicações detetáveis**

**Aponte o seu utilizador** para a experiência do portal [MyApps.](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) Aqui, podem aceder a todas as aplicações baseadas na nuvem, aplicações que disponibiliza usando [o Azure AD Connect](../hybrid/whatis-azure-ad-connect.md), e aplicações que usam [o Application Proxy](./application-proxy.md) desde que tenham permissões para aceder a essas aplicações.


Pode orientar os seus utilizadores sobre como descobrir as suas aplicações:

- Utilize a funcionalidade [de sinalização única existente](./view-applications-portal.md) para ligar os seus **utilizadores a qualquer aplicação**


- Ativar o acesso à [aplicação self-service](./manage-self-service-access.md)a uma aplicação e **deixar que os utilizadores adicionem aplicações que cura**

- [Ocultar aplicações de utilizadores finais](./hide-application-from-user-portal.md) (aplicações padrão da Microsoft ou outras apps) para **tornar as aplicações que precisam de mais descobertas**

### <a name="make-apps-accessible"></a>Tornar as aplicações acessíveis

**Deixe os utilizadores acederem a aplicações a partir dos seus dispositivos móveis.** Os utilizadores podem aceder ao portal MyApps com navegador gerido pelo Intune nos seus dispositivos [iOS](./hide-application-from-user-portal.md) 7.0 ou posteriores ou [Android.](./hide-application-from-user-portal.md)

Os utilizadores podem descarregar um **navegador gerido pelo Intune:**

- **Para dispositivos Android**, da [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser)

- **Para dispositivos Apple**, a partir da [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) ou podem descarregar a [aplicação móvel My Apps para iOS](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653)

**Deixe que os utilizadores abram as suas aplicações a partir de uma extensão do navegador.**

Os utilizadores podem [descarregar a extensão de entrada de sign-in secure myApps](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) no [Chrome,](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) [FireFox](https://addons.mozilla.org/firefox/addon/access-panel-extension/) ou [Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) e podem lançar aplicações a partir da barra de navegador para:

- **Procurem as suas apps e apareçam as suas aplicações mais recentes**

- **Converta automaticamente urls internos** que configuraste no [Application Proxy](./application-proxy.md) para os URLs externos apropriados. Os seus utilizadores podem agora trabalhar com os links que estão familiarizados, independentemente de onde estejam.

**Deixe que os utilizadores abram as suas apps a partir de Office.com.**

Os utilizadores podem ir a [Office.com](https://www.office.com/) **para procurar as suas apps e fazer com que as suas aplicações mais usadas apareçam** para elas desde onde trabalham.

### <a name="secure-app-access"></a>Acesso seguro a aplicativos

O Azure AD fornece um local de acesso centralizado para gerir as suas aplicações migratórias. Vá ao [portal Azure](https://portal.azure.com/) e ative as seguintes capacidades:

- **Garantir o acesso do utilizador a aplicações.** Ativar [as políticas de Acesso Condicional](../conditional-access/overview.md)ou [Proteção de Identidade](../identity-protection/overview-identity-protection.md)para garantir o acesso do utilizador a aplicações com base no estado do dispositivo, localização e muito mais.

- **Provisão automática.** Crie o [fornecimento automático de utilizadores](../app-provisioning/user-provisioning.md) com uma variedade de aplicações SaaS de terceiros a que os utilizadores precisam de aceder. Além de criar identidades de utilizador, inclui a manutenção e remoção das identidades dos utilizadores como alteração de estado ou de funções.

- **Delegar** **gestão de** acesso ao utilizador. Conforme apropriado, permita o acesso de aplicações self-service às suas apps e *designe um aprovador de negócio para aprovar o acesso a essas aplicações.* Utilize [a Self-Service Group Management](../enterprise-users/groups-self-service-management.md)para grupos designados para coleções de apps.

- **Delegado de acesso a administrador.** utilizando **o Diretório Fun** para atribuir uma função de administração (como administrador de aplicação, administrador de aplicação cloud ou desenvolvedor de aplicações) ao seu utilizador.

### <a name="audit-and-gain-insights-of-your-apps"></a>Auditar e obter informações sobre as suas apps

Também pode utilizar o [portal Azure](https://portal.azure.com/) para auditar todas as suas aplicações a partir de um local centralizado,

- **Audite a sua aplicação** utilizando **aplicações empresariais, auditoria** ou aceda às mesmas informações da [Azure AD Reporting API](../reports-monitoring/concept-reporting-api.md) para integrar nas suas ferramentas favoritas.

- **Ver as permissões para uma aplicação** utilizando **aplicações da Empresa, permissões** para aplicações que utilizem OAuth / OpenID Connect.

- **Obtenha informações de inscrição** utilizando **aplicações da empresa, iniciar sê-ins**. Aceda à mesma informação da [Azure AD Reporting API.](../reports-monitoring/concept-reporting-api.md)

- **Visualize o uso da sua aplicação** a partir do pacote de [conteúdos Azure AD PowerBI](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

### <a name="exit-criteria"></a>Critérios de saída

Tem sucesso nesta fase quando:

- Fornecer acesso seguro a aplicações aos seus utilizadores

- Conseguir auditar e obter informações sobre as aplicações migradas

### <a name="do-even-more-with-deployment-plans"></a>Faça ainda mais com planos de implantação

Os planos de implementação percorrem o valor do negócio, o planeamento, as etapas de implementação e a gestão de soluções Azure AD, incluindo cenários de migração de aplicações. Eles reúnem tudo o que precisas para começar a implantar e obter valor a partir das capacidades AD do Azure. Os guias de implementação incluem conteúdo como as melhores práticas recomendadas pela Microsoft, comunicações de utilizador final, guias de planeamento, etapas de implementação, casos de teste e muito mais.

Muitos [planos de implantação](../fundamentals/active-directory-deployment-plans.md) estão disponíveis para o seu uso, e estamos sempre a fazer mais!

### <a name="contact-support"></a>Contactar o suporte

Visite os seguintes links de suporte para criar ou rastrear bilhetes de apoio e monitorizar a saúde.

- **Suporte Azure:** Você pode ligar para [o Microsoft Support](https://azure.microsoft.com/support) e abrir um bilhete para qualquer Azure

Problema de implementação de identidade dependendo do seu Acordo de Empresa com a Microsoft.

- **FastTrack**: Se adquiriu licenças de Mobilidade e Segurança Empresarial (EMS) ou Azure AD Premium, pode receber assistência de implantação do [programa FastTrack.](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program)

- **Envolva a equipa de Engenharia de Produtos:** Se estiver a trabalhar numa grande implementação de clientes com milhões de utilizadores, tem o direito de apoiar a equipa de conta da Microsoft ou o seu Cloud Solutions Architect. Com base na complexidade de implantação do projeto, pode trabalhar diretamente com a [equipa de Engenharia de Produtos de Identidade da Azure.](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)

- **Blog Azure AD Identity:** Subscreva o [blog Azure AD Identity](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity) para se manter atualizado com todos os anúncios de produtos mais recentes, mergulhos profundos e informações de roteiro fornecidas diretamente pela equipa de engenharia de identidade.
