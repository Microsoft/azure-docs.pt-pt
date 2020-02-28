---
title: Gerir a escala horizontal do cluster (escala para fora) para corresponder à procura no Azure Data Explorer
description: Este artigo descreve passos para escalar e escalar num cluster azure Data Explorer baseado na mudança da procura.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: ff7420619cffc2287ab8ff6332df605d56329549
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664138"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Gerir a escala horizontal do cluster (escala para fora) no Azure Data Explorer para acomodar a mudança da procura

Dimensionar um cluster adequadamente é fundamental para o desempenho do Azure Data Explorer. Um tamanho de cluster estático pode levar à subutilização ou à utilização excessiva, nenhuma das quais é ideal. Como a procura num cluster não pode ser prevista com precisão absoluta, é melhor *escalar* um cluster, adicionando e removendo a capacidade e os recursos da CPU com a mudança da procura. 

Existem dois fluxos de trabalho para escalar um cluster Azure Data Explorer: 
* Escala horizontal, também chamada de escala dentro e fora.
* [Escala vertical,](manage-cluster-vertical-scaling.md)também chamada de escala para cima e para baixo.
Este artigo explica o fluxo de trabalho de escala horizontal.

## <a name="configure-horizontal-scaling"></a>Configurar o dimensionamento horizontal

Ao utilizar a escala horizontal, pode escalar a contagem de ocorrências automaticamente, com base em regras e horários pré-definidos. Para especificar as definições de escala automática para o seu cluster:

1. No portal Azure, aceda ao seu recurso de cluster Azure Data Explorer. Em **Definições,** **selecione Scale out**. 

2. Na janela **Scale out,** selecione o método de escala automática que deseja: **Escala manual,** **escala automática otimizada**ou **escala automática personalizada**.

### <a name="manual-scale"></a>Escala manual

A escala manual é a definição padrão durante a criação do cluster. O cluster tem uma capacidade estática que não muda automaticamente. Selecione a capacidade estática utilizando a barra de **contagem de instâncias.** A escala do cluster permanece nesse cenário até que faça outra mudança.

   ![Método de escala manual](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale-preview"></a>Escala automática otimizada (pré-visualização)

Escala automática otimizada é o método de escala automática recomendado. Este método otimiza o desempenho do cluster e os custos. Se o cluster se aproximar de um estado de subutilização, será escalado. Esta ação reduz os custos, mas mantém o nível de desempenho. Se o cluster se aproximar de um estado de sobreutilização, será dimensionado para manter o desempenho ideal. Para configurar a escala automática otimizada:

1. Selecione **escala automática otimizada.** 

1. Selecione uma contagem mínima de instância e uma contagem máxima de instância. A escala automática do cluster varia entre estes dois números, com base na carga.

1. Selecione **Guardar**.

   ![Método de escala automática otimizado](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

A escala automática otimizada começa a funcionar. As suas ações são agora visíveis no registo de atividade do Azure do cluster.

#### <a name="logic-of-optimized-autoscale"></a>Lógica da escala automática otimizada 

**Aumentar horizontalmente**

Quando o seu cluster se aproximar de um estado de sobreutilização, estem para manter o desempenho ideal. A escala para fora ocorrerá quando:
* O número de casos de cluster é inferior ao número máximo de instâncias definidas pelo utilizador.
* A utilização da cache é alta por mais de uma hora.
* A CPU está alta há mais de uma hora.
* A utilização da ingestão é alta por mais de uma hora.

> [!NOTE]
> A lógica de escala não considera atualmente a métrica de utilização da ingestão. Se esta métrica for importante para a sua caixa de utilização, utilize a [escala automática personalizada](#custom-autoscale).

**Escala em**

Quando o seu cluster se aproxima de um estado de subutilização, dimensione para custos mais baixos, mas mantenha o desempenho. Várias métricas são usadas para verificar se é seguro escalar no cluster. As seguintes regras são avaliadas de hora em hora durante 6 horas antes da escala:
* O número de ocorrências é superior a 2 e acima do número mínimo de ocorrências definidas.
* Para garantir que não há sobrecarga de recursos, as seguintes métricas devem ser verificadas antes da escala: 
    * A utilização da cache não é alta.
    * CpU está abaixo da média 
    * A utilização da ingestão está abaixo da média 
    * A utilização da ingestão de streaming (se for utilizada a ingestão de streaming) não é elevada
    * Manter os eventos vivos estão acima de um mínimo definido, processado corretamente, e a tempo.
    * Sem estrangulamento de consulta 
    * O número de consultas falhadas está abaixo de um mínimo definido.

> [!NOTE]
> A escala lógica requer atualmente uma avaliação de 7 dias antes da implementação da escala otimizada. Esta avaliação ocorre uma vez a cada 24 horas. Se for necessária uma mudança rápida, utilize a [balança manual](#manual-scale).

### <a name="custom-autoscale"></a>Escala automática personalizada

Ao utilizar a escala automática personalizada, pode escalar o seu cluster dinamicamente com base em métricas que especifica. O gráfico seguinte mostra o fluxo e os passos para configurar a escala automática personalizada. Mais detalhes seguem o gráfico.

1. Na caixa de definição de **escala automática,** introduza um nome, como *Scale-out: utilização*de cache . 

   ![Regra da escala](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. Para **o modo Escala,** selecione **Escala com base numa métrica**. Este modo proporciona uma escala dinâmica. Também pode selecionar **Escala para uma contagem de instâncias específica**.

3. Selecione **+ Adicione uma regra**.

4. Na secção **de regra de escala** à direita, introduza valores para cada definição.

    **Critérios**

    | Definição | Valor e descrição |
    | --- | --- |
    | **Agregação do tempo** | Selecione um critério de agregação, como a **Média**. |
    | **Nome métrico** | Selecione a métrica em que pretende que a operação de escala se baseie, como a **Utilização**da Cache . |
    | **Estatística do grão do tempo** | Escolha entre **média,** **mínima,** **máxima**e **soma.** |
    | **Operador** | Escolha a opção adequada, tal como **Maior ou igual a**. |
    | **Limiar** | Escolha um valor apropriado. Por exemplo, para a utilização de cache, 80 por cento é um bom ponto de partida. |
    | **Duração (em minutos)** | Escolha uma quantidade adequada de tempo para o sistema olhar para trás ao calcular as métricas. Comece com o padrão de 10 minutos. |
    |  |  |

    **Ação**

    | Definição | Valor e descrição |
    | --- | --- |
    | **Operação** | Escolha a opção adequada para escalar ou escalar para fora. |
    | **Contagem de exemplos** | Escolha o número de nós ou instâncias que pretende adicionar ou remover quando uma condição métrica é satisfeita. |
    | **Arrefecer (minutos)** | Escolha um intervalo de tempo adequado para esperar entre operações de escala. Comece com o padrão de cinco minutos. |
    |  |  |

5. Selecione **Adicionar**.

6. Na secção **de limites** por exemplo à esquerda, introduza valores para cada definição.

    | Definição | Valor e descrição |
    | --- | --- |
    | **Mínimo** | O número de casos que o seu cluster não escala abaixo, independentemente da utilização. |
    | **Máximo** | O número de casos que o seu cluster não escala acima, independentemente da utilização. |
    | **Predefinição** | O número padrão de casos. Esta definição é utilizada se houver problemas de leitura das métricas de recursos. |
    |  |  |

7. Selecione **Guardar**.

Configurao escala horizontal para o seu cluster Azure Data Explorer. Adicione outra regra para escala vertical. Se precisar de assistência com problemas de escala de cluster, [abra um pedido](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) de apoio no portal Azure.

## <a name="next-steps"></a>Passos seguintes

* [Monitor Azure Data Explorer desempenho, saúde e uso com métricas](using-metrics.md)
* [Gerir a escala vertical](manage-cluster-vertical-scaling.md) do cluster para dimensionamento adequado de um cluster.
