---
title: Configurar ASE v1
description: Configuração, gestão e monitorização do App Service Environment v1. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: ccompy
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: b37708e27887b20604a1fe921f14e51387793737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687265"
---
# <a name="configuring-an-app-service-environment-v1"></a>Configurar um ambiente de serviço de aplicações v1

> [!NOTE]
> Este artigo é sobre o App Service Environment v1.  Existe uma versão mais recente do App Service Environment que é mais fácil de usar e funciona em infraestruturas mais poderosas. Para saber mais sobre a nova versão comece com a Introdução ao Ambiente de Serviço de [Aplicações.](intro.md)
> 

## <a name="overview"></a>Descrição geral
A um nível elevado, um Ambiente de Serviço de Aplicações Azure é composto por vários componentes principais:

* Compute recursos que estão em execução no serviço app service Ambiente hospedado
* Storage
* Uma base de dados
* Uma Rede Virtual Azure (VNet) clássica (V1) ou Gestor de Recursos (V2) 
* Uma subnet com o app service environment hospedado serviço funcionando nele

### <a name="compute-resources"></a>Recursos computacionais
Você usa os recursos computacionais para os seus quatro recursos.  Cada App Service Environment (ASE) tem um conjunto de extremidades dianteiras e três possíveis piscinas de trabalhadores. Não precisa usar as três piscinas de trabalhadores, se quiser, pode usar apenas uma ou duas.

Os anfitriões nas piscinas de recursos (extremidades dianteiras e trabalhadores) não são diretamente acessíveis aos inquilinos. Não é possível utilizar o Protocolo de Ambiente de Trabalho Remoto (RDP) para se ligar a eles, alterar o seu fornecimento ou agir como um administrador sobre eles.

Você pode definir quantidade e tamanho do conjunto de recursos. Numa ASE, tem quatro opções de tamanho, que são rotuladas de P1 a P4. Para mais detalhes sobre esses tamanhos e os seus preços, consulte os preços do Serviço de [Aplicações](https://azure.microsoft.com/pricing/details/app-service/).
Mudar a quantidade ou o tamanho chama-se operação de escala.  Só uma operação de escala pode estar em andamento de cada vez.

**Extremidades dianteiras**: As extremidades dianteiras são os pontos finais HTTP/HTTPS para as suas apps que se encontram na sua ASE. Não se faz cargas de trabalho nas extremidades da frente.

* Uma ASE começa com dois P2s, o que é suficiente para trabalhos de trabalho de v/teste e cargas de trabalho de produção de baixo nível. Recomendamos vivamente P3s para cargas de trabalho de produção moderadas a pesadas.
* Para cargas de trabalho de produção moderadas a pesadas, recomendamos que tenha pelo menos quatro P3 para garantir que existem extremidades dianteiras suficientes quando a manutenção programada ocorrer. As atividades de manutenção programadas vão derrubar uma frente de cada vez. Isto reduz a capacidade frontal disponível durante as atividades de manutenção.
* As extremidades dianteiras podem levar até uma hora para o provisionamento. 
* Para uma nova afinação em escala, deve monitorizar a percentagem de CPU, a percentagem de memória e as métricas de Pedidos Ativos para o pool frontal. Se as percentagens de CPU ou Memória forem superiores a 70% ao executar P3s, adicione mais extremidades dianteiras. Se o valor dos Pedidos Ativos for de 15.000 a 20.000 pedidos por frente, também deverá adicionar mais extremidades dianteiras. O objetivo geral é manter as percentagens de CPU e Memória abaixo dos 70%, e os Pedidos Ativos com uma média inferior a 15.000 pedidos por frente quando estiver a executar P3s.  

**Trabalhadores**: Os trabalhadores são onde as suas apps realmente funcionam. Ao aumentar os seus planos de Serviço de Aplicações, isso utiliza trabalhadores na piscina de trabalhadores associados.

* Não se pode adicionar trabalhadores instantaneamente. Podem levar até uma hora para o fornecimento.
* A escalagem do tamanho de um recurso computacional para qualquer piscina levará < 1 hora por domínio de atualização. Existem 20 domínios de atualização numa ASE. Se escalou o tamanho da computação de uma piscina de trabalhadores com 10 instâncias, pode levar até 10 horas para ser concluído.
* Se alterar o tamanho dos recursos computacionais que são usados numa piscina de trabalhadores, irá causar arranques frios das aplicações que estão a funcionar naquela piscina de trabalhadores.

A forma mais rápida de alterar o tamanho do recurso computacional de um grupo de trabalhadores que não está a executar nenhuma aplicação é:

* Reduza a quantidade de trabalhadores para 2.  A escala mínima de tamanho no portal é 2. Levará alguns minutos para desalocar as suas instâncias. 
* Selecione o novo tamanho da computação e o número de instâncias. A partir daqui, levará até 2 horas para ser concluído.

Se as suas aplicações requerem um tamanho maior de recursos computacionais, não pode tirar partido da orientação anterior. Em vez de mudar o tamanho da piscina de trabalhadores que está hospedando essas aplicações, você pode povoar outra piscina de trabalhadores com trabalhadores do tamanho desejado e mover suas aplicações para aquela piscina.

* Crie as instâncias adicionais do tamanho da computação necessária em outra piscina de trabalhadores. Isto levará até uma hora para ser concluído.
* Reatribua os seus planos de Serviço de Aplicações que estão hospedando as aplicações que precisam de um tamanho maior para o recém-configurado grupo de trabalhadores. Esta é uma operação rápida que deve demorar menos de um minuto para ser concluída.  
* Reduza a primeira piscina de trabalhadores se já não precisar desses casos não utilizados. Esta operação leva alguns minutos para ser concluída.

**Autoscalcificação**: Uma das ferramentas que pode ajudá-lo a gerir o seu consumo de recursos computacionais é a autoscalcificação. Você pode usar autoscalcificação para piscinas frontais ou operárias. Você pode fazer coisas como aumentar seus casos de qualquer tipo de piscina de manhã e reduzi-lo à noite. Ou talvez se possa adicionar casos em que o número de trabalhadores disponíveis numa piscina de trabalhadores cai abaixo de um determinado limiar.

Se quiser definir regras de escala automática em torno das métricas do conjunto de recursos computacionais, tenha em mente o tempo que o provisionamento requer. Para mais detalhes sobre ambientes de serviço de aplicações autodimensionadas, consulte [como configurar automaticamente em um Ambiente][ASEAutoscale]de Serviço de Aplicações .

### <a name="storage"></a>Storage
Cada ASE está configurada com 500 GB de armazenamento. Este espaço é utilizado em todas as aplicações da ASE. Este espaço de armazenamento é uma parte da ASE e atualmente não pode ser trocado para usar o seu espaço de armazenamento. Se estiver a fazer ajustes no seu encaminhamento ou segurança de rede virtual, precisa de permitir o acesso ao Armazenamento Azure - ou a ASE não pode funcionar.

### <a name="database"></a>Base de Dados
A base de dados detém a informação que define o ambiente, bem como os detalhes sobre as aplicações que estão a ser recorridas dentro dela. Isto também faz parte da subscrição detida pelo Azure. Não é algo que tenhas uma capacidade direta de manipular. Se estiver a fazer ajustes no seu encaminhamento ou segurança de rede virtual, precisa de permitir o acesso ao SQL Azure-- ou a ASE não pode funcionar.

### <a name="network"></a>Rede
O VNet que é usado com a sua ASE pode ser aquele que fez quando criou a ASE ou uma que fez com antecedência. Quando se cria a sub-rede durante a criação da ASE, força a ASE a estar no mesmo grupo de recursos que a rede virtual. Se precisa que o grupo de recursos utilizado pela sua ASE seja diferente do do seu VNet, então precisa de criar a sua ASE utilizando um modelo de gestor de recursos.

Existem algumas restrições na rede virtual que é usada para uma ASE:

* A rede virtual deve ser uma rede virtual regional.
* Tem de haver uma subrede com 8 ou mais endereços onde a ASE está implantada.
* Depois de uma sub-rede ser utilizada para alojar uma ASE, a gama de endereços da sub-rede não pode ser alterada. Por esta razão, recomendamos que a sub-rede contenha pelo menos 64 endereços para acomodar qualquer crescimento futuro da ASE.
* Não pode haver mais nada na sub-rede a não ser a ASE.

Ao contrário do serviço hospedado que contém a ASE, a [rede virtual][virtualnetwork] e a sub-rede estão sob o controlo do utilizador.  Pode administrar a sua rede virtual através da Rede Virtual UI ou PowerShell.  Uma ASE pode ser implantada num VNet Classic ou Resource Manager.  As experiências do portal e da API são ligeiramente diferentes entre vNets clássicos e gestores de recursos, mas a experiência DaSE é a mesma.

O VNet que é usado para hospedar uma ASE pode usar endereços IP RFC1918 privados ou pode usar endereços IP públicos.  Se desejar utilizar uma gama IP que não seja abrangida pelo RFC1918 (10.0.0.0.0.8, 172.16.0.0.0/12, 192.168.0.0.0/16) então precisa de criar o seu VNet e subnet para ser utilizado pela sua ASE antes da criação da ASE.

Uma vez que esta capacidade coloca o Serviço de Aplicações Azure na sua rede virtual, significa que as suas aplicações que estão alojadas na sua ASE podem agora aceder diretamente a recursos que são disponibilizados através do ExpressRoute ou redes privadas virtuais (VPNs) do site-para-site. As aplicações que estão dentro do seu App Service Environment não necessitam de funcionalidades adicionais de networking para aceder aos recursos disponíveis para a rede virtual que está a acolher o seu App Service Environment. Isto significa que não precisa de utilizar a Integração VNET ou Ligações Híbridas para chegar aos recursos ou ligados à sua rede virtual. Ainda pode utilizar ambas as funcionalidades para aceder a recursos em redes que não estejam ligadas à sua rede virtual.

Por exemplo, pode utilizar a Integração VNET para integrar com uma rede virtual que está na sua subscrição mas não está ligada à rede virtual em que a sua ASE se encontra. Você também pode usar Conexões Híbridas para aceder a recursos que estão em outras redes, assim como você normalmente pode.  

Se tiver a sua rede virtual configurada com uma VPN ExpressRoute, deve estar ciente de algumas das necessidades de encaminhamento que uma ASE tem. Existem algumas configurações de rota definidas pelo utilizador (UDR) que são incompatíveis com uma ASE. Para mais detalhes sobre a execução de uma ASE numa rede virtual com expressRoute, consulte Executar um Ambiente de Serviço de [Aplicações numa rede virtual com expressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Assegurar o tráfego de entrada
Existem dois métodos primários para controlar o tráfego de entrada na sua ASE.  Pode utilizar os Grupos de Segurança da Rede (NSGs) para controlar quais os endereços IP que podem aceder à sua ASE, conforme descrito aqui Como controlar o tráfego de entrada num Ambiente de Serviço de [Aplicações](app-service-app-service-environment-control-inbound-traffic.md) e também pode configurar a Sua ASE com um Balancer de Carga Interna (ILB).  Estas funcionalidades também podem ser utilizadas em conjunto se pretender restringir o acesso utilizando NSGs para o seu ILB ASE.

Quando criar uma ASE, criará um VIP no seu VNet.  Existem dois tipos VIP, externos e internos.  Quando criar uma ASE com um VIP externo, as suas aplicações na ASE estarão acessíveis através de um endereço IP de saída da Internet. Quando selecionar Internal, a sua ASE será configurada com um ILB e não estará diretamente acessível à Internet.  Um ILB ASE ainda requer um VIP externo, mas é apenas utilizado para acesso à gestão e manutenção do Azure.  

Durante a criação da ILB ASE fornece o subdomínio utilizado pelo ILB ASE e terá de gerir o seu próprio DNS para o subdomínio que especifica.  Uma vez que definiu o nome de subdomínio, também precisa de gerir o certificado utilizado para acesso HTTPS.  Após a criação da ASE, é solicitado a fornecer o certificado.  Para saber mais sobre a criação e utilização de um ILB ASE lido [usando um balancer de carga interna com um ambiente][ILBASE]de serviço de aplicação . 

## <a name="portal"></a>Portal
Pode gerir e monitorizar o seu App Service Environment utilizando o UI no portal Azure. Se tiver uma ASE, é provável que veja o símbolo do Serviço de Aplicações na sua barra lateral. Este símbolo é utilizado para representar ambientes de serviço de aplicação no portal Azure:

![Símbolo do ambiente do serviço de aplicações][1]

Para abrir o UI que lista todos os seus Ambientes de Serviço de Aplicações, pode utilizar o ícone ou selecionar o símbolo chevron (">") na parte inferior da barra lateral para selecionar Ambientes de Serviço de Aplicações. Ao selecionar uma das ASEs listadas, abre o UI que é usado para monitorizá-lo e geri-lo.

![UI para monitorizar e gerir o seu Ambiente de Serviço de Aplicações][2]

Esta primeira lâmina mostra algumas propriedades da sua ASE, juntamente com um gráfico métrico por conjunto de recursos. Algumas das propriedades que são mostradas no bloco **Essentials** são também hiperligações que abrirão a lâmina que está associada a ela. Por exemplo, pode selecionar o nome **rede virtual** para abrir o UI associado à rede virtual em que a sua ASE está a funcionar. **Planos** de Serviço de Aplicações e **Apps** abrem cada uma lâminas que listam estes itens que estão na sua ASE.  

### <a name="monitoring"></a>Monitorização
Os gráficos permitem-lhe ver uma variedade de métricas de desempenho em cada conjunto de recursos. Para a piscina frontal, você pode monitorizar o CPU médio e a memória. Para piscinas de trabalhadores, pode monitorizar a quantidade que é utilizada e a quantidade disponível.

Vários planos de Serviço de Aplicações podem fazer uso dos trabalhadores numa piscina de trabalhadores. A carga de trabalho não é distribuída da mesma forma que com os servidores frontais, por isso o CPU e o uso da memória não fornecem muito no modo de informação útil. É mais importante rastrear quantos trabalhadores usaste e estás disponível, especialmente se estiveres a gerir este sistema para que outros possam usar.  

Também pode usar todas as métricas que podem ser rastreadas nas tabelas para configurar alertas. A criação de alertas aqui funciona da mesma forma que em qualquer outro lugar do Serviço de Aplicações. Pode definir um alerta a partir da parte UI dos **alertas** ou da perfuração em qualquer UI métrica e selecionar **Add Alert**.

![UI métricas][3]

As métricas que acabaram de ser discutidas são as métricas do App Service Environment. Há também métricas que estão disponíveis ao nível do plano de app service. É aqui que a monitorização da CPU e da memória faz muito sentido.

Numa ASE, todos os planos do App Service são planos dedicados ao Serviço de Aplicações. Isto significa que as únicas aplicações que estão a ser distribuídas nos anfitriões atribuídos a esse plano de Serviço de Aplicações são as aplicações desse plano de Serviço de Aplicações. Para ver detalhes sobre o seu plano de Serviço de Aplicações, traga o seu plano de Serviço de Aplicações a partir de qualquer uma das listas na UI Da ASE ou dos planos do Serviço de **Aplicações Browse** (que lista todas elas).   

### <a name="settings"></a>Definições
Dentro da lâmina ASE, existe uma secção **Definições** que contém várias capacidades importantes:

**Definições** > **Propriedades**: A lâmina **de definições** abre-se automaticamente quando levanta a sua lâmina ASE. No topo está **a Properties**. Há uma série de itens aqui que são redundantes ao que você vê no **Essencial**, mas o que é muito útil é **O Endereço IP Virtual**, bem como **endereços IP outbound**.

![Lâmina de configurações e propriedades][4]

**Definições** > **Endereços IP**: Quando criar uma aplicação IP Secure Sockets Layer (SSL) na sua ASE, precisa de um endereço IP SSL. Para obter um, a sua ASE necessita de endereços IP SSL que detém que possam ser atribuídos. Quando uma ASE é criada, tem um endereço IP SSL para este fim, mas pode adicionar mais. Existe uma taxa para endereços IP SSL adicionais, como mostra o preço do [Serviço de Aplicações][AppServicePricing] (na secção de ligações SSL). O preço adicional é o preço IP SSL.

**Settings** > **Front End Pool** / **Worker Pools**: Each of these resource pool blades offers the ability to see information only on that resource pool, in addition to providing controls to fully scale that resource pool.  

A lâmina base para cada conjunto de recursos fornece um gráfico com métricas para esse conjunto de recursos. Tal como acontece com as tabelas da lâmina ASE, pode entrar na tabela e configurar alertas conforme desejado. A definição de um alerta da lâmina ASE para um conjunto de recursos específicos faz o mesmo que fazê-lo a partir do conjunto de recursos. A partir da lâmina **definições** da piscina de trabalhadores, você tem acesso a todas as Apps ou planos do App Service que estão em execução nesta piscina de trabalhadores.

![Configurações de piscina de trabalhadorUI][5]

### <a name="portal-scale-capabilities"></a>Capacidades de escala de portal
Há três operações de escala:

* Alterar o número de endereços IP na ASE que estão disponíveis para utilização IP SSL.
* Alterar o tamanho do recurso computacional que é usado num conjunto de recursos.
* Alterar o número de recursos computacionais que são utilizados num conjunto de recursos, manualmente ou através de autoscalcificação.

No portal, existem três formas de controlar quantos servidores tem nas suas piscinas de recursos:

* Uma operação à escala da lâmina Principal ASE na parte superior. Pode efazer várias alterações de configuração de escala nas piscinas dianteiras e operárias. São todos aplicados como uma única operação.
* Uma operação manual à escala a partir da lâmina **de escala** de reserva de recursos individuais, que se encontra em **Definições**.
* Autoscalcificação, que configura a partir da lâmina **de escala** de piscina de recursos individuais.

Para utilizar o funcionamento da balança na lâmina ASE, arraste o slider para a quantidade que deseja e guarde. Este UI também suporta a alteração do tamanho.  

![UI escala][6]

Para utilizar as capacidades manuais ou de escala automática num conjunto de recursos específico, vá às **Piscinas** >  / **de Trabalhadores** da**Piscina Frontal,** conforme apropriado. Então abra a piscina que quer mudar. Vá para a escala de **definições** > para**fora** ou **configurações** > **escala para cima**. A lâmina **Scale out** permite controlar a quantidade de instância. **Scale Up** permite controlar o tamanho dos recursos.  

![Definições de escala UI][7]

## <a name="fault-tolerance-considerations"></a>Considerações de tolerância a falhas
Pode configurar um App Service Environment para utilizar até 55 recursos totais de computação. Desses 55 recursos computacionais, apenas 50 podem ser usados para hospedar cargas de trabalho. A razão para isto é dupla. Há um mínimo de 2 recursos de computação frontal.  Isso deixa até 53 para apoiar a atribuição do grupo de trabalhadores. Para fornecer tolerância a falhas, é necessário ter um recurso de cálculo adicional que seja atribuído de acordo com as seguintes regras:

* Cada grupo de trabalhadores necessita de pelo menos 1 recurso de cálculo adicional que não esteja disponível para ser atribuído uma carga de trabalho.
* Quando a quantidade de recursos de computação num conjunto de trabalhadores ultrapassa um determinado valor, então é necessário outro recurso computacional para a tolerância à falha. Este não é o caso na piscina frontal.

Dentro de qualquer grupo de trabalhadores, os requisitos de tolerância à falha são os que, para um determinado valor dos recursos X atribuídos a um grupo de trabalhadores:

* Se X estiver entre 2 e 20, a quantidade de recursos computacionais utilizáveis que pode usar para cargas de trabalho é X-1.
* Se X estiver entre 21 e 40, a quantidade de recursos computacionais utilizáveis que pode usar para cargas de trabalho é X-2.
* Se X estiver entre 41 e 53, a quantidade de recursos computacionais utilizáveis que pode usar para cargas de trabalho é X-3.

A pegada mínima tem 2 servidores frontais e 2 trabalhadores.  Com as declarações acima, então, aqui estão alguns exemplos para esclarecer:  

* Se você tem 30 trabalhadores em uma única piscina, então 28 deles podem ser usados para hospedar cargas de trabalho.
* Se você tem 2 trabalhadores em uma única piscina, então 1 pode ser usado para hospedar cargas de trabalho.
* Se você tem 20 trabalhadores em uma única piscina, então 19 pode ser usado para hospedar cargas de trabalho.  
* Se você tem 21 trabalhadores em uma única piscina, então ainda apenas 19 podem ser usados para hospedar cargas de trabalho.  

O aspeto da tolerância a falhas é importante, mas é preciso ter em mente à medida que se ultrapassa determinados limiares. Se quiser adicionar mais capacidade de 20 instâncias, então vá para 22 ou mais porque 21 não adiciona mais capacidade. O mesmo acontece com 40, onde o próximo número que acrescenta capacidade é de 42.  

## <a name="deleting-an-app-service-environment"></a>Apagar um ambiente de serviço de aplicações
Se pretender eliminar um Ambiente de Serviço de Aplicações, utilize simplesmente a ação **Delete** na parte superior da lâmina ambiente do serviço de aplicações. Quando o fizer, será solicitado que introduza o nome do seu App Service Environment para confirmar que realmente quer fazer isto. Note que ao eliminar um Ambiente de Serviço de Aplicações, também elimina todos os conteúdos que lhe são atribuídos.  

![Eliminar um UI ambiente de serviço de aplicação][9]  

## <a name="getting-started"></a>Introdução
Para começar com o App Service Environments, veja como criar um Ambiente de [Serviço de Aplicações.](app-service-web-how-to-create-an-app-service-environment.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
