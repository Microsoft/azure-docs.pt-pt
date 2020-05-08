---
title: Ligações de saída no Azure
titleSuffix: Azure Load Balancer
description: Este artigo explica como o Azure permite que os VMs se comuniquem com os serviços públicos de internet.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 80da8d2880509a8ed6a2af8cb181b3bc2c281c09
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930578"
---
# <a name="outbound-connections-in-azure"></a>Ligações de saída no Azure

O Azure fornece conectividade de saída para implementações de clientes através de vários mecanismos diferentes. Este artigo descreve quais são os cenários, quando se aplicam, como funcionam e como geri-los.

>[!NOTE] 
>Este artigo abrange apenas as implementações do Gestor de Recursos. Reveja [as ligações de saída (Classic)](load-balancer-outbound-connections-classic.md) para todos os cenários clássicos de implantação em Azure.

Uma implantação em Azure pode comunicar com pontos finais fora de Azure no espaço público de endereçoip. Quando uma instância inicia um fluxo de saída para um destino no espaço público de endereços IP, o Azure mapeia dinamicamente o endereço IP privado para um endereço IP público. Após a criação deste mapeamento, o tráfego de retorno para este fluxo originado de saída também pode chegar ao endereço IP privado onde o fluxo originou.

O Azure utiliza a tradução de endereços de rede fonte (SNAT) para executar esta função. Quando vários endereços IP privados estão disfarçados atrás de um único endereço IP público, o Azure utiliza a tradução de endereços de [porta (PAT)](#pat) para mascarar endereços IP privados. As portas efémeras são utilizadas para PAT e são [preallocalizadas](#preallocatedports) com base no tamanho da piscina.

Existem [vários cenários de saída.](#scenarios) Pode combinar estes cenários conforme necessário. Reveja-os cuidadosamente para compreender as capacidades, constrangimentos e padrões que se aplicam ao seu modelo de implementação e cenário de aplicação. Rever orientações para [a gestão destes cenários.](#snatexhaust)

>[!IMPORTANT] 
>Standard Load Balancer e Standard Public IP introduzem novas habilidades e diferentes comportamentos para a conectividade de saída.  Não são os mesmos que as SKUs básicas.  Se pretender conectividade de saída ao trabalhar com as SKUs Standard, deve defini-la explicitamente com endereços IP públicos padrão ou um balancer de carga público padrão.  Isto inclui a criação de conectividade de saída ao utilizar um Balancer de Carga Padrão interno.  Recomendamos que utilize sempre regras de saída num Balancer de Carga Pública Standard.  [O cenário 3](#defaultsnat) não está disponível com o Standard SKU.  Isto significa que, quando um Balancer de Carga Padrão interno é utilizado, você precisa tomar medidas para criar conectividade de saída para os VMs na piscina de backend se a conectividade de saída for desejada.  No contexto da conectividade de saída, um Único VM autónomo, todos os VM's em um Conjunto de Disponibilidade, todos os casos em um VMSS comportam-se como um grupo. Isto significa que, se um único VM num Conjunto de Disponibilidade estiver associado a um SKU Padrão, todos os casos de VM dentro deste Conjunto de Disponibilidade comportam-se agora pelas mesmas regras que se estiverem associados ao SKU Padrão, mesmo que uma instância individual não esteja diretamente associada ao mesmo. Este comportamento também é observado no caso de um VM autónomo com vários cartões de interface de rede ligados a um equilibrista de carga. Se um NIC for adicionado como um autónomo, terá o mesmo comportamento. Reveja cuidadosamente todo este documento para compreender os conceitos globais, reveja o [Standard Load Balancer](load-balancer-standard-overview.md) para obter diferenças entre As EK E reveja [as regras de saída](load-balancer-outbound-rules-overview.md).  A utilização de regras de saída permite-lhe um controlo fino sobre todos os aspetos da conectividade de saída.

## <a name="scenario-overview"></a><a name="scenarios"></a>Scenario overview (Descrição geral do cenário)

O Azure Load Balancer e os recursos conexos são explicitamente definidos quando se está a utilizar o Gestor de [Recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Atualmente, o Azure fornece três métodos diferentes para alcançar a conectividade de saída para os recursos do Gestor de Recursos Azure. 

| SKUs | Cenário | Método | Protocolos IP | Descrição |
| --- | --- | --- | --- | --- |
| Standard, Básico | [1. VM com um endereço IP público de nível de instância (com ou sem equilíbrio de carga)](#ilpip) | SNAT, porta disfarçada não utilizada | TCP, UDP, ICMP, ESP | O Azure utiliza o IP público atribuído à configuração IP do NIC da instância. A instância tem todas as portas efémeras disponíveis. Ao utilizar o Standard Load Balancer, [as regras](load-balancer-outbound-rules-overview.md) de saída não são suportadas se um IP público for atribuído à Máquina Virtual. |
| Standard, Básico | [2. Equilibrador de Carga Pública associado a um VM (sem endereço IP público na instância)](#lb) | SNAT com máscara de porta (PAT) utilizando as extremidades dianteiras do Balanceor de Carga | TCP |A Azure partilha o endereço IP público dos frontends públicos do Load Balancer com vários endereços IP privados. Azure usa portas efémeras das extremidades dianteiras para PAT. Deve usar regras de [saída](load-balancer-outbound-rules-overview.md) para definir explicitamente a conectividade de saída. |
| nenhum ou Básico | [3. VM autónomo (sem Balancer de carga, sem endereço IP público)](#defaultsnat) | SNAT com máscara de porta (PAT) | TCP | O Azure designa automaticamente um endereço IP público para SNAT, partilha este endereço IP público com múltiplos endereços IP privados do conjunto de disponibilidade, e utiliza portas efémeras deste endereço IP público. Este cenário é um recuo para os cenários anteriores. Não o recomendamos se precisar de visibilidade e controlo. |

Se não quiser que um VM comunique com pontos finais fora do Azure no espaço público de endereços IP, pode utilizar grupos de segurança de rede (NSGs) para bloquear o acesso conforme necessário. A secção [Que impede a conectividade de saída](#preventoutbound) discute mais detalhadamente os NSGs. A orientação sobre a conceção, implementação e gestão de uma rede virtual sem qualquer acesso de saída está fora do âmbito deste artigo.

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>Cenário 1: VM com endereço IP público

Neste cenário, o VM tem um IP público atribuído a ele. No que diz respeito às ligações de saída, não importa se o VM é equilibrado ou não. Este cenário tem precedência sobre os outros. Quando é utilizado um endereço IP público, o VM utiliza o endereço IP público para todos os fluxos de saída.  

Um IP público atribuído a um VM é uma relação 1:1 (em vez de 1: muitos) e implementado como um NAT apátrida 1:1.  Não é utilizado mascaramento de portas (PAT) e o VM tem todas as portas efémeras disponíveis para utilização.

Se a sua aplicação iniciar muitos fluxos de saída e sentir a exaustão da porta SNAT, considere atribuir um [endereço IP público para mitigar os constrangimentos do SNAT](#assignilpip). Reveja [a exaustão da SNAT](#snatexhaust) na sua totalidade.

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>Cenário 2: VM equilibrado em carga sem endereço IP público

Neste cenário, o VM faz parte de uma piscina pública de backend do Balancer. O VM não tem um endereço IP público atribuído a ele. O recurso Balancer load deve ser configurado com uma regra de equilíbrio de carga para criar uma ligação entre o frontend IP público com a piscina de backend.

Se não completar esta configuração de regra, o comportamento é descrito no cenário de [VM autónomo sem IP público](#defaultsnat). Não é necessário que a regra tenha um ouvinte que trabalha na piscina de backend para que a sonda de saúde tenha sucesso.

Quando o VM equilibrado em carga cria um fluxo de saída, o Azure traduz o endereço IP de origem privada do fluxo de saída para o endereço IP público do frontend do Balancer de Carga pública. O Azure usa o SNAT para executar esta função. Azure também usa [PAT](#pat) para mascarar vários endereços IP privados por trás de um endereço IP público. 

As portas efémeras do endereço IP do equilibrista de carga são utilizadas para distinguir os fluxos individuais originados pelo VM. O SNAT utiliza dinamicamente [portas efémeras preallocalizadas](#preallocatedports) quando os fluxos de saída são criados. Neste contexto, as portas efémeras utilizadas para o SNAT são chamadas portas SNAT.

As portas SNAT são pré-atribuídas como descrito na secção [Understanding SNAT e PAT.](#snat) São um recurso finito que pode ser esgotado. É importante entender como são [consumidos.](#pat) Para entender como projetar para este consumo e mitigar conforme necessário, reveja [a exaustão de Gestão do SNAT.](#snatexhaust)

Quando [vários endereços IP públicos estão associados ao Balancer Basic,](load-balancer-multivip-overview.md)qualquer um destes endereços IP públicos é um candidato para fluxos de saída, e um é selecionado aleatoriamente.  

Para monitorizar a saúde das ligações de saída com o Balancer Basic, pode utilizar [registos do Monitor Azure para](load-balancer-monitor-log.md) o Balancer de Carga e alertar os registos de [eventos](load-balancer-monitor-log.md#alert-event-log) para monitorizar as mensagens de exaustão da porta SNAT.

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>Cenário 3: VM autónomo sem endereço IP público

Neste cenário, o VM não faz parte de um pool público de balanceadores de carga (e não faz parte de um pool interno de balanceadores de carga) e não tem um endereço IP público atribuído a ele. Quando o VM cria um fluxo de saída, o Azure traduz o endereço IP de origem privada do fluxo de saída para um endereço IP de fonte pública. O endereço IP público utilizado para este fluxo de saída não é configurável e não conta com o limite público de recursos IP da subscrição. Este endereço IP público não lhe pertence nem pode ser reservado. Se recolocar o VM ou o Conjunto de Disponibilidade ou o conjunto de escala de máquina virtual, este endereço IP público será lançado e solicitado um novo endereço IP público. Não utilize este cenário para endereços IP de listagem branca. Em vez disso, utilize um dos outros dois cenários em que declara explicitamente o cenário de saída e o endereço IP público a ser usado para a conectividade de saída.

>[!IMPORTANT] 
>Este cenário também se aplica quando __apenas__ um equilíbrio de carga básico interno é anexado. O cenário 3 não está __disponível__ quando um balancedor de carga padrão interno é ligado a um VM.  Deve criar explicitamente o [cenário 1](#ilpip) ou [cenário 2,](#lb) para além da utilização de um Balancer de Carga Padrão interno.

O Azure utiliza o SNAT com máscara de porta[(PAT)](#pat)para executar esta função. Este cenário é semelhante ao [cenário 2](#lb), exceto que não existe controlo sobre o endereço IP utilizado. Este é um cenário de recuo para quando os cenários 1 e 2 não existem. Não recomendamos este cenário se quiser controlar o endereço de saída. Se as ligações de saída forem uma parte crítica da sua aplicação, deve escolher outro cenário.

As portas SNAT são preallocalizadas como descrito na secção [Understanding SNAT e PAT.](#snat)  O número de VMs que partilham um Conjunto de Disponibilidade determina qual o nível de pré-atribuição aplicável.  Um VM autónomo sem conjunto de disponibilidade é efetivamente um conjunto de 1 para efeitos de determinação da pré-atribuição (1024 portas SNAT). As portas SNAT são um recurso finito que pode ser esgotado. É importante entender como são [consumidos.](#pat) Para entender como projetar para este consumo e mitigar conforme necessário, reveja [a exaustão de Gestão do SNAT.](#snatexhaust)

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>Múltiplos cenários combinados

Pode combinar os cenários descritos nas secções anteriores para obter um resultado específico. Quando existem múltiplos cenários, aplica-se uma ordem de precedência: [o cenário 1](#ilpip) tem precedência sobre os [cenários 2](#lb) e [3](#defaultsnat). [Cenário 2](#lb) substitui [o cenário 3](#defaultsnat).

Um exemplo é uma implantação do Gestor de Recursos Azure onde a aplicação depende fortemente de ligações de saída a um número limitado de destinos, mas também recebe fluxos de entrada através de um frontend de Balancer load balancer. Neste caso, pode combinar cenários 1 e 2 para alívio. Para padrões adicionais, reveja [a exaustão de Gestão do SNAT.](#snatexhaust)

### <a name="multiple-frontends-for-outbound-flows"></a><a name="multife"></a>Múltiplas frentes para fluxos de saída

#### <a name="standard-load-balancer"></a>Balanceador de Carga Padrão

O Standard Load Balancer utiliza todos os candidatos para fluxos de saída ao mesmo tempo que [vários frontends IP (públicos)](load-balancer-multivip-overview.md) estão presentes. Cada extremidade frontal multiplica o número de portas SNAT preallocalizadas disponíveis se uma regra de equilíbrio de carga estiver ativada para ligações de saída.

Pode optar por suprimir um endereço IP frontal de ser utilizado para ligações de saída com uma nova opção de regra de equilíbrio de carga:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Normalmente, `disableOutboundSnat` a opção falha em _falso_ e significa que esta regra programa snat de saída para os VMs associados no pool de backend da regra de equilíbrio de carga. A `disableOutboundSnat` pode ser alterada de _forma verdadeira_ para evitar que o Balancer de Carga utilize o endereço IP frontal associado para ligações de saída para os VMs no pool de backend desta regra de equilíbrio de carga.  E também pode designar um endereço IP específico para fluxos de saída, conforme descrito em [múltiplos cenários combinados](#combinations) também.

#### <a name="load-balancer-basic"></a>Balancer Básico

Balancer Basic escolhe uma única extremidade frontal para ser usada para fluxos de saída quando [múltiplos suportes IP (públicos)](load-balancer-multivip-overview.md) são candidatos para fluxos de saída. Esta seleção não é configurável, e deve considerar o algoritmo de seleção aleatório. Pode designar um endereço IP específico para fluxos de saída conforme descrito em [múltiplos cenários combinados](#combinations).

### <a name="availability-zones"></a><a name="az"></a>Zonas de Disponibilidade

Ao utilizar o Equilíbrio de Carga Padrão com Zonas de [Disponibilidade,](load-balancer-standard-availability-zones.md)as extremidades dianteiras redundantes podem fornecer ligações SNAT redundantes e a programação SNAT sobrevive à falha da zona.  Quando as extremidades dianteiras zonais são usadas, as ligações SNAT de saída partilham o destino com a zona a que pertencem.

## <a name="understanding-snat-and-pat"></a><a name="snat"></a>Compreensão de SNAT e PAT

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Porto mascarado SNAT (PAT)

Quando um recurso público do Balancer de Carga está associado a instâncias VM, que não têm endereços IP públicos dedicados, cada fonte de ligação de saída é reescrita. A fonte é reescrita do espaço de endereço IP privado da rede virtual para o endereço IP público frontal do equilibrador de carga. No espaço de endereço IP público, o 5-tuple do fluxo (endereço IP de origem, porta de origem, protocolo de transporte IP, endereço IP de destino, porta de destino) deve ser único. O SNAT de máscara snat portuário pode ser utilizado com protocolos IP TCP ou UDP.

As portas efémeras (portas SNAT) são utilizadas para o conseguir após a reescrita do endereço IP de origem privada, uma vez que vários fluxos provêm de um único endereço IP público. O algoritmo sNAT que mascara a porta ala portas SNAT de forma diferente para UDP versus TCP.

#### <a name="tcp-snat-ports"></a><a name="tcp"></a>Portas TCP SNAT

Uma porta SNAT é consumida por fluxo para um endereço IP de destino único, porta. Para múltiplos fluxos de TCP para o mesmo endereço IP de destino, porta e protocolo, cada fluxo de TCP consome uma única porta SNAT. Isto garante que os fluxos são únicos quando são originários do mesmo endereço IP público e vão para o mesmo endereço IP de destino, porta e protocolo. 

Vários fluxos, cada um para um endereço IP de destino diferente, porto e protocolo, partilham uma única porta SNAT. O endereço IP de destino, porto e protocolo tornam os fluxos únicos sem a necessidade de portas de origem adicional para distinguir fluxos no espaço público de endereços IP.

#### <a name="udp-snat-ports"></a><a name="udp"></a>Portas SNAT uDP

As portas UDP SNAT são geridas por um algoritmo diferente das portas TCP SNAT.  Load Balancer usa um algoritmo conhecido como "cone nat restrito à porta" para uDP.  Uma porta SNAT é consumida por cada fluxo, independentemente do endereço IP de destino, porta.

#### <a name="snat-port-reuse"></a>Reutilização da porta SNAT

Uma vez libertada uma porta, a porta está disponível para reutilização conforme necessário.  Pode pensar nas portas SNAT como uma sequência do mais baixo ao mais alto disponível para um determinado cenário, e a primeira porta SNAT disponível é usada para novas ligações. 
 
#### <a name="exhaustion"></a>Exaustão

Quando os recursos portuários SNAT estão esgotados, os fluxos de saída falham até que os fluxos existentes libertem as portas SNAT. O Balancer de Carga recupera as portas SNAT quando o fluxo se fecha e utiliza um [intervalo de 4 minutos](#idletimeout) para recuperar as portas SNAT dos fluxos inativos.

As portas UDP SNAT geralmente esgotam muito mais rapidamente do que as portas TCP SNAT devido à diferença no algoritmo usado. É preciso conceber e fazer um teste de escala com esta diferença em mente.

Para padrões que mitigam as condições que geralmente levam à exaustão da porta SNAT, reveja a secção [SNAT de Gestão.](#snatexhaust)

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>Pré-atribuição da porta efémera para o snat de máscara portuária (PAT)

O Azure usa um algoritmo para determinar o número de portas SNAT preallocalizadas disponíveis com base no tamanho da piscina traseira quando se utiliza o SNAT[(PAT)](#pat)mascarado por porta. As portas SNAT são portas efémeras disponíveis para um determinado endereço de fonte ip pública. Para cada endereço IP público associado a um equilibrador de carga existem 64.000 portas disponíveis como portas SNAT para cada protocolo de transporte IP.

O mesmo número de portas SNAT são preallolocalizados para UDP e TCP respectivamente e consumidos independentemente por protocolo de transporte IP.  No entanto, o uso da porta SNAT é diferente dependendo se o fluxo é UDP ou TCP.

>[!IMPORTANT]
>A programação Padrão SKU SNAT é por protocolo de transporte IP e deriva da regra de equilíbrio de carga.  Se existir apenas uma regra de equilíbrio de carga tCP, o SNAT só está disponível para TCP. Se tiver apenas uma regra de equilíbrio de carga TCP e precisar de SNAT de saída para uDP, crie uma regra de equilíbrio de carga UDP desde a mesma extremidade frontal até à mesma piscina de backend.  Isto irá desencadear a programação sNAT para a UDP.  Não é necessária uma regra de trabalho ou uma sonda de saúde.  Basic SKU SNAT sempre programa SNAT para ambos os protocolos de transporte IP, independentemente do protocolo de transporte especificado na regra de equilíbrio de carga.

Azure pré-atribui as portas SNAT à configuração IP do NIC de cada VM. Quando uma configuração IP é adicionada à piscina, as portas SNAT são preallocalizadas para esta configuração IP com base no tamanho da piscina de backend. Quando os fluxos de saída são criados, [pat](#pat) instituindo consome (até o limite preallocalizado) e liberta estas portas quando o fluxo fecha ou os [tempos inativos](#idletimeout) acontecem.

O quadro seguinte mostra as pré-alocações da porta SNAT para níveis de tamanhos de piscina de backend:

| Tamanho da piscina (instâncias VM) | Portas SNAT preallocalizadas por configuração IP|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

>[!NOTE]
> Ao utilizar o Standard Load Balancer com [várias extremidades dianteiras,](load-balancer-multivip-overview.md)cada endereço IP frontal multiplica o número de portas SNAT disponíveis na tabela anterior. Por exemplo, um conjunto de 50 VM's com 2 regras de equilíbrio de carga, cada um com um endereço IP frontal separado, utilizará 2048 (2x 1024) portas SNAT por regra. Consulte os detalhes para [várias frentes.](#multife)

Lembre-se que o número de portas SNAT disponíveis não se traduz diretamente no número de fluxos. Uma única porta SNAT pode ser reutilizada para vários destinos únicos. Os portos só são consumidos se for necessário para tornar os fluxos únicos. Para orientação de conceção e mitigação, consulte a secção sobre [como gerir este recurso gaseificante](#snatexhaust) e a secção que descreve [pat](#pat).

Mudar o tamanho da sua piscina de backend pode afetar alguns dos seus fluxos estabelecidos. Se o tamanho da piscina de backend aumentar e transitar para o próximo nível, metade das portas SNAT preallocalizadas são recuperadas durante a transição para o próximo maior nível de piscina de backend. Os fluxos associados a uma porta SNAT recuperada terão uma escala e devem ser restabelecidos. Se se tentar um novo fluxo, o fluxo terá sucesso imediatamente enquanto estiverem disponíveis portas preallocalizadas.

Se o tamanho da piscina de backend diminuir e transitar para um nível mais baixo, o número de portas SNAT disponíveis aumenta. Neste caso, as portas SNAT existentes e os respetivos fluxos não são afetados.

As dotações de portas SNAT são específicas do protocolo de transporte IP (TCP e UDP são mantidas separadamente) e são libertadas nas seguintes condições:

### <a name="tcp-snat-port-release"></a>Lançamento da porta TCP SNAT

- Se qualquer um dos servidores/clientes enviar finack, a porta SNAT será libertada após 240 segundos.
- Se for visto um RST, a porta SNAT será libertada após 15 segundos.
- Se o tempo de paragem inativa tiver sido atingido, o porto é libertado.

### <a name="udp-snat-port-release"></a>Libertação da porta UDP SNAT

- Se o tempo de paragem inativa tiver sido atingido, o porto é libertado.

## <a name="problem-solving"></a><a name="problemsolving"></a>Resolução de problemas 

Esta secção destina-se a ajudar a mitigar a exaustão do SNAT e que pode ocorrer com ligações de saída em Azure.

### <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a>Gestão da exaustão da porta SNAT (PAT)
[As portas efémeras utilizadas](#preallocatedports) para [pat](#pat) são um recurso gaseivel, tal como descrito em [VM autónomo sem endereço IP público](#defaultsnat) e [VM equilibrado em carga sem endereço IP público](#lb). Pode monitorizar o seu uso de portas efémeras e comparar com a sua alocação atual para determinar o risco de ou confirmar a exaustão do SNAT utilizando [este](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) guia.

Se sabe que está a dar início a muitas ligações TCP ou UDP de saída para o mesmo endereço IP e porta de destino, e observa falhas nas ligações de saída ou é aconselhado pelo suporte de que está a esgotar as portas SNAT [(portas efémeras preallocalizadas utilizadas](#preallocatedports) pela [PAT),](#pat)tem várias opções gerais de mitigação. Reveja estas opções e decida o que está disponível e o melhor para o seu cenário. É possível que um ou mais possam ajudar a gerir este cenário.

Se estiver com dificuldades em compreender o comportamento de ligação de saída, pode utilizar estatísticas de pilhas IP (netstat). Ou pode ser útil observar comportamentos de conexão usando capturas de pacotes. Pode executar estas capturas de pacotes no os so do seu exemplo ou utilizar o Network Watcher para a captura de [pacotes](../network-watcher/network-watcher-packet-capture-manage-portal.md). 

#### <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>Alocar manualmente as portas SNAT para maximizar as portas SNAT por VM
Tal como definido nas [portas preallonadas,](#preallocatedports)o equilibrador de carga alocará automaticamente portas com base no número de VMs no backend. Por defeito, isto é feito de forma conservadora para garantir a escalabilidade. Se souber o número máximo de VMs que terá no backend, pode alocar manualmente as portas SNAT configurando-as em cada regra de saída. Por exemplo, se souber que terá um máximo de 10 VMs, pode alocar 6.400 portas SNAT por VM em vez das 1.024 predefinidas. 

#### <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Modificar a aplicação para reutilizar ligações 
Pode reduzir a procura de portas efémeras que são usadas para SNAT reutilizando ligações na sua aplicação. Isto é especialmente verdade para protocolos como HTTP/1.1, onde a reutilização da ligação é o padrão. E outros protocolos que utilizam http como seu transporte (por exemplo, REST) podem beneficiar por sua vez. 

A reutilização é sempre melhor do que as ligações tCP atómicas individuais para cada pedido. A reutilização resulta em transações TCP mais performantes e muito eficientes.

#### <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Modificar a aplicação para utilizar o pooling de ligação
Pode empregar um esquema de agrupamento de ligação na sua aplicação, onde os pedidos são distribuídos internamente através de um conjunto fixo de ligações (cada uma reutilizando sempre que possível). Este regime limita o número de portas efémeras em uso e cria um ambiente mais previsível. Este regime também pode aumentar a entrada de pedidos, permitindo múltiplas operações simultâneas quando uma única ligação está a bloquear a resposta de uma operação.  

O agrupamento de ligações pode já existir dentro do quadro que está a usar para desenvolver a sua aplicação ou as definições de configuração para a sua aplicação. Pode combinar o agrupamento de ligação com a reutilização da ligação. Os seus múltiplos pedidos consomem então um número fixo e previsível de portas para o mesmo endereço IP de destino e porta. Os pedidos beneficiam igualmente de uma utilização eficiente das transações de TCP, reduzindo a latência e a utilização de recursos. As transações uDP também podem beneficiar, porque gerir o número de fluxos UDP pode, por sua vez, evitar condições de escape e gerir a utilização da porta SNAT.

#### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Modificar a aplicação para usar uma lógica de retry menos agressiva
Quando as [portas efémeras preallocalizadas utilizadas](#preallocatedports) para [PAT](#pat) estiverem esgotadas ou se ocorrem falhas de aplicação, retries de força agressiva ou bruta sem a decomposição e a lógica de recuo fazem com que a exaustão ocorra ou persista. Pode reduzir a procura de portas efémeras utilizando uma lógica de retry menos agressiva. 

As portas efémeras têm um tempo inativo de 4 minutos (não ajustável). Se as tentativas forem demasiado agressivas, a exaustão não tem oportunidade de se limpar por si só. Portanto, considerando como e com que frequência -- as transações de retrys da sua aplicação é uma parte crítica do design.

#### <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Atribuir um IP público a cada VM
Atribuir um endereço IP público altera o seu cenário para [IP público para um VM](#ilpip). Todos os portos efémeros do IP público que são utilizados para cada VM estão disponíveis para o VM. (Ao contrário dos cenários em que os portos efémeros de um PI público são partilhados com todos os VMs associados à respetiva piscina de backend.) Há contrapartidas a ter em conta, tais como o custo adicional dos endereços IP públicos e o impacto potencial da lista geminada de um grande número de endereços IP individuais.

>[!NOTE] 
>Esta opção não está disponível para funções de web worker.

#### <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Use várias extremidades dianteiras

Ao utilizar o Balancer de Carga Padrão Público, atribua [vários endereços IP frontais para ligações de saída](#multife) e [multiplica o número de portas SNAT disponíveis](#preallocatedports).  Crie uma configuração ip frontal, regra e piscina de backend para desencadear a programação do SNAT para o IP público do frontend.  A regra não precisa de funcionar e uma sonda de saúde não precisa de ser bem sucedida.  Se utilizar também várias extremidades dianteiras para a entrada (em vez de apenas para saída), deve utilizar bem as sondas de saúde personalizadas para garantir a fiabilidade.

>[!NOTE]
>Na maioria dos casos, a exaustão das portas SNAT é um sinal de mau design.  Certifique-se de que compreende porque está a esgotar as portas antes de utilizar mais frontendas para adicionar portas SNAT.  Podes estar a mascarar um problema que pode levar ao fracasso mais tarde.

#### <a name="scale-out"></a><a name="scaleout"></a>Aumentar horizontalmente

[As portas preallocated](#preallocatedports) são atribuídas com base no tamanho da piscina traseira e agruparam-se em níveis para minimizar a perturbação quando algumas das portas têm de ser realojadas para acomodar o próximo maior nível de tamanho da piscina.  Você pode ter a opção de aumentar a intensidade da utilização da porta SNAT para uma determinada extremidade frontal, escalando a sua piscina de backend para o tamanho máximo para um determinado nível.  Isto requer que a aplicação se esforce de forma eficiente.

Por exemplo, duas máquinas virtuais na piscina de backend teriam 1024 portas SNAT disponíveis por configuração IP, permitindo um total de 2048 portas SNAT para a implantação.  Se a implantação for aumentada para 50 máquinas virtuais, embora o número de portas preallocalizadas permaneça constante por máquina virtual, um total de 51.200 (50 x 1024) portas SNAT podem ser utilizadas pela implantação.  Se desejar aumentar a sua implantação, verifique o número de [portas preallocalizadas](#preallocatedports) por nível para se certificar de que molda a sua balança ao máximo para o respetivo nível.  No exemplo anterior, se tivesse optado por escalar para 51 em vez de 50 instâncias, avançaria para o nível seguinte e acabaria com menos portas SNAT por VM, bem como no total.

Se você escalar para o próximo maior nível de tamanho de piscina de reserva, há potencial para algumas das suas ligações de saída para o tempo limite se as portas atribuídas tiverem que ser relocalizadas.  Se estiver a usar apenas algumas das suas portas SNAT, escalar para fora através do tamanho da piscina de backend maior é inconsequente.  Metade das portas existentes serão realojadas cada vez que se mudar para o próximo nível de piscina.  Se não quer que isto ocorra, tem de moldar a sua implantação para o tamanho do nível.  Ou certifique-se de que a sua aplicação pode detetar e voltar a tentar, se necessário.  As vidas de manutenção da TCP podem ajudar a detetar quando as portas SNAT já não funcionam devido à relocalização.

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Use vidas de manutenção para repor o tempo de saída inativo

As ligações de saída têm um intervalo de 4 minutos. Este tempo limite é ajustável através [das regras de saída.](../load-balancer/load-balancer-outbound-rules-overview.md#idletimeout) Também pode utilizar o transporte (por exemplo, a manutenção de vidas tCP) ou a manutenção da camada de aplicação para refrescar um fluxo inativo e repor este tempo inativo, se necessário.  

Ao utilizar as vidas de manutenção de TCP, é suficiente para as ativar de um lado da ligação. Por exemplo, é suficiente para os permitir no lado do servidor apenas para repor o temporizador inativo do fluxo e não é necessário para ambos os lados iniciar em manutenção de TCP.  Existem conceitos semelhantes para a camada de aplicação, incluindo configurações de servidor de cliente de base de dados.  Verifique o lado do servidor sobre quais as opções existentes para a aplicação específica de keepalives.

## <a name="discovering-the-public-ip-that-a-vm-uses"></a><a name="discoveroutbound"></a>Descobrir o IP público que um VM usa
Existem muitas formas de determinar o endereço IP de origem pública de uma ligação de saída. A OpenDNS oferece um serviço que pode mostrar-lhe o endereço IP público do seu VM. 

Ao utilizar o comando nslookup, pode enviar uma consulta de DNS para o nome myip.opendns.com para o resolver OpenDNS. O serviço devolve o endereço IP de origem que foi usado para enviar a consulta. Quando executa a seguinte consulta a partir do seu VM, a resposta é o IP público usado para esse VM:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Prevenção da conectividade de saída
Às vezes é indesejável que um VM seja autorizado a criar um fluxo de saída. Ou pode haver uma exigência de gestão de quais destinos podem ser alcançados com fluxos de saída, ou quais os destinos que podem iniciar fluxos de entrada. Neste caso, pode utilizar [grupos](../virtual-network/security-overview.md) de segurança de rede para gerir os destinos que o VM pode alcançar. Também pode usar NSGs para gerir qual destino público pode iniciar fluxos de entrada.

Quando aplicar um NSG a um VM equilibrado em carga, preste atenção às [etiquetas](../virtual-network/security-overview.md#service-tags) de serviço e às regras de [segurança padrão](../virtual-network/security-overview.md#default-security-rules). Deve certificar-se de que o VM pode receber pedidos de sonda de saúde do Azure Load Balancer. 

Se um NSG bloquear os pedidos de sonda de saúde a partir da etiqueta padrão AZURE_LOADBALANCER, a sua sonda de saúde VM falha e o VM está marcado para baixo. O Balancer de Carga deixa de enviar novos fluxos para aquele VM.

## <a name="limitations"></a>Limitações
- As Funções dos Trabalhadores Web sem um VNet e outros serviços da plataforma Microsoft podem ser acessíveis quando apenas um Balancer de Carga Padrão interno é usado devido a um efeito colateral a partir do funcionamento dos serviços pré-VNet e de outros serviços da plataforma. Não confie neste efeito colateral, uma vez que o próprio serviço ou a plataforma subjacente podem mudar sem aviso prévio. Deve sempre assumir que precisa de criar uma conectividade de saída explicitamente, se desejar, quando utilizar apenas um Balancer de Carga Padrão interno. O cenário [padrão sNAT](#defaultsnat) 3 descrito neste artigo não está disponível.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Standard Load Balancer](load-balancer-standard-overview.md).
- Saiba mais sobre [as regras de saída](load-balancer-outbound-rules-overview.md) para o Standard Public Load Balancer.
- Saiba mais sobre [o Balancer de Carga.](load-balancer-overview.md)
- Saiba mais sobre [grupos](../virtual-network/security-overview.md)de segurança de rede .
- Conheça algumas das [outras principais capacidades](../networking/networking-overview.md) de networking em Azure.
