---
title: Linha de base de segurança Azure para conjuntos de escala de máquina virtual
description: A linha de base de segurança Virtual Machine Scale Define fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2d902bbdc03596fe246fc36813895e72c53da05a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571398"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>Linha de base de segurança Azure para conjuntos de escala de máquina virtual

A Linha de Base de Segurança Azure para conjuntos de escalas de máquinas virtuais contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../security/benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte [a visão geral da Azure Security Baselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte [o controlo de segurança: segurança da rede.](../security/benchmarks/security-control-network-security.md)*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Quando criar uma máquina virtual Azure (VM), deve criar uma rede virtual ou utilizar uma rede virtual existente e configurar o VM com uma sub-rede. Certifique-se de que todas as sub-redes implementadas têm um Grupo de Segurança de Rede aplicado com controlos de acesso à rede específicos das suas aplicações portas e fontes fidedignas.

Em alternativa, se tiver uma caixa de utilização específica para uma firewall centralizada, o Azure Firewall também pode ser usado para satisfazer esses requisitos.

* [Trabalhar em rede em conjuntos de dimensionamento de máquinas virtuais do Azure](./virtual-machine-scale-sets-networking.md)

* [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

* [Como implantar e configurar firewall Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Utilize o Centro de Segurança Azure para identificar e seguir recomendações de proteção da rede para ajudar a proteger os recursos da sua Máquina Virtual Azure (VM) em Azure. Ativar os registos de fluxo NSG e enviar registos para uma conta de armazenamento para auditoria de tráfego para os VMs para atividades incomuns.

* [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Se utilizar o seu Conjunto de Balanças de Máquina Virtual (VMSS) para hospedar aplicações web, utilize um grupo de segurança de rede (NSG) na sub-rede VMSS para limitar o tráfego de rede, portas e protocolos que podem comunicar. Siga uma abordagem de rede menos privilegiada ao configurar os seus NSGs para permitir apenas o tráfego necessário à sua aplicação.

Também pode implementar o Azure Web Application Firewall (WAF) em frente a aplicações web críticas para inspeção adicional do tráfego de entrada. Ativar a Definição de Diagnóstico para WAF e ingerir registos numa conta de armazenamento, no Centro de Eventos ou no Espaço de Trabalho do Log Analytics.

* [Trabalhar em rede em conjuntos de dimensionamento de máquinas virtuais do Azure](./virtual-machine-scale-sets-networking.md)

* [Criar um gateway de aplicações com uma Firewall de Aplicação Web utilizando o portal Azure](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Permitir a negação de serviço distribuída (DDoS) Proteção padrão nas Redes Virtuais para proteger contra ataques DDoS. Utilizando a Azure Security Center Integrated Threat Intelligence, pode monitorizar comunicações com endereços IP maliciosos conhecidos. Configurar o Azure Firewall em cada um dos seus segmentos de Rede Virtual, com a Threat Intelligence ativada e configurada para "alertar e negar" para tráfego de rede malicioso.

Pode utilizar o acesso da Rede Just In Time do Azure Security Center para limitar a exposição das Máquinas Virtuais do Windows aos endereços IP aprovados por um período limitado. Além disso, utilize o Hardening de Rede Adaptável do Centro de Segurança Azure para recomendar configurações NSG que limitem portas e IPs de origem com base na inteligência real de tráfego e ameaça.

* [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)

* [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](../security-center/azure-defender.md)

* [Compreender o Hardenive de Rede Adaptável do Centro de Segurança Azure](../security-center/security-center-adaptive-network-hardening.md)

* [Compreender o Centro de Segurança Azure Mesmo no Tempo Controlo de Acesso à Rede](../security-center/security-center-just-in-time.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Pode gravar registos de fluxo NSG numa conta de armazenamento para gerar registos de fluxo para as suas Máquinas Virtuais Azure. Ao investigar uma atividade anómala, pode ativar a captura de pacotes do Network Watcher para que o tráfego de rede possa ser revisto para uma atividade invulgar e inesperada.

* [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Como ativar o Observador de Redes](../network-watcher/network-watcher-create.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Ao combinar capturas de pacotes fornecidas pelo Network Watcher e uma ferramenta IDS de código aberto, pode efetuar a deteção de intrusões de rede para uma ampla gama de ameaças. Além disso, pode implementar o Azure Firewall nos segmentos de Rede Virtual conforme apropriado, com a Threat Intelligence ativada e configurada para "alertar e negar" para tráfego de rede malicioso.

* [Realizar deteção de intrusão de rede com o Network Watcher e ferramentas de código aberto](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Como configurar alertas com a Azure Firewall](../firewall/threat-intel.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Se utilizar o Conjunto de Balanças de Máquina Virtual (VMSS) para hospedar aplicações web, poderá implementar o Gateway de Aplicações Azure para aplicações web com HTTPS/SSL habilitado para certificados fidedignos. Com o Azure Application Gateway, direciona o tráfego web da sua aplicação para recursos específicos, atribuindo os ouvintes às portas, criando regras e adicionando recursos a um pool de backend como VMSS, etc.

* [Como implementar o Gateway de Aplicações](../application-gateway/quick-create-portal.md)

* [Como configurar o Gateway de aplicações para utilizar HTTPS](../application-gateway/create-ssl-portal.md)

* [Criar um conjunto de dimensionamento que referencie um Gateway de Aplicação](./virtual-machine-scale-sets-networking.md#create-a-scale-set-that-references-an-application-gateway)

* [Compreenda o equilíbrio de carga da camada 7 com os gateways de aplicações web Azure](../application-gateway/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize tags de serviço de rede virtuais para definir controlos de acesso à rede em Grupos de Segurança de Rede ou firewall Azure configurados para as suas máquinas Virtuais Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

* [Compreender e utilizar tags de serviço](../virtual-network/service-tags-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para conjuntos de balanças de máquinas virtuais Azure utilizando a política do Azure. Também pode utilizar plantas Azure para simplificar as implementações em VM em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de gestor de recursos Azure, atribuições de funções e atribuições de Política Azure, numa única definição de planta. Pode aplicar o plano às subscrições e ativar a gestão de recursos através da versão blueprint.

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Saiba mais sobre modelos de conjunto de escala de máquina virtual](./virtual-machine-scale-sets-mvss-start.md)

* [Amostras da Política Azure para networking](../governance/policy/samples/built-in-policies.md#network)

* [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Pode utilizar etiquetas para grupos de segurança de rede (NSG) e outros recursos relacionados com a segurança da rede e o fluxo de tráfego configurados para as suas máquinas Virtual Windows. Para regras individuais de NSG, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio para quaisquer regras que permitam o tráfego de/para uma rede.

* [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

* [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividade Azure para monitorizar as alterações às configurações de recursos de rede relacionadas com o conjunto de balanças de máquinas virtuais Azure. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nas definições ou recursos críticos da rede.

Utilize a Política Azure para validar (e/ou remediar) configurações para recursos de rede relacionados com o Conjunto de Escala de Máquina Virtual.

* [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Amostras da Política Azure para networking](../governance/policy/samples/built-in-policies.md#network)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte [o controlo de segurança: Registar e monitorizar](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para recursos Azure, no entanto, tem a opção de gerir as definições de sincronização de tempo para as suas Máquinas Virtuais.

* [Como configurar a sincronização do tempo para os recursos computativos do Azure Windows](../virtual-machines/windows/time-sync.md)

* [Como configurar a sincronização temporal para os recursos computativos Azure Linux](../virtual-machines/linux/time-sync.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Os registos de atividade podem ser utilizados para auditar operações e ações realizadas em recursos de conjunto de escala de máquina virtual. O registo de atividade contém todas as operações de escrita (PUT, POST, DELETE) para os seus recursos, exceto operações de leitura (GET). Os registos de atividade podem ser utilizados para encontrar um erro na resolução de problemas ou para monitorizar como um utilizador na sua organização modificou um recurso.

Pode ativar e ativar dados de registo a bordo produzidos a partir de Registos de Atividades Azure ou recursos de Máquina Virtual para Azure Sentinel ou um SIEM de terceiros para gestão de registos de segurança central.

Utilize o Centro de Segurança Azure para fornecer monitorização de registo de eventos de segurança para máquinas virtuais Azure. Dado o volume de dados que o registo do evento de segurança gera, este não é armazenado por padrão.

Se a sua organização quiser reter os dados de registo de eventos de segurança da máquina virtual, pode ser armazenado num espaço de trabalho de Log Analytics no nível de recolha de dados pretendido configurado no Azure Security Center.

* [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

* [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

* [Data collection in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier) (Recolha de dados no Centro de Segurança do Azure)

* [Como monitorizar máquinas virtuais em Azure](../azure-monitor/vm/monitor-vm-azure.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Os registos de atividade podem ser utilizados para auditar operações e ações realizadas em recursos de conjunto de escala de máquina virtual. O registo de atividade contém todas as operações de escrita (PUT, POST, DELETE) para os seus recursos, exceto operações de leitura (GET). Os registos de atividade podem ser utilizados para encontrar um erro na resolução de problemas ou para monitorizar como um utilizador na sua organização modificou um recurso.

Ativar a recolha de dados de diagnóstico de SO de hóspedes implantando a extensão de diagnóstico nas suas Máquinas Virtuais (VM). Pode utilizar a extensão de diagnóstico para recolher dados de diagnóstico, como registos de aplicações ou contadores de desempenho de uma máquina virtual Azure.

Para uma visibilidade avançada das aplicações e serviços suportados pelo Conjunto de Escala de Máquina Virtual Azure, pode ativar tanto o Monitor Azure para informações sobre VMs como para aplicações. Com o Application Insights, pode monitorizar a sua aplicação e capturar telemetria, como pedidos HTTP, exceções, etc. para que possa correlacionar problemas entre os VMs e a sua aplicação.

* [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

* [Ver e recuperar eventos de log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Como monitorizar máquinas virtuais em Azure](../azure-monitor/vm/monitor-vm-azure.md)

* [Descrição geral do Application Insights](../azure-monitor/app/app-insights-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Utilize o Centro de Segurança Azure para fornecer monitorização de registo de eventos de segurança para máquinas virtuais Azure. Dado o volume de dados que o registo do evento de segurança gera, este não é armazenado por padrão.

Se a sua organização quiser reter os dados de registo de eventos de segurança da máquina virtual, pode ser armazenado num espaço de trabalho de Log Analytics no nível de recolha de dados pretendido configurado no Azure Security Center.

* [Data collection in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier) (Recolha de dados no Centro de Segurança do Azure)

* [Como monitorizar máquinas virtuais em Azure](../azure-monitor/vm/monitor-vm-azure.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Certifique-se de que quaisquer contas de armazenamento ou espaços de trabalho do Log Analytics utilizados para armazenar registos de máquinas virtuais tem o período de retenção de registo definido de acordo com as normas de conformidade da sua organização.

* [Como monitorizar máquinas virtuais em Azure](../azure-monitor/vm/monitor-vm-azure.md)

* [Como configurar o período de retenção do espaço de trabalho do Log Analytics](../azure-monitor/logs/manage-cost-storage.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Analise e monitorize registos para comportamento anómalo e reveja regularmente os resultados. Utilize o Azure Monitor para rever registos e efetuar consultas nos dados de registo.

Em alternativa, pode ativar e a bordo dados do Azure Sentinel ou de um SIEM de terceiros para monitorizar e rever os seus registos.

* [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Compreender log analytics workspace](../azure-monitor/logs/log-analytics-tutorial.md)

* [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o Azure Security Center configurado com um espaço de trabalho Log Analytics para monitorizar e alertar sobre a atividade anómala encontrada em registos de segurança e eventos para as suas Máquinas Virtuais Azure.

Em alternativa, pode permitir e a bordo dados de Azure Sentinel ou um SIEM de terceiros para configurar alertas para a atividade anómala.

* [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Como gerir alertas no Centro de Segurança Azure](../security-center/security-center-managing-and-responding-alerts.md)

* [Como alertar nos dados de registo de registo de registos de registos](../azure-monitor/alerts/tutorial-response.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Pode utilizar o Microsoft Anti-malware para serviços em nuvem azure e máquinas virtuais e configurar as suas máquinas Virtual Windows para registar eventos numa conta de armazenamento Azure. Configure um espaço de trabalho Log Analytics para ingerir os eventos a partir das Contas de Armazenamento e criar alertas sempre que apropriado. Siga as recomendações no Azure Security Center: "Compute &amp; Apps". Para máquinas Virtual Linux, necessitará de uma ferramenta de terceiros para deteção de vulnerabilidade anti-malware.

* [Como configurar o Anti-malware da Microsoft para serviços na Nuvem e Máquinas Virtuais](../security/fundamentals/antimalware.md)

* [Como permitir a monitorização ao nível dos hóspedes para máquinas virtuais](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

* [Instruções para embarcar servidores Linux para o Centro de Segurança Azure](../security-center/quickstart-onboard-machines.md)

* [O link seguinte fornece as diretrizes de segurança recomendadas pela Microsoft, que podem servir como uma lista de critérios para o software de vulnerabilidade selecionado](../virtual-machines/security-recommendations.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Implementar uma solução de terceiros do Azure Marketplace para a solução de registo de DNS conforme as suas organizações precisam.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: O Centro de Segurança Azure fornece monitorização de registo de eventos de segurança para máquinas virtuais Azure (VM). O Security Center fornece o Agente de Monitorização da Microsoft em todos os VMs Azure suportados e quaisquer novos que sejam criados se o fornecimento automático estiver ativado OU pode instalar o agente manualmente. O agente permite o evento de criação de processo 4688 e o campo CommandLine dentro do evento 4688. Os novos processos criados no VM são registados pelo EventLog e monitorizados pelos serviços de deteção do Security Center.

Para máquinas Virtual Linux, pode configurar manualmente o registo de consolas numa base por nó e utilizar syslogs para armazenar os dados. Além disso, utilize o espaço de trabalho do Log Analytics do Azure Monitor para rever registos e efetuar consultas sobre dados syslog de máquinas Azure Virtual.

* [Data collection in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier) (Recolha de dados no Centro de Segurança do Azure)

* [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

* [Syslog data sources in Azure Monitor](../azure-monitor/agents/data-sources-syslog.md) (Origens de dados de Syslog no Azure Monitor)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para obter mais informações, consulte [controlo de segurança: Identidade e controlo de acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Embora o Azure Ative Directory seja o método recomendado para administrar o acesso do utilizador, as Máquinas Virtuais Azure podem ter contas locais. As contas locais e de domínio devem ser revistas e geridas, normalmente com uma pegada mínima. Além disso, alavancar a Azure Privileged Identity Management para contas administrativas utilizadas para aceder aos recursos das máquinas virtuais.

* [Informações para Contas Locais estão disponíveis em](../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

* [Informação sobre Gestor de Identidade Privilegiada](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: Conjunto de escala de máquina virtual Azure e Diretório Ativo Azure não têm o conceito de senhas padrão. Cliente responsável por aplicações de terceiros e serviços de marketplace que podem usar senhas padrão.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Crie procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas que tenham acesso às suas máquinas virtuais. Utilize a identidade do Azure Security Center e a gestão de acessos para monitorizar o número de contas administrativas. Quaisquer contas de administrador utilizadas para aceder aos recursos da Azure Virtual Machine também podem ser geridas pela Azure Privileged Identity Management (PIM). A Azure Privileged Identity Management fornece várias opções, como a elevação just in Time, exigindo a autenticação multi-factor antes de assumir um papel, e opções de delegação para que as permissões só estejam disponíveis para prazos específicos e exijam um aprovador.

* [Compreender a identidade e o acesso do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

* [Informação sobre Gestor de Identidade Privilegiada](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: Sempre que possível, utilize SSO com diretório Azure Ative em vez de configurar credenciais individuais autónomas por serviço. Utilize recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure.

* [Inscrição única para aplicações no Azure Ative Directory](../active-directory/manage-apps/what-is-single-sign-on.md)

* [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Ativar O Azure AD MFA e seguir as recomendações do Azure Security Center Identity and Access Management.

* [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Utilizar estações de trabalho seguras e geridas pelo Azure para tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho privilegiadas de acesso) com MFA configurados para iniciar sessão e configurar recursos Azure.

* [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize a Azure AD Gestão de Identidade Privilegiada (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco. Opcionalmente, o cliente pode ingerir alertas de deteção de risco do Centro de Segurança Azure no Azure Monitor e configurar alertas/notificações personalizadas usando Grupos de Ação.

* [Como implementar gestão de identidade privilegiada (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Compreender deteções de risco do Centro de Segurança Azure (atividade suspeita)](../active-directory/identity-protection/overview-identity-protection.md)

* [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Como configurar grupos de ação para alerta e notificação personalizados](../azure-monitor/alerts/action-groups.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize políticas de acesso condicional do Diretório Ativo Azure e locais nomeados para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

* [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores. Pode utilizar identidades geridas para autenticar qualquer serviço que suporte a autenticação AZure AD, incluindo o Key Vault, sem quaisquer credenciais no seu código. O seu código que está a funcionar numa máquina virtual, pode usar a sua identidade gerida para solicitar o acesso a fichas de acesso para serviços que suportem a autenticação Azure AD.

* [Como criar e configurar instâncias do Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

* [Identidades geridas para visão geral dos recursos da Azure](../active-directory/managed-identities-azure-resources/overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: A Azure AD fornece registos para ajudar a descobrir contas velhas. Além disso, utilize revisões de acesso à identidade do Azure Ative Directory para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso do utilizador pode ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado. Ao utilizar as máquinas Azure Virtual, terá de rever os grupos de segurança locais e os utilizadores para se certificar de que não existem contas inesperadas que possam comprometer o sistema.

* [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Configurar definições de diagnóstico para o Azure Ative Directory enviar os registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Além disso, utilize o Azure Monitor para rever registos e efetue consultas em dados de registo a partir de máquinas Virtuais Azure.

* [Compreender log analytics workspace](../azure-monitor/logs/log-analytics-tutorial.md)

* [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

* [Como monitorizar máquinas virtuais em Azure](../azure-monitor/vm/monitor-vm-azure.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize as funcionalidades de Proteção de Risco e Identidade do Azure Ative Para configurar respostas automatizadas para detetar ações suspeitas relacionadas com os recursos da sua conta de armazenamento. Deve permitir respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização.

* [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Em cenários de suporte em que a Microsoft precisa de acesso aos dados dos clientes (como durante um pedido de suporte), utilize o Customer Lockbox para máquinas virtuais Azure para rever e aprovar ou rejeitar pedidos de acesso aos dados dos clientes.

* [Compreender o bloqueio do cliente](../security/fundamentals/customer-lockbox-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [o controlo de segurança: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize tags para ajudar a rastrear máquinas virtuais Azure que armazenam ou processam informações sensíveis.

* [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Os recursos devem ser separados por rede/sub-rede virtuais, marcados adequadamente e protegidos dentro de um grupo de segurança de rede (NSG) ou por uma Firewall Azure. Para máquinas virtuais armazenar ou processar dados sensíveis, implementar políticas e procedimentos para desligá-los quando não estiverem a ser utilizados.

* [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

* [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

* [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Como configurar alerta ou alerta e negar com a Azure Firewall](../firewall/threat-intel.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Implementar solução de terceiros em perímetros de rede que monitorize para transferência não autorizada de informações sensíveis e bloqueie tais transferências enquanto alerta os profissionais de segurança da informação.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos do cliente como sensíveis para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

* [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Os dados em trânsito para, a partir e entre máquinas virtuais (VM) que estão a executar o Windows, são encriptados de várias maneiras, dependendo da natureza da ligação, como quando se liga a um VM numa sessão de RDP ou SSH.

A Microsoft utiliza o protocolo De Segurança da Camada de Transporte (TLS) para proteger os dados quando viaja entre os serviços na nuvem e os clientes.

* [Encriptação em trânsito em VMs](../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Utilize uma ferramenta de descoberta ativa de terceiros para identificar todas as informações sensíveis armazenadas, processadas ou transmitidas pelos sistemas tecnológicos da organização, incluindo as localizadas no local ou num prestador de serviços remotos e atualize o inventário de informações sensíveis da organização.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilizando o controlo de acesso baseado em funções Azure (Azure RBAC), pode segregar os deveres dentro da sua equipa e conceder apenas a quantidade de acesso aos utilizadores na sua máquina virtual (VM) de que precisam para desempenhar os seus trabalhos. Em vez de dar permissão ilimitada a todos no VM, só pode permitir certas ações. Pode configurar o controlo de acesso para o VM no portal Azure, utilizando o Azure CLI ou Azure PowerShell.

* [RBAC do Azure](../role-based-access-control/overview.md)

* [Funções incorporadas do Azure](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Implementar uma ferramenta de terceiros, como uma solução automatizada de prevenção de perdas de dados baseada em hospedeiros, para impor controlos de acesso para mitigar o risco de violações de dados.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Os discos virtuais em Máquinas Virtuais (VM) são encriptados em repouso utilizando a encriptação do lado do Servidor ou a encriptação do disco Azure (ADE). A Azure Disk Encryption aproveita a funcionalidade DM-Crypt do Linux para encriptar discos geridos com chaves geridas pelo cliente dentro do VM do hóspede. A encriptação do lado do servidor com as teclas geridas pelo cliente melhora no ADE, permitindo-lhe utilizar quaisquer tipos e imagens de OS para os seus VMs encriptando dados no serviço de Armazenamento.

* [Encriptação do disco Azure para conjuntos de escala de máquinas virtuais](./disk-encryption-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações nos conjuntos de escala de máquinas virtuais e recursos conexos.

* [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/alerts/alerts-activity-log.md)

* [Azure Storage analytics logging](../storage/common/storage-analytics-logging.md) (Registo de análise do Armazenamento do Azure)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte [o controlo de segurança: Gestão de vulnerabilidades.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nas suas Máquinas Virtuais Azure. Utilize a solução recomendada para a Azure Security ou para a realização de avaliações de vulnerabilidade para as suas máquinas virtuais.

* [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: Ativar atualizações automáticas de SISTEMAS OPERATIVOs para versões do sistema operativo suportadas ou para imagens personalizadas armazenadas numa Galeria de Imagens Partilhadas.

* [Upgrades automáticos de SO para conjuntos de escala de máquina virtual em Azure](./virtual-machine-scale-sets-automatic-upgrade.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implementar solução automatizada de gestão de patchs para títulos de software de terceiros

**Orientação**: Conjuntos de balança de máquina virtual Azure (VMSS) podem utilizar atualizações automáticas de imagem de SO. Pode utilizar a extensão de Configuração do Estado Azure Desired (DSC) para máquinas virtuais subjacentes no VMSS. O DSC é usado para configurar os VMs à medida que entram online, por isso estão a executar o software pretendido.

* [Usando conjuntos de balança de máquina virtual com a extensão Azure DSC](./virtual-machine-scale-sets-dsc.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Exportar resultados de digitalização a intervalos consistentes e comparar os resultados para verificar se as vulnerabilidades foram remediadas. Ao utilizar a recomendação de gestão de vulnerabilidades sugerida pelo Azure Security Center, o cliente pode entrar no portal da solução selecionada para visualizar dados históricos de digitalização.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Utilize as classificações de risco predefinidas (Pontuação Segura) fornecidas pelo Azure Security Center.

* [Compreenda a pontuação segura do Centro de Segurança Azure](../security-center/secure-score-security-controls.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para obter mais informações, consulte [controlo de segurança: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descobrir todos os recursos (incluindo máquinas Virtuais) dentro das suas subscrições. Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

* [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

* [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizá-los logicamente de acordo com uma taxonomia.

* [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, grupos de gestão e subscrições separadas, se for caso disso, para organizar e rastrear conjuntos de escala de máquinas virtuais e recursos conexos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

* [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Crie um inventário de recursos Azure aprovados e software aprovado para recursos de computação de acordo com as nossas necessidades organizacionais.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Além disso, utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da(s) subscrição. Isto pode ajudar em ambientes de alta segurança, como aqueles com contas de Armazenamento.

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: A Azure Automation proporciona controlo total durante a implantação, operações e desmantelamento de cargas de trabalho e recursos. Aproveite o Inventário de Máquinas Virtuais Azure para automatizar a recolha de informações sobre todo o software em Máquinas Virtuais. Nota: O nome do software, versão, editor e tempo de atualização estão disponíveis a partir do portal Azure. Para ter acesso à data de instalação e outras informações, o cliente necessário para ativar o diagnóstico ao nível do hóspede e trazer os registos do Evento do Windows para um espaço de trabalho do Log Analytics.

Atualmente, os controlos de aplicação adaptativa não estão disponíveis para conjuntos de balanças de máquinas virtuais.

* [Uma introdução à Automatização do Azure](../automation/automation-intro.md)

* [Como ativar o inventário Azure VM](../automation/automation-tutorial-installed-software.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: A Azure Automation proporciona controlo total durante a implantação, operações e desmantelamento de cargas de trabalho e recursos. Pode utilizar o Change Tracking para identificar todo o software instalado em Máquinas Virtuais. Pode implementar o seu próprio processo ou utilizar a Configuração do Estado da Automação Azure para remover software não autorizado.

* [Uma introdução à Automatização do Azure](../automation/automation-intro.md)

* [Acompanhe as mudanças no seu ambiente com a solução Change Tracking](../automation/change-tracking/overview.md)

* [Visão geral da configuração do estado da automação Azure](../automation/automation-dsc-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Atualmente, os controlos de aplicação adaptativa não estão disponíveis para conjuntos de balanças de máquinas virtuais. Utilize software de terceiros para controlar o uso apenas para aplicações aprovadas.

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/index.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação**: Atualmente, os controlos de aplicação adaptativa não estão disponíveis para conjuntos de balanças de máquinas virtuais. Implemente a solução de terceiros se esta não cumprir os requisitos da sua organização.

* [Como utilizar os controlos de aplicações adaptativos do Centro de Segurança Azure](../security-center/security-center-adaptive-application.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

* [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação**: Dependendo do tipo de scripts, pode utilizar configurações específicas do sistema operativo ou recursos de terceiros para limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional do Azure.

* [Como controlar a execução do script PowerShell em Ambientes windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: As aplicações de alto risco implementadas no seu ambiente Azure podem ser isoladas utilizando rede virtual, sub-rede, subscrições, grupos de gestão, etc. e suficientemente protegidas com um Azure Firewall, Web Application Firewall (WAF) ou um grupo de segurança de rede (NSG).

* [Redes virtuais e máquinas virtuais em Azure](../virtual-machines/network-overview.md)

* [Visão geral do Azure Firewall](../firewall/overview.md)

* [Visão geral do Firewall de Aplicação Web](../web-application-firewall/overview.md)

* [Descrição geral da segurança de rede](../virtual-network/network-security-groups-overview.md)

* [Visão geral da Rede Virtual Azure](../virtual-network/virtual-networks-overview.md)

* [Organize your resources with Azure management groups](../governance/management-groups/overview.md) (Organizar os recursos com os grupos de gestão do Azure)

* [Guia de decisão de subscrição](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [o controlo de segurança: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize a Política Azure ou o Centro de Segurança Azure para manter configurações de segurança para todos os recursos Azure. Além disso, o Azure Resource Manager tem a capacidade de exportar o modelo na Notação de Objetos JavaScript (JSON), que deve ser revisto para garantir que as configurações cumprem /excedem os requisitos de segurança para a sua empresa.

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Informações sobre como descarregar o modelo VM](/previous-versions/azure/virtual-machines/windows/download-template)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Utilize a recomendação do Centro de Segurança Azure [Remediar vulnerabilidades em configurações de segurança nas suas Máquinas Virtuais] para manter configurações de segurança em todos os recursos de computação.

* [Como monitorizar as recomendações do Centro de Segurança Azure](../security-center/security-center-recommendations.md)

* [Como remediar as recomendações do Centro de Segurança Azure](../security-center/security-center-remediate-recommendations.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Utilize modelos de Gestor de Recursos Azure e Políticas Azure para configurar de forma segura os recursos Azure associados aos conjuntos de escala de máquinas virtuais. Os modelos do Gestor de Recursos Azure são ficheiros baseados em JSON utilizados para implementar a máquina Virtual juntamente com os recursos Azure e o modelo personalizado terá de ser mantido. A Microsoft executa a manutenção nos modelos base. Use a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

* [Informação sobre a criação de modelos do Gestor de Recursos Azure](../virtual-machines/windows/ps-template.md)

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Existem várias opções para manter uma configuração segura para Azure Virtual Machines (VM) para implantação:

1. Modelos do Gestor de Recursos Azure: Estes são ficheiros baseados em JSON usados para implementar um VM a partir do portal Azure, e o modelo personalizado terá de ser mantido. A Microsoft executa a manutenção nos modelos base.

2. Disco rígido virtual personalizado (VHD): Em algumas circunstâncias, pode ser necessário ter ficheiros VHD personalizados utilizados, como quando se lida com ambientes complexos que não podem ser geridos através de outros meios.

3. Configuração do Estado da Automação Azure: Uma vez implantado o SISTEMA base, este pode ser utilizado para um controlo mais granular das definições e aplicado através da estrutura de automatização.

Para a maioria dos cenários, os modelos VM base da Microsoft combinados com a Configuração de Estado Desejada da Automação Azure podem ajudar a cumprir e manter os requisitos de segurança.

* [Informações sobre como descarregar o modelo VM](/previous-versions/azure/virtual-machines/windows/download-template)

* [Informação sobre a criação de modelos ARM](../virtual-machines/windows/ps-template.md)

* [Como carregar um VM VHD personalizado para Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Utilize Azure DevOps para armazenar e gerir de forma segura o seu código como políticas Azure personalizadas, modelos de Gestor de Recursos Azure, scripts de configuração do estado desejado, etc.  Para aceder aos recursos que gere em Azure DevOps, como o seu código, construções e rastreio de trabalho, tem de ter permissões para esses recursos específicos. A maioria das permissões são concedidas através de grupos de segurança incorporados, conforme descrito nas Permissões e acesso. Pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se estiverem integrados com Azure DevOps ou Ative Directory se estiverem integrados com o TFS.

* [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Se utilizar imagens personalizadas (por exemplo, disco rígido virtual), utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para garantir que apenas os utilizadores autorizados possam aceder às imagens.

* [Compreender Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Como configurar o Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Alavancar a Política Azure para alertar, auditar e impor configurações do sistema para as suas máquinas virtuais. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

**Orientação**: Azure Automation State Configuration é um serviço de gestão de configuração para nós de configuração estatal desejada (DSC) em qualquer datacenter de nuvem ou no local. Permite a escalabilidade através de milhares de máquinas de forma rápida e fácil a partir de um local central e seguro. Pode facilmente embarcar máquinas, atribuí-las configurações declarativas e ver relatórios que mostrem a conformidade de cada máquina com o estado pretendido especificado.

* [Máquinas de embarque para gestão por Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Aproveite o Centro de Segurança Azure para realizar análises de base para as suas máquinas Azure Virtual. Os métodos adicionais para configuração automatizada incluem a utilização da Configuração do Estado da Automação Azure.

* [Como remediar recomendações no Centro de Segurança Azure](../security-center/security-center-remediate-recommendations.md)

* [Começando com a configuração do Estado da Automação Azure](../automation/automation-dsc-getting-started.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Azure Automation State Configuration é um serviço de gestão de configuração para nós de configuração estatal desejada (DSC) em qualquer datacenter de nuvem ou no local. Permite a escalabilidade através de milhares de máquinas de forma rápida e fácil a partir de um local central e seguro. Pode facilmente embarcar máquinas, atribuí-las configurações declarativas e ver relatórios que mostrem a conformidade de cada máquina com o estado pretendido especificado.

* [Máquinas de embarque para gestão por Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e garantir uma gestão secreta para as suas aplicações em nuvem.

* [Como integrar-se com identidades geridas aZure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Como criar um Cofre-Chave](../key-vault/general/quick-create-portal.md)

* [Como autenticar para o Cofre de Chaves](../key-vault/general/authentication.md)

* [Como atribuir uma política de acesso ao Cofre de Chaves](../key-vault/general/assign-access-policy-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize identidades geridas para fornecer serviços Azure com uma identidade gerida automaticamente em Azure AD. Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

* [Como configurar identidades geridas](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.

* [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte [o controlo de segurança: defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizar software anti-malware gerido centralmente

**Orientação**: Utilize o Microsoft Antimalware para máquinas Azure Windows Virtual para monitorizar e defender continuamente os seus recursos. Você precisará de uma ferramenta de terceiros para proteção anti-malware na máquina virtual Azure Linux.

* [Como configurar o Microsoft Antimalware para serviços em nuvem e máquinas virtuais](../security/fundamentals/antimalware.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: Não aplicável às máquinas virtuais Azure, uma vez que se trata de um recurso computacional.

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Não aplicável

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Quando implementado para máquinas Virtual do Windows, o Microsoft Antimalware para Azure instalará automaticamente as atualizações mais recentes de assinatura, plataforma e motor por predefinição. Siga as recomendações no Azure Security Center: "Compute &amp; Apps" para garantir que todos os pontos finais estão atualizados com as assinaturas mais recentes. O Sistema operativo Windows pode ser ainda protegido com segurança adicional para limitar o risco de ataques baseados em vírus ou malware com o serviço de Proteção avançada de ameaças do Microsoft Defender que se integra com o Azure Security Center.

Você precisará de uma ferramenta de terceiros para proteção anti-malware na máquina virtual Azure Linux.

* [Como implementar o Microsoft Antimalware para serviços em nuvem azure e máquinas virtuais](../security/fundamentals/antimalware.md)

* [Proteção Avançada Contra Ameaças do Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

* [Como configurar o Microsoft Antimalware para serviços em nuvem e máquinas virtuais](../virtual-machines/security-recommendations.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [o controlo de segurança: recuperação de dados.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: Crie uma imagem instantânea da instância de conjunto de máquina virtual Azure ou do disco gerido ligado à instância utilizando APIs powerShell ou REST. Também pode utilizar a Azure Automation para executar os scripts de backup em intervalos regulares.

* [Como tirar uma foto de uma caixa de conjunto de escala de máquina virtual e disco gerido](./virtual-machine-scale-sets-faq.md#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Introdução à Azure Automation](../automation/automation-intro.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Crie instantâneos das suas máquinas virtuais Azure ou dos discos geridos ligados a essas instâncias utilizando APIs PowerShell ou REST. Faça uma assistência às chaves geridas pelo cliente dentro do Cofre da Chave Azure.

Ativar a Azure Backup e direcionar as Máquinas Virtuais Azure (VM), bem como os períodos de frequência e retenção pretendidos. Isto inclui cópia de segurança completa do estado do sistema. Se estiver a utilizar a encriptação do disco Azure, a cópia de segurança do Azure VM lida automaticamente com a cópia de segurança das teclas geridas pelo cliente.

* [Backup em VMs Azure que usam encriptação](../backup/backup-azure-vms-encryption.md)

* [Visão geral do backup Azure VM](../backup/backup-azure-vms-introduction.md)

* [Como tirar uma foto de uma caixa de conjunto de escala de máquina virtual e disco gerido](./virtual-machine-scale-sets-faq.md#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Como backup chaves chave cofre em Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Garantir a capacidade de efetuar periodicamente a restauração de dados do disco gerido dentro da Cópia de Segurança Azure. Se necessário, teste restaurar o conteúdo para uma rede virtual isolada ou subscrição. Cliente para testar a restauração de chaves geridas pelo cliente.

Se estiver a utilizar a encriptação do disco Azure, pode restaurar os conjuntos de escala de máquina virtual com as teclas de encriptação do disco. Ao utilizar a encriptação do disco, pode restaurar o Azure VM com as teclas de encriptação do disco.

* [Backup em VMs Azure que usam encriptação](../backup/backup-azure-vms-encryption.md)

* [Restaurar um disco e criar uma VM recuperada no Azure](../backup/tutorial-restore-disk.md)

* [Como restaurar chaves chave do cofre em Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Como ativar a encriptação do disco para conjuntos de escala de máquina virtual Azure](./disk-encryption-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Ativar a proteção para o disco gerido utilizando fechaduras. Ativar Soft-Delete e limpar a proteção no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa.

* [Bloquear recursos para prevenir alterações inesperadas](../azure-resource-manager/management/lock-resources.md)

* [Azure Key Vault elimina e purga a proteção geral](../key-vault/general/soft-delete-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controlo de segurança: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

* [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Alavancar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) usando tags e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure, especialmente aqueles que processam dados sensíveis. É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.

* [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

* [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

* [Publicação do NIST - Guia de Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

* [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de Exportação Contínua para ajudar a identificar riscos para os recursos da Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

* [Como configurar a exportação contínua](../security-center/continuous-export.md)

* [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas de segurança e recomendações para proteger os seus recursos Azure.

* [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte [o controlo de segurança: Testes de penetração e exercícios de equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft.

* [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)