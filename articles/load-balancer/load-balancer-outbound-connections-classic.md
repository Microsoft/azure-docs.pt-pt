---
title: Ligações de saída no Azure (clássico)
titlesuffix: Azure Load Balancer
description: Este artigo explica como o Azure permite que serviços para comunicar com serviços de internet pública em nuvem.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: kumud
ms.openlocfilehash: 3267d79387586f5ca8475d7ac0ed0f86d3f64f0d
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876947"
---
# <a name="outbound-connections-classic"></a>Ligações de saída (clássicas)

O Azure fornece a conectividade de saída para implementações dos clientes através de diversos mecanismos diferentes. Este artigo descreve quais são os cenários, quando eles se aplicam, como elas funcionam e como geri-los.

>[!NOTE]
>Este artigo abrange apenas a implementações clássicas.  Revisão [ligações de saída](load-balancer-outbound-connections.md) para todos os cenários de implementação do Resource Manager no Azure.

Uma implementação no Azure pode comunicar com pontos finais de fora do Azure no espaço de endereços IP público. Quando uma instância inicia um fluxo de saída para um destino no espaço de endereços IP público, do Azure maps dinamicamente o endereço IP privado para um endereço IP público. Depois de criar esse mapeamento, tráfego de retorno para este fluxo originado pela saída também pode acessar o endereço IP privado em que o fluxo teve origem.

Para efetuar esta função, o Azure utiliza tradução de endereços de rede de origem (SNAT). Quando vários endereços IP privados são mascarando-o por trás de um único endereço IP público, o Azure utiliza [tradução de endereços (PAT) de porta](#pat) disfarçar os endereços IP privados. Portas efêmeras são utilizadas para PAT e são [pré-alocado](#preallocatedports) com base no tamanho do conjunto.

Existem várias [cenários de saída](#scenarios). Pode combinar estes cenários, conforme necessário. Reveja-os cuidadosamente para compreender as capacidades, restrições e padrões de acordo com seu modelo de implementação e o cenário do aplicativo. Reveja a documentação de orientação para [gerir estes cenários](#snatexhaust).

## <a name="scenarios"></a>Descrição geral do cenário

O Azure fornece três métodos diferentes para alcançar as implementações clássicas de conectividade de saída.  Nem todas as implementações de clássico têm todos os três cenários disponíveis:

| Cenário | Método | Protocolos IP | Descrição | Função de trabalho Web | IaaS | 
| --- | --- | --- | --- | --- | --- |
| [1. VM com um endereço IP público de nível de instância](#ilpip) | SNAT, porta mascarando não utilizado | TCP, UDP, ICMP, ESP | O Azure utiliza o IP público atribuído a Máquina Virtual. A instância tem todas as portas efêmeras disponíveis. | Não | Sim |
| [2. ponto final com balanceamento de carga o público](#publiclbendpoint) | SNAT com porta mascarando (PAT) para o ponto final público | TCP, UDP | Azure partilha o IP endereço público ponto final público com vários pontos de extremidade privados. O Azure utiliza portas efêmeras do ponto final público para PAT. | Sim | Sim |
| [3. VM autónoma](#defaultsnat) | SNAT com mascarando-porta (PAT) | TCP, UDP | Azure designa um endereço IP público para SNAT automaticamente, compartilha este endereço IP público com toda a implantação e utiliza as portas efêmeras do endereço IP do ponto final público para PAT. Este é um cenário de contingência para os cenários anteriores. Não o recomendamos se precisar de visibilidade e controlo. | Sim | Sim |

Este é um subconjunto da funcionalidade de ligação de saída disponível para implementações do Resource Manager no Azure.  

Implementações diferentes no modelo clássico têm diferentes funcionalidades:

| Implementação clássica | Funcionalidade disponível | 
| --- | --- |
| Máquina Virtual | cenário [1](#ilpip), [2](#publiclbendpoint), ou [3](#defaultsnat) |
| Função de trabalho Web | apenas o cenário [2](#publiclbendpoint), [3](#defaultsnat) | 

[Estratégias de atenuação](#snatexhaust) também têm as diferenças do mesmo.

O algoritmo utilizado para pré-alocação portas efêmeras para PAT para implementações clássicas é igual de implementações de recursos do Azure Resource Manager.

### <a name="ilpip"></a>Cenário 1: VM com um endereço IP público de nível de instância

Neste cenário, a VM tem uma instância ao nível do público IP (ILPIP) atribuído a ele. Como diz respeito a ligações de saída, não importa se a VM tem o ponto final com balanceamento de carga ou não. Este cenário tem precedência sobre as outras. Quando é utilizado um ILPIP, a VM utiliza o ILPIP para todos os fluxos de saída.  

Uma pública IP atribuído a uma VM é um 1:1 relação (em vez de 1:many) e implementado como um NAT de 1:1 sem monitoração de estado.  Não é utilizado a porta Disfarce (PAT) e a VM tem todas as portas efêmeras disponíveis para utilização.

Se seu aplicativo inicia muitos fluxos de saída e tiver exaustão de porta SNAT, considere atribuir um [ILPIP para atenuar as restrições SNAT](#assignilpip). Revisão [esgotamento de gerenciamento de SNAT](#snatexhaust) na íntegra.

### <a name="publiclbendpoint"></a>Cenário 2: Ponto final com balanceamento de carga público

Neste cenário, a VM ou a função de trabalho da Web está associada um endereço IP público através do ponto final com balanceamento de carga. A VM não tem um endereço IP público atribuído ao mesmo. 

Quando a VM com balanceamento de carga cria um fluxo de saída, Azure traduz-se o endereço IP de origem privados do fluxo de saída para o endereço IP público do ponto de extremidade com balanceamento de carga público. O Azure utiliza o SNAT para efetuar esta função. O Azure também utiliza [dar um TAPINHA](#pat) disfarçar os vários endereços IP privados por trás de um endereço IP público. 

Portas efêmeras de frontend de endereço IP público do Balanceador de carga são utilizadas para distinguir os fluxos individuais originados pela VM. Utiliza o SNAT dinamicamente [pré-alocado portas efêmeras](#preallocatedports) quando são criados os fluxos de saída. Neste contexto, as portas efémeras utilizadas para SNAT são chamadas de portas SNAT.

Portas SNAT são pré-alocado conforme descrito no [SNAT de compreensão e PAT](#snat) secção. Eles são um recurso finito que pode esgotar-se. É importante compreender como estão [consumidos](#pat). Para compreender como estruturar para esse consumo e mitigar conforme necessário, reveja [esgotamento de gerenciamento de SNAT](#snatexhaust).

Quando [vários pontos finais públicos com balanceamento de carga](load-balancer-multivip.md) existe, qualquer um destes endereços IP públicos são candidatos para fluxos de saída e um é selecionado aleatoriamente.  

### <a name="defaultsnat"></a>Cenário 3: Nenhum endereço IP público associado

Neste cenário, a VM ou a função de trabalho da Web não é parte de um ponto de final com balanceamento de carga público.  E, no caso de VM, não tem um endereço ILPIP atribuído a ele. Quando a VM cria um fluxo de saída, Azure traduz-se o endereço IP de origem privados do fluxo de saída para um endereço IP público de origem. O endereço IP público utilizado para este fluxo de saída não é configurável e não contam para o limite de recursos IP público da subscrição.  Azure atribui automaticamente este endereço.

O Azure utiliza o SNAT com mascarando-porta ([dar um TAPINHA](#pat)) para efetuar esta função. Este cenário é semelhante ao cenário 2, mas não existe nenhum controle sobre o endereço IP utilizado. Este é um cenário de contingência para quando não existem cenários 1 e 2. Este cenário não é recomendada se pretender que o controle sobre o endereço de saída. Se as ligações de saída são uma parte crítica da sua aplicação, deve escolher outro cenário.

Portas SNAT são pré-alocado conforme descrito no [SNAT de compreensão e PAT](#snat) secção.  O número de VMs ou o endereço IP público de partilha do Web Worker Roles determina o número de portas efêmeras pré-alocado.   É importante compreender como estão [consumidos](#pat). Para compreender como estruturar para esse consumo e mitigar conforme necessário, reveja [esgotamento de gerenciamento de SNAT](#snatexhaust).

## <a name="snat"></a>Noções básicas sobre SNAT e PAT

### <a name="pat"></a>Porta Disfarce SNAT (PAT)

Quando uma implementação faz uma ligação de saída, cada origem de ligação de saída é reescrita. A origem é Reescrita do espaço de endereço IP privado para o IP público associado com a implementação (com base em cenários descritos acima). No espaço de endereços IP públicos, a 5 cadeias de identificação do fluxo (endereço IP de origem, porta de origem, protocolo de transporte IP, endereço IP de destino, porta de destino) tem de ser exclusiva.  

Portas efêmeras (portas SNAT) são utilizadas para conseguir isso depois de reescrever o endereço IP de origem privados, uma vez que vários fluxos provêm de um único endereço IP público. 

Uma porta SNAT é consumida por fluxo para um endereço IP de destino único, porta e protocolo. Para vários fluxos para o mesmo endereço IP de destino, portas e protocolos, cada fluxo consome uma única porta SNAT. Isto garante que os fluxos são exclusivos, quando são provêm do mesmo endereço IP público e vão para o mesmo endereço IP de destino, porta e protocolo. 

Vários fluxos, cada um para um endereço IP de destino diferente, porta e protocolo, partilham uma única porta SNAT. O endereço IP de destino, portas e protocolos que fluxos exclusivo sem a necessidade de origem adicionais portas para distinguir os fluxos no espaço de endereços IP público.

Quando os recursos de porta SNAT são esgotados, fluxos de saída falharem até que os fluxos existentes de portas SNAT de versão. Balanceador de carga reclama portas SNAT quando o fluxo fecha e utiliza um [tempo limite de inatividade de 4 minutos](#idletimeout) para reclamação portas SNAT de fluxos de inatividade.

Padrões atenuar as condições que normalmente conduzem a exaustão de porta SNAT, reveja os [SNAT de gerir](#snatexhaust) secção.

### <a name="preallocatedports"></a>Pré-alocação de porta efêmera para a porta mascarando-o SNAT (PAT)

O Azure utiliza um algoritmo para determinar o número de pré-alocado SNAT portas disponíveis com base no tamanho do conjunto de back-end ao utilizar a porta SNAT de Disfarce ([dar um TAPINHA](#pat)). Portas SNAT são portas efêmeras disponíveis para um determinado endereço de origem IP público.

Azure preallocates SNAT portas quando uma instância for implementada com base no número de instâncias VM ou a função de trabalho Web partilha um determinado endereço IP público.  Quando os fluxos de saída são criados, [dar um TAPINHA](#pat) dinamicamente consome (até ao limite preallocated) e as versões destas portas quando fecha o fluxo ou limites de ociosidade acontecem.

A tabela seguinte mostra os preallocations de porta SNAT para os escalões de tamanhos de conjuntos de back-end:

| Instâncias | Portas SNAT pré-alocado por instância |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |

Lembre-se de que o número de portas SNAT disponíveis não traduz diretamente para o número de fluxos. Uma única porta SNAT pode ser reutilizada para vários destinos exclusivos. As portas são consumidas apenas se for necessário para que os fluxos exclusivo. Para obter orientações de design e mitigação, consulte a seção sobre [como gerir este recurso exhaustible](#snatexhaust) e a secção que descreve [dar um TAPINHA](#pat).

Alterar o tamanho da sua implementação pode afetar alguns dos seus fluxos estabelecidos. Se o tamanho do conjunto de back-end aumenta e faz a transição para a próxima camada, metade das suas portas SNAT preallocated são recuperada durante a transição para a próxima camada de conjunto de back-end maior. Fluxos associados uma porta SNAT reclaimed serão o tempo limite e devem ser restabelecidos. Se um novo fluxo é tentado, o fluxo será bem sucedida imediatamente, desde que as portas pré-alocado estão disponíveis.

Se o tamanho de implementação diminui e faz a transição para um escalão mais baixo, aumenta o número de portas SNAT disponíveis. Neste caso, existente alocadas SNAT portas e seus respectivos fluxos não são afetados.

Se um serviço em nuvem é reimplementado ou alterado, a infraestrutura temporariamente pode comunicar o conjunto de back-end para ser tão real até duas vezes, como grandes e o Azure irá por sua vez pré-alocar menos SNAT portas por instância que o esperado.  Temporariamente, isso pode aumentar a probabilidade de exaustão de porta SNAT. Finalmente, o tamanho do conjunto farão a transição para o tamanho real e Azure irá aumentar automaticamente pré-alocado portas SNAT para o número esperado de acordo com a tabela acima.  Este comportamento é propositado e não é configurável.

Alocações de portas SNAT são específico de protocolo de transporte IP (TCP e UDP que e mantidó em separado) e são lançadas nas seguintes condições:

### <a name="tcp-snat-port-release"></a>Versão de porta de TCP SNAT

- Se ambos os servidor/cliente enviar FIN/ACK, porta SNAT será lançada após 240 segundos.
- Se um RST é visto, porta SNAT será lançada após 15 segundos.
- foi atingido o tempo limite de inatividade

### <a name="udp-snat-port-release"></a>Versão de porta de SNAT de UDP

- foi atingido o tempo limite de inatividade

## <a name="problemsolving"></a> Resolução de problemas 

Esta secção destina-se para ajudar a atenuar o esgotamento de SNAT e outros cenários que podem ocorrer com ligações de saída no Azure.

### <a name="snatexhaust"></a> Gerir exaustão de porta de SNAT (PAT)
[Portas efêmeras](#preallocatedports) utilizado para [dar um TAPINHA](#pat) são um recurso de exhaustible, conforme descrito na [nenhum IP público associado](#defaultsnat) e [ponto final com balanceamento de carga público](#publiclbendpoint).

Se souber que está a iniciar muitos TCP ou UDP ligações de saída para o mesmo endereço IP de destino e porta e observar as ligações de saída a falhar ou aconselhado pelo suporte que está a esgotar portas SNAT (pré-alocado [efémeras portas](#preallocatedports) utilizada pelo [dar um TAPINHA](#pat)), tem várias opções de redução geral. Reveja estas opções e decidir o que está disponível e é melhor para seu cenário. É possível que um ou mais podem ajudar a lidar com esse cenário.

Se estiver a ter dificuldades para compreender o comportamento de ligação de saída, pode usar estatísticas de pilha IP (netstat). Ou pode ser útil observar comportamentos de ligação através de capturas de pacotes.

#### <a name="connectionreuse"></a>Modificar o aplicativo para reutilizar conexões 
Pode reduzir a pedido para portas efêmeras que são utilizados para SNAT ao reutilizar conexões em seu aplicativo. Isso é especialmente verdadeiro para protocolos como HTTP/1.1, onde a reutilização de ligação é a predefinição. E outros protocolos que utilizam HTTP como transporte (por exemplo, REST) podem se beneficiar por sua vez. 

Reutilização é sempre melhor do que individuais, atômicas conexões TCP para cada solicitação. Reutilize resulta num melhor desempenho, transações de TCP muito eficientes.

#### <a name="connection pooling"></a>Modificar a aplicação para utilizar o agrupamento de ligações
Pode empregar uma ligação de agrupamento de esquema no seu aplicativo, onde os pedidos são distribuídos internamente por um conjunto fixo de ligações (cada reutilizando sempre que possível). Este esquema restringe o número de portas efêmeras em utilização e cria um ambiente mais previsível. Este esquema também pode aumentar o débito de pedidos ao permitir que várias operações simultâneas quando uma única ligação está a bloquear a resposta de uma operação.  

Agrupamento de ligações já pode existir dentro do framework que está a utilizar para desenvolver seu aplicativo ou as definições de configuração para a sua aplicação. Pode combinar o pool de conexões com a reutilização de ligação. Os pedidos de vários, em seguida, consumam um fixo e previsível, o número de portas para o mesmo endereço IP de destino e a porta. Os pedidos também beneficiam de uma utilização eficiente de reduzir a utilização de recursos e latência de transações de TCP. Transações de UDP também podem se beneficiar, porque a gerir o número de fluxos UDP pode evitar condições de esgotar e gerir a utilização de porta SNAT por sua vez.

#### <a name="retry logic"></a>Modificar o aplicativo para usar menos agressiva lógica de repetição
Quando [pré-alocado portas efêmeras](#preallocatedports) utilizado para [dar um TAPINHA](#pat) são esgotadas ou aplicação ocorrerem falhas, agressiva ou de força bruta repete sem Win32/decay e término de lógica de fazer com que o esgotamento de ocorrer ou manter. Pode reduzir a pedido para portas efêmeras utilizando uma lógica de repetição menos agressiva. 

Portas efêmeras têm um 4 minutos tempo limite de inatividade (não ajustável). Se as repetições são demasiado agressivas, o esgotamento não tem nenhuma oportunidade de limpar por conta própria. Portanto, considerar como – e quantas vezes – a aplicação repete transações é uma parte crítica do design.

#### <a name="assignilpip"></a>Atribuir um IP público ao nível de instância para cada VM
Atribuir um ILPIP altera o seu cenário para [IP público de nível de instância para uma VM](#ilpip). Todas as portas efêmeras de IP público que são utilizadas para cada VM estão disponíveis para a VM. (Em vez de cenários nos quais as portas efêmeras de um IP público são compartilhadas com todas as VMs associadas com a respetiva implementação.) Existem vantagens e desvantagens para considerar, como o impacto potencial de listas de permissões de um grande número de endereços IP individuais.

>[!NOTE] 
>Esta opção não está disponível para funções de trabalho do web.

### <a name="idletimeout"></a>Utilizar keepalives para repor o tempo de limite de inatividade saído

As ligações de saída têm um tempo de limite de inatividade de 4 minutos. Este tempo limite não é ajustável. No entanto, pode utilizar o transporte (por exemplo, o TCP keepalives) ou keepalives de camada de aplicativo para atualizar um fluxo de inatividade e repor este tempo limite de inatividade, se necessário.  Contacte o seu fornecedor de qualquer software de pacotes nIsto é suportado ou como ativá-lo.  Em geral, apenas um lado precisa gerar keepalives para repor o tempo limite de inatividade. 

## <a name="discoveroutbound"></a>Detetar o IP público que utiliza uma VM
Existem várias formas de determinar o endereço IP de origem público de uma ligação de saída. OpenDNS fornece um serviço que pode apresentar-lhe o endereço IP público da sua VM. 

Ao utilizar o comando nslookup, pode enviar uma consulta DNS para o myip.opendns.com de nome para o resolvedor OpenDNS. O serviço devolve o endereço IP de origem que foi utilizado para enviar a consulta. Quando executar a consulta seguinte da VM, a resposta é o IP público utilizado para essa VM:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [Balanceador de carga](load-balancer-overview.md) utilizados em implementações do Resource Manager.
- Conheça o modo [ligação de saída](load-balancer-outbound-connections.md) cenários disponíveis em implementações do Resource Manager.
