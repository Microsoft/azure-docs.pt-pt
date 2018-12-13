---
title: Dashboard de aplicação
titleSuffix: Language Understanding - Azure Cognitive Services
description: Saiba mais sobre o dashboard de aplicações, uma ferramenta de geração de relatórios visualizado que lhe permite monitorizar as suas aplicações rapidamente único.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: fa5560c008270635d0d93b8004e017c810c9c27a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074423"
---
# <a name="application-dashboard"></a>Dashboard de aplicações
O dashboard de aplicações permite-lhe monitorizar a sua aplicação única rapidamente. O **Dashboard** apresenta quando abre uma aplicação ao clicar no nome de aplicação no **as minhas aplicações** página em seguida, selecione **Dashboard** do painel superior. 

> [!CAUTION]
> Se pretender que as métricas mais atualizadas para LUIS, terá de:
> * Utilizar um LUIS [chave de ponto final](luis-how-to-azure-subscription.md) criada no Azure
> * Chave de ponto final de LUIS de utilização para todos os pedidos de ponto de extremidade incluindo LUIS [API](https://aka.ms/luis-endpoint-apis) e bot
> * Utilizar a chave de ponto de extremidade diferente para cada aplicação LUIS. Não utilize uma chave de ponto final único para todas as aplicações. Chave do ponto final é controlado ao nível da chave, não ao nível da aplicação.  

O **Dashboard** página proporciona-lhe uma descrição geral da aplicação LUIS, incluindo o modelo atual Estado, bem como [endpoint](luis-glossary.md#endpoint) utilização ao longo do tempo. 
  
## <a name="app-status"></a>Estado da aplicação
O dashboard apresenta o treinamento do aplicativo e da publicação Estado, incluindo a data e hora quando a aplicação foi última treinados e publicado.  

![Dashboard - estado da aplicação](./media/luis-how-to-use-dashboard/app-state.png)

## <a name="model-data-statistics"></a>Estatísticas de dados do modelo
O dashboard apresenta o número total de objetivos, entidades e expressões com etiquetadas existente na aplicação. 

![Estatísticas de dados da aplicação](./media/luis-how-to-use-dashboard/app-model-count.png)

## <a name="endpoint-hits"></a>Acertos de ponto final
O dashboard apresenta as visitas de ponto de extremidade total que a aplicação do LUIS recebe e permite que exiba atinge dentro de um período que especifica. O número total de acertos apresentada é a soma de acertos de ponto de extremidade que utilize um [chave de ponto final](./luis-concept-keys.md#endpoint-key) e o ponto final atinge que utilizam um [chave de criação de conteúdos](./luis-concept-keys.md#authoring-key).

![Acertos de ponto final](./media/luis-how-to-use-dashboard/dashboard-endpointhits.png)

> [!NOTE] 
> Contagem de acessos de ponto de extremidade mais atual está no portal do Azure na descrição geral do serviço do LUIS. 
 
### <a name="total-endpoint-hits"></a>Acertos de ponto de extremidade total
O número total de acertos de ponto final recebido para a sua aplicação, desde a criação de aplicações até a data atual.

### <a name="endpoint-hits-per-period"></a>Acertos de ponto final por período
O número de resultados recebida dentro de um período anterior, apresentados por dia. Os pontos entre as datas de início e de fim representam os dias cair neste período. Passar o ponteiro do mouse sobre cada ponto para ver os pedidos com êxito na contagem em todos os dias durante o período. 

Para selecionar um período para ver no gráfico:
 
1. Clique em **definições adicionais** ![botão de configurações adicionais](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) aceder à lista de períodos. Pode selecionar desde uma semana até um ano, cópia de segurança de períodos. 

    ![Ponto final de acertos por período](./media/luis-how-to-use-dashboard/timerange.png)

2. Selecione um período da lista e, em seguida, clique na seta para trás ![Seta para trás](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) para apresentar o gráfico.

### <a name="key-usage"></a>Utilização de chaves
O número de acertos consumidos a partir de chave de ponto final do aplicativo. Para obter mais informações sobre chaves de ponto final, consulte [chaves de LUIS](luis-concept-keys.md). 
  
## <a name="intent-breakdown"></a>Divisão de intenção
O **análise detalhada de intenção** apresenta uma divisão dos objetivos com base em expressões com etiquetadas ou acertos de ponto final. Este gráfico de resumo mostra a importância relativa de cada objetivo na aplicação. Ao pairar o ponteiro do mouse sobre um setor, vê o nome de intenção e o representa a percentagem da contagem total de acertos de expressões com etiquetadas/ponto de extremidade. 

![Divisão de intenção](./media/luis-how-to-use-dashboard/intent-breakdown.png)

Para controlar se a divisão se baseia em expressões com etiquetadas ou acertos de ponto final:

1. Clique em **definições adicionais** ![botão de configurações adicionais](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) aceder à lista conforme a imagem seguinte:

    ![Lista de divisão de intenção](./media/luis-how-to-use-dashboard/intent-breakdown-based-on.png)
2. Selecione um valor na lista e, em seguida, clique na seta para trás ![Seta para trás](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) para apresentar o gráfico.

## <a name="entity-breakdown"></a>Divisão de entidade
O dashboard mostra uma análise detalhada das entidades com base em expressões com etiquetadas ou acertos de ponto final. Este gráfico de resumo mostra a importância relativa de cada entidade na aplicação. Ao pairar o ponteiro do mouse sobre um setor, vê o nome da entidade e a percentagem de acertos de expressões com etiquetadas/ponto de extremidade. 

![Divisão de entidade](./media/luis-how-to-use-dashboard/entity-breakdown.png)

Para controlar se a divisão se baseia em expressões com etiquetadas ou acertos de ponto final:

1. Clique em **definições adicionais** ![botão de configurações adicionais](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) aceder à lista conforme a imagem seguinte:

    ![Lista de divisão de entidades](./media/luis-how-to-use-dashboard/entity-breakdown-based-on.png)
2. Selecione um valor na lista e, em seguida, clique na seta para trás ![Seta para trás](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) para apresentar o gráfico em conformidade.
