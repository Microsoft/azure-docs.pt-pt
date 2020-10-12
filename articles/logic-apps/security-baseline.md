---
title: Linha de base de segurança Azure para Apps Lógicas
description: A linha de base de segurança Logic Apps fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: logic-apps
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 16ee5fb59741d57f47083a0c5db852872ceb91d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89296095"
---
# <a name="azure-security-baseline-for-logic-apps"></a>Linha de base de segurança Azure para Apps Lógicas

A Linha de Base de Segurança Azure para Aplicações Lógicas contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../security/benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte [a visão geral da Azure Security Baselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte o [Azure Security Benchmark: Segurança da rede](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Os conectores que funcionam no serviço "global", multi-inquilino Logic Apps são implementados e geridos pela Microsoft. Estes conectores fornecem gatilhos e ações para aceder a serviços na nuvem, sistemas no local, ou ambos, incluindo Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint, entre outros. Pode utilizar a etiqueta de serviço AzureConnectors ao especificar regras no seu grupo de segurança de rede ou Azure Firewall para permitir o acesso a recursos relacionados.

Para aplicações lógicas que necessitem de acesso direto aos recursos numa rede virtual Azure, pode criar um ambiente de serviço de integração (ISE) onde pode construir, implementar e executar as suas aplicações lógicas em recursos dedicados. Algumas redes virtuais Azure utilizam pontos finais privados (Azure Private Link) para fornecer acesso a serviços Azure PaaS, tais como Azure Storage, Azure Cosmos DB, Azure SQL Database, serviços de parceiros ou serviços de clientes que estão hospedados no Azure. Se as suas aplicações lógicas precisam de acesso a redes virtuais que usam pontos finais privados, tem de criar, implementar e executar essas aplicações lógicas dentro de um ISE.

Ao criar o ise, pode optar por utilizar os pontos finais de acesso interno ou externo. A sua seleção determina se o pedido ou webhook dispara em aplicações lógicas no seu ISE pode receber chamadas de fora da sua rede virtual. Os pontos finais de acesso interno e externo também afetam se pode ver o histórico de execução da sua aplicação lógica, incluindo as entradas e saídas para uma corrida, de dentro ou de fora da sua rede virtual.

Certifique-se de que todas as implementações de sub-redes de rede virtuais relacionadas com o seu ISE têm um grupo de segurança de rede aplicado com controlos de acesso à rede específicos das portas e fontes fidedignas da sua aplicação. Quando implementar as suas aplicações lógicas num ISE, utilize o Private Link. O Azure Private Link permite-lhe aceder aos Serviços Azure PaaS e aos serviços de parceiros do Azure PaaS hospedados num ponto final privado na sua rede virtual. Em alternativa, se tiver um caso de utilização específico, pode cumprir este requisito implementando o Azure Firewall. Para ajudar a reduzir a complexidade ao configurar regras de segurança, utilize etiquetas de serviço que representem grupos de prefixos de endereço IP para um serviço Azure específico.

- [Compreender conectores para aplicações lógicas](../connectors/apis-list.md)

- [Compreender tags de serviço em Azure](../virtual-network/service-tags-overview.md)

- [Compreender o acesso aos recursos da Rede Virtual Azure a partir de Azure Logic Apps utilizando ambientes de serviços de integração (ISEs)](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Compreender pontos finais de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md)

- [Compreenda a ligação privada Azure](../private-link/private-link-overview.md)

- [Compreender o acesso ao ponto final do ISE](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Como implantar e configurar firewall Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como permitir o acesso ao ISE](connect-virtual-network-vnet-isolated-environment.md#enable-access-for-ise)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Se executar aplicações lógicas num ambiente de serviço de integração (ISE) que utiliza um ponto de acesso externo, pode utilizar um grupo de segurança de rede (NSG) para reduzir o risco de exfiltração de dados. Ativar os registos de fluxo NSG e enviar registos para uma Conta de Armazenamento Azure para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Compreender o acesso ao ponto final do ISE](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Se a sua aplicação lógica utilizar um gatilho baseado em pedidos, que recebe chamadas ou pedidos de entrada, como o trigger 'Pedido' ou Webhook, pode limitar o acesso para que apenas os clientes autorizados possam ligar para a sua aplicação lógica.

Se estiver a executar aplicações lógicas num ambiente de serviço de integração (ISE), ative o DDoS Protection Standard na rede virtual associada ao ISE para se proteger contra ataques de DDoS. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

Implemente o Azure Firewall em cada um dos limites de rede da organização com a Threat Intelligence habilitada e configurada a "alertar e negar" para tráfego de rede malicioso.

Utilize o Centro de Segurança Azure Apenas no Tempo O acesso à rede de configuração de NSGs para limitar a exposição dos pontos finais a endereços IP aprovados por um período limitado.

Use o Hardening de Rede Adaptável do Centro de Segurança Azure para recomendar configurações NSG que limitem portas e IPs de origem com base na inteligência real de tráfego e ameaça.

- [Como garantir chamadas de entrada para apps lógicas](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [Como restringir os endereços IP de entrada](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

- [Como configurar a proteção DDoS](../virtual-network/manage-ddos-protection.md)

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](/azure/security-center/security-center-alerts-service-layer)

- [Compreender o Hardenive de Rede Adaptável do Centro de Segurança Azure](../security-center/security-center-adaptive-network-hardening.md)

- [Compreender o Centro de Segurança Azure Mesmo no Tempo Controlo de Acesso à Rede](../security-center/security-center-just-in-time.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Se for executado aplicativos lógicos num ambiente de serviço de integração (ISE) que utiliza um ponto de acesso externo, pode utilizar um grupo de segurança de rede (NSG) para reduzir o risco de exfiltração de dados. Ativar os registos de fluxo NSG e enviar registos para uma Conta de Armazenamento Azure para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Para fornecer mais proteção e informações sobre o tráfego de rede, pode consultar os registos de acesso, que só são gerados se os tiver ativado em cada instância do Gateway de aplicação. Pode utilizar este registo para visualizar padrões de acesso do Gateway de Aplicação e analisar informações importantes. Isto inclui o IP do chamador, URL solicitado, latência de resposta, código de devolução e bytes dentro e fora.

Caso contrário, pode aproveitar uma solução de terceiros do mercado para satisfazer este requisito.

- [Compreender o acesso ao ponto final do ISE](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Como integrar a Gestão da API num VNET interno com Gateway de aplicação](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Como entender os registos de acesso da WAF](../web-application-firewall/ag/web-application-firewall-logs.md#access-log)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Selecione uma oferta do Azure Marketplace que suporta a funcionalidade IDS/IPS com capacidades de inspeção de carga útil.  Se a deteção e/ou prevenção de intrusões baseada na inspeção de carga útil não for um requisito, a Azure Firewall com Threat Intelligence pode ser usada. A filtragem baseada em inteligência Azure Firewall Threat pode alertar e negar tráfego de e para endereços ip maliciosos conhecidos e domínios. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft.

Implemente a solução de firewall à sua escolha em cada um dos limites de rede da sua organização para detetar e/ou negar tráfego malicioso.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como configurar alertas com a Azure Firewall](../firewall/threat-intel.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Se estiver a executar aplicações lógicas num ambiente de serviço de integração (ISE), implemente o Azure Application Gateway.

- [Como integrar a Gestão da API num VNET interno com Gateway de Aplicação](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Como configurar o Gateway de aplicações para utilizar HTTPS](../application-gateway/create-ssl-portal.md) 

- [Compreenda o equilíbrio de carga da camada 7 com os gateways de aplicações web Azure](../application-gateway/overview.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos que necessitem de acesso às suas instâncias Azure Logic Apps, utilize tags de serviço de rede virtuais para definir controlos de acesso à rede em grupos de segurança de rede ou Azure Firewall. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, LogicApps, LogicAppsManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Para mais informações sobre a utilização de tags de serviço](../virtual-network/service-tags-overview.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede relacionados com as suas instâncias de Apps Azure Logic com Azure Policy. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.Logic" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede das suas aplicações Azure Logic. Pode também utilizar definições políticas incorporadas, tais como:

- Os registos de diagnóstico em Aplicações Lógicas devem ser ativados

- A Norma de Proteção DDoS deve ser ativada

Também pode utilizar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlo de acesso baseado em funções (RBAC) e políticas, numa única definição de planta. Aplique facilmente o projeto em novas subscrições e ambientes, e afinar o controlo e a gestão através da versão.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Para regras individuais de NSG, utilize o campo "Descrição" para especificar a necessidade do negócio e/ou a duração (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições de Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com Tags e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas Tags.

- [Como criar e usar Tags](/azure/azure-resource-manager/resource-group-using-tags)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Lista de definições de política Azure para apps lógicas](/azure/logic-apps/policy-samples)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o registo de Atividade Azure para monitorizar as configurações de recursos de rede e detetar alterações para recursos de rede relacionados com as suas instâncias de Apps Lógicas Azure. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](/azure/azure-monitor/platform/activity-log-view)

- [Como criar alertas no Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registar e monitorizar](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém a fonte de tempo utilizada para os recursos Azure, como as Aplicações Lógicas Azure para os timetamps nos registos.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Para obter informações mais ricas sobre as suas aplicações lógicas durante o tempo de execução, pode configurar e utilizar registos do Azure Monitor para gravar e armazenar informações sobre dados e eventos de tempo de execução, tais como eventos de desencadeamento, eventos de execução e eventos de ação num espaço de trabalho do Log Analytics. O Azure Monitor ajuda-o a monitorizar os ambientes na cloud e no local para que possa manter mais facilmente a disponibilidade e desempenho. Ao utilizar registos do Azure Monitor, pode criar consultas de registo que o ajudam a recolher e rever estas informações. Também pode utilizar estes dados de diagnóstico com outros serviços Azure, como o Azure Storage e o Azure Event Hubs.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Como ativar definições de diagnóstico para registo de atividades azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Como configurar registos do Azure Monitor e recolher dados de diagnóstico para apps Azure Logic](monitor-logic-apps-log-analytics.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Para obter informações mais ricas sobre as suas aplicações lógicas durante o tempo de execução, pode configurar e utilizar registos do Azure Monitor para gravar e armazenar informações sobre dados e eventos de tempo de execução, tais como eventos de desencadeamento, eventos de execução e eventos de ação num espaço de trabalho do Log Analytics. O Azure Monitor ajuda-o a monitorizar os ambientes na cloud e no local para que possa manter mais facilmente a disponibilidade e desempenho. Ao utilizar registos do Azure Monitor, pode criar consultas de registo que o ajudam a recolher e rever estas informações. Também pode utilizar estes dados de diagnóstico com outros serviços Azure, como o Azure Storage e o Azure Event Hubs.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Como ativar definições de diagnóstico para registo de atividades azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Como configurar registos do Azure Monitor e recolher dados de diagnóstico para apps Azure Logic](monitor-logic-apps-log-analytics.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Depois de criar e executar uma aplicação lógica, pode verificar o estado de execução da aplicação lógica, executar o histórico, desencadear o histórico e o desempenho. Para monitorização de eventos em tempo real e depuração mais rica, confiem de diagnósticos para a sua aplicação lógica utilizando registos do Azure Monitor. Este serviço Azure ajuda-o a monitorizar os ambientes da nuvem e do local para que possa manter mais facilmente a sua disponibilidade e desempenho. Você pode então encontrar e ver eventos, tais como eventos de desencadeamento, eventos de execução e eventos de ação. Ao armazenar estas informações nos registos do Azure Monitor, pode criar consultas de registo que o ajudam a encontrar e analisar esta informação. Também pode utilizar estes dados de diagnóstico com outros serviços Azure, como o Azure Storage e o Azure Event Hubs.

No Azure Monitor, detenha o período de retenção de registos para registos associados às suas instâncias de Apps Azure Logic de acordo com os regulamentos de conformidade da sua organização.

- [Como monitorizar o estado de execução, rever o histórico do gatilho e configurar alertas para a Azure Logic Apps](monitor-logic-apps.md)

- [Como definir parâmetros de retenção de registos](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Para configurar o registo para a sua aplicação lógica, pode ativar o Log Analytics quando criar a sua aplicação lógica, ou pode instalar a solução De Gestão de Aplicações Lógicas no seu espaço de trabalho Log Analytics para aplicações lógicas existentes. Esta solução fornece informações agregadas para a sua aplicação lógica e inclui detalhes específicos como estado, tempo de execução, estado de resubmissão e IDs de correlação. Em seguida, para permitir o registo e a criação de consultas para esta informação, crie registos do Azure Monitor.

Também pode ativar as definições de diagnóstico do Registo de Atividade do Azure e enviar os registos para um espaço de trabalho do Log Analytics. Realize consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights baseados nos Dados de Registo de Atividade que possam ter sido recolhidos para apps Azure Logic.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Como configurar registos do Azure Monitor e recolher dados de diagnóstico para apps Azure Logic](monitor-logic-apps-log-analytics.md)

- [Como ativar definições de diagnóstico para registo de atividades azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Como recolher e analisar registos de atividades do Azure no Log Analytics no Azure Monitor](/azure/azure-monitor/platform/activity-log-collect)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o Azure Security Center com o Log Analytics para monitorizar e alertar sobre a atividade anómala encontrada em registos e eventos de segurança.

Em alternativa, pode ativar e a bordo dados para a Azure Sentinel.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como gerir alertas no Centro de Segurança Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar nos dados de registo de registo de registos de registos](../azure-monitor/learn/tutorial-response.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Não aplicável; A Azure Logic Apps não processa ou produz registos relacionados com anti-malware.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Não aplicável; A Azure Logic Apps não processa ou produz registos relacionados com DNS.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e controlo de acessos](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Azure Ative Directory (AD) tem funções incorporadas que devem ser explicitamente atribuídas e que são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Para aceder facilmente a outros recursos protegidos pelo Azure Ative Directory (Azure AD) e autenticar a sua identidade sem iniciar sessão, a sua aplicação lógica pode utilizar uma identidade gerida (anteriormente Identidade de Serviço Gerida ou MSI), em vez de credenciais ou segredos. O Azure gere esta identidade automaticamente e ajuda a proteger as credenciais, uma vez que não precisa de introduzir segredos nem proceder a sua rotação.

Cada ponto final de pedido de uma aplicação lógica tem uma Assinatura de Acesso Partilhado (SAS) no URL do ponto final. Se partilhar o URL do ponto final para um gatilho baseado em pedidos com outras partes, pode gerar URLs de retorno que usam chaves específicas e têm datas de validade. Desta forma, pode enrolar perfeitamente as teclas ou restringir o acesso a desencadear a sua aplicação lógica com base num período de tempo específico.

- [Como obter um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Autenticar o acesso aos recursos do Azure utilizando identidades geridas em Azure Logic Apps](create-managed-service-identity.md)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Como garantir o acesso e os dados em Azure Logic Apps usando SAS](logic-apps-securing-a-logic-app.md#sas)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: Azure Ative Directory e Azure Logic Apps não têm o conceito de senhas padrão.

Se estiver a ser utilizada a autenticação básica, terá de especificar um nome de utilizador e uma palavra-passe. Ao criar estas credenciais, certifique-se de que configura uma palavra-passe forte para a autenticação.

Se estiver a utilizar a Infraestrutura como Código, evite armazenar senhas em código e, em vez disso, utilize o Cofre de Chaves Azure para armazenar e recuperar credenciais.

- [Como proteger e aceder a dados em Apps Lógicas](logic-apps-securing-a-logic-app.md)

- [Como definir e recuperar um segredo do Cofre da Chave Azure](../key-vault/secrets/quick-create-portal.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou políticas de Azure incorporadas, tais como:

- Deve haver mais de um proprietário atribuído à sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

- [Como utilizar o Centro de Segurança Azure para monitorizar a identidade e o acesso (Pré-visualização)](../security-center/security-center-identity-access.md)

- [Como usar a Política Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: Utilize um registo de aplicações Azure (principal serviço) para recuperar um token que pode ser usado para interagir com os seus cofres dos Serviços de Recuperação através de chamadas API.

Muitos conectores também exigem que crie primeiro uma ligação ao serviço ou sistema alvo e forneça credenciais de autenticação ou outros detalhes de configuração antes de poder usar um gatilho ou ação na sua aplicação lógica. Por exemplo, deve autorizar uma ligação a uma conta do Twitter para aceder a dados ou para publicar em seu nome.

Para conectores que utilizam OAuth Azure Ative Directory (Azure AD), criar uma ligação significa iniciar sessão no serviço, como o Office 365, Salesforce ou GitHub, onde o seu token de acesso é encriptado e armazenado de forma segura numa loja secreta Azure. Outros conectores, como FTP e SQL, requerem uma ligação que tenha detalhes de configuração, tais como o endereço do servidor, nome de utilizador e senha. Estes detalhes de configuração de ligação também são encriptados e armazenados de forma segura.

- [Como chamar Azure REST APIs](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Como registar a sua candidatura ao cliente com a Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Fluxo de trabalho despoleta informações da API](/rest/api/logic/workflowtriggers)

- [Compreender a configuração do conector](../connectors/apis-list.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Ativar o Azure Ative Directory (AD) Autenticação multi-factor (MFA) e seguir as recomendações do Azure Security Center Identity and Access Management.

- [Como permitir o MFA em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Utilizar estações de trabalho seguras e geridas pelo Azure para tarefas administrativas

**Orientação**: Utilize estações de trabalho de acesso privilegiada (PAW) com autenticação multi-factor (MFA) configuradas para iniciar sessão e configurar recursos Azure.

- [Saiba mais sobre estações de trabalho de acesso privilegiado](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Como permitir o MFA em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize o Azure Ative Directory (AD) Gestão de Identidade Privilegiada (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente.

Além disso, utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

- [Como implementar gestão de identidade privilegiada (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Compreender deteções de risco Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso ao portal Azure a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

Além disso, cada ponto final de pedido de uma aplicação lógica tem uma Assinatura de Acesso Partilhado (SAS) no URL do ponto final. Pode restringir a sua aplicação lógica para aceitar pedidos apenas a partir de determinados endereços IP.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

- [Entenda como restringir endereços IP de entrada em Apps Lógicas](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (AD) como o sistema central de autenticação e autorização para as suas instâncias Azure Logic Apps. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

Quando houver suporte em Aplicações Lógicas, utilize uma identidade gerida para aceder facilmente a outros recursos que são protegidos pelo Azure Ative Directory (Azure AD) e autenticar a sua identidade sem iniciar sessão, em vez de credenciais ou segredos. O Azure gere esta identidade automaticamente e ajuda a proteger as credenciais, uma vez que não precisa de introduzir segredos nem proceder a sua rotação.

A Azure Logic Apps suporta identidades geridas atribuídas pelo sistema e atribuídas pelo utilizador. A aplicação lógica pode utilizar a identidade atribuída pelo sistema ou uma única identidade atribuída pelo utilizador, que pode partilhar num grupo de aplicações lógicas, mas não ambas. Atualmente, apenas gatilhos e ações específicas suportam identidades geridas, não conectores geridos ou ligações, por exemplo:

-  HTTP

-  Funções do Azure

-  API Management do Azure

-  Serviços de Aplicações do Azure 

- [Como criar e configurar uma instância AD Azure](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autenticar o acesso aos recursos do Azure utilizando identidades geridas em Azure Logic Apps](create-managed-service-identity.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (AD) fornece registos para o ajudar a descobrir contas velhas. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado. 

- [Compreenda a Azure AD reportando](/azure/active-directory/reports-monitoring/)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Utilize o Azure Ative Directory (AD) como o sistema central de autenticação e autorização para as suas instâncias Azure Logic Apps. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

Tem acesso a atividade de login AD, fontes de registo de auditoria e eventos de risco, que lhe permitem integrar-se com a Azure Sentinel ou com um SIEM de terceiros.

Pode agilizar este processo criando definições de diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas de registo pretendidos no Log Analytics.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize funcionalidades de Proteção de Risco e Identidade Azure AD para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Você também pode ingerir dados em Azure Sentinel para mais investigação. 

- [Como ver a Azure AD a entrar em risco](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Atualmente não disponível; O Lockbox do cliente ainda não está suportado para aplicações Azure Logic.

- [Lista de serviços suportados pelo Cliente Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Proteção de dados](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Os conectores que funcionam no serviço "global", multi-inquilino Logic Apps são implementados e geridos pela Microsoft. Estes conectores fornecem gatilhos e ações para aceder a serviços na nuvem, sistemas no local, ou ambos, incluindo Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint, entre outros.

Para aplicações lógicas que necessitem de acesso direto aos recursos numa rede virtual Azure, pode criar um ambiente de serviço de integração (ISE) onde pode construir, implementar e executar as suas aplicações lógicas em recursos dedicados. Algumas redes virtuais Azure utilizam pontos finais privados (Azure Private Link) para fornecer acesso a serviços Azure PaaS, tais como Azure Storage, Azure Cosmos DB, ou Azure SQL Database, serviços de parceiros ou serviços de clientes que estão hospedados no Azure. Se as suas aplicações lógicas precisam de acesso a redes virtuais que usam pontos finais privados, tem de criar, implementar e executar essas aplicações lógicas dentro de um ISE.

Ao criar o ise, pode optar por utilizar os pontos finais de acesso interno ou externo. A sua seleção determina se o pedido ou webhook dispara em aplicações lógicas no seu ISE pode receber chamadas de fora da sua rede virtual.

Adicionalmente, implementar o isolamento utilizando subscrições e grupos de gestão separados para domínios de segurança individuais, tais como o tipo de ambiente e o nível de sensibilidade aos dados. Pode restringir o nível de acesso aos seus recursos Azure que as suas aplicações e ambientes empresariais exigem. Pode controlar o acesso aos recursos da Azure através do controlo de acesso baseado em funções do Azure Ative Directory.

- [Compreender conectores para aplicações lógicas](../connectors/apis-list.md)

- [Acesso aos recursos da Rede Virtual Azure a partir de Azure Logic Apps utilizando ambientes de serviços de integração (ISEs)](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Como criar subscrições adicionais do Azure](/azure/billing/billing-create-subscription) 

- [Como criar Grupos de Gestão](/azure/governance/management-groups/create) 

- [Como criar e usar tags](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Atualmente não disponível; as funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para aplicações lógicas Azure.

Aproveite uma solução de terceiros do Azure Marketplace em perímetros de rede que monitoriza a transferência não autorizada de informações sensíveis e bloqueia tais transferências enquanto alerta os profissionais de segurança da informação. 

A Microsoft gere a infraestrutura subjacente às Apps Azure Logic e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

- [Compreender a proteção de dados do cliente em Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Criptografe todas as informações sensíveis em trânsito. Nas Azure Logic Apps, todos os dados durante uma aplicação lógica executada são encriptados durante o trânsito utilizando a Transport Layer Security (TLS) e em repouso. Quando vê o histórico de execução da sua aplicação lógica, as Aplicações Lógicas autenticam o seu acesso e, em seguida, fornecem links para as entradas e saídas para os pedidos e respostas para cada execução. No entanto, para ações que lidam com quaisquer palavras-passe, segredos, chaves ou outras informações sensíveis, pretende impedir que outros o visualizam e acedam a esses dados. Por exemplo, se a sua aplicação lógica obtiver um segredo do Azure Key Vault para usar ao autenticar uma ação HTTP, pretende esconder esse segredo da vista.

O gatilho do Pedido suporta apenas a Segurança da Camada de Transporte (TLS) 1.2 para pedidos de entrada. Certifique-se de que qualquer cliente que se conecte aos seus recursos Azure seja capaz de negociar TLS 1.2 ou superior. Chamadas de saída utilizando o suporte de conector HTTP Transport Layer Security (TLS) 1.0, 1.1 e 1.2. 

Siga as recomendações do Azure Security Center para encriptação em repouso e encriptação em trânsito, sempre que aplicável.

- [Acesso seguro e dados em Azure Logic Apps - Chamadas de entrada para gatilhos baseados em pedidos](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [Acesso seguro e dados em Azure Logic Apps - Chamadas de saída para outros serviços e sistemas](logic-apps-securing-a-logic-app.md#secure-outbound-requests)

- [Compreender a encriptação em trânsito com Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Compreender encriptação de dados em repouso com Azure](../security/fundamentals/encryption-atrest.md)

- [Confiem chaves geridas pelo cliente para encriptar dados em repouso para ambientes de serviço de integração (ISEs) em Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Em Azure Logic Apps, muitos gatilhos e ações têm configurações que pode permitir garantir entradas, saídas ou ambos ocultando esses dados a partir do histórico de execução de uma aplicação lógica.

A Microsoft gere a infraestrutura subjacente às Apps Azure Logic e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

- [Acesso seguro para executar dados do histórico](logic-apps-securing-a-logic-app.md#access-to-run-history-data)

- [Compreender a proteção de dados do cliente em Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Utilizar o controlo de acesso baseado em funções para controlar o acesso aos recursos

**Orientação**: Só é possível permitir que utilizadores ou grupos específicos executem tarefas específicas, tais como gerir, editar e visualizar aplicações lógicas. Para controlar as suas permissões, utilize o Azure Role-Based Access Control (RBAC) para que possa atribuir funções personalizadas ou incorporadas aos membros da sua subscrição Azure:

- Logic App Contributor: Permite-lhe gerir aplicações lógicas, mas não pode alterar o acesso às mesmos.
- Operador de aplicativos logicos: Permite-lhe ler, ativar e desativar aplicações lógicas, mas não pode editá-las ou atualizá-las.

Para evitar que outros mudem ou apaguem a sua aplicação lógica, pode utilizar o Azure Resource Lock. Esta capacidade impede que outros mudem ou apaguem os recursos produtivos.

- [Acesso seguro às operações da Azure Logic Apps](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional. A Microsoft gere a infraestrutura subjacente às Apps Azure Logic e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

- [Proteção de dados do cliente Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: A azure Logic Apps depende do Azure Storage para armazenar e encriptar automaticamente os dados em repouso. Esta encriptação protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade. Por predefinição, o Azure Storage utiliza chaves geridas pela Microsoft para encriptar os seus dados.

Quando criar um ambiente de serviço de integração (ISE) para hospedar as suas aplicações lógicas, e pretender mais controlo sobre as chaves de encriptação utilizadas pelo Azure Storage, pode configurar, usar e gerir a sua própria chave utilizando o Azure Key Vault. Esta capacidade também é conhecida como "Bring Your Own Key" (BYOK), e a sua chave é chamada de "chave gerida pelo cliente".

- [Criptografe os dados em repouso para ambientes de serviço de integração em Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de atividade Azure para criar alertas para quando as alterações ocorrem nas Apps Azure Logic, bem como outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte o [Azure Security Benchmark: Gestão de vulnerabilidades](/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Não aplicável; A Microsoft realiza a gestão de vulnerabilidades nos sistemas subjacentes que suportam as Aplicações Lógicas Azure.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implementar solução automatizada de gestão de patchs para títulos de software de terceiros

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Não aplicável; A Microsoft realiza a gestão de vulnerabilidades nos sistemas subjacentes que suportam as Aplicações Lógicas Azure.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Não aplicável; A Microsoft realiza a gestão de vulnerabilidades nos sistemas subjacentes que suportam as Aplicações Lógicas Azure.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e gestão de ativos.](/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua(s) subscrição( s).  Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como os recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado que crie e utilize recursos do Azure Resource Manager para a frente.

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

- [Como criar e usar tags](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear os recursos do Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Além disso, utilize a Azure Policy para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como criar subscrições adicionais do Azure](/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gestão](/azure/governance/management-groups/create)

- [Como criar e usar Tags](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Crie um inventário de recursos Azure aprovados (como conectores) e software aprovado para recursos de computação de acordo com as suas necessidades organizacionais.

Nota: Devido aos dados e políticas de privacidade da Google, só pode utilizar o conector gmail com serviços aprovados pela Google. Esta situação está a evoluir e poderá afetar outros conectores da Google no futuro.

- [Lista de todos os conectores de Apps Lógicas](/connectors/connector-reference/connector-reference-logicapps-connectors)

- [Compreender problemas e limitações para conectores gmail](/connectors/gmail/#known-issues-and-limitations)

- [Mais informações sobre a política de privacidade do Google](../connectors/connectors-google-data-security-privacy-policy.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na sua(s) subscrição. 

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição.  Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Os recursos relacionados com as suas Aplicações Lógicas que são necessárias para operações comerciais, mas que podem incorrer em maior risco para a organização, devem ser isolados dentro da sua própria máquina virtual e/ou rede virtual e suficientemente protegidos com um Azure Firewall ou Grupo de Segurança de Rede.

As Aplicações Lógicas que são necessárias para operações comerciais, mas que podem incorrer em maior risco para a organização, devem ser isoladas sempre que possível através de grupos de recursos separados com permissões específicas e limites de RBAC.

- [Como criar uma rede virtual](../virtual-network/quick-create-portal.md) 

- [Como criar um NSG com um config de segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Como criar Grupos de Gestão](/azure/governance/management-groups/create) 

- [Como garantir o acesso a Apps Lógicas via RBAC](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração segura](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para as suas aplicações lógicas Azure com a Política Azure. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Logic" para criar políticas personalizadas para auditar ou impor a configuração das suas instâncias de Aplicações Lógicas. Por exemplo, pode bloquear que outros criem ou utilizem ligações a recursos onde pretende restringir o acesso.

Além disso, o Azure Resource Manager tem a capacidade de exportar o modelo em JavaScript Object Notation (JSON), que deve ser revisto para garantir que as configurações cumprem /excedem os requisitos de segurança para a sua organização.

Além disso, utilize parâmetros seguros para proteger dados e segredos sensíveis.

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Ligações de blocos criadas por conectores em Azure Logic Apps](block-connections-connectors.md)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Como implementar modelos de Gestor de Recursos Azure para apps Azure Logic](logic-apps-deploy-azure-resource-manager-templates.md)

- [Compreender parâmetros de ação seguros](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [Recomendações de segurança para parâmetros](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

Defina e implemente configurações de segurança padrão para as suas aplicações lógicas Azure com a Política Azure. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Logic" para criar políticas personalizadas para auditar ou impor a configuração das suas instâncias de Aplicações Lógicas. Por exemplo, pode bloquear que outros criem ou utilizem ligações a recursos onde pretende restringir o acesso.

Além disso, o Azure Resource Manager tem a capacidade de exportar o modelo em JavaScript Object Notation (JSON), que deve ser revisto para garantir que as configurações cumprem /excedem os requisitos de segurança para a sua organização.

Além disso, certifique-se de que protege os dados na história da execução utilizando a obfuscação.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

- [Ligações de blocos criadas por conectores em Azure Logic Apps](block-connections-connectors.md)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Como implementar modelos de Gestor de Recursos Azure para apps Azure Logic](logic-apps-deploy-azure-resource-manager-templates.md)

- [Acesso seguro para executar entradas e saídas de histórico](logic-apps-securing-a-logic-app.md#obfuscate)

- [Acesso seguro às entradas de parâmetros](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [Recomendações de segurança para parâmetros](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure, utilize Azure DevOps ou Azure Repos para armazenar e gerir o seu código de forma segura.

Além disso, o Azure Resource Manager tem a capacidade de exportar o modelo em JavaScript Object Notation (JSON), que deve ser revisto para garantir que as configurações cumprem /excedem os requisitos de segurança para a sua organização.

- [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentação de Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Logic" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use pseudónimos da Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus recursos Azure. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Logic" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a Política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para os seus recursos Azure.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Fixe entradas e saídas na aplicação lógica executar o histórico utilizando a obfuscação. Se implementar em diferentes ambientes, considere parametrizar os valores na definição de fluxo de trabalho da sua aplicação lógica que variam com base nesses ambientes. Desta forma, pode evitar dados codificados através de um modelo de Gestor de Recursos Azure para implementar a sua aplicação lógica, proteger dados sensíveis definindo parâmetros seguros e passar esses dados como entradas separadas através dos parâmetros do modelo utilizando um ficheiro de parâmetros. Pode utilizar o Key Vault para armazenar dados sensíveis e utilizar parâmetros de modelo seguros que recuperem esses valores a partir do Cofre de Chaves durante a sua implantação. Em seguida, pode fazer referência ao cofre-chave e aos segredos no seu ficheiro de parâmetros. 

Quando criar um ambiente de serviço de integração (ISE) para hospedar as suas aplicações lógicas, e pretender mais controlo sobre as chaves de encriptação utilizadas pelo Azure Storage, pode configurar, usar e gerir a sua própria chave utilizando o Azure Key Vault. Esta capacidade também é conhecida como "Bring Your Own Key" (BYOK), e a sua chave é chamada de "chave gerida pelo cliente".

- [Entradas e saídas seguras na história da execução em Azure Logic Apps](logic-apps-securing-a-logic-app.md#obfuscate)

- [Recomendações de segurança para parâmetros](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [Acesso seguro às entradas de parâmetros em Azure Logic Apps](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [Passe valores de parâmetros seguros durante a implementação usando o Cofre da Chave Azure](../azure-resource-manager/templates/key-vault-parameter.md)

- [Confiem chaves geridas pelo cliente para encriptar dados em repouso para ambientes de serviço de integração (ISEs) em Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Para aceder facilmente a outros recursos protegidos pelo Azure Ative Directory (Azure AD) e autenticar a sua identidade sem iniciar sessão, a sua aplicação lógica pode utilizar uma identidade gerida (anteriormente Identidade de Serviço Gerida ou MSI), em vez de credenciais ou segredos. O Azure gere esta identidade automaticamente e ajuda a proteger as credenciais, uma vez que não precisa de introduzir segredos nem proceder a sua rotação.

Atualmente, apenas gatilhos e ações específicas suportam identidades geridas, não conectores geridos ou ligações, por exemplo:

- HTTP
- Funções do Azure
- API Management do Azure
- Serviços de Aplicações do Azure

- [Como autenticar o acesso aos recursos do Azure utilizando identidades geridas em Azure Logic Apps](create-managed-service-identity.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Fixe entradas e saídas na aplicação lógica executar o histórico utilizando a obfuscação. Se implementar em diferentes ambientes, considere parametrizar os valores na definição de fluxo de trabalho da sua aplicação lógica que variam com base nesses ambientes. Desta forma, pode evitar dados codificados através de um modelo de Gestor de Recursos Azure para implementar a sua aplicação lógica, proteger dados sensíveis definindo parâmetros seguros e passar esses dados como entradas separadas através dos parâmetros do modelo utilizando um ficheiro de parâmetros. Pode utilizar o Key Vault para armazenar dados sensíveis e utilizar parâmetros de modelo seguros que recuperem esses valores a partir do Cofre de Chaves durante a sua implantação. Em seguida, pode fazer referência ao cofre-chave e aos segredos no seu ficheiro de parâmetros. 

Também pode implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

- [Entradas e saídas seguras na história da execução em Azure Logic Apps](logic-apps-securing-a-logic-app.md#obfuscate)

- [Recomendações de segurança para parâmetros](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [Acesso seguro às entradas de parâmetros em Azure Logic Apps](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [Passe valores de parâmetros seguros durante a implementação usando o Cofre da Chave Azure](../azure-resource-manager/templates/key-vault-parameter.md)

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte o [Azure Security Benchmark: Malware defense](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizar software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional. O anti-malware da Microsoft é ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Logic Apps), no entanto não funciona com conteúdo do cliente.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Microsoft Anti-malware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Backup), no entanto não é executado no seu conteúdo. 

Pré-digitalizar quaisquer ficheiros que sejam enviados para recursos Azure não computados, tais como App Service, Data Lake Storage, Blob Storage, etc. 

Utilize a deteção de ameaças do Azure Security Center para detetar malware enviado para contas de armazenamento. 

- [Compreenda o Anti-malware da Microsoft para serviços em nuvem azure e máquinas virtuais](../security/fundamentals/antimalware.md)

- [Compreenda a deteção de ameaças do Azure Security Center para serviços de dados](/azure/security-center/security-center-alerts-data-services)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [Azure Security Benchmark: Data Recovery](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: Implementar uma solução de recuperação de desastres (DR) para que possa proteger os dados, restaurar rapidamente os recursos que suportam funções comerciais críticas e manter as operações em funcionamento para manter a continuidade do negócio (BC).

Esta estratégia de recuperação de desastres centra-se na criação da sua aplicação de lógica primária para falhar numa aplicação lógica de standby ou backup num local alternativo onde as Azure Logic Apps também estão disponíveis. Assim, se as primárias sofrerem perdas, perturbações ou falhas, o secundário pode assumir o trabalho. Esta estratégia requer que a sua app de lógica secundária e recursos dependentes já estejam implantados e prontos na localização alternativa.

Além disso, deve expandir a definição subjacente do fluxo de trabalho da sua aplicação lógica para um modelo de Gestor de Recursos Azure. Este modelo define a infraestrutura, recursos, parâmetros e outras informações para o fornecimento e implementação da sua aplicação lógica.

- [Saiba mais sobre continuidade de negócios e recuperação de desastres para Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Como automatizar a implementação de Azure Logic Apps utilizando modelos de Gestor de Recursos Azure](logic-apps-azure-resource-manager-templates-overview.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Implementar uma solução de recuperação de desastres (DR) para que possa proteger os dados, restaurar rapidamente os recursos que suportam funções comerciais críticas e manter as operações em funcionamento para manter a continuidade do negócio (BC).

Esta estratégia de recuperação de desastres centra-se na criação da sua aplicação de lógica primária para falhar numa aplicação lógica de standby ou backup num local alternativo onde as Azure Logic Apps também estão disponíveis. Assim, se as primárias sofrerem perdas, perturbações ou falhas, o secundário pode assumir o trabalho. Esta estratégia requer que a sua app de lógica secundária e recursos dependentes já estejam implantados e prontos na localização alternativa.

Além disso, deve expandir a definição subjacente do fluxo de trabalho da sua aplicação lógica para um modelo de Gestor de Recursos Azure. Este modelo define a infraestrutura, recursos, parâmetros e outras informações para o fornecimento e implementação da sua aplicação lógica.

Cada ponto final de pedido de uma aplicação lógica tem uma Assinatura de Acesso Partilhado (SAS) no URL do ponto final. Se estiver a utilizar o Azure Key Vault para armazenar os seus segredos, certifique-se de cópias de segurança automáticas regulares das suas chaves e URLs.

- [Saiba mais sobre continuidade de negócios e recuperação de desastres para Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Automatize a implementação para apps Azure Logic utilizando modelos de Gestor de Recursos Azure](logic-apps-azure-resource-manager-templates-overview.md)

- [Acesso seguro e dados em Azure Logic Apps usando SAS](logic-apps-securing-a-logic-app.md#sas)

- [Como apoiar chaves do cofre](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: A sua estratégia de recuperação de desastres deve focar-se na criação da sua aplicação de lógica primária para falhar numa aplicação lógica de espera ou backup num local alternativo onde as Apps Azure Logic também estão disponíveis. Assim, se as primárias sofrerem perdas, perturbações ou falhas, o secundário pode assumir o trabalho. Esta estratégia requer que a sua app de lógica secundária e recursos dependentes já estejam implantados e prontos na localização alternativa.

Teste de restauração de chaves geridas pelo cliente. Note que isto se aplica apenas a Aplicações Lógicas em ambientes de serviço de integração (ISE).

- [Saiba mais sobre continuidade de negócios e recuperação de desastres para Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Confiem chaves geridas pelo cliente para encriptar dados em repouso para ambientes de serviço de integração (ISEs) em Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

- [Como restaurar chaves chave do cofre em Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: A sua estratégia de recuperação de desastres deve focar-se na criação da sua aplicação de lógica primária para falhar numa aplicação lógica de espera ou backup num local alternativo onde as Apps Azure Logic também estão disponíveis. Assim, se as primárias sofrerem perdas, perturbações ou falhas, o secundário pode assumir o trabalho. Esta estratégia requer que a sua app de lógica secundária e recursos dependentes já estejam implantados e prontos na localização alternativa. 

Proteja as chaves geridas pelo cliente. Note que isto se aplica apenas a Aplicações Lógicas em ambientes de serviço de integração (ISE).

Ativar Soft-Delete e limpar a proteção no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa.

- [Saiba mais sobre continuidade de negócios e recuperação de desastres para Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Confiem chaves geridas pelo cliente para encriptar dados em repouso para ambientes de serviço de integração (ISEs) em Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

- [Como permitir a proteção de Soft-Delete e purga no Cofre de Chaves](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para mais informações, consulte o [Benchmark de Segurança Azure: Resposta a incidentes](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Crie um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes, desde a deteção até à revisão pós-incidente. 

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Alavancar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. 

Além disso, marque claramente as subscrições (para ex. produção, não-prod) usando tags e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure, especialmente aqueles que processam dados sensíveis.  É da sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde ocorreu o incidente.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os seus recursos do Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

- [Publicação do NIST - Guia de Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de Exportação Contínua para ajudar a identificar riscos para os recursos da Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas de segurança e recomendações para proteger os seus recursos Azure.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios de equipa vermelha](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft.

- [Regras de teste de penetração de compromisso](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança da Azure](/azure/security/benchmarks/security-baselines-overview)
