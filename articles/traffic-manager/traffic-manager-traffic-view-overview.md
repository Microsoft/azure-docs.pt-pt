---
title: Vista de tráfego em Azure Traffic Manager
description: Nesta introdução, saiba como funciona o traffic manager Traffic view.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: f1a3cc5ccb326d6a198895f9f459e661f7805b1e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003842"
---
# <a name="traffic-manager-traffic-view"></a>Vista de tráfego do gestor de tráfego

O Traffic Manager fornece-lhe um encaminhamento de nível DNS para que os seus utilizadores finais sejam direcionados para pontos finais saudáveis com base no método de encaminhamento especificado quando criou o perfil. A Traffic View fornece ao Gestor de Tráfego uma visão das suas bases de utilizador (a um nível de granularidade de resolver DNS) e o seu padrão de tráfego. Quando ativa a Visão de Tráfego, estas informações são processadas para lhe fornecer informações accuáveis. 

Ao utilizar a Vista de Tráfego, pode:
- compreender onde estão as suas bases de utilizador (até uma granularidade de nível DNS local).
- ver o volume de tráfego (observado como consultas DNS tratados pelo Azure Traffic Manager) originários destas regiões.
- obter informações sobre o que é a latência representativa experimentada por estes utilizadores.
- mergulhe profundamente nos padrões de tráfego específicos de cada uma destas bases de utilizadores para as regiões de Azure, onde você tem pontos finais. 

Por exemplo, pode utilizar o Traffic View para perceber quais as regiões que têm um grande número de tráfego, mas sofrem de atrasos mais elevados. Em seguida, pode usar esta informação para planear a expansão da sua pegada para novas regiões de Azure para que estes utilizadores possam ter uma experiência de latência mais baixa.

## <a name="how-traffic-view-works"></a>Como funciona a visão de tráfego

A Traffic View funciona fazendo com que o Gestor de Tráfego veja as consultas recebidas nos últimos sete dias contra um perfil que tenha esta funcionalidade ativada. A partir das informações sobre consultas recebidas, a Traffic View extrai a origem IP do DNS que é utilizado como representação da localização dos utilizadores. Estes são então agrupados num nível de granito de resolução de DNS para criar regiões de base de utilizadores, utilizando a informação geográfica dos endereços IP mantidos pelo Traffic Manager. O Gestor de Tráfego olha então para as regiões de Azure para as quais a consulta foi encaminhada e constrói um mapa de fluxo de tráfego para os utilizadores dessas regiões.  
No passo seguinte, o Traffic Manager correlaciona a região da base de utilizadores com o mapeamento da região de Azure com as tabelas de latência de inteligência da rede que mantém para diferentes redes de utilizadores finais para entender a latência média experimentada pelos utilizadores dessas regiões ao ligar-se às regiões de Azure. Todos estes cálculos são então combinados a um nível IP de DNS por local antes de ser apresentado a si. Pode consumir a informação de várias formas.

A frequência da atualização de dados de visualização de tráfego depende de várias variáveis de serviço interno. No entanto, os dados são normalmente atualizados uma vez a cada 24 horas.

>[!NOTE]
>A latência descrita no Traffic View é uma latência representativa entre o utilizador final e as regiões de Azure a que se tinham ligado, e não é a latência de pesquisa de DNS. A Traffic View faz uma melhor estimativa de esforço da latência entre o DNS local e a região de Azure para a qual a consulta foi encaminhada, se não houver dados suficientes disponíveis, então a latência devolvida será nula. 

## <a name="visual-overview"></a>Visão geral visual

Ao navegar para a secção **'Vista de Tráfego'** na página 'Traffic Manager', é-lhe apresentado um mapa geográfico com uma sobreposição de insights de Vista de Tráfego. O mapa fornece informações sobre a base de utilizadores e pontos finais para o seu perfil de Gestor de Tráfego.

![Controlador de tráfego ver vista vista vista vista geográfica][1]

### <a name="user-base-information"></a>Informações sobre base de utilizadores

Para os dns locais para os quais a informação de localização está disponível, são mostradas no mapa. A cor do DNS resolver denota a latência média experimentada pelos utilizadores finais que utilizaram esse DNS resolver para as suas consultas de Gestor de Tráfego.

Se pairar sobre uma localização de DNS no mapa, mostra:
- o endereço IP do DNS resolver
- o volume de tráfego de consulta DNS visto pelo Gestor de Tráfego a partir dele
- os pontos finais para os quais o tráfego do DNS resolver foi encaminhado, como uma linha entre o ponto final e o DNS resolver 
- a latência média daquele local até ao ponto final, representada como a cor da linha que os liga

### <a name="endpoint-information"></a>Informações sobre ponto final

As regiões de Azure em que residem os pontos finais são mostradas como pontos azuis no mapa. Se o seu ponto final é externo e não tem uma região Azure mapeada, é mostrado no topo do mapa. Clique em qualquer ponto final para ver as diferentes localizações (com base no DNS resolver utilizado) de onde o tráfego foi direcionado para esse ponto final. As ligações são mostradas como uma linha entre o ponto final e a localização de resolver o DNS e são coloridas de acordo com a latência representativa entre esse par. Além disso, pode ver o nome do ponto final, a região Azure em que funciona, e o volume total de pedidos que lhe foram dirigidos por este perfil de Gestor de Tráfego.


## <a name="tabular-listing-and-raw-data-download"></a>Listagem tabular e download de dados brutos

Pode ver os dados do Traffic View num formato tabular no portal Azure. Há uma entrada para cada par DNS resolver IP/endpoint que mostra o endereço IP do DNS resolver, o nome e localização geográfica da região de Azure em que o ponto final está localizado (se disponível), o volume de pedidos associados a esse DNS resolver esse ponto final, e a latência representativa associada aos utilizadores finais usando esse DNS (quando disponível). Também pode descarregar os dados do Traffic View como um ficheiro CSV que pode ser usado como parte de um fluxo de trabalho analítico à sua escolha.

## <a name="billing"></a>Faturação

Quando utiliza o Traffic View, é faturado com base no número de pontos de dados utilizados para criar os insights apresentados. Atualmente, o único tipo de ponto de dados utilizado são as consultas recebidas contra o seu perfil de Gestor de Tráfego. Para mais detalhes sobre o preço, visite a [página de preços do Gestor de Tráfego.](https://azure.microsoft.com/pricing/details/traffic-manager/)

## <a name="faqs"></a>FAQs

* [O que faz a Traffic View?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-does-traffic-view-do)

* [Como posso beneficiar-me da utilização da Traffic View?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-benefit-from-using-traffic-view)

* [Como é que a Vista de Tráfego é diferente das métricas do Gestor de Tráfego disponível através do monitor Azure?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [A Traffic View utiliza informações de sub-rede do cliente EDNS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-view-use-edns-client-subnet-information)

* [Quantos dias de dados utiliza a Traffic View?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-days-of-data-does-traffic-view-use)

* [Como é que a Traffic View lida com pontos finais externos?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-handle-external-endpoints)

* [Preciso de ativar a Traffic View para cada perfil da minha assinatura?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Como posso desligar a Vista de Trânsito?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-turn-off-traffic-view)

* [Como funciona a faturação da Traffic View?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Passos seguintes

- Saiba [como funciona o Gestor de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre os [métodos de encaminhamento de tráfego suportados](traffic-manager-routing-methods.md) pelo Traffic Manager
- Saiba como [criar um perfil de Gestor de Tráfego](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png