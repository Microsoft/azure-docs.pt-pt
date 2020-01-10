---
title: Otimizar hiperparâmetros para o modelo
titleSuffix: Azure Machine Learning
description: Ajuste com eficiência hiperparâmetros para o seu modelo de aprendizado profundo/Machine Learning usando Azure Machine Learning. Você aprenderá como definir o espaço de pesquisa de parâmetro, especificar uma métrica primária para otimizar e o término de mau desempenho de execuções.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 4ca9cac00b9d00dcdbbd27f2fe769de2983c13ae
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75536645"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Ajustar hiperparâmetros para seu modelo com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ajuste com eficiência hiperparâmetros para seu modelo usando Azure Machine Learning.  A otimização de hiper-parâmetros inclui os seguintes passos:

* Definir o espaço de pesquisa de parâmetro
* Especifique uma métrica primária para otimizar  
* Especificar critérios de cessação antecipada de mau desempenho execuções
* Alocar recursos para a otimização de hiper-parâmetros
* Inicie uma experiência com a configuração acima
* Visualize as execuções de preparação
* Selecionar o melhor desempenho de configuração para o modelo

## <a name="what-are-hyperparameters"></a>Quais são hiperparâmetros?

Hiperparâmetros são parâmetros ajustável que escolher para preparar um modelo que regem o processo de treinamento em si. Por exemplo, para preparar uma rede neural profunda, decidir o número de camadas ocultas na rede e o número de nós em cada camada antes do modelo de formação. Estes valores, normalmente, mantenha-se constante durante o processo de treinamento.

Em cenários de aprendizagem profunda de aprendizagem automática / máquinas, desempenho de modelos depende muito os valores de hiper-parâmetros selecionados. O objetivo de exploração de hiper-parâmetros é pesquisar em várias configurações de hiper-parâmetros para encontrar uma configuração que resulta em melhor desempenho. Normalmente, o processo de exploração de hiper-parâmetros é extremamente manual, uma vez que o espaço de pesquisa é grande e avaliação de cada configuração pode ser cara.

O Azure Machine Learning permite-lhe automatizar hiper-parâmetros exploração de uma forma eficiente, economizando tempo significativo e recursos. Especifique o intervalo de valores de hiper-parâmetros e executa um número máximo de treinamento. O sistema, em seguida, inicia várias execuções simultâneas com configurações de parâmetros diferentes e automaticamente localiza a configuração que resulta em melhor desempenho, medido pela métrica que escolher. Execuções de preparação com mau desempenho são automaticamente antecipadas terminada, a redução de desperdício de recursos de computação. Estes recursos em vez disso, são utilizados para explorar outras configurações de hiper-parâmetros.


## <a name="define-search-space"></a>Definir o espaço de pesquisa

Ajuste automaticamente hiperparâmetros ao explorar o intervalo de valores definidos para cada hiper-parâmetros.

### <a name="types-of-hyperparameters"></a>Tipos de hiperparâmetros

Cada hiperparâmetro pode ser discreto ou contínuo e tem uma distribuição de valores descritos por uma [expressão de parâmetro](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py).

#### <a name="discrete-hyperparameters"></a>Discretos hiperparâmetros 

Hiperparâmetros discretos especificados como uma `choice` entre valores discretos. `choice` pode ser:

* um ou mais valores separados por vírgulas
* um `range` objeto
* qualquer arbitrário `list` objeto


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

Neste caso, `batch_size` assume a um dos valores [16, 32, 64, 128] e `number_of_hidden_layers` assume um dos valores [1, 2, 3, 4].

Hiperparâmetros discretos avançados também podem ser especificados com uma distribuição. Há suporte para as distribuições seguintes:

* `quniform(low, high, q)` -Devolve um valor como round (uniforme (inferior, superior) / p) * p
* `qloguniform(low, high, q)` -Devolve um valor como round (exp (uniforme (inferior, superior)) / p) * p
* `qnormal(mu, sigma, q)` -Devolve um valor como round (normal (mu, sigma) / p) * p
* `qlognormal(mu, sigma, q)` -Devolve um valor como round (exp (normal (mu, sigma)) / p) * p

#### <a name="continuous-hyperparameters"></a>Hiperparâmetros contínuos 

Hiperparâmetros contínuos são especificados como uma distribuição através de um intervalo contínuo de valores. Distribuições suportadas incluem:

* `uniform(low, high)` -Devolve um valor de distribuídas de maneira uniforme entre baixa e alta
* `loguniform(low, high)` -Devolve um valor desenhado, de acordo com o exp (uniforme (inferior, superior)), para que o logaritmo de valor de retorno é distribuído uniformemente
* `normal(mu, sigma)` -Devolve um valor real que normalmente é distribuído com mean sigma mu e desvio padrão
* `lognormal(mu, sigma)` -Devolve um valor desenhado, de acordo com o exp (normal (mu, sigma)), para que o logaritmo de valor de retorno é normalmente distribuído

Um exemplo de uma definição de espaço de parâmetro:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Esse código define um espaço de pesquisa com dois parâmetros - `learning_rate` e `keep_probability`. `learning_rate` tem uma distribuição normal com o valor médio 10 e um desvio-padrão de 3. `keep_probability` tem uma distribuição uniforme com um valor mínimo de 0,05 e um valor máximo de 0,1.

### <a name="sampling-the-hyperparameter-space"></a>O espaço de hiper-parâmetros de amostragem

Também pode especificar o método de amostragem de parâmetro a utilizar a definição de espaço de hiper-parâmetros. O Azure Machine Learning dá suporte à amostragem aleatória, amostragem de grade e amostragem de bayesiana.

#### <a name="picking-a-sampling-method"></a>Escolhendo um método de amostragem

* A amostragem de grade pode ser usada se o espaço de hiperparâmetro puder ser definido como uma opção entre valores discretos e se você tiver orçamento suficiente para pesquisar exaustivamente todos os valores no espaço de pesquisa definido. Além disso, é possível usar o encerramento automático automatizado de execuções com mau desempenho, o que reduz a desperdícios de recursos.
* A amostragem aleatória permite que o espaço de hiperparâmetro inclua hiperparâmetros discretos e contínuos. Na prática, ele produz bons resultados na maioria das vezes e também permite o uso do encerramento antecipado de execuções de mau desempenho. Alguns usuários executam uma pesquisa inicial usando amostragem aleatória e refinam iterativamente o espaço de pesquisa para melhorar os resultados.
* A amostragem de Bayesiana aproveita o conhecimento de exemplos anteriores ao escolher valores de hiperparâmetro, tentando melhorar efetivamente a métrica primária relatada. A amostragem de bayesiana é recomendada quando você tem orçamento suficiente para explorar o espaço de hiperparâmetro-para obter melhores resultados com a amostragem de Bayesiana, é recomendável usar um número máximo de execuções maiores ou iguais a 20 vezes o número de hiperparâmetros que estão sendo ajustados. Observe que a amostragem de Bayesiana atualmente não dá suporte a nenhuma política de finalização antecipada.

#### <a name="random-sampling"></a>Amostragem aleatória

Numa amostragem aleatória, os valores de hiper-parâmetros são selecionadas aleatoriamente entre o espaço de pesquisa definido. A [amostragem aleatória](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) permite que o espaço de pesquisa inclua hiperparâmetros discretos e contínuos.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Amostragem de grelha

A [amostragem de grade](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) executa uma pesquisa de grade simples em todos os valores viáveis no espaço de pesquisa definido. Só pode ser utilizado com especificados por meio de hiperparâmetros `choice`. Por exemplo, o seguinte espaço tem um total de seis amostras:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Amostragem de Bayesianos

A [amostragem de Bayesiana](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) baseia-se no algoritmo de otimização Bayesiana e faz escolhas inteligentes nos valores de hiperparâmetro para a amostra seguinte. Ele escolhe o exemplo com base em como os exemplos anteriores efetuada, como, por exemplo que o novo exemplo melhora a métrica primária comunicada.

Quando utiliza Bayesianos amostragem, o número de execuções simultâneas tem um impacto sobre a eficácia do processo de otimização. Normalmente, um número menor de execuções simultâneas pode levar a convergência de amostragem melhor, uma vez que o menor grau de paralelismo aumenta o número de execuções que tiram partido de execuções concluídas anteriormente.

A amostragem de Bayesiana dá suporte apenas a distribuições `choice`, `uniform`e `quniform` no espaço de pesquisa.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Amostragem de Bayesianos não suporta qualquer política de cessação antecipada (consulte [especificar uma política de cessação antecipada](#specify-early-termination-policy)). Ao utilizar a amostragem de parâmetro Bayesianos, defina `early_termination_policy = None`, ou deixe de fora o `early_termination_policy` parâmetro.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Especifique a métrica primária

Especifique a [métrica primária](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) que você deseja que o teste de ajuste de hiperparâmetro Otimize. Cada execução de treinamento é avaliada para a métrica primária. Mau desempenho execuções (em que a métrica primária não cumpre os critérios definidos pela política de cessação antecipada) será terminada. Além do nome de métrica principal, também especificar o objetivo da otimização - se maximizar ou minimizar a métrica primária.

* `primary_metric_name`: O nome da métrica primário para otimizar. O nome da métrica primário tem de corresponder exatamente ao nome da métrica registado pelo script de treinamento. Ver [iniciar sessão métricas para a otimização de hiper-parâmetros](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Pode ser qualquer um `PrimaryMetricGoal.MAXIMIZE` ou `PrimaryMetricGoal.MINIMIZE` e determina se a métrica primária será maximizada ou minimizada ao avaliar as execuções. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Otimize as execuções para maximizar a "precisão".  Lembre-se de que este valor de registo no seu script de treinamento.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Métricas de registo para a otimização de hiper-parâmetros

O script de treinamento para o seu modelo tem de iniciar as métricas relevantes durante a preparação de modelos. Ao configurar a otimização de hiper-parâmetros, especifique a métrica primária a utilizar para avaliar o desempenho de execução. (Consulte [especificar uma métrica primária para otimizar](#specify-primary-metric-to-optimize).)  No script de treinamento, você deve registrar essa métrica para que ela fique disponível para o processo de ajuste de hiperparâmetro.

Esta métrica de registo no seu script de treinamento com o seguinte fragmento de exemplo:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

O script de treinamento calcula o `val_accuracy` e regista-lo como "precisão", que é utilizada como a métrica primária. Sempre que a métrica é registada é recebida, o serviço de otimização de hiper-parâmetros. Cabe ao desenvolvedor de modelo para determinar a frequência de relatório esta métrica.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Especificar Política de cessação antecipada

Termine mau desempenho é executado automaticamente com uma política de cessação antecipada. Cessação reduz o desperdício de recursos e em vez disso, utiliza estes recursos para explorar outras configurações de parâmetro.

Quando utilizar uma política de cessação antecipada, pode configurar os seguintes parâmetros que controlam quando uma política é aplicada:

* `evaluation_interval`: a frequência para aplicar a política. Sempre que o script de treinamento registos a métrica primária é contabilizado como um intervalo. Portanto, um `evaluation_interval` 1 aplicará a diretiva sempre que o script de treinamento reporta a métrica primária. Um `evaluation_interval` de 2 irá aplicar a política de todas as outras vezes o script de treinamento reporta a métrica primária. Se não for especificado, `evaluation_interval` está definido como 1, por predefinição.
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos. É um parâmetro opcional que permite que todas as configurações ser executado por um número mínimo inicial de intervalos, evitando o encerramento prematuro de treinamento é executado. Se for especificado, a política aplica-se cada múltiplo de evaluation_interval é maior que ou igual a delay_evaluation.

O Azure Machine Learning dá suporte às seguintes políticas de finalização antecipada.

### <a name="bandit-policy"></a>Política de bandit

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) é uma política de encerramento com base no fator de margem de atraso/valor da margem de atraso e no intervalo de avaliação. A política desde o início termina todas as execuções em que a métrica de principal não está no fator slack especificado / run do slack quantidade em relação ao melhor desempenho de treinamento. Ele usa os seguintes parâmetros de configuração:

* `slack_factor` ou `slack_amount`: slack permitido em relação ao melhor desempenho de treinamento ser executado. `slack_factor` Especifica o slack permitido como um rácio. `slack_amount` Especifica o slack permitido como um valor absoluto, em vez de uma taxa.

    Por exemplo, considere uma política de Bandit a ser aplicada neste intervalo de 10. Partem do princípio de que a execução melhor executar no intervalo de 10 comunicado uma métrica primária 0,8 com o objetivo de maximizar a métrica primária. Se a política foi especificada com um `slack_factor` de 0,2, é executada qualquer treinamento, cuja melhor métrica no intervalo de 10 é menor que 0.66 (0,8 / (1 +`slack_factor`)) será terminada. Se em vez disso, a política foi especificada com um `slack_amount` de 0,2, é executada qualquer treinamento, cuja melhor métrica no intervalo de 10 é menor que 0.6 (0,8 - `slack_amount`) será terminada.
* `evaluation_interval`: a frequência para aplicar a política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Neste exemplo, é aplicada a política de cessação antecipada em cada intervalo quando métricas são comunicadas, começando no intervalo de avaliação de 5. Qualquer execução cuja melhor métrica é menor que (1/(1+0.1) ou 91% do melhor desempenho será execução terminada.

### <a name="median-stopping-policy"></a>Política de parar mediano

A [parada mediana](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) é uma política de término inicial com base nas médias em execução das métricas primárias relatadas pelas execuções. Esta política computa médias em execução em todas as execuções de preparação e termina execuções cujo desempenho é pior do que o valor mediano das médias em execução. Esta política tem os seguintes parâmetros de configuração:
* `evaluation_interval`: a frequência para aplicar a política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Neste exemplo, é aplicada a política de cessação antecipada em cada intervalo que começa no intervalo de avaliação de 5. Uma execução será terminada em intervalo 5 se sobre intervalos 1:5, sua melhor métrica primária é pior do que o valor mediano das médias em execução em todas as execuções de preparação.

### <a name="truncation-selection-policy"></a>Política de seleção de truncamento

[Seleção de truncamento](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) cancela um determinado percentual de execuções de execução mais baixa em cada intervalo de avaliação. Execuções são comparadas com base no respetivo desempenho na métrica primária e a mais baixa X % está terminada. Ele usa os seguintes parâmetros de configuração:

* `truncation_percentage`: a percentagem da execução mais baixa é executada para terminar a cada intervalo de avaliação. Especifique um valor inteiro entre 1 e 99.
* `evaluation_interval`: a frequência para aplicar a política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Neste exemplo, é aplicada a política de cessação antecipada em cada intervalo que começa no intervalo de avaliação de 5. Uma execução será encerrada no intervalo 5 se seu desempenho no intervalo 5 estiver nos mais baixos 20% do desempenho de todas as execuções no intervalo de 5.

### <a name="no-termination-policy"></a>Nenhuma política de terminação

Se quiser que todas as execuções de preparação para conclusão, defina a política como None. Isso terá o efeito de não aplicar qualquer política de cessação antecipada.

```Python
policy=None
```

### <a name="default-policy"></a>Política predefinida

Se nenhuma política for especificada, o serviço de ajuste de hiperparâmetro permitirá que todas as execuções de treinamento sejam executadas até a conclusão.

### <a name="picking-an-early-termination-policy"></a>Selecionando uma política de encerramento antecipado

* Se estiver procurando por uma política conservador que proporciona poupanças sem terminar tarefas promissoras, pode utilizar uma política de parar de mediana com `evaluation_interval` 1 e `delay_evaluation` 5. Estas são definições conservadoras, que podem fornecer aproximadamente 25% - 35% de poupanças sem perda em métrica primária (com base nos nossos dados de avaliação).
* Se você estiver procurando uma economia mais agressiva do encerramento antecipado, poderá usar a política Bandit com uma pequena margem de atraso permitida ou uma política de seleção de truncamento (menor), com uma porcentagem maior de truncamento.

## <a name="allocate-resources"></a>Alocar recursos

Controle o orçamento de recursos para a sua experimentação, especificando o número máximo de total de execuções de preparação de otimização de hiper-parâmetros.  Opcionalmente, especifica a duração máxima para a sua experimentação de otimização de hiper-parâmetros.

* `max_total_runs`: Número total máximo de execuções de preparação que será criada. Limite superior - pode haver menos execuções, por exemplo, se o espaço de hiper-parâmetros é finito e tem menos de exemplos. Tem de ser um número entre 1 e 1000.
* `max_duration_minutes`: Duração máxima em minutos o experimentação de otimização de hiper-parâmetros. Parâmetro é opcional e, se estiver presente, todas as execuções que estariam sendo executados depois desta duração automaticamente foram canceladas.

>[!NOTE] 
>Se os dois `max_total_runs` e `max_duration_minutes` forem especificados, o experimentação de otimização de hiper-parâmetros termina quando o primeiro desses dois limites for atingido.

Além disso, especifique o número máximo de treinamento é executado para serem executadas em simultâneo durante sua pesquisa de otimização de hiper-parâmetros.

* `max_concurrent_runs`: Número máximo de execuções para executar simultaneamente em determinado momento. Se especificado nenhum, todos `max_total_runs` será iniciado em paralelo. Se for especificado, tem de ser um número entre 1 e 100.

>[!NOTE] 
>O número de execuções simultâneas é Check controlado nos recursos disponíveis no destino de computação especificado. Por este motivo, terá de garantir que o destino de computação tem os recursos disponíveis para a simultaneidade pretendido.

Alocar recursos para a otimização de hiper-parâmetros:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Esse código configura o experimento de ajuste de hiperparâmetro para usar um máximo de 20 execuções de total, executando quatro configurações por vez.

## <a name="configure-experiment"></a>Configurar experimentação

[Configure seu experimento de ajuste de hiperparâmetro](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) usando o espaço de pesquisa de hiperparâmetro definido, a política de término antecipado, a métrica primária e a alocação de recursos das seções acima. Além disso, fornecer um `estimator` que será chamado com os amostras hiperparâmetros. O `estimator` descreve o script de treinamento, executar, os recursos por tarefa (única ou múltipla gpu) e o destino de computação para utilizar. Uma vez que a simultaneidade para a sua experimentação de otimização de hiper-parâmetros é Check controlado nos recursos disponíveis, certifique-se de que o destino de computação especificado no `estimator` tem recursos suficientes para a simultaneidade pretendido. (Para obter mais informações sobre os avaliadores, consulte [como a criar modelos](how-to-train-ml-models.md).)

Configure a sua experimentação de otimização de hiper-parâmetros:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>Submeter a experimentação

Depois de definir a configuração de ajuste de hiperparâmetro, [envie um experimento](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name` é o nome que você atribui ao seu experimento de ajuste de hiperparâmetro e `workspace` é o espaço de trabalho no qual você deseja criar o experimento (para obter mais informações sobre experimentos, consulte [como funciona o Azure Machine Learning?](concept-azure-machine-learning-architecture.md))

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Inicie o teste de ajuste do hiperparâmetro de aquecimento (opcional)

Muitas vezes, encontrar os melhores valores de hiperparâmetro para seu modelo pode ser um processo iterativo, precisando de várias execuções de ajuste que aprendam com as execuções de ajuste de hiperparâmetro anteriores. Reutilizar o conhecimento dessas execuções anteriores acelerará o processo de ajuste de hiperparâmetro, reduzindo, assim, o custo de ajuste do modelo e, potencialmente, melhorará a métrica primária do modelo resultante. Quando estiver quente iniciando um experimento de ajuste de hiperparâmetro com a amostragem de Bayesiana, as avaliações da execução anterior serão usadas como conhecimento prévio para escolher com inteligência novos exemplos, para melhorar a métrica primária. Além disso, ao usar a amostragem aleatória ou de grade, qualquer decisão de encerramento antecipado aproveitará as métricas das execuções anteriores para determinar as execuções de treinamento com mau desempenho. 

Azure Machine Learning permite que você inicie a execução de ajuste de hiperparâmetro, aproveitando o conhecimento de até 5 execuções pai concluídas/canceladas no ajuste de hiperparâmetro. Você pode especificar a lista de execuções pai que deseja que comecem a usar este trecho de código:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Além disso, pode haver ocasiões em que as execuções de treinamento individual de um experimento de ajuste de hiperparâmetro são canceladas devido a restrições de orçamento ou falham devido a outros motivos. Agora é possível retomar essas execuções de treinamento individuais do último ponto de verificação (supondo que o script de treinamento manipule pontos de verificação). Retomar uma execução de treinamento individual usará a mesma configuração de hiperparâmetro e montará a pasta de saídas usada para essa execução. O script de treinamento deve aceitar o argumento `resume-from`, que contém os arquivos de ponto de verificação ou modelo dos quais retomar a execução de treinamento. Você pode retomar as execuções de treinamento individuais usando o seguinte trecho:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Você pode configurar o teste de ajuste de hiperparâmetro para iniciar a quente de um experimento anterior ou retomar execuções de treinamento individuais usando os parâmetros opcionais `resume_from` e `resume_child_runs` na configuração:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          resume_from=warmstart_parents_to_resume_from, 
                          resume_child_runs=child_runs_to_resume,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="visualize-experiment"></a>Visualizar a experimentação

O SDK do Azure Machine Learning fornece um [Widget de bloco de anotações](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) que visualiza o progresso de suas execuções de treinamento. O fragmento seguinte visualiza a todos os seus hiper-parâmetros otimização é executada num único local num bloco de notas do Jupyter:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Esse código exibe uma tabela com detalhes sobre as execuções de preparação para cada uma das configurações de hiper-parâmetros.

![tabela de otimização de hiper-parâmetros](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Também é possível visualizar o desempenho de cada uma das execuções à medida que progride de treinamento. 

![desenho de otimização de hiper-parâmetros](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Além disso, pode identificar visualmente a correlação entre desempenho e os valores de hiperparâmetros individuais com um paralelo as coordenadas de desenho. 

[![coordenadas paralelas de ajuste de hiperparâmetro](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

É possível visualizar todos os seus hiper-parâmetros otimização é executada no portal web do Azure. Para obter mais informações sobre como visualizar uma experimentação no web portal, consulte [como controlar experimentações](how-to-track-experiments.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>Encontrar o melhor modelo

Depois que todas as execuções de ajuste de hiperparâmetro forem concluídas, [identifique a configuração de melhor desempenho](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true--include-resume-from-runs-true-----typing-union-azureml-core-run-run--nonetype-) e os valores de hiperparâmetro correspondentes:

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

## <a name="sample-notebook"></a>Bloco de notas de exemplo
Consulte os blocos de anotações Train-hiperparameter-* nesta pasta:
* [How-to-use-azureml/Training-with-Deep-Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes
* [Controlar uma experimentação](how-to-track-experiments.md)
* [Implementar um modelo preparado](how-to-deploy-and-where.md)
