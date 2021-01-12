---
title: 'Tutorial: Criar o modelo preditivo com um caderno (parte 1 de 2)'
titleSuffix: Azure Machine Learning
description: Aprenda a construir e implementar um modelo de aprendizagem automática utilizando código num Caderno Jupyter. Também crie um script de pontuação que define a entrada e a saída para uma fácil integração no Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 29b340448f3ce3e18a649065bdcd0b335bab8b73
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108250"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-jupyter-notebook-part-1-of-2"></a>Tutorial: Integração do Power BI - Crie o modelo preditivo com um Caderno Jupyter (parte 1 de 2)

Na parte 1 deste tutorial, treina-se e implementa-se um modelo preditivo de aprendizagem automática utilizando código num Caderno Jupyter. Também irá criar um script de pontuação para definir o esquema de entrada e saída do modelo para integração no Power BI.  Na parte 2, usará o modelo para prever os resultados no Microsoft Power BI.

Neste tutorial:

> [!div class="checklist"]
> * Criar um Bloco de Notas do Jupyter Notebook.
> * Crie uma instância computacional de aprendizagem automática Azure.
> * Treine um modelo de regressão utilizando scikit-learn.
> * Escreva um script de pontuação que define a entrada e saída para uma fácil integração no Microsoft Power BI.
> * Desloque o modelo para um ponto final de pontuação em tempo real.

Existem três formas de criar e implementar o modelo que vai utilizar no Power BI.  Este artigo abrange "Opção A: Treinar e implementar modelos utilizando cadernos."  Esta opção é uma experiência de autoria de código. Usa cadernos Jupyter que estão hospedados no Azure Machine Learning Studio. 

Mas em vez disso, podias usar uma das outras opções:

* [Opção B: Treine e implemente modelos utilizando o designer de aprendizagem automática Azure](tutorial-power-bi-designer-model.md). Esta experiência de autoria de código baixo utiliza uma interface de utilizador de arrastar e largar.
* [Opção C: Treine e implemente modelos utilizando machine learning automatizado](tutorial-power-bi-automated-model.md). Esta experiência de autoria sem código automatiza totalmente a preparação de dados e a formação de modelos.


## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se ainda não tiver uma subscrição, pode utilizar um [teste gratuito](https://aka.ms/AMLFree). 
- Uma área de trabalho do Azure Machine Learning. Se ainda não tem um espaço de trabalho, consulte [Criar e gerir espaços de trabalho de Aprendizagem automática Azure.](./how-to-manage-workspace.md#create-a-workspace)
- Conhecimento introdutório da língua python e fluxos de trabalho de aprendizagem automática.

## <a name="create-a-notebook-and-compute"></a>Criar um caderno e computar

Na página inicial do [**Azure Machine Learning Studio,**](https://ml.azure.com) selecione **Criar novo**  >  **Caderno**:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Screenshot mostrando como criar um caderno.":::
 
Na nova página de **ficheiros Criar:**

1. Nomeie o seu caderno (por exemplo, *my_model_notebook).*
1. Altere o **tipo de ficheiro** para o bloco de **notas.**
1. Selecione **Criar**. 
 
Em seguida, para executar células de código, crie uma instância computacional e prenda-a ao seu caderno. Comece por selecionar o ícone plus na parte superior do caderno:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Screenshot mostrando como criar uma instância de computação.":::

Na página **de instância de computação Create:**

1. Escolha um tamanho de máquina virtual CPU. Para este tutorial, pode escolher um **Standard_D11_v2,** com 2 núcleos e 14 GB de RAM.
1. Selecione **Seguinte**. 
1. Na página **Configurações configurações de configuração,** forneça um **nome compute** válido . Os caracteres válidos são letras maiúsculas e minúsculas, dígitos e hífens (-).
1. Selecione **Criar**.

No caderno, pode notar que o círculo ao lado do **Compute** virou ciano. Esta alteração de cor indica que a instância de computação está a ser criada:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Screenshot mostrando um cálculo a ser criado.":::

> [!NOTE]
> A instância de cálculo pode demorar 2 a 4 minutos a ser a provisionada.

Depois de o cálculo ser a provisionado, pode utilizar o caderno para executar células de código. Por exemplo, na célula pode escrever o seguinte código:

```python
import numpy as np

np.sin(3)
```

Em seguida, selecione Shift + Enter (ou selecione Control + Insira ou selecione o botão **Reproduzir** ao lado da célula). Deverá ver o seguinte resultado:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Imagem mostrando a saída de uma célula.":::

Agora estás pronto para construir um modelo de aprendizagem automática.

## <a name="build-a-model-by-using-scikit-learn"></a>Construa um modelo usando scikit-learn

Neste tutorial, você usa o [conjunto de dados diabetes.](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html) Este conjunto de dados está disponível em [Conjuntos de Dados Azure Open](https://azure.microsoft.com/services/open-datasets/).


### <a name="import-data"></a>Importar dados

Para importar os seus dados, copie o seguinte código e cole-os numa nova *célula de código* no seu caderno.

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

O `X_df` quadro de dados dos pandas contém 10 variáveis de entrada de base. Estas variáveis incluem idade, sexo, índice de massa corporal, pressão arterial média e seis medições de soro sanguíneo. O `y_df` quadro de dados dos pandas é a variável alvo. Contém uma medida quantitativa de progressão da doença um ano após a linha de base. O quadro de dados contém 442 registos.

### <a name="train-the-model"></a>Preparar o modelo

Crie uma nova *célula de código* no seu caderno. Em seguida, copie o seguinte código e cole-o na célula. Este código snippet constrói um modelo de regressão de crista e serializa o modelo utilizando o formato picles Python.

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Registar o modelo

Além do conteúdo do próprio ficheiro do modelo, o seu modelo registado irá armazenar metadados. Os metadados incluem a descrição do modelo, etiquetas e informações-quadro. 

Os metadados são úteis quando gere e implementa modelos no seu espaço de trabalho. Ao utilizar tags, por exemplo, pode categorizar os seus modelos e aplicar filtros quando lista modelos no seu espaço de trabalho. Além disso, se marcar este modelo com a estrutura scikit-learn, simplificará a sua implementação como um serviço web.

Copie o seguinte código e, em seguida, cole-o numa nova *célula de código* no seu caderno.

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

Também pode ver o modelo no Azure Machine Learning Studio. No menu à esquerda, selecione **Modelos:**

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Screenshot mostrando como ver um modelo.":::

## <a name="define-the-scoring-script"></a>Defina o roteiro de pontuação

Quando implementar um modelo que será integrado no Power BI, precisa de definir um *script de pontuação* Python e um ambiente personalizado. O script de pontuação contém duas funções:

- A `init()` função funciona quando o serviço começa. Carrega o modelo (que é automaticamente descarregado do registo do modelo) e deseriza-o.
- A `run(data)` função é executado quando uma chamada para o serviço inclui dados de entrada que precisam de ser marcados. 

>[!NOTE]
> Os decoradores Python no código abaixo definem o esquema dos dados de entrada e saída, que é importante para a integração no Power BI.

Copie o seguinte código e cole-o numa nova *célula de código* no seu caderno. O seguinte código snippet tem magia celular que escreve o código para um ficheiro chamado *score.py*.

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it can be serialized by JSON.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Definir o ambiente personalizado

Em seguida, definir o ambiente para marcar o modelo. No ambiente, defina os pacotes Python, como pandas e scikit-learn, que o script de pontuação *(score.py*) requer.

Para definir o ambiente, copie o seguinte código e cole-o numa nova *célula de código* no seu caderno.

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>Implementar o modelo

Para implementar o modelo, copie o seguinte código e cole-o numa nova *célula de código* no seu caderno:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> O serviço pode demorar 2 a 4 minutos a ser acionado.

Se o serviço for implementado com sucesso, deverá ver a seguinte saída:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

Também pode ver o serviço no Azure Machine Learning Studio. No menu à esquerda, selecione **Endpoints**:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Screenshot mostrando como ver o serviço.":::

Recomendamos que teste o serviço web para garantir que funciona como esperado. Para devolver o seu caderno, no Azure Machine Learning Studio, no menu à esquerda, selecione **Cadernos**. Em seguida, copie o seguinte código e cole-o numa nova *célula de código* no seu caderno para testar o serviço.

```python
import json


input_payload = json.dumps({
    'data': X_df[0:2].values.tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

A saída deve parecer-se com esta estrutura JSON: `{'predict': [[205.59], [68.84]]}` .

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, viu como construir e implementar um modelo para que possa ser consumido pelo Power BI. Na próxima parte, aprenderás a consumir este modelo num relatório do Power BI.

> [!div class="nextstepaction"]
> [Tutorial: Consumir um modelo em Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
