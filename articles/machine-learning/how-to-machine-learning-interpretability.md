---
title: Interpretabilidade de modelo no Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como explicar por que seu modelo faz previsões usando o SDK do Azure Machine Learning. Ele pode ser usado durante o treinamento e a inferência para entender como seu modelo faz previsões.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 7101cef6acd7c7b321fbd31c614063a1fa8fe17a
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771875"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Interpretabilidade de modelo no Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Visão geral da interpretação do modelo

A interpretação é fundamental para cientistas de dados e tomadores de decisões de negócios, para garantir a conformidade com as políticas da empresa, padrões do setor e regulamentos governamentais:
+ Os cientistas de dados precisam da capacidade de explicar seus modelos a executivos e participantes, para que possam entender o valor e a precisão de suas descobertas 
+ Os tomadores de decisões de negócios precisam de tranqüilidade da capacidade de fornecer transparência para os usuários finais obterem e manterem sua confiança

A habilitação da capacidade de explicar um modelo de aprendizado de máquina é importante durante duas fases principais do desenvolvimento de modelos:
+ Durante a fase de treinamento do ciclo de desenvolvimento do modelo do Machine Learning. Designers de modelo e avaliadores podem usar a saída de interpretação de um modelo para verificar as mesmas e criar confiança com os participantes. Eles também usam as informações sobre o modelo para depuração, validando o comportamento do modelo corresponde aos seus objetivos e verificando os recursos de tendência ou insignificantes.
+ Durante a fase de inferência, como a transparência de modelos implantados permite que os executivos compreendam "quando implantados" como o modelo está funcionando e como suas decisões estão tratando e afetando as pessoas na vida real. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretabilidade com Azure Machine Learning

Neste artigo, você aprenderá como os conceitos de interpretação de modelo são implementados no SDK.

Usando as classes e os métodos no SDK, você pode obter:
+ Valores de importância de recursos para recursos brutos e de engenharia
+ Interpretabilidade em conjuntos de valores reais em escala, durante o treinamento e a inferência.
+ Visualizações interativas para ajudá-lo na descoberta de padrões em dados e explicações no tempo de treinamento


No aprendizado de máquina, os **recursos** são os campos de dados usados para prever um ponto de dados de destino. Por exemplo, para prever o risco de crédito, os campos de dados para idade, tamanho da conta e idade da conta podem ser usados. Nesse caso, a idade, o tamanho da conta e a idade da conta são **recursos**. A importância do recurso informa como cada campo de dados afetou as previsões do modelo. Por exemplo, a idade pode ser muito usada na previsão, enquanto o tamanho da conta e a idade não afetam significativamente a precisão da previsão. Esse processo permite que os cientistas de dados expliquem previsões resultantes, para que os participantes tenham visibilidade sobre quais pontos de dados são mais importantes no modelo.

Usando essas ferramentas, você pode explicar os modelos de aprendizado de máquina **globalmente em todos os dados**ou **localmente em um ponto de dados específico** usando as tecnologias de ponta, de maneira fácil de usar e escalonáveis.

As classes de interpretação são disponibilizadas por meio de vários pacotes do SDK. Saiba como [instalar pacotes do SDK para Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* `azureml.interpret`, o pacote principal, que contém funcionalidades com suporte da Microsoft.

* `azureml.contrib.interpret`, visualização e funcionalidades experimentais que você pode tentar.

* `azureml.train.automl.automlexplainer` pacote para interpretar modelos de aprendizado de máquina automatizados.

> [!IMPORTANT]
> Não há suporte total para o conteúdo no namespace `contrib`. Como as funcionalidades experimentais se tornam maduras, elas serão gradualmente movidas para o namespace principal.

## <a name="how-to-interpret-your-model"></a>Como interpretar seu modelo

Você pode aplicar as classes e métodos de interpretação para entender o comportamento global do modelo ou previsões específicas. O primeiro é chamado de explicação global e o segundo é chamado de explicação local.

Os métodos também podem ser categorizados com base no fato de o método ser independente de modelo ou específico de modelo. Alguns métodos visam certos tipos de modelos. Por exemplo, o explicador de árvore do SHAP só se aplica a modelos baseados em árvore. Alguns métodos tratam o modelo como uma caixa preta, como um explicador de imitação ou de kernel SHAP. O pacote de `interpret` aproveita essas diferentes abordagens com base em conjuntos de dados, tipos de modelo e casos de uso.

A saída é um conjunto de informações sobre como um determinado modelo faz sua previsão, como:
* Importância de recurso relativo global/local
* Recurso global/local e relação de previsão

### <a name="explainers"></a>Explicadores

Este pacote usa as técnicas de interpretação desenvolvidas na [interpretação da Comunidade](https://github.com/interpretml/interpret-community/), um pacote Python de software livre para treinar modelos interpretáveis e ajudar a explicar os sistemas Blackbox ia. A [interpretação-Community](https://github.com/interpretml/interpret-community/) serve como o host para os explicadores com suporte do SDK e atualmente oferece suporte às seguintes técnicas de interpretação:

* **Explicador de árvore shap**: explicador de árvore de [shap](https://github.com/slundberg/shap), que se concentra no algoritmo de estimativa de valor de shap de tempo polinomial rápido específico para árvores e conjuntos de árvores.
* **Explicador profundo do shap**: com base na explicação do [shap](https://github.com/slundberg/shap), explicativo profundo "é um algoritmo de aproximação de alta velocidade para valores de shap em modelos de aprendizado profundo que se baseia em uma conexão com DeepLIFT descrito no [artigo shap Nips](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). Há suporte para modelos TensorFlow e modelos Keras usando o back-end TensorFlow (também há suporte preliminar para PyTorch) ".
* **Explicador linear do shap**: explicador linear de [SHAP](https://github.com/slundberg/shap)computa valores shap para um modelo linear, opcionalmente, para correlações entre recursos.

* **Explicador de kernel shap**: o explicador de kernel de [shap](https://github.com/slundberg/shap)usa uma regressão linear local especialmente ponderada para estimar os valores de shap para qualquer modelo.
* **Explicador de imitação**: o explicador de imitação se baseia na ideia de treinar [modelos substitutos globais](https://christophm.github.io/interpretable-ml-book/global.html) para imitar modelos Blackbox. Um modelo substituto global é um modelo intrinsecamente interpretável que é treinado para aproximar as previsões de um modelo de caixa preta o mais precisamente possível. O cientista de dados pode interpretar o modelo substituto para desenhar conclusões sobre o modelo de caixa preta. Você pode usar um dos seguintes modelos interpretáveis como seu modelo substituto: LightGBM (LGBMExplainableModel), regressão linear (LinearExplainableModel), estocástico gradiente descendente de modelo explicativo (SGDExplainableModel) e árvore de decisão ( DecisionTreeExplainableModel).


* **Explicador de importância do recurso de permutação**: a importância do recurso de permuta é uma técnica usada para explicar os modelos de classificação e regressão inspirados pelo [documento de florestas aleatórias do Breiman](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (consulte a seção 10). Em um alto nível, a maneira como ele funciona é por meio do embaralhamento de dados um recurso por vez para todo o DataSet e calcular quanto a métrica de desempenho de interesse muda. Quanto maior a alteração, mais importante é esse recurso.

* **Explicador de verde-limão** (`contrib`): com base em [verde](https://github.com/marcotcr/lime)-limão, o explicador de verde-limão usa o algoritmo de verde-limão (explicações de modelo interpretativo) de última geração local para criar modelos substitutos locais. Diferentemente dos modelos substitutos globais, o verde-limão se concentra em treinar modelos substitutos locais para explicar previsões individuais.
* **Explicador de texto Han** (`contrib`): o explicador de texto Han usa uma rede de atenção hierárquica para obter explicações de modelo de dados de texto para um determinado modelo de texto de caixa preta. Ele treina o modelo substituto de HAN em uma determinada saída prevista do modelo de caixa preta. Depois de treinar globalmente pelo texto corpus, ele adiciona uma etapa de ajuste fino para um documento específico a fim de melhorar a precisão das explicações. O HAN usa um RNN bidirecional com duas camadas de atenção, para a atenção da frase e da palavra. Depois que o DNN é treinado no modelo de caixa preta e ajustado em um documento específico, o usuário pode extrair a palavra importância das camadas de atenção. O HAN é mostrado para ser mais preciso do que verde-limão ou SHAP para dados de texto, mas também mais dispendioso em termos de tempo de treinamento. Foram feitas melhorias para dar ao usuário a opção de inicializar a rede com incorporações de palavras diferenciada para reduzir o tempo de treinamento. O tempo de treinamento pode ser melhorado significativamente com a execução de HAN em uma VM de GPU remota do Azure. A implementação de HAN é descrita em ["redes de atenção hierárquica para classificação de documentos (Yang et al., 2016)"](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


* **Explicador tabular**: `TabularExplainer` emprega a seguinte lógica para invocar os explicadores diretos do [shap](https://github.com/slundberg/shap) :

    1. Se for um modelo baseado em árvore, aplique SHAP `TreeExplainer`, senão
    2. Se for um modelo DNN, aplique o SHAP `DeepExplainer`, caso contrário
    3. Se for um modelo linear, aplique SHAP `LinearExplainer`, senão
    3. Tratá-lo como um modelo de caixa preta e aplicar SHAP `KernelExplainer`


`TabularExplainer` também fez melhorias significativas de desempenho e recursos em relação aos explicadores de SHAP diretos:

* **Resumo do conjunto de inicialização**. Nos casos em que a velocidade da explicação é mais importante, resumimos o conjunto de inicialização e geramos um pequeno conjunto de exemplos representativos, que aceleram a explicação global e local.
* **Amostragem do conjunto de dados de avaliação**. Se o usuário passar em um grande conjunto de amostras de avaliação, mas não precisar realmente de todas elas para ser avaliada, o parâmetro de amostragem poderá ser definido como true para acelerar a explicação global.

O diagrama a seguir mostra a estrutura atual dos explicadores diretos e meta.

[Arquitetura de interpretação de Machine Learning ![](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Modelos com suporte

Todos os modelos treinados em conjuntos de valores em Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`ou formato `scipy.sparse.csr_matrix` são suportados pelo pacote de `explain` de interpretação do SDK.

As funções de explicação aceitam modelos e pipelines como entrada. Se um modelo for fornecido, o modelo deverá implementar a função de previsão `predict` ou `predict_proba` que esteja de acordo com a Convenção Scikit. Se um pipeline (nome do script de pipeline) for fornecido, a função de explicação assumirá que o script de pipeline em execução retorna uma previsão. Damos suporte a modelos treinados por meio de estruturas de aprendizado profundo PyTorch, TensorFlow e Keras.

### <a name="local-and-remote-compute-target"></a>Destino de computação local e remota

O pacote de `explain` foi projetado para funcionar com destinos de computação locais e remotos. Se for executado localmente, as funções do SDK não entrarão em contato com nenhum serviço do Azure. Você pode executar a explicação remotamente em Azure Machine Learning computação e registrar em log as informações de explicação em Azure Machine Learning serviços de histórico de execução. Depois que essas informações são registradas, relatórios e visualizações da explicação estão prontamente disponíveis no espaço de trabalho Azure Machine Learning para análise de usuário.


## <a name="next-steps"></a>Passos seguintes

Veja [como](how-to-machine-learning-interpretability-aml.md) habilitar a interpretabilidade para treinamento de modelos tanto localmente quanto em Azure Machine Learning recursos de computação remota. Consulte os [blocos de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) para cenários adicionais.
