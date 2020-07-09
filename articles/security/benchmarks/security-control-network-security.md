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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82193127"
---
# <a name="security-control-network-security"></a>Controlo de Segurança: Segurança da rede

As recomendações de segurança da rede centram-se em especificar quais os protocolos de rede, portas TCP/UDP e serviços ligados à rede são permitidos ou impedidos de aceder aos serviços Azure.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1, 14.2, 14.3 | Cliente |

Certifique-se de que todas as implementações da sub-rede virtual têm um Grupo de Segurança de Rede aplicado com controlos de acesso à rede específicos das portas e fontes fidedignas da sua aplicação. Quando disponível, utilize pontos de terminação privados com Ligação Privada para garantir os seus recursos de serviço Azure à sua rede virtual, alargando a identidade VNet ao serviço. Quando os pontos finais privados e o Link Privado não estiverem disponíveis, utilize pontos de assistência. Para obter requisitos específicos de serviço, consulte a recomendação de segurança para esse serviço específico. 

Em alternativa, se tiver um caso de utilização específico, a exigência poderá ser satisfeita através da implementação do Azure Firewall.

- [Compreender pontos finais de serviço de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

- [Compreenda a ligação privada Azure](https://docs.microsoft.com/azure/private-link/private-link-overview)

- [Como criar uma Rede Virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Como criar um NSG com uma configuração de segurança](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [Como implantar e configurar firewall Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e NICs

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.2 | 9.3, 12.2, 12.8 | Cliente |

Utilize o Centro de Segurança Azure e siga as recomendações de proteção da rede para ajudar a proteger os recursos da sua rede em Azure. Ativar os registos de fluxo NSG e enviar registos para uma conta de armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como ativar registos de fluxo NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Como Ativar e utilizar a Análise de Tráfego](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

## <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.3 | 9.5 | Cliente |

Implemente o Azure Web Application Firewall (WAF) em frente a aplicações web críticas para inspeção adicional do tráfego de entrada. Ativar a Definição de Diagnóstico para WAF e ingerir registos numa conta de armazenamento, no Centro de Eventos ou no Espaço de Trabalho do Log Analytics.

- [Como implantar a Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.4 | 12.3 | Cliente |

Ativar a proteção DDoS Standard nas suas Redes Virtuais Azure para proteger contra ataques DDoS. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP maliciosos conhecidos.

Implemente o Azure Firewall em cada um dos limites de rede da organização com a Threat Intelligence habilitada e configurada a "alertar e negar" para tráfego de rede malicioso.

Utilize o Centro de Segurança Azure Apenas no Tempo O acesso à rede de configuração de NSGs para limitar a exposição dos pontos finais a endereços IP aprovados por um período limitado.

Use o Hardening de Rede Adaptável do Centro de Segurança Azure para recomendar configurações NSG que limitem portas e IPs de origem com base na inteligência real de tráfego e ameaça.

- [Como configurar a proteção DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Como implantar a Firewall do Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Compreender o Hardenive de Rede Adaptável do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Compreender o Centro de Segurança Azure Mesmo no Tempo Controlo de Acesso à Rede](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

## <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.5 | 12.5 | Cliente |

Habilitar a captura de pacotes do Network Watcher para investigar atividades anómalas.

- [Como ativar o Observador de Redes](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.6 | 12.6, 12.7 | Cliente |

Selecione uma oferta do Azure Marketplace que suporta a funcionalidade IDS/IPS com capacidades de inspeção de carga útil.  Se a deteção e/ou prevenção de intrusões baseada na inspeção de carga útil não for um requisito, a Azure Firewall com Threat Intelligence pode ser usada. A filtragem baseada em inteligência Azure Firewall Threat pode alertar e negar tráfego de e para endereços ip maliciosos conhecidos e domínios. Os endereços e domínios IP são obtidos a partir do feed de Inteligência de Ameaça da Microsoft.

Implemente a solução de firewall à sua escolha em cada um dos limites de rede da sua organização para detetar e/ou negar tráfego malicioso.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Como implantar a Firewall do Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Como configurar alertas com a Azure Firewall](https://docs.microsoft.com/azure/firewall/threat-intel)

## <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.7 | 12.9, 12.10 | Cliente |

Implementar gateway de aplicações Azure para aplicações web com HTTPS/TLS habilitado para certificados fidedignos.

- [Como implementar o Gateway de Aplicações](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- [Como configurar o Gateway de aplicações para utilizar HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

- [Compreenda o equilíbrio de carga da camada 7 com os gateways de aplicações web Azure](https://docs.microsoft.com/azure/application-gateway/overview)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.8 | 1.5 | Cliente |

Utilize tags de serviço de rede virtuais para definir controlos de acesso à rede em Grupos de Segurança de Rede ou firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Também pode utilizar grupos de segurança de aplicações para ajudar a simplificar a configuração complexa de segurança. Os grupos de segurança de aplicações permitem-lhe configurar a segurança de rede como uma extensão natural da estrutura de uma aplicação, possibilitando o agrupamento de máquinas virtuais e a definição de políticas de segurança de rede com base nesses grupos.

- [Compreender e utilizar tags de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Compreender e utilizar grupos de segurança de aplicações](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.9 | 11.1 | Cliente |

Definir e implementar configurações de segurança padrão para recursos de rede com a Política Azure.

Também pode usar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlos de RBAC e políticas, numa única definição de planta. Pode aplicar o projeto a novas subscrições e afinar o controlo e a gestão através da versão.

- [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Amostras da Política Azure para networking](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [Como criar uma Planta Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

## <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.10 | 11.2 | Cliente |

Utilize Tags para NSGs e outros recursos relacionados com a segurança da rede e fluxo de tráfego. Para regras individuais de NSG, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições de Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com Tags e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas Tags.

- [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Como criar uma Rede Virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Como criar um NSG com um Config de Segurança](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.11 | 11.3 | Cliente |

Utilize o Registo de Atividades Azure para monitorizar as configurações dos recursos e detetar alterações nos seus recursos Azure. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Como criar alertas no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

## <a name="next-steps"></a>Passos seguintes

- Ver o próximo Controlo de Segurança: [Registo e Monitorização](security-control-logging-monitoring.md)
