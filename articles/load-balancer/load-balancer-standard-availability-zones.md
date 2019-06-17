---
title: Balanceador de carga Standard do Azure e zonas de disponibilidade
titlesuffix: Azure Load Balancer
description: Balanceador de Carga Standard e Zonas de Disponibilidade
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: kumud
ms.openlocfilehash: 6f33be6e418366f57d243f578035b5c87079c99e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60734461"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Balanceador de Carga Standard e Zonas de Disponibilidade

De SKU o Azure do Balanceador de carga Standard suporta [zonas de disponibilidade](../availability-zones/az-overview.md) cenários. Vários novos conceitos estão disponíveis com o Balanceador de carga Standard, que permitem otimizar a disponibilidade no seu cenário de ponto-a-ponto, alinhando a recursos com zonas e distribuí-los em zonas.  Revisão [zonas de disponibilidade](../availability-zones/az-overview.md) para obter orientações sobre o que são zonas de disponibilidade, as regiões que suportam atualmente as zonas de disponibilidade e outros relacionados com conceitos e produtos. As zonas de disponibilidade em combinação com o Balanceador de carga Standard são um conjunto de recursos de abrangente e flexível que pode criar vários cenários diferentes.  Reveja este documento para compreender estas [conceitos](#concepts) e o cenário fundamental [documentação de orientação](#design).

>[!IMPORTANT]
>Revisão [zonas de disponibilidade](../availability-zones/az-overview.md) para tópicos relacionados, incluindo quaisquer informações específicas da região.

## <a name="concepts"></a> Conceitos de zonas de disponibilidade aplicados ao balanceador de carga

Não existe nenhuma relação direta entre recursos do Balanceador de carga e a infraestrutura real; criar um balanceador de carga não cria uma instância. Recursos do Balanceador de carga são objetos nos quais pode expressar como Azure deve programar a sua infra-estrutura de multi-inquilino criados previamente para alcançar o cenário que deseja criar.  Isso é significativo no contexto das zonas de disponibilidade, porque um único recurso de Balanceador de carga pode controlar a programação de infra-estrutura em várias zonas de disponibilidade, enquanto um serviço com redundância de zona é apresentado como um recurso de um ponto de vista do cliente.

Funções de um recurso de Balanceador de carga são expressos como um front-end, uma regra, uma sonda de estado de funcionamento e uma definição de conjunto de back-end.

No contexto das zonas de disponibilidade, as propriedades de um recurso de Balanceador de carga e o comportamento são descritas como com redundância de zona ou zonais.  Com redundância de zona e zonais descrevem zonality de uma propriedade.  No contexto do Balanceador de carga, com redundância de zona sempre significa *várias zonas* e zonais significa que o isolamento de serviço para um *única zona*.

Balanceador de carga públicos e internos suportam cenários com redundância de zona e zonais e ambos podem direcionar o tráfego por várias zonas conforme necessário (*balanceamento de carga entre zonas*).

Um recurso de Balanceador de carga em si é regionais e zonais nunca.  E uma VNet e uma sub-rede são sempre regionais e zonais nunca.

### <a name="frontend"></a>Front-end

Um front-end de Balanceador de carga é uma configuração de IP de front-end que referencia um recurso de endereço IP público ou um endereço IP privado na sub-rede de um recurso de rede virtual.  Compõe o ponto final com balanceamento de carga em que o seu serviço está exposto.

Um recurso de Balanceador de carga pode conter o front-ends com redundância de zona tanto zonal em simultâneo. 

Quando um recurso IP público tem sido garantido para uma zona, o zonality (ou a falta delas) não mutável.  Se desejar alterar ou omita o zonality de um front-end IP público, terá de recriar o IP público na zona apropriada.  

Pode alterar o zonality de um front-end de um balanceador de carga interno, remover e recriar o front-end, alterar ou omitindo o zonality.

Ao utilizar vários front-ends, reveja [vários front-ends de Balanceador de carga](load-balancer-multivip-overview.md) para considerações importantes.

#### <a name="zone-redundant-by-default"></a>Zona redundante por predefinição

>[!IMPORTANT]
>Revisão [zonas de disponibilidade](../availability-zones/az-overview.md) para tópicos relacionados, incluindo quaisquer informações específicas da região.

Numa região com zonas de disponibilidade, um front-end de Balanceador de carga Standard é com redundância de zona por predefinição.  Um endereço IP de front-end único pode sobreviver a falha de zona e pode ser utilizado para chegar a todos os membros do agrupamento de back-end, independentemente da zona. Isso não significa que o caminho de dados hitless, mas qualquer repetições ou reestablishment será concluída com êxito. Esquemas de redundância DNS não são necessárias. Endereço IP do front-end único é atendido em simultâneo por várias implementações de infraestrutura independentes em várias zonas de disponibilidade.  Com redundância de zona significa que todos os fluxos de entrada ou saídos são servidos por várias zonas de disponibilidade numa região simultaneamente com um único endereço IP.

Um ou mais zonas de disponibilidade pode falhar e o caminho de dados, desde que uma zona no permanece região sobrevive a bom estado de funcionamento. Configuração com redundância de zona é a predefinição e requer que não existem ações adicionais.  

Utilize o seguinte script para criar um endereço IP público com redundância de zona para o Balanceador de carga interno. Se estiver a utilizar modelos do Resource Manager existente na sua configuração, adicione a **sku** secção para estes modelos.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Utilize o seguinte script para criar um endereço IP de front-end com redundância de zona para o Balanceador de carga interno. Se estiver a utilizar modelos do Resource Manager existente na sua configuração, adicione a **sku** secção para estes modelos.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

#### <a name="optional-zone-isolation"></a>Isolamento de zona opcional

Pode optar por ter um front-end garantido para uma única zona, que é conhecido como um *front-end zonal*.  Isso significa que qualquer fluxo de entrada ou saído é atendido por uma única zona de uma região.  O destino de partilhas de front-end com o estado de funcionamento da zona.  O caminho de dados não é afetado por falhas em horários diferentes onde foi garantida. Pode usar o front-ends zonal para expor um endereço IP por zona de disponibilidade.  Além disso, pode consumir o front-ends zonal diretamente ou, quando o front-end consiste em endereços IP públicos, integrá-las num produto de balanceamento de carga DNS como [Gestor de tráfego](../traffic-manager/traffic-manager-overview.md) e utilizar um único nome DNS, que um cliente será resolvido para vários endereços IP zonais.  Pode também utilizar isso para expor por zona com balanceamento de carga pontos finais para monitorizar individualmente cada zona.  Se desejar misturar esses conceitos (com redundância de zona e zonais para o mesmo back-end), reveja [vários front-ends de Balanceador de carga do Azure](load-balancer-multivip-overview.md).

Para um público Balanceador de carga front-end, adiciona um *zonas* parâmetro para o IP público, referenciado pela configuração de IP de front-end.  

Para um frontend de Balanceador de carga interno, adicione uma *zonas* parâmetro para a configuração de IP de front-end de Balanceador de carga interno. O front-end zonal faz com que o Balanceador de carga garantir um endereço IP numa sub-rede para uma zona específica.

Utilize o seguinte script para criar um endereço IP público Standard zonal em 1 de zona de disponibilidade. Se estiver a utilizar modelos do Resource Manager existente na sua configuração, adicione a **sku** secção para estes modelos.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Utilize o seguinte script para criar um interno Balanceador de carga Standard front-end em 1 de zona de disponibilidade.

Se estiver a utilizar modelos do Resource Manager existente na sua configuração, adicione a **sku** secção para estes modelos. Além disso, definir o **zonas** propriedade na configuração de IP de front-end para o recurso de subordinados.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

### <a name="cross-zone-load-balancing"></a>Balanceamento de carga entre zonas

Balanceamento de carga entre zonas, é a capacidade de Balanceador de carga para chegar a um ponto de extremidade de back-end em qualquer zona e é independente do front-end e o respetivo zonality.

Se desejar alinhar e garantir a implementação dentro de uma única zona, alinhe front-end zona e recursos de back-end zonal para a mesma zona. Não são necessárias mais ações.

### <a name="backend"></a>Back-end

Balanceador de carga funciona com as máquinas virtuais.  Qualquer VM na VNet individual pode ser parte do agrupamento, independentemente de estar ou não tinha garantia para uma zona ou exatamente em qual zona tinha garantia de back-end.

Se desejar alinhar e garante sua front-end e back-end com uma única zona, apenas coloca VMs na mesma zona para o pool de back-end respectivos.

Se desejar endereço VMs em várias zonas, simplesmente coloque VMs a partir de várias zonas no mesmo conjunto de back-end.  Ao utilizar o dimensionamento de máquinas virtuais conjuntos, pode colocar um ou mais conjuntos de dimensionamento de máquinas virtuais ao mesmo conjunto de back-end.  E cada um desses conjuntos de dimensionamento de máquina virtual pode estar numa única ou várias zonas.

### <a name="outbound-connections"></a>Ligações de saída

[As ligações de saída](load-balancer-outbound-connections.md) são servidos pelo todas as zonas e são automaticamente com redundância de zona numa região com zonas de disponibilidade quando uma máquina virtual é associada um balanceador de carga público e um front-end com redundância de zona.  Alocações de porta SNAT de ligação de saída resistir a falhas de zona.  

Por sua vez, se a VM estiver associada um balanceador de carga público e um front-end zonal, as ligações de saída são garantidas sejam atendidos por uma única zona.  As ligações de saída partilham o destino com o estado de funcionamento da respetiva zona.

A pré-alocação de porta SNAT e o algoritmo é o mesmo com ou sem zonas.

### <a name="health-probes"></a>Sondas do estado de funcionamento

As definições de sonda de estado de funcionamento existentes permanecem como sem zonas de disponibilidade.  Mas, Expandimos o modelo de estado de funcionamento ao nível da infraestrutura. 

Ao utilizar com redundância de zona front-ends, Balanceador de carga expande o seu modelo de estado de funcionamento interno para a acessibilidade de uma VM a partir de cada zona de disponibilidade de sonda e encerrar caminhos em zonas que podem ter falhado sem a intervenção do cliente de forma independente.  Se um determinado caminho não estiver disponível da infraestrutura de Balanceador de carga de uma zona a uma VM noutra zona, o Balanceador de carga pode detectar e evitar esta falha. Outras zonas que podem aceder esta VM, podem continuar a atendê-a VM a partir de seus respectivos front-ends.  Como resultado, é possível que durante eventos de falha, cada zona pode ter distribuições de fluxo ligeiramente diferente ao proteger o estado de funcionamento global do seu serviço de ponto-a-ponto.

## <a name="design"></a> Considerações de design

Balanceador de carga é propositadamente flexível no contexto das zonas de disponibilidade. Pode optar por se alinham com as zonas ou pode optar por ser com redundância de zona.  Maior disponibilidade têm o preço do aumento da complexidade e criar disponibilidade para um desempenho ideal.  Vamos dar uma olhada em algumas considerações de design importantes.

### <a name="automatic-zone-redundancy"></a>Redundância de zona automática

Balanceador de carga simplifica a ter um único IP como um front-end com redundância de zona. Um endereço IP com redundância de zona com segurança pode servir um recurso zonal em qualquer zona e pode sobreviver uma ou mais falhas de zona, desde que uma zona permaneça em bom estado dentro da região. Por outro lado, um front-end zonal é uma redução do serviço para um único destino de zona e partilhas com a respetiva zona.

Redundância de zona não implica hitless datapath ou plano de controlo  é expressamente plano de dados. Fluxos com redundância de zona podem utilizar qualquer zonas e fluxos de um cliente irão utilizar todas as zonas de bom estado de funcionamento numa região. Em caso de falha de zona, os fluxos de tráfego com zonas de bom estado de funcionamento neste ponto no tempo não são afetados.  Fluxos de tráfego com uma zona no momento da falha de zona podem ser afetados, mas podem recuperar os aplicativos e estes fluxos podem continuar nas zonas de bom estado de funcionamento da restantes dentro da região base retransmissão ou reestablishment assim que o Azure foi convergido em torno da falha de zona.

### <a name="xzonedesign"></a> Ultrapassar os limites de zona

É importante compreender que sempre que um serviço de ponto-a-ponto cruza zonas, partilhar o destino com uma zona, não, mas potencialmente várias zonas.  Como resultado, o serviço de ponto a ponto poderá não passaram a qualquer disponibilidade sobre as implementações não zonais.

Evite introduzir dependências de entre zonas indesejadas, que serão anula os ganhos de disponibilidade ao utilizar as zonas de disponibilidade.  Quando a aplicação é composta por vários componentes e pretender ser resiliente a falhas de zona, deve ter cuidado para garantir que o sobrevivência de componentes críticos suficientes em caso de uma falha de zona.  Por exemplo, um único componente crítico para a sua aplicação pode afetar todo o seu aplicativo se existir apenas numa zona que não seja as zona ou zonas operacional.  Além disso, considere também a restauração de zona e como seu aplicativo irá convergir. Vamos rever alguns pontos importantes e utilizá-los a como inspiração para perguntas sobre como analisar o seu cenário específico.

- Se seu aplicativo tem dois componentes, como um endereço IP e uma VM com disco gerido e tem a garantia na zona 1 e zona 2, quando seu serviço de ponto-a-ponto de falha de zona 1 não irão sobreviver quando a zona 1 falha.  Não em várias zonas, a menos que entenda que está a criar um modo de falha potencialmente perigosa como essa.

- Se seu aplicativo tem dois componentes, como um endereço IP e uma VM com managed disco e é garantido que ser com redundância de zona e a zona 1, respectivamente, o serviço de ponto-a-ponto irão sobreviver a falha de zona da zona 2, zona 3, ou ambos, a menos que a zona 1 falhou.  No entanto, perde a alguma capacidade de ponderar sobre o estado de funcionamento do seu serviço se tudo o que está a experienciar é a acessibilidade de front-end.  Pondere desenvolver um modelo de estado de funcionamento e a capacidade mais amplo.  Poderá utilizar em conjunto com redundância de zona e zonais conceitos para expandir as informações e a capacidade de gestão.

- Se o aplicativo tem dois componentes, como um front-end de Balanceador de carga com redundância de zona e um conjunto de dimensionamento de máquinas virtuais de zona entre três zonas, seus recursos em zonas não são afetados pela falha irão estar disponíveis, mas a capacidade do serviço de ponto a ponto poderá estar degradada durante a falha de zona. Da perspectiva de infraestrutura, a implementação pode sobreviver a uma ou mais falhas de zona, e isso levanta as seguintes perguntas:
  - Compreender como a sua aplicação motivos pelos quais sobre tais falhas e a capacidade de degradado?
  - Precisa de ter salvaguardas no seu serviço para forçar uma ativação pós-falha para um par de região, se necessário?
  - Como irá monitorizar, detetar e mitigar um cenário como esse? Poderá utilizar os diagnósticos do Balanceador de carga Standard para aumentar a monitorização do seu desempenho do serviço de ponto-a-ponto. Considere o que está disponível e o que poderá precisar do aumento de para uma visão completa.

- Zonas podem tornar as falhas mais facilmente compreendido e contido.  No entanto, a falha de zona não é diferente de outras falhas quando se trata de conceitos, como tempos limite, as repetições e os algoritmos de término. Apesar do Balanceador de carga do Azure fornece caminhos com redundância de zona e tenta recuperar rapidamente, num nível de pacote em tempo real, retransmissões ou reestablishments poderão ocorrer durante o princípio de uma falha e é importante compreender como o seu aplicativo lida com falhas. O esquema de balanceamento de carga irão sobreviver, mas precisa planejar para o seguinte:
  - Quando ocorre uma falha numa zona, seu serviço de ponto-a-ponto compreender isso e se o estado for perdido, como recuperará?
  - Quando uma zona retorna, a sua aplicação compreender como convergir com segurança?

### <a name="zonalityguidance"></a> Com redundância de zona versus zonal

>[!IMPORTANT]
>Revisão [zonas de disponibilidade](../availability-zones/az-overview.md) para tópicos relacionados, incluindo quaisquer informações específicas da região.

Com redundância de zona pode fornecer um zona agnóstico e a mesma opção resiliente tempo com um único IP endereço para o serviço.  Pode reduzir complexidade por sua vez.  Com redundância de zona também tem mobilidade em zonas e pode ser usado com segurança em recursos em qualquer zona.  Além disso, é durável nas regiões sem zonas de disponibilidade, que pode limitar as alterações necessárias depois de uma região de obter as zonas de disponibilidade.  A sintaxe de configuração para um endereço IP com redundância de zona ou front-end for concluída com êxito em qualquer região, incluindo as pessoas sem zonas de disponibilidade.

Zonal pode fornecer uma garantia explícita a uma zona, compartilhamento de destino com o estado de funcionamento da zona. Associar um IP zonal endereço ou zonal front-end de Balanceador de carga pode ser um atributo desejável ou razoável especialmente se o seu recurso ligado é uma VM zonal na mesma zona.  Ou talvez seu aplicativo requer conhecimento explícito sobre qual zona um recurso está localizado em e desejar ponderar sobre a disponibilidade em zonas separadas explicitamente.  Pode optar por expor vários front-ends zonal para um serviço de ponto-a-ponto distribuído por várias zonas (ou seja, por zona front-ends zonal para vários dimensionamento de máquinas de virtuais zonal define).  E se o front-ends zonais são endereços IP públicos, pode utilizar esses vários front-ends zonais para expor seu serviço com [Gestor de tráfego](../traffic-manager/traffic-manager-overview.md).  Ou pode usar vários front-ends zonal para obter por informações de estado de funcionamento e desempenho de zona através de terceiros, soluções de monitorização e expor o serviço global com um front-end com redundância de zona. Apenas deve servir recursos zonais com front-ends zonal alinhados com a mesma zona e evitar potencialmente prejudiciais entre zonas cenários para recursos zonais.  Recursos zonais só existem em regiões onde existem zonas de disponibilidade.

Não existe nenhuma orientação geral que uma é uma opção melhor do que o outro sem conhecer a arquitetura de serviço.

## <a name="limitations"></a>Limitações

- Embora os dados de plano é totalmente com redundância de zona (a menos que foi especificada a garantia de zonal), operações do painel de controlo não são totalmente com redundância de zona.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [zonas de disponibilidade](../availability-zones/az-overview.md)
- Saiba mais sobre o [Balanceador de Carga Standard](load-balancer-standard-overview.md)
- Saiba como [balanceamento de carga de VMs dentro de uma zona com um balanceador de carga Standard com um front-end zonal](load-balancer-standard-public-zonal-cli.md)
- Saiba como [balanceamento de carga de VMs por zonas de utilizar um balanceador de carga Standard com um front-end com redundância de zona](load-balancer-standard-public-zone-redundant-cli.md)
