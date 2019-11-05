---
title: Usar pontos de extremidade de Machine Learning no Azure Stream Analytics
description: Este artigo descreve como usar funções definidas pelo usuário de linguagem de computador no Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 1adb7d58246ea37fd8322cb6fc6ffd53c5f19efb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467828"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>Integração Azure Machine Learning Studio (clássica) no Stream Analytics (versão prévia)
O Stream Analytics dá suporte a funções definidas pelo usuário que chamam pontos de extremidade de Azure Machine Learning Studio (clássico). O suporte à API REST para esse recurso é detalhado na [biblioteca da API REST do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx). Este artigo fornece informações complementares necessárias para a implementação bem-sucedida desse recurso no Stream Analytics. Um tutorial também foi publicado e está disponível [aqui](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Visão geral: terminologia Azure Machine Learning Studio (clássica)
Microsoft Azure Machine Learning Studio (clássico) fornece uma ferramenta colaborativa de colaboração, arrastar e soltar, que você pode usar para criar, testar e implantar soluções de análise preditiva em seus dados. Essa ferramenta é chamada de *Azure Machine Learning Studio (clássico)* . O estúdio é usado para interagir com os recursos de Machine Learning e facilmente compilar, testar e iterar em seu design. Esses recursos e suas definições estão abaixo.

* **Espaço de trabalho**: o *espaço de trabalho* é um contêiner que mantém todos os outros Machine Learning recursos juntos em um contêiner para gerenciamento e controle.
* **Teste**: os *experimentos* são criados por cientistas de dados para utilizar conjuntos e treinar um modelo de aprendizado de máquina.
* **Endpoint**: os *pontos* de extremidade são o objeto Azure Machine Learning Studio (clássico) usado para executar recursos como entrada, aplicar um modelo de Machine Learning especificado e retornar a saída pontuada.
* **WebService de Pontuação**: um *WebService de Pontuação* é uma coleção de pontos de extremidade, conforme mencionado acima.

Cada ponto de extremidade tem APIs para execução em lotes e execução síncrona. Stream Analytics usa a execução síncrona. O serviço específico é nomeado como um [serviço de solicitação/resposta](../machine-learning/studio/consume-web-services.md) no Azure Machine Learning Studio (clássico).

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Machine Learning recursos necessários para trabalhos de Stream Analytics
Para fins de Stream Analytics processamento de trabalho, um ponto de extremidade de solicitação/resposta, um [apiKey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md)e uma definição de Swagger são todos necessários para a execução bem-sucedida. Stream Analytics tem um ponto de extremidade adicional que constrói a URL para o ponto de extremidade do Swagger, pesquisa a interface e retorna uma definição de UDF padrão para o usuário.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Configurar um Stream Analytics e Machine Learning UDF via API REST
Usando as APIs REST, você pode configurar seu trabalho para chamar funções de linguagem de máquina do Azure. As etapas são as seguintes:

1. Criar uma tarefa do Stream Analytics
2. Definir uma entrada
3. Definir uma saída
4. Criar uma UDF (função definida pelo usuário)
5. Gravar uma transformação Stream Analytics que chama o UDF
6. Iniciar a tarefa

## <a name="creating-a-udf-with-basic-properties"></a>Criando um UDF com propriedades básicas
Por exemplo, o código de exemplo a seguir cria um UDF escalar denominado *newudf* que é associado a um ponto de extremidade Azure Machine Learning Studio (clássico). Observe que o *ponto de extremidade* (URI de serviço) pode ser encontrado na página de ajuda da API para o serviço escolhido e o *apiKey* pode ser encontrado na página principal de serviços.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Corpo da solicitação de exemplo:

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Chamar o ponto de extremidade RetrieveDefaultDefinition para UDF padrão
Depois que o esqueleto do UDF for criado, a definição completa do UDF será necessária. O ponto de extremidade RetrieveDefaultDefinition ajuda a obter a definição padrão para uma função escalar associada a um ponto de extremidade Azure Machine Learning Studio (clássico). A carga abaixo exige que você obtenha a definição de UDF padrão para uma função escalar associada a um ponto de extremidade de Azure Machine Learning. Ele não especifica o ponto de extremidade real, pois já foi fornecido durante a solicitação PUT. Stream Analytics chama o ponto de extremidade fornecido na solicitação se ele for fornecido explicitamente. Caso contrário, ele usa o que foi referenciado originalmente. Aqui, o UDF usa um único parâmetro de cadeia de caracteres (uma sentença) e retorna uma única saída do tipo cadeia de caracteres que indica o rótulo "sentimentos" para essa frase.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Corpo da solicitação de exemplo:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Uma saída de exemplo desse aspecto seria semelhante a esta.

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>Correção UDF com a resposta
Agora o UDF deve ser corrigido com a resposta anterior, conforme mostrado abaixo.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Corpo da solicitação (saída de RetrieveDefaultDefinition):

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementar Stream Analytics transformação para chamar o UDF
Agora, consulte o UDF (aqui chamado scoreTweet) para cada evento de entrada e grave uma resposta para esse evento em uma saída.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Obter ajuda
Para mais assistência, tente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
