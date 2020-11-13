---
title: Use o estúdio para implementar modelos treinados no designer
titleSuffix: Azure Machine Learning
description: Utilize o estúdio Azure Machine Learning para implementar modelos de machine learning sem escrever uma única linha de código.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: peterlu
ms.date: 10/29/2020
ms.topic: conceptual
ms.custom: how-to, deploy, studio
ms.openlocfilehash: 23c6417741d0753fcdaaf30c89c8f51348cc5dc5
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94554687"
---
# <a name="use-the-studio-to-deploy-models-trained-in-the-designer"></a>Use o estúdio para implementar modelos treinados no designer

Neste artigo, aprende-se a implementar um modelo treinado do designer como ponto final em tempo real no estúdio Azure Machine Learning.

A implantação no estúdio consiste nos seguintes passos:

1. Registe o modelo treinado.
1. Descarregue o ficheiro de script de entrada e dependências de conda para o modelo.
1. (Opcional) Configure o roteiro de entrada.
1. Desloque o modelo para um alvo de computação.

Também pode implementar modelos diretamente no designer para saltar as etapas de registo de modelos e de descarregamento de ficheiros. Isto pode ser útil para uma rápida implantação. Para mais informações [consulte, implemente um modelo com o designer.](tutorial-designer-automobile-price-deploy.md)

Os modelos treinados no designer também podem ser implementados através da interface SDK ou da linha de comando (CLI). Para obter mais informações, consulte [Implementar o seu modelo existente com a Azure Machine Learning.](how-to-deploy-existing-model.md)

## <a name="prerequisites"></a>Pré-requisitos

* [Um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md)

* Um gasoduto de formação completo contendo um dos seguintes módulos:
    - [Módulo modelo de trem](./algorithm-module-reference/train-model.md)
    - [Módulo de modelo de deteção de anomalias de comboio](./algorithm-module-reference/train-anomaly-detection-model.md)
    - [Módulo de modelo de clustering de trem](./algorithm-module-reference/train-clustering-model.md)
    - [Módulo modelo de Pytorch de trem](./algorithm-module-reference/train-pytorch-model.md)
    - [Módulo de recomendador SVD do comboio](./algorithm-module-reference/train-svd-recommender.md)
    - [Módulo modelo de Wabbit vowpal de trem](./algorithm-module-reference/train-vowpal-wabbit-model.md)
    - [Módulo de modelo profundo de & largo do comboio](./algorithm-module-reference/train-wide-and-deep-recommender.md)

## <a name="register-the-model"></a>Registar o modelo

Após a conclusão do pipeline de treino, registe o modelo treinado no seu espaço de trabalho Azure Machine Learning para aceder ao modelo noutros projetos.

1. Selecione o [módulo Modelo de Comboio](./algorithm-module-reference/train-model.md).
1. Selecione o **separador Saídas + registos** no painel direito.
1. Selecione o ícone **do modelo de registo** Screenshot do ícone da ![ engrenagem ](./media/how-to-deploy-model-designer/register-model-icon.png) .

    ![Screenshot do painel direito do módulo do modelo de comboio](./media/how-to-deploy-model-designer/train-model-right-pane.png)

1. Introduza um nome para o seu modelo e, em seguida, **selecione Guardar**.

Depois de registar o seu modelo, pode encontrá-lo na página de ativos dos **Modelos** no estúdio.
    
![Screenshot do modelo registado na página de ativos dos Modelos](./media/how-to-deploy-model-designer/models-asset-page.png)


## <a name="download-the-entry-script-file-and-conda-dependencies-file"></a>Descarregue o ficheiro de script de entrada e o ficheiro de dependências conda

Precisa dos seguintes ficheiros para implementar um modelo no estúdio Azure Machine Learning:

- **Ficheiro de script de entrada** - carrega o modelo treinado, processa os dados de entrada a partir de pedidos, faz inferências em tempo real e devolve o resultado. O designer gera automaticamente um `score.py` ficheiro de script de entrada quando o módulo Modelo de **Comboio** estiver concluído.

- **Ficheiro de dependências conda** - especifica quais os pacotes pip e conda de que depende o seu webservice. O designer cria automaticamente um `conda_env.yaml` ficheiro quando o módulo Modelo de **Comboio** termina.

Pode descarregar estes dois ficheiros no painel certo do módulo **Modelo de Comboio:**

1. Selecione o módulo **Modelo de Comboio.**
1. No separador **Saídas + registos,** selecione a pasta `trained_model_outputs` .
1. Descarregue o `conda_env.yaml` ficheiro e `score.py` o ficheiro.

    ![Screenshot de ficheiros de descarregamento para implantação no painel direito](./media/how-to-deploy-model-designer/download-artifacts-in-right-pane.png)

Em alternativa, pode descarregar os ficheiros da página do ativo **Dos Modelos** depois de registar o seu modelo:

1. Navegue para a página de ativos **dos Modelos.**
1. Selecione o modelo que pretende implementar.
1. Selecione o **separador Artefactos.**
1. Selecione a pasta `trained_model_outputs`.
1. Descarregue o `conda_env.yaml` ficheiro e `score.py` o ficheiro.  

    ![Screenshot de ficheiros de descarregamento para implementação na página de detalhes do modelo](./media/how-to-deploy-model-designer/download-artifacts-in-models-page.png)

> [!NOTE]
> O `score.py` ficheiro fornece quase a mesma funcionalidade que os módulos Do Modelo de **Pontuação.** No entanto, alguns módulos como [Score SVD Recommender](./algorithm-module-reference/score-svd-recommender.md), [Score Wide e Deep Recommender](./algorithm-module-reference/score-wide-and-deep-recommender.md)e [Score Vowpal Wabbit Model](./algorithm-module-reference/score-vowpal-wabbit-model.md) têm parâmetros para diferentes modos de pontuação. Também pode alterar esses parâmetros no script de entrada.
>
>Para obter mais informações sobre a definição de parâmetros no `score.py` ficheiro, consulte a secção, [configure o script de entrada](#configure-the-entry-script).

## <a name="deploy-the-model"></a>Implementar o modelo

Depois de descarregar os ficheiros necessários, está pronto para implementar o modelo.

1. Na página de ativos **dos Modelos,** selecione o modelo registado.
1. Selecione o **botão 'Implementar'.**
1. No menu de configuração, introduza as seguintes informações:

    - Insira um nome para o ponto final.
    - Selecione para implementar o modelo para o [Serviço Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md) ou [para a instância do contentor Azure](how-to-deploy-azure-container-instance.md).
    - Faça o upload do `score.py` ficheiro de script para **entrada**.
    - Faça o upload `conda_env.yml` do **ficheiro de dependências da Conda.** 

    >[!TIP]
    > Na definição **avançada,** pode definir a capacidade CPU/Memória e outros parâmetros para a implantação. Estas configurações são importantes para certos modelos, como os modelos PyTorch, que consomem uma quantidade considerável de memery (cerca de 4 GB).

1. Selecione **Implementar** para implementar o seu modelo como um ponto de final em tempo real.

    ![Screenshot do modelo de implementação na página de ativos do modelo](./media/how-to-deploy-model-designer/deploy-model.png)

## <a name="consume-the-real-time-endpoint"></a>Consumir o ponto final em tempo real

Após a implementação ter sucesso, pode encontrar o ponto final em tempo real na página de ativos **endpoints.** Uma vez lá, encontrará um ponto final REST, que os clientes podem usar para submeter pedidos ao ponto final em tempo real. 

> [!NOTE]
> O designer também gera um ficheiro json de dados de amostra para testes, pode descarregar `_samples.json` na pasta **trained_model_outputs.**

Utilize a seguinte amostra de código para consumir um ponto final em tempo real.

```python

import json
from pathlib import Path
from azureml.core.workspace import Workspace, Webservice
 
service_name = 'YOUR_SERVICE_NAME'
ws = Workspace.get(
    name='WORKSPACE_NAME',
    subscription_id='SUBSCRIPTION_ID',
    resource_group='RESOURCEGROUP_NAME'
)
service = Webservice(ws, service_name)
sample_file_path = '_samples.json'
 
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)
score_result = service.run(json.dumps(sample_data))
print(f'Inference result = {score_result}')
```

### <a name="consume-computer-vision-related-real-time-endpoints"></a>Consumir pontos finais relacionados com a visão computacional em tempo real

Ao consumir pontos finais relacionados com a visão do computador em tempo real, é necessário converter imagens em bytes, uma vez que o serviço web apenas aceita a corda como entrada. Segue-se o código de amostra:

```python
import base64
import json
from copy import deepcopy
from pathlib import Path
from azureml.studio.core.io.image_directory import (IMG_EXTS, image_from_file, image_to_bytes)
from azureml.studio.core.io.transformation_directory import ImageTransformationDirectory

# image path
image_path = Path('YOUR_IMAGE_FILE_PATH')

# provide the same parameter setting as in the training pipeline. Just an example here.
image_transform = [
    # format: (op, args). {} means using default parameter values of torchvision.transforms.
    # See https://pytorch.org/docs/stable/torchvision/transforms.html
    ('Resize', 256),
    ('CenterCrop', 224),
    # ('Pad', 0),
    # ('ColorJitter', {}),
    # ('Grayscale', {}),
    # ('RandomResizedCrop', 256),
    # ('RandomCrop', 224),
    # ('RandomHorizontalFlip', {}),
    # ('RandomVerticalFlip', {}),
    # ('RandomRotation', 0),
    # ('RandomAffine', 0),
    # ('RandomGrayscale', {}),
    # ('RandomPerspective', {}),
]
transform = ImageTransformationDirectory.create(transforms=image_transform).torch_transform

# download _samples.json file under Outputs+logs tab in the right pane of Train Pytorch Model module
sample_file_path = '_samples.json'
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)

# use first sample item as the default value
default_data = sample_data[0]
data_list = []
for p in image_path.iterdir():
    if p.suffix.lower() in IMG_EXTS:
        data = deepcopy(default_data)
        # convert image to bytes
        data['image'] = base64.b64encode(image_to_bytes(transform(image_from_file(p)))).decode()
        data_list.append(data)

# use data.json as input of consuming the endpoint
data_file_path = 'data.json'
with open(data_file_path, 'w') as f:
    json.dump(data_list, f)
```

## <a name="configure-the-entry-script"></a>Configure o script de entrada

Alguns módulos do designer como [Score SVD Recommender,](./algorithm-module-reference/score-svd-recommender.md) [Score Wide e Deep Recommender](./algorithm-module-reference/score-wide-and-deep-recommender.md)e [Score Vowpal Wabbit Model](./algorithm-module-reference/score-vowpal-wabbit-model.md) têm parâmetros para diferentes modos de pontuação. 

Nesta secção, também aprende a atualizar estes parâmetros no ficheiro do script de entrada.

O exemplo a seguir atualiza o comportamento padrão para um modelo **recomendador Wide & Deep** treinado. Por padrão, o `score.py` ficheiro diz ao serviço web para prever classificações entre utilizadores e itens. 

Pode modificar o ficheiro de script de entrada para fazer recomendações de produto e devolver os itens recomendados alterando o `recommender_prediction_kind` parâmetro.


```python
import os
import json
from pathlib import Path
from collections import defaultdict
from azureml.studio.core.io.model_directory import ModelDirectory
from azureml.designer.modules.recommendation.dnn.wide_and_deep.score. \
    score_wide_and_deep_recommender import ScoreWideAndDeepRecommenderModule
from azureml.designer.serving.dagengine.utils import decode_nan
from azureml.designer.serving.dagengine.converter import create_dfd_from_dict

model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'trained_model_outputs')
schema_file_path = Path(model_path) / '_schema.json'
with open(schema_file_path) as fp:
    schema_data = json.load(fp)


def init():
    global model
    model = ModelDirectory.load(load_from_dir=model_path)


def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    for row in data:
        for key, val in row.items():
            input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)

    # The parameter names can be inferred from Score Wide and Deep Recommender module parameters:
    # convert the letters to lower cases and replace whitespaces to underscores.
    score_params = dict(
        trained_wide_and_deep_recommendation_model=model,
        dataset_to_score=data_frame_directory,
        training_data=None,
        user_features=None,
        item_features=None,
        ################### Note #################
        # Set 'Recommender prediction kind' parameter to enable item recommendation model
        recommender_prediction_kind='Item Recommendation',
        recommended_item_selection='From All Items',
        maximum_number_of_items_to_recommend_to_a_user=5,
        whether_to_return_the_predicted_ratings_of_the_items_along_with_the_labels='True')
    result_dfd, = ScoreWideAndDeepRecommenderModule().run(**score_params)
    result_df = result_dfd.data
    return json.dumps(result_df.to_dict("list"))
```

Para os modelos **Wide & Deep recommender** e **Vowpal Wabbit,** pode configurar o parâmetro do modo de pontuação utilizando os seguintes métodos:

- Os nomes dos parâmetros são as combinações minúsculas e sublinhados de nomes de parâmetros para [Score Vowpal Wabbit Model](./algorithm-module-reference/score-vowpal-wabbit-model.md) e [Score Wide and Deep Recommender](./algorithm-module-reference/score-wide-and-deep-recommender.md);
- Os valores dos parâmetros do tipo de modo são as cadeias dos nomes de opção correspondentes. Tome como exemplo o **tipo de previsão do Recomendador** nos códigos acima, o valor pode ser ou `'Rating Prediction'` `'Item Recommendation'` . Outros valores não são permitidos.

Para o modelo treinado por **recomendadores SVD,** os nomes e valores dos parâmetros talvez menos óbvios, e você pode olhar para cima das tabelas abaixo para decidir como definir parâmetros.

| Nome do parâmetro no [Recomendador SVD de pontuação](./algorithm-module-reference/score-svd-recommender.md)                           | Nome do parâmetro no ficheiro do script de entrada |
| ------------------------------------------------------------ | --------------------------------------- |
| Tipo de previsão recomendador                                  | prediction_kind                         |
| Seleção de artigos recomendados                                   | recommended_item_selection              |
| Tamanho mínimo da piscina de recomendação para um único utilizador    | min_recommendation_pool_size            |
| Número máximo de itens a recomendar a um utilizador               | max_recommended_item_count              |
| Quer devolver as classificações previstas dos itens juntamente com os rótulos | return_ratings                          |

O código que se segue mostra como definir parâmetros para um recomendador SVD, que utiliza todos os seis parâmetros para recomendar itens classificados com classificações previstas anexadas.

```python
score_params = dict(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        training_data=None,
        # RecommenderPredictionKind has 2 members, 'RatingPrediction' and 'ItemRecommendation'. You
        # can specify prediction_kind parameter with one of them.
        prediction_kind=RecommenderPredictionKind.ItemRecommendation,
        # RecommendedItemSelection has 3 members, 'FromAllItems', 'FromRatedItems', 'FromUndatedItems'.
        # You can specify recommended_item_selection parameter with one of them.
        recommended_item_selection=RecommendedItemSelection.FromRatedItems,
        min_recommendation_pool_size=1,
        max_recommended_item_count=3,
        return_ratings=True,
    )
```


## <a name="next-steps"></a>Passos seguintes

* [Treine um modelo no designer](tutorial-designer-automobile-price-train-score.md)
* [Implementar modelos com Azure Machine Learning SDK](how-to-deploy-and-where.md)
* [Resolver problemas de uma implementação com falhas](how-to-troubleshoot-deployment.md)
* [Implementar no Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Criar aplicações de clientes para consumir serviços web](how-to-consume-web-service.md)
* [Atualizar serviços Web](how-to-deploy-update-web-service.md)
