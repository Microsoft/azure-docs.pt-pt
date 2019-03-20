---
title: 'Criar uma ligação entre VNets: clássico: Portal do Azure | Documentos da Microsoft'
description: Ligar redes virtuais do Azure em conjunto com o PowerShell e o portal do Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 6924d4eca52bfab8c90e7787bb8849b47df064db
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112267"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Configurar uma ligação de VNet a VNet (clássico)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Este artigo ajuda-o a criar uma ligação de gateway VPN entre redes virtuais. As redes virtuais podem estar nas mesmas regiões ou em regiões diferentes e pertencer às mesmas subscrições ou a subscrições diferentes. Os passos neste artigo aplicam-se para o modelo de implementação clássica e o portal do Azure. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [CLI do Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Ligue diferentes modelos de implementação - portal do Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Ligue diferentes modelos de implementação - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![VNet para VNet diagrama de conectividade](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

## <a name="about-vnet-to-vnet-connections"></a>Acerca das ligações VNet a VNet

Ligar uma rede virtual a outra rede virtual (VNet-para-VNet) no modelo de implementação clássica com um gateway de VPN é semelhante à ligação uma rede virtual a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE.

As VNets que liga podem estar em diferentes subscrições e regiões diferentes. Pode combinar a VNet para comunicação de VNet com configurações multilocal. Este procedimento permite-lhe estabelecer topologias de rede que combinam uma conetividade em vários locais com uma conetividade de rede intervirtual.

![Ligações VNet a VNet](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>Por que motivo ligar redes virtuais?

Poderá pretender ligar redes virtuais pelos seguintes motivos:

* **Geopresença e georredundância entre várias regiões**

  * Pode configurar a sua própria georreplicação ou sincronização com uma conetividade segura sem passar por pontos finais com acesso à Internet.
  * Com o Balanceador de carga do Azure e a Microsoft ou a tecnologia de clustering de terceiros, pode configurar-se a carga de trabalho de elevada disponibilidade com georredundância em várias regiões do Azure. Um exemplo importante consiste em configurar o SQL Always On com Grupos de Disponibilidade propagando-se em várias regiões do Azure.
* **Aplicações multicamadas regionais com isolamento**

  * Dentro da mesma região, pode configurar aplicações de várias camadas com várias VNets ligadas em conjunto com o isolamento forte e uma comunicação segura entre camadas.
* **Entre a subscrição, a comunicação entre organizações no Azure**

  * Se tiver várias subscrições do Azure, pode ligar as cargas de trabalho de diferentes subscrições em conjunto com segurança entre redes virtuais.
  * Para empresas ou fornecedores de serviços, pode ativar a comunicação entre organizações com tecnologia VPN segura do Azure.

Para obter mais informações sobre ligações de VNet a VNet, veja [Considerações de VNet a VNet](#faq) no final deste artigo.

### <a name="before-you-begin"></a>Antes de começar

Antes de iniciar este exercício, transfira e instale a versão mais recente dos cmdlets do PowerShell de gestão de serviço do Azure (SM). Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview). Podemos utilizar o portal para a maioria dos passos, mas é necessário utilizar o PowerShell para criar as ligações entre as VNets. Não é possível criar as ligações no portal do Azure.

## <a name="plan"></a>Passo 1 - Planear os intervalos de endereços IP

É importante decidir os intervalos que irá utilizar para configurar as suas redes virtuais. Para esta configuração, deve certificar-se de que nenhum dos seus intervalos de VNet se sobrepõem entre si, ou com qualquer uma das redes locais que se ligam ao.

A tabela seguinte mostra um exemplo de como definir as suas VNets. Utilize os intervalos de como uma diretriz apenas. Anote os intervalos para as suas redes virtuais. Estas informações são necessárias para os passos seguintes.

**Exemplo**

| Rede Virtual | Espaço de Endereços | Região | Liga-se ao site de rede local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |EUA Leste |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |EUA Oeste |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>Passo 2 - criar as redes virtuais

Criar duas redes virtuais na [portal do Azure](https://portal.azure.com). Para obter os passos criar redes virtuais clássicas, consulte [criar uma rede virtual clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Quando utilizar o portal para criar uma rede virtual clássica, terá de navegar para a página de rede virtual, utilizando os passos seguintes, caso contrário, não é apresentada a opção para criar uma rede virtual clássica:

1. Clique no '+' para abrir a página de "Novo".
2. No campo "Pesquisar no marketplace", escreva "Rede Virtual". Se selecionou em vez disso, o sistema de rede -> rede Virtual, não obterá a opção para criar uma VNet clássica.
3. Localize "Rede Virtual" na lista devolvida e clique para abrir a página de rede Virtual. 
4. Na página de rede virtual, selecione "Clássico" para criar uma VNet clássica. 

Se estiver a utilizar este artigo como um exercício, pode utilizar os seguintes valores de exemplo:

**Valores da TestVNet1**

Nome: TestVNet1<br>
Espaço de endereços: 10.11.0.0/16, 10.12.0.0/16 (opcional)<br>
Nome da sub-rede: predefinido<br>
Intervalo de endereços da sub-rede: 10.11.0.1/24<br>
Grupo de recursos: ClassicRG<br>
Localização: EUA Leste<br>
GatewaySubnet: 10.11.1.0/27

**Valores da TestVNet4**

Nome: TestVNet4<br>
Espaço de endereços: 10.41.0.0/16, 10.42.0.0/16 (opcional)<br>
Nome da sub-rede: predefinido<br>
Intervalo de endereços da sub-rede: 10.41.0.1/24<br>
Grupo de recursos: ClassicRG<br>
Localização: EUA Oeste<br>
GatewaySubnet: 10.41.1.0/27

**Ao criar as suas VNets, tenha em atenção as seguintes definições:**

* **Espaços de endereços de rede virtual** – na página de espaços de endereço de rede Virtual, especifique o intervalo de endereços que pretende utilizar para a rede virtual. Estes são os endereços IP dinâmicos, que serão atribuídos às VMs e a outras instâncias de função que implementar nesta rede virtual.<br>Os espaços de endereços que selecionar não podem sobrepor-se com os espaços de endereços para qualquer uma das outras VNets ou no local localizações que se conectarão nesta VNet.

* **Localização** – quando cria uma rede virtual, associa-a com uma localização do Azure (região). Por exemplo, se pretender que as VMs que são implementadas na sua rede virtual estejam fisicamente localizadas na região E.U.A. oeste, selecione essa localização. Não é possível alterar a localização associada com a rede virtual depois de o criar.

**Depois de criar as suas VNets, pode adicionar as seguintes definições:**

* **Espaço de endereços** – espaço de endereços adicional não é necessário para esta configuração, mas pode adicionar espaço de endereços adicionais depois de criar a VNet.

* **Sub-redes** – sub-redes adicionais não são necessárias para esta configuração, mas pode querer ter as VMs numa sub-rede separada das outras instâncias de função.

* **Servidores DNS** – introduza o nome do servidor DNS e endereço IP. Esta definição não cria um servidor DNS. Permite-lhe especificar os servidores DNS que pretende utilizar para a resolução de nomes desta rede virtual.

Nesta secção, configure o tipo de ligação, o site local e criar o gateway.

## <a name="localsite"></a>Passo 3 - configurar o site local

O Azure utiliza as definições especificadas em cada site de rede local para determinar como encaminhar o tráfego entre as VNets. Tem de apontar para a rede local correspondentes que pretende encaminhar o tráfego para cada VNet. Determinar o nome que pretende utilizar para fazer referência a cada site de rede local. É melhor usar algo descritivo.

Por exemplo, o TestVNet1 liga a um site de rede local que criar com o nome 'VNet4Local'. As definições para VNet4Local contêm os prefixos de endereço da TestVNet4.

O site local para cada VNet é a outra VNet. Os seguintes valores de exemplo são utilizados para nossa configuração:

| Rede Virtual | Espaço de Endereços | Região | Liga-se ao site de rede local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |EUA Leste |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |EUA Oeste |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Localize o TestVNet1 no portal do Azure. Na **ligações VPN** secção da página, clique em **Gateway**.

    ![Nenhum gateway](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Sobre o **nova ligação VPN** página, selecione **Site a Site**.
3. Clique em **Local site** para abrir a página de Local site e configurar as definições.
4. Sobre o **Local site** página, dê o nome do site local. No nosso exemplo, designamos de site local 'VNet4Local'.
5. Para **endereço IP do gateway de VPN**, pode utilizar qualquer endereço IP que pretende, desde que ele está num formato válido. Normalmente, usaria o verdadeiro endereço IP externo para um dispositivo VPN. Mas, para uma configuração de VNet a VNet clássica, utilizar o endereço IP público atribuído ao gateway para a sua VNet. Considerando que ainda não criou o gateway de rede virtual, especificar qualquer endereço IP público válido como um marcador de posição.<br>Não deixe em branco - não é opcional para esta configuração. Num passo posterior, volte a estas definições e configurá-los com os endereços IP de gateway de rede virtual correspondentes, uma vez que o Azure gera-lo.
6. Para **espaço de endereços de cliente**, utilize o espaço de endereços da outra VNet. Consulte o exemplo de planejamento. Clique em **OK** para guardar as definições e voltar para o **nova ligação VPN** página.

    ![local site](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>Passo 4 - criar o gateway de rede virtual

Cada rede virtual tem de ter um gateway de rede virtual. O gateway de rede virtual encaminha e encripta o tráfego.

1. Na página **Nova Ligação VPN**, selecione a caixa de verificação **Criar gateway de imediato**.
2. Clique em **sub-rede, tamanho e tipo de encaminhamento**. Sobre o **configuração do Gateway** página, clique em **sub-rede**.
3. O nome da sub-rede de gateway é preenchido automaticamente com o nome necessário "GatewaySubnet". O **intervalo de endereços** contém os endereços IP que são alocados para os serviços do gateway VPN. Algumas configurações permitem que uma sub-rede de gateway de/29, mas é melhor usar/28 ou/27 suportem configurações futuras que podem exigir mais endereços IP para os serviços do gateway. Nossas configurações de exemplo, utilizamos 10.11.1.0/27. Ajustar o espaço de endereços, em seguida, clique em **OK**.
4. Configurar o **tamanho de Gateway**. Esta definição refere-se para o [SKU de Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Configurar o **tipo de encaminhamento**. O encaminhamento de tipo para esta configuração tem de ser **dinâmica**. Não é possível alterar o tipo de encaminhamento mais tarde, a menos que subdividir o gateway e criar um novo.
6. Clique em **OK**.
7. Sobre o **nova ligação VPN** página, clique em **OK** para começar a criar o gateway de rede virtual. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

## <a name="vnet4settings"></a>Passo 5 - configurar definições de TestVNet4

Repita os passos para [criar um site local](#localsite) e [criar o gateway de rede virtual](#gw) para configurar a TestVNet4, substituindo os valores quando necessário. Se estiver fazendo isso como um exercício, utilize o [valores de exemplo](#vnetvalues).

## <a name="updatelocal"></a>Passo 6 - atualizar os sites locais

Depois dos gateways de rede virtual foram criados para ambas as VNets, terá de ajustar os sites locais **endereço IP do gateway de VPN** valores.

|Nome da VNet|Site ligado|Endereço IP do gateway|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|Endereço IP do VPN gateway da TestVNet4|
|TestVNet4|VNet1Local|Endereço IP do gateway VPN da TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Parte 1: obter o endereço IP da público de gateway rede virtual

1. Localize a sua rede virtual no portal do Azure.
2. Clique para abrir a VNet **descrição geral** página. Na página, no **ligações VPN**, pode ver o endereço IP do gateway da rede virtual.

   ![IP público](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Copie o endereço IP. Irá utilizá-lo na próxima seção.
4. Repita estes passos para a TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Parte 2: modificar os sites locais

1. Localize a sua rede virtual no portal do Azure.
2. Na VNet **descrição geral** página, clique em local site.

   ![Site local criado](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Sobre o **conexões VPN Site a Site** página, clique no nome do site local que pretende modificar.

   ![Abrir site local](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Clique nas **Local site** que pretende modificar.

   ![modificar o site](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Atualização do **endereço IP do gateway de VPN** e clique em **OK** para guardar as definições.

   ![Gateway IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Feche as outras páginas.
7. Repita estes passos para a TestVNet4.

## <a name="getvalues"></a>Passo 7 – obter valores do arquivo de configuração de rede

Quando cria VNets clássicas no portal do Azure, o nome que ver não é o nome completo do que utilizar para o PowerShell. Por exemplo, uma VNet que parece ser nomeados **TestVNet1** no portal, pode ter um nome muito mais tempo no ficheiro de configuração de rede. O nome pode ser algo como: **Grupo ClassicRG TestVNet1**. Ao criar as suas ligações, é importante utilizar os valores que vê no ficheiro de configuração de rede.

Nos passos seguintes, vai ligar à sua conta do Azure e transferir e ver o ficheiro de configuração de rede para obter os valores que são necessários para as suas ligações.

1. Baixe e instale a versão mais recente dos cmdlets do PowerShell de gestão de serviço do Azure (SM). Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

2. Abra a consola do PowerShell com direitos elevados e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

   ```powershell
   Connect-AzureRmAccount
   ```

   Verifique as subscrições da conta.

   ```powershell
   Get-AzureRmSubscription
   ```

   Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar.

   ```powershell
   Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

   Em seguida, utilize o cmdlet seguinte para adicionar a sua subscrição do Azure para o PowerShell para o modelo de implementação clássica.

   ```powershell
   Add-AzureAccount
   ```
3. Exportar e ver o ficheiro de configuração de rede. Crie um diretório no seu computador e, em seguida, exporte o ficheiro de configuração de rede para o diretório. Neste exemplo, o ficheiro de configuração de rede é exportado para o **c:\azurenet.**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
4. Abra o ficheiro com um editor de texto e ver os nomes dos seus sites e as VNets. Estes serão o nome que utiliza quando cria as suas ligações.<br>Nomes de VNet estão listados como **VirtualNetworkSite name =**<br>Os nomes de site são apresentados como **LocalNetworkSiteRef nome =**

## <a name="createconnections"></a>Passo 8 - criar as ligações de gateway VPN

Quando todos os passos anteriores estiverem concluídos, pode definir as chaves pré-partilhadas de IPsec/IKE e criar a ligação. Este conjunto de passos utiliza o PowerShell. Não não possível configurar ligações de VNet a VNet para o modelo de implementação clássica no portal do Azure.

Nos exemplos, tenha em atenção que a chave partilhada é exatamente o mesmo. A chave partilhada tem de corresponder sempre. Certifique-se de que substitui os valores nesses exemplos com os nomes de exatos para as suas VNets e os Sites de rede Local.

1. Criar a ligação da TestVNet1 para a TestVNet4.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName '17BE5E2C_VNet4Local' -SharedKey A1b2C3D4
   ```
2. Criar a ligação da TestVNet4 para a TestVNet1.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'F7F7BFC7_VNet1Local' -SharedKey A1b2C3D4
   ```
3. Aguarde que as ligações ao inicializar. Depois do gateway foi inicializado, o estado é "Com êxito".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq"></a>Considerações de VNet a VNet para VNets clássicas
* As redes virtuais podem estar nas subscrições idêntica ou diferentes.
* As redes virtuais podem estar nas mesmas regiões ou em regiões (localizações) diferentes do Azure.
* Um serviço cloud ou um ponto final de balanceamento de carga não pode abranger várias redes virtuais, mesmo se estiverem ligadas em conjunto.
* Ligar a várias redes virtuais entre si, não requer quaisquer dispositivos VPN.
* VNet a VNet suporta a ligação de redes virtuais do Azure. Suporta a ligação de máquinas virtuais ou serviços cloud que não são implantados numa rede virtual.
* VNet para vnet requer gateways de encaminhamento dinâmico. Os gateways de encaminhamento estático do Azure não são suportados.
* A conectividade de rede virtual pode ser utilizada em simultâneo com VPNs de vários locais. Há um máximo de 10 túneis VPN para um gateway de VPN de rede virtual a ligar a outras redes virtuais ou sites no local.
* Os espaços de endereços das redes virtuais e sites de rede local no local não se podem sobrepor. A sobreposição de espaços de endereços fará com que a criação de redes virtuais ou carregar arquivos de configuração netcfg para efetuar a ativação.
* Os túneis redundantes entre um par de redes virtuais não são suportados.
* Todos os túneis VPN da vnet, incluindo P2S VPNs, partilham a largura de banda disponível para o gateway de VPN e o mesmo VPN gateway SLA tempo de atividade no Azure.
* O tráfego de VNet a VNet circula entre o backbone do Azure.

## <a name="next-steps"></a>Passos Seguintes
Verifique as suas ligações. Ver [verificar uma ligação de Gateway de VPN](vpn-gateway-verify-connection-resource-manager.md).
