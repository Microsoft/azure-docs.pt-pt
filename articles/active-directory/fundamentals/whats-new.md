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
ms.date: 02/28/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 455be9ba95975ba5c3bd870cbe01ca916d85ef41
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544412"
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

## <a name="april-2019"></a>Abril de 2019

### <a name="azure-active-directory-azure-ad-entitlement-management-is-now-available-public-preview"></a>Gestão de direitos do Active Directory (Azure AD) do Azure está agora disponível (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Identity Governance  
**Capacidade de produto:** Identity Governance

Gestão de direitos do Azure AD, agora em pré-visualização pública, ajuda os clientes para delegar a gestão de pacotes de acesso, que define como os funcionários e parceiros de negócios podem solicitar acesso, que têm de aprovar e quanto aos quais têm acesso. Pacotes de acesso podem gerir a associação do Azure AD e Office 365 grupos, atribuições de funções nas aplicações empresariais e atribuições de funções de sites do SharePoint Online. Saiba mais sobre a gestão de direitos no [descrição geral da gestão de direitos do Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Para saber mais sobre a amplitude dos recursos de governação de identidade do Azure AD, incluindo o Privileged Identity Management, revisões de acesso e termos de utilização, veja [o que é a governação de identidade do Azure AD?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configurar uma política de nomes para grupos do Office 365 no portal do Azure AD (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade de produto:** Colaboração

Os administradores agora podem configurar uma política de nomes para grupos do Office 365, com o portal do Azure AD. Esta alteração ajuda a impor as convenções de nomenclatura consistentes de grupos do Office 365 criados ou editados por utilizadores na sua organização. 

Pode configurar a política de nomes para grupos do Office 365 de duas formas diferentes:

- Defina prefixos ou sufixos, que são adicionados automaticamente para um nome de grupo.

- Carregar um conjunto personalizado de palavras bloqueadas para a sua organização, que não são permitidos em nomes de grupo (por exemplo, "CEO, folhas de pagamento, RH").

Para obter mais informações, consulte [impor uma política de atribuição de nomes para grupos do Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Registos de atividade do AD do Azure estão agora disponíveis no Azure Monitor (disponibilidade geral)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Relatórios  
**Capacidade de produto:** Monitorização e Relatórios

Para ajudar a resolver os seus comentários sobre visualizações com os registos de atividades do Azure AD, que estamos a introduzir uma nova funcionalidade de informações no Log Analytics. Esta funcionalidade ajuda-o a obter informações sobre os recursos do Azure AD com os nossos modelos interativos, chamados de pastas de trabalho. Estas previamente criadas pastas de trabalho podem fornecer os detalhes para aplicações ou utilizadores e incluem:

- **Inícios de sessão.** Fornece detalhes para aplicações e utilizadores, incluindo o início de sessão local, o sistema de operativo em utilização ou cliente de navegador e versão e o número de inícios de sessão com êxito ou falhados.

- **Autenticação e acesso condicional.** Fornece detalhes para aplicações e utilizadores através da autenticação de legado, incluindo a utilização de multi-factor Authentication acionada pelas políticas de acesso condicional, aplicações através de políticas de acesso condicional, e assim por diante.

- **Análise de falhas de início de sessão.** Ajuda-o a determinar se os erros de início de sessão estão a ocorrer devido a uma ação do utilizador, a problemas de políticas ou a sua infraestrutura.

- **Relatórios personalizados.** Pode criar um novo ou editar as pastas de trabalho existentes que ajudam a personalizar a funcionalidade de informações para a sua organização.

Para obter mais informações, consulte [como utilizar as pastas de trabalho do Azure Monitor para relatórios do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Novas aplicações federadas disponíveis na Galeria de aplicações do Azure AD - Abril de 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicações Empresariais  
**Capacidade de produto:** Integração de Terceiros

De 2019 de Abril, adicionámos que 21 novos aplicativos com a Federação de suporte para a Galeria de aplicações:

[No SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [ Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Ligue-se](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel ligar](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (SSO baseado em funções)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent estudo de patrimônio gestão](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Gestor de certificados de Sectigo](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Revisões de acesso nova opção de frequência e a seleção da função múltipla

**Tipo:** Nova funcionalidade  
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

O Azure AD Connect está no processo de transição nossos sistemas de alerta de e-mail, potencialmente que um remetente de e-mail novo mostra alguns clientes. Para resolver isso, tem de adicionar `azure-noreply@microsoft.com` na lista de permissões da sua organização ou não será possível continuar a receber alertas importantes a partir do Office 365, Azure ou os serviços de sincronização.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Alterações de sufixo UPN estão agora com êxito entre domínios federados, no Azure AD Connect

**Tipo:** Fixo  
**Categoria de serviço:** Sincronização do AD  
**Capacidade de produto:** Plataforma

Pode agora alterar com êxito sufixo UPN de um utilizador de um domínio federados para outro domínio federados no Azure AD Connect. Esta correção significa que já não deve ocorrer a mensagem de erro FederatedDomainChangeError durante o ciclo de sincronização ou receber uma notificação de e-mail a indicar, "não é possível atualizar este objeto no Azure Active Directory, porque o atributo [ FederatedUser.UserPrincipalName], não é válido. Atualize o valor nos serviços de diretório local".

Para obter mais informações, consulte [resolver problemas de erros durante a sincronização](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Maior segurança através da política de acesso condicional com base em proteção de aplicações no Azure AD (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade de produto:** Segurança e Proteção de Identidade

Acesso condicional com base na proteção aplicação agora está disponível através da **necessitam de proteção de aplicações** política. Esta nova política ajuda a aumentar a segurança da sua organização ao ajudar a impedir que:

- Utilizadores obtenham acesso a aplicações sem uma licença do Microsoft Intune.

- Usuários não é possível obter uma política de proteção de aplicações do Microsoft Intune.

- Utilizadores obtenham acesso a aplicações sem uma política de proteção de aplicações do Microsoft Intune configurada.

Para obter mais informações, consulte [como requerer a política de proteção de acesso à aplicação de cloud com o acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Novo suporte para acesso do Azure AD único início de sessão e condicional no Microsoft Edge (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade de produto:** Segurança e Proteção de Identidade

Melhorámos o nosso suporte do Azure AD para o Microsoft Edge, inclusive fornecendo o novo suporte para acesso de início de sessão e condicional único no Azure AD. Se tiver utilizado anteriormente o Microsoft Intune Managed Browser, pode agora utilizar a Microsoft Edge em vez disso.

Para obter mais informações sobre como configurar e gerir os seus dispositivos e aplicações através do acesso condicional, consulte [exigir dispositivos para aceder à aplicação de cloud com o acesso condicional geridos](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) e [necessitam de aplicações de cliente para a cloud aprovadas acesso de aplicação com acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Para obter mais informações sobre como gerir o acesso com o Microsoft Edge com políticas do Microsoft Intune, consulte [acesso à Internet de gerir através de um browser do Microsoft Intune protegido por política](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Março de 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Arquitetura de experiências de identidade e de política personalizada do suporte no Azure Active Directory B2C está agora disponível (GA)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidades de Consumidor  
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

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicações Empresariais  
**Capacidade de produto:** Integração de Terceiros

De 2019 de Março, adicionámos que suportam a 14 novos aplicativos com a Federação para a Galeria de aplicações:

[ISEC7 Mobile Exchange delegado](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Sistema de auditoria com base em explicação](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Saiba](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [assuntos de desempenho de Powerschool](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit horizontes](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [tarefa no](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Novo Zscaler e Atlassian aprovisionamento conectores na galeria do Azure AD - Março de 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aprovisionamento de Aplicação  
**Capacidade de produto:** Integração de Terceiros

Automatize a criar, atualizar e excluir contas de utilizador para as seguintes aplicações:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler um](https://aka.ms/ZscalerOneProvisioning), [Zscaler dois](https://aka.ms/ZscalerTwoProvisioning), [Zscaler três](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud ](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Para obter mais informações sobre como proteger melhor a sua organização através do aprovisionamento de conta de utilizador automatizada, consulte [automatizar o aprovisionamento de utilizadores para aplicações SaaS com o Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Restaurar e gerir os seus grupos do Office 365 eliminados no portal do Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade de produto:** Colaboração

Agora pode ver e gerir os seus grupos do Office 365 eliminados do portal do Azure AD. Esta alteração ajuda-o a ver quais os grupos estão disponíveis para restaurar, juntamente com a informá-lo permanentemente eliminar todos os grupos que não são necessários para sua organização.

Para obter mais informações, consulte [restauro expirou ou grupos eliminados](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Início de sessão único está agora disponível para aplicações protegidas de SAML do Azure AD no local através do Proxy de aplicações (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proxy de Aplicações  
**Capacidade de produto:** Controlo de Acesso

Agora pode fornecer uma experiência de início de sessão único (SSO) para aplicações no local, autenticação SAML, juntamente com o acesso remoto a estas aplicações através do Proxy de aplicações. Para obter mais informações sobre como configurar o SAML SSO com as suas aplicações no local, consulte [SAML início de sessão único para aplicações no local com o Proxy de aplicações (pré-visualização)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Aplicações de cliente em ciclos de solicitação serão interrompidas para melhorar a experiência de utilizador e fiabilidade

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Inícios de sessão)  
**Capacidade de produto:** Autenticação de Utilizador

Aplicações de cliente incorretamente podem emitir centenas dos mesmos pedidos de início de sessão durante um curto período de tempo. Estes pedidos, se forem bem-sucedidos ou não, contribuem para uma experiência de usuário insatisfatória e o cliente de cargas de trabalho para o IDP, aumentando a latência para todos os utilizadores e reduzindo a disponibilidade do IDP.

Esta atualização envia uma `invalid_grant` erro: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` para aplicações de cliente que emitem pedidos de duplicação várias vezes durante um curto período de tempo, além do escopo da operação normal. Aplicações de cliente que encontram este problema devem mostrar uma linha de comandos interativa, exigir que o utilizador iniciar sessão novamente. Para obter mais informações sobre esta alteração e sobre como resolver a sua aplicação se encontrar este erro, consulte [o que há de novo para a autenticação?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nova experiência de utilizador de registos de auditoria já está disponível

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Relatórios  
**Capacidade de produto:** Monitorização e Relatórios

Nós criamos um novo do Azure AD **registos de auditoria** página para ajudar a melhorar a legibilidade e como pesquisar para sua informação. Para ver a nova **registos de auditoria** página, selecione **registos de auditoria** no **atividade** secção do Azure AD.

![Nova página de registos de auditoria, com informações de exemplo](media/whats-new/audit-logs-page.png)

Para obter mais informações sobre a nova **registos de auditoria** página, consulte [relatórios de atividade no portal do Azure Active Directory de auditoria](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Novos avisos e as orientações para ajudar a evitar o bloqueio acidental de administrador de políticas de acesso condicional configuradas incorretamente

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Acesso Condicional  
**Capacidade de produto:** Segurança e Proteção de Identidade

Para ajudar a impedir que os administradores bloqueiem acidentalmente a próprios fora de seus próprios inquilinos através de políticas de acesso condicional configuradas incorretamente, nós criamos novos avisos e orientações atualizadas no portal do Azure. Para obter mais informações sobre as novas diretrizes, consulte [quais são as dependências do serviço de acesso condicional do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Termos de utilizador melhorada de experiências de utilização em dispositivos móveis

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Termos de Utilização  
**Capacidade de produto:** Governação

Atualizámos os nossos termos existentes de experiências de utilização para ajudar a melhorar como rever e aceitar os termos de utilização num dispositivo móvel. Agora pode ampliar e reduzir, volte atrás, transferir as informações e selecione hiperlinks. Para obter mais informações sobre os termos atualizados de utilização, consulte [do Azure Active Directory termos da funcionalidade de utilização](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Disponibilidade de experiência de download de novos registos de atividades do Azure AD

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Relatórios  
**Capacidade de produto:** Monitorização e Relatórios

Pode agora transferir grandes quantidades de registos de atividade diretamente a partir do portal do Azure. Esta atualização permite-lhe:

- Transferir até 250 000 linhas.

- Seja notificado depois de concluída a transferência.

- Personalize o seu nome de ficheiro.

- Determine o formato de saída, JSON ou CSV.

Para obter mais detalhes sobre esta funcionalidade, consulte [início rápido: Transferir um relatório de auditoria no portal do Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Alteração significativa: Atualizações à avaliação de condição pelo Exchange ActiveSync (EAS)

**Tipo:** Plano de alteração  
**Categoria de serviço:** Acesso Condicional  
**Capacidade de produto:** Controlo de Acesso

Estamos atualmente a atualizar a forma como o Exchange ActiveSync (EAS) avalia as seguintes condições:

- Localização do utilizador, com base no país, região ou endereço IP

- Risco de início de sessão

- Plataforma de dispositivos

Se usou anteriormente essas condições em suas políticas de acesso condicional, lembre-se de que o comportamento de condição podem ser alteradas. Por exemplo, se tiver utilizado anteriormente a condição de localização do utilizador numa política, talvez se a política agora a ser ignorada com base na localização dos seus utilizadores.

---

## <a name="february-2019"></a>Fevereiro de 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Configurável encriptação de tokens SAML do Azure AD (pré-visualização pública) 

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicações Empresariais  
**Capacidade de produto:** SSO

Agora pode configurar qualquer aplicação SAML suportada para receber tokens SAML encriptados. Quando configurado e utilizado com uma aplicação, o AD do Azure encripta as asserções SAML emitidas com uma chave pública obtida a partir de um certificado armazenado no Azure AD.

Para obter mais informações sobre como configurar a encriptação de tokens SAML, consulte [encriptação de tokens de configurar o Azure AD SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Criar uma revisão de acesso para grupos ou aplicações com as revisões de acesso do Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Revisões de Acesso  
**Capacidade de produto:** Governação

Agora pode incluir vários grupos ou revisão para associação a um grupo ou a atribuição de aplicações de acesso de aplicações num único Azure AD. Revisões de acesso a vários grupos ou aplicações são configuradas utilizando as definições e todos os revisores incluídos são notificados ao mesmo tempo.

Para obter mais informações sobre como criar uma revisão de acesso utilizando as revisões de acesso do Azure AD, consulte [criar uma revisão de acesso de grupos ou aplicações nas revisões de acesso do Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Novas aplicações federadas disponíveis na Galeria de aplicações do Azure AD - Fevereiro de 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicações Empresariais  
**Capacidade de produto:** Integração de Terceiros
 
Fevereiro de 2019, adicionámos que 27 novos aplicativos com a Federação de suporte para a Galeria de aplicações:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [DEDO FAT](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Clique de permissão, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [sísmica ](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Partilha um sonho](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods integração Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [LMS do conhecimento em qualquer lugar](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [UO Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [dados Periscope](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud por Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp plataforma de produtividade](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Registo MFA/SSPR combinado aprimorado

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Reposição Autónoma de Palavra-passe  
**Capacidade de produto:** Autenticação de Utilizador
 
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
**Categoria de serviço:** B2C - Gestão de Identidades de Consumidor  
**Capacidade de produto:** B2B/B2C

Atualizámos o processo de criação e gestão de política para os fluxos de utilizador (anteriormente conhecidos como, incorporadas políticas) mais fácil. Esta nova experiência agora é a predefinição para todos os inquilinos do Azure AD.

Pode fornecer comentários adicionais e sugestões, utilizando o sorriso ou frown ícones no **envie-nos comentários** área na parte superior do ecrã portal.

Para obter mais informações sobre a nova experiência de gestão de política, consulte a [do Azure AD B2C tem agora a personalização de JavaScript e muitos outros novos recursos](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Escolha versões de elemento de página específica fornecidas pelo Azure AD B2C

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidades de Consumidor  
**Capacidade de produto:** B2B/B2C

Agora, pode escolher uma versão específica de elementos da página fornecido pelo Azure AD B2C. Ao selecionar uma versão específica, pode testar as atualizações antes de serem apresentados numa página e pode obter um comportamento previsível. Além disso, pode agora optar por impor versões de página específica para permitir que as personalizações de JavaScript. Para ativar esta funcionalidade, vá para o **propriedades** página nos seus fluxos de utilizador.

Para obter mais informações sobre como escolher versões específicas dos elementos da página, consulte a [do Azure AD B2C tem agora a personalização de JavaScript e muitos outros novos recursos](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Requisitos de palavra-passe do utilizador final configuráveis para B2C (GA)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidades de Consumidor  
**Capacidade de produto:** B2B/B2C

Agora pode configurar complexidade de palavra-passe da sua organização para usuários finais, em vez de precisar usar seu nativas da política de palavra-passe do Azure AD. Do **propriedades** painel de utilizadores (anteriormente conhecidos como as políticas incorporadas) de fluxos, pode escolher uma complexidade de palavra-passe de **simples** ou **forte**, ou pode criar uma **personalizado** conjunto de requisitos.

Para obter mais informações sobre a configuração do requisito de complexidade de palavra-passe, consulte [configurar requisitos de complexidade de palavras-passe no Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Novos modelos predefinidos para uma experiência de autenticação com marca personalizada

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidades de Consumidor  
**Capacidade de produto:** B2B/B2C

Pode usar o nosso novo modelos predefinidos, localizados no **layouts de página** painel dos seus fluxos de utilizador (anteriormente conhecido como as políticas incorporadas) criar um personalizado de marca de experiência de autenticação para os seus utilizadores.

Para obter mais informações sobre como utilizar os modelos, consulte [do Azure AD B2C tem agora a personalização de JavaScript e muitos outros novos recursos](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Janeiro de 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Colaboração B2B de diretório Active Directory a utilizar a autenticação de código de acesso único (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade de produto:** B2B/B2C

Autenticação de código de acesso Monouso (OTP) foi introduzida para os utilizadores convidados B2B que não podem ser autenticados através de outros meios, como o Azure AD, uma conta Microsoft (MSA) ou o Federação de Google. Este novo método de autenticação significa que os utilizadores não têm de criar uma nova conta Microsoft de convidado. Em vez disso, ao resgatar um convite ou aceder a um recurso compartilhado, um utilizador convidado pode pedir um código temporário para ser enviado para um endereço de e-mail. O utilizador convidado usando este código temporário, pode continuar a iniciar sessão.

Para obter mais informações, consulte [autenticação de código de acesso único por E-Mail (pré-visualização)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) e o blog [do Azure AD torna a partilha e colaboração integrada para qualquer utilizador com qualquer conta](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Novas definições de cookie do Proxy de aplicações do Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proxy de Aplicações  
**Capacidade de produto:** Controlo de Acesso

Introduzimos três novas definições de cookies, disponíveis para as suas aplicações publicadas através do Proxy de aplicações:

- **Utilize cookie somente de HTTP.** Conjuntos de **HTTPOnly** sinalizador no seus cookies de acesso e de sessão do Proxy de aplicações. Ativar esta definição fornece benefícios de segurança adicionais, como ajudar a impedir a copiar ou modificação de cookies por meio de scripts do lado do cliente. Recomendamos que ative este sinalizador (escolher **Sim**) para os benefícios adicionais.

- **Utilize cookie seguro.** Conjuntos de **seguro** sinalizador no seus cookies de acesso e de sessão do Proxy de aplicações. Ativar esta definição fornece benefícios de segurança adicional, tornando-se de que os cookies apenas são transmitidos através de canais de segurança de TLS, tal como HTTPS. Recomendamos que ative este sinalizador (escolher **Sim**) para os benefícios adicionais.

- **Utilize cookie persistente.** Impede que o acesso cookies prestes a expirar quando o browser é fechado. Estes cookies pela última vez para o tempo de vida do token de acesso. No entanto, os cookies são repostos se for atingida a hora de expiração ou se o utilizador o elimine manualmente o cookie. Recomendamos que mantenha a predefinição **não**, apenas Ativando a definição de aplicações mais antigas que não partilham cookies entre processos.

Para obter mais informações sobre os novos cookies, consulte [definições de cookies para aceder a aplicações no local no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações do Azure AD - janeiro de 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicações Empresariais  
**Capacidade de produto:** Integração de Terceiros
 
De 2019 de Janeiro, adicionámos que estes 35 novas aplicações com a Federação de suporte para a Galeria de aplicações:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [paleta de talento](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco guarda-chuva](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Administrador de acesso à Internet](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [lembrete de expiração](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Visualizador](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [verbo](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Digital de Pavaso fechar](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [Sistema de GTNexus SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARES para Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 para o Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [ALP de Ferry Korn](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas plataforma](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Novas melhorias no Azure AD Identity Protection (Pré-visualização pública)

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Identity Protection  
**Capacidade de produto:** Segurança e Proteção de Identidade

Temos o prazer de anunciar que adicionamos os seguintes melhoramentos para a oferta de pré-visualização pública do Azure AD Identity Protection, incluindo:

- Uma interface do usuário mais integrada e atualizados

- APIs adicionais

- Avaliação de riscos melhorada através de machine learning

- Alinhamento de todo o produto em utilizadores de risco e inícios de sessão arriscados

Para obter mais informações sobre os melhoramentos, consulte [o que é o Azure Active Directory Identity Protection (atualizadas)?](https://aka.ms/IdentityProtectionDocs) Para saber mais e Compartilhe suas idéias por meio das instruções no produto.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nova funcionalidade de Bloqueio de Aplicação para a aplicação Microsoft Authenticator em dispositivos iOS e Android

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicação Microsoft Authenticator  
**Capacidade de produto:** Segurança e Proteção de Identidade

Para manter seus códigos de acesso monouso, informações da aplicação e as definições da aplicação mais segura, pode ativar a funcionalidade de bloqueio de aplicação na aplicação Microsoft Authenticator. Ativar o bloqueio de aplicação significa que estará mais frequentes para autenticar a utilizar o seu PIN ou biométrico sempre que abrir a aplicação Microsoft Authenticator.

Para obter mais informações, consulte a [aplicação Microsoft Authenticator FAQ](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Capacidades de exportação avançadas do Azure AD Privileged Identity Management (PIM)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Privileged Identity Management  
**Capacidade de produto:** Privileged Identity Management

Administradores do Privileged Identity Management (PIM) agora podem exportar todas as atribuições de função elegível e Active Directory para um recurso específico, que inclui atribuições de funções para todos os recursos subordinados. Anteriormente, era difícil para os administradores obter uma lista completa das atribuições de funções para uma subscrição e teve de exportar as atribuições de função para cada recurso específico.

Para obter mais informações, consulte [ver histórico de atividade e auditoria para funções de recursos do Azure no PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Novembro/Dezembro de 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Os utilizadores removidos do âmbito de sincronização já não mudam para contas apenas na cloud

**Tipo:** Fixo  
**Categoria de serviço:** Gestão de Utilizadores  
**Capacidade de produto:** Diretório

>[!Important]
>Nós já ouviu falar e compreender a sua frustração devido a esta correção. Portanto, podemos já reverter esta alteração até que esse tempo que podemos fazer a correção mais fácil de implementar na sua organização.

Corrigimos um bug em que o sinalizador DirSyncEnabled de um utilizador seria erroneamente mudado para **False** quando o objeto de serviços de domínio do Active Directory (AD DS) foi excluído do escopo de sincronização e, em seguida, movido para a Reciclagem no O Azure AD no ciclo de sincronização seguinte. Como resultado desta correção, se o utilizador está excluído do âmbito de sincronização e posteriormente restaurado a partir do Azure AD da Reciclagem, a conta de utilizador continua sendo sincronizadas a partir do AD no local, conforme o esperado e não pode ser gerida na cloud, uma vez que a respetiva origem de autoridade (SoA) permanece como AD no local.

Antes desta correção, Ocorreu um problema quando o sinalizador DirSyncEnabled foi mudado para False. Ele deu a impressão errada de que estas contas foram convertidas em objetos de apenas na cloud e que as contas podem ser geridas na cloud. No entanto, as contas retidos ainda seus SoA como no local e sincronizadas todas as propriedades (atributos de cópias de sombra) provenientes do AD no local. Esta condição causou vários problemas no Azure AD e outras cloud cargas de trabalho (como o Exchange Online) que devem tratar estas contas, como sincronizadas a partir do AD, mas foram agora se comportando como contas apenas na cloud.

Neste momento, a única maneira de verdadeiramente converter uma conta de sincronizadas a partir do AD conta apenas na cloud é através da respetiva desativação DirSync ao nível do inquilino, o que aciona uma operação de back-end para transferir a SoA. Este tipo de alteração da SoA requer (mas não está limitado a) a limpeza de todos os locais de atributos relacionados (como LastDirSyncTime e atributos de cópias de sombra) e enviar um sinal para outras cargas de trabalho da cloud ter seu respectivo objeto convertido para uma conta apenas na cloud também .

Esta correção conseqüentemente impede a atualizações diretas no atributo ImmutableID de um utilizador sincronizado a partir do AD, que, em alguns cenários no passado, eram necessárias. Por predefinição, o ImmutableID de um objeto no Azure AD, como o nome indica, deve ser imutável. Novos recursos implementados no cliente do Azure AD Connect Health e a sincronização de ligação de AD do Azure estão disponíveis para lidar com esses cenários:

- **Atualização de ImmutableID em grande escala para muitos usuários numa abordagem faseada**
  
  Por exemplo, terá de efetuar uma migração de entre florestas do AD DS demorada. Solução: Utilizar o Azure AD Connect, para **configurar âncora de origem** e, como o utilizador efetua a migração, copie os valores de ImmutableID existentes do Azure AD para o atributo de ms-DS-consistência-Guid do utilizador de AD DS local da nova floresta. Para obter mais informações, consulte [usando o ms-DS-ConsistencyGuid como sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Atualizações de ImmutableID em grande escala para muitos usuários de uma só vez**

  Por exemplo, durante a implementação do Azure AD Connect, Cometa um erro, e agora precisa alterar o atributo SourceAnchor. Solução: Desative o DirSync ao nível do inquilino e limpar todos os valores de ImmutableID inválidos. Para obter mais informações, consulte [desativar a sincronização de diretórios para o Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Utilizador de locais de rematch com um utilizador existente no Azure AD** por exemplo, um utilizador que foi recriado no AD DS gera um duplicado na conta do Azure AD em vez de rematching-lo com uma conta do Azure AD existente (objeto órfão). Solução: Utilize o Azure AD Connect Health no portal do Azure para Remapeie o âncora de origem/ImmutableID. Para obter mais informações, consulte [cenário de objeto Orphaned](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Alteração de última hora: Atualizações para a auditoria e o esquema de registos de início de sessão através do Azure Monitor

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Relatórios  
**Capacidade de produto:** Monitorização e Relatórios

Atualmente, está publicando fluxos de log de auditoria tanto o início de sessão através do Azure Monitor, portanto, pode se integrar perfeitamente os ficheiros de registo com as suas ferramentas SIEM ou com o Log Analytics. Com base nos seus comentários e, em preparação para o anúncio de disponibilidade geral desta funcionalidade, estamos a tornar as seguintes alterações para nosso esquema. Estas alterações de esquema e suas atualizações da documentação relacionada acontecerá na primeira semana de Janeiro.

#### <a name="new-fields-in-the-audit-schema"></a>Novos campos no esquema de auditoria
Estamos a adicionar um novo **tipo de operação** campo, para fornecer o tipo de operação efetuada no recurso. Por exemplo, **Add**, **Update**, ou **eliminar**.

#### <a name="changed-fields-in-the-audit-schema"></a>Campos alterados no esquema de auditoria
Os seguintes campos estão mudando no esquema de auditoria:

|Nome do campo|O que mudou|Valores antigos|Novos valores|
|----------|------------|----------|----------|
|Category|Esse era o **nome do serviço** campo. Agora é o **categorias de auditoria** campo. **Nome do serviço** foi mudado para o **loggedByService** campo.|<ul><li>Aprovisionamento de Contas</li><li>Diretório do Núcleo</li><li>Reposição de palavra-passe self-service</li></ul>|<ul><li>Gestão de Utilizadores</li><li>Gestão de Grupos</li><li>Gestão de Aplicações</li></ul>|
|targetResources|Inclui **TargetResourceType** no nível superior.|&nbsp;|<ul><li>Política</li><li>Aplicação</li><li>Utilizador</li><li>Agrupar</li></ul>|
|loggedByService|Fornece o nome do serviço que gerou o registo de auditoria.|Null|<ul><li>Aprovisionamento de Contas</li><li>Diretório do Núcleo</li><li>Reposição de palavra-passe self-service</li></ul>|
|Resultado|Fornece o resultado dos registos de auditoria. Anteriormente, isso estava enumerado, mas vamos agora mostrar o valor real.|<ul><li>0</li><li>1</li></ul>|<ul><li>Com êxito</li><li>Falha</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Campos alterados no esquema de início de sessão
Os seguintes campos estão mudando no esquema de início de sessão:

|Nome do campo|O que mudou|Valores antigos|Novos valores|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Esse era o **conditionalaccessPolicies** campo. Agora é o **appliedConditionalAccessPolicies** campo.|Sem alterações|Sem alterações|
|conditionalAccessStatus|Fornece o resultado de estado de política de acesso condicional no início de sessão. Anteriormente, isso estava enumerado, mas vamos agora mostrar o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Com êxito</li><li>Falha</li><li>Não Aplicado</li><li>Desactivado</li></ul>|
|appliedConditionalAccessPolicies: resultado|Fornece o resultado do individuais estado de política de acesso condicional no início de sessão. Anteriormente, isso estava enumerado, mas vamos agora mostrar o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Com êxito</li><li>Falha</li><li>Não Aplicado</li><li>Desactivado</li></ul>|

Para obter mais informações sobre o esquema, consulte [esquema de registos de auditoria de interpretação do Azure AD no Azure Monitor (pré-visualização)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Melhorias no Identity Protection para o modelo controlado de machine learning e motor de pontuação de risco

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Identity Protection  
**Capacidade de produto:** Pontuações de risco

Melhorias ao relacionados com a proteção de identidade do usuário e início de sessão risco avaliação motor de podem ajudar a melhorar a precisão de risco de utilizador e a cobertura. Os administradores poderão reparar que o nível de risco do utilizador já não é diretamente está ligado ao nível de risco de detecções específicos e que existe um aumento no número e do nível de risco eventos de início de sessão.

Deteções de risco agora são avaliadas de modelo, que calcula o risco de utilizador com funcionalidades adicionais de inícios de sessão do utilizador e um padrão de detecções de aprendizagem supervisionada. Com base nesse modelo, o administrador talvez os usuários com pontuações de risco elevado, mesmo se deteções associadas a esse utilizador são de risco baixo ou médio. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Os administradores podem repor a própria palavra-passe com a aplicação Microsoft Authenticator (Pré-visualização pública)

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Reposição Autónoma de Palavra-passe  
**Capacidade de produto:** Autenticação de Utilizador

Administradores do Azure AD podem agora repor a sua própria palavra-passe a utilizar nas notificações da aplicação Microsoft Authenticator ou um código a partir de qualquer aplicação de autenticador móvel ou do hardware token. Para repor a sua própria palavra-passe, os administradores poderão agora utilizar dois dos seguintes métodos:

- Notificação da aplicação Microsoft Authenticator

- Outra aplicação de autenticador móvel / código de token de Hardware

- Correio Electrónico

- Chamada telefónica

- Mensagem SMS

Para obter mais informações sobre como utilizar a aplicação Microsoft Authenticator para repor palavras-passe, consulte [de reposição de palavra-passe self-service do Azure AD - aplicações móveis e de SSPR (pré-visualização)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nova função de Administrador de Dispositivos de Cloud do Azure AD (Pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão e Registo de Dispositivos  
**Capacidade de produto:** Controlo de acesso

Os administradores podem atribuir utilizadores para a nova função de administrador do dispositivo na Cloud para executar tarefas de administrador do dispositivo na cloud. Utilizadores atribuídos a função de administradores de dispositivos de nuvem podem ativar, desativar e eliminar dispositivos no Azure AD, juntamente com a capacidade de ler as chaves do BitLocker do Windows 10 (caso exista) no portal do Azure.

Para obter mais informações sobre as funções e permissões, consulte [atribuir funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Gira os seus dispositivos com a nova data/hora de atividade no Azure AD (Pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Gestão e Registo de Dispositivos  
**Capacidade de produto:** Gestão de ciclo de vida de dispositivos

Sabemos que ao longo do tempo deve atualizar e extinguir dispositivos da sua organização no Azure AD, para evitar que dispositivos obsoletos no seu ambiente. Para ajudar nesse processo, o Azure AD agora atualiza seus dispositivos com um novo carimbo de atividade, ajudando-o a gerir o ciclo de vida do dispositivo.

Para obter mais informações sobre como obter e utilizar esta timestamp, consulte [como: Gerir os dispositivos obsoletos no Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Os administradores podem exigir que os utilizadores aceitem os Termos de utilização em cada dispositivo

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Termos de Utilização  
**Capacidade de produto:** Governação
 
Os administradores podem agora ativar a **exigir que os utilizadores dar consentimento em todos os dispositivos** opção para exigir aos utilizadores para aceitarem os termos de utilização em cada dispositivo que estiver a utilizar no seu inquilino.

Para obter mais informações, consulte a [por dispositivo termos da seção de utilização dos Azure Active Directory termos de funcionalidade utilização](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Os administradores podem configurar os Termos de utilização para expirar com base numa agenda periódica

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Termos de Utilização  
**Capacidade de produto:** Governação
 

Os administradores podem agora ativar a **expirar consentimentos** opção que torna os termos de utilização expirar para todos os seus utilizadores com base na agenda recorrente especificada. O agendamento pode ser anualmente, bi anualmente, trimestralmente ou mensalmente. Depois dos termos de utilização expirarem, os utilizadores têm voltem.

Para obter mais informações, consulte a [adicionar termos da seção de utilização dos Azure Active Directory termos de funcionalidade utilização](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Os administradores podem configurar os termos de utilização para expirar com base no agendamento de cada utilizador

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Termos de Utilização  
**Capacidade de produto:** Governação

Os administradores agora podem especificar um período de tempo que o utilizador tem voltem a termos de utilização. Por exemplo, os administradores podem especificar que os utilizadores têm voltem a termos de utilização de todos os 90 dias.

Para obter mais informações, consulte a [adicionar termos da seção de utilização dos Azure Active Directory termos de funcionalidade utilização](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Novos e-mails do Azure AD Privileged Identity Management (PIM) para funções do Azure Active Directory

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Privileged Identity Management  
**Capacidade de produto:** Privileged Identity Management
 
Clientes que utilizam o Azure AD Privileged Identity Management (PIM) podem agora receber um e-mail de resumo semanal, incluindo as seguintes informações para os últimos sete dias:

- Descrição geral das atribuições principais funções elegíveis e permanentes

- Número de ativação de funções de utilizadores

- Número de utilizadores atribuídos a funções no PIM

- Número de utilizadores atribuídos a funções fora do PIM

- Número de utilizadores "feitas permanente" no PIM

Para obter mais informações sobre o PIM e as notificações de e-mail disponíveis, consulte [notificações no PIM por E-Mail](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>O licenciamento baseado em grupos está agora geralmente disponível

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Outros  
**Capacidade de produto:** Diretório

Licenciamento baseado em grupo está fora do pré-visualização pública e está agora em disponibilidade geral. Como parte desta versão gerais, fez esse recurso mais escalonável e adicionou a capacidade de Reprocessar atribuições de licenciamento baseado em grupo para um único utilizador e a capacidade de utilizar o licenciamento com o botão com base em grupo com licenças do Office 365 E3/A3.

Para obter mais informações sobre o licenciamento baseado em grupo, consulte [o que é o licenciamento no Azure Active Directory com base em grupo?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações do Azure AD - novembro de 2018

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicações Empresariais  
**Capacidade de produto:** Integração de Terceiros
 
Em Novembro de 2018, adicionámos que estes 26 novas aplicações com a Federação de suporte para a Galeria de aplicações:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://www.drivedollar.com/Account/Login), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Campus infinito](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [ HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Descompassos](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy para empresas Central 365](https://accounting.zenegy.com/), [Everbridge membro Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti do Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plexos aplicações - Teste clássico](https://test.plexonline.com/signon), [Plexos aplicações – clássico](https://www.plexonline.com/signon), [Plexos aplicações - teste UX](https://test.cloud.plex.com/sso), [Plexos aplicações – experiência do Usuário](https://cloud.plex.com/sso), [Plexos aplicações – IAM](https://accounts.plex.com/), [ARTESANATO - registos Childcare, participação e sistema de controlo financeiros](https://getcrafts.ca/craftsregistration) 

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Outubro de 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Os Registos do Azure AD já funcionam com o Azure Log Analytics (Pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Relatórios  
**Capacidade de produto:** Monitorização e Relatórios

Temos o prazer de anunciar que agora pode reencaminhar os registos do Azure AD para o Azure Log Analytics! Esta funcionalidade mais pedidas ajuda dá a acesso ainda melhor para análise para o seu negócio, operações e segurança, bem como uma forma de ajudar a monitorizar a sua infraestrutura. Para obter mais informações, consulte a [registos de atividade do Active Directory do Azure no Azure Log Analytics agora disponível](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Novas Aplicações Federadas disponíveis na galeria de aplicações do Azure AD – Outubro de 2018

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicações Empresariais  
**Capacidade de produto:** Integração de Terceiros
 
Em Outubro de 2018, adicionámos que suportam a 14 novos aplicativos com a Federação para a Galeria de aplicações:

[Meus pontos de prémio](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), teclado, [ambiente Virtual de ON24](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler três](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot controle](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Para obter mais informações sobre as aplicações, consulte [integração de aplicações SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre a listagem a sua aplicação na Galeria de aplicações do Azure AD, consulte [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Notificações de E-mail do Azure AD Domain Services

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Azure AD Domain Services  
**Capacidade de produto:** Azure AD Domain Services

O Azure AD Domain Services fornece alertas no portal do Azure sobre configurações incorretas ou problemas com o seu domínio gerido. Estes alertas incluem guias passo a passo para poder experimentar corrigir os problemas sem ter de contactar o suporte.

A partir de Outubro, poderá personalizar as definições de notificação para o seu domínio gerido, de modo quando ocorrem novos alertas, é enviado um e-mail para um grupo designado de pessoas, eliminando a necessidade de verifique constantemente o portal para atualizações.

Para obter mais informações, consulte [definições de notificação no Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>O portal do Azure AD suporta a utilização da API de domínio ForceDelete para eliminar domínios personalizados 

**Tipo:** Funcionalidade alterada  
**Categoria de serviço:** Gestão de Diretórios  
**Capacidade de produto:** Diretório

É com prazer que Anunciamos que agora pode utilizar o domínio de ForceDelete API para eliminar os nomes de domínio personalizado ao modo assíncrono mudar o nome de referências, como os utilizadores, grupos e aplicações do seu nome de domínio personalizado (contoso.com) para o (de nome de domínio predefinido inicial contoso.onmicrosoft.com).

Esta alteração ajuda a eliminar mais rapidamente os seus nomes de domínio personalizado se sua organização já não utiliza o nome, ou se precisar de utilizar o nome de domínio com o outro Azure AD.

Para obter mais informações, consulte [eliminar um nome de domínio personalizado](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---
