---
title: Controle de segurança do Azure-segurança de rede
description: Segurança de rede de controle de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: d052226470042d374544de0b7e1ced4ca0f48a14
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564156"
---
# <a name="security-control-network-security"></a>Controle de segurança: segurança de rede

As recomendações de segurança de rede concentram-se na especificação de quais protocolos de rede, portas TCP/UDP e serviços conectados à rede têm acesso permitido ou negado aos serviços do Azure.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua rede virtual

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.1 | 9,2, 9,4, 14,1-14.3 | Cliente |

Verifique se todas as implantações de sub-rede da rede virtual têm um grupo de segurança de rede aplicado com controles de acesso à rede específicos às portas e fontes confiáveis do seu aplicativo. Use os serviços do Azure com o link privado habilitado, implante o serviço dentro de sua vnet ou conecte-se de forma privada usando pontos de extremidade privados. Para requisitos específicos de serviço, consulte a recomendação de segurança para esse serviço específico.

Como alternativa, se você tiver um caso de uso específico, os requisitos poderão ser atendidos com a implementação do firewall do Azure.

Informações gerais sobre o link privado: https://docs.microsoft.com/azure/private-link/private-link-overview

Como criar uma rede virtual:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG com uma configuração de segurança:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Como implantar e configurar o Firewall do Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de Vnets, sub-redes e NICs

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.2 | 9,3, 12,2 | Cliente |

Use a central de segurança do Azure e siga as recomendações de proteção de rede para ajudar a proteger seus recursos de rede no Azure. Habilite logs de fluxo NSG e envie logs para uma conta de armazenamento para auditoria de tráfego.

Como habilitar os logs de fluxo do NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Noções básicas sobre segurança de rede fornecida pela central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1,3: proteger aplicativos Web críticos

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.3 | 9.5 | Cliente |

Implante o WAF (firewall do aplicativo Web) do Azure na frente de aplicativos Web críticos para inspeção adicional do tráfego de entrada. Habilite a configuração de diagnóstico para WAF e ingerir logs em uma conta de armazenamento, Hub de eventos ou espaço de trabalho de Log Analytics.

Como implantar o Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.4 | 12,3 | Cliente |

Habilite a proteção padrão de DDoS em suas redes virtuais do Azure para proteger contra ataques de DDoS. Use a inteligência de ameaças integrada da central de segurança do Azure para negar comunicações com endereços IP mal-intencionados conhecidos.

Implante o Firewall do Azure em cada um dos limites de rede da organização com inteligência contra ameaças habilitada e configurada para &quot;alerta e negação de&quot; para tráfego de rede mal-intencionado.

Use o acesso à rede just in time da central de segurança do Azure para configurar o NSGs para limitar a exposição de pontos de extremidade a endereços IP aprovados por um período limitado.

Use a proteção de rede adaptável da central de segurança do Azure para recomendar configurações de NSG que limitam portas e IPs de origem com base no tráfego real e na inteligência contra ameaças.

Como configurar a proteção contra DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Como implantar o Firewall do Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Entenda a inteligência de ameaças integrada da central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Entender a proteção de rede adaptável da central de segurança do Azure

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Entender o controle de acesso à rede just in time da central de segurança do Azure

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1,5: gravar pacotes de rede e logs de fluxo

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.5 | 12,5, 15,8 | Cliente |

Registre os logs de fluxo do NSG em uma conta de armazenamento para gerar registros de fluxo. Se necessário para investigar a atividade anômala, habilite a captura de pacotes do observador de rede.

Como habilitar os logs de fluxo do NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como habilitar o observador de rede: https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.6 | 12,6, 12,7 | Cliente |

Implante o Firewall do Azure em cada um dos limites de rede da organização com inteligência contra ameaças habilitada e configurada para &quot;alerta e negação de&quot; para tráfego de rede mal-intencionado.

Como implantar o Firewall do Azure: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Como configurar alertas com o Firewall do Azure: https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1,7: gerenciar o tráfego para aplicativos Web

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.7 | 12,9, 12,10 | Cliente |

Implante Aplicativo Azure gateway para aplicativos Web com HTTPS/SSL habilitado para certificados confiáveis.

Como implantar o gateway de aplicativo: https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Como configurar o gateway de aplicativo para usar HTTPS: https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Noções básicas sobre balanceamento de carga de camada 7 com gateways de aplicativo Web do Azure: https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimizar a complexidade e a sobrecarga administrativa das regras de segurança de rede

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.8 | 1.5 | Cliente |

Use as marcas de serviço de rede virtual &nbsp;para definir os controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço abordados pela marca de serviço e atualiza automaticamente a marca de serviço à medida que os endereços são alterados.

Compreendendo e usando marcas de serviço: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: manter configurações de segurança padrão para dispositivos de rede

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.9 | 11,1 | Cliente |

Defina e implemente configurações de segurança padrão para recursos de rede com Azure Policy.

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos de Azure Resource Manager, controles RBAC e políticas, em uma única definição de Blueprint. Você pode aplicar o plano gráfico a novas assinaturas e ambientes, bem como ajustar o controle e o gerenciamento por meio da versão.

Como configurar e gerenciar Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy exemplos de rede:

https://docs.microsoft.com/azure/governance/policy/samples/#network

Como criar um Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1,10: regras de configuração de tráfego do documento

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.1 | 11,2 | Cliente |

Use marcas para NSGs e outros recursos relacionados à segurança de rede e ao fluxo de tráfego. Para regras NSG individuais, use o campo Descrição do &quot;&quot; para especificar a necessidade de negócios e/ou duração (etc.) para qualquer regra que permita o tráfego de/para uma rede.

Como criar e usar marcas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como criar uma rede virtual:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG com uma configuração de segurança:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1,11 | 11,3 | Cliente |

Use Azure Policy para validar a configuração (e/ou corrigir) dos recursos de rede.

Como configurar e gerenciar Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy exemplos de rede:

https://docs.microsoft.com/azure/governance/policy/samples/#network

## <a name="next-steps"></a>Passos seguintes

- Consulte o próximo controle de segurança: [registro em log e monitoramento](security-control-logging-monitoring.md)