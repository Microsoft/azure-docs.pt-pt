---
title: Regras de saída Azure Load Balancer
description: Este artigo explica como configurar regras de saída para controlar a saída do tráfego de internet com o Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 51810876e3636b7023ce9c9318a071636bb00c4c
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002658"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Regras de saída Azure Load Balancer

As regras de saída permitem-lhe configurar o SNAT de saída do balanceador de carga padrão público (tradução de endereço de rede de origem). Esta configuração permite-lhe utilizar o IP(s) público do seu equilibrador de carga como procuração.

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
     * Duas regras foram backend pool A usa endereço IP A e B, backend pool B usa endereço IP C e D.
* **Como as portas SNAT de saída são dadas.**
     * Backend pool B é a única piscina que faz ligações de saída, dar a todas as portas SNAT para reencar a piscina B e nenhuma para reencarnar a piscina A.
* **Quais os protocolos para fornecer tradução de saída.**
     * A piscina de backend B precisa de portas UDP para saída. Backend pool A precisa de TCP. Dê portas TCP para portas A e UDP para B.
* **Que duração utilizar para o intervalo de tempo de ligação de saída (4-120 minutos).**
     * Se houver ligações longas com keepalives, reserve portas inativas para ligações de longo prazo até 120 minutos. Assuma que as ligações velhas são abandonadas e liberte portas em 4 minutos para novas ligações 
* **Se enviar um Reset TCP no tempo limite de marcha lenta.**
     * ao cronometrar as ligações ociosas, enviamos um TCP RST para o cliente e servidor para que saibam que o fluxo está abandonado?

## <a name="outbound-rule-definition"></a>Definição de regra de saída

As regras de saída seguem a mesma sintaxe familiar que o equilíbrio de carga e as regras NAT de entrada: reserva **de**  +  **parâmetros**  +  **frontend**. 

Uma regra de saída configura a saída DO NAT para _todas as máquinas virtuais identificadas pela piscina de backend_ para serem traduzidas para o _frontend_.  

Os _parâmetros_ proporcionam um controlo adicional de grãos finos sobre o algoritmo NAT de saída.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> NAT de escala com vários endereços IP

Cada endereço IP adicional fornecido por um frontend fornece mais 64.000 portas efémeras para o balanceador de carga utilizar como portas SNAT. 

Utilize vários endereços IP para planear cenários em larga escala. Utilize regras de saída para mitigar a [exaustão do SNAT](troubleshoot-outbound-connection.md#snatexhaust). 

Também pode utilizar um [prefixo IP público](https://aka.ms/lbpublicipprefix) diretamente com uma regra de saída. 

Um prefixo IP público aumenta o dimensionamento da sua implantação. O prefixo pode ser adicionado à lista de fluxos de permitir os fluxos originários dos seus recursos Azure. Pode configurar uma configuração IP frontal dentro do equilibrador de carga para fazer referência a um prefixo de endereço IP público.  

O equilibrador de carga tem controlo sobre o prefixo IP público. A regra de saída utilizará automaticamente todos os endereços IP públicos contidos no prefixo IP público para ligações de saída. 

Cada um dos endereços IP no prefixo IP público fornece mais 64.000 portas efémeras por endereço IP para o balanceador de carga a utilizar como portas SNAT.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Tempo de saída inativo e reset TCP

As regras de saída fornecem um parâmetro de configuração para controlar o tempo limite de saída e corresponder às necessidades da sua aplicação. Intervalos de tempo de saída inativos a 4 minutos. Para obter mais informações, consulte [configurar intervalos de tempo inativos](load-balancer-tcp-idle-timeout.md). 

O comportamento padrão do equilibrador de carga é baixar o fluxo silenciosamente quando o tempo de saída tiver sido atingido. O `enableTCPReset` parâmetro permite um comportamento e controlo de aplicação previsíveis. O parâmetro dita se deve enviar o Reset TCP bidirecional (TCP RST) no tempo limite do tempo de saída. 

[Reveja o TCP Reset no tempo limite de](https://aka.ms/lbtcpreset) marcha lenta para obter detalhes, incluindo a disponibilidade da região.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Assegurar e controlar explicitamente a conectividade de saída

As regras de equilíbrio de carga fornecem uma programação automática do NAT de saída. Alguns cenários beneficiam ou obrigam a desativar a programação automática do NAT de saída pela regra de equilíbrio de carga. Desativar através da regra permite-lhe controlar ou refinar o comportamento.  

Pode utilizar este parâmetro de duas formas:

1. Prevenção do endereço IP de entrada para o SNAT de saída. Desative o SNAT de saída na regra de equilíbrio de carga.
  
2. Sintonize os parâmetros **SNAT** de saída de um endereço IP utilizado para entrada e saída simultaneamente. O NAT de saída automática deve ser desativado para permitir que uma regra de saída assuma o controlo. Para alterar a atribuição da porta SNAT de um endereço também utilizado para a entrada, o `disableOutboundSnat` parâmetro deve ser definido como verdadeiro. 

A operação para configurar uma regra de saída falhará se tentar redefinir um endereço IP que é utilizado para a entrada.  Desative primeiro o NAT de saída da regra de equilíbrio de carga.

>[!IMPORTANT]
> A sua máquina virtual não terá conectividade de saída se definir este parâmetro como verdadeiro e não tiver uma regra de saída para definir conectividade de saída.  Algumas operações do seu VM ou da sua aplicação podem depender de ter conectividade de saída disponível. Certifique-se de compreender as dependências do seu cenário e considerou o impacto de fazer esta mudança.

Às vezes é indesejável que um VM crie um fluxo de saída. Pode haver uma exigência de gestão dos destinos que recebem fluxos de saída, ou quais os destinos que iniciam os fluxos de entrada. Utilize [grupos de segurança](../virtual-network/security-overview.md) de rede para gerir os destinos que o VM atinge. Utilize NSGs para gerir quais destinos públicos iniciam fluxos de entrada.

Quando aplicar um NSG a um VM equilibrado em carga, preste atenção às [etiquetas](../virtual-network/security-overview.md#service-tags) de serviço e às [regras de segurança predefinidas](../virtual-network/security-overview.md#default-security-rules). 

Certifique-se de que o VM pode receber pedidos de sonda de saúde do Azure Load Balancer.

Se um NSG bloquear pedidos de sonda de saúde a partir da etiqueta predefinição AZURE_LOADBALANCER, a sua sonda de saúde VM falha e o VM está marcado indisponível. O equilibrador de carga deixa de enviar novos fluxos para o VM.

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

