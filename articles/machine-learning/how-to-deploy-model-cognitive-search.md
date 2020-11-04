---
title: Implementar um modelo para utilizar com o Cognitive Search
titleSuffix: Azure Machine Learning
description: Aprenda a usar a Azure Machine Learning para implementar um modelo para uso com Pesquisa Cognitiva. O modelo é usado como uma habilidade personalizada para enriquecer a experiência de pesquisa.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.reviewer: larryfr
ms.date: 06/11/2020
ms.custom: deploy
ms.openlocfilehash: e1eebf88b72c87ce9db02760c5c44a0aa25c57cc
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305920"
---
# <a name="deploy-a-model-for-use-with-cognitive-search"></a>Implementar um modelo para utilizar com o Cognitive Search


Este artigo ensina-o a usar a Azure Machine Learning para implementar um modelo para uso com [Azure Cognitive Search](../search/search-what-is-azure-search.md).

A Cognitive Search executa o processamento de conteúdos sobre conteúdo heterogéneo, para torná-lo questionável por humanos ou aplicações. Este processo pode ser melhorado utilizando um modelo implantado a partir do Azure Machine Learning.

O Azure Machine Learning pode implementar um modelo treinado como serviço web. O serviço web é então incorporado numa _habilidade_ de Pesquisa Cognitiva, que se torna parte do pipeline de processamento.

> [!IMPORTANT]
> A informação neste artigo é específica para a implementação do modelo. Fornece informações sobre as configurações de implementação suportadas que permitem que o modelo seja utilizado pela Pesquisa Cognitiva.
>
> Para obter informações sobre como configurar a Pesquisa Cognitiva para utilizar o modelo implantado, consulte a Build e implemente uma habilidade personalizada com o tutorial [de Aprendizagem automática Azure.](../search/cognitive-search-tutorial-aml-custom-skill.md)
>
> Para a amostra em que o tutorial se baseia, [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) veja.

Ao implementar um modelo para utilização com a Azure Cognitive Search, a implementação deve satisfazer os seguintes requisitos:

* Utilize o Serviço Azure Kubernetes para hospedar o modelo para inferência.
* Ativar a segurança da camada de transporte (TLS) para o Serviço Azure Kubernetes. O TLS é utilizado para garantir comunicações HTTPS entre a Pesquisa Cognitiva e o modelo implantado.
* O script de entrada deve utilizar o `inference_schema` pacote para gerar um esquema OpenAPI (Swagger) para o serviço.
* O script de entrada também deve aceitar os dados JSON como entrada, e gerar JSON como saída.


## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

* Um ambiente de desenvolvimento Python com o Azure Machine Learning SDK instalado. Para mais informações, consulte [a Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).  

* Um modelo registado. Se não tiver um modelo, utilize o caderno de exemplo em [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

* Uma compreensão geral de [como e onde implementar modelos.](how-to-deploy-and-where.md)

## <a name="connect-to-your-workspace"></a>Ligar à sua área de trabalho

Um espaço de trabalho Azure Machine Learning proporciona um lugar centralizado para trabalhar com todos os artefactos que cria quando se usa Azure Machine Learning. O espaço de trabalho mantém uma história de todas as corridas de treino, incluindo registos, métricas, saídas e uma foto dos seus scripts.

Para ligar a um espaço de trabalho existente, utilize o seguinte código:

> [!IMPORTANT]
> Este código de corte espera que a configuração do espaço de trabalho seja guardada no diretório atual ou no seu progenitor. Para obter mais informações, consulte [Criar e gerir espaços de trabalho de Aprendizagem automática Azure.](how-to-manage-workspace.md) Para obter mais informações sobre a poupança da configuração para arquivar, consulte [Criar um ficheiro de configuração do espaço de trabalho](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

try:
    # Load the workspace configuration from local cached inffo
    ws = Workspace.from_config()
    print(ws.name, ws.location, ws.resource_group, ws.location, sep='\t')
    print('Library configuration succeeded')
except:
    print('Workspace not found')
```

## <a name="create-a-kubernetes-cluster"></a>Criar um cluster do Kubernetes

**Estimativa de tempo** : Aproximadamente 20 minutos.

Um cluster Kubernetes é um conjunto de casos de máquinas virtuais (chamados nós) que são usados para executar aplicações contentorizadas.

Quando implementa um modelo da Azure Machine Learning para o Serviço Azure Kubernetes, o modelo e todos os ativos necessários para o hospedar como um serviço web são embalados num recipiente Docker. Este recipiente é então colocado no aglomerado.

O seguinte código demonstra como criar um novo cluster Azure Kubernetes Service (AKS) para o seu espaço de trabalho:

> [!TIP]
> Também pode anexar um Serviço Azure Kubernetes existente ao seu espaço de trabalho Azure Machine Learning. Para mais informações, consulte [Como implementar modelos no Serviço Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

> [!IMPORTANT]
> Note que o código utiliza o `enable_ssl()` método para permitir a segurança da camada de transporte (TLS) para o cluster. Isto é necessário quando planeia utilizar o modelo implantado dos Serviços Cognitivos.

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Create or attach to an AKS inferencing cluster

# Create the provisioning configuration with defaults
prov_config = AksCompute.provisioning_configuration()

# Enable TLS (sometimes called SSL) communications
# Leaf domain label generates a name using the formula
#  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
#  where "######" is a random series of characters
prov_config.enable_ssl(leaf_domain_label = "contoso")

cluster_name = 'amlskills'
# Try to use an existing compute target by that name.
# If one doesn't exist, create one.
try:
    
    aks_target = ComputeTarget(ws, cluster_name)
    print("Attaching to existing cluster")
except Exception as e:
    print("Creating new cluster")
    aks_target = ComputeTarget.create(workspace = ws, 
                                  name = cluster_name, 
                                  provisioning_configuration = prov_config)
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> O Azure cobrar-te-á enquanto o aglomerado AKS existir. Certifique-se de eliminar o seu cluster AKS quando terminar.

Para obter mais informações sobre a utilização de AKS com Azure Machine Learning, consulte [Como implementar no Serviço Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Escreva o roteiro de entrada

O script de entrada recebe dados submetidos ao serviço web, passa-os para o modelo e devolve os resultados da pontuação. O seguinte script carrega o modelo no arranque e, em seguida, usa o modelo para marcar dados. Este ficheiro às vezes é `score.py` chamado.

> [!TIP]
> O script de entrada é específico do seu modelo. Por exemplo, o script deve conhecer a estrutura a utilizar com o seu modelo, formatos de dados, etc.

> [!IMPORTANT]
> Quando planeia utilizar o modelo implantado a partir dos Serviços Cognitivos Azure, deve utilizar o `inference_schema` pacote para permitir a geração de esquemas para a implantação. Este pacote fornece decoradores que lhe permitem definir o formato de dados de entrada e saída para o serviço web que executa inferência usando o modelo.

```python
from azureml.core.model import Model
from nlp_architect.models.absa.inference.inference import SentimentInference
from spacy.cli.download import download as spacy_download
import traceback
import json
# Inference schema for schema discovery
from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType

def init():
    """
    Set up the ABSA model for Inference  
    """
    global SentInference
    spacy_download('en')
    aspect_lex = Model.get_model_path('hotel_aspect_lex')
    opinion_lex = Model.get_model_path('hotel_opinion_lex') 
    SentInference = SentimentInference(aspect_lex, opinion_lex)

# Use inference schema decorators and sample input/output to
# build the OpenAPI (Swagger) schema for the deployment
standard_sample_input = {'text': 'a sample input record containing some text' }
standard_sample_output = {"sentiment": {"sentence": "This place makes false booking prices, when you get there, they say they do not have the reservation for that day.", 
                                        "terms": [{"text": "hotels", "type": "AS", "polarity": "POS", "score": 1.0, "start": 300, "len": 6}, 
                                                  {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 295, "len": 4}]}}
@input_schema('raw_data', StandardPythonParameterType(standard_sample_input))
@output_schema(StandardPythonParameterType(standard_sample_output))    
def run(raw_data):
    try:
        # Get the value of the 'text' field from the JSON input and perform inference
        input_txt = raw_data["text"]
        doc = SentInference.run(doc=input_txt)
        if doc is None:
            return None
        sentences = doc._sentences
        result = {"sentence": doc._doc_text}
        terms = []
        for sentence in sentences:
            for event in sentence._events:
                for x in event:
                    term = {"text": x._text, "type":x._type.value, "polarity": x._polarity.value, "score": x._score,"start": x._start,"len": x._len }
                    terms.append(term)
        result["terms"] = terms
        print("Success!")
        # Return the results to the client as a JSON document
        return {"sentiment": result}
    except Exception as e:
        result = str(e)
        # return error message back to the client
        print("Failure!")
        print(traceback.format_exc())
        return json.dumps({"error": result, "tb": traceback.format_exc()})
```

Para obter mais informações sobre scripts de entrada, consulte [como e onde implementar](how-to-deploy-and-where.md).

## <a name="define-the-software-environment"></a>Definir o ambiente de software

A classe ambiente é usada para definir as dependências python para o serviço. Inclui dependências exigidas tanto pelo modelo como pelo script de entrada. Neste exemplo, instala pacotes a partir do índice pypi regular, bem como de um repo GitHub. 

```python
from azureml.core.conda_dependencies import CondaDependencies 
from azureml.core import Environment

conda = None
pip = ["azureml-defaults", "azureml-monitoring", 
       "git+https://github.com/NervanaSystems/nlp-architect.git@absa", 'nlp-architect', 'inference-schema',
       "spacy==2.0.18"]

conda_deps = CondaDependencies.create(conda_packages=None, pip_packages=pip)

myenv = Environment(name='myenv')
myenv.python.conda_dependencies = conda_deps
```

Para obter mais informações sobre ambientes, consulte [Criar e gerir ambientes de formação e implantação.](how-to-use-environments.md)

## <a name="define-the-deployment-configuration"></a>Definir a configuração de implementação

A configuração de implementação define o ambiente de hospedagem do Serviço Azure Kubernetes utilizado para executar o serviço web.

> [!TIP]
> Se não tiver a certeza sobre as necessidades de memória, CPU ou GPU da sua implantação, pode usar perfis para aprender estes. Para mais informações, consulte [como e onde implementar um modelo.](how-to-deploy-and-where.md)

```python
from azureml.core.model import Model
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage
from azureml.core.webservice import AksWebservice, Webservice

# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                       autoscale_min_replicas=1, 
                                                       autoscale_max_replicas=3, 
                                                       autoscale_refresh_seconds=10, 
                                                       autoscale_target_utilization=70,
                                                       auth_enabled=True, 
                                                       cpu_cores=1, memory_gb=2, 
                                                       scoring_timeout_ms=5000, 
                                                       replica_max_concurrent_requests=2, 
                                                       max_request_wait_time=5000)
```

Para mais informações, consulte a documentação de referência para [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definir a configuração da inferência

A configuração da inferência aponta para o script de entrada e para o objeto ambiente:

```python
from azureml.core.model import InferenceConfig
inf_config = InferenceConfig(entry_script='score.py', environment=myenv)
```

Para mais informações, consulte a documentação de referência para [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py).

## <a name="deploy-the-model"></a>Implementar o modelo

Implemente o modelo no seu cluster AKS e aguarde que crie o seu serviço. Neste exemplo, dois modelos registados são carregados a partir do registo e implantados para a AKS. Após a implementação, o `score.py` ficheiro na implementação carrega estes modelos e utiliza-os para executar inferência.

```python
from azureml.core.webservice import AksWebservice, Webservice

c_aspect_lex = Model(ws, 'hotel_aspect_lex')
c_opinion_lex = Model(ws, 'hotel_opinion_lex') 
service_name = "hotel-absa-v2"

aks_service = Model.deploy(workspace=ws,
                           name=service_name,
                           models=[c_aspect_lex, c_opinion_lex],
                           inference_config=inf_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           overwrite=True)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

Para mais informações, consulte a documentação de referência [do Modelo.](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py)

## <a name="issue-a-sample-query-to-your-service"></a>Emite uma consulta de amostra ao seu serviço

O exemplo a seguir utiliza as informações de implantação armazenadas na `aks_service` variável pela secção de código anterior. Utiliza esta variável para recuperar o URL de pontuação e o símbolo de autenticação necessário para comunicar com o serviço:

```python
import requests
import json

primary, secondary = aks_service.get_keys()

# Test data
input_data = '{"raw_data": {"text": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend."}}'

# Since authentication was enabled for the deployment, set the authorization header.
headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ primary)} 

# Send the request and display the results
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)
print(resp.text)
```

O resultado devolvido do serviço é semelhante ao seguinte JSON:

```json
{"sentiment": {"sentence": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend.", "terms": [{"text": "place", "type": "AS", "polarity": "POS", "score": 1.0, "start": 15, "len": 5}, {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 10, "len": 4}]}}
```

## <a name="connect-to-cognitive-search"></a>Conecte-se à Pesquisa Cognitiva

Para obter informações sobre a utilização deste modelo a partir da Pesquisa Cognitiva, consulte a Build e implemente uma habilidade personalizada com o tutorial [de Aprendizagem automática Azure.](../search/cognitive-search-tutorial-aml-custom-skill.md)

## <a name="clean-up-the-resources"></a>Limpar os recursos

Se criou o cluster AKS especificamente para este exemplo, elimine os seus recursos depois de terminar de testá-lo com a Pesquisa Cognitiva.

> [!IMPORTANT]
> Azure conta-te com base na duração do cluster AKS. Certifique-se de limpá-lo depois de terminar.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Passos seguintes

* [Construa e implemente uma habilidade personalizada com a Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md)