---
title: Agendar oleodutos de aprendizagem automática Azure
titleSuffix: Azure Machine Learning
description: Agende os gasodutos azure machine learning utilizando o Azure Machine Learning SDK para Python. Os oleodutos programados permitem automatizar tarefas de rotina, demoradas, tais como processamento de dados, treino e monitorização.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.openlocfilehash: fed411ea171274513308ec3efa68da80e4d25f8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77116753"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Agendar oleodutos de aprendizagem automática com Azure Machine Learning SDK para Python

Neste artigo, você vai aprender a programar programáticamente um oleoduto para funcionar em Azure. Pode optar por criar um horário baseado no tempo decorrido ou nas alterações do sistema de ficheiros. Os horários baseados no tempo podem ser usados para cuidar de tarefas rotineiras, tais como monitorização para deriva de dados. Os horários baseados na mudança podem ser usados para reagir a alterações irregulares ou imprevisíveis, tais como novos dados a serem carregados ou dados antigos a serem editados. Depois de aprender a criar horários, aprenderá a recuperá-los e desaticioná-los.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://aka.ms/AMLFree)

* Um ambiente Python no qual está instalado o Azure Machine Learning SDK para Python. Para mais informações, consulte [Criar e gerir ambientes reutilizáveis para formação e implantação com o Azure Machine Learning.](how-to-use-environments.md)

* Um espaço de trabalho de Machine Learning com um oleoduto publicado. Pode utilizar o incorporado em Criar e executar gasodutos de [aprendizagem automática com Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="initialize-the-workspace--get-data"></a>Inicializar o espaço de trabalho & obter dados

Para agendar um oleoduto, você precisará de uma referência ao seu espaço de trabalho, ao identificador do seu oleoduto publicado, e ao nome da experiência em que pretende criar o horário. Pode obter estes valores com o seguinte código:

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

## <a name="create-a-schedule"></a>Criar um horário

Para executar um oleoduto de forma recorrente, vai criar um horário. Um `Schedule` associado um oleoduto, uma experiência, e um gatilho. O gatilho pode`ScheduleRecurrence` ser um que descreve a espera entre as corridas ou um caminho datastore que especifica um diretório para assistir a alterações. Em qualquer dos casos, você precisará do identificador de gasoduto e o nome da experiência para criar o horário.

No topo do seu ficheiro `Schedule` python, importe as e `ScheduleRecurrence` classes:

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Criar um horário baseado no tempo

O `ScheduleRecurrence` construtor tem um `frequency` argumento exigido que deve ser uma das seguintes cordas: "Minuto", "Hora", "Dia", "Semana", ou "Mês". Também requer um `interval` argumento inteiro especificando quantas `frequency` das unidades devem decorrer entre o início do horário. Os argumentos opcionais permitem-lhe ser mais específico sobre os tempos de início, conforme detalhado nos [docs SDK scheduleRecurrence](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py).

Crie `Schedule` uma corrida que comece a cada 15 minutos:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Criar um horário baseado em mudanças

Os gasodutos que são desencadeados por alterações de ficheiros podem ser mais eficientes do que os horários baseados no tempo. Por exemplo, pode querer realizar uma etapa de pré-processamento quando um ficheiro é alterado, ou quando um novo ficheiro é adicionado a um diretório de dados. Pode monitorizar quaisquer alterações a uma loja de dados ou alterações dentro de um diretório específico dentro da loja de dados. Se monitorizar um diretório específico, as alterações dentro dos subdiretórios desse diretório _não_ desencadearão uma execução.

Para criar um reativo `Schedule`de ficheiros, tem de definir o `datastore` parâmetro na chamada para [Agendar.criar](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Para monitorizar uma pasta, detete o `path_on_datastore` argumento.

O `polling_interval` argumento permite especificar, em minutos, a frequência a que o datastore é verificado para obter alterações.

Se o gasoduto foi construído com um [DataPath](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) [PipelineParameter,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)pode definir essa variável para o nome do ficheiro alterado, definindo o `data_path_parameter_name` argumento.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on time",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Argumentos opcionais ao criar um horário

Além dos argumentos discutidos anteriormente, pode `status` definir `"Disabled"` o argumento para criar uma programação inativa. Finalmente, `continue_on_step_failure` permite-lhe passar por um Boolean que irá anular o comportamento de falha padrão do oleoduto.

## <a name="view-your-scheduled-pipelines"></a>Veja os seus oleodutos programados

No seu navegador Web, navegue para Azure Machine Learning. A partir da secção **Pontos Finais** do painel de navegação, escolha **pontos finais do Pipeline**. Isto leva-o a uma lista dos oleodutos publicados no Workspace.

![Página de pipelines da AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

Nesta página pode ver informações sumárias sobre todos os oleodutos no Espaço de Trabalho: nomes, descrições, estado, etc. Faça a broca clicando no seu oleoduto. Na página resultante, existem mais detalhes sobre o seu pipeline e poderá perfurar em corridas individuais.

## <a name="deactivate-the-pipeline"></a>Desativar o oleoduto

Se tiver `Pipeline` um que seja publicado, mas não agendado, pode desativá-lo com:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Se o oleoduto estiver programado, deve cancelar primeiro o horário. Recupere o identificador do horário do portal ou executando:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Uma vez `schedule_id` que tenha o desejo de desativar, corra:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Se correr `Schedule.list(ws)` de novo, deve conseguir uma lista vazia.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, usou o Azure Machine Learning SDK para python para agendar um oleoduto de duas maneiras diferentes. Um horário repete-se com base no tempo decorrido do relógio. O outro horário é executado se um `Datastore` ficheiro for modificado num determinado ou dentro de um diretório nessa loja. Viu como usar o portal para examinar o oleoduto e as corridas individuais. Finalmente, aprendeste a desativar um horário para que o oleoduto pare de funcionar.

Para obter mais informações, consulte:

> [!div class="nextstepaction"]
> [Utilize pipelines de aprendizagem automática azure para pontuação de lotes](tutorial-pipeline-batch-scoring-classification.md)

* Saiba mais sobre [os oleodutos](concept-ml-pipelines.md)
* Saiba mais sobre [explorar a Aprendizagem automática azure com jupyter](samples-notebooks.md)

