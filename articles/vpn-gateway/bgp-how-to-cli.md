---
title: Configure BGP em Azure VPN Gateway:CLI
description: Este artigo acompanha-o através da configuração do BGP com uma porta de entrada Azure VPN utilizando o Azure Resource Manager e o CLI.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: a69ce0592b79be0868dd7c15ac054910eee75fc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89393603"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Como configurar o BGP num gateway Azure VPN utilizando o CLI

Este artigo ajuda-o a ativar o BGP numa ligação VPN site-to-site (S2S) transversal e numa ligação VNet-to-VNet (isto é, uma ligação entre redes virtuais) utilizando o modelo de implementação do Azure Resource Manager e o Azure CLI.

## <a name="about-bgp"></a>Sobre o BGP

BGP é o protocolo de encaminhamento padrão geralmente utilizado na internet para trocar informações de encaminhamento e alcance entre duas ou mais redes. A BGP permite que os gateways VPN e os seus dispositivos VPN no local, chamados pares bGP ou vizinhos, troquem rotas. As rotas informam ambos os gateways sobre a disponibilidade e a capacidade de acesso para prefixos passarem pelos gateways ou routers envolvidos. O BGP também pode permitir o encaminhamento de trânsito entre várias redes, propagando as rotas que um gateway BGP aprende de um par BGP, para todos os outros pares de BGP.

Para obter mais informações sobre os benefícios do BGP, e para compreender os requisitos técnicos e considerações da utilização do BGP, consulte [a visão geral do BGP com gateways Azure VPN](vpn-gateway-bgp-overview.md).

Este artigo ajuda-o com as seguintes tarefas:

* [Ativar o BGP para o seu gateway VPN](#enablebgp) (obrigatório)

  Em seguida, pode completar qualquer uma das seguintes secções, ou ambas:

* [Estabelecer uma ligação transversal com o BGP](#crossprembgp)
* [Estabelecer uma ligação VNet-vNet com o BGP](#v2vbgp)

Cada uma destas três secções forma um bloco básico de construção para permitir o BGP na sua conectividade de rede. Se completar as três secções, constrói a topologia como mostrado no diagrama seguinte:

![Topologia BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Você pode combinar estas secções para construir uma rede de trânsito multihop mais complexa que atenda às suas necessidades.

## <a name="enable-bgp-for-your-vpn-gateway"></a><a name ="enablebgp"></a>Ativar o BGP para o seu gateway VPN

Esta secção é necessária antes de efetuar qualquer um dos passos nas outras duas secções de configuração. Os seguintes passos de configuração configuram os parâmetros BGP do gateway Azure VPN, tal como mostrado no diagrama seguinte:

![Porta de entrada BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Antes de começar

Instale a versão mais recente dos comandos CLI (2.0 ou mais tarde). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Passo 1: Criar e configurar o TestVNet1

#### <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. Conecte-se à sua subscrição

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Criar um grupo de recursos

O exemplo a seguir cria um grupo de recursos chamado TestRG1 na localização "eastus". Se já tem um grupo de recursos na região onde pretende criar a sua rede virtual, pode usar esse.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Criar TestVNet1

O exemplo a seguir cria uma rede virtual chamada TestVNet1 e três sub-redes: GatewaySubnet, FrontEnd e BackEnd. Quando está a substituir valores, é importante que dê sempre o nome da sub-rede gateway especificamente gatewaySubnet. Se der outro nome, a criação da gateway falha.

O primeiro comando cria o espaço de endereço frontal e a sub-rede FrontEnd. O segundo comando cria um espaço de endereço adicional para a sub-rede BackEnd. Os comandos terceiro e quarto criam a sub-rede BackEnd e gatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Passo 2: Criar o gateway VPN para TestVNet1 com parâmetros BGP

#### <a name="1-create-the-public-ip-address"></a>1. Criar o endereço IP público

Solicitar um endereço IP público. O endereço IP público será atribuído ao gateway VPN que cria para a sua rede virtual.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Criar o portal VPN com o número AS

Crie o gateway de rede virtual para TestVNet1. BGP requer uma Route-Based gateway VPN. Também precisa do parâmetro adicional `-Asn` para definir o número do sistema autónomo (ASN) para o TestVNet1. A criação de um portal pode demorar um pouco (45 minutos ou mais) a ser concluída. 

Se executar este comando utilizando o `--no-wait` parâmetro, não vê qualquer feedback ou saída. O `--no-wait` parâmetro permite que o portal seja criado em segundo plano. Não significa que o portal VPN seja criado imediatamente.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Obtenha o endereço IP peer peer Azure BGP

Após a criação do gateway, você precisa obter o endereço IP peer BGP no gateway Azure VPN. Este endereço é necessário para configurar o gateway VPN como um peer BGP para os seus dispositivos VPN no local.

Executar o seguinte comando e verificar a `bgpSettings` secção na parte superior da saída:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Após a criação do gateway, pode utilizar este gateway para estabelecer uma ligação transversal ou uma ligação VNet-vNet com o BGP.

## <a name="establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembgp"></a>Estabelecer uma ligação transversal com o BGP

Para estabelecer uma ligação transversal, é necessário criar uma porta de entrada de rede local para representar o seu dispositivo VPN no local. Em seguida, ligue o gateway Azure VPN com o portal de rede local. Embora estes passos sejam semelhantes à criação de outras ligações, incluem as propriedades adicionais necessárias para especificar os parâmetros de configuração do BGP.

![BGP para instalações cruzadas](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Passo 1: Criar e configurar o portal de rede local

Este exercício continua a construir a configuração mostrada no diagrama. Confirme que substitui os valores pelos que pretende utilizar para a configuração. Quando estiver a trabalhar com os gateways de rede locais, lembre-se das seguintes coisas:

* O gateway de rede local pode estar no mesmo local e grupo de recursos que o gateway VPN, ou pode estar em um grupo de recursos e localização diferente. Este exemplo mostra os gateways em diferentes grupos de recursos em diferentes locais.
* O prefixo mínimo que precisa de declarar para o gateway de rede local é o endereço de anfitrião do seu endereço IP peer BGP no seu dispositivo VPN. Neste caso, é um prefixo /32 de 10.51.255.254/32.
* Como lembrete, deve utilizar diferentes ASNs BGP entre as suas redes no local e a rede virtual Azure. Se forem os mesmos, tem de alterar o seu VNet ASN se os seus dispositivos VPN no local já utilizarem a ASN para espreitar com outros vizinhos do BGP.

Antes de prosseguir, certifique-se de que completou o Enable BGP para a sua secção de [gateway VPN](#enablebgp) deste exercício e que ainda está ligado à Subscrição 1. Note que, neste exemplo, cria-se um novo grupo de recursos. Além disso, note os dois parâmetros adicionais para o gateway da rede local: `Asn` e `BgpPeerAddress` .

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Passo 2: Ligue o gateway VNet e o gateway de rede local

Neste passo, cria-se a ligação do TestVNet1 ao Site5. Tem de especificar o `--enable-bgp` parâmetro para ativar o BGP para esta ligação. 

Neste exemplo, o gateway de rede virtual e o gateway de rede local estão em diferentes grupos de recursos. Quando os gateways estiverem em diferentes grupos de recursos, deve especificar todo o ID de recursos dos dois gateways para configurar uma ligação entre as redes virtuais.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Obtenha o ID de recursos da VNet1GW

Utilize a saída a partir do seguinte comando para obter o ID de recurso para VNet1GW:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Na saída, encontre a `"id":` linha. É necessário que os valores dentro das aspas criem a ligação na secção seguinte.

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

Copie os valores `"id":` posteriormente a um editor de texto, como o Notepad, para que possa colar facilmente os valores ao criar a sua ligação. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Obtenha o ID de recursos do Site5

Utilize o seguinte comando para obter o ID de recurso do Site5 a partir da saída:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Criar a ligação TestVNet1-site5

Neste passo, cria-se a ligação do TestVNet1 ao Site5. Tal como discutido anteriormente, é possível ter ligações BGP e não BGP para o mesmo gateway Azure VPN. A menos que o BGP esteja ativado na propriedade de ligação, o Azure não permitirá o BGP para esta ligação, mesmo que os parâmetros BGP já estejam configurados em ambos os gateways. Substitua os IDs de subscrição pelos seus próprios.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Para este exercício, o exemplo a seguir enumera os parâmetros a introduzir na secção de configuração BGP do seu dispositivo VPN no local:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Após alguns minutos, a ligação deverá ser estabelecida. A sessão de observação do BGP começa após a criação da ligação IPsec.

## <a name="establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Estabelecer uma ligação VNet-vNet com o BGP

Esta secção adiciona uma ligação VNet-to-VNet com BGP, como mostra o seguinte diagrama: 

![BGP para VNet-to-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

As seguintes instruções continuam a partir dos passos nas secções anteriores. Para criar e configurar o TestVNet1 e o gateway VPN com BGP, tem de completar o Enable BGP para a sua secção [de gateway VPN.](#enablebgp)

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Passo 1: Criar TestVNet2 e o gateway VPN

É importante garantir que o espaço de endereço IP da nova rede virtual, TestVNet2, não se sobreponha a nenhuma das suas gamas VNet.

Neste exemplo, as redes virtuais pertencem à mesma subscrição. Pode configurar ligações VNet-vNet entre diferentes subscrições. Para saber mais, consulte [configurar uma ligação VNet-to-VNet](vpn-gateway-howto-vnet-vnet-cli.md). Certifique-se de que adiciona `-EnableBgp $True` ao criar as ligações para ativar o BGP.

#### <a name="1-create-a-new-resource-group"></a>1. Criar um novo grupo de recursos

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Criar TestVNet2 no novo grupo de recursos

O primeiro comando cria o espaço de endereço frontal e a sub-rede FrontEnd. O segundo comando cria um espaço de endereço adicional para a sub-rede BackEnd. Os comandos terceiro e quarto criam a sub-rede BackEnd e gatewaySubnet.

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

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Criar o portal VPN com o número AS

Crie o portal de rede virtual para o TestVNet2. Tem de anular a ASN predefinida nas suas portas Azure VPN. As ASNs para as redes virtuais ligadas devem ser diferentes para permitir o encaminhamento de BGP e de trânsito.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Passo 2: Ligue os gateways TestVNet1 e TestVNet2

Neste passo, cria-se a ligação do TestVNet1 ao Site5. Para ativar o BGP para esta ligação, deve especificar o `--enable-bgp` parâmetro.

No exemplo seguinte, o gateway de rede virtual e o gateway de rede local estão em diferentes grupos de recursos. Quando os gateways estiverem em diferentes grupos de recursos, deve especificar todo o ID de recursos dos dois gateways para configurar uma ligação entre as redes virtuais. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Obtenha o ID de recursos da VNet1GW 

Obtenha o ID de recursos do VNet1GW a partir da saída do seguinte comando:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Obtenha o ID de recursos da VNet2GW

Obtenha o ID de recursos do VNet2GW a partir da saída do seguinte comando:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. Criar as ligações

Crie a ligação de TestVNet1 a TestVNet2 e a ligação do TestVNet2 ao TestVNet1. Substitua os IDs de subscrição pelos seus próprios.

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

Depois de completar estes passos, a ligação será estabelecida em poucos minutos. A sessão de observação BGP terminará após a conclusão da ligação VNet-vNet.

## <a name="next-steps"></a>Passos seguintes

Depois de concluída a sua ligação, pode adicionar máquinas virtuais às suas redes virtuais. Para passos, consulte [Criar uma máquina virtual.](../virtual-machines/windows/quick-create-portal.md)
