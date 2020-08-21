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
ms.date: 06/24/2020
ms.author: allensu
ms.openlocfilehash: 738b54d9fcd86313c2581c5d0f055a7cca8230b8
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88706069"
---
# <a name="outbound-connections-in-azure"></a>Ligações de saída no Azure

O Azure Load Balancer proporciona conectividade de saída através de diferentes mecanismos. Este artigo descreve os cenários e como geri-los. Se estiver a ter problemas com a conectividade de saída através de um Balançador de Carga Azure, consulte o [guia de resolução de problemas para ligações de saída](../load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE]
>Este artigo abrange as implementações do Gestor de Recursos. A Microsoft recomenda o Gestor de Recursos para cargas de trabalho de produção.

## <a name="terminology"></a>Terminologia

| Termo | Protocolos aplicáveis | Detalhes|
| ---------|---------| -------|
| Tradução de endereço de rede de origem (SNAT) | TCP, UDP | Uma implantação em Azure pode comunicar com pontos finais fora de Azure no espaço de endereço IP público. Quando uma instância inicia um fluxo de saída para um destino no espaço de endereço IP público, a Azure mapeia dinamicamente o endereço IP privado para um endereço IP público. Após a criação deste mapeamento, o tráfego de retorno para este fluxo originado de saída também pode chegar ao endereço IP privado de onde o fluxo originou. O Azure utiliza **tradução de endereço de rede de origem (SNAT)** para executar esta função.|
| SNAT de máscaras de porta (PAT)| TCP, UDP |  Quando vários endereços IP privados estão disfarçados atrás de um único endereço IP público, a Azure utiliza **tradução de endereços portuários (PAT)** para disfarçar/ocultar endereços IP privados. As portas efémeras são utilizadas para PAT e [são pré-locadas](#preallocatedports) com base no tamanho da piscina. Quando um recurso público do Balancer de Carga está associado a casos de VM, que não têm endereços IP públicos dedicados, cada fonte de ligação de saída é reescrita. A fonte é reescrita do espaço de endereço IP privado da rede virtual para o endereço IP público frontend do esídubo de carga. No espaço de endereço IP público, o 5-tuple do fluxo (endereço IP de origem, porta de origem, protocolo de transporte IP, endereço IP de destino, porta de destino) deve ser único. O SNAT de máscaras de porta pode ser usado com protocolos TCP ou UDP IP. As portas efémeras (portas SNAT) são utilizadas para o conseguir depois de reescreverem o endereço IP de origem privada, porque vários fluxos provêm de um único endereço IP público. O algoritmo SNAT disfarçado de porta atribui portas SNAT de forma diferente para UDP versus TCP.|
| Portos SNAT| TCP | As portas SNAT são portas efémeras disponíveis para um determinado endereço de fonte de IP público. Uma porta SNAT é consumida por fluxo para um único endereço IP de destino, porta. Para vários fluxos de TCP para o mesmo endereço IP de destino, porta e protocolo, cada fluxo TCP consome uma única porta SNAT. Isto garante que os fluxos são únicos quando são originários do mesmo endereço IP público e vão para o mesmo endereço IP de destino, porto e protocolo. Fluxos múltiplos, cada um para um endereço IP de destino diferente, porta e protocolo, compartilham uma única porta SNAT. O endereço IP de destino, porta e protocolo tornam os fluxos únicos sem a necessidade de portas de origem adicionais para distinguir fluxos no espaço de endereço IP público.|
|Portos SNAT | UDP | As portas UDP SNAT são geridas por um algoritmo diferente das portas TCP SNAT.  O Balancer de Carga usa um algoritmo conhecido como "cone de cone restrito à porta NAT" para UDP.  Uma porta SNAT é consumida por cada fluxo, independentemente do endereço IP de destino, porta.|
| Exaustão | - | Quando os recursos portuários SNAT estão esgotados, os fluxos de saída falham até que os fluxos existentes libertem as portas SNAT. O Balancer de Carga recupera as portas SNAT quando o fluxo se fecha e utiliza um [tempo de 4 minutos](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) para recuperar as portas SNAT dos fluxos inativos. As portas UDP SNAT geralmente esgotam muito mais rapidamente do que as portas TCP SNAT devido à diferença de algoritmo utilizado. Você deve projetar e testar escala com esta diferença em mente.|
| Comportamento de libertação da porta SNAT | TCP | Se um dos servidores/clientes enviar a FINACK, a porta SNAT será libertada após 240 segundos. Se for visto um RST, a porta SNAT será libertada após 15 segundos. Se o tempo de 30 ó01 estiver atingido, a porta é libertada.|
| Comportamento de libertação da porta SNAT | UDP |Se o tempo de 30 ó01 estiver atingido, a porta é libertada.|
| Reutilização portuária SNAT | TCP, UDP | Uma vez que uma porta tenha sido liberada, o porto está disponível para reutilização conforme necessário.  Pode pensar nas portas SNAT como uma sequência do mais baixo ao mais alto disponível para um determinado cenário, e a primeira porta SNAT disponível é usada para novas ligações.|

### <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Algoritmo de atribuição de portos

O Azure usa um algoritmo para determinar o número de portas SNAT pré-locadas disponíveis com base no tamanho da piscina de backend ao usar o PAT. Para cada endereço IP público associado a um balanceador de carga existem 64.000 portas disponíveis como portas SNAT para cada protocolo de transporte IP. O mesmo número de portas SNAT é pré-alocado para UDP e TCP respectivamente e consumidos de forma independente por protocolo de transporte IP.  No entanto, a utilização da porta SNAT é diferente dependendo se o fluxo é UDP ou TCP. Quando os fluxos de saída são criados, estas portas são consumidas dinamicamente (até ao limite pré-locado) e libertadas quando o fluxo se fecha ou o intervalo de [tempo](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) ocorrem. As portas só são consumidas se for necessário para fazer fluxos únicos.

#### <a name="default-snat-ports-allocated"></a><a name="snatporttable"></a> Portas SNAT predefinidas atribuídas

A tabela a seguir mostra as pré-aallocations da porta SNAT para níveis de tamanhos de piscina de backend:

| Tamanho da piscina (instâncias VM) | Portas SNAT pré-locadas por configuração IP |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

Alterar o tamanho da sua piscina de backend pode afetar alguns dos seus fluxos estabelecidos:

- Se o tamanho da piscina de backend aumentar e transitar para o nível seguinte, metade das portas SNAT pré-locadas são recuperadas durante a transição para o próximo nível de piscina de backend maior. Os fluxos associados a uma porta SNAT recuperada irão esgotar-se e devem ser restabelecidos. Se for tentado um novo fluxo, o fluxo terá sucesso imediatamente enquanto as portas pré-locadas estiverem disponíveis.
- Se o tamanho da piscina de backend diminuir e transitar para um nível inferior, o número de portas SNAT disponíveis aumenta. Neste caso, as portas SNAT existentes e os respetivos fluxos não são afetados.

## <a name="outbound-connections-scenario-overview"></a><a name="scenarios"></a>Visão geral do cenário de conexões de saída

| Cenário | Método | Protocolos IP | Descrição |
|  --- | --- | --- | --- |
|  1. VM com endereço IP público (com ou sem Balançador de Carga Azure | SNAT, porta disfarçada não utilizada | TCP, UDP, ICMP, ESP | O Azure utiliza o IP público atribuído à configuração IP do NIC da instância para todos os fluxos de saída. O caso tem todas as portas efémeras disponíveis. Não importa se o VM é equilibrado ou não. Este cenário tem precedência sobre os outros. Um IP público atribuído a um VM é uma relação 1:1 (em vez de 1: muitos) e implementado como um APÁtrida 1:1 NAT. |
| 2. Balanceador de Carga Pública associado a um VM (sem endereço IP público no VM/instância) | SNAT com máscara de porta (PAT) usando os frontends do balanceador de carga | TCP, UDP | Neste cenário, o recurso Balanceador de Carga deve ser configurado com uma regra do balançador de carga para criar uma ligação entre o frontend IP público com o pool de backend. Se não completar esta configuração de regra, o comportamento é descrito no cenário 3. Não é necessário que a regra tenha um ouvinte a trabalhar na piscina de backend para que a sonda de saúde tenha sucesso. Quando o VM cria um fluxo de saída, o Azure traduz o endereço IP de origem privada do fluxo de saída para o endereço IP público do frontend do Balancer de Carga público via SNAT. As portas efémeras do endereço IP público frontend do balançador de carga são utilizadas para distinguir os fluxos individuais originados pelo VM. O SNAT utiliza dinamicamente [portas efémeras pré-alitadas](#preallocatedports) quando os fluxos de saída são criados. Neste contexto, as portas efémeras utilizadas para o SNAT são chamadas portas SNAT. As portas SNAT são pré-atribuídas conforme descrito na tabela atribuída pelas [portas SNAT predefinidas](#snatporttable). |
| 3. VM (sem Balanceador de Carga, sem endereço IP público) ou VM associados ao Balanceador de Carga Interna Básico | SNAT com máscara de porto (PAT) | TCP, UDP | Quando o VM cria um fluxo de saída, o Azure traduz o endereço IP de origem privada do fluxo de saída para um endereço IP de fonte pública. Este endereço IP público não é **configurável,** não pode ser reservado, e não conta contra o limite de recursos IP públicos da subscrição. Se recolocar o conjunto de VM ou Availability set ou de escala de máquina virtual, este endereço IP público será lançado e um novo endereço IP público solicitado. Não utilize este cenário para listar endereços IP. Em vez disso, use o cenário 1 ou 2 onde declara explicitamente o comportamento de saída. As portas SNAT são pré-locadas como descrito na tabela atribuída pelas [portas SNAT predefinidas](#snatporttable).

## <a name="outbound-rules"></a><a name="outboundrules"></a>Regras de saída

 As regras de saída simplificam a configuração da tradução de endereços de rede de saída do [Balancer de Carga Padrão](load-balancer-standard-overview.md)do público.  Você tem controlo declarativo total sobre a conectividade de saída para escalar e afinar esta capacidade às suas necessidades específicas. Esta secção expande o cenário 2 (B) acima descrito.

![Regras de saída do Balanceador de Carga](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Com as regras de saída, pode utilizar o Balancer de Carga para definir o NAT de saída do zero. Também pode escalar e afinar o comportamento do NAT existente.

As regras de saída permitem-lhe controlar:

- que as máquinas virtuais devem ser traduzidas para os endereços IP públicos.
- como devem ser atribuídas portas SNAT de saída.
- quais protocolos para fornecer tradução de saída.
- qual a duração a utilizar para o intervalo de tempo de ligação de saída (4-120 minutos).
- se enviar um Reset TCP no tempo limite de marcha lenta
- protocolos de transporte TCP e UDP com uma única regra

### <a name="outbound-rule-definition"></a>Definição de regra de saída

Como todas as regras do Balanceador de Carga, as regras de saída seguem a mesma sintaxe familiar que o equilíbrio de carga e as regras NAT de entrada: reserva **de**  +  **parâmetros**  +  **frontend**. Uma regra de saída configura a saída DO NAT para _todas as máquinas virtuais identificadas pela piscina de backend_ para serem traduzidas para o _frontend_.  Os _parâmetros_ proporcionam um controlo adicional de grãos finos sobre o algoritmo NAT de saída.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> NAT de escala com vários endereços IP

Cada endereço IP adicional fornecido por um frontend fornece mais 64.000 portas efémeras para o Balancer de Carga usar como portas SNAT. Você pode usar vários endereços IP para planear cenários de grande escala e você pode usar regras de saída para mitigar padrões propensos à [exaustão SNAT.](troubleshoot-outbound-connection.md#snatexhaust)  

Também pode utilizar um [prefixo IP público](https://aka.ms/lbpublicipprefix) diretamente com uma regra de saída.  A utilização do prefixo IP público prevê uma escala mais fácil e uma listagem branca simplificada dos fluxos originários da sua implantação do Azure. Pode configurar uma configuração IP frontal dentro do recurso Balanceador de Carga para fazer referência a um prefixo de endereço IP público diretamente.  Isto permite o controlo exclusivo do Balancer de Carga sobre o prefixo IP público e a regra de saída utilizará automaticamente todos os endereços IP públicos contidos no prefixo IP público para ligações de saída.  Cada um dos endereços IP dentro do prefixo IP público fornece mais 64.000 portas efémeras por endereço IP para o Balancer de Carga para usar como portas SNAT.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Tempo de saída inativo e reset TCP

As regras de saída fornecem um parâmetro de configuração para controlar o tempo limite de saída e corresponder às necessidades da sua aplicação. Intervalos de tempo de saída inativos a 4 minutos. Pode aprender a [configurar intervalos de tempo inativos.](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout) O comportamento padrão do Balanceador de Carga é baixar o fluxo silenciosamente quando o tempo de saída tiver sido atingido.  Com o `enableTCPReset` parâmetro, pode ativar um comportamento de aplicação mais previsível e controlar se deve enviar o Reset TCP bidirecional (TCP RST) no momento fora do tempo limite de saída. [Reveja o TCP Reset no tempo limite de](https://aka.ms/lbtcpreset) marcha lenta para obter detalhes, incluindo a disponibilidade da região.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Prevenção da conectividade de saída

As regras de equilíbrio de carga fornecem programação automática do NAT de saída. No entanto, alguns cenários beneficiam ou obrigam-no a desativar a programação automática do NAT de saída pela regra de equilíbrio de carga para permitir controlar ou aperfeiçoar o comportamento.  
Pode utilizar este parâmetro de duas formas:

1. Supressão opcional da utilização do endereço IP de entrada para SNAT de saída através da desativação do SNAT de saída para uma regra de equilíbrio de carga
  
2. Sintonize os parâmetros SNAT de saída de um endereço IP utilizado para entrada e saída simultaneamente.  A programação de acesso automático nat deve ser desativada para permitir que uma regra de saída assuma o controlo.  Por exemplo, para alterar a atribuição portuária SNAT de um endereço também utilizado para a entrada, o `disableOutboundSnat` parâmetro deve ser definido como verdadeiro.  Se tentar utilizar uma regra de saída para redefinir os parâmetros de um endereço IP também utilizado para a entrada e não tiver libertado a programação DESA da regra de equilíbrio de carga, a operação para configurar uma regra de saída falhará.

>[!IMPORTANT]
> A sua máquina virtual não terá conectividade de saída se definir este parâmetro como verdadeiro e não tiver uma regra de saída para definir conectividade de saída.  Algumas operações do seu VM ou da sua aplicação podem depender de ter conectividade de saída disponível. Certifique-se de compreender as dependências do seu cenário e considerou o impacto de fazer esta mudança.

Às vezes é indesejável que um VM seja autorizado a criar um fluxo de saída. Ou pode haver uma exigência de gestão de quais destinos podem ser alcançados com fluxos de saída, ou quais os destinos que podem começar os fluxos de entrada. Neste caso, pode utilizar [grupos de segurança de rede](../virtual-network/security-overview.md) para gerir os destinos que o VM pode alcançar. Também pode usar NSGs para gerir qual destino público pode iniciar fluxos de entrada.

Quando aplicar um NSG a um VM equilibrado em carga, preste atenção às [etiquetas](../virtual-network/security-overview.md#service-tags) de serviço e às [regras de segurança predefinidas](../virtual-network/security-overview.md#default-security-rules). Deve certificar-se de que o VM pode receber pedidos de sonda de saúde do Azure Load Balancer.

Se um NSG bloquear pedidos de sonda de saúde a partir da etiqueta padrão AZURE_LOADBALANCER, a sua sonda de saúde VM falha e o VM é marcado para baixo. O Balancer de Carga deixa de enviar novos fluxos para o VM.

## <a name="scenarios-with-outbound-rules"></a>Cenários com regras de saída

| # | Cenário| Detalhes|
|---|---|---|
| I | Ligações de saída do noivo a um conjunto específico de endereços IP públicos| Você pode usar uma regra de saída para preparar ligações de saída para preparar ligações de saída para parecer ter origem de um conjunto específico de endereços IP públicos para facilitar cenários de whitelisting.  Este endereço IP público de origem pode ser o mesmo que usado por uma regra de equilíbrio de carga ou um conjunto diferente de endereços IP públicos do que o usado por uma regra de equilíbrio de carga.  1. Criar [prefixo IP público](https://aka.ms/lbpublicipprefix) (ou endereços IP públicos a partir do prefixo IP público) 2. Criar um Balanceador de Carga Padrão público 3. Crie frontends referentes ao prefixo IP público (ou endereços IP públicos) que pretende utilizar 4. Reutilizar uma piscina de backend ou criar uma piscina de backend e colocar os VMs numa piscina de backend do balanceador de carga pública 5. Configure uma regra de saída sobre o Balanceador de Carga público para programar NAT de saída para estes VMs usando os frontends. Se não desejar que a regra de equilíbrio de carga seja utilizada para a saída, tem de desativar o SNAT de saída na regra de equilíbrio de carga.
| II | Modificar a atribuição de porta SNAT| Pode utilizar as regras de saída para sintonizar a atribuição automática da [porta SNAT com base no tamanho da piscina de backend](load-balancer-outbound-connections.md#preallocatedports). Por exemplo, se tiver duas máquinas virtuais que partilham um único endereço IP público para o NAT de saída, pode desejar aumentar o número de portas SNAT atribuídas a partir das portas padrão de 1024 se estiver a sentir exaustão do SNAT. Cada endereço IP público pode contribuir com até 64.000 portos efémeros.  Se configurar uma regra de saída com um único endereço IP público, pode distribuir um total de 64.000 portas SNAT para VMs na piscina de backend.  Para dois VMs, um máximo de 32.000 portas SNAT podem ser atribuídas com uma regra de saída (2x 32.000 = 64.000). Pode utilizar regras de saída para sintonizar as portas SNAT atribuídas por padrão. Aloca mais ou menos do que a alocação portuária padrão do SNAT proporciona. Cada endereço IP público de todas as frentes de uma regra de saída contribui com até 64.000 portas efémeras para utilização como portas SNAT.  O Balancer de Carga aloca portas SNAT em múltiplos de 8. Se fornecer um valor não divisível até 8, a operação de configuração é rejeitada.  Se tentar alocar mais portas SNAT do que as disponíveis com base no número de endereços IP públicos, a operação de configuração é rejeitada.  Por exemplo, se alocar 10.000 portas por VM e 7 VMs num pool de backend partilharia um único endereço IP público, a configuração é rejeitada (7 x 10.000 portas SNAT > 64.000 portas SNAT).  Pode adicionar mais endereços IP públicos à parte frontal da regra de saída para ativar o cenário. Pode voltar à [atribuição predefinida da porta SNAT com base no tamanho da piscina de backend,](load-balancer-outbound-connections.md#preallocatedports) especificando 0 para o número de portas. Nesse caso, as primeiras 50 instâncias VM terão 1024 portas, 51-100 VM casos obterão 512 e assim por diante, de acordo com a [tabela](#snatporttable).|
| III| Ativar apenas saída | Você pode usar um balanceador de carga padrão público para fornecer NAT de saída para um grupo de VMs. Neste cenário, pode utilizar uma regra de saída por si só, sem necessidade de quaisquer regras adicionais.|
| IV | NAT de saída apenas para VMs (sem entrada) | Defina um Balancer de Carga Padrão público, coloque os VMs na piscina de backend e configuure uma regra de saída para programar o PROGRAMA NAT e prepare as ligações de saída a partir de um endereço IP público específico. Também pode utilizar um prefixo IP público que simplifica a origem das ligações de saída. 1. Criar um balanceador de carga padrão público. 2. Crie uma piscina de backend e coloque os VMs numa piscina de backend do balanceador de carga público. 3. Configure uma regra de saída sobre o Balanceador de Carga público para programar NAT de saída para estes VMs.
| V| NAT de saída para cenários internos do Balanceador de Carga Padrão| Ao utilizar um Balanceador de Carga Padrão interno, o NAT de saída não está disponível até que a conectividade de saída tenha sido explicitamente declarada. Pode definir a conectividade de saída utilizando uma regra de saída para criar conectividade de saída para VMs por trás de um Balanceador de Carga Padrão interno com estes passos: 1. Criar um balanceador de carga padrão público. 2. Crie uma piscina de backend e coloque os VMs num pool de backend do balanceador de carga público, para além do balançador de carga interno. 3. Configure uma regra de saída sobre o Balanceador de Carga público para programar NAT de saída para estes VMs. Para mais detalhes sobre este cenário, consulte [este exemplo](https://docs.microsoft.com/azure/load-balancer/egress-only). |
| VI | Ativar os protocolos de UDP & TCP para o NAT de saída com um Balanceador de Carga Padrão público | Ao utilizar um Balanceador de Carga Padrão público, a programação nat de saída automática fornecida corresponde ao protocolo de transporte da regra de equilíbrio de carga. 1. Desative o SNAT de saída na regra de equilíbrio de carga. 2. Configure uma regra de saída no mesmo Balanceador de Carga. 3. Reutilizar a piscina de backend já utilizada pelos seus VMs. 4. Especifique "protocolo": "Todos" como parte da regra de saída. Quando apenas são utilizadas regras NAT de entrada, não é fornecido nenhum NAT de saída. 1. Coloque VMs em uma piscina de backend. 2. Defina uma ou mais configurações IP frontend com endereço IP público(es) ou prefixo IP público 3. Configure uma regra de saída no mesmo Balanceador de Carga. 4. Especificar "protocolo": "Todos" como parte da regra de saída |


## <a name="limitations"></a>Limitações

- O número máximo de portas efémeras utilizáveis por endereço IP frontal é de 64.000.
- O intervalo de tempo de saída configurável é de 4 a 120 minutos (240 a 7200 segundos).
- O Balancer de Carga não suporta o ICMP para o NAT de saída.
- As regras de saída só podem ser aplicadas à configuração ip primária de um NIC.  Não é possível criar uma regra de saída para o IP secundário de um VM ou NVA. Vários NICs são suportados.
- As funções do Web Worker roles sem um VNet e outros serviços da plataforma Microsoft podem ser acessíveis quando apenas um Balancer de Carga Standard interno é usado devido a um efeito colateral da forma como os serviços pré-VNet e outros serviços da plataforma funcionam. Não confie neste efeito colateral, uma vez que o próprio serviço ou a plataforma subjacente podem mudar sem aviso prévio. Deve sempre assumir que precisa de criar conectividade de saída explicitamente se desejar quando utilizar apenas um Balanceador de Carga Padrão interno. O cenário 3 descrito neste artigo não está disponível.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Balancer de Carga Padrão](load-balancer-standard-overview.md).
- Consulte [as nossas perguntas frequentes sobre o Azure Load Balancer](load-balancer-faqs.md).
- Saiba mais sobre [as regras de saída](load-balancer-outbound-rules-overview.md) para o Balancer de Carga público Padrão.
- Saiba mais sobre [o Balanceador de Carga.](load-balancer-overview.md)
- Saiba mais sobre [grupos de segurança de rede.](../virtual-network/security-overview.md)
- Conheça algumas das [outras principais capacidades de networking](../networking/networking-overview.md) em Azure.
