---
title: Modelo de alto desempenho servindo com Tritão (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a implementar um modelo com o Servidor de Inferência de Tritão em Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 09/23/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 3a3600c4065d331ca1cfc129cd55dd56add21424
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92428341"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Serviço de alto desempenho com Servidor de Inferência de Tritão (Pré-visualização) 

Saiba como utilizar o [NVIDIA Triton Inference Server](https://developer.nvidia.com/nvidia-triton-inference-server) para melhorar o desempenho do serviço web utilizado para a inferência do modelo.

Uma das formas de implementar um modelo de inferência é como um serviço web. Por exemplo, uma implantação para o Serviço Azure Kubernetes ou instâncias de contentores Azure. Por predefinição, o Azure Machine Learning utiliza uma estrutura web de *propósito único* para implementações de serviço web.

Tritão é um quadro que está *otimizado para a inferência.* Proporciona uma melhor utilização das GPUs e uma inferência mais rentável. No lado do servidor, em lote solicita solicitações recebidas e envia estes lotes para inferência. O loteamento melhor utiliza os recursos da GPU, e é uma parte fundamental do desempenho de Triton.

> [!IMPORTANT]
> A utilização de Tritão para implantação a partir de Azure Machine Learning está atualmente em __pré-visualização__. A funcionalidade de pré-visualização pode não ser coberta pelo suporte ao cliente. Para obter mais informações, consulte os [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> Os fragmentos de código neste documento são para fins ilustrativos e podem não apresentar uma solução completa. Para obter o código de exemplo de trabalho, consulte as [amostras de ponta a ponta de Tritão em Azure Machine Learning](https://github.com/Azure/azureml-examples/tree/main/tutorials).

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição do Azure**. Se não tiver uma, experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* Familiaridade com [como e onde implementar um modelo](how-to-deploy-and-where.md) com Azure Machine Learning.
* O [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py) **ou** o [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) e [extensão de aprendizagem automática.](reference-azure-machine-learning-cli.md)
* Uma instalação de trabalho do Docker para testes locais. Para obter informações sobre a instalação e validação do Docker, consulte [a Orientação e a configuração](https://docs.docker.com/get-started/) na documentação do estivador.

## <a name="architectural-overview"></a>Descrição geral da arquitetura

Antes de tentar usar Tritão para o seu próprio modelo, é importante entender como funciona com a Azure Machine Learning e como se compara a uma implementação padrão.

**Implementação padrão sem Tritão**

* Vários trabalhadores [de Gunicorn](https://gunicorn.org/) começam a lidar simultaneamente com pedidos de entrada.
* Estes trabalhadores lidam com o pré-processamento, chamando o modelo e após o processamento. 
* Os pedidos de inferência utilizam o __URI de pontuação__. Por exemplo, `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Normal, não tritão, diagrama de arquitetura de implantação":::

**Implementação de configuração de inferência com Tritão**

* Vários trabalhadores [de Gunicorn](https://gunicorn.org/) começam a lidar simultaneamente com pedidos de entrada.
* Os pedidos são reencaminhados para o **servidor Triton.** 
* Tritão processa pedidos em lotes para maximizar a utilização da GPU.
* O cliente usa o __URI de pontuação__ para fazer pedidos. Por exemplo, `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inferenceconfig-deploy.png" alt-text="Normal, não tritão, diagrama de arquitetura de implantação":::

O fluxo de trabalho para usar Tritão para a sua implementação de modelo é:

1. Verifique se Tritão pode servir o seu modelo.
1. Verifique se pode enviar pedidos para o seu modelo tritão.
1. Incorpore o seu código específico de Tritão na sua implementação AML.

## <a name="optional-define-a-model-config-file"></a>(Opcional) Definir um ficheiro config modelo

O ficheiro de configuração do modelo diz a Tritão quantas entradas esperam e de que dimensões serão essas entradas. Para obter mais informações sobre a criação do ficheiro de configuração, consulte a [configuração](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html) do Modelo na documentação NVIDIA.

> [!TIP]
> Utilizamos a `--strict-model-config=false` opção ao iniciar o Servidor de Inferência de Tritão, o que significa que não precisa de fornecer um `config.pbtxt` ficheiro para os modelos ONNX ou TensorFlow.
> 
> Para obter mais informações sobre esta opção, consulte [a configuração do modelo Gerado](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html#generated-model-configuration) na documentação NVIDIA.

## <a name="directory-structure"></a>Estrutura do diretório

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
> Esta estrutura de diretório é um Repositório de Modelos de Tritão e é necessária para que os seus modelos trabalhem com Tritão. Para mais informações, consulte [os Repositórios de Modelos de Tritão](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_repository.html) na documentação da NVIDIA.

## <a name="test-with-triton-and-docker"></a>Teste com Tritão e Docker

Para testar o seu modelo para ter a certeza de que funciona com o Tritão, podes usar o Docker. Os seguintes comandos puxam o recipiente Tritão para o computador local e, em seguida, iniciam o Servidor Tritão:

1. Para puxar a imagem do servidor Tritão para o seu computador local, utilize o seguinte comando:

    ```bash
    docker pull nvcr.io/nvidia/tritonserver:20.09-py3
    ```

1. Para iniciar o servidor Tritão, utilize o seguinte comando. `<path-to-models/triton>`Substitua-o pelo caminho para o repositório do modelo Tritão que contém os seus modelos:

    ```bash
    docker run --rm -ti -v<path-to-models/triton>:/models nvcr.io/nvidia/tritonserver:20.09-py3 tritonserver --model-repository=/models --strict-model-config=false
    ```

    > [!IMPORTANT]
    > Se estiver a utilizar o Windows, poderá ser solicitado que permita ligações de rede a este processo na primeira vez que executar o comando. Em caso afirmativo, selecione para permitir o acesso.

    Uma vez iniciado, informações semelhantes ao texto a seguir são registadas na linha de comando:

    ```bash
    I0923 19:21:30.582866 1 http_server.cc:2705] Started HTTPService at 0.0.0.0:8000
    I0923 19:21:30.626081 1 http_server.cc:2724] Started Metrics Service at 0.0.0.0:8002
    ```

    A primeira linha indica o endereço do serviço web. Neste caso, `0.0.0.0:8000` que é o mesmo que `localhost:8000` .

1. Utilize uma utilidade como o caracol para aceder ao ponto final da saúde.

    ```bash
    curl -L -v -i localhost:8000/v2/health/ready
    ```

    Este comando devolve informações semelhantes às seguintes. Note o `200 OK` seguinte; este estado significa que o servidor web está em execução.

    ```bash
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

Além de um exame básico de saúde, pode criar um cliente para enviar dados para Triton para inferência. Para obter mais informações sobre a criação de um cliente, consulte os [exemplos](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/client_example.html) do cliente na documentação da NVIDIA. Há também [amostras de Python no Tritão GitHub.](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples)

Para obter mais informações sobre a execução de Tritão usando Docker, consulte [Running Triton num sistema com uma GPU](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-with-a-gpu) e [Running Triton num sistema sem GPU](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-without-a-gpu).

## <a name="register-your-model"></a>Registe o seu modelo

Agora que verificou que o seu modelo trabalha com Tritão, registe-o com a Azure Machine Learning. O registo de modelos armazena os seus ficheiros de modelos no espaço de trabalho Azure Machine Learning e é utilizado ao ser implantado com o Python SDK e o Azure CLI.

Os exemplos que se seguem demonstram como registar o ou os modelos):

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.model import Model

model = Model.register(
    model_path=os.path.join("..", "triton"),
    model_name="bidaf_onnx",
    tags={'area': "Natural language processing", 'type': "Question answering"},
    description="Question answering model from ONNX model zoo",
    workspace=ws
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az ml model register --model-path='triton' \
--name='bidaf_onnx' \
--workspace-name='<my_workspace>'
```
---

<a id="processing"></a>

## <a name="add-pre-and-post-processing"></a>Adicionar pré e pós-processamento

Depois de verificar se o serviço web está a funcionar, pode adicionar código pré e pós-processamento definindo um script de _entrada_. Este ficheiro tem o `score.py` nome. Para obter mais informações sobre scripts de entrada, consulte [definir um script de entrada](how-to-deploy-and-where.md#define-an-entry-script).

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

## <a name="redeploy-with-an-inference-configuration"></a>Reimplantação com uma configuração de inferência

Uma configuração de inferência permite-lhe usar um script de entrada, bem como o processo de implementação de aprendizagem automática Azure usando o Python SDK ou Azure CLI.

> [!IMPORTANT]
> Deve especificar o `AzureML-Triton` [ambiente curado.](./resource-curated-environments.md)
>
> O código Python cita clones `AzureML-Triton` para outro ambiente chamado `My-Triton` . O código Azure CLI também utiliza este ambiente. Para obter mais informações sobre a clonagem de um ambiente, consulte a referência [Ambiente.Clone().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#clone-new-name-)

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!TIP]
> Para obter mais informações sobre a criação de uma configuração de [inferência,](./reference-azure-machine-learning-cli.md#inference-configuration-schema)consulte o esquema de configuração da inferência .

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

---

Após a colocação concluída, é apresentado o URI de pontuação. Para esta implantação local, `http://localhost:6789/score` será. Se se deslocar para a nuvem, pode utilizar o comando CLI do [serviço AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext_azure_cli_ml_az_ml_service_show) para obter o URI de pontuação.

Para obter informações sobre como criar um cliente que envia pedidos de inferência para o URI pontuante, consulte [consumir um modelo implantado como serviço web](how-to-consume-web-service.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia continuar a utilizar o espaço de trabalho Azure Machine Learning, mas se quiser livrar-se do serviço implantado, utilize uma das seguintes opções:

# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az ml service delete -n triton-densenet-onnx
```

---

## <a name="next-steps"></a>Passos seguintes

* [Ver amostras de ponta a ponta de Tritão em Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Confira os [exemplos do cliente tritão](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples)
* Leia a documentação do [Servidor de Inferência de Tritão](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/index.html)
* [Resolução de problemas de uma implantação falhada](how-to-troubleshoot-deployment.md)
* [Implementar no Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Atualizar serviços Web](how-to-deploy-update-web-service.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
