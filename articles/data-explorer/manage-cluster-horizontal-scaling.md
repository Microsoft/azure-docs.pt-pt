---
title: Gerenciar o dimensionamento horizontal do cluster (scale out) no Azure Data Explorer para acomodar a demanda em alteração
description: Este artigo descreve as etapas para escalar horizontalmente e reduzir em um cluster Data Explorer do Azure com base na demanda em alteração.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 70e6bdfcf9718244632ad02e09d3ddadee71a617
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311562"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Gerenciar o dimensionamento horizontal do cluster (scale out) no Azure Data Explorer para acomodar a demanda em alteração

O dimensionamento adequado de um cluster é essencial para o desempenho do Azure Data Explorer. Um tamanho de cluster estático pode levar à utilização ou utilização excessiva, e nenhuma delas é ideal.

Como a demanda em um cluster não pode ser prevista com precisão absoluta, é melhor *dimensionar* um cluster, adicionar e remover recursos de capacidade e CPU com alteração de demanda. 

Há dois fluxos de trabalho para dimensionar um cluster de Data Explorer do Azure: 

* Dimensionamento horizontal, também chamado de expansão e saída.
* [Dimensionamento vertical](manage-cluster-vertical-scaling.md), também chamado de expansão e redução.

Este artigo explica o fluxo de trabalho de dimensionamento horizontal.

## <a name="configure-horizontal-scaling"></a>Configurar o dimensionamento horizontal

Usando o dimensionamento horizontal, você pode dimensionar a contagem de instâncias automaticamente, com base em regras e agendamentos predefinidos. Para especificar as configurações de dimensionamento automático para o cluster:

1. Na portal do Azure, vá para o recurso de cluster de Data Explorer do Azure. Em **configurações**, selecione **escalar horizontalmente**. 

2. Na janela **escalar horizontalmente** , selecione o método de dimensionamento automático desejado: **Escala manual**, **dimensionamento automático otimizado**ou **dimensionamento automático personalizado**.

### <a name="manual-scale"></a>Escala manual

A escala manual é a configuração padrão durante a criação do cluster. O cluster tem uma capacidade estática que não é alterada automaticamente. Você seleciona a capacidade estática usando a barra de **contagem de instâncias** . O dimensionamento do cluster permanece nessa configuração até que você faça outra alteração.

   ![Método de dimensionamento manual](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale"></a>Dimensionamento automático otimizado

Dimensionamento automático otimizado é o método de dimensionamento automático recomendado. Esse método otimiza o desempenho e os custos do cluster. Se o cluster se aproximar de um estado de subutilização, ele será dimensionado no. Essa ação reduz os custos, mas mantém o nível de desempenho. Se o cluster se aproximar de um estado de excesso de utilização, ele será escalado horizontalmente para manter o desempenho ideal. Para configurar o dimensionamento automático otimizado:

1. Selecione **dimensionamento automático otimizado**. 

1. Selecione uma contagem de instâncias mínima e uma contagem máxima de instâncias. O dimensionamento automático do cluster varia entre esses dois números, com base na carga.

1. Selecione **Guardar**.

   ![Método de dimensionamento automático otimizado](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

O dimensionamento automático otimizado começa a funcionar. Suas ações agora estão visíveis no log de atividades do Azure do cluster.

### <a name="custom-autoscale"></a>Dimensionamento automático personalizado

Usando o dimensionamento automático personalizado, você pode dimensionar seu cluster dinamicamente com base nas métricas que você especificar. O gráfico a seguir mostra o fluxo e as etapas para configurar o dimensionamento automático personalizado. Para obter mais detalhes, siga o elemento gráfico.

1. Na caixa **nome da configuração de dimensionamento automático** , insira um nome, como *escala horizontal: utilização de cache*. 

   ![Regra de escala](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. Para o **modo de escala**, selecione **escala com base em uma métrica**. Esse modo fornece dimensionamento dinâmico. Você também pode selecionar **Dimensionar para uma contagem de instâncias específica**.

3. Selecione **+ Adicionar uma regra**.

4. Na seção **regra** de dimensionamento à direita, insira valores para cada configuração.

    **Critérios**

    | Definição | Valor e descrição |
    | --- | --- |
    | **Agregação de tempo** | Selecione um critério de agregação, como **Average**. |
    | **Nome da métrica** | Selecione a métrica na qual você deseja que a operação de dimensionamento se baseie, como **utilização de cache**. |
    | **Estatística de intervalo de tempo** | Escolha entre **média**, **mínimo**, **máximo**e **soma**. |
    | **Operador** | Escolha a opção apropriada, como **maior ou igual a**. |
    | **Os** | Escolha um valor apropriado. Por exemplo, para a utilização de cache, 80% é um bom ponto de partida. |
    | **Duração (em minutos)** | Escolha uma quantidade apropriada de tempo para o sistema examinar ao calcular as métricas. Comece com o padrão de 10 minutos. |
    |  |  |

    **ação**

    | Definição | Valor e descrição |
    | --- | --- |
    | **Operação** | Escolha a opção apropriada para reduzir ou escalar horizontalmente. |
    | **Contagem de instâncias** | Escolha o número de nós ou instâncias que você deseja adicionar ou remover quando uma condição de métrica for atendida. |
    | **Resfriamento (minutos)** | Escolha um intervalo de tempo apropriado para aguardar entre as operações de escala. Comece com o padrão de cinco minutos. |
    |  |  |

5. Selecione **Adicionar**.

6. Na seção **limites da instância** à esquerda, insira valores para cada configuração.

    | Definição | Valor e descrição |
    | --- | --- |
    | **Máximo** | O número de instâncias que o cluster não dimensionará abaixo, independentemente da utilização. |
    | **Maior** | O número de instâncias que o cluster não dimensionará acima, independentemente da utilização. |
    | **Predefinição** | O número padrão de instâncias. Essa configuração será usada se houver problemas na leitura das métricas de recurso. |
    |  |  |

7. Selecione **Guardar**.

Agora você configurou o dimensionamento horizontal para o cluster de Data Explorer do Azure. Adicione outra regra para o dimensionamento vertical. Se você precisar de assistência com problemas de dimensionamento de cluster, [abra uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) no portal do Azure.

## <a name="next-steps"></a>Passos seguintes

* [Monitorar o desempenho, a integridade e o uso do Data Explorer do Azure com métricas](using-metrics.md)

* [Gerencie o dimensionamento vertical do cluster](manage-cluster-vertical-scaling.md) para o dimensionamento apropriado de um cluster.
