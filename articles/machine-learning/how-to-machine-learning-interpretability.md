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
ms.reviewer: Luis.Quintanilla
ms.date: 04/02/2020
ms.openlocfilehash: fcb837af85a54102e8c9eafc33249af9dba6b5ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80631394"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Interpretação do modelo em Aprendizagem automática azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Visão geral da interpretação do modelo

A interpretação é fundamental para cientistas de dados, auditores e decisores empresariais para garantir o cumprimento das políticas da empresa, padrões da indústria e regulamentos governamentais:

+ Os cientistas de dados precisam da capacidade de explicar os seus modelos a executivos e partes interessadas, para que possam compreender o valor e a precisão das suas descobertas. Exigem também a interpretação para desinbugicar os seus modelos e tomar decisões informadas sobre como melhorá-los. 

+ Os auditores legais exigem ferramentas para validar modelos no que diz respeito à conformidade regulamentar e monitorizar como as decisões dos modelos estão a afetar os seres humanos. 

+ Os decisores empresariais precisam de paz de espírito, tendo a capacidade de fornecer transparência aos utilizadores finais. Isto permite-lhes ganhar e manter a confiança.


Permitir a capacidade de explicar um modelo de aprendizagem automática é importante durante duas fases principais do desenvolvimento do modelo:
+ Durante a fase de formação, como designers de modelos e avaliadores podem usar a saída de interpretabilidade de um modelo para verificar hipóteses e criar confiança com as partes interessadas. Também usam as ideias do modelo para depurar, validar o comportamento do modelo corresponde aos seus objetivos, e para verificar se há injustiça seleções ou características insignificantes do modelo.

+ Durante a fase de inferência, como ter transparência em torno de modelos implantados capacita executivos a entender "quando implementado" como o modelo está funcionando e como as suas decisões estão a tratar e a impactar as pessoas na vida real. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretação com Aprendizagem automática Azure

As aulas de interpretação são disponibilizadas através de vários pacotes SDK: (Saiba como [instalar pacotes SDK para Aprendizagem automática Azure)](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)

* `azureml.interpret`, o pacote principal, contendo funcionalidades suportadas pela Microsoft.

* `azureml.contrib.interpret`, pré-visualização e funcionalidades experimentais que pode experimentar.

* `azureml.train.automl.automlexplainer`pacote para a interpretação de modelos automatizados de aprendizagem automática.

Utilize `pip install azureml-interpret` `pip install azureml-interpret-contrib` e para uso `pip install azureml-interpret-contrib` geral, e para utilização automática para obter os pacotes de interpretação.


> [!IMPORTANT]
> O conteúdo `contrib` no espaço de nomenão é totalmente suportado. À medida que as funcionalidades experimentais se tornam maduras, serão gradualmente transferidas para o espaço principal de nome.
.



## <a name="how-to-interpret-your-model"></a>Como interpretar o seu modelo

Utilizando as classes e métodos no SDK, pode:
+ Explique a previsão do modelo gerando valores de importância de características para todo o modelo e/ou pontos de dados individuais. 
+ Obter a interpretação do modelo em conjuntos de dados do mundo real em escala, durante o treino e inferência.
+ Use um dashboard de visualização interativa para descobrir padrões em dados e explicações no tempo de treino


No machine learning, **as funcionalidades** são os campos de dados usados para prever um ponto de dados alvo. Por exemplo, para prever o risco de crédito, podem ser utilizados campos de dados para a idade, o tamanho da conta e a idade da conta. Neste caso, idade, tamanho da conta e idade da conta são **características.** A importância da funcionalidade diz-lhe como cada campo de dados afetou as previsões do modelo. Por exemplo, a idade pode ser fortemente utilizada na previsão, enquanto o tamanho e a idade da conta não afetam significativamente os valores de previsão. Este processo permite que os cientistas de dados expliquem as previsões resultantes, de modo que as partes interessadas tenham visibilidade sobre quais as características mais importantes no modelo.

Aprenda sobre técnicas de interpretação suportadas, modelos de aprendizagem automática suportados e ambientes de corrida suportados aqui.


## <a name="supported-interpretability-techniques"></a>Técnicas de interpretação suportadas

 `azureml-interpret`utiliza as técnicas de interpretação desenvolvidas na [Interpret-Community,](https://github.com/interpretml/interpret-community/)um pacote de pitão de código aberto para treinar modelos interpretáveis e ajudando a explicar os sistemas de IA da caixa preta. [A Interpret-Community](https://github.com/interpretml/interpret-community/) serve como anfitrião dos explicadores apoiados pelo SDK, e atualmente apoia as seguintes técnicas de interpretação:

|Técnica de Interpretação|Descrição|Tipo|
|--|--|--------------------|
|1. Explicador de árvores SHAP| O explicador de árvores do [SHAP,](https://github.com/slundberg/shap)que se foca no algoritmo de estimativa rápida de valor shap do tempo polinomial específico das **árvores e conjuntos de árvores.**|Modelo específico|
|2. Explicador Profundo SHAP| Baseado na explicação do [SHAP,](https://github.com/slundberg/shap)Deep Explainer "é um algoritmo de aproximação de alta velocidade para valores SHAP em modelos de aprendizagem profunda que se baseia numa ligação com deepLIFT descrita no [papel SHAP NIPS.](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions) Os modelos **TensorFlow** e **Keras** utilizando o backend TensorFlow são suportados (existe também suporte preliminar para pyTorch)".|Modelo específico|
|3. Explicador Linear SHAP| O explicador linear do [SHAP](https://github.com/slundberg/shap)calcula os valores SHAP para um **modelo linear,** contabilizando opcionalmente correlações inter-características.|Modelo específico|
|4. Explicador de Kernel SHAP| O explicador Kernel do [SHAP](https://github.com/slundberg/shap)usa uma regressão linear local especialmente ponderada para estimar os valores de SHAP para **qualquer modelo**.|Modelo-agnóstico|
|5. Mimic Explainer (Substituto Global)| O explicador mimic baseia-se na ideia de treinar [modelos de substitutos globais](https://christophm.github.io/interpretable-ml-book/global.html) para imitar modelos de blackbox. Um modelo global de substitutos é um modelo intrinsecamente interpretável que é treinado para aproximar as previsões de qualquer modelo de **caixa preta** com a maior precisão possível. Os cientistas de dados podem interpretar o modelo de substituto para tirar conclusões sobre o modelo da caixa preta. Pode utilizar um dos seguintes modelos interpretáveis como modelo de substituição: LightGBM (LGBMExplainableModel), Regressão Linear (LinearExplainableModel), Modelo explicador de Descida De Gradiente Stocástico (SGDExplainableModel) e Decision Tree (DecisionTreeExplainableModel).|Modelo-agnóstico|
|6. Explicador de importância da característica permutação (PFI)| Permutação Característica Importância é uma técnica usada para explicar modelos de classificação e regressão que é inspirado no [papel de Breiman Random Forests](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (ver secção 10). A um nível elevado, a forma como funciona é baralhando aleatoriamente dados uma característica de cada vez para todo o conjunto de dados e calculando quanto a métrica de desempenho do interesse muda. Quanto maior for a mudança, mais importante é a característica. O PFI pode explicar o comportamento geral de **qualquer modelo subjacente,** mas não explica as previsões individuais. |Modelo-agnóstico|




Além das técnicas de interpretabilidade acima descritas, apoiamos `TabularExplainer`outro [explicador baseado em SHAP,](https://github.com/slundberg/shap)chamado . Dependendo do modelo, `TabularExplainer` utiliza um dos explicadores SHAP suportados:

* TreeExplainer para todos os modelos à base de árvores
* DeepExplainer para modelos DNN
* LinearExplainer para modelos lineares
* KernelExplainer para todos os outros modelos

`TabularExplainer`também fez melhorias significativas de características e desempenho sobre os Explicadores Diretos SHAP:

* **Resumo do conjunto de dados de inicialização**. Nos casos em que a velocidade da explicação é mais importante, resumemos o conjunto de dados de inicialização e geramos um pequeno conjunto de amostras representativas, o que acelera a geração de valores de importância global e individual.
* **Amostragem do conjunto de dados de avaliação**. Se o utilizador passar num grande conjunto de amostras de avaliação, mas não necessitar de todas elas para ser avaliada, o parâmetro de amostragem pode ser definido como verdadeiro para acelerar o cálculo das explicações globais do modelo.

O diagrama seguinte mostra a estrutura atual dos explicadores suportados.

[![Arquitetura de interpretação de aprendizagem automática](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Modelos de aprendizagem automática suportados

O `azureml.interpret` pacote do SDK suporta modelos treinados com os seguintes formatos dataset:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

As funções de explicação aceitam os modelos e os gasodutos como entrada. Se for fornecido um modelo, o `predict` modelo `predict_proba` deve implementar a função de previsão ou em conformidade com a convenção de Scikit. Se o seu modelo não suportar isto, pode embrulhar o seu `predict` modelo `predict_proba` numa função que gere o mesmo resultado que ou em Scikit e utilizar essa função de invólucro com o explicador selecionado. Se for fornecido um gasoduto, a função de explicação pressupõe que o script do gasoduto de execução devolve uma previsão. Utilizando esta técnica `azureml.interpret` de embrulho, pode apoiar modelos treinados através de estruturas de aprendizagem profunda PyTorch, TensorFlow e Keras, bem como modelos clássicos de aprendizagem automática.

## <a name="local-and-remote-compute-target"></a>Alvo de computação local e remota

O `azureml.interpret` pacote foi concebido para funcionar com alvos de computação local e remota. Se for executada localmente, as funções SDK não entrarão em contacto com nenhum serviço Azure. 

Pode executar a explicação remotamente na Computação de Aprendizagem automática Azure e registar as informações de explicação no Serviço de História da Corrida de Aprendizagem automática Azure. Uma vez que esta informação é registada, os relatórios e visualizações da explicação estão prontamente disponíveis no estúdio Azure Machine Learning para análise do utilizador.


## <a name="next-steps"></a>Passos seguintes

Veja o [como](how-to-machine-learning-interpretability-aml.md) permitir a interpretação de modelos formando recursos de computação remota azure machine learning. Consulte os cadernos de [amostras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) para cenários adicionais.
