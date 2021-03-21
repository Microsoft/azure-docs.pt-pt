---
title: Regras de saída Azure Load Balancer
description: Este artigo explica como configurar regras de saída para controlar a saída do tráfego de internet com o Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 2fc703e0532c86bfc0874c8dccbb17c6142aeed0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590216"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Regras de saída Azure Load Balancer

As regras de saída permitem-lhe definir explicitamente o SNAT (tradução de endereços de rede de origem) para um balanceador de carga padrão público. Esta configuração permite-lhe utilizar o IP(s) público do seu balanceador de carga para fornecer conectividade de saída à Internet para as suas instâncias de backend.

Esta configuração permite:

* Disfarçado de IP
* Simplificando as suas listas de admissão.
* Reduz o número de recursos IP públicos para implantação.

Com as regras de saída, você tem total controlo declarativo sobre a conectividade de saída da Internet. As regras de saída permitem-lhe escalar e afinar esta capacidade às suas necessidades específicas. 

As regras de saída só serão seguidas se o VM backend não tiver um endereço IP público ao nível de instância (ILPIP).

![Regras de saída do Balanceador de Carga](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Com as regras de saída, pode definir explicitamente o comportamento **SNAT** de saída.

As regras de saída permitem-lhe controlar:

* **Quais máquinas virtuais são traduzidas para quais endereços IP públicos.**
     * Duas regras foram backend pool 1 usa o endereço IP azul 1 e 2, backend pool 2 usa o prefixo IP amarelo.
* **Como são atribuídas as portas SNAT de saída.**
     * Se a piscina backend 2 for a única piscina que faz ligações de saída, dê a todas as portas SNAT para reencar a piscina 2 e nenhuma para reencar a piscina 1.
* **Quais os protocolos para fornecer tradução de saída.**
     * Se a piscina de backend 2 necessitar de portas UDP para saída, e a piscina de backend 1 necessitar de TCP, dê portas TCP a 1 e portas UDP a 2.
* **Que duração utilizar para o intervalo de tempo de ligação de saída (4-120 minutos).**
     * Se houver ligações longas com keepalives, reserve portas inativas para ligações de longo prazo até 120 minutos. Assuma que as ligações velhas são abandonadas e liberte portas em 4 minutos para novas ligações 
* **Se enviar um Reset TCP no tempo limite de marcha lenta.**
     * Ao cronometrar as ligações ociosas, enviamos um TCP RST para o cliente e servidor para que saibam que o fluxo está abandonado?

## <a name="outbound-rule-definition"></a>Definição de regra de saída

As regras de saída seguem a mesma sintaxe familiar que o equilíbrio de carga e as regras NAT de entrada: reserva **de**  +  **parâmetros**  +  **frontend**. 

Uma regra de saída configura a saída DO NAT para _todas as máquinas virtuais identificadas pela piscina de backend_ para serem traduzidas para o _frontend_.  

Os _parâmetros_ proporcionam um controlo adicional de grãos finos sobre o algoritmo NAT de saída.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> NAT de escala com vários endereços IP

Cada endereço IP adicional fornecido por um frontend fornece mais 64.000 portas efémeras para o balanceador de carga utilizar como portas SNAT. 

Utilize vários endereços IP para planear cenários em larga escala. Utilize regras de saída para mitigar a [exaustão do SNAT](troubleshoot-outbound-connection.md#snatexhaust). 

Também pode utilizar um [prefixo IP público](./load-balancer-outbound-connections.md#outboundrules) diretamente com uma regra de saída. 

Um prefixo IP público aumenta o dimensionamento da sua implantação. O prefixo pode ser adicionado à lista de fluxos de permitir os fluxos originários dos seus recursos Azure. Pode configurar uma configuração IP frontal dentro do equilibrador de carga para fazer referência a um prefixo de endereço IP público.  

O equilibrador de carga tem controlo sobre o prefixo IP público. A regra de saída utilizará automaticamente todos os endereços IP públicos contidos no prefixo IP público para ligações de saída. 

Cada um dos endereços IP no prefixo IP público fornece mais 64.000 portas efémeras por endereço IP para o balanceador de carga a utilizar como portas SNAT.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Tempo de saída inativo e reset TCP

As regras de saída fornecem um parâmetro de configuração para controlar o tempo limite de saída e corresponder às necessidades da sua aplicação. Intervalos de tempo de saída inativos a 4 minutos. Para obter mais informações, consulte [configurar intervalos de tempo inativos](load-balancer-tcp-idle-timeout.md). 

O comportamento padrão do equilibrador de carga é baixar o fluxo silenciosamente quando o tempo de saída tiver sido atingido. O `enableTCPReset` parâmetro permite um comportamento e controlo de aplicação previsíveis. O parâmetro dita se deve enviar o Reset TCP bidirecional (TCP RST) no tempo limite do tempo de saída. 

[Reveja o TCP Reset no tempo limite de](./load-balancer-tcp-reset.md) marcha lenta para obter detalhes, incluindo a disponibilidade da região.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Assegurar e controlar explicitamente a conectividade de saída

As regras de equilíbrio de carga fornecem uma programação automática do NAT de saída. Alguns cenários beneficiam ou obrigam a desativar a programação automática do NAT de saída pela regra de equilíbrio de carga. Desativar através da regra permite-lhe controlar ou refinar o comportamento.  

Pode utilizar este parâmetro de duas formas:

1. Prevenção do endereço IP de entrada para o SNAT de saída. Desative o SNAT de saída na regra de equilíbrio de carga.
  
2. Sintonize os parâmetros **SNAT** de saída de um endereço IP utilizado para entrada e saída simultaneamente. O NAT de saída automática deve ser desativado para permitir que uma regra de saída assuma o controlo. Para alterar a atribuição da porta SNAT de um endereço também utilizado para a entrada, o `disableOutboundSnat` parâmetro deve ser definido como verdadeiro. 

A operação para configurar uma regra de saída falhará se tentar redefinir um endereço IP que é utilizado para a entrada.  Desative primeiro o NAT de saída da regra de equilíbrio de carga.

>[!IMPORTANT]
> A sua máquina virtual não terá conectividade de saída se definir este parâmetro como verdadeiro e não tiver uma regra de saída para definir conectividade de saída.  Algumas operações do seu VM ou da sua aplicação podem depender de ter conectividade de saída disponível. Certifique-se de compreender as dependências do seu cenário e considerou o impacto de fazer esta mudança.

Às vezes é indesejável que um VM crie um fluxo de saída. Pode haver uma exigência de gestão dos destinos que recebem fluxos de saída, ou quais os destinos que iniciam os fluxos de entrada. Utilize [grupos de segurança](../virtual-network/network-security-groups-overview.md) de rede para gerir os destinos que o VM atinge. Utilize NSGs para gerir quais destinos públicos iniciam fluxos de entrada.

Quando aplicar um NSG a um VM equilibrado em carga, preste atenção às [etiquetas](../virtual-network/network-security-groups-overview.md#service-tags) de serviço e às [regras de segurança predefinidas](../virtual-network/network-security-groups-overview.md#default-security-rules). 

Certifique-se de que o VM pode receber pedidos de sonda de saúde do Azure Load Balancer.

Se um NSG bloquear pedidos de sonda de saúde a partir da etiqueta predefinição AZURE_LOADBALANCER, a sua sonda de saúde VM falha e o VM está marcado indisponível. O equilibrador de carga deixa de enviar novos fluxos para o VM.

## <a name="scenarios-with-outbound-rules"></a>Cenários com regras de saída
        

### <a name="outbound-rules-scenarios"></a>Cenários de regras de saída


* Configure as ligações de saída a um conjunto específico de IPs ou prefixo público.
* Modificar a atribuição do porto [SNAT.](load-balancer-outbound-connections.md)
* Ativar apenas a saída.
* NAT de saída apenas para VMs (sem entrada).
* NAT de saída para o equilibrador de carga padrão interno.
* Ativar os protocolos de UDP & TCP para o NAT de saída com um balanceador de carga padrão público.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Cenário 1: Configurar ligações de saída a um conjunto específico de IPs ou prefixo público


#### <a name="details"></a>Detalhes


Utilize este cenário para personalizar as ligações de saída a partir de um conjunto de endereços IP públicos. Adicione iPs públicos ou prefixos a uma lista de permitir ou negar com base na origem.


Este IP ou prefixo público pode ser o mesmo que usado por uma regra de equilíbrio de carga. 


Para utilizar um IP ou prefixo público diferente do utilizado por uma regra de equilíbrio de carga: 


1. Crie prefixo IP público ou endereço IP público.
2. Criar um balanceador de carga padrão público 
3. Crie um frontend referente ao prefixo IP público ou ao endereço IP público que pretende utilizar. 
4. Reutilizar uma piscina de backend ou criar uma piscina de backend e colocar os VMs numa piscina de backend do equilibrador de carga pública
5. Configure uma regra de saída no equilibrador de carga pública para permitir que os VMs de saída utilizem o frontend. Não é aconselhável utilizar uma regra de equilíbrio de carga para saída, desativar o SNAT de saída na regra de equilíbrio de carga.


### <a name="scenario-2-modify-snatport-allocation"></a><a name="scenario2out"></a>Cenário 2: Modificar a atribuição da porta [SNAT](load-balancer-outbound-connections.md)


#### <a name="details"></a>Detalhes


Pode utilizar as regras de saída para sintonizar a atribuição automática da [porta SNAT com base no tamanho da piscina de backend](load-balancer-outbound-connections.md#preallocatedports). 


Se sentir esgotamento do SNAT, aumente o número de portas [SNAT](load-balancer-outbound-connections.md)dadas a partir do padrão de 1024. 


Cada endereço IP público contribui com até 64.000 portos efémeros. O número de VMs na piscina de backend determina o número de portas distribuídas por cada VM. Um VM na piscina de backend tem acesso ao máximo de 64.000 portas. Para dois VMs, um máximo de 32.000 portas [SNAT](load-balancer-outbound-connections.md)podem ser dadas com uma regra de saída (2x 32.000 = 64.000). 


Pode utilizar regras de saída para sintonizar as portas SNAT dadas por defeito. Você dá mais ou menos do que a alocação padrão da porta [SNAT](load-balancer-outbound-connections.md)fornece. Cada endereço IP público a partir de uma regra de saída contribui com até 64.000 portas efémeras para utilização como portas [SNAT.](load-balancer-outbound-connections.md) 


O equilibrador de carga dá portas [SNAT](load-balancer-outbound-connections.md)em múltiplos de 8. Se fornecer um valor não divisível até 8, a operação de configuração é rejeitada. Cada regra de equilíbrio de carga e regra NAT de entrada consumirá uma gama de 8 portas. Se uma regra NAT de equilíbrio de carga ou de entrada partilhar a mesma gama de 8 que outra, não serão consumidas portas adicionais.


Se tentar dar mais portas [SNAT](load-balancer-outbound-connections.md)do que as disponíveis com base no número de endereços IP públicos, a operação de configuração é rejeitada. Por exemplo, se você der 10.000 portas por VM e sete VMs em um pool backend compartilhar um único IP público, a configuração é rejeitada. Sete multiplicados por 10.000 ultrapassa o limite de 64.000 portos. Adicione mais endereços IP públicos ao frontend da regra de saída para ativar o cenário. 


Reverter para a [atribuição de porta padrão](load-balancer-outbound-connections.md#preallocatedports) especificando 0 para o número de portas. As primeiras 50 instâncias VM terão 1024 portas, 51-100 VM instâncias chegarão a 512 até ao máximo de ocorrências. Para obter mais informações sobre a atribuição por padrão da porta SNAT, consulte a [tabela de atribuição de portas SNAT](./load-balancer-outbound-connections.md#preallocatedports).


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>Cenário 3: Permitir apenas saídas


#### <a name="details"></a>Detalhes


Utilize um equilibrador de carga padrão público para fornecer NAT de saída para um grupo de VMs. Neste cenário, use uma regra de saída por si só, sem quaisquer regras adicionais configuradas.


> [!NOTE]
> **O Azure Virtual Network NAT** pode fornecer conectividade de saída para máquinas virtuais sem a necessidade de um equilibrador de carga. Veja [o que é Azure Virtual Network NAT?](../virtual-network/nat-overview.md)

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Cenário 4: NAT de saída apenas para VMs (sem entrada)


> [!NOTE]
> **O Azure Virtual Network NAT** pode fornecer conectividade de saída para máquinas virtuais sem a necessidade de um equilibrador de carga. Veja [o que é Azure Virtual Network NAT?](../virtual-network/nat-overview.md)

#### <a name="details"></a>Detalhes


Para este cenário: As regras de saída do Azure Load Balancer e o Virtual Network NAT são opções disponíveis para saída de uma rede virtual.


1. Crie um IP ou prefixo público.
2. Criar um balanceador de carga padrão público. 
3. Crie um frontend associado ao IP público ou prefixo dedicado à saída.
4. Crie uma piscina de backend para os VMs.
5. Coloque os VMs na piscina de backend.
6. Configure uma regra de saída para permitir a saída do NAT.



Utilize um prefixo ou IP público para escalar portas [SNAT.](load-balancer-outbound-connections.md) Adicione a fonte de ligações de saída a uma lista de permitir ou negar.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Cenário 5: NAT de saída para o balançador de carga padrão interno


> [!NOTE]
> **O Azure Virtual Network NAT** pode fornecer conectividade de saída para máquinas virtuais utilizando um equilibrador de carga padrão interno. Veja [o que é Azure Virtual Network NAT?](../virtual-network/nat-overview.md)

#### <a name="details"></a>Detalhes


A conectividade de saída não está disponível para um balanceador de carga padrão interno até que tenha sido explicitamente declarado através de IPs públicos de nível de instância ou NAT de rede virtual, ou associando os membros do pool de backend com uma configuração de balanceador de carga apenas de saída. 


Para obter mais informações, consulte [a configuração do balanceador de carga apenas de saída](./egress-only.md).




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Cenário 6: Permitir que ambos os protocolos de TCP & UDP para o NAT de saída com um balanceador de carga padrão público


#### <a name="details"></a>Detalhes


Ao utilizar um balanceador de carga padrão público, o NAT de saída automático fornecido corresponde ao protocolo de transporte da regra de equilíbrio de carga. 


1. Desative o [SNAT](load-balancer-outbound-connections.md)de saída na regra de equilíbrio de carga. 
2. Configure uma regra de saída no mesmo equilibrador de carga.
3. Reutilizar a piscina de backend já utilizada pelos seus VMs. 
4. Especifique "protocolo": "Todos" como parte da regra de saída. 


Quando apenas são utilizadas regras NAT de entrada, não é fornecido nenhum NAT de saída. 


1. Coloque VMs em uma piscina de backend.
2. Definir uma ou mais configurações IP frontend com endereço IP público(es) ou prefixo IP público 
3. Configure uma regra de saída no mesmo equilibrador de carga. 
4. Especificar "protocolo": "Todos" como parte da regra de saída


## <a name="limitations"></a>Limitações

- O número máximo de portas efémeras utilizáveis por endereço IP frontal é de 64.000.
- O intervalo de tempo de saída configurável é de 4 a 120 minutos (240 a 7200 segundos).
- O equilibrador de carga não suporta o ICMP para o NAT de saída.
- As regras de saída só podem ser aplicadas à configuração ip primária de um NIC.  Não é possível criar uma regra de saída para o IP secundário de um VM ou NVA. Vários NICs são suportados.
- Todas as máquinas virtuais dentro de um **conjunto de disponibilidade** devem ser adicionadas ao pool de backend para conectividade de saída. 
- Todas as máquinas virtuais dentro de um **conjunto de escala de máquina virtual** devem ser adicionadas à piscina de backend para conectividade de saída.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Azure Standard Load Balancer](load-balancer-overview.md)
- Consulte as [nossas perguntas frequentes sobre o Equilibrador de Carga Azure](load-balancer-faqs.md)
