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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304196"
---
# <a name="load-balancer-outbound-rules"></a>Regras de saída do Balanceador de carga

O Balanceador de carga do Azure fornece a conectividade de saída da rede virtual, além de entrada.  As regras de saída tornam simples configurar a tradução de endereços de rede de saída do [Standard Load Balancer.](load-balancer-standard-overview.md)  Tem controle completo declarativo sobre conectividade de saída para dimensionar e ajustar esta capacidade às suas necessidades específicas.

![Regras de saída do Balanceador de carga](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Com as regras de saída, pode usar o Balanceador de carga: 
- Defina NAT de saída do zero.
- Dimensionar e otimizar o comportamento de NAT de saída existentes. 

Regras de saída permitem controlar:
- quais as máquinas virtuais devem ser convertidas para que endereços IP públicos. 
- como devem ser [atribuídas portas SNAT de saída.](load-balancer-outbound-connections.md#snat)
- os protocolos para fornecer a tradução de saída para.
- que duração a utilizar para o tempo limite de ligação de saída (4-120 minutos).
- se enviar um Reset TCP no tempo de paragem inativo

As regras de saída expandem o [cenário 2](load-balancer-outbound-connections.md#lb) no artigo de [ligações de saída](load-balancer-outbound-connections.md) e a precedência do cenário permanece como está.

## <a name="outbound-rule"></a>Regra de saída

Como todas as regras de Balanceador de carga, regras de saída, siga a sintaxe familiar do mesmo como balanceamento de carga e regras NAT de entrada:

**parâmetros frontend** +  + **piscina de backend**

Uma regra de saída configura o NAT de saída para _todas as máquinas virtuais identificadas pela piscina de backend_ a traduzir para a _extremidade frontal_.  E _os parâmetros_ fornecem um controlo adicional de grãos finos sobre o algoritmo NAT de saída.

Versão "2018-07-01" de API permite uma definição de regra de saída estruturada da seguinte forma:

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
>A configuração de NAT de saída eficaz é uma composição de todas as regras de saída e regras de balanceamento de carga. Regras de saída são incrementais para regras de balanceamento de carga. Reveja a desativação do [NAT de saída para uma regra](#disablesnat) de equilíbrio de carga para gerir a tradução efetiva de nat de saída quando várias regras se aplicam a um VM. Deve [desativar o SNAT de saída](#disablesnat) ao definir uma regra de saída que utilize o mesmo endereço IP público como regra de equilíbrio de carga.

### <a name="scale"></a>Dimensionar nat de saída com vários endereços IP

Enquanto uma regra de saída pode ser utilizada com apenas um único endereço IP público, regras de saída aliviar a carga de configuração de dimensionamento de NAT de saída. Você pode usar vários endereços IP para planear cenários de grande escala e você pode usar regras de saída para mitigar padrões propensos à [exaustão sNAT.](load-balancer-outbound-connections.md#snatexhaust)  

Cada endereço IP adicional fornecido por uma extremidade frontal fornece 64.000 portas efémeras para o Balancer de Carga para usar como portas SNAT. Enquanto o balanceamento de carga ou regras NAT de entrada tem um front-end único, a regra de saída se expande a noção de front-end e permite que vários front-ends por regra.  Com vários front-ends por regra, a quantidade de portas SNAT disponíveis é multiplicada com cada endereço IP público e cenários de grandes dimensões podem ser suportados.

Além disso, pode utilizar um [prefixo IP público](https://aka.ms/lbpublicipprefix) diretamente com uma regra de saída.  Utilizar um IP público de prefixo fornece para dimensionamento mais fácil e simplificada de criar uma lista de fluxos provenientes da implementação do Azure. Pode configurar uma configuração de IP de front-end dentro de um prefixo de endereço IP público de referenciar diretamente o recurso de Balanceador de carga.  Isso permite que o Balanceador de carga um controlo exclusivo sobre o prefixo do IP público e a regra de saída irá utilizar automaticamente a todos os endereços IP públicos contidos o prefixo do IP público para ligações de saída.  Cada um dos endereços IP dentro do intervalo do prefixo IP público fornece 64.000 portas efémeras por endereço IP para o Balancer de Carga para utilizar como portas SNAT.   

Não pode ter individuais recursos de endereço IP público criados a partir do prefixo do IP público ao utilizar esta opção, como a regra de saída tem de ter controlo total sobre o prefixo do IP público.  Se precisar de mais controle refinado, pode criar o recurso de endereço IP público individual a partir de prefixo IP público e atribuir vários endereços IP públicos individualmente para o front-end de uma regra de saída.

### <a name="snatports"></a>Atribuição de porta SNAT de sintonização

Pode utilizar regras de saída para afinar a atribuição automática da [porta SNAT com base no tamanho](load-balancer-outbound-connections.md#preallocatedports) da piscina de backend e atribuir mais ou menos do que a atribuição automática de portas SNAT fornece.

Utilize o parâmetro seguinte para alocar SNAT 10.000 portas por VM (configuração de IP de NIC).
 

          "allocatedOutboundPorts": 10000

Cada endereço IP público de todas as extremidades dianteiras de uma regra de saída contribui com até 64.000 portas efémeras para utilização como portas SNAT.  Balanceador de carga aloca SNAT portas em múltiplos de 8. Se fornecer um valor não divisível por 8, a operação de configuração é rejeitada.  Se tentar alocar o SNAT mais portas que estão disponíveis com base no número de endereços IP públicos, a operação de configuração é rejeitada.  Por exemplo, se você alocar 10.000 portas por VM e 7 VMs em uma piscina de backend partilharia um único endereço IP público, a configuração é rejeitada (7 x 10.000 portas SNAT > 64.000 portas SNAT).  Pode adicionar endereços IP públicos mais para o front-end da regra de saída para ativar o cenário.

Pode voltar à atribuição automática da [porta SNAT com base no tamanho da piscina de backend,](load-balancer-outbound-connections.md#preallocatedports) especificando 0 para o número de portas. Nesse caso, os primeiros 50 casos de VM terão 1024 portas, 51-100 vM casos terão 512 e assim por diante de acordo com a tabela.

### <a name="idletimeout"></a>Controlar o tempo de paragem do fluxo de saída

Regras de saída fornecem um parâmetro de configuração para controlar o tempo de limite de inatividade de fluxo de saída e fazer sua correspondência com as necessidades da sua aplicação.  Padrão de limites de ociosidade de saída a 4 minutos.  O parâmetro aceita um valor de 4 a 120 para especificar o número de minutos para o tempo inativo para fluxos que correspondam a esta regra em particular.

Utilize o parâmetro seguinte para definir o tempo de limite de inatividade saído para 1 hora:

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a><a name="tcpreset"></a> Ativar o Reset TCP no tempo de tempo inativo

O comportamento padrão do Balanceador de carga é descartar o fluxo de forma silenciosa quando for atingido o tempo de limite de inatividade saído.  Com o parâmetro enableTCPReset, pode ativar um comportamento mais previsível em termos de aplicação e controlar se pretende enviar bidirecional (TCP RST) de reposição de TCP no tempo limite de tempo limite de inatividade saída. 

Utilize o parâmetro seguinte para ativar a reposição de TCP de mensagens em fila numa regra de saída:

           "enableTcpReset": true

Reparação do [TCP no tempo inativo](https://aka.ms/lbtcpreset) para obter detalhes, incluindo a disponibilidade da região.

### <a name="proto"></a>Apoiar tanto os protocolos de transporte tCP como uDP com uma regra única

Provavelmente desejará usar "All" para o protocolo de transporte da regra de saída, mas também pode aplicar a regra de saída para um protocolo de transporte específico também se for necessário para fazer isso.

Utilize o parâmetro seguinte para definir o protocolo como TCP e UDP:

          "protocol": "All"

### <a name="disablesnat"></a>Desativar o NAT de saída para uma regra de equilíbrio de carga

Conforme indicado anteriormente, as regras de balanceamento de carga fornecem uma programação automática de NAT de saída. No entanto, alguns cenários beneficiam ou tem de desativar a programação automática de NAT de saída por regra para permitem controlar ou refinar o comportamento de balanceamento de carga.  Regras de saída com cenários em que é importante parar a programação de NAT de saída automática.

Pode utilizar este parâmetro de duas formas:
- Supressão opcional de utilizar o endereço IP de entrada para NAT de saída.  Regras de saída são incrementais para regras de balanceamento de carga e com este conjunto de parâmetros, a regra de saída está no controle.
  
- Otimize os parâmetros NAT de saída de um endereço IP utilizado para entrada e saída em simultâneo.  A programação de NAT de saída automática tem de ser desativada para permitir que uma regra de saída assumir o controlo.  Por exemplo, para alterar a alocação de porta SNAT de um endereço também utilizado para entrada, este parâmetro tem de ser definido como true.  Se tentar utilizar uma regra de saída para redefinir os parâmetros de um endereço IP também utilizado para entrada e não tem lançado a programação de NAT de saída da regra de balanceamento de carga, a operação para configurar uma regra de saída irá falhar.

>[!IMPORTANT]
> A sua máquina virtual não terá conectividade de saída se definir este parâmetro verdadeiro e não tiver uma regra de saída (ou [cenário ip público de nível de instância](load-balancer-outbound-connections.md#ilpip) para definir a conectividade de saída.  Algumas operações de VM ou de seu aplicativo podem depender de ter conectividade de saída disponível. Certifique-se de que compreende as dependências do seu cenário e tenha considerado o impacto de efetuar esta alteração.

Pode desativar o SNAT de saída a regra com este parâmetro de configuração de balanceamento de carga:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

O parâmetro dedesactivadorOutboundSNAT predefinido para falso, o que significa que a regra de equilíbrio de carga **fornece** NAT de saída automática como uma imagem espelhada da configuração da regra de equilíbrio de carga.  

Se definir disableOutboundSnat como true na regra de balanceamento de carga, a regra de balanceamento de carga libera o controle da programação de NAT de saída outra forma automática.  SNAT de saída como resultado a regra de balanceamento de carga está desativada.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Reutilizar existente ou definir novos conjuntos de back-end

Regras de saída não introduz um novo conceito para definir o grupo de VMs para o qual a regra deve ser aplicada.  Em vez disso, eles reutilizar o conceito de um conjunto de back-end, que também é usado para regras de balanceamento de carga. Pode usar isso para simplificar a configuração reutilizar uma definição de conjunto de back-end existente ou criando um especificamente para uma regra de saída.

## <a name="scenarios"></a>Cenários

### <a name="groom"></a>Ligações de saída do noivo a um conjunto específico de endereços IP públicos

Pode utilizar uma regra de saída para o tratamento de ligações de saída para aparecer se originar de um conjunto específico de endereços IP públicos para facilitar cenários de listas de permissões.  Este endereço IP público de origem pode ser igual ao utilizado por uma regra de balanceamento de carga ou endereços de um conjunto diferente de IP público que utilizada por uma regra de balanceamento de carga.  

1. Criar [prefixo ip público](https://aka.ms/lbpublicipprefix) (ou endereços IP públicos a partir de prefixo IP público)
2. Criar um Balanceador de Carga Standard público
3. Criar a referenciar o público de front-ends prefixo IP (ou endereços IP públicos) que pretende utilizar
4. Reutilizar um conjunto de back-end ou criar um conjunto de back-end e coloque as VMs num conjunto de back-end de Balanceador de carga público
5. Configurar uma regra de saída no balanceador de carga público programar NAT de saída para estas VMs com o front-ends
   
Se não desejar que a regra de equilíbrio da carga seja utilizada para a saída, tem de [desativar o SNAT](#disablesnat) de saída na regra de equilíbrio de carga.

### <a name="modifysnat"></a>Modificar a atribuição da porta SNAT

Pode utilizar regras de saída para afinar a atribuição automática da [porta SNAT com base no tamanho da piscina de backend](load-balancer-outbound-connections.md#preallocatedports).

Por exemplo, se tiver duas máquinas virtuais a partilha de um único endereço IP público para o NAT de saída, pode querer aumentar o número de portas SNAT alocados das portas predefinidas 1024, se estiver tendo o esgotamento de SNAT. Cada endereço IP público pode contribuir com até 64.000 portas efémeras.  Se configurar uma regra de saída com um único endereço IP público frontend, você pode distribuir um total de 64.000 portas SNAT para VMs na piscina de backend.  Para dois VMs, um máximo de 32.000 portas SNAT pode ser alocado com uma regra de saída (2x 32.000 = 64.000).

Reveja as [ligações de saída](load-balancer-outbound-connections.md) e os detalhes sobre a forma como as portas [SNAT](load-balancer-outbound-connections.md#snat) são atribuídas e utilizadas.

### <a name="outboundonly"></a>Ativar apenas a saída

Pode utilizar um balanceador de carga Standard público para fornecer NAT de saída para um grupo de VMs. Neste cenário, pode utilizar uma regra de saída por si só, sem a necessidade de quaisquer regras adicionais.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>NAT de saída para VMs apenas (não entrada)

Definir um balanceador de carga Standard público, coloque as VMs em conjunto de back-end e configurar uma regra de saída para NAT de saída do programa e tratar as ligações de saída se originar de um endereço IP público específico. Também pode utilizar um prefixo IP público simplificar a criar uma lista a origem de ligações de saída.

1. Crie um balanceador de carga Standard público.
2. Criar um conjunto de back-end e coloque as VMs num conjunto de back-end de Balanceador de carga público.
3. Configure uma regra de saída no balanceador de carga público programar NAT de saída para estas VMs.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>NAT de saída para cenários de Balanceador de carga internos

Ao usar um Standard Balanceador de carga interno, NAT de saída não está disponível até que a conectividade de saída foi declarada explicitamente. Pode definir a conectividade de saída utilizando uma regra de saída para criar a conectividade de saída para VMs por trás de um balanceador de carga interno padrão com estes passos:

1. Crie um balanceador de carga Standard público.
2. Criar um conjunto de back-end e coloque as VMs num conjunto de back-end de Balanceador de carga público, além do Balanceador de carga interno.
3. Configure uma regra de saída no balanceador de carga público programar NAT de saída para estas VMs.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Ativar protocolos TCP e UDP para NAT de saída com um balanceador de carga Standard público

- Ao usar um Standard Balanceador de carga público, a programação de NAT de saída automática, fornecida corresponde o protocolo de transporte de regra de balanceamento de carga.  

   1. Desative o SNAT de saída a regra de balanceamento de carga.
   2. Configure uma regra de saída no mesmo Balanceador de carga.
   3. Reutilize o conjunto de back-end já utilizado por suas VMs.
   4. Especificar "protocolo": "Tudo" como parte da regra de saída.

- Quando são utilizadas apenas regras NAT de entrada, é fornecido sem NAT de saída.

   1. Colocar VMs num conjunto de back-end.
   2. Defina uma ou mais configurações de IP de front-end com o endereço de IP público (s) ou o prefixo do IP público.
   3. Configure uma regra de saída no mesmo Balanceador de carga.
   4. Especificar "protocolo": "Tudo" como parte da regra de saída

## <a name="limitations"></a>Limitações

- O número máximo de portas efémeras utilizáveis por endereço IP frontal é de 64.000.
- O intervalo do tempo de saída configurável é de 4 a 120 minutos (240 a 7200 segundos).
- Balanceador de carga não suporta o ICMP para NAT de saída.
- As regras de saída só podem ser aplicadas à configuração primária de IP de um NIC.  Vários NICs são suportados.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a utilizar o [Balancer de Carga para ligações de saída](load-balancer-outbound-connections.md).
- Saiba mais sobre o Equilíbrio de [Carga Padrão.](load-balancer-standard-overview.md)
- Saiba mais sobre [o Reset bidirecional do TCP no tempo de paragem inativo](load-balancer-tcp-reset.md).
- [Configure as regras de saída com o Azure CLI 2.0](configure-load-balancer-outbound-cli.md).
