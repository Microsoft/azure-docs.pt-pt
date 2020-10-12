---
title: 'Azure ExpressRoute: Encaminhamento assimétrico'
description: Este artigo explica-lhe as questões que poderá enfrentar com o encaminhamento assimétrico numa rede que tem múltiplas ligações a um destino.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: duau
ms.openlocfilehash: ee96abb51e03fb1dc567b66d180cb520de299d95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89566539"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Encaminhamento assimétrico com vários caminhos de rede
Este artigo explica como o tráfego de rede de encaminhamento e retorno poderá seguir rotas diferentes quando vários caminhos estão disponíveis entre a origem e o destino de rede.

É importante compreender dois conceitos para compreender o encaminhamento assimétrico. Um é o efeito de vários caminhos de rede. O outro é como os dispositivos, como uma firewall, mantêm o estado. Estes tipos de dispositivos são chamados dispositivos com monitorização de estado. Uma combinação destes dois fatores cria cenários em que o tráfego de rede é removido por um dispositivo com monitorização de estado porque o dispositivo com monitorização de estado não detetou que o tráfego teve origem com o próprio dispositivo.

## <a name="multiple-network-paths"></a>Vários caminhos de rede
Quando uma rede empresarial tem apenas uma ligação à Internet através do respetivo fornecedor de serviços Internet, todo o tráfego de e para a Internet percorre o mesmo caminho. Muitas vezes, as empresas adquirem vários circuitos, como caminhos redundantes, para melhorar o tempo de atividade da rede. Quando isto acontece, é possível que o tráfego que sai da rede para a Internet passe por uma ligação e que o tráfego de retorno passe por outra ligação. Isto é habitualmente designado por encaminhamento assimétrico. No encaminhamento assimétrico, o tráfego de rede inverso segue um caminho diferente do fluxo original.

![Rede com vários caminhos](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Embora ocorra principalmente na Internet, o encaminhamento assimétrico também se aplica a outras combinações de vários caminhos. Aplica-se, por exemplo, a um caminho da Internet e a um caminho privado que vão para o mesmo destino, bem como a vários caminhos privados que vão para o mesmo destino.

Cada router ao longo do caminho, desde a origem até ao destino, calcula o melhor caminho para chegar a um destino. A determinação do router do melhor caminho possível baseia-se em dois fatores principais:

* O encaminhamento entre redes externas baseia-se num protocolo de encaminhamento, o BGP (Border Gateway Protocol). O BGP pega nos anúncios de vizinhos e sujeita-os a uma série de passos para determinar o melhor caminho para o destino pretendido. Armazena o melhor caminho na sua tabela de encaminhamento.
* O comprimento de uma máscara de sub-rede associada a uma rota influencia os caminhos de encaminhamento. Se um router receber vários anúncios para o mesmo endereço IP, mas com máscaras de sub-rede diferentes, o router prefere o anúncio com uma máscara de sub-rede mais longa porque é considerado uma rota mais específica.

## <a name="stateful-devices"></a>Dispositivos com monitorização de estado
Os routers observam o cabeçalho IP de um pacote para fins de encaminhamento. Alguns dispositivos observam ainda de forma mais aprofundada o interior do pacote. Normalmente, estes dispositivos observam os cabeçalhos Layer4 (Protocolo de Controlo de Transmissão ou TCP; ou protocolo UDP (User Datagram Protocol)) ou até Layer7 (Camada de Aplicação). Estes tipos de dispositivos são dispositivos de segurança ou dispositivos de otimização de largura de banda. 

Uma firewall é um exemplo comum de um dispositivo com monitorização de estado. Uma firewall permite ou nega a passagem de um pacote pelas respetivas interfaces com base em vários campos, como o protocolo, a porta TCP/UDP ou os cabeçalhos de URL. Este nível de inspeção de pacotes coloca uma sobrecarga de processamento no dispositivo. Para melhorar o desempenho, a firewall inspeciona o primeiro pacote de um fluxo. Se permitir que o pacote prossiga, mantém as informações de fluxo na respetiva tabela de estados. Todos os pacotes subsequentes relacionados com este fluxo são permitidos com base na determinação inicial. Um pacote que faça parte de um fluxo existente poderá chegar à firewall. Se a firewall não tiver quaisquer informações de estado anteriores sobre o pacote, a firewall remove-o.

## <a name="asymmetric-routing-with-expressroute"></a>Encaminhamento assimétrico com o ExpressRoute
Quando se liga à Microsoft através do Azure ExpressRoute, a sua rede muda da seguinte forma:

* Tem várias ligações à Microsoft. Uma ligação é a ligação à Internet existente e a outra é através do ExpressRoute. Algum tráfego para a Microsoft poderá passar pela Internet, mas voltar através do ExpressRoute ou vice-versa.
* Pode receber mais endereços IP específicos através do ExpressRoute. Portanto, no caso do tráfego da sua rede para a Microsoft relativo a serviços oferecidos através do ExpressRoute, o router prefere sempre o ExpressRoute.

Para compreender o efeito que estas duas mudanças têm numa rede, consideremos alguns cenários. A título de exemplo, suponhamos que tem apenas um circuito para a Internet e que consome todos os serviços da Microsoft através da Internet. O tráfego da rede para a Microsoft e vice-versa atravessa a mesma ligação da Internet e passa pela firewall. A firewall regista o fluxo quando vê o primeiro pacote, sendo os pacotes de retorno permitidos porque o fluxo existe na tabela de estados.

![Encaminhamento assimétrico com o ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Em seguida, pode ativar o ExpressRoute e consumir os serviços oferecidos pela Microsoft através do ExpressRoute. Todos os outros serviços da Microsoft são consumidos através da Internet. Implemente uma firewall separada na extremidade que esteja ligada ao ExpressRoute. A Microsoft anuncia prefixos mais específicos para a sua rede através do ExpressRoute para serviços específicos. A infraestrutura de encaminhamento escolhe o ExpressRoute como o caminho preferido para esses prefixos. Se não estiver a anunciar os seus endereços IP públicos para a Microsoft através do ExpressRoute, a Microsoft comunica com os endereços IP públicos através da Internet. O encaminhamento de tráfego da rede para a Microsoft utiliza o ExpressRoute e o tráfego inverso da Microsoft utiliza a Internet. Quando a firewall na extremidade vê um pacote de resposta de um fluxo que não encontre na tabela de estados, remove o tráfego de retorno.

Se optar por anunciar o mesmo pool de tradução de endereços de rede (NAT) para ExpressRoute e para a Internet, verá problemas semelhantes com os clientes da sua rede em endereços IP privados. Os pedidos de serviços como o Windows Update são enviados através da Internet porque os endereços IP destes serviços não são anunciados através do ExpressRoute. No entanto, o tráfego de retorno chega através do ExpressRoute. Se a Microsoft receber um endereço IP com a mesma máscara de sub-rede da Internet e do ExpressRoute, prefere o ExpressRoute em detrimento da Internet. Se uma firewall ou outro dispositivo com monitorização de estado que está na extremidade da rede e voltada para o ExpressRoute não tiver informações anteriores sobre o fluxo, remove os pacotes que pertencem a esse fluxo.

## <a name="asymmetric-routing-solutions"></a>Soluções de encaminhamento assimétrico
Tem duas opções principais para resolver o problema de encaminhamento assimétrico. Uma é através de encaminhamento e a outra é através da utilização de NAT baseado na origem (SNAT).

### <a name="routing"></a>Encaminhamento
Certifique-se de que os seus endereços IP públicos são anunciados para ligações de rede alargada (WAN) adequadas. Por exemplo, se quiser utilizar a Internet para o tráfego de autenticação e o ExpressRoute para o tráfego de correio, não deve anunciar os endereços IP públicos dos Serviços de Federação do Active Directory (AD FS) através do ExpressRoute. Do mesmo modo, não exponha um servidor AD FS no local a endereços IP que o router recebe através do ExpressRoute. As rotas recebidas através do ExpressRoute são mais específicas, pelo que tornam o ExpressRoute o caminho preferido para o tráfego de autenticação para a Microsoft. Isto dá origem ao encaminhamento assimétrico.

Se quiser utilizar o ExpressRoute para autenticação, certifique-se de que anuncia os endereços IP públicos do AD FS através do ExpressRoute sem NAT. Desta forma, o tráfego que tem origem na Microsoft e vai para um servidor AD FS no local passa pelo ExpressRoute. O tráfego de retorno do cliente para a Microsoft utiliza o ExpressRoute porque é a rota preferida em detrimento da Internet.

### <a name="source-based-nat"></a>NAT baseado na origem
Outra forma de resolver problemas de encaminhamento assimétrico é através da utilização de SNAT. Por exemplo, não anunciou o endereço IP público de um servidor SMTP (Simple Mail Transfer Protocol) através do ExpressRoute porque pretende utilizar a Internet para este tipo de comunicação. Um pedido que tenha origem na Microsoft e que vá para o seu servidor SMTP no local atravessa a Internet. Faz o SNAT do pedido de entrada para um endereço IP interno. O tráfego inverso do servidor SMTP vai para a firewall de limite (que utiliza para o NAT) em vez de ir através do ExpressRoute. O tráfego de retorno regressa através da Internet.

![Configuração de rede NAT baseada na origem](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Deteção de encaminhamento assimétrico
O Traceroute é a melhor forma de garantir que o tráfego de rede atravessa o caminho esperado. Se espera que o tráfego do seu servidor SMTP para a Microsoft tome o caminho da Internet, o rastreador esperado é do servidor SMTP para o Microsoft 365. O resultado confirma se o tráfego está ou não a sair da rede para a Internet e não para o ExpressRoute.

