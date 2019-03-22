---
title: Introdução ao sistema de rede do Azure Service Fabric | Documentos da Microsoft
description: Saiba mais sobre redes, gateways e encaminhamento de tráfego inteligente em malha do Service Fabric.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/26/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: b0e1047c5bbd7d8caaf2afd8b002be1c46837852
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339266"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Introdução ao funcionamento em rede na malha de recursos de infraestrutura do serviço de aplicações
Este artigo descreve os diferentes tipos de balanceadores de carga, como gateways de ligar a rede com as suas aplicações a outras redes, e como o tráfego é encaminhado entre os serviços em seus aplicativos.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Camada 4 balanceadores de carga de camada 7 vs
Balanceamento de carga pode ser executado em camadas diferentes dos [modelo OSI](https://en.wikipedia.org/wiki/OSI_model) para funcionamento em rede, muitas vezes na camada 4 (L4) e de camada 7 (L7).  Normalmente, existem dois tipos de balanceadores de carga:

- Um balanceador de carga L4 funciona na rede camada de transporte, que trata da entrega de pacotes sem considerar para o conteúdo dos pacotes. Apenas os cabeçalhos de pacotes são inspecionados pelo balanceador de carga, para que os critérios balanceamento está limitado a endereços IP e portas. Por exemplo, um cliente faz uma ligação de TCP para o Balanceador de carga. O Balanceador de carga encerra a conexão (ao responder diretamente para o SYN), seleciona um back-end e faz com que uma nova ligação de TCP para o back-end (envia um novo SYN). Um balanceador de carga L4 normalmente funciona apenas no nível da ligação de TCP/UDP de L4 ou a sessão. Assim o balanceamento de carga redireciona bytes e certifica-se de que os bytes a partir da mesma sessão acabam com o mesmo back-end. O Balanceador de carga L4 tem conhecimento de quaisquer detalhes da aplicação dos bytes que estiver se movendo. Os bytes poderiam ser qualquer protocolo de aplicação.

- Um balanceador de carga L7 funciona na camada da aplicação, que lida com o conteúdo de cada pacote. Ele inspeciona o conteúdo do pacote porque ele compreende protocolos como HTTP, HTTPS ou WebSockets. O Balanceador de carga isso dá a capacidade de executar o encaminhamento avançada. Por exemplo, um cliente faz uma única ligação de TCP de HTTP/2 para o Balanceador de carga. O Balanceador de carga, em seguida, continua a fazer duas conexões de back-end. Quando o cliente envia dois fluxos HTTP/2 para o Balanceador de carga, stream um é enviado para o back-end de um e fluxo dois é enviado para dois de back-end. Assim, até mesmo multiplexação de clientes que tenham cargas de pedido diferentes irá ser balanceado com eficiência pelos back-ends. 

## <a name="networks-and-gateways"></a>Redes e gateways
Na [modelo de recursos do Service Fabric](service-fabric-mesh-service-fabric-resources.md), um recurso de rede é um recurso implementável individualmente, independente de um recurso de aplicação ou serviço que pode fazer referência a ele como sua dependência. Ele é usado para criar uma rede para as suas aplicações que está aberta à internet. Vários serviços de aplicações diferentes podem ser uma parte da mesma rede. Essa rede privada é criado e gerido pelo serviço de recursos de infraestrutura e não é uma rede virtual do Azure (VNET). Aplicativos podem ser dinamicamente adicionados e removidos do recurso de rede para ativar e desativar a conectividade VNET. 

Um gateway é utilizado para ligar duas redes. O recurso de Gateway implementa uma [proxy o Envoy](https://www.envoyproxy.io/) que fornece L4 encaminhamento para qualquer protocolo e L7 encaminhamento para o encaminhamento da aplicação de HTTP (S) avançada. O gateway encaminha o tráfego na sua rede a partir de uma rede externa e determina qual de serviço para encaminhar o tráfego.  Rede externa pode ser uma rede aberta (essencialmente, a internet pública) ou uma rede virtual do Azure, permitindo-lhe ligar-se com as suas aplicações do Azure e outros recursos. 

![Rede e de gateway][Image1]

Quando o recurso de rede é criado com `ingressConfig`, um IP público é atribuído ao recurso de rede. O IP público vai ser vinculado ao tempo de vida do recurso de rede.

Quando um aplicativo de malha é criado, ele deve fazer referência a um recurso de rede existente. Novas portas públicas podem ser adicionadas ou portas existentes podem ser removidas da configuração de entrada. Uma exclusão de um recurso de rede irá falhar se um recurso de aplicação está a referenciar. Quando o aplicativo é excluído, o recurso de rede é removido.

## <a name="next-steps"></a>Passos Seguintes 
Para saber mais sobre a malha de recursos de infraestrutura do serviço, leia a visão geral:
- [Descrição geral de malha do Service Fabric](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png