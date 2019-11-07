---
title: Grade de eventos do Azure Machine Learning esquema de evento
description: Descreve as propriedades que são fornecidas para eventos de Workspace do Machine Learning com a grade de eventos do Azure
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 6c2a8fa57bf6e3a552da57588bdbe752ef0d22e2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609570"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Esquema de evento da grade de eventos do Azure para Azure Machine Learning

Este artigo fornece as propriedades e o esquema para eventos de espaço de trabalho do Machine Learning. Para obter uma introdução aos esquemas de evento, consulte [esquema de evento da grade de eventos do Azure](event-schema.md).

Para obter uma lista de exemplos de scripts e tutoriais, consulte [origem do evento do AzureML](event-sources.md#azure-machine-learning).

## <a name="available-event-types"></a>Tipos de evento disponíveis

Azure Machine Learning emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft. MachineLearningServices. ModelRegistered | Gerado quando uma nova versão de modelo ou modelo foi registrada com êxito. |
| Microsoft. MachineLearningServices. ModelDeployed | Gerado quando modelo (s) foram implantados com êxito em um ponto de extremidade. |
| Microsoft. MachineLearningServices. RunCompleted | Gerado quando uma execução é concluída com êxito. |
| Microsoft. MachineLearningServices. DatasetDriftDetected | Gerado quando um monitor de descompasso de conjunto de uma detecta descompasso. |

## <a name="the-contents-of-an-event-response"></a>O conteúdo de uma resposta de evento

Quando um evento é disparado, o serviço de grade de eventos envia dados sobre esse evento para o ponto de extremidade de assinatura.

Esta seção contém um exemplo de como os dados seriam para cada evento.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Evento Microsoft. MachineLearningServices. ModelRegistered

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Evento Microsoft. MachineLearningServices. ModelDeployed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Evento Microsoft. MachineLearningServices. RunCompleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Evento Microsoft. MachineLearningServices. DatasetDriftDetected

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho completo do recurso para a origem do evento. Este campo não é gravável. A grade de eventos fornece esse valor. |
| Assunto | string | Caminho definido pelo Publicador para a entidade do evento. |
| EventType | string | Um dos tipos de evento registrados para essa origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Identificador exclusivo do evento. |
| dado | objeto | Dados de evento de armazenamento de BLOBs. |
| versão de | string | A versão do esquema do objeto de dados. O Publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. A grade de eventos define o esquema das propriedades de nível superior. A grade de eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades para cada tipo de evento:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft. MachineLearningServices. ModelRegistered

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| ModelName | string | O nome do modelo que foi registrado. |
| ModelVersion | int | A versão do modelo que foi registrado. |
| ModelTags | objeto | As marcas do modelo que foi registrado. |
| Modelproperties | objeto | As propriedades do modelo que foi registrado. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft. MachineLearningServices. ModelDeployed

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| ServiceName | string | O nome do serviço implantado. |
| Filecomputetype | string | O tipo de computação (por exemplo, ACI, AKS) do serviço implantado. |
| ModelIds | string | Uma lista de IDs de modelo separados em comum. As IDs dos modelos implantados no serviço. |
| Crachás | objeto | As marcas do serviço implantado. |
| ServiceProperties | objeto | As propriedades do serviço implantado. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft. MachineLearningServices. RunCompleted

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| Experimentoid | string | A ID do experimento ao qual a execução pertence. |
| experimentoname | string | O nome do experimento ao qual a execução pertence. |
| RunId | string | A ID da execução que foi concluída. |
| RunType | string | O tipo de execução da execução concluída. |
| RunTags | objeto | As marcas da execução concluída. |
| RunProperties | objeto | As propriedades da execução concluída. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft. MachineLearningServices. DatasetDriftDetected

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| Onficaid | string | A ID do monitor de descompasso de dados que disparou o evento. |
| Datalogoffname | string | O nome do monitor de descompasso de dados que disparou o evento. |
| RunId | string | A ID da execução que detectou descompasso de dados. |
| BaseDatasetId | string | A ID do conjunto de dados base usado para detectar descompasso. |
| TargetDatasetId | string | A ID do conjunto de código de destino usado para detectar descompasso. |
| DriftCoefficient | double | O resultado do coeficiente que disparou o evento. |
| StartTime | datetime | A hora de início da série temporal do conjunto de tempo de destino que resultou na detecção de descompasso.  |
| EndTime | datetime | A hora de término da série temporal do conjunto de tempo de destino que resultou na detecção de descompasso. |


## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à grade de eventos do Azure, consulte [o que é a grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da grade de eventos do Azure, consulte [esquema de assinatura da grade de eventos](subscription-creation-schema.md)
* Para obter uma introdução ao uso da grade de eventos do Azure com o Azure Machine Learning, consulte [consumir Azure Machine Learning eventos](/azure/machine-learning/service/concept-event-grid-integration)
* Para obter um exemplo de como usar a grade de eventos do Azure com Azure Machine Learning, consulte [criar fluxos de trabalho de aprendizado de máquina direcionados a eventos](/azure/machine-learning/service/event-schema-machine-learning)