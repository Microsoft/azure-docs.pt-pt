---
title: Melhores práticas para a utilização do Detetor de Anomalias API
titleSuffix: Azure Cognitive Services
description: As melhores práticas para a utilização do Detetor de Anomalias API's para aplicar deteção de anomalias nos dados da série de tempo.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: deteção de anomalias, aprendizagem automática, algoritmos
ms.openlocfilehash: 7de25b4a099c706c05b32b52492096923033f822
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315709"
---
# <a name="multivariate-time-series-anomaly-detector-best-practices"></a>As melhores práticas do Detetor de Anomalias da Série Temporal Multivariate

Este artigo fornecerá orientações em torno das práticas recomendadas a seguir quando utilizar as APIs do Detetor de Anomalias multivariadas.

## <a name="how-to-prepare-data-for-training"></a>Como preparar dados para a formação

Para usar as APIs multivariadas do Detetor de Anomalias, precisamos de treinar o nosso próprio modelo antes de usarmos a deteção. Os dados utilizados para o treino são um lote de séries de tempo, cada série de tempo deve estar em formato CSV com duas colunas, relógio e valor. Todas as séries horadas devem ser fechadas num ficheiro zip e enviadas para o armazenamento da Azure Blob. Por predefinição, o nome do ficheiro será utilizado para representar a variável para a série de tempo. Alternativamente, um meta.jsextra no ficheiro pode ser incluído no ficheiro zip se desejar que o nome da variável seja diferente do nome de ficheiro .zip. Assim que gerarmos um [URL BLOB SAS (Assinaturas de acesso Partilhado), podemos](../../../storage/common/storage-sas-overview.md)usá-lo para treinar.

## <a name="data-quality-and-quantity"></a>Qualidade e quantidade de dados

A API multivariada de detetor de anomalias utiliza redes neuronais profundas de última geração para aprender padrões normais a partir de dados históricos e prevê se os valores futuros são anomalias. A qualidade e a quantidade de dados de formação são importantes para formar um modelo ideal. À medida que o modelo aprende padrões normais a partir de dados históricos, os dados de formação devem representar o estado normal geral do sistema. É difícil para o modelo aprender este tipo de padrões se os dados de treino estiverem cheios de anomalias. Além disso, o modelo tem milhões de parâmetros e precisa de um número mínimo de pontos de dados para aprender um conjunto ideal de parâmetros. A regra geral é que você precisa fornecer pelo menos 15.000 pontos de dados por variável para treinar corretamente o modelo. Quanto mais dados, melhor o modelo.

É comum que muitas séries de tempo tenham valores em falta, o que pode afetar o desempenho de modelos treinados. O rácio em falta de cada série de tempo deve ser controlado com um valor razoável. Uma série de tempo com 90% de valores em falta fornece pouca informação sobre padrões normais do sistema. Pior ainda, o modelo pode considerar os valores preenchidos como padrões normais, que são normalmente segmentos retos ou valores constantes. Quando novos dados fluem, os dados podem ser detetados como anomalias.

Um limiar de valor em falta recomendado é de 20%, mas um limiar mais elevado pode ser aceitável em algumas circunstâncias. Por exemplo, se tiver uma série de tempo com granularidade de um minuto e outra série de tempo com granularidade horária.  A cada hora há 60 pontos de dados por minuto de dados e 1 ponto de dados para dados de hora a hora, o que significa que o rácio em falta para os dados horários é de 98,33%. No entanto, é bom preencher os dados horários com o único valor se as séries horárias não flutuarem muito.

## <a name="parameters"></a>Parâmetros

### <a name="sliding-window"></a>Janela deslizante

A deteção de anomalias multivariadas requer um segmento de pontos de comprimento como `slidingWindow` entrada e decide se o próximo ponto de dados é uma anomalia. Quanto maior for o comprimento da amostra, mais dados serão considerados para uma decisão. Deve ter em mente duas coisas ao escolher um valor adequado `slidingWindow` para: propriedades de dados de entrada, e a compensação entre o tempo de treino/inferência e a melhoria potencial do desempenho. `slidingWindow` consiste num inteiro entre 28 e 2880. Pode decidir quantos pontos de dados são utilizados como entradas com base no facto de os seus dados serem periódicos e a taxa de amostragem dos seus dados.

Quando os seus dados são periódicos, pode incluir 1 - 3 ciclos como entrada e quando os seus dados são recolhidos a uma alta frequência (pequena granularidade) como dados de nível de minuto ou segundo nível, pode selecionar mais dados como uma entrada. Outra questão é que os inputs mais longos podem causar um tempo de treino/inferência mais longo, e não há garantias de que mais pontos de entrada conduzam a ganhos de desempenho. Enquanto poucos pontos de dados podem dificultar a convergência do modelo para uma solução ideal. Por exemplo, é difícil detetar anomalias quando os dados de entrada têm apenas dois pontos.

### <a name="align-mode"></a>Modo de alinhamento

O parâmetro `alignMode` é utilizado para indicar como pretende alinhar séries de tempo múltiplas em selos temporais. Isto porque muitas séries de tempo têm valores em falta e precisamos de os alinhar nos selos de tempo simultâneos antes de serem processados. Existem duas opções para este parâmetro, `inner join` `outer join` e. `inner join` significa que reportaremos os resultados de deteção em selos temporais em que **cada série de tempo** tem um valor, enquanto significa que `outer join` reportaremos resultados de deteção em selos temporais para **qualquer série de tempo** que tenha um valor.  **O `alignMode` testamento também irá afetar a sequência de entrada do modelo**, por isso escolha um adequado para o seu `alignMode` cenário, pois os resultados podem ser significativamente diferentes.

Aqui mostramos um exemplo para explicar `alignModel` diferentes valores.

#### <a name="series1"></a>Série1

|carimbo de data/hora | valor|
----------| -----|
|`2020-11-01`| 1  
|`2020-11-02`| 2  
|`2020-11-04`| 4  
|`2020-11-05`| 5

#### <a name="series2"></a>Série2

carimbo de data/hora | valor  
--------- | -
`2020-11-01`| 1  
`2020-11-02`| 2  
`2020-11-03`| 3  
`2020-11-04`| 4

#### <a name="inner-join-two-series"></a>Interior juntar duas séries
  
carimbo de data/hora | Série1 | Série2
----------| - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-04`| 4 | 4

#### <a name="outer-join-two-series"></a>Outer join duas séries

carimbo de data/hora | série1 | série2
--------- | - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-03`| ND | 3
`2020-11-04`| 4 | 4
`2020-11-05`| 5 | ND

### <a name="fill-not-available-na"></a>Preencha não disponível (NA)

Depois de as variáveis serem alinhadas na hora da adição por união exterior, pode haver algum `Not Available` `NA` valor em algumas das variáveis. Pode especificar o método para preencher este valor NA. As opções para o `fillNAMethod` `Linear` `Previous` são, `Subsequent` , , e  `Zero` `Fixed` .

| Opção     | Método                                                                                           |
| ---------- | -------------------------------------------------------------------------------------------------|
| Linear     | Preencha os valores DE NA por interpolação linear                                                           |
| Anterior   | Propagar o último valor válido para colmatar lacunas. Exemplo: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 2, 3, 3, 4]` |
| Posterior | Utilize o próximo valor válido para colmatar lacunas. Exemplo: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 3, 3, 4, 4]`       |
| Zero       | Preencha os valores de NA com 0.                                                                           |
| Fixo      | Preencha os valores de NA com um valor válido especificado que deve ser fornecido em `paddingValue` .          |

## <a name="model-analysis"></a>Análise de modelos

### <a name="training-latency"></a>Latência de formação

O treino de deteção de anomalias multivariadas pode ser demorado. Especialmente quando se tem uma grande quantidade de timetamps usados para o treino. Por isso, permitimos que parte do processo de formação seja assíncronos. Normalmente, os utilizadores submetem a tarefa do comboio através do Modelo de Comboio API. Em seguida, obtenha o estado do modelo através do `Get Multivariate Model API` . Aqui demonstramos como extrair o tempo restante antes do treino terminar. Na resposta get multivariate model API, há um item chamado `diagnosticsInfo` . Neste item, há um `modelState` elemento. Para calcular o tempo restante, precisamos usar `epochIds` e `latenciesInSeconds` . Uma época representa um ciclo completo através dos dados de treino. A cada 10 épocas, vamos obter informações sobre o estado da produção. No total, vamos treinar durante 100 épocas, a latência indica quanto tempo uma época demora. Com esta informação, sabemos que resta tempo para treinar o modelo.

### <a name="model-performance"></a>Desempenho do modelo

Deteção de Anomalias Multivariadas, como modelo sem supervisão. A melhor forma de o avaliar é verificar manualmente os resultados da anomalia. Na resposta get multivariate Model, fornecemos algumas informações básicas para analisarmos o desempenho do modelo. No `modelState` elemento devolvido pela API do Modelo Multivariado Get, podemos utilizar `trainLosses` e avaliar se o modelo foi treinado como `validationLosses` esperado. Na maioria dos casos, as duas perdas diminuirão gradualmente. Outra informação para analisarmos o desempenho do modelo `variableStates` contra. A lista de estado de variáveis é classificada por `filledNARatio` ordem descendente. Quanto maior for o nosso desempenho, normalmente temos de reduzir isto `NA ratio` o máximo possível. `NA` pode ser causado por valores em falta ou variáveis desalinhadas de uma perspetiva de tempotamp.

## <a name="next-steps"></a>Passos seguintes

- [Quickstarts](../quickstarts/client-libraries-multivariate.md).
- [Saiba mais sobre os algoritmos subjacentes que alimentam o Detetor de Anomalias Multivariate](https://arxiv.org/abs/2009.02040)