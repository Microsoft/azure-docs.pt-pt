---
title: Log, auditoria e visibilidade do gateway na Austrália do Azure
description: Como configurar o registro em log, a auditoria e a visibilidade dentro das regiões australianas para atender aos requisitos específicos da política, regulamentos e legislações do governo australiano.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 153b9d503dfece404455fbb7e8cb51c51686ec57
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68824295"
---
# <a name="gateway-logging-auditing-and-visibility-in-azure-australia"></a>Log, auditoria e visibilidade do gateway na Austrália do Azure

Detectar e responder a ameaças de segurança cibernéticos depende da geração, coleta e análise de dados relacionados à operação de um sistema.

A Microsoft tem ferramentas internas no Azure para ajudá-lo a implementar registro em log, auditoria e visibilidade para gerenciar a segurança de seus sistemas implantados no Azure. Também há uma arquitetura de referência que se alinha com as diretrizes do consumidor australiano ACSC (centro de segurança da Austrália) e a intenção do ISM (manual de segurança da informação).

Os gateways atuam como mecanismos de controle de fluxo de informações na camada de rede e também podem controlar informações nas camadas mais altas do modelo de interconexão de sistema aberto (OSI). Os gateways são necessários para controlar os fluxos de dados entre domínios de segurança e impedir o acesso não autorizado de redes externas. Devido à importância dos gateways no controle do fluxo de informações entre domínios de segurança, qualquer falha, especialmente em classificações mais altas, pode ter conseqüências sérias. Como tal, mecanismos robustos para a equipe de alerta para situações que podem causar incidentes de segurança cibernético são especialmente importantes para gateways.

A implementação de recursos de registro em log e alertas para gateways pode auxiliar na detecção de incidentes de segurança cibernéticos, tentativas de invasão e padrões de uso incomuns. Além disso, o armazenamento de logs de eventos em um servidor de log seguro separado aumenta a dificuldade para que um adversário exclua informações de registro em log para destruir evidências de uma invasão cibernético direcionada.

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Requisitos de ACSC (centro de segurança da Austrália) australiano

Os requisitos de segurança gerais para sistemas da Comunidade são definidos no ISM (ACSC Information Security manual). Para auxiliar as entidades da Comunidade a atender a esses requisitos no Azure, o *Guia do consumidor do acsc – Microsoft Azure no* relatório de certificação protegido e *acsc – Microsoft Azure* publicações detalham os seguintes requisitos específicos relacionados para registro em log, auditoria e visibilidade:

1. Para atenuar os riscos decorrentes do uso de recursos de nuvem subjacentes compartilhados, as entidades da Comunidade devem optar por Microsoft Azure recursos fornecidos, incluindo o centro de segurança do Azure, o Azure Monitor, o Azure Policy e o Azure Advisor para auxiliar as entidades a serem executadas monitoramento em tempo real de suas cargas de trabalho do Azure

2. O ACSC também recomenda que as entidades da Comunidade encaminhem todos os logs de segurança preparados para o ACSC para todo o monitoramento governamental australiano

3. Para auxiliar na mitigação de riscos, as entidades da Comunidade devem ser configuradas em suas assinaturas do Azure:

    * Habilitar central de segurança do Azure
    * Atualizar para a camada Standard
    * Habilitar o provisionamento automático do Microsoft Monitoring Agent para VMs do Azure com suporte
    * Examine regularmente, prioritise e atenue as recomendações e alertas de segurança no painel do centro de segurança

4. As entidades governamentais devem habilitar o encaminhamento de log e evento de sua assinatura do Azure para o ACSC para fornecer a ACSC com visibilidade de não conformidade com essas diretrizes. Os hubs de eventos do Azure fornecem a capacidade de executar o streaming de log externo para os sistemas ACSC ou locais de propriedade da entidade da Comunidade

5. As entidades da Comunidade devem alinhar o log que elas habilitam no Azure para os requisitos especificados no ISM

6. A Microsoft retém os logs no Azure por 90 dias. As entidades do cliente devem implementar um regime de arquivamento de log para garantir que os logs possam ser retidos pelos sete anos necessários no NAA AFDA

7. As entidades da Comunidade que têm recursos locais ou de SIEM (gerenciamento de eventos e informações de segurança) baseados no Azure também podem encaminhar logs para esses sistemas

8. As entidades da Comunidade devem implementar logs de fluxo do observador de rede para NSGs (grupos de segurança de rede) e máquinas virtuais. Esses logs devem ser armazenados em uma conta de armazenamento dedicada contendo apenas logs de segurança, e o acesso à conta de armazenamento deve ser protegido com controles de acesso baseados em função

9. As entidades da Comunidade devem implementar a orientação do consumidor ACSC para garantir que as cargas de trabalho do Azure atendam à intenção do ISM para registro em log e monitoramento. As entidades da Comunidade também devem optar pelos recursos do Azure que ajudam o ACSC a receber monitoramento, alertas e logs em tempo real associados ao uso do governo australiano do Azure

## <a name="architecture"></a>Arquitetura

Para entender com segurança o tráfego de rede entrando e saindo do ambiente do Azure, o registro em log necessário deve ser habilitado no conjunto certo de componentes. Isso garante a visibilidade completa do ambiente e fornece os dados necessários para executar a análise.

![Arquitetura de monitoramento do Azure](media/visibility.png)

## <a name="components"></a>Componentes

A arquitetura mostrada acima é composta de componentes discretos que fornecem a função de origens de log, coleta de log, retenção de log, análise de log ou resposta a incidentes. Essa arquitetura inclui componentes individuais que normalmente estão envolvidos em implantações do Azure acessíveis à Internet.

|Functions|Componentes|
|---|---|
|Fontes de log|<ul><li>Gateway de Aplicação</li><li>Gateway de VPN</li><li>Azure Firewall</li><li>Dispositivos de rede virtual</li><li>Balanceador de Carga do Azure</li><li>Virtual Machines</li><li>Servidores DNS (sistema de nomeação de domínio)</li><li>Syslog e/ou servidores de coleta de log</li><li>NSGs</li><li>Registo de atividades do Azure</li><li>Log de diagnóstico do Azure</li><li>Política do Azure</li></ul>|
|Recolha de Registos|<ul><li>Hubs de Eventos</li><li>Observador de Rede</li><li>Log Analytics</li></ul>|
|Retenção de log|<ul><li>Storage do Azure</li></ul>|
|Análise de Registos|<ul><li>Central de segurança do Azure (ASC)</li><li>Azure Advisor</li><li>Soluções de Log Analytics<ul><li>Análises de Tráfego</li><li>Análise de DNS (pré-visualização)</li><li>Log Analytics de Atividade</li></ul></li><li>SIEM</li><li>ACSC</li></ul>|
|Resposta a Incidentes|<ul><li>Alertas do Azure</li><li>Automatização do Azure</li></ul>|
|

A arquitetura funciona primeiro gerando logs das fontes necessárias e, em seguida, coletando-os em repositórios centralizados. Depois de coletar os logs, eles podem ser:

* usado pelo Azure Analysis Services para obter informações,
* seja encaminhado para sistemas externos ou
* Obtenha arquivado no armazenamento para retenção de longo prazo.

Para responder aos principais eventos ou incidentes identificados pelas ferramentas de análise, os alertas podem ser configurados e a automação desenvolvida para realizar as ações necessárias para o gerenciamento proativo e a resposta.

## <a name="general-guidance"></a>Documentação de orientação geral

Ao implementar os componentes listados neste artigo, as seguintes diretrizes gerais se aplicam:

* Valide a disponibilidade da região dos serviços, garantindo que todos os dados permaneçam dentro dos locais autorizados e implantem na AU central ou na AU Central 2 como a primeira preferência para cargas de trabalho PROTEGIDAs

* Consulte o *relatório de certificação do Azure-acsc – publicação 2018 protegida* para obter o status de certificação de serviços individuais e executar autoavaliações em quaisquer componentes relevantes não incluídos no relatório de acordo com o *Guia do consumidor do acsc – Microsoft Azure em protegido*

* Para componentes não mencionados neste artigo, as entidades da Comunidade devem seguir os princípios incluídos em relação à geração, captura, análise e retenção de logs

* Identificar e prioritiser o registro em log, a auditoria e a visibilidade em sistemas de alto valor, bem como todos os pontos de entrada e saída da rede para sistemas hospedados no Azure

* Consolide logs e minimiza o número de instâncias de ferramentas de log, como contas de armazenamento, Log Analytics de trabalho e hubs de eventos

* Restringir privilégios administrativos por meio de controles de acesso baseados em função

* Usar a MFA (autenticação multifator) para contas que administram ou configuram recursos no Azure

* Quando centralising a coleta de log em várias assinaturas, certifique-se de que os administradores tenham os privilégios necessários em cada assinatura

* Garanta a conectividade de rede e qualquer configuração de proxy necessária para máquinas virtuais, incluindo NVAs (soluções de virtualização de rede), servidores de coleta de logs e servidores DNS, para se conectar aos serviços do Azure necessários, como os espaços de trabalho do Log Analytics, hubs de eventos e armazenamento

* Configurar o Microsoft Monitoring Agent (MMA) para o Utilise TLS versão 1,2

* Use Azure Policy para monitorar e impor a conformidade com os requisitos

* Impor a criptografia em todos os repositórios de dados, como armazenamento e bancos

* Usar o LRS (armazenamento com redundância local) e instantâneos para disponibilidade de contas de armazenamento e dados associados

* Considere o armazenamento com redundância geográfica (GRS) ou o armazenamento fora do local para alinhar com estratégias de recuperação de desastres

|Resource|URL|
|---|---|
|Documentos normativos da Austrália e conformidade da política|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Produtos do Azure-regiões australianas e não regionais|[https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, Austrália Central, Austrália Central-2, Austrália-leste, Austrália-sudeste](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Whitepaper de gerenciamento de log do Microsoft Azure Segurança e Auditoria|[https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf)|
|Configuração de Microsoft Monitoring Agent|[https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)|
|

## <a name="component-guidance"></a>Diretrizes de componente

Esta seção fornece informações sobre a finalidade de cada componente e sua função na arquitetura geral de log, auditoria e visibilidade. Links adicionais são fornecidos para acessar recursos úteis, como documentação de referência, guias e tutoriais.

## <a name="log-sources"></a>Fontes de log

Antes que qualquer análise, alerta ou relatório possa ser concluído, os logs necessários devem ser gerados. Os logs do Azure são categorizados em logs de controle/gerenciamento, logs de plano de dados e eventos processados.

|Type|Descrição|
|---|---|
|Logs de controle/gerenciamento|Fornecer informações sobre operações de Azure Resource Manager|
|Logs do plano de dados|Forneça informações sobre eventos gerados como parte do uso de recursos do Azure, como logs em uma máquina virtual e os logs de diagnóstico disponíveis por meio de Azure Monitor|
|Eventos processados|Forneça informações sobre eventos/alertas analisados que foram processados pelo Azure, como o local em que a central de segurança do Azure processou e criou assinaturas para fornecer alertas de segurança|
|

### <a name="application-gateway"></a>Gateway de Aplicação

Aplicativo Azure gateway é um dos pontos de entrada possíveis em um ambiente do Azure, portanto, você precisa capturar informações relacionadas a conexões de entrada que se comunicam com aplicativos Web. O gateway de aplicativo pode fornecer informações cruciais relacionadas ao uso do aplicativo Web, bem como auxiliar na detecção de incidentes de segurança cibernéticos. O gateway de aplicativo envia metadados para o log de atividades e logs de diagnóstico em Azure Monitor em que ele pode ser utilised em Log Analytics ou distribuído para um hub de eventos ou uma conta de armazenamento.

|Recursos|Associar|
|---|---|
|Documentação do Gateway de Aplicação|[https://docs.microsoft.com/azure/application-gateway/](https://docs.microsoft.com/azure/application-gateway/)|
|Guia de início rápido do gateway de aplicativo|[https://docs.microsoft.com/azure/application-gateway/quick-create-portal](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)|
|

### <a name="vpn-gateway"></a>Gateway de VPN

O gateway de VPN é um ponto de entrada potencial para uma ampla gama de comunicações no ambiente do Azure, como a conexão com um ambiente local e o tráfego administrativo. O registro em gateways de VPN fornece Insight e rastreabilidade para as conexões feitas ao ambiente do Azure. O registro em log pode fornecer auditoria e análise, bem como auxiliar na detecção ou investigação de conexões mal-intencionadas ou anormais. Os logs de gateway de VPN são enviados para o log de atividades do Azure Monitor, onde podem ser utilised em Log Analytics ou distribuídos para um hub de eventos ou uma conta de armazenamento.

|Recursos|Associar|
|---|---|
|Documentação do Gateway de VPN|[https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway)|
|Diretrizes de gateway de VPN específicas do governo australiano|[Configuração do gateway de VPN do Azure](vpn-gateway.md)|
|

### <a name="azure-firewall"></a>Azure Firewall

O Firewall do Azure fornece um ponto de saída controlado de um ambiente do Azure e os logs gerados, que incluem informações sobre tentativas e conexões de saída bem-sucedidas, são um elemento importante em sua estratégia de registro em log. Esses logs podem validar que os sistemas estão operando como projetados, bem como auxiliar na detecção de código mal-intencionado ou atores tentando se conectar a sistemas externos não autorizados. O Firewall do Azure grava logs no log de atividades e nos logs de diagnóstico em Azure Monitor onde eles podem ser usados no Log Analytics ou distribuídos para um hub de eventos ou uma conta de armazenamento.

|Recursos|Associar|
|---|---|
|Documentação do Azure Firewall|[https://docs.microsoft.com/azure/firewall/](https://docs.microsoft.com/azure/firewall)|
|Tutorial: Monitorar os logs e as métricas do firewall do Azure|[https://docs.microsoft.com/azure/firewall/tutorial-diagnostics](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics)|
|

### <a name="network-virtual-appliances-nva"></a>NVA (soluções de virtualização de rede)

NVAs pode ser usado para complementar os recursos de segurança disponíveis nativamente no Azure. Os logs gerados no NVAs podem ser recursos valiosos para detectar incidentes de segurança cibernéticos e são uma parte fundamental de uma estratégia geral de log, auditoria e visibilidade. Para capturar logs de NVAs, Utilise a Microsoft Monitoring Agent (MMA). Para NVAs que não dão suporte à instalação do MMA, considere usar um syslog ou outro servidor de coleta de logs para os logs de retransmissão.

|Recursos|Associar|
|---|---|
|Visão geral de soluções de virtualização de rede|[https://azure.microsoft.com/solutions/network-appliances](https://azure.microsoft.com/solutions/network-appliances)|
|Documentação do NVA|Consulte a documentação do fornecedor sobre a implementação do NVA relevante no Azure|
|

### <a name="azure-load-balancer"></a>Balanceador de Carga do Azure

Os logs de Azure Load Balancer são usados para obter informações úteis sobre as conexões e o uso relacionados aos sistemas implantados no Azure. Isso pode ser usado para monitoramento de integridade e disponibilidade, mas também constitui outro componente importante para obter a percepção necessária do tráfego de comunicações e detectar padrões de tráfego maliciosos ou anormais. Azure Load Balancer logs no log de atividades e nos logs de diagnóstico em Azure Monitor em que ele pode ser utiliseddo no Log Analytics ou distribuído para um hub de eventos ou uma conta de armazenamento.

|Recursos|Associar|
|---|---|
|Documentação do Azure Load Balancer|[https://docs.microsoft.com/azure/load-balancer](https://docs.microsoft.com/azure/load-balancer)|
|Métricas e diagnóstico de integridade para Standard Load Balancer|[https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)|
|

### <a name="virtual-machines"></a>Virtual Machines

As máquinas virtuais são pontos de extremidade que enviam e recebem comunicações de rede, processam dados e fornecem serviços. Como as máquinas virtuais podem hospedar dados ou serviços de sistema cruciais, garantir que eles estejam operando corretamente e detectar incidentes de segurança cibernéticos possam ser críticos. As máquinas virtuais coletam vários logs de eventos e de auditoria que podem acompanhar a operação do sistema e as ações executadas nesse sistema. Os logs coletados em máquinas virtuais podem ser encaminhados para um espaço de trabalho Log Analytics usando o Microsoft Monitoring Agent em que podem ser analisados pela central de segurança do Azure e soluções de Log Analytics aplicáveis. As máquinas virtuais também podem se integrar diretamente com os hubs de eventos do Azure ou com um SIEM, seja diretamente ou por meio de um servidor de coleta de logs.

|Recursos|Associar|
|---|---|
|Virtual Machines|[https://docs.microsoft.com/azure/virtual-machines](https://docs.microsoft.com/azure/virtual-machines)|
|Coletar dados de máquinas virtuais|[https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|
|Transmitir logs de máquina virtual para hubs de eventos|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs)|
|

### <a name="domain-name-services-dns-servers"></a>Servidores DNS (serviços de nomes de domínio)

Os logs do servidor DNS fornecem informações importantes relacionadas aos serviços que os sistemas estão tentando acessar, seja internamente ou externamente. A captura de logs DNS pode ajudar a identificar um incidente de segurança cibernético e fornecer informações sobre o tipo de incidente e os sistemas que podem ser afetados. O MMA (agente de gerenciamento da Microsoft) pode ser usado em servidores DNS para encaminhar os logs para Log Analytics para uso no Análise de DNS (versão prévia).

|Recursos|Associar|
|---|---|
|Resolução de nomes do Azure para redes virtuais|[https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)|
|

### <a name="syslog-and-log-collection-servers"></a>Syslog e servidores de coleta de log

Para receber logs de soluções de virtualização de rede ou logs de segurança personalizados de outros sistemas para uso em um SIEM, os servidores dedicados podem ser implantados no Azure VNets. Os logs de syslog podem ser coletados em um servidor syslog e retransmitidos para Log Analytics para análise. Um servidor de coleta de log é um termo genérico para qualquer agregação de log e recurso de distribuição usado por sistemas de monitoramento centralizados ou SIEMs. Eles podem ser usados para simplificar a arquitetura e a segurança da rede e para filtrar e agregar logs antes de serem distribuídos para o recurso centralizado.

|Recursos|Associar|
|---|---|
|Origens de dados do syslog no Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)|
|Servidor de coleta de logs|Consulte a documentação do fornecedor para obter detalhes sobre o monitoramento e a arquitetura SIEM|
|

### <a name="network-security-groups-nsgs"></a>NSGs (grupos de segurança de rede)

NSGs controle o tráfego dentro e fora das redes virtuais no Azure. NSGs aplicar regras para os fluxos de tráfego que são permitidos ou negados, o que inclui o tráfego no Azure e entre o Azure e redes externas, como no local ou na Internet. NSGs são aplicados a sub-redes em uma rede virtual ou a interfaces de rede individuais. Para capturar informações sobre o tráfego que entra e sai dos sistemas no Azure, os logs do NSG podem ser habilitados por meio do recurso de logs de fluxo do NSG do observador de rede. Esses logs são usados para formar uma linha de base para a operação padrão de um sistema e são a fonte de dados para Análise de Tráfego, que fornece informações detalhadas sobre os padrões de tráfego de sistemas hospedados no Azure.

|Recursos|Associar|
|---|---|
|Documentação do grupo de segurança de rede|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Introdução ao log de fluxo para grupos de segurança de rede|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|
|Tutorial: Registrar o tráfego de rede de e para uma máquina virtual usando o portal do Azure|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)|
|

### <a name="azure-activity-log"></a>Registo de atividades do Azure

O log de atividades do Azure, que faz parte do Azure Monitor, é um log de assinatura que fornece informações sobre eventos de nível de assinatura que ocorreram no Azure. O log de atividades pode ajudar a determinar o ' What, quem e quando ' para qualquer operação de gravação (PUT, POST, DELETE) realizada ***nos*** recursos em uma assinatura. O log de atividades é crucial para acompanhar as alterações de configuração feitas no ambiente do Azure. Os logs de atividades do Azure estão disponíveis automaticamente para uso em soluções de Log Analytics e podem ser enviados para hubs de eventos ou armazenamento do Azure para processamento ou retenção.

|Recursos|Associar|
|---|---|
|Documentação do log de atividades do Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|Transmitir o log de atividades do Azure para os hubs de eventos|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs)|
|

### <a name="azure-diagnostic-log"></a>Log de diagnóstico do Azure

Azure Monitor logs de diagnóstico são logs emitidos por um serviço do Azure que fornece dados avançados e frequentes sobre a operação do serviço. Os logs de diagnóstico fornecem informações sobre a operação de um recurso em um nível detalhado e podem ser usados para uma variedade de requisitos, como auditoria ou solução de problemas. Os logs de diagnóstico do Azure estão disponíveis automaticamente para uso em soluções de Log Analytics e podem ser enviados para hubs de eventos ou armazenamento do Azure para processamento ou retenção.

|Recursos|Associar|
|---|---|
|Documentação do log de diagnóstico do Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|Serviços de suporte para logs de diagnóstico|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema)|
|

### <a name="azure-policy"></a>Política do Azure

Azure Policy impõe regras sobre como os recursos podem ser implantados, como o tipo, o local e a configuração. Azure Policy pode ser configurado para garantir que os recursos só possam ser implantados se estiverem em conformidade com os requisitos. Azure Policy é um componente fundamental para manter a integridade de um ambiente do Azure. Eventos relacionados a Azure Policy são registrados no log de atividades do Azure e ficam automaticamente disponíveis para uso em soluções Log Analytics ou podem ser enviados aos hubs de eventos ou ao armazenamento do Azure para processamento ou retenção.

|Recursos|Associar|
|---|---|
|Documentação do Azure Policy|[https://docs.microsoft.com/azure/governance/policy](https://docs.microsoft.com/azure/governance/policy)|
|Aproveitando os modelos Azure Policy e Resource Manager usando plantas do Azure|[https://docs.microsoft.com/azure/governance/blueprints/overview](https://docs.microsoft.com/azure/governance/blueprints/overview)|
|

## <a name="log-collection"></a>Coleta de log

Depois de gerada a partir de várias fontes de log, os logs precisam ser armazenados em um local centralizado para o acesso e a análise em andamento. O Azure fornece vários métodos e opções para coleta de log que podem ser utilised dependendo do tipo de log e dos requisitos.

### <a name="event-hubs"></a>Hubs de Eventos

A finalidade de um hub de eventos é agregar os dados de log para as várias fontes para distribuição. No Hub de eventos, os dados de log podem ser enviados para um SIEM, para o ACSC para fins de conformidade e armazenamento para retenção de longo prazo.

|Recursos|Associar|
|---|---|
|Documentação dos Hubs de Eventos|[https://docs.microsoft.com/azure/event-hubs](https://docs.microsoft.com/azure/event-hubs)|
|Diretrizes sobre hubs de eventos e ferramentas externas|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs)|
|

### <a name="log-analytics"></a>Log Analytics

Log Analytics faz parte de Azure Monitor e é usada para análise de log. O Log Analytics usa um espaço de trabalho como o mecanismo de armazenamento em que os dados de log podem ser disponibilizados para uma variedade de ferramentas de análise e soluções disponíveis no Azure. O Log Analytics integra-se com uma ampla variedade de componentes do Azure diretamente, bem como máquinas virtuais por meio do Microsoft Monitoring Agent.

|Recursos|Associar|
|---|---|
|Documentação do Log Analytics|[https://docs.microsoft.com/azure/azure-monitor](https://docs.microsoft.com/azure/azure-monitor)|
|Tutorial: Analisar dados no Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata)|
|

### <a name="network-watcher"></a>Observador de Rede

O uso do observador de rede é recomendado pelo ACSC para auxiliar na compreensão e na captura do tráfego de rede em uma assinatura do Azure. Os logs de fluxo do NSG fornecem a entrada para a solução Análise de Tráfego no Log Analytics, que fornece maior visibilidade, análise e relatórios nativamente por meio do Azure. O observador de rede também fornece um recurso de captura de pacotes diretamente da portal do Azure sem a necessidade de entrar na máquina virtual. A captura de pacotes permite que você crie sessões de captura de pacote para controlar o tráfego de e para uma máquina virtual.

|Recursos|Associar|
|---|---|
|Observador de Rede|[https://docs.microsoft.com/azure/network-watcher](https://docs.microsoft.com/azure/network-watcher)|
|Visão geral da captura de pacotes|[https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)|
|

## <a name="log-retention"></a>Retenção de log

Para organizações governamentais da Austrália, os logs capturados no Azure devem ser mantidos de acordo com os arquivos nacionais da [AFDA (autoridade de descarte de funções administrativas)](http://www.naa.gov.au/information-management/records-authorities/types-of-records-authorities/AFDA/index.aspx)da Austrália, que especifica a retenção de logs de até sete anos.

|Local do log|Período de Retenção|
|---|---|
|Registo de atividades do Azure|Até 90 dias|
|Área de trabalho do Log Analytics|Até dois anos|
|Hub de Eventos|Até sete dias|
|

É sua responsabilidade garantir que os logs sejam arquivados adequadamente para aderir ao AFDA e a outros requisitos legislativos.

### <a name="azure-storage"></a>Storage do Azure

O armazenamento do Azure é o repositório de logs para retenção de longo prazo no Azure. O armazenamento do Azure pode ser usado para arquivar logs do Azure, incluindo hubs de eventos, log de atividades do Azure e logs de diagnóstico do Azure. O período de retenção de dados no armazenamento pode ser definido como zero ou pode ser especificado como um número de dias. Uma retenção de zero dias significa que os logs são mantidos para sempre, caso contrário, o valor pode ser qualquer número de dias entre 1 e 2147483647.

|Recursos|Associar|
|---|---|
|Documentação do Storage do Azure|[https://docs.microsoft.com/azure/storage](https://docs.microsoft.com/azure/storage)|
|Capturar eventos através dos Hubs de eventos do Azure no armazenamento de Blobs do Azure ou de armazenamento do Azure Data Lake|[https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Tutorial: Arquivar os dados de métricas e de registos do Azure com o Armazenamento do Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data)|
|Replicação do armazenamento do Azure|[https://docs.microsoft.com/azure/storage/common/storage-redundancy](https://docs.microsoft.com/azure/storage/common/storage-redundancy)|
|Criando um instantâneo de um blob|[https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)|
|

## <a name="log-analysis"></a>Análise de registos

Depois de gerado e armazenado em um local centralizado, os logs devem ser analisados para auxiliar na detecção de incidentes de segurança tentadas ou bem-sucedidos. Quando incidentes de segurança são detectados, uma agência precisa da capacidade de responder a esses incidentes e controlar, conter e corrigir quaisquer ameaças.

### <a name="azure-security-center-asc"></a>Central de segurança do Azure (ASC)

A central de segurança do Azure fornece gerenciamento de segurança unificado e proteção avançada contra ameaças. A central de segurança do Azure pode aplicar políticas de segurança entre cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques. A central de segurança do Azure fornece painéis e análises em uma ampla variedade de componentes do Azure. O uso da central de segurança do Azure é especificado como um requisito nas diretrizes do consumidor do ACSC.

|Recursos|Associar|
|---|---|
|Documentação do Centro de Segurança do Azure|[https://docs.microsoft.com/azure/security-center](https://docs.microsoft.com/azure/security-center)|
|Início rápido: Integrar sua assinatura do Azure à central de segurança Standard|[https://docs.microsoft.com/azure/security-center/security-center-get-started](https://docs.microsoft.com/azure/security-center/security-center-get-started)|
|

### <a name="traffic-analytics"></a>Análises de Tráfego

Análise de Tráfego é uma solução baseada em nuvem que fornece visibilidade da atividade do usuário e do aplicativo no Azure. A análise de tráfego analisa os logs de fluxo NSG do observador de rede para fornecer informações sobre o fluxo de tráfego no Azure. O Análise de Tráfego é usado para fornecer painéis, relatórios, análise e recursos de resposta de eventos relacionados ao tráfego de rede visto entre redes virtuais. Análise de Tráfego fornece insights significativos e ajuda na identificação e resolução de incidentes de segurança cibernéticos.

|Recursos|Associar|
|---|---|
|Documentação do Análise de Tráfego|[https://docs.microsoft.com/azure/network-watcher/traffic-analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)|
|

### <a name="azure-advisor"></a>Azure Advisor

O Azure Advisor analisa a configuração de recursos e outros dados para recomendar soluções para ajudar a melhorar o desempenho, a segurança e a alta disponibilidade dos recursos, ao mesmo tempo em que procura oportunidades para reduzir o gasto geral do Azure. O Azure Advisor é recomendado pelo ACSC e fornece conselhos facilmente acessíveis e detalhados sobre a configuração do ambiente do Azure.

|Recursos|Associar|
|---|---|
|Documentação do Azure Advisor|[https://docs.microsoft.com/azure/advisor](https://docs.microsoft.com/azure/advisor)|
|Introdução ao Azure Advisor|[https://docs.microsoft.com/azure/advisor/advisor-get-started](https://docs.microsoft.com/azure/advisor/advisor-get-started)|
|

### <a name="dns-analytics-preview"></a>Análise de DNS (pré-visualização)

Análise de DNS é uma solução Log Analytics que coleta, analisa e correlaciona os logs analíticos e de auditoria do DNS do Windows e outros dados relacionados. Análise de DNS identifica clientes que tentam resolver nomes de domínio mal-intencionados, registros de recursos obsoletos, nomes de domínio consultados com frequência e clientes DNS comunicativos. Análise de DNS também fornece informações sobre a carga de solicitação em servidores DNS e falhas de registro de DNS dinâmico. Análise de DNS é usado para fornecer painéis, relatórios, análise e recursos de resposta de eventos relacionados às consultas de DNS feitas em um ambiente do Azure. Análise de DNS fornece insights significativos e ajuda na identificação e resolução de incidentes de segurança cibernéticos.

|Recursos|Associar|
|---|---|
|Documentação da Análise de DNS|[https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)|
|

### <a name="activity-log-analytics"></a>Log Analytics de Atividade

Análise do Log de Atividades é uma solução de Log Analytics que ajuda a analisar e pesquisar o log de atividades do Azure em várias assinaturas do Azure. O Análise do Log de Atividades é usado para fornecer painéis, relatórios, análises e recursos de resposta de eventos centralizados relacionados às ações executadas em recursos de todo o ambiente do Azure. Análise do Log de Atividades pode auxiliar na auditoria e na investigação.

|Recursos|Associar|
|---|---|
|Recolher e analisar registos de atividades do Azure no Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)|
|

### <a name="security-information-and-event-management-siem"></a>SIEM (gerenciamento de eventos e informações de segurança)

Um SIEM é um sistema que fornece armazenamento centralizado, auditoria e análise de logs de segurança, com mecanismos definidos para ingerir uma ampla gama de dados de log e ferramentas inteligentes para análise, relatório e detecção de incidentes e resposta. Você pode usar os recursos do SIEM que incluem informações de log do Azure para complementar os recursos de segurança fornecidos nativamente no Azure. As entidades da Comunidade podem Utilise um SIEM hospedado em máquinas virtuais no Azure, no local ou como um recurso SaaS (software como serviço) dependendo dos requisitos específicos.

|Recursos|Associar|
|---|---|
|Azure Sentinel (visualização)|[https://azure.microsoft.com/services/azure-sentinel](https://azure.microsoft.com/services/azure-sentinel)|
|Documentação do SIEM|Consulte a documentação do fornecedor para obter orientações e arquitetura do SIEM|
|Usar Azure Monitor para integrar com ferramentas SIEM|[https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)|
|

### <a name="australian-cyber-security-centre"></a>Centro de segurança do Cyber australiano

O ACSC (centro de segurança da Austrália) é o líder do governo australiano na segurança de Cyber nacional. Ele reúne recursos de segurança cibernéticos de todo o governo australiano para melhorar a resiliência na região da comunidade australiana e dar suporte à prosperidade econômica e social da Austrália na época digital. O ACSC recomenda que as entidades da Comunidade encaminhem todos os arquivos de log, eventos e logs gerados pelo sistema para o ACSC para todo o monitoramento governamental australiano.

|Recursos|Associar|
|---|---|
|Site do centro de segurança da Austrália do australiano|[https://www.acsc.gov.au](https://www.acsc.gov.au)|
|

## <a name="incident-response"></a>Resposta a incidentes

Gerar os logs apropriados, coletá-los em repositórios centralizados e realizar análises aumenta a compreensão dos sistemas e fornece mecanismos para detectar incidentes de segurança cibernéticos. Depois que incidentes ou eventos forem detectados, a próxima etapa será reagir a esses eventos e executar ações para manter a integridade do sistema e proteger os serviços e os dados do comprometimento. O Azure fornece uma combinação de serviços para responder efetivamente a qualquer evento que ocorra.

### <a name="azure-alerts"></a>Alertas do Azure

Os alertas do Azure podem ser usados para notificar o suporte e a equipe de segurança em resposta a eventos específicos. Isso permite que uma entidade da Comunidade responda proativamente à detecção de eventos relevantes gerados pelo Analysis Services listado neste artigo.

|Recursos|Associar|
|---|---|
|Visão geral de alertas no Microsoft Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts)|
|Gerir e responder a alertas de segurança no Centro de Segurança do Azure|[https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)|
|Responder a eventos com Alertas do Azure Monitor|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)|
|

### <a name="azure-automation"></a>Automatização do Azure

A automação do Azure permite que as entidades da Comunidade disparem ações em resposta a eventos. Isso pode ser iniciar uma captura de pacote em máquinas virtuais, executar um fluxo de trabalho, parar ou iniciar máquinas virtuais ou serviços ou uma variedade de outras tarefas. A automação permite uma resposta rápida a alertas sem intervenção manual, reduzindo assim o tempo de resposta e a severidade de um incidente ou evento.

|Recursos|Associar|
|---|---|
|Documentação da automação do Azure|[https://docs.microsoft.com/azure/automation](https://docs.microsoft.com/azure/automation)|
|Guia de instruções: Usar um alerta para disparar um runbook de automação do Azure|[https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)|
|

## <a name="next-steps"></a>Passos Seguintes

Examine o artigo sobre [administração remota segura do gateway](gateway-secure-remote-administration.md) para obter detalhes sobre como gerenciar com segurança seu ambiente de gateway no Azure.
