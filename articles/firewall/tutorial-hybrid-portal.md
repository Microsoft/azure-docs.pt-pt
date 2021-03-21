---
title: 'Tutorial: Implementar e configurar a Firewall Azure numa rede híbrida utilizando o portal Azure'
description: Neste tutorial, você aprende a implementar e configurar Azure Firewall usando o portal Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/17/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 86e27c190b269763d8dd2f562a207b3f2020da29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98051076"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>Tutorial: Implementar e configurar a Firewall Azure numa rede híbrida utilizando o portal Azure

Quando liga a sua rede no local a uma rede virtual Azure para criar uma rede híbrida, a capacidade de controlar o acesso aos recursos da rede Azure é uma parte importante de um plano de segurança global.

Pode utilizar o Azure Firewall para controlar o acesso à rede numa rede híbrida ao utilizar regras que definem o tráfego de rede permitido e negado.

Para este tutorial, cria três redes virtuais:

- **VNet-Hub** - a firewall está nesta rede virtual.
- **VNet-Spoke** - a rede virtual falada representa a carga de trabalho localizada no Azure.
- **VNet-Onprem** - A rede virtual no local representa uma rede no local. Numa implementação real, pode ser ligada por uma ligação VPN ou ExpressRoute. Para simplificar, este tutorial utiliza uma ligação de gateway VPN, e uma rede virtual localizada no Azure é usada para representar uma rede no local.

![Firewall numa rede híbrida](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Declarar as variáveis
> * Crie a rede virtual do hub de firewall
> * Criar a rede virtual falada
> * Criar a rede virtual no local
> * Configurar e implementar a firewall
> * Criar e ligar os gateways de VPN
> * Peer the hub e falou redes virtuais
> * Criar as rotas
> * Criar as máquinas virtuais
> * Testar a firewall

Se pretender utilizar o Azure PowerShell para completar este procedimento, consulte [implementar e configurar a Azure Firewall numa rede híbrida utilizando o Azure PowerShell](tutorial-hybrid-ps.md).

## <a name="prerequisites"></a>Pré-requisitos

Uma rede híbrida utiliza o modelo de arquitetura hub-and-spoke para encaminhar o tráfego entre a Azure VNets e as redes no local. A arquitetura hub-and-spoke tem os seguintes requisitos:

- Deite **AllowGatewayTransit** ao espreitar VNet-Hub para VNet-Spoke. Numa arquitetura de rede de hub-and-spoke, um trânsito de gateway permite que as redes virtuais falada partilhem a porta de entrada VPN no centro, em vez de implantar gateways VPN em todas as redes virtuais faladas. 

   Além disso, as rotas para as redes virtuais ligadas à porta de entrada ou redes no local propagar-se-ão automaticamente para as tabelas de encaminhamento para as redes virtuais espreitadas utilizando o trânsito de gateway. Para obter mais informações, consulte o [trânsito de gateway VPN VPN para espreitar a rede virtual.](../vpn-gateway/vpn-gateway-peering-gateway-transit.md)

- Descreva **as Vias De Utilização** Quando espreitar VNet-Spoke ao VNet-Hub. Se **o UseRemoteGateways** estiver definido e **o AllowGatewayTransit** no estofo remoto também estiver definido, a rede virtual falada utiliza portais da rede virtual remota para trânsito.
- Para encaminhar o tráfego de sub-rede falada através da firewall do hub, pode utilizar uma rota definida pelo utilizador (UDR) que aponta para a firewall com a opção **de propagação** da rota de gateway de rede Virtual desativada. A opção **de propagação da rota de gateway de rede virtual** impede a distribuição de rotas para as sub-redes faladas. Isto evita que as rotas aprendidas entrem em conflito com a sua UDR. Se pretender manter a **propagação da rota de gateway de rede virtual** ativada, certifique-se de definir rotas específicas para a firewall para anular as que são publicadas no local em vez de BGP.
- Configure um UDR na sub-rede do gateway do hub que aponta para o endereço IP de firewall como o próximo salto para as redes de raios. Não é necessária nenhuma UDR na sub-rede Azure Firewall, uma vez que aprende rotas a partir de BGP.

Veja a secção [Criar Rotas](#create-the-routes) neste tutorial para perceber como estas rotas são criadas.

>[!NOTE]
>O Azure Firewall tem de ter conectividade Internet direta. Se o seu AzureFirewallSubnet aprender uma rota padrão para a sua rede no local via BGP, deve sobrepor-se a isto com um UDR de 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta na Internet.
>
>A Azure Firewall pode ser configurada para suportar túneis forçados. Para mais informações, consulte [o Azure Firewall forjando túneis.](forced-tunneling.md)

>[!NOTE]
>O tráfego entre VNets diretamente espreitados é encaminhado diretamente mesmo que um UDR aponte para Azure Firewall como o gateway padrão. Para enviar o tráfego de sub-rede para a firewall neste cenário, um UDR deve conter explicitamente o prefixo da rede de sub-redes alvo em ambas as sub-redes.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-the-firewall-hub-virtual-network"></a>Crie a rede virtual do hub de firewall

Em primeiro lugar, crie o grupo de recursos para conter os recursos para este tutorial:

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Na página inicial do portal Azure, selecione **Grupos de Recursos**  >  **Adicionar**.
3. Em **Subscrição**, selecione a sua subscrição.
1. Para **o nome do grupo de recursos**, tipo **FW-Hybrid-Test**.
2. Para **a Região**, selecione **(EUA) Leste DOS EUA.** Todos os recursos que criar mais tarde devem estar no mesmo local.
3. Selecione **Rever + Criar**.
4. Selecione **Criar**.

Agora, crie o VNet:

> [!NOTE]
> O tamanho da sub-rede AzureFirewallSubnet é /26. Para obter mais informações sobre o tamanho da sub-rede, consulte [a Azure Firewall FAQ](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Em **Rede,** selecione **Rede Virtual.**
1. Selecione **Criar**.
1. Para **o grupo de recursos**, selecione **FW-Hybrid-Test**.
1. Para **nome,** tipo **VNet-hub**.
1. Selecione **Seguinte: Endereços IP**.
1. Para **o espaço IPv4 Address,** elimine o endereço predefinido e o tipo **10.5.0.0/16**.
1. No **nome da sub-rede,** selecione **Adicionar sub-rede**.
1. Para o nome **da sub-rede,** tipo **AzureFirewallSubnet**. A firewall estará nesta sub-rede, e o nome da sub-rede **tem** de ser AzureFirewallSubnet.
1. Para **a gama de endereços sub-rede**, tipo **10.5.0.0/26**. 
1. Selecione **Adicionar**.
1. Selecione **Rever + criar**.
1. Selecione **Criar**.

## <a name="create-the-spoke-virtual-network"></a>Criar a rede virtual falada

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Em **Networking**, selecione **Rede Virtual**.
7. Para **o grupo de recursos**, selecione **FW-Hybrid-Test**.
1. Para **nome**, tipo **VNet-Spoke**.
2. Para **a Região**, selecione **(EUA) Leste DOS EUA.**
3. Selecione **Seguinte: Endereços IP**.
4. Para **o espaço de endereço IPv4,** elimine o endereço predefinido e o tipo **10.6.0.0/16**.
6. No **nome da sub-rede,** selecione **Adicionar sub-rede**.
7. Para o tipo **de sub-rede** **SN-Workload**.
8. Para **a gama de endereços sub-rede**, tipo **10.6.0.0/24**. 
9. Selecione **Adicionar**.
10. Selecione **Rever + criar**.
11. Selecione **Criar**.

## <a name="create-the-on-premises-virtual-network"></a>Criar a rede virtual no local

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Em **Networking**, selecione **Rede Virtual**.
7. Para **o grupo de recursos**, selecione **FW-Hybrid-Test**.
1. Para **nome**, tipo **VNet-OnPrem**.
2. Para **a Região**, selecione **(EUA) Leste DOS EUA.**
3. Selecione **Seguinte : Endereços IP**
4. Para **o espaço de endereço IPv4**, elimine o endereço predefinido e o tipo **192.168.0.0/16**.
5. No **nome da sub-rede,** selecione **Adicionar sub-rede**.
7. Para o nome **sub-rede** tipo **SN-Corp**.
8. Para **a gama de endereços sub-rede**, tipo **192.168.1.0/24**. 
9. Selecione **Adicionar**.
10. Selecione **Rever + criar**.
11. Selecione **Criar**.

Agora crie uma segunda sub-rede para o portal.

1. Na página **VNet-Onprem,** selecione **Subnets**.
2. Selecione **+Sub-rede**.
3. Para **nome,** **escreva GatewaySubnet**.
4. Para **a gama de endereços sub-rede** tipo **192.168.2.0/24**.
5. Selecione **OK**.

## <a name="configure-and-deploy-the-firewall"></a>Configurar e implementar a firewall

Agora, insi(implantado a firewall na rede virtual do hub de firewall.

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Na coluna esquerda, selecione **Networking** e procure e, em seguida, selecione **Firewall**.
4. Na página **Criar uma firewall**, utilize a seguinte tabela para configurar a firewall:

   |Definição  |Valor  |
   |---------|---------|
   |Subscrição     |\<your subscription\>|
   |Grupo de recursos     |**FW-Hybrid-Test** |
   |Name     |**AzFW01**|
   |Region     |**E.U.A. Leste**|
   |Escolher uma rede virtual     |**Utilização existente:**<br> **VNet-hub**|
   |Endereço IP público     |Adicione novo: <br>**fw-pip**. |

5. Selecione **Rever + criar**.
6. Reveja o resumo e, em seguida, **selecione Criar** para criar a firewall.

   Isto leva alguns minutos para ser acionado.
7. Após a implementação concluída, vá ao grupo de recursos **FW-Hybrid-Test** e selecione a firewall **AzFW01.**
8. Anote o endereço IP privado. Vai utilizá-lo mais tarde quando criar a rota predefinida.

### <a name="configure-network-rules"></a>Configurar regras de rede

Em primeiro lugar, adicione uma regra de rede para permitir o tráfego web.

1. Na página **AzFW01,** Selecione **Regras**.
2. Selecione o separador **de recolha de regras da Rede.**
3. **Selecione Adicionar a recolha de regras de rede**.
4. Para **nome**, tipo **RCNet01**.
5. Para **prioridade**, tipo **100**.
6. Em **Ação**, selecione **Permitir**.
6. De **acordo com as regras**, para **nome,** **escreva AllowWeb**.
7. Em **Protocolo**, selecione **TCP**.
8. Para **o tipo de fonte**, selecione endereço **IP**.
9. Para **fonte**, tipo **192.168.1.0/24**.
10. Para **o tipo destino**, selecione endereço **IP**.
11. Endereço **de destino**, tipo **10.6.0.0/16**
12. Para **portos de destino**, tipo **80**.

Adicione agora uma regra para permitir o tráfego rdp.

Na segunda linha de regras, digite as seguintes informações:

1. **Nome,** tipo **AllowRDP**.
2. Em **Protocolo**, selecione **TCP**.
3. Para **o tipo de fonte**, selecione endereço **IP**.
4. Para **fonte**, tipo **192.168.1.0/24**.
5. Para **o tipo destino**, selecione endereço **IP**.
6. Endereço **de destino**, tipo **10.6.0.0/16**
7. Para **portos de destino**, tipo **3389**.
8. Selecione **Adicionar**.

## <a name="create-and-connect-the-vpn-gateways"></a>Criar e ligar os gateways de VPN

O hub e as redes virtuais no local estão conectados através de gateways VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Criar uma porta de entrada VPN para a rede virtual do hub

Agora crie a porta de entrada VPN para a rede virtual do hub. As configurações rede-rede requerem um VpnType de RotaBased. Criar um gateway de VPN, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway de VPN selecionado.

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Na caixa de texto de pesquisa, escreva **gateway de rede virtual.**
3. Selecione **o gateway de rede virtual** e selecione **Criar**.
4. Para **nome,** tipo **GW-hub**.
5. Para **a Região**, selecione a mesma região que usou anteriormente.
6. Para **o tipo Gateway**, selecione **VPN**.
7. Para **o tipo VPN**, selecione **Route-based**.
8. Para **SKU**, selecione **Basic**.
9. Para **rede virtual**, selecione **VNet-hub**.
10. Para **o endereço IP público**, selecione Criar **novo**, e digite **VNet-hub-GW-pip** para o nome.
11. Aceite os predefinidos restantes e, em seguida, **selecione Review + create**.
12. Reveja a configuração e, em seguida, **selecione Criar**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Criar uma porta de entrada VPN para a rede virtual no local

Agora crie a porta de entrada VPN para a rede virtual no local. As configurações rede-rede requerem um VpnType de RotaBased. Criar um gateway de VPN, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway de VPN selecionado.

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **gateway de rede virtual** e prima **Enter**.
3. Selecione **o gateway de rede virtual** e selecione **Criar**.
4. Para **nome**, tipo **GW-Onprem**.
5. Para **a Região**, selecione a mesma região que usou anteriormente.
6. Para **o tipo Gateway**, selecione **VPN**.
7. Para **o tipo VPN**, selecione **Route-based**.
8. Para **SKU**, selecione **Basic**.
9. Para **rede virtual**, selecione **VNet-Onprem**.
10. Para **o endereço IP público**, selecione Criar **novo**, e **digite VNet-Onprem-GW-pip** para o nome.
11. Aceite os predefinidos restantes e, em seguida, **selecione Review + create**.
12. Reveja a configuração e, em seguida, **selecione Criar**.

### <a name="create-the-vpn-connections"></a>Criar as ligações VPN

Agora pode criar as ligações VPN entre o hub e as portas de entrada no local.

Neste passo, cria-se a ligação da rede virtual do hub à rede virtual no local. Nos exemplos, verá uma chave partilhada referenciada. Pode utilizar os seus próprios valores para a chave partilhada. Importante: a chave partilhada tem de corresponder a ambas as ligações. A criação de uma ligação pode demorar algum tempo.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione o gateway **gw-hub.**
2. Selecione **Ligações** na coluna esquerda.
3. Selecione **Adicionar**.
4. O nome de ligação, tipo **Hub-to-Onprem**.
5. Selecione **VNet-para-VNet** para **o tipo de ligação**.
6. Para o **segundo gateway de rede virtual**, selecione **GW-Onprem**.
7. Para **tecla partilhada (PSK),** **escreva AzureA1b2C3**.
8. Selecione **OK**.

Crie as instalações para hub ligação de rede virtual. Este passo é semelhante ao anterior, exceto que cria a ligação de VNet-Onprem ao VNet-hub. Verifique se as chaves partilhadas correspondem. Após alguns minutos, estará ligado.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione o gateway **GW-Onprem.**
2. Selecione **Ligações** na coluna esquerda.
3. Selecione **Adicionar**.
4. Para o nome de ligação, **digite Onprem-to-Hub**.
5. Selecione **VNet-para-VNet** para **o tipo de ligação**.
6. Para o **segundo gateway de rede virtual**, selecione **GW-hub**.
7. Para **tecla partilhada (PSK),** **escreva AzureA1b2C3**.
8. Selecione **OK**.


#### <a name="verify-the-connection"></a>Verificar a ligação

Após cerca de cinco minutos, o estado de ambas as ligações deve ser **ligado**.

![Ligações de gateway](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Peer the hub e falou redes virtuais

Agora, espreita o centro e fala redes virtuais.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione a rede virtual **VNet-hub.**
2. Na coluna esquerda, **selecione Peerings**.
3. Selecione **Adicionar**.
4. Nesta **rede virtual:**
 
   
   |Nome da definição  |Valor  |
   |---------|---------|
   |Nome de link de espreitar| HubtoSpoke|
   |Tráfego para rede virtual remota|   Permitir (predefinição)      |
   |Tráfego reencaminhado de rede virtual remota    |   Permitir (predefinição)      |
   |Gateway de rede virtual     |  Use o portal desta rede virtual       |
    
5. Em **rede virtual remota:**

   |Nome da definição  |Valor  |
   |---------|---------|
   |Nome de link de espreitar | SpoketoHub|
   |Modelo de implementação de rede virtual| Resource Manager|
   |Subscrição|\<your subscription\>|
   |Rede virtual| VNet-Spoke
   |Tráfego para rede virtual remota     |   Permitir (predefinição)      |
   |Tráfego reencaminhado de rede virtual remota    |   Permitir (predefinição)      |
   |Gateway de rede virtual     |  Use o portal da rede virtual remota       |

5. Selecione **Adicionar**.

   :::image type="content" source="media/tutorial-hybrid-portal/firewall-peering.png" alt-text="Vnet olhando":::

## <a name="create-the-routes"></a>Criar as rotas

Em seguida, crie duas rotas:

- Uma rota da sub-rede de gateway do hub para a sub-rede spoke através do endereço IP da firewall
- Uma rota predefinida da sub-rede spoke através do endereço IP da firewall

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Na caixa de texto de pesquisa, escreva **a tabela de rota** e prima **Enter**.
3. Selecione **tabela rota**.
4. Selecione **Criar**.
6. Selecione o **FW-Hybrid-Test** para o grupo de recursos.
8. Para **a Região**, selecione o mesmo local que usou anteriormente.
1. Para o nome, escreva **UDR-Hub-Spoke**.
9. Selecione **Rever + Criar**.
10. Selecione **Criar**.
11. Depois da tabela de rotas ser criada, selecione-a para abrir a página da tabela de rotas.
12. Selecione **Rotas** na coluna esquerda.
13. Selecione **Adicionar**.
14. Para o nome da rota, escreva **ToSpoke**.
15. Para o prefixo do endereço, tipo **10.6.0.0/16**.
16. Para o próximo tipo de lúpulo, selecione **aparelho virtual**.
17. Para o próximo endereço de lúpulo, digite o endereço IP privado da firewall que notou anteriormente.
18. Selecione **OK**.

Agora associe a rota à sub-rede.

1. Na página **UDR-Hub-Spoke - Rotas,** selecione **Subnetas**.
2. Selecione **Associado**.
3. Na **rede Virtual,** selecione **VNet-hub**.
1. Na **sub-rede,** selecione **GatewaySubnet**.
2. Selecione **OK**.

Agora crie a rota padrão a partir da sub-rede falada.

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Na caixa de texto de pesquisa, escreva **a tabela de rota** e prima **Enter**.
3. Selecione **tabela rota**.
5. Selecione **Criar**.
7. Selecione o **FW-Hybrid-Test** para o grupo de recursos.
8. Para **a Região**, selecione o mesmo local que usou anteriormente.
1. Para o nome, escreva **UDR-DG**.
4. Para **a rota de gateway propagate**, selecione **Nº**.
5. Selecione **Rever + Criar**.
6. Selecione **Criar**.
7. Depois da tabela de rotas ser criada, selecione-a para abrir a página da tabela de rotas.
8. Selecione **Rotas** na coluna esquerda.
9. Selecione **Adicionar**.
10. Para o nome da rota, escreva **ToHub**.
11. Para o prefixo do endereço, escreva **0.0.0.0/0**.
12. Para o próximo tipo de lúpulo, selecione **aparelho virtual**.
13. Para o próximo endereço de lúpulo, digite o endereço IP privado da firewall que notou anteriormente.
14. Selecione **OK**.

Agora associe a rota à sub-rede.

1. Na página **UDR-DG - Rotas,** selecione **Subnetas**.
2. Selecione **Associado**.
3. Na **rede Virtual,** selecione **VNet-spoke**.
1. Na **sub-rede,** selecione **SN-Workload**.
2. Selecione **OK**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora crie a carga de trabalho falada e as máquinas virtuais no local, e coloque-as nas sub-redes apropriadas.

### <a name="create-the-workload-virtual-machine"></a>Criar a máquina virtual de carga de trabalho

Crie uma máquina virtual na rede virtual falada, executando o IIS, sem endereço IP público.

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Em **Popular**, selecione **Windows Server 2016 Datacenter**.
3. Introduza estes valores para a máquina virtual:
    - **Grupo de recursos** - Selecione **FW-Hybrid-Test**.
    - **Nome da máquina virtual**: *VM-Spoke-01*.
    - **Região** - Mesma região que já foi usado anteriormente.
    - **Nome do utilizador**: \<type a user name\> .
    - **Senha:**\<type a password\>
4. Para **portas de entrada pública**, selecione Deixe as portas **selecionadas** e, em seguida, selecione HTTP **(80)** e **RDP (3389)**
4. Selecione **Seguinte:Discos**.
5. Aceite as predefinições e selecione **Seguinte: Networking**.
6. Selecione **VNet-Spoke** para a rede virtual e a sub-rede é **SN-Workload**.
7. Para **IP público**, selecione **Nenhum**. 
9. Selecione **Seguinte:Gestão**.
10. Para **diagnósticos de arranque**, Selecione **Disable**.
11. Selecione **Review+Create**, reveja as definições na página do resumo e, em seguida, **selecione Criar**.

### <a name="install-iis"></a>Instalar o IIS

1. A partir do portal Azure, abra a Cloud Shell e certifique-se de que está definida para **PowerShell**.
2. Executar o seguinte comando para instalar o IIS na máquina virtual e alterar a localização se necessário:

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>Crie a máquina virtual no local

Esta é uma máquina virtual que utiliza para ligar utilizando o Ambiente de Trabalho Remoto ao endereço IP público. A partir daí, liga-se ao servidor no local através da firewall.

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Em **Popular**, selecione **Windows Server 2016 Datacenter**.
3. Introduza estes valores para a máquina virtual:
    - **Grupo de recursos** - Selecione a existência e, em seguida, selecione **FW-Hybrid-Test**.
    - **Nome da máquina**  -  virtual *VM-Onprem.*
    - **Região** - Mesma região que já foi usado anteriormente.
    - **Nome do utilizador**: \<type a user name\> .
    - **Senha**: \<type a user password\> .
7. Para **portas de entrada pública**, selecione Deixe as portas **selecionadas** e, em seguida, selecione **RDP (3389)**
4. Selecione **Seguinte:Discos**.
5. Aceite as predefinições e selecione **Seguinte:Networking**.
6. Selecione **VNet-Onprem** para rede virtual e a sub-rede é **SN-Corp**.
8. Selecione **Seguinte:Gestão**.
10. Para **diagnósticos de arranque**, Selecione **Disable**.
10. Selecione **Review+Create**, reveja as definições na página do resumo e, em seguida, **selecione Criar**.

## <a name="test-the-firewall"></a>Testar a firewall

1. Em primeiro lugar, note o endereço IP privado para a máquina virtual **VM-spoke-01.**

2. No portal do Azure, ligue à máquina virtual **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Abra um navegador web em **VM-Onprem,** e navegue para http:// \<VM-spoke-01 private IP\> .

   Você deve ver a página web **VM-spoke-01:** ![ VM-Spoke-01 página web](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. A partir da máquina virtual **VM-Onprem,** abra um ambiente de trabalho remoto para **o VM-spoke-01** no endereço IP privado.

   A sua ligação deve ter sucesso, e deve ser capaz de assinar.

Então agora verificaste que as regras da firewall estão a funcionar:

<!---- You can ping the server on the spoke VNet.--->
- Pode navegar no servidor web na rede virtual falada.
- Pode ligar-se ao servidor na rede virtual falada utilizando RDP.

Em seguida, altere a ação das coleções de regras de rede da firewall para **Negar**, para verificar se as regras de firewall funcionam conforme esperado.

1. Selecione a firewall **AzFW01.**
2. Selecione **Regras**.
3. Selecione o separador **de recolha de regras da Rede** e selecione a coleção de regras **RCNet01.**
4. For **Action**, selecione **Deny**.
5. Selecione **Guardar**.

Feche quaisquer ambientes de trabalho remotos existentes antes de testar as regras alteradas. Agora execute os testes novamente. Desta vez, devem falhar todos.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode manter os recursos da firewall para o próximo tutorial. Se já não precisar dos mesmos, elimine o grupo de recursos **FW-Hybrid-Test** para eliminar todos os recursos relacionados com a firewall.

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode monitorizar os registos do Azure Firewall.

> [!div class="nextstepaction"]
> [Tutorial: monitorizar registos do Azure Firewall](./firewall-diagnostics.md)