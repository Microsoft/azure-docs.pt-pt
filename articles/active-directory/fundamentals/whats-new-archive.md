---
title: Arquivo para O que há de novo no Diretório Ativo Azure? | Microsoft Docs
description: A que há de novo notas de versão na descrição geral secção deste conjunto de conteúdos contém seis meses da atividade. Depois de 6 meses, os itens são removidos do artigo principal e colocar neste artigo de arquivo.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64229e4433ee029787ebd88eba264833827e706c
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136470"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Arquivo para O que há de novo no Diretório Ativo Azure?

As notas de lançamento primárias [O que há de novo no Azure Ative Directory?](whats-new.md)

O que há de novo no Diretório Ativo Azure? As notas de lançamento fornecem informações sobre:

- Versões mais recentes
- Problemas conhecidos
- Correções de erros
- Funcionalidades preteridas
- Planos para que as alterações

---

## <a name="august-2019"></a>Agosto de 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>A pesquisa, filtragem e triagem melhoradas para grupos estão disponíveis no portal Azure AD (Visualização Pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração

Temos o prazer de anunciar a disponibilidade pública de visualização das experiências relacionadas com grupos melhorados no portal Azure AD. Estes melhoramentos ajudam-no a gerir melhor os grupos e as listas de membros, fornecendo:

- Capacidades avançadas de pesquisa, tais como pesquisa de subcadeias em listas de grupos.
- Opções avançadas de filtragem e triagem nas listas de membros e proprietários.
- Novas capacidades de pesquisa para listas de membros e proprietários.
- Grupo mais preciso conta para grandes grupos.

Para mais informações, consulte [Gerir grupos no portal Azure.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Novas funções personalizadas estão disponíveis para gestão de registo de aplicações (Pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** RBAC  
**Capacidade do produto:** Controlo de Acesso

As funções personalizadas (disponíveis com uma subscrição Azure AD P1 ou P2) podem agora ajudar a fornecer-lhe acesso fino, permitindo-lhe criar definições de papéis com permissões específicas e, em seguida, atribuir essas funções a recursos específicos. Atualmente, cria funções personalizadas utilizando permissões para gerir registos de aplicações e, em seguida, atribuindo o papel a uma aplicação específica. Para obter mais informações sobre funções personalizadas, consulte funções de [administrador personalizado no Diretório Ativo do Azure (pré-visualização)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Se precisar de permissões ou recursos adicionais suportados, o que não vê atualmente, pode enviar feedback para o nosso site de [feedback do Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) e adicionaremos o seu pedido ao nosso mapa de atualização.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Novos registos de fornecimento podem ajudá-lo a monitorizar e resolver problemas na implementação do fornecimento de aplicações (Visualização Pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida de identidade

Novos registos de fornecimento estão disponíveis para ajudá-lo a monitorizar e resolver problemas com a implementação de fornecimento de utilizadores e grupos. Estes novos ficheiros de registo incluem informações sobre:

- Que grupos foram criados com sucesso no [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Que papéis foram importados da [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- O que os empregados não foram importados do Dia do [Trabalho](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Para mais informações, consulte os [relatórios de provisionamento no portal do Diretório Ativo Azure (pré-visualização)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Novos relatórios de segurança para todos os administradores da AD Azure (Disponibilidade Geral)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Segurança e Proteção de Identidade

Por padrão, todos os administradores da AD Azure poderão em breve aceder a relatórios de segurança modernos dentro da AD Azure. Até ao final de setembro, poderá usar o estandarte no topo dos relatórios de segurança modernos para voltar aos relatórios antigos.

Os relatórios de segurança modernos fornecerão capacidades adicionais a partir das versões mais antigas, incluindo:

- Filtragem e triagem avançadas
- Ações a granel, tais como a anulação do risco do utilizador
- Confirmação de entidades comprometidas ou seguras
- Estado de risco, cobertura: Em risco, dispensado, remediado e confirmado comprometido
- Novas deteções relacionadas com o risco (disponíveis para assinantes Do AD Premium do Azure)

Para mais informações, consulte [utilizadores de risco,](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) [inscrições arriscadas](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)e deteções de [risco.](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections)

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>A identidade gerida atribuída ao utilizador está disponível para máquinas virtuais e conjuntos de escala de máquinavirtual (Disponibilidade Geral)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Identidades geridas para recursos Azure  
**Capacidade do produto:** Experiência de Desenvolvimento

As identidades geridas atribuídas ao utilizador estão agora geralmente disponíveis para máquinas virtuais e conjuntos de escala de máquinas virtuais. Como parte disso, o Azure pode criar uma identidade no inquilino da AD Azure que é confiada pela subscrição em uso, e pode ser atribuída a uma ou mais instâncias de serviço Azure. Para obter mais informações sobre identidades geridas atribuídas ao utilizador, consulte [as identidades geridas para os recursos do Azure?](https://aka.ms/azuremanagedidentity)

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Os utilizadores podem redefinir as suas palavras-passe utilizando uma aplicação móvel ou ficha de hardware (Disponibilidade Geral)

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Reset de palavra-passe self service  
**Capacidade do produto:** Autenticação do Utilizador

Os utilizadores que tenham registado uma aplicação móvel com a sua organização podem agora redefinir a sua própria palavra-passe aprovando uma notificação da aplicação Microsoft Authenticator ou através da introdução de um código a partir da sua aplicação móvel ou ficha de hardware.

Para mais informações, consulte [como funciona: Reset de senha de autosserviço Azure AD](https://aka.ms/authappsspr). Para obter mais informações sobre a experiência do utilizador, consulte Redefinir o seu próprio trabalho ou visão geral da [palavra-passe escolar](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignora a cache partilhada MSAL.NET para cenários em nome

**Tipo:** Fixo  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador

Começando pela versão 5.0.0-preview da Biblioteca de Autenticação AD Azure (ADAL.NET), os desenvolvedores de aplicações devem [serializar uma cache por conta para aplicações web e APIs web.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api) Caso contrário, alguns cenários que utilizam o [fluxo em nome](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)próprio, juntamente com alguns casos de utilização específica de `UserAssertion`, podem resultar numa elevação do privilégio. Para evitar esta vulnerabilidade, ADAL.NET agora ignora a biblioteca de autenticação da Microsoft para o dotnet (MSAL.NET) cache partilhado para cenários em nome.

Para mais informações sobre este assunto, consulte [azure Ative Directory Authentication Library Elevation of Privilege Vulnerability](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - agosto de 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido

Em agosto de 2019, adicionámos estas 26 novas aplicações com apoio da Federação à galeria de aplicações:

[Plataforma Cívica](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox,](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial) [Viareport's Inativ Portal (Europa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks,](https://azure.microsoft.com/services/databricks) [Robin,](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial) [Academy Attendance](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), Priority [Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace,](https://cousto.platformers.be/account/login) [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync by Cincom,](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial) [Chargebee,](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial) [deliver.media&trade; Portal](https://portal.deliver.media), Frontline [Education,](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial) [F5](https://www.f5.com/products/security/access-policy-manager), [ stashcat AD connect](https://www.stashcat.com), [Blink,](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial) [Vocoli,](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial) [ProNovos Analytics,](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial) [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [Watch by Colors,](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial) [Harness,](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial) [EAB Navigate Strategic Care](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Estão disponíveis novas versões dos módulos PowerShell E AzureAD PowerShell e AzureADPreview

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Diretório

Estão disponíveis novas atualizações para os módulos PowerShell de pré-visualização AzureAD e AzureAD:

- Foi adicionado um novo parâmetro `-Filter` ao parâmetro `Get-AzureADDirectoryRole` no módulo AzureAD. Este parâmetro ajuda-o a filtrar as funções de diretório devolvidas pelo cmdlet.
- Foram adicionados novos cmdlets ao módulo AzureADPreview, para ajudar a definir e atribuir funções personalizadas em Azure AD, incluindo:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Melhorias na UI do dinâmico construtor de regras do grupo no portal Azure

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração

Fizemos algumas melhorias na UI para o dinâmico construtor de regras do grupo, disponível no portal Azure, para ajudá-lo a configurar mais facilmente uma nova regra, ou alterar as regras existentes. Esta melhoria do design permite criar regras com até cinco expressões, em vez de apenas uma. Também atualizámos a lista de propriedades do dispositivo para remover propriedades depreciadas do dispositivo.

Para mais informações, consulte [Gerir regras dinâmicas de adesão.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nova permissão de aplicação do Microsoft Graph disponível para uso com comentários de acesso

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Avaliações de Acesso  
**Capacidade do produto:** Governança de Identidade

Introduzimos uma nova permissão de aplicação do Microsoft Graph, `AccessReview.ReadWrite.Membership`, que permite que as aplicações criem e recuperem automaticamente avaliações de acesso para membros do grupo e atribuições de aplicações. Esta permissão pode ser utilizada pelos seus trabalhos programados ou como parte da sua automatização, sem necessitar de um contexto de utilizador registado.

Para mais informações, consulte o Exemplo como criar avaliações de [acesso a Anúncios Azure utilizando permissões de aplicações do Microsoft Graph com blog PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Os registos de atividade da Azure AD já estão disponíveis para casos de nuvem governamental no Monitor Azure

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Monitorização e Reportagem

Estamos animados para anunciar que os registos de atividade da Azure AD estão agora disponíveis para casos de nuvem governamental no Azure Monitor. Agora pode enviar registos de AD Azure para a sua conta de armazenamento ou para um centro de eventos para se integrar com as suas ferramentas SIEM, como [Sumologic,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic) [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)e [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Para obter mais informações sobre a criação do Monitor Azure, consulte os registos de [atividade da Azure AD no Monitor Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Atualize os seus utilizadores para a nova experiência de informação de segurança melhorada

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:**  Autenticações (Logins)   
**Capacidade do produto:** Autenticação do Utilizador

No dia 25 de setembro de 2019, vamos desligar a velha experiência de informação de segurança não melhorada para registar e gerir informações de segurança dos utilizadores e apenas ligar a nova [versão melhorada.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271) Isto significa que os seus utilizadores deixarão de poder utilizar a experiência antiga.

Para obter mais informações sobre a experiência de informação de segurança melhorada, consulte a [documentação](https://aka.ms/securityinfodocs) do nosso administrador e a documentação do nosso [utilizador.](https://aka.ms/securityinfoguide)

#### <a name="to-turn-on-this-new-experience-you-must"></a>Para ligar esta nova experiência, deve:

1. Inscreva-se no portal Azure como Administrador Global ou Administrador de Utilizador.

2. Vá ao **Diretório Ativo azure > Definições de utilizador > Gerir as definições para visualizações**do painel de acesso .

3. Nos **Utilizadores podem utilizar funcionalidades de pré-visualização para registar e gerir informações** de segurança - área melhorada, selecionar **Selecionados**, e, em seguida, escolher um grupo de utilizadores ou escolher **All** para ativar esta funcionalidade para todos os utilizadores do inquilino.

4. Na área de **Os utilizadores podem utilizar funcionalidades de pré-visualização para registar e gerir a área de segurança **info******* selecione **Nenhum**.

5. Guarde as suas definições.

    Depois de guardar as suas definições, deixará de ter acesso à antiga experiência de informação de segurança.

>[!Important]
>Se não completar estes passos antes de 25 de setembro de 2019, o seu inquilino do Azure Ative Directory estará automaticamente habilitado para a experiência melhorada. Se tiver dúvidas, contacte-nos em registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Pedidos de autenticação utilizando logins POST serão mais estritamente validados

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Normas

A partir de 2 de setembro de 2019, os pedidos de autenticação utilizando o método POST serão mais estritamente validados de acordo com as normas HTTP. Especificamente, os espaços e as pasções duplas (") deixarão de ser removidos dos valores do formulário de pedido. Estas alterações não são esperadas para quebrar nenhum cliente existente, e ajudarão a garantir que os pedidos enviados para a AD Azure sejam tratados de forma fiável cada vez.

Para mais informações, consulte o anúncio de alteração da [AD Azure](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Julho de 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Plano de alteração: Atualização do serviço Proxy de aplicação para suportar apenas TLS 1.2

**Tipo:** Plano de mudança  
**Categoria de serviço:** Procuração de aplicativos  
**Capacidade do produto:** Controlo de Acesso

Para ajudar a fornecer-lhe a nossa encriptação mais forte, vamos começar a limitar o acesso ao serviço proxy de aplicação aapenas protocolos TLS 1.2. Esta limitação será inicialmente lançada para os clientes que já estão a utilizar protocolos TLS 1.2, pelo que não verá o impacto. A deprecação completa dos protocolos TLS 1.0 e TLS 1.1 estará concluída no dia 31 de agosto de 2019. Os clientes que ainda utilizem TLS 1.0 e TLS 1.1 receberão aviso avançado para se prepararem para esta alteração.

Para manter a ligação ao serviço Proxy de Aplicação ao longo desta alteração, recomendamos que certifique-se de que as combinações do servidor de cliente e do servidor do navegador são atualizadas para utilizar o TLS 1.2. Recomendamos também que inclua todos os sistemas de clientes utilizados pelos seus colaboradores para aceder a aplicações publicadas através do serviço Application Proxy.

Para mais informações, consulte [Adicionar uma aplicação no local para acesso remoto através do Proxy de Aplicação no Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plano de mudança: Estão a chegar atualizações de design para a Galeria de Aplicações

**Tipo:** Plano de mudança  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO

Novas alterações na interface do utilizador estão a chegar ao design do **Add a partir da** área de galeria da lâmina adicionar uma **aplicação.** Estas alterações irão ajudá-lo a encontrar mais facilmente as suas aplicações que suportam o fornecimento automático, OpenID Connect, Linguagem de Marcação de Afirmação de Segurança (SAML) e inscrição individual de password (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plano de alteração: Remoção do endereço IP do servidor MFA do endereço IP do Office 365

**Tipo:** Plano de mudança  
**Categoria de serviço:** MFA  
**Capacidade do produto:** Segurança e Proteção de Identidade

Estamos a remover o endereço IP do servidor MFA do [Endereço IP do Office 365 e do serviço Web URL](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Se conta atualmente com estas páginas para atualizar as definições da firewall, deve certificar-se de que também inclui a lista de endereços IP documentados na secção de requisitos de firewall do Servidor de **Autenticação Multi-Factor Azure** da secção de Início com o artigo do Servidor de [Autenticação Multi-Factor Azure.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements)

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Tokens apenas com aplicativos agora exigem que a app do cliente exista no inquilino de recursos

**Tipo:** Fixo  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador

No dia 26 de julho de 2019, mudamos a forma como fornecemos fichas apenas de aplicações através da concessão de credenciais de [cliente.](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) Anteriormente, as aplicações poderiam obter fichas para ligar para outras aplicações, independentemente de a aplicação do cliente estar no inquilino. Atualizámos este comportamento para que os recursos de um único inquilino, por vezes chamados APIs web, só possam ser chamados por aplicações de clientes que existem no inquilino de recursos.

Se a sua aplicação não estiver localizada no inquilino de recursos, receberá uma mensagem de erro que diz, `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` Para resolver este problema, terá de criar o principal serviço de aplicação do cliente no inquilino, utilizando o [ponto final](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) do consentimento do administrador ou através da [PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), o que garante que o seu inquilino deu autorização à aplicação para operar dentro do inquilino.

Para mais informações, veja [quais as novidades para autenticação?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)

> [!NOTE]
> O consentimento existente entre o cliente e a API continua a não ser necessário. As aplicações ainda devem estar a fazer os seus próprios controlos de autorização.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Novo sem palavra-passe no Azure AD com chaves de segurança FIDO2

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador

Os clientes da Azure AD podem agora definir políticas para gerir as chaves de segurança FIDO2 para os utilizadores e grupos da sua organização. Os utilizadores finais também podem auto-registar as suas chaves de segurança, usar as chaves para iniciar sessão nas suas contas da Microsoft em sites enquanto estão em dispositivos fido-capazes, bem como iniciar sessão nos seus dispositivos Windows 10 com aA Azure.

Para mais informações, consulte [A sessão sem palavras-passe para a AD Azure (pré-visualização)](/azure/active-directory/authentication/concept-authentication-passwordless) para obter informações relacionadas com o administrador e configurar informações de segurança para utilizar uma chave de [segurança (Pré-visualização)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) para informações relacionadas com o utilizador final.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - julho 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido

Em julho de 2019, adicionámos estas 18 novas aplicações com apoio da Federação à galeria de aplicações:

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [Bright Pattern Omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), Clever [Nelly,](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial) [AcquireIO,](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial) [Looop,](https://www.looop.co/schedule-a-demo/) [productboard,](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial) [MS Azure SSO Access for Ethidex Compliance Office&trade;, ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso) [Hype,](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial) [Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [Ascentis,](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial) [Flipsnack,](https://www.flipsnack.com/accounts/sign-in-sso.html) [Wandera,](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial) [TwineSocial,](https://twinesocial.com/) [Kallidus,](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial) [HyperAnna,](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial) [PharmID WasteWitness,](https://www.pharmid.com/) [i2B Connect,](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/) [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o fornecimento de conta de utilizador para estas aplicações SaaS recentemente suportadas

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** Monitorização e Reportagem

Agora pode automatizar a criação, atualização e a eliminar as contas dos utilizadores destas aplicações recentemente integradas:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Diretório Federado](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figo](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Saltos](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Pico](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte o fornecimento de [utilizadores automate para aplicações SaaS com AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nova etiqueta de serviços de domínio Azure AD para Network Security Group

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Serviços de Domínio Azure AD  
**Capacidade do produto:** Serviços de Domínio Azure AD

Se estiver cansado de gerir longas listas de endereços e gamas IP, pode utilizar a nova etiqueta de serviço de rede **AzureActiveDirectoryDomainServices** no seu grupo de segurança de rede Azure para ajudar a garantir o tráfego de entrada na sua subnet virtual da rede virtual Azure AD Domain Services.

Para mais informações sobre esta nova etiqueta de serviço, consulte [Network Security Groups for Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Novas Auditorias de Segurança para Serviços de Domínio Azure AD (Pré-visualização Pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Serviços de Domínio Azure AD  
**Capacidade do produto:** Serviços de Domínio Azure AD

Temos o prazer de anunciar o lançamento da Auditoria de Segurança do Serviço de Domínio do Domínio Azure para pré-visualização pública. A auditoria de segurança ajuda a fornecer-lhe informações críticas sobre os seus serviços de autenticação, através do streaming de eventos de auditoria de segurança para recursos direcionados, incluindo armazenamento Azure, espaços de trabalho Azure Log Analytics e Azure Event Hub, utilizando o Serviço de Domínio AD Azure portal.

Para mais informações, consulte [Enable Security Audits for Azure AD Domain Services (Preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Utilização e insights dos novos métodos de autenticação (Pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reset de palavra-passe self service  
**Capacidade do produto:** Monitorização e Reportagem

Os novos métodos de Autenticação que utiliza e insights podem ajudá-lo a entender como funcionalidades como a Autenticação Multi-Factor e o reset de senha de autosserviço estão a ser registadas e utilizadas na sua organização, incluindo o número de inscritos utilizadores para cada funcionalidade, com que frequência é utilizado o reset de palavras-passe de autosserviço para redefinir palavras-passe e pelo qual o método o reset acontece.

Para mais informações, consulte a utilização e insights dos métodos de [autenticação (pré-visualização)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Novos relatórios de segurança estão disponíveis para todos os administradores da AD Azure (Pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Segurança e Proteção de Identidade

Todos os administradores da AD Azure podem agora selecionar o banner no topo dos relatórios de segurança existentes, como os **Utilizadores sinalizados para** relatório de risco, para começar a usar a nova experiência de segurança, como mostra os utilizadores de **Risco** e os relatórios de entrada de **risco.** Com o tempo, todos os relatórios de segurança passarão das versões mais antigas para as novas versões, com os novos relatórios a fornecerem-lhe as seguintes capacidades adicionais:

- Filtragem e triagem avançadas

- Ações a granel, tais como a anulação do risco do utilizador

- Confirmação de entidades comprometidas ou seguras

- Estado de risco, cobertura: Em risco, dispensado, remediado e confirmado comprometido

Para mais informações, consulte o relatório de [relatórios](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) dos utilizadores de Risco e o [relatório de inscrição de Risco](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Novas Auditorias de Segurança para Serviços de Domínio Azure AD (Pré-visualização Pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Serviços de Domínio Azure AD  
**Capacidade do produto:** Serviços de Domínio Azure AD

Temos o prazer de anunciar o lançamento da Auditoria de Segurança do Serviço de Domínio do Domínio Azure para pré-visualização pública. A auditoria de segurança ajuda a fornecer-lhe informações críticas sobre os seus serviços de autenticação, através do streaming de eventos de auditoria de segurança para recursos direcionados, incluindo armazenamento Azure, espaços de trabalho Azure Log Analytics e Azure Event Hub, utilizando o Serviço de Domínio AD Azure portal.

Para mais informações, consulte [Enable Security Audits for Azure AD Domain Services (Preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nova federação direta B2B usando SAML/WS-Fed (Pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C

A federação direta ajuda a facilitar-lhe o trabalho com parceiros cuja solução de identidade gerida por TI não é a Azure AD, trabalhando com sistemas de identidade que suportam os padrões SAML ou WS-Fed. Depois de configurar uma relação direta com um parceiro, qualquer novo utilizador convidado que convidar a partir desse domínio pode colaborar consigo usando a sua conta organizacional existente, tornando a experiência do utilizador para os seus hóspedes mais perfeita.

Para mais informações, consulte a [Federação Direta com AD FS e fornecedores de terceiros para utilizadores convidados (pré-visualização)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o fornecimento de conta de utilizador para estas aplicações SaaS recentemente suportadas

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** Monitorização e Reportagem

Agora pode automatizar a criação, atualização e a eliminar as contas dos utilizadores destas aplicações recentemente integradas:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Diretório Federado](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figo](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Saltos](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Pico](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte o fornecimento de [utilizadores automate para aplicações SaaS com AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Novo cheque para nomes de grupo duplicados no portal Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração

Agora, quando criar ou atualizar um nome de grupo a partir do portal Azure AD, faremos um check para ver se está a duplicar um nome de grupo existente no seu recurso. Se determinarmos que o nome já está a ser usado por outro grupo, será-lhe pedido que modifique o seu nome.

Para mais informações, consulte [Gerir grupos no portal Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>A AD Azure suporta agora parâmetros de consulta estática em resposta (redirecionamento) URIs

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador

As aplicações Azure AD podem agora registar e utilizar URIs de resposta (redirecionamento) com parâmetros estáticos de consulta (por exemplo, `https://contoso.com/oauth2?idp=microsoft`) para pedidos oAuth 2.0. O parâmetro de consulta estática estático está sujeito a correspondência de cordas para URIs de resposta, como qualquer outra parte da resposta URI. Se não houver uma cadeia registada que corresponda ao redirect-uri descodificado por URL, o pedido é rejeitado. Se a resposta URI for encontrada, toda a cadeia é utilizada para redirecionar o utilizador, incluindo o parâmetro de consulta estática.

As URIs de resposta dinâmica ainda são proibidas porque representam um risco de segurança e não podem ser usadas para reter informações do Estado através de um pedido de autenticação. Para o efeito, utilize o parâmetro `state`.

Atualmente, as telas de registo de aplicações do portal Azure ainda bloqueiam parâmetros de consulta. No entanto, pode editar manualmente o manifesto da aplicação para adicionar e testar parâmetros de consulta na sua aplicação. Para mais informações, veja [quais as novidades para autenticação?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters)

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Os registos de atividade (MS Graph APIs) para AD Azure já estão disponíveis através de Cmdlets PowerShell

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Monitorização e Reportagem

Estamos entusiasmados por anunciar que os registos de atividade da Azure AD (relatórios de auditoria e sign-ins) estão agora disponíveis através do módulo PowerShell da AD Azure. Anteriormente, você poderia criar seus próprios scripts usando pontos finais MS Graph API, e agora nós estendemos essa capacidade a cmdlets PowerShell.

Para obter mais informações sobre como utilizar estes cmdlets, consulte [os cmdlets Azure AD PowerShell para reportagem](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Controlos de filtroatualizados para registos de auditoria e login em Azure AD

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Monitorização e Reportagem

Atualizámos os relatórios de registo de Auditoria e Login para que agora possa aplicar vários filtros sem ter de os adicionar como colunas nos ecrãs do relatório. Além disso, já pode decidir quantos filtros pretende mostrar no ecrã. Estas atualizações funcionam em conjunto para facilitar a leitura dos seus relatórios e mais abrangentes às suas necessidades.

Para obter mais informações sobre estas atualizações, consulte [registos](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) de auditoria do Filter e [filtrar atividades de login](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Junho de 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Novos riscosDetecções API para Microsoft Graph (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Segurança e Proteção de Identidade

Temos o prazer de anunciar que o novo riscoDetections API para o Microsoft Graph está agora em pré-visualização pública. Pode utilizar esta nova API para visualizar uma lista do utilizador relacionado com a Proteção de Identidade da sua organização e deteções de risco de início de sessão. Também pode utilizar esta API para consultar mais eficientemente as suas deteções de risco, incluindo detalhes sobre o tipo de deteção, estado, nível e muito mais.

Para mais informações, consulte a documentação de referência da API de deteção de [risco.](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações da AD Azure - junho de 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido

Em junho de 2019, adicionámos estas 22 novas aplicações com apoio da Federação à galeria de aplicações:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai ()](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN Client](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Helper Helper,](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial) [Costpoint,](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial) [GlobalOne,](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial) [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu,](https://www.scrible.com/sign-in/#/create-account) [PandaDoc,](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial) [Perceptyx,](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial) [Proptimise OS,](https://proptimise.co.uk/software/) [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager for Oracle Merchandising de Retalho, Oracle Access Manager para oracle E-Business Suite, Oracle IDCS para E-Business Suite, Oracle IDCS para PeopleSoft, Oracle IDCS para JD Edwards

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o fornecimento de conta de utilizador para estas aplicações SaaS recentemente suportadas

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** Monitorização e Reportagem

Agora pode automatizar a criação, atualização e a eliminar as contas dos utilizadores destas aplicações recentemente integradas:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Enviado](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte o fornecimento de [utilizadores automate para aplicações SaaS com AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Veja o progresso em tempo real do serviço de provisionamento da AD Azure

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida de identidade

Atualizámos a experiência de provisionamento de Anúncios Azure para incluir uma nova barra de progresso que lhe mostra o quão longe está no processo de fornecimento de utilizadores. Esta experiência atualizada também fornece informações sobre o número de utilizadores aprovisionados durante o ciclo atual, bem como quantos utilizadores foram aprovisionados até à data.

Para mais informações, [consulte Verifique o estado do fornecimento do utilizador](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Marca da empresa aparece agora nos ecrãs de sinal e erros

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador

Atualizámos o Azure AD para que a marca da sua empresa apareça agora nos ecrãs de sinalização e erro, bem como na página de entrada. Não é preciso fazer nada para ativar esta funcionalidade, o Azure AD simplesmente utiliza os ativos que já instalou na área de **marca da Empresa** do portal Azure.

Para obter mais informações sobre a configuração da marca da sua empresa, consulte adicionar branding às páginas do [Azure Ative Directory da sua organização.](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>O Servidor de Autenticação Multi-Factor (MFA) azure já não está disponível para novas implementações

**Tipo:** Depreciado  
**Categoria de serviço:** MFA  
**Capacidade do produto:** Segurança e Proteção de Identidade

A partir de 1 de julho de 2019, a Microsoft deixará de oferecer o MFA Server para novas implementações. Os novos clientes que pretendam exigir a autenticação de vários fatores na sua organização devem agora utilizar a autenticação azure multi-factor baseada na nuvem. Os clientes que ativaram o MFA Server antes de 1 de julho não verão uma mudança. Ainda poderá descarregar a versão mais recente, obter futuras atualizações e gerar credenciais de ativação.

Para mais informações, consulte [Iniciar-se com o Servidor de Autenticação Multi-Factor Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Para obter mais informações sobre a autenticação azure multi-factor baseada na nuvem, consulte o Planeamento de uma implementação de [autenticação azure multi-factor baseada na nuvem.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

---

## <a name="may-2019"></a>Maio de 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Alteração do serviço: Suporte futuro para apenas protocolos TLS 1.2 no serviço Proxy de Aplicação

**Tipo:** Plano de mudança  
**Categoria de serviço:** Procuração de aplicativos  
**Capacidade do produto:** Controlo de Acesso

Para ajudar a fornecer encriptação de melhor classe para os nossos clientes, estamos limitando o acesso a apenas protocolos TLS 1.2 no serviço Proxy de Aplicação. Esta mudança está gradualmente a ser lançada para clientes que já estão apenas a usar protocolos TLS 1.2, pelo que não deverá ver alterações.

A depreciação de TLS 1.0 e TLS 1.1 acontece no dia 31 de agosto de 2019, mas vamos fornecer um aviso avançado adicional, por isso terá tempo para se preparar para esta mudança. Para se preparar para esta alteração, certifique-se de que as combinações de servidores de clientes e servidores de navegador, incluindo quaisquer clientes que os seus utilizadores utilizem para aceder a aplicações publicadas através do Proxy de Aplicação, são atualizados para utilizar o protocolo TLS 1.2 para manter a ligação à Aplicação Serviço de procuração. Para mais informações, consulte [Adicionar uma aplicação no local para acesso remoto através do Proxy de Aplicação no Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Utilize o relatório de utilização e insights para visualizar os seus dados de sessão relacionados com aplicações

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** Monitorização e Reportagem

Pode agora utilizar o relatório de utilização e insights, localizado na área de **aplicações** da Enterprise do portal Azure, para obter uma visão centrada na aplicação dos seus dados de entrada, incluindo informações sobre:

- Aplicativos mais utilizados para a sua organização

- Apps com os sign-ins mais falhados

- Principais erros de inscrição para cada aplicação

Para mais informações sobre esta funcionalidade, consulte [o relatório De utilização e insights no portal azure Ative Diretório](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizar o fornecimento do seu utilizador a aplicações em nuvem utilizando a AD Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** Monitorização e Reportagem

Siga estes novos tutoriais para utilizar o Serviço de Provisionamento de AD Azure para automatizar a criação, eliminação e atualização das contas dos utilizadores para as seguintes aplicações baseadas na nuvem:

- [Rio Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [Segurança dos Detentores](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Também pode seguir este novo [tutorial dropbox,](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)que fornece informações sobre como fornecer objetos de grupo.

Para obter mais informações sobre como proteger melhor a sua organização através do fornecimento automatizado de conta de utilizador, consulte o fornecimento de [utilizadores automate para aplicações SaaS com AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>A pontuação de garantia de identidade já está disponível em Azure AD (disponibilidade geral)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** N/A  
**Capacidade do produto:** Segurança e Proteção de Identidade

Agora pode monitorizar e melhorar a sua postura de segurança de identidade utilizando a funcionalidade de pontuação segura de identidade em Azure AD. A função de pontuação segura de identidade utiliza um único dashboard para o ajudar:

- Meça objectivamente a sua postura de segurança de identidade, com base numa pontuação entre 1 e 223.

- Plano para as melhorias na segurança da sua identidade

- Reveja o sucesso das suas melhorias de segurança

Para obter mais informações sobre a funcionalidade de pontuação de segurança de identidade, consulte qual é a pontuação segura de [identidade no Diretório Ativo azure?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>A experiência de registos de novas aplicações já está disponível (disponibilidade geral)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Experiência de Desenvolvimento

A nova experiência de [registos](https://aka.ms/appregistrations) da App está agora em disponibilidade geral. Esta nova experiência inclui todas as principais funcionalidades que conhece do portal Azure e do portal de registo de aplicações e melhora-as através de:

- **Melhor gestão de aplicativos.** Em vez de ver as suas aplicações em diferentes portais, agora pode ver todas as suas aplicações num só local.

- **Registo simplificado da aplicação.** Desde a experiência de navegação melhorada até à experiência renovada de seleção de permissões, agora é mais fácil registar e gerir as suas apps.

- **Informação mais detalhada.** Pode encontrar mais detalhes sobre a sua aplicação, incluindo guias de arranque rápido e muito mais.

Para mais informações, consulte a [plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) e a experiência de registos da App está agora [geralmente disponível!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) anúncio de blog.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Novas capacidades disponíveis na API de Utilizadores Arriscados para Proteção de Identidade

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Segurança e Proteção de Identidade

Temos o prazer de anunciar que agora pode usar a API de Utilizadores Arriscados para recuperar o histórico de risco dos utilizadores, descartar utilizadores arriscados e confirmar os utilizadores como comprometidos. Esta alteração ajuda-o a atualizar de forma mais eficiente o estado de risco dos seus utilizadores e a compreender o seu histórico de risco.

Para mais informações, consulte a documentação de referência da [API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)dos Utilizadores Arriscados.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações da AD Azure - maio de 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido

Em maio de 2019, adicionámos estas 21 novas aplicações com apoio da Federação à galeria de aplicações:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Real Links,](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial) [Kianda,](https://app.kianda.com/sso/OpenID/AzureAD/) [Sign Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze,](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial) [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy,](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial) [Marketo Sales Engage,](https://toutapp.com/login) [ACLP,](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial) [OutSystems,](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial) [Meta4 Global HR,](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial) [Quantum Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Cobalt,](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial) [webMethods API Cloud,](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial) [RedFlag,](https://pocketstop.com/redflag/) [Whatfix,](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial) [Control,](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial) [JOBHUB,](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial) [NEOGOV,](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial) [Foodee,](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial) [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Melhoria de grupos de criação e experiências de gestão no portal Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração

Fizemos melhorias nas experiências relacionadas com grupos no portal Azure AD. Estas melhorias permitem aos administradores gerir melhor as listas de grupos, listas de membros e fornecer opções adicionais de criação.

Melhoramentos incluem:

- Filtragem básica por tipo de membro e tipo de grupo.

- Adição de novas colunas, como endereço Fonte e E-mail.

- Capacidade de grupos, membros e listas de proprietários multi-selecionados para fácil eliminação.

- Capacidade de escolher um endereço de e-mail e adicionar proprietários durante a criação de grupo.

Para mais informações, consulte [Criar um grupo básico e adicionar membros usando o Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Configure uma política de nomeação para os grupos do Office 365 no portal Azure AD (disponibilidade geral)

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração

Os administradores podem agora configurar uma política de nomeação para os grupos Office 365, utilizando o portal Azure AD. Esta alteração ajuda a impor convenções de nomeação consistentes para os grupos office 365 criados ou editados pelos utilizadores da sua organização.

Pode configurar a política de nomeação para os grupos office 365 de duas maneiras diferentes:

- Defina prefixos ou sufixos, que são automaticamente adicionados a um nome de grupo.

- Faça upload de um conjunto personalizado de palavras bloqueadas para a sua organização, que não são permitidas em nomes de grupo (por exemplo, "CEO, Payroll, HR").

Para mais informações, consulte [Enforce a Naming Policy for Office 365 groups](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Os pontos finais da Microsoft Graph API já estão disponíveis para registos de atividade da AD Azure (disponibilidade geral)

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Monitorização e Reportagem

Temos o prazer de anunciar a disponibilidade geral do suporte final da Microsoft Graph API para registos de atividade saqueado de AD Azure. Com esta versão, pode agora utilizar a versão 1.0 dos registos de auditoria da AD Azure, bem como os registos de entrada APIs.

Para mais informações, consulte a visão geral do [registo de auditoria da AD Azure](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Os administradores podem agora utilizar o Acesso Condicional para o processo de registo combinado (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Segurança e Proteção de Identidade  

Os administradores podem agora criar políticas de Acesso Condicional para utilização através da página de registo combinado. Isto inclui a aplicação de políticas para permitir o registo se:

- Os utilizadores estão numa rede de confiança.

- Os utilizadores são um baixo risco de inscrição.

- Os utilizadores estão num dispositivo gerido.

- Os utilizadores concordam com os termos de utilização da organização (TOU).

Para mais informações sobre acesso condicional e reset de palavra-passe, pode ver o Acesso Condicional para o MFA combinado da [AD Azure e redefinir](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)a experiência de registo de passwords post . Para obter mais informações sobre as políticas de Acesso Condicional para o processo de registo combinado, consulte as políticas de [Acesso Condicional para registo combinado.](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration) Para obter mais informações sobre os termos de utilização da AD Azure, consulte a [funcionalidade de utilização do Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Abril de 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nova deteção de inteligência de ameaça da AD Azure já está disponível como parte da Proteção de Identidade da AD Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade Azure AD  
**Capacidade do produto:** Segurança e Proteção de Identidade

A deteção de informações sobre ameaças da AD Azure está agora disponível como parte da funcionalidade de Proteção de Identidade Azure AD atualizada. Esta nova funcionalidade ajuda a indicar uma atividade incomum do utilizador para um utilizador ou atividade específico que seja consistente com padrões de ataque conhecidos baseados nas fontes de inteligência de ameaças internas e externas da Microsoft.

Para obter mais informações sobre a versão renovada da Azure AD Identity Protection, consulte as quatro principais melhorias de Proteção de Identidade da [AD Azure estão agora no blog de pré-visualização pública](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) e o What is [Azure Ative Directory Identity Protection (atualizado)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) artigo. Para obter mais informações sobre a deteção de informações sobre ameaças de AD Azure, consulte o artigo de deteção de riscos de deteção de riscos de proteção de identidade do [Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks)

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>A gestão de direitos da Azure AD já está disponível (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Governança de Identidade  
**Capacidade do produto:** Governança de Identidade

A gestão de direitos da Azure AD, agora em pré-visualização pública, ajuda os clientes a delegar a gestão dos pacotes de acesso, o que define como os colaboradores e parceiros de negóciopodem solicitar o acesso, quem deve aprovar, e quanto tempo têm acesso. Os pacotes de acesso podem gerir a adesão em grupos Azure AD e Office 365, atribuições de funções em aplicações empresariais e atribuições de funções para sites SharePoint Online. Leia mais sobre a gestão de direitos na [visão geral da gestão de direitos da Azure AD.](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) Para saber mais sobre a amplitude das funcionalidades de Governança identitária azure AD, incluindo Gestão privilegiada de identidade, avaliações de acesso e termos de uso, ver [O que é a Governança identitária azure AD?](../governance/identity-governance-overview.md)

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configure uma política de nomeação para os grupos do Office 365 no portal Azure AD (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração

Os administradores podem agora configurar uma política de nomeação para os grupos Office 365, utilizando o portal Azure AD. Esta alteração ajuda a impor convenções de nomeação consistentes para os grupos office 365 criados ou editados pelos utilizadores da sua organização.

Pode configurar a política de nomeação para os grupos office 365 de duas maneiras diferentes:

- Defina prefixos ou sufixos, que são automaticamente adicionados a um nome de grupo.

- Faça upload de um conjunto personalizado de palavras bloqueadas para a sua organização, que não são permitidas em nomes de grupo (por exemplo, "CEO, Payroll, HR").

Para mais informações, consulte [Enforce a Naming Policy for Office 365 groups](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Os registos da Atividade Azure AD já estão disponíveis no Monitor Azure (disponibilidade geral)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Monitorização e Reportagem

Para ajudar a abordar o seu feedback sobre visualizações com os registos de Atividade de AD Azure, estamos a introduzir uma nova funcionalidade insights no Log Analytics. Esta funcionalidade ajuda-o a obter informações sobre os seus recursos AD Azure utilizando os nossos modelos interativos, chamados Livros de Trabalho. Estes Livros de Trabalho pré-construídos podem fornecer detalhes para apps ou utilizadores, e incluem:

- **Inscrições.** Fornece detalhes para apps e utilizadores, incluindo a localização de login, o sistema operativo em uso ou cliente e versão do navegador, e o número de inscrições bem-sucedidas ou falhadas.

- **Autenticação legado e Acesso Condicional.** Fornece detalhes para apps e utilizadores usando a autenticação legado, incluindo o uso de Autenticação Multi-Factor desencadeado por políticas de Acesso Condicional, aplicações que usam políticas de Acesso Condicional, e assim por diante.

- **Análise de falha de intenção.** Ajuda-o a determinar se os seus erros de inscrição estão a ocorrer devido a uma ação do utilizador, problemas de política ou à sua infraestrutura.

- **Relatórios personalizados.** Pode criar novos livros de trabalho ou editar livros de trabalho existentes para ajudar a personalizar a funcionalidade Insights para a sua organização.

Para mais informações, consulte [Como utilizar os livros de trabalho do Azure Monitor para relatórios de Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações da AD Azure - abril 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido

Em abril de 2019, adicionámos estas 21 novas aplicações com apoio da Federação à galeria de aplicações:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate,](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial) [MobiControl,](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial) [Citrix NetScaler,](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial) [Shibumi,](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial) [Benchling,](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial) [MileIQ,](https://mileiq.onelink.me/991934284/7e980085) [PageDNA,](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial) [EduBrite LMS,](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial) [RStudio Connect,](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial) [AMMS,](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial) [Mitel Connect,](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial) [Alibaba Cloud (SSO baseado em papéis)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management,](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial) [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit,](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial) [Workgrid,](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial) [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nova opção de frequência de avaliações de acesso e seleção de múltiplos papéis

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Avaliações de Acesso  
**Capacidade do produto:** Governança de Identidade

Novas atualizações nas avaliações de acesso a Anúncio do Azure permitem-lhe:

- Altere a frequência dos seus comentários de acesso a **semi-anualmente,** para além das opções anteriormente existentes de semanal, mensal, trimestral e anual.

- Selecione várias funções de recurso Azure AD e Azure ao criar uma única revisão de acesso. Nesta situação, todas as funções são configuradas com as mesmas configurações e todos os revisores são notificados ao mesmo tempo.

Para obter mais informações sobre como criar uma revisão de acesso, consulte [Criar uma revisão de acesso de grupos ou aplicações nas avaliações de acesso da AD Azure](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Os sistemas de alerta de email Azure AD Connect estão em transição, enviando novas informações de remetente de e-mail para alguns clientes

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Sincronização ad  
**Capacidade do produto:** Plataforma

O Azure AD Connect está em processo de transição do nosso(s sistema's de alerta de e-mail), potencialmente mostrando a alguns clientes um novo remetente de e-mail. Para resolver este problema, deve adicionar `azure-noreply@microsoft.com` à lista de autorizações da sua organização ou não poderá continuar a receber alertas importantes do seu Office 365, Azure ou dos seus serviços Sync.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>As alterações ao sufixo upn são agora bem sucedidas entre domínios federados no Azure AD Connect

**Tipo:** Fixo  
**Categoria de serviço:** Sincronização ad  
**Capacidade do produto:** Plataforma

Agora pode alterar com sucesso o sufixo UPN de um utilizador de um domínio federado para outro domínio federado no Azure AD Connect. Esta correção significa que não deve mais experimentar a mensagem de erro FederatedDomainChangeError durante o ciclo de sincronização ou receber um e-mail de notificação afirmando: "Incapaz de atualizar este objeto no Diretório Ativo do Azure, porque o atributo [ FederatedUser.UserPrincipalName], não é válido. Atualize o valor nos seus serviços de diretório local".

Para mais informações, consulte Erros de [Resolução de Problemas durante](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror)a sincronização .

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Maior segurança utilizando a política de acesso condicional baseada na proteção de aplicações em Azure AD (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Segurança e Proteção de Identidade

O Acesso Condicional baseado na proteção de aplicações já está disponível utilizando a política de proteção de **aplicações Require.** Esta nova política ajuda a aumentar a segurança da sua organização, ajudando a prevenir:

- Os utilizadores que têm acesso a apps sem licença Microsoft Intune.

- Os utilizadores não conseguem obter uma política de proteção de aplicações Microsoft Intune.

- Os utilizadores que têm acesso a apps sem uma política de proteção de aplicações configurada pelo Microsoft Intune.

Para mais informações, consulte [como exigir a política de proteção de aplicações para acesso](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access)à aplicação na nuvem com acesso condicional .

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Novo suporte para o acesso único do Azure AD e o Acesso Condicional no Microsoft Edge (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Segurança e Proteção de Identidade

Melhorámos o nosso suporte a AD Azure para o Microsoft Edge, incluindo o fornecimento de um novo suporte para o Azure AD single sign-on e Conditional Access. Se já utilizou o Microsoft Intune Managed Browser, pode agora utilizar o Microsoft Edge.

Para obter mais informações sobre a configuração e gestão dos seus dispositivos e aplicações utilizando o Acesso Condicional, consulte [Requerer dispositivos geridos para acesso](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) a aplicações na nuvem com Acesso Condicional e Exigir [aplicações de clientes aprovadas para acesso a aplicações na nuvem com Acesso Condicional.](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) Para obter mais informações sobre como gerir o acesso utilizando as políticas do Microsoft Edge com as políticas do Microsoft Intune, consulte [gerir o acesso à Internet utilizando um navegador protegido por políticas microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Março de 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Quadro de Experiência de Identidade e apoio político personalizado no Diretório Ativo Azure B2C já está disponível (GA)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C

Pode agora criar políticas personalizadas em Azure AD B2C, incluindo as seguintes tarefas, que são suportadas em escala e sob o nosso Azure SLA:

- Crie e carregue as viagens de utilizador de autenticação personalizada utilizando políticas personalizadas.

- Descreva as viagens de utilizador passo a passo como trocas entre fornecedores de sinistros.

- Defina a ramificação condicional nas viagens do utilizador.

- Transforme e mapeie pedidos de utilização em decisões e comunicações em tempo real.

- Utilize serviços com a API disponíveis para descansar nas suas viagens de utilizador de autenticação personalizada. Por exemplo, com fornecedores de e-mail, CRMs e sistemas de autorização proprietárias.

- Federate com fornecedores de identidade que estejam em conformidade com o protocolo OpenIDConnect. Por exemplo, com a Multi-inquilino Azure AD, fornecedores de conta social ou fornecedores de verificação de dois fatores.

Para obter mais informações sobre a criação de políticas personalizadas, consulte as notas do [Developer para políticas personalizadas no Azure Ative Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) e leia o [post de blogue de Alex Simon, incluindo estudos de caso.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações da AD Azure - março de 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido

Em março de 2019, adicionámos estas 14 novas aplicações com apoio da Federação à galeria de aplicações:

[ISEC7 Mobile Exchange Delegado](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Sistema de Auditoria Baseado em Explicações](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool Performance Matters,](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial) [Cinode](https://cinode.com/), [Iris Intranet,](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial) [Empactis,](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial) [SmartDraw,](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial) [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Novos conectores de fornecimento de Zscaler e Atlassian na galeria Azure AD - março de 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** 3ª Integração do Partido

Automatizar a criação, atualização e a eliminar as contas dos utilizadores para as seguintes aplicações:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta,](https://aka.ms/ZscalerBetaProvisioning) [Zscaler One,](https://aka.ms/ZscalerOneProvisioning) [Zscaler Dois,](https://aka.ms/ZscalerTwoProvisioning) [Zscaler Three,](https://aka.ms/ZscalerThreeProvisioning) [Zscaler ZSCloud,](https://aka.ms/ZscalerZSCloudProvisioning) [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Para obter mais informações sobre como proteger melhor a sua organização através do fornecimento automatizado de conta de utilizador, consulte o fornecimento de [utilizadores automate para aplicações SaaS com AD Azure](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Restaurar e gerir o seu Escritório 365 grupos eliminados no portal Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração

Agora pode ver e gerir o seu Office 365 grupos apagados do portal Azure AD. Esta alteração ajuda-o a ver quais os grupos disponíveis para restaurar, além de permitir que você elimine permanentemente quaisquer grupos que não sejam necessários pela sua organização.

Para mais informações, consulte [restaurar grupos expirados ou eliminados](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>O único sinal on está agora disponível para aplicações in-preser seguras da Azure AD SAML através do Application Proxy (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Procuração de aplicativos  
**Capacidade do produto:** Controlo de Acesso

Agora pode fornecer uma única experiência de inscrição (SSO) para aplicações autenticadas no local, aplicações autenticadas pela SAML, juntamente com acesso remoto a estas aplicações através do Application Proxy. Para obter mais informações sobre como configurar o SAML SSO com as suas aplicações no local, consulte o [saml single sign-on para aplicações no local com Application Proxy (Preview)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>As aplicações dos clientes em loops de pedido serão interrompidas para melhorar a fiabilidade e a experiência do utilizador

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador

As aplicações de clientes podem emitir incorretamente centenas dos mesmos pedidos de login durante um curto período de tempo. Estes pedidos, sejam eles bem sucedidos ou não, todos contribuem para uma má experiência do utilizador e cargas de trabalho acrescidas para o IDP, aumentando a latência para todos os utilizadores e reduzindo a disponibilidade do IDP.

Esta atualização envia um erro `invalid_grant`: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` para aplicações de clientes que emitem pedidos duplicados várias vezes durante um curto período de tempo, fora do âmbito de funcionamento normal. As aplicações de clientes que se deparam com este problema devem mostrar um pedido interativo, exigindo que o utilizador volte a inscrever-se. Para mais informações sobre esta mudança e sobre como corrigir a sua aplicação se encontrar este erro, veja [quais as novidades para autenticação?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted)

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nova experiência de utilizador de Registos de Auditoria já disponível

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Monitorização e Reportagem

Criámos uma nova página de **registos** de auditoria da AD Azure para ajudar a melhorar a legibilidade e como procura as suas informações. Para ver a nova página de registos de **auditoria,** selecione **registos de auditoria** na secção **atividade** do Azure AD.

![Nova página de registos de auditoria, com informações de amostra](media/whats-new/audit-logs-page.png)

Para obter mais informações sobre a nova página de **registos de auditoria,** consulte relatórios de atividades de [auditoria no portal do Diretório Ativo Do Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Novos avisos e orientações para ajudar a prevenir o bloqueio acidental de administradores de políticas de acesso condicional mal configuradas

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Segurança e Proteção de Identidade

Para ajudar a evitar que os administradores se bloqueiem acidentalmente dos seus próprios inquilinos através de políticas de Acesso Condicional mal configuradas, criámos novos avisos e orientações atualizadas no portal Azure. Para obter mais informações sobre as novas orientações, consulte [As dependências de serviço no Acesso Condicional do Diretório Ativo azure.](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies)

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Melhores termos de utilização do utilizador final em dispositivos móveis

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Termos de utilização  
**Capacidade do produto:** Governação

Atualizámos os nossos termos de utilização existentes para ajudar a melhorar a forma como analisa e consente em termos de utilização num dispositivo móvel. Agora pode ampliar para dentro e para fora, voltar, descarregar a informação e selecionar hiperligações. Para mais informações sobre os termos de utilização atualizados, consulte a [funcionalidade de utilização do Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nova experiência de descarregamento de registos da New Azure AD Activity disponível

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Monitorização e Reportagem

Agora pode descarregar grandes quantidades de registos de atividade diretamente do portal Azure. Esta atualização permite-lhe:

- Descarregue até 250.000 filas.

- Seja notificado após o download completo.

- Personalize o seu nome de ficheiro.

- Determine o seu formato de saída, quer jSON quer CSV.

Para mais informações sobre esta funcionalidade, consulte [Quickstart: Descarregue um relatório](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report) de auditoria utilizando o portal Azure

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Alteração de rutura: Atualizações para condicionar avaliação por Exchange ActiveSync (EAS)

**Tipo:** Plano de mudança  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Controlo de Acesso

Estamos em processo de atualização de como o Exchange ActiveSync (EAS) avalia as seguintes condições:

- Localização do utilizador, com base no endereço país, região ou IP

- Risco de inscrição

- Plataforma de dispositivos

Se já utilizou estas condições nas suas políticas de Acesso Condicional, esteja ciente de que o comportamento da condição pode mudar. Por exemplo, se usou previamente a condição de localização do utilizador numa apólice, poderá encontrar a apólice agora ignorada com base na localização do utilizador.

---

## <a name="february-2019"></a>Fevereiro de 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Encriptação token AD SAML configurável Azure (pré-visualização pública) 

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO

Agora pode configurar qualquer aplicação SAML suportada para receber tokens SAML encriptados. Quando configurado e utilizado com uma aplicação, o Azure AD encripta as afirmações saml emitidas utilizando uma chave pública obtida a partir de um certificado armazenado em Azure AD.

Para obter mais informações sobre a configuração da encriptação token SAML, consulte a [encriptação token Da Configuração Azure AD SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Criar uma revisão de acesso para grupos ou aplicações usando avaliações de acesso a AD Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Avaliações de Acesso  
**Capacidade do produto:** Governação

Pode agora incluir vários grupos ou aplicações numa única revisão de acesso a AD Azure para a filiação em grupo ou atribuição de aplicações. As avaliações de acesso com vários grupos ou aplicações são configuradas utilizando as mesmas configurações e todos os revisores incluídos são notificados ao mesmo tempo.

Para obter mais informações sobre como criar uma revisão de acesso utilizando as Avaliações de Acesso a AD do Azure, consulte [Criar uma revisão de acesso de grupos ou aplicações em Comentários de Acesso AD Azure](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações da AD Azure - fevereiro de 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Em fevereiro de 2019, adicionámos estas 27 novas aplicações com apoio da Federação à galeria de aplicações:

[Passaporte Euromonitor,](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial) [MindTickle,](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial) [FINGER FAT,](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7) [AirStack,](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial) [Oracle Fusion ERP,](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial) [IDrive,](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial) [Skyward Qmlativ,](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial) [Brightidea,](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial) [AlertOps,](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial) [Soloinsight-CloudGate SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial)Permission Click, [Brandfolder,](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial) [StoregateSmartFile,](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial) [Pexip,](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial) [Stormboard,](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial) [Sísmico,](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial) [Share A Dream,](https://www.shareadream.org/how-it-works) [Bugsnag,](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial) [WebMethods Integration Cloud,](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial) [Knowledge Em qualquer lugar LMS,](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial) [CAMPUS DA,](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial) [Dados do Periscópio,](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial) [Netop Portal,](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial) [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud by Genesys,](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial) [ClickUp Productivity Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Registo combinado melhorado de MFA/SSPR

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Reset de palavra-passe self service  
**Capacidade do produto:** Autenticação do Utilizador

Em resposta ao feedback do cliente, melhorámos a experiência combinada de pré-visualização de registo SFA/SSPR, ajudando os seus utilizadores a registarem mais rapidamente as suas informações de segurança tanto para mfa como para SSPR. 

**Para ativar a experiência melhorada para os seus utilizadores de hoje, siga estes passos:**

1. Como administrador global ou administrador de utilizador, inscreva-se no portal Azure e vá ao **Azure Ative Directory > Definições de utilizador > Gerencie as definições para as funcionalidades de pré-visualização**do painel de acesso . 

2. Nos **Utilizadores que possam utilizar as funcionalidades de pré-visualização para registar e gerir informações** de segurança – a opção de atualização, opte por ativar as funcionalidades para um **grupo de utilizadores Selecionados** ou para **Todos os utilizadores.**

Ao longo das próximas semanas, vamos remover a capacidade de ativar a velha experiência combinada de pré-visualização de registo MFA/SSPR para inquilinos que ainda não o têm ligado.

**Para ver se o controlo será removido para o seu inquilino, siga estes passos:**

1. Como administrador global ou administrador de utilizador, inscreva-se no portal Azure e vá ao **Azure Ative Directory > Definições de utilizador > Gerencie as definições para as funcionalidades de pré-visualização**do painel de acesso .  

2. Se os Utilizadores que podem utilizar as funcionalidades de **pré-visualização para registar e gerir** a opção de informação de segurança estiver definido para **Nenhum,** a opção será removida do seu inquilino.

Independentemente de ter ativado previamente a antiga experiência combinada de pré-visualização de registo MFA/SSPR para utilizadores ou não, a experiência antiga será desligada numa data futura. Por isso, sugerimos vivamente que mude para a nova experiência melhorada o mais rápido possível.

Para obter mais informações sobre a experiência de registo melhorada, consulte as [melhorias cool para o Azure AD combinado MFA e experiência](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)de registo de redefinição de passwords .

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Experiência de gestão de políticas atualizada para fluxos de utilizadores

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C

Atualizámos o processo de criação e gestão de políticas para fluxos de utilizadores (anteriormente conhecidos como políticas incorporadas) mais fácil. Esta nova experiência é agora o padrão para todos os seus inquilinos da AD Azure.

Pode fornecer feedback e sugestões adicionais utilizando o sorriso ou ícones franzidos na área de **feedback enviar-nos** na parte superior do ecrã do portal.

Para obter mais informações sobre a nova experiência de gestão de políticas, consulte o [Azure AD B2C agora tem personalização JavaScript e muitas mais novas funcionalidades](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Escolha versões específicas de elementos de página fornecidas pelo Azure AD B2C

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C

Agora pode escolher uma versão específica dos elementos da página fornecidos pelo Azure AD B2C. Ao selecionar uma versão específica, pode testar as suas atualizações antes de aparecerem numa página e pode obter um comportamento previsível. Além disso, pode agora optar por impor versões de página específicas para permitir personalizações JavaScript. Para ativar esta funcionalidade, vá à página **Propriedades** nos fluxos do utilizador.

Para obter mais informações sobre a escolha de versões específicas de elementos de página, consulte o [Azure AD B2C agora tem personalização JavaScript e muitas mais novidades](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) no blog.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Requisitos de senha de utilizador final configuráveis para B2C (GA)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C

Agora pode configurar a complexidade da sua organização para os seus utilizadores finais, em vez de ter de usar a sua política de senhas AD Azure nativa. A partir da lâmina **De Propriedades** dos fluxos do seu utilizador (anteriormente conhecidacomo as suas políticas incorporadas), pode escolher uma complexidade de palavra-passe de **Simple** ou **Strong,** ou pode criar **um** conjunto personalizado de requisitos.

Para obter mais informações sobre a configuração do requisito de complexidade da palavra-passe, consulte os requisitos de [complexidade da Configuração para as palavras-passe no Diretório Ativo Azure B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Novos modelos padrão para experiências de autenticação de marca personalizada

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C

Pode utilizar os nossos novos modelos predefinidos, localizados na lâmina de layouts da Página dos **fluxos** do seu utilizador (anteriormente conhecidos como políticas incorporadas), para criar uma experiência de autenticação de marca personalizada para os seus utilizadores.

Para mais informações sobre a utilização dos modelos, consulte [o Azure AD B2C agora tem personalização JavaScript e muitas mais novas funcionalidades](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Janeiro de 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Colaboração do Diretório Ativo B2B utilizando a autenticação de código de acesso único (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C

Introduzimos a autenticação de código de acesso único (OTP) para utilizadores convidados B2B que não podem ser autenticados através de outros meios como o Azure AD, uma conta Microsoft (MSA) ou a federação da Google. Este novo método de autenticação significa que os utilizadores convidados não têm de criar uma nova conta Microsoft. Em vez disso, ao resgatar um convite ou aceder a um recurso partilhado, um utilizador convidado pode solicitar um código temporário para ser enviado para um endereço de e-mail. Utilizando este código temporário, o utilizador convidado pode continuar a iniciar sessão.

Para mais informações, consulte a autenticação de [senha de um e-mail (pré-visualização)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) e o blog, [a Azure AD torna a partilha e colaboração perfeitas para qualquer utilizador com qualquer conta](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Novas definições de cookies de proxy de aplicação da AD Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Procuração de aplicativos  
**Capacidade do produto:** Controlo de Acesso

Introduzimos três novas definições de cookies, disponíveis para as suas apps que são publicadas através do Application Proxy:

- **Utilize cookie http-only.** Define a bandeira **HTTPOnly** no acesso ao seu Proxy de aplicação e nos cookies de sessão. A ligação desta definição proporciona benefícios adicionais de segurança, tais como ajudar a evitar copiar ou modificar cookies através de scripts do lado do cliente. Recomendamos que ligue esta bandeira (escolha **Sim)** para os benefícios adicionais.

- **Use cookie seguro.** Define a bandeira **'Secure'** no acesso ao seu Pedido proxy e nos cookies de sessão. A ativação desta definição proporciona benefícios adicionais de segurança, certificando-se de que os cookies são transmitidos apenas através de canais seguros TLS, como HTTPS. Recomendamos que ligue esta bandeira (escolha **Sim)** para os benefícios adicionais.

- **Utilize cookie persistente.** Impede que os cookies de acesso expirem quando o navegador da Web está fechado. Estes cookies duram a vida útil do token de acesso. No entanto, os cookies são redefinidos se o tempo de validade for atingido ou se o utilizador apagar manualmente o cookie. Recomendamos que mantenha a definição padrão **Não,** apenas ligando a definição para aplicações mais antigas que não partilhem cookies entre processos.

Para obter mais informações sobre os novos cookies, consulte [as definições de Cookiepara aceder às aplicações no local no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações da AD Azure - janeiro de 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Em janeiro de 2019, adicionámos estas 35 novas aplicações com apoio da Federação à galeria de aplicações:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent Palette](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite,](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial) [Cisco Umbrella,](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial) [Zscaler Internet Access Administrator,](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial) [Expiration Reminder](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax,](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial) [Verb,](https://app.verb.net/login) [OpenLattice,](https://openlattice.com/agora) [TheOrgWiki,](https://www.theorgwiki.com/signup) [Pavaso Digital Close,](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial) [GoodPractice Toolkit,](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial) [Cloud Service PICCO,](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial) [AuditBoard,](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial) [iProva,](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial) [Workable,](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial) [CallPlease,](https://webapp.callplease.com/create-account/create-account.html) [Sistema GTNexus SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar,](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial) [Coralogixv,](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial) [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARES for Enterprise,](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial) [K2 para o Office 365,](https://www.k2.com/O365) [Xledger,](https://www.xledger.net/) [iDiD Manager,](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial) [HighGear,](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial) [Visitly,](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial) [Korn Ferry ALP,](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial) [Acadia,](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial) [Plataforma Adoddle cSaas](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Novos melhoramentos de Proteção de Identidade Azure AD (pré-visualização pública)

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Segurança e Proteção de Identidade

Estamos entusiasmados por anunciar que adicionámos as seguintes melhorias à oferta pública de pré-visualização da Azure AD Identity Protection, incluindo:

- Uma interface de utilizador atualizada e mais integrada

- APIs adicionais

- Melhor avaliação de risco através da aprendizagem automática

- Alinhamento em todo o produto entre utilizadores arriscados e inscrições arriscadas

Para mais informações sobre as melhorias, consulte o que é a [Azure Ative Directory Identity Protection (atualizada)?](https://aka.ms/IdentityProtectionDocs) para aprender mais e partilhar os seus pensamentos através dos pedidos de produto.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nova funcionalidade de Bloqueio de Aplicações para a aplicação Microsoft Authenticator em dispositivos iOS e Android

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicação autenticadora da Microsoft  
**Capacidade do produto:** Segurança e Proteção de Identidade

Para manter as suas definições de senha única, informações de aplicações e configurações de aplicações mais seguras, pode ligar a funcionalidade App Lock na aplicação Microsoft Authenticator. Ligar o Bloqueio de Aplicações significa que será convidado a autenticar usando o seu PIN ou biométrico sempre que abrir a aplicação Microsoft Authenticator.

Para mais informações, consulte a [aplicação faq](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq)do Autenticador microsoft .

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Capacidades de exportação de identidade privilegiada azure (PIM) melhoradas

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão privilegiada da identidade  
**Capacidade do produto:** Gestão privilegiada da identidade

Os administradores privilegiados de Gestão de Identidade (PIM) podem agora exportar todas as atribuições de funções ativas e elegíveis para um recurso específico, que inclui atribuições de funções para todos os recursos infantis. Anteriormente, era difícil para os administradores obter uma lista completa de atribuições de funções para uma subscrição e tinham de exportar atribuições de funções para cada recurso específico.

Para mais informações, consulte a [atividade do View e audite o histórico de recursos azure na PIM.](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)

---

## <a name="novemberdecember-2018"></a>Novembro/dezembro de 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Utilizadores removidos do âmbito de sincronização já não mudam para contas apenas na nuvem

**Tipo:** Fixo  
**Categoria de serviço:** Gestão de Utilizadores  
**Capacidade do produto:** Diretório

>[!Important]
>Ouvimos e compreendemos a sua frustração por causa desta correção. Portanto, revertemos esta mudança até que possamos facilitar a correção para você implementar na sua organização.

Corrigimos um bug no qual a bandeira DirSyncEnabled de um utilizador seria erroneamente mudada para **False** quando o objeto Ative Directory Domain Services (AD DS) foi excluído do âmbito de sincronização e depois transferido para o Caixote do Reciclo em Azure AD no ciclo de sincronização seguinte. Como resultado desta correção, se o utilizador for excluído do âmbito de sincronização e posteriormente restaurado do Azure AD Recycle Bin, a conta de utilizador permanece tão sincronizada a partir de ad-local, como esperado, e não pode ser gerida na nuvem uma vez que a sua fonte de autoridade (SoA) permanece como no local ad.

Antes desta correção, houve um problema quando a bandeira DirSyncEnabled foi trocada para False. Deu a impressão errada de que estas contas foram convertidas em objetos apenas em nuvem e que as contas podiam ser geridas na nuvem. No entanto, as contas ainda mantiveram o seu SoA como no local e todas as propriedades sincronizadas (atributos de sombra) provenientes de AD no local. Esta condição causou múltiplos problemas em Azure AD e outras cargas de trabalho na nuvem (como o Exchange Online) que esperavam tratar estas contas como sincronizadas a partir de AD, mas que agora se comportavam como contas apenas na nuvem.

Neste momento, a única maneira de converter verdadeiramente uma conta sincronizada de AD para conta apenas na nuvem é desativando o DirSync ao nível do inquilino, o que desencadeia uma operação de backend para transferir o SoA. Este tipo de alteração SoA requer (mas não se limita a) limpar todos os atributos relacionados no local (como LastDirSyncTime e atributos sombra) e enviar um sinal para outras cargas de trabalho em nuvem para ter o seu objeto respetivo convertido para uma conta apenas na nuvem também .

Esta correção impede, consequentemente, atualizações diretas do atributo Imutável de um utilizador sincronizado a partir de AD, que em alguns cenários no passado foram necessários. Por design, o ID imutável de um objeto em Azure AD, como o nome indica, é para ser imutável. Novas funcionalidades implementadas no Azure AD Connect Health e no azure AD Connect Synchronization cliente estão disponíveis para abordar tais cenários:

- **Atualização Immutável de Id em larga escala para muitos utilizadores numa abordagem encenada**
  
  Por exemplo, é preciso fazer uma longa migração inter-florestal aD DS. Solução: Utilize o Azure AD Connect para **configurar** a âncora de origem e, à medida que o utilizador migra, copie os valores imutáveis existentes de Azure AD para o atributo ms-DS-Consistência-Guia do utilizador local da nova floresta. Para mais informações, consulte [A utilização de ms-DS-Consistência como fonteAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Atualizações immutáveis de ID em larga escala para muitos utilizadores de uma só vez**

  Por exemplo, ao implementar o Azure AD Connect comete um erro, e agora precisa de alterar o atributo SourceAnchor. Solução: Desative o DirSync ao nível do inquilino e limpe todos os valores immutáveis de ID inválidos. Para mais informações, consulte [Desligação da sincronização do diretório para o Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Recompor-se no local com um utilizador existente em Azure AD** Por exemplo, um utilizador que foi recriado em DS Ad gera uma duplicação na conta Azure AD em vez de recompará-lo com uma conta Azure AD existente (objeto órfão). Solução: Utilize a Azure AD Connect Health no portal Azure para remapear a Âncora Fonte/ID Imutável. Para mais informações, consulte o cenário do [objeto órfão.](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario)

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Alteração de Rutura: Atualizações para a auditoria e login saem através do Monitor Azure

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Monitorização e Reportagem

Estamos atualmente a publicar os streams de registo de Auditoria e Log-in através do Monitor Azure, para que possa integrar os ficheiros de registo sem problemas com as suas ferramentas SIEM ou com o Log Analytics. Com base no seu feedback, e na preparação para o anúncio de disponibilidade geral desta funcionalidade, estamos a fazer as seguintes alterações ao nosso esquema. Estas alterações de esquemas e as suas atualizações de documentação relacionadas vão acontecer na primeira semana de janeiro.

#### <a name="new-fields-in-the-audit-schema"></a>Novos campos no esquema de auditoria
Estamos adicionando um novo campo **De Operação Tipo,** para fornecer o tipo de operação realizada no recurso. Por exemplo, **Adicionar,** **Atualizar**ou **Eliminar**.

#### <a name="changed-fields-in-the-audit-schema"></a>Campos alterados no esquema de auditoria
Os seguintes campos estão a mudar no esquema de auditoria:

|Nome do campo|O que mudou|Valores antigos|Novos Valores|
|----------|------------|----------|----------|
|Categoria|Este era o campo **do Nome de Serviço.** Agora é o campo das Categorias de **Auditoria.** **O nome** de serviço foi renomeado para o campo **loggedByService.**|<ul><li>Aprovisionamento de Contas</li><li>Diretório do Núcleo</li><li>Reset de palavra-passe self-service</li></ul>|<ul><li>Gestão de Utilizadores</li><li>Gestão de Grupos</li><li>Gestão de Aplicações</li></ul>|
|recursos-alvo|Inclui **targetResourceType** no nível superior.|&nbsp;|<ul><li>Política</li><li>Aplicação</li><li>Utilizador</li><li>Grupo</li></ul>|
|loggedByService|Fornece o nome do serviço que gerou o registo de auditoria.|Null|<ul><li>Aprovisionamento de Contas</li><li>Diretório do Núcleo</li><li>Reposição personalizada de palavra-passe</li></ul>|
|Resultado|Fornece o resultado dos registos de auditoria. Anteriormente, isto foi enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li></ul>|<ul><li>Êxito</li><li>Falha</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Campos alterados no esquema de sign-in
Os seguintes campos estão a mudar no esquema de inscrição:

|Nome do campo|O que mudou|Valores antigos|Novos Valores|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Este era o campo **de políticas de acesso condicional.** É agora o campo **de Políticas de Acesso Condicionado aplicada.**|Sem mudança|Sem mudança|
|conditionalAccessStatus|Fornece o resultado do Estado da Política de Acesso Condicional no início do sessão. Anteriormente, isto foi enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Êxito</li><li>Falha</li><li>Não Aplicado</li><li>Desativado</li></ul>|
|aplicaçõesPolíticas de Acesso Condicional: resultado|Fornece o resultado do Estado de Política de Acesso Condicional individual no início do registo. Anteriormente, isto foi enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Êxito</li><li>Falha</li><li>Não Aplicado</li><li>Desativado</li></ul>|

Para mais informações sobre o esquema, consulte [Interprete os registos de auditoria da AD Azure no Azure Monitor (pré-visualização)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Melhorias na Proteção de Identidade do modelo de aprendizagem automática supervisionado e do motor de pontuação de risco

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Pontuações de Risco

As melhorias no utilizador relacionado com a proteção da identidade e no motor de avaliação de riscos de acesso podem ajudar a melhorar a precisão e cobertura do risco do utilizador. Os administradores podem notar que o nível de risco do utilizador já não está diretamente ligado ao nível de risco de deteções específicas, e que há um aumento no número e nível de eventos de inscrição de risco.

As deteções de risco são agora avaliadas pelo modelo de aprendizagem automática supervisionado, que calcula o risco do utilizador utilizando funcionalidades adicionais dos sign-ins do utilizador e um padrão de deteção. Com base neste modelo, o administrador pode encontrar utilizadores com pontuações de risco elevados, mesmo que as deteções associadas a esse utilizador sejam de baixo ou médio risco. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Os administradores podem redefinir a sua própria palavra-passe utilizando a aplicação Microsoft Authenticator (pré-visualização pública)

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Reset de palavra-passe self service  
**Capacidade do produto:** Autenticação do Utilizador

Os administradores da AD Azure podem agora redefinir a sua própria palavra-passe utilizando as notificações da aplicação Microsoft Authenticator ou um código de qualquer aplicação ou ficha de hardware do autenticador móvel. Para repor a sua própria palavra-passe, os administradores poderão agora utilizar dois dos seguintes métodos:

- Notificação de aplicação do Autenticador microsoft

- Outra aplicação de autenticador móvel / Código token hardware

- Email

- Chamada telefónica

- Mensagem de texto

Para obter mais informações sobre a utilização da aplicação Microsoft Authenticator para redefinir palavras-passe, consulte o [reset da palavra-passe autosserviço da Azure AD - Aplicação móvel e SSPR (Pré-visualização)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nova função de Administrador de Dispositivos em Nuvem Azure (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Registo e Gestão de Dispositivos  
**Capacidade do produto:** Controlo de acessos

Os administradores podem atribuir aos utilizadores a nova função de Administrador de Dispositivos Cloud para executar tarefas de administrador de dispositivos em nuvem. Os utilizadores designados a função Cloud Device Administrators podem ativar, desativar e eliminar dispositivos em AD Azure, juntamente com a leitura de teclas Windows 10 BitLocker (se estiverem presentes) no portal Azure.

Para mais informações sobre funções e permissões, consulte as funções de [administrador de atribuição no Diretório Ativo azure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Gerencie os seus dispositivos utilizando a nova marca de tempo de atividade em Azure AD (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Registo e Gestão de Dispositivos  
**Capacidade do produto:** Gestão do ciclo de vida do dispositivo

Percebemos que com o tempo deve refrescar e retirar os dispositivos das suas organizações em Azure AD, para evitar ter dispositivos velhos no seu ambiente. Para ajudar neste processo, a Azure AD atualiza agora os seus dispositivos com um novo timestamp de atividade, ajudando-o a gerir o seu ciclo de vida do dispositivo.

Para mais informações sobre como obter e usar esta marca de tempo, consulte [Como: Gerir os dispositivos velhos em Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Os administradores podem exigir que os utilizadores aceitem um termos de utilização em cada dispositivo

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Termos de utilização  
**Capacidade do produto:** Governação
 
Os administradores podem agora ativar os **utilizadores exigirem que os utilizadores consintam em todas as** opções do dispositivo para exigir que os seus utilizadores aceitem os seus termos de utilização em todos os dispositivos que estão a utilizar no seu inquilino.

Para mais informações, consulte a [secção de utilização por dispositivo da função de utilização do Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Os administradores podem configurar um termos de uso para expirar com base num calendário recorrente

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Termos de utilização  
**Capacidade do produto:** Governação
 

Os administradores podem agora ativar a opção de **consentimento sinuoso** para que os termos de utilização expirem para todos os seus utilizadores com base no seu calendário recorrente especificado. O horário pode ser anualmente, bianual, trimestral ou mensal. Após a expiração dos termos de utilização, os utilizadores devem reaceitar.

Para mais informações, consulte a secção adicionar termos de utilização da funcionalidade de utilização do [Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Os administradores podem configurar um termos de utilização para expirar com base na agenda de cada utilizador

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Termos de utilização  
**Capacidade do produto:** Governação

Os administradores podem agora especificar uma duração que o utilizador deve reaceitar um termos de utilização. Por exemplo, os administradores podem especificar que os utilizadores devem reaceitar um termos de utilização a cada 90 dias.

Para mais informações, consulte a secção adicionar termos de utilização da funcionalidade de utilização do [Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Novos e-mails de Gestão de Identidade Privilegiada (PIM) da Azure AD para funções de Diretório Ativo Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão privilegiada da identidade  
**Capacidade do produto:** Gestão privilegiada da identidade
 
Os clientes que utilizam a Azure AD Privileged Identity Management (PIM) podem agora receber um e-mail semanal de digestão, incluindo as seguintes informações para os últimos sete dias:

- Visão geral das principais atribuições de funções elegíveis e permanentes

- Número de utilizadores que ativam funções

- Número de utilizadores atribuídos a funções na PIM

- Número de utilizadores atribuídos a funções fora da PIM

- Número de utilizadores "tornados permanentes" na PIM

Para mais informações sobre a PIM e as notificações de email disponíveis, consulte [notificações por e-mail na PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>O licenciamento baseado em grupo está agora geralmente disponível

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Diretório

O licenciamento baseado em grupo está fora de pré-visualização pública e está agora geralmente disponível. Como parte deste lançamento geral, tornámos esta funcionalidade mais escalável e adicionámos a capacidade de reprocessar as atribuições de licenciamento baseadas em grupo para um único utilizador e a capacidade de usar o licenciamento baseado em grupo com licenças do Office 365 E3/A3.

Para obter mais informações sobre licenciamento baseado em grupo, consulte [O que é o licenciamento baseado em grupo no Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações da AD Azure - novembro de 2018

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Em novembro de 2018, adicionámos estas 26 novas aplicações com apoio da Federação à galeria de aplicações:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour,](https://getehour.com/try-now) [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux,](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial) [DriveDollar,](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview) [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya,](https://alayagood.com/en/demo/) [HeyBuddy,](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial) [Wrike SAML,](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial) [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial) [Plex Apps - Classic Test ](https://test.plexonline.com/signon), [Plex Apps – Classic](https://www.plexonline.com/signon), [Plex Apps - UX Test](https://test.cloud.plex.com/sso), [Plex Apps – UX,](https://cloud.plex.com/sso) [Plex Apps – IAM,](https://accounts.plex.com/) [CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration) 

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Outubro de 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Registos do Azure AD agora trabalham com o Azure Log Analytics (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Monitorização e Reportagem

Temos o prazer de anunciar que agora pode reencaminhar os registos do Azure AD para o Azure Log Analytics! Esta funcionalidade mais pedidas ajuda dá a acesso ainda melhor para análise para o seu negócio, operações e segurança, bem como uma forma de ajudar a monitorizar a sua infraestrutura. Para mais informações, consulte os registos de atividade de [diretório ativo do Azure no Blog Azure Log Analytics.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Novas aplicações federadas disponíveis na Galeria de aplicações do Azure AD - Outubro de 2018

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido

Em Outubro de 2018, adicionámos que suportam a 14 novos aplicativos com a Federação para a Galeria de aplicações:

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM,](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial)ambyint, [MyWorkDrive,](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial) [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet,](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial) [Appraisd,](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial) [Workspot Control,](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial) [Shuccho Navi,](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial) [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Notificações por correio eletrónico dos serviços de domínio do Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Serviços de Domínio Azure AD  
**Capacidade do produto:** Serviços de Domínio Azure AD

O Azure AD Domain Services fornece alertas no portal do Azure sobre configurações incorretas ou problemas com o seu domínio gerido. Estes alertas incluem guias passo a passo para poder experimentar corrigir os problemas sem ter de contactar o suporte.

A partir de Outubro, poderá personalizar as definições de notificação para o seu domínio gerido, de modo quando ocorrem novos alertas, é enviado um e-mail para um grupo designado de pessoas, eliminando a necessidade de verifique constantemente o portal para atualizações.

Para mais informações, consulte as definições de [Notificação nos Serviços de Domínio AD do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>O Azure AD portal suporta através do domínio de ForceDelete API para eliminar os domínios personalizados 

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Gestão de Diretórios  
**Capacidade do produto:** Diretório

É com prazer que Anunciamos que agora pode utilizar o domínio de ForceDelete API para eliminar os nomes de domínio personalizado ao modo assíncrono mudar o nome de referências, como os utilizadores, grupos e aplicações do seu nome de domínio personalizado (contoso.com) para o (de nome de domínio predefinido inicial contoso.onmicrosoft.com).

Esta alteração ajuda a eliminar mais rapidamente os seus nomes de domínio personalizado se sua organização já não utiliza o nome, ou se precisar de utilizar o nome de domínio com o outro Azure AD.

Para mais informações, consulte [Delete um nome de domínio personalizado](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Setembro de 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Permissões de função de administrador atualizadas para grupos dinâmicos

**Tipo:** Fixo  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração

Corrigimos um problema para que as funções de administrador específico podem criar e atualizar regras de associação dinâmica, sem terem de ser o proprietário do grupo.

As funções são:

- Administrador global

- Administrador insinado

- Administrador de utilizadores

Para mais informações, consulte [Criar um grupo dinâmico e verificar o estado](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Definições de configuração do Início de Sessão Único (SSO) simplificado para algumas aplicações de terceiros

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO

Sabemos que a definição de cópia de segurança único início de sessão (SSO) para Software como serviço (SaaS) aplicações podem ser um desafio devido à natureza exclusiva de cada configuração de aplicações. Criámos uma experiência de configuração simplificada para as definições de configuração de SSO para as seguintes aplicações de SaaS de terceiros de preencher automaticamente:

- Zendesk

- ArcGis Online

- Jamf Pro

Para começar a utilizar esta experiência de um clique, vá ao **portal Azure** > página de **configuração SSO** para a aplicação. Para mais informações, consulte a [integração da aplicação SaaS com o Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>O Azure Active Directory - onde a seus dados estão localizados? página

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** GoLocal

Selecione a região da sua empresa a partir do **Diretório Ativo Azure - Onde está** a página localizada nos seus dados para ver quais os datacenter sinuosos do Azure que abrigam os seus dados Azure AD em repouso para todos os serviços da Azure AD. Pode filtrar as informações por específicos de serviços do Azure AD para a região da sua empresa.

Para aceder a esta funcionalidade e para mais informações, consulte [o Diretório Ativo do Azure - Onde estão os seus dados localizados](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Novo plano de implementação disponível para o painel Acesso do My Apps

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Minhas Apps  
**Capacidade do produto:** SSO

Confira o novo plano de implementação disponível para o painel My Apps Access (https://aka.ms/deploymentplans).
O painel de acesso de aplicações My fornece aos usuários um único local para encontrar e aceder às suas aplicações. Esse portal também fornece aos usuários oportunidades de Self-serviços, tais como pedir acesso a aplicações e grupos ou gerir o acesso a estes recursos em nome de outros.

Para mais informações, consulte [o portal My Apps?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Novo separador Resolução de Problemas e Suporte na página Registos de Inícios de Sessão do portal do Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Monitorização e Reportagem

O novo separador **de resolução de problemas e suporte** na página de **Sign-ins** do portal Azure, destina-se a ajudar os administradores e engenheiros de apoio a resolver problemas relacionados com os sign-ins da AD Azure. Este novo separador fornece o código de erro, a mensagem de erro e as recomendações de reparação (se houver) para ajudar a resolver o problema. Se não conseguir resolver o problema, também lhe damos uma nova forma de criar um bilhete de apoio utilizando a Cópia para a experiência de **clipboard,** que preenche os campos de ID de **Pedido** e **Data (UTC)** para o ficheiro de log no seu bilhete de apoio.  

![Registos de login mostrando o novo separador](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Suporte avançado para propriedades de extensão personalizadas utilizadas para criar regras de associação dinâmica

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração

Com esta atualização, pode agora clicar no link de propriedades de **extensão personalizada** do construtor de regras do grupo de utilizadores dinâmico, introduzir o seu ID de aplicação único e receber a lista completa de propriedades de extensão personalizadas para usar ao criar uma regra de subscrição dinâmica para os utilizadores. Esta lista também pode ser atualizada para obter quaisquer novas propriedades de extensão personalizado para essa aplicação.

Para mais informações sobre a utilização de propriedades de extensão personalizada para regras de adesão dinâmicas, consulte propriedades de [extensão e propriedades de extensão personalizadas](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novas aplicações de clientes aprovadas para acesso condicional baseado em aplicativos Azure AD

**Tipo:** Plano de mudança  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Segurança e proteção de identidade

As seguintes aplicações estão na lista de [aplicações de clientes aprovadas:](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)

- Microsoft To-Do

- Microsoft Stream

Para obter mais informações, consulte:

- [Acesso Condicional baseado em aplicativos Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Novo suporte para Reposição Personalizada de Palavra-passe a partir do ecrã de Bloqueio do Windows 7/8/8.1

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** SSPR  
**Capacidade do produto:** Autenticação do Utilizador

Depois de configurar esta nova funcionalidade, os utilizadores verão um link para redefinir a sua palavra-passe a partir do ecrã **'Lock'** de um dispositivo que executa o Windows 7, Windows 8 ou Windows 8.1. Ao clicar nesse link, o utilizador é orientado através do mesmo fluxo de reposição de palavra-passe como, por meio de navegador da web.

Para mais informações, consulte [Como ativar a reposição de palavra-passe a partir do Windows 7, 8 e 8.1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Aviso de alteração: Os códigos de autorização já não estarão disponíveis para reutilização 

**Tipo:** Plano de mudança  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador

A partir de 15 de Novembro de 2018, do Azure AD irá parar abertos ao recebimento de códigos de autenticação utilizados anteriormente para aplicações. Esta alteração de segurança ajuda a trazer do Azure AD em conformidade com a especificação de OAuth e será imposta em pontos finais de ambos o v1 e v2.

Se a sua aplicação reutiliza os códigos de autorização para obter os tokens de vários recursos, recomendamos que utilize o código para obter um token de atualização e, em seguida, utilizar esse token de atualização para adquirir tokens adicionais para outros recursos. Códigos de autorização só podem ser utilizados uma vez, mas os tokens de atualização podem ser utilizadas várias vezes em vários recursos. Uma aplicação que tente reutilizar um código de autenticação durante o fluxo de código do OAuth obterá um erro de invalid_grant.

Para esta e outras alterações relacionadas com protocolos, consulte [a lista completa do que é novo para autenticação.](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações do Azure AD - setembro de 2018

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Em Setembro de 2018, adicionámos que suportam a 16 novos aplicativos com a Federação para a Galeria de aplicações:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet Recruiting Software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud,](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial) [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma,](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial)join.me, [ZephyrSSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial) [Silverback,](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial)Riverbed Xirrus EasyPass, [Rackspace SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial)Enlyft SSO para Azure, SurveyMonkey, [Convento,](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial) [dmarcianmarcia](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Suporte para métodos de transformações de afirmações adicionais

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO

Introduzimos novos métodos de transformação de reivindicações, ToLower() e ToUpper(), que podem ser aplicados a tokens SAML a partir da página de **configuração de sinal único** baseada em SAML.

Para mais informações, consulte [Como personalizar as reclamações emitidas no token SAML para aplicações empresariais em Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>IU de configuração de aplicações baseado em SAML atualizado (pré-visualização)

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO

Como parte da nossa IU de configuração de aplicação atualizado baseado em SAML, que irá obter:

- Uma experiência atualizada passo a passo para configurar as suas aplicações baseadas em SAML.

- Mais visibilidade sobre as novidades em falta ou incorretas na sua configuração.

- A capacidade de adicionar vários endereços de e-mail de notificação de certificado de expiração.

- Novos métodos de transformação de afirmação, ToLower() e ToUpper() e muito mais.

- Uma forma de carregar o seu próprio certificado das suas aplicações empresariais de assinatura de tokens.

- Uma forma de definir o formato NameID para aplicações SAML e uma forma de definir o valor de NameID como extensões de diretório.

Para ativar esta visualização atualizada, clique no **experimento o nosso novo** link de experiência a partir do topo da página **Single Sign-On.** Para mais informações, consulte [Tutorial: Configure o único sign-on baseado em SAML para uma aplicação com o Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).

---

## <a name="august-2018"></a>Agosto de 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Alterações aos intervalos dos endereços de IP do Azure Active Directory

**Tipo:** Plano de mudança  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Plataforma

Estamos a introduzir maior intervalos IP para o Azure AD, que significa que, se tiver configurado a intervalos de endereços IP do Azure AD para seus firewalls, routers ou grupos de segurança de rede, terá de atualizá-los. Estamos a disponibilizar esta atualização para que não tenha de alterar a firewall, o roteador ou a configurações de intervalo de IP de grupos de segurança de rede novamente quando o Azure AD adiciona novos pontos de extremidade. 

Tráfego de rede está a mudar para estes intervalos de novo nos próximos dois meses. Para continuar com o serviço ininterrupto, tem de adicionar estes valores atualizados para os endereços IP antes de 10 de Setembro de 2018:

- 20.190.128.0/18 

- 40.126.0.0/18 

É altamente recomendável não remover os intervalos de endereço IP antigos até que todo seu tráfego de rede foi movido para os intervalos de novo. Para obter atualizações sobre o movimento e para saber quando pode remover as gamas antigas, consulte os [intervalos de endereços Office 365 URLs e IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Aviso de alteração: Os códigos de autorização já não estarão disponíveis para reutilização 

**Tipo:** Plano de mudança  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador

A partir de 15 de Novembro de 2018, do Azure AD irá parar abertos ao recebimento de códigos de autenticação utilizados anteriormente para aplicações. Esta alteração de segurança ajuda a trazer do Azure AD em conformidade com a especificação de OAuth e será imposta em pontos finais de ambos o v1 e v2.

Se a sua aplicação reutiliza os códigos de autorização para obter os tokens de vários recursos, recomendamos que utilize o código para obter um token de atualização e, em seguida, utilizar esse token de atualização para adquirir tokens adicionais para outros recursos. Códigos de autorização só podem ser utilizados uma vez, mas os tokens de atualização podem ser utilizadas várias vezes em vários recursos. Uma aplicação que tente reutilizar um código de autenticação durante o fluxo de código do OAuth obterá um erro de invalid_grant.

Para esta e outras alterações relacionadas com protocolos, consulte [a lista completa do que é novo para autenticação.](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes)
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Gestão de informações de segurança convergidas para palavras-passe self-service (SSPR) e para a Autenticação Multifator (MFA)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** SSPR  
**Capacidade do produto:** Autenticação do Utilizador
 
Esse novo recurso ajuda as pessoas a gerir as suas informações de segurança (como número de telefone, aplicação móvel etc.) para SSPR e o MFA numa única localização e experiência; em comparação com a anteriormente, onde ele foi feito em duas localizações diferentes.

Esta experiência convergida também funciona para as pessoas que utilizam o SSPR ou MFA. Além disso, se sua organização não impor o registo MFA ou SSPR, as pessoas ainda podem registar quaisquer métodos de informações de segurança MFA ou SSPR permitidos pela sua organização a partir do portal as minhas aplicações.

Esta é uma participação ativa pré-visualização pública. Os administradores podem ativar a nova experiência (se desejado) para um grupo selecionado ou para todos os utilizadores num inquilino. Para mais informações sobre a experiência convergente, consulte o [blog de experiência convergente](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nova definição de cookies de apenas HTTP nas aplicações proxy da aplicação Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Procuração de aplicativos  
**Capacidade do produto:** Controlo de Acesso

Há uma nova configuração **chamada, HTTP-Only Cookies** nas suas aplicações Proxy application. Esta definição ajuda a fornecer segurança adicional, incluindo o sinalizador de HTTPOnly no cabeçalho de resposta HTTP para ambos os cookies de sessão de acesso e de Proxy de aplicações, acesso a parar o cookie de um script do lado do cliente e ainda mais a impedir ações como copiar ou modificando o cookie. Embora este sinalizador não tiver sido usado anteriormente, os cookies sempre foram encriptadas e transmitidos através de uma conexão SSL para ajudar a proteger contra modificações impróprias.

Esta definição não é compatível com aplicações que utilizam controles ActiveX, como o ambiente de trabalho remoto. Se estiver nessa situação, recomendamos que Desative esta definição.

Para obter mais informações sobre a definição de Cookies http-only, consulte [publicar aplicações utilizando o Proxy de Aplicação AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>O Privileged Identity Management (PIM) para os recursos do Azure suporta tipos de recurso do Grupo de Gestão

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão privilegiada da identidade  
**Capacidade do produto:** Gestão privilegiada da identidade
 
Definições de ativação e a atribuição de Just-In-Time agora podem ser aplicadas a tipos de recursos do grupo de gestão, tal como já não fizesse para subscrições, grupos de recursos e recursos (como VMs, os serviços de aplicações e muito mais). Além disso, qualquer pessoa com uma função que fornece acesso de administrador para um grupo de gestão pode detetar e gerir esse recurso no PIM.

Para mais informações sobre os recursos pim e Azure, consulte [Discover e geregere os recursos azure utilizando a Gestão](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources) de Identidade Privilegiada
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>O acesso de aplicação (pré-visualização) fornece um acesso mais rápido ao portal do Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão privilegiada da identidade  
**Capacidade do produto:** Gestão privilegiada da identidade
 
Hoje em dia, ao ativar uma função usando o PIM, pode demorar mais de 10 minutos para as permissões para entrar em vigor. Se optar por utilizar o acesso de aplicação, o que está atualmente em pré-visualização pública, os administradores podem aceder portal do Azure AD assim que a solicitação de ativação é concluída.

Atualmente, o acesso de aplicação apenas suporta a experiência do portal do Azure AD e os recursos do Azure. Para mais informações sobre pim e acesso a aplicação, consulte O que é a [Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações do Azure AD - Agosto de 2018

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Agosto de 2018, adicionámos que suportam a 16 novos aplicativos com a Federação para a Galeria de aplicações:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), Sauce Labs - Mobile and Web Testing , [Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [Way We Do,](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial) [Spotinst,](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial) [ProMaster (by Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me,](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial) [Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - Relatórios](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial)de [Despesas,](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial) [Comm100 Live Chat,](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial) [SafeConnect,](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial) [ZenQMS,](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial) [eLuminate,](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial) [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>O suporte do Tableau Nativo está agora disponível no proxy de aplicações do Azure AD

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Procuração de aplicativos  
**Capacidade do produto:** Controlo de Acesso

Com a nossa atualização a partir do OpenID Connect para o protocolo de concessão de código do OAuth 2.0 para o nosso protocolo de pré-autenticação, já não tem de efetuar qualquer configuração adicional para utilizar o Tableau com o Proxy de aplicações. Esta alteração de protocolo também ajuda a Proxy de aplicações mais suporte a aplicativos mais modernos utilizando apenas a redirecionamentos HTTP, que normalmente são suportados nas marcas HTML e JavaScript.

Para obter mais informações sobre o nosso apoio nativo ao Tableau, consulte [o Azure AD Application Proxy agora com suporte nativo do Tableau.](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support)

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Novo suporte para adicionar o Google como fornecedor de identidade para utilizadores convidados B2B no Azure Active Directory (pré-visualização)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C

Ao configurar a Federação com o Google na sua organização, pode permitir início de sessão de utilizadores convidados Gmail às suas aplicações partilhadas e os recursos através das respetivas contas Google existente, sem ter de criar uma Account Microsoft pessoais (MSAs) ou uma conta do Azure AD.

Esta é uma participação ativa pré-visualização pública. Para obter mais informações sobre a federação da Google, consulte o Add Google como um fornecedor de [identidade para utilizadores convidados B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Julho de 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Melhorias às notificações de e-mail do Azure Active Directory

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Gestão do ciclo de vida de identidade
 
E-mails de Active Directory (Azure AD) do Azure apresentam agora um design atualizado, bem como as alterações para o endereço de e-mail do remetente e o nome de exibição do remetente, quando enviados a partir os seguintes serviços:
 
- Revisões de acesso do Azure AD
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Notificações de certificado do prestes a expirar de aplicações da empresa
- Notificações serviço de aprovisionamento da aplicação do Enterprise
 
As notificações de e-mail serão enviadas o seguinte endereço de e-mail e nome a apresentar:

- Endereço de e-mail: azure-noreply@microsoft.com
- Nome a apresentar: Microsoft Azure
 
Para um exemplo de alguns dos novos designs de e-mail e mais informações, consulte notificações de [e-mail no Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Os Registos de Atividade do Azure AD estão agora disponíveis através do Azure Monitor

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Monitorização e Reportagem

Os registos de atividades do Azure AD estão agora disponíveis em pré-visualização pública para o Azure Monitor (serviço de monitorização ao nível da plataforma do Azure). Monitor do Azure oferece-lhe retenção de longa duração e a integração totalmente integrada, além destas melhorias:

- Retenção de longo prazo ao encaminhar os ficheiros de registo para a sua própria conta de armazenamento do Azure.

- Integração total dos SIEM, sem que seja necessário escrever ou manter scripts personalizados.

- Integração total com suas próprias soluções personalizadas, ferramentas de análise ou soluções de gestão de incidentes.

Para obter mais informações sobre estas novas capacidades, consulte os registos de atividade do nosso blog [Azure AD em diagnósticos do Monitor Azure](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) e a nossa documentação, registos de atividades do [Azure Ative Directory no Azure Monitor (pré-visualização)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informação de Acesso Condicional adicionada ao relatório de inscrições da AD Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Segurança e Proteção de Identidade
 
Esta atualização permite-lhe ver que políticas são avaliadas quando um utilizador inicia sessão, juntamente com o resultado de política. Além disso, o relatório inclui agora o tipo de aplicação de cliente utilizado pelo utilizador, para que possa identificar o tráfego de protocolo de legado. Agora também podem ser pesquisadas entradas de relatório para um ID de correlação, que pode ser encontrado na mensagem de erro destinada ao utilizador e pode ser utilizado para identificar e resolver problemas relacionados com o pedido de início de sessão correspondente.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Ver autenticações legadas através dos registos de atividade de Inícios de sessão

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Monitorização e Reportagem
 
Com a introdução do campo de **Aplicação de Clientes** nos registos de atividade sessão, os clientes podem agora ver utilizadores que estão a utilizar autenticações antigas. Os clientes poderão aceder a esta informação utilizando a API do Microsoft Graph API ou através dos registos de atividade sessão de entrada no portal Azure AD, onde poderá utilizar o controlo da **App do Cliente** para filtrar as autenticações antigas. Consulte a documentação para obter mais detalhes.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações do Azure AD - Julho de 2018

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Em Julho de 2018, adicionámos que suportam a 16 novos aplicativos com a Federação para a Galeria de aplicações:

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [Certain Admin SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial)PSUC Staging, [iPass SmartConnect,](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial) [Screencast-O-Matic,](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial)PowerSchool Unified Classroom, [Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar Remote Support,](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial) [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision,](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial) [Insight4GRC,](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial) [SecureW2 JoinNow Connector,](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial) [Kanbanize,](../saas-apps/kanbanize-tutorial.md) [SmartLPA,](../saas-apps/smartlpa-tutorial.md) [Skills Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Novas integrações de aplicações SaaS para aprovisionamento de utilizadores - Julho de 2018

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** 3ª Integração do Partido
 
O Azure AD permite-lhe automatizar a criação, a manutenção e a remoção de identidades de utilizador em aplicações de SaaS, como o Dropbox, Salesforce, ServiceNow e muito mais. Julho de 2018, adicionámos suporte para as seguintes aplicações na Galeria de aplicações do Azure AD de provisionamento de usuários:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bónus](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Para obter uma lista de todas as aplicações que suportam o fornecimento de utilizadores na galeria Azure AD, consulte a integração da [aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health para Sincronização - Uma forma mais fácil de corrigir os erros de sincronização de atributos duplicados e órfãos

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Ligação AD  
**Capacidade do produto:** Monitorização e Reportagem
 
O Azure AD Connect Health apresenta a solução de gestão personalizada para o ajudar a realçar e corrigir erros de sincronização. Esta funcionalidade soluciona problemas em erros de sincronização de atributo duplicado e correções de objetos que ficarão órfãos do Azure AD. Este diagnóstico tem as seguintes vantagens:

- Restringe os erros de sincronização de atributo duplicado, fornecer correções específicas

- Aplica-se uma correção dedicam a cenários do Azure AD, resolução de erros numa única etapa

- Nenhuma atualização ou a configuração é necessária para ativar e utilizar esta funcionalidade

Para mais informações, consulte Diagnosticar e remediar erros de sincronização de [atributos duplicados](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Atualizações visuais às experiências de início de sessão do Azure AD e MSA

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Azure AD  
**Capacidade do produto:** Autenticação do Utilizador

Atualizámos a interface do Usuário para a experiência da Microsoft online services início de sessão, por exemplo, para o Office 365 e Azure. Esta alteração torna os ecrãs mais simples e menos confuso. Para obter mais informações sobre esta mudança, consulte as próximas melhorias no blog de experiênciade entrada de [Anúncios Azure.](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/)

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nova versão do Azure AD Connect - Julho de 2018

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida de identidade

A versão mais recente do Azure AD Connect inclui: 

- Correções de erros e atualizações da capacidade de suporte 

- Disponibilidade geral da integração federar de Ping

- Atualizações para o cliente mais recente do SQL 2012 

Para mais informações sobre esta atualização, consulte o Histórico de lançamento da [versão Azure AD Connect: Versão](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Atualizações aos termos de utilização do UI de utilizador final

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Termos de utilização  
**Capacidade do produto:** Governação

Estamos a atualizar a cadeia de caracteres de aceitação na IU do utilizador final de termos de utilização.

**Texto atual.** Para poder aceder aos recursos de [tenantName], tem de aceitar os termos de utilização.<br>**Novo texto.** Para poder aceder ao recurso de [tenantName], deve ler os termos de utilização.

**Texto atual:** Optar por aceitar significa que concorda com todos os termos de utilização acima referidos.<br>**Novo texto:** Clique em Aceitar para confirmar que leu e compreendeu os termos de utilização.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>A autenticação pass-through suporta agora aplicações e protocolos de rede legados

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
Agora a autenticação pass-through suporta protocolos legados e aplicações. As seguintes limitações agora são totalmente suportadas:

- Utilizador inícios de sessão para o Office cliente aplicativos herdados, Office 2010 e o Office 2013, sem a necessidade de autenticação moderna.

- Acesso a partilha de calendário e informações de disponibilidade no Exchange ambientes híbridos no Office 2010 apenas.

- Utilizador inícios de sessão ao Skype para aplicativos de cliente de negócio sem a necessidade de autenticação moderna.

- Utilizador inícios de sessão para o PowerShell versão 1.0.

- A Apple registo de aparelho (DEP) da Apple, utilizando o Assistente de configuração de iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Gestão de informações de segurança convergidas para reposição de palavra-passe self-service e Autenticação Multifator

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** SSPR  
**Capacidade do produto:** Autenticação do Utilizador

Esta nova funcionalidade permite aos utilizadores gerir as suas informações de segurança (por exemplo, número de telefone, endereço de e-mail, aplicações móveis e assim por diante) para a reposição de palavra-passe self-service (SSPR) e o multi-factor Authentication (MFA) numa única experiência. Os utilizadores já não terá de registar as informações de segurança mesmo para SSPR e o MFA no duas experiências diferentes. Esta nova experiência também se aplica aos utilizadores que têm o SSPR ou MFA.

Se uma organização não estiver a fazer cumprir o registo de MFA ou SSPR, os utilizadores podem registar as suas informações de segurança através do portal **My Apps.** A partir daí, os utilizadores podem registar quaisquer métodos ativados para MFA ou SSPR. 

Esta é uma participação ativa pré-visualização pública. Os administradores podem ativar a nova experiência (se desejado) para um grupo selecionado de utilizadores ou de todos os utilizadores num inquilino.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Utilize a aplicação Microsoft Authenticator para verificar a sua identidade ao repor a palavra-passe

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** SSPR  
**Capacidade do produto:** Autenticação do Utilizador

Esta funcionalidade permite que os não-administradores verificar a respetiva identidade ao repor uma palavra-passe através de uma notificação ou o código a partir do Microsoft Authenticator (ou qualquer outra aplicação de autenticador). Depois de ativassem os administradores de método de repor esta palavra-passe self-service, os utilizadores que registou uma aplicação móvel por meio de aka.ms/mfasetup ou aka.ms/setupsecurityinfo podem utilizar a aplicação móvel como método de verificação ao repor a palavra-passe.

Notificação de aplicação móvel apenas pode ser ligada como parte de uma política que requer dois métodos para repor a palavra-passe.

---

## <a name="june-2018"></a>junho de 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Alterar o aviso: a correção de segurança para o fluxo de autorização delegada para aplicações que utilizam a API de registos de atividade do Azure AD

**Tipo:** Plano de mudança  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Monitorização e Reportagem

Devido à nossa aplicação de segurança mais forte, tivemos que fazer uma alteração nas permissões para apps que usam um fluxo de autorização delegado para aceder a APIs de Registos de Atividade saqueada de [AD Azure](https://aka.ms/aadreportsapi). Esta alteração ocorrerá até 26 de junho de **2018.**

Se alguma das suas aplicações utilizar APIs de Log de Atividade aD Azure, siga estes passos para garantir que a aplicação não se rompa após a mudança acontecer.

**Para atualizar as permissões da sua aplicação**

1. Inscreva-se no portal Azure, selecione **Azure Ative Directory**, e depois selecione Registos de **Aplicações**.
2. Selecione a sua aplicação que utiliza o API de Registos de Atividade do Azure AD, selecione **Definições,** selecione **permissões necessárias**e, em seguida, selecione a API **de Diretório Ativo do Windows Azure.**
3. Na área de **permissões delegadas** da lâmina de **acesso Ativa,** selecione a caixa ao lado dos dados do **diretório e,** em seguida, selecione **Guardar**.
4. Selecione **permissões de concessão**e, em seguida, selecione **Sim**.
    
    >[!Note]
    >Tem de ser um Administrador Global para conceder permissões à aplicação.

Para mais informações, consulte a área de [permissões de Concessão](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) dos Pré-requisitos para aceder ao artigo da API da API.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Configurar as definições de TLS para ligar aos serviços do Azure AD para conformidade PCI DSS

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** N/A  
**Capacidade do produto:** Plataforma

Transport Layer Security (TLS) é um protocolo que fornece privacidade e integridade dos dados entre duas aplicações de comunicação e é o protocolo de segurança implementadas mais amplamente usado hoje em dia.

O [CpI Security Standards Council](https://www.pcisecuritystandards.org/) determinou que as primeiras versões de TLS e Secure Sockets Layer (SSL) devem ser desativadas a favor de permitir novos e mais seguros protocolos de aplicações, com conformidade a partir de 30 de junho de **2018.** Esta alteração significa que, se ligar aos serviços do Azure AD e exigir conformidade do PCI DSS, tem de desativar TLS 1.0. Existem várias versões do TLS, mas o TLS 1.2 é a versão mais recente disponível para os serviços do Active Directory do Azure. É altamente recomendável mudar diretamente para o TLS 1.2 para combinações de cliente/servidor e o browser/servidor.

Browsers desatualizados podem não suportar versões mais recentes do TLS, por exemplo, o TLS 1.2. Para ver quais as versões do TLS suportadas pelo seu navegador, vá ao site da [Qualys SSL Labs](https://www.ssllabs.com/) e clique em **Testar o seu navegador**. Recomendamos que Atualize para a versão mais recente do seu navegador da web e, de preferência ative apenas TLS 1.2.

**Para ativar o TLS 1.2, por browser**

- **Microsoft Edge e Internet Explorer (ambos são definidos usando o Internet Explorer)**

    1. Open Internet Explorer, selecione **Tools** > **Internet Options** > **Advanced**.
    2. Na área **de Segurança,** selecione **utilize TLS 1.2**, e, em seguida, selecione **OK**.
    3. Feche todas as janelas do browser e reinicie o Internet Explorer. 

- **Google Chrome**

    1. Abra o Google Chrome, *escreva chrome://settings/* na barra de endereços e prima **Enter**.
    2. Expandir as opções **Avançadas,** ir para a área **do Sistema** e selecionar configurações de **procuração Aberta**.
    3. Na caixa **Internet Properties,** selecione o separador **Advanced,** vá para a área de **Segurança,** selecione **utilizar TLS 1.2**, e, em seguida, selecione **OK**.
    4. Feche todas as janelas do browser e reinicie o Google Chrome.

- **Mozilla Firefox**

    1. Abra o Firefox, escreva *sobre:config* na barra de endereços e, em seguida, prima **Enter**.
    2. Procure o termo, *TLS,* e, em seguida, selecione a entrada **security.tls.version.max.**
    3. Detete o valor para **3** para forçar o navegador a usar até à versão TLS 1.2 e, em seguida, selecione **OK**.

        >[!NOTE]
        >A versão 60.0 do Firefox suporta tLS 1.3, para que também possa definir o valor security.tls.version.max para **4**.

    4. Feche todas as janelas do browser e reinicie o Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Novas aplicações federadas disponíveis na Galeria de aplicações do Azure AD - Junho de 2018

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Junho de 2018, adicionámos que 15 novos aplicativos com a Federação de suporte para a Galeria de aplicações:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Música de assentamento,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial) [SAML 1.1 Token ativado LOB App](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial) [Endpoint Backup,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial) [Skyhigh Networks,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial)Smartway2, [TonicDM,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial) [Moconavi,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial) [Zoho One,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial) [SharePoint on-premis,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial) [ForeSee CX Suite,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial) [Vidyard,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial) [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>A proteção de palavra-passe do Azure AD está disponível em pré-visualização pública

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Autenticação do Utilizador

Utilize a proteção de palavra-passe do Azure AD para ajudar a eliminar facilmente adivinhado palavras-passe do seu ambiente. Eliminar estas palavras-passe ajuda a reduzir o risco de comprometimento de um tipo de spray de palavra-passe de ataque.

Especificamente, a proteção de palavra-passe do Azure AD ajuda-o:

- Proteger contas da sua organização do Azure AD e Windows Server Active Directory (AD). 
- Deixa os usuários usem senhas numa lista de mais de 500 das palavras-passe utilizadas com mais frequência e variações de substituição de caracteres de mais de 1 milhão dessas palavras-passe.
- Administrar a proteção de palavra-passe do Azure AD a partir de uma única localização no portal do Azure AD, para ambas do Azure AD e AD do Windows Server no local.

Para obter mais informações sobre a Proteção de Passwords AD Azure, consulte [Elimine as palavras-passe erradas na sua organização](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Novo modelo de política de acesso condicional "todos os hóspedes" criado durante os termos da criação de uso

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Termos de utilização  
**Capacidade do produto:** Governação

Durante a criação dos seus termos de utilização, é também criado um novo modelo de política de Acesso Condicional para "todos os hóspedes" e "todas as aplicações". Este novo modelo de política aplica-se os termos de utilização recém-criado, simplificando a criação e o processo de imposição para convidados.

Para mais informações, consulte a [funcionalidade De utilização do Diretório Ativo azure](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Novo modelo de política de acesso condicional "personalizado" criado durante os termos da criação de uso

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Termos de utilização  
**Capacidade do produto:** Governação

Durante a criação dos seus termos de utilização, é também criado um novo modelo de política de acesso condicional "personalizado". Este novo modelo de política permite criar a ToU e depois ir imediatamente para a lâmina de criação da política de Acesso Condicional, sem precisar de navegar manualmente pelo portal.

Para mais informações, consulte a [funcionalidade De utilização do Diretório Ativo azure](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Novo e abrangente orientações sobre como implementar o Azure multi-factor Authentication

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Segurança e Proteção de Identidade
 
Lançámos o novo orientações passo a passo sobre como implementar o Azure multi-factor Authentication (MFA) na sua organização.

Para ver o guia de implantação do MFA, vá ao repo dos Guias de [Implementação de Identidade](https://aka.ms/DeploymentPlans) no GitHub. Para fornecer feedback sobre os guias de implementação, utilize o formulário de feedback do plano de [implementação](https://aka.ms/deploymentplanfeedback). Se tiver alguma dúvida sobre os guias de implementação, contacte-nos no [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Gestão de aplicações, funções estão em pré-visualização pública de delegado do Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** Controlo de Acesso

Os administradores agora podem delegar tarefas de gestão de aplicações sem atribuir a função de Administrador Global. As novas funções e funcionalidades são:

- **Novas funções padrão de administração da AD Azure:**

    - **Administrador de candidaturas.** Concede a capacidade de gerir todos os aspetos de todas as aplicações, incluindo o registro, as definições de SSO, atribuições de aplicações e licenciamento, definições de proxy de aplicações e consentimento (exceto para recursos do Azure AD).

    - **Administrador de aplicação em nuvem.** Concede todas as capacidades de administrador da aplicação, com exceção do proxy de aplicação pois não fornece acesso no local.

    - **Desenvolvedor de aplicações.** Concede a possibilidade de criar registos de aplicações, mesmo que a **opção de utilizador para registar aplicações** seja desligada.

- **Propriedade (configuração de registo por app e app por empresa, semelhante ao processo de propriedade do grupo:**
 
    - **Proprietário do registo de aplicativos.** Concede a capacidade de gerenciar todos os aspectos do registo de aplicações pertencentes à empresa, incluindo o manifesto da aplicação e adicionar proprietários adicionais.

    - **Dono de aplicações empresariais.** Concede a capacidade de gerir muitos aspectos de aplicações do enterprise pertencentes à empresa, incluindo as definições de SSO, atribuições de aplicações e consentimento (exceto para recursos do Azure AD).

Para mais informações sobre a pré-visualização pública, consulte as funções de [gestão de aplicações delegadas da AD Azure estão em pré-visualização pública!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) Blog. Para obter mais informações sobre funções e permissões, consulte [as funções de administrador de atribuição no Diretório Ativo azure](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Maio de 2018

### <a name="expressroute-support-changes"></a>Alterações de suporte do ExpressRoute

**Tipo:** Plano de mudança  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Plataforma  

Software como um oferta de serviço, como o Azure Active Directory (Azure AD) foram concebidas para funcionar melhor indo diretamente através da Internet, sem exigir o ExpressRoute ou quaisquer outros privadas túneis VPN. Por isso, no dia 1 de agosto de **2018,** deixaremos de apoiar a ExpressRoute para os serviços da AD Azure utilizando o público azure e as comunidades Azure na Microsoft. Quaisquer serviços afetados por esta alteração poderão notar Azure tráfego AD gradualmente mudando de ExpressRoute à Internet.

Enquanto estamos a alterar a nosso suporte, podemos também sabe disso são ainda situações em que poderá ter de utilizar um conjunto dedicado de circuitos para o tráfego de autenticação. Por este motivo, do Azure AD irá continuar suportar restrições de intervalo IP por inquilino utilizar o ExpressRoute e de serviços já no peering da Microsoft com a Comunidade de "Outros serviços Online do Office 365". Se os serviços são afetados, mas exigir o ExpressRoute, terá de efetuar o seguinte:

- **Se estás a olhar para o público do Azure.** Mude-se para a Microsoft espreitando e inscreva-se na comunidade **de outros serviços online do Office 365 (12076:5100).** Para obter mais informações sobre como passar do público do Azure para o peering da Microsoft, veja o Movimento um público a espreitar para o artigo de peering da [Microsoft.](https://docs.microsoft.com/azure/expressroute/how-to-move-peering)

- **Se estiveres na Microsoft a espreitar.** Inscreva-se na comunidade **other Office 365 Online (12076:5100).** Para obter mais informações sobre os requisitos de encaminhamento, consulte a secção de [Apoio às comunidades](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) de BGP do artigo de requisitos de encaminhamento expressRoute.

Se tiver de continuar a utilizar circuitos dedicados, terá de falar com a sua equipa da Microsoft Account sobre como obter autorização para utilizar o **serviço Other Office 365 Online (12076:5100).** A MS geridos pelo Office banca examinadora irá verificar se tem os circuitos e certifique-se de que compreende as implicações técnicas de mantê-los. As assinaturas não autorizadas tentar criar filtros de rota para o Office 365 irão receber uma mensagem de erro. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph APIs para cenários administrativos para termos de utilização

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Termos de utilização  
**Capacidade do produto:** Experiência de Desenvolvimento
 
Adicionámos APIs do Microsoft Graph para a operação de administração dos termos de utilização da AD Azure. É capaz de criar, atualizar, eliminar os termos do objeto de utilização.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Adicionar ponto final de multi-inquilino do Azure AD como fornecedor de identidade no Azure AD B2C

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C
 
Utilizar políticas personalizadas, agora pode adicionar o ponto de extremidade comum do Azure AD como fornecedor de identidade no Azure AD B2C. Isto permite-lhe ter um único ponto de entrada para todos os utilizadores do Azure AD que está a iniciar sessão nas suas aplicações. Para mais informações, consulte [o Azure Ative Directory B2C: Permitir que os utilizadores inscrevam um fornecedor de identidade Azure AD multi-inquilino utilizando políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Utilizar URLs interno para aceder às aplicações a partir de qualquer lugar com a nossa extensão My Apps início de sessão e o Proxy de aplicações do Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Minhas Apps  
**Capacidade do produto:** SSO
 
Os utilizadores podem agora aceder às aplicações através de URLs internos, mesmo quando fora da rede empresarial através da utilização segura de aplicações meu início de sessão extensão para o Azure AD. Isso funcionará com qualquer aplicação que publicou com o Proxy de aplicações do Azure AD, em qualquer browser que também tenha a extensão de browser do painel de acesso instalada. A funcionalidade de redirecionamento de URL é ativada automaticamente assim que um utilizador iniciar sessão a extensão. A extensão está disponível para download no [Microsoft Edge,](https://go.microsoft.com/fwlink/?linkid=845176) [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)e [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>O Azure Active Directory - dados na Europa para os clientes da Europa

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** GoLocal

Os clientes na Europa necessitam de seus dados para se manter na Europa e não são replicados fora europeus centros de dados para a privacidade de reunião e da legislação europeia. Este [artigo](https://go.microsoft.com/fwlink/?linkid=872328) fornece os detalhes específicos sobre quais as informações de identidade que serão armazenadas na Europa e também fornece detalhes sobre informações que serão armazenadas fora dos centros de dados europeus. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Novo utilizador aprovisionamento integrações de aplicação SaaS - Maio de 2018

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** 3ª Integração do Partido
 
O Azure AD permite-lhe automatizar a criação, a manutenção e a remoção de identidades de utilizador em aplicações de SaaS, como o Dropbox, Salesforce, ServiceNow e muito mais. Maio de 2018, adicionámos suporte para as seguintes aplicações na Galeria de aplicações do Azure AD de provisionamento de usuários:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Cornerstone OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Para obter uma lista de todas as aplicações que suportam o fornecimento de utilizadores na galeria Azure AD, consulte [https://aka.ms/appstutorial](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Revisões de acesso do Azure AD de grupos e acesso de aplicação fornece agora revisões periódicas

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Avaliações de Acesso  
**Capacidade do produto:** Governação
 
Revisão de acesso de grupos e aplicações está agora geralmente disponível como parte do Azure AD Premium P2.  Os administradores poderão configurar as revisões de acesso de atribuições de aplicações e associações de grupo para repetir automaticamente em intervalos regulares, por exemplo, mensal ou trimestral.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Registos de atividade do AD do Azure (inícios de sessão e auditoria) estão agora disponíveis através do MS Graph

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Monitorização e Reportagem
 
Os registos da Atividade Azure AD, que incluem registos de inscrições e auditoria, já estão disponíveis através da API do Microsoft Graph. Expusemos dois pontos finais através da API do Microsoft Graph para aceder a estes registos. Consulte [os nossos documentos](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) para acesso programático a APIs de reporte de AD Azure para começar. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Experiência de melhorias para o resgate de B2B e deixam uma organização

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C

**Mesmo a tempo, a redenção:** Uma vez que partilhe um recurso com um utilizador convidado usando API B2B – não precisa enviar um e-mail especial de convite. Na maioria dos casos, o utilizador convidado pode aceder ao recurso e será direcionado através da experiência de resgate just-in-time. Não existem mais impacto devido a mensagens de e-mail em falta. Chega de perguntar aos seus utilizadores convidados "Clicou no link de redenção que o sistema lhe enviou?". Isso significa que uma vez SPO utiliza o Gestor de convite – anexos nublados podem ter o mesmo URL canônico para todos os utilizadores – internos e externos – em qualquer Estado de resgate.

**Experiência moderna de redenção:** Chega de página de aterragem de redenção de ecrã dividido. Os utilizadores verão um moderno consentir a experiência com a declaração de privacidade da organização de convite, tal como fazem para aplicações de terceiros.

**Os utilizadores podem sair do org:** Uma vez terminada a relação de um utilizador com uma org, podem auto-servir deixando a organização. Chega de chamar a administração do org convidativo para "ser removido", sem mais angariar bilhetes de apoio.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Novas aplicações federadas disponíveis na Galeria de aplicações do Azure AD - Maio de 2018

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Em Maio de 2018, adicionámos que suportam a essas 18 novas aplicações com a Federação para nossa Galeria de aplicações:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty Govern, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial), OpenReel, Arc [Publishing - SSO,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial) [PlanGrid,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial) [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial) [Flock,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial) [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial) [](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial).

Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Novos guias de implementação passo a passo para o Azure Active Directory

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Diretório
 
Nova orientação passo a passo sobre como implementar o Azure Ative Directory (Azure AD), incluindo o reset de senha de autosserviço (SSPR), um único sign-on (SSO), Acesso Condicional (CA), proxy de aplicações, fornecimento de utilizadores, Serviços da Federação de DirectórioActivo (ADFS) para Autenticação pass-through (PTA) e ADFS para sincronização de hash password (PHS).

Para ver os guias de implantação, vá ao Repo de Guias de [Implementação de Identidade](https://aka.ms/DeploymentPlans) no GitHub. Para fornecer feedback sobre os guias de implementação, utilize o formulário de feedback do plano de [implementação](https://aka.ms/deploymentplanfeedback). Se tiver alguma dúvida sobre os guias de implementação, contacte-nos no [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Aplicações empresariais de pesquisa – carregar mais aplicações

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO
 
Encontrar as suas aplicações / principais de serviço? Adicionámos a capacidade de carregar mais aplicações nas suas aplicações empresariais todas as listas de aplicações. Por predefinição, vamos mostrar 20 aplicações. Agora pode clicar, **carregar mais** para ver aplicações adicionais. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Maio lançamento do AADConnect contém uma pré-visualização pública da integração com o PingFederate, atualizações de segurança importante, muitas correções de erros e ótimo novo novo solucionar problemas de ferramentas. 

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Ligação AD  
**Capacidade do produto:** Gestão do ciclo de vida de identidade
 
Maio lançamento do AADConnect contém uma pré-visualização pública da integração com o PingFederate, atualizações de segurança importante, muitas correções de erros e ótimo novo novo solucionar problemas de ferramentas. Pode encontrar as notas de lançamento [aqui.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190)

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Revisões de acesso do Azure AD: automática

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Avaliações de Acesso  
**Capacidade do produto:** Governação

Revisões de acesso de grupos e aplicações estão agora geralmente disponíveis como parte do Azure AD Premium P2. Um administrador pode configurar para aplicar automaticamente as alterações do revisor a esse grupo ou aplicação que a revisão de acesso for concluída. O administrador também pode especificar o que acontece com acesso contínuo do utilizador se os revisores não responder, remover o acesso, manter o acesso ou efetuar recomendações de sistema. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Tokens de ID já não podem ser devolvidos com o response_mode de consulta para novas aplicações. 

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
As aplicações criadas no dia 25 de abril de 2018 deixarão de poder solicitar uma **id_token** utilizando a **consulta** response_mode.  Isso traz inline do Azure AD com as especificações de OIDC e ajuda a reduzir a superfície de ataque de aplicações.  As aplicações criadas antes do 25 de abril de 2018 não estão impedidas de usar a **consulta** response_mode com um response_type de **id_token**.  O erro devolvido, ao solicitar um id_token à AAD, é **AADSTS70007: "consulta" não é um valor suportado de "response_mode" ao solicitar um símbolo.**

O **fragmento** e **form_post** response_modes continuam a funcionar - ao criar novos objetos de aplicação (por exemplo, para uso de App Proxy), garantir a utilização de uma destas response_modes antes de criarem uma nova aplicação.  

---
 
## <a name="april-2018"></a>Abril de 2018 

### <a name="azure-ad-b2c-access-token-are-ga"></a>O Azure AD B2C Token de acesso estão GA

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C 

Agora pode aceder a APIs da Web protegida pelo Azure AD B2C com tokens de acesso. A funcionalidade está a mudar de pré-visualização pública para GA. Foi melhorada a experiência de interface do Usuário para configurar aplicações do Azure AD B2C e web APIs e outras pequenas melhorias foram feitas.
 
Para mais informações, consulte [Azure AD B2C: Solicitando fichas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens)de acesso .

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testar a configuração de início de sessão única para aplicações baseadas em SAML

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO

Quando configurar as aplicações baseadas em SAML SSO, pode testar a integração na página de configuração. Se ocorrer um erro durante o início de sessão, pode fornecer o erro na experiência de teste e do Azure AD fornece-lhe os passos de resolução para resolver o problema específico.

Para obter mais informações, consulte:

- [Configurar o início de sessão único em aplicações que não fazem parte da galeria de aplicações do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Como depurar o único sign-on baseado em SAML para aplicações no Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Os termos de utilização da Azure AD agora têm por relatório supor utilizador

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Termos de utilização  
**Capacidade do produto:** Conformidade
 
Os administradores podem agora selecionar um determinado termos de utilização e ver todos os utilizadores tenham consentimento ao que termos de utilização e o que data/hora ocorreu.

Para mais informações, consulte a funcionalidade de [utilização da AD Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>O Azure AD Connect Health: IP em risco para a proteção de bloqueio de extranet do AD FS 

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Monitorização e Reportagem

Agora suporta a capacidade de detetar IP, endereços que excedem um limiar de inícios de sessão falhados U/P numa base por hora ou diário do Connect Health. Os recursos fornecidos por esta funcionalidade são:

- Relatório abrangente, que mostra o endereço IP e o número de inícios de sessão falhados gerados numa base por hora/dia com o limiar personalizável.
- Alertas baseados no e-mail que mostra quando um endereço IP específico tiver excedido o limiar de inícios de sessão falhados U/P numa base por hora/dia.
- Uma opção de download para fazer uma análise detalhada dos dados

Para mais informações, consulte [Risky IP Report](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Configuração da aplicação fácil com o ficheiro de metadados ou URL

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO

Na página de aplicações empresariais, os administradores podem carregar um ficheiro de metadados SAML para configurar SAML com base em início de sessão para a aplicação da galeria do AAD e não à galeria.

Além disso, pode utilizar o URL de metadados de Federação de aplicação do Azure AD para configurar o SSO com a aplicação de destino.

Para mais informações, consulte configurar um único sinal para aplicações que não estejam na galeria de aplicações do [Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>O Azure AD termos de utilização agora em disponibilidade geral

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Termos de utilização  
**Capacidade do produto:** Conformidade
 

Os termos de utilização da AD Azure passaram da pré-visualização pública para geralmente disponíveis.

Para mais informações, consulte a funcionalidade de [utilização da AD Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Permitir ou bloquear convites para utilizadores B2B de organizações específicos

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 

Agora pode especificar quais as organizações parceiras que pretende partilhar e colaborar com em colaboração B2B do Azure AD. Para tal, pode optar por criar a lista de específicos de permitir ou negar a domínios. Quando um domínio for bloqueado com estas capacidades, os funcionários não podem mais enviar convites para as pessoas nesse domínio.

Isto ajuda a controlar o acesso aos seus recursos, ao ativar uma experiência positiva para os utilizadores aprovados.

Esta funcionalidade de Colaboração B2B está disponível para todos os clientes do Azure Ative Directory e pode ser utilizada em conjunto com funcionalidades do Azure AD Premium, como acesso condicional e proteção de identidade para um controlo mais granular de quando e como os utilizadores de negócios externos assinam dentro e ter acesso.

Para mais informações, consulte [Permitir ou bloquear convites a utilizadores B2B de organizações específicas](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novas aplicações federadas disponíveis na Galeria de aplicações do Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido

Em Abril de 2018, adicionámos que 13 novos aplicativos com a Federação de suporte para nossa Galeria de aplicações:

Critério HCM, [FiscalNote,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial) [Servidor Secreto (On-Premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), Dynamic [Signal,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial) [mindWireless,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial) [OrgChart Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial) [Elium,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) [Fluxx Labs,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial) [Cisco Cloud,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial)Shelf, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial).

Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Os utilizadores de concessão B2B no Azure AD acedem às suas aplicações no local (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C

Como uma organização que utiliza recursos de colaboração B2B do Azure Active Directory (Azure AD) para convidar utilizadores de organizações parceiras com o Azure AD, pode agora fornecer estes utilizadores B2B acesso a aplicações no local. Estas aplicações no local podem utilizar a autenticação baseada no SAML ou autenticação integrada do Windows (IWA) com a delegação restringida de Kerberos (KCD).

Para mais informações, consulte [os utilizadores do Grant B2B no Azure AD acesso às suas aplicações no local](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Obtenha tutoriais de integração do SSO do Azure Marketplace

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** 3ª Integração do Partido

Se uma aplicação listada no [mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) suporta o saml baseado em um único sign-on, clicar **em Get agora** fornece-lhe o tutorial de integração associado a essa aplicação. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Desempenho mais rápido de aprovisionamento de utilizadores automático do Azure AD para aplicações SaaS

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** 3ª Integração do Partido
 
Anteriormente, os clientes que utilizam os conectores para aplicações SaaS (por exemplo Salesforce, ServiceNow e caixa) de provisionamento de usuários do Azure Active Directory foi possível detetar um desempenho lento se seus inquilinos do Azure AD contidos mais de 100.000 usuários combinados e grupos e eles usavam atribuições de utilizador e grupo para determinar quais os utilizadores que devem ser aprovisionados.

No dia 2 de Abril de 2018, melhorias de desempenho significativos foram implementadas para o serviço de aprovisionamento do Azure AD que reduzem significativamente a quantidade de tempo necessário para efetuar sincronizações iniciais entre o Azure Active Directory e aplicações de SaaS de destino.

Como resultado, muitos clientes que tinha sincronizações iniciais para aplicações que demorou o número de dias ou nunca foi concluída, agora são concluir em questão de minutos ou horas.

Para mais informações, veja [o que acontece durante o provisionamento?](/azure//active-directory/app-provisioning/how-provisioning-works)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Senhas de auto-atendimento, repor a partir do ecrã de bloqueio do Windows 10 para o Azure AD híbrido associado máquinas

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Reset de palavra-passe self service  
**Capacidade do produto:** Autenticação do Utilizador
 
Atualizámos a funcionalidade SSPR do Windows 10 para incluir suporte para máquinas que estão associados ao Azure AD híbrido. Esta funcionalidade está disponível no Windows 10 RS4 permite que os utilizadores reponham as respetivas palavras-passe de ecrã de bloqueio de uma máquina com o Windows 10. Os utilizadores que forem ativados e registados na reposição de palavra-passe self-service podem utilizar esta funcionalidade.

Para mais informações, consulte a [palavra-passe da AD Azure redefinida a partir do ecrã](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows)de login .

---

## <a name="march-2018"></a>Março de 2018
 
### <a name="certificate-expire-notification"></a>Notificação de expiração do certificado

**Tipo:** Fixo  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO
 
O Azure AD envia uma notificação quando um certificado de uma galeria ou externas à Galeria aplicações está prestes a expirar. 

Alguns utilizadores não tiverem recebido notificações para aplicações empresariais, configuradas para baseado em SAML início de sessão único. Este problema foi resolvido. O Azure AD envia a notificação para certificados irá expirar em 7, 30 e 60 dias. É possível ver este evento nos registos de auditoria. 

Para obter mais informações, consulte:

- [Gerir Certificados para inscrição única federada no Diretório Ativo azure](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Relatórios de atividades de auditoria no portal azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Fornecedores de identidade de twitter e do GitHub no Azure AD B2C

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C
 
Agora pode adicionar o Twitter ou o GitHub como fornecedor de identidade no Azure AD B2C. Twitter é mudar de pré-visualização pública para GA. GitHub está a ser lançado em pré-visualização pública.

Para mais informações, consulte o que é a [colaboração Azure AD B2B?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Restringir o acesso ao navegador utilizando o Navegador Gerido Intune com acesso condicional baseado em aplicações Azure AD para iOS e Android

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Segurança e Proteção de Identidade
 
**Agora, em público, antevisão!**

**Intune Managed Browser SSO:** Os seus colaboradores podem utilizar um único login entre clientes nativos (como o Microsoft Outlook) e o Intune Managed Browser para todas as aplicações ligadas ao Azure AD.

Suporte de acesso condicional gerido ao **navegador intune:** Agora pode exigir que os colaboradores utilizem o navegador Intune Managed utilizando políticas de acesso condicional baseadas em aplicações.

Leia mais sobre isso no nosso post de [blog.](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/)

Para obter mais informações, consulte:

- [Acesso Condicional baseado em aplicações de configuração](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Configure as políticas de navegador geridas](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>App Proxy Cmdlets no Módulo PowerShell GA

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Procuração de aplicativos  
**Capacidade do produto:** Controlo de Acesso
 
O suporte para os cmdlets proxy de aplicação está agora no Módulo PowerShell GA! Isto requer que se mantenha atualizado nos módulos PowerShell - se ficar mais de um ano atrasado, alguns cmdlets podem deixar de funcionar. 

Para mais informações, consulte [O AzureAD.](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Clientes nativos do Office 365 são suportados pelo SSO totalmente integrado com um protocolo não interativa

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
Utilizador com clientes nativos do Office 365 (versão 16.0.8730.xxxx e acima) obter uma silenciosa experiência de logon usando o SSO totalmente integrado. Este suporte é fornecido pela adição um protocolo de não-interativa (WS-Trust) para o Azure AD.

Para mais informações, veja [como funciona o login num cliente nativo com SSO SSO sem emenda?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Os utilizadores obtêm uma silenciosa experiência de logon, com o SSO totalmente integrado, se uma aplicação envia pedidos de início de sessão para pontos finais de inquilino do Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
Os utilizadores obtêm uma experiência de inscrição silenciosa, com Seamless SSO, se uma aplicação (por exemplo, `https://contoso.sharepoint.com`) enviar pedidos de entrada para os pontos finais do inquilino da Azure AD - isto é, `https://login.microsoftonline.com/contoso.com/<..>` ou `https://login.microsoftonline.com/<tenant_ID>/<..>` - em vez do ponto final comum da Azure AD (`https://login.microsoftonline.com/common/<...>`).

Para mais informações, consulte [Azure Ative Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Tem de adicionar apenas um URL do Azure AD, em vez de dois URLs como anteriormente, para as definições de zona de Intranet dos utilizadores para implementar o SSO totalmente integrado

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
Para lançar SSO SSO sem emenda aos seus utilizadores, é necessário adicionar apenas um URL Azure AD às definições da zona Intranet dos utilizadores, utilizando a política de grupo em Diretório Ativo: `https://autologon.microsoftazuread-sso.com`. Anteriormente, os clientes eram necessário adicionar dois URLs.

Para mais informações, consulte [Azure Ative Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novas aplicações federadas disponíveis na Galeria de aplicações do Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido

Março de 2018, adicionámos que 15 novos aplicativos com a Federação de suporte para nossa Galeria de aplicações:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial)Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant by FirstAgenda, [YardiOne,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial)Vtiger CRM, inwink, [Amplitude,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial) [Spacio,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial) [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial) [Mercell,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial) [Trisotech Digital Enterprise Server,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial) [Qumu Cloud.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial)
 
Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial).

Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>PIM para recursos do Azure está disponível em geral

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão privilegiada da identidade  
**Capacidade do produto:** Gestão privilegiada da identidade
 
Se estiver a utilizar o Azure AD Privileged Identity Management para funções de diretório, agora, pode utilizar o acesso de limite de tempo e recursos de atribuição do PIM para funções de recursos do Azure, como as subscrições, grupos de recursos, máquinas virtuais e qualquer outro recurso suportado pelo Azure Resource Manager. Impor o multi-factor Authentication durante a ativação de funções Just-In-Time e agendar ativações em coordenação com o windows de alteração aprovados. Além disso, esta versão adiciona melhorias não está disponíveis durante a pré-visualização pública, incluindo uma interface do Usuário atualizada, fluxos de trabalho de aprovação e a capacidade de estender funções irá expirar em breve e renovar funções expiradas.

Para mais informações, consulte [pim para recursos Azure (Pré-visualização)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Adicionar afirmações opcionais aos tokens das aplicações (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
A aplicação do Azure AD pode agora afirmações de pedidos personalizados ou opcionais em JWTs ou SAML tokens.  Estes são declarações sobre o utilizador ou o inquilino que não estão incluídas por predefinição no token, devido a restrições de tamanho ou a aplicabilidade.  Está atualmente em pré-visualização pública para aplicações do Azure AD nos pontos finais v1.0 e v2.0.  Consulte a documentação para obter informações sobre quais as afirmações podem ser adicionados e como editar o manifesto da aplicação para solicitá-los.  

Para mais informações, consulte [As reclamações opcionais em Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>O Azure AD suporta PKCE para os fluxos do OAuth mais seguros

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
Documentos de AD do Azure foram atualizados observar o suporte para PKCE, que permite a comunicação mais segura durante o fluxo de concessão do código de autorização do OAuth 2.0.  Os pontos finais v1.0 e v2.0 são suportadas code_challenges S256 e texto sem formatação. 

Para mais informações, consulte [Solicite um código](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code)de autorização . 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Suporte para o aprovisionamento de todos os valores de atributo de utilizador disponíveis na API de Get_Workers Workday

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** 3ª Integração do Partido
 
A pré-visualização pública de entrada aprovisionamento a partir do Workday para o Active Directory e o Azure AD agora suporta a capacidade de extrair e o aprovisionamento de todos os valores de atributos disponíveis na API de Get_Workers do Workday. Esta ação adiciona suporta para centenas de standard adicional e atributos personalizados além dos fornecidos com a versão inicial do dia de trabalho de entrada conector de aprovisionamento.

Para mais informações, consulte: [Personalização da lista de atributos do utilizador do Dia de Trabalho](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Alterar a associação ao grupo de dinâmico para estático e vice versa

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração
 
É possível alterar a forma como a associação é gerida num grupo. Isto é útil quando pretender manter o mesmo nome do grupo e o ID no sistema, para que todas as referências existentes para o grupo ainda são válidas; criar um novo grupo exigiria a atualizar essas referências.
Atualizámos o Centro de administração do Azure AD para suportar esta funcionalidade. Agora, os clientes podem converter grupos existentes de associação de grupo dinâmica atribuída de associação e vice-versa. Os cmdlets do PowerShell existentes também ainda estão disponíveis.

Para mais informações, consulte regras de [adesão dinâmicas para grupos no Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Comportamento de fim de sessão melhorado com o SSO totalmente integrado

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
Anteriormente, mesmo que os utilizadores com sessão iniciada explicitamente fora de uma aplicação protegida pelo Azure AD, eles seriam iniciar sessão automaticamente na através do SSO totalmente integrado se estiver a tentar aceder uma aplicação do Azure AD novamente dentro de sua rede empresarial a partir dos seus dispositivos associados a um domínio. Com esta alteração, a fim de sessão é suportada.  Isto permite aos utilizadores escolher o Azure idêntica ou diferente conta de AD para iniciar sessão novamente com, em vez de automaticamente a ser iniciado sessão com o SSO totalmente integrado.

Para mais informações, consulte [Azure Ative Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Versão do conector de Proxy de aplicação 1.5.402.0 lançado

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Procuração de aplicativos  
**Capacidade do produto:** Segurança e Proteção de Identidade
 
Esta versão do conector será gradualmente implementada por meio de Novembro. Esta nova versão do conector inclui as seguintes alterações:

- O conector agora define cookies de nível de domínio em vez disso, o nível de subdomínio. Isso garante uma experiência SSO mais suave e evita a pedidos de autenticação redundante.
- Suporte para solicitações de codificação em partes
- Monitorização de estado de funcionamento do conector melhorado 
- Várias correções de erros e melhorias de estabilidade

Para mais informações, consulte [Conectores proxy de aplicação ad azure](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Fevereiro de 2018
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Navegação melhorada para gerir utilizadores e grupos

**Tipo:** Plano de mudança  
**Categoria de serviço:** Gestão de Diretórios  
**Capacidade do produto:** Diretório

A experiência de navegação para gerir utilizadores e grupos foi otimizada. Pode agora navegar a partir da descrição geral do diretamente à lista de todos os utilizadores, com acesso mais fácil para a lista de utilizadores eliminados. Também pode navegar da descrição geral do diretório diretamente para a lista de todos os grupos, com acesso mais fácil para as definições de gestão de grupo. E também da página de descrição geral de diretório, pode pesquisar por um utilizador, grupo, aplicação empresarial ou registo da aplicação. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Relatórios de disponibilidade de inícios de sessão e auditoria no Microsoft Azure operado pela 21Vianet (Azure China 21Vianet)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Pilha Azure  
**Capacidade do produto:** Monitorização e Reportagem

Relatórios de registo de atividade do AD do Azure estão agora disponíveis no Microsoft Azure operado pela 21Vianet (Azure China 21Vianet) instâncias. Os seguintes registos estão incluídos:

- **Registos de atividades de login** - Inclui todos os registos de login associados ao seu inquilino.

- **Autosserviço Registos** de Auditoria de Password - Inclui todos os registos de auditoria sSPR.

- Registos de Auditoria de Gestão de **Diretórios** - Inclui todos os registos de auditoria relacionados com a gestão de diretórios, como gestão de utilizadores, gestão de aplicações e outros.

Com estes registos, pode obter informações sobre como estado do seu ambiente. Os dados fornecidos permite-lhe:

- Determine como as aplicações e serviços são utilizados pelos seus utilizadores.

- Resolva problemas de impedir os utilizadores de realizar seu trabalho.

Para obter mais informações sobre como utilizar estes relatórios, consulte o [relatório do Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Utilize a função de "Leitor de relatório" (função de não administrador) para ver relatórios de atividade do Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Monitorização e Reportagem

Como parte do feedback dos clientes para permitir que as funções de não administração tenham acesso aos registos de atividade da Azure AD, permitimos que os utilizadores que estão na função "Report Reader" acedam à atividade de Sign-ins e Audit dentro do portal Azure, bem como usando o Microsoft Graph API. 

Para mais informações, como utilizar estes relatórios, consulte o [relatório do Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Ação employeeid disponível como atributo de utilizador e identificador de utilizador

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO
 
Pode configurar o **EmployeeID** como o identificador de utilizador e o atributo do Utilizador para utilizadores membros e hóspedes B2B em aplicações de sign-on baseadas em SAML a partir da ui aplicação enterprise.

Para mais informações, consulte a [Personalização de reclamações emitidas no token SAML para aplicações empresariais no Diretório Ativo azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Gestão simplificada de aplicação com carateres universais no Proxy de aplicações do Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Procuração de aplicativos  
**Capacidade do produto:** Autenticação do Utilizador
 
Para facilitar a implementação de aplicação e reduzir o overhead administrativo, suportamos agora a capacidade de publicar aplicações utilizando carateres universais. Para publicar uma aplicação de caráter universal, pode seguir o fluxo de publicação do aplicativo padrão, mas utilizar um caráter universal nos URLs internos e externos.

Para mais informações, consulte aplicações wildcard no proxy de aplicação de [diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Novos cmdlets para suportar a configuração do Proxy de aplicações

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Procuração de aplicativos  
**Capacidade do produto:** Plataforma

A versão mais recente do módulo de pré-visualização do AzureAD PowerShell contém novos cmdlets que permitem aos clientes configurar aplicações de Proxy de aplicação com o PowerShell.

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
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Novos cmdlets para suportar a configuração de grupos

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Procuração de aplicativos  
**Capacidade do produto:** Plataforma

A versão mais recente do módulo do AzureAD PowerShell contém cmdlets para gerir grupos no Azure AD. Estes cmdlets estavam disponíveis anteriormente no módulo do AzureADPreview e estão agora adicionados ao módulo do AzureAD

Os cmdlets de grupo que estão a lançamento agora para disponibilidade geral são: 

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
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Está disponível uma versão nova do Azure AD Connect

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Sincronização ad  
**Capacidade do produto:** Plataforma
 
Azure AD Connect é a ferramenta preferencial para sincronizar dados entre o Azure AD e em origens de dados locais, incluindo o Windows Server Active Directory e LDAP.

>[!Important]
>Apresenta o desta versão de esquema e sincronização as alterações da regra. O serviço do Azure AD Connect sincronização aciona uma importação completa e a sincronização completa etapas após uma atualização. Para obter informações sobre como alterar este comportamento, consulte [Como adiar a sincronização completa após](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade)a atualização .

Esta versão tem as seguintes atualizações e alterações:

**Questões fixas**

- Corrigi a janela de tempo em tarefas em segundo plano para a página de filtragem de partições quando mudar para a página seguinte.

- Foi corrigido um erro que provocou a violação de acesso durante a ação personalizada ConfigDB.

- Foi corrigido um erro ao recuperar a partir de tempo limite da ligação de sql.

- Foi corrigido um erro em que os certificados com carateres universais de SAN falharem a verificação dos pré-requisitos.

- Foi corrigido um erro que faz com que miiserver.exe falha durante a exportação de conector do AAD.

- Foi corrigido um erro em que uma tentativa de palavra-passe incorreta com sessão iniciada num controlador de domínio quando em execução causado o AAD connect Assistente para alterar a configuração

**Novas funcionalidades e melhorias**
 
- Telemetria do Application - os administradores podem alternar essa classe de dados liga/desliga.

- Dados de estado de funcionamento do AD do Azure - os administradores tem de visitar o portal de estado de funcionamento para controlar as definições de estado de funcionamento. Assim que a política do serviço tiver sido alterada, os agentes serão ler e impor-lo.

- Adicionar ações de configuração de repetição de escrita do dispositivo e uma barra de progresso para inicialização da página.

- Melhorada diagnósticos gerais com o relatório em HTML e recolha de dados completo num texto ZIP / relatório em HTML.

- Fiabilidade melhorada auto Atualize e adicionado telemetria adicional para garantir que o estado de funcionamento do servidor pode ser determinado.

- Restringir as permissões disponíveis para contas com privilégios na conta de conector AD. Para instalações novas, o assistente restringe as permissões que contas privilegiadas existentes na conta MSOL depois de criar a conta MSOL. As alterações afetam instalações express e das instalações personalizadas com a criação de conta.

- Alterar o instalador para não exigir privilégio de SA na instalação de raiz do AADConnect.

- Novo utilitário para resolver problemas de sincronização para um objeto específico. Atualmente, o utilitário verifica a existência dos seguintes pontos:

    - Erro de correspondência de UserPrincipalName entre a conta de utilizador no inquilino do Azure AD e o objeto de utilizador sincronizado.
  
    - Se o objeto é filtrado em sincronização devido a filtragem de domínio
  
    - Se o objeto é filtrado em sincronização devido a unidade organizacional (UO), filtragem

- Novo utilitário para sincronizar o hash de palavra-passe atual armazenado no diretório do Active Directory no local para uma conta de utilizador específico. O utilitário não requer uma alteração de palavra-passe. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplicações que suportam políticas de proteção de aplicações intune adicionadas para uso com acesso condicional baseado em aplicações da AD Azure

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Segurança e Proteção de Identidade

Adicionámos mais aplicações que suportam o Acesso Condicional baseado em aplicações. Agora, pode obter acesso ao Office 365 e outras aplicações de cloud do Azure AD-ligado a utilizar estas aplicações aprovadas do cliente.

As seguintes aplicações serão adicionadas ao final de Fevereiro:

- Microsoft Power BI

- Microsoft Launcher

- Faturação da Microsoft

Para obter mais informações, consulte:

- [Requisito de aplicação de cliente aprovado](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Acesso Condicional baseado em aplicativos Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Termos de atualização de utilização para experiência móvel 

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Termos de utilização  
**Capacidade do produto:** Conformidade

Quando os termos de utilização são apresentados, pode agora clicar em **ter problemas em visualização? Clique aqui**. Ao clicar nesta ligação abre os termos de utilização de forma nativa no dispositivo. Independentemente do tamanho do tipo de letra no documento ou o tamanho de ecrã do dispositivo, pode ampliar e ler o documento conforme necessário. 

---
 
## <a name="january-2018"></a>Janeiro de 2018
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novas aplicações federadas disponíveis na Galeria de aplicações do Azure AD 

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido

Em Janeiro de 2018, as seguintes novas aplicações com suporte para federação foram adicionadas na Galeria de aplicações:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Privacy Management Software,](https://go.microsoft.com/fwlink/?linkid=861660) [Dealpath,](https://go.microsoft.com/fwlink/?linkid=863526)[IriusRisk Federated Diretório, e [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial).

Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Inicie sessão com risco adicional detetado

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Segurança e Proteção de Identidade

A perceção que obtém para uma deteção de risco detetada está ligada à sua subscrição de AD Azure. Com a edição do Azure AD Premium P2, obtenha as informações mais detalhadas sobre todas as detecções subjacentes.

Com a edição Azure AD Premium P1, as deteções que não estão cobertas pela sua licença aparecem como o Sign-in de deteção de risco com risco adicional detetado.

Para mais informações, consulte deteções de risco do [Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Ocultar aplicações do Office 365 a partir de painéis de acesso do utilizador final

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Minhas Apps  
**Capacidade do produto:** SSO

Pode agora gerenciar melhor como aplicações do Office 365 aparecem nos painéis de acesso dos seus utilizadores através de uma nova definição de utilizador. Esta opção é útil para reduzir o número de aplicações nos painéis de acesso de um utilizador, se preferir Mostrar apenas as aplicações do Office no portal do Office. A definição está localizada nas **Definições** do Utilizador e está rotulada, **os Utilizadores só podem ver as aplicações do Office 365 no portal Office 365**.

Para mais informações, consulte [Ocultar uma aplicação da experiência do utilizador no Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Início de sessão totalmente integrado em aplicações ativadas para SSO de palavra-passe diretamente a partir do URL da aplicação 

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Minhas Apps  
**Capacidade do produto:** SSO

A extensão do browser as minhas aplicações agora está disponível por meio de uma ferramenta conveniente que lhe dá o minhas aplicações início de sessão único na capacidade como um atalho no seu browser. Depois de instalar, usuário verá um ícone de bolachas no browser que proporciona-lhes acesso rápido às aplicações. Os utilizadores podem agora tirar partido do:

- A capacidade de iniciar sessão diretamente em aplicações baseadas em password-SSO a partir da página de entrada da aplicação
- Iniciar qualquer aplicação a utilizar a funcionalidade de pesquisa rápida
- Atalhos para aplicações recentemente utilizadas partir da extensão
- A extensão está disponível para Microsoft Edge, Chrome e Firefox.
 
Para mais informações, consulte [A extensão de inscrição segura](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)das minhas apps .

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Administração do Azure AD foi extinto experiência no Portal clássico do Azure

**Tipo:** Depreciado   
**Categoria de serviço:** Azure AD  
**Capacidade do produto:** Diretório

A partir de 8 de Janeiro de 2018, a administração do Azure AD foi extinto experiência no portal clássico do Azure. Isso ocorria em conjunto com a desativação do portal clássico do Azure em si. No futuro, deverá utilizar o centro de [administração Azure AD](https://aad.portal.azure.com) para toda a sua administração baseada em portal saqueado da Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>O portal web do PhoneFactor foi extinto

**Tipo:** Depreciado  
**Categoria de serviço:** Azure AD  
**Capacidade do produto:** Diretório
 
A partir de 8 de Janeiro de 2018, o portal web do PhoneFactor foi preterido. Este portal foi utilizado para a administração de servidor MFA, mas essas funções foram movidas para o portal do Azure em portal.azure.com. 

A configuração mfa está localizada em: **Azure Ative Directory \> MFA Server**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Preterir o relatórios do Azure AD

**Tipo:** Depreciado  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Gestão do ciclo de vida de identidade  


Com a disponibilidade geral do novo console de administração do Active Directory do Azure e novas APIs agora disponíveis para relatórios de atividade e segurança, o relatório APIs em ponto final de "/ relatórios" foi descontinuado a partir do final do dia 31 de Dezembro de 2017.

**O que está disponível?**

Como parte da transição para a nova consola de administração, que fizemos 2 novas APIs disponíveis para recuperar os registos de atividade do Azure AD. O novo conjunto de APIs fornece mais avançado de filtragem e ordenação funcionalidade além de fornecermos o auditoria mais rica e atividades de início de sessão. Os dados anteriormente disponíveis através dos relatórios de segurança podem agora ser acedidos através da API de deteção de risco de Proteção de Identidade no Microsoft Graph.

Para obter mais informações, consulte:

- [Começar com o Diretório Ativo Azure reportando API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Começar com a Proteção de Identidade do Diretório Ativo azure e o Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Dezembro de 2017

### <a name="terms-of-use-in-the-access-panel"></a>Termos de utilização no painel de acesso

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Termos de utilização  
**Capacidade do produto:** Conformidade
 
Agora pode aceder ao painel de acesso e ver os termos de utilização que aceitou anteriormente.

Siga estes passos.

1. Vá ao [portal MyApps](https://myapps.microsoft.com)e inscreva-se.

2. No canto superior direito, selecione o seu nome e, em seguida, selecione **Perfil** da lista. 

3. No seu **Perfil,** selecione **Rever os termos de utilização**. 

4. Agora, pode rever os termos de utilização aceitaram. 

Para mais informações, consulte a funcionalidade de [utilização da AD Azure (pré-visualização)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nova experiência de início de sessão do Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Azure AD  
**Capacidade do produto:** Autenticação do utilizador
 
O Azure AD e o sistema de identidade de conta do Microsoft interfaces do usuário foram reprojetadas para que tenham uma aspeto e funcionalidade consistentes. Além disso, a página de início de sessão do Azure AD recolhe o nome de utilizador em primeiro lugar, seguido da credencial numa segunda tela.

Para mais informações, consulte A nova experiência de entrada em [Anúncios Azure está agora em pré-visualização pública.](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Menos avisos de início de sessão: uma experiência de nova "manter sessão iniciada" do Azure AD para início de sessão

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Azure AD  
**Capacidade do produto:** Autenticação do utilizador
 
O **Keep me assinado na** caixa de verificação na página de entrada da AD Azure foi substituído por um novo pedido que aparece depois de autenticar com sucesso. 

Se responder **Sim** a este pedido, o serviço dá-lhe um token de refrescamento persistente. Este comportamento é o mesmo que quando selecionaste o **Keep me assinado na** caixa de verificação na experiência antiga. Para inquilinos federados, esta linha de comandos mostra depois de autenticar com êxito com o serviço federado.

Para mais informações, consulte [Menos solicitações de entrada: A nova experiência "mantenha-me assinada" para a Azure AD está em pré-visualização](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Adicionar a configuração para exigir que os termos de utilização para ser expandido antes de os aceitarem

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Termos de utilização  
**Capacidade do produto:** Conformidade
 
Uma opção para administradores requer que os utilizadores expandam os termos de utilização antes de aceitar os termos.

Selecione **ou On** ou **Off** para exigir que os utilizadores expandam os termos de utilização. A definição **De On** requer que os utilizadores vejam os termos de utilização antes de os aceitarem.

Para mais informações, consulte a funcionalidade de [utilização da AD Azure (pré-visualização)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Ativação de âmbito para atribuições de função elegível

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão privilegiada da identidade  
**Capacidade do produto:** Gestão privilegiada da identidade
 
Pode usar o âmbito de ativação para ativar as atribuições de funções de recursos do Azure elegíveis com menos autonomia dos padrões de atribuição original. Um exemplo é se estiver atribuído como o proprietário de uma subscrição no seu inquilino. Com a ativação de âmbito, pode ativar a função de proprietário para até cinco recursos contidos dentro da subscrição (por exemplo, grupos de recursos e máquinas virtuais). A ativação de âmbito, poderá reduzir a possibilidade de alterações indesejadas aos recursos do Azure críticas em execução.

Para mais informações, consulte o que é a [Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Novas aplicações federadas na Galeria de aplicações do Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido

Em Dezembro de 2017, adicionámos que suportam a essas novas aplicações com a Federação para nossa Galeria de aplicações:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet,](https://go.microsoft.com/fwlink/?linkid=863525) [IMAGE WORKS,](https://go.microsoft.com/fwlink/?linkid=863517) [MOBI,](https://go.microsoft.com/fwlink/?linkid=863521) [MobileIron Azure AD integração,](https://go.microsoft.com/fwlink/?linkid=858027) [Reflektive,](https://go.microsoft.com/fwlink/?linkid=863518) [SAML SSO for Bamboo by resolution GmbH,](https://go.microsoft.com/fwlink/?linkid=863520) [SAML SSO for Bitbucket by resolution GmbH,](https://go.microsoft.com/fwlink/?linkid=863519) [Vodeclic,](https://go.microsoft.com/fwlink/?linkid=863522)WebHR, Zenegy Azure AD Integration.

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial).

Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Fluxos de trabalho de aprovação para funções de diretório do Azure AD

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Gestão privilegiada da identidade  
**Capacidade do produto:** Gestão privilegiada da identidade
 
Fluxo de trabalho de aprovação para funções de diretório do Azure AD está disponível em geral.

Com o fluxo de trabalho de aprovação, os administradores de função com privilégios podem exigir membros da função de elegíveis para pedir a ativação de função antes de poderem utilizar a função com privilégios. Vários utilizadores e grupos podem ser delegada aprovação responsabilidades. Os membros da função elegível recebem notificações quando a aprovação foi concluída e a respetiva função está ativa.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Autenticação pass-through: o Skype para suporte de negócio

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do utilizador

Agora a autenticação pass-through suporta início de sessão de utilizador-ins ao Skype para aplicações de cliente de negócio que suportam a autenticação moderna, que inclui online e topologias híbridas. 

Para mais informações, consulte [Skype para topoologias empresariais suportadas com autenticação moderna.](https://technet.microsoft.com/library/mt803262.aspx)
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Atualizações ao Azure AD Privileged Identity Management do RBAC do Azure (pré-visualização)

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Gestão privilegiada da identidade  
**Capacidade do produto:** Gestão privilegiada da identidade
 
Com a atualização da pré-visualização pública do Azure AD Privileged Identity Management (PIM) para o controlo de acesso de controlo (RBAC), pode agora:

* Utilize a administração Just Enough.
* Exigir aprovação para ativar as funções de recursos.
* Agende uma ativação futura de uma função que necessite de aprovação para ambas do Azure AD e funções RBAC do Azure.
 
Para mais informações, consulte [A Gestão de Identidade Privilegiada para recursos Azure (pré-visualização)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>Novembro de 2017
 
### <a name="access-control-service-retirement"></a>Extinção do serviço de controlo de acesso

**Tipo:** Plano de mudança  
**Categoria de serviço:** Serviço de Controlo de Acesso  
**Capacidade do produto:** Serviço de Controlo de Acesso 

O Azure Active Directory controlo de acesso (também conhecido como o serviço de controlo de acesso) vão ser descontinuado no final de 2018. Obter mais informações, que inclui um detalhados e orientações para a migração de elevado nível irão ser fornecidas nas próximas semanas. Pode deixar comentários nesta página, com quaisquer perguntas sobre o serviço de controlo de acesso e um membro da Equipe será respondê-las.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Restringir o acesso ao browser para o Intune Managed Browser 

**Tipo:** Plano de mudança  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Segurança e proteção de identidade

Pode restringir o acesso ao browser ao Office 365 e outras aplicações de cloud do Azure AD-ligado ao utilizar o Intune Managed Browser como uma aplicação aprovada. 

Pode agora configurar a seguinte condição para o Acesso Condicional baseado na aplicação:

**Aplicativos para clientes:** Navegador

**Qual é o efeito da mudança?**

Hoje em dia, o acesso é bloqueado ao utilizar esta condição. Quando a pré-visualização está disponível, todos os acessos exige a utilização da aplicação de browser gerido. 

Procure esta capacidade e mais informações nos próximas blogs e notas de versão. 

Para mais informações, consulte [Acesso Condicional em Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novas aplicações de clientes aprovadas para acesso condicional baseado em aplicativos Azure AD

**Tipo:** Plano de mudança  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Segurança e proteção de identidade

As seguintes aplicações estão na lista de [aplicações de clientes aprovadas:](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Para obter mais informações, consulte:

- [Requisito de aplicação de cliente aprovado](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Acesso Condicional baseado em aplicativos Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Suporte de termos de utilização de vários idiomas

**Tipo:** Nova funcionalidade    
**Categoria de serviço:** Termos de utilização  
**Capacidade do produto:** Conformidade

Agora, os administradores podem criar novos termos de utilização que contêm vários documentos PDF. Pode Etiquetar estes documentos PDF com um idioma correspondente. Os utilizadores são apresentados o PDF com a linguagem correspondente com base nas respetivas preferências. Se não houver nenhuma correspondência, é mostrado o idioma predefinido.

---
 
### <a name="real-time-password-writeback-client-status"></a>Estado do cliente de repetição de escrita de palavra-passe em tempo real

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reset de senha de autosserviço  
**Capacidade do produto:** Autenticação do utilizador

Agora pode rever o estado do seu cliente de repetição de escrita de palavra-passe no local. Esta opção está disponível na secção **de integração no local** da página de [reset](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) password. 

Se existirem problemas com a ligação para o cliente de repetição de escrita no local, verá uma mensagem de erro que fornece-lhe:

- Informações sobre por que não é possível ligar ao seu cliente de repetição de escrita no local.
- Uma ligação para a documentação que ajuda a resolver o problema. 

Para mais informações, consulte a [integração no local.](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration)

---

### <a name="azure-ad-app-based-conditional-access"></a>Acesso Condicional baseado em aplicativos Azure AD 
 
**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Azure AD  
**Capacidade do produto:** Segurança e proteção de identidade

Agora pode restringir o acesso ao Office 365 e a outras aplicações cloud ligadas ao Azure AD a [aplicações de clientes aprovadas](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview) que suportam políticas de proteção de aplicações Intune utilizando o [Acesso Condicional baseado em aplicações DaA Azure.](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) Políticas de proteção de aplicações do Intune são utilizadas para configurar e proteger os dados da empresa nesses aplicativos de cliente.

Ao combinar as políticas de acesso condicional [baseadas](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) em [aplicativos,](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) tem a flexibilidade para proteger dados para dispositivos pessoais e da empresa.

As seguintes condições e controlos estão agora disponíveis para utilização com acesso condicional baseado em aplicativos:

**Condição da plataforma suportada**

- iOS
- Android

**Condição de aplicativos para clientes**

- Aplicações móveis e clientes de ambiente de trabalho

**Controlo de acesso**

- Requer aplicação aprovada do cliente

Para mais informações, consulte [o Acesso Condicional baseado em aplicativos Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Gerir dispositivos do Azure AD no portal do Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Registo e gestão de dispositivos  
**Capacidade do produto:** Segurança e proteção de identidade

Agora pode encontrar todos os seus dispositivos ligados ao Azure AD e as atividades relacionadas com o dispositivo num único local. Há uma nova experiência de administração para gerir todas as suas identidades do dispositivo e definições no portal do Azure. Nesta versão, pode:

- Consulte todos os seus dispositivos disponíveis para Acesso Condicional em Azure AD.
- Ver as propriedades, que incluem o seu híbrido do Azure dispositivos associados ao AD.
- Localizar as chaves de BitLocker para os seus dispositivos associados ao AD do Azure, gerir o seu dispositivo com Intune e muito mais.
- Gerir definições relacionadas com o dispositivo do Azure AD.

Para mais informações, consulte [Gerir dispositivos utilizando o portal Azure](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Suporte para o macOS como plataforma de dispositivos para acesso condicional Azure AD 

**Tipo:** Nova funcionalidade    
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Segurança e proteção de identidade 

Pode agora incluir (ou excluir) o macOS como condição da plataforma do dispositivo na sua política de Acesso Condicional Azure AD. Com a adição do macOS para as plataformas de dispositivos suportados, pode:

- **Inscreva-se e gerencie os dispositivos macOS utilizando o Intune.** Assim como outras plataformas como iOS e Android, uma aplicação do portal da empresa está disponível para macOS fazer inscrições unificadas. Pode utilizar a nova aplicação do portal da empresa para macOS para inscrever um dispositivo com o Intune e registá-lo com o Azure AD.
- **Certifique-se de que os dispositivos macOS aderem às políticas de conformidade da sua organização definidas no Intune.** No Intune no portal do Azure, agora pode configurar políticas de conformidade para dispositivos macOS. 
- **Restringir o acesso a aplicações em Azure AD apenas dispositivos macOS compatíveis.** A política de acesso condicional tem o macOS como uma opção de plataforma de dispositivo separada. Agora pode ser autor de políticas de acesso condicional específicas do macOS para a aplicação direcionada definida em Azure.

Para obter mais informações, consulte:

- [Criar uma política de conformidade para dispositivos macOS com o Intune](https://aka.ms/macoscompliancepolicy)
- [Acesso Condicional em Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Extensão de servidor de políticas de rede para o Azure multi-factor Authentication 

**Tipo:** Nova funcionalidade    
**Categoria de serviço:**  Autenticação de vários fatores  
**Capacidade do produto:** Autenticação do utilizador

A extensão de servidor de políticas de rede para o Azure multi-factor Authentication adiciona capacidades de multi-factor Authentication com base na cloud à sua infraestrutura de autenticação ao utilizar seus servidores existentes. Com a extensão de servidor de políticas de rede, pode adicionar a chamada telefónica, mensagem de texto ou verificação de aplicação do telefone para o fluxo de autenticação existente. Não é preciso instalar, configurar e manter novos servidores. 

Esta extensão foi criada para as organizações que pretendem proteger as ligações de rede privada virtual sem ter de implementar o servidor do Azure multi-factor Authentication. O servidor de políticas de rede extensão age como um adaptador entre RADIUS e com base na cloud do Azure multi-factor Authentication para fornecer um segundo fator de autenticação para federada ou utilizadores sincronizados.

Para mais informações, consulte Integrar a sua infraestrutura de servidor de política de [rede existente com autenticação de multi-factores Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Restaure ou remover utilizadores eliminados permanentemente

**Tipo:** Nova funcionalidade    
**Categoria de serviço:** Gestão do utilizador  
**Capacidade do produto:** Diretório 

No Centro de administração do Azure AD, pode agora:

- Restaure um utilizador eliminado. 
- Elimine permanentemente um utilizador.

**Para experimentar:**

1. No centro de administração da AD Azure, selecione [Todos os utilizadores](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) da secção **Gerir.** 

2. Na lista **show,** selecione **utilizadores recentemente eliminados**. 

3. Selecione um ou mais utilizadores recentemente eliminados e, em seguida, restaurá-los ou eliminá-los permanentemente.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novas aplicações de clientes aprovadas para acesso condicional baseado em aplicativos Azure AD
 
**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Segurança e proteção de identidade

As seguintes aplicações foram adicionadas à lista de [aplicações de clientes aprovadas:](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)

- Microsoft Planner
- Azure Information Protection 

Para obter mais informações, consulte:

- [Requisito de aplicação de cliente aprovado](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Acesso Condicional baseado em aplicativos Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Utilizar "OR" entre controlos numa política de acesso condicional 

**Tipo:** Mudança de funcionalidade    
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Segurança e proteção de identidade
 
Agora pode utilizar "OR" (exigir um dos comandos selecionados) para controlos de acesso condicional. Pode utilizar esta funcionalidade para criar políticas com "ou" entre os controles de acesso. Por exemplo, pode utilizar esta funcionalidade para criar uma política que requer que um utilizador para iniciar sessão com a multi-factor Authentication "ou" para estar num dispositivo em conformidade.

Para mais informações, consulte [Controlos no Acesso Condicional Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Agregação de deteções de risco em tempo real

**Tipo:** Mudança de funcionalidade    
**Categoria de serviço:** Proteção de identidade  
**Capacidade do produto:** Segurança e proteção de identidade

Na Proteção de Identidade da AD Azure, todas as deteções de risco em tempo real originárias do mesmo endereço IP num determinado dia são agora agregadas para cada tipo de deteção de risco. Esta alteração limita o volume de deteções de risco demonstrados sem qualquer alteração na segurança do utilizador.

A deteção em tempo real subjacente funciona sempre que o utilizador inicia sessão. Se tiver uma política de segurança de risco de início de sessão para o multi-factor Authentication ou bloquear o acesso, ainda é acionado durante cada risco início de sessão.
 
---
 
## <a name="october-2017"></a>Outubro de 2017

### <a name="deprecate-azure-ad-reports"></a>Preterir o relatórios do Azure AD

**Tipo:** Plano de mudança  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Gestão do ciclo de vida de identidade  

O portal do Azure fornece-lhe:

- Uma nova consola de administração do Azure AD.
- Novas APIs para relatórios de atividade e segurança.
 
Devido a estas novas capacidades, o relatório APIs sob o ponto de extremidade /reports foram extinguidas a 10 de Dezembro de 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Deteção de campo de início de sessão automático

**Tipo:** Fixo   
**Categoria de serviço:** Minhas Apps  
**Capacidade do produto:** Inscrição única  

O Azure AD suporta a deteção de campo de início de sessão automático para aplicações que compõem um campo de nome e palavra-passe de utilizador do HTML. Estes passos estão documentados em [Como capturar automaticamente os campos de inscrição para uma aplicação](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Pode encontrar esta capacidade adicionando uma aplicação *não-Galeria* na página **de Aplicações Empresariais** no [portal Azure](https://aad.portal.azure.com). Além disso, pode configurar o modo **de início de início único** nesta nova aplicação para o Single **Sign-on baseado em Password,** introduzir um URL web e, em seguida, guardar a página.
 
Devido a um problema de serviço, essa funcionalidade foi temporariamente desativada. O problema foi resolvido, e a deteção de campo de início de sessão automático estiver novamente disponível.

---

### <a name="new-multi-factor-authentication-features"></a>Novos recursos de multi-factor Authentication

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticação de vários fatores  
**Capacidade do produto:** Segurança e proteção de identidade  

Autenticação multifator (MFA) é uma parte essencial de proteger a sua organização. Para tornar as credenciais mais adaptáveis e a experiência mais integrada, foram adicionadas as seguintes funcionalidades: 

- Resultados do desafio de multi-factor são diretamente integrados ao Azure AD início de sessão no relatório, que inclui o acesso programático aos resultados MFA.
- A configuração de MFA está mais profundamente integrada para a configuração do Azure AD experiência no portal do Azure.

Com esta pré-visualização pública, gestão de MFA e relatórios são uma parte integrada da experiência de configuração do núcleo do Azure AD. Agora pode gerir a funcionalidade de portal de gestão MFA na experiência do Azure AD.

Para mais informações, consulte [referência para relatórios de MFA no portal Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Termos de utilização

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Termos de utilização  
**Capacidade do produto:** Conformidade  

Pode utilizar os termos do Azure AD de utilização para apresentar informações tais como avisos de isenção relevantes para os requisitos legais ou de conformidade para os utilizadores.

Pode utilizar os termos do Azure AD de utilização nos seguintes cenários:

- Termos de utilização para todos os utilizadores na sua organização gerais
- Termos de utilização com base em atributos de um utilizador (por exemplo, médicos versus enfermeiras) ou doméstica versus a colaboradores internacionais, feito por grupos dinâmicos específicos
- Termos de utilização específicos para aceder a aplicações de elevado impacto comercial, como o Salesforce

Para mais informações, consulte os [termos de utilização da Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Aprimoramentos do Privileged Identity Management

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão privilegiada da identidade  
**Capacidade do produto:** Gestão privilegiada da identidade  

Com o Azure AD Privileged Identity Management, pode gerir, controlar e monitorizar o acesso aos recursos do Azure (pré-visualização) na sua organização para:

- Subscrições
- Grupos de recursos
- Máquinas virtuais 

Todos os recursos no portal do Azure que utilizam a funcionalidade de RBAC do Azure podem tirar partido de toda a segurança e capacidades de gestão do ciclo de vida que o Azure AD Privileged Identity Management tem a oferecer.

Para mais informações, consulte [A Gestão de Identidade Privilegiada para recursos Azure.](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)

---

### <a name="access-reviews"></a>Revisões de acesso

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Avaliações de acesso  
**Capacidade do produto:** Conformidade  

As organizações podem utilizar as revisões de acesso (pré-visualização) para gerir de forma eficiente as associações de grupo e o acesso a aplicações empresariais: 

- Pode voltar a certificar o acesso do utilizador convidado ao utilizar as revisões do respetivo acesso às aplicações e as associações a grupos. Os revisores podem decidir eficazmente se a permitir que os convidados acesso com base nas informações fornecidas pelas revisões de acesso contínuo.
- Pode voltar a certificar o acesso dos colaboradores às aplicações e as associações a grupos com revisões de acesso.

Pode recolher os controlos de revisão de acesso relativos a programas relevantes para a sua organização de modo a controlar as revisões de conformidade ou de aplicações sensíveis a riscos.

Para mais informações, consulte as avaliações de acesso da [AD Azure.](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Ocultar aplicações de terceiros partir as minhas aplicações e iniciador de aplicações do Office 365

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Minhas Apps  
**Capacidade do produto:** Inscrição única  

Agora pode gerir melhor as aplicações que aparecem nos portais dos seus utilizadores através de uma nova propriedade de **aplicações de ocultação.** Pode ocultar aplicações para ajudar a nos casos em que os mosaicos de aplicação apresentada para serviços de back-end ou mosaicos duplicados e iniciadores de aplicações dos utilizadores de confusão. O alternância encontra-se na secção **Propriedades** da aplicação de terceiros e está rotulado **de Visível para o utilizador?** Também pode ocultar uma aplicação através de programação através do PowerShell. 

Para mais informações, consulte [Ocultar uma aplicação de terceiros a partir da experiência de um utilizador em Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**O que está disponível?**

 Como parte da transição para a nova consola de administração, duas novas APIs para recuperação das atividades do Azure AD, os registos estão disponíveis. O novo conjunto de APIs fornece mais avançado de filtragem e ordenação funcionalidade além de fornecermos o auditoria mais rica e atividades de início de sessão. Os dados anteriormente disponíveis através dos relatórios de segurança podem agora ser acedidos através da API de Deteção de Riscos de Proteção de Identidade no Microsoft Graph.


## <a name="september-2017"></a>Setembro de 2017

### <a name="hotfix-for-identity-manager"></a>Correção do Identity Manager

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Gestor de Identidade  
**Capacidade do produto:** Gestão do ciclo de vida de identidade  

Um pacote de rollup de correção (compilação 4.4.1642.0) está disponível a partir de 25 de Setembro de 2017 do Identity Manager 2016 Service Pack 1. Este pacote de rollup:

- Resolve problemas e adiciona melhorias.
- É uma atualização cumulativa que substitui todas as atualizações do Identity Manager 2016 Service Pack 1 até a compilação 4.4.1459.0 do Identity Manager 2016. 
- Requer que tenha de criar 4.4.1302.0 do Identity Manager 2016. 

Para mais informações, consulte o [pacote de rollup Hotfix (build 4.4.1642.0) disponível para O Pacote de Serviços Identity Manager 2016 1](https://support.microsoft.com/help/4021562). 

---
