---
title: 'Tutorial: Proteja a sua rede virtual do hub utilizando a pré-visualização do Azure Firewall Manager'
description: Neste tutorial, aprende a proteger a sua rede virtual com o Azure Firewall Manager utilizando o portal Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: cdd416bdb833e4784334a6847d724a7375e2ef8d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77459958"
---
# <a name="tutorial-secure-your-hub-virtual-network-using-azure-firewall-manager-preview"></a>Tutorial: Proteja a sua rede virtual do hub utilizando a pré-visualização do Azure Firewall Manager 

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Quando liga a sua rede no local a uma rede virtual Azure para criar uma rede híbrida, a capacidade de controlar o acesso aos recursos da rede Azure é uma parte importante de um plano de segurança global.

Utilizando o Azure Firewall Manager Preview, pode criar uma rede virtual hub para garantir o tráfego de rede híbrida destinado a endereços IP privados, Azure PaaS e internet. Pode utilizar o Azure Firewall Manager para controlar o acesso à rede numa rede híbrida utilizando políticas que definam o tráfego de rede permitido e negado.

O Firewall Manager também suporta uma arquitetura de hub virtual segura. Para uma comparação entre os tipos de arquitetura de rede virtual e hub seguros, veja quais são as opções de [arquitetura Azure Firewall Manager?](vhubs-and-vnets.md)

Para este tutorial, cria três redes virtuais:

- **VNet-Hub** - a firewall está nesta rede virtual.
- **VNet-Spoke** - a rede virtual falada representa a carga de trabalho localizada no Azure.
- **VNet-Onprem** - A rede virtual no local representa uma rede no local. Numa implementação real, pode ser ligado por uma ligação VPN ou ExpressRoute. Para a simplicidade, este tutorial utiliza uma ligação de gateway VPN, e uma rede virtual localizada no Azure é usada para representar uma rede no local.

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

Uma rede híbrida utiliza o modelo de arquitetura hub-and-spoke para direcionar o tráfego entre as VNets Azure e as redes no local. A arquitetura hub-and-spoke tem os seguintes requisitos:

- Desprendimento **allowGatewayTransit** ao espreitar vNet-hub para VNet-Spoke. Numa arquitetura de rede de hub-and-spoke, um trânsito de gateway permite que as redes virtuais faladas partilhem o gateway VPN no centro, em vez de implantar gateways VPN em todas as redes virtuais. 

   Além disso, as rotas para as redes virtuais ligadas ao gateway ou nas redes no local propagar-se-ão automaticamente para as tabelas de encaminhamento para as redes virtuais peered utilizando o trânsito de gateway. Para mais informações, consulte o trânsito de [gateway Configure VPN para o peering de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Detete **UseRemoteGateways** quando peer VNet-Spoke to VNet-Hub. Se **o UseRemoteGateways** for definido e **o AllowGatewayTransit** no telecomando também estiver definido, a rede virtual falada utiliza gateways da rede virtual remota para trânsito.
- Para encaminhar o tráfego de sub-rede falado através da firewall do hub, precisa de uma rota Definida pelo Utilizador (UDR) que aponte para a firewall com a definição de **propagação** da rota de gateway da rede Virtual desativada. Esta opção impede a distribuição da rota para as subredes faladas. Isto impede que as rotas aprendidas entrem em conflito com o seu UDR.
- Configure um UDR na subnet de gateway do hub que aponta para o endereço IP firewall como o próximo salto para as redes faladas. Não é necessário nenhum UDR na subnet Azure Firewall, uma vez que aprende rotas a partir de BGP.

Veja a secção [Criar Rotas](#create-the-routes) neste tutorial para perceber como estas rotas são criadas.

>[!NOTE]
>O Azure Firewall deve ter conectividade direta com a Internet. Se o seu AzureFirewallSubnet aprender uma rota padrão para a sua rede no local via BGP, deve sobrepor-se a esta com um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta da Internet.
>
>A Firewall Azure pode ser configurada para suportar túneis forçados. Para mais informações, consulte [o Azure Firewall forçado a fazer túneis.](../firewall/forced-tunneling.md)

>[!NOTE]
>O tráfego entre VNets diretamente espreitados é encaminhado diretamente mesmo que um UDR aponta para O Firewall Azure como a porta de entrada padrão. Para enviar a sub-rede para sub-rede para a firewall neste cenário, um UDR deve conter explicitamente a prefixação da rede de sub-rede alvo em ambas as subredes.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-firewall-policy"></a>Criar uma Política de Firewall

1. Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em .
2. Na barra de pesquisa do portal Azure, escreva **Firewall Manager** e prima **Enter**.
3. Na página Do Gestor de Firewall Azure, selecione as políticas de **firewall do View Azure**.

   ![Política de firewall](media/tutorial-hybrid-portal/firewall-manager-policy.png)

1. Selecione **Criar a Política de Firewall Azure**.
1. Selecione a sua subscrição e, para o grupo Recursos, selecione **Criar novo** e criar um grupo de recursos chamado **FW-Hybrid-Test**.
2. Para o nome da política, digite **Pol-Net01**.
3. Para a Região, selecione **East US**.
4. Selecione **Next:Rules**.
5. Selecione **Adicionar uma coleção de regras**.
6. Para **Nome**, digite **RCNet01**.
7. Para o tipo de **recolha de regras,** selecione **Rede**.
8. Para **Prioridade**, tipo **100**.
9. Em **Ação**, selecione **Permitir**.
10. De acordo com **as regras,** para **nome,** tipo **AllowWeb**.
11. Para **endereços de origem**, tipo **192.168.1.0/24**.
12. Em **Protocolo**, selecione **TCP**.
13. Para portas de **destino,** tipo **80**.
14. Para **o Tipo de Destino,** selecione endereço **IP**.
15. Para **destino**, tipo **10.6.0.0/16**.
16. Na próxima linha de regras, insira as seguintes informações:
 
    Nome: tipo **AllowRDP**<br>
    Endereço IP fonte: tipo **192.168.1.0/24**.<br>
    Protocolo, selecione **TCP**<br>
    Portos de Destino, tipo **3389**<br>
    Tipo de destino, selecione **Endereço IP**<br>
    Para destino, tipo **10.6.0.0/16**

1. Selecione **Adicionar**.
2. Selecione **Rever + Criar**.
3. Reveja os detalhes e, em seguida, selecione **Criar**.

## <a name="create-the-firewall-hub-virtual-network"></a>Criar a rede virtual do firewall hub

> [!NOTE]
> O tamanho da sub-rede AzureFirewallSubnet é de /26. Para obter mais informações sobre o tamanho da subrede, consulte [O FaQ da Firewall Azure](../firewall/firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. A partir da página inicial do portal Azure, selecione **Criar um recurso**.
2. Em **Rede,** selecione **rede Virtual**.
4. Para **nome**, tipo **VNet-hub**.
5. Para **o espaço Endereço**, tipo **10.5.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Para **o grupo Derecursos,** selecione **FW-Hybrid-Test**.
8. Para **localização,** selecione **East US**.
9. Em **Sub-rede**, em **Nome**, escreva **AzureFirewallSubnet**. A firewall estará nesta sub-rede, e o nome da sub-rede **tem** de ser AzureFirewallSubnet.
10. Para **o intervalo de endereços**, tipo **10.5.0.0/26**. 
11. Aceite as outras definições predefinidas e, em seguida, selecione **Criar**.

## <a name="create-the-spoke-virtual-network"></a>Criar a rede virtual falada

1. A partir da página inicial do portal Azure, selecione **Criar um recurso**.
2. Em **Rede,** selecione **rede Virtual**.
4. Para **nome**, tipo **VNet-Spoke**.
5. Para **o espaço Address**, tipo **10.6.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Para **o grupo Derecursos,** selecione **FW-Hybrid-Test**.
8. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
9. Em **Sub-rede**, para **Nome**, escreva **SN-Workload**.
10. Para **o intervalo de endereços**, tipo **10.6.0.0/24**.
11. Aceite as outras definições predefinidas e, em seguida, selecione **Criar**.

## <a name="create-the-on-premises-virtual-network"></a>Criar a rede virtual no local

1. A partir da página inicial do portal Azure, selecione **Criar um recurso**.
2. Em **Rede,** selecione **rede Virtual**.
4. Para **nome**, escreva **VNet-OnPrem**.
5. Em **Espaço de endereços**, escreva **192.168.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Para **o grupo Derecursos,** selecione **FW-Hybrid-Test**.
8. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
9. Em **Subnet,** para **nome** tipo **SN-Corp**.
10. Em **Intervalo de endereços**, escreva **192.168.1.0/24**.
11. Aceite as outras definições predefinidas e, em seguida, selecione **Criar**.

Depois de a rede virtual ser implantada, crie uma segunda sub-rede para o portal.

1. Na página **VNet-Onprem,** selecione **Subnets**.
2. **Selecione +Subnet**.
3. Para **nome**, escreva **GatewaySubnet**.
4. Para **a gama de endereços (bloco CIDR)** tipo **192.168.2.0/24**.
5. Selecione **OK**.

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Este é o endereço IP público utilizado para o gateway no local.

1. A partir da página inicial do portal Azure, selecione **Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **o endereço IP público** e prima **Introduza**.
3. Selecione **o endereço IP público** e, em seguida, selecione **Criar**.
4. Para o nome, digite **VNet-Onprem-GW-pip**.
5. Para o grupo de recursos, digite **FW-Hybrid-Test**.
6. Para **localização,** selecione **East US**.
7. Aceite as outras predefinições e, em seguida, selecione **Criar**.

## <a name="configure-and-deploy-the-firewall"></a>Configurar e implementar a firewall

Quando as políticas de segurança são associadas a um hub, é referida como uma *rede virtual hub*.

Converta a rede virtual **VNet-Hub** numa *rede virtual hub* e fixe-a com o Azure Firewall.

1. Na barra de pesquisa do portal Azure, escreva **Firewall Manager** e prima **Enter**.
3. Na página Do Gestor de Firewall Azure, sob **a segurança adicionar a redes virtuais,** selecione Ver redes virtuais do **hub**.
4. Selecione **Converter redes virtuais**.
5. Selecione **VNet-hub** e, em seguida, selecione **Next: Azure Firewall**.
6. Para a **Política de Firewall,** selecione **Pol-Net01**.
7. Selecione **Next " Review + confirme**
8. Reveja os detalhes e, em seguida, selecione **Confirmar**.


   Isto leva alguns minutos para ser implantado.
7. Após a implementação completa, vá ao grupo de recursos **FW-Hybrid-Test** e selecione a firewall.
9. Note o endereço **IP privado firewall** na página **'Overview'.** Vai utilizá-lo mais tarde quando criar a rota predefinida.

## <a name="create-and-connect-the-vpn-gateways"></a>Criar e ligar os gateways de VPN

As redes virtuais do hub e no local estão ligadas através de gateways VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Criar uma porta de entrada VPN para a rede virtual hub

Agora crie a porta de entrada VPN para a rede virtual hub. As configurações de rede para rede requerem um VpnType baseado em rotas. Criar um gateway de VPN, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway de VPN selecionado.

1. A partir da página inicial do portal Azure, selecione **Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **o gateway da rede virtual** e prima **Enter**.
3. Selecione gateway de **rede Virtual**e selecione **Criar**.
4. Para **nome**, tipo **GW-hub**.
5. Para **a Região,** selecione **(EUA) Leste dos EUA**.
6. Para **o tipo Gateway,** selecione **VPN**.
7. Para **o tipo VPN,** selecione **baseado na Rota**.
8. Para **SKU,** selecione **Basic**.
9. Para **a rede Virtual,** selecione **VNet-hub**.
10. Para **o endereço IP público,** selecione Criar **novo**, e **digitar VNet-hub-GW-pip** para o nome.
11. Aceite as restantes predefinições e, em seguida, selecione **Rever + criar**.
12. Reveja a configuração e, em seguida, selecione **Criar**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Criar uma porta de entrada VPN para a rede virtual no local

Agora crie a porta de entrada VPN para a rede virtual no local. As configurações de rede para rede requerem um VpnType baseado em rotas. Criar um gateway de VPN, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway de VPN selecionado.

1. A partir da página inicial do portal Azure, selecione **Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **o gateway da rede virtual** e prima **Enter**.
3. Selecione gateway de **rede Virtual**e selecione **Criar**.
4. Para **nome**, digite **GW-Onprem**.
5. Para **a Região,** selecione **(EUA) Leste dos EUA**.
6. Para **o tipo Gateway,** selecione **VPN**.
7. Para **o tipo VPN,** selecione **baseado na Rota**.
8. Para **SKU,** selecione **Basic**.
9. Para **a rede Virtual,** selecione **VNet-Onprem**.
10. Para **o endereço IP público**, selecione * Utilize o*existente*, e selecione **VNet-Onprem-GW-pip** para o nome.
11. Aceite as restantes predefinições e, em seguida, selecione **Rever + criar**.
12. Reveja a configuração e, em seguida, selecione **Criar**.

### <a name="create-the-vpn-connections"></a>Criar as ligações VPN

Agora pode criar as ligações VPN entre o hub e as portas de entrada no local.

Neste passo, cria-se a ligação da rede virtual hub à rede virtual no local. Nos exemplos, verá uma chave partilhada referenciada. Pode utilizar os seus próprios valores para a chave partilhada. Importante: a chave partilhada tem de corresponder a ambas as ligações. A criação de uma ligação pode demorar algum tempo.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione o gateway **GW-hub.**
2. Selecione **Ligações** na coluna esquerda.
3. Selecione **Adicionar**.
4. O nome de ligação, tipo **Hub-to-Onprem**.
5. Selecione **VNet-to-VNet** para o tipo de **ligação**.
6. Para o segundo portal de **rede virtual,** selecione **GW-Onprem**.
7. Para **tecla partilhada (PSK)**, tipo **AzureA1b2C3**.
8. Selecione **OK**.

Crie as instalações para centralar a ligação de rede virtual. Este passo é semelhante ao anterior, exceto que cria a ligação do VNet-Onprem ao VNet-hub. Verifique se as chaves partilhadas correspondem. Após alguns minutos, estará ligado.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione o gateway **GW-Onprem.**
2. Selecione **Ligações** na coluna esquerda.
3. Selecione **Adicionar**.
4. O nome de ligação, escreva **Onprem-to-Hub**.
5. Selecione **VNet-to-VNet** para o tipo de **ligação**.
6. Para o segundo portal de **rede virtual,** selecione **GW-hub**.
7. Para **tecla partilhada (PSK)**, tipo **AzureA1b2C3**.
8. Selecione **OK**.


#### <a name="verify-the-connection"></a>Verificar a ligação

Após cerca de cinco minutos, o estado de ambas as ligações deve estar **ligado**.

![Ligações de gateway](media/secure-hybrid-network/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Peer the hub e falou redes virtuais

Agora, olhe o centro e falei redes virtuais.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione a rede virtual **VNet-hub.**
2. Na coluna esquerda, selecione **Peerings**.
3. Selecione **Adicionar**.
4. Para **nome**, tipo **HubtoSpoke**.
5. Para a **rede Virtual,** selecione **VNet-spoke**
6. Para o nome do peering de VNetSpoke para VNet-hub, tipo **SpoketoHub**.
7. **Selecione Permitir o trânsito**de gateway .
8. Selecione **OK**.

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>Configure configurar configurações adicionais para o olhar do SpoketoHub

Terá de ativar o **tráfego reencaminhado** para o SpoketoHub.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione a rede virtual **VNet-Spoke.**
2. Na coluna esquerda, selecione **Peerings**.
3. Selecione o olhar **do SpoketoHub.**
4. Em **Permitir o tráfego reencaminhado do VNet-hub para VNet-Spoke**, selecione **Enabled**.
5. Selecione **Guardar**.

## <a name="create-the-routes"></a>Criar as rotas

Em seguida, crie duas rotas:

- Uma rota da sub-rede de gateway do hub para a sub-rede spoke através do endereço IP da firewall
- Uma rota predefinida da sub-rede spoke através do endereço IP da firewall

1. A partir da página inicial do portal Azure, selecione **Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **tabela de rota** e prima **Introduza**.
3. Selecione **tabela Rota**.
4. Selecione **Criar**.
5. Para o nome, escreva **UDR-Hub-Spoke**.
6. Selecione o **FW-Hybrid-Test** para o grupo de recursos.
8. Para **localização**, selecione **(EUA) East US)**.
9. Selecione **Criar**.
10. Depois da tabela de rotas ser criada, selecione-a para abrir a página da tabela de rotas.
11. Selecione **Rotas** na coluna esquerda.
12. Selecione **Adicionar**.
13. Para o nome da rota, digite **ToSpoke**.
14. Para o prefixo do endereço, tipo **10.6.0.0/16**.
15. Para o próximo tipo de lúpulo, selecione **Aparelho Virtual**.
16. Para o próximo endereço de lúpulo, digite o endereço IP privado da firewall que observou anteriormente.
17. Selecione **OK**.

Agora associe a rota para a sub-rede.

1. Na página **UDR-Hub-Spoke - Rotas,** selecione **Subnets**.
2. Selecione **Associado**.
4. Em **rede Virtual,** selecione **VNet-hub**.
5. Em **Subnet,** selecione **GatewaySubnet**.
6. Selecione **OK**.

Agora crie a rota padrão a partir da subnet falada.

1. A partir da página inicial do portal Azure, selecione **Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **tabela de rota** e prima **Introduza**.
3. Selecione **tabela Rota**.
5. Selecione **Criar**.
6. Para o nome, digite **UDR-DG**.
7. Selecione o **FW-Hybrid-Test** para o grupo de recursos.
8. Para **localização**, selecione **(EUA) East US)**.
4. Para propagação da rota de gateway de **rede virtual,** selecione **Desativado**.
1. Selecione **Criar**.
2. Depois da tabela de rotas ser criada, selecione-a para abrir a página da tabela de rotas.
3. Selecione **Rotas** na coluna esquerda.
4. Selecione **Adicionar**.
5. Para o nome da rota, digite **ToHub**.
6. Para o prefixo de endereço, tipo **0.0.0.0/0 .**
7. Para o próximo tipo de lúpulo, selecione **Aparelho Virtual**.
8. Para o próximo endereço de lúpulo, digite o endereço IP privado da firewall que observou anteriormente.
9. Selecione **OK**.

Agora associe a rota para a sub-rede.

1. Na página **UDR-DG - Rotas,** selecione **Subnets**.
2. Selecione **Associado**.
4. Em **rede Virtual,** selecione **VNet-spoke**.
5. Em **Subnet,** selecione **SN-Workload**.
6. Selecione **OK**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora crie a carga de trabalho falada e as máquinas virtuais no local, e coloque-as nas subredes apropriadas.

### <a name="create-the-workload-virtual-machine"></a>Criar a máquina virtual de carga de trabalho

Crie uma máquina virtual na rede virtual de porta-voz, funcionando o IIS, sem endereço IP público.

1. A partir da página inicial do portal Azure, selecione **Criar um recurso**.
2. Em **Popular**, selecione **Windows Server 2016 Datacenter**.
3. Introduza estes valores para a máquina virtual:
    - **Grupo de recursos** - Selecione **FW-Hybrid-Test**.
    - **Nome da máquina virtual**: *VM-Spoke-01*.
    - **Região** - *(EUA) Leste dos EUA.*
    - **Nome do utilizador**: *azureuser*.
    - **Palavra-passe**: escreva a sua palavra-passe

4. Selecione **Next:Disks**.
5. Aceite as predefinições e selecione **Seguinte: Networking**.
6. Selecione **VNet-Spoke** para a rede virtual e a sub-rede é **SN-Workload**.
7. Para **IP público,** selecione **Nenhum**.
8. Para **as portas de entrada pública,** selecione Permitir portas **selecionadas,** e, em seguida, selecionar **HTTP (80)** e **RDP (3389)**
9. Selecione **Next:Management**.
10. Para **diagnósticos boot,** selecione **Off**.
11. Selecione **Review+Create,** reveja as definições na página resumo e, em seguida, **selecione Criar**.

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

### <a name="create-the-on-premises-virtual-machine"></a>Criar a máquina virtual no local

Esta é uma máquina virtual que utiliza para ligar utilizando o Remote Desktop ao endereço IP público. A partir daí, liga-se ao servidor no local através da firewall.

1. A partir da página inicial do portal Azure, selecione **Criar um recurso**.
2. Em **Popular**, selecione **Windows Server 2016 Datacenter**.
3. Introduza estes valores para a máquina virtual:
    - **Grupo de recursos** - Selecione existente e, em seguida, selecione **FW-Hybrid-Test**.
    - **Nome** - de máquina virtual*VM-Onprem*.
    - **Região** - *(EUA) Leste dos EUA.*
    - **Nome do utilizador**: *azureuser*.
    - **Palavra-passe**: digite a sua palavra-passe.

4. Selecione **Next:Disks**.
5. Aceite as predefinições e selecione **Next:Networking**.
6. Selecione **VNet-Onprem** para rede virtual e verifique se a sub-rede é **SN-Corp**.
7. Para **as portas de entrada pública,** selecione Permitir portas **selecionadas,** e, em seguida, selecione **RDP (3389)**
8. Selecione **Next:Management**.
9. Para **diagnósticos boot,** selecione **Off**.
10. Selecione **Review+Create,** reveja as definições na página resumo e, em seguida, **selecione Criar**.

## <a name="test-the-firewall"></a>Testar a firewall

1. Em primeiro lugar, note o endereço IP privado para a máquina virtual VM-Spoke-01 na página geral vM-Spoke-01.

2. No portal do Azure, ligue à máquina virtual **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Abra um navegador web no **VM-Onprem**\<e navegue para http://\>IP privado vM-spoke-01 .

   Você deve ver a página web **VM-spoke-01:** ![VM-Spoke-01 página web](media/secure-hybrid-network/vm-spoke-01-web.png)

4. A partir da máquina virtual **VM-Onprem,** abra um ambiente de trabalho remoto para **VM-spoke-01** no endereço IP privado.

   A sua ligação deve ter sucesso, e deve poder assinar.

Então agora verificou que as regras da firewall estão funcionando:

<!---- You can ping the server on the spoke VNet.--->
- Pode navegar no servidor web na rede virtual falada.
- Pode ligar-se ao servidor na rede virtual falada utilizando RDP.

Em seguida, altere a ação das coleções de regras de rede da firewall para **Negar**, para verificar se as regras de firewall funcionam conforme esperado.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione a política de firewall **Pol-Net01.**
2. Em **Definições,** selecione **Regras**.
3. De acordo com **as regras da Rede,** selecione a coleção de regras **RCNet01,** selecione as elipses (...), e selecione **Editar**.
4. Para **a ação de recolha de regras,** selecione **Deny**.
5. Selecione **Guardar**.

Feche os desktops e navegadores remotos existentes no **VM-Onprem** antes de testar as regras alteradas. Depois de a atualização da recolha de regras estar completa, volte a fazer os testes. Desta vez, devem falhar todos.

## <a name="clean-up-resources"></a>Limpar recursos

Pode manter os recursos da firewall para o próximo tutorial. Se já não precisar dos mesmos, elimine o grupo de recursos **FW-Hybrid-Test** para eliminar todos os recursos relacionados com a firewall.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Proteja o seu WAN virtual utilizando a pré-visualização do Azure Firewall Manager](secure-cloud-network.md)