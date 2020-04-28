---
title: Configure BGP em Azure VPN Gateway:CLI
description: Este artigo acompanha-o através da configuração do BGP com um gateway Azure VPN utilizando o Azure Resource Manager e o CLI.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/25/2018
ms.author: yushwang
ms.openlocfilehash: 42a07ac00fd8a26918164f6547bf57c2b021d14c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75863619"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Como configurar o BGP num gateway Azure VPN utilizando o CLI

Este artigo ajuda-o a ativar o BGP numa ligação VPN local-local (S2S) e uma ligação VNet-to-VNet (isto é, uma ligação entre redes virtuais) utilizando o modelo de implementação do Gestor de Recursos Azure e o Azure CLI.

## <a name="about-bgp"></a>Sobre o BGP

BGP é o protocolo padrão de encaminhamento geralmente utilizado na internet para trocar informações de encaminhamento e alcance entre duas ou mais redes. O BGP permite que os portões VPN e os seus dispositivos VPN no local, chamados pares de BGP ou vizinhos, troquem rotas. As rotas informam ambas as portas de entrada sobre a disponibilidade e alcance para os prefixos passarem pelos gateways ou routers envolvidos. O BGP também pode permitir o encaminhamento de trânsito entre várias redes, propagando as rotas que um gateway BGP aprende de um par de BGP, para todos os outros pares do BGP.

Para obter mais informações sobre os benefícios do BGP, e compreender os requisitos técnicos e considerações da utilização do BGP, consulte a [visão geral do BGP com os portões azure VPN](vpn-gateway-bgp-overview.md).

Este artigo ajuda-o com as seguintes tarefas:

* [Ativar o BGP para o seu gateway VPN](#enablebgp) (obrigatório)

  Em seguida, pode completar uma das seguintes secções, ou ambas:

* [Estabelecer uma ligação transversal com o BGP](#crossprembgp)
* [Estabelecer uma ligação VNet-to-VNet com BGP](#v2vbgp)

Cada uma destas três secções forma um bloco básico de construção para permitir o BGP na conectividade da sua rede. Se completar as três secções, constrói a topologia como mostrado no seguinte diagrama:

![Topologia bGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Pode combinar estas secções para construir uma rede de trânsito multihop mais complexa que responda às suas necessidades.

## <a name="enable-bgp-for-your-vpn-gateway"></a><a name ="enablebgp"></a>Ativar bGP para o seu gateway VPN

Esta secção é necessária antes de executar qualquer um dos passos nas outras duas secções de configuração. Os seguintes passos de configuração configuram os parâmetros BGP do gateway VPN Azure, como mostrado no seguinte diagrama:

![Porta de entrada bGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Antes de começar

Instale a versão mais recente dos comandos CLI (2.0 ou mais tarde). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Passo 1: Criar e configurar testVNet1

#### <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. Ligue-se à sua subscrição

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Criar um grupo de recursos

O exemplo seguinte cria um grupo de recursos chamado TestRG1 no local "eastus". Se já tem um grupo de recursos na região onde pretende criar a sua rede virtual, pode utilizá-la.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Criar testvnet1

O exemplo seguinte cria uma rede virtual chamada TestVNet1 e três subnets: GatewaySubnet, FrontEnd e BackEnd. Quando está a substituir valores, é importante que nomeie sempre a sua subnet gateway especificamente gatewaySubnet. Se der outro nome, a criação da gateway falha.

O primeiro comando cria o espaço de endereço frontal e a sub-rede FrontEnd. O segundo comando cria um espaço de endereço adicional para a sub-rede BackEnd. O terceiro e quarto comandos criam a subnet BackEnd e gatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Passo 2: Criar a porta de entrada VPN para TestVNet1 com parâmetros BGP

#### <a name="1-create-the-public-ip-address"></a>1. Criar o endereço IP público

Solicitar um endereço IP público. O endereço IP público será atribuído ao gateway VPN que cria para a sua rede virtual.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Criar o gateway VPN com o número AS

Crie o gateway de rede virtual para TestVNet1. BGP requer um gateway VPN baseado em rota. Também precisa do parâmetro `-Asn` adicional para definir o número do sistema autónomo (ASN) para TestVNet1. Criar um portal pode demorar um pouco (45 minutos ou mais) a concluir. 

Se executar este comando `--no-wait` utilizando o parâmetro, não vê qualquer feedback ou saída. O `--no-wait` parâmetro permite que o portal seja criado em segundo plano. Não significa que o gateway VPN seja criado imediatamente.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Obtenha o endereço IP de pares Azure BGP

Após a criação do gateway, você precisa obter o endereço IP de pares BGP no gateway VpN Azure. Este endereço é necessário para configurar o gateway VPN como um par BGP para os seus dispositivos VPN no local.

Executar o seguinte comando `bgpSettings` e verificar a secção na parte superior da saída:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Após a criação do gateway, pode utilizar esta porta de entrada para estabelecer uma ligação cross-premises ou uma ligação VNet-to-VNet com BGP.

## <a name="establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembgp"></a>Estabelecer uma ligação transversal com o BGP

Para estabelecer uma ligação transversal às instalações, é necessário criar uma porta de entrada de rede local para representar o seu dispositivo VPN no local. Em seguida, ligue o gateway Azure VPN com o portal de rede local. Embora estes passos sejam semelhantes à criação de outras ligações, incluem as propriedades adicionais necessárias para especificar os parâmetros de configuração bGP.

![BGP para cross-premises](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Passo 1: Criar e configurar o portal da rede local

Este exercício continua a construir a configuração mostrada no diagrama. Confirme que substitui os valores pelos que pretende utilizar para a configuração. Quando estiver a trabalhar com os portões da rede local, tenha em mente as seguintes coisas:

* O portal de rede local pode estar no mesmo local e grupo de recursos que o gateway VPN, ou pode estar em um local diferente e grupo de recursos. Este exemplo mostra os gateways em diferentes grupos de recursos em diferentes locais.
* O prefixo mínimo que precisa de declarar para o gateway da rede local é o endereço de anfitrião do seu endereço IP de pares BGP no seu dispositivo VPN. Neste caso, é um prefixo de 10.51.255.254/32.
* Como lembrete, deve utilizar diferentes ASNs BGP entre as suas redes no local e a rede virtual Azure. Se forem os mesmos, tem de alterar o seu VNet ASN se os seus dispositivos VPN no local já utilizarem a ASN para espreitar com outros vizinhos do BGP.

Antes de prosseguir, certifique-se de que completou o [Enable BGP para](#enablebgp) a sua secção de gateway VPN deste exercício e que ainda está ligado à Subscrição 1. Note que neste exemplo, cria um novo grupo de recursos. Além disso, repare nos dois parâmetros `Asn` `BgpPeerAddress`adicionais para o portal da rede local: e .

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Passo 2: Ligue o gateway VNet e o gateway da rede local

Neste passo, cria-se a ligação do TestVNet1 ao Site5. Deve especificar `--enable-bgp` o parâmetro para ativar o BGP para esta ligação. 

Neste exemplo, o portal de rede virtual e o portal de rede local estão em diferentes grupos de recursos. Quando os gateways estão em diferentes grupos de recursos, deve especificar toda a identificação de recursos dos dois gateways para estabelecer uma ligação entre as redes virtuais.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Obtenha a identificação de recursos da VNet1GW

Utilize a saída a partir do seguinte comando para obter o ID de recurso para VNet1GW:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Na saída, encontre `"id":` a linha. É necessário que os valores dentro das aspas criem a ligação na secção seguinte.

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

Copie os `"id":` valores depois para um editor de texto, como o Notepad, para que possa facilmente colá-los ao criar a sua ligação. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Obtenha a identificação de recursos do Site5

Utilize o seguinte comando para obter o ID de recurso do Site5 a partir da saída:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Criar a ligação TestVNet1-ao-Local5

Neste passo, cria-se a ligação do TestVNet1 ao Site5. Como discutido anteriormente, é possível ter ligações BGP e não-BGP para o mesmo gateway VPN Azure. A menos que o BGP esteja ativado na propriedade de ligação, o Azure não permitirá o BGP para esta ligação, mesmo que os parâmetros BGP já estejam configurados em ambos os gateways. Substitua as iDs de subscrição por si próprias.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Para este exercício, o exemplo seguinte lista os parâmetros para introduzir na secção de configuração BGP do seu dispositivo VPN no local:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Após alguns minutos, a ligação deverá ser estabelecida. A sessão de peering do BGP começa após a criação da ligação IPsec.

## <a name="establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Estabelecer uma ligação VNet-to-VNet com BGP

Esta secção adiciona uma ligação VNet-to-VNet com BGP, como mostra o seguinte diagrama: 

![BGP para VNet-to-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

As seguintes instruções continuam a partir dos passos das secções anteriores. Para criar e configurar o TestVNet1 e o gateway VPN com BGP, tem de completar o Enable BGP para a sua secção de [gateway VPN.](#enablebgp)

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Passo 1: Criar testVNet2 e o gateway VPN

É importante certificar-se de que o espaço de endereço IP da nova rede virtual, testVNet2, não se sobrepõe a nenhuma das suas gamas VNet.

Neste exemplo, as redes virtuais pertencem à mesma subscrição. Pode configurar ligações VNet-to-VNet entre diferentes subscrições. Para saber mais, consulte [Configure uma ligação VNet-to-VNet](vpn-gateway-howto-vnet-vnet-cli.md). Certifique-se de `-EnableBgp $True` que adiciona ao criar as ligações para ativar o BGP.

#### <a name="1-create-a-new-resource-group"></a>1. Criar um novo grupo de recursos

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Criar testVNet2 no novo grupo de recursos

O primeiro comando cria o espaço de endereço frontal e a sub-rede FrontEnd. O segundo comando cria um espaço de endereço adicional para a sub-rede BackEnd. O terceiro e quarto comandos criam a subnet BackEnd e gatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. Criar o endereço IP público

Solicitar um endereço IP público. O endereço IP público será atribuído ao gateway VPN que cria para a sua rede virtual.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Criar o gateway VPN com o número AS

Crie o portal de rede virtual para testVNet2. Deve anular o ASN padrão nos seus portões Azure VPN. As ASNs para as redes virtuais ligadas devem ser diferentes para permitir o encaminhamento de BGP e trânsito.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Passo 2: Ligar os gateways TestVNet1 e TestVNet2

Neste passo, cria-se a ligação do TestVNet1 ao Site5. Para ativar o BGP para `--enable-bgp` esta ligação, deve especificar o parâmetro.

No exemplo seguinte, o portal de rede virtual e o portal de rede local estão em diferentes grupos de recursos. Quando os gateways estão em diferentes grupos de recursos, deve especificar toda a identificação de recursos dos dois gateways para estabelecer uma ligação entre as redes virtuais. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Obtenha a identificação de recursos da VNet1GW 

Obtenha o ID de recurso da VNet1GW a partir da saída do seguinte comando:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Obtenha a identificação de recursos da VNet2GW

Obtenha o ID de recurso da VNet2GW a partir da saída do seguinte comando:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. Criar as ligações

Crie a ligação de TestVNet1 a TestVNet2 e a ligação do TestVNet2 ao TestVNet1. Substitua as iDs de subscrição por si próprias.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Ativar o BGP para *ambas as* ligações.
> 
> 

Depois de completar estes passos, a ligação será estabelecida em poucos minutos. A sessão de peering bGP será concluída após a ligação VNet-to-VNet estar concluída.

## <a name="next-steps"></a>Passos seguintes

Após a sua ligação estar concluída, pode adicionar máquinas virtuais às suas redes virtuais. Para passos, consulte [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
