---
title: Funcionalidades do Azure Firewall
description: Saiba mais sobre as funcionalidades do Azure Firewall
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 10/08/2020
ms.author: victorh
ms.openlocfilehash: 7429be4430b2b520fb2a66b6b2c0dd138af8e501
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850596"
---
# <a name="azure-firewall-features"></a>Funcionalidades do Azure Firewall

[O Azure Firewall](overview.md) é um serviço de segurança de rede gerido e baseado na nuvem que protege os seus recursos da Rede Virtual Azure.

![Descrição geral das firewalls](media/overview/firewall-threat.png)

A azure Firewall inclui as seguintes funcionalidades:

- [Alta disponibilidade incorporada](#built-in-high-availability)
- [Zonas de Disponibilidade](#availability-zones)
- [Escalabilidade de nuvem sem restrições](#unrestricted-cloud-scalability)
- [Regras de filtragem de FQDN de aplicação](#application-fqdn-filtering-rules)
- [Regras de filtragem de tráfego de rede](#network-traffic-filtering-rules)
- [Etiquetas FQDN](#fqdn-tags)
- [Etiquetas de serviço](#service-tags)
- [Informações sobre ameaças](#threat-intelligence)
- [Suporte SNAT de saída](#outbound-snat-support)
- [Suporte DNAT de entrada](#inbound-dnat-support)
- [Vários endereços IP públicos](#multiple-public-ip-addresses)
- [Registo do Monitor Azure](#azure-monitor-logging)
- [Túnel forçado](#forced-tunneling)
- [Certificações](#certifications)

## <a name="built-in-high-availability"></a>Elevada disponibilidade incorporada

A alta disponibilidade é incorporada, por isso não são necessários saldos de carga adicionais e não há nada que precise configurar.

## <a name="availability-zones"></a>Zonas de Disponibilidade

O Azure Firewall pode ser configurado durante a implementação para abranger várias Zonas de Disponibilidade para uma maior disponibilidade. Com As Zonas de Disponibilidade, a sua disponibilidade aumenta para 99,99% de tempo de desconto. Para obter mais informações, consulte o Acordo de [Nível de Serviço de Firewall Azure Firewall (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). O SLA de 99,99% é oferecido quando são selecionadas duas ou mais Zonas de Disponibilidade.

Também pode associar o Azure Firewall a uma zona específica apenas por razões de proximidade, utilizando o serviço padrão 99.95% SLA.

Não há custo adicional para uma firewall implantada numa Zona de Disponibilidade. No entanto, existem custos adicionais para transferências de dados de entrada e saída associadas às Zonas de Disponibilidade. Para mais informações, consulte [os detalhes dos preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

As Zonas de Disponibilidade de Firewall Azure estão disponíveis em regiões que suportam Zonas de Disponibilidade. Para mais informações, consulte [Regiões que suportam Zonas de Disponibilidade em Azure](../availability-zones/az-region.md)

> [!NOTE]
> As Zonas de Disponibilidade só podem ser configuradas durante a implantação. Não é possível configurar uma firewall existente para incluir Zonas de Disponibilidade.

Para mais informações sobre Zonas de Disponibilidade, consulte [Regiões e Zonas de Disponibilidade em Azure](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Escalabilidade da cloud sem restrições

O Azure Firewall pode aumentar verticalmente conforme as suas necessidades para acomodar fluxos de tráfego de rede em alteração, pelo que não precisa de orçamentar o tráfego de pico.

## <a name="application-fqdn-filtering-rules"></a>Regras de filtragem de FQDN de aplicação

Pode limitar o tráfego HTTP/S de saída ou o tráfego Azure SQL a uma lista especificada de nomes de domínio totalmente qualificados (FQDN) incluindo cartões selvagens. Esta funcionalidade não requer a rescisão do TLS.

## <a name="network-traffic-filtering-rules"></a>Regras de filtragem de tráfego de rede

Pode criar centralmente *regras de* filtragem de *rede* por endereço IP de origem e destino, porta e protocolo. O Azure Firewall tem total monitoração de estado, para conseguir distinguir pacotes legítimos para diferentes tipos de ligações. As regras são impostas e registadas em várias subscrições e redes virtuais.

## <a name="fqdn-tags"></a>Etiquetas FQDN

[As tags FQDN](fqdn-tags.md) facilitam-lhe a entrada de tráfego de rede de serviços Azure bem conhecido através da sua firewall. Por exemplo, digamos que deseja permitir tráfego de rede do Windows Update através da firewall. Crie uma regra de aplicação e inclua a etiqueta do Windows Update. Agora o tráfego de rede do Windows Update pode fluir através da firewall.

## <a name="service-tags"></a>Etiquetas de serviço

Uma etiqueta de [serviço](service-tags.md) representa um grupo de prefixos de endereço IP para ajudar a minimizar a complexidade para a criação de regras de segurança. Não é possível criar a sua própria etiqueta de serviço, nem especificar quais endereços IP estão incluídos numa etiqueta. A Microsoft gere os prefixos de endereços que as etiquetas abrangem e atualiza-as automaticamente à medida que os endereços são alterados.

## <a name="threat-intelligence"></a>Informações sobre ameaças

[A](threat-intel.md)filtragem baseada em inteligência de ameaça pode ser ativada para a sua firewall alertar e negar o tráfego de/para endereços e domínios IP maliciosos conhecidos. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft.

## <a name="outbound-snat-support"></a>Suporte SNAT de saída

Todos os endereços IP de tráfego de rede virtual de saída são convertidos no IP público do Azure Firewall (Tradução de Endereços de Rede de Origem). Pode identificar e permitir tráfego com origem na sua rede virtual para destinos de Internet remotos. O Azure Firewall não sNAT quando o destino IP é uma gama ip privada por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Se a sua organização utilizar um intervalo de endereços IP público para redes privadas, o Azure Firewall irá SNAT o tráfego para um dos endereços IP privados de firewall em AzureFirewallSubnet. Pode configurar o Azure Firewall para **não** sNAT o seu intervalo de endereços IP público. Para obter mais informações, consulte [as gamas de endereços IP privados Azure Firewall SNAT](snat-private-range.md).

## <a name="inbound-dnat-support"></a>Suporte DNAT de entrada

O tráfego de rede de Internet de entrada para o seu endereço IP público de firewall é traduzido (Tradução de Endereço de Rede de Destino) e filtrado para os endereços IP privados nas suas redes virtuais.

## <a name="multiple-public-ip-addresses"></a>Vários endereços IP públicos

Pode associar [vários endereços IP públicos](deploy-multi-public-ip-powershell.md) (até 250) à sua firewall.

Isto permite os seguintes cenários:

- **DNAT** - Pode traduzir várias instâncias de porta padrão para os seus servidores backend. Por exemplo, se tiver dois endereços IP públicos, poderá converter a porta TCP 3389 (RDP) para ambos os endereços IP.
- **SNAT** - Estão disponíveis portas adicionais para ligações SNAT de saída, reduzindo o potencial de exaustão portuária SNAT. Neste momento, o Azure Firewall seleciona aleatoriamente o endereço IP público de origem para utilizar para uma ligação. Se tiver alguma filtragem a jusante na rede, terá de permitir todos os endereços IP públicos associados à firewall. Considere utilizar um [prefixo de endereço IP público](../virtual-network/public-ip-address-prefix.md) para simplificar esta configuração.

## <a name="azure-monitor-logging"></a>Registo do Azure Monitor

Todos os eventos são integrados com o Azure Monitor, permitindo-lhe arquivar registos numa conta de armazenamento, transmitir eventos para o seu Centro de Eventos ou enviá-los para registos do Azure Monitor. Para amostras de registo do Monitor Azure, consulte [os registos do Monitor Azure para Azure Firewall](log-analytics-samples.md).

Para obter mais informações, consulte [Tutorial: Monitor Azure Firewall registos e métricas](tutorial-diagnostics.md). 

O Azure Firewall Workbook fornece uma tela flexível para a análise de dados do Azure Firewall. Pode usá-lo para criar relatórios visuais ricos dentro do portal Azure. Para obter mais informações, consulte [os registos do Monitor utilizando o Livro de Trabalho da Firewall Azure](firewall-workbook.md).

## <a name="forced-tunneling"></a>Túnel forçado

Você pode configurar a Azure Firewall para encaminhar todo o tráfego ligado à Internet para um próximo salto designado em vez de ir diretamente para a Internet. Por exemplo, pode ter uma firewall de borda no local ou outro aparelho virtual de rede (NVA) para processar o tráfego de rede antes de ser passado para a Internet. Para mais informações, consulte [o Azure Firewall forjando túneis.](forced-tunneling.md)

## <a name="certifications"></a>Certificações

Azure Firewall é a Indústria de Cartões de Pagamento (PCI), Os Controlos da Organização de Serviços (SOC), a Organização Internacional de Normalização (ISO) e a ICSA Labs em conformidade. Para obter mais informações, consulte [as certificações de conformidade da Azure Firewall.](compliance-certifications.md)

## <a name="next-steps"></a>Passos seguintes

- [Lógica de processamento de regras do Azure Firewall](rule-processing.md)