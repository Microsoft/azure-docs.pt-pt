---
title: Novidades Notas de lançamento - Azure Ative Directory / Microsoft Docs
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
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71726724e7c018f34b1175f323d0c8e55b604931
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836483"
---
# <a name="whats-new-in-azure-active-directory"></a>Quais as novidades no Diretório Ativo do Azure?

>Seja notificado sobre quando revisitar esta página para obter atualizações copiando e colando este URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` no leitor de feed feed feed do leitor de feed ![ RSS. ](./media/whats-new/feed-icon-16x16.png)

A Azure AD recebe melhorias continuadamente. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

- Os últimos lançamentos
- Problemas conhecidos
- Correções de erros
- Funcionalidade preterida
- Planos para alterações

Esta página é atualizada mensalmente, por isso revisite-a regularmente. Se procura itens com mais de seis meses, pode encontrá-los no [Archive for What's new in Azure Ative Directory](whats-new-archive.md).

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
> Isto não afeta quaisquer eventos nos registos de auditoria fora dos eventos de sincronização emitidos pelo serviço de fornecimento. Eventos como a criação de uma aplicação, política de acesso condicional, um utilizador no diretório, etc. continuarão a ser emitidos nos registos de auditoria. [Saiba mais](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs?context=azure/active-directory/app-provisioning/context/app-provisioning-context).
 

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
**Categoria de serviço:** N/A  
**Capacidade do produto:** Normas
 
O Azure Ative Directory irá deprecação dos seguintes protocolos até 31 de março de 2021:
- TLS 1.0
- TLS 1.1
- Suíte cifra 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Todas as combinações de servidores de clientes e servidores de navegador devem utilizar os serviços TLS 1.2 e cifra modernas para manter uma ligação segura ao Azure Ative Directory para os serviços Azure, Office 365 e Microsoft 365.

Os ambientes afetados são:
- Azure US Gov
- [Escritório 365 GCC High & DoD](https://docs.microsoft.com/microsoft-365/compliance/tls-1-2-in-office-365-gcc?view=o365-worldwide)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>Atribuir candidaturas a funções na AU e no âmbito do objeto

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** RBAC  
**Capacidade do produto:** Controlo de Acessos
 
Esta funcionalidade permite atribuir uma aplicação (SPN) a uma função de administrador no âmbito da Unidade Administrativa. Para saber mais, consulte [atribuir funções a uma unidade administrativa](../roles/admin-units-assign-roles.md).

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Agora pode desativar e eliminar utilizadores convidados quando lhes é negado o acesso a um recurso

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Comentários de Acesso  
**Capacidade do produto:** Governação da Identidade
 
Desativar e eliminar é um controlo avançado em Azure AD Access Reviews para ajudar as organizações a gerir melhor os hóspedes externos em Grupos e Apps. Se os hóspedes forem negados numa revisão de acesso, **desative e apagará** automaticamente bloqueará a sua inscrição durante 30 dias. Depois de 30 dias, serão removidos do inquilino completamente.

Para obter mais informações sobre esta funcionalidade, consulte [Desativar e eliminar identidades externas com avaliações de acesso AD Ad (Pré-visualização)](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews-preview).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Os criadores do Access Review podem adicionar mensagens personalizadas em e-mails aos revisores

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Comentários de Acesso  
**Capacidade do produto:** Governação da Identidade
 
Nas avaliações de acesso a AD do Azure, os administradores que criam comentários podem agora escrever uma mensagem personalizada aos revisores. Os revisores verão a mensagem no e-mail que recebem e que os leva a completar a revisão. Para saber mais sobre a utilização desta função, consulte o passo 6 da secção [Definições Avançadas.](../governance/create-access-review.md#advanced-settings)

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

Recomenda-se que os clientes utilizem IDs de modelo de função no seu script e código PowerShell, em vez do nome de exibição. O ID do modelo de função é suportado para uso para [diretórios](https://docs.microsoft.com/graph/api/resources/directoryrole?view=graph-rest-1.0) e [funDefinition](https://docs.microsoft.com/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) objects. Para obter mais informações sobre iDs de modelo de papel, consulte [iDs do modelo de função](../roles/permissions-reference.md#role-template-ids).

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

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblebee - Productivity Superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture Cloud,](../saas-apps/abbyy-flexicapture-cloud-tutorial.md) [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys Cloud Integration for Azure](https://apps.mypurecloud.com/msteams-integration/), [Zone Technologies Portal](https://portail.zonetechnologie.com/signin), [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker](https://console.datawiza.com/), [ZOKRI,](https://app.zokri.com/) [CheckProof](../saas-apps/checkproof-tutorial.md), [Ecochallenge.org,](https://events.ecochallenge.org/users/login) [atSpoke](http://atspoke.com/login), [Appointment Reminder](https://app.appointmentreminder.co.nz/account/login), [Cloud.Market,](https://cloud.market/) [TravelPerk,](../saas-apps/travelperk-tutorial.md) [Greetly](https://app.greetly.com/), [OrgVitality SSO}(.. /saas-apps/orgvitality-sso-tutorial.md), [Web Cargo Air](../saas-apps/web-cargo-air-tutorial.md), Loop Flow [CRM,](../saas-apps/loop-flow-crm-tutorial.md) [Starmind,](../saas-apps/starmind-tutorial.md) [Workstem,](https://hrm.workstem.com/login) [Retail Zipline,](../saas-apps/retail-zipline-tutorial.md) [Hoxhunt,](../saas-apps/hoxhunt-tutorial.md) [MEVISIO,](../saas-apps/mevisio-tutorial.md) [Samsara,](../saas-apps/samsara-tutorial.md) [Nimbus,](../saas-apps/nimbus-tutorial.md) [Pulse Secure Virtual Traffic Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

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

![Tabela de novos nomes](media/whats-new/azure-role.png)

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

## <a name="september-2020"></a>Setembro de 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Novos conectores de provisionamento na Galeria de Aplicações AD Azure - setembro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Integração de Terceiros
 
Pode agora automatizar a criação, atualização e eliminação de contas de utilizador para estas novas aplicações integradas:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Consciência de Segurança Webroot](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte [automatizar o fornecimento de utilizadores para aplicações SaaS com Azure AD](../app-provisioning/user-provisioning.md).
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Atualização de pré-visualização pública de provisionamento de provisão pública em nuvem

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Capacidade do produto de provisão de nuvem Azure **AD: Gestão** do ciclo de vida da identidade
 
A atualização de pré-visualização do Azure AD Connect Cloud Provisioning apresenta duas melhorias importantes desenvolvidas a partir do feedback do cliente: 

- Atribuir experiência de mapeamento através do portal Azure

    Com esta funcionalidade, os Administradores de TI podem mapear os atributos do utilizador, grupo ou de contacto de AD a AD AZure utilizando vários tipos de mapeamento presentes hoje em dia. O mapeamento de atributos é uma funcionalidade utilizada para normalizar os valores dos atributos que fluem do Ative Directory para o Azure Ative Directory. Pode-se determinar se mapeia diretamente o valor do atributo como é de AD para Azure AD ou se utiliza expressões para transformar os valores do atributo ao a provisionar os utilizadores. [Saiba mais](../cloud-provisioning/how-to-attribute-mapping.md)

- Provisão a pedido ou experiência do utilizador de teste

    Uma vez configurada a sua configuração, talvez queira testar para ver se a transformação do utilizador está a funcionar como esperado antes de a aplicar a todos os seus utilizadores no âmbito. Com o provisionamento a pedido, os Administradores de TI podem introduzir o Nome Distinto (DN) de um utilizador de AD e ver se estão a ser sincronizados como esperado. O fornecimento a pedido proporciona uma ótima maneira de garantir que os mapeamentos de atributos que fez anteriormente funcionam como esperado. [Saiba mais](../cloud-provisioning/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Recuperação auditada de BitLocker em AD AZure - Visualização pública

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão de Acesso a Dispositivos  
**Capacidade do produto:** Gestão do ciclo de vida do dispositivo
 
Quando os administradores de TI ou os utilizadores finais lêem as teclas de recuperação bitLocker a que têm acesso, o Azure Ative Directory gera agora um registo de auditoria que captura quem acedeu à chave de recuperação. A mesma auditoria fornece detalhes do dispositivo a que a chave BitLocker foi associada.

Os utilizadores finais podem [aceder às suas chaves de recuperação através da Minha Conta.](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key) Os administradores de TI podem aceder às teclas de recuperação através da [chave de recuperação BitLocker API em versão beta](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) ou através do Portal AD Azure. Para saber mais, consulte [ver ou copiar as teclas BitLocker no Portal AD Azure](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

---

### <a name="teams-devices-administrator-built-in-role"></a>Papel integrado do administrador de dispositivos de equipas

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** RBAC  
**Capacidade do produto:** Controlo de Acessos
 
Os utilizadores com a função [de Administrador de Dispositivos de Equipas](../roles/permissions-reference.md#teams-devices-administrator) podem gerir [dispositivos certificados por Equipas](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) a partir do Centro de Administração de Equipas. 

Esta função permite ao utilizador visualizar todos os dispositivos num só olhar, com a capacidade de pesquisar e filtrar dispositivos. O utilizador também pode verificar os detalhes de cada dispositivo, incluindo a conta iniciada e a localização e modelo do dispositivo. O utilizador pode alterar as definições do dispositivo e atualizar as versões do software. Esta função não concede permissões para verificar a atividade das Equipas e chamar a qualidade do dispositivo.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Capacidades avançadas de consulta para objetos de diretório

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** MS Graph  
**Capacidade do produto:** Experiência de Desenvolvedor
 
Todas as novas capacidades de consulta introduzidas para os Objetos de Diretório em APIs AZure AD estão agora disponíveis no ponto final v1.0 e prontas para a produção. Os desenvolvedores podem contar, pesquisar, filtrar e classificar objetos de diretório e links relacionados usando os operadores OData padrão.

Para saber mais, consulte a documentação [aqui,](https://aka.ms/BlogPostMezzoGA)e também pode enviar feedback com esta [breve pesquisa.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u)
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Pré-visualização pública: avaliação contínua de acesso para inquilinos que configuraram políticas de acesso condicional

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
A avaliação contínua de acesso (CAE) está agora disponível em pré-visualização pública para inquilinos AZURE AD com políticas de Acesso Condicional. Com o CAE, eventos críticos de segurança e políticas são avaliados em tempo real. Isto inclui desativação de conta, reset de palavra-passe e alteração de localização. Para saber mais, consulte [a avaliação de acesso contínuo.](../conditional-access/concept-continuous-access-evaluation.md)

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Pré-visualização pública: pergunte aos utilizadores que solicitem um pacote de acesso perguntas adicionais para melhorar as decisões de aprovação

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão do Acesso ao Utilizador  
**Capacidade do produto:** Gestão de Direitos
 
Os administradores podem agora exigir que os utilizadores que solicitem um pacote de acesso respondam a perguntas adicionais para além da justificação do negócio no portal My Access da Azure AD Entitlement. As respostas dos utilizadores serão então mostradas aos aprovadores para ajudá-los a tomar uma decisão de aprovação de acesso mais precisa. Para saber mais, consulte [Recolher informações adicionais do solicitador para aprovação (pré-visualização)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview).
 
---

### <a name="public-preview-enhanced-user-management"></a>Pré-visualização pública: Melhor gestão dos utilizadores

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão de Utilizadores  
**Capacidade do produto:** Gestão de Utilizadores
 

O portal AD AZure foi atualizado para facilitar a utilizadores nas páginas de Todos os utilizadores e utilizadores Eliminados. As alterações na pré-visualização incluem: 
- Propriedades de utilizador mais visíveis, incluindo ID de objeto, estado de sincronização de diretório, tipo de criação e emitente de identidade.
- A pesquisa permite agora pesquisas combinadas de nomes, e-mails e iDs de objetos.
- Filtragem melhorada por tipo de utilizador (membro, convidado e nenhum), estado de sincronização de diretórios, tipo de criação, nome da empresa e nome de domínio.
- Novas capacidades de triagem em propriedades como nome, nome principal do utilizador e data de eliminação.
- Um novo total de utilizadores conta que atualiza com quaisquer pesquisas ou filtros.

Para mais informações, consulte [as melhorias de gestão do utilizador (pré-visualização) no Azure Ative Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Novo campo de notas para aplicações da Enterprise

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Capacidade do produto de aplicações **empresariais:** SSO

Pode adicionar notas de texto gratuitas às aplicações da Enterprise. Pode adicionar qualquer informação relevante que o ajude a gerente de aplicações ao abrigo de aplicações da Enterprise. Para mais informações, consulte [Quickstart: Configure propriedades para uma aplicação no seu inquilino Azure Ative Directory (Azure AD).](../manage-apps/add-application-portal-configure.md) 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Novas Aplicações Federadas disponíveis na galeria AZure AD Application - setembro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros

Em setembro de 2020 adicionámos 34 novas aplicações na nossa galeria app com o apoio da Federação:

[VMware Horizon - Portal de Acesso Unificado,]() [Pulse Secure PCS](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventário360,](../saas-apps/pulse-secure-pcs-tutorial.md) [Frontitude,](https://services.enteksystems.de/sso/microsoft/signup) [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_Server,](https://zaas.zenmutech.com/user/signin) [HashData for Business](https://hashdata.app/login.xhtml), [SecureLogin,](https://securelogin.securelogin.nu/sso/azure/login) [CyberSolutions MAILBASEΣ/CMSS,](../saas-apps/cybersolutions-mailbase-tutorial.md) [CyberSolutions CYBERMAILΣ,](../saas-apps/cybersolutions-cybermail-tutorial.md) [LimbleCMMS,](https://auth.limblecmms.com/) [Glint Inc,](../saas-apps/glint-inc-tutorial.md) [zeroheight,](../saas-apps/zeroheight-tutorial.md) [Gender Fitness,](https://app.genderfitness.com/) [Coeo Portal,](https://my.coeo.com/) [Grammarly,](../saas-apps/grammarly-tutorial.md) [Fivetran,](../saas-apps/fivetran-tutorial.md) [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill,](../saas-apps/teamzskill-tutorial.md) [raumfürraum,](../saas-apps/raumfurraum-tutorial.md) [Saviynt,](../saas-apps/saviynt-tutorial.md) [BizMerlinHR,](https://marketplace.bizmerlin.net/bmone/signup) [Mobile Locker,](../saas-apps/mobile-locker-tutorial.md) [Zengine,](../saas-apps/zengine-tutorial.md) [CloudCADI,](https://app.cloudcadi.com/login) [Simfoni Analytics,](https://simfonianalytics.com/accounts/microsoft/login/) [Priva Identity & Access Management](https://my.priva.com/), Nitro [Pro,](https://www.gonitro.com/nps/product-details/downloads) [Eventfinity,](../saas-apps/eventfinity-tutorial.md) [Fexa,](../saas-apps/fexa-tutorial.md) [Secured Signing Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal) [AAD Setup,](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal) [Wistec Online,](https://wisteconline.com/auth/oidc) [Oracle PeopleSoft - Protected by F5 BIG-IP](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

Pode ainda encontrar a documentação de todas as aplicações a partir daqui: https://aka.ms/AppsTutorial .

Para listar a sua aplicação na galeria de aplicações AZure AD, leia os detalhes aqui: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Novo papel de delegação na gestão de direitos da Azure AD: Gestor de atribuição de pacotes de acesso

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão do Acesso ao Utilizador  
**Capacidade do produto:** Gestão de Direitos
 
Uma nova função de Gestor de Atribuição de Pacotes de Acesso foi adicionada na gestão de direitos AD da Azure para fornecer permissões granulares para gerir as atribuições. Pode agora delegar tarefas a um utilizador nesta função, que pode delegar a gestão de atribuições de um pacote de acesso a um empresário. No entanto, um Gestor de Atribuição de Pacotes de Acesso não pode alterar as políticas de pacotes de acesso ou outras propriedades que são definidas pelos administradores. 

Com este novo papel, você beneficia dos privilégios menos necessários para delegar a gestão de atribuições e manter o controlo administrativo em todas as outras configurações de pacotes de acesso. Para saber mais, consulte [as funções de gestão de direitos.](../governance/entitlement-management-delegate.md#entitlement-management-roles)
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Alterações ao fluxo de embarque da Gestão de Identidade Privilegiada

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Gestão de Identidade Privilegiada  
**Capacidade do produto:** Gestão de Identidade Privilegiada
 
Anteriormente, o embarque para a Gestão de Identidade Privilegiada (PIM) exigia o consentimento do utilizador e um fluxo de embarque na lâmina da PIM que incluía a inscrição no Azure AD MFA. Com a recente integração da experiência PIM nas funções AZure AD e lâmina de administradores, estamos removendo esta experiência. Qualquer inquilino com licença P2 válida será automaticamente a bordo da PIM.

O embarque na PIM não tem qualquer efeito adverso direto no seu inquilino. Pode esperar as seguintes alterações:
- Opções adicionais de atribuição, tais como ativa vs. elegível com tempo de início e fim quando então faz uma atribuição em funções PIM ou Azure AD e lâmina de administradores. 
- Mecanismos adicionais de deteção, como Unidades Administrativas e funções personalizadas, introduzidos diretamente na experiência de atribuição. 
- Se for um administrador global ou administrador privilegiado, poderá começar a receber alguns e-mails adicionais como a digestão semanal da PIM. 
- Você também pode ver o principal do serviço ms-pim no registo de auditoria relacionado com a atribuição de funções. Esta mudança esperada não deve afetar o seu fluxo de trabalho regular.

 Para obter mais informações, consulte [Começar a utilizar a Gestão de Identidade Privilegiada.](../privileged-identity-management/pim-getting-started.md)

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Azure AD Entitlement Management: O painel seleto de recursos de pacote de acesso mostra agora, por defeito, os recursos atualmente no catálogo selecionado

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Gestão do Acesso ao Utilizador  
**Capacidade do produto:** Gestão de Direitos
 

No fluxo de criação de pacote de acesso, no separador Funções de Recursos, o comportamento do painel Select está a mudar. Atualmente, o comportamento padrão é mostrar todos os recursos que são propriedade do utilizador e recursos adicionados ao catálogo selecionado. 

Esta experiência será alterada para exibir apenas os recursos atualmente adicionados no catálogo por padrão, para que os utilizadores possam facilmente recolher recursos do catálogo. A atualização ajudará na descoberta dos recursos para adicionar aos pacotes de acesso, e reduzirá o risco de adicionar inadvertidamente recursos pertencentes ao utilizador que não fazem parte do catálogo. Para saber mais, consulte [Criar um novo pacote de acesso na gestão de direitos AD Azure.](../governance/entitlement-management-access-package-create.md#resource-roles)
 
---

## <a name="august-2020"></a>Agosto de 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Atualizações para requisitos de firewall do Servidor de Autenticação Multi-Factor Azure

**Tipo:** Plano de mudança  
**Categoria de serviço:** MFA  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
A partir de 1 de outubro de 2020, os requisitos de firewall do Azure MFA Server exigirão gamas IP adicionais.

Se tiver regras de firewall de saída na sua organização, atualize as regras para que os seus servidores MFA possam comunicar com todas as gamas IP necessárias. As gamas IP estão documentadas nos [requisitos de firewall do Servidor de Autenticação Multi-Factor Azure](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Próximas alterações à experiência do utilizador na Identity Secure Score

**Tipo:** Plano de mudança  
**Categoria de serviço:** Capacidade **de Proteção de Identidade: Proteção** & de Segurança de Identidade

Estamos a atualizar o portal Identity Secure Score para alinhar com as alterações introduzidas na [nova versão](/microsoft-365/security/mtp/microsoft-secure-score-whats-new?view=o365-worldwide)do Microsoft Secure Score . 

A versão de pré-visualização com as alterações estará disponível no início de setembro. As alterações na versão de pré-visualização incluem:
- "Identity Secure Score" renomeado para "Secure Score for Identity" para alinhamento da marca com o Microsoft Secure Score
- Pontos normalizados para escala padrão e reportados em percentagens em vez de pontos

Nesta pré-visualização, os clientes podem alternar entre a experiência existente e a nova experiência. Esta pré-estreia vai durar até ao final de novembro de 2020. Após a pré-visualização, os clientes serão automaticamente direcionados para a nova experiência UX.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Novas permissões restritas de acesso a hóspedes em Azure AD - Visualização pública

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Controlo de Acessos   
**Capacidade do produto:** Gestão de Utilizadores

Atualizámos permissões de nível de diretório para utilizadores convidados. Estas permissões permitem que os administradores exijam restrições e controlos adicionais no acesso externo do utilizador do hóspede. Os administradores podem agora adicionar restrições adicionais para o acesso dos hóspedes externos ao perfil do utilizador e dos grupos e à informação de adesão. Com esta funcionalidade de pré-visualização pública, os clientes podem gerir o acesso externo ao utilizador em escala, obstaculando os membros do grupo, incluindo restringir os utilizadores convidados de verem os membros do grupo(s) em que se encontram.

Para saber mais, consulte [permissões de acesso restritos ao hóspede](../enterprise-users/users-restrict-guest-permissions.md) e [permissões por defeito dos utilizadores.](./users-default-permissions.md)
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Disponibilidade geral de consultas delta para os principais serviços

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** MS Graph  
**Capacidade do produto:** Experiência de Desenvolvedor
 
A Microsoft Graph Delta Query suporta agora o tipo de recurso em v1.0:
- Principal de Serviço

Agora os clientes podem acompanhar as alterações a esses recursos de forma eficiente e fornecem a melhor solução para sincronizar alterações a esses recursos com uma loja de dados local. Para aprender a configurar estes recursos numa consulta, consulte [utilizar a consulta delta para acompanhar as alterações nos dados do Microsoft Graph](/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Disponibilidade geral de consultas delta para oAuth2PermissionGrant

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** MS Graph  
**Capacidade do produto:** Experiência de Desenvolvedor

A Microsoft Graph Delta Query suporta agora o tipo de recurso em v1.0:
- OAuth2PermissionGrant

Os clientes podem agora acompanhar as alterações a esses recursos de forma eficiente e fornecem a melhor solução para sincronizar alterações a esses recursos com uma loja de dados local. Para aprender a configurar estes recursos numa consulta, consulte [utilizar a consulta delta para acompanhar as alterações nos dados do Microsoft Graph](/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Novas Aplicações Federadas disponíveis na galeria AZure AD Application - agosto de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros

Em agosto de 2020 adicionámos 25 novas aplicações na nossa galeria app com o apoio da Federação:

[Backup365](https://portal.backup365.io/login), [Soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [Enlyft Dynamics 365 Connector](http://enlyft.com/), [Serraview Space Usetion Software Solutions](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [Uniq,](https://web.uniq.app/) [Visivelmente,](../saas-apps/visibly-tutorial.md) [Zylo](../saas-apps/zylo-tutorial.md), [Edmentum - Avaliações de Cursos Caminho Exato,](https://auth.edmentum.com/elf/login) [CyberLAB,](https://cyberlab.evolvesecurity.com/#/welcome) [Altamira HRM,](../saas-apps/altamira-hrm-tutorial.md) [WireWhe, WireWheel](../saas-apps/wirewheel-tutorial.md), [Zix Compliance and Capture](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [Greenlight Enterprise Business Controls Platform](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md), [Genetec Clearance](https://www.clearance.network/), [iSAMS](../saas-apps/isams-tutorial.md), [VeraSMART](../saas-apps/verasmart-tutorial.md), [Amiko,](https://amiko.web.rivero.app/) [Twingate,](https://auth.twingate.com/signup) [Funnel Leasing,](https://nestiolistings.com/sso/oidc/azure/authorize/) [Scalefusion,](https://scalefusion.com/users/sign_in/) [Bpanda,](https://goto.bpanda.com/login) [Vivun Calendar Control Connect,](https://app.vivun.com/dashboard/calendar/connect) [FortiGate SSL VPN,](../saas-apps/fortigate-ssl-vpn-tutorial.md) [Wandera End User](https://www.wandera.com/)

Também pode encontrar a documentação de todas as aplicações a partir daqui https://aka.ms/AppsTutorial

Para listar a sua aplicação na galeria de aplicações Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Florestas de Recursos já disponíveis para Azure AD DS 

**Tipo:** Nova **categoria de serviço de recurso:** Azure AD Domain Services   
**Capacidade do produto:** Serviços de domínio Azure Ad
 
A capacidade das florestas de recursos nos Serviços de Domínio Ad Azure está agora disponível em geral. Agora pode ativar a autorização sem sincronização de hash de palavra-passe para utilizar os Serviços de Domínio Azure AD, incluindo a autorização do cartão inteligente. Para saber mais, consulte [replica sets conceitos e funcionalidades para Azure Ative Directory Domain Services (pré-visualização)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Suporte regional de réplica para domínios geridos Azure AD já disponível

**Tipo:** Nova funcionalidade   
**Categoria de serviço:** Serviços de domínio Azure Ad  
**Capacidade do produto:** Serviços de domínio Azure Ad
 
Você pode expandir um domínio gerido para ter mais de um conjunto de réplicas por inquilino AZure AD. Os conjuntos de réplicas podem ser adicionados a qualquer rede virtual esprevada em qualquer região do Azure que suporte serviços de domínio AD Azure. Conjuntos de réplicas adicionais em diferentes regiões de Azure proporcionam recuperação geográfica de desastres para aplicações antigas se uma região de Azure ficar offline. Para saber mais, consulte [replica sets conceitos e funcionalidades para Azure Ative Directory Domain Services (pré-visualização)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Disponibilidade Geral de Azure AD Meu Sign-Ins

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Experiências de utilizador final
 
Azure AD My Sign-Ins é uma nova funcionalidade que permite aos utilizadores da empresa rever o seu histórico de inscrições para verificar em qualquer atividade incomum. Além disso, esta funcionalidade permite que os utilizadores finais reportem "This was not me" ou "This was me" em atividades suspeitas. Para saber mais sobre a utilização desta funcionalidade, consulte [a Visualização e pesque a sua recente atividade de início de sposição a partir da página My Sign-Ins](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>SAP SuccessFactors HR o fornecimento de utilizadores impulsionados a Azure AD está agora geralmente disponível

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
Pode agora integrar os SAP SuccessFactors como fonte de identidade autoritária com a Azure AD e automatizar o ciclo de vida de identidade de ponta a ponta usando eventos de RH como novas contratações e rescisões para impulsionar o provisionamento e desprovisionamento de contas em Azure AD. 

Para saber mais sobre como configurar o fornecimento de utilizadores do SAP SuccessFactors ao Azure AD, consulte o tutorial [Configure SAP SuccessFactors para o provisionamento de utilizadores do Ative Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Suporte personalizado de ID Connect MS API para Azure AD B2C

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão da Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C
 
Anteriormente, os fornecedores de ID Connect Open Personalizado só podiam ser adicionados ou geridos através do portal Azure. Agora os clientes Azure AD B2C podem adicionar e geri-los através da versão beta do Microsoft Graph APIs também. Para aprender a configurar este recurso com APIs, consulte [o tipo de recurso IdentityProvider](/graph/api/resources/identityprovider?view=graph-rest-beta).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Atribua funções incorporadas a Azure a grupos de nuvem

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Funções de AD AZure  
**Capacidade do produto:** Controlo de Acessos

Agora pode atribuir funções incorporadas a Azure a grupos de nuvem com esta nova funcionalidade. Por exemplo, pode atribuir a função de Administrador SharePoint a Contoso_SharePoint_Admins grupo. Também pode usar o PIM para fazer do grupo um membro elegível do papel, em vez de conceder acesso permanente. Para aprender a configurar esta funcionalidade, consulte [Use grupos de nuvem para gerir atribuições de funções no Azure Ative Directory (pré-visualização)](../roles/groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Insights Business Leader papel incorporado agora disponível

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Funções de AD AZure  
**Capacidade do produto:** Controlo de Acessos
 
Os utilizadores na função Insights Business Leader podem aceder a um conjunto de dashboards e insights através da [aplicação M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Isto inclui acesso total a todos os dashboards e apresenta insights e funcionalidade de exploração de dados. No entanto, os utilizadores nesta função não têm acesso às definições de configuração do produto, que é da responsabilidade do administrador do Insights. Para saber mais sobre este papel, consulte [permissões de função de administrador no Azure Ative Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Papel incorporado do Administrador de Insights já disponível

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Funções de AD AZure  
**Capacidade do produto:** Controlo de Acessos
 
Os utilizadores na função de Administrador de Insights podem aceder a todo o conjunto de capacidades administrativas na [aplicação M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Um utilizador nesta função pode ler informações de diretórios, monitorizar a saúde do serviço, bilhetes de suporte de ficheiros e aceder aos aspetos de definição do administrador do Insights. Para saber mais sobre este papel, consulte [permissões de função de administrador no Azure Ative Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Administração de aplicações e administração de aplicações em nuvem pode gerir propriedades de extensão de aplicações

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Funções de AD AZure  
**Capacidade do produto:** Controlo de Acessos
 
Anteriormente, apenas o Administrador Global poderia gerir a [propriedade de extensão](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http). Estamos agora a ativar esta capacidade para o Administrador de Aplicações e Administrador de Aplicação cloud também.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MIM 2016 SP2 hotfix 4.6.263.0 e conectores 1.1.1301.0

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Gestor de Identidade da Microsoft  
**Capacidade do produto:** Gestão do ciclo de vida da identidade

Um [pacote de rollup hotfix (build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) está disponível para o Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Este pacote rollup contém atualizações para os componentes MIM CM, Mim Synchronization Manager e PAM. Além disso, os conectores genéricos MIM constroem 1.1.1301.0 inclui atualizações para o conector Graph.

---
 
## <a name="july-2020"></a>Julho de 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Como administrador de TI, quero direcionar as aplicações dos clientes usando o Acesso Condicional

**Tipo:** Plano de mudança   
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
Com a libertação da AG das aplicações do cliente condicionada no Acesso Condicional, as novas políticas serão agora aplicadas por padrão a todas as aplicações do cliente. Isto inclui clientes de autenticação legado. As políticas existentes permanecerão inalteradas, mas o *alternância Sim/Não* será removido das políticas existentes para ver facilmente a que aplicações de clientes são aplicadas pela política. 

Ao criar uma nova política, certifique-se de excluir os utilizadores e contas de serviço que ainda utilizam a autenticação antiga; Se não o fizeres, eles vão estar bloqueados. [Saiba mais](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Próximas correções de conformidade scim

**Tipo:** Plano de mudança  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
O serviço de fornecimento de Azure AD aproveita a norma SCIM para a integração com aplicações. A nossa implementação da norma SCIM está a evoluir, e esperamos fazer alterações ao nosso comportamento em torno da forma como realizamos operações patch, bem como definir a propriedade "ativa" num recurso. [Saiba mais](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>A definição do dono do grupo no portal Admin do Azure será alterada

**Tipo:** Plano de mudança  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração

As definições do proprietário na página de definição geral dos Grupos podem ser configuradas para restringir os privilégios de atribuição do proprietário a um grupo limitado de utilizadores no portal Admin E Painel de Acesso do Azure. Em breve teremos a capacidade de atribuir o privilégio do proprietário do grupo não só nestes dois portais UX, mas também impor a política no backend para fornecer comportamentos consistentes em todos os pontos finais, como o PowerShell e o Microsoft Graph. 

Começaremos a desativar a configuração atual para os clientes que não estão a usá-lo e ofereceremos uma opção para o alcance dos utilizadores para o privilégio do proprietário do grupo nos próximos meses. Para obter orientações sobre a atualização das definições do grupo, consulte editar as informações do seu grupo utilizando [o Azure Ative Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>O Serviço de Registo de Diretórios Ativos da Azure está a terminar o suporte para os TLS 1.0 e 1.1

**Tipo:** Plano de mudança  
**Categoria de serviço:** Registo e Gestão de Dispositivos  
**Capacidade do produto:** Plataforma

Segurança da camada de transporte (TLS) 1.2 e servidores e clientes de atualização irão brevemente comunicar com o Serviço de Registo de Dispositivos de Diretório Ativo Azure. O serviço de registo de dispositivos AD da Azure azure será retirado:
- Em 31 de agosto de 2020, em todas as nuvens soberanas (GCC High, DoD, etc.)
- No dia 30 de outubro de 2020, em todas as nuvens comerciais

[Saiba mais](../devices/reference-device-registration-tls-1-2.md) sobre o TLS 1.2 para o Serviço de Registo AZure AD.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Windows Hello para business sign Ins visíveis em Azure AD Sign Ins

**Tipo:** Fixo  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Relatório de & de Monitorização
 
O Windows Hello for Business permite que os utilizadores finais assinem em máquinas Windows com um gesto (como um PIN ou biométrico). Os administradores da AD AZure podem querer diferenciar o Windows Hello para iniciar sing-ins do Windows de outros insus máximos do Windows como parte da viagem de uma organização à autenticação sem palavras-passe. 

Os administradores podem agora verificar se uma autenticação do Windows usou o Windows Hello for Business, verificando o separador Detalhes de Autenticação de um evento de entrada no Windows no Azure AD Sign-Ins lâmina no portal Azure. As autenticações do Windows Hello for Business incluirão "WindowsHelloForBusiness" no campo Método de Autenticação. Para obter mais informações sobre a interpretação de Sign-In Registos, consulte a [documentação de Registos de Início de Sessão](../reports-monitoring/concept-sign-ins.md).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Correções ao comportamento de eliminação de grupo e melhorias de desempenho

**Tipo:** Fixo  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida da identidade
 
Anteriormente, quando um grupo passou de "in-scope" para "fora de alcance" e um administrador clicou no reinício antes da alteração ser concluída, o objeto de grupo não estava a ser eliminado. Agora, o objeto de grupo será eliminado da aplicação-alvo quando este se descolor (desativado, eliminado, não atribuído ou não passou o filtro de escotagem). [Saiba mais](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Antevisão do Público: Os administradores podem agora adicionar conteúdo personalizado no e-mail aos revisores ao criar uma revisão de acesso

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Comentários de Acesso  
**Capacidade do produto:** Governação da Identidade
 
Quando uma nova revisão de acesso é criada, o revisor recebe um e-mail solicitando-lhes que completem a revisão de acesso. Muitos dos nossos clientes pediram a possibilidade de adicionar conteúdo personalizado ao e-mail, como informações de contacto ou outros conteúdos de suporte adicionais para orientar o revisor. 

Agora disponível na pré-visualização pública, os administradores podem especificar conteúdo personalizado no e-mail enviado aos revisores adicionando conteúdo na secção "avançada" de Azure AD Access Reviews. Para obter orientações sobre a criação de comentários de acesso, consulte [Criar uma revisão de acesso de grupos e aplicações em avaliações de acesso AZure](../governance/create-access-review.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Fluxo de código de autorização para aplicações de página única disponíveis

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Experiência de Desenvolvedor
 
Devido às restrições modernas de cookies do navegador 3º partido, como o Safari ITP, as SPAs terão de utilizar o fluxo de código de autorização em vez do fluxo implícito para manter o SSO, e MSAL.js v 2.x irá agora suportar o fluxo de código de autorização. 

Existem atualizações correspondentes ao portal Azure para que possa atualizar o seu SPA para ser tipo "spa" e utilizar o fluxo de código auth. Consulte [o Sinal nos utilizadores e obtenha um token de acesso num SPA JavaScript utilizando o fluxo de código auth](../develop/quickstart-v2-javascript-auth-code.md) para obter mais orientações.
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD Application Proxy agora suporta o Cliente Web de Serviços de Ambiente de Trabalho Remoto

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** App Proxy  
**Capacidade do produto:** Controlo de Acessos

O Azure AD Application Proxy suporta agora o Cliente Web de Serviços de Ambiente de Trabalho Remoto (RDS). O cliente web RDS permite que os utilizadores acedam à infraestrutura de ambiente de trabalho remoto através de qualquer navegador HTLM5 capaz de ser como o Microsoft Edge, o Internet Explorer 11, o Google Chrome, etc. Os utilizadores podem interagir com aplicações remotas ou desktops como fariam com um dispositivo local de qualquer lugar. Ao utilizar o Azure AD Application Proxy, pode aumentar a segurança da sua implementação de RDS, aplicando políticas de pré-autenticação e acesso condicional para todos os tipos de aplicações de clientes ricos. Para obter orientação, consulte [publicar o Ambiente de Trabalho Remoto com o Azure AD Application Proxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Próxima geração Azure AD B2C fluxos de utilizadores em visualização pública

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão da Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C
 
A experiência de fluxo de utilizador simplificada oferece paridade de funcionalidades com funcionalidades de pré-visualização e é a casa para todas as novidades. Os utilizadores poderão ativar novas funcionalidades dentro do mesmo fluxo de utilizadores, reduzindo a necessidade de criar várias versões a cada nova funcionalidade. Por último, o novo UX fácil de utilizar simplifica a seleção e criação de fluxos de utilizadores. Experimente agora [criando um fluxo de utilizador.](../../active-directory-b2c/tutorial-create-user-flows.md) 

Para obter mais informações sobre os fluxos dos utilizadores, consulte as [versões de fluxo do utilizador no Azure Ative Directory B2C](../../active-directory-b2c/user-flow-versions.md#:~:text=    User flow  ,account. Usi ...  1 more rows ).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Novas Aplicações Federadas disponíveis na galeria AZure AD Application - julho 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos da Empresa  
**Capacidade do produto:** Integração de Terceiros
 
Em julho de 2020 adicionámos 55 novas aplicações na nossa galeria app com o apoio da Federação:

[Palmas](http://www.rmit.com.ar/)Palmas , [Appreiz](https://microsoftteams.appreiz.com/), [Inextor Vault](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect,](https://app.templafy.com/) [Rececionista PeterConnects,](https://msteams.peterconnects.com/) [AlohaCloud,](https://appfusions.alohacloud.com/auth) [Control Tower,](https://bpm.tnxcorp.com/sso/microsoft) [Cocoom,](https://start.cocoom.com/) [COINS Construction Cloud,](https://sso.coinsconstructioncloud.com/#login/) [Medxnote MT,](https://task.teamsmain.medx.im/authorization) [Reflekt,](https://reflekt.konsolute.com/login) [Rever,](https://app.reverscore.net/access) [MyCompanyArchive,](https://login.mycompanyarchive.com/) [GRem sines,](https://app.greminders.com/o365-oauth) [Titanfile](../saas-apps/titanfile-tutorial.md), [Wootric](../saas-apps/wootric-tutorial.md), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US),  [OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md), [Datasite](../saas-apps/datasite-tutorial.md), [BlogIn](../saas-apps/blogin-tutorial.md), [IntSights](../saas-apps/intsights-tutorial.md), [kpifire,](../saas-apps/kpifire-tutorial.md) [Textline](../saas-apps/textline-tutorial.md), [Cloud Academy - SSO,](../saas-apps/cloud-academy-sso-tutorial.md) [Community Spark,](../saas-apps/community-spark-tutorial.md) [Chatwork,](../saas-apps/chatwork-tutorial.md) [CloudSign](../saas-apps/cloudsign-tutorial.md), [C3M Cloud Control,](../saas-apps/c3m-cloud-control-tutorial.md) [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Michigan Data Hub Single Sign-On](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [Egress](../saas-apps/egress-tutorial.md), [SendSafe,](../saas-apps/sendsafely-tutorial.md) [Eletive,](https://app.eletive.com/) [Right-Hand Cybersecurity ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [Verme](../saas-apps/verme-tutorial.md), [Lenses.io](../saas-apps/lensesio-tutorial.md), [Momenta](../saas-apps/momenta-tutorial.md), [Uprise](https://app.uprise.co/sign-in), [Q,](https://q.moduleq.com/login) [CloudCords](../saas-apps/cloudcords-tutorial.md), [TellMe Bot](https://tellme365liteweb.azurewebsites.net/), [Inspire](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML Connector,](https://www.strata.io/identity-fabric/) [Smartschool (Sistema de Gestão Escolar)](https://smartschoolz.com/login), [Zepto - Cronometragem Inteligente,](https://user.zepto-ai.com/signin) [Studi.ly,](https://studi.ly/) [Trackplan,](http://www.trackplanfm.com/) [Skedda,](../saas-apps/skedda-tutorial.md) [WhosOnLocation,](../saas-apps/whos-on-location-tutorial.md) [Coggle,](../saas-apps/coggle-tutorial.md) [Kemp LoadMaster,](https://kemptechnologies.com/cloud-load-balancer/) [BrowserStack Single Sign-on](../saas-apps/browserstack-single-sign-on-tutorial.md)

Também pode encontrar a documentação de todas as aplicações a partir daqui https://aka.ms/AppsTutorial

Para listar a sua aplicação na galeria de aplicações Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Novos conectores de provisionamento na Galeria de Aplicações AD Azure - julho 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Integração de Terceiros

Agora pode automatizar a criação, atualização e eliminação de contas de utilizador para a nova aplicação integrada [LinkedIn Learning.](../saas-apps/linkedin-learning-provisioning-tutorial.md)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte [automatizar o fornecimento de utilizadores para aplicações SaaS com Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Ver atribuições de funções em todos os âmbitos e capacidade de descarregá-las para um ficheiro csv

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Funções de AD AZure  
**Capacidade do produto:** Controlo de Acessos
 
Agora pode ver atribuições de funções em todos os âmbitos para um papel no separador "Papéis e administradores" no portal AD AZure. Também pode descarregar essas atribuições de funções para cada papel num ficheiro CSV. Para obter orientações sobre a visualização e adição de atribuições de funções, consulte [Ver e atribuir funções de administrador no Azure Ative Directory](../roles/manage-roles-portal.md).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Azure Multi-Factor Authentication Software Development (Azure MFA SDK) Depreciação

**Tipo:** Precatado  
**Categoria de serviço:** MFA  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
O Azure Multi-Factor Authentication Software Development (Azure MFA SDK) chegou ao fim da vida em 14 de novembro de 2018, como foi anunciado pela primeira vez em novembro de 2017. A Microsoft vai encerrar o serviço SDK a partir de 30 de setembro de 2020. Quaisquer chamadas feitas para o SDK falharão.

Se a sua organização estiver a usar o Azure MFA SDK, tem de migrar até 30 de setembro de 2020:
- Azure MFA SDK para MIM: Se utilizar o SDK com MIM, deve migrar para o Azure MFA Server e ativar a Gestão de Acesso Privilegiado (PAM) seguindo estas [instruções.](/microsoft-identity-manager/working-with-mfaserver-for-mim)   
- Azure MFA SDK para aplicações personalizadas: Considere integrar a sua app em Azure AD e utilizar o Acesso Condicional para impor o MFA. Para começar, reveja esta [página.](../manage-apps/plan-an-application-integration.md) 

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
 

As propriedades externalUserState e externalUserStateChangedDateTime podem ser usadas para encontrar hóspedes B2B convidados que ainda não aceitaram os seus convites, bem como construir automatização, como eliminar utilizadores que não aceitaram os seus convites após alguns dias. Estas propriedades estão agora disponíveis no MS Graph v1. Para obter orientações sobre a utilização destas propriedades, consulte o [tipo de recurso do Utilizador](/graph/api/resources/user?view=graph-rest-1.0).
 
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
 
Uma nova permissão delegada EntitlementManagement.Read.All está agora disponível para uso com a API de Gestão de Direitos na versão beta do Microsoft Graph. Para saber mais sobre as APIs disponíveis, consulte [Trabalhar com a Azure AD gestão de direitos API.](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)

---

### <a name="identity-protection-apis-available-in-v10"></a>APIs de Proteção de Identidade disponíveis em v1.0

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Proteção & de Segurança de Identidade
 
Os riskyUsers e riskDetections Microsoft Graph APIs estão agora geralmente disponíveis. Agora que estão disponíveis no ponto final v1.0, convidamo-lo a usá-los em produção. Para mais informações, consulte os docs do [Microsoft Graph](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0).
 
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
 
Os administradores de TI podem começar a usar o novo papel de "Hybrid Admin" como o papel menos privilegiado para a criação do Azure ADConnect Cloud Provisioning. Com este novo papel, já não tens de usar o papel da Administração Global para configurar e configurar o Cloud Provisioning. [Saiba mais](../roles/delegate-by-task.md#connect).
 
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

[O modo apenas de reporte para acesso condicionado Azure AD](../conditional-access/concept-conditional-access-report-only.md) permite-lhe avaliar o resultado de uma política sem impor controlos de acesso. Pode testar políticas apenas de relatórios em toda a sua organização e compreender o seu impacto antes de as permitir, tornando a implementação mais segura e fácil. Ao longo dos últimos meses, temos visto uma forte adoção do modo apenas de relatórios - mais de 26M utilizadores já estão no âmbito de uma política apenas de relatório. Com o anúncio de hoje, novas políticas de acesso condicionado Azure AD serão criadas em modo apenas de relatório por padrão. Isto significa que pode monitorizar o impacto das suas políticas a partir do momento em que são criadas. E para aqueles que usam as APIs do MS Graph, também pode [gerir políticas de relatórios programáticas.](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) 

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
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial.md)

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

**Tipo:** Categoria de serviço de funcionalidade **alterada:** Capacidade do produto de autenticação: Experiência do desenvolvedor **Product capability:**

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
