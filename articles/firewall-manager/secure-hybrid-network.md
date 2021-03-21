---
title: 'Tutorial: Proteja a sua rede virtual do hub utilizando o Azure Firewall Manager'
description: Neste tutorial, aprende a proteger a sua rede virtual com o Azure Firewall Manager utilizando o portal Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: victorh
ms.openlocfilehash: f631100003a4ea6e191a5bdc13a11eb9aa327268
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212552"
---
# <a name="tutorial-secure-your-hub-virtual-network-using-azure-firewall-manager"></a>Tutorial: Proteja a sua rede virtual do hub utilizando o Azure Firewall Manager

Quando liga a sua rede no local a uma rede virtual Azure para criar uma rede híbrida, a capacidade de controlar o acesso aos recursos da rede Azure é uma parte importante de um plano de segurança global.

Utilizando o Azure Firewall Manager, pode criar uma rede virtual de hub para proteger o tráfego da sua rede híbrida destinada a endereços IP privados, Azure PaaS e internet. Pode utilizar o Azure Firewall Manager para controlar o acesso à rede numa rede híbrida utilizando políticas que definem tráfego de rede permitido e negado.

O Gestor de Firewall também suporta uma arquitetura de hub virtual segura. Para comparar os tipos de arquitetura de rede virtual e de hub, veja quais são as opções de [arquitetura do Azure Firewall Manager?](vhubs-and-vnets.md)

Para este tutorial, cria três redes virtuais:

- **VNet-Hub** - a firewall está nesta rede virtual.
- **VNet-Spoke** - a rede virtual falada representa a carga de trabalho localizada no Azure.
- **VNet-Onprem** - A rede virtual no local representa uma rede no local. Numa implementação real, pode ser ligada por uma ligação VPN ou ExpressRoute. Para simplificar, este tutorial utiliza uma ligação de gateway VPN, e uma rede virtual localizada no Azure é usada para representar uma rede no local.

![Rede híbrida](media/tutorial-hybrid-portal/hybrid-network-firewall.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma política de firewall
> * Criar as redes virtuais
> * Configurar e implementar a firewall
> * Criar e ligar os gateways de VPN
> * Peer the hub e falou redes virtuais
> * Criar as rotas
> * Criar as máquinas virtuais
> * Testar a firewall


## <a name="prerequisites"></a>Pré-requisitos

Uma rede híbrida utiliza o modelo de arquitetura hub-and-spoke para encaminhar o tráfego entre a Azure VNets e as redes no local. A arquitetura hub-and-spoke tem os seguintes requisitos:

- Deite **AllowGatewayTransit** ao espreitar VNet-Hub para VNet-Spoke. Numa arquitetura de rede de hub-and-spoke, um trânsito de gateway permite que as redes virtuais falada partilhem a porta de entrada VPN no centro, em vez de implantar gateways VPN em todas as redes virtuais faladas. 

   Além disso, as rotas para as redes virtuais ligadas à porta de entrada ou redes no local propagar-se-ão automaticamente para as tabelas de encaminhamento para as redes virtuais espreitadas utilizando o trânsito de gateway. Para obter mais informações, consulte o [trânsito de gateway VPN VPN para espreitar a rede virtual.](../vpn-gateway/vpn-gateway-peering-gateway-transit.md)

- Descreva **as Vias De Utilização** Quando espreitar VNet-Spoke ao VNet-Hub. Se **o UseRemoteGateways** estiver definido e **o AllowGatewayTransit** no estofo remoto também estiver definido, a rede virtual falada utiliza portais da rede virtual remota para trânsito.
- Para encaminhar o tráfego de sub-rede falada através da firewall do hub, precisa de uma rota definida pelo utilizador (UDR) que aponte para a firewall com a **definição de propagação** da rota de gateway de rede Virtual desativada. Esta opção impede a distribuição da rota para as sub-redes faladas. Isto evita que as rotas aprendidas entrem em conflito com a sua UDR.
- Configure um UDR na sub-rede do gateway do hub que aponta para o endereço IP de firewall como o próximo salto para as redes de raios. Não é necessária nenhuma UDR na sub-rede Azure Firewall, uma vez que aprende rotas a partir de BGP.

Veja a secção [Criar Rotas](#create-the-routes) neste tutorial para perceber como estas rotas são criadas.

>[!NOTE]
>O Azure Firewall tem de ter conectividade Internet direta. Se o seu AzureFirewallSubnet aprender uma rota padrão para a sua rede no local via BGP, deve sobrepor-se a isto com um UDR de 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta na Internet.
>
>A Azure Firewall pode ser configurada para suportar túneis forçados. Para mais informações, consulte [o Azure Firewall forjando túneis.](../firewall/forced-tunneling.md)

>[!NOTE]
>O tráfego entre VNets diretamente espreitados é encaminhado diretamente mesmo que um UDR aponte para Azure Firewall como o gateway padrão. Para enviar o tráfego de sub-rede para a firewall neste cenário, um UDR deve conter explicitamente o prefixo da rede de sub-redes alvo em ambas as sub-redes.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-firewall-policy"></a>Criar uma política de firewall

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Na barra de pesquisa do portal Azure, escreva **Gestor de Firewall** e prima **Enter**.
3. Na página do Gestor de Firewall Azure, selecione **Ver as políticas de firewall do Azure**.

   ![Política de firewall](media/tutorial-hybrid-portal/firewall-manager-policy.png)

1. Selecione **Criar Azure Firewall Policy**.
1. Selecione a sua subscrição e para o grupo de recursos, **selecione Criar novo** e criar um grupo de recursos chamado **FW-Hybrid-Test**.
2. Para o nome da apólice, **escreva Pol-Net01**.
3. Para a Região, selecione **East US**.
1. Selecione **Seguinte : Definições DNS**.
1. Selecione **Seguinte : Inspeção TLS (pré-visualização)**
1. Selecione **Seguinte:Regras**.
1. **Selecione Adicione uma coleção de regras.**
1. Para **nome**, tipo **RCNet01**.
1. Para **o tipo de recolha de regras**, selecione **Rede**.
1. Para **prioridade**, tipo **100**.
1. Em **Ação**, selecione **Permitir**.
1. De **acordo com as regras**, para **nome,** **escreva AllowWeb**.
1. Para **fonte**, tipo **192.168.1.0/24**.
1. Em **Protocolo**, selecione **TCP**.
1. Para **portos de destino**, tipo **80**.
1. Para **o tipo de destino**, selecione endereço **IP**.
1. Para **destino**, tipo **10.6.0.0/16**.
1. Na linha de regras seguinte, insira as seguintes informações:
 
    Nome: tipo **AllowRDP**<br>
    Fonte: tipo **192.168.1.0/24**.<br>
    Protocolo, selecione **TCP**<br>
    Portos de Destino, tipo **3389**<br>
    Tipo de destino, selecione **endereço IP**<br>
    Para destino, tipo **10.6.0.0/16**

1. Selecione **Adicionar**.
2. Selecione **Rever + Criar**.
3. Reveja os detalhes e, em seguida, **selecione Criar**.

## <a name="create-the-firewall-hub-virtual-network"></a>Crie a rede virtual do hub de firewall

> [!NOTE]
> O tamanho da sub-rede AzureFirewallSubnet é /26. Para obter mais informações sobre o tamanho da sub-rede, consulte [a Azure Firewall FAQ](../firewall/firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Procure por **rede Virtual** e, em seguida, selecione **rede Virtual.**
1. Selecione **Criar**.
1. Em **Subscrição**, selecione a sua subscrição.
1. Para **o grupo de recursos**, selecione **FW-Hybrid-Test**.
1. Para **nome,** tipo **VNet-hub**.
1. Para **a Região**, selecione **East US**.
1. Selecione **seguinte : Endereços IP**.

1. Para **o espaço de endereço IPv4**, tipo **10.5.0.0/16**.
1. No **nome da sub-rede,** selecione **predefinição**.
1.  Altere o **nome sub-rede** para **AzureFirewallSubnet**. A firewall está nesta sub-rede, e o nome da sub-rede **deve** ser AzureFirewallSubnet.
1. Para **a gama de endereços sub-rede**, tipo **10.5.0.0/26**. 
1. Aceite as outras definições predefinidos e, em seguida, **selecione Guardar**.
1. Selecione **Rever + criar**.
1. Selecione **Criar**.

## <a name="create-the-spoke-virtual-network"></a>Criar a rede virtual falada

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Procure por **rede Virtual** e, em seguida, selecione **rede Virtual.**
1. Selecione **Criar**.
1. Em **Subscrição**, selecione a sua subscrição.
1. Para **o grupo de recursos**, selecione **FW-Hybrid-Test**.
1. Para **nome**, tipo **VNet-Spoke**.
1. Para **a Região**, selecione **East US**.
1. Selecione **seguinte : Endereços IP**.

1. Para **o espaço de endereço IPv4**, tipo **10.6.0.0/16**.
1. No **nome da sub-rede,** selecione **predefinição**.
1. Altere o **nome sub-rede** para **SN-Workload**.
1. Para **a gama de endereços sub-rede**, tipo **10.6.0.0/24**. 
1. Aceite as outras definições predefinidos e, em seguida, **selecione Guardar**.
1. Selecione **Rever + criar**.
1. Selecione **Criar**.


## <a name="create-the-on-premises-virtual-network"></a>Criar a rede virtual no local

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Procure por **rede Virtual** e, em seguida, selecione **rede Virtual.**
1. Selecione **Criar**.
1. Em **Subscrição**, selecione a sua subscrição.
1. Para **o grupo de recursos**, selecione **FW-Hybrid-Test**.
1. Para **nome**, tipo **VNet-OnPrem**.
1. Para **a Região**, selecione **East US**.
1. Selecione **seguinte : Endereços IP**.

1. Para **o espaço de endereço IPv4**, tipo **192.168.0.0/16**.
1. No **nome da sub-rede,** selecione **predefinição**.
1. Mude o **nome sub-rede** para **SN-Corp**.
1. Para **a gama de endereços sub-rede**, tipo **192.168.1.0/24**. 
1. Aceite as outras definições predefinidos e, em seguida, **selecione Guardar**.
2. Selecione **Adicionar Sub-rede**.
3. Para **o nome da sub-rede,** escreva **GatewaySubnet**.
4. Para **a gama de endereços sub-rede** tipo **192.168.2.0/24**.
5. Selecione **Adicionar**.
1. Selecione **Rever + criar**.
1. Selecione **Criar**.




## <a name="configure-and-deploy-the-firewall"></a>Configurar e implementar a firewall

Quando as políticas de segurança estão associadas a um hub, é referida como uma *rede virtual de hub*.

Converta a rede virtual **VNet-Hub** numa *rede virtual de hub* e fixe-a com o Azure Firewall.

1. Na barra de pesquisa do portal Azure, escreva **Gestor de Firewall** e prima **Enter**.
3. Na página Azure Firewall Manager, em **Adicionar segurança a redes virtuais**, selecione Ver redes virtuais do **hub**.
1. Em **Redes Virtuais,** selecione a caixa de verificação para **o vNet-hub**.
1. Selecione **Gerir a Segurança** e, em seguida, **selecione Implementar uma Firewall com a Política de Firewall**.
1. Na página **de redes virtuais Converte,no** **âmbito da Política de Firewall,** selecione a caixa de verificação para **Pol-Net01**.
1. Selecione **Seguinte : Revisão + confirmar**
1. Reveja os detalhes e, em seguida, **selecione Confirmar**.


   Isto leva alguns minutos para ser acionado.
7. Após a implementação concluída, vá ao grupo de recursos **FW-Hybrid-Test** e selecione a firewall.
9. Note o endereço **IP privado firewall** na página **'Vista Geral'.** Vai utilizá-lo mais tarde quando criar a rota predefinida.

## <a name="create-and-connect-the-vpn-gateways"></a>Criar e ligar os gateways de VPN

O hub e as redes virtuais no local estão conectados através de gateways VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Criar uma porta de entrada VPN para a rede virtual do hub

Agora crie a porta de entrada VPN para a rede virtual do hub. As configurações rede-rede requerem um VpnType de RotaBased. Criar um gateway de VPN, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway de VPN selecionado.

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **gateway de rede virtual** e prima **Enter**.
3. Selecione **o gateway de rede virtual** e selecione **Criar**.
4. Para **nome,** tipo **GW-hub**.
5. Para **a Região**, selecione **(EUA) Leste DOS EUA.**
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
5. Para **a Região**, selecione **(EUA) Leste DOS EUA.**
6. Para **o tipo Gateway**, selecione **VPN**.
7. Para **o tipo VPN**, selecione **Route-based**.
8. Para **SKU**, selecione **Basic**.
9. Para **rede virtual**, selecione **VNet-Onprem**.
10. Para **o endereço IP público**, selecione Criar **novo**, e **digite VNet-Onprem-GW-pip** para o nome.
11. Aceite os predefinidos restantes e, em seguida, **selecione Review + create**.
12. Reveja a configuração e, em seguida, **selecione Criar**.

### <a name="create-the-vpn-connections"></a>Criar as ligações VPN

Agora pode criar as ligações VPN entre o hub e as portas de entrada no local.

Neste passo, cria-se a ligação da rede virtual do hub à rede virtual no local. Nos exemplos, verá uma chave partilhada referenciada. Pode utilizar os seus próprios valores para a chave partilhada. Importante: a chave partilhada tem de corresponder a ambas as ligações. Leva algum tempo para criar a ligação.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione o gateway **gw-hub.**
2. Selecione **Ligações** na coluna esquerda.
3. Selecione **Adicionar**.
4. Para o nome de ligação, escreva **Hub-to-Onprem**.
5. Selecione **VNet-para-VNet** para **o tipo de ligação**.
6. Para o **segundo gateway de rede virtual**, selecione **GW-Onprem**.
7. Para **tecla partilhada (PSK),** **escreva AzureA1b2C3**.
8. Selecione **OK**.

Crie as instalações para hub ligação de rede virtual. Este passo é semelhante ao anterior, exceto que cria a ligação de VNet-Onprem ao VNet-hub. Verifique se as chaves partilhadas correspondem. Após alguns minutos, estará ligado.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione o gateway **GW-Onprem.**
2. Selecione **Ligações** na coluna esquerda.
3. Selecione **Adicionar**.
4. O nome de ligação, **digite Onprem-to-Hub**.
5. Selecione **VNet-para-VNet** para **o tipo de ligação**.
6. Para o **segundo gateway de rede virtual**, selecione **GW-hub**.
7. Para **tecla partilhada (PSK),** **escreva AzureA1b2C3**.
8. Selecione **OK**.


#### <a name="verify-the-connection"></a>Verificar a ligação

Após cerca de cinco minutos, o estado de ambas as ligações deve ser **ligado**.

![Ligações de gateway](media/secure-hybrid-network/gateway-connections.png)

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
   |Gateway de rede virtual ou servidor de rota    |  Use o portal desta rede virtual       |
    
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

   :::image type="content" source="media/secure-hybrid-network/firewall-peering.png" alt-text="Vnet olhando":::

## <a name="create-the-routes"></a>Criar as rotas

Em seguida, crie duas rotas:

- Uma rota da sub-rede de gateway do hub para a sub-rede spoke através do endereço IP da firewall
- Uma rota predefinida da sub-rede spoke através do endereço IP da firewall

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Na caixa de texto de pesquisa, escreva **a tabela de rota** e prima **Enter**.
3. Selecione **tabela rota**.
4. Selecione **Criar**.
1. Selecione o **FW-Hybrid-Test** para o grupo de recursos.
1. Para **a Região**, selecione **East US**.
1. Para o nome, escreva **UDR-Hub-Spoke**.
1. Selecione **Rever + Criar**.
1. Selecione **Criar**.
1. Depois da tabela de rotas ser criada, selecione-a para abrir a página da tabela de rotas.
1. Selecione **Rotas** na coluna esquerda.
1. Selecione **Adicionar**.
1. Para o nome da rota, escreva **ToSpoke**.
1. Para o prefixo do endereço, tipo **10.6.0.0/16**.
1. Para o próximo tipo de lúpulo, selecione **aparelho virtual**.
1. Para o próximo endereço de lúpulo, digite o endereço IP privado da firewall que notou anteriormente.
1. Selecione **OK**.

Agora associe a rota à sub-rede.

1. Na página **UDR-Hub-Spoke - Rotas,** selecione **Subnetas**.
2. Selecione **Associado**.
4. Na **rede Virtual,** selecione **VNet-hub**.
5. Na **sub-rede,** selecione **GatewaySubnet**.
6. Selecione **OK**.

Agora crie a rota padrão a partir da sub-rede falada.

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Na caixa de texto de pesquisa, escreva **a tabela de rota** e prima **Enter**.
3. Selecione **tabela rota**.
5. Selecione **Criar**.
7. Selecione o **FW-Hybrid-Test** para o grupo de recursos.
8. Para **a Região**, selecione **East US**.
1. Para o nome, escreva **UDR-DG**.
4. Para **as rotas de gateway propagate**, selecione **Nº**.
1. Selecione **Rever + criar**.
1. Selecione **Criar**.
1. Depois da tabela de rotas ser criada, selecione-a para abrir a página da tabela de rotas.
1. Selecione **Rotas** na coluna esquerda.
1. Selecione **Adicionar**.
1. Para o nome da rota, escreva **ToHub**.
1. Para o prefixo do endereço, escreva **0.0.0.0/0**.
1. Para o próximo tipo de lúpulo, selecione **aparelho virtual**.
1. Para o próximo endereço de lúpulo, digite o endereço IP privado da firewall que notou anteriormente.
1. Selecione **OK**.

Agora associe a rota à sub-rede.

1. Na página **UDR-DG - Rotas,** selecione **Subnetas**.
2. Selecione **Associado**.
4. Na **rede Virtual,** selecione **VNet-spoke**.
5. Na **sub-rede,** selecione **SN-Workload**.
6. Selecione **OK**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora crie a carga de trabalho falada e as máquinas virtuais no local, e coloque-as nas sub-redes apropriadas.

### <a name="create-the-workload-virtual-machine"></a>Criar a máquina virtual de carga de trabalho

Crie uma máquina virtual na rede virtual falada, executando o IIS, sem endereço IP público.

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Em **Popular**, selecione **Windows Server 2016 Datacenter**.
3. Introduza estes valores para a máquina virtual:
    - **Grupo de recursos** - Selecione **FW-Hybrid-Test**
    - **Nome da máquina virtual**: *VM-Spoke-01*
    - **Região**  -  *(EUA) Leste dos EUA*
    - **Nome do utilizador**: digite um nome de utilizador
    - **Senha**: digite uma senha

4. Selecione **Seguinte:Discos**.
5. Aceite as predefinições e selecione **Seguinte: Networking**.
6. Selecione **VNet-Spoke** para a rede virtual e a sub-rede é **SN-Workload**.
8. Para **portas de entrada pública**, selecione Deixe as portas **selecionadas** e, em seguida, selecione HTTP **(80)** e **RDP (3389)**
1. Selecione **Seguinte:Gestão**.
1. Para **diagnósticos de arranque**, Selecione **Disable**.
1. Selecione **Rever + Criar,** rever as definições na página do resumo e, em seguida, selecione **Criar**.

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
    - **Grupo de recursos** - Selecione a existência e, em seguida, selecione **FW-Hybrid-Test**
    - **Nome da máquina**  -  virtual *VM-Onprem*
    - **Região**  -  *(EUA) Leste dos EUA*
    - **Nome do utilizador**: digite um nome de utilizador
    - **Senha**: digite a sua senha

4. Selecione **Seguinte:Discos**.
5. Aceite as predefinições e selecione **Seguinte:Networking**.
6. Selecione **VNet-Onprem** para rede virtual e verifique se a sub-rede é **SN-Corp**.
7. Para **portas de entrada pública**, selecione Deixe as portas **selecionadas** e, em seguida, selecione **RDP (3389)**
8. Selecione **Seguinte:Gestão**.
9. Para **diagnósticos de arranque,** selecione **Disable**.
10. Selecione **Rever + Criar,** rever as definições na página do resumo e, em seguida, selecione **Criar**.

## <a name="test-the-firewall"></a>Testar a firewall

1. Em primeiro lugar, note o endereço IP privado para a máquina virtual VM-Spoke-01 na página geral VM-Spoke-01.

2. No portal do Azure, ligue à máquina virtual **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Abra um navegador web em **VM-Onprem,** e navegue para http:// \<VM-spoke-01 private IP\> .

   Você deve ver a página web **VM-spoke-01:** ![ VM-Spoke-01 página web](media/secure-hybrid-network/vm-spoke-01-web.png)

4. A partir da máquina virtual **VM-Onprem,** abra um ambiente de trabalho remoto para **o VM-spoke-01** no endereço IP privado.

   A sua ligação deve ter sucesso, e deve ser capaz de assinar.

Então agora verificaste que as regras da firewall estão a funcionar:

<!---- You can ping the server on the spoke VNet.--->
- Pode navegar no servidor web na rede virtual falada.
- Pode ligar-se ao servidor na rede virtual falada utilizando RDP.

Em seguida, altere a ação das coleções de regras de rede da firewall para **Negar**, para verificar se as regras de firewall funcionam conforme esperado.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione a política de firewall **Pol-Net01.**
2. Em **Definições**, selecione **Coleções de Regras**.
1. Selecione a coleção de regras **RCNet01.**
1. Para **ação de recolha de regras**, selecione **Deny**.
1. Selecione **Guardar**.

Feche os ambientes de trabalho remotos existentes e os navegadores em **VM-Onprem** antes de testar as regras alteradas. Depois de concluída a atualização da recolha de regras, volte a fazer os testes. Todos devem falhar na ligação desta vez.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode manter os seus recursos de firewall para mais investigação, ou se já não for necessário, eliminar o grupo de recursos **FW-Hybrid-Test** para eliminar todos os recursos relacionados com firewall.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Proteja o seu WAN virtual usando o Azure Firewall Manager](secure-cloud-network.md)