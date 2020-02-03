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

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://aka.ms/AMLFree)

* Um ambiente do Python no qual o SDK do Azure Machine Learning para Python está instalado. Para mais informações, consulte [Criar e gerir ambientes reutilizáveis para formação e implantação com o Azure Machine Learning.](how-to-use-environments.md)

* Um espaço de trabalho Machine Learning com um pipeline publicado. Pode utilizar o incorporado em Criar e executar gasodutos de [aprendizagem automática com Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

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

Para executar um pipeline de forma recorrente, você criará uma agenda. Um `Schedule` associa um oleoduto, uma experiência e um gatilho. O gatilho pode ser um`ScheduleRecurrence` que descreve a espera entre as corridas ou um caminho datastore que especifica um diretório para assistir a alterações. Em ambos os casos, você precisará do identificador do pipeline e do nome do experimento no qual criar o agendamento.

### <a name="create-a-time-based-schedule"></a>Criar uma agenda baseada em tempo

O construtor `ScheduleRecurrence` tem um argumento `frequency` exigido que deve ser uma das seguintes cordas: "Minuto", "Hora", "Dia", "Semana", ou "Mês". Também requer um argumento inteiro `interval` especificando quantas das unidades `frequency` devem decorrer entre o início do horário. Os argumentos opcionais permitem-lhe ser mais específico sobre os tempos de início, conforme detalhado nos [docs SDK scheduleRecurrence](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py).

Crie uma `Schedule` que comece uma corrida a cada 15 minutos:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Criar uma agenda baseada em alteração

Os pipelines disparados por alterações de arquivo podem ser mais eficientes do que os agendamentos baseados em tempo. Por exemplo, você pode desejar executar uma etapa de pré-processamento quando um arquivo for alterado ou quando um novo arquivo for adicionado a um diretório de dados. Você pode monitorar qualquer alteração em um repositório de armazenamento ou alterações dentro de um diretório específico dentro do repositório de armazenamento. Se monitorizar um diretório específico, as alterações dentro dos subdiretórios desse diretório _não_ desencadearão uma execução.

Para criar um `Schedule`reativo de ficheiros, deve definir o parâmetro `datastore` na chamada para [Agendar.criar](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Para monitorizar uma pasta, detete o argumento `path_on_datastore`.

O argumento `polling_interval` permite especificar, em minutos, a frequência a que o datastore é verificado para obter alterações.

Se o gasoduto foi construído com um [DataPath](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) [PipelineParameter,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)pode definir essa variável para o nome do ficheiro alterado, definindo o argumento `data_path_parameter_name`.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on time",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Argumentos opcionais ao criar uma agenda

Além dos argumentos discutidos anteriormente, pode definir o argumento `status` para `"Disabled"` para criar uma programação inativa. Finalmente, o `continue_on_step_failure` permite-lhe passar um Boolean que irá anular o comportamento de falha padrão do oleoduto.

## <a name="view-your-scheduled-pipelines"></a>Exibir seus pipelines agendados

No navegador da Web, navegue até Azure Machine Learning. A partir da secção **Pontos Finais** do painel de navegação, escolha **pontos finais do Pipeline**. Isso leva você para uma lista dos pipelines publicados no espaço de trabalho.

![Página pipelines do AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

Nesta página, você pode ver informações resumidas sobre todos os pipelines no espaço de trabalho: nomes, descrições, status e assim por diante. Faça uma busca detalhada clicando em seu pipeline. Na página resultante, há mais detalhes sobre o pipeline e você pode fazer uma busca detalhada em execuções individuais.

## <a name="deactivate-the-pipeline"></a>Desativar o pipeline

Se tiver um `Pipeline` publicado, mas não agendado, pode desativá-lo com:

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

Se voltar a correr `Schedule.list(ws)`, deve conseguir uma lista vazia.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, você usou o SDK do Azure Machine Learning para Python para agendar um pipeline de duas maneiras diferentes. Uma agenda se repete com base no tempo decorrido do relógio. O outro horário é executado se um ficheiro for modificado num `Datastore` especificado ou dentro de um diretório nessa loja. Você viu como usar o portal para examinar o pipeline e as execuções individuais. Por fim, você aprendeu como desabilitar uma agenda para que o pipeline pare de funcionar.

Para obter mais informações, consulte:

> [!div class="nextstepaction"]
> [Utilize pipelines de aprendizagem automática azure para pontuação de lotes](tutorial-pipeline-batch-scoring-classification.md)

* Saiba mais sobre [os oleodutos](concept-ml-pipelines.md)
* Saiba mais sobre [explorar a Aprendizagem automática azure com jupyter](samples-notebooks.md)
