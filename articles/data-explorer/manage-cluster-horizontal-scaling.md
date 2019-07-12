---
title: Aumentar horizontalmente um cluster do Explorador de dados do Azure
description: Este artigo descreve os passos para aumentar e reduzir horizontalmente um cluster do Explorador de dados do Azure com base no pedido de alteração.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 29bfcc42462a667850f0b2e1bbda3d29cd1597ab
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571520"
---
# <a name="manage-cluster-horizontal-scaling-to-accommodate-changing-demand"></a>Gerir o cluster dimensionamento horizontal para acomodar a pedido de alteração

Dimensionar um cluster adequadamente é essencial para o desempenho do Explorador de dados do Azure. Mas a pedido num cluster não é possível prever com precisão absoluto. Um tamanho de cluster estático pode levar a subutilização ou utilização, nenhum dos quais é ideal.

Uma abordagem melhor é *dimensionamento* um cluster, adicionar e remover a capacidade com o pedido de alteração. Existem dois fluxos de trabalho de dimensionamento: 
* Dimensionamento horizontal, também denominado aumento e redução horizontal na.
* Dimensionamento vertical, também denominado aumentar e reduzir verticalmente.

Este artigo explica o fluxo de trabalho de dimensionamento horizontal.

Dimensionamento horizontal permite-lhe dimensionar a contagem de instâncias automaticamente com base em regras predefinidas e as agendas. Especifica as definições de dimensionamento automático do seu cluster no portal do Azure, conforme descrito neste artigo.

## <a name="steps-to-configure-horizontal-scaling"></a>Passos para configurar o dimensionamento horizontal

No portal do Azure, aceda ao seu recurso de cluster do Data Explorer. Sob o **configurações** cabeçalho, selecione **aumentar horizontalmente**. 

Selecione o método de dimensionamento automático pretendido: **Dimensionamento manual**, **com otimização de dimensionamento automático** ou **dimensionamento automático de personalizado**.

### <a name="manual-scale"></a>Dimensionamento manual

Dimensionamento manual é a predefinição com a criação do cluster. Isso significa que o cluster tem uma capacidade de cluster estático que não será alterado automaticamente. Pode escolher a capacidade de estática usando a barra e não mudará até que a próxima vez que irá alterar a definição de dimensionamento do cluster.

   ![Método de dimensionamento manual](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale"></a>Dimensionamento automático não otimizado

Dimensionamento automático otimizado é o método de dimensionamento automático recomendada. Passos para configurar o dimensionamento automático de otimizado:

1. Selecionado a opção de dimensionamento automático de otimizado e escolha um limite inferior e um limite superior para a quantidade de instâncias do cluster, o dimensionamento automático será feito entre esses limites.
2. Clique em Guardar.

   ![Método de dimensionamento automático não otimizada](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Depois de clicar em Guardar otimizado mecanismo de dimensionamento automático irá começar a trabalhar e é ações serão visíveis no registo de atividades do cluster. Este método de dimensionamento automático é otimizar o desempenho do cluster e os custos: se o cluster será começam a ficar para um Estado de subutilização que irá ser dimensionado-in que deixar os custos do mesmo e inferiores de desempenho, e se o cluster irá começar a obter um Estado de utilização, é ampliada para se certificar de que está a funcionar bem

### <a name="custom-autoscale"></a>Dimensionamento automático personalizado

Método de dimensionamento automático personalizado permite-lhe dimensionar o seu cluster dinamicamente com base nas métricas que especificar. O gráfico seguinte mostra o fluxo e os passos para configurar o dimensionamento automático de personalizado. Obter mais detalhes, siga o elemento gráfico.

1. Na **nome da definição de dimensionamento automático** caixa, indique um nome, tal como *Escalamento horizontal: colocar em cache utilização*. 

   ![Regra de dimensionamento](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. Para **modo de dimensionamento**, selecione **dimensionam com base numa métrica**. Esse modo oferecerá dimensionamento dinâmico. Também pode selecionar **Dimensionar para uma contagem de instâncias específica**.

3. Selecione **+ adicionar uma regra**.

4. Na **regra de dimensionamento** secção à direita, fornecer valores para cada definição.

    **Critérios**

    | Definição | Valor e descrição |
    | --- | --- |
    | **Agregação de tempo** | Selecione um critério de agregação, como **média**. |
    | **Nome da métrica** | Selecione a métrica que pretende que a operação de dimensionamento seja baseada no, tal como **utilização da Cache**. |
    | **Estatística de intervalo de agregação de tempo** | Escolha entre **médio**, **mínimo**, **máxima**, e **soma**. |
    | **Operador** | Escolha a opção adequada, tais como **maior que ou igual a**. |
    | **Limiar** | Escolha um valor adequado. Por exemplo, para utilização de cache, 80 por cento é um ponto de partida. |
    | **Duração (em minutos)** | Escolha uma quantidade apropriada de tempo para o sistema consultar novamente quando o cálculo de métricas. Comece com o padrão de 10 minutos. |
    |  |  |

    **ação**

    | Definição | Valor e descrição |
    | --- | --- |
    | **Operação** | Escolha a opção adequada para reduzir horizontalmente ou aumentar horizontalmente. |
    | **Contagem de instâncias** | Escolha o número de nós ou instâncias que pretende adicionar ou remover quando for cumprida uma condição de métrica. |
    | **Repouso (minutos)** | Escolha um intervalo de tempo adequado a aguardar entre as operações de dimensionamento. Comece com o padrão de cinco minutos. |
    |  |  |

5. Selecione **Adicionar**.

6. Na **limites de instância** secção no lado esquerdo, fornecer valores para cada definição.

    | Definição | Valor e descrição |
    | --- | --- |
    | **Mínimo** | O número de instâncias que o cluster não serão dimensionados abaixo, independentemente da utilização. |
    | **Máximo** | O número de instâncias que o cluster não serão dimensionados acima, independentemente da utilização. |
    | **Predefinição** | O número predefinido de instâncias. Esta definição é utilizada se existirem problemas com as métricas de recurso de leitura. |
    |  |  |

7. Selecione **Guardar**.

Agora que configurou uma operação de escalamento horizontal para o seu cluster do Explorador de dados do Azure. Adicione outra regra de uma operação de dimensionamento. Se precisar de assistência com problemas de dimensionamento de clusters, [abra um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) no portal do Azure.

## <a name="next-steps"></a>Passos Seguintes

* [Monitorizar o desempenho, a Estado de funcionamento e a utilização com métricas de Explorador de dados do Azure](using-metrics.md)
* [Gerir o dimensionamento vertical de cluster](manage-cluster-vertical-scaling.md) para o dimensionamento apropriado de um cluster.
