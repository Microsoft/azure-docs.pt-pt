---
title: Controlo de Segurança Azure - Segurança da Rede
description: Segurança da Rede de Controlo de Segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 7916bbb28602d64e0916fce7badf16a65c242227
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251877"
---
# <a name="security-control-network-security"></a>Controlo de Segurança: Segurança da Rede

As recomendações de segurança da rede concentram-se em especificar quais os protocolos de rede, portas TCP/UDP e serviços ligados à rede são permitidos ou negados aos serviços Azure.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos utilizando grupos de segurança de rede ou firewall azure na sua Rede Virtual

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1-14.3 | Cliente |

Certifique-se de que todas as implementações de subredes da Rede Virtual têm um Grupo de Segurança da Rede aplicado com controlos de acesso à rede específicos das portas e fontes fidedignas da sua aplicação. Utilize serviços Azure com Link Privado ativado, implemente o serviço dentro do vnet ou ligue-se em privado usando pontos finais privados. Para obter requisitos específicos de serviço, consulte a recomendação de segurança para esse serviço específico.

Em alternativa, se tiver um caso de utilização específico, os requisitos podem ser cumpridos implementando o Azure Firewall.

Informações Gerais sobre Ligação Privada:

https://docs.microsoft.com/azure/private-link/private-link-overview

Como criar uma Rede Virtual:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG com uma configuração de segurança:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Como implantar e configurar firewall Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitore e registe a configuração e tráfego de Vnets, Subnets e NICs

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.2 | 9.3, 12.2 | Cliente |

Utilize o Azure Security Center e siga as recomendações de proteção da rede para ajudar a proteger os seus recursos de rede em Azure. Ative os registos de fluxo do NSG e envie registos numa Conta de Armazenamento para auditoria de tráfego.

Como ativar os registos de fluxo nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Compreender a Segurança da Rede fornecida pelo Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.3 | 9.5 | Cliente |

Implemente o Firewall de Aplicação Web Azure (WAF) em frente a aplicações web críticas para uma inspeção adicional do tráfego de entrada. Ative a definição de diagnóstico para WAF e ingere os registos numa Conta de Armazenamento, Hub de Eventos ou log analytics workspace.

Como implantar o Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.4 | 12.3 | Cliente |

Ative a proteção Padrão DDoS nas suas Redes Virtuais Azure para se proteger contra ataques DDoS. Utilize o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP maliciosos conhecidos.

Implemente o Azure Firewall em cada uma das fronteiras da rede da organização com a Threat Intelligence habilitada e configurada para &quot;Alert e negar&quot; para tráfego de rede malicioso.

Utilize o Azure Security Center Just In Time Network acesso para configurar NSGs para limitar a exposição de pontos finais a endereços IP aprovados por um período limitado.

Utilize o Azure Security Center Adaptive Network Hardening para recomendar configurações de NSG que limitem portas e IPs de origem com base na inteligência real de tráfego e ameaças.

Como configurar a proteção DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Como implantar o Firewall Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Compreender o Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Compreender o Centro de Segurança Azure Adaptive Network Endureing:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Compreender o Azure Security Center just in Time Network Access Control:

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.5 | 12.5, 15.8 | Cliente |

Registre os registos de fluxo nsg numa conta de armazenamento para gerar registos de fluxo. Se necessário para investigar atividades anómalas, ative a captura de pacotes do Observador da Rede.

Como ativar os registos de fluxo nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar o Observador da Rede:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.6 | 12.6, 12.7 | Cliente |

Implemente o Azure Firewall em cada uma das fronteiras da rede da organização com a Threat Intelligence habilitada e configurada para &quot;Alert e negar&quot; para tráfego de rede malicioso.

Como implementar o Azure Firewall: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Como configurar alertas com firewall Azure: https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.7 | 12.9, 12.10 | Cliente |

Implemente o Portal de Aplicações Azure para aplicações web com HTTPS/SSL habilitados para certificados fidedignos.

Como implementar o Gateway da Aplicação:

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Como configurar o Gateway de Aplicação para utilizar HTTPS:

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Compreenda o equilíbrio de carga da camada 7 com os gateways de aplicação web Azure:

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.8 | 1.5 | Cliente |

Utilize etiquetas de serviço de rede virtual para definir controlos de acesso à rede em Grupos de Segurança da Rede ou firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Especificando o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Compreender e utilizar etiquetas de serviço:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.9 | 11.1 | Cliente |

Defina e implemente configurações de segurança padrão para recursos de rede com a Política Azure.

Também pode utilizar plantas Azure para simplificar as implementações azure em larga escala, embalagendo artefactos ambientais chave, tais como modelos de Gestor de Recursos Azure, controlos RBAC e políticas, numa única definição de planta. Pode aplicar o projeto a novas subscrições e a finas condições de controlo e gestão através da versão.

Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Amostras da Política Azure para networking:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

Como criar um Projeto Azure:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.1 | 11.2 | Cliente |

Utilize etiquetas para NSGs e outros recursos relacionados com a segurança da rede e fluxo de tráfego. Para regras nsg individuais, utilize o campo &quot;Descrição&quot; para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Como criar e utilizar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como criar uma Rede Virtual:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG com um Config de Segurança:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.11 | 11.3 | Cliente |

Utilize a Política Azure para validar (e/ou remediar) a configuração para os recursos de rede.

Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Amostras da Política Azure para networking:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

## <a name="next-steps"></a>Passos seguintes

- Consulte o próximo controlo de segurança: [Registo e Monitorização](security-control-logging-monitoring.md)
