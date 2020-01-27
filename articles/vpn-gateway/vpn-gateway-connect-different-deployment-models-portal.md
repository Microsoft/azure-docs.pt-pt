---
title: 'Conectar redes virtuais clássicas a Azure Resource Manager VNets: Portal | Microsoft Docs'
description: Etapas para conectar o VNets clássico ao Gerenciador de recursos VNets usando o gateway de VPN e o portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: c26c4c47cb17acf88bc545af3a1fc979138d56b1
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951739"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Conectar redes virtuais de diferentes modelos de implantação usando o portal

Este artigo mostra como conectar o VNets clássico ao VNets do Gerenciador de recursos para permitir que os recursos localizados nos modelos de implantação separados se comuniquem entre si. As etapas neste artigo usam principalmente o portal do Azure, mas você também pode criar essa configuração usando o PowerShell selecionando o artigo na lista.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

A conexão de uma vnet clássica a uma VNet do Resource Manager é semelhante à conexão de uma VNet a um site local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE. Você pode criar uma conexão entre VNets que estão em assinaturas diferentes e em regiões diferentes. Você também pode conectar VNets que já têm conexões com redes locais, desde que o gateway com o qual foram configuradas seja dinâmico ou baseado em rota. Para obter mais informações sobre ligações de VNet a VNet, consulte [FAQ sobre VNet para VNet](#faq) no final deste artigo. 

Se você ainda não tiver um gateway de rede virtual e não quiser criar um, convém considerar a conexão do VNets usando o emparelhamento VNet. O VNet peering não utiliza um gateway de VPN. Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

### <a name="before"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Essas etapas pressupõem que ambas as VNets já tenham sido criadas. Se você estiver usando este artigo como um exercício e não tiver o VNets, há links nas etapas para ajudá-lo a criá-los.
* Verifique se os intervalos de endereços para os VNets não se sobrepõem entre si ou sobreponham-se com qualquer um dos intervalos para outras conexões às quais os gateways podem estar conectados.
* Instale os cmdlets do PowerShell mais recentes para o Gerenciador de recursos e o gerenciamento de serviços (clássico). Neste artigo, usamos o portal do Azure e o PowerShell. O PowerShell é necessário para criar a conexão da VNet clássica com a VNet do Resource Manager. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview). 

### <a name="values"></a>Definições de exemplo

Pode utilizar estes valores para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo.

**VNet clássica**

Nome da VNet = ClassicVNet <br>
Espaço de endereço = 10.0.0.0/24 <br>
Nome da sub-rede = Subnet-1 <br>
Intervalo de endereços de sub-rede = 10.0.0.0/27 <br>
Assinatura = a assinatura que você deseja usar <br>
Grupo de recursos = ClassicRG <br>
Localização = oeste dos EUA <br>
GatewaySubnet = 10.0.0.32/28 <br>
Site local = RMVNetLocal <br>

**VNet do Resource Manager**

Nome da VNet = RMVNet <br>
Espaço de endereço = 192.168.0.0/16 <br>
Grupo de recursos = RG1 <br>
Localização = leste dos EUA <br>
Nome da sub-rede = Subnet-1 <br>
Intervalo de endereços = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Nome do gateway de rede virtual = RMGateway <br>
Tipo de gateway = VPN <br>
Tipo de VPN = baseado em rota <br>
SKU = VpnGw1 <br>
Localização = leste dos EUA <br>
Rede virtual = RMVNet <br> (associar o gateway de VPN a esta VNet) Primeira configuração de IP = rmgwpip <br> (endereço IP público do gateway) Gateway de rede local = ClassicVNetLocal <br>
Nome da conexão = RMtoClassic

### <a name="connectoverview"></a>Visão geral da conexão

Para essa configuração, você cria uma conexão de gateway de VPN em um túnel VPN IPsec/IKE entre as redes virtuais. Certifique-se de que nenhum dos seus intervalos de VNet se sobreponham entre si ou com qualquer uma das redes locais às quais eles se conectam.

A tabela a seguir mostra um exemplo de como o exemplo VNets e sites locais são definidos:

| Rede Virtual | Espaço de Endereços | Região | Conecta-se ao site de rede local |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |E.U.A. Oeste | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |E.U.A. Leste |ClassicVNetLocal (10.0.0.0/24) |

## <a name="classicvnet"></a>Seção 1-definir as configurações da VNet clássica

Nesta seção, você cria a VNet clássica, a rede local (site local) e o gateway de rede virtual. As capturas de ecrã são disponibilizadas como exemplos. Certifique-se de substituir os valores pelos seus próprios ou use os valores de [exemplo](#values) .

### 1. <a name="classicvnet"> </a>criar uma VNet clássica

Se você não tiver uma VNet clássica e estiver executando estas etapas como um exercício, poderá criar uma VNet usando [Este artigo](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e os valores de configurações de [exemplo](#values) acima.

Se você já tiver uma VNet com um gateway de VPN, verifique se o gateway é dinâmico. Se for estático, primeiro você deve excluir o gateway de VPN antes de prosseguir com a [configuração do site local](#local).

1. Abra ao [portal do Azure](https://ms.portal.azure.com) e inicie sessão com a sua conta do Azure.
2. Clique em **+ criar um recurso** para abrir a página ' novo '.
3. No campo ' Pesquisar no Marketplace ', digite ' rede virtual '. Se, em vez disso, selecione rede-> redes virtuais, você não terá a opção de criar uma VNet clássica.
4. Localize ' rede virtual ' na lista retornada e clique nela para abrir a página rede virtual. 
5. Na página rede virtual, selecione "clássico" para criar uma VNet clássica. Se você usar o padrão aqui, você será levado com uma VNet do Resource Manager em vez disso.

### 2. <a name="local"> </a>configurar o site local

1. Navegue até **todos os recursos** e localize o **ClassicVNet** na lista.
2. Clique em **Gateway** na seção **configurações** do menu e, em seguida, clique na faixa para criar um gateway.
  ![Configurar um gateway de VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Configurar um gateway de VPN ")
3. Na página **nova conexão VPN** , para **tipo de conexão**, selecione **site a site**.
4. Para **site local**, clique em **definir configurações necessárias**. Isso abre a página **site local** .
5. Na página **site local** , crie um nome para se referir à VNet do Resource Manager. Por exemplo, ' RMVNetLocal '.
6. Se o gateway de VPN para a VNet do Resource Manager já tiver um endereço IP público, use o valor para o campo **endereço IP do gateway de VPN** . Se você estiver realizando essas etapas como um exercício ou ainda não tiver um gateway de rede virtual para sua VNet do Resource Manager, você poderá criar um endereço IP de espaço reservado. Verifique se o endereço IP do espaço reservado usa um formato válido. Posteriormente, você substituirá o endereço IP do espaço reservado pelo endereço IP público do gateway de rede virtual do Resource Manager.
7. Para **espaço de endereço de cliente**, use os [valores](#connectoverview) para os espaços de endereço IP da rede virtual para a VNet do Resource Manager. Essa configuração é usada para especificar os espaços de endereço para rotear para a rede virtual do Resource Manager. No exemplo, usamos 192.168.0.0/16, o intervalo de endereços para o RMVNet.
8. Clique em **OK** para salvar os valores e retornar para a página **nova conexão VPN** .

### <a name="classicgw"></a>3. criar o gateway de rede virtual

1. Na página **nova conexão VPN** , marque a caixa de seleção **criar gateway imediatamente** .
2. Clique em **Configuração do gateway opcional** para abrir a página **Configuração do gateway**.

   ![Abrir a página de configuração do gateway](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Abrir a página de configuração do gateway")
3. Clique em **sub-rede-definir configurações necessárias** para abrir a página **Adicionar sub-rede** . O **nome** já está configurado com o valor necessário: **GatewaySubnet**.
4. O **intervalo de endereços** refere-se ao intervalo da sub-rede de gateway. Embora seja possível criar uma sub-rede de gateway com um intervalo de endereços/29 (3 endereços), é recomendável criar uma sub-rede de gateway que contenha mais endereços IP. Isso irá acomodar configurações futuras que podem exigir mais endereços IP disponíveis. Se possível, use/27 ou/28. Se você estiver usando essas etapas como um exercício, poderá consultar os valores de [exemplo](#values). Para este exemplo, usamos ' 10.0.0.32/28 '. Clique em **OK** para criar a sub-rede de gateway.
5. Na página **configuração do gateway** , **tamanho** refere-se ao SKU do gateway. Selecione o SKU de gateway para seu gateway de VPN.
6. Verifique se o **tipo de roteamento** é **dinâmico**e clique em **OK** para retornar à página **nova conexão VPN** .
7. Na página **nova conexão VPN** , clique em **OK** para começar a criar seu gateway de VPN. A criação de um gateway de VPN pode demorar até 45 minutos a concluir.

### <a name="ip"></a>4. copiar o endereço IP público do gateway de rede virtual

Depois que o gateway de rede virtual tiver sido criado, você poderá exibir o endereço IP do gateway. 

1. Navegue até sua VNet clássica e clique em **visão geral**.
2. Clique em **conexões VPN** para abrir a página conexões VPN. Na página conexões VPN, você pode exibir o endereço IP público. Esse é o endereço IP público atribuído ao seu gateway de rede virtual. Anote o endereço IP. Você o usará em etapas posteriores quando trabalhar com as definições de configuração do gateway de rede local do Resource Manager. 
3. Você pode exibir o status de suas conexões de gateway. Observe que o site de rede local que você criou está listado como ' conectando '. O status será alterado depois que você tiver criado suas conexões. Você pode fechar esta página quando terminar de exibir o status.

## <a name="rmvnet"></a>Seção 2 – definir as configurações de VNet do Resource Manager

Nesta seção, você cria o gateway de rede virtual e o gateway de rede local para sua VNet do Resource Manager. As capturas de ecrã são disponibilizadas como exemplos. Certifique-se de substituir os valores pelos seus próprios ou use os valores de [exemplo](#values) .

### <a name="1-create-a-virtual-network"></a>1. criar uma rede virtual

**Valores de exemplo:**

* Nome da VNet = RMVNet <br>
* Espaço de endereço = 192.168.0.0/16 <br>
* Grupo de recursos = RG1 <br>
* Localização = leste dos EUA <br>
* Nome da sub-rede = Subnet-1 <br>
* Intervalo de endereços = 192.168.1.0/24 <br>

Se você não tiver uma VNet do Resource Manager e estiver executando estas etapas como um exercício, crie uma rede virtual com as etapas em [criar uma rede virtual](../virtual-network/quick-create-portal.md), usando os valores de exemplo.

### <a name="creategw"></a>2. criar um gateway de rede virtual

Neste passo, vai criar o gateway de rede virtual da VNet. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

**Valores de exemplo:**

* Nome do gateway de rede virtual = RMGateway <br>
* Tipo de gateway = VPN <br>
* Tipo de VPN = baseado em rota <br>
* SKU = VpnGw1 <br>
* Localização = leste dos EUA <br>
* Rede virtual = RMVNet <br>
* GatewaySubnet = 192.168.0.0/26 <br>
* Primeira configuração de IP = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="createlng"></a>3. criar um gateway de rede local

**Valores de exemplo:** Gateway de rede local = ClassicVNetLocal

| Rede Virtual | Espaço de Endereços | Região | Conecta-se ao site de rede local |Endereço IP público do gateway|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |E.U.A. Oeste | RMVNetLocal (192.168.0.0/16) |O endereço IP público que é atribuído ao gateway ClassicVNet|
| RMVNet | (192.168.0.0/16) |E.U.A. Leste |ClassicVNetLocal (10.0.0.0/24) |O endereço IP público que é atribuído ao gateway RMVNet.|

O gateway de rede local especifica o intervalo de endereços e o endereço IP público associado à sua VNet clássica e seu gateway de rede virtual. Se você estiver seguindo estas etapas como um exercício, consulte os valores de exemplo.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>Seção 3-modificar as configurações do site local da VNet clássica

Nesta seção, você substituirá o endereço IP do espaço reservado que você usou ao especificar as configurações do site local, com o endereço IP do gateway de VPN do Resource Manager. Esta seção usa os cmdlets do PowerShell (SM) clássicos.

1. Na portal do Azure, navegue até a rede virtual clássica.
2. Na página de sua rede virtual, clique em **visão geral**.
3. Na seção **conexões VPN** , clique no nome do site local no gráfico.

   ![Conexões VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "Ligações VPN")
4. Na página **conexões VPN site a site** , clique no nome do site.

   ![Nome do site](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Nome do site local")
5. Na página conexão do site local, clique no nome do site local para abrir a página do **site local** .

   ![Site local aberto](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Abrir site local")
6. Na página **site local** , substitua o **endereço IP do gateway de VPN** pelo endereço IP do gateway do Gerenciador de recursos.

   ![Gateway-IP-address](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Endereço IP do gateway")
7. Clique em **OK** para atualizar o endereço IP.

## <a name="RMtoclassic"></a>Seção 4 – criar o Gerenciador de recursos para a conexão clássica

Nestas etapas, você configura a conexão da VNet do Resource Manager com a VNet clássica usando o portal do Azure.

1. Em **todos os recursos**, localize o gateway de rede local. Em nosso exemplo, o gateway de rede local é **ClassicVNetLocal**.
2. Clique em **configuração** e verifique se o valor do endereço IP é o gateway de VPN para a VNet clássica. Atualize, se necessário, e clique em **salvar**. Feche a página.
3. Em **todos os recursos**, clique no gateway de rede local.
4. Clique em **conexões** para abrir a página conexões.
5. Na página **conexões** , clique em **+** para adicionar uma conexão.
6. Na página **Adicionar conexão** , nomeie a conexão. Por exemplo, ' RMtoClassic '.
7. **Site a site** já está selecionado nesta página.
8. Selecione o gateway de rede virtual que você deseja associar a este site.
9. Crie uma **chave compartilhada**. Essa chave também é usada na conexão que você cria da VNet clássica para a VNet do Resource Manager. Você pode gerar a chave ou criar uma. Em nosso exemplo, usamos ' abc123 ', mas você pode (e deve) usar algo mais complexo.
10. Clique em **OK** para criar a conexão.

## <a name="classictoRM"></a>Seção 5-criar uma conexão clássica com o Gerenciador de recursos

Nestas etapas, você configura a conexão da VNet clássica com a VNet do Resource Manager. Essas etapas exigem o PowerShell. Você não pode criar essa conexão no Portal. Verifique se você baixou e instalou os cmdlets do PowerShell clássico (SM) e do Gerenciador de recursos (RM).

### <a name="1-connect-to-your-azure-account"></a>1. conectar-se à sua conta do Azure

Abra o console do PowerShell com direitos elevados e faça logon em sua conta do Azure. Depois de fazer logon, as configurações de conta são baixadas para que estejam disponíveis para Azure PowerShell. O cmdlet a seguir solicita as credenciais de logon para sua conta do Azure para o modelo de implantação do Gerenciador de recursos:

```powershell
Connect-AzAccount
```

Obtenha uma lista das suas subscrições do Azure.

```powershell
Get-AzSubscription
```

Se você tiver mais de uma assinatura, especifique a assinatura que deseja usar.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

Em seguida, faça logon para usar os cmdlets clássicos do PowerShell (gerenciamento de serviços). Use o seguinte comando para adicionar sua conta do Azure para o modelo de implantação clássico:

```powershell
Add-AzureAccount
```

Obtenha uma lista de suas assinaturas. Essa etapa pode ser necessária ao adicionar os cmdlets de gerenciamento de serviços, dependendo da instalação do módulo do Azure.

```powershell
Get-AzureSubscription
```

Se você tiver mais de uma assinatura, especifique a assinatura que deseja usar.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. exibir os valores do arquivo de configuração de rede

Quando você cria uma VNet no portal do Azure, o nome completo que o Azure usa não é visível no portal do Azure. Por exemplo, uma VNet que parece ser denominada ' ClassicVNet ' no portal do Azure pode ter um nome muito mais longo no arquivo de configuração de rede. O nome pode ser semelhante a: ' Group ClassicRG ClassicVNet '. Nestas etapas, você baixa o arquivo de configuração de rede e exibe os valores.

Crie um diretório no seu computador e, em seguida, exporte o ficheiro de configuração de rede para o diretório. Neste exemplo, o ficheiro de configuração de rede é exportado para C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Abra o arquivo com um editor de texto e exiba o nome da sua VNet clássica. Use os nomes no arquivo de configuração de rede ao executar os cmdlets do PowerShell.

- Os nomes de VNet são listados como **VirtualNetworkSite Name =**
- Os nomes de site são listados como **LocalNetworkSite Name =**

### <a name="3-create-the-connection"></a>3. criar a conexão

Defina a chave compartilhada e crie a conexão da VNet clássica com a VNet do Resource Manager. Você não pode definir a chave compartilhada usando o Portal. Certifique-se de executar essas etapas enquanto estiver conectado usando a versão clássica dos cmdlets do PowerShell. Para fazer isso, use **Add-AzureAccount**. Caso contrário, você não poderá definir '-AzureVNetGatewayKey '.

- Neste exemplo, **-VNetName** é o nome da VNet clássica como encontrada no arquivo de configuração de rede. 
- O **-LocalNetworkSiteName** é o nome que você especificou para o site local, conforme encontrado no arquivo de configuração de rede.
- O **-SharedKey** é um valor que você gera e especifica. Para este exemplo, usamos *abc123*, mas você pode gerar algo mais complexo. O importante é que o valor especificado aqui deve ser o mesmo valor que você especificou ao criar seu Gerenciador de recursos para a conexão clássica.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="verify"></a>Seção 6-verificar suas conexões

Você pode verificar suas conexões usando o portal do Azure ou o PowerShell. Ao verificar, talvez seja necessário aguardar um minuto ou dois enquanto a conexão está sendo criada. Quando uma conexão é bem-sucedida, o estado de conectividade muda de "conectando" para "conectado".

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Para verificar a conexão de sua VNet clássica com sua VNet do Resource Manager

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Para verificar a conexão de sua VNet do Resource Manager com sua VNet clássica

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>FAQ da ligação VNet a VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
