---
title: Auto-preparar um modelo de previsão de séries de tempo
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o serviço Azure Machine Learning para preparar uma série de tempo, previsão através de modelo de regressão automatizada de aprendizagem automática.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 32f96a28e027bfd0e65d934bb47bb98400af459d
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360729"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Auto-preparar um modelo de previsão de séries de tempo

Neste artigo, irá aprender a preparar um modelo de previsão de séries de tempo da regressão com aprendizagem automática no serviço Azure Machine Learning. Configurar um modelo de previsão é semelhante à configuração de um modelo de regressão padrão com aprendizagem automática, mas alguns passos de opções e o processamento prévio de configuração existem para trabalhar com dados de séries de tempo. Os exemplos seguintes mostram-lhe como para:

* Preparar dados para a Modelagem de série de tempo
* Configurar parâmetros de séries de tempo específicos numa [ `AutoMLConfig` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) objeto
* Executar predições com dados de séries temporais

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho de serviço do Azure Machine Learning. Para criar a área de trabalho, consulte [criar uma área de trabalho do serviço do Azure Machine Learning](setup-create-workspace.md).
* Este artigo pressupõe uma familiaridade básica com a configuração de uma experimentação de aprendizagem automática. Siga os [tutorial](tutorial-auto-train-models.md) ou [procedimentos](how-to-configure-auto-train.md) para ver a máquina automatizada básica aprender os padrões de design de experimentação.

## <a name="preparing-data"></a>Preparação de dados

A diferença mais importante entre um tipo de tarefa de regressão e regressão de previsão, tipo de tarefa dentro de aprendizagem automática é incluindo uma funcionalidade nos dados que representa uma série de tempo válida. Uma série de tempo regulares tem uma frequência bem definida e consistente e tem um valor em todos os pontos de exemplo num intervalo de tempo contínuo. Considere o seguinte instantâneo de um ficheiro `sample.csv`.

    week_starting,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/10/2018,A,2300,37
    9/10/2018,B,550,37
    9/17/2018,A,2100,38
    9/17/2018,B,650,38
    9/24/2018,A,2400,39
    9/24/2018,B,700,39
    10/1/2018,A,2450,40
    10/1/2018,B,650,40

Este conjunto de dados é um exemplo simples de dados de vendas semanais para uma empresa que tem dois arquivos diferentes, A e B. Além disso, há um recurso para `week_of_year` que permitirá que o modelo detetar a sazonalidade semanal. O campo `week_starting` representa uma série de tempo limpa com frequência semanal e o campo `sales_quantity` é a coluna de destino para a execução de previsões de indisponibilidade. Ler os dados para um Pandas dataframe, em seguida, utilize o `to_datetime` função para garantir que a série de tempo é um `datetime` tipo.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["week_starting"] = pd.to_datetime(data["week_starting"])
```

Neste caso os dados já estão classificados ascendente pelo campo tempo `week_starting`. No entanto, quando configurar uma experimentação, certifique-se da que coluna de tempo pretendido é ordenada por ordem para criar uma série de tempo válido ascendente. Partem do princípio dos que dados contêm 1000 registos e fazer uma divisão determinística nos dados de treinamento de criar e testar os conjuntos de dados. Em seguida, separar o campo de destino `sales_quantity` criar o comboio de predição e o teste define.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> Quando preparar um modelo para prever futuros valores, certifique-se todos os recursos utilizados na formação podem ser utilizados durante a execução de previsões para o horizonte pretendido. Por exemplo, ao criar um pedido de previsão, incluindo um recurso para o preço das ações atual poderia em massa aumentar a precisão de treinamento. No entanto, se pretende prever com um longo horizonte, pode não ser capaz de prever com precisão os valores de ações futuras correspondente para futuros pontos da série de tempo e precisão do modelo poderia ser prejudicado.

## <a name="configure-experiment"></a>Configurar experimentação

Para tarefas de previsão, aprendizagem automática utiliza passos de pré-processamento e estimativa que são específicos para dados de séries temporais. Os seguintes passos de pré-processamento serão executados:

* Detete a série de tempo frequência (por exemplo, por hora, diária, semanal) de exemplo e criar novos registos para a ausência de pontos de tempo para fazer com que a série contínua.
* Impute valores em falta no destino (por meio do preenchimento de encaminhamento) e colunas de funcionalidades (usando valores de coluna mediano)
* Criar recursos com base no intervalo de agregação para ativar os efeitos fixos em diferentes séries
* Criar recursos baseados no tempo para ajudar a aprender os padrões sazonais
* Codificar categóricas variáveis para quantidades numéricas

O `AutoMLConfig` objeto define as definições e os dados necessários para uma tarefa de aprendizagem automática de máquina. Semelhante a um problema de regressão, define os parâmetros de treinamento padrão, como o tipo de tarefa, o número de iterações, os dados, de formação e o número de validações entre. Para tarefas de previsão, existem parâmetros adicionais que devem ser definidos que afetam a experimentação. A tabela seguinte explica cada parâmetro e sua utilização.

| Param | Descrição | Necessário |
|-------|-------|-------|
|`time_column_name`|Utilizado para especificar a coluna de datetime nos dados de entrada utilizados para criar a série de tempo e inferir a frequência.|✓|
|`grain_column_names`|Nome (s) definir grupos de séries individuais nos dados de entrada. Se o intervalo de agregação não está definido, o conjunto de dados é considerado como uma série de tempo.||
|`max_horizon`|Máximo pretendido horizonte previsão em unidades de frequência de séries de tempo.|✓|

Crie as definições de séries de tempo como um objeto de dicionário. Definir o `time_column_name` para o `week_starting` campo no conjunto de dados. Definir o `grain_column_names` parâmetro para se certificar de que **dois grupos de séries de tempo de separados** criados para nossos dados; uma para o arquivo A e B. por fim, defina o `max_horizon` para 50 para prever para o teste de inteiro definida.

```python
time_series_settings = {
    "time_column_name": "week_starting",
    "grain_column_names": ["store"],
    "max_horizon": 50
}
```

Agora, crie uma norma `AutoMLConfig` objeto, especificando o `forecasting` tipo de tarefas e submeter a experimentação. Quando o modelo estiver concluído, obter a iteração de execução melhor.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

> [!NOTE]
> Para o procedimento de (CV) de validação cruzada, dados de séries temporais podem violar as suposições de estatísticas básicas da estratégia de validação cruzada do canonical K fases, para que aprendizagem automatizada implementa um procedimento de validação de origem sem interrupção para criar subconjuntos de validação cruzada para dados de séries temporais. Para utilizar este procedimento, especifique a `n_cross_validations` parâmetro no `AutoMLConfig` objeto. Pode ignorar a validação e usar sua própria validação define-se com o `X_valid` e `y_valid` parâmetros.

## <a name="forecasting-with-best-model"></a>Com o melhor modelo de previsão

Utilize a melhor iteração do modelo para prever valores para o conjunto de dados de teste.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

Calcular RMSE (erro ao quadrado média de raiz) entre o `y_test` valores reais e os valores previstos no `y_pred`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Agora que o geral determinou precisão do modelo, a próxima etapa mais realista é usar o modelo para prever valores futuras desconhecidos. Simplesmente fornecer um conjunto de dados no mesmo formato que o conjunto de teste `X_test` mas com datetimes futuros e a predição resultante conjunto é os valores previstos para cada passo de séries de tempo. Suponha que os últimos registos de séries de tempo no conjunto de dados foram para o início da semana de 12/31/2018. Para prever a procura para a próxima semana (ou os períodos conforme necessário para a previsão, < = `max_horizon`), criar um único registo de série de tempo para cada loja para o início da semana 01/07/2019.

    week_starting,store,week_of_year
    01/07/2019,A,2
    01/07/2019,A,2

Repita os passos necessários para carregar estes dados futuros para um dataframe e, em seguida, executar `best_run.predict(X_test)` para prever futuros valores.

> [!NOTE]
> Não não possível prever a valores para o número de períodos maior do que o `max_horizon`. O modelo tem de ser novamente preparado com uma maior horizonte para prever futuros valores para além de horizonte atual.

## <a name="next-steps"></a>Passos Seguintes

* Siga os [tutorial](tutorial-auto-train-models.md) aprender a criar experimentações com automatizada aprendizagem automática.
* Ver os [do Azure Machine Learning SDK para Python](https://aka.ms/aml-sdk) documentação de referência.
