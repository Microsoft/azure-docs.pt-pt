---
title: 'Tutorial: Treine o seu primeiro modelo Azure ML em Python'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você aprende os padrões de design fundacional em Azure Machine Learning, e treina um modelo simples de aprendizagem de scikit com base no conjunto de dados da diabetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: c8f259d2d4df46470a042c3f65ac1b8e1f66b1dd
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546039"
---
# <a name="tutorial-train-your-first-ml-model"></a>Tutorial: Treine o seu primeiro modelo ML

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este tutorial é a **segunda parte de uma série composta por duas partes**. No tutorial anterior, [criou-se um espaço de trabalho e escolheu um ambiente](tutorial-1st-experiment-sdk-setup.md)de desenvolvimento. Neste tutorial, você aprende os padrões de design fundacional em Azure Machine Learning, e treina um modelo simples de aprendizagem de scikit com base no conjunto de dados da diabetes. Depois de concluir este tutorial, terá o conhecimento prático do SDK para escalar até desenvolver experiências e fluxos de trabalho mais complexos.

Neste tutorial, irá aprender as seguintes tarefas:

> [!div class="checklist"]
> * Conecte o seu espaço de trabalho e crie uma experiência
> * Carregue mato de dados e treine modelos de aprendizagem de ficção científica
> * Ver resultados de formação no estúdio
> * Obter o melhor modelo

## <a name="prerequisites"></a>Pré-requisitos

O único pré-requisito é executar a primeira parte deste tutorial, ambiente de [configuração e espaço de trabalho.](tutorial-1st-experiment-sdk-setup.md)

Nesta parte do tutorial, você executa o código na amostra de tutoriais de caderno *jupyter/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb* aberto no final da primeira parte. Este artigo percorre o mesmo código que está no caderno.

## <a name="open-the-notebook"></a>Abra o caderno

1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com/)

1. Abra o **tutorial-1ª experiência-sdk-train.ipynb** na sua pasta, como mostrado na [primeira parte](tutorial-1st-experiment-sdk-setup.md#open).


> [!Warning]
> **Não** crie um *novo* caderno na interface Jupyter! Os *tutoriais portátil/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb* incluem **todo o código e dados necessários** para este tutorial.

## <a name="connect-workspace-and-create-experiment"></a>Ligar espaço de trabalho e criar experiência

> [!Important]
> O resto deste artigo contém o mesmo conteúdo que se vê no caderno.  
>
> Mude para o caderno Jupyter agora se quiser ler enquanto executa o código. 
> Para executar uma única célula de código num caderno, clique na célula de código e clique no **Shift+Enter**. Ou, executar todo o caderno escolhendo **Executar tudo** a partir da barra de ferramentas superior.

Importe `Workspace` a classe e carregue as `config.json` suas `from_config().` informações de subscrição a partir do ficheiro utilizando a função Isto procura o ficheiro `from_config(path="your/file/path")`JSON no diretório atual por padrão, mas também pode especificar um parâmetro de percurso para apontar para o ficheiro utilizando . Num servidor de portátil em nuvem, o ficheiro encontra-se automaticamente no diretório raiz.

Se o código seguinte pedir autenticação adicional, basta colar o link num browser e introduzir o símbolo de autenticação.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Agora crie uma experiência no seu espaço de trabalho. Uma experiência é outro recurso de nuvem fundacional que representa uma coleção de ensaios (executa modelo individual). Neste tutorial você usa a experiência para criar corridas e acompanhar o seu model training no estúdio Azure Machine Learning. Os parâmetros incluem a referência do espaço de trabalho e um nome de corda para a experiência.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Carregue dados e prepare-se para o treino

Para este tutorial, você usa o conjunto de dados da diabetes, que usa características como idade, sexo e IMC para prever a progressão da doença da diabetes. Carregue os dados da classe [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) e divida-os em conjuntos de treino e teste utilizando `train_test_split()`. Esta função segrega os dados para que o modelo tenha dados invisíveis para utilizar para testes após treino.


```python
from azureml.opendatasets import Diabetes
from sklearn.model_selection import train_test_split

x_df = Diabetes.get_tabular_dataset().to_pandas_dataframe().dropna()
y_df = x_df.pop("Y")

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Preparar um modelo

Treinar um modelo simples de aprendizagem de ficção pode ser facilmente feito localmente para treinoem em pequena escala, mas quando treina muitas iterações com dezenas de diferentes permutações de recursos e configurações de hiperparâmetros, é fácil perder a noção dos modelos que treinou e como os treinou. O seguinte padrão de design mostra como alavancar o SDK para acompanhar facilmente o seu treino na nuvem.

Construa um guião que treine modelos de ridge em um loop através de diferentes valores alfa hiperparâmetros.


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)

    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)

    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name

    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

O código acima realiza o seguinte:

1. Para cada valor de hiperparâmetro alfa na `alphas` matriz, uma nova execução é criada dentro da experiência. O valor alfa é registado para diferenciar entre cada corrida.
1. Em cada corrida, um modelo Ridge é instantâneo, treinado e usado para executar previsões. O erro quadrado-raiz-médio é calculado para os valores reais versus previstos, e depois registrado na execução. Neste ponto, a execução tem metadados anexados tanto para o valor alfa como para a precisão rmse.
1. Em seguida, o modelo para cada execução é serializado e carregado para a execução. Isto permite-lhe descarregar o ficheiro modelo a partir da execução no estúdio.
1. No final de cada iteração a `run.complete()`corrida é completada através da chamada .

Depois de concluído o treino, ligue para a `experiment` variável para obter um link para a experiência no estúdio.

```python
experiment
```

<table style="width:100%"><tr><th>Nome</th><th>Área de trabalho</th><th>Página de relatórios</th><th>Página do Docs</th></tr><tr><td>diabetes-experiência</td><td>seu-trabalho espaço-nome</td><td>Link para estúdio de machine learning Azure</td><td>Ligação à Documentação</td></tr></table>

## <a name="view-training-results-in-studio"></a>Ver resultados de formação em estúdio

Seguir o **estúdio Link to Azure Machine Learning** leva-o à página principal de experiências. Aqui você vê todos os indivíduos corre na experiência. Quaisquer valores personalizados `rmse`(e,`alpha_value` neste caso) tornam-se campos para cada corrida, e também ficam disponíveis para os gráficos e azulejos no topo da página de experimentação. Para adicionar uma métrica registada a um gráfico ou azulejo, paire sobre ele, clique no botão de edição e encontre a sua métrica personalizada.

Quando os modelos de treino em escala ao longo de centenas e milhares de runs separados, esta página torna fácil ver todos os modelos que treinou, especificamente como foram treinados, e como as suas métricas únicas mudaram ao longo do tempo.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/experiment-main.png" alt-text="Página principal de experiências no estúdio.":::


Selecione um link `RUN NUMBER` de número de execução na coluna para ver a página para uma execução individual. O separador predefinido **Detalhes** mostram-lhe informações mais detalhadas em cada execução. Navegue para o separador **Outputs + logs** e veja o `.pkl` ficheiro do modelo que foi carregado para a execução durante cada iteração de treino. Aqui pode descarregar o ficheiro modelo, em vez de ter de o retreinar manualmente.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/model-download.png" alt-text="Executar a página de detalhes no estúdio.":::

## <a name="get-the-best-model"></a>Obtenha o melhor modelo

Além de poder descarregar ficheiros de modelos a partir da experiência no estúdio, também pode descarregá-los programáticamente. O código seguinte iténio através de cada execução na experiência, e acede tanto às métricas de execução registadas como aos detalhes de execução (que contém o run_id). Isto acompanha a melhor execução, neste caso a corrida com o menor erro de raiz-média-quadrado.

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]

    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))
```

    Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
    Best run_id rmse: 57.234760283951765

Utilize o id de melhor execução `Run` para obter a execução individual usando o construtor juntamente com o objeto de experiência. Em `get_file_names()` seguida, ligue para ver todos os ficheiros disponíveis para download a partir desta execução. Neste caso, só fez o upload de um ficheiro para cada corrida durante o treino.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Ligue `download()` para o objeto de execução, especificando o nome do ficheiro do modelo para descarregar. Por predefinição, esta função descarrega para o diretório atual.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Limpar recursos

Não complete esta secção se planeia executar outros tutoriais de Aprendizagem automática Azure.

### <a name="stop-the-compute-instance"></a>Pare a instância computacional

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Apagar tudo

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Também pode manter o grupo de recursos, mas eliminar um único espaço de trabalho. Mostrar as propriedades do espaço de trabalho e selecionar **Apagar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez as seguintes tarefas:

> [!div class="checklist"]
> * Ligou o seu espaço de trabalho e criou uma experiência
> * Dados carregados e modelos de aprendizagem de ficção científica treinados
> * Resultados de formação visualizados no estúdio e modelos recuperados

[Implemente o seu modelo](tutorial-deploy-models-with-aml.md) com a Azure Machine Learning.
Aprenda a desenvolver experiências automatizadas de [aprendizagem automática](tutorial-auto-train-models.md) de máquinas.
