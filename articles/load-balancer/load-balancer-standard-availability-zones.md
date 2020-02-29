---
title: Equilíbrio de carga padrão Azure e zonas de disponibilidade
titleSuffix: Azure Load Balancer
description: Com este percurso de aprendizagem, inicie-se com o Azure Standard Load Balancer e as Zonas de Disponibilidade.
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
ms.openlocfilehash: 5a65982c5c13eb4e4273efcfd8d14910b0f35572
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197152"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Balanceador de Carga Standard e Zonas de Disponibilidade

O Azure Standard Load Balancer suporta [cenários de zonas](../availability-zones/az-overview.md) de disponibilidade. Pode utilizar o Standard Load Balancer para otimizar a disponibilidade no seu cenário de ponta a ponta, alinhando recursos com zonas e distribuindo-os por zonas.  Reveja [as zonas de disponibilidade](../availability-zones/az-overview.md) para orientação sobre quais são as zonas de disponibilidade, quais as regiões que atualmente suportam zonas de disponibilidade, e outros conceitos e produtos relacionados. As zonas de disponibilidade em combinação com o Standard Load Balancer é um conjunto de funcionalidades expansivo e flexível que pode criar muitos cenários diferentes.  Reveja este documento para compreender estes [conceitos](#concepts) e [orientação](#design)de design de cenário fundamental.

## <a name="concepts"></a>Conceitos de Zonas de Disponibilidade aplicados ao Balancer de Carga

Um recurso load Balancer em si é regional e nunca zonal. A granularidade do que pode configurar é limitada por cada configuração de definição frontal, regra e reserva.
No contexto das zonas de disponibilidade, o comportamento e as propriedades de uma regra do Balancer de Carga são descritos como zona-redundante ou zonal.  Zona-redundante e zonal descrevem a zonade de uma propriedade.  No contexto do Balancer load, zona-redundante significa sempre *múltiplas zonas* e zonais significa isolar o serviço para uma *única zona*.
Tanto cenários públicos e internos de suporte de balanceadores de carga são zonas redundantes e zonais e ambos podem direcionar o tráfego através das zonas conforme necessário *(equilíbrio de carga*transversal). 

### <a name="frontend"></a>Frontend

Um frontend do Balancer de carga é uma configuração IP frontal que refere um recurso de endereço IP público ou um endereço IP privado dentro da subnet de um recurso de rede virtual.  Forma o ponto final equilibrado da carga onde o seu serviço está exposto.
Um recurso load Balancer pode conter regras com extremidades dianteiras zonais e zona-redundante simultaneamente. Quando um recurso IP público ou um endereço IP privado foi garantido para uma zona, a zona (ou falta dela) não é mutável.  Se desejar alterar ou omitir a zonade de um ip público ou endereço IP privado frontend, você precisa recriar o IP público na zona apropriada.  As zonas de disponibilidade não alteram os constrangimentos para várias extremidades dianteiras, reveja [várias extremidades frontais para](load-balancer-multivip-overview.md) o Balancer de Carga para obter detalhes para esta capacidade.

#### <a name="zone-redundant"></a>Zona redundante 

Numa região com zonas de disponibilidade, um frontend de balanceadores de carga padrão pode ser redundante em zona.  A zona redundante significa que todos os fluxos de entrada ou saída são servidos por múltiplas zonas de disponibilidade numa região simultaneamente utilizando um único endereço IP. Não são necessários esquemas de despedimento do DNS. Um único endereço IP frontal pode sobreviver à falha da zona e pode ser usado para alcançar todos os membros da piscina (não impactados) independentemente da zona. Uma ou mais zonas de disponibilidade podem falhar e o caminho dos dados sobrevive enquanto uma zona da região permanecer saudável. O único endereço IP do frontend é servido simultaneamente por múltiplas implementações independentes de infraestruturas em múltiplas zonas de disponibilidade.  Isto não significa uma trajetória de dados sem sucesso, mas qualquer tentativa ou reestabelecimento terá sucesso noutras zonas não afetadas pela falha da zona.   

#### <a name="optional-zone-isolation"></a>Isolamento de zona opcional

Você pode optar por ter uma frontend garantida a uma única zona, que é conhecida como uma *extremidade frontal zonal*.  Isto significa que qualquer fluxo de entrada ou saída é servido por uma única zona numa região.  A sua fachada partilha o destino com a saúde da zona.  A trajetória de dados não é afetada por falhas em zonas diferentes das que foram garantidas. Pode utilizar as extremidades dianteiras zonais para expor um endereço IP por Zona de Disponibilidade.  

Além disso, pode consumir extremidades dianteiras zonais diretamente para pontos finais equilibrados de carga dentro de cada zona. Também pode usá-lo para expor por zona pontos finais equilibrados para monitorizar individualmente cada zona.  Ou para pontos finais públicos pode integrá-los com um produto dNS de equilíbrio de carga como [Traffic Manager](../traffic-manager/traffic-manager-overview.md) e usar um único nome DNS. Em seguida, o cliente irá então resolver este nome DNS para vários endereços IP zonais.  

Se desejar misturar estes conceitos (zona-redundante e zonal para o mesmo backend), reveja [várias extremidades frontais para O Equilíbrio de Carga Azure](load-balancer-multivip-overview.md).

Para uma extremidade frontal do Balanceor de Carga pública, adicione um parâmetro de *zonas* ao recurso IP público referenciado pela configuração IP frontal utilizada pela respetiva regra.

Para uma extremidade frontal interna do balanceor de carga, adicione um parâmetro de *zonas* à configuração IP frontal do Balanceor de Carga interna. A extremidade frontal zonal faz com que o Balancer de carga garanta um endereço IP numa subrede a uma zona específica.

### <a name="cross-zone-load-balancing"></a>Cross-zone Load-Balanceing

O equilíbrio de carga transversal é a capacidade do Balancer de Carga atingir um ponto final em qualquer zona e é independente da frontend e da sua zonalidade.  Qualquer regra de equilíbrio de carga pode visar a instância de backend em qualquer zona de disponibilidade ou instâncias regionais.

Você precisa ter cuidado para construir o seu cenário de uma forma que expresse uma noção de zonas de disponibilidade. Por exemplo, você precisa garantir a sua implantação de máquina virtual dentro de uma única zona ou múltiplas zonas, e alinhar os recursos de frente zonal e zonal para a mesma zona.  Se cruzar zonas de disponibilidade apenas com recursos zonais, o cenário funcionará, mas poderá não ter um modo de falha claro no que diz respeito às zonas de disponibilidade. 

### <a name="backend"></a>Backend

O Balancer de Carga funciona com casos de máquinas virtuais.  Estes podem ser autónomos, conjuntos de disponibilidade ou conjuntos de escala de máquina virtual.  Qualquer caso de máquina virtual numa única rede virtual pode fazer parte da piscina de backend, independentemente de ter ou não sido garantida a uma zona ou qual a zona que estava garantida.

Se desejar alinhar e garantir a sua facefrontal e a sua extremidade traseira com uma única zona, coloque apenas máquinas virtuais dentro da mesma zona na respetiva piscina de backend.

Se desejar dirigir máquinas virtuais em várias zonas, basta colocar máquinas virtuais de várias zonas na mesma piscina de backend.  Ao utilizar conjuntos de escala de máquinavirtual, pode colocar uma ou mais conjuntos de escala de máquina virtual na mesma piscina de backend.  E cada um destes conjuntos de escala de máquinas virtuais pode estar em uma única ou múltiplas zonas.

### <a name="outbound-connections"></a>Ligações de saída

As mesmas propriedades zona-redundantes e zonais aplicam-se às [ligações de saída](load-balancer-outbound-connections.md).  Um endereço IP público redundante usado para ligações de saída é servido por todas as zonas. Um endereço IP público zonal é servido apenas pela zona onde está garantido.  Ligação de saída As dotações por porta SNAT sobrevivem a falhas de zona e o seu cenário continuará a fornecer conectividade SNAT de saída se não for afetada por falha de zona.  Isto pode exigir transmissões ou restabelecer ligações para cenários de zona redundante se um fluxo for servido por uma zona afetada.  Os fluxos em zonas diferentes das zonas afetadas não são afetados.

O algoritmo de pré-alocação da porta SNAT é o mesmo com ou sem zonas de disponibilidade.

### <a name="health-probes"></a>Sondas do estado de funcionamento

As definições de sonda de saúde existentes permanecem como estão sem zonas de disponibilidade.  No entanto, expandimos o modelo de saúde a nível de infraestruturas. 

Ao utilizar frontendas redundantes de zona, a Load Balancer expande o seu modelo de saúde interna para sondar de forma independente a capacidade de uma máquina virtual de cada zona de disponibilidade e desligar caminhos através de zonas que podem ter falhado sem a intervenção do cliente.  Se não estiver disponível um determinado caminho desde a infraestrutura do Balancer de carga de uma zona a uma máquina virtual noutra zona, o Balancer de Carga pode detetar e evitar esta falha. Outras zonas que podem alcançar este VM podem continuar a servir o VM a partir das respetivas extremidades dianteiras.  Como resultado, é possível que durante os eventos de falha, cada zona possa ter distribuições ligeiramente diferentes de novos fluxos, protegendo ao mesmo tempo a saúde geral do seu serviço de ponta a ponta.

## <a name="design"></a>Considerações de design

O Load Balancer é propositadamente flexível no contexto das zonas de disponibilidade. Pode optar por alinhar-se em zonas ou pode optar por ser redundante por cada regra.  O aumento da disponibilidade pode chegar ao preço de uma maior complexidade e você deve projetar para disponibilidade para um desempenho ideal.  Vamos dar uma olhada em algumas considerações importantes de design.

### <a name="automatic-zone-redundancy"></a>Redundância automática da zona

O Balancer load torna simples ter um único IP como uma frente redundante. Um endereço IP redundante pode servir com segurança um recurso zonal em qualquer zona e pode sobreviver a uma ou mais falhas de zona, desde que uma zona permaneça saudável dentro da região. Inversamente, uma faceta zonal é uma redução do serviço para uma única zona e partilha o destino com a respetiva zona.

A redundância da zona não implica uma trajetória de dados inacerta ou um plano de controlo;  é expressamente plano de dados. Os fluxos redundantes em zonas podem usar quaisquer zonas e os fluxos de um cliente usarão todas as zonas saudáveis de uma região. Em caso de falha de zona, os fluxos de tráfego utilizando zonas saudáveis nessa altura não são afetados.  Os fluxos de tráfego que utilizam uma zona no momento da falha da zona podem ser afetados, mas as aplicações podem recuperar. Estes fluxos podem continuar nas restantes zonas saudáveis dentro da região após retransmissão ou reestabelecimento, uma vez que Azure convergiu em torno da falha da zona.

### <a name="xzonedesign"></a>Limites de zona transversal

É importante entender que sempre que um serviço de ponta a ponta cruza zonas, você partilha o destino não com uma zona, mas potencialmente várias zonas.  Como resultado, o seu serviço de ponta a ponta pode não ter ganho qualquer disponibilidade sobre implementações não zonais.

Evite introduzir dependências inter-zonas não intencionais, o que anulará os ganhos de disponibilidade ao utilizar zonas de disponibilidade.  Quando a sua aplicação é composta por múltiplos componentes e deseja ser resiliente à falha da zona, deve ter o cuidado de garantir a sobrevivência de componentes críticos suficientes em caso de falha de uma zona.  Por exemplo, um único componente crítico para a sua aplicação pode afetar toda a sua aplicação se ela existir apenas numa zona diferente da ou das zonas sobreviventes.  Além disso, considere também a restauração da zona e como a sua aplicação irá convergir. Você precisa entender como a sua aplicação razões no que diz respeito a falhas de porções da mesmas. Vamos rever alguns pontos-chave e usá-los como inspiração para perguntas como você pensa através do seu cenário específico.

- Se a sua aplicação tiver dois componentes como um endereço IP e uma máquina virtual com disco gerido, e eles estão garantidos na zona 1 e na zona 2, quando a zona 1 falhar o seu serviço de ponta a ponta não sobreviverá quando a zona 1 falhar.  Não atravesse zonas com cenários zonais a menos que compreenda completamente que está a criar um modo de falha potencialmente perigoso.  Este cenário é permitido proporcionar flexibilidade.

- Se a sua aplicação tiver dois componentes como um endereço IP e uma máquina virtual com disco gerido, e é garantido que são redundantes em zona e zona 1, respectivamente, o seu serviço de ponta a ponta sobreviverá à falha da zona 2, zona 3 ou ambas, a menos que a zona 1 tenha falhado.  No entanto, perde alguma capacidade de raciocinar sobre a saúde do seu serviço se tudo o que está a observar é a capacidade de acesso à frontenda.  Considere desenvolver um modelo de saúde e capacidade mais extenso.  Você pode usar conceitos de zona redundante e zonal juntos para expandir a perceção e a gestão.

- Se a sua aplicação tiver dois componentes como um frontend de balancedor de carga redundante e uma escala de máquina virtual de zona transversal definida em três zonas, os seus recursos em zonas não afetadas por falhas estarão disponíveis, mas a sua capacidade de serviço de ponta a ponta pode ser degradada durante a falha da zona. Do ponto de vista da infraestrutura, a sua implantação pode sobreviver a uma ou mais falhas de zona, o que levanta as seguintes questões:
  - Compreende como razões de candidatura sobre tais falhas e capacidade degradada?
  - Precisa de salvaguardas ao seu serviço para forçar uma falha a um par de regiões, se necessário?
  - Como vai monitorizar, detetar e mitigar tal cenário? Poderá utilizar diagnósticos Standard Load Balancer para aumentar a monitorização do seu desempenho de serviço de ponta a ponta. Considere o que está disponível e o que pode precisar de aumento para uma imagem completa.

- As zonas podem tornar as falhas mais facilmente compreendidas e contidas.  No entanto, a falha na zona não é diferente de outras falhas quando se trata de conceitos como timeouts, repetições e algoritmos de backoff. Embora o Azure Load Balancer forneça caminhos redundantes em zonas e tente recuperar rapidamente, a um nível de pacote em tempo real, retransmissões ou reestabelecimentos podem ocorrer durante o início de uma falha e é importante entender como a sua aplicação lida com falhas. O teu esquema de equilíbrio de carga vai sobreviver, mas tens de planear o seguinte:
  - Quando uma zona falha, o seu serviço de ponta a ponta entende isto e se o Estado está perdido, como vai se recuperar?
  - Quando uma zona retorna, a sua aplicação entende como convergir com segurança?

Reveja os padrões de design de [nuvem Azure](https://docs.microsoft.com/azure/architecture/patterns/) para melhorar a resiliência da sua aplicação a cenários de falha.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [as Zonas de Disponibilidade](../availability-zones/az-overview.md)
- Saiba mais sobre o [Balanceador de Carga Standard](load-balancer-standard-overview.md)
- Aprenda a [carregar VMs de equilíbrio dentro de uma zona usando um Balancer de Carga Padrão com uma extremidade frontal zonal](load-balancer-standard-public-zonal-cli.md)
- Aprenda a [carregar VMs de equilíbrio em zonas usando um Balancer de Carga Padrão com uma extremidade frontal redundante de zona](load-balancer-standard-public-zone-redundant-cli.md)
- Conheça os padrões de design de [nuvem Azure](https://docs.microsoft.com/azure/architecture/patterns/) para melhorar a resiliência da sua aplicação a cenários de falha.
