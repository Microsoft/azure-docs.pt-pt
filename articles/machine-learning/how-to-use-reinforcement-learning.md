---
title: Treine e implemente um modelo de aprendizagem de reforço (pré-visualização).
titleSuffix: Azure Machine Learning
description: Aprenda a usar Azure Machine Learning Reinforcement Learning (pré-visualização) para treinar um agente RL para jogar Pong.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: peterlu
author: peterclu
ms.date: 05/05/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q2
ms.openlocfilehash: 4c03016d003978b3c56361595bec7c559205574b
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520885"
---
# <a name="reinforcement-learning-preview-with-azure-machine-learning"></a>Aprendizagem de reforço (pré-visualização) com Azure Machine Learning



> [!NOTE]
> A Azure Machine Learning Reinforcement Learning é atualmente uma funcionalidade de pré-visualização. Apenas as estruturas ray e RLlib são apoiadas neste momento.

Neste artigo, você aprende a treinar um agente de aprendizagem de reforço (RL) para jogar o videojogo Pong. Você usa a biblioteca python de código aberto [Ray RLlib](https://ray.readthedocs.io/en/master/rllib.html) com Azure Machine Learning para gerir a complexidade do RL distribuído.

Neste artigo, saiba como:
> [!div class="checklist"]
> * Crie uma experiência
> * Definir os acenos de cabeça e trabalhador
> * Criar um estimador de RL
> * Submeter uma experiência para começar uma corrida
> * Ver resultados

Este artigo baseia-se no [exemplo RLlib Pong](https://aka.ms/azureml-rl-pong) que pode ser encontrado no [repositório gitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md)do azure machine learning .

## <a name="prerequisites"></a>Pré-requisitos

Executar este código em qualquer um destes ambientes. Recomendamos que experimente a azure machine learning para a experiência de arranque mais rápida. Você pode clonar rapidamente e executar os cadernos de amostra de reforço em um caso de cálculo de aprendizagem automática Azure.

 - Instância de computação do Azure Machine Learning

     - Saiba como clonar cadernos de amostras em [Tutorial: Ambiente de configuração e espaço de trabalho.](tutorial-1st-experiment-sdk-setup.md)
         - Clone a pasta **como usar-azureml** em vez de **tutoriais**
     - Executar o caderno de configuração de rede virtual localizado `/how-to-use-azureml/reinforcement-learning/setup/devenv_setup.ipynb` em portas de rede abertas utilizadas para a aprendizagem de reforço distribuído.
     - Executar o caderno de amostras `/how-to-use-azureml/reinforcement-learning/atari-on-distributed-compute/pong_rllib.ipynb`
 
 - O seu próprio servidor de cadernos Jupyter

    - Instale o [Azure Machine Learning SDK](/python/api/overview/azure/ml/install).
    - Instale o [Azure Machine Learning RL SDK:](/python/api/azureml-contrib-reinforcementlearning/)`pip install --upgrade azureml-contrib-reinforcementlearning`
    - Crie um [ficheiro de configuração do espaço de trabalho.](how-to-configure-environment.md#workspace)
    - Executar a rede virtual para abrir portas de rede utilizadas para a aprendizagem de reforço distribuído.


## <a name="how-to-train-a-pong-playing-agent"></a>Como treinar um agente pong-playing

A aprendizagem do reforço (RL) é uma abordagem à aprendizagem automática que aprende fazendo. Enquanto outras técnicas de machine learning aprendem tomando passivamente dados de entrada e encontrando padrões dentro dele, RL usa **agentes de formação** para tomar decisões ativamente e aprender com os seus resultados.

Os seus agentes de treino aprendem a jogar Pong num **ambiente simulado.** Os agentes de treino tomam uma decisão em cada frame do jogo para mover a pá para cima, para baixo, ou para se manterem no lugar. Olha para o estado do jogo (uma imagem RGB do ecrã) para tomar uma decisão.

O RL usa **recompensas** para dizer ao agente se as suas decisões são bem sucedidas. Neste exemplo, o agente recebe uma recompensa positiva quando marca um ponto e uma recompensa negativa quando um ponto é marcado contra ele. Ao longo de muitas iterações, o agente de formação aprende a escolher a ação, com base no seu estado atual, que otimiza para a soma das recompensas futuras esperadas. É comum usar **redes neuronais profundas** (DNN) para realizar esta otimização em RL. 

O treino termina quando o agente atinge uma pontuação média de recompensa de 18 numa época de treino. Isto significa que o agente venceu o seu adversário por uma média de pelo menos 18 pontos em partidas até 21.

O processo de iteração através da simulação e da reconversão de um DNN é computacionalmente caro, e requer muitos dados. Uma forma de melhorar o desempenho dos empregos RL é **paralelamente** ao trabalho para que vários agentes de formação possam agir e aprender simultaneamente. No entanto, gerir um ambiente RL distribuído pode ser uma empresa complexa.

A Azure Machine Learning fornece o enquadramento para gerir estas complexidades para escalar as suas cargas de trabalho RL.

## <a name="set-up-the-environment"></a>Configurar o ambiente

Configurar o ambiente RL local por:
1. Carregamento dos pacotes Python necessários
1. Inicializar o seu espaço de trabalho
1. Criar uma experiência
1. Especificando uma rede virtual configurada.

### <a name="import-libraries"></a>Importar bibliotecas

Importe os pacotes Python necessários para executar o resto deste exemplo.

```python
# Azure ML Core imports
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
from azureml.core.runconfig import EnvironmentDefinition
from azureml.widgets import RunDetails
from azureml.tensorboard import Tensorboard

# Azure ML Reinforcement Learning imports
from azureml.contrib.train.rl import ReinforcementLearningEstimator, Ray
from azureml.contrib.train.rl import WorkerConfiguration
```

### <a name="initialize-a-workspace"></a>Inicializar um espaço de trabalho

Inicialize um objeto [de espaço de trabalho](concept-workspace.md) a partir do ficheiro criado na secção `config.json` [pré-requisitos](#prerequisites). Se estiver a executar este código numa Instância de Cálculo de Aprendizagem de Máquinas Azure, o ficheiro de configuração já foi criado para si.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-reinforcement-learning-experiment"></a>Criar uma experiência de aprendizagem de reforço

Crie uma [experiência](/python/api/azureml-core/azureml.core.experiment.experiment) para acompanhar a sua aprendizagem de reforço. Em Azure Machine Learning, as experiências são coleções lógicas de ensaios relacionados para organizar registos de corridas, história, saídas e muito mais.

```python
experiment_name='rllib-pong-multi-node'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="specify-a-virtual-network"></a>Especificar uma rede virtual

Para trabalhos RL que usam vários alvos de computação, deve especificar uma rede virtual com portas abertas que permita que nós de trabalhadores e nós de cabeça se comuniquem entre si.

A rede virtual pode estar em qualquer grupo de recursos, mas deve estar na mesma região que o seu espaço de trabalho. Para obter mais informações sobre a configuração da sua rede virtual, consulte o caderno de configuração do espaço de trabalho na secção pré-requisitos. Aqui, especifique o nome da rede virtual no seu grupo de recursos.

```python
vnet = 'your_vnet'
```

## <a name="define-head-and-worker-compute-targets"></a>Definir alvos de computação de cabeça e trabalhador

Este exemplo usa alvos de computação separados para os nós da cabeça de Ray e dos trabalhadores. Estas definições permitem escalar os seus recursos de cálculo para cima e para baixo, dependendo da sua carga de trabalho. Desa ajuste o número de nós e o tamanho de cada nó, com base nas suas necessidades.

### <a name="head-computing-target"></a>Alvo de computação de cabeça

Você pode usar um cluster de cabeça equipado com GPU para melhorar o desempenho da aprendizagem profunda. O nó de cabeça treina a rede neural que o agente usa para tomar decisões. O nó da cabeça também recolhe pontos de dados dos nós dos trabalhadores para treinar a rede neural.

O cálculo da cabeça utiliza uma única [ `STANDARD_NC6` máquina virtual](../virtual-machines/nc-series.md) (VM). Tem 6 CPUs virtuais para distribuir trabalho.


```python
from azureml.core.compute import AmlCompute, ComputeTarget

# choose a name for the Ray head cluster
head_compute_name = 'head-gpu'
head_compute_min_nodes = 0
head_compute_max_nodes = 2

# This example uses GPU VM. For using CPU VM, set SKU to STANDARD_D2_V2
head_vm_size = 'STANDARD_NC6'

if head_compute_name in ws.compute_targets:
    head_compute_target = ws.compute_targets[head_compute_name]
    if head_compute_target and type(head_compute_target) is AmlCompute:
        print(f'found head compute target. just use it {head_compute_name}')
else:
    print('creating a new head compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = head_vm_size,
                                                                min_nodes = head_compute_min_nodes, 
                                                                max_nodes = head_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    head_compute_target = ComputeTarget.create(ws, head_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    head_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(head_compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="worker-computing-cluster"></a>Cluster de computação de trabalhadores

Este exemplo utiliza quatro [ `STANDARD_D2_V2` VMs](../virtual-machines/nc-series.md) para o alvo de computação do trabalhador. Cada nó de trabalhador tem 2 CPUs disponíveis para um total de 8 CPUs disponíveis.

As GPUs não são necessárias para os nós operários, uma vez que não estão a realizar aprendizagens profundas. Os trabalhadores executam as simulações do jogo e recolhem dados.

```python
# choose a name for your Ray worker cluster
worker_compute_name = 'worker-cpu'
worker_compute_min_nodes = 0 
worker_compute_max_nodes = 4

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
worker_vm_size = 'STANDARD_D2_V2'

# Create the compute target if it hasn't been created already
if worker_compute_name in ws.compute_targets:
    worker_compute_target = ws.compute_targets[worker_compute_name]
    if worker_compute_target and type(worker_compute_target) is AmlCompute:
        print(f'found worker compute target. just use it {worker_compute_name}')
else:
    print('creating a new worker compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = worker_vm_size,
                                                                min_nodes = worker_compute_min_nodes, 
                                                                max_nodes = worker_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    worker_compute_target = ComputeTarget.create(ws, worker_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    worker_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(worker_compute_target.get_status().serialize())
```

## <a name="create-a-reinforcement-learning-estimator"></a>Criar um estimador de aprendizagem de reforço
Utilize o [ReforçoLearningEstimator](/python/api/azureml-contrib-reinforcementlearning/azureml.contrib.train.rl.reinforcementlearningestimator) para submeter um trabalho de formação à Azure Machine Learning.

A Azure Machine Learning usa aulas de estimador para encapsular informações de configuração de execução. Isto permite especificar como configurar uma execução de script. 

### <a name="define-a-worker-configuration"></a>Definir uma configuração de trabalhador

O objeto de Configuração do Trabalhador diz à Azure Machine Learning como inicializar o cluster de trabalhadores que executa o script de entrada.

```python
# Pip packages we will use for both head and worker
pip_packages=["ray[rllib]==0.8.3"] # Latest version of Ray has fixes for isses related to object transfers

# Specify the Ray worker configuration
worker_conf = WorkerConfiguration(
    
    # Azure ML compute cluster to run Ray workers
    compute_target=worker_compute_target, 
    
    # Number of worker nodes
    node_count=4,
    
    # GPU
    use_gpu=False, 
    
    # PIP packages to use
    pip_packages=pip_packages
)
```

### <a name="define-script-parameters"></a>Definir parâmetros de script

O roteiro de entrada `pong_rllib.py` aceita uma lista de parâmetros que definem como executar o trabalho de formação. Passar estes parâmetros através do estimador como uma camada de encapsulamento torna fácil alterar os parâmetros do script e executar configurações independentemente uns dos outros.

Especificar o correto `num_workers` aproveita ao máximo os seus esforços de paralelização. Fixar o número de trabalhadores para o mesmo número de CPUs disponíveis. Para este exemplo, pode utilizar o seguinte cálculo:

O nó da cabeça é um [Standard_NC6](../virtual-machines/nc-series.md) com 6 vCPUs. O cluster operário é de 4 [Standard_D2_V2 VMs](../cloud-services/cloud-services-sizes-specs.md#dv2-series) com 2 CPUs cada, num total de 8 CPUs. No entanto, deve subtrair 1 CPU da contagem de trabalhadores, uma vez que 1 deve ser dedicado ao papel do nó de cabeça.

6 CPUs + 8 CPUs - 1 CPU de cabeça = 13 trabalhadores simultâneos. A Azure Machine Learning usa aglomerados de cabeça e trabalhadores para distinguir recursos computacional. No entanto, Ray não distingue entre cabeça e trabalhadores, e todos os CPUs estão disponíveis como fios de trabalhador.


```python
training_algorithm = "IMPALA"
rl_environment = "PongNoFrameskip-v4"

# Training script parameters
script_params = {
    
    # Training algorithm, IMPALA in this case
    "--run": training_algorithm,
    
    # Environment, Pong in this case
    "--env": rl_environment,
    
    # Add additional single quotes at the both ends of string values as we have spaces in the 
    # string parameters, outermost quotes are not passed to scripts as they are not actually part of string
    # Number of GPUs
    # Number of ray workers
    "--config": '\'{"num_gpus": 1, "num_workers": 13}\'',
    
    # Target episode reward mean to stop the training
    # Total training time in seconds
    "--stop": '\'{"episode_reward_mean": 18, "time_total_s": 3600}\'',
}
```

### <a name="define-the-reinforcement-learning-estimator"></a>Definir o estimador de aprendizagem de reforço

Utilize a lista de parâmetros e o objeto de configuração do trabalhador para construir o estimador.

```python
# RL estimator
rl_estimator = ReinforcementLearningEstimator(
    
    # Location of source files
    source_directory='files',
    
    # Python script file
    entry_script="pong_rllib.py",
    
    # Parameters to pass to the script file
    # Defined above.
    script_params=script_params,
    
    # The Azure ML compute target set up for Ray head nodes
    compute_target=head_compute_target,
    
    # Pip packages
    pip_packages=pip_packages,
    
    # GPU usage
    use_gpu=True,
    
    # RL framework. Currently must be Ray.
    rl_framework=Ray(),
    
    # Ray worker configuration defined above.
    worker_configuration=worker_conf,
    
    # How long to wait for whole cluster to start
    cluster_coordination_timeout_seconds=3600,
    
    # Maximum time for the whole Ray job to run
    # This will cut off the run after an hour
    max_run_duration_seconds=3600,
    
    # Allow the docker container Ray runs in to make full use
    # of the shared memory available from the host OS.
    shm_size=24*1024*1024*1024
)
```

### <a name="entry-script"></a>Roteiro de entrada

O [script de entrada](https://aka.ms/azure-rl-pong-script) treina uma rede neural utilizando o ambiente `pong_rllib.py` [openai gym](https://github.com/openai/gym/) `PongNoFrameSkip-v4` . OpenAI Gyms são interfaces padronizadas para testar algoritmos de aprendizagem de reforço em jogos clássicos da Atari.

Este exemplo usa um algoritmo de formação conhecido como [IMPALA](https://arxiv.org/abs/1802.01561) (Importância Ponderada Actor-Learner Arquitetura). O IMPALA paraliza cada ator de aprendizagem individual para escalar em muitos nós computacional sem sacrificar a velocidade ou a estabilidade.

[Ray Tune](https://ray.readthedocs.io/en/latest/tune.html) orquestra as tarefas dos trabalhadores do IMPALA.

```python
import ray
import ray.tune as tune
from ray.rllib import train

import os
import sys

from azureml.core import Run
from utils import callbacks

DEFAULT_RAY_ADDRESS = 'localhost:6379'

if __name__ == "__main__":

    # Parse arguments
    train_parser = train.create_parser()

    args = train_parser.parse_args()
    print("Algorithm config:", args.config)

    if args.ray_address is None:
        args.ray_address = DEFAULT_RAY_ADDRESS

    ray.init(address=args.ray_address)

    tune.run(run_or_experiment=args.run,
             config={
                 "env": args.env,
                 "num_gpus": args.config["num_gpus"],
                 "num_workers": args.config["num_workers"],
                 "callbacks": {"on_train_result": callbacks.on_train_result},
                 "sample_batch_size": 50,
                 "train_batch_size": 1000,
                 "num_sgd_iter": 2,
                 "num_data_loader_buffers": 2,
                 "model": {
                    "dim": 42
                 },
             },
             stop=args.stop,
             local_dir='./logs')
```

### <a name="logging-callback-function"></a>Função de retorno de registo


O script de entrada utiliza uma função de utilidade para definir uma [função de retorno RLlib personalizada](https://docs.ray.io/en/latest/rllib-training.html#callbacks-and-custom-metrics) para registar métricas no seu espaço de trabalho de Aprendizagem de Máquinas Azure. Saiba como ver estas métricas na secção [Monitor e ver resultados.](#monitor-and-view-results)

```python
'''RLlib callbacks module:
    Common callback methods to be passed to RLlib trainer.
'''
from azureml.core import Run

def on_train_result(info):
    '''Callback on train result to record metrics returned by trainer.
    '''
    run = Run.get_context()
    run.log(
        name='episode_reward_mean',
        value=info["result"]["episode_reward_mean"])
    run.log(
        name='episodes_total',
        value=info["result"]["episodes_total"])
```

## <a name="submit-a-run"></a>Submeter uma corrida

[Run](/python/api/azureml-core/azureml.core.run%28class%29) lida com o histórico de trabalhos em curso ou completos. 

```python
run = exp.submit(config=rl_estimator)
```
> [!NOTE]
> A corrida pode demorar até 30 a 45 minutos para ser concluída.

## <a name="monitor-and-view-results"></a>Monitorizar e visualizar resultados

Utilize o widget Azure Machine Learning Jupyter para ver o estado das suas corridas em tempo real. O widget mostra duas corridas de crianças: uma para a cabeça e outra para os trabalhadores. 

```python
from azureml.widgets import RunDetails

RunDetails(run).show()
run.wait_for_completion()
```

1. Aguarde que o widget carregue.
1. Selecione a cabeça na lista de corridas.

**Selecione Clique aqui para ver a execução no estúdio Azure Machine Learning** para obter informações adicionais sobre a execução no estúdio. Pode aceder a esta informação enquanto a execução estiver em andamento ou depois de concluída.

![Gráfico de linha mostrando como executar widget de detalhes](./media/how-to-use-reinforcement-learning/pong-run-details-widget.png)

O **enredo episode_reward_mean** mostra o número médio de pontos marcados por época de treino. Pode ver que o agente de treino inicialmente teve um mau desempenho, perdendo os seus jogos sem marcar um único ponto (mostrado por um reward_mean de -21). Em 100 iterações, o agente de treino aprendeu a vencer o oponente por uma média de 18 pontos.

Se navegar em registos da execução da criança, pode ver os resultados da avaliação registados driver_log.txt ficheiro. Pode ter de esperar alguns minutos até que estas métricas fiquem disponíveis na página 'Executar'.

Em pouco trabalho, aprendeu a configurar múltiplos recursos computacionais para formar um agente de aprendizagem de reforço para jogar Pong muito bem contra um oppponent de computador.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a treinar um agente de aprendizagem de reforço usando um agente de aprendizagem IMPALA. Para ver exemplos adicionais, vá ao [repositório de aprendizagem do GitHub do Azure Machine Learning.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md)