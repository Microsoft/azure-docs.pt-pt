---
title: 'Configurar o BGP no gateway de VPN do Azure: CLI'
description: Este artigo orienta você pela configuração do BGP com um gateway de VPN do Azure usando Azure Resource Manager e CLI.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/25/2018
ms.author: yushwang
ms.openlocfilehash: 42a07ac00fd8a26918164f6547bf57c2b021d14c
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863619"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Como configurar o BGP em um gateway de VPN do Azure usando a CLI

Este artigo ajuda você a habilitar o BGP em uma conexão VPN S2S (site a site) entre locais e uma conexão VNet a VNet (ou seja, uma conexão entre redes virtuais) usando o modelo de implantação Azure Resource Manager e CLI do Azure.

## <a name="about-bgp"></a>Sobre o BGP

O BGP é o protocolo de roteamento padrão comumente usado na Internet para trocar informações de roteamento e acessibilidade entre duas ou mais redes. O BGP permite que os gateways de VPN e seus dispositivos VPN locais, chamados de pares de BGP ou vizinhos, troquem rotas. As rotas informam aos gateways sobre a disponibilidade e a acessibilidade para que os prefixos passem pelos gateways ou roteadores envolvidos. O BGP também pode habilitar o roteamento de trânsito entre várias redes, propagando as rotas que um gateway de BGP aprende de um par de BGP para todos os outros pares de BGP.

Para obter mais informações sobre os benefícios do BGP e para entender os requisitos técnicos e as considerações de como usar o BGP, consulte [visão geral do BGP com gateways de VPN do Azure](vpn-gateway-bgp-overview.md).

Este artigo o ajuda com as seguintes tarefas:

* [Habilitar o BGP para seu gateway de VPN](#enablebgp) (obrigatório)

  Em seguida, você pode concluir qualquer uma das seções a seguir ou ambas:

* [Estabelecer uma conexão entre instalações com BGP](#crossprembgp)
* [Estabelecer uma conexão de VNet para VNet com BGP](#v2vbgp)

Cada uma dessas três seções forma um bloco de construção básico para habilitar o BGP em sua conectividade de rede. Se você concluir todas as três seções, crie a topologia conforme mostrado no diagrama a seguir:

![Topologia BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Você pode combinar essas seções para criar uma rede de trânsito de multihop mais complexa que atenda às suas necessidades.

## <a name ="enablebgp"></a>Habilitar o BGP para o gateway de VPN

Esta seção é necessária antes de executar qualquer uma das etapas nas outras duas seções de configuração. As etapas de configuração a seguir configuram os parâmetros de BGP do gateway de VPN do Azure, conforme mostrado no diagrama a seguir:

![Gateway BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Antes de começar

Instale a versão mais recente dos comandos da CLI (2,0 ou posterior). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Etapa 1: criar e configurar o TestVNet1

#### <a name="Login"></a>1. conectar-se à sua assinatura

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. criar um grupo de recursos

O exemplo a seguir cria um grupo de recursos chamado TestRG1 no local "eastus". Se você já tiver um grupo de recursos na região em que deseja criar sua rede virtual, poderá usá-lo em vez disso.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. criar TestVNet1

O exemplo a seguir cria uma rede virtual chamada TestVNet1 e três sub-redes: GatewaySubnet, FrontEnd e back-end. Quando você estiver substituindo valores, é importante que você sempre Nomeie sua sub-rede de gateway especificamente GatewaySubnet. Se der outro nome, a criação da gateway falha.

O primeiro comando cria o espaço de endereço de front-end e a sub-rede FrontEnd. O segundo comando cria um espaço de endereço adicional para a sub-rede de back-end. O terceiro e o quarto comandos criam a sub-rede de back-end e o GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Etapa 2: criar o gateway de VPN para TestVNet1 com parâmetros de BGP

#### <a name="1-create-the-public-ip-address"></a>1. criar o endereço IP público

Solicitar um endereço IP público. O endereço IP público será alocado para o gateway de VPN que você criar para sua rede virtual.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. criar o gateway de VPN com o número as

Crie o gateway de rede virtual para TestVNet1. O BGP requer um gateway de VPN baseado em rota. Você também precisa do parâmetro adicional `-Asn` para definir o número do sistema autônomo (ASN) para TestVNet1. A criação de um gateway pode levar um tempo (45 minutos ou mais) para ser concluída. 

Se você executar esse comando usando o parâmetro `--no-wait`, não verá nenhum comentário ou saída. O parâmetro `--no-wait` permite que o gateway seja criado em segundo plano. Isso não significa que o gateway de VPN é criado imediatamente.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. obter o endereço IP do par de BGP do Azure

Depois que o gateway for criado, você precisará obter o endereço IP do par de BGP no gateway de VPN do Azure. Esse endereço é necessário para configurar o gateway de VPN como um par de BGP para seus dispositivos VPN locais.

Execute o comando a seguir e marque a seção `bgpSettings` na parte superior da saída:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Depois que o gateway é criado, você pode usar esse gateway para estabelecer uma conexão entre locais ou uma conexão VNet a VNet com o BGP.

## <a name ="crossprembgp"></a>Estabelecer uma conexão entre instalações com BGP

Para estabelecer uma conexão entre locais, você precisa criar um gateway de rede local para representar o dispositivo VPN local. Em seguida, você conecta o gateway de VPN do Azure com o gateway de rede local. Embora essas etapas sejam semelhantes à criação de outras conexões, elas incluem as propriedades adicionais necessárias para especificar os parâmetros de configuração do BGP.

![BGP para entre instalações](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Etapa 1: criar e configurar o gateway de rede local

Este exercício continua a criar a configuração mostrada no diagrama. Confirme que substitui os valores pelos que pretende utilizar para a configuração. Quando estiver trabalhando com gateways de rede local, tenha em mente o seguinte:

* O gateway de rede local pode estar no mesmo local e grupo de recursos que o gateway de VPN, ou pode estar em um local e grupo de recursos diferentes. Este exemplo mostra os gateways em diferentes grupos de recursos em locais diferentes.
* O prefixo mínimo que você precisa declarar para o gateway de rede local é o endereço de host do seu endereço IP de par de BGP em seu dispositivo VPN. Nesse caso, é um prefixo/32 de 10.51.255.254/32.
* Como lembrete, você deve usar diferentes ASNs BGP entre as redes locais e a rede virtual do Azure. Se eles forem os mesmos, você precisará alterar seu ASN de VNet se os dispositivos VPN locais já usarem o ASN para emparelhar com outros vizinhos de BGP.

Antes de prosseguir, verifique se você concluiu a seção [habilitar BGP para o gateway de VPN](#enablebgp) deste exercício e se ainda está conectado à assinatura 1. Observe que, neste exemplo, você cria um novo grupo de recursos. Além disso, observe os dois parâmetros adicionais para o gateway de rede local: `Asn` e `BgpPeerAddress`.

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Etapa 2: conectar o gateway de VNet e o gateway de rede local

Nesta etapa, você cria a conexão de TestVNet1 para site5. Você deve especificar o parâmetro `--enable-bgp` para habilitar o BGP para essa conexão. 

Neste exemplo, o gateway de rede virtual e o gateway de rede local estão em grupos de recursos diferentes. Quando os gateways estiverem em grupos de recursos diferentes, você deverá especificar a ID de recurso completa dos dois gateways para configurar uma conexão entre as redes virtuais.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. obter a ID de recurso de VNet1GW

Use a saída do comando a seguir para obter a ID de recurso para VNet1GW:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Na saída, localize a linha de `"id":`. Você precisa dos valores entre aspas para criar a conexão na próxima seção.

Exemplo de saída:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

Copie os valores após `"id":` em um editor de texto, como o bloco de notas, para que você possa colá-los facilmente ao criar sua conexão. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. obter a ID de recurso de site5

Use o comando a seguir para obter a ID de recurso de site5 da saída:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. criar a conexão de TestVNet1 para site5

Nesta etapa, você cria a conexão de TestVNet1 para site5. Conforme discutido anteriormente, é possível ter conexões BGP e não BGP para o mesmo gateway de VPN do Azure. A menos que o BGP esteja habilitado na propriedade de conexão, o Azure não habilitará o BGP para essa conexão, embora os parâmetros BGP já estejam configurados em ambos os gateways. Substitua as IDs de assinatura pelos seus próprios.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Para este exercício, o exemplo a seguir lista os parâmetros a serem inseridos na seção de configuração do BGP do dispositivo VPN local:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Após alguns minutos, a ligação deverá ser estabelecida. A sessão de emparelhamento via protocolo BGP é iniciada depois que a conexão IPsec é estabelecida.

## <a name ="v2vbgp"></a>Estabelecer uma conexão de VNet para VNet com BGP

Esta seção adiciona uma conexão VNet a VNet com BGP, conforme mostrado no diagrama a seguir: 

![BGP para vnet a VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

As instruções a seguir continuam das etapas nas seções anteriores. Para criar e configurar o TestVNet1 e o gateway de VPN com BGP, você deve concluir a seção [habilitar BGP para seu gateway de VPN](#enablebgp) .

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Etapa 1: criar TestVNet2 e o gateway de VPN

É importante certificar-se de que o espaço de endereço IP da nova rede virtual, TestVNet2, não se sobreponha a nenhum dos seus intervalos de VNet.

Neste exemplo, as redes virtuais pertencem à mesma assinatura. Você pode configurar conexões de VNet para VNet entre assinaturas diferentes. Para saber mais, confira [Configurar uma conexão de vnet para vnet](vpn-gateway-howto-vnet-vnet-cli.md). Certifique-se de adicionar `-EnableBgp $True` ao criar as conexões para habilitar o BGP.

#### <a name="1-create-a-new-resource-group"></a>1. criar um novo grupo de recursos

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. criar TestVNet2 no novo grupo de recursos

O primeiro comando cria o espaço de endereço de front-end e a sub-rede FrontEnd. O segundo comando cria um espaço de endereço adicional para a sub-rede de back-end. O terceiro e o quarto comandos criam a sub-rede de back-end e o GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. criar o endereço IP público

Solicitar um endereço IP público. O endereço IP público será alocado para o gateway de VPN que você criar para sua rede virtual.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. criar o gateway de VPN com o número as

Crie o gateway de rede virtual para TestVNet2. Você deve substituir o ASN padrão em seus gateways de VPN do Azure. O ASNs para as redes virtuais conectadas deve ser diferente para habilitar o BGP e o roteamento de trânsito.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Etapa 2: conectar os gateways TestVNet1 e TestVNet2

Nesta etapa, você cria a conexão de TestVNet1 para site5. Para habilitar o BGP para essa conexão, você deve especificar o parâmetro `--enable-bgp`.

No exemplo a seguir, o gateway de rede virtual e o gateway de rede local estão em grupos de recursos diferentes. Quando os gateways estiverem em grupos de recursos diferentes, você deverá especificar a ID de recurso completa dos dois gateways para configurar uma conexão entre as redes virtuais. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. obter a ID de recurso de VNet1GW 

Obtenha a ID de recurso de VNet1GW da saída do seguinte comando:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. obter a ID de recurso de VNet2GW

Obtenha a ID de recurso de VNet2GW da saída do seguinte comando:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. criar as conexões

Crie a conexão de TestVNet1 para TestVNet2 e a conexão de TestVNet2 para TestVNet1. Substitua as IDs de assinatura pelos seus próprios.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Habilite o BGP para *ambas* as conexões.
> 
> 

Depois de concluir essas etapas, a conexão será estabelecida em alguns minutos. A sessão de emparelhamento via protocolo BGP estará ativa após a conclusão da conexão de VNet para VNet.

## <a name="next-steps"></a>Passos seguintes

Depois que a conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para obter as etapas, consulte [criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
