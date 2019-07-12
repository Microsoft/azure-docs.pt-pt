---
title: Novidades Notas - Azure Active Directory de versão | Documentos da Microsoft
description: Saiba o que há de novo no Azure Active Directory, como as notas de versão mais recente, problemas, correções de erros conhecidos de funcionalidade preterida e futuras alterações.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: af215c80148010d526c951e7a5128d6e4622b1cd
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625574"
---
# <a name="whats-new-in-azure-active-directory"></a>O que há de novo no Azure Active Directory?

>Ser notificado sobre quando rever esta página para obter atualizações ao copiar e colar este URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` em seu ![ícone de leitor de feed de RSS](./media/whats-new/feed-icon-16x16.png) feed leitor.

O Azure AD recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- Versões mais recentes
- Problemas conhecidos
- Correções de erros
- Funcionalidades preteridas
- Planos para que as alterações

Esta página é atualizada mensalmente, por isso, revisitá-lo regularmente. Se estiver procurando por itens que têm mais de seis meses, pode encontrá-los no [arquivada para o que há de novo no Azure Active Directory](whats-new-archive.md).

---

## <a name="june-2019"></a>Junho de 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>RiskDetections nova API para o Microsoft Graph (pré-visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Identity Protection  
**Capacidade de produto:** Identidade segurança e proteção

Temos o prazer de anunciar que o riskDetections nova API para o Microsoft Graph está agora em pré-visualização pública. Pode utilizar esta nova API para ver uma lista de utilizador relacionadas com o Identity Protection e deteções de risco de início de sessão da sua organização. Também pode utilizar esta API para consultar com mais eficiência sua deteções de risco, incluindo detalhes sobre o tipo de deteção, status, nível e muito mais.

Para obter mais informações, consulte a [documentação de referência da API de deteção de risco](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Novas aplicações federadas disponíveis na Galeria de aplicações do Azure AD - Junho de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicações empresariais  
**Capacidade de produto:** Integração de terceiros 3

De 2019 de Junho, adicionámos que suportam a esses 22 novas aplicações com a Federação para a Galeria de aplicações:

[Kit de ferramentas do Azure AD SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [cliente VPN do Azure](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [ Programa auxiliar de programa auxiliar](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes Benz no carro Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [ Consola de infraestrutura de nuvem do Oracle](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [autenticação CyberArk SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx ](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise SO](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Gestor de acesso do Oracle para o varejo Oracle propaganda, Gestor de acesso do Oracle para o Oracle E-Business Suite, Oracle IDCS para E-Business Suite, Oracle IDCS para PeopleSoft, Oracle IDCS para JD Edwards

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o aprovisionamento de contas de utilizador para estas aplicações de SaaS suporte

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicações empresariais  
**Capacidade de produto:** Monitorização e relatórios

Agora já pode automatizar a criar, atualizar e excluir contas de utilizador para estas aplicações recentemente integrado:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor a sua organização com o aprovisionamento de conta de utilizador automatizada, consulte [automatizar o aprovisionamento de utilizadores para aplicações SaaS com o Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Ver o progresso em tempo real do Azure AD do serviço de aprovisionamento

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Aprovisionamento de aplicações  
**Capacidade de produto:** Gestão de ciclo de vida de identidades

Atualizámos o aprovisionamento do Azure AD experiência para incluir uma nova barra de progresso mostra-lhe a distância é o processo de aprovisionamento de utilizador. Também esta experiência atualizada fornece informações sobre o número de utilizadores aprovisionado durante o ciclo de atual, bem como a forma como número de utilizadores que tiverem sido aprovisionada até à data.

Para obter mais informações, consulte [verificar o estado de aprovisionamento de utilizadores](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Agora a marca da empresa é apresentado no termine sessão e telas de erro

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Autenticações (inícios de sessão)  
**Capacidade de produto:** Autenticação do Utilizador

Atualizámos do Azure AD para que a agora a marca da empresa é apresentado na fim de sessão e telas de erro, bem como a página de início de sessão. Não tem de fazer nada para ativar esta funcionalidade, do Azure AD simplesmente usa os ativos que já tiver configurado a na **imagem corporativa da empresa** área do portal do Azure.

Para obter mais informações sobre como configurar a marca da empresa, consulte [adicionar imagem corporativa às páginas de Azure Active Directory da sua organização](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>O servidor multi-factor Authentication (MFA) do Azure já não está disponível para novas implementações

**Tipo:** Preterido  
**Categoria de serviço:** MFA  
**Capacidade de produto:** Identidade segurança e proteção

A partir de 1 de Julho de 2019 Microsoft já não irá oferecer servidor MFA para novas implementações. Novos clientes que pretendam exigir autenticação multifator na sua organização tem de utilizar agora com base na cloud do Azure multi-factor Authentication. Clientes que ativaram o servidor de MFA antes de 1 de Julho, não verão uma alteração. Ainda poderá transferir a versão mais recente, obter atualizações futuras e gerar credenciais de ativação.

Para obter mais informações, consulte [guia de introdução do servidor de multi-factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Para obter mais informações sobre com base na cloud do Azure multi-factor Authentication, consulte [planear uma implementação de multi-factor Authentication do Azure com base na cloud](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Maio de 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Alteração de serviço: Suporte futuro para apenas os protocolos de TLS 1.2 no serviço de Proxy de aplicações

**Tipo:** Plano de alteração  
**Categoria de serviço:** Proxy de aplicações  
**Capacidade de produto:** Controlo de Acesso

Para ajudar a fornecer o melhor na classe encriptação para os nossos clientes, estamos nos limitando o acesso a apenas os protocolos de TLS 1.2 no serviço de Proxy de aplicações. Esta alteração será gradualmente implementada para os clientes que são já apenas através de protocolos de TLS 1.2, para que não deve ver todas as alterações.

Descontinuação de TLS 1.0 e TLS 1.1 ocorre no dia 31 de Agosto de 2019, mas iremos fornecer aviso antecipado adicional, para que tenha tempo para se preparar para esta alteração. Para se preparar para esta alteração certificar-se de que suas combinações de servidor de cliente e servidor de navegador, incluindo todos os clientes os seus utilizadores utilizam para aceder às aplicações publicadas através do Proxy de aplicações, são atualizados para utilizar o protocolo TLS 1.2 para manter a ligação à aplicação Serviço de proxy. Para obter mais informações, consulte [adicionar uma aplicação no local para acesso remoto através do Proxy de aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Utilize o relatório de utilização e informações para ver a sua aplicação dados relacionados com o início de sessão

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicações empresariais  
**Capacidade de produto:** Monitorização e relatórios

Agora, pode utilizar o relatório de utilização e informações, localizado na **aplicações empresariais** área do portal do Azure, para obter uma visão centrada na aplicação dos seus dados início de sessão, incluindo informações sobre:

- Principais aplicações utilizadas para a sua organização

- Aplicações com os mais falhas inícios de sessão

- Erros de início de sessão principais para cada aplicação

Para obter mais informações sobre esta funcionalidade, consulte [a utilização e informações de relatórios no portal do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizar o aprovisionamento de utilizador para aplicações com o Azure AD na cloud

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicações empresariais  
**Capacidade de produto:** Monitorização e relatórios

Siga estes tutoriais de novo para utilizar o serviço de aprovisionamento do AD do Azure para automatizar a criação, eliminação e a atualização de contas de utilizador para as seguintes aplicações com base na cloud:

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Pode também seguir esta nova [Dropbox tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), que fornece informações sobre como provisionar objetos de grupo.

Para obter mais informações sobre como proteger melhor a sua organização através do aprovisionamento de conta de utilizador automatizada, consulte [automatizar o aprovisionamento de utilizadores para aplicações SaaS com o Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Pontuação de segura de identidade já está disponível no Azure AD (disponibilidade geral)

**Tipo:** Novo recurso  
**Categoria de serviço:** N/A  
**Capacidade de produto:** Identidade segurança e proteção

Agora pode monitorizar e melhorar a sua identidade postura de segurança utilizando a identidade proteger recursos de classificação no Azure AD. A identidade de segurança pontuação funcionalidade utiliza um dashboard único para ajudá-lo a:

- Avaliar objetivamente a sua postura de segurança de identidade, com base numa pontuação entre 1 e 223.

- Planear os aprimoramentos de segurança de identidade

- Reveja o sucesso de seus aprimoramentos de segurança

Para obter mais informações sobre a funcionalidade de pontuação de segurança de identidade, consulte [o que é a pontuação de seguro de identidade no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nova experiência de registos de aplicação está agora disponível (disponibilidade geral)

**Tipo:** Novo recurso  
**Categoria de serviço:** Autenticações (inícios de sessão)  
**Capacidade de produto:** Experiência do desenvolvedor

A nova [registos das aplicações](https://aka.ms/appregistrations) experiência está agora em disponibilidade geral. Esta nova experiência inclui todas as principais funcionalidades que estiver familiarizado com a partir do portal do Azure e o portal de registo de aplicação e aprimora-los por meio de:

- **Melhor gestão de aplicações.** Em vez de ver as suas aplicações em portais diferentes, agora pode ver todas as suas aplicações num único local.

- **Registo da aplicação simplificada.** A experiência de navegação melhorada para a experiência de seleção de permissão renovada, do agora é mais fácil para se registrar e gerir as suas aplicações.

- **Obter informações mais detalhadas.** Pode encontrar mais detalhes sobre a sua aplicação, incluindo guias de início rápido e muito mais.

Para obter mais informações, consulte [plataforma de identidade do Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) e o [experiência de registos de aplicação está agora em disponibilidade geral!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) anúncio do blogue.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Novos recursos disponíveis na API de utilizadores de risco do Identity Protection

**Tipo:** Novo recurso  
**Categoria de serviço:** Identity Protection  
**Capacidade de produto:** Identidade segurança e proteção

É com prazer que Anunciamos que agora pode utilizar a API de utilizadores de risco para obter o histórico de risco dos utilizadores, dispensar utilizadores de risco e para confirmar se os utilizadores como comprometido. Esta alteração ajuda-o a forma mais eficiente de atualizar o estado de risco dos seus utilizadores e compreender o respetivo histórico de risco.

Para obter mais informações, consulte a [documentação de referência da API de utilizadores de risco](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Novas aplicações federadas disponíveis na Galeria de aplicações do Azure AD - Maio de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicações empresariais  
**Capacidade de produto:** Integração de terceiros 3

Maio de 2019, adicionámos que 21 novos aplicativos com a Federação de suporte para a Galeria de aplicações:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Real liga](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [início de sessão simples](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [vendas do Marketo envolver](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 de RH Global](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [à área de trabalho de quantum](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Controle](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Criação de grupos aprimorada e gerenciamento experiências no portal do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade de produto:** Colaboração

Foram feitas melhorias para as experiências relacionadas com grupos no portal do Azure AD. Estas melhorias permitem aos administradores para melhor gerir listas de grupos, listas de membros e para fornecer opções de criação adicionais.

Melhoramentos incluem:

- Filtragem básica por tipo de associação e o tipo de grupo.

- Adição de novas colunas, como o endereço de origem e de E-Mail.

- Apresenta uma lista de capacidade de selecionar vários grupos, membros e proprietário para eliminação fácil.

- Capacidade de escolher um endereço de e-mail e adicionar proprietários durante a criação de grupo.

Para obter mais informações, consulte [criar um grupo básico e adicionar membros com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Configurar uma política de nomes para grupos do Office 365 no portal do Azure AD (disponibilidade geral)

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade de produto:** Colaboração

Os administradores agora podem configurar uma política de nomes para grupos do Office 365, com o portal do Azure AD. Esta alteração ajuda a impor as convenções de nomenclatura consistentes de grupos do Office 365 criados ou editados por utilizadores na sua organização.

Pode configurar a política de nomes para grupos do Office 365 de duas formas diferentes:

- Defina prefixos ou sufixos, que são adicionados automaticamente para um nome de grupo.

- Carregar um conjunto personalizado de palavras bloqueadas para a sua organização, que não são permitidos em nomes de grupo (por exemplo, "CEO, folhas de pagamento, RH").

Para obter mais informações, consulte [impor uma política de atribuição de nomes para grupos do Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Pontos de extremidade do Microsoft Graph API estão agora disponíveis para os registos de atividades do Azure AD (disponibilidade geral)

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Relatórios  
**Capacidade de produto:** Monitorização e relatórios

Temos o prazer de anunciar a disponibilidade geral do suporte de pontos finais de API do Microsoft Graph para a atividade do Azure AD registos. Com esta versão, agora já pode utilizar a versão 1.0 do Azure AD de auditoria registos, bem como os registos de início de sessão APIs.

Para obter mais informações, consulte [descrição geral de API de log de auditoria do Azure AD](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Os administradores agora podem utilizar o acesso condicional para o processo de registo combinado (pré-visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Acesso Condicional  
**Capacidade de produto:** Identidade segurança e proteção  

Os administradores agora podem criar políticas de acesso condicional para utilização pela página de registo combinada. Isto inclui a aplicação de políticas para permitir o registo se:

- Os utilizadores estiverem numa rede fidedigna.

- Os utilizadores são um baixo risco de início de sessão.

- Os usuários estão num dispositivo gerido.

- Os utilizadores concordar com termos a organização de utilização (TOU).

Para obter mais informações sobre o acesso condicional e de reposição de palavra-passe, pode ver o [combinado de acesso condicional do Azure AD MFA e mensagem de blogue de experiência de registo de reposição de palavra-passe](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Para obter mais informações sobre as políticas de acesso condicional para o processo de registo combinado, consulte [políticas de acesso condicional para o registo combinado](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Para obter mais informações sobre os termos do Azure AD da funcionalidade de utilização, consulte [termos do Azure Active Directory da funcionalidade de utilização](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Abril de 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-in-refreshed-azure-ad-identity-protection"></a>Nova deteção de inteligência de ameaças do Azure AD está agora disponível em atualizados do Azure AD Identity Protection

**Tipo:** Novo recurso  
**Categoria de serviço:** Azure AD Identity Protection  
**Capacidade de produto:** Identidade segurança e proteção

Deteção de inteligência de ameaças do Azure AD está agora disponível no Azure AD Identity Protection atualizado. Esta nova funcionalidade ajuda a indicar atividade do utilizador que está fora do comum para um utilizador específico ou que seja consistente com os padrões de ataques conhecidos com base na inteligência de ameaças internas e externas da Microsoft.

Para obter mais informações sobre a versão atualizada do Azure AD Identity Protection, consulte a [quatro principais melhorias do Azure AD Identity Protection estão agora em pré-visualização pública](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) blog e o [o que é o Azure Active Directory Proteção de identidade (atualizada)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) artigo. Para obter mais informações sobre a deteção de inteligência de ameaças do Azure AD, consulte a [eventos de risco do Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) artigo.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Gestão de direitos do AD do Azure está agora disponível (pré-visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Identity Governance  
**Capacidade de produto:** Identity Governance

Gestão de direitos do Azure AD, agora em pré-visualização pública, ajuda os clientes para delegar a gestão de pacotes de acesso, que define como os funcionários e parceiros de negócios podem solicitar acesso, que têm de aprovar e quanto aos quais têm acesso. Pacotes de acesso podem gerir a associação do Azure AD e Office 365 grupos, atribuições de funções nas aplicações empresariais e atribuições de funções de sites do SharePoint Online. Saiba mais sobre a gestão de direitos no [descrição geral da gestão de direitos do Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Para saber mais sobre a amplitude dos recursos de governação de identidade do Azure AD, incluindo o Privileged Identity Management, revisões de acesso e termos de utilização, veja [o que é a governação de identidade do Azure AD?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configurar uma política de nomes para grupos do Office 365 no portal do Azure AD (pré-visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade de produto:** Colaboração

Os administradores agora podem configurar uma política de nomes para grupos do Office 365, com o portal do Azure AD. Esta alteração ajuda a impor as convenções de nomenclatura consistentes de grupos do Office 365 criados ou editados por utilizadores na sua organização.

Pode configurar a política de nomes para grupos do Office 365 de duas formas diferentes:

- Defina prefixos ou sufixos, que são adicionados automaticamente para um nome de grupo.

- Carregar um conjunto personalizado de palavras bloqueadas para a sua organização, que não são permitidos em nomes de grupo (por exemplo, "CEO, folhas de pagamento, RH").

Para obter mais informações, consulte [impor uma política de atribuição de nomes para grupos do Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Registos de atividade do AD do Azure estão agora disponíveis no Azure Monitor (disponibilidade geral)

**Tipo:** Novo recurso  
**Categoria de serviço:** Relatórios  
**Capacidade de produto:** Monitorização e relatórios

Para ajudar a resolver os seus comentários sobre visualizações com os registos de atividades do Azure AD, que estamos a introduzir uma nova funcionalidade de informações no Log Analytics. Esta funcionalidade ajuda-o a obter informações sobre os recursos do Azure AD com os nossos modelos interativos, chamados de pastas de trabalho. Estas previamente criadas pastas de trabalho podem fornecer os detalhes para aplicações ou utilizadores e incluem:

- **Inícios de sessão.** Fornece detalhes para aplicações e utilizadores, incluindo o início de sessão local, o sistema de operativo em utilização ou cliente de navegador e versão e o número de inícios de sessão com êxito ou falhados.

- **Autenticação e acesso condicional.** Fornece detalhes para aplicações e utilizadores através da autenticação de legado, incluindo a utilização de multi-factor Authentication acionada pelas políticas de acesso condicional, aplicações através de políticas de acesso condicional, e assim por diante.

- **Análise de falhas de início de sessão.** Ajuda-o a determinar se os erros de início de sessão estão a ocorrer devido a uma ação do utilizador, a problemas de políticas ou a sua infraestrutura.

- **Relatórios personalizados.** Pode criar um novo ou editar as pastas de trabalho existentes que ajudam a personalizar a funcionalidade de informações para a sua organização.

Para obter mais informações, consulte [como utilizar as pastas de trabalho do Azure Monitor para relatórios do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Novas aplicações federadas disponíveis na Galeria de aplicações do Azure AD - Abril de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicações empresariais  
**Capacidade de produto:** Integração de terceiros 3

De 2019 de Abril, adicionámos que 21 novos aplicativos com a Federação de suporte para a Galeria de aplicações:

[No SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [ Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Ligue-se](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel ligar](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (SSO baseado em funções)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent estudo de patrimônio gestão](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Gestor de certificados de Sectigo](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Revisões de acesso nova opção de frequência e a seleção da função múltipla

**Tipo:** Novo recurso  
**Categoria de serviço:** Revisões de Acesso  
**Capacidade de produto:** Identity Governance

As revisões de acesso a novas atualizações no Azure AD permitem-lhe:

- Alterar a frequência de seu acesso a revisões para **semiestruturados annually**, além das opções já existentes de semanais, mensais, trimestrais e anuais.

- Selecione vários Azure AD e funções de recursos do Azure durante a criação de um único acesso rever. Nesta situação, todas as funções são configuradas com as mesmas definições e todos os revisores são notificados ao mesmo tempo.

Para obter mais informações sobre como criar uma revisão de acesso, consulte [criar uma revisão de acesso de grupos ou as revisões de acesso a aplicações no Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Sistemas de alerta do Azure AD Connect e-mail estão a transitar, envio de novas informações do remetente de e-mail para alguns clientes

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Sincronização do AD  
**Capacidade de produto:** Plataforma

O Azure AD Connect está no processo de transição nossos sistemas de alerta de e-mail, potencialmente que um remetente de e-mail novo mostra alguns clientes. Para resolver isso, tem de adicionar `azure-noreply@microsoft.com` a lista de permissões da sua organização ou não poderá continuar a receber alertas importantes a partir do Office 365, Azure ou os serviços de sincronização.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Alterações de sufixo UPN estão agora com êxito entre domínios federados, no Azure AD Connect

**Tipo:** Fixo  
**Categoria de serviço:** Sincronização do AD  
**Capacidade de produto:** Plataforma

Pode agora alterar com êxito sufixo UPN de um utilizador de um domínio federados para outro domínio federados no Azure AD Connect. Esta correção significa que já não deve ocorrer a mensagem de erro FederatedDomainChangeError durante o ciclo de sincronização ou receber uma notificação de e-mail a indicar, "não é possível atualizar este objeto no Azure Active Directory, porque o atributo [ FederatedUser.UserPrincipalName], não é válido. Atualize o valor nos serviços de diretório local".

Para obter mais informações, consulte [resolver problemas de erros durante a sincronização](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Maior segurança usando a política de acesso condicional com base na proteção da aplicação no Azure AD (pré-visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Acesso Condicional  
**Capacidade de produto:** Identidade segurança e proteção

Acesso condicional com base na proteção aplicação agora está disponível através da **necessitam de proteção de aplicações** política. Esta nova política ajuda a aumentar a segurança da sua organização ao ajudar a impedir que:

- Utilizadores obtenham acesso a aplicações sem uma licença do Microsoft Intune.

- Usuários não é possível obter uma política de proteção de aplicações do Microsoft Intune.

- Utilizadores obtenham acesso a aplicações sem uma política de proteção de aplicações do Microsoft Intune configurada.

Para obter mais informações, consulte [como requerer a política de proteção de acesso à aplicação de cloud com o acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Novo suporte para o Azure AD início de sessão único e acesso condicional no Microsoft Edge (pré-visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Acesso Condicional  
**Capacidade de produto:** Identidade segurança e proteção

Melhorámos o nosso suporte do Azure AD para o Microsoft Edge, inclusive fornecendo o novo suporte para o Azure AD início de sessão único e acesso condicional. Se tiver utilizado anteriormente o Microsoft Intune Managed Browser, pode agora utilizar a Microsoft Edge em vez disso.

Para obter mais informações sobre como configurar e gerir os seus dispositivos e aplicações através do acesso condicional, consulte [exigir dispositivos para aceder à aplicação de cloud com o acesso condicional geridos](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) e [necessitam de aplicações de cliente para a cloud aprovadas acesso de aplicação com acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Para obter mais informações sobre como gerir o acesso com o Microsoft Edge com políticas do Microsoft Intune, consulte [acesso à Internet de gerir através de um browser do Microsoft Intune protegido por política](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Março de 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Arquitetura de experiências de identidade e de política personalizada do suporte no Azure Active Directory B2C está agora disponível (GA)

**Tipo:** Novo recurso  
**Categoria de serviço:** B2C - gestão de identidades de consumidor  
**Capacidade de produto:** B2B/B2C

Agora, pode criar políticas personalizadas no Azure AD B2C, incluindo as seguintes tarefas, que são suportadas em escala e, em nosso SLA do Azure:

- Criar e carregar jornadas de utilizador de autenticação personalizado com as políticas personalizadas.

- Descreva jornadas de usuário passo a passo como trocas entre fornecedores de afirmações.

- Defina a ramificação condicional no jornadas de utilizador.

- Transformar e mapear declarações para utilização em decisões em tempo real e comunicações.

- Utilize os serviços de ativada para API do REST de sua jornadas de utilizador de autenticação personalizado. Por exemplo, com fornecedores de e-mail, CRMs e sistemas de autorização do proprietário.

- Federar com fornecedores de identidade que estão em conformidade com o protocolo de OpenIDConnect. Por exemplo, com o multi-inquilino do Azure AD, fornecedores de conta de redes sociais ou fornecedores de verificação de dois fatores.

Para obter mais informações sobre como criar políticas personalizadas, consulte [notas do programador para as políticas personalizadas no Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) e ler [postagem do blog de Alex Simon, inclusive estudos de caso](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Novas aplicações federadas disponíveis na Galeria de aplicações do Azure AD - Março de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicações empresariais  
**Capacidade de produto:** Integração de terceiros 3

De 2019 de Março, adicionámos que suportam a 14 novos aplicativos com a Federação para a Galeria de aplicações:

[ISEC7 Mobile Exchange delegado](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Sistema de auditoria com base em explicação](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Saiba](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [assuntos de desempenho de Powerschool](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit horizontes](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [tarefa no](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Novo Zscaler e Atlassian aprovisionamento conectores na galeria do Azure AD - Março de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aprovisionamento de aplicações  
**Capacidade de produto:** Integração de terceiros 3

Automatize a criar, atualizar e excluir contas de utilizador para as seguintes aplicações:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler um](https://aka.ms/ZscalerOneProvisioning), [Zscaler dois](https://aka.ms/ZscalerTwoProvisioning), [Zscaler três](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud ](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Para obter mais informações sobre como proteger melhor a sua organização através do aprovisionamento de conta de utilizador automatizada, consulte [automatizar o aprovisionamento de utilizadores para aplicações SaaS com o Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Restaurar e gerir os seus grupos do Office 365 eliminados no portal do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade de produto:** Colaboração

Agora pode ver e gerir os seus grupos do Office 365 eliminados do portal do Azure AD. Esta alteração ajuda-o a ver quais os grupos estão disponíveis para restaurar, juntamente com a informá-lo permanentemente eliminar todos os grupos que não são necessários para sua organização.

Para obter mais informações, consulte [restauro expirou ou grupos eliminados](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Início de sessão único está agora disponível para aplicações protegidas de SAML do Azure AD no local através do Proxy de aplicações (pré-visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Proxy de aplicações  
**Capacidade de produto:** Controlo de Acesso

Agora pode fornecer uma experiência de início de sessão único (SSO) para aplicações no local, autenticação SAML, juntamente com o acesso remoto a estas aplicações através do Proxy de aplicações. Para obter mais informações sobre como configurar o SAML SSO com as suas aplicações no local, consulte [SAML início de sessão único para aplicações no local com o Proxy de aplicações (pré-visualização)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Aplicações de cliente em ciclos de solicitação serão interrompidas para melhorar a experiência de utilizador e fiabilidade

**Tipo:** Novo recurso  
**Categoria de serviço:** Autenticações (inícios de sessão)  
**Capacidade de produto:** Autenticação do Utilizador

Aplicações de cliente incorretamente podem emitir centenas dos mesmos pedidos de início de sessão durante um curto período de tempo. Estes pedidos, se forem bem-sucedidos ou não, contribuem para uma experiência de usuário insatisfatória e o cliente de cargas de trabalho para o IDP, aumentando a latência para todos os utilizadores e reduzindo a disponibilidade do IDP.

Esta atualização envia uma `invalid_grant` erro: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` para aplicações de cliente que emitem pedidos de duplicação várias vezes durante um curto período de tempo, além do escopo da operação normal. Aplicações de cliente que encontram este problema devem mostrar uma linha de comandos interativa, exigir que o utilizador iniciar sessão novamente. Para obter mais informações sobre esta alteração e sobre como resolver a sua aplicação se encontrar este erro, consulte [o que há de novo para a autenticação?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nova experiência de utilizador de registos de auditoria já está disponível

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Relatórios  
**Capacidade de produto:** Monitorização e relatórios

Nós criamos um novo do Azure AD **registos de auditoria** página para ajudar a melhorar a legibilidade e como pesquisar para sua informação. Para ver a nova **registos de auditoria** página, selecione **registos de auditoria** no **atividade** secção do Azure AD.

![Nova página de registos de auditoria, com informações de exemplo](media/whats-new/audit-logs-page.png)

Para obter mais informações sobre a nova **registos de auditoria** página, consulte [relatórios de atividade no portal do Azure Active Directory de auditoria](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Novos avisos e as orientações para ajudar a evitar o bloqueio acidental de administrador de políticas de acesso condicional configuradas incorretamente

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Acesso Condicional  
**Capacidade de produto:** Identidade segurança e proteção

Para ajudar a impedir que os administradores bloqueiem acidentalmente a próprios fora de seus próprios inquilinos através de políticas de acesso condicional configuradas incorretamente, nós criamos novos avisos e orientações atualizadas no portal do Azure. Para obter mais informações sobre as novas diretrizes, consulte [quais são as dependências do serviço no Azure Active Directory condicional acesso](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Termos de utilizador melhorada de experiências de utilização em dispositivos móveis

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Termos de utilização  
**Capacidade de produto:** Governação

Atualizámos os nossos termos existentes de experiências de utilização para ajudar a melhorar como rever e aceitar os termos de utilização num dispositivo móvel. Agora pode ampliar e reduzir, volte atrás, transferir as informações e selecione hiperlinks. Para obter mais informações sobre os termos atualizados de utilização, consulte [termos do Azure Active Directory da funcionalidade de utilização](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Disponibilidade de experiência de download de novos registos de atividades do Azure AD

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Relatórios  
**Capacidade de produto:** Monitorização e relatórios

Pode agora transferir grandes quantidades de registos de atividade diretamente a partir do portal do Azure. Esta atualização permite-lhe:

- Transferir até 250 000 linhas.

- Seja notificado depois de concluída a transferência.

- Personalize o seu nome de ficheiro.

- Determine o formato de saída, JSON ou CSV.

Para obter mais detalhes sobre esta funcionalidade, consulte [início rápido: Transferir um relatório de auditoria no portal do Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Alteração interruptiva: Atualizações à avaliação de condição pelo Exchange ActiveSync (EAS)

**Tipo:** Plano de alteração  
**Categoria de serviço:** Acesso Condicional  
**Capacidade de produto:** Controlo de Acesso

Estamos atualmente a atualizar a forma como o Exchange ActiveSync (EAS) avalia as seguintes condições:

- Localização do utilizador, com base no país, região ou endereço IP

- Início de sessão de risco

- Plataforma de dispositivo

Se usou anteriormente essas condições em suas políticas de acesso condicional, lembre-se de que o comportamento de condição podem ser alteradas. Por exemplo, se tiver utilizado anteriormente a condição de localização do utilizador numa política, talvez se a política agora a ser ignorada com base na localização dos seus utilizadores.

---

## <a name="february-2019"></a>Fevereiro de 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Configurável encriptação de tokens SAML do Azure AD (pré-visualização pública) 

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicações empresariais  
**Capacidade de produto:** SSO

Agora pode configurar qualquer aplicação SAML suportada para receber tokens SAML encriptados. Quando configurado e utilizado com uma aplicação, o AD do Azure encripta as asserções SAML emitidas com uma chave pública obtida a partir de um certificado armazenado no Azure AD.

Para obter mais informações sobre como configurar a encriptação de tokens SAML, consulte [encriptação de tokens de configurar o Azure AD SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Criar uma revisão de acesso para grupos ou aplicações com as revisões de acesso do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Revisões de Acesso  
**Capacidade de produto:** Governação

Agora pode incluir vários grupos ou revisão para associação a um grupo ou a atribuição de aplicações de acesso de aplicações num único Azure AD. Revisões de acesso a vários grupos ou aplicações são configuradas utilizando as definições e todos os revisores incluídos são notificados ao mesmo tempo.

Para obter mais informações sobre como criar uma revisão de acesso utilizando as revisões de acesso do Azure AD, consulte [criar uma revisão de acesso de grupos ou aplicações nas revisões de acesso do Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Novas aplicações federadas disponíveis na Galeria de aplicações do Azure AD - Fevereiro de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicações empresariais  
**Capacidade de produto:** Integração de terceiros 3
 
Fevereiro de 2019, adicionámos que 27 novos aplicativos com a Federação de suporte para a Galeria de aplicações:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [DEDO FAT](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Clique de permissão, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [sísmica ](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Partilha um sonho](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods integração Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [LMS do conhecimento em qualquer lugar](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [UO Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [dados Periscope](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud por Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp plataforma de produtividade](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Registo MFA/SSPR combinado aprimorado

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Reposição de palavra-passe Self-Service  
**Capacidade de produto:** Autenticação do Utilizador

Em resposta aos comentários dos clientes, melhorámos a experiência pré-visualização de registo da MFA/SSPR combinada, ajudando a que os utilizadores registem as suas informações de segurança mais rapidamente para o MFA e o SSPR. 

**Para ativar a experiência aprimorada para os seus utilizadores hoje em dia, siga estes passos:**

1. Como um administrador global ou administrador de utilizadores, inicie sessão no portal do Azure e aceda a **do Azure Active Directory > definições de utilizador > Gerir as definições para as funcionalidades de pré-visualização do painel de acesso**. 

2. Na **funcionalidades de utilizadores que podem utilizar a pré-visualização para registar e gerir informações de segurança – Atualize** opção, optar por ativar as funcionalidades para um **grupo selecionado de utilizadores** ou para **todos os utilizadores** .

Ao longo das próximas semanas, podemos irá remover a capacidade de ativar a antiga combinada MFA/SSPR registo experiência de pré-visualização para os inquilinos que não o tiver ativado.

**Para ver se o controle será removido do seu inquilino, siga estes passos:**

1. Como um administrador global ou administrador de utilizadores, inicie sessão no portal do Azure e aceda a **do Azure Active Directory > definições de utilizador > Gerir as definições para as funcionalidades de pré-visualização do painel de acesso**.  

2. Se o **utilizadores que podem utilizar as funcionalidades de pré-visualização para registar e gerir informações de segurança** opção estiver definida como **nenhum**, a opção será removida do seu inquilino.

Independentemente de se ativou anteriormente sobre o registo MFA/SSPR combinado antigo experiência de pré-visualização para os utilizadores ou não, a experiência antiga será desligada numa data futura. Por causa disso, sugerimos enfaticamente que passar para a experiência nova e aprimorada logo que possível.

Para obter mais informações sobre a experiência melhorada de registo, consulte a [combinado de aprimoramentos avançados com o Azure AD MFA e experiência de registo de reposição de palavra-passe](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Experiência de gestão de política atualizada para fluxos de utilizador

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** B2C - gestão de identidades de consumidor  
**Capacidade de produto:** B2B/B2C

Atualizámos o processo de criação e gestão de política para os fluxos de utilizador (anteriormente conhecidos como, incorporadas políticas) mais fácil. Esta nova experiência agora é a predefinição para todos os inquilinos do Azure AD.

Pode fornecer comentários adicionais e sugestões, utilizando o sorriso ou frown ícones no **envie-nos comentários** área na parte superior do ecrã portal.

Para obter mais informações sobre a nova experiência de gestão de política, consulte a [do Azure AD B2C tem agora a personalização de JavaScript e muitos outros novos recursos](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Escolha versões de elemento de página específica fornecidas pelo Azure AD B2C

**Tipo:** Novo recurso  
**Categoria de serviço:** B2C - gestão de identidades de consumidor  
**Capacidade de produto:** B2B/B2C

Agora, pode escolher uma versão específica de elementos da página fornecido pelo Azure AD B2C. Ao selecionar uma versão específica, pode testar as atualizações antes de serem apresentados numa página e pode obter um comportamento previsível. Além disso, pode agora optar por impor versões de página específica para permitir que as personalizações de JavaScript. Para ativar esta funcionalidade, vá para o **propriedades** página nos seus fluxos de utilizador.

Para obter mais informações sobre como escolher versões específicas dos elementos da página, consulte a [do Azure AD B2C tem agora a personalização de JavaScript e muitos outros novos recursos](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Requisitos de palavra-passe do utilizador final configuráveis para B2C (GA)

**Tipo:** Novo recurso  
**Categoria de serviço:** B2C - gestão de identidades de consumidor  
**Capacidade de produto:** B2B/B2C

Agora pode configurar complexidade de palavra-passe da sua organização para usuários finais, em vez de precisar usar seu nativas da política de palavra-passe do Azure AD. Do **propriedades** painel de utilizadores (anteriormente conhecidos como as políticas incorporadas) de fluxos, pode escolher uma complexidade de palavra-passe de **simples** ou **forte**, ou pode criar uma **personalizado** conjunto de requisitos.

Para obter mais informações sobre a configuração do requisito de complexidade de palavra-passe, consulte [configurar requisitos de complexidade de palavras-passe no Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Novos modelos predefinidos para uma experiência de autenticação com marca personalizada

**Tipo:** Novo recurso  
**Categoria de serviço:** B2C - gestão de identidades de consumidor  
**Capacidade de produto:** B2B/B2C

Pode usar o nosso novo modelos predefinidos, localizados no **layouts de página** painel dos seus fluxos de utilizador (anteriormente conhecido como as políticas incorporadas) criar um personalizado de marca de experiência de autenticação para os seus utilizadores.

Para obter mais informações sobre como utilizar os modelos, consulte [do Azure AD B2C tem agora a personalização de JavaScript e muitos outros novos recursos](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Janeiro de 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Colaboração B2B de diretório Active Directory a utilizar a autenticação de código de acesso único (pré-visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** B2B  
**Capacidade de produto:** B2B/B2C

Autenticação de código de acesso Monouso (OTP) foi introduzida para os utilizadores convidados B2B que não podem ser autenticados através de outros meios, como o Azure AD, uma conta Microsoft (MSA) ou o Federação de Google. Este novo método de autenticação significa que os utilizadores não têm de criar uma nova conta Microsoft de convidado. Em vez disso, ao resgatar um convite ou aceder a um recurso compartilhado, um utilizador convidado pode pedir um código temporário para ser enviado para um endereço de e-mail. O utilizador convidado usando este código temporário, pode continuar a iniciar sessão.

Para obter mais informações, consulte [autenticação de código de acesso único por E-Mail (pré-visualização)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) e o blog [do Azure AD torna a partilha e colaboração integrada para qualquer utilizador com qualquer conta](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Novas definições de cookie do Proxy de aplicações do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Proxy de aplicações  
**Capacidade de produto:** Controlo de Acesso

Introduzimos três novas definições de cookies, disponíveis para as suas aplicações publicadas através do Proxy de aplicações:

- **Utilize cookie somente de HTTP.** Conjuntos de **HTTPOnly** sinalizador no seus cookies de acesso e de sessão do Proxy de aplicações. Ativar esta definição fornece benefícios de segurança adicionais, como ajudar a impedir a copiar ou modificação de cookies por meio de scripts do lado do cliente. Recomendamos que ative este sinalizador (escolher **Sim**) para os benefícios adicionais.

- **Utilize cookie seguro.** Conjuntos de **seguro** sinalizador no seus cookies de acesso e de sessão do Proxy de aplicações. Ativar esta definição fornece benefícios de segurança adicional, tornando-se de que os cookies apenas são transmitidos através de canais de segurança de TLS, tal como HTTPS. Recomendamos que ative este sinalizador (escolher **Sim**) para os benefícios adicionais.

- **Utilize cookie persistente.** Impede que o acesso cookies prestes a expirar quando o browser é fechado. Estes cookies pela última vez para o tempo de vida do token de acesso. No entanto, os cookies são repostos se for atingida a hora de expiração ou se o utilizador o elimine manualmente o cookie. Recomendamos que mantenha a predefinição **não**, apenas Ativando a definição de aplicações mais antigas que não partilham cookies entre processos.

Para obter mais informações sobre os novos cookies, consulte [definições de cookies para aceder a aplicações no local no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Novas aplicações federadas disponíveis na Galeria de aplicações do Azure AD - Janeiro de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicações empresariais  
**Capacidade de produto:** Integração de terceiros 3
 
De 2019 de Janeiro, adicionámos que estes 35 novas aplicações com a Federação de suporte para a Galeria de aplicações:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [paleta de talento](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco guarda-chuva](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Administrador de acesso à Internet](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [lembrete de expiração](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Visualizador](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [verbo](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Digital de Pavaso fechar](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [Sistema de GTNexus SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARES para Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 para o Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [ALP de Ferry Korn](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas plataforma](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Novos aprimoramentos do Azure AD Identity Protection (pré-visualização pública)

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Identity Protection  
**Capacidade de produto:** Identidade segurança e proteção

Temos o prazer de anunciar que adicionamos os seguintes melhoramentos para a oferta de pré-visualização pública do Azure AD Identity Protection, incluindo:

- Uma interface do usuário mais integrada e atualizados

- APIs adicionais

- Avaliação de riscos melhorada através de machine learning

- Alinhamento de todo o produto em utilizadores de risco e inícios de sessão arriscados

Para obter mais informações sobre os melhoramentos, consulte [o que é o Azure Active Directory Identity Protection (atualizadas)?](https://aka.ms/IdentityProtectionDocs) Para saber mais e Compartilhe suas idéias por meio das instruções no produto.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nova funcionalidade de bloqueio de aplicação para a aplicação Microsoft Authenticator em dispositivos iOS e Android

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicação Microsoft Authenticator  
**Capacidade de produto:** Identidade segurança e proteção

Para manter seus códigos de acesso monouso, informações da aplicação e as definições da aplicação mais segura, pode ativar a funcionalidade de bloqueio de aplicação na aplicação Microsoft Authenticator. Ativar o bloqueio de aplicação significa que estará mais frequentes para autenticar a utilizar o seu PIN ou biométrico sempre que abrir a aplicação Microsoft Authenticator.

Para obter mais informações, consulte a [aplicação Microsoft Authenticator FAQ](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Capacidades de exportação de avançada do Azure AD Privileged Identity Management (PIM)

**Tipo:** Novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Capacidade de produto:** Privileged Identity Management

Administradores do Privileged Identity Management (PIM) agora podem exportar todas as atribuições de função elegível e Active Directory para um recurso específico, que inclui atribuições de funções para todos os recursos subordinados. Anteriormente, era difícil para os administradores obter uma lista completa das atribuições de funções para uma subscrição e teve de exportar as atribuições de função para cada recurso específico.

Para obter mais informações, consulte [ver histórico de atividade e auditoria para funções de recursos do Azure no PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
