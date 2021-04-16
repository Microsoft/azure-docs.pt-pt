---
title: Novidades? Notas de lançamento - Azure Ative Directory | Microsoft Docs
description: Saiba quais as novidades com o Azure Ative Directory; como as últimas notas de lançamento, problemas conhecidos, correções de bugs, funcionalidades precadas e alterações futuras.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a909d946b55ee8b06d68aa8bee53bc50d2190e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532299"
---
# <a name="whats-new-in-azure-active-directory"></a>Quais as novidades no Diretório Ativo do Azure?

>Seja notificado sobre quando revisitar esta página para obter atualizações copiando e colando este URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` no leitor de feed do leitor de feed ![ RSS. ](./media/whats-new/feed-icon-16x16.png)

A Azure AD recebe melhorias continuadamente. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

- Os últimos lançamentos
- Problemas conhecidos
- Correções de erros
- Funcionalidade preterida
- Planos para alterações

Esta página é atualizada mensalmente, por isso revisite-a regularmente. Se procura itens com mais de seis meses, pode encontrá-los no [Archive for What's new in Azure Ative Directory](whats-new-archive.md).

---

## <a name="march-2021"></a>março de 2021

### <a name="guidance-on-how-to-enable-support-for-tls-12-in-your-environment-in-preparation-for-upcoming-azure-ad-tls-1011-deprecation"></a>Orientações sobre como permitir o suporte para tls 1.2 no seu ambiente, em preparação para a próxima depreciação do Azure AD TLS 1.0/1.1

**Tipo:** Plano de mudança  
**Categoria de serviço:** N/A  
**Capacidade do produto:** Normas

O Azure Ative Directory irá deprecação dos seguintes protocolos em regiões mundiais do Azure Ative Directory a partir de 30 de junho de 2021:


- TLS 1.0
- TLS 1.1
- Suíte cifra 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Os ambientes afetados incluem:

- Nuvem Comercial Azure
- Escritório 365 GCC e WW

Para obter orientações adicionais, consulte [para ativar o suporte para TLS 1.2 no seu ambiente para a deprecação Azure AD TLS 1.1 e 1.0](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment).

---

### <a name="public-preview---azure-ad-entitlement-management-now-supports-multi-geo-sharepoint-online"></a>Antevisão pública - A gestão de direitos AD Azure agora suporta multi-geo SharePoint Online

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Gestão de Direitos
 
Para organizações que usam multi-geo SharePoint Online, pode agora incluir sites desde ambientes multi-geo- específicos até aos seus pacotes de acesso à gestão de direitos. [Saiba mais](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site-preview).

---

### <a name="public-preview---restore-deleted-apps-from-app-registrations"></a>Antevisão pública - Restaurar aplicações eliminadas dos registos da App

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Experiência de Desenvolvedor
 
Os clientes podem agora ver, restaurar e remover permanentemente os registos de aplicações eliminados do portal Azure. Isto aplica-se apenas a aplicações associadas a um diretório, e não a aplicações a partir de uma conta pessoal da Microsoft. [Saiba mais](../develop/quickstart-restore-app.md).
 
---

### <a name="public-preview---new-user-action-in-conditional-access-for-registering-or-joining-devices"></a>Pré-visualização pública - Nova "ação do utilizador" no Acesso Condicional para registo ou junção de dispositivos

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
 Está disponível uma nova ação do utilizador chamada "Registar ou juntar dispositivos" no acesso condicional. Esta ação do utilizador permite-lhe controlar as políticas de autenticação de vários fatores (MFA) para o registo do dispositivo Azure AD. 

Atualmente, esta ação do utilizador apenas permite ativar o MFA como um controlo quando os utilizadores registam ou juntam dispositivos para Azure AD. Outros controlos que dependem ou não são aplicáveis ao registo do dispositivo Azure AD são desativados com esta ação do utilizador. [Saiba mais](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions). 
 
---

### <a name="public-preview---optimize-connector-groups-to-use-the-closest-application-proxy-cloud-service"></a>Visualização pública - Otimizar os grupos de conector para utilizar o serviço de nuvem Proxy de aplicação mais próximo

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** App Proxy  
**Capacidade do produto:** Controlo de Acessos
 
Com esta nova capacidade, os grupos de conector podem ser atribuídos ao serviço de procuração de aplicação regional mais próximo em que uma aplicação está hospedada. Isto pode melhorar o desempenho da aplicação em cenários onde as aplicações são hospedadas em outras regiões que não a região do arrendatário. [Saiba mais](../manage-apps/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview). 
 
---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>Pré-visualização pública - Identidades Externas Self-Service Inscrição em AAD usando contas de código de e-mail One-Time

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C

Os utilizadores externos poderão agora utilizar as contas de Email One-Time Passcode para se inscreverem nas aplicações AD 1ª e LOB do Azure. [Saiba mais](../external-identities/one-time-passcode.md).

---

### <a name="public-preview---availability-of-ad-fs-sign-ins-in-azure-ad"></a>Pré-visualização pública - Disponibilidade de Sign-Ins AD FS em Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Relatório de & de Monitorização
 
A atividade de entrada de FS da AD pode agora ser integrada com relatórios de atividade da Azure AD, proporcionando uma visão unificada da infraestrutura de identidade híbrida. Utilizando o relatório de Sign-Ins Azure, Log Analytics e Azure Monitor Workbooks, é possível realizar análises aprofundadas tanto para cenários de login da AAD como para AD FS, tais como bloqueios de conta AD FS, tentativas de senhas erradas e picos de tentativas inesperadas de login.

Para saber mais, visite [os sign-ins da AD FS em Azure AD com Connect Health](../hybrid/how-to-connect-health-ad-fs-sign-in.md).

---

### <a name="general-availability---staged-rollout-to-cloud-authentication"></a>Disponibilidade geral - Lançamento encenado para autenticação em nuvem

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Conexão de anúncios  
**Capacidade do produto:** Autenticação do Utilizador
 
O lançamento encenado para a autenticação em nuvem está geralmente disponível. A funcionalidade de lançamento encenada permite testar seletivamente grupos de utilizadores com métodos de autenticação em nuvem, tais como a Autenticação Passthrough (PTA) ou o Password Hash Sync (PHS). Entretanto, todos os outros utilizadores dos domínios federados continuam a utilizar serviços da federação, como a AD FS ou quaisquer outros serviços da federação para autenticar os utilizadores. [Saiba mais](../hybrid/how-to-connect-staged-rollout.md).

---

### <a name="general-availability---user-type-attribute-can-now-be-updated-in-the-azure-admin-portal"></a>Disponibilidade Geral - Atributo Tipo de Utilizador pode agora ser atualizado no portal de administração Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Experiência e Gestão do Utilizador  
**Capacidade do produto:** Gestão de Utilizadores
 
Os clientes podem agora atualizar o tipo de utilizador de utilizadores Azure AD quando atualizarem as informações do seu perfil de utilizador a partir do portal de administração Azure. O tipo de utilizador também pode ser atualizado a partir do Microsoft Graph. Para saber mais, consulte [Adicionar ou atualizar as informações do perfil do utilizador](active-directory-users-profile-azure-portal.md).
 
---

### <a name="general-availability---replica-sets-for-azure-active-directory-domain-services"></a>Disponibilidade Geral - Conjuntos de réplicas para serviços de domínio do diretório ativo Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Serviços de domínio Azure Ad  
**Capacidade do produto:** Serviços de domínio Azure Ad
 
A capacidade de conjuntos de réplicas em Azure AD DS está agora geralmente disponível. [Saiba mais](https://docs.microsoft.com/azure/active-directory-domain-services/concepts-replica-sets).
 
---

### <a name="general-availability---collaborate-with-your-partners-using-email-one-time-passcode-in-the-azure-government-cloud"></a>Disponibilidade geral - Colabore com os seus parceiros usando email One-Time Código de Acesso na nuvem do Governo Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
As organizações na nuvem do Governo do Microsoft Azure podem agora permitir que os seus hóspedes resgatem convites com o Email One-Time Passcode. Isto garante que qualquer utilizador convidado sem contas Azure AD, Microsoft ou Gmail na nuvem do Governo Azure ainda pode colaborar com os seus parceiros, solicitando e introduzindo um código temporário para iniciar scontabilidade de recursos partilhados. [Saiba mais](../external-identities/one-time-passcode.md#note-for-azure-us-government-customers).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---march-2021"></a>Novas Aplicações Federadas disponíveis na galeria AZure AD Application - março de 2021

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Em março de 2021, adicionámos 37 novas aplicações na nossa galeria app com o apoio da Federação:

[Bambuser Live Video Shopping](https://lcx.bambuser.com/), [DeepDyve Inc,](https://www.deepdyve.com/azure-sso) [Moqups,](../saas-apps/moqups-tutorial.md) [RICOH Spaces Mobile,](https://ricohspaces.app/welcome) [Flipgrid](https://auth.flipgrid.com/), [hCaptcha Enterprise](../saas-apps/hcaptcha-enterprise-tutorial.md), [SchoolStream ASA](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx), [TransPerfect GlobalLink Dashboard](../saas-apps/transperfect-globallink-dashboard-tutorial.md), [SimplificaCI,](https://app.simplificaci.com.br/) [Thrive LXP,](../saas-apps/thrive-lxp-tutorial.md) [Lexonis TalentScape,](../saas-apps/lexonis-talentscape-tutorial.md) [Exium,](../saas-apps/exium-tutorial.md) [Sapient,](../saas-apps/sapient-tutorial.md) [TrueChoice,](../saas-apps/truechoice-tutorial.md) [RICOH Spaces,](https://ricohspaces.app/welcome) [Saba Cloud](../saas-apps/learning-at-work-tutorial.md), [Acunetix 360](../saas-apps/acunetix-360-tutorial.md), [Exceed.ai](../saas-apps/exceed-ai-tutorial.md), [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-tutorial.md), Enterprise Vault.cloud [for Outlook](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb), [Smartlook,](../saas-apps/smartlook-tutorial.md) [Accenture Academy](../saas-apps/accenture-academy-tutorial.md), [Onshape,](../saas-apps/onshape-tutorial.md) [Tradeshift,](../saas-apps/tradeshift-tutorial.md) [JuriBlox,](../saas-apps/juriblox-tutorial.md) [SecurityStudio,](../saas-apps/securitystudio-tutorial.md) [ClicData,](https://app.clicdata.com/) [Evergreen,](../saas-apps/evergreen-tutorial.md) [Patchdeck,](https://patchdeck.com/ad_auth/authenticate/) [FAX. PLUS](../saas-apps/fax.plus-tutorial.md), [ValidSign](../saas-apps/validsign-tutorial.md), [AWS Single Sign-on](../saas-apps/aws-single-sign-on-tutorial.md), [Nura Space](https://dashboard.nuraspace.com/login), [Broadcom DX SaaS](../saas-apps/broadcom-dx-saas-tutorial.md), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [SendPro Enterprise](../saas-apps/sendpro-enterprise-tutorial.md), [FortiSASE SIA](../saas-apps/fortisase-sia-tutorial.md)

Pode ainda encontrar a documentação de todas as aplicações aqui: https://aka.ms/AppsTutorial

Para listar a sua aplicação na galeria de aplicações Azure AD, leia os detalhes aqui: https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---march-2021"></a>Novos conectores de provisionamento na Galeria de Aplicações AD Azure - março de 2021

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Integração de Terceiros

Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:

- [AWS Single Sign-on](../saas-apps/aws-single-sign-on-provisioning-tutorial.md)
- [Estação Bpanda](../saas-apps/bpanda-provisioning-tutorial.md)
- [Britive](../saas-apps/britive-provisioning-tutorial.md)
- [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-provisioning-tutorial.md)
- [Grammarly](../saas-apps/grammarly-provisioning-tutorial.md)
- [LogicGate](../saas-apps/logicgate-provisioning-tutorial.md)
- [SecureLogin](../saas-apps/secure-login-provisioning-tutorial.md)
- [TravelPerk](../saas-apps/travelperk-provisioning-tutorial.md)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte [automatizar o fornecimento de utilizadores para aplicações SaaS com Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="introducing-ms-graph-api-for-company-branding"></a>Apresentando API de Gráfico MS para Branding da Empresa

**Tipo:** Alteração de recurso  
**Categoria de serviço:** MS Graph  
**Capacidade do produto:** B2B/B2C

[A MS Graph API para a Marca da Empresa](https://docs.microsoft.com/graph/api/resources/organizationalbrandingproperties)  está disponível para a experiência de login Azure AD ou Microsoft 365 para permitir a gestão dos parâmetros de marca programáticamente.

---

### <a name="general-availability---header-based-authentication-sso-with-application-proxy"></a>Disponibilidade geral - SSO de autenticação baseada em cabeçalho com Proxy de aplicação

**Tipo:** Alteração de recurso  
**Categoria de serviço:** App Proxy  
**Capacidade do produto:** Controlo de Acessos
 
O suporte nativo da aplicação Azure AD para autenticação baseada em cabeçalho está agora disponível em geral. Com esta funcionalidade, pode configurar os atributos do utilizador necessários como cabeçalhos HTTP para a aplicação sem componentes adicionais necessários para implementar. [Saiba mais](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md).

---

### <a name="two-way-sms-for-mfa-server-is-no-longer-supported"></a>SMS bidireccionados para O Servidor MFA já não é suportado

**Tipo:** Precatado  
**Categoria de serviço:** MFA  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 

O SMS bidireccionado para O MFA Server foi originalmente depreciado em 2018, e não será suportado após 24 de fevereiro de 2021. Os administradores devem permitir outro método para os utilizadores que ainda utilizam SMS bidirecionais.

As notificações por email e as notificações do Serviço de Saúde do Portal Azure foram enviadas aos administradores afetados nos dias 8 de dezembro de 2020 e 28 de janeiro de 2021. Os alertas foram para as funções de Proprietário, Coproprietário, Administrador e Administrador de Serviços RBAC ligadas às subscrições. [Saiba mais](../authentication/how-to-authentication-two-way-sms-unsupported.md).
 
---
 
## <a name="february-2021"></a>Fevereiro de 2021

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>E-mail autenticação de código de acesso único por padrão a partir de outubro de 2021

**Tipo:** Plano de mudança  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 

A partir de 31 de outubro de 2021, a [autenticação de código de acesso do](../external-identities/one-time-passcode.md) Microsoft Azure Ative Directory passará a ser o método padrão para convidar contas e inquilinos para cenários de colaboração B2B. Neste momento, a Microsoft deixará de permitir o resgate de convites utilizando contas não administradas do Azure Ative Directory. 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>Permissões não recíprocas mas consentidas deixarão de ser adicionadas a fichas se desencadearem o Acesso Condicional

**Tipo:** Plano de mudança  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Plataforma
 
Atualmente, as aplicações que usam [permissões dinâmicas](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent) recebem todas as permissões que são consentidas em aceder. Isto inclui aplicações que não são recíprocas e mesmo que desencadeiem o acesso condicional. Por exemplo, isto pode fazer com que uma aplicação que solicite apenas `user.read` que também tenha consentimento `files.read` para, ser forçada a passar o Acesso Condicional atribuído para a `files.read` permissão. 

Para reduzir o número de pedidos de acesso condicional desnecessários, o Azure AD está a mudar a forma como os âmbitos não solicitados são fornecidos às aplicações. As aplicações apenas desencadearão o acesso condicional para a permissão que solicitam explicitamente. Para mais informações, leia [As novidades na autenticação.](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes)
 
---
 
### <a name="public-preview---use-a-temporary-access-pass-to-register-passwordless-credentials"></a>Pré-visualização do Público - Utilize um Passe de Acesso Temporário para registar credenciais sem palavras-passe

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** MFA  
**Capacidade do produto:** Proteção & de Segurança de Identidade

O Passe de Acesso Temporário é uma senha limitada que serve como credenciais fortes e permite o embarque de credenciais e recuperação sem palavras-passe quando um utilizador perdeu ou esqueceu o seu forte fator de autenticação (por exemplo, a chave de segurança FIDO2 ou o Microsoft Authenticator) e precisa de se inscrever para registar novos métodos de autenticação forte. [Saiba mais](../authentication/howto-authentication-temporary-access-pass.md).

---

### <a name="public-preview---keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>Pré-visualização pública - Mantenha-me assinado em (KMSI) na próxima geração de fluxos de utilizadores

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão da Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C

A próxima geração de fluxos de utilizadores B2C suporta agora a funcionalidade [keep me signed in (KMSI)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) que permite que os clientes prolonguem a duração da sessão para os utilizadores das suas aplicações web e nativas utilizando um cookie persistente.  a funcionalidade mantém a sessão ativa mesmo quando o utilizador fecha e reabre o navegador, e é revogada quando o utilizador assina.

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-msa-accounts"></a>Pré-visualização pública - Identidades Externas Self-Service Inscrição em AAD utilizando contas MSA

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
Os utilizadores externos poderão agora utilizar as Contas da Microsoft para iniciar súbência nas primeiras aplicações do AD e lob. [Saiba mais](../external-identities/self-service-sign-up-overview.md).

---

### <a name="public-preview---reset-redemption-status-for-a-guest-user"></a>Pré-visualização pública - Redefinir o estado de resgate para um utilizador convidado

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
Os clientes podem agora voltar a convidar os utilizadores de hóspedes externos existentes para redefinir o seu estado de resgate, o que permite que a conta de utilizador do hóspede permaneça sem que percam qualquer acesso. [Saiba mais](../external-identities/reset-redemption-status.md).
 
---

### <a name="public-preview---synchronization-provisioning-apis-now-support-application-permissions"></a>Pré-visualização pública - /sincronização (provisionamento) APIs agora suporta permissões de aplicação

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
Os clientes podem agora utilizar a aplicação.readwrite.ownedby como uma permissão de aplicação para chamar as APIs de sincronização. Note que este é apenas suportado para o provisionamento da Azure AD em aplicações de terceiros (por exemplo, AWS, Data Bricks, etc.). Atualmente não é suportado para provisão de RH (Workday / Successfactors) ou Cloud Sync (AD a Azure AD). [Saiba mais](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true).
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>Disponibilidade Geral - Papel integrado do administrador de política de autenticação

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** RBAC  
**Capacidade do produto:** Controlo de Acessos
 
Os utilizadores com esta função podem configurar a política de métodos de autenticação, as definições de MFA em todo o inquilino e a política de proteção de senhas. Esta função permite gerir as definições de Proteção de Palavras-Passe: configurações de bloqueio inteligente e atualização da lista de senhas proibidas personalizadas. [Saiba mais](../roles/permissions-reference.md#authentication-policy-administrator).

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>Disponibilidade geral - As coleções de utilizadores nas Minhas Apps já estão disponíveis!

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** As minhas apps  
**Capacidade do produto:** Experiências de utilizador final
 
Os utilizadores podem agora criar os seus próprios agrupamentos de aplicações no launcher da aplicação My Apps. Também podem reordenar e ocultar coleções partilhadas com elas pelo seu administrador. [Saiba mais](../user-help/my-apps-portal-user-collections.md).

---

### <a name="general-availability---autofill-in-authenticator"></a>Disponibilidade geral - Preenchimento automático em Autenticador

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** App autenticador microsoft  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
O Microsoft Authenticator fornece capacidades de autenticação multi-fator (MFA) e de gestão de contas, e agora também preencherá automaticamente senhas em sites e aplicações que os utilizadores visitam no seu telemóvel (iOS e Android). 

Para utilizar o preenchimento automático no Autenticador, os utilizadores precisam de adicionar a sua conta pessoal da Microsoft ao Autenticador e usá-la para sincronizar as suas palavras-passe. As contas de trabalho ou escola não podem ser usadas para sincronizar senhas neste momento. [Saiba mais](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins).

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>Disponibilidade geral - Convidar utilizadores internos para a colaboração B2B

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
Os clientes podem agora convidar os hóspedes internos a usar a colaboração B2B em vez de enviar um convite para uma conta interna existente. Isto permite que os clientes mantenham o ID do objeto do utilizador, UPN, membros do grupo e atribuições de aplicativos. [Saiba mais](../external-identities/invite-internal-users.md).

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>Disponibilidade Geral - Papel incorporado do administrador de nome de domínio

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** RBAC  
**Capacidade do produto:** Controlo de Acessos
 
Os utilizadores com esta função podem gerir (ler, adicionar, verificar, atualizar e eliminar) nomes de domínio. Também podem ler informações sobre utilizadores, grupos e aplicações, uma vez que estes objetos têm dependências de domínio. 

Para ambientes no local, os utilizadores com esta função podem configurar nomes de domínio para a federação para que os utilizadores associados sejam sempre autenticados no local. Estes utilizadores podem então assinar em serviços baseados em Azure com as suas senhas no local através de um único s-sign-on. As definições da Federação precisam de ser sincronizadas através do Azure AD Connect, pelo que os utilizadores também têm permissões para gerir o Azure AD Connect. [Saiba mais](../roles/permissions-reference.md#domain-name-administrator).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Novas Aplicações Federadas disponíveis na galeria AZure AD Application - fevereiro de 2021

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Em fevereiro de 2021, adicionámos 37 novas aplicações na nossa galeria app com o apoio da Federação:

[Extensão do Loop Messenger](https://loopworks.com/loop-flow-messenger/), [Adaptador AD Silverfort Azure,](http://www.silverfort.com/) [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [Nura Space](https://dashboard.nuraspace.com/login), [Yooz EU](https://eu1.getyooz.com/?kc_idp_hint=microsoft), [UXPressia,](https://uxpressia.com/users/sign-in)plataforma [de introdução pré e onboarding,](http://app.introdus.dk/login) [Happybot,](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All) [LeaksID,](https://app.leaksid.com/) [ShiftWizard](http://www.shiftwizard.com/), [PingFlow SSO](https://app.pingview.io/), [Swiftlane](https://admin.swiftlane.com/login), [Quasydoc SSO](https://www.quasydoc.eu/login), Conta [De Ouro Fenwick](https://businesscentral.dynamics.com/), [SeamlessDesk](https://www.seamlessdesk.com/login), [Learnsoft LMS & TMS](http://www.learnsoft.com/), [P-TH+](https://p-th.jp/), [myViewBoard](https://api.myviewboard.com/auth/microsoft/), Ponte [Tartabit IoT](https://bridge-us.tartabit.com/), [AKASHI](../saas-apps/akashi-tutorial.md), [Rewatch,](../saas-apps/rewatch-tutorial.md) [Zuddl,](../saas-apps/zuddl-tutorial.md) [Parkalot - Gestão de parques](../saas-apps/parkalot-car-park-management-tutorial.md)de estacionamento, [HSB ThoughtSpot,](../saas-apps/hsb-thoughtspot-tutorial.md) [IBMid,](../saas-apps/ibmid-tutorial.md) [SharingCloud,](../saas-apps/sharingcloud-tutorial.md) [PoolParty Semantic Suite,](../saas-apps/poolparty-semantic-suite-tutorial.md) [GlobeSmart,](../saas-apps/globesmart-tutorial.md)Samsung [Knox e Business Services](../saas-apps/samsung-knox-and-business-services-tutorial.md) [,](../saas-apps/klaxoon-saml-tutorial.md) [Penji,](../saas-apps/penji-tutorial.md) [Kendis- Scaling Agile Platform,](../saas-apps/kendis-scaling-agile-platform-tutorial.md) [Maptician,](../saas-apps/maptician-tutorial.md) [Olfeo SAAS,](../saas-apps/olfeo-saas-tutorial.md) [Sigma Computing,](../saas-apps/sigma-computing-tutorial.md) [CloudK](../saas-apps/cloudknox-permissions-management-platform-tutorial.md) [](../saas-apps/enablon-tutorial.md)

Pode ainda encontrar a documentação de todas as aplicações aqui: https://aka.ms/AppsTutorial

Para listar a sua aplicação na galeria de aplicações Azure AD, leia os detalhes aqui: https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Novos conectores de provisionamento na Galeria de Aplicações AZure AD - fevereiro de 2021

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Integração de Terceiros
 

Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [OláID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Precite](../saas-apps/preciate-provisioning-tutorial.md)

Para mais informações, leia [o fornecimento de utilizadores da Automamate para aplicações SaaS com Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>Disponibilidade Geral - 10 funções de Diretório Ativo Azure agora renomeado

**Tipo:** Alteração de recurso  
**Categoria de serviço:** RBAC  
**Capacidade do produto:** Controlo de Acessos
 
10 As funções AD AD foram renomeadas para que estejam alinhadas através do [centro de administração microsoft 365](/microsoft-365/admin/microsoft-365-admin-center-preview), [portal AD AD Azure](https://portal.azure.com/)e [Microsoft Graph](https://developer.microsoft.com/graph/). Para saber mais sobre as novas funções, consulte as [permissões de função de Administrador no Azure Ative Directory](../roles/permissions-reference.md#all-roles).

![Tabela mostrando nomes de papéis na MS Graph API e no portal Azure, e o nome final proposto através da API, portal Azure e Mac.](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>Nova marca de empresa em Registo Combinado MFA/SSPR

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Experiência e Gestão do Utilizador  
**Capacidade do produto:** Experiências de utilizador final
 
No passado, os logótipos da empresa não eram usados nas páginas de inscrição do Azure Ative Directory. A marca da empresa está agora localizada no canto superior esquerdo do Registo Combinado MFA/SSPR. A marca da empresa também está incluída na página My Sign-Ins e na página Security Info. [Saiba mais](../fundamentals/customize-branding.md).

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>Disponibilidade Geral - Gestor de segundo nível pode ser definido como aprovador alternativo

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Gestão do Acesso ao Utilizador  
**Capacidade do produto:** Gestão de Direitos
 
Uma opção extra quando seleciona aprovadores está agora disponível na Gestão de Direitos. Se selecionar "Manager como aprovador" para o Primeiro Aprovador, terá outra opção, "Gestor de segundo nível como aprovador alternativo", disponível para escolher no campo de aprovação alternativa. Se selecionar esta opção, tem de adicionar um aprovador de recuo para encaminhar o pedido para o caso de o sistema não encontrar o gestor de segundo nível. [Saiba mais](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).
 
---

### <a name="authentication-methods-activity-dashboard"></a>Painel de atividade dos métodos de autenticação

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Relatório de & de Monitorização
 

O painel de atividades de autenticação atualizado dá aos administradores uma visão geral do registo do método de autenticação e da atividade de utilização no seu inquilino. O relatório resume o número de utilizadores registados para cada método e também quais os métodos utilizados durante a entrada e a redefinição da palavra-passe. [Saiba mais](../authentication/howto-authentication-methods-activity.md).
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>Refresh and session token lifetimes configurability in Confible Token Lifetime (CTL) são aposentados

**Tipo:** Precatado  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Autenticação do Utilizador
 
Atualização e a configurabilidade das vidas simbólicas de sessão na CTL são reformadas. O Azure Ative Directory já não honra a configuração de atualização e símbolo de sessão nas políticas existentes. [Saiba mais](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens).
 
---
 
## <a name="january-2021"></a>Janeiro de 2021

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>O token secreto será um campo obrigatório ao configurar o provisionamento

**Tipo:** Plano de mudança  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida da identidade

No passado, o campo simbólico secreto poderia ser mantido vazio ao configurar o provisionamento na aplicação personalizada/BYOA. Esta função destinava-se a ser utilizada exclusivamente para testes. Vamos atualizar a UI para tornar o campo necessário. 

Os clientes podem trabalhar em torno deste requisito para fins de teste usando uma bandeira de recurso no URL do navegador. [Saiba mais](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery).
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-frontline-workers-at-scale"></a>Antevisão Pública - Personalize e configuure dispositivos partilhados android para trabalhadores da linha da frente em escala

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Registo e Gestão de Dispositivos  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
As equipas AZure AD e Microsoft Endpoint Manager combinaram-se para trazer a capacidade de personalizar, escalar e proteger os seus dispositivos de trabalho de primeira linha.

As seguintes capacidades de pré-visualização permitir-lhe-ão:
- Provisão de dispositivos partilhados Android em escala com Microsoft Endpoint Manager
- Garantir o seu acesso a trabalhadores por turnos utilizando acesso condicional baseado em dispositivos
- Personalize experiências de inscrição para os trabalhadores por turnos com ecrã doméstico gerido

Para saber mais, consulte [personalizar e configurar dispositivos partilhados para trabalhadores da linha da frente em escala.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708)

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>Pré-visualização pública - Os registos de provisionamento podem agora ser descarregados como CSV ou JSON

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida da identidade

Os clientes podem descarregar os registos de provisionamento como um ficheiro CSV ou JSON através da UI e através da API de gráfico. Para saber mais, consulte os [relatórios de Provisioning no portal Azure Ative Directory](../reports-monitoring/concept-provisioning-logs.md).

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>Pré-visualização pública - Atribuir grupos de nuvem a funções personalizadas AZure AD e funções de unidade de administração

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** RBAC  
**Capacidade do produto:** Controlo de Acessos
 
Os clientes podem atribuir um grupo de nuvem a funções personalizadas Azure AD ou uma função de unidade de administração. Para aprender a usar esta funcionalidade, consulte use [grupos de nuvem para gerir atribuições de funções no Azure Ative Directory](../roles/groups-concept.md).

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>Disponibilidade Geral - Azure AD Connect cloud sync (anteriormente conhecido como provisionamento em nuvem)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Sincronização de nuvem Azure AD Connect  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
A azure AD Connect cloud sync está agora geralmente disponível para todos os clientes.

A nuvem Azure AD Connect move o pesado levantamento da lógica de transformação para a nuvem, reduzindo a sua pegada no local. Além disso, várias implementações de agentes leves estão disponíveis para uma maior disponibilidade de sincronização. [Saiba mais](https://aka.ms/cloudsyncGA).
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>Disponibilidade Geral - Administrador de Simulação de Ataque e Funções de Payload Author incorporadas

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** RBAC  
**Capacidade do produto:** Controlo de Acessos
 
Estão disponíveis duas novas funções no Role-Based Access Control para atribuir aos utilizadores, Administrador de simulação de Ataque e autor de Payload de Ataque. 

Os utilizadores na função [de Administrador de Simulação de Ataque](../roles/permissions-reference.md#attack-simulation-administrator) têm acesso a todas as simulações no arrendatário e podem:
- criar e gerir todos os aspetos da criação de simulação de ataque
- lançamento/agendamento de uma simulação
-  rever os resultados da simulação. 

Os utilizadores na função de Autor de [Carga útil de ataque](../roles/permissions-reference.md#attack-payload-author) podem criar cargas de ataque, mas não realmente lançá-las ou programar. As cargas de ataque estão então disponíveis para todos os administradores do inquilino que podem usá-los para criar uma simulação.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>Disponibilidade Geral - Função de leitor de relatórios de resumo de utilização

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** RBAC  
**Capacidade do produto:** Controlo de Acessos
 
Os utilizadores com a função De Leitore de Relatórios De Resumo de Utilização podem aceder a dados agregados ao nível do inquilino e insights associados no Microsoft 365 Admin Center for Usage and Productivity Score. No entanto, não podem aceder a quaisquer detalhes ou insights de nível de utilizador. 

No Microsoft 365 Admin Center para os dois relatórios, diferenciamos entre dados agregados ao nível do inquilino e detalhes do nível de utilizador. Esta função adiciona uma camada extra de proteção a dados identificáveis individuais do utilizador. [Saiba mais](../roles/permissions-reference.md#usage-summary-reports-reader).

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>Disponibilidade geral - Requer subsídio de política de proteção de aplicações no Acesso Condicional Azure AD

**Tipo:** Novo Recurso  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
A bolsa de acesso condicional Azure AD para "Require App Protection Policy" é agora GA. 

A política fornece as seguintes capacidades:
- Só permite o acesso quando se utiliza uma aplicação móvel que suporta a proteção da Aplicação Intune
- Só permite o acesso quando um utilizador tem uma política de proteção de aplicações Intune entregue na aplicação móvel

Saiba mais sobre como configurar uma política de acesso condicional para proteção de aplicações [aqui.](../conditional-access/app-protection-based-conditional-access.md)
 
---

### <a name="general-availability---email-one-time-passcode"></a>Disponibilidade geral - Email One-Time Passcode

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
O Email OTP permite que organizações de todo o mundo colaborem com qualquer pessoa, enviando um link ou convite por e-mail. Os utilizadores convidados podem verificar a sua identidade com a senha única enviada para o seu e-mail para aceder aos recursos do parceiro. [Saiba mais](../external-identities/one-time-passcode.md). 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Novos conectores de provisionamento na Galeria de Aplicações AD Azure - janeiro de 2021

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Integração de Terceiros
 
Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Ligação Templafy OpenID](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

Para obter mais informações, consulte [o que é o fornecimento automatizado de utilizadores de aplicações SaaS em Azure AD?](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Novas Aplicações Federadas disponíveis na galeria AZure AD Application - janeiro de 2021

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros

Em janeiro de 2021, adicionámos 29 novas aplicações na nossa galeria app com o apoio da Federação:

[mySCView](https://dev.myscview.com/), [Talentech](https://talentech.com/contact/), [Bipsync](https://www.bipsync.com/), [OroTimesheet](https://app.orotimesheet.com/login.php), [Mio](https://app.m.io/auth/install/microsoft?scopetype=hub), [Sovelto Easy](https://login.soveltoeasy.fi/), [Supportbench](https://account.supportbench.net/agent/login/),[Bienvenue Formation](https://formation.bienvenue.pro/login), [AIDA Healthcare SSO](https://aidaforparents.com/login/organizations), [International SOS Assistance Products,](../saas-apps/international-sos-assistance-products-tutorial.md) [NAVEX One,](../saas-apps/navex-one-tutorial.md) [LabLog,](../saas-apps/lablog-tutorial.md) [Oktopost SAML,](../saas-apps/oktopost-saml-tutorial.md) [EPHOTO DAM](../saas-apps/ephoto-dam-tutorial.md), [Notion,](../saas-apps/notion-tutorial.md) [Syndio](../saas-apps/syndio-tutorial.md), [Yello Enterprise](../saas-apps/yello-enterprise-tutorial.md), Relógio [365 SAML](../saas-apps/timeclock-365-saml-tutorial.md), [Nalco E-data,](https://www.ecolab.com/) [Preenchimento de Vagas,](https://app.vacancy-filler.co.uk/VFMVC/Account/Login)Ecossistema de Crescimento [Synerise AI,](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md) [Imperva Data Security,](../saas-apps/imperva-data-security-tutorial.md) [Redes Ilustivas,](../saas-apps/illusive-networks-tutorial.md) [Proware,](../saas-apps/proware-tutorial.md) [Visitante Splan,](../saas-apps/splan-visitor-tutorial.md) [Aruba User Experience Insight,](../saas-apps/aruba-user-experience-insight-tutorial.md) [Contentsquare SSO,](../saas-apps/contentsquare-sso-tutorial.md) [Perimeter 81](../saas-apps/perimeter-81-tutorial.md), [Burp Suite Enterprise Edition](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

Também pode encontrar a documentação de todas as aplicações a partir daqui https://aka.ms/AppsTutorial

Para listar a sua aplicação na galeria de aplicações Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Pré-visualização pública - Gestor de segundo nível pode ser definido como aprovador alternativo

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Gestão do Acesso ao Utilizador  
**Capacidade do produto:** Gestão de Direitos
 
Uma opção extra quando seleciona aprovadores está agora disponível na Gestão de Direitos. Se selecionar "Manager como aprovador" para o Primeiro Aprovador, terá outra opção, "Gestor de segundo nível como aprovador alternativo", disponível para escolher no campo de aprovação alternativa. Se selecionar esta opção, tem de adicionar um aprovador de recuo para encaminhar o pedido para o caso de o sistema não encontrar o gestor de segundo nível. [Saiba mais](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>Disponibilidade geral - Navegue para equipas diretamente do portal My Access

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Gestão do Acesso ao Utilizador  
**Capacidade do produto:** Gestão de Direitos
 
Agora pode lançar Equipas diretamente a partir do portal My Access. 

Para tal, faça o sôm.º do Meu Acesso ( https://myaccess.microsoft.com/) navegue para "Pacotes de Acesso", em seguida, vá ao separador "Ative" para ver todos os pacotes de acesso a que já tem acesso. Quando expandir o pacote de acesso selecionado e pairar sobre o Teams, pode lançá-lo clicando no botão "Abrir". [Saiba mais](../governance/entitlement-management-request-access.md).
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>Solicitações de & End-User de registo melhoradas para utilizadores de hóspedes arriscados

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 

As solicitações de registo e End-User para utilizadores de hóspedes arriscados foram atualizadas. Saiba mais em [Proteção de Identidade e utilizadores B2B.](../identity-protection/concept-identity-protection-b2b.md)
 
---
 
## <a name="december-2020"></a>Dezembro de 2020

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Pré-visualização pública - Azure AD B2C Telefone Inscrição e Inscrição usando a Política Incorporada

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão da Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C
 
O B2C Phone's Sign-up e o 'Iniciar sing's' utilizando a Política Incorporada permitem aos administradores de TI e desenvolvedores de organizações permitir que os seus utilizadores finais se inscrevam e se inscrevam usando um número de telefone nos fluxos dos utilizadores. Leia [Configurar a inscrição no telefone e iniciar sômposições para os fluxos do utilizador (pré-visualização)](../../active-directory-b2c/phone-authentication-user-flows.md) para saber mais.

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Disponibilidade Geral - Incumprimentos de segurança agora ativados para todos os novos inquilinos por padrão

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
Para proteger as contas dos utilizadores, todos os novos inquilinos criados em ou depois de 12 de novembro de 2020, virão com Incumprimentos de Segurança ativados. O Defaults de Segurança aplica múltiplas políticas, incluindo:
- Exige que todos os utilizadores e administradores se registem para MFA usando a App autenticador da Microsoft
- Requer funções de administração crítica para usar MFA cada vez que se inscrevem. Todos os outros utilizadores serão solicitados para MFA sempre que necessário. 
- A autenticação do legado será bloqueada por todo o lado. 

Para mais informações, leia [O que são falhas de segurança?](../fundamentals/concept-fundamentals-security-defaults.md)

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Disponibilidade geral - Apoio a grupos com até 250K membros em AADConnect

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Conexão de anúncios  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
A Microsoft implementou um novo ponto final (API) para o Azure AD Connect que melhora o desempenho das operações de serviço de sincronização para o Azure Ative Directory. Quando utilizar o novo [ponto final V2,](../hybrid/how-to-connect-sync-endpoint-api-v2.md)sentirá ganhos de desempenho notáveis na exportação e importação para a Azure AD. Este novo ponto final suporta os seguintes cenários:

- Grupos sincronizados com até 250 mil membros
- Ganhos de desempenho na exportação e importação para a Azure AD

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Disponibilidade geral - Gestão de Direitos disponível para inquilinos na nuvem Azure China

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão do Acesso ao Utilizador  
**Capacidade do produto:** Gestão de Direitos
 

As capacidades de Gestão de Direitos estão agora disponíveis para todos os inquilinos na nuvem Azure China. Para obter informações, visite o nosso site [de documentação de governação de identidade.](https://docs.azure.cn/zh-cn/active-directory/governance/)

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Novos conectores de provisionamento na Galeria de Aplicações AD Azure - dezembro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Integração de Terceiros

Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:

- [Bizagi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut Cloud Print Management](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte [automatizar o fornecimento de utilizadores para aplicações SaaS com Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Novas Aplicações Federadas disponíveis na galeria AZure AD Application - dezembro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Em dezembro de 2020 adicionámos 18 novas aplicações na nossa galeria app com o apoio da Federação:

[AwareGo](../saas-apps/awarego-tutorial.md), [HowNow SSO](https://gethownow.com/), [ZyLAB ONE Legal Hold](https://www.zylab.com/en/product/legal-hold), [Guider](http://www.guider-ai.com/), [Softcrisis](https://www.softcrisis.se/sv/), [Pims 365](http://www.omega365.com/pims), [InformaCast,](../saas-apps/informacast-tutorial.md) [RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md), [vonage](../saas-apps/vonage-tutorial.md), [Count Me In - Operations Dashboard](../saas-apps/count-me-in-operations-dashboard-tutorial.md), [ProProfs Knowledge Base,](../saas-apps/proprofs-knowledge-base-tutorial.md) [RightCrowd Workforce Management,](../saas-apps/rightcrowd-workforce-management-tutorial.md) [JLL TRIRIGA,](../saas-apps/jll-tririga-tutorial.md) [Shutterstock,](../saas-apps/shutterstock-tutorial.md) [FortiWeb Web Application Firewall,](../saas-apps/linkedin-talent-solutions-tutorial.md) [LinkedIn Talent Solutions](../saas-apps/linkedin-talent-solutions-tutorial.md), [Equinix Federation App](../saas-apps/equinix-federation-app-tutorial.md), [KFAdvance](../saas-apps/kfadvance-tutorial.md)

Também pode encontrar a documentação de todas as aplicações a partir daqui https://aka.ms/AppsTutorial

Para listar a sua aplicação na galeria de aplicações Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Navegue diretamente para as equipas do portal My Access

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Capacidade do produto de gestão de acesso ao **utilizador:** Gestão de Direitos

Agora pode lançar Equipas diretamente do portal My Access. Para tal, faça o sôm.000 no [Meu Acesso](https://myaccess.microsoft.com/), navegue para **pacotes de acesso,** em seguida, vá ao Separador **Ativo** para ver todos os pacotes de acesso a que já tem acesso. Quando expandir o pacote de acesso e pairar sobre o Teams, pode lançá-lo clicando no botão **Iniciar.** 

Para saber mais sobre a utilização do portal My Access, aceda a [Um pacote de acesso na gestão de direitos Azure AD.](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal)

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Pré-visualização pública - Gestor de segundo nível pode ser definido como aprovador alternativo

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Gestão do Acesso ao Utilizador  
**Capacidade do produto:** Gestão de Direitos

Uma opção extra está agora disponível no processo de aprovação na Gestão de Direitos. Se selecionar o Manager como aprovador para o Primeiro Aprovação, terá outra opção, gestor de segundo nível como aprovador alternativo, disponível para escolher no campo de aprovação alternativa. Ao selecionar esta opção, tem de adicionar um aprovador de recuo para encaminhar o pedido para o caso de o sistema não encontrar o gestor de segundo nível.

Para mais informações, aceda às [definições de aprovação change para um pacote de acesso na gestão de direitos AD Azure](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).

--- 

## <a name="november-2020"></a>Novembro de 2020

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Ative Directory TLS 1.0, TLS 1.1 e 3DES depreciação

**Tipo:** Plano de mudança  
**Categoria de serviço:** Todas as aplicações AD da AZure  
**Capacidade do produto:** Normas

O Azure Ative Directory irá deprecação dos seguintes protocolos em regiões mundiais do Azure Ative Directory a partir de 30 de junho de 2021:

- TLS 1.0
- TLS 1.1
- Suíte cifra 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Os ambientes afetados são:
- Nuvem Comercial Azure
- Escritório 365 GCC e WW

Anúncio relacionado Todas as combinações de servidores de clientes e servidores de navegador devem usar os serviços TLS 1.2 e cifra modernas para manter uma ligação segura ao Azure Ative Directory para os serviços Azure, Office 365 e Microsoft 365. Esta mudança está relacionada com o [Azure Ative Directory TLS 1.0 & 1.1 e 3DES Cipher Suite Deprecation in US Gov Cloud](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud).

Para obter orientações para remover as dependências de protocolos dedeprecagem, consulte para [Ativar o suporte para TLS 1.2 no seu ambiente para a deprecção Azure AD TLS 1.1 e 1.0](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Novas Aplicações Federadas disponíveis na galeria AZure AD Application - novembro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros

Em novembro de 2020 adicionámos 52 novas aplicações na nossa galeria app com o apoio da Federação:

[Travel & Gestão de Despesas](https://app.expenseonce.com/Account/Login), [Tribeloo](../saas-apps/tribeloo-tutorial.md), Seu Selecionador de [Ficheiros,](https://pmteam.itslearning.com/)Controlo de Crises, Controlo de [Crises,](../saas-apps/crises-control-tutorial.md) [TribunalAlert,](https://www.courtalert.com/) [StealthMail](https://stealthmail.com/), [Edmentum - Ilha de Estudo,](https://app.studyisland.com/cfw/login/)Gestor de Risco [Virtual,](../saas-apps/virtual-risk-manager-tutorial.md) [TIMU,](../saas-apps/timu-tutorial.md)Plataforma [Looker Analytics,](../saas-apps/looker-analytics-platform-tutorial.md) [Talview - Recruta,](https://recruit.talview.com/login)Tradutor em Tempo Real, [Klaxoon](https://access.klaxoon.com/login), [](../saas-apps/jedox-tutorial.md) [Podbean](../saas-apps/podbean-tutorial.md), [zcal,](https://zcal.co/signup) [expensemanager](https://api.expense-manager.com/), [](../saas-apps/sailpoint-identitynow-tutorial.md) [Netsparker Enterprise](../saas-apps/netsparker-enterprise-tutorial.md), [](https://www.uipath.com/product/platform) [En-trak Tenant Experience Platform](https://portal.en-trak.app/), [Appian](../saas-apps/appian-tutorial.md), [Panorays](../saas-apps/panorays-tutorial.md),[](../saas-apps/resource-central-tutorial.md) [Builterra,](https://portal.builterra.com/) [EVA Check-in,](https://my.evacheckin.com/organization) [HowNow WebApp SSO,](../saas-apps/hownow-webapp-sso-tutorial.md) [](https://portal.brightbooking.eu/) [Coupa Risk Assess,](../saas-apps/coupa-risk-assess-tutorial.md) [Lucid (Todos os Produtos)](../saas-apps/lucid-tutorial.md) [,](../saas-apps/cequence-application-security-tutorial.md) [PerimeterX](../saas-apps/perimeterx-tutorial.md), [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [WorkWare](../saas-apps/workware-tutorial.md), [ProdPad](../saas-apps/prodpad-tutorial.md), [AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md), [AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum,](../saas-apps/luum-tutorial.md) [Freight Measure](https://www.gpcsl.com/freight.html), [Terraform Cloud](../saas-apps/terraform-cloud-tutorial.md), Nature [Research](../saas-apps/nature-research-tutorial.md), Play [Digital Signage,](https://login.playsignage.com/login) [RemotePC,](../saas-apps/remotepc-tutorial.md) [Prolorus,](../saas-apps/prolorus-tutorial.md) [Hirebridge ATS,](../saas-apps/hirebridge-ats-tutorial.md) [Teamgage,](https://www.teamgage.com/Account/ExternalLoginAzure) [Roadmunk,](../saas-apps/roadmunk-tutorial.md) [Sunrise Software Relations CRM,](https://cloud.relations-crm.com/) [Procaire,](../saas-apps/procaire-tutorial.md) [Mentor® por eDriving: Business,](https://www.edriving.com/) [Gradle Enterprise](https://gradle.com/)

Também pode encontrar a documentação de todas as aplicações a partir daqui https://aka.ms/AppsTutorial

Para listar a sua aplicação na galeria de aplicações Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Pré-visualização pública - Funções personalizadas para aplicações empresariais

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** RBAC  
**Capacidade do produto:** Controlo de Acessos
 
 [As funções personalizadas do RBAC para gestão de aplicações de empresas delegadas](../roles/custom-available-permissions.md) estão agora em pré-visualização pública. Estas novas permissões baseiam-se nas funções personalizadas para a gestão do registo de aplicações, o que permite um controlo fino sobre o acesso que os seus administradores têm. Com o tempo, serão libertadas permissões adicionais para delegar a gestão do Azure AD.

Alguns cenários comuns de delegação:
- atribuição de utilizadores e grupos que podem aceder a aplicações únicas de inscrição baseadas em SAML
- a criação de aplicações da Galeria AD Azure
- atualizar e ler as configurações básicas de SAML para aplicações de saturação única baseadas em SAML
- gestão de certificados de assinatura para pedidos de assinatura único baseados em SAML
- atualização do sinal de expiração em endereços de correio eletrónico de notificação de certificados para aplicações de inscrição única baseadas em SAML
- atualização da assinatura de token SAML e algoritmo de acesso para aplicações de sinalização única baseadas em SAML
- criar, eliminar e atualizar os atributos e pedidos de utilizador para aplicações únicas baseadas em SAML
- capacidade de ligar, desligar e reiniciar empregos de provisionamento
- atualizações para atribuir mapeamento
- capacidade de ler definições de provisionamento associadas ao objeto
- capacidade de ler definições de provisionamento associadas ao seu principal serviço
- capacidade de autorizar o acesso à aplicação para provisionamento

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Pré-visualização pública - Azure AD Application Proxy suporta de forma nativa o acesso de súm numa única súmia a aplicações que usam cabeçalhos para autenticação

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** App Proxy  
**Capacidade do produto:** Controlo de Acessos
 
Azure Ative Directory (Azure AD) Application Proxy suporta de forma nativa o acesso de assinatura única a aplicações que usam cabeçalhos para autenticação. Pode configurar os valores do cabeçalho exigidos pela sua aplicação no Azure AD. Os valores do cabeçalho serão enviados para a aplicação através do Application Proxy. Para saber mais, consulte o [sign-on único baseado em Header para aplicações no local com Proxy app AD App Azure](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Disponibilidade Geral - Azure AD B2C Registo telefónico e Inscrição usando a Política Personalizada

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão da Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C

Com a inscrição e a inscrição no número de telefone, os desenvolvedores e empresas podem permitir que os seus clientes se inscrevam e se inscrevam através de uma senha única enviada para o número de telefone do utilizador através de SMS. Esta funcionalidade também permite que o cliente altere o seu número de telefone se perder o acesso ao seu telemóvel. Com o poder das políticas personalizadas, permitir que os desenvolvedores e empresas comuniquem a sua marca através da personalização de página. Descubra como [configurar a inscrição no telefone e iniciar sôm-se com as políticas personalizadas em Azure AD B2C](../../active-directory-b2c/phone-authentication-user-flows.md).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Novos conectores de provisionamento na Galeria de Aplicações AD Azure - novembro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Integração de Terceiros
 
Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Tic - Tac Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Para obter mais informações, consulte [automatizar o fornecimento de utilizadores às aplicações do SaaS com Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Pré-visualização pública - E-mail Sign-In com ProxyAddresses agora implantáveis através do Rollout Encenado

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
Os administradores de inquilinos podem agora usar o Lançamento Encenado para implementar Sign-In de email com ProxyAddresses para grupos AD específicos do Azure. Isto pode ajudar ao experimentar a funcionalidade antes de a colocar em todo o inquilino através da política Home Realm Discovery. As instruções para a implementação de Sign-In de email com ProxyAddresses via Staged Rollout estão na [documentação](../authentication/howto-authentication-use-email-signin.md).
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Pré-visualização limitada - Diagnóstico de Inscrição

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Relatório de & de Monitorização
 
Com a versão inicial de pré-visualização do 'Diagnóstico de Insumin', os administradores podem agora rever as entradas do utilizador. Os administradores podem receber detalhes contextuais, específicos e relevantes e orientações sobre o que aconteceu durante uma sindução e como corrigir problemas. O diagnóstico está disponível tanto no nível AD AZure, como nas lâminas de diagnóstico e resolução de acesso condicional. Os cenários de diagnóstico abrangidos por esta versão são Acesso Condicional, Autenticação Multi-Factor e início de sação bem-sucedido.

Para mais informações, veja [o que é o diagnóstico de inscrição no Azure AD?](../reports-monitoring/overview-sign-in-diagnostics.md)
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Propriedades de inscrição desconhecida melhoradas

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Proteção & de Segurança de Identidade

  Foram atualizadas as deteções de propriedades de insusitadas. Os clientes podem notar mais deteções de propriedades de alto risco desconhecidas. Para mais informações, veja [o que é o risco?](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>Pré-visualização pública do agente cloud provisioning já disponível (Versão: 1.1.281.0)

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Azure AD Cloud Provisioning  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
O agente de provisionamento em nuvem foi lançado em pré-visualização pública e está agora disponível através do portal. Esta versão contém várias melhorias, incluindo suporte para GMSA para os seus domínios, que proporciona uma melhor segurança, ciclos de sincronização iniciais melhorados e suporte para grandes grupos. Confira o [histórico](../app-provisioning/provisioning-agent-release-version-history.md) da versão de lançamento para mais detalhes. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>BitLocker chave de recuperação ponto final da API agora em /informationProtection

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Gestão de Acesso a Dispositivos  
**Capacidade do produto:** Gestão do ciclo de vida do dispositivo
 
Anteriormente, pode recuperar as teclas BitLocker através do ponto final /bitlocker. Acabaremos por depregrir este ponto final, e os clientes devem começar a consumir a API que agora se enquadra na /informationProtection. 

Consulte [a API de recuperação do BitLocker](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) para obter atualizações da documentação para refletir estas alterações.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Disponibilidade Geral de Suporte de Procuração de Aplicações para Serviços remotos de Desktop HTML5 Cliente Web

**Tipo:** Alteração de recurso  
**Categoria de serviço:** App Proxy  
**Capacidade do produto:** Controlo de Acessos
 
O suporte de procuração de aplicativos Azure AD para serviços de desktop remoto (RDS) está agora na Disponibilidade Geral. O cliente web RDS permite que os utilizadores acedam à infraestrutura de Ambiente de Trabalho Remoto através de qualquer navegador HTLM5 capaz de ser como o Microsoft Edge, o Internet Explorer 11, o Google Chrome, e assim por diante. Os utilizadores podem interagir com aplicações remotas ou desktops como fariam com um dispositivo local de qualquer lugar. 

Ao utilizar o Azure AD Application Proxy, pode aumentar a segurança da sua implementação de RDS, aplicando políticas de pré-autenticação e acesso condicional para todos os tipos de aplicações de clientes ricos. Para saber mais, consulte [publicar o Desktop Remoto com o Azure AD Application Proxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Novo serviço do Grupo Dinâmico melhorado está em Visualização Pública

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração
 
O serviço de grupo dinâmico melhorado está agora na Visualização Pública. Novos clientes que criam grupos dinâmicos nos seus inquilinos vão utilizar o novo serviço. O tempo necessário para criar um grupo dinâmico será proporcional ao tamanho do grupo que está a ser criado em vez do tamanho do arrendatário. Esta atualização melhorará significativamente o desempenho dos grandes inquilinos quando os clientes criarem grupos mais pequenos. 

O novo serviço também pretende completar a adição e remoção dos membros devido a alterações de atributos dentro de poucos minutos. Além disso, falhas de processamento únicas não bloqueiam o processamento de inquilinos. Para saber mais sobre a criação de grupos dinâmicos, consulte a nossa [documentação.](../enterprise-users/groups-create-rule.md)
 
---
## <a name="october-2020"></a>Outubro de 2020

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Agentes híbridos Azure AD impactados por alterações no certificado Azure TLS

**Tipo:** Plano de mudança  
**Categoria de serviço:** N/A  
**Capacidade do produto:** Plataforma

A Microsoft está a atualizar os serviços da Azure para utilizar certificados TLS de um conjunto diferente de Autoridades de Certificados De Raiz (CAs). Esta atualização deve-se ao facto de os certificados ac atuais não cumprirem um dos requisitos de Base do Fórum CA/Browser. Esta alteração terá impacto nos agentes híbridos AD AD instalados no local que têm ambientes endurecidos com uma lista fixa de certificados de raiz e terão de ser atualizados para confiar nos novos emitentes de certificados.

Esta alteração resultará em interrupção do serviço se não tomar medidas imediatamente. Estes agentes incluem [conectores Proxy de aplicação](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) para acesso remoto a instalações, agentes [de autenticação passthrough](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) que permitem que os seus utilizadores inscrevam-se em aplicações usando as mesmas palavras-passe, e agentes [de pré-visualização de Provisionamento](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) em Nuvem que executam sincronização AD a AZure AD. 

Se tiver um ambiente com regras de firewall definido para permitir chamadas de saída apenas para download específico da Lista de Revogação de Certificados (CRL), terá de permitir os urls CRL e OCSP seguintes. Para obter informações completas sobre a alteração e os URLs CRL e OCSP para permitir o acesso, consulte as alterações do  [certificado Azure TLS](../../security/fundamentals/tls-certificate-changes.md).

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Os eventos de provisionamento serão removidos dos registos de auditoria e publicados unicamente para o provisionamento de registos

**Tipo:** Plano de mudança  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Relatório de & de Monitorização
 
A atividade do [serviço de prestação](../app-provisioning/user-provisioning.md) scim é registada tanto nos registos de auditoria como nos registos de provisionamento. Isto inclui atividades como a criação de um utilizador no ServiceNow, grupo em GSuite, ou importação de um papel da AWS. No futuro, estes eventos só serão publicados nos registos de provisionamento. Esta alteração está a ser implementada para evitar eventos duplicados em registos, e custos adicionais incorridos pelos clientes que consomem os registos em análise de registos. 

Providenciaremos uma atualização quando uma data estiver concluída. Esta depreciação não está prevista para o ano civil de 2020. 

> [!NOTE]
> Isto não afeta quaisquer eventos nos registos de auditoria fora dos eventos de sincronização emitidos pelo serviço de fornecimento. Eventos como a criação de uma aplicação, política de acesso condicional, um utilizador no diretório, etc. continuarão a ser emitidos nos registos de auditoria. [Saiba mais](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Agentes híbridos Azure AD nas instalações impactados pela segurança da camada de transporte Azure (TLS) Alterações de certificados

**Tipo:** Plano de mudança  
**Categoria de serviço:** N/A  
**Capacidade do produto:** Plataforma
 
A Microsoft está a atualizar os serviços da Azure para utilizar certificados TLS de um conjunto diferente de Autoridades de Certificados De Raiz (CAs). Haverá uma atualização devido aos certificados de CA atuais que não seguem um dos requisitos de Base do Fórum CA/Browser. Esta alteração terá impacto nos agentes híbridos AD AD instalados no local que têm ambientes endurecidos com uma lista fixa de certificados de raiz. Estes agentes terão de ser atualizados para confiar nos novos emitentes de certificados.

Esta alteração resultará em interrupção do serviço se não tomar medidas imediatamente. Estes agentes incluem: 
- [Conectores Proxy de aplicação](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) para acesso remoto a instalações 
- [Agentes de autenticação passthrough](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) que permitem aos seus utilizadores iniciar súmicos nas aplicações usando as mesmas palavras-passe
- [Agentes de pré-visualização de provisionamento em nuvem](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) que fazem sincronização AD a Azure AD. 

Se tiver um ambiente com regras de firewall definidas para permitir chamadas de saída apenas para download específico da Lista de Revogação de Certificados (CRL), terá de permitir URLs CRL e OCSP. Para obter informações completas sobre a alteração e os URLs CRL e OCSP para permitir o acesso, consulte as alterações do  [certificado Azure TLS](../../security/fundamentals/tls-certificate-changes.md).
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Ative Directory TLS 1.0, TLS 1.1 e 3DES Deprecation in US Gov Cloud

**Tipo:** Plano de mudança  
**Categoria de serviço:** Todas as aplicações AD da AZure  
**Capacidade do produto:** Normas
 
O Azure Ative Directory irá deprecação dos seguintes protocolos a partir de 31 de março de 2021:
- TLS 1.0
- TLS 1.1
- Suíte cifra 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Todas as combinações de servidores de clientes e servidores de navegador devem utilizar os serviços TLS 1.2 e cifra modernas para manter uma ligação segura ao Azure Ative Directory para os serviços Azure, Office 365 e Microsoft 365.

Os ambientes afetados são:
- Azure US Gov
- [Escritório 365 GCC High & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)

Para obter orientações para remover as dependências de protocolos dedeprecagem, consulte para [Ativar o suporte para TLS 1.2 no seu ambiente para a deprecção Azure AD TLS 1.1 e 1.0](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment).
 
---

### <a name="assign-applications-to-roles-on-administrative-unit-and-object-scope"></a>Atribuir candidaturas a funções na unidade administrativa e no âmbito do objeto

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** RBAC  
**Capacidade do produto:** Controlo de Acessos
 
Esta funcionalidade permite atribuir uma aplicação (SPN) a uma função de administrador no âmbito da unidade administrativa. Para saber mais, consulte [atribuir funções a uma unidade administrativa](../roles/admin-units-assign-roles.md).

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Agora pode desativar e eliminar utilizadores convidados quando lhes é negado o acesso a um recurso

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Comentários de Acesso  
**Capacidade do produto:** Governação da Identidade
 
Desativar e eliminar é um controlo avançado em Azure AD Access Reviews para ajudar as organizações a gerir melhor os hóspedes externos em Grupos e Apps. Se os hóspedes forem negados numa revisão de acesso, **desative e apagará** automaticamente bloqueará a sua inscrição durante 30 dias. Depois de 30 dias, serão removidos do inquilino completamente.

Para obter mais informações sobre esta funcionalidade, consulte [Desativar e eliminar identidades externas com avaliações de acesso AD Azure](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Os criadores do Access Review podem adicionar mensagens personalizadas em e-mails aos revisores

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Comentários de Acesso  
**Capacidade do produto:** Governação da Identidade
 
Nas avaliações de acesso a AD do Azure, os administradores que criam comentários podem agora escrever uma mensagem personalizada aos revisores. Os revisores verão a mensagem no e-mail que recebem e que os leva a completar a revisão. Para saber mais sobre a utilização desta funcionalidade, consulte o passo 14 da secção [de comentários de acesso Create.](../governance/create-access-review.md#create-one-or-more-access-reviews)

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Novos conectores de provisionamento na Galeria de Aplicações AD AZure - outubro 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Integração de Terceiros
 
Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Gestor Escolar da Apple](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Global Relay Identity Sync](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte [automatizar o fornecimento de utilizadores para aplicações SaaS com Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Assistente de integração do Azure AD B2C

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão da Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C
 
A experiência do Assistente de Integração (pré-visualização) já está disponível para inscrições da App Azure AD B2C. Esta experiência ajuda a guiá-lo na configuração da sua aplicação para cenários comuns.. Saiba mais sobre as [melhores práticas e recomendações da plataforma de identidade da Microsoft.](../develop/identity-platform-integration-checklist.md)
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Ver ID do modelo de função no portal Azure UI

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Papéis azuis  
**Capacidade do produto:** Controlo de Acessos
 

Agora pode ver o ID do modelo de cada papel AD Azure no portal Azure. Em Azure AD, selecione  **a descrição** da função selecionada. 

Recomenda-se que os clientes utilizem IDs de modelo de função no seu script e código PowerShell, em vez do nome de exibição. O ID do modelo de função é suportado para uso para [diretórios](/graph/api/resources/directoryrole) e [funDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta&preserve-view=true) objects. Para obter mais informações sobre os IDs do modelo de funções, consulte [as funções incorporadas AD AD do Azure](../roles/permissions-reference.md).

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>Os conectores API para fluxos de utilizadores de inscrição Azure AD B2C estão agora em pré-visualização pública

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão da Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C
 

Os conectores API estão agora disponíveis para utilização com o Azure Ative Directory B2C. Os conectores API permitem-lhe utilizar APIs web para personalizar os fluxos de utilizador de inscrição e integrar-se com sistemas de nuvem externa. Pode utilizar conectores API para:

- Integre-se com fluxos de trabalho de aprovação personalizadas
- Validar dados de entrada de utilizador
- Atributos de utilizador sobrepor-se 
- Executar lógica de negócio personalizada 

 Visite os [conectores API de utilização para personalizar e estender](../../active-directory-b2c/api-connectors-overview.md) a documentação de inscrição para saber mais.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>Propriedade do Estado para organizações ligadas na gestão de direitos

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Capacidade do produto de gestão do **diretório:** Gestão de Direitos
 

 Todas as organizações ligadas terão agora uma propriedade adicional chamada "Estado". O Estado controlará a forma como a organização conectada será usada em políticas que se refiram a "todas as organizações conectadas configuradas". O valor será "configurado" (o que significa que a organização está no âmbito das políticas que usam a cláusula "tudo") ou "proposta" (o que significa que a organização não está no âmbito).  

As organizações conectadas criadas manualmente terão uma definição padrão de "configurado". Entretanto, as criadas automaticamente (criadas através de políticas que permitem a qualquer utilizador da internet solicitar acesso) por defeito ao "proposto".  Quaisquer organizações ligadas criadas antes de 9 de setembro de 2020 serão definidas como "configuradas". Os administradores podem atualizar esta propriedade conforme necessário. [Saiba mais](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Ative Directy Identidades Externas tem agora configurações de segurança avançadas premium para B2C

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão da Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C
 
As funcionalidades de acesso condicional e de deteção de riscos de proteção de identidade baseadas em risco estão agora disponíveis no [Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Com estas funcionalidades avançadas de segurança, os clientes podem agora:
- Aproveite insights inteligentes para avaliar o risco com aplicações B2C e contas de utilizador final. As deteções incluem viagens atípicas, endereços IP anónimos, endereços IP ligados a malware e inteligência de ameaça AZure AD. Os relatórios baseados no Portal e na API também estão disponíveis.
- Resolver automaticamente os riscos configurando políticas de autenticação adaptativa para utilizadores de B2C. Os desenvolvedores e administradores de aplicações podem mitigar o risco em tempo real, exigindo a autenticação de vários fatores (MFA) ou bloqueando o acesso dependendo do nível de risco do utilizador detetado, com controlos adicionais disponíveis com base na localização, grupo e app.
- Integre com fluxos de utilizador AD B2C Azure e políticas personalizadas. As condições podem ser desencadeadas a partir de fluxos de utilizadores incorporados em Azure AD B2C ou podem ser incorporadas em políticas personalizadas B2C. Tal como acontece com outros aspetos do fluxo de utilizador B2C, as mensagens de experiência do utilizador final podem ser personalizadas. A personalização é de acordo com as alternativas de voz, marca e mitigação da organização.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Novas Aplicações Federadas disponíveis na galeria AZure AD Application - outubro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Em outubro de 2020 adicionámos 27 novas aplicações na nossa galeria app com o apoio da Federação:

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblebee - Productivity Superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture Cloud,](../saas-apps/abbyy-flexicapture-cloud-tutorial.md) [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys Cloud Integration for Azure](https://apps.mypurecloud.com/msteams-integration/), [Zone Technologies Portal](https://portail.zonetechnologie.com/signin), [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker,](https://console.datawiza.com/) [ZOKRI,](https://app.zokri.com/) [CheckProof](../saas-apps/checkproof-tutorial.md), [Ecochallenge.org,](https://events.ecochallenge.org/users/login) [atSpo lembrete](http://atspoke.com/login)de [nomeação](https://app.appointmentreminder.co.nz/account/login), [Cloud.Market](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [Greetly](https://app.greetly.com/), [OrgVitality SSO](../saas-apps/orgvitality-sso-tutorial.md), [Web Cargo Air](../saas-apps/web-cargo-air-tutorial.md), Loop Flow [CRM](../saas-apps/loop-flow-crm-tutorial.md), [Starmind,](../saas-apps/starmind-tutorial.md) [Workstem,](https://hrm.workstem.com/login) [Retail Zipline,](../saas-apps/retail-zipline-tutorial.md) [Hoxhunt,](../saas-apps/hoxhunt-tutorial.md) [MEVISIO,](../saas-apps/mevisio-tutorial.md) [Samsara,](../saas-apps/samsara-tutorial.md) [Nimbus,](../saas-apps/nimbus-tutorial.md) [Pulse Secure Virtual Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

Também pode encontrar a documentação de todas as aplicações a partir daqui https://aka.ms/AppsTutorial

Para listar a sua aplicação na galeria de aplicações Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>Os registos de provisionamento podem agora ser transmitidos para registar análises

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Relatório de & de Monitorização
 

Publique os seus registos de provisão para registar análises de forma a:
- Armazenar registos de fornecimento por mais de 30 dias
- Definir alertas e notificações personalizadas
- Construa dashboards para visualizar os registos
- Execute consultas complexas para analisar os registos 

Para aprender a usar a funcionalidade, consulte [como o provisionamento se integra com os registos do Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md).
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>Os registos de provisionamento podem agora ser vistos pelos proprietários de aplicações

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Relatório de & de Monitorização
 
Pode agora permitir que os proprietários de aplicações monitorizem a atividade através do serviço de fornecimento e problemas de resolução de problemas sem lhes proporcionar um papel privilegiado ou tornar a TI um estrangulamento. [Saiba mais](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>Renomear 10 funções de Diretório Ativo Azure

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Papéis azuis  
**Capacidade do produto:** Controlo de Acessos
 
Alguns papéis incorporados no Azure Ative Directory (AD) têm nomes que diferem dos que aparecem no Microsoft 365 admin center, no portal AD Azure e no Microsoft Graph. Esta inconsistência pode causar problemas em processos automatizados. Com esta atualização, estamos a renomear 10 nomes para torná-los consistentes. A tabela a seguir tem os novos nomes:

![Tabela mostrando nomes de papéis na MS Graph API e no portal Azure, e o novo nome de papel proposto no M365 Admin Center, portal Azure e API.](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Suporte Azure AD B2C para fluxo de código de auth para SPAs usando MSAL JS 2.x

**Tipo:** Alteração de recurso  
**Categoria de serviço:** B2C - Gestão da Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C
 
MSAL.js versão 2.x agora inclui suporte para o fluxo de código de autorização para aplicações web de página única (SPAs). O Azure AD B2C irá agora apoiar a utilização do tipo de aplicação SPA no portal Azure e a utilização de MSAL.js código de autorização com PKCE para aplicações de uma só página. Isto permitirá que os SPAs utilizem o Azure AD B2C para manter o SSO com navegadores mais recentes e respeitar as novas recomendações do protocolo de autenticação. Inicie com o [Registo uma aplicação de uma página (SPA) no tutorial Azure Ative Directory B2C.](../../active-directory-b2c/tutorial-register-spa.md)

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Atualizações para lembrar a autenticação multi-factor (MFA) numa definição de dispositivo fidedigno

**Tipo:** Alteração de recurso  
**Categoria de serviço:** MFA  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 

Atualizámos recentemente a [autenticação multi-factor (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) de uma funcionalidade de dispositivo fidedigna para prolongar a autenticação por um máximo de 365 dias. Licenças Azure Ative Directory (Azure AD) Licenças premium, também podem utilizar a [política de Acesso Condicional – Frequência de Acesso a Sinais](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) que proporciona mais flexibilidade para configurações de reautorização.

Para uma experiência ideal do utilizador, recomendamos a utilização da frequência de acesso condicional para prolongar as horas de sessão em dispositivos, locais ou sessões de baixo risco como alternativa ao MFA de remember numa definição de dispositivo de confiança. Para começar, reveja [as nossas últimas orientações sobre a otimização da experiência de reautorsa.](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)

---
