---
title: 'Tutorial: Criar o modelo preditivo com um Caderno (parte 1 de 2)'
titleSuffix: Azure Machine Learning
description: Aprenda a construir e implementar um modelo de aprendizagem automática usando código num Bloco de Notas Jupyter, para que possa usá-lo para prever resultados no Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f8209c0d26cf8c572d10666696231b0468cfcbc6
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370266"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-notebook-part-1-of-2"></a>Tutorial: Power BI integração - crie o modelo preditivo com um Caderno (parte 1 de 2)

Na primeira parte deste tutorial, treina-se e implementa-se um modelo preditivo de aprendizagem automática utilizando código num Caderno Jupyter. Na parte 2, usará o modelo para prever os resultados no Microsoft Power BI.

Neste tutorial:

> [!div class="checklist"]
> * Criar um Bloco de Notas do Jupyter Notebook
> * Criar um exemplo de cálculo de aprendizagem automática Azure
> * Treine um modelo de regressão usando scikit-learn
> * Implemente o modelo para um ponto final de pontuação em tempo real

Existem três maneiras diferentes de criar e implementar o modelo que vai utilizar no Power BI.  Este artigo abrange a Opção A: Treinar e implementar modelos utilizando Cadernos.  Esta opção mostra uma experiência de autoria de código usando cadernos Jupyter hospedados no estúdio Azure Machine Learning. 

Em vez disso, pode usar:

* [Opção B: Treine e implemente modelos utilizando designer](tutorial-power-bi-designer-model.md)- uma experiência de autoria de baixo código usando o Designer (uma interface de utilizador de arrastar e largar).
* [Opção C: Treine e implemente modelos utilizando ML automatizado](tutorial-power-bi-automated-model.md) - uma experiência de autoria sem código que automatiza totalmente a preparação de dados e a formação de modelos.


## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure[(está disponível um teste gratuito).](https://aka.ms/AMLFree) 
- Uma área de trabalho do Azure Machine Learning. Se ainda não tem um espaço de trabalho, siga [como criar um espaço de trabalho para aprendizagem de máquinas Azure.](./how-to-manage-workspace.md#create-a-workspace)
- Conhecimento introdutório da língua python e fluxos de trabalho de aprendizagem automática.

## <a name="create-a-notebook-and-compute"></a>Criar um caderno e computar

Na página inicial do [Azure Machine Learning Studio](https://ml.azure.com) selecione **Criar novo** seguido por **Notebook**:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Screenshot mostrando como criar um caderno":::
 
É-lhe mostrada uma caixa de diálogo para criar uma nova entrada **de ficheiro:**

1. Um nome de arquivo para o seu caderno (por `my_model_notebook` exemplo)
1. Alterar o **Tipo de Ficheiro** para Bloco de **Notas**

Selecione **Criar**. Em seguida, precisa de criar algum cálculo e anexá-lo ao seu caderno para executar as células de código. Para isso, selecione o ícone plus na parte superior do caderno:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Screenshot mostrando como criar instância computacional":::

Em seguida, na página **de instância de computação Create:**

1. Escolha um tamanho de máquina virtual CPU - para efeitos deste tutorial, um **Standard_D11_v2** (dois núcleos, 14-GB RAM) estará bem.
1. Selecione **Seguinte**. 
1. Na página **Configurações configurações de configuração** fornecem um **nome compute** válido (caracteres válidos são letras maiúsculas e minúsculas, dígitos e o - personagem).
1. Selecione **Criar**.

Pode notar no caderno que o círculo ao lado do **Compute** se transformou em ciano, indicando que a instância de computação está a ser criada:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Screenshot mostrando a criação do cálculo":::

> [!NOTE]
> Pode levar cerca de 2 a 4 minutos para o cálculo ser a provisionado.

Uma vez que o cálculo é provisionado, pode usar o caderno para executar células de código. Por exemplo, digite na célula:

```python
import numpy as np

np.sin(3)
```

Seguido por **Shift-Enter** (ou **Control-Enter** ou selecione o botão de reprodução ao lado da célula). Deverá ver o seguinte resultado:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Screenshot mostrando execução de células":::

Está agora pronto para construir um modelo de Machine Learning!

## <a name="build-a-model-using-scikit-learn"></a>Construa um modelo usando scikit-learn

Neste tutorial, utilize o [conjunto de dados diabetes,](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)que é disponibilizado em [Azure Open Datasets.](https://azure.microsoft.com/services/open-datasets/) 


### <a name="import-data"></a>Importar dados

Para importar os seus dados, copie e cole o código abaixo numa nova **célula de código** no seu caderno:

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

O `X_df` quadro de dados dos pandas contém 10 variáveis de entrada de base (como idade, sexo, índice de massa corporal, pressão arterial média e seis medições de soro sanguíneo). O `y_df` quadro de dados dos pandas é a variável-alvo que contém uma medida quantitativa de progressão da doença um ano após a linha de base. Há um total de 442 registos.

### <a name="train-model"></a>Preparar modelo

Crie uma nova célula de **código** no seu caderno e copie e cole o corte de código abaixo, que constrói um modelo de regressão de cume e serializa o modelo usando o formato pickle de Python:

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Registar o modelo

Além do conteúdo do próprio ficheiro do modelo, o seu modelo registado também armazenará metadados-modelo - descrição de modelos, etiquetas e informações-quadro - que serão úteis na gestão e implementação de modelos no seu espaço de trabalho. Usando tags, por exemplo, pode categorizar os seus modelos e aplicar filtros ao listar modelos no seu espaço de trabalho. Além disso, a marcação deste modelo com a estrutura scikit-learn simplificará a sua implementação como um serviço web, como veremos mais tarde.

Copiar e colar o código abaixo numa nova **célula de código** no seu caderno:

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

Também pode ver o modelo no Azure Machine Learning Studio navegando para **Endpoints** no menu da mão esquerda:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Modelo de exibição de screenshot":::

### <a name="define-the-scoring-script"></a>Defina o roteiro de pontuação

Ao implementar um modelo a integrar no Microsoft Power BI, é necessário definir um *script de pontuação* Python e um ambiente personalizado. O script de pontuação contém duas funções:

- `init()` - esta função é executada assim que o serviço começar. Esta função carrega o modelo (note que o modelo é automaticamente descarregado do registo do modelo) e o desseializa.
- `run(data)` - esta função é executada quando uma chamada é feita para o serviço com alguns dados de entrada que precisam de pontuação. 

>[!NOTE]
> Utilizamos decoradores Python para definir o esquema dos dados de entrada e saída, o que é importante para que a integração do Microsoft Power BI funcione.

Copiar e colar o código abaixo numa nova **célula de código** no seu caderno. O código abaixo tem uma magia celular que escreverá o código para um score.py arquivado.

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
    # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Definir o ambiente personalizado

Em seguida, precisamos definir o ambiente para marcar o modelo - precisamos definir neste ambiente os pacotes Python exigidos pelo script de pontuação (score.py) definido acima, como pandas, scikit-learn, etc.

Para definir o ambiente, copie e cole o código abaixo numa nova **célula de código** no seu caderno:

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

Para implementar o modelo, copie e cole o código abaixo numa nova **célula de código** no seu caderno:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> Pode levar 2 a 4 minutos para o serviço ser acionado.

Deve ver a seguinte saída de um serviço implantado com sucesso:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

Também pode ver o serviço no Azure Machine Learning Studio navegando para **Endpoints** no menu à mão esquerda:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Screenshot mostrando ponto final":::

Recomenda-se que teste o webservice para garantir que funciona como esperado. Volte ao seu caderno selecionando **Cadernos** no menu à esquerda no Azure Machine Learning Studio. Copiar e colar o código abaixo numa nova **célula de código** no seu caderno para testar o serviço:

```python
import json


input_payload = json.dumps({
    'data': X_df[0:2].values.tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

A saída deve parecer a seguinte estrutura json: `{'predict': [[205.59], [68.84]]}` .

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, viu como construir e implementar um modelo de forma a que possam ser consumidos pelo Microsoft Power BI. Na parte seguinte, aprende-se a consumir este modelo a partir de um relatório power bi.

> [!div class="nextstepaction"]
> [Tutorial: Consumir modelo em Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
