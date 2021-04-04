---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 95acb028867caf6f497dd99ad3082efcaab09c7b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93325295"
---
O script de entrada recebe os dados enviados para um serviço Web implementado e transmite-os para o modelo. Em seguida, seleciona a resposta devolvida pelo modelo e devolve-a ao cliente. *O script é específico do seu modelo.* Deve compreender os dados que o modelo espera e devolve.

As duas coisas que precisas de realizar no teu guião de entrada são:

1. Carregar o seu modelo (utilizando uma função `init()` chamada)
1. Executar o seu modelo em dados de entrada (utilizando uma função chamada `run()` )

Vamos ver estes passos em detalhe.

### <a name="writing-init"></a>Escrita init() 

#### <a name="loading-a-registered-model"></a>Carregar um modelo registado

Os seus modelos registados são armazenados num caminho apontado por uma variável ambiental chamada `AZUREML_MODEL_DIR` . Para obter mais informações sobre a estrutura exata do diretório, consulte [localizar ficheiros de modelos no seu script de entrada](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)

#### <a name="loading-a-local-model"></a>Carregar um modelo local

Se optou por não registar o seu modelo e passou o seu modelo como parte do seu diretório de origem, pode lê-lo como o faria localmente, passando o caminho para o modelo em relação ao seu script de pontuação. Por exemplo, se tivesse um diretório estruturado como:

```bash

- source_dir
    - score.py
    - models
        - model1.onnx

```

pode carregar os seus modelos com o seguinte código Python:

```python
import os

model = open(os.path.join('.', 'models', 'model1.onnx'))
```

### <a name="writing-run"></a>Execução de escrita()

`run()` é executado sempre que o seu modelo recebe um pedido de pontuação, e espera que o corpo do pedido seja um documento JSON com a seguinte estrutura:

```json
{
    "data": <model-specific-data-structure>
}

```

A entrada `run()` é uma cadeia Python contendo o que segue a chave "dados".

O exemplo a seguir demonstra como carregar um modelo de aprendizagem de scikit registado e marcá-lo com dados numpiados:

```python
import json
import numpy as np
import os
from sklearn.externals import joblib


def init():
    global model
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(data):
    try:
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Para exemplos mais avançados, incluindo a geração automática de esquemas de Swagger e dados binários (ou seja, imagem), leia [o artigo sobre a autoria de scripts de entrada avançada](../articles/machine-learning/how-to-deploy-advanced-entry-script.md)