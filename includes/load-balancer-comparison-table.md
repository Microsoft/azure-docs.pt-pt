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
ms.openlocfilehash: 1d3ce900f7354b31e999c12b8e1eb0e23d391fcb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184680"
---
| | SKU Standard | SKU Básico |
| --- | --- | --- |
| Tamanho do conjunto de back-end | Suporta até 1000 instâncias. | Suporta até 100 instâncias. |
| Pontos finais de conjunto de back-end | qualquer máquina virtual numa única rede virtual, incluindo blend de máquinas virtuais, conjuntos de disponibilidade, conjuntos de dimensionamento de máquina virtual. | Conjunto de máquinas virtuais numa escala de máquina virtual ou conjunto de disponibilidade única. |
| [Sondas de estado de funcionamento](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Sonda de estado de funcionamento para baixo de comportamento](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | Ligações de TCP permanecem ativas na instância de pesquisa para baixo __e__ em todas as sondas para baixo. | Ligações de TCP permanecem ativas na instância de pesquisa para baixo. Todas as conexões TCP terminam em todas as sondas estão indisponíveis. |
| Zonas de Disponibilidade | SKU Standard, com redundância de zona e zonais front-ends para entrada e saída, mapeamentos de fluxos de saída sobrevivem a falha de zona, balanceamento de carga entre zonas. | Não disponível. |
| Diagnóstico | Monitor do Azure, incluindo bytes e contadores de pacotes, estado de funcionamento de métricas multidimensionais sonda de estado, as tentativas de ligação (TCP SYN), estado de funcionamento da ligação de saída (SNAT com êxito ou falhados fluxos), medidas de plano de dados do Active Directory | O Azure Log Analytics para o Balanceador de carga público apenas, alerta de esgotamento de SNAT, contagem de estado de funcionamento do conjunto de back-end. |
| HA portas | Balanceador de Carga Interno | Não disponível. |
| Seguro por padrão | IP público, o Balanceador de carga pontos finais públicos, os pontos finais são fluxos fechados para entrada, a menos que o Balanceador de carga interno na lista de permissões por um grupo de segurança de rede. | Abrir por predefinição, o grupo de segurança opcional de rede. |
| [Ligações de saída](../articles/load-balancer/load-balancer-outbound-connections.md) | Pode definir explicitamente NAT de saída com base em conjunto com [regras de saída](../articles/load-balancer/load-balancer-outbound-rules-overview.md). Pode usar vários front-ao ends com por carga balanceamento regra sair. Um cenário de saída _tem_ ser explicitamente criado para a máquina virtual, conjunto de disponibilidade, dimensionamento de máquinas virtuais definido para utilizar a conectividade de saída.  Pontos finais de serviço de rede virtual pode ser contatados sem definir a conectividade de saída e não são considerados dados processados.  Quaisquer endereços IP públicos, incluindo serviços de PaaS do Azure não está disponíveis como pontos finais de serviço de VNet, tem de ser contatados por meio de conectividade de saída e contagem para dados processados. Quando apenas um balanceador de carga interno está a servir uma máquina virtual, disponibilidade conjunto ou conjunto de dimensionamento de máquina virtual, as ligações de saída através do padrão SNAT não estão disponíveis; utilizar [regras de saída](../articles/load-balancer/load-balancer-outbound-rules-overview.md) em vez disso. Programação de SNAT de saída é o protocolo de transporte específico com base no protocolo de regra de balanceamento de carga de entrada. | Único front-end, selecionado aleatoriamente quando vários front-ends estão presentes.  Quando uma máquina virtual está a servir apenas Balanceador de carga interno, conjunto de disponibilidade ou conjunto de dimensionamento de máquina virtual, predefinição SNAT é utilizada. |
| [Regras de saída](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Declarativa configuração de NAT saída, usando pública endereços IP públicos prefixos IP ou ambas, configurável saída tempo limite de inatividade (4-120 minutos), a alocação de porta SNAT personalizada | Não disponível. |
|  [Reposição TCP em inatividade](../articles/load-balancer/load-balancer-tcp-reset.md) | Ativar o TCP repostas (TCP RST) no tempo limite de inatividade em qualquer regra | Não disponível |
| [Vários front-ends](../articles/load-balancer/load-balancer-multivip-overview.md) | Entrada e [saída](../articles/load-balancer/load-balancer-outbound-connections.md) | Apenas de entrada |
| Operações de gestão | A maioria dos segundos de < a 30 de operações | 60-90 segundos típico. |
| SLA | 99,99% para o caminho de dados com duas máquinas virtuais em bom estado. | Não aplicável. | 
| Preços | Cobrado com base no número de regras, dados processados de entrada e saída associados a recursos.  | Sem custos. |
|  |  |  |
