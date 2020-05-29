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
ms.openlocfilehash: e6c7464eb1bf51a4e42d0db98d92459dc39fbb11
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170822"
---
# <a name="load-balancer-outbound-rules"></a>Regras de saída do Balanceador de Carga

O Azure Load Balancer fornece conectividade de saída a partir de uma rede virtual, além de entrada.  As regras de saída simplificam a configuração da tradução de endereços de rede de saída do [Balancer de Carga Padrão](load-balancer-standard-overview.md)do público.  Você tem controlo declarativo total sobre a conectividade de saída para escalar e afinar esta capacidade às suas necessidades específicas.

![Regras de saída do Balanceador de Carga](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Com as regras de saída, pode utilizar o Balancer de Carga para: 
- definir NAT de saída do zero.
- escala e afinar o comportamento do NAT existente. 

As regras de saída permitem-lhe controlar:
- que as máquinas virtuais devem ser traduzidas para os endereços IP públicos. 
- como devem ser [atribuídas portas SNAT de saída.](load-balancer-outbound-connections.md#snat)
- quais protocolos para fornecer tradução de saída.
- qual a duração a utilizar para o intervalo de tempo de ligação de saída (4-120 minutos).
- se enviar um Reset TCP no tempo limite de marcha lenta

As regras de saída expandem o [cenário 2](load-balancer-outbound-connections.md#lb) descrito no artigo de [ligações de saída](load-balancer-outbound-connections.md) e a precedência do cenário permanece como-é.

## <a name="outbound-rule"></a>Regra de saída

Como todas as regras do Balanceador de Carga, as regras de saída seguem a mesma sintaxe familiar que o equilíbrio de carga e as regras NAT de entrada:

**frontend**  +  **parâmetros**  +  **piscina backend**

Uma regra de saída configura a saída DO NAT para _todas as máquinas virtuais identificadas pela piscina de backend_ para serem traduzidas para o _frontend_.  E _os parâmetros_ fornecem um controlo adicional de grãos finos sobre o algoritmo NAT de saída.

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
>A configuração eficaz de SAÍDA NAT é um composto de todas as regras de saída e regras de equilíbrio de carga. As regras de saída são incrementais para as regras de equilíbrio de carga. [Reveja a desativação do NAT de saída para uma regra de equilíbrio de carga](#disablesnat) para gerir a tradução eficaz de ACESSO NAT quando várias regras se aplicam a um VM. Deve [desativar o SNAT de saída](#disablesnat) ao definir uma regra de saída que está a utilizar o mesmo endereço IP público como regra de equilíbrio de carga.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>NAT de escala com vários endereços IP

Embora uma regra de saída possa ser usada apenas com um único endereço IP público, as regras de saída facilitam o fardo de configuração para a escala de NAT de saída. Você pode usar vários endereços IP para planear cenários de grande escala e você pode usar regras de saída para mitigar padrões propensos à [exaustão SNAT.](troubleshoot-outbound-connection.md#snatexhaust)  

Cada endereço IP adicional fornecido por um frontend fornece 64.000 portas efémeras para o Balancer de Carga usar como portas SNAT. Enquanto as regras de equilíbrio de carga ou de entrada têm uma única linha frontal, a regra de saída expande a noção frontal e permite múltiplos frontends por regra.  Com vários frontends por regra, a quantidade de portas SNAT disponíveis é multiplicada com cada endereço IP público, e grandes cenários podem ser suportados.

Além disso, pode utilizar um [prefixo IP público](https://aka.ms/lbpublicipprefix) diretamente com uma regra de saída.  A utilização do prefixo IP público prevê uma escala mais fácil e uma listagem branca simplificada dos fluxos originários da sua implantação do Azure. Pode configurar uma configuração IP frontal dentro do recurso Balanceador de Carga para fazer referência a um prefixo de endereço IP público diretamente.  Isto permite o controlo exclusivo do Balancer de Carga sobre o prefixo IP público e a regra de saída utilizará automaticamente todos os endereços IP públicos contidos no prefixo IP público para ligações de saída.  Cada um dos endereços IP dentro do intervalo do prefixo IP público fornece 64.000 portas efémeras por endereço IP para o Balancer de Carga para usar como portas SNAT.   

Não é possível ter recursos individuais de endereço IP públicos criados a partir do prefixo IP público ao utilizar esta opção, uma vez que a regra de saída deve ter o controlo total do prefixo IP público.  Se precisar de um controlo mais fino, pode criar um recurso de endereço IP público individual a partir do prefixo IP público e atribuir vários endereços IP públicos individualmente à parte frontal de uma regra de saída.

### <a name="tune-snat-port-allocation"></a><a name="snatports"></a>Alocação portuária tune SNAT

Pode utilizar as regras de saída para afinar a atribuição automática da [porta SNAT com base no tamanho da piscina de backend](load-balancer-outbound-connections.md#preallocatedports) e alocar mais ou menos do que a atribuição automática da porta SNAT proporciona.

Utilize o seguinte parâmetro para alocar 10.000 portas SNAT por VM (configuração NIC IP).
 

          "allocatedOutboundPorts": 10000

Cada endereço IP público de todas as frentes de uma regra de saída contribui com até 64.000 portas efémeras para utilização como portas SNAT.  O Balancer de Carga aloca portas SNAT em múltiplos de 8. Se fornecer um valor não divisível até 8, a operação de configuração é rejeitada.  Se tentar alocar mais portas SNAT do que as disponíveis com base no número de endereços IP públicos, a operação de configuração é rejeitada.  Por exemplo, se alocar 10.000 portas por VM e 7 VMs num pool de backend partilharia um único endereço IP público, a configuração é rejeitada (7 x 10.000 portas SNAT > 64.000 portas SNAT).  Pode adicionar mais endereços IP públicos à parte frontal da regra de saída para ativar o cenário.

Pode voltar à atribuição automática da [porta SNAT com base no tamanho da piscina de backend,](load-balancer-outbound-connections.md#preallocatedports) especificando 0 para o número de portas. Nesse caso, as primeiras 50 instâncias VM terão 1024 portas, 51-100 VM casos receberão 512 e assim por diante, de acordo com a tabela.

### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a>Controle o tempo limite de fluxo de saída

As regras de saída fornecem um parâmetro de configuração para controlar o tempo limite de saída e corresponder às necessidades da sua aplicação.  Intervalos de tempo de saída inativos a 4 minutos.  O parâmetro aceita um valor de 4 a 120 para especificar o número de minutos para o intervalo de tempo inativo para fluxos correspondentes a esta regra particular.

Utilize o seguinte parâmetro para definir o tempo limite de saída para 1 hora:

          "idleTimeoutInMinutes": 60

### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a><a name="tcpreset"></a>Ativar o Reset TCP no tempo limite de marcha lenta

O comportamento padrão do Balanceador de Carga é baixar o fluxo silenciosamente quando o tempo de saída tiver sido atingido.  Com o parâmetro enableTCPReset, pode ativar um comportamento de aplicação mais previsível e controlar se deve enviar o Reset TCP bidirecional (TCP RST) no momento fora do tempo limite de saída. 

Utilize o seguinte parâmetro para ativar o Reset TCP numa regra de saída:

           "enableTcpReset": true

[Reveja o TCP Reset no tempo limite de](https://aka.ms/lbtcpreset) marcha lenta para obter detalhes, incluindo a disponibilidade da região.

### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a>Apoiar os protocolos de transporte TCP e UDP com uma única regra

É provável que queira utilizar "All" para o protocolo de transporte da regra de saída, mas também pode aplicar a regra de saída a um protocolo de transporte específico, se houver necessidade de o fazer.

Utilize o seguinte parâmetro para definir o protocolo para TCP e UDP:

          "protocol": "All"

### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a>Desative o NAT de saída para uma regra de equilíbrio de carga

Tal como indicado anteriormente, as regras de equilíbrio de carga fornecem uma programação automática do NAT de saída. No entanto, alguns cenários beneficiam ou obrigam-no a desativar a programação automática do NAT de saída pela regra de equilíbrio de carga para permitir controlar ou aperfeiçoar o comportamento.  As regras de saída têm cenários em que é importante parar a programação de NAT de saída automática.

Pode utilizar este parâmetro de duas formas:
- Supressão opcional da utilização do endereço IP de entrada para o NAT de saída.  As regras de saída são incrementais para carregar regras de equilíbrio e com este parâmetro definido, a regra de saída está no controlo.
  
- Sintonize os parâmetros NAT de saída de um endereço IP utilizado para entrada e saída simultaneamente.  A programação de acesso automático nat deve ser desativada para permitir que uma regra de saída assuma o controlo.  Por exemplo, para alterar a atribuição portuária SNAT de um endereço também utilizado para a entrada, este parâmetro deve ser definido como verdadeiro.  Se tentar utilizar uma regra de saída para redefinir os parâmetros de um endereço IP também utilizado para a entrada e não tiver libertado a programação DESA da regra de equilíbrio de carga, a operação para configurar uma regra de saída falhará.

>[!IMPORTANT]
> A sua máquina virtual não terá conectividade de saída se definir este parâmetro como verdadeiro e não tiver uma regra de saída (ou cenário IP público de [nível de instância](load-balancer-outbound-connections.md#ilpip) para definir conectividade de saída.  Algumas operações do seu VM ou da sua aplicação podem depender de ter conectividade de saída disponível. Certifique-se de compreender as dependências do seu cenário e considerou o impacto de fazer esta mudança.

Pode desativar o SNAT de saída na regra de equilíbrio de carga com este parâmetro de configuração:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

O parâmetro OutboundSNAT desativa-se por defeito de falso, o que significa que a **regra** de equilíbrio de carga fornece NAT de saída automática como uma imagem espelhada da configuração da regra de equilíbrio de carga.  

Se definir desativar o OutboundSnat para ser verdadeiro na regra de equilíbrio de carga, a regra de equilíbrio de carga liberta o controlo da programação DE NAT de saída automática.  O SNAT de saída como resultado da regra de equilíbrio de carga é desativado.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Reutilizar as piscinas existentes ou definir novos pools de backend

As regras de saída não introduzem um novo conceito de definição do grupo de VMs a que a regra deve ser aplicável.  Em vez disso, reutilizam o conceito de pool de backend, que também é usado para regras de equilíbrio de carga. Pode usar isto para simplificar a configuração reutilizando uma definição de pool de backend existente ou criando uma especificamente para uma regra de saída.

## <a name="scenarios"></a>Cenários

### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a>Ligações de saída do noivo a um conjunto específico de endereços IP públicos

Você pode usar uma regra de saída para preparar ligações de saída para preparar ligações de saída para parecer ter origem de um conjunto específico de endereços IP públicos para facilitar cenários de whitelisting.  Este endereço IP público de origem pode ser o mesmo que usado por uma regra de equilíbrio de carga ou um conjunto diferente de endereços IP públicos do que o usado por uma regra de equilíbrio de carga.  

1. Criar [prefixo IP público](https://aka.ms/lbpublicipprefix) (ou endereços IP públicos a partir do prefixo IP público)
2. Criar um Balanceador de Carga Standard público
3. Criar frontends referentes ao prefixo IP público (ou endereços IP públicos) que pretende utilizar
4. Reutilizar uma piscina de backend ou criar uma piscina de backend e colocar os VMs numa piscina de backend do balanceador de carga público
5. Configure uma regra de saída sobre o Balanceador de Carga público para programar NAT de saída para estes VMs usando os frontends
   
Se não desejar que a regra de equilíbrio de carga seja utilizada para a saída, tem de [desativar](#disablesnat) o SNAT de saída na regra de equilíbrio de carga.

### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a>Modificar a atribuição de porta SNAT

Pode utilizar as regras de saída para sintonizar a atribuição automática da [porta SNAT com base no tamanho da piscina de backend](load-balancer-outbound-connections.md#preallocatedports).

Por exemplo, se tiver duas máquinas virtuais que partilham um único endereço IP público para o NAT de saída, pode desejar aumentar o número de portas SNAT atribuídas a partir das portas padrão de 1024 se estiver a sentir exaustão do SNAT. Cada endereço IP público pode contribuir com até 64.000 portos efémeros.  Se configurar uma regra de saída com um único endereço IP público, pode distribuir um total de 64.000 portas SNAT para VMs na piscina de backend.  Para dois VMs, um máximo de 32.000 portas SNAT podem ser atribuídas com uma regra de saída (2x 32.000 = 64.000).

Reveja as [ligações de saída](load-balancer-outbound-connections.md) e os detalhes sobre a forma como as portas [SNAT](load-balancer-outbound-connections.md#snat) são atribuídas e utilizadas.

### <a name="enable-outbound-only"></a><a name="outboundonly"></a>Ativar apenas saída

Você pode usar um balanceador de carga padrão público para fornecer NAT de saída para um grupo de VMs. Neste cenário, pode utilizar uma regra de saída por si só, sem necessidade de quaisquer regras adicionais.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>NAT de saída apenas para VMs (sem entrada)

Defina um Balancer de Carga Padrão público, coloque os VMs na piscina de backend e configuure uma regra de saída para programar o PROGRAMA NAT e prepare as ligações de saída a partir de um endereço IP público específico. Também pode utilizar um prefixo IP público que simplifica a origem das ligações de saída.

1. Criar um balanceador de carga padrão público.
2. Crie uma piscina de backend e coloque os VMs numa piscina de backend do balanceador de carga público.
3. Configure uma regra de saída sobre o Balanceador de Carga público para programar NAT de saída para estes VMs.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>NAT de saída para cenários internos do Balanceador de Carga Padrão

Ao utilizar um Balanceador de Carga Padrão interno, o NAT de saída não está disponível até que a conectividade de saída tenha sido explicitamente declarada. Pode definir conectividade de saída usando uma regra de saída para criar conectividade de saída para VMs por trás de um Balanceador de Carga Padrão interno com estes passos:

1. Criar um balanceador de carga padrão público.
2. Crie uma piscina de backend e coloque os VMs num pool de backend do balanceador de carga público, para além do balançador de carga interno.
3. Configure uma regra de saída sobre o Balanceador de Carga público para programar NAT de saída para estes VMs.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Ativar os protocolos de UDP & TCP para o NAT de saída com um Balanceador de Carga Padrão público

- Ao utilizar um Balanceador de Carga Padrão público, a programação nat de saída automática fornecida corresponde ao protocolo de transporte da regra de equilíbrio de carga.  

   1. Desative o SNAT de saída na regra de equilíbrio de carga.
   2. Configure uma regra de saída no mesmo Balanceador de Carga.
   3. Reutilizar a piscina de backend já utilizada pelos seus VMs.
   4. Especifique "protocolo": "Todos" como parte da regra de saída.

- Quando apenas são utilizadas regras NAT de entrada, não é fornecido nenhum NAT de saída.

   1. Coloque VMs em uma piscina de backend.
   2. Defina uma ou mais configurações IP frontend com endereço IP público ou prefixo IP público.
   3. Configure uma regra de saída no mesmo Balanceador de Carga.
   4. Especificar "protocolo": "Todos" como parte da regra de saída

## <a name="limitations"></a>Limitações

- O número máximo de portas efémeras utilizáveis por endereço IP frontal é de 64.000.
- O intervalo de tempo de saída configurável é de 4 a 120 minutos (240 a 7200 segundos).
- O Balancer de Carga não suporta o ICMP para o NAT de saída.
- As regras de saída só podem ser aplicadas à configuração ip primária de um NIC.  Vários NICs são suportados.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre a utilização [do Balanceador de Carga para ligações de saída](load-balancer-outbound-connections.md).
- Saiba mais sobre [o Balanceador de Carga Padrão](load-balancer-standard-overview.md).
- Saiba mais sobre [o reset bidirecional do TCP no tempo limite de marcha lenta .](load-balancer-tcp-reset.md)
- [Configure as regras de saída com o Azure CLI 2.0](configure-load-balancer-outbound-cli.md).
