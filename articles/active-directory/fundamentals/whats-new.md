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
ms.date: 09/03/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ff942cdad74c3b8b71a8f1658f13faae021b983
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89567457"
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

Para saber mais, consulte [permissões de acesso restritos ao hóspede](../users-groups-roles/users-restrict-guest-permissions.md) e [permissões por defeito dos utilizadores.](./users-default-permissions.md)
 
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

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Disponibilidade Geral de AD AD AD Meus Ins

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Experiências de utilizador final
 
Azure AD My Sign-Ins é uma nova funcionalidade que permite aos utilizadores da empresa rever o seu histórico de inscrições para verificar em qualquer atividade incomum. Além disso, esta funcionalidade permite que os utilizadores finais reportem "This was not me" ou "This was me" em atividades suspeitas. Para saber mais sobre a utilização desta funcionalidade, consulte [a Visualização e pesque a sua recente atividade de início de sposição na página My's Ins](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
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

Agora pode atribuir funções incorporadas a Azure a grupos de nuvem com esta nova funcionalidade. Por exemplo, pode atribuir a função de Administrador SharePoint a Contoso_SharePoint_Admins grupo. Também pode usar o PIM para fazer do grupo um membro elegível do papel, em vez de conceder acesso permanente. Para aprender a configurar esta funcionalidade, consulte [Use grupos de nuvem para gerir atribuições de funções no Azure Ative Directory (pré-visualização)](../users-groups-roles/roles-groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Insights Business Leader papel incorporado agora disponível

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Funções de AD AZure  
**Capacidade do produto:** Controlo de Acessos
 
Os utilizadores na função Insights Business Leader podem aceder a um conjunto de dashboards e insights através da [aplicação M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Isto inclui acesso total a todos os dashboards e apresenta insights e funcionalidade de exploração de dados. No entanto, os utilizadores nesta função não têm acesso às definições de configuração do produto, que é da responsabilidade do administrador do Insights. Para saber mais sobre este papel, consulte [permissões de função de administrador no Azure Ative Directory](../users-groups-roles/directory-assign-admin-roles.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Papel incorporado do Administrador de Insights já disponível

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Funções de AD AZure  
**Capacidade do produto:** Controlo de Acessos
 
Os utilizadores na função de Administrador de Insights podem aceder a todo o conjunto de capacidades administrativas na [aplicação M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Um utilizador nesta função pode ler informações de diretórios, monitorizar a saúde do serviço, bilhetes de suporte de ficheiros e aceder aos aspetos de definição do administrador do Insights. Para saber mais sobre este papel, consulte [permissões de função de administrador no Azure Ative Directory](../users-groups-roles/directory-assign-admin-roles.md#insights-administrator)
 
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

Ao criar uma nova política, certifique-se de excluir os utilizadores e contas de serviço que ainda utilizam a autenticação antiga; Se não o fizeres, eles vão estar bloqueados. [Saiba mais](https://aka.ms/caclientapps).
 
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

Os administradores podem agora verificar se uma autenticação do Windows usou o Windows Hello for Business, verificando o separador Detalhes de Autenticação de um evento de entrada no Windows na lâmina AD AD do portal Azure. As autenticações do Windows Hello for Business incluirão "WindowsHelloForBusiness" no campo Método de Autenticação. Para obter mais informações sobre a interpretação de Registos de Inscrição, consulte a [documentação de Registos de Início de Sessão](../reports-monitoring/concept-sign-ins.md).
 
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

[Palmas](http://www.rmit.com.ar/)Palmas , [Appreiz](https://microsoftteams.appreiz.com/), [Inextor Vault](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect,](https://app.templafy.com/) [Rececionista PeterConnects,](https://msteams.peterconnects.com/) [AlohaCloud,](https://appfusions.alohacloud.com/auth) [Control Tower,](https://bpm.tnxcorp.com/sso/microsoft) [Cocoom,](https://start.cocoom.com/) [COINS Construction Cloud,](https://sso.coinsconstructioncloud.com/#login/) [Medxnote MT,](https://task.teamsmain.medx.im/authorization) [Reflekt,](https://reflekt.konsolute.com/login) [Rever,](https://app.reverscore.net/access) [MyCompanyArchive,](https://login.mycompanyarchive.com/) [GRem sines,](https://app.greminders.com/o365-oauth) [Titanfile](../saas-apps/titanfile-tutorial.md), [Wootric](../saas-apps/wootric-tutorial.md), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US),  [OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md), [Datasite](../saas-apps/datasite-tutorial.md), [BlogIn](../saas-apps/blogin-tutorial.md), [IntSights](../saas-apps/intsights-tutorial.md), [kpifire,](../saas-apps/kpifire-tutorial.md) [Textline](../saas-apps/textline-tutorial.md), [Cloud Academy - SSO,](../saas-apps/cloud-academy-sso-tutorial.md) [Community Spark,](../saas-apps/community-spark-tutorial.md) [Chatwork,](../saas-apps/chatwork-tutorial.md) [CloudSign](../saas-apps/cloudsign-tutorial.md), [C3M Cloud Control,](../saas-apps/c3m-cloud-control-tutorial.md) [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Michigan Data Hub Single Sign-On](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [Egress](../saas-apps/egress-tutorial.md), [SendSafe,](../saas-apps/sendsafely-tutorial.md) [Eletive,](https://app.eletive.com/) [Right-Hand Cybersecurity ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [Verme](../saas-apps/verme-tutorial.md), [Lenses.io](../saas-apps/lensesio-tutorial.md), [Momenta](../saas-apps/momenta-tutorial.md), [Uprise](https://app.uprise.co/sign-in), [Q,](https://q.moduleq.com/login) [CloudCords](../saas-apps/cloudcords-tutorial.md), [TellMe Bot](https://tellme365liteweb.azurewebsites.net/), [Inspire](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML Connector,](https://www.strata.io/identity-fabric/) [Smartschool (Sistema de Gestão Escolar)](https://smart-schoolapp.com/frmLoginForm), [Zepto - Cronometragem Inteligente,](https://user.zepto-ai.com/signin) [Studi.ly,](https://studi.ly/) [Trackplan,](http://www.trackplanfm.com/) [Skedda,](../saas-apps/skedda-tutorial.md) [WhosOnLocation,](../saas-apps/whos-on-location-tutorial.md) [Coggle,](../saas-apps/coggle-tutorial.md) [Kemp LoadMaster,](https://kemptechnologies.com/cloud-load-balancer/) [BrowserStack Single Sign-on](../saas-apps/browserstack-single-sign-on-tutorial.md)

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
 
Agora pode ver atribuições de funções em todos os âmbitos para um papel no separador "Papéis e administradores" no portal AD AZure. Também pode descarregar essas atribuições de funções para cada papel num ficheiro CSV. Para obter orientações sobre a visualização e adição de atribuições de funções, consulte [Ver e atribuir funções de administrador no Azure Ative Directory](../users-groups-roles/directory-manage-roles-portal.md).
 
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
 
Algumas aplicações SAML exigem que o SPNameQualifier seja devolvido no sujeito de afirmação quando solicitado. Agora, a Azure AD responde corretamente quando um SPNameQualifier é solicitado na política nameID do pedido. Isto também funciona para a sed-in iniciada pela SP, e o IdP iniciou a s inscrição.  Para saber mais sobre o protocolo SAML no Diretório Ativo Azure, consulte [o protocolo SINGLE Sign-On SAML](../develop/single-sign-on-saml-protocol.md).

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

As etiquetas de sensibilidade são importantes para proteger os seus dados críticos do negócio e permitir-lhe gerir grupos em escala, de forma compatível e segura. Para obter orientações sobre a utilização de etiquetas de sensibilidade, consulte [os rótulos de sensibilidade atribuir aos grupos Microsoft 365 no Azure Ative Directory (pré-visualização)](../users-groups-roles/groups-assign-sensitivity-labels.md).
 
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
 

Atualmente, os utilizadores que estão em domínios federados em AZure AD, mas que não estão sincronizados com o inquilino, não podem aceder às Equipas. A partir do final de junho, esta nova capacidade irá permitir-lhes fazê-lo, alargando a funcionalidade de inscrição verificada por e-mail existente. Isto permitirá aos utilizadores que possam iniciar sposição num IdP federado, mas que ainda não tenham um objeto de utilizador no ID Azure, tenham um objeto de utilizador criado automaticamente e sejam autenticados para equipas. O objeto do utilizador será marcado como "self-service sign up". Esta é uma extensão da capacidade existente para fazer auto-inscrição verificada por e-mail que os utilizadores em domínios geridos podem fazer e podem ser controlados usando a mesma bandeira. Esta alteração completa durante os dois meses seguintes. Atenção às atualizações de documentação [aqui.](../users-groups-roles/directory-self-service-signup.md)
 
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
 
Os administradores de TI podem começar a usar o novo papel de "Hybrid Admin" como o papel menos privilegiado para a criação do Azure ADConnect Cloud Provisioning. Com este novo papel, já não tens de usar o papel da Administração Global para configurar e configurar o Cloud Provisioning. [Saiba mais](../users-groups-roles/roles-delegate-by-task.md#connect).
 
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
 
Melhorámos a aplicação de provisionamento workday Writeback para agora suportar a redução do número de telefone de trabalho e dos atributos do número de telemóvel. Além do e-mail e nome de utilizador, pode agora configurar a app de provisionamento de writeback workday para fluir os valores do número de telefone de Azure AD para Workday. Para obter mais detalhes sobre como configurar a gravação do número de telefone, consulte o tutorial da aplicação [Workday Writeback.](https://aka.ms/WorkdayWriteback) 

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

A Avaliação contínua de Acesso é uma nova funcionalidade de segurança que permite a aplicação quase em tempo real de políticas em partes dependentes que consomem Tokens de Acesso AD Azure quando os eventos acontecem em Azure AD (como a eliminação de conta de utilizador). Estamos a lançar esta funcionalidade primeiro para os clientes Teams e Outlook. Para mais detalhes, leia o nosso [blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) e [documentação.](./concept-fundamentals-continuous-access-evaluation.md)

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

[O modo apenas de reporte para acesso condicionado Azure AD](../conditional-access/concept-conditional-access-report-only.md) permite-lhe avaliar o resultado de uma política sem impor controlos de acesso. Pode testar políticas apenas de relatórios em toda a sua organização e compreender o seu impacto antes de as permitir, tornando a implementação mais segura e fácil. Ao longo dos últimos meses, temos visto uma forte adoção do modo apenas de relatório, com mais de 26M utilizadores já no âmbito de uma política apenas de relatórios. Com este anúncio, novas políticas de acesso condicionado Azure AD serão criadas em modo apenas de relatório por padrão. Isto significa que pode monitorizar o impacto das suas políticas a partir do momento em que são criadas. E para aqueles que usam as APIs do MS Graph, também pode [gerir políticas apenas de relatórios programáticas.](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) 

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

[Aplicativos SincroPool](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [Float](../saas-apps/float-tutorial.md), [LMS365](https://lms.365.systems/), [IWT Procurement Suite](../saas-apps/iwt-procurement-suite-tutorial.md), [Lunni](https://lunni.fi/), [EasySSO para Jira](../saas-apps/easysso-for-jira-tutorial.md), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](../saas-apps/meraki-dashboard-tutorial.md), Microsoft [365 Mover,](https://app.mover.io/login) [Speaker Engage](https://speakerengage.com/login.php), [Honestamente,](../saas-apps/honestly-tutorial.md) [Ally,](../saas-apps/ally-tutorial.md) [DutyFlow](https://app.dutyflow.nl/), [AlertMedia,](../saas-apps/alertmedia-tutorial.md) [gr8 People](../saas-apps/gr8-people-tutorial.md), [Pendo](../saas-apps/pendo-tutorial.md), [HighGround](../saas-apps/highground-tutorial.md), [Harmony](../saas-apps/harmony-tutorial.md), [Timetabling Solutions](../saas-apps/timetabling-solutions-tutorial.md), [SynchroNet CLICK](../saas-apps/synchronet-click-tutorial.md), [empower,](https://www.made-in-office.com/en/) [Fortes Change Cloud](../saas-apps/fortes-change-cloud-tutorial.md), [Litmus](../saas-apps/litmus-tutorial.md), [GroupTalk,](https://recorder.grouptalk.com/) [Frontify,](../saas-apps/frontify-tutorial.md) [MongoDB Cloud,](../saas-apps/mongodb-cloud-tutorial.md) [TickitLMS Learn,](../saas-apps/tickitlms-learn-tutorial.md) [COCO,](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/) [Nitro Productivity Suite](../saas-apps/nitro-productivity-suite-tutorial.md) , Trend Micro Web Security [(TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](https://aka.ms/appstutorial) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](https://aka.ms/azureadapprequest)

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Suporte de consulta delta do Microsoft Graph para oAuth2PermissionGrant disponível para visualização pública

**Tipo:** Nova funcionalidade

**Categoria de serviço:** MS Graph

**Capacidade do produto:** Experiência de Desenvolvedor

A consulta delta para oAuth2PermissionGrant está disponível para pré-visualização pública! Agora pode rastrear alterações sem ter de pesquisar continuamente o Microsoft Graph. [Saiba mais.](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Suporte de consulta delta do Microsoft Graph para contacto organizacional geralmente disponível

**Tipo:** Nova funcionalidade

**Categoria de serviço:** MS Graph

**Capacidade do produto:** Experiência de Desenvolvedor

A consulta delta para contactos organizacionais está geralmente disponível! Agora é possível rastrear alterações nas aplicações de produção sem ter de fazer uma pesquisa contínua no Microsoft Graph. Substitua qualquer código existente que poll continuamente orgContact dados por consulta delta para melhorar significativamente o desempenho. [Saiba mais.](/graph/api/orgcontact-delta?tabs=http&view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Suporte de consulta delta do Microsoft Graph para aplicação geralmente disponível

**Tipo:** Nova funcionalidade

**Categoria de serviço:** MS Graph

**Capacidade do produto:** Experiência de Desenvolvedor

A consulta delta para aplicações está geralmente disponível! Agora é possível rastrear alterações nas aplicações de produção sem ter de fazer uma pesquisa contínua no Microsoft Graph. Substitua qualquer código existente que polle continuamente os dados da aplicação por consulta delta para melhorar significativamente o desempenho. [Saiba mais.](/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Suporte de consulta delta do Microsoft Graph para unidades administrativas disponíveis para visualização pública

**Tipo:** Nova funcionalidade

**Categoria de serviço:** MS Graph

**Capacidade do produto:** A consulta Delta da Developer Experience para unidades administrativas está disponível para pré-visualização pública! Agora pode rastrear alterações sem ter de pesquisar continuamente o Microsoft Graph. [Saiba mais.](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Gerir números de telefone de autenticação e muito mais em novas APIs beta do Microsoft Graph

**Tipo:** Nova funcionalidade

**Categoria de serviço:** MS Graph

**Capacidade do produto:** Experiência de Desenvolvedor

Estas APIs são uma ferramenta chave para gerir os métodos de autenticação dos seus utilizadores. Agora pode pré-registar-se programaticamente e gerir os autenticadores utilizados para mFA e redefinição de senha de autosserviço (SSPR). Esta tem sido uma das funcionalidades mais requisitadas nos espaços Azure MFA, SSPR e Microsoft Graph. As novas APIs que lançamos nesta onda dão-lhe a capacidade de:

- Leia, adicione, atualize e remova os telefones de autenticação de um utilizador
- Redefinir a senha de um utilizador
- Ligue e desligue o s-sinal de SMS

Para obter mais informações, consulte [os métodos de autenticação AZure AD API.](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)

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

Para mais informações, consulte [a gestão de unidades administrativas no Diretório Ativo Azure (pré-visualização)](https://aka.ms/AdminUnitsDocs).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Funções incorporadas do administrador de impressora e técnico de impressora

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Funções de AD AZure

**Capacidade do produto:** Controlo de Acessos

**Administrador de impressora**: Os utilizadores com esta função podem registar impressoras e gerir todos os aspetos de todas as configurações da impressora na solução De Impressão Universal da Microsoft, incluindo as definições do Conector de Impressão Universal. Podem consentir com todos os pedidos de autorização de impressão delegados. Os administradores da impressora também têm acesso a relatórios de impressão. 

**Técnico de impressora**: Os utilizadores com esta função podem registar impressoras e gerir o estado da impressora na solução de Impressão Universal da Microsoft. Também podem ler todas as informações do conector. As tarefas-chave que um Técnico de Impressora não pode fazer são definir permissões do utilizador em impressoras e partilhar impressoras. [Saiba mais.](../users-groups-roles/directory-assign-admin-roles.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Papel integrado no administrador de identidade híbrida

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Funções de AD AZure

**Capacidade do produto:** Controlo de Acessos

Os utilizadores desta função podem permitir, configurar e gerir serviços e configurações relacionadas com a ativação da identidade híbrida em AZure AD. Esta função confere a possibilidade de configurar a Azure AD a um dos três métodos de autenticação suportados&#8212;sincronização de hash password (PHS), autenticação pass-through (PTA) ou Federação (AD FS ou provedor de federação de terceiros)&#8212;e implantar infraestruturas relacionadas nas instalações para os permitir. As infraestruturas no local incluem agentes de provisionamento e de PTA. Esta função garante a capacidade de permitir um Único Sign-On (S-SSO) sem emenda para permitir a autenticação sem emenda em dispositivos não Windows 10 ou computadores não Windows Server 2016. Além disso, esta função confere a possibilidade de ver registos de inscrição e de aceder à saúde e análise para fins de monitorização e resolução de problemas. [Saiba mais.](../users-groups-roles/directory-assign-admin-roles.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Função de administrador de rede incorporado

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Funções de AD AZure

**Capacidade do produto:** Controlo de Acessos

Os utilizadores com esta função podem rever recomendações de arquitetura de perímetro de rede da Microsoft que são baseadas em telemetria de rede a partir das suas localizações de utilizadores. O desempenho da rede para o Microsoft 365 baseia-se na arquitetura cuidadosa do perímetro da rede de clientes da empresa, que é geralmente específica da localização do utilizador. Esta função permite a edição das localizações do utilizador descobertas e a configuração de parâmetros de rede para esses locais para facilitar a melhoria das medições de telemetria e recomendações de design. [Saiba mais.](../users-groups-roles/directory-assign-admin-roles.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Atividade a granel e downloads na experiência do portal de administração Admin Azure

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Gestão de Utilizadores

**Capacidade do produto:** Diretório

Agora pode realizar atividades a granel em utilizadores e grupos em Azure AD, enviando um ficheiro CSV na experiência do portal de administração Azure AD. Pode criar utilizadores, eliminar utilizadores e convidar utilizadores convidados. E pode adicionar e remover membros de um grupo.

Também pode baixar listas de recursos Ad AD Azure a partir da experiência do portal de administração Azure. Você pode baixar a lista de utilizadores no diretório, a lista de grupos no diretório, e os membros de um determinado grupo.

Para mais informações, confira o seguinte:

- [Criar utilizadores](../users-groups-roles/users-bulk-add.md) ou [convidar utilizadores convidados](../external-identities/tutorial-bulk-invite.md)
- [Eliminar utilizadores](../users-groups-roles/users-bulk-delete.md) ou [restaurar utilizadores eliminados](../users-groups-roles/users-bulk-restore.md)
- [Baixar lista de utilizadores](../users-groups-roles/users-bulk-download.md) ou [baixar lista de grupos](../users-groups-roles/groups-bulk-download.md)
- [Adicione (importar) membros](../users-groups-roles/groups-bulk-import-members.md) ou [remova membros](../users-groups-roles/groups-bulk-remove-members.md) ou [Descarregue a lista de membros](../users-groups-roles/groups-bulk-download-members.md) para um grupo

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

Para obter mais informações, consulte [validar uma regra de adesão dinâmica do grupo (pré-visualização)](../users-groups-roles/groups-dynamic-rule-validation.md).

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

Estas novas ações de melhoria requerem o registo dos seus utilizadores ou administradores para a autenticação de vários fatores (MFA) em todo o seu diretório e estabelecer o conjunto certo de políticas que se adequam às suas necessidades organizacionais. O objetivo principal é ter flexibilidade, garantindo ao mesmo tempo que todos os seus utilizadores e administradores podem autenticar com múltiplos fatores ou pedidos de verificação de identidade baseados no risco. Isto pode assumir a forma de ter múltiplas políticas que aplicam decisões âmbito, ou definir padrão de segurança (a partir de 16 de março) que permitem à Microsoft decidir quando desafiar os utilizadores para mFA. [Leia mais sobre as novidades no Microsoft Secure Score](/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---

## <a name="march-2020"></a>Março de 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Contas de Diretório Ativo Azure não geridos em atualização B2B para março de 2021

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

Para obter mais informações sobre as aplicações, consulte a [integração da aplicação SaaS com o Azure Ative Directory.](https://aka.ms/appstutorial) Para obter mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte [List a sua aplicação na galeria de aplicações Azure Ative.](https://aka.ms/azureadapprequest)

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
 
A integração do Azure Monitor com os registos AZure AD está agora disponível no Governo de Azure. Pode encaminhar os Registos AD do Azure (Registos de Auditoria e Registos) para uma conta de armazenamento, Centro de Eventos e Analítico de Log. Por favor, verifique a [documentação detalhada,](https://aka.ms/aadlogsinamd) bem como [planos de implementação para relatórios e monitorização](../reports-monitoring/plan-monitoring-and-reporting.md) para cenários Azure AD.

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
 
O serviço de fornecimento AD AZure fornece um rico conjunto de capacidades de configuração. Os clientes precisam de ser capazes de guardar a sua configuração para que possam referir-se a ela mais tarde ou reverter para uma versão conhecida. Adicionámos a capacidade de descarregar a sua configuração de provisionamento como um ficheiro JSON e carregá-la quando precisar. [Saiba mais](../app-provisioning/export-import-provisioning-configuration.md).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>A SSPR (redefinição da palavra-passe de autosserviço) requer agora dois portões para administradores no Microsoft Azure operados pela 21Vianet (Azure China 21Vianet) 

**Tipo:** Alteração de recurso  
**Categoria de serviço:** Reset da palavra-passe de autosserviço  
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

Para proporcionar uma forma mais flexível de os clientes criarem grupos de diretório que melhor satisfaçam as suas necessidades, substituímos a opção grupos de **diretório** das configurações gerais dos **grupos**  >  **General** no portal Azure por um link para [a documentação dinâmica do grupo.](../users-groups-roles/groups-dynamic-membership.md) Melhorámos a nossa documentação para incluir mais instruções para que os administradores possam criar grupos de todos os utilizadores que incluam ou excluam os utilizadores convidados.

---
