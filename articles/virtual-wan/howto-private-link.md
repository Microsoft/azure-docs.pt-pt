---
title: Partilhe um serviço de ligação privada através da Virtual WAN
titleSuffix: Azure Virtual WAN
description: Passos para configurar o Link Privado em WAN Virtual
services: virtual-wan
author: erjosito
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: jomore
ms.custom: fasttrack-new
ms.openlocfilehash: cc8e7314c941035207ecf809a9d85ef46bd58379
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913760"
---
# <a name="use-private-link-in-virtual-wan"></a>Use link privado em WAN virtual

[O Azure Private Link](../private-link/private-link-overview.md) é uma tecnologia que permite ligar ofertas Azure Platform-as-a-Service utilizando a conectividade de endereço IP privado expondo [pontos de acesso privados.](../private-link/private-endpoint-overview.md) Com o Azure Virtual WAN, pode implementar um Ponto Final Privado numa das redes virtuais ligadas a qualquer hub virtual. Isto proporciona conectividade a qualquer outra rede virtual ou ramo ligado ao mesmo WAN Virtual.

## <a name="before-you-begin"></a>Antes de começar

Os passos neste artigo assumem que já implementou um WAN virtual com um ou mais hubs, bem como pelo menos duas redes virtuais ligadas ao WAN Virtual.

Para criar um novo WAN virtual e um novo hub, utilize os passos nos seguintes artigos:

* [Criar uma WAN Virtual](virtual-wan-site-to-site-portal.md#openvwan)
* [Criar um hub](virtual-wan-site-to-site-portal.md#hub)
* [Ligar uma VNet a um hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="create-a-private-link-endpoint"></a><a name="endpoint"></a>Criar um ponto final de ligação privada

Você pode criar um ponto final de ligação privada para muitos serviços diferentes. Neste exemplo, utilizaremos a Base de Dados Azure SQL. Pode encontrar mais informações sobre como criar um ponto final privado para uma Base de Dados Azure SQL em [Quickstart: Criar um Ponto Final Privado utilizando o portal Azure](../private-link/create-private-endpoint-portal.md). A imagem a seguir mostra a configuração de rede da Base de Dados Azure SQL:

:::image type="content" source="./media/howto-private-link/create-private-link.png" alt-text="criar ligação privada" lightbox="./media/howto-private-link/create-private-link.png":::

Depois de criar a Base de Dados Azure SQL, pode verificar o endereço IP do ponto final privado que navega nos seus pontos finais privados:

:::image type="content" source="./media/howto-private-link/endpoints.png" alt-text="pontos finais privados" lightbox="./media/howto-private-link/endpoints.png":::

Clicando no ponto final privado que criamos, deverá ver o seu endereço IP privado, bem como o seu Nome de Domínio Totalmente Qualificado (FQDN). Note que o ponto final privado tem um endereço IP na gama do VNet onde foi implantado (10.1.3.0/24):

:::image type="content" source="./media/howto-private-link/sql-endpoint.png" alt-text="Ponto final SQL" lightbox="./media/howto-private-link/sql-endpoint.png":::

## <a name="verify-connectivity-from-the-same-vnet"></a><a name="connectivity"></a>Verificar conectividade a partir do mesmo VNet

Neste exemplo, verificaremos a conectividade com a Base de Dados Azure SQL a partir de uma máquina virtual Ubuntu com ferramentas MS SQL instaladas. O primeiro passo é verificar se a resolução de DNS funciona e que o Nome de Domínio Totalmente Qualificado da Base de Dados Azure SQL é resolvido num endereço IP privado, no mesmo VNet onde o Ponto Final Privado foi implantado (10.1.3.0/24):

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Como pode ver na saída anterior, o FQDN `wantest.database.windows.net` está mapeado `wantest.privatelink.database.windows.net` para, que a zona privada de DNS criada ao longo do ponto final privado irá resolver para o endereço IP privado `10.1.3.228` . Olhando para a zona privada do DNS confirmará que existe um registo A para o ponto final privado mapeado para o endereço IP privado:

:::image type="content" source="./media/howto-private-link/dns-zone.png" alt-text="Zona DNS" lightbox="./media/howto-private-link/dns-zone.png":::

Depois de verificar a resolução correta do DNS, podemos tentar ligar-nos à base de dados:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.3.75
```

Como pode ver, estamos a usar uma consulta SQL especial que nos dá o endereço IP de origem que o servidor SQL vê do cliente. Neste caso, o servidor vê o cliente com o seu IP privado ( `10.1.3.75` ), o que significa que o tráfego vai do VNet diretamente para o ponto final privado.

Note que é necessário definir as variáveis `username` e `password` corresponder às credenciais definidas na Base de Dados Azure SQL para que os exemplos deste guia funcionem.

## <a name="connect-from-a-different-vnet"></a><a name="vnet"></a>Conecte-se a partir de um VNet diferente

Agora que um VNet em Azure Virtual WAN tem conectividade com o ponto final privado, todos os outros VNets e ramos ligados ao WAN virtual também podem ter acesso a ele. É necessário fornecer conectividade através de qualquer um dos modelos suportados pela Azure Virtual WAN, como o [cenário Any-to-Any](scenario-any-to-any.md) ou o [cenário VNet dos Serviços Partilhados](scenario-shared-services-vnet.md), para citar dois exemplos.

Uma vez que tenha conectividade entre o VNet ou o ramo para o VNet onde o ponto final privado foi implantado, é necessário configurar a resolução DNS:

* Se ligar ao ponto final privado a partir de um VNet, pode utilizar a mesma zona privada que foi criada com a Base de Dados Azure SQL.
* Se ligar ao ponto final privado a partir de uma sucursal (VPN local-a-local, VPN ponto-a-local ou ExpressRoute), tem de utilizar no local a resolução DNS.

Neste exemplo, ligaremos a partir de um VNet diferente, por isso, primeiro, anexaremos a zona privada de DNS ao novo VNet para que as suas cargas de trabalho possam resolver o Nome de Domínio Totalmente Qualificado da Base de Dados Azure SQL ao endereço IP privado. Isto é feito através da ligação da zona privada de DNS ao novo VNet:

:::image type="content" source="./media/howto-private-link/dns-link.png" alt-text="Ligação DNS" lightbox="./media/howto-private-link/dns-link.png":::

Agora, qualquer máquina virtual no VNet anexado deve resolver corretamente a Base de Dados Azure SQL FQDN para o endereço IP privado do link privado:

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Para verificar duas vezes que este VNet (10.1.1.0/24) tem conectividade com o VNet original onde o ponto final privado foi configurado (10.1.3.0/24), pode verificar a tabela de rotas eficaz em qualquer máquina virtual no VNet:

:::image type="content" source="./media/howto-private-link/effective-routes.png" alt-text="rotas eficazes" lightbox="./media/howto-private-link/effective-routes.png":::

Como pode ver, existe uma rota que aponta para o VNet 10.1.3.0/24 injetado pelos Gateways de Rede Virtual em Azure Virtual WAN. Agora podemos finalmente testar a conectividade com a base de dados:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.1.75
```

Com este exemplo, vimos como a criação de um ponto final privado num dos VNets ligados a um WAN Virtual proporciona conectividade ao resto dos VNets e dos balcões no WAN Virtual.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
