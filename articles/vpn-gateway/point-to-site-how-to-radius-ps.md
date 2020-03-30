---
title: 'Ligue um computador a uma rede virtual utilizando a autenticação Ponto-a-Local e RADIUS: PowerShell / Azure'
description: Ligue os clientes Windows e Mac OS X de forma segura a uma rede virtual utilizando a autenticação P2S e RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: 25bc25d9ec12804cc20baa558dce67fb3f8269a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77149203"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Configure uma ligação Ponto-a-Local a uma VNet utilizando a autenticação RADIUS: PowerShell

Este artigo mostra-lhe como criar um VNet com uma ligação Ponto-a-Local que utiliza a autenticação RADIUS. Esta configuração só está disponível para o modelo de implementação do Gestor de Recursos.

Um gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. As ligações VPN ponto-a-local são úteis quando pretende ligar-se ao seu VNet a partir de um local remoto, como quando está a telecomutar de casa ou de uma conferência. Uma VPN P2S também é uma solução útil para utilizar em vez de uma Rede de VPNs, quando são poucos os clientes que precisam de ligar a uma VNet.

Uma ligação VPN P2S é iniciada a partir dos dispositivos Windows e Mac. Os clientes que se ligam podem utilizar os seguintes métodos de autenticação: 

* Servidor RADIUS
* Autenticação de certificado nativo VPN Gateway

Este artigo ajuda-o a configurar uma configuração P2S com autenticação utilizando o servidor RADIUS. Se pretender autenticar utilizando certificados gerados e autenticação de certificado nativo de gateway VPN, consulte [configurar uma ligação Ponto-a-Local a um VNet utilizando](vpn-gateway-howto-point-to-site-rm-ps.md)a autenticação de certificado nativo de gateway VPN .

![Diagrama de ligação - RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público. O P2S cria a ligação VPN sobre o SSTP (Protocolo de Túnel de Tomada Segura), OpenVPN ou IKEv2.

* SSTP é um túnel VPN baseado em SSL que é suportado apenas nas plataformas de cliente Windows. Consegue penetrar firewalls, pelo que é uma opção ideal para ligar ao Azure a partir de qualquer lugar. No lado do servidor, suportamos as versões 1.0, 1.1 e 1.2 do SSTP. O cliente decide que versão irá utilizar. Para o Windows 8.1 e versões posteriores, o SSTP utiliza 1.2 por predefinição.

* OpenVPN® Protocol, um protocolo VPN baseado em SSL/TLS. Uma solução SSL VPN pode penetrar firewalls, uma vez que a maioria das firewalls abrem a porta TCP 443, que o SSL utiliza. O OpenVPN pode ser utilizado para ligar a partir de dispositivos Android, iOS (versões 11.0 ou superior), Windows, Linux e Mac (versões OSX 10.13 ou superior).

* VPN IKEv2, uma solução de VPN IPsec baseada em normas. A VPN IKEv2 pode ser utilizada para ligar a partir de dispositivos Mac (versões de OSX 10.11 e superiores).

As ligações de P2S requerem o seguinte:

* Um gateway de VPN RouteBased. 
* Um servidor RADIUS para lidar com a autenticação do utilizador. O servidor RADIUS pode ser implantado no local, ou no Azure VNet.
* Um pacote de configuração de cliente VPN para os dispositivos Windows que se ligará ao VNet. Um pacote de configuração de cliente VPN fornece as definições necessárias para que um cliente VPN se conecte através do P2S.

## <a name="about-active-directory-ad-domain-authentication-for-p2s-vpns"></a><a name="aboutad"></a>Sobre a autenticação de domínio de diretório ativo (AD) para VPNs P2S

A autenticação do Domínio AD permite que os utilizadores inscrevam no Azure usando as suas credenciais de domínio da organização. Requer um servidor RADIUS que se integre com o servidor AD. As organizações também podem alavancar a sua implantação radius existente.
 
O servidor RADIUS pode residir no local, ou no seu Azure VNet. Durante a autenticação, o gateway VPN funciona como um passe-through e reencaminha as mensagens de autenticação para trás e para a frente entre o servidor RADIUS e o dispositivo de ligação. É importante que o portal VPN seja capaz de chegar ao servidor RADIUS. Se o servidor RADIUS estiver localizado no local, é necessária uma ligação VPN Site-to-Site do Azure ao local no local.

Além do Ative Directory, um servidor RADIUS também pode integrar-se com outros sistemas de identidade externos. Isto abre muitas opções de autenticação para VPNs ponto-a-site, incluindo opções de MFA. Verifique a documentação do seu servidor RADIUS para obter a lista de sistemas de identidade com os que integra.

![Diagrama de ligação - RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Apenas uma ligação VPN Site-to-Site pode ser usada para ligar a um servidor RADIUS no local. Não é possível utilizar uma ligação ExpressRoute.
>
>

## <a name="before-beginning"></a><a name="before"></a>Antes de começar

Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Valores de exemplo

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
* **Subscrição:** se tiver várias subscrições, verifique se tem a correta.
* **Grupo de Recursos: TestRG**
* **Localização: E.U.A. Leste**
* **DNS Server: Endereço IP** do servidor DNS que pretende utilizar para resolução de nomes para o seu VNet. (opcional)
* **Nome do GW: Vnet1GW**
* **Nome do IP público: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="1-set-the-variables"></a><a name="signin"></a>1. Definir as variáveis

Declare as variáveis que quer utilizar. Utilize o exemplo seguinte, substituindo os valores pelos seus próprios, quando necessário. Se fechar a sua sessão PowerShell/Cloud Shell em qualquer ponto durante o exercício, basta copiar e colar os valores novamente para redeclarar as variáveis.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="2-create-the-resource-group-vnet-and-public-ip-address"></a>2. <a name="vnet"> </a>Criar o endereço de recurso, VNet e IP público

Os seguintes passos criam um grupo de recursos e uma rede virtual no grupo de recursos com três subredes. Ao substituir valores, é importante que nomeie sempre a sua subnet de gateway especificamente 'GatewaySubnet'. Se lhe nomearoutra coisa, a sua criação de gateway falha;

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

   Neste exemplo, o parâmetro de servidor -DnsServer é opcional. A especificação de um valor não cria um novo servidor DNS. O endereço IP do servidor DNS que especificar deve ser um servidor DNS que consiga resolver os nomes dos recursos a que se está a ligar a partir da sua VNet. Para este exemplo, foi utilizado um endereço IP privado, mas é provável que este não seja o endereço IP do servidor DNS. Certifique-se de que utiliza os seus próprios valores. O valor que especifica é utilizado pelos recursos que implementa para o VNet, e não pela ligação P2S.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Um gateway de VPN deve ter um endereço IP público. Primeira, requeira o recurso de endereço IP e, em seguida, faça referência ao mesmo ao criar o gateway de rede virtual. O endereço IP é dinamicamente atribuído ao recurso quando o gateway de VPN é criado. O Gateway de VPN, atualmente, apenas suporta a alocação de endereços IP públicos *dinâmicos*. Não é possível pedir uma atribuição de endereço IP Público Estático. No entanto, isto não significa que o endereço IP é alterado após ser atribuído ao gateway de VPN. O endereço IP Público só é alterado quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN.

   Especifique as variáveis para solicitar um endereço IP público dinamicamente atribuído.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="3-set-up-your-radius-server"></a>3. <a name="radius"> </a>Configurar o seu servidor RADIUS

Antes de criar e configurar o portal de rede virtual, o seu servidor RADIUS deve ser configurado corretamente para autenticação.

1. Se não tiver um servidor RADIUS implantado, implante um. Para os passos de implementação, consulte o guia de configuração fornecido pelo seu fornecedor RADIUS.  
2. Configure o gateway VPN como um cliente RADIUS no RADIUS. Ao adicionar este cliente RADIUS, especifique a rede virtual GatewaySubnet que criou. 
3. Assim que o servidor RADIUS estiver configurado, obtenha o endereço IP do servidor RADIUS e o segredo partilhado que os clientes RADIUS devem usar para falar com o servidor RADIUS. Se o servidor RADIUS estiver no Azure VNet, utilize o CA IP do VM do servidor RADIUS.

O artigo do Servidor de [Política de Rede (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) fornece orientações sobre a configuração de um servidor De raios do Windows (NPS) para autenticação de domínio AD.

## <a name="4-create-the-vpn-gateway"></a>4. <a name="creategw"> </a>Criar o gateway VPN

Configure e crie a porta de entrada VPN para o seu VNet.

* O GatewayType deve ser 'Vpn' e o -VpnType deve ser 'RouteBased'.
* Um gateway VPN pode demorar até 45 minutos a ser concluído, dependendo do [gateway SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku) que selecionar.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## <a name="5-add-the-radius-server-and-client-address-pool"></a>5. <a name="addradius"> </a>Adicione o servidor RADIUS e o conjunto de endereços do cliente
 
* O -RadiusServer pode ser especificado pelo nome ou pelo endereço IP. Se especificar o nome e o servidor residir no local, então o gateway VPN pode não ser capaz de resolver o nome. Se for esse o caso, então é melhor especificar o endereço IP do servidor. 
* O -RadiusSecret deve corresponder ao que está configurado no seu servidor RADIUS.
* O -VpnClientAddressPool é o intervalo a partir do qual os clientes VPN de ligação recebem um endereço IP.Utilize um intervalo de endereços IP privados que não se sobrepõe à localização no local onde irá estabelecer ligação ou com a VNet a que pretende ligar. Certifique-se de que tem uma piscina de endereços suficientemente grande configurada.  

1. Crie uma corda segura para o segredo radius.

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. É-lhe pedido que entre no segredo do RADIUS. Os caracteres que introduzir não serão exibidos e, em vez disso, serão substituídos pelo personagem "*".

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. Adicione o conjunto de endereços do cliente VPN e as informações do servidor RADIUS.

   Para configurações SSTP:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Para configurações ® OpenVPN:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "OpenVPN" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```


   Para configurações IKEv2:

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

## <a name="6-download-the-vpn-client-configuration-package-and-set-up-the-vpn-client"></a>6. <a name="vpnclient"> </a>Descarregue o pacote de configuração do cliente VPN e instale o cliente VPN

A configuração do cliente VPN permite que os dispositivos se conectem a um VNet sobre uma ligação P2S.Para gerar um pacote de configuração de cliente VPN e configurar o cliente VPN, consulte Criar uma Configuração de [Cliente VPN para autenticação RADIUS](point-to-site-vpn-client-configuration-radius.md).

## <a name="7-connect-to-azure"></a><a name="connect"></a>7. Ligar a Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Para ligar a partir de um cliente VPN do Windows

1. Para se ligar à sua VNet, no computador cliente, navegue até às ligações VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Introduza as suas credenciais de domínio e clique em 'Connect'. Aparece uma mensagem pop-up a pedir direitos elevados. Aceite-o e insira as credenciais.

   ![O cliente VPN liga-se ao Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. A ligação é estabelecida.

   ![Ligação estabelecida](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Conecte-se a partir de um cliente Mac VPN

Na caixa de diálogo Rede, localize o perfil de cliente que quer utilizar e, em seguida, clique em **Ligar**.

  ![Ligação do Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-your-connection"></a><a name="verify"></a>Para verificar a ligação

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

Para resolver uma ligação P2S, consulte [ligações ponto-a-local do Troubleshooting Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Ligar a uma máquina virtual

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a><a name="faq"></a>FAQ

Este FAQ aplica-se ao P2S utilizando a autenticação RADIUS

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Passos seguintes

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/). Para compreender melhor o funcionamento em rede e as máquinas virtuais, veja [Descrição geral da rede VM do Azure e Linux](../virtual-machines/linux/azure-vm-network-overview.md).
