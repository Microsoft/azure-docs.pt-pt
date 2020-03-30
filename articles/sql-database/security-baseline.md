---
title: Linha de base de segurança azure para base de dados Azure SQL
description: Linha de base de segurança azure para base de dados Azure SQL
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 35d4d44f0b9f1b210f38a034575b589c7211d55c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246675"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Linha de base de segurança azure para base de dados Azure SQL

A Base de Base de Segurança Azure para a Base de Dados Azure SQL contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para estes serviços é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com as nossas melhores práticas de orientação.

Para mais informações, consulte a [visão geral da Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de Rede

*Para mais informações, consulte [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos utilizando grupos de segurança de rede ou firewall azure na sua Rede Virtual

**Orientação**: Pode ativar o Azure Private Link para permitir o acesso aos Serviços Azure PaaS (por exemplo, Base de Dados SQL) e ao Azure hospedados serviços de clientes/parceiros sobre um Ponto Final Privado na sua rede virtual. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Para permitir que o tráfego chegue à Base de Dados Azure SQL, utilize as etiquetas de serviço SQL para permitir o tráfego de saída através de Grupos de Segurança da Rede.


As regras de rede virtual permitem que a Base de Dados Azure SQL apenas aceite comunicações enviadas a partir de subredes selecionadas dentro de uma rede virtual.


Como configurar link privado para base de dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database


Como utilizar pontos finais e regras de serviço de rede virtual para servidores de base de dados:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitore e registe a configuração e tráfego de Vnets, Subnets e NICs

**Orientação**: Utilize o Centro de Segurança Azure e remediar as recomendações de proteção da rede para a subrede para a qual o seu Servidor de Base de Dados Azure SQL está implantado. Para máquinas virtuais Azure (VM) que estarão conectadas à sua instância de Servidor de Base de Dados Azure SQL, ativar registos de fluxo do grupo de segurança de rede (NSG) para os NSGs que protegem esses VMs e enviar registos numa Conta de Armazenamento Azure para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho de Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.


Como ativar os registos de fluxo nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Compreender a Segurança da Rede fornecida pelo Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


Como ativar e utilizar o Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


Compreender a Segurança da Rede fornecida pelo Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; esta recomendação destina-se ao Azure Apps Service ou aos recursos de computação que acolhem aplicações web.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ativar a Norma de Proteção DDoS nas Redes Virtuais associadas às instâncias do Seu Servidor SQL para obter proteções contra ataques de negação de serviço distribuídos. Utilize o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet maliciosos ou não utilizados conhecidos.


Como configurar a proteção DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Compreender o Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Para máquinas virtuais Azure (VMs) que estarão a ligar-se à sua instância de Base de Dados Azure SQL, ativar os registos de fluxo do grupo de segurança da rede (NSG) para os NSGs que protegem esses VMs e enviar registos numa Conta de Armazenamento Azure para auditoria de tráfego. Se necessário para investigar atividades anómalas, ative a captura de pacotes do Observador da Rede.


Como ativar os registos de fluxo nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Como ativar o Observador da Rede:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Ativar a Proteção Avançada de Ameaças (ATP) para a Base de Dados Azure SQL.  Os utilizadores recebem um alerta sobre atividades suspeitas de bases de dados, potenciais vulnerabilidades e ataques de injeção SQL, bem como padrões anómalos de acesso à base de dados e consultas. A Advanced Threat Protection também integra alertas com o Centro de Segurança Azure.

Compreender e utilizar proteção avançada de ameaças para base de dados Azure SQL:https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; esta recomendação destina-se ao Azure Apps Service ou aos recursos de computação que acolhem aplicações web.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize etiquetas de serviço de rede virtuais para definir controlos de acesso à rede em grupos de segurança de rede ou firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Especificando o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.


Ao utilizar pontos finais de serviço para a Base de Dados Azure SQL, é necessário chegar aos endereços IP públicos da Base de Dados Azure SQL: Os Grupos de Segurança da Rede (NSGs) devem ser abertos aos IpS de Base de Dados Azure SQL para permitir a conectividade. Pode fazê-lo utilizando etiquetas de serviço NSG para base de dados Azure SQL.


Compreenda etiquetas de serviço com pontos finais de serviço para base de dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations


Compreender e utilizar etiquetas de serviço:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança de rede para as instâncias do servidor da Base de Dados Azure SQL com a Política Azure. Pode utilizar o espaço de nome "Microsoft.Sql" para definir definições de políticas personalizadas ou utilizar qualquer uma das definições de política incorporadas concebidas para a proteção da rede de servidores de dados Azure SQL. Um exemplo de uma política de segurança de rede incorporada aplicável para o servidor de base de dados Azure SQL seria: "O SQL Server deve utilizar um ponto final de serviço de rede virtual".
 

Utilize as plantas Azure para simplificar as implantações azure em larga escala através da embalagem de artefactos ambientais chave, tais como modelos de Gestão de Recursos Azure, controlo de acesso baseado em funções (RBAC) e políticas, numa única definição de blueprint. Aplique facilmente o projeto a novas subscrições e ambientes, e afinar o controlo e a gestão através da versão.


Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Como criar um Projeto Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Etiquetas de utilização para grupos de segurança de rede (NSG) e outros recursos relacionados com a segurança da rede e fluxo de tráfego. Para regras nsg individuais, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.


Utilize qualquer uma das definições políticas do Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e notificá-lo de recursos não marcados existentes.


Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou realizar ações em recursos baseados nas suas etiquetas.


Como criar e utilizar etiquetas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividade do Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com as instâncias do servidor da Base de Dados Azure SQL. Crie alertas dentro do Monitor Azure que irão desencadear quando ocorrerem alterações aos recursos críticos da rede.


Como visualizar e recuperar eventos de registo de atividade sinuosa:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view


Como criar alertas no Monitor Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para mais informações, consulte [Controlo de Segurança: Registo e Monitorização](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilize fontes de sincronização do tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para os recursos do Azure. Pode atualizar a sincronização do tempo para as suas implementações de cálculo.



Como configurar a sincronização do tempo para os recursos computacionais do Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central de registos de segurança

**Orientação**: Permitir a auditoria à Base de Dados Azure SQL para rastrear eventos de base de dados e escrevê-los num registo de auditoria na sua Conta de Armazenamento Azure, no espaço de trabalho do Log Analytics ou nos Centros de Eventos.


Além disso, pode transmitir telemetria de diagnóstico SQL Azure para o Azure SQL Analytics, uma solução em nuvem que monitoriza o desempenho de bases de dados Azure SQL, piscinas elásticas e instâncias geridas em escala e em várias subscrições. Pode ajudá-lo a recolher e visualizar as métricas de desempenho da Base de Dados Azure SQL, e tem inteligência incorporada para resolução de problemas de desempenho.

Como configurar a auditoria para a sua Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing


Como recolher métricas e registos de recursos com o Monitor Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging


Como transmitir diagnósticos para AnaLítica Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#configure-the-streaming-export-of-diagnostic-telemetry

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditoria aos recursos do Azure

**Orientação**: Ative a auditoria na sua instância de servidor de base de dados Azure SQL e escolha um local de armazenamento para os registos de auditoria (Armazenamento Azure, Log Analytics ou Event Hub).


Como permitir a auditoria ao Servidor Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta referência destina-se a recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de registos de segurança

**Orientação**: Ao armazenar os registos da sua Base de Dados Azure SQL num espaço de trabalho de Log Analytics, detete o período de retenção de registos de acordo com as regras de conformidade da sua organização.



Como definir parâmetros de retenção de troncos:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitore e reveja registos

**Orientação**: Analise e monitorize os registos para comportamentos anómalos e reveja regularmente os resultados. Utilize a Proteção avançada de ameaças do Azure Security Center para alertar sobre atividades incomuns relacionadas com a sua instância de Base de Dados Azure SQL. Alternativamente, configure alertas com base em valores métricos ou entradas de registo de atividade azure relacionadas com as instâncias da base de dados Azure SQL.


Compreender a Proteção Avançada de Ameaças e alertar para o Servidor Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview


Como configurar alertas personalizados para a Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Ativar alertas para atividades anómalas

**Orientação**: Utilize o Azure Security Center Advanced Threat Protection para bases de dados Azure SQL para monitorização e alerta sobre atividade sanómala. Ativar a Segurança avançada de dados para as suas Bases de Dados SQL. A Advanced Data Security inclui funcionalidades para descobrir e classificar dados sensíveis, superar e mitigar potenciais vulnerabilidades de bases de dados, e detetar atividades anómalas que possam indicar uma ameaça à sua base de dados.



Compreender a Proteção Avançada de Ameaças e alertar para a Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview



Como ativar a Segurança Avançada de Dados para a Base de Dados SQL Do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security



Como gerir alertas no Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

**Orientação**: Não aplicável; para o Azure SQL Server, a solução anti-malware é gerida pela Microsoft na plataforma subjacente.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a exploração de consultas dNS

**Orientação**: Não aplicável; A exploração madeireira DNS não é aplicável ao Servidor SQL Azure.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar a exploração de auditoria da linha de comando

**Orientação**: Não aplicável; A auditoria da linha de comando não é aplicável ao Servidor Azure SQL.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Controlo de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Diretório Ativo azure (AAD) tem funções incorporadas que devem ser explicitamente atribuídas e são consultadas. Utilize o módulo AAD PowerShell para realizar consultas ad-hoc para descobrir contas que são membros de grupos administrativos.


Como obter um papel de diretório em Azure AD com powerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


Como obter membros de um papel de diretório em Azure AD com PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar as palavras-passe por defeito sempre que aplicável

**Orientação**: O Diretório Ativo do Azure não tem o conceito de senhas padrão. Ao fornecer uma instância de Base de Dados Azure SQL, recomenda-se que opte por integrar a autenticação com o Diretório Ativo Azure.


Como configurar e gerir a autenticação do Diretório Ativo Azure com o Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar políticas e procedimentos em torno da utilização de contas administrativas dedicadas. Utilize a Identidade e Gestão de Acesso do Azure Security Center para monitorizar o número de contas administrativas.



Compreender identidade e acesso do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal (SSO) com diretório ativo Azure

**Orientação**: Não aplicável; enquanto pode configurar a Autenticação de Diretório Ativo Azure para integrar com o Servidor Azure SQL, não é suportado um único sinal.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação multifactor para todos os acessos baseados em Diretório Ativo Azure

**Orientação**: Ativar o Diretório Ativo Azure (AAD) Autenticação multi-factor (MFA) e seguir as recomendações de Identidade e Gestão de Acesso do Azure Security Center.


Como permitir o MFA em Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


Como monitorizar a identidade e o acesso dentro do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Postos de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Utilize uma estação de trabalho de acesso privilegiado (PAW) com MFA de autenticação multi-factor configurada para iniciar sessão e configurar os recursos Azure.


Saiba mais sobre postos de trabalho de acesso privilegiados:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


Como permitir o MFA em Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança do Diretório Ativo Azure para a geração de registos e alertas quando ocorrer atividade suspeita ou insegura no ambiente.



Utilize proteção avançada de ameaças para a Base de Dados Azure SQL para detetar atividades anómalas que indiquem tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados.



Como identificar utilizadores de Anúncios Azure sinalizados para atividades de risco:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk



Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Do Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access



Reveja a Proteção Avançada de Ameaças e potenciais alertas:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados

**Orientação**: Utilizar locais de acesso condicional nomeados para permitir o acesso do Portal e da Gestão de Recursos Azure a partir de apenas agrupamentos lógicos específicos de gamas de endereços IP ou países/regiões.


Como configurar localizações nomeadas em Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Crie um administrador de Diretório Ativo Azure (AAD) para as instâncias do servidor de base de dados Azure SQL.


Como configurar e gerir a autenticação do Diretório Ativo Azure com o Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure


Como criar e configurar uma instância AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever regularmente e conciliar o acesso ao utilizador

**Orientação**: O Diretório Ativo Azure (AAD) fornece registos para ajudar a descobrir contas velhas. Além disso, utilize as avaliações de acesso à Identidade Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de papéis. O acesso dos utilizadores pode ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado.


Como utilizar comentários de acesso à identidade do Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização tenta aceder a contas desativadas

**Orientação**: Configure Azure Ative Directory (AAD) autenticação com O SQL Azure e crie Definições de Diagnóstico para contas de utilizadores do Diretório Ativo Azure, enviando os registos de auditoria e registos de log-in para um espaço de trabalho de Log Analytics. Configure os alertas desejados dentro do espaço de trabalho do Log Analytics.


Como configurar e gerir a autenticação do Diretório Ativo Azure com o Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure


Como integrar registos de atividade do Azure no Monitor Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize a Proteção de Identidade ativa do Azure (AAD) e deteções de risco para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com identidades do utilizador. Além disso, pode ingerir dados no Azure Sentinel para mais investigação.


Como ver os sign-ins de risco da AD Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


Como configurar e ativar políticas de risco de proteção de identidade:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante os cenários de suporte

**Orientação**: Em cenários de suporte em que a Microsoft precisa de aceder aos dados dos clientes, o Azure Customer Lockbox fornece uma interface para os clientes reverem e aprovarem ou rejeitarem os pedidos de acesso aos dados dos clientes.


Compreender o Bloqueio do Cliente:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para mais informações, consulte [Controlo de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos do Azure que armazenam ou processam informações sensíveis.


Como criar e utilizar etiquetas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou processamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Os recursos devem ser separados por Vnet/Subnet, marcados adequadamente e protegidos dentro de uma NsG ou Firewall Azure. Os recursos que armazenam ou processam dados sensíveis devem ser isolados. Utilizar link privado; implementar o Servidor Azure SQL dentro do seu Vnet e ligar-se em privado usando pontos finais privados.



Como criar subscrições adicionais do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription



Como criar Grupos de Gestão:

https://docs.microsoft.com/azure/governance/management-groups/create



Como criar e utilizar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



Como configurar link privado para base de dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorizar e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Para bases de dados Azure SQL armazenando ou processando informações sensíveis, marque a base de dados e os recursos conexos como sensíveis utilizando tags. Configure o Link Privado em conjunto com as etiquetas de serviço do Grupo de Segurança da Rede nas instâncias da base de dados Azure SQL para evitar a exfiltração de informações sensíveis.



Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.



Como configurar private link e NSGs para evitar a exfiltração de dados nas instâncias da base de dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview



Compreender a proteção de dados dos clientes em Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: A Base de Dados Azure SQL protege os seus dados encriptando os dados em movimento com a Segurança da Camada de Transporte. O SQL Server aplica sempre a encriptação (SSL/TLS) para todas as ligações. Isto garante que todos os dados estão encriptados "em trânsito" entre o cliente e o servidor, independentemente da definição de Encrypt ou TrustServerCertificate na cadeia de ligação.



Compreender a encriptação Azure SQL em Trânsito:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Utilize a funcionalidade de deteção e classificação de dados da Base de Dados Azure SQL. A descoberta e classificação de dados fornece capacidades avançadas incorporadas na &amp; Base de Dados Azure SQL para descobrir, classificar, rotular protegendo os dados sensíveis nas suas bases de dados.



Como utilizar a descoberta e classificação de dados para o Servidor Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o Diretório Ativo Azure (AAD) para autenticar e controlar o acesso às instâncias da Base de Dados Azure SQL.


Como integrar o Servidor Azure SQL com o Diretório Ativo Azure para autenticação:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication


Como controlar o acesso no Servidor Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: A Microsoft gere a infraestrutura subjacente à Base de Dados Azure SQL e implementou controlos rigorosos para evitar a perda ou exposição dos dados dos clientes.

Compreender a proteção de dados dos clientes em Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

**Orientação**: A encriptação transparente de dados (TDE) ajuda a proteger a Base de Dados Azure SQL, a instância gerida pelo Azure SQL e o Azure Data Warehouse contra a ameaça de atividade offline maliciosa, encriptando dados em repouso. Realiza a encriptação e desencriptação em tempo real da base de dados, cópias de segurança associadas e ficheiros de registo de transações inativos e não carece de alterações à aplicação. Por predefinição, a Encriptação de Dados Transparente está ativada para todas as Bases de Dados SQL do Azure recém-implementadas. A chave de encriptação TDE pode ser gerida pela Microsoft ou pelo cliente.


Como gerir a encriptação transparente de dados e usar as suas próprias chaves de encriptação:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades do Azure para criar alertas para quando ocorrerem alterações nas instâncias de produção das Bases de Dados Azure SQL e outros recursos críticos ou conexos.


Como criar alertas para eventos de Registo de Atividade seleções do Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas de digitalização automática de vulnerabilidades

**Orientação**: Permitir segurança avançada de dados para base de dados Azure SQL e seguir recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nos seus Servidores Azure SQL.



Como executar avaliações de vulnerabilidade nas suas bases de dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment



Como ativar a Segurança Avançada de Dados:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security



Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de patch do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patch de software de terceiros

**Orientação**: Não aplicável; esta referência destina-se a recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar as varreduras de vulnerabilidade consecutivas

**Orientação**: Ativar exames periódicos recorrentes para as instâncias da base de dados Azure SQL; isto configurará uma avaliação de vulnerabilidade para executar automaticamente uma varredura na sua base de dados uma vez por semana. Um resumo do resultado da digitalização será enviado para o endereço de e-mail(es) que fornecer. Compare os resultados para verificar se as vulnerabilidades foram remediadas.



Como exportar um relatório de avaliação de vulnerabilidade no Centro de Segurança Azure:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implement-vulnerability-assessment

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: Utilize as classificações de risco padrão (Pontuação Segura) fornecidas pelo Azure Security Center.

Compreenda a pontuação segura do Centro de Segurança Azure:https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Utilizar a Descoberta de Ativos Azure

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar e descobrir todos os recursos (incluindo as instâncias do Servidor Azure SQL) dentro da sua subscrição(s).  Certifique-se de que tem permissões adequadas (ler) no seu inquilino e que é capaz de enumerar todas as subscrições do Azure, bem como recursos dentro das suas subscrições.


Embora os recursos clássicos do Azure possam ser descobertos através do Azure Resource Graph, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.


Como criar consultas com o Gráfico de Recursos Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


Como visualizar as suas Assinaturas Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


Compreender o Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nos recursos do Azure, dando metadados para logicamente organizá-los numa taxonomia.



Como criar e utilizar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos Azure não autorizados

**Orientação**: Utilize a marcação, os grupos de gestão e as assinaturas separadas, se for caso disso, para organizar e rastrear os ativos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição atempadamente.



Como criar subscrições adicionais do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription



Como criar Grupos de Gestão:

https://docs.microsoft.com/azure/governance/management-groups/create



Como criar e utilizar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos títulos aprovados de recursos e software do Azure

**Orientação**: Definir lista de recursos azure aprovados e software aprovado para os seus recursos computacionais

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para os recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Gráfico de Recursos Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos do cálculo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos não aprovados do Azure e aplicações de software

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Utilize apenas pedidos aprovados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas os serviços Azure aprovados

**Orientação**: Utilize a Política Azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição do cliente utilizando as seguintes definições políticas incorporadas:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo específico de recurso com a Política Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações em execução de recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure através de scripts

**Orientação**: Utilize o Acesso Condicional azure para limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure configurando o "Block access" para a app "Microsoft Azure Management".


Como configurar o Acesso Condicional ao acesso ao portal Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacionais

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar fisicamente ou logicamente aplicações de alto risco

**Orientação**: Não aplicável; esta recomendação destina-se ao App Service ou aos recursos de computação que hospedam desktop ou aplicações web.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração Segura

*Para mais informações, consulte [Controlo de Segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: Utilize recomendações da Política Azure ou do Azure Security Center para os servidores/bases de dados Azure SQL para manter configurações de segurança para todos os recursos azure.


Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destinada aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação:** Utilize a política azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.



Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



Compreender efeitos políticos do Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração segura dos recursos Do Azure

**Orientação**: Se utilizar definições políticas personalizadas do Azure, utilize o Azure DevOps ou o Azure Repos para armazenar e gerir de forma segura o seu código.



Como armazenar código em Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops



Documentação Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.SQL" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolver um processo e um oleoduto para gerir as exceções políticas.



Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para os serviços Do Azure

**Orientação**: Leverage Azure Security Center para realizar digitalizações de base para os seus Servidores e Bases de Dados Azure SQL.



Como remediar recomendações no Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize o Cofre de Chaves Azure para armazenar chaves de encriptação para encriptação transparente de dados transparentes da Base de Dados Azure SQL (TDE).



Como proteger os dados sensíveis armazenados no Azure SQL Server e armazenar as chaves de encriptação no Cofre de Chaves Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize identidades geridas para fornecer aos serviços Azure uma identidade gerida automaticamente no Diretório Ativo Azure (AAD). Identidades Geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AAD, incluindo o Cofre chave Azure, sem qualquer credencial no seu código.


Tutorial: Utilize uma identidade gerida atribuída ao sistema Windows VM para aceder ao Azure SQL:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql


Como configurar identidades geridas:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implemente o Scanner Credencial para identificar credenciais dentro do seu código. O Credential Scanner também incentivará a mudança de credenciais descobertas para locais mais seguros, como o Cofre chave Azure. 

Como configurar o Scanner Credencial:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos. A Microsoft lida com anti-malware para a plataforma subjacente.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem carregados para recursos não computacionais do Azure

**Orientação**: O anti-malware da Microsoft está ativado no hospedeiro subjacente que suporta os serviços Do Azure (por exemplo, o Azure App Service), no entanto não funciona com conteúdo do cliente.


Pré-digitalização de qualquer conteúdo que seja enviado para recursos não computacionais do Azure, tais como Serviço de Aplicações, Armazenamento de Data Lake, Armazenamento Blob, Servidor Azure SQL, etc. A Microsoft não pode aceder aos seus dados nestes casos.


Compreenda o Microsoft Antimalware para Serviços Azure Cloud e Máquinas Virtuais:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que software anti-malware e assinaturas são atualizados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos. A Microsoft lida com anti-malware para a plataforma subjacente.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de Dados

*Para mais informações, consulte [Controlo de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automáticos regulares

**Orientação**: Para proteger o seu negócio da perda de dados, a Base de Dados Azure SQL cria automaticamente cópias de dados completas semanais, backups de bases de dados diferenciais a cada 12 horas, e cópias de segurança de registo de transações a cada 5 a 10 minutos. As cópias de segurança são armazenadas no armazenamento RA-GRS durante pelo menos 7 dias para todos os níveis de serviço. Todos os níveis de serviço, exceto o período de retenção de backup configurável de suporte básico para restauro pontual, até 35 dias.


Para satisfazer diferentes requisitos de conformidade, pode selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuais. O consumo de armazenamento depende da frequência selecionada de cópias de segurança e do período de retenção.


Compreenda backups e continuidade de negócios com o Servidor Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Partilhado

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e fazer cópias de segurança de quaisquer chaves geridas pelo cliente

**Orientação**: A Base de Dados Azure SQL cria automaticamente as cópias de segurança da base de dados que são mantidas entre 7 e 35 dias e utiliza o armazenamento geo-redundante de acesso à leitura azure (RA-GRS) para garantir que são preservadas mesmo que o centro de dados não esteja disponível. Estas cópias de segurança são criadas automaticamente. Se necessário, ative cópias de segurança georedundantes a longo prazo para as suas Bases de Dados Azure SQL.


Se utilizar as chaves geridas pelo cliente para encriptação de dados transparentes, certifique-se de que as suas chaves estão a ser apoiadas.


Compreenda as cópias de segurança no Servidor Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database


Como apoiar as chaves do cofre em Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Garantir a capacidade de realizar periodicamente a restauração de dados dos conteúdos no âmbito da Cópia de Segurança Azure. Se necessário, teste restaurar o conteúdo de um VLAN isolado. Teste a restauração de chaves apoiadas pelo cliente.


Como restaurar as chaves do cofre chave em Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


Como recuperar as cópias de segurança da Base de Dados Azure SQL utilizando a restauração ponto-a-tempo:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Ativar a eliminação suave no Cofre de Chaves Azure para proteger as chaves contra a eliminação acidental ou maliciosa.


Como permitir a eliminação suave no Cofre chave:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para mais informações, consulte [Controlo de Segurança: Resposta](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)a Incidentes .*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Certifique-se de que existem planos escritos de resposta a incidentes que definam funções de pessoal, bem como fases de tratamento/gestão de incidentes.



Como configurar as automatizações de fluxo de trabalho dentro do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma severidade aos alertas, para o ajudar a priorizar a ordem em que atende a cada alerta, para que, quando um recurso estiver comprometido, possa chegar ao mesmo imediatamente. A gravidade baseia-se na confiança do Centro de Segurança na descoberta ou na analítica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.
Alertas de segurança no Centro de Segurança Azure:https://docs.microsoft.com/azure/security-center/security-center-alerts-overview


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.



Pode consultar a publicação do NIST: Guia para Programas de Teste, Formação e Exercício para Planos e Capacidades de TI:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada.



Como definir o Contacto de Segurança do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade De Exportação Contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança Azure para transmitir os alertas para o Sentinel.


Como configurar a exportação contínua:

https://docs.microsoft.com/azure/security-center/continuous-export


Como transmitir alertas para O Sentinela Azul:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a funcionalidade de automatização de fluxos de trabalho no Centro de Segurança Azure para ativar automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.



Como configurar a Automatização do Fluxo de Trabalho e aplicações lógicas:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para mais informações, consulte [Controlo de Segurança: Testes de Penetração e Exercícios de Equipa Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias

**Orientação**: Siga as Regras de Envolvimento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.



Pode encontrar mais informações sobre a estratégia e execução da Microsoft de Red Teaming e testes de penetração no site ao vivo contra a Microsoft gerida por infraestruturas, serviços e aplicações em nuvem, aqui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

## <a name="next-steps"></a>Passos seguintes

- Ver o [Benchmark de Segurança Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
