---
title: Introdução à rede de tecido de serviço Azure
description: Conheça redes, gateways e tráfego inteligente em Malha de Tecido de Serviço.
author: dkkapur
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: dc793e2991783cc9b7b46d92fcc8e0267feb529b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75459131"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Introdução à rede nas aplicações de malha de tecido de serviço
Este artigo descreve diferentes tipos de equilibradores de carga, como os gateways conectam a rede com as suas aplicações a outras redes, e como o tráfego é encaminhado entre os serviços nas suas aplicações.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Camada 4 vs camada 7 equilibradores de carga
O equilíbrio de carga pode ser realizado em diferentes camadas do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model) para networking, muitas vezes na camada 4 (L4) e na camada 7 (L7).  Normalmente, existem dois tipos de equilibradores de carga:

- Um equilibrador de carga L4 funciona na camada de transporte em rede, que trata da entrega de pacotes sem ter em conta o conteúdo dos pacotes. Apenas os cabeçalhos dos pacotes são inspecionados pelo equilibrista de carga, pelo que os critérios de equilíbrio estão limitados a endereços IP e portas. Por exemplo, um cliente faz uma ligação TCP ao equilibrista de carga. O equilibrista de carga termina a ligação (respondendo diretamente ao SYN), seleciona um backend e faz uma nova ligação TCP ao backend (envia um novo SYN). Um equilíbrio de carga L4 normalmente funciona apenas ao nível da ligação ou sessão L4 TCP/UDP. Assim, o balanço de carga redireciona os bytes e assegura-se de que os bytes da mesma sessão acabam no mesmo backend. O equilibrador de carga L4 desconhece quaisquer detalhes de aplicação dos bytes que está a mover-se. Os bytes podem ser qualquer protocolo de candidatura.

- Um equilibrador de carga L7 funciona na camada de aplicação, que lida com o conteúdo de cada pacote. Inspeciona o conteúdo do pacote porque compreende protocolos como HTTP, HTTPS ou WebSockets. Isto dá ao equilibrador de carga a capacidade de realizar encaminhamento avançado. Por exemplo, um cliente faz uma única ligação HTTP/2 TCP ao equilibrista de carga. Em seguida, o equilibrador de carga procede à eção de duas ligações de backend. Quando o cliente envia dois fluxos HTTP/2 para o equilibrante de carga, o stream um é enviado para apoiar um e o stream dois é enviado para apoiar dois. Assim, mesmo os clientes multiplexing que têm cargas de pedidos muito diferentes serão equilibrados de forma eficiente em todas as extremidades traseiras. 

## <a name="networks-and-gateways"></a>Redes e gateways
No Modelo de Recursos de Tecido de [Serviço,](service-fabric-mesh-service-fabric-resources.md)um recurso de rede é um recurso individualmente implantável, independente mente de um recurso de aplicação ou serviço que pode referir-se a ele como sua dependência. É usado para criar uma rede para as suas aplicações que está aberta à internet. Vários serviços de diferentes aplicações podem fazer parte da mesma rede. Esta rede privada é criada e gerida pela Service Fabric e não é uma rede virtual Azure (VNET). As aplicações podem ser adicionadas e removidas dinamicamente do recurso de rede para ativar e desativar a conectividade VNET. 

Um portal é usado para fazer a ponte de duas redes. O recurso Gateway implementa um [representante do Enviado](https://www.envoyproxy.io/) que fornece encaminhamento L4 para qualquer protocolo e encaminhamento L7 para o encaminhamento avançado de aplicações HTTP(S). O gateway encaminha o tráfego para a sua rede a partir de uma rede externa e determina qual o serviço para encaminhar o tráfego para.  A rede externa poderia ser uma rede aberta (essencialmente, a internet pública) ou uma rede virtual Azure, permitindo-lhe conectar-se com as suas outras aplicações e recursos Do Azure. 

![Rede e porta de entrada][Image1]

Quando o recurso de `ingressConfig`rede é criado com , um IP público é atribuído ao recurso da rede. O IP público estará ligado ao tempo de vida do recurso da rede.

Quando uma aplicação Mesh é criada, deve fazer referência a um recurso de rede existente. Novas portas públicas podem ser adicionadas ou as portas existentes podem ser removidas da configuração de ingresso. Uma eliminação para um recurso de rede falhará se um recurso de aplicação estiver a enreferenciar-se. Quando a aplicação é eliminada, o recurso de rede é removido.

## <a name="next-steps"></a>Passos seguintes 
Para saber mais sobre a Malha de Tecido de Serviço, leia a visão geral:
- [Visão geral da malha de tecido de serviço](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png