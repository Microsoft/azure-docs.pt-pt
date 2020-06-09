---
title: Sintonize hiperparmetros para o seu modelo
titleSuffix: Azure Machine Learning
description: Sintonize eficientemente os hiperparmetros para o seu modelo de aprendizagem profunda / machine learning utilizando a Azure Machine Learning. Você aprenderá a definir o espaço de pesquisa de parâmetros, especificar uma métrica primária para otimizar, e terminar precocemente executando mal.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: seodec18, tracking-python
ms.openlocfilehash: b80122393fd71ecc7f09474759961ac52f5afb11
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560091"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Sintonize hiperparmetros para o seu modelo com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Sintonize eficientemente os hiperparmetros para o seu modelo utilizando a Azure Machine Learning.  A sintonização do hiperparímetro inclui os seguintes passos:

* Definir o espaço de pesquisa de parâmetros
* Especifique uma métrica primária para otimizar  
* Especificar critérios de rescisão antecipada para execuções mal executados
* Alocar recursos para afinação de hiperparímetros
* Lançar uma experiência com a configuração acima
* Visualizar as corridas de treino
* Selecione a configuração de melhor desempenho para o seu modelo

## <a name="what-are-hyperparameters"></a>O que são hiperparímetros?

Os hiperparmetros são parâmetros ajustáveis que escolhes treinar um modelo que rege o próprio processo de treino. Por exemplo, para treinar uma rede neural profunda, você decide o número de camadas ocultas na rede e o número de nós em cada camada antes de treinar o modelo. Estes valores geralmente permanecem constantes durante o processo de treino.

Em cenários de aprendizagem profunda/aprendizagem automática, o desempenho do modelo depende fortemente dos valores do hiperparímetro selecionados. O objetivo da exploração do hiperparímetro é pesquisar em várias configurações de hiperparímetro para encontrar uma configuração que resulte no melhor desempenho. Tipicamente, o processo de exploração do hiperparímetro é meticulosamente manual, dado que o espaço de pesquisa é vasto e a avaliação de cada configuração pode ser cara.

O Azure Machine Learning permite-lhe automatizar a exploração de hiperparímetros de forma eficiente, poupando-lhe tempo e recursos significativos. Especifica a gama de valores de hiperparímetro e um número máximo de treinos. Em seguida, o sistema lança automaticamente várias corridas simultâneas com diferentes configurações de parâmetros e encontra a configuração que resulta no melhor desempenho, medido pela métrica que escolher. Os treinos mal realizados são automaticamente encerrados, reduzindo o desperdício de recursos computacional. Estes recursos são, em vez disso, utilizados para explorar outras configurações de hiperparímetro.


## <a name="define-search-space"></a>Definir espaço de pesquisa

Sintonize automaticamente os hiperparmetros explorando a gama de valores definidos para cada hiperparímetro.

### <a name="types-of-hyperparameters"></a>Tipos de hiperparímetros

Cada hiperparmetro pode ser discreto ou contínuo e tem uma distribuição de valores descritos por uma [expressão de parâmetro](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py).

#### <a name="discrete-hyperparameters"></a>Hiperparímetros discretos 

Os hiperparmetros discretos são especificados como um `choice` entre valores discretos. `choice`pode ser:

* um ou mais valores separados por vírgula
* um `range` objeto
* qualquer objeto arbitrário `list`


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

Neste caso, `batch_size` assume um dos valores [16, 32, 64, 128] e assume um dos `number_of_hidden_layers` valores [1, 2, 3, 4].

Hiperparmetros discretos avançados também podem ser especificados usando uma distribuição. São suportadas as seguintes distribuições:

* `quniform(low, high, q)`- Devolve um valor como redondo (uniforme (baixo, alto) / q
* `qloguniform(low, high, q)`- Devolve um valor como redondo (exp(uniforme(baixo, alto)) / q
* `qnormal(mu, sigma, q)`- Devolve um valor como redondo (normal(mu, sigma) / q
* `qlognormal(mu, sigma, q)`- Devolve um valor como redondo (exp(normal(mu, sigma)) / q

#### <a name="continuous-hyperparameters"></a>Hiperparímetros contínuos 

Os hiperparmetros contínuos são especificados como uma distribuição sobre uma gama contínua de valores. As distribuições suportadas incluem:

* `uniform(low, high)`- Devolve um valor distribuído uniformemente entre baixo e alto
* `loguniform(low, high)`- Devolve um valor desenhado de acordo com exp (uniforme (baixo, alto)) de modo a que o logaritmo do valor de retorno seja distribuído uniformemente
* `normal(mu, sigma)`- Devolve um valor real que é normalmente distribuído com mu médio e sigma de desvio padrão
* `lognormal(mu, sigma)`- Devolve um valor desenhado de acordo com exp(mu,sigma)) de modo a que o logaritmo do valor de retorno seja normalmente distribuído

Um exemplo de uma definição de espaço de parâmetro:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Este código define um espaço de pesquisa com dois parâmetros - `learning_rate` e `keep_probability` . `learning_rate`tem uma distribuição normal com valor médio 10 e um desvio padrão de 3. `keep_probability`tem uma distribuição uniforme com um valor mínimo de 0,05 e um valor máximo de 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Amostragem do espaço do hiperparmetro

Também pode especificar o método de amostragem de parâmetros para utilizar sobre a definição de espaço hiperparímetro. A Azure Machine Learning suporta amostras aleatórias, amostragem de grelha e amostragem bayesiana.

#### <a name="picking-a-sampling-method"></a>Escolher um método de amostragem

* A amostragem da grelha pode ser usada se o seu espaço de hiperparímetro pode ser definido como uma escolha entre valores discretos e se tiver orçamento suficiente para pesquisar exaustivamente todos os valores no espaço de pesquisa definido. Além disso, pode-se utilizar a interrupção precoce automatizada de execuções mal executados, o que reduz o desperdício de recursos.
* A amostragem aleatória permite que o espaço hiperparímetro inclua hiperparímetros discretos e contínuos. Na prática, produz bons resultados na maior parte das vezes e também permite a utilização de terminações antecipadas automatizadas de execuções mal-executantes. Alguns utilizadores realizam uma pesquisa inicial usando amostragem aleatória e, em seguida, refinam iterativamente o espaço de pesquisa para melhorar os resultados.
* A amostragem bayesiana aproveita o conhecimento de amostras anteriores na escolha dos valores do hiperparímetro, tentando efetivamente melhorar a métrica primária reportada. Recomenda-se a amostragem bayesiana quando você tem orçamento suficiente para explorar o espaço do hiperparímetro - para obter os melhores resultados com a Amostragem Bayesiana recomendamos a utilização de um número máximo de corridas superior ou igual a 20 vezes o número de hiperparímetros a ser afinado. Note-se que a amostragem bayesiana não apoia atualmente qualquer política de rescisão antecipada.

#### <a name="random-sampling"></a>Amostragem aleatória

Na amostragem aleatória, os valores do hiperparímetro são selecionados aleatoriamente a partir do espaço de pesquisa definido. [A amostragem aleatória](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) permite que o espaço de pesquisa inclua hiperparímetros discretos e contínuos.

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

[A amostragem da](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) grelha realiza uma simples pesquisa de grelha sobre todos os valores viáveis no espaço de pesquisa definido. Só pode ser utilizado com hiperparímetros especificados utilizando `choice` . Por exemplo, o seguinte espaço tem um total de seis amostras:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Amostragem bayesiana

[A amostragem bayesiana](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) baseia-se no algoritmo de otimização bayesiano e faz escolhas inteligentes sobre os valores do hiperparímetro para amostrar a seguir. Recolhe a amostra com base na forma como as amostras anteriores foram realizadas, de modo a que a nova amostra melhore a métrica primária reportada.

Quando se utiliza a amostragem bayesiana, o número de execuções simultâneas tem um impacto na eficácia do processo de afinação. Tipicamente, um número menor de corridas simultâneas pode levar a uma melhor convergência de amostragem, uma vez que o menor grau de paralelismo aumenta o número de runs que beneficiam de execuções previamente concluídas.

A amostragem bayesiana só `choice` `uniform` suporta, e `quniform` distribui-se pelo espaço de pesquisa.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> A amostragem bayesiana não suporta nenhuma política de rescisão antecipada (ver [especificar uma política de rescisão antecipada).](#specify-early-termination-policy) Quando utilizar a amostragem do parâmetro Bayesiano, desa parte `early_termination_policy = None` do `early_termination_policy` parâmetro.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Especificar a métrica primária

Especifique a [métrica primária](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) que pretende que a experiência de sintonização do hiperparímetro otimize. Cada treino é avaliado para a métrica primária. As execuções mal executadas (em que a métrica primária não satisfaz os critérios estabelecidos pela política de rescisão antecipada) serão encerradas. Além do nome métrico primário, também especifica o objetivo da otimização - seja para maximizar ou minimizar a métrica primária.

* `primary_metric_name`: O nome da métrica primária para otimizar. O nome da métrica primária precisa corresponder exatamente ao nome da métrica registada pelo script de treino. Consulte [as métricas de registo para afinação do hiperparímetro](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Pode ser `PrimaryMetricGoal.MAXIMIZE` ou determinar se a `PrimaryMetricGoal.MINIMIZE` métrica primária será maximizada ou minimizada na avaliação das execuções. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Otimize as corridas para maximizar a "precisão".  Certifique-se de registar este valor no seu script de treino.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Métricas de registo para afinação de hiperparímetro

O roteiro de treino para o seu modelo deve registar as métricas relevantes durante o treino do modelo. Ao configurar a sintonização do hiperparímetro, especifica a métrica primária a utilizar para avaliar o desempenho do funcionamento. (Ver [Especificar uma métrica primária para otimizar](#specify-primary-metric-to-optimize).)  No seu script de treino, deve registar esta métrica para que esteja disponível para o processo de afinação do hiperparímetro.

Registar esta métrica no seu roteiro de treino com o seguinte corte de amostra:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

O script de treino calcula o `val_accuracy` e regista-o como "precisão", que é usado como a métrica primária. Cada vez que a métrica é registada é recebida pelo serviço de afinação do hiperparímetro. Cabe ao desenvolvedor do modelo determinar com que frequência reportar esta métrica.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a>Especificar a política de rescisão antecipada

Terminar o mau desempenho funciona automaticamente com uma política de rescisão antecipada. A rescisão reduz o desperdício de recursos e, em vez disso, utiliza estes recursos para explorar outras configurações de parâmetros.

Ao utilizar uma política de rescisão antecipada, pode configurar os seguintes parâmetros que controlam quando uma política é aplicada:

* `evaluation_interval`: a frequência de aplicação da apólice. Cada vez que o guião de treino regista a métrica primária conta como um intervalo. Assim, um `evaluation_interval` de 1 aplicará a política cada vez que o roteiro de formação reporta a métrica primária. Um `evaluation_interval` de 2 aplicará a apólice de vez em quando o roteiro de treino reporta a métrica primária. Se não for especificado, `evaluation_interval` é definido para 1 por predefinição.
* `delay_evaluation`: atrasa a primeira avaliação política para um número especificado de intervalos. É um parâmetro opcional que permite que todas as configurações corram para um número mínimo inicial de intervalos, evitando o fim prematuro dos treinos. Se especificado, a política aplica cada múltiplo de evaluation_interval que seja maior ou igual a delay_evaluation.

A Azure Machine Learning suporta as seguintes Políticas de Rescisão Antecipada.

### <a name="bandit-policy"></a>Política de bandidos

[O bandido](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) é uma política de rescisão baseada no fator de folga/tempo de avaliação. A política termina cedo quaisquer execuções em que a métrica primária não esteja dentro do fator de folga especificado/quantidade de folga no que diz respeito ao melhor desempenho do treino. São necessários os seguintes parâmetros de configuração:

* `slack_factor`ou `slack_amount` : a folga permitida no que diz respeito à melhor formação de desempenho. `slack_factor`especifica a folga admissível como uma relação. `slack_amount`especifica a folga admissível como uma quantidade absoluta, em vez de uma relação.

    Por exemplo, considere uma política de bandidos a ser aplicada no intervalo 10. Assuma que a melhor execução ao intervalo 10 reportou uma métrica primária 0.8 com o objetivo de maximizar a métrica primária. Se a apólice foi especificada com um `slack_factor` de 0.2, qualquer formação corre, cuja melhor métrica ao intervalo 10 é inferior a 0,66 (0,8/((1+ `slack_factor` )) será encerrada. Se, em vez disso, a apólice foi especificada com um `slack_amount` de 0.2, qualquer treino corre, cuja melhor métrica ao intervalo 10 é inferior a 0,6 (0,8 - `slack_amount` ) será encerrada.
* `evaluation_interval`: a frequência de aplicação da política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação política para um número especificado de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Neste exemplo, a política de rescisão antecipada é aplicada em todos os intervalos quando as métricas são reportadas, começando no intervalo de avaliação 5. Qualquer corrida cuja melhor métrica seja inferior (1/(1+0.1) ou 91% do melhor desempenho será encerrada.

### <a name="median-stopping-policy"></a>Política de paragem mediana

[A paragem mediana](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) é uma política de rescisão antecipada baseada nas médias de execução das métricas primárias reportadas pelas corridas. Esta política calcula as médias de execução em todas as pistas de treino e termina corridas cujo desempenho é pior do que a mediana das médias de corrida. Esta política requer os seguintes parâmetros de configuração:
* `evaluation_interval`: a frequência de aplicação da política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação política para um número especificado de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Neste exemplo, a política de rescisão antecipada é aplicada em todos os intervalos a partir do intervalo de avaliação 5. Uma corrida será terminada ao intervalo 5 se a sua melhor métrica primária for pior do que a mediana das médias de corrida em intervalos 1:5 em todas as pistas de treino.

### <a name="truncation-selection-policy"></a>Política de seleção de truncação

[A seleção de trunca](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) cancela uma dada percentagem de execuções mais baixas em cada intervalo de avaliação. As execuções são comparadas com base no seu desempenho na métrica primária e as mais baixas X% são terminadas. São necessários os seguintes parâmetros de configuração:

* `truncation_percentage`: a percentagem de desempenho mais baixo passa a terminar em cada intervalo de avaliação. Especifique um valor inteiro entre 1 e 99.
* `evaluation_interval`: a frequência de aplicação da política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação política para um número especificado de intervalos (parâmetro opcional).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Neste exemplo, a política de rescisão antecipada é aplicada em todos os intervalos a partir do intervalo de avaliação 5. Uma corrida será terminada ao intervalo 5 se o seu desempenho ao intervalo 5 estiver nos 20% mais baixos de desempenho de todas as corridas ao intervalo 5.

### <a name="no-termination-policy"></a>Sem política de rescisão

Se quer que todos os treinos se concretizem, desemote a política de Nenhum. Isto terá o efeito de não aplicar nenhuma política de rescisão antecipada.

```Python
policy=None
```

### <a name="default-policy"></a>Política de incumprimento

Se não for especificada nenhuma política, o serviço de afinação do hiperparímetro permitirá executar todas as execuções de treino até ao final.

### <a name="picking-an-early-termination-policy"></a>Escolher uma política de rescisão antecipada

* Se procura uma política conservadora que ofereça poupanças sem terminar empregos promissores, pode utilizar uma Política de Paragem Mediana com `evaluation_interval` 1 e `delay_evaluation` 5. Estas são configurações conservadoras, que podem fornecer cerca de 25%-35% de poupança sem perda na métrica primária (com base nos nossos dados de avaliação).
* Se procura poupanças mais agressivas desde a rescisão antecipada, pode utilizar a Política de Bandit com uma folga mais rigorosa (menor) ou política de seleção de truncação com uma maior percentagem de truncação.

## <a name="allocate-resources"></a>Alocar recursos

Controle o seu orçamento de recursos para a sua experiência de afinação de hiperparímetros especificando o número máximo total de treinos.  Especificar opcionalmente a duração máxima para a sua experiência de afinação de hiperparímetros.

* `max_total_runs`: Número máximo total de treinos que serão criados. Limite superior - pode haver menos corridas, por exemplo, se o espaço do hiperparímetro for finito e tiver menos amostras. Deve haver um número entre 1 e 1000.
* `max_duration_minutes`: Duração máxima em minutos da experiência de afinação do hiperparímetro. O parâmetro é opcional, e se presente, quaisquer execuções que estariam a decorrer após esta duração são automaticamente canceladas.

>[!NOTE] 
>Se ambos `max_total_runs` e `max_duration_minutes` forem especificados, a experiência de afinação do hiperparímetro termina quando o primeiro destes dois limiares é atingido.

Além disso, especifique o número máximo de treinos a ser executado simultaneamente durante a sua pesquisa de sintonização hiperparítnica.

* `max_concurrent_runs`: Número máximo de corridas a executar simultaneamente a qualquer momento. Se nenhum especificado, todos `max_total_runs` serão lançados em paralelo. Se especificado, deve ser um número entre 1 e 100.

>[!NOTE] 
>O número de execuções simultâneas está fechado nos recursos disponíveis no objetivo de computação especificado. Por isso, é necessário garantir que o alvo do cálculo tem os recursos disponíveis para a concordância desejada.

Alocar recursos para afinação de hiperparímetros:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Este código configura a experiência de sintonização do hiperparímetro para utilizar um máximo de 20 corridas totais, executando quatro configurações de cada vez.

## <a name="configure-experiment"></a>Experiência de configuração

[Configure a sua experiência de afinação de hiperparímetro](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) utilizando o espaço de pesquisa de hiperparímetro definido, a política de terminação antecipada, a métrica primária e a atribuição de recursos das secções acima. Além disso, forneça um `estimator` que será chamado com os hiperparímetros amostrados. O `estimator` descreve o roteiro de treino que executa, os recursos por trabalho (single ou multi-gpu) e o alvo de computação a utilizar. Uma vez que a concordância para a sua experiência de afinação de hiperparímetros está fechada nos recursos disponíveis, certifique-se de que o alvo de cálculo especificado no `estimator` tem recursos suficientes para a sua concordância desejada. (Para obter mais informações sobre os estimadores, consulte [como treinar modelos](how-to-train-ml-models.md).)

Configure a sua experiência de afinação de hiperparímetros:

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

Assim que definir a configuração de sintonização do hiperparímetro, [submeta uma experiência:](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-)

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name`é o nome que atribui à sua experiência de afinação de hiperparímetros, e `workspace` é o espaço de trabalho em que pretende criar a experiência (Para mais informações sobre experiências, veja [como funciona a Azure Machine Learning?](concept-azure-machine-learning-architecture.md))

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Comece quente a sua experiência de afinação de hiperparímetros (opcional)

Muitas vezes, encontrar os melhores valores de hiperparímetro para o seu modelo pode ser um processo iterativo, necessitando de múltiplas correções de afinação que aprendem com as anteriores correções de afinação do hiperparímetro. A reutilização do conhecimento destas execuções anteriores acelerará o processo de afinação do hiperparímetro, reduzindo assim o custo de afinação do modelo e potencialmente melhorará a métrica primária do modelo resultante. Quando o início quente de uma experiência de afinação de hiperparímetro com amostragem bayesiana, os ensaios da corrida anterior serão usados como conhecimento prévio para recolher inteligentemente novas amostras, para melhorar a métrica primária. Além disso, ao utilizar a amostragem Random ou Grid, quaisquer decisões de rescisão antecipada irão alavancar as métricas das execuções anteriores para determinar os treinos mal realizados. 

A Azure Machine Learning permite-lhe aquecer o seu hiperparaímetro afinando o conhecimento de até 5 ensaios de sintonização hiperparítrico previamente concluídos/cancelados. Pode especificar a lista de corridas dos pais que pretende aquecer a partir da utilização deste corte:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Além disso, pode haver ocasiões em que os treinos individuais de uma experiência de afinação de hiperparímetros são cancelados devido a restrições orçamentais ou falham por outras razões. Agora é possível retomar tais treinos individuais a partir do último ponto de verificação (assumindo que o seu script de treino lida com os pontos de verificação). Retomar uma formação individual utilizará a mesma configuração do hiperparímetro e montará a pasta de saídas utilizada para essa execução. O script de formação deve aceitar o `resume-from` argumento, que contém o ponto de verificação ou os ficheiros-modelo a partir dos quais retomar a formação. Pode retomar os treinos individuais utilizando o seguinte corte:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Pode configurar a sua experiência de afinação de hiperparímetro para aquecer a partir de uma experiência anterior ou retomar as corridas individuais de treino utilizando os parâmetros opcionais `resume_from` e `resume_child_runs` no config:

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

## <a name="visualize-experiment"></a>Visualizar a experiência

O Azure Machine Learning SDK fornece um [widget Notebook](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) que visualiza o progresso das suas corridas de treino. O seguinte corte visualiza toda a sua afinação hiperparítnica corre em um lugar em um lugar em um caderno Jupyter:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Este código apresenta uma tabela com detalhes sobre as corridas de treino para cada uma das configurações do hiperparímetro.

![mesa de afinação hiperparímetro](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Também pode visualizar o desempenho de cada uma das corridas à medida que o treino progride. 

![enredo de afinação hiperparaípeiro](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Além disso, pode identificar visualmente a correlação entre desempenho e valores de hiperparímetros individuais usando um Enredo de Coordenadas Paralelas. 

[![hiperparatómetro afinar coordenadas paralelas](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Também é possível visualizar todas as suas afinações de hiperparímetros no portal web Azure. Para obter mais informações sobre como ver uma experiência no portal web, [veja como rastrear experiências.](how-to-track-experiments.md#view-the-experiment-in-the-web-portal)

## <a name="find-the-best-model"></a>Encontre o melhor modelo

Uma vez concluídas todas as correções de afinação do hiperparímetro, [identifique a melhor configuração de desempenho](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true--include-resume-from-runs-true-----typing-union-azureml-core-run-run--nonetype-) e os valores correspondentes do hiperparímetro:

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
* [como usar-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes
* [Acompanhe uma experiência](how-to-track-experiments.md)
* [Implementar um modelo treinado](how-to-deploy-and-where.md)
