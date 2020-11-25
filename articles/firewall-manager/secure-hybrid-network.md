---
title: 'Tutorial: Proteja a sua rede virtual do hub utilizando o Azure Firewall Manager'
description: Neste tutorial, aprende a proteger a sua rede virtual com o Azure Firewall Manager utilizando o portal Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 3d4d1e65c2200aee178abefb46d3e330acbd3108
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023691"
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

Neste tutorial, vai aprender a:

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
4. Selecione **Seguinte:Regras**.
5. **Selecione Adicione uma coleção de regras.**
6. Para **nome**, tipo **RCNet01**.
7. Para **o tipo de recolha de regras**, selecione **Rede**.
8. Para **prioridade**, tipo **100**.
9. Em **Ação**, selecione **Permitir**.
10. De **acordo com as regras**, para **nome,** **escreva AllowWeb**.
11. Para **endereços de origem**, tipo **192.168.1.0/24**.
12. Em **Protocolo**, selecione **TCP**.
13. Para **portos de destino**, tipo **80**.
14. Para **o tipo de destino**, selecione endereço **IP**.
15. Para **destino**, tipo **10.6.0.0/16**.
16. Na linha de regras seguinte, insira as seguintes informações:
 
    Nome: tipo **AllowRDP**<br>
    Endereço IP de origem: tipo **192.168.1.0/24**.<br>
    Protocolo, selecione **TCP**<br>
    Portos de Destino, tipo **3389**<br>
    Tipo de destino, selecione **endereço IP**<br>
    Para destino, tipo **10.6.0.0/16**

1. Selecione **Adicionar**.
2. Selecione **Review + Criar**.
3. Reveja os detalhes e, em seguida, **selecione Criar**.

## <a name="create-the-firewall-hub-virtual-network"></a>Crie a rede virtual do hub de firewall

> [!NOTE]
> O tamanho da sub-rede AzureFirewallSubnet é /26. Para obter mais informações sobre o tamanho da sub-rede, consulte [a Azure Firewall FAQ](../firewall/firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Em **Rede,** selecione **Rede Virtual.**
4. Para **nome,** tipo **VNet-hub**.
5. Para **o espaço address**, tipo **10.5.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Para **o grupo de recursos**, selecione **FW-Hybrid-Test**.
8. Para **localização**, selecione **East US**.
9. Em **Sub-rede**, em **Nome**, escreva **AzureFirewallSubnet**. A firewall estará nesta sub-rede, e o nome da sub-rede **tem** de ser AzureFirewallSubnet.
10. Para **a gama Address**, tipo **10.5.0.0/26**. 
11. Aceite as outras definições predefinidos e, em seguida, **selecione Criar**.

## <a name="create-the-spoke-virtual-network"></a>Criar a rede virtual falada

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Em **Rede,** selecione **Rede Virtual.**
4. Para **nome**, tipo **VNet-Spoke**.
5. Para **o espaço address**, tipo **10.6.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Para **o grupo de recursos**, selecione **FW-Hybrid-Test**.
8. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
9. Em **Sub-rede**, para **Nome**, escreva **SN-Workload**.
10. Para **a gama Address**, tipo **10.6.0.0/24**.
11. Aceite as outras definições predefinidos e, em seguida, **selecione Criar**.

## <a name="create-the-on-premises-virtual-network"></a>Criar a rede virtual no local

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Em **Rede,** selecione **Rede Virtual.**
4. Para **nome**, tipo **VNet-OnPrem**.
5. Em **Espaço de endereços**, escreva **192.168.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Para **o grupo de recursos**, selecione **FW-Hybrid-Test**.
8. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
9. Na **sub-rede**, para o tipo **de nome** **SN-Corp**.
10. Em **Intervalo de endereços**, escreva **192.168.1.0/24**.
11. Aceite as outras definições predefinidos e, em seguida, **selecione Criar**.

Depois de a rede virtual ser implantada, crie uma segunda sub-rede para o gateway.

1. Na página **VNet-Onprem,** selecione **Subnets**.
2. Selecione **+Sub-rede**.
3. Para **nome,** **escreva GatewaySubnet**.
4. Para **a gama address (bloco CIDR)** tipo **192.168.2.0/24**.
5. Selecione **OK**.

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Este é o endereço IP público utilizado para o portal de entrada no local.

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **o endereço IP público** e prima **Enter**.
3. Selecione **o endereço IP público** e, em seguida, selecione **Criar**.
4. Para o nome, digite **VNet-Onprem-GW-pip**.
5. Para o grupo de recursos, escreva **FW-Hybrid-Test**.
6. Para **localização**, selecione **East US**.
7. Aceite os outros incumprimentos e, em seguida, **selecione Criar**.

## <a name="configure-and-deploy-the-firewall"></a>Configurar e implementar a firewall

Quando as políticas de segurança estão associadas a um hub, é referida como uma *rede virtual de hub*.

Converta a rede virtual **VNet-Hub** numa *rede virtual de hub* e fixe-a com o Azure Firewall.

1. Na barra de pesquisa do portal Azure, escreva **Gestor de Firewall** e prima **Enter**.
3. Na página Azure Firewall Manager, em **Adicionar segurança a redes virtuais**, selecione Ver redes virtuais do **hub**.
4. Selecione **Converter redes virtuais**.
5. Selecione **VNet-hub** e, em seguida, **selecione Seguinte : Azure Firewall**.
6. Para a Política de **Firewall**, selecione **Pol-Net01**.
7. Selecione **Next " Review + confirme**
8. Reveja os detalhes e, em seguida, **selecione Confirmar**.


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
10. Para **o endereço IP público**, selecione * Utilize *o* **VNet-Onprem-GW-pip** para o nome.
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
4. Para **nome,** **escreva HubtoSpoke**.
5. Para a **rede Virtual**, selecione **VNet-spoke**
6. Para o nome do espremiamento de VNetSpoke para VNet-hub, **escreva SpoketoHub**.
7. Selecione **Permita o trânsito de gateway**.
8. Selecione **OK**.

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>Configurar definições adicionais para o olhar do SpoketoHub

Terá de ativar o **tráfego reencaminhado** do SpoketoHub.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione a rede virtual **VNet-Spoke.**
2. Na coluna esquerda, **selecione Peerings**.
3. Selecione o **olho do SpoketoHub.**
4. Em **Permitir o tráfego reencaminhado do VNet-hub para vNet-Spoke,** selecione **Enabled**.
5. Selecione **Guardar**.

## <a name="create-the-routes"></a>Criar as rotas

Em seguida, crie duas rotas:

- Uma rota da sub-rede de gateway do hub para a sub-rede spoke através do endereço IP da firewall
- Uma rota predefinida da sub-rede spoke através do endereço IP da firewall

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Na caixa de texto de pesquisa, escreva **a tabela de rota** e prima **Enter**.
3. Selecione **tabela rota**.
4. Selecione **Criar**.
5. Para o nome, escreva **UDR-Hub-Spoke**.
6. Selecione o **FW-Hybrid-Test** para o grupo de recursos.
8. Para **localização**, selecione **(EUA) East US)**.
9. Selecione **Criar**.
10. Depois da tabela de rotas ser criada, selecione-a para abrir a página da tabela de rotas.
11. Selecione **Rotas** na coluna esquerda.
12. Selecione **Adicionar**.
13. Para o nome da rota, escreva **ToSpoke**.
14. Para o prefixo do endereço, tipo **10.6.0.0/16**.
15. Para o próximo tipo de lúpulo, selecione **aparelho virtual**.
16. Para o próximo endereço de lúpulo, digite o endereço IP privado da firewall que notou anteriormente.
17. Selecione **OK**.

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
6. Para o nome, escreva **UDR-DG**.
7. Selecione o **FW-Hybrid-Test** para o grupo de recursos.
8. Para **localização**, selecione **(EUA) East US)**.
4. Para **propagação da rota de gateway de rede virtual,** selecione **Desativado**.
1. Selecione **Criar**.
2. Depois da tabela de rotas ser criada, selecione-a para abrir a página da tabela de rotas.
3. Selecione **Rotas** na coluna esquerda.
4. Selecione **Adicionar**.
5. Para o nome da rota, escreva **ToHub**.
6. Para o prefixo do endereço, escreva **0.0.0.0/0**.
7. Para o próximo tipo de lúpulo, selecione **aparelho virtual**.
8. Para o próximo endereço de lúpulo, digite o endereço IP privado da firewall que notou anteriormente.
9. Selecione **OK**.

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
    - **Grupo de recursos** - Selecione **FW-Hybrid-Test**.
    - **Nome da máquina virtual**: *VM-Spoke-01*.
    - **Região**  -  *(EUA) Leste dos EUA)*.
    - **Nome do utilizador:** *azureuser*.
    - **Senha**: digite a sua senha

4. Selecione **Seguinte:Discos**.
5. Aceite as predefinições e selecione **Seguinte: Networking**.
6. Selecione **VNet-Spoke** para a rede virtual e a sub-rede é **SN-Workload**.
7. Para **IP público**, selecione **Nenhum**.
8. Para **portas de entrada pública**, selecione Deixe as portas **selecionadas** e, em seguida, selecione HTTP **(80)** e **RDP (3389)**
9. Selecione **Seguinte:Gestão**.
10. Para **diagnósticos de arranque**, Selecione **Off**.
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
    - **Região**  -  *(EUA) Leste dos EUA)*.
    - **Nome do utilizador:** *azureuser*.
    - **Senha:** digite a sua senha.

4. Selecione **Seguinte:Discos**.
5. Aceite as predefinições e selecione **Seguinte:Networking**.
6. Selecione **VNet-Onprem** para rede virtual e verifique se a sub-rede é **SN-Corp**.
7. Para **portas de entrada pública**, selecione Deixe as portas **selecionadas** e, em seguida, selecione **RDP (3389)**
8. Selecione **Seguinte:Gestão**.
9. Para **diagnósticos de arranque**, selecione **Off**.
10. Selecione **Review+Create**, reveja as definições na página do resumo e, em seguida, **selecione Criar**.

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
2. Em **Definições**, selecione **Regras**.
3. De acordo com **as regras da Rede**, selecione a coleção de regras **RCNet01,** selecione as elipses (...), e selecione **Editar**.
4. Para **ação de recolha de regras**, selecione **Deny**.
5. Selecione **Guardar**.

Feche os ambientes de trabalho remotos existentes e os navegadores em **VM-Onprem** antes de testar as regras alteradas. Depois de concluída a atualização da recolha de regras, volte a fazer os testes. Desta vez, devem falhar todos.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode manter os recursos da firewall para o próximo tutorial. Se já não precisar dos mesmos, elimine o grupo de recursos **FW-Hybrid-Test** para eliminar todos os recursos relacionados com a firewall.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Proteja o seu WAN virtual usando o Azure Firewall Manager](secure-cloud-network.md)