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
ms.date: 11/12/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: cddc6ccd8360ef88c95cd33c0bc8e141d721042d
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010519"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar a sua aplicação com uma rede Virtual do Azure
Este documento descreve a funcionalidade de integração de rede virtual do App Service do Azure e mostra como configurá-lo com as aplicações no [App Service do Azure](https://go.microsoft.com/fwlink/?LinkId=529714). [Redes virtuais do Azure] [ VNETOverview] (VNets) permitem-lhe colocar muitos dos seus recursos do Azure numa rede de endereçáveis não internet. Estas redes, em seguida, podem ser ligadas às suas redes no local usando as tecnologias VPN. 

O serviço de aplicações do Azure tem duas formas. 

1. Os sistemas de vários inquilinos que suportam a gama completa de planos exceto Isolated de preços
2. A aplicação ambiente de serviço (ASE), que implementa na sua VNet. 

Este documento aborda o recurso de integração de VNet, o que é destinado à utilização no multi-inquilino de serviço de aplicações.  Se a sua aplicação está em [ambiente do serviço de aplicações][ASEintro], em seguida, ele já se encontra numa VNet e não requer a utilização da funcionalidade de integração de VNet para aceder aos recursos na mesma VNet.

Integração VNet oferece o acesso a aplicações web a recursos na sua rede virtual, mas não conceder acesso privado à sua aplicação web da rede virtual. Acesso a sites privada refere-se para tornar a sua aplicação apenas acessível a partir de uma rede privada, tais como a partir de dentro de uma rede virtual do Azure. Acesso de sites privados só está disponível com um ASE configurado com uma carga balanceador interno (ILB). Para obter detalhes sobre como utilizar um ASE de ILB, começar com o artigo aqui: [Criar e utilizar um ASE de ILB][ILBASE]. 

Integração de VNet, muitas vezes, é utilizada para ativar o acesso a partir de aplicações para uma base de dados e web services em execução na sua VNet. Com a integração de VNet, não terá de expor um ponto final público para aplicações na sua VM, mas pode utilizam os endereços de encaminháveis privados não internet em vez disso. 

A funcionalidade de integração de VNet:

* requer um plano de preços Standard, Premium ou PremiumV2 
* funciona com clássica ou VNet do Resource Manager 
* oferece suporte a TCP e UDP
* funciona com a Web, móveis, aplicações API e aplicações de funções
* permite que uma aplicação para ligar a VNet apenas 1 cada vez
* permite que até cinco VNets a ser integrado com um plano do serviço de aplicações 
* permite que a mesma VNet ser utilizado por várias aplicações num plano do serviço de aplicações
* requer um Gateway de rede Virtual que está configurado com o ponto de VPN de Site
* suporta um SLA de 99,9% devido ao SLA no gateway

Existem algumas coisas que não suporta a integração de VNet, incluindo:

* montar uma unidade
* Integração do AD 
* NetBios
* acesso de sites privados
* a aceder a recursos de ExpressRoute 
* aceder a recursos nos pontos finais de serviço 

### <a name="getting-started"></a>Introdução
Seguem-se alguns aspetos a ter em mente antes de ligar a sua aplicação web a uma rede virtual:

* Uma rede virtual de destino tem de ter ativado com um gateway baseado na rota antes de pode ser ligada à aplicação VPN de ponto a site. 
* A VNet tem de ser na mesma subscrição que sua Plan(ASP) do serviço de aplicações.
* As aplicações que se integram com uma utilização de VNet, o DNS especificado para essa VNet.

## <a name="enabling-vnet-integration"></a>Ativar a integração de VNet

Há uma nova versão da funcionalidade de integração de VNet que está em pré-visualização. Ele não depende de VPN ponto a site e também suporta recursos ao aceder ao ExpressRoute ou pontos finais de serviço. Para saber mais sobre a nova capacidade de pré-visualização, vá para o final deste documento. 

### <a name="set-up-a-gateway-in-your-vnet"></a>Configurar um gateway na sua VNet ###

Se já tiver um gateway configurado com endereços de ponto a site, pode ignorar a configuração de integração de VNet com a sua aplicação.  
Para criar um gateway:

1. [Criar uma sub-rede de gateway] [ creategatewaysubnet] na sua VNet.  

1. [Criar o gateway VPN][creategateway]. Selecione um tipo VPN baseado na rota.

1. [Defina o ponto de como endereços de sites][setp2saddresses]. Se o gateway não está no SKU básico, em seguida, IKEV2 tem de ser desativada na configuração de ponto a site e SSTP tem de ser selecionado. O espaço de endereços tem de ser de uma das seguintes blocos de endereço:

* 10.0.0.0/8 - isso significa um intervalo de endereço IP de 10.0.0.0 a 10.255.255.255
* 172.16.0.0/12 - isso significa um intervalo de endereços IP de 172.16.0.0 para 172.31.255.255 
* 192.168.0.0/16 - isso significa um intervalo de endereços IP de 192.168.0.0 para 192.168.255.255

Se estiver apenas criar o gateway para utilizar com a integração de VNet de serviço de aplicações, em seguida, não é necessário carregar um certificado. Criar o gateway pode demorar 30 minutos. Não será capaz de integrar a sua aplicação com a sua VNet até que o gateway é aprovisionado. 

### <a name="configure-vnet-integration-with-your-app"></a>Configurar a integração de VNet com a sua aplicação ###

Para ativar a integração de VNet na sua aplicação: 

1. Ir para a sua aplicação no portal do Azure e abrir as definições da aplicação e selecione o sistema de rede > integração de VNet. Dimensionar a sua ASP para um SKU padrão ou ainda melhores para poder configurar a integração de VNet. 
 ![Integração de VNet da interface do Usuário][1]

1. Selecione **adicionar VNet**. 
 ![Adicionar a integração de VNet][2]

1. Selecione a sua VNet. 
  ![Selecione a sua VNet][8]
  
A aplicação vai ser reiniciada após este último passo.  

## <a name="how-the-system-works"></a>Como o sistema funciona
A funcionalidade de integração de VNet é criada sobre tecnologia VPN ponto a site. Aplicações no serviço de aplicações do Azure estão alojadas num sistema multi-inquilino, que impede o aprovisionamento de uma aplicação diretamente numa VNet. A tecnologia de ponto a site limita o acesso à rede para apenas a máquina virtual que aloja a aplicação. As aplicações são restritas apenas enviar tráfego para a internet, através de ligações híbridas ou por meio da integração de VNet. 

![Como funciona a integração de VNet][3]

## <a name="managing-the-vnet-integrations"></a>Gerir as integrações de VNet
A capacidade para se ligar e desligar a uma VNet é ao nível da aplicação. As operações que podem afetar a integração de VNet em várias aplicações são ao nível do plano do serviço de aplicações. A partir da aplicação UID, pode obter os detalhes na sua VNet. As mesmas informações também são mostradas ao nível do ASP. 

 ![Detalhes de VNet][4]

A página de estado da funcionalidade de rede, pode ver se a sua aplicação está ligada a sua VNet. Se o seu gateway de VNet estiver em baixo por algum motivo, seu estado é apresentado como não-conectado. 

As informações que tem agora disponível na aplicação da que IU de integração de VNet nível é o mesmo que as informações de detalhe, que obter a partir do ASP. Seguem-se esses itens:

* Nome da VNet - ligações para a rede virtual da interface do Usuário
* Localização - reflete a localização da sua VNet. Integrar com uma VNet em outro local, pode causar problemas de latência para a sua aplicação. 
* Estado de certificado - reflete se os certificados são sincronizados entre o seu plano do serviço de aplicações e a sua VNet.
* Estado do gateway - devem seus gateways ficar indisponíveis por algum motivo, em seguida, a aplicação não é possível aceder aos recursos na VNet. 
* Espaço de endereços da VNet - mostra o espaço de endereços IP para a sua VNet. 
* Espaço de endereços de ponto a site - mostra o ponto de espaço de endereços IP do site para a sua VNet. Ao efetuar chamadas para a sua VNet, o seu endereço de aplicação será um destes endereços. 
* Espaço de endereços de site a site - pode utilizar VPNs de site a site para ligar a VNet, aos seus recursos no local ou a outra VNet. Os intervalos IP definidos com essa conexão de VPN são mostrados aqui.
* Servidores DNS - mostra os servidores DNS configurados com a sua VNet.
* Endereços IP encaminhados para a VNet - mostra os blocos de endereços encaminhados utilizada para tráfego de unidade na sua VNet 

É a única operação que pode demorar na vista de aplicação de sua integração de VNet para desligar a sua aplicação da VNet está atualmente ligada. Para desligar a sua aplicação a partir de uma VNet, selecione **desligar**. A aplicação será reiniciada quando desligar a partir de uma VNet. A desligar não altera a sua VNet. A VNet e a respetiva configuração, incluindo os gateways permanece inalterado. Se desejar, em seguida, ao eliminar a sua VNet, terá de eliminar primeiro os recursos, incluindo os gateways. 

Para aceder a IU de integração de VNet de ASP, abra a interface do Usuário do ASP e selecione **redes**.  Em integração de VNet, selecione **clique aqui para configurar** para abrir a IU de estado do recurso de rede.

![Informações de integração de VNet ASP][5]

A interface de Usuário de integração de VNet ASP mostrará todas as VNets que são utilizadas pelas aplicações na sua ASP. Pode ter até 5 VNets conectados por qualquer número de aplicações no seu plano do serviço de aplicações. Cada aplicação pode ter apenas uma integração configurada. Para ver detalhes sobre cada VNet, clique na VNet que está interessado. Existem duas ações que pode efetuar aqui.

* **Sincronizar rede**. A operação de sincronização de rede certifica-se de que os seus certificados e informações de rede estão em sincronização. Se adicionar ou alterar o DNS da sua VNet, tem de efetuar uma **sincronizar rede** operação. Esta operação irá reiniciar quaisquer aplicações com esta VNet.
* **Adicionar rotas** adicionar rotas irá orientar o tráfego de saída na sua VNet.

**Encaminhamento** as rotas definidas na sua VNet são utilizadas para direcionar o tráfego numa VNet a partir de seu aplicativo. Se precisar de enviar o tráfego de saída adicional na VNet, em seguida, pode adicionar esses blocos de endereços aqui.   

**Certificados** a integração de VNet quando ativada, não existe uma troca de necessária de certificados para garantir a segurança da ligação. Juntamente com os certificados são a configuração de DNS, as rotas e outros elementos semelhantes que descrevem a rede.
Se certificados ou informações de rede for alteradas, terá de clicar em "Sincronizar rede". Quando clicar em "Sincronizar rede", causar uma rápida interrupção na conectividade entre a aplicação e a sua VNet. Enquanto a aplicação não é reiniciada, a perda de conectividade pode fazer com que seu site não funcione corretamente. 

## <a name="accessing-on-premises-resources"></a>Aceder a recursos no local
Aplicações podem aceder a recursos no local através da integração com VNets com ligações site a site. Para aceder a recursos no local, terá de atualizar as rotas de gateway VPN no local com os blocos de endereços de ponto a site. Quando a VPN de site a site é configurada em primeiro lugar, os scripts utilizados para configurá-lo devem configurar rotas corretamente. Se adicionar os endereços de ponto a site depois de criar a VPN de site a site, terá de atualizar manualmente as rotas. Obter detalhes sobre como fazer isso variam por gateway e não são descritas aqui. Além disso, não pode ter o BGP configurado com uma ligação de VPN de site a site.

> [!NOTE]
> A funcionalidade de integração de VNet não se integra uma aplicação com uma VNet com um Gateway do ExpressRoute. Mesmo que o Gateway do ExpressRoute está configurado nas [modo de coexistência] [ VPNERCoex] a integração de VNet não funciona. Se precisa aceder aos recursos através de uma ligação ExpressRoute, pode utilizar um [ambiente do serviço de aplicações][ASE], que é executada na sua VNet. 
> 
> 

## <a name="peering"></a>Peering
Pode utilizar a integração de VNet para aceder a recursos em VNets em modo de peering para a VNet está ligado à. Para configurar o peering para trabalhar com a sua aplicação:

1. Adicione que uma ligação de peering na VNet em modo de seu aplicativo se conecta ao. Ao adicionar a ligação peering, ative **permitir o acesso de rede virtual** e verifique **permitir tráfego reencaminhado** e **permitir trânsito de gateway**.
1. Adicione uma ligação de peering na VNet que está no modo de peering à VNet que está ligado à. Ao adicionar a ligação peering no destino VNet, ative **permitir o acesso de rede virtual** e verifique **permitir tráfego reencaminhado** e **permitir gateways remotos**.
1. Vá para o plano do serviço de aplicações > redes > interface de Usuário de integração de VNet no portal.  Selecione a VNet seu aplicativo se conecta ao. Na secção de encaminhamento, adicione o intervalo de endereços da VNet que está em modo de peering com a VNet à sua aplicação.  


## <a name="pricing-details"></a>Detalhes dos preços
Existem três custos relacionados ao uso da funcionalidade de integração de VNet:

* Requisitos de escalão de preço de ASP
* Custos da transferência de dados
* Custos de Gateway de VPN.

As suas aplicações tem de estar no Standard, Premium ou plano de serviço de aplicações do PremiumV2. Pode ver mais detalhes sobre esses custos aqui: [Preços do serviço de aplicações][ASPricing]. 

Existe um custo de saída de dados, mesmo se a VNet estiver no mesmo centro de dados. Esses custos são descritos num [detalhes de preços de transferência de dados][DataPricing]. 

Há um custo para o gateway de VNet é necessário para a VPN ponto a site. Os detalhes são sobre o [preços de Gateway de VPN] [ VNETPricing] página.

## <a name="troubleshooting"></a>Resolução de problemas
Embora a funcionalidade seja fácil de configurar, isso não significa que a sua experiência será problema gratuito. Caso encontre problemas para acessar o ponto de final desejado aqui estão alguns utilitários que pode usar para testar a conectividade a partir da consola de aplicação. Existem duas consolas, que pode utilizar. Uma é a consola Kudu e o outro é a consola no portal do Azure. Para aceder a consola Kudu a partir da sua aplicação, vá para ferramentas -> Kudu. Este é o mesmo que vai [sitename]. scm.azurewebsites.net. Uma vez que se abre, aceda ao separador consola de depuração. Para obter a consola alojada portal do Azure, em seguida, da sua aplicação go para ferramentas -> consola. 

#### <a name="tools"></a>Ferramentas
As ferramentas **ping**, **nslookup** e **tracert** não funcionará através da consola devido a restrições de segurança. Para preencher essa lacuna, duas ferramentas separadas adicionadas. Para testar a funcionalidade DNS, adicionamos uma ferramenta denominada nameresolver.exe. A sintaxe é:

    nameresolver.exe hostname [optional: DNS Server]

Pode usar **nameresolver** para verificar os nomes de anfitrião que depende da sua aplicação. Dessa forma pode testar se tiver qualquer coisa incorrectamente configurado com o seu DNS ou talvez não tem acesso ao seu servidor DNS.

A ferramenta da próxima permite-lhe testar a conectividade de TCP a uma combinação de porta e anfitrião. Essa ferramenta é chamada **tcpping** e a sintaxe é:

    tcpping.exe hostname [optional: port]

O **tcpping** utilitário informa se, pode entrar num anfitrião específico e a porta. Esta operação só pode mostrar êxito se: existe uma aplicação em escuta na combinação de porta e anfitrião e não existe acesso à rede da sua aplicação para o anfitrião especificado e a porta.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Depuração de acesso a VNet recursos alojados
Há uma série de coisas que pode impedir a sua aplicação de atingir um anfitrião específico e a porta. Na maioria das vezes ele é um dos três coisas:

* **É uma firewall da forma.** Se tiver uma firewall da forma, atingirá o tempo limite TCP. O tempo limite TCP neste caso é de 21 segundos. Utilize o **tcpping** ferramenta para testar a conectividade. Tempos limite TCP pode dever-se muitas coisas para além dos firewalls, mas começar por aqui. 
* **DNS não está acessível.** O tempo limite DNS é três segundos por servidor DNS. Se tiver dois servidores DNS, o tempo limite é de 6 segundos. Utilize nameresolver para ver se o DNS está a funcionar. Lembre-se de que não é possível utilizar o nslookup à medida que não usa o DNS a VNet está configurada com.
* **O intervalo de endereços de ponto a site é inválido.** O intervalo IP de ponto a site precisa de estar em intervalos de IP privados a RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Se o intervalo de utiliza IPs fora isso, as coisas não funcionam. 

Se esses itens não responder seu problema, a olhar para as coisas simples, como: 

* O Gateway mostra como sendo a cópia de segurança no portal?
* Certificados mostram como sendo sincronizados?
* Qualquer pessoa alterou a configuração de rede sem fazer uma "rede de sincronização" nos ASPs afetados? 

Se o gateway estiver em baixo, em seguida, colocá-lo de cópia de segurança. Se os certificados estão fora de sincronização, em seguida, ir para a vista ASP de sua integração de VNet e pressionar "Sincronizar rede". Se suspeitar que houve uma alteração efetuada à sua configuração de VNet que não foi sincronizada com o seu ASPs, em seguida, pressione "Sincronizar rede".  Uma operação de "Rede de sincronização" irá reiniciar quaisquer aplicações no ASP que estão integradas essa VNet. 

Se tudo isso é tudo bem, em seguida, terá de aprofundar um pouco mais para o serviço:

* Existem todas as outras aplicações a utilizar a integração de VNet para aceder aos recursos na mesma VNet? 
* Pode ir para a consola de aplicação e utilizar tcpping para chegar a outros recursos na sua VNet? 

Se qualquer um dos acima for VERDADEIRO, em seguida, a integração de VNet estiver tudo bem e o problema é em algum lugar. Isso é onde ele obtém a ser mais um desafio porque não é possível simple para ver por que não é possível contactar uma anfitrião: porta. Algumas das causas incluem:

* tiver uma firewall cópia de segurança no seu anfitrião impedir o acesso a porta da aplicação a partir do seu ponto de intervalo IP do site. Cruzamento entre sub-redes, muitas vezes, requer o acesso público.
* o anfitrião de destino está inativo
* seu aplicativo está inativo
* teve o IP ou nome de anfitrião errado
* a aplicação está a escutar numa porta diferente que esperava. Pode corresponder a seu ID de processo com a porta de escuta com o "netstat - aon" no anfitrião de ponto final. 
* os grupos de segurança de rede são configurados de forma que eles impedem o acesso ao seu anfitrião de aplicação e a porta do seu ponto de intervalo IP do site

Lembre-se de que não sabe quais IP no seu intervalo de IP de ponto a Site que a aplicação irá utilizar, por isso terá de autorizar o acesso o todo um intervalo. 

Os passos de depuração adicionais incluem:

* ligar a uma VM na sua VNet e tentam aceder a recursos anfitrião: porta a partir daí. Para testar o acesso TCP, utilize o comando do PowerShell **test-netconnection**. A sintaxe é:

      test-netconnection hostname [optional: -Port]

* Trazer um aplicativo numa VM e de teste de acesso ao anfitrião e na porta partir da consola da sua aplicação

#### <a name="on-premises-resources"></a>Recursos no local ####

Se a sua aplicação não é possível chegar a um recurso no local, em seguida, verifique se pode aceder o recursos da sua VNet. Utilize o **test-netconnection** comando do PowerShell para verificar a existência de acesso TCP. Se a VM não é possível contactar o seu recurso no local, em seguida, certifique-se da que ligação de VPN de Site para Site está a funcionar. Se estiver a funcionar, verifique as mesmas coisas observadas anteriormente, bem como a configuração do gateway no local e o estado. 

Se a VNet alojada VM possa alcançar seu sistema de no local, mas a aplicação não pode, então a causa é tratar de um dos seguintes motivos:

* as rotas não estão configuradas com o seu ponto de intervalos IP do site no seu gateway no local
* os grupos de segurança de rede estão a bloquear o acesso para o seu intervalo IP de ponto a Site
* as firewalls no local estão a bloquear o tráfego do seu intervalo de IP de ponto a Site


## <a name="powershell-automation"></a>Automatização de PowerShell

Pode integrar o serviço de aplicações com uma rede Virtual do Azure com o PowerShell. Para obter um script de prontos para executar, veja [ligar uma aplicação no serviço de aplicações do Azure a uma rede Virtual do Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).

## <a name="hybrid-connections-and-app-service-environments"></a>As ligações híbridas e ambientes de serviço de aplicações
Existem três recursos que permitem o acesso aos recursos da VNet alojado. São:

* Integração de VNet
* Ligações Híbridas
* Ambientes do App Service

Ligações híbridas necessitam que instale um agente de retransmissão chamado Manager(HCM) de ligação híbrida na sua rede. O HCM tem de conseguir ligar-se para o Azure e também a sua aplicação. Ligações híbridas não requer um entrada internet ponto final de acesso para a sua rede remota, como é necessário para uma ligação VPN. O HCM só é executado no Windows e pode ter até cinco instâncias em execução para proporcionar elevada disponibilidade. Ligações híbridas só suporta TCP no entanto, e cada ponto de final do HC tem a correspondência com uma combinação de anfitrião: porta específica. 

A funcionalidade Ambiente de serviço de aplicações permite-lhe executar uma instância de único inquilino do serviço de aplicações do Azure na sua VNet. Se as suas aplicações estão num ambiente de serviço de aplicações, em seguida, as suas aplicações podem aceder aos recursos na sua VNet sem quaisquer etapas extras. Com e o ambiente de serviço de aplicações, as suas aplicações executam em funções de trabalho mais potentes e podem ser dimensionado até 100 instâncias ASP. Ambientes de serviço de aplicações funcionam com todos os recursos de rede, incluindo o ExpressRoute e de pontos finais de serviço.  

Embora haja que algumas utilizam a sobreposição de caso, nenhum desses recursos pode substituir qualquer um dos outros. Saber que o recurso a usar está associado às suas necessidades. Por exemplo:

* Se for um programador e quer executar um site no Azure que pode aceder uma base de dados na estação de trabalho em sua mesa, em seguida, a coisa mais fácil de usar é ligações híbridas. 
* Se for uma empresa de grande porte que deseja colocar um grande número de propriedades da web em público e geri-los na sua própria rede na cloud, em seguida, quer chegar com o ambiente de serviço de aplicações. 
* Se tiver várias aplicações que precisam de aceder a recursos na sua VNet, integração de VNet é a melhor opção. 

Quando a VNet já está ligada à sua rede no local, em seguida, utilizar a integração de VNet ou um ambiente de serviço de aplicações é uma forma fácil de consumir recursos no local. Se a VNet não está ligada à sua rede no local, em seguida, é muito mais sobrecarga para configurar uma VPN de site a site com a sua VNet em comparação com a instalação de HCM. 

Além das diferenças funcionais, lá são também as diferenças de preços. A funcionalidade Ambiente de serviço de aplicações é uma Premium oferta de serviço oferece, mas rede mais possibilidades de configuração, além de outros excelentes recursos. Integração VNet pode ser utilizada com Standard ou Premium ASPs e é perfeita para o consumo de forma segura recursos na sua VNet a partir do serviço de aplicações de multi-inquilino. Ligações híbridas atualmente depende de uma conta que possua a preços níveis que comece gratuitamente e, em seguida, se tornar cada vez mais caro consoante a quantidade que precisa de BizTalk. Quando se trata de trabalhar em várias redes, no entanto, não há nenhum outro recurso, como as ligações híbridas, que podem permitir-lhe aceder a recursos em mais de 100 redes separadas. 

## <a name="new-vnet-integration"></a>Nova integração de VNet ##

Há uma nova versão da capacidade de integração de VNet não depende da tecnologia VPN ponto a Site. Ao contrário do recurso já existente, a nova funcionalidade de pré-visualização irá funcionar com o ExpressRoute e de pontos finais de serviço. 

A nova capacidade só está disponível a partir de unidades de escala mais recente do serviço de aplicações do Azure. Se pode ser dimensionado para PremiumV2, em seguida, é numa unidade de escala mais recente do serviço de aplicações. A interface do Usuário de integração de VNet, no portal do informará se a sua aplicação pode utilizar a nova funcionalidade de integração de VNet. 

A nova versão está em pré-visualização e tem as seguintes características.

* Nenhum gateway é necessário para utilizar a nova funcionalidade de integração de VNet
* Pode aceder a recursos através de ligações do ExpressRoute sem qualquer configuração adicional para além da integração com o ExpressRoute ligado VNet.
* A aplicação e a VNet tem de estar na mesma região
* O novo recurso exige uma sub-rede não utilizada na sua VNet do Resource Manager.
* Seu plano do serviço de aplicações tem de ser um plano Standard, Premium ou PremiumV2
* Cargas de trabalho de produção não são suportadas no novo recurso enquanto estiverem em pré-visualização
* A aplicação tem de ser numa implantação do App Service do Azure que seja capaz de aumentar verticalmente para Premium v2.
* A nova funcionalidade de integração de VNet não funciona para aplicações num ambiente de serviço de aplicações.
* Não é possível eliminar uma VNet com uma aplicação integrada.  
* Tabelas de rotas e o global peering ainda não estão disponíveis com a nova integração de VNet.  
* Um endereço é utilizado para cada instância de plano de serviço de aplicações. Uma vez que o tamanho de sub-rede não pode ser alterado após a atribuição, utilize uma sub-rede que mais do que pode cobrir o tamanho de dimensionamento máximo. / 27 com 32 endereços é o tamanho recomendado, pois o que seria acomodar um plano de serviço de aplicações é dimensionado para 20 instâncias.  Pode consumir recursos usando a nova capacidade de integração de VNet protegidos pelo ponto final de serviço. Para tal, ative pontos finais de serviço na sub-rede utilizada para a integração de VNet.

Para utilizar o novo recurso:

1. Vá para a interface do Usuário de rede no portal. Se a aplicação for capaz de usar o novo recurso, em seguida, irá ver uma capacidade para utilizar a nova funcionalidade de pré-visualização.  

 ![Selecione a nova pré-visualização da integração de VNet][6]

1. Selecione **VNet adicionar (pré-visualização)**.  

1. Selecione a VNet do Resource Manager que pretende integrar com o e, em seguida, criar uma nova sub-rede ou escolha uma sub-rede já existente vazia. A integração leva menos de um minuto para concluir. Durante a integração, seu aplicativo é reiniciado.  Quando a integração é concluída, verá detalhes sobre a VNet estão integradas e uma faixa na parte superior que diz a que a funcionalidade está em pré-visualização.

 ![Selecione a VNet e sub-rede][7]

Para ativar a aplicação para utilizar o servidor DNS que a VNet está configurada com, crie uma definição da aplicação para a sua aplicação em que o nome é WEBSITE_DNS_SERVER e o valor é o endereço IP do servidor.  Se tiver um servidor DNS secundário, em seguida, criar outra definição de aplicação em que o nome é WEBSITE_DNS_ALT_SERVER e o valor é o endereço IP do servidor. 

Para desligar a sua aplicação da VNet, selecione **desligar**. Esta ação irá reiniciar a aplicação web. 

A nova funcionalidade de integração de VNet permite-lhe utilizar pontos finais de serviço.  Para utilizar pontos finais de serviço com a sua aplicação, utilize a nova integração de VNet para ligar a uma VNet selecionada e, em seguida, configurar pontos finais de serviço na sub-rede que utilizou para a integração. 

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
