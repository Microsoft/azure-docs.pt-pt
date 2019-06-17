---
title: Regras de saída no balanceador de carga do Azure
titlesuffix: Azure Load Balancer
description: Utilizar regras de saída para definir conversões de endereço de rede de saída
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2018
ms.author: kumud
ms.openlocfilehash: 52fafa7e9dd46b6c78af3776797bae48b22ea8df
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64698438"
---
# <a name="load-balancer-outbound-rules"></a>Regras de saída do Balanceador de carga

O Balanceador de carga do Azure fornece a conectividade de saída da rede virtual, além de entrada.  Regras de saída tornam mais fácil configurar pública [Balanceador de carga Standard](load-balancer-standard-overview.md)da tradução de endereços de rede de saída.  Tem controle completo declarativo sobre conectividade de saída para dimensionar e ajustar esta capacidade às suas necessidades específicas.

![Regras de saída do Balanceador de carga](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Com as regras de saída, pode usar o Balanceador de carga: 
- Defina NAT de saída do zero.
- Dimensionar e otimizar o comportamento de NAT de saída existentes. 

Regras de saída permitem controlar:
- quais as máquinas virtuais devem ser convertidas para que endereços IP públicos. 
- como [portas de SNAT de saída](load-balancer-outbound-connections.md#snat) deve ser alocado.
- os protocolos para fornecer a tradução de saída para.
- o que duração a utilizar para a ligação de saída tempo limite de inatividade (4-120 minutos).
- Se enviar uma reposição do TCP no tempo limite de inatividade (em pré-visualização pública). 

Regras de saída expandir [cenário 2](load-balancer-outbound-connections.md#lb) descritas na [ligações de saída](load-balancer-outbound-connections.md) artigo e a precedência de cenário permanece como-é.

## <a name="outbound-rule"></a>Regra de saída

Como todas as regras de Balanceador de carga, regras de saída, siga a sintaxe familiar do mesmo como balanceamento de carga e regras NAT de entrada:

**front-end** + **parâmetros** + **conjunto back-end**

Configura o NAT de saída para uma regra de saída _todas as máquinas de virtuais identificadas pelo pool de back-end_ ser traduzida para o _front-end_.  E _parâmetros_ fornecer controle de detalhadas adicional sobre o algoritmo NAT de saída.

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
>A configuração de NAT de saída eficaz é uma composição de todas as regras de saída e regras de balanceamento de carga. Regras de saída são incrementais para regras de balanceamento de carga. Revisão [desativar a NAT de saída para uma regra de balanceamento de carga](#disablesnat) para gerir a tradução de NAT de saída em vigor quando várias regras se aplicam a uma VM. Deve [desativar SNAT de saída](#disablesnat) ao definir uma regra de saída que está a utilizar o mesmo endereço IP público como uma regra de balanceamento de carga.

### <a name="scale"></a> NAT de saída de dimensionamento com vários endereços IP

Enquanto uma regra de saída pode ser utilizada com apenas um único endereço IP público, regras de saída aliviar a carga de configuração de dimensionamento de NAT de saída. Pode usar vários endereços IP para planear para cenários em grande escala e pode usar as regras de saída para mitigar [esgotamento de SNAT](load-balancer-outbound-connections.md#snatexhaust) propenso a padrões.  

Cada endereço IP adicional fornecido por um front-end fornece 51,200 portas efêmeras para Balanceador de carga utilizar como portas SNAT. Enquanto o balanceamento de carga ou regras NAT de entrada tem um front-end único, a regra de saída se expande a noção de front-end e permite que vários front-ends por regra.  Com vários front-ends por regra, a quantidade de portas SNAT disponíveis é multiplicada com cada endereço IP público e cenários de grandes dimensões podem ser suportados.

Além disso, pode utilizar um [prefixo do IP público](https://aka.ms/lbpublicipprefix) diretamente com uma regra de saída.  Utilizar um IP público de prefixo fornece para dimensionamento mais fácil e simplificada de criar uma lista de fluxos provenientes da implementação do Azure. Pode configurar uma configuração de IP de front-end dentro de um prefixo de endereço IP público de referenciar diretamente o recurso de Balanceador de carga.  Isso permite que o Balanceador de carga um controlo exclusivo sobre o prefixo do IP público e a regra de saída irá utilizar automaticamente a todos os endereços IP públicos contidos o prefixo do IP público para ligações de saída.  Cada um dos endereços IP dentro do intervalo do prefixo do IP público do fornecem 51,200 portas efêmeras por endereço IP para o Balanceador de carga utilizar como portas SNAT.   

Não pode ter individuais recursos de endereço IP público criados a partir do prefixo do IP público ao utilizar esta opção, como a regra de saída tem de ter controlo total sobre o prefixo do IP público.  Se precisar de mais controle refinado, pode criar o recurso de endereço IP público individual a partir de prefixo IP público e atribuir vários endereços IP públicos individualmente para o front-end de uma regra de saída.

### <a name="snatports"></a> Ajustar a alocação de porta SNAT

Pode utilizar regras de saída para otimizar a [a alocação de porta SNAT automática com base no tamanho do conjunto de back-end](load-balancer-outbound-connections.md#preallocatedports) e alocar mais ou menos do que fornece a alocação de porta SNAT automática.

Utilize o parâmetro seguinte para alocar SNAT 10.000 portas por VM (configuração de IP de NIC).
 

          "allocatedOutboundPorts": 10000

Cada endereço IP público de front-ends todos de uma regra de saída contribui até 51,200 portas efêmeras para utilização como portas SNAT.  Balanceador de carga aloca SNAT portas em múltiplos de 8. Se fornecer um valor não divisível por 8, a operação de configuração é rejeitada.  Se tentar alocar o SNAT mais portas que estão disponíveis com base no número de endereços IP públicos, a operação de configuração é rejeitada.  Por exemplo, se alocar portas de 10 000 por VM e 7 VMs num back-end conjunto compartilhariam um endereço IP público único, a configuração é rejeitadas (7 x 10 000 SNAT portas > 51,200 SNAT portas).  Pode adicionar endereços IP públicos mais para o front-end da regra de saída para ativar o cenário.

Pode reverter para [a alocação de porta SNAT automática com base no tamanho do conjunto de back-end](load-balancer-outbound-connections.md#preallocatedports) especificando 0 para o número de portas.

### <a name="idletimeout"></a> Tempo limite de inatividade de fluxo de saída do controle

Regras de saída fornecem um parâmetro de configuração para controlar o tempo de limite de inatividade de fluxo de saída e fazer sua correspondência com as necessidades da sua aplicação.  Padrão de limites de ociosidade de saída a 4 minutos.  O parâmetro aceita um valor de 4 a 120 como específico, o número de minutos para que o tempo limite de inatividade para os fluxos que correspondam esta regra específica.

Utilize o parâmetro seguinte para definir o tempo de limite de inatividade saído para 1 hora:

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a> <a name="tcpreset"></a> Ativar a reposição de TCP no tempo limite de inatividade (pré-visualização)

O comportamento padrão do Balanceador de carga é descartar o fluxo de forma silenciosa quando for atingido o tempo de limite de inatividade saído.  Com o parâmetro enableTCPReset, pode ativar um comportamento mais previsível em termos de aplicação e controlar se pretende enviar bidirecional (TCP RST) de reposição de TCP no tempo limite de tempo limite de inatividade saída. 

Utilize o parâmetro seguinte para ativar a reposição de TCP de mensagens em fila numa regra de saída:

           "enableTcpReset": true

Revisão [TCP repostas no tempo limite de inatividade (pré-visualização)](https://aka.ms/lbtcpreset) para obter detalhes, incluindo a disponibilidade de região.

### <a name="proto"></a> Suportar protocolos de transporte TCP e UDP com uma única regra

Provavelmente desejará usar "All" para o protocolo de transporte da regra de saída, mas também pode aplicar a regra de saída para um protocolo de transporte específico também se for necessário para fazer isso.

Utilize o parâmetro seguinte para definir o protocolo como TCP e UDP:

          "protocol": "All"

### <a name="disablesnat"></a> Desativar NAT de saída para uma regra de balanceamento de carga

Conforme indicado anteriormente, as regras de balanceamento de carga fornecem uma programação automática de NAT de saída. No entanto, alguns cenários beneficiam ou tem de desativar a programação automática de NAT de saída por regra para permitem controlar ou refinar o comportamento de balanceamento de carga.  Regras de saída com cenários em que é importante parar a programação de NAT de saída automática.

Pode utilizar este parâmetro de duas formas:
- Supressão opcional de utilizar o endereço IP de entrada para NAT de saída.  Regras de saída são incrementais para regras de balanceamento de carga e com este conjunto de parâmetros, a regra de saída está no controle.
  
- Otimize os parâmetros NAT de saída de um endereço IP utilizado para entrada e saída em simultâneo.  A programação de NAT de saída automática tem de ser desativada para permitir que uma regra de saída assumir o controlo.  Por exemplo, para alterar a alocação de porta SNAT de um endereço também utilizado para entrada, este parâmetro tem de ser definido como true.  Se tentar utilizar uma regra de saída para redefinir os parâmetros de um endereço IP também utilizado para entrada e não tem lançado a programação de NAT de saída da regra de balanceamento de carga, a operação para configurar uma regra de saída irá falhar.

>[!IMPORTANT]
> A máquina virtual não terão conectividade de saída se definir este parâmetro como true e não tem uma regra de saída (ou [cenário IP público ao nível da instância](load-balancer-outbound-connections.md#ilpip) para definir a conectividade de saída.  Algumas operações de VM ou de seu aplicativo podem depender de ter conectividade de saída disponível. Certifique-se de que compreende as dependências do seu cenário e tenha considerado o impacto de efetuar esta alteração.

Pode desativar o SNAT de saída a regra com este parâmetro de configuração de balanceamento de carga:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

O parâmetro disableOutboundSNAT é predefinido como false, o que significa que a regra de balanceamento de carga **faz** fornecer automática NAT de saída como uma imagem espelhada da configuração de regra de balanceamento de carga.  

Se definir disableOutboundSnat como true na regra de balanceamento de carga, a regra de balanceamento de carga libera o controle da programação de NAT de saída outra forma automática.  SNAT de saída como resultado a regra de balanceamento de carga está desativada.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Reutilizar existente ou definir novos conjuntos de back-end

Regras de saída não introduz um novo conceito para definir o grupo de VMs para o qual a regra deve ser aplicada.  Em vez disso, eles reutilizar o conceito de um conjunto de back-end, que também é usado para regras de balanceamento de carga. Pode usar isso para simplificar a configuração reutilizar uma definição de conjunto de back-end existente ou criando um especificamente para uma regra de saída.

## <a name="scenarios"></a>Cenários

### <a name="groom"></a> Ligações de saída de tratar a um conjunto específico de endereços IP públicos

Pode utilizar uma regra de saída para o tratamento de ligações de saída para aparecer se originar de um conjunto específico de endereços IP públicos para facilitar cenários de listas de permissões.  Este endereço IP público de origem pode ser igual ao utilizado por uma regra de balanceamento de carga ou endereços de um conjunto diferente de IP público que utilizada por uma regra de balanceamento de carga.  

1. Crie [prefixo do IP público](https://aka.ms/lbpublicipprefix) (ou endereços IP públicos de prefixo IP público)
2. Criar um Balanceador de Carga Standard público
3. Criar a referenciar o público de front-ends prefixo IP (ou endereços IP públicos) que pretende utilizar
4. Reutilizar um conjunto de back-end ou criar um conjunto de back-end e coloque as VMs num conjunto de back-end de Balanceador de carga público
5. Configurar uma regra de saída no balanceador de carga público programar NAT de saída para estas VMs com o front-ends
   
Se não pretender para a regra de balanceamento de carga a utilizar para saída, precisa [desativar SNAT de saída](#disablesnat) a regra de balanceamento de carga.

### <a name="modifysnat"></a> Modificar a alocação de porta SNAT

Pode utilizar regras de saída para otimizar a [a alocação de porta SNAT automática com base no tamanho do conjunto de back-end](load-balancer-outbound-connections.md#preallocatedports).

Por exemplo, se tiver duas máquinas virtuais a partilha de um único endereço IP público para o NAT de saída, pode querer aumentar o número de portas SNAT alocados das portas predefinidas 1024, se estiver tendo o esgotamento de SNAT. Cada endereço IP público pode contribuir com até 51,200 portas efêmeras.  Se configurar uma regra de saída com um único frontend de endereço IP público, pode distribuir um total de portas SNAT 51,200 às VMs no conjunto de back-end.  Para duas VMs, um máximo de portas SNAT 25,600 pode ser alocado com uma regra de saída (2 x 25,600 = 51,200).

Revisão [ligações de saída](load-balancer-outbound-connections.md) e os detalhes sobre como [SNAT](load-balancer-outbound-connections.md#snat) portas são alocadas e usadas.

### <a name="outboundonly"></a> Ativar apenas saída

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
   4. Especifique "protocolo": "Tudo" como parte da regra de saída.

- Quando são utilizadas apenas regras NAT de entrada, é fornecido sem NAT de saída.

   1. Colocar VMs num conjunto de back-end.
   2. Defina uma ou mais configurações de IP de front-end com o endereço de IP público (s) ou o prefixo do IP público.
   3. Configure uma regra de saída no mesmo Balanceador de carga.
   4. Especifique "protocolo": "Tudo" como parte da regra de saída

## <a name="limitations"></a>Limitações

- O número máximo de portas efêmeras utilizáveis por endereço IP de front-end é 51,200.
- O intervalo do configurável saído tempo limite de inatividade é 4 a 120 minutos (240 para 7200 segundos).
- Balanceador de carga não suporta o ICMP para NAT de saída.
- Não é possível utilizar o portal para configurar ou ver regras de saída.  Utilize modelos, REST API, Az CLI 2.0 ou PowerShell.
- Regras de saída só podem ser aplicadas para o NIC primário e a configuração de IP primária.

## <a name="next-steps"></a>Passos Seguintes

- Aprenda a usar [Balanceador de carga para ligações de saída](load-balancer-outbound-connections.md).
- Saiba mais sobre [Balanceador de carga Standard](load-balancer-standard-overview.md).
- Saiba mais sobre [bidirecional de reposição de TCP no tempo limite de inatividade](load-balancer-tcp-reset.md).
- [Configurar regras de saída com a CLI 2.0 do Azure](configure-load-balancer-outbound-cli.md).
