---
title: Converter código de experiência de aprendizagem automática para código de produção
titleSuffix: Azure Machine Learning
description: Aprenda a converter o código experimental de aprendizagem automática para o código de produção utilizando o modelo de código MLOpsPython.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/13/2020
ms.openlocfilehash: e3c9b16ae3d2b06ec19ecd29d15762a065c0c1ae
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521446"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Tutorial: Converter código experimental ML para código de produção

Um projeto de aprendizagem automática requer experimentação onde hipóteses são testadas com ferramentas ágeis como o Jupyter Notebook usando conjuntos de dados reais. Uma vez que o modelo esteja pronto para a produção, o código-modelo deve ser colocado num repositório de código de produção. Em alguns casos, o código-modelo deve ser convertido para scripts Python a colocar no repositório do código de produção. Este tutorial abrange uma abordagem recomendada sobre como exportar código de experimentação para scripts Python.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Código não essencial limpo
> * Refactor Jupyter Notebook código em funções
> * Criar scripts Python para tarefas relacionadas
> * Criar testes de unidade

## <a name="prerequisites"></a>Pré-requisitos

- Gere o [modelo MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) e use os `experimentation/Diabetes Ridge Regression Training.ipynb` e cadernos. `experimentation/Diabetes Ridge Regression Scoring.ipynb` Estes cadernos são usados como um exemplo de conversão da experimentação para a produção. Pode encontrar estes cadernos em [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation).
- Instale o `nbconvert`. Siga apenas as instruções de instalação sob a secção __Instalação nbconvertna__ página [de instalação.](https://nbconvert.readthedocs.io/en/latest/install.html)

## <a name="remove-all-nonessential-code"></a>Remova todo o código não essencial

Alguns códigos escritos durante a experimentação destinam-se apenas a fins exploratórios. Por conseguinte, o primeiro passo para converter o código experimental em código de produção é remover este código não essencial. A remoção do código não essencial também tornará o código mais manejável. Nesta secção, removerá o código `experimentation/Diabetes Ridge Regression Training.ipynb` do caderno. As declarações que `X` `y` imprimem `features.describe` a forma e a chamada da célula são apenas para exploração de dados e podem ser removidas. Após a remoção `experimentation/Diabetes Ridge Regression Training.ipynb` do código não essencial, deve parecer o seguinte código sem marcação:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Refactor código em funções

Em segundo lugar, o código Jupyter tem de ser refactorado em funções. A refactoring do código em funções facilita o teste da unidade e torna o código mais manejável. Nesta secção, irá refactor:

- O caderno de treino de`experimentation/Diabetes Ridge Regression Training.ipynb`regressão de Diabetes Ridge.
- O caderno de pontuação de`experimentation/Diabetes Ridge Regression Scoring.ipynb`regressão de Diabetes Ridge.

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refactor Diabetes Ridge Regressão Caderno de treino em funções

Em, `experimentation/Diabetes Ridge Regression Training.ipynb`complete os seguintes passos:

1. Criar uma `split_data` função chamada para dividir o quadro de dados em dados de teste e de treino. A função deve `df` tomar o quadro de dados como parâmetro `train` `test`e devolver um dicionário contendo as teclas e .

    Mova o código sob os Conjuntos de *Formação e Validação de Dados Divididos,* indo para a função e modifique-o `split_data` para devolver o `data` objeto.

1. Crie uma `train_model`função chamada `data` , `args` que pega nos parâmetros e devolve um modelo treinado.

    Desloque o código sob o `train_model` modelo de *formação em formação de* título e modifique-o para devolver o `reg_model` objeto. Retire `args` o dicionário, os valores `args` virão do parâmetro.

1. Criar uma `get_model_metrics`função chamada `reg_model` , `data`que leva parâmetros e , e avalia o modelo e depois devolve um dicionário de métricas para o modelo treinado.

    Mova o código sob o modelo de `get_model_metrics` validação no conjunto `metrics` de *validação,* indo para a função e modifique-o para devolver o objeto.

As três funções devem ser as seguintes:

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

Ainda `experimentation/Diabetes Ridge Regression Training.ipynb`dentro, complete os seguintes passos:

1. Crie uma `main`nova função chamada , que não tem parâmetros e não devolve nada.
1. Mova o código sob a rubrica `main` "Dados de Carga" para a função.
1. Adicione invocações para as funções recentemente escritas na `main` função:
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. Mova o código sob a rubrica `main` "Save Model" para a função.

A `main` função deve parecer o seguinte código:

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

Nesta fase, não deve haver nenhum código restante no caderno que não esteja em função, além de declarações de importação na primeira célula.

Adicione uma declaração `main` que chame a função.

```python
main()
```

Após refactoring, `experimentation/Diabetes Ridge Regression Training.ipynb` deve parecer o seguinte código sem a marcação:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refactor Diabetes Ridge Regressão Reporcaderno em funções

Em, `experimentation/Diabetes Ridge Regression Scoring.ipynb`complete os seguintes passos:

1. Crie uma `init`nova função chamada , que não tem parâmetros e não devolve nada.
1. Copie o código no "Modelo `init` de Carga" que se insere na função.

A `init` função deve parecer o seguinte código:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Uma `init` vez criada a função, substitua todo o código sob `init` a rubrica "Modelo de Carga" por uma única chamada para o seguinte:

```python
init()
```

Em, `experimentation/Diabetes Ridge Regression Scoring.ipynb`complete os seguintes passos:

1. Criar uma nova `run`função `raw_data` `request_headers` chamada , que leva e como parâmetros e devolve um dicionário de resultados da seguinte forma:

    ```python
    {"result": result.tolist()}
    ```

1. Copie o código nas rubricas "Prepare mato `run` dados" e "Dados de Pontuação" na função.

    A `run` função deve parecer o seguinte código (Lembre-se `raw_data` `request_headers`de remover as declarações `run` que definiram as variáveis e , que serão usadas mais tarde quando a função for chamada):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Uma `run` vez criada a função, substitua todo o código nas rubricas "Prepare mato dados" e "Dados de Pontuação" pelo seguinte código:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

O código anterior define `raw_data` `request_header`variáveis `run` e, `raw_data` `request_header`chama a função com e, e imprime as previsões.

Após refactoring, `experimentation/Diabetes Ridge Regression Scoring.ipynb` deve parecer o seguinte código sem a marcação:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Combine funções relacionadas em ficheiros Python

Em terceiro lugar, as funções relacionadas precisam de ser fundidas em ficheiros Python para ajudar melhor a reutilizar o código. Nesta secção, estará a criar ficheiros Python para os seguintes cadernos:

- O caderno de treino de`experimentation/Diabetes Ridge Regression Training.ipynb`regressão de Diabetes Ridge.
- O caderno de pontuação de`experimentation/Diabetes Ridge Regression Scoring.ipynb`regressão de Diabetes Ridge.

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Crie o ficheiro Python para o caderno de treino de regressão de Diabetes Ridge

Converta o seu caderno num script executável executando a `nbconvert` seguinte declaração `experimentation/Diabetes Ridge Regression Training.ipynb`num pedido de comando, que utiliza a embalagem e o caminho de:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Uma vez convertido o `train.py`caderno para , remova quaisquer comentários indesejados. Substitua a `main()` chamada no final do ficheiro por uma invocação condicional como o seguinte código:

```python
if __name__ == '__main__':
    main()
```

O `train.py` seu ficheiro deve parecer o seguinte código:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

`train.py`pode agora ser invocado a `python train.py`partir de um terminal funcionando .
As funções `train.py` também podem ser chamadas de outros ficheiros.

O `train_aml.py` ficheiro encontrado `diabetes_regression/training` no diretório do repositório MLOpsPython `train.py` chama as funções definidas no contexto de uma experiência de aprendizagem automática Azure. As funções também podem ser chamadas em testes unitários, cobertos posteriormente neste guia.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Crie o ficheiro Python para o notebook de pontuação de Regressão de Diabetes Ridge

Dissimular o seu caderno para um script executável executando a seguinte declaração num pedido de comando que utiliza o `nbconvert` pacote e o caminho de: `experimentation/Diabetes Ridge Regression Scoring.ipynb`

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Uma vez convertido o `score.py`caderno para , remova quaisquer comentários indesejados. O `score.py` seu ficheiro deve parecer o seguinte código:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

A `model` variável tem de ser global para que seja visível em todo o guião. Adicione a seguinte declaração no `init` início da função:

```python
global model
```

Depois de adicionar a `init` declaração anterior, a função deve parecer o seguinte código:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Criar testes unitários para cada ficheiro Python

Em quarto lugar, crie testes unitários para as suas funções Python. Os testes de unidade protegem o código contra regressões funcionais e facilitam a sua manutenção. Nesta secção, estará a criar testes unitários `train.py`para as funções em .

`train.py`contém múltiplas funções, mas só criaremos `train_model` um único teste de unidade para a função usando a estrutura Pytest neste tutorial. Pytest não é o único quadro de testes da unidade Python, mas é um dos mais usados. Para mais informações, visite [Pytest.](https://pytest.org)

Um teste de unidade geralmente contém três ações principais:

- Organizar objeto - criar e configurar objetos necessários
- Agir sobre um objeto
- Afirmar o que se espera

O teste da `train_model` unidade irá ligar com alguns dados `train_model` e argumentos codificados, e validar que agiu como esperado usando o modelo treinado resultante para fazer uma previsão e comparando essa previsão com um valor esperado.

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>Passos seguintes

Agora que entende como se converter de uma experiência para código de produção, consulte os seguintes links para mais informações e próximos passos:

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md): Construa um oleoduto CI/CD para treinar, avaliar e implementar o seu próprio modelo utilizando pipelines Azure e Machine Learning Azure
+ [Monitor Azure ML executa e métricas](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
+ [Monitorizar e recolher dados de pontos finais do serviço web ml](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
