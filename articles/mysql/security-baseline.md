---
title: Linha de segurança Azure para Azure Database para MySQL
description: A Base de Dados Azure para a linha de base de segurança MySQL fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: mysql
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c05abc6b761b9839df83f3170ce295d4480ca96a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100577917"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>Linha de segurança Azure para Azure Database para MySQL

A Linha de Base de Segurança Azure para a Base de Dados Azure para o MySQL contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../security/benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte [a visão geral da Azure Security Baselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte o [Azure Security Benchmark: Segurança da rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Configurar link privado para base de dados Azure para MySQL com pontos finais privados. A Ligação Privada permite-lhe estabelecer ligação a vários serviços PaaS no Azure através de um ponto final privado. O Azure Private Link essencialmente traz os serviços do Azure dentro da sua Rede Privada Virtual (VNet). O tráfego entre a sua rede virtual e o caso MySQL percorre a rede de espinha dorsal da Microsoft.

Em alternativa, poderá utilizar pontos finais de serviço de rede virtual para proteger e limitar o acesso à rede à sua Base de Dados Azure para implementações do MySQL. As regras de rede virtual são uma funcionalidade de segurança de firewall que controla se a sua Base de Dados Azure para o servidor MySQL aceita comunicações que são enviadas a partir de sub-redes específicas em redes virtuais.

Também pode proteger a sua Base de Dados Azure para o servidor MySQL com regras de firewall. A firewall do servidor impede todo o acesso ao servidor de base de dados até especificar quais os computadores que têm permissão. Para configurar a firewall, crie as regras de firewall que especificam intervalos de endereços IP aceitáveis. Pode criar regras de firewall ao nível do servidor.

- [Como configurar link privado para base de dados Azure para MySQL](howto-configure-privatelink-portal.md)

- [Como criar e gerir os pontos finais do serviço VNet e as regras VNet na Base de Dados Azure para o MySQL](../azure-sql/database/vnet-service-endpoint-rule-overview.md)

- [Como configurar a base de dados Azure para as regras de firewall mySQL](howto-manage-firewall-using-portal.md)

**Monitorização do Centro de Segurança Azure**: Não disponível

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Quando a sua base de dados Azure para a instância MySQL estiver protegida a um ponto final privado, pode implantar máquinas virtuais na mesma rede virtual. Pode utilizar um grupo de segurança de rede (NSG) para reduzir o risco de exfiltração de dados. Ativar os registos de fluxo NSG e enviar registos para uma conta de armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como configurar link privado para base de dados Azure para MySQL](howto-configure-privatelink-portal.md)

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Utilize proteção de ameaças avançadas para base de dados Azure para o MySQL. A Advanced Threat Protection deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados.

Ativar a Norma de Proteção DDoS nas Redes Virtuais associadas à sua Base de Dados Azure para instâncias MySQL para proteger contra ataques de DDoS. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

- [Como configurar a Proteção Avançada de Ameaças para Base de Dados Azure para o MySQL](howto-database-threat-protection-portal.md)

- [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Quando a sua base de dados Azure para a instância MySQL estiver protegida a um ponto final privado, pode implantar máquinas virtuais na mesma rede virtual. Em seguida, pode configurar um grupo de segurança de rede (NSG) para reduzir o risco de exfiltração de dados. Ativar os registos de fluxo NSG e enviar registos para uma conta de armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Utilize proteção de ameaças avançadas para base de dados Azure para o MySQL. A Advanced Threat Protection deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados.

- [Como configurar a Proteção Avançada de Ameaças para Base de Dados Azure para o MySQL](howto-database-threat-protection-portal.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos que necessitem de acesso à sua Base de Dados Azure para instâncias MySQL, utilize Tags de Serviço de Rede Virtual para definir controlos de acesso à rede em Grupos de Segurança de Rede ou Firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Especificando o nome da etiqueta de serviço (por exemplo, SQL. WestUs) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Nota: A Azure Database for MySQL utiliza as tags de serviço "Microsoft.Sql".

- [Para mais informações sobre a utilização de tags de serviço](../virtual-network/service-tags-overview.md)

- [Compreender o uso da etiqueta de serviço para a base de dados Azure para o MySQL](concepts-data-access-and-security-vnet.md#terminology-and-description)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para configurações de rede e recursos de rede associados à sua Base de Dados Azure para instâncias MySQL com Azure Policy. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.DBforMySQL" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede da sua Base de Dados Azure para instâncias MySQL. Pode também utilizar definições de políticas incorporadas relacionadas com a rede ou a sua Base de Dados Azure para instâncias MySQL, tais como:

- A Norma de Proteção DDoS deve ser ativada

- A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Amostras da Política Azure para networking](../governance/policy/samples/index.md)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para recursos relacionados com a segurança da rede e fluxo de tráfego para a sua Base de Dados Azure para instâncias MySQL para fornecer metadados e organização lógica.

Utilize qualquer uma das definições de Política Azure incorporadas relacionadas com a marcação, tais como **a etiqueta Require e o seu valor** para garantir que todos os recursos são criados com etiquetas e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações nos recursos de rede relacionados com a sua Base de Dados Azure para instâncias MySQL. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registar e monitorizar](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém a fonte de tempo utilizada para os recursos Azure, como a Base de Dados Azure para o MySQL para os timetamps nos registos.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ative as definições de diagnóstico e registos de servidores e ingere registos para agregar dados de segurança gerados pela sua Base de Dados Azure para instâncias MySQL. Dentro do Azure Monitor, utilize o Log Analytics Workspace(s) para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

- [Compreender registos de servidores para base de dados Azure para o MySQL](concepts-monitoring.md#server-logs)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança Azure**: Não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ativar as definições de diagnóstico na sua Base de Dados Azure para os casos mySQL para acesso a registos de auditoria, consulta lenta e registos de métricas MySQL. Certifique-se de que ativa especificamente o registo de auditoria mySQL. Os registos de atividade, que estão automaticamente disponíveis, incluem fonte de evento, data, utilizador, marcação de tempo, endereços de origem, endereços de destino e outros elementos úteis. Pode também ativar as Definições de Diagnóstico do Registo de Atividade do Azure e enviar os registos para o mesmo espaço de trabalho ou conta de armazenamento do Log Analytics.

- [Compreender registos de servidores para base de dados Azure para o MySQL](concepts-monitoring.md#server-logs)

- [Como configurar e aceder a registos de consulta lenta para Azure Database for MySQL](howto-configure-server-logs-in-portal.md)

- [Como configurar e aceder a registos de auditoria para Azure Database for MySQL](howto-configure-audit-logs-portal.md)

- [Como configurar definições de diagnóstico para o registo de atividades azure](../azure-monitor/essentials/activity-log.md)

**Monitorização do Centro de Segurança Azure**: Não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: No âmbito do Monitor Azure, para o espaço de trabalho Log Analytics utilizado para manter a sua Base de Dados Azure para registos MySQL, desagure o período de retenção de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

- [Como definir parâmetros de retenção de registos para log analytics workspaces](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Armazenar registos de recursos numa conta de armazenamento Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Analise e monitorize registos da sua Base de Dados Azure para casos de MySQL para comportamento anómalo. Utilize o Azure Monitor's Log Analytics para rever registos e efetuar consultas nos dados de registo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Para mais informações sobre o Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Permitir a proteção avançada de ameaças para a base de dados Azure para o MySQL. A Advanced Threat Protection deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados.

Além disso, pode ativar registos de servidores e definições de diagnóstico para o MySQL e enviar registos para um espaço de trabalho do Log Analytics. A bordo do seu espaço de trabalho Log Analytics para Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar playbooks (soluções automatizadas) e ser usados para remediar problemas de segurança.

- [Como permitir a proteção avançada de ameaças para base de dados de azure para o MySQL (pré-visualização)](howto-database-threat-protection-portal.md)

- [Compreender registos de servidores para base de dados Azure para o MySQL](concepts-monitoring.md#server-logs)

- [Como configurar e aceder a registos de consulta lenta para Azure Database for MySQL](howto-configure-server-logs-in-portal.md)

- [Como configurar e aceder a registos de auditoria para Azure Database for MySQL](howto-configure-audit-logs-portal.md)

- [Como configurar definições de diagnóstico para o registo de atividades azure](../azure-monitor/essentials/activity-log.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Não aplicável; A Azure Database for MySQL não processa ou produz registos relacionados com anti-malware.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Não aplicável; A Azure Database for MySQL não processa ou produz registos relacionados com DNS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e controlo de acessos](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Mantenha um inventário das contas de utilizador que tenham acesso administrativo ao plano de gestão (por exemplo, portal Azure) da sua Base de Dados Azure para MySQLinstances. Além disso, mantenha um inventário das contas administrativas que tenham acesso ao plano de dados (dentro da própria base de dados) da sua Base de Dados Azure para casos mySQL. (Ao criar o servidor MySQL, fornece credenciais para um utilizador administrador. Este administrador pode ser usado para criar utilizadores adicionais do MySQL.)

A Azure Database for MySQL não suporta o controlo de acesso baseado em funções incorporado, mas pode criar funções personalizadas baseadas em opções específicas de fornecedores de recursos.

- [Compreenda as funções personalizadas para a subscrição do Azure](../role-based-access-control/custom-roles.md) 

- [Compreender a base de dados Azure para as operações do fornecedor de recursos MySQL](../role-based-access-control/resource-provider-operations.md#microsoftdbformysql)

- [Compreender a gestão de acessos para a Base de Dados Azure para o MySQL](concepts-security.md#access-management)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: A Azure AD não tem o conceito de senhas padrão.

Após a criação da Base de Dados Azure para o próprio recurso MySQL, o Azure força a criação de um utilizador administrativo com uma senha forte. No entanto, uma vez criado o caso MySQL, poderá utilizar a primeira conta de administração do servidor que criou para criar utilizadores adicionais e conceder acesso administrativo aos mesmos. Ao criar estas contas, certifique-se de que configura uma senha diferente e forte para cada conta.

- [Como criar contas adicionais para a Base de Dados Azure para o MySQL](howto-create-users.md)

- [Como atualizar a palavra-passe de administrador](howto-create-manage-server-portal.md#update-admin-password)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Crie procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas que tenham acesso à sua Base de Dados Azure para instâncias MySQL. Utilize a identidade do Centro de Segurança Azure e a gestão de acessos para monitorizar o número de contas administrativas.

- [Compreender a identidade e o acesso do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

- [Compreender como criar utilizadores administrativos na Base de Dados Azure para o MySQL](howto-create-users.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: A assinatura na Base de Dados Azure para o MySQL é suportada tanto através do nome de utilizador/palavra-passe configurado diretamente na base de dados, como utilizando uma identidade do Diretório Ativo Azure (AD) e utilizando um token AD Azure para ligar. Ao utilizar um token AD Azure, são suportados diferentes métodos, tais como um utilizador AD Azure, um grupo AD Azure ou uma aplicação AD AZure conectando-se à base de dados.

Separadamente, o acesso do avião de controlo para o MySQL está disponível através da REST API e suporta sSO. Para autenticar, desacorda o cabeçalho de Autorização para os seus pedidos num Token Web JSON que obtenha do Azure Ative Directory.

- [Utilize o Azure Ative Directy para autenticar com base de dados Azure para o MySQL](howto-configure-sign-in-azure-ad-authentication.md)

- [Compreender a base de dados Azure para MySQL REST API](/rest/api/mysql/)

- [Compreender SSO com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Ativar a autenticação multi-factor do Diretório Ativo Azure (MFA) e seguir as recomendações do Azure Security Center Identity and Access Management. Ao utilizar fichas AD Azure para iniciar sessão na sua base de dados, isto permite-lhe requerer autenticação multi-factor para iniciar sessão de base de dados.

- [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Utilize o Azure Ative Directy para autenticar com base de dados Azure para o MySQL](howto-configure-sign-in-azure-ad-authentication.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Utilizar estações de trabalho seguras e geridas pelo Azure para tarefas administrativas

**Orientação**: Utilize estações de trabalho de acesso privilegiada (PAWs) com autenticação multi-factor (MFA) configuradas para iniciar sessão e configurar recursos Azure.

- [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Permitir a Proteção Avançada de Ameaças para a Base de Dados de Azure para o MySQL gerar alertas para atividades suspeitas.

Além disso, pode utilizar a Azure AD Privileged Identity Management (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente.

Utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

- [Como configurar a Proteção Avançada de Ameaças para Base de Dados Azure para o MySQL](howto-database-threat-protection-portal.md)

- [Como implementar gestão de identidade privilegiada (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Compreender deteções de risco Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso do portal e do Gestor de Recursos Azure a partir de apenas agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

Para iniciar sessão na Base de Dados Azure para o MySQL é aconselhável utilizar a Azure AD e utilizar um token AD Azure para ligar. Ao utilizar um token AD Azure, são suportados diferentes métodos, tais como um utilizador AD Azure, um grupo AD Azure ou uma aplicação AD AZure conectando-se à base de dados.

As credenciais AD do AZure também podem ser utilizadas para a administração a nível do plano de gestão (por exemplo, o portal Azure) para controlar as contas de administração do MySQL.

- [Utilize o Azure Ative Directy para autenticar com base de dados Azure para o MySQL](howto-configure-sign-in-azure-ad-authentication.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: Reveja os registos do Azure Ative Directory para ajudar a descobrir contas velhas que podem incluir as que têm Base de Dados Azure para funções administrativas do MySQL. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais que possam ser usadas para aceder à Base de Dados Azure para o MySQL e atribuições de funções. O acesso ao utilizador deve ser revisto regularmente, como a cada 90 dias, para garantir que apenas os Utilizadores certos tenham acesso continuado.

- [Compreenda a Azure Ad Reporting](../active-directory/reports-monitoring/index.yml)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Ativar as definições de diagnóstico da Base de Dados Azure para o MySQL e o Azure Ative Directory, enviando todos os registos para um espaço de trabalho log analytics. Configure os alertas desejados (tais como tentativas de autenticação falhada) no Log Analytics.

- [Como configurar e aceder a registos de consulta lenta para Azure Database for MySQL](./howto-configure-server-logs-in-portal.md)

- [Como configurar e aceder a registos de auditoria para Azure Database for MySQL](./howto-configure-audit-logs-portal.md)

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorização do Centro de Segurança Azure**: Não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Permitir a Proteção Avançada de Ameaças para a Base de Dados de Azure para o MySQL gerar alertas para atividades suspeitas.

Utilize as funcionalidades de Proteção de Identidade do Azure Ative Directory e funcionalidades de deteção de riscos para configurar respostas automatizadas para ações suspeitas detetadas. Pode permitir respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização.

Você também pode ingerir troncos em Azure Sentinel para mais investigação.

- [Como configurar a Proteção Avançada de Ameaças para Base de Dados Azure para o MySQL](howto-database-threat-protection-portal.md)

- [Visão geral da Proteção de Identidade AD AZure](../active-directory/identity-protection/overview-identity-protection.md)

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança Azure**: Não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Não aplicável; O Lockbox do cliente ainda não está suportado para a Base de Dados Azure para o MySQL.

- [Lista de serviços suportados pelo Bloqueio do Cliente](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear a Base de Dados Azure para instâncias MySQL ou recursos relacionados que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Utilize uma combinação de regras private link, endpoints de serviço e/ou firewall para isolar e limitar o acesso à rede à sua Base de Dados Azure para instâncias MySQL.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como configurar link privado para base de dados Azure para MySQL](concepts-data-access-security-private-link.md)

- [Como criar e gerir os pontos finais do serviço VNet e as regras VNet na Base de Dados Azure para o MySQL](../azure-sql/database/vnet-service-endpoint-rule-overview.md)

- [Como configurar a base de dados Azure para as regras de firewall mySQL](concepts-firewall-rules.md)

**Monitorização do Centro de Segurança Azure**: Não disponível

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Ao utilizar VMs Azure para aceder à Base de Dados de Azure para instâncias MySQL, utilize configurações de ligações privadas, configurações de rede MySQL, grupos de segurança de rede e tags de serviço para mitigar a possibilidade de exfiltração de dados.

A Microsoft gere a infraestrutura subjacente para a Azure Database para o MySQL e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

- [Como mitigar a exfiltração de dados para a base de dados Azure para o MySQL](concepts-data-access-security-private-link.md#data-exfiltration-prevention)

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Azure Database for MySQL suporta ligar o seu servidor MySQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A imposição de ligações SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação. No portal Azure, certifique-se de que "Enforce SSL connection" está ativado para todos os seus casos de Azure Database para as instâncias MySQL por padrão.

Atualmente, a versão TLS suportada para Azure Database for MySQL são TLS 1.0, TLS 1.1, TLS 1.2.

- [Como configurar a encriptação em trânsito para a Base de Dados Azure para o MySQL](concepts-ssl-connection-security.md)

**Monitorização do Centro de Segurança Azure**: Não disponível

**Responsabilidade**: Partilhada

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para a Base de Dados Azure para o MySQL. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança Azure**: Não disponível

**Responsabilidade**: Partilhada

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para controlar o acesso à Base de Dados Azure para o plano de controlo MySQL (por exemplo, portal Azure). Para o acesso a um plano de dados (dentro da própria base de dados), utilize consultas SQL para criar utilizadores e configurar permissões do utilizador. O Azure RBAC não afeta as permissões do utilizador dentro da base de dados.

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Como configurar o acesso do utilizador com SQL para Azure Database para o MySQL](howto-create-users.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

A Microsoft gere a infraestrutura subjacente para a Azure Database para o MySQL e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: A Base de Dados Azure para o serviço MySQL utiliza o módulo criptográfico validado FIPS 140-2 para encriptação de armazenamento de dados em repouso. Os dados, incluindo cópias de segurança, são encriptados no disco, com exceção dos ficheiros temporários criados durante a execução de consultas. O serviço utiliza a cifra AES de 256 bits incluída na encriptação de armazenamento Azure, e as teclas são geridas pelo sistema. A encriptação de armazenamento está sempre ativada e não pode ser desativada.

A encriptação de dados com chaves geridas pelo cliente da Base de Dados do Azure para MySQL permite-lhe trazer a sua própria chave (BYOK) para a proteção de dados inativos. Neste momento, deve solicitar o acesso para utilizar esta capacidade. Para tal, contacte:

AskAzureDBforMySQL@service.microsoft.com

- [Compreenda a encriptação em repouso para a Base de Dados Azure para o MySQL](concepts-security.md)

- [Como configurar chaves geridas pelo cliente para a base de dados Azure para o MySQL](concepts-data-encryption-mysql.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações nas instâncias de produção da Base de Dados Azure para o MySQL e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte o [Azure Security Benchmark: Gestão de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center sobre a garantia da sua Base de Dados Azure para o MySQL e recursos conexos.

A Microsoft realiza a gestão de vulnerabilidades nos sistemas subjacentes que suportam a Base de Dados Azure para o MySQL.

- [Compreenda as recomendações do Centro de Segurança Azure](../security-center/recommendations-reference.md)

- [Cobertura de recursos para serviços Azure PaaS no Azure Security Center](../security-center/features-paas.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implementar solução automatizada de gestão de patchs para títulos de software de terceiros

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: A Microsoft realiza a gestão de vulnerabilidades nos sistemas subjacentes que suportam a Base de Dados Azure para o MySQL.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descobrir todos os recursos (incluindo a Base de Dados Azure para instâncias MySQL) dentro das suas subscrições. Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas na Base de Dados Azure para instâncias MySQL e outros recursos relacionados que dão metadados para organizá-los logicamente numa taxonomia.

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear a Base de Dados Azure para instâncias MySQL e recursos relacionados. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional e a Azure como um todo.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, utilize o Gráfico de Recursos Azure para consultar e descobrir recursos dentro das subscrições.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional e a Azure como um todo.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/index.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management". Isto pode impedir a criação e alterações de recursos num ambiente de alta segurança, como casos de Azure Database for MySQL contendo informações confidenciais.

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para a sua Base de Dados Azure para instâncias MySQL com Azure Policy. Utilize pseudónimos da Azure Policy no espaço de nomes **Microsoft.DBforMySQL** para criar políticas personalizadas para auditar ou impor a configuração de rede da sua Base de Dados Azure para instâncias MySQL. Também pode utilizar definições de políticas incorporadas relacionadas com a sua Base de Dados Azure para instâncias MySQL, tais como:

A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure para a sua Base de Dados Azure para instâncias MySQL e recursos relacionados, utilize o Azure Repos para armazenar e gerir de forma segura o seu código.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

- [Documentação de Azure Repos](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.DBforMySQL" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes **Microsoft.DBforMySQL** para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Utilize a Política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para a sua Base de Dados Azure para instâncias MySQL e recursos relacionados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service sendo usado para aceder à sua Base de Dados Azure para instâncias MySQL, utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e proteger a Base de Dados Azure para a gestão secreta do MySQL. Certifique-se de que o cofre da chave é excluído suave.

- [Como integrar-se com identidades geridas aZure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um Cofre-Chave](../key-vault/general/quick-create-portal.md)

- [Como fornecer a autenticação do Cofre-Chave com uma identidade gerida](../key-vault/general/assign-access-policy-portal.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Azure Database for MySQL instance suporta a autenticação do Azure Ative Directory para aceder a bases de dados.  Ao criar a Base de Dados Azure para o caso MySQL, fornece credenciais para um utilizador administrador. Este administrador pode ser utilizado para criar utilizadores de bases de dados adicionais.  

Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service sendo usado para aceder à sua Base de Dados Azure para instâncias MySQL, utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para armazenar e recuperar credenciais para Azure Database para o caso MySQL. Certifique-se de que o cofre da chave é excluído suave.

Utilize identidades geridas para fornecer serviços Azure com uma identidade gerida automaticamente no Azure Ative Directory (AD). Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

- [Como configurar identidades geridas](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Como integrar-se com identidades geridas aZure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte o [Azure Security Benchmark: Malware defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizar software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

O anti-malware da Microsoft é ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Database for SQL), no entanto não funciona com conteúdo do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Database for MySQL), no entanto não funciona com conteúdo do cliente.

Pré-digitalizar qualquer conteúdo que seja enviado para recursos Azure não computados, tais como App Service, Data Lake Storage, Blob Storage, Azure Database for MySQL, etc. A Microsoft não pode aceder aos seus dados nestes casos.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Database for MySQL), no entanto não funciona com conteúdo do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: N/A

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [Azure Security Benchmark: Data Recovery](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: A Azure Database for MySQL requer cópias de segurança dos ficheiros de dados e do registo de transações. Dependendo do tamanho máximo de armazenamento suportado, ou fazemos cópias de segurança completas e diferenciais (4 servidores de armazenamento máximo de TB) ou cópias de segurança instantâneas (até 16-TB máximo de armazenamento). Estas cópias de segurança permitem restaurar um servidor em qualquer ponto no tempo dentro do período de retenção de backup configurado. O período de retenção de backup predefinido é de sete dias. Pode configurar opcionalmente até 35 dias. Todas as cópias de segurança são encriptadas através da encriptação AES de 256 bits.

- [Compreenda as cópias de segurança da Base de Dados Azure para o MySQL](concepts-backup.md)

- [Compreenda a base de dados Azure para a configuração inicial do MySQL](tutorial-design-database-using-portal.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: A base de dados Azure para o MySQL cria automaticamente cópias de segurança do servidor e armazena-as num armazenamento localmente redundante ou geo-redundante, de acordo com a escolha do utilizador. As cópias de segurança podem ser utilizadas para restaurar o servidor para um ponto no tempo. A cópia de segurança e o restauro são uma parte essencial de qualquer estratégia de continuidade empresarial, uma vez que protegem os seus dados contra danos e a eliminação acidentais. 

Se utilizar o Cofre de Chaves Azure para armazenar credenciais para a sua Base de Dados Azure para instâncias MySQL, certifique-se de cópias de segurança automáticas regulares das suas chaves. 

- [Compreenda as cópias de segurança da Base de Dados Azure para o MySQL](howto-restore-server-portal.md) 

- [Como apoiar chaves do cofre](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Na Base de Dados Azure para o MySQL, executar uma restauração cria um novo servidor a partir das cópias de segurança do servidor original. Existem dois tipos de restauro disponíveis: restauro pontual e restauro georrefero. O restauro pontual está disponível com a opção de redundância de backup e cria um novo servidor na mesma região que o seu servidor original. O geo-restauro só está disponível se configurar o seu servidor para armazenamento geo-redundante e permitir-lhe restaurar o seu servidor numa região diferente.

O tempo estimado de recuperação depende de vários fatores, incluindo os tamanhos da base de dados, o tamanho do registo de transações, a largura de banda da rede e o número total de bases de dados que recuperam na mesma região ao mesmo tempo. O tempo de recuperação é geralmente inferior a 12 horas.

Teste periodicamente a restauração da sua Base de Dados Azure para instâncias MySQL.

- [Compreenda a cópia de segurança e a restauração na Base de Dados Azure para o MySQL](concepts-backup.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: A base de dados Azure para o MySQL requer cópias de segurança completas, diferenciais e de registo de transações. Estas cópias de segurança permitem restaurar um servidor em qualquer ponto no tempo dentro do período de retenção de backup configurado. O período de retenção de backup predefinido é de sete dias. Pode configurar opcionalmente até 35 dias. Todas as cópias de segurança são encriptadas através da encriptação AES de 256 bits. Certifique-se de que o cofre da chave é excluído suave.

- [Compreenda a cópia de segurança e a restauração na Base de Dados Azure para o MySQL](concepts-backup.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para mais informações, consulte o [Benchmark de Segurança Azure: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

- [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios de equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [Você pode encontrar mais informações sobre a estratégia da Microsoft e execução de Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridas pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)