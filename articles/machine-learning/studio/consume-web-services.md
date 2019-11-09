---
title: Consumir serviço Web
titleSuffix: ML Studio (classic) - Azure
description: Depois que um serviço de Machine Learning é implantado do Azure Machine Learning Studio (clássico), o serviço Web RESTFul pode ser consumido como serviço de solicitação-resposta em tempo real ou como um serviço de execução em lote.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 1a567e8f910ccf539038a19eef0319f21833f336
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839696"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-classic-web-service"></a>Como consumir um serviço Web Azure Machine Learning Studio (clássico)

Depois de implantar um modelo de previsão de Azure Machine Learning Studio (clássico) como um serviço Web, você pode usar uma API REST para enviar dados de ti e obter previsões. Você pode enviar os dados em tempo real ou em modo de lote.

Você pode encontrar mais informações sobre como criar e implantar um serviço Web Machine Learning usando Machine Learning Studio (clássico) aqui:

* Para obter um tutorial sobre como criar um experimento no Machine Learning Studio (clássico), consulte [criar seu primeiro experimento](create-experiment.md).
* Para obter detalhes sobre como implantar um serviço Web, consulte [implantar um serviço web Machine Learning](deploy-a-machine-learning-web-service.md).
* Para obter mais informações sobre Machine Learning em geral, visite o [Machine Learning centro de documentação](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Descrição geral
Com o serviço Web do Azure Machine Learning, um aplicativo externo se comunica com um modelo de Pontuação de fluxo de trabalho Machine Learning em tempo real. Uma chamada de serviço Web do Machine Learning retorna resultados de previsão para um aplicativo externo. Para fazer uma chamada de serviço Web Machine Learning, você passa uma chave de API que é criada quando você implanta uma previsão. O serviço Web Machine Learning é baseado em REST, uma opção de arquitetura popular para projetos de programação da Web.

A versão clássica do Azure Machine Learning Studio tem dois tipos de serviços:

* RRS (serviço de solicitação-resposta) – um serviço de baixa latência e altamente escalonável que fornece uma interface para os modelos sem estado criados e implantados a partir do Machine Learning Studio (clássico).
* BES (serviço de execução em lote) – um serviço assíncrono que classifica um lote de registros de dados.

Para obter mais informações sobre Machine Learning serviços Web, consulte [implantar um serviço web Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Obter uma chave de autorização
Quando você implanta seu experimento, as chaves de API são geradas para o serviço Web. Você pode recuperar as chaves de vários locais.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>No portal de serviços Web do Microsoft Azure Machine Learning
Entre no portal de [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net) .

Para recuperar a chave de API para um novo serviço Web Machine Learning:

1. No portal de serviços Web do Azure Machine Learning, clique em **Serviços Web** no menu superior.
2. Clique no serviço Web para o qual você deseja recuperar a chave.
3. No menu superior, clique em **consumir**.
4. Copie e salve a **chave primária**.

Para recuperar a chave de API para um serviço Web Machine Learning clássico:

1. No portal de serviços Web do Azure Machine Learning, clique em **Serviços Web clássicos** no menu superior.
2. Clique no serviço Web com o qual você está trabalhando.
3. Clique no ponto de extremidade para o qual você deseja recuperar a chave.
4. No menu superior, clique em **consumir**.
5. Copie e salve a **chave primária**.

### <a name="classic-web-service"></a>Serviço Web clássico
 Você também pode recuperar uma chave para um serviço Web clássico do Machine Learning Studio (clássico).

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (clássico)
1. Em Machine Learning Studio (clássico), clique em **Serviços Web** à esquerda.
2. Clique em um serviço Web. A **chave de API** está na guia **painel** .

## <a id="connect"></a>Conectar-se a um serviço Web Machine Learning
Você pode se conectar a um serviço Web Machine Learning usando qualquer linguagem de programação que dê suporte à solicitação e resposta HTTP. Você pode exibir exemplos em C#, Python e R de uma página de ajuda do serviço Web Machine Learning.

**Ajuda da API Machine Learning** Machine Learning a ajuda da API é criada quando você implanta um serviço Web. Consulte [tutorial 3: implantar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md).
A ajuda da API Machine Learning contém detalhes sobre um serviço Web de previsão.

1. Clique no serviço Web com o qual você está trabalhando.
2. Clique no ponto de extremidade para o qual você deseja exibir a página de ajuda da API.
3. No menu superior, clique em **consumir**.
4. Clique na **página de ajuda da API** em pontos de extremidade de solicitação-resposta ou execução em lote.

**Para exibir Machine Learning ajuda da API para um novo serviço Web**

No [portal de serviços Web do Azure Machine Learning](https://services.azureml.net/):

1. Clique em **Serviços Web** no menu superior.
2. Clique no serviço Web para o qual você deseja recuperar a chave.

Clique em **usar serviço Web** para obter os URIs para os serviços de solicitação-resposta e execução em lote e C#código de exemplo em, R e Python.

Clique em **API do Swagger** para obter a documentação baseada em Swagger para as APIs chamadas a partir dos URIs fornecidos.

### <a name="c-sample"></a>C#Nova
Para se conectar a um serviço Web Machine Learning, use um **HttpClient** passando ScoreData. ScoreData contém um FeatureVector, um vetor n-dimensional de recursos numéricos que representa o ScoreData. Você se autentica no serviço de Machine Learning com uma chave de API.

Para se conectar a um serviço Web Machine Learning, o pacote NuGet **Microsoft. AspNet. WebApi. Client** deve ser instalado.

**Instalar o NuGet Microsoft. AspNet. WebApi. Client no Visual Studio**

1. Publique o conjunto de conteúdo de download de UCI: serviço Web do conjunto de conteúdo da classe adulto 2.
2. clique em **Ferramentas** > **Gestor de Pacotes NuGet** > **Consola de Gestor de Pacotes**.
3. Escolha **instalar-pacote Microsoft. AspNet. WebApi. Client**.

**Para executar o exemplo de código**

1. Publicar o "exemplo 1: baixar conjunto de conteúdo de UCI: conjunto de uma classe adulto 2", parte da coleção de exemplo Machine Learning.
2. Atribua apiKey com a chave de um serviço Web. Consulte **obter uma chave de autorização** acima.
3. Atribua serviceUri com o URI de solicitação.

**Esta será a aparência de uma solicitação completa.**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.NET application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the request ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Exemplo de Python
Para se conectar a um serviço Web Machine Learning, use a biblioteca **urllib2** para Python 2. x e **urllib. Request** Library para Python 3. x. Você passará ScoreData, que contém um FeatureVector, um vetor n-dimensional de recursos numéricos que representa o ScoreData. Você se autentica no serviço de Machine Learning com uma chave de API.

**Para executar o exemplo de código**

1. Implante o "exemplo 1: baixar conjunto de conteúdo de UCI: conjunto de uma classe adulto 2" teste, parte da coleção de exemplo Machine Learning.
2. Atribua apiKey com a chave de um serviço Web. Consulte a seção **obter uma chave de autorização** próximo ao início deste artigo.
3. Atribua serviceUri com o URI de solicitação.

**Esta será a aparência de uma solicitação completa.**
```python
import urllib2 # urllib.request for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(uri, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>Exemplo de R

Para se conectar a um serviço Web Machine Learning, use as bibliotecas **as** e **rjson** para fazer a solicitação e processar a resposta JSON retornada. Você passará ScoreData, que contém um FeatureVector, um vetor n-dimensional de recursos numéricos que representa o ScoreData. Você se autentica no serviço de Machine Learning com uma chave de API.

**Esta será a aparência de uma solicitação completa.**
```r
library("RCurl")
library("rjson")

# Accept SSL certificates issued by public Certificate Authorities
options(RCurlOptions = list(cainfo = system.file("CurlSSL", "cacert.pem", package = "RCurl")))

h = basicTextGatherer()
hdr = basicHeaderGatherer()

req = list(
    Inputs = list(
            "input1" = list(
                list(
                        'column1' = "value1",
                        'column2' = "value2",
                        'column3' = "value3"
                    )
            )
        ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "<your-api-key>" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

h$reset()
curlPerform(url = "<your-api-uri>",
httpheader=c('Content-Type' = "application/json", 'Authorization' = authz_hdr),
postfields=body,
writefunction = h$update,
headerfunction = hdr$update,
verbose = TRUE
)

headers = hdr$value()
httpStatus = headers["status"]
if (httpStatus >= 400)
{
print(paste("The request failed with status code:", httpStatus, sep=" "))

# Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
print(headers)
}

print("Result:")
result = h$value()
print(fromJSON(result))
```

### <a name="javascript-sample"></a>Exemplo de JavaScript

Para se conectar a um serviço Web Machine Learning, use o pacote **Request** NPM em seu projeto. Você também usará o objeto `JSON` para formatar sua entrada e analisar o resultado. Instale o usando `npm install request --save`ou adicione `"request": "*"` ao Package. JSON em `dependencies` e execute `npm install`.

**Esta será a aparência de uma solicitação completa.**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```
