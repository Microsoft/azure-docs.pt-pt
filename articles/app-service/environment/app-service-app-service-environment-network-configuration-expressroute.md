---
title: Configurar o Azure ExpressRoute v1
description: Configuração de rede para Ambiente do Serviço de Aplicativo do PowerApps com o Azure ExpressRoute. Este documento é fornecido somente para clientes que usam o ASE v1 herdado.
author: stefsch
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 8a83c2f6ac7599ff37237834a85b7771cf4ee502
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688749"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Detalhes de configuração de rede para Ambiente do Serviço de Aplicativo do PowerApps com o Azure ExpressRoute

Os clientes podem conectar um circuito [do Azure ExpressRoute][ExpressRoute] à sua infraestrutura de rede virtual para estender sua rede local para o Azure. Ambiente do Serviço de Aplicativo é criado em uma sub-rede da infraestrutura de [rede virtual][virtualnetwork] . Aplicativos que são executados em Ambiente do Serviço de Aplicativo estabelecem conexões seguras para recursos de back-end que são acessíveis somente pela conexão do ExpressRoute.  

Ambiente do Serviço de Aplicativo pode ser criado nestes cenários:
- Azure Resource Manager redes virtuais.
- Redes virtuais do modelo de implantação clássica.
- Redes virtuais que usam intervalos de endereços públicos ou espaços de endereço RFC1918 (ou seja, endereços privados). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Conectividade de rede necessária

Ambiente do Serviço de Aplicativo tem requisitos de conectividade de rede que inicialmente não podem ser atendidos em uma rede virtual que está conectada ao ExpressRoute.

Ambiente do Serviço de Aplicativo requer que as seguintes configurações de conectividade de rede funcionem corretamente:

* Conectividade de rede de saída para pontos de extremidade de armazenamento do Azure em todo o mundo na porta 80 e na porta 443. Esses pontos de extremidade estão localizados na mesma região que Ambiente do Serviço de Aplicativo e também em outras regiões do Azure. Os pontos de extremidade do armazenamento do Azure são resolvidos nos seguintes domínios DNS: table.core.windows.net, blob.core.windows.net, queue.core.windows.net e file.core.windows.net.  

* Conectividade de rede de saída para o serviço de arquivos do Azure na porta 445.

* Conectividade de rede de saída para pontos de extremidade do banco de dados SQL do Azure que estão localizados na mesma região que Ambiente do Serviço de Aplicativo. Os pontos de extremidade do banco de dados SQL são resolvidos no domínio database.windows.net, que exige acesso aberto às portas 1433, 11000-11999 e 14000-14999. Para obter detalhes sobre o uso de porta do banco de dados SQL V12, consulte [portas além de 1433 para ADO.NET 4,5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

* Conectividade de rede de saída para os pontos de extremidade do plano de gerenciamento do Azure (o modelo de implantação clássico do Azure e os pontos de extremidade de Azure Resource Manager). A conectividade com esses pontos de extremidade inclui os domínios management.core.windows.net e management.azure.com. 

* Conectividade de rede de saída para os domínios ocsp.msocsp.com, mscrl.microsoft.com e crl.microsoft.com. A conectividade com esses domínios é necessária para dar suporte à funcionalidade SSL.

* A configuração de DNS para a rede virtual deve ser capaz de resolver todos os pontos de extremidade e domínios mencionados neste artigo. Se os pontos de extremidade não puderem ser resolvidos, a criação do Ambiente do Serviço de Aplicativo falhará. Qualquer Ambiente do Serviço de Aplicativo existente é marcada como não íntegra.

* O acesso de saída na porta 53 é necessário para a comunicação com servidores DNS.

* Se um servidor DNS personalizado existir na outra extremidade de um gateway de VPN, o servidor DNS deverá estar acessível a partir da sub-rede que contém Ambiente do Serviço de Aplicativo. 

* O caminho de rede de saída não pode viajar por meio de proxies corporativos internos e não pode ser encapsulado em um túnel local. Essas ações alteram o endereço NAT efetivo do tráfego de rede de saída do Ambiente do Serviço de Aplicativo. As alterações no endereço NAT de Ambiente do Serviço de Aplicativo tráfego de rede de saída causam falhas de conectividade para muitos dos pontos de extremidade. Falha na criação de Ambiente do Serviço de Aplicativo. Qualquer Ambiente do Serviço de Aplicativo existente é marcada como não íntegra.

* O acesso à rede de entrada para as portas necessárias para Ambiente do Serviço de Aplicativo deve ser permitido. Para obter detalhes, consulte [como controlar o tráfego de entrada para ambiente do serviço de aplicativo][requiredports].

Para atender aos requisitos de DNS, verifique se uma infraestrutura DNS válida está configurada e mantida para a rede virtual. Se a configuração de DNS for alterada depois que Ambiente do Serviço de Aplicativo for criado, os desenvolvedores poderão forçar Ambiente do Serviço de Aplicativo a selecionar a nova configuração de DNS. Você pode disparar uma reinicialização do ambiente sem interrupção usando o ícone de **reinicialização** em gerenciamento de Ambiente do Serviço de Aplicativo no [portal do Azure][NewPortal]. A reinicialização faz com que o ambiente pegue a nova configuração de DNS.

Para atender aos requisitos de acesso à rede de entrada, configure um [NSG (grupo de segurança de rede)][NetworkSecurityGroups] na sub-rede ambiente do serviço de aplicativo. O NSG permite o acesso necessário [para controlar o tráfego de entrada para ambiente do serviço de aplicativo][requiredports].

## <a name="outbound-network-connectivity"></a>Conectividade de rede de saída

Por padrão, um circuito de ExpressRoute recém-criado anuncia uma rota padrão que permite a conectividade de saída da Internet. Ambiente do Serviço de Aplicativo pode usar essa configuração para se conectar a outros pontos de extremidade do Azure.

Uma configuração de cliente comum é definir sua própria rota padrão (0.0.0.0/0), que força o tráfego de Internet de saída para o fluxo local. Esse fluxo de tráfego invariavelmente interrompe Ambiente do Serviço de Aplicativo. O tráfego de saída é bloqueado localmente ou NAT para um conjunto de endereços irreconhecível que não funciona mais com vários pontos de extremidade do Azure.

A solução é definir uma (ou mais) UDRs (rotas definidas pelo usuário) na sub-rede que contém Ambiente do Serviço de Aplicativo. Um UDR define rotas específicas de sub-rede que são respeitadas em vez da rota padrão.

Se possível, use a seguinte configuração:

* A configuração do ExpressRoute anuncia 0.0.0.0/0. Por padrão, a força de configuração encapsula todo o tráfego de saída no local.
* O UDR aplicado à sub-rede que contém Ambiente do Serviço de Aplicativo define 0.0.0.0/0 com um tipo de próximo salto de Internet. Um exemplo dessa configuração é descrito posteriormente neste artigo.

O efeito combinado dessa configuração é que a UDR no nível da sub-rede tem precedência sobre o túnel de força do ExpressRoute. O acesso à Internet de saída do Ambiente do Serviço de Aplicativo é garantido.

> [!IMPORTANT]
> As rotas definidas em um UDR devem ser específicas o suficiente para ter precedência sobre todas as rotas anunciadas pela configuração do ExpressRoute. O exemplo descrito na próxima seção usa o intervalo de endereços 0.0.0.0/0 amplo. Esse intervalo pode ser acidentalmente substituído por anúncios de rota que usam intervalos de endereços mais específicos.
> 
> Ambiente do Serviço de Aplicativo não tem suporte com as configurações do ExpressRoute que cruzam as rotas do caminho de emparelhamento público para o caminho de emparelhamento privado. As configurações do ExpressRoute que têm emparelhamento público configurado recebem anúncios de rota da Microsoft para um grande conjunto de Microsoft Azure intervalos de endereços IP. Se esses intervalos de endereços forem anunciados de forma cruzada no caminho de emparelhamento privado, todos os pacotes de rede de saída da sub-rede Ambiente do Serviço de Aplicativo serão disparados em túnel para a infraestrutura de rede local do cliente. Atualmente, não há suporte para esse fluxo de rede com Ambiente do Serviço de Aplicativo. Uma solução é parar as rotas de publicidade cruzada do caminho de emparelhamento público para o caminho de emparelhamento privado.
> 
> 

Para obter informações básicas sobre rotas definidas pelo usuário, consulte [Roteamento de tráfego de rede virtual][UDROverview].  

Para saber como criar e configurar rotas definidas pelo usuário, consulte [rotear o tráfego de rede com uma tabela de rotas usando o PowerShell][UDRHowTo].

## <a name="udr-configuration"></a>Configuração do UDR

Esta seção mostra um exemplo de configuração de UDR para Ambiente do Serviço de Aplicativo.

### <a name="prerequisites"></a>Pré-requisitos

* Instale o Azure PowerShell na [página de downloads do Azure][AzureDownloads]. Escolha um download com uma data de junho de 2015 ou posterior. Em **ferramentas de linha de comando** > **Windows PowerShell**, selecione **instalar** para instalar os cmdlets mais recentes do PowerShell.

* Crie uma sub-rede exclusiva para uso exclusivo por Ambiente do Serviço de Aplicativo. A sub-rede exclusiva garante que o UDRs aplicado à sub-rede Abra o tráfego de saída somente para Ambiente do Serviço de Aplicativo.

> [!IMPORTANT]
> Só implante Ambiente do Serviço de Aplicativo depois de concluir as etapas de configuração. As etapas garantem que a conectividade de rede de saída esteja disponível antes de tentar implantar Ambiente do Serviço de Aplicativo.

### <a name="step-1-create-a-route-table"></a>Etapa 1: criar uma tabela de rotas

Crie uma tabela de rotas denominada **DirectInternetRouteTable** na região oeste dos EUA do Azure, conforme mostrado neste trecho:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Etapa 2: criar rotas na tabela

Adicione rotas à tabela de rotas para habilitar o acesso de saída à Internet.  

Configure o acesso de saída à Internet. Defina uma rota para 0.0.0.0/0, conforme mostrado neste trecho:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 é um intervalo de endereços amplo. O intervalo é substituído por intervalos de endereços anunciados pelo ExpressRoute que são mais específicos. Um UDR com uma rota 0.0.0.0/0 deve ser usado em conjunto com uma configuração do ExpressRoute que anuncia somente 0.0.0.0/0. 

Como alternativa, Baixe uma lista atual e abrangente de intervalos CIDR em uso pelo Azure. O arquivo XML para todos os intervalos de endereços IP do Azure está disponível no [centro de download da Microsoft][DownloadCenterAddressRanges].  

> [!NOTE]
>
> Os intervalos de endereços IP do Azure mudam com o tempo. As rotas definidas pelo usuário precisam de atualizações manuais periódicas para manter a sincronização.
>
> Um único UDR tem um limite superior padrão de 100 rotas. Você precisa "resumir" os intervalos de endereços IP do Azure para se ajustarem no limite de rota de 100. As rotas definidas pelo UDR precisam ser mais específicas do que as rotas anunciadas pela conexão do ExpressRoute.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Etapa 3: associar a tabela à sub-rede

Associe a tabela de rotas à sub-rede em que você pretende implantar Ambiente do Serviço de Aplicativo. Esse comando associa a tabela **DirectInternetRouteTable** à sub-rede **ASESubnet** que conterá ambiente do serviço de aplicativo.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Etapa 4: testar e confirmar a rota

Depois que a tabela de rotas estiver associada à sub-rede, teste e confirme a rota.

Implante uma máquina virtual na sub-rede e confirme estas condições:

* O tráfego de saída para os pontos de extremidade do Azure e não Azure descritos neste artigo **não flui para** baixo no circuito do ExpressRoute. Se o tráfego de saída da sub-rede for forçado em túnel local, a criação de Ambiente do Serviço de Aplicativo sempre falhará.
* As pesquisas de DNS para os pontos de extremidade descritos neste artigo são resolvidas corretamente. 

Depois de concluir as etapas de configuração e confirmar a rota, exclua a máquina virtual. A sub-rede precisa ser "vazia" quando Ambiente do Serviço de Aplicativo é criada.

Agora você está pronto para implantar Ambiente do Serviço de Aplicativo!

## <a name="next-steps"></a>Passos seguintes

Para começar a usar o Ambiente do Serviço de Aplicativo para PowerApps, consulte [introdução ao ambiente do serviço de aplicativo][IntroToAppServiceEnvironment].

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
