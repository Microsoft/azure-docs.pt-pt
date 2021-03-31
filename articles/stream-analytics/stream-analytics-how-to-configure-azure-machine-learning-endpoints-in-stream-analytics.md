---
title: Use Azure Machine Learning Studio (clássico) pontos finais em Azure Stream Analytics
description: Este artigo descreve como utilizar funções definidas pelo utilizador do Machine Language no Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/11/2019
ms.openlocfilehash: 1dc85cb10a9e4300c57ad03900d8c8924988c6d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588125"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics"></a>Azure Machine Learning Studio (clássico) integração no Stream Analytics
O Stream Analytics suporta funções definidas pelo utilizador que chamam ao Azure Machine Learning Studio (clássico) pontos finais. O suporte da API REST para esta funcionalidade é detalhado na [biblioteca API stream Analytics REST](/rest/api/streamanalytics/). Este artigo fornece informações suplementares necessárias para uma implementação bem sucedida desta capacidade no Stream Analytics. Um tutorial também foi publicado e está disponível [aqui.](stream-analytics-machine-learning-integration-tutorial.md)

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Visão geral: Azure Machine Learning Studio (clássico) terminologia
O Microsoft Azure Machine Learning Studio (clássico) fornece uma ferramenta colaborativa, de arrastar e largar que pode usar para construir, testar e implementar soluções de análise preditivas nos seus dados. Esta ferramenta chama-se *Azure Machine Learning Studio (clássico)*. O estúdio (clássico) é usado para interagir com os recursos de aprendizagem automática e facilmente construir, testar e iterar no seu design. Estes recursos e as suas definições estão abaixo.

* **Espaço de trabalho**: O *espaço de trabalho* é um recipiente que mantém todos os outros recursos de aprendizagem automática juntos num recipiente para gestão e controlo.
* **Experiência**: *As experiências* são criadas por cientistas de dados para utilizar conjuntos de dados e treinar um modelo de aprendizagem automática.
* **Ponto final**: *Os pontos finais* são o objeto Studio (clássico) usado para tirar funcionalidades como entrada, aplicar um modelo de machine learning especificado e obter resultados pontuados de retorno.
* **Pontuação Webservice**: Um *webservice de pontuação* é uma coleção de pontos finais como mencionado acima.

Cada ponto final tem apis para execução de lote e execução sincronizada. Stream Analytics usa execução sincronizada. O serviço específico é nomeado um [Serviço de Pedido/Resposta](../machine-learning/classic/consume-web-services.md) no Azure Machine Learning Studio (clássico).

## <a name="studio-classic-resources-needed-for-stream-analytics-jobs"></a>Recursos de estúdio (clássicos) necessários para trabalhos stream Analytics
Para efeitos de processamento de trabalho stream Analytics, um ponto final de Pedido/Resposta, [um apikey](../machine-learning/classic/consume-web-services.md), e uma definição de swagger são todos necessários para uma execução bem sucedida. O Stream Analytics tem um ponto final adicional que constrói o url para o ponto final swagger, procura a interface e devolve uma definição UDF padrão ao utilizador.

## <a name="configure-a-stream-analytics-and-studio-classic-udf-via-rest-api"></a>Configure um Stream Analytics e Studio (clássico) UDF via REST API
Ao utilizar ASP DE REST pode configurar o seu trabalho para ligar para funções studio (clássicas). Os passos são os seguintes:

1. Criar uma tarefa do Stream Analytics
2. Definir uma entrada
3. Definir uma saída
4. Criar uma função definida pelo utilizador (UDF)
5. Escreva uma transformação stream analytics que chame o UDF
6. Iniciar a tarefa

## <a name="creating-a-udf-with-basic-properties"></a>Criação de um UDF com propriedades básicas
Como exemplo, o seguinte código de amostra cria um UDF escalador chamado *newudf* que se liga a um Azure Machine Learning Studio (clássico). Note que o *ponto final* (serviço URI) pode ser encontrado na página de ajuda da API para o serviço escolhido e o *apiKey* pode ser encontrado na página principal dos Serviços.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Corpo de pedido de exemplo:

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

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Ligue para RetrieveDefaultDefinition endpoint para uDF padrão
Uma vez que o esqueleto UDF é criado, a definição completa do UDF é necessária. O ponto final retrieveDefaultDefinition ajuda-o a obter a definição padrão para uma função escalar que está ligada a um Azure Machine Learning Studio (clássico). A carga abaixo requer que obtenha a definição padrão de UDF para uma função escalar que está ligada a um ponto final do Studio (clássico). Não especifica o ponto final real, uma vez que já foi fornecido durante o pedido DE. Stream Analytics chama o ponto final fornecido no pedido se for fornecido explicitamente. Caso contrário, utiliza o originalmente referenciado. Aqui, o UDF pega num único parâmetro de corda (uma frase) e devolve uma única saída de tipo de cadeia que indica a etiqueta "sentimento" para essa frase.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Corpo de pedido de exemplo:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Uma amostra desta produção seria algo como abaixo.

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
Agora, o UDF deve ser remendado com a resposta anterior, como mostrado abaixo.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Corpo de Pedido (Saída de RecuperaçãoDefaultDefinition):

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

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementar a transformação do Stream Analytics para chamar o UDF
Agora questione o UDF (aqui nomeado scoreTweet) para cada evento de entrada e escreva uma resposta para esse evento para uma saída.

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
Para mais assistência, experimente o nosso [Microsoft Q&Uma página de perguntas para Azure Stream Analytics](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](/rest/api/streamanalytics/)
