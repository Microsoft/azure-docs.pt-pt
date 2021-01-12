---
title: Exportar ou apagar dados do espaço de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como exportar ou apagar o seu espaço de trabalho com o estúdio Azure Machine Learning, CLI, SDK e APIs de REPOUSO autenticado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c4f48acc2d6e57dea0a8db2a149d7ca2871c9f39
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98072008"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportar ou eliminar os dados do seu espaço de trabalho do serviço de Machine Learning

No Azure Machine Learning, pode exportar ou eliminar os dados do seu espaço de trabalho utilizando a interface gráfica do portal ou o Python SDK. Este artigo descreve ambas as opções.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Controle os seus dados do espaço de trabalho

Os dados do produto armazenados pela Azure Machine Learning estão disponíveis para exportação e eliminação. Você pode exportar e apagar usando a Azure Machine Learning studio, CLI e SDK. Os dados de telemetria podem ser acedidos através do portal Azure Privacy. 

No Azure Machine Learning, os dados pessoais consistem em informações do utilizador em documentos de histórico executados. 

## <a name="delete-high-level-resources-using-the-portal"></a>Eliminar recursos de alto nível usando o portal

Ao criar um espaço de trabalho, o Azure cria uma série de recursos dentro do grupo de recursos:

- O espaço de trabalho em si
- Uma conta de armazenamento
- Um registo de contentores
- Uma instância de Insights de Aplicações
- Um cofre chave

Estes recursos podem ser eliminados selecionando-os da lista e escolhendo **Eliminar** 

:::image type="content" source="media/how-to-export-delete-data/delete-resource-group-resources.png" alt-text="Screenshot do portal, com ícone de exclusão realçado":::

Os documentos de histórico, que podem conter informações pessoais do utilizador, são armazenados na conta de armazenamento de bolhas, em sub-dobradeiras de `/azureml` . Pode descarregar e eliminar os dados do portal.

:::image type="content" source="media/how-to-export-delete-data/storage-account-folders.png" alt-text="Screenshot do diretório azureml na conta de armazenamento, dentro do portal":::

## <a name="export-and-delete-machine-learning-resources-using-azure-machine-learning-studio"></a>Exportar e apagar recursos de aprendizagem automática usando o estúdio Azure Machine Learning

O estúdio Azure Machine Learning oferece uma visão unificada dos seus recursos de aprendizagem automática, tais como cadernos, conjuntos de dados, modelos e experiências. O estúdio Azure Machine Learning enfatiza a preservação de um registo dos seus dados e experiências. Recursos computacionais como oleodutos e recursos computacionais podem ser eliminados usando o navegador. Para estes recursos, navegue para o recurso em questão e escolha **Eliminar**. 

Os conjuntos de dados podem não estar registados e as experiências podem ser arquivadas, mas estas operações não apagam os dados. Para remover totalmente os dados, os conjuntos de dados e os dados de execução devem ser eliminados ao nível de armazenamento. A eliminação ao nível de armazenamento é feita utilizando o portal, como descrito anteriormente.

Você pode baixar artefactos de treino de corridas experimentais usando o Estúdio. Escolha a **Experiência** e **a Corrida** na qual está interessado. Escolha **saída + registos** e navegue para os artefactos específicos que deseja descarregar. Escolha **...** e **Faça o Download.**

Você pode baixar um modelo registado navegando para o **modelo** desejado e escolhendo **Download**. 

:::image type="contents" source="media/how-to-export-delete-data/model-download.png" alt-text="Screenshot da página do modelo do estúdio com opção de descarregamento em destaque":::

## <a name="export-and-delete-resources-using-the-python-sdk"></a>Exportar e eliminar recursos utilizando o Python SDK

Pode descarregar as saídas de uma determinada execução utilizando: 

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['my-experiment']
run = next(run for run in ex.get_runs() if run.id == run_id)
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Os seguintes recursos de aprendizagem automática podem ser eliminados utilizando o Python SDK: 

| Tipo | Chamada de função | Notas | 
| --- | --- | --- |
| `Workspace` | [`delete`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete-delete-dependent-resources-false--no-wait-false-) | Use `delete-dependent-resources` para cascata a eliminação |
| `Model` | [`delete`](/python/api/azureml-core/azureml.core.model%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--) | | 
| `ComputeTarget` | [`delete`](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--) | |
| `WebService` | [`delete`](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py) | |