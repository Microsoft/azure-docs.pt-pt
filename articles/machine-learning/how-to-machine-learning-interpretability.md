---
title: Interpretação do modelo em Aprendizagem automática azure
titleSuffix: Azure Machine Learning
description: Saiba como explicar porque é que o seu modelo faz previsões utilizando o SDK de Aprendizagem automática Azure. Pode ser usado durante o treino e inferência para entender como o seu modelo faz previsões.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: b68d2a72dc18f683f2203429908a536db1b5124a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063992"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Interpretação do modelo em Aprendizagem automática azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Visão geral da interpretação do modelo

A interpretação é fundamental tanto para os cientistas de dados como para os decisores empresariais para garantir o cumprimento das políticas da empresa, padrões da indústria e regulamentos governamentais:
+ Os cientistas de dados precisam da capacidade de explicar os seus modelos a executivos e stakeholders, para que possam compreender o valor e a precisão das suas descobertas 
+ Os decisores empresariais precisam de paz de espírito da capacidade de proporcionar transparência para que os utilizadores finais ganhem e mantenham a sua confiança

Permitir a capacidade de explicar um modelo de aprendizagem automática é importante durante duas fases principais do desenvolvimento do modelo:
+ Durante a fase de formação do ciclo de desenvolvimento do modelo de aprendizagem automática. Designers e avaliadores de modelos podem usar a saída de interpretabilidade de um modelo para verificar hipóteses e criar confiança com as partes interessadas. Também usam as ideias do modelo para depurar, validar o comportamento do modelo corresponde aos seus objetivos, e para verificar se há preconceitos ou características insignificantes.
+ Durante a fase de inferência, como ter transparência em torno de modelos implantados capacita executivos a entender "quando implementado" como o modelo está funcionando e como as suas decisões estão a tratar e a impactar as pessoas na vida real. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretação com Aprendizagem automática Azure

Neste artigo, aprende-se como os conceitos de interpretação de modelos são implementados no SDK.

Utilizando as classes e métodos no SDK, pode obter:
+ Valores de destaque tanto para características cruas como para as características de engenharia
+ Interpretação em conjuntos de dados do mundo real em escala, durante o treino e inferência.
+ Visualizações interativas para ajudá-lo na descoberta de padrões em dados e explicações no tempo de treino


No machine learning, **as funcionalidades** são os campos de dados usados para prever um ponto de dados alvo. Por exemplo, para prever o risco de crédito, podem ser utilizados campos de dados para a idade, o tamanho da conta e a idade da conta. Neste caso, idade, tamanho da conta e idade da conta são **características.** A importância da funcionalidade diz-lhe como cada campo de dados afetou as previsões do modelo. Por exemplo, a idade pode ser fortemente usada na previsão, enquanto o tamanho e a idade da conta não afetam significativamente a precisão da previsão. Este processo permite que os cientistas de dados expliquem as previsões resultantes, de modo que as partes interessadas tenham visibilidade sobre quais os pontos de dados mais importantes no modelo.

Utilizando estas ferramentas, pode explicar modelos de machine learning **globalmente em todos os dados**, ou **localmente, num ponto** de dados específico, utilizando as tecnologias de ponta de uma forma fácil de usar e escalável.

As aulas de interpretação são disponibilizadas através de vários pacotes SDK. Saiba como [instalar pacotes SDK para Aprendizagem automática Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* `azureml.interpret`, o pacote principal, contendo funcionalidades suportadas pela Microsoft.

* `azureml.contrib.interpret`, pré-visualização e funcionalidades experimentais que pode experimentar.

* `azureml.train.automl.automlexplainer`pacote para a interpretação de modelos automatizados de aprendizagem automática.

> [!IMPORTANT]
> O conteúdo `contrib` no espaço de nomenão é totalmente suportado. À medida que as funcionalidades experimentais se tornam maduras, serão gradualmente transferidas para o espaço principal de nome.

## <a name="how-to-interpret-your-model"></a>Como interpretar o seu modelo

Pode aplicar as classes e métodos de interpretação para compreender o comportamento global do modelo ou previsões específicas. A primeira chama-se explicação global e esta última chama-se explicação local.

Os métodos também podem ser categorizados com base no modelo agnóstico ou específico do modelo. Alguns métodos visam certos tipos de modelos. Por exemplo, o explicador de árvores do SHAP só se aplica a modelos à base de árvores. Alguns métodos tratam o modelo como uma caixa preta, como o explicador de imitação ou o explicador de kernel do SHAP. O `interpret` pacote aproveita estas diferentes abordagens com base em conjuntos de dados, tipos de modelos e casos de utilização.

A saída é um conjunto de informações sobre como um determinado modelo faz a sua previsão, tais como:
* Importância da característica relativa global/local
* Relação de características e previsão global/local

### <a name="explainers"></a>Explicadores

Este pacote utiliza as técnicas de interpretação desenvolvidas na [Interpret-Community,](https://github.com/interpretml/interpret-community/)um pacote de pitão de código aberto para treinar modelos interpretáveis e ajudando a explicar os sistemas de IA da caixa preta. [A Interpret-Community](https://github.com/interpretml/interpret-community/) serve como anfitrião dos explicadores apoiados pelo SDK, e atualmente apoia as seguintes técnicas de interpretação:

* **Explicador de árvores SHAP**: Explicador de árvores do [SHAP,](https://github.com/slundberg/shap)que se foca no algoritmo de estimativa de valor shap rápido do tempo polinomial específico das árvores e conjuntos de árvores.
* **SHAP Deep Explainer**: Baseado na explicação do [SHAP,](https://github.com/slundberg/shap)Deep Explainer "é um algoritmo de aproximação de alta velocidade para valores SHAP em modelos de aprendizagem profunda que se baseia numa ligação com deepLIFT descrita no [papel NipS SHAP.](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions) Os modelos TensorFlow e Keras utilizando o backend TensorFlow são suportados (existe também suporte preliminar para pyTorch)".
* **Explicador linear SHAP**: [SHAP's](https://github.com/slundberg/shap)Linear explainer computa valores SHAP para um modelo linear, contabilizando opcionalmente correlações inter-características.

* **Explicador SHAP Kernel**: O explicador Kernel do [SHAP](https://github.com/slundberg/shap)usa uma regressão linear local especialmente ponderada para estimar os valores de SHAP para qualquer modelo.
* **Mimic Explainer**: Mimic explainer baseia-se na ideia de treinar [modelos de substitutos globais](https://christophm.github.io/interpretable-ml-book/global.html) para imitar modelos de blackbox. Um modelo global de substitutos é um modelo intrinsecamente interpretável que é treinado para aproximar as previsões de um modelo de caixa preta com a maior precisão possível. O cientista de dados pode interpretar o modelo de substituto para tirar conclusões sobre o modelo da caixa preta. Pode utilizar um dos seguintes modelos interpretáveis como modelo de substituição: LightGBM (LGBMExplainableModel), Regressão Linear (LinearExplainableModel), Modelo explicado de Descida De Gradiente Stocástico (SGDExplainableModel) e Decision Tree ( SGDExplainableModel) e Decision Tree ( DecisionTreeExplainableModel).


* **Explicador de características permutação**: Permutação Característica Importância é uma técnica usada para explicar modelos de classificação e regressão que é inspirado no papel de [Breiman Random Forests](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (ver secção 10). A um nível elevado, a forma como funciona é baralhando aleatoriamente dados uma característica de cada vez para todo o conjunto de dados e calculando quanto a métrica de desempenho do interesse muda. Quanto maior for a mudança, mais importante é a característica.

* **Explicador** DE`contrib`LIMA ( ): Baseado em [LIMA,](https://github.com/marcotcr/lime)LIME Explainer usa o algoritmo de explicações agnósticas de modelo-agnóstico local de última geração (LIME) para criar modelos de substituição locais. Ao contrário dos modelos globais de substitutos, a LIME foca-se na formação de modelos de substituição locais para explicar as previsões individuais.
* **Han Text Explainer** ():`contrib`HAN Text Explainer usa uma Rede de Atenção Hierárquica para obter explicações de modelo a partir de dados de texto para um determinado modelo de texto de caixa preta. Treina o modelo de substituto HAN numa dada caixa preta, as saídas previstas do modelo. Depois de treinar globalmente através do corpus de texto, adiciona um passo de ajuste fino para um documento específico, a fim de melhorar a precisão das explicações. HAN usa um RNN bidirecional com duas camadas de atenção, para atenção da frase e da palavra. Uma vez que o DNN é treinado no modelo da caixa preta e afinado num documento específico, o utilizador pode extrair as importâncias da palavra das camadas de atenção. Han é demonstrado ser mais preciso do que LIME ou SHAP para dados de texto, mas mais dispendioso em termos de tempo de treino também. Foram feitas melhorias para dar ao utilizador a opção de inicializar a rede com incorporações de palavras GloVe para reduzir o tempo de treino. O tempo de treino pode ser melhorado significativamente executando HAN em um VM GPU azure remoto. A implementação da HAN é descrita em ["Hierárquicos Redes de Atenção para Classificação de Documentos (Yang et al., 2016)"](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


* **Explicador Tabular:** `TabularExplainer` emprega a seguinte lógica para invocar os Explicadores [Diretos sHAP:](https://github.com/slundberg/shap)

    1. Se for um modelo à base `TreeExplainer`de árvores, aplique SHAP, então
    2. Se for um modelo DNN, `DeepExplainer`aplique SHAP, caso contrário
    3. Se for um modelo linear, `LinearExplainer`aplique SHAP, então
    3. Trate-o como um modelo de caixa preta e aplique SHAP`KernelExplainer`


`TabularExplainer`também fez melhorias significativas de características e desempenho sobre os Explicadores Diretos SHAP:

* **Resumo do conjunto de dados de inicialização**. Nos casos em que a velocidade da explicação é mais importante, resumemos o conjunto de dados de inicialização e geramos um pequeno conjunto de amostras representativas, o que acelera tanto a explicação global como local.
* **Amostragem do conjunto de dados de avaliação**. Se o utilizador passar num grande conjunto de amostras de avaliação, mas não precisar de todas elas para ser avaliada, o parâmetro de amostragem pode ser definido para acelerar a explicação global.

O diagrama seguinte mostra a estrutura atual de explicadores diretos e meta.

[![Arquitetura de interpretação de aprendizagem automática](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Modelos suportados

Quaisquer modelos treinados em `numpy.array`conjuntos `iml.datatypes.DenseData`de `scipy.sparse.csr_matrix` dados em Python, `pandas.DataFrame` `explain` ou formato são suportados pelo pacote de interpretação do SDK.

As funções de explicação aceitam os modelos e os gasodutos como entrada. Se for fornecido um modelo, o `predict` modelo `predict_proba` deve implementar a função de previsão ou em conformidade com a convenção de Scikit. Se for fornecido um pipeline (nome do script do gasoduto), a função de explicação pressupõe que o script do gasoduto de execução retorna uma previsão. Apoiamos modelos treinados através de estruturas de aprendizagem profunda PyTorch, TensorFlow e Keras.

### <a name="local-and-remote-compute-target"></a>Alvo de computação local e remota

O `explain` pacote foi concebido para funcionar com alvos de computação local e remota. Se for executada localmente, as funções SDK não entrarão em contacto com nenhum serviço Azure. Pode executar a explicação remotamente na Azure Machine Learning Compute e registar as informações de explicação nos Serviços de História da Corrida de Aprendizagem automática do Azure. Uma vez que esta informação é registada, os relatórios e visualizações da explicação estão prontamente disponíveis no espaço de trabalho do Azure Machine Learning para análise do utilizador.


## <a name="next-steps"></a>Passos seguintes

Veja o [como](how-to-machine-learning-interpretability-aml.md) permitir a interpretação de modelos formando recursos de computação remota azure machine learning. Consulte os cadernos de [amostras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) para cenários adicionais.
