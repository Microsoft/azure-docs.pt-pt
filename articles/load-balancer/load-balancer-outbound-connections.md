---
title: Ligações de saída no Azure
titleSuffix: Azure Load Balancer
description: Este artigo explica como o Azure permite que as VMs se comuniquem com os serviços de Internet públicos.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 5bdcd955919a91760f16287a62956542cfaa47c5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225288"
---
# <a name="outbound-connections-in-azure"></a>Ligações de saída no Azure

O Azure fornece conectividade de saída para implantações de clientes por meio de vários mecanismos diferentes. Este artigo descreve o que são os cenários, quando eles se aplicam, como eles funcionam e como gerenciá-los.

>[!NOTE] 
>Este artigo aborda apenas as implantações do Gerenciador de recursos. Revise as [conexões de saída (clássicas)](load-balancer-outbound-connections-classic.md) para todos os cenários de implantação clássicos no Azure.

Uma implantação no Azure pode se comunicar com pontos de extremidade fora do Azure no espaço de endereço IP público. Quando uma instância inicia um fluxo de saída para um destino no espaço de endereço IP público, o Azure mapeia dinamicamente o endereço IP privado para um endereço IP público. Depois que esse mapeamento é criado, o tráfego de retorno para esse fluxo originado de saída também pode alcançar o endereço IP privado onde o fluxo foi originado.

O Azure usa SNAT (conversão de endereços de rede de origem) para executar essa função. Quando vários endereços IP privados estão sendo mascarados por trás de um único endereço IP público, o Azure usa a [Pat (conversão de endereços de porta)](#pat) para mascarar endereços IP privados. As portas efêmeras são usadas para PAT e são [prealocadas](#preallocatedports) com base no tamanho do pool.

Há vários [cenários de saída](#scenarios). Você pode combinar esses cenários conforme necessário. Revise-os cuidadosamente para entender os recursos, as restrições e os padrões conforme eles se aplicam ao seu modelo de implantação e cenário de aplicativo. Examine as diretrizes para [gerenciar esses cenários](#snatexhaust).

>[!IMPORTANT] 
>Standard Load Balancer e IP público Standard introduzem novas capacidades e comportamentos diferentes para a conectividade de saída.  Eles não são os mesmos que os SKUs básicos.  Se você quiser a conectividade de saída ao trabalhar com SKUs padrão, deverá defini-la explicitamente com endereços IP públicos padrão ou Load Balancer públicos padrão.  Isso inclui a criação de conectividade de saída ao usar um Standard Load Balancer interno.  Recomendamos que você sempre use regras de saída em um Load Balancer público padrão.  O [cenário 3](#defaultsnat) não está disponível com o SKU Standard.  Isso significa que quando um Standard Load Balancer interno é usado, você precisa tomar medidas para criar a conectividade de saída para as VMs no pool de back-end se a conectividade de saída for desejada.  No contexto de conectividade de saída, uma única VM autônoma, todas as VMs em um conjunto de disponibilidade, todas as instâncias em um VMSS se comportam como um grupo. Isso significa que, se uma única VM em um conjunto de disponibilidade estiver associada a um SKU Standard, todas as instâncias de VM nesse conjunto de disponibilidade agora se comportarão pelas mesmas regras como se elas fossem associadas ao SKU Standard, mesmo se uma instância individual não estiver diretamente associada a ela.  Examine atentamente este documento inteiro para entender os conceitos gerais, examine [Standard Load Balancer](load-balancer-standard-overview.md) as diferenças entre os SKUs e examine [as regras de saída](load-balancer-outbound-rules-overview.md).  O uso de regras de saída permite um controle refinado sobre todos os aspectos da conectividade de saída.

## <a name="scenarios"></a>Visão geral do cenário

Azure Load Balancer e recursos relacionados são definidos explicitamente quando você está usando [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Atualmente, o Azure fornece três métodos diferentes para obter conectividade de saída para Azure Resource Manager recursos. 

| SKUs | Cenário | Método | Protocolos IP | Descrição |
| --- | --- | --- | --- | --- |
| Standard, básico | [1. VM com endereço IP público (com ou sem Load Balancer)](#ilpip) | SNAT, mascaramento de porta não usado | TCP, UDP, ICMP, ESP | O Azure usa o IP público atribuído à configuração de IP da NIC da instância. A instância tem todas as portas efêmeras disponíveis. Ao usar Standard Load Balancer, você deve usar [regras de saída](load-balancer-outbound-rules-overview.md) para definir explicitamente a conectividade de saída |
| Standard, básico | [2. Load Balancer público associado a uma VM (nenhum endereço IP público na instância)](#lb) | SNAT com PAT (disfarce de porta) usando os front-ends Load Balancer | TCP, UDP |O Azure compartilha o endereço IP público dos front-ends públicos Load Balancer com vários endereços IP privados. O Azure usa portas efêmeras dos front-ends para PAT. |
| nenhum ou básico | [3. VM autônoma (sem Load Balancer, nenhum endereço IP público)](#defaultsnat) | SNAT com disfarce de porta (PAT) | TCP, UDP | O Azure designa automaticamente um endereço IP público para SNAT, compartilha esse endereço IP público com vários endereços IP privados do conjunto de disponibilidade e usa portas efêmeras desse endereço IP público. Esse cenário é um fallback para os cenários anteriores. Não é recomendável se você precisar de visibilidade e controle. |

Se você não quiser que uma VM se comunique com pontos de extremidade fora do Azure no espaço de endereço IP público, você poderá usar NSGs (grupos de segurança de rede) para bloquear o acesso conforme necessário. A seção que [impede a conectividade de saída](#preventoutbound) discute NSGs mais detalhadamente. Orientação sobre como projetar, implementar e gerenciar uma rede virtual sem nenhum acesso de saída está fora do escopo deste artigo.

### <a name="ilpip"></a>Cenário 1: VM com endereço IP público

Nesse cenário, a VM tem um IP público atribuído a ela. No que diz respeito a conexões de saída, não importa se a VM tem balanceamento de carga ou não. Esse cenário tem precedência sobre os outros. Quando um endereço IP público é usado, a VM usa o endereço IP público para todos os fluxos de saída.  

Um IP público atribuído a uma VM é uma relação 1:1 (em vez de 1: muitos) e implementada como um NAT 1:1 sem estado.  PAT (mascaramento de porta) não é usado e a VM tem todas as portas efêmeras disponíveis para uso.

Se seu aplicativo iniciar muitos fluxos de saída e você enfrentar esgotamento de porta SNAT, considere atribuir um [endereço IP público para atenuar as restrições SNAT](#assignilpip). Examine o [Gerenciamento do esgotamento de SNAT](#snatexhaust) em sua totalidade.

### <a name="lb"></a>Cenário 2: VM com balanceamento de carga sem um endereço IP público

Nesse cenário, a VM faz parte de um pool de back-end público Load Balancer. A VM não tem um endereço IP público atribuído a ela. O recurso de Load Balancer deve ser configurado com uma regra de balanceador de carga para criar um vínculo entre o front-end IP público com o pool de back-end.

Se você não concluir essa configuração de regra, o comportamento será conforme descrito no cenário para [VM autônoma sem IP público](#defaultsnat). Não é necessário que a regra tenha um ouvinte de trabalho no pool de back-end para que a investigação de integridade tenha sucesso.

Quando a VM com balanceamento de carga cria um fluxo de saída, o Azure converte o endereço IP de origem privado do fluxo de saída para o endereço IP público do front-end público de Load Balancer. O Azure usa SNAT para executar essa função. O Azure também usa [Pat](#pat) para mascarar vários endereços IP privados por trás de um endereço IP público. 

As portas efêmeras do front-end do endereço IP público do balanceador de carga são usadas para distinguir fluxos individuais originados pela VM. O SNAT usa dinamicamente [portas efêmeras prealocadas](#preallocatedports) quando fluxos de saída são criados. Nesse contexto, as portas efêmeras usadas para SNAT são chamadas de portas SNAT.

As portas SNAT são previamente alocadas conforme descrito na seção [entendendo SNAT e Pat](#snat) . Eles são um recurso finito que pode ser esgotado. É importante entender como eles são [consumidos](#pat). Para entender como projetar para esse consumo e mitigar conforme necessário, examine o [Gerenciamento do esgotamento de SNAT](#snatexhaust).

Quando [vários endereços IP públicos são associados ao Load Balancer Basic](load-balancer-multivip-overview.md), qualquer um desses endereços IP públicos é um candidato para fluxos de saída e um é selecionado aleatoriamente.  

Para monitorar a integridade das conexões de saída com o Load Balancer Basic, você pode usar [logs de Azure monitor para Load Balancer](load-balancer-monitor-log.md) e [logs de eventos de alerta](load-balancer-monitor-log.md#alert-event-log) para monitorar mensagens de esgotamento de porta SNAT.

### <a name="defaultsnat"></a>Cenário 3: VM autônoma sem um endereço IP público

Nesse cenário, a VM não faz parte de um pool de Load Balancer público (e não faz parte de um pool de Standard Load Balancer interno) e não tem um endereço IP público atribuído a ele. Quando a VM cria um fluxo de saída, o Azure converte o endereço IP de origem privado do fluxo de saída para um endereço IP de origem público. O endereço IP público usado para esse fluxo de saída não é configurável e não conta com relação ao limite de recursos de IP público da assinatura. Esse endereço IP público não pertence a você e não pode ser reservado. Se você reimplantar a VM ou conjunto de disponibilidade ou conjunto de dimensionamento de máquinas virtuais, esse endereço IP público será liberado e um novo endereço IP público solicitado. Não use esse cenário para endereços IP de lista de permissões. Em vez disso, use um dos outros dois cenários em que você declara explicitamente o cenário de saída e o endereço IP público a ser usado para a conectividade de saída.

>[!IMPORTANT] 
>Esse cenário também se aplica quando __apenas__ um Load Balancer básico interno é anexado. O cenário 3 __não está disponível__ quando um Standard Load balancer interno é anexado a uma VM.  Você deve criar explicitamente o [cenário 1](#ilpip) ou [cenário 2](#lb) , além de usar um Standard Load balancer interno.

O Azure usa SNAT com disfarce de porta ([Pat](#pat)) para executar essa função. Esse cenário é semelhante ao [cenário 2](#lb), exceto que não há nenhum controle sobre o endereço IP usado. Esse é um cenário de fallback para quando os cenários 1 e 2 não existem. Não recomendamos esse cenário se você quiser ter controle sobre o endereço de saída. Se as conexões de saída forem uma parte crítica do seu aplicativo, você deverá escolher outro cenário.

As portas SNAT são prealocadas conforme descrito na seção [entendendo SNAT e Pat](#snat) .  O número de VMs que compartilham um conjunto de disponibilidade determina qual camada de prealocação se aplica.  Uma VM autônoma sem um conjunto de disponibilidade é efetivamente um pool de 1 para fins de determinação de prealocação (portas SNAT 1024). As portas SNAT são um recurso finito que pode ser esgotado. É importante entender como eles são [consumidos](#pat). Para entender como projetar para esse consumo e mitigar conforme necessário, examine o [Gerenciamento do esgotamento de SNAT](#snatexhaust).

### <a name="combinations"></a>Vários cenários combinados

Você pode combinar os cenários descritos nas seções anteriores para obter um resultado específico. Quando vários cenários estão presentes, uma ordem de precedência se aplica: o [cenário 1](#ilpip) tem precedência sobre o [cenário 2](#lb) e [3](#defaultsnat). O [cenário 2](#lb) substitui o [cenário 3](#defaultsnat).

Um exemplo é uma implantação Azure Resource Manager em que o aplicativo depende muito das conexões de saída para um número limitado de destinos, mas também recebe fluxos de entrada em um front-end Load Balancer. Nesse caso, você pode combinar os cenários 1 e 2 para o alívio. Para obter padrões adicionais, examine [Gerenciando esgotamento de SNAT](#snatexhaust).

### <a name="multife"></a>Vários front-ends para fluxos de saída

#### <a name="standard-load-balancer"></a>Balanceador de Carga Standard

Standard Load Balancer usa todos os candidatos para fluxos de saída ao mesmo tempo em que [vários front-ends de IP (público)](load-balancer-multivip-overview.md) estão presentes. Cada front-end multiplica o número de portas SNAT prealocadas disponíveis se uma regra de balanceamento de carga estiver habilitada para conexões de saída.

Você pode optar por suprimir a utilização de um endereço IP de front-end para conexões de saída com uma nova opção de regra de balanceamento de carga:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Normalmente, a opção `disableOutboundSnat` padrão é _false_ e significa que essa regra programa SNAT de saída para as VMs associadas no pool de back-end da regra de balanceamento de carga. O `disableOutboundSnat` pode ser alterado para _true_ para impedir que Load Balancer use o endereço IP de front-end associado para conexões de saída para as VMs no pool de back-end dessa regra de balanceamento de carga.  E você também pode ainda designar um endereço IP específico para fluxos de saída, conforme descrito em [vários cenários combinados](#combinations) também.

#### <a name="load-balancer-basic"></a>Load Balancer Basic

Load Balancer Basic escolhe um único front-end a ser usado para fluxos de saída quando [vários front-ends de IP (público)](load-balancer-multivip-overview.md) forem candidatos para fluxos de saída. Essa seleção não é configurável e você deve considerar o algoritmo de seleção como aleatório. Você pode designar um endereço IP específico para fluxos de saída, conforme descrito em [vários cenários combinados](#combinations).

### <a name="az"></a>Zonas de Disponibilidade

Ao usar [Standard Load Balancer com zonas de disponibilidade](load-balancer-standard-availability-zones.md), front-ends com redundância de zona podem fornecer conexões de SNAT de saída com redundância de zona e a programação SNAT sobreviver à falha de zona.  Quando front-ends zonais são usados, conexões SNAT de saída compartilham destino com a zona à qual pertencem.

## <a name="snat"></a>Compreendendo o SNAT e o PAT

### <a name="pat"></a>SNAT de mascaramento de porta (PAT)

Quando um recurso de Load Balancer público é associado a instâncias de VM, cada fonte de conexão de saída é reescrita. A origem é reescrita do espaço de endereço IP privado da rede virtual para o endereço IP público de front-end do balanceador de carga. No espaço de endereço IP público, a 5 tupla do fluxo (endereço IP de origem, porta de origem, protocolo de transporte IP, endereço IP de destino, porta de destino) deve ser exclusiva.  A representação de porta de SNAT pode ser usada com protocolos IP TCP ou UDP.

As portas efêmeras (portas SNAT) são usadas para fazer isso após a regravação do endereço IP de origem particular, pois vários fluxos se originam de um único endereço IP público. O algoritmo SNAT de mascaramento de porta aloca portas SNAT de forma diferente para UDP versus TCP.

#### <a name="tcp"></a>Portas TCP SNAT

Uma porta SNAT é consumida por fluxo para um único endereço IP de destino, porta. Para vários fluxos TCP para o mesmo endereço IP, porta e protocolo de destino, cada fluxo de TCP consome uma única porta SNAT. Isso garante que os fluxos sejam exclusivos quando eles se originam do mesmo endereço IP público e vão para o mesmo endereço IP de destino, porta e protocolo. 

Vários fluxos, cada um para um endereço IP de destino, porta e protocolo diferentes, compartilham uma única porta SNAT. O endereço IP de destino, a porta e o protocolo tornam os fluxos exclusivos sem a necessidade de portas de origem adicionais para distinguir os fluxos no espaço de endereço IP público.

#### <a name="udp"></a>Portas de SNAT UDP

As portas SNAT UDP são gerenciadas por um algoritmo diferente do que as portas SNAT TCP.  Load Balancer usa um algoritmo conhecido como "NAT de cone restrito por porta" para UDP.  Uma porta SNAT é consumida para cada fluxo, independentemente do endereço IP de destino, da porta.

#### <a name="snat-port-reuse"></a>Reutilização de porta SNAT

Depois que uma porta for liberada, a porta estará disponível para reutilização conforme necessário.  Você pode considerar as portas SNAT como uma sequência do mais baixo para o mais alto disponível para um determinado cenário, e a primeira porta SNAT disponível é usada para novas conexões. 
 
#### <a name="exhaustion"></a>Esgotamento

Quando os recursos de porta SNAT são esgotados, os fluxos de saída falham até que os fluxos existentes liberem portas SNAT. Load Balancer recupera portas SNAT quando o fluxo fecha e usa um [tempo limite de ociosidade de 4 minutos](#idletimeout) para recuperar portas SNAT de fluxos ociosos.

As portas SNAT UDP geralmente esgotam muito mais rápido que as portas SNAT TCP devido à diferença no algoritmo usado. Você deve projetar e dimensionar o teste com essa diferença em mente.

Para padrões para mitigar condições que geralmente levam ao esgotamento de porta SNAT, examine a seção [Managing SNAT](#snatexhaust) .

### <a name="preallocatedports"></a>Prealocação de porta efêmera para a porta de disfarce SNAT (PAT)

O Azure usa um algoritmo para determinar o número de portas SNAT pré-alocado disponíveis com base no tamanho do pool de back-end ao usar[Pat](#pat)(memória disfarçada SNAT). As portas SNAT são portas efêmeras disponíveis para um endereço de origem IP público específico.

O mesmo número de portas SNAT é pré-alocado para UDP e TCP, respectivamente, e consumido independentemente por protocolo de transporte IP.  No entanto, o uso da porta SNAT é diferente dependendo se o fluxo é UDP ou TCP.

>[!IMPORTANT]
>A programação SNAT de SKU Standard é por protocolo de transporte IP e derivada da regra de balanceamento de carga.  Se houver apenas uma regra de balanceamento de carga TCP, o SNAT só estará disponível para TCP. Se você tiver apenas uma regra de balanceamento de carga TCP e precisar de SNAT de saída para UDP, crie uma regra de balanceamento de carga UDP do mesmo front-end para o mesmo pool de back-end.  Isso irá disparar a programação SNAT para UDP.  Uma regra de trabalho ou investigação de integridade não é necessária.  O SNAT de SKU básico sempre programa o SNAT para o protocolo de transporte IP, independentemente do protocolo de transporte especificado na regra de balanceamento de carga.

O Azure prealoca portas SNAT para a configuração de IP da NIC de cada VM. Quando uma configuração de IP é adicionada ao pool, as portas SNAT são prealocadas para essa configuração de IP com base no tamanho do pool de back-end. Quando os fluxos de saída são criados, [Pat](#pat) consome dinamicamente (até o limite prealocado) e libera essas portas quando o fluxo fecha ou os [tempos limite de ociosidade](#idletimeout) acontecem.

A tabela a seguir mostra as prealocações de porta SNAT para camadas de tamanhos de pool de back-end:

| Tamanho do pool (instâncias de VM) | Portas SNAT prealocadas por configuração de IP|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

>[!NOTE]
> Ao usar Standard Load Balancer com [vários front-ends](load-balancer-multivip-overview.md), cada endereço IP de front-end multiplica o número de portas SNAT disponíveis na tabela anterior. Por exemplo, um pool de back-end de VMs 50 com 2 regras de balanceamento de carga, cada uma com um endereço IP de front-end separado, usará as portas SNAT 2048 (2x 1024) por configuração de IP. Veja os detalhes de [vários front-ends](#multife).

Lembre-se de que o número de portas SNAT disponíveis não é convertido diretamente no número de fluxos. Uma única porta SNAT pode ser reutilizada para vários destinos exclusivos. As portas são consumidas apenas se for necessário tornar os fluxos exclusivos. Para obter diretrizes de design e mitigação, consulte a seção sobre [como gerenciar esse recurso esse esgotável](#snatexhaust) e a seção que descreve [Pat](#pat).

Alterar o tamanho do pool de back-end pode afetar alguns dos fluxos estabelecidos. Se o tamanho do pool de back-end aumentar e fizer a transição para a próxima camada, metade das portas SNAT prealocadas serão recuperadas durante a transição para a próxima camada maior do pool de back-end. Os fluxos associados a uma porta SNAT recuperada atingirão o tempo limite e deverão ser restabelecidos. Se um novo fluxo for tentado, o fluxo terá sucesso imediatamente, desde que as portas prealocadas estejam disponíveis.

Se o tamanho do pool de back-end diminuir e fizer a transição para uma camada inferior, o número de portas SNAT disponíveis aumentará. Nesse caso, as portas SNAT alocadas existentes e seus respectivos fluxos não são afetados.

As alocações de portas SNAT são o protocolo de transporte IP específico (TCP e UDP são mantidos separadamente) e são liberadas sob as seguintes condições:

### <a name="tcp-snat-port-release"></a>Versão da porta TCP SNAT

- Se o servidor/cliente enviar FINACK, a porta SNAT será liberada após 240 segundos.
- Se um RST for visto, a porta SNAT será liberada após 15 segundos.
- Se o tempo limite de ociosidade tiver sido atingido, a porta será liberada.

### <a name="udp-snat-port-release"></a>Versão da porta TCP SNAT

- Se o tempo limite de ociosidade tiver sido atingido, a porta será liberada.

## <a name="problemsolving"></a>Solução de problemas 

Esta seção destina-se a ajudar a mitigar a exaustão de SNAT e que pode ocorrer com conexões de saída no Azure.

### <a name="snatexhaust"></a>Gerenciando esgotamento de porta SNAT (PAT)
[As portas efêmeras](#preallocatedports) usadas [para Pat](#pat) são um recurso esse esgotável, conforme descrito em [VM autônoma sem um endereço IP público](#defaultsnat) e [VM com balanceamento de carga sem um endereço IP público](#lb).

Se você souber que está iniciando muitas conexões TCP ou UDP de saída para a mesma porta e endereço IP de destino e observar conexões de saída com falha ou for avisado pelo suporte de que você está esgotando portas SNAT ( [portas efêmeras](#preallocatedports) prealocadas usadas pela [Pat](#pat)), você tem várias opções gerais de mitigação. Examine essas opções e decida o que está disponível e melhor para seu cenário. É possível que um ou mais possam ajudar a gerenciar esse cenário.

Se você estiver tendo problemas para entender o comportamento da conexão de saída, você pode usar estatísticas de pilha de IP (netstat). Ou pode ser útil observar comportamentos de conexão usando capturas de pacote. Você pode executar essas capturas de pacote no SO convidado da sua instância ou usar o [observador de rede para captura de pacote](../network-watcher/network-watcher-packet-capture-manage-portal.md).

#### <a name="connectionreuse"></a>Modificar o aplicativo para reutilizar conexões 
Você pode reduzir a demanda de portas efêmeras que são usadas para SNAT Reutilizando conexões em seu aplicativo. Isso é especialmente verdadeiro para protocolos como HTTP/1.1, onde a reutilização de conexão é o padrão. E outros protocolos que usam HTTP como seu transporte (por exemplo, REST) podem se beneficiar por vez. 

A reutilização é sempre melhor do que as conexões TCP individuais e atômicas para cada solicitação. Reutilizar os resultados em transações TCP muito eficientes e de alto desempenho.

#### <a name="connection pooling"></a>Modificar o aplicativo para usar o pool de conexões
Você pode empregar um esquema de pooling de conexão em seu aplicativo, onde as solicitações são distribuídas internamente em um conjunto fixo de conexões (cada uma reutilização sempre que possível). Esse esquema restringe o número de portas efêmeras em uso e cria um ambiente mais previsível. Esse esquema também pode aumentar a taxa de transferência de solicitações, permitindo várias operações simultâneas quando uma única conexão está bloqueando a resposta de uma operação.  

Talvez o pool de conexões já exista na estrutura que você está usando para desenvolver seu aplicativo ou as definições de configuração para seu aplicativo. Você pode combinar o pool de conexões com a reutilização de conexão. Em seguida, as várias solicitações consomem um número fixo e previsível de portas para a mesma porta e endereço IP de destino. As solicitações também se beneficiam do uso eficiente de transações de TCP, reduzindo a latência e a utilização de recursos. As transações UDP também podem se beneficiar, pois gerenciar o número de fluxos UDP pode, por sua vez, evitar condições de esgotamento e gerenciar a utilização da porta SNAT.

#### <a name="retry logic"></a>Modificar o aplicativo para usar uma lógica de repetição menos agressiva
Quando [as portas efêmeras prealocadas](#preallocatedports) usadas para [Pat](#pat) são esgotadas ou ocorrem falhas de aplicativo, novas tentativas agressivas ou de força bruta sem decaimento e retirada lógica causam a ocorrência de esgotamento ou persistência. Você pode reduzir a demanda de portas efêmeras usando uma lógica de repetição menos agressiva. 

As portas efêmeras têm um tempo limite de ociosidade de 4 minutos (não ajustável). Se as repetições forem muito agressivas, o esgotamento não terá oportunidade de se limpar por conta própria. Portanto, considerar como--e com que frequência-o aplicativo repete transações é uma parte crítica do design.

#### <a name="assignilpip"></a>Atribuir um IP público a cada VM
A atribuição de um endereço IP público altera seu cenário para o [IP público para uma VM](#ilpip). Todas as portas efêmeras do IP público que são usadas para cada VM estão disponíveis para a VM. (Em oposição aos cenários em que as portas efêmeras de um IP público são compartilhadas com todas as VMs associadas ao respectivo pool de back-end.) Há compensações a serem consideradas, como o custo adicional de endereços IP públicos e o possível impacto da lista de permissões de um grande número de endereços IP individuais.

>[!NOTE] 
>Essa opção não está disponível para funções de Web Worker.

#### <a name="multifesnat"></a>Usar vários front-ends

Ao usar Standard Load Balancer públicas, você atribui [vários endereços IP de front-end para conexões de saída](#multife) e [multiplica o número de portas SNAT disponíveis](#preallocatedports).  Crie uma configuração de IP de front-end, uma regra e um pool de back-end para disparar a programação de SNAT para o IP público do front-end.  A regra não precisa funcionar e uma investigação de integridade não precisa ter sucesso.  Se você usar vários front-ends para entrada também (em vez de apenas para saída), deverá usar as investigações de integridade personalizadas para garantir a confiabilidade.

>[!NOTE]
>Na maioria dos casos, o esgotamento de portas SNAT é um sinal de design inadequado.  Certifique-se de entender por que você está esgotando portas antes de usar mais front-ends para adicionar portas SNAT.  Você pode estar mascarando um problema que pode causar falha mais tarde.

#### <a name="scaleout"></a>Escalar horizontalmente

[As portas prealocadas](#preallocatedports) são atribuídas com base no tamanho do pool de back-end e agrupadas em camadas para minimizar a interrupção quando algumas das portas precisam ser realocadas para acomodar a próxima camada de tamanho de pool de back-end maior.  Você pode ter uma opção para aumentar a intensidade da utilização da porta SNAT para um determinado frontend, dimensionando seu pool de back-end para o tamanho máximo de uma determinada camada.  Isso exige que o aplicativo seja expandido com eficiência.

Por exemplo, duas máquinas virtuais no pool de back-end teriam 1024 portas SNAT disponíveis por configuração de IP, permitindo um total de 2048 portas SNAT para a implantação.  Se a implantação fosse aumentada para 50 máquinas virtuais, embora o número de portas prealocadas permaneça constante por máquina virtual, um total de 51.200 (50 x 1024) portas SNAT podem ser usadas pela implantação.  Se você quiser escalar horizontalmente sua implantação, verifique o número de [portas prealocadas](#preallocatedports) por camada para certificar-se de Formatar sua escala para o máximo para a respectiva camada.  No exemplo anterior, se você optou por escalar horizontalmente para 51 em vez de 50 instâncias, progrediria para a próxima camada e terminaria com menos portas SNAT por VM, bem como no total.

Se você escalar horizontalmente para a próxima camada de tamanho de pool de back-end maior, haverá a possibilidade de algumas das suas conexões de saída atingirem o tempo limite se as portas alocadas tiverem que ser realocadas.  Se você estiver usando apenas algumas de suas portas SNAT, a expansão no próximo tamanho de pool de back-end maior será irrelevante.  Metade das portas existentes serão realocadas sempre que você passar para a próxima camada de pool de back-end.  Se não quiser que isso ocorra, você precisará Formatar sua implantação para o tamanho da camada.  Ou certifique-se de que seu aplicativo possa detectar e tentar novamente conforme necessário.  Os TCP keepalives podem auxiliar na detecção quando as portas SNAT não funcionam mais devido a serem realocadas.

### <a name="idletimeout"></a>Usar keepalives para redefinir o tempo limite de ociosidade de saída

As conexões de saída têm um tempo limite de ociosidade de 4 minutos. Esse tempo limite não é ajustável. No entanto, você pode usar o transporte (por exemplo, TCP keepalives) ou keepalives da camada de aplicativo para atualizar um fluxo ocioso e redefinir esse tempo limite ocioso, se necessário.  

Ao usar o TCP keepalives, é suficiente habilitá-los em um lado da conexão. Por exemplo, é suficiente habilitá-los no lado do servidor apenas para redefinir o timer de ociosidade do fluxo e não é necessário que ambos os lados iniciem o TCP keepalives.  Existem conceitos semelhantes para a camada de aplicativo, incluindo as configurações do servidor de cliente-banco de dados.  Verifique o lado do servidor para saber quais opções existem para keepalives específicas do aplicativo.

## <a name="discoveroutbound"></a>Descobrindo o IP público que uma VM usa
Há várias maneiras de determinar o endereço IP de origem público de uma conexão de saída. O OpenDNS fornece um serviço que pode mostrar o endereço IP público de sua VM. 

Usando o comando Nslookup, você pode enviar uma consulta DNS para o nome myip.opendns.com para o resolvedor OpenDNS. O serviço retorna o endereço IP de origem que foi usado para enviar a consulta. Quando você executa a consulta a seguir de sua VM, a resposta é o IP público usado para essa VM:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Impedindo a conectividade de saída
Às vezes, é indesejável que uma VM tenha permissão para criar um fluxo de saída. Ou pode haver um requisito para gerenciar quais destinos podem ser alcançados com fluxos de saída ou quais destinos podem iniciar fluxos de entrada. Nesse caso, você pode usar [grupos de segurança de rede](../virtual-network/security-overview.md) para gerenciar os destinos que a VM pode alcançar. Você também pode usar o NSGs para gerenciar qual destino público pode iniciar fluxos de entrada.

Ao aplicar um NSG a uma VM com balanceamento de carga, preste atenção às [marcas de serviço](../virtual-network/security-overview.md#service-tags) e às [regras de segurança padrão](../virtual-network/security-overview.md#default-security-rules). Você deve garantir que a VM possa receber solicitações de investigação de integridade de Azure Load Balancer. 

Se um NSG bloquear as solicitações de investigação de integridade da AZURE_LOADBALANCER marca padrão, a investigação de integridade da VM falhará e a VM será marcada como inativa. Load Balancer para o envio de novos fluxos para essa VM.

## <a name="limitations"></a>Limitações
- DisableOutboundSnat não está disponível como uma opção ao configurar uma regra de balanceamento de carga no Portal.  Em vez disso, use as ferramentas REST, modelo ou cliente.
- As funções de Web Worker sem uma VNet e outros serviços de plataforma da Microsoft podem ser acessíveis quando apenas um Standard Load Balancer interno é usado devido a um efeito colateral de como os serviços de VNet e outros serviços de plataforma funcionam. Não confie nesse efeito colateral, pois o respectivo próprio serviço ou a plataforma subjacente poderá mudar sem aviso prévio. Você deve sempre supor que precisará criar a conectividade de saída explicitamente, se desejado, ao usar apenas um Standard Load Balancer interno. O cenário de [SNAT 3 padrão](#defaultsnat) descrito neste artigo não está disponível.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais o [Balanceador de Carga Standard](load-balancer-standard-overview.md).
- Saiba mais sobre [as regras de saída](load-balancer-outbound-rules-overview.md) para Load Balancer públicas padrão.
- Saiba mais sobre [Load Balancer](load-balancer-overview.md).
- Saiba mais sobre [grupos de segurança de rede](../virtual-network/security-overview.md).
- Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) no Azure.
