---
title: Linha de Base de Segurança Azure para Azure DevTest Labs
description: Linha de Base de Segurança Azure para Azure DevTest Labs
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 47adca5867fef1d41ccfec2455acc6932269842d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282284"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Linha de Base de Segurança Azure para Azure DevTest Labs

A Linha de Base de Segurança Azure para a Azure DevTest Labs contém recomendações que o ajudarão a melhorar a postura de segurança da sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark](../security/benchmarks/overview.md), que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure.

Para obter mais informações, consulte [a visão geral da Azure Security Baselines](../security/benchmarks/security-baselines-overview.md).

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte [Controlo de Segurança: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas
**Orientação:** A Microsoft mantém fontes de tempo para os recursos da Azure. No entanto, pode gerir as definições de sincronização de tempo para os seus recursos de computação.

Consulte o seguinte artigo para saber sobre a sincronização de tempo configurada para os recursos computativos Azure: [Sincronização de tempo para VMs do Windows em Azure](../virtual-machines/windows/time-sync.md). 

**Monitorização do Centro de Segurança Azure:** Atualmente não disponível

**Responsabilidade:** Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança
**Orientação:** Ativar as definições de diagnóstico do Registo de Atividades Azure e enviar os registos para um espaço de trabalho log Analytics, hub de eventos Azure ou conta de armazenamento Azure para o arquivo. Os registos de atividade fornecem informações sobre as operações que foram feitas nas suas instâncias Azure DevTest Labs ao nível do avião de gestão. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) feitas ao nível do plano de gestão para as suas instâncias DevTest Labs.

Para obter mais informações, consulte [Criar configurações de diagnóstico para enviar registos e métricas da plataforma para diferentes destinos.](../azure-monitor/platform/diagnostic-settings.md)

**Monitorização do Centro de Segurança Azure:** Atualmente não disponível

**Responsabilidade:** Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure
**Orientação:** Ativar as definições de diagnóstico do Registo de Atividades Azure e enviar os registos para um espaço de trabalho log Analytics, hub de eventos Azure ou conta de armazenamento Azure para o arquivo. Os registos de atividade fornecem informações sobre as operações que foram feitas nas suas instâncias Azure DevTest Labs ao nível do avião de gestão. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) feitas ao nível do plano de gestão para as suas instâncias DevTest Labs.

Para obter mais informações, consulte [Criar configurações de diagnóstico para enviar registos e métricas da plataforma para diferentes destinos.](../azure-monitor/platform/diagnostic-settings.md)

**Monitorização do Centro de Segurança Azure:** Atualmente não disponível

**Responsabilidade:** Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos
**Orientação:** As máquinas virtuais Azure DevTest Labs (VMs) são criadas e propriedade do cliente. Então, é responsabilidade da organização monitorizá-lo. Pode utilizar o Azure Security Center para monitorizar o sistema operativo computacional. Os dados recolhidos pelo Security Center do sistema operativo incluem o tipo e versão de SISTEMA, OS (Registos de Eventos do Windows), processos de execução, nome da máquina, endereços IP e registados no utilizador. O Agente Descodudo de Log também recolhe ficheiros de despejo de acidentes.

Para obter mais informações, veja os seguintes artigos: 

- [Como recolher registos internos de anfitriões da Azure Virtual Machine com o Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)
- [Compreender a recolha de dados do Centro de Segurança Azure](../security-center/security-center-enable-data-collection.md)

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança
***Orientação:** No Azure Monitor, detenva o período de retenção de registos para os espaços de trabalho do Log Analytics associados às instâncias da Azure DevTest Labs de acordo com os regulamentos de conformidade da sua organização.

Para obter mais informações, consulte o seguinte artigo: [Como definir parâmetros de retenção de registos](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos
**Orientação:** Ativar as definições de diagnóstico do Registo de Atividade do Azure e enviar os registos para um espaço de trabalho do Log Analytics. Faça consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados de registo de atividade que podem ter sido recolhidos para Azure DevTest Labs.

Para obter mais informações, veja os seguintes artigos:

- [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/platform/diagnostic-settings.md)
- [Como recolher e analisar registos de atividades do Azure no espaço de trabalho do Log Analytics no Azure Monitor](../azure-monitor/platform/activity-log.md)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Permitir alertas para atividades anómalas
**Orientação:** Utilize o espaço de trabalho Azure Log Analytics para monitorizar e alertar sobre atividades anómalas em registos de segurança e eventos relacionados com os seus Laboratórios Azure DevTest.

Para obter mais informações, consulte o seguinte artigo: [Como alertar nos dados de registo de registos de registos de registos](../azure-monitor/learn/tutorial-response.md)

**Monitorização do Centro de Segurança Azure:** Atualmente não disponível

**Responsabilidade:** Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware
**Orientação:** Não aplicável. A Azure DevTest Labs não processa ou produz registos relacionados com malware.

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS
**Orientação:** Não aplicável. A Azure DevTest Labs não processa nem produz registos relacionados com DNS.

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando
**Orientação:** A Azure DevTest Labs cria máquinas Azure Compute que são propriedade e geridas pelo cliente. Utilize o Agente de Monitorização da Microsoft em todos os VMs do Azure Windows suportados para registar o evento de criação de processos e o `CommandLine` campo. Para os VMs Azure Linux suportados, pode configurar manualmente o registo de consolas por nó e utilizar o Syslog para armazenar os dados. Além disso, utilize o espaço de trabalho do Log Analytics do Azure Monitor para rever registos e executar consultas em dados registados a partir de VMs Azure.

- [Data collection in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier) (Recolha de dados no Centro de Segurança do Azure)
- [Como executar consultas personalizadas no Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
- [Syslog data sources in Azure Monitor](../azure-monitor/platform/data-sources-syslog.md) (Origens de dados de Syslog no Azure Monitor)

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Cliente

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso
*Para mais informações, consulte [Controlo de Segurança: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas
**Orientação:** O Azure Ative Directory (Azure AD) tem funções incorporadas que devem ser explicitamente atribuídas e que são consultadas. Utilize o módulo Azure AD PowerShell para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)
- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)
- [Funções de Azure DevTest Labs](devtest-lab-add-devtest-user.md)  

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável
**Orientação:** O Azure Ative Directory (Azure AD) não tem o conceito de senhas padrão. Outros recursos Azure que requerem uma palavra-passe forçam uma senha a ser criada com requisitos de complexidade e um comprimento mínimo de senha, que diferem consoante o serviço. Você é responsável por aplicações de terceiros e serviços marketplace que podem usar senhas padrão.

A DevTest Labs não tem o conceito de senhas padrão. 

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas
**Orientação:** Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou políticas de Azure incorporadas, tais como:

- Deve haver mais de um proprietário atribuído à sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

- [Como utilizar o Centro de Segurança Azure para monitorizar a identidade e o acesso (Pré-visualização)](../security-center/security-center-identity-access.md)  
- [Como usar a Política Azure](../governance/policy/tutorials/create-and-manage.md)
- [Funções de Azure DevTest Labs](devtest-lab-add-devtest-user.md)  

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory
**Orientação:** A DevTest Labs utiliza o serviço Azure AD para gestão de identidade. Considere estes dois aspetos fundamentais quando dá aos utilizadores acesso a um ambiente baseado em DevTest Labs:

- **Gestão de recursos:** Fornece acesso ao portal Azure para gerir recursos (criar VMs, criar ambientes, iniciar, parar, reiniciar, excluir e aplicar artefactos, e assim por diante). A gestão de recursos é feita em Azure utilizando o controlo de acesso baseado em funções (RBAC). Atribui funções aos utilizadores e define permissões de recursos e de nível de acesso.
- **Máquinas virtuais (nível de rede)**: Na configuração predefinida, os VMs utilizam uma conta de administração local. Se houver um domínio disponível (Azure AD Domain Services, um domínio no local ou um domínio baseado na nuvem), as máquinas podem ser unidas ao domínio. Os utilizadores podem então utilizar as suas identidades baseadas em domínios utilizando o artefacto de união de domínios para se conectarem às máquinas. 

- [Arquitetura de referência para DevTest Labs](devtest-lab-reference-architecture.md#architecture)
- [Compreender SSO com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory
**Orientação:** Ativar o Azure Ative Directory (AD) Multi-Factor Authentication (MFA) e seguir as recomendações do Azure Security Center Identity and Access Management.

- [Como permitir o MFA em Azure](../active-directory/authentication/howto-mfa-getstarted.md)  
- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança Azure:*** Sim

**Responsabilidade:** Cliente


### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas
**Orientação:** Utilize estações de trabalho de acesso privilegiada (PAWs) com MFA configurados para iniciar sessão e configurar recursos Azure.

- [Saiba mais sobre estações de trabalho de acesso privilegiado](/windows-server/identity/securing-privileged-access/privileged-access-workstations)  
- [Como permitir o MFA em Azure](../active-directory/authentication/howto-mfa-getstarted.md)  

**Monitorização do Centro de Segurança Azure:** N/A

**Responsabilidade:** Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas
**Orientação:** Utilize relatórios de segurança do Azure Ative Directory (Azure AD) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

- [Como identificar utilizadores de AD Azure sinalizados para atividade de risco](../active-directory/identity-protection/overview-identity-protection.md)  
- [Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Azure](../security-center/security-center-identity-access.md)  

**Monitorização do Centro de Segurança Azure:** Atualmente não disponível

**Responsabilidade:** Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas
**Orientação:** Utilize locais com acesso condicional nomeados para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)  

**Monitorização do Centro de Segurança Azure:** Atualmente não disponível

**Responsabilidade:** Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure
**Orientação:** Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

- [Como criar e configurar uma instância AD Azure](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)  

**Monitorização do Centro de Segurança Azure:** Atualmente não disponível

**Responsabilidade:** Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores
**Orientação:** O Azure Ative Directory (Azure AD) fornece registos para ajudar a descobrir contas velhas. Além disso, utilize revisões de acesso à identidade Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

- [Compreenda a Azure AD reportando](../active-directory/reports-monitoring/overview-reports.md)  
- [Como utilizar comentários de acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)  

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização das tentativas de acesso a contas desativadas
**Orientação:** Tem acesso ao Azure Ative Directory (Azure AD) em fontes de registo de Atividade, Auditoria e Evento de Risco, que lhe permitem integrar-se com qualquer ferramenta de Gestão de Informação de Segurança e Eventos (SIEM) /Ferramenta de monitorização.

Pode agilizar este processo criando Definições de Diagnóstico para contas de utilizadores do Azure Ative Directory e enviando os registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar alertas dentro do Log Analytics Workspace.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)  

**Monitorização do Centro de Segurança Azure:** Atualmente não disponível

**Responsabilidade:** Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta
**Orientação:** Utilize funcionalidades de Proteção de Risco e Identidade do Azure Ative (Azure AD) para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades dos utilizadores.

- [Como ver a Azure AD a entrar em risco](../active-directory/identity-protection/overview-identity-protection.md)  
- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)  

**Monitorização do Centro de Segurança Azure:** Atualmente não disponível

**Responsabilidade:** Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte
**Orientação:** O Lockbox do cliente não é suportado atualmente para a Azure DevTest Labs.

- [Lista de serviços suportados pelo Bloqueio do Cliente](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability) 

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades
*Para obter mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades
**Orientação:** Siga as recomendações do Azure Security Center sobre a segurança dos seus casos e recursos relacionados da Azure DevTest Labs.

A Microsoft realiza a gestão de vulnerabilidades nos recursos subjacentes que suportam a Azure DevTest Labs.

- [Compreenda as recomendações do Centro de Segurança Azure](../security-center/recommendations-reference.md) 

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Compartilhado

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo
**Orientação:** Utilize o Azure Update Management para garantir que as atualizações de segurança mais recentes são instaladas nos seus VMs Windows e Linux alojados nos Laboratórios DevTest. Para os VMs do Windows, certifique-se de que o Windows Update foi ativado e definido para atualizar automaticamente. Esta definição não está atualmente disponível para configurar através da DevTest Labs, no entanto a administração de administração/subscrição de laboratório pode configurar esta definição nos VMs de computação subjacentes na sua subscrição. 

- [Como configurar a Gestão de Atualização para VMs em Azure](../automation/automation-update-management.md)
- [Compreenda as políticas de segurança do Azure monitorizadas pelo Security Center](../security-center/security-center-policy-definitions.md)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patchs de software de terceiros
***Orientação:*** Como administrador de laboratório, pode usar [artefactos da DevTest Labs](add-artifact-vm.md) para automatizar atualizações para imagens personalizadas do laboratório, incluindo patches de segurança e outras atualizações. 

Saiba mais sobre [a DevTest Labs Image Factory](image-factory-create.md), que é uma solução de configuração como código que constrói e distribui automaticamente as imagens de forma regular com todas as configurações desejadas. 

Como administrador de subscrição, também pode utilizar a solução Azure Update Management para gerir atualizações e patches para VMs da DevTest Labs. A Update Management baseia-se no repositório de atualização configurado localmente para corrigir sistemas Windows suportados. Ferramentas como o System Center Updates Publisher (Editor de Atualizações) permitem-lhe publicar atualizações personalizadas nos Serviços de Atualização do Servidor do Windows (WSUS). Este cenário permite que a Gestão de Atualização remendo máquinas que usam o Gestor de Configuração como repositório de atualização com software de terceiros.

- [Solução de Gestão de Atualização em Azure](../automation/automation-update-management.md)
- [Gerir atualizações e patches para os seus VMs Azure](../automation/automation-tutorial-update-management.md)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas
**Orientação:** A análise das exportações resulta a intervalos consistentes e compara os resultados para verificar se as vulnerabilidades foram remediadas. Ao utilizar a recomendação de gestão de vulnerabilidades sugerida pelo Azure Security Center, o cliente pode entrar no portal da solução selecionada para visualizar dados históricos de digitalização.

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas
**Orientação:** Utilize as classificações de risco predefinidas (pontuação segura) fornecidas pelo Azure Security Center.

- [Compreenda a pontuação segura do Centro de Segurança Azure](../security-center/secure-score-security-controls.md)

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário
*Para obter mais informações, consulte [controlo de segurança: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos
**Orientação:** Use o Gráfico de Recursos Azure para consultar e descobrir todos os recursos (incluindo recursos da DevTest Labs) dentro das suas subscrições. Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e pode enumerar todas as subscrições e recursos da Azure dentro das suas subscrições.

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)
- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)
- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos
**Orientação:** Aplique etiquetas nos recursos Azure, dando metadados para organizá-los logicamente de acordo com uma taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)
- [Etiquetas de configuração para DevTest Labs](devtest-lab-add-tag.md)

**Monitorização do Centro de Segurança Azure:** Não Disponível

**Responsabilidade:** Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados
**Orientação:** Use marcação, grupos de gestão e assinaturas separadas, e laboratórios separados, se for caso disso, para organizar e rastrear laboratórios e recursos relacionados com laboratórios. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados rapidamente da subscrição.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)
- [Como criar Grupos de Gestão](../governance/management-groups/create.md)
- [Como criar um laboratório usando devTest Labs](devtest-lab-create-lab.md)
- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)
- [Como configurar tags para um laboratório](devtest-lab-add-tag.md)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure
**Orientação:** Crie um inventário de recursos Azure aprovados e software aprovado para recursos de computação de acordo com as necessidades organizacionais. Como administrador de subscrição, também pode utilizar controlos de aplicações adaptativos, uma funcionalidade do Azure Security Center para o ajudar a definir um conjunto de aplicações que podem ser executadas em grupos configurados de máquinas de laboratório. Esta funcionalidade encontra-se disponível tanto para as máquinas Azure como para o Windows não-Azure (todas as versões, classic ou Azure Resource Manager) e linux.

- [Como permitir o Controlo adaptativo da aplicação](../security-center/security-center-adaptive-application.md)
 
**Monitorização do Centro de Segurança Azure:** **Não Responsabilidade Aplicável:** Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados
**Orientação:** Utilize a política da Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Além disso, utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da subscrição(s). Pode ajudar em ambientes de alta segurança, como os que têm contas de Armazenamento.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)
- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional
**Orientação:** A Azure Automation fornece controlo total durante a implantação, operações e desmantelamento de cargas de trabalho e recursos. Como administrador de subscrição, pode aproveitar o inventário da Azure Virtual Machine para automatizar a recolha de informações sobre todo o software em VMs da DevTest Labs na sua subscrição. O nome do software, versão, editor e propriedades de tempo de atualização estão disponíveis a partir do portal Azure. Para ter acesso à data de instalação e outras informações, o cliente necessário para ativar o diagnóstico ao nível do hóspede e trazer os registos do Evento do Windows para um espaço de trabalho do Log Analytics.

Além de utilizar o Change Tracking para monitorização de aplicações de software, os controlos de aplicações adaptativos no Azure Security Center utilizam machine learning para analisar as aplicações em execução nas suas máquinas e criar uma lista de permitis a partir desta inteligência. Esta capacidade simplifica muito o processo de configuração e manutenção da aplicação, permitindo políticas de listas, permitindo evitar que software indesejado seja utilizado no seu ambiente. Pode configurar o modo de auditoria ou impor o modo. O modo de auditoria apenas audita a atividade nos VM protegidos. O modo de aplicação aplica as regras e garante que as aplicações que não são permitidas a correr estão bloqueadas. 

- [Uma introdução à Automatização do Azure](../automation/automation-intro.md)
- [Como ativar o inventário Azure VM](../automation/automation-tutorial-installed-software.md)
- [Compreender controlos de aplicações adaptativos](../security-center/security-center-adaptive-application.md)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente


### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados
**Orientação:** A Azure Automation fornece controlo total durante a implantação, operações e desmantelamento de cargas de trabalho e recursos. Como administrador de subscrição, poderá utilizar o Change Tracking para identificar todos os softwares instalados em VMs hospedados em DevTest Labs. Pode implementar o seu próprio processo ou utilizar a Configuração do Estado da Automação Azure para remover software não autorizado.

- [Uma introdução à Automatização do Azure](../automation/automation-intro.md)
- [Acompanhe as mudanças no seu ambiente com a solução Change Tracking](../automation/change-tracking.md)
- [Visão geral da configuração do estado da Azure Automation](../automation/automation-dsc-overview.md)

**Monitorização do Centro de Segurança Azure:** Não Disponível

**Responsabilidade:** Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas
**Orientação:** Como administrador de subscrição, pode utilizar os Controlos de Aplicação Adaptativa do Centro de Segurança Azure para garantir que apenas o software autorizado executa, e todo o software não autorizado está bloqueado de executar em VMs Azure hospedados em DevTest Labs.

- [Como utilizar os controlos de aplicações adaptativos do Centro de Segurança Azure](../security-center/security-center-adaptive-application.md)

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados
**Orientação:** Utilize a política da Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada: 

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Consulte os seguintes artigos: 
- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)
- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/not-allowed-resource-types.md)

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Cliente


### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados
**Orientação:** O controlo de aplicações adaptativas é uma solução inteligente, automatizada e de ponta a ponta do Azure Security Center, que o ajuda a controlar quais aplicações podem ser executadas nas suas máquinas Azure e não-Azure (Windows e Linux), hospedadas na DevTest Labs. Note que precisa de ser um administrador de subscrição para poder configurar esta definição para os recursos de computação subjacentes hospedados na DevTest Labs. Implemente a solução de terceiros se esta definição não corresponder aos requisitos da sua organização.

- [Como utilizar os controlos de aplicações adaptativos do Centro de Segurança Azure](../security-center/security-center-adaptive-application.md)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure
**Orientação:** Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o acesso ao **Bloco**"** para a App de Gestão do **Azure** da Microsoft.

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional
**Orientação:** Dependendo do tipo de scripts, pode utilizar configurações específicas do sistema operativo ou recursos de terceiros para limitar a capacidade dos utilizadores de executar scripts dentro dos VMs alojados em DevTest Labs. Também pode utilizar controlos de aplicação adaptativos do Azure Security Center para garantir que apenas o software autorizado executa, e todo o software não autorizado está bloqueado de executar nos VMs Azure subjacentes.

- [Como controlar a execução do script PowerShell em Ambientes windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)
- [Como utilizar os controlos de aplicações adaptativos do Centro de Segurança Azure](../security-center/security-center-adaptive-application.md)

**Monitorização do Centro de Segurança Azure:** Não Disponível

**Responsabilidade:** Cliente


### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco
**Orientação:** As aplicações de alto risco implementadas no seu ambiente Azure podem ser isoladas usando rede virtual, sub-rede, subscrições, grupos de gestão, e assim por diante. e suficientemente seguro com um Azure Firewall, Web Application Firewall (WAF) ou um grupo de segurança de rede (NSG).

- [Configure a rede virtual para DevTest Labs](devtest-lab-configure-vnet.md)
- [Visão geral do Azure Firewall](../firewall/overview.md)
- [Visão geral do Firewall de Aplicação Web](../web-application-firewall/overview.md)
- [Descrição geral da segurança de rede](../virtual-network/security-overview.md)
- [Visão geral da Rede Virtual Azure]()
- [Organize your resources with Azure management groups](../governance/management-groups/overview.md) (Organizar os recursos com os grupos de gestão do Azure)
- [Guia de decisão de subscrição](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitorização do Centro de Segurança Azure:** Não Disponível

**Responsabilidade:** Cliente


## <a name="malware-defense"></a>Defesa contra malware
*Para mais informações, consulte o controlo de segurança: defesa de malware.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente
**Orientação:** Utilize o Microsoft Antimalware para serviços em nuvem azure e máquinas virtuais para monitorizar e defender continuamente os seus recursos. Para o Linux, utilize uma solução antimalware de terceiros. Além disso, utilize a deteção de ameaças do Azure Security Center para detetar malware enviado para contas de armazenamento.

- Como configurar o Microsoft Antimalware para serviços em nuvem e máquinas virtuais
- Proteção contra ameaças no Centro de Segurança do Azure

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados
**Orientação:** O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, o Azure App Service hospedado num laboratório), no entanto não funciona com o seu conteúdo.
Pré-digitalizar quaisquer ficheiros que sejam enviados para recursos Azure não computados, tais como App Service, Data Lake Storage, Blob Storage, etc.

Utilize a deteção de ameaças do Azure Security Center para detetar malware enviado para contas de armazenamento.

- Compreenda o Antimalware da Microsoft para serviços em nuvem Azure e máquinas virtuais
- Compreenda a deteção de ameaças do Azure Security Center para serviços de dados

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Não aplicável


### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados
**Orientação:** Quando implementado, o Microsoft Antimalware para Azure instalará automaticamente as últimas atualizações de assinatura, plataforma e motor por predefinição. Siga as recomendações no Azure Security Center: "Compute & Apps" para garantir que todos os pontos finais para os recursos de computação subjacentes da DevTest Labs estão atualizados com as assinaturas mais recentes. O Sistema operativo Windows pode ser ainda protegido com segurança adicional para limitar o risco de ataques baseados em vírus ou malware com o serviço de Proteção avançada de ameaças do Microsoft Defender que se integra com o Azure Security Center.

- Como implementar o Microsoft Antimalware para serviços em nuvem azure e máquinas virtuais
- Proteção Avançada Contra Ameaças do Microsoft Defender

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Cliente

## <a name="data-recovery"></a>Recuperação de dados
*Para obter mais informações, consulte [o controlo de segurança: recuperação de dados.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares
**Orientação:** Atualmente, a Azure DevTest Labs não suporta cópias de segurança e instantâneos VM. No entanto, pode ativar e configurar o Azure Backup nos VMs Azure subjacentes hospedados na DevTest Labs. E também pode configurar o período de frequência e retenção desejado para backups automáticos, desde que tenha acesso adequado aos recursos computatórios subjacentes. 

- [Uma visão geral do backup Azure VM](../backup/backup-azure-vms-introduction.md)
- [Apoie um VM Azure a partir das definições de VM](../backup/backup-azure-vms-first-look-arm.md)

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente
**Orientação:** Atualmente, a Azure DevTest Labs não suporta cópias de segurança e instantâneos VM. No entanto, pode criar instantâneos dos seus VMs Azure subjacentes alojados nos Laboratórios DevTest ou nos discos geridos ligados a essas instâncias usando PowerShell ou REST APIs desde que tenha acesso adequado aos recursos computacional subjacentes. Também pode fazer o back-up de quaisquer chaves geridas pelo cliente dentro do Azure Key Vault.

Ativar o Azure Backup nos VMs Azure alvo e nos períodos de frequência e retenção desejados. Inclui cópia de segurança completa do estado do sistema. Se estiver a utilizar a encriptação do disco Azure, a cópia de segurança do Azure VM lida automaticamente com a cópia de segurança das teclas geridas pelo cliente.

- [Recue em VMs Azure que usam encriptação](../backup/backup-azure-vms-encryption.md)
- [Visão geral do backup Azure VM](../backup/backup-azure-vms-introduction.md)
- [Uma visão geral do backup Azure VM](../backup/backup-azure-vms-introduction.md)
- [Como apoiar as chaves do Cofre chave em Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente
**Orientação:** Garantir a capacidade de realizar periodicamente a restauração de dados de conteúdos dentro da Cópia de Segurança Azure. Se necessário, teste a restauração do conteúdo para uma rede virtual isolada ou subscrição. Além disso, teste restauração de chaves geridas pelo cliente.

Se estiver a utilizar a encriptação do disco Azure, pode restaurar o Azure VM com as teclas de encriptação do disco. Ao utilizar a encriptação do disco, pode restaurar o Azure VM com as teclas de encriptação do disco.

- [Recue em VMs Azure que usam encriptação](../backup/backup-azure-vms-encryption.md)
- [Como recuperar ficheiros da cópia de segurança da Azure VM](../backup/backup-azure-restore-files-from-vm.md)
- [Como restaurar chaves chave do cofre em Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)
- [Como fazer o back up e restaurar um VM encriptado](../backup/backup-azure-vms-encryption.md)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente
**Orientação:** Quando faz cópia de segurança com backup Azure, os VMs são encriptados em repouso com encriptação do serviço de armazenamento (SSE). O Azure Backup também pode fazer backup de VMs Azure que são encriptados usando Azure Disk Encryption. A encriptação do disco Azure integra-se com chaves de encriptação BitLocker (BEKs), que são salvaguardadas num cofre-chave como segredos. A encriptação do disco Azure também se integra com chaves de encriptação chave Azure Key Vault (KEKs). Ativar a eliminação suave no cofre de chaves para proteger as chaves contra a eliminação acidental ou maliciosa.

- [Excluir suave para VMs](../backup/soft-delete-virtual-machines.md)
- [Azure Key Vault - visão geral de exclusão suave](../key-vault/general/overview-soft-delete.md)

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Cliente

## <a name="incident-response"></a>Resposta a incidentes
*Para obter mais informações, consulte [Controlo de Segurança: Resposta a incidentes.](../security/benchmarks/security-control-incident-response.md)*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes
**Orientação:** Crie um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal e fases de tratamento/gestão de incidentes, desde a deteção até à revisão pós-incidente.

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
- [A anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
- [Alavancar o guia de gestão de incidentes de segurança informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes
**Orientação:** O Centro de Segurança Azure atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) usando tags e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure, especialmente aqueles que processam dados sensíveis. É da sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde ocorreu o incidente.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)
- [Utilizar etiquetas para organizar os seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste
**Orientação:** Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

- [Publicação do NIST - guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança
**Orientação:** As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes
**Orientação:** Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de Exportação Contínua para ajudar a identificar riscos para os recursos da Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)
- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

#### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança
**Orientação:** Utilize a funcionalidade de Automatização de Fluxo de Trabalho no Azure Security Center para desencadear automaticamente respostas através de "Aplicações lógicas" em alertas de segurança e recomendações para proteger os seus recursos Azure.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)
 
Monitorização do Centro de Segurança Azure:**** Não aplicável

**Responsabilidade:** Cliente


## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team
*Para obter mais informações, consulte Controlo de Segurança: [Testes de penetração e exercícios de equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*


### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias
**Orientação:** Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft.

- [Regras de teste de penetração de combate](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Equipe vermelha cloud microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Compartilhado

## <a name="next-steps"></a>Passos seguintes
Consulte o seguinte artigo:

- [Alertas de segurança para ambientes em Azure DevTest Labs](environment-security-alerts.md)
