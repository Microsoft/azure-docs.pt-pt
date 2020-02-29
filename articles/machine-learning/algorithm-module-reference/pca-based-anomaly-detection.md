---
title: 'Deteção de anomalias baseada no PCA: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de deteção de anomalias baseado em PCA para criar um modelo de deteção de anomalias baseado na Análise de Componentes Principais (PCA).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: f126eb347d11d3203ad3b97abe3606738e67c33c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925842"
---
# <a name="pca-based-anomaly-detection"></a>Deteção de anomalias baseada em PCA

Este artigo descreve como usar o módulo de **deteção de anomalias baseado** em PCA no designer de machine learning azure (pré-visualização), para criar um modelo de deteção de anomalias baseado na Análise de Componentes Principais (PCA).

Este módulo ajuda-o a construir um modelo em cenários em que é fácil obter dados de formação de uma classe, como transações válidas, mas difícil de obter amostras suficientes das anomalias direcionadas. 

Por exemplo, para detetar transações fraudulentas, muitas vezes não tem exemplos suficientes de fraude para treinar, mas tem muitos exemplos de boas transações. O módulo **de deteção de anomalias baseado em PCA** resolve o problema analisando as funcionalidades disponíveis para determinar o que constitui uma classe "normal", e aplicando métricas de distância para identificar casos que representam anomalias. Isto permite-lhe treinar um modelo utilizando dados desequilibrados existentes.

## <a name="more-about-principal-component-analysis"></a>Mais sobre a Análise de Componentes Principais

*A Análise de Componentes Principais*, que é frequentemente abreviada para PCA, é uma técnica estabelecida na aprendizagem automática. O PCA é frequentemente utilizado na análise de dados exploratórios porque revela a estrutura interna dos dados e explica a variação dos dados.

O PCA funciona analisando dados que contêm múltiplas variáveis. Procura correlações entre as variáveis e determina a combinação de valores que melhor capta diferenças nos resultados. Estes valores de característica combinada são usados para criar um espaço de características mais compacto chamado os *componentes principais*.

Para deteção de anomalias, cada nova entrada é analisada, e o algoritmo de deteção de anomalias calcula a sua projeção nos eigenvectors, juntamente com um erro de reconstrução normalizado. O erro normalizado é usado como pontuação de anomalia. Quanto maior for o erro, mais anómalo é o caso.

Para obter informações adicionais sobre como funciona o PCA, e sobre a implementação para deteção de anomalias, consulte estes trabalhos:

- [Um algoritmo aleatório para a análise principal](https://arxiv.org/abs/0809.2274)do componente. Rokhlin, Szlan e Tygert

- [Encontrar estrutura com aleatoriedade: Algoritmos probabilísticos para a construção de decomposições de matriz aproximada (download](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) pdf). Halko, Martinsson e Tropp.

## <a name="how-to-configure-pca-anomaly-detection"></a>Como configurar a Deteção de Anomalias do PCA

1. Adicione o módulo **de deteção de anomalias baseado em PCA** ao seu pipeline no designer. Pode encontrar este módulo na categoria **Deteção** de Anomalias.

2. No painel **Propriedades** para o módulo de **deteção de anomalias baseado em PCA,** clique na opção modo de **treino** e indique se pretende treinar o modelo utilizando um conjunto específico de parâmetros ou utilizar uma varredura de parâmetros para encontrar os melhores parâmetros.

    - **Parâmetro único**: Selecione esta opção se souber como pretende configurar o modelo e forneça um conjunto específico de valores como argumentos.

3. **Número de componentes a utilizar em PCA**: Especifique o número de funcionalidades de saída, ou componentes, que pretende ser feito.

    A decisão de quantos componentes incluir é uma parte importante do design de experiências usando PCA. A orientação geral é que não deve incluir o mesmo número de componentes de PCA que existem variáveis. Em vez disso, deve começar com um número menor de componentes e aumentá-los até que alguns critérios sejam cumpridos.

    Os melhores resultados são obtidos quando o número de componentes de saída é **inferior** ao número de colunas de funcionalidades disponíveis no conjunto de dados.

4. Especifique a quantidade de amostragem excessiva para realizar durante o treino de PCA aleatório. Em problemas de deteção de anomalias, os dados desequilibrados dificultam a aplicação de técnicas padrão de APC. Especificando alguma quantidade de sobreamostras, pode aumentar o número de casos-alvo.

    Se especificar 1, não é efetuada uma amostragem excessiva. Se especificar qualquer valor superior a 1, são geradas amostras adicionais para utilizar no treino do modelo.

    Existem duas opções, dependendo se está a usar uma varredura de parâmetros ou não:

    - **Parâmetro de sobreamostragem para PCA aleatório:** Digite um único número inteiro que represente a relação de sobreamostragem da classe minoritária em relação à classe normal. (Disponível quando utilizar o método de treino de **parâmetro único.)**

    > [!NOTE]
    > Não é possível visualizar o conjunto de dados sobre-amostrado. Para obter mais detalhes sobre como a sobreamostragem é utilizada com PCA, consulte [notas técnicas](#technical-notes).

5. Ativar a **função de entrada significa normalização**: Selecione esta opção para normalizar todas as funcionalidades de entrada para uma média de zero. A normalização ou escalação para zero é geralmente recomendada para PCA, porque o objetivo do APC é maximizar a variação entre variáveis.

     Esta opção está selecionada por predefinição. Desmarque esta opção se os valores já tiverem sido normalizados utilizando um método ou escala diferente.

6. Ligue um conjunto de dados de treino marcado e um dos módulos de treino:

    - Se definir a opção **de modo criar** para um parâmetro **único,** utilize o módulo modelo de deteção de anomalias do [comboio.](train-anomaly-detection-model.md)

7. Executar o pipeline.

## <a name="results"></a>Resultados

Quando o treino estiver concluído, pode guardar o modelo treinado ou ligá-lo ao módulo ['Modelo de Pontuação'](score-model.md) para prever pontuações de anomalias.

Para avaliar os resultados de um modelo de deteção de anomalias requer algumas etapas adicionais:

1. Certifique-se de que uma coluna de pontuação está disponível em ambos os conjuntos de dados

    Se tentar avaliar um modelo de deteção de anomalias e obter o erro, "Não há nenhuma coluna de pontuação no conjunto de dados pontuado para comparar", significa que está a usar um conjunto de dados de avaliação típico que contém uma coluna de etiquetas, mas sem pontuações de probabilidade. Você precisa escolher um conjunto de dados que corresponda à saída de esquemapara modelos de deteção de anomalias, que inclui uma coluna **de Etiquetas pontuais** e **probabilidades pontuadas.**

2. Certifique-se de que as colunas de etiquetas estão marcadas

    Por vezes, os metadados associados à coluna de etiquetas são removidos no gráfico do gasoduto. Se isso acontecer, quando utilizar o módulo ['Avaliar Modelo'](evaluate-model.md) para comparar os resultados de dois modelos de deteção de anomalias, poderá obter o erro: "Não existe nenhuma coluna de etiqueta sintetizada no conjunto de dados pontuado", ou "Não há nenhuma coluna de etiqueta sintetizada no conjunto de dados pontuado para comparar".

    Pode evitar este erro adicionando o módulo [Dedados editar](edit-metadata.md) antes do módulo ['Avaliar Modelo'.](evaluate-model.md) Utilize o seletor de colunas para escolher a coluna de classes e, na lista de abandono dos **Campos,** selecione **Label**.

3. Utilize o [Script Python executar](execute-python-script.md) para ajustar as categorias de colunas de etiquetas como 1 (positivo, normal) e 0 (negativo, anormal).

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Notas técnicas

Este algoritmo utiliza PCA para aproximar o subespaço que contém a classe normal. O subespaço é atravessado por eigenvectores associados aos valores superiores da matriz de covariance de dados. Para cada nova entrada, o detetor de anomalias calcula primeiro a sua projeção nos eigenvectors e, em seguida, calcula o erro de reconstrução normalizado. Este erro é a pontuação da anomalia. Quanto maior for o erro, mais anómalo é a ocorrência. Para mais detalhes sobre como o espaço normal é computado, consulte wikipedia: [Principal Análise de Componentes](https://wikipedia.org/wiki/Principal_component_analysis) 


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 

Consulte [exceções e códigos de erro para o designer (pré-visualização)](designer-error-codes.md) para obter uma lista de erros específicos dos módulos de design.