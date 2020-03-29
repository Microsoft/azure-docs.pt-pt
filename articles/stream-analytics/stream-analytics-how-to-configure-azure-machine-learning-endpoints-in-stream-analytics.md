---
title: Use pontos finais de aprendizagem automática em Azure Stream Analytics
description: Este artigo descreve como usar funções definidas pelo utilizador de linguagem automática no Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 239955025f21d8679cbcf0bbfe68f9070f0217c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426186"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>Integração do Azure Machine Learning Studio (clássico) no Stream Analytics (Pré-visualização)
O Stream Analytics suporta funções definidas pelo utilizador que chamam a atenção para os pontos finais do Azure Machine Learning Studio (clássico). O suporte rest API para esta funcionalidade é detalhado na [biblioteca Stream Analytics REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx). Este artigo fornece informações suplementares necessárias para a implementação bem sucedida desta capacidade no Stream Analytics. Um tutorial também foi publicado e está disponível [aqui.](stream-analytics-machine-learning-integration-tutorial.md)

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Visão geral: Azure Machine Learning Studio (clássico) terminologia
O Microsoft Azure Machine Learning Studio (clássico) fornece uma ferramenta colaborativa, de drag-and-drop que pode usar para construir, testar e implementar soluções de análise preditiva nos seus dados. Esta ferramenta chama-se *Azure Machine Learning Studio (clássico)*. O estúdio é usado para interagir com os recursos de Machine Learning e facilmente construir, testar e iterar no seu design. Estes recursos e as suas definições estão abaixo.

* **Espaço de** *trabalho* : O espaço de trabalho é um recipiente que mantém todos os outros recursos de Machine Learning juntos num recipiente para gestão e controlo.
* **Experiência**: *As experiências* são criadas por cientistas de dados para utilizar conjuntos de dados e treinar um modelo de aprendizagem automática.
* **Ponto final**: *Os pontos finais* são o objeto Azure Machine Learning Studio (clássico) usado para tomar funcionalidades como entrada, aplicar um modelo de aprendizagem automática especificado e obter resultados pontuados de retorno.
* **Pontuação Webservice**: Um serviço web de *pontuação* é uma coleção de pontos finais como mencionado acima.

Cada ponto final tem apis para execução de lote e execução sincronizada. Stream Analytics usa execução sincronizada. O serviço específico é nomeado serviço de [pedido/resposta](../machine-learning/studio/consume-web-services.md) no Azure Machine Learning Studio (clássico).

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Recursos de Aprendizagem automática necessários para trabalhos de Streaming Analytics
Para efeitos de processamento de trabalho stream Analytics, um ponto final de pedido/resposta, um [apikey,](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md)e uma definição de swagger são todos necessários para uma execução bem sucedida. O Stream Analytics tem um ponto final adicional que constrói o url para o ponto final swagger, olha para a interface e devolve uma definição de UDF padrão ao utilizador.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Configure uma Corrente Analytics e Machine Learning UDF via REST API
Ao utilizar APIs REST, pode configurar o seu trabalho para ligar para funções de linguagem automática Azure. Os passos são os seguintes:

1. Criar uma tarefa do Stream Analytics
2. Definir uma entrada
3. Definir uma saída
4. Criar uma função definida pelo utilizador (UDF)
5. Escreva uma transformação stream analytics que chama a UDF
6. Iniciar a tarefa

## <a name="creating-a-udf-with-basic-properties"></a>Criação de uma UDF com propriedades básicas
Como exemplo, o seguinte código de amostra cria uma UDF escalar chamada *newudf* que se liga a um estúdio de aprendizagem automática Azure (clássico). Note que o *ponto final* (serviço URI) pode ser encontrado na página de ajuda da API para o serviço escolhido e o *apiKey* pode ser encontrado na página principal dos Serviços.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Órgão de pedido de exemplo:

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

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Call RetrieveDefaultDefini ponto final para A UDF padrão
Uma vez que o esqueleto UDF é criado, a definição completa da UDF é necessária. O ponto final do RetrieveDefaultDefinition ajuda-o a obter a definição padrão para uma função escalar que está ligada a um ponto final do Azure Machine Learning Studio (clássico). A carga útil abaixo requer que obtenha a definição padrão de UDF para uma função escalar que está ligada a um ponto final de Aprendizagem automática Azure. Não especifica o ponto final real, uma vez que já foi fornecido durante o pedido de PUT. Stream Analytics chama o ponto final fornecido no pedido se for fornecido explicitamente. Caso contrário, utiliza o originalmente referenciado. Aqui a UDF pega num único parâmetro de corda (uma frase) e devolve uma única saída de cadeia tipo que indica a etiqueta de "sentimento" para essa frase.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Órgão de pedido de exemplo:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Uma amostra de saída disto seria algo como abaixo.

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
Agora, a UDF deve ser corrigida com a resposta anterior, como mostrado abaixo.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Request Body (Saída de RetrieveDefaultDefinition):

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
Agora questione a UDF (aqui nomeada scoreTweet) para cada evento de entrada e escreva uma resposta para esse evento a uma saída.

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
