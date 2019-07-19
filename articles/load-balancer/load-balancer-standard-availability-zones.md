---
title: Standard Load Balancer e Zonas de Disponibilidade do Azure
titlesuffix: Azure Load Balancer
description: Balanceador de Carga Standard e Zonas de Disponibilidade
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: allensu
ms.openlocfilehash: 5ef7de148d5ef4727602b8287164f2aff9ccf822
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274504"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Balanceador de Carga Standard e Zonas de Disponibilidade

O SKU padrão de Azure Load Balancer dá suporte a cenários de [zonas de disponibilidade](../availability-zones/az-overview.md) . Vários novos conceitos estão disponíveis com Standard Load Balancer, que permitem otimizar a disponibilidade em seu cenário de ponta a ponta alinhando recursos com zonas e distribuindo-os entre zonas.  Examine [zonas de disponibilidade](../availability-zones/az-overview.md) para obter diretrizes sobre quais zonas de disponibilidade são, quais regiões atualmente dão suporte a zonas de disponibilidade e outros conceitos e produtos relacionados. Zonas de Disponibilidade em combinação com Standard Load Balancer são um conjunto de recursos extenso e flexível que pode criar vários cenários diferentes.  Examine este documento para entender esses [conceitos](#concepts) e diretrizes de [design](#design)de cenário fundamental.

>[!IMPORTANT]
>Examine [zonas de disponibilidade](../availability-zones/az-overview.md) para obter tópicos relacionados, incluindo qualquer informação específica da região.

## <a name="concepts"></a>Conceitos de Zonas de Disponibilidade aplicados a Load Balancer

Não há nenhuma relação direta entre Load Balancer recursos e a infraestrutura real; a criação de um Load Balancer não cria uma instância. Load Balancer recursos são objetos nos quais você pode expressar como o Azure deve programar sua infraestrutura de multilocatário predefinida para obter o cenário que você deseja criar.  Isso é significativo no contexto de Zonas de Disponibilidade porque um único recurso de Load Balancer pode controlar a programação de infraestrutura em vários Zonas de Disponibilidade enquanto um serviço com redundância de zona aparece como um recurso do ponto de vista do cliente.

As funções de um recurso Load Balancer são expressas como um front-end, uma regra, uma investigação de integridade e uma definição de pool de back-end.

No contexto de Zonas de Disponibilidade, o comportamento e as propriedades de um recurso de Load Balancer são descritos como com redundância de zona ou zonas.  Com redundância de zona e zonas, descrevem o zonalidade de uma propriedade.  No contexto de Load Balancer, com redundância de zona, sempre significa *várias zonas* e zonal significa isolar o serviço em uma *única zona*.

Os Load Balancer públicos e internos oferecem suporte a cenários com redundância de zona e zonas e ambos podem direcionar o tráfego entre as regiões, conforme necessário (*balanceamento de carga entre zonas*).

Um recurso Load Balancer em si é regional e nunca zonal.  E uma VNet e uma sub-rede são sempre regionais e nunca são zonais.

### <a name="frontend"></a>Front-end

Um front-end Load Balancer é uma configuração de IP de front-end referenciando um recurso de endereço IP público ou um endereço IP privado dentro da sub-rede de um recurso de rede virtual.  Ele forma o ponto de extremidade com balanceamento de carga onde o serviço é exposto.

Um recurso de Load Balancer pode conter front-ends zonais e com redundância de zonas simultaneamente. 

Quando um recurso de IP público tem sido garantido para uma zona, o zonalidade (ou a falta dele) não é mutável.  Se você quiser alterar ou omitir o zonalidade de um front-end de IP público, será necessário recriar o IP público na zona apropriada.  

Você pode alterar o zonalidade de um front-end de um Load Balancer interno removendo e recriando o front-end, alterando ou omitindo o zonalidade.

Ao usar vários front-ends, examine [vários front-ends para Load Balancer](load-balancer-multivip-overview.md) para considerações importantes.

#### <a name="zone-redundant-by-default"></a>Com redundância de zona por padrão

>[!IMPORTANT]
>Examine [zonas de disponibilidade](../availability-zones/az-overview.md) para obter tópicos relacionados, incluindo qualquer informação específica da região.

Em uma região com Zonas de Disponibilidade, um Standard Load Balancer front-end é com redundância de zona por padrão.  Um único endereço IP de front-end pode sobreviver à falha de zona e pode ser usado para alcançar todos os membros do pool de back-end, independentemente da zona. Isso não significa implica de dados de caminho, mas qualquer nova tentativa ou restabelecimento terá sucesso. Os esquemas de redundância de DNS não são necessários. O endereço IP único de front-end é servido simultaneamente por várias implantações de infraestrutura independentes em vários Zonas de Disponibilidade.  Com redundância de zona significa que todos os fluxos de entrada ou saída são servidos por vários Zonas de Disponibilidade em uma região simultaneamente usando um único endereço IP.

Um ou mais Zonas de Disponibilidade podem falhar e o caminho de dados sobreviver enquanto uma zona na região permanece íntegra. A configuração com redundância de zona é o padrão e não requer nenhuma ação adicional.  

Use o script a seguir para criar um endereço IP público com redundância de zona para seu Standard Load Balancer interno. Se você estiver usando modelos existentes do Resource Manager em sua configuração, adicione a seção **SKU** a esses modelos.

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

Use o script a seguir para criar um endereço IP de front-end com redundância de zona para seu Standard Load Balancer interno. Se você estiver usando modelos existentes do Resource Manager em sua configuração, adicione a seção **SKU** a esses modelos.

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

Você pode optar por ter um front-end garantido para uma única zona, que é conhecida como *front-end zonal*.  Isso significa que qualquer fluxo de entrada ou saída é servido por uma única zona em uma região.  Seu front-end compartilha destino com a integridade da zona.  O caminho de dados não é afetado por falhas em zonas diferentes de onde foi garantido. Você pode usar front-ends zonais para expor um endereço IP por zona de disponibilidade.  Além disso, você pode consumir front-ends zonais diretamente ou, quando o front-end consiste em endereços IP públicos, integrá-los a um produto de balanceamento de carga de DNS como o [Traffic Manager](../traffic-manager/traffic-manager-overview.md) e usar um único nome DNS, que um cliente resolverá para vários endereços IP zonais .  Você também pode usar isso para expor pontos de extremidade com balanceamento de carga por zona para monitorar individualmente cada zona.  Se você quiser misturar esses conceitos (com redundância de zona e zonas para o mesmo back-end), examine [vários front-ends para Azure Load Balancer](load-balancer-multivip-overview.md).

Para um front-end público Load Balancer, você adiciona um parâmetro de *zonas* ao IP público referenciado pela configuração de IP de front-end.  

Para um front-end interno Load Balancer, adicione um parâmetro de *zonas* à configuração de IP de front-end Load balancer interno. O front-end zonal faz com que a Load Balancer garanta um endereço IP em uma sub-rede para uma zona específica.

Use o script a seguir para criar um endereço IP público padrão de zona em Zona 1 de disponibilidade. Se você estiver usando modelos existentes do Resource Manager em sua configuração, adicione a seção **SKU** a esses modelos.

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

Use o script a seguir para criar um front-end de Standard Load Balancer interno no Zona 1 de disponibilidade.

Se você estiver usando modelos existentes do Resource Manager em sua configuração, adicione a seção **SKU** a esses modelos. Além disso, defina a propriedade **zonas** na configuração de IP de front-end para o recurso filho.

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

O balanceamento de carga entre zonas é a capacidade de Load Balancer alcançar um ponto de extremidade de back-end em qualquer zona e é independente do front-end e de seu zonalidade.

Se você quiser alinhar e garantir sua implantação em uma única zona, alinhe os recursos de back-end zonal e frontend zonas para a mesma zona. Não são necessárias mais ações.

### <a name="backend"></a>End

Load Balancer funciona com máquinas virtuais.  Qualquer VM em uma única VNet pode fazer parte do pool de back-end, independentemente de estar ou não garantido para uma zona ou em qual zona foi garantida.

Se você quiser alinhar e garantir o frontend e o back-end com uma única zona, coloque apenas as VMs dentro da mesma zona no respectivo pool de back-end.

Se você quiser endereçar VMs em várias zonas, simplesmente coloque as VMs de várias zonas no mesmo pool de back-end.  Ao usar conjuntos de dimensionamento de máquinas virtuais, você pode posicionar um ou mais conjuntos de dimensionamento de máquinas virtuais no mesmo pool de back-end.  E cada um desses conjuntos de dimensionamento de máquinas virtuais pode estar em uma única ou várias zonas.

### <a name="outbound-connections"></a>Ligações de saída

As [conexões de saída](load-balancer-outbound-connections.md) são servidas por todas as zonas e são automaticamente com redundância de zona em uma região com zonas de disponibilidade quando uma máquina virtual é associada a um Load Balancer público e um front-end com redundância de zona.  As alocações de porta SNAT de conexão de saída sobrevivem a falhas de zona.  

Por sua vez, se a VM estiver associada a um Load Balancer público e um front-end zonal, as conexões de saída terão garantia de serem atendidas por uma única zona.  As conexões de saída compartilham destino com a integridade da respectiva zona.

A prealocação de porta SNAT e o algoritmo são os mesmos com ou sem zonas.

### <a name="health-probes"></a>Sondas do estado de funcionamento

Suas definições de investigação de integridade existentes permanecem como estão sem Zonas de Disponibilidade.  Mas expandimos o modelo de integridade em um nível de infraestrutura. 

Ao usar front-ends com redundância de zona, Load Balancer expande seu modelo de integridade interno para investigar de forma independente a acessibilidade de uma VM de cada zona de disponibilidade e desligar os caminhos entre zonas que podem ter falhado sem intervenção do cliente.  Se um determinado caminho não estiver disponível na infraestrutura de Load Balancer de uma zona para uma VM em outra zona, Load Balancer poderá detectar e evitar essa falha. Outras zonas que podem acessar essa VM podem continuar a atender a VM de seus respectivos front-ends.  Como resultado, é possível que, durante os eventos de falha, cada zona possa ter distribuições de fluxo ligeiramente diferentes, ao mesmo tempo em que protege a integridade geral de seu serviço de ponta a ponta.

## <a name="design"></a>Considerações de design

A Load Balancer é intencionalmente flexível no contexto de Zonas de Disponibilidade. Você pode optar por se alinhar às zonas ou pode optar por ser com redundância de zona.  A maior disponibilidade pode chegar ao preço de maior complexidade e você deve projetar para disponibilidade para um desempenho ideal.  Vamos dar uma olhada em algumas considerações de design importantes.

### <a name="automatic-zone-redundancy"></a>Redundância de zona automática

Load Balancer torna simples ter um único IP como um front-end com redundância de zona. Um endereço IP com redundância de zona pode atender com segurança a um recurso zonal em qualquer zona e pode sobreviver a uma ou mais falhas de zona, contanto que uma zona permaneça íntegra na região. Por outro lado, um front-end zonal é uma redução do serviço para uma única zona e compartilha destino com a respectiva zona.

A redundância de zona não implica em implica ou plano de controle de caminho;  é um plano de dados expressamente. Os fluxos com redundância de zona podem usar qualquer zona e os fluxos de um cliente usarão todas as zonas íntegras em uma região. No caso de falha de zona, os fluxos de tráfego usando zonas de integridade nesse ponto no tempo não são afetados.  Os fluxos de tráfego usando uma zona no momento da falha de zona podem ser afetados, mas os aplicativos podem ser recuperados. Esses fluxos podem continuar nas zonas de integridade restantes dentro da região após a retransmissão ou o restabelecimento, uma vez que o Azure convergiu em redor da falha de zona.

### <a name="xzonedesign"></a>Limites entre zonas

É importante entender que sempre que um serviço de ponta a ponta cruza zonas, você compartilha destino com não uma zona, mas potencialmente várias zonas.  Como resultado, seu serviço de ponta a ponta pode não ter obtido nenhuma disponibilidade em implantações não zonais.

Evite introduzir dependências entre zonas não pretendidas, o que anulará ganhos de disponibilidade ao usar Zonas de Disponibilidade.  Quando seu aplicativo consiste em vários componentes e você deseja ser resiliente a falhas de zona, você deve tomar cuidado para garantir a sobrevivência de componentes críticos suficientes no caso de uma falha de zona.  Por exemplo, um único componente crítico para seu aplicativo pode afetar o aplicativo inteiro se ele existir somente em uma zona que não seja a zona (s) sobreviventes.  Além disso, considere também a restauração de zona e como seu aplicativo será convergido. Vamos examinar alguns pontos importantes e usá-los como inspiração para perguntas, conforme você imagina pelo cenário específico.

- Se seu aplicativo tiver dois componentes, como um endereço IP e uma VM com disco gerenciado, e eles estiverem garantidos na zona 1 e zona 2, quando a zona 1 falhar, seu serviço de ponta a ponta não permanecerá quando a zona 1 falhar.  Não entre zonas, a menos que você entenda totalmente que está criando um modo de falha potencialmente nocivo.

- Se seu aplicativo tiver dois componentes, como um endereço IP e uma VM com disco gerenciado, e for garantido que estejam com redundância de zona e a zona 1, respectivamente, o serviço de ponta a ponta sobreviver a falhas de zona da zona 2, zona 3 ou ambas, a menos que a zona 1 tenha falhado.  No entanto, você perde alguma capacidade de motivo da integridade do seu serviço se tudo o que você está observando é a acessibilidade do front-end.  Considere desenvolver um modelo de integridade e capacidade mais extensivo.  Você pode usar os conceitos com redundância de zona e zonas para expandir a percepção e a capacidade de gerenciamento.

- Se seu aplicativo tiver dois componentes, como um front-end de Load Balancer com redundância de zona e um conjunto de dimensionamento de máquinas virtuais entre zonas em três zonas, seus recursos em zonas não impactados por falha estarão disponíveis, mas sua capacidade de serviço de ponta a ponta pode estar degradada durante a falha de zona. Do ponto de vista da infra-estrutura, sua implantação pode sobreviver a uma ou mais falhas de zona, e isso gera as seguintes perguntas:
  - Você entende como o seu aplicativo tem a ver com falhas e capacidade degradada?
  - Você precisa ter proteções em seu serviço para forçar um failover para um par de regiões, se necessário?
  - Como você vai monitorar, detectar e atenuar esse cenário? Você poderá usar o diagnóstico de Standard Load Balancer para aumentar o monitoramento do desempenho de seu serviço de ponta a ponta. Considere o que está disponível e o que pode precisar de aumento para uma imagem completa.

- As zonas podem tornar as falhas mais fáceis compreendidas e contidas.  No entanto, a falha de zona não é diferente de outras falhas quando se trata de conceitos como tempos limite, repetições e algoritmos de retirada. Embora Azure Load Balancer forneça caminhos com redundância de zona e tente recuperar-se rapidamente, em um nível de pacote em tempo real, retransmissãos ou reestabelecimentos podem ocorrer durante o início de uma falha e é importante entender como seu aplicativo lida com sucedi. Seu esquema de balanceamento de carga sobreviverá, mas você precisará planejar o seguinte:
  - Quando uma zona falhar, seu serviço de ponta a ponta entenderá isso e se o estado for perdido, como você recuperará?
  - Quando uma zona retorna, seu aplicativo sabe como convergir com segurança?

### <a name="zonalityguidance"></a>Com redundância de zona versus zonas

>[!IMPORTANT]
>Examine [zonas de disponibilidade](../availability-zones/az-overview.md) para obter tópicos relacionados, incluindo qualquer informação específica da região.

Com redundância de zona, é possível fornecer uma não independente de zona e, ao mesmo tempo, a opção resiliente com um único endereço IP para o serviço.  Isso pode reduzir a complexidade por vez.  Com redundância de zona também tem mobilidade entre zonas e pode ser usado com segurança em recursos em qualquer zona.  Além disso, é uma prova futura em regiões sem Zonas de Disponibilidade, o que pode limitar as alterações necessárias depois que uma região é obtida Zonas de Disponibilidade.  A sintaxe de configuração para um endereço IP com redundância de zona ou front-end é realizada com sucesso em qualquer região, incluindo aqueles sem Zonas de Disponibilidade.

O zonal pode fornecer uma garantia explícita para uma zona, compartilhando destino com a integridade da zona. A associação de um endereço IP zonal ou de front-end Load Balancer de frontend pode ser um atributo desejável ou razoável, especialmente se o recurso anexado for uma VM zonal na mesma zona.  Ou talvez seu aplicativo exija conhecimento explícito sobre em qual zona um recurso está localizado e você deseja ter a razão da disponibilidade em zonas separadas explicitamente.  Você pode optar por expor vários front-ends zonais para um serviço de ponta a ponta distribuído entre as zonas (isto é, os front-ends zonais por região para vários conjuntos de escala de máquina virtual zonal).  E se os front-ends zonais forem endereços IP públicos, você poderá usar esses vários front-ends zonais para expor seu serviço com o [Gerenciador de tráfego](../traffic-manager/traffic-manager-overview.md).  Ou você pode usar vários front-ends zonais para obter informações de desempenho e integridade por zona por meio de soluções de monitoramento de terceiros e expor o serviço geral com um front-end com redundância de zona. Você deve servir apenas recursos zonais com front-ends zonais alinhados à mesma zona e evitar cenários entre zonas potencialmente prejudiciais para recursos zonais.  Os recursos zonais existem somente em regiões em que Zonas de Disponibilidade existem.

Não há nenhuma orientação geral de que uma é uma opção melhor do que a outra sem conhecer a arquitetura do serviço.

## <a name="limitations"></a>Limitações

- Embora o plano de dados seja totalmente redundante pela zona (a menos que a garantia zonal tenha sido especificada), as operações do plano de controle não são totalmente redundantes por zona.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [zonas de disponibilidade](../availability-zones/az-overview.md)
- Saiba mais sobre o [Balanceador de Carga Standard](load-balancer-standard-overview.md)
- Saiba como [balancear a carga de VMs em uma zona usando um Standard Load Balancer com um front-end zonal](load-balancer-standard-public-zonal-cli.md)
- Saiba como [balancear a carga de VMs entre zonas usando um Standard Load Balancer com um front-end com redundância de zona](load-balancer-standard-public-zone-redundant-cli.md)
