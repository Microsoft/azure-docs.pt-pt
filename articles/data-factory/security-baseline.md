---
title: Linha de segurança Azure para Azure Data Factory
description: Linha de segurança Azure para Azure Data Factory
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5ff84731196b2faf71d0134844a0a46f2198cc2c
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807636"
---
# <a name="azure-security-baseline-for-azure-data-factory"></a>Linha de segurança Azure para Azure Data Factory

A Linha de Base de Segurança Azure para a Azure Data Factory contém recomendações que o ajudarão a melhorar a postura de segurança da sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../security/benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte a visão geral das [linhas de base de segurança Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte [o controlo de segurança: segurança da rede.](../security/benchmarks/security-control-network-security.md)*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Ao criar um Tempo de Execução de Integração Azure-SSIS (IR), tem a opção de se juntar a ele com uma rede virtual. Isto permitirá à Azure Data Factory criar certos recursos de rede, como um grupo de segurança de rede (NSG) e um equilibrador de carga. Também tem a capacidade de fornecer o seu próprio endereço IP público estático ou ter a Azure Data Factory a criar um para si. No NSG que é automaticamente criado pela Azure Data Factory, a porta 3389 está aberta a todo o tráfego por padrão. Bloqueie isto para garantir que apenas os seus administradores tenham acesso.

Self-Hosted IRs podem ser implantados numa máquina virtual no local ou numa máquina virtual Azure dentro de uma rede virtual. Certifique-se de que a sua sub-rede de rede virtual tem um NSG configurado para permitir apenas o acesso administrativo. O Azure-SSIS IR não permitiu a saída da porta 3389 por defeito na regra de firewall do Windows em cada nó DE IR para proteção. Pode proteger os seus recursos configurados em rede virtuais associando um NSG à sub-rede e estabelecendo regras rígidas.

Quando o Private Link estiver disponível, utilize pontos finais privados para garantir que quaisquer recursos estão ligados ao seu oleoduto Azure Data Factory, como o Azure SQL Server. Com o Private Link, o tráfego entre a sua rede virtual e o serviço atravessa a rede de espinha dorsal da Microsoft, eliminando a exposição da Internet pública.

* [Como criar um Azure-SSIS IR](./create-azure-ssis-integration-runtime.md)

* [Como criar e configurar um IR auto-hospedado](./create-self-hosted-integration-runtime.md)

* [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

* [Junte-se a um Azure-SSIS IR a uma rede virtual](./join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)

* [Compreenda a ligação privada Azure](../private-link/private-link-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Orientação**: Utilize o Centro de Segurança Azure e remedia as recomendações de proteção da rede para o grupo de segurança da rede virtual e da rede associado à sua implementação de Tempo de Execução de Integração.

Além disso, ative os registos de fluxo do grupo de segurança da rede (NSG) para o NSG protegendo a sua implementação de Tempo de Execução de Integração e envie registos para uma conta de armazenamento Azure para auditoria de tráfego.

Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

* [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

* [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

* [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; esta recomendação destina-se ao Azure Apps Service ou a computar recursos que hospedam aplicações web.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ativar a Norma de Proteção DDoS nas redes virtuais associadas à sua implementação de tempo de execução de integração para proteção contra ataques de negação de serviço distribuídos. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

* [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)

* [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](../security-center/azure-defender.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Ative os registos de fluxo do grupo de segurança da rede (NSG) para o NSG que protege a sua implementação de tempo de execução de integração e envie registos para uma conta de armazenamento Azure para auditoria de tráfego.

Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

* [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

* [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Se pretender inspecionar o tráfego de saída da Azure-SSIS IR, pode encaminhar o tráfego iniciado desde a Azure-SSIS IR até ao aparelho de firewall no local através do túnel da força Azure ExpressRoute ou para um Aparelho Virtual de Rede (NVA) do Azure Marketplace que suporta as capacidades de IDS/IPS. Se a deteção e/ou prevenção de intrusões baseada na inspeção de carga útil não for um requisito, a Azure Firewall com Threat Intelligence pode ser usada.

* [Junte-se a um tempo de integração Azure-SSIS a uma rede virtual](./join-azure-ssis-integration-runtime-virtual-network.md)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Como configurar alertas com a Azure Firewall](../firewall/threat-intel.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; esta recomendação destina-se ao Azure Apps Service ou a computar recursos que hospedam aplicações web.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize tags de serviço de rede virtuais para definir controlos de acesso à rede em grupos de segurança de rede (NSG) ou Azure Firewall. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, DataFactoryManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego de entrada para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

* [Compreender e usar etiquetas de serviço](../virtual-network/service-tags-overview.md)

* [Compreenda as etiquetas de serviço específicas da Azure Data Factory](./join-azure-ssis-integration-runtime-virtual-network.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para configurações de rede e recursos de rede associados aos seus casos de Fábrica de dados Azure com a Política Azure. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.DataFactory" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede das suas instâncias Azure Data Factory. Pode também utilizar definições políticas incorporadas relacionadas com a criação de redes ou com as suas instâncias de fábrica de dados Azure, tais como:
- A Norma de Proteção DDoS deve ser ativada

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Amostras da Política Azure para networking](../governance/policy/samples/built-in-policies.md#network) 

* [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para recursos relacionados com a segurança da rede e fluxo de tráfego para as suas instâncias da Azure Data Factory para fornecer metadados e organização lógica.

Utilize qualquer uma das definições de Política Azure incorporadas relacionadas com a marcação, tais como, "Exigir etiqueta e seu valor", para garantir que todos os recursos são criados com etiquetas e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas.

* [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações nos recursos de rede relacionados com as suas instâncias da Azure Data Factory. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

* [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Como criar alertas no Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte [o controlo de segurança: Registar e monitorizar](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém a fonte de tempo utilizada para os recursos Azure, como a Azure Data Factory para os timetamps nos registos.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança gerados pela Azure Data Factory. Dentro do Azure Monitor, é possível consultar o espaço de trabalho Log Analytics que está configurado para receber os seus registos de atividade da Azure Data Factory. Utilize contas de armazenamento Azure para armazenamento de registos de longo prazo/arquivo ou centros de eventos para exportar dados para outros sistemas.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou para um terceiro Security Incident and Event Management (SIEM). Também pode integrar a Azure Data Factory com o Git para alavancar vários benefícios de controlo de origem, tais como a capacidade de rastrear/auditar alterações e a capacidade de reverter as alterações que introduzem bugs.

* [Como configurar definições de diagnóstico](../azure-monitor/platform/diagnostic-settings.md)

* [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Controlo de fontes na Azure Data Factory](./source-control.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Para controlar a verificação de dados, ative as definições de diagnóstico do Registo de Atividade do Azure e envie os registos para um espaço de trabalho log Analytics, Azure Event Hubs ou Azure Storage Account para o arquivo. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para os seus recursos Azure.

Utilize definições de diagnóstico para configurar registos de diagnóstico para recursos não conformes na Azure Data Factory, tais como métricas e dados executados pelo pipeline. A Azure Data Factory armazena dados de condutas geridas por gasodutos durante 45 dias. Para reter estes dados por um período mais longo, guarde os seus registos de diagnóstico numa conta de armazenamento para auditoria ou inspeção manual e especifique o tempo de retenção em dias. Também pode transmitir os registos para Azure Event Hubs ou enviar os registos para um espaço de trabalho log Analytics para análise.

* [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/platform/activity-log.md)

* [Compreenda os registos de diagnóstico da Azure Data Factory](./monitor-using-azure-monitor.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure (VM), pode utilizar o Azure Monitor para recolher dados da máquina virtual. A instalação da extensão VM do Log Analytics permite ao Azure Monitor recolher dados dos seus VMs Azure. O Azure Security Center pode então fornecer monitorização de registo de eventos de segurança para máquinas virtuais. Dado o volume de dados que o registo do evento de segurança gera, este não é armazenado por padrão.

Se a sua organização quiser reter os dados de registo de eventos de segurança, pode ser armazenado dentro de um nível de Recolha de Dados, altura em que pode ser consultado no Log Analytics.

* [Como recolher dados de Azure Virtual Machines no Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)

* [Habilitação da recolha de dados no Centro de Segurança Azure](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Ativar as definições de diagnóstico para a Azure Data Factory. Se optar por armazenar registos num espaço de trabalho do Log Analytics, desapasse o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

* [Como permitir registos de diagnóstico na Azure Data Factory](./monitor-using-azure-monitor.md)

* [Como definir parâmetros de retenção de registos para log analytics workspaces](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Ative as definições de diagnóstico para a Azure Data Factory e envie registos para um espaço de trabalho Log Analytics. Utilize o Log Analytics para analisar e monitorizar os seus registos para um comportamento anómalo e rever regularmente os resultados. Certifique-se de que também ativa as definições de diagnóstico de quaisquer lojas de dados relacionadas com as suas implementações na Azure Data Factory. Consulte a linha de segurança de cada serviço para obter orientações sobre como ativar as definições de diagnóstico.

Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure (VM), ative também as definições de diagnóstico para o VM.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

* [Log Analytics schema](./monitor-using-azure-monitor.md#schema-of-logs-and-events)

* [Como recolher dados de uma Máquina Virtual Azure com Monitor Azure](../azure-monitor/learn/quick-collect-azurevm.md)

* [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Pode elevar alertas sobre métricas suportadas na Data Factory, indo para a secção de Métricas de Alerta &amp; no Monitor Azure.

Configure as definições de diagnóstico para a Azure Data Factory e envie registos para um espaço de trabalho log analytics. Dentro do seu espaço de trabalho Log Analytics, os alertas de configuração devem ocorrer para quando um conjunto de condições pré-definidos ocorrer. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

Além disso, certifique-se de que permite configurações de diagnóstico para serviços relacionados com as suas lojas de dados. Pode consultar a linha de segurança de cada serviço.

* [Alertas na Fábrica de Dados Azure](./monitor-visually.md#alerts)

* [Todas as métricas suportadas](../azure-monitor/platform/metrics-supported.md)

* [Como configurar alertas no Log Analytics Workspace](../azure-monitor/platform/alerts-log.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure, poderá utilizar o Microsoft Antimalware para serviços em nuvem azure e máquinas virtuais e configurar as suas máquinas virtuais para registar eventos numa Conta de Armazenamento Azure. Configure um espaço de trabalho Log Analytics para ingerir os eventos a partir das Contas de Armazenamento e criar alertas sempre que apropriado. Siga as recomendações no Azure Security Center: "Compute &amp; Apps".

* [Como configurar o Microsoft Antimalware para serviços em nuvem e máquinas virtuais](../security/fundamentals/antimalware.md)

* [Como permitir a monitorização ao nível dos hóspedes para máquinas virtuais](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Não aplicável; A Azure Data Factory não processa nem produz registos relacionados com DNS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure (VM), pode ativar o registo de auditoria da linha de comando. O Centro de Segurança Azure fornece monitorização de registo de eventos de segurança para VMs Azure. O Security Center fornece o Agente de Monitorização da Microsoft em todos os VMs Azure suportados e quaisquer novos que sejam criados se o fornecimento automático estiver ativado ou se puder instalar o agente manualmente. O agente permite o evento de criação de processo 4688 e o campo CommandLine dentro do evento 4688. Os novos processos criados no VM são registados pelo EventLog e monitorizados pelos serviços de deteção do Security Center.

* [Data collection in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier) (Recolha de dados no Centro de Segurança do Azure)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para obter mais informações, consulte [controlo de segurança: Identidade e controlo de acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Dentro da Azure Data Factory, certifique-se de que rastreia e concilia o acesso do utilizador regularmente. Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser membro das funções contribuidor ou proprietário ou administrador da subscrição do Azure.

Além disso, ao nível do inquilino, o Azure Ative Directory (AD) tem funções incorporadas que devem ser explicitamente atribuídas e são passíveis de serem consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos que têm acesso administrativo ao plano de controlo das suas instâncias da Azure Data Factory.

Embora o Azure AD seja o método recomendado para administrar o acesso do utilizador, tenha em mente que se estiver a executar o Tempo de Execução de Integração numa Máquina Virtual Azure (VM), o seu VM também pode ter contas locais. As contas locais e de domínio devem ser revistas e geridas, normalmente com uma pegada mínima. Além disso, aconselhamos que o Gestor de Identidade Privilegiado seja revisto para a funcionalidade Just In Time para reduzir a disponibilidade de permissões administrativas.

* [Funções e permissões do Azure Data Factory](./concepts-roles-permissions.md)

* [Informação sobre Gestor de Identidade Privilegiada](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

* [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

* [Informação para Contas Locais](../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: A Azure Data Factory utiliza o Azure Ative Directory (AD) para fornecer acesso ao portal Azure, bem como à consola Azure Data Factory. O Azure AD não tem o conceito de senhas padrão, no entanto, é responsável por alterar ou não permitir senhas padrão para quaisquer aplicações personalizadas ou de terceiros.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Crie procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas para o acesso ao plano de controlo Azure (portal Azure) bem como à consola Azure Data Factory. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas dentro da Azure AD.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou políticas de Azure incorporadas, tais como:
- Deve haver mais de um proprietário atribuído à sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure, as contas do administrador em Azure Virtual Machines também podem ser configuradas com o Azure Privileged Identity Manager (PIM). O Azure Privileged Identity Manager fornece várias opções, tais como elevação just in time, autenticação multi-factor e opções de delegação para que as permissões estejam disponíveis apenas para prazos específicos e exijam uma segunda pessoa para aprovar.

* [Compreender a identidade e o acesso do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

* [Como usar a Política Azure](../governance/policy/tutorials/create-and-manage.md)

* [Informação sobre Gestor de Identidade Privilegiada](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Funções e permissões do Azure Data Factory](./concepts-roles-permissions.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: Utilize um registo de aplicações Azure (principal serviço) para recuperar um sinal que a sua aplicação ou função pode usar para aceder e interagir com os cofres dos Serviços de Recuperação.

* [Como chamar Azure REST APIs](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Como registar a sua aplicação ao cliente (principal serviço) com a Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Informação da Azure Recovery Services API](/rest/api/recoveryservices/)

* [Informação sobre a API REST para a Azure Data Factory](/rest/api/datafactory/)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ativar a autenticação multi-factor do Diretório Ativo Azure (MFA) e seguir as recomendações do Azure Security Center Identity and Access Management.

* [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize estações de trabalho de acesso privilegiada (PAW) com autenticação multi-factor (MFA) configuradas para iniciar sessão e configurar recursos Azure.

* [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança do Azure Ative Directory para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure (VM), pode, além disso, embarcar no seu VM para Azure Sentinel. O Microsoft Azure Sentinel é uma solução de gestão de eventos de informação de segurança (SIEM) e de orquestração de segurança (SOAR). O Azure Sentinel fornece análises inteligentes de segurança e inteligência de ameaças em toda a empresa, fornecendo uma única solução para deteção de alerta, visibilidade de ameaças, caça proativa e resposta a ameaças.

* [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](../active-directory/identity-protection/overview-identity-protection.md)

* [Como monitorizar as atividades de identidade e acesso dos utilizadores no Centro de Segurança do Azure](../security-center/security-center-identity-access.md)

* [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

* [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Uma fábrica de dados pode ser associada a uma identidade gerida para os recursos da Azure que representa a fábrica de dados específica. Pode utilizar esta identidade gerida para autenticação da Base de Dados Azure SQL. A fábrica designada pode aceder e copiar dados de ou para a sua base de dados utilizando esta identidade.

Se estiver a executar o seu Tempo de Execução de Integração (IR) numa Máquina Virtual Azure, pode utilizar identidades geridas para autenticar qualquer serviço que suporte a autenticação AD Azure, incluindo o Key Vault, sem quaisquer credenciais no seu código. O seu código que está a funcionar numa máquina virtual, pode usar a identidade gerida para solicitar o acesso a fichas de acesso para serviços que suportem a autenticação Azure AD.

* [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [Quais são as identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

* [Copiar e transformar dados na Base de Dados Azure SQL utilizando a Azure Data Factory](./connector-azure-sql-database.md)

* [Como configurar e gerir a autenticação do Azure Ative Directory com a Base de Dados Azure SQL](../azure-sql/database/authentication-aad-configure.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (AD) fornece registos para o ajudar a descobrir contas velhas. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

Se estiver a executar a sua Integração de Tempo de Execução numa Máquina Virtual Azure, terá de rever os grupos de segurança locais e os utilizadores para garantir que não existem contas inesperadas que possam comprometer o sistema.

* [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

* [Compreenda a Azure AD reportando](../active-directory/reports-monitoring/index.yml)

* [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Tem acesso a atividade de login do Azure Ative Directory (AD), fontes de registo de eventos de auditoria e risco, que lhe permitem integrar-se com qualquer ferramenta SIEM/Monitoring. Pode agilizar este processo criando definições de diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas de registo pretendidos no Log Analytics.

Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure (VM), a bordo que VM a Azure Sentinel. O Microsoft Azure Sentinel é uma solução de gestão de eventos de informação de segurança (SIEM) e de orquestração de segurança (SOAR). O Azure Sentinel fornece análises inteligentes de segurança e inteligência de ameaças em toda a empresa, fornecendo uma única solução para deteção de alerta, visibilidade de ameaças, caça proativa e resposta a ameaças.

* [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Autorizar o acesso aos recursos do Event Hubs utilizando o Azure Ative Directory](../event-hubs/authorize-access-azure-active-directory.md)

* [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize o Azure Ative Directory (AD) como o sistema central de autenticação e autorização para os seus recursos Azure Data Factory, tais como Azure SQL Database ou Azure Virtual Machines. Para desvio de comportamento de login de conta no plano de controlo (o portal Azure), utilize recursos de Proteção de Identidade AD AZure e funcionalidades de deteção de riscos para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Você também pode ingerir dados em Azure Sentinel para mais investigação.

* [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Configure e gere a autenticação do Azure Ative Directory com o SQL](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell)

* [Ativar a autenticação do Azure Active Directory para o Azure-SSIS Integration Runtime](./enable-aad-authentication-azure-ssis-ir.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Em cenários de suporte onde a Microsoft precisa de aceder aos dados dos clientes, o Azure Customer Lockbox fornece uma interface para os clientes reverem e aprovarem ou rejeitarem os pedidos de acesso aos dados dos clientes. Note que, embora o Azure Lockbox não esteja disponível para a própria Azure Data Factory, o Azure Lockbox suporta a Base de Dados Azure SQL e máquinas virtuais Azure.

* [Compreender o bloqueio do cliente](../security/fundamentals/customer-lockbox-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [o controlo de segurança: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

Utilize a funcionalidade de descoberta e classificação de dados da Base de Dados Azure SQL. A descoberta e classificação de dados fornece capacidades avançadas incorporadas na Base de Dados Azure SQL para descobrir, classificar, rotular &amp; protegendo os dados sensíveis nas suas bases de dados.

* [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

* [Como utilizar a descoberta e classificação de dados para o Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Integração Os tempos de funcionação devem ser separados por rede virtual (VNet)/sub-rede e marcados adequadamente.

 Também pode utilizar Pontos Finais Privados para realizar o isolamento da rede. Um Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura a um serviço alimentado pela Azure Private Link. O Ponto Final Privado utiliza um endereço IP privado na VNet e leva de forma eficaz o serviço até à VNet.

* [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

* [Compreender Ligação Privada](../private-link/private-endpoint-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Para fontes de dados (como a Base de Dados Azure SQL) armazenar ou processar informações sensíveis para a sua implantação na Fábrica de Dados Azure, marque os recursos conexos como sensíveis através de tags.

Onde o Private Link estiver disponível, utilize pontos finais privados para garantir quaisquer recursos ligados ao seu pipeline Azure Data Factory. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Também pode reduzir o risco de exfiltração de dados configurando um conjunto rigoroso de regras de saída num grupo de segurança de rede (NSG) e associando esse NSG à sua sub-rede.

* [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

* [Compreenda a ligação privada Azure](../private-link/private-link-overview.md)

* [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Se a loja de dados em nuvem suportar HTTPS ou TLS, todas as transferências de dados entre os serviços de movimento de dados na Data Factory e uma loja de dados em nuvem são através de um canal seguro HTTPS ou TLS. A versão TLS utilizada é 1.2.

Todas as ligações à Base de Dados Azure SQL e à Azure Synapse Analytics requerem encriptação (SSL/TLS) enquanto os dados estão em trânsito de e para a base de dados. Quando estiver a ser autor de um oleoduto utilizando o JSON, adicione a propriedade de encriptação e desacri-a para ser verdadeira na cadeia de ligação. Para o armazenamento Azure, pode utilizar HTTPS na cadeia de ligação.

* [Compreender a encriptação em trânsito na Azure Data Factory](./data-movement-security-considerations.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Se utilizar a Azure Data Factory para copiar e transformar as suas instâncias de Base de Dados Azure SQL, utilize a funcionalidade de deteção e classificação de dados da Base de Dados Azure SQL. A descoberta e classificação de dados fornece capacidades avançadas incorporadas na Base de Dados Azure SQL para descobrir, classificar, rotular &amp; protegendo os dados sensíveis nas suas bases de dados.

As funcionalidades de descoberta e classificação de dados ainda não estão disponíveis para outros serviços Azure.

* [Como utilizar a descoberta e classificação de dados para o Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para controlar o acesso ao plano de controlo Azure Data Factory (o portal Azure).

Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser membro das funções contribuidor ou proprietário ou administrador da subscrição do Azure.

Para as suas fontes de dados da Data Factory, como a Base de Dados Azure SQL, consulte a linha de base de segurança para esse serviço para obter mais informações sobre o Azure RBAC.

* [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

* [Funções e permissões do Azure Data Factory](./concepts-roles-permissions.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: As características de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Storage ou para calcular recursos. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

* [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Recomendamos que ative o mecanismo de encriptação de dados para quaisquer lojas de dados relacionadas com as suas implementações na Fábrica de Dados Azure. Pode consultar a linha de base de segurança para esse serviço para obter mais informações sobre a encriptação dos dados em repouso.

Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure, os discos virtuais em Máquinas Virtuais do Windows (VM) são encriptados em repouso utilizando a encriptação do lado do Servidor ou a encriptação do disco Azure (ADE). A Azure Disk Encryption aproveita a funcionalidade BitLocker do Windows para encriptar discos geridos com chaves geridas pelo cliente dentro do VM do hóspede. A encriptação do lado do servidor com as teclas geridas pelo cliente melhora no ADE, permitindo-lhe utilizar quaisquer tipos e imagens de OS para os seus VMs encriptando dados no serviço de Armazenamento.

Você pode armazenar credenciais ou valores secretos em um Cofre de Chave Azure e usá-los durante a execução do gasoduto para passar para as suas atividades. Também pode armazenar credenciais para lojas de dados e cálculos num Cofre de Chaves Azure. A Azure Data Factory recupera as credenciais ao executar uma atividade que utiliza a loja/computação de dados.

* [Compreender a encriptação em repouso na Azure Data Factory](./data-movement-security-considerations.md)

* [Encriptação lateral do servidor dos discos geridos pelo Azure](../virtual-machines/disk-encryption.md)

* [Encriptação do disco Azure para VMs windows](../virtual-machines/windows/disk-encryption-overview.md)

* [Como usar segredos do Azure Key Vault em atividades de pipeline](./how-to-use-azure-key-vault-secrets-pipeline-activities.md)

* [Como credenciar em Azure Key Vault](./store-credentials-in-key-vault.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações na Fábrica de Dados Azure e recursos conexos.

* [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/platform/alerts-activity-log.md)

* [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/platform/alerts-activity-log.md)

* [Azure Storage analytics logging](../storage/common/storage-analytics-logging.md) (Registo de análise do Armazenamento do Azure)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte [o controlo de segurança: Gestão de vulnerabilidades.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Se estiver a utilizar a Base de Dados Azure SQL como uma loja de dados, ative a Segurança Avançada de Dados para a Base de Dados Azure SQL e siga as recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nos seus Servidores SQL Azure.

Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure (VM), siga as recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nos seus VMs. Utilize a solução recomendada para a Azure Security ou para a realização de avaliações de vulnerabilidade para as suas máquinas virtuais.

* [Como executar avaliações de vulnerabilidade na Base de Dados Azure SQL](../azure-sql/database/sql-vulnerability-assessment.md)

* [Como permitir a Segurança Avançada de Dados](../azure-sql/database/azure-defender-for-sql.md)

* [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure (VM), utilize a solução de Gestão de Atualização Azure para gerir atualizações e patches para os seus VMs. A Update Management baseia-se no repositório de atualização configurado localmente para corrigir sistemas Windows suportados. Ferramentas como o System Center Updates Publisher (Editor de Atualizações) permitem-lhe publicar atualizações personalizadas nos Serviços de Atualização do Servidor do Windows (WSUS). Este cenário permite que a Gestão de Atualização remendo máquinas que usam o Gestor de Configuração como repositório de atualização com software de terceiros.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

* [Solução de Gestão de Atualização em Azure](../automation/update-management/overview.md)

* [Gerir atualizações e patches para os seus VMs Azure](../automation/update-management/manage-updates-for-vm.md)

* [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implementar solução automatizada de gestão de patchs para títulos de software de terceiros

**Orientação**: Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure (VM), poderá utilizar uma solução de gestão de patch de terceiros. Pode utilizar a solução Azure Update Management para gerir atualizações e patches para as suas máquinas virtuais. A Update Management baseia-se no repositório de atualização configurado localmente para corrigir sistemas Windows suportados. Ferramentas como o System Center Updates Publisher (Editor de Atualizações) permitem-lhe publicar atualizações personalizadas nos Serviços de Atualização do Servidor do Windows (WSUS). Este cenário permite que a Gestão de Atualização remendo máquinas que usam o Gestor de Configuração como repositório de atualização com software de terceiros.

* [Solução de Gestão de Atualização em Azure](../automation/update-management/overview.md)

* [Gerir atualizações e patches para os seus VMs Azure](../automation/update-management/manage-updates-for-vm.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure, exporte os resultados da varredura em intervalos consistentes e compare os resultados para verificar se as vulnerabilidades foram remediadas. Ao utilizar a recomendação de gestão de vulnerabilidades sugerida pelo Azure Security Center, pode entrar no portal da solução selecionada para visualizar dados históricos de digitalização.

* [Compreender o scanner de vulnerabilidade integrado para máquinas virtuais](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Se executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure, pode utilizar o scanner de vulnerabilidade nativa. O scanner de vulnerabilidade incluído no Azure Security Center é alimentado pela Qualys. O scanner da Qualys é a ferramenta líder para a identificação em tempo real de vulnerabilidades nas suas Máquinas Virtuais Azure.

Quando o Centro de Segurança identifica vulnerabilidades, apresenta as descobertas e informações relacionadas como recomendações. As informações relacionadas incluem medidas de reparação, CVEs relacionados, pontuações de CVSS, e muito mais. Pode ver as vulnerabilidades identificadas para uma ou mais subscrições ou para uma máquina virtual específica.

* [Scanner integrado de vulnerabilidade para máquinas virtuais](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para obter mais informações, consulte [controlo de segurança: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize a solução automatizada de Deteção de Ativos

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua(s) subscrição( s). Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como os recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

* [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

* [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription)

* [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

* [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear os recursos do Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Além disso, utilize a Azure Policy para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e Manter um inventário dos recursos aprovados do Azure

**Orientação**: Defina recursos Azure aprovados e software aprovado para recursos de computação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na sua(s) subscrição.

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure (VM), aproveite o Inventário de Máquinas Virtuais Azure para automatizar a recolha de informações sobre todo o software em Máquinas Virtuais. A Automatização do Azure fornece controlo total durante a implementação, as operações e a desativação de cargas de trabalho e recursos.

Nota: O nome do software, versão, editor e tempo de atualização estão disponíveis a partir do portal Azure. Para ter acesso à data de instalação e outras informações, o cliente necessário para ativar o diagnóstico ao nível do hóspede e trazer os registos do Evento do Windows para um espaço de trabalho do Log Analytics.

* [Uma introdução à Automatização do Azure](../automation/automation-intro.md)

* [Como ativar o inventário Azure VM](../automation/automation-tutorial-installed-software.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure, a Azure Automation fornece controlo total durante a implementação, operações e desmantelamento de cargas de trabalho e recursos. Pode utilizar o Change Tracking para identificar todo o software instalado em Máquinas Virtuais. Pode implementar o seu próprio processo ou utilizar a Configuração do Estado da Automação Azure para remover software não autorizado.

* [Uma introdução à Automatização do Azure](../automation/automation-intro.md)

* [Acompanhe as mudanças no seu ambiente com a solução Change Tracking](../automation/change-tracking/overview.md)

* [Visão geral da configuração do estado da automação Azure](../automation/automation-dsc-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure (VM), utilize controlos de aplicação adaptativos do Centro de Segurança Azure para garantir que apenas o software autorizado executa e todo o software não autorizado está impedido de executar em VMs.

* [Como utilizar os controlos de aplicações adaptativos do Centro de Segurança Azure](../security-center/security-center-adaptive-application.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/index.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação**: O controlo de aplicações adaptativos é uma solução inteligente, automatizada e de ponta a ponta do Azure Security Center, que o ajuda a controlar quais aplicações podem funcionar nas suas máquinas Azure e não-Azure (Windows e Linux). Implemente uma solução de terceiros se esta não cumprir os requisitos da sua organização.

Note que isto só se aplica se o seu tempo de execução de integração estiver a funcionar numa Máquina Virtual Azure.

* [Como utilizar os controlos de aplicações adaptativos do Centro de Segurança Azure](../security-center/security-center-adaptive-application.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

* [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação**: Se estiver a executar a sua Integração de Tempo de Execução numa Máquina Virtual Azure, dependendo do tipo de scripts, poderá utilizar configurações específicas do sistema operativo ou recursos de terceiros para limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional do Azure. Também pode aproveitar os Controlos de Aplicação Adaptativa do Azure Security Center para garantir que apenas o software autorizado executa e todo o software não autorizado está bloqueado de ser executado em Máquinas Virtuais Azure.

* [Como controlar a execução do script PowerShell em Ambientes windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

* [Como utilizar os controlos de aplicações adaptativos do Centro de Segurança Azure](../security-center/security-center-adaptive-application.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: As aplicações de alto risco implementadas no seu ambiente Azure podem ser isoladas utilizando rede virtual, sub-rede, subscrições, grupos de gestão, etc. e suficientemente protegidas com um Azure Firewall, Web Application Firewall (WAF) ou um grupo de segurança de rede (NSG).

* [Redes virtuais e máquinas virtuais em Azure](../virtual-machines/network-overview.md)

* [O que é o Azure Firewall?](../firewall/overview.md)

* [O que é a Firewall de Aplicações Web do Azure?](../web-application-firewall/overview.md)

* [Grupos de segurança de rede](../virtual-network/network-security-groups-overview.md)

* [O que é a Rede Virtual Azure?](../virtual-network/virtual-networks-overview.md)

* [Organize your resources with Azure management groups](../governance/management-groups/overview.md) (Organizar os recursos com os grupos de gestão do Azure)

* [Guia de decisão de subscrição](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [o controlo de segurança: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Definir e implementar configurações de segurança padrão para Azure Data Factory com Azure Policy. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.DataFactory" para criar políticas personalizadas para auditar ou impor a configuração das suas instâncias da Azure Data Factory.

* [Como ver pseudónimos disponíveis da Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias)

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Se estiver a executar a sua Integração de Tempo de Execução numa Máquina Virtual Azure, utilize a recomendação do Centro de Segurança Azure [Corrigir Vulnerabilidades nas Configurações de Segurança nas suas Máquinas Virtuais] para manter configurações de segurança em todos os recursos de computação.

* [Como monitorizar as recomendações do Centro de Segurança Azure](../security-center/security-center-recommendations.md)

* [Como remediar as recomendações do Centro de Segurança Azure](../security-center/security-center-remediate-recommendations.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

* [Informação sobre a criação de modelos do Gestor de Recursos Azure](../virtual-machines/windows/ps-template.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure (VM), note que existem várias opções para manter uma configuração segura para VMs para implementação:
- Modelos do Gestor de Recursos Azure: Estes são ficheiros baseados em JSON usados para implementar um VM a partir do portal Azure, e o modelo personalizado terá de ser mantido. A Microsoft executa a manutenção nos modelos base.
- Disco rígido virtual personalizado (VHD): Em algumas circunstâncias, pode ser necessário ter ficheiros VHD personalizados utilizados, como quando se lida com ambientes complexos que não podem ser geridos através de outros meios. - Configuração do Estado da Automação Azure: Uma vez implantado o SISTEMA base, este pode ser utilizado para um controlo mais granular das definições e aplicado através da estrutura de automatização.

Para a maioria dos cenários, os modelos VM base da Microsoft combinados com a Configuração de Estado Desejada da Automação Azure podem ajudar a cumprir e manter os requisitos de segurança.

* [Informações sobre como descarregar o modelo VM](/previous-versions/azure/virtual-machines/windows/download-template)

* [Informação sobre a criação de modelos do Gestor de Recursos Azure](../virtual-machines/windows/ps-template.md)

* [Como carregar um VM VHD personalizado para Azure](/azure-stack/operator/azure-stack-add-vm-image)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure, utilize Azure DevOps ou Azure Repos para armazenar e gerir o seu código de forma segura.

* [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow)

* [Documentação de Azure Repos](/azure/devops/repos/index)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Se utilizar imagens personalizadas, utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para garantir que apenas os utilizadores autorizados possam aceder às imagens. Para imagens de contentores, guarde-as no Registo do Contentor Azure e aproveite o Azure RBAC para garantir que apenas os utilizadores autorizados possam aceder às imagens.

O papel de Contribuinte da Data Factory pode ser usado para criar e gerir fábricas de dados, bem como recursos infantis dentro delas.

* [Compreender Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Compreender O RBAC de Azure para o Registo de Contentores](../container-registry/container-registry-roles.md)

* [Como configurar o Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

* [Funções e permissões do Azure Data Factory](./concepts-roles-permissions.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.DataFactory" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

**Orientação**: Esta recomendação pode aplicar-se se o seu tempo de funcionamento de integração estiver a funcionar numa Máquina Virtual Azure. A Azure Automation State Configuration é um serviço de gestão de configuração para nós de configuração estatal desejada (DSC) em qualquer datacenter de nuvem ou no local. Permite a escalabilidade através de milhares de máquinas de forma rápida e fácil a partir de um local central e seguro. Pode facilmente embarcar máquinas, atribuí-las configurações declarativas e ver relatórios que mostrem a conformidade de cada máquina com o estado pretendido especificado.

* [Máquinas de embarque para gestão por Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.DataFactory" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a Política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para os seus recursos Azure.

* [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Esta recomendação pode aplicar-se se o seu tempo de funcionamento de integração estiver a funcionar numa Máquina Virtual Azure. A Azure Automation State Configuration é um serviço de gestão de configuração para nós de configuração estatal desejada (DSC) em qualquer datacenter de nuvem ou no local. Permite a escalabilidade através de milhares de máquinas de forma rápida e fácil a partir de um local central e seguro. Pode facilmente embarcar máquinas, atribuí-las configurações declarativas e ver relatórios que mostrem a conformidade de cada máquina com o estado pretendido especificado.

* [Máquinas de embarque para gestão por Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e garantir uma gestão secreta para as suas aplicações em nuvem.

Também pode armazenar credenciais ou valores secretos num Cofre de Chaves Azure e usá-los durante a execução do gasoduto para passar às suas atividades. Certifique-se de que a eliminação suave está ativada.

* [Como integrar-se com identidades geridas aZure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Como criar um Cofre-Chave](../key-vault/secrets/quick-create-portal.md)

* [Como autenticar para o Cofre de Chaves](../key-vault/general/authentication.md)

* [Como atribuir uma política de acesso ao Cofre de Chaves](../key-vault/general/assign-access-policy-portal.md)

* [Utilizar segredos do Azure Key Vault em atividades de pipeline](./how-to-use-azure-key-vault-secrets-pipeline-activities.md)

* [Soft-delete no Cofre da Chave Azure](../key-vault/general/soft-delete-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Ao criar uma fábrica de dados, pode ser criada uma identidade gerida juntamente com a criação de fábrica. A identidade gerida é uma aplicação gerida registada no Azure Ative Directory, e representa esta fábrica de dados específica.

* [Identidade gerida para Azure Data Factory](./data-factory-service-identity.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.

* [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte [o controlo de segurança: defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure, pode utilizar o Microsoft Antimalware para Azure Windows Virtual Machines para monitorizar e defender continuamente os seus recursos.

* [Como configurar o Microsoft Antimalware para serviços em nuvem e máquinas virtuais](../security/fundamentals/antimalware.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure App Service), no entanto não funciona com o seu conteúdo.

Pré-digitalizar quaisquer ficheiros que sejam enviados para recursos Azure não computados, tais como App Service, Data Lake Storage, Blob Storage, etc.

Utilize a deteção de ameaças do Azure Security Center para detetar malware enviado para contas de armazenamento.

* [Compreenda o Antimalware da Microsoft para serviços em nuvem Azure e máquinas virtuais](../security/fundamentals/antimalware.md)

* [Compreenda a deteção de ameaças do Azure Security Center para serviços de dados](../security-center/azure-defender.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Quando implementado, o Microsoft Antimalware for Azure instalará automaticamente as atualizações mais recentes de assinatura, plataforma e motor por predefinição. Siga as recomendações no Azure Security Center: "Compute &amp; Apps" para garantir que todos os pontos finais estão atualizados com as assinaturas mais recentes. O Sistema operativo Windows pode ser ainda protegido com segurança adicional para limitar o risco de ataques baseados em vírus ou malware com o serviço de Proteção avançada de ameaças do Microsoft Defender que se integra com o Azure Security Center.

* [Como implementar o Microsoft Antimalware para serviços em nuvem azure e máquinas virtuais](../security/fundamentals/antimalware.md)

* [Proteção Avançada Contra Ameaças do Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [o controlo de segurança: recuperação de dados.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Se estiver a executar o seu Tempo de Funcionamento de Integração numa Máquina Virtual Azure (VM), ative a cópia de segurança do Azure e configuure o VM, bem como o período de frequência e retenção desejado para cópias de segurança automáticas.

Para qualquer uma das suas lojas de dados, consulte a linha de base de segurança desse serviço para obter recomendações sobre como realizar cópias de segurança regulares e automatizadas.

* [Uma visão geral do backup Azure VM](../backup/backup-azure-vms-introduction.md)

* [Apoie um VM Azure a partir das definições de VM](../backup/backup-azure-vms-first-look-arm.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure (VM), ative o Azure Backup e o Target Azure VMs, bem como os períodos de frequência e retenção pretendidos. Chaves geridas pelo cliente de reserva dentro do Cofre da Chave Azure.

Para qualquer uma das suas lojas de dados, consulte a linha de base de segurança desse serviço para obter recomendações sobre como realizar cópias de segurança regulares e automatizadas.

* [Uma visão geral do backup Azure VM](../backup/backup-azure-vms-introduction.md)

* [Como backup chaves chave cofre em Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure, certifique-se de que executa periodicamente a restauração de dados de conteúdos dentro da Cópia de Segurança Azure. Se necessário, o teste restabelece o conteúdo a um VLAN isolado. Teste periodicamente a restauração de chaves geridas pelo cliente.

Para qualquer uma das suas lojas de dados, consulte a linha de segurança desse serviço para obter orientações sobre a validação de cópias de segurança.

* [Como recuperar ficheiros da cópia de segurança da Azure Virtual Machine](../backup/backup-azure-restore-files-from-vm.md)

* [Como restaurar chaves chave do cofre em Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Se estiver a executar o seu Tempo de Execução de Integração numa Máquina Virtual Azure (VM) e tiver apoiado esse VM com Azure Backup, o seu VM está encriptado em repouso com encriptação do serviço de armazenamento (SSE). O Azure Backup também pode fazer backup de VMs Azure que são encriptados usando Azure Disk Encryption. A encriptação do disco Azure integra-se com chaves de encriptação BitLocker (BEKs), que são salvaguardadas num cofre-chave como segredos. A encriptação do disco Azure também se integra com chaves de encriptação chave Azure Key Vault (KEKs). Ativar Soft-Delete no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa.

* [Excluir suave para VMs](../backup/backup-azure-security-feature-cloud.md)

* [Descrição geral da eliminação recuperável do Azure Key Vault](../key-vault/general/soft-delete-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controlo de segurança: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

* [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md)

* [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

* [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

* [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

* [Como configurar a exportação contínua](../security-center/continuous-export.md)

* [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

* [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte [o controlo de segurança: Testes de penetração e exercícios de equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação:** 

* [Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Você pode encontrar mais informações sobre a estratégia da Microsoft e execução de Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridas pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)