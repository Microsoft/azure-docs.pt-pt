---
title: Como configurar um serviço de aplicações v1 do ambiente - Azure
description: Configuração, gestão e monitorização do ambiente de serviço de aplicações v1
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5c0b4117f6e7b48dce1746ad6eb3dbe29c0d16af
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130624"
---
# <a name="configuring-an-app-service-environment-v1"></a>Configuração de uma aplicação v1 do ambiente de serviço

> [!NOTE]
> Este artigo é sobre o ambiente de serviço de aplicações v1.  Existe uma versão mais recente do ambiente de serviço de aplicação que é mais fácil de usar e é executado numa infraestrutura mais poderosa. Para saber mais sobre o novo início de versão com o [introdução ao ambiente de serviço de aplicações](intro.md).
> 

## <a name="overview"></a>Descrição geral
Num alto nível, um ambiente de serviço de aplicações do Azure é composta por vários componentes principais:

* Recursos de computação que estão a executar o serviço de ambiente de serviço de aplicações alojadas
* Armazenamento
* Uma base de dados
* Uma Classic(V1) ou recurso Manager(V2) Azure rede Virtual (VNet) 
* Uma sub-rede com o serviço de ambiente de serviço de aplicações alojadas em execução no mesmo

### <a name="compute-resources"></a>Calcular recursos
Utilize os recursos de computação para os seus agrupamentos de recursos de quatro.  Cada ambiente de serviço de aplicações (ASE) tem um conjunto de front-ends e três conjuntos de trabalho possíveis. Não precisa de utilizar todos os conjuntos de trabalho de três, se desejar, pode simplesmente usar um ou dois.

Os anfitriões nos agrupamentos de recursos (front-ends e trabalhos) não estão diretamente acessíveis aos inquilinos. Não é possível utilizar o protocolo RDP (Remote Desktop) para se ligar aos mesmos, altere o seu aprovisionamento ou agir como um administrador nos mesmos.

Pode definir a quantidade de agrupamento de recursos e o tamanho. Num ASE, terá quatro opções de tamanho, que estão identificados como P1 a P4. Para obter detalhes sobre estes tamanhos e seus preços, consulte [preços do serviço de aplicações](https://azure.microsoft.com/pricing/details/app-service/).
Alterar a quantidade ou o tamanho é chamado de uma operação de dimensionamento.  Operação de dimensionamento apenas uma pode estar em curso, ao mesmo tempo.

**Front-ends**: Os front-ends são os pontos finais de HTTP/HTTPS para as suas aplicações que são mantidas no seu ASE. Não executar cargas de trabalho no front-ends.

* Um ASE é iniciado com dois P2s, que é suficiente para cargas de trabalho de programador/teste e cargas de trabalho de produção de nível baixo. É altamente recomendável P3s para moderada para cargas de trabalho pesadas de produção.
* Para moderada para cargas de trabalho pesadas de produção, recomendamos que tenha, pelo menos, quatro P3s para garantir que existem suficientes front-ends em execução quando ocorre a manutenção agendada. Atividades de manutenção agendada serão prejudicar um front-end ao mesmo tempo. Isso reduz global capacidade disponível de front-end durante as atividades de manutenção.
* Front-ends podem demorar até uma hora para aprovisionar. 
* Para Ajustar escala ainda mais, deve monitorar a percentagem de CPU, percentagem de memória e métricas de pedidos ativos para o conjunto de front-end. Se os percentuais de CPU ou memória são acima 70 por cento durante a execução P3s, adicione mais front-ends. Se o valor de pedidos ativos calcula a média 15.000 para 20 000 pedidos por front-end, deverá ainda adicionar mais front-ends. O objetivo geral é manter percentuais de CPU e memória abaixo 70% e pedidos ativos uma média de para abaixo de 15 000 pedidos por front end quando estiver a executar o P3s.  

**Os operadores**: Os trabalhadores estão onde, na verdade, executam as suas aplicações. Ao aumentar verticalmente os planos de serviço de aplicações, que utiliza funções de trabalho no conjunto de trabalho associados.

* Instantaneamente não é possível adicionar funções de trabalho. Poderá demorar até uma hora para aprovisionar.
* O tamanho de um recurso de computação para qualquer conjunto de dimensionamento irão demorar < 1 hora por domínio de atualização. Há 20 domínios de atualização num ASE. Se aumentado o tamanho de computação de um conjunto de trabalho com 10 instâncias, pode demorar até 10 horas a concluir.
* Se alterar o tamanho dos recursos de computação que são usados num conjunto de trabalho, fará com que arranques a frio das aplicações que estão em execução nesse agrupamento de trabalho.

É a forma mais rápida para alterar o tamanho do recurso de computação de um conjunto de trabalho que não está a executar todas as aplicações:

* Reduza a quantidade de trabalhos para 2.  O dimensionamento mínimo para baixo de tamanho no portal do é 2. Irá demorar alguns minutos para desalocar as instâncias. 
* Selecione o novo tamanho de computação e o número de instâncias. A partir daqui, demorará até duas horas a concluir.

Se as suas aplicações requerem um tamanho de recursos de computação maior, não é possível tirar partido das orientações anteriores. Em vez de alterar o tamanho do conjunto de trabalho que está a alojar as aplicações, pode preencher o outro conjunto de trabalho com funções de trabalho do tamanho desejado e mover as suas aplicações para esse agrupamento.

* Crie as instâncias adicionais do tamanho de computação necessários no outro conjunto de trabalho. Isto irá demorar até uma hora para concluir.
* Reatribua seus planos de serviço de aplicações que estão a alojar as aplicações que precisam de um tamanho maior para o conjunto de trabalho recém configurada. Esta é uma operação rápida que deve demorar menos de um minuto para concluir.  
* Reduza verticalmente o primeiro conjunto de trabalho se não precisa mais essas instâncias não utilizadas. Esta operação demora alguns minutos a concluir.

**Dimensionamento automático**: Uma das ferramentas que podem ajudar a gerir o seu consumo de recursos de computação é o dimensionamento automático. Pode utilizar o dimensionamento automático para front-end ou conjuntos de trabalho. Pode fazer coisas como aumento as instâncias de qualquer tipo de conjunto de manhã e reduzi-lo durante a noite. Ou talvez pode adicionar instâncias quando o número de trabalhos que estão disponíveis num conjunto de trabalhos cai abaixo de um determinado limiar.

Se pretender definir regras de dimensionamento automático em torno de métricas de conjunto de recursos de computação, em seguida, tenha em mente o tempo que necessita de aprovisionamento. Para obter mais detalhes sobre o dimensionamento automático de ambientes de serviço de aplicações, consulte [como configurar o dimensionamento automático num ambiente de serviço de aplicações][ASEAutoscale].

### <a name="storage"></a>Armazenamento
Cada ASE está configurado com 500 GB de armazenamento. Este espaço é utilizado em todas as aplicações no ASE. Este espaço de armazenamento é uma parte do ASE e atualmente não pode ser mudado para utilizar o seu espaço de armazenamento. Se estiver a fazer ajustes para o encaminhamento da rede virtual ou a segurança, precisa para ainda permitir acesso ao armazenamento do Azure ou o ASE não funcionará.

### <a name="database"></a>Base de Dados
A base de dados mantém as informações que definem o ambiente, bem como os detalhes sobre as aplicações que estão em execução dentro da mesma. Isso também é uma parte da subscrição do Azure-mantidos. Não é algo que tem uma capacidade direta para manipular. Se estiver a fazer ajustes para o encaminhamento da rede virtual ou a segurança, precisa para ainda permitir acesso ao SQL Azure – ou o ASE não funcionará.

### <a name="network"></a>Rede
A VNet a que é utilizada com o seu ASE pode ser que fez quando criou o ASE ou um que efetuou antes do tempo. Ao criar a sub-rede durante a criação do ASE, ela força o ASE para estar no mesmo grupo de recursos que a rede virtual. Se precisar do grupo de recursos utilizado pelo seu ASE para ser diferente da sua VNet, em seguida, terá de criar o seu ASE utilizando um modelo do resource manager.

Existem algumas restrições sobre a rede virtual que é utilizado para um ASE:

* A rede virtual tem de ser uma rede virtual regional.
* Deve haver uma sub-rede com 8 ou mais endereços em que o ASE é implementado.
* Depois de uma sub-rede é utilizada para alojar um ASE, não é possível alterar o intervalo de endereços da sub-rede. Por esse motivo, recomendamos que a sub-rede contém, pelo menos, 64 endereços para contemplar qualquer crescimento futuro do ASE.
* Pode haver nada mais na sub-rede, mas o ASE.

Ao contrário do serviço alojado que contém o ASE, o [rede virtual] [ virtualnetwork] e sub-rede estão sob o controle de usuário.  Pode administrar a sua rede virtual através da IU de rede Virtual ou do PowerShell.  Um ASE pode ser implementado num clássico ou VNet do Resource Manager.  O portal e experiências de API são um pouco diferentes entre VNets do Resource Manager e o clássico, mas a experiência de ASE é o mesmo.

A VNet a que é utilizada para alojar um ASE pode utilizar qualquer um dos endereços RFC1918 IP privados ou pode utilizar endereços IP públicos.  Se pretender utilizar um intervalo IP que não é abrangido por RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), em seguida, precisa para criar a VNet e sub-rede a ser utilizado pelo seu ASE antes da criação do ASE.

Uma vez que esta capacidade coloca o serviço de aplicações do Azure em sua rede virtual, significa que as suas aplicações alojadas no ASE podem agora aceder a recursos que são disponibilizados através do ExpressRoute ou redes privadas virtuais (VPNs) site a site diretamente. As aplicações que estejam dentro do ambiente de serviço de aplicações não precisam de funcionalidades de rede adicionais para aceder aos recursos disponíveis para a rede virtual que está a alojar o ambiente de serviço de aplicações. Isso significa que não tem de utilizar a integração de VNET ou ligações híbridas para obter a recursos em ou ligados à sua rede virtual. Pode continuar a utilizar ambos esses recursos para aceder a recursos em redes que não estão ligados à sua rede virtual.

Por exemplo, pode utilizar a integração de VNET para integrar com uma rede virtual que está na sua subscrição, mas não está ligada à rede virtual que está a seu ASE. Ainda também pode utilizar as ligações híbridas para aceder aos recursos que estão em outras redes, como pode fazer normalmente.  

Se tiver sua rede virtual configurado com uma VPN do ExpressRoute, deve estar ciente de alguns das necessidades de encaminhamento que tenha um ASE. Existem algumas configurações de rota definidas pelo utilizador (UDR) que são incompatíveis com um ASE. Para obter mais detalhes sobre a execução de um ASE numa rede virtual com o ExpressRoute, veja [com um ambiente de serviço de aplicações numa rede virtual com o ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Proteger o tráfego de entrada
Existem dois métodos principais para controlar o tráfego de entrada para o seu ASE.  Pode usar Groups(NSGs) de segurança de rede para controlar quais IP endereços podem acessar seu ASE, conforme descrito aqui [como controlar o tráfego de entrada num ambiente de serviço de aplicações](app-service-app-service-environment-control-inbound-traffic.md) e também pode configurar o ASE com um balanceador de carga interno (ILB).  Esses recursos também podem ser usados juntos para restringir o acesso com NSGs para o ASE de ILB.

Ao criar um ASE, ele criará um VIP na sua VNet.  Existem dois tipos de VIP, externos e internos.  Quando criar um ASE com um VIP externo, em seguida, as suas aplicações no ASE estará acessíveis por meio de um endereço IP encaminhável da internet. Quando seleciona interno seu ASE será configurado com um ILB e não serão diretamente acessível pela internet.  Um ASE de ILB ainda requer um VIP externo, mas só é utilizado para acesso de gestão e manutenção do Azure.  

Durante a criação do ASE de ILB fornecem o subdomínio utilizado pelo ASE de ILB e terá de gerir o seu próprio DNS para o subdomínio que especificar.  Uma vez que definir o nome de subdomínio que terá também de gerir o certificado utilizado para acesso HTTPS.  Após a criação do ASE lhe for pedido para fornecer o certificado.  Para saber mais sobre como criar e utilizar um ASE de ILB leia [com um balanceador de carga interno com um ambiente de serviço de aplicações][ILBASE]. 

## <a name="portal"></a>Portal
Pode gerir e monitorizar o ambiente de serviço de aplicações através da IU no portal do Azure. Se tiver um ASE, em seguida, é provável que vir o símbolo de serviço de aplicações na sua barra lateral. Esse símbolo é usado para representar os ambientes do serviço de aplicações no portal do Azure:

![Símbolo de ambiente de serviço de aplicações][1]

Para abrir a interface do Usuário que lista todos os seus ambientes de serviço de aplicações, pode utilizar o ícone ou selecione a divisa (">" símbolo) na parte inferior da barra lateral para selecionar os ambientes de serviço de aplicações. Ao selecionar uma das ASEs listados, abra a interface do Usuário que é utilizado para monitorizar e geri-la.

![Interface do Usuário para monitorizar e gerir o ambiente de serviço de aplicações][2]

Este primeiro painel mostra algumas propriedades do ASE, juntamente com um gráfico de métricas por agrupamento de recursos. Algumas das propriedades que são mostradas na **Essentials** bloco também são hiperligações que irão abrir o painel que está associado ele. Por exemplo, pode selecionar o **rede Virtual** nome para abrir a interface do usuário associadas à rede virtual que está a executar no seu ASE. **Planos do App Service** e **aplicações** cada abra painéis que listam esses itens que estão no seu ASE.  

### <a name="monitoring"></a>Monitorização
Os gráficos permitem-lhe ver várias métricas de desempenho em cada agrupamento de recursos. Para o conjunto de front-end, pode monitorizar a média da CPU e memória. Para os conjuntos de trabalho, pode monitorar a quantidade utilizada e a quantidade disponível.

Serviço de aplicações de vários planos podem tornar a utilização dos operadores num conjunto de trabalho. A carga de trabalho não é distribuída da mesma maneira como com os servidores front-end, por isso, a utilização de CPU e memória não oferecer muito a respeito informações úteis. É mais importante controlar quantas funções de trabalho que utilizou e estão disponíveis, especialmente se estiver a gerir este sistema para utilização de terceiros.  

Também pode utilizar todas as métricas que podem ser controladas nos gráficos para configurar alertas. Configuração de alertas aqui funciona tal como em outro lugar no serviço de aplicações. Pode definir um alerta a partir do **alertas** interface do Usuário parte ou a partir de desagregação em qualquer métrica da interface do Usuário e selecionando **Adicionar alerta**.

![Métricas da interface do Usuário][3]

As métricas que apenas foram discutidas são as métricas de ambiente de serviço de aplicações. Também existem métricas que estão disponíveis ao nível do plano do serviço de aplicações. Isso é onde a monitorização da CPU e memória faz muito sentido.

Num ASE, todos os planos de serviço de aplicações são planos dedicados do serviço de aplicações. Isso significa que as únicas aplicações que são executados nos anfitriões alocados para que o plano do serviço de aplicações são as aplicações nesse plano do serviço de aplicações. Para ver detalhes sobre o seu plano do serviço de aplicações, traga verticalmente o seu plano do serviço de aplicações a partir de qualquer uma das listas na IU do ASE ou a partir **planos do serviço de aplicações de procurar** (que apresenta uma lista de todos eles).   

### <a name="settings"></a>Definições
No painel do ASE, há uma **definições** seção que contém diversos recursos importantes:

**As definições** > **propriedades**: O **definições** painel abre automaticamente quando abrir o painel do ASE. Na parte superior é **propriedades**. Existem alguns itens aqui que redundantes para que vê na **Essentials**, mas o que é muito útil é **endereço IP Virtual**, bem como **endereços IP de saída**.

![Painel de definições e propriedades][4]

**As definições** > **endereços IP**: Quando cria uma aplicação de IP Secure Sockets Layer (SSL) no seu ASE, precisa de um endereço IP SSL. Para obter uma, o ASE tem endereços de IP SSL que ele possui que podem ser alocados. Quando um ASE é criado, ele tem um endereço IP SSL para este fim, mas pode adicionar mais. Existe uma cobrança para endereços de SSL de IP adicionais, conforme mostrado na [preços do serviço de aplicações] [ AppServicePricing] (na secção de ligações SSL). O preço adicional é o preço de SSL de IP.

**As definições** > **conjunto final de front-** / **conjuntos de trabalho**: Cada um desses painéis de conjunto de recursos oferece a capacidade para ver informações apenas sobre esse pool de recursos, além de fornecermos o controles para dimensionar completamente esse agrupamento de recursos.  

O painel de base para cada agrupamento de recursos fornece um gráfico com a métrica para esse pool de recursos. Assim como com os gráficos do painel do ASE, pode ir para o gráfico e configurar alertas conforme pretendido. Definir um alerta a partir do painel de ASE para um agrupamento de recursos específico faz a mesma coisa como fazê-lo do agrupamento de recursos. Do conjunto de trabalho **definições** painel, tem acesso a todas as aplicações ou planos de serviço de aplicações que estão a executar este conjunto de trabalho.

![Definições do conjunto de trabalho da interface do Usuário][5]

### <a name="portal-scale-capabilities"></a>Capacidades de escala de portal
Existem três operações de dimensionamento:

* Alterar o número de endereços IP no ASE que estão disponíveis para utilização de SSL de IP.
* Alterar o tamanho do recurso de computação que é utilizado num agrupamento de recursos.
* Alterar o número de recursos de computação que são utilizados no agrupamento de recursos, manualmente ou através de dimensionamento automático.

No portal, existem três formas de controlar o número de servidores que têm nos seus conjuntos de recursos:

* Uma operação de dimensionamento do painel principal do ASE na parte superior. Pode fazer dimensionamento várias alterações de configuração para os conjuntos de front-end e de trabalho. Eles são todos aplicados como uma única operação.
* Uma operação de dimensionamento manual do agrupamento de recursos individuais **escala** painel, que está sob **definições**.
* Dimensionamento automático, que configurou do agrupamento de recursos individuais **dimensionamento** painel.

Para utilizar a operação de dimensionamento no painel do ASE, arraste o controlo de deslize para a quantidade desejado e salvar. Essa interface do Usuário também suporta a alteração do tamanho.  

![Dimensionamento da interface do Usuário][6]

Para utilizar as capacidades de manual ou o dimensionamento automático num agrupamento de recursos específico, aceda a **configurações** > **conjunto de Front-End** / **conjuntos de trabalho** como apropriado. Em seguida, abra o pool de que pretende alterar. Aceda a **configurações** > **aumentar horizontalmente** ou **definições** > **aumentar verticalmente**. O **aumentar horizontalmente** painel permite-lhe controlar a quantidade de instância. **Aumentar verticalmente** permite-lhe controlar o tamanho do recurso.  

![Definições de dimensionamento da interface do Usuário][7]

## <a name="fault-tolerance-considerations"></a>Considerações sobre tolerância
Pode configurar um ambiente de serviço de aplicações para utilizar recursos de computação total até 55. Esses 55 de recursos de computação, 50 só pode ser utilizado para alojar cargas de trabalho. O motivo para isso é duplo. Existe um mínimo de 2 recursos de computação de front-end.  Isso deixa até 53 para suportar a alocação de conjunto de trabalho. Para fornecer tolerância a falhas, tem de ter um recurso de computação adicionais que podem é alocado, de acordo com as seguintes regras:

* Cada conjunto de trabalho tem de, pelo menos, 1 recurso de computação adicionais que não está disponível para ser atribuído uma carga de trabalho.
* Quando a quantidade de recursos de computação num conjunto de trabalho ultrapassem um determinado valor, em seguida, outro recurso de computação é necessário para a tolerância a falhas. Não é o caso no conjunto de front-end.

Dentro de qualquer conjunto de trabalho único, os requisitos de tolerância a falhas são que para um determinado valor de X recursos atribuídos a um conjunto de trabalho:

* Se X for a entre 2 e 20, a quantidade de recursos de computação utilizável, que pode utilizar para cargas de trabalho é X-1.
* Se X for entre 21 e 40, a quantidade de recursos de computação utilizável, que pode utilizar para cargas de trabalho é X-2.
* Se X for entre 41 e 53, a quantidade de recursos de computação utilizável, que pode utilizar para cargas de trabalho é X-3.

Os requisitos mínimos tem 2 servidores de front-end e 2 funções de trabalho.  Com as instruções acima, em seguida, aqui estão alguns exemplos para esclarecer:  

* Se tiver 30 funcionários num único pool, em seguida, 28 delas pode ser utilizado para alojar cargas de trabalho.
* Se tiver 2 funções de trabalho num único pool, em seguida, 1 pode ser utilizado para alojar cargas de trabalho.
* Se tiver 20 funções de trabalho num único pool, em seguida, 19 pode ser utilizado para alojar cargas de trabalho.  
* Se tiver 21 trabalhadores num único pool, em seguida, ainda 19 apenas pode ser utilizado para alojar cargas de trabalho.  

O aspecto de tolerância é importante, mas precisa ter em mente, à medida que aumenta acima determinados limites. Se pretender adicionar mais capacidade do 20 instâncias, em seguida, aceda a 22 ou superior porque 21 não adiciona quaisquer mais capacidade. O mesmo é verdadeiro vai acima 40, em que o número seguinte que adiciona a capacidade é 42.  

## <a name="deleting-an-app-service-environment"></a>Eliminar um ambiente de serviço de aplicações
Se pretender eliminar um ambiente de serviço de aplicações, em seguida, basta usar o **eliminar** ação na parte superior do painel de ambiente de serviço de aplicações. Ao fazê-lo, será solicitado para introduzir o nome do seu ambiente de serviço de aplicações para confirmar que pretende realmente fazê-lo. Tenha em atenção que ao eliminar um ambiente de serviço de aplicações, elimine todo o conteúdo dentro dele também.  

![Eliminar um ambiente de serviço de aplicações da interface do Usuário][9]  

## <a name="getting-started"></a>Introdução
Para começar a utilizar com ambientes de serviço de aplicações, veja [como criar um ambiente de serviço de aplicações](app-service-web-how-to-create-an-app-service-environment.md).

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
