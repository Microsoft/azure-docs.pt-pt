---
title: Arquivo morto para o que há de novo no Azure Active Directory? | Microsoft Docs
description: As notas de versão de novidades na seção visão geral deste conjunto de conteúdo contêm 6 meses de atividade. Após 6 meses, os itens são removidos do artigo principal e colocados neste artigo de arquivamento.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fcf1e5966be7708e7c6278839cbfbe5d65cdffa
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149124"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Arquivo morto para o que há de novo no Azure Active Directory?

O artigo [novidades principais no Azure Active Directory? notas de versão](whats-new.md) contém atualizações dos últimos seis meses, enquanto este artigo contém todas as informações mais antigas.

O que há de novo no Azure Active Directory? as notas de versão fornecem informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de erros
- Funcionalidade preterida
- Planos de alterações

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

## <a name="march-2019"></a>Março de 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>O Identity Experience Framework e o suporte de política personalizada no Azure Active Directory B2C agora estão disponíveis (GA)

**Tipo:** Novo recurso  
**Categoria de serviço:** B2C – gerenciamento de identidade do consumidor  
**Funcionalidade do produto:** B2B/B2C

Agora você pode criar políticas personalizadas no Azure AD B2C, incluindo as seguintes tarefas, que têm suporte em escala e em nosso SLA do Azure:

- Criar e carregar percursos do usuário de autenticação personalizada usando políticas personalizadas.

- Descrever as viagens passo a passo do usuário como trocas entre provedores de declarações.

- Defina a ramificação condicional em percursos do usuário.

- Transforme e mapeie declarações para uso em decisões e comunicações em tempo real.

- Use os serviços habilitados para API REST em suas jornadas do usuário de autenticação personalizada. Por exemplo, com provedores de email, CRMs e sistemas de autorização proprietários.

- Federar com provedores de identidade que estão em conformidade com o protocolo OpenIDConnect. Por exemplo, com vários locatários do Azure AD, provedores de contas sociais ou provedores de verificação de dois fatores.

Para obter mais informações sobre como criar políticas personalizadas, consulte [notas do desenvolvedor para políticas personalizadas no Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) e leia a [postagem do blog de Alex Simon, incluindo estudos de caso](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – março de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em março de 2019, adicionamos esses 14 novos aplicativos com suporte de Federação à galeria de aplicativos:

[ISEC7 Mobile Exchange delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [sistema de auditoria baseado em explicação](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [PowerSchool de desempenho importante](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode ](https://cinode.com/), [Intranet íris](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit horizontes](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [tas](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Novos conectores de provisionamento Zscaler e Atlassian na galeria do Azure AD – março de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** integração de terceiros

Automatize a criação, atualização e exclusão de contas de usuário para os seguintes aplicativos:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler dois](https://aka.ms/ZscalerTwoProvisioning), [Zscaler três](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Para obter mais informações sobre como proteger melhor sua organização por meio do provisionamento automatizado de contas de usuário, confira automatizar o [provisionamento de usuário para aplicativos SaaS com o Azure ad](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Restaurar e gerenciar seus grupos excluídos do Office 365 no portal do AD do Azure

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerenciamento de grupo  
**Funcionalidade do produto:** Colaboração

Agora você pode exibir e gerenciar seus grupos excluídos do Office 365 no portal do Azure AD. Essa alteração ajuda a ver quais grupos estão disponíveis para restauração, além de permitir que você exclua permanentemente todos os grupos que não são necessários para sua organização.

Para obter mais informações, consulte [restauração expirada ou grupos excluídos](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>O logon único agora está disponível para aplicativos locais do Azure AD com segurança SAML por meio do proxy de aplicativo (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Proxy de aplicativo  
**Funcionalidade do produto:** Controle de acesso

Agora você pode fornecer uma experiência de SSO (logon único) para aplicativos locais, autenticados por SAML, junto com o acesso remoto a esses aplicativos por meio do proxy de aplicativo. Para obter mais informações sobre como configurar o SSO do SAML com seus aplicativos locais, consulte [logon único do SAML para aplicativos locais com o proxy de aplicativo (versão prévia)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Os aplicativos cliente em loops de solicitação serão interrompidos para melhorar a confiabilidade e a experiência do usuário

**Tipo:** Novo recurso  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário

Os aplicativos cliente podem emitir incorretamente centenas das mesmas solicitações de logon em um curto período de tempo. Essas solicitações, sejam elas bem-sucedidas ou não, todas contribuem para uma experiência de usuário ruim e cargas de trabalho aumentadas para o IDP, aumentando a latência para todos os usuários e reduzindo a disponibilidade do IDP.

Essa atualização envia um erro de `invalid_grant`: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` aos aplicativos cliente que emitem solicitações duplicadas várias vezes por um curto período de tempo, além do escopo da operação normal. Os aplicativos cliente que encontram esse problema devem mostrar um prompt interativo, exigindo que o usuário entre novamente. Para obter mais informações sobre essa alteração e sobre como corrigir seu aplicativo se ele encontrar esse erro, consulte [o que há de novo para autenticação?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Novos logs de auditoria a experiência do usuário agora está disponível

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios

Criamos uma nova página de **logs de auditoria** do Azure ad para ajudar a melhorar a legibilidade e como pesquisar suas informações. Para ver a página novos **logs de auditoria** , selecione logs de **auditoria** na seção **atividade** do Azure AD.

![Página novos logs de auditoria, com informações de exemplo](media/whats-new/audit-logs-page.png)

Para obter mais informações sobre a página novos **logs de auditoria** , consulte [auditoria de relatórios de atividade no portal de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Novos avisos e diretrizes para ajudar a impedir o bloqueio acidental de administrador de políticas de acesso condicional configuradas incorretamente

**Tipo:** Recurso alterado  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** Proteção de & de segurança de identidade

Para ajudar a impedir que os administradores bloqueiem acidentalmente seus próprios locatários por meio de políticas de acesso condicional configuradas incorretamente, criamos novos avisos e diretrizes atualizadas no portal do Azure. Para obter mais informações sobre as novas diretrizes, consulte [o que são dependências de serviço em Azure Active Directory acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Experiência aprimorada dos termos de uso do usuário final em dispositivos móveis

**Tipo:** Recurso alterado  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Controles

Atualizamos nossas experiências existentes de termos de uso para ajudar a melhorar o modo de revisar e consentir os termos de uso em um dispositivo móvel. Agora você pode ampliar e reduzir, voltar, baixar as informações e selecionar hiperlinks. Para obter mais informações sobre os termos de uso atualizados, consulte [Azure Active Directory recurso termos de uso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nova experiência de download de logs de atividades do Azure AD disponível

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios

Agora você pode baixar grandes quantidades de logs de atividade diretamente do portal do Azure. Essa atualização permite que você:

- Baixe até 250.000 linhas.

- Seja notificado após a conclusão do download.

- Personalize o nome do arquivo.

- Determine o formato de saída, JSON ou CSV.

Para obter mais informações sobre esse recurso, consulte [início rápido: baixar um relatório de auditoria usando o portal do Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Alteração significativa: atualizações para a avaliação de condição pelo Exchange ActiveSync (EAS)

**Tipo:** Planejar alteração  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** Controle de acesso

Estamos no processo de atualizar como o Exchange ActiveSync (EAS) avalia as seguintes condições:

- Local do usuário, com base no país, na região ou no endereço IP

- Risco de entrada

- Plataforma do dispositivo

Se você já usou essas condições em suas políticas de acesso condicional, lembre-se de que o comportamento da condição pode ser alterado. Por exemplo, se você usou anteriormente a condição de local de usuário em uma política, você pode achar que a política agora está sendo ignorada com base no local do usuário.

---

## <a name="february-2019"></a>Fevereiro de 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Criptografia de token SAML do Azure AD configurável (visualização pública) 

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** EXTERNO

Agora você pode configurar qualquer aplicativo SAML com suporte para receber tokens SAML criptografados. Quando configurado e usado com um aplicativo, o AD do Azure criptografa as asserções SAML emitidas usando uma chave pública Obtida de um certificado armazenado no Azure AD.

Para obter mais informações sobre como configurar a criptografia de token SAML, consulte [Configurar a criptografia de token SAML do Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Criar uma revisão de acesso para grupos ou aplicativos usando revisões de acesso do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Controles

Agora você pode incluir vários grupos ou aplicativos em uma única revisão de acesso do Azure AD para associação de grupo ou atribuição de aplicativo. As revisões de acesso com vários grupos ou aplicativos são configuradas usando as mesmas configurações e todos os revisores incluídos são notificados ao mesmo tempo.

Para obter mais informações sobre como criar uma análise de acesso usando revisões de acesso do Azure AD, consulte [criar uma revisão de acesso de grupos ou aplicativos nas revisões de acesso do Azure ad](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – fevereiro de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em fevereiro de 2019, adicionamos esses 27 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MINDTICKLE](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [Fat Finger](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [enstack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Permissão Click, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [sísmica](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Share A sonho](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [WebMethods Integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [conhecimento Em qualquer lugar LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [campus da UO](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [NetOp portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [Smartvid.Io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud por Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp Productivity Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Registro de MFA/SSPR combinado aprimorado

**Tipo:** Recurso alterado  
**Categoria de serviço:** Redefinição de senha de autoatendimento  
**Funcionalidade do produto:** Autenticação do usuário

Em resposta aos comentários dos clientes, aprimoramos a experiência combinada de visualização do registro MFA/SSPR, ajudando seus usuários a registrarem com mais rapidez suas informações de segurança tanto para MFA quanto para SSPR. 

**Para ativar a experiência aprimorada para os seus usuários hoje, siga estas etapas:**

1. Como administrador global ou administrador de usuários, entre no portal do Azure e vá para **Azure Active Directory > configurações de usuário > gerenciar configurações para recursos de visualização do painel de acesso**. 

2. Nos **usuários que podem usar os recursos de visualização para registrar e gerenciar informações de segurança –** opção de atualização, escolha Ativar os recursos para um **grupo de usuários selecionado** ou para **todos os usuários**.

Nas próximas semanas, removeremos a capacidade de ativar a experiência de visualização de registro MFA/SSPR combinada antiga para locatários que ainda não estão ativados.

**Para ver se o controle será removido para seu locatário, siga estas etapas:**

1. Como administrador global ou administrador de usuários, entre no portal do Azure e vá para **Azure Active Directory > configurações de usuário > gerenciar configurações para recursos de visualização do painel de acesso**.  

2. Se os **usuários que podem usar os recursos de visualização para registrar e gerenciar informações de segurança** estiverem definidos como **nenhum**, a opção será removida do seu locatário.

Independentemente de você ter ativado a antiga experiência de visualização de registro MFA/SSPR combinada para usuários ou não, a experiência antiga será desativada em uma data futura. Por isso, é altamente recomendável que você migre para a nova experiência aprimorada assim que possível.

Para obter mais informações sobre a experiência de registro aprimorada, consulte os [aprimoramentos legais para a experiência combinada do Azure AD e do registro de redefinição de senha](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Experiência de gerenciamento de política atualizada para fluxos de usuário

**Tipo:** Recurso alterado  
**Categoria de serviço:** B2C – gerenciamento de identidade do consumidor  
**Funcionalidade do produto:** B2B/B2C

Atualizamos o processo de criação e gerenciamento de política para fluxos de usuário (anteriormente conhecidos como políticas internas) mais fácil. Essa nova experiência agora é o padrão para todos os seus locatários do Azure AD.

Você pode fornecer comentários e sugestões adicionais usando os ícones Smiley ou rosto triste na área **enviar comentários** na parte superior da tela do Portal.

Para obter mais informações sobre a nova experiência de gerenciamento de política, consulte o blog [Azure ad B2C agora tem personalização de JavaScript e muitos outros recursos novos](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) .

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Escolha as versões de elemento de página específicas fornecidas pelo Azure AD B2C

**Tipo:** Novo recurso  
**Categoria de serviço:** B2C – gerenciamento de identidade do consumidor  
**Funcionalidade do produto:** B2B/B2C

Agora você pode escolher uma versão específica dos elementos da página fornecidos por Azure AD B2C. Ao selecionar uma versão específica, você pode testar suas atualizações antes que elas apareçam em uma página e você pode obter um comportamento previsível. Além disso, agora você pode optar por impor versões de página específicas para permitir personalizações de JavaScript. Para ativar esse recurso, vá para a página **Propriedades** em seus fluxos de usuário.

Para obter mais informações sobre como escolher versões específicas de elementos de página, consulte o blog [Azure ad B2C agora tem personalização de JavaScript e muitos outros recursos novos](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) .

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Requisitos configuráveis de senha do usuário final para B2C (GA)

**Tipo:** Novo recurso  
**Categoria de serviço:** B2C – gerenciamento de identidade do consumidor  
**Funcionalidade do produto:** B2B/B2C

Agora você pode configurar a complexidade de senha de sua organização para seus usuários finais, em vez de ter que usar sua política de senha nativa do Azure AD. Na folha **Propriedades** de seus fluxos de usuário (anteriormente conhecido como suas políticas internas), você pode escolher uma complexidade de senha **simples** ou **forte**, ou pode criar um conjunto **personalizado** de requisitos.

Para obter mais informações sobre a configuração de requisito de complexidade de senha, consulte [Configurar requisitos de complexidade para senhas no Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Novos modelos padrão para experiências de autenticação personalizada com marca

**Tipo:** Novo recurso  
**Categoria de serviço:** B2C – gerenciamento de identidade do consumidor  
**Funcionalidade do produto:** B2B/B2C

Você pode usar nossos novos modelos padrão, localizados na folha **layouts de página** de seus fluxos de usuário (anteriormente conhecidos como políticas internas), para criar uma experiência de autenticação com marca personalizada para seus usuários.

Para obter mais informações sobre como usar os modelos, consulte [Azure ad B2C agora tem a personalização de JavaScript e muitos outros recursos novos](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Janeiro de 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory colaboração B2B usando a autenticação de senha única (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C

Apresentamos a OTP (autenticação de senha única) para usuários convidados B2B que não podem ser autenticados por outros meios como o Azure AD, um conta Microsoft (MSA) ou Google Federation. Esse novo método de autenticação significa que os usuários convidados não precisam criar um novo conta Microsoft. Em vez disso, ao resgatar um convite ou acessar um recurso compartilhado, um usuário convidado pode solicitar que um código temporário seja enviado para um endereço de email. Usando esse código temporário, o usuário convidado pode continuar a entrar.

Para obter mais informações, consulte [autenticação de senha de uso único de email (versão prévia)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) e o blog, o [Azure ad torna o compartilhamento e a colaboração contínuos para qualquer usuário com qualquer conta](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Novas configurações de cookie do Azure Proxy de Aplicativo do AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Proxy de aplicativo  
**Funcionalidade do produto:** Controle de acesso

Apresentamos três novas configurações de cookie, disponíveis para seus aplicativos que são publicados por meio do proxy de aplicativo:

- **Use o cookie somente HTTP.** Define o sinalizador **HTTPOnly** em seus cookies de acesso e sessão de proxy de aplicativo. Ativar essa configuração fornece benefícios de segurança adicionais, como ajudar a impedir a cópia ou modificação de cookies por meio de scripts do lado do cliente. Recomendamos que você ative esse sinalizador (escolha **Sim**) para obter os benefícios adicionais.

- **Usar cookie seguro.** Define o sinalizador de **segurança** em seus cookies de acesso e sessão de proxy de aplicativo. Ativar essa configuração fornece benefícios de segurança adicionais, garantindo que os cookies sejam transmitidos apenas por canais seguros de TLS, como HTTPS. Recomendamos que você ative esse sinalizador (escolha **Sim**) para obter os benefícios adicionais.

- **Use o cookie persistente.** Impede que os cookies de acesso expirem quando o navegador da Web é fechado. Esses cookies duram pelo tempo de vida do token de acesso. No entanto, os cookies serão redefinidos se o tempo de expiração for atingido ou se o usuário excluir manualmente o cookie. Recomendamos que você mantenha a configuração padrão **não**, ativando apenas a configuração de aplicativos mais antigos que não compartilham cookies entre processos.

Para obter mais informações sobre os novos cookies, consulte [configurações de cookie para acessar aplicativos locais no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – janeiro de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em janeiro de 2019, adicionamos esses 35 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [paleta de talentos](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet Access Administrator](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [expiração de vencimento](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [ Verbo](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [serviço de nuvem Picco](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [viável](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease ](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE Insight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ares for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 para Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager ](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [visitado](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry Alp](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Novos aprimoramentos de Azure AD Identity Protection (visualização pública)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** Proteção de & de segurança de identidade

Estamos empolgados em anunciar que adicionamos os seguintes aprimoramentos à oferta de visualização pública Azure AD Identity Protection, incluindo:

- Uma interface de usuário atualizada e mais integrada

- APIs adicionais

- Avaliação de risco aprimorada por meio do Machine Learning

- Alinhamento de todo o produto entre usuários arriscados e entradas arriscadas

Para obter mais informações sobre os aprimoramentos, consulte [o que é Azure Active Directory Identity Protection (atualizado)?](https://aka.ms/IdentityProtectionDocs) para saber mais e compartilhar suas ideias por meio de prompts no produto.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Novo recurso de bloqueio de aplicativo para o aplicativo Microsoft Authenticator em dispositivos iOS e Android

**Tipo:** Novo recurso  
**Categoria de serviço:** Microsoft Authenticator aplicativo  
**Funcionalidade do produto:** Proteção de & de segurança de identidade

Para manter suas senhas de uso único, informações de aplicativo e configurações de aplicativo mais seguras, você pode ativar o recurso de bloqueio de aplicativo no aplicativo Microsoft Authenticator. Ativar o bloqueio do aplicativo significa que você será solicitado a autenticar usando seu PIN ou biométrica toda vez que abrir o aplicativo Microsoft Authenticator.

Para obter mais informações, consulte as [perguntas frequentes do aplicativo Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Recursos de exportação do PIM (Azure AD Privileged Identity Management avançado)

**Tipo:** Novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management

Os administradores do Privileged Identity Management (PIM) agora podem exportar todas as atribuições de função ativas e qualificadas para um recurso específico, que inclui atribuições de função para todos os recursos filho. Anteriormente, era difícil para os administradores obter uma lista completa de atribuições de função para uma assinatura e tinham de exportar atribuições de função para cada recurso específico.

Para obter mais informações, consulte [Exibir histórico de atividades e de auditoria para funções de recurso do Azure no PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Novembro/Dezembro de 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Usuários removidos do escopo de sincronização não são mais alternados para contas somente de nuvem

**Tipo:** Fixado  
**Categoria de serviço:** Gerenciamento de usuários  
**Funcionalidade do produto:** Active

>[!Important]
>Ouvimos e entendemos sua frustração por causa dessa correção. Portanto, revertemos essa alteração até o momento em que podemos facilitar a sua implementação em sua organização.

Corrigimos um bug no qual o sinalizador DirSyncEnabled de um usuário seria erroneamente alternado para **falso** quando o objeto Active Directory Domain Services (AD DS) foi excluído do escopo de sincronização e, em seguida, movido para a lixeira no Azure AD no seguinte ciclo de sincronização. Como resultado dessa correção, se o usuário for excluído do escopo de sincronização e depois restaurado da lixeira do Azure AD, a conta de usuário permanecerá como sincronizada do AD local, conforme esperado, e não poderá ser gerenciada na nuvem, pois sua fonte de autoridade (SoA) permanece como AD local.

Antes dessa correção, houve um problema quando o sinalizador DirSyncEnabled foi alternado para false. Ele deu à impressão incorreta que essas contas foram convertidas em objetos somente de nuvem e que as contas poderiam ser gerenciadas na nuvem. No entanto, as contas ainda reteram sua SoA como local e todas as propriedades sincronizadas (atributos de sombra) provenientes do AD local. Essa condição causou vários problemas no Azure AD e outras cargas de trabalho de nuvem (como o Exchange Online) que se espera tratar dessas contas como sincronizadas do AD, mas que agora estavam se comportando como contas somente de nuvem.

Neste momento, a única maneira de realmente converter uma conta sincronizada do AD para uma conta somente em nuvem é desabilitar o DirSync no nível do locatário, que dispara uma operação de back-end para transferir a SoA. Esse tipo de alteração SoA requer (mas não se limita a) a limpeza de todos os atributos relacionados no local (como LastDirSyncTime e atributos de sombra) e o envio de um sinal para outras cargas de trabalho de nuvem para que seu respectivo objeto seja convertido em uma conta somente em nuvem .

Essa correção, consequentemente, impede que as atualizações diretas no atributo imutável de um usuário sejam sincronizadas do AD, o que em alguns cenários no passado era necessário. Por design, a imutávelid de um objeto no Azure AD, como o nome indica, deve ser imutável. Novos recursos implementados em Azure AD Connect Health e Azure AD Connect cliente de sincronização estão disponíveis para resolver esses cenários:

- **Atualização imutável de grande escala para muitos usuários em uma abordagem preparada**
  
  Por exemplo, você precisa fazer um longo AD DS migração entre florestas. Solução: Use Azure AD Connect para **Configurar a âncora de origem** e, conforme o usuário migra, copie os valores imutáveis existentes do Azure ad para o atributo ms-DS-Consistency-GUID do usuário AD DS local da nova floresta. Para obter mais informações, consulte [usando MS-DS-ConsistencyGuid como sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Atualizações imutáveis de grande escala para muitos usuários em uma única captura**

  Por exemplo, ao implementar Azure AD Connect você cometer um erro e agora precisa alterar o atributo SourceAnchor. Solução: desabilite o DirSync no nível do locatário e limpe todos os valores imutáveis inválidos. Para obter mais informações, consulte [desativar a sincronização de diretório para o Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Recorrespondência de usuário local com um usuário existente no Azure ad** Por exemplo, um usuário que foi recriado no AD DS gera uma duplicata na conta do Azure AD em vez de refazer a correspondência com uma conta existente do Azure AD (objeto órfão). Solução: Use Azure AD Connect Health na portal do Azure para remapear a âncora de origem/imutável. Para obter mais informações, consulte [cenário de objeto órfão](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Alteração significativa: atualizações para o esquema de logs de auditoria e de entrada por meio do Azure Monitor

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios

Atualmente, estamos publicando a auditoria e os fluxos de log de entrada por meio de Azure Monitor, para que você possa integrar diretamente os arquivos de log com suas ferramentas SIEM ou com Log Analytics. Com base em seus comentários e na preparação para o comunicado de disponibilidade geral do recurso, estamos fazendo as seguintes alterações em nosso esquema. Essas alterações de esquema e suas atualizações de documentação relacionadas ocorrerão na primeira semana de Janeiro.

#### <a name="new-fields-in-the-audit-schema"></a>Novos campos no esquema de auditoria
Estamos adicionando um novo campo de **tipo de operação** para fornecer o tipo de operação executada no recurso. Por exemplo, **Adicionar**, **Atualizar**ou **excluir**.

#### <a name="changed-fields-in-the-audit-schema"></a>Campos alterados no esquema de auditoria
Os campos a seguir estão sendo alterados no esquema de auditoria:

|Nome do campo|O que mudou|Valores antigos|Novos valores|
|----------|------------|----------|----------|
|Categoria|Esse era o campo **nome do serviço** . Agora é o campo de **categorias de auditoria** . O **nome do serviço** foi renomeado para o campo **loggedByService** .|<ul><li>Aprovisionamento de Contas</li><li>Diretório do Núcleo</li><li>Redefinição de senha de autoatendimento</li></ul>|<ul><li>Gestão de Utilizadores</li><li>Gestão de Grupos</li><li>Gerenciamento de aplicativos</li></ul>|
|targetResources|Inclui **TargetResourceType** no nível superior.|&nbsp;|<ul><li>Política</li><li>Aplicação</li><li>Utilizador</li><li>Grupo</li></ul>|
|loggedByService|Fornece o nome do serviço que gerou o log de auditoria.|Null|<ul><li>Aprovisionamento de Contas</li><li>Diretório do Núcleo</li><li>Reposição personalizada de palavra-passe</li></ul>|
|Resultado|Fornece o resultado dos logs de auditoria. Anteriormente, isso foi enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li></ul>|<ul><li>Êxito</li><li>Falha</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Campos alterados no esquema de entrada
Os campos a seguir estão sendo alterados no esquema de entrada:

|Nome do campo|O que mudou|Valores antigos|Novos valores|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Esse era o campo **conditionalaccessPolicies** . Agora é o campo **appliedConditionalAccessPolicies** .|Sem alteração|Sem alteração|
|conditionalAccessStatus|Fornece o resultado do status da política de acesso condicional na entrada. Anteriormente, isso foi enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Êxito</li><li>Falha</li><li>Não aplicado</li><li>Desativado</li></ul>|
|appliedConditionalAccessPolicies: resultado|Fornece o resultado do status da política de acesso condicional individual na entrada. Anteriormente, isso foi enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Êxito</li><li>Falha</li><li>Não aplicado</li><li>Desativado</li></ul>|

Para obter mais informações sobre o esquema, consulte [interpretar o esquema de logs de auditoria do Azure AD no Azure monitor (versão prévia)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Melhorias de proteção de identidade para o modelo de aprendizado de máquina supervisionado e o mecanismo de Pontuação de risco

**Tipo:** Recurso alterado  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** Pontuações de risco

Melhorias no mecanismo de avaliação de risco e usuário relacionado à proteção de identidade podem ajudar a melhorar a precisão e a cobertura de riscos do usuário. Os administradores podem notar que o nível de risco do usuário não está mais diretamente vinculado ao nível de risco de detecções específicas e que há um aumento no número e no nível de eventos de entrada arriscados.

As detecções de risco agora são avaliadas pelo modelo de aprendizado de máquina supervisionado, que calcula o risco do usuário usando recursos adicionais das entradas do usuário e um padrão de detecções. Com base nesse modelo, o administrador pode encontrar usuários com pontuações de alto risco, mesmo se as detecções associadas a esse usuário forem de risco baixo ou médio. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Os administradores podem redefinir suas próprias senhas usando o aplicativo Microsoft Authenticator (visualização pública)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Redefinição de senha de autoatendimento  
**Funcionalidade do produto:** Autenticação do usuário

Os administradores do Azure AD agora podem redefinir suas próprias senhas usando as notificações do aplicativo Microsoft Authenticator ou um código de qualquer token de hardware ou aplicativo do autenticador móvel. Para redefinir sua própria senha, os administradores agora poderão usar dois dos seguintes métodos:

- Notificação de Microsoft Authenticator aplicativo

- Outro código de token de hardware/aplicativo do Mobile Authenticator

- E-mail

- Chamada telefônica

- Mensagem de texto

Para obter mais informações sobre como usar o aplicativo Microsoft Authenticator para redefinir senhas, consulte [redefinição de senha de autoatendimento do Azure ad-aplicativo móvel e SSPR (versão prévia)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nova função de administrador de dispositivo de nuvem do Azure AD (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerenciamento e registro de dispositivos  
**Funcionalidade do produto:** Controle de acesso

Os administradores podem atribuir usuários à nova função de administrador de dispositivo de nuvem para executar tarefas de administrador de dispositivo de nuvem. Os usuários atribuídos à função Administradores de dispositivo de nuvem podem habilitar, desabilitar e excluir dispositivos no Azure AD, juntamente com a capacidade de ler as chaves do BitLocker do Windows 10 (se houver) no portal do Azure.

Para obter mais informações sobre funções e permissões, consulte [atribuindo funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Gerenciar seus dispositivos usando o novo carimbo de data/hora de atividade no Azure AD (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerenciamento e registro de dispositivos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida do dispositivo

Percebemos que, com o tempo, você deve atualizar e desativar os dispositivos de sua organização no Azure AD, para evitar ter dispositivos obsoletos em seu ambiente. Para ajudar com esse processo, o Azure AD agora atualiza seus dispositivos com um novo carimbo de data/hora de atividade, ajudando você a gerenciar o ciclo de vida do dispositivo.

Para obter mais informações sobre como obter e usar esse carimbo de data/hora, consulte [como gerenciar os dispositivos obsoletos no Azure ad](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Os administradores podem exigir que os usuários aceitem os termos de uso em cada dispositivo

**Tipo:** Novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Controles
 
Os administradores agora podem ativar a opção **exigir que os usuários consentissem em cada dispositivo** para exigir que seus usuários aceitem seus termos de uso em cada dispositivo que estão usando em seu locatário.

Para obter mais informações, consulte a [seção termos de uso por dispositivo do Azure Active Directory recurso termos de uso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Os administradores podem configurar os termos de uso para expirar com base em um agendamento recorrente

**Tipo:** Novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Controles
 

Agora, os administradores podem ativar a opção **expirar consentis** para fazer com que os termos de uso expirem para todos os seus usuários com base em sua agenda recorrente especificada. A agenda pode ser anual, bi-anual, trimestral ou mensal. Depois que os termos de uso expirarem, os usuários deverão aceitar novamente.

Para obter mais informações, consulte a [seção adicionar termos de uso do recurso Azure Active Directory termos de uso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Os administradores podem configurar os termos de uso para expirar com base na agenda de cada usuário

**Tipo:** Novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Controles

Os administradores agora podem especificar uma duração que o usuário deve reaceitar os termos de uso. Por exemplo, os administradores podem especificar que os usuários devem reaceitar os termos de uso a cada 90 dias.

Para obter mais informações, consulte a [seção adicionar termos de uso do recurso Azure Active Directory termos de uso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Novos emails de Azure AD Privileged Identity Management (PIM) para funções de Azure Active Directory

**Tipo:** Novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Os clientes que usam o Azure AD Privileged Identity Management (PIM) agora podem receber um email de resumo semanal, incluindo as seguintes informações para os últimos sete dias:

- Visão geral das principais atribuições de função qualificadas e permanentes

- Número de usuários ativando funções

- Número de usuários atribuídos a funções no PIM

- Número de usuários atribuídos a funções fora do PIM

- Número de usuários "tornados permanentes" no PIM

Para obter mais informações sobre o PIM e as notificações por email disponíveis, consulte [notificações por email no PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>O licenciamento baseado em grupo agora está disponível para o público geral

**Tipo:** Recurso alterado  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Active

O licenciamento baseado em grupo está fora de visualização pública e agora está disponível para o público em geral. Como parte desta versão geral, tornamos esse recurso mais escalonável e adicionamos a capacidade de reprocessar atribuições de licenciamento baseado em grupo para um único usuário e a capacidade de usar o licenciamento baseado em grupo com licenças do Office 365 E3/a3.

Para obter mais informações sobre licenciamento baseado em grupo, consulte [o que é licenciamento baseado em grupo no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – novembro de 2018

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em novembro de 2018, adicionamos esses 26 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [getali](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [gra-PE](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [campus infinito](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [ Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [descompasso](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business central 365](https://accounting.zenegy.com/), [Everbridge Member portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [ideo](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [pico](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [SSO de entrada](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [aplicativos de plex-teste clássico ](https://test.plexonline.com/signon), [Aplicativos de plex – aplicativos clássicos](https://www.plexonline.com/signon), [Plex-teste de UX](https://test.cloud.plex.com/sso), [aplicativos de plex – UX](https://cloud.plex.com/sso), [aplicativos de plex – iam](https://accounts.plex.com/), [Childcare registros de artesanatos, participação & sistema de controle financeiro](https://getcrafts.ca/craftsregistration) 

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Outubro de 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Os logs do Azure AD agora funcionam com o Azure Log Analytics (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios

Estamos empolgados em anunciar que agora você pode encaminhar seus logs do Azure AD para o Azure Log Analytics! Esse recurso mais solicitado ajuda a fornecer um acesso ainda melhor à análise para seus negócios, operações e segurança, bem como uma maneira de ajudar a monitorar sua infraestrutura. Para obter mais informações, consulte o blog [Azure Active Directory logs de atividades no Azure log Analytics agora disponível](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) .

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – outubro de 2018

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em outubro de 2018, adicionamos esses 14 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[Meus pontos de premiação](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), teclado, [ON24 ambiente virtual](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler três](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services notificações por email

**Tipo:** Novo recurso  
**Categoria de serviço:** Azure AD Domain Services  
**Funcionalidade do produto:** Azure AD Domain Services

Azure AD Domain Services fornece alertas sobre o portal do Azure sobre problemas ou configurações incorretas com seu domínio gerenciado. Esses alertas incluem guias passo a passo para que você possa tentar corrigir os problemas sem precisar entrar em contato com o suporte.

A partir de outubro, você poderá personalizar as configurações de notificação para seu domínio gerenciado, de modo que, quando novos alertas ocorrerem, um email seja enviado a um grupo designado de pessoas, eliminando a necessidade de verificar constantemente as atualizações no Portal.

Para obter mais informações, consulte [configurações de notificação no Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>O portal do AD do Azure dá suporte ao uso da API de domínio ForceDelete para excluir domínios personalizados 

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerenciamento de diretório  
**Funcionalidade do produto:** Active

Temos o prazer de anunciar que agora você pode usar a API de domínio ForceDelete para excluir os nomes de domínio personalizados por meio da renomeação assíncrona de referências, como usuários, grupos e aplicativos de seu nome de domínio personalizado (contoso.com) de volta para o nome de domínio padrão inicial ( contoso.onmicrosoft.com).

Essa alteração ajudará você a excluir mais rapidamente seus nomes de domínio personalizados se sua organização não usar mais o nome ou se você precisar usar o nome de domínio com outro Azure AD.

Para obter mais informações, consulte [excluir um nome de domínio personalizado](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Setembro de 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Permissões de função de administrador atualizadas para grupos dinâmicos

**Tipo:** Fixado  
**Categoria de serviço:** Gerenciamento de grupo  
**Funcionalidade do produto:** Colaboração

Corrigimos um problema para que as funções de administrador específicas agora possam criar e atualizar regras de associação dinâmica, sem a necessidade de ser o proprietário do grupo.

As funções são:

- Administrador global

- Administrador do Intune

- Administrador do usuário

Para obter mais informações, consulte [criar um grupo dinâmico e verificar o status](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Definições de configuração de SSO (logon único) simplificadas para alguns aplicativos de terceiros

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** EXTERNO

Percebemos que a configuração do SSO (logon único) para aplicativos SaaS (software como serviço) pode ser desafiadora devido à natureza exclusiva de cada configuração de aplicativos. Criamos uma experiência de configuração simplificada para preencher automaticamente as definições de configuração de SSO para os seguintes aplicativos SaaS de terceiros:

- Zendesk

- ArcGis online

- Jamf Pro

Para começar a usar essa experiência com um clique, acesse a página de **configuração de SSO** do **portal do Azure** > para o aplicativo. Para obter mais informações, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory-onde os dados estão localizados? Web

**Tipo:** Novo recurso  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** GoLocal

Selecione a região da sua empresa na página **Azure Active Directory-onde é sua localização de dados** para exibir qual Datacenter do Azure aloja seus dados do Azure AD em repouso para todos os serviços do Azure AD. Você pode filtrar as informações por serviços específicos do Azure AD para a região da sua empresa.

Para acessar esse recurso e obter mais informações, consulte [Azure Active Directory-onde estão os dados localizados](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Novo plano de implantação disponível para o painel de acesso meus aplicativos

**Tipo:** Novo recurso  
**Categoria de serviço:** Meus aplicativos  
**Funcionalidade do produto:** EXTERNO

Confira o novo plano de implantação disponível para o painel de acesso meus aplicativos (https://aka.ms/deploymentplans).
O painel de acesso meus aplicativos fornece aos usuários um único local para localizar e acessar seus aplicativos. Esse portal também fornece aos usuários oportunidades de autoatendimento, como solicitar acesso a aplicativos e grupos ou gerenciar o acesso a esses recursos em nome de outros.

Para obter mais informações, consulte [o que é o portal meus aplicativos?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nova guia de solução de problemas e suporte na página de logs de entradas do portal do Azure

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios

A nova guia de **solução de problemas e suporte** na página de **entradas** do portal do Azure, destina-se a ajudar administradores e engenheiros de suporte a solucionar problemas relacionados a entradas do Azure AD. Essa nova guia fornece o código de erro, a mensagem de erro e as recomendações de correção (se houver) para ajudar a resolver o problema. Se você não conseguir resolver o problema, também forneceremos uma nova maneira de criar um tíquete de suporte usando a experiência de **copiar para área de transferência** , que popula os campos de ID e data da **solicitação** **(UTC)** para o arquivo de log em seu tíquete de suporte.  

![Logs de entrada mostrando a nova guia](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Suporte aprimorado para propriedades de extensão personalizadas usadas para criar regras de associação dinâmica

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerenciamento de grupo  
**Funcionalidade do produto:** Colaboração

Com essa atualização, agora você pode clicar no link **obter propriedades de extensão personalizadas** do construtor de regras do grupo de usuários dinâmico, inserir sua ID de aplicativo exclusiva e receber a lista completa de propriedades de extensão personalizadas a serem usadas ao criar uma regra de associação dinâmica para os usuários. Essa lista também pode ser atualizada para obter todas as novas propriedades de extensão personalizadas para esse aplicativo.

Para obter mais informações sobre como usar propriedades de extensão personalizadas para regras de associação dinâmica, consulte [Propriedades de extensão e propriedades de extensão personalizadas](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos cliente aprovados para acesso condicional baseado em aplicativo do Azure AD

**Tipo:** Planejar alteração  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** Segurança e proteção de identidade

Os aplicativos a seguir estão na lista de [aplicativos cliente aprovados](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

Para obter mais informações, veja:

- [Acesso condicional com base no aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Novo suporte para redefinição de senha de autoatendimento da tela de bloqueio do Windows 7/8/8.1

**Tipo:** Novo recurso  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** Autenticação do usuário

Depois de configurar esse novo recurso, os usuários verão um link para redefinir sua senha na tela de **bloqueio** de um dispositivo que executa o Windows 7, o Windows 8 ou o Windows 8.1. Ao clicar nesse link, o usuário é guiado pelo mesmo fluxo de redefinição de senha do navegador da Web.

Para obter mais informações, consulte [como habilitar a redefinição de senha do Windows 7, 8 e 8,1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Aviso de alteração: os códigos de autorização não estarão mais disponíveis para reutilização 

**Tipo:** Planejar alteração  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário

A partir de 15 de novembro de 2018, o Azure AD irá parar de aceitar os códigos de autenticação usados anteriormente para aplicativos. Essa alteração de segurança ajuda a colocar o Azure AD em linha com a especificação OAuth e será aplicado nos pontos de extremidade v1 e v2.

Se seu aplicativo reutiliza códigos de autorização para obter tokens para vários recursos, recomendamos que você use o código para obter um token de atualização e, em seguida, use esse token de atualização para adquirir tokens adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez, mas os tokens de atualização podem ser usados várias vezes em vários recursos. Um aplicativo que tenta reutilizar um código de autenticação durante o fluxo de código OAuth receberá um erro invalid_grant.

Para essa e outras alterações relacionadas a protocolos, consulte [a lista completa das novidades para autenticação](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – setembro de 2018

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em setembro de 2018, adicionamos esses 16 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [coencontro software de recrutamento](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [floco de neve](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), [NavigoCloud, figma, join.me](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [RACKSPACE SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO para Azure, surveymonkey, [reunir](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Suporte para métodos adicionais de transformações de declarações

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** EXTERNO

Apresentamos novos métodos de transformação de declaração, ToLower () e ToUpper (), que podem ser aplicados a tokens SAML da página de **configuração de logon único** baseada em SAML.

Para obter mais informações, consulte [como personalizar declarações emitidas no token SAML para aplicativos empresariais no Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Interface do usuário atualizada de configuração de aplicativo baseado em SAML (versão prévia)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** EXTERNO

Como parte de nossa interface do usuário atualizada de configuração de aplicativo baseado em SAML, você obterá:

- Uma experiência de explicação explicativa atualizada para configurar seus aplicativos baseados em SAML.

- Mais visibilidade sobre o que está ausente ou incorreto na sua configuração.

- A capacidade de adicionar vários endereços de email para a notificação de certificado de expiração.

- Novos métodos de transformação de declaração, ToLower () e ToUpper () e muito mais.

- Uma maneira de carregar seu próprio certificado de assinatura de token para seus aplicativos empresariais.

- Uma maneira de definir o formato NameID para aplicativos SAML e uma maneira de definir o valor NameID como extensões de diretório.

Para ativar esse modo de exibição atualizado, clique no link **Experimente nossa nova experiência** na parte superior da página **logon único** . Para obter mais informações, consulte [tutorial: configurar logon único baseado em SAML para um aplicativo com Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).

---

## <a name="august-2018"></a>Agosto de 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Alterações em Azure Active Directory intervalos de endereços IP

**Tipo:** Planejar alteração  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Plataforma

Estamos introduzindo intervalos IP maiores para o Azure AD, o que significa que, se você configurou intervalos de endereços IP do Azure AD para seus firewalls, roteadores ou grupos de segurança de rede, precisará atualizá-los. Estamos fazendo essa atualização para que você não precise alterar as configurações de intervalo de IP de firewall, roteador ou grupos de segurança de rede novamente quando o Azure AD adiciona novos pontos de extremidade. 

O tráfego de rede está mudando para esses novos intervalos nos próximos dois meses. Para continuar com o serviço ininterrupto, você deve adicionar esses valores atualizados aos seus endereços IP antes de 10 de setembro de 2018:

- 20.190.128.0/18 

- 40.126.0.0/18 

É altamente recomendável não remover os intervalos de endereços IP antigos até que todo o tráfego de rede tenha sido movido para os novos intervalos. Para obter atualizações sobre a movimentação e saber quando você pode remover os intervalos antigos, consulte [URLs do Office 365 e intervalos de endereços IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Aviso de alteração: os códigos de autorização não estarão mais disponíveis para reutilização 

**Tipo:** Planejar alteração  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário

A partir de 15 de novembro de 2018, o Azure AD irá parar de aceitar os códigos de autenticação usados anteriormente para aplicativos. Essa alteração de segurança ajuda a colocar o Azure AD em linha com a especificação OAuth e será aplicado nos pontos de extremidade v1 e v2.

Se seu aplicativo reutiliza códigos de autorização para obter tokens para vários recursos, recomendamos que você use o código para obter um token de atualização e, em seguida, use esse token de atualização para adquirir tokens adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez, mas os tokens de atualização podem ser usados várias vezes em vários recursos. Um aplicativo que tenta reutilizar um código de autenticação durante o fluxo de código OAuth receberá um erro invalid_grant.

Para essa e outras alterações relacionadas a protocolos, consulte [a lista completa das novidades para autenticação](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Gerenciamento de informações de segurança convergido para SSPR (senha de autoatendimento) e MFA (autenticação multifator)

**Tipo:** Novo recurso  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** Autenticação do usuário
 
Esse novo recurso ajuda as pessoas a gerenciar suas informações de segurança (como número de telefone, aplicativo móvel e assim por diante) para SSPR e MFA em um único local e experiência; em comparação com o anterior, onde era feito em dois locais diferentes.

Essa experiência convergida também funciona para pessoas que usam o SSPR ou o MFA. Além disso, se sua organização não impor o registro de MFA ou SSPR, as pessoas ainda poderão registrar os métodos de informações de segurança MFA ou SSPR permitidos por sua organização no portal meus aplicativos.

Esta é uma visualização pública opcional. Os administradores podem ativar a nova experiência (se desejado) para um grupo selecionado ou para todos os usuários em um locatário. Para obter mais informações sobre a experiência convergida, consulte o [blog da experiência convergida](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nova configuração de cookies somente HTTP em aplicativos de proxy de aplicativo do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Proxy de aplicativo  
**Funcionalidade do produto:** Controle de acesso

Há uma nova configuração chamada, **cookies somente http** em seus aplicativos de proxy de aplicativo. Essa configuração ajuda a fornecer segurança extra, incluindo o sinalizador HTTPOnly no cabeçalho de resposta HTTP para acesso ao proxy de aplicativo e cookies de sessão, parando o acesso ao cookie de um script do lado do cliente e impedindo ainda mais ações como copiar ou modificando o cookie. Embora esse sinalizador não tenha sido usado anteriormente, seus cookies sempre foram criptografados e transmitidos usando uma conexão SSL para ajudar a proteger contra modificações inadequadas.

Essa configuração não é compatível com aplicativos que usam controles ActiveX, como Área de Trabalho Remota. Se você estiver nessa situação, recomendamos que você desative essa configuração.

Para obter mais informações sobre a configuração de cookies somente HTTP, consulte [publicar aplicativos usando o Azure proxy de aplicativo do AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) para recursos do Azure dá suporte aos tipos de recursos do grupo de gerenciamento

**Tipo:** Novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
A ativação Just-in-time e as configurações de atribuição agora podem ser aplicadas a tipos de recursos do grupo de gerenciamento, assim como você já faz para assinaturas, grupos de recursos e recursos (como VMs, serviços de aplicativos e muito mais). Além disso, qualquer pessoa com uma função que forneça acesso de administrador para um grupo de gerenciamento pode descobrir e gerenciar esse recurso no PIM.

Para obter mais informações sobre o PIM e os recursos do Azure, consulte [descobrir e gerenciar recursos do Azure usando o Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>O acesso ao aplicativo (versão prévia) fornece acesso mais rápido ao portal do AD do Azure

**Tipo:** Novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Hoje, ao ativar uma função usando o PIM, pode levar mais de 10 minutos para que as permissões entrem em vigor. Se você optar por usar o acesso ao aplicativo, que está atualmente em visualização pública, os administradores poderão acessar o portal do AD do Azure assim que a solicitação de ativação for concluída.

Atualmente, o acesso ao aplicativo dá suporte apenas à experiência do portal do Azure AD e aos recursos do Azure. Para obter mais informações sobre o PIM e o acesso a aplicativos, consulte [o que é Azure ad Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – agosto de 2018

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em agosto de 2018, adicionamos esses 16 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [ponte desassociado](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [ingrediente Labs – teste de dispositivos móveis e da Web](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [conector de metaredes](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [como fazemos](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [promestre (por Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dossiê](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F-relatórios de despesas](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>O suporte nativo do tableau agora está disponível no Azure Proxy de Aplicativo do AD

**Tipo:** Recurso alterado  
**Categoria de serviço:** Proxy de aplicativo  
**Funcionalidade do produto:** Controle de acesso

Com nossa atualização do OpenID Connect para o protocolo de concessão de código OAuth 2,0 para nosso protocolo de pré-autenticação, você não precisa mais fazer nenhuma configuração adicional para usar o tableau com o proxy de aplicativo. Essa alteração de protocolo também ajuda o proxy de aplicativo a oferecer melhor suporte a aplicativos modernos usando apenas redirecionamentos HTTP, que geralmente são compatíveis com marcas HTML e JavaScript.

Para obter mais informações sobre nosso suporte nativo para tableau, consulte [Azure proxy de aplicativo do AD agora com suporte nativo a tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Novo suporte para adicionar o Google como um provedor de identidade para usuários convidados B2B no Azure Active Directory (versão prévia)

**Tipo:** Novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C

Ao configurar a Federação com o Google em sua organização, você pode permitir que os usuários do Gmail convidados entrem em seus aplicativos e recursos compartilhados usando sua conta existente do Google, sem precisar criar uma conta pessoal da Microsoft (MSAs) ou uma conta do Azure AD.

Esta é uma visualização pública opcional. Para obter mais informações sobre o Google Federation, consulte [Adicionar o Google como um provedor de identidade para usuários de convidado B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Julho de 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Melhorias para Azure Active Directory notificações por email

**Tipo:** Recurso alterado  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
Os emails do Azure Active Directory (Azure AD) agora apresentam um design atualizado, bem como as alterações no endereço de email do remetente e no nome de exibição do remetente, quando enviados dos seguintes serviços:
 
- Revisões de acesso do Azure AD
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Notificações de certificado de expiração do aplicativo empresarial
- Notificações do serviço de provisionamento de aplicativo empresarial
 
As notificações de email serão enviadas do endereço de email e do nome de exibição a seguir:

- Endereço de email: azure-noreply@microsoft.com
- Nome de exibição: Microsoft Azure
 
Para obter um exemplo de alguns dos novos designs de email e mais informações, consulte [notificações por email no Azure ad PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Os logs de atividade do Azure AD agora estão disponíveis por meio de Azure Monitor

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios

Os logs de atividade do Azure AD agora estão disponíveis em visualização pública para o Azure Monitor (serviço de monitoramento em toda a plataforma do Azure). O Azure Monitor oferece a retenção de longo prazo e a integração direta, além desses aprimoramentos:

- Retenção de longo prazo roteando os arquivos de log para sua própria conta de armazenamento do Azure.

- Integração do SIEM diretamente, sem a necessidade de escrever ou manter scripts personalizados.

- Integração direta com suas próprias soluções personalizadas, ferramentas de análise ou soluções de gerenciamento de incidentes.

Para obter mais informações sobre esses novos recursos, consulte nosso blog [logs de atividades do Azure AD no Azure monitor o diagnóstico agora está em visualização pública e em](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) nossa documentação, [Azure Active Directory logs de atividades no Azure monitor (versão prévia)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informações de acesso condicional adicionadas ao relatório de entradas do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Proteção de & de segurança de identidade
 
Essa atualização permite que você veja quais políticas são avaliadas quando um usuário entra junto com o resultado da política. Além disso, o relatório agora inclui o tipo de aplicativo cliente usado pelo usuário, para que você possa identificar o tráfego de protocolo herdado. As entradas de relatório agora também podem ser pesquisadas para uma ID de correlação, que pode ser encontrada na mensagem de erro voltada para o usuário e pode ser usada para identificar e solucionar problemas de solicitação de entrada correspondente.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Exibir autenticações herdadas por meio de logs de atividade de entradas

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios
 
Com a introdução do campo **aplicativo cliente** nos logs de atividade de entrada, os clientes agora podem ver os usuários que estão usando autenticações herdadas. Os clientes poderão acessar essas informações usando as entradas MS API do Graph ou por meio dos logs de atividade de entrada no portal do AD do Azure, onde você pode usar o controle de **aplicativo cliente** para filtrar as autenticações herdadas. Confira a documentação para obter mais detalhes.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – julho de 2018

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em julho de 2018, adicionamos esses 16 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[Hub de inovação](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [LEAPSOME](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [determinado SSO de administrador](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC de preparo, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [screencast-O-](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial)vencer, PowerSchool sala de aula unificada, [integração de Eli](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar suporte remoto](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex ](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Webvision do Imagineer](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [conector SecureW2 JoinNow](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [base de habilidades](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Novas integrações de aplicativo SaaS de provisionamento de usuário – julho de 2018

**Tipo:** Novo recurso  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** integração de terceiros
 
O Azure AD permite automatizar a criação, a manutenção e a remoção de identidades de usuário em aplicativos SaaS, como dropbox, Salesforce, ServiceNow e muito mais. Para julho de 2018, adicionamos suporte de provisionamento de usuário para os seguintes aplicativos na Galeria de aplicativos do Azure AD:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Para obter uma lista de todos os aplicativos que dão suporte ao provisionamento de usuário na galeria do Azure AD, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Conectar a integridade para sincronização-uma maneira mais fácil de corrigir erros de sincronização de atributo órfãos e duplicados

**Tipo:** Novo recurso  
**Categoria de serviço:** Conexão do AD  
**Funcionalidade do produto:** Monitoramento & relatórios
 
Azure AD Connect Health apresenta a correção de autoatendimento para ajudá-lo a realçar e corrigir erros de sincronização. Esse recurso soluciona os erros de sincronização de atributo duplicados e corrige objetos órfãos do Azure AD. Esse diagnóstico tem os seguintes benefícios:

- Reduz os erros de sincronização de atributo duplicados, fornecendo correções específicas

- Aplica uma correção para cenários dedicados do Azure AD, resolvendo erros em uma única etapa

- Nenhuma atualização ou configuração é necessária para ativar e usar esse recurso

Para obter mais informações, consulte [diagnosticar e corrigir erros de sincronização de atributo duplicado](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Atualizações visuais para as experiências de entrada do Azure AD e do MSA

**Tipo:** Recurso alterado  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** Autenticação do usuário

Atualizamos a interface do usuário para a experiência de logon serviços online da Microsoft, como para o Office 365 e o Azure. Essa alteração torna as telas menos desorganizadas e mais simples. Para obter mais informações sobre essa alteração, consulte os [aprimoramentos futuros no blog de experiência de entrada do Azure ad](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nova versão do Azure AD Connect – julho de 2018

**Tipo:** Recurso alterado  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade

A versão mais recente do Azure AD Connect inclui: 

- Correções de bugs e atualizações de suporte 

- Disponibilidade geral da integração de Federação de ping

- Atualizações para o cliente do SQL 2012 mais recente 

Para obter mais informações sobre essa atualização, consulte [Azure ad Connect: histórico de lançamento de versão](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Atualizações dos termos de uso da interface do usuário final

**Tipo:** Recurso alterado  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Controles

Estamos atualizando a cadeia de caracteres de aceitação na interface do usuário final do TOU.

**Texto atual.** Para acessar os recursos de [tenantname], você deve aceitar os termos de uso.<br>**Novo texto.** Para acessar o recurso [tenantname], você deve ler os termos de uso.

**Texto atual:** Optar por aceitar significa que você concorda com todos os termos de uso acima.<br>**Novo texto:** Clique em aceitar para confirmar que você leu e entendeu os termos de uso.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>A autenticação de passagem dá suporte a protocolos e aplicativos herdados

**Tipo:** Recurso alterado  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
A autenticação de passagem agora dá suporte a protocolos e aplicativos herdados. Agora há suporte total para as seguintes limitações:

- Entradas do usuário para aplicativos cliente do Office herdados, Office 2010 e Office 2013, sem a necessidade de autenticação moderna.

- Acesso ao compartilhamento de calendário e informações de disponibilidade em ambientes híbridos do Exchange somente no Office 2010.

- Entradas do usuário para aplicativos cliente do Skype for Business sem a necessidade de autenticação moderna.

- Entradas do usuário para o PowerShell versão 1,0.

- O Apple Programa de registro de dispositivos (Apple DEP), usando o assistente de configuração do iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Gerenciamento de informações de segurança convergido para redefinição de senha de autoatendimento e autenticação multifator

**Tipo:** Novo recurso  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** Autenticação do usuário

Esse novo recurso permite que os usuários gerenciem suas informações de segurança (por exemplo, número de telefone, endereço de email, aplicativo móvel e assim por diante) para SSPR (autoatendimento de redefinição de senha) e MFA (autenticação multifator) em uma única experiência. Os usuários não precisarão mais registrar as mesmas informações de segurança para SSPR e MFA em duas experiências diferentes. Essa nova experiência também se aplica a usuários que tenham o SSPR ou o MFA.

Se uma organização não estiver impondo o registro MFA ou SSPR, os usuários poderão registrar suas informações de segurança por meio do portal **meus aplicativos** . A partir daí, os usuários podem registrar quaisquer métodos habilitados para MFA ou SSPR. 

Esta é uma visualização pública opcional. Os administradores podem ativar a nova experiência (se desejar) para um grupo selecionado de usuários ou todos os usuários em um locatário.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Use o aplicativo Microsoft Authenticator para verificar sua identidade ao redefinir sua senha

**Tipo:** Recurso alterado  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** Autenticação do usuário

Esse recurso permite que não administradores verifiquem sua identidade ao redefinir uma senha usando uma notificação ou código de Microsoft Authenticator (ou qualquer outro aplicativo autenticador). Depois que os administradores ativam esse método de redefinição de senha de autoatendimento, os usuários que registraram um aplicativo móvel por meio de aka.ms/mfasetup ou aka.ms/setupsecurityinfo podem usar seu aplicativo móvel como um método de verificação ao redefinir a senha.

A notificação de aplicativo móvel só pode ser ativada como parte de uma política que requer dois métodos para redefinir sua senha.

---

## <a name="june-2018"></a>junho de 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Aviso de alteração: correção de segurança para o fluxo de autorização delegado para aplicativos usando a API de logs de atividade do Azure AD

**Tipo:** Planejar alteração  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios

Devido à nossa maior imposição de segurança, tivemos que fazer uma alteração nas permissões para aplicativos que usam um fluxo de autorização delegado para acessar as [APIs de logs de atividades do Azure ad](https://aka.ms/aadreportsapi). Essa alteração ocorrerá até **26 de junho de 2018**.

Se qualquer um de seus aplicativos usarem as APIs de log de atividades do Azure AD, siga estas etapas para garantir que o aplicativo não seja interrompido após a alteração ocorrer.

**Para atualizar suas permissões de aplicativo**

1. Entre no portal do Azure, selecione **Azure Active Directory**e, em seguida, selecione **registros do aplicativo**.
2. Selecione seu aplicativo que usa a API de logs de atividades do Azure AD, selecione **configurações**, selecione **permissões necessárias**e, em seguida, selecione a API de **Azure Active Directory do Windows** .
3. Na área **permissões delegadas** da folha **habilitar acesso** , selecione a caixa ao lado de **ler dados do diretório** e, em seguida, selecione **salvar**.
4. Selecione **conceder permissões**e, em seguida, selecione **Sim**.
    
    >[!Note]
    >Você deve ser um administrador global para conceder permissões ao aplicativo.

Para obter mais informações, consulte a área [conceder permissões](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) do artigo pré-requisitos para acessar a API de relatórios do Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Definir configurações de TLS para se conectar aos serviços do Azure AD para PCI DSS conformidade

**Tipo:** Novo recurso  
**Categoria de serviço:** N/A  
**Funcionalidade do produto:** Plataforma

O TLS é um protocolo que fornece privacidade e integridade de dados entre dois aplicativos de comunicação e é o protocolo de segurança mais implantado atualmente usado hoje.

O [PCI Security Standards Council](https://www.pcisecuritystandards.org/) determinou que as versões anteriores do TLS e do protocolo SSL (SSL) devem ser desabilitadas em favor de habilitar os novos e mais seguros protocolos de aplicativo, com conformidade a partir de **30 de junho de 2018**. Essa alteração significa que, se você se conectar aos serviços do Azure AD e exigir PCI DSS-conformidade, deverá desabilitar o TLS 1,0. Várias versões do TLS estão disponíveis, mas o TLS 1,2 é a versão mais recente disponível para serviços de Azure Active Directory. É altamente recomendável mover diretamente para o TLS 1,2 para combinações de cliente/servidor e navegador/servidor.

Navegadores desatualizados podem não dar suporte a versões mais recentes do TLS, como o TLS 1,2. Para ver quais versões do TLS têm suporte no seu navegador, vá para o site do [Qualys SSL Labs](https://www.ssllabs.com/) e clique em **testar seu navegador**. Recomendamos que você atualize para a versão mais recente do navegador da Web e, preferencialmente, habilite apenas o TLS 1,2.

**Para habilitar o TLS 1,2, por navegador**

- **Microsoft Edge e Internet Explorer (ambos são definidos usando o Internet Explorer)**

    1. Abra o Internet Explorer, selecione **ferramentas** > **opções da Internet** > **avançado**.
    2. Na área **segurança** , selecione **usar TLS 1,2**e, em seguida, selecione **OK**.
    3. Feche todas as janelas do navegador e reinicie o Internet Explorer. 

- **Google Chrome**

    1. Abra o Google Chrome, digite *Chrome://Settings/* na barra de endereços e pressione **Enter**.
    2. Expanda as opções **avançadas** , vá para a área **sistema** e selecione **configurações de proxy abertas**.
    3. Na caixa **Propriedades da Internet** , selecione a guia **avançado** , vá para a área **segurança** , selecione **usar TLS 1,2**e, em seguida, selecione **OK**.
    4. Feche todas as janelas do navegador e reinicie o Google Chrome.

- **Mozilla Firefox**

    1. Abra o Firefox, digite *about: config* na barra de endereços e pressione **Enter**.
    2. Procure o termo, *TLS*e, em seguida, selecione a entrada **Security. TLS. Version. Max** .
    3. Defina o valor como **3** para forçar o navegador a usar a versão do TLS 1,2 e, em seguida, selecione **OK**.

        >[!NOTE]
        >O Firefox versão 60,0 dá suporte a TLS 1,3, de modo que você também pode definir o valor Security. TLS. Version. Max como **4**.

    4. Feche todas as janelas do navegador e reinicie o Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – junho de 2018

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em junho de 2018, adicionamos esses 15 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [liquidar música](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1,1 token habilitado aplicativo LOB](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [superhumor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [AutoTask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [backup de ponto de extremidade](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [redes Skyhigh](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [SharePoint local](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), um [pacote CX previsto](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>A proteção de senha do Azure AD está disponível em visualização pública

**Tipo:** Novo recurso  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** Autenticação do usuário

Use a proteção de senha do Azure AD para ajudar a eliminar senhas facilmente adivinhadas do seu ambiente. A eliminação dessas senhas ajuda a reduzir o risco de comprometimento de um tipo de ataque de irrigação de senha.

Especificamente, a proteção de senha do Azure AD ajuda você a:

- Proteja as contas da sua organização no Azure AD e no Windows Server Active Directory (AD). 
- Impede que os usuários usem senhas em uma lista com mais de 500 das senhas usadas com mais frequência e mais de 1 milhão variações de substituição de caracteres dessas senhas.
- Administre a proteção de senha do Azure AD de um único local no portal do AD do Azure, tanto para o AD do Azure quanto para o Windows Server local.

Para obter mais informações sobre a proteção de senha do Azure AD, consulte [eliminar senhas inadequadas em sua organização](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Novo modelo de política de acesso condicional "todos os convidados" criado durante a criação de termos de uso

**Tipo:** Novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Controles

Durante a criação dos seus termos de uso, um novo modelo de política de acesso condicional também é criado para "todos os convidados" e "todos os aplicativos". Esse novo modelo de política aplica o ToU recém-criado, simplificando o processo de criação e imposição para convidados.

Para obter mais informações, consulte [Azure Active Directory termos de uso recurso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Novo modelo de política de acesso condicional "personalizado" criado durante a criação de termos de uso

**Tipo:** Novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Controles

Durante a criação dos seus termos de uso, um novo modelo de política de acesso condicional "personalizado" também é criado. Esse novo modelo de política permite criar o ToU e, em seguida, ir imediatamente para a folha de criação de política de acesso condicional, sem a necessidade de navegar manualmente pelo portal.

Para obter mais informações, consulte [Azure Active Directory termos de uso recurso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Diretrizes novas e abrangentes sobre a implantação da autenticação multifator do Azure

**Tipo:** Novo recurso  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Proteção de & de segurança de identidade
 
Lançamos uma nova orientação passo a passo sobre como implantar a MFA (autenticação multifator) do Azure em sua organização.

Para exibir o guia de implantação do MFA, vá para o repositório de [guias de implantação de identidade](https://aka.ms/DeploymentPlans) no github. Para fornecer comentários sobre os guias de implantação, use o [formulário de comentários do plano de implantação](https://aka.ms/deploymentplanfeedback). Se você tiver alguma dúvida sobre os guias de implantação, entre em contato conosco em [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>As funções de gerenciamento de aplicativo delegado do Azure AD estão em visualização pública

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Controle de acesso

Agora, os administradores podem delegar tarefas de gerenciamento de aplicativo sem atribuir a função de administrador global. As novas funções e recursos são:

- **Novas funções de administrador padrão do Azure AD:**

    - **Administrador do aplicativo.** Concede a capacidade de gerenciar todos os aspectos de todos os aplicativos, incluindo registro, configurações de SSO, atribuições de aplicativo e licenciamento, configurações de proxy de aplicativo e consentimento (exceto para recursos do AD do Azure).

    - **Administrador de aplicativos de nuvem.** Concede todas as capacidades do administrador de aplicativos, exceto o proxy de aplicativo, porque ele não fornece acesso local.

    - **Desenvolvedor de aplicativos.** Concede a capacidade de criar registros de aplicativo, mesmo que a opção **permitir que os usuários registrem aplicativos** esteja desativada.

- **Propriedade (configure o registro por aplicativo e o aplicativo por empresa, semelhante ao processo de propriedade de Grupo:**
 
    - **Proprietário do registro do aplicativo.** Concede a capacidade de gerenciar todos os aspectos do registro de aplicativo de propriedade, incluindo o manifesto do aplicativo e adicionar proprietários adicionais.

    - **Proprietário do aplicativo empresarial.** Concede a capacidade de gerenciar muitos aspectos de aplicativos corporativos, incluindo configurações de SSO, atribuições de aplicativo e consentimento (exceto para recursos do Azure AD).

Para obter mais informações sobre a visualização pública, consulte as [funções de gerenciamento de aplicativo delegado do Azure ad estão em visualização pública!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) bloga. Para obter mais informações sobre funções e permissões, consulte [atribuindo funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Maio de 2018

### <a name="expressroute-support-changes"></a>Alterações de suporte do ExpressRoute

**Tipo:** Planejar alteração  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Plataforma  

A oferta de software como serviço, como Azure Active Directory (Azure AD), foi projetada para funcionar melhor indo diretamente pela Internet, sem a necessidade de ExpressRoute ou quaisquer outros túneis VPN privados. Por isso, em **1º de agosto de 2018**, iremos parar de oferecer suporte ao ExpressRoute para serviços do Azure ad usando o emparelhamento público do Azure e as comunidades do Azure no emparelhamento da Microsoft. Qualquer serviço afetado por essa alteração pode notar o tráfego do Azure AD mudando gradualmente do ExpressRoute para a Internet.

Enquanto estamos alterando nosso suporte, também sabemos que ainda há situações em que talvez seja necessário usar um conjunto dedicado de circuitos para o tráfego de autenticação. Por isso, o Azure AD continuará a dar suporte a restrições de intervalo de IP por locatário usando o ExpressRoute e os serviços que já estão no emparelhamento da Microsoft com a Comunidade "outros serviços online do Office 365". Se os serviços forem afetados, mas você precisar do ExpressRoute, você deverá fazer o seguinte:

- **Se você estiver no emparelhamento público do Azure.** Mude para o emparelhamento da Microsoft e inscreva-se para a **outra comunidade dos serviços online do Office 365 (12076:5100)** . Para obter mais informações sobre como migrar do emparelhamento público do Azure para o emparelhamento da Microsoft, consulte o artigo [mover um emparelhamento público para o emparelhamento da Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) .

- **Se você estiver no emparelhamento da Microsoft.** Inscreva-se para a outra comunidade do **serviço online do Office 365 (12076:5100)** . Para obter mais informações sobre os requisitos de roteamento, consulte a [seção suporte para comunidades BGP](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) do artigo requisitos de roteamento do ExpressRoute.

Se você precisar continuar a usar circuitos dedicados, você precisará conversar com sua equipe de contas da Microsoft sobre como obter autorização para usar a **outra comunidade do serviço online do Office 365 (12076:5100)** . O quadro de revisão gerenciado pelo MS Office verificará se você precisa desses circuitos e se entendeu as implicações técnicas de mantê-los. Assinaturas não autorizadas tentando criar filtros de rota para o Office 365 receberão uma mensagem de erro. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>APIs de Microsoft Graph para cenários administrativos para TOU

**Tipo:** Novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Experiência do desenvolvedor
 
Adicionamos Microsoft Graph APIs para a operação de administração de termos de uso do Azure AD. Você pode criar, atualizar, excluir o objeto de termos de uso.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Adicionar ponto de extremidade multilocatário do Azure AD como um provedor de identidade no Azure AD B2C

**Tipo:** Novo recurso  
**Categoria de serviço:** B2C – gerenciamento de identidade do consumidor  
**Funcionalidade do produto:** B2B/B2C
 
Usando políticas personalizadas, agora você pode adicionar o ponto de extremidade comum do Azure AD como um provedor de identidade no Azure AD B2C. Isso permite que você tenha um único ponto de entrada para todos os usuários do Azure AD que estão entrando em seus aplicativos. Para obter mais informações, consulte [Azure Active Directory B2C: permitir que os usuários entrem em um provedor de identidade multilocatário do Azure ad usando políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Use URLs internas para acessar aplicativos de qualquer lugar com nossa extensão de entrada meus aplicativos e o Proxy de Aplicativo do AD do Azure

**Tipo:** Novo recurso  
**Categoria de serviço:** Meus aplicativos  
**Funcionalidade do produto:** EXTERNO
 
Os usuários agora podem acessar aplicativos por meio de URLs internas mesmo quando estiverem fora da rede corporativa usando a extensão de entrada segura meus aplicativos para o Azure AD. Isso funcionará com qualquer aplicativo que você publicou usando o Azure Proxy de Aplicativo do AD, em qualquer navegador que também tenha a extensão de navegador do painel de acesso instalada. A funcionalidade de redirecionamento de URL é habilitada automaticamente quando um usuário faz logon na extensão. A extensão está disponível para download no [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)e [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory-dados na Europa para clientes da Europa

**Tipo:** Novo recurso  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** GoLocal

Os clientes da Europa exigem que seus dados permaneçam na Europa e não sejam replicados fora dos datacenters europeus para atender às leis da privacidade e da Europa. Este [artigo](https://go.microsoft.com/fwlink/?linkid=872328) fornece detalhes específicos sobre quais informações de identidade serão armazenadas na Europa e também fornece detalhes sobre as informações que serão armazenadas fora dos data centers europeus. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Novas integrações de aplicativo SaaS de provisionamento de usuário – maio de 2018

**Tipo:** Novo recurso  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** integração de terceiros
 
O Azure AD permite automatizar a criação, a manutenção e a remoção de identidades de usuário em aplicativos SaaS, como dropbox, Salesforce, ServiceNow e muito mais. Para maio de 2018, adicionamos suporte de provisionamento de usuário para os seguintes aplicativos na Galeria de aplicativos do Azure AD:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Base OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Para obter uma lista de todos os aplicativos que dão suporte ao provisionamento de usuário na galeria do Azure AD, consulte [https://aka.ms/appstutorial](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>As revisões de acesso de grupos e de aplicativos do Azure AD agora fornecem revisões recorrentes

**Tipo:** Novo recurso  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Controles
 
A revisão de acesso de grupos e aplicativos agora está disponível para o público geral como parte do Azure AD Premium P2.  Os administradores poderão configurar as revisões de acesso de associações de grupo e atribuições de aplicativo para repetir automaticamente em intervalos regulares, como mensal ou trimestral.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Os logs de atividades do Azure AD (entradas e auditoria) agora estão disponíveis por meio do MS Graph

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios
 
Os logs de atividades do Azure AD, que incluem entradas e logs de auditoria, agora estão disponíveis por meio do MS Graph. Expuseram dois pontos de extremidade por meio do MS Graph para acessar esses logs. Confira nossos [documentos](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) para obter acesso programático às APIs de relatório do Azure ad para começar. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Melhorias na experiência de resgate B2B e deixam uma organização

**Tipo:** Novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C

**Resgate just in time:** Depois de compartilhar um recurso com um usuário convidado usando a API B2B – você não precisa enviar um email de convite especial. Na maioria dos casos, o usuário convidado pode acessar o recurso e será levado por meio da experiência de resgate just-in-time. Não há mais impacto devido a emails perdidos. Não é mais perguntado aos seus usuários convidados "você clicou nesse link de resgate que o sistema enviou?". Isso significa que uma vez que o SPO usa o Gerenciador de convites – os anexos de nuvem podem ter a mesma URL canônica para todos os usuários – interno e externo – em qualquer estado de resgate.

**Experiência de resgate moderna:** Nenhuma página de aterrissagem de resgate de divisão de tela. Os usuários verão uma experiência de consentimento moderna com a política de privacidade da organização que convida, assim como fazem para aplicativos de terceiros.

**Os usuários convidados podem sair da organização:** Depois que a relação de um usuário com uma organização terminar, ela poderá se manter por conta própria, deixando a organização. Não é mais necessário chamar o administrador da organização que está convidando para "ser removido", não mais gerando tíquetes de suporte.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – maio de 2018

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em maio de 2018, adicionamos esses 18 novos aplicativos com suporte à Federação para nossa galeria de aplicativos:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty regem, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [JAMF pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [ENVI MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate primo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [montagem online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [Arc Publicação-SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [risco](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial).

Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Novos guias passo a passo de implantação para Azure Active Directory

**Tipo:** Novo recurso  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Active
 
Novas orientações passo a passo sobre como implantar o Azure Active Directory (Azure AD), incluindo SSPR (autoatendimento de redefinição de senha), logon único (SSO), acesso condicional (CA), proxy de aplicativo, provisionamento de usuário, Serviços de Federação do Active Directory (AD FS) (ADFS) para Autenticação de passagem (PTA) e ADFS para sincronização de hash de senha (PHS).

Para exibir os guias de implantação, vá para o repositório de [guias de implantação de identidade](https://aka.ms/DeploymentPlans) no github. Para fornecer comentários sobre os guias de implantação, use o [formulário de comentários do plano de implantação](https://aka.ms/deploymentplanfeedback). Se você tiver alguma dúvida sobre os guias de implantação, entre em contato conosco em [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Pesquisa de aplicativos empresariais – carregar mais aplicativos

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** EXTERNO
 
Está tendo problemas para encontrar seus aplicativos/entidades de serviço? Adicionamos a capacidade de carregar mais aplicativos na lista todos os aplicativos de aplicativos empresariais. Por padrão, mostramos 20 aplicativos. Agora você pode clicar em **carregar mais** para exibir aplicativos adicionais. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>A versão de maio do AADConnect contém uma visualização pública da integração com o PingFederate, atualizações de segurança importantes, muitas correções de bugs e novas ferramentas de solução de problemas excelentes. 

**Tipo:** Recurso alterado  
**Categoria de serviço:** Conexão do AD  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
A versão de maio do AADConnect contém uma visualização pública da integração com o PingFederate, atualizações de segurança importantes, muitas correções de bugs e novas ferramentas de solução de problemas excelentes. Você pode encontrar as notas de versão [aqui](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Revisões de acesso do Azure AD: aplicação automática

**Tipo:** Recurso alterado  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Controles

As revisões de acesso de grupos e aplicativos agora estão disponíveis para o público geral como parte do Azure AD Premium P2. Um administrador pode configurar o para aplicar automaticamente as alterações do revisor a esse grupo ou aplicativo à medida que a revisão de acesso é concluída. O administrador também pode especificar o que acontece com o acesso contínuo do usuário se os revisores não responderem, removerem o acesso, manterem acesso ou tomarem recomendações do sistema. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Tokens de ID não podem mais ser retornados usando o response_mode de consulta para novos aplicativos. 

**Tipo:** Recurso alterado  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
Os aplicativos criados em ou após 25 de abril de 2018 não poderão mais solicitar um **id_token** usando a **consulta** response_mode.  Isso traz o Azure AD embutido com as especificações de OIDC e ajuda a reduzir a superfície de ataque de seus aplicativos.  Os aplicativos criados antes de 25 de abril de 2018 não são impedidos de usar o response_mode de **consulta** com um response_type de **id_token**.  O erro retornado ao solicitar um id_token do AAD, é **AADSTS70007: ' Query ' não é um valor com suporte de ' response_mode ' ao solicitar um token**.

O **fragmento** e o **form_post** response_modes continuam funcionando – ao criar novos objetos de aplicativo (por exemplo, para uso de proxy de aplicativo), verifique se o uso de um desses response_modes antes de criar um novo aplicativo.  

---
 
## <a name="april-2018"></a>Abril de 2018 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C token de acesso são GA

**Tipo:** Novo recurso  
**Categoria de serviço:** B2C – gerenciamento de identidade do consumidor  
**Funcionalidade do produto:** B2B/B2C 

Agora você pode acessar APIs da Web protegidas por Azure AD B2C usando tokens de acesso. O recurso está mudando da visualização pública para GA. A experiência de interface do usuário para configurar aplicativos Azure AD B2C e APIs Web foi aprimorada, e outras melhorias secundárias foram feitas.
 
Para obter mais informações, consulte [Azure ad B2C: solicitando tokens de acesso](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testar a configuração de logon único para aplicativos baseados em SAML

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** EXTERNO

Ao configurar aplicativos SSO baseados em SAML, você pode testar a integração na página de configuração. Se você encontrar um erro durante a entrada, poderá fornecer o erro na experiência de teste e o Azure AD fornecerá as etapas de resolução para resolver o problema específico.

Para obter mais informações, veja:

- [Configurar o início de sessão único em aplicações que não fazem parte da galeria de aplicações do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Como depurar o logon único baseado em SAML para aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Os termos de uso do Azure AD agora têm relatórios por usuário

**Tipo:** Novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Regulamenta
 
Os administradores agora podem selecionar um determinado ToU e ver todos os usuários que consentiam a esse ToU e qual data/hora aconteceu.

Para obter mais informações, consulte o [recurso termos de uso do Azure ad](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: IP arriscado para proteção de bloqueio de extranet AD FS 

**Tipo:** Novo recurso  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Monitoramento & relatórios

O Connect Health agora dá suporte à capacidade de detectar endereços IP que excedem um limite de logons U/P com falha em uma base por hora ou diariamente. Os recursos fornecidos por esse recurso são:

- Relatório abrangente mostrando o endereço IP e o número de logons com falha gerados por hora/dia com o limite personalizável.
- Alertas baseados em email que mostram quando um endereço IP específico excedeu o limite de logons U/P com falha por hora/dia.
- Uma opção de download para fazer uma análise detalhada dos dados

Para obter mais informações, consulte [relatório de IP arriscado](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Configuração fácil de aplicativo com arquivo de metadados ou URL

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** EXTERNO

Na página aplicativos empresariais, os administradores podem carregar um arquivo de metadados SAML para configurar o logon baseado em SAML para a galeria do AAD e o aplicativo inexistente na galeria.

Além disso, você pode usar a URL de metadados de Federação do aplicativo do Azure AD para configurar o SSO com o aplicativo de destino.

Para obter mais informações, consulte [Configurando o logon único para aplicativos que não estão na Galeria de aplicativos Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>O Azure AD Termos de uso agora disponível para o público geral

**Tipo:** Novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Regulamenta
 

Os termos de uso do Azure AD foram movidos da visualização pública para disponibilidade geral.

Para obter mais informações, consulte o [recurso termos de uso do Azure ad](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Permitir ou bloquear convites para usuários B2B de organizações específicas

**Tipo:** Novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 

Agora você pode especificar as organizações parceiras que deseja compartilhar e colaborar com a colaboração B2B do Azure AD. Para fazer isso, você pode optar por criar uma lista de domínios específicos de permitir ou negar. Quando um domínio é bloqueado usando esses recursos, os funcionários não podem mais enviar convites para pessoas nesse domínio.

Isso ajuda você a controlar o acesso aos seus recursos, permitindo, ao mesmo tempo, uma experiência tranqüila para os usuários aprovados.

Esse recurso de colaboração B2B está disponível para todos os clientes Azure Active Directory e pode ser usado em conjunto com Azure AD Premium recursos como o acesso condicional e a proteção de identidade para um controle mais granular de quando e como os usuários corporativos externos assinam em e obter acesso.

Para obter mais informações, consulte [permitir ou bloquear convites para usuários B2B de organizações específicas](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em abril de 2018, adicionamos esses 13 novos aplicativos com suporte de Federação à nossa galeria de aplicativos:

Critério HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (on-premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [Dynamic Signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), o [Organograma agora](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [appneta performance Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [ Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), prateleira, [SafetyNET](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial).

Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Conceder aos usuários B2B no Azure AD acesso aos seus aplicativos locais (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C

Como uma organização que usa recursos de colaboração B2B do Azure Active Directory (Azure AD) para convidar usuários convidados de organizações parceiras para o Azure AD, agora você pode fornecer a esses usuários B2B acesso a aplicativos locais. Esses aplicativos locais podem usar a autenticação baseada em SAML ou a autenticação integrada do Windows (IWA) com a KCD (delegação restrita de Kerberos).

Para obter mais informações, consulte [Grant Users B2B in Azure ad Access to your local Applications](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Obter tutoriais de integração de SSO do Azure Marketplace

**Tipo:** Recurso alterado  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** integração de terceiros

Se um aplicativo listado no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) der suporte ao logon único baseado em SAML, clicar em **obter agora** fornecerá o tutorial de integração associado a esse aplicativo. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Desempenho mais rápido do provisionamento automático de usuário do Azure AD para aplicativos SaaS

**Tipo:** Recurso alterado  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** integração de terceiros
 
Anteriormente, os clientes que usam os conectores de provisionamento de usuário Azure Active Directory para aplicativos SaaS (por exemplo, Salesforce, ServiceNow e box) poderiam apresentar desempenho lento se seus locatários do Azure AD contivessem mais de 100.000 usuários combinados e os grupos e eles estavam usando atribuições de usuário e grupo para determinar quais usuários devem ser provisionados.

Em 2 de abril de 2018, aprimoramentos de desempenho significativos foram implantados no serviço de provisionamento do Azure AD, o que reduz muito o tempo necessário para executar sincronizações iniciais entre Azure Active Directory e aplicativos SaaS de destino.

Como resultado, muitos clientes que tiveram sincronizações iniciais com aplicativos que levaram muitos dias ou nunca concluíram, agora estão sendo concluídos em questão de minutos ou horas.

Para obter mais informações, consulte [o que acontece durante o provisionamento?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Redefinição de senha de autoatendimento da tela de bloqueio do Windows 10 para computadores ingressados no Azure AD híbrido

**Tipo:** Recurso alterado  
**Categoria de serviço:** Redefinição de senha de autoatendimento  
**Funcionalidade do produto:** Autenticação do usuário
 
Atualizamos o recurso SSPR do Windows 10 para incluir suporte para computadores que são ingressados no Azure AD híbrido. Esse recurso está disponível no Windows 10 RS4 permite que os usuários redefinam sua senha na tela de bloqueio de um computador com Windows 10. Os usuários que estão habilitados e registrados para redefinição de senha de autoatendimento podem utilizar esse recurso.

Para obter mais informações, consulte [redefinição de senha do Azure ad na tela de logon](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Março de 2018
 
### <a name="certificate-expire-notification"></a>Notificação de expiração do certificado

**Tipo:** Fixado  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** EXTERNO
 
O Azure AD envia uma notificação quando um certificado para um aplicativo de galeria ou não galeria está prestes a expirar. 

Alguns usuários não receberam notificações para aplicativos empresariais configurados para logon único baseado em SAML. Esse problema foi resolvido. O Azure AD envia notificação para certificados expirando em 7, 30 e 60 dias. Você pode ver esse evento nos logs de auditoria. 

Para obter mais informações, veja:

- [Gerenciar certificados para logon único federado no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Relatórios de atividade de auditoria no portal de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Provedores de identidade do Twitter e do GitHub no Azure AD B2C

**Tipo:** Novo recurso  
**Categoria de serviço:** B2C – gerenciamento de identidade do consumidor  
**Funcionalidade do produto:** B2B/B2C
 
Agora você pode adicionar o Twitter ou o GitHub como um provedor de identidade no Azure AD B2C. O Twitter está mudando da visualização pública para a GA. O GitHub está sendo lançado na visualização pública.

Para obter mais informações, consulte [o que é a colaboração B2B do Azure ad?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Restringir o acesso ao navegador usando Intune Managed Browser com acesso condicional baseado em aplicativo do Azure AD para iOS e Android

**Tipo:** Novo recurso  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** Proteção de & de segurança de identidade
 
**Agora em visualização pública!**

**INTUNE Managed browser SSO:** Seus funcionários podem usar o logon único entre clientes nativos (como o Microsoft Outlook) e o Intune Managed Browser para todos os aplicativos conectados ao Azure AD.

**Intune Managed browser suporte a acesso condicional:** Agora você pode exigir que os funcionários usem o navegador gerenciado do Intune usando políticas de acesso condicional baseadas em aplicativo.

Leia mais sobre isso em nossa [postagem no blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Para obter mais informações, veja:

- [Configurar o acesso condicional baseado no aplicativo](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Configurar políticas de navegador gerenciado](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Cmdlets de proxy de aplicativo no módulo do PowerShell GA

**Tipo:** Novo recurso  
**Categoria de serviço:** Proxy de aplicativo  
**Funcionalidade do produto:** Controle de acesso
 
O suporte para cmdlets de proxy de aplicativo agora está no módulo de GA do PowerShell! Isso exige que você permaneça atualizado nos módulos do PowerShell – se você se tornar mais de um ano atrás, alguns cmdlets podem parar de funcionar. 

Para obter mais informações, consulte [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Os clientes nativos do Office 365 têm suporte pelo SSO contínuo usando um protocolo não interativo

**Tipo:** Novo recurso  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
O usuário que usa clientes nativos do Office 365 (versão 16.0.8730. xxxx e posterior) Obtém uma experiência de logon silenciosa usando o SSO contínuo. Esse suporte é fornecido pela adição de um protocolo não interativo (WS-Trust) ao Azure AD.

Para obter mais informações, consulte [como funciona a entrada em um cliente nativo com SSO contínuo?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Os usuários obtêm uma experiência de logon silenciosa, com o SSO contínuo, se um aplicativo enviar solicitações de entrada para os pontos de extremidade de locatário do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
Os usuários obtêm uma experiência de logon silencioso, com o SSO contínuo, se um aplicativo (por exemplo, `https://contoso.sharepoint.com`) enviar solicitações de entrada para os pontos de extremidade de locatário do Azure AD, ou seja, `https://login.microsoftonline.com/contoso.com/<..>` ou `https://login.microsoftonline.com/<tenant_ID>/<..>`, em vez do Endpoint (`https://login.microsoftonline.com/common/<...>`) do Azure AD.

Para obter mais informações, consulte [Azure Active Directory logon único contínuo](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>É necessário adicionar apenas uma URL do Azure AD, em vez de duas URLs anteriormente, às configurações de zona da intranet dos usuários para distribuir o SSO contínuo

**Tipo:** Novo recurso  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
Para distribuir o SSO contínuo para seus usuários, você precisa adicionar apenas uma URL do Azure AD às configurações de zona da intranet dos usuários usando a política de grupo no Active Directory: `https://autologon.microsoftazuread-sso.com`. Anteriormente, os clientes eram obrigados a adicionar duas URLs.

Para obter mais informações, consulte [Azure Active Directory logon único contínuo](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em março de 2018, adicionamos esses 15 novos aplicativos com suporte de Federação à nossa galeria de aplicativos:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant por FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), vtiger CRM, inwink, [amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [ Trisotech digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial).

Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>O PIM para recursos do Azure está disponível para o público geral

**Tipo:** Novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Se você estiver usando Azure AD Privileged Identity Management para funções de diretório, agora poderá usar os recursos de atribuição e acesso com limite de tempo do PIM para funções de recursos do Azure, como assinaturas, grupos de recursos, máquinas virtuais e qualquer outro recurso com suporte por Azure Resource Manager. Impor a autenticação multifator ao ativar funções just-in-time e agendar ativações em coordenação com janelas de alteração aprovadas. Além disso, esta versão adiciona aprimoramentos não disponíveis durante a visualização pública, incluindo uma interface do usuário atualizada, fluxos de trabalho de aprovação e a capacidade de estender funções expirando em breve e renovar funções expiradas.

Para obter mais informações, consulte [PIM para recursos do Azure (versão prévia)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Adicionando declarações opcionais aos tokens de aplicativos (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
Seu aplicativo do Azure AD agora pode solicitar declarações personalizadas ou opcionais em tokens SAML ou JWTs.  Essas são declarações sobre o usuário ou locatário que não estão incluídos por padrão no token devido a restrições de tamanho ou aplicabilidade.  Atualmente, isso está em visualização pública para aplicativos do Azure AD nos pontos de extremidade v 1.0 e v 2.0.  Consulte a documentação para obter informações sobre quais declarações podem ser adicionadas e como editar o manifesto do aplicativo para solicitá-las.  

Para obter mais informações, consulte [declarações opcionais no Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>O Azure AD dá suporte a PKCE para fluxos OAuth mais seguros

**Tipo:** Novo recurso  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
Os documentos do Azure AD foram atualizados para observar o suporte para PKCE, que permite uma comunicação mais segura durante o fluxo de concessão de código de autorização OAuth 2,0.  O S256 e o texto não criptografado code_challenges têm suporte nos pontos de extremidade v 1.0 e v 2.0. 

Para obter mais informações, consulte [solicitar um código de autorização](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Suporte para provisionamento de todos os valores de atributo de usuário disponíveis na API Get_Workers do workday

**Tipo:** Novo recurso  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** integração de terceiros
 
A visualização pública do provisionamento de entrada do WORKDAY para o Active Directory e o Azure AD agora dá suporte à capacidade de extrair e provisionar todos os valores de atributo disponíveis na API Get_Workers do workday. Isso adiciona suporte para centenas de atributos padrão e personalizados adicionais além daqueles fornecidos com a versão inicial do conector de provisionamento de entrada do workday.

Para obter mais informações, consulte: [Personalizando a lista de atributos de usuário do workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Alterando a associação de grupo de dinâmico para estático, e vice-versa

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerenciamento de grupo  
**Funcionalidade do produto:** Colaboração
 
É possível alterar como a associação é gerenciada em um grupo. Isso é útil quando você deseja manter o mesmo nome e ID do grupo no sistema, portanto, todas as referências existentes ao grupo ainda são válidas; a criação de um novo grupo exigiria a atualização dessas referências.
Atualizamos o centro de administração do Azure AD para dar suporte a essa funcionalidade. Agora, os clientes podem converter grupos existentes de associação dinâmica para associação atribuída e vice-versa. Os cmdlets do PowerShell existentes ainda estão disponíveis.

Para obter mais informações, consulte [regras de associação dinâmica para grupos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Comportamento de saída aprimorado com SSO contínuo

**Tipo:** Recurso alterado  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
Anteriormente, mesmo que os usuários desconectaram-se explicitamente de um aplicativo protegido pelo Azure AD, eles seriam automaticamente reconectados usando o SSO contínuo se tentassem acessar um aplicativo do Azure AD novamente dentro de seus corpnet de seus dispositivos ingressados no domínio. Com essa alteração, há suporte para sair.  Isso permite que os usuários escolham a mesma conta do Azure AD ou diferente para entrar novamente com o, em vez de serem conectados automaticamente usando o SSO contínuo.

Para obter mais informações, consulte [Azure Active Directory logon único contínuo](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Conector de proxy de aplicativo versão 1.5.402.0 lançado

**Tipo:** Recurso alterado  
**Categoria de serviço:** Proxy de aplicativo  
**Funcionalidade do produto:** Proteção de & de segurança de identidade
 
Essa versão do conector está sendo distribuída gradualmente até novembro. Essa nova versão do conector inclui as seguintes alterações:

- O conector agora define cookies de nível de domínio em vez do nível de subdomínio. Isso garante uma experiência de SSO mais suave e evita prompts de autenticação redundantes.
- Suporte para solicitações de codificação em partes
- Monitoramento de integridade do conector aprimorado 
- Várias correções de bugs e aprimoramentos de estabilidade

Para obter mais informações, consulte [entender os conectores de proxy de aplicativo do AD do Azure](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Fevereiro de 2018
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Navegação aprimorada para gerenciar usuários e grupos

**Tipo:** Planejar alteração  
**Categoria de serviço:** Gerenciamento de diretório  
**Funcionalidade do produto:** Active

A experiência de navegação para gerenciar usuários e grupos foi simplificada. Agora você pode navegar da visão geral do diretório diretamente para a lista de todos os usuários, com acesso mais fácil à lista de usuários excluídos. Você também pode navegar da visão geral do diretório diretamente para a lista de todos os grupos, com acesso mais fácil às configurações de gerenciamento de grupo. Além disso, na página Visão geral do diretório, você pode procurar um usuário, grupo, aplicativo empresarial ou registro de aplicativo. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Disponibilidade de entradas e relatórios de auditoria no Microsoft Azure operado pela 21Vianet (Azure China 21Vianet)

**Tipo:** Novo recurso  
**Categoria de serviço:** Azure Stack  
**Funcionalidade do produto:** Monitoramento & relatórios

Os relatórios do log de atividades do Azure AD agora estão disponíveis no Microsoft Azure operado por instâncias da 21Vianet (Azure China 21Vianet). Os seguintes logs estão incluídos:

- **Logs de atividade de entradas** -inclui todos os logs de entradas associados ao seu locatário.

- **Logs de auditoria de senha de autoatendimento** – inclui todos os logs de auditoria do SSPR.

- **Logs de auditoria de gerenciamento de diretório** – inclui todos os logs de auditoria relacionados ao gerenciamento de diretório, como gerenciamento de usuários, gerenciamento de aplicativos e outros.

Com esses logs, você pode obter informações sobre como o seu ambiente está fazendo. Os dados fornecidos permite-lhe:

- Determine como seus aplicativos e serviços são utilizados por seus usuários.

- Solucionar problemas que impedem seus usuários de realizar seu trabalho.

Para obter mais informações sobre como usar esses relatórios, consulte [Azure Active Directory Reporting](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Use a função "leitor de relatório" (função de não administrador) para exibir os relatórios de atividade do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Monitoramento & relatórios

Como parte dos comentários dos clientes para permitir que funções não administrativas tenham acesso aos logs de atividades do Azure AD, habilitamos a capacidade dos usuários que estão na função de "leitor de relatório" para acessar a atividade de entrada e auditoria no portal do Azure, bem como usando nossas APIs de grafo. 

Para obter mais informações, como usar esses relatórios, consulte [Azure Active Directory Reporting](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Declaração EmployeeID disponível como atributo de usuário e identificador de usuário

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** EXTERNO
 
Você pode configurar **EmployeeID** como o identificador de usuário e o atributo de usuário para usuários Membros e convidados B2B em aplicativos de logon com base em SAML da interface do usuário do aplicativo empresarial.

Para obter mais informações, consulte [Personalizando declarações emitidas no token SAML para aplicativos empresariais no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Gerenciamento simplificado de aplicativos usando curingas no Azure Proxy de Aplicativo do AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Proxy de aplicativo  
**Funcionalidade do produto:** Autenticação do usuário
 
Para facilitar a implantação de aplicativos e reduzir a sobrecarga administrativa, agora damos suporte à capacidade de publicar aplicativos usando curingas. Para publicar um aplicativo curinga, você pode seguir o fluxo de publicação do aplicativo padrão, mas usar um curinga nas URLs internas e externas.

Para obter mais informações, consulte [aplicativos curinga no proxy de aplicativo Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Novos cmdlets para dar suporte à configuração do proxy de aplicativo

**Tipo:** Novo recurso  
**Categoria de serviço:** Proxy de aplicativo  
**Funcionalidade do produto:** Plataforma

A versão mais recente do módulo de visualização do PowerShell do AzureAD contém novos cmdlets que permitem aos clientes configurar aplicativos de proxy de aplicativo usando o PowerShell.

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
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Novos cmdlets para dar suporte à configuração de grupos

**Tipo:** Novo recurso  
**Categoria de serviço:** Proxy de aplicativo  
**Funcionalidade do produto:** Plataforma

A versão mais recente do módulo do PowerShell do AzureAD contém cmdlets para gerenciar grupos no Azure AD. Esses cmdlets estavam disponíveis anteriormente no módulo AzureADPreview e agora são adicionados ao módulo AzureAD

Os cmdlets de grupo que agora são lançados para disponibilidade geral são: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Redefinir-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Uma nova versão do Azure AD Connect está disponível

**Tipo:** Novo recurso  
**Categoria de serviço:** AD Sync  
**Funcionalidade do produto:** Plataforma
 
Azure AD Connect é a ferramenta preferida para sincronizar dados entre o Azure AD e fontes de dados locais, incluindo o Windows Server Active Directory e o LDAP.

>[!Important]
>Essa compilação introduz as alterações de esquema e regra de sincronização. O serviço de sincronização de Azure AD Connect dispara uma importação completa e etapas de sincronização completa após uma atualização. Para obter informações sobre como alterar esse comportamento, consulte [como adiar a sincronização completa após a atualização](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Esta versão tem as seguintes atualizações e alterações:

**Problemas corrigidos**

- Correção da janela de tempo em tarefas em segundo plano para a página de filtragem de partição ao alternar para a próxima página.

- Corrigido um bug que causou violação de acesso durante a ação personalizada ConfigDB.

- Correção de um bug para recuperar do tempo limite da conexão SQL.

- Corrigido um bug em que os certificados com curingas de SAN falham na verificação de pré-req.

- Correção de um bug que causa uma falha de MIIServer. exe durante a exportação do conector do AAD.

- Correção de um bug em que uma tentativa de senha incorreta entrou no DC ao ser executada fez com que o assistente do AAD Connect alterasse a configuração

**Novos recursos e aprimoramentos**
 
- Telemetria de aplicativos – os administradores podem ativar/desativar essa classe de dados.

- Dados de integridade do Azure AD-os administradores devem visitar o portal de integridade para controlar suas configurações de integridade. Depois que a política de serviço for alterada, os agentes irão lê-la e imaplicá-la.

- Adicionadas ações de configuração de write-back de dispositivo e uma barra de progresso para inicialização de página.

- Diagnóstico geral aprimorado com relatório HTML e coleta de dados completa em um relatório de HTML/texto ZIP.

- Maior confiabilidade da atualização automática e adição de telemetria adicional para garantir que a integridade do servidor possa ser determinada.

- Restrinja as permissões disponíveis para contas com privilégios na conta do conector do AD. Para novas instalações, o assistente restringe as permissões que as contas privilegiadas têm na conta MSOL depois de criar a conta MSOL. As alterações afetam as instalações expressas e as instalações personalizadas com a conta de criação automática.

- O instalador foi alterado para não exigir privilégios de SA na instalação limpa do AADConnect.

- Novo utilitário para solucionar problemas de sincronização de um objeto específico. Atualmente, o utilitário verifica as seguintes coisas:

    - Incompatibilidade de UserPrincipalName entre o objeto de usuário sincronizado e a conta de usuário no locatário do Azure AD.
  
    - Se o objeto for filtrado da sincronização devido à filtragem de domínio
  
    - Se o objeto for filtrado da sincronização devido à filtragem da UO (unidade organizacional)

- Novo utilitário para sincronizar o hash de senha atual armazenado no Active Directory local para uma conta de usuário específica. O utilitário não requer uma alteração de senha. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplicativos que dão suporte a políticas de Proteção de Aplicativo do Intune adicionadas para uso com o acesso condicional baseado em aplicativo do Azure AD

**Tipo:** Recurso alterado  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** Proteção de & de segurança de identidade

Adicionamos mais aplicativos que dão suporte ao acesso condicional baseado em aplicativo. Agora, você pode obter acesso ao Office 365 e a outros aplicativos de nuvem conectados ao Azure AD usando esses aplicativos cliente aprovados.

Os seguintes aplicativos serão adicionados ao final de fevereiro:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Para obter mais informações, veja:

- [Requisito de aplicativo cliente aprovado](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Acesso condicional com base no aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Termos de uso atualizar para a experiência móvel 

**Tipo:** Recurso alterado  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Regulamenta

Quando os termos de uso são exibidos, agora você pode clicar em **tendo problemas para exibir? Clique aqui**. Clicar nesse link abre os termos de uso nativamente em seu dispositivo. Independentemente do tamanho da fonte no documento ou do tamanho da tela do dispositivo, você pode aplicar o zoom e ler o documento conforme necessário. 

---
 
## <a name="january-2018"></a>Janeiro de 2018
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD 

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em janeiro de 2018, os seguintes novos aplicativos com suporte à Federação foram adicionados na Galeria de aplicativos:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [software de gerenciamento de privacidade onetrust](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk Directory federado e [Fidelity netbenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial).

Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Entrar com risco adicional detectado

**Tipo:** Novo recurso  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** Proteção de & de segurança de identidade

O insight que você obtém para uma detecção de risco detectado está vinculado à sua assinatura do Azure AD. Com a edição do Azure AD Premium P2, você obtém as informações mais detalhadas sobre todas as detecções subjacentes.

Com a edição P1 do Azure AD Premium, as detecções que não são cobertas pela sua licença são exibidas como a entrada de detecção de risco com risco adicional detectado.

Para obter mais informações, consulte [Azure Active Directory as detecções de risco](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Ocultar aplicativos do Office 365 dos painéis de acesso do usuário final

**Tipo:** Novo recurso  
**Categoria de serviço:** Meus aplicativos  
**Funcionalidade do produto:** EXTERNO

Agora você pode gerenciar melhor como os aplicativos do Office 365 aparecem nos painéis de acesso do usuário por meio de uma nova configuração de usuário. Essa opção é útil para reduzir o número de aplicativos nos painéis de acesso de um usuário se você preferir mostrar apenas os aplicativos do Office no portal do Office. A configuração está localizada nas **configurações do usuário** e é rotulada, **os usuários só podem ver os aplicativos do Office 365 no portal do Office 365**.

Para obter mais informações, consulte [ocultar um aplicativo da experiência do usuário em Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Entrada direta em aplicativos habilitados para SSO de senha diretamente da URL do aplicativo 

**Tipo:** Novo recurso  
**Categoria de serviço:** Meus aplicativos  
**Funcionalidade do produto:** EXTERNO

A extensão do navegador meus aplicativos agora está disponível por meio de uma ferramenta conveniente que fornece a funcionalidade de logon único de meus aplicativos como um atalho em seu navegador. Após a instalação, o usuário verá um ícone de waffle no navegador que fornece acesso rápido aos aplicativos. Os usuários agora podem aproveitar:

- A capacidade de entrar diretamente em aplicativos baseados em SSO de senha na página de entrada do aplicativo
- Iniciar qualquer aplicativo usando o recurso de pesquisa rápida
- Atalhos para aplicativos usados recentemente da extensão
- A extensão está disponível para o Microsoft Edge, o Chrome e o Firefox.
 
Para obter mais informações, consulte [minha extensão de entrada segura de aplicativos](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>A experiência de administração do Azure AD no Portal Clássico do Azure foi desativada

**Tipo:** Preterido   
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** Active

A partir de 8 de janeiro de 2018, a experiência de administração do Azure AD no portal clássico do Azure foi desativada. Isso ocorreu em conjunto com a desativação do próprio portal clássico do Azure. No futuro, você deve usar o [centro de administração do Azure ad](https://aad.portal.azure.com) para toda a administração baseada em portal do Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>O portal da Web do PhoneFactor foi desativado

**Tipo:** Preterido  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** Active
 
A partir de 8 de janeiro de 2018, o portal da Web do PhoneFactor foi desativado. Esse portal foi usado para a administração do servidor MFA, mas essas funções foram movidas para o portal do Azure em portal.azure.com. 

A configuração de MFA está localizada em: **Azure Active Directory \> servidor MFA**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Preterir relatórios do Azure AD

**Tipo:** Preterido  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade  


Com a disponibilidade geral do novo console de administração do Azure Active Directory e novas APIs agora disponíveis para os relatórios de atividade e de segurança, as APIs de relatório no ponto de extremidade "/Reports" foram desativadas desde o final de 31 de dezembro de 2017.

**O que está disponível?**

Como parte da transição para o novo console de administração, disponibilizamos duas novas APIs para recuperar os logs de atividades do Azure AD. O novo conjunto de APIs fornece uma funcionalidade mais avançada de filtragem e classificação, além de fornecer atividades mais avançadas de auditoria e de entrada. Os dados anteriormente disponíveis por meio dos relatórios de segurança agora podem ser acessados por meio da API de detecções de risco da proteção de identidade no Microsoft Graph.

Para obter mais informações, veja:

- [Introdução à API de relatório do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Introdução ao Azure Active Directory Identity Protection e Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Dezembro de 2017

### <a name="terms-of-use-in-the-access-panel"></a>Termos de uso no painel de acesso

**Tipo:** Novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Regulamenta
 
Agora você pode ir para o painel de acesso e exibir os termos de uso que você aceitou anteriormente.

Siga estes passos.

1. Vá para o [portal do myapps](https://myapps.microsoft.com)e entre.

2. No canto superior direito, selecione seu nome e, em seguida, selecione **perfil** na lista. 

3. Em seu **perfil**, selecione **examinar os termos de uso**. 

4. Agora você pode revisar os termos de uso aceitos. 

Para obter mais informações, consulte o [recurso termos de uso do Azure AD (versão prévia)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nova experiência de entrada do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** Autenticação do usuário
 
As UIs do Azure AD e do sistema de identidade conta Microsoft foram reprojetadas para que tenham uma aparência consistente. Além disso, a página de entrada do Azure AD coleta o nome de usuário primeiro, seguido pela credencial em uma segunda tela.

Para obter mais informações, consulte [a nova experiência de entrada do Azure ad agora está em visualização pública](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Menos prompts de entrada: uma nova experiência "Mantenha-me conectado" para entrar no Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** Autenticação do usuário
 
A caixa de seleção **manter-me conectado** na página de entrada do Azure ad foi substituída por um novo prompt que aparece após a autenticação bem-sucedida. 

Se você responder **Sim** para esse prompt, o serviço fornecerá um token de atualização persistente. Esse comportamento é o mesmo de quando você selecionou a caixa de seleção **manter-me conectado** na experiência antiga. Para locatários federados, esse prompt é mostrado após a autenticação bem-sucedida com o serviço federado.

Para obter mais informações, consulte [menos prompts de entrada: a nova experiência "Mantenha-me conectado" para o Azure ad está em versão prévia](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Adicionar configuração para exigir que os termos de uso sejam expandidos antes da aceitação

**Tipo:** Novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Regulamenta
 
Uma opção para administradores exige que seus usuários expandam os termos de uso antes de aceitar os termos.

Selecione **ativado** ou **desativado** para exigir que os usuários expandam os termos de uso. A configuração **on** exige que os usuários exibam os termos de uso antes de aceitá-los.

Para obter mais informações, consulte o [recurso termos de uso do Azure AD (versão prévia)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Ativação com escopo para atribuições de função qualificadas

**Tipo:** Novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Você pode usar a ativação com escopo para ativar atribuições de função de recurso do Azure qualificadas com menos autonomia do que os padrões de atribuição originais. Um exemplo é se você estiver atribuído como o proprietário de uma assinatura em seu locatário. Com a ativação com escopo, você pode ativar a função de proprietário para até cinco recursos contidos na assinatura (como grupos de recursos e máquinas virtuais). O escopo da ativação pode reduzir a possibilidade de executar alterações indesejadas em recursos críticos do Azure.

Para obter mais informações, consulte [o que é Azure ad Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Novos aplicativos federados na Galeria de aplicativos do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em dezembro de 2017, adicionamos esses novos aplicativos com suporte de Federação à nossa galeria de aplicativos:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, a [vitrine digital da EFI](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CYBSAFE, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [Image Works](https://go.microsoft.com/fwlink/?linkid=863517), [mobi](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure ad Integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SSO do SAML para A bambu da Resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), o [SSO do SAML para bitbucket da Resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WEBHR, integração do Azure AD com a Zenegy.

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://aka.ms/appstutorial).

Para obter mais informações sobre como listar seu aplicativo na Galeria de aplicativos do Azure AD, consulte [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Fluxos de trabalho de aprovação para funções de diretório do Azure AD

**Tipo:** Recurso alterado  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
O fluxo de trabalho de aprovação para funções de diretório do Azure AD está geralmente disponível.

Com o fluxo de trabalho de aprovação, os administradores de função privilegiada podem exigir que os membros da função qualificada solicitem a ativação da função antes que possam usar a função privilegiada. Vários usuários e grupos podem ter responsabilidades de aprovação delegadas. Os membros de função qualificados recebem notificações quando a aprovação é concluída e sua função está ativa.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Autenticação de passagem: suporte do Skype for Business

**Tipo:** Recurso alterado  
**Categoria de serviço:** Autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário

A autenticação de passagem agora dá suporte a entradas de usuário para aplicativos cliente do Skype for Business que dão suporte à autenticação moderna, que inclui topologias online e híbrida. 

Para obter mais informações, consulte [topologias do Skype for Business com suporte na autenticação moderna](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Atualizações para Azure AD Privileged Identity Management para o RBAC do Azure (versão prévia)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Com a atualização de visualização pública do Azure AD Privileged Identity Management (PIM) para o RBAC (controle de acesso baseado em função) do Azure, agora você pode:

* Use apenas a administração suficiente.
* Exigir aprovação para ativar funções de recurso.
* Agendar uma ativação futura de uma função que exija aprovação para as funções do Azure AD e do Azure RBAC.
 
Para obter mais informações, consulte [Privileged Identity Management para recursos do Azure (versão prévia)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>Novembro de 2017
 
### <a name="access-control-service-retirement"></a>Aposentadoria do serviço de controle de acesso

**Tipo:** Planejar alteração  
**Categoria de serviço:** Serviço de controle de acesso  
**Funcionalidade do produto:** Serviço de controle de acesso 

Azure Active Directory controle de acesso (também conhecido como serviço de controle de acesso) será desativado no final de 2018. Mais informações que incluem uma programação detalhada e diretrizes de migração de alto nível serão fornecidas nas próximas semanas. Você pode deixar comentários nesta página com perguntas sobre o serviço de controle de acesso e um membro da equipe irá respondê-los.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Restringir o acesso do navegador ao Intune Managed Browser 

**Tipo:** Planejar alteração  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** Segurança e proteção de identidade

Você pode restringir o acesso do navegador ao Office 365 e a outros aplicativos de nuvem conectados ao Azure AD usando o Intune Managed Browser como um aplicativo aprovado. 

Agora você pode configurar a seguinte condição para acesso condicional baseado em aplicativo:

**Aplicativos cliente:** Navegador

**Qual é o efeito da alteração?**

Hoje, o acesso é bloqueado quando você usa essa condição. Quando a visualização estiver disponível, todo o acesso exigirá o uso do aplicativo de navegador gerenciado. 

Procure essa funcionalidade e mais informações nos próximos Blogs e notas de versão. 

Para obter mais informações, consulte [acesso condicional no Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos cliente aprovados para acesso condicional baseado em aplicativo do Azure AD

**Tipo:** Planejar alteração  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** Segurança e proteção de identidade

Os aplicativos a seguir estão na lista de [aplicativos cliente aprovados](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Kaizala Microsoft](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Para obter mais informações, veja:

- [Requisito de aplicativo cliente aprovado](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Acesso condicional com base no aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Suporte de termos de uso para vários idiomas

**Tipo:** Novo recurso    
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Regulamenta

Agora, os administradores podem criar novos termos de uso que contenham vários documentos em PDF. Você pode marcar esses documentos em PDF com um idioma correspondente. Os usuários são mostrados no PDF com a linguagem de correspondência com base em suas preferências. Se não houver correspondência, o idioma padrão será mostrado.

---
 
### <a name="real-time-password-writeback-client-status"></a>Status do cliente de write-back de senha em tempo real

**Tipo:** Novo recurso  
**Categoria de serviço:** Redefinição de senha de autoatendimento  
**Funcionalidade do produto:** Autenticação do usuário

Agora você pode examinar o status do seu cliente de write-back de senha local. Essa opção está disponível na seção **integração local** da página de [redefinição de senha](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) . 

Se houver problemas com a conexão com o cliente de write-back local, você verá uma mensagem de erro que fornece:

- Informações sobre o motivo pelo qual você não pode se conectar ao cliente de write-back local.
- Um link para a documentação que ajuda você a resolver o problema. 

Para obter mais informações, consulte [integração local](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Acesso condicional com base no aplicativo do Azure AD 
 
**Tipo:** Novo recurso  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** Segurança e proteção de identidade

Agora você pode restringir o acesso ao Office 365 e a outros aplicativos de nuvem conectados ao Azure AD a [aplicativos cliente aprovados](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) que dão suporte a políticas de proteção de aplicativo do Intune usando o [acesso condicional baseado em aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). As políticas de proteção de aplicativo do Intune são usadas para configurar e proteger os dados da empresa nesses aplicativos cliente.

Ao combinar [aplicativos baseados em aplicativo](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) com políticas de acesso condicional [com base no dispositivo](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) , você tem a flexibilidade de proteger dados para dispositivos pessoais e da empresa.

As seguintes condições e controles agora estão disponíveis para uso com acesso condicional baseado em aplicativo:

**Condição de plataforma com suporte**

- iOS
- Android

**Condição de aplicativos cliente**

- Aplicativos móveis e clientes de área de trabalho

**Controlo de acesso**

- Exigir aplicativo cliente aprovado

Para obter mais informações, consulte [acesso condicional baseado em aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Gerenciar dispositivos do Azure AD no portal do Azure

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerenciamento e registro de dispositivos  
**Funcionalidade do produto:** Segurança e proteção de identidade

Agora você pode encontrar todos os dispositivos conectados ao Azure AD e as atividades relacionadas ao dispositivo em um único lugar. Há uma nova experiência de administração para gerenciar todas as identidades e configurações do dispositivo no portal do Azure. Nesta versão, você pode:

- Exiba todos os dispositivos que estão disponíveis para acesso condicional no Azure AD.
- Exiba as propriedades, que incluem seus dispositivos ingressados no Azure AD híbrido.
- Encontre chaves do BitLocker para seus dispositivos ingressados no Azure AD, gerencie seu dispositivo com o Intune e muito mais.
- Gerenciar configurações relacionadas ao dispositivo do Azure AD.

Para obter mais informações, consulte [gerenciar dispositivos usando o portal do Azure](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Suporte para macOS como uma plataforma de dispositivo para acesso condicional do Azure AD 

**Tipo:** Novo recurso    
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** Segurança e proteção de identidade 

Agora você pode incluir (ou excluir) o macOS como uma condição de plataforma de dispositivo em sua política de acesso condicional do Azure AD. Com a adição do macOS às plataformas de dispositivo com suporte, você pode:

- **Registre e gerencie dispositivos macOS usando o Intune.** Semelhante a outras plataformas como iOS e Android, um aplicativo do portal da empresa está disponível para o macOS fazer registros unificados. Você pode usar o novo aplicativo de portal da empresa para macOS para registrar um dispositivo com o Intune e registrá-lo com o Azure AD.
- **Verifique se os dispositivos macOS aderem às políticas de conformidade de sua organização definidas no Intune.** No Intune no portal do Azure, agora você pode configurar políticas de conformidade para dispositivos macOS. 
- **Restrinja o acesso a aplicativos no Azure AD para apenas dispositivos macOS compatíveis.** A criação de política de acesso condicional tem o macOS como uma opção de plataforma de dispositivo separada. Agora você pode criar políticas de acesso condicional específicas do macOS para o conjunto de aplicativos de destino no Azure.

Para obter mais informações, veja:

- [Criar uma política de conformidade para dispositivos macOS com o Intune](https://aka.ms/macoscompliancepolicy)
- [Acesso condicional no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Extensão do servidor de políticas de rede para autenticação multifator do Azure 

**Tipo:** Novo recurso    
**Categoria de serviço:**  Autenticação multifator  
**Funcionalidade do produto:** Autenticação do usuário

A extensão do servidor de políticas de rede para a autenticação multifator do Azure adiciona recursos de autenticação multifator baseados em nuvem à sua infraestrutura de autenticação usando seus servidores existentes. Com a extensão do servidor de políticas de rede, você pode adicionar a verificação de chamada telefônica, mensagem de texto ou aplicativo de telefone ao seu fluxo de autenticação existente. Você não precisa instalar, configurar e manter novos servidores. 

Essa extensão foi criada para organizações que desejam proteger conexões de rede virtual privada sem implantar o Servidor de Autenticação Multifator do Azure. A extensão do servidor de políticas de rede atua como um adaptador entre o RADIUS e a autenticação multifator do Azure baseada em nuvem para fornecer um segundo fator de autenticação para usuários federados ou sincronizados.

Para obter mais informações, consulte [integrar sua infraestrutura de servidor de política de rede existente com a autenticação multifator do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Restaurar ou remover permanentemente os usuários excluídos

**Tipo:** Novo recurso    
**Categoria de serviço:** Gerenciamento de usuários  
**Funcionalidade do produto:** Active 

No centro de administração do Azure AD, agora você pode:

- Restaurar um usuário excluído. 
- Excluir permanentemente um usuário.

**Para experimentar:**

1. No centro de administração do Azure AD, selecione [todos os usuários](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) na seção **gerenciar** . 

2. Na lista **Mostrar** , selecione **usuários excluídos recentemente**. 

3. Selecione um ou mais usuários excluídos recentemente e, em seguida, restaure-os ou exclua-os permanentemente.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos cliente aprovados para acesso condicional baseado em aplicativo do Azure AD
 
**Tipo:** Recurso alterado  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** Segurança e proteção de identidade

Os aplicativos a seguir foram adicionados à lista de [aplicativos cliente aprovados](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 

Para obter mais informações, veja:

- [Requisito de aplicativo cliente aprovado](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Acesso condicional com base no aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Use "OR" entre os controles em uma política de acesso condicional 

**Tipo:** Recurso alterado    
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** Segurança e proteção de identidade
 
Agora você pode usar "OR" (exigir um dos controles selecionados) para controles de acesso condicional. Você pode usar esse recurso para criar políticas com "ou" entre controles de acesso. Por exemplo, você pode usar esse recurso para criar uma política que exige que um usuário entre usando a autenticação multifator "ou" para estar em um dispositivo em conformidade.

Para obter mais informações, consulte [controles no acesso condicional do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Agregação de detecções de risco em tempo real

**Tipo:** Recurso alterado    
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** Segurança e proteção de identidade

Em Azure AD Identity Protection, todas as detecções de risco em tempo real originadas do mesmo endereço IP em um determinado dia agora são agregadas para cada tipo de detecção de risco. Essa alteração limita o volume de detecções de risco mostrado sem nenhuma alteração na segurança do usuário.

A detecção em tempo real subjacente funciona cada vez que o usuário entra. Se você tiver uma política de segurança de risco de entrada configurada para a autenticação multifator ou para bloquear o acesso, ela ainda será disparada durante cada entrada arriscada.
 
---
 
## <a name="october-2017"></a>Outubro de 2017

### <a name="deprecate-azure-ad-reports"></a>Preterir relatórios do Azure AD

**Tipo:** Planejar alteração  
**Categoria de serviço:** Gerar  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade  

O portal do Azure fornece:

- Um novo console de administração do Azure AD.
- Novas APIs para relatórios de atividades e de segurança.
 
Devido a esses novos recursos, as APIs de relatório no ponto de extremidade/Reports foram desativadas em 10 de dezembro de 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Detecção automática de campo de entrada

**Tipo:** Fixado   
**Categoria de serviço:** Meus aplicativos  
**Funcionalidade do produto:** Logon único  

O Azure AD dá suporte à detecção automática de campo de entrada para aplicativos que processam um campo de nome de usuário e senha HTML. Essas etapas são documentadas em [como capturar automaticamente os campos de entrada de um aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Você pode encontrar esse recurso adicionando um aplicativo *inexistente na Galeria* na página **aplicativos empresariais** no [portal do Azure](https://aad.portal.azure.com). Além disso, você pode configurar o modo de **logon único** nesse novo aplicativo para **logon único baseado em senha**, inserir uma URL da Web e, em seguida, salvar a página.
 
Devido a um problema de serviço, essa funcionalidade foi temporariamente desabilitada. O problema foi resolvido e a detecção automática do campo de entrada está disponível novamente.

---

### <a name="new-multi-factor-authentication-features"></a>Novos recursos de autenticação multifator

**Tipo:** Novo recurso  
**Categoria de serviço:** Autenticação multifator  
**Funcionalidade do produto:** Segurança e proteção de identidade  

A autenticação multifator (MFA) é uma parte essencial da proteção da sua organização. Para tornar as credenciais mais adaptáveis e a experiência mais direta, os seguintes recursos foram adicionados: 

- Os resultados do desafio multifator são diretamente integrados ao relatório de entrada do Azure AD, que inclui acesso programático aos resultados de MFA.
- A configuração de MFA é mais profundamente integrada à experiência de configuração do Azure AD no portal do Azure.

Com essa visualização pública, o gerenciamento e os relatórios de MFA são uma parte integrada da experiência de configuração principal do Azure AD. Agora você pode gerenciar a funcionalidade do portal de gerenciamento do MFA na experiência do Azure AD.

Para obter mais informações, consulte [referência para relatórios de MFA no portal do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Termos de utilização

**Tipo:** Novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** Regulamenta  

Você pode usar os termos de uso do Azure AD para apresentar informações como avisos de isenção de responsabilidade relevantes para requisitos legais ou de conformidade para os usuários.

Você pode usar os termos de uso do Azure AD nos seguintes cenários:

- Termos de uso gerais para todos os usuários em sua organização
- Termos específicos de uso com base nos atributos de um usuário (por exemplo, médicos vs. ou funcionários domésticos e internacionais, feitos por grupos dinâmicos)
- Termos de uso específicos para acessar aplicativos de negócios de alto impacto, como o Salesforce

Para obter mais informações, consulte [termos de uso do Azure ad](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Aprimoramentos no Privileged Identity Management

**Tipo:** Novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management  

Com o Azure AD Privileged Identity Management, você pode gerenciar, controlar e monitorar o acesso aos recursos do Azure (versão prévia) em sua organização para:

- Subscrições
- Grupos de recursos
- Máquinas virtuais 

Todos os recursos dentro do portal do Azure que usam a funcionalidade do RBAC do Azure podem aproveitar todos os recursos de segurança e gerenciamento do ciclo de vida que Azure AD Privileged Identity Management tem a oferecer.

Para obter mais informações, consulte [Privileged Identity Management para recursos do Azure](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Revisões de acesso

**Tipo:** Novo recurso  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Regulamenta  

As organizações podem usar revisões de acesso (versão prévia) para gerenciar com eficiência as associações de grupo e o acesso a aplicativos corporativos: 

- Pode voltar a certificar o acesso do utilizador convidado ao utilizar as revisões do respetivo acesso às aplicações e as associações a grupos. Os revisores podem decidir com eficiência se permitirão o acesso contínuo aos convidados com base nas informações fornecidas pelas revisões de acesso.
- Pode voltar a certificar o acesso dos colaboradores às aplicações e as associações a grupos com revisões de acesso.

Pode recolher os controlos de revisão de acesso relativos a programas relevantes para a sua organização de modo a controlar as revisões de conformidade ou de aplicações sensíveis a riscos.

Para obter mais informações, consulte [revisões de acesso do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Ocultar aplicativos de terceiros dos meus aplicativos e do iniciador de aplicativos do Office 365

**Tipo:** Novo recurso  
**Categoria de serviço:** Meus aplicativos  
**Funcionalidade do produto:** Logon único  

Agora você pode gerenciar melhor os aplicativos que aparecem nos portais dos usuários por meio de uma nova propriedade **ocultar aplicativo** . Você pode ocultar os aplicativos para ajudar nos casos em que os blocos de aplicativo são exibidos para serviços de back-end ou blocos duplicados e para desorganizar os iniciadores de aplicativos de usuários. A alternância está na seção **Propriedades** do aplicativo de terceiros e é rotulada **como visível para o usuário?** Você também pode ocultar um aplicativo programaticamente por meio do PowerShell. 

Para obter mais informações, consulte [ocultar um aplicativo de terceiros da experiência de um usuário no Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**O que está disponível?**

 Como parte da transição para o novo console de administração, duas novas APIs para recuperar os logs de atividade do Azure AD estão disponíveis. O novo conjunto de APIs fornece uma funcionalidade mais avançada de filtragem e classificação, além de fornecer atividades mais avançadas de auditoria e de entrada. Os dados anteriormente disponíveis por meio dos relatórios de segurança agora podem ser acessados por meio da API de detecções de risco da proteção de identidade no Microsoft Graph.


## <a name="september-2017"></a>Setembro de 2017

### <a name="hotfix-for-identity-manager"></a>Hotfix para o Identity Manager

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerenciador de identidades  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade  

Um pacote de acumulação de hotfix (Build 4.4.1642.0) estará disponível a partir de 25 de setembro de 2017 para o Identity Manager 2016 Service Pack 1. Este pacote de acumulação:

- Resolve problemas e adiciona melhorias.
- É uma atualização cumulativa que substitui todas as atualizações do Identity Manager 2016 Service Pack 1 até o Build 4.4.1459.0 para o Identity Manager 2016. 
- Exige que você tenha 4.4.1302.0 de Build do Identity Manager 2016. 

Para obter mais informações, consulte [pacote cumulativo de atualizações do hotfix (Build 4.4.1642.0) está disponível para o Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
