---
title: Gasodutos de aprendizagem de máquinas Azure trigger
titleSuffix: Azure Machine Learning
description: Os gasodutos desencadeados permitem automatizar tarefas rotineiras e demoradas, tais como processamento de dados, treino e monitorização.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 01/29/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 3ecf4458b052f4fdc0eb2e6e697b0468c71ce9c2
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519661"
---
# <a name="trigger-machine-learning-pipelines"></a>Gasodutos de aprendizagem de máquinas de gatilho

Neste artigo, você vai aprender a programar programáticamente um oleoduto para executar em Azure. Pode criar um horário baseado no tempo decorrido ou em alterações no sistema de ficheiros. Os horários baseados no tempo podem ser usados para tratar de tarefas rotineiras, tais como monitorização para a deriva de dados. Os horários baseados em alterações podem ser usados para reagir a alterações irregulares ou imprevisíveis, tais como novos dados a serem carregados ou dados antigos a serem editados. Depois de aprender a criar horários, aprenderá a recuperá-los e desativá-los. Finalmente, você vai aprender a usar outros serviços Azure, Azure Logic App e Azure Data Factory, para executar oleodutos. Uma App Azure Logic permite uma lógica ou comportamento de desencadeamento mais complexo. Os oleodutos Azure Data Factory permitem-lhe chamar um oleoduto de aprendizagem automática como parte de um oleoduto de orquestração de dados maior.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).

* Um ambiente Python no qual está instalado o Azure Machine Learning SDK para Python. Para obter mais informações, consulte [Criar e gerir ambientes reutilizáveis para treino e implantação com a Azure Machine Learning.](how-to-use-environments.md)

* Um espaço de trabalho de Machine Learning com um oleoduto publicado. Você pode usar o construído em [Criar e executar gasodutos de aprendizagem automática com Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md).

## <a name="trigger-pipelines-with-azure-machine-learning-sdk-for-python"></a>Gasodutos com Azure Machine Learning SDK para Python

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

O `ScheduleRecurrence` construtor tem um argumento obrigatório que deve ser uma das `frequency` seguintes cordas: "Minuto", "Hora", "Dia", "Semana" ou "Mês". Também requer um argumento inteiro `interval` especificando quantas unidades devem decorrer entre o início do `frequency` horário. Os argumentos opcionais permitem-lhe ser mais específico sobre os horários de início, conforme detalhado nos [docs SDK da AgendaRecurrence](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence).

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

Os oleodutos que são desencadeados por alterações de ficheiros podem ser mais eficientes do que os horários baseados no tempo. Quando pretender fazer algo antes de um ficheiro ser alterado, ou quando um novo ficheiro é adicionado a um diretório de dados, pode pré-processar esse ficheiro. Pode monitorizar quaisquer alterações a uma datastore ou alterações dentro de um diretório específico dentro da datastore. Se monitorizar um diretório específico, as alterações dentro das subdiretivas desse diretório _não_ desencadearão uma execução.

Para criar um ficheiro `Schedule` reativo, tem de definir o parâmetro na chamada para `datastore` [Agendar.criar](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Para monitorizar uma pasta, desate o `path_on_datastore` argumento.

O `polling_interval` argumento permite especificar, em minutos, a frequência em que a datastore é verificada para alterações.

Se o pipeline foi construído com um [DataPath](/python/api/azureml-core/azureml.data.datapath.datapath) [PipelineParameter,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter)pode definir essa variável para o nome do ficheiro alterado definindo o `data_path_parameter_name` argumento.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Argumentos opcionais ao criar um horário

Além dos argumentos discutidos anteriormente, pode definir o `status` argumento para criar um calendário `"Disabled"` inativo. Finalmente, `continue_on_step_failure` permite-lhe passar um Boolean que irá sobrepor-se ao comportamento de falha padrão do oleoduto.

## <a name="view-your-scheduled-pipelines"></a>Veja os seus oleodutos programados

No seu navegador Web, navegue para Azure Machine Learning. A partir da secção **ponto final** do painel de navegação, escolha **pontos finais do Pipeline**. Isto leva-o a uma lista dos oleodutos publicados no Espaço de Trabalho.

:::image type="content" source="./media/how-to-trigger-published-pipeline/scheduled-pipelines.png" alt-text="Página de gasodutos de AML":::

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

## <a name="use-azure-logic-apps-for-complex-triggers"></a>Use aplicativos Azure Logic para gatilhos complexos 

Regras ou comportamentos de gatilho mais complexos podem ser criados usando uma [App Azure Logic](../logic-apps/logic-apps-overview.md).

Para utilizar uma App Azure Logic para desencadear um oleoduto machine learning, você precisará do ponto final REST para um pipeline de Machine Learning publicado. [Crie e publique o seu pipeline.](./how-to-create-machine-learning-pipelines.md) Em seguida, encontre o ponto final REST do `PublishedPipeline` seu, utilizando o ID do gasoduto:

```python
# You can find the pipeline ID in Azure Machine Learning studio

published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
published_pipeline.endpoint 
```

## <a name="create-a-logic-app"></a>Criar uma aplicação do Logic Apps

Agora crie uma instância [da Azure Logic App.](../logic-apps/logic-apps-overview.md) Se desejar, [utilize um ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) e crie uma chave gerida pelo [cliente](../logic-apps/customer-managed-keys-integration-service-environment.md) para utilização pela sua App Lógica.

Uma vez que a sua App Lógica tenha sido aprovisionada, utilize estes passos para configurar um gatilho para o seu pipeline:

1. [Crie uma identidade gerida atribuída ao sistema](../logic-apps/create-managed-service-identity.md) para dar à app acesso ao seu espaço de trabalho de aprendizagem de máquinas Azure.

1. Navegue para a vista do Designer de Aplicativos Lógicas lógica e selecione o modelo de App Blank Logic. 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/blank-template.png" alt-text="Modelo em branco":::

1. No Designer, procure **por bolhas.** Selecione o gatilho **Quando uma bolha é adicionada ou modificada (apenas propriedades)** e adicione este gatilho à sua App Lógica.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/add-trigger.png" alt-text="Adicionar acionador":::

1. Preencha as informações de ligação para a conta de armazenamento Blob que deseja monitorizar para adições ou modificações blob. Selecione o Recipiente para monitorizar. 
 
    Escolha o **Intervalo** e **a Frequência** para fazer sondagem para obter atualizações que funcionem para si.  

    > [!NOTE]
    > Este gatilho irá monitorizar o recipiente selecionado, mas não monitoriza as sub-dobradeiras.

1. Adicione uma ação HTTP que será executada quando for detetada uma bolha nova ou modificada. Selecione **+ Novo Passo,** em seguida, procure e selecione a ação HTTP.

  > [!div class="mx-imgBorder"]
  > :::image type="content" source="media/how-to-trigger-published-pipeline/search-http.png" alt-text="Pesquisa rumo à ação HTTP":::

  Utilize as seguintes definições para configurar a sua ação:

  | Definição | Valor | 
  |---|---|
  | Ação HTTP | POST |
  | URI |o ponto final para o oleoduto publicado que encontrou como um [Pré-requisito](#prerequisites) |
  | Modo de autenticação | Identidade Gerida |

1. Desagram o seu horário para definir o valor de quaisquer [PipelineParameters dataPath](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) que possa ter:

    ```json
    {
      "DataPathAssignments": {
        "input_datapath": {
          "DataStoreName": "<datastore-name>",
          "RelativePath": "@{triggerBody()?['Name']}" 
        }
      },
      "ExperimentName": "MyRestPipeline",
      "ParameterAssignments": {
        "input_string": "sample_string3"
      },
      "RunSource": "SDK"
    }
    ```

    Utilize o `DataStoreName` seu espaço de trabalho como [pré-requisito.](#prerequisites)
     
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/http-settings.png" alt-text="Definições HTTP":::

1. **Selecione Save** e a sua agenda está agora pronta.

> [!IMPORTANT]
> Se estiver a utilizar o controlo de acesso baseado em funções (Azure RBAC) para gerir o acesso ao seu pipeline, [desajeitar as permissões para o seu cenário de pipeline (treino ou pontuação)](how-to-assign-roles.md#common-scenarios).

## <a name="call-machine-learning-pipelines-from-azure-data-factory-pipelines"></a>Pipelines de aprendizagem de máquinas de chamada a partir de oleodutos Azure Data Factory

Num oleoduto Azure Data Factory, a atividade do *Pipeline Machine Learning Execute* executa um oleoduto Azure Machine Learning. Pode encontrar esta atividade na página de autoria da Data Factory na categoria *Machine Learning:*

:::image type="content" source="media/how-to-trigger-published-pipeline/azure-data-factory-pipeline-activity.png" alt-text="Screenshot mostrando a atividade do gasoduto ML no ambiente de autoria da Azure Data Factory":::

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você usou o Azure Machine Learning SDK para Python para agendar um oleoduto de duas maneiras diferentes. Um horário repete-se com base na hora do relógio decorrido. O outro horário é executado se um ficheiro for modificado num determinado `Datastore` ou dentro de um diretório nessa loja. Viu como usar o portal para examinar o oleoduto e as corridas individuais. Aprendeu a desativar um horário para que o oleoduto deixe de funcionar. Finalmente, criou uma App Azure Logic para desencadear um oleoduto. 

Para obter mais informações, consulte:

> [!div class="nextstepaction"]
> [Utilize gasodutos de aprendizagem de máquinas Azure para pontuação de lotes](tutorial-pipeline-batch-scoring-classification.md)

* Saiba mais sobre [os oleodutos](concept-ml-pipelines.md)
* Saiba mais sobre [a exploração da Azure Machine Learning com o Jupyter](samples-notebooks.md)