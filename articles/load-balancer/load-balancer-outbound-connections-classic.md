---
title: Conexões de saída no Azure (clássico)
titlesuffix: Azure Load Balancer
description: Este artigo explica como o Azure permite que os serviços de nuvem se comuniquem com os serviços de Internet públicos.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: allensu
ms.openlocfilehash: 10af3b4838aae1565bac1d996997c117a74cedbc
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274671"
---
# <a name="outbound-connections-classic"></a>Conexões de saída (clássicas)

O Azure fornece conectividade de saída para implantações de clientes por meio de vários mecanismos diferentes. Este artigo descreve o que são os cenários, quando eles se aplicam, como eles funcionam e como gerenciá-los.

>[!NOTE]
>Este artigo aborda apenas as implantações clássicas.  Examine as [conexões de saída](load-balancer-outbound-connections.md) para todos os cenários de implantação do Gerenciador de recursos no Azure.

Uma implantação no Azure pode se comunicar com pontos de extremidade fora do Azure no espaço de endereço IP público. Quando uma instância inicia um fluxo de saída para um destino no espaço de endereço IP público, o Azure mapeia dinamicamente o endereço IP privado para um endereço IP público. Depois que esse mapeamento é criado, o tráfego de retorno para esse fluxo originado de saída também pode alcançar o endereço IP privado onde o fluxo foi originado.

O Azure usa SNAT (conversão de endereços de rede de origem) para executar essa função. Quando vários endereços IP privados estão sendo mascarados por trás de um único endereço IP público, o Azure usa a [Pat (conversão de endereços de porta)](#pat) para mascarar endereços IP privados. As portas efêmeras são usadas para PAT e [](#preallocatedports) são prealocadas com base no tamanho do pool.

Há vários [cenários de saída](#scenarios). Você pode combinar esses cenários conforme necessário. Revise-os cuidadosamente para entender os recursos, as restrições e os padrões conforme eles se aplicam ao seu modelo de implantação e cenário de aplicativo. Examine as diretrizes para [gerenciar esses cenários](#snatexhaust).

## <a name="scenarios"></a>Visão geral do cenário

O Azure fornece três métodos diferentes para obter implantações clássicas de conectividade de saída.  Nem todas as implantações clássicas têm todos os três cenários disponíveis:

| Cenário | Método | Protocolos IP | Descrição | Função Web Worker | IaaS | 
| --- | --- | --- | --- | --- | --- |
| [1. VM com um endereço IP público em nível de instância](#ilpip) | SNAT, mascaramento de porta não usado | TCP, UDP, ICMP, ESP | O Azure usa a máquina virtual atribuída por IP público. A instância tem todas as portas efêmeras disponíveis. | Não | Sim |
| [2. ponto de extremidade de balanceamento de carga público](#publiclbendpoint) | SNAT com PAT (disfarce de porta) para o ponto de extremidade público | TCP, UDP | O Azure compartilha o ponto de extremidade público do endereço IP público com vários pontos de extremidades privados. O Azure usa portas efêmeras do ponto de extremidade público para PAT. | Sim | Sim |
| [3. VM autônoma](#defaultsnat) | SNAT com disfarce de porta (PAT) | TCP, UDP | O Azure designa automaticamente um endereço IP público para SNAT, compartilha esse endereço IP público com toda a implantação e usa portas efêmeras do endereço IP do ponto de extremidade público para PAT. Este é um cenário de fallback para os cenários anteriores. Não é recomendável se você precisar de visibilidade e controle. | Sim | Sim |

Este é um subconjunto da funcionalidade de conexão de saída disponível para implantações do Gerenciador de recursos no Azure.  

Implantações diferentes no clássico têm funcionalidade diferente:

| Implementação clássica | Funcionalidade disponível | 
| --- | --- |
| Máquina virtual | cenário [1](#ilpip), [2](#publiclbendpoint)ou [3](#defaultsnat) |
| Função Web Worker | somente cenário [2](#publiclbendpoint), [3](#defaultsnat) | 

As [estratégias](#snatexhaust) de mitigação também têm as mesmas diferenças.

O algoritmo usado para a alocação de portas efêmeras para as implantações clássicas é o mesmo que para implantações de Azure Resource Manager de recursos.

### <a name="ilpip"></a>Cenário 1: VM com um endereço IP público em nível de instância

Nesse cenário, a VM tem um ILPIP (IP público em nível de instância) atribuído a ela. No que diz respeito a conexões de saída, não importa se a VM tem um ponto de extremidade com balanceamento de carga ou não. Esse cenário tem precedência sobre os outros. Quando um ILPIP é usado, a VM usa o ILPIP para todos os fluxos de saída.  

Um IP público atribuído a uma VM é uma relação 1:1 (em vez de 1: muitos) e implementada como um NAT 1:1 sem estado.  PAT (mascaramento de porta) não é usado e a VM tem todas as portas efêmeras disponíveis para uso.

Se seu aplicativo iniciar muitos fluxos de saída e você enfrentar o esgotamento de porta SNAT, considere atribuir um [ILPIP para atenuar as restrições de SNAT](#assignilpip). Examine o gerenciamento do esgotamento de [SNAT](#snatexhaust) em sua totalidade.

### <a name="publiclbendpoint"></a>Cenário 2: Ponto de extremidade com balanceamento de carga público

Nesse cenário, a função VM ou Web Worker é associada a um endereço IP público por meio do ponto de extremidade com balanceamento de carga. A VM não tem um endereço IP público atribuído a ela. 

Quando a VM com balanceamento de carga cria um fluxo de saída, o Azure converte o endereço IP de origem particular do fluxo de saída para o endereço IP público do ponto de extremidade público com balanceamento de carga. O Azure usa SNAT para executar essa função. O Azure também usa [Pat](#pat) para mascarar vários endereços IP privados por trás de um endereço IP público. 

As portas efêmeras do front-end do endereço IP público do balanceador de carga são usadas para distinguir fluxos individuais originados pela VM. O SNAT usa dinamicamente [portas efêmeras](#preallocatedports) prealocadas quando fluxos de saída são criados. Nesse contexto, as portas efêmeras usadas para SNAT são chamadas de portas SNAT.

As portas SNAT são prealocadas conforme descrito na seção [entendendo SNAT e Pat](#snat) . Eles são um recurso finito que pode ser esgotado. É importante entender como eles são consumidos. [](#pat) Para entender como projetar para esse consumo e mitigar conforme necessário, examine o [Gerenciamento](#snatexhaust)do esgotamento de SNAT.

Quando existem [vários pontos de extremidade públicos com balanceamento de carga](load-balancer-multivip.md) , qualquer um desses endereços IP públicos é um candidato para fluxos de saída e um é selecionado aleatoriamente.  

### <a name="defaultsnat"></a>Cenário 3: Nenhum endereço IP público associado

Nesse cenário, a função VM ou Web Worker não faz parte de um ponto de extremidade público com balanceamento de carga.  E, no caso da VM, ele não tem um endereço ILPIP atribuído a ele. Quando a VM cria um fluxo de saída, o Azure converte o endereço IP de origem privado do fluxo de saída para um endereço IP de origem público. O endereço IP público usado para esse fluxo de saída não é configurável e não conta com relação ao limite de recursos de IP público da assinatura.  O Azure aloca esse endereço automaticamente.

O Azure usa SNAT com disfarce de porta ([Pat](#pat)) para executar essa função. Esse cenário é semelhante ao cenário 2, exceto que não há nenhum controle sobre o endereço IP usado. Esse é um cenário de fallback para quando os cenários 1 e 2 não existem. Não recomendamos esse cenário se você quiser ter controle sobre o endereço de saída. Se as conexões de saída forem uma parte crítica do seu aplicativo, você deverá escolher outro cenário.

As portas SNAT são prealocadas conforme descrito na seção [entendendo SNAT e Pat](#snat) .  O número de VMs ou funções de Web Worker que compartilham o endereço IP público determina o número de portas efêmeras prealocadas.   É importante entender como eles são consumidos. [](#pat) Para entender como projetar para esse consumo e mitigar conforme necessário, examine o [Gerenciamento](#snatexhaust)do esgotamento de SNAT.

## <a name="snat"></a>Compreendendo o SNAT e o PAT

### <a name="pat"></a>SNAT de mascaramento de porta (PAT)

Quando uma implantação faz uma conexão de saída, cada fonte de conexão de saída é reescrita. A origem é reescrita do espaço de endereço IP privado para o IP público associado à implantação (com base nos cenários descritos acima). No espaço de endereço IP público, a 5 tupla do fluxo (endereço IP de origem, porta de origem, protocolo de transporte IP, endereço IP de destino, porta de destino) deve ser exclusiva.  

As portas efêmeras (portas SNAT) são usadas para fazer isso após a regravação do endereço IP de origem particular, pois vários fluxos se originam de um único endereço IP público. 

Uma porta SNAT é consumida por fluxo para um único endereço IP de destino, porta e protocolo. Para vários fluxos para o mesmo endereço IP de destino, porta e protocolo, cada fluxo consome uma única porta SNAT. Isso garante que os fluxos sejam exclusivos quando eles se originam do mesmo endereço IP público e vão para o mesmo endereço IP de destino, porta e protocolo. 

Vários fluxos, cada um para um endereço IP de destino, porta e protocolo diferentes, compartilham uma única porta SNAT. O endereço IP de destino, a porta e o protocolo tornam os fluxos exclusivos sem a necessidade de portas de origem adicionais para distinguir os fluxos no espaço de endereço IP público.

Quando os recursos de porta SNAT são esgotados, os fluxos de saída falham até que os fluxos existentes liberem portas SNAT. Load Balancer recupera portas SNAT quando o fluxo fecha e usa um [tempo limite de ociosidade de 4 minutos](#idletimeout) para recuperar portas SNAT de fluxos ociosos.

Para padrões para mitigar condições que geralmente levam ao esgotamento de porta SNAT, examine a seção [Managing SNAT](#snatexhaust) .

### <a name="preallocatedports"></a>Prealocação de porta efêmera para a porta de disfarce SNAT (PAT)

O Azure usa um algoritmo para determinar o número de portas SNAT pré-alocado disponíveis com base no tamanho do pool de back-end ao usar[Pat](#pat)(memória disfarçada SNAT). As portas SNAT são portas efêmeras disponíveis para um endereço de origem IP público específico.

O Azure prealoca portas SNAT quando uma instância é implantada com base em quantas instâncias de função de VM ou Web Worker compartilham um determinado endereço IP público.  Quando os fluxos de saída são criados, [Pat](#pat) consome dinamicamente (até o limite prealocado) e libera essas portas quando o fluxo fecha ou os tempos limite de ociosidade acontecem.

A tabela a seguir mostra as prealocações de porta SNAT para camadas de tamanhos de pool de back-end:

| instâncias | Portas SNAT prealocadas por instância |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |

Lembre-se de que o número de portas SNAT disponíveis não é convertido diretamente no número de fluxos. Uma única porta SNAT pode ser reutilizada para vários destinos exclusivos. As portas são consumidas apenas se for necessário tornar os fluxos exclusivos. Para obter diretrizes de design e mitigação, consulte a seção sobre [como gerenciar esse recurso esse esgotável](#snatexhaust) e a seção que descreve [Pat](#pat).

Alterar o tamanho da sua implantação pode afetar alguns dos fluxos estabelecidos. Se o tamanho do pool de back-end aumentar e fizer a transição para a próxima camada, metade das portas SNAT prealocadas serão recuperadas durante a transição para a próxima camada maior do pool de back-end. Os fluxos associados a uma porta SNAT recuperada atingirão o tempo limite e deverão ser restabelecidos. Se um novo fluxo for tentado, o fluxo terá sucesso imediatamente, desde que as portas prealocadas estejam disponíveis.

Se o tamanho da implantação diminuir e fizer a transição para uma camada inferior, o número de portas SNAT disponíveis aumentará. Nesse caso, as portas SNAT alocadas existentes e seus respectivos fluxos não são afetados.

Se um serviço de nuvem for reimplantado ou alterado, a infraestrutura poderá reportar temporariamente o pool de back-end para ser até duas vezes maior do que o real, e o Azure, por sua vez, desalocará a menos portas SNAT por instância do que o esperado.  Isso pode aumentar temporariamente a probabilidade de esgotamento da porta SNAT. Eventualmente, o tamanho do pool passará para o tamanho real e o Azure aumentará automaticamente as portas SNAT prealocadas para o número esperado de acordo com a tabela acima.  Esse comportamento é por design e não é configurável.

As alocações de portas SNAT são o protocolo de transporte IP específico (TCP e UDP são mantidos separadamente) e são liberadas sob as seguintes condições:

### <a name="tcp-snat-port-release"></a>Versão da porta TCP SNAT

- Se o servidor/cliente enviar FIN/ACK, a porta SNAT será liberada após 240 segundos.
- Se um RST for visto, a porta SNAT será liberada após 15 segundos.
- o tempo limite de ociosidade foi atingido

### <a name="udp-snat-port-release"></a>Versão da porta TCP SNAT

- o tempo limite de ociosidade foi atingido

## <a name="problemsolving"></a>Solução de problemas 

Esta seção destina-se a ajudar a mitigar o esgotamento de SNAT e outros cenários que podem ocorrer com conexões de saída no Azure.

### <a name="snatexhaust"></a>Gerenciando esgotamento de porta SNAT (PAT)
[As portas efêmeras](#preallocatedports) usadas para [Pat](#pat) são um recurso esse esgotável, conforme descrito em [nenhum IP público associado](#defaultsnat) e [ponto de extremidade público com balanceamento de carga](#publiclbendpoint).

Se você sabe que está iniciando muitas conexões TCP ou UDP de saída para o mesmo endereço IP de destino e porta, e observa as conexões de saída com falha ou são aconselhadas pelo suporte de que você está esgotando portas SNAT ( [portas efêmeras](#preallocatedports) prealocadas usado pela [Pat](#pat)), você tem várias opções de mitigação geral. Examine essas opções e decida o que está disponível e melhor para seu cenário. É possível que um ou mais possam ajudar a gerenciar esse cenário.

Se você estiver tendo problemas para entender o comportamento da conexão de saída, você pode usar estatísticas de pilha de IP (netstat). Ou pode ser útil observar comportamentos de conexão usando capturas de pacote.

#### <a name="connectionreuse"></a>Modificar o aplicativo para reutilizar conexões 
Você pode reduzir a demanda de portas efêmeras que são usadas para SNAT Reutilizando conexões em seu aplicativo. Isso é especialmente verdadeiro para protocolos como HTTP/1.1, onde a reutilização de conexão é o padrão. E outros protocolos que usam HTTP como seu transporte (por exemplo, REST) podem se beneficiar por vez. 

A reutilização é sempre melhor do que as conexões TCP individuais e atômicas para cada solicitação. Reutilizar os resultados em transações TCP muito eficientes e de alto desempenho.

#### <a name="connection pooling"></a>Modificar o aplicativo para usar o pool de conexões
Você pode empregar um esquema de pooling de conexão em seu aplicativo, onde as solicitações são distribuídas internamente em um conjunto fixo de conexões (cada uma reutilização sempre que possível). Esse esquema restringe o número de portas efêmeras em uso e cria um ambiente mais previsível. Esse esquema também pode aumentar a taxa de transferência de solicitações, permitindo várias operações simultâneas quando uma única conexão está bloqueando a resposta de uma operação.  

Talvez o pool de conexões já exista na estrutura que você está usando para desenvolver seu aplicativo ou as definições de configuração para seu aplicativo. Você pode combinar o pool de conexões com a reutilização de conexão. Em seguida, as várias solicitações consomem um número fixo e previsível de portas para a mesma porta e endereço IP de destino. As solicitações também se beneficiam do uso eficiente de transações de TCP, reduzindo a latência e a utilização de recursos. As transações UDP também podem se beneficiar, pois gerenciar o número de fluxos UDP pode, por sua vez, evitar condições de esgotamento e gerenciar a utilização da porta SNAT.

#### <a name="retry logic"></a>Modificar o aplicativo para usar uma lógica de repetição menos agressiva
Quando [as portas efêmeras](#preallocatedports) prealocadas usadas para [Pat](#pat) são esgotadas ou ocorrem falhas de aplicativo, novas tentativas agressivas ou de força bruta sem decaimento e retirada lógica causam a ocorrência de esgotamento ou persistência. Você pode reduzir a demanda de portas efêmeras usando uma lógica de repetição menos agressiva. 

As portas efêmeras têm um tempo limite de ociosidade de 4 minutos (não ajustável). Se as repetições forem muito agressivas, o esgotamento não terá oportunidade de se limpar por conta própria. Portanto, considerar como--e com que frequência-o aplicativo repete transações é uma parte crítica do design.

#### <a name="assignilpip"></a>Atribuir um IP público em nível de instância a cada VM
Atribuir um ILPIP altera seu cenário para o [IP público em nível de instância para uma VM](#ilpip). Todas as portas efêmeras do IP público que são usadas para cada VM estão disponíveis para a VM. (Em oposição aos cenários em que as portas efêmeras de um IP público são compartilhadas com todas as VMs associadas à respectiva implantação.) Há compensações a serem consideradas, como o possível impacto da lista de permissões de um grande número de endereços IP individuais.

>[!NOTE] 
>Essa opção não está disponível para funções de Web Worker.

### <a name="idletimeout"></a>Usar keepalives para redefinir o tempo limite de ociosidade de saída

As conexões de saída têm um tempo limite de ociosidade de 4 minutos. Esse tempo limite não é ajustável. No entanto, você pode usar o transporte (por exemplo, TCP keepalives) ou keepalives da camada de aplicativo para atualizar um fluxo ocioso e redefinir esse tempo limite ocioso, se necessário.  Verifique com o fornecedor de qualquer software empacotado se há suporte para isso ou como habilitá-lo.  Geralmente, apenas um lado precisa gerar keepalives para redefinir o tempo limite de ociosidade. 

## <a name="discoveroutbound"></a>Descobrindo o IP público que uma VM usa
Há várias maneiras de determinar o endereço IP de origem público de uma conexão de saída. O OpenDNS fornece um serviço que pode mostrar o endereço IP público de sua VM. 

Usando o comando Nslookup, você pode enviar uma consulta DNS para o nome myip.opendns.com para o resolvedor OpenDNS. O serviço retorna o endereço IP de origem que foi usado para enviar a consulta. Quando você executa a consulta a seguir de sua VM, a resposta é o IP público usado para essa VM:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [Load Balancer](load-balancer-overview.md) usado em implantações do Gerenciador de recursos.
- Saiba mais sobre os cenários de [conexão de saída](load-balancer-outbound-connections.md) disponíveis em implantações do Gerenciador de recursos.
