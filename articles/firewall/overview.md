---
title: O que é o Azure Firewall?
description: Saiba mais sobre as funcionalidades do Azure Firewall.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 6/20/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: ace0b56ce1ba4c140666c8f2dd6e2187f479446e
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272644"
---
# <a name="what-is-azure-firewall"></a>O que é o Azure Firewall?

O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. É um firewall totalmente com monitoração de estado como um serviço com elevada disponibilidade incorporada e a escalabilidade da cloud sem restrições.

![Descrição geral das firewalls](media/overview/firewall-threat.png)

Pode criar, impor e registar centralmente políticas de conectividade de rede e aplicações entre subscrições e redes virtuais. O Azure Firewall utiliza um endereço IP público estático para os recursos de rede virtual que permite às firewalls externas identificar o tráfego com origem na sua rede virtual.  O serviço está totalmente integrado no Azure Monitor para fins de registo e análise.

## <a name="features"></a>Funcionalidades

O Azure Firewall oferece as seguintes funcionalidades:

### <a name="built-in-high-availability"></a>Elevada disponibilidade incorporada

Elevada disponibilidade é incorporada, pelo que não foram encontrados balanceadores de carga adicionais são necessários e não há nada que tem de configurar.

### <a name="availability-zones-public-preview"></a>As zonas de disponibilidade (pré-visualização pública)

Firewall do Azure pode ser configurado durante a implementação para abranger várias zonas de disponibilidade para maior disponibilidade. Com zonas de disponibilidade, a sua disponibilidade aumenta para o tempo de atividade de 99,99%. Para obter mais informações, consulte o Firewall do Azure [contrato de nível de serviço (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). O tempo de atividade de 99,99% são oferecidos SLAS quando duas ou mais zonas de disponibilidade estão selecionadas.

Também pode associar Firewall do Azure para uma zona específica apenas por motivos de proximidade, usando o SLA de 99,95% standard do serviço.

Não existe nenhum custo adicional de uma firewall implementado numa zona de disponibilidade. No entanto, existem custos adicionais para transferências de dados de entrada e saída associados com as zonas de disponibilidade. Para obter mais informações, consulte [os detalhes dos preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

As zonas de disponibilidade de Firewall do Azure estão disponíveis nas regiões que suportam as zonas de disponibilidade. Para obter mais informações, consulte [quais são as zonas de disponibilidade no Azure?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> As zonas de disponibilidade só podem ser configuradas durante a implementação. Não é possível configurar uma firewall existente para incluir as zonas de disponibilidade.

Para obter mais informações sobre as zonas de disponibilidade, consulte [quais são as zonas de disponibilidade no Azure?](../availability-zones/az-overview.md)

### <a name="unrestricted-cloud-scalability"></a>Escalabilidade da cloud sem restrições

O Azure Firewall pode aumentar verticalmente conforme as suas necessidades para acomodar fluxos de tráfego de rede em alteração, pelo que não precisa de orçamentar o tráfego de pico.

### <a name="application-fqdn-filtering-rules"></a>Regras de filtragem de FQDN de aplicação

Pode limitar o tráfego HTTP/S de saída a uma lista especificada de nomes de domínio completamente qualificados (FQDN), incluindo carateres universais. Esta funcionalidade não precisa de terminação de SSL.

### <a name="network-traffic-filtering-rules"></a>Regras de filtragem de tráfego de rede

Pode criar centralmente regras de filtragem de rede de *permissão* ou *negação* por endereço IP de origem e destino, porta e protocolo. O Azure Firewall tem total monitoração de estado, para conseguir distinguir pacotes legítimos para diferentes tipos de ligações. As regras são impostas e registadas em várias subscrições e redes virtuais.

### <a name="fqdn-tags"></a>Etiquetas FQDN

As etiquetas FQDN facilitam a permissão do tráfego de rede bem conhecido do serviço do Azure através da firewall. Por exemplo, digamos que deseja permitir tráfego de rede do Windows Update através da firewall. Crie uma regra de aplicação e inclua a etiqueta do Windows Update. Agora o tráfego de rede do Windows Update pode fluir através da firewall.

### <a name="service-tags"></a>Etiquetas de serviço

As etiquetas de serviço representam um grupo de prefixos de endereços IP, que ajudam a minimizar a complexidade da criação de regras de segurança. Não pode criar suas próprias etiquetas de serviço nem especificar que endereços IP estão incluídos nas mesmas. A Microsoft gere os prefixos de endereços que as etiquetas abrangem e atualiza-as automaticamente à medida que os endereços são alterados.

### <a name="threat-intelligence"></a>Informações sobre ameaças

A filtragem com base em informações sobre ameaças pode ser ativada para que a sua firewall alerte e negue tráfego de/para endereços e domínios de IP maliciosos. Os endereços e domínios de IP são obtidos no feed de Informações sobre Ameaças da Microsoft.

### <a name="outbound-snat-support"></a>Suporte SNAT de saída

Todos os endereços IP de tráfego de rede virtual de saída são convertidos no IP público do Azure Firewall (Tradução de Endereços de Rede de Origem). Pode identificar e permitir tráfego com origem na sua rede virtual para destinos de Internet remotos.

### <a name="inbound-dnat-support"></a>Suporte DNAT de entrada

O tráfego de rede de entrada para o seu endereço IP público do firewall é traduzido (Tradução de Endereços de Rede de Destino) e filtrado para os endereços IP privados nas suas redes virtuais.

### <a name="multiple-public-ips-public-preview"></a>Público vários IPs (pré-visualização pública)

Pode associar vários endereços IP públicos (até 600) a sua firewall.

Isto permite que os seguintes cenários:

- **DNAT** -é possível converter várias instâncias de porta padrão para os seus servidores de back-end. Por exemplo, se tiver dois endereços IP públicos, é possível converter a porta TCP 3389 (RDP) para os dois endereços IP.
- **SNAT** -portas adicionais estão disponíveis para ligações SNAT de saída, reduzindo a possibilidade de exaustão de porta SNAT. Neste momento, o Firewall do Azure seleciona aleatoriamente o endereço IP público de origem para utilizar uma ligação. Se tiver qualquer filtragem jusante na sua rede, terá de permitir que todos os endereços IP públicos associados com a firewall.

> [!NOTE]
> Durante a pré-visualização pública, se adicionar ou remover um endereço IP público para um firewall em execução, conectividade de entrada existente usando regras DNAT pode não funcionar para 40-120 segundos.

### <a name="azure-monitor-logging"></a>Registo do Azure Monitor

Todos os eventos são integrados com o Azure Monitor, permitindo que arquivar registos para uma conta de armazenamento, transmitir eventos ao seu Hub de eventos, ou enviá-los para registos do Azure Monitor.

## <a name="known-issues"></a>Problemas conhecidos

O Azure Firewall tem os seguintes problemas conhecidos:

|Problema  |Descrição  |Mitigação  |
|---------|---------|---------|
|Conflito com a funcionalidade Just-in-Time (JIT) do Centro de Segurança do Azure (ASC)|Se uma máquina virtual for acedida por JIT e estiver numa sub-rede com uma rota definida pelo utilizador que aponta para o Azure Firewall como um gateway predefinido, o JIT do ASC não funciona. Isso é resultado do encaminhamento assimétrico – um pacote é fornecido através do IP público da máquina virtual (aberto o acesso JIT), mas o caminho de retorno é através da firewall, que remove o pacote, porque não existe nenhuma sessão estabelecida na firewall.|Para contornar este problema, coloque as máquinas virtuais JIT numa sub-rede separada que não tenha uma rota definida pelo utilizador para a firewall.|
As regras de filtragem de rede para protocolos não TCP/UDP (por exemplo, ICMP) não funcionam para o tráfego vinculado à Internet|As regras de filtragem de rede para protocolos não TCP/UDP não funcionam com SNAT para o seu endereço IP público. Os protocolos não TCP/UDP são suportados entre VNets e sub-redes spoke.|O Azure Firewall utiliza o Balanceador de Carga Standard [que não suporta atualmente SNAT para protocolos IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Estamos está explorando as opções para suportar este cenário numa versão futura.|
|Suporte do PowerShell e CLI em falta para ICMP|O Azure PowerShell e a CLI não suportam o ICMP como um protocolo válido nas regras de rede.|É possível utilizar o ICMP como um protocolo através do portal e a API REST. Estamos a trabalhar para adicionar ICMP no PowerShell e CLI em breve.|
|As etiquetas FQDN requerem um protocolo: porta a definir|As regras de aplicação com etiquetas FQDN requerem porta: definição de protocolo.|Pode utilizar **https** como a porta: valor de protocolo. Estamos a trabalhar para tornar este campo opcional e, quando as etiquetas FQDN são utilizadas.|
|Não é possível mover uma firewall para um grupo de recursos diferente ou uma subscrição|Não é suportada a mudança de uma firewall para um grupo de recursos diferente ou uma subscrição.|Suporte a essa funcionalidade é no nosso mapa da estrada. Para mover uma firewall para um grupo de recursos ou uma subscrição diferente, tem de eliminar a instância atual e recriá-la no novo grupo de recursos ou subscrição.|
|Intervalo de portas nas regras de rede e da aplicação|As portas estão limitadas a 64.000 como portas de elevada estão reservadas para gestão e o estado de funcionamento sondas. |Estamos a trabalhar para reduzir esta limitação.|
|Podem obter mascarados alertas de inteligência de ameaças|Alertas de inteligência quando configurado para o modo só de alerta de ameaças de regras de rede com o destino 80/443 para máscaras de filtragem de saída.|Crie a filtragem de saída para 80/443 usando regras de aplicações. Em alternativa, altere o modo de inteligência de ameaças **alertar e negar**.|
|Firewall do Azure utiliza o DNS do Azure apenas para resolução de nomes|Firewall do Azure resolve FQDNs apenas a utilizar o DNS do Azure. Um servidor DNS personalizado não é suportado. Não existe nenhum impacto na resolução DNS em outras sub-redes.|Estamos a trabalhar para reduzir esta limitação.|
|SNAT/DNAT de Firewall do Azure não funciona para destinos de IP privados|Suporte de Firewall SNAT/DNAT do Azure está limitado a entrada/saída de Internet. SNAT/DNAT atualmente não funciona para destinos de IP privados. Por exemplo, spoke para and-spoke.|Esta é uma limitação atual.|
|Não é possível remover o primeiro endereço IP público|Não é possível remover o primeiro endereço IP público atribuído para o firewall, a menos que a firewall está desalocada ou eliminada.|Esta ação é propositada.|
|Se adicionar ou remover um endereço IP público, as regras DNAT podem não funcionar temporariamente.| Se adicionar ou remover um endereço IP público a uma firewall em execução, conectividade de entrada existente usando regras DNAT pode não funcionar para 40-120 segundos.|Esta é uma limitação da pré-visualização pública para esta funcionalidade.|
|As zonas de disponibilidade só podem ser configuradas durante a implementação.|As zonas de disponibilidade só podem ser configuradas durante a implementação. Não é possível configurar zonas de disponibilidade depois de implementar uma firewall.|Esta ação é propositada.|

## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Implementar e configurar a Firewall do Azure no portal do Azure](tutorial-firewall-deploy-portal.md)
- [Implementar o Azure Firewall através de um modelo](deploy-template.md)
- [Criar um ambiente de teste do Azure Firewall](scripts/sample-create-firewall-test.md)
