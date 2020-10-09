---
title: Converter código de caderno em scripts Python
titleSuffix: Azure Machine Learning
description: Transforme os seus cadernos experimentais de aprendizagem automática em código pronto para a produção utilizando o modelo de código MLOpsPython. Em seguida, pode testar, implantar e automatizar esse código.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: 3a6ce5860704e6fd16b79fc253650dd45ec743e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87852621"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>Tutorial: Converter experiências ml para produzir código Python

Neste tutorial, você aprende a converter cadernos Juptyer em scripts Python para torná-lo test e automação amigável usando o modelo de código MLOpsPython e Azure Machine Learning. Tipicamente, este processo é usado para pegar na experimentação/código de treino de um caderno Juptyer e convertê-lo em scripts Python. Esses scripts podem então ser usados testes e automação ci/CD no seu ambiente de produção. 

Um projeto de aprendizagem automática requer experimentação onde as hipóteses são testadas com ferramentas ágeis como o Jupyter Notebook usando conjuntos de dados reais. Uma vez que o modelo esteja pronto para ser produtivo, o código modelo deve ser colocado num repositório de código de produção. Em alguns casos, o código-modelo deve ser convertido em scripts Python para ser colocado no repositório do código de produção. Este tutorial abrange uma abordagem recomendada sobre como exportar código de experimentação para scripts Python.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Código não essencial limpo
> * Refactor Jupyter Código de caderno em funções
> * Criar scripts Python para tarefas relacionadas
> * Criar testes de unidade

## <a name="prerequisites"></a>Pré-requisitos

- Gere o [modelo MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) e use os `experimentation/Diabetes Ridge Regression Training.ipynb` cadernos e `experimentation/Diabetes Ridge Regression Scoring.ipynb` cadernos. Estes cadernos são usados como um exemplo de conversão da experimentação para a produção. Pode encontrar estes cadernos [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation) em.
- Instale o `nbconvert`. Siga apenas as instruções de instalação na secção __Instalação nbconvert__ na página [de Instalação.](https://nbconvert.readthedocs.io/en/latest/install.html)

## <a name="remove-all-nonessential-code"></a>Remover todo o código não-essencial

Algum código escrito durante a experimentação destina-se apenas a fins exploratórios. Por conseguinte, o primeiro passo para converter o código experimental em código de produção é remover este código não-essencial. A remoção do código não essencial também tornará o código mais sustentável. Nesta secção, removerá o código do `experimentation/Diabetes Ridge Regression Training.ipynb` caderno. As declarações que imprimem a forma `X` e a chamada celular são `y` `features.describe` apenas para exploração de dados e podem ser removidas. Após a remoção do código não-essencial, `experimentation/Diabetes Ridge Regression Training.ipynb` deve parecer o seguinte código sem marcação:

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

## <a name="refactor-code-into-functions"></a>Código do refactor em funções

Segundo, o código Jupyter precisa de ser refactorado em funções. A refacção do código em funções facilita o teste de unidade e torna o código mais manejável. Nesta secção, você vai refactor:

- O caderno de treino de regressão da Diabetes `experimentation/Diabetes Ridge Regression Training.ipynb` Ridge.
- O caderno de pontuação da Regressão da Diabetes `experimentation/Diabetes Ridge Regression Scoring.ipynb` Ridge.

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Caderno de treino de regressão da Refactor Diabetes Ridge em funções

Em `experimentation/Diabetes Ridge Regression Training.ipynb` , completar os seguintes passos:

1. Crie uma função chamada `split_data` para dividir o quadro de dados em dados de teste e comboio. A função deve tomar o quadro de dados `df` como parâmetro, e devolver um dicionário que contenha as teclas `train` e `test` .

    Mover o código sob os *Dados Divididos em Conjuntos de Treino e Validação* que vão para a `split_data` função e modifique-o para devolver o `data` objeto.

1. Crie uma função chamada `train_model` , que pega nos parâmetros e devolve um modelo `data` `args` treinado.

    Mover o código de acordo com o modelo de treino de posição *de função definido* para a `train_model` função e modificá-lo para devolver o `reg_model` objeto. Remova o `args` dicionário, os valores virão do `args` parâmetro.

1. Crie uma função chamada `get_model_metrics` , que toma parâmetros `reg_model` `data` e, e avalia o modelo, em seguida, devolve um dicionário de métricas para o modelo treinado.

    Mover o código sob o *Modelo Validado no Conjunto de Validação* que vai para a `get_model_metrics` função e modifique-o para devolver o `metrics` objeto.

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

Ainda em `experimentation/Diabetes Ridge Regression Training.ipynb` , complete os seguintes passos:

1. Crie uma nova função chamada `main` , que não tem parâmetros e não devolve nada.
1. Mova o código sob o título "Dados de Carga" para a `main` função.
1. Adicione invocações para as funções recém-escritas na `main` função:
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
1. Mova o código sob o título "Save Model" para a `main` função.

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

Nesta fase, não deve haver nenhum código no caderno que não esteja em função, além de declarações de importação na primeira célula.

Adicione uma declaração que chame a `main` função.

```python
main()
```

Após a refacagem, `experimentation/Diabetes Ridge Regression Training.ipynb` deve parecer o seguinte código sem a marcação:

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

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refactor Diabetes Ridge Regression Scoring notebook em funções

Em `experimentation/Diabetes Ridge Regression Scoring.ipynb` , completar os seguintes passos:

1. Crie uma nova função chamada `init` , que não tem parâmetros e não devolve nada.
1. Copie o código sob o título "Modelo de Carga" para a `init` função.

A `init` função deve parecer o seguinte código:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Uma vez criada a `init` função, substitua todo o código na rubrica "Modelo de Carga" por uma única chamada para `init` o seguinte:

```python
init()
```

Em `experimentation/Diabetes Ridge Regression Scoring.ipynb` , completar os seguintes passos:

1. Criar uma nova função chamada `run` , que toma e como `raw_data` `request_headers` parâmetros e devolve um dicionário de resultados da seguinte forma:

    ```python
    {"result": result.tolist()}
    ```

1. Copie o código nos títulos "Preparar dados" e "Dados de Pontuação" para a `run` função.

    A `run` função deve parecer o seguinte código (Lembre-se de remover as declarações que definem as variáveis `raw_data` e , que `request_headers` serão usadas mais tarde quando a `run` função for chamada):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Uma vez criada a `run` função, substitua todos os códigos nos títulos "Preparar dados" e "Dados de Pontuação" pelo seguinte código:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

O código anterior define variáveis `raw_data` `request_header` e, chama a `run` função com `raw_data` e , e `request_header` imprime as previsões.

Após a refacagem, `experimentation/Diabetes Ridge Regression Scoring.ipynb` deve parecer o seguinte código sem a marcação:

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

Em terceiro lugar, as funções relacionadas precisam de ser fundidas em ficheiros Python para melhor ajudar a reutilização do código. Nesta secção, estará a criar ficheiros Python para os seguintes cadernos:

- O caderno de treino de regressão da Diabetes `experimentation/Diabetes Ridge Regression Training.ipynb` Ridge.
- O caderno de pontuação da Regressão da Diabetes `experimentation/Diabetes Ridge Regression Scoring.ipynb` Ridge.

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Criar ficheiro Python para o notebook de Treino de Regressão diabetes Ridge

Converta o seu caderno num script executável executando a seguinte declaração num pedido de comando, que utiliza o `nbconvert` pacote e o caminho `experimentation/Diabetes Ridge Regression Training.ipynb` de:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Uma vez que o caderno tenha sido convertido para `train.py` , remova quaisquer comentários indesejados. Substitua a chamada `main()` no final do ficheiro por uma invocação condicional como o seguinte código:

```python
if __name__ == '__main__':
    main()
```

O seu `train.py` ficheiro deve parecer o seguinte código:

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

`train.py` pode agora ser invocado a partir de um terminal executando `python train.py` .
As funções de `train.py` outros ficheiros também podem ser chamadas.

O `train_aml.py` ficheiro encontrado no `diabetes_regression/training` diretório do repositório MLOpsPython chama as funções definidas `train.py` no contexto de uma experiência de Aprendizagem automática Azure. As funções também podem ser chamadas em testes de unidade, cobertos posteriormente neste guia.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Crie o ficheiro Python para o caderno de pontuação de regressão da Diabetes Ridge

Encosta o seu caderno a um script executável executando a seguinte declaração num pedido de comando que utiliza o `nbconvert` pacote e o caminho `experimentation/Diabetes Ridge Regression Scoring.ipynb` de:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Uma vez que o caderno tenha sido convertido para `score.py` , remova quaisquer comentários indesejados. O seu `score.py` ficheiro deve parecer o seguinte código:

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

A `model` variável precisa ser global para que seja visível em todo o roteiro. Adicione a seguinte declaração no início da `init` função:

```python
global model
```

Após a adição da declaração anterior, a `init` função deve parecer o seguinte código:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Criar testes de unidade para cada ficheiro Python

Quarto, crie testes unitários para as suas funções Python. Os testes de unidade protegem o código contra regressões funcionais e facilitam a manutenção. Nesta secção, irá criar testes unitários para as funções em `train.py` .

`train.py` contém múltiplas funções, mas só criaremos um teste unitário único para a `train_model` função usando a estrutura Pytest neste tutorial. O Pytest não é o único quadro de testes da unidade Python, mas é um dos mais utilizados. Para mais informações, visite [Pytest.](https://pytest.org)

Um teste de unidade geralmente contém três ações principais:

- Organize o objeto - criando e configurando objetos necessários
- Agir num objeto
- Afirmar o que se espera

O teste de unidade irá ligar `train_model` com alguns dados e argumentos codificados, e validar que `train_model` agiu como esperado usando o modelo treinado resultante para fazer uma previsão e comparando essa previsão com um valor esperado.

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

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md): Construa um oleoduto CI/CD para treinar, avaliar e implementar o seu próprio modelo utilizando pipelines Azure e Azure Machine Learning
+ [Monitor Azure ML experimenta e métricas](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
+ [Monitorize e recolha dados dos pontos finais do serviço web ML](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
