---
title: Criar cliente para consumir o serviço web implementado
titleSuffix: Azure Machine Learning service
description: Saiba como consumir um serviço web que foi gerado quando um modelo foi implementado com o modelo do Azure Machine Learning. O web service expõe uma API REST. Crie clientes para esta API com a linguagem de programação da sua preferência.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: a862c920f1e070ab1bbb8af2546bc3d4350347b0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57889952"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Consumir um modelo do Azure Machine Learning implementado como um serviço web

Implementar um modelo do Azure Machine Learning como um serviço web cria uma API REST. Pode enviar dados para esta API e receber a predição devolvida pelo modelo. Neste documento, saiba como criar clientes para o serviço web com o C#, Go, Java e Python.

Criar um web service ao implementar uma imagem para o Azure Container Instances, o serviço Kubernetes do Azure ou o Project Brainwave (matrizes de porta programável por campo). Criar imagens a partir de modelos de registado e classificação de ficheiros. Obter o URI utilizado para aceder a um serviço web utilizando o [SDK do Azure Machine Learning](https://aka.ms/aml-sdk). Se a autenticação estiver ativada, também pode utilizar o SDK para obter as chaves de autenticação.

O fluxo de trabalho geral para a criação de um cliente que utiliza um serviço web machine learning é:

1. Utilize o SDK para obter as informações de ligação.
1. Determine o tipo de dados de pedido utilizados pelo modelo.
1. Crie uma aplicação que chama o serviço web.

## <a name="connection-information"></a>Informações da ligação

> [!NOTE]
> Utilize o SDK do Azure Machine Learning para obter as informações do serviço web. Trata-se de um SDK de Python. Pode usar qualquer linguagem para criar um cliente para o serviço.

O [azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) classe fornece as informações necessárias criar um cliente. O seguinte `Webservice` propriedades são úteis para criar uma aplicação de cliente:

* `auth_enabled` -Se a autenticação estiver ativada, `True`; caso contrário, `False`.
* `scoring_uri` -O endereço da REST API.

Para obter estas informações para os serviços web implementados, há um três formas:

* Quando implementa um modelo, um `Webservice` objeto é devolvido com informações sobre o serviço:

    ```python
    service = Webservice.deploy_from_model(name='myservice',
                                           deployment_config=myconfig,
                                           models=[model],
                                           image_config=image_config,
                                           workspace=ws)
    print(service.scoring_uri)
    ```

* Pode usar `Webservice.list` obter uma lista de implantados serviços da web para modelos em sua área de trabalho. Pode adicionar filtros para refinar a lista de informações devolvidas. Para obter mais informações sobre o que podem ser filtradas no, consulte a [Webservice.list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) documentação de referência.

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    ```

* Se souber o nome do serviço implementado, pode criar uma nova instância do `Webservice`e forneça o nome de área de trabalho e o serviço como parâmetros. O novo objeto contém informações sobre o serviço implementado.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    ```

### <a name="authentication-key"></a>Chave de autenticação

Quando ativar a autenticação para uma implementação, são criadas automaticamente as chaves de autenticação.

* Autenticação está ativada por predefinição, quando estiver a implementar no serviço Kubernetes do Azure.
* Autenticação está desativada por predefinição, quando estiver a implementar no Azure Container Instances.

Para controlar a autenticação, use o `auth_enabled` parâmetro quando estiver a criar ou atualizar uma implementação.

Se a autenticação estiver ativada, pode utilizar o `get_keys` método para recuperar uma chave de autenticação primária e secundária:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se precisar de voltar a gerar uma chave, utilize [ `service.regen_key` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

## <a name="request-data"></a>Dados de pedidos

A API REST espera que o corpo da solicitação para ser um documento JSON com a seguinte estrutura:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> A estrutura dos dados tem de corresponder ao que a classificação script e o modelo no expect serviço. O script de classificação pode modificar os dados antes de o transmitir para o modelo.

Por exemplo, o modelo no [Train dentro do bloco de notas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) exemplo espera uma matriz de 10 números. O script de classificação para este exemplo cria uma matriz de Numpy no pedido e o transmite para o modelo. O exemplo seguinte mostra os dados que deste serviço de espera:

```json
{
    "data": 
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
            ]
        ]
}
``` 

O serviço web pode aceitar vários conjuntos de dados numa solicitação. Ele retorna um documento JSON que contenha uma matriz de respostas.

### <a name="binary-data"></a>Dados binários

Se o seu modelo aceita dados binários, como uma imagem, tem de modificar o `score.py` ficheiro utilizado para a sua implementação para aceitar pedidos HTTP não processados. Eis um exemplo de um `score.py` que aceita dados binários:

```python 
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody 
        # and send to the model. Then return the response.
        
        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> O `azureml.contrib` espaço de nomes são alterados com frequência, pois estamos a trabalhar para melhorar o serviço. Como tal, qualquer coisa neste espaço de nomes deve ser considerada como uma pré-visualização e não totalmente suportada pela Microsoft.
>
> Se precisar de teste em seu ambiente de desenvolvimento local, pode instalar os componentes no `contrib` espaço de nomes utilizando o seguinte comando:
> 
> ```shell
> pip install azureml-contrib-services
> ```

## <a name="call-the-service-c"></a>Chamar o serviço (C#)

Este exemplo demonstra como utilizar o C# para chamar o serviço web criado a partir do [Train dentro do bloco de notas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) exemplo:

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
            // Set the scoring URI and authentication key
            string scoringUri = "<your web service URI>";
            string authKey = "<your key>";

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

Os resultados retornados são semelhantes para o documento JSON seguinte:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Chamar o serviço (Go)

Este exemplo demonstra como utilizar o Go para chamar o serviço web criado a partir da [Train dentro do bloco de notas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) exemplo:

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
// Set to the authentication key (if any) for your service
var authKey string = "<your key>"

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

Os resultados retornados são semelhantes para o documento JSON seguinte:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Chamar o serviço (Java)

Este exemplo demonstra como utilizar o Java para chamar o serviço web criado a partir da [Train dentro do bloco de notas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) exemplo:

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
        // If using authentication, replace with the auth key
        String key = "<your key>";
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

Os resultados retornados são semelhantes para o documento JSON seguinte:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Chamar o serviço (Python)

Este exemplo demonstra como utilizar Python para chamar o web service criado a partir da [Train dentro do bloco de notas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) exemplo:

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key
key = '<your key>'

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
headers = { 'Content-Type':'application/json' }
# If authentication is enabled, set the authorization header
headers['Authorization']=f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers = headers)
print(resp.text)

```

Os resultados retornados são semelhantes para o documento JSON seguinte:

```JSON
[217.67978776218715, 224.78937091757172]
```
