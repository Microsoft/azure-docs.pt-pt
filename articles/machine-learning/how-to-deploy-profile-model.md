---
title: Memória do modelo de perfil e utilização do CPU
titleSuffix: Azure Machine Learning
description: Aprenda a perfilar o seu modelo antes da implementação. O perfil determina a memória e o uso do CPU do seu modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
zone_pivot_groups: aml-control-methods
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: b9ae40b3d2673961f9b84ed702f18b25b79b6d0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93320403"
---
# <a name="profile-your-model-to-determine-resource-utilization"></a>Perfilar o seu modelo para determinar a utilização de recursos

Este artigo mostra como perfilar uma aprendizagem automática para modelar para determinar a quantidade de CPU e memória que terá de alocar para o modelo ao implantá-lo como um serviço web.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que treinou e registou um modelo com a Azure Machine Learning. Consulte [aqui](how-to-train-scikit-learn.md) o tutorial da amostra para um exemplo de formação e registo de um modelo de aprendizagem de scikit com Azure Machine Learning.

## <a name="limitations"></a>Limitações

* O perfil não funcionará quando o Registo do Contentor Azure (ACR) para o seu espaço de trabalho estiver por detrás de uma rede virtual.

## <a name="run-the-profiler"></a>Executar o perfil

Uma vez registado o seu modelo e preparado os outros componentes necessários para a sua implantação, pode determinar o CPU e a memória de que o serviço implantado necessitará. O perfil testa o serviço que executa o seu modelo e devolve informações como o uso do CPU, o uso da memória e a latência da resposta. Também fornece uma recomendação para o CPU e memória com base na utilização de recursos.

Para perfilar o seu modelo, necessitará:
* Um modelo registado.
* Uma configuração de inferência baseada no seu script de entrada e definição de ambiente de inferência.
* Um conjunto de dados tabular de coluna única, onde cada linha contém uma cadeia que representa dados de pedido de amostra.

> [!IMPORTANT]
> Neste momento, apenas apoiamos o perfil de serviços que esperam que os seus dados de pedido sejam uma cadeia, por exemplo: json serializado de cordas, texto, imagem serializada de cordas, etc. O conteúdo de cada linha do conjunto de dados (cadeia) será colocado no corpo do pedido HTTP e enviado para o serviço que encapsula o modelo para pontuação.

> [!IMPORTANT]
> Apoiamos apenas perfis até 2 CPUs na região da ChinaEast2 e USGovArizona.

Abaixo está um exemplo de como é possível construir um conjunto de dados de entrada para perfilar um serviço que espera que os dados do seu pedido de entrada contenham json serializado. Neste caso, criámos um conjunto de dados com base em 100 casos do mesmo conteúdo de dados de pedido. Em cenários reais, sugerimos que utilize conjuntos de dados maiores que contenham várias entradas, especialmente se o uso/comportamento do seu modelo de recursos depende.

::: zone pivot="py-sdk"

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Assim que tiver o conjunto de dados que contém dados de pedido de amostra prontos, crie uma configuração de inferência. A configuração da inferência baseia-se na score.py e na definição de ambiente. O exemplo a seguir demonstra como criar a configuração de inferência e executar perfis:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

::: zone-end

::: zone pivot="cli"


O seguinte comando demonstra como perfilar um modelo utilizando o CLI:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Para persistir a informação devolvida por perfis, utilize etiquetas ou propriedades para o modelo. A utilização de tags ou propriedades armazena os dados com o modelo no registo do modelo. Os seguintes exemplos demonstram a adição de uma nova etiqueta que contém as `requestedCpu` `requestedMemoryInGb` informações:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

::: zone-end

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas de uma implementação com falhas](how-to-troubleshoot-deployment.md)
* [Implementar no Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Criar aplicações de clientes para consumir serviços web](how-to-consume-web-service.md)
* [Atualizar serviços Web](how-to-deploy-update-web-service.md)
* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Utilizar o TLS para proteger um serviço Web através do Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorize os seus modelos de machine learning Azure com Insights de Aplicações](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
* [Criar alertas e gatilhos de eventos para implementações de modelos](how-to-use-event-grid.md)

