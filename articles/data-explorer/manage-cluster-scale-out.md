---
title: Dimensionar cluster do Explorador de dados do Azure para acomodar a pedido de alteração
description: Este artigo descreve os passos para aumentar e reduzir horizontalmente um cluster do Explorador de dados do Azure com base no pedido de alteração.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 8d369800bdc9817856eeb7039fe47e5b870735ba
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107377"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Gerir o cluster aumentar horizontalmente para acomodar a pedido de alteração

Dimensionar um cluster adequadamente é essencial para o desempenho do Explorador de dados do Azure. Mas a pedido num cluster não é possível prever com precisão absoluto. Um tamanho de cluster estático pode levar a subutilização ou utilização, nenhum dos quais é ideal.

Uma abordagem melhor é *dimensionamento* um cluster, adicionar e remover a capacidade com o pedido de alteração. Existem dois fluxos de trabalho de dimensionamento: vertical e horizontal. Este artigo explica o fluxo de trabalho de escalamento horizontal.

Este artigo mostra como gerir o cluster Escalamento horizontal, também conhecido como o dimensionamento automático. Dimensionamento automático permite-lhe aumentar horizontalmente o número de instâncias automaticamente com base em regras predefinidas e as agendas. Especifica as definições de dimensionamento automático do seu cluster no portal do Azure, conforme descrito neste artigo.

Aceda ao seu cluster. Sob **configurações**, selecione **aumentar horizontalmente**. Sob **configurar**, selecione **ativar o dimensionamento automático**.

![Ativar dimensionamento automático](media/manage-cluster-scaling/enable-autoscale.png)

O gráfico seguinte mostra o fluxo de passos seguintes. Obter mais detalhes estão abaixo do elemento gráfico.

![Regra de dimensionamento](media/manage-cluster-scaling/scale-rule.png)

1. Na **nome da definição de dimensionamento automático** caixa, indique um nome, tal como *Escalamento horizontal: colocar em cache utilização*.

1. Para **modo de dimensionamento**, selecione **dimensionam com base numa métrica**. Esse modo oferecerá dimensionamento dinâmico. Também pode selecionar **Dimensionar para uma contagem de instâncias específica**.

1. Selecione **+ adicionar uma regra**.

1. Na **regra de dimensionamento** secção à direita, fornecer valores para cada definição.

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

    **Ação**

    | Definição | Valor e descrição |
    | --- | --- |
    | **Operação** | Escolha a opção adequada para reduzir horizontalmente ou aumentar horizontalmente. |
    | **Contagem de instâncias** | Escolha o número de nós ou instâncias que pretende adicionar ou remover quando for cumprida uma condição de métrica. |
    | **Repouso (minutos)** | Escolha um intervalo de tempo adequado a aguardar entre as operações de dimensionamento. Comece com o padrão de cinco minutos. |
    |  |  |

1. Selecione **Adicionar**.

1. Na **limites de instância** secção no lado esquerdo, fornecer valores para cada definição.

    | Definição | Valor e descrição |
    | --- | --- |
    | **Mínimo** | O número de instâncias que o cluster não serão dimensionados abaixo, independentemente da utilização. |
    | **Máximo** | O número de instâncias que o cluster não serão dimensionados acima, independentemente da utilização. |
    | **Predefinição** | O número predefinido de instâncias. Esta definição é utilizada se existirem problemas com as métricas de recurso de leitura. |
    |  |  |

1. Selecione **Guardar**.

Agora que configurou uma operação de escalamento horizontal para o seu cluster do Explorador de dados do Azure. Adicione outra regra de uma operação de dimensionamento. Esta configuração permite que o cluster dimensionar dinamicamente com base nas métricas que especificar.

Também pode [gerir o aumento vertical de cluster](manage-cluster-scale-up.md) para o dimensionamento apropriado de um cluster.

Se precisar de assistência com problemas de dimensionamento de clusters, [abra um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) no portal do Azure.
