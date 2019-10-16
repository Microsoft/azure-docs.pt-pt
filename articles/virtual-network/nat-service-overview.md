---
title: O que é o serviço NAT do Azure?
titlesuffix: Azure NAT
description: Visão geral dos recursos, da arquitetura e da implementação do serviço NAT do Azure. Saiba como o serviço NAT funciona e como usá-lo na nuvem.
services: nat
documentationcenter: na
author: asudbring
ms.service: nat
Customer intent: As an IT administrator, I want to learn more about the Azure NAT service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/21/2019
ms.author: allensu
ms.openlocfilehash: 04464716384c1700571096443963499b0ca1b261
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376505"
---
# <a name="what-is-azure-nat-service-public-preview"></a>O que é o serviço NAT do Azure (visualização pública)

Você pode usar o serviço NAT do Azure para fornecer conexões de saída para destinos de endereço IP público para todas as máquinas virtuais em sua rede virtual. O serviço NAT pode acomodar diferentes tipos de cargas de trabalho fornecendo traduções sob demanda sem preplanejar por demanda por instância.

Você pode definir quais sub-redes de uma rede virtual devem usar o serviço NAT. Você pode dimensionar a conectividade de saída com um ou mais endereços IP. E você pode ajustar o tempo limite de ociosidade de 4 a 120 minutos. O serviço NAT também sempre enviará as redefinições de TCP quando as conexões inativas forem reutilizadas, como seria o caso após o tempo limite de ociosidade ter sido atingido. 

Você pode criar cenários de [zonas de disponibilidade](../availability-zones/az-overview.md) colocando o serviço NAT em uma zona de disponibilidade específica.

O serviço NAT é um serviço gerenciado e resiliente para a conectividade de saída. Você pode combinar o serviço NAT com recursos de endereço IP público padrão e o balanceador de carga padrão para criar cenários de entrada adicionais.

>[!NOTE] 
>O serviço NAT do Azure está disponível como visualização pública no momento. Atualmente, ele está disponível apenas em um conjunto limitado de [regiões](#regions). Essa visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.comsupport/legal/preview-supplemental-terms) para obter mais informações.

## <a name="nat-gateways"></a>Gateways NAT

O serviço NAT do Azure fornece um recurso de gateway NAT. Esse recurso pode ser configurado com um ou mais endereços IP públicos usando o [endereço IP público padrão](../virtual-network/virtual-network-ip-addresses-overview-arm.md#standard) ou recursos de [prefixo de IP público padrão](../virtual-network/public-ip-address-prefix.md) ou ambos. Você usa o serviço NAT Configurando sub-redes de um recurso de rede virtual para usar um gateway NAT específico. Um gateway NAT pode ser usado em várias sub-redes da mesma rede virtual. Redes virtuais diferentes precisam de gateways NAT separados.

Uma sub-rede começará a usar o gateway NAT para conexões de saída quando ela tiver sido configurada para uma sub-rede de uma rede virtual. O gateway NAT substitui o destino padrão da Internet de uma sub-rede e a configuração de um UDR não é necessária. As traduções de saída são fornecidas sob demanda, pois as máquinas virtuais as criam.

>[!IMPORTANT]
>Os fluxos de saída não terão sucesso até que você também atribua pelo menos um endereço IP público ao gateway NAT.

>[!NOTE]
> Se você precisar de garantias por alocações de máquina virtual ou precisar de granularidade para definir a conectividade de saída em um nível por instância ou pools de instâncias ou garantir portas SNAT (conversão de endereço de rede de origem) para uma instância específica, use [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) com [regras de saída](../load-balancer/load-balancer-outbound-rules-overview.md) para definir Load Balancer SNAT (conversão de endereços de rede de origem de saída) baseada em pool.   Essa flexibilidade vem com uma complexidade significativamente maior e planejamento adicional quando cargas de trabalho diferentes são combinadas ou a conectividade de saída das cargas de trabalho não pode ser dimensionada com precisão em uma instância individual.

## <a name="combination-of-inbound-and-outbound-scenarios-and-skus"></a>Combinação de cenários de entrada e saída e SKUs

Você pode criar cenários de entrada e saída nas mesmas sub-redes com o gateway NAT, combinando o gateway NAT com o IP público padrão para ter acesso em nível de instância a pontos de extremidade de Load Balancer públicos virtuais e padrão, incluindo quaisquer serviços baseados neles. O gateway NAT e o endereço IP público padrão e o balanceador de carga público Standard têm reconhecimento de direção de fluxo. Para fluxos de saída, o gateway NAT substitui todos os outros cenários de saída e tem precedência sobre [Load Balancer conectividade de saída](../load-balancer/load-balancer-outbound-connections.md) e [endereços IP públicos em nível de instância em máquinas virtuais](../load-balancer/load-balancer-outbound-connections.md) especificamente. Para fluxos originados de entrada, os endereços IP públicos em nível de instância ou os pontos de extremidade públicos em um balanceador de carga podem ser usados simultaneamente com o gateway NAT.  Quando esse cenário for concluído, os fluxos de entrada/saída serão bem-sucedidos nas mesmas sub-redes.  O cenário resultante é "seguro por padrão" para cenários originados de entrada, uma vez que um NSG é necessário e deve permitir explicitamente o tráfego originado de entrada.

Você tem a capacidade de dividir suas implantações em uma rede virtual em sub-redes para influenciar o comportamento de saída. Você também pode ter endereços IP públicos usados como endereços IP públicos em nível de instância em máquinas virtuais e o balanceador de carga forneceu a conversão de endereços de rede (SNAT) de origem de saída para conexões de saída em uma sub-rede e um gateway NAT em uma sub-rede diferente do mesma rede virtual. Não é possível combinar gateways NAT com recursos usando recursos básicos de endereço IP público ou recursos de Load Balancer básicos na mesma sub-rede. Você pode implantar os recursos básicos do SKU para uma sub-rede diferente da mesma rede virtual.

## <a name="network-address-translation"></a>Conversão de endereços de rede

Após a conclusão da configuração, o gateway de NAT fornece a conversão de endereços de rede de origem (SNAT) de máscara de porta para todos os aplicativos TCP e UDP, traduzindo o endereço IP privado de uma instância de máquina virtual para endereços IP públicos.

Os fluxos de endereços IP privados são convertidos em um ou mais endereços IP públicos com base em sua configuração. A conversão ocorre porque a origem do endereço IP privado de origem (SNAT) de fonte de endereços de rede é alterada para um endereço IP público. A representação de porta significa que a porta de origem é reescrita para distinguir os fluxos após a tradução e simplificar um mapeamento de muitos para um/muitos. O número da porta de origem para o fluxo resultante no espaço de endereço IP público é atribuído sob demanda como máquinas virtuais de origem iniciam fluxos. Várias conexões com o mesmo endereço IP público de destino, protocolo IP e a combinação de porta de destino consomem uma porta adicional por fluxo.

Quando as máquinas virtuais fazem conexões de saída nessas sub-redes, as portas de origem são dadas sob demanda e liberadas depois que o fluxo é concluído ou o tempo limite de ociosidade é atingido. Cada IP público fornece 55.000 portas SNAT para usar e você pode escalar horizontalmente usando vários endereços IP públicos. O gateway NAT tenta reutilizar portas para fluxos em destinos diferentes para aumentar ainda mais a escala de conexão de saída. 

Você pode resolver várias cargas de trabalho dentro da mesma sub-rede com facilidade.  Ao contrário da conectividade de saída de Azure Load Balancer, você não precisa prealocar ou preplanejar para o pior cenário do consumo de portas SNAT de uma máquina virtual. Em vez disso, você planeja o volume total de conexões de saída para todas as máquinas virtuais em sub-redes configuradas. Cada porta disponível é compartilhada para todas as instâncias, conforme necessário.

## <a name="timeout-and-tcp-resets"></a>Tempo limite e redefinições de TCP

O gateway NAT implementa tempos limite ociosos e envia RST (redefinições de TCP) para fluxos que não existem.  Os tempos limite podem ser configurados de 4 minutos (padrão) para 120 minutos. Um pacote TCP RST é retornado para a origem quando ele chega ao gateway NAT e não existe nenhuma conexão correspondente. Quando um fluxo atingir o tempo limite de ociosidade, o fluxo será removido do gateway NAT e a porta ficará disponível para o próximo fluxo estabelecido. Por exemplo, se uma conexão TCP tiver atingido o tempo limite de ociosidade e pacotes adicionais forem vistos para uma conexão já expirada, um RST TCP será enviado para a origem.  Seu aplicativo pode usar as keepalives TCP para sinalizar e fornecer a sincronização do estado do ponto de extremidade, se necessário.

## <a name="availability-zones"></a>Zonas de disponibilidade

Os gateways NAT podem ser colocados em uma zona de disponibilidade específica, se necessário.  

Um endereço IP público zonal deve corresponder à mesma zona de disponibilidade que o gateway de NAT. O prefixo IP público não oferece suporte a zonas de disponibilidade e não pode ser usado.

Quando um gateway de NAT zonal tiver sido configurado, o plano de dados do gateway de NAT será alinhado com e isolado para a zona de disponibilidade solicitada.

Você pode alinhar o plano de dados do gateway NAT com uma máquina virtual em uma zona específica.  Você pode fazer esse alinhamento criando uma sub-rede regional, que contém apenas máquinas virtuais na mesma zona que o gateway NAT configurado para a sub-rede.

O posicionamento da zona de disponibilidade de um gateway NAT não pode ser alterado e você não pode converter um gateway NAT de regional para zonal ou zonal para regional.

## <a name="outbound-connection-service-comparison"></a>Comparação de serviço de conexão de saída

O gateway NAT e a Load Balancer conectividade de saída são destinados a cenários diferentes. Simplicidade, alocação sob demanda e escala no nível da rede virtual versus a granularidade centrada em pool, por instância e cenários mais complicados, são as principais vantagens a serem consideradas.  Também há várias outras diferenças de comportamento e considerações de planejamento.

O serviço NAT é uma maneira mais simples de configurar a conectividade de saída para redes virtuais em vez da configuração baseada em pool para [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) usando [regras de saída](../load-balancer/load-balancer-outbound-rules-overview.md).  Você pode facilmente configurar e dimensionar a conectividade de saída para uma sub-rede e obter cenários que antes não eram possíveis.

O serviço NAT terá precedência sobre o SNAT de saída do balanceador de carga, mas ambos podem ser combinados para um cenário originado de entrada e de saída. Se você combinar os cenários originados de entrada com endereços IP públicos e pontos de extremidade de balanceador de carga, também poderá aproveitar "seguro por padrão" para o endereço IP público padrão e os balanceadores de carga padrão.  Somente depois que um NSG existir e permitir o tráfego explicitamente, o tráfego de entrada será permitido para o fluxo.

O serviço NAT pode criar conectividade somente de saída previsível para sua rede virtual. Se tudo o que você precisa é de conectividade de saída, você não precisa configurar um balanceador de carga com regras de saída, unir computadores ao pool, calcular e definir a alocação de porta SNAT ou atribuir endereços IP públicos a computadores individuais. Você pode delegar a responsabilidade pela conectividade de saída ao administrador do recurso de gateway de NAT em vez do administrador do balanceador de carga.

O serviço NAT permite que você planeje a conectividade de saída para a escala de carga de trabalho de pico de uma rede virtual ou sub-redes dentro, em vez da escala de pico da instância da máquina virtual. Você pode acomodar a demanda adicional mais facilmente aumentando o número de endereços IP públicos dinamicamente para todas as sub-redes de toda a rede virtual. Em vez de calcular e replanejar a alocação de porta SNAT do balanceador de carga, o serviço NAT compartilha portas SNAT disponíveis para a conectividade de saída e as fornece sob demanda para acomodar cargas de trabalho de intermitência ou não previsíveis facilmente.  O balanceador de carga prefixa um número específico de portas SNAT para uma determinada instância de máquina virtual.  

O serviço NAT sempre retorna pacotes RST (redefinição de TCP) para o remetente para fluxos não existentes (por exemplo, devido ao tempo limite de ociosidade). O Load Balancer Standard pode ser configurado para habilitar a [redefinição de TCP em ociosidade](../load-balancer/load-balancer-tcp-reset.md), que cria pacotes de TCP RST para os dois pontos de extremidade de uma conexão no momento do tempo limite de ociosidade.

## <a name="regions"></a>Regiões

O serviço NAT está disponível nessas regiões
- Este dos EUA 2
- E.U.A. Centro-Oeste

## <a name="limitations"></a>Limitações

- O serviço NAT não é compatível com IP público básico ou balanceadores de carga básicos na mesma sub-rede.  Eles precisam existir em uma sub-rede não servida por um gateway NAT.
- Há suporte para protocolos de aplicativo baseados em TCP e UDP.
- Os gateways NAT podem ser configurados em uma ou mais sub-redes de uma rede virtual.
- Não há suporte para IPv6.
- O prefixo IP público não dá suporte ao posicionamento zonal e não pode ser usado com um gateway NAT zonal.

## <a name="next-steps"></a>Passos seguintes

- [Forneça comentários para o roteiro do serviço NAT](https://aka.ms/natuservoice) .
