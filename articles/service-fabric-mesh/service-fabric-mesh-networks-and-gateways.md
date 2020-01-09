---
title: Introdução à rede Service Fabric do Azure
description: Saiba mais sobre redes, gateways e roteamento de tráfego inteligente na malha de Service Fabric.
author: dkkapur
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: dc793e2991783cc9b7b46d92fcc8e0267feb529b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459131"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Introdução à rede em aplicativos de Service Fabric malha
Este artigo descreve os diferentes tipos de balanceadores de carga, como os gateways conectam a rede com seus aplicativos a outras redes e como o tráfego é roteado entre os serviços em seus aplicativos.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Balanceadores de carga da camada 4 vs. camada 7
O balanceamento de carga pode ser executado em camadas diferentes no [modelo OSI](https://en.wikipedia.org/wiki/OSI_model) para rede, geralmente na camada 4 (L4) e na camada 7 (L7).  Normalmente, há dois tipos de balanceadores de carga:

- Um balanceador de carga L4 funciona na camada de transporte de rede, que lida com a entrega de pacotes sem considerar o conteúdo dos pacotes. Somente os cabeçalhos de pacote são inspecionados pelo balanceador de carga, portanto, os critérios de balanceamento são limitados a endereços IP e portas. Por exemplo, um cliente faz uma conexão TCP com o balanceador de carga. O balanceador de carga encerra a conexão (respondendo diretamente ao SYN), seleciona um back-end e faz uma nova conexão TCP para o back-end (envia um novo SYN). Um balanceador de carga L4 normalmente funciona apenas no nível da conexão ou sessão TCP/UDP L4. Portanto, o balanceamento de carga redireciona os bytes de volta e garante que os bytes da mesma sessão apareçam no mesmo back-end. O balanceador de carga L4 não reconhece os detalhes do aplicativo dos bytes que ele está movendo. Os bytes podem ser qualquer protocolo de aplicativo.

- Um balanceador de carga L7 funciona na camada de aplicativo, que lida com o conteúdo de cada pacote. Ele inspeciona o conteúdo do pacote porque ele entende protocolos como HTTP, HTTPS ou WebSockets. Isso dá ao balanceador de carga a capacidade de executar roteamento avançado. Por exemplo, um cliente faz uma única conexão HTTP/2 TCP para o balanceador de carga. Em seguida, o balanceador de carga continua fazendo duas conexões de back-end. Quando o cliente envia dois fluxos HTTP/2 para o balanceador de carga, o fluxo um é enviado para o back-end um e o fluxo dois é enviado para o back-end dois. Assim, até mesmo a multiplexação de clientes que têm cargas de solicitações amplamente diferentes será balanceada com eficiência nos back-ends. 

## <a name="networks-and-gateways"></a>Redes e gateways
No [modelo de recurso de Service Fabric](service-fabric-mesh-service-fabric-resources.md), um recurso de rede é um recurso implantável individualmente, independentemente de um recurso de aplicativo ou serviço que pode se referir a ele como sua dependência. Ele é usado para criar uma rede para seus aplicativos que estão abertos na Internet. Vários serviços de diferentes aplicativos podem fazer parte da mesma rede. Essa rede privada é criada e gerenciada pelo Service Fabric e não é uma VNET (rede virtual) do Azure. Os aplicativos podem ser adicionados e removidos dinamicamente do recurso de rede para habilitar e desabilitar a conectividade VNET. 

Um gateway é usado para ligar duas redes. O recurso de gateway implanta um [proxy Envoy](https://www.envoyproxy.io/) que fornece roteamento L4 para qualquer protocolo e roteamento L7 para roteamento de aplicativo http (S) avançado. O gateway roteia o tráfego para sua rede de uma rede externa e determina para qual serviço rotear o tráfego.  A rede externa pode ser uma rede aberta (essencialmente, a Internet pública) ou uma rede virtual do Azure, permitindo que você se conecte com seus outros aplicativos e recursos do Azure. 

![Rede e gateway][Image1]

Quando o recurso de rede é criado com `ingressConfig`, um IP público é atribuído ao recurso de rede. O IP público será vinculado ao tempo de vida do recurso de rede.

Quando um aplicativo de malha é criado, ele deve fazer referência a um recurso de rede existente. Novas portas públicas podem ser adicionadas ou as portas existentes podem ser removidas da configuração de entrada. Uma exclusão de um recurso de rede falhará se um recurso de aplicativo fizer referência a ele. Quando o aplicativo é excluído, o recurso de rede é removido.

## <a name="next-steps"></a>Passos seguintes 
Para saber mais sobre a malha de Service Fabric, leia a visão geral:
- [Visão geral da malha de Service Fabric](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png