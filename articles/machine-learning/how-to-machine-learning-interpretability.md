---
title: Interpretação do modelo em Azure Machine Learning (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a compreender & explicar como o seu modelo de aprendizagem automática faz previsões durante o treino & inferencing usando o Azure Machine Learning Python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 02/25/2021
ms.openlocfilehash: 2c61cfaf0e97f7d483239a23e5eea52b51c6a126
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690214"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Interpretação do modelo em Azure Machine Learning (pré-visualização)


## <a name="model-interpretability-overview"></a>Visão geral da interpretação do modelo

A interpretação do modelo é fundamental para cientistas de dados, auditores e decisores empresariais para garantir o cumprimento das políticas da empresa, dos padrões da indústria e dos regulamentos governamentais:

+ Os cientistas de dados precisam da capacidade de explicar os seus modelos aos executivos e às partes interessadas, para que possam compreender o valor e a precisão das suas descobertas. Exigem também a interpretação para depurar os seus modelos e tomar decisões informadas sobre como melhorá-los. 

+ Os auditores legais exigem ferramentas para validar modelos no que diz respeito à conformidade regulamentar e monitorizar a forma como as decisões dos modelos estão a afetar os seres humanos. 

+ Os decisores empresariais precisam de paz de espírito, tendo a capacidade de fornecer transparência aos utilizadores finais. Isto permite-lhes ganhar e manter a confiança.

Permitir a capacidade de explicar um modelo de aprendizagem automática é importante durante duas fases principais de desenvolvimento do modelo:

+ Durante a fase de formação, como designers de modelos e avaliadores podem usar a produção de interpretação de um modelo para verificar hipóteses e criar confiança com as partes interessadas. Também usam os insights no modelo para depurar, validar o comportamento do modelo corresponde aos seus objetivos, e para verificar se há injustiças ou características insignificantes do modelo.

+ Durante a fase de inferimento, como ter transparência em torno dos modelos implantados capacita os executivos a entender "quando implementados" como o modelo está a funcionar e como as suas decisões estão a tratar e a impactar as pessoas na vida real. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretação com Azure Machine Learning

As aulas de interpretação do modelo são disponibilizadas através do seguinte pacote SDK: (Saiba como [instalar pacotes SDK para Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py))

* `azureml.interpret`, contém funcionalidades suportadas pela Microsoft.

Utilização `pip install azureml-interpret` para uso geral.

## <a name="how-to-interpret-your-model"></a>Como interpretar o seu modelo

Utilizando as classes e métodos no SDK, pode:
+ Explique a previsão do modelo gerando valores de importância de característica para todo o modelo e/ou pontos de dados individuais. 
+ Alcançar a interpretação do modelo em conjuntos de dados do mundo real em escala, durante o treino e inferência.
+ Use um painel de visualização interativa para descobrir padrões em dados e explicações no momento do treino

No machine learning, **as funcionalidades** são os campos de dados utilizados para prever um ponto de dados-alvo. Por exemplo, para prever o risco de crédito, os campos de dados para a idade, o tamanho da conta e a idade da conta podem ser usados. Neste caso, idade, tamanho da conta e idade da conta são **características**. A importância da funcionalidade indica como cada campo de dados afetou as previsões do modelo. Por exemplo, a idade pode ser fortemente utilizada na previsão, enquanto o tamanho e a idade da conta não afetam significativamente os valores de previsão. Este processo permite que os cientistas de dados expliquem as previsões resultantes, de modo que as partes interessadas tenham visibilidade sobre quais as características mais importantes no modelo.

## <a name="supported-interpretability-techniques"></a>Técnicas de interpretação apoiadas

 `azureml-interpret` utiliza as técnicas de interpretação desenvolvidas na [Interpret-Community,](https://github.com/interpretml/interpret-community/)um pacote de pitão de código aberto para a formação de modelos interpretáveis e ajuda a explicar os sistemas de IA blackbox. [A Interpret-Community](https://github.com/interpretml/interpret-community/) serve de anfitrião para os explicadores apoiados por este SDK, e apoia atualmente as seguintes técnicas de interpretação:

|Técnica de Interpretação|Descrição|Tipo|
|--|--|--------------------|
|Explicador de árvores SHAP| O explicador de árvores [shap,](https://github.com/slundberg/shap)que se foca no tempo polinómico fast SHAP valor value value algoritmo específico para **árvores e conjuntos de árvores.**|Específico do modelo|
|Explicador Profundo SHAP| Com base na explicação da SHAP, o Deep Explainer "é um algoritmo de aproximação de alta velocidade para valores SHAP em modelos de aprendizagem profunda que se baseia numa ligação com o DeepLIFT descrita no [papel SHAP NIPS](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). Os modelos **TensorFlow** e **Keras** que utilizam o backend TensorFlow são suportados (existe também suporte preliminar para pyTorch)".|Específico do modelo|
|Explicador Linear SHAP| O explicador linear da SHAP calcula os valores de SHAP para um **modelo linear,** contabilizando opcionalmente as correlações inter-características.|Específico do modelo|
|Explicador SHAP Kernel| O explicador kernel da SHAP utiliza uma regressão linear local especialmente ponderada para estimar os valores SHAP para **qualquer modelo**.|Modelo-agnóstico|
|Imitador de Explicadores (Substituto Global)| O explicador de mimic baseia-se na ideia de treinar [modelos de substituição globais](https://christophm.github.io/interpretable-ml-book/global.html) para imitar modelos blackbox. Um modelo de substituição global é um modelo intrinsecamente interpretável que é treinado para aproximar as previsões de **qualquer modelo de caixa preta** com a maior precisão possível. Os cientistas de dados podem interpretar o modelo de substituição para tirar conclusões sobre o modelo da caixa preta. Pode utilizar um dos seguintes modelos interpretáveis como modelo de substituição: LightGBM (LGBMExplainableModel), Regressão Linear (LinearExplainableModel), Modelo explicável de Descida de Gradiente Stochastic (SGDExplainableModel) e DecisionTreeExplaableModel.|Modelo-agnóstico|
|Explicador de Importância de Recurso de Permutação (PFI)| Permutação Característica Importância é uma técnica usada para explicar modelos de classificação e regressão que é inspirado no [papel de Florestas Aleatórias de Breiman](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (ver secção 10). A um nível elevado, a forma como funciona é baralhando aleatoriamente os dados uma característica de cada vez para todo o conjunto de dados e calculando quanto a métrica de desempenho dos juros muda. Quanto maior for a mudança, mais importante é a característica. A PFI pode explicar o comportamento geral de **qualquer modelo subjacente,** mas não explica previsões individuais. |Modelo-agnóstico|

Além das técnicas de interpretação descritas acima, apoiamos outro explicador baseado em SHAP, chamado `TabularExplainer` . Dependendo do modelo, `TabularExplainer` utiliza um dos explicadores SHAP suportados:

* TreeExplainer para todos os modelos à base de árvores
* DeepExplainer para modelos DNN
* LinearExplainer para modelos lineares
* KernelExplainer para todos os outros modelos

`TabularExplainer` também fez melhorias significativas de recursos e desempenho sobre os explicadores diretos shap:

* **Resumo do conjunto de dados de inicialização**. Nos casos em que a rapidez da explicação é mais importante, resumimos o conjunto de dados de inicialização e geramos um pequeno conjunto de amostras representativas, o que acelera a geração de valores de importância de características gerais e individuais.
* **Amostragem do conjunto de dados de avaliação.** Se o utilizador passar num grande conjunto de amostras de avaliação, mas não precisar de todas elas para ser avaliada, o parâmetro de amostragem pode ser definido para acelerar o cálculo das explicações globais do modelo.

O diagrama seguinte mostra a estrutura atual dos explicadores suportados.

[![Arquitetura de Interpretação de Aprendizagem Automática](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Modelos de aprendizagem automática suportados

O `azureml.interpret` pacote do SDK suporta modelos treinados com os seguintes formatos de conjunto de dados:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

As funções de explicação aceitam tanto os modelos como os gasodutos como entrada. Se for fornecido um modelo, o modelo deve implementar a função de previsão `predict` ou conforme com a `predict_proba` convenção Scikit. Se o seu modelo não o suportar, pode embrulhar o seu modelo numa função que gere o mesmo resultado `predict` que ou `predict_proba` no Scikit e utilizar essa função de invólucro com o explicador selecionado. Se for fornecido um gasoduto, a função de explicação pressupõe que o guião do gasoduto de execução devolve uma previsão. Utilizando esta técnica de embrulho, `azureml.interpret` pode suportar modelos treinados via PyTorch, TensorFlow e Keras deep learning frameworks, bem como modelos clássicos de aprendizagem automática.

## <a name="local-and-remote-compute-target"></a>Alvo de computação local e remota

O `azureml.interpret` pacote foi concebido para funcionar com alvos de computação local e remota. Se for executado localmente, as funções SDK não entrarão em contacto com nenhum serviço Azure. 

Pode executar a explicação remotamente no Azure Machine Learning Compute e registar a informação de explicação no Serviço de História da Aprendizagem da Máquina de Azure. Uma vez que esta informação é registada, relatórios e visualizações da explicação estão prontamente disponíveis no estúdio Azure Machine Learning para análise do utilizador.


## <a name="next-steps"></a>Passos seguintes

- Consulte o como permitir [a](how-to-machine-learning-interpretability-aml.md) interpretação para modelos que treinam localmente e em recursos de computação remota Azure Machine Learning. 
- Consulte os cadernos de [amostras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) para obter cenários adicionais. 
- Se estiver interessado em interpretação para cenários de texto, consulte [Interpret-text](https://github.com/interpretml/interpret-text), um repo de código aberto relacionado com [a Interpret-Community,](https://github.com/interpretml/interpret-community/)para técnicas de interpretação para NLP. `azureml.interpret` pacote não suporta atualmente estas técnicas, mas você pode começar com um [caderno de exemplo sobre classificação de texto](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb).