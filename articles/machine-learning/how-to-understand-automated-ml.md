---
title: Avaliar os resultados da experiência AutoML
titleSuffix: Azure Machine Learning
description: Saiba como ver e avaliar gráficos e métricas para cada uma das suas experiências automatizadas de aprendizagem automática de máquinas.
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/09/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, automl
ms.openlocfilehash: 2bed95385823a167c7a31eed11d752894984ea38
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791882"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Avaliar resultados automatizados de experiências de aprendizagem automática

Neste artigo, aprenda a avaliar e comparar modelos treinados pela sua experiência automatizada de aprendizagem automática de máquinas (ML automatizado). Ao longo de uma experiência automatizada de ML, muitas corridas são criadas e cada corrida cria um modelo. Para cada modelo, mL automatizado gera métricas de avaliação e gráficos que o ajudam a medir o desempenho do modelo. 

Por exemplo, ml automatizado gera os seguintes gráficos com base no tipo de experiência.

| Classificação| Regressão/previsão |
| ----------------------------------------------------------- | ---------------------------------------- |
| [A matriz de confusão](#confusion-matrix)                       | [Histogramas residuais](#residuals)        |
| [Curva característica de funcionamento do recetor (ROC)](#roc-curve) | [Previsto vs. verdadeiro](#predicted-vs-true) |
| [Curva de precisão (PR)](#precision-recall-curve)      |                                          |
| [Curva de elevação](#lift-curve)                                   |                                          |
| [Curva de ganhos cumulativos](#cumulative-gains-curve)           |                                          |
| [Curva de calibração](#calibration-curve)                     |                     


## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. (Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://aka.ms/AMLFree) antes de começar)
- Uma experiência de Aprendizagem automática Azure criada com:
  - O [estúdio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md) (sem necessidade de código)
  - [A máquina Azure aprendendo Python SDK](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>Ver resultados de execução

Após a conclusão da sua experiência automatizada de ML, uma história das corridas pode ser encontrada através de:
  - Um navegador com [estúdio Azure Machine Learning](overview-what-is-machine-learning-studio.md)
  - Um caderno Jupyter usando o [widget RunDetails Jupyter](/python/api/azureml-widgets/azureml.widgets.rundetails)

Os seguintes passos e vídeo, mostrar-lhe como ver a história da execução e as métricas e gráficos de avaliação de modelos no estúdio:

1. [Inscreva-se no estúdio](https://ml.azure.com/) e navegue para o seu espaço de trabalho.
1. No menu esquerdo, selecione **Experiments**.
1. Selecione a sua experiência na lista de experiências.
1. Na tabela na parte inferior da página, selecione uma execução automática de ML.
1. No separador **Modelos,** selecione o **nome Algoritmo** para o modelo que pretende avaliar.
1. No **separador Métricas,** utilize as caixas de verificação à esquerda para visualizar métricas e gráficos.

![Passos para ver métricas em estúdio](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>Métricas de classificação

ML automatizado calcula métricas de desempenho para cada modelo de classificação gerado para a sua experiência. Estas métricas baseiam-se na implementação do scikit learn. 

Muitas métricas de classificação são definidas para classificação binária em duas classes, e requerem uma média sobre as classes para produzir uma pontuação para classificação multi-classes. O Scikit-learn fornece vários métodos de média, três dos quais ml automatizado expõe: **macro,** **micro** e **ponderado**.

- **Macro** - Calcular a métrica para cada classe e tomar a média não ponderada
- **Micro** - Calcular a métrica globalmente contando os verdadeiros positivos totais, falsos negativos e falsos positivos (independentes das classes).
- **Ponderado** - Calcular a métrica para cada classe e tomar a média ponderada com base no número de amostras por classe.

Embora cada método de média tenha os seus benefícios, uma consideração comum ao selecionar o método adequado é o desequilíbrio de classe. Se as classes tiverem um número diferente de amostras, pode ser mais informativo utilizar uma média macro onde as classes minoritárias recebem igual ponderação para as classes maioritárias. Saiba mais sobre [métricas binárias vs multiclasse em ML automatizado](#binary-vs-multiclass-classification-metrics). 

A tabela seguinte resume as métricas de desempenho do modelo que o ML automatizado calcula para cada modelo de classificação gerado para a sua experiência. Para obter mais detalhes, consulte a documentação scikit-learn ligada no campo de **cálculo** de cada métrica. 

|Metric|Descrição|Cálculo|
|--|--|---|
|AUC | A AUC é a área sob a [curva característica de funcionamento](#roc-curve)do recetor.<br><br> **Objetivo:** Mais perto de 1, melhor <br> **Alcance:** [0, 1]<br> <br>Os nomes métricos suportados incluem, <li>`AUC_macro`, a média aritmética da AUC para cada classe.<li> `AUC_micro`, calculado combinando os verdadeiros positivos e falsos positivos de cada classe. <li> `AUC_weighted`, média aritmética da pontuação para cada classe, ponderada pelo número de casos verdadeiros em cada classe.<br><br>Nota: Os valores AUC reportados por ML automatizado podem não corresponder à tabela ROC se existirem apenas duas classes. Para a classificação binária, a implementação subjacente de scikit-learn da AUC não aplica efetivamente uma média macro/micro/ponderada. Em vez disso, a AUC da classe positiva mais provável é devolvida. O gráfico ROC continua a aplicar a média de classe para a classificação binária, tal como acontece com a multiclasse.  |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| Precisão é a relação de previsões que correspondem exatamente às verdadeiras etiquetas de classe. <br> <br>**Objetivo:** Mais perto de 1, melhor <br> **Alcance:** [0, 1]|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|A precisão média resume uma curva de precisão-recordação como a média ponderada de precisões alcançadas em cada limiar, com o aumento da recuperação do limiar anterior utilizado como peso. <br><br> **Objetivo:** Mais perto de 1, melhor <br> **Alcance:** [0, 1]<br> <br>Os nomes métricos suportados incluem,<li>`average_precision_score_macro`, a média aritmética da pontuação média de precisão de cada classe.<li> `average_precision_score_micro`, calculado combinando os verdadeiros positivos e falsos positivos em cada corte.<li>`average_precision_score_weighted`, a média aritmética da pontuação média de precisão para cada classe, ponderada pelo número de casos verdadeiros em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|Precisão equilibrada é a média aritmética de recordação para cada classe.<br> <br>**Objetivo:** Mais perto de 1, melhor <br> **Alcance:** [0, 1]|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|A pontuação de F1 é a média harmónica de precisão e recordação. É uma boa medida equilibrada tanto de falsos positivos como de falsos negativos. No entanto, não tem em conta os verdadeiros negativos. <br> <br>**Objetivo:** Mais perto de 1, melhor <br> **Alcance:** [0, 1]<br> <br>Os nomes métricos suportados incluem,<li>  `f1_score_macro`: a média aritmética da pontuação F1 para cada classe. <li> `f1_score_micro`: calculado contando os verdadeiros positivos totais, falsos negativos e falsos positivos. <li> `f1_score_weighted`: média ponderada por frequência de classe da pontuação F1 para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|Esta é a função de perda utilizada na regressão logística (multinomial) e extensões da mesmo, tais como redes neurais, definidas como a probabilidade negativa de log-probabilidade dos verdadeiros rótulos dadas as previsões de um classificador probabilístico. <br><br> **Objetivo:** Mais perto de 0, melhor <br> **Alcance:** [0, inf)|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| A recuperação macro normalizada é a recuperação macro-média e normalizada, de modo que o desempenho aleatório tem uma pontuação de 0, e o desempenho perfeito tem uma pontuação de 1. <br> <br>**Objetivo:** Mais perto de 1, melhor <br> **Alcance:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>onde, `R` é o valor esperado de `recall_score_macro` previsões aleatórias.<br><br>`R = 0.5`&nbsp;para &nbsp; &nbsp; a classificação binária. <br>`R = (1 / C)` para problemas de classificação de classe C.|
matthews_correlation | O coeficiente de correlação matthews é uma medida equilibrada de precisão, que pode ser usada mesmo que uma classe tenha muito mais amostras do que outra. Um coeficiente de 1 indica previsão perfeita, 0 previsão aleatória e previsão inversa de -1.<br><br> **Objetivo:** Mais perto de 1, melhor <br> **Alcance:** [-1, 1]|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
precisão|Precisão é a capacidade de um modelo para evitar a rotulagem de amostras negativas como positivas. <br><br> **Objetivo:** Mais perto de 1, melhor <br> **Alcance:** [0, 1]<br> <br>Os nomes métricos suportados incluem, <li> `precision_score_macro`, a média aritmética de precisão para cada classe. <li> `precision_score_micro`, calculado globalmente contando os verdadeiros positivos e falsos positivos. <li> `precision_score_weighted`, a média aritmética de precisão para cada classe, ponderada pelo número de casos verdadeiros em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
revocação| Lembre-se é a capacidade de um modelo detetar todas as amostras positivas. <br><br> **Objetivo:** Mais perto de 1, melhor <br> **Alcance:** [0, 1]<br> <br>Os nomes métricos suportados incluem, <li>`recall_score_macro`: a média aritmética de recordação para cada classe. <li> `recall_score_micro`: calculado globalmente contando os verdadeiros positivos totais, falsos negativos e falsos positivos.<li> `recall_score_weighted`: a média aritmética de recordação para cada classe, ponderada pelo número de casos verdadeiros em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|A precisão ponderada é a precisão em que cada amostra é ponderada pelo número total de amostras pertencentes à mesma classe. <br><br>**Objetivo:** Mais perto de 1, melhor <br>**Alcance:** [0, 1]|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>Métricas de classificação binárias vs. multiclasse

ML automatizado não diferencia entre métricas binárias e multiclasses. As mesmas métricas de validação são reportadas se um conjunto de dados tem duas classes ou mais de duas classes. No entanto, algumas métricas destinam-se à classificação multiclasse. Quando aplicadas a um conjunto binário de dados, estas métricas não tratarão nenhuma classe como a `true` classe, como seria de esperar. As métricas claramente destinadas a multiclasse são sufixadas com `micro` `macro` , ou `weighted` . Exemplos `average_precision_score` `f1_score` incluem, , , `precision_score` e `recall_score` `AUC` .

Por exemplo, em vez de calcular a recordação como `tp / (tp + fn)` , as médias médias multiclasse `micro` `macro` (, ou ) `weighted` médias em ambas as classes de um conjunto de dados de classificação binária. Isto equivale a calcular a recordação para a `true` classe e a classe `false` separadamente, e, em seguida, tomar a média dos dois.

ML automatizado não calcula métricas binárias, isto é, métricas para conjuntos de dados de classificação binária. No entanto, estas métricas podem ser calculadas manualmente usando a matriz de [confusão](#confusion-matrix) que ml automatizado gerou para essa execução particular. Por exemplo, pode calcular a precisão, `tp / (tp + fp)` com os verdadeiros valores positivos e falsos positivos mostrados num gráfico de matriz de confusão 2x2.

## <a name="confusion-matrix"></a>A matriz de confusão

As matrizes de confusão fornecem um visual para como um modelo de aprendizagem automática está a cometer erros sistemáticos nas suas previsões para modelos de classificação. A palavra "confusão" no nome provém de um modelo "confuso" ou de amostras de rotulagem errada. Uma célula em linha `i` e coluna numa matriz de `j` confusão contém o número de amostras no conjunto de dados de avaliação que pertencem à classe `C_i` e foram classificadas pelo modelo como classe `C_j` .

No estúdio, uma célula mais escura indica um maior número de amostras. Selecionar a vista **normalizada** no dropdown normalizará em cada linha matriz para mostrar a percentagem de classe `C_i` prevista para ser classe `C_j` . O benefício da visão **bruta** padrão é que você pode ver se o desequilíbrio na distribuição de classes reais fez com que o modelo classificasse mal amostras da classe minoritária, uma questão comum em conjuntos de dados desequilibrados.

A matriz de confusão de um bom modelo terá a maioria das amostras ao longo da diagonal.

### <a name="confusion-matrix-for-a-good-model"></a>Matriz de confusão para um bom modelo 
![Matriz de confusão para um bom modelo ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>Matriz de confusão para um mau modelo
![Matriz de confusão para um mau modelo](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>Curva ROC

A característica de funcionamento do recetor (ROC) traça a relação entre a taxa positiva verdadeira (TPR) e a taxa falsa positiva (FPR) à medida que o limiar de decisão muda. A curva ROC pode ser menos informativa quando se treinam modelos em conjuntos de dados com desequilíbrio de classe elevada, uma vez que a classe maioritária pode abafar as contribuições das classes minoritárias.

A área sob a curva (AUC) pode ser interpretada como a proporção de amostras corretamente classificadas. Mais precisamente, a AUC é a probabilidade de o classificador classificar uma amostra positiva escolhida aleatoriamente mais do que uma amostra negativa escolhida aleatoriamente. A forma da curva dá uma intuição para a relação entre TPR e FPR em função do limiar de classificação ou limite de decisão.

Uma curva que se aproxima do canto superior esquerdo da tabela está a aproximar-se de um TPR 100% e 0% de FPR, o melhor modelo possível. Um modelo aleatório produziria uma curva ROC ao longo `y = x` da linha do canto inferior esquerdo para o canto superior direito. Um modelo pior do que aleatório teria uma curva ROC que desce abaixo da `y = x` linha.
> [!TIP]
> Para experiências de classificação, cada uma das tabelas de linha produzidas para modelos ML automatizados pode ser usada para avaliar o modelo por classe ou média em todas as classes. Pode alternar entre estas diferentes vistas clicando nas etiquetas de classe na legenda à direita da tabela.

### <a name="roc-curve-for-a-good-model"></a>Curva ROC para um bom modelo
![Curva ROC para um bom modelo](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>Curva ROC para um mau modelo
![Curva ROC para um mau modelo](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>Curva de precisão

A curva de precisão-recordação traça a relação entre precisão e recordação à medida que o limiar de decisão muda. Recorde-se que a capacidade de um modelo detetar todas as amostras positivas e precisão é a capacidade de um modelo evitar a rotulagem de amostras negativas como positivas. Alguns problemas de negócio podem exigir uma maior recuperação e uma maior precisão dependendo da importância relativa de evitar falsos negativos vs falsos positivos.
> [!TIP]
> Para experiências de classificação, cada uma das tabelas de linha produzidas para modelos ML automatizados pode ser usada para avaliar o modelo por classe ou média em todas as classes. Pode alternar entre estas diferentes vistas clicando nas etiquetas de classe na legenda à direita da tabela.
### <a name="precision-recall-curve-for-a-good-model"></a>Curva de precisão para um bom modelo
![Curva de precisão para um bom modelo](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>Curva de precisão para um mau modelo
![Curva de precisão para um mau modelo](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>Curva de ganhos cumulativos

A curva de ganhos cumulativos traça a percentagem de amostras positivas corretamente classificadas em função da percentagem de amostras consideradas onde consideramos amostras na ordem da probabilidade prevista.

Para calcular o ganho, primeiro classifica todas as amostras da probabilidade mais alta para a menor prevista pelo modelo. Em seguida, pegue `x%` as previsões de maior confiança. Divida o número de amostras positivas detetadas na `x%` questão pelo número total de amostras positivas para obter o ganho. O ganho acumulado é a percentagem de amostras positivas que detetamos quando consideramos alguns por cento dos dados que são mais prováveis de pertencer à classe positiva.

Um modelo perfeito classificará todas as amostras positivas acima de todas as amostras negativas, dando uma curva de ganhos cumulativos composta por dois segmentos retos. A primeira é uma linha com declive `1 / x` de onde está a `(0, 0)` `(x, 1)` `x` fração de amostras que pertencem à classe positiva `1 / num_classes` (se as classes são equilibradas). A segunda é uma linha horizontal de `(x, 1)` `(1, 1)` . No primeiro segmento, todas as amostras positivas são classificadas corretamente e o ganho acumulado vai para `100%` dentro da primeira das `x%` amostras consideradas.

O modelo aleatório de base terá uma curva de ganhos cumulativa, na sequência `y = x` do qual `x%` foram detetadas amostras consideradas apenas sobre `x%` o total de amostras positivas. Um modelo perfeito terá uma curva micro média que toca no canto superior esquerdo e uma linha média macro que tem inclinação `1 / num_classes` até que o ganho acumulado seja 100% e depois horizontal até que a percentagem de dados seja de 100.
> [!TIP]
> Para experiências de classificação, cada uma das tabelas de linha produzidas para modelos ML automatizados pode ser usada para avaliar o modelo por classe ou média em todas as classes. Pode alternar entre estas diferentes vistas clicando nas etiquetas de classe na legenda à direita da tabela.
### <a name="cumulative-gains-curve-for-a-good-model"></a>Curva de ganhos cumulativos para um bom modelo
![Curva de ganhos cumulativos para um bom modelo](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>Cumulativo ganha curva para um mau modelo
![Cumulativo ganha curva para um mau modelo](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>Curva de elevação

A curva de elevação mostra quantas vezes melhor um modelo executa em comparação com um modelo aleatório. O elevador é definido como o rácio de ganho cumulativo com o ganho cumulativo de um modelo aleatório.

Este desempenho relativo tem em conta o facto de que a classificação fica mais difícil à medida que aumenta o número de turmas. (Um modelo aleatório prevê incorretamente uma fração mais elevada de amostras de um conjunto de dados com 10 classes em comparação com um conjunto de dados com duas classes)

A curva de elevação de base é a `y = 1` linha onde o desempenho do modelo é consistente com o de um modelo aleatório. Em geral, a curva de elevação para um bom modelo será mais alta nesse gráfico e mais longe do eixo x, mostrando que quando o modelo está mais confiante nas suas previsões, executa muitas vezes melhor do que adivinhação aleatória.

> [!TIP]
> Para experiências de classificação, cada uma das tabelas de linha produzidas para modelos ML automatizados pode ser usada para avaliar o modelo por classe ou média em todas as classes. Pode alternar entre estas diferentes vistas clicando nas etiquetas de classe na legenda à direita da tabela.
### <a name="lift-curve-for-a-good-model"></a>Curva de elevação para um bom modelo
![Curva de elevação para um bom modelo](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>Curva de elevação para um mau modelo
![Curva de elevação para um mau modelo](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>Curva de calibração

A curva de calibração traça a confiança de um modelo nas suas previsões contra a proporção de amostras positivas a cada nível de confiança. Um modelo bem calibrado classificará corretamente 100% das previsões a que atribui 100% de confiança, 50% das previsões que atribui 50% de confiança, 20% das previsões que atribui a 20% de confiança, e assim por diante. Um modelo perfeitamente calibrado terá uma curva de calibração seguindo a `y = x` linha onde o modelo prevê perfeitamente a probabilidade de as amostras pertencerem a cada classe.

Um modelo excessivamente confiante irá prever probabilidades excessivamente próximas de zero e uma, raramente sendo incerto sobre a classe de cada amostra e a curva de calibração será semelhante a "S" atrasado. Um modelo pouco confiante atribuirá uma menor probabilidade, em média, à classe que prevê e a curva de calibração associada será semelhante a um "S". A curva de calibração não retrata a capacidade de classificação correta de um modelo, mas sim a sua capacidade de atribuir corretamente confiança às suas previsões. Um mau modelo ainda pode ter uma boa curva de calibração se o modelo atribuir corretamente baixa confiança e elevada incerteza.

> [!NOTE]
> A curva de calibração é sensível ao número de amostras, pelo que um pequeno conjunto de validação pode produzir resultados ruidosos que podem ser difíceis de interpretar. Isto não significa necessariamente que o modelo não esteja bem calibrado.

### <a name="calibration-curve-for-a-good-model"></a>Curva de calibração para um bom modelo
![Curva de calibração para um bom modelo](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>Curva de calibração para um mau modelo
![Curva de calibração para um mau modelo](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>Métricas de regressão/previsão

O ML automatizado calcula as mesmas métricas de desempenho de cada modelo gerado, independentemente de se tratar de uma experiência de regressão ou previsão. Estas métricas também passam por normalização para permitir a comparação entre modelos treinados em dados com diferentes gamas. Para saber mais, consulte [a normalização métrica.](#metric-normalization)  

A tabela seguinte resume as métricas de desempenho do modelo geradas para experiências de regressão e previsão. Tal como as métricas de classificação, estas métricas também são baseadas nas implementações do scikit learn. A documentação adequada para aprender scikit está ligada em conformidade, no campo **de Cálculo.**

|Metric|Descrição|Cálculo|
--|--|--|
explained_variance|A variação explicada mede até que ponto um modelo explica a variação da variável-alvo. É a diminuição percentual da variação dos dados originais para a variação dos erros. Quando a média dos erros é 0, é igual ao coeficiente de determinação (ver r2_score abaixo). <br> <br> **Objetivo:** Mais perto de 1, melhor <br> **Alcance:** (inf, 1]|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|Erro absoluto médio é o valor esperado do valor absoluto da diferença entre o alvo e a previsão.<br><br> **Objetivo:** Mais perto de 0, melhor <br> **Alcance:** [0, inf) <br><br> Tipos: <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`, o mean_absolute_error dividido pelo alcance dos dados. | [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|Erro percentual absoluto médio (MAPE) é uma medida da diferença média entre um valor previsto e o valor real.<br><br> **Objetivo:** Mais perto de 0, melhor <br> **Alcance:** [0, inf) ||
median_absolute_error|O erro absoluto mediano é a mediana de todas as diferenças absolutas entre o alvo e a previsão. Esta perda é robusta para os forasteiros.<br><br> **Objetivo:** Mais perto de 0, melhor <br> **Alcance:** [0, inf)<br><br>Tipos: <br> `median_absolute_error`<br> `normalized_median_absolute_error`: o median_absolute_error dividido pelo intervalo dos dados. |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R<sup>2</sup> (coeficiente de determinação) mede a redução proporcional do erro quadrado médio (MSE) em relação à variação total dos dados observados. <br> <br> **Objetivo:** Mais perto de 1, melhor <br> **Alcance:** [-1, 1]<br><br>Nota: R<sup>2</sup> tem frequentemente o alcance (-inf, 1]. O MSE pode ser maior do que a variação observada, pelo que o R<sup>2</sup> pode ter valores negativos arbitrariamente grandes, dependendo dos dados e das previsões do modelo. Os clips automatizados de ML reportaram pontuações R<sup>2</sup> em -1, pelo que um valor de -1 para R<sup>2</sup> provavelmente significa que a verdadeira pontuação R<sup>2</sup> é inferior a -1. Considere os outros valores métricas e as propriedades dos dados ao interpretar uma pontuação R<sup>2</sup> negativa.|[Cálculo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |Erro quadrado de raiz (RMSE) é a raiz quadrada da diferença quadrada esperada entre o alvo e a previsão. Para um estimador imparcial, o RMSE é igual ao desvio padrão.<br> <br> **Objetivo:** Mais perto de 0, melhor <br> **Alcance:** [0, inf)<br><br>Tipos:<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: o root_mean_squared_error dividido pelo intervalo dos dados. |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|Erro de log quadrado de raiz é a raiz quadrada do erro logarítmico quadrado esperado.<br><br>**Objetivo:** Mais perto de 0, melhor <br> **Alcance:** [0, inf) <br> <br>Tipos: <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: o root_mean_squared_log_error dividido pelo intervalo dos dados.  |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| A correlação de Spearman é uma medida nãoparamétrica da monótona relação entre dois conjuntos de dados. Ao contrário da correlação pearson, a correlação de Spearman não assume que ambos os conjuntos de dados são normalmente distribuídos. Como outros coeficientes de correlação, Spearman varia entre -1 e 1 com 0 implicando nenhuma correlação. Correlações de -1 ou 1 implicam uma relação monótona exata. <br><br> Spearman é uma métrica de correlação de ordem de classificação, o que significa que as alterações aos valores previstos ou reais não mudarão o resultado de Spearman se não alterarem a ordem de classificação dos valores previstos ou reais.<br> <br> **Objetivo:** Mais perto de 1, melhor <br> **Alcance:** [-1, 1]|[Cálculo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>Normalização métrica

O ML automatizado normaliza as métricas de regressão e previsão que permitem a comparação entre modelos treinados em dados com diferentes gamas. Um modelo treinado num dado com uma gama maior tem um erro maior do que o mesmo modelo treinado em dados com uma gama menor, a menos que esse erro seja normalizado.

Embora não exista um método padrão de normalização das métricas de erro, a ML automatizada toma a abordagem comum de dividir o erro pela gama dos dados: `normalized_error = error / (y_max - y_min)`

Ao avaliar um modelo de previsão sobre os dados da série de tempo, o ML automatizado toma medidas adicionais para garantir que a normalização acontece por série de tempo ID (grão), porque cada série de tempo provavelmente tem uma distribuição diferente dos valores-alvo.
## <a name="residuals"></a>Residuais

O gráfico residual é um histograma dos erros de previsão (residuais) gerados para regressão e experiências de previsão. Os residuais são calculados como `y_predicted - y_true` para todas as amostras e depois apresentados como um histograma para mostrar viés de modelo.

Neste exemplo, note-se que ambos os modelos são ligeiramente tendenciosos para prever abaixo do valor real. Isto não é incomum para um conjunto de dados com uma distribuição distorcida de alvos reais, mas indica pior desempenho do modelo. Um bom modelo terá uma distribuição residual que atinge zero com poucos residuais nos extremos. Um modelo pior terá uma distribuição de residuais espalhada com menos amostras em torno de zero.

### <a name="residuals-chart-for-a-good-model"></a>Gráfico de residuais para um bom modelo
![Gráfico de residuais para um bom modelo](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>Gráfico de residuais para um mau modelo
![Gráfico de residuais para um mau modelo](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>Previsto vs. verdadeiro

Para a regressão e a experiência de previsão, o gráfico previsto vs. gráfico verdadeiro traça a relação entre a característica-alvo (valores verdadeiros/reais) e as previsões do modelo. Os verdadeiros valores são ligados ao longo do eixo x e para cada caixote o valor médio previsto é traçado com barras de erro. Isto permite-lhe ver se um modelo é tendencioso para prever determinados valores. A linha apresenta a previsão média e a área sombreada indica a variação das previsões em torno dessa média.

Muitas vezes, o valor verdadeiro mais comum terá as previsões mais precisas com a menor variação. A distância da linha de tendência da linha ideal `y = x` onde existem poucos valores verdadeiros é uma boa medida do desempenho do modelo em outliers. Pode usar o histograma na parte inferior da tabela para raciocinar sobre a distribuição de dados real. Incluir mais amostras de dados onde a distribuição é escassa pode melhorar o desempenho do modelo em dados invisíveis.

Neste exemplo, note-se que o melhor modelo tem uma linha previsível vs. verdadeira que está mais próxima da `y = x` linha ideal.

### <a name="predicted-vs-true-chart-for-a-good-model"></a>Gráfico previsto vs. verdadeiro para um bom modelo
![Gráfico previsto vs. verdadeiro para um bom modelo](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>Gráfico previsto vs. verdadeiro para um mau modelo
![Gráfico previsto vs. verdadeiro para um mau modelo](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>Explicações do modelo e importâncias de características

Embora as métricas e gráficos de avaliação de modelos sejam bons para medir a qualidade geral de um modelo, inspecionar que conjunto de dados possui um modelo usado para fazer as suas previsões é essencial na prática de IA responsável. É por isso que a ML automatizada fornece um painel de explicações de modelo para medir e reportar as contribuições relativas das funcionalidades do conjunto de dados. Veja como [ver o painel de explicações no estúdio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md#model-explanations-preview).

Para uma primeira experiência de código, consulte como configurar [explicações de modelos para experiências automatizadas de ML com o Azure Machine Learning Python SDK](how-to-machine-learning-interpretability-automl.md).

> [!NOTE]
> O modelo ForecastTCN não é atualmente suportado por explicações automáticas de ML e outros modelos de previsão podem ter acesso limitado a ferramentas de interpretação.

## <a name="next-steps"></a>Passos seguintes
* Experimente os [cadernos de amostras de amostra de exemplo de machine learning automatizados.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
* Para perguntas específicas automatizadas do ML, askautomatedml@microsoft.com contacte.
