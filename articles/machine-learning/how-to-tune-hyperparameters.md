---
title: Hiperparametro afinando um modelo
titleSuffix: Azure Machine Learning
description: Automatize a afinação de hiperparímetros para a aprendizagem profunda e modelos de aprendizagem automática usando Azure Machine Learning.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: e9f9f73a8e0dbc851efdba07bf1e103f58ae9e75
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133866"
---
# <a name="hyperparameter-tuning-a-model-with-azure-machine-learning"></a>Hiperparametro afinando um modelo com Azure Machine Learning


Automatizar uma afinação eficiente do hiperparametro utilizando o pacote Azure Machine Learning [HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive?preserve-view=true&view=azure-ml-py). Saiba como completar os passos necessários para sintonizar hiperparímetros com o [Azure Machine Learning SDK:](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py)

1. Definir o espaço de pesquisa de parâmetros
1. Especifique uma métrica primária para otimizar  
1. Especificar a política de rescisão antecipada para execuções de baixo desempenho
1. Alocar recursos
1. Lançar uma experiência com a configuração definida
1. Visualizar as corridas de treino
1. Selecione a melhor configuração para o seu modelo

## <a name="what-is-hyperparameter-tuning"></a>O que é a sintonização hiperparítnica?

**Os hiperparmetros** são parâmetros ajustáveis que permitem controlar o processo de treino do modelo. Por exemplo, com redes neurais, você decide o número de camadas escondidas e o número de nós em cada camada. O desempenho do modelo depende fortemente de hiperparímetros.

 **A sintonização hiperparítnica**, também chamada **de otimização do hiperparímetro,** é o processo de encontrar a configuração de hiperparímetros que resulta no melhor desempenho. O processo é tipicamente computacionalmente caro e manual.

O Azure Machine Learning permite automatizar a afinação de hiperparímetros e executar experiências paralelas para otimizar eficientemente os hiperparímetros.


## <a name="define-the-search-space"></a>Definir o espaço de pesquisa

Sintonize os hiperparmetros explorando a gama de valores definidos para cada hiperparímetro.

Os hiperparmetros podem ser discretos ou contínuos, e tem uma distribuição de valores descritos por uma [expressão de parâmetro](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?preserve-view=true&view=azure-ml-py).

### <a name="discrete-hyperparameters"></a>Hiperparímetros discretos

Os hiperparmetros discretos são especificados como um `choice` entre valores discretos. `choice` pode ser:

* um ou mais valores separados por vírgula
* um `range` objeto
* qualquer objeto arbitrário `list`


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

Neste caso, `batch_size` um dos valores [16, 32, 64, 128] e `number_of_hidden_layers` leva um dos valores [1, 2, 3, 4].

Os seguintes hiperparmetros discretos avançados também podem ser especificados através de uma distribuição:

* `quniform(low, high, q)` - Devolve um valor como redondo (uniforme (baixo, alto) / q
* `qloguniform(low, high, q)` - Devolve um valor como redondo (exp(uniforme(baixo, alto)) / q
* `qnormal(mu, sigma, q)` - Devolve um valor como redondo (normal(mu, sigma) / q
* `qlognormal(mu, sigma, q)` - Devolve um valor como redondo (exp(normal(mu, sigma)) / q

### <a name="continuous-hyperparameters"></a>Hiperparímetros contínuos 

Os hiperparmetros contínuos são especificados como uma distribuição sobre uma gama contínua de valores:

* `uniform(low, high)` - Devolve um valor distribuído uniformemente entre baixo e alto
* `loguniform(low, high)` - Devolve um valor desenhado de acordo com exp (uniforme (baixo, alto)) de modo a que o logaritmo do valor de retorno seja distribuído uniformemente
* `normal(mu, sigma)` - Devolve um valor real que é normalmente distribuído com mu médio e sigma de desvio padrão
* `lognormal(mu, sigma)` - Devolve um valor desenhado de acordo com exp(mu,sigma)) de modo a que o logaritmo do valor de retorno seja normalmente distribuído

Um exemplo de uma definição de espaço de parâmetro:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Este código define um espaço de pesquisa com dois parâmetros - `learning_rate` e `keep_probability` . `learning_rate` tem uma distribuição normal com valor médio 10 e um desvio padrão de 3. `keep_probability` tem uma distribuição uniforme com um valor mínimo de 0,05 e um valor máximo de 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Amostragem do espaço do hiperparmetro

Especifique o método de amostragem de parâmetros para utilizar sobre o espaço do hiperparímetro. A Azure Machine Learning suporta os seguintes métodos:

* Amostragem aleatória
* Amostragem de grelha
* Amostragem bayesiana

#### <a name="random-sampling"></a>Amostragem aleatória

[A amostragem aleatória](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?preserve-view=true&view=azure-ml-py) suporta hiperparímetros discretos e contínuos. Apoia a rescisão precoce de execuções de baixo desempenho. Alguns utilizadores fazem uma pesquisa inicial com amostragem aleatória e depois refinam o espaço de pesquisa para melhorar os resultados.

Na amostragem aleatória, os valores do hiperparímetro são selecionados aleatoriamente a partir do espaço de pesquisa definido. 

```Python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import normal, uniform, choice
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Amostragem de grelha

[A amostragem da](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?preserve-view=true&view=azure-ml-py) grelha suporta hiperparímetros discretos. Use amostras de grelha se puder orçamentar para pesquisar exaustivamente o espaço de pesquisa. Suporta a rescisão precoce de execuções de baixo desempenho.

Realiza uma pesquisa de grelha simples sobre todos os valores possíveis. A amostragem da grelha só pode ser utilizada com `choice` hiperparímetros. Por exemplo, o seguinte espaço tem seis amostras:

```Python
from azureml.train.hyperdrive import GridParameterSampling
from azureml.train.hyperdrive import choice
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Amostragem bayesiana

[A amostragem bayesiana](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?preserve-view=true&view=azure-ml-py) baseia-se no algoritmo de otimização bayesiano. Recolhe amostras com base na forma como as amostras anteriores foram realizadas, de modo que novas amostras melhoram a métrica primária.

Recomenda-se a amostragem bayesiana se tiver orçamento suficiente para explorar o espaço do hiperparímetro. Para obter melhores resultados, recomendamos um número máximo de corridas superiores ou iguais a 20 vezes o número de hiperparímetros a ser afinado. 

O número de execuções simultâneas tem impacto na eficácia do processo de afinação. Um número menor de execuções simultâneas pode conduzir a uma melhor convergência de amostragem, uma vez que o menor grau de paralelismo aumenta o número de runs que beneficiam de execuções previamente concluídas.

A amostragem bayesiana só `choice` `uniform` suporta, e `quniform` distribui-se pelo espaço de pesquisa.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Especificar a métrica primária

Especifique a [métrica primária](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?preserve-view=true&view=azure-ml-py) que pretende que a sintonização do hiperparímetro otimize. Cada treino é avaliado para a métrica primária. A política de rescisão antecipada utiliza a métrica primária para identificar as execuções de baixo desempenho.

Especifique os seguintes atributos para a sua métrica primária:

* `primary_metric_name`: O nome da métrica primária precisa corresponder exatamente ao nome da métrica registada pelo script de treino
* `primary_metric_goal`: Pode ser `PrimaryMetricGoal.MAXIMIZE` ou determinar se a `PrimaryMetricGoal.MINIMIZE` métrica primária será maximizada ou minimizada na avaliação das execuções. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Esta amostra maximiza a "precisão".

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Métricas de registo para afinação de hiperparímetro

O script de treino para o seu modelo **deve** registar a métrica primária durante o treino do modelo para que o HyperDrive possa acessá-lo para afinação de hiperparímetros.

Registar a métrica primária no seu roteiro de treino com o seguinte corte de amostra:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

O roteiro de treino calcula o `val_accuracy` e regista-o como a "precisão" da métrica primária. Cada vez que a métrica é registada, é recebida pelo serviço de afinação do hiperparímetro. Cabe-lhe a si determinar a frequência dos relatórios.

Para obter mais informações sobre valores de registo em percursos de treino de modelos, consulte [Ativar o registo em treinos Azure ML](how-to-track-experiments.md).

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Especificar a política de rescisão antecipada

Terminar automaticamente com uma política de rescisão antecipada terminada automaticamente com uma política de rescisão antecipada. A rescisão precoce melhora a eficiência computacional.

Pode configurar os seguintes parâmetros que controlam quando uma política é aplicada:

* `evaluation_interval`: a frequência de aplicação da política. Cada vez que o guião de treino regista a métrica primária conta como um intervalo. Um `evaluation_interval` de 1 aplicará a apólice sempre que o roteiro de treino reportar a métrica primária. Um `evaluation_interval` de dois aplicará a apólice de vez em quando. Se não for especificado, `evaluation_interval` é definido para 1 por predefinição.
* `delay_evaluation`: atrasa a primeira avaliação política para um número especificado de intervalos. Este é um parâmetro opcional que evita a interrupção prematura dos treinos, permitindo que todas as configurações corram para um número mínimo de intervalos. Se especificado, a política aplica cada múltiplo de evaluation_interval que seja maior ou igual a delay_evaluation.

A Azure Machine Learning apoia as seguintes políticas de rescisão antecipada:
* [Política de bandidos](#bandit-policy)
* [Política de paragem mediana](#median-stopping-policy)
* [Política de seleção de truncação](#truncation-selection-policy)
* [Sem política de rescisão](#no-termination-policy-default)


### <a name="bandit-policy"></a>Política de bandidos

[A política de bandidos](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?preserve-view=true&view=azure-ml-py#&preserve-view=truedefinition) baseia-se no fator de folga/tempo de avaliação. O bandit termina em execuções em que a métrica primária não está dentro do fator de folga especificado/quantidade de folga em comparação com o melhor desempenho.

> [!NOTE]
> A amostragem bayesiana não suporta a rescisão antecipada. Quando utilizar a amostragem bayesiana, coloque em conjunto `early_termination_policy = None` .

Especificar os seguintes parâmetros de configuração:

* `slack_factor` ou `slack_amount` : a folga permitida no que diz respeito à melhor formação de desempenho. `slack_factor` especifica a folga admissível como uma relação. `slack_amount` especifica a folga admissível como uma quantidade absoluta, em vez de uma relação.

    Por exemplo, considere uma política de bandido aplicada ao intervalo 10. Assuma que a melhor execução ao intervalo 10 reportou uma métrica primária é 0.8 com o objetivo de maximizar a métrica primária. Se a apólice especificar um `slack_factor` de 0.2, qualquer formação corre cuja melhor métrica ao intervalo 10 seja inferior a 0,66 (0,8/(1+ `slack_factor` )) será encerrada.
* `evaluation_interval`: (opcional) a frequência de aplicação da política
* `delay_evaluation`: (opcional) atrasa a primeira avaliação política para um número especificado de intervalos


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Neste exemplo, a política de rescisão antecipada é aplicada em todos os intervalos quando as métricas são reportadas, começando no intervalo de avaliação 5. Qualquer corrida cuja melhor métrica seja inferior (1/(1+0.1) ou 91% do melhor desempenho será encerrada.

### <a name="median-stopping-policy"></a>Política de paragem mediana

[A paragem mediana](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?preserve-view=true&view=azure-ml-py) é uma política de rescisão antecipada baseada nas médias de execução das métricas primárias reportadas pelas corridas. Esta política calcula as médias de execução em todas as pistas de treino e termina com valores métricos primários piores do que a mediana das médias.

Esta política requer os seguintes parâmetros de configuração:
* `evaluation_interval`: a frequência de aplicação da política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação política para um número especificado de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Neste exemplo, a política de rescisão antecipada é aplicada em todos os intervalos a partir do intervalo de avaliação 5. Uma corrida é terminada ao intervalo 5 se a sua melhor métrica primária for pior do que a mediana das médias de corrida em intervalos 1:5 em todas as pistas de treino.

### <a name="truncation-selection-policy"></a>Política de seleção de truncação

[A seleção de trunca](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?preserve-view=true&view=azure-ml-py) cancela uma percentagem das execuções mais baixas em cada intervalo de avaliação. As execuções são comparadas usando a métrica primária. 

Esta política requer os seguintes parâmetros de configuração:

* `truncation_percentage`: a percentagem de desempenho mais baixo passa a terminar em cada intervalo de avaliação. Um valor inteiro entre 1 e 99.
* `evaluation_interval`: (opcional) a frequência de aplicação da política
* `delay_evaluation`: (opcional) atrasa a primeira avaliação política para um número especificado de intervalos


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Neste exemplo, a política de rescisão antecipada é aplicada em todos os intervalos a partir do intervalo de avaliação 5. Uma corrida termina ao intervalo 5 se o seu desempenho ao intervalo 5 estiver nos 20% mais baixos de desempenho de todas as corridas ao intervalo 5.

### <a name="no-termination-policy-default"></a>Sem política de rescisão (padrão)

Se não for especificada nenhuma política, o serviço de afinação do hiperparímetro permitirá executar todas as execuções de treino até ao final.

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>Escolher uma política de rescisão antecipada

* Para uma política conservadora que proporcione poupanças sem acabar com empregos promissores, considere uma Política de Paragem Mediana com `evaluation_interval` 1 e `delay_evaluation` 5. Estas são configurações conservadoras, que podem fornecer cerca de 25%-35% de poupança sem perda na métrica primária (com base nos nossos dados de avaliação).
* Para uma poupança mais agressiva, utilize a Política de Bandit com uma menor folga admissível ou política de seleção de truncação com uma maior percentagem de truncação.

## <a name="allocate-resources"></a>Alocar recursos

Controle o seu orçamento de recursos especificando o número máximo de treinos.

* `max_total_runs`: Número máximo de treinos. Deve ser um inteiro entre 1 e 1000.
* `max_duration_minutes`: (opcional) Duração máxima, em minutos, da experiência de afinação do hiperparímetro. As corridas após esta duração são canceladas.

>[!NOTE] 
>Se ambos `max_total_runs` e `max_duration_minutes` forem especificados, a experiência de afinação do hiperparímetro termina quando o primeiro destes dois limiares é atingido.

Além disso, especifique o número máximo de treinos a ser executado simultaneamente durante a sua pesquisa de sintonização hiperparítnica.

* `max_concurrent_runs`: (opcional) Número máximo de corridas que podem ser executadas simultaneamente. Se não for especificado, todas as execuções são lançadas em paralelo. Se especificado, deve ser um número inteiro entre 1 e 100.

>[!NOTE] 
>O número de execuções simultâneas está fechado nos recursos disponíveis no objetivo de computação especificado. Certifique-se de que o alvo do cálculo dispõe dos recursos disponíveis para a concordância desejada.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Este código configura a experiência de sintonização do hiperparímetro para utilizar um máximo de 20 corridas totais, executando quatro configurações de cada vez.

## <a name="configure-hyperparameter-tuning-experiment"></a>Configure experiência de afinação de hiperparímetro

Para configurar a sua experiência [de afinação de hiperparímetros,](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?preserve-view=true&view=azure-ml-py) forneça o seguinte:
* O espaço de pesquisa de hiperparímetro definido
* A sua política de rescisão antecipada
* A métrica primária
* Definições de atribuição de recursos
* ScriptRunConfig `src`

O ScriptRunConfig é o script de treino que será executado com os hiperparímetros amostrados. Define os recursos por trabalho (single ou multi-nó), e o objetivo de computação a utilizar.

> [!NOTE]
>O alvo do cálculo especificado `src` deve ter recursos suficientes para satisfazer o seu nível de concordância. Para obter mais informações sobre scriptRunConfig, consulte [as corridas de treino](how-to-set-up-training-targets.md)de Configuração .

Configure a sua experiência de afinação de hiperparímetros:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="submit-hyperparameter-tuning-experiment"></a>Submeter experiência de afinação de hiperparímetros

Depois de definir a configuração de sintonização do hiperparímetro, [submeta a experiência:](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-)

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-hyperparameter-tuning-optional"></a>Sintonização hiperparítnica de início quente (opcional)

Encontrar os melhores valores de hiperparímetro para o seu modelo pode ser um processo iterativo. Pode reutilizar o conhecimento das cinco corridas anteriores para acelerar a afinação do hiperparímetro.


O arranque quente é manuseado de forma diferente, dependendo do método de amostragem:
- **Amostragem bayesiana**: Os ensaios da corrida anterior são usados como conhecimento prévio para recolher novas amostras, e para melhorar a métrica primária.
- **Amostragem aleatória** ou **amostragem de grelha:** A terminação precoce utiliza conhecimentos de execuções anteriores para determinar as execuções mal executados. 

Especifique a lista de corridas dos pais a partir da qual pretende aquecer o início.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Se uma experiência de afinação de hiperparímetro for cancelada, pode retomar as funções a partir do último ponto de verificação. No entanto, o seu roteiro de treino tem de lidar com a lógica do checkpoint.

O treino deve utilizar a mesma configuração do hiperparímetro e montar as pastas de saída. O script de formação deve aceitar o `resume-from` argumento, que contém o ponto de verificação ou os ficheiros-modelo a partir dos quais retomar a formação. Pode retomar os treinos individuais utilizando o seguinte corte:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Pode configurar a sua experiência de afinação de hiperparímetro para aquecer a partir de uma experiência anterior ou retomar as corridas individuais de treino utilizando os parâmetros opcionais `resume_from` e `resume_child_runs` no config:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-hyperparameter-tuning-runs"></a>Visualize corridas de afinação de hiperparímetros

Utilize o [widget Notebook](/python/api/azureml-widgets/azureml.widgets.rundetails?preserve-view=true&view=azure-ml-py) para visualizar o progresso das suas corridas de treino. O seguinte corte visualiza toda a sua afinação hiperparítnica corre em um lugar em um lugar em um caderno Jupyter:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Este código apresenta uma tabela com detalhes sobre as corridas de treino para cada uma das configurações do hiperparímetro.

![mesa de afinação hiperparímetro](./media/how-to-tune-hyperparameters/hyperparameter-tuning-table.png)

Também pode visualizar o desempenho de cada uma das corridas à medida que o treino progride. 

![enredo de afinação hiperparaípeiro](./media/how-to-tune-hyperparameters/hyperparameter-tuning-plot.png)

Pode identificar visualmente a correlação entre desempenho e valores de hiperparímetros individuais utilizando um Plano de Coordenadas Paralelas. 

[![hiperparatómetro afinar coordenadas paralelas](./media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Também pode visualizar todas as suas correções de afinação de hiperparímetros no portal web Azure. Para obter mais informações sobre como ver uma experiência no portal, veja [como rastrear experiências.](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal)

## <a name="find-the-best-model"></a>Encontre o melhor modelo

Uma vez concluídas todas as correções de afinação do hiperparímetro, identifique a melhor configuração de desempenho e os valores do hiperparímetro:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Caderno de amostras

Consulte os cadernos de comboio-hiperparameter-* nesta pasta:
* [como usar-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes
* [Acompanhe uma experiência](how-to-track-experiments.md)
* [Implementar um modelo treinado](how-to-deploy-and-where.md)