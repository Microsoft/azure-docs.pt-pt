---
title: O que é o Azure Firewall?
description: Saiba mais sobre as funcionalidades do Azure Firewall.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 11/28/2018
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 003b43760b4c0ae5785315e6acf0ed9982d9b1fe
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960858"
---
# <a name="what-is-azure-firewall"></a>O que é o Azure Firewall?

O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. É uma firewall com total monitoração de estado como um serviço com elevada disponibilidade incorporada e escalabilidade da cloud sem restrições. 

![Descrição geral das firewalls](media/overview/firewall-overview.png)

Pode criar, impor e registar centralmente políticas de conectividade de rede e aplicações entre subscrições e redes virtuais. O Azure Firewall utiliza um endereço IP público estático para os recursos de rede virtual que permite às firewalls externas identificar o tráfego com origem na sua rede virtual.  O serviço está totalmente integrado no Azure Monitor para fins de registo e análise.

## <a name="features"></a>Funcionalidades

O Azure Firewall oferece as seguintes funcionalidades:

### <a name="built-in-high-availability"></a>Elevada disponibilidade incorporada
A elevada disponibilidade está incorporada, pelo que não são necessários balanceadores de carga adicionais e não existe nada que tenha de configurar.

### <a name="unrestricted-cloud-scalability"></a>Escalabilidade da cloud sem restrições 
O Azure Firewall pode aumentar verticalmente conforme as suas necessidades para acomodar fluxos de tráfego de rede em alteração, pelo que não precisa de orçamentar o tráfego de pico.

### <a name="application-fqdn-filtering-rules"></a>Regras de filtragem de FQDN de aplicação

Pode limitar o tráfego HTTP/S de saída a uma lista especificada de nomes de domínio completamente qualificados (FQDN), incluindo carateres universais. Esta funcionalidade não requer terminação de SSL.

### <a name="network-traffic-filtering-rules"></a>Regras de filtragem de tráfego de rede

Pode criar centralmente regras de filtragem de rede de *permissão* ou *negação* por endereço IP de origem e destino, porta e protocolo. O Azure Firewall tem total monitoração de estado, para conseguir distinguir pacotes legítimos para diferentes tipos de ligações. As regras são impostas e registadas em várias subscrições e redes virtuais.

### <a name="fqdn-tags"></a>Etiquetas FQDN

As etiquetas FQDN facilitam a permissão do tráfego de rede bem conhecido do serviço do Azure através da firewall. Por exemplo, digamos que deseja permitir tráfego de rede do Windows Update através da firewall. Crie uma regra de aplicação e inclua a etiqueta do Windows Update. Agora o tráfego de rede do Windows Update pode fluir através da firewall.

### <a name="outbound-snat-support"></a>Suporte SNAT de saída

Todos os endereços IP de tráfego de rede virtual de saída são convertidos no IP público do Azure Firewall (Tradução de Endereços de Rede de Origem). Pode identificar e permitir tráfego com origem na sua rede virtual para destinos de Internet remotos.

### <a name="inbound-dnat-support"></a>Suporte DNAT de entrada

O tráfego de rede de entrada para o seu endereço IP público do firewall é traduzido (Tradução de Endereços de Rede de Destino) e filtrado para os endereços IP privados nas suas redes virtuais. 

### <a name="azure-monitor-logging"></a>Registo do Azure Monitor

Todos os eventos são integrados com o Azure Monitor, permitindo que arquivar registos para uma conta de armazenamento, transmitir eventos ao seu Hub de eventos, ou enviá-los para registos do Azure Monitor.

## <a name="known-issues"></a>Problemas conhecidos

O Azure Firewall tem os seguintes problemas conhecidos:


|Problema  |Descrição  |Mitigação  |
|---------|---------|---------|
|Conflito com a funcionalidade Just-in-Time (JIT) do Centro de Segurança do Azure (ASC)|Se uma máquina virtual for acedida por JIT e estiver numa sub-rede com uma rota definida pelo utilizador que aponta para o Azure Firewall como um gateway predefinido, o JIT do ASC não funciona. Isto resulta do encaminhamento assimétrico: um pacote entra através do IP público da máquina virtual (o JIT abriu o acesso), mas o caminho de retorno é através da firewall, o que remove o pacote por não existir nenhuma sessão estabelecida na firewall.|Para contornar este problema, coloque as máquinas virtuais JIT numa sub-rede separada que não tenha uma rota definida pelo utilizador para a firewall.|
|O hub-and-spoke com peering global não é suportado|Com o modelo de hub-and-spoke, em que o hub e a firewall estão implementados numa região do Azure, com os spokes noutra região do Azure. As ligações para o hub através de Global VNet Peering não são suportadas.|Esta ação é propositada. Para obter mais informações, veja [Subscrição do Azure e limites, quotas e restrições do serviço](../azure-subscription-service-limits.md#azure-firewall-limits)|
As regras de filtragem de rede para protocolos não TCP/UDP (por exemplo, ICMP) não funcionam para o tráfego vinculado à Internet|As regras de filtragem de rede para protocolos não TCP/UDP não funcionam com SNAT para o seu endereço IP público. Os protocolos não TCP/UDP são suportados entre VNets e sub-redes spoke.|O Azure Firewall utiliza o Balanceador de Carga Standard [que não suporta atualmente SNAT para protocolos IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Estamos a explorar opções para suportar este cenário numa versão futura.|
|Suporte do PowerShell e CLI em falta para ICMP|O Azure PowerShell e a CLI não suportam o ICMP como um protocolo válido nas regras de rede.|É possível utilizar o ICMP como um protocolo através do portal e da API REST. Estamos a trabalhar para adicionar o ICMP ao PowerShell e à CLI em breve.|
|As etiquetas FQDN requerem um protocolo: porta a definir|As regras de aplicação com etiquetas FQDN requerem a porta:definição de protocolo.|Pode utilizar **https** como a porta: valor de protocolo. Estamos a trabalhar para tornar este campo opcional quando são utilizadas etiquetas FQDN.|
|Não é suportada a mudança de uma firewall para um grupo de recursos diferente ou uma subscrição|Não é suportada a mudança de uma firewall para um grupo de recursos ou uma subscrição diferente.|Suporte a essa funcionalidade é no nosso mapa da estrada. Para mover uma firewall para um grupo de recursos ou uma subscrição diferente, tem de eliminar a instância atual e recriá-la no novo grupo de recursos ou subscrição.|
|Intervalo de portas nas regras de rede e da aplicação|As portas estão limitadas a 64.000 como portas de elevada estão reservadas para gestão e o estado de funcionamento sondas. |Estamos a trabalhar para reduzir esta limitação.|
|

## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Implementar e configurar a Firewall do Azure no portal do Azure](tutorial-firewall-deploy-portal.md)
- [Implementar o Azure Firewall através de um modelo](deploy-template.md)
- [Criar um ambiente de teste do Azure Firewall](scripts/sample-create-firewall-test.md)

