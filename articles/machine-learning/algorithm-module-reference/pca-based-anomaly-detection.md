---
title: 'Deteção de anomalias PCA-Based: referência do módulo'
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o módulo de deteção de anomalias PCA-Based para criar um modelo de deteção de anomalias baseado na análise principal dos componentes (PCA).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: fa90fcb9ebc17be9a658b08873234eada98b0fba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898457"
---
# <a name="pca-based-anomaly-detection-module"></a>Módulo de deteção de anomalias PCA-Based

Este artigo descreve como usar o módulo de deteção de anomalias PCA-Based no designer Azure Machine Learning, para criar um modelo de deteção de anomalias baseado na análise principal de componentes (PCA).

Este módulo ajuda-o a construir um modelo em cenários onde é fácil obter dados de treino de uma classe, como transações válidas, mas difícil de obter amostras suficientes das anomalias direcionadas. 

Por exemplo, para detetar transações fraudulentas, muitas vezes não tem exemplos suficientes de fraude para treinar. Mas pode ter muitos exemplos de boas transações. O módulo de deteção de anomalias PCA-Based resolve o problema analisando as funcionalidades disponíveis para determinar o que constitui uma classe "normal". O módulo aplica então métricas de distância para identificar casos que representam anomalias. Esta abordagem permite-lhe treinar um modelo utilizando dados desequilibrados existentes.

## <a name="more-about-principal-component-analysis"></a>Mais sobre a análise de componentes principais

O PCA é uma técnica estabelecida na aprendizagem automática. É frequentemente usado na análise de dados exploratórios porque revela a estrutura interna dos dados e explica a variação dos dados.

O PCA funciona através da análise de dados que contêm múltiplas variáveis. Procura correlações entre as variáveis e determina a combinação de valores que melhor captura diferenças nos resultados. Estes valores combinados de características são usados para criar um espaço de características mais compacto chamado os *principais componentes.*

Para deteção de anomalias, cada nova entrada é analisada. O algoritmo de deteção de anomalias calcula a sua projeção nos eigenvectors, juntamente com um erro de reconstrução normalizado. O erro normalizado é usado como a pontuação da anomalia. Quanto maior for o erro, mais anómalo é o caso.

Para obter mais informações sobre o funcionamento do APC e sobre a implementação para deteção de anomalias, consulte estes trabalhos:

- [Um algoritmo aleatório para análise de componentes principais,](https://arxiv.org/abs/0809.2274)por Rokhlin, Szlan e Tygert

- [Estrutura de descoberta com aleatoriedade: Algoritmos Probabilísticos para a Construção de Decomposições Descosagens De MatrizEs Aproximadas](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (download pdf), por Halko, Martinsson e Tropp

## <a name="how-to-configure-pca-based-anomaly-detection"></a>Como configurar a deteção de anomalias PCA-Based

1. Adicione o módulo **de deteção de anomalias baseada em PCA** ao seu pipeline no designer. Pode encontrar este módulo na categoria **deteção de anomalias.**

2. No painel direito do módulo, selecione a opção **modo Treino.** Indique se pretende treinar o modelo utilizando um conjunto específico de parâmetros ou utilize uma varredura de parâmetros para encontrar os melhores parâmetros.

    Se souber como pretende configurar o modelo, selecione a opção **Single Parâmetro** e forneça um conjunto específico de valores como argumentos.

3. Para **o número de componentes a utilizar em APC,** especifique o número de funcionalidades de saída ou componentes que pretende.

    A decisão de quantos componentes incluir é uma parte importante do design de experiências que usa O PCA. A orientação geral é que não deve incluir o mesmo número de componentes de APC que existem variáveis. Em vez disso, deve começar com um número menor de componentes e aumentá-los até que algum critério seja cumprido.

    Os melhores resultados são obtidos quando o número de componentes de saída é *inferior* ao número de colunas de funcionalidades disponíveis no conjunto de dados.

4. Especifique a quantidade de sobresmaltamento a realizar durante o treino de APC aleatório. Em problemas de deteção de anomalias, os dados desequilibrados dificultam a aplicação de técnicas padrão de APC. Ao especificar alguma quantidade de sobresmalte, pode aumentar o número de casos-alvo.

    Se especificar **1,** não é efetuada uma sobresmaldição. Se especificar qualquer valor superior a **1,** são geradas amostras adicionais para utilizar na formação do modelo.

    Existem duas opções, dependendo se está a usar uma varredura de parâmetros ou não:

    - **Parâmetro de sobreampling para PCA aleatório**: Escreva um único número inteiro que represente a relação de sobresmaltamento da classe minoritária em relação à classe normal. (Esta opção está disponível quando estiver a utilizar o método de treino de **parâmetro único.)**

    > [!NOTE]
    > Não é possível ver o conjunto de dados sobre-amplificados. Para obter mais informações sobre a utilização excessiva de amostras com APC, consulte [notas técnicas](#technical-notes).

5. Selecione a **função de entrada Ativa significa normalização** para normalizar todas as funcionalidades de entrada para uma média de zero. A normalização ou a escala para zero é geralmente recomendada para OPC, porque o objetivo do APC é maximizar a variação entre variáveis.

    Esta opção está selecionada por predefinição. Desmarca-o se os valores já tiverem sido normalizados através de um método ou escala diferente.

6. Ligue um conjunto de dados de treino marcado e um dos módulos de treino.

   Se definir a opção **modo de formação Criar** para Single **Parameter,** utilize o módulo [Modelo de Deteção de Anomalias de Comboio.](train-anomaly-detection-model.md)

7. Envie o oleoduto.

## <a name="results"></a>Results

Quando o treino estiver completo, pode salvar o modelo treinado. Ou pode ligá-lo ao módulo ['Modelo de Pontuação'](score-model.md) para prever pontuações de anomalias.

Para avaliar os resultados de um modelo de deteção de anomalias:

1. Certifique-se de que uma coluna de pontuação está disponível em ambos os conjuntos de dados.

    Se tentar avaliar um modelo de deteção de anomalias e obter o erro "Não existe nenhuma coluna de pontuação no conjunto de dados pontuado para comparar", está a usar um conjunto de dados de avaliação típico que contém uma coluna de etiquetas, mas sem pontuações de probabilidade. Escolha um conjunto de dados que corresponda à saída do esquema para modelos de deteção de anomalias, que inclui **etiquetas pontuadas** e colunas **de probabilidades pontuadas.**

2. Certifique-se de que as colunas de etiquetas estão marcadas.

    Por vezes, os metadados associados à coluna de etiqueta são removidos no gráfico do pipeline. Se isso acontecer, quando utilizar o módulo ['Avaliar Modelo'](evaluate-model.md) para comparar os resultados de dois modelos de deteção de anomalias, poderá obter o erro "Não existe nenhuma coluna de etiqueta no conjunto de dados pontuado." Ou pode obter o erro "Não há nenhuma coluna de etiqueta no conjunto de dados pontuado para comparar."

    Pode evitar estes erros adicionando o módulo [de metadados de edição](edit-metadata.md) antes do módulo [Modelo de Avaliação.](evaluate-model.md) Utilize o seletor de colunas para escolher a coluna de classe e, na lista **De Campos,** selecione **Label**.

3. Utilize o módulo [de script de Python executar](execute-python-script.md) para ajustar as categorias de colunas de etiquetas como **1 (positivo, normal)** e **0 (negativo, anormal).**

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Notas técnicas

Este algoritmo usa PCA para aproximar o subespaço que contém a classe normal. O subespaço é abrangedo por eigenvectors associados com os valores de topo da matriz de covariância de dados. 

Para cada nova entrada, o detetor de anomalias calcula primeiro a sua projeção nos eigenvectors e, em seguida, calcula o erro de reconstrução normalizado. Este erro é a pontuação da anomalia. Quanto maior o erro, mais anómalo o caso. Para mais detalhes sobre como o espaço normal é calculado, consulte a Wikipédia: [Análise principal de componentes.](https://wikipedia.org/wiki/Principal_component_analysis) 


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 

Consulte [exceções e códigos de erro para o designer](designer-error-codes.md) para obter uma lista de erros específicos dos módulos do designer.