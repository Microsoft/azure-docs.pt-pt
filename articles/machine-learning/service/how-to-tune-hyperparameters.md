---
title: Ajustar hiperparâmetros para seu modelo
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
ms.openlocfilehash: a7b0276ca41e1b9342b3602a67dea0517c60f66a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489353"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Ajustar hiperparâmetros para seu modelo com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ajuste com eficiência hiperparâmetros para seu modelo usando Azure Machine Learning.  O ajuste de hiperparâmetro inclui as seguintes etapas:

* Definir o espaço de pesquisa do parâmetro
* Especificar uma métrica primária para otimizar  
* Especificar critérios de encerramento antecipado para execuções com mau desempenho
* Alocar recursos para ajuste de hiperparâmetro
* Iniciar um experimento com a configuração acima
* Visualizar as execuções de treinamento
* Selecione a configuração de melhor desempenho para seu modelo

## <a name="what-are-hyperparameters"></a>O que são hiperparâmetros?

Os hiperparâmetros são parâmetros ajustáveis que você escolhe para treinar um modelo que governa o processo de treinamento em si. Por exemplo, para treinar uma rede neural profunda, você decide o número de camadas ocultas na rede e o número de nós em cada camada antes de treinar o modelo. Esses valores geralmente permanecem constantes durante o processo de treinamento.

Em cenários de aprendizado profundo/aprendizado de máquina, o desempenho do modelo depende muito dos valores de hiperparâmetro selecionados. O objetivo da exploração de hiperparâmetro é Pesquisar em várias configurações de hiperparâmetro para encontrar uma configuração que resulte no melhor desempenho. Normalmente, o processo de exploração de hiperparâmetro é arduamente manual, Considerando que o espaço de pesquisa é grande e a avaliação de cada configuração pode ser cara.

Azure Machine Learning permite automatizar a exploração de hiperparâmetro de maneira eficiente, economizando tempo e recursos significativos. Você especifica o intervalo de valores de hiperparâmetro e um número máximo de execuções de treinamento. Em seguida, o sistema inicia automaticamente várias execuções simultâneas com configurações de parâmetros diferentes e localiza a configuração que resulta no melhor desempenho, medida pela métrica escolhida. As execuções de treinamento com mau desempenho são automaticamente encerradas, reduzindo a desperdícios dos recursos de computação. Em vez disso, esses recursos são usados para explorar outras configurações de hiperparâmetro.


## <a name="define-search-space"></a>Definir espaço de pesquisa

Ajuste os hiperparâmetros automaticamente explorando o intervalo de valores definido para cada hiperparâmetro.

### <a name="types-of-hyperparameters"></a>Tipos de hiperparâmetros

Cada hiperparâmetro pode ser discreto ou contínuo e tem uma distribuição de valores descritos por uma [expressão de parâmetro](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py).

#### <a name="discrete-hyperparameters"></a>Hiperparâmetros discretos 

Hiperparâmetros discretos são especificados como um `choice` entre valores discretos. `choice` pode ser:

* um ou mais valores separados por vírgula
* um objeto `range`
* qualquer objeto `list` arbitrário


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

Nesse caso, `batch_size` assume um dos valores [16, 32, 64, 128] e `number_of_hidden_layers` usa um dos valores [1, 2, 3, 4].

Hiperparâmetros discretos avançados também podem ser especificados usando uma distribuição. Há suporte para as seguintes distribuições:

* `quniform(low, high, q)`-retorna um valor como Round (uniforme (baixo, alto)/q) * q
* `qloguniform(low, high, q)`-retorna um valor como Round (exp (uniforme (baixo, alto))/q) * q
* `qnormal(mu, sigma, q)`-retorna um valor como Round (normal (MU, Sigma)/q) * q
* `qlognormal(mu, sigma, q)`-retorna um valor como Round (exp (normal (MU, Sigma))/q) * q

#### <a name="continuous-hyperparameters"></a>Hiperparâmetros contínuos 

Hiperparâmetros contínuos são especificados como uma distribuição em um intervalo contínuo de valores. As distribuições com suporte incluem:

* `uniform(low, high)`-retorna um valor distribuído uniformemente entre baixo e alto
* `loguniform(low, high)`-retorna um valor desenhado de acordo com exp (uniforme (baixo, alto)) para que o logaritmo do valor de retorno seja distribuído uniformemente
* `normal(mu, sigma)`-retorna um valor real que normalmente é distribuído com MU Mean e o desvio Standard Sigma
* `lognormal(mu, sigma)`-retorna um valor desenhado de acordo com exp (normal (MU, Sigma)) para que o logaritmo do valor de retorno seja normalmente distribuído

Um exemplo de uma definição de espaço de parâmetro:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Esse código define um espaço de pesquisa com dois parâmetros-`learning_rate` e `keep_probability`. `learning_rate` tem uma distribuição normal com valor médio de 10 e um desvio padrão de 3. `keep_probability` tem uma distribuição uniforme com um valor mínimo de 0, 5 e um valor máximo de 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Amostragem do espaço de hiperparâmetro

Você também pode especificar o método de amostragem de parâmetro para usar sobre a definição de espaço de hiperparâmetro. O Azure Machine Learning dá suporte à amostragem aleatória, amostragem de grade e amostragem de bayesiana.

#### <a name="picking-a-sampling-method"></a>Escolhendo um método de amostragem

* A amostragem de grade pode ser usada se o espaço de hiperparâmetro puder ser definido como uma opção entre valores discretos e se você tiver orçamento suficiente para pesquisar exaustivamente todos os valores no espaço de pesquisa definido. Além disso, é possível usar o encerramento automático automatizado de execuções com mau desempenho, o que reduz a desperdícios de recursos.
* A amostragem aleatória permite que o espaço de hiperparâmetro inclua hiperparâmetros discretos e contínuos. Na prática, ele produz bons resultados na maioria das vezes e também permite o uso do encerramento antecipado de execuções de mau desempenho. Alguns usuários executam uma pesquisa inicial usando amostragem aleatória e refinam iterativamente o espaço de pesquisa para melhorar os resultados.
* A amostragem de Bayesiana aproveita o conhecimento de exemplos anteriores ao escolher valores de hiperparâmetro, tentando melhorar efetivamente a métrica primária relatada. A amostragem de bayesiana é recomendada quando você tem orçamento suficiente para explorar o espaço de hiperparâmetro-para obter melhores resultados com a amostragem de Bayesiana, é recomendável usar um número máximo de execuções maiores ou iguais a 20 vezes o número de hiperparâmetros que estão sendo ajustados. Observe que a amostragem de Bayesiana atualmente não dá suporte a nenhuma política de finalização antecipada.

#### <a name="random-sampling"></a>Amostragem aleatória

Em amostragem aleatória, os valores de hiperparâmetro são selecionados aleatoriamente do espaço de pesquisa definido. A [amostragem aleatória](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) permite que o espaço de pesquisa inclua hiperparâmetros discretos e contínuos.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Amostragem de grade

A [amostragem de grade](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) executa uma pesquisa de grade simples em todos os valores viáveis no espaço de pesquisa definido. Ele só pode ser usado com hiperparâmetros especificados usando `choice`. Por exemplo, o seguinte espaço tem um total de seis amostras:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Amostragem de Bayesiana

A [amostragem de Bayesiana](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) baseia-se no algoritmo de otimização Bayesiana e faz escolhas inteligentes nos valores de hiperparâmetro para a amostra seguinte. Ele escolhe o exemplo com base em como os exemplos anteriores foram executados, de modo que o novo exemplo melhora a métrica primária relatada.

Quando você usa a amostragem de Bayesiana, o número de execuções simultâneas tem um impacto na eficácia do processo de ajuste. Normalmente, um número menor de execuções simultâneas pode levar a uma maior convergência de amostragem, uma vez que o menor grau de paralelismo aumenta o número de execuções que se beneficiam de execuções previamente concluídas.

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
> A amostragem de Bayesiana não dá suporte a nenhuma política de término antecipado (consulte [especificar uma política de encerramento antecipado](#specify-early-termination-policy)). Ao usar a amostragem de parâmetro Bayesiana, defina `early_termination_policy = None`ou deixe o parâmetro `early_termination_policy`.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Especificar métrica primária

Especifique a [métrica primária](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) que você deseja que o teste de ajuste de hiperparâmetro Otimize. Cada execução de treinamento é avaliada para a métrica primária. Execuções com mau desempenho (em que a métrica primária não atende aos critérios definidos pela política de finalização antecipada) serão encerradas. Além do nome da métrica primária, você também especifica o objetivo da otimização – se deseja maximizar ou minimizar a métrica primária.

* `primary_metric_name`: o nome da métrica primária a ser otimizada. O nome da métrica primária precisa corresponder exatamente ao nome da métrica registrada pelo script de treinamento. Consulte [métricas de log para ajuste de hiperparâmetro](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: pode ser `PrimaryMetricGoal.MAXIMIZE` ou `PrimaryMetricGoal.MINIMIZE` e determina se a métrica primária será maximizada ou minimizada ao avaliar as execuções. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Otimize as execuções para maximizar a "precisão".  Certifique-se de registrar esse valor em seu script de treinamento.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Métricas de log para ajuste de hiperparâmetro

O script de treinamento para seu modelo deve registrar as métricas relevantes durante o treinamento do modelo. Ao configurar o ajuste de hiperparâmetro, você especifica a métrica primária a ser usada para avaliar o desempenho de execução. (Consulte [especificar uma métrica primária para otimizar](#specify-primary-metric-to-optimize).)  No script de treinamento, você deve registrar essa métrica para que ela fique disponível para o processo de ajuste de hiperparâmetro.

Registre essa métrica em seu script de treinamento com o seguinte trecho de código de exemplo:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

O script de treinamento calcula o `val_accuracy` e o registra como "exatidão", que é usado como a métrica primária. Cada vez que a métrica é registrada, ela é recebida pelo serviço de ajuste de hiperparâmetro. Cabe ao desenvolvedor do modelo determinar com que frequência relatar essa métrica.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Especificar política de término antecipado

Terminar de funcionar de maneira inadequada com uma política de encerramento antecipado. O encerramento reduz o desperdícios de recursos e, em vez disso, usa esses recursos para explorar outras configurações de parâmetro.

Ao usar uma política de encerramento antecipado, você pode configurar os seguintes parâmetros que controlam quando uma política é aplicada:

* `evaluation_interval`: a frequência para aplicar a política. Cada vez que o script de treinamento registra a contagem de métricas primárias como um intervalo. Assim, um `evaluation_interval` de 1 aplicará a política sempre que o script de treinamento relatar a métrica primária. Um `evaluation_interval` de 2 aplicará a política a cada vez que o script de treinamento relatar a métrica primária. Se não for especificado, `evaluation_interval` será definido como 1 por padrão.
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos. É um parâmetro opcional que permite que todas as configurações sejam executadas para um número mínimo inicial de intervalos, evitando o encerramento prematuro de execuções de treinamento. Se especificado, a política aplicará cada múltiplo de evaluation_interval que for maior ou igual a delay_evaluation.

O Azure Machine Learning dá suporte às seguintes políticas de finalização antecipada.

### <a name="bandit-policy"></a>Política de Bandit

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) é uma política de encerramento com base no fator de margem de atraso/valor da margem de atraso e no intervalo de avaliação. A política encerra primeiro as execuções em que a métrica primária não está dentro do fator de margem de atraso especificado/valor da margem de atraso em relação à execução de treinamento de melhor desempenho. Ele usa os seguintes parâmetros de configuração:

* `slack_factor` ou `slack_amount`: a margem de atraso permitida em relação à execução de treinamento de melhor desempenho. `slack_factor` especifica a margem de atraso permitida como uma taxa. `slack_amount` especifica a margem de atraso permitida como um valor absoluto, em vez de uma taxa.

    Por exemplo, considere uma política Bandit sendo aplicada no intervalo de 10. Suponha que a execução de melhor desempenho no intervalo 10 reportou uma métrica primária 0,8 com uma meta para maximizar a métrica primária. Se a política tiver sido especificada com um `slack_factor` de 0,2, qualquer execução de treinamento, cuja melhor métrica no intervalo 10 é menor que 0,66 (0,8/(1 +`slack_factor`)) será encerrada. Em vez disso, a política foi especificada com um `slack_amount` de 0,2, qualquer execução de treinamento, cuja melhor métrica no intervalo 10 é menor que 0,6 (0,8-`slack_amount`) será encerrada.
* `evaluation_interval`: a frequência para aplicar a política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Neste exemplo, a política de término antecipado é aplicada em cada intervalo quando as métricas são relatadas, começando no intervalo de avaliação 5. Qualquer execução cuja melhor métrica é menor que (1/0,1) ou 91% da execução de melhor desempenho será encerrada.

### <a name="median-stopping-policy"></a>Política de parada mediana

A [parada mediana](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) é uma política de término inicial com base nas médias em execução das métricas primárias relatadas pelas execuções. Essa política computa as médias em execução em todas as execuções de treinamento e as execuções de terminações cujo desempenho é pior do que a mediana das médias em execução. Essa política usa os seguintes parâmetros de configuração:
* `evaluation_interval`: a frequência para aplicar a política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Neste exemplo, a política de término antecipado é aplicada a cada intervalo a partir do intervalo de avaliação 5. Uma execução será encerrada no intervalo 5 se sua melhor métrica primária for pior do que a mediana das médias em execução nos intervalos de 1:5 em todas as execuções de treinamento.

### <a name="truncation-selection-policy"></a>Política de seleção de truncamento

[Seleção de truncamento](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) cancela um determinado percentual de execuções de execução mais baixa em cada intervalo de avaliação. As execuções são comparadas com base em seu desempenho na métrica primária e os X% mais baixos são encerrados. Ele usa os seguintes parâmetros de configuração:

* `truncation_percentage`: a porcentagem de execuções de execução mais baixa para terminar em cada intervalo de avaliação. Especifique um valor inteiro entre 1 e 99.
* `evaluation_interval`: a frequência para aplicar a política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Neste exemplo, a política de término antecipado é aplicada a cada intervalo a partir do intervalo de avaliação 5. Uma execução será encerrada no intervalo 5 se seu desempenho no intervalo 5 estiver nos mais baixos 20% do desempenho de todas as execuções no intervalo de 5.

### <a name="no-termination-policy"></a>Nenhuma política de encerramento

Se você quiser que todas as execuções de treinamento sejam executadas até a conclusão, defina a política como nenhuma. Isso terá o efeito de não aplicar nenhuma política de término antecipado.

```Python
policy=None
```

### <a name="default-policy"></a>Política padrão

Se nenhuma política for especificada, o serviço de ajuste de hiperparâmetro permitirá que todas as execuções de treinamento sejam executadas até a conclusão.

### <a name="picking-an-early-termination-policy"></a>Selecionando uma política de encerramento antecipado

* Se você estiver procurando uma política conservadora que forneça economia sem encerrar trabalhos promissores, você poderá usar uma política de parada mediana com `evaluation_interval` 1 e `delay_evaluation` 5. Essas são configurações conservadoras, que podem fornecer economia de aproximadamente 25% a 35% sem perda na métrica primária (com base em nossos dados de avaliação).
* Se você estiver procurando uma economia mais agressiva do encerramento antecipado, poderá usar a política Bandit com uma pequena margem de atraso permitida ou uma política de seleção de truncamento (menor), com uma porcentagem maior de truncamento.

## <a name="allocate-resources"></a>Alocar recursos

Controle seu orçamento de recursos para seu experimento de ajuste de hiperparâmetro especificando o número total máximo de execuções de treinamento.  Opcionalmente, especifique a duração máxima para o experimento de ajuste de hiperparâmetro.

* `max_total_runs`: número total máximo de execuções de treinamento que serão criadas. Limite superior – pode haver menos execuções, por exemplo, se o espaço de hiperparâmetro for finito e tiver menos amostras. Deve ser um número entre 1 e 1000.
* `max_duration_minutes`: duração máxima em minutos do experimento de ajuste do hiperparâmetro. O parâmetro é opcional e, se presente, todas as execuções que seriam executadas após essa duração serão canceladas automaticamente.

>[!NOTE] 
>Se `max_total_runs` e `max_duration_minutes` forem especificados, o experimento de ajuste de hiperparâmetro será encerrado quando o primeiro desses dois limites for atingido.

Além disso, especifique o número máximo de execuções de treinamento a serem executadas simultaneamente durante a pesquisa de ajuste de hiperparâmetro.

* `max_concurrent_runs`: número máximo de execuções a serem executadas simultaneamente em um determinado momento. Se nenhum for especificado, todos os `max_total_runs` serão iniciados em paralelo. Se especificado, deve ser um número entre 1 e 100.

>[!NOTE] 
>O número de execuções simultâneas é restringido nos recursos disponíveis no destino de computação especificado. Portanto, você precisa garantir que o destino de computação tenha os recursos disponíveis para a simultaneidade desejada.

Alocar recursos para ajuste de hiperparâmetro:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Esse código configura o experimento de ajuste de hiperparâmetro para usar um máximo de 20 execuções de total, executando quatro configurações por vez.

## <a name="configure-experiment"></a>Configurar experimento

[Configure seu experimento de ajuste de hiperparâmetro](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) usando o espaço de pesquisa de hiperparâmetro definido, a política de término antecipado, a métrica primária e a alocação de recursos das seções acima. Além disso, forneça um `estimator` que será chamado com os hiperparâmetros amostrados. O `estimator` descreve o script de treinamento que você executa, os recursos por trabalho (única ou várias GPU) e o destino de computação a ser usado. Como a simultaneidade para seu experimento de ajuste de hiperparâmetro é restringida nos recursos disponíveis, verifique se o destino de computação especificado no `estimator` tem recursos suficientes para a simultaneidade desejada. (Para obter mais informações sobre estimadores, consulte [How to Train Models](how-to-train-ml-models.md).)

Configure seu experimento de ajuste de hiperparâmetro:

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

## <a name="submit-experiment"></a>Enviar experimento

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

## <a name="visualize-experiment"></a>Visualizar experimento

O SDK do Azure Machine Learning fornece um [Widget de bloco de anotações](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) que visualiza o progresso de suas execuções de treinamento. O trecho a seguir visualiza todas as execuções de ajuste de hiperparâmetro em um único lugar em um notebook Jupyter:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Esse código exibe uma tabela com detalhes sobre as execuções de treinamento para cada uma das configurações de hiperparâmetro.

![tabela de ajuste de hiperparâmetro](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Você também pode visualizar o desempenho de cada uma das execuções conforme o treinamento progride. 

![gráfico de ajuste de hiperparâmetro](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Além disso, você pode identificar visualmente a correlação entre o desempenho e os valores de hiperparâmetros individuais usando um gráfico de coordenadas paralelas. 

[![coordenadas paralelas de ajuste de hiperparâmetro](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Você também pode visualizar todas as suas execuções de ajuste de hiperparâmetro no portal da Web do Azure. Para obter mais informações sobre como exibir um experimento no portal da Web, consulte [como controlar experimentos](how-to-track-experiments.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>Encontre o melhor modelo

Depois que todas as execuções de ajuste de hiperparâmetro forem concluídas, [identifique a configuração de melhor desempenho](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true-) e os valores de hiperparâmetro correspondentes:

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

## <a name="sample-notebook"></a>Bloco de anotações de exemplo
Consulte os blocos de anotações Train-hiperparameter-* nesta pasta:
* [como usar – azureml/treinamento-com o aprendizado profundo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes
* [Acompanhar um experimento](how-to-track-experiments.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
