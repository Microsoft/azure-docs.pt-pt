---
title: Ligações de saída
titleSuffix: Azure Load Balancer
description: Este artigo explica como o Azure permite que os VMs comuniquem com os serviços públicos de internet.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: contperfq1
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/30/2020
ms.author: allensu
ms.openlocfilehash: 0fcd0315afcbf38af2b8175deda748522cb335ec
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996856"
---
# <a name="outbound-connections"></a>Ligações de saída

O Azure Load Balancer proporciona conectividade de saída através de diferentes mecanismos. Este artigo descreve os cenários e como geri-los. 


## <a name="scenarios"></a>Cenários

* Máquina virtual com IP público.
* Máquina virtual sem IP público.
* Máquina virtual sem IP público e sem balanceador de carga padrão.

### <a name="virtual-machine-with-public-ip"></a><a name="scenario1"></a>Máquina virtual com IP público

| Associações | Método | Protocolos IP |
| ---------- | ------ | ------------ |
| Equilibrador de carga pública ou autónomo | [SNAT (Tradução de endereços de rede de origem)](#snat) </br> [PAT (Mascaramento portuário)](#pat) não utilizado. | TCP (Protocolo de Controlo de Transmissão) </br> UDP (Protocolo de Datagrama do Utilizador) </br> ICMP (Protocolo de Mensagem de Controlo de Internet) </br> ESP (Encapsulamento da Carga útil de segurança) |

#### <a name="description"></a>Description

O Azure utiliza o IP público atribuído à configuração IP do NIC da instância para todos os fluxos de saída. O caso tem todas as portas efémeras disponíveis. Não importa se o VM é equilibrado ou não. Este cenário tem precedência sobre os outros. 

Um IP público atribuído a um VM é uma relação 1:1 (em vez de 1: muitos) e implementado como um APÁtrida 1:1 NAT.

### <a name="virtual-machine-without-public-ip"></a><a name="scenario2"></a>Máquina virtual sem IP público

| Associações | Método | Protocolos IP |
| ------------ | ------ | ------------ |
| Balanceador de carga público | Utilização de frontend de balançador de carga para [SNAT](#snat) com [PAT (mascaramento de porta)](#pat).| TCP </br> UDP |

#### <a name="description"></a>Description

O recurso do balançador de carga está configurado com uma regra do balançador de carga. Esta regra é usada para criar uma ligação entre o frontend IP público com o pool backend. 

Se não completar esta configuração de regra, o comportamento é descrito no cenário 3. 

Uma regra com um ouvinte não é necessária para que a sonda de saúde tenha sucesso.

Quando um VM cria um fluxo de saída, o Azure traduz o endereço IP de origem para o endereço IP público do frontend do balançador de carga pública. Esta tradução é feita via [SNAT.](#snat) 

As portas efémeras do endereço IP público frontend do balançador de carga são utilizadas para distinguir os fluxos individuais originados pelo VM. O SNAT utiliza dinamicamente [portas efémeras pré-alitadas](#preallocatedports) quando os fluxos de saída são criados. 

Neste contexto, as portas efémeras utilizadas para o SNAT são chamadas portas SNAT. As portas SNAT são pré-atribuídas conforme descrito no [quadro de atribuição de portas SNAT predefinido](#snatporttable).

### <a name="virtual-machine-without-public-ip-and-without-standard-load-balancer"></a><a name="scenario3"></a>Máquina virtual sem IP público e sem balanceador de carga padrão

| Associações | Método | Protocolos IP |
| ------------ | ------ | ------------ |
|Nenhum </br> Balanceador de carga básico | [SNAT](#snat) com [máscara de porto (PAT)](#pat)| TCP </br> UDP | 

#### <a name="description"></a>Description

Quando o VM cria um fluxo de saída, o Azure traduz o endereço IP de origem para um endereço IP de origem pública. Este endereço IP público **não é configurável** e não pode ser reservado. Este endereço não conta com o limite de recursos IP públicos da subscrição. 

O endereço IP público será divulgado e um novo IP público solicitado se recolocar o: 

* Máquina Virtual
* Conjunto de disponibilidade
* Conjuntos de dimensionamento de máquinas virtuais  

Não utilize este cenário para adicionar IPs a uma lista de autorizações. Use o cenário 1 ou 2 onde declara explicitamente o comportamento de saída. As portas [SNAT](#snat) são pré-locadas como descrito na [tabela de atribuição de portas SNAT predefinido](#snatporttable).



## <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Algoritmo de atribuição de portos

O Azure usa um algoritmo para determinar o número de portas [SNAT](#snat) pré-locadas disponíveis. O algoritmo baseia o número de portas no tamanho da piscina de backend. 

Para cada endereço IP público associado a um equilibrador de carga, 64.000 portas estão disponíveis como portas [SNAT.](#snat) O mesmo número de portas [SNAT](#snat) são pré-locados para UDP e TCP. As portas são consumidas de forma independente por protocolo IP. 

O uso da porta [SNAT](#snat) é diferente dependendo se o fluxo é UDP ou TCP. 

As portas são consumidas dinamicamente até ao limite pré-alado.  As portas são libertadas quando o fluxo se fecha ou ocorre uma pausa de marcha lenta.

Para obter mais informações sobre os intervalos de tempo inativos, consulte [as ligações de saída de resolução de problemas no Esquilibr de Carga Azure](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) 

As portas só são consumidas se for necessário para fazer fluxos únicos.

### <a name="dynamic-snat-ports-preallocation"></a><a name="snatporttable"></a> Portas Dinâmicas SNAT pré-atoocação

A tabela a seguir mostra as pré-aallocations da porta [SNAT](#snat) para níveis de tamanhos de piscina de backend:

| Tamanho da piscina (instâncias VM) | Portas SNAT pré-locadas por configuração IP |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

Alterar o tamanho da sua piscina de backend pode afetar alguns dos seus fluxos estabelecidos:

- O tamanho da piscina backend aumenta as transições do gatilho para o nível seguinte. Metade das portas [SNAT](#snat) pré-locadas são recuperadas durante a transição para o nível seguinte. 

- Fluxos associados a um tempo limite de porta [SNAT](#snat) recuperado e fechado. Estes fluxos devem ser restabelecidos. Se for tentado um novo fluxo, o fluxo terá sucesso imediatamente enquanto as portas pré-locadas estiverem disponíveis.

- Se o tamanho da piscina de backend baixar e transitar para um nível inferior, o número de portas [SNAT](#snat) disponíveis aumenta. As portas [SNAT](#snat) existentes e os respetivos fluxos não são afetados.

## <a name="outbound-rules"></a><a name="outboundrules"></a>Regras de saída

 As regras de saída permitem a configuração da tradução de endereço de rede de saída do [balanceador de carga padrão](load-balancer-standard-overview.md)público.  

> [!NOTE]
> **O Azure Virtual Network NAT** pode fornecer conectividade de saída para máquinas virtuais numa rede virtual.  Veja [o que é Azure Virtual Network NAT?](../virtual-network/nat-overview.md)

Você tem total controlo declarativo sobre a conectividade de saída para escalar e afinar esta capacidade às suas necessidades.

![Regras de saída do balançador de carga](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Com as regras de saída, pode utilizar o equilibrador de carga para definir o NAT de saída do zero. Também pode escalar e afinar o comportamento do NAT existente.

As regras de saída permitem-lhe controlar:

- Quais máquinas virtuais devem ser traduzidas para quais endereços IP públicos.
- Como devem ser dadas portas [SNAT](#snat) de saída.
- Quais os protocolos para fornecer tradução de saída.
- Que duração utilizar para o intervalo de tempo de ligação de saída (4-100 minutos).
- Se enviar um Reset TCP no tempo limite de marcha lenta
- Protocolos de transporte de TCP e UDP com uma única regra

### <a name="outbound-rule-definition"></a>Definição de regra de saída

As regras de saída seguem a mesma sintaxe familiar que o equilíbrio de carga e as regras NAT de entrada: reserva **de**  +  **parâmetros**  +  **frontend**. Uma regra de saída configura a saída DO NAT para _todas as máquinas virtuais identificadas pela piscina de backend_ para serem traduzidas para o _frontend_.  Os _parâmetros_ proporcionam um controlo adicional de grãos finos sobre o algoritmo NAT de saída.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> NAT de escala com vários endereços IP

Cada endereço IP adicional fornecido por um frontend fornece mais 64.000 portas efémeras para o balanceador de carga utilizar como portas SNAT. 

Utilize vários endereços IP para planear cenários em larga escala. Utilize regras de saída para mitigar a [exaustão do SNAT](troubleshoot-outbound-connection.md#snatexhaust). 

Também pode utilizar um [prefixo IP público](https://aka.ms/lbpublicipprefix) diretamente com uma regra de saída. 

Um prefixo IP público aumenta o dimensionamento da sua implantação. O prefixo pode ser adicionado à lista de fluxos de permitir os fluxos originários dos seus recursos Azure. Pode configurar uma configuração IP frontal dentro do equilibrador de carga para fazer referência a um prefixo de endereço IP público.  

O equilibrador de carga tem controlo sobre o prefixo IP público. A regra de saída utilizará automaticamente todos os endereços IP públicos contidos no prefixo IP público para ligações de saída. 

Cada um dos endereços IP no prefixo IP público fornece mais 64.000 portas efémeras por endereço IP para o balanceador de carga a utilizar como portas SNAT.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Tempo de saída inativo e reset TCP

As regras de saída fornecem um parâmetro de configuração para controlar o tempo limite de saída e corresponder às necessidades da sua aplicação. Intervalos de tempo de saída inativos a 4 minutos. Para obter mais informações, consulte [configurar intervalos de tempo inativos](load-balancer-tcp-idle-timeout.md). 

O comportamento padrão do equilibrador de carga é baixar o fluxo silenciosamente quando o tempo de saída tiver sido atingido. O `enableTCPReset` parâmetro permite um comportamento e controlo de aplicação previsíveis. O parâmetro dita se deve enviar o Reset TCP bidirecional (TCP RST) no tempo limite do tempo de saída. 

[Reveja o TCP Reset no tempo limite de](https://aka.ms/lbtcpreset) marcha lenta para obter detalhes, incluindo a disponibilidade da região.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Prevenção da conectividade de saída

As regras de equilíbrio de carga fornecem uma programação automática do NAT de saída. Alguns cenários beneficiam ou obrigam a desativar a programação automática do NAT de saída pela regra de equilíbrio de carga. Desativar através da regra permite-lhe controlar ou refinar o comportamento.  

Pode utilizar este parâmetro de duas formas:

1. Prevenção do endereço IP de entrada para o SNAT de saída. Desative o SNAT de saída na regra de equilíbrio de carga.
  
2. Sintonize os parâmetros [SNAT](#snat) de saída de um endereço IP utilizado para entrada e saída simultaneamente. O NAT de saída automática deve ser desativado para permitir que uma regra de saída assuma o controlo. Para alterar a atribuição da porta SNAT de um endereço também utilizado para a entrada, o `disableOutboundSnat` parâmetro deve ser definido como verdadeiro. 

A operação para configurar uma regra de saída falhará se tentar redefinir um endereço IP que é utilizado para a entrada.  Desative primeiro o NAT de saída da regra de equilíbrio de carga.

>[!IMPORTANT]
> A sua máquina virtual não terá conectividade de saída se definir este parâmetro como verdadeiro e não tiver uma regra de saída para definir conectividade de saída.  Algumas operações do seu VM ou da sua aplicação podem depender de ter conectividade de saída disponível. Certifique-se de compreender as dependências do seu cenário e considerou o impacto de fazer esta mudança.

Às vezes é indesejável que um VM crie um fluxo de saída. Pode haver uma exigência de gestão dos destinos que recebem fluxos de saída, ou quais os destinos que iniciam os fluxos de entrada. Utilize [grupos de segurança](../virtual-network/security-overview.md) de rede para gerir os destinos que o VM atinge. Utilize NSGs para gerir quais destinos públicos iniciam fluxos de entrada.

Quando aplicar um NSG a um VM equilibrado em carga, preste atenção às [etiquetas](../virtual-network/security-overview.md#service-tags) de serviço e às [regras de segurança predefinidas](../virtual-network/security-overview.md#default-security-rules). Certifique-se de que o VM pode receber pedidos de sonda de saúde do Azure Load Balancer.

Se um NSG bloquear pedidos de sonda de saúde a partir da etiqueta predefinição AZURE_LOADBALANCER, a sua sonda de saúde VM falha e o VM está marcado indisponível. O Balancer de Carga deixa de enviar novos fluxos para o VM.

## <a name="scenarios-with-outbound-rules"></a>Cenários com regras de saída

### <a name="outbound-rules-scenarios"></a>Cenários de regras de saída

* Configure as ligações de saída a um conjunto específico de IPs ou prefixo público.
* Modificar a atribuição do porto [SNAT.](#snat)
* Ativar apenas a saída.
* NAT de saída apenas para VMs (sem entrada).
* NAT de saída para o equilibrador de carga padrão interno.
* Ativar os protocolos de UDP & TCP para o NAT de saída com um balanceador de carga padrão público.

### <a name="configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Configure as ligações de saída a um conjunto específico de IPs ou prefixo público

#### <a name="details"></a>Detalhes

Utilize este cenário para personalizar as ligações de saída a partir de um conjunto de endereços IP públicos. Adicione iPs públicos ou prefixos a uma lista de permitir ou negar com base na origem.

Este IP ou prefixo público pode ser o mesmo que usado por uma regra de equilíbrio de carga. 

Para utilizar um IP ou prefixo público diferente do utilizado por uma regra de equilíbrio de carga:  

1. Crie prefixo IP público ou endereço IP público.
2. Criar um balanceador de carga padrão público 
3. Crie um frontend referente ao prefixo IP público ou ao endereço IP público que pretende utilizar. 
4. Reutilizar uma piscina de backend ou criar uma piscina de backend e colocar os VMs numa piscina de backend do equilibrador de carga pública
5. Configure uma regra de saída no equilibrador de carga pública para permitir que os VMs de saída utilizem o frontend. Se não desejar que a regra de equilíbrio de carga seja utilizada para a saída, desative o SNAT de saída na regra de equilíbrio de carga.

### <a name="modify-snat-port-allocation"></a><a name="scenario2out"></a>Modificar a atribuição de porta [SNAT](#snat)

#### <a name="details"></a>Detalhes

Pode utilizar as regras de saída para sintonizar a atribuição automática da [porta SNAT com base no tamanho da piscina de backend](load-balancer-outbound-connections.md#preallocatedports). 

Se sentir esgotamento do SNAT, aumente o número de portas [SNAT](#snat) dadas a partir do padrão de 1024. 

Cada endereço IP público contribui com até 64.000 portos efémeros. O número de VMs na piscina de backend determina o número de portas distribuídas por cada VM. Um VM na piscina de backend tem acesso ao máximo de 64.000 portas. Para dois VMs, um máximo de 32.000 portas [SNAT](#snat) podem ser dadas com uma regra de saída (2x 32.000 = 64.000). 

Pode utilizar regras de saída para sintonizar as portas SNAT dadas por defeito. Você dá mais ou menos do que a alocação padrão da porta [SNAT](#snat) fornece. Cada endereço IP público a partir de uma regra de saída contribui com até 64.000 portas efémeras para utilização como portas [SNAT.](#snat)  

O equilibrador de carga dá portas [SNAT](#snat) em múltiplos de 8. Se fornecer um valor não divisível até 8, a operação de configuração é rejeitada. 

Se tentar dar mais portas [SNAT](#snat) do que as disponíveis com base no número de endereços IP públicos, a operação de configuração é rejeitada.

Se você der 10.000 portas por VM e sete VMs em um pool backend compartilhar um único IP público, a configuração é rejeitada. Sete multiplicados por 10.000 ultrapassa o limite de 64.000 portos. Adicione mais endereços IP públicos ao frontend da regra de saída para ativar o cenário. 

Reverter para a [atribuição de porta padrão](load-balancer-outbound-connections.md#preallocatedports) especificando 0 para o número de portas. As primeiras 50 instâncias VM terão 1024 portas, 51-100 VM instâncias chegarão a 512 até ao máximo de ocorrências.  Para obter mais informações sobre a atribuição por padrão da porta SNAT, consulte a [tabela de atribuição de portas SNAT](#snatporttable).

### <a name="enable-outbound-only"></a><a name="scenario3out"></a>Ativar apenas saída

#### <a name="details"></a>Detalhes

Utilize um equilibrador de carga padrão público para fornecer NAT de saída para um grupo de VMs. Neste cenário, use uma regra de saída por si só, sem necessidade de quaisquer regras adicionais.

> [!NOTE]
> **O Azure Virtual Network NAT** pode fornecer conectividade de saída para máquinas virtuais sem a necessidade de um equilibrador de carga.  Veja [o que é Azure Virtual Network NAT?](../virtual-network/nat-overview.md)

### <a name="outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>NAT de saída apenas para VMs (sem entrada)

> [!NOTE]
> **O Azure Virtual Network NAT** pode fornecer conectividade de saída para máquinas virtuais sem a necessidade de um equilibrador de carga.  Veja [o que é Azure Virtual Network NAT?](../virtual-network/nat-overview.md)

#### <a name="details"></a>Detalhes

Para este cenário:

1. Crie um IP ou prefixo público.
2. Criar um balanceador de carga padrão público. 
3. Crie um frontend associado ao IP público ou prefixo dedicado à saída.
4. Crie uma piscina de backend para os VMs.
5. Coloque os VMs na piscina de backend.
6. Configure uma regra de saída para permitir a saída do NAT.

Utilize um prefixo ou IP público para escalar portas [SNAT.](#snat) Adicione a fonte de ligações de saída a uma lista de permitir ou negar.

### <a name="outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>NAT de saída para equilibrador de carga padrão interno

> [!NOTE]
> **O Azure Virtual Network NAT** pode fornecer conectividade de saída para máquinas virtuais utilizando um equilibrador de carga padrão interno.  Veja [o que é Azure Virtual Network NAT?](../virtual-network/nat-overview.md)

#### <a name="details"></a>Detalhes

A conectividade de saída não está disponível para um balanceador de carga padrão interno até que tenha sido explicitamente declarado. 

Para obter mais informações, consulte [a configuração do balanceador de carga apenas de saída](https://docs.microsoft.com/azure/load-balancer/egress-only).


### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Ativar os protocolos de UDP & TCP para o NAT de saída com um balanceador de carga padrão público

#### <a name="details"></a>Detalhes

Ao utilizar um balanceador de carga padrão público, o NAT de saída automático fornecido corresponde ao protocolo de transporte da regra de equilíbrio de carga. 

1. Desative o [SNAT](#snat) de saída na regra de equilíbrio de carga. 
2. Configure uma regra de saída no mesmo equilibrador de carga.
3. Reutilizar a piscina de backend já utilizada pelos seus VMs. 
4. Especifique "protocolo": "Todos" como parte da regra de saída. 

Quando apenas são utilizadas regras NAT de entrada, não é fornecido nenhum NAT de saída. 

1. Coloque VMs em uma piscina de backend.
2. Definir uma ou mais configurações IP frontend com endereço IP público(es) ou prefixo IP público 
3. Configure uma regra de saída no mesmo equilibrador de carga. 
4. Especificar "protocolo": "Todos" como parte da regra de saída

## <a name="terminology"></a><a name="terms"></a> Terminologia

### <a name="source-network-address-translation"></a><a name="snat"></a>Tradução de endereço de rede de origem

| Protocolos aplicáveis |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>Detalhes

Uma implantação em Azure pode comunicar com pontos finais fora de Azure no espaço de endereço IP público.

Quando um caso inicia o tráfego de saída para um destino com um endereço ip público, o Azure mapeia dinamicamente o endereço IP privado do recurso para um endereço IP público. 

Após a criação deste mapeamento, o tráfego de retorno para este fluxo originado de saída atinge o endereço IP privado de onde o fluxo originou. 

O Azure utiliza **tradução de endereço de rede de origem (SNAT)** para fazer esta função.

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>SNAT de máscaras de porta (PAT)

| Protocolos aplicáveis |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>Detalhes

Quando os IPs privados estão por detrás de um único endereço IP público, o Azure utiliza **a tradução de endereços portuários (PAT)** para ocultar os endereços IP privados. 

As portas efémeras são utilizadas para PAT e [são pré-locadas](#preallocatedports) com base no tamanho da piscina. 

Quando um balançador de carga pública está associado a VMs sem IPs públicos, cada fonte de ligação de saída é reescrita. 

A fonte é reescrita do endereço IP privado da rede virtual para o endereço IP público frontal do esídubo de carga. 

No espaço de endereço IP público, o cinco tuple do fluxo deve ser único:

* Endereço IP de origem
* Porta de origem
* Protocolo de transporte IP
* Endereço IP de destino
* Porta de destino 

O SNAT de máscaras de porta pode ser usado com protocolos TCP ou UDP. As portas SNAT são utilizadas após reescrever o endereço IP de origem privada porque vários fluxos provêm de um único endereço IP público. O algoritmo SNAT disfarçado de porta dá portas SNAT de forma diferente para UDP versus TCP.

### <a name="snat-ports-tcp"></a>Portas SNAT (TCP)

| Protocolos aplicáveis |
|------------------------|
| TCP |

#### <a name="details"></a>Detalhes

As portas SNAT são portas efémeras disponíveis para um endereço de fonte ip público. Uma porta SNAT é consumida por fluxo para um único endereço IP de destino e porta. 

Para vários fluxos de TCP para o mesmo endereço IP de destino, porta e protocolo, cada fluxo TCP consome uma única porta SNAT. 

Este consumo garante que os fluxos são únicos quando provêm do mesmo endereço IP público e viajam para:

* Mesmo endereço IP de destino
* Porta
* Protocolo 

Vários fluxos partilham uma única porta SNAT. 

O endereço IP de destino, porta e protocolo tornam os fluxos únicos sem a necessidade de portas de origem adicionais para distinguir fluxos no espaço de endereço IP público.


### <a name="snat-ports-udp"></a>Portas SNAT (UDP)

| Protocolos aplicáveis |
|------------------------|
| UDP |

#### <a name="details"></a>Detalhes

As portas UDP SNAT são geridas por um algoritmo diferente das portas TCP SNAT.  O balanceador de carga usa um algoritmo chamado cone restrito à porta NAT para UDP.

Uma porta SNAT é consumida para qualquer endereço IP de destino e porta para cada fluxo.


### <a name="exhaustion"></a><a name="exhaustion"></a>Exaustão

| Protocolos aplicáveis |
|------------------------|
| N/D |

#### <a name="details"></a>Detalhes

Quando os recursos portuários SNAT estão esgotados, os fluxos de saída falham até que os fluxos existentes libertem as portas SNAT. O balançador de carga recupera as portas SNAT quando o fluxo se fecha.

Um tempo de 4 minutos [inativo](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) é utilizado pelo equilibrador de carga para recuperar as portas SNAT.

As portas UDP SNAT geralmente esgotam muito mais rapidamente do que as portas TCP SNAT devido à diferença no algoritmo usado. Teste de design e escala por causa desta diferença.

### <a name="snat-port-release-behavior-tcp"></a>Comportamento de libertação da porta SNAT (TCP)

| Protocolos aplicáveis |
|------------------------|
| TCP |

#### <a name="details"></a>Detalhes

Quando um servidor ou cliente envia um FINACK, uma porta SNAT será libertada após 240 segundos. No caso de ser visto um RST, uma porta SNAT será libertada após 15 segundos. Se o tempo de 30 minutos tiver sido atingido, a porta será libertada.

### <a name="snat-port-release-behavior-udp"></a>Comportamento de libertação da porta SNAT (UDP)

| Protocolos aplicáveis |
|------------------------|
| TCP |

#### <a name="details"></a>Detalhes

Se o tempo de 30 minutos tiver sido atingido, a porta será libertada.

### <a name="snat-port-reuse"></a>Reutilização portuária SNAT

| Protocolos aplicáveis |
|------------------------|
| TCP </br> UDP |

#### <a name="details"></a>Detalhes

Uma vez que uma porta tenha sido liberada, o porto está disponível para reutilização. As portas SNAT são uma sequência de mais baixo para o mais alto disponível para um determinado cenário, e a primeira porta SNAT disponível é usada para novas ligações.

## <a name="limitations"></a>Limitações

- O número máximo de portas efémeras utilizáveis por endereço IP frontal é de 64.000.
- O intervalo de tempo de saída configurável é de 4 a 120 minutos (240 a 7200 segundos).
- O equilibrador de carga não suporta o ICMP para o NAT de saída.
- As regras de saída só podem ser aplicadas à configuração ip primária de um NIC.  Não é possível criar uma regra de saída para o IP secundário de um VM ou NVA. Vários NICs são suportados.
- As funções do Web Worker Roles sem uma rede virtual e outros serviços da plataforma Microsoft podem ser acessíveis quando um balanceador de carga padrão interno é usado. Esta acessibilidade deve-se a um efeito colateral do funcionamento dos serviços pré-VNet e de outros serviços da plataforma. Não confie neste efeito colateral, uma vez que o próprio serviço ou a plataforma subjacente podem mudar sem aviso prévio. Assuma sempre que precisa de criar conectividade de saída explicitamente se quiser quando utilizar apenas um balanceador de carga padrão interno. O cenário 3 descrito neste artigo não está disponível.

## <a name="next-steps"></a>Passos seguintes

Se estiver a ter problemas com a conectividade de saída através de um Equilibrador de Carga Azure, consulte o [guia de resolução de problemas para ligações de saída](../load-balancer/troubleshoot-outbound-connection.md).

- Saiba mais sobre o [balanceador de carga padrão](load-balancer-standard-overview.md).
- Consulte [as nossas perguntas frequentes sobre o Azure Load Balancer](load-balancer-faqs.md).
- Saiba mais sobre [as regras de saída](load-balancer-outbound-rules-overview.md) para o balanceador de carga público padrão.

