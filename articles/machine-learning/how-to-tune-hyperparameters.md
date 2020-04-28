---
title: Sintonize os hiperparâmetros para o seu modelo
titleSuffix: Azure Machine Learning
description: Sintonize eficientemente os hiperparâmetros para o seu modelo de aprendizagem profunda/aprendizagem automática utilizando o Azure Machine Learning. Você vai aprender a definir o espaço de pesquisa de parâmetros, especificar uma métrica primária para otimizar, e terminar precocemente corridas mal executados.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/30/2020
ms.custom: seodec18
ms.openlocfilehash: a58ea58ebf6fdc7d8521d204ac42fcbadeca39a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189305"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Sintonize os hiperparâmetros para o seu modelo com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ajuste eficientemente os hiperparâmetros para o seu modelo utilizando o Azure Machine Learning.  A sintonização do hiperparâmetro inclui os seguintes passos:

* Defina o espaço de pesquisa do parâmetro
* Especificar uma métrica primária para otimizar  
* Especificar critérios de rescisão antecipada para executar corridas mal executados
* Alocar recursos para afinação de hiperparâmetros
* Lance uma experiência com a configuração acima
* Visualizar os treinos
* Selecione a melhor configuração de desempenho para o seu modelo

## <a name="what-are-hyperparameters"></a>O que são hiperparâmetros?

Os hiperparâmetros são parâmetros ajustáveis que escolhe para treinar um modelo que rege o próprio processo de treino. Por exemplo, para treinar uma rede neural profunda, você decide o número de camadas escondidas na rede e o número de nós em cada camada antes de treinar o modelo. Estes valores geralmente permanecem constantes durante o processo de treino.

Em cenários de aprendizagem profunda/aprendizagem automática, o desempenho do modelo depende fortemente dos valores hiperparâmetros selecionados. O objetivo da exploração de hiperparâmetros é pesquisar em várias configurações de hiperparâmetros para encontrar uma configuração que resulte no melhor desempenho. Tipicamente, o processo de exploração do hiperparâmetro é meticulosamente manual, dado que o espaço de pesquisa é vasto e a avaliação de cada configuração pode ser cara.

O Azure Machine Learning permite-lhe automatizar a exploração de hiperparâmetros de forma eficiente, poupando-lhe tempo e recursos significativos. Especifica-se a gama de valores de hiperparâmetros e um número máximo de treinos. O sistema lança automaticamente vários percursos simultâneos com diferentes configurações de parâmetros e encontra a configuração que resulta no melhor desempenho, medido pela métrica que escolher. Os treinos mal executados são automaticamente encerrados precocemente, reduzindo o desperdício de recursos computacionais. Estes recursos são, em vez disso, utilizados para explorar outras configurações de hiperparâmetros.


## <a name="define-search-space"></a>Definir espaço de pesquisa

Sintonize automaticamente os hiperparâmetros explorando a gama de valores definidos para cada hiperparâmetro.

### <a name="types-of-hyperparameters"></a>Tipos de hiperparâmetros

Cada hiperparâmetro pode ser discreto ou contínuo e tem uma distribuição de valores descritos por uma [expressão de parâmetro](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py).

#### <a name="discrete-hyperparameters"></a>Hiperparâmetros discretos 

Os hiperparâmetros discretos são `choice` especificados como um entre valores discretos. `choice`pode ser:

* um ou mais valores separados de vírina
* um `range` objeto
* qualquer `list` objeto arbitrário


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

Neste caso, `batch_size` assume um dos valores [16, 32, 64, 128] e `number_of_hidden_layers` assume um dos valores [1, 2, 3, 4].

Hiperparâmetros avançados de discretos também podem ser especificados através de uma distribuição. As seguintes distribuições são suportadas:

* `quniform(low, high, q)`- Devolve um valor como redondo (uniforme (uniforme(baixo, alto) / q) * q
* `qloguniform(low, high, q)`- Devolve um valor como redondo (exp(uniforme(baixo, alto)) / q) * q
* `qnormal(mu, sigma, q)`- Devolve um valor como redondo (normal(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)`- Devolve um valor como redondo (exp(exp(mu, sigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>Hiperparâmetros contínuos 

Os hiperparâmetros contínuos são especificados como uma distribuição sobre uma gama contínua de valores. As distribuições apoiadas incluem:

* `uniform(low, high)`- Devolve um valor uniformemente distribuído entre baixo e alto
* `loguniform(low, high)`- Devolução de um valor extraído de acordo com o exp (uniforme(baixo, alto)) de modo a que o logaritário do valor de retorno seja distribuído uniformemente
* `normal(mu, sigma)`- Devolve um valor real que é normalmente distribuído com mu médio e desvio padrão sigma
* `lognormal(mu, sigma)`- Devolução de um valor extraído de acordo com o exp ((mu, sigma)) de modo a que o logaritário do valor de retorno seja normalmente distribuído

Um exemplo de uma definição de espaço parâmetro:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Este código define um espaço de `learning_rate` pesquisa `keep_probability`com dois parâmetros - e . `learning_rate`tem uma distribuição normal com valor médio 10 e um desvio padrão de 3. `keep_probability`tem uma distribuição uniforme com um valor mínimo de 0,05 e um valor máximo de 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Amostrando o espaço do hiperparâmetro

Também pode especificar o método de amostragem do parâmetro para utilizar sobre a definição de espaço hiperparâmetro. O Azure Machine Learning suporta amostras aleatórias, amostragem de grelha e amostragem bayesiana.

#### <a name="picking-a-sampling-method"></a>Escolher um método de amostragem

* A amostragem da grelha pode ser usada se o seu espaço hiperparâmetro pode ser definido como uma escolha entre valores discretos e se tiver orçamento suficiente para pesquisar exaustivamente todos os valores no espaço de pesquisa definido. Além disso, pode-se utilizar a rescisão precoce automatizada de corridas mal executados, o que reduz o desperdício de recursos.
* A amostragem aleatória permite que o espaço do hiperparâmetro inclua hiperparâmetros discretos e contínuos. Na prática produz bons resultados na maior parte dos tempos e também permite o uso de terminação precoce automatizada de corridas mal executados. Alguns utilizadores realizam uma pesquisa inicial utilizando amostras aleatórias e, em seguida, refinam iterativamente o espaço de pesquisa para melhorar os resultados.
* A amostragem bayesiana aproveita o conhecimento de amostras anteriores ao escolher valores hiperparâmetros, tentando efetivamente melhorar a métrica primária relatada. Recomenda-se a amostragem bayesiana quando você tem orçamento suficiente para explorar o espaço do hiperparâmetro - para obter os melhores resultados com amostragem bayesiana recomendamos usando um número máximo de corridas maiores ou igual a 20 vezes o número de hiperparâmetros a fina. Note-se que a amostragem bayesiana não apoia atualmente nenhuma política de rescisão antecipada.

#### <a name="random-sampling"></a>Amostragem aleatória

Em amostragem aleatória, os valores do hiperparâmetro são selecionados aleatoriamente a partir do espaço de pesquisa definido. [A amostragem aleatória](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) permite que o espaço de pesquisa inclua hiperparâmetros discretos e contínuos.

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

[A amostragem](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) da grelha realiza uma simples pesquisa de grelha sobre todos os valores viáveis no espaço de pesquisa definido. Só pode ser utilizado com hiperparâmetros especificados utilizando `choice`. Por exemplo, o seguinte espaço tem um total de seis amostras:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Amostragem bayesiana

[A amostragem bayesiana baseia-se](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) no algoritmo de otimização bayesiana e faz escolhas inteligentes nos valores do hiperparâmetro para provar a seguir. Ele escolhe a amostra com base na forma como as amostras anteriores foram realizadas, de modo a que a nova amostra melhore a métrica primária reportada.

Quando se utiliza amostragem bayesiana, o número de corridas simultâneas tem um impacto na eficácia do processo de afinação. Tipicamente, um número menor de corridas simultâneas pode levar a uma melhor convergência de amostras, uma vez que o menor grau de paralelismo aumenta o número de corridas que beneficiam de corridas previamente concluídas.

A amostragem bayesiana apenas `choice`suporta, `uniform`e `quniform` distribui sobre o espaço de pesquisa.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> A amostragem bayesiana não suporta qualquer política de rescisão antecipada (Ver Especificar uma política de [rescisão antecipada).](#specify-early-termination-policy) Quando utilizar a amostragem do `early_termination_policy = None`parâmetro bayesiano, coloque ou deixe de fora o `early_termination_policy` parâmetro.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Especificar a métrica primária

Especifique a [métrica primária](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) que pretende que a experiência de afinação do hiperparâmetro otimize. Cada treino é avaliado para a métrica primária. Serão encerradas as corridas mal sucedidas (quando a métrica primária não satisfaz os critérios estabelecidos pela política de rescisão antecipada). Além do nome métrico primário, também especifica o objetivo da otimização - quer maximizar ou minimizar a métrica primária.

* `primary_metric_name`: O nome da métrica primária para otimizar. O nome da métrica primária precisa de corresponder exatamente ao nome da métrica registada pelo script de treino. Consulte [as métricas de registo para afinação do hiperparâmetro](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Pode ser `PrimaryMetricGoal.MAXIMIZE` `PrimaryMetricGoal.MINIMIZE` ou determinar se a métrica primária será maximizada ou minimizada na avaliação das corridas. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Otimize as corridas para maximizar a "precisão".  Certifique-se de registar este valor no seu script de treino.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Métricas de log para afinação de hiperparâmetros

O script de treino para o seu modelo deve registar as métricas relevantes durante o treino do modelo. Quando configura résem a afinação do hiperparâmetro, especifice a métrica primária para utilizar para avaliar o desempenho da execução. (Ver [Especificar uma métrica primária para otimizar](#specify-primary-metric-to-optimize).)  No seu script de treino, deve registar esta métrica para que esteja disponível para o processo de afinação do hiperparâmetro.

Inicie esta métrica no seu script de treino com o seguinte corte de amostra:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

O script de `val_accuracy` treino calcula o e regista-o como "precisão", que é usado como a métrica primária. Cada vez que a métrica é registada, é recebida pelo serviço de afinação do hiperparâmetro. Cabe ao desenvolvedor de modelos determinar com que frequência reportar esta métrica.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a>Especificar a política de rescisão antecipada

Acabar com o mau desempenho corre automaticamente com uma política de rescisão antecipada. A rescisão reduz o desperdício de recursos e, em vez disso, utiliza estes recursos para explorar outras configurações de parâmetros.

Ao utilizar uma política de rescisão antecipada, pode configurar os seguintes parâmetros que controlam quando uma política é aplicada:

* `evaluation_interval`: a frequência para aplicar a apólice. Cada vez que o script de treino regista a métrica primária conta como um intervalo. Assim, `evaluation_interval` um de 1 aplicará a política sempre que o roteiro de formação reportar a métrica primária. Um `evaluation_interval` de 2 aplicará a política de cada vez que o guião de treino relata a métrica primária. Se não especificado, `evaluation_interval` está definido para 1 por defeito.
* `delay_evaluation`: atrasa a primeira avaliação política para um determinado número de intervalos. É um parâmetro opcional que permite que todas as configurações corram para um número mínimo inicial de intervalos, evitando a interrupção prematura dos treinos. Se especificada, a política aplica cada múltiplo de evaluation_interval que é maior ou igual a delay_evaluation.

A Azure Machine Learning apoia as seguintes Políticas de Rescisão Precoce.

### <a name="bandit-policy"></a>Política de bandidos

[Bandido](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) é uma política de rescisão baseada no fator de slack/folga/quantidade de folga e intervalo de avaliação. A política termina precocemente quaisquer corridas em que a métrica primária não esteja dentro do fator de folga especificado/quantidade de folga no que diz respeito ao melhor desempenho do treino. São necessários os seguintes parâmetros de configuração:

* `slack_factor`ou `slack_amount`: a folga permitida no que diz respeito ao melhor treino de desempenho. `slack_factor`especifica a folga permitida como uma razão. `slack_amount`especifica a folga permitida como uma quantidade absoluta, em vez de uma razão.

    Por exemplo, considere uma política de bandidos a ser aplicada no intervalo 10. Assuma que a melhor execução no intervalo 10 relatou uma métrica primária de 0.8 com o objetivo de maximizar a métrica primária. Se a política foi `slack_factor` especificada com um de 0,2, qualquer treino corre, cuja melhor métrica no intervalo`slack_factor`10 é inferior a 0,66 (0,8/1+ )) será terminada. Se em vez disso, a `slack_amount` política foi especificada com um de 0,2, qualquer treino corre, cuja `slack_amount`melhor métrica no intervalo 10 é inferior a 0,6 (0,8 - ) será terminada.
* `evaluation_interval`: a frequência para aplicar a apólice (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação política para um determinado número de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Neste exemplo, a política de rescisão antecipada é aplicada em todos os intervalos quando as métricas são reportadas, a partir do intervalo de avaliação 5. Qualquer corrida cuja melhor métrica seja inferior a 1/(1+0,1) ou 91% da melhor execução será encerrada.

### <a name="median-stopping-policy"></a>Política mediana de paragem

[A paragem mediana](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) é uma política de rescisão antecipada baseada nas médias de execução das métricas primárias reportadas pelas corridas. Esta política calcula que as médias de execução em todas as corridas de treino e termina executam cujo desempenho é pior do que a mediana das médias de execução. Esta política tem os seguintes parâmetros de configuração:
* `evaluation_interval`: a frequência para aplicar a apólice (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação política para um determinado número de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Neste exemplo, a política de rescisão antecipada é aplicada em todos os intervalos a partir do intervalo de avaliação 5. Uma corrida será terminada no intervalo 5 se a sua melhor métrica primária for pior do que a mediana das médias de corrida ao longo dos intervalos 1:5 em todos os treinos.

### <a name="truncation-selection-policy"></a>Política de seleção de truncation

[A seleção de truncation](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) cancela uma determinada percentagem dos resultados mais baixos em cada intervalo de avaliação. As corridas são comparadas com base no seu desempenho na métrica primária e os X% mais baixos são terminados. São necessários os seguintes parâmetros de configuração:

* `truncation_percentage`: a percentagem de desempenho mais baixo é interrompida em cada intervalo de avaliação. Especifique um valor inteiro entre 1 e 99.
* `evaluation_interval`: a frequência para aplicar a apólice (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação política para um determinado número de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Neste exemplo, a política de rescisão antecipada é aplicada em todos os intervalos a partir do intervalo de avaliação 5. Uma corrida será terminada ao intervalo 5 se o seu desempenho no intervalo 5 estiver no menor desempenho de 20% de todas as corridas ao intervalo 5.

### <a name="no-termination-policy"></a>Sem política de rescisão

Se quer que todo o treino corra até à conclusão, desempente a política para nenhum. Isto terá por efeito não aplicar nenhuma política de rescisão antecipada.

```Python
policy=None
```

### <a name="default-policy"></a>Política de incumprimento

Se nenhuma política for especificada, o serviço de afinação do hiperparâmetro permitirá que todas as corridas de treino sejam executadas até à conclusão.

### <a name="picking-an-early-termination-policy"></a>Escolher uma política de rescisão antecipada

* Se procura uma política conservadora que proporcione poupanças sem acabar com empregos `evaluation_interval` promissores, pode utilizar uma Política mediana de paragem com 1 e `delay_evaluation` 5. Trata-se de configurações conservadoras, que podem proporcionar poupanças de aproximadamente 25%-35% sem perda na métrica primária (com base nos nossos dados de avaliação).
* Se procura poupanças mais agressivas a partir da rescisão antecipada, pode utilizar a Política de Bandidos com uma política de suticidade mais rigorosa (menor) permitida ou política de seleção de truncation com uma maior percentagem de truncation.

## <a name="allocate-resources"></a>Alocar recursos

Controle o seu orçamento de recursos para a sua experiência de afinação de hiperparâmetros, especificando o número máximo total de treinos.  Especificar opcionalmente a duração máxima para a sua experiência de afinação do hiperparâmetro.

* `max_total_runs`: Número máximo total de treinos que serão criados. Limite superior - pode haver menos corridas, por exemplo, se o espaço hiperparâmetro é finito e tem menos amostras. Deve ser um número entre 1 e 1000.
* `max_duration_minutes`: Duração máxima em minutos da experiência de afinação do hiperparâmetro. O parâmetro é opcional, e se presente, quaisquer corridas que estejam a funcionar após esta duração são automaticamente canceladas.

>[!NOTE] 
>Se `max_total_runs` ambos `max_duration_minutes` e forem especificados, a experiência de afinação do hiperparâmetro termina quando o primeiro destes dois limiares é atingido.

Além disso, especifique o número máximo de treinos para executar simultaneamente durante a sua pesquisa de afinação do hiperparâmetro.

* `max_concurrent_runs`: Número máximo de corridas a executar simultaneamente a qualquer momento. Se nenhum especificado, todos `max_total_runs` serão lançados em paralelo. Se especificado, deve ser um número entre 1 e 100.

>[!NOTE] 
>O número de execuções simultâneas é fechado nos recursos disponíveis no objetivo de cálculo especificado. Por isso, é necessário garantir que o objetivo do cálculo dispõe dos recursos disponíveis para a conmoeda desejada.

Alocar recursos para a finação do hiperparâmetro:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Este código configura a experiência de afinação do hiperparâmetro para utilizar um máximo de 20 execuções totais, executando quatro configurações de cada vez.

## <a name="configure-experiment"></a>Configurar experiência

[Configure a sua](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) experiência de afinação do hiperparâmetro utilizando o espaço de pesquisa definido do hiperparâmetro, a política de terminação precoce, a métrica primária e a alocação de recursos das secções acima. Além disso, `estimator` forneça um que será chamado com os hiperparâmetros amostrados. O `estimator` descreve o roteiro de treino que executa, os recursos por trabalho (único ou multi-gpu) e o alvo da computação a utilizar. Uma vez que a concurrency para a sua experiência de afinação de hiperparâmetros está fechada nos recursos disponíveis, certifique-se de que o alvo computacional especificado no `estimator` tem recursos suficientes para a sua conmoeda desejada. (Para obter mais informações sobre os estimadores, consulte [como treinar modelos](how-to-train-ml-models.md).)

Configure a sua experiência de afinação do hiperparâmetro:

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

## <a name="submit-experiment"></a>Submeter experiência

Assim que definir a configuração de afinação do hiperparâmetro, [submeta uma experiência:](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-)

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name`é o nome que atribui à sua experiência `workspace` de afinação de hiperparâmetros, e é o espaço de trabalho em que pretende criar a experiência (Para mais informações sobre experiências, veja como funciona a [Aprendizagem automática azure?](concept-azure-machine-learning-architecture.md))

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Início quente a sua experiência de afinação de hiperparâmetros (opcional)

Muitas vezes, encontrar os melhores valores de hiperparâmetro para o seu modelo pode ser um processo iterativo, precisando de várias correções de afinação que aprendem com as anteriores correções de afinação do hiperparâmetro. A reutilização do conhecimento destas corridas anteriores acelerará o processo de afinação do hiperparâmetro, reduzindo assim o custo de afinação do modelo e potencialmente melhorará a métrica primária do modelo resultante. Ao iniciar uma experiência de afinação de hiperparâmetros com amostragem bayesiana, os ensaios da execução anterior serão usados como conhecimento prévio para recolher inteligentemente novas amostras, para melhorar a métrica primária. Além disso, ao utilizar a amostragem Random ou Grid, quaisquer decisões de rescisão antecipada irão alavancar métricas dos ensaios anteriores para determinar o mau desempenho dos treinos. 

O Azure Machine Learning permite-lhe aquecer o seu hiperparâmetro de afinação, aproveitando o conhecimento de até 5 execuções de afinação de hiperparâmetros previamente completadas/canceladas. Pode especificar a lista de executões dos pais que pretende aquecer desde a utilização deste corte:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Além disso, pode haver ocasiões em que os treinos individuais de uma experiência de afinação de hiperparâmetros são cancelados devido a restrições orçamentais ou falham devido a outras razões. Agora é possível retomar este treino individual a partir do último posto de controlo (assumindo que o seu script de treino trata de pontos de verificação). Retomar um ensaio individual utilizará a mesma configuração do hiperparâmetro e montará a pasta de saídas utilizada para esse funcionamento. O script de `resume-from` treino deve aceitar o argumento, que contém o checkpoint ou os ficheiros modelo a partir dos quais retomar o treino. Pode retomar os treinos individuais utilizando o seguinte corte:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Pode configurar a sua experiência de afinação de hiperparâmetros para aquecer `resume_from` o `resume_child_runs` início de uma experiência anterior ou retomar os treinos individuais utilizando os parâmetros opcionais e no config:

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

## <a name="visualize-experiment"></a>Visualizar experiência

O Azure Machine Learning SDK fornece um [widget de Notebook](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) que visualiza o progresso dos seus treinos. O seguinte corte visualiza toda a sua afinação do hiperparâmetro corre num só lugar num caderno jupyter:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Este código exibe uma tabela com detalhes sobre as corridas de treino para cada uma das configurações do hiperparâmetro.

![tabela de afinação de hiperparâmetros](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Também pode visualizar o desempenho de cada uma das corridas à medida que o treino progride. 

![enredo de afinação hiperparâmetro](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Além disso, pode identificar visualmente a correlação entre o desempenho e os valores dos hiperparâmetros individuais utilizando um Enredo de Coordenadas Paralelas. 

[![coordenadas paralelas de afinação de hiperparâmetros](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Também pode visualizar todas as suas afinações de hiperparâmetros no portal web Azure. Para obter mais informações sobre como ver uma experiência no portal web, veja [como rastrear as experiências.](how-to-track-experiments.md#view-the-experiment-in-the-web-portal)

## <a name="find-the-best-model"></a>Encontre o melhor modelo

Uma vez concluídas todas as afinações do hiperparâmetro, [identifique a melhor configuração](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true--include-resume-from-runs-true-----typing-union-azureml-core-run-run--nonetype-) de desempenho e os valores hiperparâmetros correspondentes:

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
Consulte os cadernos de treina-hiperparâmetro-* nesta pasta:
* [como-usar-azureml/formação-com-aprendizagem profunda](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes
* [Rastreie uma experiência](how-to-track-experiments.md)
* [Implementar um modelo treinado](how-to-deploy-and-where.md)
