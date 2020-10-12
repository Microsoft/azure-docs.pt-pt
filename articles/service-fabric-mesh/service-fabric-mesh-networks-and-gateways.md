---
title: Introdução à rede de tecidos de serviço Azure
description: Saiba mais sobre redes, gateways e encaminhamento inteligente de tráfego na Malha de Tecido de Serviço.
author: georgewallace
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 372e8a46bfe588aa22b0f3be0ef8ae442fa342e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842704"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Introdução à rede em aplicações de malha de tecido de serviço
Este artigo descreve diferentes tipos de equilibradores de carga, como os gateways ligam a rede com as suas aplicações a outras redes e como o tráfego é encaminhado entre os serviços nas suas aplicações.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Camada 4 vs camada 7 equilibradores de carga
O equilíbrio da carga pode ser realizado em diferentes camadas no [modelo OSI](https://en.wikipedia.org/wiki/OSI_model) para networking, muitas vezes na camada 4 (L4) e na camada 7 (L7).  Normalmente, existem dois tipos de equilibradores de carga:

- Um equilibrador de carga L4 trabalha na camada de transporte de rede, que trata da entrega de pacotes sem ter em conta o conteúdo dos pacotes. Apenas os cabeçalhos de pacotes são inspecionados pelo equilibrador de carga, pelo que os critérios de equilíbrio são limitados aos endereços IP e portas. Por exemplo, um cliente faz uma ligação TCP ao equilibrador de carga. O equilibrador de carga termina a ligação (respondendo diretamente ao SYN), seleciona um backend e faz uma nova ligação TCP ao backend (envia um novo SYN). Um balançador de carga L4 funciona normalmente apenas ao nível da ligação ou sessão L4 TCP/UDP. Assim, o equilíbrio de carga redireciona bytes ao redor e garante que bytes da mesma sessão acabam no mesmo backend. O equilibrador de carga L4 desconhece quaisquer detalhes da aplicação dos bytes que está a mover. Os bytes podem ser qualquer protocolo de aplicação.

- Um equilibrador de carga L7 funciona na camada de aplicação, que trata do conteúdo de cada pacote. Inspeciona o conteúdo dos pacotes porque compreende protocolos como HTTP, HTTPS ou WebSockets. Isto dá ao equilibrador de carga a capacidade de realizar encaminhamento avançado. Por exemplo, um cliente faz uma única ligação HTTP/2 TCP ao equilibrador de carga. Em seguida, o balançador de carga procede à eção de duas ligações de backend. Quando o cliente envia dois streams HTTP/2 para o balançador de carga, o fluxo um é enviado para o backend um e o stream dois é enviado para o backend dois. Assim, mesmo os clientes multiplexing que têm cargas de pedido muito diferentes serão equilibrados eficientemente em todos os backends. 

## <a name="networks-and-gateways"></a>Redes e gateways
No [Modelo de Recursos de Tecido de Serviço,](service-fabric-mesh-service-fabric-resources.md)um recurso de rede é um recurso individualmente implantável, independente de um recurso de Aplicação ou Serviço que pode referir-se a ele como a sua dependência. É utilizado para criar uma rede para as suas aplicações que está aberta à internet. Vários serviços de diferentes aplicações podem fazer parte da mesma rede. Esta rede privada é criada e gerida pela Service Fabric e não é uma rede virtual Azure (VNET). As aplicações podem ser adicionadas e removidas dinamicamente do recurso de rede para permitir e desativar a conectividade VNET. 

Um portal é usado para fazer a ponte de duas redes. O recurso Gateway implementa um [proxy enviado](https://www.envoyproxy.io/) que fornece encaminhamento L4 para qualquer protocolo e encaminhamento L7 para encaminhamento avançado de aplicações HTTP(S). O gateway encaminha o tráfego para a sua rede a partir de uma rede externa e determina para que serviço encaminhar o tráfego.  A rede externa pode ser uma rede aberta (essencialmente, a internet pública) ou uma rede virtual Azure, permitindo-lhe conectar-se com as suas outras aplicações e recursos Azure. 

![Rede e gateway][Image1]

Quando o recurso de rede é criado com `ingressConfig` , um IP público é atribuído ao recurso de rede. O IP público estará ligado ao tempo de vida do recurso de rede.

Quando uma aplicação de Malha é criada, deve referenciar um recurso de rede existente. Novas portas públicas podem ser adicionadas ou as portas existentes podem ser removidas da configuração de entrada. Uma eliminação para um recurso de rede falhará se um recurso de aplicação estiver a encaminhá-lo. Quando a aplicação é eliminada, o recurso de rede é removido.

## <a name="next-steps"></a>Passos seguintes 
Para saber mais sobre a Malha de Tecido de Serviço, leia a visão geral:
- [Visão geral da malha de tecido de serviço](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png