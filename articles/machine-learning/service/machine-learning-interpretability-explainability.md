---
title: Capacidade de interpretação do modelo
titleSuffix: Azure Machine Learning service
description: Aprenda a explicar por que o seu modelo faz predições com o Azure Machine Learning Interpretability SDK. Ele pode ser usado durante a inferência e formação para compreender como o seu modelo faz predições.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 04/09/2019
ms.openlocfilehash: 668551603dfa2a9c42f4538fd9a66ee646e1feb7
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682462"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Modelo interpretability com o serviço Azure Machine Learning

Neste artigo, irá aprender a explicar por que o seu modelo de feitas as previsões tornou-se com o SDK de Interpretability do Azure Machine Learning. É importante ser capaz de explicar o seu modelo pelos seguintes motivos:

* Os clientes e os participantes querem saber **faz com seu modelo que se podem dar as previsões**.
* Enquanto cientista de dados, quiser entender **como consultar o modelo para encontrar informações**. Também precisa de ferramentas para tomar decisões informadas sobre **como melhorar o seu modelo**.
* Como uma empresa, precisa entender **o comportamento do modelo com diferentes distribuições de entrada** e **como o modelo se comportará ao analisar a entrada específica**.

Interpretability do Machine learning é importante em duas fases do ciclo de desenvolvimento de aprendizagem automática: 

* Durante **treinamento**: Designers de modelo e avaliadores necessitam de ferramentas de interpretability para explicar o resultado de um modelo para os participantes para criar a confiança. Eles também precisam informações sobre o modelo para que eles possam depurar o modelo e tomar decisões sobre o comportamento corresponde a seus objetivos. Por fim, eles precisam para se certificar de que o modelo não é medido.

* Durante **inferência**: Predições tem de ser explicadas para as pessoas que utilizam o seu modelo. Por exemplo, por que fiz o modelo de negar um empréstimo mortgage, ou prever que um portfólio de investimento acarreta um risco mais alto?

O SDK do Azure Machine Learning Interpretability incorpora tecnologias desenvolvidas pela Microsoft e comprovada bibliotecas de terceiros (por exemplo, SHAP e verde-LIMÃO). O SDK cria uma API comum entre as bibliotecas integradas e integra-se os serviços do Azure Machine Learning. Com este SDK, pode explicar modelos de machine learning **globalmente em todos os dados**, ou **localmente num ponto de dados específico** usando as tecnologias de topo de gama de forma fácil de usar e dimensionável.

## <a name="how-does-it-work"></a>Como funciona?

Interpretability de Aprendizado de máquina do Azure podem ser aplicada para compreender o modelo global comportamento ou predições específicas. O primeiro é chamado a explicação global e a última opção é chamado de explicação local.

Métodos de Machine Learning Interpretability do Azure podem ser categorizados de também com base em se o método é independente do modelo ou modelo específico. Alguns métodos de um determinado tipo de modelos de destinam. Por exemplo, explicador de árvore do SHAP só se aplica a modelos baseados em árvore. Alguns métodos tratam o modelo como uma caixa preta, como explicador mimic ou explicador de kernel do SHAP. Azure Machine Learning Interpretability SDK tira partido destas abordagens diferentes com base em conjuntos de dados, tipos de modelo e casos de utilização.

Interpretability de Aprendizado de máquina do Azure devolve um conjunto de informações sobre como um modelo torna sua predição. As informações incluem itens como:

* Importância da funcionalidade de relativo global/local
* Relação de funcionalidade e previsão global/local

## <a name="architecture"></a>Arquitetura

Azure Machine Learning Interpretability SDK está estruturado em dois pacotes de Python:

* [azureml.EXPLAIN.Model](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py) -o pacote principal, que contém as funcionalidades que são suportadas pela Microsoft.
* `azureml.contrib.explain.model` -Pré-visualizar e funcionalidades experimentais, que pode experimentar.

    > [!IMPORTANT]
    > As coisas de contrib não são totalmente suportadas. Como as funcionalidades experimentais maduras, eles gradualmente serão movidos para o pacote principal.

### <a name="explainers"></a>Explainers

O SDK do Azure Machine Learning Interpretability introduz dois conjuntos de explainers: Direcione Explainers e Meta Explainers.

__Direcionar explainers__ provenientes de bibliotecas integradas. O SDK encapsula todas as explainers para que eles expor uma API comum e o formato de saída. Seguem-se uma lista dos explainers diretos disponíveis no SDK:

> [!TIP]
> Se for mais confortável diretamente com estes explainers, pode invocá-los diretamente em vez de utilizar a API comum e o formato de saída.

* **Explicador da árvore**: Explicador árvore do SHAP, que se concentra nas polynomial rápido SHAP valor estimativa algoritmo de tempo específico para árvores e conjuntos de árvores.
* **Explicador profunda**: Com base na explicação de SHAP, Explicador profunda "é um algoritmo de aproximação de alta velocidade para valores SHAP nos modelos de aprendizagem profunda que se baseia numa conexão com DeepLIFT descrito no documento SHAP NIPS. Modelos de TensorFlow e modelos de Keras utilizando o back-end do TensorFlow são suportados (há também um suporte preliminar para PyTorch) ".
* **Explicador kernel**: Explicador de Kernel do SHAP utiliza uma regressão linear de local especialmente ponderado para estimar os valores SHAP para qualquer modelo.
* **Imitar Explicador**: Explicador mimic baseia-se com o conceito de modelos de substituição global. Um modelo de substituição global é um modelo de intrinsecamente interpretable que está preparado para aproximar as previsões de um modelo de caixa preta de forma mais precisa possível. Cientista de dados pode interpretar o modelo de substitutos para tirar conclusões sobre o modelo de caixa preta.
* **Verde-LIMÃO Explicador**: Com base em verde-LIMÃO, verde-LIMÃO Explicador usa o algoritmo de explicações de modelo agnóstico topo de gama Local interpretable (verde-LIMÃO) para criar modelos de local de substituição. Ao contrário dos modelos de substituição global, verde-LIMÃO concentra-se na formação de modelos de local de substituição para explicar previsões individuais.
* **Explicador de texto HAN**: Explicador de texto HAN utiliza uma rede de atenção hierárquica para obter explicações de modelo de dados de texto para um modelo de texto determinado caixa-preta. Para preparar o modelo de substitutos HAN em saídas previstos de um modelo de professor determinado. Depois de treinamento globalmente entre o corpo de texto, adicionámos um passo de fine-tune para um documento específico para melhorar a exatidão das explicações. HAN utiliza um bidirecional RNN com duas camadas de atenção, pela atenção frase e word. Depois do DNN é preparado o modelo de professor e otimizar num documento específico, vamos pode extrair importances o word das camadas de atenção. Descobrimos HAN para ser mais precisos do que verde-LIMÃO ou SHAP para dados de texto, mas mais dispendiosos em termos de também o tempo de treinamento. No entanto, fizemos melhorias para o tempo de treinamento por dar ao usuário a opção para inicializar a rede com GloVe incorporações, embora seja lento ainda. O tempo de treinamento pode ser melhorado significativamente executando HAN numa VM remota GPU do Azure. A implementação de HAN é descrita em "Hierárquica redes de atenção para a classificação de documento (Yang et al., 2016)" ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

__Meta explainers__ automaticamente selecione um explicador direto adequado e gerar as informações de explicação melhor, com base no modelo em questão e conjuntos de dados. Os explainers meta tirar partido de todas as bibliotecas (SHAP, verde-LIMÃO, imitam, etc.) que foi integrado ou desenvolvidos. Seguem-se a explainers meta disponíveis no SDK:

* **Explicador tabular**: Utilizado com conjuntos de dados em tabela.
* **Explicador texto**: Utilizado com conjuntos de dados de texto.

Além de meta-selecionar dos explainers diretos, meta explainers desenvolver funcionalidades adicionais sobre as bibliotecas subjacentes e melhorar a velocidade e escalabilidade com as explainers diretos.

Atualmente `TabularExplainer` emprega a seguinte lógica para invocar o Explainers direto:

1. Se for um modelo baseado em árvore, aplicar `TreeExplainer`, outra
2. Se é um modelo DNN, aplicam-se `DeepExplainer`, outra
3. Trate-o como um modelo de caixa preta e aplicar `KernelExplainer`

A inteligência incorporada ao `TabularExplainer` irá tornar-se mais sofisticados como bibliotecas adicionais estão integradas no SDK e que conhecemos prós e contras de cada explicador.

`TabularExplainer` também fez aprimoramentos significativos de funcionalidade e o desempenho ao longo do Explainers direto:

* **Resumo do conjunto de dados de inicialização**. Em casos em que a velocidade de explicação é mais importante, vamos resumir o conjunto de dados de inicialização e gerar um pequeno conjunto de exemplos representativos, que acelera a explicação global e local.
* **O conjunto de dados de avaliação de amostragem**. Se o utilizador passa um grande conjunto de exemplos de avaliação, mas, na verdade, não tem todos eles a ser avaliada, o parâmetro de amostragem pode ser definido como verdadeiro para acelerar a explicação global.

O diagrama seguinte mostra a relação entre os dois conjuntos de direct e meta explainers.

[![Arquitetura de Interpretability do Machine Learning](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>Modelos suportados

Quaisquer modelos que estão treinados em conjuntos de dados em Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, ou `scipy.sparse.csr_matrix` formato são suportadas pelo Machine Learning Interpretability SDK.

As funções de explicação modelos e pipelines de aceitar como entrada. Se for fornecido um modelo, o modelo tem de implementar a função de predição `predict` ou `predict_proba` que está em conformidade com a Convenção de Scikit. Se não for fornecido um pipeline (nome do script de pipeline), a função de explicação parte do princípio de que o script em execução do pipeline retorna uma predição.

### <a name="local-and-remote-compute-target"></a>Destino de computação de locais e remotos

O SDK de Interpretability do Machine Learning foi concebido para trabalhar com ambos os destinos de computação de locais e remotos. Se executar localmente, funções do SDK não irão contactar todos os serviços do Azure. Pode executar a explicação remotamente na computação do Azure Machine Learning e registar as informações de explicação em serviços de histórico de execução do Azure Machine Learning. Depois desta informação seja registrada, relatórios e visualizações da explicação estão prontamente disponíveis no Portal de área de trabalho do Azure Machine Learning para análise de utilizador.

## <a name="train-and-explain-locally"></a>Dar formação e explicar localmente

1. Prepare o seu modelo num bloco de notas Jupyter local. 

    ``` python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Chame o explicador: Para iniciar um objeto de explicação, precisa passar o modelo, dados de treinamento, recursos de interesse (opcional) e nomes de classes de saída (se classificação) para a explicação. Eis como criar uma instância de um objeto de explicador utilizando [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py), e `LimeExplainer` localmente. `TabularExplainer` está chamando um as três explainers por baixo (`TreeExplainer`, `DeepExplainer`, ou `KernelExplainer`) e é automaticamente selecionar o mais adequado para seu caso de utilização. No entanto, pode chamar cada um dos seus três explainers subjacentes diretamente.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```
    ou
    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. Obter o recurso global valores de importância.

    ```python
    # You can use the training data or the test data here. 
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. Valores de importância de funcionalidade local: Utilize as seguintes chamadas de função para explicar uma instância individual ou um grupo de instâncias.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```
    ou
    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

## <a name="train-and-explain-remotely"></a>Dar formação e explicar remotamente

Enquanto pode treinar a vários destinos de computação suportados pelo serviço Azure Machine Learning, o exemplo nesta secção mostra como fazer isso usando AMLCompute.

1. Crie um script de treinamento num bloco de notas do Jupyter local (por exemplo, run_explainer.py).

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    # Train your model here

    # explain predictions on your local machine    
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0,:])
    # upload global and local explanation objects to Run History
    upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Siga as instruções [configurar destinos de computação de preparação de modelos](how-to-set-up-training-targets.md#amlcompute) para saber mais sobre como configurar uma computação do Azure Machine Learning como o destino de computação e submeter a execução de treinamento.

3. Baixe a explicação em seu bloco de notas Jupyter local. 
    > [!IMPORTANT]
    > As coisas de contrib não são totalmente suportadas. Como as funcionalidades experimentais maduras, eles gradualmente serão movidos para o pacote principal.

    ``` python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="next-steps"></a>Próximos Passos

Para ver uma coleção de blocos de notas do Jupyter que demonstram as instruções acima, consulte a [blocos de notas do Azure Machine Learning Interpretability exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
