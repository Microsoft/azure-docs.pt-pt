---
title: Linha de segurança Azure para Data Lake Analytics
description: A linha de base de segurança Data Lake Analytics fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 33196af2f3544733715ece501cceec0366d2a47c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716269"
---
# <a name="azure-security-baseline-for-data-lake-analytics"></a>Linha de segurança Azure para Data Lake Analytics

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../security/benchmarks/overview-v1.md) para a Data Lake Analytics. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Data Lake Analytics. Foram excluídos **os controlos** não aplicáveis ao Data Lake Analytics.

 
Para ver como o Data Lake Analytics mapeia completamente para o Azure Security Benchmark, consulte o ficheiro completo de [mapeamento de base de base de segurança data lake analytics](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Utilize definições de firewall para data lake analytics para limitar as gamas IP externas para permitir o acesso dos seus clientes no local e serviços de terceiros. A configuração das definições de firewall está disponível através do Portal, REST APIs ou PowerShell.

- [Visão geral das regras da firewall](/rest/api/datalakeanalytics/firewallrules) 

- [Gerir o Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Utilize definições de firewall para data lake analytics para limitar as gamas IP externas para permitir o acesso dos seus clientes no local e serviços de terceiros.  A configuração das definições de firewall está disponível através do Portal, REST APIs ou PowerShell.

- [Visão geral das regras da firewall](/rest/api/datalakeanalytics/firewallrules) 

- [Gerir o Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança como data lake analytics 'auditoria' e diagnósticos de 'pedidos'. Dentro do Azure Monitor, utilize um espaço de trabalho log analytics para consultar e realizar análises, e use contas de armazenamento Azure para armazenamento de longo prazo/arquivo, opcionalmente com funcionalidades de segurança, tais como armazenamento imutável e retenção forçada. 

Em alternativa, pode ativar e a bordo dados para o Azure Sentinel ou uma solução de informação e gestão de eventos de terceiros.

- [Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](data-lake-analytics-diagnostic-logs.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Como recolher registos e métricas da plataforma com o Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings) 

- [Como recolher registos internos de anfitriões da Azure Virtual Machine com o Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm) 

- [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Permitir configurações de diagnóstico para data lake analytics para aceder a registos de auditoria e solicitações. Estes incluem dados como fonte de evento, data, utilizador, timetamp e outros elementos úteis. 

- [Como recolher registos e métricas da plataforma com o Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings) 

- [Compreenda o registo e diferentes tipos de registo em Azure](/azure/azure-monitor/platform/platform-logs-overview)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.DataLakeAnalytics**:

[!INCLUDE [Resource Policy for Microsoft.DataLakeAnalytics 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.datalakeanalytics-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: No Azure Monitor, desa um ponto final no seu período de retenção do espaço de trabalho Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize as contas de armazenamento Azure para armazenamento a longo prazo e arquivo.

- [Alterar o período de retenção de dados em Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) 

- [Como configurar a política de retenção para os registos de conta de armazenamento Azure](/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Analise e monitorize registos para comportamento anómalo e reveja regularmente os resultados dos seus recursos data lake analytics. Utilize o log analytics workspace do Azure Monitor para rever registos e efetuar consultas nos dados de registo. Em alternativa, pode ativar e a bordo dados para o Azure Sentinel ou uma solução de informação de sistema de terceiros e gestão de eventos.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Para mais informações sobre o Log Analytics Workspace](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Como realizar consultas personalizadas no Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Ativar as definições de diagnóstico para data lake analytics e enviar registos para um espaço de trabalho de Log Analytics. A bordo do seu Log Analytics Workspace para Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar playbooks (soluções automatizadas) e ser usados para remediar problemas de segurança. 

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como alertar nos dados de registo de registo de registos de registos](/azure/azure-monitor/learn/tutorial-response)  

- [Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](data-lake-analytics-diagnostic-logs.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Azure Ative Directory (Azure AD) tem funções incorporadas que devem ser explicitamente atribuídas e que são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: Data Lake Analytics não tem o conceito de palavras-passe padrão, uma vez que a autenticação é fornecida com O Azure Ative Directory (Azure AD) e assegurada pelo controlo de acesso baseado em funções Azure (Azure RBAC).

- [Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas.

Também pode ativar um acesso Just-In-Time utilizando o Azure Ative Directory (Azure AD) Gestão de Identidade Privilegiada e Gestor de Recursos Azure.

- [Saiba mais sobre Gestão de Identidade Privilegiada](/azure/active-directory/privileged-identity-management/)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: Sempre que possível, utilize o Azure Ative Directory (Azure AD) SSO em vez de configurar credenciais individuais autónomas por serviço. Use recomendações de identidade e acesso do Azure Security Center.

- [Compreender SSO com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Permitir a autenticação multifactora Azure Ative (Azure AD) e seguir as recomendações de identidade do Centro de Segurança Azure e de gestão de acessos para ajudar a proteger os seus recursos de Data Lake Analytics.

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize uma estação de trabalho segura gerida pelo Azure (também conhecida como Estação de Trabalho de Acesso Privilegiado, ou PAW) para tarefas administrativas que exijam privilégios elevados.

- [Compreenda estações de trabalho seguras e geridas pelo Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como permitir a autenticação multifactorA Azure Ative (Azure AD)](/security/compass/privileged-access-devices)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança do Azure Ative Directory (Azure AD) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

- [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorizar as atividades de identidade e acesso dos utilizadores no Centro de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados para a Azure Ative (Azure AD) para permitir o acesso apenas a agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar a Azure AD nomeou localizações](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. O controlo de acesso baseado em funções (Azure RBAC) proporciona um controlo fino sobre o acesso de um cliente aos recursos de Data Lake Analytics.

 

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para ajudar a descobrir contas velhas. Além disso, utilize a identidade AZure E access reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado.

- [Compreenda a Azure AD reportando](/azure/active-directory/reports-monitoring/)

- [Como utilizar as revisões de identidades e acessos do Azure AD](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Permitir configurações de diagnóstico para Data Lake Analytics e Azure Ative Directory (Azure AD), enviando todos os registos para um espaço de trabalho Log Analytics. Configure os alertas desejados (tais como tentativas de acesso a segredos desativados) dentro do Log Analytics.

- [Integre registos AD AD com registos do Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize as funcionalidades de Proteção de Riscos e Identidade da Azure AD (Azure AD) para configurar respostas automatizadas para ações suspeitas detetadas relacionadas com os seus recursos de Data Lake Analytics. Deve permitir respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos do Data Lake Analytics que armazenam ou processam informações sensíveis. 

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar o isolamento utilizando assinaturas separadas, grupos de gestão para domínios de segurança individuais, tais como ambiente, sensibilidade aos dados. Pode restringir o seu Data Lake Analytics para controlar o nível de acesso aos recursos do Data Lake Analytics que as suas aplicações e ambientes empresariais exigem. Quando as regras de firewall são configuradas, apenas as aplicações que solicitam dados sobre o conjunto especificado de redes podem aceder aos seus recursos data lake analytics. Você pode controlar o acesso ao Azure Data Lake Analytics através do controlo de acesso baseado em funções Azure (Azure RBAC).

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

- [Gerir o controlo de acesso baseado em funções Azure](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-portal#manage-azure-role-based-access-control)

- [Regras de Firewall](/rest/api/datalakeanalytics/firewallrules)

- [Gerir o Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: As funcionalidades de prevenção da perda de dados ainda não estão disponíveis para os recursos Azure Data Lake Analytics. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos do cliente como sensíveis e protege contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

- [Como garantir o armazenamento do Azure](../storage/blobs/security-recommendations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Os recursos da Microsoft Azure negociarão TLS 1.2 por padrão. Certifique-se de que qualquer cliente que se conecte ao seu Data Lake Analytics pode negociar usando tLS 1.2 ou superior.

- [Lista de Operações de Exemplo](/rest/api/datalakeanalytics/operations/list)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As funcionalidades de identificação de dados ainda não estão disponíveis para os recursos Azure Data Lake Analytics. Implementar solução de terceiros, se necessário para efeitos de conformidade. 

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos 

**Orientação**: Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para controlar a forma como os utilizadores interagem com o serviço.

- [Gerir a Azure RBAC](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-portal#manage-azure-role-based-access-control)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Os dados são armazenados na conta padrão de Data Lake Storage Gen1.  Para os dados em repouso, a Data Lake Storage Gen1 suporta "on by default", encriptação transparente.

- [Encriptação de dados em Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de atividade azure para criar alertas para quando ocorrerem alterações nas instâncias de produção dos recursos Azure Data Lake Analytics.

- [Como criar alertas para eventos de Registo de Atividades Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte o [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center para garantir os seus recursos Azure Data Lake Analytics.

A Microsoft realiza a gestão de vulnerabilidades nos sistemas subjacentes que suportam o Azure Data Lake Analytics.

- [Compreenda as recomendações do Centro de Segurança Azure](../security-center/recommendations-reference.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Utilize um programa comum de pontuação de risco (por exemplo, Sistema Comum de Pontuação de Vulnerabilidade) ou as classificações de risco padrão fornecidas pela sua ferramenta de digitalização de terceiros.

- [NIST Publication--Common Vulnerability Scoring System](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos e assim por diante) nas suas subscrições. Certifique-se de permissões (leia) adequadas no seu inquilino e enumere todas as subscrições do Azure, bem como os recursos nas suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Azure Resource Graph Explorer, é altamente recomendado criar e utilizar os recursos do Azure Resource Manager.

- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas subscrições Azure](/powershell/module/az.accounts/get-azsubscription)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, grupos de gestão e subscrições separadas, se for caso disso, para organizar e rastrear os recursos Azure Data Lake Analytics. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Além disso, utilize a Azure Policy para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Informações adicionais estão disponíveis nos links referenciados.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, utilize o Azure Resource Graph para consultar/descobrir recursos dentro das subscrições.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Informações adicionais estão disponíveis nos links referenciados

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.DataLakeAnalytics" para criar políticas personalizadas para auditar ou impor a configuração do seu serviço Azure Data Lake Analytics. Você também pode fazer uso de definições políticas incorporadas relacionadas com o seu Azure Data Lake Analytics, tais como:

- Os registos de diagnóstico em Data Lake Analytics devem ser ativados

Informações adicionais estão disponíveis nos links referenciados

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] efeitos para impor configurações seguras em todos os seus recursos Azure.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Utilize o Azure Repos para armazenar e gerir de forma segura o seu código como políticas Azure personalizadas, modelos de Gestor de Recursos Azure, scripts de configuração do estado desejado, etc. Para aceder aos recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com Azure DevOps ou Azure AD se integrados com TFS.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.DataLakeAnalytics" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a Política Azure [auditoria], [negar], e [implementar se não existir] efeitos para impor automaticamente configurações para os seus recursos Azure Data Lake Analytics.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte a [Referência de Segurança Azure: Defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Data Lake Analytics), no entanto não funciona com conteúdo do cliente.

Pré-digitalize qualquer conteúdo que seja enviado para recursos Azure, tais como App Service, Data Lake Analytics, Blob Storage etc. A Microsoft não pode aceder aos seus dados nestes casos.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: Data Lake Analytics os registos de empregos e a produção de dados são armazenados no serviço de Armazenamento de Dados Lake Gen1 subjacente.  Pode utilizar vários métodos para copiar dados, incluindo ADLCopy, Azure PowerShell ou Azure Data Factory.  Também pode utilizar a Azure Automation para fazer o armazenamento de dados regularmente automaticamente.

- [Gerir os recursos da Azure Data Lake Storage Gen1 utilizando o Storage Explorer](../data-lake-store/data-lake-store-in-storage-explorer.md)

- [Copiar dados de Azure Storage Blobs para Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)

- [Visão geral da Azure Automation](../automation/automation-intro.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Data Lake Analytics os registos de empregos e a produção de dados são armazenados no serviço de Armazenamento de Dados Lake Gen1 subjacente.  Pode utilizar vários métodos para copiar dados, incluindo ADLCopy, Azure PowerShell ou Azure Data Factory.  

- [Gerir os recursos da Azure Data Lake Storage Gen1 utilizando o Storage Explorer](../data-lake-store/data-lake-store-in-storage-explorer.md)

- [Copiar dados de Azure Storage Blobs para Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Efetue periodicamente a restauração de dados dos seus dados de backup para testar a integridade dos dados.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Cópias de segurança do Data Lake Analytics armazenadas dentro da sua Gen1 de armazenamento de dados ou encriptação de suporte de armazenamento Azure por padrão e não podem ser desligadas. Deve tratar as suas cópias de segurança como dados sensíveis e aplicar os controlos de acesso e proteção de dados relevantes como parte desta linha de base.  

- [Proteger dados armazenados no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md)

- [Autorizar o acesso aos dados no Azure Storage](../storage/common/storage-auth.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Guia de manuseamento de incidentes de segurança informática do NIST](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. 

Além disso, marque claramente as subscrições (para ex. produção, não-prod) usando tags e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure, especialmente aqueles que processam dados sensíveis.  É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e, em seguida, reveja o seu plano de resposta conforme necessário. 

- [Publicação do NIST--Guia para Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de Exportação Contínua para ajudar a identificar riscos para os recursos da Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas de segurança e recomendações para proteger os seus recursos Azure.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios da equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as regras de teste de penetração da Microsoft cloud para garantir que os seus testes de penetração não violam as políticas da Microsoft. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft. 

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
