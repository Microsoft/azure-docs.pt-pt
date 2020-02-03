---
title: Standard Load Balancer e Zonas de Disponibilidade do Azure
titleSuffix: Azure Load Balancer
description: Com este roteiro de aprendizagem, comece a usar o Azure Standard Load Balancer e Zonas de Disponibilidade.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 0d61ad33b97b97c3a45334704544d72809e56848
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715267"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Balanceador de Carga Standard e Zonas de Disponibilidade

O Azure Standard Load Balancer suporta [cenários de zonas](../availability-zones/az-overview.md) de disponibilidade. Você pode usar Standard Load Balancer para otimizar a disponibilidade em seu cenário de ponta a ponta alinhando recursos com zonas e distribuindo-os entre zonas.  Reveja [as zonas de disponibilidade](../availability-zones/az-overview.md) para orientação sobre quais são as zonas de disponibilidade, quais as regiões que atualmente suportam zonas de disponibilidade, e outros conceitos e produtos relacionados. As zonas de disponibilidade em combinação com o Standard Load Balancer é um conjunto de recursos extenso e flexível que pode criar vários cenários diferentes.  Reveja este documento para compreender estes [conceitos](#concepts) e [orientação](#design)de design de cenário fundamental.

>[!IMPORTANT]
>Reveja [as Zonas de Disponibilidade](../availability-zones/az-overview.md) para tópicos relacionados, incluindo qualquer informação específica da região.

## <a name="concepts"></a>Conceitos de Zonas de Disponibilidade aplicados ao Balancer de Carga

Não há nenhuma relação direta entre Load Balancer recursos e a infraestrutura real; a criação de um Load Balancer não cria uma instância. Load Balancer recursos são objetos nos quais você pode expressar como o Azure deve programar sua infraestrutura de multilocatário predefinida para obter o cenário que você deseja criar.  Isso é significativo no contexto de zonas de disponibilidade porque um único recurso de Load Balancer pode controlar a programação de infraestrutura em várias zonas de disponibilidade, enquanto um serviço com redundância de zona aparece como um recurso do ponto de vista do cliente.  

Um recurso Load Balancer em si é regional e nunca zonal.  E uma VNet e uma sub-rede são sempre regionais e nunca são zonais. A granularidade do que você pode configurar é restrita por cada configuração de front-end, regra e definição de pool de back-end.

No contexto de zonas de disponibilidade, o comportamento e as propriedades de uma regra de Load Balancer são descritos como com redundância de zona ou zonas.  Com redundância de zona e zonas, descrevem o zonalidade de uma propriedade.  No contexto do Balancer load, zona-redundante significa sempre *múltiplas zonas* e zonais significa isolar o serviço para uma *única zona*.

Tanto cenários públicos e internos de suporte de balanceadores de carga são zonas redundantes e zonais e ambos podem direcionar o tráfego através das zonas conforme necessário *(equilíbrio de carga*transversal). 

### <a name="frontend"></a>Frontend

Um front-end Load Balancer é uma configuração de IP de front-end referenciando um recurso de endereço IP público ou um endereço IP privado dentro da sub-rede de um recurso de rede virtual.  Ele forma o ponto de extremidade com balanceamento de carga onde o serviço é exposto.

Um recurso de Load Balancer pode conter regras com front-ends zonais e com redundância de zonas simultaneamente. 

Quando um recurso IP público ou um endereço IP privado tiver sido garantido para uma zona, o zonalidade (ou a falta dele) não é mutável.  Se você quiser alterar ou omitir o zonalidade de um IP público ou front-end de endereço IP privado, será necessário recriar o IP público na zona apropriada.  As zonas de disponibilidade não alteram os constrangimentos para várias extremidades dianteiras, reveja [várias extremidades frontais para](load-balancer-multivip-overview.md) o Balancer de Carga para obter detalhes para esta capacidade.

#### <a name="zone-redundant-by-default"></a>Com redundância de zona por padrão

Em uma região com zonas de disponibilidade, um Standard Load Balancer front-end é com redundância de zona por padrão.  Com redundância de zona significa que todos os fluxos de entrada ou saída são servidos por várias zonas de disponibilidade em uma região simultaneamente usando um único endereço IP. Os esquemas de redundância de DNS não são necessários. Um único endereço IP de front-end pode sobreviver à falha de zona e pode ser usado para atingir todos os membros (não afetados) do pool de back-end independentemente da zona. Uma ou mais zonas de disponibilidade podem falhar e o caminho de dados sobreviver, desde que uma zona na região permaneça íntegra. O endereço IP único de front-end é servido simultaneamente por várias implantações de infraestrutura independentes em várias zonas de disponibilidade.  Isso não significa implica de dados de caminho, mas qualquer nova tentativa ou reestabelecimento terá êxito em outras zonas não afetadas pela falha de zona.   

O trecho a seguir é uma ilustração de como definir um endereço IP público com redundância de zona para usar com seu Standard Load Balancer público. Se estiver a utilizar os modelos existentes do Gestor de Recursos na sua configuração, adicione a secção **sku** a estes modelos.

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

O trecho a seguir é uma ilustração de como definir um endereço IP de front-end com redundância de zona para seu Standard Load Balancer interno. Se estiver a utilizar os modelos existentes do Gestor de Recursos na sua configuração, adicione a secção **sku** a estes modelos.

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

Os trechos anteriores não são modelos completos, mas pretendem mostrar como expressar as propriedades das zonas de disponibilidade.  Você precisa incorporar essas instruções em seus modelos.

#### <a name="optional-zone-isolation"></a>Isolamento de zona opcional

Você pode optar por ter uma frontend garantida a uma única zona, que é conhecida como uma *extremidade frontal zonal*.  Isso significa que qualquer fluxo de entrada ou saída é servido por uma única zona em uma região.  Seu front-end compartilha destino com a integridade da zona.  O caminho de dados não é afetado por falhas em zonas diferentes de onde foi garantido. Você pode usar front-ends zonais para expor um endereço IP por zona de disponibilidade.  

Além disso, você pode consumir front-ends zonais diretamente para pontos de extremidade com balanceamento de carga em cada zona. Você também pode usar isso para expor pontos de extremidade com balanceamento de carga por zona para monitorar individualmente cada zona.  Ou para pontos finais públicos pode integrá-los com um produto dNS de equilíbrio de carga como [Traffic Manager](../traffic-manager/traffic-manager-overview.md) e usar um único nome DNS. Em seguida, o cliente então será resolvido para esse nome DNS para vários endereços IP zonais.  

Se desejar misturar estes conceitos (zona-redundante e zonal para o mesmo backend), reveja [várias extremidades frontais para O Equilíbrio de Carga Azure](load-balancer-multivip-overview.md).

Para uma extremidade frontal do Balanceor de Carga pública, adicione um parâmetro de *zonas* ao recurso IP público referenciado pela configuração IP frontal utilizada pela respetiva regra.

Para uma extremidade frontal interna do balanceor de carga, adicione um parâmetro de *zonas* à configuração IP frontal do Balanceor de Carga interna. O front-end zonal faz com que a Load Balancer garanta um endereço IP em uma sub-rede para uma zona específica.

O trecho a seguir é uma ilustração de como definir um endereço IP público padrão de zona em Zona 1 de disponibilidade. Se estiver a utilizar os modelos existentes do Gestor de Recursos na sua configuração, adicione a secção **sku** a estes modelos.

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

O trecho a seguir é uma ilustração de como definir um front-end de Standard Load Balancer interno no Zona 1 de disponibilidade. Se estiver a utilizar os modelos existentes do Gestor de Recursos na sua configuração, adicione a secção **sku** a estes modelos. Além disso, defina a propriedade das **zonas** na configuração IP frontal para o recurso infantil.

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

Os trechos anteriores não são modelos completos, mas pretendem mostrar como expressar as propriedades das zonas de disponibilidade.  Você precisa incorporar essas instruções em seus modelos.

### <a name="cross-zone-load-balancing"></a>Balanceamento de carga entre zonas

O balanceamento de carga entre zonas é a capacidade de Load Balancer alcançar um ponto de extremidade de back-end em qualquer zona e é independente do front-end e de seu zonalidade.  Qualquer regra de balanceamento de carga pode direcionar a instância de back-end em qualquer zona de disponibilidade ou instâncias regionais.

Você precisa tomar cuidado para construir seu cenário de uma maneira que expressa uma noção de zonas de disponibilidade. Por exemplo, você precisa garantir a implantação de sua máquina virtual em uma única zona ou em várias zonas e alinhar os recursos de back-end zonal e frontend zonal para a mesma região.  Se você cruzar zonas de disponibilidade com apenas recursos de zona, o cenário funcionará, mas talvez não tenha um modo de falha clara em relação às zonas de disponibilidade. 

### <a name="backend"></a>Backend

Load Balancer funciona com instâncias de máquinas virtuais.  Eles podem ser autônomos, conjuntos de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais.  Qualquer instância de máquina virtual em uma única rede virtual pode fazer parte do pool de back-end, independentemente de ser ou não garantida a uma zona ou a qual zona foi garantida.

Se você quiser alinhar e garantir o frontend e o back-end com uma única zona, só coloque as máquinas virtuais na mesma zona no respectivo pool de back-end.

Se você quiser endereçar máquinas virtuais entre várias zonas, simplesmente coloque as máquinas virtuais de várias zonas no mesmo pool de back-end.  Ao usar conjuntos de dimensionamento de máquinas virtuais, você pode posicionar um ou mais conjuntos de dimensionamento de máquinas virtuais no mesmo pool de back-end.  E cada um desses conjuntos de dimensionamento de máquinas virtuais pode estar em uma única ou várias zonas.

### <a name="outbound-connections"></a>Ligações de saída

As mesmas propriedades zona-redundantes e zonais aplicam-se às [ligações de saída](load-balancer-outbound-connections.md).  Um endereço IP público com redundância de zona usado para conexões de saída é servido por todas as zonas. Um endereço IP público zonal é servido somente pela zona em que ele está garantido.  As alocações de porta SNAT de conexão de saída sobrevivem a falhas de zona e seu cenário continuará a fornecer conectividade SNAT de saída se não for afetado por falha de zona.  Isso pode exigir transmissões ou para que as conexões sejam restabelecidas para cenários com redundância de zona se um fluxo foi servido por uma zona afetada.  Os fluxos em zonas diferentes das zonas afetadas não são afetados.

O algoritmo de prealocação de porta SNAT é o mesmo com ou sem zonas de disponibilidade.

### <a name="health-probes"></a>Sondas do estado de funcionamento

Suas definições de investigação de integridade existentes permanecem como estão sem zonas de disponibilidade.  No entanto, expandimos o modelo de integridade em um nível de infraestrutura. 

Ao usar front-ends com redundância de zona, o Load Balancer expande seu modelo de integridade interno para investigar de forma independente a acessibilidade de uma máquina virtual de cada zona de disponibilidade e desligar os caminhos entre zonas que podem ter falhado sem intervenção do cliente.  Se um determinado caminho não estiver disponível na infraestrutura de Load Balancer de uma zona para uma máquina virtual em outra zona, Load Balancer poderá detectar e evitar essa falha. Outras zonas que podem acessar essa VM podem continuar a atender a VM de seus respectivos front-ends.  Como resultado, é possível que, durante os eventos de falha, cada zona possa ter distribuições ligeiramente diferentes dos novos fluxos, ao mesmo tempo em que protege a integridade geral de seu serviço de ponta a ponta.

## <a name="design"></a>Considerações de design

A Load Balancer é intencionalmente flexível no contexto de zonas de disponibilidade. Você pode optar por se alinhar às zonas ou pode optar por ser com redundância de zona para cada regra.  A maior disponibilidade pode chegar ao preço de maior complexidade e você deve projetar para disponibilidade para um desempenho ideal.  Vamos dar uma olhada em algumas considerações de design importantes.

### <a name="automatic-zone-redundancy"></a>Redundância de zona automática

Load Balancer torna simples ter um único IP como um front-end com redundância de zona. Um endereço IP com redundância de zona pode atender com segurança a um recurso zonal em qualquer zona e pode sobreviver a uma ou mais falhas de zona, contanto que uma zona permaneça íntegra na região. Por outro lado, um front-end zonal é uma redução do serviço para uma única zona e compartilha destino com a respectiva zona.

A redundância de zona não implica em implica ou plano de controle de caminho;  é um plano de dados expressamente. Os fluxos com redundância de zona podem usar qualquer zona e os fluxos de um cliente usarão todas as zonas íntegras em uma região. No caso de falha de zona, os fluxos de tráfego usando zonas de integridade nesse ponto no tempo não são afetados.  Os fluxos de tráfego usando uma zona no momento da falha de zona podem ser afetados, mas os aplicativos podem ser recuperados. Esses fluxos podem continuar nas zonas de integridade restantes dentro da região após a retransmissão ou o restabelecimento, uma vez que o Azure convergiu em redor da falha de zona.

### <a name="xzonedesign"></a>Limites de zona transversal

É importante entender que sempre que um serviço de ponta a ponta cruza zonas, você compartilha destino com não uma zona, mas potencialmente várias zonas.  Como resultado, seu serviço de ponta a ponta pode não ter obtido nenhuma disponibilidade em implantações não zonais.

Evite introduzir dependências entre zonas não pretendidas, o que anulará os ganhos de disponibilidade ao usar zonas de disponibilidade.  Quando seu aplicativo consiste em vários componentes e você deseja ser resiliente a falhas de zona, você deve tomar cuidado para garantir a sobrevivência de componentes críticos suficientes no caso de uma falha de zona.  Por exemplo, um único componente crítico para seu aplicativo pode afetar o aplicativo inteiro se ele existir somente em uma zona que não seja a zona (s) sobreviventes.  Além disso, considere também a restauração de zona e como seu aplicativo será convergido. Você precisa entender como os seus aplicativos têm relação a falhas de partes dele. Vamos examinar alguns pontos importantes e usá-los como inspiração para perguntas, conforme você imagina pelo cenário específico.

- Se seu aplicativo tiver dois componentes, como um endereço IP e uma máquina virtual com disco gerenciado, e eles estiverem garantidos na zona 1 e zona 2, quando a zona 1 falhar, o serviço de ponta a ponta não permanecerá quando a zona 1 falhar.  Não entre zonas com cenários de zona, a menos que você entenda totalmente que está criando um modo de falha potencialmente nocivo.  Esse cenário tem permissão para fornecer flexibilidade.

- Se seu aplicativo tiver dois componentes, como um endereço IP e uma máquina virtual com disco gerenciado, e for garantido que estejam com redundância de zona e a zona 1, respectivamente, o serviço de ponta a ponta sobreviver a falhas de zona da zona 2, zona 3 ou ambas, a menos que a zona 1 tenha falhado.  No entanto, você perde alguma capacidade de motivo da integridade do seu serviço se tudo o que você está observando é a acessibilidade do front-end.  Considere desenvolver um modelo de integridade e capacidade mais extensivo.  Você pode usar os conceitos com redundância de zona e zonas para expandir a percepção e a capacidade de gerenciamento.

- Se seu aplicativo tiver dois componentes, como um front-end de Load Balancer com redundância de zona e um conjunto de dimensionamento de máquinas virtuais entre zonas em três zonas, seus recursos em zonas não impactados por falha estarão disponíveis, mas sua capacidade de serviço de ponta a ponta pode estar degradada durante a falha de zona. Do ponto de vista da infra-estrutura, sua implantação pode sobreviver a uma ou mais falhas de zona, e isso gera as seguintes perguntas:
  - Você entende como o seu aplicativo tem a ver com falhas e capacidade degradada?
  - Você precisa ter proteções em seu serviço para forçar um failover para um par de regiões, se necessário?
  - Como você vai monitorar, detectar e atenuar esse cenário? Você poderá usar o diagnóstico de Standard Load Balancer para aumentar o monitoramento do desempenho de seu serviço de ponta a ponta. Considere o que está disponível e o que pode precisar de aumento para uma imagem completa.

- As zonas podem tornar as falhas mais fáceis compreendidas e contidas.  No entanto, a falha de zona não é diferente de outras falhas quando se trata de conceitos como tempos limite, repetições e algoritmos de retirada. Embora Azure Load Balancer forneça caminhos com redundância de zona e tente recuperar-se rapidamente, em um nível de pacote em tempo real, retransmissãos ou reestabelecimentos podem ocorrer durante o início de uma falha e é importante entender como seu aplicativo lida com sucedi. Seu esquema de balanceamento de carga sobreviverá, mas você precisará planejar o seguinte:
  - Quando uma zona falhar, seu serviço de ponta a ponta entenderá isso e se o estado for perdido, como você recuperará?
  - Quando uma zona retorna, seu aplicativo sabe como convergir com segurança?

Reveja os padrões de design de [nuvem Azure](https://docs.microsoft.com/azure/architecture/patterns/) para melhorar a resiliência da sua aplicação a cenários de falha.

### <a name="zonalityguidance"></a>Zona redundante versus zonal

Com redundância de zona, é possível fornecer uma simplicidade com uma opção independente de zona e, ao mesmo tempo, a opção resiliente com um único endereço IP para o serviço.  Isso pode reduzir a complexidade por vez.  Com redundância de zona também tem mobilidade entre zonas e pode ser usado com segurança em recursos em qualquer zona.  Além disso, é uma prova futura em regiões sem zonas de disponibilidade, o que pode limitar as alterações necessárias quando uma região tiver zonas de disponibilidade.  A sintaxe de configuração para um endereço IP com redundância de zona ou front-end é realizada com sucesso em qualquer região, incluindo aquelas sem zonas de disponibilidade: uma zona não é especificada dentro da propriedade zonas: do recurso.

As zonas podem fornecer uma garantia explícita para uma zona, compartilhando explicitamente destino com a integridade da zona. A criação de uma regra de Load Balancer com um endereço IP de zona frontend ou um front-end Load Balancer interno de frontend pode ser desejável especialmente se o recurso anexado for uma máquina virtual zonal na mesma zona.  Ou talvez seu aplicativo exija conhecimento explícito sobre a zona em que um recurso está localizado antecipadamente e você deseja ponderar a disponibilidade em zonas separadas explicitamente.  Você pode optar por expor vários front-ends zonais para um serviço de ponta a ponta distribuído entre as zonas (isto é, os front-ends zonais por região para vários conjuntos de escala de máquina virtual zonal).  E se as suas extremidades frontais zonais forem endereços IP públicos, pode utilizar estas múltiplas extremidades frontais zonais para expor o seu serviço com [o Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Ou você pode usar vários front-ends zonais para obter informações de desempenho e integridade por zona por meio de soluções de monitoramento de terceiros e expor o serviço geral com um front-end com redundância de zona. Você deve servir apenas recursos zonais com front-ends zonais alinhados à mesma zona e evitar cenários entre zonas potencialmente prejudiciais para recursos zonais.  Os recursos zonais existem somente em regiões em que existem zonas de disponibilidade.

Não há nenhuma orientação geral de que uma é uma opção melhor do que a outra sem conhecer a arquitetura do serviço.  Reveja os padrões de design de [nuvem Azure](https://docs.microsoft.com/azure/architecture/patterns/) para melhorar a resiliência da sua aplicação a cenários de falha.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [as Zonas de Disponibilidade](../availability-zones/az-overview.md)
- Saiba mais sobre o [Balanceador de Carga Standard](load-balancer-standard-overview.md)
- Aprenda a [carregar VMs de equilíbrio dentro de uma zona usando um Balancer de Carga Padrão com uma extremidade frontal zonal](load-balancer-standard-public-zonal-cli.md)
- Aprenda a [carregar VMs de equilíbrio em zonas usando um Balancer de Carga Padrão com uma extremidade frontal redundante de zona](load-balancer-standard-public-zone-redundant-cli.md)
- Conheça os padrões de design de [nuvem Azure](https://docs.microsoft.com/azure/architecture/patterns/) para melhorar a resiliência da sua aplicação a cenários de falha.
