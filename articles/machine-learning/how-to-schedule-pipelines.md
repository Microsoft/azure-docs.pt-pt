---
title: Agendar pipelines de Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Agende Azure Machine Learning pipelines usando o SDK do Azure Machine Learning para Python. Os pipelines agendados permitem automatizar tarefas demoradas de rotina, como processamento de dados, treinamento e monitoramento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.openlocfilehash: 1766b536043d8c404addb1877aa3ef9b57344ef4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722259"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Agendar pipelines do Machine Learning com o SDK do Azure Machine Learning para Python

Neste artigo, você aprenderá a agendar programaticamente um pipeline para ser executado no Azure. Você pode optar por criar uma agenda com base no tempo decorrido ou nas alterações do sistema de arquivos. Agendas baseadas em tempo podem ser usadas para cuidar de tarefas rotineiras, como o monitoramento de descompasso de dados. Os agendamentos baseados em alterações podem ser usados para reagir a alterações irregulares ou imprevisíveis, como novos dados carregados ou dados antigos sendo editados. Depois de aprender a criar agendas, você aprenderá a recuperá-las e desativá-las.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).

* Um ambiente do Python no qual o SDK do Azure Machine Learning para Python está instalado. Para obter mais informações, consulte [criar e gerenciar ambientes reutilizáveis para treinamento e implantação com o Azure Machine Learning.](how-to-use-environments.md)

* Um espaço de trabalho Machine Learning com um pipeline publicado. Você pode usar aquela criada para [criar e executar pipelines de Machine Learning com Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="initialize-the-workspace--get-data"></a>Inicializar o espaço de trabalho & obter dados

Para agendar um pipeline, você precisará de uma referência ao seu espaço de trabalho, o identificador do pipeline publicado e o nome do experimento no qual você deseja criar o agendamento. Você pode obter esses valores com o seguinte código:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>Criar uma agenda

Para executar um pipeline de forma recorrente, você criará uma agenda. Um `Schedule` associa um pipeline, um experimento e um gatilho. O gatilho pode ser um`ScheduleRecurrence` que descreve a espera entre execuções ou um caminho de repositório de armazenamento que especifica um diretório para observar as alterações. Em ambos os casos, você precisará do identificador do pipeline e do nome do experimento no qual criar o agendamento.

### <a name="create-a-time-based-schedule"></a>Criar uma agenda baseada em tempo

O construtor de `ScheduleRecurrence` tem um argumento de `frequency` necessário que deve ser uma das seguintes cadeias de caracteres: "minuto", "hora", "dia", "semana" ou "mês". Ele também requer um número inteiro `interval` argumento especificando quantas das unidades de `frequency` devem decorrer entre a agenda começar. Os argumentos opcionais permitem que você seja mais específico sobre horários de início, conforme detalhado nos [documentos do SDK do ScheduleRecurrence](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py).

Crie um `Schedule` que comece uma execução a cada 15 minutos:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Criar uma agenda baseada em alteração

Os pipelines disparados por alterações de arquivo podem ser mais eficientes do que os agendamentos baseados em tempo. Por exemplo, você pode desejar executar uma etapa de pré-processamento quando um arquivo for alterado ou quando um novo arquivo for adicionado a um diretório de dados. Você pode monitorar qualquer alteração em um repositório de armazenamento ou alterações dentro de um diretório específico dentro do repositório de armazenamento. Se você monitorar um diretório específico, as alterações nos subdiretórios desse diretório _não_ dispararão uma execução.

Para criar um arquivo-reativo `Schedule`, você deve definir o parâmetro `datastore` na chamada para [Schedule. Create](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Para monitorizar uma pasta, detete o argumento `path_on_datastore`.

O argumento `polling_interval` permite especificar, em minutos, a frequência a que o datastore é verificado para obter alterações.

Se o gasoduto foi construído com um [DataPath](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) [PipelineParameter,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)pode definir essa variável para o nome do ficheiro alterado, definindo o argumento `data_path_parameter_name`.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on time",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Argumentos opcionais ao criar uma agenda

Além dos argumentos discutidos anteriormente, você pode definir o argumento `status` como `"Disabled"` para criar uma agenda inativa. Finalmente, o `continue_on_step_failure` permite-lhe passar um Boolean que irá anular o comportamento de falha padrão do oleoduto.

## <a name="view-your-scheduled-pipelines"></a>Exibir seus pipelines agendados

No navegador da Web, navegue até Azure Machine Learning. Na seção **pontos de extremidade** do painel de navegação, escolha **pontos de extremidade de pipeline**. Isso leva você para uma lista dos pipelines publicados no espaço de trabalho.

![Página pipelines do AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

Nesta página, você pode ver informações resumidas sobre todos os pipelines no espaço de trabalho: nomes, descrições, status e assim por diante. Faça uma busca detalhada clicando em seu pipeline. Na página resultante, há mais detalhes sobre o pipeline e você pode fazer uma busca detalhada em execuções individuais.

## <a name="deactivate-the-pipeline"></a>Desativar o pipeline

Se você tiver um `Pipeline` que é publicado, mas não agendado, você pode desabilitá-lo com:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Se o pipeline estiver agendado, você deverá cancelar o agendamento primeiro. Recupere o identificador da agenda do portal ou executando:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Uma vez que tenha o `schedule_id` deseja desativar, corra:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Se você executar `Schedule.list(ws)` novamente, deverá obter uma lista vazia.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você usou o SDK do Azure Machine Learning para Python para agendar um pipeline de duas maneiras diferentes. Uma agenda se repete com base no tempo decorrido do relógio. A outra agenda será executada se um arquivo for modificado em um `Datastore` especificado ou dentro de um diretório nesse repositório. Você viu como usar o portal para examinar o pipeline e as execuções individuais. Por fim, você aprendeu como desabilitar uma agenda para que o pipeline pare de funcionar.

Para obter mais informações, veja:

> [!div class="nextstepaction"]
> [Usar pipelines de Azure Machine Learning para Pontuação de lote](tutorial-pipeline-batch-scoring-classification.md)

* Saiba mais sobre [pipelines](concept-ml-pipelines.md)
* Saiba mais sobre como [explorar Azure Machine Learning com Jupyter](samples-notebooks.md)
