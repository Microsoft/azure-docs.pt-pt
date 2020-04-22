---
title: Integrar app com rede virtual Azure
description: Integrar app no Azure App Service com redes virtuais Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 04/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 78b49b8b7e17f12d49825390a302e28a61e10d16
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770845"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integre a sua app com uma rede virtual Azure

Este artigo descreve a funcionalidade de Integração VNet do Serviço de Aplicações Azure e como configurar com aplicações no [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Com a [Rede Virtual Azure][VNETOverview] (VNets), pode colocar muitos dos seus recursos Azure numa rede não-de-internet.

O Azure App Service tem duas variações:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Ativar a Integração VNet

> [!NOTE]
> Se a lâmina "Networking" estiver desativada (cinzenta) no menu das suas aplicações Linux, significa que a funcionalidade não está disponível.
>

1. Vá ao **Networking** UI no portal do Serviço de Aplicações. Em **integração VNet,** selecione **Clique aqui para configurar**.

1. **Selecione Adicionar VNet**.

   ![Selecione Integração VNet][1]

1. A lista de drop-down contém todas as redes virtuais do Gestor de Recursos Azure na sua subscrição na mesma região. Por baixo disso está uma lista das redes virtuais do Gestor de Recursos em todas as outras regiões. Selecione o VNet com o que pretende integrar.

   ![Selecione o VNet][2]

   * Se o VNet estiver na mesma região, crie uma nova subnet a lém-venda ou selecione uma subrede pré-existente vazia.
   * Para selecionar um VNet noutra região, deve ter um gateway VNet aprovisionado com ponto para local ativado.
   * Para integrar com um VNet clássico, em vez de selecionar a lista de drop-down **da Rede Virtual,** selecione **Clique aqui para se ligar a um VNet Clássico**. Selecione a rede virtual clássica que deseja. O alvo VNet já deve ter uma rede virtual de gateway aprovisionada com o ponto-a-local ativado.

    ![Selecione Classic VNet][3]

Durante a integração, a sua aplicação é reiniciada. Quando a integração estiver concluída, verá detalhes no VNet com os quais está integrado.

## <a name="regional-vnet-integration"></a>Integração Regional de VNet

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Como funciona a Integração VNet regional

As aplicações no Serviço de Aplicações são hospedadas nas funções dos trabalhadores. Os planos de preços básicos e mais elevados são planos de hospedagem dedicados onde não existem cargas de trabalho de outros clientes a funcionar com os mesmos trabalhadores. A Integração VNet Regional funciona através da montagem de interfaces virtuais com endereços na sub-rede delegada. Como o endereço a partir está no seu VNet, pode aceder à maioria das coisas dentro ou através do seu VNet como um VM no seu VNet faria. A implementação em rede é diferente de executar um VM no seu VNet. É por isso que algumas funcionalidades de networking ainda não estão disponíveis para esta funcionalidade.

![Como funciona a Integração VNet regional][5]

Quando a Integração VNet regional está ativada, a sua aplicação faz chamadas de saída para a internet através dos mesmos canais que o normal. Os endereços de saída que estão listados no portal de propriedades da aplicação são os endereços ainda utilizados pela sua aplicação. What changes for your app are the calls to service endpoint secured services, or RFC 1918 addresses go into your VNet. Se WEBSITE_VNET_ROUTE_ALL estiver definido para 1, todo o tráfego de saída pode ser enviado para o seu VNet.

A funcionalidade suporta apenas uma interface virtual por trabalhador. Uma interface virtual por trabalhador significa uma Integração VNet regional por plano de serviço de aplicações. Todas as aplicações do mesmo plano de Serviço de Aplicações podem usar a mesma Integração VNet. Se precisa de uma aplicação para se ligar a um VNet adicional, precisa de criar outro plano de Serviço de Aplicações. A interface virtual utilizada não é um recurso a que os clientes tenham acesso direto.

Devido à natureza do funcionamento desta tecnologia, o tráfego que é usado com a Integração VNet não aparece nos registos de fluxo do Azure Network Watcher ou nsG.

## <a name="gateway-required-vnet-integration"></a>Integração VNet exigida por gateway

A VNet Integration exigida por Gateway suporta a ligação a um VNet noutra região ou a uma rede virtual clássica. Integração VNet exigida por gateway:

* Permite que uma aplicação se conectem a apenas um VNet de cada vez.
* Permite que até cinco VNets sejam integrados dentro de um plano de Serviço de Aplicações.
* Permite que o mesmo VNet seja utilizado por várias aplicações num plano de Serviço de Aplicações sem afetar o número total que pode ser usado por um plano de Serviço de Aplicações. Se tiver seis aplicações usando o mesmo VNet no mesmo plano de Serviço de Aplicações, isso conta como uma VNet a ser utilizada.
* Suporta um SLA de 99,9% devido ao SLA na porta de entrada.
* Permite que as suas aplicações utilizem o DNS com o que o VNet está configurado.
* Requer um gateway baseado em rota da Rede Virtual configurado com uma VPN ponto-a-site SSTP antes de poder ser ligado a uma aplicação.

Não pode utilizar a Integração VNet exigida pelo portal:

* Com aplicativos Linux.
* Com uma VNet ligada à Azure ExpressRoute.
* Para aceder ao serviço endpoint recursos garantidos.
* Com um portal de coexistência que suporta tanto a ExpressRoute como as VPNs ponto-a-site ou site-to-site.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Estabeleça uma porta de entrada na sua rede virtual Azure ###

Para criar um portal:

1. [Crie uma sub-rede][creategatewaysubnet] de porta no seu VNet.  

1. [Crie o gateway VPN][creategateway]. Selecione um tipo VPN baseado em rota.

1. [Definir os endereços ponto-a-local][setp2saddresses]. Se o portal não estiver no SKU básico, então o IKEV2 deve ser desativado na configuração ponto-a-local e o SSTP deve ser selecionado. O espaço de endereço seleto rfc 1918 deve estar nos blocos de endereços RFC 10.0.0.0.0.8, 172.16.0.0.0/12 e 192.168.0.0.0/16.

Se criar a porta de entrada para utilização com a Integração VNet do Serviço de Aplicações, não precisa de carregar um certificado. Criar o portal pode levar 30 minutos. Não poderá integrar a sua aplicação com o seu VNet até que o portal esteja provisionado.

### <a name="how-gateway-required-vnet-integration-works"></a>Como funciona a Integração VNet exigida por gateway

A VNet Integration exigida por gateway supor-se em topo da tecnologia VPN ponto-a-local. As VPNs ponto-a-site limitam o acesso à rede à máquina virtual que acolhe a aplicação. As aplicações são restritas a enviar tráfego para a internet apenas através de Conexões Híbridas ou através da Integração VNet. Quando a sua aplicação está configurada com o portal para utilizar a Integração VNet exigida por gateway, uma negociação complexa é gerida em seu nome para criar e atribuir certificados no gateway e no lado da aplicação. O resultado é que os trabalhadores utilizados para hospedar as suas apps são capazes de se conectar diretamente com o gateway de rede virtual no VNet selecionado.

![Como funciona a Integração VNet exigida por gateway][6]

### <a name="access-on-premises-resources"></a>Acesso aos recursos no local

As aplicações podem aceder aos recursos no local integrando-se com VNets que têm ligações site-to-site. Se utilizar a Integração VNet exigida pelo gateway, atualize as suas rotas de gateway VPN no local com os seus blocos de endereços ponto-a-local. Quando o VPN site-to-site é configurado pela primeira vez, os scripts utilizados para configurar devem configurar as rotas corretamente. Se adicionar os endereços ponto-a-site depois de criar o seu VPN site-to-site, precisa de atualizar as rotas manualmente. Detalhes sobre como fazê-lo variam por porta de entrada e não são descritos aqui. Não pode ter BGP configurado com uma ligação VPN site-to-site.

Não é necessária nenhuma configuração adicional para que a funcionalidade regional de Integração VNet alcance através do seu VNet para recursos no local. Basta ligar o seu VNet aos recursos no local, utilizando o ExpressRoute ou uma VPN site-to-site.

> [!NOTE]
> A funcionalidade de Integração VNet exigida por gateway não integra uma aplicação com um VNet que tem um gateway ExpressRoute. Mesmo que o gateway ExpressRoute esteja configurado no modo de [coexistência,][VPNERCoex]a Integração VNet não funciona. Se precisar de aceder a recursos através de uma ligação ExpressRoute, utilize a funcionalidade regional de Integração VNet ou um Ambiente de Serviço de [Aplicações,][ASE]que corre no seu VNet.
> 
> 

### <a name="peering"></a>Peering

Se utilizar o peering com a Integração VNet regional, não precisa de fazer nenhuma configuração adicional.

Se utilizar a Integração VNet exigida pelo gateway com o peering, precisa de configurar alguns itens adicionais. Para configurar o epeering para trabalhar com a sua app:

1. Adicione uma ligação de observação no VNet a que a sua aplicação se liga. Quando adicionar a ligação de peering, ative **permitir o acesso à rede virtual** e selecione Permitir tráfego **encaminhada** e permitir o trânsito de **gateway**.
1. Adicione uma ligação de observação no VNet que está a ser espreitada para o VNet a que está ligado. Quando adicionar a ligação de peering no destino VNet, ative **permitir o acesso à rede virtual** e selecione Permitir tráfego **reencaminhado** e **permitir gateways remotos**.
1. Vá ao plano > de serviço de **aplicações****Networking** > **VNet Integration** UI no portal. Selecione o VNet a que a sua aplicação se liga. Na secção de encaminhamento, adicione a gama de endereços do VNet que está empares com a VNet a que a sua aplicação está ligada.

## <a name="manage-vnet-integration"></a>Gerir a Integração VNet

A ligação e desconexão com um VNet está a um nível de aplicação. As operações que podem afetar a Integração VNet em várias aplicações estão ao nível do plano do App Service. A partir da aplicação > portal de**Integração VNet** em **rede,** > pode obter detalhes no seu VNet. Pode ver informações semelhantes ao nível do plano do App Service no portal de > **integração VNet do** plano > de serviço de**aplicação.** **App Service plan**

A única operação que pode tomar na visão da aplicação da sua instância de Integração VNet é desligar a sua aplicação do VNet a que está atualmente ligada. Para desligar a sua aplicação de um VNet, selecione **Desligar**. A sua aplicação é reiniciada quando se desliga de um VNet. Desligar não muda o seu VNet. A sub-rede ou porta de entrada não é removida. Se pretender então eliminar o seu VNet, primeiro desligue a sua aplicação do VNet e elimine os recursos da seleção, como gateways.

O plano de serviço de aplicações VNet Integration UI mostra-lhe todas as integrações VNet utilizadas pelas aplicações no seu plano de Serviço de Aplicações. Para ver detalhes em cada VNet, selecione o VNet que lhe interessa. Existem duas ações que pode realizar aqui para a Integração VNet exigida por gateway:

* **Rede de sincronização**: A operação da rede de sincronização é utilizada apenas para a função de Integração VNet dependente de gateway. A realização de uma operação de rede de sincronização garante que os seus certificados e informações de rede estão sincronizados. Se adicionar ou alterar o DNS do seu VNet, execute uma operação de sincronização da rede. Esta operação reinicia todas as aplicações que utilizam este VNet.
* **Adicionar rotas**: Adicionar rotas conduz o tráfego de saída para o seu VNet.

### <a name="gateway-required-vnet-integration-routing"></a>Encaminhamento de Integração VNet exigido por gateway
As rotas definidas no seu VNet são usadas para direcionar o tráfego para o seu VNet a partir da sua aplicação. Para enviar tráfego adicional de saída para o VNet, adicione os blocos de endereços aqui. Esta capacidade funciona apenas com a Integração VNet exigida por gateway. As tabelas de rotas não afetam o tráfego da sua aplicação quando utiliza a Integração VNet exigida pelo gateway da forma como fazem com a Integração VNet regional.

### <a name="gateway-required-vnet-integration-certificates"></a>Certificados de Integração VNet exigidos por gateway
Quando a Integração VNet exigida pelo gateway estiver ativada, há uma troca de certificados necessária para garantir a segurança da ligação. Juntamente com os certificados estão a configuração dNS, rotas e outras coisas similares que descrevem a rede.

Se os certificados ou informações de rede forem alterados, selecione **Sync Network**. Ao selecionar **a Sync Network,** causa uma breve falha na conectividade entre a sua aplicação e o seu VNet. Embora a sua aplicação não seja reiniciada, a perda de conectividade pode fazer com que o seu site não funcione corretamente.

## <a name="pricing-details"></a>Detalhes dos preços
A funcionalidade regional de Integração VNet não tem qualquer custo adicional para utilização para além dos custos de nível de preços do plano de aplicação do plano de aplicação.

Três encargos estão relacionados com a utilização da funcionalidade de Integração VNet exigida pelo portal:

* Taxas de preços do **plano de aplicativos**do aplicativo app service : As suas aplicações precisam de estar num plano de serviço de aplicações Standard, Premium ou PremiumV2. Para obter mais informações sobre esses custos, consulte os preços do Serviço de [Aplicações.][ASPricing]
* **Custos**de transferência de dados : Existe um custo para a saída de dados, mesmo que o VNet esteja no mesmo datacenter. Estes encargos são descritos em detalhes de [preços de Transferência][DataPricing]de Dados .
* **Custos**de gateway VPN : Há um custo para o gateway de rede virtual que é necessário para o ponto-a-site VPN. Para mais informações, consulte o preço do [gateway VPN][VNETPricing].

## <a name="troubleshooting"></a>Resolução de problemas

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automatização

O apoio cli está disponível para integração vnet regional. Para aceder aos seguintes comandos, [instale o Azure CLI][installCLI].

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

Para a Integração VNet exigida por gateway, pode integrar o Serviço de Aplicações com uma rede virtual Azure utilizando o PowerShell. Para um script pronto a ser executado, consulte [Connect uma aplicação no Azure App Service para uma rede virtual Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


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
[privateendpoints]: networking/private-endpoint.md
