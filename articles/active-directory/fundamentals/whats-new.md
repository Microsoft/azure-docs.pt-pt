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
ms.date: 12/10/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23e48787162bccbf45e420f2deb002879c72fa09
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978889"
---
# <a name="whats-new-in-azure-active-directory"></a>O que há de novo no Azure Active Directory?

>Seja notificado sobre quando revisitar esta página para obter as atualizações copiando e colando esta URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` em seu ícone do leitor de feed de RSS da ![](./media/whats-new/feed-icon-16x16.png) leitor de feeds.

O Azure AD recebe melhorias de forma contínua. Para manter-se atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- Versões mais recentes
- Problemas conhecidos
- Correções de erros
- Funcionalidades preteridas
- Planos para que as alterações

Esta página é atualizada mensalmente, por isso, revisitá-lo regularmente. Se você estiver procurando itens com mais de seis meses, poderá encontrá-los no [arquivo morto para as novidades no Azure Active Directory](whats-new-archive.md).

---
## <a name="december-2019"></a>Dezembro de 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrar o provisionamento do SAP SuccessFactors ao Azure AD e ao AD local (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** aprovisionamento de aplicações  
**Capacidade de produto:** gerenciamento de ciclo de vida de identidade

Agora você pode integrar o SAP SuccessFactors como uma fonte de identidade autoritativa no Azure AD. Essa integração ajuda a automatizar o ciclo de vida de identidade de ponta a ponta, incluindo o uso de eventos baseados em RH, como novas contratações ou encerramentos, para controlar o provisionamento de contas do Azure AD.

Para obter mais informações sobre como configurar o provisionamento de entrada do SAP SuccessFactors para o Azure AD, consulte o tutorial [Configurar provisionamento automático do SAP SuccessFactors](https://aka.ms/SAPSuccessFactorsInboundTutorial) .

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Suporte para emails personalizados no Azure AD B2C (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gestão de identidades de consumidor  
**Capacidade de produto:** B2B/B2C

Agora você pode usar Azure AD B2C para criar emails personalizados quando os usuários se inscrevem para usar seus aplicativos. Usando o DisplayControls (atualmente em visualização) e um provedor de email de terceiros (como, [SendGrid](https://sendgrid.com/), [SPARKPOST](https://sparkpost.com/)ou uma API REST personalizada), você pode usar seu próprio modelo de email, endereço e texto **de** assunto, bem como a localização de suporte e configurações de senha de uso único (OTP) personalizadas.

Para obter mais informações, consulte [verificação de email personalizada em Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Substituição de políticas de linha de base com padrões de segurança

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** outros  
**Funcionalidade do produto:** Segurança e proteção de identidade

Como parte de um modelo seguro por padrão para autenticação, estamos removendo as políticas de proteção de linha de base existentes de todos os locatários. Essa remoção é destinada à conclusão no final de fevereiro. A substituição para essas políticas de proteção de linha de base é o padrão de segurança. Se você estiver usando políticas de proteção de linha de base, deverá planejar a movimentação para a nova política de padrões de segurança ou para o acesso condicional. Se você não usou essas políticas, não há nenhuma ação a ser tomada.

Para obter mais informações sobre os novos padrões de segurança, consulte [o que são os padrões de segurança?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Para obter mais informações sobre políticas de acesso condicional, consulte [políticas de acesso condicional comum](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## <a name="november-2019"></a>Novembro de 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Suporte para o atributo SameSite e Chrome 80

**Tipo:** plano de alteração  
**Categoria de serviço:** autenticações (inícios de sessão)  
**Capacidade de produto:** autenticação de utilizador

Como parte de um modelo seguro por padrão para cookies, o navegador Chrome 80 está alterando como ele trata cookies sem o atributo `SameSite`. Qualquer cookie que não especifique o atributo `SameSite` será tratado como se estivesse definido como `SameSite=Lax`, o que resultará no cromo bloquear determinados cenários de compartilhamento de cookies entre domínios em que seu aplicativo possa depender. Para manter o comportamento mais antigo do Chrome, você pode usar o atributo `SameSite=None` e adicionar um atributo `Secure` adicional, de modo que os cookies entre sites só possam ser acessados por conexões HTTPS. O Chrome está agendado para concluir essa alteração até 4 de fevereiro de 2020.

Recomendamos que todos os nossos desenvolvedores testem seus aplicativos usando estas diretrizes:

- Defina o valor padrão para a configuração **usar cookie seguro** como **Sim**.

- Defina o valor padrão para o atributo **SameSite** como **None**.

- Adicione um atributo de `SameSite` adicional de **Secure**.

Para obter mais informações, consulte [próximas alterações de cookie de SameSite em ASP.net e ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) e [potencial interrupção em sites de clientes e produtos e serviços da Microsoft no Chrome versão 79 e posterior](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Novo hotfix para Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Tipo:** fixo  
**Categoria de serviço:** Microsoft Identity Manager  
**Capacidade de produto:** gerenciamento de ciclo de vida de identidade

Um pacote cumulativo de atualizações de hotfix (Build 4.6.34.0) está disponível para Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Esse pacote cumulativo resolve problemas e adiciona melhorias que são descritas na seção "problemas corrigidos e aprimoramentos adicionados nesta atualização".

Para obter mais informações e baixar o pacote de hotfix, consulte [Microsoft Identity Manager 2016 Service Pack 2 (Build 4.6.34.0). o pacote cumulativo de atualizações está disponível](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Novo relatório de atividade do aplicativo AD FS para ajudar a migrar aplicativos para o Azure AD (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** aplicações empresariais  
**Capacidade de produto:** SSO

Use o novo relatório de atividade de aplicativo Serviços de Federação do Active Directory (AD FS) (AD FS), no portal do Azure, para identificar quais dos seus aplicativos podem ser migrados para o Azure AD. O relatório avalia todos os aplicativos AD FS para compatibilidade com o Azure AD, verifica se há problemas e fornece orientação sobre como preparar aplicativos individuais para migração.

Para obter mais informações, consulte [usar o relatório de atividade do aplicativo AD FS para migrar aplicativos para o Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Novo fluxo de trabalho para os usuários solicitarem o consentimento do administrador (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** aplicações empresariais  
**Capacidade de produto:** controlo de acesso

O novo fluxo de trabalho de consentimento do administrador fornece aos administradores uma maneira de conceder acesso a aplicativos que exigem aprovação de administrador. Se um usuário tentar acessar um aplicativo, mas não puder fornecer consentimento, ele agora poderá enviar uma solicitação de aprovação de administrador. A solicitação é enviada por email e colocada em uma fila que pode ser acessada pelo portal do Azure, para todos os administradores que foram designados como revisores. Depois que um revisor executa uma ação em uma solicitação pendente, os usuários solicitantes são notificados sobre a ação.

Para obter mais informações, consulte [Configurar o fluxo de trabalho de consentimento do administrador (versão prévia)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nova experiência de configuração de token de registros de Aplicativo Azure AD para gerenciar declarações opcionais (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** outros  
**Capacidade de produto:** experiência do desenvolvedor

A folha de configuração do novo **token de registros de aplicativo Azure ad** no portal do Azure agora mostra aos desenvolvedores de aplicativos uma lista dinâmica de declarações opcionais para seus aplicativos. Essa nova experiência ajuda a simplificar as migrações de aplicativo do Azure AD e a minimizar as configurações incorretas de declarações opcionais.

Para obter mais informações, consulte [fornecer declarações opcionais para seu aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Novo fluxo de trabalho de aprovação de dois estágios no gerenciamento de direitos do Azure AD (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** outros  
**Funcionalidade do produto:** Gerenciamento de direitos

Apresentamos um novo fluxo de trabalho de aprovação de dois estágios que permite que você exija que dois aprovadores aprovem a solicitação de um usuário para um pacote do Access. Por exemplo, você pode defini-lo para que o gerente do usuário solicitante deva primeiro aprovar e, em seguida, você também pode exigir que um proprietário do recurso aprove. Se um dos aprovadores não aprovar, o acesso não será concedido.

Para obter mais informações, consulte [alterar as configurações de solicitação e aprovação para um pacote de acesso no gerenciamento de direitos do Azure ad](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Atualizações para a página meus aplicativos junto com novos espaços de trabalho (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** as minhas aplicações  
**Capacidade de produto:** 3º integração de terceiros

Agora você pode personalizar a maneira como os usuários da sua organização exibem e acessam a experiência dos meus aplicativos atualizados. Essa nova experiência também inclui o novo recurso de espaços de trabalho, o que torna mais fácil para os usuários localizar e organizar aplicativos.

Para obter mais informações sobre a nova experiência meus aplicativos e criar espaços de trabalho, consulte [criar espaços de trabalho no portal meus aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Suporte de ID do Google social para colaboração B2B do Azure AD (disponibilidade geral)

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Capacidade de produto:** autenticação de utilizador

O novo suporte para o uso de IDs sociais do Google (contas do Gmail) no Azure AD ajuda a tornar a colaboração mais simples para seus usuários e parceiros. Não há mais necessidade de que seus parceiros criem e gerenciem uma nova conta específica da Microsoft. O Microsoft Teams agora dá suporte completo aos usuários do Google em todos os clientes e nos pontos de extremidade de autenticação comuns e relacionados ao locatário.

Para obter mais informações, consulte [Adicionar o Google como um provedor de identidade para usuários de convidado B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Suporte móvel do Microsoft Edge para acesso condicional e logon único (disponibilidade geral)

**Tipo:** novo recurso  
**Categoria de serviço:** acesso condicional  
**Capacidade de produto:** identidade de segurança e proteção

O Azure AD para Microsoft Edge no iOS e Android agora dá suporte ao logon único do Azure AD e ao acesso condicional:

- **Logon único (SSO) do Microsoft Edge:** O logon único agora está disponível entre clientes nativos (como o Microsoft Outlook e o Microsoft Edge) para todos os aplicativos conectados ao Azure AD.

- **Acesso condicional do Microsoft Edge:** Por meio de políticas de acesso condicional baseadas em aplicativo, os usuários devem usar navegadores protegidos por Microsoft Intune, como o Microsoft Edge.

Para obter mais informações sobre o acesso condicional e o SSO com o Microsoft Edge, consulte a postagem do blog de [suporte móvel do Microsoft Edge para acesso condicional e logon único agora disponível](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) . Para obter mais informações sobre como configurar seus aplicativos cliente usando o [acesso condicional baseado em aplicativo](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) ou o [acesso condicional baseado em dispositivo](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices), consulte Gerenciar o [acesso à Web usando um navegador Microsoft Intune protegido por política](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Gerenciamento de direitos do Azure AD (disponibilidade geral)

**Tipo:** novo recurso  
**Categoria de serviço:** outros  
**Funcionalidade do produto:** Gerenciamento de direitos

O gerenciamento de direitos do Azure AD é um novo recurso de governança de identidade, que ajuda as organizações a gerenciar o ciclo de vida de identidade e acesso em escala. Esse novo recurso ajuda a automatizar fluxos de trabalho de solicitação de acesso, atribuições de acesso, análises e expiração entre grupos, aplicativos e sites do SharePoint Online.

Com o gerenciamento de direitos do Azure AD, você pode gerenciar o acesso de forma mais eficiente para funcionários e também para usuários fora da sua organização que precisam de acesso a esses recursos.

Para obter mais informações, consulte [o que é o gerenciamento de direitos do Azure ad?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o provisionamento de conta de usuário para esses aplicativos SaaS recentemente suportados

**Tipo:** novo recurso  
**Categoria de serviço:** aplicações empresariais  
**Capacidade de produto:** 3º integração de terceiros  

Agora você pode automatizar a criação, atualização e exclusão de contas de usuário para esses aplicativos integrados recentemente:

[Serviço de autenticação de identidade da SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [matriz de prioridade](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor sua organização usando o provisionamento automatizado de contas de usuário, confira automatizar o [provisionamento de usuários para aplicativos SaaS com o Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – novembro de 2019

**Tipo:** novo recurso  
**Categoria de serviço:** aplicações empresariais  
**Capacidade de produto:** 3º integração de terceiros

Em novembro de 2019, adicionamos esses 21 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

O [HootSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial) [, o](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial) [acesso azul para Membros (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal SSO (logon único)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), Motus, [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [BlueMail](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [](https://loginself1.bluemail.me/) [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets Idea & o gerenciamento de inovação](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope User Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), uniFLOW online [, Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) [](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial) , [JISC Student eleitor Registration](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Galeria de aplicativos novos e aprimorados do Azure AD

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** aplicações empresariais  
**Capacidade de produto:** SSO

Atualizamos a Galeria de aplicativos do Azure AD para facilitar a localização de aplicativos previamente integrados que dão suporte ao provisionamento, ao OpenID Connect e ao SAML em seu locatário Azure Active Directory.

Para obter mais informações, consulte [Adicionar um aplicativo ao seu locatário Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Maior limite de tamanho de definição de função de aplicativo de 120 a 240 caracteres

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** aplicações empresariais  
**Capacidade de produto:** SSO

Ouvimos clientes de que o limite de comprimento para o valor de definição de função de aplicativo em alguns aplicativos e serviços é muito curto em 120 caracteres. Em resposta, aumentamos o comprimento máximo da definição do valor da função para 240 caracteres.

Para obter mais informações sobre como usar definições de função específicas do aplicativo, consulte [adicionar funções de aplicativo em seu aplicativo e recebê-las no token](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---

## <a name="october-2019"></a>Outubro de 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Substituição da API identityRiskEvent para Azure AD Identity Protection as detecções de risco  

**Tipo:** plano de alteração  
**Categoria de serviço:** Identity Protection  
**Capacidade de produto:** identidade de segurança e proteção

Em resposta aos comentários do desenvolvedor, os assinantes do Azure AD Premium P2 agora podem executar consultas complexas nos dados de detecção de risco Azure AD Identity Protection usando a nova API do riskDetection para Microsoft Graph. A versão beta da API do [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) existente deixará de retornar dados em até **10 de janeiro de 2020**. Se sua organização estiver usando a API identityRiskEvent, você deverá fazer a transição para a nova API riskDetection.

Para obter mais informações sobre a nova API do riskDetection, consulte a [documentação de referência de API de detecção de risco](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Suporte ao proxy de aplicativo para o atributo SameSite e Chrome 80

**Tipo:** plano de alteração  
**Categoria de serviço:** do Proxy de aplicações  
**Capacidade de produto:** controlo de acesso

Algumas semanas antes da versão do navegador Chrome 80, planejamos atualizar como os cookies de proxy de aplicativo tratam o atributo **SameSite** . Com o lançamento do Chrome 80, qualquer cookie que não especifique o atributo **SameSite** será tratado como se estivesse definido como `SameSite=Lax`.

Para ajudar a evitar impactos potencialmente negativos devido a essa alteração, estamos atualizando o acesso do proxy de aplicativo e cookies de sessão por:

- Definindo o valor padrão para a configuração **usar cookie seguro** como **Sim**.

- Definindo o valor padrão para o atributo **SameSite** como **None**.

    >[!NOTE]
    > Os cookies de acesso ao proxy de aplicativo sempre foram transmitidos exclusivamente por canais seguros. Essas alterações se aplicam somente a cookies de sessão.

Para obter mais informações sobre as configurações de cookie do proxy de aplicativo, consulte [configurações de cookie para acessar aplicativos locais no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Registros de aplicativo (Herdado) e o gerenciamento de aplicativo convergido do apps.dev.microsoft.com (portal de registro de aplicativos) não estarão mais disponíveis

**Tipo:** plano de alteração  
**Categoria de serviço:** n/d  
**Capacidade de produto:** experiência do desenvolvedor

Em breve, os usuários com contas do Azure AD não poderão mais registrar e gerenciar aplicativos convergidos usando o apps.dev.microsoft.com (portal de registro de aplicativos) ou registrar e gerenciar aplicativos no Registros de aplicativo (Herdado) experiência no portal do Azure.

Para saber mais sobre a nova experiência de Registros de aplicativo, consulte o [registros de aplicativo no guia de treinamento de portal do Azure](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Os usuários não precisam mais se registrar novamente durante a migração do MFA por usuário para MFA baseada em acesso condicional

**Tipo:** fixo  
**Categoria de serviço:** FATO  
**Capacidade de produto:** identidade de segurança e proteção

Corrigimos um problema conhecido no qual quando os usuários eram solicitados a se registrar novamente se eles estivessem desabilitados para MFA (autenticação multifator) por usuário e, em seguida, habilitados para MFA por meio de uma política de acesso condicional.

Para exigir que os usuários se registrem novamente, você pode selecionar a opção **reregistrar o MFA necessária** nos métodos de autenticação do usuário no portal do Azure AD. Para obter mais informações sobre como migrar usuários do MFA por usuário para MFA baseada em acesso condicional, consulte [converter usuários de MFA por usuário para MFA baseada em acesso condicional](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Novos recursos para transformar e enviar declarações em seu token SAML

**Tipo:** novo recurso  
**Categoria de serviço:** aplicações empresariais  
**Capacidade de produto:** SSO

Adicionamos recursos adicionais para ajudá-lo a personalizar e enviar declarações em seu token SAML. Esses novos recursos incluem:

- Funções de transformação de declarações adicionais, ajudando a modificar o valor que você envia na declaração.

- Capacidade de aplicar várias transformações a uma única declaração.

- Capacidade de especificar a origem da declaração, com base no tipo de usuário e no grupo ao qual o usuário pertence.

Para obter informações detalhadas sobre esses novos recursos, incluindo como usá-los, consulte [Personalizar declarações emitidas no token SAML para aplicativos empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nova página minhas entradas para usuários finais no Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (inícios de sessão)  
**Capacidade de produto:** monitoramento e relatório

Adicionamos uma nova página **minhas** entradas (https://mysignins.microsoft.com) para permitir que os usuários da sua organização exibam seu histórico de entrada recente para verificar se há atividades incomuns. Essa nova página permite que os usuários vejam:

- Se alguém estiver tentando adivinhar sua senha.

- Se um invasor tiver entrado com êxito em sua conta e a partir de qual local.

- Quais aplicativos o invasor tentou acessar.

Para obter mais informações, consulte os [usuários agora podem verificar seu histórico de entrada para o blog de atividades incomuns](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migração de Azure AD Domain Services (AD DS do Azure) do clássico para Azure Resource Manager redes virtuais

**Tipo:** novo recurso  
**Categoria de serviço:** Azure AD Domain Services  
**Capacidade de produto:** Azure AD Domain Services

Para nossos clientes que ficaram presos em redes virtuais clássicas, temos excelentes notícias para você! Agora você pode executar uma migração única de uma rede virtual clássica para uma rede virtual do Resource Manager existente. Depois de mudar para a rede virtual do Resource Manager, você poderá aproveitar os recursos adicionais e atualizados, como políticas de senha refinadas, notificações por email e logs de auditoria.

Para obter mais informações, consulte [Preview-migrar Azure AD Domain Services do modelo de rede virtual clássica para o Gerenciador de recursos](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Atualizações para o layout do contrato de página Azure AD B2C

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gestão de identidades de consumidor  
**Capacidade de produto:** B2B/B2C

Apresentamos algumas novas alterações na versão 1.2.0 do contrato de página para Azure AD B2C. Nesta versão atualizada, agora você pode controlar a ordem de carga para seus elementos, o que também pode ajudar a parar a cintilação que acontece quando a folha de estilos (CSS) é carregada.

Para obter uma lista completa das alterações feitas no contrato de página, consulte o [log de alterações de versão](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Atualizar para a página meus aplicativos junto com novos espaços de trabalho (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** as minhas aplicações  
**Capacidade de produto:** controlo de acesso

Agora você pode personalizar a maneira como os usuários da sua organização exibem e acessam a experiência da nova empresa de aplicativos, incluindo o uso do novo recurso de espaços de trabalho para facilitar a localização de aplicativos. A nova funcionalidade de espaços de trabalho atua como um filtro para os aplicativos aos quais os usuários da sua organização já têm acesso.

Para obter mais informações sobre como distribuir a nova experiência meus aplicativos e criar espaços de trabalho, consulte [criar espaços de trabalho no portal meus aplicativos (versão prévia)](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Suporte para o modelo de cobrança baseado no usuário ativo mensal (disponibilidade geral)

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gestão de identidades de consumidor  
**Capacidade de produto:** B2B/B2C

Azure AD B2C agora dá suporte à cobrança MAU (usuários ativos mensais). A cobrança do MAU é baseada no número de usuários exclusivos com atividade de autenticação durante um mês civil. Os clientes existentes podem alternar para esse novo método de cobrança a qualquer momento.

A partir de 1º de novembro de 2019, todos os novos clientes serão automaticamente cobrados usando esse método. Esse método de cobrança beneficia os clientes por meio de benefícios de custo e a capacidade de planejar com antecedência.

Para obter mais informações, consulte [atualizar para o modelo de cobrança de usuários ativos mensais](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – outubro de 2019

**Tipo:** novo recurso  
**Categoria de serviço:** aplicações empresariais  
**Capacidade de produto:** 3º integração de terceiros

Em outubro de 2019, adicionamos esses 35 novos aplicativos com suporte de Federação à galeria de aplicativos:

[No caso de crise – móvel](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [intacto](https://tact.ai/assistant/), [OpusCapita caixa Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [dynaTrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [ECornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [contently](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue Coordinate – UE](https://www.hirevue.com/), [HireVue Coordinate-USOnly](https://www.hirevue.com/), [HireVue Coordinate-US](https://www.hirevue.com/), [WittyParrot Caixa de conhecimento](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [cambium Xirrus EasyPass portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [sorte de email!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [teamie](https://theteamie.com/), [Velocity for](https://velocity.peakup.org/teams/login)Teams [, SIGNL4,](https://account.signl4.com/manage) [EAB Navigate impl](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [ômega Point](https://pi.ompnt.com/), [falando de email para o Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [falando email para Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome de navegação](https://ihealthnav.com/account/signin)para o sistema, [ Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Item de menu de segurança consolidado no portal do Azure AD

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** Identity Protection  
**Capacidade de produto:** identidade de segurança e proteção

Agora você pode acessar todos os recursos de segurança do Azure AD disponíveis no novo item de menu **segurança** e, na barra de **pesquisa** , na portal do Azure. Além disso, a nova página de aterrissagem de **segurança** , chamada **introdução à segurança**, fornecerá links para nossa documentação pública, diretrizes de segurança e guias de implantação.

O novo menu de **segurança** inclui:

- Acesso Condicional
- Proteção de Identidade
- Centro de Segurança
- Pontuação segura de identidade
- Métodos de autenticação
- MFA
- Relatórios de risco-usuários arriscados, entradas arriscadas, detecções de risco
- E muito mais...

Para obter mais informações, consulte [segurança-introdução](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Política de expiração dos grupos do Office 365 aprimorada com renovação automática

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** grupo de gestão  
**Capacidade de produto:** gerenciamento de ciclo de vida de identidade

A política de expiração dos grupos do Office 365 foi aprimorada para renovar automaticamente os grupos que estão ativamente em uso por seus membros. Os grupos serão renovados com base na atividade do usuário em todos os aplicativos do Office 365, incluindo o Outlook, o SharePoint e as equipes.

Esse aprimoramento ajuda a reduzir as notificações de expiração do grupo e ajuda a garantir que os grupos ativos continuem disponíveis. Se você já tiver uma política de expiração ativa para seus grupos do Office 365, não precisará fazer nada para ativar essa nova funcionalidade.

Para obter mais informações, consulte [Configurar a política de expiração para grupos do Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Experiência de criação de Azure AD Domain Services (AD DS do Azure) atualizada

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** Azure AD Domain Services  
**Capacidade de produto:** Azure AD Domain Services

Atualizamos Azure AD Domain Services (AD DS do Azure) para incluir uma nova e aprimorada experiência de criação, ajudando você a criar um domínio gerenciado em apenas três cliques! Além disso, agora você pode carregar e implantar o Azure AD DS de um modelo.

Para obter mais informações, consulte [tutorial: criar e configurar uma instância de Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---

## <a name="september-2019"></a>Setembro de 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Planejar alteração: substituição dos pacotes de conteúdo do Power BI

**Tipo:** plano de alteração  
**Categoria de serviço:** relatórios  
**Capacidade de produto:** monitoramento e relatório

A partir de 1º de outubro de 2019, Power BI começará a substituir todos os pacotes de conteúdo, incluindo o pacote de conteúdo do Azure AD Power BI. Como alternativa para esse pacote de conteúdo, você pode usar pastas de trabalho do Azure AD para obter informações sobre seus serviços relacionados ao Azure AD. Pastas de trabalho adicionais são disponibilizadas, incluindo pastas de trabalho sobre políticas de acesso condicional no modo somente de relatório, informações baseadas em consentimento do aplicativo e muito mais.

Para obter mais informações sobre as pastas de trabalho, consulte [como usar pastas de trabalho do Azure monitor para relatórios Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Para obter mais informações sobre a substituição dos pacotes de conteúdo, consulte a postagem do blog [anunciando Power bi aplicativos de modelo de disponibilidade geral](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Meu perfil está renomeando e integrando com a página da conta Microsoft Office

**Tipo:** plano de alteração  
**Categoria de serviço:** Meu perfil/conta  
**Capacidade de produto:** colaboração

A partir de outubro, a minha experiência de perfil se tornará minha conta. Como parte dessa alteração, em todos os lugares que, no momento, **o meu perfil** será alterado para **minha conta**. Além da alteração de nomenclatura e de algumas melhorias de design, a experiência atualizada oferecerá integração adicional com a página de Microsoft Office conta. Especificamente, você poderá acessar as instalações e assinaturas do Office na página da conta de **visão geral** , juntamente com as preferências de contato relacionadas ao Office na página de **privacidade** .

Para obter mais informações sobre a experiência meu perfil (versão prévia), consulte [visão geral do portal meu perfil (versão prévia)](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Gerenciar grupos e membros em massa usando arquivos CSV no portal do AD do Azure (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** grupo de gestão  
**Capacidade de produto:** colaboração

Temos o prazer de anunciar a disponibilidade de visualização pública das experiências de gerenciamento de grupo em massa no portal do Azure AD. Agora você pode usar um arquivo CSV e o portal do AD do Azure para gerenciar grupos e listas de membros, incluindo:

- Adicionando ou removendo membros de um grupo.

- Baixando a lista de grupos do diretório.

- Baixando a lista de membros do grupo para um grupo específico.

Para obter mais informações, consulte [adicionar membros em massa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [remover membros em](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)massa, [lista de membros de download em massa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)e lista de grupos de [download em massa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>O consentimento dinâmico agora tem suporte por meio de um novo ponto de extremidade de consentimento do administrador

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (inícios de sessão)  
**Capacidade de produto:** autenticação de utilizador

Criamos um novo ponto de extremidade de consentimento do administrador para dar suporte ao consentimento dinâmico, o que é útil para aplicativos que desejam usar o modelo de consentimento dinâmico na plataforma de identidade da Microsoft.

Para obter mais informações sobre como usar esse novo ponto de extremidade, consulte [usando o ponto de extremidade de consentimento do administrador](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – setembro de 2019

**Tipo:** novo recurso  
**Categoria de serviço:** aplicações empresariais  
**Capacidade de produto:** 3º integração de terceiros

Em setembro de 2019, adicionamos esses 29 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Access for Ethidex Compliance Office™-logon único](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), portal do [IServer](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [concur Travel e Expense](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), [YeeFlow](https://apps.yeeflow.com/), [instalações Arc](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [Luware Stratus Team](https://stratus.emea.luware.cloud/login), [Wide ideas](https://wideideas.online/wideideas/), [prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [JDLT cliente Hub](https://clients.jdlt.co.uk/login), [RENRAKU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial), [SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [Penneo,](https://app.penneo.com/) [Hiretual](https://app.testhtm.com/settings/email-integration)e [Cintoo Cloud](https://aec.cintoo.com/login), [Whitestate](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [SSO hospedado online de patrimônio](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [bis](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [COO Kai Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Gerenciamento de identidades da Adobe](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), benefícios de [descoberta SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/), [ITask](https://itask.yipinapp.com/)

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-global-reader-role"></a>Nova função de leitor global do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** RBAC  
**Capacidade de produto:** controlo de acesso

A partir de 24 de setembro de 2019, vamos começar a distribuir uma nova função de Azure Active Directory (AD) chamada leitor global. Essa distribuição começará com a produção e os clientes de nuvem global (GCC), concluindo todo o mundo em outubro.

A função de leitor global é a contraparte somente leitura para o administrador global. Os usuários nessa função podem ler configurações e informações administrativas entre Microsoft 365 serviços, mas não podem tomar ações de gerenciamento. Criamos a função de leitor global para ajudar a reduzir o número de administradores globais em sua organização. Como as contas de administrador global são poderosas e vulneráveis a ataques, recomendamos que você tenha menos de cinco administradores globais. É recomendável usar a função de leitor global para planejamento, auditorias ou investigações. Também é recomendável usar a função de leitor global em combinação com outras funções de administrador limitadas, como o administrador do Exchange, para ajudar a realizar o trabalho sem exigir a função de administrador global.

A função leitor global funciona com o novo centro de administração do Microsoft 365, centro de administração do Exchange, centro de administração do Team, central de segurança, centro de conformidade, centro de administração do Azure AD e centro de administração do gerenciamento de dispositivos.

>[!NOTE]
> No início da visualização pública, a função de leitor global não funcionará com: SharePoint, Privileged Access Management, Sistema de Proteção de Dados do Cliente, rótulos de sensibilidade, ciclo de vida de equipes, relatórios de equipes & análise de chamadas, gerenciamento de dispositivo de telefone IP de equipes e aplicativo de equipes Catalog. Todos esses serviços têm a finalidade de trabalhar com a função no futuro.

Para obter mais informações, consulte [permissões de função de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Acessar um servidor de relatório local de seu aplicativo Power BI Mobile usando Proxy de Aplicativo do Azure Active Directory

**Tipo:** novo recurso  
**Categoria de serviço:** do Proxy de aplicações  
**Capacidade de produto:** controlo de acesso

A nova integração entre o aplicativo móvel Power BI e o Azure Proxy de Aplicativo do AD permite que você entre com segurança no aplicativo Power BI Mobile e exiba os relatórios de sua organização hospedados no Servidor de Relatórios do Power BI local.

Para obter informações sobre o aplicativo Power BI Mobile, incluindo onde baixar o aplicativo, consulte o [site do Power bi](https://powerbi.microsoft.com/mobile/). Para obter mais informações sobre como configurar o aplicativo móvel Power BI com o Azure Proxy de Aplicativo do AD, consulte [habilitar o acesso remoto para Power bi Mobile com o azure proxy de aplicativo do AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>A nova versão do módulo do PowerShell do AzureADPreview está disponível

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** outros  
**Capacidade de produto:** diretório

Novos cmdlets foram adicionados ao módulo AzureADPreview para ajudar a definir e atribuir funções personalizadas no Azure AD, incluindo:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nova versão do Azure AD Connect

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** outros  
**Capacidade de produto:** diretório

Lançamos uma versão atualizada do Azure AD Connect para clientes de atualização automática. Essa nova versão inclui vários novos recursos, aprimoramentos e correções de bugs. Para obter mais informações sobre essa nova versão, consulte [Azure ad Connect: histórico de lançamento de versão](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>O servidor de autenticação multifator (MFA) do Azure, versão 8.0.2 agora está disponível

**Tipo:** fixo  
**Categoria de serviço:** FATO  
**Capacidade de produto:** identidade de segurança e proteção

Se você for um cliente existente, que ativou o servidor MFA antes de 1º de julho de 2019, agora você pode baixar a versão mais recente do servidor MFA (versão 8.0.2). Nesta nova versão, nós:

- Correção de um problema para quando o Azure AD Sync altera um usuário de desabilitado para habilitado, um email é enviado para o usuário.

- Correção de um problema para que os clientes possam atualizar com êxito, ao mesmo tempo em que continuam a usar a funcionalidade de marcas.

- Adicionou o código do país Kosovo (+ 383).

- Adicionado log de auditoria de bypass único para o MultiFactorAuthSvc. log.

- Desempenho aprimorado para o SDK do serviço Web.

- Correção de outros bugs secundários.

A partir de 1º de julho de 2019, a Microsoft parou de oferecer o servidor MFA para novas implantações. Novos clientes que exigem autenticação multifator devem usar a autenticação multifator do Azure baseada em nuvem. Para obter mais informações, consulte [planejando uma implantação da autenticação multifator do Azure baseada em nuvem](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="august-2019"></a>Agosto de 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Pesquisa, filtragem e classificação aprimoradas para grupos estão disponíveis no portal do AD do Azure (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** grupo de gestão  
**Capacidade de produto:** colaboração

Temos o prazer de anunciar a disponibilidade de visualização pública das experiências relacionadas a grupos aprimoradas no portal do AD do Azure. Esses aprimoramentos ajudam você a gerenciar melhor grupos e listas de membros, fornecendo:

- Recursos de pesquisa avançada, como a pesquisa de subcadeia de caracteres em listas de grupos.
- Opções avançadas de filtragem e classificação em listas de membros e proprietários.
- Novos recursos de pesquisa para listas de membros e proprietários.
- Contagens de grupos mais precisas para grupos grandes.

Para obter mais informações, consulte [gerenciar grupos na portal do Azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Novas funções personalizadas estão disponíveis para gerenciamento de registro de aplicativo (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** RBAC  
**Capacidade de produto:** controlo de acesso

As funções personalizadas (disponíveis com uma assinatura P1 ou P2 do Azure AD) agora podem ajudar a fornecer acesso refinado, permitindo que você crie definições de função com permissões específicas e, em seguida, atribua essas funções a recursos específicos. No momento, você cria funções personalizadas usando permissões para gerenciar registros de aplicativo e, em seguida, atribuir a função a um aplicativo específico. Para obter mais informações sobre funções personalizadas, consulte [funções de administrador personalizado no Azure Active Directory (versão prévia)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Se você precisar de permissões ou recursos adicionais com suporte, que você não vê no momento, poderá enviar comentários para nosso [site de comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) e adicionaremos sua solicitação ao mapa de estrada de atualização.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Novos logs de provisionamento podem ajudá-lo a monitorar e solucionar problemas de implantação de provisionamento de aplicativo (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** aprovisionamento de aplicações  
**Capacidade de produto:** gerenciamento de ciclo de vida de identidade

Novos logs de provisionamento estão disponíveis para ajudá-lo a monitorar e solucionar problemas de implantação de provisionamento de usuários e grupos. Esses novos arquivos de log incluem informações sobre:

- Quais grupos foram criados com êxito no [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Quais funções foram importadas de [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- O que os funcionários não foram importados do [workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Para obter mais informações, consulte [Provisionando relatórios no portal de Azure Active Directory (versão prévia)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Novos relatórios de segurança para todos os administradores do Azure AD (disponibilidade geral)

**Tipo:** novo recurso  
**Categoria de serviço:** Identity Protection  
**Capacidade de produto:** identidade de segurança e proteção

Por padrão, todos os administradores do Azure AD serão capazes de acessar relatórios de segurança modernos no Azure AD. Até o fim de setembro, você poderá usar a faixa na parte superior dos relatórios de segurança modernos para retornar aos relatórios antigos.

Os relatórios de segurança modernos fornecerão recursos adicionais das versões mais antigas, incluindo:

- Filtragem e classificação avançadas
- Ações em massa, como ignorar o risco do usuário
- Confirmação de entidades comprometidas ou seguras
- Estado de risco, abrangendo: em risco, ignorado, remediado e confirmado comprometido
- Novas detecções relacionadas a riscos (disponíveis para assinantes Azure AD Premium)

Para obter mais informações, consulte [usuários arriscados](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [entradas arriscadas](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)e [detecções de risco](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>A identidade gerenciada atribuída pelo usuário está disponível para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais (disponibilidade geral)

**Tipo:** novo recurso  
**Categoria de serviço:** Identidades gerenciadas para recursos do Azure  
**Capacidade de produto:** experiência do desenvolvedor

Agora, as identidades gerenciadas atribuídas pelo usuário estão disponíveis para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais. Como parte disso, o Azure pode criar uma identidade no locatário do Azure AD que é confiável para a assinatura em uso e pode ser atribuída a uma ou mais instâncias de serviço do Azure. Para obter mais informações sobre identidades gerenciadas atribuídas pelo usuário, consulte [o que são identidades gerenciadas para recursos do Azure?](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Os usuários podem redefinir suas senhas usando um token de hardware ou aplicativo móvel (disponibilidade geral)

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** reposição personalizada de palavra-passe  
**Capacidade de produto:** autenticação de utilizador

Os usuários que registraram um aplicativo móvel com sua organização agora podem redefinir sua própria senha, aprovando uma notificação do aplicativo Microsoft Authenticator ou inserindo um código de seu aplicativo móvel ou token de hardware.

Para obter mais informações, consulte [como funciona: redefinição de senha de autoatendimento do Azure ad](https://aka.ms/authappsspr). Para obter mais informações sobre a experiência do usuário, consulte [visão geral de redefinir sua própria senha corporativa ou de estudante](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignora o cache compartilhado MSAL.NET para cenários em nome de

**Tipo:** fixo  
**Categoria de serviço:** autenticações (inícios de sessão)  
**Capacidade de produto:** autenticação de utilizador

A partir da ADAL.NET (biblioteca de autenticação do Azure AD) versão 5.0.0-Preview, os desenvolvedores de aplicativos devem [serializar um cache por conta para aplicativos Web e APIs da Web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). Caso contrário, alguns cenários que usam o [fluxo em nome de](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow), juntamente com alguns casos de uso específicos de `UserAssertion`, podem resultar em uma elevação de privilégio. Para evitar essa vulnerabilidade, o ADAL.NET agora ignora o cache compartilhado da biblioteca de autenticação da Microsoft para dotNet (MSAL.NET) para cenários em nome de.

Para obter mais informações sobre esse problema, consulte [Azure Active Directory vulnerabilidade de elevação de privilégio da biblioteca de autenticação](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – agosto de 2019

**Tipo:** novo recurso  
**Categoria de serviço:** aplicações empresariais  
**Capacidade de produto:** 3º integração de terceiros

Em agosto de 2019, adicionamos esses 26 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[Plataforma cívico](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [pronovo Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Portal de Inativ da Viareport (Europa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [participação na Academia](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), matriz de [prioridade](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [CARBONITE Endpoint backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync by Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [entregar. Media™ portal](https://portal.deliver.media), [frente Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD conectar](https://www.stashcat.com), [piscar](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [pronovo análises](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [assistir por cores](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [Harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB navegar pelo atendimento estratégico](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Novas versões dos módulos AzureAD PowerShell e AzureADPreview PowerShell estão disponíveis

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** outros  
**Capacidade de produto:** diretório

Novas atualizações para os módulos do PowerShell de visualização do AzureAD e AzureAD estão disponíveis:

- Um novo parâmetro `-Filter` foi adicionado ao parâmetro `Get-AzureADDirectoryRole` no módulo AzureAD. Esse parâmetro ajuda você a filtrar as funções de diretório retornadas pelo cmdlet.
- Novos cmdlets foram adicionados ao módulo AzureADPreview para ajudar a definir e atribuir funções personalizadas no Azure AD, incluindo:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Melhorias na interface do usuário do construtor de regras de grupo dinâmico no portal do Azure

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** grupo de gestão  
**Capacidade de produto:** colaboração

Fizemos algumas melhorias da interface do usuário no construtor de regras de grupo dinâmico, disponível na portal do Azure, para ajudá-lo a configurar uma nova regra com mais facilidade ou alterar as regras existentes. Essa melhoria de design permite que você crie regras com até cinco expressões, em vez de apenas uma. Também atualizamos a lista de propriedades do dispositivo para remover as propriedades de dispositivo preteridas.

Para obter mais informações, consulte [gerenciar regras de associação dinâmica](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nova permissão de aplicativo Microsoft Graph disponível para uso com revisões de acesso

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** revisões de acesso  
**Funcionalidade do produto:** Governança de identidade

Apresentamos uma nova permissão de aplicativo Microsoft Graph, `AccessReview.ReadWrite.Membership`, que permite aos aplicativos criar e recuperar automaticamente as revisões de acesso para associações de grupo e atribuições de aplicativo. Essa permissão pode ser usada por seus trabalhos agendados ou como parte de sua automação, sem a necessidade de um contexto de usuário conectado.

Para obter mais informações, confira o [exemplo como criar revisões de acesso do Azure ad usando Microsoft Graph permissões de aplicativo com o blog do PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Os logs de atividade do Azure AD agora estão disponíveis para instâncias de nuvem do governo no Azure Monitor

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** relatórios  
**Capacidade de produto:** monitoramento e relatório

Estamos empolgados em anunciar que os logs de atividades do Azure AD agora estão disponíveis para instâncias de nuvem do governo no Azure Monitor. Agora você pode enviar logs do Azure AD para sua conta de armazenamento ou para um hub de eventos para integrar com suas ferramentas SIEM, como [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)e [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Para obter mais informações sobre como configurar Azure Monitor, consulte [logs de atividades do Azure AD no Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Atualize seus usuários para a nova experiência de informações de segurança aprimorada

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:**  Autenticações (logons)   
**Capacidade de produto:** autenticação de utilizador

Em 25 de setembro de 2019, vamos desligar a experiência de informações de segurança antiga e não avançada para registrar e gerenciar informações de segurança do usuário e ativar apenas a nova [versão aprimorada](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Isso significa que os usuários não poderão mais usar a experiência antiga.

Para obter mais informações sobre a experiência de informações de segurança aprimorada, consulte nossa [documentação de administração](https://aka.ms/securityinfodocs) e a [documentação do usuário](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Para ativar essa nova experiência, você deve:

1. Entre no portal do Azure como um administrador global ou administrador de usuários.

2. Vá para **Azure Active Directory > configurações de usuário > gerenciar configurações para recursos de visualização do painel de acesso**.

3. No, os **usuários podem usar os recursos de visualização para registrar e gerenciar informações de segurança – área avançada** , selecionar **selecionado**e, em seguida, escolher um grupo de usuários ou escolher **tudo** para ativar esse recurso para todos os usuários no locatário.

4. Em **os usuários podem usar recursos de visualização para registrar e gerenciar segurança** **info** ,selecione **nenhum**.

5. Salve suas configurações.

    Depois de salvar suas configurações, você não terá mais acesso à antiga experiência de informações de segurança.

>[!Important]
>Se você não concluir essas etapas antes de 25 de setembro de 2019, seu locatário de Azure Active Directory será habilitado automaticamente para a experiência aprimorada. Se você tiver dúvidas, entre em contato conosco em registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>As solicitações de autenticação que usam os logons POST serão mais rigorosamente validadas

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** autenticações (inícios de sessão)  
**Funcionalidade do produto:** Padrões

A partir de 2 de setembro de 2019, as solicitações de autenticação usando o método POST serão mais rigorosamente validadas em relação aos padrões HTTP. Especificamente, espaços e aspas duplas (") não serão mais removidos dos valores do formulário de solicitação. Essas alterações não devem interromper nenhum cliente existente e ajudarão a garantir que as solicitações enviadas ao Azure AD sejam manipuladas de maneira confiável todas as vezes.

Para obter mais informações, consulte os [avisos de alterações significativas do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Julho de 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Plano para alteração: atualização do serviço de proxy de aplicativo para dar suporte apenas a TLS 1,2

**Tipo:** plano de alteração  
**Categoria de serviço:** do Proxy de aplicações  
**Capacidade de produto:** controlo de acesso

Para ajudar a fornecer a criptografia mais forte, vamos começar limitando o acesso do serviço de proxy de aplicativo somente a protocolos TLS 1,2. Essa limitação inicialmente será distribuída para os clientes que já estão usando protocolos TLS 1,2, portanto, você não verá o impacto. A reprovação completa dos protocolos TLS 1,0 e TLS 1,1 será concluída em 31 de agosto de 2019. Os clientes que ainda usam o TLS 1,0 e o TLS 1,1 receberão um aviso avançado para se preparar para essa alteração.

Para manter a conexão com o serviço de proxy de aplicativo durante essa alteração, recomendamos que você verifique se as combinações de servidor cliente e navegador-servidor estão atualizadas para usar o TLS 1,2. Também recomendamos que você inclua todos os sistemas clientes usados por seus funcionários para acessar os aplicativos publicados por meio do serviço de proxy de aplicativo.

Para obter mais informações, consulte [Adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plano para alteração: as atualizações de design são recebidas para a Galeria de aplicativos

**Tipo:** plano de alteração  
**Categoria de serviço:** aplicações empresariais  
**Capacidade de produto:** SSO

Novas alterações de interface do usuário estão chegando ao design do **Adicionar da** área da Galeria da folha **Adicionar um aplicativo** . Essas alterações ajudarão você a localizar mais facilmente seus aplicativos que dão suporte ao provisionamento automático, ao OpenID Connect, Security Assertion Markup Language (SAML) e ao SSO (logon único) com senha.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plano de alteração: remoção do endereço IP do servidor MFA do endereço IP do Office 365

**Tipo:** plano de alteração  
**Categoria de serviço:** FATO  
**Capacidade de produto:** identidade de segurança e proteção

Estamos removendo o endereço IP do servidor MFA do [endereço IP do Office 365 e do serviço Web URL](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Se, no momento, você confiar nessas páginas para atualizar as configurações do firewall, deverá ter certeza de que também está incluindo a lista de endereços IP documentados na seção **requisitos do firewall do azure servidor de autenticação multifator** no artigo [introdução ao servidor de autenticação multifator do Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Tokens somente de aplicativo agora exigem que o aplicativo cliente exista no locatário do recurso

**Tipo:** fixo  
**Categoria de serviço:** autenticações (inícios de sessão)  
**Capacidade de produto:** autenticação de utilizador

Em 26 de julho de 2019, alteramos a maneira como fornecemos tokens somente de aplicativo por meio da [concessão de credenciais de cliente](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Anteriormente, os aplicativos podiam obter tokens para chamar outros aplicativos, independentemente se o aplicativo cliente estava no locatário. Atualizamos esse comportamento para que recursos de locatário único, às vezes chamados de APIs da Web, só possam ser chamados por aplicativos cliente que existem no locatário do recurso.

Se seu aplicativo não estiver localizado no locatário de recursos, você receberá uma mensagem de erro que diz, `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` para corrigir esse problema, você deve criar a entidade de serviço de aplicativo cliente no locatário, usando o [ponto de extremidade de consentimento do administrador](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) ou [por meio do PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), que garante que seu locatário tenha dado a permissão do aplicativo para operar dentro do locatário.

Para obter mais informações, consulte [o que há de novo para autenticação?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> O consentimento existente entre o cliente e a API continua a não ser necessário. Os aplicativos ainda devem estar fazendo suas próprias verificações de autorização.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nova entrada sem senha no Azure AD usando as chaves de segurança do FIDO2

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (inícios de sessão)  
**Capacidade de produto:** autenticação de utilizador

Os clientes do Azure AD agora podem definir políticas para gerenciar chaves de segurança FIDO2 para os usuários e grupos de sua organização. Os usuários finais também podem registrar automaticamente suas chaves de segurança, usar as chaves para entrar em suas contas da Microsoft em sites, enquanto em dispositivos com capacidade de FIDO, bem como entrar em seus dispositivos Windows 10 ingressados no Azure AD.

Para obter mais informações, consulte [Habilitar entrada sem senha para o Azure AD (versão prévia)](/azure/active-directory/authentication/concept-authentication-passwordless) para obter informações relacionadas ao administrador e configurar informações de [segurança para usar uma chave de segurança (versão prévia)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) para informações relacionadas ao usuário final.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – julho de 2019

**Tipo:** novo recurso  
**Categoria de serviço:** aplicações empresariais  
**Capacidade de produto:** 3º integração de terceiros

Em julho de 2019, adicionamos esses 18 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[Ungerboeck software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [Omnichannel de padrão brilhante, centro de contatos](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [Nelly inteligente](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [acesso de SSO do MS Azure para Ethidex Compliance Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [atenção](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [abstrato](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [ascentl](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [hiperanna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [pharmingid WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), JFrog [artefatoy](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o provisionamento de conta de usuário para esses aplicativos SaaS recentemente suportados

**Tipo:** novo recurso  
**Categoria de serviço:** aplicações empresariais  
**Capacidade de produto:** monitoramento e relatório

Agora você pode automatizar a criação, atualização e exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Teclado](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Diretório federado](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Pico](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor sua organização usando o provisionamento automatizado de contas de usuário, confira automatizar o [provisionamento de usuários para aplicativos SaaS com o Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nova marca de serviço de Azure AD Domain Services para o grupo de segurança de rede

**Tipo:** novo recurso  
**Categoria de serviço:** Azure AD Domain Services  
**Capacidade de produto:** Azure AD Domain Services

Se estiver cansado de gerenciar listas longas de endereços IP e intervalos, você poderá usar a nova marca de serviço de rede **AzureActiveDirectoryDomainServices** em seu grupo de segurança de rede do Azure para ajudar a proteger o tráfego de entrada para sua sub-rede Azure AD Domain Services rede virtual.

Para obter mais informações sobre essa nova marca de serviço, consulte [grupos de segurança de rede para Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Novas auditorias de segurança para Azure AD Domain Services (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** Azure AD Domain Services  
**Capacidade de produto:** Azure AD Domain Services

Temos o prazer de anunciar o lançamento da auditoria de segurança do serviço de domínio do Azure AD para a visualização pública. A auditoria de segurança ajuda a fornecer informações críticas sobre os serviços de autenticação transmitindo eventos de auditoria de segurança para os recursos de destino, incluindo o armazenamento do Azure, os espaços de trabalho do Azure Log Analytics e o Hub de eventos do Azure, usando o serviço de domínio do Azure AD Portal.

Para obter mais informações, consulte [habilitar auditorias de segurança para Azure AD Domain Services (versão prévia)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Novos métodos de autenticação uso & insights (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** reposição personalizada de palavra-passe  
**Capacidade de produto:** monitoramento e relatório

Os novos métodos de autenticação de uso & relatórios do insights podem ajudá-lo a entender como os recursos como a autenticação multifator do Azure e a redefinição de senha de autoatendimento estão sendo registrados e usados em sua organização, incluindo o número de registrado os usuários para cada recurso, com que frequência a redefinição de senha de autoatendimento é usada para redefinir senhas e por qual método a redefinição ocorre.

Para obter mais informações, consulte [métodos de autenticação uso & insights (versão prévia)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Novos relatórios de segurança estão disponíveis para todos os administradores do Azure AD (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** Identity Protection  
**Capacidade de produto:** identidade de segurança e proteção

Todos os administradores do Azure AD agora podem selecionar a faixa na parte superior dos relatórios de segurança existentes, como o relatório **usuários sinalizados para risco** , para começar a usar a nova experiência de segurança, conforme mostrado nos relatórios de **usuários arriscados** e de **entradas arriscadas** . Ao longo do tempo, todos os relatórios de segurança serão movidos das versões mais antigas para as novas versões, com os novos relatórios, fornecendo os seguintes recursos adicionais:

- Filtragem e classificação avançadas

- Ações em massa, como ignorar o risco do usuário

- Confirmação de entidades comprometidas ou seguras

- Estado de risco, abrangendo: em risco, ignorado, remediado e confirmado comprometido

Para obter mais informações, consulte relatório de [usuários arriscados](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) e [registro de entradas arriscadas](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Novas auditorias de segurança para Azure AD Domain Services (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** Azure AD Domain Services  
**Capacidade de produto:** Azure AD Domain Services

Temos o prazer de anunciar o lançamento da auditoria de segurança do serviço de domínio do Azure AD para a visualização pública. A auditoria de segurança ajuda a fornecer informações críticas sobre os serviços de autenticação transmitindo eventos de auditoria de segurança para os recursos de destino, incluindo o armazenamento do Azure, os espaços de trabalho do Azure Log Analytics e o Hub de eventos do Azure, usando o serviço de domínio do Azure AD Portal.

Para obter mais informações, consulte [habilitar auditorias de segurança para Azure AD Domain Services (versão prévia)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nova Federação B2B direta usando SAML/WS-enalimentado (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Capacidade de produto:** B2B/B2C

A Federação direta ajuda a facilitar o trabalho com parceiros cuja solução de identidade gerenciada por ti não é o AD do Azure, trabalhando com sistemas de identidade que dão suporte aos padrões SAML ou WS-enalimentados. Depois de configurar uma relação de Federação direta com um parceiro, qualquer usuário convidado novo que você convidar desse domínio poderá colaborar com você usando sua conta institucional existente, tornando a experiência do usuário para seus convidados mais direta.

Para obter mais informações, consulte [Federação direta com AD FS e provedores de terceiros para usuários convidados (visualização)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o provisionamento de conta de usuário para esses aplicativos SaaS recentemente suportados

**Tipo:** novo recurso  
**Categoria de serviço:** aplicações empresariais  
**Capacidade de produto:** monitoramento e relatório

Agora você pode automatizar a criação, atualização e exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Teclado](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Diretório federado](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Pico](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor sua organização usando o provisionamento automatizado de contas de usuário, confira automatizar o [provisionamento de usuários para aplicativos SaaS com o Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nova verificação de nomes de grupo duplicados no portal do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** grupo de gestão  
**Capacidade de produto:** colaboração

Agora, quando você criar ou atualizar um nome de grupo no portal do Azure AD, realizaremos uma verificação para ver se você está duplicando um nome de grupo existente em seu recurso. Se determinarmos que o nome já está em uso por outro grupo, você será solicitado a modificar seu nome.

Para obter mais informações, consulte [gerenciar grupos no portal do Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>O Azure AD agora dá suporte a parâmetros de consulta estáticos em URIs de resposta (redirecionamento)

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (inícios de sessão)  
**Capacidade de produto:** autenticação de utilizador

Os aplicativos do Azure AD agora podem registrar e usar URIs de resposta (redirecionamento) com parâmetros de consulta estáticos (por exemplo, `https://contoso.com/oauth2?idp=microsoft`) para solicitações do OAuth 2,0. O parâmetro de consulta estática está sujeito à correspondência de cadeia de caracteres para URIs de resposta, assim como qualquer outra parte do URI de resposta. Se não houver nenhuma cadeia de caracteres registrada que corresponda ao redirecionamento de URL decodificado-URI, a solicitação será rejeitada. Se o URI de resposta for encontrado, a cadeia de caracteres inteira será usada para redirecionar o usuário, incluindo o parâmetro de consulta estática.

Os URIs de resposta dinâmica ainda são proibidos porque representam um risco de segurança e não podem ser usados para reter informações de estado em uma solicitação de autenticação. Para essa finalidade, use o parâmetro `state`.

Atualmente, as telas de registro do aplicativo do portal do Azure ainda bloqueiam parâmetros de consulta. No entanto, você pode editar manualmente o manifesto do aplicativo para adicionar e testar parâmetros de consulta em seu aplicativo. Para obter mais informações, consulte [o que há de novo para autenticação?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Os logs de atividade (APIs do MS Graph) para o Azure AD agora estão disponíveis por meio de cmdlets do PowerShell

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Capacidade de produto:** monitoramento e relatório

Estamos empolgados em anunciar que os logs de atividades do Azure AD (relatórios de auditoria e de entradas) agora estão disponíveis por meio do módulo do PowerShell do Azure AD. Anteriormente, você poderia criar seus próprios scripts usando os pontos de extremidade do MS API do Graph e agora estendemos esse recurso aos cmdlets do PowerShell.

Para obter mais informações sobre como usar esses cmdlets, consulte [cmdlets do PowerShell do Azure ad para relatórios](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Controles de filtro atualizados para logs de auditoria e de entrada no Azure AD

**Tipo:** funcionalidade foi alterado  
**Categoria de serviço:** relatórios  
**Capacidade de produto:** monitoramento e relatório

Atualizamos os relatórios de auditoria e log de entrada para que você possa aplicar vários filtros sem precisar adicioná-los como colunas nas telas de relatório. Além disso, agora você pode decidir quantos filtros deseja mostrar na tela. Todas as atualizações funcionam em conjunto para facilitar a leitura e o escopo de seus relatórios de forma a suas necessidades.

Para obter mais informações sobre essas atualizações, consulte [Filtrar logs de auditoria](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) e [Filtrar atividades de entrada](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---
