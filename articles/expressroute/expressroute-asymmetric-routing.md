---
title: 'Azure ExpressRoute: Encaminhamento assimétrico'
description: Este artigo explica-lhe as questões que poderá enfrentar com o encaminhamento assimétrico numa rede que tem múltiplas ligações a um destino.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 0713c52c7f07db93d9ae9084062ef2c3b25a9074
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560513"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Encaminhamento assimétrico com vários caminhos de rede
Este artigo explica como o tráfego de rede pode tomar caminhos diferentes quando várias rotas estão disponíveis entre a fonte de rede e o destino.

Há dois conceitos que precisa de saber para entender o encaminhamento assimétrico. O primeiro é o efeito de vários caminhos de rede. O outro é como os dispositivos, como uma firewall mantêm o estado. Estes tipos de dispositivos são chamados dispositivos com monitorização de estado. Quando estes dois fatores são combinados, podem criar um cenário em que o tráfego de rede é abandonado pelo dispositivo imponente.  O trânsito foi desatado porque não detetou que o trânsito se originou de si próprio.

## <a name="multiple-network-paths"></a>Vários caminhos de rede
Quando uma rede empresarial tem apenas uma ligação à internet através de um fornecedor de serviços de internet, todo o tráfego de e para a internet percorre o mesmo caminho. É comum que as empresas comprem vários circuitos para criar caminhos redundantes para melhorar o tempo de funcionamento da rede. Com este tipo de configuração é possível que o tráfego vá para fora uma ligação para a internet e retorne através de um link diferente. Este cenário é vulgarmente conhecido como encaminhamento assimétrico. No encaminhamento assimétrico, o tráfego da rede de retorno toma um caminho diferente do fluxo original de saída.

![Rede com vários caminhos](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Embora o encaminhamento assimétrico ocorra geralmente quando se vai para a internet. Também acontece quando uma combinação de múltiplos caminhos é introduzida. O primeiro exemplo é quando se tem um caminho de internet e um caminho privado que vai para o mesmo destino. O segundo exemplo é quando se tem vários caminhos privados que também vão para o mesmo destino.

Cada router ao longo do caminho entre a fonte e o destino calculará o melhor caminho a seguir para chegar ao destino. O router determina o melhor caminho possível com base em dois fatores principais:

* O encaminhamento entre redes externas baseia-se num protocolo de encaminhamento, o BGP (Border Gateway Protocol). O BGP pega nos anúncios de vizinhos e sujeita-os a uma série de passos para determinar o melhor caminho para o destino pretendido. Armazena o melhor caminho na sua tabela de encaminhamento.
* O comprimento de uma máscara de sub-rede associada a uma rota influencia os caminhos de encaminhamento. Se um router receber vários anúncios para o mesmo endereço IP, o router irá selecionar o caminho com a máscara de sub-rede mais comprida porque é considerado uma rota mais específica.

## <a name="stateful-devices"></a>Dispositivos com monitorização de estado
Os routers observam o cabeçalho IP de um pacote para fins de encaminhamento. Alguns dispositivos observam ainda de forma mais aprofundada o interior do pacote. Normalmente, estes dispositivos olham para a Camada 4 - Protocolo de Controlo de Transmissão (TCP) ou protocolo de datagrama do utilizador (UDP), ou mesmo os cabeçalhos da Camada 7 (Camada de Aplicação). Estes tipos de dispositivos são dispositivos de segurança ou dispositivos de otimização de largura de banda. 

Uma firewall é um exemplo comum de um dispositivo com monitorização de estado. Uma firewall permite ou rejeita pacotes para passar através das suas interfaces com base em vários critérios. Estes critérios incluem, mas não se limitam ao protocolo, porta TCP/UDP e cabeçalhos URL. Este nível de inspeção de pacotes pode colocar uma carga de processamento pesada no dispositivo. 

Para melhorar o desempenho, a firewall inspeciona o primeiro pacote de um fluxo. Se permitir que o pacote passe pelas suas interfaces, mantém a informação de fluxo na sua tabela de estado. Quaisquer pacotes que se seguem relacionados com este fluxo são então permitidos com base na determinação inicial. Um pacote que faz parte de um fluxo existente pode chegar à firewall da qual não se originou. Uma vez que não tem informações prévias sobre o fluxo inicial, a firewall deixa cair o pacote.

## <a name="asymmetric-routing-with-expressroute"></a>Encaminhamento assimétrico com o ExpressRoute
Quando se liga à Microsoft através do Azure ExpressRoute, a sua rede muda da seguinte forma:

* Tem várias ligações à Microsoft. Um link é a sua ligação à Internet existente, e o outro é através da sua ligação ExpressRoute. Determinado tráfego destinado à Microsoft pode passar pela ligação à Internet, mas retorno através da sua ligação ExpressRoute. O mesmo também pode acontecer quando o tráfego passa por ExpressRoute, mas retorna pelo caminho da internet.
* Recebeu endereços IP mais específicos do circuito ExpressRoute. Assim, quando o tráfego da sua rede vai para a Microsoft para serviços oferecidos através do ExpressRoute, os seus routers preferirão sempre a ligação ExpressRoute.

Para entender o efeito de como estas duas mudanças têm numa rede, vamos considerar alguns cenários. Como exemplo, tem um circuito para a internet e consome todos os serviços da Microsoft através da internet. O tráfego da sua rede de e para a Microsoft atravessa a mesma ligação de internet e passa através de uma firewall. A firewall regista o fluxo quando vê o primeiro pacote. Todos os pacotes que se seguem dessa conversa são permitidos porque o fluxo existe na mesa do Estado.

![Encaminhamento assimétrico com o ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Em seguida, você traz um circuito ExpressRoute para consumir serviços oferecidos pela Microsoft em vez do ExpressRoute. Todos os outros serviços da Microsoft são consumidos através da internet. Coloque uma firewall separada na sua borda que esteja ligada à ligação ExpressRoute. A Microsoft anuncia prefixos mais específicos na sua rede sobre o ExpressRoute para determinados serviços. A infraestrutura de encaminhamento escolhe o ExpressRoute como o caminho preferido para esses prefixos. 

Se não anunciar os seus endereços IP públicos à Microsoft em vez do ExpressRoute. A Microsoft comunicará com os seus endereços IP públicos através da internet. O tráfego enviado da sua rede para a Microsoft utiliza a ligação ExpressRoute, mas o tráfego de retorno da Microsoft utilizará então o caminho da Internet. Quando a firewall na sua borda vir um pacote de resposta para um fluxo que desconhece, deixará cair os pacotes.

Se optar por anunciar a mesma piscina de tradução de endereços de rede (NAT) para ExpressRoute e para a internet. Você verá problemas semelhantes com os clientes da sua rede em endereços IP privados. Os pedidos de serviços como o Windows Update passarão pela internet porque os endereços IP para estes serviços não são publicitados através do ExpressRoute. No entanto, o tráfego de retorno voltará através do ExpressRoute. Uma vez que a Microsoft recebeu um endereço IP com a mesma máscara de sub-rede da internet e do ExpressRoute, o caminho preferido é sempre ExpressRoute. Se uma firewall ou outro dispositivo imponente na borda da rede virada para a ligação ExpressRoute não tiver informações prévias sobre um fluxo, deixará cair esses pacotes.

## <a name="asymmetric-routing-solutions"></a>Soluções de encaminhamento assimétrico
Tem duas opções disponíveis para resolver o problema do encaminhamento assimétrico. A primeira é através do encaminhamento, e a segunda é utilizando um NAT baseado em origem (SNAT).

### <a name="routing"></a>Encaminhamento
Certifique-se de que os seus endereços IP públicos são anunciados para obter ligações de rede de ampla área (WAN). Por exemplo, se pretender utilizar a internet para o tráfego de autenticação e o ExpressRoute para o seu tráfego de correio. Não anuncie os seus endereços IP públicos da Federação de Diretórios Ativos (AD FS) em expressRoute. Certifique-se também de não expor o servidor AD FS no local aos endereços IP que o router recebe sobre o ExpressRoute. As rotas recebidas através do ExpressRoute são mais específicas, pelo que tornam o ExpressRoute o caminho preferido para o tráfego de autenticação para a Microsoft. Se não prestar atenção à forma como o encaminhamento é feito na sua rede, podem surgir problemas de encaminhamento assimétricos.

Se pretender utilizar o ExpressRoute para autenticação, certifique-se de que está a publicitar endereços IP públicos AD FS em ExpressRoute sem NAT. Quando configurado desta forma, o tráfego originário da Microsoft vai para o seu servidor AD FS no local irá passar por ExpressRoute. O tráfego de retorno da sua rede que vai para a Microsoft usará o ExpressRoute porque é a rota preferida através da internet.

### <a name="source-based-nat"></a>NAT baseado na origem
Outra forma de resolver o problema do encaminhamento assimétrico é utilizando o SNAT. Por exemplo, opta por não divulgar o endereço IP público de um servidor do Simple Mail Transfer Protocol (SMTP) no local sobre o ExpressRoute. Em vez disso, pretende utilizar a internet para este tipo de comunicação. Um pedido originário da Microsoft que vai para o seu servidor SMTP no local atravessa a internet. Faz o SNAT do pedido de entrada para um endereço IP interno. O tráfego de retorno do servidor SMTP irá para a firewall de borda (que utiliza para NAT) em vez de através do ExpressRoute. Como resultado, o tráfego de retorno tomará o caminho da internet.

![Configuração de rede NAT baseada na origem](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Deteção de encaminhamento assimétrico
O Traceroute é a melhor forma de garantir que o tráfego de rede atravessa o caminho esperado. Se espera que o tráfego do seu servidor SMTP para a Microsoft tome o caminho da internet, o rastreador esperado é do servidor SMTP para o Microsoft 365. O resultado valida que o tráfego está, de facto, a deixar a sua rede em direção à internet e não em direção ao ExpressRoute.

