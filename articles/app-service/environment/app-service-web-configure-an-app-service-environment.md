---
title: Configurar ASE v1
description: Configuração, gestão e monitorização do Ambiente de Serviço de Aplicações v1. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: ccompy
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 598e43d07c213cfeb25f0ecbc7bd02b6ec54b7ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962592"
---
# <a name="configuring-an-app-service-environment-v1"></a>Configurar um Ambiente de Serviço de Aplicações v1

> [!NOTE]
> Este artigo é sobre o App Service Environment v1.  Existe uma versão mais recente do App Service Environment que é mais fácil de usar e funciona em infraestruturas mais poderosas. Para saber mais sobre a nova versão comece com a [Introdução ao Ambiente de Serviço de Aplicações.](intro.md)
> 

## <a name="overview"></a>Descrição geral
A um nível elevado, um Azure App Service Environment é composto por vários componentes principais:

* Computação de recursos que estão em execução no serviço de acolhimento do App Service Environment
* Armazenamento
* Uma base de dados
* Uma rede virtual clássica (V1) ou gestora de recursos (V2) Azure Virtual Network (VNet) 
* Uma sub-rede com o Serviço de Aplicações Ambiente hospedado no mesmo

### <a name="compute-resources"></a>Recursos de cálculo
Você usa os recursos compute para os seus quatro conjuntos de recursos.  Cada Ambiente de Serviço de Aplicações (ASE) tem um conjunto de extremidades dianteiras e três possíveis piscinas de trabalhadores. Não precisas de usar as três piscinas de trabalhadores, se quiseres, podes usar uma ou duas.

Os anfitriões nas piscinas de recursos (front ends e trabalhadores) não são diretamente acessíveis aos inquilinos. Não é possível utilizar o Protocolo de Ambiente de Trabalho Remoto (PDR) para se ligar a eles, alterar o seu fornecimento ou agir como administrador sobre eles.

Pode definir a quantidade e o tamanho da piscina de recursos. Num ASE, você tem quatro opções de tamanho, que são rotulados P1 a P4. Para obter detalhes sobre esses tamanhos e os seus preços, consulte [os preços do Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/)
A alteração da quantidade ou tamanho é chamada de operação de escala.  Só uma operação à escala pode estar em andamento de cada vez.

**Extremidades dianteiras**: As extremidades dianteiras são os pontos finais HTTP/HTTPS para as suas aplicações que são mantidas no seu ASE. Não se faz cargas de trabalho nas extremidades da frente.

* Um ASE começa com dois P2s, o que é suficiente para cargas de trabalho dev/teste e cargas de trabalho de baixo nível de produção. Recomendamos vivamente p3s para cargas de trabalho de produção moderadas a pesadas.
* Para cargas de trabalho de produção moderada a pesada, recomendamos que tenha pelo menos quatro P3s para garantir que existem extremidades dianteiras suficientes quando ocorrer uma manutenção programada. As atividades de manutenção programadas derrubarão uma frente de cada vez. Isto reduz a capacidade frontal disponível durante as atividades de manutenção.
* As extremidades dianteiras podem demorar até uma hora até a provisão. 
* Para uma melhor afinação de escala, deverá monitorizar a percentagem de CPU, percentagem de memória e métricas de Pedidos Ativos para o pool frontal. Se as percentagens de CPU ou Memória estiverem acima de 70% ao executar P3s, adicione mais extremidades dianteiras. Se o valor ative Requests for de 15.000 a 20.000 pedidos por frente, também deverá adicionar mais extremidades frontais. O objetivo geral é manter percentagens de CPU e Memória abaixo dos 70%, e Pedidos Ativos em média para menos de 15.000 pedidos por frente quando estiver a executar P3s.  

**Trabalhadores**: Os trabalhadores são onde as suas aplicações realmente funcionam. Quando você escala os seus planos de Serviço de Aplicações, que usa os trabalhadores na piscina de trabalhadores associados.

* Não se pode adicionar trabalhadores instantaneamente. Podem demorar até uma hora.
* A escala do tamanho de um recurso de computação para qualquer piscina levará < 1 hora por domínio de atualização. Existem 20 domínios de atualização num ASE. Se escalonou o tamanho do cálculo de uma piscina de trabalhadores com 10 instâncias, pode levar até 10 horas para ser concluído.
* Se alterar o tamanho dos recursos computacional que são utilizados numa piscina de trabalhadores, irá causar arranques frios das aplicações que estão a ser executadas naquela piscina de trabalhadores.

A forma mais rápida de alterar o tamanho do recurso computacional de uma piscina de trabalhadores que não está a executar nenhuma aplicação é:

* Reduza a quantidade de trabalhadores para 2.  O tamanho mínimo de baixo no portal é 2. Levará alguns minutos para resolver os seus casos. 
* Selecione o novo tamanho do cálculo e o número de casos. A partir daqui, levará até 2 horas para ser completado.

Se as suas aplicações requerem um maior tamanho de recursos compute, não pode tirar partido da orientação anterior. Em vez de alterar o tamanho da piscina de trabalhadores que está hospedando essas aplicações, você pode povoar outra piscina de trabalhadores com trabalhadores do tamanho desejado e mover suas apps para essa piscina.

* Crie os casos adicionais do tamanho do cálculo necessário em outra piscina de trabalhadores. Isto levará até uma hora para ser concluído.
* Reatribua os seus planos de Serviço de Aplicações que estão a hospedar as aplicações que precisam de um tamanho maior para o recém-configurado pool de trabalhadores. Esta é uma operação rápida que deve levar menos de um minuto para ser concluída.  
* Reduza a primeira piscina de trabalhadores se já não precisar desses casos não reutilizados. Esta operação leva alguns minutos para ser concluída.

**Autoscaling**: Uma das ferramentas que o podem ajudar a gerir o seu consumo de recursos computacionais é a autoscalagem. Você pode usar autoscaling para piscinas frontais ou de trabalhadores. Você pode fazer coisas como aumentar suas instâncias de qualquer tipo de piscina de manhã e reduzi-lo à noite. Ou talvez se possa acrescentar casos em que o número de trabalhadores disponíveis numa piscina de trabalhadores cai abaixo de um determinado limiar.

Se pretender definir regras de autoescalação em torno das métricas do conjunto de recursos computacional, então tenha em mente o tempo que o provisionamento requer. Para obter mais detalhes sobre a autoescalação de Ambientes de Serviço de Aplicações, consulte [Como configurar a autoescalação num Ambiente de Serviço de Aplicações.][ASEAutoscale]

### <a name="storage"></a>Armazenamento
Cada ASE está configurado com 500 GB de armazenamento. Este espaço é utilizado em todas as aplicações da ASE. Este espaço de armazenamento é uma parte do ASE e atualmente não pode ser trocado para usar o seu espaço de armazenamento. Se estiver a fazer ajustes no seu encaminhamento ou segurança de rede virtual, tem de continuar a permitir o acesso ao Azure Storage -- ou o ASE não pode funcionar.

### <a name="database"></a>Base de Dados
A base de dados detém a informação que define o ambiente, bem como os detalhes sobre as aplicações que estão a ser executando dentro do mesmo. Isto também faz parte da assinatura do Azure. Não é algo que se tenha uma capacidade direta de manipular. Se estiver a fazer ajustes no seu encaminhamento ou segurança de rede virtual, tem de continuar a permitir o acesso ao SQL Azure-- ou o ASE não pode funcionar.

### <a name="network"></a>Rede
O VNet que é usado com o seu ASE pode ser aquele que fez quando criou o ASE ou um que fez antes do tempo. Quando cria a sub-rede durante a criação da ASE, força o ASE a estar no mesmo grupo de recursos que a rede virtual. Se precisa do grupo de recursos utilizado pelo seu ASE para ser diferente do do seu VNet, então precisa criar o seu ASE utilizando um modelo de gestor de recursos.

Existem algumas restrições na rede virtual que é usada para um ASE:

* A rede virtual deve ser uma rede virtual regional.
* Tem de haver uma sub-rede com 8 ou mais endereços onde o ASE é implantado.
* Depois de uma sub-rede ser utilizada para hospedar um ASE, o intervalo de endereços da sub-rede não pode ser alterado. Por esta razão, recomendamos que a sub-rede contenha pelo menos 64 endereços para acomodar qualquer crescimento ase futuro.
* Não pode haver mais nada na sub-rede a não ser o ASE.

Ao contrário do serviço hospedado que contém o ASE, a [rede virtual][virtualnetwork] e a sub-rede estão sob o controlo do utilizador.  Pode administrar a sua rede virtual através da UI de Rede Virtual ou powerShell.  Um ASE pode ser implantado num VNet Clássico ou Gestor de Recursos.  As experiências do portal e da API são ligeiramente diferentes entre os VNets Classic e Resource Manager, mas a experiência ASE é a mesma.

O VNet que é utilizado para hospedar um ASE pode usar endereços IP RFC1918 privados ou pode usar endereços IP públicos.  Se desejar utilizar uma gama DEP que não esteja coberta pelo RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) então tem de criar o seu VNet e sub-rede para ser utilizado pelo seu ASE antes da criação da ASE.

Uma vez que esta capacidade coloca o Azure App Service na sua rede virtual, significa que as suas aplicações que estão hospedadas no seu ASE podem agora aceder diretamente aos recursos que são disponibilizados através do ExpressRoute ou de redes privadas virtuais (VPNs) site-to-site diretamente. As aplicações que estão dentro do seu Ambiente de Serviço de Aplicações não requerem funcionalidades adicionais de rede para aceder aos recursos disponíveis para a rede virtual que está hospedando o seu Ambiente de Serviço de Aplicações. Isto significa que não precisa de utilizar a Integração VNET ou conexões híbridas para obter recursos dentro ou ligados à sua rede virtual. Ainda é possível utilizar ambas as funcionalidades para aceder a recursos em redes que não estejam ligadas à sua rede virtual.

Por exemplo, pode utilizar a Integração VNET para se integrar a uma rede virtual que está na sua subscrição mas não está ligada à rede virtual em que o seu ASE se encontra. Também pode utilizar Ligações Híbridas para aceder a recursos que se encontram noutras redes, tal como normalmente pode.  

Se tiver a sua rede virtual configurada com uma VPN ExpressRoute, deve estar ciente de algumas das necessidades de encaminhamento que um ASE tem. Existem algumas configurações de rota definida pelo utilizador (UDR) que são incompatíveis com um ASE. Para obter mais detalhes sobre a execução de um ASE numa rede virtual com o ExpressRoute, consulte [executar um Ambiente de Serviço de Aplicações numa rede virtual com o ExpressRoute.][ExpressRoute]

#### <a name="securing-inbound-traffic"></a>Assegurar o tráfego de entrada
Existem dois métodos primários para controlar o tráfego de entrada para o seu ASE.  Pode utilizar os Grupos de Segurança da Rede (NSGs) para controlar quais os endereços IP que podem aceder ao seu ASE, conforme descrito aqui [Como controlar o tráfego de entrada num Ambiente de Serviço de Aplicações](app-service-app-service-environment-control-inbound-traffic.md) e também pode configurar o seu ASE com um Balançador de Carga Interna (ILB).  Estas funcionalidades também podem ser usadas em conjunto se pretender restringir o acesso usando NSGs ao seu ILB ASE.

Quando criar um ASE, criará um VIP no seu VNet.  Existem dois tipos VIP, externos e internos.  Quando criar um ASE com um VIP externo, as suas aplicações no seu ASE estarão acessíveis através de um endereço IP via internet. Quando selecionar Internamente, o seu ASE será configurado com um ILB e não estará diretamente acessível à Internet.  Um ILB ASE ainda requer um VIP externo, mas é utilizado apenas para o acesso à gestão e manutenção do Azure.  

Durante a criação do ILB ASE fornece-se o subdomínio utilizado pelo ILB ASE e terá de gerir o seu próprio DNS para o subdomínio que especifica.  Como definiu o nome do subdomínio, também precisa de gerir o certificado utilizado para o acesso a HTTPS.  Após a criação da ASE, é solicitado que forneça o certificado.  Para saber mais sobre a criação e utilização de um ILB ASE leia [utilizando um balançador de carga interna com um Ambiente de Serviço de Aplicações.][ILBASE] 

## <a name="portal"></a>Portal
Pode gerir e monitorizar o seu Ambiente de Serviço de Aplicações utilizando o UI no portal Azure. Se tiver um ASE, é provável que veja o símbolo do Serviço de Aplicações na sua barra lateral. Este símbolo é utilizado para representar ambientes de serviço de aplicações no portal Azure:

![Símbolo de ambiente de serviço de aplicativo][1]

Para abrir o UI que lista todos os seus Ambientes de Serviço de Aplicações, pode utilizar o ícone ou selecionar o chevron (símbolo ">") na parte inferior da barra lateral para selecionar Ambientes de Serviço de Aplicações. Ao selecionar um dos ASEs listados, abre o UI que é usado para monitorá-lo e geri-lo.

![UI para monitorização e gestão do seu Ambiente de Serviço de Aplicações][2]

Esta primeira lâmina mostra algumas propriedades do seu ASE, juntamente com um gráfico métrico por conjunto de recursos. Algumas das propriedades que são mostradas no bloco **Essentials** são também hiperligações que irão abrir a lâmina que está associada a ela. Por exemplo, pode selecionar o nome **de Rede Virtual** para abrir o UI associado à rede virtual em que o seu ASE está a funcionar. **Os planos** e apps do Serviço **de Aplicações** abrem cada uma as lâminas que listam estes itens que estão no seu ASE.  

### <a name="monitoring"></a>Monitorização
Os gráficos permitem-lhe ver uma variedade de métricas de desempenho em cada conjunto de recursos. Para a piscina frontal, pode monitorizar o CPU médio e a memória. Para piscinas de trabalhadores, pode monitorizar a quantidade que é utilizada e a quantidade disponível.

Vários planos de Serviço de Aplicações podem fazer uso dos trabalhadores numa piscina de trabalhadores. A carga de trabalho não é distribuída da mesma forma que com os servidores frontais, pelo que o uso do CPU e da memória não fornecem muito na forma de informação útil. É mais importante saber quantos trabalhadores usou e está disponível- especialmente se estiver a gerir este sistema para outros utilizarem.  

Também pode usar todas as métricas que podem ser rastreadas nas tabelas para configurar alertas. A configuração de alertas aqui funciona da mesma forma que em qualquer outro lugar no Serviço de Aplicações. Pode definir um alerta a partir da parte de **UI alerta** ou de perfurar em quaisquer métricas UI e selecionar **Add Alert**.

![UI métricas][3]

As métricas que acabam de ser discutidas são as métricas do App Service Environment. Há também métricas que estão disponíveis ao nível do plano do Serviço de Aplicações. É aqui que monitorizar a CPU e a memória faz muito sentido.

Numa ASE, todos os planos do Serviço de Aplicações são planos dedicados do Serviço de Aplicações. Isto significa que as únicas aplicações que estão a ser executadas nos anfitriões alocados a esse plano de Serviço de Aplicações são as aplicações nesse plano de Serviço de Aplicações. Para ver detalhes sobre o seu plano de Serviço de Aplicações, traga o seu plano de Serviço de Aplicações a partir de qualquer uma das listas no UI ASE ou nos planos do Serviço de **Aplicações Browse** (que lista todos eles).   

### <a name="settings"></a>Definições
Dentro da lâmina ASE, existe uma secção **de Definições** que contém várias capacidades importantes:

**Configurações**  >  **Propriedades**: A lâmina **de definições** abre-se automaticamente quando traz a sua lâmina ASE. No topo está a **Properties.** Existem vários itens aqui que são redundantes para o que você vê no **Essencial**, mas o que é muito útil é **o Endereço IP Virtual,** bem como **endereços IP outbound**.

![Lâmina e propriedades de configurações][4]

**Configurações**  >  **Endereços IP**: Quando criar uma aplicação IP Secure Sockets Layer (SSL) no seu ASE, necessita de um endereço IP SSL. Para obter um, o seu ASE necessita de endereços IP SSL que possua que possam ser atribuídos. Quando um ASE é criado, tem um endereço IP SSL para este fim, mas pode adicionar mais. Existe uma taxa para endereços IP SSL adicionais, como mostrado nos [preços do Serviço de Aplicações][AppServicePricing] (na secção de ligações SSL). O preço adicional é o preço IP SSL.

**Configurações**  >  **Piscina**  /  frontal **Piscinas de Trabalho**: Cada uma destas lâminas de piscina de recursos oferece a capacidade de ver informações apenas nesse conjunto de recursos, além de fornecer controlos para escalar totalmente esse conjunto de recursos.  

A lâmina base de cada conjunto de recursos fornece um gráfico com métricas para esse conjunto de recursos. Tal como acontece com as tabelas da lâmina ASE, pode entrar na tabela e configurar alertas conforme desejado. Definir um alerta da lâmina ASE para um conjunto de recursos específicos faz o mesmo que fazê-lo a partir do conjunto de recursos. A partir da lâmina **de configuração** da piscina de trabalhadores, você tem acesso a todos os planos de Apps ou App Service que estão executando nesta piscina de trabalhadores.

![Configurações de piscina de trabalhadores UI][5]

### <a name="portal-scale-capabilities"></a>Capacidades de escala do portal
Há operações de três escalas:

* Alterar o número de endereços IP no ASE que estão disponíveis para utilização IP SSL.
* Alterar o tamanho do recurso computacional que é utilizado num conjunto de recursos.
* Alterar o número de recursos de computação que são utilizados num conjunto de recursos, manualmente ou através de autoscalagem.

No portal, existem três formas de controlar quantos servidores tem nas suas reservas de recursos:

* Uma operação de escala da lâmina ase principal na parte superior. Pode escamar alterações de configuração em várias escalas nas piscinas frontais e de trabalhadores. São todos aplicados como uma única operação.
* Um funcionamento em escala manual a partir da lâmina de **escala** de conjunto de recursos individual, que está em **Definições**.
* Autoscalagem, que configura a partir da **lâmina** de escala de conjunto de recursos individuais.

Para utilizar a operação de balança na lâmina ASE, arraste o slider para a quantidade desejada e guarde. Esta UI também suporta a alteração do tamanho.  

![UI escala][6]

Para utilizar as capacidades manuais ou de autoescalação num conjunto de recursos **específicos,** vá às Piscinas  >  **frontais de**trabalho de pool,  /  **Worker Pools** conforme apropriado. Então abra a piscina que quer mudar. Ir para **a escala de definições**para  >  **fora** ou **para as definições**  >  **escalam -** A **lâmina Scale out** permite controlar a quantidade de casos. **O Scale Up** permite controlar o tamanho do recurso.  

![Definições de escala UI][7]

## <a name="fault-tolerance-considerations"></a>Considerações de tolerância a falhas
Pode configurar um Ambiente de Serviço de Aplicações para utilizar até 55 recursos de computação total. Desses 55 recursos de computação, apenas 50 podem ser usados para acolher cargas de trabalho. A razão para isto é dupla. Há um mínimo de 2 recursos computacional front-end.  Isso deixa até 53 para apoiar a atribuição de piscinas de trabalhadores. Para proporcionar tolerância a falhas, é necessário dispor de um recurso adicional de computação que seja atribuído de acordo com as seguintes regras:

* Cada piscina de trabalhadores necessita de pelo menos 1 recurso adicional de computação que não esteja disponível para ser atribuído uma carga de trabalho.
* Quando a quantidade de recursos de cálculo num pool de trabalhadores ultrapassa um determinado valor, então é necessário outro recurso de cálculo para a tolerância à falha. Não é o caso na piscina frontal.

Dentro de qualquer agrupamento de trabalhadores, os requisitos de tolerância a falhas são os exigidos por um dado valor dos recursos X atribuídos a um pool de trabalhadores:

* Se X estiver entre 2 e 20, a quantidade de recursos computáveis utilizáveis que pode usar para cargas de trabalho é X-1.
* Se X tem entre 21 e 40 anos, a quantidade de recursos computáveis utilizáveis que pode usar para cargas de trabalho é X-2.
* Se X estiver entre 41 e 53, a quantidade de recursos computáveis utilizáveis que pode usar para cargas de trabalho é X-3.

A pegada mínima tem 2 servidores frontais e 2 trabalhadores.  Com as declarações acima, então, aqui estão alguns exemplos para clarificar:  

* Se você tem 30 trabalhadores em uma única piscina, então 28 deles podem ser usados para hospedar cargas de trabalho.
* Se você tem 2 trabalhadores em uma única piscina, então 1 pode ser usado para hospedar cargas de trabalho.
* Se você tem 20 trabalhadores em uma única piscina, então 19 podem ser usados para hospedar cargas de trabalho.  
* Se você tem 21 trabalhadores em uma única piscina, então ainda apenas 19 podem ser usados para hospedar cargas de trabalho.  

O aspeto da tolerância a falhas é importante, mas tens de ter isso em mente à medida que escalas acima de determinados limiares. Se quiser adicionar mais capacidade de 20 instâncias, então vá para 22 ou mais porque 21 não adiciona mais capacidade. O mesmo acontece ao ultrapassar os 40, onde o próximo número que aumenta a capacidade é de 42.  

## <a name="deleting-an-app-service-environment"></a>Apagar um Ambiente de Serviço de Aplicações
Se pretender eliminar um Ambiente de Serviço de Aplicações, utilize simplesmente a ação **Eliminar** na parte superior da lâmina Do Ambiente do Serviço de Aplicações. Quando o fizer, será solicitado que introduza o nome do seu Ambiente de Serviço de Aplicações para confirmar que realmente quer fazer isto. Note que quando elimina um Ambiente de Serviço de Aplicações, também elimina todo o conteúdo dentro dele.  

![Eliminar um UI de Ambiente de Serviço de Aplicações][9]  

## <a name="getting-started"></a>Introdução
Para começar com os Ambientes de Serviço de Aplicações, consulte [como criar um Ambiente de Serviço de Aplicações.](app-service-web-how-to-create-an-app-service-environment.md)

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
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md