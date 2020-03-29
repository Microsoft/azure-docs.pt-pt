---
title: Integrar app com rede virtual Azure
description: Integre aplicações no Serviço de Aplicações Azure com redes virtuais Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673212"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar uma aplicação Web com uma Rede Virtual do Azure
Este documento descreve a funcionalidade de integração de redes virtuais do Azure App Service e como configurar com aplicações no Serviço de [Aplicações Azure.](https://go.microsoft.com/fwlink/?LinkId=529714) As [Redes Virtuais Azure][VNETOverview] (VNets) permitem colocar muitos dos seus recursos Azure numa rede não-internet de saída.  

O Serviço de Aplicações Azure tem duas variações.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Ativar a Integração VNet 

1. Vá ao Networking UI no portal do Serviço de Aplicações. Em Integração VNet, selecione *"Clique aqui para configurar"*. 

1. **Selecione Adicionar VNet**.  

   ![Selecione Integração VNet][1]

1. A lista de drop-down incluirá todos os VNets do Gestor de Recursos na sua subscrição na mesma região e abaixo que é uma lista de todos os VNets gestorde recursos em todas as outras regiões. Selecione o VNet com o que pretende integrar.

   ![Selecione o VNet][2]

   * Se o VNet estiver na mesma região, então crie uma nova subnet a ou escolha uma sub-rede pré-existente vazia. 

   * Para selecionar um VNet noutra região, deve ter um gateway de rede virtual aprovisionado com ponto para site habilitado.

   * Para integrar com um VNet Clássico, em vez de clicar na queda do VNet, selecione **Clique aqui para ligar a um VNet clássico**. Selecione o VNet Clássico desejado. O alvo VNet já deve ter uma rede virtual de gateway aprovisionada com ponto para site habilitado.

    ![Selecione Classic VNet][3]
    
Durante a integração, a sua aplicação é reiniciada.  Quando a integração estiver concluída, verá detalhes no VNet com os quais está integrado. 

Uma vez que a sua aplicação esteja integrada com o seu VNet, utilizará o mesmo servidor DNS com o qual o seu VNet está configurado, a menos que seja o Azure DNS Private Zones. Atualmente não pode utilizar a Integração VNet com zonas privadas Azure DNS.

## <a name="regional-vnet-integration"></a>Integração Regional de VNet

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Como funciona a Integração Regional vNet

As aplicações no Serviço de Aplicações são hospedadas em funções de trabalhador. Os planos de preços básicos e mais elevados são planos de hospedagem dedicados onde não há outros clientes a funcionar com os mesmos trabalhadores. A Integração VNet Regional funciona através da montagem de interfaces virtuais com endereços na sub-rede delegada. Como o endereço a partir está no seu VNet, pode aceder à maioria das coisas dentro ou através do seu VNet, tal como um VM no seu VNet faria. A implementação em rede é diferente de executar um VM no seu VNet e é por isso que algumas funcionalidades de networking ainda não estão disponíveis durante a utilização desta funcionalidade.

![Como funciona a Integração VNet regional][5]

Quando a Integração VNet regional estiver ativada, a sua aplicação ainda fará chamadas de saída para a internet através dos mesmos canais que o normal. Os endereços de saída que estão listados no portal de propriedades da aplicação ainda são os endereços utilizados pela sua aplicação. What changes for your app are, calls to service endpoint secured services or RFC 1918 addresses goes into your VNet. Se WEBSITE_VNET_ROUTE_ALL está definido para 1, todo o tráfego de saída pode ser enviado para o seu VNet. 

A funcionalidade suporta apenas uma interface virtual por trabalhador.  Uma interface virtual por trabalhador significa uma Integração VNet regional por plano de serviço de aplicações. Todas as aplicações do mesmo plano de Serviço de Aplicações podem usar a mesma Integração VNet, mas se precisar de uma aplicação para se ligar a um VNet adicional, terá de criar outro plano de Serviço de Aplicações. A interface virtual utilizada não é um recurso a que os clientes tenham acesso direto.

Devido à natureza do funcionamento desta tecnologia, o tráfego que é utilizado com a Integração VNet não aparece nos registos de fluxo do Observador de Rede ou nsg.  

## <a name="gateway-required-vnet-integration"></a>Gateway exigia integração VNet

Gateway exigiu suportes de Integração VNet que se conectam a um VNet em outra região, ou a um VNet clássico. Gateway exigia integração VNet: 

* Permite que uma aplicação se conectem a apenas 1 VNet de cada vez
* Permite que até cinco VNets sejam integrados dentro de um Plano de Serviço de Aplicações 
* Permite que o mesmo VNet seja utilizado por várias aplicações num Plano de Serviço de Aplicações sem afetar o número total que pode ser usado por um plano de Serviço de Aplicações.  Se tiver seis aplicações usando o mesmo VNet no mesmo plano de Serviço de Aplicações, isso conta como 1 VNet sendo usado. 
* Suporta um SLA de 99,9% devido ao SLA na porta de entrada
* Permite que as suas apps utilizem o DNS com o que o VNet está configurado com
* Requer um gateway baseado em rota da Rede Virtual configurado com VPN ponto-a-site SSTP antes de poder ser ligado à aplicação. 

Não pode utilizar a integração vnet necessária ao gateway:

* Com aplicativos Linux
* Com uma VNet ligada com expressRoute 
* Para aceder ao Serviço Endpoints garantiu recursos
* Com um gateway de coexistência que suporta tanto a ExpressRoute como aponta para o site/site para o site VPNs

### <a name="set-up-a-gateway-in-your-vnet"></a>Estabeleça uma porta de entrada no seu VNet ###

Para criar um portal:

1. [Crie uma sub-rede][creategatewaysubnet] de porta no seu VNet.  

1. [Crie o gateway VPN][creategateway]. Selecione um tipo VPN baseado em rota.

1. [Detete o ponto para os endereços][setp2saddresses]do site . Se o portal não estiver no SKU básico, então o IKEV2 deve ser desativado no ponto para a configuração do local e o SSTP deve ser selecionado. O espaço de endereço seleto rfc 1918 deve estar nos blocos de endereços RFC 1918, 10.0.0.0/8, 172.16.0.0.0/12, 192.168.0.0/16

Se está apenas a criar a porta de entrada para utilização com a Integração VNet do Serviço de Aplicações, então não precisa de carregar um certificado. Criar o portal pode levar 30 minutos. Não poderá integrar a sua aplicação com o seu VNet até que o portal esteja aprovisionado. 

### <a name="how-gateway-required-vnet-integration-works"></a>Como funciona a Integração VNet necessária

Gateway exigiu a Integração VNet construída em cima do ponto para a tecnologia VPN do site. Apontar para o site VPNs limita o acesso da rede apenas à máquina virtual que hospeda a aplicação. As aplicações limitam-se apenas a enviar tráfego para a internet, através de Conexões Híbridas ou através da Integração VNet. Quando a sua aplicação está configurada com o portal para utilizar a integração vnet necessária, uma negociação complexa é gerida em seu nome para criar e atribuir certificados no gateway e no lado da aplicação. O resultado final é que os trabalhadores utilizados para hospedar as suas apps são capazes de se conectar diretamente com o gateway da rede virtual no VNet selecionado. 

![Como funciona a Integração VNet necessária][6]

### <a name="accessing-on-premises-resources"></a>Accessing on-premises resources (Aceder a recursos no local)

As aplicações podem aceder aos recursos no local integrando-se com VNets que têm ligações site-to-site. Se estiver a utilizar o gateway necessário para a Integração VNet, precisa de atualizar as suas rotas de gateway VPN no local com os seus blocos de endereços ponto-a-local. Quando o VPN site-to-site é configurado pela primeira vez, os scripts utilizados para configurar devem configurar as rotas corretamente. Se adicionar os endereços ponto-a-site depois de criar o seu VPN site-to-site, precisa de atualizar as rotas manualmente. Detalhes sobre como fazê-lo variam por porta de entrada e não são descritos aqui. Não é possível configurar o BGP com uma ligação VPN site-to-site.

Não é necessária uma configuração adicional para que a funcionalidade regional de Integração VNet alcance através do seu VNet e no local. Basta ligar o seu VNet às instalações utilizando o ExpressRoute ou uma VPN site-to-site. 

> [!NOTE]
> A funcionalidade de Integração VNet necessária não integra uma aplicação com um VNet que tenha um Gateway ExpressRoute. Mesmo que o ExpressRoute Gateway esteja configurado no modo de [coexistência,][VPNERCoex] a Integração VNet não funciona. Se precisar de aceder a recursos através de uma ligação ExpressRoute, então pode utilizar a funcionalidade regional de Integração VNet ou um Ambiente de Serviço de [Aplicações,][ASE]que corre no seu VNet. 
> 
> 

### <a name="peering"></a>Peering

Se estiver a utilizar o peering com a Integração VNet regional, não precisa de fazer nenhuma configuração adicional. 

Se estiver a utilizar o gateway necessário para a Integração VNet com o peering, precisa de configurar alguns itens adicionais. Para configurar o epeering para trabalhar com a sua app:

1. Adicione uma ligação de observação no VNet a que a sua aplicação se liga. Ao adicionar a ligação de peering, ative **permitir o acesso** à rede virtual e verificar Permitir tráfego **encaminhada** e permitir o trânsito de **gateway**.
1. Adicione uma ligação de observação no VNet que está a ser espreitado para o VNet a que está ligado. Ao adicionar a ligação de observação no destino VNet, ative **permitir o acesso** à rede virtual e verificar Permitir tráfego **reencaminhado** e **permitir gateways remotos**.
1. Vá ao plano de serviço de aplicações > Networking > VNet Integration UI no portal.  Selecione o VNet a que a sua aplicação se liga. Na secção de encaminhamento, adicione a gama de endereços do VNet que é analisado com o VNet a que a sua aplicação está ligada.  

## <a name="managing-vnet-integration"></a>Gestão da Integração VNet 

A ligação e desconexão com um VNet está a um nível de aplicação. As operações que podem afetar a Integração VNet em várias aplicações estão ao nível do plano do App Service. A partir da aplicação > Networking > portal de Integração VNet, pode obter detalhes no seu VNet. Pode ver informações semelhantes ao nível asp no asp > portal de integra >ção VNet.

A única operação que pode tomar na visão da aplicação da sua Integração VNet é desligar a sua aplicação do VNet a que está atualmente ligada. Para desligar a sua aplicação de um VNet, selecione **Desligar**. A sua aplicação será reiniciada quando desligar de um VNet. Desligar não muda o seu VNet. A sub-rede ou porta de entrada não é removida. Se pretender eliminar o seu VNet, tem primeiro de desligar a sua aplicação do VNet e eliminar os recursos que nele se advoquem, como gateways. 

O ASP VNet Integration UI irá mostrar-lhe todas as integrações VNet utilizadas pelas aplicações no seu ASP. Para ver detalhes em cada VNet, clique no VNet que lhe interessa. Existem duas ações que pode realizar aqui para a integração VNet necessária.

* **Rede sync**. O funcionamento da rede de sincronização destina-se apenas à funcionalidade de Integração VNet dependente de gateway. A realização de uma operação de rede de sincronização garante que os seus certificados e informações de rede estão sincronizados. Se adicionar ou alterar o DNS do seu VNet, tem de executar uma operação de **rede Sync.** Esta operação reiniciará todas as aplicações que utilizem este VNet.
* **Adicionar rotas** A adição de rotas conduzirá o tráfego de saída para o seu VNet. 

**Gateway exigia encaminhamento de integração VNet** As rotas definidas no seu VNet são usadas para direcionar o tráfego para o seu VNet a partir da sua aplicação. Se precisar enviar tráfego adicional de saída para o VNet, então pode adicionar os blocos de endereços aqui. Esta capacidade funciona apenas com a entrada necessária para a Integração VNet. As tabelas de rotas não afetam o tráfego da sua aplicação quando utilizam o gateway necessário para a Integração VNet da forma como fazem com a Integração VNet regional.

**Gateway exigia certificados de integração VNet** Quando o gateway exigia a Integração VNet ativada, existe uma troca de certificados necessária para garantir a segurança da ligação. Juntamente com os certificados estão a configuração dNS, rotas e outras coisas similares que descrevem a rede.
Se os certificados ou informações de rede forem alterados, tem de clicar em "Sync Network". Ao clicar em "Sync Network", causa uma breve falha na conectividade entre a sua aplicação e o seu VNet. Embora a sua aplicação não seja reiniciada, a perda de conectividade pode fazer com que o seu site não funcione corretamente. 

## <a name="pricing-details"></a>Detalhes dos preços
A funcionalidade regional de Integração VNet não tem qualquer custo adicional para utilização para além dos encargos de nível de preços ASP.

Existem três encargos relacionados com a utilização da funcionalidade de integração VNet necessária:

* Taxas de nível de preços ASP - As suas aplicações precisam de estar num Plano de Serviço de Aplicações Standard, Premium ou PremiumV2. Pode ver mais detalhes sobre esses custos aqui: Preços do Serviço de [Aplicações][ASPricing]. 
* Custos de transferência de dados - Existe um custo para a saída de dados, mesmo que o VNet esteja no mesmo centro de dados. Estes encargos são descritos em Detalhes de [Preços de Transferência][DataPricing]de Dados . 
* CUSTOS VPN Gateway - Existe um custo para o gateway VNet que é necessário para o ponto-a-site VPN. Os detalhes estão na página de Preços do [Gateway VPN.][VNETPricing]

## <a name="troubleshooting"></a>Resolução de problemas

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automatização

Existe apoio cli para integração regional vnet. Para aceder aos seguintes comandos, [instale o Azure CLI][installCLI]. 

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

Para a integração vnet necessária ao gateway, pode integrar o Serviço de Aplicações com uma Rede Virtual Azure utilizando o PowerShell. Para um script pronto a ser executado, consulte [Connect uma aplicação no Azure App Service para uma Rede Virtual Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
