---
title: Tradução de endereço de rede de origem (SNAT) para ligações de saída
titleSuffix: Azure Load Balancer
description: Saiba como o Azure Load Balancer é utilizado para a conectividade de internet de saída (SNAT).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 99f15afdab917fe28e22df8cb0e372b6c30c8526
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027334"
---
# <a name="using-source-network-address-translation-snat-for-outbound-connections"></a>Utilização de Tradução de Endereços de Rede Fonte (SNAT) para ligações de saída

Os IPs frontend de um equilibrador de carga pública Azure podem ser usados para fornecer conectividade de saída à internet para instâncias de backend. Esta configuração utiliza **tradução de endereço de rede de origem (SNAT)**. O SNAT reescreve o endereço IP do backend para o endereço IP público do seu equilibrador de carga. 

O SNAT permite **mascarar IP** da instância backend. Esta mascarada impede que fontes externas tenham um endereço direto para as instâncias de backend. Um endereço IP partilhado entre instâncias de backend reduz o custo de IPs públicos estáticos. Um endereço IP conhecido suporta cenários como simplificar a lista de autorizações IP com tráfego de IPs públicos conhecidos. 

>[!Note]
> Para aplicações que requerem um grande número de ligações de saída ou clientes empresariais que exigem que um único conjunto de IPs sejam usados a partir de uma determinada rede virtual, [o Virtual Network NAT](../virtual-network/nat-overview.md) é a solução recomendada. A sua alocação dinâmica permite uma configuração simples e a utilização mais eficiente das portas SNAT a partir de cada endereço IP. Permite que todos os recursos da rede virtual partilhem um conjunto de endereços IP sem necessidade de partilhar um equilibrador de carga.

>[!Important]
> Mesmo sem o SNAT de saída configurado, as contas de armazenamento do Azure na mesma região continuarão a ser acessíveis e os recursos de backend continuarão a ter acesso a serviços da Microsoft, como o Windows Updates.

>[!NOTE] 
>Este artigo abrange apenas as implementações do Azure Resource Manager. Reveja [as ligações de saída (Classic)](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic) para todos os cenários clássicos de implantação em Azure.

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> Partilha de endereço IP frontend através de recursos backend

Se os recursos de backend de um balanceador de carga não tiverem endereços IP públicos de nível de instância (ILPIP), eles estabelecem conectividade de saída através do IP frontal do esquilibrista de carga pública. As portas são usadas para gerar identificadores únicos usados para manter fluxos distintos. A internet usa um cinco tuple para fornecer esta distinção.

O cinco tuple consiste em:

* IP de destino
* Porta de destino
* IP de origem
* Fonte portuária e protocolo para fornecer esta distinção.

Se uma porta for utilizada para ligações de entrada, tem um **ouvinte** para pedidos de ligação de entrada nessa porta. Aquela porta não pode ser usada para ligações de saída. Para estabelecer uma ligação de saída, é utilizado um **porto efémero** para fornecer ao destino um porto para comunicar e manter um fluxo de tráfego distinto. Quando estas portas efémeras são usadas para snat, são chamadas **portas SNAT** 

Por definição, cada endereço IP tem 65.535 portas. Cada porta pode ser utilizada para ligações de entrada ou saída para TCP (Protocolo de Controlo de Transmissão) e UDP (Protocolo de Datagrama do Utilizador). 

Quando um endereço IP público é adicionado como um FRONTend IP a um balanceador de carga, a Azure dá 64.000 portas elegíveis para SNAT.

>[!NOTE]
> Cada porta utilizada para uma regra DE NA de equilíbrio de carga ou entrada consumirá uma gama de oito portas destes 64.000 portos, reduzindo o número de portos elegíveis para o SNAT. Se uma regra de equilíbrio de carga ou nat estiver na mesma gama de oito que outra, não consumirá portas adicionais. 

Através [de regras de saída](./outbound-rules.md) e regras de equilíbrio de carga, estas portas SNAT podem ser distribuídas para backend instances para permitir-lhes partilhar os IPs públicos do equilibrador de carga para ligações de saída.

Quando [o cenário 2](#scenario2) abaixo estiver configurado, o anfitrião de cada instância de backend irá pacotes SNAT que fazem parte de uma ligação de saída. 

Ao fazer SNAT numa ligação de saída a partir de uma instância de backend, o anfitrião reescreve o IP de origem para um dos IPs frontend. 

Para manter fluxos únicos, o hospedeiro reescreve a porta de origem de cada pacote de saída para uma porta SNAT na instância de backend.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>Comportamento de ligação de saída para diferentes cenários
  * Máquina virtual com IP público.
  * Máquina virtual sem IP público.
  * Máquina virtual sem IP público e sem balanceador de carga padrão.
        
 ### <a name="scenario-1-virtual-machine-with-public-ip"></a><a name="scenario1"></a> Cenário 1: Máquina virtual com IP público

 | Associações | Método | Protocolos IP |
 | ---------- | ------ | ------------ |
 | Equilibrador de carga pública ou autónomo | [SNAT (Tradução de endereços de rede de origem)](#snat) </br> não usado. | TCP (Protocolo de Controlo de Transmissão) </br> UDP (Protocolo de Datagrama do Utilizador) </br> ICMP (Protocolo de Mensagem de Controlo de Internet) </br> ESP (Encapsulamento da Carga útil de segurança) |

 #### <a name="description"></a>Description

 O Azure utiliza o IP público atribuído à configuração IP do NIC da instância para todos os fluxos de saída. O caso tem todas as portas efémeras disponíveis. Não importa se o VM é equilibrado ou não. Este cenário tem precedência sobre os outros. 

 Um IP público atribuído a um VM é uma relação 1:1 (em vez de 1: muitos) e implementado como um APÁtrida 1:1 NAT.

 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>Cenário 2: Máquina virtual sem IP público e atrás do Balanceador público Padrão de Carga

 | Associações | Método | Protocolos IP |
 | ------------ | ------ | ------------ |
 | Balanceador de carga pública padrão | Utilização de IPs frontend do balançador de carga para [SNAT](#snat).| TCP </br> UDP |

 #### <a name="description"></a>Description

 O recurso do balançador de carga é configurado com uma regra de saída ou uma regra de equilíbrio de carga que permite o SNAT. Esta regra é usada para criar uma ligação entre o frontend IP público com o pool backend. 

 Se não completar esta configuração de regra, o comportamento é descrito no cenário 3. 

 Uma regra com um ouvinte não é necessária para que a sonda de saúde tenha sucesso.

 Quando um VM cria um fluxo de saída, o Azure traduz o endereço IP de origem para o endereço IP público do frontend do balançador de carga pública. Esta tradução é feita via [SNAT.](#snat) 

 As portas efémeras do endereço IP público frontend do balançador de carga são utilizadas para distinguir os fluxos individuais originados pelo VM. O SNAT utiliza dinamicamente [portas efémeras pré-alitadas](#preallocatedports) quando os fluxos de saída são criados. 

 Neste contexto, as portas efémeras utilizadas para o SNAT são chamadas portas SNAT. É altamente recomendado que uma [regra de saída](./outbound-rules.md) seja explicitamente configurada. Se utilizar o SNAT predefinido através de uma regra de equilíbrio de carga, as portas SNAT são pré-atribuídas conforme descrito no [quadro de atribuição de portas SNAT predefinido](#snatporttable).

> [!NOTE]
> **O Azure Virtual Network NAT** pode fornecer conectividade de saída para máquinas virtuais sem a necessidade de um equilibrador de carga. Veja [o que é Azure Virtual Network NAT?](../virtual-network/nat-overview.md)

 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-standard-internal-load-balancer"></a><a name="scenario3"></a>Cenário 3: Máquina virtual sem IP público e atrás do Balanceador de Carga Interno Standard

 | Associações | Método | Protocolos IP |
 | ------------ | ------ | ------------ |
 | Balanceador de carga interno padrão | Sem conectividade na Internet.| Nenhum |

 #### <a name="description"></a>Description
 
Ao utilizar um balanceador interno standard, não existe a utilização de endereços IP efémeros para O SNAT. Esta funcionalidade suporta a segurança por defeito. Esta funcionalidade garante que todos os endereços IP utilizados pelos recursos são configuráveis e podem ser reservados. 

Para alcançar a conectividade de saída para a internet ao utilizar um balanceador de carga interno Standard, configuure um endereço IP público de nível de instância para seguir o comportamento no [cenário 1](#scenario1). 

Outra opção é adicionar as instâncias de backend a um balanceador de carga público Standard com uma regra de saída configurada. As instâncias de backend são adicionadas a um equilibrador de carga interno para o equilíbrio interno da carga. Esta implementação segue o comportamento no [cenário 2](#scenario2). 

> [!NOTE]
> **O Azure Virtual Network NAT** pode fornecer conectividade de saída para máquinas virtuais sem a necessidade de um equilibrador de carga. Veja [o que é Azure Virtual Network NAT?](../virtual-network/nat-overview.md)

 ### <a name="scenario-4-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario4"></a>Cenário 4: Máquina virtual sem IP público e por trás do Balanceador de Carga Básico

 | Associações | Método | Protocolos IP |
 | ------------ | ------ | ------------ |
 |Nenhum </br> Balanceador de carga básico | [SNAT](#snat) com endereço IP dinâmico de nível de instância| TCP </br> UDP | 

 #### <a name="description"></a>Description

 Quando o VM cria um fluxo de saída, o Azure traduz o endereço IP de origem para um endereço IP de fonte pública dinamicamente dado. Este endereço IP público **não é configurável** e não pode ser reservado. Este endereço não conta com o limite de recursos IP públicos da subscrição. 

O endereço IP público será divulgado e um novo IP público solicitado se recolocar o: 

 * Máquina Virtual
 * Conjunto de disponibilidade
 * Conjuntos de dimensionamento de máquinas virtuais 

 Não utilize este cenário para adicionar IPs a uma lista de admissões. Use o cenário 1 ou 2 onde declara explicitamente o comportamento de saída. As portas [SNAT](#snat) são pré-locadas como descrito na [tabela de atribuição de portas SNAT predefinido](#snatporttable).

## <a name="exhausting-ports"></a><a name="scenarios"></a> Portas exaustivas

Todas as ligações ao mesmo destino IP e porto de destino usarão uma porta SNAT. Esta ligação mantém um fluxo de **tráfego** distinto da instância de backend ou **cliente** para um **servidor**. Este processo dá ao servidor uma porta distinta para abordar o tráfego. Sem este processo, a máquina cliente desconhece de que fluxo um pacote faz parte.

Imagine ter vários navegadores https://www.microsoft.com indo, que é:

* Destino IP = 23.53.254.142
* Porto de Destino = 443
* Protocolo = TCP

Sem diferentes portas de destino para o tráfego de retorno (a porta SNAT utilizada para estabelecer a ligação), o cliente não terá forma de separar um resultado de consulta de outro.

As ligações de saída podem rebentar. Uma instância de backend pode ser atribuída portos insuficientes. Sem **a reutilização da ligação** ativada, o risco de **exaustão portuária** SNAT é aumentado.

Novas ligações de saída para um IP de destino falharão quando ocorrer a exaustão do porto. As ligações terão sucesso quando uma porta estiver disponível. Este esgotamento ocorre quando as 64.000 portas de um endereço IP são espalhadas finamente em muitas instâncias de backend. Para obter orientações sobre a mitigação da exaustão do porto SNAT, consulte o [guia de resolução de problemas](./troubleshoot-outbound-connection.md).  

Para as ligações TCP, o equilibrador de carga utilizará uma única porta SNAT para cada destino IP e porta. Este multiuso permite múltiplas ligações ao mesmo destino IP com a mesma porta SNAT. Este multiuso é limitado se a ligação não for para diferentes portas de destino.

Para ligações UDP, o balanceador de carga utiliza um algoritmo **NAT cone restrito por porta,** que consome uma porta SNAT por destino IP qualquer que seja a porta de destino. 

Uma porta é reutilizada para um número ilimitado de ligações. A porta só é reutilizada se o destino IP ou porta for diferente.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Alocação portuária predefinido

Cada IP público atribuído como um IP frontal do seu balanceador de carga recebe 64.000 portas SNAT para os seus membros de pool backend. Os portos não podem ser partilhados com membros da piscina de backend. Uma gama de portas SNAT só pode ser utilizada por uma única instância de backend para garantir que os pacotes de devolução são encaminhados corretamente. 

Recomenda-se que utilize uma regra de saída explícita para configurar a atribuição de porta SNAT. Esta regra maximizará o número de portas SNAT que cada instância de backend tem disponível para ligações de saída. 

Se utilizar a atribuição automática de SNAT de saída através de uma regra de equilíbrio de carga, a tabela de atribuição definirá a sua atribuição portuária.

A tabela a seguir <a name="snatporttable"></a> mostra as pré-aallocations da porta SNAT para níveis de tamanhos de piscina de backend:

| Tamanho da piscina (instâncias VM) | Portas SNAT pré-locadas por configuração IP |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 | 

>[!NOTE]
> Se tiver uma piscina de backend com um tamanho máximo de 10, cada instância pode ter 64.000/10 = 6.400 portas se definir uma regra de saída explícita. De acordo com a tabela acima, cada um terá apenas 1.024 se escolher a atribuição automática.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Regras de saída e Nat de rede virtual

As regras de saída do Azure Load Balancer e o Virtual Network NAT são opções disponíveis para saída de uma rede virtual.

Para obter mais informações sobre as regras de saída, consulte [as regras de saída.](outbound-rules.md)

Para obter mais informações sobre a Rede Virtual Azure NAT, consulte [o que é Azure Virtual Network NAT](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Restrições

*   Quando uma ligação é inativa sem que sejam enviados novos pacotes, as portas serão libertadas após 4 a 120 minutos.
  * Este limiar pode ser configurado através de regras de saída.
*   Cada endereço IP fornece 64.000 portas que podem ser usadas para SNAT.
*   Cada porta pode ser utilizada tanto para ligações TCP como UDP para um endereço IP de destino
  * É necessária uma porta UDP SNAT se a porta de destino é única ou não. Para cada ligação UDP a um IP de destino, é utilizada uma porta UDP SNAT.
  * Uma porta TCP SNAT pode ser usada para múltiplas ligações ao mesmo destino IP, desde que as portas de destino sejam diferentes.
*   A exaustão do SNAT ocorre quando uma instância de backend se esgota de determinadas portas SNAT. Um equilibrador de carga ainda pode ter portas SNAT não uusadas. Se as portas SNAT utilizadas por uma instância de backend excederem as portas SNAT dadas, não será possível estabelecer novas ligações de saída.

## <a name="next-steps"></a>Passos seguintes

*   [Falhas de ligação de saída de resolução de problemas por causa da exaustão do SNAT](./troubleshoot-outbound-connection.md)
*   [Reveja as métricas SNAT](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) e familiarize-se com a forma correta de filtrar, dividir e vê-las.
