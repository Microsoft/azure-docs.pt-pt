---
title: Configurar ASE v1
description: Configuração, gerenciamento e monitoramento do Ambiente do Serviço de Aplicativo v1. Este documento é fornecido somente para clientes que usam o ASE v1 herdado.
author: ccompy
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: b37708e27887b20604a1fe921f14e51387793737
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687265"
---
# <a name="configuring-an-app-service-environment-v1"></a>Configurando um Ambiente do Serviço de Aplicativo v1

> [!NOTE]
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v1.  Há uma versão mais recente do Ambiente do Serviço de Aplicativo que é mais fácil de usar e é executada em uma infraestrutura mais potente. Para saber mais sobre a nova versão, comece com a [introdução ao ambiente do serviço de aplicativo](intro.md).
> 

## <a name="overview"></a>Visão geral
Em um alto nível, uma Ambiente do Serviço de Aplicativo do Azure consiste em vários componentes principais:

* Recursos de computação em execução no serviço hospedado Ambiente do Serviço de Aplicativo
* Armazenamento
* Um banco de dados
* Uma rede virtual (VNet) do Azure clássica (v1) ou do Resource Manager (v2) 
* Uma sub-rede com o Ambiente do Serviço de Aplicativo serviço hospedado em execução

### <a name="compute-resources"></a>Recursos de computação
Você usa os recursos de computação para seus quatro pools de recursos.  Cada Ambiente do Serviço de Aplicativo (ASE) tem um conjunto de front-ends e três possíveis pools de trabalho. Você não precisa usar todos os três pools de trabalho, se desejar, você pode usar apenas um ou dois.

Os hosts nos pools de recursos (front-ends e trabalhos) não são diretamente acessíveis aos locatários. Você não pode usar protocolo RDP (RDP) para se conectar a eles, alterar o provisionamento ou agir como um administrador neles.

Você pode definir a quantidade e o tamanho do pool de recursos. Em um ASE, você tem quatro opções de tamanho, que são rotuladas P1 a P4. Para obter detalhes sobre esses tamanhos e seus preços, consulte [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/).
Alterar a quantidade ou o tamanho é chamado de operação de escala.  Somente uma operação de escala pode estar em andamento por vez.

**Front-ends**: os front-ends são os pontos de extremidade http/https para seus aplicativos que são mantidos em seu ASE. Você não executa cargas de trabalho em front-ends.

* Um ASE começa com dois P2s, o que é suficiente para cargas de trabalho de desenvolvimento/teste e cargas de trabalho de produção de nível baixo. É altamente recomendável P3s para cargas de trabalho de produção moderadas a pesadas.
* Para cargas de trabalho de produção moderadas a pesadas, recomendamos que você tenha pelo menos quatro P3s para garantir que haja front-ends suficientes em execução quando a manutenção agendada ocorrer. As atividades de manutenção agendadas desativarão um front-end por vez. Isso reduz a capacidade de front-end disponível geral durante as atividades de manutenção.
* Os front-ends podem levar até uma hora para provisionar. 
* Para ajuste fino da escala, você deve monitorar a porcentagem de CPU, a porcentagem de memória e as métricas de solicitações ativas para o pool de front-end. Se as porcentagens de CPU ou de memória estiverem acima de 70% ao executar P3s, adicione mais front-ends. Se o valor das solicitações ativas for a média de 15.000 a 20.000 solicitações por front-end, você também deverá adicionar mais front-ends. A meta geral é manter as porcentagens de CPU e memória abaixo de 70% e solicitações ativas, calculando a média de 15.000 solicitações por front-end quando você estiver executando o P3s.  

**Trabalhadores**: os trabalhos são onde seus aplicativos realmente são executados. Ao escalar verticalmente seus planos do serviço de aplicativo, o usa trabalhadores no pool de trabalho associado.

* Você não pode adicionar trabalhos instantaneamente. Pode levar até uma hora para provisionar.
* O dimensionamento do tamanho de um recurso de computação para qualquer pool levará < 1 hora por domínio de atualização. Há 20 domínios de atualização em um ASE. Se você dimensionou o tamanho de computação de um pool de trabalho com 10 instâncias, pode levar até 10 horas para ser concluída.
* Se você alterar o tamanho dos recursos de computação que são usados em um pool de trabalho, você fará com que o frio inicie os aplicativos em execução nesse pool de trabalho.

A maneira mais rápida de alterar o tamanho do recurso de computação de um pool de trabalho que não está executando aplicativos é:

* Reduza verticalmente a quantidade de trabalhadores para 2.  O tamanho mínimo de escala vertical no portal é 2. Levará alguns minutos para desalocar suas instâncias. 
* Selecione o novo tamanho de computação e o número de instâncias. A partir daqui, levará até 2 horas para ser concluída.

Se seus aplicativos exigirem um tamanho de recurso de computação maior, você não poderá aproveitar as diretrizes anteriores. Em vez de alterar o tamanho do pool de trabalho que está hospedando esses aplicativos, você pode preencher outro pool de trabalho com trabalhadores do tamanho desejado e mover seus aplicativos para esse pool.

* Crie as instâncias adicionais do tamanho de computação necessário em outro pool de trabalho. Isso levará até uma hora para ser concluído.
* Reatribua os planos do serviço de aplicativo que estão hospedando os aplicativos que precisam de um tamanho maior para o pool de trabalho recém configurado. Essa é uma operação rápida que deve levar menos de um minuto para ser concluída.  
* Reduza o primeiro pool de trabalho se você não precisar mais dessas instâncias não utilizadas. Esta operação leva alguns minutos para ser concluída.

**Dimensionamento**automático: uma das ferramentas que podem ajudá-lo a gerenciar o consumo de recursos de computação é o dimensionamento automático. Você pode usar o dimensionamento automático para front-end ou pools de trabalho. Você pode fazer coisas como aumentar suas instâncias de qualquer tipo de pool na manhã e reduzi-las à noite. Ou talvez você possa adicionar instâncias quando o número de trabalhadores que estão disponíveis em um pool de trabalho cair abaixo de um determinado limite.

Se você quiser definir as regras de dimensionamento automático em relação às métricas do pool de recursos de computação, tenha em mente o tempo necessário para o provisionamento. Para obter mais detalhes sobre o dimensionamento automático de ambientes do serviço de aplicativo, consulte [como configurar a autoescala em um ambiente do serviço de aplicativo][ASEAutoscale].

### <a name="storage"></a>Armazenamento
Cada ASE é configurado com 500 GB de armazenamento. Esse espaço é usado em todos os aplicativos no ASE. Esse espaço de armazenamento faz parte do ASE e, no momento, não pode ser alternado para usar o espaço de armazenamento. Se você estiver fazendo ajustes em seu roteamento ou segurança de rede virtual, ainda será necessário permitir o acesso ao armazenamento do Azure, ou o ASE não poderá funcionar.

### <a name="database"></a>Base de Dados
O banco de dados contém as informações que definem o ambiente, bem como os detalhes sobre os aplicativos que estão sendo executados nele. Isso também faz parte da assinatura do Azure. Não é algo que você tenha uma capacidade direta de manipular. Se estiver fazendo ajustes em seu roteamento ou segurança de rede virtual, você ainda precisará permitir o acesso ao SQL Azure--ou o ASE não pode funcionar.

### <a name="network"></a>Rede
A VNet usada com seu ASE pode ser aquela que você fez quando criou o ASE ou um que você fez antecipadamente. Quando você cria a sub-rede durante a criação do ASE, ele força o ASE a estar no mesmo grupo de recursos que a rede virtual. Se você precisar que o grupo de recursos usado pelo seu ASE seja diferente daquele da sua VNet, você precisará criar seu ASE usando um modelo do Resource Manager.

Há algumas restrições na rede virtual que é usada para um ASE:

* A rede virtual deve ser uma rede virtual regional.
* Precisa haver uma sub-rede com 8 ou mais endereços em que o ASE é implantado.
* Depois que uma sub-rede é usada para hospedar um ASE, o intervalo de endereços da sub-rede não pode ser alterado. Por esse motivo, recomendamos que a sub-rede contenha pelo menos 64 endereços para acomodar qualquer crescimento futuro do ASE.
* Não pode haver nada mais na sub-rede, mas no ASE.

Ao contrário do serviço hospedado que contém o ASE, a [rede virtual][virtualnetwork] e a sub-rede estão sob controle do usuário.  Você pode administrar sua rede virtual por meio da interface do usuário da rede virtual ou do PowerShell.  Um ASE pode ser implantado em uma VNet clássica ou do Resource Manager.  As experiências do portal e da API são um pouco diferentes entre o VNets clássico e o Resource Manager, mas a experiência do ASE é a mesma.

A VNet usada para hospedar um ASE pode usar endereços IP RFC1918 privados ou pode usar endereços IP públicos.  Se você quiser usar um intervalo IP que não seja coberto por RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), você precisará criar sua VNet e sub-rede a ser usada pelo seu ASE antes da criação do ASE.

Como esse recurso coloca o serviço de Azure App em sua rede virtual, isso significa que seus aplicativos hospedados em seu ASE agora podem acessar os recursos disponibilizados por meio do ExpressRoute ou de VPNs (redes virtuais privadas) site a site diretamente. Os aplicativos que estão em seu Ambiente do Serviço de Aplicativo não exigem recursos de rede adicionais para acessar os recursos disponíveis para a rede virtual que está hospedando seu Ambiente do Serviço de Aplicativo. Isso significa que você não precisa usar Integração VNET ou Conexões Híbridas para acessar recursos ou estar conectados à sua rede virtual. Você ainda pode usar esses dois recursos para acessar recursos em redes que não estão conectadas à sua rede virtual.

Por exemplo, você pode usar Integração VNET para integrar com uma rede virtual que está em sua assinatura, mas não está conectada à rede virtual em que seu ASE está. Você ainda pode também usar Conexões Híbridas para acessar recursos que estão em outras redes, assim como você normalmente pode.  

Se você tiver sua rede virtual configurada com uma VPN de ExpressRoute, você deve estar ciente de algumas das necessidades de roteamento que um ASE tem. Há algumas configurações de UDR (rota definida pelo usuário) que são incompatíveis com um ASE. Para obter mais detalhes sobre como executar um ASE em uma rede virtual com o ExpressRoute, consulte [executando um ambiente do serviço de aplicativo em uma rede virtual com o expressroute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Protegendo o tráfego de entrada
Há dois métodos principais para controlar o tráfego de entrada para seu ASE.  Você pode usar NSGs (grupos de segurança de rede) para controlar quais endereços IP podem acessar seu ASE, conforme descrito aqui [como controlar o tráfego de entrada em um ambiente do serviço de aplicativo](app-service-app-service-environment-control-inbound-traffic.md) e você também pode configurar seu ase com um Load balancer interno (ILB).  Esses recursos também podem ser usados juntos se você quiser restringir o acesso usando o NSGs para o ASE ILB.

Quando você cria um ASE, ele criará um VIP em sua VNet.  Há dois tipos de VIP, externos e internos.  Quando você cria um ASE com um VIP externo, seus aplicativos em seu ASE poderão ser acessados por meio de um endereço IP roteável da Internet. Quando você seleciona interno, seu ASE será configurado com um ILB e não estará diretamente acessível à Internet.  Um ASE ILB ainda requer um VIP externo, mas ele é usado apenas para o acesso de gerenciamento e manutenção do Azure.  

Durante a criação do ASE ILB, você fornece o subdomínio usado pelo ASE ILB e terá que gerenciar seu próprio DNS para o subdomínio que você especificar.  Como você define o nome do subdomínio, também precisa gerenciar o certificado usado para acesso HTTPS.  Após a criação do ASE, você será solicitado a fornecer o certificado.  Para saber mais sobre como criar e usar um ASE ILB, leia [usando um Load balancer interno com um ambiente do serviço de aplicativo][ILBASE]. 

## <a name="portal"></a>Portal
Você pode gerenciar e monitorar seu Ambiente do Serviço de Aplicativo usando a interface do usuário no portal do Azure. Se você tiver um ASE, provavelmente verá o símbolo do serviço de aplicativo na sua barra lateral. Esse símbolo é usado para representar ambientes do serviço de aplicativo no portal do Azure:

![Símbolo de Ambiente do Serviço de Aplicativo][1]

Para abrir a interface do usuário que lista todos os seus ambientes de serviço de aplicativo, você pode usar o ícone ou selecionar a divisa (símbolo ">") na parte inferior da barra lateral para selecionar ambientes de serviço de aplicativo. Selecionando uma das ASEs listadas, você abre a interface do usuário que é usada para monitorá-la e gerenciá-la.

![Interface do usuário para monitorar e gerenciar seu Ambiente do Serviço de Aplicativo][2]

Essa primeira folha mostra algumas propriedades de seu ASE, juntamente com um gráfico de métrica por pool de recursos. Algumas das propriedades que são mostradas no bloco **Essentials** também são hiperlinks que abrirão a folha associada a ela. Por exemplo, você pode selecionar o nome da **rede virtual** para abrir a interface do usuário associada à rede virtual em que seu ase está sendo executado. Os planos e **aplicativos** do **serviço de aplicativo** abrem folhas que listam esses itens que estão em seu ASE.  

### <a name="monitoring"></a>Monitorização
Os gráficos permitem que você veja uma variedade de métricas de desempenho em cada pool de recursos. Para o pool de front-end, você pode monitorar a média de CPU e memória. Para pools de trabalho, você pode monitorar a quantidade que é usada e a quantidade disponível.

Vários planos do serviço de aplicativo podem usar os trabalhadores em um pool de trabalho. A carga de trabalho não é distribuída da mesma maneira que com os servidores front-end, portanto, o uso de CPU e memória não fornece muito no modo de informações úteis. É mais importante acompanhar Quantos trabalhadores você usou e estão disponíveis – especialmente se você estiver gerenciando esse sistema para outras pessoas usarem.  

Você também pode usar todas as métricas que podem ser controladas nos gráficos para configurar alertas. A configuração de alertas aqui funciona da mesma forma que em outro lugar no serviço de aplicativo. Você pode definir um alerta da parte da interface do usuário de **alertas** ou de detalhar qualquer interface do usuário de métricas e selecionar **adicionar alerta**.

![Interface do usuário de métricas][3]

As métricas que acabamos de discutir são as métricas de Ambiente do Serviço de Aplicativo. Também há métricas que estão disponíveis no nível do plano do serviço de aplicativo. É aí que o monitoramento de CPU e memória faz muito sentido.

Em um ASE, todos os planos do serviço de aplicativo são planos de serviço de aplicativo dedicados. Isso significa que os únicos aplicativos em execução nos hosts alocados para esse plano do serviço de aplicativo são os aplicativos nesse plano do serviço de aplicativo. Para ver detalhes sobre o plano do serviço de aplicativo, traga o plano do serviço de aplicativo de qualquer uma das listas na interface do usuário do ASE ou de **procurar planos do serviço de aplicativo** (que lista todos eles).   

### <a name="settings"></a>Definições
Na folha do ASE, há uma seção de **configurações** que contém vários recursos importantes:

**Configurações** > **Propriedades**: a folha **configurações** é aberta automaticamente quando você abre a folha do ase. Na parte superior, há **Propriedades**. Há vários itens aqui que são redundantes para o que você vê no **Essentials**, mas o que é muito útil é o **endereço IP virtual**, bem como os **endereços IP de saída**.

![Folha de configurações e propriedades][4]

**Configurações** > **endereços IP**: quando você cria um aplicativo de protocolo SSL IP (SSL) em seu ASE, você precisa de um endereço IP SSL. Para obter um, seu ASE precisa de IP SSL endereços que ele possui, que pode ser alocado. Quando um ASE é criado, ele tem um endereço IP SSL para essa finalidade, mas você pode adicionar mais. Há um encargo para endereços IP SSL adicionais, conforme mostrado em [preços do serviço de aplicativo][AppServicePricing] (na seção sobre conexões SSL). O preço adicional é o preço de IP SSL.

**Configurações** > **pool de Front-end** / **pools de trabalho**: cada uma dessas folhas de pool de recursos oferece a capacidade de ver informações apenas sobre esse pool de recursos, além de fornecer controles para dimensionar totalmente esse pool de recursos.  

A folha base de cada pool de recursos fornece um gráfico com métricas para esse pool de recursos. Assim como nos gráficos da folha do ASE, você pode entrar no gráfico e configurar alertas conforme desejado. A definição de um alerta da folha ASE para um pool de recursos específico faz a mesma coisa que fazer no pool de recursos. Na folha **configurações** do pool de trabalho, você tem acesso a todos os aplicativos ou planos do serviço de aplicativo em execução neste pool de trabalho.

![Interface do usuário configurações do pool de trabalho][5]

### <a name="portal-scale-capabilities"></a>Recursos de escala do portal
Há três operações de escala:

* Alteração do número de endereços IP no ASE que estão disponíveis para uso IP SSL.
* Alterar o tamanho do recurso de computação que é usado em um pool de recursos.
* Alterar o número de recursos de computação usados em um pool de recursos manualmente ou por meio de dimensionamento automático.

No portal, há três maneiras de controlar quantos servidores você tem em seus pools de recursos:

* Uma operação de dimensionamento da folha do ASE principal na parte superior. Você pode fazer várias alterações de configuração de escala nos pools de front-end e de trabalho. Todos eles são aplicados como uma única operação.
* Uma operação de dimensionamento manual da folha de **escala** do pool de recursos individual, que está em **configurações**.
* Dimensionamento automático, que você configura na folha de **escala** do pool de recursos individual.

Para usar a operação de dimensionamento na folha do ASE, arraste o controle deslizante para a quantidade desejada e salve. Essa interface do usuário também dá suporte à alteração do tamanho.  

![Expandir interface do usuário][6]

Para usar os recursos manuais ou de dimensionamento automático em um pool de recursos específico, vá para **configurações** > **pool de Front-end** / **pools de trabalho** , conforme apropriado. Em seguida, abra o pool que você deseja alterar. Vá para **configurações** > **Scale Out** ou **configurações** > **escalar verticalmente**. A folha **scale out** permite que você controle a quantidade de instâncias. **Escalar verticalmente** permite que você controle o tamanho do recurso.  

![Interface do usuário de configurações de escala][7]

## <a name="fault-tolerance-considerations"></a>Considerações sobre tolerância a falhas
Você pode configurar um Ambiente do Serviço de Aplicativo para usar até 55 de recursos de computação totais. Desses 55 recursos de computação, somente 50 pode ser usado para hospedar cargas de trabalho. O motivo disso é duplo. Há um mínimo de 2 recursos de computação front-end.  Isso deixa até 53 para dar suporte à alocação do pool de trabalho. Para fornecer tolerância a falhas, você precisa ter um recurso de computação adicional que é alocado de acordo com as seguintes regras:

* Cada pool de trabalho precisa de pelo menos um recurso de computação adicional que não esteja disponível para receber uma carga de trabalho.
* Quando a quantidade de recursos de computação em um pool de trabalho fica acima de um determinado valor, outro recurso de computação é necessário para tolerância a falhas. Esse não é o caso no pool de front-end.

Em qualquer pool de trabalho único, os requisitos de tolerância a falhas são aqueles para um determinado valor de X recursos atribuídos a um pool de trabalho:

* Se X estiver entre 2 e 20, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho será X-1.
* Se X estiver entre 21 e 40, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho será X-2.
* Se X estiver entre 41 e 53, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho será X-3.

A superfície mínima tem dois servidores front-end e 2 trabalhadores.  Com as instruções acima, aqui estão alguns exemplos para esclarecer:  

* Se você tiver 30 trabalhadores em um único pool, então 28 deles poderão ser usados para hospedar cargas de trabalho.
* Se você tiver 2 trabalhadores em um único pool, 1 poderá ser usado para hospedar cargas de trabalho.
* Se você tiver 20 trabalhadores em um único pool, 19 poderá ser usado para hospedar cargas de trabalho.  
* Se você tiver 21 trabalhadores em um único pool, ainda será possível usar apenas 19 para hospedar cargas de trabalho.  

O aspecto de tolerância a falhas é importante, mas você precisa mantê-lo em mente ao dimensionar acima de determinados limites. Se você quiser adicionar mais capacidade de 20 instâncias, vá para 22 ou superior, pois 21 não adiciona mais capacidade. O mesmo acontece acima de 40, em que o próximo número que adiciona capacidade é 42.  

## <a name="deleting-an-app-service-environment"></a>Excluindo um Ambiente do Serviço de Aplicativo
Se você quiser excluir um Ambiente do Serviço de Aplicativo, basta usar a ação **excluir** na parte superior da folha ambiente do serviço de aplicativo. Ao fazer isso, você será solicitado a inserir o nome do seu Ambiente do Serviço de Aplicativo para confirmar que realmente deseja fazer isso. Observe que, ao excluir um Ambiente do Serviço de Aplicativo, você também exclui todo o conteúdo dentro dele.  

![Excluir uma interface do usuário do Ambiente do Serviço de Aplicativo][9]  

## <a name="getting-started"></a>Introdução
Para começar a usar os ambientes do serviço de aplicativo, consulte [como criar um ambiente do serviço de aplicativo](app-service-web-how-to-create-an-app-service-environment.md).

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
