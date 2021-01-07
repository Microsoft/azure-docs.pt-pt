---
title: Integrar app com Azure Virtual Network
description: Integrar aplicativo no Azure App Service com redes virtuais Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 077d200dcaf957f636acecebb441ff99a68eb96f
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963592"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar a aplicação numa rede virtual do Azure

Este artigo descreve a funcionalidade de Integração VNet do Serviço de Aplicações Azure e como conehá-la com aplicações no [Azure App Service](./overview.md). Com [a Rede Virtual Azure][VNETOverview] (VNets), pode colocar muitos dos seus recursos Azure numa rede não-internet-routable. A funcionalidade de Integração VNet permite que as suas aplicações acedam a recursos dentro ou através de um VNet. A Integração VNet não permite que as suas aplicações sejam acedidas em privado.

O Azure App Service tem duas variações na funcionalidade de Integração VNet:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Ativar a integração do VNet

1. Aceda ao **UI de Rede** no portal do Serviço de Aplicações. Em **Integração VNet,** selecione **Clique aqui para configurar**.

1. **Selecione Adicionar VNet**.

   ![Selecione integração VNet][1]

1. A lista de drop-down contém todas as redes virtuais do Azure Resource Manager na sua subscrição na mesma região. Por baixo disso está uma lista das redes virtuais do Gestor de Recursos em todas as outras regiões. Selecione o VNet com o quais pretende integrar-se.

   ![Selecione o VNet][2]

   * Se o VNet estiver na mesma região, ou crie uma nova sub-rede ou selecione uma sub-rede pré-existente vazia.
   * Para selecionar um VNet noutra região, tem de ter um gateway VNet atado com o ponto para o site ativado.
   * Para se integrar com um VNet clássico, em vez de selecionar a lista de drop-down **da Rede Virtual,** selecione **Clique aqui para ligar a um VNet clássico**. Selecione a rede virtual clássica que deseja. O VNet alvo já deve ter um gateway de rede virtual a provisionado com o ponto a local ativado.

    ![Selecione VNet Clássico][3]

Durante a integração, a sua aplicação é reiniciada. Quando a integração estiver terminada, verá detalhes sobre o VNet com o qual está integrado.

## <a name="regional-vnet-integration"></a>Integração Regional de VNet

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Como funciona a Integração Regional de VNet

As aplicações no Serviço de Aplicações estão hospedadas em funções de trabalhador. Os planos básicos e de preços mais elevados são planos de hospedagem dedicados onde não existem cargas de trabalho de outros clientes a funcionar nos mesmos trabalhadores. A Integração Regional de VNet funciona através da montagem de interfaces virtuais com endereços na sub-rede delegada. Como o endereço está no seu VNet, pode aceder à maioria das coisas dentro ou através do seu VNet como um VM no seu VNet. A implementação em rede é diferente de executar um VM no seu VNet. É por isso que algumas funcionalidades de networking ainda não estão disponíveis para esta funcionalidade.

![Como funciona a Integração Regional de VNet][5]

Quando a Integração Regional de VNet está ativada, a sua aplicação faz chamadas de saída para a internet através dos mesmos canais que o normal. Os endereços de saída listados no portal das propriedades da aplicação são os endereços ainda utilizados pela sua aplicação. O que muda para a sua aplicação são as chamadas para serviços de serviço seguros de ponto final, ou endereços RFC 1918 vão para o seu VNet. Se WEBSITE_VNET_ROUTE_ALL estiver definido para 1, todo o tráfego de saída pode ser enviado para o seu VNet.

> [!NOTE]
> `WEBSITE_VNET_ROUTE_ALL` atualmente, não é suportado em recipientes Windows.
> 

A funcionalidade suporta apenas uma interface virtual por trabalhador. Uma interface virtual por trabalhador significa uma Integração VNet regional por plano de Serviço de Aplicação. Todas as aplicações do mesmo plano de Serviço de Aplicações podem utilizar a mesma Integração VNet. Se precisar de uma aplicação para se ligar a um VNet adicional, precisa de criar outro plano de Serviço de Aplicações. A interface virtual utilizada não é um recurso a que os clientes tenham acesso direto.

Devido à natureza do funcionamento desta tecnologia, o tráfego que é usado com a Integração VNet não aparece nos registos de fluxo da Rede Azure ou NSG.

## <a name="gateway-required-vnet-integration"></a>Integração VNet exigida pelo Gateway

A Integração VNet exigida pelo Gateway suporta a ligação a um VNet noutra região ou a uma rede virtual clássica. Integração VNet exigida pelo Gateway:

* Permite que uma aplicação se conecte a apenas um VNet de cada vez.
* Permite a integração de até cinco VNets num plano de Serviço de Aplicações.
* Permite que o mesmo VNet seja usado por várias aplicações num plano de Serviço de Aplicações sem afetar o número total que pode ser usado por um plano de Serviço de Aplicações. Se tiver seis aplicações usando o mesmo VNet no mesmo plano de Serviço de Aplicações, isso conta como um VNet a ser usado.
* Suporta um SLA de 99,9% devido ao SLA no gateway.
* Permite que as suas aplicações utilizem o DNS com o qual o VNet está configurado.
* Requer um gateway baseado em rede virtual configurado com uma VPN ponto-a-local SSTP antes de poder ser ligado a uma aplicação.

Não pode utilizar a integração VNet exigida por gateways:

* Com um VNet ligado ao Azure ExpressRoute.
* De uma aplicação Linux.
* De um [recipiente windows](quickstart-custom-container.md).
* Para aceder ao ponto final do serviço, recursos seguros.
* Com uma porta de entrada de coexistência que suporta tanto o ExpressRoute como as VPNs ponto-a-local ou site-to-site.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Crie um portal na sua rede virtual Azure ###

Para criar uma porta de entrada:

1. [Crie uma sub-rede de gateway][creategatewaysubnet] no seu VNet.

1. [Crie o gateway VPN][creategateway]. Selecione um tipo VPN baseado em rota.

1. [Desaponte os endereços ponto a local][setp2saddresses]. Se o gateway não estiver no SKU básico, então o IKEV2 deve ser desativado na configuração ponto-a-local e o SSTP deve ser selecionado. O espaço de endereço ponto-a-local deve estar nos blocos de endereços RFC 1918 10.0.0.0/8, 172.16.0.0/12 e 192.168.0.0/16.

Se criar o portal para utilização com a Integração VNet do Serviço de Aplicações, não precisa de carregar um certificado. Criar o portal pode demorar 30 minutos. Não poderá integrar a sua aplicação com o seu VNet até que o gateway seja aprovisionado.

### <a name="how-gateway-required-vnet-integration-works"></a>Como funciona a integração VNet exigida por gateways

A Integração VNet exigida pelo Gateway é construída em cima da tecnologia VPN ponto-a-local. As VPNs ponto-a-local limitam o acesso à rede à máquina virtual que acolhe a aplicação. As aplicações são restritas para enviar tráfego para a internet apenas através de Conexões Híbridas ou através da Integração VNet. Quando a sua aplicação está configurada com o portal para utilizar a Integração VNet exigida por gateways, uma negociação complexa é gerida em seu nome para criar e atribuir certificados no gateway e no lado da aplicação. O resultado é que os trabalhadores utilizados para hospedar as suas apps são capazes de ligar diretamente ao gateway de rede virtual no VNet selecionado.

![Como funciona a integração VNet exigida por gateways][6]

### <a name="access-on-premises-resources"></a>Acesso aos recursos no local

As aplicações podem aceder aos recursos no local, integrando-se com VNets que têm ligações site-to-site. Se utilizar a Integração VNet exigida por gateways, atualize as suas rotas de gateway VPN no local com os seus blocos de endereços ponto-a-local. Quando a VPN site-to-site é configurada pela primeira vez, os scripts utilizados para configurar as rotas devem configurar as rotas corretamente. Se adicionar os endereços ponto-a-local depois de criar a VPN do site, tem de atualizar as rotas manualmente. Detalhes sobre como fazer isso variam por gateway e não são descritos aqui. Não pode ter bGP configurado com uma ligação VPN site-to-site.

Não é necessária nenhuma configuração adicional para que a funcionalidade regional de Integração VNet atinja através do seu VNet para recursos no local. Basta ligar o seu VNet aos recursos no local, utilizando o ExpressRoute ou uma VPN site-to-site.

> [!NOTE]
> A funcionalidade de Integração VNet exigida por gateway não integra uma aplicação com um VNet que tenha um gateway ExpressRoute. Mesmo que o gateway ExpressRoute esteja configurado em [modo de coexistência,][VPNERCoex]a Integração VNet não funciona. Se precisar de aceder aos recursos através de uma ligação ExpressRoute, utilize a funcionalidade regional de Integração VNet ou um [Ambiente de Serviço de Aplicações][ASE], que funciona no seu VNet.
>
>

### <a name="peering"></a>Peering

Se utilizar o seu olhar com a Integração Regional de VNet, não precisa de fazer nenhuma configuração adicional.

Se utilizar a Integração VNet exigida por gateways com o seu espremia, tem de configurar alguns itens adicionais. Para configurar olhando para trabalhar com a sua aplicação:

1. Adicione uma ligação de espreitar no VNet a que a sua aplicação se conecta. Ao adicionar a ligação de pares, ative **o acesso à rede virtual** e selecione Deixe o tráfego **reencaminhado** e **permita o trânsito de gateway**.
1. Adicione uma ligação de observação no VNet que está a ser espreitada para o VNet a que está ligado. Quando adicionar a ligação de espreitamento no destino VNet, ative **o acesso à rede virtual** e selecione Deixe o tráfego **reencaminhado** e **permita gateways remotos**.
1. Aceda ao **plano de Serviço** de  >  **Aplicações networking**  >  **VNet Integration** UI no portal. Selecione o VNet a que a sua aplicação se conecta. Na secção de encaminhamento, adicione o intervalo de endereços do VNet que está espreguiçadado com o VNet a que a sua aplicação está ligada.

## <a name="manage-vnet-integration"></a>Gerir a Integração da VNet

A ligação e desconexão com um VNet encontra-se ao nível da aplicação. As operações que podem afetar a integração do VNet em várias aplicações estão ao nível do plano do Serviço de Aplicações. A partir da aplicação > portal **de**  >  **Integração VNet** em rede, pode obter detalhes sobre o seu VNet. Pode ver informações semelhantes ao nível do plano do Serviço de Aplicações no portal de Integração VNet do **plano** de serviço de  >    >   aplicações.

A única operação que pode ter na visão da aplicação da sua instância de Integração VNet é desligar a sua aplicação do VNet a que está atualmente ligada. Para desligar a sua aplicação de um VNet, **selecione Disconnect**. A sua aplicação é reiniciada quando desliga de um VNet. Desligar não muda o seu VNet. A sub-rede ou gateway não é removida. Se quiser então eliminar o seu VNet, desligue primeiro a sua aplicação do VNet e elimine os recursos nele, como os gateways.

O plano de Serviço de Aplicações VNet Integration UI mostra-lhe todas as integrações VNet utilizadas pelas aplicações no seu plano de Serviço de Aplicações. Para ver detalhes em cada VNet, selecione o VNet em que está interessado. Existem duas ações que pode realizar aqui para integração VNet exigida por gateway:

* **Rede sincronizada**: A operação de rede de sincronização é utilizada apenas para a função de Integração VNet dependente de gateways. A realização de uma operação de rede de sincronização garante que os seus certificados e informações de rede estão sincronizados. Se adicionar ou alterar o DNS do seu VNet, efetue uma operação de rede de sincronização. Esta operação reinicia quaisquer aplicações que utilizem este VNet. Esta operação não funcionará se estiver a utilizar uma app e um vnet pertencentes a diferentes subscrições.
* **Adicionar rotas**: Adicionar rotas conduz o tráfego de saída para o seu VNet.

O IP privado atribuído ao caso é exposto através da variável ambiental, **WEBSITE_PRIVATE_IP**. A UI da consola Kudu também mostra a lista de variáveis ambientais disponíveis para a Web App. Este IP é atribuído a partir do intervalo de endereços da sub-rede integrada. Para a Integração Regional de VNet, o valor de WEBSITE_PRIVATE_IP é um IP a partir do intervalo de endereços da sub-rede delegada, e para a integração VNet exigida pelo Gateway, o valor é um IP a partir da gama de endereços do conjunto de endereços ponto-a-local configurado no Gateway de Rede Virtual. Este é o IP que será usado pela Web App para se conectar aos recursos através da Rede Virtual. 

> [!NOTE]
> O valor do WEBSITE_PRIVATE_IP vai mudar. No entanto, será um IP dentro do intervalo de endereços da sub-rede de integração ou da gama de endereços ponto a local, pelo que terá de permitir o acesso a partir de toda a gama de endereços.
>

### <a name="gateway-required-vnet-integration-routing"></a>Encaminhamento de integração VNet exigido pela Porta de entrada
As rotas definidas no seu VNet são usadas para direcionar o tráfego para o seu VNet a partir da sua aplicação. Para enviar tráfego adicional de saída para o VNet, adicione os blocos de endereços aqui. Esta capacidade funciona apenas com a integração VNet exigida por gateways. As tabelas de rotas não afetam o tráfego da sua aplicação quando utiliza a Integração VNet exigida pelo gateway da forma que fazem com a Integração Regional de VNet.

### <a name="gateway-required-vnet-integration-certificates"></a>Certificados de integração VNet exigidos pelo Gateway
Quando a integração VNet exigida pelo gateway está ativada, há uma troca obrigatória de certificados para garantir a segurança da ligação. Juntamente com os certificados estão a configuração de DNS, rotas e outras coisas semelhantes que descrevem a rede.

Se os certificados ou informações de rede forem alterados, selecione **Sync Network**. Quando seleciona **a Sync Network,** causa uma breve interrupção na conectividade entre a sua aplicação e o seu VNet. Embora a sua aplicação não seja reiniciada, a perda de conectividade pode fazer com que o seu site não funcione corretamente.

## <a name="pricing-details"></a>Detalhes dos preços
A funcionalidade regional de Integração VNet não tem qualquer custo adicional para utilização para além dos custos de preços do plano de preços do App Service.

Três encargos estão relacionados com a utilização da funcionalidade de Integração VNet exigida por gateway:

* **Taxas de preços do plano app**: As suas aplicações precisam de estar num plano standard, premium, premiumV2 ou premiumV3 app Service. Para obter mais informações sobre estes custos, consulte [os preços do Serviço de Aplicações.][ASPricing]
* **Custos de transferência** de dados : Há um custo para a saída de dados, mesmo que o VNet esteja no mesmo centro de dados. Estes encargos são descritos em [detalhes de preços de transferência de dados.][DataPricing]
* **Custos de gateway VPN**: Há um custo para o gateway de rede virtual que é necessário para a VPN ponto-a-local. Para obter mais informações, consulte [os preços da porta de entrada VPN.][VNETPricing]

## <a name="troubleshooting"></a>Resolução de problemas

> [!NOTE]
> A integração do VNET não é suportada para cenários Docker Compose no Serviço de Aplicações.
> As restrições de acesso a funções Azure são ignoradas se houver um ponto final privado presente.
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automatização

O suporte CLI está disponível para integração regional de VNet. Para aceder aos seguintes comandos, [instale o Azure CLI][installCLI].

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.

az appservice vnet-integration --help

Group
    az appservice vnet-integration : A method that lists the virtual network
    integrations used in an appservice plan.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    list : List the virtual network integrations used in an appservice plan.
```

O suporte powerShell para a integração regional do VNet também está disponível, mas você deve criar um recurso genérico com um conjunto de propriedade do recurso subnetID

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

#Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

#Creation of the VNet integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```


Para a integração VNet exigida por gateways, pode integrar o Serviço de Aplicações com uma rede virtual Azure utilizando o PowerShell. Para um script pronto a executar, consulte [uma aplicação no Azure App Service a uma rede virtual Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli?view=azure-cli-latest%2f
[privateendpoints]: networking/private-endpoint.md
