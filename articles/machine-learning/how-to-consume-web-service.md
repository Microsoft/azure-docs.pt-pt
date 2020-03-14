---
title: Criar cliente para modelo implementado como serviço web
titleSuffix: Azure Machine Learning
description: Aprenda a consumir um serviço web que foi gerado quando um modelo foi implementado com o modelo Azure Machine Learning. O serviço web expõe uma API REST. Crie clientes para esta API utilizando a linguagem de programação à sua escolha.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: 4c3e60e9c296dc8e3a1e31a52a262d8462237407
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283696"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Consumir um modelo do Azure Machine Learning implementado como um serviço web
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Implementar um modelo do Azure Machine Learning como um serviço web cria uma API REST. Pode enviar dados para esta API e receber a predição devolvida pelo modelo. Neste documento, aprenda a criar clientes para C#o serviço web utilizando , Go, Java e Python.

Cria um serviço web quando implementa uma imagem para as instâncias de contentores Azure, o Serviço Azure Kubernetes ou matrizes de porta programáveis em campo (FPGA). Cria imagens de modelos registados e de ficheiros de pontuação. Você recupera o URI usado para aceder a um serviço web usando o [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Se a autenticação estiver ativada, também pode utilizar o SDK para obter as chaves de autenticação ou fichas.

O fluxo de trabalho geral para a criação de um cliente que utiliza um serviço web de aprendizagem automática é:

1. Utilize o SDK para obter a informação de ligação.
1. Determine o tipo de dados de pedido utilizados pelo modelo.
1. Crie uma aplicação que chame o serviço web.

> [!TIP]
> Os exemplos deste documento são criados manualmente sem a utilização de especificações OpenAPI (Swagger). Se tiver ativado uma especificação OpenAPI para a sua implementação, pode utilizar ferramentas como [o código swagger](https://github.com/swagger-api/swagger-codegen) para criar bibliotecas de clientes para o seu serviço.

## <a name="connection-information"></a>Informações da ligação

> [!NOTE]
> Utilize o Azure Machine Learning SDK para obter a informação do serviço web. Trata-se de um SDK de Python. Pode usar qualquer idioma para criar um cliente para o serviço.

A classe [azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) fornece a informação de que precisa para criar um cliente. As seguintes propriedades `Webservice` são úteis para criar uma aplicação de cliente:

* `auth_enabled` - Se a autenticação chave estiver ativada, `True`; caso contrário, `False`.
* `token_auth_enabled` - Se a autenticação simbólica estiver ativada, `True`; caso contrário, `False`.
* `scoring_uri` - O endereço REST API.
* `swagger_uri` - O endereço da especificação OpenAPI. Este URI está disponível se tiver ativado a geração automática de esquemas. Para mais informações, consulte [Implementar modelos com Aprendizagem automática Azure](how-to-deploy-and-where.md#schema).

Para obter estas informações para os serviços web implementados, há um três formas:

* Quando implementa um modelo, um objeto `Webservice` é devolvido com informações sobre o serviço:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* Pode utilizar `Webservice.list` para recuperar uma lista de serviços web implantados para modelos no seu espaço de trabalho. Pode adicionar filtros para refinar a lista de informações devolvidas. Para obter mais informações sobre o que pode ser filtrado, consulte a documentação de referência da [Webservice.list.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py)

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Se souber o nome do serviço implantado, pode criar uma nova instância de `Webservice`, e fornecer o espaço de trabalho e o nome do serviço como parâmetros. O novo objeto contém informações sobre o serviço implementado.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

### <a name="secured-web-service"></a>Serviço web seguro

Se tiver assegurado o serviço web implantado utilizando um certificado SSL, pode utilizar o [HTTPS](https://en.wikipedia.org/wiki/HTTPS) para se ligar ao serviço utilizando a pontuação ou swagger URI. HTTPS ajuda a garantir comunicações entre um cliente e um serviço web encriptando comunicações entre os dois. A encriptação utiliza segurança da [camada de transporte (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS é por vezes ainda referido como *Secure Sockets Layer* (SSL), que foi o antecessor do TLS.

> [!IMPORTANT]
> Os serviços web implementados pelo Azure Machine Learning suportam apenas a versão TLS 1.2. Ao criar uma aplicação de cliente, certifique-se de que suporta esta versão.

Para mais informações, consulte [Use O SSL para garantir um serviço web através](how-to-secure-web-service.md)do Azure Machine Learning .

### <a name="authentication-for-services"></a>Autenticação para serviços

O Azure Machine Learning fornece duas formas de controlar o acesso aos seus serviços web.

|Método de Autenticação|ACI|AKS|
|---|---|---|
|Chave|Desativado por defeito| Ativado por predefinição|
|Certificado de| Não disponível| Desativado por defeito |

Ao enviar um pedido para um serviço que esteja protegido com uma chave ou ficha, utilize o cabeçalho __de Autorização__ para passar a chave ou ficha. A chave ou ficha deve ser formatada como `Bearer <key-or-token>`, onde `<key-or-token>` é a sua chave ou valor simbólico.

#### <a name="authentication-with-keys"></a>Autenticação com chaves

Quando ativa a autenticação para uma implementação, cria automaticamente as teclas de autenticação.

* A autenticação é ativada por padrão quando está a ser implementada para o Serviço Azure Kubernetes.
* A autenticação é desativada por padrão quando está a ser implementada para as instâncias do contentor Azure.

Para controlar a autenticação, utilize o parâmetro `auth_enabled` quando estiver a criar ou atualizar uma implementação.

Se a autenticação estiver ativada, pode utilizar o método `get_keys` para recuperar uma chave de autenticação primária e secundária:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se precisar regenerar uma chave, use [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

#### <a name="authentication-with-tokens"></a>Autenticação com fichas

Quando ativa a autenticação simbólica para um serviço web, um utilizador deve fornecer um símbolo De IMT de Aprendizagem automática azure ao serviço web para aceder ao mesmo. 

* A autenticação token é desativada por padrão quando está a ser implementada para o Serviço Azure Kubernetes.
* A autenticação token não é suportada quando estiver a ser implantada para instâncias de contentores Azure.

Para controlar a autenticação simbólica, utilize o parâmetro `token_auth_enabled` quando estiver a criar ou atualizar uma implementação.

Se a autenticação simbólica estiver ativada, pode utilizar o método `get_token` para recuperar um token do portador e que tokens tempo de validade:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Terá de pedir um novo sinal depois do `refresh_by` tempo do token. 

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

Por exemplo, o modelo no Comboio dentro do [portátil,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) exemplo, espera uma série de 10 números. O roteiro de pontuação para este exemplo cria uma matriz numpy a partir do pedido, e passa-o para o modelo. O exemplo seguinte mostra os dados que deste serviço de espera:

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

Para obter informações sobre como ativar o suporte para dados binários no seu serviço, consulte [dados binários](how-to-deploy-and-where.md#binary).

### <a name="cross-origin-resource-sharing-cors"></a>Partilha de recursos de origem cruzada (CORS)

Para obter informações sobre a ativação do suporte cors no seu serviço, consulte a [partilha de recursos de origem cruzada](how-to-deploy-and-where.md#cors).

## <a name="call-the-service-c"></a>Chamar o serviço (C#)

Este exemplo demonstra como C# usar para chamar o serviço web criado a partir do Comboio no exemplo do [portátil:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

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

Os resultados retornados são semelhantes para o documento JSON seguinte:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Chamar o serviço (Go)

Este exemplo demonstra como usar o Go para ligar para o serviço web criado a partir do Comboio no exemplo do [portátil:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

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

Os resultados retornados são semelhantes para o documento JSON seguinte:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Chamar o serviço (Java)

Este exemplo demonstra como usar java para chamar o serviço web criado a partir do Comboio dentro do exemplo [do portátil:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

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

Os resultados retornados são semelhantes para o documento JSON seguinte:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Chamar o serviço (Python)

Este exemplo demonstra como usar python para chamar o serviço web criado a partir do Comboio dentro do exemplo [do portátil:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

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

Os resultados retornados são semelhantes para o documento JSON seguinte:

```JSON
[217.67978776218715, 224.78937091757172]
```

## <a name="consume-the-service-from-power-bi"></a>Consumir o serviço da Power BI

O Power BI suporta o consumo de serviços web Azure Machine Learning para enriquecer os dados em Power BI com previsões. 

Para gerar um serviço web suportado para consumo em Power BI, o esquema deve suportar o formato que é exigido pelo Power BI. [Aprenda a criar um esquema apoiado](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#example-entry-script)por Power BI .

Uma vez que o serviço web é implantado, é consumível a partir de fluxos de dados power BI. [Aprenda a consumir um serviço web Azure Machine Learning da Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Passos seguintes

Para ver uma arquitetura de referência para pontuação em tempo real de Python e modelos de aprendizagem profunda, vá ao centro de [arquitetura Azure.](/azure/architecture/reference-architectures/ai/realtime-scoring-python)
