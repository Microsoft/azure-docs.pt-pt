---
title: Linha de segurança Azure para o Serviço Azure Kubernetes
description: A linha de base de segurança do Serviço Azure Kubernetes fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2ebfe8e8b3d948d3e11bc8c24def6d5dbcb58de9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558912"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Linha de segurança Azure para o Serviço Azure Kubernetes

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../security/benchmarks/overview-v1.md) a Azure Kubernetes. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável a Azure Kubernetes. Foram excluídos **os controlos** não aplicáveis a Azure Kubernetes.

 
Para ver como Azure Kubernetes mapeia completamente para o Azure Security Benchmark, consulte o ficheiro completo de [mapeamento de base de segurança Azure Kubernetes](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Por padrão, um grupo de segurança de rede e uma tabela de rotas são automaticamente criados com a criação de um cluster Microsoft Azure Kubernetes Service (AKS). A AKS modifica automaticamente os grupos de segurança da rede para um fluxo de tráfego adequado, uma vez que os serviços são criados com equilibradores de carga, mapeamentos portuários ou rotas de entrada. O grupo de segurança da rede está automaticamente associado aos NICs virtuais nos nós do cliente e à tabela de rotas com a sub-rede na rede virtual. 

Utilize as políticas de rede AKS para limitar o tráfego de rede, definindo regras para o tráfego de entradas e saídas entre as cápsulas Linux num cluster baseado na escolha de espaços de nome e seletores de etiquetas. A utilização da política de rede requer o plug-in CNI Azure com rede virtual definida e sub-redes e só pode ser ativado na criação de clusters. Não podem ser implantados num cluster AKS existente.

Pode implementar um cluster AKS privado para garantir o tráfego de rede entre o servidor AKS API e as piscinas de nó permanecem apenas na rede privada. O plano de controlo ou servidor API, reside numa subscrição Azure gerida pela AKS e utiliza endereços IP internos (RFC1918), enquanto o cluster ou o conjunto de nós do cliente estão na sua própria subscrição. O servidor e o cluster ou piscina de nó comunicam entre si utilizando o serviço Azure Private Link na rede virtual do servidor API e um ponto final privado que está exposto na sub-rede do cluster AKS do cliente.  Em alternativa, utilize um ponto final público para o servidor AKS API, mas restrinja o acesso com a funcionalidade DE GAMA IP autorizada do AKS API Server. 

- [Conceitos de segurança para aplicações e clusters no Azure Kubernetes Service (AKS)](concepts-security.md)

- [Tráfego seguro entre cápsulas utilizando políticas de rede no Serviço Azure Kubernetes (AKS)](use-network-policies.md)

- [Criar um cluster privado de serviçoS Azure Kubernetes](private-clusters.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Orientação**: Utilize o Security Center e siga as suas recomendações de proteção de rede para garantir os recursos de rede utilizados pelos seus clusters Azure Kubernetes Service (AKS). 

Ativar os registos de fluxo do grupo de segurança da rede e enviar os registos para uma conta de Armazenamento Azure para auditoria. Também pode enviar os registos de fluxo para um espaço de trabalho do Log Analytics e, em seguida, usar o Traffic Analytics para fornecer informações sobre os padrões de tráfego na sua nuvem Azure para visualizar a atividade da rede, identificar pontos quentes e ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Como ativar os registos de fluxo de segurança da rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Utilize um Gateway de aplicação Azure ativado firewall de aplicação web (WAF) em frente a um cluster AKS para fornecer uma camada adicional de segurança filtrando o tráfego de entrada para as suas aplicações web. A Azure WAF utiliza um conjunto de regras, fornecidas pelo Open Web Application Security Project (OWASP), para ataques, tais como scripts de sites cruzados ou envenenamento por cookies contra este tráfego. 

Utilize um gateway API para autenticação, autorização, estrangulamento, caching, transformação e monitorização de APIs utilizados no seu ambiente AKS. Uma porta de entrada da API serve de porta de entrada para os microserviços, separa os clientes dos seus microserviços e diminui a complexidade dos seus microserviços, removendo o fardo de lidar com preocupações de corte transversal.

- [Compreender as melhores práticas para a conectividade da rede e segurança em AKS](operator-best-practices-network.md)

- [Controlador de entrada de gateway de aplicação ](../application-gateway/ingress-controller-overview.md)

- [Utilizar a Azure API Management com microserviços implantados no Serviço Azure Kubernetes](../api-management/api-management-kubernetes.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ativar a proteção padrão de negação de serviço distribuída pela Microsoft (DDoS) nas redes virtuais onde os componentes do Serviço Azure Kubernetes (AKS) são implantados para proteções contra ataques DDoS.

Instale o motor de política de rede e crie políticas de rede Kubernetes para controlar o fluxo de tráfego entre cápsulas em AKS, uma vez que, por padrão, todo o tráfego é permitido entre estas cápsulas. A política de rede só deve ser utilizada para nós e cápsulas baseados em Linux em AKS. Defina regras que limitem a comunicação do pod para uma segurança melhorada. 

Opte por permitir ou negar tráfego com base em configurações como etiquetas atribuídas, espaço de identificação ou porta de tráfego. As políticas de rede necessárias podem ser aplicadas automaticamente à medida que as cápsulas são criadas dinamicamente num cluster AKS. 

- [Tráfego seguro entre cápsulas utilizando políticas de rede no Serviço Azure Kubernetes (AKS)](use-network-policies.md)

- [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Utilize a captura de pacotes do Observador de Rede, conforme necessário para investigar a atividade anómala. 

O Network Watcher é ativado automaticamente na região da sua rede virtual quando cria ou atualiza uma rede virtual na sua subscrição. Também pode criar novos casos de Network Watcher utilizando o PowerShell, o Azure CLI, a API REST ou o método do Cliente gestor de recursos Azure

- [Como ativar o Observador de Redes](../network-watcher/network-watcher-create.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Fixe o seu cluster Azure Kubernetes Service (AKS) com um Gateway de aplicação Azure ativado com uma Firewall de Aplicação Web (WAF). 

Se a deteção e/ou prevenção de intrusões baseadas na inspeção de carga útil ou na análise de comportamento não for um requisito, um Gateway de aplicação Azure com WAF pode ser usado e configurado em "modo de deteção" para registar alertas e ameaças, ou "modo de prevenção" para bloquear intrusões e ataques detetados ativamente.

- [Compreenda as melhores práticas para garantir o seu cluster AKS com um WAF](./operator-best-practices-network.md#secure-traffic-with-a-web-application-firewall-waf)

- [Como implementar o Gateway de Aplicações Azure (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize tags de serviço de rede virtuais para definir controlos de acesso à rede em grupos de segurança de rede associados a instâncias do Serviço Azure Kubernetes (AKS). As etiquetas de serviço podem ser utilizadas no lugar de endereços IP específicos ao criar regras de segurança para permitir ou negar o tráfego do serviço correspondente especificando o nome da etiqueta de serviço. 

A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Aplique uma etiqueta Azure em piscinas de nó no seu cluster AKS. São diferentes das tags de serviço de rede virtual, e são aplicadas a cada nó dentro da piscina do nó e persistem através de upgrades. 

- [Compreender e usar tags de serviço](../virtual-network/service-tags-overview.md)

- [Compreender NSGs para AKS](support-policies.md)

- [Controlo tráfego de saídas para nós de cluster no Serviço Azure Kubernetes (AKS)](limit-egress-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão com a Política Azure para os recursos de rede associados aos seus clusters Azure Kubernetes Service (AKS). 

Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.ContainerService" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus clusters AKS. 

Além disso, utilize definições de política incorporadas relacionadas com aKS, tais como:

- As gamas IP autorizadas devem ser definidas nos Serviços Kubernetes

- Impor entrada HTTPS no cluster do Kubernetes

- Garantir que os serviços ouvem apenas em portas permitidas no cluster Kubernetes

Informações adicionais estão disponíveis nos links referenciados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Amostras da Política Azure para networking](../governance/policy/samples/built-in-policies.md#network)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para grupos de segurança de rede e outros recursos para o fluxo de tráfego de e para os clusters do Serviço Azure Kubernetes (AKS). Utilize o campo "Descrição" para as regras individuais do grupo de segurança da rede para especificar a necessidade e/ou duração do negócio, e assim por diante, para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições relacionadas com a marcação da Política Azure incorporada, por exemplo, "Exigir etiqueta e seu valor" que garantam que todos os recursos são criados com etiquetas e para receber notificações para os recursos não marcados existentes.

Opte por permitir ou negar caminhos de rede específicos dentro do cluster com base em espaços de nome e seletores de etiquetas com políticas de rede. Utilize estes espaços de nome e etiquetas como descritores para regras de configuração de tráfego. Utilize a interface de linha de comando Azure PowerShell ou Azure (CLI) para procurar ou executar ações em recursos baseados nas suas etiquetas.

- [Política de Azure com CLI](/cli/azure/policy)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Como criar um NSG com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações nos recursos de rede relacionados com os clusters do Serviço Azure Kubernetes (AKS). 

Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede. Quaisquer entradas do utilizador AzureContainerService nos registos de atividade são registadas como ações da plataforma. 

Utilize registos do Azure Monitor para ativar e consultar os registos a partir de AKS os componentes principais, kube-apiserver e kube-controller-manager. Crie e gere os nós que executam o kubelet com tempo de funcionamento do contentor e implemente as suas aplicações através do servidor API gerido de Kubernetes. 

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

- [Ativar e rever os registos de nó principal do Kubernetes no Azure Kubernetes Service (AKS)](./view-control-plane-logs.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: Os nós do Serviço Azure Kubernetes (AKS) utilizam ntp.ubuntu.com para sincronização de tempo, juntamente com a porta 123 da UDP e o Protocolo de Tempo de Rede (NTP). 

Certifique-se de que os servidores NTP estão acessíveis pelos nós do cluster se utilizarem servidores DNS personalizados. 

- [Compreenda os requisitos do domínio NTP e dos requisitos portuários para os nosdes de cluster AKS](limit-egress-traffic.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ative os registos de auditoria dos componentes principais da Azure Kubernetes Services (AKS), kube-apiserver e kube-controller-manager, que são fornecidos como um serviço gerido. 

- kube-auditaksService: O nome de exibição no registo de auditoria para a operação do plano de controlo (a partir do hcpService) 

- masterclient: O nome de exibição no registo de auditoria do MasterClientCertificate, o certificado que obtém a az aks obter credenciais 

- não declive: O nome de exibição do ClientCertificate, que é usado por nós de agente

Ativar outros registos de auditoria, como a auditoria de kube também. 

Exporte estes registos para Log Analytics ou outra plataforma de armazenamento. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.

Ativar e embarcar estes dados para a Azure Sentinel ou um SIEM de terceiros com base nos seus requisitos de negócio organizacional.

- [Reveja o esquema de Log, incluindo funções de log aqui](./view-control-plane-logs.md)

- [Compreender o Monitor Azure para contentores](../azure-monitor/containers/container-insights-overview.md)

- [Como ativar o Monitor Azure para contentores](../azure-monitor/containers/container-insights-onboard.md)

- [Ativar e rever os registos de nó principal do Kubernetes no Azure Kubernetes Service (AKS)](./view-control-plane-logs.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Utilize registos de atividade para monitorizar as ações dos recursos do Serviço Azure Kubernetes (AKS) para visualizar toda a atividade e o seu estado. Determinar que operações foram tomadas sobre os recursos da sua subscrição com registos de atividade: 

- que iniciou a operação
- quando a operação ocorreu
- o estado da operação
- os valores de outras propriedades que podem ajudá-lo a pesquisar a operação

Recupere informações do registo de atividade através do Azure PowerShell, da Interface da Linha de Comando Azure (CLI), da API Azure REST ou do portal Azure. 

Permitir registos de auditoria em componentes principais da AKS, tais como: 

- kube-auditaksService: O nome de exibição no registo de auditoria para a operação do plano de controlo (a partir do hcpService) 

- masterclient: O nome de exibição no registo de auditoria do MasterClientCertificate, o certificado que obtém a az aks obter credenciais 

- não declive: O nome de exibição do ClientCertificate, que é usado por nós de agente

Ligue outros registos de auditoria, como a auditoria de kube também. 

- [Como ativar e rever registos de nó de nó master kubernetes em AKS](./view-control-plane-logs.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Permitir a instalação automática de agentes do Log Analytics para recolher dados dos nós do cluster AKS. Além disso, o fornecimento automático de ligação do Agente de Monitorização Azure Log Analytics do Azure Security Center, como por defeito, o provisionamento automático está desligado. O agente também pode ser instalado manualmente. Com o fornecimento automático, o Security Center implementa o agente Log Analytics em todos os VMs Azure suportados e quaisquer novos que sejam criados. O centro de segurança recolhe dados de Máquinas Virtuais Azure (VM), conjuntos de escala de máquinas virtuais e contentores IaaS, como os nós de cluster Kubernetes, para monitorizar as vulnerabilidades e ameaças de segurança. Os dados são recolhidos utilizando o Azure Log Analytics Agent, que lê várias configurações relacionadas com a segurança e registos de eventos da máquina e copia os dados para o seu espaço de trabalho para análise. 

A recolha de dados é necessária para fornecer visibilidade em atualizações em falta, definições de segurança de SO mal configuradas, estado de proteção de ponto final e deteção de saúde e ameaças.

- [Como permitir o provisionamento automático do Agente de Análise de Registos](../security-center/security-center-enable-data-collection.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: A bordo das suas instâncias do Serviço Azure Kubernetes (AKS) para o Azure Monitor e de definir o período de retenção correspondente do espaço de trabalho Azure Log Analytics de acordo com os requisitos de conformidade da sua organização. 

- [Como definir parâmetros de retenção de registos para log analytics workspaces](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: A bordo das suas instâncias do Serviço Azure Kubernetes (AKS) para o Azure Monitor e configurar as definições de diagnóstico para o seu cluster. 

Utilize o espaço de trabalho do Log Analytics do Azure Monitor para rever registos e efetuar consultas nos dados de registo. Os registos do Azure Monitor são ativados e geridos no portal Azure, ou através do CLI, e trabalham com ambos os clusters de acesso baseados em funções de Kubernetes (Kubernetes RBAC), Azure RBAC e clusters AKS não-RBAC.

Veja os registos gerados por componentes principais da AKS (kube-apiserver e controlador de kube) para resolver problemas na resolução da sua aplicação e serviços. Ativar e aceder a dados a bordo ao Azure Sentinel ou a um SIEM de terceiros para gestão e monitorização centralizada de registos.

- [Como ativar e rever registos de nó de nó master kubernetes em AKS](./view-control-plane-logs.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o Serviço Azure Kubernetes (AKS) juntamente com o Security Center para obter uma visibilidade mais profunda nos nós AKS. 

Reveja os alertas do Centro de Segurança sobre ameaças e atividades maliciosas detetadas no hospedeiro e ao nível do cluster. O Security Center implementa uma análise contínua de eventos de segurança bruta que ocorrem num cluster AKS, como dados de rede, criação de processos e registo de auditoria de Kubernetes. Determine se esta atividade é um comportamento esperado ou se a aplicação está a portar-se mal. Utilize métricas e registos no Azure Monitor para fundamentar as suas descobertas. 

- [Compreenda a integração dos Serviços Azure Kubernetes com o Security Center](../security-center/defender-for-kubernetes-introduction.md)

- [Como ativar o Standard Tier do Centro de Segurança Azure](../security-center/security-center-get-started.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Instale e permita que o Microsoft Anti-malware para Azure para Azure Kubernetes Service (AKS) máquinas virtuais e nós de balança de máquinas virtuais. Reveja os alertas no Centro de Segurança para reparação.

- [Microsoft Antimalware para Azure Cloud Services e Máquinas Virtuais](../security/fundamentals/antimalware.md)

- [Guia de referência de alertas de segurança](../security-center/alerts-reference.md)

- [Alertas para contentores - Agrupamentos de serviço Azure Kubernetes](../security-center/alerts-reference.md#alerts-akscluster)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: O Serviço Azure Kubernetes (AKS) utiliza o projeto CoreDNS para gestão e resolução de DNS do cluster.

Ativar a consulta de DNS através da aplicação de configuração documentada no seu ConfigMap personalizado para coredns. 

- [Personalizar o CoreDNS com o Azure Kubernetes Service](coredns-custom.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Utilize kubectl, um cliente de linha de comando, no Serviço Azure Kubernetes (AKS) para gerir um cluster Kubernetes e obter os seus registos do nó AKS para fins de resolução de problemas. O Kubectl já está instalado se utilizar a Azure Cloud Shell. Para instalar kubectl localmente, utilize o cmdlet 'Install-AzAksKubectl'.

- [Quickstart - Implementar um cluster de serviço Azure Kubernetes utilizando o PowerShell](kubernetes-walkthrough-powershell.md)

- [Obter registos kubelet dos nós de cluster do Azure Kubernetes Service (AKS)](kubelet-logs.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Serviço Azure Kubernetes (AKS) em si não fornece uma solução de gestão de identidade que armazena contas e senhas de utilizador regulares. Com a integração do Azure Ative Directory (Azure AD), pode conceder aos utilizadores ou grupos acesso aos recursos de Kubernetes dentro de um espaço de nome ou em todo o cluster.

Realize consultas ad hoc para descobrir contas que são membros de grupos administrativos AKS com o módulo Azure AD PowerShell

Utilize o Azure CLI para operações como "Obtenha credenciais de acesso para um cluster de Kubernetes gerido" para ajudar a conciliar o acesso regularmente. Implemente este processo para manter um inventário atualizado das contas de serviço, que são outro tipo de utilizador primário em AKS. Impor as recomendações de Gestão de Identidade e Acesso do Centro de Segurança.

- [Como integrar a AKS com Azure AD](azure-ad-integration-cli.md)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Como monitorizar a identidade e o acesso com o Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O Serviço Azure Kubernetes (AKS) não tem o conceito de senhas comuns por defeito e não fornece uma solução de gestão de identidade onde as contas e senhas regulares do utilizador possam ser armazenadas. Com a integração do Azure Ative Directory (Azure AD), pode conceder acesso baseado em funções aos recursos AKS dentro de um espaço de nome ou em todo o cluster. 

Realize consultas ad hoc para descobrir contas que são membros de grupos administrativos AKS com o módulo Azure AD PowerShell

- [Compreender opções de acesso e identidade para AKS](concepts-identity.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Integre a autenticação do utilizador para os clusters Azure Kubernetes Service (AKS) com O Diretório Ativo Azure (Azure AD). Inscreva-se num cluster AKS utilizando um token de autenticação AD Azure. Configure o controlo de acesso baseado em funções (Kubernetes RBAC) para acesso administrativo a informações e permissões de configuração de Kubernetes (kubeconfig), espaços de nome e recursos de cluster. 

Criar políticas e procedimentos em torno da utilização de contas administrativas dedicadas. Implementar recomendações de Gestão de Identidade e Acesso do Centro de Segurança.

- [Como monitorizar a identidade e o acesso com o Azure Security Center](../security-center/security-center-identity-access.md)

- [Controlar o acesso aos recursos de cluster](azure-ad-rbac.md)

- [Utilize controlos de acesso baseados em funções Azure](control-kubeconfig-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: Utilize um único sinal de acesso para o Serviço Azure Kubernetes (AKS) com a autenticação integrada Azure Ative Directory (Azure AD) para um cluster AKS.

- [Como ver registos, eventos e métricas de kubernetes em tempo real](../azure-monitor/containers/container-insights-livedata-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Integrar autenticação para o Serviço Azure Kubernetes (AKS) com diretório ativo Azure (Azure AD). 

Ativar a autenticação multifactor AZure AD e seguir as recomendações de Gestão de Identidade e Acesso do Centro de Segurança.

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize uma estação de trabalho de acesso privilegiado (PAW), com autenticação multi-factor (MFA), configurada para iniciar sessão nos clusters e recursos conexos do Serviço Azure Kubernetes especificados ( AKS).

- [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como permitir a autenticação multi-factor (MFA) em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança do Azure Ative (Azure AD) com a autenticação integrada Azure AD para o Serviço Azure Kubernetes (AKS). Os alertas podem ser gerados quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança para monitorizar a atividade de identidade e acesso.

- [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorizar a identidade e a atividade de acesso dos utilizadores no Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Gerir os recursos do Azure apenas a partir de locais aprovados

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso aos clusters do Serviço Azure Kubernetes (AKS) a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões. Isto requer autenticação integrada para AKS com Diretório Ativo Azure (Azure AD).

Limitar o acesso ao servidor AKS API a partir de um conjunto limitado de intervalos de endereços IP, uma vez que recebe pedidos para executar ações no cluster para criar recursos ou escalar o número de nós. 

- [Acesso seguro ao servidor API utilizando intervalos de endereços IP autorizados no Serviço Azure Kubernetes (AKS)](api-server-authorized-ip-ranges.md)

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como o sistema central de autenticação e autorização do Serviço Azure Kubernetes (AKS). O Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito e sais, hashes e armazena de forma segura as credenciais dos utilizadores.

Utilize as funções incorporadas AKS com controlo de acesso baseado em funções Azure (Azure RBAC) - Colaborador e Proprietário de Políticas de Recursos, para operações de atribuição de políticas ao seu cluster Kubernetes

- [Descrição Geral do Azure Policy](../governance/policy/overview.md)

- [Como integrar a AZure AD com a AKS](azure-ad-integration-cli.md)

- [Integre a Azure AD gerida pela AKS](managed-aad.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: Utilize relatórios de segurança do Azure Ative (Azure AD) com a autenticação integrada Azure AD para o Serviço Azure Kubernetes (AKS). Procure registos Azure AD para ajudar a descobrir contas velhas. 

Execute revisões de acesso à identidade Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. Remediar recomendações de Identidade e Acesso do Centro de Segurança.

Esteja atento às funções utilizadas para fins de apoio ou resolução de problemas. Por exemplo, quaisquer ações de cluster tomadas pelo suporte da Microsoft (com o consentimento do utilizador) são feitas sob um papel de "editar" de Kubernetes incorporado do nome aks-support-rolebining. O suporte AKS é habilitado com esta função a editar a configuração do cluster e recursos para resolver problemas e diagnosticar problemas de cluster. No entanto, este papel não pode modificar permissões nem criar papéis ou encadernações de papéis. Este acesso de função só é habilitado em bilhetes de apoio ativos com acesso just-in-time (JIT).
 
- [Access and identity options for Azure Kubernetes Service (AKS)](concepts-identity.md) (Opções de acesso e de identidade do Azure Kubernetes Service (AKS))

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

- [Como monitorizar a identidade e a atividade de acesso do utilizador no Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Integrar a autenticação do utilizador para o Serviço Azure Kubernetes (AKS) com o Azure Ative Directory (Azure AD). Crie Definições de Diagnóstico para Azure AD, enviando os registos de auditoria e de login para um espaço de trabalho Azure Log Analytics. Configure os alertas desejados (como quando uma conta desativada tenta entrar) dentro de um espaço de trabalho Azure Log Analytics.
- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como criar, visualizar e gerir alertas de registo usando o Azure Monitor](../azure-monitor/alerts/alerts-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Integrar a autenticação do utilizador para o Serviço Azure Kubernetes (AKS) com o Azure Ative Directory (Azure AD). Utilize a funcionalidade de Deteção de Riscos e Proteção de Identidade da Azure AD para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades dos utilizadores. Ingerir dados no Azure Sentinel para mais investigações baseadas nas necessidades do negócio.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas em recursos relacionados com as implementações do Serviço Azure Kubernetes (AKS) para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Etiquetas de atualização para clusters geridos](/rest/api/aks/managedclusters/updatetags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Isolar logicamente equipas e cargas de trabalho no mesmo cluster com o Serviço Azure Kubernetes (AKS) para fornecer o menor número de privilégios, atendidos aos recursos exigidos por cada equipa. 

Use o espaço de nome em Kubernetes para criar um limite de isolamento lógico. Considere implementar funcionalidades adicionais de Kubernetes para isolamento e multi-arrendamento, tais como, agendamento, networking, autenticação/autorização e contentores.

Implementar subscrições separadas e/ou grupos de gestão para ambientes de desenvolvimento, teste e produção. Separe os agrupamentos AKS com a ligação em rede, implantando-os em redes virtuais distintas, que são marcadas adequadamente.

- [Conheça as melhores práticas para o isolamento do cluster em AKS](operator-best-practices-cluster-isolation.md)

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Compreender as melhores práticas para a conectividade da rede e segurança em AKS](operator-best-practices-network.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Utilize uma solução de terceiros do Azure Marketplace em perímetros de rede que monitorize para transferência não autorizada de informações sensíveis e bloqueie tais transferências enquanto alerta os profissionais de segurança da informação.

A Microsoft gere a plataforma subjacente e trata todos os conteúdos do cliente como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Lista de portas, endereços e nomes de domínio necessários para a funcionalidade AKS](limit-egress-traffic.md)

- [Como configurar definições de diagnóstico para firewall Azure](../firewall/firewall-diagnostics.md)

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Crie um controlador de entrada HTTPS e utilize os seus próprios certificados TLS (ou opcionalmente, Vamos Encriptar) para as suas implementações do Serviço Azure Kubernetes (AKS). 

O tráfego de saída de Kubernetes é encriptado por padrão HTTPS/TLS. Reveja qualquer tráfego de saída potencialmente não encriptado das suas instâncias AKS para monitorização adicional. Isto pode incluir tráfego NTP, tráfego DNS, tráfego HTTP para recuperar atualizações em alguns casos. 

- [Como criar um controlador de entrada HTTPS na AKS e utilizar os seus próprios certificados TLS](ingress-own-tls.md)

- [Como criar um controlador de entrada HTTPS na AKS com Let's Encrypt](ingress-tls.md)

- [Lista de potenciais portos e protocolos de saída utilizados pela AKS](limit-egress-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As características de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Storage ou para calcular recursos. Implementar solução de terceiros, se necessário para efeitos de conformidade.
A Microsoft gere a plataforma subjacente e trata todos os conteúdos do cliente como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. 

Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Utilizar o Azure RBAC para gerir o acesso aos recursos

**Orientação**: Utilize o sistema de autorização de acesso baseado em funções Azure (Azure RBAC) construído no Azure Resource Manager para fornecer uma gestão de acesso de grãos finos dos recursos Azure.

Configure Azure Kubernetes Service (AKS) para utilizar o Azure Ative Directory (Azure AD) para a autenticação do utilizador. Inscreva-se num cluster AKS utilizando um token de autenticação Azure AD utilizando esta configuração. 

Utilize as funções incorporadas AKS com o Azure RBAC- Colaborador e Proprietário da Política de Recursos, para operações de atribuição de políticas no seu cluster AKS

- [Como controlar o acesso aos recursos de cluster usando identidades Azure RBAC e Azure AD em AKS](azure-ad-rbac.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 4.6](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-4-6.md)]

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: As características de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Storage ou para calcular recursos. Implementar solução de terceiros, se necessário para efeitos de conformidade.
A Microsoft gere a plataforma subjacente e trata todos os conteúdos do cliente como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Os dois tipos primários de armazenamento fornecidos para volumes no Serviço Azure Kubernetes (AKS) são apoiados por Discos Azure ou Ficheiros Azure. Ambos os tipos de armazenamento utilizam encriptação do Serviço de Armazenamento Azure (SSE), que encripta dados em repouso para melhorar a segurança. Por predefinição, os dados são encriptados com as teclas geridas pela Microsoft.

A encriptação em repouso utilizando chaves geridas pelo cliente está disponível para encriptar tanto o SISTEMA como os discos de dados em clusters AKS para controlo adicional sobre chaves de encriptação. Os clientes são responsáveis por atividades-chave de gestão, tais como backup e rotação chave. Os discos não podem ser encriptados utilizando a encriptação do disco Azure ao nível do nó AKS.

- [Melhores práticas para armazenamento e backups no Serviço Azure Kubernetes (AKS)](operator-best-practices-storage.md)

- [Traga as suas próprias chaves (BYOK) com discos Azure no Serviço Azure Kubernetes (AKS)](azure-disk-customer-managed-keys.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure para os contentores para monitorizar o desempenho das cargas de trabalho dos contentores implantados em aglomerados de Kubernetes geridos alojados no Serviço Azure Kubernetes (AKS). 

Alertas de configuração para notificação proactiva ou criação de registo quando a CPU e a utilização da memória em nós ou contentores excederem os limiares definidos, ou quando ocorre uma alteração do estado de saúde no cluster da infraestrutura ou nos nós de saúde. 

Utilize o Registo de Atividades Azure para monitorizar os seus clusters AKS e recursos relacionados a um nível elevado. Integre com a Prometheus para visualizar as métricas de aplicação e carga de trabalho que recolhe de nós e Kubernetes usando consultas para criar alertas personalizados, dashboards e análise detalhada detalhada.

- [Compreender o Monitor Azure para contentores](../azure-monitor/containers/container-insights-overview.md)

- [Como ativar o Monitor Azure para contentores](../azure-monitor/containers/container-insights-onboard.md)

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte o [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Utilize o Centro de Segurança para monitorizar o registo do seu contentor Azure, incluindo instâncias do Serviço Azure Kubernetes (AKS) para vulnerabilidades. Ativar o pacote de registos de contentores no Centro de Segurança para garantir que o Centro de Segurança está pronto para digitalizar imagens que são empurradas para o registo.

Seja notificado no painel do Centro de Segurança quando os problemas forem encontrados após o Centro de Segurança digitalizar a imagem usando o Qualys. A funcionalidade do pacote de registos de contentores proporciona uma visibilidade mais profunda às vulnerabilidades das imagens utilizadas nos registos baseados em Azure Resource Manager. 

Utilize o Security Center para recomendações accitivas para todas as vulnerabilidades. Estas recomendações incluem uma classificação de gravidade e orientação para a reparação. 

- [Melhores práticas para gestão de imagem de contentores e segurança no Serviço Azure Kubernetes (AKS)](../security-center/defender-for-container-registries-introduction.md)

- [Compreender as melhores práticas para a gestão de imagem de contentores e segurança em AKS](operator-best-practices-container-image-management.md)

- [Compreender a integração do registo de contentores com o Azure Security Center](../security-center/defender-for-container-registries-introduction.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: As atualizações de segurança são automaticamente aplicadas aos nós Linux para proteger os clusters do Serviço Azure Kubernetes (AKS) do cliente. Estas atualizações incluem correções de segurança de SO ou atualizações de kernel. 

Note que o processo para manter os nós do Windows Server atualizados difere dos nós que executam o Linux, uma vez que os nós do servidor do Windows não recebem atualizações diárias. Em vez disso, os clientes precisam de realizar uma atualização nas piscinas de nó do Windows Server nos seus clusters AKS, que implementa novos nós com a mais recente imagem do Servidor de Janelas de base e patches utilizando o painel de controlo Azure ou o CLI Azure. Estas atualizações contêm melhorias de segurança ou funcionalidades na AKS.

- [Entenda como as atualizações são aplicadas aos nódoas de cluster AKS que executam o Linux](node-updates-kured.md)

- [Como atualizar uma piscina de nó AKS para clusters AKS que usam os nóns do Windows Server](./use-multiple-node-pools.md#upgrade-a-node-pool)

- [Azure Kubernetes Service (AKS) upgrades de imagem de nó](node-image-upgrade.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implementar uma solução automatizada de gestão de patchs para títulos de software de terceiros

**Orientação**: Implemente um processo manual para garantir que as aplicações de terceiros do nó de grupo Azure Kubernetes (AKS) permaneçam corrigidas durante o período de vida útil do cluster. Isto pode exigir ativar atualizações automáticas, monitorizar os nós ou executar reboots periódicos.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 5.3](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-5-3.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: O Centro de Segurança de Exportação verifica os resultados em intervalos consistentes e compara os resultados para verificar se as vulnerabilidades foram remediadas.

Utilize o cmdlet PowerShell "Get-AzSecurityTask" para automatizar a recuperação de tarefas de segurança que o Security Center recomenda que execute de forma a reforçar a sua postura de segurança e a sua vulnerabilidade de remediação.

- [Como usar o PowerShell para ver vulnerabilidades descobertas pelo Azure Security Center](/powershell/module/az.security/get-azsecuritytask)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Utilize a classificação de severidade fornecida pelo Security Center para priorizar a reparação de vulnerabilidades. 

Utilize o Sistema Comum de Pontuação de Vulnerabilidade (CVSS) (ou outro sistema de pontuação fornecido pela sua ferramenta de digitalização) se utilizar uma ferramenta de avaliação de vulnerabilidade incorporada (como Qualys ou Rapid7, oferecida pela Azure).

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, e assim por diante) dentro das suas subscrições. Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e que é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Resource Graph, é altamente recomendado criar e utilizar recursos baseados em Recursos Azure.

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas nos recursos Azure com metadados para organizar logicamente numa taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear ativos. 

Aplique manchas, etiquetas ou etiquetas ao criar um conjunto de nó de nó Azure Kubernetes Service (AKS). Todos os nós dentro dessa piscina de nós também herdarão essa mancha, etiqueta ou etiqueta.

As manchas, etiquetas ou etiquetas podem ser utilizadas para conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados das subscrições em tempo útil.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e utilizadores Tags](../azure-resource-manager/management/tag-resources.md)

- [Clusters Geridos - Etiquetas de atualização](/rest/api/aks/managedclusters/updatetags)

- [Especifique uma mancha, etiqueta ou etiqueta para uma piscina de nó](./use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário dos recursos aprovados do Azure

**Orientação**: Defina uma lista de recursos Azure aprovados e software aprovado para recursos de computação baseados nas necessidades organizacionais do negócio.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:
-   Tipos de recursos não permitidos 

-   Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro das suas subscrições. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados com base nos requisitos organizacionais do negócio.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Utilize funcionalidades de rastreio e inventário de alterações de automatização da Azure para descobrir o software instalado no seu ambiente. 

Recolher e visualizar o inventário para software, ficheiros, daemons Linux, serviços Windows e chaves do Registo do Windows nos seus computadores e monitor para aplicações de software não aprovadas. 

Acompanhe as configurações das suas máquinas para ajudar a identificar problemas operacionais em todo o seu ambiente e compreenda melhor o estado das suas máquinas.

- [Como ativar o inventário de máquinas virtuais Azure](../automation/automation-tutorial-installed-software.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Utilize funcionalidades de rastreio e inventário de alterações de automatização da Azure para descobrir o software instalado no seu ambiente. 

Recolher e visualizar o inventário para software, ficheiros, daemons Linux, serviços Windows e chaves do Registo do Windows nos seus computadores e monitor para aplicações de software não aprovadas. 

Acompanhe as configurações das suas máquinas para ajudar a identificar problemas operacionais em todo o seu ambiente e compreenda melhor o estado das suas máquinas.

- [Como ativar o inventário de máquinas virtuais Azure](../automation/automation-tutorial-installed-software.md)

- [Como utilizar o Monitor de Integridade do Ficheiro](../security-center/security-center-file-integrity-monitoring.md)

- [Compreenda o rastreio da mudança de Azure](../automation/change-tracking/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Utilize funcionalidades de rastreio e inventário de alterações de automatização da Azure para descobrir o software instalado no seu ambiente. 

Recolher e visualizar o inventário para software, ficheiros, daemons Linux, serviços Windows e chaves do Registo do Windows nos seus computadores e monitor para aplicações de software não aprovadas. 

Acompanhe as configurações das suas máquinas para ajudar a identificar problemas operacionais em todo o seu ambiente e compreenda melhor o estado das suas máquinas.

Ativar a análise de Aplicações Adaptativas no Centro de Segurança para aplicações que existam no seu ambiente.

- [Como ativar o inventário de máquinas virtuais Azure](../automation/automation-tutorial-installed-software.md)

- [Como utilizar os controlos de aplicações adaptativos do Centro de Segurança Azure](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro das suas subscrições. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação**: Use a Política Azure para impor restrições ao tipo de recursos que podem ser criados nas suas subscrições utilizando definições políticas incorporadas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".
- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts em recursos computacional

**Orientação**: O Serviço Azure Kubernetes (AKS) em si não fornece uma solução de gestão de identidade onde as contas de utilizador regulares e palavras-passe são armazenadas. Em vez disso, utilize o Azure Ative Directory (Azure AD) como solução de identidade integrada para os seus clusters AKS.

Conceder aos utilizadores ou grupos acesso aos recursos de Kubernetes dentro de um espaço de nome ou em todo o cluster usando a integração AD do Azure.

Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros dos seus grupos administrativos AKS e usá-lo para conciliar o acesso regularmente. Utilize o Azure CLI para operações como 'Obtenha credenciais de acesso para um cluster de Kubernetes gerido. Implementar recomendações de Gestão de Identidade e Acesso do Centro de Segurança.

- [Gerir aKS com Azure CLI](/cli/azure/aks)

- [Compreender a integração da AKS e da AD Azure](concepts-identity.md)

- [Como integrar a AKS com Azure AD](azure-ad-integration-cli.md)

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Como monitorizar a identidade e o acesso com o Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Utilize as funcionalidades do Serviço Azure Kubernetes (AKS) para isolar logicamente equipas e cargas de trabalho no mesmo cluster para o menor número de privilégios, aparentados nos recursos exigidos por cada equipa. 

Implementar espaço de nome em Kubernetes para criar um limite de isolamento lógico. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.ContainerService" para criar políticas personalizadas para auditar ou impor a configuração das suas instâncias do Serviço Azure Kubernetes (AKS). 

Rever e implementar funcionalidades e considerações adicionais de Kubernetes para isolamento e multi-arrendamento para incluir o seguinte: agendamento, networking, autenticação/autorização e contentores. Utilize também assinaturas e grupos de gestão separados para desenvolvimento, teste e produção. Separe os agrupamentos AKS com redes virtuais, sub-redes que são marcadas adequadamente e protegidas com uma Firewall de Aplicação Web (WAF).

- [Conheça as melhores práticas para o isolamento do cluster em AKS](operator-best-practices-cluster-isolation.md)

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Compreender as melhores práticas para a conectividade da rede e segurança em AKS](operator-best-practices-network.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.ContainerService" para criar políticas personalizadas para auditar ou impor a configuração das suas instâncias do Serviço Azure Kubernetes (AKS). Utilize definições de Política Azure incorporadas.

Exemplos de definições políticas incorporadas para a AKS incluem:

- Impor entrada HTTPS no cluster do Kubernetes

- As gamas IP autorizadas devem ser definidas nos Serviços Kubernetes

- O Controlo de Acesso baseado em funções (RBAC) deve ser utilizado nos serviços kubernetes

- Garantir apenas imagens de contentor permitidas no cluster do Kubernetes

Exporte um modelo da sua configuração AKS na Notação de Objetos JavaScript (JSON) com o Azure Resource Manager. Reveja-o periodicamente para garantir que estas configurações cumprem os requisitos de segurança para a sua organização. Utilize as recomendações do Azure Security Center como uma base de configuração segura para os seus recursos Azure. 

- [Como configurar e gerir as políticas de segurança do pod AKS](use-pod-security-policies.md)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Os clusters Azure Kubernetes Clusters (AKS) são implantados em máquinas virtuais hospedeiras com um SISTEMA otimizado de segurança. O hospedeiro tem etapas adicionais de endurecimento de segurança incorporadas nele para reduzir a área de superfície de ataque e permite a colocação de recipientes de forma segura. 

O Azure aplica patches diários (incluindo patches de segurança) a anfitriões de máquinas virtuais AKS com alguns patches que requerem um reboot. Os clientes são responsáveis por agendar reboots de anfitriões de máquina virtual AKS conforme necessário. 

- [Endurecimento de segurança para o número de agente da AKS anfitrião OS](security-hardened-vm-host-image.md)

- [Compreenda o endurecimento da segurança nos anfitriões de máquinas virtuais AKS](security-hardened-vm-host-image.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Fixe o seu cluster Azure Kubernetes Service (AKS) utilizando políticas de segurança do casulo.  Limite quais as cápsulas que podem ser programadas para melhorar a segurança do seu cluster. 

As cápsulas que solicitam recursos que não são permitidos não podem funcionar no cluster AKS. 

Utilize também a Política Azure [negar] e [implementar se não existir] efeitos para impor definições seguras para os recursos Azure relacionados com as suas implementações AKS (tais como Redes Virtuais, Subnetas, Firewalls Azure, Contas de Armazenamento, e assim por diante). 

Crie definições personalizadas da Política Azure utilizando pseudónimos a partir dos seguintes espaços de nome: 

- Microsoft.ContainerService

- Microsoft.Network

Informações adicionais estão disponíveis nos links referenciados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Os clusters Azure Kubernetes Service (AKS) são implantados em máquinas virtuais hospedeiras com um SISTEMA otimizado de segurança. O hospedeiro tem etapas adicionais de endurecimento de segurança incorporadas nele para reduzir a área de superfície de ataque e permite a colocação de recipientes de forma segura. 

Consulte a lista de controlos do Center for Internet Security (CIS) que são incorporados no sistema operativo hospedeiro.  

- [Endurecimento de segurança para o número de agente da AKS anfitrião OS](security-hardened-vm-host-image.md)

- [Compreender a configuração estatal dos clusters AKS](./concepts-clusters-workloads.md#control-plane)

- [Compreenda o endurecimento da segurança nos anfitriões de máquinas virtuais AKS](security-hardened-vm-host-image.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Utilize o Azure Repos para armazenar e gerir de forma segura as suas configurações se utilizar definições personalizadas da Política Azure. Exporte um modelo da sua configuração do Serviço Azure Kubernetes (AKS) na Notação de Objetos JavaScript (JSON) com o Azure Resource Manager. Reveja-a periodicamente para garantir que as configurações cumprem os requisitos de segurança para a sua organização.

Implementar soluções de terceiros como a Terraform para criar um ficheiro de configuração que declare os recursos para o cluster Kubernetes. Pode endurecer a sua implementação AKS implementando as melhores práticas de segurança e armazenar a sua configuração como código num local seguro.

- [Definir um cluster do Kubernetes](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

- [Endurecimento de segurança para o número de agente da AKS anfitrião OS](security-hardened-vm-host-image.md)

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável ao Serviço Azure Kubernetes (AKS). A AKS fornece um Sistema Operativo de anfitriões otimizado de segurança (OS) por padrão. Não existe nenhuma opção atual para selecionar um sistema operativo alternativo ou personalizado.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Use a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições utilizando definições de políticas incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.ContainerService". 

Crie políticas personalizadas para auditar e impor configurações do sistema. Desenvolver um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como usar pseudónimos](../governance/policy/concepts/definition-structure.md#aliases)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

**Orientação**: Os clusters Azure Kubernetes Service (AKS) são implantados em máquinas virtuais hospedeiras com um SISTEMA otimizado de segurança. O hospedeiro tem etapas adicionais de endurecimento de segurança incorporadas nele para reduzir a área de superfície de ataque e permite a colocação de recipientes de forma segura. 

Consulte a lista de controlos do Center for Internet Security (CIS) que são incorporados em anfitriões AKS.  

- [Endurecimento de segurança para o número de agente da AKS anfitrião OS](security-hardened-vm-host-image.md)

- [Compreenda o endurecimento da segurança nos anfitriões de máquinas virtuais AKS](security-hardened-vm-host-image.md)

- [Compreender a configuração estatal dos clusters AKS](./concepts-clusters-workloads.md#control-plane)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize o Security Center para efetuar verificações de base para recursos relacionados com as suas implementações do Serviço Azure Kubernetes (AKS). Os recursos exemplos incluem, mas não se limitam ao cluster AKS em si, a rede virtual onde o cluster AKS foi implantado, a Conta de Armazenamento Azure usada para rastrear o estado terraform, ou instâncias do Azure Key Vault sendo usadas para as chaves de encriptação para o sistema operativo do cluster AKS e discos de dados.

- [Como remediar recomendações no Centro de Segurança Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Utilize recomendações de contentores do Centro de Segurança na &amp; secção "Aplicações computacional" para realizar análises de linha de base para os seus clusters Azure Kubernetes Service (AKS). 

Seja notificado no painel do Centro de Segurança quando forem encontrados problemas de configuração ou vulnerabilidades. Isto requer permitir o pacote opcional de registos de contentores que permite ao Centro de Segurança digitalizar a imagem.  

- [Compreender as recomendações do contentor do Centro de Segurança do Azure](../security-center/container-security.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Integre o Cofre da Chave Azure com um cluster Azure Kubernetes Service (AKS) utilizando uma unidade FlexVolume. Utilize o Cofre de Chaves Azure para armazenar e rodar regularmente segredos como credenciais, chaves de conta de armazenamento ou certificados. O controlador FlexVolume permite que o cluster AKS recupere as credenciais do Key Vault e as forneça de forma segura apenas à cápsula de solicitação. Utilize uma cápsula de identidade gerida para solicitar o acesso ao Key Vault e recuperar as credenciais necessárias através do controlador FlexVolume. Certifique-se de que o cofre da chave é excluído suave. 

Limite a exposição credencial não definindo credenciais no seu código de aplicação. 

Evite a utilização de credenciais fixas ou partilhadas. 

- [Conceitos de segurança para aplicações e clusters no Azure Kubernetes Service (AKS)](concepts-security.md)

- [Como usar o Key Vault com o seu cluster AKS](./developer-best-practices-pod-security.md#limit-credential-exposure)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Não defina credenciais no seu código de aplicação como uma boa prática de segurança. Utilize identidades geridas para os recursos da Azure para permitir que uma cápsula se autente contra qualquer serviço em Azure que o suporte, incluindo o Azure Key Vault. O casulo é atribuído a uma Identidade Azure para autenticar o Azure Ative Directory (Azure AD) e receber um token digital que pode ser apresentado a outros serviços da Azure que verificam se a cápsula está autorizada a aceder ao serviço e a executar as ações necessárias.

Note que as identidades geridas pelo Pod destinam-se apenas a ser utilizadas apenas com cápsulas Linux e imagens de contentores. Provision Azure Key Vault para armazenar e recuperar chaves e credenciais digitais. Chaves como as utilizadas para encriptar discos de SISTEMA, os dados do cluster AKS podem ser armazenados no Cofre da Chave Azure.

Os princípios de serviço também podem ser usados em clusters AKS. No entanto, os agrupamentos que utilizam os princípios de serviço podem eventualmente chegar a um estado em que o diretor de serviço deve ser renovado para manter o cluster a funcionar. Gerir os diretores de serviços adiciona complexidade, e é por isso que é mais fácil usar identidades geridas em vez disso. Os mesmos requisitos de permissão aplicam-se tanto aos princípios de serviço como às identidades geridas.

- [Compreender identidades geridas e cofre-chave com serviço Azure Kubernetes (AKS)](./developer-best-practices-pod-security.md#limit-credential-exposure)

- [Identidade do Azure Ad Pod](https://github.com/Azure/aad-pod-identity)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner Credencial também encoraja a movimentação de credenciais descobertas para locais mais seguros, como o Azure Key Vault, com recomendações.

Limite a exposição credencial não definindo credenciais no seu código de aplicação. e evitar o uso de credenciais partilhadas. O Azure Key Vault deve ser utilizado para armazenar e recuperar chaves e credenciais digitais. Utilize identidades geridas para os recursos da Azure para permitir que o seu pod solicite acesso a outros recursos. 

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Melhores práticas para segurança de pod](developer-best-practices-pod-security.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte a [Referência de Segurança Azure: Defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Utilizar software antimalware gerido centralmente

**Orientação**: AKS gere o ciclo de vida e as operações dos nós de agente em seu nome - modificando os recursos iaaS associados aos nós do agente não é suportado. No entanto, para os nós Linux pode utilizar conjuntos daemon para instalar software personalizado como uma solução anti-malware.

- [Guia de referência de alertas de segurança](../security-center/alerts-reference.md)

- [Alertas para contentores - Agrupamentos de serviço Azure Kubernetes](../security-center/alerts-reference.md#alerts-akscluster)

- [AKS partilha responsabilidade e Conjuntos Daemon](./support-policies.md#shared-responsibility)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: Pré-digitalize quaisquer ficheiros que sejam enviados para os seus recursos AKS. Utilize a deteção de ameaças do Security Center para os serviços de dados detetarem malware enviado para contas de armazenamento se utilizar uma Conta de Armazenamento Azure como uma loja de dados ou para rastrear o estado terraform para o seu cluster AKS. 

- [Compreenda a deteção de ameaças do Azure Security Center para serviços de dados](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Assegurar que o software e assinaturas antimalware sejam atualizados

**Orientação**: AKS gere o ciclo de vida e as operações dos nós de agente em seu nome - modificando os recursos iaaS associados aos nós do agente não é suportado. No entanto, para os nós Linux pode utilizar conjuntos daemon para instalar software personalizado como uma solução anti-malware.

- [Guia de referência de alertas de segurança](../security-center/alerts-reference.md)

- [Alertas para contentores - Agrupamentos de serviço Azure Kubernetes](../security-center/alerts-reference.md#alerts-akscluster)

- [AKS partilha responsabilidade e Conjuntos Daemon](./support-policies.md#shared-responsibility)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Utilize uma ferramenta adequada para o seu tipo de armazenamento, como o Velero, que pode fazer o backs volumes persistentes juntamente com recursos e configurações adicionais do cluster. Periodicamente, verifique a integridade e segurança daqueles reforços. 

Remova o estado das suas aplicações antes da cópia de segurança. Nos casos em que tal não possa ser feito, faça o back up dos dados de volumes persistentes e teste regularmente as operações de restauro para verificar a integridade dos dados e os processos necessários.

- [Melhores práticas para armazenamento e backups em AKS](operator-best-practices-storage.md)

- [Melhores práticas para a continuidade do negócio e recuperação de desastres na AKS](operator-best-practices-multi-region.md)

- [Compreender a recuperação do local do Azure](../site-recovery/site-recovery-overview.md)

- [Como configurar Velero em Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Utilize uma ferramenta adequada para o seu tipo de armazenamento, como o Velero, que pode fazer o backs volumes persistentes juntamente com recursos e configurações adicionais do cluster. 

Execute cópias de segurança automáticas regulares de certificados de cofre chave, chaves, contas de armazenamento gerido e segredos, com comandos PowerShell. 

- [Como apoiar certificados de cofre de chaves](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Como apoiar chaves do cofre](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Como apoiar contas de armazenamento geridos do Cofre do Cofre](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Como apoiar segredos do Cofre de Chaves](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [Como ativar o backup do Azure](../backup/index.yml)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Execute periodicamente a restauração de dados do conteúdo no Velero Backup. Se necessário, teste restaurar para uma rede virtual isolada.

Periodicamente realizar a restauração de dados de Certificados de Cofre Chave, Chaves, Contas de Armazenamento Gerido e Segredos, com comandos PowerShell.

- [Como restaurar certificados de cofre chave](/powershell/module/az.keyvault/restore-azkeyvaultcertificate?amp;preserve-view=true&view=azps-4.8.0)

- [Como restaurar chaves do cofre](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-4.8.0)

- [Como restaurar contas de armazenamento gerido de cofre chave](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Como restaurar os segredos do cofre da chave](/powershell/module/az.keyvault/restore-azkeyvaultsecret?amp;preserve-view=true&view=azps-4.8.0)

- [Como recuperar ficheiros da cópia de segurança da Azure Virtual Machine](../backup/backup-azure-restore-files-from-vm.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Utilize uma ferramenta adequada para o seu tipo de armazenamento, como o Velero, que pode fazer o backs volumes persistentes juntamente com recursos e configurações adicionais do cluster. 

Ativar Soft-Delete no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa se o Azure Key Vault estiver a ser utilizado para implantações do Serviço Azure Kubernetes (AKS).

- [Compreender encriptação do serviço de armazenamento Azure](../storage/common/storage-service-encryption.md)

- [Como permitir Soft-Delete em Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Guia de manuseamento de incidentes de segurança informática do NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: Priorize quais os alertas que devem ser investigados primeiro com o Centro de Segurança atribuído à gravidade dos alertas. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.
Marque claramente as subscrições (por exemplo, produção, não produção) e crie um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja os planos de resposta a incidentes conforme necessário.

- [Guia de Programas de Teste, Formação e Exercício para Planos e Capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada. 

Rever os incidentes, após o facto, para garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Alertas e recomendações do Centro de Segurança da Exportação utilizando a sua funcionalidade de Exportação Contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. 

Escolha o conector de dados do Centro de Segurança para transmitir os alertas para a Azure Sentinel, conforme necessário e baseado nos requisitos de negócio organizacionais.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança para ativar automaticamente respostas através de "Aplicações lógicas" em alertas e recomendações de segurança.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios da equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft. Informações adicionais sobre a estratégia da Microsoft e a execução de testes de penetração em Red Teaming e sites ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft nas ligações referenciadas.

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)