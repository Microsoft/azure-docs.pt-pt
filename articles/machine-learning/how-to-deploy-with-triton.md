---
title: Modelo de alto desempenho servindo com Tritão (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a implementar o seu modelo com o NVIDIA Triton Inference Server em Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 2966b685e1904102467bf16994ea781556544047
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519202"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Serviço de alto desempenho com Servidor de Inferência de Tritão (Pré-visualização) 

Saiba como utilizar o [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) para melhorar o desempenho do serviço web utilizado para a inferência do modelo.

Uma das formas de implementar um modelo de inferência é como um serviço web. Por exemplo, uma implantação para o Serviço Azure Kubernetes ou instâncias de contentores Azure. Por predefinição, o Azure Machine Learning utiliza uma estrutura web de *propósito único* para implementações de serviço web.

Tritão é um quadro que está *otimizado para a inferência.* Proporciona uma melhor utilização das GPUs e uma inferência mais rentável. No lado do servidor, em lote solicita solicitações recebidas e envia estes lotes para inferência. O loteamento melhor utiliza os recursos da GPU, e é uma parte fundamental do desempenho de Triton.

> [!IMPORTANT]
> A utilização de Tritão para implantação a partir de Azure Machine Learning está atualmente em __pré-visualização__. A funcionalidade de pré-visualização pode não ser coberta pelo suporte ao cliente. Para obter mais informações, consulte os [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> Os fragmentos de código neste documento são para fins ilustrativos e podem não apresentar uma solução completa. Para obter o código de exemplo de trabalho, consulte as [amostras de ponta a ponta de Tritão em Azure Machine Learning](https://aka.ms/triton-aml-sample).

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição do Azure**. Se não tiver uma, experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* Familiaridade com [como e onde implementar um modelo](how-to-deploy-and-where.md) com Azure Machine Learning.
* O [Azure Machine Learning SDK para Python](/python/api/overview/azure/ml/) **ou** o [Azure CLI](/cli/azure/) e [extensão de aprendizagem automática.](reference-azure-machine-learning-cli.md)
* Uma instalação de trabalho do Docker para testes locais. Para obter informações sobre a instalação e validação do Docker, consulte [a Orientação e a configuração](https://docs.docker.com/get-started/) na documentação do estivador.

## <a name="architectural-overview"></a>Descrição geral da arquitetura

Antes de tentar usar Tritão para o seu próprio modelo, é importante entender como funciona com a Azure Machine Learning e como se compara a uma implementação padrão.

**Implementação padrão sem Tritão**

* Vários trabalhadores [de Gunicorn](https://gunicorn.org/) começam a lidar simultaneamente com pedidos de entrada.
* Estes trabalhadores lidam com o pré-processamento, chamando o modelo e após o processamento. 
* Os clientes usam o __Azure ML scoreing URI__. Por exemplo, `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Normal, não tritão, diagrama de arquitetura de implantação":::

**Implantação com Tritão diretamente**

* Os pedidos vão diretamente para o servidor tritão.
* Tritão processa pedidos em lotes para maximizar a utilização da GPU.
* O cliente usa o __Tritão URI__ para fazer pedidos. Por exemplo, `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Inferênciaconfig implantação apenas com Tritão, e sem middleware Python":::

**Implementação de configuração de inferência com Tritão**

* Vários trabalhadores [de Gunicorn](https://gunicorn.org/) começam a lidar simultaneamente com pedidos de entrada.
* Os pedidos são reencaminhados para o **servidor Triton.** 
* Tritão processa pedidos em lotes para maximizar a utilização da GPU.
* O cliente usa o __Azure ML pontuando URI__ para fazer pedidos. Por exemplo, `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Implantação com middleware Tritão e Python":::

O fluxo de trabalho para usar Tritão para a sua implementação de modelo é:

1. Sirva o seu modelo diretamente com Tritão.
1. Verifique se pode enviar pedidos para o seu modelo tritão.
1. (Opcional) Crie uma camada de middleware Python para o pré e pós-processamento do lado do servidor

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Implantação de Tritão sem Python pré e pós-processamento

Primeiro, siga os passos abaixo para verificar se o Servidor de Inferência de Tritão pode servir o seu modelo.

### <a name="optional-define-a-model-config-file"></a>(Opcional) Definir um ficheiro config modelo

O ficheiro de configuração do modelo diz a Tritão quantas entradas esperam e de que dimensões serão essas entradas. Para obter mais informações sobre a criação do ficheiro de configuração, consulte a [configuração](https://aka.ms/nvidia-triton-docs) do Modelo na documentação NVIDIA.

> [!TIP]
> Utilizamos a `--strict-model-config=false` opção ao iniciar o Servidor de Inferência de Tritão, o que significa que não precisa de fornecer um `config.pbtxt` ficheiro para os modelos ONNX ou TensorFlow.
> 
> Para obter mais informações sobre esta opção, consulte [a configuração do modelo Gerado](https://aka.ms/nvidia-triton-docs) na documentação NVIDIA.

### <a name="use-the-correct-directory-structure"></a>Use a estrutura correta do diretório

Ao registar um modelo com Azure Machine Learning, pode registar ficheiros individuais ou uma estrutura de diretório. Para utilizar Tritão, o registo do modelo deve ser para uma estrutura de diretório que contenha um diretório denominado `triton` . A estrutura geral deste diretório é:

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
                - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> Esta estrutura de diretório é um Repositório de Modelos de Tritão e é necessária para que os seus modelos trabalhem com Tritão. Para mais informações, consulte [os Repositórios de Modelos de Tritão](https://aka.ms/nvidia-triton-docs) na documentação da NVIDIA.

### <a name="register-your-triton-model"></a>Registe o seu modelo Tritão

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

Para obter mais `az ml model register` informações, consulte a [documentação de referência.](/cli/azure/ext/azure-cli-ml/ml/model)

# <a name="python"></a>[Python](#tab/python)


```python

from azureml.core.model import Model

model_path = "models"

model = Model.register(
    model_path=model_path,
    model_name="bidaf-9-tutorial",
    tags={"area": "Natural language processing", "type": "Question-answering"},
    description="Question answering from ONNX model zoo",
    workspace=ws,
    model_framework=Model.Framework.MULTI,  # This line tells us you are registering a Triton model
)

```
Para mais informações, consulte a documentação para a [classe Modelo.](/python/api/azureml-core/azureml.core.model.model)

---

### <a name="deploy-your-model"></a>Implemente o seu modelo

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

Se tiver um cluster de Serviço Azure Kubernetes ativado pela GPU chamado "aks-gpu" criado através do Azure Machine Learning, pode utilizar o seguinte comando para implementar o seu modelo.

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import InferenceConfig
from random import randint

service_name = "triton-webservice"

config = AksWebservice.deploy_configuration(
    compute_target_name="aks-gpu",
    gpu_cores=1,
    cpu_cores=1,
    memory_gb=4,
    auth_enabled=True,
)

service = Model.deploy(
    workspace=ws,
    name=service_name,
    models=[model],
    deployment_config=config,
    overwrite=True,
)
```
---

Consulte [esta documentação para obter mais detalhes sobre os modelos de implantação.](how-to-deploy-and-where.md)

### <a name="call-into-your-deployed-model"></a>Ligue para o seu modelo implantado

Primeiro, arranja as tuas fichas URI e Bearer.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)


```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

```python
import requests

print(service.scoring_uri)
print(service.get_keys())

```

---

Em seguida, certifique-se de que o seu serviço está a funcionar fazendo: 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

Este comando devolve informações semelhantes às seguintes. Note o `200 OK` seguinte; este estado significa que o servidor web está em execução.

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```

Uma vez realizada uma verificação de saúde, pode criar um cliente para enviar dados para Triton para inferência. Para obter mais informações sobre a criação de um cliente, consulte os [exemplos](https://aka.ms/nvidia-client-examples) do cliente na documentação da NVIDIA. Há também [amostras de Python no Tritão GitHub.](https://aka.ms/nvidia-triton-docs)

Neste ponto, se não quiser adicionar Python pré e pós-processamento ao seu serviço web implantado, poderá ser feito. Se quiser adicionar esta lógica de pré e pós-processamento, leia.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>(Opcional) Re-implantar com um script de entrada Python para pré e pós-processamento

Depois de verificar se o Tritão é capaz de servir o seu modelo, pode adicionar código pré e pós-processamento definindo um script de _entrada_. Este ficheiro tem o `score.py` nome. Para obter mais informações sobre scripts de entrada, consulte [definir um script de entrada](how-to-deploy-and-where.md#define-an-entry-script).

Os dois principais passos são inicializar um cliente Tritão HTTP no seu `init()` método, e chamar esse cliente na sua `run()` função.

### <a name="initialize-the-triton-client"></a>Inicializar o Cliente Tritão

Inclua código como o seguinte exemplo no seu `score.py` ficheiro. Tritão em Azure Machine Learning espera ser abordado na localidade, porto 8000. Neste caso, a localidade está dentro da imagem do Docker para esta implantação, não uma porta na sua máquina local:

> [!TIP]
> O `tritonhttpclient` pacote pip está incluído no ambiente `AzureML-Triton` curado, por isso não há necessidade de especirá-lo como uma dependência de pip.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Modifique o seu script de pontuação para ligar para Tritão

O exemplo a seguir demonstra como solicitar dinamicamente os metadados para o modelo:

> [!TIP]
> Pode solicitar dinamicamente os metadados dos modelos que foram carregados com Tritão utilizando o `.get_model_metadata` método do cliente Tritão. Consulte o [caderno de amostras](https://aka.ms/triton-aml-sample) para obter um exemplo da sua utilização.

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

### <a name="redeploy-with-an-inference-configuration"></a>Reimplantação com uma configuração de inferência

Uma configuração de inferência permite-lhe usar um script de entrada, bem como o processo de implementação de aprendizagem automática Azure usando o Python SDK ou Azure CLI.

> [!IMPORTANT]
> Deve especificar o `AzureML-Triton` [ambiente curado.](./resource-curated-environments.md)
>
> O código Python cita clones `AzureML-Triton` para outro ambiente chamado `My-Triton` . O código Azure CLI também utiliza este ambiente. Para obter mais informações sobre a clonagem de um ambiente, consulte a referência [Ambiente.Clone().](/python/api/azureml-core/azureml.core.environment.environment#clone-new-name-)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

> [!TIP]
> Para obter mais informações sobre a criação de uma configuração de [inferência,](./reference-azure-machine-learning-cli.md#inference-configuration-schema)consulte o esquema de configuração da inferência .

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

---

Após a colocação concluída, é apresentado o URI de pontuação. Para esta implantação local, `http://localhost:6789/score` será. Se se deslocar para a nuvem, pode utilizar o comando CLI do [serviço AZ ML](/cli/azure/ext/azure-cli-ml/ml/service#ext_azure_cli_ml_az_ml_service_show) para obter o URI de pontuação.

Para obter informações sobre como criar um cliente que envia pedidos de inferência para o URI pontuante, consulte [consumir um modelo implantado como serviço web](how-to-consume-web-service.md).

### <a name="setting-the-number-of-workers"></a>Definição do número de trabalhadores

Para definir o número de trabalhadores na sua implantação, desaprote a variável ambiental `WORKER_COUNT` . Dado que tem um objeto [ambiente](/python/api/azureml-core/azureml.core.environment.environment) `env` chamado, pode fazer o seguinte:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

Isto dirá à Azure ML para aumentar o número de trabalhadores que especifica.


## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia continuar a utilizar o espaço de trabalho Azure Machine Learning, mas se quiser livrar-se do serviço implantado, utilize uma das seguintes opções:


# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---

## <a name="next-steps"></a>Passos seguintes

* [Ver amostras de ponta a ponta de Tritão em Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Confira os [exemplos do cliente tritão](https://aka.ms/nvidia-client-examples)
* Leia a documentação do [Servidor de Inferência de Tritão](https://aka.ms/nvidia-triton-docs)
* [Resolver problemas de uma implementação com falhas](how-to-troubleshoot-deployment.md)
* [Implementar no Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Atualizar serviços Web](how-to-deploy-update-web-service.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)