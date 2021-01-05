---
title: Arquivo para o que há de novo no Azure Ative Directory? | Microsoft Docs
description: As novas notas de lançamento na secção Visão Geral deste conjunto de conteúdos contêm 6 meses de atividade. Após 6 meses, os artigos são removidos do artigo principal e colocados neste artigo de arquivo.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: e90adcb5312df6e1dfe6d4b58407edeb924a8809
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862443"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Arquivo para o que há de novo no Azure Ative Directory?

O artigo [de notas de lançamento do Azure Ative é o](whats-new.md) qual contém atualizações para os últimos seis meses, enquanto este artigo contém todas as informações mais antigas.

O que há de novo no Diretório Ativo Azure? As notas de lançamento fornecem informações sobre:

- Os últimos lançamentos
- Problemas conhecidos
- Correções de erros
- Funcionalidade preterida
- Planos para alterações

---
## <a name="june-2020"></a>Junho de 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Condição de risco do utilizador na política de acesso condicional

**Tipo:** Plano de mudança  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 

O suporte ao risco do utilizador na política de acesso condicionado Azure AD permite-lhe criar várias políticas baseadas no risco do utilizador. Diferentes níveis mínimos de risco do utilizador podem ser necessários para diferentes utilizadores e aplicações. Com base no risco do utilizador, pode criar políticas para bloquear o acesso, exigir autenticação de vários fatores, alterar palavras-passe seguras ou redirecionar para a Microsoft Cloud App Security para impor a política de sessão, como auditoria adicional.

A condição de risco do utilizador requer Azure AD Premium P2 porque utiliza a Azure Identity Protection, que é uma oferta P2. para obter mais informações sobre o acesso condicional, consulte a [documentação de Acesso Condicional AD Azure.](../conditional-access/index.yml)

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO agora suporta apps que exigem que SPNameQualifier seja definido quando solicitado

**Tipo:** Fixo  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** SSO
 
Algumas aplicações SAML exigem que o SPNameQualifier seja devolvido no sujeito de afirmação quando solicitado. Agora, a Azure AD responde corretamente quando um SPNameQualifier é solicitado na política nameID do pedido. Isto também funciona para a sed-in iniciada pela SP, e o IdP iniciou a s inscrição.  Para saber mais sobre o protocolo SAML no Azure Ative Directory, consulte [o protocolo Single Sign-On SAML](../develop/single-sign-on-saml-protocol.md).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>A Azure AD B2B Collaboration apoia convidar utilizadores da MSA e do Google em inquilinos do Governo de Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 

Os inquilinos do Governo Azure que usam as funcionalidades de colaboração B2B podem agora convidar os utilizadores que tenham uma conta Microsoft ou Google. Para saber se o seu inquilino pode usar estas capacidades, siga as instruções em [Como posso dizer se a colaboração B2B está disponível no meu inquilino do Governo Azure US?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>O objeto do utilizador no MS Graph v1 agora inclui propriedades externalUserState e externalUserStateChangedDateTime

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 

As propriedades externalUserState e externalUserStateChangedDateTime podem ser usadas para encontrar hóspedes B2B convidados que ainda não aceitaram os seus convites, bem como construir automatização, como eliminar utilizadores que não aceitaram os seus convites após alguns dias. Estas propriedades estão agora disponíveis no MS Graph v1. Para obter orientações sobre a utilização destas propriedades, consulte o [tipo de recurso do Utilizador](/graph/api/resources/user).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Gerir sessões de autenticação em Azure AD Conditional Access já está disponível geralmente

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
As capacidades de gestão da sessão de autenticação permitem-lhe configurar a frequência com que os seus utilizadores precisam de fornecer credenciais de inscrição e se precisam de fornecer credenciais após o fecho e reabertura dos navegadores para oferecer mais segurança e flexibilidade no seu ambiente.
 
Adicionalmente, a gestão da sessão de autenticação utilizada para aplicar apenas à autenticação do Primeiro Fator em AD Azure juntou-se, a Hybrid Azure AD juntou-se e a Azure AD dispositivos registados. Agora a gestão da sessão de autenticação aplicar-se-á também ao MFA. Para mais informações, consulte a gestão da [sessão de autenticação configurada com Acesso Condicional.](../conditional-access/howto-conditional-access-session-lifetime.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Novas Aplicações Federadas disponíveis na galeria AZure AD Application - junho de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Em junho de 2020 adicionámos as seguintes 29 novas aplicações na nossa galeria app com o apoio da Federação:

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [MailGates](../saas-apps/mailgates-tutorial.md), [BullseyeTDP,](../saas-apps/bullseyetdp-tutorial.md) [Raketa,](../saas-apps/raketa-tutorial.md)Segmento , [Ai](../saas-apps/segment-tutorial.md) [Auditor,](https://www.mindbridge.ai/products/ai-auditor/) [Pobuca Connect,](https://app.pobu.ca/) [Proto.io,](../saas-apps/proto.io-tutorial.md) [Gatekeeper,](https://www.gatekeeperhq.com/) [Hub Planner,](../saas-apps/hub-planner-tutorial.md) [Ansira-Partner Go-to-Market Toolbox,](https://ansira.com/technology/channel-engagement) [IBM Digital Business Automation on Cloud,](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md) [Kisi Physical Security,](../saas-apps/kisi-physical-security-tutorial.md) [ViewpointOne,](https://team.viewpoint.com/) [IntelligenceBank,](../saas-apps/intelligencebank-tutorial.md) [pymetric (](../saas-apps/pymetrics-tutorial.md) [Zero](https://www.teamzero.com/), [InStation](https://instation.invillia.com/), [edX for Business SAML 2.0 Integração](../saas-apps/edx-for-business-saml-integration-tutorial.md), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](../saas-apps/smartkargo-tutorial.md), [PKIsigning platform](https://platform.pkisigning.nl/), [SiteIntel](../saas-apps/siteintel-tutorial.md), [Field iD,](../saas-apps/field-id-tutorial.md) [Curricula SAML](../saas-apps/curricula-saml-tutorial.md), [Perforce Helix Core - Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md), [MyCompliance Cloud](https://cloud.metacompliance.com/), [Smallstep SSH](https://smallstep.com/sso-ssh/)  

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

Para obter mais informações sobre todas as experiências possíveis com conectores API, consulte [utilize conectores API para personalizar e alargar a inscrição de self-service](../external-identities/api-connectors-overview.md), ou [personalize o autosserviço de identidades externas com integrações de API web.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)
 
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
 
Uma nova permissão delegada EntitlementManagement.Read.All está agora disponível para uso com a API de Gestão de Direitos na versão beta do Microsoft Graph. Para saber mais sobre as APIs disponíveis, consulte [Trabalhar com a Azure AD gestão de direitos API.](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta&preserve-view=true)

---

### <a name="identity-protection-apis-available-in-v10"></a>APIs de Proteção de Identidade disponíveis em v1.0

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
Os riskyUsers e riskDetections Microsoft Graph APIs estão agora geralmente disponíveis. Agora que estão disponíveis no ponto final v1.0, convidamo-lo a usá-los em produção. Para mais informações, consulte os docs do [Microsoft Graph](/graph/api/resources/identityprotectionroot).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Rótulos de sensibilidade para aplicar políticas aos grupos Microsoft 365 estão agora geralmente disponíveis

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração
 

Pode agora criar etiquetas de sensibilidade e utilizar as definições de etiquetas para aplicar políticas a grupos Microsoft 365, incluindo a política de privacidade (Pública ou Privada) e política externa de acesso ao utilizador. Pode criar um rótulo com a política de privacidade para ser privado e política externa de acesso ao utilizador para não permitir adicionar utilizadores convidados. Quando um utilizador aplica esta etiqueta a um grupo, o grupo será privado e nenhum utilizador convidado pode ser adicionado ao grupo. 

As etiquetas de sensibilidade são importantes para proteger os seus dados críticos do negócio e permitir-lhe gerir grupos em escala, de forma compatível e segura. Para obter orientações sobre a utilização de etiquetas de sensibilidade, consulte [os rótulos de sensibilidade atribuir aos grupos Microsoft 365 no Azure Ative Directory (pré-visualização)](../enterprise-users/groups-assign-sensitivity-labels.md).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Atualizações para suporte para o Gestor de Identidade da Microsoft para clientes Azure AD Premium

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Gestor de Identidade da Microsoft  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
O Azure Support está agora disponível para componentes de integração AD do Microsoft Identity Manager 2016, até ao final do Suporte Alargado para o Microsoft Identity Manager 2016. Leia mais na [atualização de Suporte para clientes Azure AD Premium utilizando o Microsoft Identity Manager](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>A utilização de condições de adesão ao grupo na configuração de sinistros SSO é aumentada

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** SSO
 
Anteriormente, o número de grupos que poderia utilizar quando alterou condicionalmente as reclamações com base na adesão ao grupo dentro de qualquer configuração de aplicação única estava limitado a 10. A utilização das condições de adesão ao grupo na configuração de sinistros SSO aumentou agora para um máximo de 50 grupos. Para obter mais informações sobre como configurar reclamações, consulte a [configuração de reclamações SSO de aplicações empresariais](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Habilitando formatação básica no componente Sign In Page Text em Branding da empresa.

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
A funcionalidade de Marca da Empresa na experiência de login Azure AD/Microsoft 365 foi atualizada para permitir ao cliente adicionar hiperligações e formatação simples, incluindo fonte arrojada, sublinhado e itálico. Para obter orientações sobre a utilização desta funcionalidade, consulte [adicionar a marca à página de início de súming do Azure Ative Directory da sua organização.](./customize-branding.md)

---

### <a name="provisioning-performance-improvements"></a>Melhorias no desempenho do provisionamento

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
O serviço de fornecimento foi atualizado para reduzir o tempo para que um [ciclo incremental](../app-provisioning/how-provisioning-works.md#incremental-cycles) esteja concluído. Isto significa que os utilizadores e grupos serão a provisionados nas suas aplicações mais rapidamente do que eram anteriormente. Todos os novos postos de trabalho criados após 6/10/2020 beneficiarão automaticamente das melhorias de desempenho. Quaisquer aplicações configuradas para provisão antes de 6/10/2020 terão de ser reiniciadas uma vez após 6/10/2020 para tirar partido das melhorias de desempenho. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Anunciando a depreciação da Paridade de Gráficos ADAL e MS

**Tipo:** Precatado  
**Categoria de serviço:** N/A  
**Capacidade do produto:** Gestão do ciclo de vida do dispositivo

Agora que as Bibliotecas de Autenticação da Microsoft (MSAL) estão disponíveis, deixaremos de adicionar novas funcionalidades às Bibliotecas de Autenticação ativa do Azure Ative (ADAL) e terminaremos com as correções de segurança a 30 de junho de 2022. Para obter mais informações sobre como migrar para o MSAL, consulte [as aplicações migrar para a Microsoft Authentication Library (MSAL)](../develop/msal-migration.md).

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
 

Atualmente, os utilizadores que estão em domínios federados em AZure AD, mas que não estão sincronizados com o inquilino, não podem aceder às Equipas. A partir do final de junho, esta nova capacidade irá permitir-lhes fazê-lo, alargando a funcionalidade de inscrição verificada por e-mail existente. Isto permitirá aos utilizadores que possam iniciar sposição num IdP federado, mas que ainda não tenham um objeto de utilizador no ID Azure, tenham um objeto de utilizador criado automaticamente e sejam autenticados para equipas. O objeto do utilizador será marcado como "self-service sign up". Esta é uma extensão da capacidade existente para fazer auto-inscrição verificada por e-mail que os utilizadores em domínios geridos podem fazer e podem ser controlados usando a mesma bandeira. Esta alteração completa durante os dois meses seguintes. Atenção às atualizações de documentação [aqui.](../enterprise-users/directory-self-service-signup.md)
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Correção seguinte: O documento de descoberta OIDC para a nuvem do Governo Azure está a ser atualizado para fazer referência aos pontos finais corretos do Gráfico.

**Tipo:** Plano de mudança  
**Categoria de serviço:** Nuvens Soberanas  
**Capacidade do produto:** Autenticação do Utilizador
 
A partir de junho, o documento de descoberta OIDC A plataforma de identidade microsoft e o [protocolo OpenID Connect](../develop/v2-protocols-oidc.md) no ponto final da nuvem do Governo [Azure](../develop/authentication-national-cloud.md) (login.microsoftonline.us), começarão a devolver o ponto final de [gráfico de nuvem nacional](/graph/deployments) correto ( ou , com base no inquilino https://graph.microsoft.us https://dod-graph.microsoft.us) fornecido.  Atualmente, fornece o ponto final do gráfico incorreto (graph.microsoft.com) "msgraph_host".  

Esta correção de insetos será lançada gradualmente ao longo de aproximadamente 2 meses.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Os utilizadores do Governo do Azure deixarão de poder inscrever-se no login.microsoftonline.com

**Tipo:** Plano de Mudança  
**Categoria de serviço:** Nuvens Soberanas  
**Capacidade do produto:** Autenticação do Utilizador
 
Em 1 de junho de 2018, a Autoridade Oficial do Azure Ative Directory (Azure AD) para o Governo Azure passou de https://login-us.microsoftonline.com https://login.microsoftonline.us . Se possuir uma aplicação dentro de um inquilino do Governo Azure, deve atualizar a sua aplicação para iniciar seduções nos utilizadores no ponto final .us.

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
 
Os administradores de TI podem começar a usar o novo papel de "Hybrid Admin" como o papel menos privilegiado para a criação do Azure AD Connect Cloud Provisioning. Com este novo papel, já não tens de usar o papel da Administração Global para configurar e configurar o Cloud Provisioning. [Saiba mais](../roles/delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Novas Aplicações Federadas disponíveis na galeria AZure AD Application - maio 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Em maio de 2020, adicionámos as seguintes 36 novas aplicações na nossa galeria de aplicações com o apoio da Federação:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell Teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [ASC Recording Insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-Engaged](https://b-engaged.se/), [Competella Contact Center Workgroup](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft Identity](https://identity.imagesoftinc.com/), My [IBISWorld](https://identity.imagesoftinc.com/), [insuite,](../saas-apps/insuite-tutorial.md) [Change Process Management](../saas-apps/change-process-management-tutorial.md), [Cyara CX Assurance Platform](../saas-apps/cyara-cx-assurance-platform-tutorial.md), Smart Global [Governance](../saas-apps/smart-global-governance-tutorial.md), [Prezi,](../saas-apps/prezi-tutorial.md) [Mapbox](../saas-apps/mapbox-tutorial.md), [Datava Enterprise Service Plataforma](../saas-apps/datava-enterprise-service-platform-tutorial.md), [Whimsical](../saas-apps/whimsical-tutorial.md), [Trelica,](../saas-apps/trelica-tutorial.md) [EasySSO for Confluence](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO for BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md), [EasySSO for Bamboo](../saas-apps/easysso-for-bamboo-tutorial.md), [Torii,](../saas-apps/torii-tutorial.md) [Axiad Cloud,](../saas-apps/axiad-cloud-tutorial.md) [Humanage,](../saas-apps/humanage-tutorial.md) [ColorTokens ZTNA,](../saas-apps/colortokens-ztna-tutorial.md) [CCH Tagetik,](../saas-apps/cch-tagetik-tutorial.md) [ShareVault,](../saas-apps/sharevault-tutorial.md) [Vyond,](../saas-apps/vyond-tutorial.md) [TextExpander,](../saas-apps/textexpander-tutorial.md) [Anyone Home CRM,](../saas-apps/anyone-home-crm-tutorial.md) [askSpoke](../saas-apps/askspoke-tutorial.md), [Ice Contact Center](../saas-apps/ice-contact-center-tutorial.md)

Pode também encontrar a documentação de todas as candidaturas a partir https://aka.ms/AppsTutorial daqui.

Para listar a sua aplicação na galeria de aplicações Azure AD, leia os detalhes https://aka.ms/AzureADAppRequest aqui.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>O modo apenas de reporte para acesso condicional está geralmente disponível

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Proteção & de Segurança de Identidade

[O modo apenas de reporte para acesso condicionado Azure AD](../conditional-access/concept-conditional-access-report-only.md) permite-lhe avaliar o resultado de uma política sem impor controlos de acesso. Pode testar políticas apenas de relatórios em toda a sua organização e compreender o seu impacto antes de as permitir, tornando a implementação mais segura e fácil. Ao longo dos últimos meses, temos visto uma forte adoção do modo apenas de relatórios - mais de 26M utilizadores já estão no âmbito de uma política apenas de relatório. Com o anúncio de hoje, novas políticas de acesso condicionado Azure AD serão criadas em modo apenas de relatório por padrão. Isto significa que pode monitorizar o impacto das suas políticas a partir do momento em que são criadas. E para aqueles que usam as APIs do MS Graph, também pode [gerir políticas de relatórios programáticas.](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) 

---

### <a name="self-service-sign-up-for-guest-users"></a>Inscrição de self-service para utilizadores convidados

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
Com identidades externas em Azure AD, pode permitir que pessoas fora da sua organização acedam às suas apps e recursos, deixando-as assinar em qualquer identidade que prefiram. Ao partilhar uma aplicação com utilizadores externos, pode nem sempre saber com antecedência quem irá precisar de acesso à aplicação. Com [a inscrição de self-service,](../external-identities/self-service-sign-up-overview.md)pode permitir que os utilizadores convidados se inscrevam e obtenham uma conta de hóspedes para as suas aplicações de negócio (LOB). O fluxo de inscrição pode ser criado e personalizado para suportar Azure AD e identidades sociais. Também pode recolher informações adicionais sobre o utilizador durante a inscrição.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Os relatórios de acesso condicional e relatórios estão geralmente disponíveis

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Proteção & de Segurança de Identidade

O [livro de insights e relatórios](../conditional-access/howto-conditional-access-insights-reporting.md) dá aos administradores uma visão sumária do Acesso Condicional Azure AD no seu inquilino. Com a capacidade de selecionar uma política individual, os administradores podem entender melhor o que cada política faz e monitorizar quaisquer mudanças em tempo real. O livro transmite dados armazenados no Azure Monitor, que pode configurar em poucos minutos [seguindo estas instruções](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). Para tornar o painel mais detetável, mudámo-lo para o novo separador de insights e relatórios dentro do menu Azure AD Conditional Access.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>A lâmina de detalhes de política para acesso condicional está em pré-visualização pública

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Proteção & de Segurança de Identidade

A [lâmina de novos detalhes da política](../conditional-access/troubleshoot-conditional-access.md) exibe as atribuições, condições e controlos satisfeitos durante a avaliação da política de acesso condicional. Pode aceder à lâmina selecionando uma linha nos separadores de Acesso Condicional ou relatório apenas nos detalhes de Iniciar sing.in.

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
 
O suporte para criar e configurar uma aplicação a partir da Galeria AD Azure usando APIs ms graph em Beta já está disponível. Se precisar de configurar um único sinal de saturação baseado em SAML para várias instâncias de uma aplicação, economize tempo utilizando as APIs do Gráfico do Microsoft para [automatizar a configuração de um único sinal baseado em SAML](/graph/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Novos conectores de provisionamento na Galeria de Aplicações AD Azure - maio de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Integração de Terceiros
 
Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic por Organização](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infrastructure-console-provisioning-tutorial.md)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte [automatizar o fornecimento de utilizadores para aplicações SaaS com Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="saml-token-encryption-is-generally-available"></a>A encriptação token SAML está geralmente disponível

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** SSO
 
[A encriptação do token SAML](../manage-apps/howto-saml-token-encryption.md) permite que as aplicações sejam configuradas para receber afirmações SAML encriptadas. A funcionalidade encontra-se agora geralmente disponível em todas as nuvens.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Reclamações de nome de grupo em fichas de aplicação estão geralmente disponíveis

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** SSO
 
As alegações do grupo emitidas num token podem agora limitar-se apenas aos grupos afetados ao pedido.  Isto é especialmente importante quando os utilizadores são membros de um grande número de grupos e existe o risco de exceder os limites de tamanho dos símbolos. Com esta nova capacidade em vigor, a capacidade de [adicionar nomes de grupo a tokens](../hybrid/how-to-connect-fed-group-claims.md) está geralmente disponível.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Workday Writeback agora suporta a definição de atributos de número de telefone de trabalho

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
Melhorámos a aplicação de provisionamento workday Writeback para agora suportar a redução do número de telefone de trabalho e dos atributos do número de telemóvel. Além do e-mail e nome de utilizador, pode agora configurar a app de provisionamento de writeback workday para fluir os valores do número de telefone de Azure AD para Workday. Para obter mais detalhes sobre como configurar a gravação do número de telefone, consulte o tutorial da aplicação [Workday Writeback.](../saas-apps/workday-writeback-tutorial.md) 

---

### <a name="publisher-verification-preview"></a>Verificação do Editor (pré-visualização)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Experiência de Desenvolvedor
 
A verificação do editor (pré-visualização) ajuda os administradores e os utilizadores finais a compreender a autenticidade dos desenvolvedores de aplicações que se integram na plataforma de identidade da Microsoft. Para mais informações, consulte a [verificação do Editor (pré-visualização)](../develop/publisher-verification-overview.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Fluxo de código de autorização para aplicações de página única

**Tipo:** Categoria de serviço de funcionalidade **alterada:** Capacidade do produto de autenticação: Experiência do desenvolvedor 

Devido às [restrições modernas de cookies do navegador 3º partido, como o Safari ITP,](../develop/reference-third-party-cookies-spas.md)as SPAs terão de utilizar o fluxo de código de autorização em vez do fluxo implícito para manter o SSO; MSAL.js v 2.x irá agora suportar o fluxo de código de autorização. Existem como atualizações correspondentes ao portal Azure para que possa atualizar o seu SPA para ser tipo "spa" e utilizar o fluxo de código auth. Para obter orientações, consulte [o Quickstart: Inicie sedudas nos utilizadores e obtenha um token de acesso num SPA JavaScript utilizando o fluxo de código auth](../develop/quickstart-v2-javascript-auth-code.md).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Filtragem melhorada para dispositivos está em visualização pública

**Tipo:** Recurso alterado   
**Categoria de serviço:** Capacidade do produto de gestão de **dispositivos:** Gestão do ciclo de vida do dispositivo
 
Anteriormente, os únicos filtros que poderia utilizar eram "Ativado" e "Data de Atividade". Agora, pode [filtrar a sua lista de dispositivos em mais propriedades](../devices/device-management-azure-portal.md#device-list-filtering-preview), incluindo tipo DE, tipo de junção, conformidade e muito mais. Estas adições devem simplificar a localização de um determinado dispositivo.

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

Para mais informações, visite [a nova experiência de registo de aplicações para Azure AD B2C.](../../active-directory-b2c/app-registrations-training-guide.md)

---
## <a name="april-2020"></a>Abril de 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>A experiência combinada de registo de informações de segurança está agora geralmente disponível

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Autenticações (Logins)

**Capacidade do produto:** Proteção & de Segurança de Identidade

A experiência de registo combinado para autenticação multi-factor (MFA) e Self-Service Redefinição de Password (SSPR) está agora geralmente disponível. Esta nova experiência de registo permite que os utilizadores se registem para MFA e SSPR num único processo passo a passo. Quando implementa a nova experiência para a sua organização, os utilizadores podem registar-se em menos tempo e com menos agios. Confira o post do blog [aqui.](https://bit.ly/3etiRyQ)

---

### <a name="continuous-access-evaluation"></a>Avaliação de Acesso Contínuo

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Autenticações (Logins)

**Capacidade do produto:** Proteção & de Segurança de Identidade

A Avaliação contínua de Acesso é uma nova funcionalidade de segurança que permite a aplicação quase em tempo real de políticas em partes dependentes que consomem Tokens de Acesso AD Azure quando os eventos acontecem em Azure AD (como a eliminação de conta de utilizador). Estamos a lançar esta funcionalidade primeiro para os clientes Teams e Outlook. Para mais detalhes, leia o nosso [blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) e [documentação.](../conditional-access/concept-continuous-access-evaluation.md)

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>Sação POR SMS: Os trabalhadores da Firstline podem iniciar sação em aplicações apoiadas pelo AZure com o seu número de telefone e sem senha

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Autenticações (Logins)

**Capacidade do produto:** Autenticação do Utilizador

O Office está a lançar uma série de aplicações empresariais de primeira para dispositivos móveis que atendem a organizações não tradicionais, e a colaboradores de grandes organizações que não usam o e-mail como o seu método de comunicação primário. Estas aplicações visam funcionários da linha da frente, trabalhadores sem secretária, agentes de campo ou empregados de retalho que podem não obter um endereço de e-mail do seu empregador, ter acesso a um computador ou a TI. Este projeto permitirá que estes colaboradores inscrevam-se em aplicações empresariais, introduzindo um número de telefone e arredondando um código. Para mais detalhes, consulte a nossa [documentação de administração](../authentication/howto-authentication-sms-signin.md) e [documentação do utilizador final.](../user-help/sms-sign-in-explainer.md)

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Convidar utilizadores internos a utilizarem a colaboração B2B

**Tipo:** Nova funcionalidade

**Categoria de serviço:** B2B

**Capacidade do produto:**

Estamos a expandir a capacidade de convite b2B para permitir que as contas internas existentes sejam convidadas a usar credenciais de colaboração B2B. Isto é feito passando o objeto do utilizador para a API convite, além de parâmetros típicos como o endereço de e-mail convidado. O ID do objeto do utilizador, UPN, membro do grupo, atribuição de aplicativos, etc. permanecem intactos, mas para a frente eles usarão B2B para autenticar com as credenciais do seu inquilino de casa em vez das credenciais internas que usaram antes do convite. Para mais detalhes, consulte a [documentação.](../external-identities/invite-internal-users.md)

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>O modo apenas de reporte para acesso condicional está geralmente disponível

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Acesso Condicional

**Capacidade do produto:** Proteção & de Segurança de Identidade

[O modo apenas de reporte para acesso condicionado Azure AD](../conditional-access/concept-conditional-access-report-only.md) permite-lhe avaliar o resultado de uma política sem impor controlos de acesso. Pode testar políticas apenas de relatórios em toda a sua organização e compreender o seu impacto antes de as permitir, tornando a implementação mais segura e fácil. Ao longo dos últimos meses, temos visto uma forte adoção do modo apenas de relatório, com mais de 26M utilizadores já no âmbito de uma política apenas de relatórios. Com este anúncio, novas políticas de acesso condicionado Azure AD serão criadas em modo apenas de relatório por padrão. Isto significa que pode monitorizar o impacto das suas políticas a partir do momento em que são criadas. E para aqueles que usam as APIs do MS Graph, também pode [gerir políticas apenas de relatórios programáticas.](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Os insights de acesso condicional e o livro de relatórios estão geralmente disponíveis

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Acesso Condicional

**Capacidade do produto:** Proteção & de Segurança de Identidade

Os insights de acesso condicional [e o livro de relatórios](../conditional-access/howto-conditional-access-insights-reporting.md) dão aos administradores uma visão sumária do Acesso Condicional AD Azure no seu inquilino. Com a capacidade de selecionar uma política individual, os administradores podem entender melhor o que cada política faz e monitorizar quaisquer mudanças em tempo real. O livro transmite dados armazenados no Azure Monitor, que pode configurar em poucos minutos [seguindo estas instruções](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). Para tornar o painel mais detetável, mudámo-lo para o novo separador de insights e relatórios dentro do menu Azure AD Conditional Access.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>A lâmina de detalhes de política para acesso condicional está em pré-visualização pública

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Acesso Condicional

**Capacidade do produto:** Proteção & de Segurança de Identidade

Os novos [detalhes da política](../conditional-access/troubleshoot-conditional-access.md) mostram quais as atribuições, condições e controlos que foram satisfeitos durante a avaliação da política de acesso condicional. Pode aceder à lâmina selecionando uma linha nos separadores **de Acesso Condicional** ou relatório **apenas** nos detalhes de Iniciar sing.in.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - abril de 2020

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Aplicativos da Empresa

**Capacidade do produto:** Integração de Terceiros

Em abril de 2020, adicionámos estas 31 novas aplicações com apoio da Federação à galeria de aplicações: 

[Aplicativos SincroPool](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [Float](../saas-apps/float-tutorial.md), [LMS365](https://lms.365.systems/), [IWT Procurement Suite](../saas-apps/iwt-procurement-suite-tutorial.md), [Lunni](https://lunni.fi/), [EasySSO para Jira](../saas-apps/easysso-for-jira-tutorial.md), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](../saas-apps/meraki-dashboard-tutorial.md), Microsoft [365 Mover,](https://app.mover.io/login) [Speaker Engage](https://speakerengage.com/login.php), [Honestamente,](../saas-apps/honestly-tutorial.md) [Ally,](../saas-apps/ally-tutorial.md) [DutyFlow](https://app.dutyflow.nl/), [AlertMedia,](../saas-apps/alertmedia-tutorial.md) [gr8 People](../saas-apps/gr8-people-tutorial.md), [Pendo](../saas-apps/pendo-tutorial.md), [HighGround](../saas-apps/highground-tutorial.md), [Harmony](../saas-apps/harmony-tutorial.md), [Timetabling Solutions](../saas-apps/timetabling-solutions-tutorial.md), [SynchroNet CLICK](../saas-apps/synchronet-click-tutorial.md), [empower,](https://www.made-in-office.com/en/) [Fortes Change Cloud](../saas-apps/fortes-change-cloud-tutorial.md), [Litmus](../saas-apps/litmus-tutorial.md), [GroupTalk,](https://recorder.grouptalk.com/) [Frontify,](../saas-apps/frontify-tutorial.md) [MongoDB Cloud,](../saas-apps/mongodb-cloud-tutorial.md) [TickitLMS Learn,](../saas-apps/tickitlms-learn-tutorial.md) [COCO,](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/) [Nitro Productivity Suite](../saas-apps/nitro-productivity-suite-tutorial.md), Trend Micro Web Security [(TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Suporte de consulta delta do Microsoft Graph para oAuth2PermissionGrant disponível para visualização pública

**Tipo:** Nova funcionalidade

**Categoria de serviço:** MS Graph

**Capacidade do produto:** Experiência de Desenvolvedor

A consulta delta para oAuth2PermissionGrant está disponível para pré-visualização pública! Agora pode rastrear alterações sem ter de pesquisar continuamente o Microsoft Graph. [Saiba mais.](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Suporte de consulta delta do Microsoft Graph para contacto organizacional geralmente disponível

**Tipo:** Nova funcionalidade

**Categoria de serviço:** MS Graph

**Capacidade do produto:** Experiência de Desenvolvedor

A consulta delta para contactos organizacionais está geralmente disponível! Agora é possível rastrear alterações nas aplicações de produção sem ter de fazer uma pesquisa contínua no Microsoft Graph. Substitua qualquer código existente que poll continuamente orgContact dados por consulta delta para melhorar significativamente o desempenho. [Saiba mais.](/graph/api/orgcontact-delta?tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Suporte de consulta delta do Microsoft Graph para aplicação geralmente disponível

**Tipo:** Nova funcionalidade

**Categoria de serviço:** MS Graph

**Capacidade do produto:** Experiência de Desenvolvedor

A consulta delta para aplicações está geralmente disponível! Agora é possível rastrear alterações nas aplicações de produção sem ter de fazer uma pesquisa contínua no Microsoft Graph. Substitua qualquer código existente que polle continuamente os dados da aplicação por consulta delta para melhorar significativamente o desempenho. [Saiba mais.](/graph/api/application-delta)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Suporte de consulta delta do Microsoft Graph para unidades administrativas disponíveis para visualização pública

**Tipo:** Nova funcionalidade

**Categoria de serviço:** MS Graph

**Capacidade do produto:** A consulta Delta da Developer Experience para unidades administrativas está disponível para pré-visualização pública! Agora pode rastrear alterações sem ter de pesquisar continuamente o Microsoft Graph. [Saiba mais.](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Gerir números de telefone de autenticação e muito mais em novas APIs beta do Microsoft Graph

**Tipo:** Nova funcionalidade

**Categoria de serviço:** MS Graph

**Capacidade do produto:** Experiência de Desenvolvedor

Estas APIs são uma ferramenta chave para gerir os métodos de autenticação dos seus utilizadores. Agora pode pré-registar-se programaticamente e gerir os autenticadores utilizados para mFA e redefinição de senha de autosserviço (SSPR). Esta tem sido uma das funcionalidades mais requisitadas nos espaços Azure AD MFA, SSPR e Microsoft Graph. As novas APIs que lançamos nesta onda dão-lhe a capacidade de:

- Leia, adicione, atualize e remova os telefones de autenticação de um utilizador
- Redefinir a senha de um utilizador
- Ligue e desligue o s-sinal de SMS

Para obter mais informações, consulte [os métodos de autenticação AZure AD API.](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)

---

### <a name="administrative-units-public-preview"></a>Pré-visualização pública das Unidades Administrativas

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Funções de AD AZure

**Capacidade do produto:** Controlo de Acessos

As unidades administrativas permitem-lhe conceder permissões administrativas restritas a um departamento, região ou outro segmento da sua organização que define. Pode utilizar unidades administrativas para delegar permissões a administradores regionais ou para definir a política a nível granular. Por exemplo, um administrador de conta de utilizador poderia atualizar informações de perfil, redefinir palavras-passe e atribuir licenças para utilizadores apenas na sua unidade administrativa.

Utilizando unidades administrativas, um administrador central poderia:

- Criar uma unidade administrativa para gestão descentralizada de recursos
- Atribuir uma função com permissões administrativas apenas sobre utilizadores Azure AD numa unidade administrativa
- Povoar as unidades administrativas com utilizadores e grupos conforme necessário

Para mais informações, consulte [a gestão de unidades administrativas no Diretório Ativo Azure (pré-visualização)](../roles/administrative-units.md).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Funções incorporadas do administrador de impressora e técnico de impressora

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Funções de AD AZure

**Capacidade do produto:** Controlo de Acessos

**Administrador de impressora**: Os utilizadores com esta função podem registar impressoras e gerir todos os aspetos de todas as configurações da impressora na solução De Impressão Universal da Microsoft, incluindo as definições do Conector de Impressão Universal. Podem consentir com todos os pedidos de autorização de impressão delegados. Os administradores da impressora também têm acesso a relatórios de impressão. 

**Técnico de impressora**: Os utilizadores com esta função podem registar impressoras e gerir o estado da impressora na solução de Impressão Universal da Microsoft. Também podem ler todas as informações do conector. As tarefas-chave que um Técnico de Impressora não pode fazer são definir permissões do utilizador em impressoras e partilhar impressoras. [Saiba mais.](../roles/permissions-reference.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Papel integrado no administrador de identidade híbrida

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Funções de AD AZure

**Capacidade do produto:** Controlo de Acessos

Os utilizadores desta função podem permitir, configurar e gerir serviços e configurações relacionadas com a ativação da identidade híbrida em AZure AD. Esta função confere a possibilidade de configurar a Azure AD a um dos três métodos de autenticação suportados&#8212;sincronização de hash password (PHS), autenticação pass-through (PTA) ou Federação (AD FS ou provedor de federação de terceiros)&#8212;e implantar infraestruturas relacionadas nas instalações para os permitir. As infraestruturas no local incluem agentes de provisionamento e de PTA. Esta função garante a capacidade de permitir que o Seamless Single Sign-On (S-SSO) permita a autenticação sem emenda em dispositivos não Windows 10 ou computadores não-Windows Server 2016. Além disso, esta função confere a possibilidade de ver registos de inscrição e de aceder à saúde e análise para fins de monitorização e resolução de problemas. [Saiba mais.](../roles/permissions-reference.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Função de administrador de rede incorporado

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Funções de AD AZure

**Capacidade do produto:** Controlo de Acessos

Os utilizadores com esta função podem rever recomendações de arquitetura de perímetro de rede da Microsoft que são baseadas em telemetria de rede a partir das suas localizações de utilizadores. O desempenho da rede para o Microsoft 365 baseia-se na arquitetura cuidadosa do perímetro da rede de clientes da empresa, que é geralmente específica da localização do utilizador. Esta função permite a edição das localizações do utilizador descobertas e a configuração de parâmetros de rede para esses locais para facilitar a melhoria das medições de telemetria e recomendações de design. [Saiba mais.](../roles/permissions-reference.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Atividade a granel e downloads na experiência do portal de administração Admin Azure

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Gestão de Utilizadores

**Capacidade do produto:** Diretório

Agora pode realizar atividades a granel em utilizadores e grupos em Azure AD, enviando um ficheiro CSV na experiência do portal de administração Azure AD. Pode criar utilizadores, eliminar utilizadores e convidar utilizadores convidados. E pode adicionar e remover membros de um grupo.

Também pode baixar listas de recursos Ad AD Azure a partir da experiência do portal de administração Azure. Você pode baixar a lista de utilizadores no diretório, a lista de grupos no diretório, e os membros de um determinado grupo.

Para mais informações, confira o seguinte:

- [Criar utilizadores](../enterprise-users/users-bulk-add.md) ou [convidar utilizadores convidados](../external-identities/tutorial-bulk-invite.md)
- [Eliminar utilizadores](../enterprise-users/users-bulk-delete.md) ou [restaurar utilizadores eliminados](../enterprise-users/users-bulk-restore.md)
- [Baixar lista de utilizadores](../enterprise-users/users-bulk-download.md) ou [baixar lista de grupos](../enterprise-users/groups-bulk-download.md)
- [Adicione (importar) membros](../enterprise-users/groups-bulk-import-members.md) ou [remova membros](../enterprise-users/groups-bulk-remove-members.md) ou [Descarregue a lista de membros](../enterprise-users/groups-bulk-download-members.md) para um grupo

---

### <a name="my-staff-delegated-user-management"></a>O meu Pessoal delegou a gestão dos utilizadores

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Gestão de Utilizadores

**Capacidade do produto:**

A My Staff permite que os Gestores da Firstline, como um gerente de loja, garantam que os seus colaboradores possam aceder às suas contas AD Azure. Em vez de confiarem num helpdesk central, as organizações podem delegar tarefas comuns, tais como repor palavras-passe ou alterar números de telefone, para um Gestor da Linha Inicial. Com o My Staff, um utilizador que não consiga aceder à sua conta pode voltar a ter acesso em apenas alguns cliques, sem necessidade de ajuda ou pessoal de TI. Para obter mais informações, consulte a Gestão dos [seus utilizadores com o Meu Pessoal (pré-visualização)](../roles/my-staff-configure.md) e [delegar a gestão do utilizador com o Meu Pessoal (pré-visualização)](../user-help/my-staff-team-manager.md).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Uma experiência de utilizador final atualizada em comentários de acesso

**Tipo:** Alteração de recurso

**Categoria de serviço:** Comentários de Acesso

**Capacidade do produto:** Governação da Identidade

Atualizámos a experiência do revisor para avaliações de acesso a Azure no portal My Apps. No final de abril, os seus revisores que estão ligados à experiência do revisor de comentários de acesso AZure AD verão um banner que lhes permitirá experimentar a experiência atualizada no My Access. Por favor, note que a experiência atualizada de avaliações do Access oferece a mesma funcionalidade que a experiência atual, mas com uma interface de utilizador melhorada em cima de novas capacidades para permitir que os seus utilizadores sejam produtivos. [Pode saber mais sobre a experiência atualizada aqui.](../governance/perform-access-review.md) Esta pré-estreia pública durará até ao final de julho de 2020. No final de julho, os revisores que não optarem pela experiência de pré-visualização serão automaticamente direcionados para o My Access para realizar avaliações de acesso. Se desejar que os seus revisores tenham mudado permanentemente para a experiência de pré-visualização no My Access agora, [por favor, faça um pedido aqui](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>As aplicações de fornecimento e writeback dos utilizadores de trabalho suportam agora as versões mais recentes da Workday Web Services API

**Tipo:** Alteração de recurso

**Categoria de serviço:** Provisionamento de Aplicativos

**Capacidade do produto:** 

Com base no feedback do cliente, atualizámos as aplicações de provisionamento e de writeback dos utilizadores do Workday na galeria de aplicações da empresa para suportar as versões mais recentes da API dos Workday Web Services (WWS). Com esta alteração, os clientes podem especificar a versão API WWS que gostariam de usar na cadeia de ligação. Isto dá aos clientes a capacidade de recuperar mais atributos DE RH disponíveis nos lançamentos do Workday. A aplicação Workday Writeback utiliza agora o serviço web Change_Work_Contact_Info Workday recomendado para ultrapassar as limitações de Maintain_Contact_Info.

Se nenhuma versão for especificada na cadeia de ligação, por padrão, as aplicações de provisionamento de entrada workday continuarão a utilizar WWS v21.1 Para mudar para as mais recentes APIs do Workday para o provisionamento do utilizador de entrada, os clientes precisam de atualizar a cadeia de ligação como documentado [no tutorial](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) e também atualizar os XPATHs utilizados para atributos Workday como documentado no guia de referência do dia [de trabalho](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30). 

Para utilizar a nova API para reversão, não são necessárias alterações na aplicação de provisionamento de writeback workday. Do lado do Workday, certifique-se de que a conta do Utilizador do Sistema de Integração do Workday (ISU) tem permissões para invocar o processo de negócio Change_Work_Contact, conforme documentado na secção tutorial, [configurar permissões](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions)de política de segurança do processo de negócio. 

Atualizámos o nosso [guia tutorial](../saas-apps/workday-inbound-tutorial.md) para refletir o novo suporte à versão API.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Os utilizadores com função de acesso predefinido estão agora no âmbito do provisionamento

**Tipo:** Alteração de recurso

**Categoria de serviço:** Provisionamento de Aplicativos

**Capacidade do produto:** Gestão do ciclo de vida da identidade

Historicamente, os utilizadores com a função de acesso predefinido têm estado fora de alcance para o provisionamento. Ouvimos feedback de que os clientes querem que os utilizadores com esta função estejam em campo para provisões. A partir de 16 de abril de 2020, todas as novas configurações de provisionamento permitem que os utilizadores com a função de acesso predefinido sejam a provisionados. Gradualmente, vamos alterar o comportamento das configurações de provisionamento existentes para apoiar o a provisionamento dos utilizadores com esta função. [Saiba mais.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>UI de provisionamento atualizado

**Tipo:** Alteração de recurso

**Categoria de serviço:** Provisionamento de Aplicativos

**Capacidade do produto:** Gestão do ciclo de vida da identidade

Refrescamos a nossa experiência de provisão para criar uma visão de gestão mais focada. Quando navegar para a lâmina de provisionamento para uma aplicação empresarial que já foi configurada, poderá monitorizar facilmente o progresso do provisionamento e gestão de ações como iniciar, parar e reiniciar o provisionamento. [Saiba mais.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>A validação da regra do Grupo Dinâmico já está disponível para visualização pública

**Tipo:** Alteração de recurso

**Categoria de serviço:** Gestão de Grupos

**Capacidade do produto:** Colaboração

O Azure Ative Directory (Azure AD) fornece agora os meios para validar regras dinâmicas de grupo. No separador **Regras Validadas,** pode validar a sua regra dinâmica contra membros do grupo de amostras para confirmar que a regra está a funcionar como esperado. Ao criar ou atualizar regras dinâmicas do grupo, os administradores querem saber se um utilizador ou um dispositivo será membro do grupo. Isto ajuda a avaliar se um utilizador ou dispositivo cumpre os critérios de regra e ajuda na resolução de problemas quando a adesão não é esperada.

Para obter mais informações, consulte [validar uma regra de adesão dinâmica do grupo (pré-visualização)](../enterprise-users/groups-dynamic-rule-validation.md).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Pontuação Segura de Identidade - Incumprimentos de segurança e atualizações de ação de melhoria de MFA

**Tipo:** Alteração de recurso

**Categoria de serviço:** N/A

**Capacidade do produto:** Proteção & de Segurança de Identidade

**Incumprimentos de segurança para ações de melhoria da AD Azure:** O Microsoft Secure Score irá atualizar as ações de melhoria para suportar [falhas de segurança no AZure AD](./concept-fundamentals-security-defaults.md), que facilitam a proteção da sua organização com definições de segurança pré-configuradas para ataques comuns. Isto afetará as seguintes ações de melhoria:

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

Estas novas ações de melhoria requerem o registo dos seus utilizadores ou administradores para a autenticação de vários fatores (MFA) em todo o seu diretório e estabelecer o conjunto certo de políticas que se adequam às suas necessidades organizacionais. O objetivo principal é ter flexibilidade, garantindo ao mesmo tempo que todos os seus utilizadores e administradores podem autenticar com múltiplos fatores ou pedidos de verificação de identidade baseados no risco. Isto pode assumir a forma de ter múltiplas políticas que aplicam decisões âmbito, ou definir padrão de segurança (a partir de 16 de março) que permitem à Microsoft decidir quando desafiar os utilizadores para mFA. [Leia mais sobre as novidades no Microsoft Secure Score](/microsoft-365/security/mtp/microsoft-secure-score#whats-new).

---

## <a name="march-2020"></a>Março de 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march--2021"></a>Contas de Diretório Ativo Azure não geridos em atualização B2B para março de 2021

**Tipo:** Plano de mudança  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
**A partir de 31 de março de 2021,** a Microsoft deixará de apoiar o resgate de convites através da criação de contas e inquilinos não geridos do Azure Ative Directory (Azure AD) para cenários de colaboração B2B. Na preparação para isso, encorajamo-lo a optar por [um e-mail](../external-identities/one-time-passcode.md)com a autenticação de código de acesso único.

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Os utilizadores com a função de acesso predefinido estarão em campo para o provisionamento

**Tipo:** Plano de mudança  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
Historicamente, os utilizadores com a função de acesso predefinido têm estado fora de alcance para o provisionamento. Ouvimos feedback de que os clientes querem que os utilizadores com esta função estejam em campo para provisões. Estamos a trabalhar na implementação de uma alteração para que todas as novas configurações de provisionamento permitam aos utilizadores com a função de acesso predefinido ser alocada. Gradualmente, vamos alterar o comportamento das configurações existentes para apoiar o a provisionamento dos utilizadores com esta função. Não é necessária qualquer ação do cliente. Publicaremos uma atualização da nossa [documentação](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md) assim que esta alteração estiver em vigor.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>A colaboração Azure AD B2B estará disponível no Microsoft Azure operado por inquilinos 21Vianet (Azure China 21Vianet)

**Tipo:** Plano de mudança  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
As capacidades de colaboração Azure AD B2B serão disponibilizadas no Microsoft Azure operado por inquilinos 21Vianet (Azure China 21Vianet), permitindo aos utilizadores de um inquilino Azure China 21Vianet colaborarem perfeitamente com os utilizadores de outros inquilinos da Azure China 21Vianet. [Saiba mais sobre a colaboração Azure AD B2B](/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B Colaboração email redesign

**Tipo:** Plano de mudança  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
Os [e-mails enviados](../external-identities/invitation-email-elements.md) pelo serviço de convite de colaboração Azure AD B2B para convidar os utilizadores para o diretório serão redesenhados para tornar mais clara a informação do convite e os próximos passos do utilizador.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>As alterações de política do HomeRealmDiscovery aparecerão nos registos de auditoria

**Tipo:** Fixo  
**Categoria de serviço:** Auditoria  
**Capacidade do produto:** Relatório de & de Monitorização
 
Corrigimos um bug onde as alterações à [política homeRealmDiscovery](../manage-apps/configure-authentication-for-federated-users-portal.md) não foram incluídas nos registos de auditoria. Agora poderão ver quando e como a política foi alterada, e por quem. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - março de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Em março de 2020, adicionámos estas 51 novas aplicações com apoio da Federação à galeria de aplicações: 

[Cisco AnyConnect](../saas-apps/cisco-anyconnect.md), [Zoho One China](../saas-apps/zoho-one-china-tutorial.md), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co APP SAML](../saas-apps/profitco-saml-app-tutorial.md), [iPoint Service Provider](../saas-apps/ipoint-service-provider-tutorial.md), contexxt.ai [SPHERE](https://contexxt-sphere.com/login), Wisdom [By Invictus](../saas-apps/wisdom-by-invictus-tutorial.md), Flare [Digital Signage](https://spark-dev.pixelnebula.com/login), [Logz.io - Cloud Observability for Engineers](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md), [SpectrumU,](../saas-apps/spectrumu-tutorial.md) [BizzContact,](https://bizzcontact.app/) [Elqano SSO](../saas-apps/elqano-sso-tutorial.md), [MarketSignShare](http://www.signshare.com/), [CrossKnowledge Learning Suite](../saas-apps/crossknowledge-learning-suite-tutorial.md), [Netvision Compas](../saas-apps/netvision-compas-tutorial.md), [FCM HUB](../saas-apps/fcm-hub-tutorial.md), RIB [A/S Byggeweb Mobile,](https://apps.apple.com/us/app/docia/id529058757) [GoLinks,](../saas-apps/golinks-tutorial.md) [Datadog](../saas-apps/datadog-tutorial.md), [Zscaler B2B User Portal,](../saas-apps/zscaler-b2b-user-portal-tutorial.md) [LIFT,](../saas-apps/lift-tutorial.md) [Planview Enterprise One](../saas-apps/planview-enterprise-one-tutorial.md), [WatchTeams](https://www.devfinition.com/), [Aster](https://demo.asterapp.io/login), [Skills Workflow](../saas-apps/skills-workflow-tutorial.md), [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx), Plataforma [IP](../saas-apps/ip-platform-tutorial.md), [InVision](../saas-apps/invision-tutorial.md), [Pipedrive](../saas-apps/pipedrive-tutorial.md), [Showcase Workshop](https://app.showcaseworkshop.com/), [Greenlight Integration Platform](../saas-apps/greenlight-integration-platform-tutorial.md), [Greenlight Compliant Access Management](../saas-apps/greenlight-compliant-access-management-tutorial.md), [Grok Learning](../saas-apps/grok-learning-tutorial.md), [Miradore Online,](https://login.online.miradore.com/) [Khoros Care,](../saas-apps/khoros-care-tutorial.md) [AskYourTeam,](../saas-apps/askyourteam-tutorial.md) [TruNarrative,](../saas-apps/trunarrative-tutorial.md) [Smartwaiver,](https://www.smartwaiver.com/m/user/sw_login.php?wms_login) [Bizagi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md), [insuiteX](https://www.insuite.jp/), [sybo](https://www.systexsoftware.com.tw/), [Britive](../saas-apps/britive-tutorial.md), [WhosOffice](../saas-apps/whosoffice-tutorial.md), [E-days](../saas-apps/e-days-tutorial.md), [Kollective SDN](https://portal.kollective.app/login), [Witivio,](https://app.witivio.com/) [Playvox,](https://my.playvox.com/login) [Korn Ferry 360,](../saas-apps/korn-ferry-360-tutorial.md) [Campus Café,](../saas-apps/campus-cafe-tutorial.md) [Catchpoint,](../saas-apps/catchpoint-tutorial.md) [Code42](../saas-apps/code42-tutorial.md)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Colaboração Azure AD B2B disponível em inquilinos do Governo de Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
As funcionalidades de colaboração Azure AD B2B estão agora disponíveis entre alguns inquilinos do Governo de Azure.  Para saber se o seu inquilino é capaz de usar estas capacidades, siga as instruções em [Como posso dizer se a colaboração B2B está disponível no meu inquilino do Governo Azure US?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>A integração do Azure Monitor para logs Azure já está disponível no Governo de Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Relatório de & de Monitorização
 
A integração do Azure Monitor com os registos AZure AD está agora disponível no Governo de Azure. Pode encaminhar os Registos AD do Azure (Registos de Auditoria e Registos) para uma conta de armazenamento, Centro de Eventos e Analítico de Log. Por favor, verifique a [documentação detalhada,](../reports-monitoring/concept-activity-logs-azure-monitor.md) bem como [planos de implementação para relatórios e monitorização](../reports-monitoring/plan-monitoring-and-reporting.md) para cenários Azure AD.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Atualização de Proteção de Identidade no Governo de Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Proteção & de Segurança de Identidade

Estamos entusiasmados por partilhar que lançámos agora a renovada experiência [de Proteção de Identidade Azure AD](../identity-protection/overview-identity-protection.md)   no portal do Governo microsoft [Azure](https://portal.azure.us/). Para mais informações, consulte o nosso [post de blog de anúncio.](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Recuperação de desastres: Descarregue e guarde a sua configuração de provisionamento

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
O serviço de fornecimento AD AZure fornece um rico conjunto de capacidades de configuração. Os clientes precisam de ser capazes de guardar a sua configuração para que possam referir-se a ela mais tarde ou reverter para uma versão conhecida. Adicionámos a capacidade de descarregar a sua configuração de provisionamento como um ficheiro JSON e carregá-la quando precisar. [Saiba mais](../app-provisioning/export-import-provisioning-configuration.md).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>A SSPR (redefinição da palavra-passe de autosserviço) requer agora dois portões para administradores no Microsoft Azure operados pela 21Vianet (Azure China 21Vianet) 

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Self-Service Redefinição de password  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
Anteriormente no Microsoft Azure operado pela 21Vianet (Azure China 21Vianet), os administradores que usam a redefinição da palavra-passe de autosserviço (SSPR) para redefinir as suas próprias palavras-passe precisavam apenas de um "portal" (desafio) para provar a sua identidade. Em nuvens públicas e outras, os administradores geralmente devem usar dois portões para provar a sua identidade quando utilizam sSPR. Mas como não apoiámos SMS ou chamadas telefónicas no Azure China 21Vianet, permitimos que a palavra-passe de um portão seja reposta por administradores.

Estamos a criar paridade de recursos SSPR entre a Azure China 21Vianet e a nuvem pública. Para a frente, os administradores devem utilizar dois portões quando utilizarem SSPR. As notificações e códigos de aplicações por SMS, chamadas telefónicas e autenticadores serão suportados. [Saiba mais](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>O comprimento da palavra-passe é limitado a 256 caracteres

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
Para garantir a fiabilidade do serviço Azure AD, as palavras-passe do utilizador estão agora limitadas em comprimento a 256 caracteres. Os utilizadores com palavras-passe mais longas do que esta serão solicitados a alterar a sua palavra-passe no início de sessão subsequente, quer contactando a sua administração, quer utilizando a funcionalidade de reset da palavra-passe de autosserviço.

Esta alteração foi ativada no dia 13 de março de 2020, às 10:00 PST (18:00 UTC), sendo o erro AADSTS 50052, InvalidPasswordExceedsMaxLength. Consulte o [aviso de mudança de rutura](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters) para mais detalhes.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Os registos de inscrição da AD AZure já estão disponíveis para todos os inquilinos gratuitos através do portal Azure

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Relatório de & de Monitorização
 
A partir de agora, os clientes que tenham inquilinos gratuitos podem aceder aos [registos de inscrição Azure AD do portal Azure](../reports-monitoring/concept-sign-ins.md) por um máximo de 7 dias. Anteriormente, os registos de login só estavam disponíveis para clientes com licenças Azure Ative Directory Premium. Com esta mudança, todos os inquilinos podem aceder a estes registos através do portal.

> [!NOTE]
> Os clientes ainda precisam de uma licença premium (Azure Ative Directory Premium P1 ou P2) para aceder aos registos de login através da Microsoft Graph API e do Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Depreciação da opção de grupos em todo o diretório a partir de Configurações Gerais de Grupos no portal Azure

**Tipo:** Precatado  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração

Para proporcionar uma forma mais flexível de os clientes criarem grupos de diretório que melhor satisfaçam as suas necessidades, substituímos a opção grupos de **diretório** das configurações gerais dos **grupos**  >   no portal Azure por um link para [a documentação dinâmica do grupo.](../enterprise-users/groups-dynamic-membership.md) Melhorámos a nossa documentação para incluir mais instruções para que os administradores possam criar grupos de todos os utilizadores que incluam ou excluam os utilizadores convidados.

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

Estas novas ações de melhoria exigirão registar os seus utilizadores ou administradores para MFA em todo o seu diretório e estabelecer o conjunto certo de políticas que se adequam às suas necessidades organizacionais. O objetivo principal é ter flexibilidade, garantindo ao mesmo tempo que todos os seus utilizadores e administradores podem autenticar com múltiplos fatores ou pedidos de verificação de identidade baseados no risco. Isto pode assumir a forma de definir padrão de segurança que permite à Microsoft decidir quando desafiar os utilizadores para MFA, ou ter várias políticas que aplicam decisões de âmbito. Como parte destas atualizações de ação de melhoria, as políticas de proteção de base deixarão de ser incluídas nos cálculos de pontuação. [Leia mais sobre o que está a chegar no Microsoft Secure Score](/microsoft-365/security/mtp/microsoft-secure-score-whats-coming).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Seleção SKU de Serviços de Domínio Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Serviços de domínio Azure Ad  
**Capacidade do produto:** Serviços de domínio Azure Ad
 
Ouvimos feedback de que os clientes da Azure AD Domain Services querem mais flexibilidade na seleção dos níveis de desempenho para as suas instâncias. A partir de 1 de fevereiro de 2020, mudamos de um modelo dinâmico (onde a Azure AD determina o nível de desempenho e preços baseado na contagem de objetos) para um modelo de autosseleção. Agora os clientes podem escolher um nível de desempenho que corresponda ao seu ambiente. Esta alteração permite-nos também ativar novos cenários como As Florestas de Recursos e funcionalidades Premium como backups diários. A contagem de objetos é agora ilimitada para todos os SKUs, mas continuaremos a oferecer sugestões de contagem de objetos para cada nível.

**Não é necessária qualquer ação imediata do cliente.** Para os clientes existentes, o nível dinâmico que estava em uso a 1 de fevereiro de 2020, determina o novo nível de incumprimento. Não existe um impacto de preços ou desempenho como resultado desta alteração. Para a frente, os clientes da Azure AD DS terão de avaliar os requisitos de desempenho à medida que o seu tamanho de diretório e as suas características de carga de trabalho mudarem. A troca entre os níveis de serviço continuará a ser uma operação sem tempo de inatividade, e deixaremos de transferir automaticamente os clientes para novos níveis com base no crescimento do seu diretório. Além disso, não haverá aumentos de preços, e novos preços irão alinhar-se com o nosso modelo de faturação atual. Para obter mais informações, consulte a [documentação Azure AD DS SKUs](../../active-directory-domain-services/administration-concepts.md#azure-ad-ds-skus) e a [página de preços dos Serviços de Domínio AZure AD](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - fevereiro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Em fevereiro de 2020, adicionámos estas 31 novas aplicações com apoio da Federação à galeria de aplicações: 

[Plataforma de Patentes IamIP](../saas-apps/iamip-patent-platform-tutorial.md), [Experience Cloud](../saas-apps/experience-cloud-tutorial.md), [NS1 SSO For Azure](../saas-apps/ns1-sso-azure-tutorial.md), [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), Em caso de crise - Portal [On-line,](../saas-apps/in-case-of-crisis-online-portal-tutorial.md) [BIC Cloud Design,](../saas-apps/bic-cloud-design-tutorial.md) [Apicultor Azure AD Data Connector,](../saas-apps/beekeeper-azure-ad-data-connector-tutorial.md) [Korn Ferry Assessments,](https://www.kornferry.com/solutions/kf-digital/kf-assess) [Verkada Command,](../saas-apps/verkada-command-tutorial.md) [Splashtop,](../saas-apps/splashtop-tutorial.md) [Syxsense,](../saas-apps/syxsense-tutorial.md) [EAB Navigate,](../saas-apps/eab-navigate-tutorial.md) [Nova Relíquia (Lançamento Limitado)](../saas-apps/new-relic-limited-release-tutorial.md), [Thulium](https://admin.thulium.com/login/instance), [Gestor de Bilhetes,](../saas-apps/ticketmanager-tutorial.md)Escolha [de Modelos para Equipas,](https://links.officeatwork.com/templatechooser-download-teams) [Beesy,](https://www.beesy.me/index.php/site/login) [Sistema de Suporte de Saúde,](../saas-apps/health-support-system-tutorial.md) [MURAL,](https://app.mural.co/signup) [Colmeia,](../saas-apps/hive-tutorial.md) [LavaDo,](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview) [Wakelet,](https://wakelet.com/login) [Firmex VDR,](../saas-apps/firmex-vdr-tutorial.md) [ThingLink para Professores e Escolas,](https://www.thinglink.com/) [Coda,](../saas-apps/coda-tutorial.md) [NearpodApp,](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product) [WEDO,](../saas-apps/wedo-tutorial.md) [InvitePeople,](https://invitepeople.com/login) [Reprints Desk - Artigo Galaxy,](../saas-apps/reprints-desk-article-galaxy-tutorial.md) [TeamViewer](../saas-apps/teamviewer-tutorial.md)

 
Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Novos conectores de provisionamento na Galeria de Aplicações AD Azure - fevereiro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:

- [Mixpanel](../saas-apps/mixpanel-provisioning-tutorial.md)
- [TeamViewer](../saas-apps/teamviewer-provisioning-tutorial.md)
- [Azure Databricks](/azure/databricks/administration-guide/users-groups/scim/aad)
- [PureCloud da Genesys](../saas-apps/purecloud-by-genesys-provisioning-tutorial.md)
- [Zapier](../saas-apps/zapier-provisioning-tutorial.md)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte [automatizar o fornecimento de utilizadores para aplicações SaaS com Azure AD](../app-provisioning/user-provisioning.md).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Suporte AZURE AD para chaves de segurança FIDO2 em ambientes híbridos

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
Estamos a anunciar a pré-visualização pública do suporte da AD Azure para chaves de segurança FIDO2 em ambientes híbridos. Os utilizadores podem agora utilizar as chaves de segurança FIDO2 para iniciar súbs nos seus dispositivos Híbrido Azure AD e obter um sinal de saúde sem emenda nos seus locais e recursos na nuvem. O suporte aos ambientes Híbridos tem sido a característica mais requisitada dos nossos clientes sem palavras-passe desde que inicialmente lançámos a pré-visualização pública para suporte FIDO2 em dispositivos aderidos a Azure AD. A autenticação sem palavras-passe utilizando tecnologias avançadas como a biometria e a criptografia de chaves públicas/privadas proporcionam comodidade e facilidade de utilização enquanto estão seguros. Com esta pré-visualização pública, você pode agora usar a autenticação moderna como chaves de segurança FIDO2 para aceder aos recursos tradicionais do Ative Directory. Para mais informações, aceda [ao SSO para obter recursos no local.](../authentication/howto-authentication-passwordless-security-key-on-premises.md) 

Para começar, visite [as chaves de segurança FIDO2 para o seu inquilino](../authentication/howto-authentication-passwordless-security-key.md) para obter instruções passo a passo. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>A nova experiência My Account está agora geralmente disponível

**Tipo:** Alteração de recurso  
**Categoria de serviço:** O meu perfil/conta  
**Capacidade do produto:** Experiências de utilizador final
 
A Minha Conta, o balcão único para todas as necessidades de gestão de conta de utilizador final, está agora geralmente disponível! Os utilizadores finais podem aceder a este novo site via URL, ou no cabeçalho da nova experiência My Apps. Saiba mais sobre todas as capacidades de self-service que a nova experiência oferece no [My Account Portal Overview](../user-help/my-account-portal-overview.md).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>URL do site da minha conta atualizando para myaccount.microsoft.com

**Tipo:** Alteração de recurso  
**Categoria de serviço:** O meu perfil/conta  
**Capacidade do produto:** Experiências de utilizador final
 
A nova experiência do utilizador final my Account irá atualizar o seu URL para `https://myaccount.microsoft.com` o próximo mês. Encontre mais informações sobre a experiência e todas as capacidades de self-service da conta que oferece aos utilizadores finais no [portal My Account.](../user-help/my-account-portal-overview.md)

---

## <a name="january-2020"></a>Janeiro de 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>O novo portal My Apps está disponível em geral

**Tipo:** Plano de mudança  
**Categoria de serviço:** As minhas apps  
**Capacidade do produto:** Experiências de utilizador final
 
Atualize a sua organização para o novo portal My Apps que está disponível em geral! Encontre mais informações sobre o novo portal e coleções na [Create collections no portal My Apps.](../manage-apps/access-panel-collections.md)

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Espaços de trabalho em Azure AD foram renomeados para coleções

**Tipo:** Alteração de recurso  
**Categoria de serviço:** As minhas apps   
**Capacidade do produto:** Experiências de utilizador final
 
Os espaços de trabalho, os administradores de filtros podem configurar para organizar as aplicações dos seus utilizadores, passarão a ser referidos como coleções. Encontre mais informações sobre como configurá-las nas [coleções Create no portal My Apps](../manage-apps/access-panel-collections.md).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C Inscrição e inscrição no telefone usando a política personalizada (Visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão da Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C
 
Com a inscrição e a inscrição no número de telefone, os desenvolvedores e empresas podem permitir que os seus clientes se inscrevam e se inscrevam através de uma senha única enviada para o número de telefone do utilizador através de SMS. Esta funcionalidade também permite que o cliente altere o seu número de telefone se perder o acesso ao seu telemóvel. Com o poder de políticas personalizadas e inscrição de telefone e inscrição, permite que os desenvolvedores e empresas comuniquem a sua marca através da personalização da página. Descubra como [configurar a inscrição no telefone e iniciar sôm-se com as políticas personalizadas em Azure AD B2C](../../active-directory-b2c/phone-authentication.md).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Novos conectores de provisionamento na Galeria de Aplicações AD Azure - janeiro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:

- [Promapp]( ../saas-apps/promapp-provisioning-tutorial.md)
- [Zscaler Private Access](../saas-apps/zscaler-private-access-provisioning-tutorial.md)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte [automatizar o fornecimento de utilizadores para aplicações SaaS com Azure AD](../app-provisioning/user-provisioning.md).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - janeiro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Em janeiro de 2020, adicionámos estas 33 novas aplicações com apoio da Federação à galeria de aplicações: 

[JOSA](../saas-apps/josa-tutorial.md), [FastLy Edge Cloud](../saas-apps/fastly-edge-cloud-tutorial.md), [Terraform Enterprise](../saas-apps/terraform-enterprise-tutorial.md), [Spintr SSO](../saas-apps/spintr-sso-tutorial.md), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly,](../saas-apps/upshotly-tutorial.md) [LeaveBot,](https://appsource.microsoft.com/en-us/product/office/WA200001175) [DataCamp,](../saas-apps/datacamp-tutorial.md) [TripActions,](../saas-apps/tripactions-tutorial.md) [SmartWork,](https://www.intumit.com/english/SmartWork.html) [Dotcom-Monitor](../saas-apps/dotcom-monitor-tutorial.md), [SSOGEN - Azure AD SSO Gateway Oracle for E-Business Suite - EBS, PeopleSoft, e JDE](../saas-apps/ssogen-tutorial.md), [Hospedado MyCirqa SSO](../saas-apps/hosted-mycirqa-sso-tutorial.md), [Yuhu Property Management Platform,](../saas-apps/yuhu-property-management-platform-tutorial.md) [LumApps](https://sites.lumapps.com/login), [Upwork Enterprise,](../saas-apps/upwork-enterprise-tutorial.md) [Talentsoft,](../saas-apps/talentsoft-tutorial.md) [SmartDB for Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](../saas-apps/presspage-tutorial.md), [ContractSafe Saml2 SSO](../saas-apps/contractsafe-saml2-sso-tutorial.md), [Maxient Conduct Manager Software](../saas-apps/maxient-conduct-manager-software-tutorial.md), [Helpshift](../saas-apps/helpshift-tutorial.md), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), [Squelch Cloud Office365 Connector](https://laxmi.squelch.io/login), [PingFlow Authentication](https://app-staging.pingview.io/), [ PrinterLogic SaaS](../saas-apps/printerlogic-saas-tutorial.md), [Taskize Connect](../saas-apps/taskize-connect-tutorial.md), [Sandwai](https://app.sandwai.com/), [EZRentOut,](../saas-apps/ezrentout-tutorial.md) [AssetSonar](../saas-apps/assetsonar-tutorial.md), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="two-new-identity-protection-detections"></a>Duas novas deteções de Proteção de Identidade

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
Adicionámos dois novos tipos de deteção ligados à Proteção de Identidade: regras suspeitas de manipulação de caixas de entrada e viagens impossíveis. Estas deteções offline são descobertas pela Microsoft Cloud App Security (MCAS) e influenciam o utilizador e o risco de acesso à proteção de identidade. Para obter mais informações sobre estas deteções, consulte os nossos [tipos de risco de inscrição.](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Breaking Change: Os fragmentos URI não serão transportados através do redirecionamento de login

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
A partir de 8 de fevereiro de 2020, quando um pedido é enviado para login.microsoftonline.com para assinar um utilizador, o serviço anexará um fragmento vazio ao pedido.  Isto impede uma classe de ataques de redirecionamento, garantindo que o navegador elimina qualquer fragmento existente no pedido. Nenhuma aplicação deve ter uma dependência deste comportamento. Para obter mais informações, consulte [Breaking changes](../develop/reference-breaking-changes.md#february-2020) na documentação da plataforma de identidade da Microsoft.

---

## <a name="december-2019"></a>Dezembro de 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrar os SAP SuccessFactors que se aprovisionam em Azure AD e no local AD (Visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida da identidade

Pode agora integrar os SAP SuccessFactors como fonte de identidade autoritária em Azure AD. Esta integração ajuda-o a automatizar o ciclo de vida de identidade de ponta a ponta, incluindo a utilização de eventos baseados em RH, como novas contratações ou rescisões, para controlar o fornecimento de contas AD Azure.

Para obter mais informações sobre como configurar o SAP SuccessFactors que está a chegar à Azure AD, consulte o tutorial [de provisionamento automático Configure SAP SuccessFactors.](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Suporte para e-mails personalizados em Azure AD B2C (Visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão da Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C

Agora pode utilizar o Azure AD B2C para criar e-mails personalizados quando os seus utilizadores se inscrevem para usar as suas apps. Ao utilizar o DisplayControls (atualmente em pré-visualização) e um fornecedor de e-mail de terceiros (como, [SendGrid,](https://sendgrid.com/) [SparkPost,](https://sparkpost.com/)ou uma API rest personalizada), pode utilizar o seu próprio modelo de e-mail, **do** endereço e do texto sujeito, bem como apoiar a localização e as definições de senha de uma única vez (OTP).

Para mais informações, consulte [a verificação de email personalizada no Azure Ative Directory B2C](../../active-directory-b2c/custom-email-sendgrid.md).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Substituição das políticas de base por incumprimentos de segurança

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Segurança e Proteção de Identidade

Como parte de um modelo seguro por defeito para a autenticação, estamos removendo as políticas de proteção de base existentes de todos os inquilinos. Esta remoção tem como objetivo a sua conclusão no final de fevereiro. A substituição destas políticas de proteção de base são falhas de segurança. Se tem usado políticas de proteção de base, tem de planear passar para a nova política de incumprimentos de segurança ou para o Acesso Condicional. Se não usaste estas políticas, não há ação a tomar.

Para obter mais informações sobre os novos incumprimentos de segurança, veja [quais são os incumprimentos de segurança?](./concept-fundamentals-security-defaults.md) Para obter mais informações sobre as políticas de acesso condicional, consulte [as políticas comuns de acesso condicional.](../conditional-access/concept-conditional-access-policy-common.md)

---

## <a name="november-2019"></a>Novembro de 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Suporte para o atributo SameSite e Chrome 80

**Tipo:** Plano de mudança  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador

Como parte de um modelo seguro por padrão para cookies, o navegador Chrome 80 está a mudar a forma como trata os cookies sem o `SameSite` atributo. Qualquer cookie que não especifique o `SameSite` atributo será tratado como se estivesse definido para , o que `SameSite=Lax` resultará no bloqueio do Chrome a certos cenários de partilha de cookies de domínio transversal de que a sua app pode depender. Para manter o comportamento mais antigo do Chrome, pode utilizar o `SameSite=None` atributo e adicionar um atributo `Secure` adicional, para que os cookies do cross-site só possam ser acedidos sobre as ligações HTTPS. O Chrome deverá concluir esta mudança até 4 de fevereiro de 2020.

Recomendamos que todos os nossos desenvolvedores testem as suas apps usando esta orientação:

- Defina o valor predefinido para a definição **de Cookie Seguro de Utilização** para **Sim**.

- Deite o valor predefinido para o atributo **SameSite** a **Nenhum**.

- Adicione um atributo adicional `SameSite` de **Secure**.

Para obter mais informações, consulte [as Próximas Alterações de Cookies SameSite em ASP.NET e ASP.NET perturbação do núcleo](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) e potencial para os [websites dos clientes e produtos e serviços da Microsoft na versão 79 do Chrome e posteriormente.](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Novo hotfix para Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Tipo:** Fixo  
**Categoria de serviço:** Gestor de Identidade da Microsoft  
**Capacidade do produto:** Gestão do ciclo de vida da identidade

Um pacote de rollup hotfix (build 4.6.34.0) está disponível para o Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Este pacote rollup resolve problemas e adiciona melhorias que são descritas na secção "Problemas fixos e melhorias adicionadas nesta atualização".

Para obter mais informações e para descarregar o pacote hotfix, consulte o [Microsoft Identity Manager 2 (build 4.6.34.0) Update Rollup está disponível](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Novo relatório de atividade da app AD FS para ajudar a migrar aplicações para Azure AD (Visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** SSO

Utilize o novo relatório de atividade da App Do Diretório Ativo (AD FS), no portal Azure, para identificar quais das suas apps são capazes de ser migradas para a Azure AD. O relatório avalia todas as aplicações AD FS para compatibilidade com a Azure AD, verifica quaisquer problemas e dá orientações sobre a preparação de aplicações individuais para a migração.

Para obter mais informações, consulte [o relatório de atividade da aplicação AD FS para migrar as aplicações para Azure AD](../manage-apps/migrate-adfs-application-activity.md).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Novo fluxo de trabalho para os utilizadores solicitarem o consentimento do administrador (Visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Controlo de Acessos

O novo fluxo de trabalho de consentimento administrativo dá aos administradores uma forma de conceder acesso a apps que requerem aprovação de administração. Se um utilizador tentar aceder a uma aplicação, mas não conseguir obter o seu consentimento, pode agora enviar um pedido de aprovação de administração. O pedido é enviado por e-mail, e colocado numa fila acessível a partir do portal Azure, a todos os administradores que foram designados como revisores. Depois de um revisor tomar medidas sobre um pedido pendente, os utilizadores que solicitam são notificados da ação.

Para obter mais informações, consulte Configurar o fluxo de trabalho de consentimento administrativo [(pré-visualização)](../manage-apps/configure-admin-consent-workflow.md).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nova experiência de configuração de registos de aplicações AZure AD para gerir reclamações opcionais (Visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Experiência de Desenvolvedor

A nova lâmina de **configuração de Registos de Aplicações AZure AD** no portal Azure mostra agora aos desenvolvedores de aplicações uma lista dinâmica de reclamações opcionais para as suas apps. Esta nova experiência ajuda a agilizar as migrações de aplicações AD da Azure e a minimizar as alegações opcionais de configurações erradas.

Para obter mais informações, consulte [Fornecer reclamações opcionais à sua aplicação AD Azure.](../develop/active-directory-optional-claims.md)

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Novo fluxo de trabalho de aprovação em duas fases na gestão de direitos Azure AD (Visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Gestão de Direitos

Introduzimos um novo fluxo de trabalho de aprovação em duas fases que permite que dois aprovadores aprovem o pedido de um utilizador para um pacote de acesso. Por exemplo, pode defini-lo para que o gestor do utilizador que solicita tenha primeiro de aprovar, e depois também pode exigir que um proprietário de recursos aprove. Se um dos aprovadores não aprovar, o acesso não é concedido.

Para obter mais informações, consulte [as definições de pedido de alteração e aprovação para um pacote de acesso na gestão de direitos AD da Azure](../governance/entitlement-management-access-package-request-policy.md).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Atualizações para a página My Apps juntamente com novos espaços de trabalho (Visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** As minhas apps  
**Capacidade do produto:** Integração de Terceiros

Agora pode personalizar a forma como os utilizadores da sua organização vêem e acedem à experiência das Minhas Apps. Esta nova experiência também inclui a nova funcionalidade de espaços de trabalho, o que facilita a utilizadores a encontrar e organizar aplicações.

Para obter mais informações sobre a experiência my apps e criar espaços de trabalho, consulte [Criar espaços de trabalho no portal My Apps.](../manage-apps/access-panel-collections.md)

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Suporte social google iD para colaboração Azure AD B2B (Disponibilidade Geral)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** Autenticação do Utilizador

O novo suporte para a utilização de IDs sociais do Google (contas Gmail) em Azure AD ajuda a simplificar a colaboração para os seus utilizadores e parceiros. Já não há necessidade de os seus parceiros criarem e gerirem uma nova conta específica da Microsoft. O Microsoft Teams suporta agora totalmente os utilizadores do Google em todos os clientes e em todos os pontos finais de autenticação comuns e relacionados com o inquilino.

Para obter mais informações, consulte [o Add Google como um fornecedor de identidade para utilizadores convidados B2B.](../external-identities/google-federation.md)

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Suporte móvel do Microsoft Edge para acesso condicional e seduca (Disponibilidade Geral)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Proteção & de Segurança de Identidade

Azure AD para o Microsoft Edge no iOS e Android agora suporta Azure AD Single Sign-On e Acesso Condicional:

- **Microsoft Edge single sign-on (SSO):** O login único está agora disponível em clientes nativos (como o Microsoft Outlook e o Microsoft Edge) para todas as aplicações ligadas ao AD Azure.

- **Acesso condicional do Microsoft Edge:** Através de políticas de acesso condicional baseadas em aplicações, os seus utilizadores devem utilizar navegadores protegidos pelo Microsoft Intune, como o Microsoft Edge.

Para obter mais informações sobre acesso condicional e SSO com o Microsoft Edge, consulte o Suporte Móvel microsoft Edge para acesso condicional e post de blog [de assinatura única agora geralmente disponível.](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) Para obter mais informações sobre como configurar as suas aplicações de clientes utilizando [acesso condicional baseado em aplicações](../conditional-access/app-based-conditional-access.md) ou acesso condicional baseado em [dispositivos,](../conditional-access/require-managed-devices.md)consulte Gerir o acesso à Web utilizando um navegador protegido por políticas do [Microsoft Intune.](/intune/apps/app-configuration-managed-browser)

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Gestão de direitos Azure AD (Disponibilidade Geral)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Gestão de Direitos

A gestão de direitos da AD AZure é uma nova funcionalidade de governação de identidade, que ajuda as organizações a gerir a identidade e a aceder ao ciclo de vida em escala. Esta nova funcionalidade ajuda automatizando fluxos de trabalho de pedidos de acesso, atribuições de acesso, avaliações e expiração entre grupos, apps e sites Online do SharePoint.

Com a gestão de direitos AD Azure, você pode gerir de forma mais eficiente o acesso tanto para funcionários como também para utilizadores fora da sua organização que precisam de acesso a esses recursos.

Para mais informações, consulte [o que é a gestão de direitos da AD Azure?](../governance/entitlement-management-overview.md#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o fornecimento de conta de utilizador para estas novas aplicações SaaS apoiadas

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros  

Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:

[Serviço de Autenticação de Identidade da Plataforma DE Nuvem SAP,](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md) [RingCentral,](../saas-apps/ringcentral-provisioning-tutorial.md) [SpaceIQ,](../saas-apps/spaceiq-provisioning-tutorial.md) [Miro,](../saas-apps/miro-provisioning-tutorial.md) [Cloudgate,](../saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial.md) [Infor CloudSuite,](../saas-apps/infor-cloudsuite-provisioning-tutorial.md) [OfficeSpace Software](../saas-apps/officespace-software-provisioning-tutorial.md), [Matriz Prioritária](../saas-apps/priority-matrix-provisioning-tutorial.md)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte [automatizar o fornecimento de utilizadores para aplicações SaaS com Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - novembro 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros

Em novembro de 2019, adicionámos estas 21 novas aplicações com apoio da Federação à galeria de aplicações:

[Airtable](../saas-apps/airtable-tutorial.md), [Hootsuite,](../saas-apps/hootsuite-tutorial.md) [Blue Access for Members (BAM)](../saas-apps/blue-access-for-members-tutorial.md), [Bitly](../saas-apps/bitly-tutorial.md), [Riva](../saas-apps/riva-tutorial.md), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Single Sign On (SSO)](../saas-apps/negometrixportal-tutorial.md), [TeamsChamp,](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279) [Motus,](../saas-apps/motus-tutorial.md) [MyAryaka,](../saas-apps/myaryaka-tutorial.md) [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](../saas-apps/visma-tutorial.md), [OneDesk](../saas-apps/onedesk-tutorial.md), [Foko Retail,](../saas-apps/foko-retail-tutorial.md) [Qmarkets Idea & Innovation Management](../saas-apps/qmarkets-idea-innovation-management-tutorial.md), [Netskope User Authentication](../saas-apps/netskope-user-authentication-tutorial.md), [uniFLOW Online,](../saas-apps/uniflow-online-tutorial.md) [Claromentis,](../saas-apps/claromentis-tutorial.md) [Jisc Student Registration](../saas-apps/jisc-student-voter-registration-tutorial.md), [e4enable](https://portal.e4enable.com/)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Galeria de aplicações AD AZure nova e melhorada

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** SSO

Atualizámos a galeria de aplicações AD AZure para facilitar a procura de aplicações pré-integradas que suportem o provisionamento, o OpenID Connect e o SAML no seu inquilino Azure Ative Directory.

Para mais informações, consulte [Adicionar uma aplicação ao seu inquilino Azure Ative Directory.](../manage-apps/add-application-portal.md)

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Aumento do limite de comprimento de definição de função da aplicação de 120 para 240 caracteres

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** SSO

Ouvimos dos clientes que o limite de comprimento para o valor de definição de função da aplicação em algumas apps e serviços é muito curto em 120 caracteres. Em resposta, aumentámos o comprimento máximo da definição de valor de função para 240 caracteres.

Para obter mais informações sobre a utilização de definições de funções específicas para aplicações, consulte [as funções da aplicação na sua aplicação e receba-as no token](../develop/howto-add-app-roles-in-azure-ad-apps.md).

---

## <a name="october-2019"></a>Outubro de 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Depreciação da identidadeRiskEvent API para deteções de risco de proteção de identidade Azure AD

**Tipo:** Plano de alteração **da categoria serviço:** Capacidade de proteção de **identidade: Proteção** de identidade & Proteção

Em resposta ao feedback do desenvolvedor, os subscritores Azure AD Premium P2 podem agora realizar consultas complexas sobre os dados de deteção de risco da Azure AD Identity Protection utilizando a nova API de deteção de risco para o Microsoft Graph. A versão beta da [API](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) de identidade existente irá deixar de devolver os dados em **10 de janeiro de 2020**. Se a sua organização estiver a usar a identidadeRiskEvent API, deverá transitar para a nova API de riscodetecção.

Para obter mais informações sobre o novo risco API de deteção de risco, consulte a documentação de referência da [API de deteção de risco.](/graph/api/resources/riskdetection)

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Suporte proxy de aplicação para o Atributo SameSite e Chrome 80

**Tipo:** Plano de mudança **categoria de serviço:** Capacidade do produto Proxy **app:** Controlo de acesso

Algumas semanas antes do lançamento do navegador Chrome 80, pretendemos atualizar como os cookies Proxy da Aplicação tratam o atributo **SameSite.** Com o lançamento do Chrome 80, qualquer cookie que não especifique o atributo **SameSite** será tratado como se estivesse definido para `SameSite=Lax` .

Para ajudar a evitar impactos potencialmente negativos devido a esta mudança, estamos a atualizar o acesso ao Proxy da Aplicação e cookies de sessão através de:

- Definição do valor predefinido para a definição **de Cookie Seguro de Utilização** para **Sim**.

- Definição do valor predefinido para o atributo **SameSite** a **Nenhum**.

    >[!NOTE]
    > Os cookies de acesso proxy de aplicação sempre foram transmitidos exclusivamente através de canais seguros. Estas alterações aplicam-se apenas aos cookies de sessão.

Para obter mais informações sobre as definições de cookies Proxy da aplicação, consulte [as definições de Cookies para aceder a aplicações no local no Azure Ative Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>As inscrições de aplicações (legado) e a gestão de aplicações no Portal de Registo de Aplicações (apps.dev.microsoft.com) já não estão disponíveis

**Tipo:** Plano de alteração **da categoria de serviço:** Capacidade do produto N/A: Experiência do Desenvolvedor 

Os utilizadores com contas AZURE AD já não podem registar ou gerir aplicações utilizando o Portal de Registo de Aplicações (apps.dev.microsoft.com), ou registar e gerir aplicações na experiência de registos (legado) da App no portal Azure.

Para saber mais sobre a experiência das novas inscrições da App, consulte as inscrições da App no guia de [formação do portal Azure.](../develop/quickstart-register-app.md)

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Os utilizadores deixaram de ser obrigados a re-registar-se durante a migração de MFA por utilizador para MFA baseado em acesso condicional

**Tipo:** Categoria **serviço fixo:** Capacidade do produto MFA: Proteção & de Segurança de Identidade 

Corrigimos um problema conhecido pelo qual, quando os utilizadores eram obrigados a re-registar-se se fossem desativados para a Autenticação Multi-Factor (MFA) por utilizador e depois ativados para MFA através de uma política de Acesso Condicional.

Para exigir que os utilizadores se registem, pode selecionar a opção **MFA de re-registo requerida** a partir dos métodos de autenticação do utilizador no portal AD Azure. Para obter mais informações sobre a migração de utilizadores de MFA por utilizador para MFA baseado em acesso condicional, consulte [Converter os utilizadores de MFA por utilizador para MFA baseado em acesso condicional](../authentication/howto-mfa-getstarted.md#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Novas capacidades para transformar e enviar reclamações no seu token SAML

**Tipo:** Nova **categoria de funcionalidades:** Capacidade do produto de aplicações **empresariais:** SSO

Adicionámos capacidades adicionais para o ajudar a personalizar e enviar reclamações no seu token SAML. Estas novas capacidades incluem:

- Funções adicionais de transformação de reclamações, ajudando-o a modificar o valor que envia na reclamação.

- Capacidade de aplicar múltiplas transformações a uma única reivindicação.

- Capacidade de especificar a fonte de reclamação, com base no tipo de utilizador e no grupo a que o utilizador pertence.

Para obter informações detalhadas sobre estas novas capacidades, incluindo como usá-las, consulte [Personalizar as reclamações emitidas no token SAML para aplicações empresariais.](../develop/active-directory-saml-claims-customization.md)

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nova página my's ins para utilizadores finais em Azure AD

**Tipo:** Nova **categoria de serviço** de recurso: Autenticações (Logins) **Capacidade do produto:** Monitorização & Reporte

Adicionámos uma nova página **my's Ins** https://mysignins.microsoft.com) (para permitir que os utilizadores da sua organização vejam o seu histórico de entrada recente para verificar qualquer atividade incomum. Esta nova página permite que os seus utilizadores vejam:

- Se alguém está a tentar adivinhar a sua senha.

- Se um agressor se inscreveu com sucesso na sua conta e a partir de que local.

- Que aplicações o agressor tentou aceder.

Para mais informações, consulte os [Utilizadores que podem agora verificar o seu histórico de inscrições para](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) um blog de atividades invulgares.

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migração de Serviços de Domínio AZure AD (Azure AD DS) de redes virtuais clássicas para Azure Resource Manager

**Tipo:** Nova **categoria de serviço de recurso:** Azure AD Domain Services **Capacidade do produto:** Azure AD Domain Services

Para os nossos clientes que têm estado presos em redes virtuais clássicas. Agora pode realizar uma migração única de uma rede virtual clássica para uma rede virtual do Gestor de Recursos existente. Depois de se mudar para a rede virtual do Gestor de Recursos, poderá tirar partido das funcionalidades adicionais e atualizadas, tais como políticas de palavras-passe de grão fino, notificações de e-mail e registos de auditoria.

Para obter mais informações, consulte [Preview - Migrate Azure AD Domain Services from the Classic virtual network model to Resource Manager](../../active-directory-domain-services/migrate-from-classic-vnet.md).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Atualizações para o layout do contrato de página AZure AD B2C

**Tipo:** Nova **categoria de serviço de recurso:** B2C - Capacidade de Produto de Gestão de Identidade do **Consumidor:** B2B/B2C

Introduzimos algumas novas alterações na versão 1.2.0 do contrato de página para Azure AD B2C. Nesta versão atualizada, pode agora controlar a ordem de carga dos seus elementos, o que também pode ajudar a parar o piscar que acontece quando a folha de estilo (CSS) está carregada.

Para obter uma lista completa das alterações es feitas ao contrato de página, consulte o [registo de alteração de versão](../../active-directory-b2c/page-layout.md#other-pages-providerselection-claimsconsent-unifiedssd).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Atualização para a página My Apps juntamente com novos espaços de trabalho (pré-visualização pública)

**Tipo:** Nova **categoria de serviço de funcionalidade:** A **minha capacidade de produto apps:** Controlo de acesso

Agora pode personalizar a forma como os utilizadores da sua organização vêem e acedem à experiência novísica My Apps, incluindo a utilização da nova funcionalidade de espaços de trabalho para facilitar a procura de apps. A nova funcionalidade de espaço de trabalho funciona como um filtro para as aplicações a que os utilizadores da sua organização já têm acesso.

Para obter mais informações sobre a saída da nova experiência My Apps e criar espaços de trabalho, consulte [Criar espaços de trabalho no portal My Apps (pré-visualização).](../manage-apps/access-panel-collections.md)

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Suporte para o modelo de faturação ativa mensal (disponibilidade geral)

**Tipo:** Nova **categoria de serviço de recurso:** B2C - Capacidade de Produto de Gestão de Identidade do **Consumidor:** B2B/B2C

O Azure AD B2C suporta agora a faturação mensal dos utilizadores ativos (MAU). A faturação DA MAU baseia-se no número de utilizadores únicos com atividade de autenticação durante um mês de calendário. Os clientes existentes podem mudar para este novo método de faturação a qualquer momento.

A partir de 1 de novembro de 2019, todos os novos clientes serão automaticamente cobrados através deste método. Este método de faturação beneficia os clientes através de benefícios de custos e da capacidade de planear o futuro.

Para obter mais informações, consulte [upgrade para o modelo de faturação de utilizadores ativos mensais.](../../active-directory-b2c/billing.md#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - outubro 2019

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em outubro de 2019, adicionámos estas 35 novas aplicações com apoio da Federação à galeria de aplicações:

[Em Caso de Crise – Mobile](../saas-apps/in-case-of-crisis-mobile-tutorial.md), Juno [Journey](../saas-apps/juno-journey-tutorial.md), [ExponentHR](../saas-apps/exponenthr-tutorial.md), [Tact,](https://www.tact.ai/products/tact-assistant) [OpusCapita Cash Management,](https://appsource.microsoft.com/product/web-apps/opuscapitagroupoy-1036255.opuscapita-cm) [Salestim,](https://www.salestim.com/) [Learnster,](../saas-apps/learnster-tutorial.md) [Dynatrace,](../saas-apps/dynatrace-tutorial.md) [HunchBuzz,](https://login.hunchbuzz.com/integrations/azure/process) [Freshworks,](../saas-apps/freshworks-tutorial.md) [eCornell,](../saas-apps/ecornell-tutorial.md) [ShipHazmat,](../saas-apps/shiphazmat-tutorial.md) [Netskope Cloud Security,](../saas-apps/netskope-cloud-security-tutorial.md) [Contentamento](../saas-apps/contentful-tutorial.md), [Bindtuning](https://bindtuning.com/login), [HireVue Coordinate – Europe](https://www.hirevue.com/), [HireVue Coordinate - USOnly](https://www.hirevue.com/), [HireVue Coordinate - US,](https://www.hirevue.com/) [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](../saas-apps/cloudmore-tutorial.md), [Visit.org,](../saas-apps/visitorg-tutorial.md) [Cambium Xirrus EasyPass Portal,](https://login.xirrus.com/azure-signup) [Paylocity,](../saas-apps/paylocity-tutorial.md) [Mail Luck!](../saas-apps/mail-luck-tutorial.md), [Teamie](https://theteamie.com/), [Velocity for Teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Navigate IMPL](../saas-apps/eab-navigate-impl-tutorial.md), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), Speaking Email [for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Talking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](../saas-apps/exactcare-sso-tutorial.md), [iHealthHome Care Navigation System](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Item do menu de Segurança Consolidada no portal AD AZure

**Tipo:** Categoria de serviço de funcionalidade **alterada:** **Capacidade de proteção de identidade: Proteção** & de Segurança de Identidade

Agora pode aceder a todas as funcionalidades de segurança Azure AD disponíveis a partir do novo item do menu **Security,** e a partir da barra **De pesquisa,** no portal Azure. Além disso, a nova página de aterragem **de Segurança,** chamada **Security - Getting started**, fornecerá ligações à nossa documentação pública, orientação de segurança e guias de implementação.

O novo menu **Security** inclui:

- Acesso Condicional
- Identity Protection
- Centro de Segurança
- Pontuação segura de identidade
- Métodos de autenticação
- MFA
- Relatórios de risco - Utilizadores arriscados, insuposições arriscadas, deteções de risco
- E mais...

Para mais informações, consulte [Segurança - Começar.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Office 365 grupos de expiração política reforçada com a autorenewal

**Tipo:** Categoria de serviço de funcionalidade **alterada:** Capacidade do produto de gestão de **grupo:** Gestão do ciclo de vida de identidade

A política de expiração dos grupos Office 365 foi reforçada para renovar automaticamente os grupos que estão ativamente a ser utilizados pelos seus membros. Os grupos serão auto-licenciados com base na atividade do utilizador em todas as aplicações do Office 365, incluindo Outlook, SharePoint e Teams.

Esta melhoria ajuda a reduzir as notificações de expiração do seu grupo e ajuda a garantir que os grupos ativos continuam disponíveis. Se já tem uma política de expiração ativa para os seus grupos Office 365, não precisa de fazer nada para ativar esta nova funcionalidade.

Para obter mais informações, consulte a política de [caducidade dos grupos Office 365](../enterprise-users/groups-lifecycle.md).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Experiência de criação atualizada dos Serviços de Domínio Azure AD (Azure AD DS)

**Tipo:** Alteração da **categoria de serviço de recurso:** Capacidade de produto Azure AD Domain **Services:** Azure AD Domain Services

Atualizámos os Azure AD Domain Services (Azure AD DS) para incluir uma nova e melhorada experiência de criação, ajudando-o a criar um domínio gerido em apenas três cliques! Além disso, agora pode carregar e implementar Azure AD DS a partir de um modelo.

Para obter mais informações, consulte [Tutorial: Criar e configurar uma instância de Serviços de Domínio do Diretório Ativo Azure](../../active-directory-domain-services/tutorial-create-instance.md).

---

## <a name="september-2019"></a>Setembro de 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Plano de mudança: Depreciação dos pacotes de conteúdo do Power BI

**Tipo:** Plano de alteração **da categoria de serviço:** Capacidade do produto de reporte: Monitorização & Reporte 

A partir de 1 de outubro de 2019, o Power BI começará a depreciar todos os pacotes de conteúdos, incluindo o pacote de conteúdos Azure AD Power BI. Como alternativa a este pacote de conteúdos, pode utilizar os Livros AD da Azure para obter informações sobre os seus serviços relacionados com AZure AD. Estão a chegar livros adicionais, incluindo livros sobre políticas de Acesso Condicional em modo apenas de relatório, insights baseados em consentimento de aplicações, e muito mais.

Para obter mais informações sobre os livros, consulte [Como utilizar os livros do Azure Monitor para relatórios do Azure Ative Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md). Para obter mais informações sobre a depreciação dos pacotes de conteúdo, consulte o post de blog de disponibilidade geral de aplicações do [modelo DeDR anunciado.](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/)

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>O meu perfil é renomear e integrar-se na página de conta do Microsoft Office

**Tipo:** Plano de alteração **da categoria de Serviço:** O meu perfil/capacidade **de produto de conta:** Colaboração

A partir de outubro, a experiência My Profile passará a ser a Minha Conta. Como parte dessa mudança, em todo o lado que atualmente diz, **o Meu Perfil** vai mudar para a Minha **Conta.** Além da mudança de nomeação e de algumas melhorias de design, a experiência atualizada oferecerá integração adicional com a página da conta do Microsoft Office. Especificamente, poderá aceder a instalações e subscrições do Office a partir da página **Conta Geral,** juntamente com as preferências de contacto relacionadas com o Office a partir da página **de Privacidade.**

Para obter mais informações sobre a experiência My Profile (pré-visualização), consulte [a visão geral do portal Do Meu Perfil (pré-visualização).](../user-help/my-account-portal-overview.md)

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>A granel gere grupos e membros usando ficheiros CSV no portal AD Azure (Visualização pública)

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do Produto de Gestão de **Grupo:** Colaboração

Temos o prazer de anunciar a disponibilidade pública de visualização das experiências de gestão de grupos a granel no portal AD AZure. Agora pode utilizar um ficheiro CSV e o portal AD Azure para gerir grupos e listas de membros, incluindo:

- Adicionar ou remover membros de um grupo.

- A descarregar a lista de grupos do diretório.

- Descarregando a lista de membros do grupo para um grupo específico.

Para obter mais informações, consulte [a Bulk add members](../enterprise-users/groups-bulk-import-members.md), Bulk remove [members,](../enterprise-users/groups-bulk-remove-members.md) [Bulk download members list](../enterprise-users/groups-bulk-download-members.md), and Bulk download [groups](../enterprise-users/groups-bulk-download.md).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>O consentimento dinâmico é agora suportado através de um novo ponto final de consentimento administrativo

**Tipo:** Nova **categoria de serviço** de funcionalidade: Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

Criámos um novo ponto final de consentimento para suporte ao consentimento dinâmico, o que é útil para aplicações que pretendem utilizar o modelo de consentimento dinâmico na plataforma Microsoft Identity.

Para obter mais informações sobre como utilizar este novo ponto final, consulte [utilizando o ponto final de consentimento de administração](../develop/v2-admin-consent.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - setembro 2019

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em setembro de 2019, adicionámos estas 29 novas aplicações com apoio da Federação à galeria de aplicações:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Acesso para Ethidex Compliance Office &trade; - Único sinal,](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md) [iServer Portal,](../saas-apps/iserver-portal-tutorial.md) [SKYSITE,](../saas-apps/skysite-tutorial.md) [Concur Viagens e Despesas,](../saas-apps/concur-travel-and-expense-tutorial.md) [WorkBoard](../saas-apps/workboard-tutorial.md), `https://apps.yeeflow.com/` ARC [Facilities,](../saas-apps/arc-facilities-tutorial.md) [Luware Stratus Team](https://stratus.emea.luware.cloud/login), Wide [Ideas,](https://wideideas.online/wideideas/) [Prisma Cloud,](../saas-apps/prisma-cloud-tutorial.md) [JDLT Client Hub](https://clients.jdlt.co.uk/login), [RENRAKU,](../saas-apps/renraku-tutorial.md) [SealPath Secure Browser,](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive) [Prisma Cloud,](../saas-apps/prisma-cloud-tutorial.md) `https://app.penneo.com/` , `https://app.testhtm.com/settings/email-integration` [Cintoo Cloud](https://aec.cintoo.com/login), [Whitesource](../saas-apps/whitesource-tutorial.md), [Hosted Heritage Online SSO](../saas-apps/hosted-heritage-online-sso-tutorial.md), [IDC,](../saas-apps/idc-tutorial.md) [CakeHR,](../saas-apps/cakehr-tutorial.md) [BIS,](../saas-apps/bis-tutorial.md) [Coo Kai Team Build,](https://ms-contacts.coo-kai.jp/) [Sonarqube,](../saas-apps/sonarqube-tutorial.md) [Adobe Identity Management,](../saas-apps/tutorial-list.md) [Discovery Benefits SSO,](../saas-apps/discovery-benefits-sso-tutorial.md) [Amelio,](https://app.amelio.co/)`https://itask.yipinapp.com/`

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-azure-ad-global-reader-role"></a>Novo papel de leitor global da Azure AD

**Tipo:** Nova **categoria de funcionalidades Serviço:** Azure AD roles **Capacidade de produto:** Controlo de acesso

A partir de 24 de setembro de 2019, vamos começar a lançar um novo papel de Azure Ative Directory (AD) chamado Global Reader. Este lançamento começará com a produção e os clientes da Global cloud (GCC), terminando em todo o mundo em outubro.

O papel de Global Reader é a contrapartida apenas de leitura para o Administrador Global. Os utilizadores desta função podem ler definições e informações administrativas em todos os serviços da Microsoft 365, mas não podem tomar ações de gestão. Criámos o papel de Global Reader para ajudar a reduzir o número de Administradores Globais na sua organização. Como as contas do Administrador Global são poderosas e vulneráveis a ataques, recomendamos que tenha menos de cinco Administradores Globais. Recomendamos a utilização do papel de Global Reader para planeamento, auditorias ou investigações. Recomendamos também a utilização do papel de Global Reader em combinação com outras funções de administrador limitada, como o Exchange Administrator, para ajudar a fazer o trabalho sem exigir o papel de Administrador Global.

O papel de Global Reader funciona com o novo Microsoft 365 Admin Center, Exchange Admin Center, Teams Admin Center, Security Center, Compliance Center, Azure AD Admin Center e o Device Management Admin Center.

>[!NOTE]
> No início da pré-visualização pública, o papel do Global Reader não funcionará com: SharePoint, Gestão de Acesso Privilegiado, Bloqueio de Clientes, etiquetas de sensibilidade, Ciclo de Vida das Equipas, Equipas A reportar & Call Analytics, Teams IP Phone Device Management e Teams App Catalog.

Para obter mais informações, consulte [permissões de função de administrador no Diretório Ativo Azure](../roles/permissions-reference.md).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Aceda a um Servidor de Relatório no Local a partir da sua aplicação Power BI Mobile utilizando o Azure Ative Directory Application Proxy

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade de produto Proxy **app:** Controle de Acesso

A nova integração entre a aplicação móvel Power BI e o Azure AD Application Proxy permite-lhe iniciar sessão de forma segura na aplicação móvel Power BI e ver qualquer um dos relatórios da sua organização alojados no Power BI Report Server.

Para obter informações sobre a aplicação Power BI Mobile, incluindo onde descarregar a aplicação, consulte o [site Power BI](https://powerbi.microsoft.com/mobile/). Para obter mais informações sobre como configurar a aplicação móvel Power BI com Proxy de aplicação AD AD Azure, consulte [Ativar o acesso remoto ao Power BI Mobile com Proxy de aplicação AD ADure](../manage-apps/application-proxy-integrate-with-power-bi.md).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Está disponível a nova versão do módulo AzureADPreview PowerShell

**Tipo:** Alteração **da categoria de serviço de recurso:** Outra **capacidade do produto:** Diretório

Novos cmdlets foram adicionados ao módulo AzureADPreview, para ajudar a definir e atribuir funções personalizadas em Azure AD, incluindo:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nova versão do Azure AD Connect

**Tipo:** Alteração **da categoria de serviço de recurso:** Outra **capacidade do produto:** Diretório

Lançámos uma versão atualizada do Azure AD Connect para clientes de upgrade automático. Esta nova versão inclui várias novidades, melhorias e correções de bugs. Para obter mais informações sobre esta nova versão, consulte [O Azure AD Connect: O histórico de lançamentos da versão](../hybrid/reference-connect-version-history.md#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Servidor de Autenticação Multi-Factor (MFA) Azure, versão 8.0.2 já está disponível

**Tipo:** Categoria **serviço fixo:** Capacidade do produto MFA: Proteção & de Segurança de Identidade 

Se é um cliente existente, que ativou o MFA Server antes de 1 de julho de 2019, pode agora descarregar a versão mais recente do MFA Server (versão 8.0.2). Nesta nova versão, nós:

- Corrigiu um problema para que quando a sincronização AZure AD muda um utilizador de Desativado para Ativado, é enviado um e-mail ao utilizador.

- Corrigiu um problema para que os clientes pudessem fazer o upgrade com sucesso, enquanto continuavam a utilizar a funcionalidade Tags.

- Acrescentou o código do país do Kosovo (+383).

- Adicionou uma auditoria de bypass único ao MultiFactorAuthSvc.log.

- Melhor desempenho para o Web Service SDK.

- Consertado outros pequenos insetos.

A partir de 1 de julho de 2019, a Microsoft deixou de oferecer O MFA Server para novas implementações. Os novos clientes que necessitam de autenticação multi-factor devem utilizar a autenticação multi-factor Azure AD baseada na nuvem. Para obter mais informações, consulte [Planejando uma implementação de autenticação multi-factor Azure AD baseada na nuvem](../authentication/howto-mfa-getstarted.md).

---

## <a name="august-2019"></a>Agosto de 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>A procura, filtragem e triagem melhoradas para grupos estão disponíveis no portal AD Azure (Visualização Pública)

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do Produto de Gestão de **Grupo:** Colaboração

Temos o prazer de anunciar a disponibilidade pública de visualização das experiências relacionadas com grupos melhorados no portal AD AZure. Estas melhorias ajudam-no a gerir melhor os grupos e as listas de membros, fornecendo:

- Capacidades de pesquisa avançadas, tais como sub-adiscurar pesquisas em listas de grupos.
- Opções avançadas de filtragem e triagem nas listas de membros e proprietários.
- Novas capacidades de pesquisa para listas de membros e proprietários.
- O grupo mais preciso conta para grandes grupos.

Para obter mais informações, consulte [Gerir grupos no portal Azure.](./active-directory-groups-members-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Estão disponíveis novas funções personalizadas para gestão de registo de aplicações (Visualização pública)

**Tipo:** Nova **categoria de funcionalidades Serviço:** Azure AD roles **Capacidade de produto:** Controlo de acesso

As funções personalizadas (disponíveis com uma subscrição AZure AD P1 ou P2) podem agora ajudar-lhe a fornecer acesso fino, permitindo-lhe criar definições de papéis com permissões específicas e, em seguida, atribuir essas funções a recursos específicos. Atualmente, cria funções personalizadas usando permissões para gerir registos de aplicações e, em seguida, atribuindo o papel a uma aplicação específica. Para obter mais informações sobre funções personalizadas, consulte [as funções de administrador personalizado no Diretório Ativo Azure (pré-visualização)](../roles/custom-overview.md).

Se precisar de permissões ou recursos adicionais suportados, o que não vê atualmente, pode enviar feedback para o nosso [site de feedback Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) e adicionaremos o seu pedido ao nosso roteiro de atualização.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Novos registos de provisionamento podem ajudá-lo a monitorizar e resolver problemas na implementação de provisionamento de aplicações (Visualização pública)

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade de Fornecimento de **Aplicativos: Gestão** do ciclo de vida da identidade

Novos registos de provisionamento estão disponíveis para ajudá-lo a monitorizar e resolver problemas na implementação do fornecimento de provisionamento do utilizador e do grupo. Estes novos ficheiros de registo incluem informações sobre:

- Que grupos foram criados com sucesso no [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)
- Que funções foram importadas da [Amazon Web Services (AWS)](../saas-apps/amazon-web-service-tutorial.md#configure-and-test-azure-ad-sso-for-amazon-web-services-aws)
- O que os empregados não foram importados do [Workday](../saas-apps/workday-inbound-tutorial.md)

Para obter mais informações, consulte [relatórios de Provisionamento no portal Azure Ative Directory (pré-visualização)](../reports-monitoring/concept-provisioning-logs.md).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Novos relatórios de segurança para todos os administradores da AD Azure (Disponibilidade Geral)

**Tipo:** Nova **categoria de serviço** de recurso: **Capacidade de proteção de identidade: Proteção** & de Segurança de Identidade

Por padrão, todos os administradores da AD Azure em breve poderão aceder aos relatórios de segurança modernos dentro do Azure AD. Até ao final de setembro, poderá usar o estandarte no topo dos relatórios de segurança modernos para voltar aos relatórios antigos.

Os relatórios de segurança modernos fornecerão capacidades adicionais das versões mais antigas, incluindo:

- Filtragem e triagem avançadas
- Ações a granel, tais como descartar o risco do utilizador
- Confirmação de entidades comprometidas ou seguras
- Estado de risco, cobrindo: Em risco, dispensado, remediado e confirmado comprometido
- Novas deteções relacionadas com o risco (disponíveis para assinantes Azure AD Premium)

Para obter mais informações, consulte [utilizadores](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users) [arriscados, insusouções de risco](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)e [deteções de risco.](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections)

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>A identidade gerida atribuída pelo utilizador está disponível para máquinas virtuais e conjuntos de escala de máquinas virtuais (Disponibilidade geral)

**Tipo:** Nova **categoria de serviço de recurso:** Identidades geridas para recursos Azure **Capacidade de produto:** Developer Experience

As identidades geridas atribuídas pelo utilizador estão agora geralmente disponíveis para máquinas virtuais e conjuntos de escala de máquinas virtuais. Como parte disso, a Azure pode criar uma identidade no inquilino AZure AD que é confiável pela subscrição em uso, e pode ser atribuída a uma ou mais instâncias de serviço Azure. Para obter mais informações sobre identidades geridas atribuídas pelo utilizador, consulte [o que são identidades geridas para os recursos Azure?](../managed-identities-azure-resources/overview.md)

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Os utilizadores podem redefinir as suas palavras-passe usando uma aplicação móvel ou um token de hardware (Disponibilidade Geral)

**Tipo:** Categoria **de serviço** de funcionalidade alterada: Autosserviço Password Redefinir **capacidade de produto:** Autenticação do utilizador

Os utilizadores que tenham registado uma aplicação móvel junto da sua organização podem agora redefinir a sua própria palavra-passe aprovando uma notificação da aplicação Microsoft Authenticator ou introduzindo um código a partir da sua aplicação móvel ou token de hardware.

Para obter mais informações, consulte [como funciona: Redefinição da palavra-passe de autosserviço Azure AD](../authentication/concept-sspr-howitworks.md). Para obter mais informações sobre a experiência do utilizador, consulte [redefinir o seu próprio trabalho ou a visão geral da palavra-passe da escola.](../user-help/active-directory-passwords-reset-register.md)

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignora a cache partilhada MSAL.NET para cenários em nome

**Tipo:** **Categoria serviço fixo:** Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

Começando pela biblioteca de autenticação AD AD (ADAL.NET) versão 5.0.0 pré-visualização, os desenvolvedores de aplicações devem [serializar uma cache por conta para aplicações web e APIs web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). Caso contrário, alguns cenários que utilizam o [fluxo em nome de](../develop/scenario-web-api-call-api-app-configuration.md?tabs=java) Java, juntamente com alguns casos específicos de uso `UserAssertion` de, podem resultar numa elevação do privilégio. Para evitar esta vulnerabilidade, ADAL.NET agora ignora a Biblioteca de Autenticação da Microsoft para a cache partilhada do dotnet (MSAL.NET) para cenários em nome.

Para obter mais informações sobre este assunto, consulte [a Azure Ative Directory Authentication Library Elevation of Privilege Vulnerability](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - agosto 2019

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em agosto de 2019, adicionámos estas 26 novas aplicações com apoio da Federação à galeria de aplicações:

[Plataforma Cívica](../saas-apps/civic-platform-tutorial.md), [Amazon Business](../saas-apps/amazon-business-tutorial.md), [ProNovos Ops Manager,](../saas-apps/pronovos-ops-manager-tutorial.md) [Cognidox](../saas-apps/cognidox-tutorial.md), [Viareport's Inativ Portal (Europa)](../saas-apps/viareports-inativ-portal-europe-tutorial.md), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](../saas-apps/robin-tutorial.md), [Academy Assiding](../saas-apps/academy-attendance-tutorial.md), [Priority Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](../saas-apps/carbonite-endpoint-backup-tutorial.md), [CPQSync by Cincom](../saas-apps/cpqsync-by-cincom-tutorial.md), [Chargebee,](../saas-apps/chargebee-tutorial.md) [deliver.media &trade; Portal](https://portal.deliver.media), [Frontline Education](../saas-apps/frontline-education-tutorial.md), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD connect,](https://www.stashcat.com) [Blink](../saas-apps/blink-tutorial.md), [Vocoli,](../saas-apps/vocoli-tutorial.md) [ProNovos Analytics,](../saas-apps/pronovos-analytics-tutorial.md) [Sigstr,](../saas-apps/sigstr-tutorial.md) [Darwinbox,](../saas-apps/darwinbox-tutorial.md) [Watch by Colors](../saas-apps/watch-by-colors-tutorial.md), [Harness](../saas-apps/harness-tutorial.md), [EAB Navigate Strategic Care](../saas-apps/eab-navigate-strategic-care-tutorial.md)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Estão disponíveis novas versões dos módulos AzureAD PowerShell e AzureADPreview PowerShell

**Tipo:** Alteração **da categoria de serviço de recurso:** Outra **capacidade do produto:** Diretório

Estão disponíveis novas atualizações para os módulos PowerShell de pré-visualização AzureAD e AzureAD:

- Um novo `-Filter` parâmetro foi adicionado ao parâmetro no módulo `Get-AzureADDirectoryRole` AzureAD. Este parâmetro ajuda-o a filtrar as funções de diretório devolvidas pelo cmdlet.
- Novos cmdlets foram adicionados ao módulo AzureADPreview, para ajudar a definir e atribuir funções personalizadas em Azure AD, incluindo:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Melhorias na UI do dinâmico construtor de regras do grupo no portal Azure

**Tipo:** Categoria **de serviço** de funcionalidade alterada: Capacidade do produto de gestão **de grupo:** colaboração

Fizemos algumas melhorias na UI ao dinâmico construtor de regras do grupo, disponível no portal Azure, para ajudá-lo mais facilmente a configurar uma nova regra, ou alterar as regras existentes. Esta melhoria de design permite criar regras com até cinco expressões, em vez de apenas uma. Também atualizámos a lista de propriedades do dispositivo para remover as propriedades do dispositivo precotado.

Para obter mais informações, consulte [Gerir as regras de adesão dinâmicas.](../enterprise-users/groups-dynamic-membership.md)

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nova permissão da aplicação Microsoft Graph disponível para utilização com comentários de acesso

**Tipo:** Alteração **da categoria de serviço de recurso:** Acesso Revê **a capacidade do produto:** Governação da Identidade

Introduzimos uma nova permissão para aplicações microsoft Graph, `AccessReview.ReadWrite.Membership` que permite que as aplicações criem e recuperem automaticamente comentários de acesso para membros do grupo e atribuições de aplicações. Esta permissão pode ser utilizada pelos seus trabalhos programados ou como parte da sua automatização, sem necessitar de um contexto de utilizador iniciado.

Para obter mais informações, consulte o [Exemplo de como criar avaliações de acesso AD AZure utilizando permissões de aplicações do Microsoft Graph com o blog PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Os registos de atividades da AD Azure já estão disponíveis para instâncias de nuvem governamental no Azure Monitor

**Tipo:** Categoria de serviço de funcionalidade **alterada:** Capacidade do produto de reporte: Monitorização & Reporte 

Estamos entusiasmados por anunciar que os registos de atividade da AD Azure estão agora disponíveis para casos de nuvem governamental no Azure Monitor. Pode agora enviar registos AD AD da Azure para a sua conta de armazenamento ou para um centro de eventos para integrar com as suas ferramentas SIEM, como [a Sumologic,](../reports-monitoring/howto-integrate-activity-logs-with-sumologic.md) [Splunk](../reports-monitoring/howto-integrate-activity-logs-with-splunk.md)e [ArcSight.](../reports-monitoring/howto-integrate-activity-logs-with-arcsight.md)

Para obter mais informações sobre a criação do Azure Monitor, consulte [os registos de atividade da AD Azure no Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Atualize os seus utilizadores para a nova experiência de informação de segurança melhorada

**Tipo:** Alteração **da categoria de serviço**  de funcionalidade: Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

No dia 25 de setembro de 2019, vamos desligar a antiga experiência de informação de segurança não reforçada para registar e gerir informações de segurança dos utilizadores e apenas ligar a nova [versão melhorada.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271) Isto significa que os seus utilizadores deixarão de poder utilizar a experiência antiga.

Para mais informações sobre a experiência de informação de segurança reforçada, consulte a nossa [documentação de administração](../authentication/concept-registration-mfa-sspr-combined.md) e [a documentação do](../user-help/security-info-setup-signin.md)nosso utilizador.

#### <a name="to-turn-on-this-new-experience-you-must"></a>Para dar a conhecer esta nova experiência, deve:

1. Inscreva-se no portal Azure como Administrador Global ou Administrador de Utilizador.

2. Aceda ao **Azure Ative Directory > As definições do utilizador > Gerir as definições para funcionalidades de pré-visualização do painel de acesso**.

3. Nos **Utilizadores podem utilizar funcionalidades de pré-visualização para registar e gerir informações de segurança -** área melhorada, selecione **Selecionado**- e, em seguida, escolher um grupo de utilizadores ou escolher **All** para ligar esta funcionalidade para todos os utilizadores do arrendatário.

4. Na área ***Os utilizadores podem utilizar funcionalidades de pré-visualização para registar e gerir a área de segurança **info**** selecione **Nenhum**.

5. Guarde as suas definições.

    Depois de guardar as suas definições, deixará de ter acesso à antiga experiência de informação de segurança.

>[!Important]
>Se não completar estes passos antes de 25 de setembro de 2019, o seu inquilino do Azure Ative Directory será automaticamente habilitado para a experiência melhorada. Se tiver dúvidas, contacte-nos em registrationpreview@microsoft.com .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Os pedidos de autenticação utilizando logins POST serão mais rigorosamente validados

**Tipo:** Alteração da categoria de serviço de **recurso:** Autenticações (Logins) **Capacidade do produto:** Padrões

A partir de 2 de setembro de 2019, os pedidos de autenticação utilizando o método POST serão validados de forma mais rigorosa em comparação com as normas HTTP. Especificamente, os espaços e as cotações duplas (") deixarão de ser removidos dos valores do formulário de pedido. Não se espera que estas alterações quebrem os clientes existentes, e ajudarão a garantir que os pedidos enviados para a Azure AD são tratados de forma fiável todas as vezes.

Para obter mais informações, consulte os avisos de [alteração de alterações da AD Azure](../develop/reference-breaking-changes.md#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Julho de 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Plano de mudança: Atualização do serviço Proxy de aplicação para suportar apenas TLS 1.2

**Tipo:** Plano de mudança **categoria de serviço:** Capacidade do produto Proxy **app:** Controlo de acesso

Para ajudar a fornecer-lhe a nossa encriptação mais forte, vamos começar a limitar o acesso do serviço Proxy de aplicação a apenas protocolos TLS 1.2. Esta limitação será inicialmente lançada para clientes que já estão a utilizar protocolos TLS 1.2, para que não veja o impacto. A depreciação completa dos protocolos TLS 1.0 e TLS 1.1 estará concluída no dia 31 de agosto de 2019. Os clientes que ainda utilizem TLS 1.0 e TLS 1.1 receberão aviso prévio para se prepararem para esta mudança.

Para manter a ligação ao serviço Application Proxy ao longo desta alteração, recomendamos que certifique-se de que as combinações de servidores de clientes e servidores de navegador estão atualizadas para utilizar o TLS 1.2. Recomendamos também que se certifique de incluir quaisquer sistemas de clientes utilizados pelos seus colaboradores para aceder a aplicações publicadas através do serviço Application Proxy.

Para obter mais informações, consulte [adicionar uma aplicação no local para acesso remoto através do Application Proxy no Azure Ative Directory](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plano de mudança: Estão a chegar atualizações de design para a Galeria de Aplicações

**Tipo:** Plano para mudança **categoria de serviço:** Capacidade do produto de aplicações **empresariais:** SSO

Novas alterações na interface do utilizador estão a chegar ao design do **Add a partir da** área de galeria da lâmina de **aplicação Add.** Estas alterações irão ajudá-lo a encontrar mais facilmente as suas aplicações que suportam o fornecimento automático, OpenID Connect, Security Assertion Markup Language (SAML) e Password single sign-on (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plano de alteração: Remoção do endereço IP do servidor MFA do endereço IP do Office 365

**Tipo:** Plano de alteração **da categoria serviço:** Capacidade do produto MFA: Proteção & de Segurança de Identidade 

Estamos a remover o endereço IP do servidor MFA do [Endereço IP do Office 365 e do serviço Web URL](/office365/enterprise/office-365-ip-web-service). Se atualmente confia nestas páginas para atualizar as definições da sua firewall, tem de se certificar de que também inclui a lista de endereços IP documentados na secção de requisitos de firewall do Servidor de **Autenticação Multi-Factor Azure** da Obtenção iniciada com o artigo do Servidor de [Autenticação Multi-Factor Azure.](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Fichas só para aplicações agora exigem que a app do cliente exista no inquilino de recursos

**Tipo:** **Categoria serviço fixo:** Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

No dia 26 de julho de 2019, mudamos a forma como fornecemos fichas só para aplicações através da concessão de credenciais de [cliente.](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) Anteriormente, as aplicações podiam obter fichas para ligar para outras aplicações, independentemente de a app do cliente estar no inquilino. Atualizámos este comportamento para que os recursos de um único inquilino, às vezes chamados APIs web, só podem ser chamados por aplicações de clientes que existem no inquilino de recursos.

Se a sua aplicação não estiver localizada no arrendatário de recursos, receberá uma mensagem de erro que diz: `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` Para corrigir este problema, deve criar o principal do serviço de aplicações do cliente no arrendatário, utilizando o ponto final de [consentimento administrativo](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) ou através [do PowerShell,](../develop/howto-authenticate-service-principal-powershell.md)o que garante que o seu inquilino deu permissão à app para operar dentro do arrendatário.

Para mais informações, veja [quais as novidades para a autenticação?](../develop/reference-breaking-changes.md#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)

> [!NOTE]
> O consentimento existente entre o cliente e a API continua a não ser exigido. As aplicações ainda devem estar a fazer os seus próprios controlos de autorização.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Novo sinal sem palavra-passe para Azure AD usando chaves de segurança FIDO2

**Tipo:** Nova **categoria de serviço** de funcionalidade: Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

Os clientes AZURE AD podem agora definir políticas para gerir chaves de segurança FIDO2 para os utilizadores e grupos da sua organização. Os utilizadores finais também podem registar as suas chaves de segurança, utilizar as chaves para iniciar sôm nas suas contas microsoft em sites web enquanto em dispositivos capazes de FIDO, bem como iniciar sôs-in nos seus dispositivos AD aderidos ao Azure AD.

Para obter mais informações, consulte [Ativar o login sem palavras-passe para Azure AD (pré-visualização)](../authentication/concept-authentication-passwordless.md) para obter informações relacionadas com o administrador e configurar informações de segurança para utilizar uma chave de [segurança (Pré-visualização)](../user-help/security-info-setup-security-key.md) para obter informações relacionadas com o utilizador final.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - julho 2019

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em julho de 2019, adicionámos estas 18 novas aplicações com apoio da Federação à galeria de aplicações:

[Software Ungerboeck](../saas-apps/ungerboeck-software-tutorial.md), [Bright Pattern Omnichannel Contact Center](../saas-apps/bright-pattern-omnichannel-contact-center-tutorial.md), Clever [Nelly](../saas-apps/clever-nelly-tutorial.md), [AcquireIO](../saas-apps/acquireio-tutorial.md), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](../saas-apps/productboard-tutorial.md), [MS Azure SSO Access for Ethidex Compliance Office &trade; ](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [Hype](../saas-apps/hype-tutorial.md), [Abstract,](../saas-apps/abstract-tutorial.md) [Ascentis,](../saas-apps/ascentis-tutorial.md) [Flipsnack,](https://www.flipsnack.com/accounts/sign-in-sso.html) [Wandera,](../saas-apps/wandera-tutorial.md) [TwineSocial,](https://twinesocial.com/) [Kallidus](../saas-apps/kallidus-tutorial.md), [HyperAnna](../saas-apps/hyperanna-tutorial.md), [PharmID WasteWitness,](https://pharmid.com/) [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](../saas-apps/jfrog-artifactory-tutorial.md)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o fornecimento de conta de utilizador para estas novas aplicações SaaS apoiadas

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto de aplicações **empresariais:** Monitorização & Reporte

Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Diretório federado](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o provisionamento automatizado de conta de utilizador, consulte [automatizar o fornecimento de utilizadores para aplicações SaaS com Azure AD](../app-provisioning/user-provisioning.md)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nova etiqueta de serviço de serviços de domínio Azure AD para o Grupo de Segurança da Rede

**Tipo:** Nova **categoria de serviço de recurso:** Azure AD Domain Services **Capacidade do produto:** Azure AD Domain Services

Se estiver cansado de gerir longas listas de endereços e gamas IP, pode utilizar a nova etiqueta de serviço de rede **AzureActiveDirectorDomainServices** no seu grupo de segurança da rede Azure para ajudar a garantir o tráfego de entrada na sua sub-rede virtual Azure AD Domain Services.

Para obter mais informações sobre esta nova etiqueta de serviço, consulte [Grupos de Segurança de Rede para Serviços de Domínio Azure AD](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Novas auditorias de segurança para serviços de domínio Azure AD (Visualização pública)

**Tipo:** Nova **categoria de serviço de recurso:** Azure AD Domain Services **Capacidade do produto:** Azure AD Domain Services

Temos o prazer de anunciar o lançamento da Azure AD Domain Service Security Auditing para pré-visualização pública. A auditoria de segurança ajuda a fornecer-lhe informações críticas sobre os seus serviços de autenticação, através do streaming de eventos de auditoria de segurança a recursos direcionados, incluindo Azure Storage, Azure Log Analytics e Azure Event Hub, utilizando o portal Azure AD Domain Service.

Para obter mais informações, consulte [Ativar as Auditorias de Segurança dos Serviços de Domínio Azure AD (Pré-visualização)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Novos métodos de autenticação utilização & insights (Visualização pública)

**Tipo:** Nova **categoria de serviço de recurso:** Autosserviço Password Redefinir **capacidade de produto:** Monitorização & Reporte

A utilização dos novos métodos de autenticação & relatórios de insights pode ajudá-lo a entender como funcionalidades como Azure AD Multi-Factor Authentication e self-service password reset estão sendo registadas e usadas na sua organização, incluindo o número de utilizadores registados para cada recurso, quantas vezes a palavra-passe de autosserviço é usada para redefinir palavras-passe, e por que método o reset acontece.

Para obter mais informações, consulte [a utilização dos métodos de autenticação & insights (pré-visualização)](../authentication/howto-authentication-methods-usage-insights.md).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Novos relatórios de segurança estão disponíveis para todos os administradores da AD Azure (Visualização pública)

**Tipo:** Nova **categoria de serviço** de recurso: **Capacidade de proteção de identidade: Proteção** & de Segurança de Identidade

Todos os administradores da AD Azure podem agora selecionar o banner no topo dos relatórios de segurança existentes, como os **Utilizadores sinalizados para** relatório de risco, para começar a usar a nova experiência de segurança, como mostrado nos **utilizadores risky** e **nos relatórios de inscrições de Risco.** Com o tempo, todos os relatórios de segurança passarão das versões mais antigas para as novas versões, com os novos relatórios a fornecerem-lhe as seguintes capacidades adicionais:

- Filtragem e triagem avançadas

- Ações a granel, tais como descartar o risco do utilizador

- Confirmação de entidades comprometidas ou seguras

- Estado de risco, cobrindo: Em risco, dispensado, remediado e confirmado comprometido

Para obter mais informações, consulte [o relatório dos utilizadores arriscados](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users) e [o relatório de inscrições de risco](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Novas auditorias de segurança para serviços de domínio Azure AD (Visualização pública)

**Tipo:** Nova **categoria de serviço de recurso:** Azure AD Domain Services **Capacidade do produto:** Azure AD Domain Services

Temos o prazer de anunciar o lançamento da Azure AD Domain Service Security Auditing para pré-visualização pública. A auditoria de segurança ajuda a fornecer-lhe informações críticas sobre os seus serviços de autenticação, através do streaming de eventos de auditoria de segurança a recursos direcionados, incluindo Azure Storage, Azure Log Analytics e Azure Event Hub, utilizando o portal Azure AD Domain Service.

Para obter mais informações, consulte [Ativar as Auditorias de Segurança dos Serviços de Domínio Azure AD (Pré-visualização)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nova federação direta B2B que utiliza SAML/WS-Fed (Visualização pública)

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto B2B: B2B/B2C 

A federação direta ajuda a facilitar o seu trabalho com parceiros cuja solução de identidade gerida por TI não é a AZure AD, trabalhando com sistemas de identidade que suportam os padrões DE SAML ou WS-Fed. Depois de configurar uma relação direta de federação com um parceiro, qualquer novo utilizador convidado que convidar desse domínio pode colaborar consigo usando a sua conta organizacional existente, tornando a experiência do utilizador para os seus hóspedes mais perfeita.

Para obter mais informações, consulte [a federação direta com AD FS e fornecedores de terceiros para utilizadores convidados (pré-visualização)](../external-identities/direct-federation.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o fornecimento de conta de utilizador para estas novas aplicações SaaS apoiadas

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto de aplicações **empresariais:** Monitorização & Reporte

Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Diretório federado](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte [automatizar o fornecimento de utilizadores para aplicações SaaS com Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nova verificação de nomes de grupo duplicados no portal AD AZure

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do Produto de Gestão de **Grupo:** Colaboração

Agora, quando criar ou atualizar um nome de grupo a partir do portal AD Azure, faremos uma verificação para ver se está a duplicar um nome de grupo existente no seu recurso. Se determinarmos que o nome já está a ser usado por outro grupo, ser-lhe-á pedido que modifique o seu nome.

Para obter mais informações, consulte [Gerir grupos no portal AD AZure](./active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>AZure AD agora suporta parâmetros de consulta estática em resposta (redirecionamento) URIs

**Tipo:** Nova **categoria de serviço** de funcionalidade: Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

As aplicações AD AZure podem agora registar e utilizar URIs de resposta (redirecionamento) com parâmetros de consulta estática (por exemplo, `https://contoso.com/oauth2?idp=microsoft` ) para pedidos de OAuth 2.0. O parâmetro de consulta estática está sujeito a correspondência de cordas para URIs de resposta, assim como qualquer outra parte do URI de resposta. Se não houver uma cadeia registada que corresponda ao reorientar-uri descodificado pelo URL, o pedido é rejeitado. Se o URI de resposta for encontrado, toda a cadeia é utilizada para redirecionar o utilizador, incluindo o parâmetro de consulta estática.

Os URIs de resposta dinâmica continuam a ser proibidos porque representam um risco de segurança e não podem ser utilizados para reter informações do Estado através de um pedido de autenticação. Para o efeito, utilize o `state` parâmetro.

Atualmente, os ecrãs de registo de aplicações do portal Azure ainda bloqueiam parâmetros de consulta. No entanto, pode editar manualmente o manifesto da aplicação para adicionar e testar parâmetros de consulta na sua aplicação. Para mais informações, veja [quais as novidades para a autenticação?](../develop/reference-breaking-changes.md#redirect-uris-can-now-contain-query-string-parameters)

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Os registos de atividade (MS Graph APIs) para Azure AD estão agora disponíveis através de Cmdlets PowerShell

**Tipo:** Nova **categoria de serviço** de recurso: Capacidade do produto de reporte: Monitorização & Reporte 

Estamos entusiasmados por anunciar que os registos de atividades da AZure AD (relatórios de auditoria e login) estão agora disponíveis através do módulo Azure AD PowerShell. Anteriormente, poderias criar os teus próprios scripts usando pontos finais da MS Graph API, e agora alargámos essa capacidade aos cmdlets powerShell.

Para obter mais informações sobre como utilizar estes cmdlets, consulte [os cmdlets Azure AD PowerShell para reportar](../reports-monitoring/reference-powershell-reporting.md).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Controlos de filtro atualizados para registos de auditoria e login no Azure AD

**Tipo:** Categoria de serviço de funcionalidade **alterada:** Capacidade do produto de reporte: Monitorização & Reporte 

Atualizámos os relatórios de registo de auditoria e de início de sessão para que agora possa aplicar vários filtros sem ter de os adicionar como colunas nos ecrãs de relatório. Além disso, pode agora decidir quantos filtros pretende mostrar no ecrã. Estas atualizações funcionam em conjunto para facilitar a leitura dos seus relatórios e mais ajustou-se às suas necessidades.

Para obter mais informações sobre estas atualizações, consulte [os registos de auditoria do filtro](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) e as [atividades de login do filtro](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

---

## <a name="june-2019"></a>Junho de 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Novo riscoDetections API para Microsoft Graph (pré-visualização pública)

**Tipo:** Nova **categoria de serviço** de recurso: **Capacidade de proteção de identidade: Proteção** & de Segurança de Identidade

Temos o prazer de anunciar que a nova API de riscodetecção para o Microsoft Graph está agora em pré-visualização pública. Pode utilizar esta nova API para visualizar uma lista do utilizador relacionado com a Proteção de Identidade da sua organização e deteções de risco de inscrição. Também pode utilizar esta API para consultar de forma mais eficiente as suas deteções de risco, incluindo detalhes sobre o tipo de deteção, estado, nível e muito mais.

Para obter mais informações, consulte a documentação de referência da [API de deteção de risco.](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações AZure AD - junho 2019

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em junho de 2019, adicionámos estas 22 novas aplicações com apoio da Federação à galeria de aplicações:

[Azure AD SAML Toolkit](../saas-apps/saml-toolkit-tutorial.md), [Otsuka Shokai (大塚商会)](../saas-apps/otsuka-shokai-tutorial.md), [ANAQUA](../saas-apps/anaqua-tutorial.md), [Cliente Azure VPN](https://portal.azure.com/), [ExpenseIn](../saas-apps/expensein-tutorial.md), [Helper Helper](../saas-apps/helper-helper-tutorial.md), [Costpoint](../saas-apps/costpoint-tutorial.md), [GlobalOne,](../saas-apps/globalone-tutorial.md) [Mercedes-Benz In-Car Office,](https://me.secure.mercedes-benz.com/) [Skore,](https://app.justskore.it/) [Oracle Cloud Consola de Infraestruturas](../saas-apps/oracle-cloud-tutorial.md), [Autenticação CYBERArk SAML](../saas-apps/cyberark-saml-authentication-tutorial.md), [Scrible Edu,](https://www.scrible.com/sign-in/#/create-account) [PandaDoc,](../saas-apps/pandadoc-tutorial.md) [Perceptyx,](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial) [Proptimise OS](https://proptimise.co.uk/), [Vtiger CRM (SAML)](../saas-apps/vtiger-crm-saml-tutorial.md), Oracle Access Manager para o Oracle Retail Merchandising, Oracle Access Manager para Oracle E-Business Suite, Oracle IDCS para E-Business Suite, Oracle IDCS para PeopleSoft, Oracle IDCS para JD Edwards

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o fornecimento de conta de utilizador para estas novas aplicações SaaS apoiadas

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto de aplicações **empresariais:** Monitorização & Reporte

Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:

- [Zoom](../saas-apps/zoom-provisioning-tutorial.md)

- [Envoy](../saas-apps/envoy-provisioning-tutorial.md)

- [Proxyclick](../saas-apps/proxyclick-provisioning-tutorial.md)

- [4me](../saas-apps/4me-provisioning-tutorial.md)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o provisionamento automatizado de conta de utilizador, consulte [automatizar o fornecimento de utilizadores para aplicações SaaS com Azure AD](../app-provisioning/user-provisioning.md)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Ver o progresso em tempo real do serviço de prestação de AD Azure

**Tipo:** Categoria de serviço de funcionalidade **alterada:** Capacidade de Fornecimento de **Aplicativos: Gestão** do ciclo de vida da identidade

Atualizámos a experiência de provisionamento da AD Azure para incluir uma nova barra de progresso que mostra o quão longe está no processo de fornecimento de utilizadores. Esta experiência atualizada também fornece informações sobre o número de utilizadores a provisionados durante o ciclo atual, bem como quantos utilizadores foram a provisionados até à data.

Para obter mais informações, [consulte verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Marca da empresa agora aparece em ecrãs de sinalização e erro

**Tipo:** Alteração **da categoria de serviço** de funcionalidade: Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

Atualizámos o Azure AD para que a marca da sua empresa apareça agora nos ecrãs de sinalização e erro, bem como na página de inscrição. Não é preciso fazer nada para ligar esta funcionalidade, a Azure AD simplesmente utiliza os ativos que já criou na área de **marca da Empresa** do portal Azure.

Para obter mais informações sobre a configuração da marca da sua empresa, consulte [adicionar a marca às páginas do Azure Ative Directory da sua organização.](./customize-branding.md)

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>O Servidor de Autenticação Multi-Factor (MFA) do Azure já não está disponível para novas implementações

**Tipo:** Categoria de **Serviço** Precotado: Capacidade do Produto **MFA:** Proteção & de Segurança de Identidade

A partir de 1 de julho de 2019, a Microsoft deixará de oferecer o MFA Server para novas implementações. Os novos clientes que pretendam necessitar de autenticação multi-factor na sua organização devem agora utilizar a autenticação multi-factor Azure AD baseada na nuvem. Os clientes que ativaram o MFA Server antes de 1 de julho não verão uma alteração. Ainda poderá descarregar a versão mais recente, obter futuras atualizações e gerar credenciais de ativação.

Para obter mais informações, consulte [Começar com o Azure Multi-Factor Authentication Server](../authentication/howto-mfaserver-deploy.md). Para obter mais informações sobre a autenticação multi-factor Azure AD baseada na nuvem, consulte [o Planeamento de uma implementação de autenticação multi-factor Azure AD baseada na nuvem](../authentication/howto-mfa-getstarted.md).

---

## <a name="may-2019"></a>Maio de 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Alteração de serviço: Suporte futuro para apenas protocolos TLS 1.2 no serviço Application Proxy

**Tipo:** Plano de mudança **categoria de serviço:** Capacidade do produto Proxy **app:** Controlo de acesso

Para ajudar a fornecer encriptação de melhor classe para os nossos clientes, estamos limitando o acesso a apenas protocolos TLS 1.2 no serviço Application Proxy. Esta mudança está a ser gradualmente lançada para clientes que já estão apenas a usar protocolos TLS 1.2, pelo que não deverá ver alterações.

A depreciação dos TLS 1.0 e TLS 1.1 acontece no dia 31 de agosto de 2019, mas forneceremos um aviso avançado adicional, pelo que terá tempo para se preparar para esta mudança. Para se preparar para esta alteração, certifique-se de que as combinações do seu servidor de clientes e servidor de navegador, incluindo quaisquer clientes que os seus utilizadores utilizem para aceder a aplicações publicadas através do Application Proxy, sejam atualizadas para utilizar o protocolo TLS 1.2 para manter a ligação ao serviço Application Proxy. Para obter mais informações, consulte [adicionar uma aplicação no local para acesso remoto através do Application Proxy no Azure Ative Directory](../manage-apps/application-proxy-add-on-premises-application.md#prerequisites).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Utilize o relatório de utilização e insights para visualizar os seus dados de inscrição relacionados com a aplicação

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto de aplicações **empresariais:** Monitorização & Reporte

Pode agora utilizar o relatório de utilização e insights, localizado na área de **aplicações** da Enterprise do portal Azure, para obter uma visão centrada na aplicação dos seus dados de login, incluindo informações sobre:

- Aplicativos mais utilizados para a sua organização

- Aplicativos com os insus máximos de inscrições

- Principais erros de inscrição para cada app

Para obter mais informações sobre esta funcionalidade, consulte [o relatório de utilização e insights no portal Azure Ative Directory](../reports-monitoring/concept-usage-insights-report.md)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatize o fornecimento do seu utilizador para aplicações na nuvem usando Azure AD

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto de aplicações **empresariais:** Monitorização & Reporte

Siga estes novos tutoriais para utilizar o Serviço de Provisionamento AZURE AD para automatizar a criação, eliminação e atualização das contas dos utilizadores para as seguintes aplicações baseadas na nuvem:

- [Comete](../saas-apps/comeet-recruiting-software-provisioning-tutorial.md)

- [Dinâmicosignal](../saas-apps/dynamic-signal-provisioning-tutorial.md)

- [KeeperSecurity](../saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial.md)

Também pode seguir este novo [tutorial dropbox,](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)que fornece informações sobre como fornecer objetos de grupo.

Para obter mais informações sobre como proteger melhor a sua organização através do provisionamento automatizado de conta de utilizador, consulte [o fornecimento de utilizadores do Automatismo às aplicações do SaaS com Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>A pontuação de segurança de identidade já está disponível em Azure AD (disponibilidade geral)

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto N/A: Proteção & de Segurança de Identidade 

Agora pode monitorizar e melhorar a sua postura de segurança de identidade utilizando a funcionalidade de pontuação de segurança de identidade em Azure AD. A funcionalidade de pontuação de segurança de identidade utiliza um único dashboard para o ajudar:

- Meje a sua postura de segurança de identidade, com base numa pontuação entre 1 e 223.

- Plano para as melhorias na sua segurança de identidade

- Reveja o sucesso das suas melhorias de segurança

Para obter mais informações sobre a funcionalidade de pontuação de segurança de identidade, veja [qual é a pontuação de identidade segura no Diretório Ativo Azure?](./identity-secure-score.md)

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>A experiência de inscrições de novas aplicações já está disponível (disponibilidade geral)

**Tipo:** Nova **categoria de serviço de recurso:** Autenticações (Logins) **Capacidade de produto:** Experiência de Desenvolvimento

A nova experiência [de registos de aplicações](https://aka.ms/appregistrations) está agora disponível em geral. Esta nova experiência inclui todas as funcionalidades-chave que conhece do portal Azure e do portal de Registo de Aplicações e melhora-as através de:

- **Melhor gestão de aplicativos.** Em vez de ver as suas apps através de diferentes portais, agora pode ver todas as suas aplicações num só local.

- **Registo de aplicações simplificado.** Desde a melhor experiência de navegação até à renovada experiência de seleção de permissões, agora é mais fácil registar e gerir as suas apps.

- **Informação mais detalhada.** Pode encontrar mais detalhes sobre a sua aplicação, incluindo guias de arranque rápido e muito mais.

Para mais informações, consulte a [plataforma de identidade](../develop/index.yml) da Microsoft e a experiência de registos da App já está [disponível em geral!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) anúncio de blog.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Novas capacidades disponíveis na API dos Utilizadores Arriscados para Proteção de Identidade

**Tipo:** Nova **categoria de serviço** de recurso: **Capacidade de proteção de identidade: Proteção** & de Segurança de Identidade

Temos o prazer de anunciar que agora pode utilizar a API dos Utilizadores Arriscados para recuperar o histórico de riscos dos utilizadores, dispensar utilizadores de risco e confirmar que os utilizadores estão comprometidos. Esta alteração ajuda-o a atualizar de forma mais eficiente o estado de risco dos seus utilizadores e a compreender o seu histórico de riscos.

Para obter mais informações, consulte a documentação de referência da [API dos Utilizadores Arriscados.](/graph/api/resources/riskyuser?view=graph-rest-beta&preserve-view=true)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações AZure AD - maio 2019

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em maio de 2019, adicionámos estas 21 novas aplicações com apoio da Federação à galeria de aplicações:

[Freedcamp](../saas-apps/freedcamp-tutorial.md), [Real Links](../saas-apps/real-links-tutorial.md), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](../saas-apps/simple-sign-tutorial.md), [Braze](../saas-apps/braze-tutorial.md), [Displayr](../saas-apps/displayr-tutorial.md), [Templafy,](../saas-apps/templafy-tutorial.md) [Marketo Sales Engage,](https://toutapp.com/login) [ACLP,](../saas-apps/aclp-tutorial.md) [OutSystems](../saas-apps/outsystems-tutorial.md), [Meta4 Global HR](../saas-apps/meta4-global-hr-tutorial.md), Quantum [Workplace](../saas-apps/quantum-workplace-tutorial.md), [Cobalt,](../saas-apps/cobalt-tutorial.md) [webMethods API Cloud,](../saas-apps/webmethods-integration-cloud-tutorial.md) [RedFlag](https://pocketstop.com/redflag/), [Whatfix,](../saas-apps/whatfix-tutorial.md) [Control,](../saas-apps/control-tutorial.md) [JOBHUB,](../saas-apps/jobhub-tutorial.md) [NEOGOV,](../saas-apps/neogov-tutorial.md) [Foodee,](../saas-apps/foodee-tutorial.md) [MyVR](../saas-apps/myvr-tutorial.md)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Melhores experiências de criação e gestão de grupos no portal AD AZure

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do Produto de Gestão de **Grupo:** Colaboração

Fizemos melhorias nas experiências relacionadas com grupos no portal AD AZure. Estas melhorias permitem aos administradores gerir melhor as listas de grupos, listas de membros e fornecer opções adicionais de criação.

As melhorias incluem:

- Filtragem básica por tipo de associação e tipo de grupo.

- Adição de novas colunas, como o endereço de Origem e E-mail.

- Capacidade de grupos multi-selecionados, membros e listas de proprietários para facilitar a eliminação.

- Capacidade de escolher um endereço de e-mail e adicionar proprietários durante a criação do grupo.

Para obter mais informações, consulte [Criar um grupo básico e adicionar membros utilizando o Azure Ative Directory](./active-directory-groups-create-azure-portal.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Configure uma política de nomeação para o Office 365 grupos no portal AD Azure (disponibilidade geral)

**Tipo:** Categoria **de serviço** de funcionalidade alterada: Capacidade do produto de gestão **de grupo:** colaboração

Os administradores podem agora configurar uma política de nomeação para grupos office 365, utilizando o portal AD AZure. Esta alteração ajuda a impor convenções consistentes de nomeação para grupos do Office 365 criados ou editados por utilizadores na sua organização.

Pode configurar a política de nomeação para grupos do Office 365 de duas maneiras diferentes:

- Defina prefixos ou sufixos, que são automaticamente adicionados a um nome de grupo.

- Faça o upload de um conjunto personalizado de palavras bloqueadas para a sua organização, que não são permitidas em nomes de grupo (por exemplo, "CEO, Payroll, HR").

Para obter mais informações, consulte [Impor uma Política de Nomeação para o Office 365 grupos](../enterprise-users/groups-naming-policy.md).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Os pontos finais da Microsoft Graph API estão agora disponíveis para registos de atividades AD Azure (disponibilidade geral)

**Tipo:** Categoria de serviço de funcionalidade **alterada:** Capacidade do produto de reporte: Monitorização & Reporte 

Temos o prazer de anunciar a disponibilidade geral do suporte de pontos finais da Microsoft Graph API para registos de atividades AZure AD. Com esta versão, pode agora utilizar a Versão 1.0 dos registos de auditoria AZure AD, bem como os apIs de login.

Para obter mais informações, consulte [a visão geral do registo de auditoria da AZure AD](/graph/api/resources/azure-ad-auditlog-overview).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Os administradores podem agora utilizar o Acesso Condicional para o processo de registo combinado (visualização pública)

**Tipo:** Nova **categoria de serviço** de recurso: Capacidade de acesso condicional **do produto:** Proteção & de Segurança de Identidade

Os administradores podem agora criar políticas de Acesso Condicional para utilização pela página de registo combinado. Isto inclui a aplicação de políticas para permitir o registo se:

- Os utilizadores estão numa rede de confiança.

- Os utilizadores são um baixo risco de inscrição.

- Os utilizadores estão num dispositivo gerido.

- Os utilizadores concordam com os termos de uso da organização (TOU).

Para obter mais informações sobre o Acesso Condicional e o reset da palavra-passe, pode ver o [Acesso Condicional para o MFA combinado Azure e o post de blog de experiência de registo de redefinição de palavra-passe.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348) Para obter mais informações sobre as políticas de acesso condicional para o processo de registo combinado, consulte [as políticas de Acesso Condicional para registo combinado.](../authentication/howto-registration-mfa-sspr-combined.md#conditional-access-policies-for-combined-registration) Para obter mais informações sobre a funcionalidade de utilização dos termos de utilização da Azure AD, consulte [a funcionalidade de utilização do Azure Ative Directory](../conditional-access/terms-of-use.md).

---

## <a name="april-2019"></a>Abril de 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>A nova deteção de informações de ameaças da AZure está agora disponível como parte da Proteção de Identidade AZure AD

**Tipo:** Nova **categoria de serviço:** Azure AD Identity Protection **Product Capability:** Identity Security & Protection

A deteção de informações de ameaça a Azure AD está agora disponível como parte da funcionalidade atualizada de Proteção de Identidade Azure AD. Esta nova funcionalidade ajuda a indicar uma atividade incomum de utilizadores para um utilizador ou atividade específica que é consistente com padrões de ataque conhecidos com base nas fontes internas e externas de inteligência da Microsoft.

Para obter mais informações sobre a versão renovada da Azure AD Identity Protection, consulte as [quatro principais melhorias da Proteção de Identidade Azure AD estão agora no blog de pré-visualização pública](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) e no [What is Azure Ative Directory Identity Protection (refrescado)?](../identity-protection/overview-identity-protection.md) . Para obter mais informações sobre a deteção de informações sobre ameaças Azure AD, consulte o artigo [de deteção de risco de proteção de identidade ativa do Azure Ative.](../identity-protection/concept-identity-protection-risks.md)

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>A gestão de direitos AD AZure já está disponível (visualização pública)

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto de governação **de identidade:** Governação da Identidade

A gestão de direitos da AD AZure, agora em pré-visualização pública, ajuda os clientes a delegar a gestão dos pacotes de acesso, que define como os colaboradores e parceiros de negócio podem solicitar o acesso, quem deve aprovar e quanto tempo têm acesso. Os pacotes de acesso podem gerir a adesão em grupos AZure AD e Office 365, atribuições de funções em aplicações empresariais e atribuições de funções para sites SharePoint Online. Leia mais sobre a gestão de direitos na [visão geral da gestão de direitos da AD Azure.](../governance/entitlement-management-overview.md) Para saber mais sobre a amplitude das funcionalidades de Governança de Identidade Azure AD, incluindo Gestão de Identidade Privilegiada, comentários de acesso e termos de utilização, veja [o que é Azure AD Identity Governance?](../governance/identity-governance-overview.md)

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configure uma política de nomeação para o Office 365 grupos no portal AD Azure (visualização pública)

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do Produto de Gestão de **Grupo:** Colaboração

Os administradores podem agora configurar uma política de nomeação para grupos office 365, utilizando o portal AD AZure. Esta alteração ajuda a impor convenções consistentes de nomeação para grupos do Office 365 criados ou editados por utilizadores na sua organização.

Pode configurar a política de nomeação para grupos do Office 365 de duas maneiras diferentes:

- Defina prefixos ou sufixos, que são automaticamente adicionados a um nome de grupo.

- Faça o upload de um conjunto personalizado de palavras bloqueadas para a sua organização, que não são permitidas em nomes de grupo (por exemplo, "CEO, Payroll, HR").

Para obter mais informações, consulte [Impor uma Política de Nomeação para o Office 365 grupos](../enterprise-users/groups-naming-policy.md).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Os registos de atividade azure AD estão agora disponíveis no Azure Monitor (disponibilidade geral)

**Tipo:** Nova **categoria de serviço** de recurso: Capacidade do produto de reporte: Monitorização & Reporte 

Para ajudar a resolver o seu feedback sobre visualizações com os registos de Atividade AD Azure, estamos a introduzir uma nova funcionalidade de Insights no Log Analytics. Esta funcionalidade ajuda-o a obter informações sobre os seus recursos Azure AD utilizando os nossos modelos interativos, chamados Workbooks. Estes livros de trabalho pré-construídos podem fornecer detalhes para apps ou utilizadores, e incluem:

- **Inscrições.** Fornece detalhes para apps e utilizadores, incluindo a localização de login, o sistema operativo em uso ou cliente e versão do navegador, e o número de logins bem sucedidos ou falhados.

- **Autenticação do legado e Acesso Condicional.** Fornece detalhes para apps e utilizadores que usam a autenticação antiga, incluindo o uso de autenticação multi-factor desencadeado por políticas de Acesso Condicional, apps que usam políticas de Acesso Condicional, e assim por diante.

- **Análise de falha de saúde.** Ajuda-o a determinar se os seus erros de inscrição estão a ocorrer devido a uma ação do utilizador, problemas de política ou à sua infraestrutura.

- **Relatórios personalizados.** Pode criar novos livros de trabalho ou editar livros de trabalho existentes para ajudar a personalizar a funcionalidade Insights para a sua organização.

Para obter mais informações, consulte [Como utilizar os livros do Azure Monitor para relatórios do Azure Ative Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações AZure AD - abril 2019

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em abril de 2019, adicionámos estas 21 novas aplicações com apoio da Federação à galeria de aplicações:

[SAP Fiori](../saas-apps/sap-fiori-tutorial.md), [HRworks Single Sign-On,](../saas-apps/hrworks-single-sign-on-tutorial.md) [Percolate,](../saas-apps/percolate-tutorial.md) [MobiControl,](../saas-apps/mobicontrol-tutorial.md) [Citrix NetScaler,](../saas-apps/citrix-netscaler-tutorial.md) [Shibumi,](../saas-apps/shibumi-tutorial.md) [Benchling,](../saas-apps/benchling-tutorial.md) [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](../saas-apps/pagedna-tutorial.md), [EduBrite LMS,](../saas-apps/edubrite-lms-tutorial.md) [RStudio Connect,](../saas-apps/rstudio-connect-tutorial.md) [AMMS,](../saas-apps/amms-tutorial.md) [Mitel Connect,](../saas-apps/mitel-connect-tutorial.md)Alibaba Cloud [(SSO baseado em funções)](../saas-apps/alibaba-cloud-service-role-based-sso-tutorial.md), [](https://indiggolead.com/) [Certent Equity Management](../saas-apps/certent-equity-management-tutorial.md), [Sectigo Certificate Manager](../saas-apps/sectigo-certificate-manager-tutorial.md), [GreenOrbit](../saas-apps/greenorbit-tutorial.md), [Workgrid](../saas-apps/workgrid-tutorial.md), [monday.com](../saas-apps/mondaycom-tutorial.md), [SurveyMon](../saas-apps/surveymonkey-enterprise-tutorial.md)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nova opção de frequência de comentários de acesso e seleção de funções múltiplas

**Tipo:** Nova **categoria de serviço de recurso:** Acesso Revê **capacidade de produto:** Governação de Identidade

As novas atualizações nas avaliações de acesso a AD do Azure permitem-lhe:

- Altere a frequência dos seus comentários de acesso para **semeseando,** para além das opções anteriormente existentes de semanal, mensal, trimestral e anualmente.

- Selecione várias funções de recursos Azure AD e Azure ao criar uma única revisão de acesso. Nesta situação, todas as funções são criadas com as mesmas definições e todos os revisores são notificados ao mesmo tempo.

Para obter mais informações sobre como criar uma revisão de acesso, consulte [Criar uma revisão de acesso de grupos ou aplicações em avaliações de acesso Azure AD](../governance/create-access-review.md).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>O sistema de alerta de e-mail Azure AD Connect está em transição, enviando novas informações de remetente de e-mail para alguns clientes

**Tipo:** Alteração da **categoria de serviço de funcionalidade:** Capacidade de produto AD **Sync:** Plataforma

O Azure AD Connect está em processo de transição do nosso(s) sistema de alerta de e-mail, potencialmente mostrando a alguns clientes um novo remetente de e-mail. Para resolver este problema, tem de adicionar `azure-noreply@microsoft.com` à lista de autorizações da sua organização ou não poderá continuar a receber alertas importantes do seu Office 365, Azure ou dos seus serviços Sync.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>As alterações de sufixo da UPN são agora bem sucedidas entre domínios federados no Azure AD Connect

**Tipo:** Categoria **serviço fixo:** **Capacidade de produto** AD Sync: Plataforma

Pode agora alterar com sucesso o sufixo UPN de um utilizador de um domínio federado para outro domínio Federado em Azure AD Connect. Esta correção significa que não deve continuar a experimentar a mensagem de erro FederatedDomainChangeError durante o ciclo de sincronização ou receber um e-mail de notificação indicando: "Não é possível atualizar este objeto no Diretório Ativo Azure, porque o atributo [FederatedUser.UserPrincipalName], não é válido. Atualize o valor nos seus serviços de diretório local".

Para obter mais informações, consulte [erros de resolução de problemas durante a sincronização](../hybrid/tshoot-connect-sync-errors.md#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Maior segurança utilizando a política de acesso condicional baseada na aplicação em Azure AD (pré-visualização pública)

**Tipo:** Nova **categoria de serviço** de recurso: Capacidade de acesso condicional **do produto:** Proteção & de Segurança de Identidade

O Acesso Condicional baseado na proteção de aplicações está agora disponível utilizando a política de proteção de **aplicações Require.** Esta nova política ajuda a aumentar a segurança da sua organização, ajudando a prevenir:

- Utilizadores que tenham acesso a apps sem licença Microsoft Intune.

- Os utilizadores não conseguem obter uma política de proteção de aplicações microsoft Intune.

- Os utilizadores têm acesso a apps sem uma política de proteção de aplicações configurada do Microsoft Intune.

Para obter mais informações, consulte [Como Exigir a política de proteção de aplicações para acesso a aplicações na nuvem com acesso condicional.](../conditional-access/app-protection-based-conditional-access.md)

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Novo suporte para Azure AD único sign-on e acesso condicional no Microsoft Edge (pré-visualização pública)

**Tipo:** Nova **categoria de serviço** de recurso: Capacidade de acesso condicional **do produto:** Proteção & de Segurança de Identidade

Melhorámos o nosso suporte Azure AD para o Microsoft Edge, incluindo o fornecimento de um novo suporte para o Azure AD single sign-on e Acesso Condicional. Se já utilizou o Microsoft Intune Managed Browser, pode agora utilizar o Microsoft Edge.

Para obter mais informações sobre a configuração e gestão dos seus dispositivos e aplicações utilizando o Acesso Condicional, consulte [Dispositivos geridos para acesso a aplicações na nuvem com acesso condicional](../conditional-access/require-managed-devices.md) e exija [aplicações de clientes aprovadas para acesso a aplicações na nuvem com Acesso Condicional.](../conditional-access/app-based-conditional-access.md) Para obter mais informações sobre como gerir o acesso utilizando as políticas do Microsoft Edge com as políticas do Microsoft Intune, consulte gerir o acesso à [Internet utilizando um navegador protegido por políticas do Microsoft Intune.](/intune/app-configuration-managed-browser)

---

## <a name="march-2019"></a>Março de 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>O Quadro de Experiência de Identidade e o suporte de políticas personalizadas no Azure Ative Directory B2C já estão disponíveis (GA)

**Tipo:** Nova **categoria de serviço de recurso:** B2C - Capacidade de Produto de Gestão de Identidade do **Consumidor:** B2B/B2C

Pode agora criar políticas personalizadas em Azure AD B2C, incluindo as seguintes tarefas, que são suportadas em escala e sob o nosso Azure SLA:

- Crie e carrema as viagens personalizadas do utilizador da autenticação utilizando políticas personalizadas.

- Descreva as viagens dos utilizadores passo a passo como trocas entre fornecedores de sinistros.

- Defina a ramificação condicional nas viagens do utilizador.

- Transforme e mapeia pedidos para uso em decisões e comunicações em tempo real.

- Utilize serviços ativados pela API REST nas suas viagens personalizadas de utilizador de autenticação. Por exemplo, com fornecedores de e-mail, CRMs e sistemas de autorização proprietários.

- Federate com fornecedores de identidade que estejam em conformidade com o protocolo OpenIDConnect. Por exemplo, com a Azure AD multi-arrendatário, os prestadores de contas sociais ou os fornecedores de verificação de dois fatores.

Para obter mais informações sobre a criação de políticas personalizadas, consulte [notas do Developer para políticas personalizadas no Azure Ative Directory B2C](../../active-directory-b2c/custom-policy-developer-notes.md) e leia o post de alex Simon no [blog, incluindo estudos de caso.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações AZure AD - março 2019

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em março de 2019, adicionámos estas 14 novas aplicações com apoio da Federação à galeria de aplicações:

[Delegado de Intercâmbio Móvel ISEC7](https://www.isec7.com/english/), [MediusFlow,](https://office365.cloudapp.mediusflow.com/) [ePlatform,](../saas-apps/eplatform-tutorial.md) [Fulcrum,](../saas-apps/fulcrum-tutorial.md) [ExcelityGlobal,](../saas-apps/excelityglobal-tutorial.md) [Sistema de Auditoria Baseado em Explicações,](../saas-apps/explanation-based-auditing-system-tutorial.md) [Lean,](../saas-apps/lean-tutorial.md) [Powerschool Performance Matters](../saas-apps/powerschool-performance-matters-tutorial.md), [Cinode,](https://cinode.com/) [Iris Intranet,](../saas-apps/iris-intranet-tutorial.md) [Empactis,](../saas-apps/empactis-tutorial.md) [SmartDraw,](../saas-apps/smartdraw-tutorial.md) [Confirmit Horizons,](../saas-apps/confirmit-horizons-tutorial.md) [TAS](../saas-apps/tas-tutorial.md)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Novos conectores de provisão Zscaler e Atlassian na galeria AZure AD - março 2019

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto de fornecimento de **aplicativos: Integração** de terceiros

Automatizar a criação, atualização e eliminação das contas dos utilizadores para as seguintes aplicações:

[Zscaler,](../saas-apps/zscaler-provisioning-tutorial.md) [Zscaler Beta,](../saas-apps/zscaler-beta-provisioning-tutorial.md) [Zscaler One,](../saas-apps/zscaler-one-provisioning-tutorial.md) [Zscaler Two,](../saas-apps/zscaler-two-provisioning-tutorial.md) [Zscaler Three,](../saas-apps/zscaler-three-provisioning-tutorial.md) [Zscaler ZSCloud,](../saas-apps/zscaler-zscloud-provisioning-tutorial.md) [Atlassian Cloud](../saas-apps/atlassian-cloud-provisioning-tutorial.md)

Para obter mais informações sobre como proteger melhor a sua organização através do provisionamento automatizado de conta de utilizador, consulte [o fornecimento de utilizadores do Automatismo às aplicações do SaaS com Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Restaurar e gerir os seus grupos eliminados do Office 365 no portal AD AZure

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do Produto de Gestão de **Grupo:** Colaboração

Agora pode ver e gerir os seus grupos eliminados do Office 365 a partir do portal AD AZure. Esta alteração ajuda-o a ver quais grupos estão disponíveis para restaurar, juntamente com o facto de lhe permitir eliminar permanentemente quaisquer grupos que não sejam necessários pela sua organização.

Para obter mais informações, consulte [Os grupos Restore expirados ou eliminados.](../enterprise-users/groups-restore-deleted.md#view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore)

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>O único sinal de saúde está agora disponível para aplicações seguras em Azure AD SAML no local através do Application Proxy (pré-visualização pública)

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade de produto Proxy **app:** Controle de Acesso

Agora pode fornecer uma única experiência de início de s-on (SSO) para aplicações no local, aplicações autenticadas por SAML, juntamente com acesso remoto a estas aplicações através do Application Proxy. Para obter mais informações sobre como configurar o SSO SAML com as suas aplicações no local, consulte [o único sign-on da SAML para aplicações no local com Proxy de aplicação (Preview)](../manage-apps/application-proxy-configure-single-sign-on-on-premises-apps.md).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>As aplicações do cliente nos ciclos de pedido serão interrompidas para melhorar a fiabilidade e a experiência do utilizador

**Tipo:** Nova **categoria de serviço** de funcionalidade: Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

As aplicações do cliente podem emitir incorretamente centenas dos mesmos pedidos de login durante um curto período de tempo. Estes pedidos, sejam bem sucedidos ou não, contribuem para uma má experiência do utilizador e para o aumento da carga de trabalho para o IDP, aumentando a latência para todos os utilizadores e reduzindo a disponibilidade do IDP.

Esta atualização envia um `invalid_grant` erro: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` para as aplicações do cliente que emitem pedidos duplicados várias vezes durante um curto período de tempo, além do âmbito de funcionamento normal. As aplicações do cliente que encontrarem este problema devem mostrar uma solicitação interativa, exigindo que o utilizador volte a iniciar sposição. Para obter mais informações sobre esta alteração e sobre como corrigir a sua app se encontrar este erro, veja [quais as novidades para autenticação?](../develop/reference-breaking-changes.md#looping-clients-will-be-interrupted)

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nova experiência de utilizador de Registos de Auditoria já disponível

**Tipo:** Categoria de serviço de funcionalidade **alterada:** Capacidade do produto de reporte: Monitorização & Reporte 

Criámos uma nova página de **registos de auditoria** Azure para ajudar a melhorar a legibilidade e a forma como procura as suas informações. Para ver a nova página **de registos de auditoria,** selecione **registos** de auditoria na secção **Atividade** do Azure AD.

![Página de registos de nova auditoria, com informações de amostra](media/whats-new/audit-logs-page.png)

Para obter mais informações sobre a nova página **de registos de auditoria,** consulte [os relatórios de atividades de Auditoria no portal Azure Ative Directory](../reports-monitoring/concept-audit-logs.md#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Novos avisos e orientações para ajudar a prevenir o bloqueio acidental do administrador de políticas de acesso condicional mal configuradas

**Tipo:** Alteração **da categoria de serviço de recurso:** Capacidade de acesso condicional **do produto:** Proteção & de segurança de identidade

Para ajudar a evitar que os administradores se tranquem acidentalmente fora dos seus próprios inquilinos através de políticas de acesso condicional mal configuradas, criámos novos avisos e orientações atualizadas no portal Azure. Para obter mais informações sobre a nova orientação, consulte [quais são as dependências de serviços no Azure Ative Directory Conditional Access](../conditional-access/service-dependencies.md).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Melhores termos de utilização dos utilizadores finais em dispositivos móveis

**Tipo:** Alteração **da categoria de serviço** de recurso: Termos de utilização **Capacidade do produto:** Governação

Atualizámos os nossos termos de utilização existentes para ajudar a melhorar a forma como revê e consentir os termos de utilização num dispositivo móvel. Agora pode fazer zoom para dentro e para fora, voltar, baixar as informações e selecionar hiperligações. Para obter mais informações sobre os termos de utilização atualizados, consulte [a funcionalidade de utilização do Azure Ative Directory](../conditional-access/terms-of-use.md#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nova Azure AD Activity regista experiência de descarregamento disponível

**Tipo:** Categoria de serviço de funcionalidade **alterada:** Capacidade do produto de reporte: Monitorização & Reporte 

Agora você pode baixar grandes quantidades de registos de atividade diretamente a partir do portal Azure. Esta atualização permite-lhe:

- Faça o download até 250.000 filas.

- Ser notificado após o download completo.

- Personalize o nome do seu ficheiro.

- Determine o seu formato de saída, ou JSON ou CSV.

Para obter mais informações sobre esta funcionalidade, consulte [Quickstart: Descarregue um relatório de auditoria utilizando o portal Azure](../reports-monitoring/quickstart-download-audit-report.md)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Alteração de rutura: Atualizações para condicionar a avaliação por Exchange ActiveSync (EAS)

**Tipo:** Plano de alteração **da categoria de serviço:** Capacidade do produto de acesso **condicional:** Controlo de acesso

Estamos em processo de atualização de como o Exchange ActiveSync (EAS) avalia as seguintes condições:

- Localização do utilizador, com base no país, região ou endereço IP

- Risco de início de sessão

- Plataforma de dispositivo

Se já utilizou estas condições nas suas políticas de Acesso Condicional, esteja ciente de que o comportamento da condição pode mudar. Por exemplo, se usou previamente a condição de localização do utilizador numa apólice, poderá encontrar a política agora a ser ignorada com base na localização do seu utilizador.

---

## <a name="february-2019"></a>Fevereiro de 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Encriptação de fichas AZURE AD SAML (visualização pública)

**Tipo:** Nova **categoria de funcionalidades:** Capacidade do produto de aplicações **empresariais:** SSO

Agora pode configurar qualquer aplicação SAML suportada para receber fichas SAML encriptadas. Quando configurado e utilizado com uma aplicação, a Azure AD encripta as afirmações SAML emitidas utilizando uma chave pública obtida a partir de um certificado armazenado em Azure AD.

Para obter mais informações sobre a configuração da encriptação do token SAML, consulte [a encriptação do token Configure AD SAML](../manage-apps/howto-saml-token-encryption.md).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Crie uma revisão de acesso para grupos ou apps que utilizem avaliações de acesso a Ad Azure

**Tipo:** Nova **categoria de serviço de recurso:** Acesso Revê **capacidade de produto:** Governação

Agora pode incluir vários grupos ou aplicativos numa única revisão de acesso AD Azure para membros do grupo ou atribuição de aplicativos. As avaliações de acesso com vários grupos ou aplicações são configuradas usando as mesmas definições e todos os revisores incluídos são notificados ao mesmo tempo.

Para obter mais informações sobre como criar uma revisão de acesso utilizando avaliações de acesso a Azure, consulte [Criar uma revisão de acesso de grupos ou aplicações em Azure AD Access Reviews](../governance/create-access-review.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações AZure AD - fevereiro 2019

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em fevereiro de 2019, adicionámos estas 27 novas aplicações com apoio da Federação à galeria de aplicações:

[Euromonitor Passport](../saas-apps/euromonitor-passport-tutorial.md), [MindTickle](../saas-apps/mindtickle-tutorial.md), [FAT FINGER,](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7) [AirStack,](../saas-apps/airstack-tutorial.md) [Oracle Fusion ERP,](../saas-apps/oracle-fusion-erp-tutorial.md) [IDrive,](../saas-apps/idrive-tutorial.md) [Skyward Qmlativ,](../saas-apps/skyward-qmlativ-tutorial.md) [Brightidea](../saas-apps/brightidea-tutorial.md), [AlertOps](../saas-apps/alertops-tutorial.md), [Soloinsight-CloudGate SSO,](../saas-apps/soloinsight-cloudgate-sso-tutorial.md)Permission Click, [Brandfolder](../saas-apps/brandfolder-tutorial.md), [StoregateSmartFile,](../saas-apps/smartfile-tutorial.md) [Pexip,](../saas-apps/pexip-tutorial.md) [Stormboard](../saas-apps/stormboard-tutorial.md), [Sísmico,](../saas-apps/seismic-tutorial.md) [Partilhar Um Sonho,](https://www.shareadream.org/how-it-works) [Bugsnag,](../saas-apps/bugsnag-tutorial.md) [WebMethods Integration Cloud,](../saas-apps/webmethods-integration-cloud-tutorial.md) [Knowledge Anywhere LMS](../saas-apps/knowledge-anywhere-lms-tutorial.md), [OU Campus,](../saas-apps/ou-campus-tutorial.md) [Periscope Data,](../saas-apps/periscope-data-tutorial.md) [Netop Portal,](../saas-apps/netop-portal-tutorial.md) [smartvid.io,](../saas-apps/smartvid.io-tutorial.md) [PureCloud by Genesys,](../saas-apps/purecloud-by-genesys-tutorial.md) [ClickUp Productivity Platform](../saas-apps/clickup-productivity-platform-tutorial.md)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="enhanced-combined-mfasspr-registration"></a>Registo combinado de MFA/SSPR combinado melhorado

**Tipo:** Categoria **de serviço** de funcionalidade alterada: Autosserviço Password Redefinir **capacidade de produto:** Autenticação do utilizador

Em resposta ao feedback do cliente, melhorámos a experiência combinada de pré-visualização do registo MFA/SSPR, ajudando os seus utilizadores a registarem mais rapidamente as suas informações de segurança tanto para o MFA como para o SSPR.

**Para ligar a experiência melhorada para os seus utilizadores de hoje, siga estes passos:**

1. Como administrador global ou administrador de utilizador, inscreva-se no portal Azure e vá ao **Azure Ative Directory > As definições do Utilizador > Gerir as definições para funcionalidades de pré-visualização do painel** de acesso .

2. Nos **Utilizadores que podem utilizar as funcionalidades de pré-visualização para registar e gerir informações de segurança –** opção de atualização, opte por ligar as funcionalidades para um grupo selecionado de **utilizadores** ou para **todos os utilizadores.**

Ao longo das próximas semanas, vamos remover a capacidade de ligar a antiga experiência combinada de pré-visualização de registos MFA/SSPR para inquilinos que ainda não o têm ligado.

**Para ver se o controlo será removido para o seu inquilino, siga estes passos:**

1. Como administrador global ou administrador de utilizador, inscreva-se no portal Azure e vá ao **Azure Ative Directory > As definições do Utilizador > Gerir as definições para funcionalidades de pré-visualização do painel** de acesso .

2. Se os **Utilizadores que podem utilizar as funcionalidades de pré-visualização para registar e gerir** a opção de informação de segurança estiverem definidos para **Nenhum,** a opção será removida do seu inquilino.

Independentemente de ter ligado previamente a antiga experiência de pré-visualização de registos combinados MFA/SSPR para os utilizadores ou não, a experiência antiga será desligada numa data futura. Por causa disso, sugerimos vivamente que se mude para a nova experiência melhorada o mais rápido possível.

Para obter mais informações sobre a experiência de registo melhorada, consulte as [melhorias Cool para a experiência de registo combinado de MFA combinado Azure AD e password reset.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Experiência atualizada de gestão de políticas para fluxos de utilizadores

**Tipo:** Categoria de serviço de funcionalidade **alterada:** B2C - Capacidade de Produto de Gestão de Identidade do **Consumidor:** B2B/B2C

Atualizámos o processo de criação e gestão de políticas para fluxos de utilizadores (anteriormente conhecidos como políticas incorporadas) mais facilmente. Esta nova experiência é agora o padrão para todos os seus inquilinos AD Azure.

Pode fornecer feedback e sugestões adicionais utilizando os ícones de sorriso ou franzir a testa na área de **feedback Enviar-nos** feedback no topo do ecrã do portal.

Para obter mais informações sobre a nova experiência de gestão de políticas, consulte o [Azure AD B2C agora tem a personalização JavaScript e muitos mais blog de funcionalidades novas.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Escolha versões específicas de elementos de página fornecidas por Azure AD B2C

**Tipo:** Nova **categoria de serviço de recurso:** B2C - Capacidade de Produto de Gestão de Identidade do **Consumidor:** B2B/B2C

Pode agora escolher uma versão específica dos elementos de página fornecidos pelo Azure AD B2C. Ao selecionar uma versão específica, pode testar as suas atualizações antes de aparecerem numa página e obter um comportamento previsível. Além disso, pode agora optar por impor versões de página específicas para permitir personalizações javaScript. Para ligar esta funcionalidade, aceda à página **Propriedades** nos fluxos do seu utilizador.

Para obter mais informações sobre a escolha de versões específicas de elementos de página, consulte o [Azure AD B2C agora tem a personalização JavaScript e muitos mais blog de funcionalidades novas.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Requisitos de senha de utilizador final configuráveis para B2C (GA)

**Tipo:** Nova **categoria de serviço de recurso:** B2C - Capacidade de Produto de Gestão de Identidade do **Consumidor:** B2B/B2C

Pode agora configurar a complexidade da palavra-passe da sua organização para os seus utilizadores finais, em vez de ter de usar a sua política de senha AZure AD nativa. A partir da lâmina **Propriedades** dos fluxos do seu utilizador (anteriormente conhecidas como as suas políticas incorporadas), pode escolher uma complexidade de palavra-passe de **Simples** ou **Forte,** ou pode criar um conjunto de requisitos **personalizados.**

Para obter mais informações sobre a configuração do requisito de complexidade da palavra-passe, consulte [os requisitos de complexidade de configuração para palavras-passe no Azure Ative Directory B2C](../../active-directory-b2c/password-complexity.md).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Novos modelos predefinidos para experiências de autenticação de marca personalizada

**Tipo:** Nova **categoria de serviço de recurso:** B2C - Capacidade de Produto de Gestão de Identidade do **Consumidor:** B2B/B2C

Pode utilizar os nossos novos modelos predefinidos, localizados na lâmina de **layouts** da página dos fluxos do seu utilizador (anteriormente conhecidos como políticas incorporadas), para criar uma experiência de autenticação de marca personalizada para os seus utilizadores.

Para obter mais informações sobre a utilização dos modelos, consulte [Azure AD B2C agora tem a personalização JavaScript e muitas mais novidades.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)

---

## <a name="january-2019"></a>Janeiro de 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Colaboração do Diretório Ativo B2B utilizando a autenticação de código de acesso único (pré-visualização pública)

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto B2B: B2B/B2C 

Introduzimos uma única vez a autenticação de código de acesso (OTP) para utilizadores convidados B2B que não podem ser autenticados através de outros meios como o Azure AD, uma conta Microsoft (MSA) ou federação do Google. Este novo método de autenticação significa que os utilizadores convidados não têm de criar uma nova conta Microsoft. Em vez disso, ao resgatar um convite ou aceder a um recurso partilhado, um utilizador convidado pode solicitar um código temporário para ser enviado para um endereço de e-mail. Utilizando este código temporário, o utilizador convidado pode continuar a iniciar sção.

Para mais informações, consulte [a autenticação de código de acesso de email (pré-visualização)](../external-identities/one-time-passcode.md) e o blog, [a Azure AD torna a partilha e colaboração perfeitas para qualquer utilizador com qualquer conta.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949)

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Novas configurações de cookies de procuração de aplicação Azure Ad

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade de produto Proxy **app:** Controle de Acesso

Introduzimos três novas configurações de cookies, disponíveis para as suas aplicações que são publicadas através do Application Proxy:

- **Use HTTP-Only cookie.** Define a bandeira **HTTPOnly** no acesso de procuração de aplicação e cookies de sessão. Ligar esta definição proporciona benefícios adicionais de segurança, tais como ajudar a evitar a cópia ou modificação de cookies através da scripting do lado do cliente. Recomendamos que ligue esta bandeira (escolha **Sim)** para os benefícios adicionais.

- **Use cookie seguro.** Define a bandeira **Secure** no seu acesso proxy de aplicação e cookies de sessão. Ligar esta definição proporciona benefícios adicionais de segurança, certificando-se de que os cookies são transmitidos apenas através de canais seguros TLS, como HTTPS. Recomendamos que ligue esta bandeira (escolha **Sim)** para os benefícios adicionais.

- **Use cookie persistente.** Impede que os cookies de acesso expirem quando o navegador está fechado. Estes cookies duram a vida útil do token de acesso. No entanto, os cookies são reiniciados se o prazo de validade for atingido ou se o utilizador eliminar manualmente o cookie. Recomendamos que mantenha a definição padrão **Nº,** apenas ligando a definição para aplicações mais antigas que não partilham cookies entre processos.

Para obter mais informações sobre os novos cookies, consulte [as definições de Cookies para aceder a aplicações no local no Azure Ative Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações AZure AD - janeiro 2019

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em janeiro de 2019, adicionámos estas 35 novas aplicações com apoio da Federação à galeria de aplicações:

[Firstbird](../saas-apps/firstbird-tutorial.md), [Folloze](../saas-apps/folloze-tutorial.md), [Talent Palette](../saas-apps/talent-palette-tutorial.md), [Infor CloudSuite](../saas-apps/infor-cloud-suite-tutorial.md), [Cisco Umbrella,](../saas-apps/cisco-umbrella-tutorial.md) [Zscaler Internet Access Administrator](../saas-apps/zscaler-internet-access-administrator-tutorial.md), [Expiration Reminder,](../saas-apps/expiration-reminder-tutorial.md) [InstaVR Viewer,](../saas-apps/instavr-viewer-tutorial.md) [CorpTax,](../saas-apps/corptax-tutorial.md) [Verb,](https://app.verb.net/login) [OpenLattice,](https://openlattice.com/agora) [TheOrgWiki,](https://www.theorgwiki.com/signup) [Pavaso Digital Close,](../saas-apps/pavaso-digital-close-tutorial.md) [GoodPractice Toolkit,](../saas-apps/goodpractice-toolkit-tutorial.md) [Cloud Service PICCO,](../saas-apps/cloud-service-picco-tutorial.md) [AuditBoard,](../saas-apps/auditboard-tutorial.md) [iProva](../saas-apps/iprova-tutorial.md), [Workable,](../saas-apps/workable-tutorial.md) [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](../saas-apps/gtnexus-sso-module-tutorial.md), [CBRE ServiceInsight](../saas-apps/cbre-serviceinsight-tutorial.md), [Deskradar](../saas-apps/deskradar-tutorial.md), [Coralogixv](../saas-apps/coralogix-tutorial.md), [Signagelive](../saas-apps/signagelive-tutorial.md), [ARES for Enterprise](../saas-apps/ares-for-enterprise-tutorial.md), [K2 for Office 365](https://www.k2.com/O365), [Xledger,](https://www.xledger.net/) [iDiD Manager,](../saas-apps/idid-manager-tutorial.md) [HighGear,](../saas-apps/highgear-tutorial.md) [Visitly](../saas-apps/visitly-tutorial.md), [Korn Ferry ALP,](../saas-apps/korn-ferry-alp-tutorial.md) [Acadia,](../saas-apps/acadia-tutorial.md) [Adoddle cSaas Platform](../saas-apps/adoddle-csaas-platform-tutorial.md)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Novos melhoramentos de proteção de identidade Azure AD (visualização pública)

**Tipo:** Categoria de serviço de funcionalidade **alterada:** **Capacidade de proteção de identidade: Proteção** & de Segurança de Identidade

Estamos entusiasmados por anunciar que adicionámos as seguintes melhorias à oferta pública de pré-visualização da Azure AD Identity Protection, incluindo:

- Uma interface de utilizador atualizada e mais integrada

- APIs adicionais

- Melhor avaliação dos riscos através da aprendizagem automática

- Alinhamento a nível do produto entre utilizadores de risco e entradas de risco

Para obter mais informações sobre as melhorias, consulte [o que é a Azure Ative Directory Identity Protection (refrescado)?](../identity-protection/overview-identity-protection.md) para aprender mais e partilhar os seus pensamentos através das solicitações no produto.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nova funcionalidade de Bloqueio de Aplicações para a aplicação Microsoft Authenticator em dispositivos iOS e Android

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade de produto app autenticador **microsoft: Proteção** & de segurança de identidade

Para manter os seus códigos de acesso únicos, informações de aplicações e definições de aplicações mais seguras, pode ligar a funcionalidade 'Bloqueio de Aplicações' na aplicação Microsoft Authenticator. Ligar o Bloqueio de Aplicações significa que lhe será solicitado que autente para autenticar usando o seu PIN ou biométrico sempre que abrir a aplicação Microsoft Authenticator.

Para obter mais informações, consulte a [aplicação microsoft Authenticator FAQ](../user-help/user-help-auth-app-faq.md).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Capacidades de exportação de gestão de identidade privilegiada da Azure AD (PIM)

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade privilegiada de produto de gestão de **identidade:** Gestão de Identidade privilegiada

Os administradores privilegiados de Gestão de Identidade (PIM) podem agora exportar todas as atribuições de funções ativas e elegíveis para um recurso específico, que inclui atribuições de funções para todos os recursos infantis. Anteriormente, era difícil para os administradores obter uma lista completa de atribuições de funções para uma subscrição e tinham de exportar atribuições de funções para cada recurso específico.

Para obter mais informações, consulte [o histórico de atividades e auditorias para funções de recursos Azure em PIM](../privileged-identity-management/azure-pim-resource-rbac.md).

---

## <a name="novemberdecember-2018"></a>Novembro/dezembro 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Os utilizadores removidos do âmbito de sincronização já não mudam para contas apenas na nuvem

**Tipo:** Categoria **serviço fixo:** Capacidade do produto de gestão do **utilizador:** Diretório

>[!Important]
>Ouvimos e compreendemos a sua frustração por causa desta correção. Por isso, revertemos esta mudança até ao momento em que podemos facilitar a sua implementação na sua organização.

Corrigimos um bug no qual a bandeira do DirSyncEnabled de um utilizador seria erroneamente mudada para **Falso** quando o objeto Ative Directory Domain Services (AD DS) foi excluído do âmbito de sincronização e depois transferido para o Caixote do Lixo em Azure AD no ciclo de sincronização seguinte. Como resultado desta correção, se o utilizador for excluído do âmbito de sincronização e posteriormente restaurado do Azure AD Recycle Bin, a conta do utilizador permanece sincronizada a partir de AD no local, como esperado, e não pode ser gerida na nuvem uma vez que a sua fonte de autoridade (SoA) permanece como AD no local.

Antes desta correção, houve um problema quando a bandeira do DirSyncEnabled foi mudada para Falsa. Deu a impressão errada de que estas contas eram convertidas em objetos apenas com nuvens e que as contas podiam ser geridas na nuvem. No entanto, as contas ainda mantiveram o seu SoA como no local e todas as propriedades sincronizadas (atributos de sombra) provenientes de AD no local. Esta condição causou vários problemas em AZure AD e outras cargas de trabalho em nuvem (como o Exchange Online) que esperavam tratar estas contas como sincronizadas a partir de AD, mas que agora se comportavam como contas apenas na nuvem.

Neste momento, a única maneira de converter verdadeiramente uma conta sincronizada da AD para uma conta só em nuvem é desativando o DirSync ao nível do inquilino, o que desencadeia uma operação de backend para transferir o SoA. Este tipo de alteração de SoA requer (mas não se limita) a limpar todos os atributos relacionados no local (como LastDirSyncTime e atributos de sombra) e a enviar um sinal para outras cargas de trabalho em nuvem para que o seu respetivo objeto seja convertido numa conta apenas na nuvem.

Esta correção impede, consequentemente, atualizações diretas sobre o atributo ImutávelID de um utilizador sincronizado a partir de AD, o que em alguns cenários no passado foram necessários. Por design, o ImuttableID de um objeto em Azure AD, como o nome indica, deve ser imutável. Novas funcionalidades implementadas no Azure AD Connect Health e no Azure AD Connect Synchronization estão disponíveis para abordar tais cenários:

- **Atualização ImuttableID em larga escala para muitos utilizadores numa abordagem encenada**

  Por exemplo, você precisa fazer uma longa migração inter-florestal AD DS. Solução: Utilize o Azure AD Connect para configurar a **âncora de origem** e, à medida que o utilizador migra, copie os valores ImuttableID existentes da Azure AD para o atributo ms-DS-Consistência-Y do utilizador local da nova floresta. Para obter mais informações, consulte [a utilização da ms-DS-ConsistencyGuid como fonteAnchor](../hybrid/plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

- **Atualizações ImuttableID em larga escala para muitos utilizadores de uma só vez**

  Por exemplo, ao implementar O Azure AD Connect comete um erro e agora precisa de alterar o atributo SourceAnchor. Solução: Desative a DirSync ao nível do inquilino e limpe todos os valores Imutáveis Imutáveis inválidos. Para obter mais informações, consulte [desligar a sincronização do diretório para o Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Desforra no local utilizador com um utilizador existente no Azure AD** Por exemplo, um utilizador que foi recriado em DS AD gera uma duplicação na conta AD Azure em vez de a reencacupar com uma conta AD Azure existente (objeto órfão). Solução: Utilize a Azure AD Connect Health no portal Azure para remaptar o Âncora fonte/ImutávelID. Para mais informações, consulte [o cenário do objeto órfão.](../hybrid/how-to-connect-health-diagnose-sync-errors.md#orphaned-object-scenario)

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Breaking Change: Atualizações para o esquema de registos de auditoria e login através do Azure Monitor

**Tipo:** Categoria de serviço de funcionalidade **alterada:** Capacidade do produto de reporte: Monitorização & Reporte 

Estamos atualmente a publicar os fluxos de registo de auditoria e de login através do Azure Monitor, para que possa integrar perfeitamente os ficheiros de registo com as suas ferramentas SIEM ou com o Log Analytics. Com base no seu feedback, e na preparação para o anúncio geral de disponibilidade desta funcionalidade, estamos a fazer as seguintes alterações ao nosso esquema. Estas alterações de esquema e as suas atualizações de documentação relacionadas irão acontecer na primeira semana de janeiro.

#### <a name="new-fields-in-the-audit-schema"></a>Novos campos no esquema de auditoria
Estamos adicionando um novo campo **Operação Tipo,** para fornecer o tipo de operação realizada no recurso. Por exemplo, **Adicionar,** **atualizar** ou **eliminar**.

#### <a name="changed-fields-in-the-audit-schema"></a>Campos alterados no esquema de auditoria
Os seguintes campos estão a mudar no esquema de auditoria:

|Nome do campo|O que mudou|Valores antigos|Novos Valores|
|----------|------------|----------|----------|
|Categoria|Este era o campo **de Nome de Serviço.** Agora é o campo **das Categorias de Auditoria.** **O Nome de Serviço** foi renomeado para o campo **DeSema registado.**|<ul><li>Aprovisionamento de Contas</li><li>Diretório do Núcleo</li><li>Reset de senha de autosserviço</li></ul>|<ul><li>Gestão de Utilizadores</li><li>Gestão de Grupos</li><li>Gestão de Aplicações</li></ul>|
|targetResources|Inclui **TargetResourceType** no nível superior.|&nbsp;|<ul><li>Política</li><li>Aplicação</li><li>Utilizador</li><li>Grupo</li></ul>|
|registradoByService|Fornece o nome do serviço que gerou o registo de auditoria.|Nulo|<ul><li>Aprovisionamento de Contas</li><li>Diretório do Núcleo</li><li>Reposição personalizada de palavra-passe</li></ul>|
|Resultado|Fornece o resultado dos registos de auditoria. Anteriormente, isto foi enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li></ul>|<ul><li>Success</li><li>Falha</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Campos alterados no esquema de inscrição
Os seguintes campos estão a mudar no esquema de inscrição:

|Nome do campo|O que mudou|Valores antigos|Novos Valores|
|----------|------------|----------|----------|
|aplicadaScionalAccessPolicies|Este era o campo **de segurança condicionada.** Agora é o campo **de Política Da Organização** Da Política.|Sem alterações|Sem alterações|
|condicionalAccessStatus|Fornece o resultado do Estado da Política de Acesso Condicional no início da sposição. Anteriormente, isto foi enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>Falha</li><li>Não aplicado</li><li>Desativado</li></ul>|
|aplicadoSacionalAcissess: resultado|Fornece o resultado do estado individual da política de acesso condicional no início da sposição. Anteriormente, isto foi enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>Falha</li><li>Não aplicado</li><li>Desativado</li></ul>|

Para obter mais informações sobre o esquema, consulte [o esquema de registos de auditoria AZure AD no Azure Monitor (pré-visualização)](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Melhorias na Proteção de Identidade ao modelo de aprendizagem de máquinas supervisionado e ao motor de pontuação de risco

**Tipo:** Categoria **de serviço de** funcionalidade alterada: Capacidade de proteção de **identidade: Pontuações** de risco

As melhorias no utilizador relacionado com a Proteção de Identidade e no motor de avaliação de risco de inscrição podem ajudar a melhorar a precisão e a cobertura do risco do utilizador. Os administradores podem notar que o nível de risco do utilizador já não está diretamente ligado ao nível de risco de deteções específicas, e que há um aumento no número e nível de eventos de entrada de risco.

As deteções de risco são agora avaliadas pelo modelo de aprendizagem automática supervisionado, que calcula o risco do utilizador utilizando funcionalidades adicionais dos insusitados do utilizador e um padrão de deteção. Com base neste modelo, o administrador poderá encontrar utilizadores com pontuações de alto risco, mesmo que as deteções associadas a esse utilizador sejam de baixo ou médio risco.

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Os administradores podem redefinir a sua própria palavra-passe utilizando a aplicação Microsoft Authenticator (pré-visualização pública)

**Tipo:** Categoria **de serviço** de funcionalidade alterada: Autosserviço Password Redefinir **capacidade de produto:** Autenticação do utilizador

Os administradores da AD Azure podem agora redefinir a sua própria palavra-passe utilizando as notificações de aplicações do Microsoft Authenticator ou um código a partir de qualquer aplicação de autenticador móvel ou token de hardware. Para redefinir a sua própria palavra-passe, os administradores poderão agora utilizar dois dos seguintes métodos:

- Notificação de aplicativo Microsoft Authenticator

- Outra aplicação de autenticação móvel / Código de ficha de hardware

- E-mail

- Chamada telefónica

- Mensagem de texto

Para obter mais informações sobre a utilização da app Microsoft Authenticator para redefinir palavras-passe, consulte [o reset da palavra-passe de autosserviço AD Azure - Aplicação móvel e SSPR (Preview)](../authentication/concept-sspr-howitworks.md#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nova função de administrador de dispositivo de nuvem Azure AD (pré-visualização pública)

**Tipo:** Nova **categoria de serviço** de recurso: Registo de dispositivos e capacidade de produto de gestão: controlo de acesso 

Os administradores podem atribuir aos utilizadores a nova função de Administrador de Dispositivos cloud para executar tarefas de administrador de dispositivos em nuvem. Os utilizadores atribuídos à função de Administrador de Dispositivos cloud podem ativar, desativar e eliminar dispositivos em Azure AD, juntamente com a forma de poderem ler as teclas BitLocker do Windows 10 (se estiverem presentes) no portal Azure.

Para obter mais informações sobre funções e permissões, consulte [atribuindo funções de administrador no Azure Ative Directory](../roles/permissions-reference.md)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Gerir os seus dispositivos utilizando o novo calendário de atividades em Azure AD (pré-visualização pública)

**Tipo:** Nova **categoria de serviço de recurso:** Registo de dispositivos e capacidade de produto de gestão: Gestão do ciclo de vida do dispositivo 

Sabemos que com o tempo tem de refrescar e retirar os dispositivos das suas organizações em Azure AD, para evitar ter dispositivos em mau estado no seu ambiente. Para ajudar neste processo, o Azure AD atualiza agora os seus dispositivos com uma nova estamp de tempo de atividade, ajudando-o a gerir o ciclo de vida do dispositivo.

Para obter mais informações sobre como obter e usar este timetamp, consulte [Como: Gerir os dispositivos em Azure AD](../devices/manage-stale-devices.md)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Os administradores podem exigir que os utilizadores aceitem um termos de utilização em cada dispositivo

**Tipo:** Nova **categoria de serviço** de recurso: Termos de utilização **Capacidade do produto:** Governação

Os administradores podem agora ligar os **utilizadores do Require para consentir em todas as** opções do dispositivo para exigir que os seus utilizadores aceitem os seus termos de utilização em todos os dispositivos que estão a usar no seu inquilino.

Para obter mais informações, consulte os [termos de utilização por dispositivo da função Azure Ative Directory .](../conditional-access/terms-of-use.md#per-device-terms-of-use)

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Os administradores podem configurar um termo de uso para expirar com base num horário recorrente

**Tipo:** Nova **categoria de serviço** de recurso: Termos de utilização **Capacidade do produto:** Governação


Os administradores podem agora ligar a opção **de consentimentos de Expiração** para que um prazo de utilização expire para todos os seus utilizadores com base no seu calendário recorrente especificado. O horário pode ser anual, bi-anual, trimestral ou mensal. Após o termo dos termos de utilização, os utilizadores devem voltar a aceitar.

Para obter mais informações, consulte a [secção de utilização dos termos de utilização do Azure Ative Directory](../conditional-access/terms-of-use.md#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Os administradores podem configurar um termo de uso para expirar com base na agenda de cada utilizador

**Tipo:** Nova **categoria de serviço** de recurso: Termos de utilização **Capacidade do produto:** Governação

Os administradores podem agora especificar uma duração que o utilizador deve readcetar um termos de utilização. Por exemplo, os administradores podem especificar que os utilizadores devem readcepter um termos de utilização a cada 90 dias.

Para obter mais informações, consulte a [secção de utilização dos termos de utilização do Azure Ative Directory](../conditional-access/terms-of-use.md#add-terms-of-use).

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Novos e-mails de Gestão de Identidade Privilegiada da Azure AD (PIM) para funções de Diretório Ativo Azure

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade privilegiada de produto de gestão de **identidade:** Gestão de Identidade privilegiada

Os clientes que utilizam a Azure AD Privileged Identity Management (PIM) podem agora receber um email semanal de digestão, incluindo as seguintes informações para os últimos sete dias:

- Visão geral das principais atribuições de funções elegíveis e permanentes

- Número de utilizadores que ativam funções

- Número de utilizadores atribuídos a funções na PIM

- Número de utilizadores atribuídos a funções fora da PIM

- Número de utentes "tornados permanentes" na PIM

Para obter mais informações sobre a PIM e as notificações de email disponíveis, consulte [notificações por e-mail em PIM](../privileged-identity-management/pim-email-notifications.md).

---

### <a name="group-based-licensing-is-now-generally-available"></a>O licenciamento baseado em grupo está agora geralmente disponível

**Tipo:** Alteração **da categoria de serviço de recurso:** Outra **capacidade do produto:** Diretório

O licenciamento baseado em grupo está fora da pré-visualização pública e está agora geralmente disponível. Como parte desta versão geral, tornamos esta funcionalidade mais escalável e adicionámos a capacidade de reprocessar as atribuições de licenciamento baseadas em grupo para um único utilizador e a capacidade de usar licenças baseadas em grupo com licenças Office 365 E3/A3.

Para obter mais informações sobre o licenciamento baseado em grupo, consulte [o que é o licenciamento baseado em grupo no Azure Ative Directory?](./active-directory-licensing-whatis-azure-portal.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações AZure AD - novembro de 2018

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em novembro de 2018, adicionámos estas 26 novas aplicações com apoio da Federação à galeria de aplicações:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](../saas-apps/hubspot-tutorial.md), [GetThere](../saas-apps/getthere-tutorial.md), [Gra-Pe,](../saas-apps/grape-tutorial.md) [eHour](https://getehour.com/try-now), [Consent2Go](../saas-apps/consent2go-tutorial.md), [Appinux](../saas-apps/appinux-tutorial.md), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](../saas-apps/useall-tutorial.md), [Infinite Campus,](../saas-apps/infinitecampus-tutorial.md) [Alaya,](https://alayagood.com) [HeyBuddy,](../saas-apps/heybuddy-tutorial.md) [Wrike SAML,](../saas-apps/wrike-tutorial.md) [Drift,](../saas-apps/drift-tutorial.md) [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](../saas-apps/everbridge-tutorial.md), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](../saas-apps/ivanti-service-manager-tutorial.md), [Peakon](../saas-apps/peakon-tutorial.md), [Allbound SSO](../saas-apps/allbound-sso-tutorial.md), [Aplicações Plex - Classic Test](https://test.plexonline.com/signon), [Aplicações Plex – Classic](https://www.plexonline.com/signon), [Plex Apps - UX Test](https://test.cloud.plex.com/sso), [Plex Apps – UX](https://cloud.plex.com/sso), [Plex Apps – IAM,](https://accounts.plex.com/) [CRAFTS - Childcare Records, Attendance, Sistema de Rastreio Financeiro &](https://getcrafts.ca/craftsregistration)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

## <a name="october-2018"></a>Outubro de 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Os Registos AD Azure agora funcionam com a Azure Log Analytics (pré-visualização pública)

**Tipo:** Nova **categoria de serviço** de recurso: Capacidade do produto de reporte: Monitorização & Reporte 

Estamos entusiasmados por anunciar que agora pode encaminhar os seus registos AD Azure para a Azure Log Analytics! Esta funcionalidade mais procurada ajuda a dar-lhe ainda melhor acesso a análises para o seu negócio, operações e segurança, bem como uma forma de ajudar a monitorizar a sua infraestrutura. Para mais informações, consulte os registos de [Atividade do Diretório Ativo do Azure no Azure Log Analytics já disponível.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações AZure AD - outubro 2018

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em outubro de 2018, adicionámos estas 14 novas aplicações com apoio da Federação à galeria de aplicações:

[Os meus pontos de prêmio](../saas-apps/myawardpoints-tutorial.md), [Vibe HCM,](../saas-apps/vibehcm-tutorial.md)ambyint, [MyWorkDrive](../saas-apps/myworkdrive-tutorial.md), [BorrowBox,](../saas-apps/borrowbox-tutorial.md)Dialpad, [ON24 Virtual Environment,](../saas-apps/on24-tutorial.md) [RingCentral,](../saas-apps/ringcentral-tutorial.md) [Zscaler Three](../saas-apps/zscaler-three-tutorial.md), [Phraseanet](../saas-apps/phraseanet-tutorial.md), [Appraisd,](../saas-apps/appraisd-tutorial.md) [Workspot Control,](../saas-apps/workspotcontrol-tutorial.md) [Shuccho Navi,](../saas-apps/shucchonavi-tutorial.md) [Glassfrog](../saas-apps/glassfrog-tutorial.md)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="azure-ad-domain-services-email-notifications"></a>Notificações de email dos serviços de domínio Azure AD

**Tipo:** Nova **categoria de serviço de recurso:** Azure AD Domain Services **Capacidade do produto:** Azure AD Domain Services

A Azure AD Domain Services fornece alertas no portal Azure sobre configurações erradas ou problemas com o seu domínio gerido. Estes alertas incluem guias passo a passo para que possa tentar corrigir os problemas sem ter de contactar suporte.

A partir de outubro, poderá personalizar as definições de notificação para o seu domínio gerido para que, quando ocorrerem novos alertas, seja enviado um e-mail para um grupo de pessoas designado, eliminando a necessidade de verificar constantemente o portal para obter atualizações.

Para obter mais informações, consulte [as definições de Notificação nos Serviços de Domínio Azure AD](../../active-directory-domain-services/notifications.md).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>O portal AD AZure suporta a utilização da API de domínio ForceDelete para eliminar domínios personalizados

**Tipo:** Categoria de serviço de funcionalidade **alterada:** Capacidade do produto de gestão de diretório: Diretório 

Temos o prazer de anunciar que agora pode utilizar a API de domínio ForceDelete para eliminar os seus nomes de domínio personalizados, nomeando assíncroneamente referências, como utilizadores, grupos e aplicações do seu nome de domínio personalizado (contoso.com) de volta ao nome de domínio padrão inicial (contoso.onmicrosoft.com).

Esta alteração ajuda-o a eliminar mais rapidamente os seus nomes de domínio personalizados se a sua organização deixar de usar o nome, ou se precisar de usar o nome de domínio com outro Azure AD.

Para obter mais informações, consulte [Eliminar um nome de domínio personalizado](../enterprise-users/domains-manage.md#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Setembro de 2018

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Permissões de funções de administrador atualizadas para grupos dinâmicos

**Tipo:** Categoria **serviço fixo:** Capacidade do Produto de Gestão de **Grupo:** Colaboração

Corrigimos um problema para que as funções específicas de administrador possam agora criar e atualizar regras dinâmicas de adesão, sem precisar de ser o proprietário do grupo.

Os papéis são:

- Administrador global

- Administrador do Intune

- Administrador de utilizadores

Para obter mais informações, consulte [Criar um grupo dinâmico e verificar o estado](../enterprise-users/groups-create-rule.md)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Configurações de configuração de Sign-On única simplificada (SSO) para algumas aplicações de terceiros

**Tipo:** Nova **categoria de funcionalidades:** Capacidade do produto de aplicações **empresariais:** SSO

Percebemos que a criação de aplicações Single Sign-On (SSO) para Software como serviço (SaaS) pode ser um desafio devido à natureza única de cada configuração de aplicações. Construímos uma experiência de configuração simplificada para preencher automaticamente as definições de configuração SSO para as seguintes aplicações SaaS de terceiros:

- Zendesk

- ArcGis Online

- Jamf Pro

Para começar a utilizar esta experiência de um clique, aceda à página de  >  **configuração SSO** do portal Azure para a aplicação. Para mais informações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](../saas-apps/tutorial-list.md)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Diretório Ativo Azure - Onde estão os seus dados localizados? página

**Tipo:** Nova **categoria de funcionalidades Serviço:** Outra **capacidade do produto:** GoLocal

Selecione a região da sua empresa a partir do **Diretório Ativo Azure - Onde está** a página dos seus dados para ver qual o datacenter Azure que abriga os seus dados AD Azure em repouso para todos os serviços Azure AD. Pode filtrar a informação através de serviços específicos da Azure AD para a região da sua empresa.

Para aceder a esta funcionalidade e para mais informações, consulte [o Diretório Ativo Azure - Onde estão localizados os seus dados.](https://aka.ms/AADDataMap)

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Novo plano de implementação disponível para o painel My Apps Access

**Tipo:** Nova **categoria de funcionalidades Serviço:** A minha **capacidade de produto apps:** SSO

Confira o novo plano de implementação disponível para o painel My Apps Access ( My Apps Access). https://aka.ms/deploymentplans)
O painel My Apps Access fornece aos utilizadores um único local para encontrar e aceder às suas apps. Este portal também oferece aos utilizadores oportunidades de self-service, como solicitar o acesso a apps e grupos, ou gerir o acesso a esses recursos em nome de outros.

Para mais informações, consulte [o portal My Apps?](../user-help/my-apps-portal-end-user-access.md)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Novo separador de resolução de problemas e suporte na página de registos de logins do portal Azure

**Tipo:** Nova **categoria de serviço** de recurso: Capacidade do produto de reporte: Monitorização & Reporte 

O novo **separador troubleshooting and Support** na página **de Iniciars-ins** do portal Azure, destina-se a ajudar os administradores e os engenheiros de suporte a resolver problemas relacionados com os sign-ins Azure AD. Este novo separador fornece o código de erro, mensagem de erro e recomendações de reparação (se houver) para ajudar a resolver o problema. Se não conseguir resolver o problema, também lhe damos uma nova forma de criar um bilhete de apoio utilizando a experiência **Copy to Clipboard,** que preenche os campos **de ID** e Data de Pedido **(UTC)** para o ficheiro de registo no seu bilhete de suporte.

![Registos de inscrição mostrando o novo separador](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Suporte melhorado para propriedades de extensão personalizadas usadas para criar regras dinâmicas de adesão

**Tipo:** Categoria **de serviço** de funcionalidade alterada: Capacidade do produto de gestão **de grupo:** colaboração

Com esta atualização, pode agora clicar no link Obter propriedades de **extensão personalizadas** a partir do construtor de regras do grupo de utilizadores dinâmico, introduzir o seu ID de aplicação único e receber a lista completa de propriedades de extensão personalizadas para usar ao criar uma regra de adesão dinâmica para os utilizadores. Esta lista também pode ser atualizada para obter quaisquer novas propriedades de extensão personalizada para essa aplicação.

Para obter mais informações sobre a utilização de propriedades de extensão personalizadas para regras dinâmicas de [adesão, consulte propriedades de extensão e propriedades de extensão personalizadas](../enterprise-users/groups-dynamic-membership.md#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novas aplicações para clientes aprovados para acesso condicional baseado em aplicativos Azure AD

**Tipo:** Plano de alteração **da categoria serviço:** Capacidade de **acesso condicional: Segurança** e proteção de identidade

As seguintes aplicações estão na lista de [aplicações de clientes aprovadas:](../conditional-access/concept-conditional-access-conditions.md#client-apps)

- Microsoft To-Do

- Microsoft Stream

Para obter mais informações, veja:

- [Acesso Condicional baseado em aplicativos Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Novo suporte para Self-Service redefinição de palavra-passe a partir do ecrã de bloqueio do Windows 7/8.1

**Tipo:** Nova **categoria de serviço** de funcionalidade: Capacidade do produto SSPR: Autenticação do utilizador 

Depois de configurar esta nova funcionalidade, os seus utilizadores verão um link para redefinir a sua palavra-passe a partir do ecrã **de bloqueio** de um dispositivo que executa o Windows 7, Windows 8 ou Windows 8.1. Ao clicar nesse link, o utilizador é guiado através do mesmo fluxo de reset de palavra-passe que através do navegador web.

Para obter mais informações, consulte [Como ativar a redefinição da palavra-passe do Windows 7, 8 e 8.1](../authentication/howto-sspr-windows.md)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Aviso de alteração: Os códigos de autorização deixarão de estar disponíveis para reutilização

**Tipo:** Plano de alteração **Da categoria de Serviço:** Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

A partir de 15 de novembro de 2018, o Azure AD deixará de aceitar códigos de autenticação usados anteriormente para apps. Esta mudança de segurança ajuda a alinhar o Azure AD com a especificação OAuth e será aplicada tanto nos pontos finais v1 como v2.

Se a sua aplicação reutilizar códigos de autorização para obter fichas para vários recursos, recomendamos que use o código para obter um token de atualização e, em seguida, use esse token de atualização para adquirir fichas adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez, mas os tokens de atualização podem ser usados várias vezes em vários recursos. Uma aplicação que tente reutilizar um código de autenticação durante o fluxo de código OAuth obterá um erro invalid_grant.

Para esta e outras alterações relacionadas com protocolos, consulte [a lista completa das novidades para a autenticação.](../develop/reference-breaking-changes.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações AZure AD - setembro de 2018

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em setembro de 2018, adicionámos estas 16 novas aplicações com apoio da Federação à galeria de aplicações:

[Uberflip](../saas-apps/uberflip-tutorial.md), [Comeet Recruiting Software](../saas-apps/comeetrecruitingsoftware-tutorial.md), [Workteam,](../saas-apps/workteam-tutorial.md) [ArcGIS Enterprise](../saas-apps/arcgisenterprise-tutorial.md), [Nuclino](../saas-apps/nuclino-tutorial.md), [JDA Cloud,](../saas-apps/jdacloud-tutorial.md) [Snowflake,](../saas-apps/snowflake-tutorial.md)NavigoCloud, [Figma,](../saas-apps/figma-tutorial.md)join.me, [ZephyrSSO,](../saas-apps/zephyrsso-tutorial.md) [Silverback,](../saas-apps/silverback-tutorial.md)Riverbed Xirrus EasyPass, [Rackspace SSO](../saas-apps/rackspacesso-tutorial.md), Enlyft SSO for Azure, SurveyMonkey, [Convene,](../saas-apps/convene-tutorial.md) [dmarcian](../saas-apps/dmarcian-tutorial.md)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="support-for-additional-claims-transformations-methods"></a>Apoio a métodos adicionais de transformação de sinistros

**Tipo:** Nova **categoria de funcionalidades:** Capacidade do produto de aplicações **empresariais:** SSO

Introduzimos novos métodos de transformação de reivindicações, ToLower e ToUpper(), que podem ser aplicados a fichas SAML a partir da página de **Configuração single Sign-On** baseada em SAML.

Para obter mais informações, consulte [Como personalizar as reclamações emitidas no token SAML para aplicações empresariais em Azure AD](../develop/active-directory-saml-claims-customization.md)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>UI de configuração de aplicativos atualizado baseado em SAML (pré-visualização)

**Tipo:** Alteração **da categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** SSO

Como parte da nossa uI de configuração de aplicativos baseado em SAML atualizada, você receberá:

- Uma experiência de walkthrough atualizada para configurar as suas aplicações baseadas em SAML.

- Mais visibilidade sobre o que falta ou está errado na sua configuração.

- A capacidade de adicionar vários endereços de e-mail para notificação de certificado de expiração.

- Novos métodos de transformação de reivindicações, ToLower e ToUpper(), e muito mais.

- Uma forma de carregar o seu próprio certificado de assinatura de fichas para as suas aplicações empresariais.

- Uma forma de definir o formato NameID para aplicações SAML, e uma forma de definir o valor NameID como Extensões de Diretório.

Para ligar esta vista atualizada, clique no Teste o **nosso novo** link de experiência a partir do topo da página **'Sign-On' único.** Para obter mais informações, consulte [Tutorial: Configurar o único sinal de inscrição baseado em SAML para uma aplicação com o Azure Ative Directory](../manage-apps/view-applications-portal.md).

---

## <a name="august-2018"></a>Agosto de 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Alterações nas gamas de endereços IP do Azure Ative Directory

**Tipo:** Plano de mudança **categoria de serviço:** Outra **capacidade de produto:** Plataforma

Estamos a introduzir gamas ip maiores para Azure AD, o que significa que se configurar as gamas de endereços IP AZure para as suas firewalls, routers ou Grupos de Segurança de Rede, terá de os atualizar. Estamos a fazer esta atualização para que não tenhas de alterar as configurações de gama IP do firewall, router ou Grupos de Segurança de Rede novamente quando o Azure AD adicionar novos pontos finais.

O tráfego da rede está a mover-se para estas novas gamas nos próximos dois meses. Para continuar com um serviço ininterrupto, deve adicionar estes valores atualizados aos seus Endereços IP antes de 10 de setembro de 2018:

- 20.190.128.0/18

- 40.126.0.0/18

Recomendamos vivamente não remover as antigas gamas de Endereços IP até que todo o tráfego da sua rede tenha mudado para as novas gamas. Para obter atualizações sobre o movimento e para saber quando pode remover as gamas antigas, consulte os [intervalos de urls e endereços IP do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Aviso de alteração: Os códigos de autorização deixarão de estar disponíveis para reutilização

**Tipo:** Plano de alteração **Da categoria de Serviço:** Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

A partir de 15 de novembro de 2018, o Azure AD deixará de aceitar códigos de autenticação usados anteriormente para apps. Esta mudança de segurança ajuda a alinhar o Azure AD com a especificação OAuth e será aplicada tanto nos pontos finais v1 como v2.

Se a sua aplicação reutilizar códigos de autorização para obter fichas para vários recursos, recomendamos que use o código para obter um token de atualização e, em seguida, use esse token de atualização para adquirir fichas adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez, mas os tokens de atualização podem ser usados várias vezes em vários recursos. Uma aplicação que tente reutilizar um código de autenticação durante o fluxo de código OAuth obterá um erro invalid_grant.

Para esta e outras alterações relacionadas com protocolos, consulte [a lista completa das novidades para a autenticação.](../develop/reference-breaking-changes.md)

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Gestão de informações de segurança convergentes para senha de autosserviço (SSPR) e Autenticação multi-factor (MFA)

**Tipo:** Nova **categoria de serviço** de funcionalidade: Capacidade do produto SSPR: Autenticação do utilizador 

Esta nova funcionalidade ajuda as pessoas a gerir as suas informações de segurança (como, número de telefone, aplicação móvel, e assim por diante) para SSPR e MFA num único local e experiência; em comparação com anteriormente, onde foi feito em dois locais diferentes.

Esta experiência convergente também funciona para pessoas que usam SSPR ou MFA. Além disso, se a sua organização não aplicar o registo de MFA ou SSPR, as pessoas ainda podem registar quaisquer métodos de informação de segurança MFA ou SSPR permitidos pela sua organização a partir do portal My Apps.

Esta é uma pré-visualização pública opt-in. Os administradores podem ligar a nova experiência (se desejar) para um grupo selecionado ou para todos os utilizadores num inquilino. Para mais informações sobre a experiência convergente, consulte o [blog de experiência converged](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Definição de cookies de HTTP-Only nova em aplicativos proxy Azure AD Application

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade de produto Proxy **app:** Controle de Acesso

Há uma nova definição chamada **COOKIES HTTP-Only** nas suas aplicações Proxy de aplicações. Esta definição ajuda a fornecer segurança extra, incluindo a bandeira HTTPOnly no cabeçalho de resposta HTTP para ambos os cookies de acesso proxy de aplicação e sessão, parar o acesso ao cookie a partir de um script do lado do cliente e impedir ainda mais ações como copiar ou modificar o cookie. Embora esta bandeira não tenha sido utilizada anteriormente, os seus cookies foram sempre encriptados e transmitidos utilizando uma ligação TLS para ajudar a proteger contra modificações impróprias.

Esta definição não é compatível com aplicações que utilizem controlos ActiveX, como o Remote Desktop. Se estiver nesta situação, recomendamos que desligue esta definição.

Para obter mais informações sobre a definição de Cookies HTTP-Only, consulte [publicar aplicações utilizando o Azure AD Application Proxy](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Gestão de Identidade Privilegiada (PIM) para recursos Azure suporta tipos de recursos do Grupo de Gestão

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade privilegiada de produto de gestão de **identidade:** Gestão de Identidade privilegiada

As definições de ativação e atribuição just-in-time podem agora ser aplicadas aos tipos de recursos do Grupo de Gestão, tal como já faz para Subscrições, Grupos de Recursos e Recursos (como VMs, Serviços de Aplicações e muito mais). Além disso, qualquer pessoa com um papel que forneça acesso a administrador para um Grupo de Gestão pode descobrir e gerir esse recurso em PIM.

Para obter mais informações sobre os recursos PIM e Azure, consulte [Descubra e gere os recursos do Azure utilizando a Gestão de Identidade Privilegiada](../privileged-identity-management/pim-resource-roles-discover-resources.md)

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>O acesso à aplicação (pré-visualização) proporciona um acesso mais rápido ao portal AD Azure

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade privilegiada de produto de gestão de **identidade:** Gestão de Identidade privilegiada

Hoje, ao ativar uma função usando PIM, pode levar mais de 10 minutos para que as permissões produzam efeitos. Se optar por utilizar o acesso à Aplicação, que está atualmente em pré-visualização pública, os administradores podem aceder ao portal AD Azure assim que o pedido de ativação estiver concluído.

Atualmente, o acesso à aplicação suporta apenas a experiência do portal AD Azure e os recursos Azure. Para obter mais informações sobre o acesso a PIM e aplicação, consulte [o que é Azure AD Gestão de Identidade Privilegiada?](../privileged-identity-management/pim-configure.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações AZure AD - agosto de 2018

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em agosto de 2018, adicionámos estas 16 novas aplicações com apoio da Federação à galeria de aplicações:

[Hornbill](../saas-apps/hornbill-tutorial.md), [Bridgeline Unbound](../saas-apps/bridgelineunbound-tutorial.md), [Sauce Labs - Mobile and Web Testing,](../saas-apps/saucelabs-mobileandwebtesting-tutorial.md) [Meta Networks Connector](../saas-apps/metanetworksconnector-tutorial.md), [Way We Do,](../saas-apps/waywedo-tutorial.md) [Spotinst,](../saas-apps/spotinst-tutorial.md) [ProMaster (by Inlogik)](../saas-apps/promaster-tutorial.md), SchoolBooking, [4me](../saas-apps/4me-tutorial.md), [Dossier](../saas-apps/dossier-tutorial.md), [N2F - Relatórios de despesas,](../saas-apps/n2f-expensereports-tutorial.md) [Comm100 Live Chat,](../saas-apps/comm100livechat-tutorial.md) [SafeConnect,](../saas-apps/safeconnect-tutorial.md) [ZenQMS,](../saas-apps/zenqms-tutorial.md) [eLuminate,](../saas-apps/eluminate-tutorial.md) [Dovetale.](../saas-apps/dovetale-tutorial.md)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>O suporte native Tableau já está disponível no Azure AD Application Proxy

**Tipo:** Alteração **da categoria de serviço de funcionalidade:** Capacidade do produto Proxy **app:** Controlo de acesso

Com a nossa atualização do Protocolo de Concessão de CódigoS OAuth 2.0 da OAuth 2.0 para o nosso protocolo de pré-autenticação, já não tem de fazer qualquer configuração adicional para utilizar o Tableau com o Application Proxy. Esta alteração de protocolo também ajuda a Aplicação Proxy a suportar melhor as aplicações mais modernas, utilizando apenas redirecionamentos HTTP, que são comumente suportados em tags JavaScript e HTML.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Novo suporte para adicionar o Google como fornecedor de identidade para utilizadores convidados B2B em Azure Ative Directory (pré-visualização)

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto B2B: B2B/B2C 

Ao criar uma federação com o Google na sua organização, pode deixar que os utilizadores convidados do Gmail inscrevam-se nas suas aplicações e recursos partilhados utilizando a sua conta Google existente, sem ter de criar uma Conta Microsoft pessoal (MSAs) ou uma conta Azure AD.

Esta é uma pré-visualização pública opt-in. Para obter mais informações sobre a federação do Google, consulte [o Add Google como um fornecedor de identidade para utilizadores convidados B2B.](../external-identities/google-federation.md)

---

## <a name="july-2018"></a>Julho de 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Melhorias nas notificações de email do Azure Ative Directory

**Tipo:** Alteração da **categoria de serviço de recurso:** Outra capacidade do **produto:** Gestão do ciclo de vida da identidade

Os e-mails do Azure Ative Directory (Azure AD) passam a contar com um design atualizado, bem como alterações no endereço de e-mail e nome de exibição do remetente, quando enviados a partir dos seguintes serviços:

- Comentários de acesso a Ad Azure
- Azure AD Connect Health
- Azure AD Identity Protection
- Azure AD Privileged Identity Management
- Notificações de certificado de expiração da aplicação da empresa
- Notificações de serviço de fornecimento de aplicativos da empresa

As notificações por e-mail serão enviadas a partir do seguinte endereço de e-mail e nome de exibição:

- Endereço de e-mail: azure-noreply@microsoft.com
- Nome do ecrã: Microsoft Azure

Para um exemplo de alguns dos novos desenhos de e-mail e mais informações, consulte [notificações de email em Azure AD PIM](../privileged-identity-management/pim-email-notifications.md).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Os Registos de Atividades AZure AD já estão disponíveis através do Azure Monitor

**Tipo:** Nova **categoria de serviço** de recurso: Capacidade do produto de reporte: Monitorização & Reporte 

Os Registos de Atividades Azure AD estão agora disponíveis em pré-visualização pública para o Azure Monitor (serviço de monitorização em toda a plataforma da Azure). O Azure Monitor oferece-lhe retenção a longo prazo e integração perfeita, além destas melhorias:

- Retenção a longo prazo, encaminhando os seus ficheiros de registo para a sua própria conta de armazenamento Azure.

- Integração SIEM sem emenda, sem que você escreva ou mantenha scripts personalizados.

- Integração perfeita com as suas próprias soluções personalizadas, ferramentas de análise ou soluções de gestão de incidentes.

Para obter mais informações sobre estas novas capacidades, consulte [os registos de atividades do Azure AD no Azure Monitor e está agora em pré-visualização pública](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) e a nossa documentação, [registos de atividade do Azure Ative Directory no Azure Monitor (pré-visualização)](../reports-monitoring/concept-activity-logs-azure-monitor.md).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informações de acesso condicional adicionadas ao relatório de inscrições da Azure AD

**Tipo:** Nova **categoria de serviço** de funcionalidade: Capacidade do produto reportando: Proteção & de Segurança de Identidade 

Esta atualização permite-lhe ver quais as políticas que são avaliadas quando um utilizador assina juntamente com o resultado da política. Além disso, o relatório inclui agora o tipo de aplicação de clientes utilizada pelo utilizador, para que possa identificar o tráfego de protocolos antigos. As entradas de relatório também podem agora ser procuradas por um ID de correlação, que pode ser encontrado na mensagem de erro virada para o utilizador e pode ser usado para identificar e resolver problemas no pedido de inscrição correspondente.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Ver autenticações antigas através de registos de atividades de login

**Tipo:** Nova **categoria de serviço** de recurso: Capacidade do produto de reporte: Monitorização & Reporte 

Com a introdução do campo **app** cliente nos registos de atividade de Login, os clientes podem agora ver os utilizadores que estão a usar autenticações antigas. Os clientes poderão aceder a estas informações utilizando os Logins Microsoft Graph API ou através dos registos de atividade de Login no portal AD Azure, onde pode utilizar o controlo da App do **Cliente** para filtrar as autenticações antigas. Consulte a documentação para mais detalhes.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações AZure AD - julho 2018

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em julho de 2018, adicionámos estas 16 novas aplicações com apoio da Federação à galeria de aplicações:

[Innovation Hub](../saas-apps/innovationhub-tutorial.md), [Leapsome](../saas-apps/leapsome-tutorial.md), [Certain Admin SSO](../saas-apps/certainadminsso-tutorial.md), PSUC Staging, [iPass SmartConnect](../saas-apps/ipasssmartconnect-tutorial.md), [Screencast-O-Matic,](../saas-apps/screencast-tutorial.md)PowerSchool Uniified Classroom, [Eli Onboarding,](../saas-apps/elionboarding-tutorial.md) [Bomgar Remote Support,](../saas-apps/bomgarremotesupport-tutorial.md) [Nimblex,](../saas-apps/nimblex-tutorial.md) [Imagineer WebVision,](../saas-apps/imagineerwebvision-tutorial.md) [Insight4GRC,](../saas-apps/insight4grc-tutorial.md) [SecureW2 JoinNow Connector,](../saas-apps/securejoinnow-tutorial.md) [Kanbanize,](../saas-apps/kanbanize-tutorial.md) [SmartLPA,](../saas-apps/smartlpa-tutorial.md) [Skills Base](../saas-apps/skillsbase-tutorial.md)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Novos utilizadores que aprovisionam integrações de aplicações saaS - julho de 2018

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto de fornecimento de **aplicativos: Integração** de terceiros

O Azure AD permite automatizar a criação, manutenção e remoção de identidades de utilizadores em aplicações SaaS como Dropbox, Salesforce, ServiceNow, entre outras. Para julho de 2018, adicionámos suporte ao fornecimento de utilizadores para as seguintes aplicações na galeria de aplicações AZure AD:

- [Cisco WebEx](../saas-apps/cisco-webex-provisioning-tutorial.md)

- [Bonusly](../saas-apps/bonusly-provisioning-tutorial.md)

Para obter uma lista de todas as aplicações que suportam o provisionamento do utilizador na galeria AZure AD, consulte a integração da [aplicação SaaS com o Azure Ative Directory](../saas-apps/tutorial-list.md).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync - Uma forma mais fácil de corrigir erros de sincronização de atributos órfãos e duplicados

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade de produto AD **Connect:** Monitorização & Reporte

A azure AD Connect Health introduz a remediação do autosserviço para ajudá-lo a realçar e corrigir erros de sincronização. Esta funcionalidade resolve erros de sincronização duplicados de atributos e corre objetos órfãos do Azure AD. Este diagnóstico tem os seguintes benefícios:

- Reduz os erros de sincronização de atributos duplicados, fornecendo correções específicas

- Aplica uma correção para cenários Azure AD dedicados, resolvendo erros num único passo

- Não é necessário nenhum upgrade ou configuração para ligar e utilizar esta funcionalidade

Para obter mais informações, consulte [diagnosticar e remediar erros de sincronização de atributos duplicados](../hybrid/how-to-connect-health-diagnose-sync-errors.md)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Atualizações visuais para as experiências de inscrição Azure AD e MSA

**Tipo:** Alteração **da categoria de serviço de funcionalidade:** Capacidade do produto AD Azure: Autenticação do utilizador 

Atualizámos o UI para a experiência de sindução de serviços online da Microsoft, como para o Office 365 e OZure. Esta mudança torna os ecrãs menos desordenados e mais simples. Para obter mais informações sobre esta mudança, consulte as [próximas melhorias no blog de experiência de inscrição AZure AD.](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/)

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Novo lançamento do Azure AD Connect - julho 2018

**Tipo:** Categoria de serviço de funcionalidade **alterada:** Capacidade de Fornecimento de **Aplicativos: Gestão** do ciclo de vida da identidade

A mais recente versão do Azure AD Connect inclui:

- Correções de bugs e atualizações de suporte

- Disponibilidade Geral da integração Ping-Federate

- Atualizações para o mais recente cliente SQL 2012

Para mais informações sobre esta atualização, consulte [Azure AD Connect: Versão release history](../hybrid/reference-connect-version-history.md)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Atualizações para os termos de utilização uI do utilizador final

**Tipo:** Alteração **da categoria de serviço** de recurso: Termos de utilização **Capacidade do produto:** Governação

Estamos a atualizar a cadeia de aceitação na UI do utilizador final da TOU.

**Texto atual.** Para aceder aos recursos [do nome do inquilino], deve aceitar os termos de utilização.<br>**Novo texto.** Para aceder ao recurso [tenantName], deve ler os termos de utilização.

**Texto atual:** Optar por aceitar significa que concorda com todos os termos de uso acima.<br>**Novo texto:** Clique em Aceitar para confirmar que leu e entendeu os termos de utilização.

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>A Autenticação Pass-through suporta protocolos e aplicações antigas

**Tipo:** Alteração **da categoria de serviço** de funcionalidade: Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

A Authentication pass-through suporta agora protocolos e aplicações antigas. As seguintes limitações são agora totalmente suportadas:

- Logins de utilizador para aplicações de clientes do Office, Office 2010 e Office 2013, sem necessidade de autenticação moderna.

- Acesso à partilha de calendários e informações gratuitas/ocupadas em ambientes híbridos exchange apenas no Office 2010.

- Logins do utilizador no Skype para aplicações de clientes empresariais sem necessidade de autenticação moderna.

- Insuposições do utilizador para a versão 1.0 do PowerShell.

- O Programa de Inscrição de Dispositivos apple (Apple DEP), utilizando o assistente de configuração do iOS.

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Gestão de informações de segurança convergentes para reset de senha de autosserviço e autenticação multi-factor

**Tipo:** Nova **categoria de serviço** de funcionalidade: Capacidade do produto SSPR: Autenticação do utilizador 

Esta nova funcionalidade permite que os utilizadores gerem as suas informações de segurança (por exemplo, número de telefone, endereço de e-mail, aplicação móvel, etc.) para reset de senha de autosserviço (SSPR) e Autenticação multi-factor (MFA) numa única experiência. Os utilizadores deixarão de ter de registar as mesmas informações de segurança para sSPR e MFA em duas experiências diferentes. Esta nova experiência aplica-se também aos utilizadores que tenham SSPR ou MFA.

Se uma organização não estiver a fazer cumprir o registo de MFA ou SSPR, os utilizadores podem registar as suas informações de segurança através do portal **My Apps.** A partir daí, os utilizadores podem registar todos os métodos habilitados para MFA ou SSPR.

Esta é uma pré-visualização pública opt-in. Os administradores podem ligar a nova experiência (se desejar) para um grupo selecionado de utilizadores ou todos os utilizadores num inquilino.

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Utilize a aplicação Microsoft Authenticator para verificar a sua identidade quando redefinir a sua palavra-passe

**Tipo:** Alteração da **categoria de serviço de funcionalidade:** Capacidade do produto SSPR: Autenticação do utilizador 

Esta funcionalidade permite que os administradores não administrados verifiquem a sua identidade enquanto reiniciam uma palavra-passe utilizando uma notificação ou código do Microsoft Authenticator (ou qualquer outra aplicação de autenticador). Depois de os administradores ligarem este método de reset de senha de autosserviço, os utilizadores que tenham registado uma aplicação móvel através de aka.ms/mfasetup ou aka.ms/setupsecurityinfo podem usar a sua aplicação móvel como método de verificação enquanto reiniciam a sua palavra-passe.

A notificação de aplicações móveis só pode ser ligada como parte de uma política que requer dois métodos para redefinir a sua palavra-passe.

---

## <a name="june-2018"></a>junho de 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Aviso de alteração: Correção de segurança para o fluxo de autorização delegado para apps que utilizam Azure AD Activity Logs API

**Tipo:** Plano de alteração **da categoria de serviço:** Capacidade do produto de reporte: Monitorização & Reporte 

Devido à nossa aplicação de segurança mais forte, tivemos que fazer uma alteração nas permissões para apps que usam um fluxo de autorização delegado para aceder a [APIs de Registos de Atividades AD Azure](../reports-monitoring/concept-reporting-api.md). Esta alteração ocorrerá até **26 de junho de 2018**.

Se alguma das suas aplicações utilizar APIs de Registo de Atividade AD Azure, siga estes passos para garantir que a aplicação não se parte após a mudança acontecer.

**Para atualizar as permissões da sua aplicação**

1. Inscreva-se no portal Azure, selecione **Azure Ative Directory** e, em seguida, selecione **Registos de Aplicações**.
2. Selecione a sua aplicação que utiliza a API de Registos de Atividade azure, selecione **Definições**, **selecione permissões necessárias** e, em seguida, selecione a API **do Diretório Ativo Windows Azure.**
3. Na área de **permissões delegadas** da lâmina de **acesso Ativa,** selecione a caixa ao lado dos dados **do diretório ler** e, em seguida, selecione **Guardar**.
4. Selecione **permissões grant** e, em seguida, selecione **Sim**.

    >[!Note]
    >Você deve ser um administrador global para conceder permissões à app.

Para mais informações, consulte a área de permissões de [concessão](../reports-monitoring/howto-configure-prerequisites-for-reporting-api.md#grant-permissions) dos Pré-requisitos para aceder ao artigo da API de relatório da API.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Configurar definições de TLS para ligar aos serviços AZure AD para a conformidade com o DSS PCI

**Tipo:** Nova **categoria de serviço** de recurso: Capacidade do produto N/A: Plataforma 

Transport Layer Security (TLS) é um protocolo que fornece privacidade e integridade de dados entre duas aplicações comunicantes e é o protocolo de segurança mais amplamente implementado hoje.

O Conselho de Normas de [Segurança do PCI](https://www.pcisecuritystandards.org/) determinou que as primeiras versões de TLS e Secure Sockets Layer (SSL) devem ser desativadas a favor de permitir protocolos de aplicações novos e mais seguros, com o cumprimento a partir de **30 de junho de 2018**. Esta alteração significa que, se ligar aos serviços AZure AD e necessitar de conformidade com o DSS do PCI, deve desativar o TLS 1.0. Várias versões de TLS estão disponíveis, mas tLS 1.2 é a versão mais recente disponível para Azure Ative Directory Services. Recomendamos vivamente que se mose diretamente para o TLS 1.2 para combinações de cliente/servidor e navegador/servidor.

Os navegadores desatualizados podem não suportar versões TLS mais recentes, como o TLS 1.2. Para ver quais versões de TLS são suportadas pelo seu navegador, vá ao site [qualys SSL Labs](https://www.ssllabs.com/) e clique em **Testar o seu navegador.** Recomendamos que atualize para a versão mais recente do seu navegador web e de preferência ative apenas O TLS 1.2.

**Para ativar TLS 1.2, por browser**

- **Microsoft Edge e Internet Explorer (ambos estão definidos usando o Internet Explorer)**

    1. Abra o Internet Explorer, selecione **Tools**  >  **Internet Options**  >  **Advanced**.
    2. Na área **de Segurança,** selecione **utilizar TLS 1.2** e, em seguida, selecione **OK**.
    3. Feche todas as janelas do navegador e reinicie o Internet Explorer.

- **Google Chrome**

    1. Abra o Google Chrome, escreva *chrome://settings/* na barra de endereços e prima **Enter**.
    2. Expandir as opções **Advanced,** ir para a área **do Sistema** e selecionar **definições de procuração abertas**.
    3. Na caixa Propriedades da **Internet,** selecione o separador **Advanced,** vá à área **de Segurança,** selecione **use TLS 1.2** e, em seguida, selecione **OK**.
    4. Feche todas as janelas do navegador e reinicie o Google Chrome.

- **Mozilla Firefox**

    1. Abra o Firefox, *escreva cerca de:config* na barra de endereços e, em seguida, prima **Enter**.
    2. Procure o termo *TLS* e, em seguida, selecione a **versão security.tls..max** entrada.
    3. Desacione o valor em **3** para forçar o navegador a utilizar até à versão TLS 1.2 e, em seguida, selecione **OK**.

        >[!NOTE]
        >A versão 60.0 do Firefox suporta o TLS 1.3, para que também possa definir o valor security.tls.tls..max valor para **4**.

    4. Feche todas as janelas do navegador e reinicie o Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações AZure AD - junho de 2018

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em junho de 2018, adicionámos estas 15 novas aplicações com apoio da Federação à galeria de aplicações:

[Skytap](../saas-apps/skytap-tutorial.md), [Settling music](../saas-apps/settlingmusic-tutorial.md), [SAML 1.1 Token enabled LOB App](../saas-apps/saml-tutorial.md), [Supermood](../saas-apps/supermood-tutorial.md), [Autotask](../saas-apps/autotaskendpointbackup-tutorial.md), [Endpoint Backup,](../saas-apps/autotaskendpointbackup-tutorial.md) [Skyhigh Networks](../saas-apps/skyhighnetworks-tutorial.md), Smartway2, [TonicDM,](../saas-apps/tonicdm-tutorial.md) [Moconavi,](../saas-apps/moconavi-tutorial.md) [Zoho One](../saas-apps/zohoone-tutorial.md), [SharePoint on-in- In-in,](../saas-apps/sharepoint-on-premises-tutorial.md) [ForeSee CX Suite,](../saas-apps/foreseecxsuite-tutorial.md) [Vidyard,](../saas-apps/vidyard-tutorial.md) [ChronicX](../saas-apps/chronicx-tutorial.md)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD Password Protection está disponível em pré-visualização pública

**Tipo:** Nova **categoria de serviço** de funcionalidade: **Capacidade de Proteção** de Identidade Produto: Autenticação do Utilizador

Utilize o Azure AD Password Protection para ajudar a eliminar palavras-passe facilmente adivinhadas do seu ambiente. A eliminação destas palavras-passe ajuda a reduzir o risco de compromisso de um tipo de ataque de spray de palavra-passe.

Especificamente, a proteção de senha AD AD Azure ajuda-o:

- Proteja as contas da sua organização tanto no AD AD como no Windows Server Ative Directory (AD).
- Impede os seus utilizadores de usarem palavras-passe numa lista de mais de 500 das palavras-passe mais utilizadas e mais de 1 milhão de variações de substituição de caracteres dessas palavras-passe.
- Administrar a Azure AD Password Protection a partir de uma única localização no portal AD Azure, tanto para a AZure AD como para o Windows Server AD no local.

Para obter mais informações sobre a Proteção de Passwords AD AZure, consulte [Eliminar palavras-passe más na sua organização.](../authentication/concept-password-ban-bad.md)

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Novo modelo de política de acesso condicional "todos os hóspedes" criado durante a criação de uso

**Tipo:** Nova **categoria de serviço** de recurso: Termos de utilização **Capacidade do produto:** Governação

Durante a criação dos seus termos de uso, um novo modelo de política de acesso condicional também é criado para "todos os hóspedes" e "todas as aplicações". Este novo modelo de política aplica a recém-criada ToU, dinamizando o processo de criação e aplicação para os hóspedes.

Para obter mais informações, consulte [a funcionalidade de utilização do Azure Ative Directory Terms of use](../conditional-access/terms-of-use.md).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Novo modelo de política de acesso condicional "personalizado" criado durante a criação de termos de utilização

**Tipo:** Nova **categoria de serviço** de recurso: Termos de utilização **Capacidade do produto:** Governação

Durante a criação dos seus termos de uso, também é criado um novo modelo de política de acesso condicional "personalizado". Este novo modelo de política permite-lhe criar o ToU e, em seguida, ir imediatamente para a lâmina de criação de políticas de acesso condicional, sem necessidade de navegar manualmente através do portal.

Para obter mais informações, consulte [a funcionalidade de utilização do Azure Ative Directory Terms of use](../conditional-access/terms-of-use.md).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-ad-multi-factor-authentication"></a>Novas e abrangentes orientações sobre a implementação da autenticação multi-factor Azure AD

**Tipo:** Nova **categoria de serviço de recurso:** Outra **capacidade do produto:** Proteção & de Segurança de Identidade

Lançamos novas orientações passo a passo sobre como implementar a Azure AD Multi-Factor Authentication (MFA) na sua organização.

Para ver o guia de implementação do MFA, aceda aos [Guias de Implementação de Identidade](./active-directory-deployment-plans.md) no GitHub. Para fornecer feedback sobre os guias de implantação, utilize o [formulário de Feedback do Plano de Implementação](https://aka.ms/deploymentplanfeedback). Se tiver alguma dúvida sobre os guias de implantação, contacte-nos na [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD delegada em funções de gestão de aplicações estão em pré-visualização pública

**Tipo:** Nova **categoria de funcionalidades:** Capacidade do produto de aplicações **empresariais:** Controlo de acesso

Os administradores podem agora delegar tarefas de gestão de aplicações sem atribuir o papel de Administrador Global. As novas funções e capacidades são:

- **Novos papéis de administração standard Azure AD:**

    - **Administrador de candidatura.** Concede a capacidade de gerir todos os aspetos de todas as aplicações, incluindo registo, definições de SSO, atribuição de aplicações e licenciamento, configurações de procuração de aplicações e consentimento (exceto recursos AD AZure).

    - **Administrador de aplicação em nuvem.** Concede todas as capacidades do Administrador de Aplicação, com exceção do proxy da App, porque não fornece acesso no local.

    - **Desenvolvedor de Aplicações.** Concede a capacidade de criar registos de aplicações, mesmo que a opção **de inscrição dos utilizadores** seja desligada.

- **Propriedade (configuração de registo por aplicação e app por empresa, semelhante ao processo de propriedade do grupo:**

    - **Proprietário de Registo de Aplicativos.** Concede a capacidade de gerir todos os aspetos do registo de aplicações de propriedade, incluindo o manifesto da aplicação e adicionando proprietários adicionais.

    - **Proprietário de aplicativo da empresa.** Concede a capacidade de gerir muitos aspetos de aplicações empresariais próprias, incluindo definições de SSO, atribuições de aplicações e consentimento (exceto recursos AD AZure).

Para mais informações sobre a pré-visualização pública, consulte as [funções de gestão de aplicações delegadas da Azure AD em visualização pública!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blog. Para obter mais informações sobre funções e permissões, consulte [atribuindo funções de administrador no Azure Ative Directory](../roles/permissions-reference.md).

---

## <a name="may-2018"></a>Maio de 2018

### <a name="expressroute-support-changes"></a>Alterações de suporte ExpressRoute

**Tipo:** Plano de alteração **Da categoria de Serviço:** Autenticações (Logins) **Capacidade do produto:** Plataforma

O software como uma oferta de serviço, como o Azure Ative Directory (Azure AD) são projetados para funcionar melhor, indo diretamente pela Internet, sem exigir ExpressRoute ou quaisquer outros túneis privados VPN. Por isso, no dia 1 de agosto de **2018,** deixaremos de apoiar os serviços ExpressRoute para a Azure AD usando as comunidades públicas Azure e Azure na Microsoft. Qualquer serviço afetado por esta mudança poderá notar que o tráfego Azure AD se desloca gradualmente do ExpressRoute para a Internet.

Enquanto mudamos o nosso suporte, também sabemos que ainda existem situações em que poderá ter de usar um conjunto dedicado de circuitos para o seu tráfego de autenticação. Por isso, a Azure AD continuará a suportar as restrições de gama IP por inquilino usando ExpressRoute e serviços já na Microsoft, espreitando com a comunidade "Outros serviços Online office 365". Se os seus serviços forem afetados, mas necessitar do ExpressRoute, deve fazer o seguinte:

- **Se estás a olhar para o público do Azure.** Mude-se para a Microsoft e inscreva-se na comunidade **de outros serviços online do Office 365 (12076:5100).** Para obter mais informações sobre como passar do público Azure a espreitar para a Microsoft, veja o [Movimento um espremomento público para](../../expressroute/how-to-move-peering.md) o artigo de observação da Microsoft.

- **Se estiveres a espreitar pela Microsoft.** Inscreva-se na comunidade **de serviço Online 365 (12076:5100)** do Outro Office. Para obter mais informações sobre os requisitos de encaminhamento, consulte a secção de Apoio às [Comunidades BGP](../../expressroute/expressroute-routing.md#bgp) do artigo requisitos de encaminhamento ExpressRoute.

Se tiver de continuar a utilizar circuitos dedicados, terá de falar com a sua equipa da Microsoft Account sobre como obter autorização para utilizar o **serviço Online Other Office 365 (12076:5100).** O painel de revisão gerido pelo MS Office verificará se precisa desses circuitos e certifique-se de que compreende as implicações técnicas de mantê-los. As subscrições não autorizadas que tentem criar filtros de rota para o Office 365 receberão uma mensagem de erro.

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>APIs do Microsoft Graph para cenários administrativos para TOU

**Tipo:** Nova **categoria de serviço** de recurso: Termos de utilização **Capacidade do produto:** Experiência do Desenvolvedor

Adicionámos APIs de Gráficos da Microsoft para o funcionamento da administração dos termos de utilização da AD Azure. É capaz de criar, atualizar, eliminar os termos do objeto de utilização.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Adicione a Azure AD multi-inquilino ponto final como fornecedor de identidade em Azure AD B2C

**Tipo:** Nova **categoria de serviço de recurso:** B2C - Capacidade de Produto de Gestão de Identidade do **Consumidor:** B2B/B2C

Utilizando políticas personalizadas, pode agora adicionar o ponto final comum Azure AD como fornecedor de identidade em Azure AD B2C. Isto permite-lhe ter um único ponto de entrada para todos os utilizadores AD Azure que estão a iniciar sessão nas suas aplicações. Para obter mais informações, consulte [o Azure Ative Directory B2C: Permita que os utilizadores inscrevam-se num fornecedor de identidade Azure AD multi-inquilino utilizando políticas personalizadas](../../active-directory-b2c/identity-provider-azure-ad-multi-tenant.md).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Utilize URLs Internos para aceder a apps de qualquer lugar com a nossa extensão de inscrição de aplicações my apps e o Proxy de aplicação AD Azure

**Tipo:** Nova **categoria de funcionalidades Serviço:** A minha **capacidade de produto apps:** SSO

Os utilizadores podem agora aceder a aplicações através de URLs internos mesmo quando fora da sua rede corporativa, utilizando a Extensão de Inscrição Segura das Minhas Aplicações para AZure AD. Isto funcionará com qualquer aplicação que tenha publicado usando o Azure AD Application Proxy, em qualquer navegador que também tenha a extensão do navegador Access Panel instalada. A funcionalidade de redirecionamento de URL é ativada automaticamente assim que um utilizador inicia sessão na extensão. A extensão está disponível para download no [Microsoft Edge,](https://go.microsoft.com/fwlink/?linkid=845176) [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)e [Firefox.](https://go.microsoft.com/fwlink/?linkid=866366)

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Ative Directory - Dados na Europa para clientes europeus

**Tipo:** Nova **categoria de funcionalidades Serviço:** Outra **capacidade do produto:** GoLocal

Os clientes na Europa exigem que os seus dados permaneçam na Europa e não sejam replicados fora dos centros de dados europeus para cumprir a privacidade e as leis europeias. Este [artigo](./active-directory-data-storage-eu.md) fornece os detalhes específicos sobre as informações de identidade que serão armazenadas na Europa e também fornece detalhes sobre informações que serão armazenadas fora dos centros de dados europeus.

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Novos utilizadores que aprovisionam integrações de aplicações saaS - maio de 2018

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto de fornecimento de **aplicativos: Integração** de terceiros

O Azure AD permite automatizar a criação, manutenção e remoção de identidades de utilizadores em aplicações SaaS como Dropbox, Salesforce, ServiceNow, entre outras. Para maio de 2018, adicionámos suporte ao fornecimento de utilizadores para as seguintes aplicações na galeria de aplicações AZure AD:

- [BlueJeans](../saas-apps/bluejeans-provisioning-tutorial.md)

- [Cornerstone OnDemand](../saas-apps/cornerstone-ondemand-provisioning-tutorial.md)

- [Zendesk](../saas-apps/zendesk-provisioning-tutorial.md)

Para obter uma lista de todas as aplicações que suportam o provisionamento do utilizador na galeria AZure AD, consulte [https://aka.ms/appstutorial](../saas-apps/tutorial-list.md) .

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Azure AD avaliações de acesso a grupos e acesso a apps agora fornece avaliações recorrentes

**Tipo:** Nova **categoria de serviço de recurso:** Acesso Revê **capacidade de produto:** Governação

A revisão de acesso a grupos e aplicações está agora geralmente disponível como parte do Azure AD Premium P2.  Os administradores poderão configurar revisões de acesso de membros do grupo e atribuições de candidaturas para se repetirem automaticamente a intervalos regulares, como mensal ou trimestralmente.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Os registos de atividade azure AD (logins e auditoria) estão agora disponíveis através do MS Graph

**Tipo:** Nova **categoria de serviço** de recurso: Capacidade do produto de reporte: Monitorização & Reporte 

Os registos de atividade azure AD, que inclui logins e registos de auditoria, estão agora disponíveis através da API do Gráfico microsoft. Expusemos dois pontos finais através da Microsoft Graph API para aceder a estes registos. Consulte os [nossos documentos](../reports-monitoring/concept-reporting-api.md) para acesso programático às APIs de reportagem da Azure AD para começar.

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Melhorias na experiência de resgate B2B e deixar um org

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto B2B: B2B/B2C 

**Mesmo a tempo da redenção:** Uma vez que partilhe um recurso com um utilizador convidado usando a API B2B – não precisa de enviar um e-mail de convite especial. Na maioria dos casos, o utilizador convidado pode aceder ao recurso e será levado através da experiência de resgate mesmo a tempo. Sem mais impacto devido a e-mails perdidos. Chega de perguntar aos utilizadores convidados "Clicou no link de resgate que o sistema lhe enviou?". Isto significa que assim que o SPO utilizar o gestor de convites – os anexos nublados podem ter o mesmo URL canónico para todos os utilizadores – internos e externos – em qualquer estado de redenção.

**Experiência moderna de redenção:** Chega de página de aterragem de redenção de ecrã dividido. Os utilizadores verão uma experiência de consentimento moderna com a declaração de privacidade da organização convidativa, tal como fazem para aplicações de terceiros.

**Os utilizadores convidados podem deixar o org:** Uma vez terminada a relação de um utilizador com um org, podem auto-servir-se deixando a organização. Chega de chamar o administrador do org convidativo para "ser removido", sem mais bilhetes de apoio.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações AZure AD - maio 2018

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em maio de 2018, adicionámos estas 18 novas aplicações com apoio da Federação à nossa galeria de aplicações:

[PrêmioSpr,](../saas-apps/awardspring-tutorial.md)Infogix Data3Sixty Govern, [Yodeck,](../saas-apps/infogix-tutorial.md) [Jamf Pro,](../saas-apps/jamfprosamlconnector-tutorial.md) [KnowledgeOwl,](../saas-apps/knowledgeowl-tutorial.md) [Envi MMIS,](../saas-apps/envimmis-tutorial.md) [LaunchDarkly,](../saas-apps/launchdarkly-tutorial.md) [Adobe Captivate Prime,](../saas-apps/adobecaptivateprime-tutorial.md) [Montage Online,](../saas-apps/montageonline-tutorial.md)[まなびポケット,](../saas-apps/manabipocket-tutorial.md)OpenReel, [Arc Publishing - SSO,](../saas-apps/arc-tutorial.md) [PlanGrid,](../saas-apps/plangrid-tutorial.md) [iWellnessNow,](../saas-apps/iwellnessnow-tutorial.md) [Proxyclick,](../saas-apps/proxyclick-tutorial.md) [Riskware](../saas-apps/riskware-tutorial.md), [Flock](../saas-apps/flock-tutorial.md), [Reviewsnap](../saas-apps/reviewsnap-tutorial.md)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md)

Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Novos guias de implantação passo a passo para o Azure Ative Directory

**Tipo:** Nova **categoria de serviço de recurso:** Outra **capacidade do produto:** Diretório

Novas orientações passo a passo sobre como implantar o Azure Ative Directory (Azure AD), incluindo reset de senha de autosserviço (SSPR), único sign-on (SSO), Acesso Condicional (CA), procuração de aplicativos, fornecimento de utilizador, Serviços de Federação de Diretórios Ativos (ADFS) para Autenticação Pass-through (PTA) e ADFS para password hash sync (PHS).

Para ver os guias de implantação, aceda aos [Guias de Implementação de Identidade](./active-directory-deployment-plans.md) repo no GitHub. Para fornecer feedback sobre os guias de implantação, utilize o [formulário de Feedback do Plano de Implementação](https://aka.ms/deploymentplanfeedback). Se tiver alguma dúvida sobre os guias de implantação, contacte-nos na [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Pesquisa de aplicações empresariais - Carregar mais apps

**Tipo:** Nova **categoria de funcionalidades:** Capacidade do produto de aplicações **empresariais:** SSO

Tem dificuldade em encontrar as suas aplicações/ diretores de serviço? Adicionámos a capacidade de carregar mais aplicações nas aplicações da sua empresa, todas as listas de aplicações. Por padrão, mostramos 20 aplicações. Já pode clicar, **carregar mais** para ver aplicações adicionais.

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>O lançamento de maio do AADConnect contém uma pré-visualização pública da integração com o PingFederate, importantes atualizações de segurança, muitas correções de bugs e novas novas grandes ferramentas de resolução de problemas.

**Tipo:** Alteração da **categoria de serviço de funcionalidade:** Capacidade de produto AD **Connect:** Gestão do ciclo de vida da identidade

O lançamento de maio do AADConnect contém uma pré-visualização pública da integração com o PingFederate, importantes atualizações de segurança, muitas correções de bugs e novas novas grandes ferramentas de resolução de problemas. Pode encontrar as notas de lançamento [aqui.](../hybrid/reference-connect-version-history.md)

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Comentários de acesso Azure AD: aplicam-se automaticamente

**Tipo:** Alteração **da categoria de serviço de recurso:** Capacidade de produto de comentários de **acesso:** Governação

As análises de acesso a grupos e aplicações estão agora geralmente disponíveis como parte do Azure AD Premium P2. Um administrador pode configurar para aplicar automaticamente as alterações do revisor a esse grupo ou app à medida que a revisão de acesso estiver concluída. O administrador também pode especificar o que acontece ao acesso continuado do utilizador se os revisores não responderem, removerem o acesso, manterem o acesso ou aceitarem recomendações do sistema.

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Os tokens de ID já não podem ser devolvidos usando a consulta response_mode para novas aplicações.

**Tipo:** Alteração **da categoria de serviço** de funcionalidade: Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

As aplicações criadas no dia 25 de abril de 2018 deixarão de poder solicitar um **id_token** utilizando a **consulta** response_mode.  Isto traz a Azure AD em linha com as especificações OIDC e ajuda a reduzir a superfície de ataque das suas aplicações.  As aplicações criadas antes do dia 25 de abril de 2018 não estão impedidas de utilizar a **consulta** response_mode com um response_type de **id_token**.  O erro devolvido, ao solicitar uma id_token à AZure AD, é **AADSTS70007: "consulta" não é um valor suportado de "response_mode" ao solicitar um token.**

O **fragmento** e **form_post** response_modes continuam a funcionar - ao criar novos objetos de aplicação (por exemplo, para utilização de App Proxy), garantir o uso de um destes response_modes antes de criarem uma nova aplicação.

---

## <a name="april-2018"></a>Abril de 2018

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C Access Token são GA

**Tipo:** Nova **categoria de serviço de recurso:** B2C - Capacidade de Produto de Gestão de Identidade do **Consumidor:** B2B/B2C

Agora pode aceder às APIs da Web protegidas pelo Azure AD B2C utilizando fichas de acesso. A funcionalidade está a passar da pré-visualização pública para a AG. A experiência da UI para configurar aplicações Azure AD B2C e APIs web foi melhorada, e outras melhorias menores foram feitas.

Para mais informações, consulte [Azure AD B2C: Solicitando fichas de acesso.](../../active-directory-b2c/access-tokens.md)

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Teste uma configuração única de sinal para aplicações baseadas em SAML

**Tipo:** Nova **categoria de funcionalidades:** Capacidade do produto de aplicações **empresariais:** SSO

Ao configurar aplicações SSO baseadas em SAML, é capaz de testar a integração na página de configuração. Se encontrar um erro durante a entrada, pode fornecer o erro na experiência de teste e a Azure AD fornece-lhe medidas de resolução para resolver o problema específico.

Para obter mais informações, veja:

- [Configurar o início de sessão único em aplicações que não fazem parte da galeria de aplicações do Azure Active Directory](../manage-apps/view-applications-portal.md)
- [Como depurar o único sign-on baseado em SAML para aplicações no Azure Ative Directory](../manage-apps/debug-saml-sso-issues.md)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Os termos de utilização da AD AZure têm agora por relatório de utilizador

**Tipo:** Nova **categoria de serviço** de recurso: Termos de utilização **Capacidade do produto:** Conformidade

Os administradores podem agora selecionar uma determinada ToU e ver todos os utilizadores que tenham consentido com essa ToU e qual a data/hora que ocorreu.

Para obter mais informações, consulte a [funcionalidade de utilização dos termos de utilização Azure AD](../conditional-access/terms-of-use.md).

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: IP arriscado para proteção de bloqueio de extranet AD FS

**Tipo:** Nova **categoria de serviço de recurso:** Outra **capacidade do produto:** Monitorização & Reporte

A Connect Health suporta agora a capacidade de detetar endereços IP que excedam um limiar de logins U/P falhados numa base horária ou diária. As capacidades fornecidas por esta funcionalidade são:

- Relatório abrangente que mostra o endereço IP e o número de logins falhados gerados numa base horária/diária com limiar personalizável.
- Alertas baseados em e-mail que mostram quando um endereço IP específico excedeu o limiar de logins U/P falhados numa base horária/diária.
- Uma opção de descarregamento para fazer uma análise detalhada dos dados

Para obter mais informações, consulte [o Relatório IP Arriscado.](../hybrid/how-to-connect-health-adfs.md)

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>Config de app fácil com arquivo de metadados ou URL

**Tipo:** Nova **categoria de funcionalidades:** Capacidade do produto de aplicações **empresariais:** SSO

Na página de aplicações da Enterprise, os administradores podem carregar um ficheiro de metadados SAML para configurar o sign-in baseado em SAML para a Azure AD Gallery e a aplicação Non-Gallery.

Além disso, pode utilizar o URL de metadados da federação de aplicações AD Azure para configurar o SSO com a aplicação direcionada.

Para obter mais informações, consulte [configurar o acesso único a aplicações que não constam da galeria de candidaturas do Azure Ative Directory](../manage-apps/view-applications-portal.md).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Termos de utilização Azure AD agora geralmente disponíveis

**Tipo:** Nova **categoria de serviço** de recurso: Termos de utilização **Capacidade do produto:** Conformidade


Os termos de utilização da AD Azure passaram da pré-visualização pública para geralmente disponível.

Para obter mais informações, consulte a [funcionalidade de utilização dos termos de utilização Azure AD](../conditional-access/terms-of-use.md).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Permitir ou bloquear convites para utilizadores B2B de organizações específicas

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto B2B: B2B/B2C 


Agora pode especificar quais as organizações parceiras com as quais pretende partilhar e colaborar na Colaboração Azure AD B2B. Para isso, pode optar por criar uma lista de domínios específicos de permitir ou negar. Quando um domínio é bloqueado usando estas capacidades, os funcionários não podem mais enviar convites para pessoas nesse domínio.

Isto ajuda-o a controlar o acesso aos seus recursos, ao mesmo tempo que permite uma experiência suave para utilizadores aprovados.

Esta funcionalidade de Colaboração B2B está disponível para todos os clientes do Azure Ative Directory e pode ser usada em conjunto com funcionalidades Azure AD Premium, como Acesso Condicional e proteção de identidade para um maior controlo granular de quando e como os utilizadores externos de negócios assinam e têm acesso.

Para obter mais informações, consulte [Permitir ou bloquear convites a utilizadores B2B de organizações específicas.](../external-identities/allow-deny-list.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novas aplicações federadas disponíveis na galeria de aplicações AZure AD

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em abril de 2018, adicionámos estas 13 novas aplicações com apoio da Federação à nossa galeria de aplicações:

Critério HCM, [FiscalNote](../saas-apps/fiscalnote-tutorial.md), [Servidor Secreto (On-Premis)](../saas-apps/secretserver-on-premises-tutorial.md), Sinal [Dinâmico,](../saas-apps/dynamicsignal-tutorial.md) [MindWireless](../saas-apps/mindwireless-tutorial.md), [OrgChart Now](../saas-apps/orgchartnow-tutorial.md), [Ziflow,](../saas-apps/ziflow-tutorial.md) [AppNeta Performance Monitor,](../saas-apps/appneta-tutorial.md) [Elium,](../saas-apps/elium-tutorial.md) [Fluxx Labs,](../saas-apps/fluxxlabs-tutorial.md) [Cisco Cloud,](../saas-apps/ciscocloud-tutorial.md)Prateleira, [SafetyNet](../saas-apps/safetynet-tutorial.md)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md)

Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Grant B2B utilizadores em Azure AD acesso às suas aplicações no local (pré-visualização pública)

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto B2B: B2B/B2C 

Como uma organização que utiliza capacidades de colaboração B2B (Azure AD) para convidar utilizadores convidados de organizações parceiras para o seu AD Azure, pode agora fornecer a estes utilizadores B2B acesso a aplicações no local. Estas aplicações no local podem utilizar a autenticação baseada em SAML ou a Autenticação Integrada do Windows (IWA) com a delegação restrita kerberos (KCD).

Para mais informações, consulte [os utilizadores do Grant B2B no Azure AD acesso às suas aplicações no local.](../external-identities/hybrid-cloud-to-on-premises.md)

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Obtenha tutoriais de integração SSO do Azure Marketplace

**Tipo:** Alteração **da categoria de serviço de recurso:** Outra **capacidade do produto:** Integração de 3ª Parte

Se uma aplicação listada no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) suporta um único sinal de saúde baseado em SAML, clicar **agora fornece-lhe** o tutorial de integração associado a essa aplicação.

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Desempenho mais rápido do fornecimento automático de utilizadores Azure AD às aplicações saaS

**Tipo:** Categoria de Serviço de Funcionalidade **Alterada:** Capacidade do Produto de Provisionamento de Aplicações: Integração de 3ª Parte 

Anteriormente, os clientes que utilizam o utilizador do Azure Ative Directory que fornece conectores para aplicações SaaS (por exemplo Salesforce, ServiceNow e Box) poderiam experimentar um desempenho lento se os seus inquilinos AD Azure contivessem mais de 100.000 utilizadores e grupos combinados, e estavam a usar atribuições de utilizador e grupo para determinar quais os utilizadores que deveriam ser a provisionados.

Em 2 de abril de 2018, foram implementadas melhorias significativas no desempenho para o serviço de fornecimento de AD Azure que reduzem consideravelmente o tempo necessário para realizar sincronizações iniciais entre o Azure Ative Directory e as aplicações saaS visadas.

Como resultado, muitos clientes que tiveram sincronizações iniciais para apps que demoraram muitos dias ou nunca concluídas, estão agora a completar-se numa questão de minutos ou horas.

Para mais informações, consulte [o que acontece durante o provisionamento?](../..//active-directory/app-provisioning/how-provisioning-works.md)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Palavra-passe de autosserviço reiniciada a partir do ecrã de bloqueio do Windows 10 para máquinas híbridas Azure AD

**Tipo:** Categoria **de serviço** de funcionalidade alterada: Autosserviço Password Redefinir **capacidade de produto:** Autenticação do utilizador

Atualizámos a funcionalidade SSPR do Windows 10 para incluir suporte para máquinas que são híbridas Azure AD. Esta funcionalidade encontra-se disponível no Windows 10 RS4, permitindo aos utilizadores redefinirem a sua palavra-passe a partir do ecrã de bloqueio de uma máquina Windows 10. Os utilizadores que estejam ativados e registados para redefinição da palavra-passe de autosserviço podem utilizar esta funcionalidade.

Para obter mais informações, consulte [a palavra-passe AD do Azure a partir do ecrã de login](../authentication/howto-sspr-windows.md).

---

## <a name="march-2018"></a>Março de 2018

### <a name="certificate-expire-notification"></a>Notificação de caducidade do certificado

**Tipo:** **Categoria serviço fixo:** Capacidade do produto de aplicações **empresariais:** SSO

A Azure AD envia uma notificação quando um certificado para um pedido de galeria ou não-galeria está prestes a expirar.

Alguns utilizadores não receberam notificações para aplicações empresariais configuradas para um único sign-on baseado em SAML. Esta questão foi resolvida. A Azure AD envia notificação para certificados que expiram em 7, 30 e 60 dias. Você pode ver este evento nos registos de auditoria.

Para obter mais informações, veja:

- [Gerir certificados para inscrição única federada no Diretório Ativo Azure](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
- [Relatório de atividade de auditoria no portal do Azure Active Directory](../reports-monitoring/concept-audit-logs.md)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Fornecedores de identidade Twitter e GitHub em Azure AD B2C

**Tipo:** Nova **categoria de serviço de recurso:** B2C - Capacidade de Produto de Gestão de Identidade do **Consumidor:** B2B/B2C

Pode agora adicionar o Twitter ou o GitHub como fornecedor de identidade no Azure AD B2C. O Twitter está a passar da pré-estreia pública para a AG. GitHub vai ser lançado em pré-visualização pública.

Para mais informações, veja [o que é a colaboração Azure AD B2B?](../external-identities/what-is-b2b.md)

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Restringir o acesso ao navegador usando o Navegador Gerido Intune com acesso condicional baseado em aplicações AZure AD para iOS e Android

**Tipo:** Nova **categoria de serviço** de recurso: Capacidade de acesso condicional **do produto:** Proteção & de Segurança de Identidade

**Agora, em exibição pública!**

**Intune Managed Browser SSO:** Os seus colaboradores podem utilizar um único login em clientes nativos (como o Microsoft Outlook) e o Intune Managed Browser para todas as aplicações ligadas ao AD Azure.

Suporte de acesso condicional gerido **intune:** Agora pode exigir que os colaboradores utilizem o navegador Intune Managed usando políticas de Acesso Condicional baseadas em aplicações.

Leia mais sobre isso na nossa [publicação de blog.](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/)

Para obter mais informações, veja:

- [Acesso Condicional baseado em aplicações de configuração](../conditional-access/app-based-conditional-access.md)

- [Configurar políticas de navegador geridas](/mem/intune/apps/manage-microsoft-edge)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Cmdlets de Proxy de aplicativos no módulo PowerShell GA

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade de produto Proxy **app:** Controle de Acesso

Suporte para cmdlets de procuração de aplicação está agora no Módulo PowerShell GA! Isto requer que permaneça atualizado nos módulos PowerShell - se ficar mais de um ano atrasado, alguns cmdlets podem deixar de funcionar.

Para mais informações, consulte [a AzureAD.](/powershell/module/Azuread/)

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>O Office 365 clientes nativos é apoiado pela Seamless SSO usando um protocolo não interativo

**Tipo:** Nova **categoria de serviço** de funcionalidade: Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

O utilizador que utiliza o Office 365 clientes nativos (versão 16.0.8730.xxxx e acima) obtém uma experiência de login silencioso utilizando o Seamless SSO. Este suporte é fornecido pela adição de um protocolo não interativo (WS-Trust) à Azure AD.

Para mais informações, consulte [como é que se inscreve num cliente nativo com sSO sem emenda?](../hybrid/how-to-connect-sso-how-it-works.md#how-does-sign-in-on-a-native-client-with-seamless-sso-work)

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Os utilizadores obtêm uma experiência de inscrição silenciosa, com Seamless SSO, se uma aplicação enviar pedidos de inscrição para os pontos finais do Azure AD

**Tipo:** Nova **categoria de serviço** de funcionalidade: Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

Os utilizadores obtêm uma experiência de inscrição silenciosa, com Seamless SSO, se uma aplicação (por `https://contoso.sharepoint.com` exemplo, ) enviar pedidos de inscrição para os pontos finais do Azure AD - isto é, `https://login.microsoftonline.com/contoso.com/<..>` ou - em vez do ponto final comum da `https://login.microsoftonline.com/<tenant_ID>/<..>` AD AD Azure `https://login.microsoftonline.com/common/<...>` ().

Para obter mais informações, consulte [o Azure Ative Directory Seamless Single Sign-On](../hybrid/how-to-connect-sso.md).

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Precisa adicionar apenas um URL AD Azure, em vez de dois URLs anteriormente, às definições da zona intranet dos utilizadores para lançar SSO sem emenda

**Tipo:** Nova **categoria de serviço** de funcionalidade: Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

Para lançar SSO sem emenda aos seus utilizadores, é necessário adicionar apenas um URL AD AZure às definições da zona Intranet dos utilizadores utilizando a política de grupo no Ative Directory: `https://autologon.microsoftazuread-sso.com` . Anteriormente, os clientes eram obrigados a adicionar dois URLs.

Para obter mais informações, consulte [o Azure Ative Directory Seamless Single Sign-On](../hybrid/how-to-connect-sso.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações AZure AD

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em março de 2018, adicionámos estas 15 novas aplicações com apoio da Federação à nossa galeria de aplicações:

[Boxcryptor](../saas-apps/boxcryptor-tutorial.md), [CylancePROTECT,](../saas-apps/cylanceprotect-tutorial.md)Wrike, [SignalFx,](../saas-apps/signalfx-tutorial.md)Assistant by FirstAgenda, [YardiOne](../saas-apps/yardione-tutorial.md), Vtiger CRM, inwink, [Amplitude,](../saas-apps/amplitude-tutorial.md) [Spacio,](../saas-apps/spacio-tutorial.md) [ContractWorks,](../saas-apps/contractworks-tutorial.md) [Bersin,](../saas-apps/bersin-tutorial.md) [Mercell,](../saas-apps/mercell-tutorial.md) [Trisotech Digital Enterprise Server,](../saas-apps/trisotechdigitalenterpriseserver-tutorial.md) [Qumu Cloud.](../saas-apps/qumucloud-tutorial.md)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md)

Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="pim-for-azure-resources-is-generally-available"></a>PIM para Recursos Azure está geralmente disponível

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade privilegiada de produto de gestão de **identidade:** Gestão de Identidade privilegiada

Se estiver a utilizar a Azure AD Privileged Identity Management para funções de diretório, pode agora utilizar as capacidades de acesso e atribuição de recursos da PIM para funções de Recursos Azure, tais como Subscrições, Grupos de Recursos, Máquinas Virtuais e qualquer outro recurso suportado pelo Azure Resource Manager. Imponha a autenticação multi-factor ao ativar funções Just-In-Time e agende as ativações em coordenação com as janelas de alteração aprovadas. Além disso, esta versão adiciona melhorias não disponíveis durante a pré-visualização pública, incluindo uma UI atualizada, fluxos de trabalho de aprovação, e a capacidade de estender funções expirando em breve e renovar funções caducadas.

Para mais informações, consulte [PIM para recursos Azure (Preview)](../privileged-identity-management/azure-pim-resource-rbac.md)

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Adicionar Reclamações Opcionais aos seus tokens de apps (pré-visualização pública)

**Tipo:** Nova **categoria de serviço** de funcionalidade: Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

A sua aplicação Azure AD pode agora solicitar reclamações personalizadas ou opcionais em fichas JWTs ou SAML.  Trata-se de reclamações sobre o utilizador ou inquilino que não estão incluídos por padrão no token, devido a restrições de tamanho ou aplicabilidade.  Isto encontra-se atualmente em pré-visualização pública para aplicações AD Azure nos pontos finais v1.0 e v2.0.  Consulte a documentação para obter informações sobre quais as reclamações que podem ser adicionadas e como editar o manifesto da sua candidatura para as solicitar.

Para mais informações, consulte [as reclamações opcionais em Azure AD](../develop/active-directory-optional-claims.md).

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD suporta PKCE para fluxos OAuth mais seguros

**Tipo:** Nova **categoria de serviço** de funcionalidade: Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

Os docs Azure AD foram atualizados para notar suporte para o PKCE, que permite uma comunicação mais segura durante o fluxo de concessão do Código de Autorização OAuth 2.0.  Tanto o S256 como o texto simples code_challenges são suportados nos pontos finais v1.0 e v2.0.

Para mais informações, consulte [Solicitar um código de autorização.](../develop/v2-oauth2-auth-code-flow.md#request-an-authorization-code)

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Suporte para a provisionamento de todos os valores de atributos do utilizador disponíveis na API Get_Workers do Trabalho

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto de fornecimento de **aplicativos: Integração** de terceiros

A pré-visualização pública do fornecimento de entrada de Workday a Ative Directory e Azure AD suporta agora a capacidade de extrair e a provisionar todos os valores de atributos disponíveis no Workday Get_Workers API. Isto adiciona suportes para centenas de atributos padrão e personalizados adicionais para além dos enviados com a versão inicial do conector de provisionamento de entrada workday.

Para obter mais informações, consulte: [Personalizar a lista de atributos do utilizador workday](../saas-apps/workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Mudança da adesão ao grupo de dinâmica para estática, e vice-versa

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do Produto de Gestão de **Grupo:** Colaboração

É possível alterar a forma como a adesão é gerida num grupo. Isto é útil quando pretende manter o mesmo nome de grupo e ID no sistema, pelo que quaisquer referências existentes ao grupo ainda são válidas; a criação de um novo grupo exigiria a atualização dessas referências.
Atualizámos o centro de administração Admin Azure para suportar esta funcionalidade. Agora, os clientes podem converter grupos existentes de adesão dinâmica a membros atribuídos e vice-versa. Os cmdlets PowerShell existentes também estão disponíveis.

Para mais informações, consulte [as regras de adesão dinâmica para grupos no Azure Ative Directory](../enterprise-users/groups-dynamic-membership.md)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Melhor comportamento de sinalização com SSO sem emenda

**Tipo:** Alteração **da categoria de serviço** de funcionalidade: Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

Anteriormente, mesmo que os utilizadores assinassem explicitamente uma aplicação protegida pela Azure AD, seriam automaticamente re-contratados de volta na utilização do Seamless SSO se estivessem a tentar aceder a uma aplicação AD Azure novamente dentro da sua corpnet a partir dos seus dispositivos de ligação de domínio. Com esta mudança, a assinatura é apoiada.  Isto permite que os utilizadores escolham a mesma conta Azure AD para voltar a entrar, em vez de serem automaticamente assinados na utilização de SSO sem emenda.

Para mais informações, consulte [O Diretório Ativo Sem Emenda Sem Emenda](../hybrid/how-to-connect-sso.md)

---

### <a name="application-proxy-connector-version-154020-released"></a>Aplicação Proxy Connector Versão 1.5.402.0 Lançado

**Tipo:** Alteração **da categoria de serviço de recurso:** Capacidade de produto proxy **app:** Proteção & de Segurança de Identidade

Esta versão do conector está a ser lançada gradualmente até novembro. Esta nova versão do conector inclui as seguintes alterações:

- O conector agora define cookies de nível de domínio em vez do nível de subdomínio. Isto garante uma experiência SSO mais suave e evita instruções de autenticação redundante.
- Apoio a pedidos de codificação em pedaços
- Melhor monitorização da saúde do conector
- Várias correções de bugs e melhorias de estabilidade

Para obter mais informações, consulte [os conectores Proxy da aplicação AD Azure](../manage-apps/application-proxy-connectors.md).

---

## <a name="february-2018"></a>Fevereiro de 2018

### <a name="improved-navigation-for-managing-users-and-groups"></a>Melhor navegação para gestão de utilizadores e grupos

**Tipo:** Plano de mudança **categoria serviço:** Capacidade do Produto de Gestão de Diretório: Diretório 

A experiência de navegação para gerir utilizadores e grupos foi simplificada. Pode agora navegar a partir da visão geral do diretório diretamente para a lista de todos os utilizadores, com acesso mais fácil à lista de utilizadores eliminados. Também pode navegar a partir da visão geral do diretório diretamente para a lista de todos os grupos, com acesso mais fácil às definições de gestão do grupo. E também a partir da página geral do diretório, você pode pesquisar por um utilizador, grupo, aplicação empresarial ou registo de aplicações.

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Disponibilidade de insusimentos e relatórios de auditoria no Microsoft Azure operados pela 21Vianet (Azure China 21Vianet)

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade do produto Azure **Stack:** Monitorização & Reporte

Os relatórios de registo de atividade azure AD estão agora disponíveis no Microsoft Azure operado por 21 Instânciasvianet (Azure China 21Vianet). Os seguintes registos estão incluídos:

- **Registos de atividades de login**  - Inclui todos os registos de login associados ao seu inquilino.

- **Registos de auditoria de senha de autosserviço** - Inclui todos os registos de auditoria SSPR.

- **Registos de Auditoria de Gestão de Diretórios** - Inclui todos os registos de auditoria relacionados com a gestão de diretórios, como gestão de utilizadores, Gestão de Aplicações, entre outros.

Com estes registos, podes obter informações sobre como o teu ambiente está a correr. Os dados fornecidos permite-lhe:

- Determine como as suas aplicações e serviços são utilizados pelos seus utilizadores.

- Problemas de resolução de problemas que impedem os seus utilizadores de fazer o seu trabalho.

Para obter mais informações sobre como utilizar estes relatórios, consulte [o Azure Ative Directory reportando](../reports-monitoring/overview-reports.md).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Use o papel de "Report Reader" (função não administrador) para ver relatórios de atividade da AD Azure

**Tipo:** Nova **categoria de serviço** de recurso: Capacidade do produto de reporte: Monitorização & Reporte 

Como parte do feedback dos clientes para permitir que as funções não administradas tenham acesso aos registos de atividades da Azure AD, permitimos aos utilizadores que estão na função de "Report Reader" acederem à atividade de Login e Auditoria dentro do portal Azure, bem como utilizando a API do Microsoft Graph.

Para obter mais informações, como utilizar estes relatórios, consulte [o Azure Ative Directory reportando](../reports-monitoring/overview-reports.md).

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Reclamação do EmployeeID disponível como atributo de utilizador e identificador de utilizador

**Tipo:** Nova **categoria de funcionalidades:** Capacidade do produto de aplicações **empresariais:** SSO

Pode configurar **o EmployeeID** como o identificador do Utilizador e o atributo utilizador para os utilizadores membros e os hóspedes B2B em aplicações de assinatura baseadas em SAML a partir da UI da aplicação Enterprise.

Para obter mais informações, consulte [personalizar reclamações emitidas no token SAML para aplicações empresariais no Azure Ative Directory](../develop/active-directory-saml-claims-customization.md).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Gestão simplificada de aplicações utilizando wildcards em Proxy de aplicação AD Azure

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do Produto Proxy **app:** Autenticação do utilizador

Para facilitar a implementação da aplicação e reduzir as suas despesas administrativas, apoiamos agora a capacidade de publicar aplicações usando wildcards. Para publicar uma aplicação wildcard, pode seguir o fluxo de publicação de aplicações padrão, mas use um wildcard nos URLs internos e externos.

Para mais informações, consulte as aplicações Wildcard no proxy de aplicação do [Azure Ative Directory](../manage-apps/application-proxy-wildcard.md)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Novos cmdlets para apoiar a configuração do Application Proxy

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade de Produto Proxy **app:** Plataforma

A mais recente versão do módulo de pré-visualização powerShell AzureAD contém novos cmdlets que permitem aos clientes configurar aplicações proxy de aplicação usando o PowerShell.

Os novos cmdlets são:

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Novos cmdlets para apoiar a configuração de grupos

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade de Produto Proxy **app:** Plataforma

A mais recente versão do módulo AzureAD PowerShell contém cmdlets para gerir grupos em Azure AD. Estes cmdlets estavam anteriormente disponíveis no módulo AzureADPreview e são agora adicionados ao módulo AzureAD

Os cmdlets do Grupo que agora são lançados para Disponibilidade Geral são:

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

---

### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Um novo lançamento do Azure AD Connect está disponível

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade de produto AD **Sync:** Plataforma

Azure AD Connect é a ferramenta preferida para sincronizar dados entre Azure AD e em fontes de dados do Windows Server, incluindo o Windows Server Ative Directory e o LDAP.

>[!Important]
>Esta construção introduz alterações de regras de esquema e sincronização. O Serviço de Sincronização AZURE AD Connect Ativamente aciona uma extensão completa de medidas de importação e sincronização completa após uma atualização. Para obter informações sobre como alterar este comportamento, consulte [Como adiar a sincronização completa após a atualização](../hybrid/how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).

Esta versão tem as seguintes atualizações e alterações:

**Questões fixas**

- Fixar a janela de tempo nas tarefas de fundo para a página de filtragem de partição ao mudar para a página seguinte.

- Corrigiu um bug que causou a violação do Acesso durante a ação personalizada ConfigDB.

- Corrigiu um bug para recuperar do tempo limite de ligação do sql.

- Fixo um bug onde os certificados com wildcards SAN falham a verificação pré-req.

- Corrigiu um inseto que causa miiserver.exe acidente durante a exportação do conector AZure AD.

- Corrigi um bug onde uma má tentativa de senha sessão em DC durante a execução fez com que o assistente de ligação AZure para alterar a configuração

**Novas funcionalidades e melhorias**

- Telemetria de aplicação - Os administradores podem alternar esta classe de dados ligados/desligados.

- Dados de Saúde Azure AD - Os administradores devem visitar o portal de saúde para controlar as suas definições de saúde. Uma vez alterada a política de serviços, os agentes vão lê-la e aplicá-la.

- Adicionou ações de configuração de writeback do dispositivo e uma barra de progresso para a inicialização da página.

- Diagnóstico geral melhorado com relatório HTML e recolha completa de dados num relatório ZIP-Text/HTML.

- Melhor fiabilidade da atualização automática e adicionação de telemetria adicional para garantir que a saúde do servidor pode ser determinada.

- Restringir as permissões disponíveis para contas privilegiadas na conta AD Connector. Para novas instalações, o assistente restringe as permissões que as contas privilegiadas têm na conta MSOL após a criação da conta MSOL. As alterações afetam as instalações expressas e as instalações personalizadas com a conta Auto-Create.

- Alterou o instalador para não necessitar de privilégio SA na instalação limpa do AADConnect.

- Novo utilitário para resolver problemas de sincronização para um objeto específico. Atualmente, o serviço de utilidade verifica as seguintes coisas:

    - Desfasamento entre o objeto de utilizador sincronizado e a conta de utilizador no Azure AD Tenant.

    - Se o objeto for filtrado da sincronização devido à filtragem do domínio

    - Se o objeto for filtrado da sincronização devido à filtragem da unidade organizacional (OU)

- Novo utilitário para sincronizar o hash de palavra-passe atual armazenado no Ative Directory no local para uma conta de utilizador específica. O utilitário não requer uma alteração de senha.

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplicações que suportam políticas de proteção de aplicações Intune adicionadas para uso com acesso condicional baseado em aplicações AZure

**Tipo:** Alteração **da categoria de serviço de recurso:** Capacidade de acesso condicional **do produto:** Proteção & de segurança de identidade

Adicionámos mais aplicações que suportam o Acesso Condicional baseado em aplicações. Agora, você pode ter acesso ao Office 365 e outras aplicações de nuvem AZure conectadas com aD usando estas aplicações de clientes aprovadas.

As seguintes candidaturas serão adicionadas até ao final de fevereiro:

- Microsoft Power BI

- Microsoft Launcher

- Faturação da Microsoft

Para obter mais informações, veja:

- [Requisito de aplicação de cliente aprovado](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Acesso Condicional baseado em aplicativos Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Termos de utilização atualizado para experiência móvel

**Tipo:** Alteração da categoria de serviço de **recurso:** Termos de utilização **Capacidade do produto:** Conformidade

Quando os termos de utilização são apresentados, pode agora clicar em **ter dificuldade em visualizar? Clique aqui.** Clicar neste link abre os termos de utilização de forma nativa no seu dispositivo. Independentemente do tamanho do tipo de letra no documento ou do tamanho do ecrã do dispositivo, pode ampliar e ler o documento conforme necessário.

---

## <a name="january-2018"></a>Janeiro de 2018

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações AZure AD

**Tipo:** Nova **categoria de serviço de funcionalidade:** Capacidade do produto de aplicações **empresariais:** Integração de 3ª Parte

Em janeiro de 2018, foram adicionadas na galeria de aplicações as seguintes novas aplicações com suporte à federação:

[IBM OpenPages](../saas-apps/ibmopenpages-tutorial.md), [OneTrust Privacy Management Software,](../saas-apps/onetrust-tutorial.md) [Dealpath](../saas-apps/dealpath-tutorial.md), [IriusRisk Federated Directory, and [Fidelity NetBenefits](../saas-apps/fidelitynetbenefits-tutorial.md).

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md)

Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="sign-in-with-additional-risk-detected"></a>Inscreva-se com risco adicional detetado

**Tipo:** Nova **categoria de serviço** de recurso: **Capacidade de proteção de identidade: Proteção** & de Segurança de Identidade

A perceção que obtém para uma deteção de risco detetada está ligada à sua subscrição AZure AD. Com a edição Azure AD Premium P2, obtém-se a informação mais detalhada sobre todas as deteções subjacentes.

Com a edição Azure AD Premium P1, as deteções que não estão abrangidas pela sua licença aparecem como o Pré-In de deteção de risco com risco adicional detetado.

Para obter mais informações, consulte [as deteções de risco do Azure Ative Directory](../identity-protection/overview-identity-protection.md).

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Hide Office 365 aplicações dos painéis de acesso do utilizador final

**Tipo:** Nova **categoria de funcionalidades Serviço:** A minha **capacidade de produto apps:** SSO

Agora é possível gerir melhor a forma como as aplicações do Office 365 aparecem nos painéis de acesso do utilizador através de uma nova definição do utilizador. Esta opção é útil para reduzir o número de aplicações nos painéis de acesso de um utilizador se preferir apenas mostrar aplicações do Office no portal do Office. A definição está localizada nas **Definições** do Utilizador e está rotulada, **os utilizadores só podem ver as aplicações do Office 365 no portal do Office 365**.

Para obter mais informações, consulte [Ocultar uma aplicação da experiência do utilizador no Azure Ative Directory](../manage-apps/hide-application-from-user-portal.md).

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Inscreva-se sem emenda em apps ativadas para Password SSO diretamente a partir do URL da aplicação

**Tipo:** Nova **categoria de funcionalidades Serviço:** A minha **capacidade de produto apps:** SSO

A extensão do navegador My Apps já se encontra disponível através de uma ferramenta conveniente que lhe dá o único sinal das Minhas Apps na capacidade como atalho no seu navegador. Depois de instalar, o utilizador verá um ícone de waffles no seu navegador que lhes proporciona acesso rápido às apps. Os utilizadores podem agora aproveitar:

- A capacidade de iniciar diretamente o sSO com base em aplicações baseadas em passwords a partir da página de entrada da aplicação
- Lançar qualquer app usando a funcionalidade de pesquisa rápida
- Atalhos para aplicações recentemente utilizadas a partir da extensão
- A extensão está disponível para Microsoft Edge, Chrome e Firefox.

Para obter mais informações, consulte [a extensão de inscrição segura das minhas apps.](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>A experiência de administração da AD em Azure Classic Portal foi reformada

**Tipo:** Categoria de **Serviço** Precotado: Capacidade do produto AD Azure: Diretório 

A partir de 8 de janeiro de 2018, a experiência de administração da AD Azure no portal clássico Azure foi reformada. Isto aconteceu em conjunto com a reforma do próprio portal clássico do Azure. No futuro, deverá utilizar o [centro de administração Azure AD](https://aad.portal.azure.com) para toda a sua administração baseada no portal da Azure AD.

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>O portal phoneFactor foi reformado

**Tipo:** Categoria de **Serviço** Precotado: Capacidade do produto AD Azure: Diretório 

A partir de 8 de janeiro de 2018, o portal phoneFactor foi reformado. Este portal foi utilizado para a administração do servidor MFA, mas essas funções foram transferidas para o portal Azure em portal.azure.com.

A configuração do MFA está localizada em: **Azure Ative Directory \> MFA Server**

---

### <a name="deprecate-azure-ad-reports"></a>Relatórios da AD deprecação Ad

**Tipo:** Categoria de **Serviço** Precotado: Capacidade do produto **reportando: Gestão** do ciclo de vida da identidade


Com a disponibilidade geral da nova consola da Azure Ative Directory Administration e das novas APIs agora disponíveis tanto para relatórios de atividade como de segurança, o relatório APIs em "/reports" foi retirado a partir do final de 31 de dezembro de 2017.

**O que está disponível?**

Como parte da transição para a nova consola de administração, disponibilizamos 2 novos APIs para recuperar registos de atividade azure. O novo conjunto de APIs fornece uma funcionalidade de filtragem e triagem mais rica, além de proporcionar atividades de auditoria e de inscrição mais ricas. Os dados anteriormente disponíveis através dos relatórios de segurança podem agora ser acedidos através da API de deteção de risco de Proteção de Identidade no Microsoft Graph.

Para obter mais informações, veja:

- [Começar com o Diretório Ativo Azure reportando API](../reports-monitoring/concept-reporting-api.md)

- [Começa com a Azure Ative Directory Identity Protection e o Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)

---

## <a name="december-2017"></a>Dezembro de 2017

### <a name="terms-of-use-in-the-access-panel"></a>Termos de utilização no Painel de Acesso

**Tipo:** Nova **categoria de serviço** de recurso: Termos de utilização **Capacidade do produto:** Conformidade

Agora pode ir ao Painel de Acesso e ver os termos de utilização que aceitou anteriormente.

Siga estes passos:

1. Vá ao [portal MyApps](https://myapps.microsoft.com)e inscreva-se.

2. No canto superior direito, selecione o seu nome e, em seguida, selecione **Profile** da lista.

3. No seu **Perfil,** **selecione os termos de utilização de Revisões**.

4. Agora pode rever os termos de uso que aceitou.

Para obter mais informações, consulte os [termos de utilização Azure AD (pré-visualização)](../conditional-access/terms-of-use.md).

---

### <a name="new-azure-ad-sign-in-experience"></a>Nova experiência de inscrição AD AZure

**Tipo:** Nova **categoria de funcionalidades Serviço:** Capacidade do Produto AD Azure: Autenticação do utilizador 

Os UIs do sistema de identidade Azure AD e Microsoft foram redesenhados para que tenham um aspeto e uma sensação consistentes. Além disso, a página de ad AD Azure recolhe primeiro o nome de utilizador, seguida da credencial num segundo ecrã.

Para mais informações, consulte [a nova experiência de inscrição da Azure AD está agora em visualização pública.](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Menos pedidos de inscrição: Uma nova experiência de "mantenha-me inscrito" para o Azure AD

**Tipo:** Nova **categoria de funcionalidades Serviço:** Capacidade do Produto AD Azure: Autenticação do utilizador 

O **Keep me assinado na** caixa de verificação na página de entrada Azure AD foi substituído por um novo pedido que aparece depois de autenticar com sucesso.

Se responder **Sim** a esta solicitação, o serviço dá-lhe um token de atualização persistente. Este comportamento é o mesmo que quando selecionou o **Keep me assinado na** caixa de cheques na experiência antiga. Para os inquilinos federados, este alerta mostra depois de autenticar com sucesso com o serviço federado.

Para obter mais informações, consulte [menos pedidos de inscrição: A nova experiência de "mantenha-me inscrito" para a Azure AD está em pré-visualização](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/).

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Adicionar configuração para exigir que os termos de uso sejam expandidos antes de aceitar

**Tipo:** Nova **categoria de serviço** de recurso: Termos de utilização **Capacidade do produto:** Conformidade

Uma opção para os administradores exige que os seus utilizadores expandam os termos de utilização antes de aceitarem os termos.

Selecione **On** or **Off** para exigir que os utilizadores expandam os termos de utilização. A definição **On** requer que os utilizadores vejam os termos de utilização antes de os aceitarem.

Para obter mais informações, consulte os [termos de utilização Azure AD (pré-visualização)](../conditional-access/terms-of-use.md).

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Ativação âmbito para atribuições de funções elegíveis

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade privilegiada de produto de gestão de **identidade:** Gestão de Identidade privilegiada

Pode utilizar a ativação de âmbito para ativar atribuições de funções de recursos Azure elegíveis com menos autonomia do que os padrão de atribuição originais. Um exemplo é se você é designado como proprietário de uma assinatura no seu inquilino. Com a ativação de âmbito, pode ativar a função do proprietário para até cinco recursos contidos na subscrição (tais como grupos de recursos e máquinas virtuais). A deteção da sua ativação pode reduzir a possibilidade de executar alterações indesejadas em recursos críticos do Azure.

Para mais informações, veja [o que é Azure AD Gestão de Identidade Privilegiada?](../privileged-identity-management/pim-configure.md)

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Novas aplicações federadas na galeria de aplicações AD AZure

**Tipo:** Nova **categoria de funcionalidades Serviço:** Capacidade de produto de aplicações **empresariais:** Integração de 3ª Parte

Em dezembro de 2017, adicionámos estas novas aplicações com suporte da Federação à nossa galeria de aplicações:

[Accredible](../saas-apps/accredible-tutorial.md), Adobe Experience Manager, [EFI Digital StoreFront](../saas-apps/efidigitalstorefront-tutorial.md), [Communifire](../saas-apps/communifire-tutorial.md) CybSafe, [FactSet](../saas-apps/factset-tutorial.md), [IMAGE WORKS,](../saas-apps/imageworks-tutorial.md) [MOBI,](../saas-apps/mobi-tutorial.md) [MobileIron Azure AD integração,](../saas-apps/mobileiron-tutorial.md) [Reflektive,](../saas-apps/reflektive-tutorial.md) [SAML SSO for Bamboo por resolução GmbH,](../saas-apps/bamboo-tutorial.md) [SAML SSO for Bitbucket by resolution GmbH,](../saas-apps/bitbucket-tutorial.md) [Vodeclic,](../saas-apps/vodeclic-tutorial.md)WebHR, Zenegy Azure AD Integration.

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](../saas-apps/tutorial-list.md)

Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Fluxos de trabalho de aprovação para funções de diretório Azure AD

**Tipo:** Categoria **de Serviço de** Funcionalidade Alterada: Capacidade privilegiada de produto de gestão de **identidade:** Gestão de Identidade privilegiada

O fluxo de trabalho de aprovação para funções de diretório Azure AD está geralmente disponível.

Com o fluxo de trabalho de aprovação, os administradores de funções privilegiadas podem exigir que os membros elegíveis solicitem a ativação de funções antes de poderem usar o papel privilegiado. Vários utilizadores e grupos podem ser delegados de responsabilidades de aprovação. Os membros elegíveis recebem notificações quando a aprovação está terminada e o seu papel está ativo.

---

### <a name="pass-through-authentication-skype-for-business-support"></a>Autenticação pass-through: Skype para suporte ao negócio

**Tipo:** Alteração **da categoria de serviço** de recurso: Autenticações (Logins) **Capacidade do produto:** Autenticação do utilizador

A autenticação pass-through suporta agora as inscrições do utilizador no Skype para aplicações de clientes empresariais que suportam a autenticação moderna, que inclui topologias online e híbridas.

Para mais informações, consulte [as topologias skype for Business suportadas com a autenticação moderna.](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Atualizações para Azure AD Gestão de Identidade Privilegiada para Azure RBAC (pré-visualização)

**Tipo:** Categoria **de Serviço de** Funcionalidade Alterada: Capacidade privilegiada de produto de gestão de **identidade:** Gestão de Identidade privilegiada

Com a atualização de pré-visualização pública da Azure AD Privileged Identity Management (PIM) para o controlo de acesso baseado em funções Azure (Azure RBAC), pode agora:

* Use apenas a administração suficiente.
* Requer aprovação para ativar funções de recursos.
* Agende uma ativação futura de um papel que requer aprovação tanto para os papéis de AZure AD como para a Azure.

Para mais informações, consulte [Gestão de Identidade Privilegiada para recursos Azure (pré-visualização)](../privileged-identity-management/azure-pim-resource-rbac.md).

---

## <a name="november-2017"></a>Novembro de 2017

### <a name="access-control-service-retirement"></a>Aposentadoria do serviço access Control

**Tipo:** Plano de mudança **categoria de serviço:** Capacidade do serviço de controlo de **acesso:** serviço de controlo de acesso

O Azure Ative Directory Access Control (também conhecido como serviço de Controlo de Acesso) será reformado no final de 2018. Nas próximas semanas serão fornecidas mais informações que incluam um calendário detalhado e orientações de migração de alto nível. Pode deixar comentários nesta página com quaisquer perguntas sobre o serviço de Controlo de Acesso, e um membro da equipa irá respondê-los.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Restringir o acesso do navegador ao Navegador Gerido Intune

**Tipo:** Plano de alteração **da categoria serviço:** Capacidade de **acesso condicional: Segurança** e proteção de identidade

Pode restringir o acesso do navegador ao Office 365 e a outras aplicações em nuvem ligadas ao Azure, utilizando o Navegador Gerido Intune como uma aplicação aprovada.

Agora pode configurar a seguinte condição para acesso condicional baseado em aplicações:

**Aplicativos para clientes:** Navegador

**Qual é o efeito da mudança?**

Hoje, o acesso está bloqueado quando se utiliza esta condição. Quando a pré-visualização estiver disponível, todo o acesso exigirá a utilização da aplicação gerida para o navegador.

Procure esta capacidade e mais informações nos próximos blogs e divulgue notas.

Para mais informações, consulte [acesso condicional em Azure AD](../conditional-access/overview.md).

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novas aplicações para clientes aprovados para acesso condicional baseado em aplicativos Azure AD

**Tipo:** Plano de alteração **da categoria serviço:** Capacidade de **acesso condicional: Segurança** e proteção de identidade

As seguintes aplicações estão na lista de [aplicações de clientes aprovadas:](../conditional-access/concept-conditional-access-conditions.md#client-apps)

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Para obter mais informações, veja:

- [Requisito de aplicação de cliente aprovado](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Acesso Condicional baseado em aplicativos Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Suporte a termos de utilização para vários idiomas

**Tipo:** Nova **categoria de serviço** de recurso: Termos de utilização **Capacidade do produto:** Conformidade

Os administradores podem agora criar novos termos de utilização que contenham vários documentos PDF. Pode marcar estes documentos PDF com uma língua correspondente. Os utilizadores são mostrados o PDF com o idioma correspondente com base nas suas preferências. Se não houver correspondência, a língua predefinida é mostrada.

---

### <a name="real-time-password-writeback-client-status"></a>Estado do cliente de writeback de palavra-passe em tempo real

**Tipo:** Nova **categoria de funcionalidade Serviço:** Autosserviço palavra-passe redefinir **Capacidade do produto:** Autenticação do utilizador

Pode agora rever o estado do seu cliente de writeback de palavra-passe no local. Esta opção está disponível na secção **de integração no local** da página de reset da [Palavra-passe.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

Se houver problemas com a sua ligação ao seu cliente de writeback no local, vê uma mensagem de erro que lhe fornece:

- Informações sobre o porquê de não conseguir ligar-se ao seu cliente no local.
- Um link para documentação que o ajuda na resolução do problema.

Para mais informações, consulte [a integração no local.](../authentication/concept-sspr-howitworks.md#on-premises-integration)

---

### <a name="azure-ad-app-based-conditional-access"></a>Acesso Condicional baseado em aplicativos Azure AD

**Tipo:** Nova **categoria de serviço** de recurso: Capacidade de produto AD Azure: Segurança e proteção de identidade 

Agora pode restringir o acesso ao Office 365 e a outras aplicações em nuvem ligadas a Azure a [aplicações de clientes aprovadas](../conditional-access/concept-conditional-access-conditions.md#client-apps) que suportem políticas de proteção de aplicações Intune utilizando [o Acesso Condicional baseado em aplicações AD AZure.](../conditional-access/app-based-conditional-access.md) As políticas de proteção de aplicações Intune são usadas para configurar e proteger os dados da empresa nestas aplicações do cliente.

Ao combinar políticas de acesso condicional [baseadas em](../conditional-access/app-based-conditional-access.md) [aplicativos,](../conditional-access/require-managed-devices.md) tem a flexibilidade para proteger os dados para dispositivos pessoais e da empresa.

As seguintes condições e controlos estão agora disponíveis para utilização com acesso condicional baseado em aplicações:

**Condição da plataforma suportada**

- iOS
- Android

**Condição de aplicativos de cliente**

- Aplicativos móveis e clientes de desktop

**Controlo de acesso**

- Requera uma aplicação de cliente aprovada

Para obter mais informações, consulte [o Acesso Condicional baseado em aplicações Azure AD.](../conditional-access/app-based-conditional-access.md)

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Gerir dispositivos AD AZure no portal Azure

**Tipo:** Nova **categoria de funcionalidades Serviço:** Registo e gestão de **dispositivos Capacidade do produto:** Segurança e proteção de identidade

Agora pode encontrar todos os seus dispositivos ligados ao Azure AD e as atividades relacionadas com o dispositivo num só local. Existe uma nova experiência de administração para gerir todas as identidades e configurações do seu dispositivo no portal Azure. Nesta versão, pode:

- Veja todos os seus dispositivos que estão disponíveis para Acesso Condicional em AZure AD.
- Ver propriedades, que incluem os seus dispositivos híbridos Azure AD.
- Encontre as teclas BitLocker para os seus dispositivos AD Azure, gerencie o seu dispositivo com o Intune e muito mais.
- Gerir as definições relacionadas com o dispositivo AZure.

Para obter mais informações, consulte [Gerir os dispositivos utilizando o portal Azure.](../devices/device-management-azure-portal.md)

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Suporte para macOS como plataforma de dispositivos para acesso condicional Azure AD

**Tipo:** Nova **categoria de serviço** de recurso: Capacidade de acesso condicional **do produto:** Segurança e proteção de identidade

Agora pode incluir (ou excluir) o macOS como condição da plataforma do dispositivo na sua política de Acesso Condicionado AZure AD. Com a adição de macOS às plataformas de dispositivos suportados, pode:

- **Inscreva-se e gere dispositivos macOS utilizando o Intune.** À semelhança de outras plataformas como iOS e Android, está disponível uma aplicação de portal da empresa para o macOS para fazer inscrições unificadas. Pode utilizar a nova aplicação do portal da empresa para o macOS para inscrever um dispositivo com o Intune e registá-lo com a Azure AD.
- **Certifique-se de que os dispositivos macOS aderem às políticas de conformidade da sua organização definidas no Intune.** No Intune, no portal Azure, pode agora configurar políticas de conformidade para dispositivos macOS.
- **Restringir o acesso a aplicações em Azure AD a apenas dispositivos macOS compatíveis.** A autoria da política de acesso condicional tem o macOS como uma opção de plataforma de dispositivos separada. Agora pode autoria políticas de Acesso Condicional específicas ao macOS para a aplicação direcionada definida no Azure.

Para obter mais informações, veja:

- [Criar uma política de conformidade para dispositivos macOS com o Intune](/mem/intune/protect/compliance-policy-create-mac-os)
- [Acesso Condicional em Azure AD](../conditional-access/overview.md)

---

### <a name="network-policy-server-extension-for-azure-ad-multi-factor-authentication"></a>Extensão do servidor de política de rede para autenticação de vários fatores Azure AD

**Tipo:** Nova **categoria de funcionalidades Serviço:**  Capacidade de autenticação multi-factor: **Autenticação** do utilizador

A extensão do Servidor de Política de Rede para autenticação multi-factor Azure AD adiciona capacidades de autenticação multi-factor baseadas na nuvem à sua infraestrutura de autenticação utilizando os servidores existentes. Com a extensão do Servidor de Política de Rede, pode adicionar chamada telefónica, mensagem de texto ou verificação de aplicativos telefónicos ao fluxo de autenticação existente. Não é preciso instalar, configurar e manter novos servidores.

Esta extensão foi criada para organizações que pretendem proteger as ligações de rede privada virtual sem implementar o Azure Multi-Factor Authentication Server. A extensão do Servidor de Política de Rede funciona como um adaptador entre o RADIUS e a Autenticação Multi-Factor Azure AD baseada na nuvem para fornecer um segundo fator de autenticação para utilizadores federados ou sincronizados.

Para obter mais informações, consulte [Integrar a infraestrutura existente do Servidor de Política de Rede com autenticação multi-factor AD Azure.](../authentication/howto-mfa-nps-extension.md)

---

### <a name="restore-or-permanently-remove-deleted-users"></a>Restaurar ou remover permanentemente utilizadores eliminados

**Tipo:** Nova **categoria de funcionalidades Serviço:** Capacidade de gestão do **utilizador Capacidade do produto:** Diretório

No centro de administração Admin Azure, pode agora:

- Restaurar um utilizador eliminado.
- Elimine permanentemente um utilizador.

**Para experimentá-lo:**

1. No centro de administração Admin Azure, selecione [Todos os utilizadores](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) na secção **Gerir.**

2. Na lista **'Mostrar',** selecione **utilizadores recentemente eliminados.**

3. Selecione um ou mais recentemente eliminados utilizadores e, em seguida, ou restaurá-los ou eliminá-los permanentemente.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novas aplicações para clientes aprovados para acesso condicional baseado em aplicativos Azure AD

**Tipo:** Alteração **da categoria de serviço de recurso:** Capacidade de acesso condicional **do produto:** Segurança e proteção de identidade

As seguintes aplicações foram adicionadas à lista de [aplicações de clientes aprovadas:](../conditional-access/concept-conditional-access-conditions.md#client-apps)

- Microsoft Planner
- Azure Information Protection

Para obter mais informações, veja:

- [Requisito de aplicação de cliente aprovado](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Acesso Condicional baseado em aplicativos Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Utilize "OR" entre controlos numa política de acesso condicional

**Tipo:** Alteração **da categoria de serviço de recurso:** Capacidade de acesso condicional **do produto:** Segurança e proteção de identidade

Agora pode utilizar "OR" (requerer um dos controlos selecionados) para controlos de acesso condicional. Pode utilizar esta funcionalidade para criar políticas com "OR" entre controlos de acesso. Por exemplo, pode utilizar esta funcionalidade para criar uma política que exija que um utilizador faça sessão, utilizando a autenticação multi-factor "OR" para estar num dispositivo em conformidade.

Para obter mais informações, consulte [Controlos no Acesso Condicionado AZURE AD](../conditional-access/controls.md).

---

### <a name="aggregation-of-real-time-risk-detections"></a>Agregação de deteções de riscos em tempo real

**Tipo:** Alteração da **categoria de serviço de funcionalidade:** Capacidade de proteção de identidade Capacidade do **produto:** Segurança e proteção de identidade

Na Proteção de Identidade Azure AD, todas as deteções de risco em tempo real originadas do mesmo endereço IP num determinado dia são agora agregadas para cada tipo de deteção de risco. Esta alteração limita o volume de deteções de risco mostradas sem qualquer alteração na segurança do utilizador.

A deteção subjacente em tempo real funciona sempre que o utilizador assina. Se tiver uma política de segurança de risco de entrada configurada para autenticação multi-factor ou acesso a blocos, ela ainda é ativada durante cada entrada de risco.

---

## <a name="october-2017"></a>Outubro de 2017

### <a name="deprecate-azure-ad-reports"></a>Relatórios da AD deprecação Ad

**Tipo:** Plano de mudança **categoria de serviço:** Capacidade do produto reportando: Gestão do ciclo de vida da identidade 

O portal Azure fornece-lhe:

- Uma nova consola de administração AZure.
- Novas APIs para relatórios de atividade e segurança.

Devido a estas novas capacidades, o relatório APIs no âmbito do ponto final /reporte foi retirado em 10 de dezembro de 2017.

---

### <a name="automatic-sign-in-field-detection"></a>Deteção automática de campo de inscrição

**Tipo:** Categoria **serviço fixo:** A minha **capacidade de produto apps:** único sinal

O Azure AD suporta a deteção automática de campo de entrada para aplicações que tornem um nome de utilizador HTML e um campo de palavra-passe. Estes passos estão documentados em [Como capturar automaticamente campos de inscrição para uma aplicação](../manage-apps/troubleshoot-password-based-sso.md#manually-capture-sign-in-fields-for-an-app). Pode encontrar esta capacidade adicionando uma aplicação *Não Galeria* na página **aplicações** da Empresa no [portal Azure.](https://aad.portal.azure.com) Além disso, pode configurar o modo **único de início de súmula** nesta nova aplicação para O Único Sinal baseado em **Palavra-Passe,** introduzir um URL web e, em seguida, guardar a página.

Devido a um problema de serviço, esta funcionalidade foi temporariamente desativada. O problema foi resolvido e a deteção automática de campo de inscrição está novamente disponível.

---

### <a name="new-multi-factor-authentication-features"></a>Novas funcionalidades de autenticação multi-factor

**Tipo:** Nova **categoria de funcionalidades Serviço:** Capacidade de autenticação de vários fatores **Capacidade do produto:** Segurança e proteção de identidade

A autenticação multi-factor (MFA) é uma parte essencial da proteção da sua organização. Para tornar as credenciais mais adaptáveis e a experiência mais perfeita, foram adicionadas as seguintes características:

- Os resultados do desafio multi-factor são diretamente integrados no relatório de inscrição AZure AD, que inclui acesso programático aos resultados do MFA.
- A configuração do MFA está mais profundamente integrada na experiência de configuração AD Azure no portal Azure.

Com esta pré-visualização pública, a gestão e reporte de MFA são uma parte integrada da experiência de configuração AD core Azure. Agora pode gerir a funcionalidade do portal de gestão MFA dentro da experiência Azure AD.

Para obter mais informações, consulte [referência para relatórios de MFA no portal Azure](../authentication/howto-mfa-reporting.md).

---

### <a name="terms-of-use"></a>Termos de utilização

**Tipo:** Nova **categoria de serviço** de recurso: Termos de utilização **Capacidade do produto:** Conformidade

Pode utilizar os termos de utilização da Azure AD para apresentar informações como isenções de responsabilidade relevantes para requisitos legais ou de conformidade para os utilizadores.

Pode utilizar os termos de utilização do AD Azure nos seguintes cenários:

- Termos gerais de utilização para todos os utilizadores da sua organização
- Termos de utilização específicos baseados nos atributos de um utilizador (por exemplo, médicos vs. enfermeiros ou empregados domésticos vs. internacionais, feitos por grupos dinâmicos)
- Termos de uso específicos para aceder a aplicações empresariais de alto impacto, como salesforce

Para mais informações, consulte [os termos de utilização da AD Azure](../conditional-access/terms-of-use.md).

---

### <a name="enhancements-to-privileged-identity-management"></a>Melhoramentos à Gestão de Identidades Privilegiadas

**Tipo:** Nova **categoria de serviço de recurso:** Capacidade privilegiada de produto de gestão de **identidade:** Gestão de Identidade privilegiada

Com a Azure AD Gestão de Identidade Privilegiada, pode gerir, controlar e monitorizar o acesso aos recursos Azure (pré-visualização) dentro da sua organização para:

- Subscrições
- Grupos de recursos
- Máquinas virtuais

Todos os recursos dentro do portal Azure que utilizam a funcionalidade Azure RBAC podem tirar partido de todas as capacidades de gestão de segurança e ciclo de vida que a Azure AD Privileged Identity Management tem para oferecer.

Para mais informações, consulte [Gestão de Identidade Privilegiada para recursos Azure.](../privileged-identity-management/azure-pim-resource-rbac.md)

---

### <a name="access-reviews"></a>Revisões de acesso

**Tipo:** Nova **categoria de serviço de recurso:** Acesso revê **capacidade do produto:** Conformidade

As organizações podem usar comentários de acesso (pré-visualização) para gerir eficientemente os membros do grupo e o acesso a aplicações empresariais:

- Pode voltar a certificar o acesso do utilizador convidado ao utilizar as revisões do respetivo acesso às aplicações e as associações a grupos. Os revisores podem decidir eficazmente se permitem aos hóspedes o acesso continuado com base nos insights fornecidos pelas avaliações de acesso.
- Pode voltar a certificar o acesso dos colaboradores às aplicações e as associações a grupos com revisões de acesso.

Pode recolher os controlos de revisão de acesso relativos a programas relevantes para a sua organização de modo a controlar as revisões de conformidade ou de aplicações sensíveis a riscos.

Para mais informações, consulte [as avaliações de acesso a Azure AD](../governance/access-reviews-overview.md).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Ocultar aplicações de terceiros das Minhas Apps e do launcher de aplicações office 365

**Tipo:** Nova **categoria de serviço de funcionalidade:** A minha **capacidade de produto apps:** único sinal de sação

Agora é possível gerir melhor as aplicações que aparecem nos portais dos seus utilizadores através de uma nova propriedade **de aplicações ocultas.** Pode ocultar aplicações para ajudar nos casos em que os azulejos de aplicações aparecem para serviços de back-end ou duplicam azulejos e lança-aplicações de desordem. O toggle está na secção **Propriedades** da app de terceiros e está rotulado **Visível para o utilizador?** Também pode esconder uma aplicação programáticamente através do PowerShell.

Para obter mais informações, consulte [Ocultar uma aplicação de terceiros a partir da experiência de um utilizador em Azure AD](../manage-apps/hide-application-from-user-portal.md).


**O que está disponível?**

 Como parte da transição para a nova consola de administração, estão disponíveis duas novas APIs para a recuperação de registos de atividade azure AD. O novo conjunto de APIs fornece uma funcionalidade de filtragem e triagem mais rica, além de proporcionar atividades de auditoria e de inscrição mais ricas. Os dados anteriormente disponíveis através dos relatórios de segurança podem agora ser acedidos através da API de Deteção de Riscos de Proteção de Identidade no Microsoft Graph.


## <a name="september-2017"></a>Setembro de 2017

### <a name="hotfix-for-identity-manager"></a>Hotfix para Gestor de Identidade

**Tipo:** Categoria de serviço de funcionalidade **alterada:** Capacidade de produto do Gestor de **Identidade:** Gestão do ciclo de vida da identidade

Um pacote de roll-up hotfix (build 4.4.1642.0) está disponível a partir de 25 de setembro de 2017, para o Identity Manager 2016 Service Pack 1. Este pacote roll-up:

- Resolve problemas e adiciona melhorias.
- É uma atualização cumulativa que substitui todas as atualizações do Identity Manager 2016 Service Pack 1 até à construção de 4.4.1459.0 para O Gestor de Identidade 2016.
- Exige que tenha o Gestor de Identidade 2016 a construir 4.4.1302.0.

Para mais informações, consulte [o pacote hotfix rollup (build 4.4.1642.0) está disponível para o Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562).

---
