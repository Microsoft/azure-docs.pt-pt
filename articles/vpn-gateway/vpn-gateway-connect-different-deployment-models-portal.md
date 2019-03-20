---
title: 'Ligar redes virtuais clássicas a VNets do Resource Manager do Azure: Portal | Microsoft Docs'
description: Passos para ligar VNets clássicas a VNets do Resource Manager com o Gateway de VPN e o portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: bf7d80bbbe63204cda47719a7d7c019013ad800b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099176"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Ligar redes virtuais a partir de modelos de implementação diferentes com o portal

Este artigo mostra-lhe como ligar VNets clássicas a VNets do Resource Manager para permitir que os recursos localizados nos modelos de implantação à parte para comunicar entre si. Os passos neste artigo utilizam principalmente o portal do Azure, mas também pode criar esta configuração com o PowerShell, selecionando o artigo desta lista.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Ligar uma VNet clássica a uma VNet do Resource Manager é semelhante à ligação VNet a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE. Pode criar uma ligação entre VNets que estão em subscrições diferentes e em regiões diferentes. Também pode ligar VNets que já tenham ligações a redes no local, desde que o gateway que tenham sido configuradas com é dinâmico ou baseado na rota. Para obter mais informações sobre ligações de VNet a VNet, consulte [FAQ sobre VNet para VNet](#faq) no final deste artigo. 

Se ainda não tiver um gateway de rede virtual e não pretender criar uma, pode querer em vez disso, considere ligar às VNets utilização o VNet Peering. O VNet peering não utiliza um gateway de VPN. Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

### <a name="before"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Estes passos partem do princípio de que ambas as VNets já foram criadas. Se estiver a utilizar este artigo como um exercício e não tiver VNets, existem ligações nos passos que o ajudarão a criá-los.
* Certifique-se de que os intervalos de endereços para as VNets não se sobrepõem entre si, ou se sobrepõe a qualquer um dos intervalos de outras ligações que os gateways podem ser ligados a.
* Instale os cmdlets do PowerShell mais recente para o Resource Manager e gestão de serviço (clássico). Neste artigo, vamos utilizar o portal do Azure e o PowerShell. PowerShell é necessário para criar a ligação a partir da VNet clássica para a VNet do Resource Manager. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview). 

### <a name="values"></a>Definições de exemplo

Pode utilizar estes valores para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo.

**Rede virtual clássica**

Nome da VNet = ClassicVNet <br>
Espaço de endereços = 10.0.0.0/24 <br>
Nome da sub-rede = Subnet-1 <br>
Intervalo de endereços da sub-rede = 10.0.0.0/27 <br>
Subscrição = a subscrição que pretende utilizar <br>
Grupo de recursos = ClassicRG <br>
Localização = E.U.A. oeste <br>
GatewaySubnet = 10.0.0.32/28 <br>
Local site = RMVNetLocal <br>

**VNet do Resource Manager**

Nome da VNet = RMVNet <br>
Espaço de endereços = 192.168.0.0/16 <br>
Grupo de recursos = RG1 <br>
Localização = E.U.A. leste <br>
Nome da sub-rede = Subnet-1 <br>
Intervalo de endereços = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Nome do gateway de rede virtual = RMGateway <br>
Tipo de gateway de VPN de = <br>
Tipo de VPN = baseado na rota <br>
SKU = VpnGw1 <br>
Localização = E.U.A. leste <br>
Rede virtual = RMVNet <br> (associar o gateway de VPN para esta VNet) Configuração do primeiro IP = rmgwpip <br> (endereço público do gateway IP) Gateway de rede local = ClassicVNetLocal <br>
Nome da ligação = RMtoClassic

### <a name="connectoverview"></a>Descrição geral da ligação

Para esta configuração, crie uma ligação de gateway VPN através de um túnel VPN IPsec/IKE entre as redes virtuais. Certifique-se de que nenhum dos seus intervalos de VNet se sobrepõem entre si, ou com qualquer uma das redes locais que se ligam ao.

A tabela seguinte mostra um exemplo de como o exemplo VNets e os sites locais são definidas:

| Rede Virtual | Espaço de Endereços | Região | Liga-se ao site de rede local |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |EUA Oeste | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |EUA Leste |ClassicVNetLocal (10.0.0.0/24) |

## <a name="classicvnet"></a>Secção 1 - configurar as definições da VNet clássicas

Nesta secção, vai criar a VNet clássica, o local de rede (local site) e o gateway de rede virtual. As capturas de ecrã são disponibilizadas como exemplos. Certifique-se de que substitui os valores pelos seus próprios ou utilizar o [exemplo](#values) valores.

### 1. <a name="classicvnet"></a>Criar uma VNet clássica

Se não tiver uma VNet clássica e está a executar estes passos como um exercício, pode criar uma VNet utilizando [este artigo](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e o [exemplo](#values) valores de definições acima.

Se já tiver uma VNet com um gateway VPN, certifique-se de que o gateway é dinâmico. Se for estático, tem primeiro de eliminar o gateway de VPN antes de avançar para [configurar o site local](#local).

1. Abra ao [portal do Azure](https://ms.portal.azure.com) e inicie sessão com a sua conta do Azure.
2. Clique em **+ criar um recurso** para abrir a página de "Novo".
3. No campo "Pesquisar no marketplace", escreva "Rede Virtual". Se selecionou em vez disso, o sistema de rede -> rede Virtual, não obterá a opção para criar uma VNet clássica.
4. Localize "Rede Virtual" na lista devolvida e clique para abrir a página de rede Virtual. 
5. Na página de rede virtual, selecione "Clássico" para criar uma VNet clássica. Se usar o padrão aqui, faça isso com uma VNet do Resource Manager em vez disso.

### 2. <a name="local"></a>Configurar o site local

1. Navegue para **todos os recursos** e localize a **ClassicVNet** na lista.
2. No **descrição geral** página, além do **ligações VPN** secção, clique em **Gateway** para criar um gateway.
  ![Configurar um gateway de VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "configurar um gateway de VPN")
3. Sobre o **nova ligação VPN** página, para **tipo de ligação**, selecione **Site a site**.
4. Para **Local site**, clique em **configurar definições necessárias**. Esta ação abre o **Local site** página.
5. Sobre o **Local site** página, criar um nome para fazer referência à VNet do Resource Manager. Por exemplo, 'RMVNetLocal'.
6. Se o gateway de VPN para a VNet do Resource Manager já tem um endereço IP público, utilize o valor para o **endereço IP do gateway de VPN** campo. Se estiver a seguir estes passos como um exercício, ou ainda não tiver um gateway de rede virtual para a sua VNet do Resource Manager, pode compor um endereço IP do marcador de posição. Certifique-se de que o endereço IP do marcador de posição usa um formato válido. Mais tarde, substituir o endereço IP do marcador de posição pelo endereço IP público do gateway de rede virtual do Resource Manager.
7. Para **espaço de endereços de cliente**, utilize o [valores](#connectoverview) para o IP de rede virtual espaços de endereços para a VNet do Resource Manager. Esta definição é utilizada para especificar os espaços de endereços para encaminhar para a rede virtual do Resource Manager. No exemplo, utilizamos 192.168.0.0/16, o intervalo de endereços para o RMVNet.
8. Clique em **OK** para guardar os valores e voltar para o **nova ligação VPN** página.

### <a name="classicgw"></a>3. Criar o gateway de rede virtual

1. Sobre o **nova ligação VPN** página, selecione a **criar gateway de imediato** caixa de verificação.
2. Clique em **Configuração do gateway opcional** para abrir a página **Configuração do gateway**.

   ![Página de configuração do Open gateway](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "página de configuração do gateway aberto")
3. Clique em **sub-rede - configurar definições necessárias** para abrir o **adicionar sub-rede** página. O **nome** já está configurada com o valor necessário: **GatewaySubnet**.
4. O **intervalo de endereços** refere-se para o intervalo para a sub-rede do gateway. Embora seja possível criar uma sub-rede de gateway com/29 (3 endereços), de intervalo de endereços, recomendamos a criação de uma sub-rede de gateway que contém mais endereços IP. Isto irá acomodar configurações futuras que possam precisar de endereços IP mais disponíveis. Se possível, utilize/27 ou /28. Se estiver a utilizar estes passos como um exercício, pode consultar o [valores de exemplo](#values). Neste exemplo, vamos utilizar '10.0.0.32/28'. Clique em **OK** para criar a sub-rede do gateway.
5. Sobre o **configuração do Gateway** página, **tamanho** refere-se para o SKU de gateway. Selecione o SKU de gateway para o seu gateway VPN.
6. Certifique-se do **tipo de encaminhamento** é **dinâmico**, em seguida, clique em **OK** para regressar ao **nova ligação VPN** página.
7. Sobre o **nova ligação VPN** página, clique em **OK** para começar a criar o gateway de VPN. A criação de um gateway de VPN pode demorar até 45 minutos a concluir.

### <a name="ip"></a>4. Copie o endereço IP público do gateway de rede virtual

Depois do gateway de rede virtual tiver sido criado, pode ver o endereço IP do gateway. 

1. Navegue para a VNet clássica e clique em **descrição geral**.
2. Clique em **ligações VPN** para abrir a página de ligações de VPN. Na página de ligações VPN, pode ver o endereço IP público. Este é o endereço de IP público atribuído ao gateway de rede virtual. Tome nota do endereço IP. Utilize-lo em passos posteriores quando trabalha com as definições de configuração do gateway de rede local do Resource Manager. 
3. Pode ver o estado das suas ligações de gateway. Observe que o site de rede local que criou é listado como "A ligar". O estado será alterado depois de criar as suas ligações. Pode fechar esta página quando terminar de visualizar o estado.

## <a name="rmvnet"></a>Secção 2 - configurar as definições da VNet do Resource Manager

Nesta secção, vai criar o gateway de rede virtual e o gateway de rede local para a sua VNet do Resource Manager. As capturas de ecrã são disponibilizadas como exemplos. Certifique-se de que substitui os valores pelos seus próprios ou utilizar o [exemplo](#values) valores.

### <a name="1-create-a-virtual-network"></a>1. Criar uma rede virtual

**Valores de exemplo:**

* Nome da VNet = RMVNet <br>
* Espaço de endereços = 192.168.0.0/16 <br>
* Grupo de recursos = RG1 <br>
* Localização = E.U.A. leste <br>
* Nome da sub-rede = Subnet-1 <br>
* Intervalo de endereços = 192.168.1.0/24 <br>


Se não tiver uma VNet do Resource Manager e está a executar estes passos como um exercício, criar uma rede virtual com os passos em [criar uma rede virtual](../virtual-network/quick-create-portal.md), utilizando os valores de exemplo.

### <a name="2-create-a-gateway-subnet"></a>2. Criar uma sub-rede do gateway

**Valor de exemplo:** GatewaySubnet = 192.168.0.0/26

Antes de criar um gateway de rede virtual, tem primeiro de criar a sub-rede do gateway. Criar uma sub-rede de gateway com contagem CIDR de/28 ou superior (/ 27, / 26, etc.). Se estiver a criar isso como parte de um exercício, pode utilizar os valores de exemplo.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="creategw"></a>3. Criar um gateway de rede virtual

**Valores de exemplo:**

* Nome do gateway de rede virtual = RMGateway <br>
* Tipo de gateway de VPN de = <br>
* Tipo de VPN = baseado na rota <br>
* SKU = VpnGw1 <br>
* Localização = E.U.A. leste <br>
* Rede virtual = RMVNet <br>
* Configuração do primeiro IP = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="createlng"></a>4. Criar um gateway de rede local

**Valores de exemplo:** Gateway de rede local = ClassicVNetLocal

| Rede Virtual | Espaço de Endereços | Região | Liga-se ao site de rede local |Endereço IP público do gateway|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |EUA Oeste | RMVNetLocal (192.168.0.0/16) |O endereço IP público atribuído ao ClassicVNet gateway|
| RMVNet | (192.168.0.0/16) |EUA Leste |ClassicVNetLocal (10.0.0.0/24) |O endereço IP público atribuído ao RMVNet gateway.|

O gateway de rede local Especifica o intervalo de endereços e o endereço IP público associado à sua VNet clássica e o seu gateway de rede virtual. Se estiver fazendo estes passos como um exercício, veja os valores de exemplo.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>Secção 3 - modifique as definições de local site VNet clássicas

Nesta secção, substitua o endereço IP do marcador de posição que utilizou ao especificar as definições de local site, com o endereço IP do gateway de VPN do Resource Manager. Esta secção utiliza os cmdlets do PowerShell (SM) clássicos.

1. No portal do Azure, navegue para a rede virtual clássica.
2. Na página da sua rede virtual, clique em **descrição geral**.
3. Na **ligações VPN** secção, clique no nome do seu site local no gráfico.

   ![Ligações de VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "ligações VPN")
4. Sobre o **conexões VPN de Site a site** página, clique no nome do site.

   ![Nome do site](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "nome do Local site")
5. Na página de ligação para o seu site local, clique no nome do site local para abrir o **Local site** página.

   ![Site-local-aberto](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Abrir site local")
6. Sobre o **Local site** página, substitua a **endereço IP do gateway de VPN** com o endereço IP do gateway do Resource Manager.

   ![Endereço de ip de gateway](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "endereço IP do Gateway")
7. Clique em **OK** para atualizar o endereço IP.

## <a name="RMtoclassic"></a>Secção 4 - criar o Resource Manager para ligação clássica

Estes passos, irá configurar a ligação da VNet do Resource Manager para a VNet clássica no portal do Azure.

1. Na **todos os recursos**, localize o gateway de rede local. No nosso exemplo, o gateway de rede local for **ClassicVNetLocal**.
2. Clique em **configuração** e certifique-se de que o valor do endereço IP é o gateway VPN para a VNet clássica. Atualizar, se necessário, em seguida, clique em **guardar**. Feche a página.
3. Na **todos os recursos**, clique no gateway de rede local.
4. Clique em **ligações** para abrir a página de ligações.
5. Sobre o **conexões** página, clique em **+** para adicionar uma ligação.
6. Sobre o **adicionar ligação** página, dê o nome da ligação. Por exemplo, 'RMtoClassic'.
7. **Site-Site** já está selecionado nesta página.
8. Selecione o gateway de rede virtual que pretende associar este site.
9. Criar uma **chave partilhada**. Esta chave também é usada na ligação que criou a VNet clássica para a VNet do Resource Manager. Pode gerar a chave ou criar uma cópia de segurança. No nosso exemplo, utilizamos "abc123", mas pode e (deve) utilizar algo mais complexo.
10. Clique em **OK** para criar a ligação.

## <a name="classictoRM"></a>Secção 5 - criar a implementação clássica para Resource Manager ligação

Estes passos, irá configurar a ligação entre a VNet clássica para a VNet do Resource Manager. Estes passos requerem o PowerShell. Não é possível criar esta ligação no portal. Certifique-se de que transferiu e instalou o clássico (SM) e os cmdlets do PowerShell do Gestor de recursos (RM).

### <a name="1-connect-to-your-azure-account"></a>1. Ligar à sua conta do Azure

Abra a consola do PowerShell com direitos elevados e inicie sessão sua conta do Azure. Após iniciar sessão, as definições da conta são transferidas para que fiquem disponíveis para o Azure PowerShell. O seguinte cmdlet pede-lhe as credenciais de início de sessão para a sua conta do Azure para o modelo de implementação do Resource Manager:

```powershell
Connect-AzAccount
```

Obtenha uma lista das suas subscrições do Azure.

```powershell
Get-AzSubscription
```

Se tiver mais de uma subscrição, especifique a subscrição que pretende utilizar.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

Em seguida, inicie sessão para utilizar os cmdlets do PowerShell Clássicos (gestão de serviço). Utilize o seguinte comando para adicionar a sua conta do Azure para o modelo de implementação clássica:

```powershell
Add-AzureAccount
```

Obter uma lista das suas subscrições. Este passo pode ser necessário quando instalar os cmdlets de gestão do serviço, dependendo do seu módulo do Azure a adicionar.

```powershell
Get-AzureSubscription
```

Se tiver mais de uma subscrição, especifique a subscrição que pretende utilizar.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Ver os valores de ficheiro de configuração de rede

Quando cria uma VNet no portal do Azure, o nome completo, que utiliza o Azure não é visível no portal do Azure. Por exemplo, uma VNet que aparece para ter o nome "ClassicVNet" no portal do Azure pode ter um nome muito mais tempo no ficheiro de configuração de rede. O nome pode ser algo como: 'Grupo ClassicRG ClassicVNet'. Nestes passos, transfira o ficheiro de configuração de rede e visualizar os valores.

Crie um diretório no seu computador e, em seguida, exporte o ficheiro de configuração de rede para o diretório. Neste exemplo, o ficheiro de configuração de rede é exportado para C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Abra o ficheiro com um editor de texto e ver o nome para a sua VNet clássica. Utilize os nomes no ficheiro de configuração de rede ao executar os cmdlets do PowerShell.

- Nomes de VNet estão listados como **VirtualNetworkSite name =**
- Os nomes de site são apresentados como **LocalNetworkSite name =**

### <a name="3-create-the-connection"></a>3. Criar a ligação

Definir a chave partilhada e criar a ligação da VNet em modo de clássica para a VNet do Resource Manager. Não é possível definir a chave partilhada com o portal. Certifique-se de que executar estes passos, enquanto tem sessão iniciada sessão com a versão clássica dos cmdlets do PowerShell. Para tal, utilize **Add-AzureAccount**. Caso contrário, não será capaz de definir o "-AzureVNetGatewayKey'.

- Neste exemplo, **- VNetName** é o nome da VNet clássica como encontrada no ficheiro de configuração de rede. 
- O **- LocalNetworkSiteName** é o nome que especificou para o site local, como encontrado no ficheiro de configuração de rede.
- O **- SharedKey** é um valor que geram e especificar. Neste exemplo, utilizámos *abc123*, mas pode gerar algo mais complexo. O importante é que o valor que especificar aqui tem de ser o mesmo valor que especificou ao criar o seu Gestor de recursos para conexão clássico.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="verify"></a>Secção 6 - Verifique as suas ligações

Pode verificar as suas ligações com o portal do Azure ou o PowerShell. Durante a verificação, poderá ter de aguardar um minuto ou dois como a ligação está a ser criada. Quando uma ligação é bem-sucedida, o estado de conectividade é alterado de "A ligar" para "Ligado".

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Para verificar a ligação da sua VNet clássica a VNet do Resource Manager

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Para verificar a ligação da sua VNet do Resource Manager para a VNet clássica

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>FAQ da ligação VNet a VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
