---
title: Ligações de saída no Azure
titlesuffix: Azure Load Balancer
description: Este artigo explica como o Azure permite às VMs comunicarem com os serviços de internet pública.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/05/2019
ms.author: kumud
ms.openlocfilehash: a42a56b8a4a54c33297461a427a2b64b72357020
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194083"
---
# <a name="outbound-connections-in-azure"></a>Ligações de saída no Azure

O Azure fornece a conectividade de saída para implementações dos clientes através de diversos mecanismos diferentes. Este artigo descreve quais são os cenários, quando eles se aplicam, como elas funcionam e como geri-los.

>[!NOTE] 
>Este artigo abrange apenas as implementações do Resource Manager. Revisão [ligações de saída (clássicas)](load-balancer-outbound-connections-classic.md) para todos os cenários de implementação clássico no Azure.

Uma implementação no Azure pode comunicar com pontos finais de fora do Azure no espaço de endereços IP público. Quando uma instância inicia um fluxo de saída para um destino no espaço de endereços IP público, do Azure maps dinamicamente o endereço IP privado para um endereço IP público. Depois de criar esse mapeamento, tráfego de retorno para este fluxo originado pela saída também pode acessar o endereço IP privado em que o fluxo teve origem.

Para efetuar esta função, o Azure utiliza tradução de endereços de rede de origem (SNAT). Quando vários endereços IP privados são mascarando-o por trás de um único endereço IP público, o Azure utiliza [tradução de endereços (PAT) de porta](#pat) disfarçar os endereços IP privados. Portas efêmeras são utilizadas para PAT e são [pré-alocado](#preallocatedports) com base no tamanho do conjunto.

Existem várias [cenários de saída](#scenarios). Pode combinar estes cenários, conforme necessário. Reveja-os cuidadosamente para compreender as capacidades, restrições e padrões de acordo com seu modelo de implementação e o cenário do aplicativo. Reveja a documentação de orientação para [gerir estes cenários](#snatexhaust).

>[!IMPORTANT] 
>Standard Balanceador de carga e o IP público Standard apresentam novas capacidades e comportamentos diferentes para conectividade de saída.  Eles não são os mesmos que os SKUs básico.  Se quiser conectividade de saída ao trabalhar com Standard SKUs, tem de defini-lo, com endereços IP públicos Standard ou Balanceador de carga Standard público explicitamente.  Isto inclui a criação de conectividade de saída ao utilizar e o Balanceador de carga interno do padrão.  Recomendamos que sempre usar regras de saída num Balanceador de carga Standard público.  [Cenário 3](#defaultsnat) não está disponível com o Standard SKU.  Isso significa que quando é utilizado um Standard Balanceador de carga interno, terá de tomar medidas para criar a conectividade de saída para as VMs no conjunto de back-end se conectividade de saída for o pretendido.  No contexto de conectividade de saída, uma única VM autónoma, todas as VMS no conjunto de disponibilidade, todas as instâncias numa VMSS se comportam como um grupo. Isso significa que, se uma única VM num conjunto de disponibilidade estiver associada um SKU Standard, todas as instâncias VM dentro deste conjunto de disponibilidade agora se comportam pelas mesmas regras como se eles estão associados a Standard SKU, mesmo que uma instância individual não é diretamente associada com o mesmo.  Reveja com cuidado todo este documento a compreender os conceitos globais, consulte [Balanceador de carga Standard](load-balancer-standard-overview.md) às diferenças entre SKUs e revisão [regras de saída](load-balancer-outbound-rules-overview.md).  Utilizar regras de saída permite-lhe controlo detalhado sobre todos os aspetos de conectividade de saída.

## <a name="scenarios"></a>Descrição geral do cenário

O Balanceador de carga do Azure e os recursos relacionados são definidos explicitamente quando estiver usando [do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Atualmente, o Azure fornece três métodos diferentes para alcançar a conectividade de saída para os recursos do Azure Resource Manager. 

| SKUs | Cenário | Método | Protocolos IP | Descrição |
| --- | --- | --- | --- | --- |
| Standard, Basic | [1. VM com um endereço IP público de nível de instância (com ou sem o Balanceador de carga)](#ilpip) | SNAT, porta mascarando não utilizado | TCP, UDP, ICMP, ESP | O Azure utiliza o IP público atribuído à configuração de IP de NIC de. da instância A instância tem todas as portas efêmeras disponíveis. Ao utilizar o Balanceador de carga Standard, deve usar [regras de saída](load-balancer-outbound-rules-overview.md) definir explicitamente a conectividade de saída |
| Standard, Basic | [2. Balanceador de carga público associado a uma VM (nenhum endereço IP público de nível de instância na instância)](#lb) | SNAT com mascarando-porta (PAT) usando o front-ends do Balanceador de carga | TCP, UDP |Azure partilha o endereço IP público do front-ends de Balanceador de carga público com vários endereços IP privados. O Azure utiliza portas efêmeras do front-ends para PAT. |
| nenhum ou básico | [3. VM autónoma (nenhum Balanceador de carga, nenhum endereço IP público de nível de instância)](#defaultsnat) | SNAT com mascarando-porta (PAT) | TCP, UDP | Azure automaticamente designa um endereço IP público para SNAT, compartilha este endereço IP público com vários endereços IP privados do conjunto de disponibilidade e utiliza portas efêmeras deste endereço IP público. Este cenário é uma contingência para os cenários anteriores. Não o recomendamos se precisar de visibilidade e controlo. |

Se não pretender que uma VM para comunicar com pontos finais fora do Azure no espaço de endereço IP público, pode utilizar grupos de segurança de rede (NSGs) para bloquear o acesso conforme necessário. A secção [impedir a conectividade de saída](#preventoutbound) aborda os NSGs mais detalhadamente. Instruções sobre como conceber, implementar e gerir uma rede virtual sem qualquer acesso de saída estão fora do escopo deste artigo.

### <a name="ilpip"></a>Cenário 1: VM com um endereço IP público de nível de instância

Neste cenário, a VM tem uma instância ao nível do público IP (ILPIP) atribuído a ele. Como diz respeito a ligações de saída, não importa se a VM está ou não de balanceamento de carga. Este cenário tem precedência sobre as outras. Quando é utilizado um ILPIP, a VM utiliza o ILPIP para todos os fluxos de saída.  

Um IP público atribuído a uma VM é uma relação de 1:1 (em vez de 1: muitos) e implementado como um NAT de 1:1 sem monitoração de estado.  Não é utilizado a porta Disfarce (PAT) e a VM tem todas as portas efêmeras disponíveis para utilização.

Se seu aplicativo inicia muitos fluxos de saída e tiver exaustão de porta SNAT, considere atribuir um [ILPIP para atenuar as restrições SNAT](#assignilpip). Revisão [esgotamento de gerenciamento de SNAT](#snatexhaust) na íntegra.

### <a name="lb"></a>Cenário 2: VM com balanceamento de carga sem um endereço IP público de nível de instância

Neste cenário, a VM faz parte de um conjunto de back-end de Balanceador de carga público. A VM não tem um endereço IP público atribuído ao mesmo. O recurso do Balanceador de carga tem de ser configurado com uma regra de Balanceador de carga para criar uma ligação entre pública IP Front-end com o conjunto de back-end.

Se não concluir esta configuração de regra, o comportamento é conforme descrito no cenário para [VM autónoma com nenhum IP público de nível de instância](#defaultsnat). Não é necessário para a regra ter um serviço de escuta de trabalho no conjunto de back-end para a sonda de estado de funcionamento com êxito.

Quando a VM com balanceamento de carga cria um fluxo de saída, Azure traduz-se o endereço IP de origem privados do fluxo de saída para o endereço IP público do front-end de Balanceador de carga público. O Azure utiliza o SNAT para efetuar esta função. O Azure também utiliza [dar um TAPINHA](#pat) disfarçar os vários endereços IP privados por trás de um endereço IP público. 

Portas efêmeras de frontend de endereço IP público do Balanceador de carga são utilizadas para distinguir os fluxos individuais originados pela VM. Utiliza o SNAT dinamicamente [pré-alocado portas efêmeras](#preallocatedports) quando são criados os fluxos de saída. Neste contexto, as portas efémeras utilizadas para SNAT são chamadas de portas SNAT.

Portas SNAT previamente são alocadas, conforme descrito no [SNAT de compreensão e PAT](#snat) secção. Eles são um recurso finito que pode esgotar-se. É importante compreender como estão [consumidos](#pat). Para compreender como estruturar para esse consumo e mitigar conforme necessário, reveja [esgotamento de gerenciamento de SNAT](#snatexhaust).

Quando [vários endereços IP públicos estão associados com o Balanceador de carga básico](load-balancer-multivip-overview.md), qualquer um destes endereços IP públicos são candidatos para fluxos de saída e um é selecionado aleatoriamente.  

Para monitorizar o estado de funcionamento das ligações de saída com o Balanceador de carga básico, pode usar [registos do Azure Monitor para o Balanceador de carga](load-balancer-monitor-log.md) e [registos de eventos de alerta](load-balancer-monitor-log.md#alert-event-log) para monitorizar as mensagens de esgotamento de porta SNAT.

### <a name="defaultsnat"></a>Cenário 3: VM autónoma, sem um endereço IP público de nível de instância

Neste cenário, a VM não é parte de um conjunto de Balanceador de carga público (e não faz parte de um conjunto de Balanceador de carga interno) e não tem um endereço ILPIP atribuído a ele. Quando a VM cria um fluxo de saída, Azure traduz-se o endereço IP de origem privados do fluxo de saída para um endereço IP público de origem. O endereço IP público utilizado para este fluxo de saída não é configurável e não contam para o limite de recursos IP público da subscrição. Este endereço IP público não pertence ao utilizador e não pode ser reservado. Se voltar a implementar o conjunto de dimensionamento VM ou o conjunto de disponibilidade ou a máquina virtual, este endereço IP público será liberado e um novo endereço IP público pedida. Não utilize este cenário para endereços IP da lista de permissões. Em vez disso, utilize um dos outros dois cenários em que declara explicitamente o cenário de saída e o endereço IP público a utilizar para conectividade de saída.

>[!IMPORTANT] 
>Este cenário também se aplica quando __apenas__ está ligado um balanceador de carga básico interno. Cenário 3 é __não está disponível__ quando um Standard Balanceador de carga interno está ligado a uma VM.  Tem de criar explicitamente [cenário 1](#ilpip) ou [cenário 2](#lb) além de utilizar um Standard Balanceador de carga interno.

O Azure utiliza o SNAT com mascarando-porta ([dar um TAPINHA](#pat)) para efetuar esta função. Este cenário é semelhante à [cenário 2](#lb), mas não existe nenhum controle sobre o endereço IP utilizado. Este é um cenário de contingência para quando não existem cenários 1 e 2. Este cenário não é recomendada se pretender que o controle sobre o endereço de saída. Se as ligações de saída são uma parte crítica do seu aplicativo, deve escolher outro cenário.

Portas SNAT são pré-alocado conforme descrito no [SNAT de compreensão e PAT](#snat) secção.  O número de VMs um conjunto de disponibilidade de partilha determina qual dos escalões preallocation aplica-se.  Uma VM sem um conjunto de disponibilidade autónoma é efetivamente um conjunto de 1 para efeitos de determinar a pré-alocação (portas SNAT de 1024). Portas SNAT são um recurso finito que pode esgotar-se. É importante compreender como estão [consumidos](#pat). Para compreender como estruturar para esse consumo e mitigar conforme necessário, reveja [esgotamento de gerenciamento de SNAT](#snatexhaust).

### <a name="combinations"></a>Vários cenários combinados

Pode combinar os cenários descritos nas secções anteriores para obter um resultado específico. Quando existirem vários cenários, uma ordem de precedência aplica-se: [cenário 1](#ilpip) terão precedência sobre [cenário 2](#lb) e [3](#defaultsnat). [Cenário 2](#lb) substitui [cenário 3](#defaultsnat).

Um exemplo é uma implementação do Azure Resource Manager, onde o aplicativo depende muito de ligações de saída para um número limitado de destinos, mas também recebe os fluxos de entrada através de um front-end de Balanceador de carga. Neste caso, pode combinar cenários 1 e 2 para alívio. Para padrões adicionais, reveja [esgotamento de gerenciamento de SNAT](#snatexhaust).

### <a name="multife"></a> Vários front-ends para fluxos de saída

#### <a name="standard-load-balancer"></a>Balanceador de Carga Standard

Balanceador de carga Standard utiliza todos os candidatos para fluxos de saída ao mesmo tempo hora em que [(público) vários IP Front-Ends](load-balancer-multivip-overview.md) está presente. Cada front-end multiplica o número de portas SNAT pré-alocado disponíveis se uma regra de balanceamento de carga está ativada para ligações de saída.

Pode escolher suprimir um endereço IP de front-end sejam utilizados para ligações de saída com uma nova carga balanceamento regra opção:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Normalmente, o `disableOutboundSnat` opção assume a predefinição _false_ e significa que esta regra programas SNAT de saída para as VMs associadas no conjunto de back-end de regra de balanceamento de carga. O `disableOutboundSnat` pode ser alterado para _true_ para impedir que o Balanceador de carga com o endereço IP de front-end associado para ligações de saída para as VMs no conjunto de back-end desta regra de balanceamento de carga.  E também ainda pode designar um endereço IP específico para fluxos de saída conforme descrito em [combinadas de vários cenários](#combinations) também.

#### <a name="load-balancer-basic"></a>Basic de Balanceador de carga

Balanceador de carga básico escolhe um front-end único a ser utilizado para fluxos de saída quando [(público) vários IP Front-Ends](load-balancer-multivip-overview.md) são candidatos para fluxos de saída. Esta seleção não é configurável e deve considerar o algoritmo de seleção para ser aleatório. Pode designar um endereço IP específico para fluxos de saída, conforme descrito em [combinadas de vários cenários](#combinations).

### <a name="az"></a> As zonas de disponibilidade

Ao usar [Balanceador de carga Standard com zonas de disponibilidade](load-balancer-standard-availability-zones.md), front-ends com redundância de zona podem fornecer ligações SNAT de saída com redundância de zona e programação de SNAT sobrevive a falha de zona.  Quando são utilizados front-ends zonais, ligações SNAT de saída partilham destino com a zona que pertencem.

## <a name="snat"></a>Noções básicas sobre SNAT e PAT

### <a name="pat"></a>Porta Disfarce SNAT (PAT)

Quando um recurso de Balanceador de carga público é associado a instâncias de VM, cada origem de ligação de saída é reescrita. A origem é Reescrita do espaço de endereços IP privado da rede virtual para o endereço IP público do Balanceador de carga de front-end. No espaço de endereços IP públicos, a 5 cadeias de identificação do fluxo (endereço IP de origem, porta de origem, protocolo de transporte IP, endereço IP de destino, porta de destino) tem de ser exclusiva.  Porta SNAT de Disfarce pode ser utilizado com protocolos TCP ou UDP IP.

Portas efêmeras (portas SNAT) são utilizadas para conseguir isso depois de reescrever o endereço IP de origem privados, uma vez que vários fluxos provêm de um único endereço IP público. O algoritmo SNAT de Disfarce de porta aloca portas SNAT de forma diferente para UDP e TCP.

#### <a name="tcp"></a>Portas de SNAT de TCP

Uma porta SNAT é consumida por fluxo para um endereço IP de destino única porta. Para vários fluxos TCP para o mesmo endereço IP de destino, portas e protocolos, cada fluxo TCP consome uma única porta SNAT. Isto garante que os fluxos são exclusivos, quando são provêm do mesmo endereço IP público e vão para o mesmo endereço IP de destino, porta e protocolo. 

Vários fluxos, cada um para um endereço IP de destino diferente, porta e protocolo, partilham uma única porta SNAT. O endereço IP de destino, portas e protocolos que fluxos exclusivo sem a necessidade de origem adicionais portas para distinguir os fluxos no espaço de endereços IP público.

#### <a name="udp"></a> Portas de SNAT de UDP

Portas UDP SNAT são geridas por um algoritmo diferente do que as portas de SNAT de TCP.  Balanceador de carga utiliza um algoritmo conhecido como "restritas por porta cone NAT" para UDP.  Uma porta SNAT é consumida para cada fluxo, independentemente do endereço IP de destino, porta.

#### <a name="exhaustion"></a>Esgotamento

Quando os recursos de porta SNAT são esgotados, fluxos de saída falharem até que os fluxos existentes de portas SNAT de versão. Balanceador de carga reclama portas SNAT quando o fluxo fecha e utiliza um [tempo limite de inatividade de 4 minutos](#idletimeout) para reclamação portas SNAT de fluxos de inatividade.

Portas UDP SNAT geralmente esgotar muito mais rapidamente do que as portas de TCP SNAT devido uma diferença no algoritmo utilizado. Devem testar de design e o dimensionamento, com essa diferença em mente.

Padrões atenuar as condições que normalmente conduzem a exaustão de porta SNAT, reveja os [SNAT de gerir](#snatexhaust) secção.

### <a name="preallocatedports"></a>Pré-alocação de porta efêmera para a porta mascarando-o SNAT (PAT)

O Azure utiliza um algoritmo para determinar o número de pré-alocado SNAT portas disponíveis com base no tamanho do conjunto de back-end ao utilizar a porta SNAT de Disfarce ([dar um TAPINHA](#pat)). Portas SNAT são portas efêmeras disponíveis para um determinado endereço de origem IP público.

O mesmo número de portas SNAT é pré-alocado para UDP e TCP, respectivamente e consumido de forma independente por protocolo de transporte IP.  No entanto, a utilização de porta SNAT é diferente, dependendo do fluxo é UDP ou TCP.

>[!IMPORTANT]
>Padrão de SKU de SNAT de programação é por protocolo de transporte IP e deriva a regra de balanceamento de carga.  Se só existe uma regra de balanceamento de carga TCP, SNAT só está disponível para TCP. Se tiver apenas uma carga TCP regra de balanceamento e precisar de SNAT de saída para UDP, crie uma regra a partir do mesmo front-end para o mesmo conjunto de back-end de balanceamento de carga do UDP.  Isto irá acionar SNAT de programação para UDP.  Não é necessária uma sonda de estado de funcionamento ou a regra de trabalho.  SNAT de SKU básico programas sempre SNAT para ambos os protocolo de transporte IP, independentemente do protocolo de transporte especificado na regra de balanceamento de carga.

Azure preallocates SNAT de portas para a configuração de IP da NIC de cada VM. Quando uma configuração de IP é adicionada ao conjunto, as portas SNAT são pré-alocado para esta configuração de IP com base no tamanho do conjunto de back-end. Quando os fluxos de saída são criados, [dar um TAPINHA](#pat) dinamicamente consome (até ao limite preallocated) e as versões destas portas quando o fluxo for fechado ou [tempos limite de inatividade](#idletimeout) acontecer.

A tabela seguinte mostra os preallocations de porta SNAT para os escalões de tamanhos de conjuntos de back-end:

| Tamanho do conjunto (instâncias VM) | Portas SNAT pré-alocado por configuração de IP|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

>[!NOTE]
> Ao utilizar o Balanceador de carga Standard com [vários front-ends](load-balancer-multivip-overview.md), cada endereço IP de front-end multiplica o número de portas SNAT disponíveis na tabela anterior. Por exemplo, um conjunto de back-end de 50 VM com 2 carga regras de balanceamento, cada um com um endereço IP de front-end separado, irá utilizar as portas SNAT de 2048 (2 x 1024) por configuração de IP. Consulte os detalhes para [vários front-ends](#multife).

Lembre-se de que o número de portas SNAT disponíveis não traduz diretamente para o número de fluxos. Uma única porta SNAT pode ser reutilizada para vários destinos exclusivos. As portas são consumidas apenas se for necessário para que os fluxos exclusivo. Para obter orientações de design e mitigação, consulte a seção sobre [como gerir este recurso exhaustible](#snatexhaust) e a secção que descreve [dar um TAPINHA](#pat).

Alterar o tamanho do seu conjunto de back-end pode afetar alguns dos seus fluxos estabelecidos. Se o tamanho do conjunto de back-end aumenta e faz a transição para a próxima camada, metade das suas portas SNAT preallocated são recuperada durante a transição para a próxima camada de conjunto de back-end maior. Fluxos associados uma porta SNAT reclaimed serão o tempo limite e devem ser restabelecidos. Se um novo fluxo é tentado, o fluxo será bem sucedida imediatamente, desde que as portas pré-alocado estão disponíveis.

Se o tamanho do conjunto de back-end diminui e faz a transição para um escalão mais baixo, aumenta o número de portas SNAT disponíveis. Neste caso, existente alocadas SNAT portas e seus respectivos fluxos não são afetados.

Alocações de portas SNAT são específico de protocolo de transporte IP (TCP e UDP que e mantidó em separado) e são lançadas nas seguintes condições:

### <a name="tcp-snat-port-release"></a>Versão de porta de TCP SNAT

- Se ambos os servidor/cliente enviar FIN/ACK, porta SNAT será lançada após 240 segundos.
- Se um RST é visto, porta SNAT será lançada após 15 segundos.
- foi atingido o tempo limite de inatividade

### <a name="udp-snat-port-release"></a>Versão de porta de SNAT de UDP

- foi atingido o tempo limite de inatividade

## <a name="problemsolving"></a> Resolução de problemas 

Esta secção destina-se para ajudar a atenuar o esgotamento de SNAT e que pode ocorrer com ligações de saída no Azure.

### <a name="snatexhaust"></a> Gerir exaustão de porta de SNAT (PAT)
[Portas efêmeras](#preallocatedports) utilizado para [dar um TAPINHA](#pat) são um recurso de exhaustible, conforme descrito na [VM autónoma, sem um endereço IP público de nível de instância](#defaultsnat) e [com balanceamento de carga VM sem um Endereço IP público de nível de instância](#lb).

Se souber que está a iniciar muitos TCP ou UDP ligações de saída para o mesmo endereço IP de destino e porta e observar as ligações de saída a falhar ou aconselhado pelo suporte que está a esgotar portas SNAT (pré-alocado [efémeras portas](#preallocatedports) utilizada pelo [dar um TAPINHA](#pat)), tem várias opções de redução geral. Reveja estas opções e decidir o que está disponível e é melhor para seu cenário. É possível que um ou mais podem ajudar a lidar com esse cenário.

Se estiver a ter dificuldades para compreender o comportamento de ligação de saída, pode usar estatísticas de pilha IP (netstat). Ou pode ser útil observar comportamentos de ligação através de capturas de pacotes. Pode efetuar estas capturas de pacotes no SO convidado da sua instância ou usar [observador de rede de captura de pacotes](../network-watcher/network-watcher-packet-capture-manage-portal.md).

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
Atribuir um ILPIP altera o seu cenário para [IP público de nível de instância para uma VM](#ilpip). Todas as portas efêmeras de IP público que são utilizadas para cada VM estão disponíveis para a VM. (Em vez de cenários nos quais as portas efêmeras de um IP público são compartilhadas com todas as VMs associadas com o conjunto de back-end respectivos.) Existem vantagens e desvantagens a serem considerados, como o custo adicional de endereços IP públicos e o impacto potencial de listas de permissões de um grande número de endereços IP individuais.

>[!NOTE] 
>Esta opção não está disponível para funções de trabalho do web.

#### <a name="multifesnat"></a>Utilizar vários front-ends

Ao utilizar o Balanceador de carga Standard público, atribua [vários endereços IP de front-end para as ligações de saída](#multife) e [multiplique o número de portas SNAT disponíveis](#preallocatedports).  Crie uma configuração de IP de front-end, a regra e o conjunto de back-end para acionar a programação de SNAT ao IP público de front-end.  A regra não tem de funcionar e uma sonda de estado de funcionamento não precisa de ter êxito.  Se utilizar vários front-ends para entrada também (e não apenas para saída), deve usar as sondas de estado de funcionamento personalizado também para garantir a confiabilidade.

>[!NOTE]
>Na maioria dos casos, o esgotamento de portas SNAT é um sinal de um design inadequado.  Certifique-se de que compreende o motivo pelo qual são portas esgotar antes de utilizar mais de front-ends para adicionar as portas SNAT.  Pode ser a máscara de um problema que pode levar a falhas mais tarde.

#### <a name="scaleout"></a>Aumentar horizontalmente

[Pré-alocado portas](#preallocatedports) são atribuídas com base no tamanho do conjunto de back-end e agrupados em camadas para minimizar a interrupção quando algumas das portas tem de ser reatribuída para acomodar a próxima maior back-end conjunto tamanho camada.  Poderá ter uma opção para aumentar a intensidade da utilização de porta SNAT para um determinado front-end através do seu conjunto de back-end para o tamanho máximo para uma determinada camada de dimensionamento.  Isto requer para a aplicação aumentar horizontalmente de forma eficiente.

Por exemplo, duas máquinas virtuais no conjunto de back-end teria de portas SNAT de 1024 disponíveis por configuração de IP, permitindo que um total de SNAT de 2048 portas para a implementação.  Se a implementação fosse ser aumentado para 50 virtual máquinas, mesmo que o número de pré-alocado constante de permanece de portas por máquina virtual, um total de 51,200 (50 x 1024) portas SNAT podem ser utilizadas pela implantação.  Se pretender aumentar horizontalmente a implementação, verifique o número de [pré-alocado portas](#preallocatedports) por escalão certificar-se de formatar o aumento horizontal para o máximo para o respetivo escalão.  No exemplo anterior, se tinha escolhido para aumentar horizontalmente para 51 em vez de 50 instâncias, seria avançar para a próxima camada e de fim cópia de segurança com menos portas SNAT por VM, bem como no total.

Se aumentar horizontalmente para a próxima maior back-end conjunto tamanho camada, há possíveis para algumas das suas ligações de saída para o tempo limite se as portas alocadas tem sejam realocados.  Se estiver apenas a utilizar algumas das suas portas de SNAT, aumentar horizontalmente no próximo tamanho maior do conjunto back-end é irrelevante.  Metade as portas existentes, serão possível reatribuir sempre que se mover para a próxima camada de conjunto de back-end.  Se não quiser que isso aconteça, terá de moldar a sua implementação para o tamanho de escalão.  Ou certifique-se de que seu aplicativo pode detectar e repita conforme necessário.  TCP keepalives podem ajudá-lo em detetar quando o SNAT já não portas função devido a serem realocadas.

### <a name="idletimeout"></a>Utilizar keepalives para repor o tempo de limite de inatividade saído

As ligações de saída têm um tempo de limite de inatividade de 4 minutos. Este tempo limite não é ajustável. No entanto, pode utilizar o transporte (por exemplo, o TCP keepalives) ou keepalives de camada de aplicativo para atualizar um fluxo de inatividade e repor este tempo limite de inatividade, se necessário.  

Ao utilizar o TCP keepalives, é suficiente para ativá-las de um lado da ligação. Por exemplo, é suficiente para ativá-las no lado do servidor, apenas para repor o temporizador inativo do fluxo e não é necessário para ambos os lados iniciada keepalives TCP.  Conceitos similares existem para a camada de aplicação, incluindo configurações de cliente-servidor de base de dados.  Verifique o lado do servidor para que opções existem para keepalives específico do aplicativo.

## <a name="discoveroutbound"></a>Detetar o IP público que utiliza uma VM
Existem várias formas de determinar o endereço IP de origem público de uma ligação de saída. OpenDNS fornece um serviço que pode apresentar-lhe o endereço IP público da sua VM. 

Ao utilizar o comando nslookup, pode enviar uma consulta DNS para o myip.opendns.com de nome para o resolvedor OpenDNS. O serviço devolve o endereço IP de origem que foi utilizado para enviar a consulta. Quando executar a consulta seguinte da VM, a resposta é o IP público utilizado para essa VM:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Impedir a conectividade de saída
Por vezes, não é desejável para uma VM tenha permissão para criar um fluxo de saída. Ou talvez haja um requisito para gerir os destinos podem ser acedidos com fluxos de saída, ou que destinos podem começar a fluxos de entrada. Neste caso, pode usar [grupos de segurança de rede](../virtual-network/security-overview.md) para gerir os destinos que a VM pode entrar. Também pode utilizar NSGs para gerir o destino público pode iniciar fluxos de entrada.

Quando aplica um NSG a uma VM com balanceamento de carga, preste atenção para o [etiquetas de serviço](../virtual-network/security-overview.md#service-tags) e [regras de segurança predefinidas](../virtual-network/security-overview.md#default-security-rules). Tem de se certificar de que a VM pode receber pedidos de sonda de estado de funcionamento do Balanceador de carga do Azure. 

Se um NSG bloquear pedidos de sonda de estado de funcionamento de etiqueta de predefinida AZURE_LOADBALANCER, a sonda de estado de funcionamento da VM falha e a VM está marcada para baixo. Balanceador de carga deixará de enviar novos fluxos para essa VM.

## <a name="limitations"></a>Limitações
- DisableOutboundSnat não está disponível como uma opção quando configurar uma regra no portal de balanceamento de carga.  Utilize ferramentas REST, modelo ou do cliente em vez disso.
- Funções de trabalho de Web sem uma VNet e a outros serviços de plataforma da Microsoft pode ser acessíveis quando apenas um Standard Balanceador de carga interno é utilizado devido a um efeito colateral de como os serviços de pré-VNet e outra plataforma dos serviços de função. Não confie neste efeito colateral como o próprio serviço respectivo ou a plataforma subjacente pode ser alterada sem aviso prévio. Sempre deve supor que tem de criar explicitamente a conectividade de saída se assim o desejar quando utilizar um Standard Balanceador de carga interno apenas. O [predefinido SNAT](#defaultsnat) cenário 3 descrito neste artigo não está disponível.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais o [Balanceador de Carga Standard](load-balancer-standard-overview.md).
- Saiba mais sobre [regras de saída](load-balancer-outbound-rules-overview.md) para Balanceador de carga Standard público.
- Saiba mais sobre [Balanceador de carga](load-balancer-overview.md).
- Saiba mais sobre [grupos de segurança de rede](../virtual-network/security-overview.md).
- Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) no Azure.
