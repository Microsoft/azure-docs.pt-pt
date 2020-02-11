---
title: Integrar app com rede virtual Azure
description: Saiba como o Azure App Service se integra com a Rede Virtual Azure e como ligar uma aplicação a uma rede virtual.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/21/2019
ms.author: ccompy
ms.custom: fasttrack-edit
ms.openlocfilehash: 472fe621fc7a95317f143ef96a1d7f8b5adfe581
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016974"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integre a sua app com uma Rede Virtual Azure
Este documento descreve a funcionalidade de integração de redes virtuais do Azure App Service e como configurar com aplicações no Serviço de [Aplicações Azure.](https://go.microsoft.com/fwlink/?LinkId=529714) As [Redes Virtuais Azure][VNETOverview] (VNets) permitem colocar muitos dos seus recursos Azure numa rede não-internet de saída.  

O Serviço de Aplicações Azure tem duas variações. 

1. Os sistemas multi-inquilinos que suportam toda a gama de planos de preços, exceto Isolados
2. O App Service Environment (ASE), que se implanta no seu VNet e suporta aplicações de planode preços isolados

Este documento passa pelas duas funcionalidades de Integração VNet, que é para uso no Serviço de Aplicações multi-inquilinos. Se a sua aplicação estiver no [App Service Environment][ASEintro], então já está num VNet e não requer a utilização da funcionalidade de Integração VNet para chegar a recursos no mesmo VNet. Para mais detalhes sobre todas as funcionalidades de rede do Serviço de Aplicações, leia as funcionalidades de [rede do Serviço de Aplicações](networking-features.md)

Existem duas formas para a funcionalidade de Integração VNet

1. Uma versão permite a integração com VNets na mesma região. Esta forma da funcionalidade requer uma sub-rede numa VNet na mesma região. Esta funcionalidade ainda se encontra em pré-visualização, mas é suportada para cargas de trabalho de produção de aplicações windows com algumas ressalvas anotadas abaixo.
2. A outra versão permite a integração com VNets noutras regiões ou com VNets Clássicos. Esta versão da funcionalidade requer a implementação de um Gateway de Rede Virtual no seu VNet. Esta é a funcionalidade baseada em VPN ponto-a-site e é suportada apenas com aplicações do Windows.

Uma aplicação só pode usar uma forma da funcionalidade de Integração VNet de cada vez. A questão, então, é qual a funcionalidade que deve utilizar. Podes usar qualquer um para muitas coisas. Os diferenciadores claros são:

| Problema  | Solução | 
|----------|----------|
| Deseja alcançar um endereço RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) na mesma região | integração regional vnet |
| Quer chegar a recursos num VNet clássico ou num VNet em outra região | gateway necessária Integração VNet |
| Quer chegar aos pontos finais do RFC 1918 através da ExpressRoute | integração regional vnet |
| Deseja acessar recursos entre pontos de extremidade de serviço | integração regional vnet |

Nenhuma das funcionalidades lhe permitirá alcançar endereços não RFC 1918 através do ExpressRoute. Para isso, precisa de usar uma ASE por enquanto.

A utilização da Integração VNet regional não liga o seu VNet a pontos finais de serviço no local ou configurar pontos finais de serviço. Isto é uma configuração separada de rede. A Integração VNet regional simplesmente permite que a sua aplicação evoque chamadas através desses tipos de ligação.

Independentemente da versão utilizada, a VNet Integration dá à sua aplicação web acesso aos recursos da sua rede virtual, mas não concede acesso privado à sua aplicação web a partir da rede virtual. O acesso ao site privado refere-se a tornar a sua aplicação apenas acessível a partir de uma rede privada, como dentro de uma rede virtual Azure. A VNet Integration é apenas para fazer chamadas de saída da sua aplicação para o seu VNet. 

A funcionalidade de Integração VNet:

* requer um plano de preços Standard, Premium ou PremiumV2 
* Dá suporte a TCP e UDP
* funciona com apps de Serviço de Aplicações e aplicações função

Há algumas coisas que a VNet Integration não suporta, incluindo:

* montando uma unidade
* Integração do AD 
* A NetBios

## <a name="regional-vnet-integration"></a>Integração Regional de VNet 

> [!NOTE]
> Peering ainda não está disponível para o Serviço de Aplicações baseado em Linux.
>

Quando a VNet Integration é utilizada com VNets na mesma região que a sua aplicação, requer a utilização de uma subnet delegada com pelo menos 32 endereços na mesma. A sub-rede não pode ser usada para mais nada. As chamadas de saída feitas a partir da sua aplicação serão feitas a partir dos endereços na sub-rede delegada. Quando utiliza esta versão da Integração VNet, as chamadas são feitas a partir de endereços no seu VNet. A utilização de endereços no seu VNet permite:

* Faça chamadas para serviços seguros de ponto final
* Acesso a recursos através das ligações ExpressRoute
* Recursos de acesso no VNet a que está ligado
* Recursos de acesso através de ligações peered, incluindo conexões ExpressRoute

Esta funcionalidade encontra-se em pré-visualização mas, é suportada para cargas de trabalho de produção de aplicações windows com as seguintes limitações:

* Só é possível alcançar endereços que estejam na gama RFC 1918. Estes são endereços nos blocos de endereços 10.0.0.0.0,8, 172.16.0.0.0/12, 192.168.0.0/16.
* Não é possível alcançar recursos através de ligações globais de observação
* Não é possível definir rotas no tráfego proveniente da sua aplicação para o seu VNet
* A funcionalidade só está disponível a partir de unidades de escala mais recentes do App Service que suportam planos de Serviço de Aplicações PremiumV2. Note que isso não significa que a sua aplicação deve funcionar num SKU PremiumV2, apenas que deve funcionar num Plano de Serviço de Aplicações onde a opção PremiumV2 está disponível (o que implica que é uma unidade de escala mais recente onde esta funcionalidade de integração VNet também está disponível).
* A subnet de integração só pode ser utilizada por um plano de Serviço de Aplicações
* A funcionalidade não pode ser utilizada por aplicações de plano isoladoque se encontram num Ambiente de Serviço de Aplicações
* A funcionalidade requer uma sub-rede não utilizada que seja uma /27 com 32 endereços ou maior no seu Gestor de Recursos VNet
* A aplicação e o VNet devem estar na mesma região
* Não é possível eliminar um VNet com uma aplicação integrada. Deve remover a integração primeiro 
* Você pode ter apenas uma Integração VNet regional por plano de serviço de aplicações. Várias aplicações no mesmo plano de Serviço de Aplicações podem usar o mesmo VNet. 
* Não é possível alterar a subscrição de uma app ou de um plano de Serviço de Aplicações enquanto existe uma aplicação que está a utilizar a Integração Regional vNet

Um endereço é usado para cada instância do plano de serviço de aplicações. Se escalou a sua aplicação para 5 instâncias, então são utilizados 5 endereços. Uma vez que o tamanho da subrede não pode ser alterado após a atribuição, deve utilizar uma sub-rede suficientemente grande para acomodar qualquer escala que a sua aplicação possa alcançar. A /26 com 64 endereços é o tamanho recomendado. A /27 com 32 endereços acomodaria um plano premium do Serviço de Aplicações 20 instâncias se não alterasse o tamanho do plano de Serviço de Aplicações. Ao dimensionar um plano de Serviço de Aplicações para cima ou para baixo, precisa do dobro dos endereços por um curto período de tempo. 

Se quiser que as suas aplicações noutro plano de App Service cheguem a um VNet que já esteja ligado por aplicações noutro plano de Serviço de Aplicações, precisa de selecionar uma sub-rede diferente da utilizada pela Integração VNet pré-existente.  

A funcionalidade está em pré-visualização também para o Linux. Para utilizar a funcionalidade de Integração VNet com um Gestor de Recursos VNet na mesma região:

1. Vá para a Rede UI no portal. Se a sua aplicação for capaz de utilizar a nova funcionalidade, verá uma opção para Adicionar VNet (pré-visualização).  

   ![Selecione Integração VNet][6]

1. **Selecione Adicionar VNet (pré-visualização)** .  

1. Selecione o VNet do Gestor de Recursos com o que pretende integrar e, em seguida, crie uma nova subnet ou escolha uma sub-rede pré-existente vazia. A integração leva menos de um minuto para ser concluída. Durante a integração, a sua aplicação é reiniciada.  Quando a integração estiver concluída, verá detalhes no VNet com os quais está integrado e um banner no topo que lhe diz que a funcionalidade está em pré-visualização.

   ![Selecione o VNet e a subnet][7]

Assim que a sua aplicação estiver integrada com o seu VNet, utilizará o mesmo servidor DNS com o qual o seu VNet está configurado. 

A Integração VNet Regional requer que a sua subnet de integração seja delegada na Microsoft.Web.  O VNet Integration UI delegará a subnet para microsoft.Web automaticamente. Se a sua conta não tiver permissões de rede suficientes para o definir, precisará de alguém que possa definir atributos na sua subnet de integração para delegar a sub-rede. Para delegar manualmente a subnet de integração, vá à subnet UI da rede virtual Azure e detetete a delegação para a Microsoft.Web.

Para desligar a sua aplicação do VNet, selecione **Desligar**. Isto irá reiniciar a sua aplicação web. 


#### <a name="web-app-for-containers"></a>Aplicação Web para Contentores

Se utilizar o Serviço de Aplicações no Linux com as imagens incorporadas, a funcionalidade regional de Integração VNet funciona sem alterações adicionais. Se utilizar a Web App para contentores, tem de modificar a sua imagem de estivador para utilizar a Integração VNet. Na sua imagem de estivador, utilize a variável do ambiente PORT como porta de escuta do servidor web principal, em vez de utilizar um número de porta codificado. A variável ambiente PORT é automaticamente definida pela plataforma app service no momento de arranque do contentor. Se estiver a utilizar o SSH, o daemon SSH deve ser configurado para ouvir o número de porta especificado pela variável ambiente SSH_PORT ao utilizar a integração regional de VNet.

### <a name="service-endpoints"></a>Pontos Finais de Serviço

A nova funcionalidade de Integração VNet permite-lhe utilizar pontos finais de serviço.  Para utilizar pontos finais de serviço com a sua aplicação, utilize a nova Integração VNet para se ligar a um VNet selecionado e, em seguida, configurar pontos finais de serviço na subnet que utilizou para a integração. 


### <a name="how-vnet-integration-works"></a>Como funciona a Integração VNet

As aplicações no Serviço de Aplicações são hospedadas em funções de trabalhador. Os planos de preços básicos e mais elevados são planos de hospedagem dedicados onde não há outros clientes a funcionar com os mesmos trabalhadores. A VNet Integration funciona montando interfaces virtuais com endereços na sub-rede delegada. Como o endereço a partir está no seu VNet, tem acesso à maioria das coisas dentro ou através do seu VNet, tal como um VM no seu VNet faria. A implementação em rede é diferente de executar um VM no seu VNet e é por isso que algumas funcionalidades de networking ainda não estão disponíveis durante a utilização desta funcionalidade.

![Integração de VNet](media/web-sites-integrate-with-vnet/vnet-integration.png)

Quando a Integração VNet estiver ativada, a sua aplicação ainda fará chamadas de saída para a internet através dos mesmos canais que o normal. Os endereços de saída que estão listados no portal de propriedades da aplicação ainda são os endereços utilizados pela sua aplicação. What changes for your app are, calls to service endpoint secured services or RFC 1918 addresses goes into your VNet. 

A funcionalidade suporta apenas uma interface virtual por trabalhador.  Uma interface virtual por trabalhador significa uma Integração VNet regional por plano de serviço de aplicações. Todas as aplicações do mesmo plano de Serviço de Aplicações podem usar a mesma Integração VNet, mas se precisar de uma aplicação para se ligar a um VNet adicional, terá de criar outro plano de Serviço de Aplicações. A interface virtual utilizada não é um recurso a que os clientes tenham acesso direto.

Devido à natureza do funcionamento desta tecnologia, o tráfego que é utilizado com a Integração VNet não aparece nos registos de fluxo do Observador de Rede ou nsg.  

## <a name="gateway-required-vnet-integration"></a>Gateway exigia integração VNet 

O gateway requeria integração VNet:

* Pode ser usado para ligar a VNets em qualquer região seja o Gestor de Recursos ou VNets Clássicos
* Permite que uma aplicação se conectem a apenas 1 VNet de cada vez
* Permite que até cinco VNets sejam integrados num Plano de Serviço de Aplicações 
* Permite que o mesmo VNet seja utilizado por várias aplicações num Plano de Serviço de Aplicações sem afetar o número total que pode ser usado por um plano de Serviço de Aplicações.  Se tiver 6 aplicações usando o mesmo VNet no mesmo plano de Serviço de Aplicações, isso conta como 1 VNet sendo usado. 
* Requer um Gateway de Rede Virtual que está configurado com Point to Site VPN
* Suporta um SLA de 99,9% devido ao SLA na porta de entrada

Esta funcionalidade não suporta:
* Usar com aplicativos Linux
* O acesso a recursos no ExpressRoute 
* O acesso a recursos nos Pontos Finais de Serviço 

### <a name="getting-started"></a>Introdução

Aqui estão algumas coisas a ter em mente antes de ligar a sua aplicação web a uma rede virtual:

* Uma rede virtual alvo deve ter VPN ponto-a-site ativado com um gateway baseado em rota antes de poder ser ligado à aplicação. 
* O VNet deve estar na mesma subscrição que o seu Plano de Serviço de Aplicações (ASP).
* As aplicações que se integram com um VNet utilizam o DNS especificado para esse VNet.

### <a name="set-up-a-gateway-in-your-vnet"></a>Estabeleça uma porta de entrada no seu VNet ###

Se já tem um portal configurado com endereços ponto-a-site, pode saltar para configurar a Integração VNet com a sua aplicação.  
Para criar um portal:

1. [Crie uma sub-rede][creategatewaysubnet] de porta no seu VNet.  

1. [Crie o gateway VPN][creategateway]. Selecione um tipo VPN baseado em rota.

1. [Detete o ponto para os endereços][setp2saddresses]do site . Se o portal não estiver no SKU básico, então o IKEV2 deve ser desativado na configuração ponto-a-local e o SSTP deve ser selecionado. O espaço de endereço deve estar nos blocos de endereços RFC 1918, 10.0.0.0/8, 172.16.0.0.0/12, 192.168.0.0/16

Se está apenas a criar a porta de entrada para utilização com a Integração VNet do Serviço de Aplicações, então não precisa de carregar um certificado. Criar o portal pode levar 30 minutos. Não poderá integrar a sua aplicação com o seu VNet até que o portal esteja aprovisionado. 

### <a name="configure-vnet-integration-with-your-app"></a>Configure a Integração VNet com a sua app 

Para ativar a Integração VNet na sua aplicação: 

1. Vá à sua aplicação no portal Azure e abra as definições de aplicações e selecione Networking > VNet Integration. O seu ASP deve estar num SKU Padrão ou melhor para utilizar qualquer função de Integração VNet. 
 ![VNet Integration UI][1]

1. **Selecione Adicionar VNet**. 
 ![adicionar][2] de integração VNet

1. Selecione o seu VNet. 
  ![Selecione o seu][8] VNet
  
A sua aplicação será reiniciada após este último passo.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Como funciona a funcionalidade de Integração VNet necessária

A funcionalidade de Integração VNet necessária para gateway é construída em cima da tecnologia VPN ponto-a-local. A tecnologia ponto-a-site limita o acesso da rede apenas à máquina virtual que acolhe a aplicação. As aplicações limitam-se apenas a enviar tráfego para a internet, através de Conexões Híbridas ou através da Integração VNet. 

![Como funciona a Integração VNet][3]

## <a name="managing-vnet-integration"></a>Gestão da Integração VNet
A capacidade de ligar e desligar-se a um VNet está a nível de aplicação. As operações que podem afetar a Integração VNet em várias aplicações estão ao nível do plano do App Service. A partir do portal de integração de aplicativos > Networking > VNet, pode obter detalhes no seu VNet. Pode ver informações semelhantes ao nível ASP no portal ASP > Networking > VNet Integration, incluindo quais as aplicações desse plano de App Service que estão a usar uma determinada integração.

 ![Detalhes da VNet][4]

A informação que tem à sua disposição no VNet Integration UI é a mesma entre a app e os portais ASP.

* VNet Name - links para a rede virtual UI
* Localização - reflete a localização do seu VNet. Integrar-se com um VNet noutro local pode causar problemas de latência para a sua aplicação. 
* Estado do Certificado - reflete se os seus certificados estão sincronizados entre o seu plano de Serviço de Aplicações e o seu VNet.
* Estado do gateway - Caso esteja a utilizar o gateway necessário para a Integração VNet, pode ver o estado do gateway.
* Espaço de endereço VNet - mostra o espaço de endereço IP para o seu VNet. 
* Espaço de endereço ponto-a-local - mostra o espaço de endereço IP do local para o seu VNet. Ao efazer chamadas para o seu VNet enquanto utiliza a funcionalidade necessária para o gateway, o endereço FROM da sua aplicação será um desses endereços. 
* Espaço de endereço seleto do site -para-site - Você pode usar VPNs site-to-site para ligar o seu VNet aos seus recursos no local ou a outros VNet. As gamas IP definidas com essa ligação VPN são mostradas aqui.
* DNS Servers - mostra os Servidores DNS configurados com o seu VNet.
* Endereços IP encaminhados para o VNet - mostra os blocos de endereços encaminhados utilizados para conduzir o tráfego para o seu VNet 

A única operação que pode tomar na visão da aplicação da sua Integração VNet é desligar a sua aplicação do VNet a que está atualmente ligada. Para desligar a sua aplicação de um VNet, selecione **Desligar**. A sua aplicação será reiniciada quando desligar de um VNet. Desligar não muda o seu VNet. A sub-rede ou porta de entrada não é removida. Se pretender eliminar o seu VNet, tem primeiro de desligar a sua aplicação do VNet e eliminar os recursos que nele se advoquem, como gateways. 

Para chegar ao ASP VNet Integration UI, abra o seu ASP UI e selecione **Networking**.  Em integração VNet, selecione **Clique aqui para configurar** para abrir o UI de Estado da Funcionalidade de Rede.

![Informação de integração ASP VNet][5]

O ASP VNet Integration UI irá mostrar-lhe todos os VNets que são usados pelas aplicações no seu ASP. Para ver detalhes em cada VNet, clique no VNet que lhe interessa. Há duas ações que pode sacá-lo aqui.

* **Rede sync**. O funcionamento da rede de sincronização destina-se apenas à funcionalidade de Integração VNet dependente de gateway. A realização de uma operação de rede de sincronização garante que os seus certificados e informações de rede estão sincronizados. Se adicionar ou alterar o DNS do seu VNet, tem de executar uma operação de **rede Sync.** Esta operação reiniciará todas as aplicações que utilizem este VNet.
* **Adicionar rotas** A adição de rotas conduzirá o tráfego de saída para o seu VNet.

**Encaminhamento** As rotas definidas no seu VNet são usadas para direcionar o tráfego para o seu VNet a partir da sua aplicação. Se precisar enviar tráfego adicional de saída para o VNet, então pode adicionar os blocos de endereços aqui. Esta capacidade funciona apenas com a entrada necessária para a Integração VNet.

**Certificados** Quando o gateway exigia a Integração VNet ativada, existe uma troca de certificados necessária para garantir a segurança da ligação. Juntamente com os certificados estão a configuração dNS, rotas e outras coisas similares que descrevem a rede.
Se os certificados ou informações de rede forem alterados, tem de clicar em "Sync Network". Ao clicar em "Sync Network", causa uma breve falha na conectividade entre a sua aplicação e o seu VNet. Embora a sua aplicação não seja reiniciada, a perda de conectividade pode fazer com que o seu site não funcione corretamente. 

## <a name="accessing-on-premises-resources"></a>Acesso aos recursos no local
As aplicações podem aceder aos recursos no local integrando-se com VNets que têm ligações site-to-site. Se estiver a utilizar o gateway necessário para a Integração VNet, precisa de atualizar as suas rotas de gateway VPN no local com os seus blocos de endereços ponto-a-local. Quando o VPN site-to-site é configurado pela primeira vez, os scripts utilizados para configurar devem configurar as rotas corretamente. Se adicionar os endereços ponto-a-site depois de criar o seu VPN site-to-site, precisa de atualizar as rotas manualmente. Detalhes sobre como fazê-lo variam por porta de entrada e não são descritos aqui. Não é possível configurar o BGP com uma ligação VPN site-to-site.

Não é necessária uma configuração adicional para que a funcionalidade regional de Integração VNet alcance através do seu VNet e no local. Basta ligar o seu VNet às instalações utilizando o ExpressRoute ou uma VPN site-to-site. 

> [!NOTE]
> A funcionalidade de Integração VNet necessária não integra uma aplicação com um VNet que tenha um Gateway ExpressRoute. Mesmo que o ExpressRoute Gateway esteja configurado no modo de [coexistência,][VPNERCoex] a Integração VNet não funciona. Se precisar de aceder a recursos através de uma ligação ExpressRoute, então pode utilizar a funcionalidade regional de Integração VNet ou um Ambiente de Serviço de [Aplicações,][ASE]que corre no seu VNet. 
> 
> 

## <a name="peering"></a>Peering
Se estiver a utilizar o peering com a Integração VNet regional, não precisa de fazer nenhuma configuração adicional. 

Se estiver a utilizar o gateway necessário para a Integração VNet com o peering, precisa de configurar alguns itens adicionais. Para configurar o epeering para trabalhar com a sua app:

1. Adicione uma ligação de observação no VNet a que a sua aplicação se liga. Ao adicionar a ligação de peering, ative **permitir o acesso** à rede virtual e verificar Permitir tráfego **encaminhada** e permitir o trânsito de **gateway**.
1. Adicione uma ligação de observação no VNet que está a ser espreitado para o VNet a que está ligado. Ao adicionar a ligação de observação no destino VNet, ative **permitir o acesso** à rede virtual e verificar Permitir tráfego **reencaminhado** e **permitir gateways remotos**.
1. Vá ao plano de Serviço de Aplicações > Networking > VNet Integration UI no portal.  Selecione o VNet a que a sua aplicação se liga. Na secção de encaminhamento, adicione a gama de endereços do VNet que é analisado com o VNet a que a sua aplicação está ligada.  


## <a name="pricing-details"></a>Detalhes dos preços
A funcionalidade regional de Integração VNet não tem qualquer custo adicional para utilização para além dos encargos de nível de preços ASP.

Existem três encargos relacionados com a utilização da funcionalidade de integração VNet necessária:

* Taxas de nível de preços ASP - As suas aplicações precisam de estar num Plano de Serviço de Aplicações Standard, Premium ou PremiumV2. Pode ver mais detalhes sobre esses custos aqui: Preços do Serviço de [Aplicações][ASPricing]. 
* Custos de transferência de dados - Existe um custo para a saída de dados, mesmo que o VNet esteja no mesmo centro de dados. Estes encargos são descritos em Detalhes de [Preços de Transferência][DataPricing]de Dados . 
* CUSTOS VPN Gateway - Existe um custo para o gateway VNet que é necessário para o ponto-a-site VPN. Os detalhes estão na página de Preços do [Gateway VPN.][VNETPricing]


## <a name="troubleshooting"></a>Resolução de problemas
Embora a funcionalidade seja fácil de configurar, isso não significa que a sua experiência será livre de problemas. Caso encontre problemas de acesso ao ponto final pretendido, existem alguns utilitários que pode utilizar para testar a conectividade a partir da consola de aplicações. Há duas consolas que podes usar. Uma é a consola Kudu e a outra é a consola no portal Azure. Para chegar à consola Kudu a partir da sua aplicação, vá a Tools -> Kudu. Você também pode chegar à consola Kudo em [sitename].scm.azurewebsites.net. Assim que o site estiver cheio, vá ao separador de consola Debug. Para chegar ao portal Azure hospedado consola então da sua aplicação vá para Tools -> Console. 

#### <a name="tools"></a>Ferramentas
As ferramentas **ping**, **nslookup** e **tracert** não funcionarão através da consola devido a restrições de segurança. Para preencher o vazio, duas ferramentas separadas adicionadas. Para testar a funcionalidade DNS, adicionámos uma ferramenta chamada nameresolver.exe. A sintaxe é:

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
* é o seu destino um endereço RFC 1918
* existe uma saída de nSG bloqueando a sua subnet de integração
* Se atravessar a ExpressRoute ou uma VPN, o seu portal no local está configurado para encaminhar o tráfego de volta para Azure? Se conseguir chegar a pontos finais no seu VNet mas não no local, isto é bom para verificar.

**gateway necessária Integração VNet**
* é a gama de endereços ponto-a-local nas gamas RFC 1918 (10.0.0.0.0.0.10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* O Gateway mostra como estando no portal? Se o seu portal está para baixo, então traga-o de volta para cima.
* Os certificados mostram estar sincronizado ou suspeita que a configuração da rede foi alterada?  Se os seus certificados estiverem dessincronizados ou suspeitar que houve uma alteração na configuração vNet que não foi sincronizada com os seus ASPs, então acerte em "Sync Network".
* Se atravessar a ExpressRoute ou uma VPN, o seu portal no local está configurado para encaminhar o tráfego de volta para Azure? Se conseguir chegar a pontos finais no seu VNet mas não no local, isto é bom para verificar.

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


## <a name="powershell-automation"></a>Automação PowerShell

Pode integrar o Serviço de Aplicações com uma Rede Virtual Azure utilizando o PowerShell. Para um script pronto a ser executado, consulte [Connect uma aplicação no Azure App Service para uma Rede Virtual Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

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
