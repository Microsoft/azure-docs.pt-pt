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
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 6deb5714a43d61f5ceb793757d49bd099f09f2b7
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977663"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Balanceador de Carga Standard e Zonas de Disponibilidade

O Azure Standard Load Balancer suporta cenários de zonas de disponibilidade. Você pode usar o equilíbrio de carga padrão para aumentar a disponibilidade em todo o seu cenário, alinhando recursos com, e distribuição em áreas. As zonas de disponibilidade em combinação com o equilíbrio de carga padrão são um conjunto de funcionalidades expansivo e flexível que pode criar muitos cenários diferentes.  Reveja este documento para compreender estes [conceitos](#concepts) e [orientação](#design)de design de cenário fundamental.

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a>Conceitos de Zonas de Disponibilidade aplicados ao Balancer de Carga

Um equilibrador de carga herda a configuração da zona dos seus componentes: 

* Front-end
* Regras
* Definição de piscina de backend

No contexto das zonas de disponibilidade, o comportamento e as propriedades de uma regra de equilíbrio de carga são descritos como zona-redundante ou zonal.  No contexto do equilibrador de carga, a zona redundante significa sempre **múltiplas zonas** e zonais significa isolar o serviço para uma **única zona**. Um Balancer de Carga Azure tem dois tipos, públicos e internos. Ambos os tipos de redundância da zona de suporte do equilíbrio de carga e implantação zonal.  Ambos os tipos de equilíbrio de carga podem direcionar o tráfego através das zonas conforme necessário.

## <a name="frontend"></a>Front-end

Um frontend de suporte de carga é uma configuração IP frontal que refere um endereço IP público ou um endereço IP privado dentro da subnet de uma rede virtual. Forma o ponto final equilibrado da carga onde o seu serviço está exposto.

Um recurso de equilíbrio de carga pode conter regras com extremidades dianteiras zonais e zona-redundante simultaneamente. Quando um IP público ou privado é garantido a uma zona, a zona (ou falta dela) não é alterável. Para alterar ou omitir a zonade de um endereço IP público ou privado frontend, recrie o IP na zona apropriada. 

As zonas de disponibilidade não alteram os constrangimentos para várias frentes. Reveja [vários frontends para Load Balancer](load-balancer-multivip-overview.md) para obter detalhes para esta capacidade.

### <a name="zone-redundant"></a>Zona redundante 

Numa região com zonas de disponibilidade, um frontend de equilíbrio de carga padrão pode ser redundante em zona. Várias zonas podem servir de entrada ou saída numa região. Este tráfego é servido por um único endereço IP. Não são necessários esquemas de despedimento do DNS. 

Um único endereço IP frontal sobreviverá à falha da zona. O IP frontal pode ser usado para alcançar todos os membros da piscina (não impactados) não importa a zona. Uma ou mais zonas de disponibilidade podem falhar e o caminho dos dados sobrevive enquanto uma zona da região permanecer saudável. 

O endereço IP do frontend é servido simultaneamente por múltiplas implementações independentes de infraestruturas em múltiplas zonas de disponibilidade. Qualquer reestabelecimento ou reestabelecimento terá sucesso noutras zonas não afetadas pela falha da zona. 

:::image type="content" source="./media/az-zonal/zone-redundant-lb-1.svg" alt-text="Zona redundante" border="true":::

*Figura: Equilibrador de carga redundante da zona*

### <a name="zonal"></a>Zonal

Você pode optar por ter uma frontend garantida a uma única zona, que é conhecida como uma *extremidade frontal zonal*.  Este cenário significa que qualquer fluxo de entrada ou saída é servido por uma única zona numa região.  A sua fachada partilha o destino com a saúde da zona.  A trajetória de dados não é afetada por falhas em zonas diferentes das que foram garantidas. Pode utilizar as extremidades dianteiras zonais para expor um endereço IP por Zona de Disponibilidade.  

Além disso, é suportada a utilização de extremidades dianteiras zonais diretamente para pontos finais equilibrados de carga dentro de cada zona. Pode utilizar esta configuração para expor por pontos finais equilibrados por zona para monitorizar individualmente cada zona. Para pontos finais públicos, pode integrá-los com um produto dNS de equilíbrio de carga como [Traffic Manager](../traffic-manager/traffic-manager-overview.md) e usar um único nome DNS.

:::image type="content" source="./media/az-zonal/zonal-lb-1.svg" alt-text="Zona redundante" border="true":::

Se desejar misturar estes conceitos (zona-redundante e zonal para o mesmo backend), reveja [várias extremidades frontais para O Equilíbrio de Carga Azure](load-balancer-multivip-overview.md).

Para um frontend de equilíbrio de carga pública, adicione um parâmetro de **zonas** ao IP público. Este IP público é referenciado pela configuração IP frontal utilizada pela respetiva regra.

Para uma extremidade frontal do equilíbrio interno de carga, adicione um parâmetro de **zona** à configuração IP frontal do equilíbrio interno. Uma extremidade frontal zonal garante um endereço IP numa sub-rede para uma zona específica.

## <a name="backend"></a>Back-end

O Azure Load Balancer trabalha com instâncias de máquinas virtuais. Estes casos podem ser autónomos, conjuntos de disponibilidade ou conjuntos de escala de máquinas virtuais. Qualquer máquina virtual numa única rede virtual pode fazer parte da piscina de backend, qualquer que seja a zona a que a máquina virtual esteja garantida.

Para alinhar e garantir a sua faceta dianteira e a sua extremidade traseira com uma única zona, coloque apenas máquinas virtuais dentro da mesma zona na piscina correspondente.

Para abordar máquinas virtuais em várias zonas, coloque máquinas virtuais de várias zonas na mesma piscina de backend. 

Quando utilizar conjuntos de escala de máquinavirtual, coloque uma ou mais conjuntos de escala de máquina virtual na mesma piscina de backend. Os conjuntos de escala podem estar em zonas únicas ou múltiplas.

## <a name="outbound-connections"></a>Ligações de saída

A zona-redundante e a zonal aplicam-se às [ligações de saída](load-balancer-outbound-connections.md). Um endereço IP público redundante usado para ligações de saída é servido por todas as zonas. Um endereço IP público zonal é servido apenas pela zona onde está garantido. 

Ligação de saída As dotações por porta SNAT sobrevivem a falhas de zona e o seu cenário continuará a fornecer conectividade SNAT de saída se não for afetado por falha de zona. As falhas de zona podem exigir que as ligações sejam restabelecidas para cenários de zona redundante se o tráfego for servido por uma zona afetada. Os fluxos em outras zonas que não as zonas afetadas não são afetados.

O algoritmo de pré-alocação da porta SNAT é o mesmo com ou sem zonas de disponibilidade.

## <a name="health-probes"></a>Sondas do estado de funcionamento

As definições de sonda de saúde existentes permanecem como estão sem zonas de disponibilidade. No entanto, expandimos o modelo de saúde a nível de infraestruturas. 

Ao utilizar extremidades dianteiras redundantes, o equilibrador de carga expande o seu controlo interno de saúde. O equilibrador de carga sonda independentemente a disponibilidade de uma máquina virtual de cada zona e desliga caminhos através de zonas que falharam sem intervenção.  

Outras zonas que podem alcançar este VM podem continuar a servir o VM a partir das respetivas extremidades dianteiras. Durante os eventos de falha, cada zona pode ter diferentes distribuições de novos fluxos, protegendo ao mesmo tempo a saúde geral do seu serviço.

## <a name="design-considerations"></a><a name="design"></a>Considerações de design

O equilibrador de carga é flexível no contexto das zonas de disponibilidade. Pode optar por alinhar-se em zonas ou ser redundante para cada regra. O aumento da disponibilidade pode chegar ao preço do aumento da complexidade. Design para disponibilidade para um desempenho ideal.

### <a name="automatic-zone-redundancy"></a>Redundância automática da zona

O Balancer load torna simples ter um único IP como uma frente redundante. Um endereço IP redundante pode servir um recurso zonal em qualquer zona.  O IP pode sobreviver a uma ou mais falhas de zona, desde que uma zona permaneça saudável dentro da região.  Em vez disso, uma faceta zonal é uma redução do serviço para uma única zona e partilha o destino com a respetiva zona.

A redundância da zona não implica um plano de dados ou de controlo inútil; é um avião de dados. Os fluxos redundantes em zonas podem usar quaisquer zonas e os fluxos de um cliente usarão todas as zonas saudáveis de uma região. Numa falha de zona, os fluxos de tráfego usando zonas saudáveis não são afetados. 

Os fluxos de tráfego que utilizam uma zona no momento da falha da zona podem ser afetados, mas as aplicações podem recuperar. O tráfego continua nas zonas saudáveis da região após a retransmissão quando Azure convergiu em torno da falha da zona.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a>Limites de zona transversal

É importante entender que sempre que um serviço cruza zonas, você partilha o destino não com uma zona, mas potencialmente várias zonas. Como resultado, o seu serviço pode não ter ganho qualquer disponibilidade sobre implementações não zonais.

Evite introduzir dependências inter-zonas não intencionais, o que anulará os ganhos de disponibilidade ao utilizar zonas de disponibilidade. Se a sua aplicação contiver múltiplos componentes críticos, certifique-se de sobrevivência se uma zona falhar.

Um único componente crítico para a sua aplicação pode afetar toda a sua aplicação se ela existir apenas numa zona diferente da ou das zonas sobreviventes. Considere a restauração da zona e como a sua aplicação irá convergir. Compreenda como a sua aplicação responde a falhas de porções da mesmas. Reveja os pontos-chave e use-os para perguntas enquanto pensa no seu cenário específico.

- Se a sua aplicação tiver dois componentes:

    * Endereço IP
    * Máquina virtual com disco gerido

Estão configurados na zona 1 e na zona 2. Quando a zona 1 falhar, o seu serviço não sobreviverá. Não atravesse zonas com cenários zonais a menos que compreenda completamente que está a criar um modo de falha potencialmente perigoso. Este cenário é permitido proporcionar flexibilidade.

- Se a sua aplicação tiver dois componentes:

    * Endereço IP
    * Máquina virtual com disco gerido

Estão configurados para serem de zona redundante e zona 1. O seu serviço sobreviverá à falha da zona 2, zona 3 ou ambas, a menos que a zona 1 tenha falhado. No entanto, perde alguma capacidade de raciocinar sobre a saúde do seu serviço se tudo o que está a observar é a capacidade de acesso à frontenda.  Considere desenvolver um modelo de saúde e capacidade mais extenso.  Você pode usar conceitos de zona redundante e zonal juntos para expandir a perceção e a gestão.

- Se a sua aplicação tiver dois componentes:

    * Extremidade frontal do equilibrador de carga redundante de zona
    * Escala de máquina virtual de cross-zone definida em três zonas

Os seus recursos em zonas não afetadas por falhas estarão disponíveis. A sua capacidade de serviço pode estar degradada durante a falha. Do ponto de vista da infraestrutura, a sua implantação pode sobreviver a uma ou mais falhas de zona. Este cenário levanta as seguintes questões:

  - Compreende como a sua aplicação responde a falhas e capacidade degradada?
  - Precisa de salvaguardas ao seu serviço para forçar uma falha a um par de regiões, se necessário?
  - Como vai monitorizar, detetar e mitigar tal cenário? Pode utilizar diagnósticos de balanceadores de carga padrão para aumentar a monitorização do desempenho do seu serviço. Considere o que está disponível e o que pode precisar de aumento.

- As zonas podem tornar as falhas mais facilmente compreendidas e contidas. A falha na zona não é diferente de outras falhas quando se trata de timeouts, repetições e algoritmos de recuo. O Azure Load Balancer fornece caminhos redundantes em zonas. O equilibrador de carga tenta recuperar rapidamente, a um nível de pacote em tempo real. Podem ocorrer retransmissões ou reestabelecimentos durante o início de uma avaria. É importante entender como a sua aplicação lida com falhas. O seu esquema de equilíbrio de carga vai sobreviver, mas precisa de planear as seguintes perguntas:

  - Quando uma zona falha, o seu serviço compreende esta falha e se o Estado está perdido, como vai recuperar?
  - Quando uma zona retorna, a sua aplicação entende como convergir com segurança?

Reveja os padrões de design de [nuvem Azure](https://docs.microsoft.com/azure/architecture/patterns/) para melhorar a resiliência da sua aplicação a cenários de falha.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [as Zonas de Disponibilidade](../availability-zones/az-overview.md)
- Saiba mais sobre o [Balanceador de Carga Standard](load-balancer-standard-overview.md)
- Aprenda a [carregar VMs de equilíbrio dentro de uma zona usando um Balancer de Carga Padrão com uma extremidade frontal zonal](load-balancer-standard-public-zonal-cli.md)
- Aprenda a [carregar VMs de equilíbrio em zonas usando um Balancer de Carga Padrão com uma extremidade frontal redundante de zona](load-balancer-standard-public-zone-redundant-cli.md)
- Conheça os padrões de design de [nuvem Azure](https://docs.microsoft.com/azure/architecture/patterns/) para melhorar a resiliência da sua aplicação a cenários de falha.
