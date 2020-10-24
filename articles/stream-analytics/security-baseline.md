---
title: Linha de segurança Azure para Stream Analytics
description: Linha de segurança Azure para Stream Analytics
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b5a7f27ff20a4770caea44e73d91001d9785c015
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517230"
---
# <a name="azure-security-baseline-for-stream-analytics"></a>Linha de segurança Azure para Stream Analytics

A Linha de Base de Segurança Azure para Stream Analytics contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte a visão geral das [linhas de base de segurança Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte [o controlo de segurança: segurança da rede.](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: A Azure Stream Analytics não suporta a utilização de grupos de segurança de rede (NSG) e Azure Firewall.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Orientação**: A Azure Stream Analytics não suporta a utilização de redes virtuais e sub-redes.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Utilize a proteção contra ameaças do Azure Security Center para detetar e alertar em comunicações com endereços IP de Internet maliciosos ou não utilizados conhecidos.

* [Proteção contra ameaças para a camada de serviço Azure no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/threat-protection)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: O Azure Stream Analytics não utiliza grupos de segurança de rede e os registos de fluxo para O Azure Key Vault não são capturados.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Utilize a proteção contra ameaças do Azure Security Center para detetar operações incomuns ou potencialmente nocivas no seu ambiente de subscrição Azure.

* [Proteção contra ameaças para a camada de serviço Azure no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/threat-protection)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: O Azure Stream Analytics não suporta a utilização de redes virtuais e regras de rede.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: O Azure Stream Analytics não suporta a utilização de redes virtuais e dispositivos de rede.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: O Azure Stream Analytics não suporta a utilização de redes virtuais e regras de configuração de tráfego.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações dos recursos e detetar alterações para os seus recursos stream Analytics. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos.

* [Como visualizar e recuperar eventos de Registo de Atividades Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Como criar alertas no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte [o controlo de segurança: Registar e monitorizar](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém a fonte de tempo utilizada para os recursos Azure, como o Stream Analytics.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança, tais como eventos de auditoria e pedidos. Dentro do Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize a conta de Armazenamento Azure para armazenamento de longo prazo/arquivo, opcionalmente com funcionalidades de segurança, tais como armazenamento imutável e retenção forçada.

* [Como recolher registos e métricas da plataforma com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ativar as definições de diagnóstico no seu Azure Stream Analytics para acesso a registos administrativos, de segurança e de diagnóstico. Também pode ativar as Definições de Diagnóstico do Registo de Atividade do Azure e enviar os registos para o mesmo espaço de trabalho ou conta de armazenamento do Log Analytics.

* [Azure Stream Analytics fornece registos de diagnóstico e dados de atividade para revisão](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Ao armazenar registos de eventos de segurança na conta de armazenamento Azure ou no espaço de trabalho Log Analytics, poderá definir a política de retenção de acordo com os requisitos da sua organização.

* [Azure Stream Analytics fornece registos de diagnóstico e dados de atividade para revisão](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)

* [Como configurar a política de retenção para os registos de conta de armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

* [Alterar o período de retenção de dados em Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Analise e monitorize registos para comportamento anómalo e reveja regularmente os resultados dos seus recursos Stream Analytics. Utilize o log analytics workspace do Azure Monitor para rever registos e efetuar consultas nos dados de registo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

* [Como embarcar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Para mais informações sobre o Log Analytics Workspace](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Como realizar consultas personalizadas no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Ativar as definições de diagnóstico para stream analytics e enviar registos para um espaço de trabalho de Log Analytics. A bordo do seu Log Analytics Workspace para Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar playbooks (soluções automatizadas) e ser usados para remediar problemas de segurança.

* [Como embarcar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Como alertar nos dados de registo de registo de registos de registos](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

* [Azure Stream Analytics fornece registos de diagnóstico e dados de atividade para revisão](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Não aplicável; Stream Analytics não processa ou produz registos relacionados com anti-malware.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: A solução Azure DNS Analytics (Preview) no Azure Monitor reúne informações sobre infraestruturas de DNS sobre segurança, desempenho e operações. Atualmente isto não suporta a Azure Stream Analytics no entanto pode utilizar a solução de registo de DNS de terceiros.

* [Recolha informações sobre a sua infraestrutura DNS com a solução de pré-visualização do DNS Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para obter mais informações, consulte [controlo de segurança: Identidade e controlo de acesso.](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: A Azure AD tem funções incorporadas que devem ser explicitamente atribuídas. Os papéis podem ser questionados para descobrir a adesão. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

* [Como obter um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Como obter membros de um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: Stream Analytics não tem o conceito de palavras-passe padrão, uma vez que a autenticação é fornecida com o Azure Ative Directory e assegurada pelo Azure role-based access control (Azure RBAC) para gerir o serviço. Dependendo dos serviços de fluxo de injeção e serviços de saída, é necessário rodar credenciais configuradas nos trabalhos.

* [Rode as credenciais de login para entradas e saídas de um trabalho stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-login-credentials-inputs-outputs)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar um plano de gestão de identidade e de segurança de funções, seguindo as melhores práticas, incluindo o princípio do acesso menos privilegiado para funções de administrador. Utilize a Azure Privileged Identity Management (PIM) para fornecer acesso privilegiado just-in-time aos recursos Azure AD e Azure. Utilize alertas EM Azure e histórico de auditoria para monitorizar a atividade das contas administrativas. Utilize relatórios de segurança da AZure para ajudar a identificar contas administrativas que possam ter sido comprometidas.

* [Saiba mais](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: Sempre que possível, utilize o Azure Ative Directory SSO em vez de configurar credenciais autónomas por serviço. Implementar as recomendações de acesso à identidade do Azure Security &amp; Center.

* [Compreender SSO com Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ative Directory multi-factor de autenticação (MFA) e siga as recomendações de identidade do Centro de Segurança Azure e de gestão de acessos para ajudar a proteger os seus recursos stream Analytics.

* [Como permitir o MFA em Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize PAWs (estações de acesso privilegiada) com autenticação multi-factor (MFA) configurada para iniciar sessão e configurar recursos stream analytics.

* [Saiba mais sobre estações de trabalho de acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Como permitir o MFA em Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança do Azure Ative Directory para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

* [Como identificar utilizadores de AD Azure sinalizados para atividade de risco](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados para acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

* [Como configurar localizações nomeadas em Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. A Azure AD fornece o controlo de acesso baseado em funções Azure (Azure RBAC) para controlo de grãos finos sobre o acesso de um cliente aos recursos stream analytics.

* [Como criar e configurar uma instância AD Azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: Reveja os registos do Azure Ative Directory para ajudar a descobrir contas velhas que podem incluir as que têm funções administrativas de conta de Armazenamento. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador deve ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

* [Compreenda a Azure AD reportando](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Como utilizar comentários sobre acesso à identidade do Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Ative as definições de diagnóstico para Azure Stream Analytics e Azure Ative Directory, enviando todos os registos para um espaço de trabalho Log Analytics. Configure os alertas desejados (tais como tentativas de acesso a segredos desativados) dentro do Log Analytics.

* [Integre registos AD AD com registos do Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize as funcionalidades de Proteção de Risco e Identidade do Azure Ative Directory para configurar respostas automatizadas para detetar ações suspeitas relacionadas com os seus recursos stream Analytics. Deve permitir respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização.

* [Como ver a Azure AD a entrar em risco](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Como configurar e permitir políticas de risco de proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Como embarcar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Não aplicável; O Lockbox do cliente não suportado para a Azure Stream Analytics.

* [Serviços e cenários apoiados em disponibilidade geral](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [o controlo de segurança: proteção de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos do Stream Analytics que armazenam ou processam informações sensíveis.

* [Como criar e usar tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Isolar os trabalhos do Stream Analytics colocando entradas, saídas e contas de armazenamento na mesma subscrição. Pode restringir o stream Analytics para controlar o nível de acesso aos recursos stream Analytics que as suas aplicações e ambientes empresariais exigem. Você pode controlar o acesso ao Azure Stream Analytics via Azure AD RBAC.

* [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Como criar e usar tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Compreenda as entradas para a Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-add-inputs)

* [Compreender as saídas do Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: As funcionalidades de prevenção da perda de dados ainda não estão disponíveis para os recursos do Azure Stream Analytics. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos do cliente como sensíveis e protege contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

* [Compreender a proteção de dados do cliente em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

* [Como garantir contas de armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Azure Stream Analytics encripta todas as comunicações recebidas e saídas e suporta o TLS 1.2. Os postos de controlo incorporados também são encriptados.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As funcionalidades de identificação de dados ainda não estão disponíveis para os recursos Azure Stream Analytics. Implementar solução de terceiros, se necessário para efeitos de conformidade.

* [Compreender a proteção de dados do cliente em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para controlar a forma como os utilizadores interagem com o serviço.

* [Como configurar o Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Stream Analytics não armazena os dados recebidos uma vez que todo o processamento é feito na memória. Quaisquer dados privados, incluindo consultas e funções que sejam necessárias para serem persistidos pelo Stream Analytics, são armazenados na conta de armazenamento configurada. Utilize as teclas geridas pelo cliente (CMK) para encriptar os seus dados de saída em repouso nas suas contas de armazenamento. Mesmo sem CMK, o Stream Analytics emprega automaticamente os melhores padrões de encriptação em classe em toda a sua infraestrutura para encriptar e proteger os seus dados.

* [Proteção de dados em Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-protection)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de atividade azure para criar alertas para quando ocorrerem alterações nas instâncias de produção dos recursos Azure Stream Analytics.

* [Como criar alertas para eventos de Registo de Atividades Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte [o controlo de segurança: Gestão de vulnerabilidades.](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center para garantir os seus recursos Azure Stream Analytics.

A Microsoft realiza uma gestão de vulnerabilidades nos sistemas subjacentes que suportam o Azure Stream Analytics.

* [Compreenda as recomendações do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implementar solução automatizada de gestão de patchs para títulos de software de terceiros

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Utilize as classificações de risco predefinidas (Pontuação Segura) fornecidas pelo Azure Security Center.

* [Compreenda a pontuação segura do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para obter mais informações, consulte [controlo de segurança: Inventário e gestão de ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize a solução automatizada de Deteção de Ativos

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua(s) subscrição( s). Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como os recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

* [Como criar consultas com gráfico de recursos Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Como ver as suas Subscrições Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Compreender Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

* [Como criar e usar tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, grupos de gestão e subscrições separadas, se for caso disso, para organizar e rastrear os recursos do Azure Stream Analytics. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Além disso, utilize a Azure Policy para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e Manter um inventário dos recursos aprovados do Azure

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional e a Azure como um todo.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Além disso, utilize o Azure Resource Graph para consultar/descobrir recursos dentro da subscrição.s.

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como criar consultas com Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional e a Azure como um todo.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

* [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [o controlo de segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.StreamAnalytics" para criar políticas personalizadas para auditar ou impor a configuração do seu Azure Stream Analytics. Também pode utilizar definições de políticas incorporadas relacionadas com o seu Azure Stream Analytics, tais como: -Registos de diagnóstico em Azure Stream Analytics devem ser ativados

* [Como ver pseudónimos disponíveis da Azure Policy Aliases](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Definições políticas incorporadas da Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies)

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Compreender efeitos da política do Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Utilize o Azure Repos para armazenar e gerir de forma segura o seu código, incluindo políticas personalizadas de Azure, modelos de Gestor de Recursos Azure, scripts de configuração do estado desejado, funções definidas pelo utilizador, consultas. Para aceder aos recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com Azure DevOps, ou Ative Directory se integrados com TFS.

* [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Sobre permissões e grupos em Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.StreamAnalytics" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.StreamAnalytics" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a Política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para os seus recursos Azure Stream Analytics.

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Os detalhes de ligação dos recursos de entrada ou saída, que são utilizados pelo seu trabalho stream Analytics, são armazenados na conta de armazenamento configurada. Criptografe a sua conta de armazenamento para proteger todos os seus dados. Além disso, rode regularmente as credenciais para uma entrada ou saída de um trabalho stream Analytics.

* [Proteção de dados em Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-protection)

* [Rode as credenciais de login para entradas e saídas de um Trabalho de Análise de Fluxo](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-login-credentials-inputs-outputs)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: A autenticação de identidade gerida para a saída dá ao Stream Analytics empregos acesso direto ao serviço, incluindo Power BI, Conta de Armazenamento, em vez de utilizar uma cadeia de ligação.

* [Authenticate Stream Analytics para Azure Data Lake Storage Gen1 usando identidades geridas](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls)

* [Utilize identidade gerida para autenticar o seu trabalho Azure Stream Analytics para a saída de armazenamento Azure Blob](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity)

* [Utilize identidade gerida para autenticar o seu trabalho Azure Stream Analytics para Power BI](https://docs.microsoft.com/azure/stream-analytics/powerbi-output-managed-identity)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

* [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte [o controlo de segurança: defesa contra malware.](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Stream Analytics), no entanto não funciona com conteúdo do cliente.

Pré-digitalize qualquer conteúdo que seja enviado para recursos Azure, tais como App Service, Stream Analytics, Blob Storage etc. A Microsoft não pode aceder aos seus dados nestes casos.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [o controlo de segurança: recuperação de dados.](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Com base no tipo de serviço de saída selecionado. pode efetuar cópias de segurança automatizadas dos dados de saída de acordo com as diretrizes recomendadas para o seu serviço de saída. Os dados internos, incluindo funções definidas pelo Utilizador, consultas, instantâneos de dados são armazenados na conta de armazenamento configurada que pode fazer cópias de segurança regularmente.

Os dados na sua conta de armazenamento Microsoft Azure são sempre replicados automaticamente para garantir a durabilidade e alta disponibilidade. O Azure Storage copia os seus dados de modo a que esteja protegido contra eventos planeados e não planeados, incluindo falhas de hardware transitórios, falhas de rede ou de energia e desastres naturais maciços. Pode optar por replicar os seus dados dentro do mesmo centro de dados, através de centros de dados zonais dentro da mesma região, ou em regiões geograficamente separadas.

Também pode utilizar a função de gestão do ciclo de vida para fazer backup de dados para o nível Archive. Além disso, ative a eliminação suave para as suas cópias de segurança armazenadas na conta de Armazenamento.

* [Proteção de dados em Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

* [Compreender os acordos de redundância e Service-Level de armazenamento da Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

* [Gerir o ciclo de vida do Armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts)

Excluir suavemente para bolhas de armazenamento Azure: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Os dados internos, incluindo funções definidas pelo Utilizador, consultas, instantâneos de dados são armazenados na conta de armazenamento configurada que pode fazer cópias de segurança regularmente.

Para fazer cópias de segurança dos dados dos serviços suportados pela conta de Armazenamento, existem vários métodos disponíveis, incluindo a utilização de ferramentas azcopia ou de terceiros. O armazenamento imutável para o armazenamento Azure Blob permite que os utilizadores armazenem objetos de dados críticos do negócio num estado WORM (Write Once, Read Many). Este estado torna os dados não apagáveis e não modificáveis para um intervalo especificado pelo utilizador.

* [Proteção de dados em Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

* [Introdução ao AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

* [Definir e gerir políticas de imutabilidade para armazenamento blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage?tabs=azure-portal)

As chaves geridas pelo cliente/fornecidas podem ser apoiadas dentro do Cofre da Chave Azure utilizando o Azure CLI ou o PowerShell.

* [Como backup chaves chave cofre em Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Efetue periodicamente a restauração de dados dos seus dados de backup para testar a integridade dos dados.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: As cópias de segurança do Stream Analytics armazenadas no seu Azure Storage suportam a encriptação por predefinição e não podem ser desligadas. Deve tratar as suas cópias de segurança como dados sensíveis e aplicar os controlos de acesso e proteção de dados relevantes como parte desta linha de base.

* [Proteção de dados em Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

* [Autorizar o acesso aos dados no Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controlo de segurança: Resposta a incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Crie um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes, desde a deteção até à revisão pós-incidente.

* [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) usando tags e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure, especialmente aqueles que processam dados sensíveis. É da sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde ocorreu o incidente.

* [Alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [Utilizar etiquetas para organizar os recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

* [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

* [Como definir o Contacto de Segurança do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de Exportação Contínua para ajudar a identificar riscos para os recursos da Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

* [Como configurar a exportação contínua](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Como transmitir alertas para o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas de segurança e recomendações para proteger os seus recursos Azure.

* [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte [o controlo de segurança: Testes de penetração e exercícios de equipa vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação:** 

* [Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Você pode encontrar mais informações sobre a estratégia da Microsoft e execução de Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridas pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança da Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
