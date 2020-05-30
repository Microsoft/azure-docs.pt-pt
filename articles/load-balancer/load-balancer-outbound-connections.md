---
title: Ligações de saída no Azure
titleSuffix: Azure Load Balancer
description: Este artigo explica como o Azure permite que os VMs comuniquem com os serviços públicos de internet.
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
ms.openlocfilehash: 988434a72359ebe6ef84c4fd94041b2d4b90d5d9
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84221092"
---
# <a name="outbound-connections-in-azure"></a>Ligações de saída no Azure

O Azure Load Balancer fornece conectividade de saída para implementações de clientes através de vários mecanismos diferentes. Este artigo descreve quais são os cenários, quando se aplicam, como funcionam e como geri-los. Se estiver a ter problemas com a conectividade de saída através de um Balançador de Carga Azure, consulte o guia de resolução de problemas para ligações de saída (.. /load-balancer/troubleshoot-out-connection.md).

>[!NOTE] 
>Este artigo abrange apenas as implementações do Gestor de Recursos. Reveja [as ligações de saída (Classic)](load-balancer-outbound-connections-classic.md) para todos os cenários clássicos de implantação em Azure.

Uma implantação em Azure pode comunicar com pontos finais fora de Azure no espaço de endereço IP público. Quando uma instância inicia um fluxo de saída para um destino no espaço de endereço IP público, a Azure mapeia dinamicamente o endereço IP privado para um endereço IP público. Após a criação deste mapeamento, o tráfego de retorno para este fluxo originado de saída também pode chegar ao endereço IP privado de onde o fluxo originou.

O Azure utiliza tradução de endereço de rede de origem (SNAT) para executar esta função. Quando vários endereços IP privados estão disfarçados atrás de um único endereço IP público, a Azure utiliza [tradução de endereços portuários (PAT)](#pat) para mascarar endereços IP privados. As portas efémeras são utilizadas para PAT e [são pré-locadas](#preallocatedports) com base no tamanho da piscina.

Existem vários [cenários de saída.](#scenarios) Pode combinar estes cenários conforme necessário. Reveja-os cuidadosamente para entender as capacidades, constrangimentos e padrões à medida que se aplicam ao seu modelo de implementação e cenário de aplicação. Rever orientações para [a gestão destes cenários.](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)

>[!IMPORTANT] 
>O Balancer de Carga Padrão e o IP público standard introduzem novas habilidades e comportamentos diferentes para a conectividade de saída.  Não são os mesmos que os SKUs básicos.  Se quiser conectividade de saída ao trabalhar com SKUs padrão, deve defini-lo explicitamente com endereços IP públicos padrão ou Balancer de Carga pública Padrão.  Isto inclui a criação de conectividade de saída quando se utiliza um Balancer de Carga Padrão interno.  Recomendamos que utilize sempre regras de saída num Balanceador de Carga público Standard.  [O cenário 3](#defaultsnat) não está disponível com o Standard SKU.  Isto significa que quando um Balancer de Carga Padrão interno é utilizado, você precisa tomar medidas para criar conectividade de saída para os VMs na piscina de backend se a conectividade de saída for desejada.  No contexto da conectividade de saída, um único VM autónomo, todos os VM's num Conjunto de Disponibilidade, todos os casos num VMSS comportam-se em grupo. Isto significa que, se um único VM num Conjunto de Disponibilidade estiver associado a um SKU Standard, todos os casos de VM dentro deste Conjunto de Disponibilidades comportam-se agora pelas mesmas regras que se estão associados ao SKU padrão, mesmo que um caso individual não esteja diretamente associado a ele. Este comportamento também é observado no caso de um VM autónomo com vários cartões de interface de rede ligados a um equilibrador de carga. Se um NIC for adicionado como um autónomo, terá o mesmo comportamento. Reveja cuidadosamente todo este documento para compreender os conceitos globais, reveja o [Balanceador de Carga Padrão](load-balancer-standard-overview.md) para as diferenças entre SKUs e reveja [as regras de saída](load-balancer-outbound-rules-overview.md).  A utilização de regras de saída permite-lhe um controlo fino sobre todos os aspetos da conectividade de saída.

## <a name="scenario-overview"></a><a name="scenarios"></a>Descrição geral do cenário

O Azure Load Balancer e os recursos relacionados são explicitamente definidos quando se está a utilizar [o Gestor de Recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  A Azure fornece atualmente três métodos diferentes para alcançar a conectividade de saída para os recursos do Azure Resource Manager. 

| SKUs | Cenário | Método | Protocolos IP | Descrição |
| --- | --- | --- | --- | --- |
| Padrão, Básico | [1. VM com um endereço IP público de nível de instância (com ou sem balanceador de carga)](#ilpip) | SNAT, porta disfarçada não utilizada | TCP, UDP, ICMP, ESP | O Azure utiliza o IP público atribuído à configuração IP do NIC da instância. O caso tem todas as portas efémeras disponíveis. Ao utilizar o Balancer de Carga Padrão, [as regras de saída](load-balancer-outbound-rules-overview.md) não são suportadas se um IP público for atribuído à Máquina Virtual. |
| Padrão, Básico | [2. Balanceador de Carga Pública associado a um VM (sem endereço IP público no caso)](#lb) | SNAT com máscara de porta (PAT) usando os frontends do balanceador de carga | TCP, UDP |A Azure partilha o endereço IP público dos frontends do Balancer de Carga público com vários endereços IP privados. Azure usa portas efémeras dos frontends para PAT. Ao utilizar o Balancer de Carga Padrão, deve utilizar [regras de saída](load-balancer-outbound-rules-overview.md) para definir explicitamente a conectividade de saída. |
| nenhum ou Básico | [3. VM autónomo (sem balanceador de carga, sem endereço IP público)](#defaultsnat) | SNAT com máscara de porto (PAT) | TCP, UDP | O Azure designa automaticamente um endereço IP público para O SNAT, partilha este endereço IP público com vários endereços IP privados do conjunto de disponibilidade, e utiliza portas efémeras deste endereço IP público. Este cenário é uma recaiação para os cenários anteriores. Não recomendamos se precisar de visibilidade e controlo. |

Se não quiser que um VM comunique com pontos finais fora de Azure no espaço de endereço IP público, pode utilizar grupos de segurança de rede (NSGs) para bloquear o acesso conforme necessário. A secção [Prevenção da conectividade de saída](#preventoutbound) discute mais detalhadamente os NSGs. A orientação sobre a conceção, implementação e gestão de uma rede virtual sem qualquer acesso de saída está fora do âmbito deste artigo.

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>Cenário 1: VM com endereço IP público

Neste cenário, o VM tem um IP público atribuído a ele. No que diz respeito às ligações de saída, não importa se o VM é equilibrado ou não. Este cenário tem precedência sobre os outros. Quando um endereço IP público é utilizado, o VM utiliza o endereço IP público para todos os fluxos de saída.  

Um IP público atribuído a um VM é uma relação 1:1 (em vez de 1: muitos) e implementado como um APÁtrida 1:1 NAT.  A máscara portuária (PAT) não é utilizada e o VM tem todas as portas efémeras disponíveis para utilização.

Se a sua aplicação iniciar muitos fluxos de saída e experimentar a exaustão do porto SNAT, considere atribuir um [endereço IP público para mitigar as restrições do SNAT](../load-balancer/troubleshoot-outbound-connection.md#assignilpip). Rever [a exaustão do SNAT](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) na sua totalidade.

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>Cenário 2: VM equilibrado de carga sem endereço IP público

Neste cenário, o VM faz parte de uma piscina pública de backend Balancer de Carga. O VM não tem um endereço IP público que lhe seja atribuído. O recurso Balanceador de Carga deve ser configurado com uma regra do balançador de carga para criar uma ligação entre o frontend IP público com o pool de backend.

Se não completar esta configuração de regra, o comportamento é descrito no cenário para [VM autónomo sem IP público](#defaultsnat). Não é necessário que a regra tenha um ouvinte a trabalhar na piscina de backend para que a sonda de saúde tenha sucesso.

Quando o VM equilibrado em carga cria um fluxo de saída, o Azure traduz o endereço IP de origem privada do fluxo de saída para o endereço IP público do frontend do Balancer de Carga público. O Azure utiliza o SNAT para executar esta função. O Azure também usa [PAT](#pat) para mascarar vários endereços IP privados por trás de um endereço IP público. 

As portas efémeras do endereço IP do balançador de carga são utilizadas para distinguir os fluxos individuais originados pelo VM. O SNAT utiliza dinamicamente [portas efémeras pré-alitadas](#preallocatedports) quando os fluxos de saída são criados. Neste contexto, as portas efémeras utilizadas para o SNAT são chamadas portas SNAT.

As portas SNAT são pré-atribuídas conforme descrito na secção [Understanding SNAT e PAT.](#snat) São um recurso finito que pode ser esgotado. É importante entender como são [consumidos.](#pat) Para entender como projetar para este consumo e mitigar se necessário, [reveja a gestão da exaustão do SNAT.](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)

Quando [vários endereços IP públicos estão associados ao Load Balancer Basic,](load-balancer-multivip-overview.md)qualquer um destes endereços IP públicos é um candidato para fluxos de saída, e um é selecionado aleatoriamente.  

Para monitorizar a saúde das ligações de saída com o Load Balancer Basic, pode utilizar [registos do Monitor Azure para Balancer de Carga](load-balancer-monitor-log.md) e [alertar os registos de eventos](load-balancer-monitor-log.md#alert-event-log) para monitorizar as mensagens de exaustão da porta SNAT.

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>Cenário 3: VM autónomo sem endereço IP público

Neste cenário, o VM não faz parte de um pool público de Balanceadores de Carga (e não faz parte de um pool interno do Balancer de Carga Padrão) e não tem um endereço IP público atribuído a ele. Quando o VM cria um fluxo de saída, o Azure traduz o endereço IP de origem privada do fluxo de saída para um endereço IP de fonte pública. O endereço IP público utilizado para este fluxo de saída não é configurável e não conta contra o limite de recursos IP públicos da subscrição. Este endereço IP público não lhe pertence e não pode ser reservado. Se recolocar o conjunto de VM ou Availability set ou de escala de máquina virtual, este endereço IP público será lançado e um novo endereço IP público solicitado. Não utilize este cenário para listar endereços IP. Em vez disso, use um dos outros dois cenários onde declara explicitamente o cenário de saída e o endereço IP público para ser usado para a conectividade de saída.

>[!IMPORTANT] 
>Este cenário aplica-se também quando se fixa __apenas__ um Balanceador de Carga Básica interno. O cenário 3 não está __disponível__ quando um Balanceador de Carga Padrão interno está ligado a um VM.  Deve criar explicitamente [o cenário 1](#ilpip) ou [o cenário 2,](#lb) além de utilizar um Balancer de Carga Padrão interno.

O Azure utiliza o SNAT com máscaras de porta[(PAT)](#pat)para executar esta função. Este cenário é semelhante ao [cenário 2](#lb), exceto que não existe controlo sobre o endereço IP utilizado. Este é um cenário de recuo para quando os cenários 1 e 2 não existirem. Não recomendamos este cenário se quiser controlar o endereço de saída. Se as ligações de saída forem uma parte crítica da sua aplicação, deve escolher outro cenário.

As portas SNAT são pré-locadas como descrito na secção [Understanding SNAT e PAT.](#snat)  O número de VMs que partilham um Conjunto de Disponibilidade determina qual o nível de pré-afectuação aplicável.  Um VM autónomo sem um Conjunto de Disponibilidade é efetivamente um pool de 1 para efeitos de determinação da pré-aallocation (portas 1024 SNAT). As portas SNAT são um recurso finito que pode ser esgotado. É importante entender como são [consumidos.](#pat) Para entender como projetar para este consumo e mitigar se necessário, [reveja a gestão da exaustão do SNAT.](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>Múltiplos cenários combinados

Pode combinar os cenários descritos nas secções anteriores para obter um resultado específico. Quando existem vários cenários, aplica-se uma ordem de precedência: [o cenário 1](#ilpip) tem precedência sobre [os cenários 2](#lb) e [3](#defaultsnat). [O cenário 2](#lb) sobrepõe-se [ao cenário 3](#defaultsnat).

Um exemplo é uma implementação do Azure Resource Manager onde a aplicação depende fortemente de ligações de saída para um número limitado de destinos, mas também recebe fluxos de entrada sobre um frontend de load balancer. Neste caso, pode combinar cenários 1 e 2 para alívio. Para padrões adicionais, [reveja a gestão da exaustão do SNAT.](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)

### <a name="multiple-frontends-for-outbound-flows"></a><a name="multife"></a>Múltiplos frontends para fluxos de saída

#### <a name="standard-load-balancer"></a>Balanceador de carga padrão

O Balancer de Carga Padrão utiliza todos os candidatos para fluxos de saída ao mesmo tempo que [existem vários frontends IP (públicos).](load-balancer-multivip-overview.md) Cada frontend multiplica o número de portas SNAT pré-locadas disponíveis se for ativada uma regra de equilíbrio de carga para ligações de saída.

Pode optar por suprimir um endereço IP frontend de ser utilizado para ligações de saída com uma nova opção de regra de equilíbrio de carga:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Normalmente, a `disableOutboundSnat` opção é _falsa_ e significa que esta regra programa SNAT de saída para os VMs associados no pool de backend da regra de equilíbrio de carga. Pode `disableOutboundSnat` ser alterado para ser _verdadeiro_ para evitar que o Balanceador de Carga utilize o endereço IP de frontend associado para ligações de saída para os VMs no pool de backend desta regra de equilíbrio de carga.  E também pode designar um endereço IP específico para fluxos de saída, conforme descrito em [múltiplos cenários combinados](#combinations) também.

#### <a name="load-balancer-basic"></a>Balanceador de Carga Básico

Load Balancer Basic escolhe um único frontend para ser usado para fluxos de saída quando [vários frontends IP (públicos)](load-balancer-multivip-overview.md) são candidatos para fluxos de saída. Esta seleção não é configurável, e você deve considerar o algoritmo de seleção como aleatório. Pode designar um endereço IP específico para fluxos de saída, conforme descrito em [múltiplos cenários combinados](#combinations).

### <a name="availability-zones"></a><a name="az"></a>Zonas de Disponibilidade

Ao utilizar [o Balancer de Carga Padrão com Zonas de Disponibilidade,](load-balancer-standard-availability-zones.md)os frontends redundantes de zona podem fornecer ligações SNAT de saída redundantes e a programação SNAT sobrevive à falha da zona.  Quando as frentes zonais são usadas, as ligações SNAT de saída partilham o destino com a zona a que pertencem.

## <a name="understanding-snat-and-pat"></a><a name="snat"></a>Compreensão SNAT e PAT

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>SNAT de máscaras de porta (PAT)

Quando um recurso público do Balancer de Carga está associado a casos de VM, que não têm endereços IP públicos dedicados, cada fonte de ligação de saída é reescrita. A fonte é reescrita do espaço de endereço IP privado da rede virtual para o endereço IP público frontend do esídubo de carga. No espaço de endereço IP público, o 5-tuple do fluxo (endereço IP de origem, porta de origem, protocolo de transporte IP, endereço IP de destino, porta de destino) deve ser único. O SNAT de máscaras de porta pode ser usado com protocolos TCP ou UDP IP.

As portas efémeras (portas SNAT) são utilizadas para o conseguir depois de reescreverem o endereço IP de origem privada, porque vários fluxos provêm de um único endereço IP público. O algoritmo SNAT disfarçado de porta atribui portas SNAT de forma diferente para UDP versus TCP.

#### <a name="tcp-snat-ports"></a><a name="tcp"></a>Portas TCP SNAT

Uma porta SNAT é consumida por fluxo para um único endereço IP de destino, porta. Para vários fluxos de TCP para o mesmo endereço IP de destino, porta e protocolo, cada fluxo TCP consome uma única porta SNAT. Isto garante que os fluxos são únicos quando são originários do mesmo endereço IP público e vão para o mesmo endereço IP de destino, porto e protocolo. 

Fluxos múltiplos, cada um para um endereço IP de destino diferente, porta e protocolo, compartilham uma única porta SNAT. O endereço IP de destino, porta e protocolo tornam os fluxos únicos sem a necessidade de portas de origem adicionais para distinguir fluxos no espaço de endereço IP público.

#### <a name="udp-snat-ports"></a><a name="udp"></a>Portos UDP SNAT

As portas UDP SNAT são geridas por um algoritmo diferente das portas TCP SNAT.  O Balancer de Carga usa um algoritmo conhecido como "cone de cone restrito à porta NAT" para UDP.  Uma porta SNAT é consumida por cada fluxo, independentemente do endereço IP de destino, porta.

#### <a name="snat-port-reuse"></a>Reutilização portuária SNAT

Uma vez que uma porta tenha sido liberada, o porto está disponível para reutilização conforme necessário.  Pode pensar nas portas SNAT como uma sequência do mais baixo ao mais alto disponível para um determinado cenário, e a primeira porta SNAT disponível é usada para novas ligações. 
 
#### <a name="exhaustion"></a>Exaustão

Quando os recursos portuários SNAT estão esgotados, os fluxos de saída falham até que os fluxos existentes libertem as portas SNAT. O Balancer de Carga recupera as portas SNAT quando o fluxo se fecha e utiliza um [tempo de 4 minutos](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) para recuperar as portas SNAT dos fluxos inativos.

As portas UDP SNAT geralmente esgotam muito mais rapidamente do que as portas TCP SNAT devido à diferença de algoritmo utilizado. Você deve projetar e testar escala com esta diferença em mente.

Para padrões para mitigar condições que geralmente levam à exaustão da porta SNAT, reveja a secção [Managing SNAT.](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>Pré-aalocação do porto efémero para o SNAT de máscaras de porto (PAT)

O Azure utiliza um algoritmo para determinar o número de portas SNAT pré-locadas disponíveis com base no tamanho da piscina de backend ao utilizar o SNAT de máscaras de porta[(PAT).](#pat) As portas SNAT são portas efémeras disponíveis para um determinado endereço de fonte de IP público. Para cada endereço IP público associado a um balanceador de carga existem 64.000 portas disponíveis como portas SNAT para cada protocolo de transporte IP.

O mesmo número de portas SNAT é pré-alocado para UDP e TCP respectivamente e consumidos de forma independente por protocolo de transporte IP.  No entanto, a utilização da porta SNAT é diferente dependendo se o fluxo é UDP ou TCP.

>[!IMPORTANT]
>A programação padrão SKU SNAT é por protocolo de transporte IP e derivada da regra de equilíbrio de carga.  Se existir apenas uma regra de equilíbrio de carga TCP, o SNAT só está disponível para TCP. Se tiver apenas uma regra de equilíbrio de carga TCP e precisar de SNAT de saída para UDP, crie uma regra de equilíbrio de carga UDP do mesmo frontend para o mesmo pool de backend.  Isto irá desencadear a programação do SNAT para a UDP.  Não é necessária uma regra de trabalho ou uma sonda de saúde.  O SKU SNAT básico programa sempre o SNAT para ambos os protocolos de transporte IP, independentemente do protocolo de transporte especificado na regra de equilíbrio de carga.

Azure pré-loca portas SNAT para a configuração IP do NIC de cada VM. Quando uma configuração IP é adicionada à piscina, as portas SNAT são pré-locadas para esta configuração IP com base no tamanho da piscina de backend. Quando os fluxos de saída são criados, [o PAT](#pat) consome dinamicamente (até ao limite pré-alado) e liberta estas portas quando o fluxo se fecha ou acontece tempo [de inatividade.](../load-balancer/troubleshoot-outbound-connection.md#idletimeout)

A tabela a seguir mostra as pré-aallocations da porta SNAT para níveis de tamanhos de piscina de backend:

| Tamanho da piscina (instâncias VM) | Portas SNAT pré-locadas por configuração IP|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

>[!NOTE]
> Ao utilizar o Balanceador de Carga Padrão com [vários frontends,](load-balancer-multivip-overview.md)cada endereço IP frontend multiplica o número de portas SNAT disponíveis na tabela anterior. Por exemplo, um pool de backend de 50 VM's com 2 regras de equilíbrio de carga, cada um com um endereço IP frontal separado, usará portas SNAT 2048 (2x 1024) por regra. Consulte os detalhes para [vários frontends](#multife).

Lembre-se que o número de portas SNAT disponíveis não se traduz diretamente no número de fluxos. Uma única porta SNAT pode ser reutilizada para vários destinos únicos. As portas só são consumidas se for necessário para fazer fluxos únicos. Para orientação de conceção e mitigação, consulte a secção sobre [como gerir este recurso esgotável](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) e a secção que descreve [PAT](#pat).

Mudar o tamanho da sua piscina de backend pode afetar alguns dos seus fluxos estabelecidos. Se o tamanho da piscina de backend aumentar e transitar para o nível seguinte, metade das portas SNAT pré-locadas são recuperadas durante a transição para o próximo nível de piscina de backend maior. Os fluxos associados a uma porta SNAT recuperada irão esgotar-se e devem ser restabelecidos. Se for tentado um novo fluxo, o fluxo terá sucesso imediatamente enquanto as portas pré-locadas estiverem disponíveis.

Se o tamanho da piscina de backend diminuir e transitar para um nível inferior, o número de portas SNAT disponíveis aumenta. Neste caso, as portas SNAT existentes e os respetivos fluxos não são afetados.

As dotações portuárias SNAT são específicas do protocolo de transporte IP (TCP e UDP são mantidas separadamente) e são libertadas nas seguintes condições:

### <a name="tcp-snat-port-release"></a>Lançamento do porto TCP SNAT

- Se um dos servidores/clientes enviar a FINACK, a porta SNAT será libertada após 240 segundos.
- Se for visto um RST, a porta SNAT será libertada após 15 segundos.
- Se o tempo de 30 ó01 estiver atingido, a porta é libertada.

### <a name="udp-snat-port-release"></a>Lançamento do porto UDP SNAT

- Se o tempo de 30 ó01 estiver atingido, a porta é libertada.

## <a name="discovering-the-public-ip-that-a-vm-uses"></a><a name="discoveroutbound"></a>Descobrir o IP público que um VM usa
Existem muitas formas de determinar o endereço IP de fonte pública de uma ligação de saída. O OpenDNS fornece um serviço que lhe pode mostrar o endereço IP público do seu VM. 

Ao utilizar o comando nslookup, pode enviar uma consulta DNS para o nome myip.opendns.com para o openDns resolver. O serviço devolve o endereço IP de origem que foi utilizado para enviar a consulta. Quando executou a seguinte consulta a partir do seu VM, a resposta é o IP público utilizado para esse VM:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Prevenção da conectividade de saída
Às vezes é indesejável que um VM seja autorizado a criar um fluxo de saída. Ou pode haver uma exigência de gestão de quais destinos podem ser alcançados com fluxos de saída, ou quais os destinos que podem começar os fluxos de entrada. Neste caso, pode utilizar [grupos de segurança de rede](../virtual-network/security-overview.md) para gerir os destinos que o VM pode alcançar. Também pode usar NSGs para gerir qual destino público pode iniciar fluxos de entrada.

Quando aplicar um NSG a um VM equilibrado em carga, preste atenção às [etiquetas](../virtual-network/security-overview.md#service-tags) de serviço e às [regras de segurança predefinidas](../virtual-network/security-overview.md#default-security-rules). Deve certificar-se de que o VM pode receber pedidos de sonda de saúde do Azure Load Balancer. 

Se um NSG bloquear pedidos de sonda de saúde a partir da etiqueta padrão AZURE_LOADBALANCER, a sua sonda de saúde VM falha e o VM é marcado para baixo. O Balancer de Carga deixa de enviar novos fluxos para o VM.

## <a name="connections-to-azure-storage-in-the-same-region"></a>Ligações ao Armazenamento Azure na mesma região

Ter conectividade de saída através dos cenários acima não é necessário ligar ao Armazenamento na mesma região que o VM. Se não quiser, utilize grupos de segurança de rede (NSGs) como explicado acima. Para a conectividade com o Armazenamento noutras regiões, é necessária conectividade de saída. Por favor, note que ao ligar ao Armazenamento a partir de um VM na mesma região, o endereço IP de origem nos registos de diagnóstico de Armazenamento será um endereço interno do fornecedor, e não o endereço IP público do seu VM. Se pretender restringir o acesso à sua conta de Armazenamento a VMs numa ou mais sub-redes de Rede Virtual na mesma região, utilize [os pontos finais do serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md) e não o endereço IP público ao configurar a firewall da sua conta de armazenamento. Uma vez configurados os pontos finais do serviço, verá o seu endereço IP privado da Rede Virtual nos seus registos de diagnóstico de Armazenamento e não no endereço do fornecedor interno.

## <a name="limitations"></a>Limitações
- As funções do Web Worker roles sem um VNet e outros serviços da plataforma Microsoft podem ser acessíveis quando apenas um Balancer de Carga Standard interno é usado devido a um efeito colateral da forma como os serviços pré-VNet e outros serviços da plataforma funcionam. Não confie neste efeito colateral, uma vez que o próprio serviço ou a plataforma subjacente podem mudar sem aviso prévio. Deve sempre assumir que precisa de criar conectividade de saída explicitamente se desejar quando utilizar apenas um Balanceador de Carga Padrão interno. O cenário [padrão SNAT](#defaultsnat) 3 descrito neste artigo não está disponível.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Balancer de Carga Padrão](load-balancer-standard-overview.md).
- Saiba mais sobre [as regras de saída](load-balancer-outbound-rules-overview.md) para o Balancer de Carga público Padrão.
- Saiba mais sobre [o Balanceador de Carga.](load-balancer-overview.md)
- Saiba mais sobre [grupos de segurança de rede.](../virtual-network/security-overview.md)
- Conheça algumas das [outras principais capacidades de networking](../networking/networking-overview.md) em Azure.
