---
title: Resolução de problemas de implementação de modelo remoto
titleSuffix: Azure Machine Learning
description: Aprenda a trabalhar, resolver e resolver problemas com alguns erros comuns de implementação do Docker com o Serviço Azure Kubernetes e as Instâncias de Contentores Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: contperf-fy20q4, devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 8bec083e62bec6a0311487c1e64e780ad14f451b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518268"
---
# <a name="troubleshooting-remote-model-deployment"></a>Resolução de problemas de implementação de modelo remoto 

Aprenda a resolver problemas e a resolver, ou a contornar, erros comuns que poderá encontrar ao implementar um modelo para Azure Container Instances (ACI) e Azure Kubernetes Service (AKS) utilizando a Azure Machine Learning.

> [!NOTE]
> Se estiver a implementar um modelo para o Serviço Azure Kubernetes (AKS), aconselhamos que adinga [o Azure Monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md) para esse cluster. Isto irá ajudá-lo a compreender a saúde geral do cluster e o uso de recursos. Também poderá encontrar os seguintes recursos úteis:
>
> * [Verifique se os eventos de Saúde de Recursos impactam o seu cluster AKS](../aks/aks-resource-health.md)
> * [Diagnóstico de Serviço Azure Kubernetes](../aks/concepts-diagnostics.md)
>
> Se estiver a tentar implantar um modelo num cluster pouco saudável ou sobrecarregado, espera-se que experimente problemas. Se precisar de ajuda para resolver problemas de resolução de problemas de cluster AKS contacte o Suporte AKS.

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição do Azure**. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* [O Azure Machine Learning SDK.](/python/api/overview/azure/ml/install)
* O [Azure CLI.](/cli/azure/install-azure-cli)
* A [extensão CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Passos para implantação de Docker de modelos de aprendizagem automática

Quando se implementa um modelo para computação não local no Azure Machine Learning, acontecem as seguintes coisas:

1. O Dockerfile especificado no seu objeto Ambientes no seu InferenceConfig é enviado para a nuvem, juntamente com o conteúdo do seu diretório de origem
1. Se uma imagem previamente construída não estiver disponível no registo do seu contentor, uma nova imagem do Docker é construída na nuvem e armazenada no registo padrão do contentor do seu espaço de trabalho.
1. A imagem do Docker do seu registo de contentores é transferida para o seu alvo de computação.
1. A loja Blob padrão do seu espaço de trabalho está montada no seu alvo de computação, dando-lhe acesso a modelos registados
1. O seu servidor web é inicializado executando a função do seu script de entrada `init()`
1. Quando o seu modelo implantado recebe um pedido, a sua `run()` função lida com esse pedido

A principal diferença ao utilizar uma implantação local é que a imagem do contentor é construída na sua máquina local, razão pela qual precisa de ter o Docker instalado para uma implantação local.

Compreender estes passos de alto nível deve ajudá-lo a entender onde os erros estão a acontecer.

## <a name="get-deployment-logs"></a>Obtenha registos de implantação

O primeiro passo para depurar erros é obter os seus registos de implantação. Primeiro, siga as [instruções aqui](how-to-deploy-and-where.md#connect-to-your-workspace) para ligar ao seu espaço de trabalho.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

Para obter os registos de um serviço web implantado, faça:

```bash
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


Assumindo que tem um objeto do tipo `azureml.core.Workspace` `ws` chamado, pode fazer o seguinte:

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>Debug localmente

Se tiver problemas ao implementar um modelo para ACI ou AKS, implemente-o como um serviço web local. A utilização de um serviço Web local facilita a resolução de problemas. Para resolver problemas numa implantação local, consulte o [artigo local de resolução de problemas](./how-to-troubleshoot-deployment-local.md).

## <a name="container-cannot-be-scheduled"></a>O contentor não pode ser programado

Ao implementar um serviço num destino de computação do Azure Kubernetes Service, o Azure Machine Learning tenta agendar o serviço com a quantidade pedida de recursos. Se não houver nós disponíveis no cluster com a quantidade adequada de recursos após 5 minutos, a implantação falhará. A mensagem de falha é `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Pode resolver este erro adicionando mais nós, alterando o SKU dos seus nós ou alterando os requisitos de recursos do seu serviço. 

A mensagem de erro normalmente indica qual o recurso de que precisa mais - por exemplo, se vir uma mensagem de erro `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` indicando que o serviço requer GPUs e existem três nós no cluster que não têm GPUs disponíveis. Isto poderia ser abordado adicionando mais nós se estiver a usar um GPU SKU, mudando para um SKU ativado por GPU se não estiver ou mudar o seu ambiente para não exigir GPUs.  

## <a name="service-launch-fails"></a>Falha no lançamento do serviço

Depois de a imagem ser construída com sucesso, o sistema tenta iniciar um contentor utilizando a sua configuração de implantação. Como parte do processo de arranque do contentor, a `init()` função no seu script de pontuação é invocada pelo sistema. Se houver exceções não conseguindo na `init()` função, poderá ver o erro **do CrashLoopBackOff** na mensagem de erro.

Utilize a informação no artigo [de registo do Docker.](how-to-troubleshoot-deployment-local.md#dockerlog)

## <a name="function-fails-get_model_path"></a>Falha na função: get_model_path()

Frequentemente, `init()` na função no script de pontuação, [Model.get_model_path()](/python/api/azureml-core/azureml.core.model.model#get-model-path-model-name--version-none---workspace-none-) a função é chamada para localizar um ficheiro modelo ou uma pasta de ficheiros de modelos no recipiente. Se não for possível encontrar o ficheiro de modelo ou a pasta, a função falhará. A maneira mais fácil de depurar este erro é executar o código Python abaixo na concha do recipiente:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Este exemplo imprime o caminho local `/var/azureml-app` (relativo a) no recipiente onde o seu script de pontuação espera encontrar o ficheiro ou pasta do modelo. Em seguida, pode verificar se o ficheiro ou a pasta estão realmente onde se espera que esteja.

A definição do nível de registo para DEBUG pode fazer com que sejam registadas informações adicionais, o que pode ser útil na identificação da falha.

## <a name="function-fails-runinput_data"></a>Falha na função: execução (input_data)

Se o serviço for implementado com sucesso, mas falhar quando publicar dados no ponto final de pontuação, pode adicionar uma declaração de captura de erros na sua `run(input_data)` função para que retorne uma mensagem de erro detalhada. Por exemplo:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Nota:** As mensagens de erro de devolução da `run(input_data)` chamada devem ser feitas apenas para depurar. Por razões de segurança, não deve devolver mensagens de erro desta forma num ambiente de produção.

## <a name="http-status-code-502"></a>Código de estado de HTTP 502

Um código de estado 502 indica que o serviço lançou uma exceção ou se despenhou no `run()` método do ficheiro score.py. Utilize as informações deste artigo para depurar o ficheiro.

## <a name="http-status-code-503"></a>Código de estado de HTTP 503

As implementações do Serviço Azure Kubernetes suportam a autoscalagem, que permite adicionar réplicas para suportar carga adicional. O autoescalador foi concebido para lidar com alterações **graduais** na carga. Se receber grandes picos de pedidos por segundo, os clientes poderão receber um código de estado HTTP 503. Mesmo que o autoescalador reaja rapidamente, a AKS leva uma quantidade significativa de tempo para criar recipientes adicionais.

As decisões de escala para cima/para baixo baseiam-se na utilização das réplicas atuais do contentor. O número de réplicas que estão ocupadas (processando um pedido) divididas pelo número total de réplicas atuais é a utilização atual. Se este número `autoscale_target_utilization` exceder, mais réplicas são criadas. Se for mais baixo, as réplicas são reduzidas. As decisões de adicionar réplicas são ansiosas e rápidas (cerca de 1 segundo). As decisões de remover réplicas são conservadoras (cerca de 1 minuto). Por predefinição, a utilização do alvo de autoscalização é definida em **70%,** o que significa que o serviço pode lidar com picos em pedidos por segundo (RPS) **até 30%.**

Há duas coisas que podem ajudar a prevenir 503 códigos de estado:

> [!TIP]
> Estas duas abordagens podem ser utilizadas individualmente ou em combinação.

* Alterar o nível de utilização em que a autoscalagem cria novas réplicas. Pode ajustar o alvo de utilização definindo o `autoscale_target_utilization` valor mais baixo.

    > [!IMPORTANT]
    > Esta alteração não faz com que as réplicas sejam criadas *mais rapidamente.* Em vez disso, são criados num limiar de utilização mais baixo. Em vez de esperar até que o serviço seja utilizado em 70%, alterar o valor para 30% faz com que as réplicas sejam criadas quando ocorrem 30% de utilização.
    
    Se o serviço web já estiver a utilizar as réplicas max atuais e ainda estiver a ver 503 códigos de estado, aumente o `autoscale_max_replicas` valor para aumentar o número máximo de réplicas.

* Altere o número mínimo de réplicas. Aumentar as réplicas mínimas proporciona uma piscina maior para lidar com os picos de entrada.

    Para aumentar o número mínimo de réplicas, definido `autoscale_min_replicas` para um valor mais elevado. Pode calcular as réplicas necessárias utilizando o seguinte código, substituindo valores por valores específicos do seu projeto:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Se receber picos de pedido maiores do que as novas réplicas mínimas podem manusear, poderá receber 503s novamente. Por exemplo, à medida que o tráfego para o seu serviço aumenta, poderá ter de aumentar as réplicas mínimas.

Para obter mais informações sobre a definição `autoscale_target_utilization` `autoscale_max_replicas` , e `autoscale_min_replicas` para, consulte a referência do módulo [AksWebservice.](/python/api/azureml-core/azureml.core.webservice.akswebservice)

## <a name="http-status-code-504"></a>Código de estado de HTTP 504

Um código de estado 504 indica que o pedido está esgotado. O tempo limite de tempo padrão é de 1 minuto.

Pode aumentar o tempo limite ou tentar acelerar o serviço modificando o score.py para remover chamadas desnecessárias. Se estas ações não corrigirem o problema, utilize as informações deste artigo para depurar o ficheiro score.py. O código pode estar num estado não responsivo ou num ciclo infinito.

## <a name="other-error-messages"></a>Outras mensagens de erro

Tome estas ações para os seguintes erros:

|Erro  | Resolução  |
|---------|---------|
|Falha na construção de imagem ao implementar o serviço web     |  Adicione "pynacl==1.2.1" como uma dependência de pip ao ficheiro Conda para configuração de imagem       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Altere o SKU para VMs utilizados na sua implantação para um que tenha mais memória. |
|Falha da FPGA     |  Não poderá implementar modelos em FPGAs até que tenha solicitado e aprovado para a quota FPGA. Para solicitar o acesso, preencha o formulário de pedido de quota: https://aka.ms/aml-real-time-ai       |

## <a name="advanced-debugging"></a>Depuragem avançada

É possível que tenha de depurar interativamente o código Python contido na implementação de modelo. Por exemplo, se o script de entrada estiver a falhar e a razão não puder ser determinada por registo adicional. Ao utilizar o Código de Estúdio Visual e o depuratório, pode anexar-se ao código que está a decorrer dentro do contentor Docker.

Para mais informações, visite a [depuragem interativa no guia do Código VS.](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments)

## <a name="model-deployment-user-forum"></a>[Fórum de utilizador de implementação de modelos](/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a implementação:

* [Como implantar e onde](how-to-deploy-and-where.md)
* [Tutorial: Modelos de implantação de & de comboios](tutorial-train-models-with-aml.md)
* [Como executar e depurar experiências localmente](./how-to-debug-visual-studio-code.md)