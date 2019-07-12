---
title: Utilizar pontos de extremidade do Machine Learning no Azure Stream Analytics
description: Este artigo descreve como utilizar funções definidas pelo utilizador de linguagem de máquina no Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 650f8952e58046082768007295208f52113b5f81
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620893"
---
# <a name="azure-machine-learning-studio-integration-in-stream-analytics-preview"></a>Integração do Azure Machine Learning Studio no Stream Analytics (pré-visualização)
Stream Analytics suporta funções definidas pelo utilizador que chamam pontos finais do Azure Machine Learning Studio. Suporte da REST API para esta funcionalidade está detalhado na [biblioteca de API de REST do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx). Este artigo fornece informações complementares necessárias para a implementação com êxito desta capacidade no Stream Analytics. Um tutorial também foi publicado e está disponível [aqui](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-studio-terminology"></a>Descrição geral: Terminologia do Azure Machine Learning Studio
Microsoft Azure Machine Learning Studio fornece uma ferramenta de colaboração, de arrastar e largar que pode utilizar para criar, testar e implementar soluções de Análise Preditiva nos seus dados. Essa ferramenta é chamada de *Azure Machine Learning Studio*. O studio é utilizado para interagir com os recursos de Aprendizado de máquina e facilmente criar, testar e iterar o design. Esses recursos e as respetivas definições são abaixo.

* **Área de trabalho**: O *área de trabalho* é um contentor que retém todos os outros recursos de aprendizagem automática em conjunto num contentor para o gerenciamento e controle.
* **Experimentação**: *Experimentações* são criados por cientistas de dados para utilizar conjuntos de dados e treinar um modelo de aprendizagem automática.
* **Ponto final**: *Pontos de extremidade* são o objeto do Azure Machine Learning Studio usado para necessitam de funcionalidades como entrada, aplicar um modelo de aprendizagem de máquina especificada e devolve dados com a pontuação.
* **Serviço Web de classificação**: R *classificação webservice* é uma coleção de pontos de extremidade, conforme mencionado acima.

Cada ponto de extremidade tem apis para execução de lote e execução síncrona. Stream Analytics utiliza execução síncrona. O serviço específico é denominado um [serviço de solicitação/resposta](../machine-learning/studio/consume-web-services.md) no Azure Machine Learning studio.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Aprendizagem automática recursos necessários para tarefas do Stream Analytics
Para efeitos do Stream Analytics da tarefa de processamento, um ponto de extremidade de solicitação/resposta, um [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md), e uma definição de swagger são necessária para a execução bem-sucedida. Stream Analytics tem um ponto de final adicional que constrói a url para o ponto final de swagger, procura a interface e retorna uma definição de UDF padrão para o utilizador.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Configurar um Stream Analytics e o Machine Learning UDF através da REST API
Ao utilizar REST APIs só pode configurar o seu trabalho para chamar funções de linguagem de máquina do Azure. Os passos são os seguintes:

1. Criar uma tarefa do Stream Analytics
2. Definir uma entrada
3. Definir uma saída
4. Criar uma função definida pelo utilizador (UDF)
5. Escrever uma transformação de Stream Analytics que chama a UDF
6. Iniciar a tarefa

## <a name="creating-a-udf-with-basic-properties"></a>Criar uma UDF com propriedades básicas
Por exemplo, o código de exemplo seguinte cria uma UDF escalar chamado *newudf* que liga a um ponto de final do Azure Machine Learning Studio. Tenha em atenção que o *ponto final* (URI de serviço) pode ser encontrada na página de ajuda da API para o serviço escolhido e o *apiKey* podem ser encontradas na página principal dos serviços.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Corpo do pedido de exemplo:

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

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Chamar o ponto final de RetrieveDefaultDefinition para predefinição UDF
Assim que o esqueleto UDF for criado é necessária a definição completa do UDF. O ponto de extremidade RetrieveDefaultDefinition ajuda a obter a definição padrão para uma função escalar, que está vinculada a um ponto de final do Azure Machine Learning Studio. O payload abaixo exige que obter a definição de UDF padrão para uma função escalar, que está vinculada a um ponto de final do Azure Machine Learning. Ele não especificar o ponto final real, como já foi fornecido durante o pedido PUT. Stream Analytics chama o ponto final fornecido no pedido, se fornecida explicitamente. Caso contrário, ele usa um originalmente referenciado. Aqui, a leva UDF uma única cadeia de parâmetro (uma frase) e devolve o resultado de um único do tipo de cadeia que indica que a etiqueta "sentiment" para a essa frase.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Corpo do pedido de exemplo:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Um exemplo de saída isso seria procure algo, conforme mostrado abaixo.

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

## <a name="patch-udf-with-the-response"></a>Patch UDF com a resposta
Agora a UDF deve ser corrigida com a resposta anterior, conforme mostrado abaixo.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Corpo do pedido (saída de RetrieveDefaultDefinition):

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

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementar a transformação do Stream Analytics para chamar a UDF
Agora, consultar a UDF (aqui com o nome scoreTweet) para todos os eventos de entrada e escrever uma resposta para esse evento para uma saída.

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

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
