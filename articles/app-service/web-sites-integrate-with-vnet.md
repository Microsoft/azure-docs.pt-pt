---
title: Integrar aplicações com a rede Virtual do Azure - serviço de aplicações do Azure
description: Mostra-lhe como ligar uma aplicação no App Service do Azure a uma rede de virtual do Azure nova ou existente
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: b269c75be7fec55fb77afecc6d04b86266c74a6f
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147310"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar a sua aplicação com uma rede Virtual do Azure
Este documento descreve a funcionalidade de integração de rede virtual do App Service do Azure e como configurá-lo com as aplicações no [App Service do Azure](https://go.microsoft.com/fwlink/?LinkId=529714). [Redes virtuais do Azure] [ VNETOverview] (VNets) permitem-lhe colocar muitos dos seus recursos do Azure numa rede de endereçáveis não internet.  

O serviço de aplicações do Azure tem duas formas. 

1. Os sistemas de vários inquilinos que suportam a gama completa de planos exceto Isolated de preços
2. A aplicação ambiente de serviço (ASE), que implementa na sua VNet e suporta aplicações de plano de preços isolada

Este documento aborda os dois recursos de integração de VNet, que é usado no multi-inquilino de serviço de aplicações. Se a sua aplicação está em [ambiente do serviço de aplicações][ASEintro], em seguida, ele já se encontra numa VNet e não requer a utilização da funcionalidade de integração de VNet para aceder aos recursos na mesma VNet. Para obter detalhes sobre todos os recursos de rede do serviço de aplicações, leia [serviço de aplicações, funcionalidades de rede](networking-features.md)

Existem duas formas para a funcionalidade de integração de VNet

1. Uma versão permite a integração com VNets na mesma região. Essa forma do recurso requer uma sub-rede numa VNet na mesma região. Esta funcionalidade ainda está em pré-visualização, mas é suportada para cargas de trabalho de produção de aplicações de Windows, com algumas limitações indicadas abaixo.
2. A outra versão permite a integração com VNets noutras regiões ou com VNets clássicas. Esta versão da funcionalidade requer a implementação de um Gateway de rede Virtual na sua VNet. Este é o recurso VPN com base de ponto a site.

Uma aplicação só pode utilizar um formulário da funcionalidade de integração de VNet de cada vez. Em seguida, a pergunta é qual o recurso que utiliza. Pode usar para muitas coisas. No entanto são as diferenças claras:

| Problema  | Solução | 
|----------|----------|
| Quer alcançar um endereço de RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) na mesma região | Integração de VNet regional |
| Pretende chegar uma VNet clássica ou VNet noutra região | Gateway necessário integração de VNet |
| Quer alcançar pontos finais de RFC 1918 em ExpressRoute | Integração de VNet regional |
| Pretende aceder aos recursos em pontos finais de serviço | Integração de VNet regional |

Nenhum desses recursos permitirá que alcançar endereços não RFC 1918 em ExpressRoute. Para fazer isso, precisa usar um ASE por agora.

Através da integração de VNet regional não ligar o seu VNet no local ou configurar pontos finais de serviço. Que é separada de configuração de rede. A integração de VNet regional simplesmente permite à sua aplicação fazer chamadas entre esses tipos de ligação.

Independentemente da versão utilizada, integração de VNet fornece o acesso a aplicações web a recursos na sua rede virtual, mas não conceder acesso privado de entrada à sua aplicação web da rede virtual. Acesso a sites privada refere-se para tornar a sua aplicação apenas acessível a partir de uma rede privada, tais como a partir de dentro de uma rede virtual do Azure. Integração VNet está apenas para fazer chamadas de saída da sua aplicação na sua VNet. 

A funcionalidade de integração de VNet:

* requer um plano de preços Standard, Premium ou PremiumV2 
* oferece suporte a TCP e UDP
* funciona com aplicações de serviço de aplicações e aplicações de funções

Existem algumas coisas que não suporta a integração de VNet, incluindo:

* montar uma unidade
* A integração do AD 
* NetBios

## <a name="regional-vnet-integration"></a>Integração de VNet regional 

Quando a integração VNet é utilizada com VNets na mesma região que a sua aplicação, ele requer a utilização de uma sub-rede delegada com pelo menos 32 endereços na mesma. A sub-rede não pode ser utilizada para qualquer outra coisa. Saída chamadas feitas a partir da sua aplicação serão feitas a partir de endereços da sub-rede de delegado. Quando utilizar esta versão de integração de VNet, as chamadas são feitas a partir de endereços da vnet. Utilização de endereços da vnet, permite à sua aplicação para:

* fazer chamadas para serviços protegidos por ponto final de serviço
* aceder aos recursos através de ligações do ExpressRoute
* aceder a recursos na VNet estiver ligado a
* aceder aos recursos através de ligações de peering, incluindo ligações do ExpressRoute

Esta funcionalidade está em pré-visualização, mas é suportada para cargas de trabalho do Windows app produção com as seguintes limitações:

* apenas pode chegar endereços que estão no intervalo RFC 1918. Esses são endereços 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 blocos de endereços.
* Não é possível chegar a recursos através de ligações de peering global
* Não é possível definir rotas sobre o tráfego proveniente da sua aplicação na sua VNet
* a funcionalidade só está disponível a partir de unidades de escala de serviço de aplicações mais recente que o serviço de aplicações de PremiumV2 planos de suporte.
* a funcionalidade não pode ser utilizada por aplicações de plano isolado que estiverem num ambiente de serviço de aplicações
* a funcionalidade exige uma sub-rede não utilizada com pelo menos 32 endereços na sua VNet do Resource Manager.
* A aplicação e a VNet tem de estar na mesma região
* Um endereço é utilizado para cada instância de plano de serviço de aplicações. Uma vez que o tamanho de sub-rede não pode ser alterado após a atribuição, utilize uma sub-rede que mais do que pode cobrir o tamanho de dimensionamento máximo. / 27 com 32 endereços é o tamanho recomendado, pois o que seria acomodar um plano de serviço de aplicações é dimensionado para 20 instâncias.
* Não é possível eliminar uma VNet com uma aplicação integrada. Tem de remover primeiro a integração 
* Pode ter apenas uma integração de VNet regional por plano do serviço de aplicações. Várias aplicações no mesmo plano de serviço de aplicações podem utilizar a mesma VNet. 

A funcionalidade está em pré-visualização também para Linux. Para utilizar a funcionalidade de integração de VNet com uma VNet do Resource Manager na mesma região:

1. Vá para a interface do Usuário de rede no portal. Se a aplicação for capaz de usar o novo recurso, em seguida, verá uma opção para adicionar VNet (pré-visualização).  

   ![Selecione a integração de VNet][6]

1. Selecione **VNet adicionar (pré-visualização)** .  

1. Selecione a VNet do Resource Manager que pretende integrar com o e, em seguida, criar uma nova sub-rede ou escolha uma sub-rede já existente vazia. A integração leva menos de um minuto para concluir. Durante a integração, seu aplicativo é reiniciado.  Quando a integração é concluída, verá detalhes sobre a VNet estão integradas e uma faixa na parte superior que diz a que a funcionalidade está em pré-visualização.

   ![Selecione a VNet e sub-rede][7]

Assim que a aplicação é integrada com a sua VNet, irá utilizar o mesmo servidor DNS que a VNet está configurada com. 

Para desligar a sua aplicação da VNet, selecione **desligar**. Esta ação irá reiniciar a aplicação web. 

A nova funcionalidade de integração de VNet permite-lhe utilizar pontos finais de serviço.  Para utilizar pontos finais de serviço com a sua aplicação, utilize a nova integração de VNet para ligar a uma VNet selecionada e, em seguida, configurar pontos finais de serviço na sub-rede que utilizou para a integração. 

#### <a name="web-app-for-containers"></a>Aplicações Web para Contentores

Se utilizar o serviço de aplicações no Linux com imagens incorporadas, a funcionalidade de integração de VNet regional funciona sem alterações adicionais. Se utilizar a aplicação Web para contentores, terá de modificar a imagem do docker para utilizar a integração de VNet. Na sua imagem do docker, utilize a variável de ambiente de porta como porta de escuta do servidor web principal, em vez de utilizar um número de porta embutido em código. A variável de ambiente de porta é automaticamente definida pela plataforma de serviço de aplicações em tempo de inicialização de contentor.

### <a name="how-vnet-integration-works"></a>Como funciona a integração de VNet

As aplicações no serviço de aplicações são alojadas em funções de trabalho. Básico e mais elevado de planos de preços são dedicados a planos de alojamento em que existem não existem outras clientes cargas de trabalho em execução em funções de trabalho mesmo. Integração VNet funciona ao montar virtual interfaces com endereços da sub-rede de delegado. Uma vez que a partir do endereço é na sua VNet, tem acesso a maior parte das informações no ou através da sua VNet tal como faria com uma VM na sua VNet. A implementação do sistema de rede é diferente de executar uma VM na sua VNet e isto é por que alguns recursos de rede ainda não estão disponíveis ao utilizar esta funcionalidade.

![Integração de VNet](media/web-sites-integrate-with-vnet/vnet-integration.png)

Quando a integração VNet está ativada, a aplicação ainda fará chamadas de saída à internet através dos canais de mesmo como habitualmente. Os endereços de saída que estão listados no portal de propriedades da aplicação ainda são os endereços utilizados pela sua aplicação. Quais são as alterações para a sua aplicação que serviços protegidos por chamadas para o ponto final de serviço ou endereços de RFC 1918 entra na sua VNet. 

A funcionalidade suporta apenas uma interface virtual por função de trabalho.  Uma interface virtual por função de trabalho significa uma integração de VNet regional por plano do serviço de aplicações. Todas as aplicações no mesmo plano de serviço de aplicações podem utilizar o mesmo integração de VNet, mas se precisar de uma aplicação para ligar a uma VNet adicional, terá de criar outro plano do serviço de aplicações. A interface virtual usada não é um recurso que os clientes têm acesso direto a.

Devido à natureza de como funciona essa tecnologia, o tráfego que é utilizado com a integração de VNet não aparece nos registos de fluxo do observador de rede ou o NSG.  

## <a name="gateway-required-vnet-integration"></a>Gateway necessário integração de VNet 

O Gateway necessário a funcionalidade de integração de VNet:

* pode ser utilizado para ligar VNets em qualquer região sejam eles Resource Manager ou VNets clássicas
* permite que uma aplicação para ligar a VNet apenas 1 cada vez
* permite que até cinco VNets a ser integrado com um plano do serviço de aplicações 
* permite que a mesma VNet ser utilizado por várias aplicações num plano do serviço de aplicações sem afetar o número total, que pode ser utilizado por um plano de serviço de aplicações.  Se tiver 6 aplicações que utilizam a mesma VNet no mesmo plano de serviço de aplicações, que é contabilizada como 1 VNet que está a ser utilizada. 
* requer um Gateway de rede Virtual que está configurado com o ponto de VPN de Site
* Não é suportada para utilização com aplicações do Linux
* Suporta um SLA de 99,9% devido ao SLA no gateway

Esta funcionalidade não suporta:

* O acesso a recursos no ExpressRoute 
* O acesso a recursos nos Pontos Finais de Serviço 

### <a name="getting-started"></a>Introdução

Seguem-se alguns aspetos a ter em mente antes de ligar a sua aplicação web a uma rede virtual:

* Uma rede virtual de destino tem de ter ativado com um gateway baseado na rota antes de pode ser ligada à aplicação VPN de ponto a site. 
* A VNet tem de ser na mesma subscrição que sua Plan(ASP) do serviço de aplicações.
* As aplicações que se integram com uma utilização de VNet, o DNS especificado para essa VNet.

### <a name="set-up-a-gateway-in-your-vnet"></a>Configurar um gateway na sua VNet ###

Se já tiver um gateway configurado com endereços de ponto a site, pode ignorar a configuração de integração de VNet com a sua aplicação.  
Para criar um gateway:

1. [Criar uma sub-rede de gateway] [ creategatewaysubnet] na sua VNet.  

1. [Criar o gateway VPN][creategateway]. Selecione um tipo VPN baseado na rota.

1. [Defina o ponto de como endereços de sites][setp2saddresses]. Se o gateway não está no SKU básico, em seguida, IKEV2 tem de ser desativada na configuração de ponto a site e SSTP tem de ser selecionado. O espaço de endereços tem de ser blocos de endereços RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Se estiver apenas criar o gateway para utilizar com a integração de VNet de serviço de aplicações, em seguida, não é necessário carregar um certificado. Criar o gateway pode demorar 30 minutos. Não será capaz de integrar a sua aplicação com a sua VNet até que o gateway é aprovisionado. 

### <a name="configure-vnet-integration-with-your-app"></a>Configurar a integração de VNet com a sua aplicação 

Para ativar a integração de VNet na sua aplicação: 

1. Ir para a sua aplicação no portal do Azure e abrir as definições da aplicação e selecione o sistema de rede > integração de VNet. O ASP tem de ser um SKU padrão ou ainda melhores para utilizar qualquer funcionalidade de integração de VNet. 
 ![Integração de VNet da interface do Usuário][1]

1. Selecione **adicionar VNet**. 
 ![Adicionar a integração de VNet][2]

1. Selecione a sua VNet. 
  ![Selecione a sua VNet][8]
  
A aplicação vai ser reiniciada após este último passo.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Funciona como o gateway necessária a funcionalidade de integração de VNet

O gateway necessária a funcionalidade de integração de VNet é criada sobre tecnologia VPN ponto a site. A tecnologia de ponto a site limita o acesso à rede para apenas a máquina virtual que aloja a aplicação. As aplicações são restritas apenas enviar tráfego para a internet, através de ligações híbridas ou por meio da integração de VNet. 

![Como funciona a integração de VNet][3]

## <a name="managing-vnet-integration"></a>Gerir a integração de VNet
A capacidade para se ligar e desligar a uma VNet é ao nível da aplicação. As operações que podem afetar a integração de VNet em várias aplicações são ao nível do plano do serviço de aplicações. A partir da aplicação > redes > portal de integração de VNet, pode obter os detalhes na sua VNet. Pode ver informações semelhantes a nível de ASP no ASP > redes > portal de integração de VNet, incluindo a que aplicações nesse plano do serviço de aplicações estão a utilizar uma determinada integração.

 ![Detalhes de VNet][4]

As informações disponíveis para na interface de Usuário de integração de VNet é o mesmo entre os portais ASP e a aplicação.

* Nome da VNet - ligações para a rede virtual da interface do Usuário
* Localização - reflete a localização da sua VNet. Integrar com uma VNet em outro local, pode causar problemas de latência para a sua aplicação. 
* Estado de certificado - reflete se os certificados são sincronizados entre o seu plano do serviço de aplicações e a sua VNet.
* Estado do gateway - deve lhe estar a utilizar o gateway necessário integração VNet, pode ver o estado do gateway.
* Espaço de endereços da VNet - mostra o espaço de endereços IP para a sua VNet. 
* Espaço de endereços de ponto a site - mostra o ponto de espaço de endereços IP do site para a sua VNet. Ao efetuar chamadas para a sua VNet ao utilizar a funcionalidade necessária de gateway, o seu endereço de aplicação será um destes endereços. 
* Espaço de endereços de site a site - pode utilizar VPNs de site a site para ligar a VNet, aos seus recursos no local ou a outra VNet. Os intervalos IP definidos com essa conexão de VPN são mostrados aqui.
* Servidores DNS - mostra os servidores DNS configurados com a sua VNet.
* Endereços IP encaminhados para a VNet - mostra os blocos de endereços encaminhados utilizada para tráfego de unidade na sua VNet 

É a única operação que pode demorar na vista de aplicação de sua integração de VNet para desligar a sua aplicação da VNet está atualmente ligada. Para desligar a sua aplicação a partir de uma VNet, selecione **desligar**. A aplicação será reiniciada quando desligar a partir de uma VNet. A desligar não altera a sua VNet. A sub-rede ou o gateway não é removido. Se desejar, em seguida, ao eliminar a sua VNet, é preciso primeiro desligar a sua aplicação da VNet e eliminar os recursos nele como gateways. 

Para aceder a IU de integração de VNet de ASP, abra a interface do Usuário do ASP e selecione **redes**.  Em integração de VNet, selecione **clique aqui para configurar** para abrir a IU de estado do recurso de rede.

![Informações de integração de VNet ASP][5]

A interface de Usuário de integração de VNet ASP mostrará todas as VNets que são utilizadas pelas aplicações na sua ASP. Para ver detalhes sobre cada VNet, clique na VNet que está interessado. Existem duas ações que pode efetuar aqui.

* **Sincronizar rede**. A operação de rede de sincronização é apenas para a funcionalidade de integração de VNet dependente do gateway. Efetuar uma operação de sincronização de rede assegura que os seus certificados e informações de rede estão em sincronização. Se adicionar ou alterar o DNS da sua VNet, tem de efetuar uma **sincronizar rede** operação. Esta operação irá reiniciar quaisquer aplicações com esta VNet.
* **Adicionar rotas** adicionar rotas irá orientar o tráfego de saída na sua VNet.

**Encaminhamento** as rotas definidas na sua VNet são utilizadas para direcionar o tráfego numa VNet a partir de seu aplicativo. Se precisar de enviar o tráfego de saída adicional na VNet, em seguida, pode adicionar esses blocos de endereços aqui. Esta funcionalidade só funciona com o gateway necessário integração de VNet.

**Certificados** quando o gateway necessário integração de VNet ativada, não há um exchange necessário de certificados para garantir a segurança da ligação. Juntamente com os certificados são a configuração de DNS, as rotas e outros elementos semelhantes que descrevem a rede.
Se certificados ou informações de rede for alteradas, terá de clicar em "Sincronizar rede". Quando clicar em "Sincronizar rede", causar uma rápida interrupção na conectividade entre a aplicação e a sua VNet. Enquanto a aplicação não é reiniciada, a perda de conectividade pode fazer com que seu site não funcione corretamente. 

## <a name="accessing-on-premises-resources"></a>Aceder a recursos no local
Aplicações podem aceder a recursos no local através da integração com VNets com ligações site a site. Se estiver a utilizar o gateway necessário integração VNet, tem de atualizar as rotas de gateway VPN no local com os blocos de endereços de ponto a site. Quando a VPN de site a site é configurada em primeiro lugar, os scripts utilizados para configurá-lo devem configurar rotas corretamente. Se adicionar os endereços de ponto a site depois de criar a VPN de site a site, terá de atualizar manualmente as rotas. Obter detalhes sobre como fazer isso variam por gateway e não são descritas aqui. Não pode ter o BGP configurado com uma ligação de VPN de site a site.

Não existe nenhuma configuração adicional necessária para a funcionalidade de integração de VNet regional chegar através da sua VNet e no local. Simplesmente precisa de ligar o seu VNet no local com o ExpressRoute ou uma VPN de site a site. 

> [!NOTE]
> O gateway necessária a funcionalidade de integração de VNet não se integra uma aplicação com uma VNet com um Gateway do ExpressRoute. Mesmo que o Gateway do ExpressRoute está configurado nas [modo de coexistência] [ VPNERCoex] a integração de VNet não funciona. Se precisar de aceder aos recursos através de uma ligação ExpressRoute, pode utilizar a funcionalidade de integração de VNet regional ou uma [ambiente do serviço de aplicações][ASE], que é executada na sua VNet. 
> 
> 

## <a name="peering"></a>Peering
Se estiver a utilizar o peering com a integração de VNet regional, não é necessário efetuar qualquer configuração adicional. 

Se estiver a utilizar o gateway necessário integração VNet com o peering, terá de configurar alguns itens adicionais. Para configurar o peering para trabalhar com a sua aplicação:

1. Adicione que uma ligação de peering na VNet em modo de seu aplicativo se conecta ao. Ao adicionar a ligação peering, ative **permitir o acesso de rede virtual** e verifique **permitir tráfego reencaminhado** e **permitir trânsito de gateway**.
1. Adicione uma ligação de peering na VNet que está no modo de peering à VNet que está ligado à. Ao adicionar a ligação peering no destino VNet, ative **permitir o acesso de rede virtual** e verifique **permitir tráfego reencaminhado** e **permitir gateways remotos**.
1. Vá para o plano do serviço de aplicações > redes > interface de Usuário de integração de VNet no portal.  Selecione a VNet seu aplicativo se conecta ao. Na secção de encaminhamento, adicione o intervalo de endereços da VNet que está em modo de peering com a VNet à sua aplicação.  


## <a name="pricing-details"></a>Detalhes dos preços
A funcionalidade de integração de VNet regional tem sem qualquer custo adicional para utilização para além de ASP cobranças de escalão de preço.

Existem três custos relacionados ao uso da funcionalidade de integração de VNet é necessário um gateway:

* Custos de escalão preços ASP - as suas aplicações precisam de ser no Standard, Premium ou plano de serviço de aplicações do PremiumV2. Pode ver mais detalhes sobre esses custos aqui: [Preços do serviço de aplicações][ASPricing]. 
* Custos de transferência de dados - daí é um custo de saída de dados, mesmo se a VNet estiver no mesmo centro de dados. Esses custos são descritos num [detalhes de preços de transferência de dados][DataPricing]. 
* Os custos de Gateway de VPN - daí é um custo para o gateway de VNet é necessário para a VPN ponto a site. Os detalhes são sobre o [preços de Gateway de VPN] [ VNETPricing] página.


## <a name="troubleshooting"></a>Resolução de problemas
Embora a funcionalidade seja fácil de configurar, isso não significa que a sua experiência será problema gratuito. Caso encontre problemas para acessar o ponto de final desejado aqui estão alguns utilitários que pode usar para testar a conectividade a partir da consola de aplicação. Existem duas consolas, que pode utilizar. Uma é a consola Kudu e o outro é a consola no portal do Azure. Para aceder a consola Kudu a partir da sua aplicação, vá para ferramentas -> Kudu. Este é o mesmo que vai [sitename]. scm.azurewebsites.net. Uma vez que se abre, aceda ao separador consola de depuração. Para obter a consola alojada portal do Azure, em seguida, da sua aplicação go para ferramentas -> consola. 

#### <a name="tools"></a>Ferramentas
As ferramentas **ping**, **nslookup** e **tracert** não funcionará através da consola devido a restrições de segurança. Para preencher essa lacuna, duas ferramentas separadas adicionadas. Para testar a funcionalidade DNS, adicionamos uma ferramenta denominada nameresolver.exe. A sintaxe é:

    nameresolver.exe hostname [optional: DNS Server]

Pode usar **nameresolver** para verificar os nomes de anfitrião que depende da sua aplicação. Dessa forma pode testar se tiver qualquer coisa incorrectamente configurado com o seu DNS ou talvez não tem acesso ao seu servidor DNS. Pode ver o servidor DNS que a aplicação irá utilizar na consola do observando as variáveis de ambiente WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

A ferramenta da próxima permite-lhe testar a conectividade de TCP a uma combinação de porta e anfitrião. Essa ferramenta é chamada **tcpping** e a sintaxe é:

    tcpping.exe hostname [optional: port]

O **tcpping** utilitário informa se, pode entrar num anfitrião específico e a porta. Esta operação só pode mostrar êxito se: existe uma aplicação em escuta na combinação de porta e anfitrião e não existe acesso à rede da sua aplicação para o anfitrião especificado e a porta.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Depuração de acesso a VNet recursos alojados
Há uma série de coisas que pode impedir a sua aplicação de atingir um anfitrião específico e a porta. Na maioria das vezes ele é um dos três coisas:

* **É uma firewall da forma.** Se tiver uma firewall da forma, atingirá o tempo limite TCP. O tempo limite TCP neste caso é de 21 segundos. Utilize o **tcpping** ferramenta para testar a conectividade. Tempos limite TCP pode dever-se muitas coisas para além dos firewalls, mas começar por aqui. 
* **DNS não está acessível.** O tempo limite DNS é três segundos por servidor DNS. Se tiver dois servidores DNS, o tempo limite é de 6 segundos. Utilize nameresolver para ver se o DNS está a funcionar. Lembre-se de que não é possível utilizar o nslookup à medida que não usa o DNS a VNet está configurada com. Se inacessíveis, poderia ter uma firewall ou NSG a bloquear o acesso ao DNS ou pode estar inativos.

Se esses itens não responder os problemas, a olhar para coisas como: 

**Integração de VNet regional**
* é o destino de um endereço de RFC 1918
* Existe uma saída de bloqueio de NSG de sub-rede sua integração
* se numa VPN ou ExpressRoute, está o seu gateway no local configurado para encaminhar o tráfego de cópia de segurança para o Azure? Se pode entrar pontos finais na sua VNet, mas não no local, isso é bom para verificar.

**Gateway necessário integração de VNet**
* é o intervalo de endereços de ponto a site nos intervalos de RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* O Gateway mostra como sendo a cópia de segurança no portal? Se o gateway estiver em baixo, em seguida, colocá-lo de cópia de segurança.
* Voltar a mostrar de certificados como sendo sincronizadas ou suspeitar de que a configuração de rede foi alterada?  Se os certificados estão fora de sincronização ou se suspeitar que houve uma alteração efetuada à sua configuração de VNet que não foi sincronizada com o seu ASPs, em seguida, pressione "Sincronizar rede".
* se numa VPN ou ExpressRoute, está o seu gateway no local configurado para encaminhar o tráfego de cópia de segurança para o Azure? Se pode entrar pontos finais na sua VNet, mas não no local, isso é bom para verificar.

Depuração de problemas de rede é um desafio, pois existem não é possível ver o que está a bloquear o acesso a uma combinação de anfitrião: porta específica. Algumas das causas incluem:

* tiver uma firewall cópia de segurança no seu anfitrião impedir o acesso a porta da aplicação a partir do seu ponto de intervalo IP do site. Cruzamento entre sub-redes, muitas vezes, requer o acesso público.
* o anfitrião de destino está inativo
* seu aplicativo está inativo
* teve o IP ou nome de anfitrião errado
* a aplicação está a escutar numa porta diferente que esperava. Pode corresponder a seu ID de processo com a porta de escuta com o "netstat - aon" no anfitrião de ponto final. 
* os grupos de segurança de rede são configurados de forma que eles impedem o acesso ao seu anfitrião de aplicação e a porta do seu ponto de intervalo IP do site

Lembre-se de que não sabe que endereço de seu aplicativo, na verdade, irá utilizar. Pode ser qualquer endereço a integração ponto a site ou de sub-rede intervalo de endereços, por isso terá de permitir o acesso a partir do intervalo de endereços completo. 

Os passos de depuração adicionais incluem:

* ligar a uma VM na sua VNet e tentam aceder a recursos anfitrião: porta a partir daí. Para testar o acesso TCP, utilize o comando do PowerShell **test-netconnection**. A sintaxe é:

      test-netconnection hostname [optional: -Port]

* Abra um aplicativo numa VM e de teste de acesso a esse anfitrião e a partir da consola da sua aplicação através da porta **tcpping**

#### <a name="on-premises-resources"></a>Recursos no local ####

Se a sua aplicação não é possível chegar a um recurso no local, em seguida, verifique se pode aceder o recursos da sua VNet. Utilize o **test-netconnection** comando do PowerShell para verificar a existência de acesso TCP. Se a VM não é possível contactar o seu recurso no local, a ligação VPN ou ExpressRoute pode não estar corretamente configurada.

Se a VNet alojada VM possa alcançar seu sistema de no local, mas a aplicação não pode, então a causa é tratar de um dos seguintes motivos:

* as rotas não estão configuradas com a sub-rede ou de apontar para intervalos de endereços de site no seu gateway no local
* os grupos de segurança de rede estão a bloquear o acesso para o seu intervalo IP de ponto a Site
* as firewalls no local estão a bloquear o tráfego do seu intervalo de IP de ponto a Site
* está a tentar alcançar um endereço de não - RFC 1918 a utilizar a funcionalidade de integração de VNet regional


## <a name="powershell-automation"></a>Automatização de PowerShell

Pode integrar o serviço de aplicações com uma rede Virtual do Azure com o PowerShell. Para obter um script de prontos para executar, veja [ligar uma aplicação no serviço de aplicações do Azure a uma rede Virtual do Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


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
[creategatewaysubnet]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
