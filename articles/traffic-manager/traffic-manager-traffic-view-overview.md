---
title: Vista de tráfego em Gestor de Tráfego Azure
description: Nesta introdução, saiba como funciona a visão de tráfego do gestor de tráfego.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: d5a03fde564b14baee97f50fa63fd58bf83694b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938347"
---
# <a name="traffic-manager-traffic-view"></a>Vista de tráfego do gestor de tráfego

O Traffic Manager fornece-lhe o encaminhamento de nível DNS para que os seus utilizadores finais sejam direcionados para pontos finais saudáveis com base no método de encaminhamento especificado quando criou o perfil. Traffic View fornece ao Traffic Manager uma visão das suas bases de utilizador (ao nível da granularidade do DNS) e do seu padrão de tráfego. Quando ativa a Traffic View, esta informação é processada para lhe fornecer informações atuais. 

Ao utilizar a Vista de Tráfego, pode:
- compreender onde estão as bases de utilizador (até uma granularidade de nível DNS local).
- ver o volume de tráfego (observado como consultas dNS tratadas pelo Gestor de Tráfego Azure) originários destas regiões.
- obter informações sobre o que é a latência representativa experimentada por estes utilizadores.
- mergulho profundo nos padrões de tráfego específicos de cada uma destas bases de utilizadores para regiões de Azure onde você tem pontos finais. 

Por exemplo, pode utilizar o Traffic View para entender quais as regiões que têm um grande número de tráfego, mas sofrem de tardios mais elevados. Em seguida, pode utilizar esta informação para planear a expansão da sua pegada para novas regiões do Azure para que estes utilizadores possam ter uma menor experiência de latência.

## <a name="how-traffic-view-works"></a>Como funciona a Vista de Tráfego

Traffic View funciona fazendo com que o Gestor de Tráfego olhe para as consultas recebidas nos últimos sete dias contra um perfil que tem esta funcionalidade ativada. A partir das informações de perguntas de entrada, o Traffic View extrai a fonte IP do DNS resolver que é usada como representação da localização dos utilizadores. Estes são então agrupados a uma granularidade de nível DNS para criar regiões de base de utilizadores utilizando a informação geográfica dos endereços IP mantidos pelo Gestor de Tráfego. O Gestor de Tráfego olha então para as regiões de Azure para as quais a consulta foi encaminhada e constrói um mapa de fluxo de tráfego para os utilizadores dessas regiões.  
No passo seguinte, o Traffic Manager correlaciona a região da base de utilizadores com o mapeamento da região de Azure com as tabelas de latência da inteligência da rede que mantém para diferentes redes de utilizadores finais entenderem a latência média sentida pelos utilizadores dessas regiões quando ligação às regiões de Azure. Todos estes cálculos são então combinados a um nível ip local de DNS antes de ser apresentado a si. Pode consumir a informação de várias formas.

A frequência da atualização de dados da visualização de tráfego depende de múltiplas variáveis de serviço interno. No entanto, os dados são geralmente atualizados uma vez a cada 24 horas.

>[!NOTE]
>A latência descrita no Traffic View é uma latência representativa entre o utilizador final e as regiões azure às quais tinham ligado, e não é a latência de procura ção do DNS. A Traffic View faz uma melhor estimativa de esforço da latência entre o DNS local e a região de Azure para a consulta, se não houver dados disponíveis suficientes, então a latência devolvida será nula. 

## <a name="visual-overview"></a>Visão geral visual

Ao navegar para a secção **Traffic View** na página do Traffic Manager, é-lhe apresentado um mapa geográfico com uma sobreposição de insights de Traffic View. O mapa fornece informações sobre a base de utilizadores e pontos finais para o seu perfil de Gestor de Tráfego.

![Vista geográfica do gestor de tráfego][1]

### <a name="user-base-information"></a>Informações da base do utilizador

Para os dNS locais para os quais as informações de localização estão disponíveis, são mostradas no mapa. A cor do dns resolver denota a latência média experimentada pelos utilizadores finais que usaram esse dNS resolver para as suas consultas de Gestor de Tráfego.

Se pairar sobre uma localização de dns resolver no mapa, ele mostra:
- o endereço IP do DNS resolver
- o volume de tráfego de consulta DNS visto pelo Gestor de Tráfego a partir dele
- os pontos finais para os quais o tráfego do DNS resolver foi encaminhado, como uma linha entre o ponto final e o resolver DNS 
- a latência média desse local para o ponto final, representada como a cor da linha que os liga

### <a name="endpoint-information"></a>Informação sobre ponto final

As regiões azure em que residem os pontos finais são mostradas como pontos azuis no mapa. Se o seu ponto final é externo e não tem uma região azure mapeada, é mostrado no topo do mapa. Clique em qualquer ponto final para ver os diferentes locais (com base no resolver DNS utilizado) de onde o tráfego foi direcionado para esse ponto final. As ligações são mostradas como uma linha entre o ponto final e a localização do DNS resolver e são coloridas de acordo com a latência representativa entre esse par. Além disso, pode ver o nome do ponto final, a região de Azure em que funciona, e o volume total de pedidos que lhe foram direcionados por este perfil de Gestor de Tráfego.


## <a name="tabular-listing-and-raw-data-download"></a>Listagem tabular e download de dados brutos

Pode ver os dados do Traffic View num formato tabular no portal Azure. Existe uma entrada para cada par de Ip/endpoint do DNS que mostra o endereço IP do DNS resolver, o nome e localização geográfica da região de Azure em que o ponto final está localizado (se disponível), o volume de pedidos associados a esse DNS resolver para esse ponto final, e a latência representativa associada aos utilizadores finais que usam esse DNS (se disponível). Também pode descarregar os dados do Traffic View como um ficheiro CSV que pode ser usado como parte de um fluxo de trabalho analítico à sua escolha.

## <a name="billing"></a>Faturação

Quando utiliza a Traffic View, é faturado com base no número de pontos de dados utilizados para criar os insights apresentados. Atualmente, o único tipo de ponto de dados utilizado são as consultas recebidas contra o seu perfil de Gestor de Tráfego. Para mais detalhes sobre os preços, visite a página de preços do Gestor de [Tráfego.](https://azure.microsoft.com/pricing/details/traffic-manager/)

## <a name="faqs"></a>FAQs

* [O que faz a Vista de Trânsito?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-does-traffic-view-do)

* [Como posso beneficiar da utilização da Traffic View?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-benefit-from-using-traffic-view)

* [Como é que a Traffic View é diferente das métricas do Traffic Manager disponíveis através do monitor Azure?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [A Vista de Tráfego utiliza informações da Subnet do cliente EDNS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-view-use-edns-client-subnet-information)

* [Quantos dias de dados o Traffic View utiliza?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-days-of-data-does-traffic-view-use)

* [Como é que a Traffic View lida com pontos finais externos?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-handle-external-endpoints)

* [Preciso de ativar a Vista de Tráfego para cada perfil na minha subscrição?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Como posso desligar a Vista de Trânsito?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-turn-off-traffic-view)

* [Como funciona a faturação da Traffic View?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Passos seguintes

- Saiba como funciona o [Gestor de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre os [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) suportados pelo Traffic Manager
- Saiba como [criar um perfil de Gestor](traffic-manager-create-profile.md) de Tráfego

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png