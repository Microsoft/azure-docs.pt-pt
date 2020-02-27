---
title: Integrar app com rede virtual Azure
description: Integre aplicações no Serviço de Aplicações Azure com redes virtuais Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 76139716fe11536faa0ff792185ba1643801c641
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649077"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integre a sua app com uma Rede Virtual Azure
Este documento descreve a funcionalidade de integração de redes virtuais do Azure App Service e como configurar com aplicações no Serviço de [Aplicações Azure.](https://go.microsoft.com/fwlink/?LinkId=529714) As [Redes Virtuais Azure][VNETOverview] (VNets) permitem colocar muitos dos seus recursos Azure numa rede não-internet de saída.  

O Serviço de Aplicações Azure tem duas variações. 

1. Os sistemas multi-inquilinos que suportam toda a gama de planos de preços, exceto Isolados
2. O App Service Environment (ASE), que se implanta no seu VNet e suporta aplicações de planode preços isolados

Este documento passa pela funcionalidade de Integração VNet, que é para uso no Serviço de Aplicações multi-inquilinos. Se a sua aplicação estiver no [App Service Environment][ASEintro], então já está num VNet e não requer a utilização da funcionalidade de Integração VNet para chegar a recursos no mesmo VNet. Para mais detalhes sobre todas as funcionalidades de rede do Serviço de Aplicações, leia as funcionalidades de [rede do Serviço de Aplicações](networking-features.md)

A VNet Integration dá à sua aplicação web acesso aos recursos da sua rede virtual, mas não concede acesso privado à sua aplicação web a partir do VNet. O acesso ao site privado refere-se a tornar a sua aplicação apenas acessível a partir de uma rede privada, como dentro de uma rede virtual Azure. A VNet Integration é apenas para fazer chamadas de saída da sua aplicação para o seu VNet. A funcionalidade de Integração VNet comporta-se de forma diferente quando utilizada com VNets na mesma região e com VNets noutras regiões. A funcionalidade de Integração VNet tem duas variações.

1. Integração VNet Regional - Ao ligar-se aos VNets gestor de recursos na mesma região, deve ter uma subrede dedicada no VNet com que está a integrar. 
2. Gateway exigiu integração VNet - Quando ligar a VNets em outras regiões ou a um VNet Clássico na mesma região, você precisa de um gateway de rede virtual aprovisionado no alvo VNet.

As funcionalidades de Integração VNet:

* requerem um plano de preços Standard, Premium, PremiumV2 ou Elástico Premium 
* apoiar TCP e UDP
* trabalhar com apps de Serviço de Aplicações e aplicativos função

Há algumas coisas que a VNet Integration não suporta, incluindo:

* montando uma unidade
* A integração do AD 
* A NetBios

Gateway necessário VNet Integração apenas fornece acesso a recursos no VNet alvo ou em redes ligadas ao alvo VNet com peering ou VPNs. Gateway necessário VNet Integração não permite o acesso aos recursos disponíveis através de conexões ExpressRoute ou trabalha com pontos finais de serviço. 

Independentemente da versão utilizada, a VNet Integration dá à sua aplicação web acesso aos recursos da sua rede virtual, mas não concede acesso privado à sua aplicação web a partir da rede virtual. O acesso ao site privado refere-se a tornar a sua aplicação apenas acessível a partir de uma rede privada, como dentro de uma rede virtual Azure. A VNet Integration é apenas para fazer chamadas de saída da sua aplicação para o seu VNet. 

## <a name="enable-vnet-integration"></a>Ativar a Integração VNet 

1. Vá ao Networking UI no portal do Serviço de Aplicações. Em Integração VNet, selecione *"Clique aqui para configurar"* . 

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

## <a name="regional-vnet-integration"></a>integração regional vnet

A utilização da Integração VNet regional permite ao acesso da sua aplicação:

* recursos na VNet na mesma região com que se integra 
* recursos em VNets olhou para o seu VNet que estão na mesma região
* serviço endpoint serviços seguros
* recursos através das ligações ExpressRoute
* recursos na VNet a que está ligado
* recursos através de conexões peered, incluindo conexões ExpressRoute
* Pontos finais privados 

Ao utilizar a Integração VNet com VNets na mesma região, pode utilizar as seguintes funcionalidades de Networking Azure:

* Grupos de Segurança da Rede (NSGs) - Pode bloquear o tráfego de saída com um Grupo de Segurança de Rede que é colocado na sua subnet de integração. As regras de entrada não se aplicam, uma vez que não pode utilizar a Integração VNet para fornecer acesso de entrada à sua aplicação web.
* Tabelas de Rotas (UDRs) - Pode colocar uma tabela de rotas na subnet de integração para enviar tráfego de saída onde quiser. 

Por padrão, a sua aplicação apenas irá direcionar o tráfego RFC1918 para o seu VNet. Se quiser encaminhar todo o tráfego de saída para o seu VNet, aplique a definição de aplicação WEBSITE_VNET_ROUTE_ALL na sua aplicação. Para configurar a definição da aplicação:

1. Vá ao UI de Configuração no portal da sua aplicação. Selecione **nova definição de aplicação**
1. Digite **WEBSITE_VNET_ROUTE_ALL** no campo Nome e **1** no campo Valor

   ![Fornecer a definição de aplicações][4]

1. Selecione **OK**
1. Selecione **Guardar**

Se encaminhar todo o tráfego de saída para o seu VNet, então estará sujeito aos NSGs e UDRs que são aplicados à sua subnet de integração. Quando encaminha todo o seu tráfego de saída para o seu VNet, os seus endereços de saída continuarão a ser os endereços de saída que estão listados nas propriedades da sua aplicação, a menos que forneça rotas para enviar o tráfego para outro lugar. 

Existem algumas limitações na utilização da Integração VNet com VNets na mesma região:

* Não é possível alcançar recursos através de ligações globais de observação
* A funcionalidade só está disponível a partir de unidades de escala mais recentes do App Service que suportam planos de Serviço de Aplicações PremiumV2.
* A subnet de integração só pode ser utilizada por um plano de Serviço de Aplicações
* A funcionalidade não pode ser utilizada por aplicações de plano isoladoque se encontram num Ambiente de Serviço de Aplicações
* A funcionalidade requer uma sub-rede não utilizada que seja uma /27 com 32 endereços ou maior num VNet do Gestor de Recursos
* A aplicação e o VNet devem estar na mesma região
* Não é possível eliminar um VNet com uma aplicação integrada. Retire a integração antes de eliminar o VNet. 
* Só pode integrar-se com VNets na mesma subscrição que a aplicação web
* Você pode ter apenas uma Integração VNet regional por plano de serviço de aplicações. Várias aplicações no mesmo plano de Serviço de Aplicações podem usar o mesmo VNet. 
* Não é possível alterar a subscrição de uma app ou de um plano de Serviço de Aplicações enquanto existe uma aplicação que está a utilizar a Integração Regional vNet

Um endereço é usado para cada instância do plano de serviço de aplicações. Se escalar a sua aplicação para cinco instâncias, então são utilizados cinco endereços. Uma vez que o tamanho da subrede não pode ser alterado após a atribuição, deve utilizar uma sub-rede suficientemente grande para acomodar qualquer escala que a sua aplicação possa alcançar. A /26 com 64 endereços é o tamanho recomendado. A /26 com 64 endereços acomodará um plano de Serviço de Aplicações Premium com 30 instâncias. Ao dimensionar um plano de Serviço de Aplicações para cima ou para baixo, precisa do dobro dos endereços por um curto período de tempo. 

Se quiser que as suas aplicações noutro plano de App Service cheguem a um VNet que já esteja ligado por aplicações noutro plano de Serviço de Aplicações, precisa de selecionar uma sub-rede diferente da utilizada pela Integração VNet pré-existente.  

A funcionalidade está em pré-visualização do Linux. A forma Linux da funcionalidade apenas suporta a realização de chamadas para endereços RFC 1918 (10.0.0.0.0/8, 172.16.0.0/12, 192.168.0.0.0/16).

### <a name="web-app-for-containers"></a>Aplicações Web para Contentores

Se utilizar o Serviço de Aplicações no Linux com as imagens incorporadas, a VNet Integration regional funciona sem alterações adicionais. Se utilizar a Web App para contentores, tem de modificar a sua imagem de estivador para utilizar a Integração VNet. Na sua imagem de estivador, utilize a variável do ambiente PORT como porta de escuta do servidor web principal, em vez de utilizar um número de porta codificado. A variável ambiente PORT é automaticamente definida pela plataforma app service no momento de arranque do contentor. Se estiver a utilizar o SSH, o daemon SSH deve ser configurado para ouvir o número de porta especificado pela variável ambiente SSH_PORT ao utilizar a integração regional de VNet.  Não há suporte para a integração vnet necessária em Linux. 

### <a name="service-endpoints"></a>Pontos Finais de Serviço

A Integração VNet Regional permite-lhe utilizar pontos finais de serviço.  Para utilizar pontos finais de serviço com a sua aplicação, utilize a Integração VNet regional para se ligar a um VNet selecionado e, em seguida, configurar pontos finais de serviço na subnet que utilizou para a integração. 

### <a name="network-security-groups"></a>Grupos de Segurança de Rede

Os Grupos de Segurança da Rede permitem bloquear o tráfego de entrada e saída para recursos num VNet. Uma aplicação web que usa a Integração VNet regional pode usar [o Network Security Group][VNETnsg] para bloquear o tráfego de saída para recursos no seu VNet ou na internet. Para bloquear o tráfego para endereços públicos, deve ter a definição de aplicação WEBSITE_VNET_ROUTE_ALL definida para 1. As regras de entrada num NSG não se aplicam à sua aplicação, uma vez que a Integração VNet apenas afeta o tráfego de saída da sua aplicação. Para controlar o tráfego de entrada na sua aplicação web, utilize a funcionalidade Restrições de Acesso. Um NSG aplicado à sua subnet de integração estará em vigor independentemente de quaisquer rotas aplicadas à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL estivesse definido para 1 e não tivesse nenhuma rota que afetasse o tráfego de endereços públicos na sua subnet de integração, todo o seu tráfego de saída ainda estaria sujeito a NSGs atribuídos à sua subnet de integração. Se WEBSITE_VNET_ROUTE_ALL não fosse definida, os NSGs só seriam aplicados ao tráfego RFC1918.

### <a name="routes"></a>Rotas

As Route Tables permitem-lhe desviar o tráfego de saída da sua app para onde quiser. Por padrão, as tabelas de rotas apenas afetarão o tráfego de destino RFC1918.  Se WEBSITE_VNET_ROUTE_ALL para 1, todas as suas chamadas de saída serão afetadas. As rotas definidas na sua subnet de integração não afetarão as respostas aos pedidos de aplicações de entrada. Os destinos comuns podem incluir dispositivos de firewall ou gateways. Se quiser encaminhar todo o tráfego de saída no local, pode utilizar uma mesa de rota para enviar todo o tráfego de saída para o seu gateway ExpressRoute. Se fizer o tráfego de encaminhamento para um portal, certifique-se de que define rotas na rede externa para enviar quaisquer respostas de volta.

As rotas do Border Gateway Protocol (BGP) também afetarão o tráfego da sua aplicação. Se tiver rotas BGP a partir de algo como um gateway ExpressRoute, o tráfego de saída da sua aplicação será afetado. Por predefinição, as rotas BGP apenas afetarão o tráfego de destino RFC1918. Se WEBSITE_VNET_ROUTE_ALL está definido para 1, todo o tráfego de saída pode ser afetado pelas suas rotas de BGP. 

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

### <a name="accessing-on-premises-resources"></a>Acesso aos recursos no local

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
1. Vá ao plano de Serviço de Aplicações > Networking > VNet Integration UI no portal.  Selecione o VNet a que a sua aplicação se liga. Na secção de encaminhamento, adicione a gama de endereços do VNet que é analisado com o VNet a que a sua aplicação está ligada.  

## <a name="managing-vnet-integration"></a>Gestão da Integração VNet 

A ligação e desconexão com um VNet está a um nível de aplicação. As operações que podem afetar a Integração VNet em várias aplicações estão ao nível do plano do App Service. A partir do portal de integração de aplicativos > Networking > VNet, pode obter detalhes no seu VNet. Pode ver informações semelhantes ao nível ASP no portal ASP > Networking > VNet Integration.

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
Embora a funcionalidade seja fácil de configurar, isso não significa que a sua experiência será livre de problemas. Caso encontre problemas de acesso ao ponto final pretendido, existem alguns utilitários que pode utilizar para testar a conectividade a partir da consola de aplicações. Há duas consolas que podes usar. Uma é a consola Kudu e a outra é a consola no portal Azure. Para chegar à consola Kudu a partir da sua aplicação, vá a Tools -> Kudu. Você também pode chegar à consola Kudo em [sitename].scm.azurewebsites.net. Assim que o site estiver cheio, vá ao separador de consola Debug. Para chegar ao portal Azure hospedado consola então da sua aplicação vá para Tools -> Console. 

#### <a name="tools"></a>Ferramentas
As ferramentas **ping**, **nslookup**, e **tracert** não funcionarão através da consola devido a restrições de segurança. Para preencher o vazio, duas ferramentas separadas adicionadas. Para testar a funcionalidade DNS, adicionámos uma ferramenta chamada nameresolver.exe. A sintaxe é:

    nameresolver.exe hostname [optional: DNS Server]

Pode utilizar **nameresolver** para verificar os nomes de anfitriões de que a sua aplicação depende. Desta forma, pode testar se tem algo mal configurado com o seu DNS ou talvez não tenha acesso ao seu servidor DNS. Pode ver o servidor DNS que a sua aplicação irá utilizar na consola olhando para as variáveis ambientais WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

A próxima ferramenta permite-lhe testar a conectividade TCP com uma combinação de hospedeiro e porta. Esta ferramenta chama-se **tcpping** e a sintaxe é:

    tcpping.exe hostname [optional: port]

O utilitário **tcpping** diz-lhe se pode chegar a um hospedeiro específico e porto. Só pode mostrar sucesso se: houver uma aplicação a ouvir a combinação de anfitriões e portas, e existe acesso de rede da sua app ao anfitrião especificado e à porta.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Depurando o acesso aos recursos hospedados pela VNet
Há uma série de coisas que podem impedir que a sua aplicação chegue a um anfitrião específico e à porta. A maior parte do tempo é uma das três coisas:

* **Uma firewall está no caminho.** Se tiver uma firewall no caminho, vai atingir o intervalo do TCP. O tempo de saída do TCP é de 21 segundos neste caso. Utilize a ferramenta **de tcpping** para testar a conectividade. Os intervalos de tCP podem ser devidos a muitas coisas além das firewalls, mas começam por aí. 
* **DNS não é acessível.** O tempo de paragem do DNS é de três segundos por servidor DNS. Se tiver dois servidores DNS, o tempo é de 6 segundos. Use nameresolver para ver se o DNS está funcionando. Lembre-se que não pode usar o nslookup, uma vez que não utiliza o DNS com o qual o VNet está configurado. Se não for acessível, pode ter uma firewall ou NSG bloqueando o acesso ao DNS ou pode estar em baixo.

Se esses itens não responderem aos seus problemas, procure primeiro coisas como: 

**integração regional vnet**
* é o seu destino um endereço não RFC1918 e você não tem WEBSITE_VNET_ROUTE_ALL definido para 1
* existe uma saída de nSG bloqueando a sua subnet de integração
* Se atravessar a ExpressRoute ou uma VPN, o seu portal no local está configurado para encaminhar o tráfego de volta para Azure? Se conseguir chegar a pontos finais no seu VNet mas não no local, verifique as suas rotas.
* Tem permissão suficiente para definir delegação na subnet de integração? Durante a configuração regional de Integração VNet, a sua subnet de integração será delegada na Microsoft.Web. O VNet Integration UI delegará a subnet para microsoft.Web automaticamente. Se a sua conta não tiver permissões de networking suficientes para definir a delegação, precisará de alguém que possa definir atributos na sua subnet de integração para delegar a sub-rede. Para delegar manualmente a subnet de integração, vá à subnet UI da rede virtual Azure e detetete a delegação para a Microsoft.Web. 

**gateway necessária Integração VNet**
* é a gama de endereços ponto-a-local nas gamas RFC 1918 (10.0.0.0.0.0.10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* O Gateway mostra como estando no portal? Se o seu portal está para baixo, então traga-o de volta para cima.
* Os certificados mostram estar sincronizado ou suspeita que a configuração da rede foi alterada?  Se os seus certificados estiverem dessincronizados ou suspeitar que houve uma alteração na configuração vNet que não foi sincronizada com os seus ASPs, então acerte em "Sync Network".
* se atravessar uma VPN, a porta de entrada no local está configurada para encaminhar o tráfego de volta para Azure? Se conseguir chegar a pontos finais no seu VNet mas não no local, verifique as suas rotas.
* Está a tentar usar um portal de coexistência que suporta tanto o ponto para o site como o ExpressRoute? Os gateways de coexistência não são suportados com a Integração VNet 

Depurar problemas de networking é um desafio porque não se consegue ver o que está a bloquear o acesso a uma combinação específica de hospedeiro:porta. Algumas das causas incluem:

* tem uma firewall no seu anfitrião impedindo o acesso à porta de aplicação do seu ponto para o intervalo IP do local. Atravessar subredes requer frequentemente acesso público.
* o seu anfitrião alvo está em baixo
* sua aplicação está em baixo
* você tinha o IP errado ou nome de anfitrião
* a sua aplicação está a ouvir num porto diferente do que esperava. Pode comparar o seu ID de processo com a porta de audição utilizando "netstat -aon" no hospedeiro final. 
* os seus grupos de segurança de rede estão configurados de forma a impedir o acesso ao seu anfitrião de aplicação e porta do seu ponto para o intervalo IP do site

Lembre-se que não sabe qual o endereço que a sua aplicação irá realmente usar. Pode ser qualquer endereço na subnet de integração ou no intervalo de endereços ponto-a-local, por isso precisa de permitir o acesso a partir de toda a gama de endereços. 

Os passos adicionais de depuração incluem:

* ligue-se a um VM no seu VNet e tente chegar ao seu anfitrião de recursos:porta a partir daí. Para testar o acesso à TCP, utilize a **ligação**de teste de comando PowerShell . A sintaxe é:

      test-netconnection hostname [optional: -Port]

* apresentar uma aplicação num VM e testar o acesso a esse anfitrião e porta a partir da consola a partir da sua aplicação utilizando **tcpping**

#### <a name="on-premises-resources"></a>Recursos no local ####

Se a sua aplicação não conseguir contactar um recurso no local, verifique se consegue contactar o recurso a partir do seu VNet. Utilize o comando PowerShell de **ligação de rede de teste** para verificar se há acesso tCP. Se o seu VM não conseguir contactar o seu recurso no local, a sua ligação VPN ou ExpressRoute pode não estar configurada corretamente.

Se o vNet hospedado VM pode chegar ao seu sistema no local, mas a sua aplicação não pode, então a causa é provavelmente uma das seguintes razões:

* as suas rotas não estão configuradas com a sua subnet ou apontar para as gamas de endereços do site no seu gateway no local
* os seus grupos de segurança de rede estão bloqueando o acesso para a sua gama IP ponto-a-site
* as suas firewalls no local estão bloqueando o tráfego da sua gama IP ponto-a-local
* está a tentar alcançar um endereço não RFC 1918 utilizando a funcionalidade regional de Integração VNet


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
[4]: ./media/web-sites-integrate-with-vnet/vnetint-appsetting.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
