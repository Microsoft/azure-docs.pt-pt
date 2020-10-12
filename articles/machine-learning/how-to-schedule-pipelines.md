---
title: Agendar gasodutos de aprendizagem automática Azure
titleSuffix: Azure Machine Learning
description: Agendar os oleodutos Azure Machine Learning utilizando o Azure Machine Learning SDK para Python. Os gasodutos programados permitem automatizar tarefas rotineiras e demoradas, tais como processamento de dados, formação e monitorização.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: df9447160fe6a0aa2a3ae001ad8a337c3ff488f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91275948"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Agendar oleodutos de aprendizagem automática com Azure Machine Learning SDK para Python

Neste artigo, você vai aprender a programar programáticamente um oleoduto para executar em Azure. Pode optar por criar um horário baseado no tempo decorrido ou em alterações no sistema de ficheiros. Os horários baseados no tempo podem ser usados para tratar de tarefas rotineiras, tais como monitorização para a deriva de dados. Os horários baseados em alterações podem ser usados para reagir a alterações irregulares ou imprevisíveis, tais como novos dados a serem carregados ou dados antigos a serem editados. Depois de aprender a criar horários, aprenderá a recuperá-los e desativá-los.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).

* Um ambiente Python no qual está instalado o Azure Machine Learning SDK para Python. Para obter mais informações, consulte [Criar e gerir ambientes reutilizáveis para treino e implantação com a Azure Machine Learning.](how-to-use-environments.md)

* Um espaço de trabalho de Machine Learning com um oleoduto publicado. Você pode usar o construído em [Criar e executar gasodutos de aprendizagem automática com Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="initialize-the-workspace--get-data"></a>Inicialize o espaço de trabalho & obter dados

Para agendar um oleoduto, você precisará de uma referência ao seu espaço de trabalho, o identificador do seu oleoduto publicado, e o nome da experiência em que deseja criar o horário. Pode obter estes valores com o seguinte código:

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

Para executar um oleoduto numa base recorrente, criará um horário. Um `Schedule` associado um oleoduto, uma experiência e um gatilho. O gatilho pode ser um `ScheduleRecurrence` que descreve a espera entre as corridas ou um caminho datastore que especifica um diretório para observar as alterações. Em qualquer dos casos, você precisará do identificador do gasoduto e o nome da experiência para criar o horário.

No topo do seu ficheiro python, importe as `Schedule` classes e `ScheduleRecurrence` as classes:

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Criar um horário baseado no tempo

O `ScheduleRecurrence` construtor tem um argumento obrigatório que deve ser uma das `frequency` seguintes cordas: "Minuto", "Hora", "Dia", "Semana" ou "Mês". Também requer um argumento inteiro `interval` especificando quantas unidades devem decorrer entre o início do `frequency` horário. Os argumentos opcionais permitem-lhe ser mais específico sobre os horários de início, conforme detalhado nos [docs SDK da AgendaRecurrence](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py&preserve-view=true).

Criar uma `Schedule` corrida a cada 15 minutos:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Criar um horário baseado em alterações

Os oleodutos que são desencadeados por alterações de ficheiros podem ser mais eficientes do que os horários baseados no tempo. Por exemplo, pode querer executar um passo de pré-processamento quando um ficheiro é alterado, ou quando um novo ficheiro é adicionado a um diretório de dados. Pode monitorizar quaisquer alterações a uma datastore ou alterações dentro de um diretório específico dentro da datastore. Se monitorizar um diretório específico, as alterações dentro das subdiretivas desse diretório _não_ desencadearão uma execução.

Para criar um ficheiro `Schedule` reativo, tem de definir o parâmetro na chamada para `datastore` [Agendar.criar](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py&preserve-view=true#&preserve-view=truecreate-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Para monitorizar uma pasta, desate o `path_on_datastore` argumento.

O `polling_interval` argumento permite especificar, em minutos, a frequência em que a datastore é verificada para alterações.

Se o pipeline foi construído com um [DataPath](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py&preserve-view=true) [PipelineParameter,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py&preserve-view=true)pode definir essa variável para o nome do ficheiro alterado definindo o `data_path_parameter_name` argumento.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Argumentos opcionais ao criar um horário

Além dos argumentos discutidos anteriormente, pode definir o `status` argumento para criar um calendário `"Disabled"` inativo. Finalmente, `continue_on_step_failure` permite-lhe passar um Boolean que irá sobrepor-se ao comportamento de falha padrão do oleoduto.

### <a name="use-azure-logic-apps-for-more-complex-workflows"></a>Use aplicativos Azure Logic para fluxos de trabalho mais complexos

A Azure Logic Apps suporta fluxos de trabalho mais complexos e está muito mais amplamente integrado do que os oleodutos Azure Machine Learning. Consulte [o Trigger uma execução de um pipeline machine learning a partir de uma App lógica](how-to-trigger-published-pipeline.md) para obter mais informações.

## <a name="view-your-scheduled-pipelines"></a>Veja os seus oleodutos programados

No seu navegador Web, navegue para Azure Machine Learning. A partir da secção **ponto final** do painel de navegação, escolha **pontos finais do Pipeline**. Isto leva-o a uma lista dos oleodutos publicados no Espaço de Trabalho.

![Página de gasodutos de AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

Nesta página pode ver informações resumidas sobre todos os oleodutos no Espaço de Trabalho: nomes, descrições, estado e assim por diante. Faça um broco clicando no seu oleoduto. Na página resultante, há mais detalhes sobre o seu oleoduto e pode perfurar em corridas individuais.

## <a name="deactivate-the-pipeline"></a>Desativar o oleoduto

Se tiver um `Pipeline` que seja publicado, mas não agendado, pode desativá-lo com:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Se o gasoduto estiver programado, deve cancelar primeiro o horário. Recupere o identificador do horário a partir do portal ou executando:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Uma vez que tenha o `schedule_id` desejo de desativar, corra:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Se voltar a `Schedule.list(ws)` correr, deve ter uma lista vazia.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você usou o Azure Machine Learning SDK para Python para agendar um oleoduto de duas maneiras diferentes. Um horário repete-se com base na hora do relógio decorrido. O outro horário é executado se um ficheiro for modificado num determinado `Datastore` ou dentro de um diretório nessa loja. Viu como usar o portal para examinar o oleoduto e as corridas individuais. Finalmente, aprendeu a desativar um horário para que o gasoduto deixe de funcionar.

Para obter mais informações, consulte:

> [!div class="nextstepaction"]
> [Utilize gasodutos de aprendizagem de máquinas Azure para pontuação de lotes](tutorial-pipeline-batch-scoring-classification.md)

* Saiba mais sobre [os oleodutos](concept-ml-pipelines.md)
* Saiba mais sobre [a exploração da Azure Machine Learning com o Jupyter](samples-notebooks.md)

