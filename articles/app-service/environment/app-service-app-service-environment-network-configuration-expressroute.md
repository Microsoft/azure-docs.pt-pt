---
title: Configure Azure ExpressRoute v1
description: Configuração de rede para App Service Environment para PowerApps com Azure ExpressRoute. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: stefsch
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 3fa2677f7c71b118ec3f7da717e29d5879ee982b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88961827"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Detalhes de configuração de rede para App Service Environment para PowerApps com Azure ExpressRoute

Os clientes podem ligar um circuito [Azure ExpressRoute][ExpressRoute] à sua infraestrutura de rede virtual para estender a sua rede no local ao Azure. O App Service Environment é criado numa sub-rede da infraestrutura [de rede virtual.][virtualnetwork] As aplicações que funcionam no App Service Environment estabelecem ligações seguras a recursos back-end que só são acessíveis sobre a ligação ExpressRoute.  

O Ambiente de Serviço de Aplicações pode ser criado nestes cenários:
- Redes virtuais Azure Resource Manager.
- Redes virtuais de modelo de implementação clássica.
- Redes virtuais que utilizam intervalos de endereços públicos ou espaços de endereço RFC1918 (isto é, endereços privados). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Conectividade de rede necessária

O App Service Environment tem requisitos de conectividade de rede que inicialmente podem não ser cumpridos numa rede virtual que está ligada ao ExpressRoute.

O Ambiente de Serviço de Aplicações requer que as seguintes definições de conectividade da rede funcionem corretamente:

* Conectividade de rede de saída para pontos finais de armazenamento Azure em todo o mundo tanto na porta 80 como na porta 443. Estes pontos finais estão localizados na mesma região que o App Service Environment e também outras regiões do Azure. Os pontos finais do Azure Storage resolvem-se nos seguintes domínios DNS: table.core.windows.net, blob.core.windows.net, queue.core.windows.net e file.core.windows.net.  

* Conectividade de rede de saída ao serviço Azure Files na porta 445.

* Conectividade de rede de saída para pontos finais da Base de Dados Azure SQL que estão localizados na mesma região que o App Service Environment. Os pontos finais da Base de Dados SQL resolvem-se sob o domínio database.windows.net, que requer acesso aberto às portas 1433, 11000-11999 e 14000-14999. Para obter mais informações sobre a utilização da porta V12 da Base de Dados SQL, consulte [portas para além de 1433 para ADO.NET 4.5](../../azure-sql/database/adonet-v12-develop-direct-route-ports.md).

* Conectividade de rede de saída para os pontos finais do avião de gestão Azure (tanto o modelo de implementação clássico Azure como os pontos finais do Azure Resource Manager). A conectividade com estes pontos finais inclui os domínios management.core.windows.net e management.azure.com. 

* Conectividade de rede de saída para os domínios ocsp.msocsp.com, mscrl.microsoft.com e crl.microsoft.com. A conectividade com estes domínios é necessária para suportar a funcionalidade TLS.

* A configuração de DNS para a rede virtual deve ser capaz de resolver todos os pontos finais e domínios mencionados neste artigo. Se os pontos finais não puderem ser resolvidos, a criação do App Service Environment falha. Qualquer Ambiente de Serviço de Aplicações existente é marcado como insalubre.

* O acesso de saída na porta 53 é necessário para comunicação com servidores DNS.

* Se existir um servidor DNS personalizado na outra extremidade de um gateway VPN, o servidor DNS deve ser acessível a partir da sub-rede que contém O Ambiente de Serviço de Aplicações. 

* O caminho da rede de saída não pode passar por proxies corporativos internos e não pode ser forçada a fazer túneis no local. Estas ações alteram o endereço NAT eficaz do tráfego de rede de saída do App Service Environment. Alterações no endereço NAT do tráfego de rede de saída do App Service Environment causam falhas de conectividade em muitos dos pontos finais. A criação de App Service Environment falha. Qualquer Ambiente de Serviço de Aplicações existente é marcado como insalubre.

* O acesso à rede de entrada às portas necessárias para o Ambiente de Serviço de Aplicações deve ser permitido. Para mais informações, consulte [como controlar o tráfego de entrada no App Service Environment][requiredports].

Para cumprir os requisitos dns, certifique-se de que uma infraestrutura de DNS válida está configurada e mantida para a rede virtual. Se a configuração do DNS for alterada após a criação do App Service Environment, os desenvolvedores podem forçar o App Service Environment a captar a nova configuração de DNS. Pode desencadear um reboot de ambiente rolante utilizando o ícone **Restart** sob a gestão do Ambiente do Serviço de Aplicações no [portal Azure.][NewPortal] O reboot faz com que o ambiente apanhe a nova configuração de DNS.

Para cumprir os requisitos de acesso à rede de entrada, configurar um [grupo de segurança de rede (NSG)][NetworkSecurityGroups] na sub-rede App Service Environment. O NSG permite o acesso necessário [ao controlo do tráfego de entrada para o App Service Environment.][requiredports]

## <a name="outbound-network-connectivity"></a>Conectividade de rede de saída

Por padrão, um circuito ExpressRoute recém-criado anuncia uma rota padrão que permite a conectividade de saída da Internet. O App Service Environment pode utilizar esta configuração para se ligar a outros pontos finais do Azure.

Uma configuração comum do cliente é definir a sua própria rota padrão (0.0.0.0/0), que força o tráfego de internet de saída a fluir no local. Este fluxo de tráfego invariavelmente quebra o Ambiente de Serviço de Aplicações. O tráfego de saída está bloqueado no local ou NAT'd para um conjunto irreconhecível de endereços que já não funcionam com vários pontos finais do Azure.

A solução é definir uma (ou mais) rotas definidas pelo utilizador (UDRs) na sub-rede que contém o Ambiente de Serviço de Aplicações. Um UDR define rotas específicas da sub-rede que são honradas em vez da rota padrão.

Se possível, utilize a seguinte configuração:

* A configuração ExpressRoute anuncia 0.0.0.0/0. Por defeito, a configuração força túneis de todo o tráfego de saída no local.
* A UDR aplicada à sub-rede que contém App Service Environment define 0.0.0.0/0 com um próximo tipo de internet de lúpulo. Um exemplo desta configuração é descrito mais tarde neste artigo.

O efeito combinado desta configuração é que a UDR de nível sub-rede tem precedência sobre o túnel de força ExpressRoute. O acesso à Internet de saída do App Service Environment é garantido.

> [!IMPORTANT]
> As rotas definidas numa UDR devem ser específicas o suficiente para ter precedência sobre quaisquer rotas que sejam anunciadas pela configuração ExpressRoute. O exemplo descrito na secção seguinte utiliza a gama de endereços broad 0.0.0.0/0. Esta gama pode ser acidentalmente ultrapassada por anúncios de rotas que usam intervalos de endereços mais específicos.
> 
> O Ambiente de Serviço de Aplicações não é suportado com configurações ExpressRoute que cruzam rotas do caminho de observação pública para o caminho de observação privado. As configurações ExpressRoute que têm o olhar público configurado recebem anúncios de rotas da Microsoft para um grande conjunto de gamas de endereços IP do Microsoft Azure. Se estes intervalos de endereços forem cruzados no caminho de observação privado, todos os pacotes de rede de saída da sub-rede De Serviço de Aplicação são obrigados a fazer um túnel para a infraestrutura de rede do cliente no local. Este fluxo de rede não é suportado atualmente com o App Service Environment. Uma solução é parar as rotas de publicidade cruzada do caminho de observação pública para o caminho de observação privada.
> 
> 

Para obter informações sobre as rotas definidas pelo utilizador, consulte [o encaminhamento de tráfego de rede virtual][UDROverview].  

Para aprender a criar e configurar rotas definidas pelo utilizador, consulte o [tráfego da rede De Rota com uma tabela de rotas utilizando o PowerShell][UDRHowTo].

## <a name="udr-configuration"></a>Configuração UDR

Esta secção mostra uma configuração de UDR exemplo para o Ambiente de Serviço de Aplicações.

### <a name="prerequisites"></a>Pré-requisitos

* Instale a Azure PowerShell a partir da [página Azure Downloads][AzureDownloads]. Escolha um download com data de junho de 2015 ou posterior. Sob **as ferramentas da linha de comando** Windows  >  **PowerShell,** **selecione Instale** para instalar os cmdlets powershell mais recentes.

* Crie uma sub-rede única para uso exclusivo pelo App Service Environment. A sub-rede única garante que os UDRs aplicados ao tráfego de saída aberto da sub-rede apenas para o Ambiente de Serviço de Aplicações.

> [!IMPORTANT]
> Só implemente o Ambiente de Serviço de Aplicações depois de completar os passos de configuração. Os passos garantem que a conectividade da rede de saída está disponível antes de tentar implementar o App Service Environment.

### <a name="step-1-create-a-route-table"></a>Passo 1: Criar uma tabela de rotas

Crie uma tabela de rotas chamada **DirectInternetRouteTable** na região do Azure dos EUA, como mostra este corte:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Passo 2: Criar rotas na tabela

Adicione rotas à tabela de rotas para permitir o acesso à Internet de saída.  

Configure o acesso à internet. Defina uma rota para 0.0.0.0/0 como mostrado neste corte:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 é um amplo intervalo de endereços. A gama é ultrapassada por intervalos de endereços anunciados pelo ExpressRoute que são mais específicos. Uma UDR com uma rota de 0.0.0.0/0 deve ser usada em conjunto com uma configuração ExpressRoute que anuncia apenas 0.0.0.0/0. 

Como alternativa, faça o download de uma lista atual e completa de gamas CIDR em uso pela Azure. O ficheiro XML para todas as gamas de endereços Azure IP está disponível no [Microsoft Download Center][DownloadCenterAddressRanges].  

> [!NOTE]
>
> Os intervalos de endereço IP Azure mudam ao longo do tempo. As rotas definidas pelo utilizador precisam de atualizações manuais periódicas para se manterem sincronizadas.
>
> Um único UDR tem um limite superior padrão de 100 rotas. Tem de "resumir" os intervalos de endereçoS IP Azure para se encaixar dentro do limite de 100 rotas. As rotas definidas pela UDR têm de ser mais específicas do que as rotas que são publicitadas pela sua ligação ExpressRoute.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Passo 3: Associar a tabela à sub-rede

Associe a tabela de rotas à sub-rede onde pretende implementar o App Service Environment. Este comando associa a tabela **DirectInternetRouteTable** à sub-rede **ASESubnet** que conterá o Ambiente de Serviço de Aplicações.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Passo 4: Testar e confirmar a rota

Depois da tabela de rotas estar ligada à sub-rede, teste e confirme a rota.

Insaire uma máquina virtual na sub-rede e confirme estas condições:

* O tráfego de saída para os pontos finais Azure e não-Azure descritos neste artigo **não** flui pelo circuito ExpressRoute. Se o tráfego de saída da sub-rede for a força escavada no local, a criação do App Service Environment falha sempre.
* As pesquisas de DNS para os pontos finais descritos neste artigo resolvem-se corretamente. 

Depois de completar os passos de configuração e confirmar a rota, elimine a máquina virtual. A sub-rede precisa de ser "vazia" quando o Ambiente de Serviço de Aplicações é criado.

Agora está pronto para implementar o App Service Environment!

## <a name="next-steps"></a>Passos seguintes

Para começar com o Ambiente de Serviço de Aplicações para PowerApps, consulte [Introdução ao Ambiente de Serviço de Aplicações.][IntroToAppServiceEnvironment]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[UDROverview]: ../../virtual-network/virtual-networks-udr-overview.md
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: ../../virtual-network/tutorial-create-route-table-powershell.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->