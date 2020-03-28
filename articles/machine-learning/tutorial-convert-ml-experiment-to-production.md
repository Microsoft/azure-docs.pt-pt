---
title: Converter código de experiência de aprendizagem automática para código de produção
titleSuffix: Azure Machine Learning
description: Aprenda a converter o código experimental de aprendizagem automática para o código de produção utilizando o modelo de código MLOpsPython.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/13/2020
ms.openlocfilehash: f40c2b5f7134458b3f8cb492652bebf14388634c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477141"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Tutorial: Converter código experimental ML para código de produção

Um projeto de aprendizagem automática requer experimentação onde hipóteses são testadas com ferramentas ágeis como o Jupyter Notebook usando conjuntos de dados reais. Uma vez que o modelo esteja pronto para a produção, o código-modelo deve ser colocado num repositório de código de produção. Em alguns casos, o código-modelo deve ser convertido para scripts Python a colocar no repositório do código de produção. Este tutorial abrange uma abordagem recomendada sobre como exportar código de experimentação para scripts Python.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Código não essencial limpo
> * Refactor Jupyter Notebook código em funções
> * Criar scripts Python para tarefas relacionadas
> * Criar testes de unidade

## <a name="prerequisites"></a>Pré-requisitos

- Gere o [modelo MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) e use os `experimentation/Diabetes Ridge Regression Training.ipynb` e cadernos. `experimentation/Diabetes Ridge Regression Scoring.ipynb` Estes cadernos são usados como um exemplo de conversão da experimentação para a produção. Pode encontrar estes cadernos em [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation).
- Instale o nbconvert. Siga apenas as instruções de instalação sob a secção __Instalação nbconvertna__ página [de instalação.](https://nbconvert.readthedocs.io/en/latest/install.html)

## <a name="remove-all-nonessential-code"></a>Remova todo o código não essencial

Alguns códigos escritos durante a experimentação destinam-se apenas a fins exploratórios. Por conseguinte, o primeiro passo para converter o código experimental em código de produção é remover este código não essencial. A remoção do código não essencial também tornará o código mais manejável. Nesta secção, removerá o código `experimentation/Diabetes Ridge Regression Training.ipynb` do caderno. As declarações que `X` `y` imprimem `features.describe` a forma e a chamada da célula são apenas para exploração de dados e podem ser removidas. Após a remoção `experimentation/Diabetes Ridge Regression Training.ipynb` do código não essencial, deve parecer o seguinte código sem marcação:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib

X, y = load_diabetes(return_X_y=True)

X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

alpha = 0.5

reg = Ridge(alpha=alpha)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg.predict(data["test"]["X"])
print("mse", mean_squared_error(preds, data["test"]["y"]))

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Refactor código em funções

Em segundo lugar, o código Jupyter tem de ser refactorado em funções. A refactoring do código em funções facilita o teste da unidade e torna o código mais manejável. Nesta secção, irá refactor:

- O caderno de treino de`experimentation/Diabetes Ridge Regression Training.ipynb`regressão de Diabetes Ridge.
- O caderno de pontuação de`experimentation/Diabetes Ridge Regression Scoring.ipynb`regressão de Diabetes Ridge.

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refactor Diabetes Ridge Regressão Caderno de treino em funções

Em, `experimentation/Diabetes Ridge Regression Training.ipynb`complete os seguintes passos:

1. Crie uma `train_model`função chamada `data` , `alpha` que pega nos parâmetros e devolve um modelo.
1. Copie o código sob as rubricas "Modelo de Comboio no Conjunto `train_model` de Formação" e "Modelo de Validação no Conjunto de Validação" na função.

A `train_model` função deve parecer o seguinte código:

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

Uma `train_model` vez criada a função, substitua o código nas rubricas "Modelo de Comboio no Conjunto de Formação" e "Modelo de Validação no Conjunto de Validação" pela seguinte declaração:

```python
reg = train_model(data, alpha)
```

A declaração anterior `train_model` chama `data` a `alpha` função passando os parâmetros e parâmetros e devolve o modelo.

Em, `experimentation/Diabetes Ridge Regression Training.ipynb`complete os seguintes passos:

1. Crie uma `main`nova função chamada , que não tem parâmetros e não devolve nada.
1. Copie o código nas rubricas "Dados de Carga", "Divida os dados `main` em conjuntos de formação e validação", e "Salvar o Modelo" na função.
1. Copie a chamada `train_model` recém-criada para a `main` função.

A `main` função deve parecer o seguinte código:

```python
def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)
```

Assim `main` que a função for criada, substitua todo o código nas rubricas "Dados de Carga", "Dados divididos em Conjuntos de Formação e Validação", e "Save Model" juntamente com a chamada recém-criada para `train_model` a seguinte declaração:

```python
main()
```

Após refactoring, `experimentation/Diabetes Ridge Regression Training.ipynb` deve parecer o seguinte código sem a marcação:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

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

Converta o seu caderno num script executável executando a seguinte declaração num `experimentation/Diabetes Ridge Regression Training.ipynb`pedido de comando, que utiliza o pacote nbconvert e o caminho de:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Uma vez convertido o `train.py`caderno para , remova todos os comentários. O `train.py` seu ficheiro deve parecer o seguinte código:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():
    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

O `train.py` ficheiro encontrado `diabetes_regression/training` no diretório do repositório MLOpsPython suporta argumentos `build_id`de `model_name`linha `alpha`de comando (nomeadamente , e ). O suporte para argumentos de linha `train.py` de comando pode ser `alpha` adicionado ao seu ficheiro para suportar nomes e valores dinâmicos do modelo, mas não é necessário que o código execute com sucesso.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Crie o ficheiro Python para o notebook de pontuação de Regressão de Diabetes Ridge

Dissimular o seu caderno para um script executável executando a seguinte `experimentation/Diabetes Ridge Regression Scoring.ipynb`declaração num pedido de comando que utiliza o pacote nbconvert e o caminho de:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Uma vez convertido o `score.py`caderno para , remova todos os comentários. O `score.py` seu ficheiro deve parecer o seguinte código:

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

A `train_model` função precisa de modificação para instantaneamente um modelo variável global para que seja visível em todo o script. Adicione a seguinte declaração no `init` início da função:

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

Em quarto lugar, é necessário criar testes unitários para cada ficheiro Python, o que torna o código mais robusto e mais fácil de manter. Nesta secção, estará a criar um teste de unidade `train.py`para uma das funções em .

`train.py`contém duas funções: `train_model` e `main`. Cada função precisa de um teste de unidade, mas `train_model` só criaremos um único teste de unidade para a função usando a estrutura Pytest neste tutorial. Pytest não é o único quadro de testes da unidade Python, mas é um dos mais usados. Para mais informações, visite [Pytest.](https://pytest.org)

Um teste de unidade geralmente contém três ações principais:

- Organizar objeto - criar e configurar objetos necessários
- Agir sobre um objeto
- Afirmar o que se espera

Uma condição `train_model` comum `data` para `alpha` quando e um valor são passados. O resultado esperado `Ridge.train` é `Ridge.predict` que as funções e funções devem ser chamadas. Uma vez que os métodos de treino `Ridge.train` de aprendizagem automática muitas vezes não são rápidos, a chamada será ridicularizada. Porque o valor `Ridge.train` de retorno é um objeto `Ridge.predict`gozado, também vamos gozar. O teste `train_model` unitário para `data` testar `alpha` a passagem e `Ridge.train` `Ridge.predict` um valor com o resultado esperado e as funções a serem chamadas usando zombando e a estrutura Pytest deve parecer o seguinte código:

```python
import pytest
from code.training.train import train_model

class TestTrain:

    @staticmethod
    def test_train_model(mocker):
        # Arrange
        test_data = {"train": {"X": [[1, 2, 3]], "y": [0]},
                     "test": {"X": [[4, 5, 6]], "y": [0]}}
        test_alpha = 0.5
        mock_ridge_fit = mocker.patch('Ridge.fit')
        mock_ridge_predict = mocker.patch('Ridge.predict')

        # Act
        train_model(test_data, test_alpha)

        # Assert
        mock_ridge_fit.assert_called()
        mock_ridge_predict.assert_called()
```

## <a name="use-your-own-model-with-mlopspython-code-template"></a>Use o seu próprio modelo com modelo de código MLOpsPython

Se tem seguido os passos deste guia, terá um conjunto de scripts que se relacionam com os scripts de comboio/pontuação/teste disponíveis no repositório MLOpsPython.  De acordo com a estrutura acima mencionada, os seguintes passos irão percorrer o que é necessário para usar estes ficheiros para o seu próprio projeto de aprendizagem automática:

1. Siga o guia MLOpsPython [Getting Started](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md)
2. Siga as [instruções](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md) mlOpsPython para criar o seu ponto de partida do projeto
3. Substituir o Código de Formação
4. Substituir o Código de Pontuação
5. Atualizar o Código de Avaliação

### <a name="follow-the-getting-started-guide"></a>Siga o Guia de Início
Seguir o guia [Getting Started](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md) é necessário ter a infraestrutura de suporte e os oleodutos para executar MLOpsPython.

### <a name="follow-the-bootstrap-instructions"></a>Siga as instruções da Bootstrap

O guia de [repositório Sabotador Do MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md) irá ajudá-lo a preparar rapidamente o repositório para o seu projeto.

**Nota:** Uma vez que o script bootstrap mudará o nome da pasta diabetes_regression para `[project name]` o nome do projeto à sua escolha, vamos referir-nos ao seu projeto como quando os caminhos estiverem envolvidos.

### <a name="replace-training-code"></a>Substituir código de formação

A substituição do código utilizado para treinar o modelo e a remoção ou substituição dos respetivos ensaios unitários é necessária para que a solução funcione com o seu próprio código. Siga minuciosamente estes passos:

1. Substitua `[project name]/training/train.py`. Este guião treina o seu modelo localmente ou no computacional Azure ML.
1. Remover ou substituir os testes da unidade de treino encontrados em`[project name]/training/test_train.py`

### <a name="replace-score-code"></a>Substituir código de pontuação

Para que o modelo forneça capacidades de inferência em tempo real, o código de pontuação precisa de ser substituído. O modelo MLOpsPython usa o código de pontuação para implementar o modelo para fazer pontuação em tempo real em aplicações ACI, AKS ou Web. Se quiser continuar a marcar, substitua. `[project name]/scoring/score.py`

### <a name="update-evaluation-code"></a>Código de Avaliação de Atualização

O modelo MLOpsPython usa o guião evaluate_model para comparar o desempenho do modelo recém-treinado e o modelo de produção atual baseado no Error Quadrado Médio. Se o desempenho do modelo recém-treinado for melhor do que o modelo de produção atual, então os gasodutos continuam. Caso contrário, os oleodutos são cancelados. Para manter a avaliação, `mse` `[project name]/evaluate/evaluate_model.py` substitua todos os casos de in com a métrica que deseja.

Para se livrar da avaliação, coloque `RUN_EVALUATION` a `.pipelines/[project name]-variables-template.yml` `false`variável do gasoduto DevOps em .

## <a name="next-steps"></a>Passos seguintes

Agora que compreende como se converter de uma experiência para código de produção, use os seguintes links para aprender a monitorizar as experiências e os modelos implementados como serviços web:

> [!div class="nextstepaction"]
> [Monitor Azure ML experimenta executa e metrics](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [Monitor e recolhe dados de pontos finais do serviço web ML](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
