---
title: 'Conectar um computador a uma rede virtual usando a autenticação ponto a site e RADIUS: PowerShell | Azure'
description: Conecte clientes Windows e Mac OS X com segurança a uma rede virtual usando P2S e autenticação RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: cherylmc
ms.openlocfilehash: 1f55b8963ad9f940202816704c5818c6853ffcde
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75353707"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Configurar uma conexão ponto a site com uma VNet usando a autenticação RADIUS: PowerShell

Este artigo mostra como criar uma VNet com uma conexão ponto a site que usa a autenticação RADIUS. Essa configuração só está disponível para o modelo de implantação do Gerenciador de recursos.

Um gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. As conexões VPN ponto a site são úteis quando você deseja se conectar à sua rede virtual de um local remoto, como quando você está intercomutando de casa ou de uma conferência. Uma VPN P2S também é uma solução útil para utilizar em vez de uma Rede de VPNs, quando são poucos os clientes que precisam de ligar a uma VNet.

Uma ligação VPN P2S é iniciada a partir dos dispositivos Windows e Mac. Os clientes que se ligam podem utilizar os seguintes métodos de autenticação: 

* Servidor RADIUS
* Autenticação de certificado nativo do gateway de VPN

Este artigo ajuda você a configurar uma configuração do P2S com autenticação usando o servidor RADIUS. Se você quiser autenticar usando certificados gerados e autenticação de certificado nativo de gateway de VPN em vez disso, consulte [Configurar uma conexão ponto a site com uma VNet usando a autenticação de certificado nativo do gateway de VPN](vpn-gateway-howto-point-to-site-rm-ps.md).

![Diagrama de conexão-RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público. O P2S cria a conexão VPN por meio do SSTP (protocolo de encapsulamento de soquete seguro), OpenVPN ou IKEv2.

* SSTP é um túnel VPN baseado em SSL que é suportado apenas nas plataformas de cliente Windows. Consegue penetrar firewalls, pelo que é uma opção ideal para ligar ao Azure a partir de qualquer lugar. No lado do servidor, suportamos as versões 1.0, 1.1 e 1.2 do SSTP. O cliente decide que versão irá utilizar. Para o Windows 8.1 e versões posteriores, o SSTP utiliza 1.2 por predefinição.

* Protocolo de® OpenVPN, um protocolo VPN baseado em SSL/TLS. Uma solução de VPN SSL pode penetrar em firewalls, pois a maioria dos firewalls abre a porta TCP 443 de saída, que o SSL usa. O OpenVPN pode ser usado para se conectar do Android, iOS (versões 11,0 e superiores), Windows, Linux e dispositivos Mac (OSX versões 10,13 e posteriores).

* VPN IKEv2, uma solução de VPN IPsec baseada em normas. A VPN IKEv2 pode ser utilizada para ligar a partir de dispositivos Mac (versões de OSX 10.11 e superiores).

As ligações de P2S requerem o seguinte:

* Um gateway de VPN RouteBased. 
* Um servidor RADIUS para lidar com a autenticação do usuário. O servidor RADIUS pode ser implantado localmente ou na VNet do Azure.
* Um pacote de configuração de cliente VPN para dispositivos Windows que se conectarão à VNet. Um pacote de configuração de cliente VPN fornece as configurações necessárias para que um cliente VPN se conecte por P2S.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="aboutad"></a>Sobre a autenticação de domínio do Active Directory (AD) para VPNs P2S

A autenticação de domínio do AD permite que os usuários entrem no Azure usando suas credenciais de domínio da organização. Ele requer um servidor RADIUS que se integre ao servidor do AD. As organizações também podem aproveitar sua implantação RADIUS existente.
 
O servidor RADIUS pode residir localmente ou em sua VNet do Azure. Durante a autenticação, o gateway de VPN atua como uma passagem e encaminha as mensagens de autenticação entre o servidor RADIUS e o dispositivo de conexão. É importante que o gateway de VPN possa acessar o servidor RADIUS. Se o servidor RADIUS estiver localizado no local, será necessária uma conexão VPN site a site do Azure para o site local.

Além do Active Directory, um servidor RADIUS também pode ser integrado a outros sistemas de identidade externos. Isso abre várias opções de autenticação para VPNs ponto a site, incluindo opções de MFA. Verifique a documentação do fornecedor do servidor RADIUS para obter a lista de sistemas de identidade com os quais ele se integra.

![Diagrama de conexão-RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Somente uma conexão VPN site a site pode ser usada para se conectar a um servidor RADIUS local. Uma conexão de ExpressRoute não pode ser usada.
>
>

## <a name="before"></a>Antes de começar

Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

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
* **Subscrição:** se tiver várias subscrições, verifique se tem a correta.
* **Grupo de Recursos: TestRG**
* **Localização: E.U.A. Leste**
* **Servidor DNS: endereço IP** do servidor DNS que você deseja usar para a resolução de nomes de sua VNet. (opcional)
* **Nome do GW: Vnet1GW**
* **Nome do IP público: VNet1GWPIP**
* **VpnType: RouteBased**


## <a name="signin"></a>Entrar e definir variáveis

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

### <a name="declare-variables"></a>Declarar variáveis

Declare as variáveis que quer utilizar. Utilize o exemplo seguinte, substituindo os valores pelos seus próprios, quando necessário. Se você fechar sua sessão do PowerShell/Cloud Shell a qualquer momento durante o exercício, basta copiar e colar os valores novamente para declarar as variáveis novamente.

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

## 1. <a name="vnet"> </a>criar o grupo de recursos, a VNet e o endereço IP público

As etapas a seguir criam um grupo de recursos e uma rede virtual no grupo de recursos com três sub-redes. Ao substituir valores, é importante que você sempre Nomeie sua sub-rede de gateway especificamente como ' GatewaySubnet '. Se você nomear outra coisa, a criação do gateway falhará;

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

   Neste exemplo, o parâmetro de servidor -DnsServer é opcional. A especificação de um valor não cria um novo servidor DNS. O endereço IP do servidor DNS que especificar deve ser um servidor DNS que consiga resolver os nomes dos recursos a que se está a ligar a partir da sua VNet. Para este exemplo, foi utilizado um endereço IP privado, mas é provável que este não seja o endereço IP do servidor DNS. Certifique-se de que utiliza os seus próprios valores. O valor especificado é usado pelos recursos que você implanta na VNet, não pela conexão P2S.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Um gateway de VPN deve ter um endereço IP público. Primeira, requeira o recurso de endereço IP e, em seguida, faça referência ao mesmo ao criar o gateway de rede virtual. O endereço IP é dinamicamente atribuído ao recurso quando o gateway de VPN é criado. O Gateway de VPN, atualmente, apenas suporta a alocação de endereços IP públicos *dinâmicos*. Não é possível pedir uma atribuição de endereço IP Público Estático. No entanto, isto não significa que o endereço IP é alterado após ser atribuído ao gateway de VPN. O endereço IP Público só é alterado quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN.

   Especifique as variáveis para solicitar um endereço IP público atribuído dinamicamente.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## 2. <a name="radius"> </a>configurar o servidor RADIUS

Antes de criar e configurar o gateway de rede virtual, seu servidor RADIUS deve ser configurado corretamente para autenticação.

1. Se você não tiver um servidor RADIUS implantado, implante um. Para obter as etapas de implantação, consulte o guia de instalação fornecido pelo seu fornecedor RADIUS.  
2. Configure o gateway de VPN como um cliente RADIUS no raio. Ao adicionar esse cliente RADIUS, especifique a rede virtual GatewaySubnet que você criou. 
3. Depois que o servidor RADIUS for configurado, obtenha o endereço IP do servidor RADIUS e o segredo compartilhado que os clientes RADIUS devem usar para se comunicar com o servidor RADIUS. Se o servidor RADIUS estiver na VNet do Azure, use o IP da AC da VM do servidor RADIUS.

O artigo [servidor de políticas de rede (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) fornece orientação sobre como configurar um servidor RADIUS do Windows (NPS) para autenticação de domínio do AD.

## 3. <a name="creategw"> </a>criar o gateway de VPN

Configure e crie o gateway de VPN para sua VNet.

* O-Gatewaytype deve ser ' VPN ' e-VpnType deve ser ' RouteBased '.
* Um gateway de VPN pode levar até 45 minutos para ser concluído, dependendo do [SKU de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku) você selecionar.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## 4. <a name="addradius"> </a>adicionar o servidor RADIUS e o pool de endereços do cliente
 
* O-RadiusServer pode ser especificado pelo nome ou pelo endereço IP. Se você especificar o nome e o servidor residir no local, o gateway de VPN poderá não ser capaz de resolver o nome. Se esse for o caso, é melhor especificar o endereço IP do servidor. 
* O-RadiusSecret deve corresponder ao que está configurado no seu servidor RADIUS.
* O-VpnClientAddressPool é o intervalo do qual os clientes VPN de conexão recebem um endereço IP. Use um intervalo de endereços IP privado que não se sobreponha ao local com o qual você se conectará ou com a VNet à qual você deseja se conectar. Verifique se você tem um pool de endereços grande o suficiente configurado.  

1. Crie uma cadeia de caracteres segura para o segredo RADIUS.

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. Você será solicitado a inserir o segredo RADIUS. Os caracteres inseridos não serão exibidos e, em vez disso, serão substituídos pelo caractere "*".

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. Adicione o pool de endereços do cliente VPN e as informações do servidor RADIUS.

   Para configurações de SSTP:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Para configurações de® de OpenVPN:

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

## 5. <a name="vpnclient"> </a>baixar o pacote de configuração do cliente VPN e configurar o cliente VPN

A configuração de cliente VPN permite que os dispositivos se conectem a uma VNet por meio de uma conexão P2S. Para gerar um pacote de configuração de cliente VPN e configurar o cliente VPN, consulte [criar uma configuração de cliente VPN para autenticação RADIUS](point-to-site-vpn-client-configuration-radius.md).

## <a name="connect"></a>6. conectar-se ao Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Para ligar a partir de um cliente VPN do Windows

1. Para se ligar à sua VNet, no computador cliente, navegue até às ligações VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Insira suas credenciais de domínio e clique em ' conectar '. Uma mensagem pop-up solicitando direitos elevados é exibida. Aceite-o e insira as credenciais.

   ![O cliente VPN liga-se ao Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. A ligação é estabelecida.

   ![Ligação estabelecida](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Conectar-se de um cliente Mac VPN

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

Para solucionar problemas de conexão do P2S, consulte [Solucionando problemas de conexões ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="connectVM"></a>Ligar a uma máquina virtual

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a>FAQ

Estas perguntas frequentes se aplicam ao P2S usando a autenticação RADIUS

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Passos seguintes

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/). Para compreender melhor o funcionamento em rede e as máquinas virtuais, veja [Descrição geral da rede VM do Azure e Linux](../virtual-machines/linux/azure-vm-network-overview.md).
