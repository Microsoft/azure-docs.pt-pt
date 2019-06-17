---
title: Detalhes de configuração de rede para o Azure ExpressRoute - serviço de aplicações
description: Detalhes de configuração de rede para o ambiente de serviço de aplicações do PowerApps em redes virtuais ligadas a um circuito do ExpressRoute do Azure.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: e0fa87facec73efdfff1a9908dcba92838215425
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130675"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Detalhes de configuração de rede para o ambiente de serviço de aplicações do PowerApps com o Azure ExpressRoute

Os clientes podem ligar-se um [Azure ExpressRoute] [ ExpressRoute] circuito para a sua infraestrutura de rede virtual para expandir a sua rede no local para o Azure. Ambiente de serviço de aplicações é criado numa sub-rede do [rede virtual] [ virtualnetwork] infraestrutura. Aplicações que são executadas no ambiente de serviço de aplicações estabelecer ligações seguras para os recursos de back-end que estão acessíveis apenas através da ligação de ExpressRoute.  

Ambiente de serviço de aplicações pode ser criado nos seguintes cenários:
- Redes virtuais do Azure Resource Manager.
- Redes virtuais do modelo de implementação clássica.
- (Ou seja, endereços privados) de espaços de endereços de redes virtuais que utilizam intervalos de endereços públicos ou RFC1918. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Conectividade de rede necessária

Ambiente de serviço de aplicações tem requisitos de conectividade de rede que inicialmente não podem ser cumpridos numa rede virtual que está ligada ao ExpressRoute.

Ambiente de serviço de aplicações requer as seguintes definições de conectividade de rede a funcionar corretamente:

* Conectividade de rede de saída para pontos finais de armazenamento do Azure em todo o mundo na porta 80 e a porta 443. Estes pontos finais estão localizados na mesma região que o ambiente de serviço de aplicações bem como noutras regiões do Azure. Pontos finais de armazenamento do Azure resolver sob os seguintes domínios DNS: table.core.windows.net, blob.core.windows.net, queue.core.windows.net e file.core.windows.net.  

* Conectividade de rede de saída para o serviço de ficheiros do Azure na porta 445.

* Conectividade de rede de saída para os pontos finais da SQL Database do Azure que se encontram na mesma região que o ambiente de serviço de aplicações. Pontos finais de base de dados SQL resolver sob o domínio database.windows.net, que requer acesso aberto à portas 1433, 11000 11999 e 14000 14999. Para obter detalhes sobre a utilização de porta do SQL da base de dados V12, consulte [portas para além do 1433 para ADO.NET 4.5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

* Conectividade de rede de saída para os plano de gestão pontos finais do Azure (modelo de implementação clássica do Azure e pontos finais do Gestor de recursos do Azure). Conectividade para estes pontos finais inclui os domínios management.core.windows.net e management.azure.com. 

* Conectividade de rede de saída para os domínios ocsp.msocsp.com mscrl.microsoft.com e crl.microsoft.com. Conectividade com estes domínios é necessária para suportar a funcionalidade SSL.

* A configuração de DNS para a rede virtual tem de ser capaz de resolver todos os pontos de extremidade e domínios mencionados neste artigo. Se não conseguir resolver os pontos de extremidade, Falha ao criar o ambiente de serviço de aplicações. Qualquer ambiente de serviço de aplicações existente está marcado como mau estado de funcionamento.

* Acesso de saída na porta 53 é necessário para a comunicação com servidores DNS.

* Se existir um servidor DNS personalizado na outra extremidade de um gateway VPN, o servidor DNS tem de ser acessível a partir da sub-rede que contém o ambiente de serviço de aplicações. 

* O caminho de rede de saída não é possível viajar por meio de proxies corporativos internos e não é possível forçar o túnel no local. Estas ações alterar o endereço NAT em vigor a partir de tráfego de rede de saída do ambiente de serviço de aplicações. Alterações ao endereço NAT do tráfego de rede de saída de ambiente de serviço de aplicações dão origem a falhas de conectividade para muitos dos pontos finais. Falha ao criar de ambiente de serviço de aplicações. Qualquer ambiente de serviço de aplicações existente está marcado como mau estado de funcionamento.

* Acesso de rede de entrada para as portas necessárias para o ambiente de serviço de aplicações têm de ser permitido. Para obter detalhes, consulte [como controlar o tráfego de entrada para o ambiente de serviço de aplicações][requiredports].

Para cumprir os requisitos de DNS, certifique-se de uma infraestrutura de DNS válida é configurada e mantida para a rede virtual. Se a configuração de DNS for alterada após a criação do ambiente de serviço de aplicações, os desenvolvedores podem forçar o ambiente de serviço de aplicações de retirada a nova configuração de DNS. Pode acionar um reinício sem interrupção do ambiente utilizando o **reinicie** ícone sob gestão do ambiente de serviço de aplicações no [portal do Azure][NewPortal]. O reinício faz com que o ambiente de retirada a nova configuração de DNS.

Para cumprir os requisitos de acesso de rede de entrada, configure uma [grupo de segurança de rede (NSG)] [ NetworkSecurityGroups] na sub-rede do ambiente de serviço de aplicações. O NSG permite o acesso necessário [para controlar o tráfego de entrada para o ambiente de serviço de aplicações][requiredports].

## <a name="outbound-network-connectivity"></a>Conectividade de rede de saída

Por predefinição, um circuito ExpressRoute criado recentemente anuncia uma rota padrão que permite a conectividade de internet de saída. Ambiente de serviço de aplicações pode utilizar esta configuração para ligar a outros pontos finais do Azure.

Uma configuração de cliente comum é definir sua própria rota predefinida (0.0.0.0/0), que força o tráfego de internet de saída para o fluxo no local. Este fluxo de tráfego Invariavelmente divide o ambiente de serviço de aplicações. O tráfego de saída está bloqueado no local ou o NAT iria para um conjunto irreconhecível de endereços que já não funcionam com vários pontos de extremidade do Azure.

A solução é definir uma (ou mais) rotas definidas pelo utilizador (UDRs) na sub-rede que contém o ambiente de serviço de aplicações. Um UDR define rotas de sub-rede específica, que são respeitadas em vez da rota predefinida.

Se possível, utilize a seguinte configuração:

* A configuração de ExpressRoute anuncia 0.0.0.0/0. Por predefinição, a força de configuração de túneis todo o tráfego de saída no local.
* O UDR aplicado à sub-rede que contém o ambiente de serviço de aplicações define 0.0.0.0/0 com um tipo de próximo salto de internet. Um exemplo desta configuração é descrito neste artigo.

O efeito combinado desta configuração é que o UDR de nível de sub-rede terão precedência sobre o túnel de forçar de ExpressRoute. Acesso de internet de saída do ambiente de serviço de aplicações é garantido.

> [!IMPORTANT]
> As rotas definidas num UDR tem de ser específicas o suficiente para têm precedência sobre quaisquer rotas que são anunciadas pela configuração do ExpressRoute. O exemplo descrito na secção seguinte utiliza o intervalo de endereços abrangente 0.0.0.0/0. Este intervalo pode ser substituído por acidente anúncios de rota que utilizam intervalos de endereço mais específicos.
> 
> Ambiente de serviço de aplicações não é suportada com as configurações do ExpressRoute que anunciem transversalmente rotas do caminho de peering público para o caminho de peering privado. Configurações do ExpressRoute com peering público configurado recebem anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se estes intervalos de endereços forem anunciados transversalmente no caminho de peering privado, todos os pacotes de rede de saída da sub-rede do ambiente de serviço de aplicações são forçado a infraestrutura de rede no local do cliente. Este fluxo de rede não é atualmente suportado com o ambiente de serviço de aplicações. Uma solução é parar de anunciar transversalmente rotas do caminho de peering público para o caminho de peering privado.
> 
> 

Para obter informações sobre as rotas definidas pelo utilizador, consulte [encaminhamento de tráfego de rede Virtual][UDROverview].  

Para saber como criar e configurar rotas definidas pelo utilizador, veja [encaminhar o tráfego de rede com uma tabela de rotas com o PowerShell][UDRHowTo].

## <a name="udr-configuration"></a>Configuração UDR

Esta secção mostra um exemplo de configuração de UDR para o ambiente de serviço de aplicações.

### <a name="prerequisites"></a>Pré-requisitos

* Instalar o Azure PowerShell a partir da [página de transferências do Azure][AzureDownloads]. Escolha um download com uma data de Junho de 2015 ou posterior. Sob **ferramentas de linha de comandos** > **Windows PowerShell**, selecione **instalar** para instalar os cmdlets do PowerShell mais recente.

* Crie uma sub-rede exclusiva para uso exclusivo pelo ambiente de serviço de aplicações. A sub-rede exclusiva garante que as UDRs aplicadas para o tráfego de saída abertas de sub-rede para o ambiente de serviço de aplicações apenas.

> [!IMPORTANT]
> Apenas implemente o ambiente de serviço de aplicações depois de concluir os passos de configuração. Os passos Certifique-se de conectividade de rede de saída está disponível antes de tentar implementar o ambiente de serviço de aplicações.

### <a name="step-1-create-a-route-table"></a>Passo 1: Criar uma tabela de rotas

Criar uma tabela de rotas com o nome **DirectInternetRouteTable** na região Oeste nos Azure, conforme mostrado neste fragmento:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Passo 2: Criar rotas na tabela

Adicione rotas à tabela de rotas para permitir o acesso de internet de saída.  

Configure o acesso de saída à internet. Defina uma rota para 0.0.0.0/0, conforme mostrado neste fragmento:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 é um intervalo de endereços abrangente. O intervalo é substituído por intervalos de endereços divulgados pelo ExpressRoute, que são mais específicos. Um UDR com uma rota de 0.0.0.0/0 deve ser utilizado em conjunto com uma configuração de ExpressRoute que anuncia apenas 0.0.0.0/0. 

Como alternativa, transfira uma lista atual e abrangente de intervalos CIDR em utilização pelo Azure. O arquivo XML para todos os intervalos de endereços IP do Azure está disponível a partir da [Microsoft Download Center][DownloadCenterAddressRanges].  

> [!NOTE]
>
> Alteram os intervalos de endereços IP do Azure ao longo do tempo. Rotas definidas pelo utilizador tem das atualizações periódicas manuais para manter em sincronia.
>
> Um único UDR tem um limite superior de padrão de 100 rotas. Precisa para os intervalos de endereços IP do Azure para caber dentro do limite de 100-route "resumir". Rotas definidas pelo UDR tem de ser mais específicas que rotas que são anunciadas pela ligação do ExpressRoute.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Passo 3: Associar a tabela para a sub-rede

Associe a tabela de rotas à sub-rede onde pretende implementar o ambiente de serviço de aplicações. Este comando associa os **DirectInternetRouteTable** da tabela para o **ASESubnet** sub-rede que irá conter o ambiente de serviço de aplicações.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Passo 4: Testar e confirmar a rota

Depois da tabela de rotas é vinculada à sub-rede, testar e confirmar a rota.

Implementar uma máquina virtual para a sub-rede e confirmar estas condições:

* Tráfego de saída para o Azure e os pontos finais de não pertencente ao Azure descritos neste artigo faz **não** fluem para o circuito do ExpressRoute. Se o tráfego de saída da sub-rede é força falhar encapsulado no local, sempre a criação do ambiente de serviço de aplicações.
* Pesquisas de DNS para os pontos finais descritos neste artigo todos os resolver corretamente. 

Depois de concluir os passos de configuração e confirmar a rota, elimine a máquina virtual. A sub-rede tem de ser "vazia" quando é criado o ambiente de serviço de aplicações.

Agora, está pronto para implementar o ambiente de serviço de aplicações!

## <a name="next-steps"></a>Passos Seguintes

Para começar a utilizar com o ambiente de serviço de aplicações do PowerApps, veja [introdução ao ambiente de serviço de aplicações][IntroToAppServiceEnvironment].

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
