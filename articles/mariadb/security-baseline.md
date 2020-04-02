---
title: Linha de Base de Segurança Azure para Base de Dados Azure para MariaDB
description: Linha de Base de Segurança Azure para Base de Dados Azure para MariaDB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ca3a79b0d31cc18b8ff77f02ce4ce3d517d7a638
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548687"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Linha de Base de Segurança Azure para Base de Dados Azure para MariaDB

A Base de Base de Segurança Azure para a Base de Dados Azure para MariaDB contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com as nossas melhores práticas de orientação.

Para mais informações, consulte a [visão geral da Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de Rede

*Para mais informações, consulte [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos utilizando grupos de segurança de rede ou firewall azure na sua Rede Virtual

**Orientação**: Configure link privado para base de dados Azure para MariaDB com pontos finais privados. O Private Link permite-lhe ligar-se a vários serviços PaaS em Azure através de um ponto final privado. O Azure Private Link essencialmente traz serviços Azure dentro da sua rede virtual privada (VNet). O tráfego entre a sua rede virtual e a instância MariaDB viaja na rede de espinha dorsal da Microsoft.

Em alternativa, poderá utilizar pontos finais de serviço de rede virtual para proteger e limitar o acesso à rede à sua Base de Dados Azure para implementações MariaDB. As regras de rede virtual são uma característica de segurança de firewall que controla se a sua Base de Dados Azure para MariaDB aceita comunicações que são enviadas de determinadas subredes em redes virtuais.

Também pode garantir a sua Base de Dados Azure para MariaDB com regras de firewall. A firewall do servidor impede todo o acesso ao seu servidor de base de dados até especificar quais os computadores que têm permissão. Para configurar a firewall, crie as regras de firewall que especificam intervalos de endereços IP aceitáveis. Pode criar regras de firewall ao nível do servidor.

Como configurar link privado para base de dados Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Como criar e gerir pontos finais de serviço VNet e regras VNet na Base de Dados Azure para servidor MariaDB:https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Como configurar a Base de Dados Azure para regras de firewall MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitore e registe a configuração e tráfego de Vnets, Subnets e NICs

**Orientação**: Quando a sua Base de Dados Azure para o servidor MariaDB estiver fixada num ponto final privado, pode implantar máquinas virtuais na mesma rede virtual. Pode utilizar um grupo de segurança de rede (NSG) para reduzir o risco de exfiltração de dados. Ative os registos de fluxo do NSG e envie registos numa Conta de Armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho de Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Como configurar link privado para base de dados Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Como ativar os registos https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal de fluxo nsg: como ativar e utilizar o Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Utilize proteção avançada contra ameaças para base de dados Azure para MariaDB. A Advanced Threat Protection deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados.

Ative a Norma de Proteção DDoS nas redes virtuais associadas à sua Base de Dados Azure para os casos de MariaDB para se proteger contra ataques DDoS. Utilize o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet maliciosos ou não utilizados conhecidos.

Como configurar a Proteção Avançada de Ameaças para a Base de Dados Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Como configurar a proteção DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Quando a sua Base de Dados Azure para o servidor MariaDB estiver fixada num ponto final privado, pode implantar máquinas virtuais na mesma rede virtual. Em seguida, pode configurar um grupo de segurança de rede (NSG) para reduzir o risco de exfiltração de dados. Ative os registos de fluxo do NSG e envie registos numa Conta de Armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho de Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Como ativar os registos https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal de fluxo nsg: como ativar e utilizar o Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Utilize proteção avançada contra ameaças para base de dados Azure para MariaDB. A Advanced Threat Protection deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados.
Como configurar a Proteção Avançada de Ameaças para a Base de Dados Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos que necessitem de acesso à sua Base de Dados Azure para instâncias MariaDB, utilize etiquetas de serviço de rede virtuais para definir controlos de acesso à rede em grupos de segurança de rede ou Firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Especificando o nome da etiqueta de serviço (por exemplo, SQL. WestUs) no campo de origem ou destino apropriado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.
Nota: A Base de Dados Azure para MariaDB utiliza a etiqueta de serviço "Microsoft.Sql".

Para mais informações sobre https://docs.microsoft.com/azure/virtual-network/service-tags-overview a utilização de etiquetas de serviço: Compreenda o uso da etiqueta de serviço para a Base de Dados Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para configurações de rede e recursos de rede associados à sua Base de Dados Azure para instâncias MariaDB com a Política Azure. Utilize pseudónimos da Política Azure nos espaços de nome "Microsoft.DBforMariaDB" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração da rede da sua Base de Dados Azure para instâncias MariaDB. Também pode utilizar definições políticas incorporadas relacionadas com o networking ou a sua Base de Dados Azure para instâncias MariaDB, tais como:

- Norma de proteção DDoS deve ser ativada

- Ponto final privado deve ser ativado para servidores MariaDB

- O servidor MariaDB deve usar um ponto final do serviço de rede virtual

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Amostras da Política Azure para networking:https://docs.microsoft.com/azure/governance/policy/samples/#network

Como criar um Projeto Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para recursos relacionados com a segurança da rede e fluxo de tráfego para os seus casos MariaDB fornecer metadados e organização lógica.

Utilize qualquer uma das definições políticas do Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com Tags e notificá-lo de recursos não marcados existentes.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou realizar ações em recursos baseados nas suas Tags.

Como criar e utilizar Tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividade seleções do Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com a sua Base de Dados Azure para instâncias MariaDB. Crie alertas dentro do Monitor Azure que irão desencadear quando ocorrerem alterações aos recursos críticos da rede.
Como visualizar e recuperar eventos https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view de Registo de Atividades do Azure: Como criar alertas no Monitor Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para mais informações, consulte [Controlo de Segurança: Registo e Monitorização](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilize fontes de sincronização do tempo aprovadas

**Orientação**: A Microsoft mantém a fonte de tempo utilizada para os recursos Azure, como a Base de Dados Azure para MariaDB para os carimbos temporais nos registos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central de registos de segurança

**Orientação**: Ative as Definições de Diagnóstico e os Registos do Servidor e ingere os registos para agregar os dados de segurança gerados pela sua Base de Dados Azure para instâncias MariaDB. Dentro do Monitor Azure, utilize log analytics workspace(s) para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo. Em alternativa, pode ativar e embarcar dados para o Azure Sentinel ou um SIEM de terceiros.
Como configurar e aceder aos registos do servidor para base de dados Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Como configurar e aceder a registos de auditoria https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal para a Base de Dados Azure para MariaDB: Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard



**Monitorização**do Azure Security Center : Não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditoria aos recursos do Azure

**Orientação**: Ativar as definições de diagnóstico na sua Base de Dados Azure para casos MariaDB para acesso a registos de auditoria, segurança e diagnóstico. Certifique-se de que ativa especificamente o registo de Auditoria MariaDB. Os registos de atividade, que estão automaticamente disponíveis, incluem fonte de evento, data, utilizador, carimbo de tempo, endereços de origem, endereços de destino e outros elementos úteis. Também pode ativar as Definições de Diagnóstico de Registo de Atividade do Azure e enviar os registos para o mesmo espaço de trabalho de Log Analytics ou Conta de Armazenamento.

Como configurar e aceder registos de servidores https://docs.microsoft.com/azure/mariadb/concepts-server-logs para base de dados Azure para MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal Como configurar e aceder a registos de auditoria para a Base de Dados Azure para MariaDB: Como configurar as Definições de Diagnóstico para o Registo de Atividade seleção do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy



**Monitorização**do Azure Security Center : Não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de registos de segurança

**Orientação**: Dentro do Monitor Azure, para o espaço de trabalho de Log Analytics que está a ser utilizado para manter a sua Base de Dados Azure para registos MariaDB, deteta o período de retenção de acordo com as regras de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.
Como definir parâmetros de retenção de https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period registos para espaços de trabalho de Log Analytics: Armazenar registos de recursos numa conta de armazenamento Azure:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitore e reveja registos

**Orientação**: Analise e monitorize os registos dos seus casos MariaDB por comportamento anómalo. Utilize o log analytics workspace do Azure Monitor para rever os registos e executar consultas nos dados de registo. Em alternativa, pode ativar e embarcar dados para o Azure Sentinel ou um SIEM de terceiros.

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Para mais informações sobre o espaço de trabalho de Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como realizar consultas personalizadas no Monitor Azure:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Ativar alertas para atividades anómalas

**Orientação**: Permitir proteção avançada de ameaças para o MariaDB. Advanced Threat Protection for Azure Database for MariaDB deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados.

Além disso, pode ativar registos de servidores e definições de diagnóstico para o MariaDB e enviar registos para um espaço de trabalho de Log Analytics. A bordo do log analytics workspace para o Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar e utilizar os livros de jogadas (soluções automatizadas) para remediar problemas de segurança.

Como ativar a Proteção Avançada de Ameaças para o MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Como configurar e aceder aos Registos do Servidor para O MaD:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Como configurar e aceder a registos de auditoria para mariadb:https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

**Orientação**: N/A; A MariaDB não processa nem produz registos relacionados com anti-malware.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a exploração de consultas dNS

**Orientação**: N/A; A MariaDB não processa nem produz registos relacionados com DNS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar a exploração de auditoria da linha de comando

**Orientação**: N/A; o benchmark destina-se aos recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Controlo de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Manter um inventário das contas de utilizador que tenham acesso administrativo ao plano de gestão (portal Azure/Gestor de Recursos Azure) das suas instâncias MariaDB. Além disso, mantenha um inventário das contas administrativas que tenham acesso ao plano de dados dos seus casos MariaDB. (Ao criar o servidor MariaDB, fornece credenciais para um utilizador administrador. Este administrador pode ser usado para criar utilizadores mariadb adicionais.)

Compreender a gestão de acessos para a MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-security#access-management

Compreender as funções rBAC incorporadas para assinaturas Azure:https://docs.microsoft.com/azure/role-based-access-control/built-in-roles


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar as palavras-passe por defeito sempre que aplicável

**Orientação**: O Diretório Ativo do Azure não tem o conceito de senhas padrão.

Após a criação do próprio recurso MariaDB, o Azure obriga à criação de um utilizador administrativo com uma senha forte. No entanto, uma vez criada a instância MariaDB, poderá utilizar a primeira conta de administração do servidor que criou para criar utilizadores adicionais e conceder acesso administrativo aos mesmos. Ao criar estas contas, certifique-se de configurar uma senha diferente e forte para cada conta.

Como criar contas adicionais para o MariaDB:https://docs.microsoft.com/azure/mariadb/howto-create-users


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Crie procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas que tenham acesso aos seus casos MariaDB. Utilize a identidade do Azure Security Center Identidade e gestão de acesso para monitorizar o número de contas administrativas.

Compreender identidade e acesso do Centro de Segurança Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal (SSO) com diretório ativo Azure

**Orientação**: O acesso do avião de dados ao MariaDB é controlado por identidades armazenadas na base de dados e não suporta SSO. O acesso do avião de controlo para o MariaDB está disponível via REST API e suporta o SSO. Para autenticar, detete tede o cabeçalho de Autorização para os seus pedidos a um Token Web JSON que obtenha do Diretório Ativo Azure.

Compreender a Base de Dados Azure para MariaDB REST API:https://docs.microsoft.com/rest/api/mariadb/

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação multifactor para todos os acessos baseados em Diretório Ativo Azure

**Orientação**: Habilitar o Azure AD MFA e seguir as recomendações de Identidade e Gestão de Acesso do Azure Security Center.

Como permitir o MFA em Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorizar a identidade e o acesso dentro do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Postos de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho de acesso privilegiados) com MFA configurado para iniciar sessão e configurar os recursos Azure.

Saiba mais sobre postos de trabalho de acesso privilegiados:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como permitir o MFA em Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Permitir que a Proteção Avançada de Ameaças para o MariaDB gere alertas para atividades suspeitas.

Além disso, pode utilizar a Azure AD Privileged Identity Management (PIM) para a geração de registos e alertas quando ocorrer atividade suspeita ou insegura no ambiente. Utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

Como configurar proteção avançada de ameaças para o MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Como implementar a Gestão de Identidade Privilegiada (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Compreender as deteções de risco da AD Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados

**Orientação**: Utilizar locais de acesso condicional nomeados para permitir o acesso a partir de apenas agrupamentos lógicos específicos de gamas de endereços IP ou países/regiões para limitar o acesso aos recursos Azure, como o MariaDB.

Como configurar localizações nomeadas em Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Diretório Ativo Azure (AAD) como sistema central de autenticação e autorização. A AAD protege os dados utilizando encriptação forte para dados em repouso e em trânsito. A AAD também sais, hashes e armazena seguramente credenciais de utilizador.

A autenticação Azure AD não pode ser utilizada para o acesso direto ao plano de dados MariaDB, no entanto, as credenciais da AD Azure podem ser utilizadas para administração a nível de planode gestão (por exemplo, o portal Azure) para controlar as contas de administração da MariaDB.

Como atualizar a palavra-passe de administrador para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever regularmente e conciliar o acesso ao utilizador

**Orientação**: Reveja os registos do Diretório Ativo Azure para ajudar a descobrir contas velhas que podem incluir as que têm funções administrativas MariaDB. Além disso, utilize as Análises de Acesso à Identidade do Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais que possam ser usadas para aceder ao MariaDB e atribuições de papéis. O acesso ao utilizador deve ser revisto regularmente, como a cada 90 dias, para garantir que apenas os Utilizadores certos tenham acesso continuado.

Compreender relatórios da AD Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como utilizar comentários de acesso à identidade do Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização tenta aceder a contas desativadas

**Orientação**: Ative Definições de Diagnóstico para O Diretório Ativo MariaDB e Azure, enviando todos os registos para um espaço de trabalho de Log Analytics. Configure os Alertas desejados (tais como tentativas de autenticação falhadas) dentro do espaço de trabalho de Log Analytics.

Como configurar e aceder aos Registos do Servidor para O MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Como configurar e aceder a registos de auditoria para mariadb:https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Como integrar registos de atividade do Azure no Monitor Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorização**do Azure Security Center : Não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Permitir proteção avançada de ameaças para o MariaDB. Advanced Threat Protection for Azure Database for MariaDB deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados.

Utilize as funcionalidades de Proteção de Identidade e Deteção de Risco do Azure Ative Directory para configurar respostas automatizadas a ações suspeitas detetadas. Pode permitir respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização.

Como ativar a Proteção Avançada de Ameaças para o MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Como configurar e ativar políticas de risco de proteção de identidade:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como ver os sign-ins de risco da AD Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorização**do Azure Security Center : Não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante os cenários de suporte

**Orientação**: Não aplicável; O Customer Lockbox ainda não foi suportado para a Base de Dados Azure para a MariaDB.

Lista de serviços suportados pelo Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de Dados

*Para mais informações, consulte [Controlo de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear a Base de Dados Azure para instâncias MariaDB ou recursos conexos que armazenem ou processem informações sensíveis.

Como criar e utilizar Tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou processamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Utilize uma combinação de regras de Link Privado, Pontos Finais de Serviço e/ou regras de firewall MariaDB para isolar e limitar o acesso à rede às instâncias do MariaDB.

Como criar subscrições adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Como configurar link privado para base de dados Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Como configurar os pontos finais de serviço para a Base de Dados Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Como configurar as regras de firewall para a Base de Dados Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Monitorização**do Azure Security Center : Não disponível

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorizar e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Ao utilizar VMs Azure para aceder a instâncias MariaDB, utilizar o Private Link, as configurações da rede MariaDB, os Grupos de Segurança da Rede e as Etiquetas de Serviço para mitigar a possibilidade de exfiltração de dados.

A Microsoft gere a infraestrutura subjacente ao MariaDB e implementou controlos rigorosos para evitar a perda ou exposição dos dados dos clientes.

Como mitigar a exfiltração de dados para a Base de Dados Azure para o MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Compreender a proteção de dados dos clientes em Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Base de dados Azure para O MariaDB suporta a ligação da sua Base de Dados Azure para o servidor MariaDB às aplicações do cliente utilizando a Camada de Tomadas Seguras (SSL). A imposição de ligações SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação. No portal Azure, certifique-se de que a "Enforce sSL connection" está ativada para todos os seus casos MariaDB.

Como configurar a encriptação em trânsito para o MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-ssl

**Monitorização**do Azure Security Center : Não disponível

**Responsabilidade**: Partilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para a Base de Dados Azure para o MariaDB. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados dos clientes em Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização**do Azure Security Center : Não disponível

**Responsabilidade**: Partilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o Azure AD RBAC para controlar o acesso à Base de Dados Azure para o plano de gestão MariaDB (portal Azure/Gestor de Recursos Azure). Para acesso a plano de dados (dentro da própria base de dados), utilize consultas SQL para criar utilizadores e configurar permissões dos utilizadores.

Como configurar o RBAC em Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Como configurar o acesso do utilizador com SQL para MariaDB:https://docs.microsoft.com/azure/mariadb/howto-create-users

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: N/A; esta orientação destina-se a recursos de computação.

A Microsoft gere a infraestrutura subjacente ao MariaDB e implementou controlos rigorosos para evitar a perda ou exposição dos dados dos clientes.

Compreender a proteção de dados dos clientes em Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

**Orientação**: A Base de Dados Azure para o serviço MariaDB utiliza o módulo criptográfico validado FIPS 140-2 para encriptação de armazenamento de dados em repouso. Os dados, incluindo cópias de segurança, são encriptados no disco, com exceção dos ficheiros temporários criados durante a execução de consultas. O serviço utiliza a cifra AES de 256 bits incluída na encriptação de armazenamento Azure, e as chaves são geridas pelo sistema. A encriptação de armazenamento está sempre ativada e não pode ser desativada.

Compreenda a encriptação em repouso para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-security

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades do Azure para criar alertas para quando ocorrerem alterações nas instâncias de produção da Base de Dados Azure para MariaDB e outros recursos críticos ou relacionados.

Como criar alertas para eventos de Registo de Atividade seleções do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas de digitalização automática de vulnerabilidades

**Orientação**: Atualmente não disponível; O Azure Security Center ainda não suporta a avaliação de vulnerabilidade para a Base de Dados Azure para o servidor MariaDB.


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de patch do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patch de software de terceiros

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar as varreduras de vulnerabilidade consecutivas

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: A Microsoft realiza a gestão de vulnerabilidades nos sistemas subjacentes que suportam a Base de Dados Azure para o servidor MariaDB.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Utilizar a Descoberta de Ativos Azure

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar e descobrir todos os recursos (incluindo a Base de Dados Azure para o servidor MariaDB) dentro da sua subscrição(s). Certifique-se de que tem permissões adequadas (ler) no seu inquilino e que é capaz de enumerar todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

Como criar consultas com o Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como visualizar as suas Assinaturas Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Compreender o Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas na Base de Dados Azure para o servidor MariaDB e outros recursos relacionados que dêem metadados para logicamente organizá-los numa taxonomia.

Como criar e utilizar etiquetas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos Azure não autorizados

**Orientação**: Utilize a marcação, os grupos de gestão e as subscrições separadas, se for caso disso, para organizar e rastrear a Base de Dados Azure para servidor MariaDB e recursos conexos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição atempadamente.

Como criar subscrições adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizar etiquetas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos títulos aprovados de recursos e software do Azure

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação e ao Azure como um todo.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para os recursos Azure não aprovados

**Orientação**: Utilize a política azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da(s subscrição)."

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos do cálculo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos não aprovados do Azure e aplicações de software

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação e ao Azure como um todo.



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Utilize apenas pedidos aprovados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas os serviços Azure aprovados

**Orientação**: Utilize a política azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo específico de recurso com a Política Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-divlimit-users-ability-to-interact-with-azure-resources-manager-via-scriptsdiv"></a>6.11: <div>Limite a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure através de scripts</div>

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure configurando o "Block access" para a App "Microsoft Azure Management". Isto pode impedir a criação e alterações de recursos num ambiente de alta segurança, tal Base de Dados Azure para servidor MariaDB contendo informações sensíveis.

Como configurar o Acesso Condicional ao acesso ao portal Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacionais

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar fisicamente ou logicamente aplicações de alto risco

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração Segura

*Para mais informações, consulte [Controlo de Segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: Defina e implemente configurações de segurança padrão para a sua Base de Dados Azure para instâncias MariaDB com Política Azure. Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.DBforMariaDB" para criar políticas personalizadas para auditar ou impor a configuração de rede da sua Base de Dados Azure para servidores MariaDB. Também pode utilizar definições de políticas incorporadas relacionadas com a sua Base de Dados Azure para servidores MariaDB, tais como:

- Backup geo-redundante deve ser ativado para base de dados Azure para MariaDB

Como visualizar os aliases de política do Azure disponíveis:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação:** Utilize a política azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Compreender efeitos políticos do Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração segura dos recursos Do Azure

**Orientação**: Se utilizar definições de política personalizadas do Azure para a sua Base de Dados Azure para servidores MariaDB e recursos conexos, utilize o Azure Repos para armazenar e gerir de forma segura o seu código.

Como armazenar código em Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação Azure Repos:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.DBforMariaDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolver um processo e um oleoduto para gerir as exceções políticas.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para os serviços Do Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.DBforMariaDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Utilize a política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente as configurações para a sua Base de Dados Azure para instâncias MariaDB e recursos conexos.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Para máquinas virtuais Azure ou aplicações web em execução no Serviço de Aplicações Azure que estão a ser utilizados para aceder à sua Base de Dados Azure para servidores MariaDB, utilize a Identidade de Serviço Gerida em conjunto com o Azure Key Vault para simplificar e proteger a Base de Dados Azure para a gestão secreta do servidor MariaDB. Certifique-se de que o Soft Delete do cofre de chaves está ativado.

Como integrar-se com identidades geridas pelo Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre chave:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer a autenticação do Cofre Chave com uma identidade gerida:https://docs.microsoft.com/azure/key-vault/managed-identity 



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: A base de dados Azure para o servidor MariaDB não suporta atualmente a autenticação do Diretório Ativo Azure para aceder a bases de dados.  Ao criar a Base de Dados Azure para o servidor MariaDB, fornece credenciais para um utilizador administrador. Este administrador pode ser utilizado para criar utilizadores mariadb adicionais.  

Para máquinas virtuais Azure ou aplicações web em execução no Serviço de Aplicações Azure que estão a ser utilizados para aceder à sua Base de Dados Azure para servidor MariaDB, utilize a Identidade de Serviço Gerida em conjunto com o Azure Key Vault para armazenar e recuperar credenciais para o Azure Database para o servidor MariaDB.  Certifique-se de que o Soft Delete do cofre de chaves está ativado.

Utilize identidades geridas para fornecer aos serviços Azure uma identidade gerida automaticamente no Diretório Ativo Azure (AD). Identidades Geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AD Azure, incluindo o Key Vault, sem qualquer credencial no seu código. Como configurar identidades geridas: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm. Como integrar-se com identidades https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identitygeridas azure: .



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a mudança de credenciais descobertas para locais mais seguros, como o Cofre chave Azure. 

Como configurar o Scanner Credencial:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

O anti-malware da Microsoft está ativado no hospedeiro subjacente que suporta os serviços Do Azure (por exemplo, o Azure App Service), no entanto não funciona com conteúdo de clientes.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem carregados para recursos não computacionais do Azure

**Orientação**: O anti-malware da Microsoft está ativado no hospedeiro subjacente que suporta os serviços Do Azure (por exemplo, Base de Dados Azure para servidor MariaDB), no entanto não funciona com conteúdo do cliente.

Pré-digitalização de qualquer conteúdo que seja enviado para recursos não computacionais do Azure, tais como Serviço de Aplicações, Armazenamento de Data Lake, Armazenamento Blob, Base de Dados Azure para servidor MariaDB, etc. A Microsoft não pode aceder aos seus dados nestes casos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que software anti-malware e assinaturas são atualizados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Do Azure (por exemplo, Base de Dados Azure para servidor MariaDB), no entanto não funciona com conteúdo de clientes.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de Dados

*Para mais informações, consulte [Controlo de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automáticos regulares

**Orientação**: Base de dados Azure para MariaDB requer cópias de segurança completas, diferenciais e de registo de transações.  A Base de Dados Azure para MariaDB cria automaticamente cópias de segurança do servidor e armazena-as em armazenamento localmente redundante ou geo-redundante. As cópias de segurança podem ser utilizadas para restaurar o servidor para um ponto no tempo. Backup e restauro são uma parte essencial de qualquer estratégia de continuidade do negócio porque protegem os seus dados de corrupção acidental ou eliminação.  O período de retenção de reserva padrão é de sete dias. Pode configurá-lo opcionalmente até 35 dias. Todas as cópias de segurança são encriptadas utilizando encriptação AES de 256 bits.

Compreenda os backups para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup

Compreender a configuração inicial do MariaDB:https://docs.microsoft.com/azure/mariadb/tutorial-design-database-using-portal



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Partilhado

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e fazer cópias de segurança de quaisquer chaves geridas pelo cliente

**Orientação**: Base de dados Azure para MariaDB cria automaticamente cópias de segurança do servidor e armazena-as em armazenamento localmente redundante ou geo-redundante. As cópias de segurança podem ser utilizadas para restaurar o servidor para um ponto no tempo.  Backup e restauro são uma parte essencial de qualquer estratégia de continuidade do negócio porque protegem os seus dados de corrupção acidental ou eliminação.

Se utilizar o Key Vault para encriptação de dados do lado do cliente para os dados armazenados no seu servidor MariaDB, certifique-se de cópias de segurança automáticas regulares das suas chaves.

Compreenda os backups para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup

Como fazer backup das chaves do cofre de chaves:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Partilhado

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Na Base de Dados Azure para MariaDB, efetue um restauro das cópias de segurança do servidor original para testes periódicos de backups. Existem dois tipos de restauro disponíveis: restauro pontual e geo-restauro. O restauro pontual está disponível com a opção de redundância de reserva e cria um novo servidor na mesma região que o seu servidor original. A Geo-restore só está disponível se configurar o seu servidor para armazenamento geo-redundante e permite-lhe restaurar o seu servidor para uma região diferente.

O tempo estimado de recuperação depende de vários fatores, incluindo o tamanho da base de dados, o tamanho do registo de transações, a largura de banda da rede e o número total de bases de dados que se recuperam na mesma região ao mesmo tempo. O tempo de recuperação é geralmente inferior a 12 horas.

Compreenda a cópia de segurança e restaure na Base de Dados Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup#restore


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Base de dados Azure para MariaDB requer cópias de segurança completas, diferenciais e de registo de transações. Estas cópias de segurança permitem restaurar um servidor em qualquer ponto-a-tempo dentro do período de retenção de cópia de segurança configurado. O período de retenção de reserva padrão é de sete dias. Pode configurá-lo opcionalmente até 35 dias. Todas as cópias de segurança são encriptadas utilizando encriptação AES de 256 bits.

Compreenda a cópia de segurança e restaure na Base de Dados Azure para MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para mais informações, consulte [Controlo de Segurança: Resposta](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)a Incidentes .*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Eleve um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes desde a deteção até à revisão pós-incidente.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final 



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança do Centro de Segurança na descoberta ou na analítica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

Utilize etiquetas para organizar os seus recursos Azure:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.
    

    How to set the Azure Security Center Security Contact: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exportar os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade De Exportação Contínua para ajudar a identificar riscos para os recursos do Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança Azure para transmitir os alertas para o Azure Sentinel.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a funcionalidade workflow Automation no Azure Security Center para ativar automaticamente respostas através de "Aplicações Lógicas" em alertas de segurança e recomendações para proteger os seus recursos Azure.
    

Como configurar a Automatização do Fluxo de Trabalho e aplicações lógicas:https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para mais informações, consulte [Controlo de Segurança: Testes de Penetração e Exercícios de Equipa Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias

**Orientação**: Siga as Regras de Envolvimento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Pode encontrar mais informações sobre a estratégia e execução da Microsoft de Red Teaming e testes de penetração no site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft, aqui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

## <a name="next-steps"></a>Passos seguintes

- Ver o [Benchmark de Segurança Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
