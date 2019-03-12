---
title: Vista de tráfego no Gestor de tráfego do Azure
description: Introdução à vista de tráfego do Gestor de tráfego
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 70ac4319e2ea0081f7805c2fb936af1310d57d8f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534945"
---
# <a name="traffic-manager-traffic-view"></a>Vista de tráfego do Gestor de tráfego

O Gestor de tráfego fornece com o encaminhamento ao nível do DNS para que os utilizadores finais são direcionados para pontos finais de bom estado de funcionamento com base no método de encaminhamento especificou quando criou o perfil. Vista de tráfego fornece o Gestor de tráfego com uma visão das suas bases de utilizadores (num nível de granularidade de resolução DNS) e seu padrão de tráfego. Quando ativa a vista de tráfego, esta informação é processada para lhe fornecer informações acionáveis. 

Ao utilizar a vista de tráfego, pode:
- compreenda a localização suas bases de utilizadores (local DNS resolvedor nível com uma granularidade até).
- ver o volume de tráfego (observado como consultas DNS, processadas pelo Gestor de tráfego do Azure) com origem nestas regiões.
- Obtenha informações sobre o que é a latência representativa registada por esses utilizadores.
- descrição aprofundada padrões de tráfego específicos de cada uma destas bases de utilizadores para as regiões do Azure em que tem pontos de extremidade. 

Por exemplo, pode utilizar a vista de tráfego para compreender quais são as regiões têm um número elevado de tráfego, mas sofrem latências maiores. Em seguida, pode usar essas informações para planear a expansão de requisitos de espaço para novas regiões do Azure, para que estes utilizadores podem ter uma experiência de latência mais baixa.

## <a name="how-traffic-view-works"></a>Como funciona a vista de tráfego

Vista de tráfego funciona por meio de Gestor de tráfego examinar as consultas de entrada recebidas nos últimos sete dias, em relação a um que tenha esta funcionalidade ativada. Informações de consultas recebidas, a vista de tráfego extrai o IP de origem do resolvedor DNS que serve como uma representação da localização dos utilizadores. Estes são, em seguida, agrupados em conjunto com uma granularidade de nível de resolvedor DNS para criar regiões de base de usuário usando as informações geográficas de endereços IP mantido pelo Gestor de tráfego. O Gestor de tráfego analisa, em seguida, as regiões do Azure para o qual a consulta foi encaminhada e constrói um mapa de fluxo de tráfego para os utilizadores dessas regiões.  
No próximo passo, o Gestor de tráfego correlaciona a região base do utilizador para mapeamento de região do Azure com as tabelas de latência de inteligência de rede que mantém para redes de utilizador final diferentes compreender a latência média encontrada pelos utilizadores a partir dessas regiões quando a ligar a regiões do Azure. Todos os cálculos, em seguida, são combinados num por local DNS resolvedor nível de IP antes que seja apresentada ao utilizador. Pode consumir as informações de várias formas.

A frequência de atualização de dados de vista de tráfego depende de várias variáveis de serviço interno. No entanto, os dados normalmente são atualizados uma vez a cada 24 horas.

>[!NOTE]
>A latência descrita na vista de tráfego é uma latência representativa entre o utilizador final e regiões do Azure à qual tinha ligada para e não é a latência de pesquisa DNS. Tráfego vista faz uma melhor estimativa do esforço da latência entre o resolvedor DNS local e a região do Azure, que a consulta foi encaminhada, se houver dados insuficientes disponível, em seguida, a latência retornada será nulo. 

## <a name="visual-overview"></a>Visão geral do Visual

Quando navegar para o **vista de tráfego** secção na página do Gestor de tráfego, é apresentada com um mapa geográfico com uma sobreposição de informações de vista de tráfego. O map fornece informações sobre a base de usuários e os pontos finais para o seu perfil do Gestor de tráfego.

### <a name="user-base-information"></a>Informações básicas do utilizador

Para essas resoluções DNS locais para o qual local as informações estão disponíveis, estas são apresentadas no mapa. A cor de resolução de DNS indica a latência média experientes pelos utilizadores finais que usaram esse resolvedor DNS para as consultas de Gestor de tráfego.

Se coloque o cursor sobre uma localização do resolvedor DNS no mapa, mostra:
- o endereço IP do resolvedor de DNS
- o volume de tráfego de consulta DNS visto pelo Gestor de tráfego a partir do mesmo
- os pontos de extremidade ao qual o tráfego do DNS resolvedor foi encaminhado, como uma linha entre o ponto final e o resolvedor DNS 
- a latência média partir dessa localização para o ponto final, representado como a cor da linha de ligá-las

### <a name="endpoint-information"></a>Informações do ponto final

Regiões do Azure na qual residem os pontos finais é mostrado como pontos azuis no mapa. Se o ponto final é externo e não tem uma região do Azure mapeada para ele, é apresentada na parte superior do mapa. Clique em qualquer ponto final para ver as diferentes localizações (com base no resolvedor DNS utilizado) de onde o tráfego foi direcionado para esse ponto final. As ligações são apresentadas como uma linha entre o ponto final e a localização do resolvedor DNS e são coloridas de acordo com a latência representativa entre esse par. Além disso, pode ver o nome do ponto final, a região do Azure em que é executado e o volume total de pedidos que foram direcionados para o mesmo por este perfil do Gestor de tráfego.


## <a name="tabular-listing-and-raw-data-download"></a>Lista tabular e a transferência de dados não processados

Pode ver os dados da vista de tráfego num formato tabular no portal do Azure. Existe uma entrada para cada IP de resolução DNS / ponto de extremidade emparelhar que mostra o endereço IP do resolvedor DNS, o nome e localização geográfica da região do Azure em que o ponto final está localizado (se disponível), o volume de pedidos associadas com essa resolução de DNS para ponto de extremidade e a latência representativa associados a utilizadores finais com esse DNS (onde disponível). Também pode transferir os dados da vista de tráfego como um ficheiro CSV que pode ser utilizado como parte de um fluxo de trabalho de análise da sua preferência.

## <a name="billing"></a>Faturação

Quando utiliza a vista de tráfego, é cobrado com base no número de pontos de dados utilizado para criar as informações apresentadas. Atualmente, o tipo de ponto de dados único utilizado é as consultas recebidas em relação a seu perfil do Gestor de tráfego. Para obter mais detalhes sobre os preços, visite o [Gestor de tráfego, página de preços](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Passos Seguintes

- Saiba [como funciona o Gestor de tráfego](traffic-manager-overview.md)
- Saiba mais sobre o [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) suportada pelo Gestor de tráfego
- Saiba como [criar um perfil do Gestor de tráfego](traffic-manager-create-profile.md)

