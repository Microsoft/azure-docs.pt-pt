---
title: Zonas de balançador de carga padrão Azure e zonas de disponibilidade
titleSuffix: Azure Load Balancer
description: Com este caminho de aprendizagem, começa com o Azure Standard Load Balancer e as Zonas de Disponibilidade.
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
ms.openlocfilehash: 541aa7da3e804931c1793e455bcbfca83c809dae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89669177"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Balanceador de Carga Standard e Zonas de Disponibilidade

O Azure Standard Load Balancer suporta cenários de zonas de disponibilidade. Pode utilizar o balanceador de carga padrão para aumentar a disponibilidade em todo o seu cenário, alinhando recursos e distribuindo por zonas. As zonas de disponibilidade em combinação com o balanceador de carga padrão são um conjunto de funcionalidades expansivas e flexíveis que podem criar muitos cenários diferentes.  Reveja este documento para compreender estes [conceitos](#concepts) e [orientação](#design)de design de cenários fundamentais.

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a> Conceitos de Zonas de Disponibilidade aplicados ao Balanceador de Carga

Um equilibrador de carga herda a configuração da zona a partir dos seus componentes: 

* Front-end
* Regras
* Definição de piscina de backend

No contexto das zonas de disponibilidade, o comportamento e as propriedades de uma regra do balançador de carga são descritos como zona redundante ou zonal.  No contexto do equilibrador de carga, zona redundante significa sempre **várias zonas** e meios zonais que isolam o serviço para uma **única zona.** Um Equilibrador de Carga Azure tem dois tipos, públicos e internos. Ambos os tipos de redundância de zona de suporte de suporte de carga e implantação zonal.  Ambos os tipos de equilibrador de carga podem direcionar o tráfego através das zonas, se necessário.

## <a name="frontend"></a>Front-end

Um frontend de balançador de carga é uma configuração IP frontal que refere um endereço IP público ou um endereço IP privado dentro da sub-rede de uma rede virtual. Forma o ponto final equilibrado de carga onde o seu serviço está exposto.

Um recurso de balançador de carga pode conter regras com frontends zonais e redundantes em zona simultaneamente. Quando um IP público ou privado é garantido para uma zona, a zonalidade (ou falta dela) não é alterável. Para alterar ou omitir a zonalidade de um endereço IP público ou privado, recrie o IP na zona apropriada. 

As zonas de disponibilidade não alteram os constrangimentos para vários frontends. [Reveja vários frontends para Load Balancer](load-balancer-multivip-overview.md) para obter detalhes sobre esta capacidade.

### <a name="zone-redundant"></a>Zona redundante 

Numa região com zonas de disponibilidade, um frontend de balançador de carga padrão pode ser redundante em zona. Várias zonas podem servir de entrada ou saída numa região. Este tráfego é servido por um único endereço IP. Os esquemas de despedimento do DNS não são necessários. 

Um único endereço IP frontal sobreviverá à falha da zona. O IP frontal pode ser utilizado para alcançar todos os membros do pool de backend (sem impacto) independentemente da zona. Uma ou mais zonas de disponibilidade podem falhar e o caminho dos dados sobrevive enquanto uma zona da região permanecer saudável. 

O endereço IP do frontend é servido simultaneamente por múltiplas infraestruturas independentes em várias zonas de disponibilidade. Qualquer retração ou restabelecimento terá sucesso noutras zonas não afetadas pela falha da zona. 

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Rede Virtual NAT">
</p>

*Figura: Balanceador de carga redundante zona*

### <a name="zonal"></a>Zonal

Você pode optar por ter um frontend garantido para uma única zona, que é conhecida como *frontend zonal*.  Este cenário significa que qualquer fluxo de entrada ou saída é servido por uma única zona numa região.  O seu frontend partilha o destino com a saúde da zona.  O caminho dos dados não é afetado por falhas em zonas diferentes das que foram garantidas. Pode utilizar frontes zonais para expor um endereço IP por Zona de Disponibilidade.  

Adicionalmente, é suportada a utilização de frontends zonais diretamente para pontos finais equilibrados de carga dentro de cada zona. Pode utilizar esta configuração para expor por pontos finais equilibrados em zona para monitorizar individualmente cada zona. Para pontos finais públicos, pode integrá-los com um produto de equilíbrio de carga DNS como [Traffic Manager](../traffic-manager/traffic-manager-overview.md) e usar um único nome DNS.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Rede Virtual NAT">
</p>

*Figura: Balanceador de carga zonal*

Se desejar misturar estes conceitos (zona redundante e zonal para o mesmo backend), reveja [vários frontends para Azure Load Balancer](load-balancer-multivip-overview.md).

Para um frontend do balançador de carga pública, adicione um parâmetro **de zonas** ao IP público. Este IP público é referenciado pela configuração IP frontend utilizada pela respetiva regra.

Para uma frente do balançador de carga interna, adicione um parâmetro **de zonas** à configuração IP do balanço de carga interno. Um frontend zonal garante um endereço IP numa sub-rede para uma zona específica.

## <a name="backend"></a>Back-end

O Azure Load Balancer trabalha com casos de máquinas virtuais. Estes casos podem ser conjuntos autónomos, conjuntos de disponibilidade ou conjuntos de escala de máquinas virtuais. Qualquer máquina virtual numa única rede virtual pode fazer parte da piscina de backend, qualquer que seja a zona a que a máquina virtual esteja garantida.

Para alinhar e garantir o seu frontend e backend com uma única zona, coloque apenas as máquinas virtuais dentro da mesma zona na piscina de backend correspondente.

Para endereçar máquinas virtuais em várias zonas, coloque máquinas virtuais de várias zonas na mesma piscina de backend. 

Quando utilizar conjuntos de balança de máquinas virtuais, coloque um ou mais conjuntos de balança de máquinas virtuais na mesma piscina de backend. Os conjuntos de escala podem ser em zonas individuais ou múltiplas.

## <a name="outbound-connections"></a>Ligações de saída

Zona redundante e zonal aplicam-se às [ligações de saída](load-balancer-outbound-connections.md). Um endereço IP público redundante de zona utilizado para ligações de saída é servido por todas as zonas. Um endereço IP público zonal é servido apenas pela zona em que é garantido. 

Ligação de saída As dotações portuárias SNAT sobrevivem a falhas de zona e o seu cenário continuará a fornecer conectividade SNAT de saída se não for afetado por falha de zona. As falhas de zona podem exigir que as ligações sejam restabelecidas para cenários redundantes de zona se o tráfego for servido por uma zona afetada. Os fluxos em zonas que não as zonas afetadas não são afetados.

O algoritmo de pré-adição da porta SNAT é o mesmo com ou sem zonas de disponibilidade.

## <a name="health-probes"></a>Sondas do estado de funcionamento

As definições de sonda de saúde existentes permanecem como estão sem zonas de disponibilidade. No entanto, expandimos o modelo de saúde a nível de infraestruturas. 

Ao utilizar frontends redundantes de zona, o balançador de carga expande a sua verificação interna de saúde. O equilibrador de carga sonda independentemente a disponibilidade de uma máquina virtual de cada zona e fecha caminhos através de zonas que falharam sem intervenção.  

Outras zonas que podem chegar a este VM podem continuar a servir o VM a partir dos respetivos frontends. Durante os eventos de falha, cada zona pode ter diferentes distribuições de novos fluxos, protegendo ao mesmo tempo a saúde geral do seu serviço.

## <a name="design-considerations"></a><a name="design"></a> Considerações de design

O equilibrador de carga é flexível no contexto das zonas de disponibilidade. Pode optar por alinhar-se com zonas ou ser redundante em zona para cada regra. O aumento da disponibilidade pode vir ao preço do aumento da complexidade. Design para disponibilidade para um desempenho ideal.

### <a name="zone-redundancy"></a>Redundância de zona

O Balancer de Carga torna simples ter um único IP como frontend redundante de zona. Um endereço IP redundante de zona pode servir um recurso zonal em qualquer zona.  O IP pode sobreviver a uma ou mais falhas de zona enquanto uma zona permanecer saudável na região.  Em vez disso, uma frente zonal é uma redução do serviço para uma única zona e partilha o destino com a respetiva zona.

A redundância de zona não implica datapath ou avião de controlo sem impacto; é um plano de dados. Os fluxos redundantes de zona podem utilizar quaisquer zonas e os fluxos de um cliente utilizarão todas as zonas saudáveis de uma região. Numa falha de zona, os fluxos de tráfego usando zonas saudáveis não são afetados. 

Os fluxos de tráfego que utilizam uma zona no momento da avaria do fuso podem ser afetados, mas as aplicações podem recuperar. O tráfego continua nas zonas saudáveis da região após a retransmissão quando Azure convergiu em torno da falha da zona.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a> Fronteiras de zonas cruzadas

É importante entender que sempre que um serviço cruza zonas, você compartilha o destino com não uma zona, mas potencialmente várias zonas. Como resultado, o seu serviço pode não ter obtido qualquer disponibilidade sobre implementações não zonais.

Evite introduzir dependências de zonas cruzadas não intencionais, o que anulará os ganhos de disponibilidade ao utilizar zonas de disponibilidade. Se a sua aplicação contiver vários componentes críticos, certifique-se de que a sobrevivência se uma zona falhar.

Um único componente crítico para a sua aplicação pode afetar toda a sua aplicação se ela existir apenas numa zona diferente da(s) zona de sobrevivência. Considere a restauração da zona e como a sua aplicação irá convergir. Compreenda como a sua aplicação responde a falhas de partes da sua. Reveja os pontos-chave e use-os para perguntas à medida que pensa no seu cenário específico.

- Se a sua aplicação tiver dois componentes:

    * Endereço IP
    * Máquina virtual com disco gerido

Estão configurados na zona 1 e na zona 2. Quando a zona 1 falhar, o seu serviço não sobreviverá. Não cruze zonas com cenários zonais a menos que compreenda que está a criar um modo de falha potencialmente perigoso. Este cenário é permitido proporcionar flexibilidade.

- Se a sua aplicação tiver dois componentes:

    * Endereço IP
    * Máquina virtual com disco gerido

Estão configurados para serem redundantes na zona 1. O seu serviço sobreviverá à falha da zona 2, zona 3, ou ambas, a menos que a zona 1 tenha falhado. No entanto, perde-se alguma capacidade de raciocinar sobre a saúde do seu serviço se tudo o que está a observar é a capacidade de alcance do frontend.  Considere desenvolver um modelo de saúde e capacidade mais extenso.  Você pode usar conceitos zona-redundantes e zonais juntos para expandir a perceção e a gerenciabilidade.

- Se a sua aplicação tiver dois componentes:

    * Frontend de balançador de carga redundante de zona
    * Escala de máquina virtual transversal definida em três zonas

Os seus recursos em zonas não afetadas por falhas estarão disponíveis. A sua capacidade de serviço pode ser degradada durante a avaria. Do ponto de vista da infraestrutura, a sua implantação pode sobreviver a uma ou mais falhas de zona. Este cenário levanta as seguintes questões:

  - Compreende como a sua aplicação responde a falhas e capacidade degradada?
  - Precisa de ter salvaguardas ao seu serviço para forçar um fracasso a um par de regiões, se necessário?
  - Como vai monitorizar, detetar e mitigar tal cenário? Pode utilizar diagnósticos padrão do balanceador de carga para aumentar a monitorização do desempenho do seu serviço. Considere o que está disponível e o que pode precisar de aumento.

- As zonas podem tornar as falhas mais facilmente compreendidas e contidas. A falha na zona não é diferente de outras falhas quando se trata de intervalos, retrações e algoritmos de backoff. O Azure Load Balancer fornece caminhos redundantes de zona. O equilibrador de carga tenta recuperar rapidamente, a um nível de pacote em tempo real. Podem ocorrer retransmissão ou restabelecimentos durante o início de uma falha. É importante entender como a sua aplicação lida com falhas. O seu esquema de equilíbrio de cargas sobreviverá, mas precisa de planear as seguintes perguntas:

  - Quando uma zona falha, o seu serviço compreende esta falha e se o Estado está perdido, como vai recuperar?
  - Quando uma zona regressa, a sua aplicação entende como convergir com segurança?

Reveja [os padrões de design em nuvem Azure](https://docs.microsoft.com/azure/architecture/patterns/) para melhorar a resiliência da sua aplicação a cenários de falha.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [As Zonas de Disponibilidade](../availability-zones/az-overview.md)
- Saiba mais sobre o [Balanceador de Carga Standard](load-balancer-standard-overview.md)
- Saiba como [carregar VMs de equilíbrio dentro de uma zona usando um Balanceador de Carga Padrão com um frontend zonal](load-balancer-standard-public-zonal-cli.md)
- Saiba como [carregar vMs de equilíbrio em zonas usando um Balanceador de Carga Padrão com um frontend redundante de zona](load-balancer-standard-public-zone-redundant-cli.md)
- Conheça os [padrões de design de nuvem Azure](https://docs.microsoft.com/azure/architecture/patterns/) para melhorar a resiliência da sua aplicação a cenários de falha.
