---
title: Consumir o serviço web
titleSuffix: Azure Machine Learning Studio
description: Depois de um serviço de aprendizagem automática é implementado a partir do Azure Machine Learning Studio, o serviço RESTFul Web pode ser utilizado como o serviço de solicitação-resposta em tempo real ou como um serviço de execução do batch.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: a537227a7003391122e10f7f39233040cef49db3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57870066"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-web-service"></a>Como consumir um serviço web do Azure Machine Learning Studio

Depois de implementar um modelo preditivo do Azure Machine Learning Studio como um serviço Web, pode utilizar uma API REST para enviar os dados e obter previsões de indisponibilidade. Pode enviar os dados em tempo real ou no modo de lote.

Pode encontrar mais informações sobre como criar e implementar um serviço Web do Machine Learning com o Machine Learning Studio aqui:

* Para obter um tutorial sobre como criar uma experimentação no Machine Learning Studio, consulte [criar a sua primeira experiência](create-experiment.md).
* Para obter detalhes sobre como implementar um serviço da Web, consulte [implementar um serviço Web do Machine Learning](publish-a-machine-learning-web-service.md).
* Para obter mais informações sobre o Machine Learning em geral, visite o [Centro de documentação do Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Descrição geral
Com o serviço Web do Azure Machine Learning, uma aplicação externa comunica com um modelo de classificação de fluxo de trabalho de Machine Learning em tempo real. Uma chamada de serviço Web do Machine Learning devolve resultados de predição para uma aplicação externa. Para fazer uma chamada do serviço Web do Machine Learning, tem de transmitir uma chave de API que foi criada quando implementou uma predição. O serviço Web do Machine Learning é baseado em REST, uma opção de arquitetura popular para projetos de programação web.

O Azure Machine Learning Studio tem dois tipos de serviços:

* O serviço de solicitação-resposta (RRS) – uma latência baixa e o serviço altamente dimensionável, que fornece uma interface para os modelos sem monitorização de estado criados e implementados a partir do Machine Learning Studio.
* Execução serviço batch (BES) – um assíncrono que pontua um lote de registos de dados do serviço.

Para obter mais informações sobre os serviços Web do Machine Learning, consulte [implementar um serviço Web do Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-studio-authorization-key"></a>Obter uma chave de autorização do Azure Machine Learning Studio
Ao implementar a sua experimentação, as chaves de API são geradas para o serviço Web. Pode obter as chaves de várias localizações.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Partir do portal do Microsoft Azure Machine Learning Web Services
Inicie sessão para o [serviços de Web do Microsoft Azure Machine Learning](https://services.azureml.net) portal.

Para obter a chave de API para um serviço novo Web do Machine Learning:

1. No portal do Azure Machine Learning Web Services, clique em **serviços da Web** menu superior.
2. Clique no serviço de Web para o qual pretende obter a chave.
3. No menu superior, clique em **Consume**.
4. Copie e guarde o **chave primária**.

Para obter a chave de API para um serviço Web clássico de Aprendizado de máquina:

1. No portal do Azure Machine Learning Web Services, clique em **serviços Web clássicos** menu superior.
2. Clique no serviço de Web com a qual estiver a trabalhar.
3. Clique no ponto de final para o qual pretende obter a chave.
4. No menu superior, clique em **Consume**.
5. Copie e guarde o **chave primária**.

### <a name="classic-web-service"></a>Serviço Web clássico
 Também pode obter uma chave para um serviço Web clássico a partir do Machine Learning Studio.

#### <a name="machine-learning-studio"></a>Machine Learning Studio
1. No Machine Learning Studio, clique em **serviços da WEB** à esquerda.
2. Clique num serviço Web. O **chave de API** está ativada a **DASHBOARD** separador.

## <a id="connect"></a>Ligar a um serviço Web do Machine Learning
Pode ligar a um serviço de Machine Learning Web usando qualquer linguagem de programação que suporte a solicitação e resposta HTTP. Pode ver exemplos na C#, Python e R a partir de uma página de ajuda do serviço Web do Machine Learning.

**Ajuda da API de aprendizagem da máquina** ajuda da API do Machine Learning é criada quando implementar um serviço Web. Consulte [Tutorial 3: Implementar o modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md).
A ajuda da API do Machine Learning contém detalhes sobre uma predição do serviço Web.

1. Clique no serviço de Web com a qual estiver a trabalhar.
2. Clique no ponto de final para o qual pretende ver a página de ajuda da API.
3. No menu superior, clique em **Consume**.
4. Clique em **página de ajuda da API** em pontos finais de solicitação-resposta ou de execução de lote.

**Para a vista de API do Machine Learning ajuda para um serviço Web novo**

Na [do Azure Machine Learning Web Services Portal](https://services.azureml.net/):

1. Clique em **serviços da WEB** no menu superior.
2. Clique no serviço de Web para o qual pretende obter a chave.

Clique em **utilize o serviço Web** para obter os URIs para o solicitação-resposta e serviços de execução de lote e código de exemplo no C#, R e Python.

Clique em **API do Swagger** obter o Swagger com base em documentação para as APIs chamado a partir dos URIs fornecido.

### <a name="c-sample"></a>Exemplo de c#
Para ligar a um serviço Web do Machine Learning, utilize um **HttpClient** passando ScoreData. ScoreData contém um FeatureVector, um vetor n-dimensional de funcionalidades numéricas que representa o ScoreData. Autenticação no serviço de Machine Learning com uma chave de API.

Para ligar a um serviço Web do Machine Learning, o **Microsoft.AspNet.WebApi.Client** deve ser instalado o pacote NuGet.

**Instalar Microsoft.AspNet.WebApi.Client NuGet no Visual Studio**

1. Publicar o conjunto de dados de transferência de UCI: Conjunto de dados de classe de adulto 2 serviço Web.
2. clique em **Ferramentas** > **Gestor de Pacotes NuGet** > **Consola de Gestor de Pacotes**.
3. Escolher **Install-Package Microsoft.AspNet.WebApi.Client**.

**Para executar o exemplo de código**

1. Publicar "exemplo 1: Transferir o conjunto de dados de UCI: Experimentação do conjunto de dados de classe adulto 2", parte da coleção de exemplo de Machine Learning.
2. Atribua apiKey com a chave de um serviço Web. Ver **obter uma chave de autorização do Azure Machine Learning Studio** acima.
3. Atribua serviceUri com o URI do pedido.

**Eis o que uma solicitação completa será semelhante.**
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
Para ligar a um serviço Web do Machine Learning, utilize o **urllib2** biblioteca para o Python 2.X e **urllib.request** biblioteca para o Python 3.X. Passará ScoreData, que contém um FeatureVector, um vetor n-dimensional de funcionalidades numéricas que representa o ScoreData. Autenticação no serviço de Machine Learning com uma chave de API.

**Para executar o exemplo de código**

1. Implementar "exemplo 1: Transferir o conjunto de dados de UCI: Experimentação do conjunto de dados de classe adulto 2", parte da coleção de exemplo de Machine Learning.
2. Atribua apiKey com a chave de um serviço Web. Consulte a **obter uma chave de autorização do Azure Machine Learning Studio** seção perto do início deste artigo.
3. Atribua serviceUri com o URI do pedido.

**Eis o que uma solicitação completa será semelhante.**
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

Para ligar a um serviço Web do Machine Learning, utilize o **RCurl** e **rjson** bibliotecas para fazer a solicitação e processar a resposta JSON devolvida. Passará ScoreData, que contém um FeatureVector, um vetor n-dimensional de funcionalidades numéricas que representa o ScoreData. Autenticação no serviço de Machine Learning com uma chave de API.

**Eis o que uma solicitação completa será semelhante.**
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

Para ligar a um serviço Web do Machine Learning, utilize o **pedido** pacote npm no seu projeto. Também irá utilizar o `JSON` objeto para formatar os dados introduzidos e analisar o resultado. Instalar utilizando `npm install request --save`, ou adicione `"request": "*"` para Package. JSON na `dependencies` e executar `npm install`.

**Eis o que uma solicitação completa será semelhante.**
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
