---
title: incluir ficheiro
description: incluir ficheiro
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 4219df03f74f737c5f2435f9bc0842189dc1fd49
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76909132"
---
| | SKU Standard | SKU Básico |
| --- | --- | --- |
| Tamanho do pool de back-end | Dá suporte a até 1000 instâncias. | Suporta até 300 instâncias. |
| Pontos de extremidade do pool de back-end | Qualquer máquina virtual em uma única rede virtual, incluindo mesclagens de máquinas virtuais, conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais. | Máquinas virtuais em um único conjunto de disponibilidade ou conjunto de dimensionamento de máquinas virtuais. |
| [Sondas de estado de funcionamento](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportamento de investigação de integridade](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | As conexões TCP permanecem ativas em uma investigação de instância __e__ em todas as investigações. | As conexões TCP permanecem ativas em uma investigação de instância. Todas as conexões TCP são encerradas quando todas as investigações estão inativas. |
| Zonas de Disponibilidade | Front-ends com redundância de zona e zonas para tráfego de entrada e de saída. Os mapeamentos de fluxos de saída sobrevivem à falha da zona. Balanceamento de carga entre zonas. | Não disponível |
| Diagnósticos | Monitor Azure. Métricas multidimensionais, incluindo contadores de byte e pacote. Status da investigação de integridade. Tentativas de conexão (TCP SYN). Integridade da conexão de saída (fluxos SNAT com êxito e com falha). Medições de plano de dados ativo. | Log Analytics do Azure somente para Load Balancer públicas. Alerta de esgotamento de SNAT. Contagem de integridade do pool de back-end. |
| Portas de alta disponibilidade | Balanceador de Carga Interno | Não disponível |
| Seguro por padrão | O IP público, os pontos de extremidade públicos Load Balancer e os pontos de extremidade internos do Load Balancer são fechados para fluxos de entrada, a menos que sejam permitidos por um grupo de segurança de rede. Observe que o tráfego interno da VNET para o balanceador de carga interno ainda é permitido. | Abrir por padrão. Grupo de segurança de rede opcional. |
| [Ligações de saída](../articles/load-balancer/load-balancer-outbound-connections.md) | Você pode definir explicitamente o NAT de saída baseado em pool com [as regras de saída](../articles/load-balancer/load-balancer-outbound-rules-overview.md). Você pode usar vários front-ends com por aceitação de regra de balanceamento de carga. Um cenário de saída _deve_ ser explicitamente criado para a máquina virtual, o conjunto de disponibilidade ou o conjunto de dimensionamento de máquinas virtuais para usar a conectividade de saída. Os pontos de extremidade de serviço de rede virtual podem ser acessados sem definir a conectividade de saída e não contam para os dados processados. Todos os endereços IP públicos, incluindo os serviços de PaaS do Azure não disponíveis como pontos de extremidade de serviço de rede virtual, devem ser acessados usando a conectividade de saída e contam para os dados processados. Quando apenas um Load Balancer interno serve a máquina virtual, conjunto de disponibilidade ou conjunto de dimensionamento de máquinas virtuais, as conexões de saída por meio do SNAT padrão não estão disponíveis. Use [regras de saída](../articles/load-balancer/load-balancer-outbound-rules-overview.md) em vez disso. A programação SNAT de saída depende do protocolo de transporte da regra de balanceamento de carga de entrada. | Front-end único, selecionado aleatoriamente quando vários front-ends estiverem presentes. Quando somente Load Balancer internas atendem a uma máquina virtual, um conjunto de disponibilidade ou um conjunto de dimensionamento de máquinas virtuais, o SNAT padrão é usado. |
| [Regras de saída](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Configuração de NAT de saída declarativa, usando endereços IP públicos ou prefixos de IP público ou ambos. Tempo limite de ociosidade de saída configurável (4-120 minutos). Alocação de porta SNAT personalizada | Não disponível |
| [Redefinição de TCP on Idle](../articles/load-balancer/load-balancer-tcp-reset.md) | Habilitar a redefinição de TCP (TCP RST) no tempo limite de ociosidade em qualquer regra | Não disponível |
| [Vários front-ends](../articles/load-balancer/load-balancer-multivip-overview.md) | Entrada e [saída](../articles/load-balancer/load-balancer-outbound-connections.md) | Apenas de entrada |
| Operações de gerenciamento | A maioria das operações < 30 segundos | 60-90 + segundos típicos |
| SLA | 99,99% para o caminho dos dados com duas máquinas virtuais saudáveis. | Não aplicável | 
| Preços | Cobrado com base no número de regras, os dados processaram a entrada e a saída associados ao recurso. | Sem custos |
|  |  |  |
