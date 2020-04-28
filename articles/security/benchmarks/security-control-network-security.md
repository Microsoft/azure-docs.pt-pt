---
title: Controlo de Segurança Azure - Segurança da Rede
description: Segurança da Rede de Controlo de Segurança Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: dad01212be3589af7167082ff22c624fa776772a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193127"
---
# <a name="security-control-network-security"></a>Controlo de Segurança: Segurança da Rede

As recomendações de segurança da rede concentram-se em especificar quais os protocolos de rede, portas TCP/UDP e serviços ligados à rede são permitidos ou negados aos serviços Azure.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure dentro das redes virtuais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1, 14.2, 14.3 | Cliente |

Certifique-se de que todas as implementações de subredes da Rede Virtual têm um Grupo de Segurança da Rede aplicado com controlos de acesso à rede específicos das portas e fontes fidedignas da sua aplicação. Quando disponível, utilize pontos finais privados com Private Link para garantir os seus recursos de serviço Azure à sua rede virtual, alargando a identidade VNet ao serviço. Quando os pontos finais privados e o Link Privado não estiverem disponíveis, utilize pontos finais de serviço. Para obter requisitos específicos de serviço, consulte a recomendação de segurança para esse serviço específico. 

Em alternativa, se tiver um caso de utilização específico, o requisito pode ser cumprido através da implementação do Firewall Azure.

- [Compreender pontos finais do serviço de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

- [Compreender ligação privada azure](https://docs.microsoft.com/azure/private-link/private-link-overview)

- [Como criar uma Rede Virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Como criar um NSG com uma configuração de segurança](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [Como implantar e configurar firewall Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, subredes e NICs

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.2 | 9.3, 12.2, 12.8 | Cliente |

Utilize o Azure Security Center e siga as recomendações de proteção da rede para ajudar a proteger os seus recursos de rede em Azure. Ative os registos de fluxo do NSG e envie registos numa Conta de Armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho de Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como ativar os registos de fluxo nsg](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Como ativar e utilizar o Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

## <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.3 | 9.5 | Cliente |

Implemente o Firewall de Aplicação Web Azure (WAF) em frente a aplicações web críticas para uma inspeção adicional do tráfego de entrada. Ative a definição de diagnóstico para WAF e ingere os registos numa Conta de Armazenamento, Hub de Eventos ou log analytics workspace.

- [Como implantar o Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.4 | 12.3 | Cliente |

Ative a proteção Padrão DDoS nas suas Redes Virtuais Azure para se proteger contra ataques DDoS. Utilize o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP maliciosos conhecidos.

Implementar o Azure Firewall em cada um dos limites de rede da organização com a Threat Intelligence habilitada e configurada para "Alertar e negar" para tráfego de rede malicioso.

Utilize o Azure Security Center Just In Time Network acesso para configurar NSGs para limitar a exposição de pontos finais a endereços IP aprovados por um período limitado.

Utilize o Azure Security Center Adaptive Network Hardening para recomendar configurações de NSG que limitem portas e IPs de origem com base na inteligência real de tráfego e ameaças.

- [Como configurar a proteção DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Como implantar o Firewall Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Compreender o Centro de Segurança Azure Inteligência Integrada de Ameaças](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Compreender o Centro de Segurança Azure Adaptive Network Hardening](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Compreender o Centro de Segurança Azure apenas no controlo de acesso à rede de tempo](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

## <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registo

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.5 | 12.5 | Cliente |

Ativar a captura de pacotes do Network Watcher para investigar atividades anómalas.

- [Como ativar o Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.6 | 12.6, 12.7 | Cliente |

Selecione uma oferta do Azure Marketplace que suporta a funcionalidade IDS/IPS com capacidades de inspeção de carga útil.  Se a deteção e/ou prevenção de intrusões com base na inspeção da carga útil não for um requisito, o Azure Firewall com a Threat Intelligence pode ser utilizado. A filtragem baseada na inteligência da Ameaça de Firewall Azure pode alertar e negar o tráfego de e para endereços e domínios ip maliciosos conhecidos. Os endereços e domínios IP são obtidos a partir do feed da Microsoft Threat Intelligence.

Implemente a solução de firewall da sua escolha em cada um dos limites de rede da sua organização para detetar e/ou negar tráfego malicioso.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Como implantar o Firewall Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Como configurar alertas com firewall Azure](https://docs.microsoft.com/azure/firewall/threat-intel)

## <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.7 | 12.9, 12.10 | Cliente |

Implemente o Portal de Aplicações Azure para aplicações web com HTTPS/TLS habilitados para certificados fidedignos.

- [Como implementar o Gateway de Aplicação](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- [Como configurar o Gateway de Aplicação para utilizar https](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

- [Compreender o equilíbrio de carga da camada 7 com os gateways de aplicação web do Azure](https://docs.microsoft.com/azure/application-gateway/overview)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.8 | 1.5 | Cliente |

Utilize etiquetas de serviço de rede virtual para definir controlos de acesso à rede em Grupos de Segurança da Rede ou firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Especificando o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Também pode utilizar grupos de segurança de aplicações para ajudar a simplificar a configuração complexa de segurança. Os grupos de segurança de aplicações permitem-lhe configurar a segurança de rede como uma extensão natural da estrutura de uma aplicação, possibilitando o agrupamento de máquinas virtuais e a definição de políticas de segurança de rede com base nesses grupos.

- [Compreender e usar etiquetas de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Compreender e utilizar grupos de segurança de aplicações](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.9 | 11.1 | Cliente |

Defina e implemente configurações de segurança padrão para recursos de rede com a Política Azure.

Também pode utilizar as Plantas Azure para simplificar as implementações azure em larga escala, embalagendo artefactos ambientais chave, tais como modelos de Gestor de Recursos Azure, controlos RBAC e políticas, numa única definição de blueprint. Pode aplicar o projeto a novas subscrições e a finas condições de controlo e gestão através da versão.

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Amostras de política azure para networking](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [Como criar uma Planta Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

## <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.10 | 11.2 | Cliente |

Utilize etiquetas para NSGs e outros recursos relacionados com a segurança da rede e fluxo de tráfego. Para regras nsg individuais, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições de Política Azure incorporadarelacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com Tags e notificá-lo de recursos não marcados existentes.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou realizar ações em recursos baseados nas suas Tags.

- [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Como criar uma Rede Virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Como criar um NSG com um Config de Segurança](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.11 | 11.3 | Cliente |

Utilize o Registo de Atividade seleções do Azure para monitorizar as configurações de recursos e detetar alterações nos seus recursos Azure. Crie alertas dentro do Monitor Azure que irão desencadear quando ocorrerem alterações aos recursos críticos.

- [Como visualizar e recuperar eventos de Registo de Atividade sinuosa do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Como criar alertas no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

## <a name="next-steps"></a>Passos seguintes

- Consulte o próximo Controlo de Segurança: [Registo e Monitorização](security-control-logging-monitoring.md)
