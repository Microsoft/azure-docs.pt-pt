---
title: 'Ligue um VNet a outro VNet utilizando uma ligação VNet-to-VNet de Gateway Azure VPN: PowerShell'
description: Ligue redes virtuais entre si com uma ligação VNet a VNet e o PowerShell.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 7de83302dd91d7d679b9c35718d184a9767ba436
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655362"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-powershell"></a>Configurar uma ligação de gateway de VPN de VNet a VNet com o PowerShell

Este artigo ajuda-o a ligar redes virtuais com o tipo de ligação VNet a VNet. As redes virtuais podem estar nas mesmas regiões ou em regiões diferentes e pertencer às mesmas subscrições ou a subscrições diferentes. Ao ligar VNets de diferentes subscrições, estas não têm de estar associadas ao mesmo inquilino do Active Directory.

Os passos deste artigo aplicam-se ao modelo de implementação Resource Manager e o PowerShell. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [CLI do Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Ligue diferentes modelos de implementação - portal do Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Ligue diferentes modelos de implementação - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

## <a name="about-connecting-vnets"></a><a name="about"></a>Sobre a ligação de VNets

Existem múltiplas formas de ligar VNets. As secções abaixo descrevem as diferentes formas de ligar redes virtuais.

### <a name="vnet-to-vnet"></a>VNet a VNet

Configurar uma ligação VNet a VNet é uma boa forma de ligar facilmente as VNets. Ligar uma rede virtual a outra com o tipo de ligação VNet a VNet (VNet2VNet) é semelhante a criar uma ligação IPsec Site a Site a uma localização no local.  Ambos os tipos de conectividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE e funcionam da mesma forma quando estão a comunicar. A diferença entre os tipos de ligação é a forma como o gateway de rede local é configurado. Quando cria uma ligação VNet a VNet, não vê o espaço de endereços do gateway de rede local. Este é criado e preenchido automaticamente. Se atualizar o espaço de endereços de uma VNet, a outra VNet reconhece automaticamente que deve efetuar o encaminhamento para o espaço de endereços atualizado. Criar uma ligação VNet a VNet é, normalmente, mais rápido e fácil do que criar uma ligação Site a Site entre VNets.

### <a name="site-to-site-ipsec"></a>Site a Site (IPsec)

Se estiver a trabalhar com uma configuração de rede mais complicada, poderá preferir ligar as VNets utilizando os passos [Site a Site](vpn-gateway-create-site-to-site-rm-powershell.md), em vez de utilizar os passos de VNet a VNet. Quando utilizar os passos de Site a Site, pode criar e configurar manualmente os gateways de rede local. O gateway de rede local para cada VNet trata a outra VNet como um site local. Desta forma, pode especificar um espaço de endereços adicional para o gateway de rede local, de modo a encaminhar o tráfego. Se o espaço de endereço para uma VNet for alterado, terá de atualizar o gateway de rede local correspondente para refletir a alteração. Não será atualizado automaticamente.

### <a name="vnet-peering"></a>VNet peering

Poderá querer considerar ligar às VNets utilização o VNet Peering. O VNet peering não utiliza um gateway de VPN e tem restrições de diferentes. Além disso, o [preço do VNet peering](https://azure.microsoft.com/pricing/details/virtual-network) é calculado de forma diferente que os [preços dos Gateways VPN de VNet a VNet](https://azure.microsoft.com/pricing/details/vpn-gateway). Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a><a name="why"></a>Porquê criar uma ligação VNet a VNet?

Poderá pretender ligar redes virtuais utilizando uma ligação VNet a VNet pelos seguintes motivos:

* **Geopresença e georredundância entre várias regiões**

  * Pode configurar a sua própria georreplicação ou sincronização com uma conetividade segura sem passar por pontos finais com acesso à Internet.
  * Com o Gestor de Tráfego e o Balanceador de Carga do Azure, pode configurar uma carga de trabalho de elevada disponibilidade com georredundância em várias regiões do Azure. Um exemplo importante consiste em configurar o SQL Always On com Grupos de Disponibilidade propagando-se em várias regiões do Azure.
* **Aplicações multicamadas regionais com isolamento ou limites administrativos**

  * Na mesma região, pode configurar aplicações de várias camadas com várias redes virtuais ligadas em conjunto devido a requisitos de isolamento ou administrativos.

A comunicação VNet a VNet pode ser combinada com configurações multilocal. Este procedimento permite-lhe estabelecer topologias de rede que combinam uma conetividade em vários locais com uma conetividade de rede intervirtual.

## <a name="which-vnet-to-vnet-steps-should-i-use"></a><a name="steps"></a>Que passos de VNet a VNet devo utilizar?

Neste artigo, verá dois conjuntos de passos diferentes. Um conjunto de passos para [VNets que residem na mesma subscrição](#samesub) e outro para [VNets que residem em diferentes subscrições](#difsub).
A principal diferente entre ambos os conjuntos é que tem de utilizar sessões do PowerShell separadas quando configurar as ligações para VNets que residem em subscrições diferentes. 

Neste exercício, pode combinar configurações ou escolher apenas aquela com que quer trabalhar. Todas as configurações utilizam o tipo de ligação VNet a VNet. O tráfego de rede flui entre as VNets que estão ligadas diretamente entre si. Neste exercício, o tráfego de TestVNet4 não é encaminhado para TestVNet5.

* [VNets que residem na mesma subscrição](#samesub): Os passos para esta configuração utilizam TestVNet1 e TestVNet4.

  ![Diagrama que mostra passos de Rede V Net-a-V para V Nets que residem na mesma subscrição.](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

* [VNets que residem em diferentes subscrições](#difsub): Os passos para esta configuração utilizam o TestVNet1 e o TestVNet5.

  ![Diagrama v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

## <a name="how-to-connect-vnets-that-are-in-the-same-subscription"></a><a name="samesub"></a>Como ligar VNets que estão na mesma subscrição

### <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Como leva até 45 minutos para criar uma porta de entrada, a Azure Cloud Shell irá periodicamente cronometrar durante este exercício. Pode reiniciar a Cloud Shell clicando na parte superior esquerda do terminal. Certifique-se de que redeclare quaisquer variáveis quando reiniciar o terminal.

* Se preferir instalar a versão mais recente do módulo Azure PowerShell localmente, consulte [Como instalar e configurar a Azure PowerShell](/powershell/azure/).

### <a name="step-1---plan-your-ip-address-ranges"></a><a name="Step1"></a>Passo 1 - Planear os intervalos de endereços IP

Nos passos seguintes, vai criar duas redes virtuais, juntamente com as respetivas sub-redes de gateway e configurações. Depois, vai criar uma ligação de VPN entre as duas VNets. É importante planear os intervalos de endereços IP da configuração da rede. Nota: precisa confirmar que nenhum dos intervalos de VNet ou intervalos de rede local se sobrepõe de modo algum. Nestes exemplos, não incluímos um servidor DNS. Se pretender a resolução de nomes para as suas redes virtuais, veja [Name resolution](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) (Resolução de nomes).

Utilizamos os seguintes valores nos exemplos:

**Valores da TestVNet1:**

* Nome da VNet: TestVNet1
* Grupo de Recursos: TestRG1
* Localização: E.U.A. Leste
* TestVNet1: 10.11.0.0/16 e 10.12.0.0/16
* Front-End: 10.11.0.0/24
* Back-End: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* GatewayName: VNet1GW
* IP Público: VNet1GWIP
* VPNType: RouteBased
* Ligação (1 a 4): VNet1toVNet4
* Ligação(1 a 5): VNet1aVNet5 (Para VNets em subscrições diferentes)
* ConnectionType: VNet2VNet

**Valores da TestVNet4:**

* Nome da VNet: TestVNet4
* TestVNet2: 10.41.0.0/16 e 10.42.0.0/16
* Front-End: 10.41.0.0/24
* Back-End: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* Grupo de Recursos: TestRG4
* Localização: E.U.A. Oeste
* GatewayName: VNet4GW
* IP Público: VNet4GWIP
* VPNType: RouteBased
* Ligação: VNet4toVNet1
* ConnectionType: VNet2VNet


### <a name="step-2---create-and-configure-testvnet1"></a><a name="Step2"></a>Passo 2 - Criar e configurar a TestVNet1

1. Verifique as definições da subscrição.

   Ligue-se à sua conta se estiver a executar o PowerShell localmente no seu computador. Se estiver a utilizar o Azure Cloud Shell, está ligado automaticamente.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Verifique as subscrições da conta.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

   Se tiver mais de uma subscrição, especifique a subscrição que pretende utilizar.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName nameofsubscription
   ```
2. Declarar as variáveis. Este exemplo declara as variáveis com os valores deste exercício. Na maioria dos casos, deverá substituir os valores pelos seus próprios. Contudo, pode utilizar estas variáveis se estiver a executar os passos para se familiarizar com este tipo de configuração. Modifique as variáveis, se necessário, e, em seguida, copie e cole-as na consola do PowerShell.

   ```azurepowershell-interactive
   $RG1 = "TestRG1"
   $Location1 = "East US"
   $VNetName1 = "TestVNet1"
   $FESubName1 = "FrontEnd"
   $BESubName1 = "Backend"
   $VNetPrefix11 = "10.11.0.0/16"
   $VNetPrefix12 = "10.12.0.0/16"
   $FESubPrefix1 = "10.11.0.0/24"
   $BESubPrefix1 = "10.12.0.0/24"
   $GWSubPrefix1 = "10.12.255.0/27"
   $GWName1 = "VNet1GW"
   $GWIPName1 = "VNet1GWIP"
   $GWIPconfName1 = "gwipconf1"
   $Connection14 = "VNet1toVNet4"
   $Connection15 = "VNet1toVNet5"
   ```
3. Crie um grupo de recursos.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
4. Criar as configurações de sub-rede da TestVNet1. Este exemplo cria uma rede virtual com o nome TestVNet1 e três sub-redes, uma chamada GatewaySubnet, outra FrontEnd e a última BackEnd. Quando estiver a substituir os valores, é importante que dê sempre à sub-rede do gateway o nome específico GatewaySubnet. Se der outro nome, a criação da gateway falha. Por esta razão, não é atribuído através de variável abaixo.

   O exemplo seguinte utiliza as variáveis que definiu anteriormente. Neste exemplo, a sub-rede do gateway está a utilizar um /27. Embora seja possível criar uma sub-rede de gateway tão pequena como /29, recomendamos que crie uma sub-rede maior que inclui mais endereços selecionando pelo menos /28 ou /27. Desta forma, se quiser ter mais configurações no futuro, haverá endereços suficientes para as acomodar.

   ```azurepowershell-interactive
   $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
   $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
   $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $GWSubPrefix1
   ```
5. Criar a TestVNet1.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
   -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
   ```
6. Peça para que seja atribuído um endereço IP público ao gateway que vai criar para a VNet. Tenha em atenção que o AllocationMethod é Dinâmico. Não pode especificar o endereço IP que pretende utilizar. É atribuído dinamicamente ao seu gateway. 

   ```azurepowershell-interactive
   $gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
   -Location $Location1 -AllocationMethod Dynamic
   ```
7. Criar a configuração do gateway. A configuração do gateway define a sub-rede e o endereço IP público a utilizar. Utilize o exemplo para criar a configuração do gateway.

   ```azurepowershell-interactive
   $vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
   $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
   -Subnet $subnet1 -PublicIpAddress $gwpip1
   ```
8. Criar o gateway da TestVNet1. Neste passo, vai criar o gateway de rede virtual da TestVNet1. As configurações VNet a VNet requerem um VpnType RouteBased. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
   -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
   -VpnType RouteBased -GatewaySku VpnGw1
   ```

Depois de terminar os comandos, levará até 45 minutos para criar esta porta de entrada. Se estiver a utilizar o Azure Cloud Shell, pode reiniciar a sessão Cloud Shell clicando na parte superior esquerda do terminal Cloud Shell e, em seguida, configurar o TestVNet4. Não é preciso esperar até que o portal TestVNet1 esteja completo.

### <a name="step-3---create-and-configure-testvnet4"></a>Passo 3 – Criar e configurar a TestVNet4

Assim que tiver configurado a TestVNet1, crie a TestVNet4. Siga os passos abaixo, substituindo os valores pelos seus, quando necessário.

1. Conecte-se e declare as suas variáveis. Confirme que substitui os valores pelos que pretende utilizar para a configuração.

   ```azurepowershell-interactive
   $RG4 = "TestRG4"
   $Location4 = "West US"
   $VnetName4 = "TestVNet4"
   $FESubName4 = "FrontEnd"
   $BESubName4 = "Backend"
   $VnetPrefix41 = "10.41.0.0/16"
   $VnetPrefix42 = "10.42.0.0/16"
   $FESubPrefix4 = "10.41.0.0/24"
   $BESubPrefix4 = "10.42.0.0/24"
   $GWSubPrefix4 = "10.42.255.0/27"
   $GWName4 = "VNet4GW"
   $GWIPName4 = "VNet4GWIP"
   $GWIPconfName4 = "gwipconf4"
   $Connection41 = "VNet4toVNet1"
   ```
2. Crie um grupo de recursos.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG4 -Location $Location4
   ```
3. Criar as configurações de sub-rede da TestVNet4.

   ```azurepowershell-interactive
   $fesub4 = New-AzVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
   $besub4 = New-AzVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
   $gwsub4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $GWSubPrefix4
   ```
4. Criar a TestVNet4.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
   -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
   ```
5. Solicitar um endereço IP público.

   ```azurepowershell-interactive
   $gwpip4 = New-AzPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
   -Location $Location4 -AllocationMethod Dynamic
   ```
6. Criar a configuração do gateway.

   ```azurepowershell-interactive
   $vnet4 = Get-AzVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
   $subnet4 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
   $gwipconf4 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
   ```
7. Criar o gateway da TestVNet4. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
   -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
   -VpnType RouteBased -GatewaySku VpnGw1
   ```

### <a name="step-4---create-the-connections"></a>Passo 4 - Criar as ligações

Aguarde até que ambos os portões estejam concluídos. Reinicie a sessão Azure Cloud Shell e copie e cole as variáveis desde o início do Passo 2 e passo 3 na consola para redeclare valores.

1. Obter os gateways da rede virtual.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   $vnet4gw = Get-AzVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
   ```
2. Criar a ligação da TestVNet1 para a TestVNet4. Neste passo, vai criar a ligação da TestVNet1 à TestVNet4. Nos exemplos, verá uma chave partilhada referenciada. Pode utilizar os seus próprios valores para a chave partilhada. Importante: a chave partilhada tem de corresponder a ambas as ligações. A criação de uma ligação pode demorar algum tempo.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
   -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
   -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
3. Criar a ligação da TestVNet4 para a TestVNet1. Este passo é semelhante ao anterior, exceto que está a criar a ligação da TestVNet4 para a TestVNet1. Verifique se as chaves partilhadas correspondem. Após alguns minutos, estará ligado.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
   -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
   -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
4. Verifique a ligação. Veja a secção [Como verificar a ligação](#verify).

## <a name="how-to-connect-vnets-that-are-in-different-subscriptions"></a><a name="difsub"></a>Como ligar VNets que estão em subscrições diferentes

Neste cenário, vai ligar TestVNet1 e TestVNet5. O TestVNet1 e o TestVNet5 residem em diferentes subscrições. As subscrições não têm de estar associadas ao mesmo inquilino do Active Directory.

A diferença entre estes passos e as definições anteriores é que alguns dos passos de configuração têm de ser realizados numa sessão separada do PowerShell no contexto da segunda subscrição. especialmente quando as duas subscrições pertencem a organizações distintas.

Devido à alteração do contexto de subscrição neste exercício, poderá ser mais fácil utilizar o PowerShell localmente no seu computador, em vez de utilizar a Azure Cloud Shell, quando chegar ao Passo 8.

### <a name="step-5---create-and-configure-testvnet1"></a>Passo 5 - Criar e configurar a TestVNet1

Tem de concluir o [Passo 1](#Step1) e o [Passo 2](#Step2) da secção anterior para criar e configurar a TestVNet1 e o Gateway de VPN da TestVNet1. Para esta configuração, não tem de criar a TestVNet4 da secção anterior, embora se a criar, não entrará em conflito com estes passos. Depois de concluir o Passo 1 e o Passo 2, avance para o Passo 6 para criar a TestVNet5.

### <a name="step-6---verify-the-ip-address-ranges"></a>Passo 6 – Verificar os intervalos de endereços IP

É importante garantir que o espaço de endereços IP da nova rede virtual, TestVNet5, não se sobrepõe a qualquer um dos intervalos de VNets ou intervalos de gateways de rede local. Neste exemplo, as redes virtuais poderão pertencer a diferentes organizações. Para este exercício, pode utilizar os seguintes valores para a TestVNet5:

**Valores da TestVNet5:**

* Nome da VNet: TestVNet5
* Grupo de Recursos: TestRG5
* Localização: Leste do Japão
* TestVNet5: 10.51.0.0/16 e 10.52.0.0/16
* Front-End: 10.51.0.0/24
* Back-End: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* GatewayName: VNet5GW
* IP Público: VNet5GWIP
* VPNType: RouteBased
* Ligação: VNet5toVNet1
* ConnectionType: VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a>Passo 7 – Criar e configurar a TestVNet5

Este passo tem de ser realizado no contexto da nova subscrição. Esta parte pode ser realizada pelo administrador noutra organização que seja proprietária da subscrição.

1. Declarar as variáveis. Confirme que substitui os valores pelos que pretende utilizar para a configuração.

   ```azurepowershell-interactive
   $Sub5 = "Replace_With_the_New_Subscription_Name"
   $RG5 = "TestRG5"
   $Location5 = "Japan East"
   $VnetName5 = "TestVNet5"
   $FESubName5 = "FrontEnd"
   $BESubName5 = "Backend"
   $GWSubName5 = "GatewaySubnet"
   $VnetPrefix51 = "10.51.0.0/16"
   $VnetPrefix52 = "10.52.0.0/16"
   $FESubPrefix5 = "10.51.0.0/24"
   $BESubPrefix5 = "10.52.0.0/24"
   $GWSubPrefix5 = "10.52.255.0/27"
   $GWName5 = "VNet5GW"
   $GWIPName5 = "VNet5GWIP"
   $GWIPconfName5 = "gwipconf5"
   $Connection51 = "VNet5toVNet1"
   ```
2. Estabelecer ligação à subscrição 5. Abra a consola do PowerShell e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar na ligação:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Verifique as subscrições da conta.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

   Especifique a subscrição que pretende utilizar.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName $Sub5
   ```
3. Criar um novo grupo de recursos.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG5 -Location $Location5
   ```
4. Criar as configurações de sub-rede para TestVNet5.

   ```azurepowershell-interactive
   $fesub5 = New-AzVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
   $besub5 = New-AzVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
   $gwsub5 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
   ```
5. Criar a TestVNet5.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
   -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
   ```
6. Solicitar um endereço IP público.

   ```azurepowershell-interactive
   $gwpip5 = New-AzPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
   -Location $Location5 -AllocationMethod Dynamic
   ```
7. Criar a configuração do gateway.

   ```azurepowershell-interactive
   $vnet5 = Get-AzVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
   $subnet5  = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
   $gwipconf5 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
   ```
8. Criar o gateway da TestVNet5.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
   -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1
   ```

### <a name="step-8---create-the-connections"></a>Passo 8 - Criar as ligações

Neste exemplo, uma vez que os gateways estão em subscrições diferentes, dividimos este passo em duas sessões do PowerShell marcadas como [Subscrição 1] e [Subscrição 5].

1. **[Assinatura 1]** Obtenha o portal de rede virtual para a Assinatura 1. Iniciar s.000 e ligar à Subscrição 1 antes de executar o seguinte exemplo:

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   ```

   Copie a saída dos seguintes elementos e envie-os ao administrador da Subscrição 5 por e-mail ou outro método.

   ```azurepowershell-interactive
   $vnet1gw.Name
   $vnet1gw.Id
   ```

   Estes dois elementos terão valores semelhantes ao seguinte exemplo de saída:

   ```
   PS D:\> $vnet1gw.Name
   VNet1GW
   PS D:\> $vnet1gw.Id
   /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
   ```
2. **[Assinatura 5]** Obtenha o portal de rede virtual para a Assinatura 5. Iniciar s.A. e ligar à Subscrição 5 antes de executar o seguinte exemplo:

   ```azurepowershell-interactive
   $vnet5gw = Get-AzVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
   ```

   Copie a saída dos seguintes elementos e envie-os para o administrador da Subscrição 1 por e-mail ou outro método.

   ```azurepowershell-interactive
   $vnet5gw.Name
   $vnet5gw.Id
   ```

   Estes dois elementos terão valores semelhantes ao seguinte exemplo de saída:

   ```
   PS C:\> $vnet5gw.Name
   VNet5GW
   PS C:\> $vnet5gw.Id
   /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
   ```
3. **[Assinatura 1]** Crie a ligação TestVNet1 para TestVNet5. Neste passo, vai criar a ligação da TestVNet1 à TestVNet5. A diferença aqui é que $vnet5gw não pode ser obtido diretamente porque se está numa subscrição diferente. Terá de criar um novo objeto do PowerShell com os valores comunicados da Subscrição 1 nos passos acima. Utilize o exemplo abaixo. Substitua o Nome, ID e a tecla partilhada pelos seus próprios valores. Importante: a chave partilhada tem de corresponder a ambas as ligações. A criação de uma ligação pode demorar algum tempo.

   Ligue-se à Subscrição 1 antes de executar o exemplo a seguir:

   ```azurepowershell-interactive
   $vnet5gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
   $vnet5gw.Name = "VNet5GW"
   $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
   $Connection15 = "VNet1toVNet5"
   New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
4. **[Assinatura 5]** Crie a ligação TestVNet5 para TestVNet1. Este passo é semelhante ao de cima, exceto que está a criar a ligação da TestVNet5 para a TestVNet1. Aplica-se também aqui o mesmo processo de criação de um objeto do PowerShell basedo nos valores obtidos na Subscrição 1. Neste passo, verifique se as chaves partilhadas correspondem.

   Ligue-se à Subscrição 5 antes de executar o exemplo a seguir:

   ```azurepowershell-interactive
   $vnet1gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
   $vnet1gw.Name = "VNet1GW"
   $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
   $Connection51 = "VNet5toVNet1"
   New-AzVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```

## <a name="how-to-verify-a-connection"></a><a name="verify"></a>Como verificar uma ligação

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>FAQ da ligação VNet a VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Passos seguintes

* Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Consulte a [documentação das Máquinas Virtuais](../index.yml) para obter mais informações.
* Para obter informações sobre o BGP, veja a [Descrição Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).