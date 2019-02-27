---
title: 'Ligar um computador a uma rede virtual com o ponto a Site e autenticação RADIUS: PowerShell | Azure'
description: Ligue os clientes Windows e Mac OS X com segurança a uma rede virtual através da autenticação P2S e RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: cherylmc
ms.openlocfilehash: 0aea797458649af3f839b7b8ae9b5cea384fe3b0
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56865149"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Configure uma ligação de ponto a Site a uma VNet com a autenticação RADIUS: PowerShell

Este artigo mostra-lhe como criar uma VNet com uma ligação de ponto a Site que utiliza a autenticação RADIUS. Esta configuração só está disponível para o modelo de implementação do Resource Manager.

Um gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. Ligações de VPN ponto a Site são úteis quando pretende ligar a VNet a partir de uma localização remota, por exemplo, quando teletrabalho em casa ou numa conferência. Uma VPN P2S também é uma solução útil para utilizar em vez de uma Rede de VPNs, quando são poucos os clientes que precisam de ligar a uma VNet.

Uma ligação VPN P2S é iniciada a partir dos dispositivos Windows e Mac. Os clientes que se ligam podem utilizar os seguintes métodos de autenticação:

* Servidor RADIUS
* Autenticação de certificados nativa do Gateway de VPN

Este artigo ajuda-o a configurar uma configuração P2S com autenticação com o servidor RADIUS. Se pretender efetuar a autenticação com certificados gerados e autenticação de certificados nativa do gateway VPN em vez disso, consulte [configurar uma ligação ponto a Site a uma VNet com a autenticação de certificados nativa do gateway VPN](vpn-gateway-howto-point-to-site-rm-ps.md).

![Diagrama da ligação - RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público. A P2S cria a ligação VPN através de SSTP (Secure Socket Tunneling Protocol) ou de IKEv2.

* SSTP é um túnel VPN baseado em SSL que é suportado apenas nas plataformas de cliente Windows. Consegue penetrar firewalls, pelo que é uma opção ideal para ligar ao Azure a partir de qualquer lugar. No lado do servidor, suportamos as versões 1.0, 1.1 e 1.2 do SSTP. O cliente decide que versão irá utilizar. Para o Windows 8.1 e versões posteriores, o SSTP utiliza 1.2 por predefinição.

* VPN IKEv2, uma solução de VPN IPsec baseada em normas. A VPN IKEv2 pode ser utilizada para ligar a partir de dispositivos Mac (versões de OSX 10.11 e superiores).

As ligações de P2S requerem o seguinte:

* Um gateway de VPN RouteBased. 
* Um servidor RADIUS para processar a autenticação de utilizador. O servidor RADIUS pode ser implementado no local, ou na VNet do Azure.
* Pacote de configuração de cliente VPN para os dispositivos do Windows que irão ligar à VNet. Um pacote de configuração de cliente VPN fornece as definições necessárias para um cliente VPN para se ligar através do P2S.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="aboutad"></a>Sobre a autenticação de domínio do Active Directory (AD) para as P2S VPNs

Autenticação de domínio do AD permite aos utilizadores iniciar sessão no Azure através das respetivas credenciais de domínio da organização. Ele requer um servidor RADIUS que se integra com o servidor do AD. As organizações também podem aproveitar a implantação de RADIUS existente.
 
O servidor RADIUS pode residir no local, ou na sua VNet do Azure. Durante a autenticação, o gateway de VPN atua como um pass-through e encaminha mensagens de autenticação e volta entre o servidor RADIUS e o dispositivo de ligação. É importante para o gateway VPN conseguir alcançar o servidor RADIUS. Se o servidor RADIUS estiver localizada no local, é necessária uma ligação de VPN Site a Site do Azure para o site no local.

Para além dos Active Directory, um servidor RADIUS, também pode integrar com outros sistemas de identidade externo. Isso abre muitas opções de autenticação para as VPNs ponto a Site, incluindo opções de MFA. Verifique a documentação do fornecedor do servidor RADIUS para obter a lista de sistemas de identidade integra-se.

![Diagrama da ligação - RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Apenas uma ligação de VPN Site a Site pode ser utilizada para ligar a um servidor RADIUS no local. Não é possível utilizar uma ligação do ExpressRoute.
>
>

## <a name="before"></a>Antes de começar

Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="signin"></a>Iniciar sessão

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

### <a name="example"></a>Valores de exemplo

Pode utilizar os valores de exemplo para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo. Pode utilizar os passos como orientação e utilizar os valore sem os alterar ou alterá-los de modo a refletir o seu ambiente.

* **Nome: VNet1**
* **Espaço de endereços: 192.168.0.0/16** e **10.254.0.0/16**<br>Para este exemplo, utilizamos mais do que um espaço de endereço para ilustrar que esta configuração funciona com vários espaços de endereço. No entanto, vários espaços de endereços não são precisos para esta configuração.
* **Nome da sub-rede: FrontEnd**
  * **Intervalo de endereços da sub-rede: 192.168.1.0/24**
* **Nome da sub-rede: BackEnd**
  * **Intervalo de endereços da sub-rede: 10.254.1.0/24**
* **Nome da sub-rede: GatewaySubnet**<br>O nome da Sub-rede *GatewaySubnet* é obrigatório para o gateway de VPN funcionar.
  * **Intervalo de endereços do GatewaySubnet: 192.168.200.0/24** 
* **Conjunto de endereços de cliente VPN: 172.16.201.0/24**<br>Os clientes VPN que se ligam à VNet através desta ligação Ponto a Site recebem um endereço IP a partir do conjunto de endereços de cliente VPN especificado.
* **Subscrição:** Se tiver mais de uma subscrição, certifique-se de que está a utilizar correta.
* **Grupo de recursos: TestRG**
* **Localização: E.U.A. leste**
* **Servidor DNS: Endereço IP** do servidor DNS que pretende utilizar para a resolução de nome para a sua VNet. (opcional)
* **Nome do GW: Vnet1GW**
* **Nome do IP público: VNet1GWPIP**
* **VpnType: RouteBased**

## 1. <a name="vnet"></a>Criar o grupo de recursos, a VNet e o IP público de endereço

Os passos seguintes criam um grupo de recursos e uma rede virtual no grupo de recursos com três sub-redes. Quando estiver a substituir valores, é importante que sempre atribua um nome à sub-rede do gateway especificamente "GatewaySubnet". Se lhe der outro nome, a criação da gateway falha;

1. Crie um grupo de recursos.

  ```azurepowershell-interactive
  New-AzResourceGroup -Name "TestRG" -Location "East US"
  ```
2. Crie as configurações de sub-rede para a rede virtual, nomeando-as *FrontEnd*, *BackEnd* e *GatewaySubnet*. Estes prefixos têm de fazer parte do espaço de endereços da VNet que declarou.

  ```azurepowershell-interactive
  $fesub = New-AzVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
  $besub = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
  $gwsub = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
  ```
3. Criar a rede virtual.

  Neste exemplo, o parâmetro de servidor -DnsServer é opcional. A especificação de um valor não cria um novo servidor DNS. O endereço IP do servidor DNS que especificar deve ser um servidor DNS que consiga resolver os nomes dos recursos a que se está a ligar a partir da sua VNet. Para este exemplo, foi utilizado um endereço IP privado, mas é provável que este não seja o endereço IP do servidor DNS. Certifique-se de que utiliza os seus próprios valores. O valor que especificar é utilizado pelos recursos que implementa na VNet, não pela ligação P2S.

  ```azurepowershell-interactive
  New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
  ```
4. Um gateway de VPN deve ter um endereço IP público. Primeira, requeira o recurso de endereço IP e, em seguida, faça referência ao mesmo ao criar o gateway de rede virtual. O endereço IP é dinamicamente atribuído ao recurso quando o gateway de VPN é criado. O Gateway de VPN, atualmente, apenas suporta a alocação de endereços IP públicos *dinâmicos*. Não é possível pedir uma atribuição de endereço IP Público Estático. No entanto, isto não significa que o endereço IP é alterado após ser atribuído ao gateway de VPN. O endereço IP Público só é alterado quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN.

  Especifique as variáveis para pedir um endereço IP público atribuído de forma dinâmica.

  ```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
  $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
  $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
  ```

## 2. <a name="radius"></a>Configurar o servidor RADIUS

Antes de criar e configurar o gateway de rede virtual, o servidor RADIUS deve ser configurado corretamente para autenticação.

1. Se não tiver um servidor RADIUS implementado, implemente uma. Para obter os passos de implementação, consulte o guia de configuração fornecido pelo fornecedor de RADIUS.  
2. Configure o gateway VPN como um cliente RADIUS no RADIUS. Ao adicionar este cliente RADIUS, especifique a rede virtual GatewaySubnet que criou. 
3. Assim que o servidor RADIUS estiver configurado, obtenha o endereço IP do servidor RADIUS e o segredo partilhado que os clientes RADIUS devem utilizar para comunicar com o servidor RADIUS. Se o servidor RADIUS estiver na VNet do Azure, utilize o IP de AC do servidor RADIUS de VM.

O [servidor de políticas de rede (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) artigo fornece orientações sobre como configurar um servidor de RADIUS do Windows (NPS) para autenticação de domínio do AD.

## 3. <a name="creategw"></a>Criar o gateway VPN

Configurar e criar o gateway de VPN para a sua VNet.

* O - GatewayType tem de ser "Vpn" e o - VpnType tem de ser 'RouteBased'.
* Um gateway VPN pode demorar até 45 minutos a concluir, dependendo do [SKU de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku) que selecionar.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## 4. <a name="addradius"></a>Adicionar o conjunto de endereços de servidor e cliente RADIUS
 
* -RadiusServer podem ser especificado por nome ou por endereço IP. Se especificar o nome e o servidor reside no local, em seguida, o gateway de VPN pode não conseguir resolver o nome. Se for esse o caso, em seguida, é melhor especificar o endereço IP do servidor. 
* -RadiusSecret deve corresponder ao que está configurado no seu servidor RADIUS.
* -VpnClientAddressPool é o intervalo a partir do qual os clientes VPN ligados recebem um endereço IP. Utilize um intervalo de endereços IP privados que não se sobrepõe à localização no local onde irá estabelecer ligação ou com a VNet a que pretende ligar. Certifique-se de que tem um conjunto de endereços suficientemente grande configurado.  

1. Crie uma cadeia segura para o RADIUS secreta.

  ```azurepowershell-interactive
  $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
  ```

2. São-lhe pedido que introduza o segredo RADIUS. Os carateres que introduziu não serão apresentados e em vez disso, serão substituídos pela "*" caráter.

  ```azurepowershell-interactive
  RadiusSecret:***
  ```
3. Adicione o conjunto de endereços de cliente VPN e as informações do servidor RADIUS.

  Para configurações de SSTP:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  Para configurações de IKEv2:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  Para SSTP + IKEv2

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## 5. <a name="vpnclient"></a>Transferir o pacote de configuração de cliente VPN e configurar o cliente VPN

A configuração do cliente VPN permite que os dispositivos, ligar a uma VNet através de uma ligação de P2S. Para gerar um pacote de configuração de cliente VPN e configurar o cliente VPN, veja [criar uma configuração de cliente de VPN para autenticação RADIUS](point-to-site-vpn-client-configuration-radius.md).

## <a name="connect"></a>6. Ligar ao Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Para ligar a partir de um cliente VPN do Windows

1. Para se ligar à sua VNet, no computador cliente, navegue até às ligações VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Introduza as credenciais de domínio e clique em "Ligar". É apresentada uma mensagem de pop-up solicitando direitos elevados. Aceitá-lo e introduza as credenciais.

  ![O cliente VPN liga-se ao Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. A ligação é estabelecida.

  ![Ligação estabelecida](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Ligar a partir de um cliente de VPN do Mac

Na caixa de diálogo Rede, localize o perfil de cliente que quer utilizar e, em seguida, clique em **Ligar**.

  ![Ligação do Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>Para verificar a ligação

1. Para verificar se a ligação VPN está ativa, abra uma linha de comandos elevada e execute *ipconfig/all*.
2. Veja os resultados. Repare que o endereço IP que recebeu é um dos endereços dentro do Conjunto de Endereços de Cliente de VPN de Ponto a Site que especificou na configuração. Os resultados são semelhantes a este exemplo:

  ```
  PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
  ```

Para resolver problemas de uma ligação de P2S, consulte o artigo [ligações de ponto a site de resolução de problemas do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="connectVM"></a>Ligar a uma máquina virtual

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a>FAQ

Estas FAQ aplicam-se para P2S através da autenticação RADIUS

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Passos Seguintes

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/). Para compreender melhor o funcionamento em rede e as máquinas virtuais, veja [Descrição geral da rede VM do Azure e Linux](../virtual-machines/linux/azure-vm-network-overview.md).
