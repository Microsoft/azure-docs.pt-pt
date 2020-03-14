---
title: Configure Azure ExpressRoute v1
description: Configuração de rede para App Service Environment for PowerApps com Azure ExpressRoute. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: stefsch
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 8a83c2f6ac7599ff37237834a85b7771cf4ee502
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243877"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Detalhes de configuração de rede para App Service Environment for PowerApps com Azure ExpressRoute

Os clientes podem ligar um circuito [Azure ExpressRoute][ExpressRoute] à sua infraestrutura de rede virtual para alargar a sua rede no local ao Azure. O App Service Environment é criado numa subnet da infraestrutura de [rede virtual.][virtualnetwork] As aplicações que funcionam no App Service Environment estabelecem ligações seguras a recursos back-end que só são acessíveis sobre a ligação ExpressRoute.  

O Ambiente de Serviço sinuoso pode ser criado nestes cenários:
- Redes virtuais do Gestor de Recursos Azure.
- Redes virtuais modelo de implementação clássica.
- Redes virtuais que utilizam gamas de endereços públicos ou espaços de endereçoS RFC1918 (isto é, endereços privados). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Conectividade de rede necessária

O App Service Environment tem requisitos de conectividade de rede que inicialmente podem não ser cumpridos numa rede virtual que está ligada ao ExpressRoute.

O Ambiente do Serviço de Aplicações requer que as seguintes definições de conectividade da rede funcionem corretamente:

* Conectividade de rede de saída para pontos finais de armazenamento Azure em todo o mundo tanto no porto 80 como no porto 443. Estes pontos finais estão localizados na mesma região que o App Service Environment e também outras regiões azure. Os pontos finais do Armazenamento Azure resolvem-se nos seguintes domínios DNS: table.core.windows.net, blob.core.windows.net, queue.core.windows.net e file.core.windows.net.  

* Conectividade de rede de saída para o serviço Azure Files na porta 445.

* Conectividade de rede de saída para pontos finais da Base de Dados Azure SQL que estão localizados na mesma região que o App Service Environment. Os pontos finais da Base de Dados SQL resolvem-se no domínio database.windows.net, que requer acesso aberto às portas 1433, 11000-11999 e 14000-14999. Para mais detalhes sobre a utilização da porta SQL Database V12, consulte portas para além de [1433 para ADO.NET 4.5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

* Conectividade de rede de saída para os pontos finais de plano de gestão Azure (tanto o modelo de implantação clássico do Azure como os pontos finais do Gestor de Recursos Azure). A conectividade com estes pontos finais inclui os domínios management.core.windows.net e management.azure.com. 

* Conectividade de rede de saída para os domínios ocsp.msocsp.com, mscrl.microsoft.com e crl.microsoft.com. A conectividade com estes domínios é necessária para suportar a funcionalidade SSL.

* A configuração DNS para a rede virtual deve ser capaz de resolver todos os pontos finais e domínios mencionados neste artigo. Se os pontos finais não puderem ser resolvidos, a criação do App Service Environment falha. Qualquer ambiente de serviço de aplicações existente é marcado como insalubre.

* O acesso de saída na porta 53 é necessário para a comunicação com os servidores DNS.

* Se existir um servidor DNS personalizado na outra extremidade de um gateway VPN, o servidor DNS deve ser acessível a partir da subnet que contém o Ambiente de Serviço de Aplicações. 

* O caminho da rede de saída não pode viajar através de proxies corporativos internos e não pode ser forçada a fazer túneis no local. Estas ações alteram o endereço NAT eficaz do tráfego de rede de saída do App Service Environment. Alterações no endereço NAT do serviço de aplicações O tráfego de saída da rede de tráfego de rede causa falhas de conectividade em muitos dos pontos finais. App Service A criação ambiental falha. Qualquer ambiente de serviço de aplicações existente é marcado como insalubre.

* Deve ser permitido o acesso à rede de entrada às portas necessárias para o Ambiente de Serviço sinuoso. Para mais detalhes, consulte Como controlar o tráfego de entrada para o Ambiente de Serviço de [Aplicações][requiredports].

Para satisfazer os requisitos dNS, certifique-se de que uma infraestrutura DNS válida é configurada e mantida para a rede virtual. Se a configuração dNS for alterada após a criação do App Service Environment, os desenvolvedores podem forçar o App Service Environment a captar a nova configuração DNS. Pode desencadear um reboot ambiente rolante utilizando o ícone **Restart** sob gestão do ambiente de serviço de aplicação no [portal Azure][NewPortal]. O reboot faz com que o ambiente apanhe a nova configuração DNS.

Para satisfazer os requisitos de acesso à rede de entrada, configure um grupo de segurança de [rede (NSG)][NetworkSecurityGroups] na subnet App Service Environment. O NSG permite o acesso necessário para controlar o tráfego de entrada para o Ambiente de Serviço de [Aplicações][requiredports].

## <a name="outbound-network-connectivity"></a>Conectividade da rede de saída

Por padrão, um circuito ExpressRoute recém-criado anuncia uma rota padrão que permite a conectividade de acesso à Internet. O App Service Environment pode utilizar esta configuração para se ligar a outros pontos finais do Azure.

Uma configuração comum do cliente é definir a sua própria rota padrão (0.0.0.0/0), que força o tráfego de internet de saída a fluir no local. Este fluxo de tráfego invariavelmente quebra o App Service Environment. O tráfego de saída está bloqueado no local ou na NAT'd para um conjunto irreconhecível de endereços que já não funcionam com vários pontos finais Azure.

A solução é definir uma (ou mais) rotas definidas pelo utilizador (UDRs) na subnet que contém o Ambiente do Serviço de Aplicações. Um UDR define rotas específicas da sub-rede que são honradas em vez da rota padrão.

Se possível, utilize a seguinte configuração:

* A configuração ExpressRoute anuncia 0.0.0.0/0. Por padrão, a força de configuração túneis todo o tráfego de saída no local.
* O UDR aplicado à subnet que contém app service Environment define 0.0.0.0/0 com um próximo tipo de internet de lúpulo. Um exemplo desta configuração é descrito mais tarde neste artigo.

O efeito combinado desta configuração é que o UDR de nível de subnet tem precedência sobre o túnel de força ExpressRoute. O acesso à Internet de saída do App Service Environment está garantido.

> [!IMPORTANT]
> As rotas definidas num UDR devem ser específicas o suficiente para prevalecer sobre quaisquer rotas que sejam publicitadas pela configuração ExpressRoute. O exemplo descrito na secção seguinte utiliza a gama de endereços 0.0.0.0/0. Esta gama pode acidentalmente ser ultrapassada por anúncios de rotas que utilizam faixas de endereço mais específicas.
> 
> O App Service Environment não é suportado com configurações expressRoute que transversam rotas do caminho de espreitar o público para o caminho de espreitar privado. As configurações expressRoute que têm o público configurado recebem anúncios de rotas da Microsoft para um grande conjunto de gamas de endereços IP microsoft Azure. Se estas gamas de endereços forem publicitadas cruzadas no caminho de observação privada, todos os pacotes de rede de saída da subnet App Service Environment são obrigados a fazer um túnel para a infraestrutura de rede no local do cliente. Este fluxo de rede não é atualmente suportado com o App Service Environment. Uma solução é parar as rotas de publicidade cruzada do caminho do público para o caminho do público para o caminho do peering privado.
> 
> 

Para obter informações sobre as rotas definidas pelo utilizador, consulte o [encaminhamento de tráfego][UDROverview]da rede virtual .  

Para aprender a criar e configurar rotas definidas pelo utilizador, consulte o tráfego da rede Route com uma tabela de rotas utilizando o [PowerShell][UDRHowTo].

## <a name="udr-configuration"></a>Configuração UDR

Esta secção mostra um exemplo de configuração UDR para o Ambiente de Serviço de Aplicações.

### <a name="prerequisites"></a>Pré-requisitos

* Instale o Azure PowerShell na [página Descarregamentos do Azure][AzureDownloads]. Escolha um download com data de junho de 2015 ou posterior. Sob **as ferramentas da linha de comando** > Windows **PowerShell,** selecione **Instalar** para instalar os mais recentes cmdlets PowerShell.

* Crie uma subnet única para uso exclusivo pelo App Service Environment. A sub-rede única garante que os UDRs aplicados à subrede abrem tráfego de saída apenas para o Ambiente de Serviço de Aplicações.

> [!IMPORTANT]
> Basta implementar o App Service Environment depois de completar os passos de configuração. Os passos garantem que a conectividade da rede de saída está disponível antes de tentar implementar o App Service Environment.

### <a name="step-1-create-a-route-table"></a>Passo 1: Criar uma tabela de rotas

Crie uma tabela de rotas chamada **DirectInternetRouteTable** na região west US Azure, como mostra este corte:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Passo 2: Criar rotas na tabela

Adicione rotas para a tabela de rotas para permitir o acesso à Internet de saída.  

Configure o acesso de saída à internet. Defina uma rota para 0.0.0.0/0, como mostrado neste corte:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 é um vasto endereço. A gama é sobreroverpor gamas de endereços anunciadas pelo ExpressRoute que são mais específicas. Um UDR com uma rota de 0.0.0.0/0 deve ser utilizado em conjunto com uma configuração ExpressRoute que anuncia apenas 0.0.0.0/0. 

Como alternativa, descarregue uma lista atual e completa de gamas CIDR em uso pelo Azure. O ficheiro XML para todas as gamas de endereços IP do Azure está disponível no [Microsoft Download Center][DownloadCenterAddressRanges].  

> [!NOTE]
>
> Os intervalos de endereçoip Azure mudam ao longo do tempo. As rotas definidas pelo utilizador precisam de atualizações manuais periódicas para se manterem sincronizadas.
>
> Um único UDR tem um limite superior padrão de 100 rotas. É necessário "resumir" as gamas de endereços IP Do Azure para se encaixar dentro do limite de 100 rotas. As rotas definidas pela UDR têm de ser mais específicas do que as rotas que são publicitadas pela sua ligação ExpressRoute.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Passo 3: Associar a tabela à sub-rede

Associe a tabela de rotas à subnet onde pretende implementar o App Service Environment. Este comando associa a tabela **DirectInternetRouteTable** à subnet **ASESubnet** que conterá o Ambiente do Serviço de Aplicações.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Passo 4: Testar e confirmar a rota

Depois de a tabela de rotas estar ligada à sub-rede, testar e confirmar a rota.

Desloque uma máquina virtual para a sub-rede e confirme estas condições:

* O tráfego de saída para os pontos finais Azure e não-Azure descritos neste artigo **não** flui pelo circuito ExpressRoute. Se o tráfego de saída da subnet for forçada a fazer túneis no local, a criação do App Service Environment falha sempre.
* As análises ao DNS para os pontos finais descritos neste artigo resolvem-se corretamente. 

Depois de completar os passos de configuração e confirmar a rota, elimine a máquina virtual. A sub-rede precisa de estar "vazia" quando o Ambiente de Serviço de Aplicações é criado.

Agora está pronto para implementar o App Service Environment!

## <a name="next-steps"></a>Passos seguintes

Para começar com o App Service Environment for PowerApps, consulte introdução ao Ambiente do [Serviço de Aplicações][IntroToAppServiceEnvironment].

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[UDROverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/ 
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell 
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->
