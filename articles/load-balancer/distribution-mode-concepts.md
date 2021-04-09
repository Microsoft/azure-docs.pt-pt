---
title: Modos de distribuição do balançador de carga Azure
description: Começa a aprender sobre os diferentes modos de distribuição do Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 02/04/2021
ms.custom: template-concept
ms.openlocfilehash: fcea2e962722773f59e73df898e0188c3f6454b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99551320"
---
# <a name="azure-load-balancer-distribution-modes"></a>Modos de distribuição do balançador de carga Azure

O Azure Load Balancer suporta dois modos de distribuição para ligações de encaminhamento à sua aplicação equilibrada de carga:

* Baseado em haxixe
* Afinidade do IP de origem

## <a name="hash-based"></a>Baseado em haxixe

O modo de distribuição predefinido do Balanceador de Carga do Azure é um hash de cinco cadeias. 

A tuple é composta por:
* **IP de origem**
* **Porta de origem**
* **IP de destino**
* **Porta de destino**
* **Tipo de protocolo**

O haxixe é usado para mapear o tráfego para os servidores disponíveis. O algoritmo só proporciona adesão dentro de uma sessão de transporte. Os pacotes que estão na mesma sessão são direcionados para o mesmo DATAcenter IP atrás do ponto final equilibrado de carga. Quando o cliente inicia uma nova sessão a partir da mesma fonte IP, a porta de origem muda e faz com que o tráfego vá para um ponto final diferente do datacenter.

![Modo de distribuição baseado em haxixe de cinco tuples](./media/distribution-mode-concepts/load-balancer-distribution.png)

O modo baseado em haxixe tem um tipo de configuração:

* **Nenhum (baseado em haxixe)** - Especifica que os pedidos sucessivos do mesmo cliente podem ser tratados por qualquer máquina virtual.

## <a name="source-ip-affinity"></a>Afinidade do IP de origem

O modo de distribuição também é conhecido como afinidade de sessão ou afinidade de IP de cliente. O modo utiliza um haxixe de dois tuples (IP de origem e destino IP) ou três tuple (fonte IP, destino IP e tipo de protocolo) para mapear o tráfego para os servidores disponíveis. 

Utilizando a afinidade IP de origem, as ligações que são iniciadas a partir do mesmo computador cliente vão para o mesmo ponto final do datacenter.

A seguinte figura ilustra uma configuração de dois tuples. Note como o dois tuple passa pelo equilibrador de carga para a máquina virtual 1 (VM1). VM1 é então apoiado por VM2 e VM3.

![Modo de distribuição de afinidade de sessão de dois tuple](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

O modo de afinidade IP de origem tem dois tipos de configuração:

* **Computador do Cliente (fonte de afinidade IP 2-tuple)** - Especifica que os pedidos sucessivos do mesmo endereço IP do cliente serão tratados pela mesma máquina virtual.
* **Computador e protocolo do Cliente (fonte de afinidade IP 3-tuple)** - Especifica que os pedidos sucessivos a partir do mesmo endereço IP do cliente e combinação de protocolos serão tratados pela mesma máquina virtual.

## <a name="use-cases"></a>Casos de utilização

A afinidade IP de origem com o IP do cliente e o protocolo (fonte ip affinity 3-tuple), resolve uma incompatibilidade entre O Balançador de Carga Azure e Gateway de Desktop Remoto (GATEWAY RD). 

Outro cenário de uso é o upload dos media. O upload de dados acontece através da UDP, mas o plano de controlo é alcançado através da TCP:

* Um cliente inicia uma sessão de TCP para o endereço público equilibrado e é direcionado para um DIP específico. O canal é deixado ativo para monitorizar a saúde da ligação.
* Uma nova sessão de UDP do mesmo computador cliente é iniciada para o mesmo ponto final público equilibrado em carga. A ligação é direcionada para o mesmo ponto final DIP que a ligação TCP anterior. O upload de mídia pode ser executado em alta produção, mantendo um canal de controlo através de TCP.

> [!NOTE]
> Quando um conjunto equilibrado de carga muda removendo ou adicionando uma máquina virtual, a distribuição dos pedidos do cliente é recomputação. Não pode depender de novas ligações de clientes existentes para acabar no mesmo servidor. Além disso, a utilização do modo de distribuição de afinidade IP de origem pode causar uma distribuição desigual do tráfego. Os clientes que correm atrás de proxies podem ser vistos como uma aplicação única para clientes.


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como configurar o modo de distribuição do Balançador de Carga Azure, consulte [configurar o modo de distribuição para O Balancer de Carga Azure](load-balancer-distribution-mode.md).

