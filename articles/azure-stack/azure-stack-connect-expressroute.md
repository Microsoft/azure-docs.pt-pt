---
title: Ligar o Azure Stack ao Azure através do ExpressRoute
description: Saiba como ligar redes virtuais no Azure Stack para redes virtuais no Azure através do ExpressRoute.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 0ebd17eca363d7fc02daeb851bb24b8d1d307efc
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339606"
---
# <a name="connect-azure-stack-to-azure-using-azure-expressroute"></a>Ligar o Azure Stack ao Azure através do ExpressRoute do Azure

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Este artigo descreve como ligar uma rede virtual do Azure Stack para uma rede virtual do Azure com um [Microsoft Azure ExpressRoute](/azure/expressroute/) ligação direta.

Pode utilizar este artigo como um tutorial e utilizar os exemplos para configurar o mesmo ambiente de teste. Em alternativa, pode utilizar o artigo como um passo a passo que o orienta pela configuração de seu próprio ambiente do ExpressRoute.

## <a name="overview-assumptions-and-prerequisites"></a>Descrição geral, suposições e pré-requisitos

O Azure ExpressRoute permite-lhe expandir as redes no local para a cloud da Microsoft através de uma ligação privada fornecida por um fornecedor de conectividade. O ExpressRoute não é uma ligação VPN na Internet pública.

Para obter mais informações sobre o Azure ExpressRoute, consulte a [descrição geral do ExpressRoute](../expressroute/expressroute-introduction.md).

### <a name="assumptions"></a>Suposições

Este artigo pressupõe que:

* Tem um conhecimento prático do Azure.
* Tem uma compreensão básica do Azure Stack.
* Tem uma compreensão básica do funcionamento em rede.

### <a name="prerequisites"></a>Pré-requisitos

Para ligar o Azure Stack e o Azure através do ExpressRoute, tem de cumprir os seguintes requisitos:

* Um aprovisionado [circuito do ExpressRoute](../expressroute/expressroute-circuit-peerings.md) através de um [fornecedor de conectividade](../expressroute/expressroute-locations.md).
* Uma subscrição do Azure para criar um circuito do ExpressRoute e as VNets no Azure.
* Um router que tem:
  * Suportam ligações de VPN de Site a Site entre sua interface de LAN e o Gateway de multi-inquilino do Azure Stack.
  * Suporta a criação de vários VRFs (encaminhamento Virtual e encaminhamento) se existir mais do que um inquilino na sua implementação do Azure Stack.
* Um router com:
  * Uma porta WAN ligadas ao circuito do ExpressRoute.
  * Uma porta LAN ligado ao Gateway de multi-inquilino do Azure Stack.

### <a name="expressroute-network-architecture"></a>Arquitetura de rede do ExpressRoute

O diagrama seguinte mostra o Azure Stack e ambientes do Azure depois de concluir a configuração do ExpressRoute com os exemplos neste artigo:

*Figura 1. Rede do ExpressRoute*

![Rede do ExpressRoute](media/azure-stack-connect-expressroute/Conceptual.png)

O diagrama seguinte mostra como vários inquilinos ligar da infraestrutura do Azure Stack através do roteador de ExpressRoute para o Azure na borda da Microsoft:

*Figura 2. Ligações de multi-inquilinos*

![Ligações de multi-inquilinos com o ExpressRoute](media/azure-stack-connect-expressroute/Architecture.png)

O exemplo neste artigo utiliza a mesma arquitetura de multi-inquilino mostrada na figura 2 para ligar o Azure Stack para o Azure com o peering privado do ExpressRoute. A conexão é feita usando uma conexão de VPN de site a site do gateway de rede virtual no Azure Stack para um roteador de ExpressRoute.

Os passos neste artigo mostram como criar uma ligação ponto a ponto entre duas VNets, de dois inquilinos diferentes no Azure Stack para VNets correspondentes no Azure. Configurar dois inquilinos é opcional; Também pode utilizar estes passos para um único inquilino.

## <a name="configure-azure-stack"></a>Configurar o Azure Stack

Para configurar o ambiente do Azure Stack para o inquilino primeiro, utilize os passos no diagrama seguinte como guia. Se estiver a configurar mais do que um inquilino, repita estes passos:

>[!NOTE]
>Estes passos explicam como criar recursos com o portal do Azure Stack, mas também pode utilizar o PowerShell.

![Configuração de rede do Azure Stack](media/azure-stack-connect-expressroute/image2.png)

### <a name="before-you-begin"></a>Antes de começar

Antes de começar a configurar o Azure Stack, terá de:

* Uma implementação do sistema integrado do Azure Stack ou uma implementação do Azure Stack Development Kit (ASDK). Para obter informações sobre como implementar o ASDK, consulte a [início rápido de implementação do Development Kit do Azure Stack](azure-stack-deploy-overview.md).
* Uma oferta no Azure Stack que os utilizadores podem subscrever. Para obter mais informações, consulte [planos, ofertas e subscrições](azure-stack-plan-offer-quota-overview.md).

### <a name="create-network-resources-in-azure-stack"></a>Criar recursos de rede no Azure Stack

Utilize os procedimentos seguintes para criar os recursos de rede necessária no Azure Stack para um inquilino.

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e a sub-rede VM

1. Inicie sessão no portal de utilizador com uma conta de utilizador (inquilino).

2. No portal, selecione **+ criar um recurso**.

3. Sob **do Azure Marketplace**, selecione **Networking**.

4. Sob **em destaque**, selecione **rede Virtual**.

5. Sob **criar rede virtual**, introduza os valores mostrados na tabela a seguir nos campos apropriados:

   |Campo  |Valor  |
   |---------|---------|
   |Name     |Tenant1VNet1         |
   |Espaço de endereços     |10.1.0.0/16|
   |Nome da sub-rede     |Tenant1-Sub1|
   |Intervalo de endereços da sub-rede     |10.1.1.0/24|

6. Deverá ver a subscrição que criou anteriormente preenchida no **subscrição** campo. Para os campos restantes:

    * Sob **grupo de recursos**, selecione **criar nova** para criar um novo grupo de recursos ou se já tiver um, selecione **utilizar existente**.
    * Certifique-se a predefinição **localização**.
    * Clique em **Criar**.
    * (Opcional) Clique em **afixar ao dashboard**.

#### <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway

1. Sob **rede Virtual**, selecione **Tenant1VNet1**.
1. Em **DEFINIÇÕES**, selecione **sub-redes**.
1. Selecione **+ sub-rede de Gateway** para adicionar uma sub-rede de gateway para a rede virtual.
1. O nome da sub-rede está predefinido como **GatewaySubnet**. As sub-redes de gateway são um caso especial e tem de utilizar este nome para funcionar corretamente.
1. Certifique-se de que o **intervalo de endereços** é **10.1.0.0/24**.
1. Clique em **OK** para criar a sub-rede do gateway.

#### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

1. No portal de utilizador do Azure Stack, clique em **+ criar um recurso**.
1. Sob **do Azure Marketplace**, selecione **Networking**.
1. Selecione **Gateway de rede virtual** na lista de recursos de rede.
1. Na **Name** , insira **GW1**.
1. Selecione **Rede virtual**.
1. Selecione **Tenant1VNet1** na lista pendente.
1. Selecione **endereço IP público**, em seguida, **escolher endereço IP público**e, em seguida, clique em **criar novo**.
1. Na **Name** , digite **GW1-PiP**e, em seguida, clique em **OK**.
1. O **Tipo de VPN** deve ter a opção **Baseado na rota** selecionada por predefinição. Mantenha esta definição.
1. Certifique-se de que a **Subscrição** e a **Localização** estão corretas. Clique em **Criar**.

#### <a name="create-the-local-network-gateway"></a>Criar o gateway de rede local

O recurso de gateway de rede local identifica o gateway remoto na outra extremidade da ligação VPN. Neste exemplo, o final da ligação remota é a interface de frações de LAN do roteador ExpressRoute. Para o inquilino 1, mostrado na figura 2, o endereço remoto é 10.60.3.255.

1. Inicie sessão portal de utilizador do Azure Stack com a sua conta de utilizador e selecione **+ criar um recurso**.
1. Sob **do Azure Marketplace**, selecione **Networking**.
1. Selecione **Gateway de rede local** na lista de recursos.
1. Na **Name** , digite **ER-Router-GW**.
1. Para o **endereço IP** campo, veja a figura 2. O endereço IP do ExpressRoute router interface de LAN secundárias para o inquilino 1 é 10.60.3.255. Para o seu próprio ambiente, introduza o endereço IP da interface correspondente do seu router.
1. Na **espaço de endereços** , insira o espaço de endereços das VNets a que pretende ligar no Azure. As sub-redes para o inquilino 1 na *figura 2* são os seguintes:

   * 192.168.2.0/24 é o hub VNet no Azure.
   * 10.100.0.0/16 é o spoke VNet no Azure.

   > [!IMPORTANT]
   > Este exemplo assume que está a utilizar rotas estáticas para a ligação de VPN de Site a Site entre o gateway do Azure Stack e o router do ExpressRoute.

1. Certifique-se de que sua **subscrição**, **grupo de recursos**, e **localização** estão corretos. Em seguida, selecione **Criar**.

#### <a name="create-the-connection"></a>Criar a ligação

1. No portal de utilizador do Azure Stack, selecione **+ criar um recurso**.
1. Sob **do Azure Marketplace**, selecione **Networking**.
1. Selecione **Ligação** na lista de recursos.
1. Sob **Noções básicas**, escolha **Site-site (IPSec)** como o **tipo de ligação**.
1. Selecione o **subscrição**, **grupo de recursos**, e **localização**. Clique em **OK**.
1. Sob **configurações**, selecione **gateway de rede Virtual**e, em seguida, selecione **GW1**.
1. Selecione **gateway de rede Local**e, em seguida, selecione **ER Router GW**.
1. Na **nome da ligação** , insira **ConnectToAzure**.
1. Na **chave partilhada (PSK)** , insira **abc123** e, em seguida, selecione **OK**.
1. Sob **resumo**, selecione **OK**.

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>Obtenha o endereço IP da público de gateway rede virtual

Depois de criar o gateway de rede virtual, pode obter o endereço IP público do gateway. Tome nota deste endereço no caso de precisa dele mais tarde para a sua implementação. Dependendo da sua implementação, este endereço é utilizado como o **endereço IP interno**.

1. No portal de utilizador do Azure Stack, selecione **todos os recursos**.
1. Sob **todos os recursos**, selecione o gateway de rede virtual, o que é **GW1** no exemplo.
1. Sob **gateway de rede Virtual**, selecione **descrição geral** na lista de recursos. Em alternativa, pode selecionar **propriedades**.
1. O endereço IP que pretende ter em conta que está listado em **endereço IP público**. Para a configuração de exemplo, este endereço é 192.68.102.1.

#### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para testar o tráfego de dados através da ligação VPN, terá de máquinas virtuais para enviar e receber dados na VNet do Azure Stack. Criar uma máquina virtual e implementá-la para a sub-rede VM para a rede virtual.

1. No portal de utilizador do Azure Stack, selecione **+ criar um recurso**.
1. Sob **do Azure Marketplace**, selecione **computação**.
1. Na lista de imagens de máquinas virtuais, selecione o **Windows Server 2016 Datacenter Eval** imagem.

   >[!NOTE]
   >Se a imagem utilizada para este artigo não estiver disponível, peça ao seu operador do Azure Stack para fornecer uma imagem diferente do Windows Server.

1. Na **criar máquina virtual**, selecione **Noções básicas**, em seguida, escreva **VM01** como o **nome**.
1. Introduza um nome de utilizador válido e palavra-passe. Irá utilizar esta conta para iniciar sessão na VM depois de este ter sido criado.
1. Fornecer um **subscrição**, **grupo de recursos**e um **localização**. Selecione **OK**.
1. Sob **escolher um tamanho**, selecione um tamanho de máquina virtual para esta instância e, em seguida, selecione **selecione**.
1. Sob **definições**, confirme que:

   * A rede virtual é **Tenant1VNet1**.
   * A sub-rede está definida como **10.1.1.0/24**.

   Utilizar as predefinições e clique em **OK**.

1. Sob **resumo**, reveja a configuração da VM e, em seguida, clique em **OK**.

Para adicionar mais inquilinos, repita os passos que seguiu nestas secções:

* [Criar a rede virtual e sub-rede VM](#create-the-virtual-network-and-vm-subnet)
* [Criar a sub-rede do gateway](#create-the-gateway-subnet)
* [Criar o gateway de rede virtual](#create-the-virtual-network-gateway)
* [Criar o gateway de rede local](#create-the-local-network-gateway)
* [Criar a ligação](#create-the-connection)
* [Criar uma máquina virtual](#create-a-virtual-machine)

Se estiver a utilizar o inquilino 2 como exemplo, não se esqueça de alterar os endereços IP para evitar sobreposições.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>Configurar a máquina virtual NAT para o gateway transversal

> [!IMPORTANT]
> Esta secção destina-se apenas a implementações do Azure Stack Development Kit (ASDK). O NAT não é necessário para implementações de vários nós.

O Kit de desenvolvimento do Azure Stack é independente e isolada da rede em que o anfitrião físico é implementado. A rede VIP que os gateways estão ligados a não seja externa; está escondido atrás de um roteador de efetuar a tradução de endereços de rede (NAT).

O router é uma máquina de virtual do Windows Server (AzS-BGPNAT01) a executar a função de encaminhamento e serviços de acesso remoto (RRAS). É necessário configurar a NAT na máquina virtual AzS-BGPNAT01 para ativar a ligação de VPN de site a site ligar em ambas as extremidades.

#### <a name="configure-the-nat"></a>Configurar o NAT

1. Inicie sessão no computador anfitrião do Azure Stack com a sua conta de administrador.
1. Copie e edite o seguinte script do PowerShell. Substitua `your administrator password` com a sua palavra-passe de administrador e, em seguida, execute o script num ISE do PowerShell elevada. Esse script retorna seu **endereço externo BGPNAT**.

   ```PowerShell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "your administrator password" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```

1. Para configurar o NAT, copie e edite o seguinte script do PowerShell. Edite o script para substituir a `External BGPNAT address` e `Internal IP address` com os seguintes valores de exemplo:

   * Para *endereço externo BGPNAT* utilizar 10.10.0.62
   * Para *endereço IP interno* utilizar 192.168.102.1

   Execute o seguinte script de um ISE do PowerShell elevada:

   ```PowerShell
   $ExtBgpNat = 'External BGPNAT address'
   $IntBgpNat = 'Internal IP address'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501}
   # Create a static NAT mapping to map the external address to the Gateway public IP address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Configure NAT traversal which uses port 4500 to  establish the complete IPSEC tunnel over NAT devices.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Configurar o Azure

Depois de concluir a configuração do Azure Stack, pode implementar os recursos do Azure. A figura a seguir mostra um exemplo de uma rede virtual do inquilino no Azure. Pode utilizar qualquer nome e o esquema de endereçamento para a sua VNet no Azure. No entanto, o intervalo de endereços das VNets no Azure e o Azure Stack, tem de ser exclusivo e não pode sobrepor:

*Figura 3. Azure VNets*

![VNets do Azure](media/azure-stack-connect-expressroute/AzureArchitecture.png)

Os recursos a que implementar no Azure são semelhantes para os recursos implementados no Azure Stack. Implementar os seguintes componentes:

* Redes virtuais e sub-redes
* Uma sub-rede de gateway
* Um gateway de rede virtual
* Uma ligação
* Um circuito do ExpressRoute

A infraestrutura de rede do Azure de exemplo está configurada da seguinte forma:

* Um modelo de VNet spoke (10.100.0.0./16) e standard de hub (192.168.2.0/24). Para obter mais informações sobre a topologia de rede hub-and-spoke, veja [implementar uma topologia de rede hub-and-spoke no Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
* As cargas de trabalho são implementadas numa spoke VNet e o circuito do ExpressRoute está ligado à VNet do hub.
* As duas VNets estão ligadas a utilização de VNet peering.

### <a name="configure-the-azure-vnets"></a>Configurar as VNets do Azure

1. Inicie sessão no portal do Azure com as credenciais do Azure.
1. Crie a VNet com o intervalo de endereços 192.168.2.0/24 do hub.
1. Criar uma sub-rede com o intervalo de endereços 192.168.2.0/25 e adicionar uma sub-rede de gateway com o intervalo de endereços 192.168.2.128/27.
1. Criar o spoke intervalo de endereços da VNet e sub-rede com o 10.100.0.0/16.

Para obter mais informações sobre como criar redes virtuais no Azure, consulte [criar uma rede virtual](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>Configurar um circuito do ExpressRoute

1. Reveja os pré-requisitos ExpressRoute [ExpressRoute pré-requisitos e lista de verificação](../expressroute/expressroute-prerequisites.md).

1. Siga os passos em [criar e modificar um circuito do ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) para criar um circuito ExpressRoute com a sua subscrição do Azure.

   >[!NOTE]
   >Permitir que a chave de serviço para o seu circuito ao seu serviço, de modo que podem configurar o seu circuito do ExpressRoute em seu final.

1. Siga os passos em [criar e modificar um peering para um circuito ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md) para configurar o peering privado no circuito do ExpressRoute.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

Siga os passos em [configurar um gateway de rede virtual para o ExpressRoute com o PowerShell](../expressroute/expressroute-howto-add-gateway-resource-manager.md) para criar um gateway de rede virtual para o ExpressRoute na VNet do hub.

### <a name="create-the-connection"></a>Criar a ligação

Para ligar o circuito do ExpressRoute a VNet do hub, siga os passos em [ligar uma rede virtual a um circuito do ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

### <a name="peer-the-vnets"></a>Configurar o peering entre as VNets

Configurar o peering entre o hub- and -spoke VNets utilizando os passos em [criar um peering de rede virtual com o portal do Azure](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md). Ao configurar o peering de VNet, certifique-se de que utilizar as seguintes opções:

* Do hub para o spoke **permitir que o trânsito de gateway**.
* Do spoke para o hub **gateway remoto de utilização**.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Implemente as máquinas de virtuais de carga de trabalho para o VNet spoke.

Repita estes passos para qualquer inquilino adicional VNets que pretende ligar-se no Azure através de seus respectivos circuitos do ExpressRoute.

## <a name="configure-the-router"></a>Configurar o router

Pode utilizar o seguinte diagrama de configuração do router do ExpressRoute como um guia para configurar o Router do ExpressRoute. Este diagrama mostra dois inquilinos (inquilino 1 e 2 do inquilino), com seus respectivos circuitos do ExpressRoute. Cada inquilino está ligado a seus próprios VRF (encaminhamento Virtual e encaminhamento) do lado de LAN e WAN do router do ExpressRoute. Esta configuração garante que o isolamento de ponto a ponto entre os dois inquilinos. Tome nota dos endereços IP utilizados nas interfaces de router à medida que seguir o exemplo de configuração.

*Figura 4. Configuração de router do ExpressRoute*

![Configuração de router do ExpressRoute](media/azure-stack-connect-expressroute/EndToEnd.png)

Pode usar qualquer roteador que suporte a IKEv2 VPN e BGP para terminar a ligação de VPN de site a site do Azure Stack. O router do mesmo é utilizado para ligar ao Azure através de um circuito do ExpressRoute.

O seguinte exemplo de configuração de Router de serviços de agregação de série de 1000 de ASR do Cisco suporta a infraestrutura de rede mostrada a *configuração do roteador ExpressRoute* diagrama.

```shell
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2
description IKEv2 proposal for Tenant 1
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2
description IKEv2 proposal for Tenant 2
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2
description IKEv2 Policy for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>Testar a ligação

Teste a ligação depois de estabelecer a ligação de site a site e o circuito do ExpressRoute.

Execute os seguintes testes de ping:

* Inicie sessão para uma das máquinas virtuais na VNet do Azure e enviar um ping a máquina virtual criada no Azure Stack.
* Inicie sessão para uma das máquinas virtuais que criou no Azure Stack e ping a máquina virtual que criou na VNet do Azure.

>[!NOTE]
>Para certificar-se de que está a enviar o tráfego através do Site a Site e as ligações do ExpressRoute, deve enviar pings para o endereço IP (DIP) dedicado da máquina virtual em ambas as extremidades e não o endereço VIP da máquina virtual.

### <a name="allow-icmp-in-through-the-firewall"></a>Permitir o protocolo ICMP em através da firewall

Por predefinição, o Windows Server 2016 não permite a pacotes de entrada ICMP através da firewall. Para cada máquina virtual que utiliza para testes de ping, tem de permitir pacotes de ICMP de entrada. Para criar uma regra de firewall para o ICMP, execute o seguinte cmdlet numa janela elevada do PowerShell:

```PowerShell
# Create ICMP firewall rule.
New-NetFirewallRule `
  –DisplayName “Allow ICMPv4-In” `
  –Protocol ICMPv4
```

### <a name="ping-the-azure-stack-virtual-machine"></a>Enviar um ping a máquina virtual do Azure Stack

1. Inicie sessão no portal de utilizador do Azure Stack através de uma conta de inquilino.

1. Localizar a máquina virtual que criou e selecione a máquina virtual.

1. Selecione **Ligar**.

1. A partir de uma linha de comando Windows ou do PowerShell elevada, introduzir **ipconfig/all**. Tenha em atenção o endereço IPv4 devolvido no resultado.

1. Enviar pings para o endereço IPv4 da máquina virtual na VNet do Azure.

   No ambiente de exemplo, o endereço IPv4 é da sub-rede 10.1.1.x/24. No seu ambiente, o endereço poderá ser diferente, mas deve ser a sub-rede que criou para o inquilino sub-rede da VNet.

### <a name="view-data-transfer-statistics"></a>Ver estatísticas de transferência de dados

Se deseja saber quanto tráfego passa por meio de sua conexão, pode encontrar estas informações no portal de utilizador do Azure Stack. Também é uma boa maneira de saber se é ou não os seus dados de teste de ping passou pelas ligações ExpressRoute e VPN:

1. Inicie sessão portal de utilizador do Azure Stack com a sua conta de inquilino e selecione **todos os recursos**.
1. Navegue para o grupo de recursos para o seu Gateway de VPN e selecione o **ligação** tipo de objeto.
1. Selecione o **ConnectToAzure** ligação na lista.
1. Sob **conexões** > **descrição geral**, pode ver as estatísticas de **dados no** e **dados de saída**. Deverá ver alguns valores diferentes de zero.

   ![Dados e de saída de dados](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Passos Seguintes

[Implementar aplicações no Azure e o Azure Stack](azure-stack-solution-pipeline.md)
