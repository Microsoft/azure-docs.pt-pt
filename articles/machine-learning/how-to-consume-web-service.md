---
title: Criar cliente para modelo implementado como serviço web
titleSuffix: Azure Machine Learning
description: Saiba como chamar um ponto final de serviço web que foi gerado quando um modelo foi implementado a partir de Azure Machine Learning. O ponto final expõe uma API REST, que pode chamar para realizar inferência com o modelo. Crie clientes para esta API utilizando a linguagem de programação à sua escolha.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-csharp
ms.openlocfilehash: 6aacc2778e02b96f31c633671da014ced30778fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91756675"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Consumir um modelo do Azure Machine Learning implementado como serviço Web


A implementação de um modelo Azure Machine Learning como um serviço web cria um ponto final rest API. Pode enviar dados para este ponto final e receber a previsão devolvida pelo modelo. Neste documento, aprenda a criar clientes para o serviço web utilizando C#, Go, Java e Python.

Cria um serviço web quando implementa um modelo para o seu ambiente local, Instâncias de Contentores Azure, Serviço Azure Kubernetes ou matrizes de portão programáveis em campo (FPGA). Você recupera o URI usado para aceder ao serviço web usando o [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true). Se a autenticação estiver ativa, também pode utilizar o SDK para obter as teclas de autenticação ou fichas.

O fluxo de trabalho geral para criar um cliente que utiliza um serviço web de machine learning é:

1. Utilize o SDK para obter a informação de ligação.
1. Determine o tipo de dados de pedido utilizados pelo modelo.
1. Crie uma aplicação que ligue para o serviço web.

> [!TIP]
> Os exemplos deste documento são criados manualmente sem a utilização de especificações OpenAPI (Swagger). Se tiver ativado uma especificação OpenAPI para a sua implementação, pode utilizar ferramentas como [o código swagger](https://github.com/swagger-api/swagger-codegen) para criar bibliotecas de clientes para o seu serviço.

## <a name="connection-information"></a>Informações da ligação

> [!NOTE]
> Utilize o Azure Machine Learning SDK para obter as informações do serviço web. Este é um Python SDK. Pode utilizar qualquer idioma para criar um cliente para o serviço.

A classe [azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true) fornece a informação necessária para criar um cliente. As `Webservice` seguintes propriedades são úteis para a criação de uma aplicação ao cliente:

* `auth_enabled` - Se a autenticação da chave estiver `True` ativada, caso contrário, `False` .
* `token_auth_enabled` - Se a autenticação simbólica estiver `True` ativada, caso contrário, `False` .
* `scoring_uri` - O endereço da API REST.
* `swagger_uri` - O endereço da especificação OpenAPI. Este URI está disponível se for habilitado a geração de esquemas automáticos. Para obter mais informações, consulte [implementar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

Existem três formas de recuperar esta informação para serviços web implantados:

* Quando implementa um modelo, um `Webservice` objeto é devolvido com informações sobre o serviço:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* Pode utilizar `Webservice.list` para recuperar uma lista de serviços web implantados para modelos no seu espaço de trabalho. Pode adicionar filtros para reduzir a lista de informações devolvidas. Para obter mais informações sobre o que pode ser filtrado, consulte a documentação de referência [da lista Webservice.list.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py&preserve-view=true)

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Se souber o nome do serviço implantado, pode criar uma nova instância de , e fornecer o espaço de trabalho e o nome de `Webservice` serviço como parâmetros. O novo objeto contém informações sobre o serviço implantado.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

### <a name="secured-web-service"></a>Serviço web seguro

Se tiver assegurado o serviço web implantado utilizando um certificado TLS/SSL, pode utilizar [HTTPS](https://en.wikipedia.org/wiki/HTTPS) para se ligar ao serviço utilizando a pontuação ou swagger URI. HTTPS ajuda a garantir comunicações entre um cliente e um serviço web encriptando comunicações entre os dois. A encriptação utiliza [a Segurança da Camada de Transporte (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS é por vezes ainda referido como *Camada de Tomadas Seguras* (SSL), que foi o antecessor de TLS.

> [!IMPORTANT]
> Os serviços web implementados pela Azure Machine Learning suportam apenas a versão 1.2 do TLS. Ao criar uma aplicação ao cliente, certifique-se de que suporta esta versão.

Para obter mais informações, consulte [o Use TLS para garantir um serviço web através do Azure Machine Learning](how-to-secure-web-service.md).

### <a name="authentication-for-services"></a>Autenticação para serviços

O Azure Machine Learning fornece duas formas de controlar o acesso aos seus serviços web.

|Método de autenticação|ACI|AKS|
|---|---|---|
|Chave|Desativado por padrão| Ativado por predefinição|
|Token| Não Disponível| Desativado por padrão |

Ao enviar um pedido a um serviço que esteja seguro com uma chave ou ficha, utilize o cabeçalho __de Autorização__ para passar a chave ou o token. A chave ou token deve ser formatada como `Bearer <key-or-token>` , onde está a sua chave ou valor `<key-or-token>` simbólico.

A principal diferença entre chaves e fichas é que **as teclas são estáticas e podem ser regeneradas manualmente,** e **as fichas precisam de ser refrescadas após a expiração**. O auth baseado em chaves é suportado para Azure Container Instance e Azure Kubernetes Service implantados web-services, e auth baseada em token está disponível **apenas** para implementações do Serviço Azure Kubernetes. Consulte a [autenticação](how-to-setup-authentication.md#web-service-authentication) para obter mais informações e amostras de código específicas.


#### <a name="authentication-with-keys"></a>Autenticação com chaves

Quando ativa a autenticação para uma implantação, cria automaticamente chaves de autenticação.

* A autenticação é ativada por predefinição quando estiver a ser implantada no Serviço Azure Kubernetes.
* A autenticação é desativada por predefinição quando estiver a ser implantada em Instâncias de Contentores Azure.

Para controlar a autenticação, utilize o `auth_enabled` parâmetro quando estiver a criar ou atualizar uma implementação.

Se a autenticação estiver ativa, pode utilizar o `get_keys` método para recuperar uma chave de autenticação primária e secundária:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se precisar de regenerar uma chave, [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true) use.

#### <a name="authentication-with-tokens"></a>Autenticação com fichas

Quando ativa a autenticação simbólica de um serviço web, o utilizador deve fornecer um sinal JWT de Aprendizagem de Máquinas Azure ao serviço web para aceder ao mesmo. 

* A autenticação token é desativada por padrão quando estiver a ser implantada no Serviço Azure Kubernetes.
* A autenticação token não é suportada quando estiver a ser implantada em Instâncias de Contentores Azure.

Para controlar a autenticação simbólica, utilize o `token_auth_enabled` parâmetro quando estiver a criar ou atualizar uma implementação.

Se a autenticação simbólica estiver ativada, pode utilizar o `get_token` método para recuperar um token ao portador e que o tempo de validade dos tokens:

```python
token, refresh_by = service.get_token()
print(token)
```

Se tiver o [Azure CLI e a extensão de machine learning,](reference-azure-machine-learning-cli.md)pode utilizar o seguinte comando para obter um token:

```azurecli
az ml service get-access-token -n <service-name>
```

> [!IMPORTANT]
> Atualmente, a única maneira de recuperar o símbolo é usando o Azure Machine Learning SDK ou a extensão de aprendizagem automática Azure CLI.

Terá de pedir um novo símbolo depois da hora do `refresh_by` token. 

## <a name="request-data"></a>Solicitar dados

A API DO REST espera que o corpo do pedido seja um documento JSON com a seguinte estrutura:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> A estrutura dos dados precisa de corresponder ao que o script de pontuação e o modelo do serviço esperam. O script de pontuação pode modificar os dados antes de os passar para o modelo.

### <a name="binary-data"></a>Dados binários

Para obter informações sobre como ativar o suporte a dados binários no seu serviço, consulte os [dados binários](how-to-deploy-advanced-entry-script.md#binary-data).

> [!TIP]
> Permitir o suporte para dados binários ocorre no score.py ficheiro utilizado pelo modelo implantado. A partir do cliente, utilize a funcionalidade HTTP da sua linguagem de programação. Por exemplo, o seguinte corte envia o conteúdo de um ficheiro JPG para um serviço web:
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>Partilha de recursos de origem cruzada (CORS)

Para obter informações sobre como ativar o suporte do CORS no seu serviço, consulte [a partilha de recursos cross-origin](how-to-deploy-advanced-entry-script.md#cors).

## <a name="call-the-service-c"></a>Ligue para o serviço (C#)

Este exemplo demonstra como usar C# para chamar o serviço web criado a partir do Comboio dentro do exemplo [do portátil:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb)

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

Os resultados devolvidos são semelhantes ao seguinte documento JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Ligue para o serviço (Go)

Este exemplo demonstra como usar o Go para chamar o serviço web criado a partir do Comboio dentro do exemplo [do portátil:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/notebook_example.ipynb)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

Os resultados devolvidos são semelhantes ao seguinte documento JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Ligue para o serviço (Java)

Este exemplo demonstra como usar a Java para chamar o serviço web criado a partir do Comboio dentro do exemplo [do portátil:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/notebook_example.ipynb)

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

Os resultados devolvidos são semelhantes ao seguinte documento JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Ligue para o serviço (Python)

Este exemplo demonstra como usar python para chamar o serviço web criado a partir do Comboio dentro do exemplo [do portátil:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/notebook_example.ipynb)

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Os resultados devolvidos são semelhantes ao seguinte documento JSON:

```JSON
[217.67978776218715, 224.78937091757172]
```


## <a name="web-service-schema-openapi-specification"></a>Esquema de serviço web (especificação OpenAPI)

Se utilizar a geração de esquemas automáticos com a sua implantação, pode obter o endereço da especificação OpenAPI para o serviço utilizando a [propriedade swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=trueswagger-uri). (Por exemplo, `print(service.swagger_uri)` .) Utilize um pedido GET ou abra o URI num browser para recuperar a especificação.

O seguinte documento JSON é um exemplo de um esquema (especificação OpenAPI) gerado para uma implantação:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Para mais informações, consulte [a especificação OpenAPI.](https://swagger.io/specification/)

Para um utilitário que possa criar bibliotecas de clientes a partir da especificação, consulte [swagger-codegen](https://github.com/swagger-api/swagger-codegen).


> [!TIP]
> Pode recuperar o documento de esquema JSON depois de implementar o serviço. Utilize a [propriedade swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=trueswagger-uri) a partir do serviço web implantado (por exemplo, ) para obter `service.swagger_uri` o URI para o ficheiro Swagger do serviço web local.

## <a name="consume-the-service-from-power-bi"></a>Consumir o serviço no Power BI

O Power BI suporta o consumo de serviços web Azure Machine Learning para enriquecer os dados no Power BI com previsões. 

Para gerar um serviço web suportado para consumo em Power BI, o esquema deve suportar o formato que é exigido pelo Power BI. [Saiba como criar um esquema suportado por Power BI](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#example-entry-script).

Uma vez que o serviço web é implementado, é consumível a partir de fluxos de dados Power BI. [Saiba como consumir um serviço web Azure Machine Learning da Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Passos seguintes

Para ver uma arquitetura de referência para pontuação em tempo real de Python e modelos de deep learning, vá ao [centro de arquitetura Azure.](/azure/architecture/reference-architectures/ai/realtime-scoring-python)
