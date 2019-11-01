---
title: Regras de saída no Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Usar regras de saída para definir conversões de endereço de rede de saída
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
ms.openlocfilehash: fd43e9c71db9ae553b24e6cd774495ee8cc5b621
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242341"
---
# <a name="load-balancer-outbound-rules"></a>Load Balancer regras de saída

Azure Load Balancer fornece conectividade de saída de uma rede virtual, além de entrada.  As regras de saída facilitam a configuração da conversão de endereços de rede de saída do [Standard Load Balancer](load-balancer-standard-overview.md)público.  Você tem total controle declarativo sobre a conectividade de saída para dimensionar e ajustar essa capacidade às suas necessidades específicas.

![Load Balancer regras de saída](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Com as regras de saída, você pode usar Load Balancer para: 
- Defina o NAT de saída do zero.
- dimensione e ajuste o comportamento do NAT de saída existente. 

As regras de saída permitem que você controle:
- quais máquinas virtuais devem ser traduzidas para quais endereços IP públicos. 
- como [as portas SNAT de saída](load-balancer-outbound-connections.md#snat) devem ser alocadas.
- para quais protocolos fornecer tradução de saída.
- Qual duração usar para tempo limite de ociosidade de conexão de saída (4-120 minutos).
- Se deseja enviar uma redefinição de TCP no tempo limite ocioso (em visualização pública). 

As regras de saída expandem o [cenário 2](load-balancer-outbound-connections.md#lb) em descrito no artigo [conexões de saída](load-balancer-outbound-connections.md) e a precedência de cenário permanece no estado em que se encontra.

## <a name="outbound-rule"></a>Regra de saída

Como todas as regras de Load Balancer, as regras de saída seguem a mesma sintaxe familiar que o balanceamento de carga e as regras de NAT de entrada:

**parâmetros** de + **frontend** + **pool de back-end**

Uma regra de saída configura o NAT de saída para _todas as máquinas virtuais identificadas pelo pool de back-end_ a serem convertidos no _front-end_.  Os _parâmetros_ e fornecem um controle refinado adicional sobre o algoritmo NAT de saída.

A versão de API "2018-07-01" permite uma definição de regra de saída estruturada da seguinte maneira:

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
>A configuração de NAT de saída efetiva é uma composição de todas as regras de saída e regras de balanceamento de carga. As regras de saída são incrementais para as regras de balanceamento de carga. Examine [desabilitando o NAT de saída para uma regra de balanceamento de carga](#disablesnat) para gerenciar a conversão de NAT de saída em vigor quando várias regras se aplicam a uma VM. Você deve [desabilitar o SNAT de saída](#disablesnat) ao definir uma regra de saída que está usando o mesmo endereço IP público que uma regra de balanceamento de carga.

### <a name="scale"></a>Dimensionar NAT de saída com vários endereços IP

Embora uma regra de saída possa ser usada com apenas um único endereço IP público, as regras de saída facilitam a carga de configuração para o dimensionamento do NAT de saída. Você pode usar vários endereços IP para planejar cenários de grande escala e pode usar regras de saída para atenuar os padrões propensos a [esgotamento de SNAT](load-balancer-outbound-connections.md#snatexhaust) .  

Cada endereço IP adicional fornecido por um front-end fornece 64.000 portas efêmeras para Load Balancer usar como portas SNAT. Embora as regras de balanceamento de carga ou de entrada NAT tenham um único front-end, a regra de saída expande a noção de front-end e permite vários front-ends por regra.  Com vários front-ends por regra, a quantidade de portas SNAT disponíveis é multiplicada por cada endereço IP público e os cenários grandes podem ter suporte.

Além disso, você pode usar um [prefixo IP público](https://aka.ms/lbpublicipprefix) diretamente com uma regra de saída.  O uso do prefixo IP público fornece um dimensionamento mais fácil e uma lista branca simplificada de fluxos provenientes de sua implantação do Azure. Você pode configurar uma configuração de IP de front-end dentro do Load Balancer recurso para fazer referência diretamente a um prefixo de endereço IP público.  Isso permite Load Balancer controle exclusivo sobre o prefixo IP público e a regra de saída usará automaticamente todos os endereços IP públicos contidos no prefixo IP público para conexões de saída.  Cada um dos endereços IP dentro do intervalo do prefixo IP público fornece 64.000 portas efêmeras por endereço IP para Load Balancer usar como portas SNAT.   

Você não pode ter recursos de endereço IP público individuais criados a partir do prefixo IP público ao usar essa opção, pois a regra de saída deve ter controle total do prefixo IP público.  Se precisar de um controle mais refinado, você poderá criar um recurso de endereço IP público individual do prefixo IP público e atribuir vários endereços IP públicos individualmente ao front-end de uma regra de saída.

### <a name="snatports"></a>Ajustar a alocação de porta SNAT

Você pode usar regras de saída para ajustar a [alocação de porta SNAT automática com base no tamanho do pool de back-end](load-balancer-outbound-connections.md#preallocatedports) e alocar mais ou menos do que a alocação de porta SNAT automática fornece.

Use o seguinte parâmetro para alocar 10.000 portas SNAT por VM (configuração de IP NIC).
 

          "allocatedOutboundPorts": 10000

Cada endereço IP público de todos os front-ends de uma regra de saída contribui para até 64.000 portas efêmeras para uso como portas SNAT.  Load Balancer aloca portas SNAT em múltiplos de 8. Se você fornecer um valor não divisível por 8, a operação de configuração será rejeitada.  Se você tentar alocar mais portas SNAT do que as disponíveis com base no número de endereços IP públicos, a operação de configuração será rejeitada.  Por exemplo, se você alocar 10.000 portas por VM e 7 VMs em um pool de back-end compartilharão um único endereço IP público, a configuração será rejeitada (7 x 10.000 portas SNAT > 64.000 portas SNAT).  Você pode adicionar mais endereços IP públicos ao front-end da regra de saída para habilitar o cenário.

Você pode reverter para a [alocação de porta SNAT automática com base no tamanho do pool de back-end](load-balancer-outbound-connections.md#preallocatedports) , especificando 0 para número de portas. Nesse caso, as primeiras 50 instâncias de VM receberão 1024 portas, 51-100 as instâncias de VM terão 512 e assim por diante, de acordo com a tabela. Ter mais de um front-end com um IP público associado à regra de saída não aumenta o número de portas alocadas para cada instância de VM.

### <a name="idletimeout"></a>Controlar tempo limite de ociosidade do fluxo de saída

As regras de saída fornecem um parâmetro de configuração para controlar o tempo limite de ociosidade do fluxo de saída e fazer a correspondência com as necessidades do seu aplicativo.  O padrão de tempos limite de ociosidade de saída é de 4 minutos.  O parâmetro aceita um valor de 4 a 120 para um número específico de minutos para o tempo limite de ociosidade para fluxos que correspondem a essa regra específica.

Use o seguinte parâmetro para definir o tempo limite de ociosidade de saída como 1 hora:

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a><a name="tcpreset"></a> Habilitar a redefinição de TCP no tempo limite ocioso (visualização)

O comportamento padrão de Load Balancer é descartar o fluxo silenciosamente quando o tempo limite de ociosidade de saída for atingido.  Com o parâmetro enableTCPReset, você pode habilitar um comportamento de aplicativo mais previsível e controlar se deseja enviar a redefinição TCP bidirecional (TCP RST) no momento do tempo limite de ociosidade de saída. 

Use o seguinte parâmetro para habilitar a redefinição de TCP em uma regra de saída:

           "enableTcpReset": true

Examine a [redefinição de TCP no tempo limite ocioso (versão prévia)](https://aka.ms/lbtcpreset) para obter detalhes, incluindo a disponibilidade da região.

### <a name="proto"></a>Suporte a protocolos de transporte TCP e UDP com uma única regra

Você provavelmente desejará usar "All" para o protocolo de transporte da regra de saída, mas também poderá aplicar a regra de saída a um protocolo de transporte específico, também se houver a necessidade de fazer isso.

Use o seguinte parâmetro para definir o protocolo como TCP e UDP:

          "protocol": "All"

### <a name="disablesnat"></a>Desabilitar NAT de saída para uma regra de balanceamento de carga

Conforme mencionado anteriormente, as regras de balanceamento de carga fornecem programação automática de NAT de saída. No entanto, alguns cenários se beneficiam ou exigem que você desabilite a programação automática do NAT de saída pela regra de balanceamento de carga para permitir que você controle ou Refine o comportamento.  As regras de saída têm cenários em que é importante parar a programação NAT de saída automática.

Você pode usar esse parâmetro de duas maneiras:
- Supressão opcional do uso do endereço IP de entrada para NAT de saída.  As regras de saída são incrementais às regras de balanceamento de carga e com esse conjunto de parâmetros, a regra de saída está no controle.
  
- Ajuste os parâmetros NAT de saída de um endereço IP usado para entrada e saída simultaneamente.  A programação NAT de saída automática deve ser desabilitada para permitir que uma regra de saída assuma o controle.  Por exemplo, para alterar a alocação de porta SNAT de um endereço também usado para entrada, esse parâmetro deve ser definido como true.  Se você tentar usar uma regra de saída para redefinir os parâmetros de um endereço IP também usado para entrada e não tiver liberado a programação de NAT de saída da regra de balanceamento de carga, a operação para configurar uma regra de saída falhará.

>[!IMPORTANT]
> Sua máquina virtual não terá conectividade de saída se você definir esse parâmetro como true e não tiver uma regra de saída (ou [cenário de IP público em nível de instância](load-balancer-outbound-connections.md#ilpip) para definir a conectividade de saída.  Algumas operações de sua VM ou seu aplicativo podem depender de ter conectividade de saída disponível. Certifique-se de entender as dependências do seu cenário e ter considerado o impacto de fazer essa alteração.

Você pode desabilitar o SNAT de saída na regra de balanceamento de carga com este parâmetro de configuração:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

O parâmetro disableOutboundSNAT assume como padrão false, o que significa que **a regra de** balanceamento de carga fornece NAT de saída automático como uma imagem espelho da configuração da regra de balanceamento de carga.  

Se você definir disableOutboundSnat como true na regra de balanceamento de carga, a regra de balanceamento de carga liberará o controle da programação NAT de saída automática, caso contrário.  O SNAT de saída como resultado da regra de balanceamento de carga está desabilitado.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Reutilizar existente ou definir novos pools de back-end

As regras de saída não introduzem um novo conceito para definir o grupo de VMs ao qual a regra deve ser aplicada.  Em vez disso, eles reutilizam o conceito de um pool de back-end, que também é usado para regras de balanceamento de carga. Você pode usar isso para simplificar a configuração reutilizando uma definição de pool de back-end existente ou criando uma especificamente para uma regra de saída.

## <a name="scenarios"></a>Cenários

### <a name="groom"></a>Conexões de saída de Groom para um conjunto específico de endereços IP públicos

Você pode usar uma regra de saída para que as conexões de saída do Groom pareçam originar-se de um conjunto específico de endereços IP públicos para facilitar os cenários de lista de permissões.  Esse endereço IP público de origem pode ser o mesmo usado por uma regra de balanceamento de carga ou um conjunto diferente de endereços IP públicos do que o usado por uma regra de balanceamento de carga.  

1. Criar um [prefixo IP público](https://aka.ms/lbpublicipprefix) (ou endereços IP públicos do prefixo IP público)
2. Criar um Balanceador de Carga Standard público
3. Criar front-ends referenciando o prefixo IP público (ou endereços IP públicos) que você deseja usar
4. Reutilize um pool de back-end ou crie um pool de back-end e coloque as VMs em um pool de back-end do Load Balancer público
5. Configurar uma regra de saída no Load Balancer público para programar o NAT de saída para essas VMs usando os front-ends
   
Se não quiser que a regra de balanceamento de carga seja usada para saída, você precisará [desabilitar o SNAT de saída](#disablesnat) na regra de balanceamento de carga.

### <a name="modifysnat"></a>Modificar a alocação de porta SNAT

Você pode usar regras de saída para ajustar a [alocação de porta SNAT automática com base no tamanho do pool de back-end](load-balancer-outbound-connections.md#preallocatedports).

Por exemplo, se você tiver duas máquinas virtuais compartilhando um único endereço IP público para NAT de saída, talvez queira aumentar o número de portas SNAT alocadas a partir das portas 1024 padrão se estiver experimentando esgotamento de SNAT. Cada endereço IP público pode contribuir até 64.000 portas efêmeras.  Se você configurar uma regra de saída com um único front-end de endereço IP público, poderá distribuir um total de 64.000 portas SNAT para VMs no pool de back-end.  Para duas VMs, no máximo 32.000 portas SNAT podem ser alocadas com uma regra de saída (2x 32.000 = 64.000).

Examine [as conexões de saída](load-balancer-outbound-connections.md) e os detalhes sobre como as portas [SNAT](load-balancer-outbound-connections.md#snat) são alocadas e usadas.

### <a name="outboundonly"></a>Habilitar somente saída

Você pode usar uma Standard Load Balancer pública para fornecer NAT de saída para um grupo de VMs. Nesse cenário, você pode usar uma regra de saída por si só, sem a necessidade de qualquer regra adicional.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>NAT de saída somente para VMs (sem entrada)

Defina um Standard Load Balancer público, coloque as VMs no pool de back-end e configure uma regra de saída para programar o NAT de saída e fazer com que as conexões de saída sejam originadas de um endereço IP público específico. Você também pode usar um prefixo IP público Simplifique a listagem de branco na origem das conexões de saída.

1. Crie um Standard Load Balancer público.
2. Crie um pool de back-end e coloque as VMs em um pool de back-end do Load Balancer público.
3. Configure uma regra de saída no Load Balancer público para programar o NAT de saída para essas VMs.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>NAT de saída para cenários de Standard Load Balancer interno

Ao usar um Standard Load Balancer interno, o NAT de saída não estará disponível até que a conectividade de saída tenha sido declarada explicitamente. Você pode definir a conectividade de saída usando uma regra de saída para criar conectividade de saída para VMs por trás de um Standard Load Balancer interno com estas etapas:

1. Crie um Standard Load Balancer público.
2. Crie um pool de back-end e coloque as VMs em um pool de back-end do Load Balancer público, além da Load Balancer interna.
3. Configure uma regra de saída no Load Balancer público para programar o NAT de saída para essas VMs.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Habilite os protocolos UDP TCP & para NAT de saída com um Standard Load Balancer público

- Ao usar um Standard Load Balancer público, a programação NAT de saída automática fornecida corresponde ao protocolo de transporte da regra de balanceamento de carga.  

   1. Desabilite o SNAT de saída na regra de balanceamento de carga.
   2. Configure uma regra de saída na mesma Load Balancer.
   3. Reutilize o pool de back-end já usado por suas VMs.
   4. Especifique "protocolo": "todos" como parte da regra de saída.

- Quando apenas as regras NAT de entrada são usadas, nenhum NAT de saída é fornecido.

   1. Coloque as VMs em um pool de back-end.
   2. Defina uma ou mais configurações de IP de front-end com endereço IP público ou prefixo de IP público.
   3. Configure uma regra de saída na mesma Load Balancer.
   4. Especificar "protocolo": "todos" como parte da regra de saída

## <a name="limitations"></a>Limitações

- O número máximo de portas efêmeras utilizáveis por endereço IP de front-end é 64.000.
- O intervalo do tempo limite de ociosidade de saída configurável é de 4 a 120 minutos (240 a 7200 segundos).
- Load Balancer não dá suporte a ICMP para NAT de saída.
- O portal não pode ser usado para configurar ou exibir as regras de saída.  Use modelos, a API REST, AZ CLI 2,0 ou PowerShell.
- As regras de saída só podem ser aplicadas à configuração de IP primário de uma NIC.  Há suporte para várias NICs.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como usar [Load Balancer para conexões de saída](load-balancer-outbound-connections.md).
- Saiba mais sobre [Standard Load Balancer](load-balancer-standard-overview.md).
- Saiba mais sobre a [redefinição de TCP bidirecional no tempo limite ocioso](load-balancer-tcp-reset.md).
- [Configure as regras de saída com o CLI do Azure 2,0](configure-load-balancer-outbound-cli.md).
