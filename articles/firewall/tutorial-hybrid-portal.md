---
title: 'Tutorial: implantar e configurar o Firewall do Azure em uma rede híbrida usando o portal do Azure'
description: Neste tutorial, você aprenderá a implantar e configurar o Firewall do Azure usando o portal do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/02/2019
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 4a4fd2f89bc662f394b59aa6295c3a909cb8552b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468460"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>Tutorial: implantar e configurar o Firewall do Azure em uma rede híbrida usando o portal do Azure

Quando você conecta sua rede local a uma rede virtual do Azure para criar uma rede híbrida, a capacidade de controlar o acesso aos recursos de rede do Azure é uma parte importante de um plano de segurança geral.

Você pode usar o Firewall do Azure para controlar o acesso à rede em uma rede híbrida usando regras que definem o tráfego de rede permitido e negado.

Para este tutorial, você cria três redes virtuais:

- **VNet-Hub** -o firewall está nessa rede virtual.
- **VNet-spoke** -a rede virtual spoke representa a carga de trabalho localizada no Azure.
- **VNet-** local – a rede virtual local representa uma rede local. Em uma implantação real, ele pode ser conectado por uma conexão VPN ou ExpressRoute. Para simplificar, este tutorial usa uma conexão de gateway de VPN e uma rede virtual localizada no Azure é usada para representar uma rede local.

![Firewall numa rede híbrida](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Declarar as variáveis
> * Criar a rede virtual do hub de firewall
> * Criar a rede virtual do spoke
> * Criar a rede virtual local
> * Configurar e implementar a firewall
> * Criar e ligar os gateways de VPN
> * Emparelhar as redes virtuais Hub e spoke
> * Criar as rotas
> * Criar as máquinas virtuais
> * Testar a firewall

Se você quiser usar Azure PowerShell em vez disso para concluir este procedimento, consulte [implantar e configurar o Firewall do Azure em uma rede híbrida usando Azure PowerShell](tutorial-hybrid-ps.md).

## <a name="prerequisites"></a>Pré-requisitos

Existem três requisitos chave para este cenário funcionar corretamente:

- Uma rota definida pelo usuário (UDR) na sub-rede do spoke que aponta para o endereço IP do firewall do Azure como o gateway padrão. A propagação de rotas BGP tem de ser **Desativada** nesta tabela de rotas.
- Um UDR na sub-rede de gateway de Hub deve apontar para o endereço IP do firewall como o próximo salto para as redes spoke.

   Nenhum UDR é necessário na sub-rede do firewall do Azure, pois ele aprende as rotas do BGP.
- Certifique-se de que define **AllowGatewayTransit** no peering de VNet-Hub para VNet-Spoke e que utiliza **UseRemoteGateways** no peering de VNet-Spoke para VNet-Hub.

Veja a secção [Criar Rotas](#create-the-routes) neste tutorial para perceber como estas rotas são criadas.

>[!NOTE]
>O Firewall do Azure deve ter conectividade direta com a Internet. Se o seu AzureFirewallSubnet aprende uma rota padrão para sua rede local via BGP, você deve substituí-lo por um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta com a Internet.
>
>No momento, o Firewall do Azure não dá suporte a túnel forçado. Se sua configuração requer o túnel forçado para uma rede local e você pode determinar os prefixos de IP de destino para seus destinos de Internet, você pode configurar esses intervalos com a rede local como o próximo salto por meio de uma rota definida pelo usuário no AzureFirewallSubnet. Ou, você pode usar o BGP para definir essas rotas.

>[!NOTE]
>O tráfego entre VNets emparelhadas diretamente é roteado diretamente mesmo se um UDR aponta para o Firewall do Azure como o gateway padrão. Para enviar a sub-rede ao tráfego de sub-rede para o firewall nesse cenário, um UDR deve conter o prefixo de rede de sub-rede de destino explicitamente em ambas as sub-redes.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-the-firewall-hub-virtual-network"></a>Criar a rede virtual do hub de firewall

Primeiro, crie o grupo de recursos para conter os recursos para este tutorial:

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Na home page portal do Azure, selecione **grupos de recursos** > **Adicionar**.
3. Para **nome do grupo de recursos**, digite **FW-Hybrid-Test**.
4. Em **Subscrição**, selecione a sua subscrição.
5. Para **região**, selecione **leste dos EUA**. Todos os recursos que você criar posteriormente deverão estar no mesmo local.
6. Selecione **revisão + criar**.
7. Selecione **Criar**.

Agora, crie a VNet:

> [!NOTE]
> O tamanho da sub-rede AzureFirewallSubnet é/26. Para obter mais informações sobre o tamanho da sub-rede, consulte [perguntas frequentes sobre o Firewall do Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. No home page de portal do Azure, selecione **criar um recurso**.
2. Em **rede**, selecione **rede virtual**.
4. Para **nome**, digite **VNet-Hub**.
5. Para **espaço de endereço**, digite **10.5.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Para **grupo de recursos**, selecione **FW-Hybrid-Test**.
8. Para **local**, selecione **leste dos EUA**.
9. Em **Sub-rede**, em **Nome**, escreva **AzureFirewallSubnet**. A firewall estará nesta sub-rede, e o nome da sub-rede **tem** de ser AzureFirewallSubnet.
10. Para **intervalo de endereços**, digite **10.5.0.0/26**. 
11. Aceite as outras configurações padrão e, em seguida, selecione **criar**.

## <a name="create-the-spoke-virtual-network"></a>Criar a rede virtual do spoke

1. No home page de portal do Azure, selecione **criar um recurso**.
2. Em **rede**, selecione **rede virtual**.
4. Para **nome**, digite **VNet-spoke**.
5. Para **espaço de endereço**, digite **10.6.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Para **grupo de recursos**, selecione **FW-Hybrid-Test**.
8. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
9. Em **Sub-rede**, para **Nome**, escreva **SN-Workload**.
10. Para **intervalo de endereços**, digite **10.6.0.0/24**.
11. Aceite as outras configurações padrão e, em seguida, selecione **criar**.

## <a name="create-the-on-premises-virtual-network"></a>Criar a rede virtual local

1. No home page de portal do Azure, selecione **criar um recurso**.
2. Em **rede**, selecione **rede virtual**.
4. Para **nome**, digite **VNet-onlocal**.
5. Em **Espaço de endereços**, escreva **192.168.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Para **grupo de recursos**, selecione **FW-Hybrid-Test**.
8. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
9. Em **sub-rede**, para o **nome** , digite **SN-Corp**.
10. Em **Intervalo de endereços**, escreva **192.168.1.0/24**.
11. Aceite as outras configurações padrão e, em seguida, selecione **criar**.

Agora, crie uma segunda sub-rede para o gateway.

1. Na página **VNet-local** , selecione sub- **redes**.
2. Selecione **+ sub-rede**.
3. Para **nome**, digite **GatewaySubnet**.
4. Para o **intervalo de endereços (bloco CIDR)** , digite **192.168.2.0/24**.
5. Selecione **OK**.

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Esse é o endereço IP público usado para o gateway local.

1. No home page de portal do Azure, selecione **criar um recurso**.
2. Na caixa de texto Pesquisar, digite **endereço IP público** e pressione **Enter**.
3. Selecione **endereço IP público** e, em seguida, selecione **criar**.
4. Para o nome, digite **VNet-onlocal-GW-Pip**.
5. Para o grupo de recursos, digite **FW-Hybrid-Test**.
6. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
7. Aceite os outros padrões e, em seguida, selecione **criar**.

## <a name="configure-and-deploy-the-firewall"></a>Configurar e implementar a firewall

Agora, implante o firewall na rede virtual do hub de firewall.

1. No home page de portal do Azure, selecione **criar um recurso**.
2. Na coluna à esquerda, selecione **rede**e, em seguida, selecione **Firewall**.
4. Na página **Criar uma firewall**, utilize a seguinte tabela para configurar a firewall:

   |Definição  |Valor  |
   |---------|---------|
   |Subscrição     |\<a sua subscrição\>|
   |Grupo de recursos     |**FW-híbrido-teste** |
   |Nome     |**AzFW01**|
   |Localização     |Selecionar a mesma localização que utilizou anteriormente|
   |Escolher uma rede virtual     |**Usar existente**:<br> **Hub VNet**|
   |Endereço IP público     |Criar novo: <br>**Nome** - **FW-Pip**. |

5. Selecione **Rever + criar**.
6. Examine o resumo e, em seguida, selecione **criar** para criar o firewall.

   Isso leva alguns minutos para ser implantado.
7. Após a conclusão da implantação, vá para o grupo de recursos **FW-híbrido-teste** e selecione o Firewall do **AzFW01** .
8. Anote o endereço IP privado. Vai utilizá-lo mais tarde quando criar a rota predefinida.

### <a name="configure-network-rules"></a>Configurar regras de rede

Primeiro, adicione uma regra de rede para permitir o tráfego da Web.

1. Na página **AzFW01** , selecione **regras**.
2. Selecione a guia **coleção de regras de rede** .
3. Selecione **Adicionar coleção de regras de rede**.
4. Para **nome**, digite **RCNet01**.
5. Para **prioridade**, digite **100**.
6. Em **Ação**, selecione **Permitir**.
6. Em **regras**, para **nome**, digite **AllowWeb**.
7. Em **Protocolo**, selecione **TCP**.
8. Para **endereços de origem**, digite **192.168.1.0/24**.
9. Para endereço de destino, digite **10.6.0.0/16**
10. Para **portas de destino**, digite **80**.

Agora, adicione uma regra para permitir o tráfego RDP.

Na segunda linha de regra, digite as seguintes informações:

1. **Nome**, digite **AllowRDP**.
2. Em **Protocolo**, selecione **TCP**.
3. Para **endereços de origem**, digite **192.168.1.0/24**.
4. Para endereço de destino, digite **10.6.0.0/16**
5. Para **portas de destino**, digite **3389**.
6. Selecione **Adicionar**.

## <a name="create-and-connect-the-vpn-gateways"></a>Criar e ligar os gateways de VPN

As redes virtuais de Hub e locais são conectadas por meio de gateways de VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Criar um gateway de VPN para a rede virtual do Hub

Agora, crie o gateway de VPN para a rede virtual do Hub. As configurações de rede para rede exigem um VpnType RouteBased. Criar um gateway de VPN, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway de VPN selecionado.

1. No home page de portal do Azure, selecione **criar um recurso**.
2. Na caixa de texto Pesquisar, digite **Gateway de rede virtual** e pressione **Enter**.
3. Selecione **Gateway de rede virtual**e selecione **criar**.
4. Para **nome**, digite **GW-Hub**.
5. Para **região**, selecione a mesma região que você usou anteriormente.
6. Para **tipo de gateway**, selecione **VPN**.
7. Para **tipo de VPN**, selecione **baseado em rota**.
8. Para **SKU**, selecione **básico**.
9. Para **rede virtual**, selecione **VNet-Hub**.
10. Para **endereço IP público**, selecione **criar novo**e digite **VNet-Hub-GW-Pip** para o nome.
11. Aceite os padrões restantes e, em seguida, selecione **revisar + criar**.
12. Examine a configuração e, em seguida, selecione **criar**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Criar um gateway de VPN para a rede virtual local

Agora, crie o gateway de VPN para a rede virtual local. As configurações de rede para rede exigem um VpnType RouteBased. Criar um gateway de VPN, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway de VPN selecionado.

1. No home page de portal do Azure, selecione **criar um recurso**.
2. Na caixa de texto Pesquisar, digite **Gateway de rede virtual** e pressione **Enter**.
3. Selecione **Gateway de rede virtual**e selecione **criar**.
4. Para **nome**, digite **GW-onlocal**.
5. Para **região**, selecione a mesma região que você usou anteriormente.
6. Para **tipo de gateway**, selecione **VPN**.
7. Para **tipo de VPN**, selecione **baseado em rota**.
8. Para **SKU**, selecione **básico**.
9. Para **rede virtual**, selecione **VNet-local**.
10. Para **endereço IP público**, selecione **criar novo**e digite **VNET-onlocal-GW-Pip** para o nome.
11. Aceite os padrões restantes e, em seguida, selecione **revisar + criar**.
12. Examine a configuração e, em seguida, selecione **criar**.

### <a name="create-the-vpn-connections"></a>Criar as ligações VPN

Agora você pode criar as conexões VPN entre o Hub e os gateways locais.

Nesta etapa, você cria a conexão da rede virtual do hub para a rede virtual local. Nos exemplos, verá uma chave partilhada referenciada. Pode utilizar os seus próprios valores para a chave partilhada. Importante: a chave partilhada tem de corresponder a ambas as ligações. A criação de uma ligação pode demorar algum tempo.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione o gateway **GW-Hub** .
2. Selecione **conexões** na coluna esquerda.
3. Selecione **Adicionar**.
4. O nome da conexão, digite **Hub para onlocal**.
5. Selecione **vnet a vnet** para o **tipo de conexão**.
6. Para o **segundo gateway de rede virtual**, selecione **GW-onlocal**.
7. Para **chave compartilhada (PSK)** , digite **AzureA1b2C3**.
8. Selecione **OK**.

Crie a conexão de rede virtual local para o Hub. Esta etapa é semelhante à anterior, exceto que você cria a conexão de VNet-local para VNet-Hub. Verifique se as chaves partilhadas correspondem. Após alguns minutos, estará ligado.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione o gateway **GW-onlocal** .
2. Selecione **conexões** na coluna esquerda.
3. Selecione **Adicionar**.
4. O nome da conexão, digite **onlocal-to-Hub**.
5. Selecione **vnet a vnet** para o **tipo de conexão**.
6. Para o **segundo gateway de rede virtual**, selecione **GW-Hub**.
7. Para **chave compartilhada (PSK)** , digite **AzureA1b2C3**.
8. Selecione **OK**.


#### <a name="verify-the-connection"></a>Verificar a ligação

Após cerca de cinco minutos ou mais, o status de ambas as conexões deve estar **conectado**.

![Conexões de gateway](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Emparelhar as redes virtuais Hub e spoke

Agora, emparelhar as redes virtuais Hub e spoke.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione a rede virtual do **Hub VNet** .
2. Na coluna à esquerda, selecione **emparelhamentos**.
3. Selecione **Adicionar**.
4. Para **nome**, digite **HubtoSpoke**.
5. Para a **rede virtual**, selecione **VNet-spoke**
6. Para o nome do emparelhamento de VNetSpoke para VNet-Hub, digite **SpoketoHub**.
7. Selecione **Permitir trânsito de gateway**.
8. Selecione **OK**.

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>Definir configurações adicionais para o emparelhamento SpoketoHub

Você precisará habilitar o **tráfego de permitir encaminhamento** no emparelhamento SpoketoHub.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione a rede virtual **VNet-spoke** .
2. Na coluna à esquerda, selecione **emparelhamentos**.
3. Selecione o emparelhamento **SpoketoHub** .
4. Em **Permitir tráfego encaminhado do Hub vnet para o vnet-spoke**, selecione **habilitado**.
5. Selecione **Guardar**.

## <a name="create-the-routes"></a>Criar as rotas

Em seguida, crie duas rotas:

- Uma rota da sub-rede de gateway do hub para a sub-rede spoke através do endereço IP da firewall
- Uma rota predefinida da sub-rede spoke através do endereço IP da firewall

1. No home page de portal do Azure, selecione **criar um recurso**.
2. Na caixa de texto Pesquisar, digite **tabela de rotas** e pressione **Enter**.
3. Selecione **tabela de rotas**.
4. Selecione **Criar**.
5. Para o nome, digite **UDR-hub-spoke**.
6. Selecione o **teste FW-Hybrid-Test** para o grupo de recursos.
8. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
9. Selecione **Criar**.
10. Depois que a tabela de rotas for criada, selecione-a para abrir a página tabela de rotas.
11. Selecione **rotas** na coluna esquerda.
12. Selecione **Adicionar**.
13. Para o nome da rota, digite **tospoke**.
14. Para o prefixo de endereço, digite **10.6.0.0/16**.
15. Para tipo do próximo salto, selecione **dispositivo virtual**.
16. Para endereço do próximo salto, digite o endereço IP privado do firewall que você anotou anteriormente.
17. Selecione **OK**.

Agora associe a rota à sub-rede.

1. Na página **UDR-hub-spoke-Routes** , selecione **sub-redes**.
2. Selecione **associar**.
3. Selecione **escolher uma rede virtual**.
4. Selecione **VNet-Hub**.
5. Selecione **GatewaySubnet**.
6. Selecione **OK**.

Agora, crie a rota padrão da sub-rede do spoke.

1. No home page de portal do Azure, selecione **criar um recurso**.
2. Na caixa de texto Pesquisar, digite **tabela de rotas** e pressione **Enter**.
3. Selecione **tabela de rotas**.
5. Selecione **Criar**.
6. Para o nome, digite **UDR-DG**.
7. Selecione o **teste FW-Hybrid-Test** para o grupo de recursos.
8. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
4. Para **propagação de rota de gateway de rede virtual**, selecione **desabilitado**.
1. Selecione **Criar**.
2. Depois que a tabela de rotas for criada, selecione-a para abrir a página tabela de rotas.
3. Selecione **rotas** na coluna esquerda.
4. Selecione **Adicionar**.
5. Para o nome da rota, digite **ToHub**.
6. Para o prefixo de endereço, digite **0.0.0.0/0**.
7. Para tipo do próximo salto, selecione **dispositivo virtual**.
8. Para endereço do próximo salto, digite o endereço IP privado do firewall que você anotou anteriormente.
9. Selecione **OK**.

Agora associe a rota à sub-rede.

1. Na página **UDR-DG-Routes** , selecione **sub-redes**.
2. Selecione **associar**.
3. Selecione **escolher uma rede virtual**.
4. Selecione **VNet-spoke**.
5. Selecione **SN-carga de trabalho**.
6. Selecione **OK**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora, crie a carga de trabalho do spoke e as máquinas virtuais locais e coloque-as nas sub-redes apropriadas.

### <a name="create-the-workload-virtual-machine"></a>Criar a máquina virtual de carga de trabalho

Crie uma máquina virtual na rede virtual do spoke, executando o IIS, sem endereço IP público.

1. No home page de portal do Azure, selecione **criar um recurso**.
2. Em **popular**, selecione **Windows Server 2016 datacenter**.
3. Introduza estes valores para a máquina virtual:
    - **Grupo de recursos** -selecione **FW-Hybrid-Test**.
    - **Nome da máquina virtual**: *VM-spoke-01*.
    - **Região** -mesma região que você está acostumado anteriormente.
    - **Nome de usuário**: *azureuser*.
    - **Senha**: *Azure123456!*
4. Selecione **Avançar: discos**.
5. Aceite os padrões e selecione **Avançar: rede**.
6. Selecione **VNet-spoke** para a rede virtual e a sub-rede é **SN-Workload**.
7. Para **IP público**, selecione **nenhum**.
8. Para **portas de entrada públicas**, selecione **permitir portas selecionadas**e, em seguida, selecione **http (80)** e **RDP (3389)**
9. Selecione **Avançar: gerenciamento**.
10. Para **diagnóstico de inicialização**, selecione **desativado**.
11. Selecione **revisão + criar**, examine as configurações na página Resumo e, em seguida, selecione **criar**.

### <a name="install-iis"></a>Instalar o IIS

1. No portal do Azure, abra o Cloud Shell e verifique se ele está definido como **PowerShell**.
2. Execute o seguinte comando para instalar o IIS na máquina virtual e altere o local, se necessário:

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

### <a name="create-the-on-premises-virtual-machine"></a>Criar a máquina virtual local

Essa é uma máquina virtual que você usa para se conectar usando Área de Trabalho Remota para o endereço IP público. A partir daí, você se conectará ao servidor local por meio do firewall.

1. No home page de portal do Azure, selecione **criar um recurso**.
2. Em **popular**, selecione **Windows Server 2016 datacenter**.
3. Introduza estes valores para a máquina virtual:
    - **Grupo de recursos** -selecione existente e, em seguida, selecione **FW-híbrido-teste**.
    - **Nome da máquina Virtual** - *VM-local*.
    - **Região** -mesma região que você está acostumado anteriormente.
    - **Nome de usuário**: *azureuser*.
    - **Senha**: *Azure123456!* .
4. Selecione **Avançar: discos**.
5. Aceite os padrões e selecione **Avançar: rede**.
6. Selecione **VNet-local** para a rede virtual e a sub-rede é **SN-Corp**.
7. Para **portas de entrada públicas**, selecione **permitir portas selecionadas**e, em seguida, selecione **RDP (3389)**
8. Selecione **Avançar: gerenciamento**.
9. Para **diagnóstico de inicialização**, selecione **desativado**.
10. Selecione **revisão + criar**, examine as configurações na página Resumo e, em seguida, selecione **criar**.

## <a name="test-the-firewall"></a>Testar a firewall

1. Primeiro, anote o endereço IP privado para a máquina virtual **VM-spoke-01** .

2. No portal do Azure, ligue à máquina virtual **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Abra um navegador da Web em **VM-local**e navegue até http://\<VM-spoke-01 IP privado\>.

   Você deve ver a página da Web **VM-spoke-01** : ![a página da Web VM-spoke-01](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. Na máquina virtual **VM-local** , abra uma área de trabalho remota para **VM-spoke-01** no endereço IP privado.

   Sua conexão deve ter sucesso e você deve ser capaz de entrar.

Agora você verificou que as regras de firewall estão funcionando:

<!---- You can ping the server on the spoke VNet.--->
- Você pode procurar o servidor Web na rede virtual do spoke.
- Você pode se conectar ao servidor na rede virtual do spoke usando o RDP.

Em seguida, altere a ação das coleções de regras de rede da firewall para **Negar**, para verificar se as regras de firewall funcionam conforme esperado.

1. Selecione o Firewall do **AzFW01** .
2. Selecione **regras**.
3. Selecione a guia **coleção de regras de rede** e selecione a coleção de regras **RCNet01** .
4. Para **ação**, selecione **negar**.
5. Selecione **Guardar**.

Feche quaisquer ambientes de trabalho remotos existentes antes de testar as regras alteradas. Agora execute os testes novamente. Desta vez, devem falhar todos.

## <a name="clean-up-resources"></a>Limpar recursos

Pode manter os recursos da firewall para o próximo tutorial. Se já não precisar dos mesmos, elimine o grupo de recursos **FW-Hybrid-Test** para eliminar todos os recursos relacionados com a firewall.

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode monitorizar os registos do Azure Firewall.

> [!div class="nextstepaction"]
> [Tutorial: monitorizar registos do Azure Firewall](./tutorial-diagnostics.md)
