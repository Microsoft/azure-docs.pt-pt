---
title: Novidades? Notas de lançamento - Azure Ative Directory / Microsoft Docs
description: Saiba quais as novidades com o Azure Ative Directory, como as últimas notas de lançamento, problemas conhecidos, correções de bugs, funcionalidades preprecadas e próximas alterações.
services: active-directory
author: msaburnley
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac87bda4251a8414ef659ad486c989c0c1cd42e1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87034725"
---
# <a name="whats-new-in-azure-active-directory"></a>Quais as novidades no Diretório Ativo do Azure?

>Seja notificado sobre quando revisitar esta página para obter atualizações copiando e colando este URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` no leitor de feed feed feed do leitor de feed ![ RSS. ](./media/whats-new/feed-icon-16x16.png)

A Azure AD recebe melhorias continuadamente. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

- Os últimos lançamentos
- Problemas conhecidos
- Correções de erros
- Funcionalidade preterida
- Planos para alterações

Esta página é atualizada mensalmente, por isso revisite-a regularmente. Se procura itens com mais de seis meses, pode encontrá-los no Arquivo para o [que há de novo no Azure Ative Directory.](whats-new-archive.md)

---

## <a name="june-2020"></a>Junho de 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Condição de risco do utilizador na política de acesso condicional

**Tipo:** Plano de mudança  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 

O suporte ao risco do utilizador na política de acesso condicionado Azure AD permite-lhe criar várias políticas baseadas no risco do utilizador. Diferentes níveis mínimos de risco do utilizador podem ser necessários para diferentes utilizadores e aplicações. Com base no risco do utilizador, pode criar políticas para bloquear o acesso, exigir autenticação de vários fatores, alterar palavras-passe seguras ou redirecionar para a Microsoft Cloud App Security para impor a política de sessão, como auditoria adicional.

A condição de risco do utilizador requer Azure AD Premium P2 porque utiliza a Azure Identity Protection, que é uma oferta P2. para obter mais informações sobre o acesso condicional, consulte a [documentação de Acesso Condicional AD Azure.](https://docs.microsoft.com/azure/active-directory/conditional-access/)

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO agora suporta apps que exigem que SPNameQualifier seja definido quando solicitado

**Tipo:** Fixo  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** SSO
 
Algumas aplicações SAML exigem que o SPNameQualifier seja devolvido no sujeito de afirmação quando solicitado. Agora, a Azure AD responde corretamente quando um SPNameQualifier é solicitado na política nameID do pedido. Isto também funciona para a sed-in iniciada pela SP, e o IdP iniciou a s inscrição.  Para saber mais sobre o protocolo SAML no Diretório Ativo Azure, consulte [o protocolo SINGLE Sign-On SAML](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>A Azure AD B2B Collaboration apoia convidar utilizadores da MSA e do Google em inquilinos do Governo de Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 

Os inquilinos do Governo Azure que usam as funcionalidades de colaboração B2B podem agora convidar os utilizadores que tenham uma conta Microsoft ou Google. Para saber se o seu inquilino pode usar estas capacidades, siga as instruções em [Como posso dizer se a colaboração B2B está disponível no meu inquilino do Governo Azure US?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>O objeto do utilizador no MS Graph v1 agora inclui propriedades externalUserState e externalUserStateChangedDateTime

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 

As propriedades externalUserState e externalUserStateChangedDateTime podem ser usadas para encontrar hóspedes B2B convidados que ainda não aceitaram os seus convites, bem como construir automatização, como eliminar utilizadores que não aceitaram os seus convites após alguns dias. Estas propriedades estão agora disponíveis no MS Graph v1. Para obter orientações sobre a utilização destas propriedades, consulte o [tipo de recurso do Utilizador](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Gerir sessões de autenticação em Azure AD Conditional Access já está disponível geralmente

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
As capacidades de gestão da sessão de autenticação permitem-lhe configurar a frequência com que os seus utilizadores precisam de fornecer credenciais de inscrição e se precisam de fornecer credenciais após o fecho e reabertura dos navegadores para oferecer mais segurança e flexibilidade no seu ambiente.
 
Adicionalmente, a gestão da sessão de autenticação utilizada para aplicar apenas à autenticação do Primeiro Fator em AD Azure juntou-se, a Hybrid Azure AD juntou-se e a Azure AD dispositivos registados. Agora a gestão da sessão de autenticação aplicar-se-á também ao MFA. Para mais informações, consulte a gestão da [sessão de autenticação configurada com Acesso Condicional.](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Novas Aplicações Federadas disponíveis na galeria AZure AD Application - junho de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Em junho de 2020 adicionámos as seguintes 29 novas aplicações na nossa galeria app com o apoio da Federação:

[Shopify Plus](https://docs.microsoft.com/azure/active-directory/saas-apps/shopify-plus-tutorial), [Ekarda](https://docs.microsoft.com/azure/active-directory/saas-apps/ekarda-tutorial), [MailGates](https://docs.microsoft.com/azure/active-directory/saas-apps/mailgates-tutorial), [BullseyeTDP,](https://docs.microsoft.com/azure/active-directory/saas-apps/bullseyetdp-tutorial) [Raketa,](https://docs.microsoft.com/azure/active-directory/saas-apps/raketa-tutorial)Segmento , [Ai](https://docs.microsoft.com/azure/active-directory/saas-apps/segment-tutorial) [Auditor,](https://www.mindbridge.ai/products/ai-auditor/) [Pobuca Connect,](https://app.pobu.ca/) [Proto.io,](https://docs.microsoft.com/azure/active-directory/saas-apps/proto.io-tutorial) [Gatekeeper,](https://www.gatekeeperhq.com/) [Hub Planner,](https://docs.microsoft.com/azure/active-directory/saas-apps/hub-planner-tutorial) [Ansira-Partner Go-to-Market Toolbox,](https://ansira.com/technology/channel-engagement) [IBM Digital Business Automation on Cloud,](https://docs.microsoft.com/azure/active-directory/saas-apps/ibm-digital-business-automation-on-cloud-tutorial) [Kisi Physical Security,](https://docs.microsoft.com/azure/active-directory/saas-apps/kisi-physical-security-tutorial) [ViewpointOne,](https://team.viewpoint.com/) [IntelligenceBank,](https://docs.microsoft.com/azure/active-directory/saas-apps/intelligencebank-tutorial) [pymetric (](https://docs.microsoft.com/azure/active-directory/saas-apps/pymetrics-tutorial) [Zero](https://www.teamzero.com/), [InStation](https://instation.invillia.com/), [edX for Business SAML 2.0 Integração](https://docs.microsoft.com/azure/active-directory/saas-apps/edx-for-business-saml-integration-tutorial), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](https://docs.microsoft.com/azure/active-directory/saas-apps/smartkargo-tutorial), [PKIsigning platform](https://platform.pkisigning.nl/), [SiteIntel](https://docs.microsoft.com/azure/active-directory/saas-apps/siteintel-tutorial), [Field iD,](https://docs.microsoft.com/azure/active-directory/saas-apps/field-id-tutorial) [Curricula SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/curricula-saml-tutorial), [Perforce Helix Core - Helix Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/perforce-helix-core-tutorial), [MyCompliance Cloud](https://cloud.metacompliance.com/), [Smallstep SSH](https://smallstep.com/sso-ssh/)  

Pode também encontrar a documentação de todas as candidaturas a partir https://aka.ms/AppsTutorial daqui. Para listar a sua aplicação na galeria de aplicações AZure AD, leia os detalhes aqui: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>Conectores API para identidades externas autosserviço estão agora em pré-visualização pública

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
Os conectores API de identidades externas permitem-lhe alavancar as APIs web para integrar o autosserviço de inscrição com sistemas de nuvem externa. Isto significa que agora pode invocar apis web como passos específicos num fluxo de inscrição para desencadear fluxos de trabalho personalizados baseados na nuvem. Por exemplo, pode utilizar conectores API para:

- Integre-se com fluxos de trabalho de aprovação personalizados.
- Realizar a prova de identidade
- Validar dados de entrada de utilizador
- Atributos de utilizador sobrepor-se
- Executar lógica de negócio personalizada

Para obter mais informações sobre todas as experiências possíveis com conectores API, consulte [utilize conectores API para personalizar e alargar a inscrição de self-service](https://docs.microsoft.com/azure/active-directory/b2b/api-connectors-overview), ou [personalize o autosserviço de identidades externas com integrações de API web.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Provisão a pedido e entrada dos utilizadores nas suas apps em segundos

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
O serviço de prestação de AD Azure opera atualmente numa base cíclica. O serviço funciona a cada 40 minutos. A [capacidade de provisionamento a pedido permite-lhe](https://aka.ms/provisionondemand) escolher um utilizador e forerá-lo em segundos. Esta capacidade permite-lhe resolver rapidamente problemas de provisionamento, sem ter de fazer um reinício para forçar o ciclo de provisionamento a recomeçar. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Nova permissão para usar a gestão de direitos Azure AD no Gráfico

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Gestão de Direitos
 
Uma nova permissão delegada EntitlementManagement.Read.All está agora disponível para uso com a API de Gestão de Direitos na versão beta do Microsoft Graph. Para saber mais sobre as APIs disponíveis, consulte [Trabalhar com a Azure AD gestão de direitos API.](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)

---

### <a name="identity-protection-apis-available-in-v10"></a>APIs de Proteção de Identidade disponíveis em v1.0

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
Os riskyUsers e riskDetections Microsoft Graph APIs estão agora geralmente disponíveis. Agora que estão disponíveis no ponto final v1.0, convidamo-lo a usá-los em produção. Para mais informações, consulte os docs do [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/identityprotectionroot?view=graph-rest-1.0).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Rótulos de sensibilidade para aplicar políticas aos grupos Microsoft 365 estão agora geralmente disponíveis

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração
 

Pode agora criar etiquetas de sensibilidade e utilizar as definições de etiquetas para aplicar políticas a grupos Microsoft 365, incluindo a política de privacidade (Pública ou Privada) e política externa de acesso ao utilizador. Pode criar um rótulo com a política de privacidade para ser privado e política externa de acesso ao utilizador para não permitir adicionar utilizadores convidados. Quando um utilizador aplica esta etiqueta a um grupo, o grupo será privado e nenhum utilizador convidado pode ser adicionado ao grupo. 

As etiquetas de sensibilidade são importantes para proteger os seus dados críticos do negócio e permitir-lhe gerir grupos em escala, de forma compatível e segura. Para obter orientações sobre a utilização de etiquetas de sensibilidade, consulte [os rótulos de sensibilidade do Office 365 no Azure Ative Directory (pré-visualização)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-assign-sensitivity-labels).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Atualizações para suporte para o Gestor de Identidade da Microsoft para clientes Azure AD Premium

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Gestor de Identidade da Microsoft  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
O Azure Support está agora disponível para componentes de integração AD do Microsoft Identity Manager 2016, até ao final do Suporte Alargado para o Microsoft Identity Manager 2016. Leia mais na [atualização de Suporte para clientes Azure AD Premium utilizando o Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>A utilização de condições de adesão ao grupo na configuração de sinistros SSO é aumentada

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** SSO
 
Anteriormente, o número de grupos que poderia utilizar quando alterou condicionalmente as reclamações com base na adesão ao grupo dentro de qualquer configuração de aplicação única estava limitado a 10. A utilização das condições de adesão ao grupo na configuração de sinistros SSO aumentou agora para um máximo de 50 grupos. Para obter mais informações sobre como configurar reclamações, consulte a [configuração de reclamações SSO de aplicações empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Habilitando formatação básica no componente Sign In Page Text em Branding da empresa.

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
A funcionalidade de Marca da Empresa na experiência de login Azure AD/Microsoft 365 foi atualizada para permitir ao cliente adicionar hiperligações e formatação simples, incluindo fonte arrojada, sublinhado e itálico. Para obter orientações sobre a utilização desta funcionalidade, consulte [adicionar a marca à página de início de súming do Azure Ative Directory da sua organização.](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)

---

### <a name="provisioning-performance-improvements"></a>Melhorias no desempenho do provisionamento

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
O serviço de fornecimento foi atualizado para reduzir o tempo para que um [ciclo incremental](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles) esteja concluído. Isto significa que os utilizadores e grupos serão a provisionados nas suas aplicações mais rapidamente do que eram anteriormente. Todos os novos postos de trabalho criados após 6/10/2020 beneficiarão automaticamente das melhorias de desempenho. Quaisquer aplicações configuradas para provisão antes de 6/10/2020 terão de ser reiniciadas uma vez após 6/10/2020 para tirar partido das melhorias de desempenho. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Anunciando a depreciação da Paridade de Gráficos ADAL e MS

**Tipo:** Precatado  
**Categoria de serviço:** N/A  
**Capacidade do produto:** Gestão do ciclo de vida do dispositivo

Agora que as Bibliotecas de Autenticação da Microsoft (MSAL) estão disponíveis, deixaremos de adicionar novas funcionalidades às Bibliotecas de Autenticação ativa do Azure Ative (ADAL) e terminaremos com as correções de segurança a 30 de junho de 2022. Para obter mais informações sobre como migrar para o MSAL, consulte [as aplicações migrar para a Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-migration).

Além disso, terminámos o trabalho para disponibilizar toda a funcionalidade Azure AD Graph através do MS Graph. Assim, as APIs de gráficos AZure AD receberão apenas correções de bugfix e segurança até 30 de junho de 2022. Para obter mais informações, consulte [Atualizar as suas aplicações para utilizar a Microsoft Authentication Library e a Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
 
## <a name="may-2020"></a>Maio de 2020

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Aposentadoria de imóveis em signIns, riskyUsers e riscoDetections APIs

**Tipo:** Plano de mudança  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Proteção & de Segurança de Identidade

Atualmente, os tipos enumerados são usados para representar a propriedade de riscoType tanto na API de riscodetecções como em riskyUserHistoryItem (em pré-visualização). Os tipos enumerados também são usados para a propriedade riskEventTypes no signIns API. Daqui para a frente, vamos representar estas propriedades como cordas. 

Os clientes devem transitar para a propriedade riskEventType nas beta riskDetections e riskyUserHistoryItem API, e riskEventTypes_v2 propriedade no signins betaIns API até 9 de setembro de 2020. Nessa data, vamos retirar as propriedades de risco actualType e riscoEventTypes. Para obter mais informações, consulte [alterações às propriedades do evento de risco e APIs de proteção de identidade no Gráfico do Microsoft](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/).

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Depreciação da propriedade riskEventTypes em signIns v1.0 API no Microsoft Graph

**Tipo:** Plano de mudança  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Proteção & de Segurança de Identidade

Os tipos enumerados mudarão para tipos de cordas quando representarem propriedades de eventos de risco no Microsoft Graph setembro de 2020. Além de impactar as APIs de pré-visualização, esta alteração também terá impacto no sinal de In-productionIns API.

Introduzimos uma nova propriedade riskEventsTypes_v2 (string) para o signIns v1.0 API. Retiraremos a propriedade atual do RiskEventTypes (enum) em 11 de junho de 2022, de acordo com a nossa política de depreciação do Microsoft Graph. Os clientes deverão transitar para o riskEventTypes_v2 propriedade na API v1.0 signins até 11 de junho de 2022. Para obter mais informações, consulte a [propriedade Deprecação de riscoEventTypes em signIns v1.0 API no Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//).

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Próximas alterações às notificações de email do MFA

**Tipo:** Plano de mudança  
**Categoria de serviço:** MFA  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 

Estamos a fazer as seguintes alterações às notificações de e-mail para MFA na nuvem:

As notificações por e-mail serão enviadas a partir do seguinte endereço: azure-noreply@microsoft.com e msonlineservicesteam@microsoftonline.com . Estamos a atualizar o conteúdo de e-mails de alerta de fraude para indicar melhor as medidas necessárias para desbloquear as utilizações.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Novos self-service inscrevem-se para utilizadores em domínios federados que não conseguem aceder às Equipas microsoft porque não estão sincronizados com o Azure Ative Directory.

**Tipo:** Plano de mudança  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 

Atualmente, os utilizadores que estão em domínios federados em AZure AD, mas que não estão sincronizados com o inquilino, não podem aceder às Equipas. A partir do final de junho, esta nova capacidade irá permitir-lhes fazê-lo, alargando a funcionalidade de inscrição verificada por e-mail existente. Isto permitirá aos utilizadores que possam iniciar sposição num IdP federado, mas que ainda não tenham um objeto de utilizador no ID Azure, tenham um objeto de utilizador criado automaticamente e sejam autenticados para equipas. O objeto do utilizador será marcado como "self-service sign up". Esta é uma extensão da capacidade existente para fazer auto-inscrição verificada por e-mail que os utilizadores em domínios geridos podem fazer e podem ser controlados usando a mesma bandeira. Esta alteração completa durante os dois meses seguintes. Atenção às atualizações de documentação [aqui.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup)
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Correção seguinte: O documento de descoberta OIDC para a nuvem do Governo Azure está a ser atualizado para fazer referência aos pontos finais corretos do Gráfico.

**Tipo:** Plano de mudança  
**Categoria de serviço:** Nuvens Soberanas  
**Capacidade do produto:** Autenticação do Utilizador
 
A partir de junho, o documento de descoberta OIDC A plataforma de identidade microsoft e o [protocolo OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) no ponto final da nuvem do Governo [Azure](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) (login.microsoftonline.us), começarão a devolver o ponto final de [gráfico de nuvem nacional](https://docs.microsoft.com/graph/deployments) correto ( ou , com base no inquilino https://graph.microsoft.us https://dod-graph.microsoft.us) fornecido.  Atualmente, fornece o ponto final do gráfico incorreto (graph.microsoft.com) "msgraph_host".  

Esta correção de insetos será lançada gradualmente ao longo de aproximadamente 2 meses.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Os utilizadores do Governo do Azure deixarão de poder inscrever-se no login.microsoftonline.com

**Tipo:** Plano de Mudança  
**Categoria de serviço:** Nuvens Soberanas  
**Capacidade do produto:** Autenticação do Utilizador
 
Em 1 de junho de 2018, a Autoridade Oficial do Azure Ative Directory (AAD) para o Governo Azure passou de https://login-us.microsoftonline.com https://login.microsoftonline.us . Se possuir uma aplicação dentro de um inquilino do Governo Azure, deve atualizar a sua aplicação para iniciar seduções nos utilizadores no ponto final .us.

A partir de 5 de maio, a Azure AD começará a impor a mudança de ponto final, impedindo os utilizadores do Governo do Azure de assinarem em aplicações hospedadas em inquilinos do Governo de Azure usando o ponto final público (microsoftonline.com). As aplicações impactadas começarão a ver um erro AADSTS900439 - USGClientNotSupportedOnPublicEndpoint. 

Haverá um lançamento gradual desta mudança, com a execução prevista para ser concluída em todas as aplicações junho de 2020. Para mais detalhes, consulte a publicação de blogue do [Governo Azure.](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>Pedido de logout único SAML agora envia NameID no formato correto

**Tipo:** Fixo  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
Quando um utilizador clica na sessão de sessão de sessão (por exemplo, no portal MyApps), o AZure AD envia uma mensagem de logout único SAML para cada aplicação que esteja ativa na sessão do utilizador e tenha um URL logout configurado. Estas mensagens contêm um NameID num formato persistente.

Se o token original do SAML usou um formato diferente para NameID (por exemplo, e-mail/UPN), então a aplicação SAML não pode correlacionar o NameID na mensagem de logout para uma sessão existente (uma vez que os NameIDs utilizados em ambas as mensagens são diferentes), o que fez com que a mensagem de logoto fosse descartada pela app SAML e pelo utilizador para se manter iniciado. Esta correção torna a mensagem de inscrição consistente com o NameID configurado para a aplicação.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>A função de Administrador de Identidade Híbrida está agora disponível com cloud Provisioning

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Azure AD Cloud Provisioning  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
Os administradores de TI podem começar a usar o novo papel de "Hybrid Admin" como o papel menos privilegiado para a criação do Azure ADConnect Cloud Provisioning. Com este novo papel, já não tens de usar o papel da Administração Global para configurar e configurar o Cloud Provisioning. [Saiba mais](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-by-task#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Novas Aplicações Federadas disponíveis na galeria AZure AD Application - maio 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Em maio de 2020, adicionámos as seguintes 36 novas aplicações na nossa galeria de aplicações com o apoio da Federação:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell Teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [ASC Recording Insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-Engaged](https://b-engaged.se/), [Competella Contact Center Workgroup](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft Identity](https://identity.imagesoftinc.com/), My [IBISWorld](https://identity.imagesoftinc.com/), [insuite,](https://docs.microsoft.com/azure/active-directory/saas-apps/insuite-tutorial) [Change Process Management](https://docs.microsoft.com/azure/active-directory/saas-apps/change-process-management-tutorial), [Cyara CX Assurance Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/cyara-cx-assurance-platform-tutorial), Smart Global [Governance](https://docs.microsoft.com/azure/active-directory/saas-apps/smart-global-governance-tutorial), [Prezi,](https://docs.microsoft.com/azure/active-directory/saas-apps/prezi-tutorial) [Mapbox](https://docs.microsoft.com/azure/active-directory/saas-apps/mapbox-tutorial), [Datava Enterprise Service Plataforma](https://docs.microsoft.com/azure/active-directory/saas-apps/datava-enterprise-service-platform-tutorial), [Whimsical](https://docs.microsoft.com/azure/active-directory/saas-apps/whimsical-tutorial), [Trelica,](https://docs.microsoft.com/azure/active-directory/saas-apps/trelica-tutorial) [EasySSO for Confluence](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-confluence-tutorial), [EasySSO for BitBucket](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bitbucket-tutorial), [EasySSO for Bamboo](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bamboo-tutorial), [Torii,](https://docs.microsoft.com/azure/active-directory/saas-apps/torii-tutorial) [Axiad Cloud,](https://docs.microsoft.com/azure/active-directory/saas-apps/axiad-cloud-tutorial) [Humanage,](https://docs.microsoft.com/azure/active-directory/saas-apps/humanage-tutorial) [ColorTokens ZTNA,](https://docs.microsoft.com/azure/active-directory/saas-apps/colortokens-ztna-tutorial) [CCH Tagetik,](https://docs.microsoft.com/azure/active-directory/saas-apps/cch-tagetik-tutorial) [ShareVault,](https://docs.microsoft.com/azure/active-directory/saas-apps/sharevault-tutorial) [Vyond,](https://docs.microsoft.com/azure/active-directory/saas-apps/vyond-tutorial) [TextExpander,](https://docs.microsoft.com/azure/active-directory/saas-apps/textexpander-tutorial) [Anyone Home CRM,](https://docs.microsoft.com/azure/active-directory/saas-apps/anyone-home-crm-tutorial) [askSpoke](https://docs.microsoft.com/azure/active-directory/saas-apps/askspoke-tutorial), [Ice Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/ice-contact-center-tutorial)

Pode também encontrar a documentação de todas as candidaturas a partir https://aka.ms/AppsTutorial daqui.

Para listar a sua aplicação na galeria de aplicações Azure AD, leia os detalhes https://aka.ms/AzureADAppRequest aqui.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>O modo apenas de reporte para acesso condicional está geralmente disponível

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Proteção & de Segurança de Identidade

[O modo apenas de reporte para acesso condicionado Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) permite-lhe avaliar o resultado de uma política sem impor controlos de acesso. Pode testar políticas apenas de relatórios em toda a sua organização e compreender o seu impacto antes de as permitir, tornando a implementação mais segura e fácil. Ao longo dos últimos meses, temos visto uma forte adoção do modo apenas de relatórios - mais de 26M utilizadores já estão no âmbito de uma política apenas de relatório. Com o anúncio de hoje, novas políticas de acesso condicionado Azure AD serão criadas em modo apenas de relatório por padrão. Isto significa que pode monitorizar o impacto das suas políticas a partir do momento em que são criadas. E para aqueles que usam as APIs do MS Graph, também pode [gerir políticas de relatórios programáticas.](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) 

---

### <a name="self-service-sign-up-for-guest-users"></a>Inscrição de self-service para utilizadores convidados

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
Com identidades externas em Azure AD, pode permitir que pessoas fora da sua organização acedam às suas apps e recursos, deixando-as assinar em qualquer identidade que prefiram. Ao partilhar uma aplicação com utilizadores externos, pode nem sempre saber com antecedência quem irá precisar de acesso à aplicação. Com [a inscrição de self-service,](https://docs.microsoft.com/azure/active-directory/b2b/self-service-sign-up-overview)pode permitir que os utilizadores convidados se inscrevam e obtenham uma conta de hóspedes para as suas aplicações de negócio (LOB). O fluxo de inscrição pode ser criado e personalizado para suportar Azure AD e identidades sociais. Também pode recolher informações adicionais sobre o utilizador durante a inscrição.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Os relatórios de acesso condicional e relatórios estão geralmente disponíveis

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Proteção & de Segurança de Identidade

O [livro de insights e relatórios](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) dá aos administradores uma visão sumária do Acesso Condicional Azure AD no seu inquilino. Com a capacidade de selecionar uma política individual, os administradores podem entender melhor o que cada política faz e monitorizar quaisquer mudanças em tempo real. O livro transmite dados armazenados no Azure Monitor, que pode configurar em poucos minutos [seguindo estas instruções](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). Para tornar o painel mais detetável, mudámo-lo para o novo separador de insights e relatórios dentro do menu Azure AD Conditional Access.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>A lâmina de detalhes de política para acesso condicional está em pré-visualização pública

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Proteção & de Segurança de Identidade

A [lâmina de novos detalhes da política](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) exibe as atribuições, condições e controlos satisfeitos durante a avaliação da política de acesso condicional. Pode aceder à lâmina selecionando uma linha nos separadores de Acesso Condicional ou relatório apenas nos detalhes de Iniciar sing.in.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Novas capacidades de consulta para objetos de diretório no Microsoft Graph estão em visualização pública

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** **Capacidade do produto ms graph:** Experiência de desenvolvimento

Estão a ser introduzidas novas capacidades para as APIs do Diretoria de Gráficos da Microsoft, permitindo operações de contagem, pesquisa, filtro e sort. Isto dará aos desenvolvedores a capacidade de consultar rapidamente os nossos Objetos de Diretório sem soluções alternativas, tais como filtragem e triagem na memória. Saiba mais nesta [publicação de blog.](https://aka.ms/CountFilterMSGraphAAD)

Estamos neste momento em Visualização Pública, à procura de feedback. Por favor envie os seus comentários com esta [breve pesquisa.](https://aka.ms/MsGraphAADSurveyDocs)

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Configurar um único sign-on baseado em SAML usando a Microsoft Graph API (Beta)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** SSO
 
O suporte para criar e configurar uma aplicação a partir da Galeria AD Azure usando APIs ms graph em Beta já está disponível. Se precisar de configurar um único sinal de saturação baseado em SAML para várias instâncias de uma aplicação, economize tempo utilizando as APIs do Gráfico do Microsoft para [automatizar a configuração de um único sinal baseado em SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Novos conectores de provisionamento na Galeria de Aplicações AD Azure - maio de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Integração de Terceiros
 
Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:

* [8x8](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8-provisioning-tutorial)
* [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-provisioning-tutorial)
* [MediusFlow](https://docs.microsoft.com/azure/active-directory/saas-apps/mediusflow-provisioning-tutorial)
* [New Relic por Organização](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-by-organization-provisioning-tutorial)
* [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte [automatizar o fornecimento de utilizadores para aplicações SaaS com Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="saml-token-encryption-is-generally-available"></a>A encriptação token SAML está geralmente disponível

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** SSO
 
[A encriptação do token SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption) permite que as aplicações sejam configuradas para receber afirmações SAML encriptadas. A funcionalidade encontra-se agora geralmente disponível em todas as nuvens.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Reclamações de nome de grupo em fichas de aplicação estão geralmente disponíveis

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** SSO
 
As alegações do grupo emitidas num token podem agora limitar-se apenas aos grupos afetados ao pedido.  Isto é especialmente importante quando os utilizadores são membros de um grande número de grupos e existe o risco de exceder os limites de tamanho dos símbolos. Com esta nova capacidade em vigor, a capacidade de [adicionar nomes de grupo a tokens](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims) está geralmente disponível.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Workday Writeback agora suporta a definição de atributos de número de telefone de trabalho

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
Melhorámos a aplicação de provisionamento workday Writeback para agora suportar a redução do número de telefone de trabalho e dos atributos do número de telemóvel. Além do e-mail e nome de utilizador, pode agora configurar a app de provisionamento de writeback workday para fluir os valores do número de telefone de Azure AD para Workday. Para obter mais detalhes sobre como configurar a gravação do número de telefone, consulte o tutorial da aplicação [Workday Writeback.](https://aka.ms/WorkdayWriteback) 

---

### <a name="publisher-verification-preview"></a>Verificação do Editor (pré-visualização)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Experiência de Desenvolvedor
 
A verificação do editor (pré-visualização) ajuda os administradores e os utilizadores finais a compreender a autenticidade dos desenvolvedores de aplicações que se integram na plataforma de identidade da Microsoft. Para mais informações, consulte a [verificação do Editor (pré-visualização)](https://docs.microsoft.com/azure/active-directory/develop/publisher-verification-overview).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Fluxo de código de autorização para aplicações de página única

**Tipo:** Categoria de serviço de funcionalidade **alterada:** Capacidade do produto de autenticação: Experiência do desenvolvedor **Product capability:**

Devido às [restrições modernas de cookies do navegador 3º partido, como o Safari ITP,](https://docs.microsoft.com/azure/active-directory/develop/reference-third-party-cookies-spas)as SPAs terão de utilizar o fluxo de código de autorização em vez do fluxo implícito para manter o SSO; MSAL.js v 2.x irá agora suportar o fluxo de código de autorização. Existem como atualizações correspondentes ao portal Azure para que possa atualizar o seu SPA para ser tipo "spa" e utilizar o fluxo de código auth. Para obter orientações, consulte [o Quickstart: Inicie sedudas nos utilizadores e obtenha um token de acesso num SPA JavaScript utilizando o fluxo de código auth](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Filtragem melhorada para dispositivos está em visualização pública

**Tipo:** Recurso alterado   
**Categoria de serviço:** Capacidade do produto de gestão de **dispositivos:** Gestão do ciclo de vida do dispositivo
 
Anteriormente, os únicos filtros que poderia utilizar eram "Ativado" e "Data de Atividade". Agora, pode [filtrar a sua lista de dispositivos em mais propriedades](https://docs.microsoft.com/azure/active-directory/devices/device-management-azure-portal#device-list-filtering-preview), incluindo tipo DE, tipo de junção, conformidade e muito mais. Estas adições devem simplificar a localização de um determinado dispositivo.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>A nova experiência de registos de aplicações para Azure AD B2C está agora disponível em geral

**Tipo:** Recurso alterado   
**Categoria de serviço:** B2C - Gestão da Identidade do Consumidor  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
A nova experiência de registos de aplicações para Azure AD B2C está agora disponível em geral. 

Anteriormente, tinha de gerir as suas aplicações viradas para o consumidor B2C separadamente das restantes aplicações utilizando a experiência legacy 'Applications'. Isso significou diferentes experiências de criação de aplicações em diferentes lugares em Azure.

A nova experiência mostra todos os registos de aplicações B2C e registos de aplicações AD AZure num só local e fornece uma forma consistente de os gerir. Quer seja necessário gerir uma aplicação virada para o cliente ou uma app que tenha acesso ao Microsoft Graph para gerir programáticamente os recursos do Azure AD B2C, só precisa de aprender uma maneira de fazer as coisas.

Pode chegar à nova experiência navegando no serviço Azure AD B2C e selecionando a lâmina de registos da App. A experiência também é acessível a partir do serviço Azure Ative Directory.

A experiência de registos de aplicações Azure AD B2C baseia-se na experiência geral de [Registo de Aplicações](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) para inquilinos AZure AD, mas é personalizada para Azure AD B2C. A experiência legado "Aplicações" será depreciada no futuro.

Para mais informações, visite [a nova experiência de registo de aplicações para Azure AD B2C.](https://aka.ms/b2cappregtraining)

---

## <a name="april-2020"></a>Abril de 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>A experiência combinada de registo de informações de segurança está agora geralmente disponível

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Autenticações (Logins)

**Capacidade do produto:** Proteção & de Segurança de Identidade

A experiência de registo combinado para autenticação multi-factor (MFA) e autosserviço de redefinição de senha (SSPR) está agora geralmente disponível. Esta nova experiência de registo permite que os utilizadores se registem para MFA e SSPR num único processo passo a passo. Quando implementa a nova experiência para a sua organização, os utilizadores podem registar-se em menos tempo e com menos agios. Confira o post do blog [aqui.](https://bit.ly/3etiRyQ)

---

### <a name="continuous-access-evaluation"></a>Avaliação de Acesso Contínuo

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Autenticações (Logins)

**Capacidade do produto:** Proteção & de Segurança de Identidade

A Avaliação contínua de Acesso é uma nova funcionalidade de segurança que permite a aplicação quase em tempo real de políticas em partes dependentes que consomem Tokens de Acesso AD Azure quando os eventos acontecem em Azure AD (como a eliminação de conta de utilizador). Estamos a lançar esta funcionalidade primeiro para os clientes Teams e Outlook. Para mais detalhes, leia o nosso [blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) e [documentação.](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation)

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>Sação POR SMS: Os trabalhadores da Firstline podem iniciar sação em aplicações apoiadas pelo AZure com o seu número de telefone e sem senha

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Autenticações (Logins)

**Capacidade do produto:** Autenticação do Utilizador

O Office está a lançar uma série de aplicações empresariais de primeira para dispositivos móveis que atendem a organizações não tradicionais, e a colaboradores de grandes organizações que não usam o e-mail como o seu método de comunicação primário. Estas aplicações visam funcionários da linha da frente, trabalhadores sem secretária, agentes de campo ou empregados de retalho que podem não obter um endereço de e-mail do seu empregador, ter acesso a um computador ou a TI. Este projeto permitirá que estes colaboradores inscrevam-se em aplicações empresariais, introduzindo um número de telefone e arredondando um código. Para mais detalhes, consulte a nossa [documentação de administração](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin) e [documentação do utilizador final.](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer)

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Convidar utilizadores internos a utilizarem a colaboração B2B

**Tipo:** Nova funcionalidade

**Categoria de serviço:** B2B

**Capacidade do produto:**

Estamos a expandir a capacidade de convite b2B para permitir que as contas internas existentes sejam convidadas a usar credenciais de colaboração B2B. Isto é feito passando o objeto do utilizador para a API convite, além de parâmetros típicos como o endereço de e-mail convidado. O ID do objeto do utilizador, UPN, membro do grupo, atribuição de aplicativos, etc. permanecem intactos, mas para a frente eles usarão B2B para autenticar com as credenciais do seu inquilino de casa em vez das credenciais internas que usaram antes do convite. Para mais detalhes, consulte a [documentação.](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users)

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>O modo apenas de reporte para acesso condicional está geralmente disponível

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Acesso Condicional

**Capacidade do produto:** Proteção & de Segurança de Identidade

[O modo apenas de reporte para acesso condicionado Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) permite-lhe avaliar o resultado de uma política sem impor controlos de acesso. Pode testar políticas apenas de relatórios em toda a sua organização e compreender o seu impacto antes de as permitir, tornando a implementação mais segura e fácil. Ao longo dos últimos meses, temos visto uma forte adoção do modo apenas de relatório, com mais de 26M utilizadores já no âmbito de uma política apenas de relatórios. Com este anúncio, novas políticas de acesso condicionado Azure AD serão criadas em modo apenas de relatório por padrão. Isto significa que pode monitorizar o impacto das suas políticas a partir do momento em que são criadas. E para aqueles que usam as APIs do MS Graph, também pode [gerir políticas apenas de relatórios programáticas.](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Os insights de acesso condicional e o livro de relatórios estão geralmente disponíveis

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Acesso Condicional

**Capacidade do produto:** Proteção & de Segurança de Identidade

Os insights de acesso condicional [e o livro de relatórios](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) dão aos administradores uma visão sumária do Acesso Condicional AD Azure no seu inquilino. Com a capacidade de selecionar uma política individual, os administradores podem entender melhor o que cada política faz e monitorizar quaisquer mudanças em tempo real. O livro transmite dados armazenados no Azure Monitor, que pode configurar em poucos minutos [seguindo estas instruções](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). Para tornar o painel mais detetável, mudámo-lo para o novo separador de insights e relatórios dentro do menu Azure AD Conditional Access.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>A lâmina de detalhes de política para acesso condicional está em pré-visualização pública

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Acesso Condicional

**Capacidade do produto:** Proteção & de Segurança de Identidade

Os novos [detalhes da política](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) mostram quais as atribuições, condições e controlos que foram satisfeitos durante a avaliação da política de acesso condicional. Pode aceder à lâmina selecionando uma linha nos separadores **de Acesso Condicional** ou relatório **apenas** nos detalhes de Iniciar sing.in.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - abril de 2020

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Aplicativos da Empresa

**Capacidade do produto:** Integração de Terceiros

Em abril de 2020, adicionámos estas 31 novas aplicações com apoio da Federação à galeria de aplicações: 

[Aplicativos SincroPool](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [Float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial), [LMS365](https://lms.365.systems/), [IWT Procurement Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial), [Lunni](https://lunni.fi/), [EasySSO para Jira](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial), [Academia de Formação Virtual,](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft) [Meraki Dashboard](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial), Office [365 Mover,](https://app.mover.io/login) [Speaker Engage](https://speakerengage.com/login.php), [Honestamente,](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial) [Ally,](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial) [DutyFlow](https://app.dutyflow.nl/), [AlertMedia,](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial) [gr8 People](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial), [Pendo](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial), [HighGround](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial), [Harmony](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial), [Timetabling Solutions](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial), [SynchroNet CLICK](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial), [empower,](https://www.made-in-office.com/en/) [Fortes Change Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial), [Litmus](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial), [GroupTalk,](https://recorder.grouptalk.com/) [Frontify,](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial) [MongoDB Cloud,](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial) [TickitLMS Learn,](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial) [COCO,](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/) [Nitro Productivity Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial) , Trend Micro Web Security [(TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](https://aka.ms/appstutorial) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](https://aka.ms/azureadapprequest)

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Suporte de consulta delta do Microsoft Graph para oAuth2PermissionGrant disponível para visualização pública

**Tipo:** Nova funcionalidade

**Categoria de serviço:** MS Graph

**Capacidade do produto:** Experiência de Desenvolvedor

A consulta delta para oAuth2PermissionGrant está disponível para pré-visualização pública! Agora pode rastrear alterações sem ter de pesquisar continuamente o Microsoft Graph. [Saiba mais.](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Suporte de consulta delta do Microsoft Graph para contacto organizacional geralmente disponível

**Tipo:** Nova funcionalidade

**Categoria de serviço:** MS Graph

**Capacidade do produto:** Experiência de Desenvolvedor

A consulta delta para contactos organizacionais está geralmente disponível! Agora é possível rastrear alterações nas aplicações de produção sem ter de fazer uma pesquisa contínua no Microsoft Graph. Substitua qualquer código existente que poll continuamente orgContact dados por consulta delta para melhorar significativamente o desempenho. [Saiba mais.](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Suporte de consulta delta do Microsoft Graph para aplicação geralmente disponível

**Tipo:** Nova funcionalidade

**Categoria de serviço:** MS Graph

**Capacidade do produto:** Experiência de Desenvolvedor

A consulta delta para aplicações está geralmente disponível! Agora é possível rastrear alterações nas aplicações de produção sem ter de fazer uma pesquisa contínua no Microsoft Graph. Substitua qualquer código existente que polle continuamente os dados da aplicação por consulta delta para melhorar significativamente o desempenho. [Saiba mais.](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Suporte de consulta delta do Microsoft Graph para unidades administrativas disponíveis para visualização pública

**Tipo:** Nova funcionalidade

**Categoria de serviço:** MS Graph

**Capacidade do produto:** A consulta Delta da Developer Experience para unidades administrativas está disponível para pré-visualização pública! Agora pode rastrear alterações sem ter de pesquisar continuamente o Microsoft Graph. [Saiba mais.](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Gerir números de telefone de autenticação e muito mais em novas APIs beta do Microsoft Graph

**Tipo:** Nova funcionalidade

**Categoria de serviço:** MS Graph

**Capacidade do produto:** Experiência de Desenvolvedor

Estas APIs são uma ferramenta chave para gerir os métodos de autenticação dos seus utilizadores. Agora pode pré-registar-se programaticamente e gerir os autenticadores utilizados para mFA e redefinição de senha de autosserviço (SSPR). Esta tem sido uma das funcionalidades mais requisitadas nos espaços Azure MFA, SSPR e Microsoft Graph. As novas APIs que lançamos nesta onda dão-lhe a capacidade de:

- Leia, adicione, atualize e remova os telefones de autenticação de um utilizador
- Redefinir a senha de um utilizador
- Ligue e desligue o s-sinal de SMS

Para obter mais informações, consulte [os métodos de autenticação AZure AD API.](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)

---

### <a name="administrative-units-public-preview"></a>Pré-visualização pública das Unidades Administrativas

**Tipo:** Nova funcionalidade

**Categoria de serviço:** RBAC

**Capacidade do produto:** Controlo de Acessos

As unidades administrativas permitem-lhe conceder permissões administrativas restritas a um departamento, região ou outro segmento da sua organização que define. Pode utilizar unidades administrativas para delegar permissões a administradores regionais ou para definir a política a nível granular. Por exemplo, um administrador de conta de utilizador poderia atualizar informações de perfil, redefinir palavras-passe e atribuir licenças para utilizadores apenas na sua unidade administrativa.

Utilizando unidades administrativas, um administrador central poderia:

- Criar uma unidade administrativa para gestão descentralizada de recursos
- Atribuir uma função com permissões administrativas apenas sobre utilizadores Azure AD numa unidade administrativa
- Povoar as unidades administrativas com utilizadores e grupos conforme necessário

Para mais informações, consulte [a gestão de unidades administrativas no Diretório Ativo Azure (pré-visualização)](https://aka.ms/AdminUnitsDocs).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Funções incorporadas do administrador de impressora e técnico de impressora

**Tipo:** Nova funcionalidade

**Categoria de serviço:** RBAC

**Capacidade do produto:** Controlo de Acessos

**Administrador de impressora**: Os utilizadores com esta função podem registar impressoras e gerir todos os aspetos de todas as configurações da impressora na solução De Impressão Universal da Microsoft, incluindo as definições do Conector de Impressão Universal. Podem consentir com todos os pedidos de autorização de impressão delegados. Os administradores da impressora também têm acesso a relatórios de impressão. 

**Técnico de impressora**: Os utilizadores com esta função podem registar impressoras e gerir o estado da impressora na solução de Impressão Universal da Microsoft. Também podem ler todas as informações do conector. As tarefas-chave que um Técnico de Impressora não pode fazer são definir permissões do utilizador em impressoras e partilhar impressoras. [Saiba mais.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Papel integrado no administrador de identidade híbrida

**Tipo:** Nova funcionalidade

**Categoria de serviço:** RBAC

**Capacidade do produto:** Controlo de Acessos

Os utilizadores desta função podem permitir, configurar e gerir serviços e configurações relacionadas com a ativação da identidade híbrida em AZure AD. Esta função confere a possibilidade de configurar a Azure AD a um dos três métodos de autenticação suportados&#8212;sincronização de hash password (PHS), autenticação pass-through (PTA) ou Federação (AD FS ou provedor de federação de terceiros)&#8212;e implantar infraestruturas relacionadas nas instalações para os permitir. As infraestruturas no local incluem agentes de provisionamento e de PTA. Esta função garante a capacidade de permitir um Único Sign-On (S-SSO) sem emenda para permitir a autenticação sem emenda em dispositivos não Windows 10 ou computadores não Windows Server 2016. Além disso, esta função confere a possibilidade de ver registos de inscrição e de aceder à saúde e análise para fins de monitorização e resolução de problemas. [Saiba mais.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Função de administrador de rede incorporado

**Tipo:** Nova funcionalidade

**Categoria de serviço:** RBAC

**Capacidade do produto:** Controlo de Acessos

Os utilizadores com esta função podem rever recomendações de arquitetura de perímetro de rede da Microsoft que são baseadas em telemetria de rede a partir das suas localizações de utilizadores. O desempenho da rede para o Office 365 baseia-se na arquitetura cuidadosa do perímetro da rede de clientes da empresa, que é geralmente específica da localização do utilizador. Esta função permite a edição das localizações do utilizador descobertas e a configuração de parâmetros de rede para esses locais para facilitar a melhoria das medições de telemetria e recomendações de design. [Saiba mais.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Atividade a granel e downloads na experiência do portal de administração Admin Azure

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Gestão de Utilizadores

**Capacidade do produto:** Diretório

Agora pode realizar atividades a granel em utilizadores e grupos em Azure AD, enviando um ficheiro CSV na experiência do portal de administração Azure AD. Pode criar utilizadores, eliminar utilizadores e convidar utilizadores convidados. E pode adicionar e remover membros de um grupo.

Também pode baixar listas de recursos Ad AD Azure a partir da experiência do portal de administração Azure. Você pode baixar a lista de utilizadores no diretório, a lista de grupos no diretório, e os membros de um determinado grupo.

Para mais informações, confira o seguinte:

- [Criar utilizadores](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add) ou [convidar utilizadores convidados](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)
- [Eliminar utilizadores](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete) ou [restaurar utilizadores eliminados](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)
- [Baixar lista de utilizadores](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download) ou [baixar lista de grupos](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)
- [Adicione (importar) membros](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members) ou [remova membros](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members) ou [Descarregue a lista de membros](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members) para um grupo

---

### <a name="my-staff-delegated-user-management"></a>O meu Pessoal delegou a gestão dos utilizadores

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Gestão de Utilizadores

**Capacidade do produto:**

A My Staff permite que os Gestores da Firstline, como um gerente de loja, garantam que os seus colaboradores possam aceder às suas contas AD Azure. Em vez de confiarem num helpdesk central, as organizações podem delegar tarefas comuns, tais como repor palavras-passe ou alterar números de telefone, para um Gestor da Linha Inicial. Com o My Staff, um utilizador que não consiga aceder à sua conta pode voltar a ter acesso em apenas alguns cliques, sem necessidade de ajuda ou pessoal de TI. Para obter mais informações, consulte a Gestão dos [seus utilizadores com o Meu Pessoal (pré-visualização)](https://aka.ms/MyStaffAdminDocs) e [delegar a gestão do utilizador com o Meu Pessoal (pré-visualização)](https://aka.ms/MyStaffUserDocs).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Uma experiência de utilizador final atualizada em comentários de acesso

**Tipo:** Alteração de recurso

**Categoria de serviço:** Comentários de Acesso

**Capacidade do produto:** Governação da Identidade

Atualizámos a experiência do revisor para avaliações de acesso a Azure no portal My Apps. No final de abril, os seus revisores que estão ligados à experiência do revisor de comentários de acesso AZure AD verão um banner que lhes permitirá experimentar a experiência atualizada no My Access. Por favor, note que a experiência atualizada de avaliações do Access oferece a mesma funcionalidade que a experiência atual, mas com uma interface de utilizador melhorada em cima de novas capacidades para permitir que os seus utilizadores sejam produtivos. [Pode saber mais sobre a experiência atualizada aqui.](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review) Esta pré-estreia pública durará até ao final de julho de 2020. No final de julho, os revisores que não optarem pela experiência de pré-visualização serão automaticamente direcionados para o My Access para realizar avaliações de acesso. Se desejar que os seus revisores tenham mudado permanentemente para a experiência de pré-visualização no My Access agora, [por favor, faça um pedido aqui](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>As aplicações de fornecimento e writeback dos utilizadores de trabalho suportam agora as versões mais recentes da Workday Web Services API

**Tipo:** Alteração de recurso

**Categoria de serviço:** Provisionamento de Aplicativos

**Capacidade do produto:** 

Com base no feedback do cliente, atualizámos as aplicações de provisionamento e de writeback dos utilizadores do Workday na galeria de aplicações da empresa para suportar as versões mais recentes da API dos Workday Web Services (WWS). Com esta alteração, os clientes podem especificar a versão API WWS que gostariam de usar na cadeia de ligação. Isto dá aos clientes a capacidade de recuperar mais atributos DE RH disponíveis nos lançamentos do Workday. A aplicação Workday Writeback utiliza agora o serviço web Change_Work_Contact_Info Workday recomendado para ultrapassar as limitações de Maintain_Contact_Info.

Se nenhuma versão for especificada na cadeia de ligação, por padrão, as aplicações de provisionamento de entrada workday continuarão a utilizar WWS v21.1 Para mudar para as mais recentes APIs do Workday para o provisionamento do utilizador de entrada, os clientes precisam de atualizar a cadeia de ligação como documentado [no tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) e também atualizar os XPATHs utilizados para atributos Workday como documentado no guia de referência do dia [de trabalho](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30). 

Para utilizar a nova API para reversão, não são necessárias alterações na aplicação de provisionamento de writeback workday. Do lado do Workday, certifique-se de que a conta do Utilizador do Sistema de Integração do Workday (ISU) tem permissões para invocar o processo de negócio Change_Work_Contact, conforme documentado na secção tutorial, [configurar permissões](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions)de política de segurança do processo de negócio. 

Atualizámos o nosso [guia tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) para refletir o novo suporte à versão API.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Os utilizadores com função de acesso predefinido estão agora no âmbito do provisionamento

**Tipo:** Alteração de recurso

**Categoria de serviço:** Provisionamento de Aplicativos

**Capacidade do produto:** Gestão do ciclo de vida da identidade

Historicamente, os utilizadores com a função de acesso predefinido têm estado fora de alcance para o provisionamento. Ouvimos feedback de que os clientes querem que os utilizadores com esta função estejam em campo para provisões. A partir de 16 de abril de 2020, todas as novas configurações de provisionamento permitem que os utilizadores com a função de acesso predefinido sejam a provisionados. Gradualmente, vamos alterar o comportamento das configurações de provisionamento existentes para apoiar o a provisionamento dos utilizadores com esta função. [Saiba mais.](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>UI de provisionamento atualizado

**Tipo:** Alteração de recurso

**Categoria de serviço:** Provisionamento de Aplicativos

**Capacidade do produto:** Gestão do ciclo de vida da identidade

Refrescamos a nossa experiência de provisão para criar uma visão de gestão mais focada. Quando navegar para a lâmina de provisionamento para uma aplicação empresarial que já foi configurada, poderá monitorizar facilmente o progresso do provisionamento e gestão de ações como iniciar, parar e reiniciar o provisionamento. [Saiba mais.](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>A validação da regra do Grupo Dinâmico já está disponível para visualização pública

**Tipo:** Alteração de recurso

**Categoria de serviço:** Gestão de Grupos

**Capacidade do produto:** Colaboração

O Azure Ative Directory (Azure AD) fornece agora os meios para validar regras dinâmicas de grupo. No separador **Regras Validadas,** pode validar a sua regra dinâmica contra membros do grupo de amostras para confirmar que a regra está a funcionar como esperado. Ao criar ou atualizar regras dinâmicas do grupo, os administradores querem saber se um utilizador ou um dispositivo será membro do grupo. Isto ajuda a avaliar se um utilizador ou dispositivo cumpre os critérios de regra e ajuda na resolução de problemas quando a adesão não é esperada.

Para obter mais informações, consulte [validar uma regra de adesão dinâmica do grupo (pré-visualização)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Pontuação Segura de Identidade - Incumprimentos de segurança e atualizações de ação de melhoria de MFA

**Tipo:** Alteração de recurso

**Categoria de serviço:** N/A

**Capacidade do produto:** Proteção & de Segurança de Identidade

**Incumprimentos de segurança para ações de melhoria da AD Azure:** O Microsoft Secure Score irá atualizar as ações de melhoria para suportar [falhas de segurança no AZure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults), que facilitam a proteção da sua organização com definições de segurança pré-configuradas para ataques comuns. Isto afetará as seguintes ações de melhoria:

- Certifique-se de que todos os utilizadores podem completar a autenticação de vários fatores para um acesso seguro
- Exigir MFA para funções administrativas
- Permitir a política para bloquear a autenticação do legado
 
**Atualizações de ação de melhoria do MFA:** Para refletir a necessidade de as empresas garantirem a segurança mais alta ao mesmo tempo que aplicam políticas que funcionam com o seu negócio, o Microsoft Secure Score removeu três ações de melhoria centradas na autenticação de vários fatores e adicionou duas.

Ações de melhoria removidas:

- Registe todos os utilizadores para autenticação multi-factor
- Pedir MFA para todos os utilizadores
- Exigir MFA para funções privilegiadas da Azure AD

Ações de melhoria adicionadas:

- Certifique-se de que todos os utilizadores podem completar a autenticação de vários fatores para um acesso seguro
- Exigir MFA para funções administrativas

Estas novas ações de melhoria requerem o registo dos seus utilizadores ou administradores para a autenticação de vários fatores (MFA) em todo o seu diretório e estabelecer o conjunto certo de políticas que se adequam às suas necessidades organizacionais. O objetivo principal é ter flexibilidade, garantindo ao mesmo tempo que todos os seus utilizadores e administradores podem autenticar com múltiplos fatores ou pedidos de verificação de identidade baseados no risco. Isto pode assumir a forma de ter múltiplas políticas que aplicam decisões âmbito, ou definir padrão de segurança (a partir de 16 de março) que permitem à Microsoft decidir quando desafiar os utilizadores para mFA. [Leia mais sobre as novidades no Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---

## <a name="march-2020"></a>Março de 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Contas de Diretório Ativo Azure não geridos em atualização B2B para março de 2021

**Tipo:** Plano de mudança  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
**A partir de 31 de março de 2021,** a Microsoft deixará de apoiar o resgate de convites através da criação de contas e inquilinos não geridos do Azure Ative Directory (Azure AD) para cenários de colaboração B2B. Na preparação para isso, encorajamo-lo a optar por [um e-mail](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)com a autenticação de código de acesso único.

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Os utilizadores com a função de acesso predefinido estarão em campo para o provisionamento

**Tipo:** Plano de mudança  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
Historicamente, os utilizadores com a função de acesso predefinido têm estado fora de alcance para o provisionamento. Ouvimos feedback de que os clientes querem que os utilizadores com esta função estejam em campo para provisões. Estamos a trabalhar na implementação de uma alteração para que todas as novas configurações de provisionamento permitam aos utilizadores com a função de acesso predefinido ser alocada. Gradualmente, vamos alterar o comportamento das configurações existentes para apoiar o a provisionamento dos utilizadores com esta função. Não é necessária qualquer ação do cliente. Publicaremos uma atualização da nossa [documentação](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) assim que esta alteração estiver em vigor.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>A colaboração Azure AD B2B estará disponível no Microsoft Azure operado por inquilinos 21Vianet (Azure China 21Vianet)

**Tipo:** Plano de mudança  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
As capacidades de colaboração Azure AD B2B serão disponibilizadas no Microsoft Azure operado por inquilinos 21Vianet (Azure China 21Vianet), permitindo aos utilizadores de um inquilino Azure China 21Vianet colaborarem perfeitamente com os utilizadores de outros inquilinos da Azure China 21Vianet. [Saiba mais sobre a colaboração Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B Colaboração email redesign

**Tipo:** Plano de mudança  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
Os [e-mails enviados](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) pelo serviço de convite de colaboração Azure AD B2B para convidar os utilizadores para o diretório serão redesenhados para tornar mais clara a informação do convite e os próximos passos do utilizador.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>As alterações de política do HomeRealmDiscovery aparecerão nos registos de auditoria

**Tipo:** Fixo  
**Categoria de serviço:** Auditoria  
**Capacidade do produto:** Relatório de & de Monitorização
 
Corrigimos um bug onde as alterações à [política homeRealmDiscovery](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) não foram incluídas nos registos de auditoria. Agora poderão ver quando e como a política foi alterada, e por quem. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - março de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Em março de 2020, adicionámos estas 51 novas aplicações com apoio da Federação à galeria de aplicações: 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Zoho One China](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co APP SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial), [iPoint Service Provider](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial), contexxt.ai [SPHERE](https://contexxt-sphere.com/login), Wisdom [By Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial), Flare [Digital Signage](https://spark-dev.pixelnebula.com/login), [Logz.io - Cloud Observability for Engineers](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial), [SpectrumU,](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial) [BizzContact,](https://bizzcontact.app/) [Elqano SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial), [MarketSignShare](http://www.signshare.com/), [CrossKnowledge Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial), [Netvision Compas](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial), [FCM HUB](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial), RIB [A/S Byggeweb Mobile,](https://apps.apple.com/us/app/docia/id529058757) [GoLinks,](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial) [Datadog](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial), [Zscaler B2B User Portal,](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial) [LIFT,](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial) [Planview Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial), [WatchTeams](https://www.devfinition.com/), [Aster](https://demo.asterapp.io/login), [Skills Workflow](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial), [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx), Plataforma [IP](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial), [InVision](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial), [Pipedrive](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial), [Showcase Workshop](https://app.showcaseworkshop.com/), [Greenlight Integration Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial), [Greenlight Compliant Access Management](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial), [Grok Learning](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial), [Miradore Online,](https://login.online.miradore.com/) [Khoros Care,](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial) [AskYourTeam,](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial) [TruNarrative,](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial) [Smartwaiver,](https://www.smartwaiver.com/m/user/sw_login.php?wms_login) [Bizagi Studio for Digital Process Automation](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial), [insuiteX](https://www.insuite.jp/), [sybo](https://www.systexsoftware.com.tw/), [Britive](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial), [WhosOffice](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial), [E-days](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial), [Kollective SDN](https://portal.kollective.app/login), [Witivio,](https://app.witivio.com/) [Playvox,](https://my.playvox.com/login) [Korn Ferry 360,](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial) [Campus Café,](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial) [Catchpoint,](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial) [Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](https://aka.ms/appstutorial) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](https://aka.ms/azureadapprequest)

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Colaboração Azure AD B2B disponível em inquilinos do Governo de Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
As funcionalidades de colaboração Azure AD B2B estão agora disponíveis entre alguns inquilinos do Governo de Azure.  Para saber se o seu inquilino é capaz de usar estas capacidades, siga as instruções em [Como posso dizer se a colaboração B2B está disponível no meu inquilino do Governo Azure US?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>A integração do Azure Monitor para logs Azure já está disponível no Governo de Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Relatório de & de Monitorização
 
A integração do Azure Monitor com os registos AZure AD está agora disponível no Governo de Azure. Pode encaminhar os Registos AD do Azure (Registos de Auditoria e Registos) para uma conta de armazenamento, Centro de Eventos e Analítico de Log. Por favor, verifique a [documentação detalhada,](https://aka.ms/aadlogsinamd) bem como [planos de implementação para relatórios e monitorização](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) para cenários Azure AD.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Atualização de Proteção de Identidade no Governo de Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Proteção & de Segurança de Identidade

Estamos entusiasmados por partilhar que lançámos agora a renovada experiência [de Proteção de Identidade Azure AD](https://aka.ms/IdentityProtectionDocs)   no portal do Governo microsoft [Azure](https://portal.azure.us/). Para mais informações, consulte o nosso [post de blog de anúncio.](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Recuperação de desastres: Descarregue e guarde a sua configuração de provisionamento

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
O serviço de fornecimento AD AZure fornece um rico conjunto de capacidades de configuração. Os clientes precisam de ser capazes de guardar a sua configuração para que possam referir-se a ela mais tarde ou reverter para uma versão conhecida. Adicionámos a capacidade de descarregar a sua configuração de provisionamento como um ficheiro JSON e carregá-la quando precisar. [Saiba mais](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>A SSPR (redefinição da palavra-passe de autosserviço) requer agora dois portões para administradores no Microsoft Azure operados pela 21Vianet (Azure China 21Vianet) 

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Reset da palavra-passe de autosserviço  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
Anteriormente no Microsoft Azure operado pela 21Vianet (Azure China 21Vianet), os administradores que usam a redefinição da palavra-passe de autosserviço (SSPR) para redefinir as suas próprias palavras-passe precisavam apenas de um "portal" (desafio) para provar a sua identidade. Em nuvens públicas e outras, os administradores geralmente devem usar dois portões para provar a sua identidade quando utilizam sSPR. Mas como não apoiámos SMS ou chamadas telefónicas no Azure China 21Vianet, permitimos que a palavra-passe de um portão seja reposta por administradores.

Estamos a criar paridade de recursos SSPR entre a Azure China 21Vianet e a nuvem pública. Para a frente, os administradores devem utilizar dois portões quando utilizarem SSPR. As notificações e códigos de aplicações por SMS, chamadas telefónicas e autenticadores serão suportados. [Saiba mais](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>O comprimento da palavra-passe é limitado a 256 caracteres

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
Para garantir a fiabilidade do serviço Azure AD, as palavras-passe do utilizador estão agora limitadas em comprimento a 256 caracteres. Os utilizadores com palavras-passe mais longas do que esta serão solicitados a alterar a sua palavra-passe no início de sessão subsequente, quer contactando a sua administração, quer utilizando a funcionalidade de reset da palavra-passe de autosserviço.

Esta alteração foi ativada no dia 13 de março de 2020, às 10:00 PST (18:00 UTC), sendo o erro AADSTS 50052, InvalidPasswordExceedsMaxLength. Consulte o [aviso de mudança de rutura](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) para mais detalhes.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Os registos de inscrição da AD AZure já estão disponíveis para todos os inquilinos gratuitos através do portal Azure

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Relatório de & de Monitorização
 
A partir de agora, os clientes que tenham inquilinos gratuitos podem aceder aos [registos de inscrição Azure AD do portal Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) por um máximo de 7 dias. Anteriormente, os registos de login só estavam disponíveis para clientes com licenças Azure Ative Directory Premium. Com esta mudança, todos os inquilinos podem aceder a estes registos através do portal.

> [!NOTE]
> Os clientes ainda precisam de uma licença premium (Azure Ative Directory Premium P1 ou P2) para aceder aos registos de login através da Microsoft Graph API e do Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Depreciação da opção de grupos em todo o diretório a partir de Configurações Gerais de Grupos no portal Azure

**Tipo:** Precatado  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração

Para proporcionar uma forma mais flexível de os clientes criarem grupos de diretório que melhor satisfaçam as suas necessidades, substituímos a opção grupos de **diretório** das configurações gerais dos **grupos**  >  **General** no portal Azure por um link para [a documentação dinâmica do grupo.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) Melhorámos a nossa documentação para incluir mais instruções para que os administradores possam criar grupos de todos os utilizadores que incluam ou excluam os utilizadores convidados.

---

## <a name="february-2020"></a>Fevereiro de 2020

### <a name="upcoming-changes-to-custom-controls"></a>Próximas alterações aos controlos personalizados

**Tipo:** Plano de mudança  
**Categoria de serviço:** MFA  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
Estamos a planear substituir a pré-visualização dos controlos personalizados atuais por uma abordagem que permite que as capacidades de autenticação fornecidas pelos parceiros funcionem perfeitamente com o administrador do Azure Ative Directory e experiências finais do utilizador. Hoje, as soluções parceiras da MFA enfrentam as seguintes limitações: só funcionam depois de uma palavra-passe ter sido inserida; não servem como MFA para autenticação de escalões noutros cenários-chave; e não se integram com funções de administração de utilizador final ou de gestão de credenciais administrativas. A nova implementação permitirá que os fatores de autenticação fornecidos pelos parceiros trabalhem em conjunto com fatores integrados para cenários-chave, incluindo registo, utilização, reclamações de MFA, aumento da autenticação, reporte e registo. 

Os controlos personalizados continuarão a ser suportados na pré-visualização, juntamente com o novo design até atingir a disponibilidade geral. Nessa altura, daremos tempo aos clientes para migrarem para o novo design. Devido às limitações da abordagem atual, não vamos embarcar novos fornecedores até que o novo design esteja disponível. Estamos a trabalhar em estreita colaboração com clientes e fornecedores e comunicaremos a cronologia à medida que nos aproximamos. [Saiba mais](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identity Secure Score - Atualizações de ação de melhoria do MFA

**Tipo:** Plano de mudança  
**Categoria de serviço:** MFA  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
Para refletir a necessidade de as empresas garantirem a segurança mais alta ao mesmo tempo que aplicam políticas que funcionam com o seu negócio, o Microsoft Secure Score está a remover três ações de melhoria centradas na autenticação de vários fatores (MFA) e a adicionar duas.

Serão eliminadas as seguintes ações de melhoria:

- Registar todos os utilizadores para MFA
- Pedir MFA para todos os utilizadores
- Exigir MFA para funções privilegiadas da Azure AD

Serão adicionadas as seguintes ações de melhoria:

- Certifique-se de que todos os utilizadores podem completar MFA para acesso seguro
- Exigir MFA para funções administrativas

Estas novas ações de melhoria exigirão registar os seus utilizadores ou administradores para MFA em todo o seu diretório e estabelecer o conjunto certo de políticas que se adequam às suas necessidades organizacionais. O objetivo principal é ter flexibilidade, garantindo ao mesmo tempo que todos os seus utilizadores e administradores podem autenticar com múltiplos fatores ou pedidos de verificação de identidade baseados no risco. Isto pode assumir a forma de definir padrão de segurança que permite à Microsoft decidir quando desafiar os utilizadores para MFA, ou ter várias políticas que aplicam decisões de âmbito. Como parte destas atualizações de ação de melhoria, as políticas de proteção de base deixarão de ser incluídas nos cálculos de pontuação. [Leia mais sobre o que está a chegar no Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Seleção SKU de Serviços de Domínio Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Serviços de domínio Azure Ad  
**Capacidade do produto:** Serviços de domínio Azure Ad
 
Ouvimos feedback de que os clientes da Azure AD Domain Services querem mais flexibilidade na seleção dos níveis de desempenho para as suas instâncias. A partir de 1 de fevereiro de 2020, mudamos de um modelo dinâmico (onde a Azure AD determina o nível de desempenho e preços baseado na contagem de objetos) para um modelo de autosseleção. Agora os clientes podem escolher um nível de desempenho que corresponda ao seu ambiente. Esta alteração permite-nos também ativar novos cenários como As Florestas de Recursos e funcionalidades Premium como backups diários. A contagem de objetos é agora ilimitada para todos os SKUs, mas continuaremos a oferecer sugestões de contagem de objetos para cada nível.

**Não é necessária qualquer ação imediata do cliente.** Para os clientes existentes, o nível dinâmico que estava em uso a 1 de fevereiro de 2020, determina o novo nível de incumprimento. Não existe um impacto de preços ou desempenho como resultado desta alteração. Para a frente, os clientes da Azure AD DS terão de avaliar os requisitos de desempenho à medida que o seu tamanho de diretório e as suas características de carga de trabalho mudarem. A troca entre os níveis de serviço continuará a ser uma operação sem tempo de inatividade, e deixaremos de transferir automaticamente os clientes para novos níveis com base no crescimento do seu diretório. Além disso, não haverá aumentos de preços, e novos preços irão alinhar-se com o nosso modelo de faturação atual. Para obter mais informações, consulte a [documentação Azure AD DS SKUs](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) e a [página de preços dos Serviços de Domínio AZure AD](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - fevereiro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Em fevereiro de 2020, adicionámos estas 31 novas aplicações com apoio da Federação à galeria de aplicações: 

[Plataforma de Patentes IamIP](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [NS1 SSO For Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), Em caso de crise - Portal [On-line,](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial) [BIC Cloud Design,](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial) [Apicultor Azure AD Data Connector,](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial) [Korn Ferry Assessments,](https://www.kornferry.com/solutions/kf-digital/kf-assess) [Verkada Command,](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial) [Splashtop,](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial) [Syxsense,](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial) [EAB Navigate,](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial) [Nova Relíquia (Lançamento Limitado)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium](https://admin.thulium.com/login/instance), [Gestor de Bilhetes,](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial)Escolha [de Modelos para Equipas,](https://links.officeatwork.com/templatechooser-download-teams) [Beesy,](https://www.beesy.me/index.php/site/login) [Sistema de Suporte de Saúde,](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial) [MURAL,](https://app.mural.co/signup) [Colmeia,](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial) [LavaDo,](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview) [Wakelet,](https://wakelet.com/login) [Firmex VDR,](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial) [ThingLink para Professores e Escolas,](https://www.thinglink.com/) [Coda,](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial) [NearpodApp,](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product) [WEDO,](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial) [InvitePeople,](https://invitepeople.com/login) [Reprints Desk - Artigo Galaxy,](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial) [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](https://aka.ms/appstutorial) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](https://aka.ms/azureadapprequest)

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Novos conectores de provisionamento na Galeria de Aplicações AD Azure - fevereiro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud da Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte [automatizar o fornecimento de utilizadores para aplicações SaaS com Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Suporte AZURE AD para chaves de segurança FIDO2 em ambientes híbridos

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
Estamos a anunciar a pré-visualização pública do suporte da AD Azure para chaves de segurança FIDO2 em ambientes híbridos. Os utilizadores podem agora utilizar as chaves de segurança FIDO2 para iniciar súbs nos seus dispositivos Híbrido Azure AD e obter um sinal de saúde sem emenda nos seus locais e recursos na nuvem. O suporte aos ambientes Híbridos tem sido a característica mais requisitada dos nossos clientes sem palavras-passe desde que inicialmente lançámos a pré-visualização pública para suporte FIDO2 em dispositivos aderidos a Azure AD. A autenticação sem palavras-passe utilizando tecnologias avançadas como a biometria e a criptografia de chaves públicas/privadas proporcionam comodidade e facilidade de utilização enquanto estão seguros. Com esta pré-visualização pública, você pode agora usar a autenticação moderna como chaves de segurança FIDO2 para aceder aos recursos tradicionais do Ative Directory. Para mais informações, aceda [ao SSO para obter recursos no local.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises) 

Para começar, visite [as chaves de segurança FIDO2 para o seu inquilino](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) para obter instruções passo a passo. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>A nova experiência My Account está agora geralmente disponível

**Tipo:** Alteração de recurso  
**Categoria de serviço:** O meu perfil/conta  
**Capacidade do produto:** Experiências de utilizador final
 
A Minha Conta, o balcão único para todas as necessidades de gestão de conta de utilizador final, está agora geralmente disponível! Os utilizadores finais podem aceder a este novo site via URL, ou no cabeçalho da nova experiência My Apps. Saiba mais sobre todas as capacidades de self-service que a nova experiência oferece no [My Account Portal Overview](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>URL do site da minha conta atualizando para myaccount.microsoft.com

**Tipo:** Alteração de recurso  
**Categoria de serviço:** O meu perfil/conta  
**Capacidade do produto:** Experiências de utilizador final
 
A nova experiência do utilizador final my Account irá atualizar o seu URL para `https://myaccount.microsoft.com` o próximo mês. Encontre mais informações sobre a experiência e todas as capacidades de self-service da conta que oferece aos utilizadores finais no [portal My Account.](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)

---
 
## <a name="january-2020"></a>Janeiro de 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>O novo portal My Apps está disponível em geral

**Tipo:** Plano de mudança  
**Categoria de serviço:** As minhas apps  
**Capacidade do produto:** Experiências de utilizador final
 
Atualize a sua organização para o novo portal My Apps que está disponível em geral! Encontre mais informações sobre o novo portal e coleções na [Create collections no portal My Apps.](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Espaços de trabalho em Azure AD foram renomeados para coleções

**Tipo:** Alteração de recurso  
**Categoria de serviço:** As minhas apps   
**Capacidade do produto:** Experiências de utilizador final
 
Os espaços de trabalho, os administradores de filtros podem configurar para organizar as aplicações dos seus utilizadores, passarão a ser referidos como coleções. Encontre mais informações sobre como configurá-las nas [coleções Create no portal My Apps](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C Inscrição e inscrição no telefone usando a política personalizada (Visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão da Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C
 
Com a inscrição e a inscrição no número de telefone, os desenvolvedores e empresas podem permitir que os seus clientes se inscrevam e se inscrevam através de uma senha única enviada para o número de telefone do utilizador através de SMS. Esta funcionalidade também permite que o cliente altere o seu número de telefone se perder o acesso ao seu telemóvel. Com o poder das políticas personalizadas, a inscrição por telefone e a inscrição permitem aos programadores e empresas comunicar a sua marca através da personalização da página. Descubra como [configurar a inscrição no telefone e iniciar sôm-se com as políticas personalizadas em Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Novos conectores de provisionamento na Galeria de Aplicações AD Azure - janeiro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte [automatizar o fornecimento de utilizadores para aplicações SaaS com Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - janeiro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Em janeiro de 2020, adicionámos estas 33 novas aplicações com apoio da Federação à galeria de aplicações: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [FastLy Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly,](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial) [LeaveBot,](https://leavebot.io/#home) [DataCamp,](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial) [TripActions,](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial) [SmartWork,](https://www.intumit.com/english/SmartWork.html) [Dotcom-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN - Azure AD SSO Gateway Oracle for E-Business Suite - EBS, PeopleSoft, e JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [Hospedado MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management Platform,](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial) [LumApps](https://sites.lumapps.com/login), [Upwork Enterprise,](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial) [Talentsoft,](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial) [SmartDB for Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient Conduct Manager Software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), [Squelch Cloud Office365 Connector](https://laxmi.squelch.io/login), [PingFlow Authentication](https://app-staging.pingview.io/), [PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai](https://app.sandwai.com/), [EZRentOut,](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial) [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](https://aka.ms/appstutorial) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](https://aka.ms/azureadapprequest)

---

### <a name="two-new-identity-protection-detections"></a>Duas novas deteções de Proteção de Identidade

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
Adicionámos dois novos tipos de deteção ligados à Proteção de Identidade: regras suspeitas de manipulação de caixas de entrada e viagens impossíveis. Estas deteções offline são descobertas pela Microsoft Cloud App Security (MCAS) e influenciam o utilizador e o risco de acesso à proteção de identidade. Para obter mais informações sobre estas deteções, consulte os nossos [tipos de risco de inscrição.](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Breaking Change: Os fragmentos URI não serão transportados através do redirecionamento de login

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
A partir de 8 de fevereiro de 2020, quando um pedido é enviado para login.microsoftonline.com para assinar um utilizador, o serviço anexará um fragmento vazio ao pedido.  Isto impede uma classe de ataques de redirecionamento, garantindo que o navegador elimina qualquer fragmento existente no pedido. Nenhuma aplicação deve ter uma dependência deste comportamento. Para obter mais informações, consulte [Breaking changes](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) na documentação da plataforma de identidade da Microsoft.

