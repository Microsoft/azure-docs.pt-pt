---
title: Novidades Notas de versão-Azure Active Directory | Microsoft Docs
description: Saiba o que há de novo no Azure Active Directory, como as notas de versão mais recentes, problemas conhecidos, correções de bugs, funcionalidade preterida e alterações futuras.
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
ms.date: 10/15/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 096c5f06c53f1e93a0c661f856ab112236c0ec60
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373554"
---
# <a name="whats-new-in-azure-active-directory"></a>O que há de novo no Azure Active Directory?

>Seja notificado sobre quando revisitar esta página para obter atualizações copiando e colando esta URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` em seu @no__t ícone do leitor de feeds 1RSS @ no__t-2 Feed Reader.

O Azure AD recebe melhorias em uma base contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de erros
- Funcionalidade preterida
- Planos de alterações

Esta página é atualizada mensalmente, portanto, reveja-a regularmente. Se você estiver procurando itens com mais de seis meses, poderá encontrá-los no [arquivo morto para as novidades no Azure Active Directory](whats-new-archive.md).

---

## <a name="september-2019"></a>Setembro de 2019

### <a name="deprecation-of-the-power-bi-content-packs"></a>Substituição dos pacotes de conteúdo do Power BI

**Tipo:** Preterido  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios

Power BI estiver preterindo pacotes de conteúdo de seu armazenamento e a partir de **1º de outubro de 2019**, não estamos mais dando suporte ao pacote de conteúdo do Azure ad Power bi. Atualmente, os clientes que usam o pacote de conteúdo podem continuar a usá-lo, mesmo que as APIs subjacentes sejam versões mais antigas. Como alternativa para esse pacote de conteúdo, você pode usar pastas de trabalho do Azure AD (criadas em Log Analytics) para obter informações sobre seus serviços relacionados ao Azure AD.

Para obter mais informações sobre as pastas de trabalho, consulte [como usar pastas de trabalho do Azure monitor para relatórios Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Para obter mais informações sobre a substituição dos pacotes de conteúdo, consulte a postagem do blog [anunciando Power bi aplicativos de modelo de disponibilidade geral](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

### <a name="my-profile-is-re-naming-and-integrating-with-the-microsoft-office-account-page"></a>Meu perfil está renomeando e integrando com a página da conta Microsoft Office

**Tipo:** Planejar alteração  
**Categoria de serviço:** Meu perfil/conta  
**Funcionalidade do produto:** Colaboração

A partir de outubro, a minha experiência de perfil se tornará minha conta. Como parte dessa alteração, em todos os lugares que, no momento, **o meu perfil** será alterado para **minha conta**. Além da alteração de nomenclatura e de algumas melhorias de design, a experiência atualizada oferecerá integração adicional com a página de Microsoft Office conta. Especificamente, você poderá acessar as instalações e assinaturas do Office na página da conta de **visão geral** , juntamente com as preferências de contato relacionadas ao Office na página de **privacidade** .

Para obter mais informações sobre a experiência meu perfil (versão prévia), consulte [visão geral do portal meu perfil (versão prévia)](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Gerenciar grupos e membros em massa usando arquivos CSV no portal do AD do Azure (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerenciamento de grupo  
**Funcionalidade do produto:** Colaboração

Temos o prazer de anunciar a disponibilidade de visualização pública das experiências de gerenciamento de grupo em massa no portal do Azure AD. Agora você pode usar um arquivo CSV e o portal do AD do Azure para gerenciar grupos e listas de membros, incluindo:

- Adicionando ou removendo membros de um grupo.

- Baixando a lista de grupos do diretório.

- Baixando a lista de membros do grupo para um grupo específico.

Para obter mais informações, consulte [adicionar membros em massa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [remover membros em](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)massa, [lista de membros de download em massa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)e lista de grupos de [download em massa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>O consentimento dinâmico agora tem suporte por meio de um novo ponto de extremidade de consentimento do administrador

**Tipo:** Novo recurso  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário

Criamos um novo ponto de extremidade de consentimento do administrador para dar suporte ao consentimento dinâmico, o que é útil para aplicativos que desejam usar o modelo de consentimento dinâmico na plataforma de identidade da Microsoft.

Para obter mais informações sobre como usar esse novo ponto de extremidade, consulte [usando o ponto de extremidade de consentimento do administrador](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-azure-ad-global-reader-role"></a>Nova função de leitor global do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** RBAC  
**Funcionalidade do produto:** Controle de acesso

A partir de 24 de setembro de 2019, vamos começar a distribuir uma nova função de Azure Active Directory (AD) chamada leitor global. Essa distribuição começará com a produção e os clientes de nuvem global (GCC), concluindo todo o mundo em outubro.

A função de leitor global é a contraparte somente leitura para o administrador global. Os usuários nessa função podem ler configurações e informações administrativas entre Microsoft 365 serviços, mas não podem tomar ações de gerenciamento. Criamos a função de leitor global para ajudar a reduzir o número de administradores globais em sua organização. Como as contas de administrador global são poderosas e vulneráveis a ataques, recomendamos que você tenha menos de cinco administradores globais. É recomendável usar a função de leitor global para planejamento, auditorias ou investigações. Também é recomendável usar a função de leitor global em combinação com outras funções de administrador limitadas, como o administrador do Exchange, para ajudar a realizar o trabalho sem exigir a função de administrador global.

A função leitor global funciona com o novo centro de administração do Microsoft 365, centro de administração do Exchange, centro de administração do Team, central de segurança, centro de conformidade, centro de administração do Azure AD e centro de administração do gerenciamento de dispositivos.

>[!NOTE]
> No início da visualização pública, a função de leitor global não funcionará com: SharePoint, Privileged Access Management, Sistema de Proteção de Dados do Cliente, rótulos de sensibilidade, ciclo de vida de equipes, relatórios de equipes & análise de chamadas, gerenciamento de dispositivo de telefone IP de equipes e aplicativo de equipes Catalog. Todos esses serviços têm a finalidade de trabalhar com a função no futuro.

Para obter mais informações, consulte [permissões de função de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Acessar um servidor de relatório local de seu aplicativo Power BI Mobile usando Proxy de Aplicativo do Azure Active Directory

**Tipo:** Novo recurso  
**Categoria de serviço:** Proxy de aplicativo  
**Funcionalidade do produto:** Controle de acesso

A nova integração entre o aplicativo móvel Power BI e o Azure Proxy de Aplicativo do AD permite que você entre com segurança no aplicativo Power BI Mobile e exiba os relatórios de sua organização hospedados no Servidor de Relatórios do Power BI local.

Para obter informações sobre o aplicativo Power BI Mobile, incluindo onde baixar o aplicativo, consulte o [site do Power bi](https://powerbi.microsoft.com/mobile/). Para obter mais informações sobre como configurar o aplicativo móvel Power BI com o Azure Proxy de Aplicativo do AD, consulte [habilitar o acesso remoto para Power bi Mobile com o azure proxy de aplicativo do AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>A nova versão do módulo do PowerShell do AzureADPreview está disponível

**Tipo:** Recurso alterado  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Active

Novos cmdlets foram adicionados ao módulo AzureADPreview para ajudar a definir e atribuir funções personalizadas no Azure AD, incluindo:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nova versão do Azure AD Connect

**Tipo:** Recurso alterado  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Active

Lançamos uma versão atualizada do Azure AD Connect para clientes de atualização automática. Essa nova versão inclui vários novos recursos, aprimoramentos e correções de bugs. Para obter mais informações sobre essa nova versão, consulte [Azure ad Connect: histórico de lançamento de versão](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>O servidor de autenticação multifator (MFA) do Azure, versão 8.0.2 agora está disponível

**Tipo:** Fixado  
**Categoria de serviço:** FATO  
**Funcionalidade do produto:** Proteção de & de segurança de identidade

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

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Planejar alteração: substituição dos pacotes de conteúdo do Power BI

**Tipo:** Planejar alteração  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios

A partir de 1º de outubro de 2019, Power BI começará a substituir todos os pacotes de conteúdo, incluindo o pacote de conteúdo do Azure AD Power BI. Como alternativa para esse pacote de conteúdo, você pode usar pastas de trabalho do Azure AD para obter informações sobre seus serviços relacionados ao Azure AD. Pastas de trabalho adicionais são disponibilizadas, incluindo pastas de trabalho sobre políticas de acesso condicional no modo somente de relatório, informações baseadas em consentimento do aplicativo e muito mais.

Para obter mais informações sobre as pastas de trabalho, consulte [como usar pastas de trabalho do Azure monitor para relatórios Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Para obter mais informações sobre a substituição dos pacotes de conteúdo, consulte a postagem do blog [anunciando Power bi aplicativos de modelo de disponibilidade geral](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Pesquisa, filtragem e classificação aprimoradas para grupos estão disponíveis no portal do AD do Azure (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerenciamento de grupo  
**Funcionalidade do produto:** Colaboração

Temos o prazer de anunciar a disponibilidade de visualização pública das experiências relacionadas a grupos aprimoradas no portal do AD do Azure. Esses aprimoramentos ajudam você a gerenciar melhor grupos e listas de membros, fornecendo:

- Recursos de pesquisa avançada, como a pesquisa de subcadeia de caracteres em listas de grupos.
- Opções avançadas de filtragem e classificação em listas de membros e proprietários.
- Novos recursos de pesquisa para listas de membros e proprietários.
- Contagens de grupos mais precisas para grupos grandes.

Para obter mais informações, consulte [gerenciar grupos na portal do Azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Novas funções personalizadas estão disponíveis para gerenciamento de registro de aplicativo (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** RBAC  
**Funcionalidade do produto:** Controle de acesso

As funções personalizadas (disponíveis com uma assinatura P1 ou P2 do Azure AD) agora podem ajudar a fornecer acesso refinado, permitindo que você crie definições de função com permissões específicas e, em seguida, atribua essas funções a recursos específicos. No momento, você cria funções personalizadas usando permissões para gerenciar registros de aplicativo e, em seguida, atribuir a função a um aplicativo específico. Para obter mais informações sobre funções personalizadas, consulte [funções de administrador personalizado no Azure Active Directory (versão prévia)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Se você precisar de permissões ou recursos adicionais com suporte, que você não vê no momento, poderá enviar comentários para nosso [site de comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) e adicionaremos sua solicitação ao mapa de estrada de atualização.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Novos logs de provisionamento podem ajudá-lo a monitorar e solucionar problemas de implantação de provisionamento de aplicativo (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade

Novos logs de provisionamento estão disponíveis para ajudá-lo a monitorar e solucionar problemas de implantação de provisionamento de usuários e grupos. Esses novos arquivos de log incluem informações sobre:

- Quais grupos foram criados com êxito no [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Quais funções foram importadas de [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- O que os funcionários não foram importados do [workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Para obter mais informações, consulte [Provisionando relatórios no portal de Azure Active Directory (versão prévia)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Novos relatórios de segurança para todos os administradores do Azure AD (disponibilidade geral)

**Tipo:** Novo recurso  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** Proteção de & de segurança de identidade

Por padrão, todos os administradores do Azure AD serão capazes de acessar relatórios de segurança modernos no Azure AD. Até o fim de setembro, você poderá usar a faixa na parte superior dos relatórios de segurança modernos para retornar aos relatórios antigos.

Os relatórios de segurança modernos fornecerão recursos adicionais das versões mais antigas, incluindo:

- Filtragem e classificação avançadas
- Ações em massa, como ignorar o risco do usuário
- Confirmação de entidades comprometidas ou seguras
- Estado de risco, abrangendo: em risco, ignorado, remediado e confirmado comprometido
- Novas detecções relacionadas a riscos (disponíveis para assinantes Azure AD Premium)

Para obter mais informações, consulte [usuários arriscados](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report), [entradas arriscadas](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report)e [detecções de risco](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections-report).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>A identidade gerenciada atribuída pelo usuário está disponível para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais (disponibilidade geral)

**Tipo:** Novo recurso  
**Categoria de serviço:** Identidades gerenciadas para recursos do Azure  
**Funcionalidade do produto:** Experiência do desenvolvedor

Agora, as identidades gerenciadas atribuídas pelo usuário estão disponíveis para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais. Como parte disso, o Azure pode criar uma identidade no locatário do Azure AD que é confiável para a assinatura em uso e pode ser atribuída a uma ou mais instâncias de serviço do Azure. Para obter mais informações sobre identidades gerenciadas atribuídas pelo usuário, consulte [o que são identidades gerenciadas para recursos do Azure?](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Os usuários podem redefinir suas senhas usando um token de hardware ou aplicativo móvel (disponibilidade geral)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Redefinição de senha de autoatendimento  
**Funcionalidade do produto:** Autenticação do usuário

Os usuários que registraram um aplicativo móvel com sua organização agora podem redefinir sua própria senha, aprovando uma notificação do aplicativo Microsoft Authenticator ou inserindo um código de seu aplicativo móvel ou token de hardware.

Para obter mais informações, consulte [como funciona: redefinição de senha de autoatendimento do Azure ad](https://aka.ms/authappsspr). Para obter mais informações sobre a experiência do usuário, consulte [visão geral de redefinir sua própria senha corporativa ou de estudante](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignora o cache compartilhado MSAL.NET para cenários em nome de

**Tipo:** Fixado  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário

A partir da ADAL.NET (biblioteca de autenticação do Azure AD) versão 5.0.0-Preview, os desenvolvedores de aplicativos devem [serializar um cache por conta para aplicativos Web e APIs da Web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). Caso contrário, alguns cenários que usam o [fluxo em nome de](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow), juntamente com alguns casos de uso específicos de `UserAssertion`, podem resultar em uma elevação de privilégio. Para evitar essa vulnerabilidade, o ADAL.NET agora ignora o cache compartilhado da biblioteca de autenticação da Microsoft para dotNet (MSAL.NET) para cenários em nome de.

Para obter mais informações sobre esse problema, consulte [Azure Active Directory vulnerabilidade de elevação de privilégio da biblioteca de autenticação](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – agosto de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em agosto de 2019, adicionamos esses 26 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[Plataforma cívico](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [pronovo Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Portal de Inativ da Viareport (Europa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [participação da Academia](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), matriz de [prioridade](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync by Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [entregue. Media™ portal](https://portal.deliver.media), [frente Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD conectar](https://www.stashcat.com), [piscar](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [pronovo análises](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [assistir por cores](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [Harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB navegar pelo atendimento estratégico](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Novas versões dos módulos AzureAD PowerShell e AzureADPreview PowerShell estão disponíveis

**Tipo:** Recurso alterado  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Active

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

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerenciamento de grupo  
**Funcionalidade do produto:** Colaboração

Fizemos algumas melhorias da interface do usuário no construtor de regras de grupo dinâmico, disponível na portal do Azure, para ajudá-lo a configurar uma nova regra com mais facilidade ou alterar as regras existentes. Essa melhoria de design permite que você crie regras com até cinco expressões, em vez de apenas uma. Também atualizamos a lista de propriedades do dispositivo para remover as propriedades de dispositivo preteridas.

Para obter mais informações, consulte [gerenciar regras de associação dinâmica](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nova permissão de aplicativo Microsoft Graph disponível para uso com revisões de acesso

**Tipo:** Recurso alterado  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Governança de identidade

Apresentamos uma nova permissão de aplicativo Microsoft Graph, `AccessReview.ReadWrite.Membership`, que permite aos aplicativos criar e recuperar automaticamente as revisões de acesso para associações de grupo e atribuições de aplicativo. Essa permissão pode ser usada por seus trabalhos agendados ou como parte de sua automação, sem a necessidade de um contexto de usuário conectado.

Para obter mais informações, confira o [exemplo como criar revisões de acesso do Azure ad usando Microsoft Graph permissões de aplicativo com o blog do PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Os logs de atividade do Azure AD agora estão disponíveis para instâncias de nuvem do governo no Azure Monitor

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios

Estamos empolgados em anunciar que os logs de atividades do Azure AD agora estão disponíveis para instâncias de nuvem do governo no Azure Monitor. Agora você pode enviar logs do Azure AD para sua conta de armazenamento ou para um hub de eventos para integrar com suas ferramentas SIEM, como [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)e [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Para obter mais informações sobre como configurar Azure Monitor, consulte [logs de atividades do Azure AD no Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Atualize seus usuários para a nova experiência de informações de segurança aprimorada

**Tipo:** Recurso alterado  
**Categoria de serviço:**  Autenticações (logons)   
**Funcionalidade do produto:** Autenticação do usuário

Em 25 de setembro de 2019, vamos desligar a experiência de informações de segurança antiga e não avançada para registrar e gerenciar informações de segurança do usuário e ativar apenas a nova [versão aprimorada](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Isso significa que os usuários não poderão mais usar a experiência antiga.

Para obter mais informações sobre a experiência de informações de segurança aprimorada, consulte nossa documentação de [administrador](https://aka.ms/securityinfodocs) and nossa [documentação do usuário](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Para ativar essa nova experiência, você deve:

1. Entre no portal do Azure como um administrador global ou administrador de usuários.

2. Vá para **Azure Active Directory > configurações de usuário > gerenciar configurações para recursos de visualização do painel de acesso**.

3. No, os **usuários podem usar os recursos de visualização para registrar e gerenciar informações de segurança – área avançada** , selecionar **selecionado**e, em seguida, escolher um grupo de usuários ou escolher **tudo** para ativar esse recurso para todos os usuários no locatário.

4. Em * * os usuários podem usar recursos de visualização para registrar e gerenciar segurança * * info * * * *, selecione **nenhum**.

5. Salve suas configurações.

    Depois de salvar suas configurações, você não terá mais acesso à antiga experiência de informações de segurança.

>[!Important]
>Se você não concluir essas etapas antes de 25 de setembro de 2019, seu locatário de Azure Active Directory será habilitado automaticamente para a experiência aprimorada. Se você tiver dúvidas, entre em contato conosco em registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>As solicitações de autenticação que usam os logons POST serão mais rigorosamente validadas

**Tipo:** Recurso alterado  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Padrões

A partir de 2 de setembro de 2019, as solicitações de autenticação usando o método POST serão mais rigorosamente validadas em relação aos padrões HTTP. Especificamente, espaços e aspas duplas (") não serão mais removidos dos valores do formulário de solicitação. Essas alterações não devem interromper nenhum cliente existente e ajudarão a garantir que as solicitações enviadas ao Azure AD sejam manipuladas de maneira confiável todas as vezes.

Para obter mais informações, consulte os [avisos de alterações significativas do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Julho de 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Plano para alteração: atualização do serviço de proxy de aplicativo para dar suporte apenas a TLS 1,2

**Tipo:** Planejar alteração  
**Categoria de serviço:** Proxy de aplicativo  
**Funcionalidade do produto:** Controle de acesso

Para ajudar a fornecer a criptografia mais forte, vamos começar limitando o acesso do serviço de proxy de aplicativo somente a protocolos TLS 1,2. Essa limitação inicialmente será distribuída para os clientes que já estão usando protocolos TLS 1,2, portanto, você não verá o impacto. A reprovação completa dos protocolos TLS 1,0 e TLS 1,1 será concluída em 31 de agosto de 2019. Os clientes que ainda usam o TLS 1,0 e o TLS 1,1 receberão um aviso avançado para se preparar para essa alteração.

Para manter a conexão com o serviço de proxy de aplicativo durante essa alteração, recomendamos que você verifique se as combinações de servidor cliente e navegador-servidor estão atualizadas para usar o TLS 1,2. Também recomendamos que você inclua todos os sistemas clientes usados por seus funcionários para acessar os aplicativos publicados por meio do serviço de proxy de aplicativo.

Para obter mais informações, consulte [Adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plano para alteração: as atualizações de design são recebidas para a Galeria de aplicativos

**Tipo:** Planejar alteração  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** EXTERNO

Novas alterações de interface do usuário estão chegando ao design do **Adicionar da** área da Galeria da folha **Adicionar um aplicativo** . Essas alterações ajudarão você a localizar mais facilmente seus aplicativos que dão suporte ao provisionamento automático, ao OpenID Connect, Security Assertion Markup Language (SAML) e ao SSO (logon único) com senha.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plano de alteração: remoção do endereço IP do servidor MFA do endereço IP do Office 365

**Tipo:** Planejar alteração  
**Categoria de serviço:** FATO  
**Funcionalidade do produto:** Proteção de & de segurança de identidade

Estamos removendo o endereço IP do servidor MFA do [endereço IP do Office 365 e do serviço Web URL](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Se, no momento, você confiar nessas páginas para atualizar as configurações do firewall, deverá ter certeza de que também está incluindo a lista de endereços IP documentados na seção **requisitos do firewall do Azure servidor de autenticação multifator** de [introdução com o artigo Servidor de Autenticação Multifator do Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Tokens somente de aplicativo agora exigem que o aplicativo cliente exista no locatário do recurso

**Tipo:** Fixado  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário

Em 26 de julho de 2019, alteramos a maneira como fornecemos tokens somente de aplicativo por meio da [concessão de credenciais de cliente](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Anteriormente, os aplicativos podiam obter tokens para chamar outros aplicativos, independentemente se o aplicativo cliente estava no locatário. Atualizamos esse comportamento para que recursos de locatário único, às vezes chamados de APIs da Web, só possam ser chamados por aplicativos cliente que existem no locatário do recurso.

Se seu aplicativo não estiver localizado no locatário de recursos, você receberá uma mensagem de erro que diz, `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` para corrigir esse problema, você deve criar a entidade de serviço de aplicativo cliente no locatário, usando o [ponto de extremidade de consentimento do administrador](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) ou [por meio do PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), que garante que seu locatário tenha dado a permissão do aplicativo para operar dentro do locatário.

Para obter mais informações, consulte [o que há de novo para autenticação?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> O consentimento existente entre o cliente e a API continua a não ser necessário. Os aplicativos ainda devem estar fazendo suas próprias verificações de autorização.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nova entrada sem senha no Azure AD usando as chaves de segurança do FIDO2

**Tipo:** Novo recurso  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário

Os clientes do Azure AD agora podem definir políticas para gerenciar chaves de segurança FIDO2 para os usuários e grupos de sua organização. Os usuários finais também podem registrar suas chaves de segurança automaticamente, usar as chaves para entrar em suas contas da Microsoft em sites, enquanto em dispositivos com capacidade de FIDO, bem como entrar em seus dispositivos Windows 10 ingressados no Azure AD.

Para obter mais informações, consulte [Habilitar entrada sem senha para o Azure AD (versão prévia)](/azure/active-directory/authentication/concept-authentication-passwordless) para obter informações relacionadas ao administrador e configurar informações de [segurança para usar uma chave de segurança (versão prévia)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) para informações relacionadas ao usuário final.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – julho de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em julho de 2019, adicionamos esses 18 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[Ungerboeck software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [Omnichannel de padrão brilhante, centro de contatos](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [Nelly inteligente](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [acesso de SSO do MS Azure para conformidade com o Office™, a](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso) [atenção](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [ Abstrato](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [ascentl](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [hiperanna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [pharmingid WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog artefato](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o provisionamento de conta de usuário para esses aplicativos SaaS recentemente suportados

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Monitoramento & relatórios

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

**Tipo:** Novo recurso  
**Categoria de serviço:** Azure AD Domain Services  
**Funcionalidade do produto:** Azure AD Domain Services

Se estiver cansado de gerenciar listas longas de endereços IP e intervalos, você poderá usar a nova marca de serviço de rede **AzureActiveDirectoryDomainServices** em seu grupo de segurança de rede do Azure para ajudar a proteger o tráfego de entrada para seu Azure AD Domain Services virtual sub-rede.

Para obter mais informações sobre essa nova marca de serviço, consulte [grupos de segurança de rede para Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Novas auditorias de segurança para Azure AD Domain Services (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Azure AD Domain Services  
**Funcionalidade do produto:** Azure AD Domain Services

Temos o prazer de anunciar o lançamento da auditoria de segurança do serviço de domínio do Azure AD para a visualização pública. A auditoria de segurança ajuda a fornecer informações críticas sobre os serviços de autenticação transmitindo eventos de auditoria de segurança para os recursos de destino, incluindo o armazenamento do Azure, os espaços de trabalho do Azure Log Analytics e o Hub de eventos do Azure, usando o serviço de domínio do Azure AD Portal.

Para obter mais informações, consulte [habilitar auditorias de segurança para Azure AD Domain Services (versão prévia)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Novos métodos de autenticação uso & insights (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Redefinição de senha de autoatendimento  
**Funcionalidade do produto:** Monitoramento & relatórios

Os novos métodos de autenticação de uso & relatórios do insights podem ajudá-lo a entender como os recursos como a autenticação multifator do Azure e a redefinição de senha de autoatendimento estão sendo registrados e usados em sua organização, incluindo o número de registrado os usuários para cada recurso, com que frequência a redefinição de senha de autoatendimento é usada para redefinir senhas e por qual método a redefinição ocorre.

Para obter mais informações, consulte [métodos de autenticação uso & insights (versão prévia)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Novos relatórios de segurança estão disponíveis para todos os administradores do Azure AD (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** Proteção de & de segurança de identidade

Todos os administradores do Azure AD agora podem selecionar a faixa na parte superior dos relatórios de segurança existentes, como o relatório **usuários sinalizados para risco** , para começar a usar a nova experiência de segurança, conforme mostrado nos relatórios de **usuários arriscados** e de **entradas arriscadas** . Ao longo do tempo, todos os relatórios de segurança serão movidos das versões mais antigas para as novas versões, com os novos relatórios, fornecendo os seguintes recursos adicionais:

- Filtragem e classificação avançadas

- Ações em massa, como ignorar o risco do usuário

- Confirmação de entidades comprometidas ou seguras

- Estado de risco, abrangendo: em risco, ignorado, remediado e confirmado comprometido

Para obter mais informações, consulte relatório de [usuários arriscados](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report) e [registro de entradas arriscadas](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Novas auditorias de segurança para Azure AD Domain Services (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Azure AD Domain Services  
**Funcionalidade do produto:** Azure AD Domain Services

Temos o prazer de anunciar o lançamento da auditoria de segurança do serviço de domínio do Azure AD para a visualização pública. A auditoria de segurança ajuda a fornecer informações críticas sobre os serviços de autenticação transmitindo eventos de auditoria de segurança para os recursos de destino, incluindo o armazenamento do Azure, os espaços de trabalho do Azure Log Analytics e o Hub de eventos do Azure, usando o serviço de domínio do Azure AD Portal.

Para obter mais informações, consulte [habilitar auditorias de segurança para Azure AD Domain Services (versão prévia)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nova Federação B2B direta usando SAML/WS-enalimentado (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C

A Federação direta ajuda a facilitar o trabalho com parceiros cuja solução de identidade gerenciada por ti não é o AD do Azure, trabalhando com sistemas de identidade que dão suporte aos padrões SAML ou WS-enalimentados. Depois de configurar uma relação de Federação direta com um parceiro, qualquer usuário convidado novo que você convidar desse domínio poderá colaborar com você usando sua conta institucional existente, tornando a experiência do usuário para seus convidados mais direta.

Para obter mais informações, consulte [Federação direta com AD FS e provedores de terceiros para usuários convidados (visualização)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o provisionamento de conta de usuário para esses aplicativos SaaS recentemente suportados

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Monitoramento & relatórios

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

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerenciamento de grupo  
**Funcionalidade do produto:** Colaboração

Agora, quando você criar ou atualizar um nome de grupo no portal do Azure AD, realizaremos uma verificação para ver se você está duplicando um nome de grupo existente em seu recurso. Se determinarmos que o nome já está em uso por outro grupo, você será solicitado a modificar seu nome.

Para obter mais informações, consulte [gerenciar grupos no portal do Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>O Azure AD agora dá suporte a parâmetros de consulta estáticos em URIs de resposta (redirecionamento)

**Tipo:** Novo recurso  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário

Os aplicativos do Azure AD agora podem registrar e usar URIs de resposta (redirecionamento) com parâmetros de consulta estáticos (por exemplo, `https://contoso.com/oauth2?idp=microsoft`) para solicitações do OAuth 2,0. O parâmetro de consulta estática está sujeito à correspondência de cadeia de caracteres para URIs de resposta, assim como qualquer outra parte do URI de resposta. Se não houver nenhuma cadeia de caracteres registrada que corresponda ao redirecionamento de URL decodificado-URI, a solicitação será rejeitada. Se o URI de resposta for encontrado, a cadeia de caracteres inteira será usada para redirecionar o usuário, incluindo o parâmetro de consulta estática.

Os URIs de resposta dinâmica ainda são proibidos porque representam um risco de segurança e não podem ser usados para reter informações de estado em uma solicitação de autenticação. Para essa finalidade, use o parâmetro `state`.

Atualmente, as telas de registro do aplicativo do portal do Azure ainda bloqueiam parâmetros de consulta. No entanto, você pode editar manualmente o manifesto do aplicativo para adicionar e testar parâmetros de consulta em seu aplicativo. Para obter mais informações, consulte [o que há de novo para autenticação?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Os logs de atividade (APIs do MS Graph) para o Azure AD agora estão disponíveis por meio de cmdlets do PowerShell

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios

Estamos empolgados em anunciar que os logs de atividades do Azure AD (relatórios de auditoria e de entradas) agora estão disponíveis por meio do módulo do PowerShell do Azure AD. Anteriormente, você poderia criar seus próprios scripts usando os pontos de extremidade do MS API do Graph e agora estendemos esse recurso aos cmdlets do PowerShell.

Para obter mais informações sobre como usar esses cmdlets, consulte [cmdlets do PowerShell do Azure ad para relatórios](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Controles de filtro atualizados para logs de auditoria e de entrada no Azure AD

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios

Atualizamos os relatórios de auditoria e log de entrada para que você possa aplicar vários filtros sem precisar adicioná-los como colunas nas telas de relatório. Além disso, agora você pode decidir quantos filtros deseja mostrar na tela. Todas as atualizações funcionam em conjunto para facilitar a leitura e o escopo de seus relatórios de forma a suas necessidades.

Para obter mais informações sobre essas atualizações, consulte [Filtrar logs de auditoria](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) e [Filtrar atividades de entrada](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Junho de 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nova API do riskDetections para Microsoft Graph (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** Proteção de & de segurança de identidade

Temos o prazer de anunciar a nova API do riskDetections para Microsoft Graph está agora em visualização pública. Você pode usar essa nova API para exibir uma lista de usuários relacionados à proteção de identidade da sua organização e as detecções de risco de entrada. Você também pode usar essa API para consultar com mais eficiência suas detecções de risco, incluindo detalhes sobre o tipo de detecção, status, nível e muito mais.

Para obter mais informações, consulte a [documentação de referência de API de detecção de riscos](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – junho de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em junho de 2019, adicionamos esses 22 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[Kit de ferramentas de SAML do Azure ad](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [anágua](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [cliente de VPN do Azure](https://portal.azure.com/), [despesa](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial) [, auxiliar auxiliar,](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial) [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz no carro](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Console de infraestrutura de nuvem da Oracle](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [autenticação SAML CyberArk](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise os](https://proptimise.co.uk/software/), [vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager para Oracle Merchandising de varejo, Oracle Access Manager para Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o provisionamento de conta de usuário para esses aplicativos SaaS recentemente suportados

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Monitoramento & relatórios

Agora você pode automatizar a criação, atualização e exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Aplicar](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor sua organização usando o provisionamento automatizado de contas de usuário, confira automatizar o [provisionamento de usuários para aplicativos SaaS com o Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Exibir o progresso em tempo real do serviço de provisionamento do Azure AD

**Tipo:** Recurso alterado  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade

Atualizamos a experiência de provisionamento do Azure AD para incluir uma nova barra de progresso que mostre a você o quanto você está no processo de provisionamento do usuário. Essa experiência atualizada também fornece informações sobre o número de usuários provisionados durante o ciclo atual, bem como quantos usuários foram provisionados até o momento.

Para obter mais informações, consulte [verificar o status do provisionamento do usuário](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>A identidade visual da empresa agora aparece nas telas sair e erro

**Tipo:** Recurso alterado  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário

Atualizamos o Azure AD para que a identidade visual da sua empresa agora apareça nas telas de saída e erro, bem como na página de entrada. Você não precisa fazer nada para ativar esse recurso, o AD do Azure simplesmente usa os ativos que você já configurou na área de **identidade visual da empresa** do portal do Azure.

Para obter mais informações sobre como configurar a identidade visual da sua empresa, consulte [Adicionar identidade visual às páginas de Azure Active Directory da sua organização](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>O servidor de autenticação multifator (MFA) do Azure não está mais disponível para novas implantações

**Tipo:** Preterido  
**Categoria de serviço:** FATO  
**Funcionalidade do produto:** Proteção de & de segurança de identidade

A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o servidor MFA para novas implantações. Os novos clientes que desejam exigir a autenticação multifator em sua organização agora devem usar a autenticação multifator do Azure baseada em nuvem. Os clientes que ativaram o servidor MFA antes de 1º de julho não verão uma alteração. Você ainda poderá baixar a versão mais recente, obter atualizações futuras e gerar credenciais de ativação.

Para obter mais informações, consulte [introdução ao servidor de autenticação multifator do Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Para obter mais informações sobre a autenticação multifator do Azure baseada em nuvem, consulte [planejando uma implantação da autenticação multifator do Azure baseada em nuvem](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Maio de 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Alteração de serviço: suporte futuro somente para protocolos TLS 1,2 no serviço de proxy de aplicativo

**Tipo:** Planejar alteração  
**Categoria de serviço:** Proxy de aplicativo  
**Funcionalidade do produto:** Controle de acesso

Para ajudar a fornecer a melhor criptografia para nossos clientes, estamos limitando o acesso somente a protocolos TLS 1,2 no serviço de proxy de aplicativo. Essa alteração é gradualmente distribuída para clientes que já usam protocolos TLS 1,2, portanto, você não deve ver nenhuma alteração.

A substituição do TLS 1,0 e do TLS 1,1 ocorre em 31 de agosto de 2019, mas forneceremos um aviso avançado adicional, portanto, você terá tempo para se preparar para essa alteração. Para se preparar para essa alteração, verifique se as combinações de servidor cliente e navegador-servidor, incluindo os clientes que os usuários usam para acessar os aplicativos publicados por meio do proxy de aplicativo, foram atualizadas para usar o protocolo TLS 1,2 para manter a conexão com o aplicativo Serviço de proxy. Para obter mais informações, consulte [Adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Usar o relatório de uso e informações para exibir os dados de entrada relacionados ao aplicativo

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Monitoramento & relatórios

Agora você pode usar o relatório de uso e informações, localizado na área **aplicativos empresariais** do portal do Azure, para obter uma exibição centrada no aplicativo de seus dados de entrada, incluindo informações sobre:

- Principais aplicativos usados para sua organização

- Aplicativos com as entradas mais malsucedidas

- Principais erros de entrada para cada aplicativo

Para obter mais informações sobre esse recurso, consulte [relatório de informações e uso no portal de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizar seu provisionamento de usuário para aplicativos de nuvem usando o Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Monitoramento & relatórios

Siga estes novos tutoriais para usar o serviço de provisionamento do Azure AD para automatizar a criação, a exclusão e a atualização de contas de usuário para os seguintes aplicativos baseados em nuvem:

- [Coencontro](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Você também pode seguir este novo [tutorial do Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), que fornece informações sobre como provisionar objetos de grupo.

Para obter mais informações sobre como proteger melhor sua organização por meio do provisionamento automatizado de contas de usuário, confira automatizar o [provisionamento de usuário para aplicativos SaaS com o Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>A pontuação de segurança de identidade agora está disponível no Azure AD (disponibilidade geral)

**Tipo:** Novo recurso  
**Categoria de serviço:** N/A  
**Funcionalidade do produto:** Proteção de & de segurança de identidade

Agora você pode monitorar e melhorar sua postura de segurança de identidade usando o recurso identidade de Pontuação segura no Azure AD. O recurso de Pontuação segura de identidade usa um único painel para ajudá-lo:

- Meça objetivamente sua postura de segurança de identidade, com base em uma pontuação entre 1 e 223.

- Planejar suas melhorias de segurança de identidade

- Examine o sucesso de suas melhorias de segurança

Para obter mais informações sobre o recurso de Pontuação de segurança de identidade, consulte [o que é a pontuação segura de identidade em Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nova experiência de Registros de aplicativo agora está disponível (disponibilidade geral)

**Tipo:** Novo recurso  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Experiência do desenvolvedor

A nova experiência de [registros de aplicativo](https://aka.ms/appregistrations) agora está em disponibilidade geral. Essa nova experiência inclui todos os principais recursos com os quais você está familiarizado a partir da portal do Azure e do portal de registro de aplicativos e o melhora por meio de:

- **Melhor gerenciamento de aplicativos.** Em vez de ver seus aplicativos em portais diferentes, agora você pode ver todos os seus aplicativos em um único local.

- **Registro de aplicativo simplificado.** Da melhor experiência de navegação à experiência de seleção de permissão remodelada, agora é mais fácil registrar e gerenciar seus aplicativos.

- **Informações mais detalhadas.** Você pode encontrar mais detalhes sobre seu aplicativo, incluindo guias de início rápido e muito mais.

Para obter mais informações, consulte [plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) e a experiência de [registros de aplicativo agora está disponível para o público geral!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) comunicado do blog.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Novos recursos disponíveis na API de usuários arriscados para a proteção de identidade

**Tipo:** Novo recurso  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** Proteção de & de segurança de identidade

Temos o prazer de anunciar que agora você pode usar a API de usuários arriscados para recuperar o histórico de riscos dos usuários, ignorar usuários arriscados e confirmar os usuários como comprometidos. Essa alteração ajuda a atualizar com mais eficiência o status de risco de seus usuários e a entender seu histórico de riscos.

Para obter mais informações, consulte a [documentação de referência da API de usuários arriscados](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – maio de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em maio de 2019, adicionamos esses 21 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [links reais](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Sign-simples](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [displayer](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [envolvimento de vendas do marketo](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [subsistemas](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum Local de trabalho](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [WebMethods API](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [controle](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [alimento](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Experiência aprimorada de criação e gerenciamento de grupos no portal do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerenciamento de grupo  
**Funcionalidade do produto:** Colaboração

Fizemos melhorias nas experiências relacionadas a grupos no portal do Azure AD. Esses aprimoramentos permitem que os administradores gerenciem melhor listas de grupos, listas de membros e forneçam opções de criação adicionais.

Os aprimoramentos incluem:

- Filtragem básica por tipo de associação e tipo de grupo.

- Adição de novas colunas, como a origem e o endereço de email.

- Capacidade de selecionar vários grupos, membros e listas de proprietários para fácil exclusão.

- Capacidade de escolher um endereço de email e adicionar proprietários durante a criação do grupo.

Para obter mais informações, consulte [criar um grupo básico e adicionar membros usando Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Configurar uma política de nomenclatura para grupos do Office 365 no portal do AD do Azure (disponibilidade geral)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerenciamento de grupo  
**Funcionalidade do produto:** Colaboração

Agora, os administradores podem configurar uma política de nomenclatura para grupos do Office 365 usando o portal do AD do Azure. Essa alteração ajuda a impor convenções de nomenclatura consistentes para grupos do Office 365 criados ou editados por usuários em sua organização.

Você pode configurar a política de nomenclatura para grupos do Office 365 de duas maneiras diferentes:

- Defina prefixos ou sufixos, que são adicionados automaticamente a um nome de grupo.

- Carregue um conjunto personalizado de palavras bloqueadas para sua organização, que não são permitidas em nomes de grupos (por exemplo, "CEO, Payroll, HR").

Para obter mais informações, consulte [impor uma política de nomenclatura para grupos do Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Os pontos de extremidade de API Microsoft Graph agora estão disponíveis para logs de atividade do Azure AD (disponibilidade geral)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios

Estamos felizes em anunciar a disponibilidade geral do suporte aos pontos de extremidade da API de Microsoft Graph para os logs de atividades do Azure AD. Com esta versão, agora você pode usar a versão 1,0 dos logs de auditoria do Azure AD, bem como as APIs de logs de entrada.

Para obter mais informações, consulte [visão geral da API de log de auditoria do Azure ad](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Os administradores agora podem usar o acesso condicional para o processo de registro combinado (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** Proteção de & de segurança de identidade  

Agora, os administradores podem criar políticas de acesso condicional para uso pela página de registro combinada. Isso inclui a aplicação de políticas para permitir o registro se:

- Os usuários estão em uma rede confiável.

- Os usuários são um risco de entrada baixo.

- Os usuários estão em um dispositivo gerenciado.

- Os usuários concordam com os termos de uso da organização (TOU).

Para obter mais informações sobre o acesso condicional e a redefinição de senha, você pode ver o [acesso condicional para a publicação de blog de MFA combinada do Azure AD e a experiência de registro de redefinição de senha](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Para obter mais informações sobre políticas de acesso condicional para o processo de registro combinado, consulte [políticas de acesso condicional para registro combinado](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Para obter mais informações sobre o recurso de termos de uso do Azure AD, consulte [Azure Active Directory recurso termos de uso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Abril de 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>A nova detecção de inteligência contra ameaças do Azure AD agora está disponível como parte do Azure AD Identity Protection

**Tipo:** Novo recurso  
**Categoria de serviço:** Azure AD Identity Protection  
**Funcionalidade do produto:** Proteção de & de segurança de identidade

A detecção de inteligência contra ameaças do Azure AD agora está disponível como parte do recurso de Azure AD Identity Protection atualizado. Essa nova funcionalidade ajuda a indicar atividade de usuário incomum para um usuário ou atividade específica que é consistente com padrões de ataque conhecidos com base nas fontes de inteligência contra ameaças internas e externas da Microsoft.

Para obter mais informações sobre a versão atualizada do Azure AD Identity Protection, consulte os [quatro principais aprimoramentos de Azure ad Identity Protection agora estão no blog de visualização pública](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) e o [que é Azure Active Directory Identity Protection (atualizado)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) artigo. Para obter mais informações sobre a detecção de inteligência contra ameaças do Azure AD, consulte o artigo [Azure Active Directory Identity Protection de detecções de risco](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>O gerenciamento de direitos do Azure AD agora está disponível (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Governança de identidade  
**Funcionalidade do produto:** Governança de identidade

O gerenciamento de direitos do Azure AD, agora em visualização pública, ajuda os clientes a delegar o gerenciamento de pacotes de acesso, que define como os funcionários e parceiros de negócios podem solicitar acesso, quem deve aprovar e por quanto tempo eles têm acesso. Os pacotes do Access podem gerenciar a associação nos grupos do Azure AD e do Office 365, atribuições de função em aplicativos empresariais e atribuições de função para sites do SharePoint Online. Leia mais sobre o gerenciamento de direitos na [visão geral do gerenciamento de direitos do Azure ad](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Para saber mais sobre a amplitude dos recursos de Azure AD Identity Governance, incluindo Privileged Identity Management, revisões de acesso e termos de uso, consulte [o que é Azure ad Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configurar uma política de nomenclatura para grupos do Office 365 no portal do Azure AD (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerenciamento de grupo  
**Funcionalidade do produto:** Colaboração

Agora, os administradores podem configurar uma política de nomenclatura para grupos do Office 365 usando o portal do AD do Azure. Essa alteração ajuda a impor convenções de nomenclatura consistentes para grupos do Office 365 criados ou editados por usuários em sua organização.

Você pode configurar a política de nomenclatura para grupos do Office 365 de duas maneiras diferentes:

- Defina prefixos ou sufixos, que são adicionados automaticamente a um nome de grupo.

- Carregue um conjunto personalizado de palavras bloqueadas para sua organização, que não são permitidas em nomes de grupos (por exemplo, "CEO, Payroll, HR").

Para obter mais informações, consulte [impor uma política de nomenclatura para grupos do Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Os logs de atividade do Azure AD agora estão disponíveis no Azure Monitor (disponibilidade geral)

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios

Para ajudar a resolver seus comentários sobre visualizações com os logs de atividades do Azure AD, estamos introduzindo um novo recurso de informações no Log Analytics. Esse recurso ajuda você a obter informações sobre seus recursos do Azure AD usando nossos modelos interativos, chamados de pastas de trabalho. Essas pastas de trabalho predefinidas podem fornecer detalhes para aplicativos ou usuários e incluem:

- **Entradas.** Fornece detalhes para aplicativos e usuários, incluindo o local de entrada, o sistema operacional em uso ou o cliente e a versão do navegador e o número de entradas com êxito ou com falha.

- **Autenticação herdada e acesso condicional.** Fornece detalhes para aplicativos e usuários usando a autenticação herdada, incluindo o uso da autenticação multifator disparado pelas políticas de acesso condicional, aplicativos que usam políticas de acesso condicional e assim por diante.

- **Análise de falha de entrada.** Ajuda a determinar se os erros de entrada estão ocorrendo devido a uma ação do usuário, a problemas de política ou à sua infraestrutura.

- **Relatórios personalizados.** Você pode criar novas ou editar pastas de trabalho existentes para ajudar a personalizar o recurso de informações para sua organização.

Para obter mais informações, consulte [como usar pastas de trabalho do Azure monitor para relatórios Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – abril de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em abril de 2019, adicionamos esses 21 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks logon único](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix Netscaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [bancada](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect ](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [alibaba Cloud (SSO baseado em função)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent gerenciamento de patrimônio](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), Workgrid [, Monday.com,](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial) [](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial) [Surveymonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nova opção de frequência de revisões de acesso e seleção de várias funções

**Tipo:** Novo recurso  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Governança de identidade

Novas atualizações nas revisões de acesso do Azure AD permitem que você:

- Altere a frequência de suas revisões de acesso para **semianualmente**, além das opções de semana, mensal, trimestral e anual existentes anteriormente.

- Selecione várias funções do Azure AD e recursos do Azure ao criar uma revisão de acesso único. Nessa situação, todas as funções são configuradas com as mesmas configurações e todos os revisores são notificados ao mesmo tempo.

Para obter mais informações sobre como criar uma revisão de acesso, consulte [criar uma revisão de acesso de grupos ou aplicativos nas revisões de acesso do Azure ad](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect sistema (es) de alertas de email estão em transição, enviando novas informações de remetente de email para alguns clientes

**Tipo:** Recurso alterado  
**Categoria de serviço:** AD Sync  
**Funcionalidade do produto:** Plataforma

Azure AD Connect está no processo de transição de nossos sistemas de alerta de email, potencialmente mostrando a alguns clientes um novo remetente de email. Para resolver isso, você deve adicionar `azure-noreply@microsoft.com` à lista de permissões da sua organização ou não poderá continuar recebendo alertas importantes do seu Office 365, do Azure ou dos serviços de sincronização.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>As alterações de sufixo UPN agora são bem-sucedidas entre domínios federados no Azure AD Connect

**Tipo:** Fixado  
**Categoria de serviço:** AD Sync  
**Funcionalidade do produto:** Plataforma

Agora você pode alterar com êxito o sufixo UPN do usuário de um domínio federado para outro domínio federado em Azure AD Connect. Essa correção significa que você não deve mais experimentar a mensagem de erro FederatedDomainChangeError durante o ciclo de sincronização ou receber um email de notificação informando "não é possível atualizar este objeto no Azure Active Directory, pois o atributo [ FederatedUser. UserPrincipalName], não é válido. Atualize o valor em seus serviços de diretório local ".

Para obter mais informações, consulte [Solucionando erros durante a sincronização](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Maior segurança usando a política de acesso condicional com base na proteção do aplicativo no Azure AD (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** Proteção de & de segurança de identidade

O acesso condicional baseado em proteção de aplicativo agora está disponível usando a política de **proteção de aplicativo necessária** . Essa nova política ajuda a aumentar a segurança da sua organização, ajudando a evitar:

- Usuários que obtêm acesso a aplicativos sem uma licença de Microsoft Intune.

- Os usuários não conseguem obter uma política de proteção de aplicativo Microsoft Intune.

- Usuários que obtêm acesso a aplicativos sem uma política de proteção de aplicativo Microsoft Intune configurada.

Para obter mais informações, consulte [como exigir a política de proteção de aplicativo para acesso de aplicativo de nuvem com acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Novo suporte para logon único do Azure AD e acesso condicional no Microsoft Edge (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** Proteção de & de segurança de identidade

Aprimoramos nosso suporte do Azure AD para o Microsoft Edge, incluindo o fornecimento de novo suporte para logon único do Azure AD e acesso condicional. Se você já usou Microsoft Intune Managed Browser, agora você pode usar o Microsoft Edge em vez disso.

Para obter mais informações sobre como configurar e gerenciar seus dispositivos e aplicativos usando o acesso condicional, consulte [exigir dispositivos gerenciados para acesso ao aplicativo de nuvem com acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) e [exigir aplicativos cliente aprovados para acesso ao aplicativo de nuvem com acesso condicional ](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Para obter mais informações sobre como gerenciar o acesso usando o Microsoft Edge com políticas de Microsoft Intune, consulte [gerenciar o acesso à Internet usando um navegador Microsoft Intune protegido por política](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---
