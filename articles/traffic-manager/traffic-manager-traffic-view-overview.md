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
ms.date: 01/22/2021
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 66376655c61903761d93ea228c6d72fa05734353
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743054"
---
# <a name="traffic-manager-traffic-view"></a>Vista de tráfego do gestor de tráfego

O Gestor de Tráfego fornece-lhe um encaminhamento de nível DNS (Sistema de Nome de Domínio). Este serviço permite que os seus utilizadores finais se direcionem para pontos finais saudáveis com base no método de encaminhamento à sua escolha. A Traffic View fornece ao Gestor de Tráfego uma visão das suas bases de utilizador (a um nível de granularidade de resolver DNS) e o seu padrão de tráfego. Quando ativa a Visão de Tráfego, estas informações são processadas para lhe fornecer informações accuáveis. 

Ao utilizar a Vista de Tráfego, pode:
- compreender onde estão as suas bases de utilizador (até uma granularidade de nível DNS local).
- ver o volume de tráfego (observado como consultas DNS tratados pelo Azure Traffic Manager) originários destas regiões.
- obter informações sobre o que é a latência representativa experimentada por estes utilizadores.
- mergulhe profundamente nos padrões de tráfego específicos de cada uma destas bases de utilizadores para as regiões de Azure, onde você tem pontos finais. 

Por exemplo, você pode usar o Traffic View para entender quais as regiões que têm uma grande quantidade de tráfego, mas sofrem de latências mais elevadas. Em seguida, você usa esta informação para planear a sua expansão de pegada para novas regiões de Azure. Desta forma, os seus utilizadores terão uma experiência de latência mais baixa.

## <a name="how-traffic-view-works"></a>Como funciona a visão de tráfego

A Traffic View funciona através da análise das consultas recebidas nos últimos sete dias para um perfil. A partir das informações de consultas recebidas, a Traffic View extrai a origem IP do DNS resolver utilizado para representar a localização dos utilizadores. Esta informação é agrupada a nível de DNS para criar regiões de base de utilizadores. O Gestor de Tráfego mantém a informação geográfica dos endereços IP. O Gestor de Tráfego olha então para as regiões de Azure para as quais a consulta é encaminhada e constrói um mapa de fluxo de tráfego para os utilizadores dessas regiões.
 
No passo seguinte, o Traffic Manager correlaciona a região da base de utilizadores com o mapeamento da região de Azure com as tabelas de latência da inteligência da rede. Este quadro é mantido para que diferentes redes de utilizadores finais compreendam a latência média sentida pelos utilizadores dessas regiões ao ligarem-se às regiões de Azure. Todos estes cálculos são então combinados a um nível IP de DNS por local antes de ser apresentado a si. Pode consumir a informação de várias formas.

A frequência da atualização de dados de visualização de tráfego depende de várias variáveis de serviço interno. No entanto, os dados são atualizados uma vez a cada 24 horas.

>[!NOTE]
>A latência descrita no Traffic View é uma latência representativa entre o utilizador final e as regiões de Azure a que se tinham ligado, e não é a latência de pesquisa de DNS. A Traffic View faz uma melhor estimativa de esforço da latência entre o DNS local e a região de Azure para a qual a consulta foi encaminhada, se não houver dados suficientes disponíveis, então a latência devolvida será nula. 

## <a name="visual-overview"></a>Visão geral visual

Ao navegar para a secção **'Vista de Tráfego'** na página do Gestor de Tráfego, vê um mapa geográfico com uma sobreposição de insights de Vista de Tráfego. O mapa fornece informações sobre a base de utilizadores e pontos finais para o seu perfil de Gestor de Tráfego.

![Controlador de tráfego ver vista geográfica][1]

### <a name="user-base-information"></a>Informações sobre base de utilizadores

Para os dns locais para os quais a informação de localização está disponível, eles são mostrados no mapa. A cor do DNS resolver denota a latência média experimentada pelos utilizadores finais que utilizaram esse DNS resolver para as suas consultas de Gestor de Tráfego.

Se pairar sobre uma localização de DNS no mapa, mostra:
- o endereço IP do DNS resolver
- o volume de tráfego de consulta DNS visto pelo Gestor de Tráfego a partir dele
- os pontos finais para os quais o tráfego do DNS resolver foi encaminhado, como uma linha entre o ponto final e o DNS resolver 
- a latência média daquele local até ao ponto final, representada como a cor da linha que os liga

### <a name="endpoint-information"></a>Informações sobre ponto final

As regiões de Azure em que os pontos finais estão localizados são mostradas como pontos azuis no mapa. Se o seu ponto final é externo e não tem um mapa da região de Azure, então eles são mostrados no topo do mapa. Selecione qualquer ponto final para ver as diferentes localizações (com base no DNS resolver utilizado) de onde o tráfego foi direcionado para esse ponto final. As ligações são apresentadas como uma linha entre o ponto final e a localização do DNS. São coloridas de acordo com a latência representativa entre aquele par. Pode ver o nome do ponto final e da região de Azure em que corre. O volume total de pedidos que lhe são dirigidos por este perfil de Gestor de Tráfego também é apresentado.


## <a name="tabular-listing-and-raw-data-download"></a>Listagem tabular e download de dados brutos

Pode ver os dados do Traffic View num formato tabular no portal Azure. Há uma entrada para cada par DNS resolver IP/endpoint que mostra:

* O endereço IP do DNS resolver.
* O nome.
* A localização geográfica da região de Azure em que se situa o ponto final (se disponível).
* O volume de pedidos associados a esse DNS resolve-se a esse ponto final.
* A latência representativa associada aos utilizadores finais que utilizam o DNS (quando disponível). 

Também pode descarregar os dados do Traffic View como um ficheiro CSV que pode ser usado como parte de um fluxo de trabalho analítico à sua escolha.

## <a name="billing"></a>Faturação

Quando utiliza o Traffic View, é faturado com base no número de pontos de dados utilizados para criar os insights apresentados. Atualmente, o único tipo de ponto de dados utilizado são as consultas recebidas contra o seu perfil de Gestor de Tráfego. Para mais detalhes sobre o preço, visite a [página de preços do Gestor de Tráfego.](https://azure.microsoft.com/pricing/details/traffic-manager/)

## <a name="faqs"></a>FAQs

* [O que faz a Traffic View?](./traffic-manager-faqs.md#what-does-traffic-view-do)

* [Como posso beneficiar-me da utilização da Traffic View?](./traffic-manager-faqs.md#how-can-i-benefit-from-using-traffic-view)

* [Como é que a Vista de Tráfego é diferente das métricas do Gestor de Tráfego disponível através do monitor Azure?](./traffic-manager-faqs.md#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [A Traffic View utiliza informações de sub-rede do cliente EDNS?](./traffic-manager-faqs.md#does-traffic-view-use-edns-client-subnet-information)

* [Quantos dias de dados utiliza a Traffic View?](./traffic-manager-faqs.md#how-many-days-of-data-does-traffic-view-use)

* [Como é que a Traffic View lida com pontos finais externos?](./traffic-manager-faqs.md#how-does-traffic-view-handle-external-endpoints)

* [Preciso de ativar a Traffic View para cada perfil da minha assinatura?](./traffic-manager-faqs.md#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Como posso desligar a Vista de Trânsito?](./traffic-manager-faqs.md#how-can-i-turn-off-traffic-view)

* [Como funciona a faturação da Traffic View?](./traffic-manager-faqs.md#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Passos seguintes

- Saiba [como funciona o Gestor de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre os [métodos de encaminhamento de tráfego suportados](traffic-manager-routing-methods.md) pelo Traffic Manager
- Saiba como [criar um perfil de Gestor de Tráfego](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png