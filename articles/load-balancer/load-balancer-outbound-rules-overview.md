---
title: Regras de saída - Azure Load Balancer
description: Com este caminho de aprendizagem, comece a usar regras de saída para definir traduções de endereços de rede de saída.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 7/17/2019
ms.author: allensu
ms.openlocfilehash: d419c213b3bcfef3631d68eb9d4cb485291bed31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304196"
---
# <a name="load-balancer-outbound-rules"></a>Regras de saída do Balancer de Carga

O Azure Load Balancer fornece conectividade de saída a partir de uma rede virtual para além da entrada.  As regras de saída tornam simples configurar a tradução de endereços de rede de saída do [Standard Load Balancer.](load-balancer-standard-overview.md)  Você tem controlo declarativo total sobre a conectividade de saída para escalar e afinar esta capacidade às suas necessidades específicas.

![Regras de saída do Balancer de Carga](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Com regras de saída, pode utilizar o Balancer de Carga para: 
- definir nat de saída do zero.
- escala e afinar o comportamento do NAT de saída existente. 

As regras de saída permitem-lhe controlar:
- que máquinas virtuais devem ser traduzidas para os endereços IP públicos. 
- como devem ser [atribuídas portas SNAT de saída.](load-balancer-outbound-connections.md#snat)
- quais os protocolos para fornecer tradução de saída.
- que duração a utilizar para o tempo limite de ligação de saída (4-120 minutos).
- se enviar um Reset TCP no tempo de paragem inativo

As regras de saída expandem o [cenário 2](load-balancer-outbound-connections.md#lb) no artigo de [ligações de saída](load-balancer-outbound-connections.md) e a precedência do cenário permanece como está.

## <a name="outbound-rule"></a>Regra de saída

Como todas as regras do Load Balancer, as regras de saída seguem a mesma sintaxe familiar que o equilíbrio de carga e as regras nat de entrada:

**piscina de** + **parâmetros** + frontend**backend**

Uma regra de saída configura o NAT de saída para _todas as máquinas virtuais identificadas pela piscina de backend_ a traduzir para a _extremidade frontal_.  E _os parâmetros_ fornecem um controlo adicional de grãos finos sobre o algoritmo NAT de saída.

A versão API "2018-07-01" permite uma definição de regra de saída estruturada da seguinte forma:

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>A configuração eficaz do NAT de saída é um composto de todas as regras de saída e regras de equilíbrio de carga. As regras de saída são incrementais para carregar regras de equilíbrio. Reveja a desativação do [NAT de saída para uma regra](#disablesnat) de equilíbrio de carga para gerir a tradução efetiva de nat de saída quando várias regras se aplicam a um VM. Deve [desativar o SNAT de saída](#disablesnat) ao definir uma regra de saída que utilize o mesmo endereço IP público como regra de equilíbrio de carga.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>Dimensionar nat de saída com vários endereços IP

Embora uma regra de saída possa ser usada com apenas um único endereço IP público, as regras de saída facilitam o fardo de configuração para escalar o NAT de saída. Você pode usar vários endereços IP para planear cenários de grande escala e você pode usar regras de saída para mitigar padrões propensos à [exaustão sNAT.](load-balancer-outbound-connections.md#snatexhaust)  

Cada endereço IP adicional fornecido por uma extremidade frontal fornece 64.000 portas efémeras para o Balancer de Carga para usar como portas SNAT. Embora as regras de equilíbrio de carga ou de entrada na NAT tenham uma única extremidade frontal, a regra de saída expande a noção frontend e permite múltiplas extremidades dianteiras por regra.  Com vários frontendas por regra, a quantidade de portas SNAT disponíveis é multiplicada com cada endereço IP público, e grandes cenários podem ser suportados.

Além disso, pode utilizar um [prefixo IP público](https://aka.ms/lbpublicipprefix) diretamente com uma regra de saída.  A utilização de prefixo ip público proporciona uma escala mais fácil e simplificada lista de fluxos brancos originários da sua implantação Azure. Pode configurar uma configuração IP frontal dentro do recurso Balancer de carga para fazer referência a um prefixo de endereço IP público diretamente.  Isto permite o controlo exclusivo do Balancer sobre o prefixo IP público e a regra de saída utilizará automaticamente todos os endereços IP públicos contidos no prefixo IP público para ligações de saída.  Cada um dos endereços IP dentro do intervalo do prefixo IP público fornece 64.000 portas efémeras por endereço IP para o Balancer de Carga para utilizar como portas SNAT.   

Não é possível criar recursos de endereçoIP públicos individuais a partir do prefixo IP público ao utilizar esta opção, uma vez que a regra de saída deve ter controlo total do prefixo IP público.  Se necessitar de um controlo mais fino, pode criar um recurso de endereço IP público individual a partir do prefixo IP público e atribuir vários endereços IP públicos individualmente à extremidade frontal de uma regra de saída.

### <a name="tune-snat-port-allocation"></a><a name="snatports"></a>Atribuição de porta SNAT de sintonização

Pode utilizar regras de saída para afinar a atribuição automática da [porta SNAT com base no tamanho](load-balancer-outbound-connections.md#preallocatedports) da piscina de backend e atribuir mais ou menos do que a atribuição automática de portas SNAT fornece.

Utilize o seguinte parâmetro para alocar 10.000 portas SNAT por VM (configuração NIC IP).
 

          "allocatedOutboundPorts": 10000

Cada endereço IP público de todas as extremidades dianteiras de uma regra de saída contribui com até 64.000 portas efémeras para utilização como portas SNAT.  Balancer load alocado portas SNAT em múltiplos de 8. Se fornecer um valor não divisível por 8, a operação de configuração é rejeitada.  Se tentar alocar mais portas SNAT do que disponíveis com base no número de endereços IP públicos, a operação de configuração é rejeitada.  Por exemplo, se você alocar 10.000 portas por VM e 7 VMs em uma piscina de backend partilharia um único endereço IP público, a configuração é rejeitada (7 x 10.000 portas SNAT > 64.000 portas SNAT).  Pode adicionar mais endereços IP públicos à extremidade frontal da regra de saída para permitir o cenário.

Pode voltar à atribuição automática da [porta SNAT com base no tamanho da piscina de backend,](load-balancer-outbound-connections.md#preallocatedports) especificando 0 para o número de portas. Nesse caso, os primeiros 50 casos de VM terão 1024 portas, 51-100 vM casos terão 512 e assim por diante de acordo com a tabela.

### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a>Controlar o tempo de paragem do fluxo de saída

As regras de saída fornecem um parâmetro de configuração para controlar o tempo de paragem do fluxo de saída e compará-lo às necessidades da sua aplicação.  Os intervalos de saída não param em 4 minutos.  O parâmetro aceita um valor de 4 a 120 para especificar o número de minutos para o tempo inativo para fluxos que correspondam a esta regra em particular.

Utilize o seguinte parâmetro para definir o tempo de saída para 1 hora:

          "idleTimeoutInMinutes": 60

### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a><a name="tcpreset"></a> Ativar o Reset TCP no tempo de tempo inativo

O comportamento padrão do Balancer de Carga é baixar o fluxo silenciosamente quando o tempo de saída for atingido.  Com o parâmetro enableTCPReset, pode ativar um comportamento de aplicação e controlo mais previsíveis se enviar o Reset TCP (TCP RST) bidirecional no momento fora do tempo de saída. 

Utilize o seguinte parâmetro para ativar o Reset tCP numa regra de saída:

           "enableTcpReset": true

Reparação do [TCP no tempo inativo](https://aka.ms/lbtcpreset) para obter detalhes, incluindo a disponibilidade da região.

### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a>Apoiar tanto os protocolos de transporte tCP como uDP com uma regra única

É provável que queira utilizar "All" para o protocolo de transporte da regra de saída, mas também pode aplicar a regra de saída a um protocolo de transporte específico, se houver necessidade de o fazer.

Utilize o seguinte parâmetro para definir o protocolo à TCP e uDP:

          "protocol": "All"

### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a>Desativar o NAT de saída para uma regra de equilíbrio de carga

Como referido anteriormente, as regras de equilíbrio de carga fornecem uma programação automática de NAT de saída. No entanto, alguns cenários beneficiam ou exigem que desative a programação automática do NAT de saída pela regra de equilíbrio de carga supor que você controle ou refine o comportamento.  As regras de saída têm cenários em que é importante parar a programação automática de nat de saída.

Pode utilizar este parâmetro de duas formas:
- Supressão opcional da utilização do endereço IP de entrada para o NAT de saída.  As regras de saída são incrementais para carregar regras de equilíbrio e com este parâmetro definido, a regra de saída está no controlo.
  
- Sintonize simultaneamente os parâmetros NAT de saída de um endereço IP utilizado para a entrada e saída.  A programação automática de saída do NAT deve ser desativada para permitir que uma regra de saída assuma o controlo.  Por exemplo, para alterar a atribuição da porta SNAT de um endereço também utilizado para a entrada, este parâmetro deve ser definido como verdadeiro.  Se tentar utilizar uma regra de saída para redefinir os parâmetros de um endereço IP também utilizado sacar e não ter libertado a programação nat de saída da regra de equilíbrio de carga, a operação para configurar uma regra de saída falhará.

>[!IMPORTANT]
> A sua máquina virtual não terá conectividade de saída se definir este parâmetro verdadeiro e não tiver uma regra de saída (ou [cenário ip público de nível de instância](load-balancer-outbound-connections.md#ilpip) para definir a conectividade de saída.  Algumas operações do seu VM ou da sua aplicação podem depender de ter conectividade de saída disponível. Certifique-se de compreender as dependências do seu cenário e ter considerado o impacto de fazer esta mudança.

Pode desativar o SNAT de saída na regra de equilíbrio de carga com este parâmetro de configuração:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

O parâmetro dedesactivadorOutboundSNAT predefinido para falso, o que significa que a regra de equilíbrio de carga **fornece** NAT de saída automática como uma imagem espelhada da configuração da regra de equilíbrio de carga.  

Se definir desactivadorOutboundSnat para verdadeiro na regra de equilíbrio de carga, a regra de equilíbrio de carga liberta o controlo da programação NAT de saída automática de outra forma automática.  O SNAT de saída como resultado da regra de equilíbrio da carga é desativado.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Reutilizar as piscinas existentes ou definir novas piscinas de backend

As regras de saída não introduzem um novo conceito de definição do grupo de VMs a que a regra deve ser aplicada.  Em vez disso, reutilizam o conceito de piscina de backend, que também é usada para regras de equilíbrio de carga. Pode usá-lo para simplificar a configuração, reutilizando uma definição de piscina de backend existente ou criando uma especificamente para uma regra de saída.

## <a name="scenarios"></a>Cenários

### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a>Ligações de saída do noivo a um conjunto específico de endereços IP públicos

Você pode usar uma regra de saída para preparar ligações de saída para parecer originar de um conjunto específico de endereços IP públicos para facilitar cenários de listagem de branco.  Este endereço IP público de origem pode ser o mesmo usado por uma regra de equilíbrio de carga ou um conjunto diferente de endereços IP públicos do que usado por uma regra de equilíbrio de carga.  

1. Criar [prefixo ip público](https://aka.ms/lbpublicipprefix) (ou endereços IP públicos a partir de prefixo IP público)
2. Criar um Balanceador de Carga Standard público
3. Crie frontends referentes ao prefixo IP público (ou endereços IP públicos) que deseja utilizar
4. Reutilizar uma piscina de backend ou criar uma piscina de backend e colocar os VMs em uma piscina de backend do equilíbrio de carga pública
5. Configure uma regra de saída no equilíbrio de carga público para programar NAT de saída para estes VMs usando as extremidades dianteiras
   
Se não desejar que a regra de equilíbrio da carga seja utilizada para a saída, tem de [desativar o SNAT](#disablesnat) de saída na regra de equilíbrio de carga.

### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a>Modificar a atribuição da porta SNAT

Pode utilizar regras de saída para afinar a atribuição automática da [porta SNAT com base no tamanho da piscina de backend](load-balancer-outbound-connections.md#preallocatedports).

Por exemplo, se tiver duas máquinas virtuais que partilham um único endereço IP público para o NAT de saída, poderá desejar aumentar o número de portas SNAT atribuídas a partir das 1024 portas padrão se estiver a sentir exaustão sNAT. Cada endereço IP público pode contribuir com até 64.000 portas efémeras.  Se configurar uma regra de saída com um único endereço IP público frontend, você pode distribuir um total de 64.000 portas SNAT para VMs na piscina de backend.  Para dois VMs, um máximo de 32.000 portas SNAT pode ser alocado com uma regra de saída (2x 32.000 = 64.000).

Reveja as [ligações de saída](load-balancer-outbound-connections.md) e os detalhes sobre a forma como as portas [SNAT](load-balancer-outbound-connections.md#snat) são atribuídas e utilizadas.

### <a name="enable-outbound-only"></a><a name="outboundonly"></a>Ativar apenas a saída

Você pode usar um Balancer de Carga Padrão público para fornecer NAT de saída para um grupo de VMs. Neste cenário, pode utilizar uma regra de saída por si só, sem a necessidade de quaisquer regras adicionais.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>NAT de saída apenas para VMs (sem entrada)

Defina um Balancer de Carga Padrão público, coloque os VMs na piscina de backend e configure uma regra de saída para programar nat de saída e preparar as ligações de saída para originar de um endereço IP público específico. Também pode utilizar um prefixo IP público para simplificar a listagem de branco da fonte de ligações de saída.

1. Crie um Equilibrador de Carga Padrão público.
2. Crie uma piscina de backend e coloque os VMs numa piscina de backend do equilibrador de carga pública.
3. Configure uma regra de saída no equilíbrio de carga público para programar NAT de saída para estes VMs.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>NAT de saída para cenários internos de equilíbrio de carga padrão

Ao utilizar um balancedor de carga padrão interno, o NAT de saída não está disponível até que a conectividade de saída tenha sido explicitamente declarada. Pode definir a conectividade de saída utilizando uma regra de saída para criar conectividade de saída para VMs por trás de um Balancer de Carga Padrão interno com estes passos:

1. Crie um Equilibrador de Carga Padrão público.
2. Crie uma piscina de backend e coloque os VMs numa piscina de backend do balancer de carga pública, além do Balancer de Carga interna.
3. Configure uma regra de saída no equilíbrio de carga público para programar NAT de saída para estes VMs.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Permitir tanto os protocolos TCP & UDP para o NAT de saída com um equilibrista de carga padrão público

- Ao utilizar um balancedor de carga padrão público, a programação automática de saída na NAT fornecida corresponde ao protocolo de transporte da regra de equilíbrio de carga.  

   1. Desative a saída sNAT na regra de equilíbrio de carga.
   2. Configure uma regra de saída no mesmo Balancer de Carga.
   3. Reutilize a piscina de backend já utilizada pelos seus VMs.
   4. Especifique "protocolo": "Todos" como parte da regra de saída.

- Quando apenas são utilizadas as regras de entrada na NAT, não é fornecido nenhum NAT de saída.

   1. Coloque vMs em uma piscina de backend.
   2. Defina uma ou mais configurações IP frontend com endereçoip público ou prefixo IP público.
   3. Configure uma regra de saída no mesmo Balancer de Carga.
   4. Especificar "protocolo": "Todos" como parte da regra de saída

## <a name="limitations"></a>Limitações

- O número máximo de portas efémeras utilizáveis por endereço IP frontal é de 64.000.
- O intervalo do tempo de saída configurável é de 4 a 120 minutos (240 a 7200 segundos).
- O Balancer de Carga não suporta o ICMP para o NAT de saída.
- As regras de saída só podem ser aplicadas à configuração primária de IP de um NIC.  Vários NICs são suportados.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a utilizar o [Balancer de Carga para ligações de saída](load-balancer-outbound-connections.md).
- Saiba mais sobre o Equilíbrio de [Carga Padrão.](load-balancer-standard-overview.md)
- Saiba mais sobre [o Reset bidirecional do TCP no tempo de paragem inativo](load-balancer-tcp-reset.md).
- [Configure as regras de saída com o Azure CLI 2.0](configure-load-balancer-outbound-cli.md).
