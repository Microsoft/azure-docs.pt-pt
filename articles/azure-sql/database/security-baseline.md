---
title: Linha de Base de Segurança Azure
description: Linha de Base de Segurança Azure para Azure SQL Database e Azure SQL Gestded Instance
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9db0562e7d1c994c93b5a2dde9a3589c3d97ee9b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792621"
---
# <a name="azure-security-baseline-for-azure-sql-database--sql-managed-instance"></a>Linha de base de segurança Azure para Azure SQL Base de dados & SQL Caso gerido
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Linha de Base de Segurança Azure para Azure SQL Database contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../../security/benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte [a visão geral da Azure Security Baselines](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, consulte [Controlo de Segurança: Segurança da Rede](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteger recursos utilizando grupos de segurança de rede ou firewall Azure na sua Rede Virtual

**Orientação** : Pode permitir que o Azure Private Link permita o acesso aos Serviços Azure PaaS (por exemplo, SQL Database) e serviços de cliente/parceiro hospedados no Azure durante um Ponto Final Privado na sua rede virtual. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública.

Para permitir que o tráfego chegue à Base de Dados Azure SQL, utilize as tags de serviço SQL para permitir o tráfego de saída através de Grupos de Segurança de Rede.

As regras de rede virtual permitem ao Azure SQL Database apenas aceitar comunicações que são enviadas a partir de sub-redes selecionadas dentro de uma rede virtual.

Como configurar o Link Privado para a Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

Como utilizar pontos finais de serviço de rede virtual e regras para servidores:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registar a configuração e o tráfego de Vnets, Subnetas e NICs

**Orientação** : Utilize o Centro de Segurança Azure e remedia as recomendações de proteção da rede para a sub-rede para a qual a base de dados Azure SQL está implantada.

Para as Máquinas Virtuais Azure (VM) que estarão a ligar-se à sua Base de Dados Azure SQL, possibilite registos de fluxo de grupos de segurança de rede (NSG) para os NSGs que protejam esses VMs e enviem registos para uma Conta de Armazenamento Azure para auditoria de tráfego.

Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Como ativar os registos de fluxo NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Compreender a Segurança da Rede fornecida pelo Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Como Ativar e utilizar a Análise de Tráfego:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Compreender a Segurança da Rede fornecida pelo Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação** : Não aplicável; esta recomendação destina-se ao Azure Apps Service ou a computar recursos que hospedam aplicações web.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação** : Ativar a Norma de Proteção DDoS nas Redes Virtuais associadas à sua Base de Dados Azure SQL para proteções contra ataques de negação de serviço distribuídos. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

Como configurar a proteção DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Compreenda a Inteligência Integrada de Ameaças do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação** : Para máquinas virtuais Azure (VMs) que estarão ligadas à sua caixa de dados Azure SQL Database, possibilite registos de fluxo de grupo de segurança de rede (NSG) para os NSGs que protegem esses VMs e enviem registos para uma Conta de Armazenamento Azure para auditoria de tráfego. Se necessário para investigar a atividade anómala, ative a captura de pacotes do Observador de Rede.

Como ativar os registos de fluxo NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar o Observador de Rede:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação** : Ativar a Proteção Avançada de Ameaças (ATP) para a Base de Dados Azure SQL.  Os utilizadores recebem um alerta sobre atividades suspeitas de base de dados, potenciais vulnerabilidades e ataques de injeção de SQL, bem como padrões anómalos de acesso a bases de dados e padrões de consultas. A Advanced Threat Protection também integra alertas com o Centro de Segurança Azure.

Compreender e utilizar a Proteção Avançada de Ameaças para a Base de Dados Azure SQL: https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação** : Não aplicável; esta recomendação destina-se ao Azure Apps Service ou a computar recursos que hospedam aplicações web.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação** : Utilize etiquetas de serviço de rede virtuais para definir controlos de acesso à rede em grupos de segurança de rede ou Azure Firewall. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Ao utilizar pontos finais de serviço para a Base de Dados Azure SQL, é necessário sair para a base de dados Azure SQL Endereços IP públicos: Os Grupos de Segurança da Rede (NSGs) devem ser abertos ao Azure SQL Database IPs para permitir a conectividade. Pode fazê-lo utilizando etiquetas de serviço NSG para Azure SQL Database.

Compreender tags de serviço com pontos finais de serviço para base de dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

Compreender e utilizar tags de serviço:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação** : Defina e implemente configurações de segurança de rede para a sua Base de Dados Azure SQL com Azure Policy. Pode utilizar o espaço de nome "Microsoft.Sql" para definir definições de política personalizadas ou utilizar qualquer uma das definições de política incorporadas concebidas para a proteção da rede de servidores. Um exemplo de uma política de segurança de rede incorporada aplicável para um servidor seria: "A Base de Dados SQL deve utilizar um ponto final de serviço de rede virtual".

Use plantas Azure para simplificar as implementações de Azure em larga escala por artefactos de ambiente chave de embalagem, tais como modelos de gestão de recursos Azure, controlo de acesso baseado em funções (RBAC), e políticas, numa única definição de planta. Aplique facilmente o projeto em novas subscrições e ambientes, e afinar o controlo e a gestão através da versão.

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar uma Planta Azure: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação** : Utilize etiquetas para grupos de segurança de rede (NSG) e outros recursos relacionados com a segurança da rede e o fluxo de tráfego. Para regras individuais de NSG, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições da Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas.

Como criar e utilizar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação** : Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com o seu servidor. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

Como visualizar e recuperar eventos de Registo de Atividades Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte [Controlo de Segurança: Registo e monitorização](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação** : A Microsoft mantém fontes de tempo para os recursos Azure. Pode atualizar a sincronização de tempo para as suas implementações de cálculo.

Como configurar a sincronização temporal para os recursos computativos Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação** : Permitir a auditoria da Base de Dados Azure SQL para rastrear eventos de base de dados e escrevê-los num registo de auditoria na sua Conta de Armazenamento Azure, Log Analytics ou Centros de Eventos.

Além disso, pode transmitir telemetria de diagnóstico Azure SQL para Azure SQL Analytics, uma solução em nuvem que monitoriza o desempenho da Base de Dados Azure SQL e da Azure SQL Managed Instance em escala e em várias subscrições. Pode ajudá-lo a recolher e visualizar as métricas de desempenho da Base de Dados Azure SQL, e tem inteligência incorporada para resolução de problemas de desempenho.

Como configurar a auditoria para a sua Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

Como recolher registos e métricas da plataforma com o Azure Monitor:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

Como transmitir diagnósticos para a Azure SQL Analytics:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação** : Ative a auditoria no seu servidor e escolha um local de armazenamento para os registos de auditoria (Azure Storage, Log Analytics ou Event Hub).

Como permitir a auditoria para a Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação** : Não aplicável; este referencial destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação** : Ao armazenar os seus registos de base de dados Azure SQL num espaço de trabalho de Log Analytics, desconfie do período de retenção de registos de acordo com os regulamentos de conformidade da sua organização.

Como definir parâmetros de retenção de registos:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação** : Analise e monitorize registos para comportamentos anómalos e reveja regularmente os resultados. Utilize a Proteção avançada de ameaças do Azure Security Center para alertar sobre atividades incomuns relacionadas com a sua página da Base de Dados Azure SQL. Em alternativa, configurar alertas com base em Valores Métricos ou Entradas de Registo de Atividade Azure relacionadas com as suas instâncias de Base de Dados Azure SQL.

Compreenda a Proteção Avançada de Ameaças e alerta para a Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Como configurar alertas personalizados para a Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Permitir alertas para atividades anómalas

**Orientação** : Utilize o Centro de Segurança Azure Advanced Threat Protection for Azure SQL Database para monitorizar e alertar sobre a atividade anómala. Ative o Azure Defender para SQL para as suas Bases de Dados SQL. O Azure Defender for SQL inclui funcionalidades para aacessibilização e mitigação de potenciais vulnerabilidades de base de dados e a deteção de atividades anómalas que possam indicar uma ameaça à sua base de dados.

Compreenda a Proteção Avançada de Ameaças e alerta para a Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Como ativar o Azure Defender para SQL para Azure SQL Database:

https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql

Como gerir alertas no Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação** : Não aplicável; para a Azure SQL Database, a solução anti-malware é gerida pela Microsoft na plataforma subjacente.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação** : Não aplicável; O registo de DNS não é aplicável à Base de Dados Azure SQL.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação** : Não aplicável; a auditoria da linha de comando não é aplicável à Base de Dados Azure SQL.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Identidade e Controlo de Acesso.](../../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação** : O Azure Ative Directory (Azure AD) tem funções incorporadas que devem ser explicitamente atribuídas e que são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad-hoc para descobrir contas que são membros de grupos administrativos.

Como obter um papel de diretório em Azure AD com PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de um papel de diretório em Azure AD com PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação** : O Azure Ative Directory não tem o conceito de palavras-passe padrão. Ao providenciar uma instância de Base de Dados Azure SQL, recomenda-se que opte por integrar a autenticação com o Azure Ative Directory.

Como configurar e gerir a autenticação do Azure Ative Directory com a Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação** : Criar políticas e procedimentos em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Compreenda a identidade e o acesso do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação** : Não aplicável; enquanto pode configurar a autenticação do Diretório Ativo Azure para integrar-se com a Base de Dados Azure SQL, não é suportada uma única sação de súprico.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação** : Ativar o Azure Ative Directory (Azure AD) Autenticação multi-factor (MFA) e seguir as recomendações do Azure Security Center Identity and Access Management.

Como permitir o MFA em Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação** : Utilize uma estação de trabalho de acesso privilegiada (PAW) com MFA de autenticação multi-factor configurado para iniciar sessão e configurar recursos Azure.

Saiba mais sobre estações de acesso privilegiadas:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como permitir o MFA em Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação** : Utilize relatórios de segurança do Azure Ative Directory para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente.

Utilize proteção de ameaças avançadas para a base de dados Azure SQL para detetar atividades anómalas que indiquem tentativas incomuns e potencialmente nocivas de aceder ou explorar bases de dados.

Como identificar utilizadores de AD Azure sinalizados para atividade de risco:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

Reveja a Proteção Avançada de Ameaças e potenciais alertas:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação** : Utilize locais nomeados de acesso condicional para permitir o acesso ao Portal e à Gestão de Recursos Azure a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

Como configurar localizações nomeadas em Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação** : Crie um administrador Azure Ative Directory (Azure AD) para o seu servidor.

Como configurar e gerir a autenticação do Azure Ative Directory com a Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Como criar e configurar um exemplo AD Azure:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação** : O Azure Ative Directory (Azure AD) fornece registos para ajudar a descobrir contas velhas. Além disso, utilize revisões de acesso à identidade Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso dos utilizadores pode ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado.

Como utilizar comentários sobre o acesso à identidade do Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização das tentativas de acesso a contas desativadas

**Orientação** : Configurar a autenticação do Azure Ative Directory (Azure AD) com o Azure SQL e criar Definições de Diagnóstico para contas de utilizadores do Azure Ative Directory, enviando os registos de auditoria e registos de login para um espaço de trabalho log analytics. Configure os alertas desejados dentro do espaço de trabalho do Log Analytics.

Como configurar e gerir a autenticação do Azure Ative Directory com a Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Como integrar os Registos de Atividade do Azure no Monitor Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação** : Utilize o Azure Ative Directory (Azure AD) Proteção de Identidade e deteções de riscos para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Além disso, você pode ingerir dados em Azure Sentinel para mais investigação.

Como visualizar os sign-ins de risco Azure AD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e permitir políticas de risco de proteção de identidade:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação** : Em cenários de suporte onde a Microsoft precisa de aceder aos dados dos clientes, o Azure Customer Lockbox fornece uma interface para os clientes reverem e aprovarem ou rejeitarem os pedidos de acesso aos dados dos clientes.

Compreenda o bloqueio do cliente:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, consulte [Controlo de Segurança: Proteção de Dados](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação** : Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

Como criar e utilizar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação** : Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Os recursos devem ser separados pela Vnet/Subnet, marcados adequadamente e protegidos dentro de uma Firewall NSG ou Azure. Os recursos que armazenam ou processam dados sensíveis devem ser isolados. Utilizar Link Privado; implementar a Base de Dados Azure SQL dentro da sua Vnet e ligar-se privadamente utilizando pontos de final privados.

Como criar subscrições adicionais do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:

https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como configurar o Link Privado para a Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação** : Para bases de dados na Base de Dados Azure SQL, armazenar ou processar informações sensíveis, marque a base de dados e os recursos conexos como sensíveis através de Tags. Configure o Link Privado em conjunto com as Tags de Serviço do Grupo de Segurança de Rede nas suas instâncias de Base de Dados Azure SQL para evitar a exfiltração de informações sensíveis.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Como configurar o Private Link e os NSGs para evitar a exfiltração de dados nas suas instâncias da Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

Compreender a proteção de dados do cliente em Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação** : A Azure SQL Database protege os seus dados encriptando dados em movimento com a Segurança da Camada de Transporte. A SQL Database aplica sempre encriptação (SSL/TLS) para todas as ligações. Isto garante que todos os dados são encriptados "em trânsito" entre o cliente e o servidor, independentemente da definição de Encrypt ou TrustServerCertificate na cadeia de ligação.

Compreenda a encriptação Azure SQL em Trânsito:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação** : Utilize a funcionalidade de descoberta e classificação de dados da Base de Dados Azure SQL. A descoberta e classificação de dados fornece capacidades avançadas incorporadas na Base de Dados Azure SQL para descobrir, classificar, rotular &amp; protegendo os dados sensíveis nas suas bases de dados.

Como utilizar a descoberta e classificação de dados para a Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação** : Utilize o Azure Ative Directory (Azure AD) para autenticar e controlar o acesso a instâncias da Base de Dados Azure SQL.

Como integrar a Base de Dados Azure SQL com o Azure Ative Directory para autenticação:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

Como controlar o acesso na Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação** : A Microsoft gere a infraestrutura subjacente à Base de Dados Azure SQL e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

Compreender a proteção de dados do cliente em Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação** : A encriptação transparente de dados (TDE) ajuda a proteger a Base de Dados Azure SQL, Azure SQL Managed Instance e Azure Data Warehouse contra a ameaça de atividade offline maliciosa, encriptando dados em repouso. Realiza a encriptação e desencriptação em tempo real da base de dados, cópias de segurança associadas e ficheiros de registo de transações inativos e não carece de alterações à aplicação. Por predefinição, o TDE está ativado para todas as bases de dados recentemente implantadas na Base de Dados SQL e na SQL Managed Instance. A chave de encriptação TDE pode ser gerida pela Microsoft ou pelo cliente.

Como gerir encriptação de dados transparentes e usar as suas próprias chaves de encriptação:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação** : Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações nas instâncias de produção da Base de Dados Azure SQL e outros recursos críticos ou relacionados.

Como criar alertas para eventos de Registo de Atividades Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para obter mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades.](../../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação** : Ative O Azure Defender for SQL for Azure SQL Database e siga as recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nos seus servidores.

Como executar avaliações de vulnerabilidade na Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

Como ativar o Azure Defender para o SQL:

https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql

Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patchs de software de terceiros

**Orientação** : Não aplicável; este referencial destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação** : Ative as análises periódicas recorrentes para as suas instâncias de Base de Dados Azure SQL; isto configurará uma avaliação de vulnerabilidade para executar automaticamente uma verificação na sua base de dados uma vez por semana. Um resumo do resultado será enviado para o endereço de e-mail(es) que fornecer. Compare os resultados para verificar se as vulnerabilidades foram remediadas.

Como exportar um relatório de avaliação de vulnerabilidades no Centro de Segurança Azure:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação** : Utilize as classificações de risco predefinidas (Pontuação Segura) fornecidas pelo Azure Security Center.

Compreenda a pontuação segura do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use a Azure Asset Discovery

**Orientação** : Utilize o Azure Resource Graph para consultar e descubra todos os recursos (incluindo a Base de Dados Azure SQL) dentro da sua subscrição( s).  Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

Como criar consultas com gráfico de recursos Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como ver as suas Subscrições Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Compreenda Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação** : Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

Como criar e utilizar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação** : Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear ativos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Como criar subscrições adicionais do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:

https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos recursos aprovados da Azure e dos títulos de software

**Orientação** : Defina a lista de recursos aprovados da Azure e software aprovado para os seus recursos de computação

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação** : Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição.s. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com O Gráfico Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação** : Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição.s. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo específico de recurso com a Política Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação** : Não aplicável; esta recomendação destina-se a aplicações em execução de recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure através de scripts

**Orientação** : Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação** : Não aplicável; esta recomendação destina-se ao Serviço de Aplicações ou a recursos computacional que hospedam aplicações de desktop ou web.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte [Controlo de Segurança: Configuração Segura](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação** : Utilize recomendações do Azure Policy ou do Azure Security Center para a Azure SQL Database para manter configurações de segurança para todos os Recursos Azure.

Como configurar e gerir a Política de Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação** : Não aplicável; esta recomendação destinada aos recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação** : Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

Como configurar e gerir a Política de Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Compreenda os efeitos da política do Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação** : Se utilizar definições personalizadas da Política Azure, utilize Azure DevOps ou Azure Repos para armazenar e gerir o seu código de forma segura.

Como armazenar código em Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação** : Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.SQL" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

Como configurar e gerir a Política de Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para serviços Azure

**Orientação** : Alavancar o Centro de Segurança Azure para realizar análises de base para a Base de Dados Azure SQL.

Como remediar recomendações no Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação** : Utilize o Cofre da Chave Azure para armazenar chaves de encriptação para encriptação de dados transparentes (TDE) da Base de Dados Azure SQL.

Como proteger os dados sensíveis que estão a ser armazenados na Base de Dados Azure SQL e armazenar as chaves de encriptação no Cofre da Chave Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação** : Utilize identidades geridas para fornecer aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo Azure Key Vault, sem quaisquer credenciais no seu código.

Tutorial: Utilize uma identidade gerida atribuída ao sistema Windows VM para aceder ao Azure SQL:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

Como configurar identidades geridas:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação** : Implementar o Scanner credencial para identificar credenciais dentro do seu código. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

Como configurar o Scanner Credencial: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Controlo de Segurança: Defesa de Malware.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional. A Microsoft lida com anti-malware para a plataforma subjacente.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação** : O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure App Service), no entanto não funciona com conteúdo do cliente.

Pré-digitalizar qualquer conteúdo que seja enviado para recursos Azure não computados, tais como App Service, Data Lake Storage, Blob Storage, Azure SQL Database, etc. A Microsoft não pode aceder aos seus dados nestes casos.

Compreenda o Antimalware da Microsoft para serviços em nuvem Azure e máquinas virtuais: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional. A Microsoft lida com anti-malware para a plataforma subjacente.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte [Controlo de Segurança: Recuperação de Dados](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação** : Para proteger o seu negócio da perda de dados, a Azure SQL Database cria automaticamente cópias de dados completas semanais, cópias de dados diferenciais a cada 12 horas e cópias de segurança de registo de transações a cada 5 - 10 minutos. As cópias de segurança são armazenadas no armazenamento RA-GRS durante pelo menos 7 dias para todos os níveis de serviço. Todos os níveis de serviço, exceto o período de retenção de backup de suporte de suporte de suporte básico configurável para a restauração pontual, até 35 dias.

Para satisfazer diferentes requisitos de conformidade, pode selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anualmente. O consumo de armazenamento depende da frequência selecionada de backups e do(s) período de retenção.

Compreenda as cópias de segurança e a continuidade do negócio com a Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Compartilhado

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação** : A Azure SQL Database cria automaticamente as cópias de dados que são mantidas entre 7 e 35 dias, e utiliza o armazenamento geo-redundante de acesso de leitura Azure (RA-GRS) para garantir que são preservados mesmo que o centro de dados não esteja disponível. Estas cópias de segurança são criadas automaticamente. Se necessário, ative cópias de segurança geo-redundantes a longo prazo para as suas bases de dados Azure SQL.

Se utilizar as teclas geridas pelo cliente para encriptação de dados transparentes, certifique-se de que as suas chaves estão a ser apoiadas.

Compreenda as cópias de segurança na Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

Como fazer backup chaves do cofre em Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

**Orientação** : Garantir a capacidade de efetuar periodicamente a restauração de dados de conteúdos dentro da Cópia de Segurança Azure. Se necessário, o teste restabelece o conteúdo a um VLAN isolado. Teste de restauração de chaves geridas pelo cliente.

Como restaurar as chaves do cofre chave em Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

Como recuperar backups da Base de Dados Azure SQL utilizando a restauração pontual:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação** : Ative a eliminação suave no Cofre da Chave Azure para proteger as chaves contra a eliminação acidental ou maliciosa.

Como permitir a eliminação suave no Cofre de Chaves:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, consulte [Controlo de Segurança: Resposta a incidentes.](../../security/benchmarks/security-control-incident-response.md)*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação** : Certifique-se de que existem planos escritos de resposta a incidentes que definem funções de pessoal, bem como fases de tratamento/gestão de incidentes.

Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação** : O Centro de Segurança atribui uma gravidade aos alertas, para o ajudar a priorizar a ordem em que atende a cada alerta, para que quando um recurso estiver comprometido, possa chegar imediatamente ao mesmo. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Alertas de segurança no Centro de Segurança Azure: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação** : Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

Pode consultar a publicação do NIST: Guide to Test, Training e Exercise Programs for IT Plans and Capabilities:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação** : As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada.

Como definir o Contacto de Segurança do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação** : Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Sentinel.

Como configurar a exportação contínua:

https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação** : Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

Como configurar a automatização do fluxo de trabalho e as aplicações lógicas:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte [Controlo de Segurança: Testes de penetração e exercícios da equipa vermelha.](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias

**Orientação** : Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Pode encontrar mais informações sobre a estratégia e execução da Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridos pela Microsoft na nuvem, aqui: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Compartilhado

## <a name="next-steps"></a>Passos seguintes

- Consulte o [Azure Security Benchmark](../../security/benchmarks/overview.md)
- Saiba mais sobre [as Linhas de Base de Segurança Azure](../../security/benchmarks/security-baselines-overview.md)